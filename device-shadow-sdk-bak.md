# shadow-device-sdk

## TODO
- 认证机制?
- 是否需要will/ retain 扩展？

## Data structure

- public
```c
enum {INT, STR, ARR} TAG;

struct attr {
    TAG tag;
    char        key;
    union {
        int     val_int;
        char    *val_str;
        attr    *val_arr;
    };
};

// struct attr_int {
//     char        *key
//     int         val;
// };
// 
// struct attr_str {
//     char        *key
//     char        *val;
// };
// 
// struct attr_arr {
//     char        *key;
//     void        *val;
// };

enum {reported, desired} state;
enum {syn, asyn} way_of_io;

```

- private
```c
enum {.............} error_code;

struct property {
    int         timestamp;
    attr        *attr_vec;
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
==================================================

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
==================================================

@ brief Set control callback.  
@ param emq : an emqx client instance.
@ param func : a callback function for receive data.
@ ret : error code.
error_code emqx_set_control_callback(emqx *emq, control_callback func);

@ brief Set get callback.  
@ param emq : an emqx client instance.
@ param func : a callback function for receive data.
@ ret : error code.
error_code emqx_set_get_callback(emqx *emq, get_callback func);

@ brief Set update callback.  
@ param emq : an emqx client instance.
@ param func : a callback function for update data.
@ ret : error code.
error_code emqx_set_update_callback(emqx *emq, update_callback func);

@ brief Set delete callback.  
@ param emq : an emqx client instance.
@ param func : a callback function for dalete data.
@ ret : error code.
error_code emqx_set_delete_callback(emqx *emq, delete_callback func);
==================================================

@ brief Update information to shadow.  
@ param emq : an emqx client instance.
@ ret : server response.
server_response *emqx_get(emqx);
error_code emqx_asyn_get(emqx *emq);

@ brief Update information to shadow.  
@ param emq : an emqx client instance.
@ param request : server request.
@ ret : error code.
error_code emqx_update(emqx *emq, server_request *request);
error_code emqx_asyn_update(emqx *emq, server_request *resquest);

@ brief Delete shadow data.  
@ param emq : an emqx client instance.
@ param request : server request.
@ ret : error code.
error_code emqx_delete(emqx *emq, server_request *request);
error_code emqx_asyn_delete(emqx *emq, server_request *request);

@ brief Receive and process data from shadow.  
@ param emq : an emqx client instance.
@ param obj : function list.
@ ret : error code.
server_response *emqx_control(emqx *emq);
error_code emqx_asyn_control(emqx *emq);
==================================================

@ brief Get method from server response
@ param response : server response
@ ret : method
char *get_param_method(server_response *response);

@ brief Get type and structure with key && value
@ param response : server response
@ param s : desired or reported
@ ret : attribute int array
void *get_param_attr(server_response *response, state s, TAG *type);

@ brief Get all type and structure with key && value
@ param response : server response
@ param s : desired or reported
@ ret : attribute int array
void **get_all_param_attr(server_response *response, state s, TAG *type[]);

// @ brief Get all int type data
// @ param response : server response
// @ param p : desired or reported
// @ ret : attribute int array
// attr_int **get_param_int_arr(server_response *response, state s);
// 
// @ brief Get all string type data
// @ param response : server response
// @ param p : desired or reported
// @ ret : attribute string array
// attr_str *get_param_str_arr(server_response *response, state s);
// 
// @ brief Get all array type data
// @ param response : server response
// @ param p : desired or reported
// @ ret : attribute array array
// attr_arr *get_param_arr(server_response *response, state s);

@ brief Builder a server request structure
@ param request : request
@ ret : 
server_re *request = builder(server_request *request);


// @ brief Add type and structure with key && value to request.
// @ param request : server request.
// @ param s : desired or reported.
// @ param type : object type we add.
// @ ret : 
// int add_all_param_attr(server_request *request, state s, char *key, int val, TAG type);
// 
// @ brief Add all type and structure with key && value to request.
// @ param request : server request.
// @ param key : key
// @ param val :value
// @ param type : object type we get.
// @ ret : 
// int add_all_param_attr(server_request *request, state s, char *key, int val, TAG type[]);

@ brief Add method to server request
@ param method : method
@ ret : 
int add_param_method(char *method);

@ brief Add int type to server request
@ param response : server request
@ param key : key
@ param val :value
@ param s : desired or reported
@ ret : 
int add_param_int(server_request *request, char *key, int val, state s);

@ brief Add string type to server request
@ param response : server request
@ param key : key
@ param val :value
@ param s : desired or reported
@ ret : 
int add_param_str(server_request *request, char *key, char *str, state s);

@ brief Add array type to server request
@ param response : server request
@ param key : key
@ param val :value
@ param s : desired or reported
@ ret : 
int add_param_arr(server_request *request, char *key, void *arr, state s);

@ brief Make error code to string 
@ param error_code : error code 
@ ret : error information
char *error_code_to_str(error_code error);
==================================================

```

- private:
```c
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

server_response *json_parser(json);
json *json_builder(server_request *request);
```
