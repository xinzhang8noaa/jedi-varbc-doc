Problem
---------------------

  .. math::

    (\textbf{B}^{-1} + \mathcal{H}^T \textbf{R}^{-1} \mathcal{H}) \vec{x} = \mathcal{H}^T \textbf{R}^{-1} \vec{y}

  which equvilents to solve the linear system:

  .. math::

    \textbf{A} \vec{x} = \vec{b}

  in which 

  .. math::

    & \textbf{A} = \textbf{B}^{-1} + \mathcal{H}^T \textbf{R}^{-1} \mathcal{H} \\
    & \vec{b} = \mathcal{H}^T \textbf{R}^{-1} \vec{y}

