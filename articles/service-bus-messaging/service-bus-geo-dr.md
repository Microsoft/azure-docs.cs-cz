---
title: Azure Service Bus geograficky zotavení po havárii | Microsoft Docs
description: Použití geografických oblastí k převzetí služeb při selhání a zotavení po havárii v Azure Service Bus
ms.topic: article
ms.date: 01/04/2021
ms.openlocfilehash: c07721c07923a40da9fe28e0e3116bfd6a52210f
ms.sourcegitcommit: aeba98c7b85ad435b631d40cbe1f9419727d5884
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2021
ms.locfileid: "97862353"
---
# <a name="azure-service-bus-geo-disaster-recovery"></a>Azure Service Bus geografické zotavení po havárii

Odolnost proti katastrofální důsledkym výpadků prostředků zpracování dat je požadavkem pro mnoho podniků a v některých případech i v případě potřeby i podle odvětví. 

Azure Service Bus už šíří riziko závažných chyb jednotlivých počítačů nebo dokonce kompletních stojanů napříč clustery, které v rámci datového centra zaujímají více domén selhání, a implementuje transparentní mechanismy detekce selhání a převzetí služeb při selhání tak, aby služba pokračovala v provozu v rámci zajištěných úrovní služeb a obvykle bez znatelného přerušení v případě takových selhání. Pokud byl vytvořen obor názvů Service Bus s možností povolení pro [zóny dostupnosti](../availability-zones/az-overview.md), riziko výpadku je dále rozdělené do tří fyzicky oddělených zařízení a služba má dostatečné rezervy na kapacitu, aby se okamžitě vypořádat s kompletní a závažnou ztrátou celého zařízení. 

Model clusteru All-Active Azure Service Bus s využitím zóny dostupnosti je nadřazený jakémukoli místnímu produktu zprostředkovatele zpráv z hlediska odolnosti proti závažným chybám hardwaru a i závažným ztrátám celých zařízení Datacenter. Pořád se může stát, že v případě vysokého fyzického zničení dojde k závažné situaci, že i tyto míry nemůžou dostatečně bránit. 

Service Bus funkce geografického zotavení po havárii je navržená tak, aby se usnadnilo zotavení po havárii této velikosti, aby nedošlo k selhání oblasti Azure pro zajištění dobrého a nemusíte měnit konfigurace aplikace. Opuštění oblasti Azure obvykle zahrnuje několik služeb a tato funkce primárně směřuje k tomu, že pomáhá zachovat integritu konfigurace složených aplikací. Tato funkce je globálně dostupná pro SKU Service Bus Premium. 

Funkce obnovení Geo-Disaster zajišťuje, aby byla celá konfigurace oboru názvů (fronty, témata, odběry, filtry) průběžně replikována z primárního oboru názvů do sekundárního oboru názvů, pokud je spárována, a umožňuje kdykoli spustit pouze jednou převzetí služeb při selhání z primární na sekundární. Přesunutí převzetí služeb při selhání znovu nasměruje zvolený název aliasu oboru názvů na sekundární obor názvů a potom přeruší párování. Převzetí služeb při selhání je skoro okamžité po zahájení. 

> [!IMPORTANT]
> Tato funkce umožňuje okamžitou kontinuitu operací se stejnou konfigurací, ale **nereplikuje zprávy uchovávané ve frontách nebo předplatných témat nebo frontách nedoručených** zpráv. Aby se zachovala Sémantika fronty, bude taková replikace vyžadovat nejen replikaci dat zprávy, ale všechny změny stavu ve zprostředkovateli. U většiny Service Bus oborů názvů by požadovaný provoz replikace daleko překročil provoz aplikace a s frontami s vysokou propustností, většina zpráv bude stále replikována do sekundárního počítače, zatímco jsou již odstraňována z primární služby, což způsobí nadměrné wasteful provoz. Pro trasy replikace s vysokou latencí, které se vztahují na mnoho dvojic, které byste si zvolili pro geografickou obnovu po havárii, může být taky možné, že provoz replikace nebude tolerovat provoz aplikací kvůli latenci, která se vyvolala.
 
> [!TIP]
> Pro replikaci obsahu front a odběrů témat a provozu odpovídajících oborů názvů v aktivních/aktivních konfiguracích za účelem odolat výpadkům a haváriím se tato sada funkcí geografického zotavení po havárii neštíhlá, ale postupujte podle [pokynů pro replikaci](service-bus-federation-overview.md).  

## <a name="outages-and-disasters"></a>Výpadky a havárie

Je důležité poznamenat rozdíl mezi "výpadky" a "katastrofami". 

*Výpadek* je dočasná nedostupnost Azure Service Bus a může ovlivnit některé součásti služby, jako je třeba úložiště pro zasílání zpráv nebo i celé datacentrum. Po vyřešení problému však bude Service Bus opět k dispozici. Výpadky obvykle nezpůsobí ztrátu zpráv nebo jiných dat. Příkladem takového výpadku může být výpadek napájení v datovém centru. Některé výpadky představují jenom krátké ztráty připojení kvůli přechodným nebo síťovým problémům. 

*Havárie* se definuje jako trvalá nebo dlouhodobá ztráta Service Bus clusteru, oblasti Azure nebo datového centra. Oblast nebo datové centrum může nebo nemusí být k dispozici znovu nebo může být vypnuté hodiny nebo dny. Příklady takových katastrof jsou požáry, zahlcení nebo zemětřesení. Havárie, která se stala trvalo, může způsobit ztrátu některých zpráv, událostí nebo jiných dat. Ve většině případů by ale nemělo dojít ke ztrátě dat a po zálohování datového centra se dají obnovit zprávy.

Funkce Azure Service Bus geografického zotavení po havárii je řešením zotavení po havárii. Koncepty a pracovní postupy popsané v tomto článku se týkají scénářů po havárii a nepřechodných nebo dočasných výpadků. Podrobnou diskuzi o zotavení po havárii v Microsoft Azure najdete v [tomto článku](/azure/architecture/resiliency/disaster-recovery-azure-applications).   

## <a name="basic-concepts-and-terms"></a>Základní pojmy a pojmy

Funkce zotavení po havárii implementuje zotavení po havárii metadat a závisí na primárních a sekundárních oborech názvů pro zotavení po havárii. Všimněte si, že funkce geografického zotavení po havárii je dostupná jenom pro [SKU úrovně Premium](service-bus-premium-messaging.md) . Nemusíte dělat žádné změny připojovacího řetězce, protože připojení se provádí pomocí aliasu.

V tomto článku se používají následující výrazy:

-  *Alias*: název pro konfiguraci zotavení po havárii, kterou jste nastavili. Alias poskytuje jediný stabilní řetězec plně kvalifikovaného názvu domény (FQDN). Aplikace používají tento připojovací řetězec aliasu pro připojení k oboru názvů. Použití aliasu zajistí, že se připojovací řetězec při aktivaci převzetí služeb při selhání nezměnil.

-  *Primární nebo sekundární obor názvů*: obory názvů, které odpovídají aliasu. Primární obor názvů je "aktivní" a přijímá zprávy (může to být existující nebo nový obor názvů). Sekundární obor názvů je "pasivní" a nepřijímá zprávy. Metadata mezi oběma je synchronizována, takže obě můžou bezproblémově přijímat zprávy bez nutnosti změny kódu aplikace nebo připojovacího řetězce. Chcete-li zajistit, že pouze aktivní obor názvů přijímá zprávy, je nutné použít alias. 

-  *Metadata*: entity, jako jsou fronty, témata a předplatná; a jejich vlastnosti služby, které jsou přidruženy k oboru názvů. Všimněte si, že se automaticky replikují jenom entity a jejich nastavení. Zprávy nejsou replikovány.

-  *Převzetí služeb při selhání*: proces aktivace sekundárního oboru názvů.

## <a name="setup"></a>Nastavení

V následující části je přehled nastavení párování mezi obory názvů.

![1][]

Proces instalace je následující –

1. Zřídí obor názvů ***Primary** _ Service Bus Premium.

2. Zřídí _*_sekundární_*_ obor názvů Service Bus Premium v oblasti _different z místa, kde je primární obor názvů zřízený *. To vám pomůže zajistit izolaci chyb napříč různými oblastmi Datacenter.

3. Vytvořte párování mezi primárním a sekundárním oborem názvů, abyste získali ***alias** _.

    >[!NOTE] 
    > Pokud jste [přenesli obor názvů Azure Service Bus Standard do Azure Service Bus Premium](service-bus-migrate-standard-premium.md), je nutné použít již existující alias (tj. připojovací řetězec oboru názvů standardu Service Bus) k vytvoření konfigurace zotavení po havárii prostřednictvím _ *PS/CLI** nebo **REST API**.
    >
    >
    > Důvodem je, že během migrace se jako připojovací řetězec oboru názvů Azure Service Bus Standard nebo název DNS sám vytvoří alias pro obor názvů Azure Service Bus Premium.
    >
    > Vaše klientské aplikace musí používat tento alias (tj. připojovací řetězec oboru názvů Azure Service Bus Standard) pro připojení k oboru názvů Premium, ve kterém bylo nastaveno párování zotavení po havárii.
    >
    > Pokud použijete portál k nastavení konfigurace zotavení po havárii, portál tuto výstrahu zavede.


4. Pomocí *_aliasu_** získaného v kroku 3 Připojte klientské aplikace k primárnímu oboru názvů s povoleným geografickým Dr. Zpočátku alias odkazuje na primární obor názvů.

5. Volitelné Pokud chcete zjistit, jestli je převzetí služeb při selhání nezbytné, přidejte nějaké monitorování.

## <a name="failover-flow"></a>Postup převzetí služeb při selhání

Převzetí služeb při selhání se aktivuje ručně zákazníkem (buď explicitně prostřednictvím příkazu, nebo prostřednictvím obchodní logiky vlastněné klientem, která spouští příkaz) a nikdy neplatí pro Azure. Tím se zákazníkům poskytne úplné vlastnictví a přehled o řešení výpadků v páteřní síti Azure.

![4][]

Po aktivaci převzetí služeb při selhání –

1. Připojovací řetězec _*_aliasu_*_ je aktualizovaný, aby odkazoval na sekundární obor názvů Premium.

2. Klienti (odesílatelé a přijímače) se automaticky připojí k sekundárnímu oboru názvů.

3. Existující párování mezi primárním a sekundárním oborem názvů Premium je přerušeno.

Po zahájení převzetí služeb při selhání –

1. Pokud dojde k dalšímu výpadku, budete chtít být schopni převzít služby po obnovení. Proto nastavte další pasivní obor názvů a aktualizujte párování. 

2. Vyžádat zprávy z bývalého primárního oboru názvů, jakmile budou opět k dispozici. Potom tento obor názvů použijte pro běžné zasílání zpráv mimo instalaci geografického obnovení, nebo odstraňte starý primární obor názvů.

> [!NOTE]
> Jsou podporovány pouze sémantiky při předání služeb při selhání. V tomto scénáři převezmete služby při selhání a pak znovu spárujte s novým oborem názvů. Navrácení služeb po obnovení není podporováno. například v clusteru SQL. 

Převzetí služeb při selhání můžete automatizovat buď s monitorovacími systémy, nebo s vlastními řešeními monitorování. Tato automatizace ale má dodatečné plánování a práci, což není v rozsahu tohoto článku.

![2][]

## <a name="management"></a>Správa

Pokud jste udělali chybu; například jste spároval nesprávné oblasti při počátečním nastavení, můžete kdykoli rozdělit párování obou oborů názvů. Pokud chcete používat spárované obory názvů jako běžné obory názvů, odstraňte alias.

## <a name="use-existing-namespace-as-alias"></a>Použít existující obor názvů jako alias

Pokud máte scénář, ve kterém nemůžete změnit připojení výrobců a uživatelů, můžete název oboru názvů použít jako název aliasu. Tady najdete [ukázkový kód na GitHubu](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/SBGeoDR_existing_namespace_name).

## <a name="samples"></a>ukázky

[Ukázky na GitHubu](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/) ukazují, jak nastavit a iniciovat převzetí služeb při selhání. Tyto ukázky ukazují následující koncepty:

- Ukázka a nastavení .NET, které jsou požadovány v Azure Active Directory pro použití Azure Resource Manager s Service Bus k nastavení a povolení geografického zotavení po havárii.
- Postup potřebný ke spuštění ukázkového kódu.
- Jak používat existující obor názvů jako alias.
- Postup nebo umožnění geografického zotavení po havárii prostřednictvím PowerShellu nebo rozhraní příkazového řádku.
- [Odeslat a přijmout](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/TestGeoDR/ConsoleApp1) z aktuálního primárního nebo sekundárního oboru názvů pomocí aliasu

## <a name="considerations"></a>Požadavky

Vezměte v úvahu následující skutečnosti:

1. Při plánování převzetí služeb při selhání byste měli také zvážit časový faktor. Pokud například ztratíte připojení po dobu delší než 15 až 20 minut, můžete se rozhodnout zahájit převzetí služeb při selhání.

2. Skutečnost, že žádná data nejsou replikována znamená, že aktuálně aktivní relace nebudou replikovány. Kromě toho nemusí fungovat duplicita duplicit a naplánované zprávy. Nové relace budou fungovat nové naplánované zprávy a nové duplicity. 

3. Převzetí služeb při selhání přes složitou distribuovanou infrastrukturu by mělo být alespoň jednou [vyzkoušeno](/azure/architecture/reliability/disaster-recovery#disaster-recovery-plan) .

4. Synchronizace entit může nějakou dobu trvat přibližně 50-100 entit za minutu. Předplatná a pravidla se také počítají jako entity.

## <a name="availability-zones"></a>Zóny dostupnosti

SKU Service Bus Premium také podporuje [zóny dostupnosti](../availability-zones/az-overview.md)a poskytuje umístění pro izolované chyby v oblasti Azure.

> [!NOTE]
> Podpora Zóny dostupnosti pro Azure Service Bus Premium je dostupná jenom v [oblastech Azure](../availability-zones/az-region.md) , kde se nacházejí zóny dostupnosti.

Zóny dostupnosti můžete povolit jenom pro nové obory názvů pomocí Azure Portal. Service Bus nepodporuje migraci stávajících oborů názvů. Po povolení v oboru názvů nelze zakázat redundanci zóny.

![3][]

## <a name="private-endpoints"></a>Soukromé koncové body
V této části najdete další požadavky při použití geografického zotavení po havárii s obory názvů, které používají privátní koncové body. Další informace o používání privátních koncových bodů s Service Bus obecně najdete v tématu věnovaném [integraci Azure Service Bus s privátním odkazem Azure](private-link-service.md).

### <a name="new-pairings"></a>Nové párování
Pokud se pokusíte vytvořit párování mezi primárním oborem názvů s privátním koncovým bodem a sekundárním oborem názvů bez privátního koncového bodu, párování selže. Párování bude úspěšné pouze v případě, že oba primární i sekundární obory názvů mají privátní koncové body. Doporučujeme použít stejné konfigurace na primárních a sekundárních oborech názvů a na virtuálních sítích, ve kterých jsou vytvořeny privátní koncové body. 

> [!NOTE]
> Pokud se pokusíte spárovat primární obor názvů se soukromým koncovým bodem a sekundárním oborem názvů, proces ověření kontroluje pouze to, zda privátní koncový bod existuje v sekundárním oboru názvů. Nekontroluje, jestli koncový bod funguje nebo bude po převzetí služeb při selhání fungovat. Je vaše zodpovědnost za to, že sekundární obor názvů s privátním koncovým bodem bude po převzetí služeb při selhání fungovat podle očekávání.
>
> Chcete-li otestovat, zda jsou konfigurace privátních koncových bodů stejné, zaslat požadavek [Get Queues](/rest/api/servicebus/stable/queues/get) sekundárnímu oboru názvů mimo virtuální síť a ověřte, že se od služby zobrazí chybová zpráva.

### <a name="existing-pairings"></a>Existující párování
Pokud párování mezi primárním a sekundárním oborem názvů už existuje, vytvoření privátního koncového bodu na primárním oboru názvů se nezdaří. Chcete-li tento problém vyřešit, vytvořte nejprve privátní koncový bod v sekundárním oboru názvů a potom jej vytvořte pro primární obor názvů.

> [!NOTE]
> I když povolujeme přístup jen pro čtení k sekundárnímu oboru názvů, jsou povoleny aktualizace konfigurací privátního koncového bodu. 

### <a name="recommended-configuration"></a>Doporučená konfigurace
Při vytváření konfigurace zotavení po havárii pro vaši aplikaci a Service Bus musíte vytvořit privátní koncové body pro primární i sekundární obory názvů Service Bus pro virtuální sítě, které hostují primární i sekundární instance vaší aplikace.

Řekněme, že máte dvě virtuální sítě: VNET-1, VNET-2 a tyto primární a druhé obory názvů: ServiceBus-Namespace1-Primary, ServiceBus-Namespace2-Secondary. Je třeba provést následující kroky: 

- V ServiceBus-Namespace1-Primary vytvořte dva privátní koncové body, které používají podsítě z VNET-1 a VNET-2.
- V ServiceBus-Namespace2-Secondary vytvořte dva privátní koncové body, které používají stejné podsítě z VNET-1 a VNET-2. 

![Privátní koncové body a virtuální sítě](./media/service-bus-geo-dr/private-endpoints-virtual-networks.png)


Výhodou tohoto přístupu je, že k převzetí služeb při selhání může dojít v aplikační vrstvě nezávisle na Service Bus oboru názvů. Zvažte následující scénáře: 

_ *Převzetí služeb při selhání jen pro aplikace:** sem aplikace nebude existovat v VNet-1, ale přesune se do virtuální sítě-2. Jak jsou oba privátní koncové body konfigurovány pro virtuální i sekundární obory názvů VNET-1 i VNET-2, bude aplikace pracovat pouze. 

**Service Bus převzetí služeb při selhání jen pro obor názvů**: tady se nakonfigurují oba privátní koncové body v obou virtuálních sítích pro primární i sekundární obory názvů. aplikace bude jenom fungovat. 

> [!NOTE]
> Pokyny pro obnovení geografických havárií virtuální sítě najdete v tématu [Virtual Network – provozní kontinuita](../virtual-network/virtual-network-disaster-recovery-guidance.md).

## <a name="next-steps"></a>Další kroky

- [Tady najdete referenční](/rest/api/servicebus/stable/disasterrecoveryconfigs)informace o geografickém zotavení po havárii REST API.
- Spusťte ukázku geografického zotavení po havárii [na GitHubu](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/SBGeoDR2).
- Podívejte se na ukázku obnovení geografického po havárii [, které odesílá zprávy do aliasu](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/TestGeoDR/ConsoleApp1).

Další informace o Service Bus zasílání zpráv najdete v následujících článcích:

* [Fronty, témata a odběry služby Service Bus](service-bus-queues-topics-subscriptions.md)
* [Začínáme s frontami služby Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak používat témata a odběry Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
* [Rozhraní REST API](/rest/api/servicebus/) 

[1]: ./media/service-bus-geo-dr/geodr_setup_pairing.png
[2]: ./media/service-bus-geo-dr/geo2.png
[3]: ./media/service-bus-geo-dr/az.png
[4]: ./media/service-bus-geo-dr/geodr_failover_alias_update.png
