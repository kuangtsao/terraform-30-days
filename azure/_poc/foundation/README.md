如果直接使用的話會直接死掉，原因是因為 provider 的 azurerm version 原先的太舊。  
會出現類似像這樣的 log：
```
2023-10-05T02:18:28.727+0800 [DEBUG] provider.terraform-provider-azurerm_v2.65.0_x5: AzureRM Request: 
GET /subscriptions/[YOU CANT SEE ME]/providers?api-version=2016-02-01 HTTP/1.1
Host: management.azure.com
User-Agent: Go/go1.16.3 (amd64-linux) go-autorest/v14.2.1 Azure-SDK-For-Go/v55.3.0 resources/2016-02-01 HashiCorp Terraform/1.5.7 (+https://www.terraform.io) Terraform Plugin SDK/2.6.1 terraform-provider-azurerm/2.65.0 pid-222c6c49-1b0a-5959-a213-6608f9eb8820
X-Ms-Correlation-Request-Id: be378667-213e-f351-65fd-5bb4f0c05d6e
Accept-Encoding: gzip: timestamp=2023-10-05T02:18:28.726+0800
2023-10-05T02:18:28.956+0800 [DEBUG] provider.terraform-provider-azurerm_v2.65.0_x5: Request to https://management.azure.com/subscriptions/[YOU CANT SEE ME]/providers?api-version=2016-02-01 completed with no response: timestamp=2023-10-05T02:18:28.956+0800
2023-10-05T02:18:29.537+0800 [TRACE] dag/walk: vertex "azurerm_storage_container.main (expand)" is waiting for "azurerm_storage_account.main (expand)"
2023-10-05T02:18:29.537+0800 [TRACE] dag/walk: vertex "azurerm_resource_group.rg (expand)" is waiting for "provider[\"registry.terraform.io/hashicorp/azurerm\"]"
2023-10-05T02:18:29.537+0800 [TRACE] dag/walk: vertex "provider[\"registry.terraform.io/hashicorp/azurerm\"] (close)" is waiting for "azurerm_storage_container.main (expand)"
2023-10-05T02:18:29.537+0800 [TRACE] dag/walk: vertex "root" is waiting for "provider[\"registry.terraform.io/hashicorp/azurerm\"] (close)"
2023-10-05T02:18:29.537+0800 [TRACE] dag/walk: vertex "output.resource_group_name (expand)" is waiting for "azurerm_resource_group.rg (expand)"
2023-10-05T02:18:29.537+0800 [TRACE] dag/walk: vertex "output.storage_container_name (expand)" is waiting for "azurerm_storage_container.main (expand)"
2023-10-05T02:18:29.537+0800 [TRACE] dag/walk: vertex "output.storage_account_name (expand)" is waiting for "azurerm_storage_account.main (expand)"
2023-10-05T02:18:29.747+0800 [TRACE] dag/walk: vertex "azurerm_storage_account.main (expand)" is waiting for "azurerm_resource_group.rg (expand)"
2023-10-05T02:18:34.538+0800 [TRACE] dag/walk: vertex "output.storage_account_name (expand)" is waiting for "azurerm_storage_account.main (expand)"
2023-10-05T02:18:34.538+0800 [TRACE] dag/walk: vertex "root" is waiting for "provider[\"registry.terraform.io/hashicorp/azurerm\"] (close)"
2023-10-05T02:18:34.538+0800 [TRACE] dag/walk: vertex "output.resource_group_name (expand)" is waiting for "azurerm_resource_group.rg (expand)"
2023-10-05T02:18:34.538+0800 [TRACE] dag/walk: vertex "provider[\"registry.terraform.io/hashicorp/azurerm\"] (close)" is waiting for "azurerm_storage_container.main (expand)"
2023-10-05T02:18:34.538+0800 [TRACE] dag/walk: vertex "azurerm_resource_group.rg (expand)" is waiting for "provider[\"registry.terraform.io/hashicorp/azurerm\"]"
2023-10-05T02:18:34.538+0800 [TRACE] dag/walk: vertex "output.storage_container_name (expand)" is waiting for "azurerm_storage_container.main (expand)"
2023-10-05T02:18:34.538+0800 [TRACE] dag/walk: vertex "azurerm_storage_container.main (expand)" is waiting for "azurerm_storage_account.main (expand)"
2023-10-05T02:18:34.749+0800 [TRACE] dag/walk: vertex "azurerm_storage_account.main (expand)" is waiting for "azurerm_resource_group.rg (expand)"
2023-10-05T02:18:39.539+0800 [TRACE] dag/walk: vertex "azurerm_storage_container.main (expand)" is waiting for "azurerm_storage_account.main (expand)"
2023-10-05T02:18:39.540+0800 [TRACE] dag/walk: vertex "output.storage_container_name (expand)" is waiting for "azurerm_storage_container.main (expand)"
2023-10-05T02:18:39.540+0800 [TRACE] dag/walk: vertex "azurerm_resource_group.rg (expand)" is waiting for "provider[\"registry.terraform.io/hashicorp/azurerm\"]"
2023-10-05T02:18:39.540+0800 [TRACE] dag/walk: vertex "provider[\"registry.terraform.io/hashicorp/azurerm\"] (close)" is waiting for "azurerm_storage_container.main (expand)"
2023-10-05T02:18:39.540+0800 [TRACE] dag/walk: vertex "output.storage_account_name (expand)" is waiting for "azurerm_storage_account.main (expand)"
2023-10-05T02:18:39.540+0800 [TRACE] dag/walk: vertex "root" is waiting for "provider[\"registry.terraform.io/hashicorp/azurerm\"] (close)"
2023-10-05T02:18:39.540+0800 [TRACE] dag/walk: vertex "output.resource_group_name (expand)" is waiting for "azurerm_resource_group.rg (expand)"
2023-10-05T02:18:39.749+0800 [TRACE] dag/walk: vertex "azurerm_storage_account.main (expand)" is waiting for "azurerm_resource_group.rg (expand)"
```

觀察方式：
```
$ export TF_LOG=trace
```
就可以看到 `terraform plan` 會開始秀出，如果要稍微記錄下這些 Log 到公開的任何地方的話，記得把 subscription
 token 遮起來，不然就可以拿來挖礦了 XD

解決方法：將 provider 的 azurerm version 調高，可以不用到最新的， 只要讓 `terraform plan` 不會卡太久就行了。
