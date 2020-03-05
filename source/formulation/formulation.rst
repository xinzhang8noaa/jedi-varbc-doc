Formulation
+++++++++++++++++++++

1. 3DVAR

    .. math::
        :label: costf3

        J = \frac{1}{2} {( x - x_b )}^\intercal \mathrm{B}^{-1} ( x - x_b ) + \frac{1}{2} {( y - H_y(x) )}^\intercal \mathrm{R}^{-1} ( y - H_y(x) )

#. 4DVAR

    .. math::
        :label: costf4

        J = \frac{1}{2} {( x - x_b )}^\intercal \mathrm{B}^{-1} ( x - x_b ) + \frac{1}{2} \sum_{t=0}^{T} {( y_t - H_y_t M_t(x) )}^\intercal \mathrm{R_t}^{-1} ( y_t - H_y_t M_t(x) )