---
title: Použití rozšíření se fondy Batch
description: Rozšíření jsou malé aplikace, které usnadňují konfiguraci po zřízení a nastavení na výpočetních uzlech Batch.
ms.topic: how-to
ms.date: 02/10/2021
ms.openlocfilehash: 1bf9847af57347c143ee3d790d89988ba7cd48e4
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100417169"
---
# <a name="use-extensions-with-batch-pools"></a>Použití rozšíření se fondy Batch

Rozšíření jsou malé aplikace, které usnadňují konfiguraci po zřízení a nastavení na výpočetních uzlech Batch. Můžete vybrat libovolná rozšíření, která jsou povolena nástrojem Azure Batch a jejich instalace na výpočetních uzlech, jak jsou zřízeny. Potom může rozšíření provést zamýšlenou operaci.

Můžete kontrolovat stav živého rozšíření, která používáte, a načíst informace, které vrátí, aby bylo možné provést všechny možnosti detekce, oprav nebo diagnostiky.

## <a name="prerequisites"></a>Požadavky

- Fondy s rozšířeními musí používat [konfiguraci virtuálního počítače](nodes-and-pools.md#virtual-machine-configuration).
- Typ rozšíření CustomScript je vyhrazený pro službu Azure Batch a nedá se přepsat.

### <a name="supported-extensions"></a>Podporovaná rozšíření

Při vytváření fondu Batch se teď dají nainstalovat tato rozšíření. 

- Azure Key Vault rozšíření pro [Linux](../virtual-machines/extensions/key-vault-linux.md) i [Windows](../virtual-machines/extensions/key-vault-windows.md)
- Log Analytics a monitorovací rozšíření pro [Linux](../virtual-machines/extensions/oms-linux.md) i [Windows](../virtual-machines/extensions/oms-windows.md)
- Sada zabezpečení Azure

Můžete požádat o podporu dalších vydavatelů nebo typů rozšíření otevřením žádosti o podporu.

## <a name="create-a-pool-with-extensions"></a>Vytvoření fondu s rozšířeními

Následující příklad vytvoří fond dávkových uzlů pro Linux, který používá rozšíření Azure Key Vault.

Identifikátor URI v REST API

```http
 PUT https://management.azure.com/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.Batch/batchAccounts/<batchaccountName>/pools/<batchpoolName>?api-version=2021-01-01
```

Text požadavku

```json
{
    "name": "test1",
    "type": "Microsoft.Batch/batchAccounts/pools",
    "properties": {
        "vmSize": "STANDARD_DS2_V2",
        "taskSchedulingPolicy": {
            "nodeFillType": "Pack"
        },
        "deploymentConfiguration": {
            "virtualMachineConfiguration": {
                "imageReference": {
                    "publisher": "canonical",
                    "offer": "ubuntuserver",
                    "sku": "18.04-lts",
                    "version": "latest"
                },
                "nodeAgentSkuId": "batch.node.ubuntu 18.04",
                "extensions": [
                    {
                        "name": "secretext",
                        "type": "KeyVaultForLinux",
                        "publisher": "Microsoft.Azure.KeyVault",
                        "typeHandlerVersion": "1.0",
                        "autoUpgradeMinorVersion": true,
                        "settings": {
                            "secretsManagementSettings": {
                                "pollingIntervalInS": "300",
                                "certificateStoreLocation": "/var/lib/waagent/Microsoft.Azure.KeyVault",
                                "requireInitialSync": true,
                                "observedCertificates": [
                                    "https://testkvwestus2.vault.azure.net/secrets/authsecreat"
                                ]
                            },
                            "authenticationSettings": {
                                "msiEndpoint": "http://169.254.169.254/metadata/identity",
                                "msiClientId": "885b1a3d-f13c-4030-afcf-9f05044d78dc"
                            }
                        },
                        "protectedSettings":{}
                    }
                ]
            }
        },
        "scaleSettings": {
            "fixedScale": {
                "targetDedicatedNodes": 1,
                "targetLowPriorityNodes": 0,
                "resizeTimeout": "PT15M"
            }
        }
```

## <a name="get-extension-data-from-a-pool"></a>Získat data rozšíření z fondu

Následující příklad načte data z rozšíření Azure Key Vault.

Identifikátor URI v REST API

```http
 GET https://<accountname>.<region>.batch.azure.com/pools/test3/nodes/tvmps_a3ce79db285d6c124399c5bd3f3cf308d652c89675d9f1f14bfc184476525278_d/extensions/secretext?api-version=2010-01-01
```

Tělo odpovědi

```json
{
  "odata.metadata":"https://testwestus2batch.westus2.batch.azure.com/$metadata#extensions/@Element","instanceView":{
    "name":"secretext","statuses":[
      {
        "code":"ProvisioningState/succeeded","level":0,"displayStatus":"Provisioning succeeded","message":"Successfully started Key Vault extension service. 2021-02-08T19:49:39Z"
      }
    ]
  },"vmExtension":{
    "name":"KVExtensions","publisher":"Microsoft.Azure.KeyVault","type":"KeyVaultForLinux","typeHandlerVersion":"1.0","autoUpgradeMinorVersion":true,"settings":"{\r\n  \"secretsManagementSettings\": {\r\n    \"pollingIntervalInS\": \"300\",\r\n    \"certificateStoreLocation\": \"/var/lib/waagent/Microsoft.Azure.KeyVault\",\r\n    \"requireInitialSync\": true,\r\n    \"observedCertificates\": [\r\n      \"https://testkvwestus2.vault.azure.net/secrets/testumi\"\r\n    ]\r\n  },\r\n  \"authenticationSettings\": {\r\n    \"msiEndpoint\": \"http://169.254.169.254/metadata/identity\",\r\n    \"msiClientId\": \"885b1a3d-f13c-4030-afcf-922f05044d78dc\"\r\n  }\r\n}"
  }
}

```

## <a name="next-steps"></a>Další kroky

- Přečtěte si o různých způsobech [kopírování aplikací a dat do uzlů fondu](batch-applications-to-pool-nodes.md).
- Přečtěte si další informace o práci s [uzly a fondy](nodes-and-pools.md).
