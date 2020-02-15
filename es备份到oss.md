
# 将es快照创建到hdfs中

1. 安装repository-hdfs插件

    ```shell
        # 安装:
        sudo bin/elasticsearch-plugin install repository-hdfs
        # 卸载:
        sudo bin/elasticsearch-plugin remove repository-hdfs
    ```

2. 重启es
3. 在hdfs上创建仓库

    ```text
    PUT _snapshot/hdfs_backup
    {
    "type": "hdfs",
    "settings": {
        "uri": "hdfs://localhost:8020/",
        "path": "/elasticsearch/repository",
        "conf.dfs.client.read.shortcircuit": "false"
        }
    }
    ```

    仓库settings文档:

    ```url
    https://www.elastic.co/guide/en/elasticsearch/plugins/master/repository-hdfs-config.html
    ```

4. 增量备份

    ```text
    PUT _snapshot/hdfs_backup/test_snapshot_01?wait_for_completion=true
    {
        "indices": "index_1",   # 指定index，不指定则全部备份
        "ignore_unavailable": true, # 忽略不存在的index
        "include_global_state": false,  # 备份集群状态
        "partial": true # 如果开启主节点异常将会备份失败
    }
    ```

    ```shell
    # 第一次是全量备份，之后就变成增量了，快照名称不能相同
    PUT _snapshot/hdfs_backup/test_snapshot?wait_for_completion=false
    {
        "indices": "index1,index2",
        "ignore_unavailable": true,
        "include_global_state": false,
        "partial": true
    }
    # 查看备份进度
    GET _snapshot/hdfs_backup/test_snapshot/_status?pretty
    # 删除快照
    DELETE _snapshot/hdfs_backup/test_snapshot
    ```

5. 恢复数据

    ```shell
    # 关闭index，恢复完成后会自动打开index
    POST index/_close
    # 开始恢复
    POST _snapshot/hdfs_backup/test_snapshot/_restore
    {
        "indices": "hzeg-history-words",
        "ignore_unavailable": true,
        "include_global_state": true
    }
    ```