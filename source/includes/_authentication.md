# Authentication

AIbro uses email & password to allow access to the API. You can register an account at our [website](https://aipaca.ai).

Authentication is required when APIs from [AIbro python library](https://pypi.org/project/aibro/) are called for the first time.

# Start Your First Training Job on AIbro

<aside class="success">
Play around the executable Colab tutorial <a href = "https://colab.research.google.com/drive/19sXZ4kbic681zqEsrl_CZfB5cegUwuIB#forceEdit=true&sandboxMode=true&scrollTo=Et8ivBtkckme"> here</a>
</aside>

## Step 1: Install

```python
pip install aibro
```

```shell
pip install aibro
```

Install [aibro python library](https://pypi.org/project/aibro/) by pip.

## Step 2: Prepare Model & Data

```python
import numpy as np
import tensorflow as tf
from tensorflow.keras.applications import MobileNetV2
def _get_random_train_data( s, h, w, c ) :
    random_x_train = np.random.rand( s, h, w, c )
    random_y_train = tf.one_hot( [i for i in range(s)], 3 )
    return random_x_train, random_y_train

model = MobileNetV2( weights=None, classes=3 )
model.compile ( loss="categorical_crossentropy",
   optimizer="sgd", metrics=["accuracy"] )
train_X, train_Y = _get_random_train_data( 10, 224, 224, 3 )
```

As an example, we used `MobileNetV2` as the model and a random training data with shape (10, 224, 224, 3). You could plug in custom model and just remember to confirm the model's compilability with [Support Environment](#support-environment).

## Step 3: Pass Model & Data to AIbro API

```python
from aibro.train import online_fit

job_id, result_model, history= online_fit(
    model=model,
    train_X=train_X,
    train_Y=train_Y,
    machine_ids["p2.xlarge", "g4dn.4xlarge.od"],
    batch_size=1,
    epochs=15,
    description="my first training job",
)
```

Once the fitting start, you can track the job status on the [Jobs page of AIbro Console](https://aipaca.ai/jobs).

This example used a very basic `online_fit()`. If you would like to explore more features, please check out the [aibro.Fit](#aibro-fit) section for more details.
