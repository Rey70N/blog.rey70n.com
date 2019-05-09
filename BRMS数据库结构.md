## Collections

#### project

描述：项目信息

| 字段名称    | 数据类型 | 描述                      |
| ----------- | -------- | ------------------------- |
| projectId   | String   | 项目的唯一标识            |
| projectName | String   | 项目名称 |
| groupId     | String   | GAV要素，以JAVA包格式定义 |
| artifact    | String   | GAV要素，项目**英文**名称 |
| version     | String   | GAV要素，项目版本号       |
| tenantId    | String   | 租户ID                    |
| createTime  | Date     | 创建时间                  |
| updateTime  | Date     | 更新时间                  |
| createBy    | String   | 创建者                    |

#### ruleObject

描述：业务对象

| 字段名称   | 数据类型 | 描述                 |
| ---------- | -------- | -------------------- |
| nameCN       | String   | 业务对象名称（中文） |
| name       | String   | 业务对象名称（英文） |
| objectJSON | Object   | JSON格式的业务对象   |
| projectId  | String   | 所属项目             |
| tenantId    | String   | 租户ID                    |
| createTime  | Date     | 创建时间                  |
| updateTime  | Date     | 更新时间                  |
| createBy    | String   | 创建者                    |

#### rule

描述：规则信息

| 字段名称   | 数据类型 | 描述             |
| ---------- | -------- | ---------------- |
| nameCN       | String   | 规则名称（中文） |
| name       | String   | 规则名称（英文） |
| drl        | String   | 规则文件内容     |
| projectId  | String   | 所属项目         |
| tanantId   | String   | 租户ID           |
| createTime | Date     | 创建时间         |
| updateTIme | Date     | 更新时间         |
| createBy   | String   | 创建者           |

