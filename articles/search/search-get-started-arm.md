---
title: Nasazení služby pomocí šablony Azure Resource Manageru
titleSuffix: Azure Cognitive Search
description: Instanci služby Azure Cognitive Search můžete rychle nasadit pomocí šablony Správce prostředků Azure.
manager: nitinme
author: tchristiani
ms.author: terrychr
ms.service: cognitive-search
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 03/20/2020
ms.openlocfilehash: 6da2c324872a86c2c0ce6f55801bc7b0ee8d713e
ms.sourcegitcommit: 515482c6348d5bef78bb5def9b71c01bb469ed80
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80607621"
---
# <a name="quickstart-deploy-cognitive-search-using-a-resource-manager-template"></a>Úvodní příručka: Nasazení kognitivního vyhledávání pomocí šablony Správce prostředků

Tento článek vás provede procesem použití šablony Správce prostředků k nasazení prostředku Azure Cognitive Search na webu Azure Portal.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Požadavky

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="create-a-cognitive-search-service"></a>Vytvoření služby kognitivního vyhledávání

### <a name="review-the-template"></a>Kontrola šablony

Šablona použitá v tomto rychlém startu je ze [šablon Azure](https://docs.microsoft.com/azure/templates/Microsoft.Search/2015-08-19/searchservices).

:::code language="json"source="~/quickstart-templates/101-azure-search-create/azuredeploy.json" range="1-86" highlight="4-50":::

Prostředek Azure definovaný v této šabloně:

- [Microsoft.Search/searchServices:](https://docs.microsoft.com/azure/templates/Microsoft.Search/2015-08-19/searchServices)vytvoření služby Azure Cognitive Search

### <a name="deploy-the-template"></a>Nasazení šablony

Vyberte následující obrázek a přihlaste se k Azure a otevřete šablonu. Šablona vytvoří prostředek Azure Cognitive Search.

[![Nasazení do Azure](./media/search-get-started-arm/arm-deploybuttona.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fazure-quickstart-templates%2Fmaster%2F101-azure-search-create%2Fazuredeploy.json)

Portál zobrazí formulář, který umožňuje snadno zadat hodnoty parametrů. Některé parametry jsou předvyplněny výchozími hodnotami ze šablony. Budete muset zadat předplatné, skupinu prostředků, umístění a název služby. Pokud chcete používat služby Cognitive Services v kanálu [obohacení AI,](https://docs.microsoft.com/azure/search/cognitive-search-concept-intro) například k analýze binárních obrazových souborů pro text, zvolte umístění, které nabízí služby Cognitive Search a Cognitive Services. Obě služby musí být ve stejné oblasti pro úlohy obohacení ai. Po vyplnění formuláře budete muset souhlasit s podmínkami a poté vybrat tlačítko nákupu k dokončení nasazení.

> [!div class="mx-imgBorder"]
> ![Zobrazení šablony na webu Azure Portal](./media/search-get-started-arm/arm-portalscrnsht.png)

## <a name="review-deployed-resources"></a>Kontrola nasazených prostředků

Po dokončení nasazení máte přístup k nové skupině prostředků a nové vyhledávací službě na portálu.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Další rychlé starty a kurzy kognitivního vyhledávání vycházejí z tohoto rychlého startu. Pokud máte v plánu pokračovat v práci s následnými rychlými starty a kurzy, můžete chtít ponechat tento prostředek na místě. Pokud již není potřeba, můžete odstranit skupinu prostředků, která odstraní službu Kognitivní vyhledávání a související prostředky.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili službu cognitive search pomocí šablony Azure Resource Manager a ověřili nasazení. Další informace o kognitivním vyhledávání a Správci prostředků Azure najdete v následujících článcích.

 - Přečtěte si [přehled Azure Cognitive Search](https://docs.microsoft.com/azure/search/search-what-is-azure-search)
 - [Vytvoření indexu](https://docs.microsoft.com/azure/search/search-get-started-portal) pro vyhledávací službu
 - [Vytvoření vyhledávací aplikace](https://docs.microsoft.com/azure/search/search-create-app-portal) pomocí průvodce portálem
 - [Vytvoření sady dovedností](https://docs.microsoft.com/azure/search/cognitive-search-quickstart-blob) pro extrahování informací z dat


