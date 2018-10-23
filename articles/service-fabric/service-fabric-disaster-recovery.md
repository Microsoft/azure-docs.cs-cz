---
title: Zotavení po havárii Azure Service Fabric | Dokumentace Microsoftu
description: Azure Service Fabric nabízí funkce, která je potřeba řešit všechny typy z jiného problému ovlivňujícího. Tento článek popisuje typy z jiného problému ovlivňujícího, které může dojít a jak zacházet s nimi.
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: b22d18408d040d564d6220e74e8b8a893fe41ae9
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2018
ms.locfileid: "49646241"
---
# <a name="disaster-recovery-in-azure-service-fabric"></a>Zotavení po havárii v Azure Service Fabric
Důležitou součástí zajištění vysoké dostupnosti zajišťuje, že služby přežijí všechny různé druhy chyb. To je obzvláště důležité pro chyby, které neplánované a mimo vaši kontrolu. Tento článek popisuje některé běžné režimy selhání, které může být jiného problému ovlivňujícího není-li modelovat a spravovány správně. Je také zaměří na omezení rizik a akcí Pokud přesto došlo k havárii. Cílem je omezit nebo eliminovat riziko výpadkům nebo ztrátě, které se objeví chyby, plánované nebo jinak, mohlo dojít.

## <a name="avoiding-disaster"></a>Jak se vyhnout po havárii
Service Fabric primární cílem je pomoct vám modelovat vaše prostředí a služby tak, že běžné typy selhání nejsou jiného problému ovlivňujícího. 

Obecně jsou dva typy scénářů po havárii nebo selhání:

1. Chyby hardwaru nebo softwaru
2. Provozní chyby

### <a name="hardware-and-software-faults"></a>Chyby hardwaru a softwaru
Chyby hardwaru a softwaru nepředvídatelné. Nejjednodušší způsob, jak překonat chyby běží více kopií služby rozložené přes hranice selhání hardwaru nebo softwaru. Například pokud vaše služba je spuštěná jenom na jeden konkrétní počítač, pak selhání jednoho počítače je po havárii za danou službu. Jednoduchým způsobem, aby tento po havárii je zajistit, že služba běží ve skutečnosti ve více počítačích. Testování je také potřeba zajistit, aby že selhání jednoho počítače nebude rušit spuštěné služby. Plánování kapacity zajišťuje je možné vytvořit instanci nahrazení, jinde a že snížení kapacity není přetížení zbývajících služeb. Stejný vzor funguje bez ohledu na to, co se pokoušíte Vyhněte se selhání. Např. Pokud máte obavy o selhání v síti SAN, spouštět napříč několika sítě SAN. Pokud máte obavy o ztrátu-of-rack serverů, můžete spustit do několika stojanů. Pokud máte obavy o ztrátou datových center, vaše služba by měla spustit napříč několika oblastmi Azure nebo datových centrech. 

Při spuštění v tomto typu rozložené režimu, budete pořád v souladu s některé typy současné selhání, ale jedním a ještě více selhání určitého typu (ex: jednoho selhává odkaz virtuálních počítačů nebo síťových) jsou automaticky zpracovány (a tedy už "havárii"). Service Fabric nabízí mnoho mechanismů pro rozšíření clusteru a přináší obslužné rutiny selhal uzly a služby zpět. Service Fabric také umožňuje spuštění více instancí služby předejdete tak tyto druhy neplánovaných selhání z zapnutí do skutečných havárií.

Možné důvody, proč není možné spuštěná dostatečně velký, aby zahrnovat selhání nasazení. Například může trvat více hardwarových prostředků, než jste ochotní k úhradě vzhledem k riziko selhání. Při zpracování komplexnějších distribuované aplikace, je možné, že stav replikace a další komunikaci směrování náklady napříč geografické vzdálenosti způsobí nepřijatelnou latenci. Pokud tento linie vychází se liší pro jednotlivé aplikace. Pro chyby softwaru konkrétně chyba může být ve službě, která se pokoušíte škálování. Další kopie není v tomto případě zabránit po havárii, protože chybový stav, je vztažen napříč všemi instancemi.

### <a name="operational-faults"></a>Provozní chyby
I v případě, že vaše služba je předané po celém světě s mnoha redundance, mohou stále katastrofální důsledky události. Pokud například někdo neúmyslně změní konfiguraci názvu dns pro danou službu nebo odstraní rovnou předplatit. Například Řekněme, že jste měli stavové služby Service Fabric, a někdo možné omylem odstranit tuto službu. Pokud není některá omezení, dané služby a veškerý stav měl je nyní pryč. Tyto typy provozní jiného problému ovlivňujícího ("ale ne") vyžadují různé způsoby zmírnění rizik a kroky pro obnovení než regulární neplánovaných selhání. 

Mají nejlepších způsobů, aby tyto typy provozní chyb
1. omezit provozní přístup k prostředí
2. přísně nebezpečné operace auditu
3. Uložit automatizace, bránit ruční nebo mimo pásmo změny a ověřit určité změny pro skutečné prostředí před jejich přijetí
4. Ujistěte se, že jsou destruktivní operace "text soft". Obnovitelné operace se projeví okamžitě, nebo lze zrušit v rámci některé časový interval

Service Fabric nabízí některé mechanismy, aby se zabránilo provozní chyby, jako je například poskytnutí [na základě rolí](service-fabric-cluster-security-roles.md) pro operace clusteru řízení přístupu. Většina tyto provozní chyby ale vyžadují organizační úsilí a dalšími systémy. Service Fabric poskytnout určitý mechanismus pro přežití provozní chyby, zejména zálohování a obnovení pro stavové služby.

## <a name="managing-failures"></a>Správa chyb
Cílem Service Fabric je téměř vždy Automatická správa chyb. Aby bylo možné zpracovat některé typy chyb, ale služby musí mít další kód. Jiné typy selhání by _není_ automaticky řešit z důvodů bezpečnosti a obchodní kontinuity podnikových procesů. 

### <a name="handling-single-failures"></a>Jeden selhání zpracování
Jednotlivé počítače může selhat pro celou řadu důvodů. Některé z nich se způsobí, že hardware, jako jsou napájení a sítě selhání hardwaru. Jiné chyby jsou v softwaru. Patří mezi ně selhání aplikace skutečný operační systém a samotné služby. Service Fabric automaticky rozpoznává tyto druhy chyb, včetně případů, kde se počítač stane izolované od jiných počítačů kvůli problémům se sítí.

Bez ohledu na typ služby spuštění jedné instance výsledky nebude znamenat výpadek za danou službu Pokud z nějakého důvodu selže jeden kopii kódu. 

Zvládnutí jakékoli jediné chyby, je nejjednodušší věcí, které vám pomůžou zajistit, že se vaše služby ve výchozím nastavení spouští na více než jeden uzel. Pro bezstavové služby, můžete to provést tak, že `InstanceCount` větší než 1. Pro stavové služby, je vždy minimální doporučení `TargetReplicaSetSize` a `MinReplicaSetSize` alespoň 3. Spuštění více kopií kódu služby se zajistí, že vaše služba dokáže zpracovat žádné jediné chyby automaticky. 

### <a name="handling-coordinated-failures"></a>Koordinovaný zpracování chyb
Koordinované selhání může dojít v clusteru kvůli buď plánované nebo neplánované infrastruktury chyby a změny nebo změny plánované softwaru. Service Fabric modely infrastruktury zón, které docházet k selháním koordinované jako domén selhání. Oblasti, které budou moct používat změny koordinované softwaru jsou modelovány jako upgradu domény. Další informace o chybových nebo upgradovacích doménách, musí [tento dokument](service-fabric-cluster-resource-manager-cluster-description.md) popisující Clusterová topologie a definice.

Ve výchozím nastavení Service Fabric bere v úvahu chybových nebo upgradovacích doménách při plánování, ve kterém by měly běžet vaše služby. Ve výchozím nastavení Service Fabric se pokusí zajistit, že vaše služby spouštět napříč několika chybových nebo upgradovacích doménách, pokud dojde k plánované nebo neplánované změnám služby nadále k dispozici. 

Například Řekněme, že selhání zdroj napájení způsobí, že rack počítačů současné selhání. S využitím více kopií běží ztráty velký počet počítačů v proti doméně selhání se změní jenom další příklad jednoho selhání dané služby. To je důvod, proč je důležité pro zajištění vysoké dostupnosti služby Správa domén selhání. Při spuštění v Azure Service Fabric, jsou automaticky spravovaných domén selhání. V jiných prostředích nemusí být. Pokud vytváříte vlastní clustery v místním prostředí, je potřeba namapovat a správně naplánovat rozložení domény selhání.

Upgradovací domény jsou užitečné pro modelování oblasti, kde se to software upgradovat ve stejnou dobu. Z tohoto důvodu definování upgradu domény také často hranice, ve kterém je software odstavit během plánovaného upgradu. Upgrade aplikace Service Fabric a služby postupujte podle stejného modelu. Další informace o upgradu, upgradovacích domén a stavu modelu Service Fabric, která pomáhá zabránit nežádoucí změny v vliv na vaši službu a clusteru se zajištěním provozu najdete v těchto dokumentech:

 - [Upgrade aplikace](service-fabric-application-upgrade.md)
 - [Kurz upgradu aplikace](service-fabric-application-upgrade-tutorial.md)
 - [Service Fabric stavu modelu](service-fabric-health-introduction.md)

Můžete vizualizovat rozložení clusteru pomocí cluster mapu poskytnutou metodou v [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md):

<center>
![Uzly rozděleny mezi doménami selhání v Service Fabric Exploreru][sfx-cluster-map]
</center>

> [!NOTE]
> Modelování oblasti selhání postupné upgrady, spouštění více instancí kódu služby a stav, pravidla pro umístění k zajištění služeb spouštět napříč chybových nebo upgradovacích doménách a monitorování integrovaných stavu jsou právě **některé** z Funkce, které poskytuje Service Fabric, aby bylo možné zabránit zapnutí do jiného problému ovlivňujícího normální provozní problémy a chyby. 
>

### <a name="handling-simultaneous-hardware-or-software-failures"></a>Zpracování souběžných selhání hardwaru nebo softwaru
Výše jsme se zabývali jedné chyby. Jak je vidět, se snadno zpracovat pro bezstavové a stavové služby pouhým udržování více kopií kódu (ve státě) fungovat v různých chybových nebo upgradovacích doménách. Selhání několika náhodné může také dojít. Jedná se pravděpodobně povede k skutečné po havárii.


### <a name="random-failures-leading-to-service-failures"></a>Náhodné chyby, což vede k selhání služby
Řekněme, že služba měli `InstanceCount` 5 a několik uzlů spuštění všechny tyto instance se nezdařilo ve stejnou dobu. Service Fabric reaguje, protože instance nahrazení se automaticky vytvoří na jiných uzlech. Vytváření nahrazení, dokud služba nebude zpět do tohoto počtu požadovaných instance bude pokračovat. Jako jiný příklad Řekněme, že se bezstavové služby pomocí `InstanceCount`-1, což znamená, poběží na všech uzlech clusteru platný. Řekněme, že některé z těchto instancí se nezdaří. Service Fabric v takovém případě zjistí, že není v jeho požadovaný stav služby a pokusí o vytvoření instance na uzlech, kde jsou chybějící. 

Pro stavové služby situace závisí na Určuje, zda má službu trvalý stav nebo ne. Také závisí na tom mnoho replik měli službu a kolik se nezdařilo. Určení, zda došlo k havárii pro stavové služby a správy řídí tři fáze:

1. Určení, pokud došlo ke ztrátě kvora nebo ne
 - Ztráty kvora je vždy většinou replik stavové služby jsou vypnuté ve stejnou dobu, včetně primární.
2. Určení, zda je trvalé ztrátě kvora, nebo ne
 - Ve většině případů, jsou přechodné chyby. Procesy, které se restartují, uzly se restartují, virtuální počítače jsou relaunched, opravy rozdělení sítě. V některých případech ale jsou trvalé selhání. 
    - Pro bez trvalé služby stavu, selhání kvorum, nebo více replik výsledků _okamžitě_ ve ztrátě kvora trvalé. Když Service Fabric zjistí stavové služby dočasné ztrátě kvora, okamžitě pokračuje ke kroku 3 () ztrátě dat deklarací. Budete k datům ztráty dává smysl, protože Service Fabric ví, že neexistuje žádný bod v čekání repliky k téhle akci vrátit, protože i v případě, že byla obnovena by byla prázdná.
    - Pro stavové trvalé služby způsobí selhání kvorum, nebo více replik Service Fabric spusťte čekání replik přejděte zpět a obnovit kvora. Výsledkem je výpadku služeb pro všechny _zapíše_ ovlivněné oddíl (nebo "sady replik") služby. Ale čtení může být stále možné s záruky konzistence nižší. Výchozí dobu, kterou Service Fabric počká kvora pro obnovení je nekonečné, protože budete pokračovat, je (potenciální) před událostí ztráty a má jiná rizika. Přepsání výchozího `QuorumLossWaitDuration` hodnotu je možné, ale nedoporučuje. Místo toho v současné době veškeré úsilí je třeba obnovit dolů repliky. To vyžaduje přináší uzly, které jsou vypnuté zálohování a zajištění, že se znovu připojte jednotky, kde uloženy místní trvalý stav. Je-li ztráty kvora selhání procesu, Service Fabric automaticky pokusí znovu vytvořit procesy a restartujte repliky v nich obsažené. Když se to nepovede, Service Fabric oznámí chyby stavu. Pokud tyto lze vyřešit repliky obvykle vraťte se zpátky. V některých případech však repliky nelze uvést do zpět. Například jednotky může všechny selhaly nebo počítače fyzicky zničení nějakým způsobem. V těchto případech se teď máme událostí ztráty trvalé kvora. Předat ukončení čekání dolů repliky k téhle akci vrátit služba Service Fabric, Správce clusteru musíte určit, které oddíly, jejichž služby se to týká a volání `Repair-ServiceFabricPartition -PartitionId` nebo ` System.Fabric.FabricClient.ClusterManagementClient.RecoverPartitionAsync(Guid partitionId)` rozhraní API.  Toto rozhraní API umožňuje zadat ID oddílu, který se má přesunout mimo QuorumLoss a do potenciální dataloss.

  > [!NOTE]
  > Je _nikdy_ bezpečné používat toto rozhraní API jiných než způsobem cílené na konkrétní oddíly. 
  >

3. Zjištění, zda došlo ke ztrátě skutečná data a obnovení ze zálohy
  - Když Service Fabric volá `OnDataLossAsync` metoda, je vždy z důvodu _vzbuzovat podezření na_ únikem informací. Service Fabric zajišťuje, že toto volání se doručí do _nejlepší_ zbývající repliky. To je podle toho, která repliky byla provedena většina průběh. Z důvodu vždy říkáme _podezřelý_ ztrátě dat je, že je možné, že zbývající repliky ve skutečnosti má všechny stejného stavu jako primární udělali při snížila. Ale bez tímto stavem, které se má porovnat, neexistuje žádný dobrý způsob, jak Service Fabric nebo operátory s jistotu zjistit. V tomto okamžiku Service Fabric také ví, že že repliky nejsou vracejte. Bylo to rozhodnutí provedených při zastavení čeká na ztráty kvora sám nevyřeší. Nejlepší postup pro službu je obvykle ukotvit a počkat na konkrétní zásahů správce. Tak co dělá obvyklá implementace metody `OnDataLossAsync` metody?
  - Nejprve, protokolu, který `OnDataLossAsync` byla spuštěna a aktivovat výstrahy všechny potřeby správy.
   - Obvykle v tomto okamžiku pozastavení a počkejte na další rozhodnutí a ručně prováděné akce, jež mají být provedeny. Je to proto, že i v případě, že nejsou k dispozici zálohy se možná muset připravit. Například pokud dvě různé služby koordinovat informace, tyto zálohy možná muset upravit tak, aby Ujistěte se, že po obnovení se stane, informace o těchto dvou služeb záleží konzistentní vzhledem k aplikacím. 
  - Často se také některé další telemetrická data nebo výfukového ze služby. Tato metadata mohou být obsaženy v dalších služeb nebo v protokolech. Tyto informace můžete použít potřebných k určení, pokud byly všechna volání přijme a zpracuje na primární, které nebyly k dispozici v záloze nebo replikují do této konkrétní repliky. Je budete muset znovu přehrát nebo přidaný do zálohování, obnovení je možné použít.  
   - Porovnání zbývající repliky stavu, který obsažen v žádné zálohy, které jsou k dispozici. Pokud použitím spolehlivých kolekcí Service Fabric, existují nástroje a procesy k dispozici to udělat, je popsáno v [v tomto článku](service-fabric-reliable-services-backup-restore.md). Cílem je zobrazíte stav v rámci repliky není dostatečná, zda také jaké zálohování může chybět.
  - Po porovnání se provádí a potřeby obnovení bylo dokončeno, kód služby by měl vrátit hodnotu true, pokud byly provedeny nějaké změny stavu. Pokud replika určit, že se nejlépe k dispozici kopii stavu a provedeny žádné změny, pak se vraťte hodnotu false. Hodnota TRUE označuje, že všechny _jiných_ zbývající repliky může být nyní nekonzistentní s touto položkou. Se bude vyřazen a znovu vytvořen z této repliky. Hodnota false označuje, že nebyly provedeny žádné změny stavu, tak tyto repliky můžete ponechat toho, co máte. 

Je obzvláště důležité, že služba autoři praxi potenciální scénáře selhání a ztrátou dat před služby jsou někdy nasadili v produkčním prostředí. K ochraně proti možné ztrátě dat, je potřeba pravidelně [zálohovat stav](service-fabric-reliable-services-backup-restore.md) žádné stavové služby do geograficky redundantního úložiště. Musíte také zajistit, že máte možnost ji obnovit. Protože zálohy z mnoha různých služeb jsou prováděny v různou dobu, je potřeba zajistit, že po obnovení vaší služby mají konzistentní zobrazení vzájemně. Představte si třeba situaci, kde jedna služba vygeneruje číslo a uloží je a odesílá je do jiné službě, která také ukládá. Po obnovení můžete zjistit, že druhou služby má číslo ale první nepodporuje, protože je zálohování nezahrnuli tuto operaci.

Pokud zjistíte, že zbývající repliky dostatečná k pokračovat v případě ztráty dat, a nelze rekonstruovat služby stavu z telemetrických dat nebo výfukového, četnost záloh určí vaší nejlepší cíl bodu možné obnovení (RPO). Service Fabric nabízí celou řadu nástrojů pro testování různé scénáře selhání, včetně trvalé kvora a ztrátě dat, které vyžadují obnovení ze zálohy. Tyto scénáře jsou zahrnuty jako součást nástrojů pro testovatelnost Service Fabric spravuje Fault Analysis Service. Další informace o těchto nástrojů a vzorů je k dispozici [tady](service-fabric-testability-overview.md). 

> [!NOTE]
> Služby systému také může být negativně ztráty kvora, s vlivem specifické pro příslušné služby. Například ztráty kvora ve službě pojmenování service má vliv na překlad, zatímco blokuje ztráty kvora ve službě Správce převzetí služeb při selhání vytvoření nové služby a převzetí služeb při selhání. Zatímco systémové služby Service Fabric postupují stejným způsobem jako u služeb pro správu stavu, není doporučeno, měli byste se pokusit se je přesunout mimo ztráty kvora a do potenciální ztráty dat. Doporučujeme místo toho [vyhledat podporu](service-fabric-support.md) k určení řešení, které cílí na konkrétní situaci.  Obvykle je vhodnější jednoduše počkat, až dolů repliky vrátit.
>

## <a name="availability-of-the-service-fabric-cluster"></a>Dostupnost clusteru Service Fabric
Obecně řečeno samotného clusteru Service Fabric je hodně rozptýlenou prostředí s žádný jediný bod selhání. Selhání jednoho libovolného uzlu nezpůsobí dostupnosti nebo problémy se spolehlivostí pro cluster, především proto systémové služby Service Fabric použijte stejné pokyny jako dříve k dispozici: se vždy spustila s třemi nebo více replikami, ve výchozím nastavení systému služby, které jsou bezstavové spouštět na všech uzlech. Service Fabric sítě a selhání detekce vrstvách jsou plně distribuovány. Většina služeb systému můžete znovu sestavit z metadat v clusteru nebo vědět, jak se Opětovná synchronizace jejich stav z jiného místa. Dostupnost clusteru mohou způsobit ohrožení, pokud se zobrazí systémové služby do kvora ztráty situace jako jsou tu popsané výše. V těchto případech nemusí být schopna provádět určité operace v clusteru, například spuštěním upgradu nebo nasazování nových služeb, ale samotného clusteru je pořád zapnutý. Služby na spuštěna zůstane běžící v těchto podmínkách, pokud potřebují zápisy do systémové služby nadále fungovat. Například pokud správce převzetí služeb při selhání je ve ztrátě kvora všechny služby budou nadále spuštěné, ale žádné služby, které selžou, nebude možné automaticky restartovat, protože to vyžaduje zapojení správce převzetí služeb při selhání. 

### <a name="failures-of-a-datacenter-or-azure-region"></a>Selhání datacentra nebo oblasti Azure
Ve výjimečných případech může být fyzické datové centrum dočasně nedostupný kvůli ztrátám napájení nebo připojením k síti. V těchto případech se clustery Service Fabric a služeb v daném datacentra nebo oblasti Azure bude k dispozici. Ale _vaše data se zachovají_. Pro clustery spuštěné v Azure, můžete zobrazení aktualizací na výpadky na [stránce stavu Azure][azure-status-dashboard]. V případě vysoce nepravděpodobné, že fyzické datové centrum částečně nebo zcela odstraněna, všechny clustery Service Fabric hostované existuje nebo služby v nich by mohly být ztraceny. To zahrnuje jakýkoli stav nejsou zálohovány mimo tento datacentra nebo oblasti.

Není k dispozici dva různé strategie pro přežití trvalé nebo trvalé selhání jednoho datacentra nebo oblasti. 

1. Spouštění samostatné clustery Service Fabric do několika těchto oblastí a pomocí některé mechanismus pro převzetí služeb při selhání a navrácení služeb po zpět mezi těmito prostředími. Tento druh více cluster typu aktivní aktivní nebo aktivní pasivní modelu vyžaduje další operace správy a kódu. Tento proces také vyžaduje koordinaci záloh v rámci služeb do jednoho datacentra nebo oblasti tak, aby byly k dispozici v jiných datacentrech nebo oblastech, pokud jeden selže. 
2. Spusťte jeden cluster Service Fabric, která zahrnuje více datacentrech nebo oblastech. Minimální podporovanou konfiguraci pro tento je tři datacentrech nebo oblastech. Doporučený počet oblastí nebo datových centrech je pět. To vyžaduje složitější topologie clusteru. Výhodou tohoto modelu je však, že selhání jednoho datacentra nebo oblasti se převede po havárii na normální selhání. Tyto chyby může být zpracována mechanismy, které fungují pro clustery v rámci jedné oblasti. Domén selhání a upgradovacích domén, pravidla pro umístění Service Fabric zajistěte, aby úlohy distribuují tak, aby se tolerovat běžné chyby. Další informace o zásadách, které pomáhají provozovat služby v tomto typu clusteru, přečtěte si [zásady umístění](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)

### <a name="random-failures-leading-to-cluster-failures"></a>Náhodné chyby, což vede k selhání clusteru
Service Fabric obsahuje koncepci počáteční uzly. Jedná se o uzly, které zajišťují dostupnost základního clusteru. Tyto uzly pomáhají zajistit, že cluster zůstane až vytvořením zapůjčení s ostatními uzly a slouží jako tiebreakers během určité druhy chyb v síti. Pokud nejsou přepne zpět do režimu náhodným chybám odebrání většinou počáteční uzly v clusteru, cluster automaticky ukončí. V Azure, jsou automaticky spravovány počáteční uzly: jsou rozloženy dostupné chybových nebo upgradovacích doménách, a pokud jednu počáteční uzel je odebrán z clusteru bude vytvořen jinou na příslušné místo. 

"Primární uzel typu" v samostatných clusterů Service Fabric a Azure, je ten, který běží rychlosti. Při definování typu primárního uzlu Service Fabric se automaticky výhod počet uzlů zajištěna vytvořením až 9 počáteční uzly a 7 repliky všech systémových služeb. Sada náhodná selhání trvá současně si většina těchto replik služby systému, systémové služby přejde ztráty kvora, jak jsme je popsáno výše. Pokud se většina počáteční uzly jsou ztraceny, cluster se vypne krátce po.

## <a name="next-steps"></a>Další postup
- Zjistěte, jak simulace různých chyb pomocí [framework testovatelnosti](service-fabric-testability-overview.md)
- Přečtěte si další prostředky pro zotavení po havárii a vysoká dostupnost. Společnost Microsoft publikovala velké množství pokyny v těchto tématech. Zatímco některé z těchto dokumentů odkazovat na konkrétní postupy pro použití v jiných produktech, které obsahují mnoho obecné osvědčené postupy, které lze použít v rámci Service Fabric:
  - [Kontrolní seznam k dostupnosti](../best-practices-availability-checklist.md)
  - [Provedení postupu zotavení po havárii](../sql-database/sql-database-disaster-recovery-drills.md)
  - [Zotavení po havárii a vysoká dostupnost pro aplikace Azure][dr-ha-guide]
- Informace o [možnostech podpory pro Service Fabric](service-fabric-support.md)

<!-- External links -->

[repair-partition-ps]: https://msdn.microsoft.com/library/mt163522.aspx
[azure-status-dashboard]:https://azure.microsoft.com/status/
[azure-regions]: https://azure.microsoft.com/regions/
[dr-ha-guide]: https://msdn.microsoft.com/library/azure/dn251004.aspx


<!-- Images -->

[sfx-cluster-map]: ./media/service-fabric-disaster-recovery/sfx-clustermap.png
