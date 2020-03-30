---
title: Geografické zotavení po havárii – Azure Event Hubs| Dokumenty společnosti Microsoft
description: Jak používat geografické oblasti k převzetí služeb při selhání a provádět zotavení po havárii v azure event hubs
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281467"
---
# <a name="azure-event-hubs---geo-disaster-recovery"></a>Azure Event Hubs – geografické zotavení po havárii 

Když se v ypracují prostoje celé oblasti Azure nebo datových center (pokud se nepoužívají žádné [zóny dostupnosti),](../availability-zones/az-overview.md) je důležité, aby zpracování dat dál fungovalo v jiné oblasti nebo datovém centru. Jako takové *geografické zotavení po havárii* a *geografická replikace* jsou důležité funkce pro všechny podniky. Azure Event Hubs podporuje geografické zotavení po havárii i geografickou replikaci na úrovni oboru názvů. 

> [!NOTE]
> Funkce geografického zotavení po havárii je k dispozici pouze pro [standardní a vyhrazené sloky](https://azure.microsoft.com/pricing/details/event-hubs/).  

## <a name="outages-and-disasters"></a>Výpadky a katastrofy

Je důležité si uvědomit rozdíl mezi "výpadky" a "katastrofy". *Výpadek* je dočasná nedostupnost Centra událostí Azure a může ovlivnit některé součásti služby, jako je například úložiště zpráv nebo dokonce celé datové centrum. Po ohlášení problému však centra událostí budou znovu k dispozici. Výpadek obvykle nezpůsobí ztrátu zpráv nebo jiných dat. Příkladem takového výpadku může být výpadku napájení v datovém centru. Některé výpadky jsou pouze krátké ztráty připojení z důvodu přechodných nebo problémů se sítí. 

Katastrofa *disaster* je definována jako trvalá nebo dlouhodobá ztráta clusteru Event Hubs, oblasti Azure nebo datového centra. Oblast nebo datové centrum může nebo nemusí být znovu k dispozici nebo může být vypnuto po celé hodiny nebo dny. Příkladem takových katastrof jsou požár, záplavy nebo zemětřesení. Havárie, která se stane trvalou, může způsobit ztrátu některých zpráv, událostí nebo jiných dat. Ve většině případů by však neměla být žádná ztráta dat a zprávy lze obnovit po zálohování datového centra.

Funkce geografického zotavení po havárii služby Azure Event Hubs je řešení pro zotavení po havárii. Koncepty a pracovní postup popsané v tomto článku platí pro scénáře katastrofy a nikoli pro přechodné nebo dočasné výpadky. Podrobnou diskusi o zotavení po havárii v Microsoft Azure najdete v [tomto článku](/azure/architecture/resiliency/disaster-recovery-azure-applications).

## <a name="basic-concepts-and-terms"></a>Základní pojmy a pojmy

Funkce zotavení po havárii implementuje metadata zotavení po havárii a spoléhá na primární a sekundární obory názvů zotavení po havárii. 

Funkce geografického zotavení po havárii je k dispozici pouze pro [standardní a vyhrazené sloky.](https://azure.microsoft.com/pricing/details/event-hubs/) Není nutné provádět žádné změny připojovacího řetězce, protože připojení je provedeno pomocí aliasu.

V tomto článku se používají následující termíny:

-  *Alias*: Název konfigurace zotavení po havárii, kterou jste nastavili. Alias poskytuje jeden stabilní plně kvalifikovaný připojovací řetězec dns (FQDN). Aplikace používají tento připojovací řetězec aliasu k připojení k oboru názvů. 

-  *Primární/sekundární obor názvů*: Obory názvů, které odpovídají aliasu. Primární obor názvů je "aktivní" a přijímá zprávy (může se jedná o existující nebo nový obor názvů). Sekundární obor názvů je "pasivní" a nepřijímá zprávy. Metadata mezi oběma jsou synchronizována, takže obě mohou bezproblémově přijímat zprávy bez jakýchkoli změn kódu aplikace nebo připojovacího řetězce. Chcete-li zajistit, aby zprávy přijímali pouze aktivní obor názvů, musíte alias použít. 

-  *Metadata*: Entity, jako jsou centra událostí a skupiny spotřebitelů; a jejich vlastnosti služby, které jsou přidruženy k oboru názvů. Všimněte si, že pouze entity a jejich nastavení jsou replikovány automaticky. Zprávy a události nejsou replikovány. 

-  *Převzetí služeb při selhání*: Proces aktivace sekundárního oboru názvů.

## <a name="supported-namespace-pairs"></a>Podporované dvojice oborů názvů
Podporovány jsou následující kombinace primárních a sekundárních oborů názvů:  

| Primární obor názvů | Sekundární obor názvů | Podporuje se | 
| ----------------- | -------------------- | ---------- |
| Standard | Standard | Ano | 
| Standard | Vyhrazená | Ano | 
| Vyhrazená | Vyhrazená | Ano | 
| Vyhrazená | Standard | Ne | 

> [!NOTE]
> Obory názvů, které jsou ve stejném vyhrazeném clusteru, nelze spárovat. Obory názvů, které jsou v samostatných clusterech, můžete spárovat. 

## <a name="setup-and-failover-flow"></a>Nastavení a tok převzetí služeb při selhání

V následující části je přehled procesu převzetí služeb při selhání a vysvětluje, jak nastavit počáteční převzetí služeb při selhání. 

![1][]

### <a name="setup"></a>Nastavení

Nejprve vytvořte nebo použijte existující primární obor názvů a nový sekundární obor názvů a poté je spárujte. Toto párování poskytuje alias, který můžete použít k připojení. Protože používáte alias, není třeba měnit připojovací řetězce. Do párování převzetí služeb při selhání lze přidat pouze nové obory názvů. Nakonec byste měli přidat některé monitorování zjistit, zda je nutné převzetí služeb při selhání. Ve většině případů je služba součástí velkého ekosystému, takže automatické převzetí služeb při selhání jsou zřídka možné, protože velmi často je nutné převzetí služeb při selhání provádět synchronně se zbývajícím subsystémem nebo infrastrukturou.

### <a name="example"></a>Příklad

V jednom příkladu tohoto scénáře zvažte řešení pokladního místa (POS), které vydává zprávy nebo události. Event Hubs předá tyto události nějakému řešení mapování nebo přeformátování, které pak předá namapovaná data jinému systému pro další zpracování. V tomto okamžiku všechny tyto systémy může být hostované ve stejné oblasti Azure. Rozhodnutí o tom, kdy a jakou část převzetí služeb při selhání závisí na toku dat ve vaší infrastruktuře. 

Převzetí služeb při selhání můžete automatizovat buď pomocí monitorovacích systémů, nebo pomocí vlastních monitorovacích řešení. Taková automatizace však vyžaduje další plánování a práci, která je mimo rozsah tohoto článku.

### <a name="failover-flow"></a>Tok převzetí služeb při selhání

Pokud zahájíte převzetí služeb při selhání, jsou vyžadovány dva kroky:

1. Pokud dojde k jinému výpadku, chcete mít možnost převzetí služeb při selhání znovu. Proto nastavte jiný pasivní obor názvů a aktualizujte párování. 

2. Vyžádat zprávy z bývalého primárního oboru názvů, jakmile je opět k dispozici. Poté použijte tento obor názvů pro běžné zasílání zpráv mimo nastavení geografické obnovy nebo odstraňte starý primární obor názvů.

> [!NOTE]
> Podporovány jsou pouze selhání dopředu sémantiku. V tomto scénáři převzetí služeb při selhání a znovu spárovat s novým oborem názvů. Vrácení se selháním není podporováno. například v clusteru SQL. 

![2][]

## <a name="management"></a>Správa

Pokud jste udělali chybu; například jste během počátečního nastavení spárovali nesprávné oblasti, můžete kdykoli přerušit párování dvou oborů názvů. Pokud chcete spárované obory názvů použít jako běžné obory názvů, odstraňte alias.

## <a name="samples"></a>ukázky

[Ukázka na GitHubu](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/GeoDRClient) ukazuje, jak nastavit a iniciovat převzetí služeb při selhání. Tato ukázka ukazuje následující pojmy:

- Nastavení požadovaná ve službě Azure Active Directory pro použití Azure Resource Manager u event hubů. 
- Kroky potřebné ke spuštění ukázkového kódu. 
- Odesílat a přijímat z aktuálního primárního oboru názvů. 

## <a name="considerations"></a>Požadavky

Všimněte si následujících úvah, které je třeba mít na paměti v této verzi:

1. Podle návrhu služby Event Hubs geografické zotavení po havárii nereplikuje data, a proto nelze znovu použít starou hodnotu posunu primárního centra událostí v centru sekundárních událostí. Doporučujeme restartovat přijímač událostí jedním z následujících:

- *EventPosition.FromStart()* - Pokud si přejete přečíst všechna data v centru sekundárních událostí.
- *EventPosition.FromEnd()* - Pokud chcete číst všechna nová data z doby připojení k centru sekundárních událostí.
- *EventPosition.FromEnqueuedTime(dateTime)* - Pokud chcete číst všechna data přijatá v centru sekundárních událostí od daného data a času.

2. Při plánování převzetí služeb při selhání byste měli také zvážit faktor času. Pokud například ztratíte připojení po dobu delší než 15 až 20 minut, můžete se rozhodnout zahájit převzetí služeb při selhání. 
 
3. Skutečnost, že žádná data jsou replikována znamená, že aktuálně aktivní relace nejsou replikovány. Navíc nemusí fungovat vyhledávání duplicit a naplánované zprávy. Nové relace, naplánované zprávy a nové duplikáty budou fungovat. 

4. Selhání komplexní distribuované infrastruktury by mělo být [nacvičeno](/azure/architecture/reliability/disaster-recovery#disaster-recovery-plan) alespoň jednou. 

5. Synchronizace entit může nějakou dobu trvat, přibližně 50-100 entit za minutu.

## <a name="availability-zones"></a>Zóny dostupnosti 

Standardní skladová položka centra event hubů podporuje [zóny dostupnosti](../availability-zones/az-overview.md)a poskytuje umístění izolovaná po chybám v rámci oblasti Azure. 

> [!NOTE]
> Podpora zón dostupnosti pro Azure Event Hubs Standard je dostupná jenom v [oblastech Azure,](../availability-zones/az-overview.md#services-support-by-region) kde jsou k dispozici zóny dostupnosti.

Zóny dostupnosti můžete povolit pouze v nových oborech názvů pomocí portálu Azure. Event Hubs nepodporuje migraci existujících oborů názvů. Redundanci zóny nelze zakázat po povolení v oboru názvů.

![3][]

## <a name="next-steps"></a>Další kroky

* [Ukázka na GitHubu](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/GeoDRClient) prochází jednoduchým pracovním postupem, který vytvoří geografické párování a iniciuje převzetí služeb při selhání pro scénář zotavení po havárii.
* [Odkaz rozhraní REST API](/rest/api/eventhub/disasterrecoveryconfigs) popisuje rozhraní API pro provádění konfigurace geografického zotavení po havárii.

Další informace o službě Event Hubs naleznete pod těmito odkazy:

- Začínáme se službou Event Hubs
    - [.NET Core](get-started-dotnet-standard-send-v2.md)
    - [Java](get-started-java-send-v2.md)
    - [Python](get-started-python-send-v2.md)
    - [Javascript](get-started-java-send-v2.md)
* [Nejčastější dotazy k Event Hubs](event-hubs-faq.md)
* [Ukázkové aplikace, které používají službu Event Hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples)

[1]: ./media/event-hubs-geo-dr/geo1.png
[2]: ./media/event-hubs-geo-dr/geo2.png
[3]: ./media/event-hubs-geo-dr/eh-az.png
