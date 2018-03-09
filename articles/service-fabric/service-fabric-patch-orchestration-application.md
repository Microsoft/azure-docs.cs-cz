---
title: Azure Service Fabric oprava orchestration aplikace | Microsoft Docs
description: "Aplikace pro automatizaci opravy operačního systému na cluster Service Fabric."
services: service-fabric
documentationcenter: .net
author: novino
manager: timlt
editor: 
ms.assetid: de7dacf5-4038-434a-a265-5d0de80a9b1d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/07/2018
ms.author: nachandr
ms.openlocfilehash: 43a0675b1613e7bcf338537c1203de7df9a02fc4
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2018
---
# <a name="patch-the-windows-operating-system-in-your-service-fabric-cluster"></a>Oprava operačního systému Windows v clusteru Service Fabric

> [!div class="op_single_selector"]
> * [Windows](service-fabric-patch-orchestration-application.md)
> * [Linux](service-fabric-patch-orchestration-application-linux.md)
>
>

Oprava aplikace orchestration je aplikace Azure Service Fabric, která automatizuje operačního systému opravy na cluster Service Fabric bez výpadků.

Oprava aplikace orchestration poskytuje následující funkce:

- **Automatická instalace operačního systému aktualizaci**. Aktualizace operačního systému se automaticky stáhnout a nainstalovat. Uzly se restartují podle potřeby a bez výpadku clusteru.

- **Clustery opravy a stavu integrace**. Při použití aktualizací, aplikace orchestration oprava monitoruje stav uzlů clusteru. Upgradovaná jeden uzel nebo jednu upgradovací doménu jsou uzly clusteru současně. Pokud stav clusteru ocitne mimo provoz z důvodu proces oprav, opravy je zastavena, aby zhorší problém.

## <a name="internal-details-of-the-app"></a>Interní podrobnosti o aplikaci

Oprava aplikace orchestration se skládá z následujících tyto dílčí součásti:

- **Služba Koordinátor**: Tato stavová služba je zodpovědná za:
    - Koordinace úlohu služby Windows Update na celý cluster.
    - Ukládání výsledek dokončené operace aplikace Windows Update.
- **Služba agenta uzlu**: Tento bezstavové služby běží na všech uzlech clusteru Service Fabric. Služba je zodpovědná za:
    - Zavádění NTService agenta uzlu.
    - Monitorování NTService agenta uzlu.
- **Uzel agenta NTService**: Tento systém Windows NT služba běží na vyšší úrovni oprávnění (systém). Naproti tomu Služba agenta uzlu a službu koordinátora spustit na nižší úrovni oprávnění (síťová služba). Služba je zodpovědná za provádění následujících úloh aktualizace systému Windows na všech uzlech clusteru:
    - Zakázání automatické aktualizace systému Windows na uzlu.
    - Stažení a instalaci služby Windows Update podle zásad uživatel zadal.
    - Restartování počítače post instalace služby Windows Update.
    - Výsledky aktualizace systému Windows se nahrávají na službu Koordinátor.
    - Vytváření sestav stavu hlásí v případě, že operace se nezdařilo po jejím vyčerpání všechny opakování.

> [!NOTE]
> Oprava aplikace orchestration používá službu Service Fabric opravy správce systému pro zakázání nebo povolení uzlu a provádění kontroly stavu. Úloha opravy vytvořené aplikací orchestration oprava sleduje průběh Windows Update pro každý uzel.

## <a name="prerequisites"></a>Požadavky

### <a name="enable-the-repair-manager-service-if-its-not-running-already"></a>Povolit službu opravy správce (Pokud již není spuštěn)

Aplikace orchestration oprava vyžaduje službu opravy správce systému povolení v clusteru.

#### <a name="azure-clusters"></a>Azure clustery

Azure clustery ve vrstvě stříbrným odolnost mají službu opravy manager ve výchozím nastavení povolené. Azure clusterů ve vrstvě gold odolnost může nebo nemusí mít službu správce oprava povoleno, v závislosti na tom, kdy byly vytvořeny těchto clusterů. Azure clusterů ve vrstvě bronzovým odolnost ve výchozím nastavení, není nutné službu opravy manager povolena. Pokud služba je již povolen, zobrazí se jeho spuštění v části systémové služby v Service Fabric Exploreru.

##### <a name="azure-portal"></a>Azure Portal
Opravte manager z portálu Azure můžete povolit v době nastavování clusteru. Vyberte **zahrnují opravte Manager** možnost pod **funkcí doplňku** v době konfigurace clusteru.
![Image Manager opravy povolení z portálu Azure](media/service-fabric-patch-orchestration-application/EnableRepairManager.png)

##### <a name="azure-resource-manager-deployment-model"></a>Model nasazení Azure Resource Manager
Případně můžete použít [modelu nasazení Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) povolit službu opravy správce v nových nebo stávajících clusterů Service Fabric. Získáte šablonu pro cluster, který chcete nasadit. Můžete použít ukázkové šablony, nebo vytvořit vlastní šablony modelu nasazení Azure Resource Manager. 

Povolit pomocí service manager oprava [šablony modelu nasazení Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm):

1. Nejdřív zkontrolujte, zda `apiversion` je nastaven na `2017-07-01-preview` pro `Microsoft.ServiceFabric/clusters` prostředků. Pokud se liší, pak je potřeba aktualizovat `apiVersion` na hodnotu `2017-07-01-preview` nebo vyšší:

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. Teď povolit službu správce opravy přidáním následující `addonFeatures` části po `fabricSettings` části:

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

3. Po aktualizaci šablony clusteru se tyto změny použít, je a mohli dokončit upgrade. Nyní můžete vidět službu system manager oprava běžící v clusteru. Je volána `fabric:/System/RepairManagerService` v části systémové služby v Service Fabric Exploreru. 

### <a name="standalone-on-premises-clusters"></a>Samostatné místní clustery

Můžete použít [nastavení konfigurace pro samostatný cluster Windows](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest) povolit službu opravy správce na nové a stávající cluster Service Fabric.

Chcete-li povolit službu opravy správce:

1. Nejdřív zkontrolujte, zda `apiversion` v [konfigurace obecné clusteru](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest#general-cluster-configurations) je nastaven na `04-2017` nebo vyšší:

    ```json
    {
        "name": "SampleCluster",
        "clusterConfigurationVersion": "1.0.0",
        "apiVersion": "04-2017",
        ...
    }
    ```

2. Teď povolit service manager opravy přidáním následující `addonFeatures` části po `fabricSettings` části, jak je uvedeno níže:

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

3. Aktualizovat manifestu clusteru se tyto změny pomocí v manifestu clusteru aktualizované [vytvoření nového clusteru](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-for-windows-server) nebo [upgradovat konfiguraci clusteru](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-upgrade-windows-server#Upgrade-the-cluster-configuration). Jakmile v clusteru je spuštěn s manifestu aktualizované clusteru, se zobrazí služba system manager oprava běžící v clusteru, který se označuje `fabric:/System/RepairManagerService`v systému služby v Service Fabric explorer části.

### <a name="disable-automatic-windows-update-on-all-nodes"></a>Zakázat automatické aktualizace systému Windows na všech uzlech

Automatické aktualizace systému Windows můžou způsobit ztrátu dostupnosti, protože více uzlech clusteru můžete restartovat ve stejnou dobu. Oprava aplikace orchestration, ve výchozím nastavení, pokusí se zakázat automatické aktualizace systému Windows na všech uzlech clusteru. Pokud nastavení spravuje správce nebo zásad skupiny, doporučujeme však explicitně nastavení zásad Windows Update "Oznámit před stáhnout".

## <a name="download-the-app-package"></a>Stáhněte si balíček aplikace

Aplikaci společně s skripty instalace si můžete stáhnout z [archivu odkaz](https://go.microsoft.com/fwlink/?linkid=869566).

Aplikace ve formátu sfpkg si můžete stáhnout z [sfpkg odkaz](https://go.microsoft.com/fwlink/?linkid=869567). To je užitečné, [Azure Resource Manager na základě nasazení aplikace](service-fabric-application-arm-resource.md).

## <a name="configure-the-app"></a>Konfigurace aplikace

Chování aplikace orchestration oprava lze nakonfigurovat podle svých potřeb. Přepište výchozí hodnoty předáním v aplikaci parametru během vytváření aplikace nebo aktualizace. Parametry aplikačního lze zadat zadáním `ApplicationParameter` k `Start-ServiceFabricApplicationUpgrade` nebo `New-ServiceFabricApplication` rutiny.

|**Parameter**        |**Typ**                          | **Podrobnosti**|
|:-|-|-|
|MaxResultsToCache    |Dlouhé                              | Maximální počet výsledků Windows Update, které by měl být mezipaměti. <br>Výchozí hodnota je 3000 za předpokladu, že: <br> -Počet uzlů je 20. <br> -Počet aktualizací děje na uzlu za měsíc je pět. <br> -Počet výsledků na operace může být 10. <br> -By měly být uložené výsledky pro poslední tři měsíce. |
|TaskApprovalPolicy   |výčet <br> { NodeWise, UpgradeDomainWise }                          |TaskApprovalPolicy označuje zásady, které má být používána službu koordinátora k instalaci aktualizací Windows mezi uzly clusteru Service Fabric.<br>                         Povolené hodnoty jsou: <br>                                                           <b>NodeWise</b>. Windows Update je nainstalovaná jednoho uzlu současně. <br>                                                           <b>UpgradeDomainWise</b>. Windows Update je nainstalovaná jednu upgradovací doménu najednou. (Na maximum, můžete přejít všechny uzly, které patří k doméně upgradu pro Windows Update.)
|LogsDiskQuotaInMB   |Dlouhé  <br> (Výchozí: 1024)               |Maximální velikost oprava orchestration aplikace přihlásí MB, který můžete nastavit jako trvalý, místně na uzlech.
| WUQuery               | řetězec<br>(Výchozí: "IsInstalled = 0")                | Dotaz pro získání aktualizace systému Windows. Další informace najdete v tématu [WuQuery.](https://msdn.microsoft.com/library/windows/desktop/aa386526(v=vs.85).aspx)
| InstallWindowsOSOnlyUpdates | Logická hodnota <br> (výchozí: True)                 | Tento příznak umožňuje instalaci aktualizací operačního systému Windows.            |
| WUOperationTimeOutInMinutes | Int <br>(Výchozí: 90).                   | Určuje časový limit pro všechny operace služby Windows Update (hledání nebo stáhnout nebo nainstalovat). Pokud operaci nelze dokončit v rámci zadaného časového limitu, byl přerušen.       |
| WURescheduleCount     | Int <br> (Výchozí: 5).                  | Maximální počet časy službu přeplánuje Windows aktualizovat v případě, že operace selže trvalé.          |
| WURescheduleTimeInMinutes | Int <br>(Výchozí: 30). | Interval, ve kterém přeplánuje službu Windows update v případě, že chyba přetrvávat. |
| WUFrequency           | Řetězce s hodnotami oddělenými čárkou (výchozí: "Každý týden, středu, 7:00:00")     | Četnost pro instalaci služby Windows Update. Formát a možné hodnoty jsou: <br>-Měsíců, DD, hh, například každý měsíc, 5, 12: 22:32. <br> -Každý týden, den, hh: mm:, pro příklad, týdně, úterý, 12:22:32.  <br> -Denní, hh: mm:, např. denně, 12:22:32.  <br> -Žádný označuje, že by neměl Windows Update provést.  <br><br> Všimněte si, že je ve formátu UTC.|
| AcceptWindowsUpdateEula | Logická hodnota <br>(Výchozí: true) | Nastavením tohoto příznaku aplikace přijímá licenční smlouvy s koncovým uživatelem pro Windows Update jménem vlastník počítače.              |

> [!TIP]
> Pokud chcete, aby Windows Update provést okamžitě, nastavte `WUFrequency` podle času nasazení aplikace. Předpokládejme například, že máte testovací pěti uzly clusteru a plánování nasazení aplikace na přibližně 5:00 PM UTC. Pokud budete předpokládat, že upgradu aplikace nebo nasazení maximálně trvá 30 minut, nastavte WUFrequency jako "Denně, 17:30:00."

## <a name="deploy-the-app"></a>Nasazení aplikace

1. Dokončete všechny požadované kroky při přípravě clusteru.
2. Nasaďte aplikaci orchestration oprava stejně jako jiná aplikace Service Fabric. Aplikaci můžete nasadit pomocí prostředí PowerShell. Postupujte podle kroků v [nasazení a odeberte aplikací pomocí prostředí PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications).
3. Konfigurace aplikace v době nasazení, předat `ApplicationParamater` k `New-ServiceFabricApplication` rutiny. Pro usnadnění nabízíme skript Deploy.ps1 spolu s aplikací. Použití skriptu:

    - Připojení ke clusteru Service Fabric pomocí `Connect-ServiceFabricCluster`.
    - Spustit skript prostředí PowerShell Deploy.ps1 s příslušnou `ApplicationParameter` hodnotu.

> [!NOTE]
> Zachovat skript a složce aplikace PatchOrchestrationApplication ve stejném adresáři.

## <a name="upgrade-the-app"></a>Upgrade aplikace

Pokud chcete upgradovat stávající aplikace orchestration oprava pomocí prostředí PowerShell, postupujte podle kroků v [upgradu aplikace Service Fabric pomocí prostředí PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade-tutorial-powershell).

## <a name="remove-the-app"></a>Odeberte aplikaci

Chcete-li odebrat aplikace, postupujte podle kroků v [nasazení a odeberte aplikací pomocí prostředí PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications).

Pro usnadnění nabízíme skript Undeploy.ps1 spolu s aplikací. Použití skriptu:

  - Připojení ke clusteru Service Fabric pomocí ```Connect-ServiceFabricCluster```.

  - Spusťte skript prostředí PowerShell Undeploy.ps1.

> [!NOTE]
> Zachovat skript a složce aplikace PatchOrchestrationApplication ve stejném adresáři.

## <a name="view-the-windows-update-results"></a>Zobrazení výsledků Windows Update

Oprava aplikace orchestration zpřístupňuje rozhraní REST API zobrazit historická výsledky pro uživatele. Příklad výsledku JSON:
```json
[
  {
    "NodeName": "_stg1vm_1",
    "WindowsUpdateOperationResults": [
      {
        "OperationResult": 0,
        "NodeName": "_stg1vm_1",
        "OperationTime": "2017-05-21T11:46:52.1953713Z",
        "UpdateDetails": [
          {
            "UpdateId": "7392acaf-6a85-427c-8a8d-058c25beb0d6",
            "Title": "Cumulative Security Update for Internet Explorer 11 for Windows Server 2012 R2 (KB3185319)",
            "Description": "A security issue has been identified in a Microsoft software product that could affect your system. You can help protect your system by installing this update from Microsoft. For a complete listing of the issues that are included in this update, see the associated Microsoft Knowledge Base article. After you install this update, you may have to restart your system.",
            "ResultCode": 0
          }
        ],
        "OperationType": 1,
        "WindowsUpdateQuery": "IsInstalled=0",
        "WindowsUpdateFrequency": "Daily,10:00:00",
        "RebootRequired": false
      }
    ]
  },
  ...
]
```

Dále jsou uvedená pole JSON.

Pole | Hodnoty | Podrobnosti
-- | -- | --
Výsledek | 0 – úspěšné<br> 1 - bylo úspěšně dokončeno s chybami<br> 2 – se nezdařilo<br> 3 - přerušeno<br> 4 - přerušena s vypršením časového limitu | Určuje výsledek celkové operace (obvykle zahrnující instalaci jedné nebo více aktualizací).
resultCode | Stejné jako výsledek | Toto pole určuje výsledek operace instalace pro individuální aktualizaci.
Typ operace | 1 - instalace<br> 0 – hledání a stahování.| Instalace je pouze typ operace, které se zobrazí ve výsledcích ve výchozím nastavení.
WindowsUpdateQuery | Výchozí hodnota je "IsInstalled = 0" |Služby Windows update dotaz, který byl použit k vyhledání aktualizací. Další informace najdete v tématu [WuQuery.](https://msdn.microsoft.com/library/windows/desktop/aa386526(v=vs.85).aspx)
RebootRequired | true – nebyla nutná restartování<br> false – nebyl požadován restart | Označuje, pokud byl dokončení instalace aktualizace vyžaduje restartování.

Pokud žádná aktualizace je ještě naplánováno, výsledkem JSON je prázdný.

Přihlaste se ke clusteru k dotazu služby Windows Update výsledky. Poté zjistit adresu repliky pro primární službu koordinátora a stiskněte tlačítko adresu URL z prohlížeče: http://&lt;REPLIKY IP&gt;:&lt;ApplicationPort&gt;/PatchOrchestrationApplication/v1 / GetWindowsUpdateResults.

Koncový bod REST pro službu koordinátora má dynamický port. Pokud chcete zkontrolovat přesnou adresu URL, najdete v Service Fabric Exploreru. Například jsou k dispozici na výsledky `http://10.0.0.7:20000/PatchOrchestrationApplication/v1/GetWindowsUpdateResults`.

![Obrázek koncový bod REST](media/service-fabric-patch-orchestration-application/Rest_Endpoint.png)


Pokud je v clusteru je povoleno reverzní proxy server, můžete adresu URL z mimo cluster také přistupovat.
Koncový bod, který musí být dosáhl je http://&lt;SERVERURL&gt;:&lt;REVERSEPROXYPORT&gt;/PatchOrchestrationApplication/CoordinatorService/v1/GetWindowsUpdateResults.

Pokud chcete povolit reverzní proxy server v clusteru, postupujte podle kroků v [reverzní proxy server v Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy). 

> 
> [!WARNING]
> Po dokončení konfigurace reverzní proxy server, všechny malých služby v clusteru, které zveřejňují koncový bod protokolu HTTP jsou adresovatelné z mimo cluster.

## <a name="diagnosticshealth-events"></a>Diagnostika stavu události

### <a name="diagnostic-logs"></a>Diagnostické protokoly

Oprava orchestration aplikace protokoly se shromažďují v rámci protokoly modulu runtime Service Fabric.

V případě, že chcete zaznamenat protokoly pomocí diagnostických nástrojů nebo kanálu podle svého výběru. Oprava orchestration aplikace používá níže pevné zprostředkovatele ID do protokolu událostí prostřednictvím [eventsource](https://docs.microsoft.com/dotnet/api/system.diagnostics.tracing.eventsource?view=netframework-4.5.1)

- e39b723c-590c-4090-abb0-11e3e6616346
- fc0028ff-bfdc-499f-80dc-ed922c52c5e9
- 24afa313-0d3b-4c7c-b485-1047fd964b60
- 05dc046c-60e9-4ef7-965e-91660adffa68

### <a name="health-reports"></a>Sestavy o stavu

Oprava aplikace orchestration, publikuje také sestavy stavu proti službu koordinátora nebo službu Agent uzlu v následujících případech:

#### <a name="a-windows-update-operation-failed"></a>Windows Update operace se nezdařila.

Pokud na uzlu selže operace služby Windows Update, sestavy stavu se vygeneruje pro službu Agent uzlu. Podrobnosti stavu sestavy obsahují název problematické uzlu.

Po dokončení opravy je úspěšně na uzlu problematické, jsou automaticky vymazány sestavy.

#### <a name="the-node-agent-ntservice-is-down"></a>NTService agenta uzlu je mimo provoz

Pokud NTService agenta uzlu je vypnutý na uzlu, vygeneruje se na službu Agent uzlu sestavy stavu úroveň upozornění.

#### <a name="the-repair-manager-service-is-not-enabled"></a>Služba Správce opravy není povolena.

Pokud službu opravy manager nebyl nalezen v clusteru, se generuje sestavy stavu úroveň upozornění pro službu koordinátora.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

OTÁZKY. **Proč se při spuštěné aplikaci orchestration oprava vidí Moje clusteru v chybovém stavu?**

A. Během procesu instalace aplikace orchestration oprava zakáže nebo restartování uzlů, které mohou dočasně stav clusteru směrem dolů.

Na základě zásad pro aplikace, buď jeden uzel můžete přejděte během operace oprav *nebo* celá doména upgradu můžete současně přejděte.

Na konci instalace služby Windows Update jsou opětovně uzly povolena post restartování.

V následujícím příkladu clusteru byl přesunut do chybový stav dočasně vzhledem k tomu, že dva uzly byly dolů a porušení zásady MaxPercentageUnhealthNodes. Chyba je dočasný, dokud probíhá oprav operaci.

![Bitové kopie není v pořádku clusteru](media/service-fabric-patch-orchestration-application/MaxPercentage_causing_unhealthy_cluster.png)

Pokud potíže potrvají, naleznete v části řešení potíží.

OTÁZKY. **Oprava aplikace orchestration je ve stavu upozornění**

A. Zkontrolujte, zda sestavy stavu odeslány vzhledem k aplikaci je hlavní příčinu. Upozornění obvykle obsahuje podrobnosti o problému. Pokud přechodný problém se předpokládá, že aplikace je automaticky obnovit z tohoto stavu.

OTÁZKY. **Co mám dělat, když můj clusteru není v pořádku a je třeba provést aktualizaci naléhavé operačního systému?**

A. Oprava aplikace orchestration neinstaluje aktualizace při clusteru není v pořádku. Zkuste a převeďte cluster do stavu v pořádku odblokovat pracovního postupu oprava orchestration aplikace.

OTÁZKY. **Proč opravy napříč clustery trvá tak dlouho spustit?**

A. Čas potřebný oprava aplikace orchestration je ve většině případů závislé na následujících faktorech:

- Zásada službu koordinátora. 
  - Výchozí zásady `NodeWise`, výsledkem opravy jenom jeden uzel v čase. Zejména v případě, že je větší, clusteru, doporučujeme použít `UpgradeDomainWise` zásadu, abyste dosáhli rychlejší opravy napříč clustery.
- Počet aktualizací, které jsou k dispozici ke stažení a instalaci. 
- Průměrný čas potřebný ke stažení a instalaci aktualizace, které by neměl překročit několik hodin.
- Výkon virtuálního počítače a šířku pásma sítě.

OTÁZKY. **Proč vidí některé aktualizace ve výsledcích Windows Update získat prostřednictvím REST API, ale ne v historii služby Windows Update v počítači?**

A. Některé aktualizace produktu se objeví jenom v historii jejich příslušné aktualizace nebo opravy. Například aktualizace programu Windows Defender se nezobrazí v historii služby Windows Update v systému Windows Server 2016.

## <a name="disclaimers"></a>Právní omezení

- Oprava aplikace orchestration přijme licenční smlouvy s koncovým uživatelem z Windows Update jménem uživatele. Volitelně může být vypnuto nastavení v konfiguraci aplikace.

- Oprava aplikace orchestration shromažďuje telemetrická data pro sledování využití a výkonu. Telemetrie aplikace odpovídá nastavení telemetrie nastavení modulu runtime Service Fabric, (který je ve výchozím).

## <a name="troubleshooting"></a>Řešení potíží

### <a name="a-node-is-not-coming-back-to-up-state"></a>Uzel není vracející se zpět do až stavu

**Uzel může zasekla v automatickém zakázání stavu, protože**:

Kontrola zabezpečení čeká na vyřízení. Chcete-li opravit tuto situaci, zajistěte, aby byly dostatek uzlů k dispozici v dobrém stavu.

**Uzel se mohla zastavit v zakázaném stavu, protože**:

- Uzlu zakázal ručně.
- Uzel byl zakázán z důvodu úlohu probíhající infrastruktury Azure.
- Uzlu byla dočasně zakázána správcem aplikace oprava orchestration opravit uzlu.

**Uzel může zasekla v automatickém dolů stavu, protože**:

- Uzel byla zařazena v dolů stavu ručně.
- Uzlu právě probíhá restartování počítače (který může být aktivovány oprava orchestration aplikace).
- Uzel je mimo provoz z důvodu vadné virtuálního počítače nebo počítače nebo síťové problémy s připojením.

### <a name="updates-were-skipped-on-some-nodes"></a>Aktualizace, které byly přeskočeny na některých uzlech

Oprava aplikace orchestration se pokusí o instalaci služby Windows update podle přeplánování zásad. Služba se pokusí obnovit uzel a přeskočit aktualizace podle zásad aplikací.

V takovém případě se vygeneruje sestavy stavu úroveň upozornění pro službu Agent uzlu. Výsledek pro služby Windows Update také obsahuje možný důvod selhání.

### <a name="the-health-of-the-cluster-goes-to-error-while-the-update-installs"></a>Stav clusteru přejde k chybě při instalaci aktualizace

Vadný služby Windows update můžete zahrnout dolů stav aplikace nebo clusteru v konkrétním uzlu nebo doméně pro upgrade. Oprava aplikace orchestration ze všechny následné operace služby Windows Update, dokud znovu je v pořádku clusteru.

Správce musí zasáhnout a zjistit, proč k problému z důvodu Windows Update aplikace nebo clusteru.

## <a name="release-notes"></a>Poznámky k verzi

### <a name="version-110"></a>Verze 1.1.0
- Veřejné verze

### <a name="version-111"></a>Verze 1.1.1
- Pevná chyby ve SetupEntryPoint z NodeAgentService, která zabránila instalace NodeAgentNTService.

### <a name="version-120"></a>Verze 1.2.0

- Opravy chyb kolem systém restartovat pracovního postupu.
- Oprava chyby při vytváření úlohy RM kvůli které stavu nebyl kontrola během přípravy úlohy oprava děje podle očekávání.
- Změnit režim spuštění pro služby systému windows POANodeSvc z automatické na odložené automaticky.

### <a name="version-121-latest"></a>Verze 1.2.1 (nejnovější)

- Oprava chyby v pracovním postupu clusteru vertikální snížení kapacity. Zavedly logiku kolekce uvolňování paměti pro úlohy oprava druhů patřící do neexistující uzly.
