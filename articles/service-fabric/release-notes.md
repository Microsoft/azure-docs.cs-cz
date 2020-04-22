---
title: Azure Service Fabric verze
description: Poznámky k verzi pro Azure Service Fabric. Obsahuje informace o nejnovějších funkcích a vylepšení chod service fabric.
ms.date: 06/10/2019
ms.topic: conceptual
hide_comments: true
hideEdit: true
ms.openlocfilehash: 3e0f6c78b6e5dd066cbfbac6805bb3c42068e66a
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81729600"
---
# <a name="service-fabric-releases"></a>Service Fabric zprávy

| <a href="https://github.com/Azure/Service-Fabric-Troubleshooting-Guides" target="blank">Ukázky</a> 
| <a href="https://github.com/Azure/service-fabric-issues" target="blank">Issue Tracking</a> 
| <a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-support" target="blank">Support Options</a> 
| <a href="https://azure.microsoft.com/resources/samples/?service=service-fabric&sort=0" target="blank">kódu kódu pro</a> odstraňování problémů s<a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-versions" target="blank">průvodci</a> 
| 

Tento článek obsahuje další informace o nejnovějších verzích a aktualizacích za běhu Service Fabric a sad SDK.

## <a name="whats-new-in-service-fabric"></a>Co je nového v Service Fabric

### <a name="service-fabric-71"></a>Servisní tkanina 7,1
Vzhledem k současné krizi COVID-19 a s ohledem na výzvy, kterým čelí naši zákazníci, zpřístupňujeme 7.1, ale automaticky neupgradujeme clustery nastavené na automatické upgrady. Automatické upgrady pozastavujeme až do odvolání, abychom zajistili, že zákazníci mohou používat upgrady, pokud je to pro ně nejvhodnější, abychom předešli neočekávaným poruchám.

Budete moct aktualizovat na 7.1 prostřednictvím [portálu Azure nebo](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-upgrade-version-azure#upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-portal) prostřednictvím [nasazení Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-upgrade-version-azure#set-the-upgrade-mode-using-a-resource-manager-template).

Clustery Service Fabric s povolenými automatickými inovacemi začnou automaticky přijímat aktualizaci 7.1, jakmile obnovíme standardní postup zavádění. Před zahájením standardního zavádění na [webu Service Fabric Tech Community](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)Site poskytneme další oznámení.
Také jsme zveřejnili aktualizace na konci data podpory pro hlavní verze od 6,5 do 7,1 [zde](https://docs.microsoft.com/azure/service-fabric/service-fabric-versions#supported-versions). 

## <a name="what-is-new-in-service-fabric-71"></a>Co je nového v service fabric 7.1?
Jsme rádi, že oznamujeme další vydání Service Fabric. Tato verze je plná klíčových funkcí a vylepšení. Některé z klíčových funkcí jsou zvýrazněny níže:
## <a name="key-announcements"></a>Klíčová oznámení
- **Obecná dostupnost** [ **spravovaných identit fabric service fabric pro aplikace Service Fabric**](https://docs.microsoft.com/azure/service-fabric/concepts-managed-identity)
- [**Podpora pro Ubuntu 1804**](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-create-vnet-and-linux-cluster)
 - [**Náhled: Podpora disku Sondy SS VMS **:**](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-azure-deployment-preparation#use-ephemeral-os-disks-for-virtual-machine-scale-sets)Dočasné disky operačního systému jsou úložiště vytvořené na místním virtuálním počítači a neuložené do vzdáleného úložiště Azure. Jsou doporučeny pro všechny typy uzlů Service Fabric (primární a sekundární), protože ve srovnání s tradičními disky trvalého operačního systému, dočasné disky operačního systému:
      -  Snížení latence čtení a zápisu na disk operačního systému
      -  Povolení rychlejších operací správy uzlu resetování/opětovného zobrazení bitové kopie
      -  Snižte celkové náklady (disky jsou zdarma a nevznikají žádné další náklady na úložiště)
- Podpora pro deklaraci [**certifikátů service endpoint aplikací Service Fabric podle běžného názvu subjektu**](https://docs.microsoft.com/azure/service-fabric/service-fabric-service-manifest-resources).
- [**Podpora sond stavu pro kontejnerizované služby:**](https://docs.microsoft.com/azure/service-fabric/probes-codepackage)Podpora mechanismu sondy Liveness pro kontejnerizované aplikace. Liveness Probe pomáhají oznámit živost kontejnerizované aplikace a když nereagují včas, bude to mít za následek restartování. 
- [**Podpora balíčků kódu inicializátoru**](https://docs.microsoft.com/azure/service-fabric/initializer-codepackages) pro [kontejnery](https://review.docs.microsoft.com/azure/service-fabric/service-fabric-containers-overview) a spustitelné aplikace [hosta.](https://review.docs.microsoft.com/azure/service-fabric/service-fabric-guest-executables-introduction) To umožňuje provádění balíčků kódu (např. kontejnery), v zadaném pořadí, k provedení inicializace balíčku služby.
- **FabricObserver a ClusterObserver** jsou bezstavové aplikace, které zachycují telemetrii Service Fabric související s různými aspekty clusteru SF. Obě tyto aplikace jsou připraveny pro nasazení do produkčních clusterů systému Windows pro zachycení bohaté telemetrie s implementovanou podporou Pro ApplicationInsights, EventSource a LogAnalytics.
    - [**FabricObserver (FO) 2.0**](https://github.com/microsoft/service-fabric-observer)- běží na všech uzlech, generuje události stavu, vydává telemetrii při dosažení prahových hodnot využití prostředků nakonfigurované uživatelem. Tato verze obsahuje několik vylepšení napříč monitorováním, správou dat, podrobnostmi o událostech stavu, strukturovanou telemetrií.
     - [**ClusterObserver (CO) 1.1**](https://github.com/microsoft/service-fabric-observer/tree/master/ClusterObserver) - běží na jednom uzlu, zachycuje telemetrii stavu na úrovni clusteru. V této verzi ClusterObserver také monitoruje stav uzlu a vydává telemetrii, když je uzel vypnutý/zakázáný/zakázaný po dobu delší než časzadaný uživatelem.

### <a name="improve-application-life-cycle-experience"></a>Zlepšete si zážitek z životního cyklu aplikace

- **[Náhled:Vyrequest:](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade-advanced#avoid-connection-drops-during-planned-downtime-of-stateless-services)** Během plánované údržby služby, jako jsou upgrady služeb nebo deaktivace uzlu, chcete povolit službám řádně vyprázdnit připojení. Tato funkce přidá dobu trvání instance close delay v konfiguraci služby. Během plánovaných operací SF odebere adresu služby z zjišťování a pak čekat tuto dobu trvání před vypnutím služby.
- **[Automatická detekce a vyvažování podclusterů](https://docs.microsoft.com/azure/service-fabric/cluster-resource-manager-subclustering )**: K podclusterování dochází, když služby s různými omezeními umístění mají společnou [metriku zatížení](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-metrics). Pokud se zatížení různých sad uzlů výrazně liší, správce prostředků clusteru Service Fabric se domnívá, že cluster je nevyvážený, i když má nejlepší možnou rovnováhu z důvodu omezení umístění. V důsledku toho se pokusí vyvážit clusteru, potenciálně způsobuje zbytečné přesuny služeb (protože "nerovnováha" nelze podstatně zlepšit). Počínaje touto verzí se nyní Správce prostředků clusteru pokusí automaticky rozpoznat tyto druhy konfigurací a pochopit, kdy lze nerovnováhu opravit pohybem a kdy by místo toho měl nechat věci na pokoji, protože nelze provést žádné podstatné zlepšení.  
- [**Různé náklady na přesunutí pro sekundární repliky**](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-movement-cost): Zavedli jsme novou hodnotu nákladů přesunutí VeryHigh, která poskytuje další flexibilitu v některých scénářích definovat, pokud by měly být pro sekundární repliky použity samostatné náklady na přesunutí.
- Povolen mechanismus [**liveness probe**](https://docs.microsoft.com/azure/service-fabric/probes-codepackage ) pro kontejnerizované aplikace. Liveness Probe pomáhají oznámit živost kontejnerizované aplikace a když nereagují včas, bude to mít za následek restartování.
- [**Spustit do dokončení / jednou pro služby**](https://docs.microsoft.com/azure/service-fabric/run-to-completion)**

### <a name="image-store-improvements"></a>Vylepšení úložiště obrázků
 - Service Fabric 7.1 používá **vlastní přenos k zabezpečení přenosu souborů mezi uzly ve výchozím nastavení**. Závislost na sdílené složce SMB je odebrána z verze 7.1. Zabezpečené sdílené složky SMB stále existují na uzlech, které obsahují repliku služby Úložiště obrázků pro výběr zákazníka, aby se odhlásil z výchozího nastavení a upgrade a downgrade na starou verzi.
       
 ### <a name="reliable-collections-improvements"></a>Vylepšení spolehlivých kolekcí

- [**V paměti pouze ukládat podporu pro stavové služby pomocí spolehlivé kolekce:**](https://docs.microsoft.com/azure/service-fabric/service-fabric-work-with-reliable-collections#volatile-reliable-collections)Volatile spolehlivé kolekce umožňuje data, která mají být trvalé na disk pro odolnost proti výpadky ve velkém měřítku, lze použít pro úlohy, jako je replikované mezipaměti například kde může být tolerována občasné ztráty dat. Na základě [omezení a omezení volatile spolehlivé kolekce](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-reliable-collections-guidelines#volatile-reliable-collections), doporučujeme pro úlohy, které nepotřebují trvalost, pro služby, které zpracovávají vzácné případy ztráty kvora.
- [**Náhled: Service Fabric Backup Explorer:**](https://github.com/microsoft/service-fabric-backup-explorer)Pro usnadnění správy záloh spolehlivé kolekce pro service fabric stavové aplikace, Service Fabric Backup Explorer umožňuje uživatelům
    - Audit a kontrola obsahu spolehlivých sbírek,
    - Aktualizace aktuálního stavu na konzistentní zobrazení
    - Vytvořit zálohu aktuálnísnímek spolehlivé kolekce
    - Oprava poškození dat
                 
### <a name="service-fabric-71-releases"></a>Service Fabric 7.1 vydání
| Datum vydání | Vydat | Další informace |
|---|---|---|
| 20. dubna 2020 | [Azure Service Fabric 7.1](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-release/ba-p/1311373)  | [Zpráva k vydání verze](https://github.com/microsoft/service-fabric/tree/master/release_notes/Service-Fabric-71-releasenotes.md)|


### <a name="service-fabric-70"></a>Servisní tkanina 7,0

Azure Service Fabric 7.0 je teď k dispozici! Budete moct aktualizovat na 7.0 prostřednictvím portálu Azure nebo prostřednictvím nasazení Azure Resource Manager. Vzhledem k zpětné vazbě od zákazníků k verzím v období dovolených nezačneme automaticky aktualizovat clustery nastavené na automatické upgrady až do ledna.
V lednu budeme pokračovat v postupu standardního zavádění a clustery s povolenými automatickými upgrady začnou automaticky přijímat aktualizaci 7.0. Před zahájením zavádění poskytneme další oznámení.
Aktualizujeme také naše plánovaná data vydání, abychom uvedli, že tyto zásady bereme v úvahu. Podívejte se zde pro aktuální informace o našich budoucích [verzích plány](https://github.com/Microsoft/service-fabric/#service-fabric-release-schedule).
 
Toto je nejnovější verze Service Fabric a je naloženo s klíčovými funkcemi a vylepšeními.

### <a name="key-announcements"></a>Klíčová oznámení
 - [**Podpora keyvaultreference pro tajné kódy aplikací (Preview):**](https://docs.microsoft.com/azure/service-fabric/service-fabric-keyvault-references)Aplikace Service Fabric, které [povolily spravované identity,](https://docs.microsoft.com/azure/service-fabric/concepts-managed-identity) teď mohou přímo odkazovat na adresu URL tajného klíče trezoru klíčů jako na proměnnou prostředí, parametr aplikace nebo pověření úložiště kontejneru. Service Fabric automaticky vyřeší tajný klíč pomocí spravované identity aplikace. 
     
- **Zvýšená bezpečnost upgradu pro bezstavové služby**: Chcete-li zaručit dostupnost během upgradu aplikace, zavedli jsme nové konfigurace definovat [minimální počet instancí pro bezstavové služby,](https://docs.microsoft.com/dotnet/api/system.fabric.description.statelessservicedescription?view=azure-dotnet) které mají být považovány za k dispozici. Dříve byla tato hodnota 1 pro všechny služby a nebyla měnitelná. Pomocí této nové kontroly bezpečnosti pro vlastní služby můžete zajistit, aby vaše služby zachovaly minimální počet instancí během upgradů aplikací, upgradů clusterů a další údržby, která závisí na kontrolách stavu a bezpečnosti service fabric.
  
- [**Omezení prostředků pro uživatelské služby**](https://docs.microsoft.com/azure/service-fabric/service-fabric-resource-governance#enforcing-the-resource-limits-for-user-services): Uživatelé mohou nastavit omezení prostředků pro uživatelské služby v uzlu, aby zabránili scénářům, jako je vyčerpání prostředků systémových služeb Service Fabric. 
  
- [**Velmi vysoké náklady na přesunutí služby**](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-movement-cost) pro typ repliky. Repliky s velmi vysokými náklady na přesun budou přesunuty pouze v případě, že v clusteru dochází k porušení omezení, které nelze opravit žádným jiným způsobem. Další informace o tom, kdy je použití "velmi vysoké" náklady na přesun přiměřené, a další informace o tom, zda jsou v dokumentech k dispozici další informace.
  
-  **Další bezpečnostní kontroly clusteru**: V této verzi jsme zavedli konfigurovatelnou kontrolu bezpečnosti kvora uzly. To umožňuje přizpůsobit, kolik uzly osiva musí být k dispozici během scénáře životního cyklu clusteru a správy. Operace, které by převzaly cluster pod nakonfigurovanou hodnotou, jsou blokovány. Dnes je výchozí hodnota vždy kvorem uzly, například pokud máte 7 uzly, operace, která by vás pod 5 semenu uzly by být blokovány ve výchozím nastavení. S touto změnou můžete provést minimální bezpečnou hodnotu 6, která by umožnila pouze jeden uzel osiva být dolů najednou.
   
- Byla přidána podpora pro [**správu služby Zálohování a obnovení v aplikaci Service Fabric Explorer**](https://docs.microsoft.com/azure/service-fabric/service-fabric-backuprestoreservice-quickstart-azurecluster). Díky tomu jsou následující aktivity možné přímo z aplikace SFX: zjišťování služby zálohování a obnovení, vytváření zásad zálohování, povolení automatického zálohování, provádění záloh adhoc, spuštění operací obnovení a procházení existujících záloh.

- Oznámení [**dostupnosti ReliableCollectionsMissingTypesTool**](https://github.com/hiadusum/ReliableCollectionsMissingTypesTool): Tento nástroj pomáhá ověřit, že typy používané ve spolehlivých kolekcích jsou dopředné a zpětně kompatibilní během postupné inovace aplikace. To pomáhá zabránit selhání upgradu nebo ztrátě dat a poškození dat z důvodu chybějících nebo nekompatibilních typů.

- [**Povolit stabilní čtení na sekundární repliky**](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-configuration#configuration-names-1): Stabilní čtení omezí sekundární repliky na vrácení hodnoty, které byly kvorum-acked.

Kromě toho tato verze obsahuje další nové funkce, opravy chyb a supportability, spolehlivost a vylepšení výkonu. Úplný seznam změn naleznete v [poznámkách](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_70.md)k verzi .

### <a name="service-fabric-70-releases"></a>Service Fabric 7.0 vydání

| Datum vydání | Vydat | Další informace |
|---|---|---|
| 18. listopadu 2019 | [Azure Service Fabric 7.0](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Service-Fabric-7-0-Release/ba-p/1015482)  | [Zpráva k vydání verze](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_70.md)|
| 30. ledna 2020 | [Azure Service Fabric 7.0 aktualizace verze](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-second-refresh-release/ba-p/1137690)  | [Zpráva k vydání verze](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU2-releasenotes.md)|
| 6. února 2020 | [Azure Service Fabric 7.0 aktualizace verze](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-third-refresh-release/ba-p/1156508)  | [Zpráva k vydání verze](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU3-releasenotes.md)|
| 2. března 2020 | [Azure Service Fabric 7.0 aktualizace verze](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-fourth-refresh-release/ba-p/1205414)  | [Zpráva k vydání verze](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU4-releasenotes.md)

### <a name="service-fabric-65"></a>Servisní tkanina 6,5

Tato verze zahrnuje supportability, spolehlivost a vylepšení výkonu, nové funkce, opravy chyb a vylepšení pro usnadnění správy clusteru a životního cyklu aplikací.

> [!IMPORTANT]
> Service Fabric 6.5 je konečná verze s podporou nástrojů Service Fabric v sadě Visual Studio 2015. Zákazníkům se doporučuje přejít do [Visual Studia 2019](https://visualstudio.microsoft.com/vs/) do budoucna.

Co je nového v service fabric6.5:

- Aplikace Service Fabric Explorer obsahuje [prohlížeč úložiště obrázků](service-fabric-visualizing-your-cluster.md#image-store-viewer) pro kontrolu aplikací, které jste nahráli do úložiště obrázků.

- [Patch Orchestraation Application (POA)](service-fabric-patch-orchestration-application.md) verze [1.4.0](https://github.com/microsoft/Service-Fabric-POA/releases/tag/v1.4.0) obsahuje mnoho vylepšení samodiagnostiky. Zákazníkům SPOLEČNOSTI POA se doporučuje přejít na tuto verzi.

- [Služba EventStore je ve výchozím nastavení povolena](service-fabric-visualizing-your-cluster.md#event-store) pro clustery Service Fabric 6.5, pokud jste se neodhlásili.

- Přidány [události životního cyklu repliky](service-fabric-diagnostics-event-generation-operational.md#replica-events) pro stavové služby.

- [Lepší viditelnost stavu osiva uzlu](service-fabric-understand-and-troubleshoot-with-system-health-reports.md#seed-node-status), včetně upozornění na úrovni clusteru, pokud není uzel osiva v pořádku *(Dolů*, *Odebráno* nebo *Neznámé).*

- [Nástroj Service Fabric Application Recovery Recovery Tool](https://github.com/Microsoft/Service-Fabric-AppDRTool) umožňuje stavové služby Service Fabric rychle obnovit, když primární cluster narazí na havárii. Data z primárního clusteru jsou průběžně synchronizována v sekundární pohotovostní aplikaci pomocí pravidelného zálohování a obnovení.

- Podpora visual studia pro [publikování aplikací .NET Core do clusterů založených na Linuxu](service-fabric-how-to-publish-linux-app-vs.md).

- [Azure Service Fabric CLI (SFCTL)](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) se nainstaluje automaticky pro Service Fabric 6.5 (a novější verze) při upgradu nebo vytvoření nového clusteru Linuxu v Azure.

- [SFCTL](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) je ve výchozím nastavení nainstalován na clusterech MacOS/Linux OneBox.

Další podrobnosti naleznete v [service fabric 6.5 poznámky k verzi](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf).

### <a name="service-fabric-65-releases"></a>Service Fabric 6,5 vydání

| Datum vydání | Vydat | Další informace |
|---|---|---|
| 11. června 2019 | [Azure Service Fabric 6.5](https://blogs.msdn.microsoft.com/azureservicefabric/2019/06/11/azure-service-fabric-6-5-release/)  | [Zpráva k vydání verze](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf)|
| 2. července 2019 | [Azure Service Fabric 6.5 Aktualizace verze](https://blogs.msdn.microsoft.com/azureservicefabric/2019/07/04/azure-service-fabric-6-5-refresh-release/)  | [Zpráva k vydání verze](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU1.pdf)  |
| 29. července 2019 | [Azure Service Fabric 6.5 Aktualizace verze](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Second-Refresh-Release/ba-p/800523)  | [Zpráva k vydání verze](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU2.pdf)  |
| 23. srpna 2019 | [Azure Service Fabric 6.5 Aktualizace verze](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Third-Refresh-Release/ba-p/818599)  | [Zpráva k vydání verze](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU3.pdf)  |
| 14. října 2019 | [Azure Service Fabric 6.5 Aktualizace verze](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Fifth-Refresh-Release/ba-p/913296)  | [Poznámky k verzi] (https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU5.md  |


## <a name="previous-versions"></a>Předchozí verze

### <a name="service-fabric-64-releases"></a>Service Fabric 6.4 vydání

| Datum vydání | Vydat | Další informace |
|---|---|---|
| 30. listopadu 2018November 30, 2018 | [Azure Service Fabric 6.4](https://blogs.msdn.microsoft.com/azureservicefabric/2018/11/30/azure-service-fabric-6-4-release/)  | [Zpráva k vydání verze](https://msdnshared.blob.core.windows.net/media/2018/12/Service-Fabric-6.4-Release.pdf)|
| 12. prosince 2018December 12, 2018 | [Azure Service Fabric 6.4 Aktualizace verze pro clustery Windows](https://blogs.msdn.microsoft.com/azureservicefabric/2018/12/12/azure-service-fabric-6-4-refresh-for-windows-clusters/)  | [Zpráva k vydání verze](https://msdnshared.blob.core.windows.net/media/2018/12/Links.pdf)  |
| 4. února 2019 | [Azure Service Fabric 6.4 Aktualizace verze](https://blogs.msdn.microsoft.com/azureservicefabric/2019/02/04/azure-service-fabric-6-4-refresh-release/) | [Zpráva k vydání verze](https://msdnshared.blob.core.windows.net/media/2019/02/Service-Fabric-6.4CU3-Release-Notes.pdf) |
| 4. března 2019 | [Azure Service Fabric 6.4 Aktualizace verze](https://blogs.msdn.microsoft.com/azureservicefabric/2019/03/12/azure-service-fabric-6-4-refresh-release-2/) | [Zpráva k vydání verze](https://msdnshared.blob.core.windows.net/media/2019/03/Service-Fabric-6.4CU4-Release-Notes.pdf)
| 8. dubna 2019 | [Azure Service Fabric 6.4 Aktualizace verze](https://blogs.msdn.microsoft.com/azureservicefabric/2019/04/08/azure-service-fabric-6-4-refresh-release-5/) | [Zpráva k vydání verze](https://msdnshared.blob.core.windows.net/media/2019/04/Service-Fabric-6.4CU5-ReleaseNotes3.pdf)
| 2. května 2019 | [Azure Service Fabric 6.4 Aktualizace verze](https://blogs.msdn.microsoft.com/azureservicefabric/2019/05/02/azure-service-fabric-6-4-refresh-release-3/) | [Zpráva k vydání verze](https://msdnshared.blob.core.windows.net/media/2019/05/Service-Fabric-64CU6-Release-Notes-V2.pdf)
| 28. května 2019 | [Azure Service Fabric 6.4 Aktualizace verze](https://blogs.msdn.microsoft.com/azureservicefabric/2019/05/28/azure-service-fabric-6-4-refresh-release-4/) | [Poznámky](https://msdnshared.blob.core.windows.net/media/2019/05/Service_Fabric_64CU7_Release_Notes1.pdf)
