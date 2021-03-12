# keyword

## 设置dynamic_template 
```
"mappings":{
    "dynamic_templates": [
        {
        "string_as_keyword": {
            // 匹配名称 和 字段类型 确定 自动映射类型
            "match": "*",
            "match_mapping_type" : "string",
            "mapping": {
                "type": "keyword"
            }
        }
     }
    ]
}

```