# Sending KoBo submissions to EspoCRM

**Under Construction**

## Prepare KoBo form


## Prepare MS PowerAutomate

### Special case: select_multiple
If you use select_multiple question type in KoBo, the payload of that field will be a string formatted like:

```
"QuestionName":"Option1 Option2 Option4"
```

EspoCRM expects to receive an array for a multi-enum field, so you need to convert the string into an arry in MS PowerAutomate. You can do this as follows:
1. Split up the string by using a "Compose" action. Then use the split expression to split with space as the delimeter. e.g. ```split(triggerBody()?['Data_Collection_Tools'],' ')```
2. Use the output to form an array, by using the "Initialise variable" action.
	* Give it a name
	* Select "Array" as the type
	* Use the output from the previous compose as Value
3. Then use the variable created in step 2, to create your the body that you will send to espo.