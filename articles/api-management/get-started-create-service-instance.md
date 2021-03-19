---
title: Rychlý Start – vytvoření instance služby Azure API Management
description: Pomocí Azure Portal vytvořte novou instanci služby Azure API Management.
author: vladvino
ms.service: api-management
ms.topic: quickstart
ms.custom: mvc
ms.date: 09/08/2020
ms.author: apimpm
ms.openlocfilehash: 66d0874536b358b720b43780d6ecce879ac866ee
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "90708202"
---
# <a name="quickstart-create-a-new-azure-api-management-service-instance-by-using-the-azure-portal"></a>Rychlý Start: vytvoření nové instance služby Azure API Management Service pomocí Azure Portal

Azure API Management (APIM) pomáhá organizacím při publikování rozhraní API pro externí, partnerské a interní vývojáře, aby tak uvolnila potenciál jejich dat a služeb. Služba API Management nabízí základní možnosti pro zajištění úspěšného programu s rozhraním API prostřednictvím zapojení vývojářů, informací o podniku, analýz, zabezpečení a ochrany. APIM umožňuje vytvářet a spravovat moderní brány rozhraní API pro stávající služby back-end hostované kdekoli. Další informace najdete v tématu [Přehled](api-management-key-concepts.md).

Tento rychlý start popisuje kroky pro vytvoření nové instance API Managementu pomocí webu Azure Portal.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

:::image type="content" source="media/get-started-create-service-instance/get-started-create-service-instance-created.png" alt-text="Instance API Management":::

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se na [Azure Portal](https://portal.azure.com).

## <a name="create-a-new-service"></a>Vytvoření nové služby

1. V nabídce webu Azure Portal vyberte **Vytvořit prostředek**. Na **domovské** stránce Azure můžete také vybrat **vytvořit prostředek** . 
   
   :::image type="content" source="media/get-started-create-service-instance/00-CreateResource-01.png" alt-text="Vyberte vytvořit prostředek.":::

   
1. Na stránce **Nový** vyberte možnost **integrace**  >  **API Management**.

   :::image type="content" source="media/get-started-create-service-instance/00-CreateResource-02.png" alt-text="Nová instance Azure API Managementu":::
   
1. Na stránce **API Management služba** zadejte nastavení.

   :::image type="content" source="media/get-started-create-service-instance/get-started-create-service-instance-create-new.png" alt-text="Nová instance":::
   
   | Nastavení                 | Popis   |                                                                     
   |-------------------------|-----------------------------------------------|
   | **Název**                | Jedinečný název služby API Management. Tento název není možné později změnit. Název služby odkazuje jak na službu, tak na odpovídající prostředek Azure. <br/> Název služby se používá k vygenerování výchozího názvu domény: *\<name\> . Azure-API.NET.* Pokud chcete použít vlastní název domény, přečtěte si téma [Konfigurace vlastní domény](configure-custom-domain.md). |
   | **Předplatné**          | Předplatné, v jehož rámci se tato nová instance služby vytvoří.   |
   | **Skupina prostředků**      |  Vyberte novou nebo existující skupinu prostředků. Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. |
   | **Umístění**          | Vyberte geografickou oblast poblíž dostupného umístění služby API Management. | 
   | **Název organizace**   | Název vaší organizace. Tento název se používá na řadě míst, včetně názvu portálu pro vývojáře a odesilatele e-mailů s oznámeními. |                                                         
   | **E-mail správce** | E-mailová adresa, na kterou budou odeslána všechna oznámení z **API Management** .   |  
   | **Cenová úroveň**        | Vyberte úroveň **vývojáře** pro vyhodnocení služby. Tato vrstva není pro produkční použití. Další informace o škálování úrovní služby API Management najdete v tématu věnovaném [upgradu a škálování](upgrade-and-scale.md). |

3. Vyberte **Vytvořit**.

    > [!TIP]
    > Vytvoření a aktivace služby API Management v této vrstvě může trvat 30 až 40 minut. Výběr možnosti **Připnout na řídicí panel** usnadňuje pozdější vyhledání nově vytvořené služby.

[!INCLUDE [api-management-navigate-to-instance](../../includes/api-management-navigate-to-instance.md)]

Zkontrolujte vlastnosti služby na stránce **Přehled** .

   :::image type="content" source="media/get-started-create-service-instance/get-started-create-service-instance-created.png" alt-text="Instance API Management":::

Když je vaše instance API Management služby online, jste připraveni ji použít. Začněte s kurzem [importu a publikování vašeho prvního rozhraní API](import-and-publish.md).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete k odebrání skupiny prostředků a všech souvisejících prostředků použít následující postup:

1. V Azure Portal vyhledejte a vyberte **skupiny prostředků**. **Skupiny prostředků** můžete také vybrat na **domovské** stránce. 

   :::image type="content" source="media/get-started-create-service-instance/00-DeleteResource-01.png" alt-text="Navigace skupiny prostředků":::

1. Na stránce **skupiny prostředků** vyberte skupinu prostředků.

   :::image type="content" source="media/get-started-create-service-instance/00-DeleteResource-02.png" alt-text="Výběr skupiny prostředků":::

1. Na stránce skupina prostředků vyberte **Odstranit skupinu prostředků**. 
   
1. Zadejte název vaší skupiny prostředků a pak vyberte **Odstranit**.

   :::image type="content" source="media/get-started-create-service-instance/00-DeleteResource-03.png" alt-text="Odstranění skupiny prostředků":::

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Import a publikování vašeho prvního rozhraní API](import-and-publish.md)
