# shadow-device-sdk


## TODO
- 认证机制
- 重连机制

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

struct func_list {
    func_call *call;
}

enum {reported, desired} state;
enum {syn, asyn} way_of_io;
enum {.............} error_code;
enum {.............} func_enum;

```

- private
```c
struct property {
    int         timestamp;
    attr_int    *attr_i;
    attr_str    *attr_s;
    void        *attr_a;
};

struct server_response {
    char        *method;
    int         version;
    int         timestamp;
    property    *reported;
    property    *desired;
};

struct server_request {
    char       *method;
    int        version;
    int        timestamp;
    property   *reported;
    property   *desired;
};

struct emqx {
    char    *id;
    char    *dev;
    // other
};
```
## API 

- public:
```c
// TODO
// emqx *emqx_regester(dev_info *info);

@ brief Create a new emqx client instance.  
@ param id : client id, if it is NULL, random id will be generated.
@ ret : emqx client instance.
emqx *emqx_new(char *id);

@ brief Use to free memory associated with a emqx client instance.  
@ param emq : emqx client instance.
@ ret : 
void emqx_destory(emqx *emq);

@ brief Connect to an mqtt broker.  
@ param emq : an emqx client instance. 
@ param host : hostname or ip address of the broker to connect to.
@ param port : network port to connect to.  Usually 1883.
@ ret : error code.
error_code emqx_connect(emqx *emq, char *host, int port);

@ brief Reconnect to a broker.  
@ param emq : an emqx client instance. 
@ ret : error_code.
error_code emqx_reconnect(emqx * emq);

@ brief Disconnect from a broker.  
@ param emq : an emqx client instance. 
@ ret : error_code.
error_code emqx_disconnect(emqx *emq);

@ brief Update information to shadow.  
@ param emq : an emqx client instance.
@ ret : server response.
server_response *emqx_get(emqx);

@ brief Update information to shadow.  
@ param emq : an emqx client instance.
@ param request : server request.
@ ret : error code.
error_code emqx_update(emqx *emq, server_request *request);

@ brief Receive and process data from shadow.  
@ param emq : an emqx client instance.
@ param obj : function list.
@ ret : error code.
error_code emqx_control(emqx *emq, func_list *obj);

@ brief Delete shadow data.  
@ param emq : an emqx client instance.
@ param request : server request.
@ ret : error code.
error_code emqx_delete(emqx *emq, server_request *request);


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
attr_int **get_param_int_arr(server_response *response, state s);

@ brief Get all string type data
@ param response : server response
@ param p : desired or reported
@ ret : attribute string array
attr_str *get_param_str_arr(server_response *responsem, state s);

@ brief Get all array type data
@ param response : server response
@ param p : desired or reported
@ ret : attribute array array
attr_arr *get_param_arr(server_response *response, state s);


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
int add_param_int(server_request *request, char *key, int val, state s);

@ brief Add string type to server request
@ param response : server request
@ param key : key
@ param val :value
@ param p : desired or reported
@ ret : 
int add_param_str(server_request *request, char *key, char *str, state s);

@ brief Add array type to server request
@ param response : server request
@ param key : key
@ param val :value
@ param p : desired or reported
@ ret : 
int add_param_arr(server_request *request, char *key, void *arr, state s);

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
error_code send_and_recv(server_request *request, char *send_topic, char *recv_topic);

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
