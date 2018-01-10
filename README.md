# ms-monitor
MS-monitor is a MicroService (MS).


## Development requirements

### Goals
The MS-monitor is divided in 2 parts : the client and the server.  
- The client needs to be able to :
  - convert a JSON to a graph and vice versa (see the [Representations](#representations) section) ;
  - use D3 (SVG) to draw graphs (see the [Representations](#representations) section) ;
  - treat HTML routes ;
- The server must be able to :
  - receive a namespace, find the associated pipeline and its tasks (see the [Pipeline definitions](#definitions) section) ;
  - find the state of all the tasks composing a pipeline (see the [Pipeline](#pipeline) section) ;
  - interact with other MS thanks to packets (see the [Task states](#task-states) section) ;

### Pipeline

#### Definitions
A pipeline is :
- a sequence of tasks, organized in graph ;
- described by a JSON ;
- saved in a directory named **namespace**, containing one directory per task ;

A task wraps a bioinformatic job in order to manage its pre- and post-processing. The interest is that tasks can be linked up to each other and thereby form a pipeline :
```
A ---→ B
```
In this pipeline, the results of the task A will be received as input by the task B.


#### Representations
A pipeline can be represented with :
- a graph in a web interface, using the D3 library ([Force-Directed Graph][1]) ;
- a JSON for informatic purposes.  
Therefore, the ms-monitor must be able to convert a graph into a JSON and conversely.  
For example, for the pipeline represented by this graph :

```
 ┌-→ 001 -→ 002 ┐
 |              ↓
000            004
 |              ↑
 └----→ 003 ----┘
```

The JSON file is :

```
{
    'type' : 'pipeline',
    'namespace' : '1147aef7-6c57-409a-8f5c-9b9b2349a9b3',
    'nodes' : [
        {'id' : '000', 'type' : 'task'},
        {'id' : '001', 'type' : 'task'},
        {'id' : '002', 'type' : 'task'},
        {'id' : '003', 'type' : 'task'},
        {'id' : '004', 'type' : 'task'}
    ],
    'links' : [
        {'source' : '000', 'target' : '001'},
        {'source' : '001', 'target' : '002'},
        {'source' : '002', 'target' : '004'},
        {'source' : '000', 'target' : '003'},
        {'source' : '003', 'target' : '004'}
    ]
}
```


#### Task states
Each task in a pipeline can be :
- idle : no data received at all (since the instanciation of the task) ;
- pending : data received but the calculations are waiting for ressources ;
- running ;
- finished with error (lost, error during calculation, etc.) ;
- finished without error.  

The ms-monitor must interact with a JobManager (JM) to find the state of a task. In our case, JM is nslurm ([GitHub repo][2], [NPM package][3]). 

>**---------- Notes for later ----------**  
>The states of a pipeline can be :
>- idle : ?
>- pending : all the tasks are pending ;
>- running : at least one task is running AND all the finished tasks has no error ;
>- finished with error : at least one task has an error ;
>- successfully finished : all the tasks end successfully.
>
>To summarise, for a pipeline possible state (in a row), the number of task of each state (in the columns) :
>
>| Pipeline ↓ \ Task → | idle | pending | running | error | success |
>|:---:|:---:|:---:|:---:|:---:|:---:|
>| **pending** | ≥ 0 | ≥ 1 | 0 | 0 | 0 |
>| **running** | ≥ 0 | ≥ 0 | ≥ 1 | 0 | ≥ 1 |
>| **error** | ≥ 0 | ≥ 0 | ≥ 0 | ≥ 0 | ≥ 1 | ≥ 0 |
>| **success** | 0 | 0 | 0 | 0 | all |


### Specifications




### Packets





[1]: https://bl.ocks.org/mbostock/4062045
[2]: https://github.com/glaunay/nslurm
[3]: https://www.npmjs.com/package/nslurm
