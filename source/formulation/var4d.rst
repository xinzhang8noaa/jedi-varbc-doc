4DVAR
--------------


General form
^^^^^^^^^^^^^^

    .. math::
        :label: costf4

        J(\vec{x}) = \frac{1}{2} ( \vec{x} - \vec{x}_b )^T \textbf{B}^{-1} ( \vec{x} - \vec{x}_b ) + 
                     \frac{1}{2} \sum_{i=0}^{n} ( H_i (\vec{x}_i) - \vec{y}_i  )^T \textbf{R}_i^{-1} ( H_i (\vec{x}_i) - \vec{y}_i )

    where

        - Strong nonlineat constraint

            .. math::

                \forall i , \vec{x}_i & = M_{0 \to i}(\vec{x}) \\
                                      & = M_i M_{i-1} \dots M_1 \vec{x}_1

        - Linearizable

            .. math::

                H_i M_{0 \to i} (\vec{x}) - \vec{y}_i \approx H_i M_{0 \to i} (\vec{x}_b) + \mathcal{H}_i \mathcal{M}_{0 \to i} (\vec{x} -\vec{x}_b) - \vec{y}_i

Incremental form
^^^^^^^^^^^^^^^^^^^^^

    .. math::

        J(\vec{\delta x}) = & \frac{1}{2} \vec{\delta x}^T \textbf{B}^{-1} \vec{\delta x} + 
                              \frac{1}{2} \sum_{i=0}^{n} ( \mathcal{H}_i (\vec{\delta x}_i) - \vec{d}_i )^T \textbf{R}_i^{-1} ( \mathcal{H}_i (\vec{\delta x}_i) - \vec{d}_i ) \\
                          = & \frac{1}{2} \vec{\delta x}^T \textbf{B}^{-1} \vec{\delta x} \ + \\
                            & \frac{1}{2} \sum_{i=0}^{n} ( \mathcal{H}_i \mathcal{M}_{0 \to i} (\vec{\delta x}) - \vec{d}_i )^T \textbf{R}_i^{-1} ( \mathcal{H}_i \mathcal{M}_{0 \to i} (\vec{\delta x}) - \vec{d}_i ) \\
                          = & \frac{1}{2} \vec{\delta x}^T ( \textbf{B}^{-1} + \sum_{i=0}^n \mathcal{M}_{i \to 0}^T \mathcal{H}_i^T \textbf{R}_i^{-1} \mathcal{H}_i \mathcal{M}_{0 \to i} ) \vec{\delta x} \ - \\
                            & \vec{\delta x}^T \sum_{i=0}^n \mathcal{M}_{i \to 0}^T \mathcal{H}_i^T \textbf{R}^{-1} \vec{d}_i +
                              \frac{1}{2} \sum_{i=0}^n \vec{d}_i^T \textbf{R}^{-1} \vec{d}_i

    where

        .. math::

            \vec{d}_i =  \vec{y}_i - H_i M_{0 \to i} (\vec{x}_b)

Gradient
^^^^^^^^^^^^^^

    .. math::

        \nabla J(\vec{\delta x}) = ( \textbf{B}^{-1} + \sum_{i=0}^n \mathcal{M}_{i \to 0}^T \mathcal{H}_i^T \textbf{R}_i^{-1} \mathcal{H}_i \mathcal{M}_{0 \to i} ) \vec{\delta x} - \sum_{i=0}^n \mathcal{M}_{i \to 0}^T \mathcal{H}_i^T \textbf{R}^{-1} \vec{d}_i
