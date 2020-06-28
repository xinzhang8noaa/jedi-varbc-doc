3DVAR
----------------

Standard 3DVAR
^^^^^^^^^^^^^^^^^

  .. math::
      :label: costf3

      J(x) = \frac{1}{2} ( \vec{x}- \vec{x}_b ) )^T \textbf{B}^{-1} ( \vec{x} - \vec{x}_b ) +
             \frac{1}{2} ( H(\vec{x}) - \vec{y} )^T \textbf{R}^{-1} ( H(\vec{x}) -\vec{y} )

  define :math:`\vec{\delta{x}} = \vec{x} -\vec{x}_b`, called *Increment*
  
  thus, based on *Taylor expansion*

    .. math::

      H(\vec{x}) = H(\vec{x}_b) + \mathcal{H} (\vec{\delta{x}})

  where :math:`\mathcal{H} = \frac{\partial H}{\partial \vec{x}} \Bigg \vert_{\vec{x}=\vec{x}_b}`, called *Linear observation operator*

  :eq:`costf3` can be writted as incremental form

    .. math::
      :label: Incremental3dvar

      J(\vec{\delta{x}}) & = \frac{1}{2} \vec{\delta{x}}^T \textbf{B}^{-1} \vec{\delta{x}} + \frac{1}{2} ( H(\vec{x}_b) + \mathcal{H} (\vec{\delta{x}}) - \vec{y} )^T \textbf{R}^{-1} ( H(\vec{x}_b) + \mathcal{H} (\vec{\delta{x}}) - \vec{y} ) \\
       & = \frac{1}{2} \vec{\delta{x}}^T \textbf{B}^{-1} \vec{\delta{x}} + \frac{1}{2} ( \mathcal{H} (\vec{\delta{x}}) - \vec{d} )^T \textbf{R}^{-1} ( \mathcal{H} (\vec{\delta{x}}) - \vec{d} ) \\
       & = \frac{1}{2} \vec{\delta{x}}^T ( \textbf{B}^{-1} + \mathcal{H}^T \textbf{R}^{-1} \mathcal{H}) \vec{\delta{x}} -
           \vec{\delta{x}}^T \mathcal{H}^T \textbf{R}^{-1} \vec{d} + \frac{1}{2} \vec{d}^T \textbf{R}^{-1} \vec{d}

  where :math:`\vec{d} = \vec{y} - H(\vec{x}_b)`, called *Innovation*

  :eq:`Incremental3dvar` is a `quadratic eqautaion <https://en.wikipedia.org/wiki/Quadratic_equation>`_, then, to find the :math:`\vec{\delta{x}}` which minimizeds :math:`J`

    .. math::

      \nabla J = ( \textbf{B}^{-1} + \mathcal{H}^T \textbf{R}^{-1} \mathcal{H}) \vec{\delta{x}} - \mathcal{H}^T \textbf{R}^{-1} \vec{d} = 0


Variation Observation Bias correction
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  Define the augmentated control vector

    .. math::

      \vec{z}^T = \lbrack \vec{x}^T \vec{\beta}^T \rbrack

  Therefore, the cost function to be minimizaed

    .. math::

      J(\vec{z}) = \frac{1}{2} (\vec{z}_b - \vec{z})^T \textbf{Z}^{-1} (\vec{z}_b - \vec{z}) +  \frac{1}{2} (\vec{y} - \tilde{H}(\vec{z}))^T \textbf{R}^{-1} (\vec{y} - \tilde{H}(\vec{z}))

  where

    .. math::

      \tilde{H}(\vec{z}) & =  \begin{bmatrix}
                                H & P  \\
                              \end{bmatrix} \cdot
                              \begin{bmatrix}
                                 \vec{x} \\
                                 \vec{\beta} \\
                              \end{bmatrix} \\
                          & = H(\vec{x}) + P(\vec{x}) \vec{\beta}

  In general the parameter estimation errors will be correlated with the state estimation errors, because they depend on the same data. We know of no practical way to account for this statistical dependence, and therefore take

    .. math::

      \textbf{Z} = \begin{bmatrix}
                      \textbf{B}_x & 0 \\
                      0 & \textbf{B}_{\beta}
                    \end{bmatrix}

  where :math:`\textbf{B}_x` denotes the usual (state) background error covariance, and :math:`\textbf{B}_\beta` the parameter background error covariance.

  We take :math:`\textbf{B}_\beta` diagonal:
  
  Also define define :math:`\vec{\delta{z}} = \vec{z} -\vec{z}_b`, then the *Increment form* is :

    .. math::
      :label: Incremental3dvarVarBC

      J(\vec{\delta{z}}) = \frac{1}{2} \vec{\delta{z}}^T ( \textbf{Z}^{-1} + \tilde{\mathcal{H}}^T \textbf{R}^{-1} \tilde{\mathcal{H}}) \vec{\delta{z}} -
           \vec{\delta{z}}^T \tilde{\mathcal{H}}^T \textbf{R}^{-1} \vec{\tilde{d}} + \frac{1}{2} \vec{d}^T \textbf{R}^{-1} \vec{\tilde{d}}

  The gradient is

    .. math::

      \nabla J = ( \textbf{Z}^{-1} + \tilde{\mathcal{H}}^T \textbf{R}^{-1} \tilde{\mathcal{H}} ) \vec{\delta{z}} - \tilde{\mathcal{H}}^T \textbf{R}^{-1} \vec{\tilde{d}}

  where:

    - Linear observation operator

      .. math::

        \tilde{\mathcal{H}}(\vec{\delta{z}}) & = \begin{bmatrix}
                                                   \mathcal{H} \vert_{\vec{x}_b} & P \vert_{\vec{x}_b} \\
                                                 \end{bmatrix} \cdot
                                                 \begin{bmatrix}
                                                   \vec{\delta{x}} \\
                                                   \vec{\delta{\beta}} \\
                                                 \end{bmatrix} \\
             & = \mathcal{H} \vert_{\vec{x}_b} \vec{\delta{x}} + P \vert_{\vec{x}_b} \vec{\delta{\beta}}

    - Innovation

      .. math::

        \vec{\tilde{d}} & = \vec{y} - \tilde{H}(\vec{z}_b) \\
                        & = \vec{y} - H(\vec{x}_b) - P(\vec{x}_b) \vec{\beta}_b