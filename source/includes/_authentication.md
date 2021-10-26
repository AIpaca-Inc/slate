# Authentication

AIbro uses email & password to allow access to the API. Accounts are registered at the AIbro [website](https://aipaca.ai).

Authentication is required when APIs from [AIbro python library](https://pypi.org/project/aibro/) are called for the first time.

# Start First Training Job on AIbro

<aside class="success">
Play around the executable Colab tutorial <a href = "https://colab.research.google.com/drive/19sXZ4kbic681zqEsrl_CZfB5cegUwuIB#forceEdit=true&sandboxMode=true&scrollTo=Et8ivBtkckme"> here</a>
</aside>

## Step 1: Install

```python
pip install aibro
```

Install [aibro python library](https://pypi.org/project/aibro/) by pip.

If `OSError: protocol not found` shows up, it is caused by missing `/etc/protocols` file. This command should be able to resolve the error: `sudo apt-get -o Dpkg::Options::="--force-confmiss" install --reinstall netbase`

## Step 2: Prepare Model & Data

```python
import tensorflow as tf
from tensorflow import keras
def get_mnist_data():
    num_val_samples = 100

    # Return the MNIST dataset in the form of a [`tf.data.Dataset`]
    # reference: (https://www.tensorflow.org/api_docs/python/tf/data/Dataset).
    (x_train, y_train), (x_test, y_test) = keras.datasets.mnist.load_data()

    # Preprocess the data (these are Numpy arrays)
    x_train = x_train.reshape(-1, 784).astype("float32") / 255
    x_test = x_test.reshape(-1, 784).astype("float32") / 255
    y_train = y_train.astype("float32")
    y_test = y_test.astype("float32")

    # Reserve num_val_samples samples for validation
    x_val = x_train[-num_val_samples:]
    y_val = y_train[-num_val_samples:]
    x_train = x_train[:-num_val_samples]
    y_train = y_train[:-num_val_samples]
    return x_train, y_train, x_val, y_val


def get_compiled_FFNN_model():
    # Make a simple 2-layer densely-connected neural network.
    inputs = keras.Input(shape=(784,))
    x = keras.layers.Dense(256, activation="relu")(inputs)
    x = keras.layers.Dense(256, activation="relu")(x)
    outputs = keras.layers.Dense(10)(x)
    model = keras.Model(inputs, outputs)
    model.compile(
        optimizer=keras.optimizers.Adam(),
        loss=keras.losses.SparseCategoricalCrossentropy(from_logits=True),
        metrics=[keras.metrics.SparseCategoricalAccuracy()],
    )
    return model


train_X, train_Y, validation_X, validation_Y = get_mnist_data()
model = get_compiled_FFNN_model()
```

As an example, we used a custom feed-forward neural network (FFNN) as the model and [MNIST](https://www.tensorflow.org/api_docs/python/tf/data/Dataset) dataset. You could plug in your own model and just remember to confirm the model's compilability with [Support Environment](#support-environment).

## Step 3: The Magic One-Line Code

```python
from aibro import Fit

job_id, result_model, history= Fit.online_fit(
    model=model,
    train_X=train_X,
    train_Y=train_Y,
    validation_data=(validation_X, validation_Y),
    machine_ids=["p2.xlarge.od"],
    batch_size=8,
    epochs=15,
    description="my first training job",
)
```

Now, it is the time to train the model on cloud with the **one line of code** - `Fit.online_fit()`.

Once the job start, job status update will be displayed on the [Jobs page of AIbro Console](https://aipaca.ai/jobs) in real-time.

This example used a basic `online_fit()`. To explore more features, please check out the [aibro.Fit](#aibro-fit) section.
