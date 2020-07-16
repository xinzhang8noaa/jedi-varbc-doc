DRMinimizer 
++++++++++++++++++

.. note::

  Discussion
  
Use case
==========

  DRMinimizer is the base class for all minimizers that use :math:`\textbf{B}` to precondition the variational minimisation problem and use the auxiliary :math:`\hat{x} = \textbf{B}^{-1} \vec{x}` and to update it in parallel to :math:`\vec{x}` based on Derber and Rosati, 1989, J. Phys. Oceanog. 1333-1347.

  :math:`J_b` is then computed as :math:`\vec{x}^T \hat{x}` eliminating the need for :math:`\textbf{B}^{-1}` or :math:`\textbf{B}^{1/2}` .

    .. note::

      Used by pcgsoi in `GSI <http://www.dtcenter.org/community-code/gridpoint-statistical-interpolation-gsi/download>`_

Algorithm
=============

    .. code-block:: c++
      :linenos:

      if (gradJb_) {
          gradJb_.reset(new CtrlInc_(J_.jb().resolution(), *gradJb_));
        } else {
          gradJb_.reset(new CtrlInc_(J_.jb()));
        }

        Log::info() << std::endl;
        Log::info() << classname() << ": max iter = " << ninner
                    <<  ", requested norm reduction = " << gnreduc << std::endl;

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

        Log::test() << classname() << ": reduction in residual norm = " << reduc << std::endl;
        Log::info() << classname() << " output increment:" << *dx << std::endl;

      // Update gradient Jb
        *gradJb_ += dxh;
        dxh_.push_back(dxh);

      // Update Jb component of J[0]: 0.5 (x_i - x_b)^T B^-1 (x_i - x_b)
        costJ0Jb_ += 0.5 * dot_product(*dx, dxh);
        for (unsigned int jouter = 1; jouter < dxh_.size(); ++jouter) {
          CtrlInc_ dxhtmp(dx->geometry(), dxh_[jouter-1]);
          costJ0Jb_ += dot_product(*dx, dxhtmp);
        }

Solver arguments
====================

      - *dx* : :math:`\vec{x}_0`
      - *dxh* : :math:`\hat{x} = \textbf{B}^{-1} \vec{x}_0`
      - *rhs* : :math:`- \textbf{B}^{-1} \vec{x} + \mathcal{H}^T \textbf{R}^{-1} \vec{y}`
      - *HtRinvH* : :math:`\mathcal{H}^T \textbf{R}^{-1} \mathcal{H}`
      - *B* : preconditioner, using :math:`\textbf{B}`
      - *ninner* : :math:`i_{max}`
      - *gnreduc* : :math:`\varepsilon`

Pseudo code (based on :code:`DRIPCGMinimizer.h` )
=====================================================

  Interface:

    .. code-block:: c++

      template<typename MODEL>
      double DRIPCGMinimizer<MODEL>::solve(CtrlInc_ & xx, CtrlInc_ & xh, CtrlInc_ & rr,
                                          const Bmat_ & B, const HtRinvH_ & HtRinvH,
                                          const double costJ0Jb, const double costJ0JoJc,
                                          const int maxiter, const double tolerance) {

  Arguments:

      - *xx* : :math:`\vec{x}_0`
      - *xh* : :math:`\hat{x} = \textbf{B}^{-1} \vec{x}_0`
      - *rr* : :math:`- \textbf{B}^{-1} \vec{x}_0 + \mathcal{H}^T \textbf{R}^{-1} \vec{y}_0`
      - *B* : preconditioner, using :math:`\textbf{B}`
      - *HtRinvH* : :math:`\mathcal{H}^T \textbf{R}^{-1} \mathcal{H}`
      - *maxiter* : :math:`i_{max}`
      - *tolerance* : :math:`\varepsilon`

  Code:

    .. math::

      &\textbf{Input:} \quad \vec{xx}_0, \ \vec{xh}_0, \ \vec{rr}_0, \ \textbf{B}, \ \mathcal{H}^T \textbf{R}^{-1} \mathcal{H}, \ maxiter, \ tolerance \\ 
      &\textbf{Output:} \quad \vec{xx}, \ \vec{xh}, \ \vec{rr} \\ 
      &\textbf{Subroutine:} \quad \textbf{lmp} \qquad (preconditioner) \\ 
      &\textbf{Algorithm:} \\ 
      &\qquad i \Leftarrow 0 \\ 
      &\qquad \vec{r}_0 \Leftarrow \vec{rr} \\ 
      &\qquad \\ 
      &\qquad \vec{sh} \Leftarrow \textbf{lmp} \cdot \vec{rr} \\
      &\qquad \vec{ss} \Leftarrow \textbf{B} \cdot \vec{sh} \\
      &\qquad \\
      &\qquad dotRr0 \Leftarrow \vec{rr}^T \cdot \vec{rr} \\ 
      &\qquad dotSr0 \Leftarrow \vec{rr}^T \cdot \vec{ss} \\ 
      &\qquad normReduction \Leftarrow 1.0 \\ 
      &\qquad rdots \Leftarrow dotRr0 \\ 
      &\qquad rdots_{old} \Leftarrow dotSr0 \\
      &\qquad \\
      &\qquad \vec{pp} \Leftarrow \vec{ss} \\
      &\qquad \vec{ph} \Leftarrow \vec{sh} \\
      &\qquad \textbf{while} \quad i < maxiter \quad \textbf{and} \quad normReduction > tolerance \quad \textbf{do} \\ 
      &\qquad \qquad \qquad \vec{ap} \Leftarrow \mathcal{H}^T \textbf{R}^{-1} \mathcal{H} \cdot \vec{pp} \\
      &\qquad \qquad \qquad \vec{ap} \Leftarrow \vec{ap} + \vec{ph} \\
      &\qquad \qquad \qquad \\
      &\qquad \qquad \qquad \vec{dr} \Leftarrow \vec{rr} \\
      &\qquad \qquad \qquad \\
      &\qquad \qquad \qquad \rho \Leftarrow \vec{pp}^T \cdot \vec{ap} \\
      &\qquad \qquad \qquad \alpha \Leftarrow \frac{rdots}{\rho} \\
      &\qquad \qquad \qquad \\
      &\qquad \qquad \qquad \vec{x} \Leftarrow \vec{x} + \alpha * \vec{pp} \\ 
      &\qquad \qquad \qquad \vec{xh} \Leftarrow \vec{xh} + \alpha * \vec{ph} \\ 
      &\qquad \qquad \qquad \vec{rr} \Leftarrow \vec{rr} - \alpha * \vec{ap} \\
      &\qquad \qquad \qquad \\
      &\qquad \qquad \qquad costJ \Leftarrow costJ0 - 0.5 * \vec{xx} \cdot \vec{r}_0 \\
      &\qquad \qquad \qquad costJb \Leftarrow costJ0Jb + 0.5 * \vec{xx} \cdot \vec{xh} \\
      &\qquad \qquad \qquad costJoJc \Leftarrow costJ -costJb \\
      &\qquad \qquad \qquad \\
      &\qquad \qquad \qquad \vec{rr} \Leftarrow \vec{rr} - \sum_{k=0}^{i-1} \frac{\vec{rr}^T \cdot \vec{ss}_k}{rdots_k} * \vec{rr}_k \\
      &\qquad \qquad \qquad \\
      &\qquad \qquad \qquad \vec{sh} \Leftarrow \textbf{lmp} \cdot \vec{rr} \\ 
      &\qquad \qquad \qquad \vec{ss} \Leftarrow \textbf{B} \cdot \vec{sh} \\
      &\qquad \qquad \qquad \\
      &\qquad \qquad \qquad rdots_{old} \Leftarrow rdots \\ 
      &\qquad \qquad \qquad rdots \Leftarrow \vec{rr}^T \cdot \vec{ss} \\ 
      &\qquad \qquad \qquad \\ 
      &\qquad \qquad \qquad normReduction \Leftarrow \sqrt{ \frac{\vec{rr}^T \cdot \vec{rr}}{dotRr0} } \\
      &\qquad \qquad \qquad \\
      &\qquad \qquad \qquad \vec{dr} \Leftarrow \vec{dr} - \vec{rr} \\
      &\qquad \qquad \qquad \beta \Leftarrow -\frac{ \vec{ss}^T \cdot \vec{dr} }{rdots_{old}} \\
      &\qquad \qquad \qquad \\
      &\qquad \qquad \qquad \vec{pp} \Leftarrow \vec{ss} + \beta * \vec{pp} \\
      &\qquad \qquad \qquad \vec{ph} \Leftarrow \vec{sh} + \beta * \vec{ph} \\
      &\qquad \qquad \qquad \\
      &\qquad \qquad \qquad i \Leftarrow i + 1

Implementation example
============================

    `DRIPCGMinimizer.h <https://github.com/JCSDA/oops/blob/develop/src/oops/assimilation/DRIPCGMinimizer.h>`_