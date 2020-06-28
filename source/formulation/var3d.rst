3DVAR
----------------

Standard 3DVAR
^^^^^^^^^^^^^^^^^

  .. math::
      :label: costf3

      J(x) = \frac{1}{2} ( \vec{x}- \vec{x}_b ) )^T \textbf{B}^{-1} ( \vec{x} - \vec{x}_b ) +
             \frac{1}{2} ( H(\vec{x}) - \vec{y} )^T \textbf{R}^{-1} ( H(\vec{x}) -\vec{y} )

  define :math:`\delta \vec{x} = \vec{x} -\vec{x}_b`, called *Increment*
  
  thus, based on *Taylor expansion*

    .. math::

      H(\vec{x}) = H(\vec{x}_b) + \mathcal{H} (\delta \vec{x})

  where :math:`\mathcal{H} = \frac{\partial H}{\partial \vec{x}} \Bigg \vert_{\vec{x}=\vec{x}_b}`, called *Linear observation operator*

  :eq:`costf3` can be writted as incremental form

    .. math::
      :label: Incremental3dvar

      J(\delta \vec{x}) & = \frac{1}{2} \delta \vec{x} ^T \textbf{B}^{-1} \delta \vec{x} + \frac{1}{2} ( H(\vec{x}_b) + \mathcal{H} (\delta \vec{x}) - \vec{y} )^T \textbf{R}^{-1} ( H(\vec{x}_b) + \mathcal{H} (\delta \vec{x}) - \vec{y} ) \\
       & = \frac{1}{2} \delta \vec{x} ^T \textbf{B}^{-1} \delta \vec{x} + \frac{1}{2} ( \mathcal{H} (\delta{\vec{x}}) - \vec{d} )^T \textbf{R}^{-1} ( \mathcal{H} (\delta{\vec{x}}) - \vec{d} ) \\
       & = \frac{1}{2} \delta \vec{x} ^T ( \textbf{B}^{-1} + \mathcal{H}^T \textbf{R}^{-1} \mathcal{H}) \delta \vec{x} -
           \delta \vec{x}^T \mathcal{H}^T \textbf{R}^{-1} \vec{d} + \frac{1}{2} \vec{d}^T \textbf{R}^{-1} \vec{d}

  where :math:`\vec{d} = \vec{y} - H(\vec{x}_b)`, called *Innovation*

  :eq:`Incremental3dvar` is a `quadratic eqautaion <https://en.wikipedia.org/wiki/Quadratic_equation>`_, then, to find the :math:`\delta \vec{x}` which minimizeds :math:`J`

    .. math::

      \nabla J = ( \textbf{B}^{-1} + \mathcal{H}^T \textbf{R}^{-1} \mathcal{H}) \delta \vec{x} - \mathcal{H}^T \textbf{R}^{-1} \vec{d} = 0


Variation Observation Bias correction
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  .. math::
    :label: costf3VarBC

    J(\vec{x},\vec{\beta}) = & \frac{1}{2} ( \vec{x} - \vec{x}_b )^T \textbf{B}_{\vec{x}}^{-1} ( \vec{x} - \vec{x}_b ) \ + \\
                   & \frac{1}{2} ( \vec{\beta} - \vec{\beta}_b )^T \textbf{B}_{\vec{\beta}}^{-1} ( \vec{\beta} - \vec{\beta}_b ) \ + \\
                   & \frac{1}{2} {[H(\vec{x}) + P(\vec{x}) \vec{\beta} -\vec{y} ]}^T \textbf{R}^{-1} [H(\vec{x}) + P(\vec{x}) \vec{\beta} -\vec{y} ]

  where:
  
    - :math:`\beta` : Predictor coefficients vector
    - :math:`P(\vec{x})` : Predictors evaulation on all observations locations, usually, it is evaluated only at outer loops, therefore, it is treated as a constant within the minimization inner loop.
  
  Also define define :math:`\delta \vec{\beta} = \vec{\beta} -\vec{\beta}_b`, then *Increment form* is :

  .. math::
      :label: Incremental3dvarVarBC

      J(\delta \vec{x}, \delta \vec{\beta}) = & \frac{1}{2} \delta \vec{x} ^T \textbf{B}_{\vec{x}}^{-1} \delta \vec{x} + \frac{1}{2} \delta \vec{\beta}^T \textbf{B}_{\vec{\beta}}^{-1} \delta \vec{\beta} \ + \\
       & \frac{1}{2} ( H(\vec{x}_b) + \mathcal{H} (\delta \vec{x}) + P(\vec{x}_b) \vec{\beta}_b + P(\vec{x}_b) \delta \vec{\beta} - \vec{y} )^T \textbf{R}^{-1} \\
       & \quad ( H(\vec{x}_b) + \mathcal{H} (\delta \vec{x}) + P(\vec{x}_b) \vec{\beta}_b + P(\vec{x}_b) \delta \vec{\beta} - \vec{y} ) \\
       = & \frac{1}{2} \delta \vec{x} ^T \textbf{B}_{\vec{x}}^{-1} \delta \vec{x} + \frac{1}{2} \delta \vec{\beta}^T \textbf{B}_{\vec{\beta}}^{-1} \delta \vec{\beta} + \frac{1}{2} ( \mathcal{H} (\delta{\vec{x}}) - \vec{d} )^T \textbf{R}^{-1} ( \mathcal{H} (\delta{\vec{x}}) - \vec{d} ) \\
       = & \frac{1}{2} \delta \vec{x} ^T ( \textbf{B}^{-1} + \mathcal{H}^T \textbf{R}^{-1} \mathcal{H}) \delta \vec{x} -
           \delta \vec{x}^T \mathcal{H}^T \textbf{R}^{-1} \vec{d} + \frac{1}{2} \vec{d}^T \textbf{R}^{-1} \vec{d}
