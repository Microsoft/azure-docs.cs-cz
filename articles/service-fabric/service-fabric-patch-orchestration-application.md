---
title: Aplikace orchestrace oprav pro Azure Service Fabric | Microsoft Docs
description: Aplikace pro automatizaci oprav operačního systému v clusteru Service Fabric.
services: service-fabric
documentationcenter: .net
author: khandelwalbrijeshiitr
manager: chackdan
editor: ''
ms.assetid: de7dacf5-4038-434a-a265-5d0de80a9b1d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/01/2019
ms.author: brkhande
ms.openlocfilehash: 2aa2dd8373a9568478a02691ca5e6a43e80cd408
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2019
ms.locfileid: "71289414"
---
# <a name="patch-the-windows-operating-system-in-your-service-fabric-cluster"></a>Oprava operačního systému Windows v clusteru Service Fabric

> 
> [!IMPORTANT]
> Verze aplikace 1,2. * vychází z podpory 30. dubna 2019. Upgradujte prosím na nejnovější verzi.


[Automatické upgrady bitových kopií operačního systému Azure pro škálování virtuálního počítače](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade) jsou osvědčenými postupy pro udržování oprav operačního systému v Azure a aplikace orchestrace oprav (Poa) je obálkou týkající se služby Service Fabric RepairManager Systems. povoluje plánování oprav operačního systému na základě konfigurace pro clustery hostované mimo Azure. Pro clustery, které nejsou hostované na Azure, se nepožaduje POA, ale k opravě Service Fabricch clusterů bez výpadků se vyžaduje naplánování instalace opravy pomocí domén upgradu.

POA je aplikace Service Fabric v Azure, která automatizuje opravy operačního systému v clusteru Service Fabric bez výpadků.

Aplikace orchestrace oprav nabízí tyto funkce:

- **Instalace automatické aktualizace operačního systému**. Aktualizace operačního systému se automaticky stáhnou a nainstalují. Clusterové uzly se restartují podle potřeby bez výpadku clusteru.

- **Opravy podporující clustery a integrace stavu**. Aplikace orchestrace oprav při použití aktualizací monitoruje stav uzlů clusteru. Uzly clusteru jsou upgradovány vždy po jednom uzlu nebo v jedné upgradovací doméně. Pokud dojde k výpadku stavu clusteru z důvodu procesu opravy, zastaví se oprava, aby se zabránilo zhoršení problému.

## <a name="internal-details-of-the-app"></a>Interní podrobnosti aplikace

Aplikace orchestrace oprav se skládá z následujících dílčích součástí:

- **Služba koordinátora**: Tato stavová služba zodpovídá za:
    - Koordinace úlohy web Windows Update v celém clusteru.
    - Ukládání výsledků dokončených operací web Windows Update.
- **Služba agenta uzlu**: Tato Bezstavová služba běží na všech uzlech clusteru Service Fabric. Služba zodpovídá za:
    - Probíhá zavádění agenta uzlu NTService.
    - Monitorování agenta uzlu NTService.
- **NTService agenta uzlu**: Tato služba systému Windows NT běží na úrovni oprávnění (systém) vyšší úrovně. Naproti tomu se služba agenta pro uzly a služba koordinátora spouští s oprávněními nižší úrovně (síťová služba). Služba zodpovídá za provádění následujících úloh web Windows Update na všech uzlech clusteru:
    - Zákaz automatických web Windows Update v uzlu.
    - Stažení a instalace web Windows Update v závislosti na zásadách, které uživatel zadal.
    - Restartování počítače po web Windows Update instalaci.
    - Nahrávají se výsledky aktualizací Windows do služby koordinátora.
    - Vytváření sestav o stavu v případě neúspěchu operace po vyčerpání všech opakovaných pokusů.

> [!NOTE]
> Aplikace orchestrace oprav používá systémovou službu Service Fabric Repair Manager System k zakázání nebo povolení uzlu a provádění kontrol stavu. Úkol opravy vytvořený aplikací orchestrace opravy sleduje web Windows Update průběh každého uzlu.

## <a name="prerequisites"></a>Požadavky

> [!NOTE]
> Minimální požadovaná verze rozhraní .NET Framework je 4,6.

### <a name="enable-the-repair-manager-service-if-its-not-running-already"></a>Povolit službu správce oprav (Pokud ještě není spuštěná)

Aplikace orchestrace oprav vyžaduje, aby v clusteru byla povolená systémová služba nástroje Oprava.

#### <a name="azure-clusters"></a>Clustery Azure

Clustery Azure v úrovni odolnosti stříbra mají ve výchozím nastavení povolenou službu správce oprav. Clustery Azure v zlatý úrovni odolnosti mohou nebo nemusí mít povolenou službu správce oprav v závislosti na tom, kdy byly tyto clustery vytvořeny. Clustery Azure v úrovni bronzové odolnosti ve výchozím nastavení nemají povolenou službu správce oprav. Pokud je tato služba již povolená, můžete ji zobrazit v části systémové služby v Service Fabric Explorer.

##### <a name="azure-portal"></a>portál Azure
Správce oprav můžete povolit z Azure Portal v době nastavení clusteru. V době konfigurace clusteru vyberte možnost **zahrnout Repair Manager** v části **Doplňky – funkce** .
![Obrázek povolení Repair Manager z Azure Portal](media/service-fabric-patch-orchestration-application/EnableRepairManager.png)

##### <a name="azure-resource-manager-deployment-model"></a>Model nasazení Azure Resource Manager
Alternativně můžete použít [model nasazení Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) pro povolení služby Repair Manager v nových a existujících clusterech Service Fabric. Získejte šablonu pro cluster, který chcete nasadit. Můžete buď použít ukázkové šablony, nebo vytvořit vlastní šablonu modelu nasazení Azure Resource Manager. 

Povolení služby Repair Manager pomocí [šablony modelu nasazení Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm):

1. Nejdřív ověřte, že `apiversion` je u `Microsoft.ServiceFabric/clusters` prostředku `2017-07-01-preview` nastavené na hodnotu. Pokud se liší, je nutné aktualizovat `apiVersion` na hodnotu `2017-07-01-preview` nebo vyšší:

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. Nyní povolte službu oprava správce přidáním následující `addonFeatures` části `fabricSettings` za oddíl:

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

3. Po aktualizaci šablony clusteru pomocí těchto změn ji použijte a nechte dokončení upgradu. Teď si můžete prohlédnout systémovou službu oprav správce spuštěnou ve vašem clusteru. Je volána `fabric:/System/RepairManagerService` v části systémové služby v Service Fabric Explorer. 

### <a name="standalone-on-premises-clusters"></a>Samostatné místní clustery

Pomocí [nastavení konfigurace pro samostatný cluster systému Windows](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest) můžete povolit službu Repair Manager Service na novém a existujícím clusteru Service Fabric.

Postup povolení služby správce oprav:

1. Nejprve ověřte, zda `apiversion` je v části [obecné konfigurace clusteru](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest#general-cluster-configurations) nastavena `04-2017` nebo vyšší:

    ```json
    {
        "name": "SampleCluster",
        "clusterConfigurationVersion": "1.0.0",
        "apiVersion": "04-2017",
        ...
    }
    ```

2. Nyní povolte službu oprav správce přidáním následujícího `addonFeatures` oddílu `fabricSettings` za oddíl, jak je uvedeno níže:

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

3. Aktualizujte svůj manifest clusteru pomocí těchto změn pomocí aktualizovaného manifestu clusteru [vytvořte nový cluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-for-windows-server) nebo [upgradujte konfiguraci clusteru](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-upgrade-windows-server). Po spuštění clusteru s aktualizovaným manifestem clusteru teď můžete zobrazit systémovou službu oprav správce spuštěnou ve vašem clusteru, která je zavolána `fabric:/System/RepairManagerService`v části systémové služby v Průzkumníkovi Service Fabric.

### <a name="configure-windows-updates-for-all-nodes"></a>Konfigurovat aktualizace Windows pro všechny uzly

Automatické aktualizace Windows můžou způsobit ztrátu dostupnosti, protože víc uzlů clusteru se může restartovat současně. Aplikace Orchestration ve výchozím nastavení se pokusí zakázat automatickou web Windows Update na všech uzlech clusteru. Pokud však nastavení spravuje správce nebo Zásady skupiny, doporučujeme nastavit zásadu web Windows Update na hodnotu "upozorňovat před explicitně stahovat".

## <a name="download-the-app-package"></a>Stažení balíčku aplikace

Pokud chcete stáhnout balíček aplikace, navštivte [stránku](https://github.com/microsoft/Service-Fabric-POA/releases/latest/) verze aplikace Orchestration na GitHubu.

## <a name="configure-the-app"></a>Konfigurace aplikace

Chování aplikace orchestrace oprav se dá nakonfigurovat tak, aby vyhovovala vašim potřebám. Přepište výchozí hodnoty předáním parametru aplikace při vytváření nebo aktualizaci aplikace. Parametry aplikace lze poskytnout zadáním `ApplicationParameter` `Start-ServiceFabricApplicationUpgrade` rutin nebo `New-ServiceFabricApplication` .

|**Parametr**        |**Typ**                          | **Podrobnosti**|
|:-|-|-|
|MaxResultsToCache    |Dlouhé                              | Maximální počet výsledků web Windows Update, které by měly být uloženy v mezipaměti. <br>Výchozí hodnota je 3000 za předpokladu: <br> -Počet uzlů je 20. <br> -Počet aktualizací, které se odehrává na uzlu za měsíc, je pět. <br> -Počet výsledků na operaci může být 10. <br> -Výsledky za poslední tři měsíce by měly být uloženy. |
|TaskApprovalPolicy   |Enum <br> { NodeWise, UpgradeDomainWise }                          |TaskApprovalPolicy určuje zásadu, kterou má služba koordinátora použít k instalaci aktualizací Windows v uzlech clusteru Service Fabric.<br>                         Povolené hodnoty jsou: <br>                                                           <b>NodeWise</b>. Web Windows Update je nainstalován vždy po jednom uzlu. <br>                                                           <b>UpgradeDomainWise</b>. Web Windows Update je v jednu chvíli nainstalovaná jedna upgradovací doména. (Na maximum můžou všechny uzly patřící k doméně upgradu přejít pro web Windows Update.)<br> V části [Nejčastější dotazy](#frequently-asked-questions) najdete informace o tom, jak se rozhodnout, které z nich nejlépe vyhovuje zásadám pro váš cluster.
|LogsDiskQuotaInMB   |Dlouhé  <br> Výchozí 1024)               |Maximální velikost protokolů aplikace orchestrace oprav v MB, které je možné trvale uchovat místně na uzlech.
| WUQuery               | řetězec<br>Výchozí "IsInstalled = 0")                | Dotaz pro získání aktualizací Windows Další informace najdete v tématu [WuQuery.](https://msdn.microsoft.com/library/windows/desktop/aa386526(v=vs.85).aspx)
| InstallWindowsOSOnlyUpdates | Logická hodnota <br> (výchozí: NEPRAVDA)                 | Pomocí tohoto příznaku můžete řídit, které aktualizace se mají stáhnout a nainstalovat. Jsou povoleny následující hodnoty. <br>true – nainstaluje jenom aktualizace operačního systému Windows.<br>false – nainstaluje všechny dostupné aktualizace na počítač.          |
| WUOperationTimeOutInMinutes | Int <br>Výchozí 90)                   | Určuje časový limit pro jakoukoliv operaci web Windows Update (hledání nebo stažení nebo instalace). Pokud operace není dokončena v rámci zadaného časového limitu, je přerušena.       |
| WURescheduleCount     | Int <br> Výchozí 5)                  | Maximální počet pokusů, kolikrát služba Windows Update v případě, že se operace nezdařila, bude provedena trvale.          |
| WURescheduleTimeInMinutes | Int <br>Výchozí 30) | Interval, ve kterém služba znovu plánuje aktualizaci Windows v případě selhání. |
| WUFrequency           | Řetězec oddělený čárkami (výchozí: "Týdně, středa, 7:00:00")     | Frekvence instalace web Windows Update. Formáty a možné hodnoty jsou následující: <br>-Měsíčně, DD, HH: MM: SS, například měsíčně, 5, 12:22:32.<br>Povolené hodnoty pro pole DD (den) jsou čísla mezi rozsahem 1-28 a "Last". <br> -Týdně, DAY, HH: MM: SS, například týdně, úterý, 12:22:32.  <br> -Denně, HH: MM: SS, například denně, 12:22:32.  <br> -None znamená, že by web Windows Update neměl být proveden.  <br><br> Všimněte si, že časy jsou v UTC.|
| AcceptWindowsUpdateEula | Logická hodnota <br>(Výchozí: pravda) | Nastavením tohoto příznaku aplikace přijme licenční smlouvu s koncovým uživatelem pro web Windows Update jménem vlastníka počítače.              |

> [!TIP]
> Pokud chcete, aby web Windows Update probíhala hned, `WUFrequency` nastavte relativní čas nasazení aplikace. Předpokládejme například, že máte testovací cluster s pěti uzly a naplánujete nasazení aplikace v přibližně 5:00./odp. UTC. Pokud předpokládáte, že upgrade nebo nasazení aplikace trvá 30 minut v maximálním limitu, nastavte WUFrequency jako "denně, 17:30:00".

## <a name="deploy-the-app"></a>Nasazení aplikace

1. Dokončete všechny požadované kroky a připravte cluster.
2. Nasaďte aplikaci orchestrace oprav stejně jako jakoukoli jinou aplikaci Service Fabric. Aplikaci můžete nasadit pomocí prostředí PowerShell. Postupujte podle kroků v části [nasazení a odebrání aplikací pomocí prostředí PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications).
3. Chcete-li nakonfigurovat aplikaci v době nasazení, předejte `ApplicationParameter` `New-ServiceFabricApplication` rutinu do rutiny. Pro usnadnění práce poskytujeme skript Deploy. ps1 spolu s aplikací. Postup použití skriptu:

    - Připojte se ke clusteru Service Fabric pomocí `Connect-ServiceFabricCluster`.
    - Spusťte skript prostředí PowerShell Deploy. ps1 s odpovídající `ApplicationParameter` hodnotou.

> [!NOTE]
> Nechejte skript a složku aplikace PatchOrchestrationApplication ve stejném adresáři.

## <a name="upgrade-the-app"></a>Upgrade aplikace

Pokud chcete upgradovat existující aplikaci orchestrace oprav pomocí prostředí PowerShell, postupujte podle kroků v části [Service Fabric upgrade aplikace pomocí PowerShellu](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade-tutorial-powershell).

## <a name="remove-the-app"></a>Odebrat aplikaci

Pokud chcete aplikaci odebrat, postupujte podle kroků v části [nasazení a odebrání aplikací pomocí PowerShellu](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications).

Pro usnadnění práce jsme zadali skript pro odinstalaci. ps1 spolu s aplikací. Postup použití skriptu:

  - Připojte se ke clusteru Service Fabric pomocí ```Connect-ServiceFabricCluster```.

  - Spusťte skript PowerShellu undeploy. ps1.

> [!NOTE]
> Nechejte skript a složku aplikace PatchOrchestrationApplication ve stejném adresáři.

## <a name="view-the-windows-update-results"></a>Zobrazit výsledky web Windows Update

Aplikace orchestrace oprav zpřístupňuje rozhraní REST API pro zobrazení historických výsledků uživateli. Příklad formátu JSON výsledku:
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

Pole JSON jsou popsána níže.

Pole | Hodnoty | Podrobnosti
-- | -- | --
OperationResult | 0 – úspěšné<br> 1 – úspěšné s chybami<br> 2 – chyba<br> 3 – přerušeno<br> 4 – přerušeno s časovým limitem | Označuje výsledek celkové operace (obvykle zahrnující instalaci jedné nebo více aktualizací).
resultCode | Stejné jako výsledek operace uvnitř | Toto pole indikuje výsledek operace instalace pro jednotlivou aktualizaci.
OperationType | 1\. instalace<br> 0 – vyhledat a stáhnout.| Instalace je jediná typem operace OperationType, která se ve výchozím nastavení zobrazí ve výsledcích.
WindowsUpdateQuery | Výchozí hodnota je "IsInstalled = 0" |Dotaz služby Windows Update, který byl použit k hledání aktualizací. Další informace najdete v tématu [WuQuery.](https://msdn.microsoft.com/library/windows/desktop/aa386526(v=vs.85).aspx)
RebootRequired | true – byl vyžadován restart.<br> false – restart nebyl požadován. | Určuje, zda byl restart nutný k dokončení instalace aktualizací.
OperationStartTime | DateTime | Určuje čas, kdy byla spuštěna operace (stažení a instalace).
OperationTime | DateTime | Určuje čas, kdy byla dokončena operace (stažení a instalace).
HResult | 0 – úspěšné<br> jiné – selhání| Určuje důvod selhání Windows Update s UpdateID naformátovat "7392acaf-6a85-427c-8a8d-058c25beb0d6".

Pokud ještě není naplánována žádná aktualizace, je výsledek JSON prázdný.

Přihlaste se ke clusteru a Dotazujte web Windows Update výsledků. Pak vyhledejte adresu repliky pro primární službu koordinátora a stiskněte&lt;adresu URL z prohlížeče: http://Replica-IP-IP&gt;:&lt;ApplicationPort&gt;/PatchOrchestrationApplication/v1/ GetWindowsUpdateResults.

Koncový bod REST pro službu koordinátora má dynamický port. Chcete-li zjistit přesnou adresu URL, přečtěte si Service Fabric Explorer. Například výsledky jsou k dispozici na adrese `http://10.0.0.7:20000/PatchOrchestrationApplication/v1/GetWindowsUpdateResults`.

![Obrázek koncového bodu REST](media/service-fabric-patch-orchestration-application/Rest_Endpoint.png)


Pokud je reverzní proxy server povolený v clusteru, máte přístup i k adrese URL mimo cluster.
Koncový bod, který musí být dosaženo, je&lt;http://&gt;SERVERURL&lt;:&gt;REVERSEPROXYPORT/PatchOrchestrationApplication/CoordinatorService/v1/GetWindowsUpdateResults.

Pokud chcete povolit reverzní proxy server v clusteru, postupujte podle kroků v části [reverzní proxy v Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy). 

> 
> [!WARNING]
> Po nakonfigurování reverzního proxy serveru budou všechny mikroslužby v clusteru, které zveřejňují koncový bod HTTP, adresovatelné z vnějšího clusteru.

## <a name="diagnosticshealth-events"></a>Diagnostika/události stavu

V následující části se dozvíte, jak ladit a diagnostikovat problémy s aktualizacemi patch prostřednictvím aplikace orchestrace oprav v Service Fabric clusterech.

> [!NOTE]
> Měli byste mít nainstalovaný 1.4.0 verze POA, abyste získali spoustu z následujících možností diagnostiky s sebou.

NodeAgentNTService vytvoří [úlohy opravy](https://docs.microsoft.com/dotnet/api/system.fabric.repair.repairtask?view=azure-dotnet) pro instalaci aktualizací na uzlech. Každý úkol pak připraví CoordinatorService podle zásad schvalování úloh. Připravené úkoly jsou nakonec schváleny Repair Manager, které neschvalují žádné úlohy, pokud je cluster ve stavu není v pořádku. Umožňuje postupovat krok za krokem k pochopení, jak se aktualizace na uzlu dostanou.

1. NodeAgentNTService běžící na každém uzlu vyhledá dostupné web Windows Update v naplánovaném čase. Pokud jsou aktualizace k dispozici, bude pokračovat a stáhne je na uzel.
2. Po stažení aktualizací NodeAgentNTService vytvoří odpovídající úlohu opravy pro uzel s názvem POS___ < unique_id >. Jeden z nich může zobrazit tyto úlohy opravy pomocí rutiny [Get-ServiceFabricRepairTask](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricrepairtask?view=azureservicefabricps) nebo v SFX v části Podrobnosti uzlu. Po vytvoření úlohy opravy se rychle přesune do [přihlášeného stavu](https://docs.microsoft.com/dotnet/api/system.fabric.repair.repairtaskstate?view=azure-dotnet).
3. Služba koordinátora pravidelně vyhledává úlohy opravy v deklarovaném stavu a předá je a aktualizuje na přípravu stavu na základě TaskApprovalPolicy. Pokud je TaskApprovalPolicy nakonfigurován tak, aby NodeWise, je úloha opravy odpovídající uzlu připravena pouze v případě, že ve stavu Příprava/schválení/spuštění/obnovení není aktuálně žádná jiná úloha opravy. Podobně v případě UpgradeWise TaskApprovalPolicy je zajištěno, že v jakémkoli okamžiku jsou úlohy ve výše uvedených stavech pouze pro uzly, které patří do stejné upgradovací domény. Po přesunu úlohy opravy do stavu Příprava se odpovídající uzel Service Fabric [zakáže](https://docs.microsoft.com/powershell/module/servicefabric/disable-servicefabricnode?view=azureservicefabricps) s záměrem "restartovat".

   POA (v 1.4.0 a vyšší) odešle události s vlastností "ClusterPatchingStatus" na CoordinaterService pro zobrazení uzlů, které se opravují. Pod imagí vidíte, že aktualizace se instalují na _poanode_0:

    [![Obrázek stavu opravy clusteru](media/service-fabric-patch-orchestration-application/clusterpatchingstatus.png)](media/service-fabric-patch-orchestration-application/clusterpatchingstatus.png#lightbox)

4. Po deaktivaci uzlu je úloha opravy přesunuta do stavu spuštěno.
   
   >[!NOTE]
   > Uzel zablokované v zakázaném stavu může blokovat novou úlohu opravy, která zastaví operaci opravy v clusteru.

5. Jakmile je úloha opravy ve spuštěném stavu, zahájí se instalace opravy v tomto uzlu. V případě, že je tato oprava nainstalovaná, uzel se v závislosti na opravě může nebo nemusí restartovat. Vystavte si, že je úloha opravy přesunuta do stavu obnovování, který znovu povolí uzel a pak je označený jako dokončený.

   V 1.4.0 a vyšších verzích aplikace se stav aktualizace dá najít tak, že se podíváte na události stavu v NodeAgentService s vlastností WUOperationStatus-[Název_uzlu]. Zvýrazněné oddíly na obrázcích níže zobrazují stav Windows Update na uzlech "poanode_0" a "poanode_2":

   [![Obrázek stavu operace Windows Update](media/service-fabric-patch-orchestration-application/wuoperationstatusa.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusa.png#lightbox)

   [![Obrázek stavu operace Windows Update](media/service-fabric-patch-orchestration-application/wuoperationstatusb.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusb.png#lightbox)

   K získání podrobností můžete také použít PowerShell tak, že se připojíte ke clusteru a načtete stav úlohy opravy pomocí rutiny [Get-ServiceFabricRepairTask](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricrepairtask?view=azureservicefabricps). Podobně jako v příkladu vidíte, že úloha "POS__poanode_2_125f2969-933c-4774-85d1-ebdf85e79f15" je ve stavu DownloadComplete. To znamená, že aktualizace byly staženy do uzlu "poanode_2" a pokus o instalaci bude proveden, jakmile se úloha přesune do spuštěného stavu.

   ``` powershell
    D:\service-fabric-poa-bin\service-fabric-poa-bin\Release> $k = Get-ServiceFabricRepairTask -TaskId "POS__poanode_2_125f2969-933c-4774-85d1-ebdf85e79f15"

    D:\service-fabric-poa-bin\service-fabric-poa-bin\Release> $k.ExecutorData
    {"ExecutorSubState":2,"ExecutorTimeoutInMinutes":90,"RestartRequestedTime":"0001-01-01T00:00:00"}
    ```

   Pokud je ještě více nalezen, přihlaste se ke konkrétnímu virtuálnímu počítači nebo virtuálnímu počítači a zjistěte další informace o problému pomocí protokolů událostí systému Windows. Výše zmíněná úloha opravy může mít jenom tyto dílčí stavy prováděcích modulů:

      ExecutorSubState | Podrobnosti
    -- | -- 
      Žádné = 1 |  Znamená, že na uzlu neexistují žádné probíhající operace. Možné přechody stavu.
      DownloadCompleted = 2 | Znamená, že operace stahování byla dokončena s úspěchem, částečnou chybou nebo selháním.
      InstallationApproved=3 | Znamená, že operace stahování byla dokončena dříve a Repair Manager schválila instalaci.
      InstallationInProgress=4 | Odpovídá stavu provádění úlohy opravy.
      InstallationCompleted=5 | Předpokládá, že se instalace dokončila s úspěchem, částečnou úspěchem nebo selháním.
      RestartRequested=6 | Implikuje instalaci opravy dokončenou a na uzlu je nedokončená akce restartování.
      RestartNotNeeded=7 |  Znamená, že po dokončení instalace opravy nebyl restart potřeba.
      RestartCompleted=8 | Znamená, že restart byl úspěšně dokončen.
      OperationCompleted=9 | Operace Windows Update se úspěšně dokončila.
      OperationAborted=10 | Znamená, že operace Windows Update je přerušená.

6. V 1.4.0 a vyšší části aplikace platí, že při dokončení pokusu o aktualizaci na uzlu se na NodeAgentService pošle událost s vlastností "WUOperationStatus-[Název_uzlu]", která upozorní, že se další pokus o stažení a instalaci aktualizace spustí. Viz následující obrázek:

     [![Obrázek stavu operace Windows Update](media/service-fabric-patch-orchestration-application/wuoperationstatusc.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusc.png#lightbox)

### <a name="diagnostic-logs"></a>Diagnostické protokoly

Protokoly aplikací orchestrace oprav se shromažďují jako součást protokolů Service Fabric runtime.

V případě, že chcete protokoly zachytit prostřednictvím diagnostického nástroje nebo kanálu podle vašeho výběru. Aplikace orchestrace oprav používá níže uvedená ID zprostředkovatelů k protokolování událostí prostřednictvím [zdroje událostí](https://docs.microsoft.com/dotnet/api/system.diagnostics.tracing.eventsource?view=netframework-4.5.1) .

- e39b723c-590c-4090-abb0-11e3e6616346
- fc0028ff-bfdc-499f-80dc-ed922c52c5e9
- 24afa313-0d3b-4c7c-b485-1047fd964b60
- 05dc046c-60e9-4ef7-965e-91660adffa68

### <a name="health-reports"></a>Sestavy stavu

Aplikace Orchestration také zveřejňuje zprávy o stavu ve službě koordinátora nebo službě agenta uzlů v následujících případech:

#### <a name="the-node-agent-ntservice-is-down"></a>NTService agenta uzlu je mimo provoz.

Pokud je agent uzlu NTService mimo provoz na uzlu, bude pro službu agenta uzlu vygenerována Sestava stavu na úrovni upozornění.

#### <a name="the-repair-manager-service-is-not-enabled"></a>Služba opravy správce není povolena.

Pokud se v clusteru nenajde služba Repair Manager, pro službu koordinátora se vygeneruje sestava stavu na úrovni upozornění.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

Otázka: **Proč je můj cluster zobrazen v chybovém stavu, když je spuštěná aplikace Orchestration patch?**

A. Během procesu instalace aplikace orchestrace opravy zakáže nebo restartuje uzly, což může dočasně způsobit stav clusteru.

Na základě zásad pro aplikaci může během operace opravy přejít buď na jeden uzel, *nebo* může celá upgradovací doména přejít současně.

Po ukončení instalace web Windows Update jsou uzly znovu povoleny po restartování.

V následujícím příkladu se cluster dočasně přesunul do chybového stavu, protože byly vypnuté dva uzly a zásady MaxPercentageUnhealthyNodes se porušily. Tato chyba je dočasná, dokud nepokračuje operace opravy.

![Obrázek clusteru, který není v pořádku](media/service-fabric-patch-orchestration-application/MaxPercentage_causing_unhealthy_cluster.png)

Pokud se problém nevyřeší, přečtěte si část věnované řešení potíží.

Otázka: **Aplikace orchestrace oprav je ve stavu upozornění.**

A. Zkontrolujte, zda je zpráva o stavu odeslaná na aplikaci hlavní příčinou. Upozornění obsahuje obvykle podrobnosti o problému. Pokud je problém přechodný, očekává se, že se aplikace automaticky obnoví z tohoto stavu.

Otázka: **Co můžu udělat, když má cluster špatný stav a potřebuji udělat aktualizaci operačního systému jako urgenti?**

A. Aplikace Orchestration neinstaluje aktualizace, Pokud cluster není v pořádku. Zkuste svůj cluster převést do stavu v pořádku, aby se odblokoval pracovní postup aplikace orchestrace opravy.

Otázka: **Mám pro svůj cluster nastavit TaskApprovalPolicy jako "NodeWise" nebo "UpgradeDomainWise"?**

A. ' UpgradeDomainWise ' zajistí rychlejší opravy clusterů tím, že opraví všechny uzly patřící do upgradovací domény paralelně. To znamená, že uzly patřící k celé doméně upgradu nebudou během procesu opravy k [](https://docs.microsoft.com/dotnet/api/system.fabric.query.nodestatus?view=azure-dotnet#System_Fabric_Query_NodeStatus_Disabled) dispozici (v zakázaném stavu).

V důsledku toho, že se zásady NodeWise opravují jenom na jednom uzlu, znamená to, že by Celková oprava clusteru mohla trvat déle. V případě maximálního počtu však bude během procesu opravy nedostupný pouze [](https://docs.microsoft.com/dotnet/api/system.fabric.query.nodestatus?view=azure-dotnet#System_Fabric_Query_NodeStatus_Disabled) jeden uzel (v zakázaném stavu).

Pokud váš cluster může tolerovat spouštění v N-1 počtu domén upgradu během cyklu oprav (kde N je celkový počet domén upgradu v clusteru), můžete zásadu nastavit jako UpgradeDomainWise, jinak ji nastavit na NodeWise.

Otázka: **Kolik času trvá k opravě uzlu?**

A. Oprava uzlu může trvat několik minut (například: [Aktualizace definic v programu Windows Defender](https://www.microsoft.com/en-us/wdsi/definitions)) do hodin (například: [Kumulativní aktualizace systému Windows](https://www.catalog.update.microsoft.com/Search.aspx?q=windows%20server%20cumulative%20update)). Čas potřebný k opravě uzlu závisí hlavně na 
 - Velikost aktualizací
 - Počet aktualizací, které se musí použít v okně oprav
 - Čas potřebný k instalaci aktualizací, restartování uzlu (v případě potřeby) a dokončení instalačních kroků po restartování
 - Výkon virtuálních počítačů/počítačů a síťových podmínek.

Otázka: **Jak dlouho trvá oprava celého clusteru?**

A. Čas potřebný k opravě celého clusteru závisí na následujících faktorech:

- Čas potřebný k opravě uzlu.
- Zásady služby koordinátora. – Výchozí zásada `NodeWise`má za následek opravy pouze jednoho uzlu v čase, což by bylo pomalejší než `UpgradeDomainWise`. Příklad: Pokud má uzel po dobu 1 hodiny, která se má opravit, aby se mohl opravit cluster 20 uzlů (stejný typ uzlů) s pěti doménami upgradu, z nichž každý obsahuje 4 uzly.
    - Oprava celého clusteru by měla trvat 20 hodin, pokud je zásada`NodeWise`
    - Pokud je zásada, měla by trvat 5 hodin.`UpgradeDomainWise`
- Zatížení clusteru – každá operace opravy vyžaduje přemístění úlohy zákazníka k ostatním dostupným uzlům v clusteru. Oprava uzlu by byla během této doby [zakázána](https://docs.microsoft.com/dotnet/api/system.fabric.query.nodestatus?view=azure-dotnet#System_Fabric_Query_NodeStatus_Disabling) . Pokud cluster běží blízko špičky zátěže, proces vypnutí bude trvat déle. Proto se může v takových přípravných podmínkách zpomalit celkový proces opravy.
- Selhání stavu clusteru během opravy – jakékoli [snížení](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthstate?view=azure-dotnet#System_Fabric_Health_HealthState_Error) [stavu clusteru](https://docs.microsoft.com/azure/service-fabric/service-fabric-health-introduction) by přerušilo proces opravy. To by mohlo přidat k celkovému času potřebnému k opravě celého clusteru.

Otázka: **Proč se mi zobrazují některé aktualizace v web Windows Update výsledky získány prostřednictvím REST API, ale ne pod historií web Windows Update v počítači?**

A. Některé aktualizace produktu se zobrazí pouze v příslušné historii aktualizace/opravy. Například aktualizace programu Windows Defender mohou nebo nemusí být zobrazeny v historii web Windows Update v systému Windows Server 2016.

Otázka: **Dá se použít k opravě clusteru pro vývoj (cluster s jedním uzlem), aby se mohla opravit aplikace Orchestration?**

A. Ne, aplikace orchestrace oprav se nedá použít k opravě clusteru s jedním uzlem. Toto omezení je záměrné vzhledem k tomu, že [systémové služby Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-technical-overview#system-services) nebo aplikace pro zákazníky budou čelit výpadkům, takže všechny úlohy opravy pro opravy by nikdy neschválil správce oprav.

Otázka: **Návody opravit uzly clusteru v systému Linux?**

A. Informace o orchestraci aktualizací v systému Linux najdete v tématu [automatické upgrady bitových kopií operačního systému Azure Virtual Machine Scale set](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade) .

Otázka.**Proč je cyklus aktualizace zabírat dlouho?**

A. Dotaz na výsledek JSON, potom Projděte položku cyklus aktualizace pro všechny uzly a pak se můžete pokusit zjistit čas potřebný při instalaci aktualizace na každém uzlu pomocí OperationStartTime a OperationTime (OperationCompletionTime). Pokud se objevil velký časový interval, ve kterém se žádná aktualizace neprovádí, může to být tím, že cluster byl v chybovém stavu a z důvodu, že správce oprav neschválil žádné jiné úkoly opravy rodu POA. Pokud instalace aktualizace trvala na jakémkoli uzlu, může být možné, že se uzel neaktualizoval z dlouhého času a spousta aktualizací čekala na instalaci, což trvalo čas. Může také dojít k tomu, že je v důsledku zablokování uzlu při vypnutí stavu zablokované opravy na uzlu, což obvykle nastane, protože jeho zakázání může vést k situaci kvora nebo ztráty dat.

Otázka: **Proč je nutné zakázat uzel při jeho opravách pro POA?**

A. Aplikace orchestrace opravy zakáže uzel s záměrem restartu, který zastaví nebo znovu přidělí všechny služby Service Fabric spuštěné v uzlu. K tomu je potřeba zajistit, aby aplikace nekončily pomocí kombinace nových a starých knihoven DLL, takže se nedoporučuje opravit uzel bez jeho zakázání.

## <a name="disclaimers"></a>Právní omezení

- Aplikace Orchestration patch přijímá licenční smlouvu s koncovým uživatelem web Windows Update jménem uživatele. Volitelně můžete nastavení vypnout v konfiguraci aplikace.

- Aplikace orchestrace oprav shromažďuje telemetrii pro sledování využití a výkonu. Telemetrie aplikace sleduje nastavení nastavení telemetrie Service Fabric runtime (což je ve výchozím nastavení zapnuté).

## <a name="troubleshooting"></a>Řešení potíží

### <a name="a-node-is-not-coming-back-to-up-state"></a>Uzel se nevrací zpět do stavu nahoru.

**Uzel může být zablokovaný v zakázaném stavu z těchto důvodů**:

Čeká se na kontrolu zabezpečení. Chcete-li tuto situaci napravit, ujistěte se, že je v dobrém stavu k dispozici dostatek uzlů.

**Uzel může být zablokovaný v zakázaném stavu, protože**:

- Uzel byl zakázán ručně.
- Uzel byl zakázán z důvodu probíhající úlohy infrastruktury Azure.
- Uzel byl dočasně zakázán aplikací orchestrace opravy, aby bylo možné tento uzel opravit.

**Uzel může být zablokovaný v nefunkčním stavu, protože**:

- Uzel byl umístěn v nefunkčním stavu ručně.
- Uzel se přechází na restart (který může aktivovat aplikace orchestrace opravy).
- Uzel je mimo provoz kvůli vadnému VIRTUÁLNÍmu počítači nebo problémům s připojením k síti.

### <a name="updates-were-skipped-on-some-nodes"></a>Na některých uzlech se přeskočily aktualizace.

Aplikace Orchestration se pokusí nainstalovat Windows Update podle zásad přeplánování. Služba se pokusí obnovit uzel a přeskočit aktualizaci podle zásad použití.

V takovém případě se sestava stavu na úrovni upozornění generuje proti službě agenta uzlu. Výsledek web Windows Update také obsahuje možný důvod selhání.

### <a name="the-health-of-the-cluster-goes-to-error-while-the-update-installs"></a>Stav clusteru přejde k chybě při instalaci aktualizace.

Chybná aktualizace systému Windows může přinést stav aplikace nebo clusteru v konkrétní uzel nebo upgradovací doméně. Aplikace orchestrace oprav po dalším web Windows Update operaci nepokračuje, dokud nebude cluster znovu v pořádku.

Správce musí zasáhnout a zjistit, proč se aplikace nebo cluster stala špatným kvůli web Windows Update.

## <a name="release-notes"></a>Poznámky k verzi

>[!NOTE]
> Od verze 1.4.0, poznámky k verzi a vydání můžete najít na [stránce](https://github.com/microsoft/Service-Fabric-POA/releases/)verze GitHubu.

### <a name="version-110"></a>Verze 1.1.0
- Veřejná verze

### <a name="version-111"></a>Verze 1.1.1
- Opravili jsme chybu v SetupEntryPoint NodeAgentService, která zabránila instalaci NodeAgentNTService.

### <a name="version-120"></a>Verzi 1.2.0

- Opravy chyb kolem pracovního postupu pro restartování systému.
- Oprava chyb při vytváření úloh služby RM v důsledku kontroly stavu během přípravy úloh opravy neprobíhá podle očekávání.
- Změnil se režim spouštění pro Windows Service POANodeSvc z automatické na odložené – automaticky.

### <a name="version-121"></a>Verze 1.2.1

- Oprava chyb v pracovním postupu škálování na více clusterů. Zavedla se logika uvolňování paměti pro úlohy opravy POA patřící k neexistujícím uzlům.

### <a name="version-122"></a>Verze 1.2.2

- Různé opravy chyb.
- Binární soubory jsou nyní podepsány.
- Přidání odkazu sfpkg pro aplikaci

### <a name="version-130"></a>Verze 1.3.0

- Nastavením InstallWindowsOSOnlyUpdates na hodnotu false nyní nainstalujete všechny dostupné aktualizace.
- Změnila se logika zakázání automatických aktualizací. Tím se vyřeší chyba, kdy se na serveru 2016 a novějších neaktivovaly automatické aktualizace.
- Parametrizované omezení umístění pro mikroslužby v případě pokročilého použití.

### <a name="version-131"></a>Verze 1.3.1
- Oprava regrese, kde POA 1.3.0 nebude fungovat v systému Windows Server 2012 R2 nebo nižším, protože se nezdařilo zakázat automatické aktualizace. 
- Oprava chyby, kdy se konfigurace InstallWindowsOSOnlyUpdates vždycky vybrala jako true.
- Změna výchozí hodnoty InstallWindowsOSOnlyUpdates na false.

### <a name="version-132"></a>Verze 1.3.2
- Oprava problému, který nastavil životní cyklus opravy v uzlu v případě, že existují uzly s názvem, který je podmnožinou aktuálního názvu uzlu. U takových uzlů je to možné, že nejsou k dispozici opravy nebo se čeká na restartování. 
