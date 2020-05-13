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
Funkce usnadňují přidání Application Insights integrace do aplikace Function App z [Azure Portal].

1. Na portálu [Azure Portal][Azure Portal]vyhledejte a vyberte **aplikace Function App**a pak zvolte svou aplikaci Function App. 

1. V horní části okna vyberte hlavičku **Application Insights není nakonfigurovaná** . Pokud se tento banner nezobrazuje, aplikace už má Application Insights povolenou.

    :::image type="content" source="media/functions-connect-new-app-insights/enable-application-insights.png" alt-text="Povolení Application Insights z portálu":::

1. Vytvořte prostředek Application Insights pomocí nastavení uvedeného v tabulce pod obrázkem.

   :::image type="content" source="media/functions-connect-new-app-insights/ai-general.png" alt-text="Vytvoření prostředku Application Insights":::

    | Nastavení      | Navrhovaná hodnota  | Popis                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Název** | Jedinečný název aplikace | Je nejjednodušší použít stejný název jako aplikace Function App, který musí být ve vašem předplatném jedinečný. | 
    | **Umístění** | Západní Evropa | Pokud je to možné, použijte stejnou [oblast](https://azure.microsoft.com/regions/) jako aplikace Function App nebo tu, která je blízko této oblasti. |

1. Vyberte **Použít**. Prostředek Application Insights se vytvoří ve stejné skupině prostředků a předplatném jako vaše aplikace Function App. Po vytvoření prostředku zavřete okno Application Insights.

1. Zpátky ve své aplikaci Function App vyberte **Nastavení**  >  **Konfigurace**a pak vyberte **nastavení aplikace**. Pokud se zobrazí nastavení s názvem `APPINSIGHTS_INSTRUMENTATIONKEY` , Application Insights integrace aplikace Function App běžící v Azure je povolená.

[Azure Portal]: https://portal.azure.com
