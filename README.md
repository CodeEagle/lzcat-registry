# lzcat-registry

`lzcat-trigger` 的集中配置仓库。

## 目录

- `repos/index.json`: 仓库索引
- `repos/*.json`: 单个目标仓库的默认检查和构建配置

## 配置原则

- 一个目标仓库对应一个 JSON 文件，避免多人修改同一个大文件
- `workflow_dispatch` 或 `repository_dispatch` 传入的参数优先级高于这里的默认配置
- `_template` 不再承载定时检查逻辑；这里只保存默认检查和构建信息

## 字段说明

- `repo`: 目标仓库，格式 `owner/name`
- `enabled`: 是否参与定时检查
- `upstream_repo`: 上游仓库，格式 `owner/name`
- `check_strategy`: 版本检查策略，当前支持 `github_release`、`github_tag`、`commit_sha`
- `build_strategy`: 构建策略，当前支持 `official_image`、`precompiled_binary`、`upstream_dockerfile`、`upstream_with_target_template`、`target_repo_dockerfile`
- `publish_to_store`: 默认是否发布到懒猫应用商店
- `official_image_registry`: 官方镜像前缀，不带 tag
- `precompiled_binary_url`: 预编译二进制下载地址模板，支持 `$LATEST_VERSION`
- `dockerfile_type`: `simple` 或 `custom`
- `dockerfile_path`: Dockerfile 或 Dockerfile.template 的相对路径
- `build_context`: Docker build context 的相对路径
- `service_port`: 默认暴露端口
- `service_cmd`: 默认启动命令数组
- `image_targets`: 主镜像需要回写到 manifest 的服务名列表
- `build_args`: 传给 Docker build 的额外参数
- `dependencies`: 依赖镜像列表，`target_service` 为目标 manifest 中要替换的服务名

## 新增仓库

1. 在 `repos/` 下新增一个 `<owner>__<repo>.json`
2. 在 `repos/index.json` 中追加文件名
3. 提交到配置仓库后，`lzcat-trigger` 下次轮询会自动加载
