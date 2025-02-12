@Library('Jenkins-shared-library') _

// create a variable of map type and set the values

def configMap = [
    type: "nodejsEKS"
    component: "backend"
    project: "expense"

]
//calling pipelineDecision.groovy of decidePipeline() function
pipelineDecision.decidePipeline(configMap)

// when you execute this on pipeline it will automatically invoke pipelineDecision.groovy and also invoke the decidePipeline() function present in it