---
title: Verze Azure Service Fabric
description: Poznámky k verzi pro Azure Service Fabric. Obsahuje informace o nejnovějších funkcích a vylepšeních v Service Fabric.
ms.date: 06/10/2019
ms.topic: conceptual
hide_comments: true
hideEdit: true
ms.openlocfilehash: 38ec7949b5fc04852568e9e69f35f212b1edee5d
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2021
ms.locfileid: "103201034"
---
# <a name="service-fabric-releases"></a>Verze Service Fabric

- <a href="https://github.com/Azure/Service-Fabric-Troubleshooting-Guides" target="blank">Průvodci odstraňováním potíží</a> 
- <a href="https://github.com/Azure/service-fabric-issues" target="blank">Sledování problémů</a> 
- <a href="/azure/service-fabric/service-fabric-support" target="blank">Možnosti podpory</a> 
- <a href="/azure/service-fabric/service-fabric-versions" target="blank">Podporované verze</a> 
- <a href="https://azure.microsoft.com/resources/samples/?service=service-fabric&sort=0" target="blank">Vzorky kódu</a>

V tomto článku najdete další informace o nejnovějších vydáních a aktualizacích Service Fabric runtime a sadách SDK.

## <a name="service-fabric-72"></a>Service Fabric 7,2

S radostí oznamujeme, že vydání služby Service Fabric runtime od verze 7,2 bylo zahájeno v různých oblastech Azure spolu s aktualizacemi nástrojů a sad SDK. Aktualizace pro .NET SDK, Java SDK a Service Fabric runtime jsou k dispozici prostřednictvím instalačního programu webové platformy, balíčků NuGet a úložišť Maven.

### <a name="key-announcements"></a>Oznámení klíčů

- **Verze Preview**: [**Service Fabric spravované clustery**](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-managed-clusters-are-now-in-public-preview/ba-p/1721572) jsou teď ve verzi Public Preview. Service Fabric spravovaných clusterů se snaží zjednodušit nasazování a správu clusteru zapouzdřením základních prostředků, které tvoří Service Fabric cluster do jednoho prostředku ARM. Další podrobnosti najdete v tématu [přehled Service Fabric spravovaného clusteru](./overview-managed-cluster.md).
- **Preview**: [**Podpora bezstavových služeb s větším počtem instancí, než je počet uzlů**](./service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md) , je teď ve verzi Public Preview. Zásady umístění umožňují vytvoření více bezstavových instancí oddílu na uzlu.
- [**FabricObserver (FO) 3,0**](https://aka.ms/sf/fabricobserver) je nyní k dispozici.
    - Nyní můžete spustit FabricObserver v clusterech se systémy Linux a Windows.
    - Nyní můžete vytvářet vlastní moduly plug-in pro pozorovatele. Podrobnosti a kód najdete v [souboru Readme modulů plug](https://github.com/microsoft/service-fabric-observer/blob/master/Documentation/Plugins.md) -in a v [projektu ukázkového modulu plug-in](https://github.com/microsoft/service-fabric-observer/tree/master/SampleObserverPlugin) .
    - Nyní můžete změnit jakékoli nastavení pozorovatele prostřednictvím upgradu parametrů aplikace. To znamená, že už nebudete muset znovu nasazovat a upravovat konkrétní nastavení pozorovatele. Podívejte se prosím na [ukázku](https://github.com/microsoft/service-fabric-observer/blob/master/Documentation/Using.md#parameterUpdates).
- [**Podpora imagí kontejnerů Ubuntu 18,04 OneBox**](https://hub.docker.com/_/microsoft-service-fabric-onebox).
- **Preview**: [ **referenční informace o trezoru klíčů pro aplikace Service Fabric podporují **jenom tajné klíče se správou verzí**. Tajné kódy bez verzí nejsou podporovány.**](./service-fabric-keyvault-references.md)
- SF SDK vyžaduje nejnovější VS 2019 Update 16.7.6 nebo 16,8 Preview 4, aby bylo možné vytvořit .NET Framework nové projekty nestavové/stavové/aktéry. Pokud nemáte nejnovější aktualizaci VS Update, po vytvoření projektu služby použijte Správce balíčků k instalaci Microsoft. ServiceFabric. Services (verze 4.2. x) pro stavové a bezstavové projekty a Microsoft. ServiceFabric. Actors (verze 4.2. x) pro projekty actor z nuget.org.
- **RunToCompletion**: Service Fabric podporuje koncept běhu do dokončování pro spustitelné soubory typu Host. V případě této aktualizace, jakmile se replika spustí, se uvolní prostředky clusteru přidělené této replice.
- [**Podpora zásad správného řízení prostředků je vylepšená**](./service-fabric-resource-governance.md): povolení požadavků a omezení pro prostředky procesoru a paměti.

### <a name="service-fabric-72-releases"></a>Verze Service Fabric 7,2
| Datum vydání | Vydat | Další informace |
|---|---|---|
| 21. října 2020 | [Azure Service Fabric 7,2](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-2-release/ba-p/1805653)  | [Zpráva k vydání verze](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-72-releasenotes.md)|
| 9. listopadu 2020 | [Verze Azure Service Fabric 7,2 druhé aktualizace](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-2-second-refresh-release/ba-p/1874738) | [Zpráva k vydání verze](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-72CU2-releasenotes.md) |
| 10. listopadu 2020  | Verze třetí aktualizace pro Azure Service Fabric 7,2 | [Zpráva k vydání verze](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-72CU3-releasenotes.md) |
| 2. prosince 2020 | [Verze čtvrté aktualizace pro Azure Service Fabric 7,2](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-2-fourth-refresh-release/ba-p/1950584) | [Zpráva k vydání verze](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-72CU4.md)
| 25. ledna 2021 | [Vydání páté aktualizace pro Azure Service Fabric 7,2](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-2-fifth-refresh-release/ba-p/2096575) | [Zpráva k vydání verze](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-72CU5-ReleaseNotes.md)
| 17. února 2021 | [Verze Azure Service Fabric 7,2 šesté aktualizace](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-sixth-refresh-release/ba-p/2144685) | [Zpráva k vydání verze](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-72CU6-ReleaseNotes.md)
| 10. března 2021 | [Verze Azure Service Fabric 7,2 sedmé aktualizace](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-seventh-refresh-release/ba-p/2201100) | [Zpráva k vydání verze](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-72CU7-releasenotes.md)

## <a name="previous-versions"></a>Předchozí verze

### <a name="service-fabric-71"></a>Service Fabric 7,1

Z důvodu aktuální COVID krize a zvážení výzev, které čelí naši zákazníci, provedeme 7,1 k dispozici, ale nebude automaticky upgradovat clustery nastavené tak, aby přijímaly automatické upgrady. Automatické upgrady se pozastavuje až do dalšího upozornění, aby zákazníci mohli použít upgrady, které jsou pro ně nejvhodnější, aby se předešlo neočekávaným výpadkům.

Pomocí [Azure Portal](./service-fabric-cluster-upgrade-version-azure.md#upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-portal) nebo prostřednictvím [Azure Resource Manager nasazení](./service-fabric-cluster-upgrade-version-azure.md#set-the-upgrade-mode-using-a-resource-manager-template)budete moct aktualizovat na 7,1.

Service Fabric clusterů s povolenými automatickými upgrady začnou aktualizace 7,1 automaticky dostávat po obnovení standardního postupu zavedení. Před zahájením standardního zavedení na [webu Service Fabric odborné komunity](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)budeme poskytovat další oznámení.
Také jsme publikovali aktualizace na konci data podpory pro hlavní verze od 6,5 [do 7,1.](./service-fabric-versions.md#supported-versions) 

#### <a name="key-announcements"></a>Oznámení klíčů

- **Obecná dostupnost** [ **Service Fabric spravovaných identit pro Service Fabric aplikace**](./concepts-managed-identity.md)
- [**Podpora pro Ubuntu 18,04**](./service-fabric-tutorial-create-vnet-and-linux-cluster.md)
 - [**Verze Preview: podpora virtuálních počítačů s dočasným operačním systémem pro škálování virtuálního počítače**](./service-fabric-cluster-azure-deployment-preparation.md#use-ephemeral-os-disks-for-virtual-machine-scale-sets)* *: dočasné disky s operačním systémem se vytvářejí na místním virtuálním počítači a neukládají se do vzdáleného Azure Storage. Doporučuje se pro všechny Service Fabric typy uzlů (primární a sekundární), protože jsou v porovnání s tradičními trvalými disky s operačním systémem a s dočasnými disky operačního systému:
      -  Snížit latenci čtení/zápisu na disk s operačním systémem
      -  Povolení rychlejšího resetování nebo opětovného nastavování operací správy uzlů v imagi
      -  Snižte celkové náklady (disky jsou bezplatné a neúčtují se žádné další náklady na úložiště).
- Podpora pro deklaraci [**certifikátů koncového bodu služby Service Fabric aplikací podle běžného názvu subjektu**](./service-fabric-service-manifest-resources.md)
- [**Podpora sond stavu pro kontejnerové služby**](./probes-codepackage.md): podpora mechanismu testování živých aplikací pro aplikace s podporou kontejnerů. Funkce sonda živého testu oznamuje živý kontejnerové aplikace a pokud neodpoví včas, bude mít za následek restartování. 
- [**Podpora pro balíčky inicializačních kódů**](./initializer-codepackages.md) pro [kontejnery](/azure/service-fabric/service-fabric-containers-overview) a [spustitelné aplikace hosta](/azure/service-fabric/service-fabric-guest-executables-introduction) . To umožňuje spouštět balíčky kódu (např. kontejnery) v zadaném pořadí k provedení inicializace balíčku služby.
- **FabricObserver a ClusterObserver** jsou bezstavové aplikace, které zachycují Service Fabric telemetrie související s různými aspekty v clusteru SF. Obě tyto aplikace jsou připravené pro nasazení do produkčních clusterů systému Windows za účelem zachycení bohatých telemetrie s implementovanou podporou pro ApplicationInsights, EventSource a LogAnalytics.
    - [**FabricObserver (FO) 2,0**](https://github.com/microsoft/service-fabric-observer)– běží na všech uzlech, vygeneruje události stavu, vygeneruje telemetrii, když se dosáhne uživatelem konfigurovaných prahových hodnot využití prostředků. Tato verze obsahuje několik vylepšení v rámci monitorování, správy dat, podrobností události stavu a strukturované telemetrie.
     - [**ClusterObserver (co) 1,1**](https://github.com/microsoft/service-fabric-observer/tree/master/ClusterObserver) – spouští se na jednom uzlu, zachycuje telemetrii stavu na úrovni clusteru. V této verzi ClusterObserver také monitoruje stav uzlu a generuje telemetrii, pokud je uzel vypnutý/zakázán/zakázán po dobu delší než uživatelem zadané časové období.

#### <a name="improve-application-life-cycle-experience"></a>Zlepšení prostředí pro životní cyklus aplikací

- **[Verze Preview: vyprázdnit požadavek](./service-fabric-application-upgrade-advanced.md#avoid-connection-drops-during-stateless-service-planned-downtime)**: během plánované údržby služby, jako jsou například upgrady služby nebo deaktivace uzlu, byste chtěli dovolit službám bezproblémové vyprazdňování připojení. Tato funkce přidá dobu zpoždění ukončení instance v konfiguraci služby. Během plánovaných operací SF odstraní adresu služby ze zjišťování a pak před vypnutím služby počká tuto dobu.
- **[Automatické zjišťování a vyrovnávání podclusterů](./cluster-resource-manager-subclustering.md)**: k subclusterování dochází, když služby s různým omezením umístění mají společnou [metriku zatížení](./service-fabric-cluster-resource-manager-metrics.md). Pokud se zatížení různých sad uzlů výrazně liší, Cluster Service Fabric Správce prostředků se domnívá, že cluster je nevyvážený, a to i v případě, že má nejlepší možný zůstatek z důvodu omezení umístění. V důsledku toho se pokusí cluster znovu vyvážit, což může způsobit zbytečné přesuny služeb (vzhledem k tomu, že nerovnováha není možné podstatně zlepšit). Od této verze se Správce prostředků clusteru pokusí automaticky detekovat tyto typy konfigurací a porozumět tomu, kdy se nerovnováha dá opravit prostřednictvím přesunu, a když místo toho by měla opustit pouze takové věci, protože není možné provést žádné podstatné zlepšení.  
- [**Různé náklady na přesun pro sekundární repliky**](./service-fabric-cluster-resource-manager-movement-cost.md): zavedli jsme novou hodnotu pro přesunutí VeryHigh, která v některých scénářích poskytuje větší flexibilitu, která definuje, jestli se mají pro sekundární repliky použít samostatné náklady na přesun.
- Pro kontejnerové aplikace je povolen mechanismus [**živého testování**](./probes-codepackage.md) . Funkce sonda živého testu oznamuje živý kontejnerové aplikace a pokud neodpoví včas, bude mít za následek restartování.
- [**Spustit pro služby pro doplňování**](./run-to-completion.md)**

#### <a name="image-store-improvements"></a>Vylepšení Image Store
 - Service Fabric 7,1 používá **vlastní přenos k zabezpečení přenosu souborů mezi uzly ve výchozím nastavení**. Závislost na sdílené složce SMB je odebrána z verze 7,1. Zabezpečené sdílené složky protokolu SMB stále existují na uzlech, které obsahují Image Store replice služby pro výběr zákazníka, aby se odhlásily od výchozího nastavení a aby je bylo možné upgradovat a downgradovat na starou verzi.
       
 #### <a name="reliable-collections-improvements"></a>Vylepšení spolehlivých kolekcí

- [**Pouze v úložišti jenom pro stavové služby, které používají spolehlivé kolekce**](./service-fabric-work-with-reliable-collections.md#volatile-reliable-collections): volatile Reliable Collections umožňuje uchovat data na disk a zajistit tak odolnost proti výpadkům ve velkém rozsahu, dá se použít pro úlohy jako replikovanou mezipaměť, například v případě, kdy je možné tolerovat příležitostné ztráty dat. V závislosti na [omezeních a omezeních stálých spolehlivých kolekcí](./service-fabric-reliable-services-reliable-collections-guidelines.md#volatile-reliable-collections)doporučujeme tuto úlohu pro úlohy, které nepotřebují persistenci, pro služby, které zpracovávají vzácná výpadky při ztrátě kvora.
- [**Verze Preview: Service Fabric Průzkumník služby Backup**](https://github.com/microsoft/service-fabric-backup-explorer): pro snadnější správu zálohování spolehlivých kolekcí pro Service Fabric stavové aplikace Service Fabric Průzkumník zálohování umožňuje uživatelům
    - Audit a kontrola obsahu spolehlivých kolekcí,
    - Aktualizuje aktuální stav na konzistentní zobrazení.
    - Vytvoří zálohu aktuálního snímku spolehlivých kolekcí.
    - Opravit poškození dat
                 
#### <a name="service-fabric-71-releases"></a>Verze Service Fabric 7,1
| Datum vydání | Vydat | Další informace |
|---|---|---|
| 20. dubna 2020 | [Azure Service Fabric 7,1](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-release/ba-p/1311373)  | [Zpráva k vydání verze](https://github.com/microsoft/service-fabric/tree/master/release_notes/Service-Fabric-71-releasenotes.md)|
| 16. června 2020 | [Microsoft Azure Service Fabric 7,1 nejdřív aktualizovat](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-first-refresh-release/ba-p/1466517) | [Zpráva k vydání verze](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU1-releasenotes.md)
| 20. července 2020 | [Microsoft Azure Service Fabric druhá aktualizace 7,1](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-second-refresh-release/ba-p/1534246) | [Zpráva k vydání verze](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU2-releasenotes.md)
| 12. srpna 2020 | [Třetí aktualizace Microsoft Azure Service Fabric 7,1](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-third-refresh-release/ba-p/1587586) | [Zpráva k vydání verze](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU3-releasenotes.md)
| 10. září 2020 | [Čtvrtá aktualizace Microsoft Azure Service Fabric 7,1](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-fourth-refresh-release/ba-p/1653859)  | [Zpráva k vydání verze](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU5-releasenotes.md)|
| 7. října 2020 | Microsoft Azure Service Fabric 7,1 šesté aktualizace | [Zpráva k vydání verze](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU6-releasenotes.md)|
| 23. listopadu 2020 | Microsoft Azure Service Fabric 7,1 osmá aktualizace | [Zpráva k vydání verze](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU8-releasenotes.md)|


### <a name="service-fabric-70"></a>Service Fabric 7,0

Azure Service Fabric 7,0 je teď k dispozici! Pomocí Azure Portal nebo prostřednictvím Azure Resource Manager nasazení budete moct aktualizovat na 7,0. V důsledku zpětné vazby od zákazníků k vydaným obdobím po dobu svátků nezačneme automaticky aktualizovat clustery nastavené na příjem automatických upgradů do ledna.
V lednu budeme pokračovat standardním postupem a clustery s povolenými automatickými upgrady, začnou se aktualizace 7,0 automaticky dostávat. Před začátkem zavedení budeme poskytovat další oznámení.
Také aktualizujeme naše datum plánované verze, abychom zjistili, že tyto zásady máme v potaz. V budoucích [plánech](https://github.com/Microsoft/service-fabric/#service-fabric-release-schedule)vydaných verzí najdete informace o aktualizacích.

#### <a name="key-announcements"></a>Oznámení klíčů
 - [**Podpora KeyVaultReference pro aplikační tajné klíče (Preview)**](./service-fabric-keyvault-references.md): Service Fabric aplikace, které mají povolené [spravované identity](./concepts-managed-identity.md) , teď můžou přímo odkazovat na adresu URL Key Vault tajných klíčů jako na proměnnou prostředí, parametr aplikace nebo přihlašovací údaje úložiště kontejnerů. Service Fabric bude tajný klíč automaticky přeložit pomocí spravované identity aplikace. 
     
- **Vylepšená bezpečnost upgradu pro bezstavové služby**: Pokud chcete zaručit dostupnost během upgradu aplikace, zavedli jsme nové konfigurace, abychom definovali [minimální počet instancí bezstavových služeb](/dotnet/api/system.fabric.description.statelessservicedescription) , které se budou považovat za dostupné. Dřív byla tato hodnota 1 pro všechny služby a nedá se změnit. Díky této nové kontrole bezpečnosti pro jednotlivé služby můžete zajistit, aby vaše služby během upgradování aplikace, upgrady clusterů a jiné údržby zachovaly minimální počet instancí, a další údržbu, která spoléhá na kontrolu stavu a bezpečnosti Service Fabric.
  
- [**Omezení prostředků pro uživatelské služby**](./service-fabric-resource-governance.md#enforcing-the-resource-limits-for-user-services): uživatelé můžou nastavit omezení prostředků pro uživatelské služby na uzlu, aby se předešlo scénářům, jako je vyčerpání prostředků Service Fabric systémových služeb. 
  
- [**Velmi vysoké náklady na přesun služeb**](./service-fabric-cluster-resource-manager-movement-cost.md) pro typ repliky. Repliky s velmi vysokými náklady na přesun budou přesunuty pouze v případě porušení omezení v clusteru, které nelze opravit jiným způsobem. Další informace o tom, kdy je využití "velmi vysoké" nákladů na přesunutí přijatelné a další okolnosti, najdete v odkazovaném dokumentu.
  
-  **Další bezpečnostní kontroly clusteru**: v této verzi jsme představili konfigurovatelnou kontrolu bezpečnosti kvora uzlů pro počáteční uzly. To vám umožní přizpůsobit, kolik počátečních uzlů musí být k dispozici během životního cyklu clusteru a scénářů správy. Operace, které by mohly převzít cluster pod konfigurovanou hodnotou, jsou zablokované. Dnes výchozí hodnota je vždy kvorum počátečních uzlů, například pokud máte 7 počátečních uzlů, operace, která by poznamenala méně než 5 počátečních uzlů, bude ve výchozím nastavení blokována. V důsledku této změny byste mohli nastavit minimální bezpečnou hodnotu 6, která by v jednom okamžiku umožňovala snížit pouze jeden uzel počáteční hodnoty.
   
- Přidání podpory pro [**správu služby zálohování a obnovení v Service Fabric Explorer**](./service-fabric-backuprestoreservice-quickstart-azurecluster.md). Díky tomu jsou možné následující aktivity přímo z SFX: zjišťování služby zálohování a obnovení, vytváření zásad zálohování, povolení automatického zálohování, provádění záloh ad hoc, spouštění operací obnovení a procházení stávajících záloh.

- Oznamujeme dostupnost [**ReliableCollectionsMissingTypesTool**](https://github.com/hiadusum/ReliableCollectionsMissingTypesTool): Tento nástroj pomáhá ověřit, že typy používané ve spolehlivých kolekcích jsou během upgradu postupného použití předávány a zpětně kompatibilní. To pomáhá zabránit selháním upgradu nebo ztrátě dat a poškození dat z důvodu chybějících nebo nekompatibilních typů.

- [**Povolit stabilní čtení u sekundárních replik**](./service-fabric-reliable-services-configuration.md#configuration-names-1): stabilní čtení omezí sekundární repliky tak, aby vracely hodnoty, které byly kvorum-potvrzeno.

Kromě toho tato verze obsahuje další nové funkce, opravy chyb a vylepšení podpory, spolehlivosti a výkonu. Úplný seznam změn najdete v [poznámkách k verzi](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_70.md).

#### <a name="service-fabric-70-releases"></a>Verze Service Fabric 7,0

| Datum vydání | Vydat | Další informace |
|---|---|---|
| 18. listopadu 2019 | [Azure Service Fabric 7,0](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Service-Fabric-7-0-Release/ba-p/1015482)  | [Zpráva k vydání verze](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_70.md)|
| 30. ledna 2020 | [Vydání aktualizace pro Azure Service Fabric 7,0](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-second-refresh-release/ba-p/1137690)  | [Zpráva k vydání verze](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU2-releasenotes.md)|
| 6. února 2020 | [Vydání aktualizace pro Azure Service Fabric 7,0](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-third-refresh-release/ba-p/1156508)  | [Zpráva k vydání verze](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU3-releasenotes.md)|
| 2. března 2020 | [Vydání aktualizace pro Azure Service Fabric 7,0](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-fourth-refresh-release/ba-p/1205414)  | [Zpráva k vydání verze](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU4-releasenotes.md)
| 6. května 2020 | [Verze Azure Service Fabric 7,0 šesté aktualizace](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-sixth-refresh-release/ba-p/1365709) | [Zpráva k vydání verze](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU6-releasenotes.md)|
| 9. října 2020 | Vydání Azure Service Fabric 7,0 deváté aktualizace | [Zpráva k vydání verze](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU9-releasenotes.md)|

### <a name="service-fabric-65"></a>Service Fabric 6,5

Tato verze zahrnuje podporu, spolehlivost a vylepšení výkonu, nové funkce, opravy chyb a vylepšení, které usnadňují správu životního cyklu clusteru a aplikací.

> [!IMPORTANT]
> Service Fabric 6,5 je konečná verze s podporou nástrojů Service Fabric Tools v aplikaci Visual Studio 2015. Zákazníkům se doporučuje přesunout se do sady [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) až dál.

Co je nového v Service Fabric 6,5:

- Service Fabric Explorer obsahuje [Image Store Viewer](service-fabric-visualizing-your-cluster.md#image-store-viewer) pro kontrolu aplikací, které jste nahráli do úložiště imagí.

- [Aplikace orchestrace opravy (Poa)](service-fabric-patch-orchestration-application.md) verze [1.4.0](https://github.com/microsoft/Service-Fabric-POA/releases/tag/v1.4.0) obsahuje mnoho vylepšení s automatickým diagnostikou. Zákazníkům rodu POA se doporučuje přejít na tuto verzi.

- [Služba eventstoru je ve výchozím nastavení povolená](service-fabric-visualizing-your-cluster.md#event-store) pro clustery Service Fabric 6,5, pokud jste se nerozhodli.

- Byly přidány [události životního cyklu repliky](service-fabric-diagnostics-event-generation-operational.md#replica-events) pro stavové služby.

- [Lepší viditelnost stavu počátečního uzlu](service-fabric-understand-and-troubleshoot-with-system-health-reports.md#seed-node-status), včetně upozornění na úrovni clusteru, pokud je počáteční uzel v pořádku (*dolů*, *odebrán* nebo *Neznámý*).

- [Service Fabric Nástroj pro zotavení po havárii](https://github.com/Microsoft/Service-Fabric-AppDRTool) umožňuje Service Fabric stavové služby rychle obnovit v případě, že dojde k havárii primárního clusteru. Data z primárního clusteru se průběžně synchronizují v sekundárním úsporném režimu pomocí pravidelného zálohování a obnovování.

- Podpora sady Visual Studio pro [publikování aplikací .NET Core do clusterů se systémem Linux](service-fabric-how-to-publish-linux-app-vs.md).

- [Azure Service Fabric CLI (SFCTL)](./service-fabric-cli.md) se nainstaluje automaticky pro Service Fabric 6,5 (a novější verze) při upgradu nebo vytvoření nového clusteru se systémem Linux v Azure.

- [SFCTL](./service-fabric-cli.md) se ve výchozím nastavení instaluje v clusterech OneBox MacOS/Linux.

Další podrobnosti najdete v [poznámkách k verzi pro Service Fabric 6,5](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf).

#### <a name="service-fabric-65-releases"></a>Verze Service Fabric 6,5

| Datum vydání | Vydat | Další informace |
|---|---|---|
| 11. června 2019 | [Azure Service Fabric 6,5](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)  | [Zpráva k vydání verze](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf)|
| 2\. července 2019 | [Vydání aktualizace pro Azure Service Fabric 6,5](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)  | [Zpráva k vydání verze](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU1.pdf)  |
| 29. července 2019 | [Vydání aktualizace pro Azure Service Fabric 6,5](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Second-Refresh-Release/ba-p/800523)  | [Zpráva k vydání verze](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU2.pdf)  |
| Srpna 23, 2019 | [Vydání aktualizace pro Azure Service Fabric 6,5](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Third-Refresh-Release/ba-p/818599)  | [Zpráva k vydání verze](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU3.pdf)  |
| 14. října 2019 | [Vydání aktualizace pro Azure Service Fabric 6,5](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Fifth-Refresh-Release/ba-p/913296)  | [Poznámky k verzi] (https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU5.md  |


### <a name="service-fabric-64-releases"></a>Verze Service Fabric 6,4

| Datum vydání | Vydat | Další informace |
|---|---|---|
| 30. listopadu 2018 | [Azure Service Fabric 6,4](https://blogs.msdn.microsoft.com/azureservicefabric/2018/11/30/azure-service-fabric-6-4-release/)  | [Zpráva k vydání verze](https://msdnshared.blob.core.windows.net/media/2018/12/Service-Fabric-6.4-Release.pdf)|
| 12. prosince 2018 | [Azure Service Fabric 6,4 aktualizace pro clustery Windows ve verzi](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)  | [Zpráva k vydání verze](https://msdnshared.blob.core.windows.net/media/2018/12/Links.pdf)  |
| 4. února 2019 | [Vydání aktualizace pro Azure Service Fabric 6,4](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) | [Zpráva k vydání verze](https://msdnshared.blob.core.windows.net/media/2019/02/Service-Fabric-6.4CU3-Release-Notes.pdf) |
| 4. března 2019 | [Vydání aktualizace pro Azure Service Fabric 6,4](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) | [Zpráva k vydání verze](https://msdnshared.blob.core.windows.net/media/2019/03/Service-Fabric-6.4CU4-Release-Notes.pdf)
| 8. dubna 2019 | [Vydání aktualizace pro Azure Service Fabric 6,4](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) | [Zpráva k vydání verze](https://msdnshared.blob.core.windows.net/media/2019/04/Service-Fabric-6.4CU5-ReleaseNotes3.pdf)
| 2. května 2019 | [Vydání aktualizace pro Azure Service Fabric 6,4](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) | [Zpráva k vydání verze](https://msdnshared.blob.core.windows.net/media/2019/05/Service-Fabric-64CU6-Release-Notes-V2.pdf)
| 28. května 2019 | [Vydání aktualizace pro Azure Service Fabric 6,4](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) | [Zpráva k vydání verze](https://msdnshared.blob.core.windows.net/media/2019/05/Service_Fabric_64CU7_Release_Notes1.pdf)
