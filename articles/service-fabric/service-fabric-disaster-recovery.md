---
title: Zotavení po havárii Azure Service Fabric
description: Azure Service Fabric nabízí funkce potřebné k řešení všech typů katastrof. Tento článek popisuje typy havárií, ke kterým může dojít, a o tom, jak s nimi pracovat.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: f9bde0f81dc364aaa09dc9763f2014d83f992371
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78298287"
---
# <a name="disaster-recovery-in-azure-service-fabric"></a>Zotavení po havárii v Azure Service Fabric
Kritická součást poskytování vysoké dostupnosti zajišťuje, aby služby mohly zaručovat všechny různé typy selhání. To je obzvláště důležité pro neplánované chyby a mimo vaši kontrolu. Tento článek popisuje některé běžné režimy selhání, které by mohly selhat, pokud není modelem a spravováno správně. Také se zabývá riziky a akcemi, které se mají provést, pokud se přesto stala havárie. Cílem je omezit nebo odstranit riziko výpadku nebo ztráty dat, když dojde k chybám, plánovanému nebo jinému výskytu.

## <a name="avoiding-disaster"></a>Předcházení havárii
Primárním cílem Service Fabric je pomáhat při modelování vašeho prostředí i služeb tak, aby běžné typy selhání nebyly katastrofy. 

Obecně existují dva typy scénářů havárie/selhání:

1. Chyby hardwaru nebo softwaru
2. Provozní chyby

### <a name="hardware-and-software-faults"></a>Chyby hardwaru a softwaru
Chyby hardwaru a softwaru je nepředvídatelné. Nejjednodušší způsob, jak zablokovat chyby, je spuštění více kopií služby rozložené napříč hardwarovými nebo softwarovými hranicemi selhání. Například pokud vaše služba běží jenom na jednom konkrétním počítači, pak je selhání tohoto jednoho počítače u této služby. Jednoduchý způsob, jak se tomuto selhání vyhnout, je zajistit, aby služba byla ve skutečnosti spuštěná na více počítačích. Testování je také nutné k tomu, aby bylo jisté, že selhání jednoho počítače nenarušilo běžící službu. Plánování kapacity zajišťuje, aby se náhradní instance vytvořila jinde a aby snížení kapacity nepřetížit zbývající služby. Stejný vzor funguje bez ohledu na to, co se snažíte vyhnout selhání. Například. Pokud máte obavy o selhání sítě SAN, spustíte na více sítích San. Pokud máte obavy o ztrátu stojanu serverů, můžete spustit ve více skříních. Pokud máte obavy o ztrátu datových center, měla by služba běžet v několika oblastech Azure nebo datových centrech. 

Při spuštění v tomto typu rozloženého režimu pořád vycházíte z některých typů souběžných selhání, ale jedno a dokonce několik selhání určitého typu (např. jeden virtuální počítač nebo síťové propojení se nedaří) se automaticky zpracovávají (a tak už nejedná o "havárií"). Service Fabric poskytuje spoustu mechanismů pro rozšíření clusteru a zpracování back-v uzlech a službách, které selhaly. Service Fabric taky umožňuje spouštět mnoho instancí vašich služeb, aby se předešlo těmto typům neplánovaných selhání při zapínání na reálné katastrofy.

Mohou nastat důvody, proč je spuštění nasazení dostatečně velké pro rozsah selhání neproveditelné. Například může trvat víc hardwarových prostředků, než jste ochotni platit za relativní k výpadku. Při práci s distribuovanými aplikacemi by mohlo dojít k tomu, že další přesměrování komunikace nebo náklady na replikaci stavu mezi geografickými vzdálenostmi způsobují nepřijatelnou latenci. Kde se tento řádek vykreslí, se pro každou aplikaci liší. V případě chyb softwaru může být chyba ve službě, kterou se pokoušíte škálovat. V tomto případě více kopií nebrání v případě havárie, protože stav selhání je koreluje napříč všemi instancemi.

### <a name="operational-faults"></a>Provozní chyby
I když je vaše služba rozložená na celém světě s mnoha redundancemi, může stále docházet k katastrofální důsledkym událostem. Například pokud někdo omylem překonfiguruje název DNS pro danou službu nebo ho odstraní hned. Řekněme například, že jste měli stavovou Service Fabric službu a někdo tuto službu odstranil omylem. Pokud se nejedná o nějaké jiné zmírnění, je tato služba a všechny její stavy teď pryč. Tyto typy provozních katastrof ("ne") vyžadují jiné zmírňování a kroky pro obnovení než běžné neplánované chyby. 

Nejlepším způsobem, jak se vyhnout těmto typům provozních chyb, je
1. omezení provozního přístupu k prostředí
2. striktně auditovat nebezpečné operace
3. zavedení automatizace, zabránění ručnímu nebo nedostatku změn a ověření specifických změn před vlastním prostředím
4. Ujistěte se, že jsou destruktivní operace "měkké". Měkké operace se projeví okamžitě nebo je můžete v některých časových intervalech zrušit.

Service Fabric poskytuje některé mechanismy, které zabraňují provozním chybám, jako je například poskytování řízení přístupu [na základě rolí](service-fabric-cluster-security-roles.md) pro operace clusteru. Většina těchto provozních chyb ale vyžaduje organizační úsilí a další systémy. Service Fabric poskytuje určitý mechanismus pro zbývající provozní chyby, zejména zálohování a obnovení stavových služeb.

## <a name="managing-failures"></a>Správa selhání
Cílem Service Fabric je téměř vždy Automatická správa selhání. Za účelem zpracování některých typů selhání však služby musí mít další kód. Jiné typy _selhání by se neměly automaticky_ adresovat z důvodů bezpečnosti a provozní kontinuity. 

### <a name="handling-single-failures"></a>Zpracování jednoduchých selhání
U jednoho počítače může selhat z nejrůznějších důvodů. Některé z nich jsou příčiny hardwaru, jako jsou napájení a chyby hardwaru sítě. Další chyby jsou v softwaru. Mezi ně patří selhání skutečného operačního systému a samotné služby. Service Fabric automaticky detekuje tyto typy selhání, včetně případů, kdy se počítač od jiných počítačů izoluje kvůli problémům se sítí.

Bez ohledu na typ služby způsobí spuštění jedné instance výpadky této služby, pokud tato jediná kopie kódu z nějakého důvodu se nezdařila. 

Aby se zajistila jediná chyba, nejjednodušší věc, kterou můžete udělat, je zajistit, aby vaše služby ve výchozím nastavení běžely ve více než jednom uzlu. U bezstavových služeb to můžete provést pomocí `InstanceCount` větší než 1. U stavových služeb je minimální doporučení vždy `TargetReplicaSetSize` a `MinReplicaSetSize` nejméně 3. Spuštění více kopií kódu služby zajistí, že vaše služba bude automaticky zpracovávat každou jednotlivou chybu. 

### <a name="handling-coordinated-failures"></a>Zpracování koordinovaných selhání
K koordinovaným chybám může dojít v clusteru z důvodu plánovaného nebo neplánovaného selhání infrastruktury a změn nebo plánované změny softwaru. Service Fabric modely infrastruktury, které se zachováním koordinovaných selhání jako domén selhání. Oblasti, ve kterých se projeví koordinované změny softwaru, se modelují jako upgradovací domény. Další informace o chybách a upgradovacích doménách najdete v [tomto dokumentu](service-fabric-cluster-resource-manager-cluster-description.md) , který popisuje topologii a definici clusteru.

Ve výchozím nastavení Service Fabric při plánování, kde by měly vaše služby běžet, vyvažovat domény při selhání a upgrady. Ve výchozím nastavení se Service Fabric snaží zajistit, aby se vaše služby spouštěly v několika selháních a upgradovacích doménách, takže pokud dojde k plánovaným nebo neplánovaným změnám, zůstanou vaše služby dostupné. 

Řekněme například, že selhání zdroje napájení způsobí selhání stojanu počítačů současně. U více kopií služby, na kterých je spuštěná ztráta velkého množství počítačů v doméně selhání, se u dané služby změní jenom na další příklad jediného selhání. Důvodem je, že správa domén selhání je zásadní pro zajištění vysoké dostupnosti vašich služeb. Při spuštění Service Fabric v Azure se domény selhání spravují automaticky. V jiných prostředích nemusí být. Pokud vytváříte vlastní clustery v místním prostředí, nezapomeňte správně namapovat a naplánovat rozložení domén selhání.

Domény upgradu jsou užitečné pro oblasti modelování, ve kterých se bude software upgradovat současně. Z tohoto důvodu také domény upgradu často definují hranice, při kterých se software během plánovaných upgradů vypíná. Upgrade obou Service Fabric a služeb se řídí stejným modelem. Další informace o postupných inovacích, upgradovacích doménách a modelu stavu Service Fabric, který pomáhá zabránit nezamýšleným změnám v ovlivnění clusteru a služby, najdete v těchto dokumentech:

 - [Upgrade aplikace](service-fabric-application-upgrade.md)
 - [Kurz upgradu aplikace](service-fabric-application-upgrade-tutorial.md)
 - [Service Fabric model stavu](service-fabric-health-introduction.md)

Rozložení clusteru můžete vizualizovat pomocí mapy clusteru poskytované v [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md):

<center>

Uzly ![se v různých doménách selhání rozšíří v Service Fabric Explorer][sfx-cluster-map]
</center>

> [!NOTE]
> Modelovací oblasti selhání, postupné upgrady, spuštění mnoha instancí kódu a stavu služby, pravidla umístění, aby se zajistilo, že se vaše služby spouštějí napříč doménami selhání a upgradu, a integrované monitorování stavu jsou jenom **některé** z funkcí, které Service Fabric poskytují, aby se zachovaly normální provozní problémy a chyby při zapínání na katastrofy. 
>

### <a name="handling-simultaneous-hardware-or-software-failures"></a>Zpracování současných hardwarových nebo softwarových selhání
Nad námi jsme mluvilii o jednom selhání. Jak vidíte, můžete se snadno zvládnout pro bezstavové a stavové služby pouze tím, že zachováte více kopií kódu (a stavu), které jsou spuštěny v rámci selhání a upgradovacích domén. Může se taky vyskytnout víc souběžných náhodných selhání. Je pravděpodobnější, že by to vedlo k skutečné havárii.


### <a name="random-failures-leading-to-service-failures"></a>Náhodné chyby vedoucí k selhání služby

#### <a name="stateless-services"></a>Bezstavové služby
`InstanceCount` pro službu indikují požadovaný počet instancí, které musí být spuštěny. Pokud dojde k selhání jakékoli (nebo všech) instancí, Service Fabric odpoví automatickým vytvořením náhradních instancí na jiných uzlech. Service Fabric bude pokračovat ve vytváření nahrazení až do chvíle, kdy se služba vrátí do požadovaného počtu instancí.
Dalším příkladem je, že Bezstavová služba má `InstanceCount`-1, což znamená, že jedna instance by měla být spuštěna na každém uzlu v clusteru. Pokud se některé z těchto instancí nezdařily, Service Fabric zjistí, že služba není v požadovaném stavu, a pokusí se vytvořit instance v uzlech, kde chybí.

#### <a name="stateful-services"></a>Stavové služby
Existují dva typy stavových služeb:
1. Stav s trvalým stavem
2. Stav bez trvalého stavu (stav je uložen v paměti)

Obnova stavové služby závisí na typu stavové služby a počtu replik, které měla služba a kolik se nezdařila.

Co je kvorum?
Ve stavové službě se příchozí data replikují mezi replikami (primární a ActiveSecondary). Pokud většina replik obdrží data, považují se za potvrzená data kvora (pro 5 replik, 3 bude _kvorum_). To znamená, že v libovolném okamžiku je zaručeno, že bude k dispozici alespoň kvorum replik s nejnovějšími daty. Pokud se repliky nezdaří (řekněme 2 z 5 replik), můžeme použít hodnotu kvora k výpočtu, jestli můžeme provést obnovení (vzhledem k tomu, že zbývající 3 z 5 replik jsou stále v provozu, je zaručeno, že nejméně jedna replika bude mít úplná data).

Co je ztráta kvora?
V případě selhání kvora repliky se oddíl deklaruje jako ve stavu ztráty kvora. Řekněme, že oddíl má 5 replik, což znamená, že nejméně 3 mají zaručená úplná data. Pokud kvorum (3.5) replik selže, Service Fabric nemůže určit, jestli zbývající repliky (2 z 5) mají dostatek dat pro obnovení oddílu.

Určení toho, jestli došlo k havárii pro stavovou službu a Správa se skládá ze tří fází:

1. Zjištění, zda došlo ke ztrátě kvora nebo ne
    - Ztráta kvora je deklarována v případě, že většina replik stavové služby je mimo provoz.
2. Zjištění, zda je ztráta kvora trvalá nebo ne
   - Ve většině případů jsou chyby přechodné. Procesy se restartují, uzly se restartují, virtuální počítače se znovu spouštějí a zamění se síťové oddíly. Někdy ale chyby jsou trvalé. 
     - U služeb bez trvalého stavu dojde k selhání kvora nebo většího počtu replik _okamžitě_ v trvalé ztrátě kvora. Pokud Service Fabric zjistí ztrátu kvora ve stavové netrvalé službě, bude okamžitě pokračovat krokem 3 tím, že deklaruje (možná) ztrátu dat. Pokračování na ztrátu dat dává smysl, protože Service Fabric ví, že není potřeba čekat na to, že se repliky vrátí, protože i v případě, že byly obnoveny kvůli netrvalému charakteru služby, došlo ke ztrátě dat.
     - U stavových trvalých služeb způsobí selhání kvora nebo většího počtu replik Service Fabric počkat, než se repliky vrátí a obnoví kvorum. Výsledkem je výpadek služby pro jakékoli _zápisy_ do ovlivněného oddílu (neboli sady replik) služby. Nicméně je možné, že čtení je stále možné se sníženými zárukami konzistence. Výchozí doba, po kterou Service Fabric čeká na obnovení kvora, je nekonečná, protože se jedná o (možná) událost ztráty dat a přináší další rizika.
3. Určení, zda došlo ke ztrátě skutečných dat a obnovení ze záloh
   
   Když Service Fabric volá metodu `OnDataLossAsync`, je vždy z důvodu _podezřelé_ ztráty dat. Service Fabric zajistí, že se toto volání doručí do _nejlepší_ zbývající repliky. To je to, že replika provedla nejvíce. Důvodem, kdy vždycky říkáme _podezření_ na ztrátu dat, je to, že zbývající replika ve skutečnosti má stejný stav jako primární v době, kdy došlo k výpadku. Nicméně bez tohoto stavu pro porovnání s, neexistuje žádný dobrý způsob, jak Service Fabric nebo operátory znát, zda jsou. V tomto okamžiku Service Fabric taky ví, že se ostatní repliky nevrátí. To bylo rozhodnutí, které bylo provedeno, když jsme zastavili čekání na vyřešení ztráty kvora. Nejlepší akci pro službu je obvykle zablokovat a čekat na konkrétní zásah správce. Co to dělá Typická implementace `OnDataLossAsync` metody?
   1. Nejprve protokolujte, že `OnDataLossAsync` aktivované, a vypínejte všechny nezbytné výstrahy správy.
   1. Obvykle se v tomto okamžiku dá pozastavit a počkat na další rozhodnutí a ruční akce, které se mají provést. Důvodem je to, že i když jsou k dispozici zálohy, může být potřeba je připravit. Pokud například dvě různé služby koordinují informace, může být nutné upravit tyto zálohy, aby se zajistilo, že po obnovení dojde k tomu, že informace, o kterých tyto dvě služby záleží, jsou konzistentní. 
   1. Z této služby se často vyskytuje taky nějaká další telemetrie nebo výfukové služba. Tato metadata mohou být obsažena v jiných službách nebo v protokolech. Tyto informace se dají použít k určení, jestli se na primárním místě nedostala žádná volání, která se neobjevila v záloze nebo replikují do této konkrétní repliky. Aby bylo možné obnovení provést, může být nutné přehrajte nebo přidat do zálohy.  
   1. Porovnání stavu zbývající repliky do, který je obsažený ve všech zálohách, které jsou k dispozici. Pokud používáte Service Fabric Reliable Collections, jsou k dispozici nástroje a procesy, které jsou popsané v [tomto článku](service-fabric-reliable-services-backup-restore.md). Cílem je zjistit, jestli je stav v replice dostatečný, nebo taky to, co může zálohování chybět.
   1. Po provedení porovnání a v případě potřeby obnovení by kód služby měl vracet hodnotu true, pokud byly provedeny nějaké změny stavu. Pokud replika zjistila, že se jednalo o nejlepší dostupnou kopii stavu a neudělala žádné změny, vrátí hodnotu false. Hodnota true označuje, že všechny _ostatní_ zbývající repliky teď můžou být nekonzistentní s tímto. Budou z této repliky vyřazeny a znovu sestaveny. Hodnota false znamená, že se neudělaly žádné změny stavu, takže ostatní repliky můžou zachovat, co mají. 

Je důležité, aby tvůrci služeb zvykli na potenciální ztráty dat a scénáře selhání předtím, než se služby v produkčním prostředí někdy nasazují. Aby se zabránilo ochraně před ztrátou dat, je důležité pravidelně [zálohovat stav](service-fabric-reliable-services-backup-restore.md) jakékoli stavové služby na geograficky redundantní úložiště. Musíte také zajistit, aby bylo možné ji obnovit. Vzhledem k tomu, že se zálohují spousty různých služeb v různou dobu, je nutné zajistit, aby po obnovení služeb byly vzájemně konzistentní zobrazení. Představte si třeba situaci, kdy jedna služba vygeneruje číslo a uloží ji, a pošle ji do jiné služby, která je také ukládá. Po obnovení se můžete setkat s tím, že druhá služba má číslo, ale první z nich ne, protože zálohování nezahrnovalo tuto operaci.

Pokud zjistíte, že zbývající repliky nestačí, aby pokračovaly ve scénáři ztráty dat, a nemůžete rekonstruovat stav služby z telemetrie nebo výfukového modulu, určí frekvence zálohování nejlepší možný cíl bodu obnovení (RPO). Service Fabric poskytuje mnoho nástrojů pro testování různých scénářů selhání, včetně trvalého kvora a ztráty dat, které vyžadují obnovení ze zálohy. Tyto scénáře jsou součástí testovacích nástrojů Service Fabric, které spravuje služba pro analýzu chyb. Další informace o těchto nástrojích a vzorcích najdete [tady](service-fabric-testability-overview.md). 

> [!NOTE]
> Systémové služby mohou také snížit ztrátu kvora, přičemž dopad je specifický pro příslušnou službu. Například ztráta kvora ve službě pojmenování má vliv na překlad názvů, zatímco ztráta kvora ve službě Správce převzetí služeb při selhání blokuje nové vytvoření a převzetí služeb při selhání. I když jsou systémové služby Service Fabric stejné vzoru jako vaše služby pro správu stavu, nedoporučuje se, abyste se pokusili je přesunout ze ztráty kvora a potenciální ztráty dat. Doporučení je místo toho, abyste [vyhledali podporu](service-fabric-support.md) pro určení řešení zaměřeného na konkrétní situaci.  Obvykle je vhodnější, abyste počkali na to, dokud nevrátí repliky.
>

#### <a name="troubleshooting-quorum-loss"></a>Řešení potíží s ztrátou kvora

V důsledku přechodného selhání mohou být repliky nepřerušované. Počkejte nějakou dobu, než se Service Fabric pokusí je přenést. Pokud se repliky neprovozují déle než očekávanou dobu trvání, postupujte prosím podle následujících kroků pro řešení potíží.
- Repliky mohou selhat. Kontroluje sestavy stavu repliky a protokoly aplikací. Shromážděte výpisy stavu systému a proveďte potřebné akce k obnovení.
- Proces repliky mohl přestat reagovat. Zkontrolujte protokoly aplikací, abyste ověřili tuto kontrolu. Shromážděte výpis procesu a potom ukončí nereagující proces. Service Fabric vytvoří náhradní proces a pokusí se repliku přenést zpátky.
- Uzly hostující repliky můžou být mimo provoz. Pokud chcete uzly přenést, restartujte příslušný virtuální počítač.

Mohou nastat situace, kdy není možné obnovit repliky, například jednotky se pravděpodobně nezdařily nebo fyzické nereagující počítače. V těchto případech Service Fabric nutné, aby nečekaly na obnovení repliky.
Nepoužívejte tyto metody, pokud potenciální ztráta dat není přijatelná, aby se služba mohla převést do online režimu v takovém případě by mělo být provedeno veškeré úsilí na obnovu fyzických počítačů.

Následující kroky by mohly vést ke ztrátě informací – Ujistěte se prosím, že je nebudete potřebovat:
   
> [!NOTE]
> Použití těchto metod, které nejsou cíleny na konkrétní oddíly, není _nikdy_ bezpečné. 
>

- Použijte `Repair-ServiceFabricPartition -PartitionId` nebo `System.Fabric.FabricClient.ClusterManagementClient.RecoverPartitionAsync(Guid partitionId)` API. Toto rozhraní API umožňuje zadat ID oddílu, který se má obnovit z QuorumLoss, s možnou ztrátou dat.
- Pokud má váš cluster často časté selhání, které způsobuje, že služby přecházejí do stavu ztráty kvora a může dojít _ke ztrátě dat_, pak zadáním příslušné hodnoty [QuorumLossWaitDuration](https://docs.microsoft.com/powershell/module/servicefabric/update-servicefabricservice?view=azureservicefabricps) může pomáhat vaší službě Automatické obnovení. Service Fabric bude před provedením obnovení čekat na zadaný QuorumLossWaitDuration (výchozí hodnota je nekonečno). Tato metoda se _nedoporučuje_ , protože to může způsobit neočekávané ztráty dat.

## <a name="availability-of-the-service-fabric-cluster"></a>Dostupnost clusteru Service Fabric
Obecně řečeno je Cluster Service Fabric sám o sobě vysoce distribuovaným prostředím bez jednoho bodu selhání. Selhání žádného uzlu nebude způsobovat problémy s dostupností nebo spolehlivostí pro cluster, primárně proto, že Service Fabric systémové služby se řídí stejnými pokyny, které jsou k dispozici v předchozích krocích: ve výchozím nastavení se vždy spouštějí se třemi nebo více replikami a těmito systémy. služby, které jsou spuštěny bez stavu na všech uzlech. Základní Service Fabric sítě a vrstvy detekce selhání jsou plně distribuované. Většinu systémových služeb lze znovu vytvořit z metadat v clusteru nebo zjistit, jak znovu synchronizovat svůj stav z jiných míst. Pokud se systémové služby dostanou do situace, kdy jsou popsané výše, může dojít k ohrožení dostupnosti clusteru. V těchto případech možná nebudete moci na clusteru provádět určité operace, jako je třeba spuštění upgradu nebo nasazení nových služeb, samotný cluster ale zůstane v provozu. Služby, které jsou už spuštěné, zůstanou v těchto podmínkách spuštěné, pokud nebudou vyžadovat zápis do systémových služeb, aby fungovaly i nadále. Pokud je Správce převzetí služeb při selhání například v případě ztráty kvora, budou všechny služby nadále spuštěné, ale jakékoli služby, které selžou, nebude možné automaticky restartovat, protože to vyžaduje zapojení Správce převzetí služeb při selhání. 

### <a name="failures-of-a-datacenter-or-azure-region"></a>Selhání datového centra nebo oblasti Azure
Ve výjimečných případech může být fyzické datové centrum dočasně nedostupné kvůli výpadku napájení nebo připojení k síti. V těchto případech nebudou vaše Service Fabric clustery a služby v tomto datovém centru nebo v oblasti Azure k dispozici. _Vaše data jsou ale zachovaná_. U clusterů, které běží v Azure, můžete na [stránce stavu Azure][azure-status-dashboard]zobrazit aktualizace na výpadky. V velmi nepravděpodobném případě, že je fyzické datacentrum částečně nebo zcela zničené, může dojít ke ztrátě všech Service Fabricch hostovaných clusterů nebo služeb, které jsou v nich umístěné. To zahrnuje jakýkoli stav, který není zálohovaný mimo toto datacentrum nebo oblast.

Existují dvě různé strategie pro udržení trvalého nebo trvalého selhání jednoho datacentra nebo oblasti. 

1. Spouštějte samostatné clustery Service Fabric v několika takových oblastech a využijte nějaký mechanismus pro převzetí služeb při selhání a navrácení služeb po obnovení mezi těmito prostředími. Tento model modelu aktivní-aktivní nebo aktivní-pasivní pro více clusterů vyžaduje další kód pro správu a provoz. Tato operace také vyžaduje koordinaci záloh ze služeb v jednom datovém centru nebo oblastech, aby byly k dispozici v jiných datových centrech nebo oblastech, pokud jedna z nich dojde k chybě. 
2. Spusťte jeden Service Fabric cluster, který pokrývá více datových center nebo oblastí. Minimální podporovaná konfigurace je tři datová centra nebo oblasti. Doporučený počet oblastí nebo datových Center je pět. To vyžaduje složitější topologii clusteru. Výhodou tohoto modelu ale je, že selhání jednoho datacentra nebo oblasti se převede z havárie do normálního selhání. Tyto chyby můžete zpracovat pomocí mechanismů, které fungují pro clustery v rámci jedné oblasti. Domény selhání, upgradované domény a pravidla pro umístění Service Fabric zajišťují, že úlohy jsou distribuované, aby tolerovány normální selhání. Další informace o zásadách, které mohou pomáhat při provozu služeb v tomto typu clusteru, najdete v tématu [Zásady umístění](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md) .

### <a name="random-failures-leading-to-cluster-failures"></a>Náhodné chyby vedoucí k selhání clusteru
Service Fabric má koncept počátečních uzlů. Jedná se o uzly, které udržují dostupnost podkladového clusteru. Tyto uzly pomůžou zajistit, že cluster zůstane v platnosti, a to tak, že se zapůjčí s ostatními uzly a během určitých druhů selhání sítě zaručí jako tiebreakers. Pokud náhodné chyby odeberou většinu uzlů počátečního prostředí v clusteru a nevrátí se zpátky, váš federační federační cluster se sbalí, protože jste ztratili kvorum uzlu s počátečními uzly a cluster se nezdaří. V Azure Service Fabric poskytovatel prostředků spravuje Service Fabric konfiguracích clusterů a ve výchozím nastavení distribuuje v rámci primárního typu uzlu selhání a upgradovací domény uzly s osivem. Pokud je primární NodeType označený jako stříbrná nebo zlatá, odeberete-li uzel počáteční hodnoty pomocí škálování v primárním NodeType nebo ručně odeberete uzel počáteční hodnoty, cluster se pokusí zvýšit úroveň jiného nepočátečního uzlu z primárního uzlu NodeType, který je k dispozici. kapacita a selže, pokud máte méně dostupnou kapacitu, než je úroveň spolehlivosti clusteru nutná pro primární typ uzlu.

V samostatných Service Fabric clusterech a v Azure je "primární typ uzlu" ten, který spouští semena. Při definování typu primárního uzlu bude Service Fabric automaticky využívat počet uzlů poskytnutý vytvořením až 9 počátečních uzlů a 7 replik každé systémové služby. Pokud sada náhodných selhání převezme většinu těchto replik systémových služeb současně, zadají systémové služby ztrátu kvora, jak je popsáno výše. Pokud dojde ke ztrátě většiny počátečních uzlů, cluster se brzy ukončí.

## <a name="next-steps"></a>Další kroky
- Informace o simulaci různých selhání pomocí [architektury testování](service-fabric-testability-overview.md)
- Přečtěte si další prostředky pro zotavení po havárii a vysokou dostupnost. Společnost Microsoft publikovala v těchto tématech velké množství pokynů. I když některé z těchto dokumentů odkazují na konkrétní techniky pro použití v jiných produktech, obsahují mnoho obecných osvědčených postupů, které můžete použít také v kontextu Service Fabric:
  - [Kontrolní seznam k dostupnosti](/azure/architecture/checklist/resiliency-per-service)
  - [Postup při provádění postupu zotavení po havárii](../sql-database/sql-database-disaster-recovery-drills.md)
  - [Zotavení po havárii a vysoká dostupnost pro aplikace Azure][dr-ha-guide]
- Informace o [možnostech podpory pro Service Fabric](service-fabric-support.md)


<!-- External links -->

[repair-partition-ps]: https://msdn.microsoft.com/library/mt163522.aspx
[azure-status-dashboard]:https://azure.microsoft.com/status/
[azure-regions]: https://azure.microsoft.com/regions/
[dr-ha-guide]: https://msdn.microsoft.com/library/azure/dn251004.aspx


<!-- Images -->

[sfx-cluster-map]: ./media/service-fabric-disaster-recovery/sfx-clustermap.png
