---
title: Vytvoření clusteru Service Fabric s Windows v Azure | Dokumentace Microsoftu
description: V tomto kurzu se dozvíte, jak nasadit cluster Windows Service Fabric do virtuální sítě Azure a skupinu zabezpečení sítě pomocí prostředí PowerShell.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/13/2019
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: ade7f86bc5a00c079a7ccbe719ae46043d692047
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2019
ms.locfileid: "58225140"
---
# <a name="tutorial-deploy-a-service-fabric-cluster-running-windows-into-an-azure-virtual-network"></a>Kurz: Nasazení clusteru Service Fabric s Windows do virtuální sítě Azure

Tento kurz je první částí série. Zjistíte, jak nasadit cluster Azure Service Fabric s Windows do [virtuální síť Azure](../virtual-network/virtual-networks-overview.md) a [skupinu zabezpečení sítě](../virtual-network/virtual-networks-nsg.md) pomocí Powershellu a šablony. Jakmile budete hotovi, máte cluster běží v cloudu, do kterého můžete nasazovat aplikace. Vytvoření clusteru s Linuxem, který používá Azure CLI najdete v tématu [vytvoření zabezpečeného clusteru s Linuxem v Azure](service-fabric-tutorial-create-vnet-and-linux-cluster.md).

Tento kurz popisuje produkční scénář. Pokud chcete vytvořit cluster menší pro účely testování, najdete v článku [vytvoření testovacího clusteru](./scripts/service-fabric-powershell-create-secure-cluster-cert.md).

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření virtuální sítě v Azure pomocí PowerShellu
> * Vytvoření trezoru klíčů a nahrání certifikátu
> * Nastavení ověřování Azure Active Directory
> * Konfigurace shromažďování diagnostických dat
> * Nastavení služby Eventstoru
> * Nastavte si protokoly Azure monitoru
> * Vytvoření zabezpečeného clusteru Service Fabric v Azure PowerShellu
> * Zabezpečení clusteru pomocí certifikátu X.509
> * Připojení ke clusteru pomocí prostředí PowerShell
> * Odebrání clusteru

V této sérii kurzů se naučíte:
> [!div class="checklist"]
> * Vytvoření zabezpečeného clusteru v Azure
> * [Monitorování clusteru](service-fabric-tutorial-monitor-cluster.md)
> * [Horizontální snížení nebo navýšení kapacity clusteru](service-fabric-tutorial-scale-cluster.md)
> * [Upgrade modulu runtime clusteru](service-fabric-tutorial-upgrade-cluster.md)
> * [Odstranění clusteru](service-fabric-tutorial-delete-cluster.md)

## <a name="prerequisites"></a>Požadavky

Než začnete s tímto kurzem:

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Nainstalujte [Service Fabric SDK a modul Powershellu](service-fabric-get-started.md).
* Nainstalujte [modul Azure Powershellu verze 4.1 nebo vyšší](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps).
* Projděte si klíčové koncepty [Azure clustery](service-fabric-azure-clusters-overview.md).

Následující postupy vytvářejí sedm uzly clusteru Service Fabric. Použití [cenovou kalkulačku funkcí Azure](https://azure.microsoft.com/pricing/calculator/) vypočítat náklady vzniklé používáním clusteru Service Fabric v Azure.

## <a name="download-and-explore-the-template"></a>Stažení a prozkoumání šablony

Stáhněte následující soubory šablon Azure Resource Manageru:

* [azuredeploy.json][template]
* [azuredeploy.parameters.json][parameters]

Tato šablona nasadí zabezpečený cluster sedmi virtuálních počítačů a tři typy uzlů do virtuální sítě a skupinu zabezpečení sítě.  Další ukázkové šablony najdete na [GitHubu](https://github.com/Azure-Samples/service-fabric-cluster-templates). [Azuredeploy.json] [ template] nasadí řadu prostředků včetně následujících.

### <a name="service-fabric-cluster"></a>Cluster Service Fabric

V prostředku **Microsoft.ServiceFabric/clusters** se konfiguruje cluster s Windows s těmito charakteristikami:

* Tři typy uzlů.
* Pět uzlů primárního typu (možnost konfigurace v parametrech šablony) a jeden uzel v každém z ostatních typů dvěma uzly.
* Operační systém: Windows Server 2016 Datacenter s kontejnery (možnost konfigurace v parametrech šablony).
* Zabezpečení pomocí certifikátu (možnost konfigurace v parametrech šablony).
* [Reverzní proxy server](service-fabric-reverseproxy.md) je povolená.
* [Služba DNS](service-fabric-dnsservice.md) je povolená.
* [Úroveň odolnosti](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) bronzová (možnost konfigurace v parametrech šablony).
* [Úroveň spolehlivosti](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) stříbrná (možnost konfigurace v parametrech šablony).
* koncový bod připojení klienta: 19000 (možnost konfigurace v parametrech šablony).
* Koncový bod brány HTTP: 19080 (možnost konfigurace v parametrech šablony).

### <a name="azure-load-balancer"></a>Nástroj pro vyrovnávání zatížení Azure

V **Microsoft.Network/loadBalancers** prostředek, nástroj pro vyrovnávání zatížení je nakonfigurován. Sond a pravidel jsou nastavené pro následující porty:

* koncový bod připojení klienta: 19000
* Koncový bod brány HTTP: 19080
* port aplikací: 80
* port aplikací: 443
* Service Fabric reverzního proxy serveru: 19081

Pokud nejsou potřebné další porty aplikací, bude nutné upravit **Microsoft.Network/loadBalancers** prostředků a **Microsoft.Network/networkSecurityGroups** prostředek pro provoz.

### <a name="virtual-network-subnet-and-network-security-group"></a>Virtuální síť, podsíť a skupina zabezpečení sítě

Názvy virtuální sítě, podsítě a skupiny zabezpečení sítě jsou deklarované v parametrech šablony. Adresní prostory virtuální sítě a podsítě se taky deklarují v parametrech šablony a konfigurují v prostředku **Microsoft.Network/virtualNetworks**:

* adresní prostor virtuální sítě: 172.16.0.0/20
* Adresní prostor podsítě Service Fabric: 172.16.2.0/23

V prostředku **Microsoft.Network/networkSecurityGroups** jsou povolená následující pravidla pro příchozí provoz. Hodnoty portů můžete změnit změnou proměnných šablony.

* ClientConnectionEndpoint (TCP): 19000
* HttpGatewayEndpoint (HTTP/TCP): 19080
* SMB: 445
* Internodecommunication: 1025, 1026, 1027
* Rozsah dočasných portů: 49152 až 65534 (potřebných alespoň 256 portů).
* Porty pro použití aplikací: 80 a 443
* Rozsah portů aplikace: 49152 až 65534 (používají pro komunikace mezi službami. Jiné porty nejsou otevřené v nástroji pro vyrovnávání zatížení).
* Všechny ostatní porty jsou blokované

Pokud nejsou potřebné další porty aplikací, bude nutné upravit **Microsoft.Network/loadBalancers** prostředků a **Microsoft.Network/networkSecurityGroups** prostředek pro provoz.

### <a name="windows-defender"></a>Windows Defender
Ve výchozím nastavení [antivirový program Windows Defender](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-on-windows-server-2016) je nainstalovaná a funkční v systému Windows Server 2016. Uživatelské rozhraní je nainstalované ve výchozím nastavení na některých jednotkách SKU, ale není povinné. Pro každý uzel typu/škálovací sady virtuálních počítačů deklarované v šabloně [Azure VM Antimalwarové rozšíření](/azure/virtual-machines/extensions/iaas-antimalware-windows) se používá k vyloučení adresáře Service Fabric a procesy:

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

Soubor s parametry [azuredeploy.parameters.json][parameters] deklaruje mnoho hodnot používaných pro nasazení clusteru a přidružených prostředků. Následují parametry upravit pro vaše nasazení:

**Parametr** | **Příklad hodnoty** | **Poznámky** 
|---|---|---|
|adminUserName|vmadmin| Uživatelské jméno správce pro virtuální počítače clusteru. [Požadavky na uživatelské jméno pro virtuální počítač](https://docs.microsoft.com/azure/virtual-machines/windows/faq#what-are-the-username-requirements-when-creating-a-vm). |
|adminPassword|Password#1234| Heslo správce pro virtuální počítače clusteru. [Požadavky na heslo pro virtuální počítač](https://docs.microsoft.com/azure/virtual-machines/windows/faq#what-are-the-password-requirements-when-creating-a-vm).|
|clusterName|mysfcluster123| Název clusteru. Může obsahovat jenom písmena a číslice. Může mít délku 3 až 23 znaků.|
|location|southcentralus| Umístění clusteru. |
|certificateThumbprint|| <p>Pokud vytváříte certifikát podepsaný svým držitelem nebo poskytujete soubor certifikátu, měla by být hodnota prázdná.</p><p>Pokud chcete použít existující certifikát, který se dříve odeslal do trezoru klíčů, vyplňte hodnotu kryptografického otisku certifikátu SHA1. Příklad: „6190390162C988701DB5676EB81083EA608DCCF3“.</p> |
|certificateUrlValue|| <p>Pokud vytváříte certifikát podepsaný svým držitelem nebo poskytujete soubor certifikátu, měla by být hodnota prázdná. </p><p>Pokud chcete použít existující certifikát, který byl dříve odeslán do trezoru klíčů, vyplňte URL certifikátu. Například https://mykeyvault.vault.azure.net:443/secrets/mycertificate/02bea722c9ef4009a76c5052bcbf8346.</p>|
|sourceVaultValue||<p>Pokud vytváříte certifikát podepsaný svým držitelem nebo poskytujete soubor certifikátu, měla by být hodnota prázdná.</p><p>Pokud chcete použít existující certifikát, který byl dříve odeslán do trezoru klíčů, vyplňte hodnotu zdrojového trezoru. Například: /subscriptions/333cc2c84-12fa-5778-bd71-c71c07bf873f/resourceGroups/MyTestRG/providers/Microsoft.KeyVault/vaults/MYKEYVAULT</p>|

## <a name="set-up-azure-active-directory-client-authentication"></a>Nastavení ověřování klienta služby Azure Active Directory
Pro clustery Service Fabric nasazené ve veřejné síti hostované v Azure se doporučení pro vzájemné ověřování uzel klienta:
* Použití Azure Active Directory pro identity klienta.
* Použijte certifikát pro server identity a šifrování SSL komunikaci pomocí protokolu HTTP.

Nastavení Azure Active Directory (Azure AD) k ověřování klientů pro cluster Service Fabric je třeba provést před [vytváření clusteru](#createvaultandcert). Azure AD umožňuje organizacím (označuje se jako tenantů) ke správě přístupu uživatelů k aplikacím. 

Cluster Service Fabric nabízí několik vstupních bodů do jeho funkce správy, včetně webová [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) a [sady Visual Studio](service-fabric-manage-application-in-visual-studio.md). Proto vytvoříte dvě aplikace Azure AD pro řízení přístupu ke clusteru: jeden webové aplikace a jedné nativní aplikace.  Po vytvoření aplikace můžete přiřadit uživatele jen pro čtení a role správce.

> [!NOTE]
> Před vytvořením clusteru, musíte dokončit následující kroky. Vzhledem k tomu, že skripty očekávají názvů clusterů a koncových bodů, hodnoty plánování byste měli využít a ne hodnoty, že jste již vytvořili.

V tomto článku předpokládáme, že jste už vytvořili tenanta. Pokud jste tak dosud, začněte tím, že čtení [získání tenanta služby Azure Active Directory](../active-directory/develop/quickstart-create-new-tenant.md).

Pokud chcete zjednodušit kroky při konfiguraci Azure AD s clusterem Service Fabric, jsme vytvořili sadu skriptů prostředí Windows PowerShell. [Stáhněte si skripty](https://github.com/robotechredmond/Azure-PowerShell-Snippets/tree/master/MicrosoftAzureServiceFabric-AADHelpers/AADTool) k vašemu počítači.

### <a name="create-azure-ad-applications-and-assign-users-to-roles"></a>Vytvoření aplikace Azure AD a přiřazení uživatelů k rolím
Vytvořit dvě aplikace Azure AD pro řízení přístupu ke clusteru: jeden webové aplikace a jedné nativní aplikace. Po vytvoření aplikace, které chcete představují vašeho clusteru, přiřaďte uživatele, aby [role podporuje Service Fabric](service-fabric-cluster-security-roles.md): jen pro čtení a správce.

Spustit `SetupApplications.ps1`a zadat tenanta jako parametry ID, název clusteru a adresy URL odpovědi webové aplikace. Zadejte uživatelská jména a hesla pro uživatele. Příklad:

```PowerShell
$Configobj = .\SetupApplications.ps1 -TenantId '<MyTenantID>' -ClusterName 'mysfcluster123' -WebApplicationReplyUrl 'https://mysfcluster123.eastus.cloudapp.azure.com:19080/Explorer/index.html' -AddResourceAccess
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestUser' -Password 'P@ssword!123'
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestAdmin' -Password 'P@ssword!123' -IsAdmin
```

> [!NOTE]
> Pro národní cloudy (například Azure Government, Azure China, Azure Germany), zadejte `-Location` parametru.

Můžete najít vaše *TenantId*, nebo ID adresáře v [webu Azure portal](https://portal.azure.com). Vyberte **Azure Active Directory** > **vlastnosti** a zkopírujte **ID adresáře** hodnotu.

*Název clusteru* používat jako předpona aplikací v Azure AD, které jsou vytvořeny skriptem. Nemusí přesně odpovídat skutečný název clusteru. Pouze usnadňuje mapování artefakty Azure AD do clusteru Service Fabric používá.

*WebApplicationReplyUrl* je výchozí koncový bod, který Azure AD vrací uživatelům po jejich dokončení přihlašování. Nastavte jako koncový bod Service Fabric Exploreru pro váš cluster, který ve výchozím nastavení je tento koncový bod:

https://&lt;cluster_domain&gt;:19080/Explorer

Budete vyzváni k přihlášení k účtu, který má oprávnění správce pro tenanta Azure AD. Po přihlášení, skript vytvoří webové a nativní aplikace pro reprezentaci vašeho clusteru Service Fabric. Do vašeho tenanta aplikací [webu Azure portal](https://portal.azure.com), měli byste vidět dvě nové položky:

   * *Název clusteru*\_clusteru
   * *Název clusteru*\_klienta

Skript vypíše JSON šablony Resource Manageru vyžaduje při vytváření clusteru, takže je vhodné ponechat otevřené okno Powershellu.

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

### <a name="add-azure-ad-configuration-to-use-azure-ad-for-client-access"></a>Přidat konfiguraci Azure AD, které používají Azure AD pro klientský přístup
V [azuredeploy.json][template], nakonfigurujte v Azure AD **Microsoft.ServiceFabric/clusters** části. Přidat parametry pro tenanta, ID aplikace ID clusteru a ID klienta aplikace.  

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

Přidání hodnoty parametrů v [azuredeploy.parameters.json] [ parameters] soubor parametrů. Příklad:

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

## <a name="configure-diagnostics-collection-on-the-cluster"></a>Konfigurace shromažďování diagnostických dat v clusteru
Když používáte cluster Service Fabric, je vhodné pro shromažďování protokolů ze všech uzlů v centrálním umístění. S protokoly v centrálním umístění vám pomáhají analyzovat a řešit problémy ve vašem clusteru nebo problémy v aplikace a služby běžící v tomto clusteru.

Jedním ze způsobů shromažďování protokolů a nahrát je pomocí rozšíření Azure Diagnostics (WAD), nahraje protokoly do služby Azure Storage, který má také možnost odeslat protokoly do služby Azure Application Insights nebo Center událostí. Externí proces lze také použít ke čtení události ze služby storage a umístit je do o produkt poskytovaný analýzy platformy, jako jsou protokoly Azure monitoru nebo jiné řešení analýzy protokolů.

Pokud jsou projdete tímto kurzem, shromažďování diagnostických dat je již nakonfigurován v [šablony][template].

Pokud máte existující cluster, který nemá diagnostiky nasazený, můžete přidat nebo aktualizovat prostřednictvím šablona clusteru. Upravte šablonu Resource Manageru, který se používá k vytvoření stávajícího clusteru nebo stáhněte šablonu z portálu. Upravte soubor template.json provedením následujících úloh:

Přidáte nový prostředek úložiště do oddílu prostředků v šabloně:
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

V dalším kroku přidáte parametry pro název účtu úložiště a typ do části parametrů šablony. Nahraďte název účtu úložiště text zástupného symbolu platí, že se že tady s názvem úložiště účtu, který jste chtěli.

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

V dalším kroku přidejte **IaaSDiagnostics** rozšíření k rozšíření pole **VirtualMachineProfile** vlastnosti každého **Microsoft.Compute/virtualMachineScaleSets** prostředek v clusteru.  Pokud používáte [Ukázková šablona][template], existují tři škálovací sady virtuálních počítačů (jeden pro každý typ uzlu v clusteru).

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

## <a name="configure-the-eventstore-service"></a>Konfigurace služby Eventstoru
Služba Eventstoru je možnosti monitorování v Service Fabric. Eventstoru poskytuje způsob, jak porozumět stavu clusteru nebo úloh v daném bodě v čase. Eventstoru je stavovou službu Service Fabric, který udržuje události z clusteru. Události jsou vystaveny prostřednictvím Service Fabric Explorer, REST a rozhraní API. Eventstoru dotazů clusteru přímo k získání diagnostických dat na entitu ve vašem clusteru a by měla sloužit ke:

* Diagnostikujte problémy ve vývoj a testování, nebo kde může pomocí monitorování kanálu
* Potvrďte, že správně zpracovává akce správy, které je možné ve vašem clusteru
* Získat "snímek" jak dixons carphone Service Fabric s konkrétní entity



Pokud chcete povolit službu Eventstoru ve vašem clusteru, přidejte následující text do **nastavení fabricSettings** vlastnost **Microsoft.ServiceFabric/clusters** prostředků:

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

## <a name="set-up-azure-monitor-logs-for-the-cluster"></a>Nastavte si protokoly Azure monitoru pro cluster

Protokoly služby Azure Monitor je naše doporučení pro monitorování událostí na úrovni clusteru. Chcete-li nastavit protokoly Azure monitoru ke sledování vašeho clusteru, musíte mít [diagnostiky povoleno zobrazení událostí úrovně clusteru](#configure-diagnostics-collection-on-the-cluster).  

Pracovní prostor se musí být připojen k diagnostická data z vašeho clusteru.  Tato data protokolu se ukládají v *applicationDiagnosticsStorageAccountName* účtu úložiště, WADServiceFabric * EventTable WADWindowsEventLogsTable a WADETWEventTable tabulky.

Přidat pracovní prostor Azure Log Analytics a přidejte řešení do pracovního prostoru:

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

V dalším kroku přidejte parametry
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

V dalším kroku přidejte proměnné:
```json
"variables": {
    ...
    "solution": "[Concat('ServiceFabric', '(', parameters('omsWorkspacename'), ')')]",
    "solutionName": "ServiceFabric"
}
```

Přidání Log Analytics rozšíření agenta do každého virtuálního počítače škálovací nastavit v clusteru a připojení agenta k pracovnímu prostoru Log Analytics. To umožňuje shromažďování diagnostická data o kontejnerech, aplikací a monitorování výkonu. Tak, že přidáte jako rozšíření k prostředku virtuálního počítače škálovací sady, Azure Resource Manageru se zajistí, že se nainstaluje na všech uzlech i při škálování clusteru.

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

Dále nastavte topologii sítě a nasaďte cluster Service Fabric. [Azuredeploy.json] [ template] šablony Resource Manageru se vytvoří virtuální síť, podsíť a skupinu zabezpečení sítě pro Service Fabric. Šablona také nasadí cluster s povoleným zabezpečením pomocí certifikátu. Pro produkční clustery používejte certifikát od certifikační autority jako certifikát clusteru. K zabezpečení testovacích clusterů můžete použít certifikát podepsaný svým držitelem.

Šablona v tomto článku se nasadí cluster, který používá kryptografický otisk certifikátu pro identifikaci certifikátu clusteru. Žádné dva certifikáty můžou mít se stejným kryptografickým otiskem, což znesnadňuje správy certifikátů. Přepínání nasazeném clusteru z kryptografické otisky certifikátů k běžnému názvu certifikátu ve skupinách usnadňuje správu certifikátů. Zjistěte, jak aktualizovat cluster používat běžné názvy certifikátů pro správu certifikátů, přečtěte si téma [clusteru změnit na společný název správy certifikátů](service-fabric-cluster-change-cert-thumbprint-to-cn.md).

### <a name="create-a-cluster-by-using-an-existing-certificate"></a>Vytvoření clusteru s použitím existujícího certifikátu

Následující skript pomocí rutiny [New-AzureRmServiceFabricCluster](/powershell/module/azurerm.servicefabric/New-AzureRmServiceFabricCluster) a šablony nasadí nový cluster do Azure. Rutina vytvoří v Azure nový trezor klíčů a odešle váš certifikát.

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
Connect-AzureRmAccount
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>

# Create a new resource group for your deployment, and give it a name and a location.
New-AzureRmResourceGroup -Name $groupname -Location $clusterloc

# Create the Service Fabric cluster.
New-AzureRmServiceFabricCluster  -ResourceGroupName $groupname -TemplateFile "$templatepath\azuredeploy.json" `
-ParameterFile "$templatepath\azuredeploy.parameters.json" -CertificatePassword $certpwd `
-KeyVaultName $vaultname -KeyVaultResourceGroupName $vaultgroupname -CertificateFile $certpath
```

### <a name="create-a-cluster-by-using-a-new-self-signed-certificate"></a>Vytvoření clusteru pomocí nového certifikátu podepsaného svým držitelem

Následující skript pomocí rutiny [New-AzureRmServiceFabricCluster](/powershell/module/azurerm.servicefabric/New-AzureRmServiceFabricCluster) a šablony nasadí nový cluster do Azure. Rutina vytvoří v Azure nový trezor klíčů, přidá nový certifikát podepsaný svým držitelem do služby key vault a stáhne soubor certifikátu místně.

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
Connect-AzureRmAccount
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>

# Create a new resource group for your deployment, and give it a name and a location.
New-AzureRmResourceGroup -Name $groupname -Location $clusterloc

# Create the Service Fabric cluster.
New-AzureRmServiceFabricCluster  -ResourceGroupName $groupname -TemplateFile "$templatepath\azuredeploy.json" `
-ParameterFile "$templatepath\azuredeploy.parameters.json" -CertificatePassword $certpwd `
-CertificateOutputFolder $certfolder -KeyVaultName $vaultname -KeyVaultResourceGroupName $vaultgroupname -CertificateSubjectName $subname

```

## <a name="connect-to-the-secure-cluster"></a>Připojení k zabezpečenému clusteru

Připojení ke clusteru pomocí modulu Powershellu pro Service Fabric nainstalovaný v Service Fabric SDK.  Nejprve nainstalujte certifikát do osobního úložiště (Moje) aktuálního uživatele na počítači. Spusťte následující příkaz PowerShellu:

```powershell
$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
        -FilePath C:\mycertificates\mysfcluster20170531104310.pfx `
        -Password $certpwd
```

Nyní jste připraveni připojit ke svému zabezpečenému clusteru.

Modul PowerShellu pro **Service Fabric** poskytuje řadu rutin pro správu clusterů Service Fabric, aplikací a služeb. Pomocí rutiny [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) se připojte k zabezpečenému clusteru. Podrobnosti o kryptografickém otisku certifikátu SHA1 a koncovém bodu připojení se nacházejí ve výstupu z předchozího kroku.

Pokud jste dříve nastavili ověření klienta služby Azure AD, spusťte následující příkaz: 
```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster123.southcentralus.cloudapp.azure.com:19000 `
        -KeepAliveIntervalInSec 10 `
        -AzureActiveDirectory `
        -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10
```

Pokud jste neměli nastavili ověření klienta služby Azure AD, spusťte následující příkaz:
```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster123.southcentralus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -FindType FindByThumbprint -FindValue C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -StoreLocation CurrentUser -StoreName My
```

Zkontrolujte, že jste připojeni a cluster je v pořádku pomocí [Get-ServiceFabricClusterHealth](/powershell/module/servicefabric/get-servicefabricclusterhealth) rutiny.

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

V dalších článcích v této sérii kurzů používá cluster, který jste vytvořili. Pokud nechcete ihned pokračovat dalším článkem, můžete [cluster odstranit](service-fabric-cluster-delete.md), aby se vám neúčtovaly poplatky.

## <a name="next-steps"></a>Další postup

Přejděte k následujícímu kurzu se naučíte škálování clusteru.

> [!div class="checklist"]
> * Vytvoření virtuální sítě v Azure pomocí PowerShellu
> * Vytvoření trezoru klíčů a nahrání certifikátu
> * Nastavení ověřování Azure Active Directory
> * Konfigurace shromažďování diagnostických dat
> * Nastavení služby Eventstoru
> * Nastavte si protokoly Azure monitoru
> * Vytvoření zabezpečeného clusteru Service Fabric v Azure PowerShellu
> * Zabezpečení clusteru pomocí certifikátu X.509
> * Připojení ke clusteru pomocí prostředí PowerShell
> * Odebrání clusteru

Teď přejděte k následujícímu kurzu se naučíte monitorovat svůj cluster.
> [!div class="nextstepaction"]
> [Monitorování clusteru](service-fabric-tutorial-monitor-cluster.md)

[template]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.json
[parameters]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.Parameters.json
