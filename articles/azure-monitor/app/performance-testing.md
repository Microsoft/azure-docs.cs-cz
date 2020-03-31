---
title: Testování výkonu a zatížení pomocí přehledů aplikací Azure | Dokumenty společnosti Microsoft
description: Nastavení testů výkonu a zatížení pomocí Azure Application Insights
ms.topic: conceptual
ms.date: 06/19/2019
ms.reviewer: sdash
ms.openlocfilehash: 0bfb60b876add70be6cbcd3d726fb3317a8dfcf9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77669858"
---
# <a name="performance-testing"></a>Testování výkonu

> [!NOTE]
> Cloudová služba zátěžového testování byla zastaralá. Více informací o vyřazení, dostupnosti služeb a alternativních službách naleznete [zde](https://docs.microsoft.com/azure/devops/test/load-test/overview?view=azure-devops).

Application Insights umožňuje generovat zátěžové testy pro vaše webové stránky. Stejně jako [testy dostupnosti](monitor-web-app-availability.md)můžete odesílat základní požadavky nebo [vícekrokové požadavky](availability-multistep.md) od testovacích agentů Azure po celém světě. Testy výkonu umožňují simulovat až 20 000 současných uživatelů po dobu až 60 minut.

## <a name="create-an-application-insights-resource"></a>Vytvořte prostředek Application Insights

Chcete-li vytvořit test výkonu, musíte nejprve vytvořit prostředek Application Insights. Pokud jste již zdroj vytvořili, přejděte k další části.

Na portálu Azure vyberte **Vytvořit zdroj** > **nástroje pro vývojáře** > **Application Insights** a vytvořte prostředek Application Insights.

## <a name="configure-performance-testing"></a>Konfigurace testování výkonu

Pokud je to vaše první vytvoření testu výkonu vyberte **Nastavit organizaci** a zvolte Organizaci Azure DevOps jako zdroj pro vaše testy výkonu.

V **části Konfigurovat**přejděte na testování **výkonu** a klepnutím na tlačítko **Nový** vytvořte test.

![Vyplňte alespoň adresu URL webu](./media/performance-testing/new-performance-test.png)

Chcete-li vytvořit základní test výkonu, vyberte typ testu **ručního testu** a vyplňte požadovaná nastavení testu.

|Nastavení| Max. hodnota
|----------|------------|
| Zatížení uživatele | 20 000 |
| Doba trvání (minuty)  | 60 |  

Po vytvoření testu klepněte na tlačítko **Spustit test**.

Po dokončení testu se zobrazí výsledky, které vypadají podobně jako níže uvedené výsledky:

![Výsledky testů](./media/performance-testing/test-results.png)

## <a name="configure-visual-studio-web-test"></a>Konfigurace webového testu sady Visual Studio

Pokročilé možnosti testování výkonu Application Insights jsou postavené na projektech výkonu a zátěžových testů sady Visual Studio.

![Visual Studio ](./media/performance-testing/visual-studio-test.png)

## <a name="next-steps"></a>Další kroky

* [Vícekrokové webové testy](availability-multistep.md)
* [Testy příkazu url](monitor-web-app-availability.md)
