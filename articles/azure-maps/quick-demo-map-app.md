---
title: Interaktivní hledání v mapách pomocí Azure Maps | Dokumentace Microsoftu
description: Rychlý start Azure – vytvoření ukázku interaktivního hledání v mapách s využitím Azure Maps
author: walsehgal
ms.author: v-musehg
ms.date: 12/02/2018
ms.topic: quickstart
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 681a76de02d91b39ee74d1e4fa764c06d79ff3ab
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/07/2019
ms.locfileid: "54065133"
---
# <a name="create-an-interactive-search-map-by-using-azure-maps"></a>Vytvořte mapu interaktivního hledání s využitím Azure Maps

Tento článek ukazuje možnosti Azure Maps pro vytvoření mapy, která uživatelům nabídne interaktivní hledání. Provede vás pomocí těchto základních kroků:
* Vytvoření účtu Azure Maps.
* Získejte klíč k účtu pro použití v ukázkové webové aplikaci.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

## <a name="create-an-account-and-get-your-key"></a>Vytvoření účtu a získání klíče

1. V levém horním rohu [webu Azure portal](https://portal.azure.com)vyberte **vytvořit prostředek**.
2. V **Hledat na Marketplace** zadejte **mapy**.
3. Ve **výsledcích** vyberte **Maps**. Vyberte **vytvořit** tlačítko, které se zobrazí pod mapou.
4. Na **vytvořit účet Azure Maps** stránky, zadejte následující hodnoty:
    - **Název** vašeho nového účtu.
    - **Předplatné**, které chcete pro tento účet použít.
    - **Skupinu prostředků** pro tento účet. Můžete zvolit **vytvořit nový** nebo **použít existující** skupinu prostředků.
    - Vyberte **cenová úroveň** podle vašeho výběru.
    - Přečtěte si **licence** a **prohlášení o zásadách**. Zaškrtněte políčko pro přijetí podmínek.
    - Nakonec vyberte **vytvořit** tlačítko.

    ![Vytvoření účtu Azure Maps na portálu](./media/quick-demo-map-app/create-account.png)

5. Po úspěšném vytvoření účtu ho otevřete a vyhledejte část nastavení v nabídce účtu. Vyberte **klíče** zobrazíte primární a sekundární klíče pro váš účet Azure Maps. Zkopírujte do místní schránky hodnotu **primárního klíče** pro použití v následující části.

## <a name="download-the-application"></a>Stažení aplikace

1. Stáhněte nebo zkopírujte obsah souboru [interactiveSearch.html](https://github.com/Azure-Samples/azure-maps-samples/blob/master/src/interactiveSearch.html).
2. Uložte obsah tohoto souboru místně jako **AzureMapDemo.html**. Otevřete ho v textovém editoru.
3. Vyhledejte řetězec `<insert-key>`. Nahraďte ho hodnotou **primární klíč** hodnotu z předchozí části.

## <a name="open-the-application"></a>Otevření aplikace

1. V libovolném prohlížeči otevřete soubor **AzureMapDemo.html**.
2. Sledujte města Los Angeles zobrazené na mapě. Mapu můžete přiblížit nebo oddálit a ta pak v závislosti na úrovni přiblížení vykreslí více nebo méně informací. 
3. Změňte výchozí střed mapy. V souboru **AzureMapDemo.html** vyhledejte proměnnou **center**. Nahraďte hodnotu páru zeměpisné délky a šířky pro tuto proměnnou novými hodnotami **[-74.0060, 40.7128]**. Uložte soubor a aktualizujte prohlížeč.
4. Vyzkoušejte interaktivní hledání. Do vyhledávacího pole v levém horním rohu ukázkové webové aplikace, vyhledejte **restaurace**.
5. Najeďte myší seznam adres a umístění, které se zobrazí pod vyhledávacím polem. Všimněte si, jak odpovídajícího špendlíku na mapě vyskočí informace o tomto umístění. Z důvodu zajištění ochrany soukromých podniků jsou zobrazené fiktivní názvy a adresy.

    ![Webová aplikace interaktivního hledání](./media/quick-demo-map-app/interactive-search.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

V kurzech podrobně popisují, jak používat a nakonfigurovat svůj účet Azure Maps. Nechcete vyčistit prostředky vytvořené v rámci tohoto rychlého startu, pokud máte v plánu kurzech. Pokud pokračovat nechcete, vyčistit prostředky pomocí těchto kroků:

1. Zavřete prohlížeč, na kterém běží **AzureMapDemo.html** webovou aplikaci.
2. V nabídce vlevo na webu Azure Portal vyberte **všechny prostředky**. Pak vyberte svůj účet Azure Maps. V horní části **všechny prostředky** okně vyberte **odstranit**.

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste vytvořili účet Azure Maps a vytvořili ukázkovou aplikaci. Pokud chcete zjistit, jak vytvořit vlastní aplikaci pomocí rozhraní API služby Azure Maps, pokračujte k následujícímu kurzu:

> [!div class="nextstepaction"]
> [Hledání okolních bodů zájmu s použitím Azure Maps](./tutorial-search-location.md)

Další příklady kódu a interaktivní prostředí pro psaní kódu najdete v těchto návodů:

> [!div class="nextstepaction"]
> [Najít adresu pomocí služby vyhledávání Azure Maps](./how-to-search-for-address.md)

> [!div class="nextstepaction"]
> [Použití mapového ovládacího prvku Azure Maps](./how-to-use-map-control.md)
