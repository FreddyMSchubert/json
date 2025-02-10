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
It's essentially just a more complicated linked list. When elements in `array` are just single values, their key is NULL. Objects and arrays both save their data in `array`, even if theres just a single child node. `array` is always null-terminated.

Here's the main functions:
```C
json_node	*string_to_json(char *string);
char		*json_to_string(json_node *json);
char		*json_to_formatted_string(json_node *json);
json_node	*json_find(json_node *json, char *key);
json_node	*json_find_recursive(json_node *json, char *key)
void		free_json(json_node *json);
json_node	*create_node(json_type type);
```
Should be pretty self-explanatory. Good luck!

## Basic Example

```C
char *json_str = "{\"greeting\": \"Hello, world!\", \"numbers\": [1, 2, 3]}";

json_node *root = string_to_json(json_str);
char *formatted = json_to_formatted_string(root);
printf("Formatted:\n%s\n", formatted);

json_node *numbers = json_find(root, "numbers");
char *nbrstring = json_to_string(numbers);
printf("numbers: %s\n", nbrstring);

free(formatted);
free(nbrstring);
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

## JSON Construction Example

```C
json_node * root = create_node(JSON_TYPE_OBJECT);
root->array = malloc(sizeof(json_node *) * 4);
root->array[3] = NULL;

json_node *answer = create_node(JSON_TYPE_NUMBER);
answer->key = strdup("answer");
answer->number = 42;
root->array[0] = answer;

json_node *id = create_node(JSON_TYPE_STRING);
id->key = strdup("id");
id->string = argv[1] ? strdup(argv[1]) : strdup("-1");
root->array[1] = id;

json_node *name = create_node(JSON_TYPE_STRING);
name->key = strdup("name");
name->string = team_name ? strdup(team_name) : strdup("default");
root->array[2] = name;

char * msg = json_to_string(root);
free_json(root);
```

Note that all keys & string values are dynamically allocated to guarantee smooth free_json() functionality.
