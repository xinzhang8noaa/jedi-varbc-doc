VarBC implementation discussion
----------------------------------

    .. note::
    
      Discussion

    In summary, according to :cite:`DDVarBC04` and :cite:`ZhuVarBC14`:

    1. For the background term, the Hessian is 
    
        .. math::
        
            \frac{\partial^2 J}{\partial \vec{x}^2} \Bigg\vert_{\vec{x} = \vec{x}_b} = \textbf{B}_{\vec{x}}^{-1} + \mathcal{H}_{\vec{x}}^T \textbf{R}^{-1} \mathcal{H}_{\vec{x}}
            
        it is popular to ignore the contribution from observation and use :math:`\mathcal{L}_\vec{x} = \textbf{B}^{-1/2}` (for example: ECMWF DA, WRFDA) or :math:`\mathcal{L}_\vec{x} = \textbf{B}^{-1}` (for example: GSI) as preconditioner, thus define a new control variable

        .. math::

            \vec{\chi}_\vec{x} = \mathcal{L}_\vec{x} (\vec{x}_b -\vec{x})
    
    #. For the bias parameter term, the Hessian is 

        .. math::

            \frac{\partial^2 J}{\partial \vec{\beta}^2} \Bigg\vert_{\vec{\beta} = \vec{\beta}_b} = \textbf{B}_{\vec{\beta}}^{-1} + \mathcal{H}_{\vec{\beta}}^T \textbf{R}^{-1} \mathcal{H}_{\vec{\beta}}

        due to the major contributionis come from observations, therfore, the full Hessian is recommended to use as preconditioner(trivial to compute before minimization for the linear bias model)

        .. math::

            \vec{\chi}_\vec{\beta} & = \mathcal{L}_\vec{\beta} ( \vec{\beta}_b - \vec{\beta} ) \\
                                   & = (\textbf{B}_{\vec{\beta}}^{-1} + \mathcal{H}_{\vec{\beta}}^T \textbf{R}^{-1} \mathcal{H}_{\vec{\beta}}) ( \vec{\beta}_b - \vec{\beta} )

Minimizers in OOPS
^^^^^^^^^^^^^^^^^^^^^^^^

        In **OOPS**, several minimizers have been looked into to understand the algorithms:

            1. PrimalMinimizer

                minimize the variational data assimilation cost function in primal (model) space.

                * Algorithm:

                    .. code-block:: c++
                        :linenos:
                        :emphasize-lines: 3, 16

                        // Define the matrices
                          Hessian_ hessian(J_, runOnlineAdjTest);
                          Bmat_ B(J_);

                        // Compute RHS
                          CtrlInc_ rhs(J_.jb());
                          J_.computeGradientFG(rhs);
                          J_.jb().addGradientFG(rhs);
                          rhs *= -1.0;
                          Log::info() << classname() << " rhs" << rhs << std::endl;

                        // Define minimisation starting point
                          CtrlInc_ * dx = new CtrlInc_(J_.jb());

                        // Solve the linear system
                          double reduc = this->solve(*dx, rhs, hessian, B, ninner, gnreduc);

                * Concrete implementation:

                    - PCG
                    - IPCG
                    - FGMRES
                    - GMRESR
                    - MINRES
                    - PLanczos

                * Generic implementation interfaces

                    .. code-block:: c++
                        :linenos:
                        :emphasize-lines: 4, 10, 13

                        ...
                        ...
                        double solve(CtrlInc_ &, const CtrlInc_ &,
                                     const Hessian_ &, const Bmat_ &,
                                     const int, const double) override;
                        ...
                        ...
                        template<typename MODEL>
                        double XXXXXXXXXXXXX<MODEL>::solve(CtrlInc_ & dx, const CtrlInc_ & rhs,
                                                           const Hessian_ & hessian, const Bmat_ & B,
                                                           const int ninner, const double gnreduc) {
                        // Solve the linear system
                          double reduc = XXXXXXXX(dx, rhs, hessian, B, ninner, gnreduc);
                        ...
                        ...
                        template <typename VECTOR, typename AMATRIX, typename PMATRIX>
                        double XXXXXXXX(VECTOR & xx, const VECTOR & bb,
                                        const AMATRIX & A, const PMATRIX & precond,
                                        const int maxiter, const double tolerance) {
                        ...
                        ...

            #. DRMinimizer

                use :math:`\textbf{B}` to precondition the variational minimisation problem and use the auxiliary variable :math:`\hat{\vec{x}}=\textbf{B}^{-1} \vec{x}` and to update it in parallel to :math:`\vec{x}`                

                * Algorithm

                    .. code-block:: c++
                        :linenos:
                        :emphasize-lines: 2, 24

                        // Define the matrices
                          const Bmat_    B(J_);
                          const HtRinvH_ HtRinvH(J_, runOnlineAdjTest);

                        // Compute RHS (sum B^{-1} dx_{i}) + H^T R^{-1} d
                        // dx_i = x_i - x_{i-1}; dx_1 = x_1 - x_b
                          CtrlInc_ rhs(J_.jb());
                          J_.computeGradientFG(rhs);
                          J_.jb().addGradientFG(rhs, *gradJb_);
                          rhs *= -1.0;
                          Log::info() << classname() << " rhs" << rhs << std::endl;

                        // Define minimisation starting point
                          // dx
                          CtrlInc_ * dx = new CtrlInc_(J_.jb());
                          // dxh = B^{-1} dx
                          CtrlInc_ dxh(J_.jb());

                        // Set J[0] = 0.5 (x_i - x_b)^T B^{-1} (x_i - x_b) + 0.5 d^T R^{-1} d
                          const double costJ0Jb = costJ0Jb_;
                          const double costJ0JoJc = J_.getCostJoJc();

                        // Solve the linear system
                          double reduc = this->solve(*dx, dxh, rhs, B, HtRinvH, costJ0Jb, costJ0JoJc, ninner, gnreduc);

                * Concrete implementations

                    - DRPCG
                    - DRIPCG
                    - DRGMRESR
                    - DRPFOM
                    - DRPLanczos

                * Generic implementation interfaces

                    .. code-block:: c++
                        :linenos:
                        :emphasize-lines: 1, 7

                        double solve(CtrlInc_ &, CtrlInc_ &, CtrlInc_ &, const Bmat_ &, const HtRinvH_ &,
                                     const double, const double, const int, const double) override;
                        ...
                        ...
                        template<typename MODEL>
                        double DRXXXXMinimizer<MODEL>::solve(CtrlInc_ & dx, CtrlInc_ & dxh, CtrlInc_ & rr,
                                                             const Bmat_ & B, const HtRinvH_ & HtRinvH,
                                                             const double costJ0Jb, const double costJ0JoJc,
                                                             const int maxiter, const double tolerance) {
                        ...
                        ...

        I have not looked into following minimizers

            1. LBMinimizer

                - LBGMRESR


            #. DualMinimizer

                - RPCG
                - RPLanczos

            #. SaddlePointMinimizer
    
Proposed changes
^^^^^^^^^^^^^^^^^^^^^

        From tha code snippets demonstared above. :code:`Bmat_` (:code:`BMatrix`) has been used almost everywhere and the :math:`\textbf{B}^{-1}` is used as the preconditioner, which means only the :math:`\textbf{B}_{\vec{x}}^{-1}` and :math:`\textbf{B}_{\vec{\beta}}^{-1}` can be used for preconditionning under current interfaces. 

        A new class :code:`oops::PMatrix` should be designed to accomodate the needs to contain both :math:`\textbf{B}^{-1}` and :math:`\mathcal{H}^T \textbf{R}^{-1} \mathcal{H}`, also the needs to use :math:`\textbf{B}^{-\frac{1}{2}}` as preconditioner.

        Here is the proposed design:


        1. A new class :code:`HauxtRinvHauxMatrix` denotes :eq:`HessianBeta`

            .. uml::

                namespace oops.assimilation #DDDDDD {
                    class HauxtRinvHauxMatrix <MODEL> {
                        {static} classname() : const string
                        + HauxtRinvHauxMatrix(const CostFct_ &, const bool) : void
                        + ~HauxtRinvHauxMatrix() : void
                        + multiply(const CtrlInc_ &, CtrlInc_ &) const : void
                        - print(ostream &) const : void
                        - CostFct_ const : j_
                        - bool : test_
                        - int mutable : iter
                    }
                }

            .. note::

                Since bias model is linear, please refere to :ref:`Adjoint of the bias model` , to save computational cost, the TLM and ADM are :math:`\textbf{I}`

                But the cost of :eq:`HessianX` is redundant and can not be eliminated under this context. Future developments are needed to fine-grained control on Hessian computation

            .. code-block:: c++
                :linenos:
                :emphasize-lines: 8-11, 19-21, 36-37
                
                ...
                ...
                template<typename MODEL>
                void HauxtRinvHauxMatrix<MODEL>::multiply(const CtrlInc_ & dx0, CtrlInc_ & dz) const {
                // Increment counter
                  iter_++;

                // Only keep the obsVar
                  CtrlInc dx(dx0); 
                  dx.state()[0].zero();
                  dx.modVar().zero();

                // Setup TL terms of cost function
                  PostProcessorTLAD<MODEL> costtl;
                  for (unsigned jj = 0; jj < j_.nterms(); ++jj) {
                    costtl.enrollProcessor(j_.jterm(jj).setupTL(dx));
                  }

                // Run Identity TLM since bias model is linear
                  CtrlInc_ mdx(dx);
                  j_.runTLM(mdx, costtl, idModel = true);

                // Get TLM outputs, multiply by covariance inverses, and setup ADJ forcing terms
                  j_.zeroAD(dz);
                  PostProcessorTLAD<MODEL> costad;

                  DualVector<MODEL> ww;
                  DualVector<MODEL> zz;

                  for (unsigned jj = 0; jj < j_.nterms(); ++jj) {
                    ww.append(costtl.releaseOutputFromTL(jj));
                    zz.append(j_.jterm(jj).multiplyCoInv(*ww.getv(jj)));
                    costad.enrollProcessor(j_.jterm(jj).setupAD(zz.getv(jj), dz));
                  }

                // Run identity ADJ since bias model is linear
                  j_.runADJ(dz, costad, bool idModel = true);

                  if (test_) {
                     // <G dx, dy>, where dy = Rinv H dx
                     double adj_tst_fwd = dot_product(ww, zz);
                     // <dx, Gt dy> , where dy = Rinv H dx
                     double adj_tst_bwd = dot_product(dx, dz);

                     Log::info() << "Online adjoint test, iteration: " << iter_ << std::endl
                                 << util::PrintAdjTest(adj_tst_fwd, adj_tst_bwd, "G")
                                 << std::endl;
                  }
                }
                ...
                ...

        #. A new class denotes the new preconditioner:

            .. math::

                \mathcal{L} =  (\textbf{B}_{\vec{x}}^{-1} + \textbf{B}_{\vec{\beta}}^{-1}) + \textbf{H}_{\vec{\beta}}^T \textbf{R}^{-1} \mathcal{H}_{\vec{\beta}}


            .. uml::

                namespace oops.assimilation #DDDDDD {
                    class PMatrix <MODEL> {
                        {static} classname() : const string
                        + PMatrix(const CostFct_ &) : void
                        + ~PMatrix() : void
                        + multiply(const CtrlInc_ &, CtrlInc_ &) const : void
                        - print(ostream &) const : void
                        - CostFct_ const : j_ 
                    }
                }

            .. code-block:: c++
                :linenos:

                ...
                ...
                void multiply(const CtrlInc_ & dx, CtrlInc_ & pdx) const {
                  CtrlInc_ dbx(dx);
                  j_.jb().multiplyB(dx, bdx);
                  HauxtRinvHauxMatrix.multiply(dx, pdx);
                  pdx += dbx;
                }
                ...
                ...

        #. Replace :code:`Bmat_` in minimisation algorithms with :code:`Pmat_ => oops::PMatrix`