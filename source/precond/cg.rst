Conjugate Gradient
---------------------

Pseudocode
^^^^^^^^^^^^^^^^^

  .. math::

    &\textbf{Input:} \quad \textbf{A}, \ \vec{b}, \ \vec{x}, \ i_{max}, \ \varepsilon \\ 
    &\textbf{Output:} \quad \vec{x} \\ 
    &\textbf{Algorithm:} \\ 
    &\qquad i \Leftarrow 0 \\ 
    &\qquad \vec{r} \Leftarrow \vec{b} - \textbf{A} \vec{x} \\ 
    &\qquad \vec{d} \Leftarrow \vec{r} \\ 
    &\qquad \delta_{new} \Leftarrow \vec{r}^T \vec{r} \\ 
    &\qquad \delta_0 \Leftarrow \delta_{new} \\ 
    &\qquad \textbf{while} \quad i < i_{max} \quad \textbf{and} \quad \delta_{new} > \varepsilon^2 \delta_{0} \quad \textbf{do} \\ 
    &\qquad \qquad \qquad \vec{q} \Leftarrow \textbf{A} \vec{d} \\ 
    &\qquad \qquad \qquad \alpha \Leftarrow \frac{\delta_{new}}{\vec{d}^T \vec{q} } \\ 
    &\qquad \qquad \qquad \vec{x} \Leftarrow \vec{x} + \alpha \vec{d} \\ 
    &\qquad \qquad \qquad \vec{r} \Leftarrow \vec{r} - \alpha \vec{q} \\ 
    &\qquad \qquad \qquad \delta_{old} \Leftarrow \delta_{new} \\ 
    &\qquad \qquad \qquad \delta_{new} \Leftarrow \vec{r}^T \vec{r} \\ 
    &\qquad \qquad \qquad \beta \Leftarrow \frac{\delta_{new}}{\delta_{old}} \\ 
    &\qquad \qquad \qquad \vec{d} \Leftarrow \vec{r} + \beta \vec{d} \\ 
    &\qquad \qquad \qquad i \Leftarrow i + 1