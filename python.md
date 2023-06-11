# Python
Useful as a general purpose language for automation, working with data.

## Learning Resources
- 100 Days of Code: The Complete Python Pro Bootcamp (Udemy) -- on day 36
- PCEP and PCAP certs optional

## Major Concepts
- Functions (*args, **kwargs)
- Scope and Global variables (avoid if possible, but if not ensure it is reachable)
- Exceptions - try, except, else, finally. Use if "exceptional" case...if can protect in other ways (e.g. if/else) then do that
- JSON - dump (write), load (read)

### Typing
- Dynamic typing - can change type of variable (e.g. int to string) which is unique
- Type hints

```
def greeting(name:str) -> str:
  return 'Hello' + name
```

### OOP
Classes, modules, init and super_init

### List/Dictionary
- Looping
```
for item in list:
  print(item)
```
- List comprehension 
```
new_list = [new_item for item in list if test]
```
- Dictionary comprehension
```
# iterating on list
new_dict = {new_key:new_value for item in list}

# iterating on dictionary
new_dict = {key:value for (key,value) in dict.items() if test}
```
### File I/O
- Pandas -- read_csv()
- "with open" handles file close

### String Manipulation
- replace() --> need to store to new variable to see result
- split() --> break into list by a separator
- f strings for formatting text and variables

### User Interface
- tkinter can build GUI
- use pack() or grid() to layout
- use window.main_loop() to keep it running, use window.after() to perform action after delay

### Requests
- ⭐ response.raise_for_status() --> raises HTTP exception for you if unsuccessful status code
- response.json() --> get payload in JSON format
- GET request - pass arguments in as a dict
```
response = requests.get(URL, params=param_dict)
```
- for authentication, use secrets in header (i.e. pass in headers dict)

### Recurring Tasks
cronjob
PythonAnywhere schedule
time.sleep()
lambda function

### Secrets
os.environ.get() --> environment variables
