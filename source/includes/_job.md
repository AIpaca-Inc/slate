# aibro.Job

## plot_timeline()

```python
def plot_timeline(job_id: str, char_type: str = None)
```

This method is used to interpret the time spend of a training job from end-to-end.

### Parameters

**job_id**: _str_<br/>
The target Job's id.

**char_type**: _str_ = None<br/>
The type of chart. If its value is "pie", a pie chart would be plotted. Otherwise, it is a funnel chart.

### Timeline

This method can be called anytime even if the job has not been ended.

The timeline shows a little more insights than job status.

From the beginning to the end, the following time periods are shown on timeline plots:

- **Job Create**: time taken from code execution to job creation.
- **Request Launch**: time taken from a spot request start to be fulfilled; this period only applies to spot instance.
- **Instance Connect**: time taken from request fulfilled to successfully establish instance connection.
- **API Transfer & Server Setup**: time taken to set up AIbro API infrastructure in the instance.
- **M&D Serialization**: time taken to serialize model and training data.
- **Env Gear up**: time taken to gear up tensorflow.
- **M&D Transfer**: time taken to transfer model and training data from your local machine to the instance.
- **M&D Deserialization**: time taken to deserialize model and training data.
- **Model Training**: time taken to train the model.
- **Result Serialization**: time taken to serialize the trained model and other relevant objects.
- **Result Transfer**: time taken to transfer the trained model and other relevant objects.

[Timeline Sample](https://aibro-user-timeline.s3.amazonaws.com/example/new_instance_timeline.html)

## replay_job()

```python
def replay_job(
    job_id: str,
    description: str = "",
    directory_to_save_ckpt: str = ".",
    directory_to_save_log: str = ".",
    wait_request_s: int = 10000,
)
```

Once a recorded job is submitted, AIbro team would use _replay_job()_ method to reproduce the issue. You may also use it to check whether the reported issue is reproducible.

### Parameters

**job_id**: _str_<br/>
The recorded job's id.

**description**: _str = ""_<br/>
The issue description. More details help us diagnose the issue easier.

**directory_to_save_ckpt**: _str = None_<br/>
The directory used to save checkpoints. Checkpoints are stored by per epoch. If `None`, the model checkpoint won't be saved in your local machine.

**directory_to_save_log**: _str = None_<br/>
The directory used to save tensorboard log. If `None`, the log file won't be saved in your local machine.

**wait_request_s**: _int = 10000_<br/>
The time in seconds used to wait instance request to be fulfilled. A long enough `wait_request_s` is helpful when
requesting a spot instance with low availability.

## Job & Instance Status

Once a job starts, its states and substates are updated on the [Jobs page of AIbro Console](https://aipaca.ai/jobs).

| Job Status | Description                                           |
| ---------- | ----------------------------------------------------- |
| QUEUING    | Waiting for training                                  |
| TRAINING   | During training process or returning training results |
| CANCELED   | Canceled due to some errors                           |
| COMPLETED  | Completed the job                                     |

| Job Substatus        | Description                                     |
| -------------------- | ----------------------------------------------- |
| REQUESTING SERVER    | Requesting an instance to train models          |
| CONNECTING SERVER    | Connecting an initializing instance             |
| GEARING UP ENV       | Gearing up tensorflow and mounting GPUs         |
| SENDING MODEL & DATA | Sending model and training data to the instance |
| TRAINING             | Training model                                  |
| RETURNING            | Returning trained model                         |
| CANCELED             | Canceled due to some errors                     |
| COMPLETED            | Completed the job                               |

| Instance Status | Description                              |
| --------------- | ---------------------------------------- |
| LAUNCHING       | Setting up instance for training         |
| EXECUTING       | Having jobs in training process          |
| COOLING         | Within [Cooling Period](#cooling-period) |
| CLOSING         | Stopping/terminating instance            |
| CLOSED          | instance has been stopped/terminated     |

| Instance Substatus     | Description                                                                                                                |
| ---------------------- | -------------------------------------------------------------------------------------------------------------------------- |
| STOPPING/STOPPED       | Shut down instance but retain root volume [Reference](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/Stop_Start.html) |
| TERMINATING/TERMINATED | Completely delete the instance [Reference](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/terminating-instances.html) |

In the AIbro usage case, [Setup speed](#setup-speed) is the main advantage of stopped instance over terminated instance.

The following table is a status-substatus map of jobs and instances.

| Job Status | Job Substatus        | Instance Status        | Instance Substatus                                    |
| ---------- | -------------------- | ---------------------- | ----------------------------------------------------- |
| QUEUING    | REQUESTING SERVER    |                        |                                                       |
| QUEUING    | CONNECTING SERVER    | LAUNCHING              |                                                       |
| QUEUING    | GEARING UP ENV       | LAUNCHING              |                                                       |
| QUEUING    | SENDING MODEL & DATA | LAUNCHING              |                                                       |
| ---------- | ------------         | ---------------------- | --------------------------                            |
| TRAINING   | TRAINING             | EXECUTING              |                                                       |
| TRAINING   | RETURNING            | EXECUTING              |                                                       |
| ---------- | ------------         | ---------------------- | --------------------------                            |
| CANCELED   | CANCELED             | COOLING/CLOSING/CLOSED | COOLING/(STOPPING, TERMINATING)/(STOPPED, TERMINATED) |
| COMPLETED  | COMPLETED            | COOLING/CLOSING/CLOSED | COOLING/(STOPPING, TERMINATING)/(STOPPED, TERMINATED) |
