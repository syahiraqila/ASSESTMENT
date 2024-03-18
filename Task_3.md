
# Behaviour
//Create a Behaviour which prefixes an issue's summary field to display specific label

import com.onresolve.scriptrunner.runner.util.UserMessageUtil

def priorityField = getFieldById("priority")
def summaryField = getFieldById("summary")
def prioritySummary = priorityField.getValue() //take the value from priority

// Function to check if the screen is the "Create" screen
def isCreateScreen() {
    def screenName = getActionName()
    return screenName == "Create"
}

// Check if the screen is the "Create" screen
if (isCreateScreen()) {
    if (priorityField in ["High","Highest"]) {
        // Added "URGENT: " prefix from summary if priority is "High" or "Highest"
        summaryField.setFormValue("URGENT: " + summaryField.getFormValue())
    }else {
        // Remove "URGENT: " prefix from summary if priority is "Medium" or lower
        summaryField.setFormValue(summaryField.getFormValue())
        log.debug("Value after replacement: ${summaryField.getFormValue()}")
    }
} else {
    // Display a message flagging that this behavior does not work on the Edit screen
    UserMessageUtil.error("This behavior is only applicable on the Create screen.")
}