# json
> super simple &amp; lightweight C json parsing without dependencies

Been struggling all day with terrible json parsing libs because C doesn't have classes (whoa thanks bjarne), so imma do it myself.

### Here's how it works:

```C
typedef enum e_json_type
{
	JSON_TYPE_NULL,
	JSON_TYPE_STRING,
	JSON_TYPE_NUMBER,
	JSON_TYPE_OBJECT,
	JSON_TYPE_ARRAY
} json_type;

typedef struct s_json_node
{
	char *key;
	json_type type;

	union
	{
		char *string;
		double number;
		struct s_json_node **array;
	};
}	json_node;
```
It's essentially just a more complicated linked list. When elements in an array are just single values, their key is NULL. Objects and arrays both save their data in array, even if theres just a single child node. Array is always null-terminated.

Here's the main functions:
```C
json_node *string_to_json(char *string);
char *json_to_string(json_node *json);
char *json_to_formatted_string(json_node *json);
json_node *json_find(json_node *json, char *key);
json_node *json_find_recursive(json_node *json, char *key)
void free_json(json_node *json);
```
Should be pretty self-explanatory. Good luck!


```C
char *json_str = "{\"greeting\": \"Hello, world!\", \"numbers\": [1, 2, 3]}";

json_node *root = string_to_json(json_str);
char *formatted = json_to_formatted_string(root);
printf("Formatted:\n%s\n", formatted);

json_node *numbers = json_find(root, "numbers");
printf("numbers: %s\n", json_to_string(numbers));

free(formatted);
free_json(root);
```

```
Formatted:
{
  "greeting": "Hello, world!",
  "numbers": [
    1,
    2,
    3
  ]
}
numbers: [1,2,3]
```
