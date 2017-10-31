# ServiceNow REST API
[![MIT](https://img.shields.io/badge/license-MIT-green.svg)](https://opensource.org/licenses/MIT)

Node.JS wrapper library for ServiceNow REST API.

## Installing

Run `npm install servicenow-rest-api` to install the package.

## Basic Usage

#### Request

1. Get reference to servicenow package.
2. Initialize servicenow object.
3. Call authenticate function.
4. You can now access Servicenow data by calling functions.

```
const sn=require('servicenow-rest-api');

const ServiceNow=new sn('_INSTANCE','_USERID','_PASSWORD');

ServiceNow.Authenticate();

ServiceNow.getSampleData('change_request',(res)=>{    // 
    console.log(res);
});
```

#### Response

```
[ { parent: '',
    reason: '',
    made_sla: 'false',
    backout_plan: 'Current prod environment to be snapshotted with VmWare\n            prior to change.\n        ',
    watch_list: '',
    upon_reject: '',
    sys_updated_on: '2017-08-10 12:42:23',
    type: 'normal',
    conflict_status: '',
    approval_history: '',
    number: 'CHG0000001',
    test_plan: 'Multi-User testing on Sunday night',
    cab_delegate: '',
    sys_updated_by: 'admin',
    .
    .
    .
```

## Supported REST API calls

In this package, wrappers are available for below REST interfaces.

| API       | GET                                | POST                     | PUT                               | DELETE                            |
|-----------|------------------------------------|--------------------------|-----------------------------------|-----------------------------------|
| Table API | /now/v2/table/{tableName}          | now/v2/table/{tableName} | now/v2/table/{tableName}/{sys_id} | now/v2/table/{tableName}/{sys_id} |
|           | /now/v2/table/{tableName}/{sys_id} |                          |                                   |                                   |

## Functions

### ServiceNow.Authenticate

| Parameters                   |
|------------------------------|
| Callback function (optional) |

#### Request

```
ServiceNow.Authenticate(res=>{
    console.log(res.status);
});
```
#### Response

res.status

```
200
```

For complete response use res.raw

```
{ status: 200,
  statusText: 'OK',
  headers:
   { 'set-cookie':
      [_cookieinfo],
     'x-is-logged-in': 'true',
     'x-total-count': '1',
     pragma: 'no-store,no-cache',
     'cache-control': 'no-cache,no-store,must-revalidate,max-age=-1',
     expires: '0',
     'content-type': 'application/json;charset=UTF-8',
     'transfer-encoding': 'chunked',
     .
     .
     .
```
___

### ServiceNow.getSampleData

This function can be used as a reference to get sample data for tables in ServiceNow. This can be used to check the fields in a table.

| Parameters        | Description                                                    |
|-------------------|----------------------------------------------------------------|
| type              | Table type in ServiceNow (incident,change_request)             |
| Callback function | Response will be available as a parameter to callback function |

#### Request

```
ServiceNow.getSampleData('change_request',(res)=>{
    console.log(res);
});
```

#### Response

```
[ { parent: '',
    made_sla: 'false',
    caused_by: '',
    watch_list: '',
    upon_reject: '',
    sys_updated_on: '2017-08-10 20:16:07',
    child_incidents: '',
    hold_reason: '',
    approval_history: '',
    number: 'INC0000001',
```

___


### ServiceNow.getTableData

Access ServiceNow table data using this function.

| Parameters        | Description                                                                                          |
|-------------------|------------------------------------------------------------------------------------------------------|
| fields            | Fields that you want from ServiceNow table. Pass an empty array if you want to get all the fields    |
| filters           | Filters that has to be applied on ServiceNow table. Pass an empty array if filters are not required. |
| type              | type of table - incident, change_request                                                             |
| callback function | Response will be available as a parameter to this function                                           |                                           |

#### Request

```
const fields=[
    'number',
    'short_description',
    'assignment_group',
    'priority'
];

const filters=[
    'urgency=1'
];

ServiceNow.getTableData(fields,filters,'incident',function(res){
    console.log(res);
});
```

#### Response

```
https://devserver.service-now.com/api/now/v2/table/incident?sysparm_display_value=true&sysparm_fields=number,short_description,assignment_group,p
riority&sysparm_query=urgency=1
[ { number: 'INC0000025',
    short_description: 'Need to add more memory to laptop',
    assignment_group: '',
    priority: '1 - Critical' },
  { number: 'INC0000016',
    short_description: 'Rain is leaking on main DNS Server',
    assignment_group:
     { display_value: 'Hardware',
       link: 'https://dev16219.service-now.com/api/now/v2/table/sys_user_group/8a5055c9c61122780043563ef53438e3' },
    priority: '1 - Critical' },
    .
    .
    .
```

___


### ServiceNow.getSysId

Used as a reference to get sys_id of a record in ServiceNow.

| Parameters        | Description                                                |
|-------------------|------------------------------------------------------------|
| type              | type of table - incident, change_request                   |
| number            | Ticket number in ServiceNow                                |
| callback function | Response will be available as a parameter to this function |

#### Request

```
ServiceNow.getSysId('incident','INC0000016',res=>{
    console.log(res);
});
```

#### Response

```
46e3e949a9fe19810069b824ba2c761a
```

___


### ServiceNow.createNewTask

This is used to create a new task in ServiceNow.

| Parameters        | Description                                                |
|-------------------|------------------------------------------------------------|
| data              | Object that contains data for your record                  |
| type              | type of table - incident, change_request                   |
| callback function | Response will be available as a parameter to this function |

#### Request

```
const data={
    'short_description':'Need urgent attention!!',
    'urgency':'1',
    'priority':'1',
    'assignment_group':'Hardware'
};

ServiceNow.createNewTask(data,'incident',res=>{
    console.log(res);
});
```

#### Response

```
{ parent: '',
  made_sla: 'true',
  caused_by: '',
  watch_list: '',
  upon_reject: 'Cancel all future Tasks',
  sys_updated_on: '2017-10-30 22:24:30',
  child_incidents: '0',
  hold_reason: '',
  approval_history: '',
  number: 'INC0010006',
  resolved_by: '',
  sys_updated_by: 'admin',
  .
  .
  .
```

___


### ServiceNow.UpdateTask

This is used to update existing record in ServiceNow.

| Parameters        | Description                                                |
|-------------------|------------------------------------------------------------|
| type              | type of table - incident, change_request                   |
| number            | Ticket number in ServiceNow                                |
| data              | Object that contains data for your record                  |
| callback function | Response will be available as a parameter to this function |

#### Request

```
const data={
    'work_notes':'Assigning this to different team',
    'assignment_group':'Network'
};

ServiceNow.UpdateTask('incident','INC0010006',data,res=>{
    console.log(res);
});
```

#### Response

```
{ parent: '',
  made_sla: 'true',
  caused_by: '',
  watch_list: '',
  upon_reject: 'Cancel all future Tasks',
  sys_updated_on: '2017-10-30 22:31:49',
  child_incidents: '0',
  hold_reason: '',
  approval_history: '',
  number: 'INC0010006',
  .
  .
  .
  work_notes: '2017-10-30 22:31:49 - System Administrator (Work notes)\nAssigning this to different team\n\n',
  short_description: 'Need urgent attention!!',
  close_code: null,
  correlation_display: '',
  delivery_task: '',
  work_start: '',
  assignment_group:
   { display_value: 'Network',
   .
   .
   .
   
```

___


### ServiceNow.DeleteTask

To delete an existing record in ServiceNow table.

| Parameters        | Description                                                |
|-------------------|------------------------------------------------------------|
| type              | type of table - incident, change_request                   |
| number            | Ticket number in ServiceNow                                |
| callback function | Response will be available as a parameter to this function |

#### Request

```
ServiceNow.DeleteTask('incident','INC0010006',res=>{
    console.log(res.status);
});
```

#### Response

```
204
```


## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE.md) file for details
