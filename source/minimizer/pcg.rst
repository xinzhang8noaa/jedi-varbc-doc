Preconditioned Conjugate Gradient
-------------------------------------

For more details, please refer to :cite:`NoceWrig06`


  Instead of solving：

    .. math::

      \textbf{A} \vec{x} = \vec{b}

  now, we solve:

    .. math::

      \textbf{M}^{-1} \textbf{A} \vec{x} = \textbf{M}^{-1} \vec{b}

  :math:`\textbf{M}` is the preconditioner, hopefully, :math:`\kappa(\textbf{M}^{-1} \textbf{A}) \ll \kappa(\textbf{A})`

Pseudo code
^^^^^^^^^^^^^^^^^

  .. math::

    &\textbf{Input:} \quad \textbf{A}, \ \textbf{M}, \ \vec{b}, \ \vec{x}, \ i_{max}, \ \varepsilon \\ 
    &\textbf{Output:} \quad \vec{x} \\ 
    &\textbf{Algorithm:} \\ 
    &\qquad i \Leftarrow 0 \\ 
    &\qquad \vec{r} \Leftarrow \vec{b} - \textbf{A} \cdot \vec{x} \\ 
    &\qquad \vec{d} \Leftarrow \textbf{M}^{-1} \cdot \vec{r} \\ 
    &\qquad \delta_{new} \Leftarrow \vec{r}^T \cdot \vec{d} \\ 
    &\qquad \delta_0 \Leftarrow \delta_{new} \\ 
    &\qquad \textbf{while} \quad i < i_{max} \quad \textbf{and} \quad \delta_{new} > \varepsilon^2 \delta_{0} \quad \textbf{do} \\ 
    &\qquad \qquad \qquad \vec{q} \Leftarrow \textbf{A} \cdot \vec{d} \\ 
    &\qquad \qquad \qquad \alpha \Leftarrow \frac{\delta_{new}}{\vec{d}^T \cdot \vec{q} } \\ 
    &\qquad \qquad \qquad \vec{x} \Leftarrow \vec{x} + \alpha * \vec{d} \\ 
    &\qquad \qquad \qquad \vec{r} \Leftarrow \vec{r} - \alpha * \vec{q} \\ 
    &\qquad \qquad \qquad \vec{s} \Leftarrow \textbf{M}^{-1} \cdot \vec{r} \\ 
    &\qquad \qquad \qquad \delta_{old} \Leftarrow \delta_{new} \\ 
    &\qquad \qquad \qquad \delta_{new} \Leftarrow \vec{r}^T \cdot \vec{s} \\ 
    &\qquad \qquad \qquad \beta \Leftarrow \frac{\delta_{new}}{\delta_{old}} \\ 
    &\qquad \qquad \qquad \vec{d} \Leftarrow \vec{s} + \beta * \vec{d} \\ 
    &\qquad \qquad \qquad i \Leftarrow i + 1


Different flavors
^^^^^^^^^^^^^^^^^^^^^^^

.. toctree::
   :titlesonly:

   pcg_oops
   dripcg_oops
   dripcg_m_oops