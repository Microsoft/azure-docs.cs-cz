---
title: zahrnout soubor
description: zahrnout soubor
services: functions
author: ggailey777
ms.service: functions
ms.topic: include
ms.date: 04/06/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: b6cafcfe6c892cd43f056458fe3586da834c2fd1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66131429"
---
Funkce výrazně zjednodušuje integraci Application Insights přidat do aplikace function app z [Azure Portal].

1. V [portál][azure portal]vyberte **všechny služby > aplikace Function App**, vyberte vaši aplikaci function app a klikněte na tlačítko **Application Insights** úvodní nápis v horní části okna

    ![Povolit Application Insights z portálu](media/functions-connect-new-app-insights/enable-application-insights.png)

1. Vytvořte prostředek Application Insights s použitím nastavení uvedená v tabulce pod obrázkem:

   ![Vytvořte prostředek Application Insights](media/functions-connect-new-app-insights/ai-general.png)

    | Nastavení      | Navrhovaná hodnota  | Popis                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Název** | Jedinečný název aplikace | Je nejjednodušší použijte stejný název jako aplikace function app, který musí být jedinečný v rámci vašeho předplatného. | 
    | **Location** | Západní Evropa | Pokud je to možné, použijte stejný [oblasti](https://azure.microsoft.com/regions/) jako vaši aplikaci function app, nebo blízko ho. |

1. Zvolte **OK**. Prostředek Application Insights se vytvoří ve stejné skupině prostředků a předplatném jako aplikace function app. Po dokončení vytváření, zavřete okno Application Insights.

1. Po návratu do aplikace function app, vyberte **nastavení aplikace**a přejděte dolů k položce **nastavení aplikace**. Když se zobrazí nastavení s názvem `APPINSIGHTS_INSTRUMENTATIONKEY`, znamená to, zda je povoleno integrace Application Insights pro vaši aplikaci funkcí spuštěnou v Azure.

[Azure Portal]: https://portal.azure.com
