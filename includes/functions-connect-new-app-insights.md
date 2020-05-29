---
title: zahrnout soubor
description: zahrnout soubor
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/09/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 3289ba03d0f613d004bc8bff4dbcf2bd434f3da3
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/12/2020
ms.locfileid: "83121523"
---
Funkce usnadňují přidání integrace Application Insights do aplikace funkcí z webu [Azure Portal].

1. Na webu [Azure Portal][Azure Portal] vyhledejte a vyberte **aplikaci funkcí** a pak zvolte svou aplikaci funkcí. 

1. Vyberte v horní části okna banner **Služba Application Insights není nakonfigurovaná**. Pokud se tento banner nezobrazuje, aplikace už má službu Application Insights povolenou.

    :::image type="content" source="media/functions-connect-new-app-insights/enable-application-insights.png" alt-text="Povolení služby Application Insights z portálu":::

1. Vytvořte prostředek Application Insights pomocí nastavení uvedeného v tabulce pod obrázkem.

   :::image type="content" source="media/functions-connect-new-app-insights/ai-general.png" alt-text="Vytvoření prostředku Application Insights":::

    | Nastavení      | Navrhovaná hodnota  | Popis                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Název** | Jedinečný název aplikace | Nejjednodušší je použít stejný název, jako má vaše aplikace funkcí, který musí být ve vašem předplatném jedinečný. | 
    | **Umístění** | Západní Evropa | Pokud je to možné, použijte stejnou [oblast](https://azure.microsoft.com/regions/), jakou má vaše aplikace funkcí, nebo tu, která je blízko této oblasti. |

1. Vyberte **Použít**. Prostředek Application Insights se vytvoří ve stejné skupině prostředků a předplatném jako vaše aplikace funkcí. Po vytvoření prostředku zavřete okno Application Insights.

1. Zpátky v aplikaci funkcí vyberte **Nastavení** > **Konfigurace** a pak vyberte **Nastavení aplikace**. Pokud se zobrazí nastavení s názvem `APPINSIGHTS_INSTRUMENTATIONKEY`, je pro vaši aplikaci funkcí běžící v Azure povolena integrace Application Insights.

[Azure Portal]: https://portal.azure.com
