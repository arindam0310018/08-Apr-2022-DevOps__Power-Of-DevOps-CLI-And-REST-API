# POWER OF DEVOPS CLI AND REST API

Greetings my fellow Technology Advocates and Specialists.

In this Session, I will demonstrate how to __Create and Setup Azure DevOps Project__ with __Best Practices__ using __DEVOPS CLI__, __REST API__ and __DEVOPS PIPELINE__

I had the Privilege to talk on this topic in __FOUR__ Azure Communities:-

| __NAME OF THE AZURE COMMUNITY__ | __TYPE OF SPEAKER SESSION__ |
| --------- | --------- |
| __Journey to the Cloud 5.0__ | __Virtual__ |
| __Microsoft Azure Bern User Group__ | __In Person__ |
| __Cloud Lunch and Learn__ | __Virtual__ |
| __Azure Back To School - 2022__ | __Virtual__ |

| __VIRTUAL SESSION:-__ |
| --------- |
| __LIVE DEMO__ was Recorded as part of my Presentation in __JOURNEY TO THE CLOUD 5.0__ Forum/Platform |
| Duration of My Demo = __41 Mins 42 Secs__ |
| [![IMAGE ALT TEXT HERE](https://img.youtube.com/vi/-8smQnve87k/0.jpg)](https://www.youtube.com/watch?v=-8smQnve87k) |
| __IN-PERSON SESSION:-__ |
| I presented this Demo as a part of __AZURE DEVOPS: TAKEAWAYS BEST PRACTISES AND LIVE DEMOS__ In-Person Speaker Session in __MICROSOFT AZURE BERN USER GROUP__ Forum/Platform. |
| __Event Meetup Announcement:-__ |
| ![Image description](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/cpz6voitr8v43psbgsii.jpg) |
| __Moment Captured with Founders of MICROSOFT AZURE BERN USER GROUP "STEFAN JOHNER", "STEFAN ROTH", "PAUL AFFENTRANGER" and Co-organizer "DAMIEN BOWDEN":-__ |
| ![Image description](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/jxz1bp0dw976e7s3j3c0.JPG) |
| __VIRTUAL SESSION:-__ |
| __LIVE DEMO__ was Recorded as part of my Presentation in __CLOUD LUNCH AND LEARN__ Forum/Platform |
| Duration of My Demo = __54 Mins 34 Secs__ |
| [![IMAGE ALT TEXT HERE](https://img.youtube.com/vi/e4ub3zFF_wE/0.jpg)](https://www.youtube.com/watch?v=e4ub3zFF_wE) |
| __VIRTUAL SESSION:-__ |
| __LIVE DEMO__ was Recorded as part of my Presentation in __AZURE BACK TO SCHOOL - 2022__ Forum/Platform |
| Duration of My Demo = __49 Mins 17 Secs__ |
| [![IMAGE ALT TEXT HERE](https://img.youtube.com/vi/KdqEID2kYCo/0.jpg)](https://www.youtube.com/watch?v=KdqEID2kYCo) |

| __WHAT DOES THE PIPELINE DO:-__ |
| --------- |

| # | PIPELINE TASKS | 
| --------- | --------- |
| 1. | INSTALL AZURE DEVOPS CLI EXTENSION |
| 2. | EXECUTING HELP OPTION OF AZURE DEVOPS CLI | 
| 3. | DISPLAY PAT (PERSONAL ACCESS TOKEN) |
| 4. | CREATE AZURE DEVOPS PROJECT |
| 5. | SET DEFAULTS AZURE DEVOPS ORGANISATION & PROJECT |
| 6. | CREATE REPOSITORIES |
| 7. | INITIALIZE REPOSITORIES |
| 8. | CREATE PIPELINE FOLDERS |
| 9. | CREATE PIPELINE ENVIRONMENT |
| 10. | CREATE AGENT POOL |
| 11. | CREATE POLICY - MINIMUM NUMBER OF REVIEWERS |
| 12. | CREATE POLICY - CHECK FOR LINKED WORK ITEMS |
| 13. | CREATE POLICY - CHECK FOR COMMENT RESOLUTION |
| 14. | CREATE POLICY - LIMIT MERGE TYPES |
| 15. | CREATE POLICY - CODE REVIEWERS |

| __Below follows the contents of the YAML File (Azure DevOps):-__ |
| --------- |

```
trigger:
  none

######################
#DECLARE PARAMETERS:-
######################
parameters:
- name: PAT
  type: object
  default: <Please Provide the PAT Value Here>

- name: DevOpsOrganisation
  type: object
  default: https://dev.azure.com/ArindamMitra0251

- name: DevOpsProjName
  type: object
  default: AM001

- name: DevOpsProjDescription
  type: object
  default: Test Project

######################
#DECLARE VARIABLES:-
######################
variables:
  DevOpsEnv: DEV
  DevOpsPool: AMPool001
  DevOpsGITUser: AM
  DevOpsGITEmail: arindam0310018@gmail.com
  DevOpsOrganisationWithoutHTTPS: dev.azure.com/ArindamMitra0251
  DevOpsPipelineInfraFoldername: Infrastructure
  DevOpsPipelineAppFoldername: Application
  DevOpsReqReviewer1: arindam.mitra@test.onmicrosoft.com

######################
#DECLARE BUILD AGENT:-
######################
pool:
  vmImage: 'ubuntu-latest'

###################
#DECLARE STAGES:-
###################
stages:

- stage: AZ_DEVOPS_PROJECT
  jobs:
  - job: SETUP_DEVOPS_PROJECT
    displayName: SETUP DEVOPS PROJECT
    steps:

########################################################
# Install Az DevOps CLI Extension in the Build Agent:-
#######################################################
    - task: AzureCLI@1
      displayName: INSTALL DEVOPS CLI EXTENSION
      inputs:
        azureSubscription: 'amcloud-cicd-service-connection'
        scriptType: ps
        scriptLocation: inlineScript
        inlineScript: |
          az extension add --name azure-devops
          az extension show --name azure-devops --output table

###############################################################
# Help Option of Az DevOps CLI Extension in the Build Agent:-
###############################################################
    - task: PowerShell@2
      displayName: HELP OPTION OF AZ DEVOPS CLI
      inputs:
        targetType: 'inline'
        script: |
          az devops -h

###################################################################
# Display Az DevOps PAT in the Build Agent:-
##################################################################
    - task: CmdLine@2
      displayName: DISPLAY PAT
      inputs:
        script: |
         echo "PAT TOKEN IS: ${{ parameters.PAT }}"

#########################################################################
# Create DevOps Project:-
# Azure DevOps organization URL required if not configured as default.
# source-control - "git" is default
# visibility - "private" is default
#########################################################################
    - task: PowerShell@2
      displayName: CREATE DEVOPS PROJECT
      inputs:
        targetType: 'inline'
        script: |
         echo "${{ parameters.PAT }}" | az devops login  
         az devops project create --name ${{ parameters.DevOpsProjName }} --description "${{ parameters.DevOpsProjDescription }}" --org ${{ parameters.DevOpsOrganisation }} --process Agile --output table

##################################################
# Set Default DevOps Organization and Project:-
##################################################
    - task: PowerShell@2
      displayName: SET DEFAULTS DEVOPS ORG & PROJECT
      inputs:
        targetType: 'inline'
        script: |
         az devops configure --defaults organization=${{ parameters.DevOpsOrganisation }} project=${{ parameters.DevOpsProjName }}

##########################
# Create Repositories:-
#########################
    - task: PowerShell@2
      displayName: CREATE REPOSITORIES 
      inputs:
        targetType: 'inline'
        script: |
         az repos create --name "${{ parameters.DevOpsProjName }}-INFRASTRUCTURE" -p ${{ parameters.DevOpsProjName }} --org ${{ parameters.DevOpsOrganisation }} --output table
         az repos create --name "${{ parameters.DevOpsProjName }}-APPLICATION" -p ${{ parameters.DevOpsProjName }} --org ${{ parameters.DevOpsOrganisation }} --output table

##############################
# Initialize Repositories:-
#############################
    - task: PowerShell@2
      displayName: INITIALIZE REPOSITORIES 
      inputs:
        targetType: 'inline'
        script: |
         mkdir ${{ parameters.DevOpsProjName }}
         mkdir ${{ parameters.DevOpsProjName }}-APPLICATION

         git config --global user.email "$(DevOpsGITEmail)"
         git config --global user.name "$(DevOpsGITUser)"
         git config --global init.defaultBranch main
         git config --global --unset https.proxy
         
         cd ${{ parameters.DevOpsProjName }}
         git init
         "REPO NAME: ${{ parameters.DevOpsProjName }}" > README.md
         git add .
         git commit -m "Initial commit"
         git remote add origin (az repos list --project "${{ parameters.DevOpsProjName }}" --org ${{ parameters.DevOpsOrganisation }} --query [1].webUrl)
         git push https://$(DevOpsGITUser):${{ parameters.PAT }}@$(DevOpsOrganisationWithoutHTTPS)/${{ parameters.DevOpsProjName }}/_git/${{ parameters.DevOpsProjName }}

         mkdir ${{ parameters.DevOpsProjName }}-INFRASTRUCTURE
         cd ${{ parameters.DevOpsProjName }}-INFRASTRUCTURE
         git init
         "REPO NAME: ${{ parameters.DevOpsProjName }}-INFRASTRUCTURE" > README.md
         git add .
         git commit -m "Initial commit"
         git remote add origin (az repos list --project "${{ parameters.DevOpsProjName }}" --org ${{ parameters.DevOpsOrganisation }} --query [2].webUrl)
         git push https://$(DevOpsGITUser):${{ parameters.PAT }}@$(DevOpsOrganisationWithoutHTTPS)/${{ parameters.DevOpsProjName }}/_git/${{ parameters.DevOpsProjName }}-INFRASTRUCTURE
        

         mkdir ${{ parameters.DevOpsProjName }}-APPLICATION
         cd ${{ parameters.DevOpsProjName }}-APPLICATION
         git init
         "REPO NAME: ${{ parameters.DevOpsProjName }}-APPLICATION" > README.md
         git add .
         git commit -m "Initial commit"
         git remote add origin (az repos list --project "${{ parameters.DevOpsProjName }}" --org ${{ parameters.DevOpsOrganisation }} --query [2].webUrl)
         git push https://$(DevOpsGITUser):${{ parameters.PAT }}@$(DevOpsOrganisationWithoutHTTPS)/${{ parameters.DevOpsProjName }}/_git/${{ parameters.DevOpsProjName }}-APPLICATION

#############################
# Create Pipeline folders:-
#############################
    - task: PowerShell@2
      displayName: CREATE PIPELINE FOLDERS 
      inputs:
        targetType: 'inline'
        script: |
         az pipelines folder create --path \$(DevOpsPipelineInfraFoldername) --org ${{ parameters.DevOpsOrganisation }} -p ${{ parameters.DevOpsProjName }} --output table
         az pipelines folder create --path \$(DevOpsPipelineAppFoldername) --org ${{ parameters.DevOpsOrganisation }} -p ${{ parameters.DevOpsProjName }} --output table
         az pipelines folder list --org ${{ parameters.DevOpsOrganisation }} -p ${{ parameters.DevOpsProjName }} --output table

#############################################
# Create Environment Using DEVOPS REST API:-
#############################################
    - task: PowerShell@2
      displayName: CREATE PIPELINE ENVIRONMENT 
      inputs:
        targetType: 'inline'
        script: |
         $env = "$(DevOpsEnv)"
         $envJSON = @{
         name = $env
         description = "My $env environment"
          }
         $infile = "envdetails.json"
         Set-Content -Path $infile -Value ($envJSON | ConvertTo-Json)
         az devops invoke --area distributedtask --resource environments --route-parameters project=${{ parameters.DevOpsProjName }} --org ${{ parameters.DevOpsOrganisation }} --http-method POST --in-file $infile --api-version "6.0-preview"

#############################################
# Create Agent Pool Using DEVOPS REST API:-
#############################################
    - task: PowerShell@2
      displayName: CREATE AGENT POOL 
      inputs:
        targetType: 'inline'
        script: |
         $pool = "$(DevOpsPool)" 
         $poolJSON = @{
         name = $pool
         description = "My $pool Agent Pool"
          }
         $infile = "pooldetails.json"
         Set-Content -Path $infile -Value ($poolJSON | ConvertTo-Json)
         az devops invoke --area distributedtask --resource pools --route-parameters project=${{ parameters.DevOpsProjName }} --org ${{ parameters.DevOpsOrganisation }} --http-method POST --in-file $infile --api-version "6.0"

###################################
# Branch policies and settings:-
###################################

##########################################
# Require a minimum number of reviewers:-
##########################################
    - task: PowerShell@2
      displayName: POLICY - MIN NUMBER OF REVIEWERS 
      inputs:
        targetType: 'inline'
        script: |
         $repoID = az repos list --project ${{ parameters.DevOpsProjName }} --org ${{ parameters.DevOpsOrganisation }} --query [0].id
         echo $repoID
         az repos policy approver-count create --project ${{ parameters.DevOpsProjName }} --allow-downvotes false --blocking true --branch main --creator-vote-counts false --enabled true --minimum-approver-count 2 --repository-id $repoID --reset-on-source-push true --output table
         
         $repoID = az repos list --project ${{ parameters.DevOpsProjName }} --org ${{ parameters.DevOpsOrganisation }} --query [1].id
         echo $repoID
         az repos policy approver-count create --project ${{ parameters.DevOpsProjName }} --allow-downvotes false --blocking true --branch main --creator-vote-counts false --enabled true --minimum-approver-count 2 --repository-id $repoID --reset-on-source-push true --output table
         
         $repoID = az repos list --project ${{ parameters.DevOpsProjName }} --org ${{ parameters.DevOpsOrganisation }} --query [2].id
         echo $repoID
         az repos policy approver-count create --project ${{ parameters.DevOpsProjName }} --allow-downvotes false --blocking true --branch main --creator-vote-counts false --enabled true --minimum-approver-count 2 --repository-id $repoID --reset-on-source-push true --output table

################################
# Check for Linked Work Items:-
################################
    - task: PowerShell@2
      displayName: POLICY - CHECK FOR LINKED WORK ITEMS 
      inputs:
        targetType: 'inline'
        script: |
         az repos policy work-item-linking create --project ${{ parameters.DevOpsProjName }} --blocking true --branch main --enabled true --repository-id $(az repos list --project ${{ parameters.DevOpsProjName }} --org ${{ parameters.DevOpsOrganisation }} --query [0].id) --output table
         az repos policy work-item-linking create --project ${{ parameters.DevOpsProjName }} --blocking true --branch main --enabled true --repository-id $(az repos list --project ${{ parameters.DevOpsProjName }} --org ${{ parameters.DevOpsOrganisation }} --query [1].id) --output table
         az repos policy work-item-linking create --project ${{ parameters.DevOpsProjName }} --blocking true --branch main --enabled true --repository-id $(az repos list --project ${{ parameters.DevOpsProjName }} --org ${{ parameters.DevOpsOrganisation }} --query [2].id) --output table

################################
# Check for Comment Resolution:-
################################
    - task: PowerShell@2
      displayName: POLICY - CHECK FOR COMMENT RESOLUTION 
      inputs:
        targetType: 'inline'
        script: |
         az repos policy comment-required create --project ${{ parameters.DevOpsProjName }} --blocking true --branch main --enabled true --repository-id $(az repos list --project ${{ parameters.DevOpsProjName }} --org ${{ parameters.DevOpsOrganisation }} --query [0].id) --output table
         az repos policy comment-required create --project ${{ parameters.DevOpsProjName }} --blocking true --branch main --enabled true --repository-id $(az repos list --project ${{ parameters.DevOpsProjName }} --org ${{ parameters.DevOpsOrganisation }} --query [1].id) --output table
         az repos policy comment-required create --project ${{ parameters.DevOpsProjName }} --blocking true --branch main --enabled true --repository-id $(az repos list --project ${{ parameters.DevOpsProjName }} --org ${{ parameters.DevOpsOrganisation }} --query [2].id) --output table

#######################
# Limit merge types:-
#######################
    - task: PowerShell@2
      displayName: POLICY - LIMIT MERGE TYPES 
      inputs:
        targetType: 'inline'
        script: |
         az repos policy merge-strategy create --project ${{ parameters.DevOpsProjName }} --blocking true --branch main --enabled true --repository-id $(az repos list --project ${{ parameters.DevOpsProjName }} --org ${{ parameters.DevOpsOrganisation }} --query [0].id) --allow-no-fast-forward true --allow-rebase true --allow-rebase-merge true --allow-squash true --output table
         az repos policy merge-strategy create --project ${{ parameters.DevOpsProjName }} --blocking true --branch main --enabled true --repository-id $(az repos list --project ${{ parameters.DevOpsProjName }} --org ${{ parameters.DevOpsOrganisation }} --query [1].id) --allow-no-fast-forward true --allow-rebase true --allow-rebase-merge true --allow-squash true --output table
         az repos policy merge-strategy create --project ${{ parameters.DevOpsProjName }} --blocking true --branch main --enabled true --repository-id $(az repos list --project ${{ parameters.DevOpsProjName }} --org ${{ parameters.DevOpsOrganisation }} --query [2].id) --allow-no-fast-forward true --allow-rebase true --allow-rebase-merge true --allow-squash true --output table

###########################################
# Automatically include code reviewers:-
###########################################
    - task: PowerShell@2
      displayName: POLICY - CODE REVIEWERS 
      inputs:
        targetType: 'inline'
        script: |
         az repos policy required-reviewer create --project ${{ parameters.DevOpsProjName }} --blocking true --branch main --enabled true --message "REVIEW REQUIRED" --repository-id $(az repos list --project ${{ parameters.DevOpsProjName }} --org ${{ parameters.DevOpsOrganisation }} --query [0].id) --required-reviewer-ids $(DevOpsReqReviewer1) --output table
         az repos policy required-reviewer create --project ${{ parameters.DevOpsProjName }} --blocking true --branch main --enabled true --message "REVIEW REQUIRED" --repository-id $(az repos list --project ${{ parameters.DevOpsProjName }} --org ${{ parameters.DevOpsOrganisation }} --query [1].id) --required-reviewer-ids $(DevOpsReqReviewer1) --output table
         az repos policy required-reviewer create --project ${{ parameters.DevOpsProjName }} --blocking true --branch main --enabled true --message "REVIEW REQUIRED" --repository-id $(az repos list --project ${{ parameters.DevOpsProjName }} --org ${{ parameters.DevOpsOrganisation }} --query [2].id) --required-reviewer-ids $(DevOpsReqReviewer1) --output table

```
| __PIPELINE RUNTIME VARIABLES:-__ |
| --------- |
| ![Image description](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/i748gv1r18njfhs371np.png) |
| __Personal Access Token (PAT)__ needs to be provided at Runtime which is then __used for DevOps Login__ to __Create DevOps Project__ |

| __Below follows the code snippet for PIPELINE RUNTIME VARIABLES:-__ |
| --------- | 

```
#####################
#DECLARE PARAMETERS:-
######################
parameters:
- name: PAT
  type: object
  default: <Please Provide the PAT Value Here>

- name: DevOpsOrganisation
  type: object
  default: https://dev.azure.com/ArindamMitra0251

- name: DevOpsProjName
  type: object
  default: AM001

- name: DevOpsProjDescription
  type: object
  default: Test Project
```

| __Values of the VARIABLES incase if you wish to change:-__ |
| --------- | 

```
######################
#DECLARE VARIABLES:-
######################
variables:
  DevOpsEnv: DEV
  DevOpsPool: AMPool001
  DevOpsGITUser: AM
  DevOpsGITEmail: arindam0310018@gmail.com
  DevOpsOrganisationWithoutHTTPS: dev.azure.com/ArindamMitra0251
  DevOpsPipelineInfraFoldername: Infrastructure
  DevOpsPipelineAppFoldername: Application
  DevOpsReqReviewer1: arindam.mitra@test.onmicrosoft.com
```


| __PIPELINE RESULTS:-__ |
| --------- |
| ![Image description](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/s6q8yxhzwg0sqy1tdx6o.png) |


| __DEVOPS PROJECT CREATED SUCCESSFULLY:-__ |
| --------- |
| ![Image description](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/kthe0hjw7k7oxzbtzr51.png) |


| __REPOSITORIES CREATED SUCCESSFULLY:-__ |
| --------- |
| ![Image description](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/qu7f3p4e64d0eizf4o6n.png) |

| __REPOSITORIES INITIALISED SUCCESSFULLY:-__ |
| --------- |
| REPO NAME = __AM001__ |
| ![Image description](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/5c9dv9vfq1vdo4xm54g6.png) |
| REPO NAME = __AM001-INFRASTRUCTURE__ |
| ![Image description](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/pitf66xsrje9xo3ecjwf.png) |
| REPO NAME = __AM001-APPLICATION__ |
| ![Image description](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/sicd3tv9bz69gg247hcb.png) |

| __POINTS TO NOTE ON REPOSITORIES INITIALIZATION:-__ |
| --------- |

```
git config --global user.email "$(DevOpsGITEmail)"
git config --global user.name "$(DevOpsGITUser)"
git config --global init.defaultBranch main
```
| If above not added, below __ERROR__ was encountered:- |
| --------- |
| ![Image description](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/1hnh5s98qza5al4b77uu.png) |

```
git config --global --unset https.proxy
```
| If above not added, below __ERROR__ was encountered:- |
| --------- |
| ![Image description](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/0a9puv383jec0sps041r.png) |


```
git push https://$(DevOpsGITUser):${{ parameters.PAT }}@$(DevOpsOrganisationWithoutHTTPS)/${{ parameters.DevOpsProjName }}/_git/${{ parameters.DevOpsProjName }}
```
| If above not added, below __ERROR__ was encountered:- |
| --------- |
| ![Image description](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/310xfapb127ek4nwkt9z.png) |

| __PIPELINES FOLDERS CREATED SUCCESSFULLY:-__ |
| --------- |
| ![Image description](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/nkghfzzu3s7em7xce5rb.png) |

| POINTS TO NOTE ON "__PIPELINE ENVIRONMENT__" AND "__AGENT POOL__":- |
| --------- |
| "__Pipeline Environment__" and "__Agent Pool__" __CANNOT__ be created using Azure DevOps CLI. Hence both are __created using Azure DevOps REST API.__|
| Details on __REST API Add Pipeline Environment__ can be found [HERE](https://docs.microsoft.com/en-us/rest/api/azure/devops/distributedtask/environments/add?view=azure-devops-rest-7.1)    |
| Details on __REST API Add Agent__ can be found [HERE](https://docs.microsoft.com/en-us/rest/api/azure/devops/distributedtask/agents/add?view=azure-devops-rest-7.1)  |

| __PIPELINE ENVIRONMENT CREATED SUCCESSFULLY USING DEVOPS REST API:-__ |
| --------- |
| ![Image description](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/pn7z4raok534tdcdw84v.png) |

| __Below follows the code snippet for PIPELINE ENVIRONMENT:-__ |
| --------- | 

```
#############################################
# Create Environment Using DEVOPS REST API:-
#############################################
    - task: PowerShell@2
      displayName: CREATE PIPELINE ENVIRONMENT 
      inputs:
        targetType: 'inline'
        script: |
         $env = "$(DevOpsEnv)"
         $envJSON = @{
         name = $env
         description = "My $env environment"
          }
         $infile = "envdetails.json"
         Set-Content -Path $infile -Value ($envJSON | ConvertTo-Json)
         az devops invoke --area distributedtask --resource environments --route-parameters project=${{ parameters.DevOpsProjName }} --org ${{ parameters.DevOpsOrganisation }} --http-method POST --in-file $infile --api-version "6.0-preview"
```

| __AGENT POOL CREATED SUCCESSFULLY USING DEVOPS REST API:-__ |
| --------- |
| ![Image description](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/g4gdu1n0giubushyvunc.png) |

| __Below follows the code snippet for AGENT POOL:-__ |
| --------- | 

```
#############################################
# Create Agent Pool Using DEVOPS REST API:-
#############################################
    - task: PowerShell@2
      displayName: CREATE AGENT POOL 
      inputs:
        targetType: 'inline'
        script: |
         $pool = "$(DevOpsPool)" 
         $poolJSON = @{
         name = $pool
         description = "My $pool Agent Pool"
          }
         $infile = "pooldetails.json"
         Set-Content -Path $infile -Value ($poolJSON | ConvertTo-Json)
         az devops invoke --area distributedtask --resource pools --route-parameters project=${{ parameters.DevOpsProjName }} --org ${{ parameters.DevOpsOrganisation }} --http-method POST --in-file $infile --api-version "6.0"
```

| __BRANCH POLICIES CREATED SUCCESSFULLY:-__ |
| --------- |
| Policies are 1) __MINIMUM NUMBER OF REVIEWERS__ 2) __CHECK FOR LINKED WORK ITEMS__ 3) __CHECK FOR COMMENT RESOLUTION__, 4) __LIMIT MERGE TYPES__, and 5) __CODE REVIEWERS__ |
| ![Image description](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/rzm9eo8hnaf09vn5nbev.png) |
| ![Image description](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/74a2goxknaizv6upndcw.png) |

| __Below follows the code snippet for BRANCH POLICIES:-__ |
| --------- | 

```
###################################
# Branch policies and settings:-
###################################

##########################################
# Require a minimum number of reviewers:-
##########################################
    - task: PowerShell@2
      displayName: POLICY - MIN NUMBER OF REVIEWERS 
      inputs:
        targetType: 'inline'
        script: |
         $repoID = az repos list --project ${{ parameters.DevOpsProjName }} --org ${{ parameters.DevOpsOrganisation }} --query [0].id
         echo $repoID
         az repos policy approver-count create --project ${{ parameters.DevOpsProjName }} --allow-downvotes false --blocking true --branch main --creator-vote-counts false --enabled true --minimum-approver-count 2 --repository-id $repoID --reset-on-source-push true --output table
         
         $repoID = az repos list --project ${{ parameters.DevOpsProjName }} --org ${{ parameters.DevOpsOrganisation }} --query [1].id
         echo $repoID
         az repos policy approver-count create --project ${{ parameters.DevOpsProjName }} --allow-downvotes false --blocking true --branch main --creator-vote-counts false --enabled true --minimum-approver-count 2 --repository-id $repoID --reset-on-source-push true --output table
         
         $repoID = az repos list --project ${{ parameters.DevOpsProjName }} --org ${{ parameters.DevOpsOrganisation }} --query [2].id
         echo $repoID
         az repos policy approver-count create --project ${{ parameters.DevOpsProjName }} --allow-downvotes false --blocking true --branch main --creator-vote-counts false --enabled true --minimum-approver-count 2 --repository-id $repoID --reset-on-source-push true --output table
################################
# Check for Linked Work Items:-
################################
    - task: PowerShell@2
      displayName: POLICY - CHECK FOR LINKED WORK ITEMS 
      inputs:
        targetType: 'inline'
        script: |
         az repos policy work-item-linking create --project ${{ parameters.DevOpsProjName }} --blocking true --branch main --enabled true --repository-id $(az repos list --project ${{ parameters.DevOpsProjName }} --org ${{ parameters.DevOpsOrganisation }} --query [0].id) --output table
         az repos policy work-item-linking create --project ${{ parameters.DevOpsProjName }} --blocking true --branch main --enabled true --repository-id $(az repos list --project ${{ parameters.DevOpsProjName }} --org ${{ parameters.DevOpsOrganisation }} --query [1].id) --output table
         az repos policy work-item-linking create --project ${{ parameters.DevOpsProjName }} --blocking true --branch main --enabled true --repository-id $(az repos list --project ${{ parameters.DevOpsProjName }} --org ${{ parameters.DevOpsOrganisation }} --query [2].id) --output table
################################
# Check for Comment Resolution:-
################################
    - task: PowerShell@2
      displayName: POLICY - CHECK FOR COMMENT RESOLUTION 
      inputs:
        targetType: 'inline'
        script: |
         az repos policy comment-required create --project ${{ parameters.DevOpsProjName }} --blocking true --branch main --enabled true --repository-id $(az repos list --project ${{ parameters.DevOpsProjName }} --org ${{ parameters.DevOpsOrganisation }} --query [0].id) --output table
         az repos policy comment-required create --project ${{ parameters.DevOpsProjName }} --blocking true --branch main --enabled true --repository-id $(az repos list --project ${{ parameters.DevOpsProjName }} --org ${{ parameters.DevOpsOrganisation }} --query [1].id) --output table
         az repos policy comment-required create --project ${{ parameters.DevOpsProjName }} --blocking true --branch main --enabled true --repository-id $(az repos list --project ${{ parameters.DevOpsProjName }} --org ${{ parameters.DevOpsOrganisation }} --query [2].id) --output table
#######################
# Limit merge types:-
#######################
    - task: PowerShell@2
      displayName: POLICY - LIMIT MERGE TYPES 
      inputs:
        targetType: 'inline'
        script: |
         az repos policy merge-strategy create --project ${{ parameters.DevOpsProjName }} --blocking true --branch main --enabled true --repository-id $(az repos list --project ${{ parameters.DevOpsProjName }} --org ${{ parameters.DevOpsOrganisation }} --query [0].id) --allow-no-fast-forward true --allow-rebase true --allow-rebase-merge true --allow-squash true --output table
         az repos policy merge-strategy create --project ${{ parameters.DevOpsProjName }} --blocking true --branch main --enabled true --repository-id $(az repos list --project ${{ parameters.DevOpsProjName }} --org ${{ parameters.DevOpsOrganisation }} --query [1].id) --allow-no-fast-forward true --allow-rebase true --allow-rebase-merge true --allow-squash true --output table
         az repos policy merge-strategy create --project ${{ parameters.DevOpsProjName }} --blocking true --branch main --enabled true --repository-id $(az repos list --project ${{ parameters.DevOpsProjName }} --org ${{ parameters.DevOpsOrganisation }} --query [2].id) --allow-no-fast-forward true --allow-rebase true --allow-rebase-merge true --allow-squash true --output table
###########################################
# Automatically include code reviewers:-
###########################################
    - task: PowerShell@2
      displayName: POLICY - CODE REVIEWERS 
      inputs:
        targetType: 'inline'
        script: |
         az repos policy required-reviewer create --project ${{ parameters.DevOpsProjName }} --blocking true --branch main --enabled true --message "REVIEW REQUIRED" --repository-id $(az repos list --project ${{ parameters.DevOpsProjName }} --org ${{ parameters.DevOpsOrganisation }} --query [0].id) --required-reviewer-ids $(DevOpsReqReviewer1) --output table
         az repos policy required-reviewer create --project ${{ parameters.DevOpsProjName }} --blocking true --branch main --enabled true --message "REVIEW REQUIRED" --repository-id $(az repos list --project ${{ parameters.DevOpsProjName }} --org ${{ parameters.DevOpsOrganisation }} --query [1].id) --required-reviewer-ids $(DevOpsReqReviewer1) --output table
         az repos policy required-reviewer create --project ${{ parameters.DevOpsProjName }} --blocking true --branch main --enabled true --message "REVIEW REQUIRED" --repository-id $(az repos list --project ${{ parameters.DevOpsProjName }} --org ${{ parameters.DevOpsOrganisation }} --query [2].id) --required-reviewer-ids $(DevOpsReqReviewer1) --output table
```

Hope You Enjoyed the Session!!!

__Stay Safe | Keep Learning | Spread Knowledge__
