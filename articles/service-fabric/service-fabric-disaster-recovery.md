---
title: Zotavení po havárii Azure Service Fabric
description: Azure Service Fabric nabízí funkce pro řešení havárií. Tento článek popisuje typy havárií, ke kterým může dojít, a o tom, jak s nimi pracovat.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 8d99b4d1fbf227d850de387b7ca24dcd3fd40646
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98791151"
---
# <a name="disaster-recovery-in-azure-service-fabric"></a>Zotavení po havárii v Azure Service Fabric
Důležitou součástí poskytování vysoké dostupnosti je zajištění, že služby mohou zachovány všechny různé typy selhání. To je obzvláště důležité pro neplánované chyby a mimo váš ovládací prvek. 

Tento článek popisuje některé běžné režimy selhání, které mohou nastat při nesprávném modelování a spravování. Také se zabývá riziky a akcemi, které je třeba provést, pokud dojde k havárii. Cílem je omezit nebo odstranit riziko výpadku nebo ztráty dat, pokud dojde k selhání, plánovaná nebo jinak.

## <a name="avoiding-disaster"></a>Předcházení havárii
Hlavním cílem Azure Service Fabric je pomáhat při modelování vašeho prostředí i služeb tak, aby běžné typy selhání nebyly katastrofy. 

Obecně existují dva typy scénářů havárie/selhání:
- Chyby hardwaru a softwaru
- Provozní chyby

### <a name="hardware-and-software-faults"></a>Chyby hardwaru a softwaru
Chyby hardwaru a softwaru je nepředvídatelné. Nejjednodušší způsob, jak zablokovat chyby, je spuštění více kopií služby napříč hardwarovými nebo softwarovými hranicemi selhání. 

Například pokud je vaše služba spuštěná jenom v jednom počítači, jedná se o selhání tohoto jednoho počítače pro tuto službu. Jednoduchý způsob, jak se tomuto selhání vyhnout, je zajistit, aby služba běžela na více počítačích. Testování je také potřeba k tomu, aby se zajistilo, že selhání jednoho počítače nenarušilo běžící službu. Plánování kapacity zajišťuje, že se náhradní instance dá vytvořit jinde a že snížení kapacity nepřetěžují zbývající služby. 

Stejný vzor funguje bez ohledu na to, co se snažíte vyhnout selhání. Pokud máte například obavy o selhání sítě SAN, spustíte na více sítích San. Pokud máte obavy o ztrátu stojanu serverů, můžete spustit ve více skříních. Pokud máte obavy o ztrátu datových center, měla by být vaše služba spuštěná v několika oblastech Azure, v různých Zóny dostupnosti Azure nebo napříč vašimi datacentry. 

Když je služba rozložená mezi několik fyzických instancí (počítače, stojany, datová centra, oblasti), pořád se jedná o některé typy současných selhání. Nejedná se ale o jednu a ještě více selhání určitého typu (například při selhání jednoho virtuálního počítače nebo síťového propojení), a proto už se nejedná o "havárií". 

Service Fabric poskytuje mechanismy pro rozšíření clusteru a zpracování back-vrácených uzlů a služeb. Service Fabric také umožňuje spouštět mnoho instancí služeb, aby nedocházelo k neplánovaným chybám při zapínání na reálné katastrofy.

Mohou nastat důvody, proč je spuštění nasazení dostatečně velké pro rozsah selhání neproveditelné. Například může trvat víc hardwarových prostředků, než jste ochotni platit za relativní k výpadku. Když pracujete s distribuovanými aplikacemi, další přesměrování komunikace nebo náklady na replikaci stavu mezi geografickými vzdálenostmi mohou způsobit nepřijatelnou latenci. Kde se tento řádek vykreslí, se pro každou aplikaci liší. 

V případě chyb softwaru může být chyba ve službě, kterou se pokoušíte škálovat. V takovém případě více kopií nebrání v případě havárie, protože stav selhání je koreluje napříč všemi instancemi.

### <a name="operational-faults"></a>Provozní chyby
I když je vaše služba rozložená na celém světě s mnoha redundancemi, může stále docházet k katastrofální důsledkym událostem. Například někdo může omylem znovu nakonfigurovat název DNS pro službu nebo ho odstranit přímo. 

Řekněme například, že jste měli stavovou Service Fabric službu a někdo tuto službu odstranil omylem. Pokud se nejedná o nějaké jiné zmírnění, tato služba a všechny stavy, které by měly být teď pryč. Tyto typy provozních katastrof ("ne") vyžadují jiné zmírňování a kroky pro obnovení než běžné neplánované chyby. 

Nejlepším způsobem, jak se vyhnout těmto typům provozních chyb, je:
- Omezte provozní přístup k prostředí.
- Striktně auditujte nebezpečné operace.
- Umožňuje zajistit automatizaci, zabránit ručnímu nebo nedostatku změn a ověřit konkrétní změny před prostředím a teprve potom je přijmout.
- Zajistěte, aby byly destruktivní operace "měkké". Měkké operace se projeví okamžitě nebo je můžete vrátit zpět v časovém intervalu.

Service Fabric poskytuje mechanismy, které zabraňují provozním chybám, jako je například poskytování řízení přístupu [na základě rolí](service-fabric-cluster-security-roles.md) pro operace clusteru. Většina těchto provozních chyb ale vyžaduje organizační úsilí a další systémy. Service Fabric poskytuje mechanismy pro zbývající provozní chyby, zejména [zálohování a obnovení stavových služeb](service-fabric-backuprestoreservice-quickstart-azurecluster.md).

## <a name="managing-failures"></a>Správa selhání
Cílem Service Fabric je automatická správa selhání. Pro zpracování některých typů selhání ale služby musí mít další kód. Jiné typy _selhání by se neměly automaticky_ řešit z důvodů bezpečnosti a kontinuity obchodních potřeb. 

### <a name="handling-single-failures"></a>Zpracování jednoduchých selhání
U jednoho počítače může selhat z nejrůznějších důvodů. V některých případech se jedná o příčiny hardwaru, jako jsou napájení a selhání síťového hardwaru. Další chyby jsou v softwaru. Mezi ně patří selhání operačního systému a samotné služby. Service Fabric automaticky detekuje tyto typy selhání, včetně případů, kdy se počítač od jiných počítačů izoluje kvůli problémům se sítí.

Bez ohledu na typ služby způsobí spuštění jedné instance výpadky této služby, pokud tato jediná kopie kódu z nějakého důvodu se nezdařila. 

Aby se zajistila jedna neúspěšná, nejjednodušší věc, kterou můžete udělat, je, že ve výchozím nastavení běží vaše služby ve více než jednom uzlu. U bezstavových služeb se ujistěte, že `InstanceCount` je větší než 1. U stavových služeb je minimální doporučení v tom, že `TargetReplicaSetSize` a `MinReplicaSetSize` jsou obě nastaveny na 3. Spuštění více kopií kódu služby zajistí, že vaše služba bude automaticky zpracovávat každou jednotlivou chybu. 

### <a name="handling-coordinated-failures"></a>Zpracování koordinovaných selhání
Koordinované chyby v clusteru můžou být způsobeny buď plánovanými, nebo neplánovanými chybami infrastruktury a změnami, nebo plánovanými změnami softwaru. Service Fabric modely infrastruktury, které se zachováním koordinovaných selhání jako *domén selhání*. Oblasti, ve kterých se projeví koordinované změny softwaru, se modelují jako *upgradovací domény*. Další informace o doménách selhání, upgradovacích doménách a topologii clusteru najdete v tématu [popisujícím Service Fabric cluster pomocí Správce prostředků clusteru](service-fabric-cluster-resource-manager-cluster-description.md).

Ve výchozím nastavení Service Fabric při plánování, kde se vaše služby spouštějí, považovat za domény selhání a upgrady. Ve výchozím nastavení se Service Fabric snaží zajistit, aby se vaše služby spouštěly v několika selháních a upgradovacích doménách, takže pokud dojde k plánovaným nebo neplánovaným změnám, vaše služby zůstanou dostupné. 

Řekněme například, že selhání zdroje napájení způsobí, že všechny počítače v racku selžou souběžně. Pokud je spuštěno více kopií služby, ztráta velkého počtu počítačů v doméně selhání se změní pouze na další příklad jediného selhání služby. Důvodem je, že správa domén selhání a upgradu je zásadní pro zajištění vysoké dostupnosti vašich služeb. 

Pokud používáte Service Fabric v Azure, domény selhání a upgradovací domény se spravují automaticky. V jiných prostředích nemusí být. Pokud vytváříte vlastní clustery v místním prostředí, nezapomeňte správně namapovat a naplánovat rozložení domén selhání.

Domény upgradu jsou užitečné pro oblasti modelování, ve kterých se bude software upgradovat ve stejnou dobu. Z tohoto důvodu také domény upgradu často definují hranice, při kterých se software během plánovaných upgradů vypíná. Upgrade obou Service Fabric a služeb se řídí stejným modelem. Další informace o postupných inovacích, upgradovacích doménách a modelu stavu Service Fabric, který pomáhá zabránit nezamýšleným změnám v ovlivnění clusteru a služby, najdete v tématech:

 - [Upgrade aplikací](service-fabric-application-upgrade.md)
 - [Kurz upgradu aplikace](service-fabric-application-upgrade-tutorial.md)
 - [Service Fabric model stavu](service-fabric-health-introduction.md)

Rozložení clusteru můžete vizualizovat pomocí mapy clusteru poskytované v [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md):

<center>

![Uzly jsou rozdělené do různých domén selhání v Service Fabric Explorer][sfx-cluster-map]
</center>

> [!NOTE]
> Modelovací oblasti selhání, postupné upgrady, spuštění mnoha instancí kódu a stavu služby, pravidla umístění, aby se zajistilo, že se vaše služby spouští napříč doménami selhání a upgradu, a integrované monitorování stavu jsou jenom *některé* z funkcí, které Service Fabric poskytují, aby byly normální provozní problémy a chyby při zapínání na katastrofy. 
>

### <a name="handling-simultaneous-hardware-or-software-failures"></a>Zpracování současných hardwarových nebo softwarových selhání
Seznámili jsme se s jedním selháním. Jak vidíte, lze je snadno zvládnout pro bezstavové a stavové služby, a to pouze tím, že zachováte více kopií kódu (a stavu), které jsou spuštěny v rámci selhání a upgradovacích domén. 

Může se taky vyskytnout víc souběžných náhodných selhání. To je pravděpodobnější, že vede k výpadkům nebo skutečné havárii.


#### <a name="stateless-services"></a>Bezstavové služby
Počet instancí nestavové služby indikuje požadovaný počet instancí, které musí být spuštěny. Když dojde k selhání jakékoli (nebo všech) instancí, Service Fabric odpoví automatickým vytvořením náhradních instancí na jiných uzlech. Service Fabric nadále vytvářet náhrady, dokud se služba nevrátí do požadovaného počtu instancí.

Předpokládejme například, že Bezstavová služba má `InstanceCount` hodnotu-1. Tato hodnota znamená, že na každém uzlu v clusteru by měla běžet jedna instance. Pokud některé z těchto instancí selžou, Service Fabric zjistí, že služba není v požadovaném stavu, a pokusí se vytvořit instance na uzlech, kde chybí.

#### <a name="stateful-services"></a>Stavové služby
Existují dva typy stavových služeb:
- Stav s trvalým stavem.
- Stav se stavem bez trvalého uložení. (Stav je uložený v paměti.)

Obnovení stavové služby z neúspěšného provozu závisí na typu stavové služby, počtu replik, které měla služba obsahovat, a počtu replik, které selhaly.

Ve stavové službě se příchozí data replikují mezi replikami (primární a jakákoli aktivní sekundární). Pokud většina replik obdrží data, považují se za potvrzená data *kvora* . (Pro pět replik, tři budou kvorum.) To znamená, že v jakémkoli okamžiku bude k dispozici alespoň kvorum replik s nejnovějšími daty. Pokud se repliky nezdaří (například dvě než pět), můžeme použít hodnotu kvora k výpočtu, jestli můžeme provést obnovení. (Vzhledem k tomu, že zbývající tři z pěti replik jsou stále v, je zaručeno, že nejméně jedna replika bude mít úplná data.)

V případě selhání kvora repliky je oddíl deklarován jako ve stavu *ztráty kvora* . Řekněme, že oddíl má pět replik, což znamená, že k úplným datům mají zaručená aspoň tři repliky. Pokud kvorum (tři pět) replik selže, Service Fabric nemůže určit, jestli zbývající repliky (dvě z nich) mají dostatek dat pro obnovení oddílu. V případech, kdy Service Fabric detekuje ztrátu kvora, její výchozí chování je zabránit dalším zápisům do oddílu, deklarovat ztrátu kvora a počkat na obnovení kvora replik.

Určení, jestli došlo k havárii pro stavovou službu a pak ji spravovat podle tří fází:

1. Zjištění, zda došlo ke ztrátě kvora nebo ne.
   
   Ztráta kvora je deklarována v případě, že většina replik stavové služby je mimo provoz.
2. Určení, zda je ztráta kvora trvalá.
   
   Ve většině případů jsou chyby přechodné. Procesy se restartují, uzly se restartují, virtuální počítače se znovu spouštějí a zamění se síťové oddíly. Někdy ale chyby jsou trvalé. Zda jsou chyby trvalé nebo nezávisí na tom, zda stavová služba uchovává svůj stav nebo zda je uchovávána pouze v paměti: 
   
   - U služeb bez trvalého stavu dojde k selhání kvora nebo většího počtu replik _okamžitě_ v trvalé ztrátě kvora. Když Service Fabric detekuje ztrátu kvora ve stavové netrvalé službě, okamžitě pokračuje krokem 3 tím, že deklaruje (možná) ztrátu dat. Řízení ztráty dat dává smysl, protože Service Fabric ví, že není potřeba čekat na to, aby se repliky vrátily zpátky. I po obnovení budou data ztracena z důvodu netrvalého charakteru služby.
   - U stavových trvalých služeb způsobí selhání kvora nebo většího počtu replik Service Fabric počkat, až se repliky vrátí a obnoví kvorum. Výsledkem je výpadek služby pro jakékoli _zápisy_ do ovlivněného oddílu (neboli sady replik) služby. Nicméně je možné, že čtení je stále možné se sníženými zárukami konzistence. Výchozí doba, po kterou Service Fabric čeká na obnovení kvora, je *nekonečná*, protože pokračuje v případě (potenciální) událost ztráty dat a přináší další rizika. To znamená, že Service Fabric nebude pokračovat k dalšímu kroku, pokud správce neprovede akci k deklaraci ztráty dat.
3. Určení, zda jsou data ztracena, a obnovení ze zálohy.

   Pokud došlo ke ztrátě kvora (buď automaticky, nebo prostřednictvím akce správy), Service Fabric a služby se přesunou k určení, jestli se data skutečně ztratila. V tomto okamžiku Service Fabric taky ví, že se ostatní repliky nevrátí. To bylo rozhodnutí, které bylo provedeno, když jsme zastavili čekání na vyřešení ztráty kvora. Nejlepší akci pro službu je obvykle zablokovat a čekat na konkrétní zásah správce.
   
   Když Service Fabric volá `OnDataLossAsync` metodu, je vždy z důvodu _podezřelé_ ztráty dat. Service Fabric zajistí, že se toto volání doručí do _nejlepší_ zbývající repliky. To je to, že replika provedla nejvíce. 
   
   Důvodem, kdy vždycky říkáme _podezření_ na ztrátu dat, je to, že zbývající replika má stejný stav jako primární v případě ztráty kvora. Nicméně bez tohoto stavu pro porovnání s, neexistuje žádný dobrý způsob, jak Service Fabric nebo operátory znát, zda jsou.     
   
   Co to dělá Typická implementace `OnDataLossAsync` metody?
   1. Implementační protokoly, které `OnDataLossAsync` byly aktivovány, a aktivují všechny nezbytné výstrahy pro správu.
   1. Implementace se obvykle pozastavuje a čeká na další rozhodování a ruční akce, které je potřeba provést. Důvodem je to, že i když jsou zálohy k dispozici, může být potřeba je připravit. 
   
      Pokud například dvě různé služby koordinují informace, může být nutné upravit tyto zálohy, aby se zajistilo, že po obnovení budou informace, které tyto dvě služby mají v souladu s konzistentní. 
   1. Často se z této služby vyskytuje nějaká další telemetrie nebo začerpání. Tato metadata mohou být obsažena v jiných službách nebo v protokolech. Tyto informace můžete podle potřeby použít k určení, jestli se v primárním umístění neobjevila žádná volání, která se neobjevila v záloze nebo replikují do této konkrétní repliky. Aby bylo možné obnovení provést, možná budete muset tato volání přehrajte nebo přidat do zálohy.  
   1. Implementace porovnává stav zbývající repliky s, která je obsažena v jakémkoli dostupném zálohování. Pokud používáte Service Fabric Reliable Collections, existují [nástroje a procesy](service-fabric-reliable-services-backup-restore.md) k dispozici. Cílem je zjistit, jestli je stav v replice dostatečný, a zjistit, co může zálohování chybět.
   1. Po provedení porovnání a po dokončení obnovení (v případě potřeby) by kód služby měl vracet **hodnotu true** , pokud byly provedeny nějaké změny stavu. Pokud replika zjistila, že se jednalo o nejlepší dostupnou kopii stavu a neudělala žádné změny, kód vrátí **hodnotu false**. 
   
      Hodnota **true** značí, že všechny _ostatní_ zbývající repliky teď můžou být nekonzistentní s tímto. Budou z této repliky vyřazeny a znovu sestaveny. Hodnota **false** znamená, že se neudělaly žádné změny stavu, takže ostatní repliky můžou zachovat, co mají. 

Před nasazením služeb v produkčním prostředí je důležité, aby tvůrci služeb v praxi nasadili potenciální scénáře ztráty dat a selhání. Aby se zabránilo ochraně před ztrátou dat, je důležité pravidelně [zálohovat stav](service-fabric-reliable-services-backup-restore.md) jakékoli stavové služby na geograficky redundantní úložiště. 

Musíte také zajistit, aby bylo možné obnovit stav. Vzhledem k tomu, že se zálohují spousty různých služeb v různou dobu, je potřeba zajistit, aby po obnovení byly vaše služby konzistentní. 

Představte si třeba situaci, kdy jedna služba vygeneruje číslo a uloží ji, a pošle ji do jiné služby, která je také ukládá. Po obnovení si můžete všimnout, že druhá služba má číslo, ale první z nich ne, protože její záloha nezahrnovala tuto operaci.

Pokud zjistíte, že zbývající repliky nejsou dostačující pro pokračování v případě ztráty dat, a nemůžete rekonstruovat stav služby z telemetrie nebo výfuku, bude frekvence zálohování určovat nejlepší možný cíl bodu obnovení (RPO). Service Fabric poskytuje mnoho nástrojů pro testování různých scénářů selhání, včetně trvalého kvora a ztráty dat, které vyžadují obnovení ze zálohy. Tyto scénáře jsou součástí nástrojů pro testování v Service Fabric, které spravuje služba pro analýzu chyb. Další informace o těchto nástrojích a vzorcích najdete v tématu [Úvod do služby analýzy chyb](service-fabric-testability-overview.md). 

> [!NOTE]
> Systémové služby můžou také snížit ztrátu kvora. Dopad je specifický pro příslušnou službu. Například ztráta kvora ve službě pojmenování má vliv na překlad adres, zatímco ztráta kvora ve službě Správce převzetí služeb při selhání blokuje nové vytvoření a převzetí služeb při selhání. 
> 
> Systémové služby Service Fabric se řídí stejným vzorem jako vaše služby pro správu stavu, ale nedoporučujeme, abyste je přesunuli ze ztráty kvora a potenciální ztráty dat. Místo toho doporučujeme, abyste  [vyhledali podporu](service-fabric-support.md) , abyste našli řešení, které je pro vaši situaci cílené. Obvykle je vhodné jednoduše počkat, dokud se nevrátí.
>

#### <a name="troubleshooting-quorum-loss"></a>Řešení potíží s ztrátou kvora

Kvůli přechodnému selhání se repliky můžou považovat za občasné. Počkejte nějakou dobu, než se Service Fabric pokusí je přenést. Pokud se repliky neprovozují déle než očekávanou dobu trvání, postupujte podle těchto kroků pro řešení potíží:
- Může dojít k chybě replik. Kontroluje sestavy stavu na úrovni repliky a protokoly aplikací. Shromážděte výpisy stavu systému a proveďte potřebné akce k obnovení.
- Je možné, že proces repliky přestane reagovat. Zkontrolujte protokoly aplikací, abyste ověřili tuto kontrolu. Shromážděte výpisy procesu a potom zastavte nereagující proces. Service Fabric vytvoří náhradní proces a pokusí se repliku přenést zpátky.
- Uzly, které hostují repliky, mohou být mimo provoz. Pokud chcete uzly přenést, restartujte příslušný virtuální počítač.

V některých případech nemusí být možné repliky obnovit. Například jednotky se nezdařily nebo fyzické počítače nereagují. V těchto případech Service Fabric nutné, aby nečekaly na obnovení repliky.

Nepoužívejte *tyto* metody, pokud se potenciální ztráta dat neakceptuje k převedení služby do režimu online. V takovém případě by se mělo provádět veškeré úsilí k obnovování fyzických počítačů.

Následující akce mohou mít za následek ztrátu dat. Před provedením tohoto postupu ověřte.
   
> [!NOTE]
> Tyto metody nejsou _nikdy_ bezpečné k použití těchto metod, než je cíleno na konkrétní oddíly. 
>

- Použijte `Repair-ServiceFabricPartition -PartitionId` `System.Fabric.FabricClient.ClusterManagementClient.RecoverPartitionAsync(Guid partitionId)` rozhraní API nebo. Toto rozhraní API umožňuje zadat ID oddílu pro přesunutí ztráty kvora a potenciální ztráty dat.
- Pokud se v clusteru vyskytují časté chyby, které způsobují, že se služby přestanou do stavu ztráty kvora, a může dojít _ke ztrátě dat_, zadáním příslušné hodnoty [QuorumLossWaitDuration](/powershell/module/servicefabric/update-servicefabricservice) může vaše služba automaticky obnovit. Service Fabric `QuorumLossWaitDuration` před provedením obnovení počká na poskytnutou hodnotu (výchozí je nekonečno). Tuto metodu *nedoporučujeme* , protože může dojít k neočekávaným ztrátám dat.

## <a name="availability-of-the-service-fabric-cluster"></a>Dostupnost clusteru Service Fabric
Obecně platí, že Cluster Service Fabric je vysoce distribuované prostředí bez jednoho bodu selhání. Selhání žádného uzlu nebude způsobovat problémy s dostupností nebo spolehlivostí clusteru, a to hlavně proto, že systémové služby Service Fabric postupují podle výše uvedených pokynů. To znamená, že se ve výchozím nastavení vždycky spouštějí se třemi nebo více replikami a systémové služby, které jsou na všech uzlech bez stavového provozu. 

Základní Service Fabric sítě a vrstvy detekce selhání jsou plně distribuované. Většinu systémových služeb lze znovu vytvořit z metadat v clusteru nebo zjistit, jak znovu synchronizovat svůj stav z jiných míst. Pokud se systémové služby dostanou do situace v případě ztráty kvora, například popsané výše, může dojít k ohrožení dostupnosti clusteru. V těchto případech možná nebudete moci v clusteru provádět určité operace (například spuštění upgradu nebo nasazení nových služeb), ale samotný cluster je stále v provozu. 

Služby na běžícím clusteru budou průběžně fungovat, pokud nebudou vyžadovat zápis do systémových služeb, aby bylo možné pokračovat v práci. Pokud je například Správce převzetí služeb při selhání v případě ztráty kvora, budou všechny služby nadále spuštěny. Všechny služby, které selžou, ale nebudou moct automaticky restartovat, protože to vyžaduje zapojení Správce převzetí služeb při selhání. 

### <a name="failures-of-a-datacenter-or-an-azure-region"></a>Selhání datového centra nebo oblasti Azure
Ve výjimečných případech může být fyzické datacentrum dočasně nedostupné ze ztráty napájení nebo připojení k síti. V těchto případech nebudou vaše Service Fabric clustery a služby v tomto datovém centru nebo v oblasti Azure k dispozici. _Vaše data jsou ale zachovaná_. 

U clusterů, které běží v Azure, můžete na [stránce stavu Azure][azure-status-dashboard]zobrazit aktualizace na výpadky. V vysoce pravděpodobném případě, že fyzické datacentrum je částečně nebo zcela zničené, může dojít ke ztrátě všech Service Fabricch hostovaných clusterů nebo služeb uvnitř nich. Tato ztráta zahrnuje jakýkoli stav, který není zálohovaný mimo toto datacentrum nebo oblast.

Existují dvě různé strategie pro udržení trvalého nebo trvalého selhání jednoho datového centra nebo oblasti: 

- Spouštějte samostatné clustery Service Fabric v několika takových oblastech a použijte některý mechanismus pro převzetí služeb při selhání a navrácení služeb po obnovení mezi těmito prostředími. Tento model aktivních/aktivních nebo aktivních/pasivních modelů s vícenásobným clusterem vyžaduje další kód pro správu a provoz. Tento model také vyžaduje koordinaci záloh ze služeb v jednom datovém centru nebo oblasti, aby byly k dispozici v jiných datových centrech nebo oblastech, pokud jedna z nich dojde k chybě. 
- Spusťte jeden Service Fabric cluster, který pokrývá více datových center nebo oblastí. Minimální podporovaná konfigurace této strategie je tři datová centra nebo oblasti. Doporučený počet oblastí nebo datových Center je pět. 
  
  Tento model vyžaduje složitější topologii clusteru. Výhodou ale je, že selhání jednoho datacentra nebo oblasti se převede z havárie do normálního selhání. Tyto chyby můžete zpracovat pomocí mechanismů, které fungují pro clustery v rámci jedné oblasti. Domény selhání, upgradované domény a Service Fabric pravidla umístění zajišťují, aby byly úlohy distribuované, aby tolerovány normální selhání. 
  
  Další informace o zásadách, které vám pomůžou s provozováním služeb v tomto typu clusteru, najdete v tématu [Zásady umístění pro Service Fabric služby](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md).

### <a name="random-failures-that-lead-to-cluster-failures"></a>Náhodné chyby, které vedou k selhání clusteru
Service Fabric má koncept *počátečních uzlů*. Jedná se o uzly, které udržují dostupnost podkladového clusteru. 

Uzly dosazení vám pomůžou zajistit, že cluster zůstane v platnosti, a to tak, že se zapůjčí s ostatními uzly a během určitých druhů selhání zaručí jako tiebreakers. Pokud náhodné chyby odeberou většinu uzlů počátečního prostředí v clusteru a nevrátí se do režimu rychlé, cluster se automaticky vypne. Cluster pak dojde k chybě. 

V Azure Service Fabric poskytovatel prostředků spravuje Service Fabric konfiguracích clusteru. Ve výchozím nastavení poskytovatel prostředků distribuuje uzly počátečních hodnot mezi selhání a upgradovací domény pro *typ primárního uzlu*. Pokud je typ primárního uzlu označený jako stříbrná nebo zlatá, odeberete-li uzel počáteční hodnoty (buď změnou velikosti primárního typu uzlu, nebo jeho ručním odebráním), cluster se pokusí zvýšit úroveň jiného nepočátečního uzlu z dostupné kapacity typu primární uzel. Tento pokus selže, pokud máte méně dostupnou kapacitu, než je úroveň spolehlivosti clusteru nutná pro primární typ uzlu.

V samostatných Service Fabric clusterech a v Azure je typ primárního uzlu ten, který spouští semena. Pokud definujete typ primárního uzlu, Service Fabric bude automaticky využívat počet uzlů poskytnutý vytvořením až devíti uzlů počátečních a sedmi replik každé systémové služby. Pokud sada náhodných selhání převezme většinu těchto replik současně, systémové služby zadají ztrátu kvora. Pokud dojde ke ztrátě většiny počátečních uzlů, cluster se brzy ukončí.

## <a name="next-steps"></a>Další kroky
- Naučte se simulovat různé chyby pomocí [architektury testování](service-fabric-testability-overview.md).
- Přečtěte si další prostředky pro zotavení po havárii a vysokou dostupnost. Společnost Microsoft publikovala v těchto tématech velké množství pokynů. I když některé z těchto prostředků odkazují na konkrétní techniky pro použití v jiných produktech, obsahují mnoho obecných osvědčených postupů, které můžete použít v kontextu Service Fabric:
  - [Kontrolní seznam k dostupnosti](/azure/architecture/checklist/resiliency-per-service)
  - [Postup při provádění postupu zotavení po havárii](../azure-sql/database/disaster-recovery-drills.md)
  - [Zotavení po havárii a vysoká dostupnost pro aplikace Azure][dr-ha-guide]
- Přečtěte si o [možnostech podpory Service Fabric](service-fabric-support.md).


<!-- External links -->

[repair-partition-ps]: /windows/win32/perfctrs/specifying-a-counter-path
[azure-status-dashboard]:https://azure.microsoft.com/status/
[azure-regions]: https://azure.microsoft.com/regions/
[dr-ha-guide]: /previous-versions/azure/dn251004(v=azure.100)


<!-- Images -->

[sfx-cluster-map]: ./media/service-fabric-disaster-recovery/sfx-clustermap.png
