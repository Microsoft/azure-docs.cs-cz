---
title: Zotavení po havárii GEO - Azure Event Hubs | Dokumentace Microsoftu
description: Použití geografických oblastí na převzetí služeb při selhání a provádět zotavení po havárii ve službě Azure Event Hubs
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 40db6e9f429569bc19641aa5f0f371f287db7b18
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79281467"
---
# <a name="azure-event-hubs---geo-disaster-recovery"></a>Azure Event Hubs – Geo-zotavení po havárii 

Při výpadku provozu celé oblasti Azure nebo Datacenter (Pokud se nepoužívají žádné [zóny dostupnosti](../availability-zones/az-overview.md) ) je důležité, aby zpracování dat pokračovalo v práci v jiné oblasti nebo datacentru. V takovém případě *geografické zotavení po havárii* a *geografická replikace* jsou důležité funkce pro všechny podniky. Azure Event Hubs podporuje geo-zotavení po havárii a geografická replikace, na úrovni oboru názvů. 

> [!NOTE]
> Funkce geografického zotavení po havárii je dostupná jenom pro [standardní a vyhrazené SKU](https://azure.microsoft.com/pricing/details/event-hubs/).  

## <a name="outages-and-disasters"></a>Výpadků a havárií

Je důležité si uvědomit rozdíl mezi "výpadků" a "havárií." *Výpadek* je dočasná nedostupnost Azure Event Hubs a může ovlivnit některé součásti služby, jako je třeba úložiště pro zasílání zpráv nebo i celé datacentrum. Ale po byl problém vyřešen, Event Hubs opět k dispozici. Kvůli výpadku obvykle nezpůsobí ztrátu zprávy nebo jiná data. Příkladem takových výpadek může být výpadek napájení v datovém centru. Některé výpadky jsou pouze krátkou připojení ztráty kvůli problémům s přechodným nebo sítě. 

*Havárie* se definuje jako trvalá nebo dlouhodobá ztráta Event Hubs clusteru, oblasti Azure nebo datového centra. Oblasti nebo datového centra může nebo nemusí opět k dispozici nebo je pravděpodobně mimo provoz pro hodiny nebo i dny. Příkladem takové jiného problému ovlivňujícího jsou fire, zahlcení nebo zemětřesení. Po havárii, který se stane trvalé může způsobit ztrátu některé zprávy, události nebo jiná data. Ale ve většině případů by měl být nulové ztráty a zprávy lze obnovit zálohování po datové centrum.

Funkce zotavení po havárii geograficky služby Azure Event Hubs je řešení pro zotavení po havárii. Koncepty a pracovní postup popsaný v tomto článku použít scénáře po havárii a není přechodná nebo dočasné výpadky. Podrobnou diskuzi o zotavení po havárii v Microsoft Azure najdete v [tomto článku](/azure/architecture/resiliency/disaster-recovery-azure-applications).

## <a name="basic-concepts-and-terms"></a>Základními koncepcemi a termíny

Funkce zotavení po havárii implementuje zotavení po havárii metadata a spoléhá na obory názvů zotavení po havárii primární a sekundární. 

Funkce geografického zotavení po havárii je dostupná jenom pro [standardní a vyhrazené SKU](https://azure.microsoft.com/pricing/details/event-hubs/) . Není potřeba nic měnit připojovací řetězec, protože připojení se provádí prostřednictvím alias.

V tomto článku se používají následující termíny:

-  *Alias*: název pro konfiguraci zotavení po havárii, kterou jste nastavili. Alias poskytuje jeden řetězec připojení stabilní plně kvalifikovaný název domény (FQDN). Aplikace použít tento připojovací řetězec aliasu pro připojení k oboru názvů. 

-  *Primární nebo sekundární obor názvů*: obory názvů, které odpovídají aliasu. Primární obor názvů je "aktivní" a přijímá zprávy (to může být existující nebo nový obor názvů). Sekundární obor názvů je "pasivní" a nepřijímá zprávy. Metadata mezi oběma jsou synchronizované, tak i hladce přijmout zprávy bez nutnosti jakkoli měnit aplikace kódu nebo připojovací řetězec. Aby bylo zajištěno, že pouze aktivní obor názvů přijímá zprávy, musíte použít alias. 

-  *Metadata*: entity, jako jsou centra událostí a skupiny uživatelů; a jejich vlastnosti služby, které jsou přidruženy k oboru názvů. Všimněte si, že se automaticky replikují jenom entity a jejich nastavení. Zprávy a události se nereplikují. 

-  *Převzetí služeb při selhání*: proces aktivace sekundárního oboru názvů.

## <a name="supported-namespace-pairs"></a>Podporované páry oboru názvů
Podporovány jsou následující kombinace primárních a sekundárních oborů názvů:  

| Primární obor názvů | Sekundární obor názvů | Podporuje se | 
| ----------------- | -------------------- | ---------- |
| Standardní | Standardní | Ano | 
| Standardní | Vyhrazený | Ano | 
| Vyhrazený | Vyhrazený | Ano | 
| Vyhrazený | Standardní | Ne | 

> [!NOTE]
> Obory názvů, které jsou ve stejném vyhrazeném clusteru, nelze spárovat. Obory názvů, které jsou v samostatných clusterech, můžete spárovat. 

## <a name="setup-and-failover-flow"></a>Instalační program a převzetí služeb při selhání toku

Následující části je uveden přehled procesu převzetí služeb při selhání a vysvětluje, jak nastavit počáteční převzetí služeb při selhání. 

![1][]

### <a name="setup"></a>Nastavení

Můžete nejprve vytvořit, nebo použijte existující primární obor názvů a nový sekundární obor názvů, a spárujte dvě. Toto párování získáte alias, který používáte pro připojení. Vzhledem k tomu, že používáte alias, není nutné změnit připojovací řetězce. Nové obory názvů lze přidat na váš párování převzetí služeb při selhání. A konečně měli byste přidat, některá monitorování ke zjištění, pokud je nutné převzetí služeb při selhání. Ve většině případů je jednou ze součástí sady rozsáhlého ekosystému služby, proto jsou automatické převzetí služeb při selhání jen zřídka je to možné, jak často převzetí služeb při selhání se musí provádět synchronizované s zbývající subsystému nebo infrastruktury.

### <a name="example"></a>Příklad

V příkladem tohoto scénáře vezměte v úvahu bodu prodej (POS) řešení, které generuje zprávy nebo události. Event Hubs se předá tyto události některé mapování nebo přeformátování řešení, které potom předává mapovaná data do jiného systému pro další zpracování. V tu chvíli se všech těchto systémech může být hostovaná ve stejné oblasti Azure. Rozhodnutí o a jaká část převzít služby při selhání závisí na toku dat ve vaší infrastruktuře. 

Můžete automatizovat převzetí služeb při selhání se systémy pro monitorování nebo s vlastními silami sestavených řešení monitorování. Tato automatizace však trvá dodatečné plánování a činnosti, což je mimo rámec tohoto článku.

### <a name="failover-flow"></a>Tok převzetí služeb při selhání

Pokud spustíte převzetí služeb při selhání, jsou požadovány dva kroky:

1. Pokud dojde k jinému výpadku, chcete být schopni převzetí služeb při selhání znovu. Proto nastavit jiný obor názvů pasivní a aktualizujte párování. 

2. Jakmile je opět k dispozici vytahují zprávy z předchozí primární obor názvů. Potom použijte tento obor názvů pro zasílání zpráv regulární mimo nastavení geografického zotavení nebo odstraňte starý primární obor názvů.

> [!NOTE]
> Pouze dopředné sémantiku selhání jsou podporovány. V tomto scénáři převzetí služeb při selhání a potom znovu spárovat nový obor názvů. Navrácení služeb po obnovení není podporována. například v clusteru serveru SQL. 

![2][]

## <a name="management"></a>Správa

Pokud se jedná o chybu; například spárované nesprávné oblasti při počátečním nastavení, můžete přerušit párování dané dva obory názvů v každém okamžiku. Pokud chcete použít jako regulární obory názvů spárované obory názvů, odstraňte alias.

## <a name="samples"></a>Ukázky

[Ukázka na GitHubu](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/GeoDRClient) ukazuje, jak nastavit a iniciovat převzetí služeb při selhání. Tato ukázka demonstruje následující pojmy:

- Nastavení v Azure Active Directory pro použití Azure Resource Manageru pomocí služby Event Hubs. 
- Kroky potřebné ke spuštění vzorového kódu. 
- Odesílání a příjem z aktuální primární obor názvů. 

## <a name="considerations"></a>Požadavky

Mějte na paměti následující aspekty brát v úvahu v této vydané verzi:

1. V rámci návrhu Event Hubs geograficky zotavení po havárii nereplikují data, a proto nemůžete znovu použít starou hodnotu posunu primárního centra událostí v sekundárním centru událostí. K restartování přijímače událostí doporučujeme použít jednu z následujících možností:

- *EventPosition. FromStart ()* – Pokud chcete číst všechna data v sekundárním centru událostí.
- *EventPosition. FromEnd ()* – Pokud chcete číst všechna nová data z doby připojení k sekundárnímu centru událostí.
- *EventPosition. FromEnqueuedTime (DateTime)* – Pokud chcete číst všechna data přijatá v sekundárním centru událostí počínaje od daného data a času.

2. Při plánování převzetí služeb při selhání, měli byste také zvážit faktor čas. Například pokud ztratíte připojení po dobu delší než 15 až 20 minut, můžete se rozhodnout k zahájení převzetí služeb při selhání. 
 
3. Fakt, že žádná data se replikují znamená, že momentálně se nereplikují aktivní relace. Plánované zprávy a duplicit navíc nemusí fungovat. Nové relace, plánované zprávy a nové duplikáty budou fungovat. 

4. Převzetí služeb při selhání přes složitou distribuovanou infrastrukturu by mělo být alespoň jednou [vyzkoušeno](/azure/architecture/reliability/disaster-recovery#disaster-recovery-plan) . 

5. Synchronizace entit může trvat nějakou dobu, přibližně 50 – 100 entit za minutu.

## <a name="availability-zones"></a>Zóny dostupnosti 

SKU Event Hubs standard podporuje [zóny dostupnosti](../availability-zones/az-overview.md)a poskytuje umístění s izolací chyb v oblasti Azure. 

> [!NOTE]
> Podpora Zóny dostupnosti pro Azure Event Hubs Standard je dostupná jenom v [oblastech Azure](../availability-zones/az-overview.md#services-support-by-region) , kde se nacházejí zóny dostupnosti.

Zóny dostupnosti můžete povolit na pouze nové obory názvů pomocí webu Azure portal. Event Hubs nepodporuje migraci z existující oborů názvů. Po povolení na váš obor názvů nejde zakázat redundanci zón.

![3][]

## <a name="next-steps"></a>Další kroky

* [Ukázka na GitHubu](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/GeoDRClient) vás provede jednoduchým pracovním postupem, který vytvoří geografickou spárování a zahájí převzetí služeb při selhání ve scénáři zotavení po havárii.
* [Odkazy na REST API](/rest/api/eventhub/disasterrecoveryconfigs) popisují rozhraní API pro konfiguraci obnovení geografického zotavení po havárii.

Další informace o službě Event Hubs naleznete pod těmito odkazy:

- Začínáme se službou Event Hubs
    - [.NET Core](get-started-dotnet-standard-send-v2.md)
    - [Java](get-started-java-send-v2.md)
    - [Python](get-started-python-send-v2.md)
    - [JavaScript](get-started-java-send-v2.md)
* [Nejčastější dotazy k Event Hubs](event-hubs-faq.md)
* [Ukázkové aplikace, které používají službu Event Hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples)

[1]: ./media/event-hubs-geo-dr/geo1.png
[2]: ./media/event-hubs-geo-dr/geo2.png
[3]: ./media/event-hubs-geo-dr/eh-az.png
