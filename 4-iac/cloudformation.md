# CloudFormation

- CloudFormation is a declarative way of outlining AWS infrastructure for any resources

## Benefits of CloudFormation

- Infrastructure as code:
    - No resources are manually created, which is excellent for control
    - The code can be version controlled
    - Changes to the infrastructure are reviewed through code reviews
- Cost:
    - Each resource within the stack is tagged with an identifier, we can easily see how much a stack costs
    - We can estimate the cost of the resources using CloudFormation templates
    - Saving strategy: in dev, we can automate deletion of templates at 5PM and recreate the whole stack at 8AM in the morning
- Productivity:
    - Ability to destroy and re-create an infrastructure on the cloud on the fly
    - Automated generation of diagram for the templates
    - Declarative programming, no need to figure out ordering and orchestration
- Separation of concerns: create many CF stacks for applications and layers

## How CloudFormation Works

- Templates have to uploaded to S3 and then be references in CloudFormation
- To update a template we can't edit previous ones. We have to re-upload a new version of the template to AWS
- Stacks are identified by a name
- Deleting a stack deletes every single artifact that was created by CloudFormation

## Deploy CloudFormation Templates

- Manual way:
    - Editing templates in the CloudFormation Designer
    - Using the console to input parameters, etc.
- Automated way:
    - Templates are YAML files, we can edit them in a text editor
    - Using the AWS CLI we can deploy them
    - Recommended way for automated workflow

## CloudFormation Building Blocks

- Template components:
    1. Resources: AWS resources declared in the template (MANDATORY)
    2. Parameters: dynamic input for the templates
    3. Mappings: static variables for the templates
    4. Outputs: references to what has be created
    5. Conditionals: list of conditions ot perform resource creation
    6. Metadata
- Template helpers:
    1. References
    2. Functions