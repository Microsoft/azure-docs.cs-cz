---
title: Porovnání – Event Grid, Event Hubs, služby Service Bus pro zasílání zpráv Azure
description: Popisuje tři zasílání zpráv služby Azure – Azure Event Grid, Event Hubs a služby Service Bus. Doporučuje nejvhodnější služby pro různé situace.
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: overview
ms.date: 12/07/2018
ms.author: tomfitz
ms.custom: seodec18
ms.openlocfilehash: 7e78dd77d5bd3d9e5aee55aaeffa933a21c3b477
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53084953"
---
# <a name="choose-between-azure-messaging-services---event-grid-event-hubs-and-service-bus"></a>Výběr mezi zasílání zpráv služby Azure - Event Grid, Event Hubs a služby Service Bus

Azure nabízí tři služby, které v rámci určitého řešení pomáhají doručovat zprávy o událostech. Jsou to tyto služby:

* [Event Grid](/azure/event-grid/)
* [Event Hubs](/azure/event-hubs/)
* [Service Bus](/azure/service-bus-messaging/)

I když se tyto služby v mnohém podobají, každá z nich je navržená pro určité situace. Tento článek vysvětluje rozdíly mezi nimi a pomůže vám pochopit, kterou službu si máte pro svoji aplikaci vybrat. V mnoha případech se služby zasílání zpráv vzájemně doplňují a dají se použít společně.

## <a name="event-vs-message-services"></a>Služby událostí a služby zpráv

Je potřeba rozlišovat mezi službami, které doručují události, a službami, které doručují zprávy.

### <a name="event"></a>Událost

Událost je odlehčené oznámení o stavu nebo změně stavu. Vydavatel události nemá žádné očekávání ohledně toho, jak se má událost zpracovat. Příjemce události se rozhodne, jak s oznámením naložit. Události můžou být samostatné jednotky nebo můžou být součástí řady.

Samostatné události hlásí změny stavu a dá se na ně reagovat. Pokud chce příjemce přejít k dalšímu kroku, stačí mu jenom vědět, že se něco stalo. Data události obsahují informace o tom, co se stalo, ale neobsahují data, která událost aktivovala. Událost třeba příjemcům oznamuje, že došlo k vytvoření souboru. Může obsahovat obecné informace o tomto souboru, ale ne soubor samotný. Samostatné události jsou ideální pro řešení bez serveru, která vyžadují škálování.

Řady událostí hlásí určitý stav a jsou analyzovatelné. Události jsou seřazené podle času a vzájemně provázané. Pokud chcete příjemce analyzovat, co se stalo, potřebuje sekvenční řadu událostí.

### <a name="message"></a>Zpráva

Zprávu tvoří nezpracovaná data vytvořená určitou službou, která se mají spotřebovat nebo uložit na jiném místě. Zpráva obsahuje data, která aktivovala kanál zprávy. Vydavatel zprávy má určitá očekávání ohledně toho, jak příjemce zprávu zpracuje. Mezi oběma stranami existuje dohoda. Vydavatel třeba odešle zprávu s nezpracovanými daty a očekává, že příjemce vytvoří z těchto dat soubor a po dokončení práce odešle odpověď.

## <a name="comparison-of-services"></a>Porovnání služeb

| Služba | Účel | Typ | Kdy je použít |
| ------- | ------- | ---- | ----------- |
| Event Grid | Reaktivní programování | Distribuce události (samostatná) | Reakce na změny stavu |
| Event Hubs | Kanál pro velké objemy dat | Streamování událostí (řady) | Streamování telemetrických a distribuovaných dat |
| Service Bus | Zasílání podnikových zpráv s vysokou hodnotou | Zpráva | Zpracování objednávek a finanční transakce |

### <a name="event-grid"></a>Event Grid

Event Grid je propojovací rovina událostí, která umožňuje reaktivní programování založené na událostech. Používá model publikování-přihlášení k odběru. Vydavatelé vydávají události, ale nemají žádné očekávání ohledně toho, které události se mají zpracovat. O tom, které události se zpracují, rozhodují odběratelé.

Služba Event Grid je úzce propojená se službami Azure a umožňuje taky integraci se službami třetích stran. To zjednodušuje příjem událostí a snižuje náklady, protože není potřeba neustále provádět dotazování. Event Grid efektivně a spolehlivě směruje události z prostředků Azure i mimo Azure. Distribuuje události do registrovaných koncových bodů odběratelů. Zpráva události obsahuje informace, které potřebujete, abyste mohli reagovat na změny ve službách a aplikacích. Event Grid není datový kanál a nedoručuje samotný objekt, u kterého došlo k aktualizaci.

Podporuje Služba Event Grid dead-lettering pro události, které nejsou doručeny do koncového bodu.

Má následující vlastnosti:

* Možnost dynamického škálování
* Nízké náklady
* Prostředí bez serveru
* Alespoň jedno doručení

### <a name="event-hubs"></a>Event Hubs

Azure Event Hubs je kanál pro velké objemy dat. Usnadňuje zachytávání, uchovávání a přehrávání streamovaných telemetrických dat a dat událostí. Data můžou pocházet z mnoha souběžných zdrojů. Event Hubs umožňuje zpřístupnění telemetrických dat a dat událostí do řady infrastruktur pro zpracování datových streamů a analytických služeb. Služba je dostupná buď v podobě datových streamů, nebo jako spojené dávky událostí. Tato služba poskytuje jedno řešení, které umožňuje rychlé načítání dat pro zpracování v reálném čase i opakované přehrávání uložených nezpracovaných dat. Může zachytávat streamovaná data do souboru, který umožňuje další zpracování a analýzu.

Má následující vlastnosti:

* Nízká latence
* Schopnost přijímat a zpracovávat miliony událostí za sekundu
* Alespoň jedno doručení

### <a name="service-bus"></a>Service Bus

Služba Service Bus je určená pro tradiční podnikové aplikace. Tyto podnikové aplikace vyžadují transakce, řazení, vyhledávání duplicit a okamžitou konzistenci. Service Bus umožňuje nativním cloudovým aplikacím poskytovat spolehlivou správu přechodů mezi stavy pro obchodní procesy. Pokud zpracováváte zprávy s vysokou hodnotou, které se nesmí ztratit ani duplikovat, použijte Azure Service Bus. Service Bus zároveň usnadňuje vysoce zabezpečenou komunikaci v rámci hybridního cloudového řešení a může propojovat stávající místní systémy s cloudovými řešeními.

Service Bus je zprostředkovaný systém zasílání zpráv. Ukládá zprávy do „zprostředkovatele“ (třeba fronty), dokud není přijímající strana připravená zprávy přijmout.

Má následující vlastnosti:

* Spolehlivé asynchronní doručování zpráv, které vyžaduje dotazování
* Pokročilé funkce zasílání zpráv, jako je FIFO, dávky/relace, transakce, ukládání nedoručených zpráv, časová kontrola, směrování, filtrování a vyhledávání duplicit
* Alespoň jedno doručení
* Volitelné doručení v daném pořadí

## <a name="use-the-services-together"></a>Použití služeb v kombinaci

V některých případech používáte služby současně k plnění různých úkolů. Například web internetového obchodu může pomocí služby Service Bus zpracovat objednávku, služba Event Hubs zachytí telemetrická data webu a Event Grid bude reagovat na události, jako je odeslání položky.

V jiných případech služby vzájemně propojíte do kanálu událostí a dat. Event Grid slouží k reagování na události v jiných službách. V článku [Streamování velkých objemů dat do datového skladu](event-grid-event-hubs-integration.md) najdete příklad použití Event Gridu v kombinaci se službou Event Hubs. Následující obrázek ukazuje pracovní postup pro streamování dat.

![Přehled streamování dat](./media/compare-messaging-services/overview.png)

## <a name="next-steps"></a>Další postup

* Další informace o službách zasílání zpráv v Azure najdete v příspěvku na blogu [Události, datové body a zprávy – výběr správné služby zasílání zpráv Azure pro vaše data](https://azure.microsoft.com/blog/events-data-points-and-messages-choosing-the-right-azure-messaging-service-for-your-data/).
* Úvod do Event Gridu najdete v článku [Informace o službě Event Grid](overview.md).
* Pokud chcete začít pracovat s Event Gridem, přečtěte si článek [Vytvoření a směrování vlastních událostí pomocí služby Azure Event Grid](custom-event-quickstart.md).
* Pokud chcete začít pracovat se službou Event Hubs, přečtěte si článek [Vytvoření oboru názvů služby Event Hubs a centra událostí pomocí webu Azure Portal](../event-hubs/event-hubs-create.md).
* Pokud chcete začít pracovat se službou Service Bus, přečtěte si článek [Vytvoření oboru názvů služby Service Bus pomocí webu Azure Portal](../service-bus-messaging/service-bus-create-namespace-portal.md).
