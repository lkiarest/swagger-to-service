# swagger-to-service

Convert API definitions to source code files of api/service/ut. Support user defined plugins via Tapable.

### install
```shell
yarn add swagger-to-service --dev
```

### usage
```javascript
const sts = require('swagger-to-service')

sts({
    url: 'http://yaml/server/test.yaml', // yaml uri
    apiPath: './out/api.js', // dist dir of api file
    servicePath: './out/service.js', // dist dir of service file
    utPath: './out/service.ut.js' // dist dir of service ut file,
    plugins: [{
        apply (config) {
            config.plugin('before-parse', (data, config) => {
                console.log('processing:', data.info.title)
            })
        }
    }]
})
```

### plugins

View [test code](https://github.com/lkiarest/swagger-to-service/blob/master/test/DemoPlugin.js) for all plugins usage.

__Supported Events:__

1. __before-parse__

    process raw json data from yaml

    __params__: data, config
1. __after-parse__

    process render json data

    __params__: data, config
1. __before-params__

    process params of eath api

    __params__: params, path
1. __after-params__

    after processing params

    __params__: params, path
1. __after-api__

    after processing each api

    __params__: apiInfo, path
1. __api-template__

    after processing params

    __params__: template, data
1. __service-template__

    after processing params

    __params__: template, data
1. __test-template__

    after processing params

    __params__: template, data

### code generated
api.js
```javascript
export default {
    menu_list: _dataPath('/menu/list'), // 获取信息管理中心菜单列表
    user_login_role_view: _dataPath('/user/login/role/view'), // 获取用户角色信息
    user_list: _dataPath('/user/list'), // 根据搜索条件和分页信息获取用户列表
}
```

service.js
```javascript
/**
 * 获取信息管理中心菜单列表
 * @param { Array } params 请求参数
 */
export const listMenus = () => {
    return http.post(api.menu_list)
}

/**
 * 获取用户角色信息
 * @param { Array } params 请求参数
 */
export const viewLoginUserRole = () => {
    return http.post(api.user_login_role_view)
}

/**
 * 根据搜索条件和分页信息获取用户列表
 * @param { Object } params 请求参数
 * @param { String } params['departmentId'] 单位Id
 * @param { String } params['searchCondition'] 搜索条件
 * @param { Integer } params['pageNumber'] 分页页码
 * @param { Integer } params['pageSize'] 分页大小
 */
export const listUsers = (params) => {
    return http.post(api.user_list, params)
}
```

service.ut.js
```
describe('获取信息管理中心菜单列表', () => {
    it('normal request', () => {
        expect.assertions(1)
        return service.listMenus().then(data => {
            expect(data.code).toBe(0)
        })
    })
})

describe('获取用户角色信息', () => {
    it('normal request', () => {
        expect.assertions(1)
        return service.viewLoginUserRole().then(data => {
            expect(data.code).toBe(0)
        })
    })
})
```