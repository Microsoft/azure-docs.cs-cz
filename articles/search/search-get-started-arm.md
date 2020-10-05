---
title: Použití šablony Azure Resource Manager k nasazení služby
titleSuffix: Azure Cognitive Search
description: Pomocí šablony Azure Resource Manageru můžete rychle nasadit instanci služby Azure Kognitivní hledání.
manager: nitinme
author: tchristiani
ms.author: terrychr
ms.service: cognitive-search
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 07/15/2020
ms.openlocfilehash: c7d11b9e699ec4cba59d2af9e411692cbce35278
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "88640170"
---
# <a name="quickstart-deploy-cognitive-search-using-an-arm-template"></a>Rychlý Start: nasazení Kognitivní hledání pomocí šablony ARM

Tento článek vás provede procesem použití šablony Azure Resource Manager (šablona ARM) k nasazení prostředku Azure Kognitivní hledání v Azure Portal.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Pokud vaše prostředí splňuje požadavky a jste obeznámeni s používáním šablon ARM, vyberte tlačítko **Nasazení do Azure**. Šablona se otevře v prostředí Azure Portal.

[![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fazure-quickstart-templates%2Fmaster%2F101-azure-search-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="review-the-template"></a>Kontrola šablony

Šablona použitá v tomto rychlém startu je jednou z [šablon pro rychlý start Azure](https://azure.microsoft.com/resources/templates/101-azure-search-create/).

:::code language="json"source="~/quickstart-templates/101-azure-search-create/azuredeploy.json":::

Prostředek Azure definovaný v této šabloně:

- [Microsoft. Search/searchServices](/azure/templates/Microsoft.Search/searchServices): vytvoření služby Azure kognitivní hledání

## <a name="deploy-the-template"></a>Nasazení šablony

Vyberte následující obrázek a přihlaste se k Azure a otevřete šablonu. Šablona vytvoří prostředek služby Azure Kognitivní hledání.

[![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fazure-quickstart-templates%2Fmaster%2F101-azure-search-create%2Fazuredeploy.json)

Portál zobrazí formulář, který umožňuje snadno zadat hodnoty parametrů. Některé parametry jsou předem vyplněny výchozími hodnotami ze šablony. Budete muset zadat předplatné, skupinu prostředků, umístění a název služby. Pokud chcete použít Cognitive Services v kanálu pro [rozšíření AI](cognitive-search-concept-intro.md) , například k analýze souborů binárních obrázků pro text, vyberte umístění, které nabízí Kognitivní hledání i Cognitive Services. Obě služby se musí nacházet ve stejné oblasti pro úlohy rozšíření AI. Po vyplnění formuláře budete muset souhlasit s podmínkami a ujednáními a pak vybrat tlačítko koupit a dokončit nasazení.

> [!div class="mx-imgBorder"]
> ![Azure Portal zobrazení šablony](./media/search-get-started-arm/arm-portalscrnsht.png)

## <a name="review-deployed-resources"></a>Kontrola nasazených prostředků

Po dokončení nasazení získáte přístup k nové skupině prostředků a nové službě vyhledávání na portálu.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Další Kognitivní hledání rychlý Start a kurzy, které se na tomto rychlém startu sestavují. Pokud máte v úmyslu pokračovat v práci s dalšími rychlými starty a kurzy, možná budete chtít tento prostředek ponechat na místě. Pokud už je nepotřebujete, můžete odstranit skupinu prostředků, která odstraní službu Kognitivní hledání a související prostředky.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili Kognitivní hledání službu pomocí šablony ARM a ověřili jste nasazení. Další informace o Kognitivní hledání a Azure Resource Manager najdete dál v článcích níže.

- Přečtěte si [Přehled služby Azure kognitivní hledání](search-what-is-azure-search.md).
- [Vytvořte index](search-get-started-portal.md) pro vyhledávací službu.
- Pomocí Průvodce portálem [vytvořte ukázkovou aplikaci](search-create-app-portal.md) .
- [Vytvořte dovednosti](cognitive-search-quickstart-blob.md) k extrakci informací z vašich dat.
