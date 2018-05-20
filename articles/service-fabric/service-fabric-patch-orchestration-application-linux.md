---
title: Azure Service Fabric oprava orchestration aplikace pro linux | Microsoft Docs
description: Aplikace pro automatizaci opravy operačního systému na cluster Linux Service Fabric.
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
ms.date: 1/22/2018
ms.author: nachandr
ms.openlocfilehash: f5d9b39a91567dd04b4e8ca0cd580c58024bb2f2
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="patch-the-linux-operating-system-in-your-service-fabric-cluster"></a>Oprava operačního systému Linux v clusteru Service Fabric

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
    - Koordinace úloha aktualizace operačního systému na celý cluster.
    - Ukládání výsledek dokončené operace aktualizace operačního systému.
- **Služba agenta uzlu**: Tento bezstavové služby běží na všech uzlech clusteru Service Fabric. Služba je zodpovědná za:
    - Zavádění uzlu agenta démon v systému Linux.
    - Služba démon monitorování.
- **Démon agenta uzlu**: Tento Linux démon služba běží na vyšší úrovni oprávnění (uživatel root). Naproti tomu Služba agenta uzlu a službu koordinátora spustit na nižší úrovni oprávnění. Služba je zodpovědná za provádění následujících úloh aktualizace na všech uzlech clusteru:
    - Zakázání automatické aktualizace operačního systému na uzlu.
    - Stahování a instalace aktualizací operačního systému podle zásad uživatel zadal.
    - Restartování počítače post instalace aktualizací operačního systému, v případě potřeby.
    - Výsledky aktualizacím operačního systému se nahrávají na službu Koordinátor.
    - Vytváření sestav stavu hlásí v případě, že operace se nezdařilo po jejím vyčerpání všechny opakování.

> [!NOTE]
> Oprava aplikace orchestration používá službu Service Fabric opravy správce systému pro zakázání nebo povolení uzlu a provádění kontroly stavu. Úloha opravy vytvořené aplikací orchestration oprava sleduje proces aktualizace pro každý uzel.

## <a name="prerequisites"></a>Požadavky

### <a name="ensure-that-your-azure-vms-are-running-ubuntu-1604"></a>Ujistěte se, že virtuální počítače Azure používají Ubuntu 16.04
V době psaní tohoto dokumentu Ubuntu 16.04 (`Xenial Xerus`) je jediná podporovaná verze.

### <a name="ensure-that-the-service-fabric-linux-cluster-is-version-61x-and-above"></a>Zajistěte, aby byl service fabric linux cluster verze 6.1.x a vyšší

Oprava orchestration aplikace linux používá určité funkce modulu runtime, které jsou dostupné ve verzi modulu runtime service fabric jenom 6.1.x a vyšší.

### <a name="enable-the-repair-manager-service-if-its-not-running-already"></a>Povolit službu opravy správce (Pokud již není spuštěn)

Aplikace orchestration oprava vyžaduje službu opravy správce systému povolení v clusteru.

#### <a name="azure-clusters"></a>Azure clustery

Azure linux clusterů v nástroji stříbrná a vrstva gold odolnost mít službu opravy manager ve výchozím nastavení povolené. Azure clusterů ve vrstvě bronzovým odolnost ve výchozím nastavení, není nutné službu opravy manager povolena. Pokud služba je již povolen, zobrazí se jeho spuštění v části systémové služby v Service Fabric Exploreru.

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

Samostatné Service Fabric Linux clustery nejsou podporovány v době psaní tohoto dokumentu.

### <a name="disable-automatic-os-update-on-all-nodes"></a>Zakázat automatické aktualizace operačního systému na všech uzlech

Automatické aktualizace operačního systému může způsobit ztrátu dostupnosti a nebo změna v chování spuštěné aplikace. Oprava aplikace orchestration, ve výchozím nastavení, pokusí se zakázat automatické aktualizace operačního systému na každém uzlu clusteru, aby se zabránilo takových scénářů.
Pro Ubuntu [bezobslužné upgrady](https://help.ubuntu.com/community/AutomaticSecurityUpdates) oprava orchestration aplikace jsou zakázané.

## <a name="download-the-app-package"></a>Stáhněte si balíček aplikace

Stažení aplikace z [stáhnout odkaz](https://go.microsoft.com/fwlink/?linkid=867984).

## <a name="configure-the-app"></a>Konfigurace aplikace

Chování aplikace orchestration oprava lze nakonfigurovat podle svých potřeb. Přepište výchozí hodnoty předáním v aplikaci parametru během vytváření aplikace nebo aktualizace. Parametry aplikačního lze zadat zadáním `ApplicationParameter` k `Start-ServiceFabricApplicationUpgrade` nebo `New-ServiceFabricApplication` rutiny.

|**Parametr**        |**Typ**                          | **Podrobnosti**|
|:-|-|-|
|MaxResultsToCache    |Dlouhé                              | Maximální počet výsledků aktualizace, které by měl být mezipaměti. <br>Výchozí hodnota je 3000 za předpokladu, že: <br> -Počet uzlů je 20. <br> -Počet aktualizací děje na uzlu za měsíc je pět. <br> -Počet výsledků na operace může být 10. <br> -By měly být uložené výsledky pro poslední tři měsíce. |
|TaskApprovalPolicy   |výčet <br> {NodeWise, UpgradeDomainWise}                          |TaskApprovalPolicy označuje zásady, které má být používána službu koordinátora pro instalaci aktualizace pro uzly clusteru Service Fabric.<br>                         Povolené hodnoty jsou: <br>                                                           <b>NodeWise</b>. Aktualizace jsou nainstalované jednoho uzlu současně. <br>                                                           <b>UpgradeDomainWise</b>. Aktualizace jsou nainstalované jednu upgradovací doménu najednou. (Na maximum, můžete přejít všechny uzly, které patří k doméně upgradu pro aktualizaci.)
| UpdateOperationTimeOutInMinutes | Int <br>(Výchozí: 180)                   | Určuje časový limit pro žádnou operaci aktualizace (stáhnout nebo nainstalovat). Pokud operaci nelze dokončit v rámci zadaného časového limitu, byl přerušen.       |
| RescheduleCount      | Int <br> (Výchozí: 5).                  | Maximální počet časy službu přeplánuje operačního systému aktualizovat v případě, že operace selže trvalé.          |
| RescheduleTimeInMinutes  | Int <br>(Výchozí: 30). | Interval, ve kterém služba přeplánuje operačního systému aktualizovat v případě, že chyba přetrvává. |
| UpdateFrequency           | Řetězce s hodnotami oddělenými čárkou (výchozí: "Každý týden, středu, 7:00:00")     | Četnost instalace aktualizací operačního systému na clusteru. Formát a možné hodnoty jsou: <br>-Měsíců, DD, hh: mm:, například každý měsíc, 5, 12:22:32. <br> -Každý týden, den, hh: mm:, pro příklad, týdně, úterý, 12:22:32.  <br> -Denní, hh: mm:, např. denně, 12:22:32.  <br> -Žádný označuje, že aktualizace by neměl provést.  <br><br> Všechny časy jsou ve formátu UTC.|
| UpdateClassification | Řetězce s hodnotami oddělenými čárkou (výchozí: "securityupdates") | Typ aktualizace, které by měly být nainstalovány na uzlech clusteru. Přípustné hodnoty jsou securityupdates, všechny. <br> -securityupdates - by instalovat pouze aktualizace zabezpečení <br> z výstižný – vše – by nainstalovat všechny dostupné aktualizace.|
| ApprovedPatches | Řetězce s hodnotami oddělenými čárkou (výchozí: "") | Toto je seznam schválených aktualizací, které by měly být nainstalovány na uzlech clusteru. Čárkami oddělený seznam obsahuje schválených balíčků a volitelně požadované cílovou verzi.<br> například: "výstižný utils = 1.2.10ubuntu1, python3 jwt, výstižný přenos https < 1.2.194, libsystemd0 > = 229 4ubuntu16" <br> Nainstalujte výše by <br> -výstižný utils s 1.2.10ubuntu1 verze, pokud je k dispozici v výstižný mezipaměti. Pokud tuto konkrétní verzi není k dispozici, pak je žádná operace. <br> -python3 jwt upgrade na nejnovější dostupnou verzi. Pokud balíček není přítomen, je žádná operace. <br> -upgrady výstižný přenos https na nejvyšší verzi, která je menší než 1.2.194. Pokud tato verze není zadán, je žádná operace. <br> -libsystemd0 upgrady na nejvyšší verzi, která je větší nebo rovná 229 4ubuntu16. Pokud tyto verze neexistuje, je žádná operace.|
| RejectedPatches | Řetězce s hodnotami oddělenými čárkou (výchozí: "") | Toto je seznam aktualizací, které by se neměly instalovat na uzlech clusteru <br> například: "bash, sudo" <br> Podle předchozích filtruje bash, sudo příjem všech aktualizací. |


> [!TIP]
> Pokud chcete aktualizaci operačního systému dojít okamžitě, nastavte `UpdateFrequency` podle času nasazení aplikace. Předpokládejme například, že máte testovací pěti uzly clusteru a plánování nasazení aplikace na přibližně 5:00 PM UTC. Pokud budete předpokládat, že upgradu aplikace nebo nasazení maximálně trvá 30 minut, nastavte UpdateFrequency jako "Denně, 17:30:00."

## <a name="deploy-the-app"></a>Nasazení aplikace

1. Připravte clusteru dokončení všech požadovaných předchozích kroků.
2. Nasaďte aplikaci orchestration oprava stejně jako jiná aplikace Service Fabric. Aplikaci můžete nasadit pomocí prostředí PowerShell nebo rozhraní příkazového řádku pro Azure Service Fabric. Postupujte podle kroků v [nasazení a odeberte aplikací pomocí prostředí PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications) nebo [nasazení aplikace pomocí příkazového řádku Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/scripts/cli-deploy-application)
3. Konfigurace aplikace v době nasazení, předat `ApplicationParamater` k `New-ServiceFabricApplication` zadat rutiny nebo skripty. Pro usnadnění vaší práce prostředí powershell (Deploy.ps1) a skripty bash (Deploy.sh) zajišťuje spolu s aplikací. Použití skriptu:

    - Připojení ke clusteru Service Fabric.
    - Spusťte skript nasazení. Volitelně můžete předejte parametr aplikace do skriptu. Příklad:.\Deploy.ps1 - ApplicationParameter @{UpdateFrequency = "Denně, 11:00:00"} nebo./Deploy.sh "{\"UpdateFrequency\":\"denně, 11:00:00\"}" 

> [!NOTE]
> Zachovat skript a složce aplikace PatchOrchestrationApplication ve stejném adresáři.

## <a name="upgrade-the-app"></a>Upgrade aplikace

Pokud chcete upgradovat stávající aplikace orchestration opravy, postupujte podle kroků v [upgradu aplikace Service Fabric pomocí prostředí PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade-tutorial-powershell) nebo [upgradu aplikace Service Fabric pomocí příkazového řádku Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-sfctl-application#sfctl-application-upgrade)

## <a name="remove-the-app"></a>Odeberte aplikaci

Chcete-li odebrat aplikace, postupujte podle kroků v [nasazení a odeberte aplikací pomocí prostředí PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications) nebo [odebrání aplikace pomocí příkazového řádku Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-sfctl-application#sfctl-application-delete)

Pro usnadnění vaší práce prostředí powershell (Undeploy.ps1) a skripty bash (Undeploy.sh) zajišťuje spolu s aplikací. Použití skriptu:

  - Připojení ke clusteru Service Fabric.
  - Spustit skript Undeploy.ps1 nebo Undeploy.sh

> [!NOTE]
> Zachovat skript a složce aplikace PatchOrchestrationApplication ve stejném adresáři.

## <a name="view-the-update-results"></a>Zobrazit výsledky aktualizace

Oprava aplikace orchestration zpřístupňuje rozhraní REST API zobrazit historická výsledky pro uživatele. Tady je ukázka výsledek: ```testadm@bronze000001:~$ curl -X GET http://10.0.0.5:20002/PatchOrchestrationApplication/v1/GetResults```
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

Pole JSON jsou popsány následovně:

Pole | Hodnoty | Podrobnosti
-- | -- | --
Výsledek | 0 – úspěšné<br> 1 - bylo úspěšně dokončeno s chybami<br> 2 – se nezdařilo<br> 3 - přerušeno<br> 4 - přerušena s vypršením časového limitu | Určuje výsledek celkové operace (obvykle zahrnující instalaci jedné nebo více aktualizací).
resultCode | Stejné jako výsledek | Toto pole určuje výsledek operace instalace pro individuální aktualizaci.
Typ operace | 1 - instalace<br> 0 – hledání a stahování.| Instalace je pouze typ operace, které se zobrazí ve výsledcích ve výchozím nastavení.
UpdateClassification | Výchozí hodnota je "securityupdates" | Typ aktualizace, který je nainstalován během operace aktualizace
UpdateFrequency | Výchozí hodnota je "Každý týden, středu, 7:00:00" | Aktualizujte četnosti nakonfigurovaná pro tuto aktualizaci.
RebootRequired | true – nebyla nutná restartování<br> false – nebyl požadován restart | Označuje, že dokončení instalace aktualizace vyžaduje restartování.
ApprovedList | Výchozí hodnota je "" | Seznam schválených oprav pro tuto aktualizaci
RejectedList | Výchozí hodnota je "" | Seznam odmítnutých oprav pro tuto aktualizaci

Pokud žádná aktualizace je ještě naplánováno, výsledkem JSON je prázdný.

Přihlaste se ke clusteru k aktualizaci výsledků dotazu. Poté zjistit adresu repliky pro primární službu koordinátora a stiskněte tlačítko adresu URL z prohlížeče: http://&lt;REPLIKY IP&gt;:&lt;ApplicationPort&gt;/PatchOrchestrationApplication/v1/GetResults .

Koncový bod REST pro službu koordinátora má dynamický port. Pokud chcete zkontrolovat přesnou adresu URL, najdete v Service Fabric Exploreru. Například jsou k dispozici na výsledky `http://10.0.0.7:20000/PatchOrchestrationApplication/v1/GetResults`.

![Obrázek koncový bod REST](media/service-fabric-patch-orchestration-application/Rest_Endpoint.png)

## <a name="diagnosticshealth-events"></a>Diagnostika stavu události

### <a name="diagnostic-logs"></a>Diagnostické protokoly

Oprava orchestration aplikace protokoly se shromažďují v rámci protokoly modulu runtime Service Fabric.

V případě, že chcete zaznamenat protokoly pomocí diagnostických nástrojů nebo kanálu podle svého výběru. Oprava orchestration aplikace používá následující pevné zprostředkovatele ID do protokolu událostí prostřednictvím [eventsource](https://docs.microsoft.com/dotnet/api/system.diagnostics.tracing.eventsource?view=netstandard-2.0)

- e39b723c-590c-4090-abb0-11e3e6616346
- fc0028ff-bfdc-499f-80dc-ed922c52c5e9
- 24afa313-0d3b-4c7c-b485-1047fd964b60
- 05dc046c-60e9-4ef7-965e-91660adffa68

### <a name="health-reports"></a>Sestavy o stavu

Oprava aplikace orchestration, publikuje také sestavy stavu proti službu koordinátora nebo službu Agent uzlu v následujících případech:

#### <a name="an-update-operation-failed"></a>Operace aktualizace se nezdařila

Pokud na uzlu selže operaci aktualizace, sestavy stavu se vygeneruje pro službu Agent uzlu. Podrobnosti stavu sestavy obsahují název problematické uzlu.

Po dokončení opravy je úspěšně na uzlu problematické, jsou automaticky vymazány sestavy.

#### <a name="the-node-agent-daemon-service-is-down"></a>Služba démona agenta uzlu je mimo provoz

Pokud službu démon agenta uzlu je vypnutý na uzlu, vygeneruje se sestavy stavu úroveň upozornění pro službu Agent uzlu.

#### <a name="the-repair-manager-service-is-not-enabled"></a>Služba Správce opravy není povolena.

Sestavy stavu úroveň upozornění se generuje pro službu koordinátora, pokud opravy service manager nebyl nalezen v clusteru.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

OTÁZKY. **Proč se při spuštěné aplikaci orchestration oprava vidí Moje clusteru v chybovém stavu?**

A. Během procesu instalace aplikace orchestration oprava zakáže nebo restartování uzlů. Tato operace může způsobit dočasně stav clusteru směrem dolů.

Na základě zásad pro aplikace, buď jeden uzel můžete přejděte během operace oprav *nebo* celá doména upgradu můžete současně přejděte.

Na konci instalace, jsou opětovně uzly povolena post restartování.

V následujícím příkladu clusteru byl přesunut do chybový stav dočasně vzhledem k tomu, že dva uzly byly dolů a získali došlo k porušení zásady MaxPercentageUnhealthyNodes. Chyba je dočasný, dokud probíhá oprav operaci.

![Bitové kopie není v pořádku clusteru](media/service-fabric-patch-orchestration-application/MaxPercentage_causing_unhealthy_cluster.png)

Pokud potíže potrvají, naleznete v části řešení potíží.

OTÁZKY. **Oprava aplikace orchestration je ve stavu upozornění**

A. Zkontrolujte, zda sestavy stavu odeslány vzhledem k aplikaci je hlavní příčinu. Upozornění obvykle obsahuje podrobnosti o problému. Pokud přechodný problém se předpokládá, že aplikace je automaticky obnovit z tohoto stavu.

OTÁZKY. **Co mám dělat, když můj clusteru není v pořádku a je třeba provést aktualizaci naléhavé operačního systému?**

A. Oprava aplikace orchestration neinstaluje aktualizace při clusteru není v pořádku. Odblokování pracovního postupu oprava orchestration aplikaci, přepněte cluster do stavu v pořádku.

OTÁZKY. **Proč opravy napříč clustery trvá tak dlouho spustit?**

A. Čas potřebný oprava aplikace orchestration je ve většině případů závislé na následujících faktorech:

- Zásada službu koordinátora. 
  - Výchozí zásady `NodeWise`, výsledkem opravy jenom jeden uzel v čase. Zejména v případě, že je větší, clusteru, doporučujeme použít `UpgradeDomainWise` zásadu, abyste dosáhli rychlejší opravy v clusteru.
- Počet aktualizací, které jsou k dispozici ke stažení a instalaci. 
- Průměrný čas potřebný ke stažení a instalaci aktualizace, které by neměl překročit několik hodin.
- Výkon virtuálního počítače a šířku pásma sítě.

OTÁZKY. **Jak aplikace orchestration oprava nemá rozhodne aktualizací, které jsou aktualizace zabezpečení.**

A. Oprava orchestration aplikace používá logiku distro specifické pro určení, jaké aktualizace mezi dostupné aktualizace, které jsou aktualizace zabezpečení. Příklad: V aplikaci vyhledávání aktualizací z archivy $RELEASE ubuntu – zabezpečení, $RELEASE-aktualizace ($RELEASE = xenial nebo linux standardní základní verzi). 

 
OTÁZKY. **Jak můžete uzamknout na konkrétní verzi balíčku?**

A. Pomocí nastavení ApprovedPatches zamknout vlastních balíčků na konkrétní verzi. 


OTÁZKY. **Co se stane automatické aktualizace povolena v Ubuntu?**

A. Jakmile nainstalujete opravu orchestration aplikace v clusteru, by zakázána bezobslužné upgrady v uzlu clusteru. Všechny pracovní postup pravidelnou aktualizaci by bude týkat oprava orchestration aplikace.
Pokud chcete, aby konzistence prostředí v clusteru, doporučujeme nainstalovat aktualizace prostřednictvím opravy orchestration pouze aplikace. 
 
OTÁZKY. **Po upgradu oprava orchestration aplikace provádějí čištění nepoužívané balíčků?**

A. Ano, čištění probíhá v rámci kroků po instalaci. 

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

Oprava aplikace orchestration se pokusí nainstalovat aktualizace podle přeplánování zásad. Služba se pokusí obnovit uzel a přeskočit aktualizace podle zásad aplikací.

V takovém případě se vygeneruje sestavy stavu úroveň upozornění pro službu Agent uzlu. Výsledek pro aktualizaci také obsahuje možný důvod selhání.

### <a name="the-health-of-the-cluster-goes-to-error-while-the-update-installs"></a>Stav clusteru přejde k chybě při instalaci aktualizace

Vadný aktualizace může způsobit zastavení stav aplikace nebo clusteru v konkrétním uzlu nebo doméně pro upgrade. Oprava aplikace orchestration ze operace všechny následné aktualizace, dokud clusteru je v pořádku znovu.

Správce musí zasáhnout a zjistit, proč k problému z důvodu dříve nainstalované aktualizace aplikace nebo clusteru.

## <a name="disclaimer"></a>Právní omezení

Oprava aplikace orchestration shromažďuje telemetrická data pro sledování využití a výkonu. Telemetrie aplikace odpovídá nastavení telemetrie nastavení modulu runtime Service Fabric, (který je ve výchozím).

## <a name="release-notes"></a>Poznámky k verzi

### <a name="version-010"></a>Verze 0.1.0
- Privátní preview verzi

### <a name="version-200-latest"></a>Verze 2.0.0 (nejnovější)
- Veřejné verze
