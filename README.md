## TaskQuant
This is a CLI application that helps Taskwarrior users quantify their productivity by tracking the 'reward' attribute in their tasks.

`reward` is a custom [User Defined Attributes](https://taskwarrior.org/docs/udas.html). You will need to have that configured in your `.taskrc` file. A sample configuration for this attribute would be:

```
uda.reward.type=numeric
uda.reward.label=reward
uda.reward.default=0.0
```
After this, it is recommended to update all your tasks to have one default value as reward. You can do it by run
```bash
task rc.allow.empty.filter=1 modify reward:0
```
To change your report to show this new column, just add to your `.taskrc` this lines
```
default.command=ready
report.ready.labels=ID,Active,Age,D,P,Project,Tags,R,S,Due,Until,Description,Reward,Urg
report.ready.columns=id,start.age,entry.age,depends.indicator,priority,project,tags,recur.indicator,scheduled.countdown,due.countdown,until.remaining,description,reward,urgency
report.completed.labels=ID,UUID,Created,Completed,Age,Deps,P,Project,Tags,R,Reward,Due,Description
report.completed.columns=id,uuid.short,entry,end,entry.age,depends,priority,project,tags,recur.indicator,reward,due,description
```


It allow you to create rewards to your tasks. For example, try `task add Take out the garbage reward:1`.
Not only, but it allow you to create some tasks with negative reward value, that you can interpret as one award that you pay with the accumulated rewards. Try `task log Watch a movie reward:-10 +award`.
> I suggest you to always duplicate the award tasks before use done on these.
Another good idea is to create some contexts to filter for this awards:
```
context.work.read=project:work and (reward.after:0 or reward:0)
context.market.read=reward.before:0 or +award
context.market.write=project:fun +award
urgency.user.tag.award.coefficient=-15.0
```

TaskQuant will then compute the rewards you have accumulated across the different tasks and return a rewardsheet.

TaskQuant **has no external dependencies** except `tasklib`, which is also by [the same organization](https://github.com/GothenburgBitFactory) that developed Taskwarrior. 

It is written entirely in Python, using standard library. It may have optional dependencies, but those are not required and a fallback option will always be used by default. 

For those reasons, TaskQuant is extremely lightweight. As it stands, it's only 6.8kb of code, and should install in under a second.

#### Installation
TaskQuant is available on [pypi](https://pypi.org/project/taskquant/).
```
pip install taskquant
```
If you so desire, you can optionally install `tabulate` to get a prettier table output (this is completely optional, and pure cosmetic):
```
pip install tabulate
```


### Usage
Install the package and execute:

```bash
tq 
```

- It supports an optional `-p` (`path`) argument to specify the path to your `.task` file. **Especially** helpful if you changed the default location of your `.task` file.
- It supports an optional `-v` (`verbose`) argument to print out additional
- It supports an optional `-b` (`balance`) argument to print just your current balance
information in its output.

```bash
tq -p ~/vaults/tasks -v 

# outputs:
+------------+-------+------------+
|    Date    | reward |  Balance  |
+------------+-------+------------+
| 2022-03-09 |   0   |     0      |
| 2022-03-10 |   8   |     8      |
| 2022-03-11 |   1   |     9      |
| 2022-03-12 |  43   |     52     |
| 2022-03-13 |   4   |     56     |
| 2022-03-14 |   4   |     60     |
| 2022-03-15 |   7   |     67     |
| 2022-03-16 |   9   |     76     |
| 2022-03-17 |   4   |     80     |
| 2022-03-18 |   4   |     84     |
| 2022-03-19 |   3   |     87     |
| 2022-03-20 |   2   |     89     |
| 2022-03-21 |   5   |     94     |
+------------+-------+------------+
Total completed tasks: 48
Active dates: 13
task_path: /home/samuel/vaults/tasks
```
```bash
tq -p ~/vaults/tasks -b 
94
```


You can also print a weekly (`-w`) version of the rewardsheet:

```bash
tq -w 
+-------+-------+------------+
| Week# | reward |  Balance  |
+-------+-------+------------+
|  10   |  56   |     56     |
|  11   |  33   |     89     |
|  12   |  26   |    115     |
|  13   |  12   |    127     |
+-------+-------+------------+
```
The first column refers to the ISO week number of the year.


- To see all optional arguments, use the `-h` (`help`) argument.

```bash
tq -h
```

#### Dependencies
- [tasklib](https://github.com/GothenburgBitFactory/tasklib)
- (Optional) [tabulate](https://github.com/astanin/python-tabulate)

#### Testing
Tests are written in `unittest` and stored in the `tests` directory.

To execute tests without arguments and automatic test discovery:

```bash
python -m unittest 
```

To test a specific test file (`-v` for verbose output):

```bash
python -m unittest -v tests/test_accum.py

test_create_combined_table (tests.test_accum.TestAccum) ... ok
test_create_full_date ... ok
test_create_table_auto (tests.test_accum.TestAccum) ... ok
test_extract_tasks (tests.test_accum.TestAccum) ... ok
test_fill_rolling_date (tests.test_accum.TestAccum) ... ok
test_invalid_path_warning (tests.test_accum.TestAccum) ... ok
test_task_to_dict (tests.test_accum.TestAccum) ... ok

----------------------------------------------------------------------
Ran 7 tests in 0.049s

OK


```

### Roadmap
- Add terminal-based charts and graphs
- New ways to visualize rewards based on tags, projects or other attributes

### Links to Tutorials
Watch how we build this package, line by line (detailed tutorial):
- [Building TaskQuant | Part 1](https://youtu.be/lT2jqmhRkxo)
- [Taskwarrior: Terminal-based task management Quick Tour](https://youtu.be/cDYIes9avW4)
- [Building TaskQuant | Full Playlist](https://youtube.com/playlist?list=PLXsFtK46HZxXIVE4tRjwMjwKFVaQSdT5W)
- [Dev.to article: Todo + gamification with Taskwarrior & Taskquant](https://dev.to/onlyphantom/todo-gamification-with-taskwarrior-taskquant-3e38)
