PrimalMinimizer 
++++++++++++++++++++++++++++++

.. note::

  Discussion

Use case
================

    PrimalMinimizer is the base class for all minimizers that minimize thevariational data assimilation cost function in primal (model) space.

Algorithm
===============

    .. code-block:: c++
      :linenos:

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

Solver arguments
====================

      - *dx* : :math:`\vec{x}_0`
      - *rhs* : :math:`- J' = - \textbf{B}^{-1} \vec{x} + \mathcal{H}^T \textbf{R}^{-1} \vec{y}`
      - *hessian* : :math:`\textbf{A} = \textbf{B}^{-1} + \mathcal{H}^T \textbf{R}^{-1} \mathcal{H}`
      - *B* : :math:`\textbf{M} \approx \textbf{A}^{-1}`
      - *ninner* : :math:`i_{max}`
      - *gnreduc* : :math:`\varepsilon`

Pseudo code (based on :code:`PCG.h` )
========================================

  Interface:

    .. code-block:: c++

      template <typename VECTOR, typename AMATRIX, typename PMATRIX>
      double PCG(VECTOR & x, const VECTOR & b,
                  const AMATRIX & A, const PMATRIX & precond,
                  const int maxiter, const double tolerance ) {

  Arguments :

      - *x* : :math:`\vec{x}_0`
      - *b* : :math:`- J' = \textbf{B}^{-1} \vec{x} + \mathcal{H}^T \textbf{R}^{-1} \vec{y}`
      - *A* : :math:`\textbf{A} = \textbf{B}^{-1} + \mathcal{H}^T \textbf{R}^{-1} \mathcal{H}`
      - *precond* : :math:`\textbf{M} \approx \textbf{A}^{-1}`
      - *maxiter* : :math:`i_{max}`
      - *tolerance* : :math:`\varepsilon`

  Code:

    .. math::

        &\textbf{Input:} \quad \textbf{A}, \ \textbf{precond}, \ \vec{b}, \ \vec{x}_0, \ maxiter, \ tolerance \\ 
        &\textbf{Output:} \quad \vec{x} \\ 
        &\textbf{Algorithm:} \\ 
        &\qquad i \Leftarrow 0 \\ 
        &\qquad \vec{r} \Leftarrow \vec{b} \\
        &\qquad \vec{s} \Leftarrow \textbf{A} \cdot \vec{x} \\
        &\qquad \vec{r} \Leftarrow \vec{r} - \vec{s} \\
        &\qquad \\
        &\qquad \vec{s} \Leftarrow \textbf{precond} \cdot \vec{r} \\
        &\qquad \\ 
        &\qquad dotRr0 \Leftarrow \vec{r}^T \cdot \vec{s} \\
        &\qquad normReduction \Leftarrow 1.0 \\ 
        &\qquad rdots \Leftarrow dotRr0 \\ 
        &\qquad \\
        &\qquad \vec{p} \Leftarrow \vec{s} \\
        &\qquad \textbf{while} \quad i < maxiter \quad \textbf{and} \quad normReduction > tolerance \quad \textbf{do} \\ 
        &\qquad \qquad \qquad \vec{ap} \Leftarrow \textbf{A} \cdot \vec{p} \\ 
        &\qquad \qquad \qquad \\ 
        &\qquad \qquad \qquad \alpha \Leftarrow \frac{rdots}{\vec{p}^T \cdot \vec{ap} } \\ 
        &\qquad \qquad \qquad  \\ 
        &\qquad \qquad \qquad \vec{x} \Leftarrow \vec{x} + \alpha * \vec{p} \\ 
        &\qquad \qquad \qquad \vec{r} \Leftarrow \vec{r} - \alpha * \vec{ap} \\
        &\qquad \qquad \qquad \\
        &\qquad \qquad \qquad \vec{r} \Leftarrow \vec{r} - \sum_{k=0}^{i-1} \frac{\vec{r}^T \cdot \vec{s}_k}{rdots_k} * \vec{r}_k \\
        &\qquad \qquad \qquad \\
        &\qquad \qquad \qquad \vec{s} \Leftarrow \textbf{precond} \cdot \vec{r} \\ 
        &\qquad \qquad \qquad \\
        &\qquad \qquad \qquad rdots_{old} \Leftarrow rdots \\ 
        &\qquad \qquad \qquad rdots \Leftarrow \vec{r}^T \cdot \vec{s} \\ 
        &\qquad \qquad \qquad \\
        &\qquad \qquad \qquad normReduction \Leftarrow \sqrt{\frac{rdots}{dotRr0}} \\ 
        &\qquad \qquad \qquad \\
        &\qquad \qquad \qquad \beta \Leftarrow \frac{\vec{s}^T \cdot \vec{r}}{rdots_{old}} \\
        &\qquad \qquad \qquad \vec{p} \Leftarrow \vec{s} + \beta * \vec{p} \\
        &\qquad \qquad \qquad \\
        &\qquad \qquad \qquad i \Leftarrow i + 1

Implementation example
==========================

    `PCG.h <https://github.com/JCSDA/oops/blob/develop/src/oops/assimilation/PCG.h>`_

