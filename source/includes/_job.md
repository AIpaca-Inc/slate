# aibro.Job

## Job & Instance Status

Once a job starts, it and its instance will experience a series of statuses and substatuses. You can monitor their changes on [AIbro Console](https://aipaca.ai/jobs).

| Job Status | Job Substatus        | Instance Status        | Instance Substatus                                    |
| ---------- | -------------------- | ---------------------- | ----------------------------------------------------- |
| QUEUING    | REQUESTING SERVER    |                        |                                                       |
| QUEUING    | CONNECTING SERVER    | LAUNCHING              |                                                       |
| QUEUING    | SENDING MODEL & DATA | LAUNCHING              |                                                       |
| QUEUING    | GEARING ENV          | LAUNCHING              |                                                       |
| ---------- | ------------         | ---------------------- | --------------------------                            |
| TRAINING   | TRAINING             | EXECUTING              |                                                       |
| TRAINING   | RETURNING            | EXECUTING              |                                                       |
| ---------- | ------------         | ---------------------- | --------------------------                            |
| CANCELED   | CANCELED             | COOLING/CLOSING/CLOSED | COOLING/(STOPPING, TERMINATING)/(STOPPED, TERMINATED) |
| COMPLETED  | COMPLETED            | COOLING/CLOSING/CLOSED | COOLING/(STOPPING, TERMINATING)/(STOPPED, TERMINATED) |

## plot_timeline()

```python
def plot_timeline(job_id: str, char_type: str = None)
```

### Parameters

**job_id**: _str_<br/>
The target Job's id.

**char_type**: _str_ = None<br/>
The type of chart. If its value is "pie", a pie chart would be plotted. Otherwise, it is a funnel chart.

### Timeline

This method can be called anytime even if the job has not been ended.

Timeline shows a little more insights than job status.

From the begin to the end, the following time periods are shown on timeline plots:

- **Job Create**: time taken from code execution to job creation.
- **Request Launch**: time taken from a spot request start to fulfilled; this period only applies to spot instance.
- **Instance Connect**: time taken from request fulfilled to successfully establish instance connection.
- **API Transfer & Server Setup**: time taken to set up AIbro API infrastructure in the instance.
- **M&D Serialization**: time taken to serialize model and training data.
- **Env Gear up**: time taken to gear up tensorflow.
- **M&D Transfer**: time taken to transfer model and training data from your local machine to the instance.
- **M&D Deserialization**: time taken to deserialize model and training data.
- **Model Training**: time taken to training the model.
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

Once you submitted a recorded job, AIbro team would use _replay_job()_ method to reproduce the issue. You may also use it to check whether your reported issue is reproducable.

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
The amount of seconds to wait instance request to be fulfilled. You probably want to set a long enough time when
requesting a spot instance with low availability.
