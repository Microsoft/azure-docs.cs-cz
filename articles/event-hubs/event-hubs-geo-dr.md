---
title: Azure Event Hubs geo-zotavení po havárii | Dokumentace Microsoftu
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
ms.date: 08/14/2018
ms.author: shvija
ms.openlocfilehash: 89ecfd93c22fe72fee3befd8d4a722eb41d816af
ms.sourcegitcommit: b5ac31eeb7c4f9be584bb0f7d55c5654b74404ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/23/2018
ms.locfileid: "42747167"
---
# <a name="azure-event-hubs-geo-disaster-recovery"></a>Azure Event Hubs Geo-zotavení po havárii

Po celé oblasti Azure nebo datových centrech (Pokud ne [zóny dostupnosti](../availability-zones/az-overview.md) se používají) dojít k výpadku, je velmi důležité pro zpracování dat i nadále fungovat v jiné oblasti nebo datového centra. V důsledku toho *zotavení po havárii geograficky* a *geografickou replikaci* jsou důležité funkce pro všechny podniky. Azure Event Hubs podporuje geo-zotavení po havárii a geografická replikace, na úrovni oboru názvů. 

Funkce zotavení po havárii geograficky je globálně dostupná pro standardní SKU Event Hubs.

## <a name="outages-and-disasters"></a>Výpadků a havárií

Je důležité si uvědomit rozdíl mezi "výpadků" a "havárií." *Výpadek* je dočasná nedostupnost služby Azure Event Hubs a může mít vliv na některé součásti služby, jako je například úložiště, nebo dokonce celé datové centrum. Ale po byl problém vyřešen, Event Hubs opět k dispozici. Kvůli výpadku obvykle nezpůsobí ztrátu zprávy nebo jiná data. Příkladem takových výpadek může být výpadek napájení v datovém centru. Některé výpadky jsou pouze krátkou připojení ztráty kvůli problémům s přechodným nebo sítě. 

A *po havárii* je definován jako trvalý, nebo dlouhodobější ztráty clusteru služby Event Hubs, Azure oblasti nebo datového centra. Oblasti nebo datového centra může nebo nemusí opět k dispozici nebo je pravděpodobně mimo provoz pro hodiny nebo i dny. Příkladem takové jiného problému ovlivňujícího jsou fire, zahlcení nebo zemětřesení. Po havárii, který se stane trvalé může způsobit ztrátu některé zprávy, události nebo jiná data. Ale ve většině případů by měl být nulové ztráty a zprávy lze obnovit zálohování po datové centrum.

Funkce zotavení po havárii geograficky služby Azure Event Hubs je řešení pro zotavení po havárii. Koncepty a pracovní postup popsaný v tomto článku použít scénáře po havárii a není přechodná nebo dočasné výpadky. Podrobnou diskuzi o zotavení po havárii v Microsoft Azure, najdete v části [v tomto článku](/azure/architecture/resiliency/disaster-recovery-azure-applications).

## <a name="basic-concepts-and-terms"></a>Základními koncepcemi a termíny

Funkce zotavení po havárii implementuje zotavení po havárii metadata a spoléhá na obory názvů zotavení po havárii primární a sekundární. Všimněte si, že je k dispozici pro funkce zotavení po havárii geograficky [standardní SKU](https://azure.microsoft.com/pricing/details/event-hubs/) pouze. Není potřeba nic měnit připojovací řetězec, protože připojení se provádí prostřednictvím alias.

V tomto článku se používají následující termíny:

-  *Alias*: název pro konfigurace zotavení po havárii, které jste nastavili. Alias poskytuje jeden řetězec připojení stabilní plně kvalifikovaný název domény (FQDN). Aplikace použít tento připojovací řetězec aliasu pro připojení k oboru názvů. 

-  *Obor názvů primárního a sekundárního*: obory názvů, které odpovídají na alias. Primární obor názvů je "aktivní" a přijímá zprávy (to může být existující nebo nový obor názvů). Sekundární obor názvů je "pasivní" a nepřijímá zprávy. Metadata mezi oběma jsou synchronizované, tak i hladce přijmout zprávy bez nutnosti jakkoli měnit aplikace kódu nebo připojovací řetězec. Aby bylo zajištěno, že pouze aktivní obor názvů přijímá zprávy, musíte použít alias. 

-  *Metadata*: entity jako jsou event hubs a skupiny uživatelů a jejich vlastnosti služby, které jsou spojeny s oborem názvů. Všimněte si, že se automaticky replikují jenom entity a jejich nastavení. Zprávy a události se nereplikují. 

-  *Převzetí služeb při selhání*: proces aktivace sekundární obor názvů.

## <a name="setup-and-failover-flow"></a>Instalační program a převzetí služeb při selhání toku

Následující části je uveden přehled procesu převzetí služeb při selhání a vysvětluje, jak nastavit počáteční převzetí služeb při selhání. 

![1][]

### <a name="setup"></a>Nastavení

Můžete nejprve vytvořit, nebo použijte existující primární obor názvů a nový sekundární obor názvů, a spárujte dvě. Toto párování získáte alias, který používáte pro připojení. Vzhledem k tomu, že používáte alias, není nutné změnit připojovací řetězce. Nové obory názvů lze přidat na váš párování převzetí služeb při selhání. A konečně měli byste přidat, některá monitorování ke zjištění, pokud je nutné převzetí služeb při selhání. Ve většině případů je jednou ze součástí sady rozsáhlého ekosystému služby, proto jsou automatické převzetí služeb při selhání jen zřídka je to možné, jak často převzetí služeb při selhání se musí provádět synchronizované s zbývající subsystému nebo infrastruktury.

### <a name="example"></a>Příklad:

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

[Ukázka na Githubu](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/GeoDRClient) ukazuje, jak nastavit a spustit převzetí služeb při selhání. Tato ukázka demonstruje následující pojmy:

- Nastavení v Azure Active Directory pro použití Azure Resource Manageru pomocí služby Event Hubs. 
- Kroky potřebné ke spuštění vzorového kódu. 
- Odesílání a příjem z aktuální primární obor názvů. 

## <a name="considerations"></a>Požadavky

Mějte na paměti následující aspekty brát v úvahu v této vydané verzi:

1. Při plánování převzetí služeb při selhání, měli byste také zvážit faktor čas. Například pokud ztratíte připojení po dobu delší než 15 až 20 minut, můžete se rozhodnout k zahájení převzetí služeb při selhání. 
 
2. Fakt, že žádná data se replikují znamená, že momentálně se nereplikují aktivní relace. Plánované zprávy a duplicit navíc nemusí fungovat. Nové relace, plánované zprávy a nové duplikáty budou fungovat. 

3. Přebírání služeb při selhání komplexní distribuované infrastruktury by měla být [vyzkoušená](/azure/architecture/resiliency/disaster-recovery-azure-applications#disaster-simulation) alespoň jednou. 

4. Synchronizace entit může trvat nějakou dobu, přibližně 50 – 100 entit za minutu.

## <a name="availability-zones-preview"></a>Zóny dostupnosti (preview)

Event Hubs standardní skladová jednotka podporuje také [zóny dostupnosti](../availability-zones/az-overview.md), poskytuje umístění s izolací chyb v rámci oblasti Azure. 

> [!NOTE]
> Ve verzi preview zón dostupnosti je podporována pouze v **USA (střed)**, **USA – východ 2**, a **Francie – střed** oblastech.

Zóny dostupnosti můžete povolit na pouze nové obory názvů pomocí webu Azure portal. Event Hubs nepodporuje migraci z existující oborů názvů. Po povolení na váš obor názvů nejde zakázat redundanci zón.

![3][]

## <a name="next-steps"></a>Další postup

* [Ukázka na Githubu](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/GeoDRClient) provede jednoduchý pracovní postup, který vytvoří geograficky párování a iniciuje převzetí služeb při selhání pro scénář zotavení po havárii.
* [Reference k rozhraní REST API](/rest/api/eventhub/disasterrecoveryconfigs) popisuje rozhraní API pro provádění konfigurace zotavení po havárii Geo.

Další informace o službě Event Hubs naleznete pod těmito odkazy:

* Úvodní [Kurz služby Event Hubs](event-hubs-dotnet-standard-getstarted-send.md)
* [Nejčastější dotazy k Event Hubs](event-hubs-faq.md)
* [Ukázkové aplikace, které používají službu Event Hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples)

[1]: ./media/event-hubs-geo-dr/geo1.png
[2]: ./media/event-hubs-geo-dr/geo2.png
[3]: ./media/event-hubs-geo-dr/eh-az.png