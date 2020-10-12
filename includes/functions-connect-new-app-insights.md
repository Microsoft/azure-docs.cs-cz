---
title: zahrnout soubor
description: zahrnout soubor
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 06/10/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 87af50c5b5e5b69fd175ac4a570c4b6f659b97e6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84730982"
---
Azure Functions usnadňuje přidání Application Insights integrace do aplikace Function App z [Azure Portal].

1. Na webu [Azure Portal][Azure Portal] vyhledejte a vyberte **aplikaci funkcí** a pak zvolte svou aplikaci funkcí. 

1. Vyberte v horní části okna banner **Služba Application Insights není nakonfigurovaná**. Pokud se vám nápis nezobrazuje, může být aplikace již Application Insights povolena.

    :::image type="content" source="media/functions-connect-new-app-insights/enable-application-insights.png" alt-text="Povolení služby Application Insights z portálu":::

1. Rozbalte položku **změnit prostředek** a vytvořte prostředek Application Insights pomocí nastavení uvedeného v následující tabulce.  

    | Nastavení      | Navrhovaná hodnota  | Popis                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nový název prostředku** | Jedinečný název aplikace | Nejjednodušší je použít stejný název, jako má vaše aplikace funkcí, který musí být ve vašem předplatném jedinečný. | 
    | **Umístění** | Západní Evropa | Pokud je to možné, použijte stejnou [oblast](https://azure.microsoft.com/regions/), jakou má vaše aplikace funkcí, nebo tu, která je blízko této oblasti. |

    :::image type="content" source="media/functions-connect-new-app-insights/ai-general.png" alt-text="Povolení služby Application Insights z portálu":::

1. Vyberte **Použít**. 

   Prostředek Application Insights se vytvoří ve stejné skupině prostředků a předplatném jako vaše aplikace funkcí. Po vytvoření prostředku zavřete okno Application Insights.

1. Ve vaší aplikaci Function App vyberte v části **Nastavení**položku **Konfigurace** a pak vyberte **nastavení aplikace**. Pokud se zobrazí nastavení s názvem `APPINSIGHTS_INSTRUMENTATIONKEY`, je pro vaši aplikaci funkcí běžící v Azure povolena integrace Application Insights.

[Azure Portal]: https://portal.azure.com
