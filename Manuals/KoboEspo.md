# Sending KoBo submissions to EspoCRM

**Under Construction**

## Prepare KoBo form

- [ ] Explain how and why to configure the baseURL in KoBo
- [ ] Explain that questionnames ideally match espofieldnames (include that preferrable you dont use underscores (_) in questionnames

## Prepare MS PowerAutomate

### Special case: select_multiple
*Maybe there is a better way, but this is how we make it work at the moment.*
*We will look into Logic Apps in the future and replace this section*

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


## Using attachements in KoBo (file/picture/video) -> Additional logic in EspoCRM
After you've configured your form and powerautomate (or logic app), the final step is to configure the logic in Espo. Everything should work, but if you are using attachements in your form, you need to configure some additional logic. Depending on what records you are creating, open up the formula section of that entity.

Here we will construct the logic to concatenate the url to the attachement making use of the filename and baseURL.

Repeat this line below for all of the fields that will have a url to the attachement. 
*(it could probably be done better/more elegantly with a while loop and a list of fieldnames you want to include, but for now this works)*
```
ifThen(urlUpdated != 'yes' && fieldname != null, fieldname = string\concatenate(baseURL,string\replace(fieldname, ' ', '_')));
```
Lets break this down:
- `urlUpdated != 'yes'` Only do this once, and do not concatenate the baseURL every time the record is changed
- `fieldname != null` Only add the baseURL when there was content in the original field
- `string\replace(fieldname, ' ', '_')` Replace spaces in the filename with underscores, this is what KoBo does as well to construct URLs.
- `fieldname = string\concatenate()` Concatenate the baseURL and filename to create the url.

And then at the end of your list, add:
```
urlUpdated = 'yes'
```