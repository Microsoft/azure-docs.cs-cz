---
title: Geografické zotavení po havárii – Azure Event Hubs | Microsoft Docs
description: Použití geografických oblastí k převzetí služeb při selhání a zotavení po havárii v Azure Event Hubs
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
ms.openlocfilehash: 61318fbccdf92c6502aa8b2236d8b234cec67668
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "82209141"
---
# <a name="azure-event-hubs---geo-disaster-recovery"></a>Azure Event Hubs – geografická zotavení po havárii 

Při výpadku provozu celé oblasti Azure nebo Datacenter (Pokud se nepoužívají žádné [zóny dostupnosti](../availability-zones/az-overview.md) ) je důležité, aby zpracování dat pokračovalo v práci v jiné oblasti nebo datacentru. V takovém případě *geografické zotavení po havárii* a *geografická replikace* jsou důležité funkce pro všechny podniky. Azure Event Hubs podporuje jak geografické zotavení po havárii, tak i geografickou replikaci na úrovni oboru názvů. 

> [!NOTE]
> Funkce geografického zotavení po havárii je dostupná jenom pro [standardní a vyhrazené SKU](https://azure.microsoft.com/pricing/details/event-hubs/).  

## <a name="outages-and-disasters"></a>Výpadky a havárie

Je důležité poznamenat rozdíl mezi "výpadky" a "katastrofami". *Výpadek* je dočasná nedostupnost Azure Event Hubs a může ovlivnit některé součásti služby, jako je třeba úložiště pro zasílání zpráv nebo i celé datacentrum. Po vyřešení problému však bude Event Hubs opět k dispozici. Výpadky obvykle nezpůsobí ztrátu zpráv nebo jiných dat. Příkladem takového výpadku může být výpadek napájení v datovém centru. Některé výpadky představují jenom krátké ztráty připojení kvůli přechodným nebo síťovým problémům. 

*Havárie* se definuje jako trvalá nebo dlouhodobá ztráta Event Hubs clusteru, oblasti Azure nebo datového centra. Oblast nebo datové centrum může nebo nemusí být k dispozici znovu nebo může být vypnuté hodiny nebo dny. Příklady takových katastrof jsou požáry, zahlcení nebo zemětřesení. Havárie, která se stala trvalo, může způsobit ztrátu některých zpráv, událostí nebo jiných dat. Ve většině případů by ale nemělo dojít ke ztrátě dat a po zálohování datového centra se dají obnovit zprávy.

Funkce geografického zotavení po havárii v Azure Event Hubs je řešení zotavení po havárii. Koncepty a pracovní postupy popsané v tomto článku se týkají scénářů po havárii a nepřechodných nebo dočasných výpadků. Podrobnou diskuzi o zotavení po havárii v Microsoft Azure najdete v [tomto článku](/azure/architecture/resiliency/disaster-recovery-azure-applications).

## <a name="basic-concepts-and-terms"></a>Základní pojmy a pojmy

Funkce zotavení po havárii implementuje zotavení po havárii metadat a závisí na primárních a sekundárních oborech názvů pro zotavení po havárii. 

Funkce geografického zotavení po havárii je dostupná jenom pro [standardní a vyhrazené SKU](https://azure.microsoft.com/pricing/details/event-hubs/) . Nemusíte dělat žádné změny připojovacího řetězce, protože připojení se provádí pomocí aliasu.

V tomto článku se používají následující výrazy:

-  *Alias*: název pro konfiguraci zotavení po havárii, kterou jste nastavili. Alias poskytuje jediný stabilní řetězec plně kvalifikovaného názvu domény (FQDN). Aplikace používají tento připojovací řetězec aliasu pro připojení k oboru názvů. 

-  *Primární nebo sekundární obor názvů*: obory názvů, které odpovídají aliasu. Primární obor názvů je "aktivní" a přijímá zprávy (může to být existující nebo nový obor názvů). Sekundární obor názvů je "pasivní" a nepřijímá zprávy. Metadata mezi oběma je synchronizována, takže obě můžou bezproblémově přijímat zprávy bez nutnosti změny kódu aplikace nebo připojovacího řetězce. Chcete-li zajistit, že pouze aktivní obor názvů přijímá zprávy, je nutné použít alias. 

-  *Metadata*: entity, jako jsou centra událostí a skupiny uživatelů; a jejich vlastnosti služby, které jsou přidruženy k oboru názvů. Všimněte si, že se automaticky replikují jenom entity a jejich nastavení. Zprávy a události nejsou replikovány. 

-  *Převzetí služeb při selhání*: proces aktivace sekundárního oboru názvů.

## <a name="supported-namespace-pairs"></a>Podporované páry oboru názvů
Podporovány jsou následující kombinace primárních a sekundárních oborů názvů:  

| Primární obor názvů | Sekundární obor názvů | Podporuje se | 
| ----------------- | -------------------- | ---------- |
| Standard | Standard | Ano | 
| Standard | Vyhrazená | Ano | 
| Vyhrazená | Vyhrazená | Ano | 
| Vyhrazená | Standard | Ne | 

> [!NOTE]
> Obory názvů, které jsou ve stejném vyhrazeném clusteru, nelze spárovat. Obory názvů, které jsou v samostatných clusterech, můžete spárovat. 

## <a name="setup-and-failover-flow"></a>Nastavení a postup převzetí služeb při selhání

Následující část obsahuje přehled procesu převzetí služeb při selhání a vysvětluje, jak nastavit počáteční převzetí služeb při selhání. 

![1][]

### <a name="setup"></a>Nastavení

Nejprve vytvoříte nebo použijete existující primární obor názvů a nový sekundární obor názvů a potom oba dvojici. Toto párování vám poskytne alias, který můžete použít k připojení. Protože používáte alias, nemusíte měnit připojovací řetězce. Do párování převzetí služeb při selhání se dají přidat jenom nové obory názvů. Nakonec byste měli přidat nějaké monitorování, abyste zjistili, jestli je převzetí služeb při selhání nezbytné. Ve většině případů je služba jednou ze velkých ekosystémů, takže automatické převzetí služeb při selhání je možné jen zřídka, protože velmi často se musí provádět převzetí služeb při selhání v synchronizaci se zbývajícím subsystémem nebo infrastrukturou.

### <a name="example"></a>Příklad

V jednom z těchto scénářů zvažte řešení prodejního bodu (POS), které vysílá buď zprávy, nebo události. Event Hubs tyto události předá do některého řešení mapování nebo přeformátování, které pak předává namapovaná data do jiného systému pro další zpracování. V tomto okamžiku můžou být všechny tyto systémy hostované ve stejné oblasti Azure. Rozhodnutí o tom, kdy a jakou část služby převezme služby při selhání, závisí na toku dat ve vaší infrastruktuře. 

Převzetí služeb při selhání můžete automatizovat buď s monitorovacími systémy, nebo s vlastními řešeními monitorování. Tato automatizace ale má dodatečné plánování a práci, což není v rozsahu tohoto článku.

### <a name="failover-flow"></a>Postup převzetí služeb při selhání

Pokud zahájíte převzetí služeb při selhání, vyžadují se dva kroky:

1. Pokud dojde k dalšímu výpadku, budete chtít znovu provést převzetí služeb při selhání. Proto nastavte další pasivní obor názvů a aktualizujte párování. 

2. Vyžádat zprávy z bývalého primárního oboru názvů, jakmile budou opět k dispozici. Potom tento obor názvů použijte pro běžné zasílání zpráv mimo instalaci geografického obnovení, nebo odstraňte starý primární obor názvů.

> [!NOTE]
> Jsou podporovány pouze sémantiky při předání služeb při selhání. V tomto scénáři převezmete služby při selhání a pak znovu spárujte s novým oborem názvů. Navrácení služeb po obnovení není podporováno. například v clusteru SQL. 

![2][]

## <a name="management"></a>Správa

Pokud jste udělali chybu; například jste spároval nesprávné oblasti při počátečním nastavení, můžete kdykoli rozdělit párování obou oborů názvů. Pokud chcete používat spárované obory názvů jako běžné obory názvů, odstraňte alias.

## <a name="samples"></a>ukázky

[Ukázka na GitHubu](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/GeoDRClient) ukazuje, jak nastavit a iniciovat převzetí služeb při selhání. Tato ukázka demonstruje následující koncepty:

- Nastavení požadovaná v Azure Active Directory pro použití Azure Resource Manager s Event Hubs. 
- Postup potřebný ke spuštění ukázkového kódu. 
- Odeslat a přijmout z aktuálního primárního oboru názvů 

## <a name="considerations"></a>Požadavky

Vezměte v úvahu následující skutečnosti:

1. V rámci návrhu Event Hubs geograficky zotavení po havárii nereplikují data, a proto nemůžete znovu použít starou hodnotu posunu primárního centra událostí v sekundárním centru událostí. K restartování přijímače událostí doporučujeme použít jednu z následujících možností:

- *EventPosition. FromStart ()* – Pokud chcete číst všechna data v sekundárním centru událostí.
- *EventPosition. FromEnd ()* – Pokud chcete číst všechna nová data z doby připojení k sekundárnímu centru událostí.
- *EventPosition. FromEnqueuedTime (DateTime)* – Pokud chcete číst všechna data přijatá v sekundárním centru událostí počínaje od daného data a času.

2. Při plánování převzetí služeb při selhání byste měli také zvážit časový faktor. Pokud například ztratíte připojení po dobu delší než 15 až 20 minut, můžete se rozhodnout zahájit převzetí služeb při selhání. 
 
3. Skutečnost, že žádná data nejsou replikována znamená, že aktuálně aktivní relace nebudou replikovány. Kromě toho nemusí fungovat duplicita duplicit a naplánované zprávy. Budou fungovat nové relace, naplánované zprávy a nové duplicity. 

4. Převzetí služeb při selhání přes složitou distribuovanou infrastrukturu by mělo být alespoň jednou [vyzkoušeno](/azure/architecture/reliability/disaster-recovery#disaster-recovery-plan) . 

5. Synchronizace entit může nějakou dobu trvat přibližně 50-100 entit za minutu.

## <a name="availability-zones"></a>Zóny dostupnosti 

SKU Event Hubs standard podporuje [zóny dostupnosti](../availability-zones/az-overview.md)a poskytuje umístění s izolací chyb v oblasti Azure. 

> [!NOTE]
> Podpora Zóny dostupnosti pro Azure Event Hubs Standard je dostupná jenom v [oblastech Azure](../availability-zones/az-region.md) , kde se nacházejí zóny dostupnosti.

Zóny dostupnosti můžete povolit jenom pro nové obory názvů pomocí Azure Portal. Event Hubs nepodporuje migraci stávajících oborů názvů. Po povolení v oboru názvů nelze zakázat redundanci zóny.

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
