---
title: Výkonnostní a zátěžové testování pomocí Azure Application Insights | Dokumentace Microsoftu
description: Nastavení výkonu a zátěžové testy pomocí Azure Application Insights
services: application-insights
author: mrbullwinkle
manager: carmonm
ms.assetid: 46dc13b4-eb2e-4142-a21c-94a156f760ee
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/19/2019
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: 55d743e32f6db0828317d3764a97bcb35b104dad
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2019
ms.locfileid: "67304971"
---
# <a name="performance-testing"></a>Testování výkonu

> [!NOTE]
> Cloudové zátěžové testování služby se už nepoužívá. Další informace o vyřazení podpory, dostupnost služby a alternativní služeb můžete najít [tady](https://docs.microsoft.com/azure/devops/test/load-test/overview?view=azure-devops).

Application Insights umožňuje vygenerovat zátěžových testů pro vaše weby. Stejně jako [testy dostupnosti](monitor-web-app-availability.md), můžete odeslat buď základní požadavky nebo [vícekrokové požadavky](availability-multistep.md) z Azure testovací agenty po celém světě. Testy výkonu umožňuje simulovat až 20 000 souběžných uživatelů po dobu až 60 minut.

## <a name="create-an-application-insights-resource"></a>Vytvořte prostředek Application Insights

Pokud chcete vytvořit test výkonu, musíte nejprve vytvořit prostředek Application Insights. Pokud už máte vytvořený prostředek přejít k další části.

Na webu Azure Portal, vyberte **vytvořit prostředek** > **vývojářské nástroje** > **Application Insights** a vytvoření Application Insights prostředek.

## <a name="configure-performance-testing"></a>Konfigurace testování výkonu

Pokud to je poprvé vytváření vyberte test výkonu **nastavení organizace** a zvolit organizaci Azure DevOps se zdroje pro testy výkonu.

V části **konfigurovat**, přejděte na stránku **testování výkonu** a klikněte na tlačítko **nový** pro vytvoření testu.

![Vyplňte alespoň adresu URL webu](./media/performance-testing/new-performance-test.png)

Pro vytvoření základní výkon testu vyberte test typu **manuálních testů** a vyplňte požadovaná nastavení pro váš test.

|Nastavení| Maximální hodnota
|----------|------------|
| Uživatelské zatížení | 20,000 |
| Doba trvání (minuty)  | 60 |  

Po vytvoření testu klikněte na tlačítko **spuštění testu**.

Po dokončení testu se zobrazí výsledky, které vypadají podobně jako následující výsledky:

![Výsledky testů](./media/performance-testing/test-results.png)

## <a name="configure-visual-studio-web-test"></a>Konfigurace webového testu sady Visual Studio

Application Insights Upřesnit možnosti testování jsou vytvořené s využitím výkonu sady Visual Studio a zátěžové testování projektů.

![Visual Studio ](./media/performance-testing/visual-studio-test.png)

## <a name="next-steps"></a>Další postup

* [Vícekrokové webové testy](availability-multistep.md)
* [Testování ping adresy URL](monitor-web-app-availability.md)