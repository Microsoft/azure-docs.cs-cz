---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 49ac1a7585ddf2a6500c7e9382880109c3f7f431
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/16/2019
ms.locfileid: "69534269"
---
## <a name="export-an-api-definition"></a>Exportovat definici rozhraní API
Máte definici OpenAPI pro funkci, od [Vytvoření definice openapi pro funkci](../articles/azure-functions/functions-openapi-definition.md). Dalším krokem v tomto procesu je exportovat definici rozhraní API, aby ho PowerApps a Microsoft Flow mohl použít ve vlastním rozhraní API.

> [!IMPORTANT]
> Nezapomeňte, že musíte být přihlášeni k Azure se stejnými přihlašovacími údaji, které používáte pro klienty PowerApps a Microsoft Flow. Díky tomu Azure vytvoří vlastní rozhraní API a zpřístupní ho pro PowerApps i Microsoft Flow.

1. V [Azure Portal](https://portal.azure.com)klikněte na název aplikace funkcí (například **Function-demo-Energy**) > **funkce** > platformy**definice rozhraní API**.

    ![Definice rozhraní API](media/functions-export-api-definition/api-definition.png)

1. Klikněte na **exportovat do PowerApps a flow**.

    ![Zdroj definice rozhraní API](media/functions-export-api-definition/export-api-1.png)

1. V pravém podokně použijte nastavení uvedené v tabulce.

    |Nastavení|Popis|
    |--------|------------|
    |**Režim exportu**|Vyberte **Express** pro automatické generování vlastního rozhraní API. Výběr **ručních** exportů definice rozhraní API, ale pak je musíte importovat do PowerApps a Microsoft Flow ručně. Další informace najdete v tématu [Export do PowerApps a Microsoft Flow](../articles/azure-functions/app-service-export-api-to-powerapps-and-flow.md).|
    |**Prostředí**|Vyberte prostředí, do kterého se má vlastní rozhraní API Uložit. Další informace najdete v tématech [Přehled prostředí (PowerApps)](https://powerapps.microsoft.com/tutorials/environments-overview/) nebo [přehled prostředí (Microsoft flow)](https://us.flow.microsoft.com/documentation/environments-overview-admin/).|
    |**Název vlastního rozhraní API**|Zadejte název, třeba `Turbine Repair`.|
    |**Název klíče rozhraní API**|Zadejte název, který by měl sestavování aplikací a toků zobrazit ve vlastním uživatelském rozhraní API. Všimněte si, že příklad obsahuje užitečné informace.|
 
    ![Export do PowerApps a Microsoft Flow](media/functions-export-api-definition/export-api-2.png)

1. Klikněte na **OK**. Vlastní rozhraní API je teď sestavené a přidané do zadaného prostředí.