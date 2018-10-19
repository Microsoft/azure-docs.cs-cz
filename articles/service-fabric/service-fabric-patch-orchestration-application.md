---
title: Aplikace orchestraci oprav Azure Service Fabric | Dokumentace Microsoftu
description: Aplikace automatizace oprav operačního systému v clusteru Service Fabric.
services: service-fabric
documentationcenter: .net
author: novino
manager: timlt
editor: ''
ms.assetid: de7dacf5-4038-434a-a265-5d0de80a9b1d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 5/22/2018
ms.author: nachandr
ms.openlocfilehash: a8b2070b6f5b10cb60c6658aefc8cc90331ecfd9
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/18/2018
ms.locfileid: "49409352"
---
# <a name="patch-the-windows-operating-system-in-your-service-fabric-cluster"></a>Opravy operačního systému Windows ve vašem clusteru Service Fabric

> [!div class="op_single_selector"]
> * [Windows](service-fabric-patch-orchestration-application.md)
> * [Linux](service-fabric-patch-orchestration-application-linux.md)
>
>

[Azure škálovací sady virtuálních počítačů automatické upgrady operačního systému image](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade) je nejvhodnějším postupem pro zachování opravit operačních systémů v Azure a aplikace orchestraci oprav (POA) představuje obálku kolem služby Service Fabric RepairManager systémy která umožňuje konfiguraci na základě plánování opravy operačního systému pro hostované clustery mimo Azure. POA není vyžadován pro hostované clustery mimo Azure, ale plánování opravu instalace upgradu domény, je potřeba opravovat hostitele clusterů Service Fabric bez jakýchkoli prostojů.

POA je aplikace Azure Service Fabric, který automatizuje operačního systému, použití dílčích oprav v clusteru Service Fabric bez jakýchkoli prostojů.

Aplikace orchestraci oprav poskytuje následující funkce:

- **Automatická instalace operačního systému aktualizaci**. Aktualizace operačního systému se automaticky stahují a instalují. Uzly clusteru restartují podle potřeby a bez výpadků clusteru.

- **Clustery použití dílčích oprav a stavu integrace**. Při použití aktualizací aplikace orchestraci oprav monitoruje stav uzlů clusteru. Uzly clusteru jsou upgradované jeden uzel nebo jednu upgradovací doménu najednou. Pokud stav clusteru ocitne mimo provoz kvůli procesu oprav, oprav zastavena zabránit zhorší problém.

## <a name="internal-details-of-the-app"></a>Interní podrobnosti aplikace

Orchestrace aplikaci patch se skládá z následujících tyto dílčí součásti:

- **Služba Koordinátor**: Tato stavová služba je zodpovědná za:
    - Koordinace úloh Windows Update v celém clusteru.
    - Ukládání výsledek dokončené operace s Windows Update.
- **Služba agenta uzlu**: tuto bezstavovou službu běží na všech uzlech clusteru Service Fabric. Služba je zodpovědná za:
    - Probíhá spuštění NTService agenta uzlu.
    - Monitorování NTService agenta uzlu.
- **Uzel agenta NTService**: Tento Windows NT service běží na vyšší úrovni oprávnění (systém). Naproti tomu službu agenta uzlu a služba Koordinátor běží na nižší úrovni oprávnění (síťová služba). Služba je odpovědný za provedení těchto úloh aktualizace Windows na všech uzlech clusteru:
    - Zakázat automatické aktualizace Windows na uzlu.
    - Stažení a instalaci aktualizací Windows podle zásad uživatel zadal.
    - Restartování počítače po instalaci aktualizace Windows.
    - Nahrává se výsledky aktualizace Windows ke službě Koordinátor.
    - Vytváření sestav stavu zprávy v případě, že operace selhala po vyčerpání všechny opakované pokusy.

> [!NOTE]
> Aplikace orchestraci oprav používá službu Service Fabric opravy správce systému zakázat nebo povolit uzlu a provádění kontroly stavu. Úloha opravy vytvořené aplikací orchestraci oprav sleduje průběh aktualizace Windows pro každý uzel.

## <a name="prerequisites"></a>Požadavky

### <a name="enable-the-repair-manager-service-if-its-not-running-already"></a>Povolit službu správce opravit (Pokud již není spuštěná)

Aplikace orchestraci oprav vyžaduje, aby na clusteru povolit služba opravy správce systému.

#### <a name="azure-clusters"></a>Clustery Azure

Clustery Azure na úrovni silver odolnosti mít ve službě Správce opravit ve výchozím nastavení povolená. Úroveň gold odolnosti Azure clusterů mohou nebo nemusí mít službu správce oprava povolí, v závislosti na tom, kdy byly vytvořeny tyto clustery. Clustery Azure na úrovni bronzové odolnosti se ve výchozím nastavení, není nutné servis manager povolena. Pokud služba již není povolena, zobrazí se v části systému služby v Service Fabric Exploreru.

##### <a name="azure-portal"></a>portál Azure
Nástroj pro správu oprav z webu Azure portal můžete povolit v době vytváření clusteru. Vyberte **zahrnují nástroj pro správu oprav** v části **doplňkové funkce** v době konfigurace clusteru.
![Správce opravy povolení Image z webu Azure portal](media/service-fabric-patch-orchestration-application/EnableRepairManager.png)

##### <a name="azure-resource-manager-deployment-model"></a>Model nasazení Azure Resource Manageru
Případně můžete použít [modelu nasazení Azure Resource Manageru](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) povolit službu správce opravy na nové a existující clustery Service Fabric. Získáte šablonu pro cluster, do které chcete nasadit. Můžete použít ukázkové šablony nebo vytvořit vlastní šablony modelu nasazení Azure Resource Manageru. 

Pokud chcete umožnit používání opravy Správce služby [šablony modelu nasazení Azure Resource Manageru](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm):

1. Nejprve zkontrolujte, že `apiversion` je nastavena na `2017-07-01-preview` pro `Microsoft.ServiceFabric/clusters` prostředků. Pokud se liší, pak je potřeba aktualizovat `apiVersion` hodnotě `2017-07-01-preview` nebo vyšší:

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. Nyní povolte službu správce opravit přidáním následujícího kódu `addonFeatures` části po `fabricSettings` části:

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

3. Po aktualizaci šablony clusteru se tyto změny použít a nechat dokončit upgrade. Nyní je vidět službu opravy správce systému ve vašem clusteru spuštěná. Je volána `fabric:/System/RepairManagerService` v části systému služby v Service Fabric Exploreru. 

### <a name="standalone-on-premises-clusters"></a>Samostatné clustery místních

Můžete použít [nastavení konfigurace pro samostatný cluster Windows](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest) povolit službu správce opravy na nové i stávající cluster Service Fabric.

Pokud chcete povolit službu správce opravy:

1. Nejprve zkontrolujte, že `apiversion` v [obecné clusterové konfigurace](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest#general-cluster-configurations) je nastavena na `04-2017` nebo vyšší:

    ```json
    {
        "name": "SampleCluster",
        "clusterConfigurationVersion": "1.0.0",
        "apiVersion": "04-2017",
        ...
    }
    ```

2. Nyní povolte službu správce opravit přidáním následujícího kódu `addonFeatures` části po `fabricSettings` jak vidíte níže:

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

3. Aktualizace manifestu clusteru se tyto změny pomocí manifestu clusteru aktualizované [vytvořit nový cluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-for-windows-server) nebo [upgradovat konfiguraci clusteru](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-upgrade-windows-server#Upgrade-the-cluster-configuration). Když cluster běží s manifestem clusteru aktualizované, nyní je vidět službu opravy správce systému spuštěné v clusteru, která se nazývá `fabric:/System/RepairManagerService`v části systémové služby v Service Fabric explorer části.

### <a name="disable-automatic-windows-update-on-all-nodes"></a>Zakázat automatické aktualizace Windows na všech uzlech

Automatické aktualizace Windows může vést ke ztrátě dostupnosti protože více uzlech clusteru můžete restartovat ve stejnou dobu. Oprava Orchestrace aplikace, ve výchozím nastavení, bude zakázat automatické aktualizace Windows na všech uzlech clusteru. Pokud nastavení spravuje správce nebo zásad skupiny, doporučujeme však explicitně nastavení zásad Windows Update "Oznámit před stažení".

## <a name="download-the-app-package"></a>Stáhněte si balíček aplikace

Aplikace spolu s instalační skripty si můžete stáhnout z [archivu odkaz](https://go.microsoft.com/fwlink/?linkid=869566).

Aplikace ve formátu sfpkg si můžete stáhnout z [sfpkg odkaz](https://aka.ms/POA/POA_v1.2.2.sfpkg). To je užitečné, [nasazení aplikace založené na Azure Resource Manageru](service-fabric-application-arm-resource.md).

## <a name="configure-the-app"></a>Konfigurace aplikace

Chování aplikace orchestraci oprav je možné nakonfigurovat podle svých potřeb. Přepište výchozí hodnoty předáním parametru aplikace během vytváření aplikace nebo aktualizace. Lze zadat parametry aplikace tak, že zadáte `ApplicationParameter` k `Start-ServiceFabricApplicationUpgrade` nebo `New-ServiceFabricApplication` rutiny.

|**Parametr**        |**Typ**                          | **Podrobnosti**|
|:-|-|-|
|MaxResultsToCache    |Dlouhé                              | Maximální počet výsledků Windows Update, které by měly být uložené v mezipaměti. <br>Výchozí hodnota je 3000 za předpokladu, že: <br> -Počet uzlů je 20. <br> -Počet aktualizací děje na uzel a měsíc je pět. <br> -Počet výsledků na operace může být 10. <br> – Výsledky po dobu posledních tří měsíců by měla být uložena. |
|TaskApprovalPolicy   |Výčet <br> {NodeWise, UpgradeDomainWise}                          |TaskApprovalPolicy označuje zásadu, která má být použit službou koordinátora k instalaci aktualizací Windows napříč uzly clusteru Service Fabric.<br>                         Povolené hodnoty jsou: <br>                                                           <b>NodeWise</b>. Aktualizace Windows je nainstalované jednoho uzlu současně. <br>                                                           <b>UpgradeDomainWise</b>. Aktualizace Windows je nainstalované jednu upgradovací doménu najednou. (Na maximum, můžete přejít všechny uzly, které patří do logických sítí pro aktualizace Windows.)<br> Odkazovat na [nejčastější dotazy k](#frequently-asked-questions) část o tom, jak rozhodnout, který je nejlépe hodí zásady pro váš cluster.
|LogsDiskQuotaInMB   |Dlouhé  <br> (Výchozí: 1024)               |Maximální velikost oprava Orchestrace aplikace přihlásí MB, který mohl být trvalý místně na uzlech.
| WUQuery               | řetězec<br>(Výchozí: "IsInstalled = 0")                | Použijte dotaz pro získání aktualizace Windows. Další informace najdete v tématu [WuQuery.](https://msdn.microsoft.com/library/windows/desktop/aa386526(v=vs.85).aspx)
| InstallWindowsOSOnlyUpdates | Logická hodnota <br> (výchozí: True)                 | Tento příznak umožňuje instalaci aktualizací operačního systému Windows.            |
| WUOperationTimeOutInMinutes | Int <br>(Výchozí: 90).                   | Určuje časový limit pro všechny operace aktualizace Windows (hledání nebo stáhnout nebo nainstalovat). Pokud se operace nedokončí v rámci zadaného časového limitu, je přerušeno.       |
| WURescheduleCount     | Int <br> (Výchozí: 5).                  | Maximální počet pokusů, které služba přeplánuje Windows update v případě, že docházet k chybě operace.          |
| WURescheduleTimeInMinutes | Int <br>(Výchozí: 30). | Interval, ve kterém přeplánuje služby Windows update v případě, že chyba přetrvává. |
| WUFrequency           | Řetězec s hodnotami oddělenými čárkou (výchozí: "Každý týden, Středa 7:00:00")     | Frekvence pro instalaci aktualizace Windows. Formát a možné hodnoty jsou: <br>– Měsíční, DD, hh, například každý měsíc, 5, 12: 22:32. <br> – Každý týden, den, hh: mm:, například týdně, úterý, 12:22:32.  <br> -Denní, hh: mm:, třeba každý den, 12:22:32.  <br> -Žádný označuje, že by se neměly provést aktualizace Windows.  <br><br> Všimněte si, že čas ve standardu UTC.|
| AcceptWindowsUpdateEula | Logická hodnota <br>(Výchozí: true) | Tím, že nastavíte tento příznak, tato aplikace přijme licenční smlouva koncového uživatele pro Windows Update jménem vlastníka počítače.              |

> [!TIP]
> Pokud chcete aktualizaci Windows okamžitě, nastavte `WUFrequency` relativní vůči času nasazení aplikace. Předpokládejme například, že máte cluster s pěti uzly testu a plánujete nasazení aplikace v přibližně 17:00:00 UTC. Pokud budete předpokládat, že upgrade aplikace nebo nasazení trvá 30 minut na maximum, nastavte WUFrequency jako "Každý den, 17:30:00."

## <a name="deploy-the-app"></a>Nasazení aplikace

1. Dokončete požadované kroky pro přípravu clusteru.
2. Nasazení aplikace orchestraci oprav stejně jako jakoukoli jinou aplikaci Service Fabric. Aplikaci můžete nasadit pomocí prostředí PowerShell. Postupujte podle kroků v [nasazení a odeberte aplikací pomocí prostředí PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications).
3. Konfigurace aplikace v době nasazování, předejte `ApplicationParamater` k `New-ServiceFabricApplication` rutiny. Pro usnadnění nabízíme skriptu Deploy.ps1 spolu s aplikace. Použití skriptu:

    - Připojení ke clusteru Service Fabric pomocí `Connect-ServiceFabricCluster`.
    - Spusťte skript prostředí PowerShell Deploy.ps1 příslušnou `ApplicationParameter` hodnotu.

> [!NOTE]
> Zachovat tento skript a složka aplikace PatchOrchestrationApplication ve stejném adresáři.

## <a name="upgrade-the-app"></a>Upgrade aplikace

Upgrade stávající aplikace pro orchestraci oprav pomocí prostředí PowerShell, postupujte podle kroků v [upgrade aplikace Service Fabric pomocí Powershellu](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade-tutorial-powershell).

## <a name="remove-the-app"></a>Odebrání aplikace

Odebrání aplikace, postupujte podle kroků v [nasazení a odeberte aplikací pomocí prostředí PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications).

Pro usnadnění nabízíme skriptu Undeploy.ps1 spolu s aplikace. Použití skriptu:

  - Připojení ke clusteru Service Fabric pomocí ```Connect-ServiceFabricCluster```.

  - Spusťte skript prostředí PowerShell Undeploy.ps1.

> [!NOTE]
> Zachovat tento skript a složka aplikace PatchOrchestrationApplication ve stejném adresáři.

## <a name="view-the-windows-update-results"></a>Zobrazení výsledků Windows Update

Aplikace orchestraci oprav zpřístupňuje rozhraní REST API k zobrazení historických výsledků pro uživatele. Příklad výsledku JSON:
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

Pole JSON jsou popsané níže.

Pole | Hodnoty | Podrobnosti
-- | -- | --
Výsledek | 0 – úspěšné<br> 1 - bylo úspěšně dokončeno s chybami<br> 2 – se nezdařilo<br> 3 - bylo přerušeno<br> 4 - bylo přerušeno s časovým limitem | Určuje výsledek operace (obvykle zahrnující instalace jedné nebo více aktualizací).
Kód výsledku | Stejný jako výsledek | Toto pole indikuje výsledek operace instalace pro individuální aktualizaci.
Typ operace | 1 – instalace<br> 0 - hledání a stahování.| Instalace je jediným typem operace OperationType, který by být standardně zobrazena ve výsledcích.
WindowsUpdateQuery | Výchozí hodnota je "IsInstalled = 0" |Windows aktualizujte dotaz, který byl použit k vyhledání aktualizací. Další informace najdete v tématu [WuQuery.](https://msdn.microsoft.com/library/windows/desktop/aa386526(v=vs.85).aspx)
RebootRequired | true – se vyžaduje restartování<br> false – nebyl požadován restart | Označuje, pokud restartování se vyžaduje pro dokončení instalace aktualizace.

Pokud žádná aktualizace je ještě naplánováno, výsledek JSON je prázdný.

Přihlásit se na klastru, aby byl dotaz Windows Update výsledky. Potom zjistit adresu repliky pro primární službu Koordinátor a stiskněte tlačítko adresu URL z prohlížeče: http://&lt;REPLIKY IP&gt;:&lt;ApplicationPort&gt;/PatchOrchestrationApplication/v1 / GetWindowsUpdateResults.

Koncový bod REST pro službu Koordinátor má dynamický port. Zkontrolujte přesnou adresu URL, naleznete v Service Fabric Explorer. Například jsou k dispozici na výsledky `http://10.0.0.7:20000/PatchOrchestrationApplication/v1/GetWindowsUpdateResults`.

![Obrázek koncového bodu REST](media/service-fabric-patch-orchestration-application/Rest_Endpoint.png)


Pokud v clusteru je povoleno reverzní proxy server, můžete přistupovat mimo cluster také adresu URL.
Koncový bod, který potřebuje k je http://&lt;SERVERURL&gt;:&lt;REVERSEPROXYPORT&gt;/PatchOrchestrationApplication/CoordinatorService/v1/GetWindowsUpdateResults.

Pokud chcete povolit reverzní proxy server v clusteru, postupujte podle kroků v [reverzní proxy server v Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy). 

> 
> [!WARNING]
> Po dokončení konfigurace reverzního proxy serveru jsou adresovatelné z mimo cluster všechny mikroslužby v clusteru, která zpřístupňují koncový bod HTTP.

## <a name="diagnosticshealth-events"></a>Diagnostika stavu události

### <a name="diagnostic-logs"></a>Diagnostické protokoly

Protokoly aplikací orchestraci oprav se shromažďuje jako součást protokolech modulu runtime Service Fabric.

V případě, že chcete zaznamenat protokoly prostřednictvím diagnostické nástroje/kanálu podle vašeho výběru. Oprava Orchestrace aplikace používá pod oprava zprostředkovatele ID protokolovat události prostřednictvím [eventsource](https://docs.microsoft.com/dotnet/api/system.diagnostics.tracing.eventsource?view=netframework-4.5.1)

- e39b723c-590c-4090-abb0-11e3e6616346
- fc0028ff-bfdc-499f-80dc-ed922c52c5e9
- 24afa313-0d3b-4c7c-b485-1047fd964b60
- 05dc046c-60e9-4ef7-965e-91660adffa68

### <a name="health-reports"></a>Sestav stavu

Aplikace orchestraci oprav, publikuje také sestav o stavu pro službu Koordinátor nebo službu agenta uzlu v následujících případech:

#### <a name="a-windows-update-operation-failed"></a>Windows Update operace se nezdařila.

Pokud na uzlu selže operace Windows Update, vygeneruje se sestava stavu na službu agenta uzlu. Podrobnosti sestavy health obsahují název problematické uzlu.

Po dokončení opravy je úspěšně problematické uzlu, jsou automaticky vymazány sestavy.

#### <a name="the-node-agent-ntservice-is-down"></a>NTService agenta uzlu je mimo provoz

Pokud NTService agenta uzlu neběží na uzlu, vygeneruje se sestava stavu úroveň upozornění na službu agenta uzlu.

#### <a name="the-repair-manager-service-is-not-enabled"></a>Služba Správce opravy není povolená.

Pokud se ve službě Správce opravit nenajde v clusteru, úroveň upozornění stavu sestava se generuje pro službu koordinátora.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

Otázka: **Proč vidím cluster v chybovém stavu, když je spuštěná aplikace orchestraci oprav?**

A. Během procesu instalace aplikace orchestraci oprav zakáže nebo restartuje uzly, které dočasně může vést k stav clusteru počítače.

Na základě zásad pro aplikace, buď jeden uzel může ujmout během operace opravy *nebo* celé doméně upgradu můžete přejít současně.

Na konci instalace aktualizace Windows se opětovně uzly povolena příspěvku restartování.

V následujícím příkladu clusteru přešel do stavu chyba dočasně vzhledem k tomu, že dva uzly se dolů a porušení zásady MaxPercentageUnhealthNodes. Chyba je dočasný, dokud probíhá operace opravy.

![Obrázek clusteru není v pořádku](media/service-fabric-patch-orchestration-application/MaxPercentage_causing_unhealthy_cluster.png)

Pokud se problém nevyřeší, naleznete v části řešení problémů.

Otázka: **Oprava Orchestrace aplikace je ve stavu s varováním**

A. Zaškrtněte, pokud chcete zjistit, zda je sestava stavu účtováno aplikace původní příčinu. Upozornění obvykle obsahuje podrobnosti o problému. Pokud je přechodný problém, očekává se automatické obnovení z tohoto stavu aplikace.

Otázka: **Co mám dělat, když můj cluster není v pořádku a je potřeba udělat při aktualizaci urgentní operačního systému?**

A. Aplikace orchestraci oprav neinstaluje aktualizace, zatímco clusteru není v pořádku. Pokuste se převést cluster do stavu v pořádku pro odblokování pracovní postup aplikace orchestraci oprav.

Otázka: **By měl nastavit TaskApprovalPolicy jako 'NodeWise' nebo "UpgradeDomainWise" pro cluster**

A. "UpgradeDomainWise" je celkový clusteru opravy rychleji opravuje všechny uzly, které patří k upgradovací doméně paralelně. To znamená, že uzly patřící do celé doméně upgradu budou k dispozici (v [zakázané](https://docs.microsoft.com/dotnet/api/system.fabric.query.nodestatus?view=azure-dotnet#System_Fabric_Query_NodeStatus_Disabled) stavu) během procesu oprav.

Naproti tomu "NodeWise zásad opravy jenom jeden uzel současně, to znamená, že celkový použití dílčích oprav clusteru by trvalo déle. Při dosažení maxima pouze jeden uzel by však bylo k dispozici (v [zakázané](https://docs.microsoft.com/dotnet/api/system.fabric.query.nodestatus?view=azure-dotnet#System_Fabric_Query_NodeStatus_Disabled) stavu) během procesu oprav.

Pokud váš cluster může tolerovat možnost, při použití dílčích oprav cyklu (kde N je počet upgradovacích domén ve vašem clusteru), pak můžete nastavit zásady jako "UpgradeDomainWise" a systémem N-1 počet upgradovacích domén, v opačném případě nastavte ji na "NodeWise".

Otázka: **Kolik času udělá proveďte opravu uzel?**

A. Opravy chyb uzlu může trvat minuty (třeba: [aktualizací definic Windows Defenderu](https://www.microsoft.com/wdsi/definitions)) hodin (například: [Windows kumulativní aktualizace](https://www.catalog.update.microsoft.com/Search.aspx?q=windows%20server%20cumulative%20update)). Čas potřebný k opravě uzel závisí hlavně na 
 - Velikost aktualizace
 - Počet aktualizací, které se mají použít v interval oprav
 - Čas potřebný k instalaci aktualizací, restartovat uzel (v případě potřeby) a dokončete postup instalace po restartování.
 - Výkon virtuálního počítače nebo počítače i síťové podmínky.

Otázka: **Jak dlouho trvá oprava celý cluster?**

A. Čas potřebný k opravě celý cluster, závisí na následujících faktorech:

- Čas potřebný k opravě uzlu.
- Zásady služby Koordinátor. – Výchozí zásady `NodeWise`, výsledkem opravy jenom jeden uzel v době, kterou by pomalejší než `UpgradeDomainWise`. Příklad: Pokud uzel trvá přibližně za 1 hodinu, který se má opravit, mohla o opravu 20 uzel (uzly stejného typu) cluster s 5 upgradovacích domén, každá obsahuje 4 uzly.
    - Mělo by to trvat přibližně 20 hodin na opravu celý cluster, pokud je zásada `NodeWise`
    - Pokud je zásada mělo stačit přibližně 5 hodin `UpgradeDomainWise`
- Zatížení clusteru – každé použití dílčích oprav operace vyžaduje přemístění do jiných uzlů clusteru k dispozici úloha zákazníka. Probíhá oprava uzlu by měly být v [zakázání](https://docs.microsoft.com/dotnet/api/system.fabric.query.nodestatus?view=azure-dotnet#System_Fabric_Query_NodeStatus_Disabling) stavu během této doby. Pokud cluster běží téměř zátěž ve špičce, zakázání by trvat delší dobu. Proto může pomalý za těchto podmínek přízvukový zobrazí celkový proces opravy.
- Libovolný cluster chyby stavu během opravy - [snížení](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthstate?view=azure-dotnet#System_Fabric_Health_HealthState_Error) v [stav clusteru](https://docs.microsoft.com/azure/service-fabric/service-fabric-health-introduction) by přerušit proces opravy. Tímto způsobem přidáte na celkový čas potřebný k opravě celý cluster.

Otázka: **Proč vidím některé aktualizace ve Windows Update výsledků získaných prostřednictvím rozhraní REST API, ale ne v části historie aktualizací Windows na počítači?**

A. Některé aktualizace produktu by být použit pouze v historii jejich příslušných aktualizací a oprav. Například aktualizace programu Windows Defender může nebo nemusí zobrazit v historii aktualizací Windows na Windows serveru 2016.

Otázka: **Je možné aplikaci orchestraci oprav o opravu clusteru dev (clusteru s jedním uzlem)?**

A. Ne, aplikace orchestraci oprav nelze použít na clusteru s jedním uzlem opravy. Toto omezení je záměrné, jako [service fabric systémové služby](https://docs.microsoft.com/azure/service-fabric/service-fabric-technical-overview#system-services) nebo všech zákaznických aplikací bude čelí výpadkům a proto všechny opravy úlohy pro opravy chyb by nikdy získáte schválené nástroj pro správu oprav.

## <a name="disclaimers"></a>Právní omezení

- Oprava Orchestrace aplikace přijme licenční smlouva koncového uživatele z Windows aktualizace jménem uživatele. Volitelně můžete toto nastavení lze vypnout v nastavení aplikace.

- Aplikace orchestraci oprav shromažďuje telemetrii ke sledování využití a výkonu. Telemetrie vaší aplikace se řídí nastavení telemetrie nastavení modulu runtime Service Fabric, (která je ve výchozím).

## <a name="troubleshooting"></a>Řešení potíží

### <a name="a-node-is-not-coming-back-to-up-state"></a>Uzel není vracející se zpět do stavu nahoru

**Uzel se mohly zaseknout ve zakázání stavu, protože**:

Bezpečnostní kontrola čeká na vyřízení. Chcete tuto situaci napravit, ujistěte se, že dostatek jsou k dispozici uzly v dobrém stavu.

**Uzel se mohly zaseknout v zakázaném stavu, protože**:

- Uzel byl zakázán ručně.
- Uzel byl zakázán z důvodu úlohu probíhající infrastruktury Azure.
- Uzel byl dočasně zakázané aplikace Orchestrace opravy na opravu uzlu.

**Uzel se mohly zaseknout ve dolů stavu, protože**:

- Uzel byl umístěn do stavu Dole ručně.
- Uzel Probíhá restartování počítače (který může spouštět aplikace orchestraci oprav).
- Uzel je vypnutý z důvodu chybného virtuálního počítače nebo počítače nebo síťové potíže s připojením.

### <a name="updates-were-skipped-on-some-nodes"></a>Aktualizace přeskočily. na některých uzlech

Aplikace orchestraci oprav, pokusí se nainstalovat aktualizace Windows podle přeplánování zásad. Služba se pokusí obnovit uzel a přeskočit aktualizaci souladu se zásadami pro aplikace.

V takovém případě se na službu agenta uzlu generování sestavy stavu úroveň pro upozornění. Výsledek pro aktualizace Windows obsahuje taky možný důvod selhání.

### <a name="the-health-of-the-cluster-goes-to-error-while-the-update-installs"></a>Stav clusteru přejde na chybu při instalaci aktualizace

Chybný aktualizace Windows může způsobit zastavení stav aplikace nebo na konkrétní uzel nebo doména upgradu clusteru. Aplikace orchestraci oprav navrátí jakékoli následné operace aktualizace Windows, dokud clusteru je opět v pořádku.

Správce musíte zasáhnout a zjistit, proč k problému, kvůli aktualizaci Windows aplikace nebo clusteru.

## <a name="release-notes"></a>Poznámky k verzi

### <a name="version-110"></a>Verze 1.1.0
- Veřejné vydané verze

### <a name="version-111"></a>Verze 1.1.1
- Oprava chyby v SetupEntryPoint z NodeAgentService, který zabránil instalaci NodeAgentNTService.

### <a name="version-120"></a>Verzi 1.2.0

- Opravy chyb kolem systém restartovat pracovního postupu.
- Oprava chyby při vytváření úlohy RM, kvůli které stavu nebyl děje kontrolu během přípravy úlohy opravit, podle očekávání.
- Změnit režim spuštění pro služby systému windows POANodeSvc z automatického na zpožděné automaticky.

### <a name="version-121"></a>Verze 1.2.1

- Oprava chyby v pracovním postupu vertikální snížení kapacity clusteru Zavedla uvolňování paměti kolekce logiku pro POA opravit úlohy patřící do neexistující uzly.

### <a name="version-122-latest"></a>Verze 1.2.2 (nejnovější)

- Různé opravy chyb.
- Nyní jsou podepsané binární soubory.
- odkaz ke stažení sfpkg nyní odkazuje na konkrétní verzi.
