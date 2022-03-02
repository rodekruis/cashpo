# Sending KoBo submissions to EspoCRM

**Under Construction**

## Prepare KoBo form


## Prepare MS PowerAutomate

### Special case: select_multiple
*Maybe there is a better way, but this is how we make it work at the moment.*

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
3. Repeat for all select_multiple fields
4. Then use the variable created in step 2/3, to create your the body that you will send to espo. You don't have to put the variable between ```" "``` since it already is an array.

![image](https://user-images.githubusercontent.com/39266480/153206058-b9302ff0-fda5-4cba-8654-9212ea9e7b46.png)
