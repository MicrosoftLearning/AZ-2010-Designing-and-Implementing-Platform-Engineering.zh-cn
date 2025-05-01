---
lab:
  title: 使用 Bicep 实现自助服务基础结构
  module: Strategic Platform Road Mapping
---

# 实验室 03：使用 Bicep 实现自助服务基础结构

## 预计用时：20 分钟

## 先决条件

- Azure 订阅 - 如果没有 Azure 订阅，请创建一个[免费帐户](https://azure.microsoft.com/free/)。
- Azure 服务与 Azure CLI 的基础知识
- 安装有 Bicep 扩展的 Visual Studio Code。
- 已安装并配置到本地计算机的 Azure CLI。
- 熟悉基础结构即代码 (IaC) 概念。

## 目标

- 在环境中设置 Bicep。
- 创建 Bicep 模板以定义 Azure 资源。
- 使用 SQL 数据库后端部署 Azure 应用服务
- 使用标记和策略实施治理。
- 使用 Bicep 实现自动缩放。

## 练习 1：创建 Bicep 模板以部署 Azure 资源

在平台工程环境中，开发人员需要一种一致且高效的方式来预配基础结构。 本实验室将指导你使用基础结构即代码 （IaC） 工具 Bicep，以自助服务方式部署和管理 Azure 资源。 你将创建一个 Bicep 模板，以预配资源组、Azure 应用服务、Azure SQL 数据库和存储帐户，同时使用标记和策略实现治理控制。

### 任务 1：安装 Bicep CLI

1. 打开本地终端。
1. 要验证是否已安装 Bicep，请运行：

   ```bash
   az bicep version
   ```

   如果 Bicep 未安装，请按以下步骤安装：

   ```bash
   az bicep install
   ```

1. 运行以下内容，确认安装：

   ```bash
   az bicep version
   ```

   应显示类似于 Bicep CLI 版本 X.Y.Z 的输出。

   > **注意：** 请确保已安装 [Azure CLI](https://learn.microsoft.com/cli/azure/install-azure-cli)。

### 任务 2：创建 Bicep 模板

1. 在本地终端中，创建新的文件：

   ```bash
   code main.bicep
   ```

1. 将以下 Bicep 代码复制并粘贴到文件中：

   ```bicep
   param location string = 'eastus2'
   param appServicePlanName string = 'bicepAppPlan'
   param webAppName string = 'bicep-webapp'
   param storageAccountName string = 'biceplabstorage'
   param sqlServerName string = 'bicep-sqlserver'
   param sqlDatabaseName string = 'bicepdb'
   param sqlAdminUser string
   @secure()
   param sqlAdminPassword string

   targetScope = 'resourceGroup'

   resource storage 'Microsoft.Storage/storageAccounts@2021-09-01' = {
     name: storageAccountName
     location: location
     sku: {
       name: 'Standard_LRS'
     }
     kind: 'StorageV2'
   }

   resource appPlan 'Microsoft.Web/serverfarms@2021-02-01' = {
     name: appServicePlanName
     location: location
     sku: {
       name: 'F1'
       tier: 'Free'
     }
   }

   resource webApp 'Microsoft.Web/sites@2021-02-01' = {
     name: webAppName
     location: location
     properties: {
       serverFarmId: appPlan.id
     }
   }

   resource sqlServer 'Microsoft.Sql/servers@2022-05-01-preview' = {
     name: sqlServerName
     location: location
     properties: {
       administratorLogin: sqlAdminUser
       administratorLoginPassword: sqlAdminPassword
       version: '12.0'
     }
   }

   resource sqlDb 'Microsoft.Sql/servers/databases@2022-05-01-preview' = {
     name: sqlDatabaseName
     location: location
     parent: sqlServer
     properties: {
       collation: 'SQL_Latin1_General_CP1_CI_AS'
       maxSizeBytes: 2147483648
     }
   }
   ```

   > **备注：** 你可以在 Visual Studio Code 中安装 Bicep 扩展，以获取 Bicep 文件的语法高亮和 IntelliSense 支持。

   > **重要说明：** 确保 storageAccountName、webAppName 和 sqlServerName 在全局范围内唯一。 如果因名称冲突导致部署失败，请相应修改名称。

   > **备注：** 如果遇到与该区域相关的错误，请尝试更改 `location` 参数值，部署到其他区域。

1. 保存并关闭该文件。

### 任务 3：使用 Azure CLI 部署模板

1. 运行以下命令创建资源组：

   ```bash
   az group create --name BicepLab-RG --location centralus

   ```

   > **备注：** 如果尚未进行身份验证，可能需要登录 Azure 帐户。 有关详细说明，请参阅[使用 Azure CLI 进行 Azure 身份验证](https://learn.microsoft.com/cli/azure/authenticate-azure-cli)。

2. 将模板部署到资源组：

   ```bash
   az deployment group create --resource-group BicepLab-RG --template-file main.bicep --parameters sqlAdminUser='azureuser' sqlAdminPassword='YourSecurePassword123!'

   ```

   > **注意：** 替换 YourSecurePassword123！ 使用强密码和有效的用户名 azureuser。

3. 等待部署完成。 你会看到一条确认消息。
4. 在 Azure 门户中，导航到“资源组”。
5. 找到 BicepLab-RG 并单击它。
6. 验证是否已成功创建资源。

你已成功使用 Bicep 模板，通过SQL 数据库后端部署了 Azure 应用服务。 现在，你可以将基础结构作为代码进行管理，并一致高效地部署资源。 下一步是将此模板集成到 CI/CD 管道中，实现自动化部署。

## 练习 2：使用标记和策略实施治理。

在自助服务的基础结构环境中，实施治理以确保合规性与成本控制至关重要。 标记和策略是实现此目的的两个关键机制。

### 任务 1：将标记添加到资源

1. 运行以下命令，为资源组添加标记：

   ```bash
   az group update --name BicepLab-RG --set tags.Owner='PlatformEngineering'
   ```

1. 运行以验证标记是否已添加：

   ```bash
   az group show --name BicepLab-RG --query tags
   ```

### 任务 2：创建策略以实施标记

1. 创建 JSON 策略文件：

   ```bash
   code tagging-policy.json
   ```

1. 将以下策略定义复制并粘贴到文件中：

   ```json
   {
     "if": {
       "not": {
         "field": "tags[Owner]",
         "exists": "true"
       }
     },
     "then": {
       "effect": "deny"
     }
   }
   ```

1. 使用 JSON 文件创建策略定义：

   ```bash
    az policy definition create --name 'tagging-policy' --display-name 'Enforce tagging' --rules @tagging-policy.json --mode All
   ```

1. 将策略分配给资源组：

   ```bash
   az policy assignment create --name 'tagging-policy-assignment' --display-name 'Enforce tagging' --policy "/subscriptions/<subscription-id>/providers/Microsoft.Authorization/policyDefinitions/tagging-policy" --scope "/subscriptions/<subscription-id>/resourceGroups/BicepLab-RG"

   ```

   > **重要说明：** 将两个 <subscription-id> 替换为你的 Azure 订阅 ID。

1. 运行以验证策略是否已分配：

   ```bash
   az policy assignment list --output table
   ```

### 任务 3：测试策略强制实施

1. 如需从资源组中移除标记，请运行以下命令：

   ```bash
   az tag update --resource-id /subscriptions/<subscription-id>/resourceGroups/BicepLab-RG --operation delete --tags Owner
   ```

   > **重要说明：** 将 <subscription-id> 替换为你的 Azure 订阅 ID。

1. 你应该会看到一条错误消息，提示由于策略强制措施，操作被拒绝。 不应移除标记。 这表明策略按预期发挥作用。 要解决此错误，请暂时移除策略分配并重新运行命令。

   ```bash
   az policy exemption create --name 'tagging-policy-exemption' --policy-assignment "/subscriptions/<subscription-id>/resourceGroups/BicepLab-RG/providers/Microsoft.Authorization/policyAssignments/tagging-policy-assignment" --scope "/subscriptions/<subscription-id>/resourceGroups/BicepLab-RG" --display-name "Temporary exemption to remove tag" --exemption-category Waiver
   ```

   > **重要说明：** 将两个 <subscription-id> 替换为你的 Azure 订阅 ID。

你已成功使用标记和策略实施治理。 开发人员现在必须使用“所有者”标记来标记资源，以确保正确的所有权和责任归属。 可以创建其他策略来实施其他治理要求，例如资源命名约定、资源类型和资源位置。

## 练习 3：使用 Bicep 实现自动缩放

在平台工程环境中，确保应用程序能够高效缩放是一个关键需求。 自动化缩放能够提升性能、优化资源利用并最大程度地降低成本。 在本练习中，你将使用 Bicep 为Azure 应用服务实现自动缩放。

### 任务 1：在 Bicep 中定义自动缩放策略

1. 打开 main.bicep 文件，找到现有的应用服务计划部分：

   ```bicep
   resource appPlan 'Microsoft.Web/serverfarms@2021-02-01' = {
     name: appServicePlanName
     location: location
     sku: {
       name: 'F1'
       tier: 'Free'
     }
   }
   ```

1. 修改 appPlan 资源，以使用 S1 SKU（支持自动缩放）：

   ```bicep
   resource appPlan 'Microsoft.Web/serverfarms@2021-02-01' = {
     name: appServicePlanName
     location: location
     sku: {
       name: 'S1'
       tier: 'Standard'
     }
     properties: {
       perSiteScaling: false
       maximumElasticWorkerCount: 10
     }
   }
   ```

1. 在此资源之后，立即添加 autoscaleSetting 配置：

   ```bicep
   resource autoscaleSetting 'Microsoft.Insights/autoscaleSettings@2024-01-01-preview' = {
   name: 'autoscale-rule'
   location: location
   properties: {
    profiles: [
      {
        name: 'defaultProfile'
        capacity: {
          minimum: '1'
          maximum: '5'
          default: '1'
        }
        rules: [
          {
            metricTrigger: {
              metricName: 'CpuPercentage'
              metricResourceUri: resourceId('Microsoft.Web/serverfarms', appServicePlanName)
              operator: 'GreaterThan'
              threshold: 75
              timeAggregation: 'Average'
              statistic: 'Average'
              timeWindow: 'PT5M'
              timeGrain: 'PT1M'
            }
            scaleAction: {
              direction: 'Increase'
              type: 'ChangeCount'
              value: '1'
              cooldown: 'PT1M'
            }
          }
        ]
      }
    ]
   }
   }
   ```

1. 保存文件。
1. 验证已更新的 Bicep 文件：

   ```bash
   az bicep build --file main.bicep
   ```

1. 部署已更新的模板：

   ```bash
   az deployment group create --resource-group BicepLab-RG --template-file main.bicep --parameters sqlAdminUser='azureuser' sqlAdminPassword='YourSecurePassword123!'

   ```

1. 验证自动缩放策略是否已应用于应用服务计划。 转到 Azure 门户，导航到“应用服务计划”，选择“横向扩展”（应用服务计划）边栏选项卡，验证是否已配置自动缩放规则。

   > **备注：** 如果想测试自动缩放的行为，可以通过运行负载测试或生成流量，以模拟应用服务的高 CPU 使用率。 应用服务应根据已定义的规则自动横向扩展。

你已成功使用 Bicep 为 Azure 应用服务实现自动缩放。 这将确保应用程序能够高效处理增加的流量和需求，以提高性能并降低成本。
