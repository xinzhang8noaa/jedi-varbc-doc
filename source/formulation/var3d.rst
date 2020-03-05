3DVAR
----------------

    .. math::
        :label: costf3

        J(x) \quad = \quad & \frac{1}{2} {( x - x_b )}^\intercal \mathrm{B}^{-1} ( x - x_b ) \quad + \\
                           & \frac{1}{2} {( y - H_y(x) )}^\intercal \mathrm{R}^{-1} ( y - H_y(x) )

    Add ObsBias terms:

    .. math::
        :label: costf3+bias

        J(x,\beta) \quad = \quad & \frac{1}{2} {( x - x_b )}^\intercal \mathrm{B}^{-1} ( x - x_b ) \quad + \\
                                 & \frac{1}{2} {( \beta - {\beta}_b )}^{\intercal} \mathrm{B}_{\beta}^{-1} ( \beta - {\beta}_b ) \quad + \\
                                 & \frac{1}{2} {[ y - H_y(x) - \mathrm{P} \beta ]}^\intercal \mathrm{R}^{-1} [ y - H_y(x) - \mathrm{P} \beta ]

    define:

        - :math:`m` : Observation size, scalar
        - :math:`k` : Predictor size, scalar
        - :math:`\beta` : Predictor coefficients, :math:`k \times 1` shape vector
        - :math:`\mathrm{P}` : Calculated Predictors for all observations, :math:`m \times k` shape matrix

          for each observation :math:`i = 1, m` , the obsbias correction term is:

          .. math::

            \mathrm{P} \beta \quad = \quad \sum_{j=1}^{k} P_{(i,j)}[H_{y_i}(x_b), y_i] {\beta}_j, \qquad i = 1, m
