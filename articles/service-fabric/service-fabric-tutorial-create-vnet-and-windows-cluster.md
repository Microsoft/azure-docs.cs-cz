---
title: Vytvoření clusteru Service Fabric s Windows v Azure
description: V tomto kurzu se naučíte nasadit cluster Windows Service Fabric do virtuální sítě Azure a skupiny zabezpečení sítě pomocí PowerShellu.
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc
ms.openlocfilehash: a7390858e55a456ec5fb2f851be1a7443be97082
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86245037"
---
# <a name="tutorial-deploy-a-service-fabric-cluster-running-windows-into-an-azure-virtual-network"></a>Kurz: nasazení clusteru Service Fabric se systémem Windows do virtuální sítě Azure

Tento kurz je první částí série. Naučíte se, jak nasadit cluster Azure Service Fabric se systémem Windows do [virtuální sítě Azure](../virtual-network/virtual-networks-overview.md) a [skupiny zabezpečení sítě](../virtual-network/virtual-network-vnet-plan-design-arm.md) pomocí PowerShellu a šablony. Až budete hotovi, budete mít cluster spuštěný v cloudu, do kterého můžete nasazovat aplikace. Pokud chcete vytvořit cluster se systémem Linux, který používá rozhraní příkazového řádku Azure, přečtěte si téma [Vytvoření zabezpečeného clusteru Linux v Azure](service-fabric-tutorial-create-vnet-and-linux-cluster.md).

Tento kurz popisuje produkční scénář. Pokud chcete vytvořit menší cluster pro účely testování, přečtěte si téma [Vytvoření testovacího clusteru](./scripts/service-fabric-powershell-create-secure-cluster-cert.md).

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření virtuální sítě v Azure pomocí PowerShellu
> * Vytvoření trezoru klíčů a nahrání certifikátu
> * Nastavení Azure Active Directory ověřování
> * Konfigurace kolekce diagnostiky
> * Nastavení služby Eventstoru
> * Nastavení protokolů Azure Monitor
> * Vytvoření zabezpečeného clusteru Service Fabric v Azure PowerShellu
> * Zabezpečit cluster pomocí certifikátu X.509
> * Připojení ke clusteru pomocí prostředí PowerShell
> * Odebrat cluster

V této sérii kurzů se naučíte:
> [!div class="checklist"]
> * Vytvoření zabezpečeného clusteru v Azure
> * [Monitorování clusteru](service-fabric-tutorial-monitor-cluster.md)
> * [Horizontální snížení nebo navýšení kapacity clusteru](service-fabric-tutorial-scale-cluster.md)
> * [Upgrade modulu runtime clusteru](service-fabric-tutorial-upgrade-cluster.md)
> * [Odstranění clusteru](service-fabric-tutorial-delete-cluster.md)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky

Než začnete s tímto kurzem:

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Nainstalujte [sadu Service Fabric SDK a modul prostředí PowerShell](service-fabric-get-started.md).
* Nainstalujte [Azure PowerShell](/powershell/azure/install-az-ps).
* Přečtěte si klíčové koncepty [clusterů Azure](service-fabric-azure-clusters-overview.md).
* [Naplánujte a připravte](service-fabric-cluster-azure-deployment-preparation.md) nasazení produkčního clusteru.

Následující postupy vytvoří cluster se sedmi uzly Service Fabric. Pomocí [cenové kalkulačky Azure](https://azure.microsoft.com/pricing/calculator/) Vypočítejte náklady vzniklé spuštěním Service Fabric clusteru v Azure.

## <a name="download-and-explore-the-template"></a>Stažení a prozkoumání šablony

Stáhněte následující soubory šablon Azure Resource Manager:

* [azuredeploy.jsna][template]
* [azuredeploy.parameters.json][parameters]

Tato šablona nasadí zabezpečený cluster sedmi virtuálních počítačů a tří typů uzlů do virtuální sítě a skupiny zabezpečení sítě.  Další ukázkové šablony najdete na [GitHubu](https://github.com/Azure-Samples/service-fabric-cluster-templates). [azuredeploy.jsv systému][template] nasadí řadu prostředků včetně následujících.

### <a name="service-fabric-cluster"></a>Cluster Service Fabric

V prostředku **Microsoft.ServiceFabric/clusters** se konfiguruje cluster s Windows s těmito charakteristikami:

* Tři typy uzlů.
* Pět uzlů v primárním uzlu typ (konfigurovatelné v parametrech šablony) a jeden uzel v každém z dalších dvou typů uzlů.
* OPERAČNÍ systém: Windows Server 2016 Datacenter s kontejnery (konfigurovatelné v parametrech šablony).
* Zabezpečený certifikát (lze konfigurovat v parametrech šablony).
* [Reverzní proxy](service-fabric-reverseproxy.md) je povolený.
* [Služba DNS](service-fabric-dnsservice.md) je povolena.
* Bronzová [úroveň odolnosti](service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster) (konfigurovatelné v parametrech šablony)
* [Úroveň spolehlivosti](service-fabric-cluster-capacity.md#reliability-characteristics-of-the-cluster) stříbrného (konfigurovatelné v parametrech šablony).
* Koncový bod připojení klienta: 19000 (konfigurovatelné v parametrech šablony).
* Koncový bod brány HTTP: 19080 (konfigurovatelné v parametrech šablony).

### <a name="azure-load-balancer"></a>Azure Load Balancer

V prostředku **Microsoft. Network/loadBalancers** je nakonfigurován Nástroj pro vyrovnávání zatížení. Testy a pravidla se nastavují pro následující porty:

* Koncový bod připojení klienta: 19000
* koncový bod brány HTTP: 19080
* Port aplikace: 80
* Port aplikace: 443
* reverzní proxy server Service Fabric: 19081

Pokud jsou potřeba další porty aplikací, budete muset upravit prostředek **Microsoft. Network/loadBalancers** a prostředek **Microsoft. Network/networkSecurityGroups** , aby se provoz povolil.

### <a name="virtual-network-subnet-and-network-security-group"></a>Virtuální síť, podsíť a skupina zabezpečení sítě

Názvy virtuální sítě, podsítě a skupiny zabezpečení sítě jsou deklarované v parametrech šablony. Adresní prostory virtuální sítě a podsítě se taky deklarují v parametrech šablony a konfigurují v prostředku **Microsoft.Network/virtualNetworks**:

* Adresní prostor virtuální sítě: 172.16.0.0/20
* adresní prostor podsítě Service Fabric: 172.16.2.0/23

V prostředku **Microsoft.Network/networkSecurityGroups** jsou povolená následující pravidla pro příchozí provoz. Hodnoty portů můžete změnit změnou proměnných šablony.

* ClientConnectionEndpoint (TCP): 19000
* HttpGatewayEndpoint (HTTP/TCP): 19080
* SMB: 445
* Internodecommunication: 1025, 1026, 1027
* Rozsah dočasných portů: 49152 až 65534 (vyžaduje minimálně porty 256).
* Porty pro použití aplikací: 80 a 443
* Rozsah portů aplikace: 49152 až 65534 (používá se pro komunikaci mezi službami. V nástroji pro vyrovnávání zatížení nejsou otevřeny jiné porty).
* Všechny ostatní porty jsou blokované

Pokud jsou potřeba další porty aplikací, budete muset upravit prostředek **Microsoft. Network/loadBalancers** a prostředek **Microsoft. Network/networkSecurityGroups** , aby se provoz povolil.

### <a name="windows-defender"></a>Windows Defender
Ve výchozím nastavení je [antivirový program v programu Windows Defender](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-on-windows-server-2016) nainstalovaný a funkční na Windows serveru 2016. Uživatelské rozhraní je ve výchozím nastavení nainstalováno u některých SKU, ale není vyžadováno. Pro každý typ uzlu/sadu škálování virtuálního počítače, který je v šabloně deklarovaný, se k vyloučení Service Fabric adresářů a procesů používá [antimalwarové rozšíření Azure VM](../virtual-machines/extensions/iaas-antimalware-windows.md) :

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

Soubor s parametry [azuredeploy.parameters.json][parameters] deklaruje mnoho hodnot používaných pro nasazení clusteru a přidružených prostředků. Níže jsou uvedené parametry, které je potřeba upravit pro vaše nasazení:

**Parametr** | **Příklad hodnoty** | **Poznámky** 
|---|---|---|
|adminUserName|vmadmin| Uživatelské jméno správce pro virtuální počítače clusteru. [Požadavky na uživatelské jméno pro virtuální počítač](../virtual-machines/windows/faq.md#what-are-the-username-requirements-when-creating-a-vm) |
|adminPassword|Password#1234| Heslo správce pro virtuální počítače clusteru. [Požadavky na heslo pro virtuální počítač](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).|
|clusterName|mysfcluster123| Název clusteru. Může obsahovat jenom písmena a číslice. Může mít délku 3 až 23 znaků.|
|location|southcentralus| Umístění clusteru. |
|certificateThumbprint|| <p>Pokud vytváříte certifikát podepsaný svým držitelem nebo poskytujete soubor certifikátu, měla by být hodnota prázdná.</p><p>Pokud chcete použít existující certifikát, který se dříve odeslal do trezoru klíčů, vyplňte hodnotu kryptografického otisku certifikátu SHA1. Příklad: „6190390162C988701DB5676EB81083EA608DCCF3“.</p> |
|certificateUrlValue|| <p>Pokud vytváříte certifikát podepsaný svým držitelem nebo poskytujete soubor certifikátu, měla by být hodnota prázdná. </p><p>Pokud chcete použít existující certifikát, který byl dříve odeslán do trezoru klíčů, vyplňte URL certifikátu. Například "https: \/ /mykeyvault.Vault.Azure.NET:443/Secrets/mycertificate/02bea722c9ef4009a76c5052bcbf8346".</p>|
|sourceVaultValue||<p>Pokud vytváříte certifikát podepsaný svým držitelem nebo poskytujete soubor certifikátu, měla by být hodnota prázdná.</p><p>Pokud chcete použít existující certifikát, který byl dříve odeslán do trezoru klíčů, vyplňte hodnotu zdrojového trezoru. Například: /subscriptions/333cc2c84-12fa-5778-bd71-c71c07bf873f/resourceGroups/MyTestRG/providers/Microsoft.KeyVault/vaults/MYKEYVAULT</p>|

## <a name="set-up-azure-active-directory-client-authentication"></a>Nastavení ověřování klienta Azure Active Directory
U clusterů Service Fabric nasazených ve veřejné síti hostované v Azure je doporučení pro vzájemné ověřování mezi klientem a uzlem:
* Pro identitu klienta použijte Azure Active Directory.
* Použijte certifikát pro identitu serveru a šifrování TLS komunikace HTTP.

Nastavení Azure Active Directory (Azure AD) pro ověřování klientů pro Service Fabric cluster se musí provést před [vytvořením clusteru](#createvaultandcert). Azure AD umožňuje organizacím (označovaným jako klienti) spravovat přístup uživatelů k aplikacím. 

Cluster Service Fabric nabízí několik vstupních bodů ke svým funkcím správy, včetně webových [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) a sady [Visual Studio](service-fabric-manage-application-in-visual-studio.md). V důsledku toho vytvoříte dvě aplikace Azure AD pro řízení přístupu ke clusteru: jednu webovou aplikaci a jednu nativní aplikaci.  Po vytvoření aplikací přiřadíte uživatele k rolím jen pro čtení a správcům.

> [!NOTE]
> Před vytvořením clusteru je nutné provést následující kroky. Vzhledem k tomu, že skripty očekávají názvy a koncové body clusteru, hodnoty by měly být plánované a ne hodnoty, které jste již vytvořili.

V tomto článku předpokládáme, že jste už tenanta vytvořili. Pokud jste to ještě neudělali, začněte tím, že si přečtete [Azure Active Directory tenanta](../active-directory/develop/quickstart-create-new-tenant.md).

Pro zjednodušení kroků týkajících se konfigurace služby Azure AD pomocí Service Fabricho clusteru jsme vytvořili sadu skriptů prostředí Windows PowerShell. [Stáhněte si skripty](https://github.com/Azure-Samples/service-fabric-aad-helpers) do svého počítače.

### <a name="create-azure-ad-applications-and-assign-users-to-roles"></a>Vytváření aplikací Azure AD a přiřazení uživatelů k rolím
Vytvořte dvě aplikace Azure AD pro řízení přístupu ke clusteru: jednu webovou aplikaci a jednu nativní aplikaci. Po vytvoření aplikací, které reprezentují váš cluster, přiřaďte uživatele k [rolím, které podporuje Service Fabric](service-fabric-cluster-security-roles.md): jen pro čtení a správce.

Spusťte `SetupApplications.ps1` příkaz a jako parametry zadejte ID klienta, název clusteru a adresu URL odpovědi webové aplikace. Zadejte uživatelská jména a hesla pro uživatele. Například:

```powershell
$Configobj = .\SetupApplications.ps1 -TenantId '<MyTenantID>' -ClusterName 'mysfcluster123' -WebApplicationReplyUrl 'https://mysfcluster123.eastus.cloudapp.azure.com:19080/Explorer/index.html' -AddResourceAccess
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestUser' -Password 'P@ssword!123'
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestAdmin' -Password 'P@ssword!123' -IsAdmin
```

> [!NOTE]
> Pro národní cloudy (například Azure Government, Azure Čína, Azure Německo) zadejte `-Location` parametr.

*TenantId*nebo ID adresáře můžete najít v [Azure Portal](https://portal.azure.com). Vyberte **Azure Active Directory**  >  **vlastnosti** a zkopírujte hodnotu **ID adresáře** .

*Název_clusteru* slouží k vytvoření předpony aplikací služby Azure AD, které jsou vytvořeny pomocí skriptu. Nemusí přesně odpovídat skutečnému názvu clusteru. Usnadňuje mapování artefaktů Azure AD na Service Fabric používaný cluster.

*WebApplicationReplyUrl* je výchozí koncový bod, který Azure AD vrátí vašim uživatelům po dokončení přihlášení. Nastavte tento koncový bod jako koncový bod Service Fabric Explorer pro váš cluster, který je ve výchozím nastavení:

https:// &lt; cluster_domain &gt; : 19080/Explorer

Budete vyzváni k přihlášení k účtu, který má oprávnění správce pro tenanta Azure AD. Po přihlášení vytvoří skript webové a nativní aplikace, které reprezentují váš Service Fabric cluster. V aplikacích klienta v [Azure Portal](https://portal.azure.com)byste měli vidět dvě nové položky:

   * *Název clusteru* \_ Služby
   * *Název clusteru* \_ Služba

Skript vytiskne JSON vyžadovaný šablonou Správce prostředků při vytváření clusteru, takže je vhodné ponechat okno PowerShellu otevřené.

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

### <a name="add-azure-ad-configuration-to-use-azure-ad-for-client-access"></a>Přidání konfigurace Azure AD pro použití Azure AD pro klientský přístup
V [azuredeploy.jsna][template]portálu NAKONFIGURUJTE Azure AD v části **Microsoft. ServiceFabric/clustery** . Přidejte parametry pro ID tenanta, ID aplikace clusteru a ID klientské aplikace.  

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

Přidejte hodnoty parametrů do souboru parametrů [azuredeploy.parameters.js][parameters] . Například:

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
Pokud používáte Cluster Service Fabric, je vhodné shromáždit protokoly ze všech uzlů v centrálním umístění. Protokoly v centrálním umístění vám pomůžou analyzovat a řešit problémy v clusteru nebo problémy s aplikacemi a službami běžícími v tomto clusteru.

Jedním ze způsobů, jak nahrávat a shromažďovat protokoly, je použít rozšíření Azure Diagnostics (WAD), které nahrává protokoly do Azure Storage a má také možnost odesílat protokoly do Azure Application Insights nebo Event Hubs. Můžete také použít externí proces ke čtení událostí z úložiště a jejich umístění do produktu Analysis Platform, jako jsou protokoly Azure Monitor nebo jiné řešení pro analýzu protokolů.

Pokud s tímto kurzem pracujete, kolekce diagnostiky je už v [šabloně][template]nakonfigurovaná.

Pokud máte existující cluster, který nemá nasazenou diagnostiku, můžete ho přidat nebo aktualizovat prostřednictvím šablony clusteru. Upravte šablonu Správce prostředků, která se používá k vytvoření existujícího clusteru, nebo stažení šablony z portálu. Upravte template.jsv souboru prováděním následujících úloh:

Přidejte nový prostředek úložiště do části Resources (prostředky) v šabloně:
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

Dále přidejte parametry pro název a typ účtu úložiště do části Parameters (parametry) v šabloně. Nahraďte zástupný text název účtu úložiště místo pro název účtu úložiště, který chcete.

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

Dále přidejte rozšíření **IaaSDiagnostics** do pole rozšíření vlastnosti **VirtualMachineProfile** každého prostředku **Microsoft. COMPUTE/virtualMachineScaleSets** v clusteru.  Pokud používáte [ukázkovou šablonu][template], existují tři služby Virtual Machine Scale Sets (jedna pro každý typ uzlu v clusteru).

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
Služba Eventstoru je možnost monitorování v Service Fabric. Eventstoru poskytuje způsob, jak pochopit stav clusteru nebo úloh v daném časovém okamžiku. Eventstoru je stavová služba Service Fabric, která udržuje události z clusteru. Událost se zveřejňuje prostřednictvím Service Fabric Explorer, REST a rozhraní API. Eventstoru dotazuje cluster přímo, aby získal diagnostická data na jakékoli entitě v clusteru a měla by se používat k usnadnění:

* Diagnostikujte problémy při vývoji nebo testování nebo na místě, kde je možné používat sledovací kanál.
* Ověřte, že se správně zpracovávají akce správy, které provedete v clusteru.
* Získání "snímku" způsobu, jakým Service Fabric interakci s konkrétní entitou



Pokud chcete ve svém clusteru povolit službu Eventstoru, přidejte do vlastnosti **fabricSettings** prostředku **Microsoft. ServiceFabric/clustery** následující:

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

Protokoly Azure Monitor jsou naše doporučení pro monitorování událostí na úrovni clusteru. Chcete-li nastavit protokoly Azure Monitor pro monitorování clusteru, je nutné mít [povolenou diagnostiku pro zobrazení událostí na úrovni clusteru](#configure-diagnostics-collection-on-the-cluster).  

Pracovní prostor musí být připojený ke diagnostická data, která přicházejí z vašeho clusteru.  Tato data protokolu se ukládají v účtu úložiště *applicationDiagnosticsStorageAccountName* v tabulkách WADServiceFabric * Event, WADWindowsEventLogsTable a WADETWEventTable.

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

Přidejte rozšíření agenta Log Analytics do každé sady škálování virtuálního počítače v clusteru a připojte agenta k pracovnímu prostoru Log Analytics. To umožňuje shromažďovat diagnostická data o kontejnerech, aplikacích a monitorování výkonu. Když ho přidáte jako rozšíření prostředku sady škálování virtuálních počítačů, Azure Resource Manager zajistí, že se nainstalují na každý uzel, a to i při škálování clusteru.

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

Dále nastavte topologii sítě a nasaďte cluster Service Fabric. Šablona [azuredeploy.json][template] správce prostředků vytvoří virtuální síť, podsíť a skupinu zabezpečení sítě pro Service Fabric. Šablona také nasadí cluster s povoleným zabezpečením pomocí certifikátu. Pro produkční clustery použijte certifikát od certifikační autority jako certifikát clusteru. K zabezpečení testovacích clusterů můžete použít certifikát podepsaný svým držitelem.

Šablona v tomto článku nasadí cluster, který používá kryptografický otisk certifikátu k identifikaci certifikátu clusteru. Žádné dva certifikáty nemohou mít stejný kryptografický otisk, což ztěžuje správu certifikátů. Přepínání nasazeného clusteru z kryptografických otisků certifikátů na běžné názvy certifikátů usnadňuje správu certifikátů. Pokud se chcete dozvědět, jak cluster aktualizovat tak, aby používal běžné názvy certifikátů pro správu certifikátů, přečtěte si téma [Změna clusteru do společné správy názvů certifikátů](service-fabric-cluster-change-cert-thumbprint-to-cn.md).

### <a name="create-a-cluster-by-using-an-existing-certificate"></a>Vytvoření clusteru pomocí existujícího certifikátu

Následující skript používá rutinu [New-AzServiceFabricCluster](/powershell/module/az.servicefabric/New-azServiceFabricCluster) a šablonu k nasazení nového clusteru v Azure. Rutina vytvoří v Azure nový trezor klíčů a odešle váš certifikát.

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

Následující skript používá rutinu [New-AzServiceFabricCluster](/powershell/module/az.servicefabric/New-azServiceFabricCluster) a šablonu k nasazení nového clusteru v Azure. Rutina vytvoří nový trezor klíčů v Azure, přidá do trezoru klíčů nový certifikát podepsaný svým držitelem a stáhne soubor certifikátu místně.

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

Připojte se ke clusteru pomocí modulu Service Fabric PowerShellu nainstalovaného s Service Fabric SDK.  Nejprve nainstalujte certifikát do osobního úložiště (Moje) aktuálního uživatele na počítači. Spusťte následující příkaz PowerShellu:

```powershell
$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
        -FilePath C:\mycertificates\mysfcluster20170531104310.pfx `
        -Password $certpwd
```

Nyní jste připraveni připojit se k zabezpečenému clusteru.

Modul PowerShellu pro **Service Fabric** poskytuje řadu rutin pro správu clusterů Service Fabric, aplikací a služeb. Pomocí rutiny [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) se připojte k zabezpečenému clusteru. Podrobnosti o kryptografickém otisku certifikátu SHA1 a koncovém bodu připojení se nacházejí ve výstupu z předchozího kroku.

Pokud jste dříve nastavili ověřování klienta služby Azure AD, spusťte následující příkaz: 
```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster123.southcentralus.cloudapp.azure.com:19000 `
        -KeepAliveIntervalInSec 10 `
        -AzureActiveDirectory `
        -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10
```

Pokud jste nenastavili ověřování klienta služby Azure AD, spusťte následující příkaz:
```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster123.southcentralus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -FindType FindByThumbprint -FindValue C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -StoreLocation CurrentUser -StoreName My
```

Pomocí rutiny [Get-ServiceFabricClusterHealth](/powershell/module/servicefabric/get-servicefabricclusterhealth) ověřte, že jste připojení a že cluster je v pořádku.

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

V dalších článcích v této sérii kurzů se používá cluster, který jste vytvořili. Pokud nechcete ihned pokračovat dalším článkem, můžete [cluster odstranit](./service-fabric-tutorial-delete-cluster.md), aby se vám neúčtovaly poplatky.

## <a name="next-steps"></a>Další kroky

Pokud se chcete dozvědět, jak škálovat cluster, přejděte k následujícímu kurzu.

> [!div class="checklist"]
> * Vytvoření virtuální sítě v Azure pomocí PowerShellu
> * Vytvoření trezoru klíčů a nahrání certifikátu
> * Nastavení Azure Active Directory ověřování
> * Konfigurace kolekce diagnostiky
> * Nastavení služby Eventstoru
> * Nastavení protokolů Azure Monitor
> * Vytvoření zabezpečeného clusteru Service Fabric v Azure PowerShellu
> * Zabezpečit cluster pomocí certifikátu X.509
> * Připojení ke clusteru pomocí prostředí PowerShell
> * Odebrat cluster

Potom přejděte k následujícímu kurzu, kde se dozvíte, jak monitorovat cluster.
> [!div class="nextstepaction"]
> [Monitorování clusteru](service-fabric-tutorial-monitor-cluster.md)

[template]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.json
[parameters]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.Parameters.json
