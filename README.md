## neo4j_graph_COVID19
Play just for fun with neo4j and how visualize propagations of COVID to a small group of people

![Image description](https://github.com/mrNicky/neo4j_graph_COVID19/blob/master/graph_global.png)

#### Create
CREATE(p:Person{nom:"Le Roy", prenom:"François", age:55})
CREATE(v:Virus{nom: "Covid19"})

#### Read
MATCH(p:Person{nom:"Le Roy"})
MATCH(v:Virus{nom:"Covid19"}) 
return p,v

#### Link two Nodes
MATCH(p:Person{nom:"Le Roy"})
MATCH(v:Virus{nom:"Covid19"})
CREATE(v)-[:Sicken]→(p)

#### Get all nodes
MATCH(n) RETURN n

#### One node alone
CREATE(p:Person{nom:"Dubois",prenom:"Julia",age:30})

#### Get nodes with link
MATCH(p1)-[]->(p2) RETURN p1,p2

#### Delete a specific node 
MATCH(p:Person{age:22}) DELETE p

#### Creates Nodes and link them at the same times
CREATE(p1:Person{prenom:"sam", 22})
CREATE(p2:Person{nom:"Laure", age:40})
CREATE(p3:Person{nom:"Mu", prenom:"Shee"})
CREATE(p1)-[:Friend]->(p2)
CREATE(p2)-[:Friend]->(p1)

#### Find Sicken relation and set actual time
MATCH(v:Virus)-[:Sicken]->(p:Person) 
SET s.at = datetime()
RETURN v,p

#### Virus propagation to sam 
MATCH(v:Virus{nom:"Covid19"})
MATCH(p2:Person) WHERE (v)-[:Sicken]->(p2) 
MATCH(p:Person{prenom:"sam"})
CREATE(p2)-[:Contaminated]->(p)
RETURN v,p,p2

#### Virus grippe
CREATE(v:Virus{nom:"Grippe"})

#### Virus grippe to Julia
MATCH(v:Virus{nom:"Grippe"})
MATCH(p:Person{prenom:"Julia"})
CREATE (v)-[s:Sicken]->(p)
SET s.at = datetime()
RETURN v,p

#### Sam want to become friend with Julia, but she doesn’t want ! So Julia can’t has Covid19 from sam !
MATCH(p1:Person{prenom:"sam"})
MATCH(p2:Person{prenom:"Julia"})
CREATE (p1)-[:Friend]->(p2) 
RETURN p1, p2


#### Select Potential covid19 contamination of friend !
MATCH(p1:Person)-[:Contaminated|:Friend]->(p2:Person)
MATCH(p2)-[Friend]->(p1)
MATCH(v:Virus{nom:"Covid19"})-[:Sicken]->(p3:Person) 
CREATE(p1)-[d:Contaminated]->(p2)
SET d.at = datetime()
RETURN p1,p2,p3,v

#### Number of sick persons
MATCH()-[:Sicken|:Contaminated]->(p:Person) RETURN count(distinct p)
