---
title: Vytvoření clusteru Service Fabric se systémem Windows v Azure
description: V tomto kurzu se dozvíte, jak nasadit cluster Windows Service Fabric do virtuální sítě Azure a skupiny zabezpečení sítě pomocí Prostředí PowerShell.
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc
ms.openlocfilehash: 086379e788966b300f988e06ec42c94b880b8281
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "75551705"
---
# <a name="tutorial-deploy-a-service-fabric-cluster-running-windows-into-an-azure-virtual-network"></a>Kurz: Nasazení clusteru Service Fabric se systémem Windows do virtuální sítě Azure

Tento kurz je první částí série. Pomocí PowerShellu a šablony se dozvíte, jak nasadit cluster Azure Service Fabric se systémem Windows do skupiny zabezpečení [virtuální sítě Azure](../virtual-network/virtual-networks-overview.md) a [sítě.](../virtual-network/virtual-networks-nsg.md) Po dokončení máte cluster spuštěný v cloudu, do kterého můžete nasadit aplikace. Pokud chcete vytvořit linuxový cluster, který používá Azure CLI, přečtěte si informace [o vytvoření zabezpečeného linuxového clusteru v Azure](service-fabric-tutorial-create-vnet-and-linux-cluster.md).

Tento kurz popisuje produkční scénář. Pokud chcete vytvořit menší cluster pro účely testování, přečtěte si informace [o vytvoření testovacího clusteru](./scripts/service-fabric-powershell-create-secure-cluster-cert.md).

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření virtuální sítě v Azure pomocí PowerShellu
> * Vytvoření trezoru klíčů a nahrání certifikátu
> * Nastavení ověřování služby Azure Active Directory
> * Konfigurace kolekce diagnostiky
> * Nastavení služby EventStore
> * Nastavení protokolů Azure Monitoru
> * Vytvoření zabezpečeného clusteru Service Fabric v Azure PowerShellu
> * Zabezpečit cluster pomocí certifikátu X.509
> * Připojení ke clusteru pomocí prostředí PowerShell
> * Odebrat cluster

V této sérii kurzů se naučíte:
> [!div class="checklist"]
> * Vytvoření zabezpečeného clusteru v Azure
> * [Sledování clusteru](service-fabric-tutorial-monitor-cluster.md)
> * [Horizontální snížení nebo navýšení kapacity clusteru](service-fabric-tutorial-scale-cluster.md)
> * [Upgrade modulu runtime clusteru](service-fabric-tutorial-upgrade-cluster.md)
> * [Odstranění clusteru](service-fabric-tutorial-delete-cluster.md)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky

Než začnete s tímto kurzem:

* Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Nainstalujte [service fabric sdchartu a modul Prostředí PowerShell](service-fabric-get-started.md).
* Nainstalujte [Azure Powershell](https://docs.microsoft.com/powershell/azure/install-Az-ps).
* Projděte si klíčové koncepty [clusterů Azure](service-fabric-azure-clusters-overview.md).
* [Naplánujte a připravte se](service-fabric-cluster-azure-deployment-preparation.md) na nasazení produkčního clusteru.

Následující postupy vytvoří cluster prostředků service fabric se sedmi uzlem. Pomocí [cenové kalkulačky Azure](https://azure.microsoft.com/pricing/calculator/) můžete vypočítat náklady vzniklé spuštěním clusteru Service Fabric v Azure.

## <a name="download-and-explore-the-template"></a>Stažení a prozkoumání šablony

Stáhněte si následující soubory šablon Azure Resource Manager:

* [azuredeploy.json][template]
* [azuredeploy.parameters.json][parameters]

Tato šablona nasazuje zabezpečený cluster sedmi virtuálních počítačů a tří typů uzlů do virtuální sítě a skupiny zabezpečení sítě.  Další ukázkové šablony najdete na [GitHubu](https://github.com/Azure-Samples/service-fabric-cluster-templates). [Azuredeploy.json][template] nasazuje řadu prostředků, včetně následujících.

### <a name="service-fabric-cluster"></a>Cluster Service Fabric

V prostředku **Microsoft.ServiceFabric/clusters** se konfiguruje cluster s Windows s těmito charakteristikami:

* Tři typy uzlů.
* Pět uzlů v typu primárního uzlu (konfigurovatelné v parametrech šablony) a jeden uzel v každém z ostatních dvou typů uzlů.
* OS: Windows Server 2016 Datacenter s kontejnery (konfigurovatelné v parametrech šablony).
* Certifikát zabezpečen (konfigurovatelný v parametrech šablony).
* [Reverzní proxy server](service-fabric-reverseproxy.md) je povolen.
* [Služba DNS](service-fabric-dnsservice.md) je povolena.
* [Úroveň odolnosti](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) bronzu (konfigurovatelná v parametrech šablony).
* [Úroveň spolehlivosti](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) silver (konfigurovatelné v parametrech šablony).
* Koncový bod připojení klienta: 19000 (konfigurovatelný v parametrech šablony).
* Koncový bod http brány: 19080 (konfigurovatelný v parametrech šablony).

### <a name="azure-load-balancer"></a>Azure Load Balancer

V prostředku **Microsoft.Network/loadBalancers** je nakonfigurován systém vyrovnávání zatížení. Sondy a pravidla jsou nastaveny pro následující porty:

* Koncový bod připojení klienta: 19000
* koncový bod brány HTTP: 19080
* Aplikační port: 80
* Aplikační port: 443
* reverzní proxy server Service Fabric: 19081

Pokud jsou potřeba další porty aplikace, budete muset upravit prostředek **Microsoft.Network/loadBalancers** a prostředek **Microsoft.Network/networkSecurityGroups,** aby byl povolen provoz.

### <a name="virtual-network-subnet-and-network-security-group"></a>Virtuální síť, podsíť a skupina zabezpečení sítě

Názvy virtuální sítě, podsítě a skupiny zabezpečení sítě jsou deklarované v parametrech šablony. Adresní prostory virtuální sítě a podsítě se taky deklarují v parametrech šablony a konfigurují v prostředku **Microsoft.Network/virtualNetworks**:

* Adresní prostor virtuální sítě: 172.16.0.0/20
* adresní prostor podsítě Service Fabric: 172.16.2.0/23

V prostředku **Microsoft.Network/networkSecurityGroups** jsou povolená následující pravidla pro příchozí provoz. Hodnoty portů můžete změnit změnou proměnných šablony.

* ClientConnectionEndpoint (TCP): 19000
* HttpGatewayEndpoint (HTTP/TCP): 19080
* SMB: 445
* Internodekomunikace: 1025, 1026, 1027
* Rozsah dočasných portů: 49152 až 65534 (potřebujete minimálně 256 portů).
* Porty pro použití aplikací: 80 a 443
* Rozsah aplikačního portu: 49152 až 65534 (používá se pro komunikaci mezi službami. Ostatní porty nejsou otevřeny na vyrovnávání zatížení).
* Všechny ostatní porty jsou blokované

Pokud jsou potřeba další porty aplikace, budete muset upravit prostředek **Microsoft.Network/loadBalancers** a prostředek **Microsoft.Network/networkSecurityGroups,** aby byl povolen provoz.

### <a name="windows-defender"></a>Windows Defender
Ve výchozím nastavení je [antivirový program programu Windows Defender](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-on-windows-server-2016) nainstalován a funkční v systému Windows Server 2016. Uživatelské rozhraní je ve výchozím nastavení nainstalováno na některých sku, ale není vyžadováno. Pro každý typ uzlu nebo škálovací sadu virtuálních počítače deklarovanou v šabloně se [rozšíření Azure VM Antimalware](/azure/virtual-machines/extensions/iaas-antimalware-windows) používá k vyloučení adresářů a procesů Service Fabric:

```json
{
"name": "[concat('VMIaaSAntimalware','_vmNodeType0Name')]",
"properties": {
        "publisher": "Microsoft.Azure.Security",
        "type": "IaaSAntimalware",
        "typeHandlerVersion": "1.5",
        "settings": {
        "AntimalwareEnabled": "true",
        "Exclusions": {
                "Paths": "D:\\SvcFab;D:\\SvcFab\\Log;C:\\Program Files\\Microsoft Service Fabric",
                "Processes": "Fabric.exe;FabricHost.exe;FabricInstallerService.exe;FabricSetup.exe;FabricDeployer.exe;ImageBuilder.exe;FabricGateway.exe;FabricDCA.exe;FabricFAS.exe;FabricUOS.exe;FabricRM.exe;FileStoreService.exe"
        },
        "RealtimeProtectionEnabled": "true",
        "ScheduledScanSettings": {
                "isEnabled": "true",
                "scanType": "Quick",
                "day": "7",
                "time": "120"
        }
        },
        "protectedSettings": null
}
}
```

## <a name="set-template-parameters"></a>Nastavení parametrů šablony

Soubor s parametry [azuredeploy.parameters.json][parameters] deklaruje mnoho hodnot používaných pro nasazení clusteru a přidružených prostředků. Níže jsou uvedeny parametry, které je třeba upravit pro vaše nasazení:

**Parametr** | **Příklad hodnoty** | **Poznámky** 
|---|---|---|
|adminUserName|vmadmin| Uživatelské jméno správce pro virtuální počítače clusteru. [Požadavky na uživatelské jméno pro virtuální hod](https://docs.microsoft.com/azure/virtual-machines/windows/faq#what-are-the-username-requirements-when-creating-a-vm). |
|adminPassword|Password#1234| Heslo správce pro virtuální počítače clusteru. [Požadavky na heslo pro virtuální počítače](https://docs.microsoft.com/azure/virtual-machines/windows/faq#what-are-the-password-requirements-when-creating-a-vm).|
|clusterName|mysfcluster123| Název clusteru. Může obsahovat jenom písmena a číslice. Může mít délku 3 až 23 znaků.|
|location|southcentralus| Umístění clusteru. |
|certificateThumbprint|| <p>Pokud vytváříte certifikát podepsaný svým držitelem nebo poskytujete soubor certifikátu, měla by být hodnota prázdná.</p><p>Pokud chcete použít existující certifikát, který se dříve odeslal do trezoru klíčů, vyplňte hodnotu kryptografického otisku certifikátu SHA1. Příklad: „6190390162C988701DB5676EB81083EA608DCCF3“.</p> |
|certificateUrlValue|| <p>Pokud vytváříte certifikát podepsaný svým držitelem nebo poskytujete soubor certifikátu, měla by být hodnota prázdná. </p><p>Pokud chcete použít existující certifikát, který byl dříve odeslán do trezoru klíčů, vyplňte URL certifikátu. Například "https:\//mykeyvault.vault.azure.net:443/secrets/mycertificate/02bea722c9ef4009a76c5052bcbf8346".</p>|
|sourceVaultValue||<p>Pokud vytváříte certifikát podepsaný svým držitelem nebo poskytujete soubor certifikátu, měla by být hodnota prázdná.</p><p>Pokud chcete použít existující certifikát, který byl dříve odeslán do trezoru klíčů, vyplňte hodnotu zdrojového trezoru. Například: /subscriptions/333cc2c84-12fa-5778-bd71-c71c07bf873f/resourceGroups/MyTestRG/providers/Microsoft.KeyVault/vaults/MYKEYVAULT</p>|

## <a name="set-up-azure-active-directory-client-authentication"></a>Nastavení ověřování klienta Služby Azure Active Directory
Pro clustery Service Fabric nasazené ve veřejné síti hostované v Azure je doporučení pro vzájemné ověřování mezi klientem a uzlem:
* Pro identitu klienta použijte službu Azure Active Directory.
* Použijte certifikát pro identitu serveru a šifrování SSL komunikace HTTP.

Nastavení služby Azure Active Directory (Azure AD) k ověření klientů pro cluster Service Fabric musí být provedeno před [vytvořením clusteru](#createvaultandcert). Azure AD umožňuje organizacím (označované jako tenanty) spravovat přístup uživatelů k aplikacím. 

Cluster Service Fabric nabízí několik vstupních bodů pro své funkce správy, včetně webové aplikace [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) a sady Visual [Studio](service-fabric-manage-application-in-visual-studio.md). V důsledku toho vytvoříte dvě aplikace Azure AD pro řízení přístupu ke clusteru: jedna webová aplikace a jedna nativní aplikace.  Po vytvoření aplikací přiřadíte uživatele k rolím jen pro čtení a k rolím správce.

> [!NOTE]
> Před vytvořením clusteru je nutné provést následující kroky. Vzhledem k tomu, že skripty očekávají názvy clusterů a koncové body, hodnoty by měly být naplánovány a nikoli hodnoty, které jste již vytvořili.

V tomto článku předpokládáme, že jste již vytvořili klienta. Pokud jste tak neučinili, začněte tím, že si přečtete [jak získat klienta Služby Azure Active Directory](../active-directory/develop/quickstart-create-new-tenant.md).

Abychom zjednodušili kroky při konfiguraci Azure AD pomocí clusteru Service Fabric, vytvořili jsme sadu skriptů prostředí Windows PowerShell. [Stáhněte si skripty](https://github.com/Azure-Samples/service-fabric-aad-helpers) do počítače.

### <a name="create-azure-ad-applications-and-assign-users-to-roles"></a>Vytváření aplikací Azure AD a přiřazování uživatelů k rolím
Vytvořte dvě aplikace Azure AD pro řízení přístupu ke clusteru: jedna webová aplikace a jedna nativní aplikace. Po vytvoření aplikací představujících váš cluster přiřaďte uživatele k [rolím podporovaným service fabricem](service-fabric-cluster-security-roles.md): jen pro čtení a správce.

Spusťte `SetupApplications.ps1`a zadejte jako parametry ID klienta, název clusteru a adresu URL odpovědi webové aplikace. Zadejte uživatelská jména a hesla pro uživatele. Například:

```powershell
$Configobj = .\SetupApplications.ps1 -TenantId '<MyTenantID>' -ClusterName 'mysfcluster123' -WebApplicationReplyUrl 'https://mysfcluster123.eastus.cloudapp.azure.com:19080/Explorer/index.html' -AddResourceAccess
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestUser' -Password 'P@ssword!123'
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestAdmin' -Password 'P@ssword!123' -IsAdmin
```

> [!NOTE]
> Pro národní cloudy (například Azure Government, Azure `-Location` China, Azure Germany) zadejte parametr.

Id *tenanta*nebo ID adresáře najdete na [webu Azure Portal](https://portal.azure.com). Vyberte**Vlastnosti služby** **Azure Active Directory** > a zkopírujte hodnotu **ID adresáře.**

*Název clusteru* se používá k předponě aplikací Azure AD, které jsou vytvořeny skriptem. Nemusí přesně odpovídat skutečnému názvu clusteru. Pouze usnadňuje mapování artefaktů Azure AD na cluster Service Fabric, který se používá.

*WebApplicationReplyUrl* je výchozí koncový bod, který Azure AD vrátí uživatelům po dokončení přihlášení. Nastavte tento koncový bod jako koncový bod aplikace Service Fabric Explorer pro váš cluster, který je ve výchozím nastavení:

https://&lt;&gt;cluster_domain :19080/Explorer

Budete vyzváni k přihlášení k účtu, který má oprávnění správce pro klienta Azure AD. Po přihlášení skript vytvoří webové a nativní aplikace představující cluster Service Fabric. V aplikacích klienta na [webu Azure Portal](https://portal.azure.com)byste měli vidět dvě nové položky:

   * *ClusterName*\_Cluster Název clusteru
   * *Klient Název_clusteru*\_

Skript vytiskne JSON vyžadované šablonou Správce prostředků při vytváření clusteru, takže je vhodné ponechat okno Prostředí PowerShell otevřené.

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

### <a name="add-azure-ad-configuration-to-use-azure-ad-for-client-access"></a>Přidání konfigurace Azure AD pro použití Azure AD pro přístup ke klientům
V [souboru azuredeploy.json][template]nakonfigurujte Azure AD v části **Microsoft.ServiceFabric/clusters.** Přidejte parametry pro ID klienta, ID aplikace clusteru a ID klientské aplikace.  

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    ...

    "aadTenantId": {
      "type": "string",
      "defaultValue": "0e3d2646-78b3-4711-b8be-74a381d9890c"
    },
    "aadClusterApplicationId": {
      "type": "string",
      "defaultValue": "cb147d34-b0b9-4e77-81d6-420fef0c4180"
    },
    "aadClientApplicationId": {
      "type": "string",
      "defaultValue": "7a8f3b37-cc40-45cc-9b8f-57b8919ea461"
    }
  },

...

{
  "apiVersion": "2018-02-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  ...
  "properties": {
    ...
    "azureActiveDirectory": {
      "tenantId": "[parameters('aadTenantId')]",
      "clusterApplication": "[parameters('aadClusterApplicationId')]",
      "clientApplication": "[parameters('aadClientApplicationId')]"
    },
    ...
  }
}
```

Přidejte hodnoty parametrů do souboru parametrů [azuredeploy.parameters.json.][parameters] Například:

```json
"aadTenantId": {
"value": "0e3d2646-78b3-4711-b8be-74a381d9890c"
},
"aadClusterApplicationId": {
"value": "cb147d34-b0b9-4e77-81d6-420fef0c4180"
},
"aadClientApplicationId": {
"value": "7a8f3b37-cc40-45cc-9b8f-57b8919ea461"
}
```
<a id="configurediagnostics" name="configurediagnostics_anchor"></a>

## <a name="configure-diagnostics-collection-on-the-cluster"></a>Konfigurace kolekce diagnostiky v clusteru
Pokud používáte cluster Service Fabric, je vhodné shromažďovat protokoly ze všech uzlů v centrálním umístění. S protokoly v centrálním umístění vám pomůže analyzovat a řešit problémy v clusteru nebo problémy v aplikacích a službách spuštěných v tomto clusteru.

Jedním ze způsobů, jak nahrávat a shromažďovat protokoly, je použití rozšíření Azure Diagnostics (WAD), které nahrává protokoly do Služby Azure Storage a má také možnost odesílat protokoly do Azure Application Insights nebo Event Hubs. Můžete také použít externí proces ke čtení událostí z úložiště a umístit je do produktu platformy analýzy, jako jsou protokoly Azure Monitor nebo jiné řešení analýzy protokolů.

Pokud používáte tento kurz, kolekce diagnostiky je již nakonfigurován a [šablona][template].

Pokud máte existující cluster, ve kterých není nasazena diagnostika, můžete jej přidat nebo aktualizovat prostřednictvím šablony clusteru. Upravte šablonu Správce prostředků, která se používá k vytvoření existujícího clusteru nebo ke stažení šablony z portálu. Upravte soubor template.json provedením následujících úkolů:

Přidejte nový prostředek úložiště do oddílu prostředků v šabloně:
```json
"resources": [
...
{
  "apiVersion": "2015-05-01-preview",
  "type": "Microsoft.Storage/storageAccounts",
  "name": "[parameters('applicationDiagnosticsStorageAccountName')]",
  "location": "[parameters('computeLocation')]",
  "sku": {
    "accountType": "[parameters('applicationDiagnosticsStorageAccountType')]"
  },
  "tags": {
    "resourceType": "Service Fabric",
    "clusterName": "[parameters('clusterName')]"
  }
},
...
]
```

Dále přidejte parametry pro název účtu úložiště a zadejte do části parametry šablony. Nahraďte zástupný název účtu textového úložiště sem s názvem účtu úložiště, který chcete.

```json
"parameters": {
...
"applicationDiagnosticsStorageAccountType": {
    "type": "string",
    "allowedValues": [
    "Standard_LRS",
    "Standard_GRS"
    ],
    "defaultValue": "Standard_LRS",
    "metadata": {
    "description": "Replication option for the application diagnostics storage account"
    }
},
"applicationDiagnosticsStorageAccountName": {
    "type": "string",
    "defaultValue": "**STORAGE ACCOUNT NAME GOES HERE**",
    "metadata": {
    "description": "Name for the storage account that contains application diagnostics data from the cluster"
    }
},
...
}
```

Dále přidejte rozšíření **IaaSDiagnostics** do pole rozšíření **vlastnosti VirtualMachineProfile** každého prostředku **Microsoft.Compute/virtualMachineScaleSets** v clusteru.  Pokud používáte [ukázkovou šablonu][template], existují tři škálovací sady virtuálních strojů (jedna pro každý typ uzlu v clusteru).

```json
"apiVersion": "2018-10-01",
"type": "Microsoft.Compute/virtualMachineScaleSets",
"name": "[variables('vmNodeType1Name')]",
"properties": {
    ...
    "virtualMachineProfile": {
        "extensionProfile": {
            "extensions": [
                {
                    "name": "[concat(parameters('vmNodeType0Name'),'_Microsoft.Insights.VMDiagnosticsSettings')]",
                    "properties": {
                        "type": "IaaSDiagnostics",
                        "autoUpgradeMinorVersion": true,
                        "protectedSettings": {
                        "storageAccountName": "[parameters('applicationDiagnosticsStorageAccountName')]",
                        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('applicationDiagnosticsStorageAccountName')),'2015-05-01-preview').key1]",
                        "storageAccountEndPoint": "https://core.windows.net/"
                        },
                        "publisher": "Microsoft.Azure.Diagnostics",
                        "settings": {
                        "WadCfg": {
                            "DiagnosticMonitorConfiguration": {
                            "overallQuotaInMB": "50000",
                            "EtwProviders": {
                                "EtwEventSourceProviderConfiguration": [
                                {
                                    "provider": "Microsoft-ServiceFabric-Actors",
                                    "scheduledTransferKeywordFilter": "1",
                                    "scheduledTransferPeriod": "PT5M",
                                    "DefaultEvents": {
                                    "eventDestination": "ServiceFabricReliableActorEventTable"
                                    }
                                },
                                {
                                    "provider": "Microsoft-ServiceFabric-Services",
                                    "scheduledTransferPeriod": "PT5M",
                                    "DefaultEvents": {
                                    "eventDestination": "ServiceFabricReliableServiceEventTable"
                                    }
                                }
                                ],
                                "EtwManifestProviderConfiguration": [
                                {
                                    "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                                    "scheduledTransferLogLevelFilter": "Information",
                                    "scheduledTransferKeywordFilter": "4611686018427387904",
                                    "scheduledTransferPeriod": "PT5M",
                                    "DefaultEvents": {
                                    "eventDestination": "ServiceFabricSystemEventTable"
                                    }
                                }
                                ]
                            }
                            }
                        },
                        "StorageAccount": "[parameters('applicationDiagnosticsStorageAccountName')]"
                        },
                        "typeHandlerVersion": "1.5"
                    }
                }
            ...
            ]
        }
    }
}
```
<a id="configureeventstore" name="configureeventstore_anchor"></a>

## <a name="configure-the-eventstore-service"></a>Konfigurace služby EventStore
Služba EventStore je možnost monitorování v Service Fabric. EventStore poskytuje způsob, jak pochopit stav clusteru nebo úlohy v daném okamžiku. EventStore je stavová služba Service Fabric, která udržuje události z clusteru. Události jsou vystaveny prostřednictvím aplikace Service Fabric Explorer, REST a rozhraní API. EventStore se dotazuje clusteru přímo na získání diagnostických dat na libovolné entitě v clusteru a měl by být použit k pomoci:

* Diagnostikovat problémy ve vývoji nebo testování nebo kde pravděpodobně používáte kanál pro monitorování
* Zkontrolujte, zda jsou akce správy, které provádíte v clusteru, zpracovávány správně.
* Získejte "snímek" o tom, jak Service Fabric interaguje s konkrétní entitou



Chcete-li povolit službu EventStore v clusteru, přidejte do vlastnosti **fabricSettings** prostředku **Microsoft.ServiceFabric/clusters:**

```json
"apiVersion": "2018-02-01",
"type": "Microsoft.ServiceFabric/clusters",
"name": "[parameters('clusterName')]",
"properties": {
    ...
    "fabricSettings": [
        ...
        {
            "name": "EventStoreService",
            "parameters": [
                {
                "name": "TargetReplicaSetSize",
                "value": "3"
                },
                {
                "name": "MinReplicaSetSize",
                "value": "1"
                }
            ]
        }
    ]
}
```
<a id="configureloganalytics" name="configureloganalytics_anchor"></a>

## <a name="set-up-azure-monitor-logs-for-the-cluster"></a>Nastavení protokolů Azure Monitor pro cluster

Protokoly Azure Monitor unás je naše doporučení ke sledování událostí na úrovni clusteru. Chcete-li nastavit protokoly Azure Monitor pro sledování clusteru, musíte mít [povolenou diagnostiku pro zobrazení událostí na úrovni clusteru](#configure-diagnostics-collection-on-the-cluster).  

Pracovní prostor musí být připojen k diagnostickým datům přicházejícím z vašeho clusteru.  Tato data protokolu jsou uložena v tabulkách úložiště *applicationDiagnosticsStorageAccountName,* v tabulkách WADServiceFabric*EventTable, WADWindowsEventLogsTable a WADETWEventTable.

Přidejte pracovní prostor Azure Log Analytics a přidejte řešení do pracovního prostoru:

```json
"resources": [
    ...
    {
        "apiVersion": "2015-11-01-preview",
        "location": "[parameters('omsRegion')]",
        "name": "[parameters('omsWorkspacename')]",
        "type": "Microsoft.OperationalInsights/workspaces",
        "properties": {
            "sku": {
                "name": "Free"
            }
        },
        "resources": [
            {
                "apiVersion": "2015-11-01-preview",
                "name": "[concat(variables('applicationDiagnosticsStorageAccountName'),parameters('omsWorkspacename'))]",
                "type": "storageinsightconfigs",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]",
                    "[concat('Microsoft.Storage/storageAccounts/', variables('applicationDiagnosticsStorageAccountName'))]"
                ],
                "properties": {
                    "containers": [],
                    "tables": [
                        "WADServiceFabric*EventTable",
                        "WADWindowsEventLogsTable",
                        "WADETWEventTable"
                    ],
                    "storageAccount": {
                        "id": "[resourceId('Microsoft.Storage/storageaccounts/', variables('applicationDiagnosticsStorageAccountName'))]",
                        "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('applicationDiagnosticsStorageAccountName')),'2015-06-15').key1]"
                    }
                }
            },
            {
                "apiVersion": "2015-11-01-preview",
                "type": "datasources",
                "name": "sampleWindowsPerfCounter",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
                ],
                "kind": "WindowsPerformanceCounter",
                "properties": {
                    "objectName": "Memory",
                    "instanceName": "*",
                    "intervalSeconds": 10,
                    "counterName": "Available MBytes"
                }
            },
            {
                "apiVersion": "2015-11-01-preview",
                "type": "datasources",
                "name": "sampleWindowsPerfCounter2",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
                ],
                "kind": "WindowsPerformanceCounter",
                "properties": {
                    "objectName": "Service Fabric Service",
                    "instanceName": "*",
                    "intervalSeconds": 10,
                    "counterName": "Average milliseconds per request"
                }
            }
        ]
    },
    {
        "apiVersion": "2015-11-01-preview",
        "location": "[parameters('omsRegion')]",
        "name": "[variables('solution')]",
        "type": "Microsoft.OperationsManagement/solutions",
        "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
        ],
        "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
        },
        "plan": {
            "name": "[variables('solution')]",
            "publisher": "Microsoft",
            "product": "[Concat('OMSGallery/', variables('solutionName'))]",
            "promotionCode": ""
        }
    }
]
```

Dále přidejte parametry
```json
"parameters": {
    ...
    "omsWorkspacename": {
        "type": "string",
        "defaultValue": "mysfomsworkspace",
        "metadata": {
            "description": "Name of your OMS Log Analytics Workspace"
        }
    },
    "omsRegion": {
        "type": "string",
        "defaultValue": "West Europe",
        "allowedValues": [
            "West Europe",
            "East US",
            "Southeast Asia"
        ],
        "metadata": {
            "description": "Specify the Azure Region for your OMS workspace"
        }
    }
}
```

Dále přidejte proměnné:
```json
"variables": {
    ...
    "solution": "[Concat('ServiceFabric', '(', parameters('omsWorkspacename'), ')')]",
    "solutionName": "ServiceFabric"
}
```

Přidejte rozšíření agenta Log Analytics do každé škálovací sady virtuálních strojů v clusteru a připojte ho k pracovnímu prostoru Log Analytics. To umožňuje shromažďování diagnostických dat o kontejnerech, aplikacích a monitorování výkonu. Přidáním jako rozšíření prostředků škálovací sady virtuálních strojů Azure Resource Manager zajišťuje, že se nainstaluje na každém uzlu, a to i při škálování clusteru.

```json
"apiVersion": "2018-10-01",
"type": "Microsoft.Compute/virtualMachineScaleSets",
"name": "[variables('vmNodeType1Name')]",
"properties": {
    ...
    "virtualMachineProfile": {
        "extensionProfile": {
            "extensions": [
                {
                    "name": "[concat(variables('vmNodeType0Name'),'OMS')]",
                    "properties": {
                        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
                        "type": "MicrosoftMonitoringAgent",
                        "typeHandlerVersion": "1.0",
                        "autoUpgradeMinorVersion": true,
                        "settings": {
                            "workspaceId": "[reference(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename')), '2015-11-01-preview').customerId]"
                        },
                        "protectedSettings": {
                            "workspaceKey": "[listKeys(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename')),'2015-11-01-preview').primarySharedKey]"
                        }
                    }
                }
            ...
            ]
        }
    }
}
```

<a id="createvaultandcert" name="createvaultandcert_anchor"></a>

## <a name="deploy-the-virtual-network-and-cluster"></a>Nasazení virtuální sítě a clusteru

Dále nastavte topologii sítě a nasaďte cluster Service Fabric. Šablona [azuredeploy.json][template] Resource Manager vytvoří pro Service Fabric virtuální síť, podsíť a skupinu zabezpečení sítě. Šablona také nasadí cluster s povoleným zabezpečením pomocí certifikátu. Pro produkční clustery použijte jako certifikát clusteru certifikát od certifikační autority. K zabezpečení testovacích clusterů můžete použít certifikát podepsaný svým držitelem.

Šablona v tomto článku nasazuje cluster, který používá kryptografický otisk certifikátu k identifikaci certifikátu clusteru. Žádné dva certifikáty mohou mít stejný kryptografický otisk, což ztěžuje správu certifikátů. Přepnutí nasazeného clusteru z kryptografických otisků certifikátů na běžné názvy certifikátů zjednodušuje správu certifikátů. Informace o tom, jak aktualizovat cluster tak, aby používal běžné názvy certifikátů pro správu certifikátů, najdete v tématu [Změna clusteru na správu běžných názvů certifikátů](service-fabric-cluster-change-cert-thumbprint-to-cn.md).

### <a name="create-a-cluster-by-using-an-existing-certificate"></a>Vytvoření clusteru pomocí existujícího certifikátu

Následující skript používá rutinu [New-AzServiceFabricCluster](/powershell/module/az.servicefabric/New-azServiceFabricCluster) a šablonu k nasazení nového clusteru v Azure. Rutina vytvoří nový trezor klíčů v Azure a nahraje váš certifikát.

```powershell
# Variables.
$groupname = "sfclustertutorialgroup"
$clusterloc="southcentralus"  # Must match the location parameter in the template
$templatepath="C:\temp\cluster"

$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
$clustername = "mysfcluster123"  # Must match the clustername parameter in the template
$vaultname = "clusterkeyvault123"
$vaultgroupname="clusterkeyvaultgroup123"
$subname="$clustername.$clusterloc.cloudapp.azure.com"

# Sign in to your Azure account and select your subscription
Connect-AzAccount
Get-AzSubscription
Set-AzContext -SubscriptionId <guid>

# Create a new resource group for your deployment, and give it a name and a location.
New-AzResourceGroup -Name $groupname -Location $clusterloc

# Create the Service Fabric cluster.
New-AzServiceFabricCluster  -ResourceGroupName $groupname -TemplateFile "$templatepath\azuredeploy.json" `
-ParameterFile "$templatepath\azuredeploy.parameters.json" -CertificatePassword $certpwd `
-KeyVaultName $vaultname -KeyVaultResourceGroupName $vaultgroupname -CertificateFile $certpath
```

### <a name="create-a-cluster-by-using-a-new-self-signed-certificate"></a>Vytvoření clusteru pomocí nového certifikátu podepsaného svým držitelem

Následující skript používá rutinu [New-AzServiceFabricCluster](/powershell/module/az.servicefabric/New-azServiceFabricCluster) a šablonu k nasazení nového clusteru v Azure. Rutina vytvoří nový trezor klíčů v Azure, přidá nový certifikát podepsaný svým držitelem do trezoru klíčů a stáhne soubor certifikátu místně.

```powershell
# Variables.
$groupname = "sfclustertutorialgroup"
$clusterloc="southcentralus"  # Must match the location parameter in the template
$templatepath="C:\temp\cluster"

$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
$certfolder="c:\mycertificates\"
$clustername = "mysfcluster123"
$vaultname = "clusterkeyvault123"
$vaultgroupname="clusterkeyvaultgroup123"
$subname="$clustername.$clusterloc.cloudapp.azure.com"

# Sign in to your Azure account and select your subscription
Connect-AzAccount
Get-AzSubscription
Set-AzContext -SubscriptionId <guid>

# Create a new resource group for your deployment, and give it a name and a location.
New-AzResourceGroup -Name $groupname -Location $clusterloc

# Create the Service Fabric cluster.
New-AzServiceFabricCluster  -ResourceGroupName $groupname -TemplateFile "$templatepath\azuredeploy.json" `
-ParameterFile "$templatepath\azuredeploy.parameters.json" -CertificatePassword $certpwd `
-CertificateOutputFolder $certfolder -KeyVaultName $vaultname -KeyVaultResourceGroupName $vaultgroupname -CertificateSubjectName $subname

```

## <a name="connect-to-the-secure-cluster"></a>Připojení k zabezpečenému clusteru

Připojte se ke clusteru pomocí modulu Service Fabric PowerShell nainstalovaného sadou Service Fabric SDK.  Nejprve nainstalujte certifikát do osobního úložiště (Moje) aktuálního uživatele na počítači. Spusťte následující příkaz PowerShellu:

```powershell
$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
        -FilePath C:\mycertificates\mysfcluster20170531104310.pfx `
        -Password $certpwd
```

Nyní jste připraveni připojit se k zabezpečenému clusteru.

Modul PowerShellu pro **Service Fabric** poskytuje řadu rutin pro správu clusterů Service Fabric, aplikací a služeb. Pomocí rutiny [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) se připojte k zabezpečenému clusteru. Podrobnosti o kryptografickém otisku certifikátu SHA1 a koncovém bodu připojení se nacházejí ve výstupu z předchozího kroku.

Pokud jste dříve nastavili ověřování klienta Azure AD, spusťte následující příkaz: 
```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster123.southcentralus.cloudapp.azure.com:19000 `
        -KeepAliveIntervalInSec 10 `
        -AzureActiveDirectory `
        -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10
```

Pokud jste nenastavili ověřování klienta Azure AD, spusťte následující příkaz:
```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster123.southcentralus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -FindType FindByThumbprint -FindValue C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -StoreLocation CurrentUser -StoreName My
```

Zkontrolujte, zda jste připojeni a že cluster je v pořádku pomocí rutiny [Get-ServiceFabricClusterHealth.](/powershell/module/servicefabric/get-servicefabricclusterhealth)

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Ostatní články v této sérii kurzů používají cluster, který jste vytvořili. Pokud nechcete ihned pokračovat dalším článkem, můžete [cluster odstranit](service-fabric-cluster-delete.md), aby se vám neúčtovaly poplatky.

## <a name="next-steps"></a>Další kroky

Přejdete k následujícímu kurzu, kde se dozvíte, jak škálovat cluster.

> [!div class="checklist"]
> * Vytvoření virtuální sítě v Azure pomocí PowerShellu
> * Vytvoření trezoru klíčů a nahrání certifikátu
> * Nastavení ověřování služby Azure Active Directory
> * Konfigurace kolekce diagnostiky
> * Nastavení služby EventStore
> * Nastavení protokolů Azure Monitoru
> * Vytvoření zabezpečeného clusteru Service Fabric v Azure PowerShellu
> * Zabezpečit cluster pomocí certifikátu X.509
> * Připojení ke clusteru pomocí prostředí PowerShell
> * Odebrat cluster

Dále přejdete k následujícímu kurzu, kde se dozvíte, jak sledovat cluster.
> [!div class="nextstepaction"]
> [Sledování clusteru](service-fabric-tutorial-monitor-cluster.md)

[template]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.json
[parameters]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.Parameters.json
