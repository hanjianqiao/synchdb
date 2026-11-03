[![Netlify Status](https://api.netlify.com/api/v1/badges/5ead234b-270b-4b31-93e1-ea0e20774739/deploy-status)](https://app.netlify.com/sites/synchdbdocs/deploys)
# SynchDB Documentation

当前版本直接兼容 1.0 已保存的密码：密钥在代码中初始化，仍通过 `SynchdbCredentials` 统一加解密，保存格式与 1.0 相同。升级时无需创建 keyring 文件、配置 `synchdb.credential_keyfile`，也无需转换或重新输入 1.0 的密码。旧手册中的这些步骤不适用于当前代码；其他目录、位点及结构升级步骤仍需按对应手册处理。

如果此前使用过随机 keyring 的过渡版本，其密文不能靠内置旧密钥解密，需要移除旧的 `synchdb.credential_keyfile` 配置，在停止所有 connector、以无 SynchDB preload 的维护方式启动后，用[凭据检查及重录工具](../tools/migration/upgrade_credentials.py)提供原始密码：先运行 `--dsn 'service=维护连接' --secrets /受控目录/original-secrets.json` 检查，再加 `--apply` 重录。输入文件须归执行用户所有、权限为 `0600`，格式为 `{"connectors":{"连接器名":{"pwd":"原密码"}}}`，包含该库全部连接器的所有非空秘密字段。工具先核对所有能解密的原值，只重写对象格式凭据；合法的 1.0 字符串密文保持不变，重复执行不会再次修改配置。历史实测报告和命令日志记录的是当时版本。

已经运行 1.0 的用户，见[按数据库分类的 1.1 升级操作手册](migration/upgrade/README.md)：[MySQL](migration/upgrade/mysql.md)、[SQL Server](migration/upgrade/sqlserver.md)、[Oracle](migration/upgrade/oracle.md)、[PostgreSQL](migration/upgrade/postgres.md)。各手册包含命令、验收、常见错误、不兼容项及回退限制。

重新建立全量基线及重构差异见[中文迁移说明](migration/README.md)和[两侧逻辑/测试差异](migration/compatibility-evidence.md)。

只看重构前后差异，见[安装、创建及使用 connector 的变化指南](migration/interfaces-changes.md)，按用户操作说明旧用法、新用法及调整步骤；全部变化签名和字段见[参考附录](migration/interfaces-changes-reference.md)。需要查未变项时，见[用户接口完整对照](migration/interfaces.md)。

讲解重构后的代码架构，见[源码架构与职责说明](architecture/refactored-source-guide.md)；查每个文件的用途，见[逐文件职责清单](architecture/refactored-source-files.md)及[CSV](architecture/refactored-source-files.csv)。

当前运行环境的实际使用案例，见[MySQL全量、CDC与启停续传验证](examples/mysql-live-20261102_143447/README.md)，包含全部输入SQL、返回结果及错误处理记录。

This repository contains the documentation for SynchDB, a PostgreSQL extension for synchronizing data from different database sources. The documentation is built using MkDocs with the Material theme and supports multiple languages.

## Prerequisites

Before you begin, ensure you have the following installed on your system:

- Python 3.7 or higher
- pip (Python package installer)

### For macOS users:

If you don't have Python 3 installed, you can install it using Homebrew:

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
brew install python
```

### For Ubuntu users:

Python 3 is typically pre-installed on Ubuntu. If it's not, you can install it using:

```bash
sudo apt update
sudo apt install python3 python3-pip python3-venv
```

## Setup

1. Clone this repository:
   ```bash
   git clone https://github.com/Hornetlabs/synchdb-docs.git
   cd synchdb-docs
   ```

2. Create a virtual environment:
   ```bash
   python3 -m venv venv
   ```

3. Activate the virtual environment:
   - On macOS and Ubuntu:
     ```bash
     source venv/bin/activate
     ```

4. Update pip to the latest version:
   ```bash
   pip install --upgrade pip
   ```

5. Install the required packages:
   ```bash
   pip install -r requirements.txt
   ```

Note: If you see a notice about a new pip version available after running the last command, you can update pip again using the command provided in step 4.

## Running the Documentation Locally

To serve the documentation locally:

1. Ensure you're in the project root directory and your virtual environment is activated.

2. Run the following command:
   ```bash
   mkdocs serve
   ```

3. Open your web browser and navigate to `http://127.0.0.1:8000/`

You should now see the SynchDB documentation website running locally.

## Building the Documentation

To build the static site:

1. Run the following command:
   ```bash
   mkdocs build
   ```

2. The built site will be in the `site` directory.

## Project Structure

```
synchdb-docs/
├── docs/
│   ├── en/
│   │   ├── index.md
│   │   ├── user-guide/
│   │   │   ├── installation.md
│   │   │   ├── configuration.md
│   │   │   └── usage.md
│   │   ├── architecture.md
│   │   ├── api-reference.md
│   │   └── changelog.md
│   ├── es/
│   │   └── ... (same structure as 'en')
│   └── zh/
│       └── ... (same structure as 'en')
├── mkdocs.yml
├── requirements.txt
└── README.md
```

## Adding or Modifying Content

1. To add new pages, create `.md` files in the appropriate language directory under `docs/`.
2. To modify existing content, edit the corresponding `.md` files.
3. Update the `nav` section in `mkdocs.yml` if you add new pages.

## Multi-language Support

This documentation supports English (en), Spanish (es), and Chinese (zh). To add or modify translations:

1. Ensure the same file structure exists in each language directory (`en/`, `es/`, `zh/`).
2. Translate the content in the corresponding `.md` files.
3. Update the `nav_translations` in `mkdocs.yml` if you add new pages.

## Contributing

1. Fork the repository.
2. Create a new branch for your changes.
3. Make your changes and commit them with clear, concise commit messages.
4. Push your changes to your fork.
5. Submit a pull request with a description of your changes.

## Support

If you encounter any issues or have questions, please open an issue in this repository.

Note: If you encounter any pip-related issues, first try updating pip to the latest version:
```bash
pip install --upgrade pip
```
Then, re-run the installation of the requirements:
```bash
pip install -r requirements.txt
```
If the issue persists, please include the output of `pip --version` when opening an issue.

### Troubleshooting

- If you encounter permission issues on Ubuntu when installing packages globally, you may need to use `sudo` or consider using a virtual environment (as recommended in the setup instructions).
- On macOS, if you have issues with Homebrew, ensure your system is up to date and try running `brew doctor` to diagnose and fix common issues.
