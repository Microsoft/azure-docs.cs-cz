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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132574"
---
Funkce usnadňují přidání integrace Application Insights do aplikace funkcí z [portálu Azure].

1. Na [portálu][Azure] `Function Apps` Portal zadejte vyhledávací panel v horní části stránky, zvolte aplikaci funkcí a pak vyberte **Application Insights není nakonfigurovaný** banner v horní části okna. Pokud tento nápis nevidíte, pak už vaše aplikace má povolené Application Insights.

    ![Povolení přehledů aplikací z portálu](media/functions-connect-new-app-insights/enable-application-insights.png)

1. Vytvořte prostředek Application Insights pomocí nastavení zadaných v tabulce pod obrázkem.

   ![Vytvořte prostředek Application Insights](media/functions-connect-new-app-insights/ai-general.png)

    | Nastavení      | Navrhovaná hodnota  | Popis                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Název** | Jedinečný název aplikace | Nejjednodušší je použít stejný název jako aplikace funkce, která musí být v předplatném jedinečná. | 
    | **Umístění** | Západní Evropa | Pokud je to možné, použijte stejnou [oblast](https://azure.microsoft.com/regions/) jako aplikace funkce nebo oblast, která je blízko této oblasti. |

1. Vyberte **OK**. Prostředek Application Insights se vytvoří ve stejné skupině prostředků a předplatné jako vaše aplikace funkce. Po vytvoření prostředku zavřete okno Přehledy aplikací.

1. Zpět do aplikace funkce vyberte **Nastavení aplikace**a pak přejděte dolů na **Nastavení aplikace**. Pokud se zobrazí `APPINSIGHTS_INSTRUMENTATIONKEY`nastavení s názvem , Application Insights integrace je povolena pro vaše funkce aplikace spuštěné v Azure.

[Azure Portal]: https://portal.azure.com
