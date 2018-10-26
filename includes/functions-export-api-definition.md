---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 49ac1a7585ddf2a6500c7e9382880109c3f7f431
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2018
ms.locfileid: "50133267"
---
## <a name="export-an-api-definition"></a>Exportovat definici rozhraní API
Máte definici OpenAPI pro funkci, z [vytvoření definice OpenAPI pro funkci](../articles/azure-functions/functions-openapi-definition.md). Dalším krokem v tomto procesu je exportovat definice rozhraní API tak, aby PowerApps a Microsoft Flow můžete použít ve vlastních rozhraní API.

> [!IMPORTANT]
> Mějte na paměti, že musíte být přihlášeni do Azure pomocí stejných přihlašovacích údajů, které používáte pro vaši službu PowerApps a Microsoft Flow klienty. To umožňuje systému Azure k vytvoření vlastního rozhraní API a zpřístupní ji pro PowerApps a Microsoft Flow.

1. V [webu Azure portal](https://portal.azure.com), klikněte na název vaší aplikace funkcí (jako je **function-demo-energy**) > **funkce platformy** > **definice rozhraní API** .

    ![Definice rozhraní API](media/functions-export-api-definition/api-definition.png)

1. Klikněte na tlačítko **vyexportovat do PowerApps a Flow**.

    ![Zdroj definice rozhraní API](media/functions-export-api-definition/export-api-1.png)

1. V pravém podokně použijte nastavení uvedená v tabulce.

    |Nastavení|Popis|
    |--------|------------|
    |**Režim exportu**|Vyberte **Express** automaticky generovat vlastní rozhraní API. Výběr **ruční** definice exportů rozhraní API, ale potom je naimportujete do PowerApps a Microsoft Flow ručně. Další informace najdete v tématu [Export do PowerApps a Microsoft Flow](../articles/azure-functions/app-service-export-api-to-powerapps-and-flow.md).|
    |**Prostředí**|Vyberte prostředí, které by měl být uložen vlastního rozhraní API. Další informace najdete v tématu [Přehled prostředí (PowerApps)](https://powerapps.microsoft.com/tutorials/environments-overview/) nebo [Přehled prostředí (Microsoft Flow)](https://us.flow.microsoft.com/documentation/environments-overview-admin/).|
    |**Název vlastního rozhraní API**|Zadejte název, jako je třeba `Turbine Repair`.|
    |**Název klíče rozhraní API**|Zadejte název, který by se měla zobrazit tvůrci aplikací a toků v uživatelském rozhraní pro vlastní rozhraní API. Všimněte si, že příklad obsahuje užitečné informace.|
 
    ![Export do PowerApps a Microsoft Flow](media/functions-export-api-definition/export-api-2.png)

1. Klikněte na **OK**. Vlastní rozhraní API je teď vytvořené a přidány do prostředí, které jste zadali.