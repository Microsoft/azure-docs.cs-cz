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
ms.openlocfilehash: 266cd52117f36b282fdd4bc8615a15e451cc203f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80132574"
---
Funkce usnadňují přidání Application Insights integrace do aplikace Function App z [Azure Portal].

1. Na portálu [portal][Azure]Portal zadejte `Function Apps` do horní části stránky panel hledání, zvolte aplikaci Function App a potom v horní části okna vyberte hlavičku **Application Insights není nakonfigurovaná** . Pokud se tento banner nezobrazuje, aplikace už má Application Insights povolenou.

    ![Povolení Application Insights z portálu](media/functions-connect-new-app-insights/enable-application-insights.png)

1. Vytvořte prostředek Application Insights pomocí nastavení uvedeného v tabulce pod obrázkem.

   ![Vytvořte prostředek Application Insights](media/functions-connect-new-app-insights/ai-general.png)

    | Nastavení      | Navrhovaná hodnota  | Popis                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Název** | Jedinečný název aplikace | Je nejjednodušší použít stejný název jako aplikace Function App, který musí být ve vašem předplatném jedinečný. | 
    | **Umístění** | Západní Evropa | Pokud je to možné, použijte stejnou [oblast](https://azure.microsoft.com/regions/) jako aplikace Function App nebo tu, která je blízko této oblasti. |

1. Vyberte **OK**. Prostředek Application Insights se vytvoří ve stejné skupině prostředků a předplatném jako vaše aplikace Function App. Po vytvoření prostředku zavřete okno Application Insights.

1. Zpátky ve své aplikaci Function App vyberte **nastavení aplikace**a potom se posuňte dolů k **nastavení aplikace**. Pokud se zobrazí nastavení s názvem `APPINSIGHTS_INSTRUMENTATIONKEY`, Application Insights integrace aplikace Function App běžící v Azure je povolená.

[Portál Azure]: https://portal.azure.com
