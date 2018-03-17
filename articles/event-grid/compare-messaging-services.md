---
title: "Zasílání zpráv porovnání služeb Azure"
description: "Porovná Azure událostí mřížky, Event Hubs a Service Bus. Doporučuje služby, která chcete použít pro různé scénáře."
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 03/16/2018
ms.author: tomfitz
ms.openlocfilehash: 30bbe7442cac96a1dcf6959cac2abedd61454a29
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/17/2018
---
# <a name="choose-between-azure-services-that-deliver-messages"></a>Zvolit služby Azure, které doručování zpráv

Azure nabízí tři služby, které pomáhají s doručováním zprávy o událostech v celé řešení. Tyto služby jsou:

* [Událost mřížky](/azure/event-grid/)
* [Event Hubs](/azure/event-hubs/)
* [Service Bus](/azure/service-bus-messaging/)

I když mají některé podobnosti, každá služba je určená pro určité scénáře. Tento článek popisuje rozdíly mezi tyto služby a vám pomůže pochopit, vybírat pro vaši aplikaci. V mnoha případech služby zasílání zpráv jsou vzájemně doplňují a dá se použít společně.

## <a name="event-vs-message-services"></a>Událost oproti služba zpráv

Je důležité rozdíl mezi služby přinášející událost a služby přinášející zprávu poznamenat.

### <a name="event"></a>Událost

Událost je lightweight oznámení podmínku nebo změně stavu. Vydavatel události nemá žádné očekávání o zpracování události. Příjemci události rozhodne co dělat s oznámením. Události může být diskrétní jednotky nebo součástí řady.

Diskrétní události změny stavu sestavy a je možné použít. Chcete-li přejít k dalšímu kroku, příjemce jenom je potřeba vědět, že se něco stalo. Data události obsahuje informace o co se stalo, ale nemá data, která spustí událost. Například událost upozorní příjemci, že soubor byl vytvořen. Obecné informace o souboru může obsahovat, ale neobsahuje samotném souboru. Diskrétní události jsou ideální pro bez serveru řešení, které je potřeba škálování.

Události řady sestavy podmínku a jsou analyzable. Události jsou seřazené čas a vzájemně propojené. Příjemce musí sekvencované řadu událostí k analýze, co se stalo.

### <a name="message"></a>Zpráva

Zpráva je nezpracovaných dat vytváří služba spotřebované nebo uložená na jiném místě. Zpráva obsahuje data, která aktivuje zpráva kanálu. Vydavatel zpráva má očekávané o zpracování zprávy příjemce. Mezi sociálními existuje kontraktu. Například vydavatele odešle zprávu s nezpracovaná data a očekává příjemce k vytvoření souboru z těchto dat a odeslání odpovědi při práci.

## <a name="comparison-of-services"></a>Porovnání služeb

| Služba | Účel | Typ | Kdy je použít |
| ------- | ------- | ---- | ----------- |
| Event Grid | Přepnutí do reaktivního programování | Distribuci událostí (diskrétní) | Reagovat na změny stavu |
| Event Hubs | Kanál velkých objemů dat | Událost streamování (řada) | Telemetrická data a vysílání datového proudu distribuovaných datech |
| Service Bus | Citlivých podnikových způsobů zasílání zpráv | Zpráva | Pořadí zpracování a finanční transakce |

### <a name="event-grid"></a>Event Grid

Mřížka událostí je eventing propojovacího rozhraní, která umožňuje událostmi, reaktivní programování. Používá publikování-přihlášení k odběru modelu. Vydavatelé emitování události, ale mít žádné očekávání o tom, které jsou zpracovávány události. Odběratelé, kteří se rozhodnout události, které chce zpracovat.

Událost mřížky se úzce integruje se službami Azure a může být integrovaná v služeb třetích stran. To zjednodušuje příjmu událostí a snižuje náklady na odstraněním potřeby konstantní cyklické dotazování. Událost mřížky efektivně a spolehlivě směruje události z Azure a prostředky mimo Azure. Distribuuje události, které registrované odběratele koncové body. Zpráva události obsahuje informace, které je potřeba reagovat na změny v služeb a aplikací. Mřížky událostí není datovém kanálu a nejsou poskytovány skutečné objektu, který byl aktualizován.

Má následující vlastnosti:

* dynamicky škálovatelné
* Nízké náklady
* Bez serveru

### <a name="event-hubs"></a>Event Hubs

Azure Event Hubs je velkých objemů dat kanálu. Zařídí zachycení, uchovávání a opětovného přehrání dat datový proud telemetrie a událostí. Data mohou pocházet z mnoha souběžných zdrojů. Služba Event Hubs umožňuje data telemetrie a událostí bylo k dispozici k různým službám infrastruktury a analýzy zpracování datového proudu. Je k dispozici buď jako datové proudy nebo připojené události dávek. Tato služba poskytuje jeden řešení, které umožňuje načtení rychlé data pro zpracování v reálném čase, jakož i opakované přehrání uložené nezpracovaná data. Streamování dat ji můžete zaznamenat do souboru pro zpracování a analýzu.

Má následující vlastnosti:

* s nízkou latencí
* umožňuje přijímání a zpracování miliony událostí za sekundu

### <a name="service-bus"></a>Service Bus

Service Bus je určený pro tradiční podnikové aplikace. Tyto aplikace enterprise vyžadují transakce, řazení, detekci duplikátů a okamžitou konzistence. Service Bus umožňuje aplikacím nativní cloudu a zajistit tak správu spolehlivé stavu přechodu pro podnikové procesy. Při zpracování zpráv vysoké hodnoty, které nemohou být ke ztrátě nebo duplicitní, použijte Azure Service Bus. Service Bus také usnadňuje vysoce zabezpečených komunikaci mezi hybridní cloudové řešení a stávajících místních systémů se může připojit k cloudové řešení.

Service Bus je zprostředkované zasílání zpráv systému. Uloží zprávy do "zprostředkovatele" (například fronty), dokud spotřebitel nebude připravený přijímat zprávy.

Má následující vlastnosti:

* doručení spolehlivé asynchronní zpráv (enterprise zasílání zpráv jako služba), který vyžaduje dotazování
* pokročilé funkce zasílání zpráv jako FIFO, dávkování/relací, transakce, řízení zpráv lettering, dočasné, směrování a filtrování a detekci duplikátů

## <a name="use-the-services-together"></a>Použití služeb společně

V některých případech byste používat služby vedle sebe ke splnění odlišné role. Například můžete použít stránku elektronické obchodování Service Bus zpracovat objednávku, Event Hubs k zachycení lokality telemetrie a událostí mřížky na reakce na události, jako byl dodán položku.

V ostatních případech můžete propojit je dohromady a kanál události a data. Reakce na události v jiných službách použijete událostí mřížky. Příklad použití událostí mřížky službou Event Hubs pro migraci dat do datového skladu, naleznete v části [Stream velkých objemů dat do datového skladu](event-grid-event-hubs-integration.md). Následující obrázek ukazuje pracovní postup pro streamování data.

![Datový proud dat – přehled](./media/compare-messaging-services/overview.png)

## <a name="next-steps"></a>Další postup

* Další informace o zasílání zpráv služby Azure, naleznete v příspěvku blogu [události, datové body a zprávy – výběr právo Azure službou zasílání zpráv pro vaše data](https://azure.microsoft.com/blog/events-data-points-and-messages-choosing-the-right-azure-messaging-service-for-your-data/).
* Úvod k mřížce událostí, naleznete v části [o mřížky událostí](overview.md).
* Začínáme s událostí mřížky, najdete v tématu [vytvořit a směrování vlastních událostí s Azure událostí mřížky](custom-event-quickstart.md).
* Chcete-li začít se službou Event Hubs, přečtěte si téma [vytvořit obor názvů Event Hubs a centra událostí pomocí portálu Azure](../event-hubs/event-hubs-create.md).
* Chcete-li začít se službou Service Bus, přečtěte si téma [vytvoření oboru názvů Service Bus pomocí portálu Azure](../service-bus-messaging/service-bus-create-namespace-portal.md).