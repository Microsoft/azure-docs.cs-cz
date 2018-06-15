---
title: Zotavení po havárii Azure Service Fabric | Microsoft Docs
description: Azure Service Fabric nabízí možnosti, které jsou nutné zabývat se všechny typy havárie. Tento článek popisuje typy havárie, které můžou nastat a o tom, jak nakládat s nimi.
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
ms.openlocfilehash: 295772b70529f79c7a4c135d8ea7c12a1c661fe6
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
ms.locfileid: "34206432"
---
# <a name="disaster-recovery-in-azure-service-fabric"></a>Zotavení po havárii v Azure Service Fabric
Důležitou součástí doručování vysoká dostupnost zajišťuje, že služby přežijí všechny různých typů chyb. To je obzvláště důležité pro chyby, které neplánované a mimo váš dosah. Tento článek popisuje některé běžné selhání režimy, které může být havárie není-li modelovány a spravovat správně. Také popisují, akce mají přijmout v případě havárie bylo provedeno přesto a způsoby zmírnění rizik. Cílem je omezit nebo vyloučit riziko výpadku nebo ztráty dat při jejich výskytu chyb, plánované nebo jinak, mohlo dojít.

## <a name="avoiding-disaster"></a>Zamezení po havárii
Primární cílem Service Fabric je můžete model prostředí a vaše služby tak, že běžné typy selhání nejsou havárie. 

Obecně existují dva typy po havárii nebo selhání scénáře:

1. Hardwarový nebo softwarový chyb
2. Provozní chyb

### <a name="hardware-and-software-faults"></a>Chyby hardwaru a softwaru
Hardware a software chyby mohou být nepředvídatelné. Nejjednodušší způsob, jak zachována i po ukončení chyb běží více kopií služby předané hranice selhání hardwaru nebo softwaru. Například pokud vaše služba běží pouze na jednu konkrétní počítač, pak selhání tohoto jednoho počítače je po havárii pro danou službu. Jednoduchý způsob, jak zabránit tento po havárii je zajistit, že služba běží ve skutečnosti ve více počítačích. Testování je také potřeba zajistit, aby že selhání jeden počítač nemá přerušit spuštěné služby. Plánování kapacity zajišťuje jinde vytvořením nahrazení instance a že snížení kapacity nemá přetížení zbývajících služeb. Stejného vzoru funguje bez ohledu na to, co se pokoušíte vyhnout selhání. Například. Pokud máte obavy o selhání sítě SAN, spuštěním napříč více sítě SAN. Pokud máte obavy o ztrátu rack serverů, můžete spustit napříč rackovými stojany. Pokud máte obavy o ztrátě datových center, měli spustit služby napříč více oblastí Azure nebo datových centrech. 

Při spuštění v tomto typu rozložené režimu, jste stále vztahují některé typy souběžných selhání, ale jeden a i několik chyb určitého typu (například: jeden odkaz selhání virtuálního počítače nebo sítě) jsou zpracovávány automaticky (a proto už "havárie"). Service Fabric nabízí mnoho mechanismy pro rozšíření clusteru a převedení obslužné rutiny selhalo uzly a služby zpět. Service Fabric také umožňuje spouštět velký počet instancí služeb předejdete tak tyto typy neplánované výpadky z měnící do skutečné havárie.

Může být z důvodů, proč není možné běží dostatečně velký pro span přes selhání nasazení. Například může trvat další hardwarové prostředky, než jste ochotni platit relativně k riziko selhání. Při plánování práce s distribuovaných aplikací, může to být, stav replikace a další komunikaci směrování stojí napříč geografické vzdálenosti příčiny nepřijatelné latence. Místo, kde je tento řádek vykreslovat se liší pro jednotlivé aplikace. Pro chyby softwaru konkrétně selhání může být v rámci služby, který se pokoušíte škálování. Další kopie není v tomto případě zabránit po havárii, vzhledem k tomu, že stav selhání je korelační napříč všemi instancemi.

### <a name="operational-faults"></a>Provozní chyb
I v případě, že je vaše služba předané po celém světě s mnoha propouštění, může stále přetrvávají katastrofální události. Pokud například někdo neúmyslně změní konfiguraci názvu dns pro službu nebo odstraní přímý. Jako příklad předpokládejme měl stavové služby Service Fabric a někdo omylem odstranit tuto službu. Pokud se některé další omezení rizik, služby a všechny stavu, jako je nyní zmizel. Tyto typy provozu havárie ("bohužel") vyžadují různé způsoby zmírnění rizik a kroky pro obnovení než regulární neplánované výpadky. 

Mají nejlepších způsobů, jak se vyhnout tomuto typu provozní chyb
1. omezit provozní přístup do prostředí
2. výhradně nebezpečné operace auditu
3. použít automatizace, zabránit ruční nebo mimo pásmo změny a ověřit určité změny ve skutečné prostředí před jejich přijetí
4. Zkontrolujte, zda jsou destruktivní operace "soft". Logicky operations není se projeví okamžitě nebo může být v rámci některé časové okno vrátit zpět

Service Fabric nabízí některé mechanismy, aby se zabránilo provozní chyb, jako je například poskytuje [na základě rolí](service-fabric-cluster-security-roles.md) přístup k ovládacímu prvku pro operace clusteru. Většina těchto provozní chyb však vyžadují organizační úsilí a dalšími systémy. Service Fabric nabízejí některé mechanismus zbývajících provozní chyb, zejména zálohování a obnovení pro stavové služby.

## <a name="managing-failures"></a>Správa chyb
Cílem Service Fabric je téměř vždy Automatická správa chyb. Ale zvládnout některé typy chyb, musí mít další kód. Jiné typy chyb by _není_ automaticky řešit kvůli důvodům, zabezpečení a obchodní kontinuity. 

### <a name="handling-single-failures"></a>Jeden selhání zpracování
Jednoho počítače může selhat pro nejrůznějším důvodů. Některé z nich se příčiny hardwaru, jako je napájení a sítě selhání hardwaru. Jiné chyby jsou v softwaru. Mezi ně patří selhání skutečné operačního systému a službu samotnou. Service Fabric automaticky rozpozná tyto typy chyb, včetně případů, kdy se počítač stal izolované od ostatních počítačů kvůli problémům se sítí.

Bez ohledu na typ služby spuštěna jedna instance výsledky výpadek pro tuto službu Pokud z nějakého důvodu selže této jednu kopii kód. 

Aby bylo možné zpracovat žádné jediné chyby, je nejjednodušší věcí, kterou můžete provést k zajištění, že vašim službám spustit na více než jeden uzel ve výchozím nastavení. Pro bezstavové služby, můžete to provést tak, že `InstanceCount` větší než 1. Pro stavové služby, je vždy minimální doporučení `TargetReplicaSetSize` a `MinReplicaSetSize` aspoň 3. Spuštění více kopií kódu služby zajišťuje, že vaše služba dokáže zpracovat žádné jediné chyby automaticky. 

### <a name="handling-coordinated-failures"></a>Koordinované zpracování chyb
Koordinované selhání může dojít v clusteru s podporou důvodů plánované nebo selhání neplánované infrastruktury a změny nebo změny plánované softwaru. Service Fabric modely infrastruktury zón, které dojde k selhání koordinované jako domén selhání. Oblasti, které budou mít změny koordinované softwaru jsou modelovat jako upgradu domény. Další informace o doménách selhání a upgradu najdete v [tento dokument](service-fabric-cluster-resource-manager-cluster-description.md) definice a topologie clusteru, který popisuje.

Ve výchozím nastavení považuje Service Fabric doménách selhání a upgradu, při plánování, které by měly běžet vaše služby. Ve výchozím nastavení Service Fabric pokusí zkontrolujte, jestli vaše služby spustili mezi několik domén selhání a upgradu, pokud změny plánovaná nebo neplánovaná vašim službám zůstanou dostupné. 

Například předpokládejme, že selhání zdroje napájení způsobí, že racku počítačů současné selhání. S více kopií službu ztrátu velký počet počítačů v doméně selhání selhání se změní jenom další příklad jediné chyby pro danou službu. Z tohoto důvodu Správa domén selhání je důležité používat k zajištění vysoké dostupnosti služeb. Při spuštění v Azure Service Fabric, jsou automaticky spravované domény selhání. V jiných prostředích nemusí být. Pokud vytváříte vlastní clustery místně, je nutné mapovat aplikací a naplánujte rozložení domény selhání správně.

Domén upgradu jsou užitečné pro modelování oblasti, kam softwaru k upgradu ve stejnou dobu. Z toho důvodu upgradu domén také často zadejte hranice, kde je ukončena softwaru během plánované upgrady. Upgrady Service Fabric a vaše služby podle stejného modelu. Další informace o vrácení upgrady, domén upgradu a modelu stavu Service Fabric, která pomáhá zabránit neúmyslnému změny v clusteru a služby, které mají vliv najdete v těchto dokumentů:

 - [Upgrade aplikace](service-fabric-application-upgrade.md)
 - [Kurz upgradu aplikace](service-fabric-application-upgrade-tutorial.md)
 - [Model stavu prostředků infrastruktury služby](service-fabric-health-introduction.md)

Můžete vizualizovat rozložení pomocí mapy clusteru v clusteru [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md):

<center>
![Uzly rozloženy domén selhání v Service Fabric Exploreru][sfx-cluster-map]
</center>

> [!NOTE]
> Modelování oblasti nezdaří, vrácení upgrady, spuštěním mnoho instancí kódu služby a stav, umístění pravidla pro zajištění vaší služby spusťte napříč doménami selhání a upgradu a sledování stavu předdefinované jsou právě **některé** funkcí této Service Fabric nabízí, chcete-li zabránit měnící do havárie normální provozní problémy a chyby. 
>

### <a name="handling-simultaneous-hardware-or-software-failures"></a>Zpracování souběžných selhání hardwaru nebo softwaru
Vyšší už jsme mluvili o jeden selhání. Jak vidíte, lze snadno zpracovat pro bezstavové a stavové služby, stejně tak další kopie kódu (a stavu) spuštěná v doménách selhání a upgradu. Více souběžných náhodné chyby může také dojít. To se pravděpodobně povede k skutečné po havárii.


### <a name="random-failures-leading-to-service-failures"></a>Náhodné chyby vedoucí k selhání služby
Řekněme, že služba měla `InstanceCount` 5 a několik uzlů spuštěné tyto všechny instance se nezdařilo ve stejnou dobu. Service Fabric reaguje automaticky vytváření instancí nahrazení na jiných uzlech. Vytváření náhrady, dokud služba je zpět do tohoto počtu požadované instance bude pokračovat. Například Řekněme došlo bezstavové služby s `InstanceCount`-1, což znamená, běží na všechny platné uzly v clusteru. Řekněme, že některé z těchto instancí byly selhání. V takovém případě Service Fabric oznámení, že není v jeho požadovaný stav služby a se pokusí vytvořit instance na uzlech tam, kde jsou chybějící. 

Pro stavové služby situaci závisí na tom, jestli služba obsahuje trvalé stavu, nebo ne. Také závisí na tom mnoho repliky měl službu a kolik se nezdařilo. Určení, zda došlo k havárii pro stavové služby a správu odpovídá tří fází:

1. Určení, zda existuje došlo ke ztrátě kvora nebo ne
 - Ztrátě kvora je vždy, když jsou většinou replik stavové služby dolů ve stejnou dobu, včetně primární.
2. Určení, zda je trvalé ztrátě kvora, či nikoliv
 - Ve většině případů, jsou přechodné chyby. Procesy, jsou restartovány, uzly se restartují, virtuální počítače jsou relaunched, retušovat síťové oddíly. V některých případech ale selhání jsou trvalé. 
    - Pro služby bez trvalý stav, selhání kvorum nebo více replik výsledků _okamžitě_ ve ztrátě kvora trvalé. Zjistí-li Service Fabric ztrátě kvora v stavové služby dočasnou, okamžitě pokračuje ke kroku 3 deklarováním dataloss (potenciální). Pokračováním dataloss má smysl, protože Service Fabric ví, že neexistuje žádný bod v čekání replik vraťte, protože i v případě, že byly obnoveny by být prázdný.
    - Pro stavové služby trvalé způsobí selhání kvorum nebo více replik Service Fabric zahájíte čekání replik se vrátit a obnovení kvora. To vede k výpadku služeb pro libovolný _zapíše_ ovlivněných oddílu (nebo "sady replik") službu. Ale čtení může být stále možné s omezenou konzistence záruky. Výchozí množství času, kterou Service Fabric čeká kvora pro obnovení je nekonečno, vzhledem k tomu, že budete pokračovat, je (potenciální) událost dataloss a představuje další rizika. Přepsání výchozího `QuorumLossWaitDuration` hodnotu je možné, ale není to doporučeno. Místo toho v tuto chvíli veškeré úsilí je třeba obnovit dolů repliky. To vyžaduje přinesou uzly, které jsou vypnutí zálohování a zajistit, aby se znovu připojit jednotky, kde budou uloženy místní trvalý stav. Pokud ztrátě kvora je způsobena selhání procesu, Service Fabric se pokusí automaticky znovu procesy a restartujte repliky v nich. Pokud se to nezdaří, Service Fabric hlásí stav chyby. Pokud tyto lze vyřešit pak repliky obvykle se vrátit. V některých případech ale repliky nelze převést zpět. Například jednotky všechny nebyla úspěšná, nebo na počítače fyzicky zničeno nějakým způsobem. V těchto případech máme teď ztráty trvalé kvora. Říct Service Fabric zastavit čekání dolů repliky opět online, musí správce clusteru určit, které oddíly, které služby jsou vliv a volání `Repair-ServiceFabricPartition -PartitionId` nebo ` System.Fabric.FabricClient.ClusterManagementClient.RecoverPartitionAsync(Guid partitionId)` rozhraní API.  Toto rozhraní API umožňuje zadání ID oddílu přesunout mimo QuorumLoss a do potenciální dataloss.

> [!NOTE]
> Je _nikdy_ bezpečně používat toto rozhraní API jinak než v cílové způsob, jak na konkrétní oddíly. 
>

3. Určení, zda došlo ke ztrátě skutečná data a obnovení ze zálohy
  - Při volání Service Fabric `OnDataLossAsync` metoda je vždy z důvodu _by mohly vzbuzovat podezření_ dataloss. Service Fabric zajistí, že toto volání se doručí na _nejlepší_ zbývající repliky. Toto je, podle toho, která repliky udělal většina průběh. Z důvodu vždy říkáme _by mohly vzbuzovat podezření_ dataloss je, že je možné, že zbývající repliky ve skutečnosti má všechny stejného stavu jako primární to udělali při snížila. Ale bez k porovnat s tímto stavem, neexistuje žádný vhodný způsob pro Service Fabric nebo operátory zcela jisti. V tomto okamžiku Service Fabric také ví, že další repliky nejsou vracející se zpět. Rozhodnutí provedeny, když jsme zastavil čeká na ztrátě kvora sám nevyřeší, který byl. Nejlepší akce pro službu je většinou zmrazení a počkat na konkrétní zásahů správce. Jaké jsou proto Typická implementace `OnDataLossAsync` metoda provést?
  - Nejprve protokolu, který `OnDataLossAsync` byla spuštěna a spusťte vypnout všechny potřebné pro správu výstrahy.
   - Obvykle v tomto okamžiku pozastavení a čekat na další rozhodnutí a ručně prováděné akce, které mají být provedeny. Je to proto, že i v případě, že nejsou dostupné zálohy budou muset připravit. Například pokud dva různé služby koordinaci informace, tyto zálohy možná muset upravit, aby se zajistilo, že po obnovení stane, že tyto dvě služby péče o je konzistentní. 
  - Často je zde také některé další telemetrií nebo výfukového ze služby. Tato metadata mohou být obsaženy v jiných služeb nebo v protokolech. Tyto informace můžete použít potřeba zjistit, pokud byly všechny volání přijme a zpracuje na primární, které nebyly součástí zálohy nebo replikují do této konkrétní repliky. Tyto muset přehrány nebo přidán do zálohování, než je možné je obnovení.  
   - Porovnání hodnot stavu zbývající repliky, která obsahovala v všechny zálohy, které jsou k dispozici. Pokud pomocí spolehlivé kolekcí Service Fabric, pak jsou nástroje a zpracuje k dispozici pro to, které jsou popsané v [v tomto článku](service-fabric-reliable-services-backup-restore.md). Cílem je zda stav v rámci replika bude stačit, nebo jaké zálohování může být také chybí.
  - Jakmile porovnání se provádí a potřeby obnovení bylo dokončeno, kódu služby by měla vrátit hodnotu true, pokud nebyly provedeny žádné změny stavu. Pokud replika zjištěno, že byla nejlépe kopie stavu, která je k dispozici a provedeny žádné změny, pak se vraťte hodnotu false. Pravda označuje, že jakékoli _jiných_ zbývající repliky může být nyní nekonzistentní tímto tématem. Bude být zrušen a znovu vytvořen z této repliky. NEPRAVDA označuje, že nebyly provedeny žádné změny stavu, tak další repliky můžete ponechat co mají. 

Je důležité, že autoři služby praxi potenciální dataloss a scénářích selhání, před služby jsou někdy nasazením v produkčním prostředí. K ochraně proti možnosti dataloss, je potřeba pravidelně [zálohovat stav](service-fabric-reliable-services-backup-restore.md) každého vaše stavové služby, aby geograficky redundantní úložiště. Musí také zajistit, že budete mít možnost obnovit. Vzhledem k tomu, že zálohy mnoho různých služeb se provádějí v různou dobu, budete muset zajistěte, aby po obnovení vašim službám konzistentní zobrazení navzájem. Představte si třeba situaci, kde jedna služba vygeneruje číslo a uloží ji pak odešle ji do jiné službě, která také ukládá ji. Po obnovení může zjistit, že službu druhé číslo však první nemá není, protože je zálohování nezahrnuli tuto operaci.

Pokud zjistíte, že zbývající repliky nestačí pokračovat od ve scénáři dataloss a nemůže rekonstrukci stav služby z telemetrie nebo výfukového, určuje četnost záloh vaší nejlepší plánovaného bodu možné obnovení (RPO). Service Fabric nabízí celou řadu nástrojů pro testování různých scénářích selhání, včetně trvalé kvora a dataloss, které vyžadují obnovení ze zálohy. Tyto scénáře jsou zahrnuty jako součást nástrojů testovatelnosti Service Fabric, spravované službou Analysis selhání. Další informace o těchto nástrojů a vzory je k dispozici [zde](service-fabric-testability-overview.md). 

> [!NOTE]
> Systémové služby může také dojít k ztráty kvora, s vlivem specifické pro příslušné služby. Například ztrátě kvora ve službě pojmenování ovlivňuje překlad IP adres, zatímco ztrátě kvora ve službě Správce převzetí služeb při selhání blokuje vytvoření nové služby a převzetí služeb při selhání. Při systémových služeb Service Fabric postupují stejným způsobem jako vaší služby pro správu stavu, není doporučeno, pokuste se je přesunout mimo ztrátě kvora a do potenciální dataloss. Doporučuje se místo toho [vyhledat podporu](service-fabric-support.md) k určení řešení, které je cílená na konkrétní situaci.  Obvykle je vhodnější jednoduše Počkejte, dokud vrátit dolů repliky.
>

## <a name="availability-of-the-service-fabric-cluster"></a>Dostupnost clusteru Service Fabric
Obecně řečeno samotného clusteru Service Fabric je vysoce distribuovaném prostředí s žádný jediný bod selhání. Selhání jednoho libovolného uzlu nezpůsobí dostupnosti nebo problémy se spolehlivosti pro cluster, především, protože systémových služeb Service Fabric postupujte stejným pokynů dříve: budou vždy spustit s tři nebo více replik ve výchozím nastavení, a tyto služby systému, které jsou bezstavové spouštět na všech uzlech. Základní vrstvy detekce Service Fabric sítě a selhání jsou plně distribuován. Většina systémových služeb můžete znovu sestavit z metadat v clusteru nebo vědět, jak znovu synchronizovat jejich stavu z jiného místa. Dostupnost clusteru může způsobit ohrožení, pokud systémových služeb do kvora případů ztráty jako ty, které jsou popsané výše. V těchto případech nemusí být možné k provádění některých operací v clusteru jako spuštěním upgradu nebo nasazování nových služeb, ale samotný cluster je pořád zapnutý. Služeb v již byla spuštěna zůstane spuštěný v těchto podmínkách, pokud potřebují zápisy k službám systému, aby fungovaly. Například pokud je manažer převzetí služeb při selhání ve ztrátě kvora všechny služby bude nadále spouštět, ale žádné služby, které nesplní nebude možné automaticky restartovat, protože to vyžaduje zapojení správce převzetí služeb při selhání. 

### <a name="failures-of-a-datacenter-or-azure-region"></a>Selhání datacenter nebo oblast Azure
Ve výjimečných případech se může stát dočasně nedostupná z důvodu ztráty power ani síťové připojení fyzických datového centra. V těchto případech clusterů Service Fabric a služeb na tomto datovém centru nebo v oblasti Azure k dispozici. Ale _vašich dat se zachová, i_. Pro clustery spuštěná v Azure, můžete zobrazit aktualizace na výpadky na [Azure stavové stránce][azure-status-dashboard]. V případě vysoce nepravděpodobné, fyzické datového centra částečně nebo zcela zničení, všechny clustery infrastruktury služby hostované existuje nebo služby v nich by mohly být ztraceny. To zahrnuje všechny stavu nejsou zálohovány mimo dané datacenter nebo oblasti.

Není k dispozici dvě různé strategie pro zbývajících trvalé nebo dlouhodobě selhání jednoho datového centra nebo oblasti. 

1. Spusťte samostatných clusterů Service Fabric v několika takových oblastech a využívat některé mechanismus pro převzetí služeb při selhání a navrácení služeb mezi těchto prostředích. Tento druh více clusteru aktivní aktivní nebo aktivní – pasivní modelu vyžaduje další operace správy a kód. Také je vyžadována koordinaci zálohy ze služby v jednom datacenter nebo oblast, aby byly k dispozici v dalších datových center nebo oblastech, pokud jeden selže. 
2. Spusťte jeden cluster Service Fabric, která přesahuje více datových centrech nebo oblasti. Na minimální podporovanou konfiguraci pro toto je tři datových centrech nebo oblasti. Doporučený počet oblastí nebo datových centrech je pět. To vyžaduje složitější topologie clusteru. Výhodou tohoto modelu je ale, že selhání jednoho datového centra nebo oblasti je převeden po havárii na normální selhání. Tyto chyby mohou být zpracována mechanismy, které fungují pro clustery v jedné oblasti. Domén selhání, domén upgradu a pravidla pro umístění Service Fabric Ujistěte se, že úlohy distribuují tak, aby se tolerovat selhání normální. Další informace o zásadách, které vám mohou pomoci provoz služby v tomto typu clusteru, najdete v tématu na [zásady umístění](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)

### <a name="random-failures-leading-to-cluster-failures"></a>Náhodné chyby úvodní vytvářením clusteru
Service Fabric obsahuje koncepci uzly počáteční hodnoty. Jedná se o uzly, které udržují dostupnost základní clusteru. Tyto uzly Nápověda zajistěte, aby byl že cluster zůstane až zřízení zapůjčení s ostatními uzly a slouží jako tiebreakers během určité druhy chyb v síti. Pokud náhodné chyby odebrat většinou uzly počáteční hodnoty v clusteru a jejich zpět nedostaly, cluster automaticky vypne. V Azure, jsou automaticky spravovány uzly počáteční hodnoty: jsou distribuovány prostřednictvím k dispozici doménách selhání a upgradu, pokud jeden počáteční uzel je odebrán z clusteru jiný bude vytvořena na příslušné místo. 

"Primární uzel typu" v samostatných clusterů Service Fabric a Azure, je ten, který spouští semen. Při definování typu primárního uzlu, Service Fabric se automaticky využít výhod počet uzlů zajištěna vytvořením až 9 uzly počáteční hodnoty a 9 repliky jednotlivých systémových služeb. Pokud sadu náhodné chyby používá současně se většina těchto replik služby systému, systémových služeb zadá ztráty kvora, jsme popsaný výše. Pokud Většina uzlů počáteční hodnoty jsou ztraceny, cluster bude vypnut krátce po.

## <a name="next-steps"></a>Další postup
- Zjistěte, jak k simulaci různé chyby pomocí [testovatelnosti framework](service-fabric-testability-overview.md)
- Přečtěte si další prostředky pro zotavení po havárii a vysoká dostupnost. Microsoft publikuje velké množství pokyny v těchto tématech. Při některé z těchto dokumentů naleznete konkrétní postupy pro použití v jiných produktech, obsahují mnoho obecné osvědčené postupy, které můžete použít v rámci Service Fabric:
  - [Kontrolní seznam k dostupnosti](../best-practices-availability-checklist.md)
  - [Provádění postupu zotavení po havárii](../sql-database/sql-database-disaster-recovery-drills.md)
  - [Zotavení po havárii a vysoká dostupnost pro aplikace Azure][dr-ha-guide]
- Informace o [možnostech podpory pro Service Fabric](service-fabric-support.md)

<!-- External links -->

[repair-partition-ps]: https://msdn.microsoft.com/library/mt163522.aspx
[azure-status-dashboard]:https://azure.microsoft.com/status/
[azure-regions]: https://azure.microsoft.com/regions/
[dr-ha-guide]: https://msdn.microsoft.com/library/azure/dn251004.aspx


<!-- Images -->

[sfx-cluster-map]: ./media/service-fabric-disaster-recovery/sfx-clustermap.png
