<H3> Experiment : 1</H3>
<H3>DATE:</H3>
<H3>ENTER YOUR NAME :DHANASHREE M</H3>
<H3>ENTER YOUR REGISTER NO :212221230018</H3>

<H1 ALIGN=CENTER> Implementation of Bayesian Networks</H1>

##  Aim :

    To create a bayesian Network for the given dataset in Python
## Algorithm:

### Step 1:
Import necessary libraries: pandas, networkx, matplotlib.pyplot, Bbn, Edge, EdgeType, BbnNode, Variable, EvidenceBuilder, InferenceController<br/>
### Step 2:
Set pandas options to display more columns<br/>
### Step 3:
Read in weather data from a CSV file using pandas<br/>
### Step 4:
Remove records where the target variable RainTomorrow has missing values<br/>
### Step 5:
Fill in missing values in other columns with the column mean<br/>
### Step 6:
Create bands for variables that will be used in the model (Humidity9amCat, Humidity3pmCat, and WindGustSpeedCat)<br/>
### Step 7:
Define a function to calculate probability distributions, which go into the Bayesian Belief Network (BBN)<br/>
### Step 8:
Create BbnNode objects for Humidity9amCat, Humidity3pmCat, WindGustSpeedCat, and RainTomorrow, using the probs() function to calculate their probabilities<br/>
### Step 9:
Create a Bbn object and add the BbnNode objects to it, along with edges between the nodes<br/>
### Step 10:
Convert the BBN to a join tree using the InferenceController<br/>
### Step 11:
Set node positions for the graph<br/>
### Step 12:
Set options for the graph appearance<br/>
### Step 13:
Generate the graph using networkx<br/>
### Step 14:
Update margins and display the graph using matplotlib.pyplot<br/>

## Program:
```
 Name :DHANASHREE M
 Register No.212221230018
```
```
!pip install pybbn

import pandas as pd
import networkx as nx
import matplotlib.pyplot as plt
from pybbn.graph.dag import Bbn
from pybbn.graph.edge import Edge, EdgeType
from pybbn.graph.jointree import EvidenceBuilder
from pybbn.graph.node import BbnNode
from pybbn.graph.variable import Variable
from pybbn.pptc.inferencecontroller import InferenceController #Set Pandas options to display more columns
pd.options.display.max_columns=50
df=pd.read_csv('weatherAUS.csv', encoding='utf-8')
df=df[pd.isnull(df['RainTomorrow'])==False]
df=df.fillna(df.mean())
df['WindGustSpeedCat']=df['WindGustSpeed'].apply(lambda x: '0.<=40' if x<=40 else 
                                                   '1.40-50'  if 40<x<=50 else '2.>50')
df['Humidity9amCat' ]=df[ 'Humidity9am'].apply(lambda x: '1.>60' if x>60 else '0.<=60')
df['Humidity3pmCat']=df['Humidity3pm'].apply(lambda x: '1.>60' if x>60 else '0.<=60')
print(df)

def probs(data, child, parent1=None, parent2=None):
  if parent1==None:
    prob=pd.crosstab(data[child],'Empty', margins=False, normalize='columns').sort_index().to_numpy().reshape(-1).tolist()
  elif parent1!=None:
    if parent2==None:
      prob=pd.crosstab(data[parent1],data[child], margins=False, normalize='index').sort_index().to_numpy().reshape(-1).tolist()
    else:
      prob=pd. crosstab([data[parent1], data[parent2]],data[child], margins=False, normalize='index').sort_index().to_numpy().reshape(-1).tolist()
  else: print("Error in Probability Frequency Calculations")
  return prob
H9am = BbnNode(Variable(0, 'H9am', ['<=60', '>60']), probs(df, child='Humidity9amCat'))
H3pm = BbnNode(Variable(1, 'H3pm', ['<=60', '>60']), probs(df, child= 'Humidity3pmCat', parent1='Humidity9amCat'))
W =BbnNode(Variable(2, 'W', ['<=40', '40-50', '>50']), probs(df, child='WindGustSpeedCat'))
RT = BbnNode(Variable(3, 'RT', ['No', 'Yes']), probs(df, child='RainTomorrow', parent1='Humidity3pmCat', parent2='WindGustSpeedCat'))

bbn= Bbn() \
  .add_node(H9am) \
  .add_node(H3pm) \
  .add_node(W) \
  .add_node(RT) \
  .add_edge(Edge(H9am,H3pm, EdgeType.DIRECTED)) \
  .add_edge(Edge(H3pm, RT, EdgeType.DIRECTED)) \
  .add_edge(Edge(W,RT, EdgeType.DIRECTED))

join_tree =InferenceController.apply(bbn)
pos={0: (-1,2), 1: (-1, 0.5), 2: (1, 0.5), 3:(0,-1)}
options ={
"font_size": 16,
"node_size": 4000,
"node_color": "red",
"edgecolors": "blue",
"edge_color": "black",
"linewidths": 5,
"width": 5,
}
n,d=bbn.to_nx_graph()
nx.draw(n, with_labels=True,labels=d, pos=pos, **options)

ax=plt.gca()
ax.margins (0.20)
plt.axis("off")
plt.show()

import networkx as nx
import pandas as pd
import matplotlib.pyplot as plt
from pybbn.graph.dag import Bbn
from pybbn.graph.dag import Edge,EdgeType
from pybbn.graph.jointree import EvidenceBuilder
from pybbn.graph.node import BbnNode
from pybbn.graph.variable import Variable
from pybbn.pptc.inferencecontroller import InferenceController
pd.options.display.max_columns=50
df=pd.read_csv('weatherAUS.csv',encoding='utf-8')
df=df[pd.isnull(df['RainTomorrow'])==False]
# For other columns with missing values, fill them in with column mean
df=df.fillna(df.mean())
# Create bands for variables that we want to use in the model
df['WindGustSpeedCat']=df['WindGustSpeed'].apply(lambda x: '0.<=40'   if x<=40 else
                                                            '1.40-50' if 40<x<=50 else '2.>50')
df['Humidity9amCat']=df['Humidity9am'].apply(lambda x: '1.>60' if x>60 else '0.<=60')
df['Humidity3pmCat']=df['Humidity3pm'].apply(lambda x: '1.>60' if x>60 else '0.<=60')
# Show a snaphsot of data
print(df)
# This function helps to calculate probability distribution, which goes into BBN (note, can handle up to 2 parents)
def probs(data, child, parent1=None, parent2=None):
    if parent1==None:
        # Calculate probabilities
        prob=pd.crosstab(data[child], 'Empty', margins=False, normalize='columns').sort_index().to_numpy().reshape(-1).tolist()
    elif parent1!=None:
            # Check if child node has 1 parent or 2 parents
            if parent2==None:
                # Caclucate probabilities
                prob=pd.crosstab(data[parent1],data[child], margins=False, normalize='index').sort_index().to_numpy().reshape(-1).tolist()
            else:
                # Caclucate probabilities
                prob=pd.crosstab([data[parent1],data[parent2]],data[child], margins=False, normalize='index').sort_index().to_numpy().reshape(-1).tolist()
    else: print("Error in Probability Frequency Calculations")
    return prob
# Create nodes by using our earlier function to automatically calculate probabilities
H9am = BbnNode(Variable(0, 'H9am', ['<=60', '>60']), probs(df, child='Humidity9amCat'))
H3pm = BbnNode(Variable(1, 'H3pm', ['<=60', '>60']), probs(df, child='Humidity3pmCat', parent1='Humidity9amCat'))
W = BbnNode(Variable(2, 'W', ['<=40', '40-50', '>50']), probs(df, child='WindGustSpeedCat'))
RT = BbnNode(Variable(3, 'RT', ['No', 'Yes']), probs(df, child='RainTomorrow', parent1='Humidity3pmCat', parent2='WindGustSpeedCat'))

# Create Network
bbn = Bbn() \
    .add_node(H9am) \
    .add_node(H3pm) \
    .add_node(W) \
    .add_node(RT) \
    .add_edge(Edge(H9am, H3pm, EdgeType.DIRECTED)) \
    .add_edge(Edge(H3pm, RT, EdgeType.DIRECTED)) \
    .add_edge(Edge(W, RT, EdgeType.DIRECTED))

# Convert the BBN to a join tree
join_tree = InferenceController.apply(bbn)
# Set node positions
pos = {0: (-1, -2), 1: (-1, 0.5), 2: (1, 0.5), 3: (0, -1)}

# Set options for graph looks
options = {
    "font_size": 16,
    "node_size": 4000,
    "node_color": "green",
    "edgecolors": "blue",
    "edge_color": "black",
    "linewidths": 5,
    "width": 5,}

# Generate graph
n, d = bbn.to_nx_graph()
nx.draw(n, with_labels=True, labels=d, pos=pos, **options)

# Update margins and print the graph
ax = plt.gca()
ax.margins(0.10)
plt.axis("off")
plt.show()
```
## Output:
![1](https://github.com/Dhanashreemullaithasan/Ex1-AAI/assets/94165415/494b5333-143c-4bdd-8c87-362d5620af37)

![2](https://github.com/Dhanashreemullaithasan/Ex1-AAI/assets/94165415/b4ff8c80-0f3e-4fa8-abd7-63a254ede259)

![3](https://github.com/Dhanashreemullaithasan/Ex1-AAI/assets/94165415/1e1d8d86-7405-42b7-8083-67745e43b322)

![4](https://github.com/Dhanashreemullaithasan/Ex1-AAI/assets/94165415/d6988703-d70f-4ddd-8f0f-528102eb6695)

![5](https://github.com/Dhanashreemullaithasan/Ex1-AAI/assets/94165415/bf484fee-47ff-410a-8ce7-887b7944544f)

![6](https://github.com/Dhanashreemullaithasan/Ex1-AAI/assets/94165415/acc9b3a9-5e7d-4ed6-be57-426ae1031411)

![7](https://github.com/Dhanashreemullaithasan/Ex1-AAI/assets/94165415/296703f6-5b5e-49fe-8470-6bf82a52bce1)

![8](https://github.com/Dhanashreemullaithasan/Ex1-AAI/assets/94165415/0cad285e-7796-4bcc-82b8-9b9fe20e4f04)

## Result:
   Thus a Bayesian Network is generated using Python

