---
title: Obnovení zotavení po havárii azure service fabric
description: Azure Service Fabric nabízí funkce pro řešení katastrof. Tento článek popisuje typy katastrof, které mohou nastat a jak s nimi zacházet.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: b29985d40ae3a1bf582099e998e000fed83460f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371643"
---
# <a name="disaster-recovery-in-azure-service-fabric"></a>Zotavení po havárii ve službě Azure Fabric
Důležitou součástí poskytování vysoké dostupnosti je zajistit, aby služby mohly přežít všechny různé typy selhání. To je zvláště důležité pro selhání, která jsou neplánovaná a mimo vaši kontrolu. 

Tento článek popisuje některé běžné režimy selhání, které mohou být katastrofy, pokud nejsou modelovány a spravovány správně. Popisuje také skutečnosti snižující závažnost rizika a opatření, která je třeba provést, pokud dojde k havárii tak jako tak. Cílem je omezit nebo vyloučit riziko výpadku nebo ztráty dat, když dojde k selhání, plánované nebo jiné.

## <a name="avoiding-disaster"></a>Předcházení katastrofě
Hlavním cílem Azure Service Fabric je pomoci modelovat prostředí i služby takovým způsobem, že běžné typy selhání nejsou katastrofy. 

Obecně existují dva typy scénářů selhání a selhání:
- Poruchy hardwaru a softwaru
- Provozní závady

### <a name="hardware-and-software-faults"></a>Poruchy hardwaru a softwaru
Poruchy hardwaru a softwaru jsou nepředvídatelné. Nejjednodušší způsob, jak přežít chyby, je spuštění více kopií služby přes hranice hardwarových nebo softwarových poruch. 

Například pokud vaše služba běží pouze na jednom počítači, selhání tohoto jednoho počítače je katastrofa pro tuto službu. Jednoduchý způsob, jak se této katastrofě vyhnout, je zajistit, že služba je spuštěna na více počítačích. Testování je také nutné zajistit, že selhání jednoho počítače nenaruší spuštěnou službu. Plánování kapacity zajišťuje, že náhradní instance může být vytvořena jinde a snížení kapacity nepřetěžuje zbývající služby. 

Stejný vzor funguje bez ohledu na to, co se snažíte vyhnout selhání. Pokud se například obáváte selhání sítě SAN, narazíte na více sítě SAN. Pokud máte obavy ze ztráty racku serverů, narazíte na více racků. Pokud máte obavy ze ztráty datových center, vaše služba by měla běžet napříč několika oblastmi Azure, ve více zónách dostupnosti Azure nebo ve vašich vlastních datových centrech. 

Když je služba rozložena na více fyzických instancí (počítače, racky, datová centra, oblasti), stále se na vás vztahují některé typy souběžných selhání. Ale jeden a dokonce i více selhání určitého typu (například jeden virtuální počítač nebo síťové propojení selhání) jsou automaticky zpracovány, a tak již nejsou "katastrofa.". 

Service Fabric poskytuje mechanismy pro rozšíření clusteru a popisovače přináší selhání uzly a služby zpět. Service Fabric také umožňuje spuštění mnoha instancí vašich služeb, aby se zabránilo neplánovaným selháním, které by se změnily v skutečné katastrofy.

Může být důvod, proč spuštění nasazení dostatečně velké pro rozsah selhání není možné. Může například trvat více hardwarových prostředků, než jste ochotni zaplatit vzhledem k pravděpodobnosti selhání. Pokud máte co do činění s distribuovanými aplikacemi, další komunikační směrování nebo náklady na státní replikaci napříč zeměpisnými vzdálenostmi může způsobit nepřijatelnou latenci. Pokud je tato čára nakreslena se liší pro každou aplikaci. 

Konkrétně u softwarových chyb může být chyba ve službě, kterou se pokoušíte škálovat. V tomto případě další kopie nezabrání havárii, protože podmínka selhání je korelována ve všech instancích.

### <a name="operational-faults"></a>Provozní závady
I v případě, že vaše služba je rozložena po celém světě s mnoha propouštění, může stále dochází ke katastrofální události. Někdo může například omylem překonfigurovat název DNS pro službu nebo jej úplně odstranit. 

Řekněme například, že jste měli stavovou službu Service Fabric a někdo tuto službu omylem odstranil. Pokud není nějaké jiné zmírnění, že služba a všechny státu, že měl, jsou nyní pryč. Tyto typy provozních havárií ("jejda") vyžadují různé skutečnosti snižující závažnost rizika a kroky pro obnovení než běžné neplánované selhání. 

Nejlepším způsobem, jak se těmto typům provozních chyb vyhnout, je:
- Omezte provozní přístup k prostředí.
- Přísně audit nebezpečných operací.
- Ukláňte automatizaci, zabraňte ručním nebo nepásmovým změnám a ověřte konkrétní změny v prostředí před jejich přijetím.
- Ujistěte se, že destruktivní operace jsou "měkké". Měkké operace se neprojeví okamžitě nebo mohou být v časovém okně zpět.

Service Fabric poskytuje mechanismy, aby se zabránilo provozní chyby, jako je například poskytování řízení [přístupu na základě rolí](service-fabric-cluster-security-roles.md) pro operace clusteru. Většina těchto provozních chyb však vyžaduje organizační úsilí a další systémy. Service Fabric poskytuje mechanismy pro přežití provozních chyb, zejména [zálohování a obnovení pro stavové služby](service-fabric-backuprestoreservice-quickstart-azurecluster.md).

## <a name="managing-failures"></a>Správa chyb
Cílem service fabric je automatická správa selhání. Ale pro zpracování některých typů selhání, služby musí mít další kód. Jiné typy selhání by _neměly_ být automaticky řešeny z důvodů bezpečnosti a kontinuity provozu. 

### <a name="handling-single-failures"></a>Zpracování jednotlivých poruch
Jednotlivé počítače mohou selhat z nejrůznějších důvodů. Někdy je to hardwarové příčiny, jako jsou napájecí zdroje a selhání síťového hardwaru. Další chyby jsou v softwaru. Patří mezi ně selhání operačního systému a samotné služby. Service Fabric automaticky detekuje tyto typy selhání, včetně případů, kdy se počítač izoluje od jiných počítačů z důvodu problémů se sítí.

Bez ohledu na typ služby, spuštění jedné instance má za následek prostoje pro tuto službu, pokud tato jedna kopie kódu selže z jakéhokoli důvodu. 

Chcete-li zpracovat jakékoli jednotlivé selhání, nejjednodušší věc, kterou můžete udělat, je zajistit, aby vaše služby běží na více než jednom uzlu ve výchozím nastavení. U služeb bez stavů `InstanceCount` se ujistěte, že je větší než 1. Pro stavové služby je `TargetReplicaSetSize` minimální `MinReplicaSetSize` doporučení, že a jsou nastaveny na 3. Spuštění dalších kopií kódu služby zajišťuje, že vaše služba může automaticky zpracovat jakékoli selhání. 

### <a name="handling-coordinated-failures"></a>Manipulace s koordinovanými poruchami
Koordinované chyby v clusteru mohou být způsobeny plánovanými nebo neplánovanými chybami a změnami infrastruktury nebo plánovanými změnami softwaru. Service Fabric modely infrastruktury zóny, které dochází ke koordinovaným selháním jako *domény selhání*. Oblasti, ve kterých dojde ke koordinovaným změnám softwaru, jsou modelovány jako *upgradovací domény*. Další informace o doménách selhání, upgradovacích doménách a topologii clusteru naleznete [v tématu Describe a Service Fabric cluster using Cluster Resource Manager](service-fabric-cluster-resource-manager-cluster-description.md).

Service Fabric ve výchozím nastavení zvažuje chyby a upgradu domény při plánování, kde by měly být spuštěny služby. Ve výchozím nastavení service fabric se snaží zajistit, aby vaše služby běžet přes několik selhání a upgrade domény tak, aby v případě plánovaných nebo neplánovaných změn, služby zůstávají k dispozici. 

Řekněme například, že selhání zdroje napájení způsobí, že všechny počítače na stojanu selžou současně. Při spuštění více kopií služby se ztráta mnoha počítačů v selhání domény selhání změní na další příklad jednoho selhání služby. To je důvod, proč správa chyb a upgradu domén je rozhodující pro zajištění vysoké dostupnosti vašich služeb. 

Když v Azure používáte Service Fabric, domény selhání a upgradovací domény se spravují automaticky. V jiných prostředích nemusí být. Pokud vytváříte vlastní clustery místně, nezapomeňte správně mapovat a naplánovat rozložení domény selhání.

Upgradovací domény jsou užitečné pro modelování oblastí, kde bude software upgradován současně. Z tohoto důvodu upgradovací domény také často definují hranice, kde je software odstraněn během plánovaných upgradů. Upgrady service fabric a vaše služby postupujte podle stejného modelu. Další informace o postupných inovacích, upgradovacích doménách a modelu stavu Service Fabric, který pomáhá zabránit nechtěným změnám ovlivňujícím cluster a vaši službu, naleznete v následujících tématech:

 - [Upgrade aplikací](service-fabric-application-upgrade.md)
 - [Kurz pro upgrade aplikace](service-fabric-application-upgrade-tutorial.md)
 - [Model stavu service fabric](service-fabric-health-introduction.md)

Rozložení clusteru můžete vizualizovat pomocí mapy clusteru, která je k dispozici v [aplikaci Service Fabric Explorer](service-fabric-visualizing-your-cluster.md):

<center>

![Uzly rozprostřené mezi doménami selhání v Průzkumníku prostředků Service Fabric][sfx-cluster-map]
</center>

> [!NOTE]
> Modelování oblastí selhání, postupné upgrady, spuštění mnoha instancí kódu služby a stavu, pravidla umístění, aby bylo zajištěno, že vaše služby běží napříč doménami selhání a upgradu, a integrované monitorování stavu jsou jen *některé* z funkcí, které service fabric poskytuje, aby se normální provozní problémy a selhání neproměnily v katastrofy. 
>

### <a name="handling-simultaneous-hardware-or-software-failures"></a>Zpracování souběžných selhání hardwaru nebo softwaru
Mluvili jsme o jednotlivých neúspěších. Jak můžete vidět, jsou snadno ovladatelné pro bezstavové i stavové služby pouhým udržováním více kopií kódu (a stavu) běžících napříč doménami selhání a upgradu. 

Může se také stát více souběžných náhodných selhání. Ty s větší pravděpodobností povedou k prostojům nebo skutečné katastrofě.


#### <a name="stateless-services"></a>Služby bez státní příslušnosti
Počet instancí pro bezstavovou službu označuje požadovaný počet instancí, které je třeba spustit. Když se nezdaří některé (nebo všechny) instance, Service Fabric reaguje automatickým vytvořením náhradní instance na jiných uzlech. Service Fabric pokračuje ve vytváření náhrad, dokud se služba nevrátí na požadovaný počet instancí.

Předpokládejme například, že služba `InstanceCount` bez stavů má hodnotu -1. Tato hodnota znamená, že jedna instance by měla být spuštěna na každém uzlu v clusteru. Pokud některé z těchto instancí nezdaří, Service Fabric zjistí, že služba není v požadovaném stavu a pokusí se vytvořit instance na uzlech, kde chybí.

#### <a name="stateful-services"></a>Státem poskytované služby
Existují dva typy stavových služeb:
- Stavové s trvalým stavem.
- Stavový s netrvalým stavem. (Stav je uložen v paměti.)

Obnovení ze selhání stavové služby závisí na typu stavové služby, kolik replik služba měla a kolik replik se nezdařilo.

Ve stavové službě jsou příchozí data replikována mezi replikami (primární a všechny aktivní sekundární položky). Pokud většina replik přijímat data, data se považuje za *kvorum* potvrzena. (Pro pět replik budou tři kvorum.) To znamená, že v každém okamžiku bude existovat alespoň kvorum replik s nejnovějšími daty. Pokud repliky selžou (řekněme dva z pěti), můžeme použít hodnotu kvora k výpočtu, pokud se můžeme zotavit. (Vzhledem k tomu, že zbývající tři z pěti replik jsou stále nahoru, je zaručeno, že alespoň jedna replika bude mít úplná data.)

Pokud kvorum replik y nezdaří, oddíl je deklarován ve stavu *ztráty kvora.* Řekněme, že oddíl má pět replik, což znamená, že alespoň tři jsou zaručeny mít úplná data. Pokud kvorum (tři z pěti) replik nezdaří, Service Fabric nelze určit, pokud zbývající repliky (dva z pěti) mají dostatek dat k obnovení oddílu. V případech, kdy Service Fabric zjistí ztrátu kvora, jeho výchozí chování je zabránit další zápisy do oddílu, deklarovat ztrátu kvora a čekat na obnovení kvora replik.

Určení, zda došlo k pohromě pro stavové služby a potom ji spravuje následuje tři fáze:

1. Určení, zda došlo ke ztrátě kvora nebo ne.
   
   Ztráta kvora je deklarována, když většina replik stavové služby jsou ve stejnou dobu mimo provoz.
2. Určení, zda je ztráta kvora trvalá či nikoli.
   
   Většinou jsou chyby přechodné. Procesy jsou restartovány, uzly se restartují, virtuální počítače se znovu spustí a síťové oddíly se hojí. Někdy jsou však selhání trvalá. Zda jsou selhání trvalá nebo ne, závisí na tom, zda stavová služba přetrvává svůj stav nebo zda ji uchovává pouze v paměti: 
   
   - U služeb bez trvalého stavu má selhání kvora nebo více replik _okamžitě_ trvalé ztráty kvora. Když Service Fabric zjistí ztrátu kvora ve stavové netrvalé službě, okamžitě pokračuje ke kroku 3 deklarováním (potenciální) ztráty dat. Pokračování ke ztrátě dat má smysl, protože Service Fabric ví, že nemá smysl čekat na repliky vrátit. I v případě, že se obnoví, data budou ztracena z důvodu netrvalé povahy služby.
   - U stavových trvalých služeb selhání kvora nebo více replik způsobí, že service fabric čeká na návrat replik a obnovení kvora. To má za následek výpadku služby pro všechny _zápisy_ do ohroženého oddílu (nebo "replika set") služby. Čtení však může být stále možné se sníženou konzistencí záruky. Výchozí doba, po kterou service fabric čeká na obnovení kvora, je *nekonečná*, protože proceeding je (potenciální) událost ztráty dat a nese další rizika. To znamená, že Service Fabric nebude pokračovat k dalšímu kroku, pokud správce neprovede akci k deklarování ztráty dat.
3. Určení, zda dojde ke ztrátě dat, a obnovení ze záloh.

   Pokud byla deklarována ztráta kvora (automaticky nebo prostřednictvím administrativní akce), Service Fabric a služby přejít k určení, pokud data byla skutečně ztracena. V tomto okamžiku Service Fabric také ví, že ostatní repliky se nevrátí. To bylo rozhodnutí, které bylo učiněno, když jsme přestali čekat, až se ztráta kvora vyřeší sama. Nejlepší postup pro službu je obvykle zmrazit a čekat na konkrétní administrativní zásah.
   
   Při service fabric `OnDataLossAsync` volá metodu, je to vždy z důvodu _ztráty podezřelých_ dat. Service Fabric zajišťuje, že toto volání je doručena _nejlepší_ zbývající repliky. Toto je podle toho, která replika dosáhla největšího pokroku. 
   
   Důvod, proč vždy říkáme _podezření_ na ztrátu dat je, že je možné, že zbývající replika má stejný stav jako primární udělal, když došlo ke ztrátě kvora. Nicméně, bez tohoto stavu porovnat, neexistuje žádný dobrý způsob, jak service fabric nebo operátory vědět jistě.     
   
   Takže co dělá typická `OnDataLossAsync` implementace metody dělat?
   1. Protokoly implementace, `OnDataLossAsync` která byla spuštěna a spustí všechny nezbytné výstrahy správy.
   1. Implementace se obvykle pozastaví a čeká na další rozhodnutí a ruční akce, které mají být přijata. Důvodem je, že i v případě, že zálohy jsou k dispozici, může být nutné připravit. 
   
      Například pokud dvě různé služby koordinovat informace, tyto zálohy může být nutné upravit, aby zajistily, že po obnovení dojde, informace, které tyto dvě služby péče o je konzistentní. 
   1. Často existuje nějaká jiná telemetrie nebo výfukové plyny ze služby. Tato metadata mohou být obsažena v jiných službách nebo v protokolech. Tyto informace lze použít podle potřeby k určení, pokud byly všechny volání přijaté a zpracované na primární, které nebyly přítomny v záloze nebo replikovány do této konkrétní repliky. Tato volání může být nutné přehrát nebo přidat do zálohy před obnovení je možné.  
   1. Implementace porovnává stav zbývající repliky se stavem obsaženým ve všech dostupných zálohách. Pokud používáte Service Fabric spolehlivé kolekce, existují [nástroje a procesy](service-fabric-reliable-services-backup-restore.md) k dispozici pro to. Cílem je zjistit, zda je stav v rámci repliky dostatečný a zjistit, co může chybět záloha.
   1. Po porovnání a po dokončení obnovení (v případě potřeby), kód služby by měl vrátit **true,** pokud byly provedeny změny stavu. Pokud replika zjistila, že se jedná o nejlepší dostupnou kopii stavu a neprovedla žádné změny, vrátí kód **hodnotu false**. 
   
      Hodnota **true** označuje, že všechny _ostatní_ repliky může být nyní nekonzistentní s touto. Budou vynechány a znovu sestaveny z této repliky. Hodnota **false** označuje, že nebyly provedeny žádné změny stavu, takže ostatní repliky můžete zachovat to, co mají. 

Je kriticky důležité, aby autoři služeb praktikovat potenciální data ztráty a selhání scénáře před služby jsou nasazeny v produkčním prostředí. Chcete-li se chránit před možností ztráty dat, je důležité pravidelně [zálohovat stav](service-fabric-reliable-services-backup-restore.md) některé ze stavových služeb do geograficky redundantního úložiště. 

Musíte také zajistit, že máte možnost obnovit stav. Vzhledem k tomu, že zálohy mnoha různých služeb jsou prováděny v různých časech, je třeba zajistit, že po obnovení mají vaše služby konzistentní zobrazení navzájem. 

Zvažte například situaci, kdy jedna služba generuje číslo a ukládá ho a pak ji odešle do jiné služby, která ji také ukládá. Po obnovení můžete zjistit, že druhá služba má číslo, ale první nemá, protože jeho záloha nezahrnovala tuto operaci.

Pokud zjistíte, že zbývající repliky jsou nedostatečné pokračovat ve scénáři ztráty dat a nelze rekonstruovat stav služby z telemetrie nebo vyčerpání, četnost záloh určuje nejlepší možný cíl bodu obnovení (RPO). Service Fabric poskytuje mnoho nástrojů pro testování různých scénářů selhání, včetně trvalého kvora a ztráty dat, která vyžaduje obnovení ze zálohy. Tyto scénáře jsou zahrnuty jako součást nástrojů testovatelnosti v Service Fabric, spravované službou Fault Analysis Service. Další informace o těchto nástrojích a vzorech naleznete [v tématu Úvod do služby analýzy poruch](service-fabric-testability-overview.md). 

> [!NOTE]
> Systémové služby mohou také utrpět ztrátu kvora. Dopad je specifický pro danou službu. Například ztráta kvora ve službě pojmenování ovlivňuje překlad názvů, zatímco ztráta kvora ve službě Správce převzetí služeb při selhání blokuje vytváření nových služeb a převzetí služeb při selhání. 
> 
> Služby Service Fabric systému postupujte stejným vzorem jako služby pro správu stavu, ale nedoporučujeme, abyste se je pokusili přesunout ze ztráty kvora a do potenciální ztráty dat. Místo toho doporučujeme [vyhledat podporu](service-fabric-support.md) k nalezení řešení, které je zaměřeno na vaši situaci. Obvykle je vhodnější jednoduše počkat, až se repliky dolů vrátí.
>

#### <a name="troubleshooting-quorum-loss"></a>Řešení potíží se ztrátou kvora

Repliky mohou být nepravidelně z důvodu přechodné selhání. Počkejte nějakou dobu jako Service Fabric se snaží vychovávat je. Pokud repliky byly vypnuty po dobu delší než očekávaná doba trvání, postupujte podle těchto akcí řešení potíží:
- Repliky se mohou zřítit. Zkontrolujte sestavy stavu na úrovni repliky a protokoly aplikací. Shromážděte výpisy stavu a provést nezbytné kroky k obnovení.
- Proces repliky pravděpodobně přestal odpovídat. Zkontrolujte protokoly aplikací a ověřte to. Shromážděte výpisy procesů a zastavte proces neodpovídá. Service Fabric vytvoří proces nahrazení a pokusí se vrátit repliku zpět.
- Uzly, které jsou hostiteli repliky může být dolů. Restartujte základní virtuální počítač, abyste uzly zvýšili.

Někdy nemusí být možné obnovit repliky. Například jednotky selhaly nebo počítače fyzicky nereagují. V těchto případech service fabric musí být řečeno, ne čekat na obnovení repliky.

*Nepoužívejte* tyto metody, pokud potenciální ztráta dat je nepřijatelné, aby službu do režimu online. V takovém případě by mělo být vynaloženo veškeré úsilí k obnovení fyzické stroje.

Následující akce může mít za následek ztrátu dat. Zkontrolujte, než je budete sledovat.
   
> [!NOTE]
> _Nikdy_ není bezpečné používat tyto metody než cíleným způsobem proti konkrétním oddílům. 
>

- Použijte `Repair-ServiceFabricPartition -PartitionId` rozhraní `System.Fabric.FabricClient.ClusterManagementClient.RecoverPartitionAsync(Guid partitionId)` nebo API. Toto rozhraní API umožňuje určení ID oddílu přesunout ze ztráty kvora a do potenciální ztráty dat.
- Pokud váš cluster narazí na časté chyby, které způsobují, že služby přejdou do stavu ztráty kvora a potenciální _ztráta dat je přijatelná_, může zadání příslušné hodnoty [QuorumLossWaitDuration](https://docs.microsoft.com/powershell/module/servicefabric/update-servicefabricservice?view=azureservicefabricps) pomoci vaší službě automaticky obnovit. Service Fabric bude čekat `QuorumLossWaitDuration` na zadaný hodnotu (výchozí je nekonečný) před provedením obnovení. *Tuto metodu nedoporučujeme,* protože může způsobit neočekávané ztráty dat.

## <a name="availability-of-the-service-fabric-cluster"></a>Dostupnost clusteru Service Fabric
Obecně platí, že cluster Service Fabric je vysoce distribuované prostředí bez jediného místa selhání. Selhání jednoho uzlu nezpůsobí problémy s dostupností nebo spolehlivostí clusteru, především proto, že systémové služby Service Fabric postupujte podle stejných pokynů uvedených dříve. To znamená, že ve výchozím nastavení vždy běží se třemi nebo více replikami a systémovými službami, které jsou bezstavové, jsou spuštěny na všech uzlech. 

Základní vrstvy sítě Service Fabric a úrovně detekce selhání jsou plně distribuovány. Většinu systémových služeb lze znovu sestavit z metadat v clusteru nebo vědět, jak znovu synchronizovat jejich stav z jiných míst. Dostupnost clusteru může být ohrožena, pokud se systémové služby dostanou do situací ztráty kvora, jako jsou popsány výše. V těchto případech nemusí být možné provádět určité operace v clusteru (jako je spuštění upgradu nebo nasazení nových služeb), ale samotný cluster je stále funkční. 

Služby ve spuštěném clusteru budou v těchto podmínkách spuštěny, pokud nevyžadují zápisy do systémových služeb, aby mohly nadále fungovat. Například pokud správce převzetí služeb při selhání je ve ztrátě kvora, všechny služby budou nadále spuštěny. Ale všechny služby, které se nezdaří nebude moci automaticky restartovat, protože to vyžaduje zapojení správce služeb převzetí služeb při selhání. 

### <a name="failures-of-a-datacenter-or-an-azure-region"></a>Selhání datového centra nebo oblasti Azure
Ve výjimečných případech může být fyzické datové centrum dočasně nedostupné ze ztráty napájení nebo připojení k síti. V těchto případech vaše clustery service fabric a služby v tomto datovém centru nebo oblasti Azure nebude k dispozici. Data jsou však _zachována_. 

U clusterů spuštěných v Azure můžete zobrazit aktualizace výpadků na [stránce stavu Azure][azure-status-dashboard]. Ve vysoce nepravděpodobném případě, že fyzické datové centrum je částečně nebo zcela zničeno, může dojít ke ztrátě všech clusterů Service Fabric, které jsou v nich hostovány, nebo služeb uvnitř nich. Tato ztráta zahrnuje všechny stavy, které nejsou zálohovány mimo toto datové centrum nebo oblast.

Existují dvě různé strategie pro přežití trvalého nebo trvalého selhání jednoho datového centra nebo oblasti: 

- Spusťte samostatné clustery Service Fabric ve více takových oblastech a použijte určitý mechanismus pro převzetí služeb při selhání a navrácení služeb po selhání mezi těmito prostředími. Tento druh aktivního/aktivního nebo aktivního/pasivního modelu s více clustery vyžaduje další kód správy a operací. Tento model také vyžaduje koordinaci záloh ze služeb v jednom datovém centru nebo oblasti tak, aby byly k dispozici v jiných datových centrech nebo oblastech, když jeden selže. 
- Spusťte jeden cluster Service Fabric, který zahrnuje více datových center nebo oblastí. Minimální podporovaná konfigurace pro tuto strategii jsou tři datová centra nebo oblasti. Doporučený počet oblastí nebo datových center je pět. 
  
  Tento model vyžaduje složitější topologii clusteru. Výhodou je však selhání jednoho datového centra nebo oblasti je převeden z havárie do normální selhání. Tyto chyby mohou být zpracovány mechanismy, které pracují pro clustery v rámci jedné oblasti. Domény selhání, upgradovací domény a pravidla umístění Service Fabric zajišťují, že úlohy jsou distribuovány tak, aby tolerovaly normální selhání. 
  
  Další informace o zásadách, které mohou pomoci provozovat služby v tomto typu clusteru, naleznete v [tématu Placement policies for Service Fabric services](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md).

### <a name="random-failures-that-lead-to-cluster-failures"></a>Náhodná selhání, která vedou k selhání clusteru
Service Fabric má koncept *semenných uzlů*. Jedná se o uzly, které udržují dostupnost základního clusteru. 

Uzly osiva pomáhají zajistit, že cluster zůstane nahoru tím, že naváže zapůjčení s jinými uzly a slouží jako tiebreakers během určitých druhů selhání. Pokud náhodné selhání odeberou většinu semenných uzlů v clusteru a nejsou rychle přeneseny zpět, cluster se automaticky vypne. Cluster se pak nezdaří. 

V Azure spravuje zprostředkovatel prostředků Service Fabric konfigurace clusteru Service Fabric. Ve výchozím nastavení zprostředkovatel prostředků distribuuje uzly sesemeny mezi domény selhání a upgradu pro *primární typ uzlu*. Pokud je primární typ uzlu označen jako odolnost stříbra nebo zlata, při odebrání uzlu závěť (buď změnou velikosti v primárním typu uzlu, nebo ručním odebráním), cluster se pokusí povýšit jiný uzel bez semen z dostupné kapacity primárního typu uzlu. Tento pokus se nezdaří, pokud máte menší dostupnou kapacitu, než vyžaduje úroveň spolehlivosti clusteru pro primární typ uzlu.

V obou samostatných clusterů Service Fabric a Azure, primární typ uzlu je ten, který spouští semena. Při definování typu primárního uzlu service fabric automaticky využije počet uzlů poskytovaných vytvořením až devíti osivových uzlů a sedmi replik každé systémové služby. Pokud sada náhodných selhání převezme většinu těchto replik současně, systémové služby zadají ztrátu kvora. Pokud dojde ke ztrátě většiny uzlin, cluster se brzy vypne.

## <a name="next-steps"></a>Další kroky
- Zjistěte, jak simulovat různé chyby pomocí [architektury testovatelnosti](service-fabric-testability-overview.md).
- Přečtěte si další zdroje zotavení po havárii a vysoké dostupnosti. Společnost Microsoft publikovala velké množství pokynů k těmto tématům. Přestože některé z těchto prostředků odkazují na konkrétní techniky pro použití v jiných produktech, obsahují mnoho obecných doporučených postupů, které lze použít v kontextu Service Fabric:
  - [Kontrolní seznam k dostupnosti](/azure/architecture/checklist/resiliency-per-service)
  - [Provedení cvičení zotavení po havárii](../sql-database/sql-database-disaster-recovery-drills.md)
  - [Zotavení po havárii a vysoká dostupnost pro aplikace Azure][dr-ha-guide]
- Další informace o [možnostech podpory service fabric](service-fabric-support.md).


<!-- External links -->

[repair-partition-ps]: https://msdn.microsoft.com/library/mt163522.aspx
[azure-status-dashboard]:https://azure.microsoft.com/status/
[azure-regions]: https://azure.microsoft.com/regions/
[dr-ha-guide]: https://msdn.microsoft.com/library/azure/dn251004.aspx


<!-- Images -->

[sfx-cluster-map]: ./media/service-fabric-disaster-recovery/sfx-clustermap.png
