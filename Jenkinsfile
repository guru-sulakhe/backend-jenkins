@Library('Jenkins-shared-library') _
// Library will helps in calling another pipeline from present pipeline
// If a pipeline is including @Library means that it will call another pipeline during execution
// create a variable of map type and set the values

def configMap = [ //this will call nodejsEKS.groovy pipeline
    type: "nodejsEKS"
    component: "backend"
    project: "expense"

]
// calling pipelineDecision.groovy of decidePipeline() function
if ( ! env.BRANCH_NAME.equalsIgnoreCase('main')){ //checking branch of the pipeline is not main branch 
    pipelineDecision.decidePipeline(configMap)
}
else { //if it is main then this will run
    echo "Proceed with CR or Non-Prod pipeline"
}

// when you execute this on pipeline it will automatically invoke pipelineDecision.groovy and also invoke the decidePipeline() function present in it