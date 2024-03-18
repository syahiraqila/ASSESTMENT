
# Listener
//Create a Listener that updates the issue description with the issue's last

import com.atlassian.jira.component.ComponentAccessor

//Define the current Issue and the desired Custom Field Name
def issue = event.issue
def FieldName = '<FIELD_NAME>'

//Define Change History Manager and retrieve the last updated item for this issue
def changeHistoryManager = ComponentAccessor.changeHistoryManager
def lastChangeItem = changeHistoryManager.getAllChangeItems(issue).last() as String

if (lastChangeItem.contains(FieldName)) {
    def newField = lastChangeItem.toString() // convert last change item to string
        
    if (newField == '<DESIRED_VALUE>') {
    //Execute further code, for example, send a log message
    log.warn("The Field $FieldName has a new value of $newField")
    }
}