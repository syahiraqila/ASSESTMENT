
# Listener
//Create multiple issues upon one issue creation using a Script Listener.

import com.atlassian.jira.component.ComponentAccessor
import com.atlassian.jira.issue.Issue
import com.atlassian.jira.issue.IssueInputParametersImpl

def originalissue = event.issue
if (originalissue.labels*.label.contains("UI not working")){
    createSubtask(originalissue)
    createTask(originalIssue)
}
def createSubtask(Issue parentIssue) {
    def subTaskManager = ComponentAccessor.subTaskManager
    def asUser = ComponentAccessor.jiraAuthenticationContext.loggedInUser
    def constantsManager = ComponentAccessor.constantsManager
    def issueService = ComponentAccessor.issueService

    def subtaskIssueType = constantsManager.allIssueTypeObjects.findByName('Sub-task')

    assert subtaskIssueType?.subTask

    // Fill the required fields
    def issueInputParameters1 = new IssueInputParametersImpl()
    issueInputParameters1
        .setProjectId(parentIssue.projectId)
        .setIssueTypeId(subtaskIssueType.id)
        .setSummary('Reproduction steps')
        .setReporterId(asUser.username)

    def issueInputParameters2 = new IssueInputParametersImpl()
    issueInputParameters2
        .setProjectId(parentIssue.projectId)
        .setIssueTypeId(subtaskIssueType.id)
        .setSummary('UAT')
        .setReporterId(asUser.username)

    def createValidationResult1 = ComponentAccessor.issueService.validateSubTaskCreate(asUser, parentIssue.id, issueInputParameters1)
    if (!createValidationResult1.valid) {
        log.error createValidationResult1.errorCollection
        return
    }

    def createValidationResult2 = ComponentAccessor.issueService.validateSubTaskCreate(asUser, parentIssue.id, issueInputParameters2)
    if (!createValidationResult2.valid) {
        log.error createValidationResult2.errorCollection
        return
    }

    def newIssue1 = issueService.create(asUser, createValidationResult1).issue
    def newIssue2 = issueService.create(asUser, createValidationResult2).issue
    subTaskManager.createSubTaskIssueLink(parentIssue, newIssue1, asUser)
    subTaskManager.createSubTaskIssueLink(parentIssue, newIssue2, asUser)
}

def createTask(Issue parentIssue) {
    def asUser = ComponentAccessor.jiraAuthenticationContext.loggedInUser
    def constantsManager = ComponentAccessor.constantsManager
    def issueService = ComponentAccessor.issueService
    def currentissueLinkManager = ComponentAccessor.issueLinkManager

    def taskIssueType = constantsManager.allIssueTypeObjects.findByName("Task")

    // Fill the required fields for the task
    def issueInputParameters = new IssueInputParametersImpl()
    issueInputParameters
        .setProjectId(parentIssue.projectId)
        .setIssueTypeId(taskIssueType.id)
        .setSummary("${parentIssue.summary}: Cost in hours")
        .setReporterId(asUser.username)

    // Create the task
    def createValidationResult = issueService.validateSubTaskCreate(asUser, parentIssue.id, issueInputParameters)
    if (!createValidationResult.isValid()) {
        log.error createValidationResult.errorCollection
        return
    }
    def newIssue = issueService.create(asUser, createValidationResult).issue

    // Link the task to the parent issue
}