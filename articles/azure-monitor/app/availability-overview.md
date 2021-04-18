---
title: Přehled dostupnosti Application Insights
description: Nastavte opakované webové testy pro monitorování dostupnosti a odezvy vaší aplikace nebo webu.
ms.topic: conceptual
ms.date: 04/15/2021
ms.openlocfilehash: 3c2d248df98fcb9f75f5de9c05b9f600955cbac7
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600790"
---
# <a name="availability-tests-overview"></a>Přehled testů dostupnosti

Po nasazení webové aplikace nebo webu můžete nastavit opakované testy pro monitorování dostupnosti a odezvy. [Application Insights](./app-insights-overview.md) odesílá webové požadavky do vaší aplikace v pravidelných intervalech z bodů po celém světě. Může vás upozornit, pokud vaše aplikace nereaguje nebo pokud reaguje příliš pomalu.

Testy dostupnosti můžete nastavit pro libovolný koncový bod HTTP nebo HTTPS, který je přístupný z veřejného internetu. Na webu, který testujete, nemusíte dělat žádné změny. Ve skutečnosti to ani nemusí být lokalita, kterou vlastníte. Můžete testovat dostupnost REST API, na kterém je vaše služba závislá.

## <a name="types-of-availability-tests"></a>Typy testů dostupnosti

Existují tři typy testů dostupnosti:

* [Test adresy URL pomocí příkazů](monitor-web-app-availability.md): Tato kategorie má dva jednoduché testy, které můžete vytvořit prostřednictvím portálu.
* [Webový test s více kroky](availability-multistep.md): záznam sekvence webových požadavků, které je možné přehrát zpětně testovat složitější scénáře. Webové testy s více kroky jsou vytvořeny v Visual Studio Enterprise a nahrány na portál pro provádění.
* [Testy dostupnosti vlastních stop](/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability): Pokud se rozhodnete vytvořit vlastní aplikaci pro spuštění testů dostupnosti, `TrackAvailability()` lze použít metodu k odeslání výsledků do Application Insights.

> [!IMPORTANT]
> [Test příkazového testu adresy URL](monitor-web-app-availability.md) a [webový test na více kroků](availability-multistep.md) se spoléhá na veřejnou internetovou infrastrukturu DNS k překladu názvů domén testovaných koncových bodů. To znamená, že pokud používáte Privátní DNS, musíte buď zajistit, aby všechny názvy domén testu byly přeložitelnými servery veřejných domén, nebo pokud není možné, můžete místo toho použít [vlastní sledovací testy dostupnosti](/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability) .

**Pro každý prostředek Application Insights můžete vytvořit testy dostupnosti až 100.**

## <a name="troubleshooting"></a>Řešení potíží

Vyhrazený [článek týkající se řešení potíží](troubleshoot-availability.md).

## <a name="next-step"></a>Další krok

* [Výstrahy dostupnosti](availability-alerts.md)
* [Webové testy s více kroky](availability-multistep.md)
* [Testy URL](monitor-web-app-availability.md)
* [Vytvářejte a spouštějte vlastní testy dostupnosti pomocí Azure Functions.](availability-azure-functions.md)