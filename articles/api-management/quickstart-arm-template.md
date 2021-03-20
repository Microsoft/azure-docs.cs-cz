---
title: Rychlý Start – vytvoření instance služby Azure API Management pomocí šablony ARM
description: Naučte se, jak vytvořit instanci služby Azure API Management ve vrstvě pro vývojáře pomocí šablony Azure Resource Manager (šablona ARM).
services: azure-resource-manager
author: dlepow
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: danlep
ms.date: 10/09/2020
ms.openlocfilehash: 1610c51b613712c06410247e2eb673a50a9988b3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92792238"
---
# <a name="quickstart-create-a-new-azure-api-management-service-instance-using-an-arm-template"></a>Rychlý Start: vytvoření nové instance služby Azure API Management Service pomocí šablony ARM

V tomto rychlém startu se dozvíte, jak pomocí šablony Azure Resource Manager (šablony ARM) vytvořit instanci služby Azure API Management (APIM). APIM pomáhá organizacím při publikování rozhraní API pro externí, partnerské a interní vývojáře, aby odemkli potenciál jejich dat a služeb. Služba API Management nabízí základní možnosti pro zajištění úspěšného programu s rozhraním API prostřednictvím zapojení vývojářů, informací o podniku, analýz, zabezpečení a ochrany. APIM umožňuje vytvářet a spravovat moderní brány rozhraní API pro stávající služby back-end hostované kdekoli. Další informace najdete v tématu [Přehled](api-management-key-concepts.md).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Pokud vaše prostředí splňuje požadavky a jste obeznámeni s používáním šablon ARM, vyberte tlačítko **Nasazení do Azure**. Šablona se otevře v prostředí Azure Portal.

[![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azure-api-management-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="review-the-template"></a>Kontrola šablony

Šablona použitá v tomto rychlém startu je jednou z [šablon pro rychlý start Azure](https://azure.microsoft.com/resources/templates/101-azure-api-management-create/).

:::code language="json" source="~/quickstart-templates/101-azure-api-management-create/azuredeploy.json":::

Následující prostředek je definován v šabloně:

- **[Microsoft.ApiManagement/service](/azure/templates/microsoft.apimanagement/service)**

Další ukázky šablon pro Azure API Management najdete v [šablonách rychlý Start pro Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Apimanagement&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Nasazení šablony

1. Vyberte následující obrázek a přihlaste se k Azure a otevřete šablonu. Šablona vytvoří instanci služby API Management s automaticky generovaným názvem.

    [![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azure-api-management-create%2Fazuredeploy.json)

    V tomto příkladu je instance nakonfigurovaná na úrovni pro vývojáře, což je ekonomická možnost pro vyhodnocení API Management Azure. Tato vrstva není pro produkční použití. Další informace o škálování úrovní služby API Management najdete v tématu věnovaném [upgradu a škálování](upgrade-and-scale.md).

1. Vyberte nebo zadejte následující hodnoty.
    - **Předplatné:** Vyberte předplatné Azure.
    - **Skupina prostředků**: vyberte **vytvořit novou**, zadejte jedinečný název pro skupinu prostředků a pak vyberte **OK**.
    - **Oblast**: vyberte umístění pro skupinu prostředků. Příklad: **střed USA**.
    - **E-mail vydavatele**: Zadejte e-mailovou adresu pro příjem oznámení.
    - **Název vydavatele**: zadejte název, který zvolíte pro vydavatele rozhraní API.
    - **SKU**: přijměte výchozí hodnotu pro **vývojáře**.
    - **Počet SKU**: přijměte výchozí hodnotu.
    - **Umístění**: přijměte vygenerované umístění pro službu API Management.

    :::image type="content" source="media/quickstart-arm-template/create-instance-template.png" alt-text="Vlastnosti šablony API Management":::

1. Vyberte **zkontrolovat + vytvořit** a pak si prohlédněte podmínky a ujednání. Pokud souhlasíte, vyberte **vytvořit**.

    > [!TIP]
    >  Vytvoření a aktivace služby API Management ve vrstvě pro vývojáře může trvat 30 až 40 minut.

1. Po úspěšném vytvoření instance obdržíte oznámení:

    :::image type="content" source="media/quickstart-arm-template/deployment-notification.png" alt-text="Oznámení o nasazení":::

 K nasazení šablony se použije Azure Portal. Kromě Azure Portal můžete použít také Azure PowerShell, Azure CLI a REST API. Další informace o dalších metodách nasazení najdete v tématu [Nasazení šablon](../azure-resource-manager/templates/deploy-cli.md).

## <a name="review-deployed-resources"></a>Kontrola nasazených prostředků

Použijte Azure Portal ke kontrole nasazených prostředků nebo k vypsání nasazených prostředků použijte nástroje, jako je Azure CLI nebo Azure PowerShell.

1. V [Azure Portal](https://portal.azure.com)vyhledejte a vyberte **služby API Management** a vyberte instanci služby, kterou jste vytvořili.
1. Zkontrolujte vlastnosti služby na stránce **Přehled** .

:::image type="content" source="media/quickstart-arm-template/service-instance-created.png" alt-text="Stránka s přehledem služby":::

Když je vaše instance API Management služby online, jste připraveni ji použít. Začněte s kurzem [importu a publikování](import-and-publish.md) vašeho prvního rozhraní API.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud máte v úmyslu pokračovat v práci s dalšími kurzy, můžete chtít ponechat instanci API Management na místě. Pokud už je nepotřebujete, odstraňte skupinu prostředků, která odstraní prostředky ve skupině prostředků.

1. V [Azure Portal](https://portal.azure.com)vyhledejte a vyberte **skupiny prostředků**. **Skupiny prostředků** můžete také vybrat na **domovské** stránce.
1. Na stránce **skupiny prostředků** vyberte skupinu prostředků.
1. Na stránce skupina prostředků vyberte **Odstranit skupinu prostředků**.

    :::image type="content" source="media/quickstart-arm-template/delete-resource-group.png" alt-text="Odstranění skupiny prostředků":::
1. Zadejte název vaší skupiny prostředků a pak vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kurz: Import a publikování vašeho prvního rozhraní API](import-and-publish.md)
