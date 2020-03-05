4DVAR
--------------

    .. math::
        :label: costf4

        J \quad = \quad & \frac{1}{2} {( x - x_b )}^\intercal \mathrm{B}^{-1} ( x - x_b ) \quad + \\
                        & \frac{1}{2} \sum_{t=0}^{T} {( y_t - H_{y_t} M_t(x) )}^\intercal \mathrm{R_t}^{-1} ( y_t - H_{y_t} M_t(x) )