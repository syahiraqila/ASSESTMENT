
# Custom Field
//A field which calculates a value based on two other issue field values.

def complexity = getCustomFieldValue("Complexity")
def priority = getCustomFieldValue("Priority")

if (complexity && priority) {
    def score_complex = 0
    def score_prio = 0
    switch(complexity) {
    case "High":
        // code block
        score_complex = 3
        break;
    case "Low":
        // code block
        score_complex = 1
        break;
    }
    switch(priority) {
    case "High":
        // code block
        score_prio = 3
        break;
    case "Low":
        // code block
        score_prio = 1
        break;
    }
    def total_score = score_complex * score_prio
    return total_score
} else {
    return null
}