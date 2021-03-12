---
title: Oprava operačního systému Windows v clusteru Service Fabric
description: Tento článek popisuje, jak automatizovat opravy operačního systému na Service Fabric clusteru pomocí aplikace Orchestration.
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
ms.openlocfilehash: e51b247f8c1a5a9ed8f6ec8e24363015afb2f7de
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102614407"
---
# <a name="patch-the-windows-operating-system-in-your-service-fabric-cluster"></a>Oprava operačního systému Windows v clusteru Service Fabric

## <a name="automatic-os-image-upgrades"></a>Automatické upgrady bitových kopií operačního systému

Získání [automatických upgradů bitových kopií operačního systému na Virtual Machine Scale Sets](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md) je osvědčeným postupem, jak udržovat operační systém v Azure. Automatické upgrady bitových kopií operačního systému založené na sadě škálování virtuálních počítačů budou vyžadovat u sady škálování stříbrné nebo větší trvanlivost.

Požadavky na automatické upgrady bitových kopií operačního systému pomocí Virtual Machine Scale Sets
-   [Úroveň odolnosti](../service-fabric/service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster) Service Fabric je stříbrná nebo zlatá a není bronzová.
-   Rozšíření Service Fabric v definici modelu sady škálování musí mít TypeHandlerVersion 1,1 nebo vyšší.
-   Úroveň odolnosti by měla být stejná jako u Service Fabric clusteru a rozšíření Service Fabric v definici modelu sady škálování.
- Další sondu stavu nebo použití rozšíření pro stav aplikace pro Virtual Machine Scale Sets není vyžadováno.

Zajistěte, aby se nastavení odolnosti neshodovalo s Service Fabric clusterem a rozšířením Service Fabric, protože v důsledku neshody dojde k chybám upgradu. Úrovně trvanlivosti se dají upravovat podle pokynů popsaných na [této stránce](../service-fabric/service-fabric-cluster-capacity.md#changing-durability-levels).

V případě bronzové odolnosti není k dispozici automatický upgrade pro image operačního systému. I když se [aplikace Orchestration](#patch-orchestration-application ) (určená jenom pro clustery, které nejsou hostované na Azure) *nedoporučují* pro stříbrné nebo vyšší úrovně odolnosti, jedná se o jedinou možnost pro automatizaci aktualizací Windows s ohledem na Service Fabric upgradovacích domén.

> [!IMPORTANT]
> Upgrady v rámci virtuálních počítačů, kde web Windows Update používá opravy operačního systému bez nahrazení disku s operačním systémem, se v Azure Service Fabric nepodporují.

K povolení funkce zakázané web Windows Update v operačním systému je potřeba provést dva kroky.

1. Povoluje se automatické aktualizace bitové kopie operačního systému, což zakazuje Windows Updates ARM. 
    ```json
    "virtualMachineProfile": { 
        "properties": {
          "upgradePolicy": {
            "automaticOSUpgradePolicy": {
              "enableAutomaticOSUpgrade":  true
            }
          }
        }
      }
    ```
    
    ```json
    "virtualMachineProfile": { 
        "osProfile": { 
            "windowsConfiguration": { 
                "enableAutomaticUpdates": false 
            }
        }
    }
    ```

    Azure PowerShell
    ```azurepowershell-interactive
    Update-AzVmss -ResourceGroupName $resourceGroupName -VMScaleSetName $scaleSetName -AutomaticOSUpgrade $true -EnableAutomaticUpdate $false
    ``` 
    
1. Aktualizovat model sady škálování po této změně konfigurace je potřeba znovu vytvořit bitovou kopii všech počítačů, aby se aktualizoval model sady škálování, aby se změna projevila.
    
    Azure PowerShell
    ```azurepowershell-interactive
    $scaleSet = Get-AzVmssVM -ResourceGroupName $resourceGroupName -VMScaleSetName $scaleSetName
    $instances = foreach($vm in $scaleSet)
    {
        Set-AzVmssVM -ResourceGroupName $resourceGroupName -VMScaleSetName $scaleSetName -InstanceId $vm.InstanceID -Reimage
    }
    ``` 
    
Další pokyny najdete v tématu [automatické upgrady bitových kopií operačního systému pomocí Virtual Machine Scale Sets](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md) .

## <a name="patch-orchestration-application"></a>Aplikace orchestrace oprav

> [!IMPORTANT]
> Od 30. dubna 2019 již není podporována aplikace orchestrace verze 1,2. *. Ujistěte se, že upgradujete na nejnovější verzi.

Aplikace orchestrace opravy (POA) je obálkou v rámci služby Azure Service Fabric Repair Manager, která umožňuje plánování oprav operačního systému na základě konfigurace pro clustery hostované mimo Azure. Pro clustery, které nejsou hostované na Azure, se nepožaduje POA, ale k opravě Service Fabric hostitelů clusteru bez výpadků je potřeba naplánovat instalaci opravy pomocí aktualizační domény.

POA je Service Fabric aplikace, která automatizuje opravy operačního systému v clusteru Service Fabric, aniž by docházelo k výpadkům.

POA poskytuje následující funkce:

- **Instalace automatické aktualizace operačního systému**. Aktualizace operačního systému se automaticky stáhnou a nainstalují. Clusterové uzly se restartují podle potřeby, aniž by docházelo k výpadkům clusteru.

- **Opravy podporující clustery a integrace stavu**. I když POA používá aktualizace, monitoruje stav uzlů clusteru. Uzly clusteru jsou aktualizovány v jednom nebo více uzlech najednou nebo v každé doméně aktualizace. Pokud dojde k výpadku stavu clusteru z důvodu procesu opravy, dojde k zastavení opravy, aby nedošlo ke zhoršení problému.

## <a name="internal-details-of-poa"></a>Interní podrobnosti rodu POA

POA se skládá z následujících podsoučástí:

- **Služba koordinátora**: Tato stavová služba zodpovídá za:
    - Koordinace úlohy web Windows Update v celém clusteru.
    - Ukládání výsledků dokončených operací web Windows Update.

- **Služba agenta uzlu**: Tato Bezstavová služba běží na všech uzlech clusteru Service Fabric. Služba zodpovídá za:
    - Probíhá zavádění agenta uzlu NTService.
    - Monitorování agenta uzlu NTService.

- **NTService agenta uzlu**: Tato služba systému Windows NT běží na úrovni oprávnění (systém) vyšší úrovně. Naproti tomu se služba agenta pro uzly a služba koordinátora spouští s oprávněními nižší úrovně (síťová služba). Služba zodpovídá za provádění následujících úloh web Windows Update na všech uzlech clusteru:
    - Zakázání automatických aktualizací Windows na uzlu.
    - Stažení a instalace aktualizací Windows podle zásad, které uživatel zadal
    - Restart instalace aktualizací po instalaci systému Windows.
    - Nahrávají se výsledky aktualizací Windows do služby koordinátora.
    - Vytváření sestav o stavu, pokud po vyčerpání všech opakovaných pokusů dojde k selhání operace.

> [!NOTE]
> POA používá službu Service Fabric Repair Manager k zakázání nebo povolení uzlu a provádění kontrol stavu. Úloha opravy vytvořená v POA sleduje průběh web Windows Update pro každý uzel.

## <a name="prerequisites"></a>Požadavky

> [!NOTE]
> Požadovaná minimální verze .NET Framework je 4,6.

### <a name="enable-the-repair-manager-service-if-its-not-running-already"></a>Povolit službu Repair Manager (Pokud ještě není spuštěná)

POA vyžaduje, aby byla v clusteru povolená služba Repair Manager.

#### <a name="azure-clusters"></a>Clustery Azure

Clustery Azure v úrovni odolnosti stříbra mají ve výchozím nastavení povolenou službu Repair Manager. Clustery Azure v zlatý úrovni odolnosti může nebo nemusí mít povolenou službu Repair Manager, podle toho, kdy byly tyto clustery vytvořené. Clustery Azure v úrovni bronzové odolnosti ve výchozím nastavení nemají povolenou službu Repair Manager. Pokud je tato služba již povolená, můžete ji zobrazit v části systémové služby v Service Fabric Explorer.

##### <a name="the-azure-portal"></a>Azure Portal
Při nastavování clusteru můžete povolit Repair Manager z Azure Portal. Když konfigurujete cluster, vyberte možnost **zahrnout Repair Manager** v části **Doplňky – funkce**.

![Obrázek povolení Repair Manager z Azure Portal](media/service-fabric-patch-orchestration-application/EnableRepairManager.png)

##### <a name="the-azure-resource-manager-deployment-model"></a>Model nasazení Azure Resource Manager
Případně můžete použít [model nasazení Azure Resource Manager](./service-fabric-cluster-creation-via-arm.md) k povolení služby Repair Manager na novém i existujícím clusteru Service Fabric. Získejte šablonu pro cluster, který chcete nasadit. Můžete buď použít ukázkové šablony, nebo vytvořit vlastní šablonu modelu nasazení Azure Resource Manager. 

Pokud chcete službu Repair Manager povolit pomocí [šablony modelu nasazení Azure Resource Manager](./service-fabric-cluster-creation-via-arm.md), udělejte toto:

1. Zkontrolujte, jestli `apiVersion` je u prostředku *Microsoft. ServiceFabric/clusterů* nastavená *verze 2017-07-01-Preview* . Pokud se liší, musíte aktualizovat `apiVersion` na *2017-07-01-Preview* nebo novější:

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

1. Povolte službu Repair Manager přidáním následujícího `addonFeatures` oddílu za `fabricSettings` oddíl:

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

3. Po aktualizaci šablony clusteru pomocí těchto změn ji nainstalujte a nechte dokončit aktualizaci. Teď uvidíte službu Repair Manager spuštěnou ve vašem clusteru. Označuje se jako *Fabric:/System/RepairManagerService* v části systémové služby v Service Fabric Explorer. 

### <a name="standalone-on-premises-clusters"></a>Samostatné místní clustery

Pokud chcete službu Repair Manager povolit na novém nebo existujícím clusteru Service Fabric, můžete použít [nastavení konfigurace pro samostatný cluster Windows](./service-fabric-cluster-manifest.md).

Povolení služby Repair Manager:

1. Zkontrolujte, zda `apiVersion` je v části [obecné konfigurace clusteru](./service-fabric-cluster-manifest.md#general-cluster-configurations) nastavena na *04-2017* nebo vyšší, jak je znázorněno zde:

    ```json
    {
        "name": "SampleCluster",
        "clusterConfigurationVersion": "1.0.0",
        "apiVersion": "04-2017",
        ...
    }
    ```

1. Povolte službu Repair Manager přidáním následujícího `addonFeatures` oddílu za `fabricSettings` oddíl, jak je znázorněno zde:

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

1. Aktualizujte manifest clusteru pomocí těchto změn pomocí aktualizovaného manifestu clusteru [vytvořte nový cluster](./service-fabric-cluster-creation-for-windows-server.md) nebo [upgradujte konfiguraci clusteru](./service-fabric-cluster-upgrade-windows-server.md). 

   Po spuštění clusteru s aktualizovaným manifestem clusteru můžete zobrazit službu Repair Manager spuštěnou ve vašem clusteru. Označuje se jako *Fabric:/System/RepairManagerService* a je v části systémové služby v Service Fabric Explorer.

### <a name="configure-windows-updates-for-all-nodes"></a>Konfigurovat aktualizace Windows pro všechny uzly

Automatické aktualizace Windows můžou způsobit ztrátu dostupnosti, protože víc uzlů clusteru se může restartovat současně. POA se ve výchozím nastavení pokusí zakázat automatické aktualizace Windows na všech uzlech clusteru. Pokud však nastavení spravuje správce nebo Zásady skupiny, doporučujeme nastavit zásadu web Windows Update na hodnotu "upozorňovat před explicitním stažením".

## <a name="download-the-application-package"></a>Stažení balíčku aplikace

Chcete-li stáhnout balíček aplikace, navštivte [stránku Release Orchestration Application](https://github.com/microsoft/Service-Fabric-POA/releases/latest/) na GitHubu.

## <a name="configure-poa-behavior"></a>Konfigurace chování POA

Chování POA můžete nakonfigurovat tak, aby vyhovovalo vašim potřebám. Přepište výchozí hodnoty předáním parametru aplikace při vytváření nebo aktualizaci aplikace. Parametry aplikace můžete zadat zadáním `ApplicationParameter` `Start-ServiceFabricApplicationUpgrade` `New-ServiceFabricApplication` rutin nebo.

| Parametr        | Typ                          | Podrobnosti |
|:-|-|-|
|MaxResultsToCache    |Dlouhou                              | Maximální počet web Windows Update výsledků, které by měly být uloženy do mezipaměti. <br><br>Výchozí hodnota je 3000 za předpokladu, že: <br> &nbsp;&nbsp;– Počet uzlů je 20. <br> &nbsp;&nbsp;– Počet aktualizací uzlu za měsíc je 5. <br> &nbsp;&nbsp;-Počet výsledků na operaci může být 10. <br> &nbsp;&nbsp;– Výsledky za poslední tři měsíce by měly být uloženy. |
|TaskApprovalPolicy   |Výčet <br> { NodeWise, UpgradeDomainWise }                          |TaskApprovalPolicy určuje zásadu, kterou má služba koordinátora použít k instalaci aktualizací Windows v uzlech clusteru Service Fabric.<br><br>Povolené hodnoty jsou následující: <br>*NodeWise*: aktualizace systému Windows jsou nainstalovány vždy po jednom uzlu. <br> *UpgradeDomainWise*: aktualizace systému Windows jsou nainstalovány v jednu chvíli po doméně aktualizace. (Na maximum můžou všechny uzly patřící k doméně aktualizace přejít ke službě Windows Update.)<br><br> V části [Nejčastější dotazy](#frequently-asked-questions) vám pomůžou určit, které zásady jsou pro váš cluster nejvhodnější.
|LogsDiskQuotaInMB   |Dlouhou  <br> (Výchozí: *1024*)               | Maximální velikost protokolů aplikace orchestrace oprav v MB, které je možné trvale uchovat místně na uzlech.
| WUQuery               | řetězec<br>(Výchozí: *IsInstalled = 0*)                | Dotaz pro získání aktualizací Windows Další informace najdete v tématu [WuQuery.](/windows/win32/api/wuapi/nf-wuapi-iupdatesearcher-search)
| InstallWindowsOSOnlyUpdates | *Logická hodnota* <br> (výchozí: NEPRAVDA)                 | Pomocí tohoto příznaku můžete řídit, které aktualizace se mají stáhnout a nainstalovat. Jsou povoleny následující hodnoty. <br>true – nainstaluje jenom aktualizace operačního systému Windows.<br>false – nainstaluje všechny dostupné aktualizace na počítač.          |
| WUOperationTimeOutInMinutes | Int <br>(Výchozí: *90*)                   | Určuje časový limit pro jakoukoliv operaci web Windows Update (hledání nebo stažení nebo instalace). Pokud operace není dokončena v rámci zadaného časového limitu, je přerušena.       |
| WURescheduleCount     | Int <br> (Výchozí: *5*)                  | Maximální počet, kolikrát služba aktualizuje službu Windows Update, pokud se operace trvale nezdařila.          |
| WURescheduleTimeInMinutes | Int <br>(Výchozí: *30*) | Interval, ve kterém služba aktualizuje aktualizace systému Windows, pokud chyba přetrvává. |
| WUFrequency           | Textový řetězec s oddělovači (výchozí: *týdně, středa, 7:00:00*)     | Frekvence pro instalaci aktualizací systému Windows. Formáty a možné hodnoty jsou následující: <br>-Monthly, DD, HH: MM: SS (příklad: *měsíčně, 5, 12:22:32*). Povolené hodnoty pro pole _DD_ (den) jsou čísla od 1 do 28 do _Poslední_. <br>-Týdně, Day, HH: MM: SS (příklad: *týdně, úterý, 12:22:32*)  <br>-Denně, HH: MM: SS (příklad: *denně, 12:22:32*)  <br>-Week, Day, HH: MM: SS (příklad: *2, pátek, 21:00:00* INDIKUJE 9:00 dop. UTC v pátek za druhý týden v každém měsíci) <br>- *Žádné* znamená, že aktualizace systému Windows by se neměly provádět.  <br><br> Časy jsou ve formátu UTC.|
| AcceptWindowsUpdateEula | Logická hodnota <br>(Výchozí: *pravda*) | Nastavením tohoto příznaku přijímá aplikace End-User licenční smlouvu web Windows Update jménem vlastníka počítače.              |

> [!TIP]
> Pokud chcete, aby aktualizace systému Windows byly provedeny ihned, nastavte `WUFrequency` relativní čas nasazení aplikace. Předpokládejme například, že máte testovací cluster s pěti uzly a naplánujete nasazení aplikace v přibližně 5:00./odp. UTC. Pokud předpokládáte, že upgrade nebo nasazení aplikace trvá maximálně 30 minut, nastavte WUFrequency jako *denní 17:30:00*.

## <a name="deploy-poa"></a>Nasazení POA

1. Dokončete všechny požadované kroky a připravte cluster.
1. Nasaďte POA jako jakoukoli jinou aplikaci Service Fabric. Pokud ho chcete nasadit pomocí PowerShellu, přečtěte si téma [nasazení a odebrání aplikací pomocí PowerShellu](./service-fabric-deploy-remove-applications.md).
1. Chcete-li nakonfigurovat aplikaci v době nasazení, předejte `ApplicationParameter` `New-ServiceFabricApplication` rutinu do rutiny. Pro usnadnění práce jsme zadali skript Deploy.ps1 společně s aplikací. Postup použití skriptu:

    - Připojte se ke clusteru Service Fabric pomocí `Connect-ServiceFabricCluster` .
    - Spusťte skript prostředí PowerShell Deploy.ps1 s příslušnou `ApplicationParameter` hodnotou.

> [!NOTE]
> Nechejte skript a složku aplikace *PatchOrchestrationApplication* ve stejném adresáři.

## <a name="upgrade-poa"></a>Aktualizace pro POA

Pokud chcete upgradovat verzi POA pomocí prostředí PowerShell, postupujte podle pokynů v tématu [Service Fabric upgrade aplikace pomocí PowerShellu](./service-fabric-application-upgrade-tutorial-powershell.md).

## <a name="remove-poa"></a>Odebrat POA

Pokud chcete aplikaci odebrat, postupujte podle pokynů v tématu [nasazení a odebrání aplikací pomocí PowerShellu](./service-fabric-deploy-remove-applications.md).

Pro usnadnění práce jsme zadali Undeploy.ps1 skript spolu s aplikací. Postup použití skriptu:

  - Připojte se ke clusteru Service Fabric pomocí ```Connect-ServiceFabricCluster``` .
  - Spusťte skript prostředí PowerShell Undeploy.ps1.

> [!NOTE]
> Nechejte skript a složku aplikace *PatchOrchestrationApplication* ve stejném adresáři.

## <a name="view-the-windows-update-results"></a>Zobrazit výsledky web Windows Update

POA zpřístupňuje rozhraní REST API pro zobrazení historických výsledků uživatelům. Tady je příklad formátu JSON výsledku:

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
Výsledek operace uvnitř | 0 – úspěšné<br> 1 – úspěšné s chybami<br> 2 – chyba<br> 3 – přerušeno<br> 4 – přerušeno s časovým limitem | Označuje výsledek celkové operace, která obvykle zahrnuje instalaci jedné nebo více aktualizací.
ResultCode | Stejné jako výsledek operace uvnitř | Toto pole indikuje výsledek operace instalace pro jednotlivou aktualizaci.
Typ operace | 1. instalace<br> 0 – Hledat a stáhnout| Ve výchozím nastavení je instalace jedinou typem operace OperationType, která se zobrazuje ve výsledcích.
WindowsUpdateQuery | Výchozí hodnota je "IsInstalled = 0" | Web Windows Update dotaz, který se použil k hledání aktualizací. Další informace najdete v tématu [WuQuery](/windows/win32/api/wuapi/nf-wuapi-iupdatesearcher-search).
RebootRequired | true – byl vyžadován restart.<br> false – restart nebyl požadován. | Určuje, zda byl pro dokončení instalace aktualizací požadován restart.
OperationStartTime | DateTime | Určuje čas, kdy byla spuštěna operace (stažení a instalace).
OperationTime | DateTime | Určuje čas, kdy byla dokončena operace (stažení/instalace).
HResult | 0 – úspěšné<br> jiné – selhání| Označuje důvod selhání služby Windows Update s UpdateID naformátovat "7392acaf-6a85-427c-8a8d-058c25beb0d6".

Pokud ještě není naplánována žádná aktualizace, je výsledek JSON prázdný.

Přihlaste se ke clusteru a Dotazujte web Windows Update výsledků. Zjistěte IP adresu repliky pro primární adresu služby koordinátora a otevřete následující adresu URL z prohlížeče: http:// &lt; replika-IP &gt; : &lt; ApplicationPort &gt; /PatchOrchestrationApplication/v1/GetWindowsUpdateResults.

Koncový bod REST pro službu koordinátora má dynamický port. Pokud chcete vyhledat přesnou adresu URL, přečtěte si téma Service Fabric Explorer. Například výsledky jsou k dispozici na adrese *http://10.0.0.7:20000/PatchOrchestrationApplication/v1/GetWindowsUpdateResults* .

![Obrázek koncového bodu REST](media/service-fabric-patch-orchestration-application/Rest_Endpoint.png)

Pokud je reverzní proxy server povolený v clusteru, můžete k adrese URL přistupovat mimo cluster i z nich.

Koncový bod, který je třeba stisknout, je *http:// &lt; SERVERURL &gt; : &lt; REVERSEPROXYPORT &gt; /PatchOrchestrationApplication/CoordinatorService/v1/GetWindowsUpdateResults*.

Pokud chcete povolit reverzní proxy server v clusteru, postupujte podle pokynů v [reverzní proxy serveru v Azure Service Fabric](./service-fabric-reverseproxy.md). 

> 
> [!WARNING]
> Po nakonfigurování reverzního proxy serveru budou všechny mikroslužby v clusteru, které zveřejňují koncový bod HTTP, adresovatelné z vnějšího clusteru.

## <a name="diagnostics-and-health-events"></a>Události diagnostiky a stavu

Tato část popisuje, jak ladit nebo diagnostikovat problémy s aktualizacemi Update prostřednictvím POA na Service Fabric clustery.

> [!NOTE]
> Chcete-li získat mnoho z následujících povolaných vylepšení s sebou samým, měli byste mít nainstalovanou verzi POA verze 1.4.0 nebo novější.

Agent Node NTService vytvoří [úlohy opravy](/dotnet/api/system.fabric.repair.repairtask) pro instalaci aktualizací na uzlech. Každou úlohu potom připraví služba koordinátora v souladu se zásadami schvalování úkolů. Připravené úkoly jsou nakonec schváleny Repair Manager, které neschvalují žádnou úlohu, pokud je cluster ve stavu není v pořádku. 

Abychom vám pomohli porozumět tomu, jak aktualizace postupují v uzlu, pojďme přejít na krok za krokem:

1. NodeAgentNTService běžící na každém uzlu vyhledá dostupné aktualizace Windows v naplánovaném čase. Pokud jsou aktualizace k dispozici, stáhne je na uzel.

1. Po stažení aktualizací agent Node NTService vytvoří odpovídající úlohu opravy pro uzel s názvem *POS___ \<unique_id>*. Tyto úlohy opravy můžete zobrazit pomocí rutiny [Get-ServiceFabricRepairTask](/powershell/module/servicefabric/get-servicefabricrepairtask) nebo pomocí SFX v části Podrobnosti uzlu. Po vytvoření úlohy opravy se rychle přesune do [ *přihlášeného* stavu](/dotnet/api/system.fabric.repair.repairtaskstate).

1. Služba koordinátora pravidelně hledá úlohy opravy v *deklarovaném* stavu a pak je *aktualizuje na základě* TaskApprovalPolicy. Pokud je TaskApprovalPolicy nakonfigurovaná tak, aby NodeWise, je úloha opravy, která odpovídá uzlu, připravená jenom v případě, že se v současné době neprovádí *Příprava*, *schválení*, *spuštění* nebo *obnovení* stavu žádné jiné úlohy opravy. 

   Podobně platí, že v případě UpgradeWise TaskApprovalPolicy jsou v předchozích stavech úlohy pouze pro uzly, které patří do stejné aktualizační domény. Po přesunu úlohy opravy do stavu *Příprava* je odpovídající uzel Service Fabric [zakázán](/powershell/module/servicefabric/disable-servicefabricnode) se sadou záměrů k *restartování*.

   POA verze 1.4.0 a později odesílá události s vlastností ClusterPatchingStatus v CoordinatorService pro zobrazení uzlů, které jsou opraveny. Aktualizace se instalují na _poanode_0, jak je znázorněno na následujícím obrázku:

    [![Obrázek stavu opravy clusteru](media/service-fabric-patch-orchestration-application/clusterpatchingstatus.png)](media/service-fabric-patch-orchestration-application/clusterpatchingstatus.png#lightbox)

1. Po deaktivaci uzlu je úloha opravy přesunuta do stavu *spuštěno* . 
   
   > [!NOTE]
   > Uzel, který je zablokovaný v *zakázaném* stavu, může blokovat novou úlohu opravy, která zastaví operaci opravy v clusteru.

1. Pokud je úloha opravy v *vykonávajícím* stavu, bude zahájena instalace opravy v tomto uzlu. Po instalaci opravy se uzel může nebo nemusí restartovat v závislosti na opravě. V dalším kroku je úloha opravy přesunuta do stavu *obnovování* , čímž se uzel znovu aktivuje. Úloha opravy je pak označena jako dokončená.

   V části POA verze 1.4.0 a novější můžete zjistit stav aktualizace zobrazením událostí stavu v NodeAgentService s vlastností WUOperationStatus- \<NodeName> Property. Zvýrazněné části na následujících obrázcích ukazují stav aktualizací Windows na uzlech *poanode_0* a *poanode_2*:

   [![Snímek obrazovky ukazuje okno konzoly web Windows Update stav operace se zvýrazněným poanode_0em.](media/service-fabric-patch-orchestration-application/wuoperationstatusa.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusa.png#lightbox)

   [![Snímek obrazovky ukazuje okno konzoly web Windows Update stav operace se zvýrazněným poanode_1em.](media/service-fabric-patch-orchestration-application/wuoperationstatusb.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusb.png#lightbox)

   Podrobnosti můžete získat také pomocí prostředí PowerShell. Provedete to tak, že se připojíte ke clusteru a načtete stav úlohy opravit pomocí [Get-ServiceFabricRepairTask](/powershell/module/servicefabric/get-servicefabricrepairtask). 
   
   V následujícím příkladu je úloha "POS__poanode_2_125f2969-933c-4774-85d1-ebdf85e79f15" v *DownloadComplete* stavu. To znamená, že aktualizace byly staženy na *poanode_2* uzlu a při přechodu úlohy do stavu *spuštění* bude proveden pokus o instalaci.

   ``` powershell
    D:\service-fabric-poa-bin\service-fabric-poa-bin\Release> $k = Get-ServiceFabricRepairTask -TaskId "POS__poanode_2_125f2969-933c-4774-85d1-ebdf85e79f15"

    D:\service-fabric-poa-bin\service-fabric-poa-bin\Release> $k.ExecutorData
    {"ExecutorSubState":2,"ExecutorTimeoutInMinutes":90,"RestartRequestedTime":"0001-01-01T00:00:00"}
    ```

   Pokud se stále nacházejí další problémy, přihlaste se k virtuálnímu počítači nebo virtuálním počítačům a seznamte se s nimi pomocí protokolů událostí systému Windows. Dřív zmíněná úloha opravy může existovat jenom v následujících podstavech prováděcího modulu:

      ExecutorSubState | Description
    -- | -- 
      Žádné = 1 |  Znamená, že na uzlu neexistují žádné probíhající operace. Stav může být v přechodném stavu.
      DownloadCompleted = 2 | Znamená, že operace stahování byla dokončena se úspěchem, částečnou chybou nebo selháním.
      InstallationApproved = 3 | Znamená, že operace stahování byla dokončena dříve a Repair Manager schválila instalaci.
      InstallationInProgress = 4 | Odpovídá stavu provádění úlohy opravy.
      InstallationCompleted = 5 | Znamená, že instalace byla dokončena se úspěchem, částečnou úspěchem nebo selháním.
      RestartRequested = 6 | Znamená, že se instalace opravy dokončila a že je na uzlu nedokončená akce restartování.
      RestartNotNeeded = 7 |  Znamená, že restart nebyl po dokončení instalace opravy potřeba.
      RestartCompleted = 8 | Znamená, že restart byl úspěšně dokončen.
      Volána OperationCompleted = 9 | Operace web Windows Update byla úspěšně dokončena.
      OperationAborted = 10 | Znamená, že operace web Windows Update byla přerušena.

1. V rodu POA verze 1.4.0 a novější, když se dokončí pokus o aktualizaci uzlu, se na NodeAgentService pošle událost s vlastností WUOperationStatus-[Název_uzlu], která vás upozorní, když začne další pokus o stažení a instalaci aktualizací Windows. Zobrazuje se na následujícím obrázku:

     [![Snímek obrazovky ukazuje okno konzoly s web Windows Update stav operace s NodeAgentService.](media/service-fabric-patch-orchestration-application/wuoperationstatusc.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusc.png#lightbox)

### <a name="diagnostics-logs"></a>Protokoly diagnostiky

Protokoly aplikací orchestrace oprav se shromažďují jako součást protokolů Service Fabric runtime.

Protokoly můžete zachytit pomocí diagnostického nástroje nebo zvoleného kanálu. POA používá následující ID pevných zprostředkovatelů k protokolování událostí prostřednictvím [zdroje událostí](/dotnet/api/system.diagnostics.tracing.eventsource):

- e39b723c-590c-4090-abb0-11e3e6616346
- fc0028ff-bfdc-499f-80dc-ed922c52c5e9
- 24afa313-0d3b-4c7c-b485-1047fd964b60
- 05dc046c-60e9-4ef7-965e-91660adffa68

### <a name="health-reports"></a>Sestavy stavu

POA také zveřejňuje zprávy o stavu proti službě agenta uzlů nebo službě koordinátora v následujících scénářích:

* NTService agenta uzlu je mimo provoz.

   Pokud je agent uzlu NTService mimo provoz na uzlu, bude pro službu agenta uzlu vygenerována Sestava stavu na úrovni upozornění.

* Služba Repair Manager není povolená.

   Pokud se služba Repair Manager v clusteru nenajde, pro službu koordinátora se vygeneruje sestava stavu na úrovni upozornění.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

**Otázka: Proč se můj cluster zobrazí v chybovém stavu, když je spuštěný POA?**

Odpověď: během procesu instalace aplikace POA zakáže nebo restartuje uzly, což může dočasně vést k tomu, že cluster není v pořádku.

V závislosti na zásadách aplikace může během operace opravy přejít buď na jeden uzel, *nebo* celá aktualizační doména.

Po dokončení instalace aktualizací Windows jsou uzly znovu povoleny po restartu.

V následujícím příkladu se cluster dočasně přesunul do chybového stavu, protože byly vypnuté dva uzly a zásady MaxPercentageUnhealthyNodes se porušily. Tato chyba je dočasná, dokud nebude možné zahájit operaci opravy.

![Obrázek clusteru, který není v pořádku](media/service-fabric-patch-orchestration-application/MaxPercentage_causing_unhealthy_cluster.png)

Pokud se problém nevyřeší, přečtěte si část věnované řešení potíží.

**Otázka: co mohu udělat, pokud je POA ve stavu upozornění?**

O: Zkontrolujte, zda sestava stavu odeslaná na aplikaci označuje hlavní příčinu. Upozornění obsahuje obvykle podrobnosti o problému. Pokud je problém přechodný, očekává se, že se aplikace obnoví automaticky.

**Otázka: co mohu udělat, pokud můj cluster není v pořádku a potřebuji udělat aktualizaci operačního systému jako urgenti?**

A: POA neinstaluje aktualizace, Pokud cluster není v pořádku. Zkuste svůj cluster převést do stavu v pořádku, aby se odblokoval pracovní postup POA.

**Otázka: Mám pro svůj cluster nastavit TaskApprovalPolicy jako "NodeWise" nebo "UpgradeDomainWise"?**

Odpověď: nastavení "UpgradeDomainWise" urychluje veškerou opravu clusteru tak, že se opraví paralelně všechny uzly, které patří do aktualizační domény. Během procesu jsou uzly, které patří do celé aktualizační domény, nedostupné (v [ *zakázaném* stavu](/dotnet/api/system.fabric.query.nodestatus#System_Fabric_Query_NodeStatus_Disabled)).

Naproti tomu nastavení "NodeWise" opraví jenom jeden uzel najednou, což by znamenalo, že by Celková oprava clusteru mohla trvat delší dobu. V průběhu procesu opravy však nebude v průběhu procesu opravy k dispozici pouze jeden uzel na nejvyšší úrovni (v *zakázaném* stavu).

Pokud váš cluster může tolerovat chod na N-1 doméně aktualizací během cyklu oprav (kde N je celkový počet aktualizačních domén v clusteru), můžete zásady nastavit jako "UpgradeDomainWise". V opačném případě ho nastavte na "NodeWise".

**Otázka: jak dlouho trvá aktualizace uzlu?**

Odpověď: Oprava uzlu může trvat několik minut (například [aktualizace definic programu Windows Defender](https://www.microsoft.com/en-us/wdsi/definitions)) na hodiny (například [kumulativní aktualizace Windows](https://www.catalog.update.microsoft.com/Search.aspx?q=windows%20server%20cumulative%20update)). Čas potřebný k opravě uzlu závisí hlavně na: 
 - Velikost aktualizací
 - Počet aktualizací, které je nutné použít v okně oprav.
 - Čas potřebný k instalaci aktualizací, restartování uzlu (v případě potřeby) a dokončení instalačních kroků po restartování.
 - Výkon virtuálního počítače nebo počítače a síťové podmínky.

**Otázka: jak dlouho trvá oprava celého clusteru?**

Odpověď: čas potřebný k opravě celého clusteru závisí na:

- Čas potřebný k opravě uzlu.

- Zásady služby koordinátora. Výchozí zásada "NodeWise" má za následek opravy pouze jednoho uzlu najednou a přístup, který je pomalejší než použití "UpgradeDomainWise". 

   Příklad: Pokud bude uzel trvat 1 hodinu, bude se aktualizovat cluster s pěti uzly (stejný typ uzlů) s 5 aktualizačními doménami, z nichž každý obsahuje 4 uzly, vyžaduje:
    - "NodeWise": ~ 20 hodin.
    - "UpgradeDomainWise": ~ 5 hodin.

- Zatížení clusteru. Každá operace opravy vyžaduje přemístění úlohy zákazníka k ostatním dostupným uzlům v clusteru. Uzel, který se má opravit, by byl během této doby [ *zakázán* stavem](/dotnet/api/system.fabric.query.nodestatus#System_Fabric_Query_NodeStatus_Disabling) . Pokud cluster běží blízko špičky zátěže, proces zakázání bude trvat déle. Proto se celkový proces opravy může v takových podmínkách navýšení zpomalit.

- Selhání stavu clusteru během oprav. Jakékoli [snížení](/dotnet/api/system.fabric.health.healthstate#System_Fabric_Health_HealthState_Error) [stavu clusteru](./service-fabric-health-introduction.md) by přerušilo proces opravy. Tento problém by měl přidat do celkového času potřebného k opravě celého clusteru.

**Otázka: Proč se mi zobrazují některé aktualizace v web Windows Update výsledky získané prostřednictvím REST API, ale ne pod historií web Windows Update v počítači?**

Odpověď: některé aktualizace produktu se zobrazí pouze ve vlastní aktualizaci nebo v historii oprav. Například aktualizace programu Windows Defender mohou nebo nemusí být zobrazeny v historii web Windows Update v systému Windows Server 2016.

**Otázka: je možné použít POA k opravě vývojového clusteru (cluster s jedním uzlem)?**

Odpověď: Ne, POA nelze použít k opravě clusteru s jedním uzlem. Toto omezení je záměrné, protože [Service Fabric systémových služeb](./service-fabric-technical-overview.md#system-services) nebo jiných zákaznických aplikací by mohlo dojít k výpadku. Proto by se úlohy opravy opravy nikdy neschválily Repair Manager.

**Otázka: Návody oprava uzlů clusteru v systému Linux?**

Odpověď: informace o orchestraci aktualizací v systému Linux najdete v tématu [automatické upgrady bitových kopií operačního systému Azure Virtual Machine Scale set](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md).

**Otázka: Proč cyklus aktualizace trvá dlouho?**

Odpověď: dotaz na výsledky JSON, zadejte cyklus aktualizace pro všechny uzly a potom se můžete pokusit zjistit čas potřebný při instalaci aktualizace na každém uzlu pomocí OperationStartTime a OperationTime (OperationCompletionTime). 

Pokud existuje velký časový interval, ve kterém se nekoná žádná aktualizace, cluster může být v chybovém stavu a v důsledku toho Repair Manager nemůže schvalovat žádné úlohy opravy POA. Pokud instalace aktualizace trvá na jakémkoli uzlu dlouhou dobu, tento uzel se nemusí v průběhu chvilky aktualizovat. K instalaci může být potřeba čekat hodně aktualizací, což může vést k prodlevám. 

Může být taky možné, že je aktualizace uzlů blokovaná, protože je zablokovaná v *zakázaném* stavu. K tomu obvykle dochází, protože zakázání uzlu může způsobit situaci kvora nebo ztráty dat.

**Otázka: Proč je nutné, aby byl uzel zakázán v případě, že je nástroj POA opravý?**

Odpověď: POA zakáže uzel s *restartem* , který zastaví nebo znovu přidělí všechny Service Fabric služby, které jsou spuštěny na uzlu. POA má za to, že aplikace nekončí kombinací nových a starých knihoven DLL, takže nedoporučujeme opravovat uzel bez jeho zakázání.

**Dotaz: Jaký je maximální počet uzlů, které lze aktualizovat pomocí POA?**

A: POA používá Service Fabric Repair Manager k vytvoření úloh opravy pro uzly pro aktualizace. Nemůžete ale současně vyskytovat více než 250 úloh opravy. V současné době každý uzel (POA) vytváří úlohy opravy, takže POA může aktualizovat maximálně 250 uzlů v clusteru. 

## <a name="disclaimers"></a>Právní omezení

- POA přijímá licenční smlouvu End-User pro web Windows Update jménem uživatele. Volitelně můžete nastavení vypnout v konfiguraci aplikace.

- POA shromažďuje telemetrii pro sledování využití a výkonu. Telemetrie aplikace sleduje nastavení nastavení telemetrie Service Fabric runtime (což je ve výchozím nastavení zapnuté).

## <a name="troubleshooting"></a>Řešení potíží

Tato část poskytuje možná řešení pro řešení potíží s chybami při opravách uzlů.

### <a name="a-node-is-not-coming-back-to-up-state"></a>Uzel se nevrací zpět do stavu nahoru.

* Uzel může být zablokovaný ve stavu *vypnutí* z těchto důvodů:

  - Čeká se na kontrolu zabezpečení. Chcete-li tuto situaci napravit, ujistěte se, že je v dobrém stavu k dispozici dostatek uzlů.

* Uzel může být zablokovaný v *zakázaném* stavu, protože:

  - Byl zakázán ručně.
  - Byla zakázaná kvůli probíhající úloze infrastruktury Azure.
  - Byl dočasně zakázán pomocí POA k opravě uzlu.

* Uzel může být zablokovaný v nefunkčním stavu, protože:

  - Byl umístěn v nefunkčním stavu ručně.
  - Přechází se na restartování (které může aktivovat POA).
  - Má chybný virtuální počítač nebo počítač nebo se jedná o problémy s připojením k síti.

### <a name="updates-were-skipped-on-some-nodes"></a>Na některých uzlech se přeskočily aktualizace.

POA se pokusí nainstalovat Windows Update podle zásad přeplánování. Služba se pokusí obnovit uzel a přeskočit aktualizaci podle zásad použití.

V takovém případě se sestava stavu na úrovni upozornění generuje proti službě agenta uzlu. Výsledek web Windows Update také obsahuje možný důvod selhání.

### <a name="the-health-of-the-cluster-goes-to-error-while-the-update-is-being-installed"></a>Stav clusteru se při instalaci aktualizace přehraje na chybu.

Chybná aktualizace systému Windows může přinést stav aplikace nebo clusteru v konkrétní uzel nebo doméně aktualizace. POA nepokračuje v jakékoli následné operaci web Windows Update, dokud nebude cluster znovu v pořádku.

Správce musí zasáhnout a zjistit, proč se aplikace nebo cluster stala špatným kvůli web Windows Update.

## <a name="poa-release-notes"></a>Poznámky k verzi POA

>[!NOTE]
> Pro POA verze 1.4.0 a novější můžete najít poznámky k verzi a vydání na [stránce verze orchestrace oprav](https://github.com/microsoft/Service-Fabric-POA/releases/) na GitHubu.

### <a name="version-110"></a>1.1.0 verze
- Veřejná verze

### <a name="version-111"></a>Verze 1.1.1
- Opravili jsme chybu v SetupEntryPoint NodeAgentService, která zabránila instalaci NodeAgentNTService.

### <a name="version-120"></a>1.2.0 verze

- Opravy chyb kolem pracovního postupu pro restartování systému.
- Oprava chyb při vytváření úloh služby RM v důsledku kontroly stavu během přípravy úloh opravy neprobíhá podle očekávání.
- Změnil se režim spouštění pro Windows Service POANodeSvc z automatické na odložené – automaticky.

### <a name="version-121"></a>Verze 1.2.1

- Oprava chyb v pracovním postupu škálování na více clusterů. Zavedla se logika uvolňování paměti pro úlohy opravy POA patřící k neexistujícím uzlům.

### <a name="version-122"></a>Verze 1.2.2

- Různé opravy chyb.
- Binární soubory jsou nyní podepsány.
- Přidání odkazu sfpkg pro aplikaci

### <a name="version-130"></a>1.3.0 verze

- Nastavením InstallWindowsOSOnlyUpdates na hodnotu false nyní nainstalujete všechny dostupné aktualizace.
- Změnila se logika zakázání automatických aktualizací. Tím se vyřeší chyba, kdy se na serveru 2016 a novějších neaktivovaly automatické aktualizace.
- Parametrizované omezení umístění pro mikroslužby v případě pokročilého použití.

### <a name="version-131"></a>Verze 1.3.1
- Oprava regrese, kde POA 1.3.0 nebude fungovat na Windows Serveru 2012 R2 nebo starším z důvodu chyby při zakázání automatických aktualizací. 
- Oprava chyby, kdy se konfigurace InstallWindowsOSOnlyUpdates vždycky vybrala jako true.
- Změna výchozí hodnoty InstallWindowsOSOnlyUpdates na false.

### <a name="version-132"></a>Verze 1.3.2
- Oprava problému, který má vliv na životní cyklus opravy na uzlu, pokud jsou uzly s názvem, který je podmnožinou aktuálního názvu uzlu. V takových uzlech je možné, že se opravy neobjevily, nebo čeká na restartování.
