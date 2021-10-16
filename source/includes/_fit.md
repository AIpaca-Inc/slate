# aibro.Fit

## online_fit()

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
    wait_new_job_create_s: int = -1,
    wait_new_job_create_interval: int = 10,
    record: bool = False,
) -> Tuple[Optional[str], Optional[TF_Model], Optional[History]]
```

Online fit is basically synchronize fit. Once it is called, model fitting progress will be shown in real time. We used
the word "online" because it requires the fitting process to be running and connecting to the internet until the end.

Every call of online fit will create a new training job unless the maximum limit of active jobs or instances per user is reached.

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
Please checkout the [Cooling Period](#cooling-period) section for more details.

**fit_kwargs**: _Dict[str, Any] = {}_<br/>
the arguments used to pass into model.fit().

**directory_to_save_ckpt**: _str = None_<br/>
The directory used to save checkpoints. Checkpoints are stored by per epoch. If `None`, the model checkpoint won't be saved in your local machine.

**directory_to_save_log**: _str = None_<br/>
The directory used to save tensorboard log. If `None`, the log file won't be saved in your local machine.
current working directory.

**wait_request_s**: _int = 10000_<br/>
The amount of seconds to wait instance request to be fulfilled. You probably want to set a long enough time when
requesting a spot instance with low availability.

**wait_new_job_create_s**: _int = -1_<br/>
The amount of seconds to wait a new job to be created. This parameter is helpful when you have reached the maximum active
jobs or instances. It allows the new job to wait until one of the active jobs or instances are finished. If the value
is non-positive, the new job will wait 99999999 seconds (basically forever).

**wait_new_job_create_interval**: _int = 10_<br/>
The amount of seconds to check whether there is a spot for the new job to be created.

**record**: _bool = False_<br/>
Turn on record mode to report issues. With record mode turned on, Our support team can easily reproduce issues. Before
using the feature, we would recommend you to read more details in the [Report Issue](#report-issue) section and its
[Privacy Items](#data-privacy).

## Cooling Period

The amount of second that an idle instance will be held before termination. **This is an important concept**.

To use cooling period, you should set **cool_down_period_s** non-zero and request the **same machine_id** in your next
job. AIbro will automatically pick up the same instance for the next job. To avoid confusion, we use **cooling server** to
call those servers under cooling period.

Cooling period has the following benefit:

- Avoid environment gear up time (around 5-6 mins). When a new instance is requested, it takes over 5 mins to mount GPU
  and gear up tensorflow modules. Once the next job successfully picks up the same instance, there is no need to redo
  the environment gear up.
- Saving money. Except paying for environment gear up time, instance will also spend around 5 minutes to terminate. Both
  of them will spend you extra money. Cooling period can help you avoid frequent instance launch and termination.

However, we don't really encourage users to hold an idle spot instance as spot instances are not always available. The
cooling period may impact the capacity of other users. Therefore, if you hold a spot instance over a `BASELINE` period,
its pricing would increment an `UNIT_PERCENTAGE` per minute. After `1/UNIT_PERCENTAGE` minutes, its price will reach a
maximum, which is same as its on-demand price. This rule doesn't apply to on-demand instances.

In this version, we set the variables as the following:

| Variable        | Value      |
| --------------- | ---------- |
| BASELINE        | 10 minutes |
| UNIT_PERCENTAGE | 1%         |
