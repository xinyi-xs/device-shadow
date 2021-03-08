# shadow-device-sdk

## TODO
- 认证机制?
- 是否需要will/ retain 扩展？
- 自动重连（loop, on_disconnect), 机制

## Data structure

- public
```c
enum {INT, STR, ARR} TAG;

struct attr {
    TAG         tag;
    char        key;
    union {
        int     val_int;
        char    *val_str;
        attr    *val_arr;
    };
};

enum {reported, desired} state;

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

struct mqtt {
    char    *id;
    char    *dev_info;
    // other
};


struct option {
    bool        auto_reconnect;
    int         intervel_time;
    int         times;
    // TODO others
};
```
## API 

- public:
```c
// TODO
// mqtt *mqtt_regester(dev_info *info);

@ brief Create a new mqtt client instance.  
@ param id : client id, if it is NULL, random id will be generated.
@ ret : mqtt client instance.
mqtt *mqtt_new(char *id, option *opt);

@ brief Use to free memory associated with a mqtt client instance.  @ param handle : mqtt client instance.  @ ret : void mqtt_destory(mqtt *handle); ================================================== @ brief Connect to an mqtt broker.  @ param handle : an mqtt client instance.  @ param host : hostname or ip address of the broker to connect to.  @ param port : network port to connect to.  Usually 1883.
@ ret : error code.
error_code mqtt_connect(mqtt *handle, char *host, int port);

@ brief Reconnect to a broker.  
@ param handle : an mqtt client instance. 
@ ret : error_code.
error_code mqtt_reconnect(mqtt * handle);

@ brief Disconnect from a broker.  
@ param handle : an mqtt client instance. 
@ ret : error_code.
error_code mqtt_disconnect(mqtt *handle);
==================================================

@ brief Set control callback.  
@ param handle : an mqtt client instance.
@ param func : a callback function for receive data.
@ ret : error code.
error_code mqtt_set_control_callback(mqtt *handle, control_callback func);

@ brief Set get callback.  
@ param handle : an mqtt client instance.
@ param func : a callback function for receive data.
@ ret : error code.
error_code mqtt_set_get_callback(mqtt *handle, get_callback func);

@ brief Set update callback.  
@ param handle : an mqtt client instance.
@ param func : a callback function for update data.
@ ret : error code.
error_code mqtt_set_update_callback(mqtt *handle, update_callback func);

@ brief Set delete callback.  
@ param handle : an mqtt client instance.
@ param func : a callback function for dalete data.
@ ret : error code.
error_code mqtt_set_delete_callback(mqtt *handle, delete_callback func);
==================================================

@ brief Update information to shadow.  
@ param handle : an mqtt client instance.
@ ret : server response.
server_response *mqtt_get(mqtt);
error_code mqtt_asyn_get(mqtt *handle);

@ brief Update information to shadow.  
@ param handle : an mqtt client instance.
@ param request : server request.
@ ret : error code.
error_code mqtt_update(mqtt *handle, server_request *request);
error_code mqtt_asyn_update(mqtt *handle, server_request *resquest);

@ brief Delete shadow data.  
@ param handle : an mqtt client instance.
@ param request : server request.
@ ret : error code.
error_code mqtt_delete(mqtt *handle, server_request *request);
error_code mqtt_asyn_delete(mqtt *handle, server_request *request);

@ brief Receive and process data from shadow.  
@ param handle : an mqtt client instance.
@ param obj : function list.
@ ret : error code.
server_response *mqtt_control(mqtt *handle);
error_code mqtt_asyn_control(mqtt *handle);
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
@ param s : desired or reported
@ ret : 
int add_param_int(server_request *request, char *key, int val, state s);

@ brief Add string type to server request
@ param response : server request
@ param key : key
@ param val :value
@ param s : desired or reported
@ ret : 
int add_param_str(server_request *request, char *key, char *val, state s);

@ brief Add array type to server request
@ param response : server request
@ param key : key
@ param val :value
@ param s : desired or reported
@ ret : 
int add_param_arr(server_request *request, char *key, void *val, state s);

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
