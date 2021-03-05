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

@ brief
@ param topic : subscibe topic 
@ param way_of_io : syn or asyn
@ return : struct server response
server_response *recv(char *topic, way_of_io io);

@ brief
@ param topic : subscibe topic 
@ param way_of_io : syn or asyn
@ return : raw payload
char *recv_raw(char *topic, way_of_io io);

@ brief
@ param response : server response
@ return : method
char *get_param_method(server_response *response);

@ brief
@ response : server response
@ p : desired or reported
@ return : attribute int array
attr_int *get_param_int_arr(server_response *response, property p);

@ brief
@ response : server response
@ p : desired or reported
@ return : attribute string array
attr_str *get_param_str_arr(server_response *responsem, property p);

@ brief
@ response : server response
@ p : desired or reported
@ return : attribute array array
attr_arr *get_param_arr(server_response *response, property p);


int add_param_method(char *method);
int add_param_int(server_request *request, char *key, int val, property p);
int add_param_str(server_request *request, char *key, char *str, property p);
int add_param_arr(server_request *request, char *key, void *arr, property p);
server_re *request = builder(server_request *request);


@ brief
@ param topic : publish topic 
@ param request: request infomation
@ return : 
int send(server_request *request, char *topic);

error_code send_and_recv(char *send_topic, char *recv_topic);
char *error_code_to_str(error_code error);

```
- private:
```c
server_response *json_parser(json);
json *json_builder(server_request *request);
```
