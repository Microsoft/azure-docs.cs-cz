---
title: 'Kurz pro JavaScript: Přehled integrace hledání'
titleSuffix: Azure Cognitive Search
description: Technický přehled a nastavení pro přidání vyhledávání na web a nasazení do statické webové aplikace Azure
manager: nitinme
author: diberry
ms.author: diberry
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 03/18/2021
ms.custom: devx-track-js
ms.devlang: javascript
ms.openlocfilehash: 03192b8a84b78682b53bf3d47e7de7b65eb8bceb
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "104726821"
---
# <a name="1---overview-of-adding-search-to-a-website"></a>1 – Přehled přidání vyhledávání na web

Tento kurz vytvoří web pro hledání katalogu knih a pak nasadí web do statické webové aplikace Azure. 

Aplikace je k dispozici: 
* [Ukázka](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/search-website)
* [Ukázkový web – aka.ms/azs-good-books](https://aka.ms/azs-good-books)

## <a name="what-does-the-sample-do"></a>Co ukázka dělá? 

Tento ukázkový web poskytuje přístup ke katalogu 10 000 knih. Uživatel může vyhledávat v katalogu zadáním textu do panelu hledání. Když uživatel zadá text, web použije funkci navrhnout indexu hledání k dokončení textu. Po dokončení dotazu se seznam knih zobrazí s částí podrobností. Uživatel může vybrat knihu a zobrazit všechny podrobnosti, které jsou uložené v indexu hledání knihy. 

:::image type="content" source="./media/tutorial-javascript-overview/cognitive-search-enabled-book-website.png" alt-text="Tento ukázkový web poskytuje přístup ke katalogu 10 000 knih. Uživatel může vyhledávat v katalogu zadáním textu do panelu hledání. Když uživatel zadá text, web použije funkci navrhnout indexu hledání k dokončení textu. Po dokončení hledání se seznam knih zobrazí s částí podrobností. Uživatel může vybrat knihu a zobrazit všechny podrobnosti, které jsou uložené v indexu hledání knihy.":::

Možnosti vyhledávání zahrnují: 

* Hledání – poskytuje funkci vyhledávání pro aplikaci.
* Návrh – poskytuje návrhy, které uživatel zadá do panelu hledání.
* Vyhledávání dokumentů – vyhledá dokument podle ID a načte veškerý jeho obsah na stránku podrobností.

## <a name="how-is-the-sample-organized"></a>Jak je ukázka uspořádána?

[Ukázka](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/search-website) zahrnuje následující:

|Aplikace|Účel|GitHub<br>Repository<br>Umístění|
|--|--|--|
|Klient|Aplikace reaguje (prezentační vrstva) k zobrazení knih a hledání. Volá aplikaci funkcí Azure Functions. |[/search-website/src](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/search-website/src)|
|Server|Azure Function App (obchodní vrstva) – volá rozhraní API Azure Kognitivní hledání pomocí sady JavaScript SDK. |[/search-website/api](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/search-website/src)|
|Hromadné vložení (Bulk insert)|JavaScriptový soubor pro vytvoření indexu a přidání dokumentů do tohoto souboru.|[/search-website/bulk-insert](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/search-website/bulk-insert)|

## <a name="set-up-your-development-environment"></a>Nastavíte vývojové prostředí

Pro místní vývojové prostředí nainstalujte následující: 

- [Node.js 12 nebo 14](https://nodejs.org/en/download)
    - Pokud máte v místním počítači nainstalovanou jinou verzi Node.js, zvažte použití [nástroje Node Version Manager](https://github.com/nvm-sh/nvm) (NVM) nebo kontejneru Docker.  
- [Git](https://git-scm.com/downloads)
- [Visual Studio Code](https://code.visualstudio.com/) a následující rozšíření
    - [Azure Resources](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureresourcegroups) (Prostředky Azure)
    - [Azure Kognitivní hledání 0.2.0 +](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurecognitivesearch)
    - [Statická webová aplikace Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestaticwebapps) 
- Volitelné:
    - V tomto kurzu se místně nespouští rozhraní API služby Azure Functions, ale pokud ho máte v úmyslu spustit místně, budete muset [Azure-Functions-Core-Tools](/azure/azure-functions/functions-run-local?tabs=linux%2Ccsharp%2Cbash) nainstalovat globálně s následujícím příkazem bash: 
    
    ```bash
    npm install -g azure-functions-core-tools
    ```

## <a name="fork-and-clone-the-search-sample-with-git"></a>Rozvětvení a klonování ukázky hledání pomocí Gitu

Rozvětvení ukázkového úložiště je důležité, aby bylo možné nasadit statickou webovou aplikaci. Webové aplikace určují akce sestavení a obsah nasazení na základě vlastního umístění rozvětvení GitHubu. Spouštění kódu ve statické webové aplikaci je vzdálené, přičemž Azure static Web Apps čte z kódu v rozvětvené ukázce.

1. Na GitHubu rozvětvete [ukázkové úložiště](https://github.com/Azure-Samples/azure-search-javascript-samples). 

    Dokončete proces rozvětvení ve webovém prohlížeči s vaším účtem GitHub. Tento kurz používá vaše rozvětvení jako součást nasazení do statické webové aplikace Azure. 

1. V terminálu bash Stáhněte ukázkovou aplikaci do svého místního počítače. 

    Nahraďte `YOUR-GITHUB-ALIAS` aliasem GitHubu. 

    ```bash
    git clone https://github.com/YOUR-GITHUB-ALIAS/azure-search-javascript-samples
    ```

1. V Visual Studio Code otevřete místní složku klonovaného úložiště. Zbývající úkoly jsou provedeny z Visual Studio Code, pokud nejsou zadány.

## <a name="create-a-resource-group-for-your-azure-resources"></a>Vytvoření skupiny prostředků pro prostředky Azure

1. V Visual Studio Code otevřete [řádek aktivity](https://code.visualstudio.com/docs/getstarted/userinterface)a vyberte ikonu Azure. 
1. Na bočním panelu **klikněte pravým tlačítkem na své předplatné Azure** v `Resource Groups` oblasti a vyberte **vytvořit skupinu prostředků**.

    :::image type="content" source="./media/tutorial-javascript-overview/visual-studio-code-create-resource-group.png" alt-text="Na bočním panelu * * klikněte pravým tlačítkem na své předplatné Azure * * v oblasti skupiny prostředků a vyberte * * vytvořit skupinu prostředků * *.":::
1. Zadejte název skupiny prostředků, například `cognitive-search-website-tutorial` . 
1. Vyberte umístění, které je blízko vás.
1. Když vytváříte prostředky Kognitivní hledání a statických webových aplikací, později v tomto kurzu použijete tuto skupinu prostředků. 

    Vytvořením skupiny prostředků získáte logickou jednotku pro správu prostředků, včetně jejich odstranění po jejich použití.

## <a name="next-steps"></a>Další kroky

* [Vytvoření indexu vyhledávání a načtení do dokumentů](tutorial-javascript-create-load-index.md)
* [Nasazení statické webové aplikace](tutorial-javascript-deploy-static-web-app.md)
