---
title: Oprava operačního systému Windows v clusteru Service Fabric
description: Tento článek popisuje, jak automatizovat opravy operačního systému v clusteru Service Fabric pomocí aplikace Orchestrace opravy.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: de7dacf5-4038-434a-a265-5d0de80a9b1d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/01/2019
ms.author: atsenthi
ms.openlocfilehash: 857a4da0b24d600ecc572933af578e2e8faf501a
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366318"
---
# <a name="patch-the-windows-operating-system-in-your-service-fabric-cluster"></a>Oprava operačního systému Windows v clusteru Service Fabric

> 
> [!IMPORTANT]
> dubna 2019 již není podporována aplikace Pro orchestraci opravy verze 1.2.*. Nezapomeňte upgradovat na nejnovější verzi.

> [!NOTE]
> Získání [automatických upgradů bitových obrázků operačního systému ve škálovací sadě virtuálních strojů](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade) je osvědčeným postupem pro udržování opravy operačního systému v Azure. Automatické upgrady bitových obrázků na základě škálování virtuálního počítače budou vyžadovat stříbrnou nebo větší odolnost na škálovací sadě.
>

 Aplikace Orchestrace oprav (POA) je obálka kolem služby Azure Service Fabric Repair Manager, která umožňuje plánování oprav operačního systému založené na konfiguraci pro clustery hostované mimo Azure. POA není vyžadováno pro hostované clustery než Azure, ale plánování instalace opravy podle aktualizační domény je nutné opravit service fabric clusteru hostitelů bez prostojů.

POA je aplikace Service Fabric, která automatizuje opravy operačního systému v clusteru Service Fabric bez prostojů.

POA poskytuje následující funkce:

- **Automatická instalace aktualizace operačního systému**. Aktualizace operačního systému jsou automaticky staženy a nainstalovány. Uzly clusteru jsou restartovány podle potřeby bez prostojů clusteru.

- **Opravy clusteru a integrace stavu**. Při použití aktualizací podispozici, sleduje stav uzlů clusteru. Uzly clusteru jsou aktualizovány jeden uzel najednou nebo jeden aktualizační domény najednou. Pokud stav clusteru klesne z důvodu procesu opravy, opravy je zastaven, aby se zabránilo zhoršení problému.

## <a name="internal-details-of-poa"></a>Interní detaily POA

POA se skládá z následujících dílčích složek:

- **Služba koordinátora**: Tato stavová služba je zodpovědná za:
    - Koordinace úlohy služby Windows Update v celém clusteru.
    - Uložení výsledku dokončených operací služby Windows Update.

- **Služba agenta uzlu**: Tato bezstavová služba je spuštěna na všech uzlech clusteru Service Fabric. Služba je zodpovědná za:
    - Zaváděcí agent uzlu NTService.
    - Sledování agenta uzlu NTService.

- **Agent uzlu NTService**: Tato služba systému Windows NT je spuštěna s oprávněním vyšší úrovně (SYSTEM). Naproti tomu služba agenta uzlu a služba koordinátora jsou spuštěny s oprávněním nižší úrovně (NETWORK SERVICE). Služba je zodpovědná za provádění následujících úloh služby Windows Update ve všech uzlech clusteru:
    - Zakázání automatických aktualizací systému Windows v uzlu.
    - Stahování a instalace aktualizací systému Windows v souladu se zásadami, které uživatel poskytl.
    - Restartování instalace počítače po aktualizacích systému Windows.
    - Nahrání výsledků aktualizací systému Windows do služby koordinátora.
    - Hlášení zpráv o stavu, pokud se operace nezdařila poté, co vyčerpá všechny opakování.

> [!NOTE]
> PoA používá službu Service Fabric Repair Manager zakázat nebo povolit uzel a provádět kontroly stavu. Úloha opravy vytvořená společností POA sleduje průběh služby Windows Update pro každý uzel.

## <a name="prerequisites"></a>Požadavky

> [!NOTE]
> Požadovaná minimální verze rozhraní .NET Framework je 4.6.

### <a name="enable-the-repair-manager-service-if-its-not-running-already"></a>Povolení služby Správce oprav (pokud ještě není spuštěná)

POA vyžaduje, aby byla v clusteru povolena služba Správce oprav.

#### <a name="azure-clusters"></a>Clustery Azure

Clustery Azure ve stříbrné vrstvě odolnosti mají ve výchozím nastavení povolenou službu Repair Manager. Clustery Azure ve zlaté vrstvě odolnosti mohou nebo nemusí mít povolenou službu Správce oprav v závislosti na tom, kdy byly tyto clustery vytvořeny. Clustery Azure v bronzové vrstvě odolnosti ve výchozím nastavení nemají povolenou službu Správce oprav. Pokud je služba již povolena, můžete ji vidět spuštěnou v části Systémové služby v aplikaci Service Fabric Explorer.

##### <a name="the-azure-portal"></a>Azure Portal
Správce oprav můžete povolit z portálu Azure při nastavovat cluster. Při konfiguraci clusteru vyberte v části **Funkce doplňků**možnost Zahrnout **Správce oprav** .

![Obrázek povolení Správce oprav z portálu Azure](media/service-fabric-patch-orchestration-application/EnableRepairManager.png)

##### <a name="the-azure-resource-manager-deployment-model"></a>Model nasazení Azure Resource Manageru
Případně můžete použít [model nasazení Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) u povolit službu Repair Manager na nových a stávajících clusterech Service Fabric. Získejte šablonu pro cluster, který chcete nasadit. Můžete použít ukázkové šablony nebo vytvořit vlastní šablonu modelu nasazení Azure Resource Manager. 

Chcete-li povolit službu Správce oprav pomocí [šablony modelu nasazení Nástroje pro nasazení Správce prostředků Azure](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm), postupujte takto:

1. Zkontrolujte, `apiVersion` zda je nastavena na *2017-07-01-preview* pro *microsoft.servicefabric/clusters* prostředku. Pokud se to liší, musíte `apiVersion` aktualizovat na *2017-07-01-preview* nebo novější:

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

1. Povolte službu Správce oprav `addonFeatures` přidáním `fabricSettings` následující části za oddíl:

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

3. Po aktualizaci šablony clusteru s těmito změnami je použijte a nechte aktualizaci dokončit. Nyní můžete zobrazit službu Správce oprav spuštěnou v clusteru. Nazývá se *fabric:/System/RepairManagerService* v části systémové služby v aplikaci Service Fabric Explorer. 

### <a name="standalone-on-premises-clusters"></a>Samostatné místní clustery

Chcete-li povolit službu Správce oprav v novém nebo existujícím clusteru Service Fabric, můžete použít [nastavení konfigurace samostatného clusteru Windows](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest).

Povolení služby Správce oprav:

1. Zkontrolujte, `apiVersion` zda je v [obecné konfiguraci clusteru](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest#general-cluster-configurations) nastavena na *04-2017* nebo novější, jak je znázorněno zde:

    ```json
    {
        "name": "SampleCluster",
        "clusterConfigurationVersion": "1.0.0",
        "apiVersion": "04-2017",
        ...
    }
    ```

1. Povolte službu Správce oprav `addonFeatures` přidáním `fabricSettings` následující části za oddíl, jak je znázorněno zde:

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

1. Aktualizujte manifest clusteru těmito změnami pomocí aktualizovaného [manifestu clusteru, vytvořte nový cluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-for-windows-server) nebo [inovujte konfiguraci clusteru](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-upgrade-windows-server). 

   Po spuštění clusteru s aktualizovaným manifestem clusteru se ve vašem clusteru zobrazí služba Správce oprav spuštěná. Nazývá se *fabric:/System/RepairManagerService*a je v části systémové služby v aplikaci Service Fabric Explorer.

### <a name="configure-windows-updates-for-all-nodes"></a>Konfigurace aktualizací systému Windows pro všechny uzly

Automatické aktualizace systému Windows mohou vést ke ztrátě dostupnosti, protože více uzlů clusteru může být restartováno současně. PoA se ve výchozím nastavení pokusí zakázat automatické aktualizace systému Windows v každém uzlu clusteru. Pokud je však nastavení spravováno správcem nebo zásadami skupiny, doporučujeme explicitně nastavit zásady služby Windows Update na Upozornit před stažením.

## <a name="download-the-application-package"></a>Stáhněte si balíček aplikace

Chcete-li stáhnout balíček aplikace, přejděte na [stránku vydání aplikace Orchestrace opravy](https://github.com/microsoft/Service-Fabric-POA/releases/latest/) na GitHubu.

## <a name="configure-poa-behavior"></a>Konfigurace chování POA

Chování POA můžete nakonfigurovat tak, aby vyhovovalo vašim potřebám. Výchozí hodnoty přepište předáním parametru aplikace při vytváření nebo aktualizaci aplikace. Parametry aplikace můžete zadat `ApplicationParameter` zadáním `Start-ServiceFabricApplicationUpgrade` `New-ServiceFabricApplication` rutiny nebo.

| Parametr        | Typ                          | Podrobnosti |
|:-|-|-|
|Maximální hodnota výsledků    |Dlouhé                              | Maximální počet výsledků služby Windows Update, které by měly být uloženy do mezipaměti. <br><br>Výchozí hodnota je 3000, za předpokladu, že: <br> &nbsp;&nbsp;- Počet uzlů je 20. <br> &nbsp;&nbsp;- Počet aktualizací uzlu za měsíc je 5. <br> &nbsp;&nbsp;- Počet výsledků na operaci může být 10. <br> &nbsp;&nbsp;- Výsledky za poslední tři měsíce by měly být uloženy. |
|Zásady schvalování úloh   |Výčet <br> { NodeWise, UpgradeDomainWise }                          |TaskApprovalPolicy označuje zásadu, kterou má služba koordinátora použít k instalaci aktualizací systému Windows v uzlech clusteru Service Fabric.<br><br>Povolené hodnoty jsou následující: <br>*NodeWise*: Aktualizace systému Windows jsou nainstalovány po jednom uzlu. <br> *UpgradeDomainWise*: Aktualizace systému Windows jsou nainstalovány jednu aktualizační doménu najednou. (Nanejvýš všechny uzly, které patří do aktualizační domény, mohou přejít na aktualizaci systému Windows.)<br><br> Informace o tom, které zásady jsou pro váš cluster nejvhodnější, najdete v části [Nejčastější dotazy.](#frequently-asked-questions)
|ProtokolyDiskQuotaInMB   |Dlouhé  <br> (Výchozí: *1024*)               | Maximální velikost protokolů aplikace orchestrace oprav v MB, které mohou být místně trvalé na uzlech.
| WUQuery               | řetězec<br>(Výchozí: *IsInstalled=0*)                | Dotaz na získání aktualizací systému Windows. Další informace naleznete v tématu [WuQuery.](https://msdn.microsoft.com/library/windows/desktop/aa386526(v=vs.85).aspx)
| InstallWindowsOSOnlyUpdates InstallWindowsOSOnlyUpdates InstallWindowsOSOnlyUpdates InstallWindows | *Logická hodnota* <br> (výchozí: false)                 | Tento příznak slouží k řízení, které aktualizace by měly být staženy a nainstalovány. Následující hodnoty jsou povoleny <br>true - Nainstaluje pouze aktualizace operačního systému Windows.<br>false - Nainstaluje všechny dostupné aktualizace do počítače.          |
| WuOperationTimeOutInMinutes | Int <br>(Výchozí: *90*)                   | Určuje časový limit pro všechny operace služby Windows Update (hledání nebo stahování nebo instalace). Pokud operace není dokončena v rámci zadaného časového režimu, je přerušena.       |
| WURescheduleCount     | Int <br> (Výchozí: *5*)                  | Maximální počet přeplánování aktualizace systému Windows, pokud se operace nezdaří trvale, je maximální počet přeplánování aktualizace systému Windows.          |
| WURescheduleTimeInMinutes | Int <br>(Výchozí: *30*) | Interval, ve kterém služba přeplánuje aktualizace systému Windows, pokud potrvá selhání. |
| WUFrekvence           | Řetězec oddělený čárkami (výchozí: *týdně, středa, 7:00:00*)     | Frekvence pro instalaci aktualizací systému Windows. Formát a možné hodnoty jsou: <br>&nbsp;&nbsp;- Měsíčně: DD, HH:MM:SS (například *Měsíčně, 5,12:22:32*)<br>Povolené hodnoty pro pole DD (den) jsou čísla od 1 do 28 a "poslední". <br> &nbsp;&nbsp;- týdně, den, HH: MM: SS (například *týdně, úterý, 12:22:32*)  <br> &nbsp;&nbsp;- Denně, HH: MM: SS (například *denně, 12:22:32*)  <br> &nbsp;&nbsp;-  *Žádný z nich* nenaznačuje, že aktualizace systému Windows by neměly být prováděny.  <br><br> Časy jsou v UTC.|
| Přijmout WindowsUpdateEula | Logická hodnota <br>(Výchozí: *true*) | Nastavením tohoto příznaku aplikace přijme licenční smlouvu s koncovým uživatelem pro službu Windows Update jménem vlastníka počítače.              |

> [!TIP]
> Pokud chcete, aby aktualizace systému Windows proběhnout okamžitě, nastavte `WUFrequency` vzhledem k době nasazení aplikace. Předpokládejme například, že máte testovací cluster s pěti uzly a plánujete nasazení aplikace kolem 5:00 PM UTC. Pokud předpokládáte, že upgrade nebo nasazení aplikace trvá maximálně 30 minut, nastavte wufrekvence jako *denně, 17:30:00*.

## <a name="deploy-poa"></a>Nasazení POA

1. Dokončete všechny nezbytné kroky pro přípravu clusteru.
1. Nasaďte POA jako všechny ostatní aplikace Service Fabric. Pokud chcete nasadit pomocí PowerShellu, přečtěte si informace [o nasazení a odebrání aplikací pomocí PowerShellu](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications).
1. Chcete-li nakonfigurovat aplikaci v `ApplicationParameter` době `New-ServiceFabricApplication` nasazení, předajte rutinu. Pro vaše pohodlí jsme poskytli skript Deploy.ps1 spolu s aplikací. Použití skriptu:

    - Připojte se ke clusteru `Connect-ServiceFabricCluster`Service Fabric pomocí .
    - Spusťte skript Prostředí PowerShell Deploy.ps1 s příslušnou `ApplicationParameter` hodnotou.

> [!NOTE]
> Ponechte skript a složku aplikace *PatchOrchestrationApplication* ve stejném adresáři.

## <a name="upgrade-poa"></a>Upgrade POA

Chcete-li upgradovat verzi POA pomocí prostředí PowerShell, postupujte podle pokynů v [inovaci aplikací Service Fabric pomocí prostředí PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade-tutorial-powershell).

## <a name="remove-poa"></a>Odebrat POA

Chcete-li aplikaci odebrat, postupujte podle pokynů v [části Nasazení a odebrání aplikací pomocí prostředí PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications).

Pro vaše pohodlí jsme poskytli skript Undeploy.ps1 spolu s aplikací. Použití skriptu:

  - Připojte se ke clusteru ```Connect-ServiceFabricCluster```Service Fabric pomocí .
  - Spusťte skript prostředí PowerShell Undeploy.ps1.

> [!NOTE]
> Ponechte skript a složku aplikace *PatchOrchestrationApplication* ve stejném adresáři.

## <a name="view-the-windows-update-results"></a>Zobrazení výsledků služby Windows Update

POA zpřístupňuje REST API pro zobrazení historických výsledků uživatelům. Zde je příklad výsledku JSON:

```json
[
  {
    "NodeName": "_stg1vm_1",
    "WindowsUpdateOperationResults": [
      {
        "OperationResult": 0,
        "NodeName": "_stg1vm_1",
        "OperationTime": "2019-05-13T08:44:56.4836889Z",
        "OperationStartTime": "2019-05-13T08:44:33.5285601Z",
        "UpdateDetails": [
          {
            "UpdateId": "7392acaf-6a85-427c-8a8d-058c25beb0d6",
            "Title": "Cumulative Security Update for Internet Explorer 11 for Windows Server 2012 R2 (KB3185319)",
            "Description": "A security issue has been identified in a Microsoft software product that could affect your system. You can help protect your system by installing this update from Microsoft. For a complete listing of the issues that are included in this update, see the associated Microsoft Knowledge Base article. After you install this update, you may have to restart your system.",
            "ResultCode": 0,
            "HResult": 0
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

Pole JSON jsou popsána v následující tabulce:

Pole | Hodnoty | Podrobnosti
-- | -- | --
Výsledek operace | 0 - Úspěšné<br> 1 - Úspěšné s chybami<br> 2 - Nezdařilo se<br> 3 - Přerušeno<br> 4 - Přerušeno s časovým časem | Označuje výsledek celkové operace, která obvykle zahrnuje instalaci jedné nebo více aktualizací.
Výsledekkód | Stejné jako Výsledek operace | Toto pole označuje výsledek instalace pro jednotlivé aktualizace.
Operationtype | 1 - Instalace<br> 0 - Vyhledávání a stahování| Ve výchozím nastavení je instalace jediným typem operace, který je zobrazen ve výsledcích.
Program WindowsUpdateQuery | Výchozí hodnota je "IsInstalled=0" | Dotaz služby Windows Update, který byl použit k vyhledání aktualizací. Další informace naleznete v tématu [WuQuery](https://msdn.microsoft.com/library/windows/desktop/aa386526(v=vs.85).aspx).
Restartování je vyžadováno | true - bylo nutné restartovat<br> false - restartování nebylo nutné | Označuje, zda bylo k dokončení instalace aktualizací nutné restartovat počítač.
OperaceStartTime | DateTime | Označuje čas spuštění operace (stahování/instalace).
Doba provozu | DateTime | Označuje čas, kdy byla operace (stahování/instalace) dokončena.
Hresult | 0 - Úspěšné<br> other - selhání| Označuje důvod selhání aktualizace systému Windows s updateID "7392acaf-6a85-427c-8a8d-058c25beb0d6".

Pokud ještě není naplánována žádná aktualizace, výsledek JSON je prázdný.

Přihlaste se ke clusteru a dotazte se na výsledky služby Windows Update. Zjistěte adresu IP repliky pro primární adresu služby koordinátora a otevřete z&gt;&lt;prohlížeče následující adresu URL: http://&lt;REPLICA-IP : ApplicationPort&gt;/PatchOrchestrationApplication/v1/GetWindowsUpdateResults.

Koncový bod REST pro službu koordinátora má dynamický port. Chcete-li zkontrolovat přesnou adresu URL, naleznete v aplikaci Service Fabric Explorer. Například výsledky jsou k *http://10.0.0.7:20000/PatchOrchestrationApplication/v1/GetWindowsUpdateResults*dispozici na adrese .

![Obrázek koncového bodu REST](media/service-fabric-patch-orchestration-application/Rest_Endpoint.png)

Pokud je v clusteru povolen reverzní proxy server, můžete k adrese URL přistupovat také mimo cluster.

Koncový bod, který je třeba shodnout, je *http://&lt;&gt;SERVERURL :&lt;REVERSEPROXYPORT&gt;/PatchOrchestrationApplication/CoordinatorService/v1/GetWindowsUpdateResults*.

Chcete-li povolit reverzní proxy server v clusteru, postupujte podle pokynů v [reverzní proxy v Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy). 

> 
> [!WARNING]
> Po konfiguraci reverzní proxy server, všechny mikroslužby v clusteru, které zveřejňují koncový bod HTTP jsou adresovatelné z mimo clusteru.

## <a name="diagnostics-and-health-events"></a>Diagnostika a zdravotní události

Tato část popisuje, jak ladit nebo diagnostikovat problémy s aktualizacemi oprav prostřednictvím poa na clusterech Service Fabric.

> [!NOTE]
> Chcete-li získat mnoho z následujících vyzývacích, samodiagnostických vylepšení, měli byste mít nainstalovanou verzi POA verze 1.4.0 nebo novější.

Agent uzlu NTService vytvoří [úlohy opravy](https://docs.microsoft.com/dotnet/api/system.fabric.repair.repairtask?view=azure-dotnet) pro instalaci aktualizací na uzly. Každý úkol je pak připraven službou koordinátora podle zásad schválení úkolu. Nakonec připravené úkoly jsou schváleny Správcem oprav, který neschvaluje žádný úkol, pokud je cluster v nestavu. 

Abychom vám pomohli pochopit, jak aktualizace na uzlu postupují, pojďme krok za krokem:

1. NodeAgentNTService, spuštěné na každém uzlu, vyhledá dostupné aktualizace systému Windows v naplánovaném čase. Pokud jsou k dispozici aktualizace, stáhne je do uzlu.

1. Po stažení aktualizací vytvoří agent uzlu NTService odpovídající úlohu opravy uzlu s názvem *POS___\<unique_id>*. Tyto úlohy opravy můžete zobrazit pomocí rutiny [Get-ServiceFabricRepairTask](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricrepairtask?view=azureservicefabricps) nebo pomocí sfx v části podrobnosti uzlu. Po vytvoření úkolu opravy se rychle přesune do stavu [ *Nárokovaný* ](https://docs.microsoft.com/dotnet/api/system.fabric.repair.repairtaskstate?view=azure-dotnet).

1. Služba koordinátora pravidelně vyhledává úkoly opravy ve stavu *Nárokovaný* a poté je aktualizuje na *stav Příprava* na základě zásad schvalování úloh. Pokud taskapprovalPolicy je nakonfigurován jako NodeWise, úkol opravy, který odpovídá uzlu je připraven pouze v případě, že žádný jiný úkol opravy je aktuálně v *Příprava*, *Schváleno*, *Provádění*nebo *Obnovení* stavu. 

   Podobně v případě UpgradeWise TaskApprovalPolicy existují úkoly v předchozích stavech pouze pro uzly, které patří do stejné domény aktualizace. Po přesunutí úlohy opravy do stavu *Příprava* je odpovídající uzel Service Fabric [zakázán](https://docs.microsoft.com/powershell/module/servicefabric/disable-servicefabricnode?view=azureservicefabricps) s záměrem nastaveným na *restart*.

   POA verze 1.4.0 a novější zaúčtovat události s ClusterPatchingStatus vlastnost na CoordinatorService zobrazit uzly, které jsou opravovány. Aktualizace jsou nainstalovány na _poanode_0, jak je znázorněno na následujícím obrázku:

    [![Obrázek stavu oprav clusteru](media/service-fabric-patch-orchestration-application/clusterpatchingstatus.png)](media/service-fabric-patch-orchestration-application/clusterpatchingstatus.png#lightbox)

1. Po zakázání uzlu je úloha opravy přesunuta do *stavu Provádění.* 
   
   > [!NOTE]
   > Uzel, který se zasekl ve stavu *Zakázáno,* může blokovat novou úlohu opravy, která zastaví operaci oprav v clusteru.

1. Pokud je úloha opravy ve *stavu Provádění,* spustí se instalace opravy v tomto uzlu. Po instalaci opravy může nebo nemusí být uzel restartován v závislosti na opravě. Dále je úloha opravy přesunuta do stavu *Obnovení,* který znovu povolí uzel. Úkol opravy je pak označen jako dokončený.

   Ve verzi POA 1.4.0 a novějších můžete najít stav aktualizace zobrazením událostí stavu ve službě\<NodeAgentService pomocí vlastnosti> WUOperationStatus- NodeName. Zvýrazněné části na následujících obrázcích zobrazují stav aktualizací systému Windows v uzlech *poanode_0* a *poanode_2*:

   [![Obrázek stavu operace služby Windows Update](media/service-fabric-patch-orchestration-application/wuoperationstatusa.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusa.png#lightbox)

   [![Obrázek stavu operace služby Windows Update](media/service-fabric-patch-orchestration-application/wuoperationstatusb.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusb.png#lightbox)

   Podrobnosti můžete také získat pomocí prostředí PowerShell. Chcete-li tak učinit, připojte se ke clusteru a načíst stav úlohy opravy pomocí [Get-ServiceFabricRepairTask](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricrepairtask?view=azureservicefabricps). 
   
   V následujícím příkladu je úloha "POS__poanode_2_125f2969-933c-4774-85d1-ebdf85e79f15" ve stavu *DownloadComplete.* To znamená, že aktualizace byly staženy na *poanode_2* uzlu a instalace se pokusí při přesunu úlohy do *spuštěného* stavu.

   ``` powershell
    D:\service-fabric-poa-bin\service-fabric-poa-bin\Release> $k = Get-ServiceFabricRepairTask -TaskId "POS__poanode_2_125f2969-933c-4774-85d1-ebdf85e79f15"

    D:\service-fabric-poa-bin\service-fabric-poa-bin\Release> $k.ExecutorData
    {"ExecutorSubState":2,"ExecutorTimeoutInMinutes":90,"RestartRequestedTime":"0001-01-01T00:00:00"}
    ```

   Pokud se ještě najdou další problémy, přihlaste se k virtuálnímu počítači (VM) nebo virtuálním počítači a získejte o nich informace pomocí protokolů událostí systému Windows. Výše uvedený úkol opravy může existovat pouze v následujících podsucích vykonavatele:

      ExecutorSubState | Popis
    -- | -- 
      Žádné=1 |  Znamená to, že v uzlu nebyla žádná probíhající operace. Stát může být v přechodu.
      DownloadCompleted=2 | Znamená to, že operace stahování byla dokončena s úspěchem, částečné selhání nebo selhání.
      InstalaceSchválená=3 | Znamená to, že operace stahování byla dokončena dříve a Správce oprav schválil instalaci.
      InstalaceV průběhu=4 | Odpovídá stavu provádění úlohy opravy.
      InstalaceDokončena=5 | Znamená to, že instalace byla dokončena s úspěchem, částečným úspěchem nebo neúspěchem.
      RestartRequested=6 | Znamená to, že instalace opravy byla dokončena a v uzlu probíhá čekající akce restartování.
      RestartNotNeeded=7 |  Znamená to, že restartování nebylo potřeba po dokončení instalace opravy.
      Restartovatdokončeno=8 | Znamená to, že restartování bylo úspěšně dokončeno.
      OperaceDokončeno=9 | Operace služby Windows Update byla úspěšně dokončena.
      Operace přerušena=10 | Znamená to, že operace windows update byla přerušena.

1. Ve verzi POA 1.4.0 a novějších, když je dokončen pokus o aktualizaci uzlu, je událost s vlastností "WUOperationStatus-[NodeName]" zaúčtována na NodeAgentService, aby vás upozornila na další pokus o stažení a instalaci aktualizací systému Windows. Zobrazí se na následujícím obrázku:

     [![Obrázek stavu operace služby Windows Update](media/service-fabric-patch-orchestration-application/wuoperationstatusc.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusc.png#lightbox)

### <a name="diagnostics-logs"></a>Protokoly diagnostiky

Protokoly aplikací orchestrace opravy jsou shromažďovány jako součást protokolů runtime Service Fabric.

Protokoly můžete zachytit pomocí diagnostického nástroje nebo kanálu podle vašeho výběru. POA používá následující Pevná ID zprostředkovatele k protokolování událostí prostřednictvím [zdroje událostí](https://docs.microsoft.com/dotnet/api/system.diagnostics.tracing.eventsource?view=netframework-4.5.1):

- e39b723c-590c-4090-abb0-11e3e6616346
- fc0028ff-bfdc-499f-80dc-ed922c52c5e9 fc028ff-bfdc-499f-80dc-ed922c52c5e9 fc028ff-bfdc-499f-80dc-ed922c52c5e9 fc028
- 24afa313-0d3b-4c7c-b485-1047fd964b60
- 05dc046c-60e9-4ef7-965e-91660adffa68

### <a name="health-reports"></a>Zdravotní zprávy

POA také publikuje sestavy stavu proti službě agentuzlu nebo službě koordinátora v následujících scénářích:

* Agent uzlu NTService je vypnutý.

   Pokud agent uzlu NTService je dole na uzlu, sestava stavu na úrovni upozornění je generována proti službě agenta uzlu.

* Služba Správce oprav není povolena.

   Pokud služba Správce oprav není v clusteru nalezena, je pro službu koordinátora vygenerována sestava stavu na úrovni upozornění.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

**Otázka: Proč se můj cluster zobrazuje v chybovém stavu, když běží POA?**

Odpověď: Během procesu instalace POA zakáže nebo restartuje uzly, což může dočasně způsobit, že se stane cluster v nepořádku.

V závislosti na zásadách aplikace může jeden uzel během operace opravy přejít dolů *nebo* celá aktualizační doména může přejít dolů najednou.

Na konci instalace aktualizací systému Windows jsou uzly znovu povoleny po restartování.

V následujícím příkladu clusteru přešel do chybového stavu dočasně, protože dva uzly byly dole a MaxPercentageUnhealthyNodes zásady byla porušena. Chyba je dočasná, dokud nebude možné zahájit operaci opravy.

![Obrázek clusteru není v pořádku](media/service-fabric-patch-orchestration-application/MaxPercentage_causing_unhealthy_cluster.png)

Pokud problém přetrvává, naleznete v části Řešení potíží.

**Otázka: Co mám dělat, když je POA ve stavu upozornění?**

A: Zkontrolujte, zda sestava stavu zaúčtované proti aplikaci označuje hlavní příčinu. Upozornění obvykle obsahuje podrobnosti o problému. Pokud je problém přechodný, očekává se, že se aplikace automaticky obnoví.

**Otázka: Co mám dělat, když je můj cluster v pořádku a potřebuji provést naléhavou aktualizaci operačního systému?**

A: POA nenainstaluje aktualizace, zatímco cluster není v pořádku. Pokuste se přenést cluster do stavu v pořádku odblokovat pracovní postup POA.

**Otázka: Mám nastavit TaskApprovalPolicy jako "NodeWise" nebo "UpgradeDomainWise" pro můj cluster?**

Odpověď: Nastavení "UpgradeDomainWise" urychluje celkovou opravu clusteru paralelním opravami všech uzlů, které patří do aktualizační domény. Během procesu nejsou k dispozici uzly, které patří do celé domény aktualizace (ve stavu [ *Zakázáno* ](https://docs.microsoft.com/dotnet/api/system.fabric.query.nodestatus?view=azure-dotnet#System_Fabric_Query_NodeStatus_Disabled)).

Naproti tomu nastavení "NodeWise" opravuje pouze jeden uzel najednou, což by znamenalo, že celkové opravy clusteru může trvat déle. Během procesu opravy však nebude k dispozici maximálně jeden uzel (ve stavu *Zakázáno).*

Pokud váš cluster může tolerovat spuštění na N-1 počet aktualizačních domén během cyklu oprav (kde N je celkový počet aktualizačních domén v clusteru), můžete nastavit zásady jako "UpgradeDomainWise." V opačném případě nastavte na "NodeWise.".

**Otázka: Kolik času trvá oprava uzlu?**

A: Oprava uzlu může trvat od minut (například [aktualizace definic programu Windows Defender](https://www.microsoft.com/en-us/wdsi/definitions)) na hodiny (například kumulativní aktualizace [systému Windows](https://www.catalog.update.microsoft.com/Search.aspx?q=windows%20server%20cumulative%20update)). Doba potřebná k opravě uzlu závisí především na: 
 - Velikost aktualizací.
 - Počet aktualizací, které mají být použity v okně opravy.
 - Čas potřebný k instalaci aktualizací, restartování uzlu (v případě potřeby) a dokončení kroků instalace po restartování.
 - Výkon virtuálního počítače nebo počítače a podmínky sítě.

**Otázka: Jak dlouho trvá oprava celého clusteru?**

Odpověď: Čas potřebný k opravě celého clusteru závisí na:

- Čas potřebný k opravě uzlu.

- Politika služby koordinátora. Výchozí zásady "NodeWise", výsledkem je oprava pouze jeden uzel najednou, přístup, který je pomalejší než pomocí "UpgradeDomainWise." 

   Příklad: Pokud oprava uzlu trvá ~1 hodinu, vyžaduje oprava clusteru s 20 uzly (stejný typ uzlů) s 5 aktualizačními doménami, z nichž každý obsahuje 4 uzly, vyžaduje:
    - Pro "NodeWise": ~20 hodin.
    - Pro "UpgradeDomainWise": ~5 hodin.

- Zatížení clusteru. Každá operace oprav vyžaduje přemístění zatížení zákazníka do jiných dostupných uzlů v clusteru. Uzel, který je oprava by být ve stavu [ *zakázání* ](https://docs.microsoft.com/dotnet/api/system.fabric.query.nodestatus?view=azure-dotnet#System_Fabric_Query_NodeStatus_Disabling) během této doby. Pokud cluster běží v blízkosti zatížení ve špičce, proces zakázání bude trvat déle. Proto se může zdát, že celkový proces oprav je v těchto stresových podmínkách pomalý.

- Selhání stavu clusteru během opravy. Jakékoli [zhoršení](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthstate?view=azure-dotnet#System_Fabric_Health_HealthState_Error) [stavu clusteru](https://docs.microsoft.com/azure/service-fabric/service-fabric-health-introduction) by přerušilo proces opravy. Tento problém by přidat k celkové době potřebné k opravě celého clusteru.

**Otázka: Proč se ve výsledcích služby Windows Update zobrazují některé aktualizace, které jsou získány prostřednictvím rozhraní REST API, ale ne v historii služby Windows Update v počítači?**

A: Některé aktualizace produktu se zobrazí pouze v jejich vlastní historii aktualizací nebo oprav. Aktualizace programu Windows Defender se například mohou nebo nemusí zobrazit v historii služby Windows Update v systému Windows Server 2016.

**Otázka: Lze poa použít k opravě dev clusteru (cluster s jedním uzlem)?**

A: Ne, POA nelze použít k opravě clusteru s jedním uzlem. Toto omezení je záměrné, protože [systémové služby Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-technical-overview#system-services) nebo jiné zákaznické aplikace by měly vzniknout prostojů. Proto opravy opravy úlohy by nikdy získat schválen správcem opravy.

**Otázka: Jak opravím uzly clusteru v Linuxu?**

A: Informace o orchestraci aktualizací na Linuxu, najdete v [tématu Škálování virtuálních strojů Azure automatické upgrady bitových bitových obrázků operačního systému](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade).

**Otázka: Proč cyklus aktualizace trvá tak dlouho?**

A: Dotaz na výsledek JSON, zadejte cyklus aktualizace pro všechny uzly a pak se můžete pokusit zjistit čas přijatý instalací aktualizace na každém uzlu pomocí OperationStartTime a OperationTime (OperationCompletionTime). 

Pokud existuje velké časové okno, ve kterém neprobíhá žádná aktualizace, může být cluster v chybovém stavu a v důsledku toho správce oprav nemůže schválit žádné úlohy opravy POA. Pokud instalace aktualizace trvá dlouhou dobu na libovolném uzlu, je možné, že tento uzel nebyl nějakou dobu aktualizován. Mnoho aktualizací může být čekající na instalaci, což může mít za následek zpoždění. 

Je také možné, že je oprava uzlu blokována, protože se zasekla ve stavu *Zakázání.* K tomu obvykle dochází, protože zakázání uzlu může vést k situacím ztráty kvora nebo dat.

**Otázka: Proč musí být uzel zakázán, když ho poa opravuje?**

A: POA zakáže uzel s záměrem *restartování,* který zastaví nebo přerozdělí všechny služby Service Fabric, které jsou spuštěny na uzlu. POA to k zajištění, že aplikace neskončí pomocí kombinace nových a starých knihoven DLL, proto doporučujeme není oprava uzlu bez zakázání.

**Otázka: Jaký je maximální počet uzlů, které lze aktualizovat pomocí POA?**

A: POA používá Service Fabric Repair Manager k vytvoření úlohy opravy pro uzly pro aktualizace. Současně však může být přítomno maximálně 250 oprav. V současné době poa vytvoří úkoly opravy pro každý uzel ve stejnou dobu, takže POA můžete aktualizovat více než 250 uzlů v clusteru. 

## <a name="disclaimers"></a>Právní omezení

- PoA přijímá licenční smlouvu s koncovým uživatelem pro službu Windows Update jménem uživatele. Volitelně lze nastavení vypnout v konfiguraci aplikace.

- POA shromažďuje telemetrii ke sledování využití a výkonu. Telemetrie aplikace následuje po nastavení nastavení telemetrie za běhu service fabric (která je ve výchozím nastavení zapnutá).

## <a name="troubleshooting"></a>Řešení potíží

Tato část obsahuje možná řešení potíží s problémy s uzly oprav.

### <a name="a-node-is-not-coming-back-to-up-state"></a>Uzel se nevrátí do stavu nahoru.

* Uzel může být zablokovaný ve stavu *Zakázání,* protože:

  - Bezpečnostní kontrola čeká na vyřízení. Chcete-li tuto situaci napravit, ujistěte se, že dostatek uzlů jsou k dispozici v zdravém stavu.

* Uzel může být zablokovaný ve stavu *Zakázáno,* protože:

  - Byl zakázán ručně.
  - Byla zakázána z důvodu probíhající úlohy infrastruktury Azure.
  - Byl dočasně zakázán poa opravit uzel.

* Uzel může být zablokovaný ve stavu dolů, protože:

  - Byl umístěn do stavu dolů ručně.
  - Prochází restartováním (které může být spuštěno poa).
  - Má vadný virtuální počítač nebo počítač nebo má problémy s připojením k síti.

### <a name="updates-were-skipped-on-some-nodes"></a>Aktualizace byly přeskočeny na některých uzlech

POA se pokusí nainstalovat aktualizaci systému Windows v souladu se zásadami přeplánování. Služba se pokusí obnovit uzel a přeskočit aktualizaci podle zásad aplikace.

V takovém případě je generována sestava stavu na úrovni upozornění proti službě agenta uzlu. Výsledek služby Windows Update také obsahuje možný důvod selhání.

### <a name="the-health-of-the-cluster-goes-to-error-while-the-update-is-being-installed"></a>Stav clusteru přejde k chybě při instalaci aktualizace

Vadná aktualizace systému Windows může snížit stav aplikace nebo clusteru v konkrétní doméně uzlu nebo aktualizace. POA ukončí všechny následné operace windows update, dokud cluster je v pořádku znovu.

Správce musí zasáhnout a zjistit, proč se aplikace nebo cluster stal nefunkční z důvodu služby Windows Update.

## <a name="poa-release-notes"></a>Poznámky k verzi POA

>[!NOTE]
> Pro POA verze 1.4.0 a novější, můžete najít poznámky k verzi a verze na [stránce vydání aplikace Záorchestration aplikace](https://github.com/microsoft/Service-Fabric-POA/releases/) na GitHubu.

### <a name="version-110"></a>Verze 1.1.0
- Veřejné vydání

### <a name="version-111"></a>Verze 1.1.1
- Opravena chyba v SetupEntryPoint of NodeAgentService, která bránila instalaci služby NodeAgentNTService.

### <a name="version-120"></a>Verze 1.2.0

- Opravy chyb v okolí pracovního postupu restartování systému.
- Oprava chyb při vytváření úloh RM, kvůli kterým se kontrola stavu během přípravy oprav nedělo podle očekávání.
- Byl změněn režim spuštění služby Windows POANodeSvc z automatického na zpožděné auto.

### <a name="version-121"></a>Verze 1.2.1

- Oprava chyb v pracovním postupu škálování clusteru. Zavedena logika uvolňování paměti pro úlohy opravy POA patřící do neexistujících uzlů.

### <a name="version-122"></a>Verze 1.2.2

- Různé opravy chyb.
- Binární soubory jsou nyní podepsány.
- Přidán odkaz sfpkg pro aplikaci.

### <a name="version-130"></a>Verze 1.3.0

- Nastavení InstallWindowsOSOnlyAktualizace false nyní nainstaluje všechny dostupné aktualizace.
- Změnila se logika zakázání automatických aktualizací. To opravuje chybu, kdy automatické aktualizace nebyly stále zakázány na serveru 2016 a vyšší.
- Parametrizované omezení umístění pro obě mikroslužeb POA pro pokročilé případy použití.

### <a name="version-131"></a>Verze 1.3.1
- Oprava regrese, kde POA 1.3.0 nebude fungovat v systému Windows Server 2012 R2 nebo dříve z důvodu selhání zakázání automatických aktualizací. 
- Oprava chyby, kde InstallWindowsOSOnlyUpdates konfigurace je vždy vybral jako True.
- Změna výchozí hodnoty InstallWindowsOSOnlyUpdates to False.

### <a name="version-132"></a>Verze 1.3.2
- Oprava problému, který ovlivňuje životní cyklus oprav na uzlu, pokud existují uzly s názvem, který je podmnožinou aktuálního názvu uzlu. Pro tyto uzly je možné, že oprava byla vynechána nebo čeká na restartování.
