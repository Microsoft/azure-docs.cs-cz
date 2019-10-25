---
title: Testování výkonu a zátěže s využitím Azure Application Insights | Microsoft Docs
description: Nastavení výkonu a zátěžových testů pomocí Azure Application Insights
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 06/19/2019
ms.reviewer: sdash
ms.openlocfilehash: 9c86b69239bed1a15c754ce28232b97e8439942b
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2019
ms.locfileid: "72819101"
---
# <a name="performance-testing"></a>Testování výkonu

> [!NOTE]
> Služba zátěžového testování založená na cloudu je zastaralá. Další informace o zastaralosti, dostupnosti služby a alternativních službách najdete [tady](https://docs.microsoft.com/azure/devops/test/load-test/overview?view=azure-devops).

Application Insights umožňuje generovat zátěžové testy pro vaše weby. Podobně jako u [testů dostupnosti](monitor-web-app-availability.md)můžete odeslat buď základní požadavky, nebo [požadavky na více kroků](availability-multistep.md) od agentů Azure Testers po celém světě. Testy výkonu vám umožní simulovat až 20 000 současných uživatelů po dobu až 60 minut.

## <a name="create-an-application-insights-resource"></a>Vytvořte prostředek Application Insights

Aby bylo možné vytvořit test výkonnosti, je nejprve třeba vytvořit prostředek Application Insights. Pokud jste už prostředek vytvořili, přejděte k další části.

V Azure Portal vyberte **vytvořit prostředek** > **vývojářské nástroje** > **Application Insights** a vytvořte prostředek Application Insights.

## <a name="configure-performance-testing"></a>Konfigurace testování výkonu

Pokud je to poprvé, když vytváříte test výkonnosti, vyberte **nastavit organizaci** a zvolte organizaci Azure DevOps, která bude zdrojem pro vaše testy výkonu.

V části **Konfigurovat**přejděte na **testování výkonu** a kliknutím na **Nový** vytvořte test.

![Vyplňte alespoň adresu URL webu](./media/performance-testing/new-performance-test.png)

Chcete-li vytvořit základní test výkonnosti, vyberte typ testu **manuálního testu** a vyplňte požadovaná nastavení testu.

|Nastavení| Maximální hodnota
|----------|------------|
| Uživatelské zatížení | 20 000 |
| Doba trvání (minuty)  | 60 |  

Po vytvoření testu klikněte na tlačítko **Spustit test**.

Po dokončení testu se zobrazí výsledky, které vypadají podobně jako následující výsledky:

![Výsledky testů](./media/performance-testing/test-results.png)

## <a name="configure-visual-studio-web-test"></a>Konfigurovat webový test sady Visual Studio

Application Insights pokročilé možnosti testování výkonu jsou postavené na projektech testů výkonu a zatížení sady Visual Studio.

![Visual Studio ](./media/performance-testing/visual-studio-test.png)

## <a name="next-steps"></a>Další kroky

* [Webové testy s více kroky](availability-multistep.md)
* [Testy adresy URL pro příkazy URL](monitor-web-app-availability.md)