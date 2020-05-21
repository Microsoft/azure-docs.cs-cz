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
ms.date: 03/20/2020
ms.openlocfilehash: 670cebe1c0f1c9002e33b729d0db9ee9f9a01283
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2020
ms.locfileid: "83682531"
---
# <a name="quickstart-deploy-cognitive-search-using-a-resource-manager-template"></a>Rychlý Start: nasazení Kognitivní hledání pomocí šablony Správce prostředků

Tento článek vás provede procesem použití šablony Správce prostředků k nasazení prostředku Azure Kognitivní hledání v Azure Portal.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), ještě než začnete.

## <a name="create-a-cognitive-search-service"></a>Vytvoření služby Kognitivní hledání

### <a name="review-the-template"></a>Kontrola šablony

Šablona použitá v tomto rychlém startu je ze [šablon Azure pro rychlý Start](https://azure.microsoft.com/resources/templates/101-azure-search-create/).

:::code language="json"source="~/quickstart-templates/101-azure-search-create/azuredeploy.json" range="1-86" highlight="4-50,70-85":::

Prostředek Azure definovaný v této šabloně:

- [Microsoft. Search/searchServices](https://docs.microsoft.com/azure/templates/Microsoft.Search/2015-08-19/searchServices): vytvoření služby Azure kognitivní hledání

### <a name="deploy-the-template"></a>Nasazení šablony

Vyberte následující obrázek a přihlaste se k Azure a otevřete šablonu. Šablona vytvoří prostředek služby Azure Kognitivní hledání.

[![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fazure-quickstart-templates%2Fmaster%2F101-azure-search-create%2Fazuredeploy.json)

Portál zobrazí formulář, který umožňuje snadno zadat hodnoty parametrů. Některé parametry jsou předem vyplněny výchozími hodnotami ze šablony. Budete muset zadat předplatné, skupinu prostředků, umístění a název služby. Pokud chcete použít Cognitive Services v kanálu pro [rozšíření AI](https://docs.microsoft.com/azure/search/cognitive-search-concept-intro) , například k analýze souborů binárních obrázků pro text, vyberte umístění, které nabízí Kognitivní hledání i Cognitive Services. Obě služby se musí nacházet ve stejné oblasti pro úlohy rozšíření AI. Po vyplnění formuláře budete muset souhlasit s podmínkami a ujednáními a pak vybrat tlačítko koupit a dokončit nasazení.

> [!div class="mx-imgBorder"]
> ![Azure Portal zobrazení šablony](./media/search-get-started-arm/arm-portalscrnsht.png)

## <a name="review-deployed-resources"></a>Kontrola nasazených prostředků

Po dokončení nasazení získáte přístup k nové skupině prostředků a nové službě vyhledávání na portálu.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Další Kognitivní hledání rychlý Start a kurzy, které se na tomto rychlém startu sestavují. Pokud máte v úmyslu pokračovat v práci s dalšími rychlými starty a kurzy, možná budete chtít tento prostředek ponechat na místě. Pokud už je nepotřebujete, můžete odstranit skupinu prostředků, která odstraní službu Kognitivní hledání a související prostředky.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili Kognitivní hledání službu pomocí Azure Resource Manager šablony a ověřili jste nasazení. Další informace o Kognitivní hledání a Azure Resource Manager najdete dál v článcích níže.

 - Přečtěte si [Přehled Azure kognitivní hledání](https://docs.microsoft.com/azure/search/search-what-is-azure-search)
 - [Vytvoření indexu](https://docs.microsoft.com/azure/search/search-get-started-portal) pro vyhledávací službu
 - [Vytvoření vyhledávací aplikace](https://docs.microsoft.com/azure/search/search-create-app-portal) pomocí Průvodce portálem
 - [Vytvoření dovednosti](https://docs.microsoft.com/azure/search/cognitive-search-quickstart-blob) k extrakci informací z vašich dat


