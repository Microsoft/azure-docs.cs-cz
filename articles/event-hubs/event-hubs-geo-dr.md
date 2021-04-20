---
title: Geografické zotavení po havárii – Azure Event Hubs | Microsoft Docs
description: Použití geografických oblastí k převzetí služeb při selhání a zotavení po havárii v Azure Event Hubs
ms.topic: article
ms.date: 04/14/2021
ms.openlocfilehash: b2cf2b0ebef2b460b626e45d6b52309c9281d6ce
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739237"
---
# <a name="azure-event-hubs---geo-disaster-recovery"></a>Azure Event Hubs – geografická zotavení po havárii 

Odolnost proti katastrofální důsledkym výpadků prostředků zpracování dat je požadavkem pro mnoho podniků a v některých případech i v případě potřeby i podle odvětví. 

Azure Event Hubs už šíří riziko závažných chyb jednotlivých počítačů nebo dokonce kompletních stojanů napříč clustery, které v rámci datového centra přesahují více domén selhání, a implementuje transparentní mechanismy detekce selhání a převzetí služeb při selhání, takže služba bude dále fungovat v rámci zajištěných úrovní služeb a obvykle bez znatelného přerušení v případě takových selhání. Pokud byl vytvořen obor názvů Event Hubs s povolenou možností pro [zóny dostupnosti](../availability-zones/az-overview.md), je riziko výpadku dále rozdělené mezi tři fyzicky oddělená zařízení a služba má dostatek rezerv pro kapacitu, aby se okamžitě vypořádat s kompletní a závažnou ztrátou celého zařízení. 

Model clusteru pro všechny aktivní služby Azure Event Hubs s podporou zóny dostupnosti poskytuje odolnost proti čárkám, které nestačí k selhání hardwaru, i i k závažným ztrátám celých zařízení Datacenter. Pořád se může stát, že v případě vysokého fyzického zničení dojde k závažné situaci, že i tyto míry nemůžou dostatečně bránit. 

Event Hubs funkce geografického zotavení po havárii je navržená tak, aby se usnadnilo zotavení po havárii této velikosti, aby nedošlo k selhání oblasti Azure pro zajištění dobrého a nemusíte měnit konfigurace aplikace. Opuštění oblasti Azure obvykle zahrnuje několik služeb a tato funkce primárně směřuje k tomu, že pomáhá zachovat integritu konfigurace složených aplikací.  

Funkce obnovení Geo-Disaster zajišťuje, aby byla celá konfigurace oboru názvů (Event Hubs, skupiny uživatelů a nastavení) průběžně replikována z primárního oboru názvů do sekundárního oboru názvů, pokud je spárována, a umožňuje kdykoli spustit pouze jednou převzetí služeb při selhání z primární na sekundární. Přesunutí převzetí služeb při selhání znovu nasměruje zvolený název aliasu oboru názvů na sekundární obor názvů a potom přeruší párování. Převzetí služeb při selhání je skoro okamžité po zahájení. 

> [!IMPORTANT]
> Tato funkce umožňuje okamžitou kontinuitu operací se stejnou konfigurací, ale **nereplikuje data události**. Pokud havárie nezpůsobí ztrátu všech zón, data události, která se zachovají v primárním centru událostí po převzetí služeb při selhání, bude možné obnovit a v případě, že dojde k obnovení přístupu, může dojít k získání historických událostí. Pokud chcete replikovat data událostí a provozovat odpovídající obory názvů v aktivních/aktivních konfiguracích a vypořádat se s výpadky a katastrofami, nevybírejte tuto sadu funkcí geografického zotavení po havárii, ale postupujte podle [pokynů pro replikaci](event-hubs-federation-overview.md).  

## <a name="outages-and-disasters"></a>Výpadky a havárie

Je důležité poznamenat rozdíl mezi "výpadky" a "katastrofami". **Výpadek** je dočasná nedostupnost Azure Event Hubs a může ovlivnit některé součásti služby, jako je třeba úložiště pro zasílání zpráv nebo i celé datacentrum. Po vyřešení problému však bude Event Hubs opět k dispozici. Obvykle výpadek nezpůsobí ztrátu zpráv nebo jiných dat. Příkladem takového výpadku může být výpadek napájení v datovém centru. Některé výpadky představují jenom krátké ztráty připojení kvůli přechodným nebo síťovým problémům. 

*Havárie* se definuje jako trvalá nebo dlouhodobá ztráta Event Hubs clusteru, oblasti Azure nebo datového centra. Oblast nebo datové centrum může nebo nemusí být k dispozici znovu nebo může být vypnuté hodiny nebo dny. Příklady takových katastrof jsou požáry, zahlcení nebo zemětřesení. Havárie, která se stala trvalo, může způsobit ztrátu některých zpráv, událostí nebo jiných dat. Ve většině případů by ale nemělo dojít ke ztrátě dat a po zálohování datového centra se dají obnovit zprávy.

Funkce geografického zotavení po havárii v Azure Event Hubs je řešení zotavení po havárii. Koncepty a pracovní postupy popsané v tomto článku se týkají scénářů po havárii a nepřechodných nebo dočasných výpadků. Podrobnou diskuzi o zotavení po havárii v Microsoft Azure najdete v [tomto článku](/azure/architecture/resiliency/disaster-recovery-azure-applications).

## <a name="basic-concepts-and-terms"></a>Základní pojmy a pojmy

Funkce zotavení po havárii implementuje zotavení po havárii metadat a závisí na primárních a sekundárních oborech názvů pro zotavení po havárii. 

Funkce geografického zotavení po havárii je dostupná jenom pro [standardní a vyhrazené SKU](https://azure.microsoft.com/pricing/details/event-hubs/) . Nemusíte dělat žádné změny připojovacího řetězce, protože připojení se provádí pomocí aliasu.

V tomto článku se používají následující výrazy:

-  *Alias*: název pro konfiguraci zotavení po havárii, kterou jste nastavili. Alias poskytuje jediný stabilní řetězec plně kvalifikovaného názvu domény (FQDN). Aplikace používají tento připojovací řetězec aliasu pro připojení k oboru názvů. 

-  *Primární nebo sekundární obor názvů*: obory názvů, které odpovídají aliasu. Primární obor názvů je "aktivní" a přijímá zprávy (může se jednat o existující nebo nový obor názvů). Sekundární obor názvů je "pasivní" a nepřijímá zprávy. Metadata mezi oběma je synchronizována, takže obě můžou bezproblémově přijímat zprávy bez nutnosti změny kódu aplikace nebo připojovacího řetězce. Chcete-li zajistit, že pouze aktivní obor názvů přijímá zprávy, je nutné použít alias.
-  *Metadata*: entity, jako jsou centra událostí a skupiny uživatelů; a jejich vlastnosti služby, které jsou přidruženy k oboru názvů. Automaticky se replikují jenom entity a jejich nastavení. Zprávy a události nejsou replikovány. 
-  *Převzetí služeb při selhání*: proces aktivace sekundárního oboru názvů.

## <a name="supported-namespace-pairs"></a>Podporované páry oboru názvů
Podporovány jsou následující kombinace primárních a sekundárních oborů názvů:  

| Primární obor názvů | Sekundární obor názvů | Podporováno | 
| ----------------- | -------------------- | ---------- |
| Standard | Standard | Yes | 
| Standard | Vyhrazená | Yes | 
| Vyhrazená | Vyhrazená | Yes | 
| Vyhrazená | Standard | No | 

> [!NOTE]
> Obory názvů, které jsou ve stejném vyhrazeném clusteru, nelze spárovat. Obory názvů, které jsou v samostatných clusterech, můžete spárovat. 

## <a name="setup-and-failover-flow"></a>Nastavení a postup převzetí služeb při selhání

Následující část obsahuje přehled procesu převzetí služeb při selhání a vysvětluje, jak nastavit počáteční převzetí služeb při selhání. 

![1][]

### <a name="setup"></a>Nastavení

Nejprve vytvoříte nebo použijete existující primární obor názvů a nový sekundární obor názvů a potom oba dvojici. Toto párování vám poskytne alias, který můžete použít k připojení. Protože používáte alias, nemusíte měnit připojovací řetězce. Do párování převzetí služeb při selhání se dají přidat jenom nové obory názvů. 

1. Vytvořte primární obor názvů.
1. Vytvořte sekundární obor názvů v jiné oblasti. Tento krok je volitelný. Sekundární obor názvů můžete vytvořit při vytváření párování v dalším kroku. 
1. V Azure Portal přejděte k primárnímu oboru názvů.
1. V nabídce vlevo vyberte **geografické obnovení** a na panelu nástrojů vyberte **Zahájit párování** . 

    :::image type="content" source="./media/event-hubs-geo-dr/primary-namspace-initiate-pairing-button.png" alt-text="Iniciace párování z primárního oboru názvů":::    
1. Na stránce **Zahájit párování** proveďte tyto kroky:
    1. Vyberte existující sekundární obor názvů nebo ho vytvořte v jiné oblasti. V tomto příkladu je vybrán existující obor názvů.  
    1. Jako **alias** zadejte alias pro párování geografického Dr. 
    1. Potom vyberte **Vytvořit**. 

    :::image type="content" source="./media/event-hubs-geo-dr/initiate-pairing-page.png" alt-text="Vybrat sekundární obor názvů":::        
1. Měla by se zobrazit stránka **alias geografického Dr** . Můžete také přejít na tuto stránku z primárního oboru názvů tak, že v nabídce vlevo vyberete **geografické obnovení** .

    :::image type="content" source="./media/event-hubs-geo-dr/geo-dr-alias-page.png" alt-text="Stránka alias geografického DR":::    
1. Na stránce **alias geografického dru** vyberte v levé nabídce **zásady sdíleného přístupu** pro přístup k primárnímu připojovacímu řetězci pro daný alias. Místo přímého použití připojovacího řetězce k primárnímu nebo sekundárnímu oboru názvů použijte tento připojovací řetězec. 
1. Na této stránce **přehledu** můžete provádět následující akce: 
    1. Přerušit párování mezi primárními a sekundárními obory názvů. Na panelu nástrojů vyberte **přerušení párování** . 
    1. Ruční převzetí služeb při selhání sekundárnímu oboru názvů. Na panelu nástrojů vyberte **převzetí služeb při selhání** . 
    
        > [!WARNING]
        > Při selhání dojde k aktivaci sekundárního oboru názvů a odebrání primárního oboru názvů z párování obnovení Geo-Disaster. Vytvořte jiný obor názvů, abyste měli novou dvojici geografického zotavení po havárii. 

Nakonec byste měli přidat nějaké monitorování, abyste zjistili, jestli je převzetí služeb při selhání nezbytné. Ve většině případů je služba jednou ze velkých ekosystémů, takže automatické převzetí služeb při selhání je možné provést jenom v rámci synchronizace se zbývajícím subsystémem nebo infrastrukturou.

### <a name="example"></a>Příklad

V jednom z těchto scénářů zvažte řešení prodejního bodu (POS), které vysílá buď zprávy, nebo události. Event Hubs tyto události předá do některého řešení mapování nebo přeformátování, které pak předává namapovaná data do jiného systému pro další zpracování. V tomto okamžiku můžou být všechny tyto systémy hostované ve stejné oblasti Azure. Rozhodnutí o tom, kdy a jakou část převezme služby při selhání, závisí na toku dat ve vaší infrastruktuře. 

Převzetí služeb při selhání můžete automatizovat buď s monitorovacími systémy, nebo s vlastními řešeními monitorování. Tato automatizace ale má dodatečné plánování a práci, což není v rozsahu tohoto článku.

### <a name="failover-flow"></a>Postup převzetí služeb při selhání

Pokud zahájíte převzetí služeb při selhání, vyžadují se dva kroky:

1. Pokud dojde k dalšímu výpadku, budete chtít být schopni převzít služby po obnovení. Proto nastavte další pasivní obor názvů a aktualizujte párování. 

2. Vyžádat zprávy z bývalého primárního oboru názvů, jakmile budou opět k dispozici. Potom tento obor názvů použijte pro běžné zasílání zpráv mimo instalaci geografického obnovení, nebo odstraňte starý primární obor názvů.

> [!NOTE]
> Jsou podporovány pouze sémantiky při předání služeb při selhání. V tomto scénáři převezmete služby při selhání a pak znovu spárujte s novým oborem názvů. Navrácení služeb po obnovení není podporováno. například v clusteru SQL. 

![2][]

## <a name="management"></a>Správa

Pokud jste udělali chybu; například jste spároval nesprávné oblasti při počátečním nastavení, můžete kdykoli rozdělit párování obou oborů názvů. Pokud chcete používat spárované obory názvů jako běžné obory názvů, odstraňte alias.

## <a name="considerations"></a>Požadavky

Vezměte na vědomí následující skutečnosti:

1. V rámci návrhu Event Hubs geograficky zotavení po havárii nereplikují data, a proto nemůžete znovu použít starou hodnotu posunu primárního centra událostí v sekundárním centru událostí. K restartování přijímače událostí doporučujeme použít jednu z následujících metod:

   - *EventPosition. FromStart ()* – Pokud chcete číst všechna data v sekundárním centru událostí.
   - *EventPosition. FromEnd ()* – Pokud chcete číst všechna nová data z doby připojení k sekundárnímu centru událostí.
   - *EventPosition. FromEnqueuedTime (DateTime)* – Pokud chcete číst všechna data přijatá v sekundárním centru událostí počínaje od daného data a času.

2. Při plánování převzetí služeb při selhání byste měli také zvážit časový faktor. Pokud například ztratíte připojení po dobu delší než 15 až 20 minut, můžete se rozhodnout zahájit převzetí služeb při selhání. 
 
3. Skutečnost, že nejsou replikována žádná data znamená, že aktuální aktivní relace nebudou replikovány. Kromě toho nemusí fungovat duplicita duplicit a naplánované zprávy. Budou fungovat nové relace, naplánované zprávy a nové duplicity. 

4. Převzetí služeb při selhání přes složitou distribuovanou infrastrukturu by mělo být alespoň jednou [vyzkoušeno](/azure/architecture/reliability/disaster-recovery#disaster-recovery-plan) . 

5. Synchronizace entit může nějakou dobu trvat přibližně 50-100 entit za minutu.

## <a name="availability-zones"></a>Zóny dostupnosti 

SKU Event Hubs standard podporuje [zóny dostupnosti](../availability-zones/az-overview.md)a poskytuje umístění s izolací chyb v oblasti Azure. 

> [!NOTE]
> Podpora Zóny dostupnosti pro Azure Event Hubs Standard je dostupná jenom v [oblastech Azure](../availability-zones/az-region.md) , kde se nacházejí zóny dostupnosti.

Zóny dostupnosti můžete povolit jenom pro nové obory názvů pomocí Azure Portal. Event Hubs nepodporuje migraci stávajících oborů názvů. Po povolení v oboru názvů nemůžete zakázat redundanci zóny.

Když použijete zóny dostupnosti, metadata i data (události) se replikují napříč datovými centry v zóně dostupnosti. 

![3][]

## <a name="private-endpoints"></a>Privátní koncové body
V této části najdete další informace o použití geografického zotavení po havárii s obory názvů, které používají privátní koncové body. Další informace o používání privátních koncových bodů s Event Hubs obecně najdete v tématu [Konfigurace privátních koncových bodů](private-link-service.md).

### <a name="new-pairings"></a>Nové párování
Pokud se pokusíte vytvořit párování mezi primárním oborem názvů s privátním koncovým bodem a sekundárním oborem názvů bez privátního koncového bodu, párování selže. Párování bude úspěšné pouze v případě, že oba primární i sekundární obory názvů mají privátní koncové body. Doporučujeme použít stejné konfigurace na primárních a sekundárních oborech názvů a na virtuálních sítích, ve kterých jsou vytvořeny privátní koncové body.  

> [!NOTE]
> Když se pokusíte spárovat primární obor názvů se soukromým koncovým bodem a sekundárním oborem názvů, proces ověření kontroluje pouze to, zda privátní koncový bod existuje v sekundárním oboru názvů. Nekontroluje, jestli koncový bod funguje nebo bude po převzetí služeb při selhání fungovat. Je vaše zodpovědnost za to, že sekundární obor názvů s privátním koncovým bodem bude po převzetí služeb při selhání fungovat podle očekávání.
>
> Pokud chcete otestovat, jestli jsou konfigurace privátních koncových bodů stejné na primárních a sekundárních oborech názvů, pošlete žádost o čtení (třeba [získat centrum událostí](/rest/api/eventhub/get-event-hub)) do sekundárního oboru názvů mimo virtuální síť a ověřte, že se od této služby zobrazila chybová zpráva.

### <a name="existing-pairings"></a>Existující párování
Pokud párování mezi primárním a sekundárním oborem názvů už existuje, vytvoření privátního koncového bodu na primárním oboru názvů se nezdaří. Chcete-li tento problém vyřešit, vytvořte nejprve privátní koncový bod v sekundárním oboru názvů a potom jej vytvořte pro primární obor názvů.

> [!NOTE]
> I když povolujeme přístup jen pro čtení k sekundárnímu oboru názvů, jsou povoleny aktualizace konfigurací privátního koncového bodu. 

### <a name="recommended-configuration"></a>Doporučená konfigurace
Při vytváření konfigurace zotavení po havárii pro vaše aplikace a Event Hubs obory názvů je nutné vytvořit privátní koncové body pro primární i sekundární obory názvů Event Hubs na virtuálních sítích hostujících primární i sekundární instanci vaší aplikace. 

Řekněme, že máte dvě virtuální sítě: VNET-1, VNET-2 a tyto primární a sekundární obory názvů: EventHubs-Namespace1-Primary, EventHubs-Namespace2-Secondary. Je třeba provést následující kroky: 

- V EventHubs-Namespace1-Primary vytvořte dva privátní koncové body, které používají podsítě z VNET-1 a VNET-2.
- V EventHubs-Namespace2-Secondary vytvořte dva privátní koncové body, které používají stejné podsítě z VNET-1 a VNET-2. 

![Privátní koncové body a virtuální sítě](./media/event-hubs-geo-dr/private-endpoints-virtual-networks.png)

Výhodou tohoto přístupu je, že k převzetí služeb při selhání může dojít v aplikační vrstvě nezávisle na Event Hubs oboru názvů. Zvažte následující scénáře: 

**Převzetí služeb při selhání jen pro aplikace:** V tomto případě aplikace nebude existovat v VNET-1, ale přesune se do sítě VNET-2. Jak jsou oba privátní koncové body konfigurovány pro virtuální i sekundární obory názvů VNET-1 i VNET-2, bude aplikace pracovat pouze. 

**Event Hubs převzetí služeb při selhání jen pro obor názvů**: tady se nakonfigurují oba privátní koncové body v obou virtuálních sítích pro primární i sekundární obory názvů. aplikace bude jenom fungovat. 

> [!NOTE]
> Pokyny pro obnovení geografických havárií virtuální sítě najdete v tématu [Virtual Network – provozní kontinuita](../virtual-network/virtual-network-disaster-recovery-guidance.md).
 
## <a name="next-steps"></a>Další kroky
Přečtěte si následující ukázky nebo referenční dokumentaci. 
- [Ukázka .NET GeoDR](https://github.com/Azure/azure-event-hubs/tree/master/samples/Management/DotNet/GeoDRClient) 
- [Ukázka Java GeoDR](https://github.com/Azure-Samples/eventhub-java-manage-event-hub-geo-disaster-recovery)
- [Ukázky .NET – Azure. Messaging. EventHubs](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs/samples)
- [Ukázky pro .NET – Microsoft. Azure. EventHubs](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet)
- [Java – Azure-zasílání zpráv – ukázky eventhubs](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs)
- [Ukázky pro Java – Azure – eventhubs](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java)
- [Ukázky Pythonu](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples)
- [Ukázky JavaScriptu](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/javascript)
- [Ukázky TypeScriptu](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/typescript)
- [REST API – referenční informace](/rest/api/eventhub/)

[1]: ./media/event-hubs-geo-dr/geo1.png
[2]: ./media/event-hubs-geo-dr/geo2.png
[3]: ./media/event-hubs-geo-dr/eh-az.png
