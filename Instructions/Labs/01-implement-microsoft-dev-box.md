---
lab:
  title: 实现 Microsoft Dev Box
  module: Implement Developer Self-Service
---

# 实验室 01：Microsoft Dev Box 实现

## 预计用时：60 分钟

## 先决条件

- 一个 Microsoft Entra 租户，其中具有三个预创建的用户帐户（以及可选的三个预创建的 Microsoft Entra 组），代表 Microsoft Dev Box 部署中涉及的三个不同角色。 为了确保清晰，实验室说明中的用户和组名称将与以下表格中的信息一致：

| 用户              | 组                        | 角色                  |
| ----------------- | ---------------------------- | --------------------- |
| platformegineer01 | DevCenter_Platform_Engineers | 平台工程师     |
| devlead01         | DevCenter_Dev_Leads          | 开发团队负责人 |
| devuser01         | DevCenter_Dev_Users          | 开发人员             |

- 一个 Azure 订阅，用于托管与托管用户和组帐户的 Microsoft Entra 租户相关的 Microsoft Dev Box 资源
- 一个 Microsoft Intune 订阅，其与 Azure 订阅相同的 Microsoft Entra 租户关联
- 分配给三个预创建的 Microsoft Entra 用户帐户的 Microsoft Intune 许可证
- 将需要 GitHub 帐户和存储库。
  - 要创建 GitHub 帐户，请按照[在 GitHub 上创建帐户](https://docs.github.com/get-started/quickstart/creating-an-account-on-github)一文中的说明操作。
  - 要创建 GitHub 存储库，请按照文章“[创建新存储库](https://docs.github.com/repositories/creating-and-managing-repositories/creating-a-new-repository)”中的说明操作。
- 创建为 https://github.com/microsoft/devcenter-catalog 分支的 GitHub 存储库
- 创建为 https://github.com/MicrosoftLearning/contoso-co-eShop 分支的 GitHub 存储库

## 目标

完成本研讨会后，你将能够：

- 实现 Microsoft Dev Box 环境
- 自定义 Microsoft Dev Box 环境

## 练习 1：实现 Microsoft Dev Box 环境

在本练习中，你将利用 Microsoft 提供的一组功能来实现 Microsoft Dev Box 环境。 此方法侧重于最大程度地减少构建功能开发人员自助服务解决方案所涉及的工作量。

该练习由以下任务组成：

- 任务 1：创建开发人员中心
- 任务 2：查看开发人员中心设置
- 任务3：创建开发箱定义
- 任务 4：创建项目
- 任务 5：创建开发箱池
- 任务 6：配置权限
- 任务 7：评估开发箱

### 任务 1：创建开发人员中心

在此任务中，你将创建一个 Azure 开发人员中心，该中心将在本实验室的第一个练习中使用。 开发人员中心是一项平台工程服务，用于集中创建和管理可缩放、预配置的开发和部署环境，优化软件开发团队的协作和资源利用率。

1. 启动 Web 浏览器，并导航到位于 `https://portal.azure.com` 的 Azure 门户。
1. 当系统提示进行身份验证时，使用 Microsoft Entra 用户帐户登录。
1. 在 Azure 门户的“**搜索**”文本框中，搜索 **`Dev centers`** 并将其选中。
1. 在**开发人员中心**页面上，选择“**+ 创建**”。
1. 在“**创建开发人员中心**”页的“**基本信息**”选项卡中，指定以下设置，然后选择“**下一步: 设置**”：

   | 设置                                                                 | 值                                                        |
   | ----------------------------------------------------------------------- | ------------------------------------------------------------ |
   | 订阅                                                            | 你在此实验室中使用的 Azure 订阅的名称 |
   | 资源组                                                          | **新**资源组名 **rg-devcenter-01**     |
   | 名称                                                                    | **devcenter-01**                                             |
   | 位置                                                                | **（美国）美国东部**                                             |
   | 附加快速入门目录 - Azure 部署环境定义 | 已启用                                                      |
   | 附加快速入门目录 - 开发箱自定义任务              | 已禁用                                                     |

   > **注意：** 你将在本实验室的第二个练习中配置一个启用自定义任务的目录。

1. 在**创建开发人员中心**页面的“设置”**** 选项卡上，指定以下设置，然后选择**查看 + 创建**：

   | 设置                                    | “值”   |
   | ------------------------------------------ | ------- |
   | 为每个项目启用目录                | 已启用 |
   | 允许在项目中使用 Microsoft 托管的网络 | 已启用 |
   | 启用 Azure Monitor 代理的安装    | 已启用 |

   > **注意：** 根据设计，附加到开发人员中心的目录资源可供其中的所有项目使用。 启用**为每个项目启用目录**设置后，还可以将其他目录附加到任意选择的项目中。

   > **注意：** 开发箱可连接到你在 Azure 订阅中的虚拟网络，或 Microsoft 托管的虚拟网络，具体取决于是否需要访问你环境中的资源。 如果不需要访问本地资源，可启用**允许项目中使用 Microsoft 托管网络**设置，启用后可选择将开发箱连接到 Microsoft 托管网络的选项，有效最大限度地降低管理与配置负担。

   > **注意：****启用 Azure Monitor 代理的安装**设置会自动触发在开发人员中心的所有开发箱中安装 Azure Monitor 代理。

1. 在**查看 + 创建**选项卡上，等待验证过程完成后选择**创建**。

   > **注意：** 等待项目创建完成。 项目创建可能需要约 1 分钟。

1. 在“**部署完成**”页上，选择“**转到资源**”。

### 任务 2：查看开发人员中心设置

在此任务中，你将查看上一个任务中创建的开发人员中心的基本配置设置。

1. 在显示 Azure 门户的 Web 浏览器中，打开 **devcenter-01** 页面，在左侧垂直导航菜单中展开**环境配置**部分，然后选择**目录**。
1. 在 **devcenter-01 \| 目录**页面上，请注意开发人员中心已配置 **quickstart-environment-definitions** 目录，该目录指向 GitHub 存储库`https://github.com/microsoft/devcenter-catalog.git`。
1. 验证**状态**列是否包含**同步成功**条目。 如果不是这种情况，请使用以下步骤序列重新创建目录：

   1. 选中自动生成的目录条目 **quickstart-environment-definitions** 旁的复选框，然后在工具栏中选择删除****。
   1. 在 **devcenter-01 \| 目录** 页面，选择 **+ 添加**。
   1. 在**添加目录**窗格中，**名称**文本框中输入 ****`quickstart-environment-definitions-fix`，在**目录位置**部分选择 **GitHub**，在**身份验证类型**中选择 **GitHub Apps**，保留**自动同步此目录**复选框选中状态，然后选择**使用 GitHub 登录**。
   1. 在**使用 GitHub 登录**窗口中，输入 GitHub 凭据，然后选择**登录**。

      > **注意：** 这些 GitHub 凭据为你提供对作为<https://github.com/microsoft/devcenter-catalog>分支的 GitHub 存储库的访问权限

   1. 出现提示时，在**授权 Microsoft DevCenter** 窗口中，选择**授权 Microsoft DevCenter**。
   1. 返回**添加目录**窗格，在**存储库**下拉列表中选择 **devcenter-catalog**，在**分支**下拉列表中保留**默认分支**条目，在**文件夹路径**中输入****`Environment-Definitions`，然后选择**添加**。
   1. 返回 **devcenter-01 \| 目录**页面，监控**状态**列中的条目，验证同步是否成功完成。

1. 在 **devcenter-01 \| 目录**页面，选择 **quickstart-environment-definitions-fix** 条目。
1. 在 **quickstart-environment-definitions-fix** 页面，查看预定义的环境定义列表。

   > **注意：** 每个条目对应 GitHub 存储库`https://github.com/microsoft/devcenter-catalog.git`中**环境定义**文件夹下相应子文件夹定义的 Azure 部署环境。

   > **注意：** 部署环境是由名为环境定义的模板所定义的一组 Azure 资源。 开发人员可以使用这些定义来部署将用于托管其解决方案的基础结构。 有关 Azure 部署环境的详细信息，请参阅 Microsoft Learn 文章：[什么是 Azure 部署环境？](https://learn.microsoft.com/azure/deployment-environments/overview-what-is-azure-deployment-environments)

### 任务3：创建开发箱定义

在此任务中，你将创建开发框定义。 其用途是定义操作系统、工具、设置和资源，作为创建一致和定制开发环境的蓝图（称为开发框）。

1. 在显示 Azure 门户的 Web 浏览器中，在 **devcenter-01** 页面上的左侧垂直导航菜单中，展开“**开发箱配置**”部分，然后选择“**开发箱定义**”。
1. 在“**devcenter-01 \| 开发箱定义**”页上，选择“**+ 创建**”。
1. 在“**创建开发箱定义**”页上，指定以下设置，然后选择“**创建**”：

   | 设置            | 值                                                                                |
   | ------------------ | ------------------------------------------------------------------------------------ | ----------------------- |
   | 名称               | **devbox-definition-01**                                                             |
   | 映像              | \*\*Windows 11 企业版上的 Visual Studio 2022 Enterprise + Microsoft 365 应用版 24H2 | 支持休眠\*\* |
   | 映像版本      | **最新**                                                                           |
   | 计算            | **8 vCPU，32 GB RAM**                                                                |
   | 存储            | **256 GB SSD**                                                                       |
   | 启用休眠 | 已启用                                                                              |

   > **备注：** 等待创建开发箱定义。 此过程应该会在 1 分钟内完成。

### 任务 4：创建开发人员中心项目

在此任务中，你将创建开发人员中心项目。 开发人员中心项目通常与组织中的开发项目相对应。 例如，可以创建一个项目用于开发业务线应用程序，创建另一个项目用于开发公司网站。 一个开发人员中心中的所有项目都将共享相同的开发箱定义、网络连接、目录和计算库。 如果你有多个具有单独的项目管理员和访问权限要求的开发项目，则可以考虑创建多个开发人员中心项目。

1. 在显示Azure 门户的 Web 浏览器中，**在 devcenter-01** 页面上的左侧垂直导航菜单中，展开“**管理**”部分，然后选择“**项目**”。
1. 在“**devcenter-01 \| 项目**”页上，选择“**+ 创建**”。
1. 在“**创建项目**”页的“**基本信息**”选项卡中，指定以下设置并选择“**下一步: 开发箱管理**”：

   | 设置        | 值                                                        |
   | -------------- | ------------------------------------------------------------ |
   | 订阅   | 你在此实验室中使用的 Azure 订阅的名称 |
   | 资源组 | **rg-devcenter-01**                                          |
   | 开发人员中心     | **devcenter-01**                                             |
   | 名称           | **devcenter-project-01**                                     |
   | 说明    | **devcenter-project-01**                                     |

1. 在“**创建项目**”页的“**开发箱管理**”选项卡上，指定以下设置，然后选择**下一步：目录**：

   | 设置                 | “值” |
   | ----------------------- | ----- |
   | 启用开发箱限制   | 是   |
   | 每个开发人员的开发箱 | **2** |

1. 在“**创建项目**”页的“**目录**”选项卡上，指定以下设置，然后选择**查看 + 创建**：

   | 设置                            | “值”   |
   | ---------------------------------- | ------- |
   | 部署环境定义 | 已启用 |
   | 映像定义                  | 已启用 |

   > **备注：** 由于在创建开发人员中心时启用了项目级目录，因此对于附加到开发项目的目录，这些设置确定同步时将导入到项目中的目录项。

1. 在“**创建开发人员中心**”页的“**查看 + 创建**”选项卡上，选择“**创建**”。

   > **备注：** 等待创建项目。 此过程应该会在 1 分钟内完成。

1. 在“**部署完成**”页上，选择“**转到资源**”。

### 任务 5：创建开发箱池

在此任务中，将在上一任务中创建的开发人员中心项目中创建开发箱池。 开发箱用户使用开发箱池创建开发箱。 开发箱池会将开发箱定义与网络连接进行链接。 一般来说，你可以从 Microsoft 托管连接或自己的 Azure 网络连接中进行选择。 网络连接确定开发箱的位置及其对其他云和本地资源的访问。 请考虑创建一个具有最接近开发箱用户的网络连接的开发箱池。 此外，为了降低运行开发箱的成本，你可以在开发箱池中配置开发箱，使其每天在预定义的时间关闭。

1. 在显示 Azure 门户的 Web 浏览器中，在 **devcenter-project-01** 页面上的左侧垂直导航菜单中，展开 **“管理**”部分，然后选择“**开发箱池**”。
1. 在 **devcenter-project-01 \| 开发箱池** 页上，选择“ **+ 创建**”。
1. 在“**创建开发箱池**”页的“**基本信息**”选项卡上，指定以下设置，然后选择“**创建**”：

   | 设置                                                                                                           | 值                                    |
   | ----------------------------------------------------------------------------------------------------------------- | ---------------------------------------- |
   | 名称                                                                                                              | **devcenter-project-01-devbox-pool-01**  |
   | 定义                                                                                                        | **devbox-definition-01**                 |
   | 网络连接                                                                                                | **部署到 Microsoft 托管的网络** |
   | 区域                                                                                                            | **（美国）美国东部**                         |
   | 启用单一登录                                                                                             | 已启用                                  |
   | Dev Box 创建者特权                                                                                        | **本地管理员**                  |
   | 按计划启用自动停止                                                                                      | 已启用                                  |
   | 停止时间                                                                                                         | **下午 07:00**                             |
   | 时区                                                                                                         | 当前时区                   |
   | 在断开连接时启用休眠                                                                                    | 已启用                                  |
   | 宽限期（分钟）                                                                                           | **60**                                   |
   | 我确认组织有 Azure 混合权益许可证，这将应用于此池中的所有开发箱 | 已启用                                  |

   > **备注：** 等待创建开发箱池。 这可能需要大约 2 分钟。

### 任务 6：配置权限

在此任务中，将为已在实验室环境中预配的三个 Microsoft Entra 安全主体分配适当的 Microsoft Dev Box 相关权限。 这些安全主体对应于平台工程方案中的典型角色：

| 用户              | 组                        | 角色                  |
| ----------------- | ---------------------------- | --------------------- |
| platformegineer01 | DevCenter_Platform_Engineers | 平台工程师     |
| devlead01         | DevCenter_Dev_Leads          | 开发团队负责人 |
| devuser01         | DevCenter_Dev_Users          | 开发人员             |

Microsoft Dev Box 依靠 Azure 基于角色的访问控制 (Azure RBAC) 来控制对项目级功能的访问权限。 平台工程师应完全控制创建和管理开发人员中心、其目录和项目。 这实际上需要所有者或参与者角色，具体取决于他们是否需要向其他人委派权限。 应为开发团队主管分配开发人员中心项目管理员角色，该角色授予对 Microsoft Dev Box 项目执行管理任务的功能。 开发箱用户需要能够创建和管理自己的开发箱，这些开发箱与 Dev Box 用户角色相关联。

> **备注：** 首先将权限分配给要包含平台工程师用户帐户的 Microsoft Entra 组。

1. 在显示 Azure 门户的 Web 浏览器中，导航到 **devcenter-01** 页面，在左侧的垂直导航菜单中，选择 **“访问控制”（IAM）**。
1. 在 **devcenter-01 \| 访问控制 (IAM)** 页上，选择“**+ 添加**”，然后在下拉列表中选择“**添加角色分配**”。
1. 在“**添加角色分配**”页的“**角色**”选项卡上，选择“**特权管理员角色**”选项卡，在角色列表中选择“**所有者**”，最后选择“**下一步**”。
1. 在“**添加角色分配**”页的“**成员**”选项卡上，确保已选择“**用户、组或服务主体**”选项，然后单击“**+ 选择成员**”。
1. 在**选择成员**窗格中，搜索并选择**`DevCenter_Platform_Engineers`**，然后单击**选择**。
1. 返回到“**添加角色分配**”页的“**成员**”选项卡上，选择“**下一步**”。
1. 在**添加角色分配**页面的**条件**选项卡中，找到**用户可以执行的操作**部分，选择 **“允许用户分配所有角色（高权限）** 选项，然后点击**下一步**。
1. 在“**添加角色分配**”页的“**查看 + 分配**”选项卡上，选择“**查看 + 分配**”。

   > **注意：** 接下来，你将为包含开发团队潜在客户用户帐户的 Microsoft Entra 组分配权限。

1. 返回 **devcenter-01\| 访问控制 (IAM) **页面，在左侧垂直导航菜单中展开**管理**部分，选择**项目**，在项目列表中选择 **devcenter-project-01**。
1. 在**devcenter-project-01**页面，左侧垂直导航菜单中选择**访问控制 (IAM)**。
1. 在 **devcenter-project-01\| 访问控制 (IAM) **访问控制 (IAM)页面上，选择 **+ 添加**，然后在下拉菜单中选择**添加角色分配**。
1. 在“**添加角色分配**”页的“**角色**”选项卡上，确保已选择“**工作职能角色**”选项卡，在角色列表中选择“**DevCenter 项目管理员**”，然后选择“**下一步**”。
1. 在“**添加角色分配**”页的“**成员**”选项卡上，确保已选择“**用户、组或服务主体**”选项，然后单击“**+ 选择成员**”。
1. 在**选择成员**窗格中，搜索并选择**`DevCenter_Dev_Leads`**，然后单击**选择**。
1. 返回到“**添加角色分配**”页的“**成员**”选项卡上，选择“**下一步**”。
1. 在“**添加角色分配**”页的“**查看 + 分配**”选项卡上，选择“**查看 + 分配**”。

   > **注意：** 最后，你将为包含开发人员用户帐户的 Microsoft Entra 组分配权限。

1. 返回到“**devcenter-project-01 \| 访问控制 (IAM) **”页面上，选择“**+ 添加**”，然后在下拉菜单中选择“**添加角色分配**”。
1. 在“**添加角色分配**”页面的“**角色**”选项卡上，确保已选择“**工作职能角色**选项卡，在角色列表中选择“**DevCenter 开发箱用户**”，然后选择“**下一步**”。
1. 在“**添加角色分配**”页的“**成员**”选项卡上，确保已选择“**用户、组或服务主体**”选项，然后单击“**+ 选择成员**”。
1. 在**选择成员**窗格中，搜索并选择**`DevCenter_Dev_Users`**，然后单击**选择**。
1. 返回到“**添加角色分配**”页的“**成员**”选项卡上，选择“**下一步**”。
1. 在“**添加角色分配**”页的“**查看 + 分配**”选项卡上，选择“**查看 + 分配**”。

### 任务 7：评估开发箱

在此任务中，你将使用 Microsoft Entra 开发人员用户帐户评估开发箱功能。

1. 在 Web 浏览器中启用隐身/专用模式，并导航到 `https://aka.ms/devbox-portal` 的 Microsoft Dev Box 开发人员门户。
1. 出现登录提示时，提供 **devuser01** 用户帐户的凭据。
1. 在 Microsoft Dev Box 开发人员门户的 **欢迎，devuser01** 页面上，选择 **+ 新建开发箱**。
1. 在**添加开发箱**窗格中的**名称**文本框中，输入**`devuser01box01`**
1. 查看**添加开发箱**窗格中显示的其他信息，包括项目名、开发箱池规格、休眠支持状态以及计划关闭时间。 此外，请注意应用自定义项的选项，以及开发箱创建可能需要最多 65 分钟的通知。

   > **注意：** 开发箱名在项目中必须是唯一的。

1. 在**添加开发箱**窗格中，选择**创建**。

   > **注意：** 不要等待开发箱创建完成。 而是直接进行本实验室的下一个练习。 但完成下一个练习后，考虑返回此练习并继续完成其余部分。

1. 开发箱创建完成并运行后，选择**通过应用连接**选项进行连接。

   > **备注：** 可以使用远程桌面 Windows 应用、远程桌面客户端 (mstsc.exe)，或直接在 Web 浏览器窗口中，与开发箱建立连接。

1. 在弹出的**此站点正尝试打开 Microsoft 远程连接中心**窗口中，选择**打开**。 这会自动发起与开发箱的远程桌面会话。
1. 系统提示输入凭据时，提供 **devuser01** 帐户的用户名和密码以完成身份验证。
1. 在与开发箱的远程桌面会话中，验证配置中是否已安装 Visual Studio 2022 和 Microsoft 365 应用。

   > **注意：** 你可以直接从 Microsoft Dev Box 开发人员门户关闭开发箱。进入**你的开发箱**界面，选择省略号符号，并在级联菜单中选择**关闭**即可。 或者，平台工程师或开发团队主管可以在相应开发人员中心项目的**开发箱池**部分控制开发箱的生命周期。

## 练习 2：自定义 Microsoft Dev Box 环境

在本练习中，你将自定义 Microsoft Dev Box 环境的功能。 此方法侧重于实现自定义开发人员自助服务解决方案时可以应用的更改范围。

该练习由以下任务组成：

- 任务 1：创建 Azure compute gallery，并将其附加到开发人员中心
- 任务 2：为 Azure 映像生成器配置身份验证和授权
- 任务 3：使用 Azure 映像生成器创建自定义映像
- 任务 4：创建 Azure 开发人员中心网络连接
- 任务 5：将映像定义添加到 Azure 开发人员中心项目
- 任务 6：创建自定义开发箱池
- 任务 7：评估自定义开发箱

### 任务 1：创建 Azure compute gallery，并将其附加到开发人员中心

在此任务中，你将创建一个 Azure compute gallery，并将其附加到在本实验室上一个练习中创建的开发人员中心。 库是存储在 Azure 订阅中的一个存储库，帮助你围绕自定义映像构建结构和组织。 将计算库附加到开发人员中心并填充映像后，即可根据存储在计算库中的映像创建开发箱定义。

1. 启动 Web 浏览器，并导航到位于 `https://portal.azure.com` 的 Azure 门户。
1. 系统提示进行身份验证时，使用 Microsoft 帐户登录。
1. 在 Azure 门户的**搜索**文本框中，搜索并选择**`Azure compute galleries`**。
1. 在 **Azure compute galleries** 页面上选择 **+ 创建**。
1. 在“**创建 Azure Compute Gallery**”页面的“**基本信息**”选项卡上，指定以下设置，然后选择**下一步：共享方法**：

   | 设置        | 值                                                        |
   | -------------- | ------------------------------------------------------------ |
   | 订阅   | 你在此实验室中使用的 Azure 订阅的名称 |
   | 资源组 | **rg-devcenter-01**                                          |
   | 名称           | **compute_gallery_01**                                       |
   | 区域         | **（美国）美国东部**                                             |

1. 在“**创建 Azure Compute Gallery**”页面的“**共享方法**”选项卡上，保持选项“**基于角色的访问控制 (RBAC)**”为选中状态，然后选择**查看+创建**：
1. 在**查看 + 创建**选项卡上，等待验证过程完成后选择**创建**。

   > **注意：** 等待项目创建完成。 创建 Azure compute gallery 用时不到 1 分钟。

1. 在Azure 门户中，搜索并选择**`Dev centers`**，然后在**开发人员中心**页面上选择 **devcenter-01**。
1. 在 **devcenter-01** 页面左侧的垂直导航菜单中，展开**开发箱配置**部分，然后选择 **Azure compute galleries**。
1. 在 **devcenter-01\| Azure compute galleries** 页面上，选择 **+ 添加**。
1. 在**添加 Azure compute gallery** 窗格中的**库**下拉列表中，选择 **compute_gallery_01**，然后选择**添加**。

   > **注意：** 如果收到错误消息：“_此开发人员中心未分配系统标识或用户标识。必须先分配标识才能添加库。_” 你需要为开发人员中心分配系统标识。
   > 为此，请在 Azure 门户的“**devcenter-01**”页面，左侧垂直导航菜单中选择“设置”下的“**标识**”，在“**系统分配**”选项卡中，将“**状态**”开关切换为“**打开**”，然后选择“保存”****。

### 任务 2：配置身份验证和授权

在此任务中，你将创建一个用户分配的托管标识，供 Azure 映像生成器使用，以将映像添加到上一任务中创建的 Azure compute gallery。 你还需要创建一个自定义的基于角色的访问控制 (RBAC) 角色，并将其分配给托管标识，以配置所需的权限。 这样，你就能够在下一个任务中使用 Azure 映像生成器生成自定义映像。

1. 在 Azure 门户中，选择 **Cloud Shell** 工具栏图标以打开 Cloud Shell 窗格。如需使用 PowerShell，请选择**切换到 PowerShell**，在**切换到 Cloud Shell 中的 PowerShell** 对话框中，选择**确认**以启动 PowerShell 会话。

   > **备注：** 如果你是第一次打开 Cloud Shell，请在“**欢迎使用 Azure Cloud Shell**”对话框中选择“**PowerShell**”，然后在“**入门**”窗格中选择“**不需要存储帐户**”选项，最后在“**订阅**”下拉列表中，选择你在本实验室中使用的 Azure 订阅名。

1. 在 Cloud Shell 窗格的 PowerShell 会话中运行以下命令，确保所有必需的资源提供程序均已注册：

   ```powershell
   Register-AzResourceProvider -ProviderNamespace Microsoft.VirtualMachineImages
   Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
   Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
   Register-AzResourceProvider -ProviderNamespace Microsoft.KeyVault
   Register-AzResourceProvider -ProviderNamespace Microsoft.Network
   ```

1. 运行以下命令以安装所需的 PowerShell 模块（出现提示时，键入 **A** 并按 Enter **** 键）：

   ```powershell
   'Az.ImageBuilder', 'Az.ManagedServiceIdentity' | ForEach-Object {Install-Module -Name $_ -AllowPrerelease}
   ```

1. 运行以下命令，设置将在映像生成过程中引用的变量：

   ```powershell
   $currentAzContext = Get-AzContext
   # the target Azure subscription ID
   $subscriptionID=$currentAzContext.Subscription.Id
   # the target Azure resource group name
   $imageResourceGroup='rg-devcenter-01'
   # the target Azure region
   $location='eastus'
   # the reference name assigned to the image created by using the Azure Image Builder service
   $runOutputName="aibWinImg01"
   # image template name
   $imageTemplateName="templateWinVSCode01"
   # the Azure compute gallery name
   $computeGallery = 'compute_gallery_01'
   ```

1. 运行以下命令，创建用户分配的托管标识（VM 映像生成器使用提供的用户标识将映像存储在目标 Azure 计算库中）：

   ```powershell
   # Install the Azure PowerShell module to support AzUserAssignedIdentity
   Install-Module -Name Az.ManagedServiceIdentity
   # Generate a pseudo-random integer to be used for resource names
   $timeInt=$(get-date -UFormat "%s")

   # Create an identity
   $identityName='identityAIB' + $timeInt
   New-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName -Location $location
   $identityNameResourceId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).Id
   $identityNamePrincipalId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).PrincipalId
   ```

1. 运行以下命令，向新创建的用户分配的托管标识授予在 **rg-devcenter-01** 资源组中存储映像所需的权限：

   ```powershell
   # Set variables
   $imageRoleDefName = 'Custom Azure Image Builder Image Def' + $timeInt
   $aibRoleImageCreationUrl = 'https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json'
   $aibRoleImageCreationPath = 'aibRoleImageCreation.json'

   # Customize the role definition file
   Invoke-WebRequest -Uri $aibRoleImageCreationUrl -OutFile $aibRoleImageCreationPath -UseBasicParsing
   ((Get-Content -path $aibRoleImageCreationPath -Raw) -Replace '<subscriptionID>', $subscriptionID) | Set-Content -Path $aibRoleImageCreationPath
   ((Get-Content -path $aibRoleImageCreationPath -Raw) -Replace '<rgName>', $imageResourceGroup) | Set-Content -Path $aibRoleImageCreationPath
   ((Get-Content -path $aibRoleImageCreationPath -Raw) -Replace 'Azure Image Builder Service Image Creation Role', $imageRoleDefName) | Set-Content -Path $aibRoleImageCreationPath

   # Create a role definition
   New-AzRoleDefinition -InputFile  ./aibRoleImageCreation.json

   # Assign the role to the VM Image Builder user-assigned managed identity within the scope of the **rg-devcenter-01** resource group
   New-AzRoleAssignment -ObjectId $identityNamePrincipalId -RoleDefinitionName $imageRoleDefName -Scope "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"
   ```

### 任务 3：使用 Azure 映像生成器创建自定义映像

在此任务中，将使用 Azure 映像生成器基于现有的 Azure 资源管理器 （ARM） 模板创建自定义映像，该模板定义自动安装的 Choco 和 Visual Studio Code 的 Windows 11 企业版映像。 Azure VM 映像生成器大大简化了定义和配置 VM 映像的过程。 它依赖于你指定的映像配置来配置自动化映像管道。 随后，开发人员将能够使用此类映像来配置其开发箱。

1. 在 Cloud Shell 窗格的 PowerShell 会话中，运行以下命令以创建映像定义，并将其添加到本练习第一个任务中创建的 Azure compute gallery：

   ```powershell
   # ensure that the image definition security type property is set to 'TrustedLaunch'
   $securityType = @{Name='SecurityType';Value='TrustedLaunch'}
   $features = @($securityType)
   # Image definition name
   $imageDefName = 'imageDefDevBoxVSCode01'

   # Create the image definition
   New-AzGalleryImageDefinition -GalleryName $computeGallery -ResourceGroupName $imageResourceGroup -Location $location -Name $imageDefName -OsState generalized -OsType Windows -Publisher 'Contoso' -Offer 'vscodedevbox' -Sku '1-0-0' -Feature $features -HyperVGeneration 'V2'
   ```

   > **注意：** 开发箱映像必须符合多个要求，包括使用第 2 代、Hyper-V v2 和 Windows 10 或 11 企业版 20H2 或更高版本。 有关完整列表，请参阅 Microsoft Learn 文章[为 Microsoft Dev Box 配置 Azure Compute Gallery](https://learn.microsoft.com/en-us/azure/dev-box/how-to-configure-azure-compute-gallery)。

1. 运行以下命令，创建一个名为 template.json 的空文件，该文件包含一个 ARM 模板，定义了自动安装 Choco 和 Visual Studio Code 的 Windows 11 企业版映像：

   ```powershell
   Set-Location -Path ~
   $templateFile = 'template.json'
   Set-Content -Path $templateFile -Value ''
   ```

1. 在 Cloud Shell 的 PowerShell 会话中，使用 nano 文本编辑器将以下内容添加到新创建的文件中：

   > **注意：** 运行命令`nano ./template.json`以打开文本编辑器。 如果要保存更改并退出 nano 文本编辑器，请按 Ctrl+X ****，然后按 Y ****，最后按 Enter****。

   ```json
   {
     "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
     "contentVersion": "1.0.0.0",
     "parameters": {
       "imageTemplateName": {
         "type": "string"
       },
       "api-version": {
         "type": "string"
       },
       "svclocation": {
         "type": "string"
       }
     },
     "variables": {},
     "resources": [
       {
         "name": "[parameters('imageTemplateName')]",
         "type": "Microsoft.VirtualMachineImages/imageTemplates",
         "apiVersion": "[parameters('api-version')]",
         "location": "[parameters('svclocation')]",
         "dependsOn": [],
         "tags": {
           "imagebuilderTemplate": "win11multi",
           "userIdentity": "enabled"
         },
         "identity": {
           "type": "UserAssigned",
           "userAssignedIdentities": {
             "<imgBuilderId>": {}
           }
         },
         "properties": {
           "buildTimeoutInMinutes": 100,
           "vmProfile": {
             "vmSize": "Standard_DS2_v2",
             "osDiskSizeGB": 127
           },
           "source": {
             "type": "PlatformImage",
             "publisher": "MicrosoftWindowsDesktop",
             "offer": "Windows-11",
             "sku": "win11-21h2-ent",
             "version": "latest"
           },
           "customize": [
             {
               "type": "PowerShell",
               "name": "Install Choco and Vscode",
               "inline": [
                 "Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))",
                 "choco install -y vscode"
               ]
             }
           ],
           "distribute": [
             {
               "type": "SharedImage",
               "galleryImageId": "/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/galleries/<sharedImageGalName>/images/<imageDefName>",
               "runOutputName": "<runOutputName>",
               "artifactTags": {
                 "source": "azureVmImageBuilder",
                 "baseosimg": "win11multi"
               },
               "replicationRegions": ["<region1>", "<region2>"]
             }
           ]
         }
       }
     ]
   }
   ```

1. 运行以下命令，将 template.json 中的占位符替换为特定于 Azure 环境的值：

   ```powershell
   $replRegion2 = 'eastus2'
   $templateFilePath = '.\template.json'
   (Get-Content -Path $templateFilePath -Raw ) -Replace '<subscriptionID>', $subscriptionID | Set-Content -Path $templateFilePath
   (Get-Content -Path $templateFilePath -Raw ) -Replace '<rgName>', $imageResourceGroup | Set-Content -Path $templateFilePath
   (Get-Content -Path $templateFilePath -Raw ) -Replace '<runOutputName>', $runOutputName | Set-Content -Path $templateFilePath
   (Get-Content -Path $templateFilePath -Raw ) -Replace '<imageDefName>', $imageDefName | Set-Content -Path $templateFilePath
   (Get-Content -Path $templateFilePath -Raw ) -Replace '<sharedImageGalName>', $computeGallery | Set-Content -Path $templateFilePath
   (Get-Content -Path $templateFilePath -Raw ) -Replace '<region1>', $location | Set-Content -Path $templateFilePath
   (Get-Content -Path $templateFilePath -Raw ) -Replace '<region2>', $replRegion2 | Set-Content -Path $templateFilePath
   ((Get-Content -Path $templateFilePath -Raw) -Replace '<imgBuilderId>', $identityNameResourceId) | Set-Content -Path $templateFilePath
   ```

1. 运行以下命令，将模板提交到 Azure 映像生成器服务（该服务通过下载任何依赖项目（例如脚本），并将其存储在包括 **IT\_** 前缀的临时资源组中，以生成自定义虚拟机映像）：

   ```powershell
   New-AzResourceGroupDeployment -ResourceGroupName $imageResourceGroup -TemplateFile $templateFilePath -Api-Version "2020-02-14" -imageTemplateName $imageTemplateName -svclocation $location
   ```

1. 运行以下命令以调用映像生成流程：

   ```powershell
   Invoke-AzResourceAction -ResourceName $imageTemplateName -ResourceGroupName $imageResourceGroup -ResourceType Microsoft.VirtualMachineImages/imageTemplates -ApiVersion "2020-02-14" -Action Run -Force
   ```

1. 运行以下命令以确定映像预配状态：

   ```powershell
   Get-AzImageBuilderTemplate -ImageTemplateName $imageTemplateName -ResourceGroupName $imageResourceGroup | Select-Object -Property Name, LastRunStatusRunState, LastRunStatusMessage, ProvisioningState
   ```

   > **注意：** 以下输出表示映像生成流程已成功完成：

   ```powershell
   Name                LastRunStatusRunState LastRunStatusMessage ProvisioningState
   ----                --------------------- -------------------- -----------------
   templateWinVSCode01 Succeeded                                  Succeeded
   ```

1. 或者，如需监视生成进度，可按以下过程操作：

   1. 在 Azure 门户中，搜索并选择**`Image templates`**。
   1. 在**映像模板**页面，选择 **templateWinVSCode01**。
   1. 在 **templateWinVSCode01** 页面中的**Essentials**部分，注意**生成运行状态**条目的值。

   > **注意：** 生成流程可能需要约 30 分钟。 不要等待它完成，可继续执行本练习的下一个任务。

1. 生成完成后，在 Azure 门户中搜索并选择**`Azure compute galleries`**。
1. 在 **Azure compute galleries** 页面，选择 **compute_gallery_01**。
1. 在 **compute_gallery_01** 页面，确保已选择**定义**选项卡，然后在定义列表中选择 **imageDefDevBoxVSCode**。
1. 在 **imageDefDevBoxVSCode** 页面，选择**版本**选项卡，验证列表中显示了 **1.0.0（最新版本）** 条目，且**预配状态**设置为**成功**。
1. 选择**1.0.0（最新版本）** 条目。
1. 在**1.0.0（compute_gallery_01/imageDefDevBoxVSCode/1.0.0）** 页面，查看 VM 映像的版本设置。

### 任务 4：创建 Azure 开发人员中心网络连接

在本任务中，你将配置 Azure 开发中心网络，适用于需要私有连接 访问 Azure 虚拟网络中托管资源的应用场景。 与本实验室第一个练习中使用的 Microsoft 托管网络不同，虚拟网络连接还支持混合应用场景（提供与本地资源的连接），并支持 Azure 开发箱的 Microsoft Entra 混合联接和 Microsoft Entra 加入。

1. 在显示 Azure 门户的 Web 浏览器窗口中，使用**搜索**文本框，搜索并选择**`Virtual networks`**。
1. 在“虚拟网络”页面上，选择“+ 创建”。********
1. 在**创建虚拟网络**页面的**基本信息**选项卡中，指定以下设置并选择**下一页**：

   | 设置        | 值                                                        |
   | -------------- | ------------------------------------------------------------ |
   | 订阅   | 你在此实验室中使用的 Azure 订阅的名称 |
   | 资源组 | **rg-devcenter-01**                                          |
   | 名称           | **vnet-01**                                                  |
   | 位置       | **（美国）美国东部**                                             |

1. 在“**创建虚拟网络**”页面的“**安全**”选项卡上，查看现有设置，保持默认值不变，然后选择“**下一步**”。
1. 在**创建虚拟网络**页面的 **IP 地址**选项卡上，查看现有设置，保持默认值不变，然后选择**查看 + 创建**。
1. 在**创建虚拟网络**页面的**查看 + 创建**选项卡上，选择**创建**。

   > **注意：** 请等到虚拟网络创建完成。 此过程应该会在 1 分钟内完成。

1. 在 Azure 门户的**搜索**文本框中，搜索并选择**`Network connections`**。
1. 在**网络连接**页面上，选择 **+ 创建**。
1. 在**创建网络连接**页面的**基本信息**选项卡上，指定以下设置，然后选择**查看 + 创建**：

   | 设置         | 值                                                        |
   | --------------- | ------------------------------------------------------------ |
   | 订阅    | 你在此实验室中使用的 Azure 订阅的名称 |
   | 资源组  | **rg-devcenter-01**                                          |
   | 名称            | **network-connection-vnet-01**                               |
   | 虚拟网络 | **vnet-01**                                                  |
   | 子网          | **default**                                                  |

1. 在**创建虚拟网络**页面的**查看 + 创建**选项卡上，选择**创建**。

   > **注意：** 等待网络连接创建完成。 这大约需要 1 分钟。

1. 在Azure 门户中，搜索并选择**`Dev centers`**，然后在**开发人员中心**页面上选择 **devcenter-01**。
1. 在 **devcenter-01** 页面左侧的垂直导航菜单中，展开**开发箱配置**部分，然后选择**网络**。
1. 在 **devcenter-01\| 网络** 页面，选择 **+ 添加**。
1. 在**添加网络连接**窗格的**网络连接**下拉列表中，选择**network-connection-vnet-01**，然后选择**添加**。

   > **注意：** 不要等待网络连接创建完成，直接继续执行下一个任务。 添加网络连接可能需要约 1 分钟，

### 任务 5：将映像定义添加到 Azure 开发人员中心项目

在此任务中，你将为本实验室的第一个练习中创建的 Azure 开发人员中心项目添加映像定义。 映像定义将 Azure 市场或自定义映像与可配置任务结合，后者定义了对基础映像的其他修改。 映像定义可用于生成新映像（包含所有更改，如任务应用的更改），或直接创建开发箱池。 创建可重用映像可最大程度地减少开发箱预配时间。

要配置 Microsoft Dev Box 团队的自定义映像，必须启用项目级目录（这部分已在本实验室的第一个练习中完成）。 在此任务中，你将配置项目的目录同步设置。 这将涉及附加包含映像定义文件的目录。

1. 在显示 Azure 门户的 Web 浏览器窗口中，使用**搜索**文本框，搜索并选择**`Dev centers`**。
1. 在**开发人员中心**页面，选择 **devcenter-01**。
1. 在 **devcenter-01** 页面左侧的垂直导航菜单中，展开**管理**部分，然后选择**项目**。
1. 在 **devcenter-01\| 项目** 页面的项目列表中，选择 **devcenter-project-01**。
1. 在 **devcenter-project-01** 页面的左侧垂直导航菜单中，展开**设置**部分，然后选择**目录**。
1. 在 **devcenter-project-01 \| 目录**页面，选择 **+ 添加**。
1. 在**添加目录**窗格中的**名称**文本框中，输入 **`image-definitions-01`**，在**目录源**部分选择**GitHub**，在**身份验证类型**中选择 **GitHub Apps**，保留**自动同步此目录**复选框选，最后选择**使用 GitHub 登录**。
1. 如果系统提示，在**使用 GitHub 登陆**窗口中输入你的 GitHub 凭据，然后选择**登录**。

   > **注意：** 在完成此步骤之前，你需要将 https://github.com/MicrosoftLearning/contoso-co-eShop 存储库分叉到你的 GitHub 帐户。

1. 如果系统提示，请在**授权Microsoft DevCenter** 窗口中选择**授权 Microsoft DevCenter**。
1. 返回**添加目录**窗格，在**存储库**下拉列表中选择 **contoso-co-eShop**，在**分支**下拉列表中接受**默认分支**条目，在**文件夹路径**中输入 **`.devcenter/catalog/image-definitions`**，然后选择**添加**。
1. 返回 **devcenter-project-01 \| 目录**页面，通过监控**状态**列中的条目来验证同步是否成功完成。
1. 在**状态**列中选择**同步成功**链接，查看弹出的通知窗格，验证是否已将 3 个项目添加到目录中，并通过选择右上角的 x**** 符号关闭窗格。
1. 返回 **devcenter-project-01 \| 目录**页面，选择 **image-definitions-01**，验证它是否包含名为 **ContosoBaseImageDefinition**、**backend-eng** 和 **frontend-eng** 的三个条目。

   > **注意：** 出于本实验室的目的，你将测试 **frontend-eng** 映像定义的功能，该定义包含以下内容：

   ```yaml
   $schema: "1.0"
   name: "frontend-eng"
   # Using the "Windows 11 Enterprise 24H2" image as the base
   image: microsoftwindowsdesktop_windows-ent-cpc_win11-24H2-ent-cpc
   description: "This definition is for the eShop frontend engineering environment"

   tasks:
     - name: ~/winget
       description: Install Visual Studio Code
       parameters:
         package: Microsoft.VisualStudioCode
         runAsUser: true
   ```

1. 在Azure 门户中，导航到 **devcenter-project-01** 页面左侧的垂直导航菜单中，展开**管理**部分，选择**映像定义**，并验证页面是否显示你在此任务中之前识别的3 个相同的映像定义。

### 任务 6：创建自定义开发箱池

在此任务中，你将使用新预配的映像定义来创建开发箱池。 该池还将利用你在本练习前面设置的网络连接。

1. 在显示 **devcenter-project-01 \| 映像定义**页的Azure 门户左侧的垂直导航菜单中，在**管理**部分，选择**开发箱池**。
1. 在 **devcenter-project-01 \| 开发箱池** 页上，选择“ **+ 创建**”。
1. 在“**创建开发箱池**”页的“**基本信息**”选项卡上，指定以下设置，然后选择“**创建**”：

   | 设置                                                                                                           | 值                                                 |
   | ----------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------- |
   | 名称                                                                                                              | **devcenter-project-01-devbox-pool-02**               |
   | 定义                                                                                                        | **frontend-eng**                                      |
   | 计算                                                                                                           | **8 vCPU，32 GB RAM**                                 |
   | 存储                                                                                                           | **256 GB SSD**                                        |
   | 网络连接                                                                                                | **部署到组织中的网络连接** |
   | 网络连接名                                                                                           | **network-connection-vnet-01**                        |
   | 启用单一登录                                                                                             | 已启用                                               |
   | Dev Box 创建者特权                                                                                        | **本地管理员**                               |
   | 按计划启用自动停止                                                                                      | 已启用                                               |
   | 停止时间                                                                                                         | **下午 07:00**                                          |
   | 时区                                                                                                         | 当前时区                                |
   | 在断开连接时启用休眠                                                                                    | 已启用                                               |
   | 宽限期（分钟）                                                                                           | **60**                                                |
   | 我确认组织有 Azure 混合权益许可证，这将应用于此池中的所有开发箱 | 已启用                                               |

   > **备注：** 等待创建开发箱池。 这可能需要大约 2 分钟。

### 任务 7：评估自定义开发箱

在此任务中，你将使用 Microsoft Entra 开发人员用户帐户评估自定义开发箱功能。

> **注意：** 由于完成此任务需要额外时间，因此其完成是可选的。

1. 在 Web 浏览器中启用隐身/专用模式，并导航到 `https://aka.ms/devbox-portal` 的 Microsoft Dev Box 开发人员门户。
1. 出现登录提示时，提供 **devuser01** 用户帐户的凭据。
1. 在 Microsoft Dev Box 开发人员门户的 **欢迎，devuser01** 页面上，选择 **+ 新建开发箱**。
1. 在**添加开发箱**窗格的**名称**文本框中，输入 **`devuser01box02`**。
1. 在**开发箱池**下拉列表中，选择 **devcenter-project-01-devbox-pool-02**。
1. 查看**添加开发箱**窗格中显示的其他信息，包括项目名、开发箱池规格、休眠支持状态以及计划关闭时间。 此外，请注意应用自定义项的选项，以及开发箱创建可能需要最多 65 分钟的通知。
1. 在**添加开发箱**窗格中，选择**创建**。
1. 开发箱创建完成并运行后，选择**通过应用连接**选项进行连接。
1. 在弹出的**此站点正尝试打开 Microsoft 远程连接中心**窗口中，选择**打开**。 这会自动发起与开发箱的远程桌面会话。
1. 系统提示输入凭据时，提供 **devuser01** 帐户的用户名和密码以完成身份验证。
1. 在与开发箱的远程桌面会话中，验证配置中是否已安装 Visual Studio Code。

   > **注意：** 你还可以从**你的开发箱**界面中选择省略号符号，然后从级联菜单中选择**自定义项**来验证自定义结果。
