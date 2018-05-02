### 主要改动点

| 级别      | 改动点                 | 改造                                                                               | 描述                                                                                        |
|:---------|:----------------------|:----------------------------------------------------------------------------------|:-------------------------------------------------------------------------------------------|
| template | 索引匹配表达式          | template --> index_patterns                                                       | 原匹配定义字段由 template 变更为 index_patterns                                                |
| template | _all 字段去除          | _all定义干掉                                                                       | es6开始禁用 _all, 要使用全文检索功能，可以使用 copy_to                                           |
| template | boolean_similarity    | 去除tempalte中的定义，并且将field中的引用用，调整为 Boolean                            | es2.3中，相关性打分的bug，自助实现了该功能，在5.0之后已经修复                                      |
| template | string 类型            | 普通 string -> text                                                                | text默认分词，支持全文检索                                                                     |
| template | string & not_analyzed | type直接调整为：keyword, index的定义去掉                                             | es2中，index=not_analyzed 代表改字段是否需要被分词，5.0后，keyword代表字段可以被检索，但是不会被分词 |
| template | type命名强制为 _doc    | type命名强制为 _doc                                                                | es6之后，默认type为 _doc, 若不叫这个名字，后续es升级，type仍旧需要改造                             |
| index    | 一个索引只能有一个type   | （type转索引）house-resource-v5/demand-want --> house-resource_demand-want_v5/_doc | es6开始，仅支持一个type，且type概念将在es7中彻底移除                                             |
|          |                       |                                                                                   |                                                                                            |
| java-api | 依赖升级               | org.elasticsearch.elasticsearch -> org.elasticsearch.client.transport             |                                                                                            |
| java-api | minimumShouldMatch    | minimumShouldMatch 只能与 should 配合使用                                           | minimumShouldMatch同无should, 将查询不到数据，且后续版本中，可能报错。                            |
| java-api | date range 查询        | 日期必须格式化成: yyyy-MM-dd'T'hh:mm:ss.SSSZ                                        | 若直接传 Date 类型，程序搜索时，将报错                                                          |


### reindex 数据迁移
开发过程中，最好将现有测试或者开发环境中数据，导出一份到新的es集群，自测下，数据导出示例如下：
```shell
curl -X POST http://slave1.test.apitops.com:9200/_reindex -H Content-Type: application/json -d {
  "dest": {
    "index": "topsale_building-process-deal_dev",
    "type": "_doc"
  },
  "source": {
    "remote": {
      "host": "http://logservice1.test.apitops.com:9200"
    },
    "size": 100,
    "type": "building-process-deal",
    "index": "topsale-dev"
  }
}
```

### 配置示例
topsale_building-template.json

```json
{
  "index_patterns": [
    "topsale_building_*"
  ],
  "settings": {
    "index": {
      "max_result_window": "500000",
      "refresh_interval": "5s",
      "analysis": {
        "filter": {
          "lowercaseFilter": {
            "type": "lowercase"
          },
          "uppercaseFilter": {
            "type": "uppercase"
          }
        },
        "analyzer": {
          "title_ngram_analyzer": {
            "filter": [
              "lowercaseFilter",
              "uppercaseFilter"
            ],
            "tokenizer": "title_ngram_tokenizer"
          }
        },
        "tokenizer": {
          "title_ngram_tokenizer": {
            "token_chars": [
              "letter",
              "digit"
            ],
            "min_gram": "1",
            "type": "nGram",
            "max_gram": "1"
          }
        }
      }
    }
  },
  "mappings": {
    "_default_": {
      "_source": {
        "enabled": true
      }
    },
    "_doc": {
      "dynamic": "strict",
      "properties": {
        "@timestamp": {
          "type": "date"
        },
        "building_add_admin_id": {
          "type": "long"
        },
        "building_address": {
          "type": "text"
        },
        "building_avg_price_unit": {
          "type": "short"
        },
        "building_begin_time": {
          "type": "date"
        },
        "building_belong_type": {
          "type": "short"
        },
        "building_building_city_id": {
          "type": "long"
        },
        "building_building_city_name": {
          "type": "text"
        },
        "building_building_code": {
          "type": "text"
        },
        "building_building_id": {
          "type": "long"
        },
        "building_building_selling_point_name": {
          "type": "text"
        },
        "building_building_status": {
          "type": "keyword"
        },
        "building_building_style": {
          "type": "text"
        },
        "building_building_type": {
          "type": "short"
        },
        "building_crawl_url": {
          "type": "text"
        },
        "building_create_time": {
          "type": "date"
        },
        "building_delete_time": {
          "type": "date"
        },
        "building_end_time": {
          "type": "date"
        },
        "building_extend_add_admin_id": {
          "type": "long"
        },
        "building_extend_commission_type": {
          "type": "integer"
        },
        "building_extend_contract_effective_time": {
          "type": "date"
        },
        "building_extend_contract_status": {
          "type": "integer"
        },
        "building_extend_create_time": {
          "type": "date"
        },
        "building_extend_deal_description": {
          "type": "text"
        },
        "building_extend_developer_id": {
          "type": "long"
        },
        "building_extend_education": {
          "type": "text"
        },
        "building_extend_environment": {
          "type": "text"
        },
        "building_extend_image_3d_url": {
          "type": "text"
        },
        "building_extend_in_province_max": {
          "type": "long"
        },
        "building_extend_in_province_min": {
          "type": "long"
        },
        "building_extend_is_deal_reward": {
          "type": "integer"
        },
        "building_extend_is_diff_provinces": {
          "type": "integer"
        },
        "building_extend_is_look_reward": {
          "type": "integer"
        },
        "building_extend_is_need_ued": {
          "type": "integer"
        },
        "building_extend_is_voucher_reward": {
          "type": "integer"
        },
        "building_extend_location": {
          "type": "text"
        },
        "building_extend_look_description": {
          "type": "text"
        },
        "building_extend_main_house_type": {
          "type": "text"
        },
        "building_extend_out_province_max": {
          "type": "long"
        },
        "building_extend_out_province_min": {
          "type": "long"
        },
        "building_extend_plate": {
          "type": "text"
        },
        "building_extend_push_customer_description": {
          "type": "text"
        },
        "building_extend_resident_id": {
          "type": "long"
        },
        "building_extend_salesperson_id": {
          "type": "long"
        },
        "building_extend_supporting": {
          "type": "text"
        },
        "building_extend_traffic": {
          "type": "text"
        },
        "building_extend_ued_docker_id": {
          "type": "long"
        },
        "building_extend_update_admin_id": {
          "type": "long"
        },
        "building_extend_update_time": {
          "type": "date"
        },
        "building_fax": {
          "type": "text"
        },
        "building_first_abc": {
          "type": "text"
        },
        "building_head_photo": {
          "type": "keyword"
        },
        "building_is_channel_building": {
          "type": "short"
        },
        "building_is_deleted": {
          "type": "short"
        },
        "building_is_enable": {
          "type": "short"
        },
        "building_is_open": {
          "type": "short"
        },
        "building_link_man": {
          "type": "text"
        },
        "building_link_phone": {
          "type": "text"
        },
        "building_location": {
          "type": "geo_point"
        },
        "building_logo": {
          "type": "keyword"
        },
        "building_lowest_price_unit": {
          "type": "short"
        },
        "building_max_area": {
          "type": "float"
        },
        "building_max_price": {
          "type": "float"
        },
        "building_min_area": {
          "type": "float"
        },
        "building_min_price": {
          "type": "float"
        },
        "building_name": {
          "analyzer": "title_ngram_analyzer",
          "type": "text"
        },
        "building_open_time": {
          "type": "date"
        },
        "building_plate_id": {
          "type": "long"
        },
        "building_plate_name": {
          "type": "text"
        },
        "building_property_id": {
          "type": "long"
        },
        "building_property_name": {
          "type": "text"
        },
        "building_province_id": {
          "type": "long"
        },
        "building_province_name": {
          "type": "text"
        },
        "building_region_id": {
          "type": "long"
        },
        "building_region_name": {
          "type": "text"
        },
        "building_remark": {
          "type": "text"
        },
        "building_sell_content": {
          "type": "text"
        },
        "building_setting_add_admin_id": {
          "type": "long"
        },
        "building_setting_assign_time": {
          "type": "date"
        },
        "building_setting_come_day": {
          "type": "integer"
        },
        "building_setting_create_time": {
          "type": "date"
        },
        "building_setting_define_minute": {
          "type": "integer"
        },
        "building_setting_export_name": {
          "type": "text"
        },
        "building_setting_export_phone": {
          "type": "text"
        },
        "building_setting_follow_come_day": {
          "type": "integer"
        },
        "building_setting_follow_phone_day": {
          "type": "integer"
        },
        "building_setting_follow_pledged_day": {
          "type": "integer"
        },
        "building_setting_is_auto_assign": {
          "type": "integer"
        },
        "building_setting_is_come_look": {
          "type": "integer"
        },
        "building_setting_is_hide": {
          "type": "integer"
        },
        "building_setting_is_support_cyb" : {
          "type" : "integer"
        },
        "building_setting_limit_count": {
          "type": "integer"
        },
        "building_setting_own_claim_count": {
          "type": "integer"
        },
        "building_setting_phone_day": {
          "type": "integer"
        },
        "building_setting_pledged_day": {
          "type": "integer"
        },
        "building_setting_pre_protect_come_day": {
          "type": "integer"
        },
        "building_setting_pre_protect_phone_day": {
          "type": "integer"
        },
        "building_setting_protect_push_come_day": {
          "type": "integer"
        },
        "building_setting_protect_push_invalid_day": {
          "type": "integer"
        },
        "building_setting_push_end_time": {
          "type": "date"
        },
        "building_setting_push_start_time": {
          "type": "date"
        },
        "building_setting_update_admin_id": {
          "type": "long"
        },
        "building_setting_update_time": {
          "type": "date"
        },
        "building_show_order": {
          "type": "long"
        },
        "building_sys_time": {
          "type": "date"
        },
        "building_tags": {
          "similarity": "boolean",
          "type": "text"
        },
        "building_tenant_id": {
          "type": "long"
        },
        "building_tenant_type": {
          "type": "short"
        },
        "building_the_avg_price": {
          "similarity": "boolean",
          "type": "long"
        },
        "building_the_lowest_price": {
          "type": "long"
        },
        "building_update_admin_id": {
          "type": "long"
        },
        "building_update_time": {
          "type": "date"
        },
        "language_type": {
          "type": "integer"
        }
      }
    }
  },
  "aliases": {}
}
```


### 操作日志查询改造

这块代码默认spring实例化构造，暂时注释掉，等search-service改造完成后，再改造。

- dubbo-provider.xml
- spring-biz-context.xml

```xml
<!--
<dubbo:service interface="com.tops001.logs.service.OperationLogQueryService"
                   ref="esOperationLogService" version="1.0.0" timeout="6000" protocol="dubbo"/>
                   -->
                   
<!--
<bean class="com.tops001.logs.service.common.TransportClientProvider" init-method="init"></bean>
-->
```

### 改造进度

| 接口           | 方法               | 描述                                    | 进度  | 验证点                                   |
|:--------------|:------------------|:---------------------------------------|:------|:-----------------------------------------|
| DemandService | getDemandSellDTOs | 匹配卖房需求（目前只有需求id和经纪人id有效） | 未改造 | 线上、测试环境 demand，无 demand-sell type |
| DemandService | getDemandWantDTOs | 匹配求租需求（目前只有需求id和经纪人id有效） | 未改造 | 方法写死                                  |


### search-service改造

ES6+ 一个index仅支持一个 type, 一次对 2.3 中多 type
进行映射，目标type均为：_doc

映射规则为： sourceIndex_sourceType_envCode 如：

house-resource-v5/demand-want --> house-resource_demand-want_v5/_doc

| source_index   | source_type               | 映射 | 备注                               |
|:---------------|:--------------------------|:----|:-----------------------------------|
| broker-center  | broker                    | Y   |                                    |
|                |                           |     |                                    |
| common-data    | region                    | Y   |                                    |
|                |                           |     |                                    |
| demand         | demand-want               | Y   |                                    |
| demand         | demand-purchase           | N   | search-api 中，未发现有接口调用源type |
|                |                           |     |                                    |
| house-resource | second-house              | Y   |                                    |
| house-resource | house-resource            | Y   |                                    |
| house-resource | network-house             | Y   |                                    |
| house-resource | second-house-match        | Y   |                                    |
|                |                           |     |                                    |
| topsale        | project                   | Y   |                                    |
| topsale        | building-score            | Y   |                                    |
| topsale        | building-process-withlook | Y   |                                    |
| topsale        | building-process          | Y   |                                    |
| topsale        | building                  | Y   |                                    |
| topsale        | building-process-deal     | Y   |                                    |
| topsale        | project-house-type        | N   | 无引用                             |
| topsale        | building-house-type       | N   | 无引用                             |

