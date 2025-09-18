# CI/CD 流水线配置说明

本项目包含三个平台的CI/CD配置文件，用于自动部署到腾讯云CloudBase静态托管。

## 配置文件说明

### 1. GitHub Actions (`.github/workflows/deploy.yml`)
- **触发条件**: 推送到main分支或创建PR到main分支
- **运行环境**: ubuntu-latest
- **Node.js版本**: 18
- **缓存**: 启用npm缓存

### 2. GitLab CI (`.gitlab-ci.yml`)
- **阶段**: build（构建）和 deploy（部署）
- **镜像**: node:18-alpine
- **缓存**: node_modules和.npm目录
- **制品**: 构建产物保存1小时
- **环境**: 生产环境配置

### 3. Gitee Actions (`.gitee/workflows/deploy.yml`)
- **功能**: 与GitHub Actions基本相同
- **增强**: 添加了部署状态通知
- **兼容性**: 使用actions/checkout@v3和actions/setup-node@v3

## 环境变量配置

所有平台都需要配置以下密钥变量：

### GitHub Secrets
在 GitHub 仓库的 Settings > Secrets and variables > Actions 中添加：
- `TCB_SECRET_ID`: 腾讯云API密钥ID
- `TCB_SECRET_KEY`: 腾讯云API密钥Key
- `TCB_ENV_ID`: CloudBase环境ID

### GitLab Variables
在 GitLab 项目的 Settings > CI/CD > Variables 中添加：
- `TCB_SECRET_ID`: 腾讯云API密钥ID（设置为Protected和Masked）
- `TCB_SECRET_KEY`: 腾讯云API密钥Key（设置为Protected和Masked）
- `TCB_ENV_ID`: CloudBase环境ID

### Gitee Secrets
在 Gitee 仓库的 管理 > Actions > 密钥管理 中添加：
- `TCB_SECRET_ID`: 腾讯云API密钥ID
- `TCB_SECRET_KEY`: 腾讯云API密钥Key
- `TCB_ENV_ID`: CloudBase环境ID

## 部署流程

1. **代码检出**: 获取最新代码
2. **环境设置**: 安装Node.js 18
3. **依赖安装**: 使用npm ci安装依赖
4. **项目构建**: 执行npm run build
5. **CLI安装**: 安装CloudBase CLI工具
6. **登录认证**: 使用API密钥登录CloudBase
7. **静态部署**: 将dist目录部署到/home路径

## 注意事项

1. **package.json要求**: 确保项目包含`build`脚本
2. **构建输出**: 默认构建输出目录为`./dist`
3. **部署路径**: 静态文件部署到CloudBase的`/home`路径
4. **分支保护**: 只有main分支的推送会触发部署
5. **权限配置**: 确保API密钥具有CloudBase静态托管的部署权限

## 故障排除

- **构建失败**: 检查Node.js版本和依赖配置
- **登录失败**: 验证TCB_SECRET_ID和TCB_SECRET_KEY是否正确
- **部署失败**: 确认TCB_ENV_ID和部署权限
- **缓存问题**: 可以在CI/CD设置中清除缓存重新构建