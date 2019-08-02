---
title: zahrnout soubor
description: zahrnout soubor
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/06/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: b1bbc11d7772e4f56d7dc6ead580b0a0cbd3cd8d
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2019
ms.locfileid: "68669584"
---
Funkce usnadňují přidání Application Insights integrace do aplikace Function App z [Azure Portal].

1. Na portálu [][azure portal]portal vyberte **všechny služby > Function Apps**, vyberte svou aplikaci Function App a potom v horní části okna vyberte banner **Application Insights** .

    ![Povolení Application Insights z portálu](media/functions-connect-new-app-insights/enable-application-insights.png)

1. Vytvořte prostředek Application Insights pomocí nastavení uvedeného v tabulce pod obrázkem.

   ![Vytvořte prostředek Application Insights](media/functions-connect-new-app-insights/ai-general.png)

    | Nastavení      | Navrhovaná hodnota  | Popis                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Název** | Jedinečný název aplikace | Je nejjednodušší použít stejný název jako aplikace Function App, který musí být ve vašem předplatném jedinečný. | 
    | **Location** | Západní Evropa | Pokud je to možné, použijte stejnou [oblast](https://azure.microsoft.com/regions/) jako aplikace Function App nebo tu, která je blízko této oblasti. |

1. Vyberte **OK**. Prostředek Application Insights se vytvoří ve stejné skupině prostředků a předplatném jako vaše aplikace Function App. Po vytvoření prostředku zavřete okno Application Insights.

1. Zpátky ve své aplikaci Function App vyberte **nastavení aplikace**a potom se posuňte dolů k **nastavení aplikace**. Pokud se zobrazí nastavení s názvem `APPINSIGHTS_INSTRUMENTATIONKEY`, Application Insights integrace aplikace Function App běžící v Azure je povolená.

[Azure Portal]: https://portal.azure.com
