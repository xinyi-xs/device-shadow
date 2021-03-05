# shadow-device-sdk

## Data structure

- public
```c
struct attr_int {
    char        *key
    int         val;
};

struct attr_str {
    char        *key
    char        *val;
};

struct attr_arr {
    char        *key;
    void        *val;
};

enum {reported, desired} property;
enum {syn, asyn} way_of_io;
enum {.............} error_code;

```

- private
```c
struct property_desired {
    int         timestamp;
    attr_int    *attr_i;
    attr_str    *attr_s;
    void        *attr_a;
};

struct property_reported {
    int         timestamp;
    attr_int    *attr_i;
    attr_str    *attr_s;
    void        *attr_a;
};

struct server_response {
    char                *method;
    int                 version;
    int                 timestamp;
    property_reported   *reported;
    property_desired    *desired;
};

struct server_request {
    char                *method;
    int                 version;
    int                 timestamp;
    property_reported   *reported;
    property_desired    *desired;

};
```
## API 

- public:
```c
// TODO 
int register(device_info *info);

@ brief Receive topic payload and parse to struct  
@ param topic : subscibe topic 
@ param way_of_io : syn or asyn
@ ret : struct server response
server_response *recv(char *topic, way_of_io io);

@ brief Receive topic payload and return
@ param topic : subscibe topic 
@ param way_of_io : syn or asyn
@ ret : raw payload
char *recv_raw(char *topic, way_of_io io);

@ brief Get method from server response
@ param response : server response
@ ret : method
char *get_param_method(server_response *response);

@ brief Get all int type data
@ param response : server response
@ param p : desired or reported
@ ret : attribute int array
attr_int **get_param_int_arr(server_response *response, property p);

@ brief Get all string type data
@ param response : server response
@ param p : desired or reported
@ ret : attribute string array
attr_str *get_param_str_arr(server_response *responsem, property p);

@ brief Get all array type data
@ param response : server response
@ param p : desired or reported
@ ret : attribute array array
attr_arr *get_param_arr(server_response *response, property p);


@ brief Builder a server request structure
@ param request : request
@ ret : 
server_re *request = builder(server_request *request);

@ brief Add method to server request
@ param method : method
@ ret : 
int add_param_method(char *method);

@ brief Add int type to server request
@ param response : server request
@ param key : key
@ param val :value
@ param p : desired or reported
@ ret : 
int add_param_int(server_request *request, char *key, int val, property p);

@ brief Add string type to server request
@ param response : server request
@ param key : key
@ param val :value
@ param p : desired or reported
@ ret : 
int add_param_str(server_request *request, char *key, char *str, property p);

@ brief Add array type to server request
@ param response : server request
@ param key : key
@ param val :value
@ param p : desired or reported
@ ret : 
int add_param_arr(server_request *request, char *key, void *arr, property p);

@ brief Send request infomation to topic
@ param request: request infomation
@ param topic : publish topic 
@ ret : 
int send(server_request *request, char *topic);

@ brief One times request and response 
@ param request: request infomation
@ param send_topic : publish topic 
@ param recv_topic : subsrcibe topic 
@ ret : errorcode
error_code send_and_recv(char *send_topic, char *recv_topic);

@ brief Make error code to string 
@ param error_code : error code 
@ ret : error information
char *error_code_to_str(error_code error);

```
- private:
```c
server_response *json_parser(json);
json *json_builder(server_request *request);
```
