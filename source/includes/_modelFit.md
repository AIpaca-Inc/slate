# Model Fit

## Online Fit

```python
def online_fit(
    model: tensorflow.keras.models.Model,
    train_X: numpy.ndarray,
    train_Y: numpy.ndarray,
    machine_ids: List[str] = None,
    batch_size: int = 1,
    epochs: int = 1,
    validation_data: Tuple[np.array, np.array] = None,
    description: str = "",
    cool_down_period_s: int = 0,
    fit_kwargs: Dict[str, Any] = {},
    directory_to_save_ckpt: str = None,
    directory_to_save_log: str = None,
    wait_request_s: int = 30,
) -> Tuple[Optional[str], Optional[TF_Model], Optional[History]]
```

Online fit means synchronize fitting. Once it is called, model fitting progress will be shown in real time. We used
the word "online" because it requires the fitting process to keep running and connecting to internet until the training
stop.

Every call of online fit will create a new training job unless the maximum limit of active job per user is reached.

When designing the parameters, we were trying to stay with the tensorflow style as close as possible.

### Parameters

**model**: _tensorflow.keras.models.Model_<br/>
The machine learning model to be trained.

**train_X**: _numpy.ndarray_<br/>
The input training data feeding to model.

**train_Y**: _numpy.ndarray_<br/>
The output training data feeding to model.

**machine_ids**: _List[str] = None_<br/>
The cloud machines used to train the model. The machines are requested in the order of list; for example,
in a case of `["p2.xlarge", "g4dn.4xlarge"]`, the API will try to request `p2.xlarge` first. If `p2.xlarge` is not available
or has no capacity within `wait_request_s` seconds, `g4dn.4xlarge` will be requested next.

If no machine id is `None`, a selection message will ask you to select one from AIbro marketplace.

**batch_size**: _int = 1_<br/>
The training batch size.

**epochs**: _int = 1_<br/>
The training epochs.

**validation_data**: _Tuple[np.array, np.array] = None_<br/>
The input and output validation data feeding to model. The order is (validation_X, validation_Y).

**description**: _str = ""_<br/>
The description used to remind you which training job was which. We highly recommend setting every job a unique
description for easy lookup.

**cool_down_period_s**: _int = 0_<br/>
The amount of second that an idle instance will be held before termination. **This is an important concept**.
Please checkout the following section for more details.

**fit_kwargs**: _Dict[str, Any] = {}_<br/>
the arguments used to pass into model.fit()

**directory_to_save_ckpt**: _str = None_<br/>
The directory used to save checkpoints. Checkpoints are stored by per epoch. If `None`, the default directory is
set as the current working directory.

**directory_to_save_log**: _str = None_<br/>
The directory used to save tensorboard log. If `None`, the default directory is set as the
current working directory.

**wait_request_s**: _int = 30_<br/>
The amount of second to wait instance request to be fulfilled. You probably want to set a long enough time when
requesting a spot instance with low availability.

## Cooling Period

The amount of second that an idle instance will be held before termination. **This is an important concept**.

To use cooling period, you should set **cool_down_period_s** non-zero and request the **same machine_id** in your next
job. AIbro will automatically pick up the same instance for the next job.

Cooling period has the following benefit:

- Avoid environment gear up time (around 5-6 mins). When a new instance is requested, it takes over 5 mins to mount GPU
  and gear up tensorflow modules. Once the next job successfully picks up the same instance, there is no need to redo 
  the environment gear up.
- Saving money. Except paying for environment gear up time, instance will also spend around 5 minutes to terminate. Both
  of them will spend you extra money. Cooling period can help you avoid frequent instance launch and termination.

However, we don't really encourage users to hold an idle instance as this is a waste of money. For spot instance, the
cooling period may impact the capacity of other users. Therefore, if you hold a spot instance over a `BASELINE` period,
its pricing would increment an `UNIT_PERCENTAGE` per minute. After `1/UNIT_PERCENTAGE` minutes, its price will reach a
maximum, which is same as its on-demand price.

In this version, we set the variables as the following:

| Variable        | Value      |
| --------------- | ---------- |
| BASELINE        | 10 minutes |
| UNIT_PERCENTAGE | 1%         |