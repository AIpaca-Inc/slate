# Authentication

AIbro uses email & password to allow access to the API. You can register an account at our [website](https://aipaca.ai).

Authentication is required when APIs from [AIbro python library](https://pypi.org/project/aibro/) are called for the first time.

# Start Your First Training Job on AIbro

<aside class="success">
Checkout the executable Colab tutorial <a href = "https://colab.research.google.com/drive/19sXZ4kbic681zqEsrl_CZfB5cegUwuIB#forceEdit=true&sandboxMode=true&scrollTo=Et8ivBtkckme"> here</a>
</aside>

## Step 1: Install

`pip instal aibro`

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