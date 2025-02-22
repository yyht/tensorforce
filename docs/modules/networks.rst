Networks
========

Default network: ``LayeredNetwork`` with default argument ``layers``, so a ``list`` is a short-form specification of a sequential layer-stack network architecture:

.. code-block:: python

    Agent.create(
        ...
        policy=dict(network=[
            dict(type='dense', size=64, activation='tanh'),
            dict(type='dense', size=64, activation='tanh')
        ]),
        ...
    )

Note that the final action/value layer of the policy/baseline network is implicitly added, so the network output can be of arbitrary size and use any activation function, and is only required to be a rank-one embedding vector, or optionally have the same shape as the action in the case of a higher-rank action shape.

Multi-input and other non-sequential networks are specified as nested list of lists of layers, where each of the inner lists forms a sequential component of the overall network architecture. The following example illustrates how to specify such a more complex network, by using the `special layers <layers.html#tensorforce.core.layers.Register>`_ ``Register`` and ``Retrieve`` to combine the sequential network components:

.. code-block:: python

    Agent.create(
        states=dict(
            observation=dict(type='float', shape=(16, 16, 3)),
            attributes=dict(type='int', shape=(4, 2), num_values=5)
        ),
        ...
        policy=[
            [
                dict(type='retrieve', tensors=['observation']),
                dict(type='conv2d', size=32),
                dict(type='flatten'),
                dict(type='register', tensor='obs-embedding')
            ],
            [
                dict(type='retrieve', tensors=['attributes']),
                dict(type='embedding', size=32),
                dict(type='flatten'),
                dict(type='register', tensor='attr-embedding')
            ],
            [
                dict(
                    type='retrieve', aggregation='concat',
                    tensors=['obs-embedding', 'attr-embedding']
                ),
                dict(type='dense', size=64)
            ]
        ],
        ...
    )

The ``AutoNetwork`` automatically configures a suitable network architecture based on input types and shapes, and offers high-level customization.

Details about the network layer architecture (policy, baseline, state-preprocessing) can be accessed via ``agent.get_architecture()``.


.. autoclass:: tensorforce.core.networks.AutoNetwork

.. autoclass:: tensorforce.core.networks.LayeredNetwork

.. autoclass:: tensorforce.core.networks.KerasNetwork
