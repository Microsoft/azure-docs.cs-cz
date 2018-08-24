---
title: Použití Orchestrace opravy Azure Service Fabric pro linux | Dokumentace Microsoftu
description: Aplikace automatizace oprav operačního systému v clusteru Service Fabric s Linuxem.
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
ms.openlocfilehash: 0aadb5964b5fe08b02397588dd9b2695fb4db4ce
ms.sourcegitcommit: b5ac31eeb7c4f9be584bb0f7d55c5654b74404ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/23/2018
ms.locfileid: "42746713"
---
# <a name="patch-the-linux-operating-system-in-your-service-fabric-cluster"></a>Opravy operačního systému Linux ve vašem clusteru Service Fabric

> [!div class="op_single_selector"]
> * [Windows](service-fabric-patch-orchestration-application.md)
> * [Linux](service-fabric-patch-orchestration-application-linux.md)
>
>

Použití Orchestrace opravy je aplikace Azure Service Fabric, která automatizuje operačního systému, použití dílčích oprav v clusteru Service Fabric bez jakýchkoli prostojů.

Aplikace orchestraci oprav poskytuje následující funkce:

- **Automatická instalace operačního systému aktualizaci**. Aktualizace operačního systému se automaticky stahují a instalují. Uzly clusteru restartují podle potřeby a bez výpadků clusteru.

- **Clustery použití dílčích oprav a stavu integrace**. Při použití aktualizací aplikace orchestraci oprav monitoruje stav uzlů clusteru. Uzly clusteru jsou upgradované jeden uzel nebo jednu upgradovací doménu najednou. Pokud stav clusteru ocitne mimo provoz kvůli procesu oprav, oprav zastavena zabránit zhorší problém.

## <a name="internal-details-of-the-app"></a>Interní podrobnosti aplikace

Orchestrace aplikaci patch se skládá z následujících tyto dílčí součásti:

- **Služba Koordinátor**: Tato stavová služba je zodpovědná za:
    - Koordinace úloh aktualizace operačního systému v celém clusteru.
    - Ukládání výsledek dokončené operace aktualizace operačního systému.
- **Služba agenta uzlu**: tuto bezstavovou službu běží na všech uzlech clusteru Service Fabric. Služba je zodpovědná za:
    - Spuštění agenta uzlu démon v Linuxu.
    - Monitorování služby démona.
- **Démon agenta uzlu**: Služba démona tento Linux běží na vyšší úrovni oprávnění (uživatel root). Naproti tomu službu agenta uzlu a služba Koordinátor běží na nižší úrovni oprávnění. Služba je zodpovědná za provádí následující úlohy aktualizace se na všech uzlech clusteru:
    - Zakázat automatické aktualizace operačního systému na uzlu.
    - Stažení a instalace aktualizace operačního systému podle zásad uživatel zadal.
    - Restartování počítače po instalaci aktualizace operačního systému v případě potřeby.
    - Výsledky aktualizace operačního systému se nahrávají na službu Koordinátor.
    - Vytváření sestav stavu zprávy v případě, že operace selhala po vyčerpání všechny opakované pokusy.

> [!NOTE]
> Aplikace orchestraci oprav používá službu Service Fabric opravy správce systému zakázat nebo povolit uzlu a provádění kontroly stavu. Úloha opravy vytvořené aplikací orchestraci oprav sleduje průběh aktualizace pro každý uzel.

## <a name="prerequisites"></a>Požadavky

### <a name="ensure-that-your-azure-vms-are-running-ubuntu-1604"></a>Ujistěte se, že virtuální počítače Azure se systémem Ubuntu 16.04
V době psaní tohoto dokumentu, Ubuntu 16.04 (`Xenial Xerus`) je jedinou podporovanou verzí.

### <a name="ensure-that-the-service-fabric-linux-cluster-is-version-62x-and-above"></a>Ujistěte se, že service fabric linux cluster verze 6.2.x a vyšší

Oprava Orchestrace aplikace linux používá určité funkce modulu runtime, které jsou dostupné pouze ve verzi modulu runtime service fabric 6.2.x a vyšší.

### <a name="enable-the-repair-manager-service-if-its-not-running-already"></a>Povolit službu správce opravit (Pokud již není spuštěná)

Aplikace orchestraci oprav vyžaduje, aby na clusteru povolit služba opravy správce systému.

#### <a name="azure-clusters"></a>Clustery Azure

Azure linux clusterů v nástroji stříbrné a mají úroveň gold odolnosti servis manager ve výchozím nastavení povolená. Clustery Azure na úrovni bronzové odolnosti se ve výchozím nastavení, není nutné servis manager povolena. Pokud služba již není povolena, zobrazí se v části systému služby v Service Fabric Exploreru.

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

Samostatné Service Fabric s Linuxem clustery nejsou podporované v době psaní tohoto dokumentu.

### <a name="disable-automatic-os-update-on-all-nodes"></a>Zakázat automatické aktualizace operačního systému na všech uzlech

Automatickým aktualizacím operačního systému může vést ke ztrátě dostupnosti a nebo změnit chování spuštěné aplikace. Oprava Orchestrace aplikace, ve výchozím nastavení, bude zakázat automatické aktualizace operačního systému na každém uzlu clusteru, aby se zabránilo scénářů.
Pro Ubuntu [bezobslužného upgradu](https://help.ubuntu.com/community/AutomaticSecurityUpdates) zakázal aplikaci orchestraci oprav.

## <a name="download-the-app-package"></a>Stáhněte si balíček aplikace

Aplikace spolu s instalační skripty si můžete stáhnout z [archivu odkaz](https://go.microsoft.com/fwlink/?linkid=867984).

Aplikace ve formátu sfpkg si můžete stáhnout z [sfpkg odkaz](https://aka.ms/POA/POA_v2.0.2.sfpkg). To je užitečné, [nasazení aplikace založené na Azure Resource Manageru](service-fabric-application-arm-resource.md).

## <a name="configure-the-app"></a>Konfigurace aplikace

Chování aplikace orchestraci oprav je možné nakonfigurovat podle svých potřeb. Přepište výchozí hodnoty předáním parametru aplikace během vytváření aplikace nebo aktualizace. Lze zadat parametry aplikace tak, že zadáte `ApplicationParameter` k `Start-ServiceFabricApplicationUpgrade` nebo `New-ServiceFabricApplication` rutiny.

|**Parametr**        |**Typ**                          | **Podrobnosti**|
|:-|-|-|
|MaxResultsToCache    |Dlouhé                              | Maximální počet výsledků aktualizace, které by měly být uložené v mezipaměti. <br>Výchozí hodnota je 3000 za předpokladu, že: <br> -Počet uzlů je 20. <br> -Počet aktualizací děje na uzel a měsíc je pět. <br> -Počet výsledků na operace může být 10. <br> – Výsledky po dobu posledních tří měsíců by měla být uložena. |
|TaskApprovalPolicy   |Výčet <br> {NodeWise, UpgradeDomainWise}                          |TaskApprovalPolicy označuje zásadu, která má být použit službou koordinátora k instalaci aktualizací na uzlech clusteru Service Fabric.<br>                         Povolené hodnoty jsou: <br>                                                           <b>NodeWise</b>. Aktualizace jsou nainstalované jednoho uzlu současně. <br>                                                           <b>UpgradeDomainWise</b>. Aktualizace jsou nainstalované jednu upgradovací doménu najednou. (Na maximum, můžete přejít všechny uzly, které patří do logických sítí pro aktualizace.)
| UpdateOperationTimeOutInMinutes | Int <br>(Výchozí: 180)                   | Určuje časový limit pro libovolnou operaci aktualizace (stáhnout nebo nainstalovat). Pokud se operace nedokončí v rámci zadaného časového limitu, je přerušeno.       |
| RescheduleCount      | Int <br> (Výchozí: 5).                  | Maximální počet pokusů, operační systém přeplánuje služby aktualizovat v případě, že docházet k chybě operace.          |
| RescheduleTimeInMinutes  | Int <br>(Výchozí: 30). | Interval, ve kterém služba přeplánuje operační systém aktualizovat v případě, že chyba přetrvává. |
| UpdateFrequency           | Řetězec s hodnotami oddělenými čárkou (výchozí: "Každý týden, Středa 7:00:00")     | Frekvence pro instalaci aktualizace operačního systému v clusteru. Formát a možné hodnoty jsou: <br>– Měsíční, DD, hh: mm:, třeba každý měsíc, 5, 12:22:32. <br> – Každý týden, den, hh: mm:, například týdně, úterý, 12:22:32.  <br> -Denní, hh: mm:, třeba každý den, 12:22:32.  <br> -Žádný označuje této aktualizace by neměla být provedena.  <br><br> Všechny časy jsou ve formátu UTC.|
| UpdateClassification | Řetězec s hodnotami oddělenými čárkou (výchozí: "securityupdates") | Typ aktualizace, které musí být nainstalován na uzlech clusteru. Přípustné hodnoty jsou securityupdates, všechny. <br> -securityupdates – by instalovat pouze aktualizace zabezpečení <br> z apt - all - by instalace všech dostupných aktualizací.|
| ApprovedPatches | Řetězec s hodnotami oddělenými čárkou (výchozí: "") | Toto je seznam schválených aktualizací, které musí být nainstalován na uzly clusteru. Čárkami oddělený seznam obsahuje schválených balíčků a volitelně požadovanou cílovou verzi.<br> Příklad: "apt utils = 1.2.10ubuntu1, pythonu3 jwt, apt přenos https < 1.2.194, libsystemd0 > = 229 4ubuntu16" <br> Nainstalovat výše by <br> -apt utils s 1.2.10ubuntu1 verze, pokud je k dispozici v apt-cache. Pokud tuto konkrétní verzi není k dispozici, jde no-op. <br> -pythonu3 jwt upgrade na nejnovější dostupnou verzi. Pokud balíček není k dispozici, jde no-op. <br> – upgrady apt přenos https na nejvyšší verze, která je menší než 1.2.194. Pokud tato verze není k dispozici, jde no-op. <br> – upgrady libsystemd0 nejvyšší verzi, která je větší než nebo rovna 229 4ubuntu16. Pokud tato verze neexistuje, jde no-op.|
| RejectedPatches | Řetězec s hodnotami oddělenými čárkou (výchozí: "") | Toto je seznam aktualizací, které by se neměly instalovat na uzlech clusteru <br> Příklad: "bash, sudo" <br> Předchozí filtruje bash, sudo příjem všech aktualizací. |


> [!TIP]
> Pokud chcete aktualizaci operačního systému, která se provede hned, nastavte `UpdateFrequency` relativní vůči času nasazení aplikace. Předpokládejme například, že máte cluster s pěti uzly testu a plánujete nasazení aplikace v přibližně 17:00:00 UTC. Pokud budete předpokládat, že upgrade aplikace nebo nasazení trvá 30 minut na maximum, nastavte UpdateFrequency jako "Každý den, 17:30:00."

## <a name="deploy-the-app"></a>Nasazení aplikace

1. Připravte clusteru tak, že dokončíte všechny požadované kroky.
2. Nasazení aplikace orchestraci oprav stejně jako jakoukoli jinou aplikaci Service Fabric. Aplikaci můžete nasadit pomocí Powershellu nebo Azure Service Fabric CLI. Postupujte podle kroků v [nasazení a odeberte aplikací pomocí prostředí PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications) nebo [nasazení aplikace pomocí Azure Service Fabric CLI](https://docs.microsoft.com/azure/service-fabric/scripts/cli-deploy-application)
3. Konfigurace aplikace v době nasazování, předejte `ApplicationParamater` k `New-ServiceFabricApplication` rutiny nebo skripty k dispozici. Pro usnadnění práce prostředí powershell (Deploy.ps1) a skripty bash (Deploy.sh) jsou poskytovány spolu s aplikace. Použití skriptu:

    - Připojení ke clusteru Service Fabric.
    - Spusťte skript nasazení. Volitelně můžete předejte parametr aplikace skriptu. Příklad:.\Deploy.ps1 - ApplicationParameter @{UpdateFrequency = "Denně, 11:00:00"} nebo./Deploy.sh "{\"UpdateFrequency\":\"každý den, 11:00:00\"}" 

> [!NOTE]
> Zachovat tento skript a složka aplikace PatchOrchestrationApplication ve stejném adresáři.

## <a name="upgrade-the-app"></a>Upgrade aplikace

Pokud chcete upgradovat existující aplikace pro orchestraci oprav, postupujte podle kroků v [upgrade aplikace Service Fabric pomocí Powershellu](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade-tutorial-powershell) nebo [upgrade aplikace Service Fabric pomocí Azure Service Fabric CLI](https://docs.microsoft.com/azure/service-fabric/service-fabric-sfctl-application#sfctl-application-upgrade)

## <a name="remove-the-app"></a>Odebrání aplikace

Odebrání aplikace, postupujte podle kroků v [nasazení a odeberte aplikací pomocí prostředí PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications) nebo [odebrání aplikace pomocí Azure Service Fabric CLI](https://docs.microsoft.com/azure/service-fabric/service-fabric-sfctl-application#sfctl-application-delete)

Pro usnadnění práce prostředí powershell (Undeploy.ps1) a skripty bash (Undeploy.sh) jsou poskytovány spolu s aplikace. Použití skriptu:

  - Připojení ke clusteru Service Fabric.
  - Spustit skript Undeploy.ps1 nebo Undeploy.sh

> [!NOTE]
> Zachovat tento skript a složka aplikace PatchOrchestrationApplication ve stejném adresáři.

## <a name="view-the-update-results"></a>Zobrazení výsledků aktualizace

Aplikace orchestraci oprav zpřístupňuje rozhraní REST API k zobrazení historických výsledků pro uživatele. Tady je ukázka výsledek: ```testadm@bronze000001:~$ curl -X GET http://10.0.0.5:20002/PatchOrchestrationApplication/v1/GetResults```
```json
[ 
  { 
    "NodeName": "_bronze_0", 
    "UpdateOperationResults": [ 
      { 
        "OperationResult": "succeeded", 
        "NodeName": "_bronze_0", 
        "OperationTime": "2017-11-21T12:39:29.0435917Z", 
        "UpdateDetails": [ 
          { 
            "UpdateId": "linux-cloud-tools-azure:amd64=4.11.0.1015.15", 
            "ResultCode": "succeeded" 
          }, 
          { 
            "UpdateId": "linux-headers-azure:amd64=4.11.0.1015.15", 
            "ResultCode": "succeeded" 
          }, 
          { 
            "UpdateId": "linux-image-azure:amd64=4.11.0.1015.15", 
            "ResultCode": "succeeded" 
          }, 
          { 
            "UpdateId": "linux-tools-azure:amd64=4.11.0.1015.15", 
            "ResultCode": "succeeded" 
          }, 
          { 
            "UpdateId": "python3-apport:amd64=2.20.1-0ubuntu2.13", 
            "ResultCode": "succeeded" 
          }, 
        ], 
        "OperationType": "installation", 
        "UpdateClassification": "securityupdates", 
        "UpdateFrequency": "Daily, 7:00:00", 
        "RebootRequired": true, 
        "ApprovedList": "", 
        "RejectedList": "" 
      } 
    ] 
  } 
] 
```

Pole JSON jsou popsány následujícím způsobem:

Pole | Hodnoty | Podrobnosti
-- | -- | --
Výsledek | 0 – úspěšné<br> 1 - bylo úspěšně dokončeno s chybami<br> 2 – se nezdařilo<br> 3 - bylo přerušeno<br> 4 - bylo přerušeno s časovým limitem | Určuje výsledek operace (obvykle zahrnující instalace jedné nebo více aktualizací).
Kód výsledku | Stejný jako výsledek | Toto pole indikuje výsledek operace instalace pro individuální aktualizaci.
Typ operace | 1 – instalace<br> 0 - hledání a stahování.| Instalace je jediným typem operace OperationType, který by být standardně zobrazena ve výsledcích.
UpdateClassification | Výchozí hodnota je "securityupdates" | Typ aktualizace, který je nainstalován během operace aktualizace
UpdateFrequency | Výchozí hodnota je "Každý týden, Středa 7:00:00" | Aktualizujte četnosti nakonfigurovaná pro tuto aktualizaci.
RebootRequired | true – se vyžaduje restartování<br> false – nebyl požadován restart | Označuje, že restartování se vyžaduje pro dokončení instalace aktualizace.
ApprovedList | Výchozí hodnota je "" | Seznam schválených oprav pro tuto aktualizaci
RejectedList | Výchozí hodnota je "" | Seznam odmítnutých oprav pro tuto aktualizaci

Pokud žádná aktualizace je ještě naplánováno, výsledek JSON je prázdný.

Přihlaste se ke clusteru na aktualizace výsledků dotazu. Potom zjistit adresu repliky pro primární službu Koordinátor a stiskněte tlačítko adresu URL z prohlížeče: http://&lt;REPLIKY IP&gt;:&lt;ApplicationPort&gt;/PatchOrchestrationApplication/v1/GetResults .

Koncový bod REST pro službu Koordinátor má dynamický port. Zkontrolujte přesnou adresu URL, naleznete v Service Fabric Explorer. Například jsou k dispozici na výsledky `http://10.0.0.7:20000/PatchOrchestrationApplication/v1/GetResults`.

![Obrázek koncového bodu REST](media/service-fabric-patch-orchestration-application/Rest_Endpoint.png)

## <a name="diagnosticshealth-events"></a>Diagnostika stavu události

### <a name="diagnostic-logs"></a>Diagnostické protokoly

Protokoly aplikací orchestraci oprav se shromažďuje jako součást protokolech modulu runtime Service Fabric.

V případě, že chcete zaznamenat protokoly prostřednictvím diagnostické nástroje/kanálu podle vašeho výběru. Oprava Orchestrace aplikace používá následující oprava zprostředkovatele ID protokolovat události prostřednictvím [eventsource](https://docs.microsoft.com/dotnet/api/system.diagnostics.tracing.eventsource?view=netstandard-2.0)

- e39b723c-590c-4090-abb0-11e3e6616346
- fc0028ff-bfdc-499f-80dc-ed922c52c5e9
- 24afa313-0d3b-4c7c-b485-1047fd964b60
- 05dc046c-60e9-4ef7-965e-91660adffa68

### <a name="health-reports"></a>Sestav stavu

Aplikace orchestraci oprav, publikuje také sestav o stavu pro službu Koordinátor nebo službu agenta uzlu v následujících případech:

#### <a name="an-update-operation-failed"></a>Operace aktualizace se nezdařila

Pokud na uzlu selže operace aktualizace, vygeneruje se sestava stavu na službu agenta uzlu. Podrobnosti sestavy health obsahují název problematické uzlu.

Po dokončení opravy je úspěšně problematické uzlu, jsou automaticky vymazány sestavy.

#### <a name="the-node-agent-daemon-service-is-down"></a>Služba démona agenta uzlu je mimo provoz

Pokud služba démona agenta uzlu neběží na uzlu, generování sestavy stavu úroveň pro upozornění na službu agenta uzlu.

#### <a name="the-repair-manager-service-is-not-enabled"></a>Služba Správce opravy není povolená.

Sestava stavu úroveň upozornění je vygenerovaný pro službu Koordinátor, pokud oprava service manager nebyl nalezen v clusteru.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

Otázka: **Proč vidím cluster v chybovém stavu, když je spuštěná aplikace orchestraci oprav?**

A. Během procesu instalace aplikace orchestraci oprav zakáže nebo restartování uzlů. Tato operace může způsobit dočasně stav clusteru počítače.

Na základě zásad pro aplikace, buď jeden uzel může ujmout během operace opravy *nebo* celé doméně upgradu můžete přejít současně.

Na konci instalace jsou opětovně uzly povolena příspěvku restartování.

V následujícím příkladu clusteru přešel do stavu chyba dočasně vzhledem k tomu, že dva uzly se dolů a máte došlo k porušení zásady MaxPercentageUnhealthyNodes. Chyba je dočasný, dokud probíhá operace opravy.

![Obrázek clusteru není v pořádku](media/service-fabric-patch-orchestration-application/MaxPercentage_causing_unhealthy_cluster.png)

Pokud se problém nevyřeší, naleznete v části řešení problémů.

Otázka: **Oprava Orchestrace aplikace je ve stavu s varováním**

A. Zaškrtněte, pokud chcete zjistit, zda je sestava stavu účtováno aplikace původní příčinu. Upozornění obvykle obsahuje podrobnosti o problému. Pokud je přechodný problém, očekává se automatické obnovení z tohoto stavu aplikace.

Otázka: **Co mám dělat, když můj cluster není v pořádku a je potřeba udělat při aktualizaci urgentní operačního systému?**

A. Aplikace orchestraci oprav neinstaluje aktualizace, zatímco clusteru není v pořádku. Odblokování pracovní postup aplikace orchestraci oprav, přeneste cluster do stavu v pořádku.

Otázka: **Proč opravy napříč clustery trvá tak dlouho ke spuštění?**

A. Doba potřebná oprava Orchestrace aplikací je ve většině případů závislé na následujících faktorech:

- Zásady služby Koordinátor. 
  - Výchozí zásady `NodeWise`, výsledkem opravy jenom jeden uzel v čase. Zejména v případě, že dojde větší cluster, doporučujeme použít `UpgradeDomainWise` zásady k dosažení rychlejší opravy clusteru.
- Počet, které jsou k dispozici ke stažení a instalaci aktualizace. 
- Průměrný čas potřebný ke stažení a instalaci aktualizace, které by neměl překročit několik hodin.
- Výkon virtuálním počítači a šířku pásma sítě.

Otázka: **Jak nemá opravu Orchestrace aplikace rozhodne, jaké aktualizace jsou aktualizace zabezpečení.**

A. Oprava Orchestrace aplikace používá konkrétní distribuce logiku pro určení, jaké aktualizace mezi dostupných aktualizací jsou aktualizace zabezpečení. Příklad: V ubuntu aplikace vyhledá aktualizace archivy $RELEASE – zabezpečení, $RELEASE – aktualizace ($RELEASE = xenial nebo standardní základní prodejní verzi systému linux). 

 
Otázka: **Jak lze uzamknout ke konkrétní verzi balíčku?**

A. Použití nastavení ApprovedPatches k uzamčení balíčků na konkrétní verzi. 


Otázka: **Co se stane v Ubuntu povolené automatické aktualizace?**

A. Poté, co nainstalujete aplikaci orchestraci oprav v clusteru, by zakázané bezobslužného upgradu uzlu clusteru. Všechny pravidelnou aktualizaci pracovního postupu by řízené aplikace pro orchestraci oprav.
Pokud chcete, aby konzistence prostředí clusteru, doporučujeme, abyste instalaci aktualizace prostřednictvím opravy Orchestrace pouze aplikace. 
 
Otázka: **Po upgradu opravu Orchestrace aplikace proveďte vyčištění nepoužívaných balíčků?**

A. Ano, čištění probíhá v rámci kroků po instalaci. 

Otázka: **Je možné aplikaci orchestraci oprav o opravu clusteru dev (clusteru s jedním uzlem)?**

A. Ne, aplikace orchestraci oprav nelze použít na clusteru s jedním uzlem opravy. Toto omezení je záměrné, jako [service fabric systémové služby](https://docs.microsoft.com/azure/service-fabric/service-fabric-technical-overview#system-services) nebo všech zákaznických aplikací bude čelí výpadkům a proto všechny opravy úlohy pro opravy chyb by nikdy získáte schválené nástroj pro správu oprav.

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

Aplikace orchestraci oprav, pokusí se nainstalovat aktualizace podle přeplánování zásad. Služba se pokusí obnovit uzel a přeskočit aktualizaci souladu se zásadami pro aplikace.

V takovém případě se na službu agenta uzlu generování sestavy stavu úroveň pro upozornění. Výsledek pro aktualizaci také obsahuje možný důvod selhání.

### <a name="the-health-of-the-cluster-goes-to-error-while-the-update-installs"></a>Stav clusteru přejde na chybu při instalaci aktualizace

Chybný aktualizace může způsobit zastavení stav aplikace nebo na konkrétní uzel nebo doména upgradu clusteru. Aplikace orchestraci oprav navrátí jakékoli následné aktualizace operace, dokud clusteru je opět v pořádku.

Správce musíte zasáhnout a zjistit, proč k problému z důvodu dříve nainstalovaných aktualizací aplikace nebo clusteru.

## <a name="disclaimer"></a>Právní omezení

Aplikace orchestraci oprav shromažďuje telemetrii ke sledování využití a výkonu. Telemetrie vaší aplikace se řídí nastavení telemetrie nastavení modulu runtime Service Fabric, (která je ve výchozím).

## <a name="release-notes"></a>Poznámky k verzi

### <a name="version-010"></a>Verze 0.1.0
- Ve verzi Private preview verze

### <a name="version-200"></a>Verze 2.0.0
- Veřejné vydané verze

### <a name="version-201"></a>Verze 2.0.1
- Znovu zkompilovat aplikaci pomocí nejnovější sady SDK Service Fabric

### <a name="version-202-latest"></a>Verze bodu 2.0.2 (nejnovější)
- Opravili jsme problém s upozorněním stavu získávání zachovají během restartování.
