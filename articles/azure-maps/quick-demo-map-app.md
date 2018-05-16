---
title: Interaktivní hledání v mapách s využitím Azure Maps | Microsoft Docs
description: Rychlý start Azure – Spuštění ukázky interaktivního hledání v mapách s využitím Azure Maps
services: azure-maps
keywords: ''
author: kgremban
ms.author: kgremban
ms.date: 05/07/2018
ms.topic: quickstart
ms.service: azure-maps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 8dedaf95289d9637f5f3d1e80a763b5fb400c617
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="launch-an-interactive-search-map-using-azure-maps"></a>Spuštění interaktivního hledání v mapách s využitím Azure Maps

Tento článek ukazuje možnosti Azure Maps pro vytvoření mapy, která uživatelům nabídne interaktivní hledání. Provede vás základními kroky k vytvoření vlastního účtu Maps a získání klíče účtu pro použití v ukázkové webové aplikaci. 

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.


## <a name="log-in-to-the-azure-portal"></a>Přihlášení k portálu Azure Portal

Přihlaste se k portálu [Azure Portal](https://portal.azure.com/).

## <a name="create-an-account-and-get-your-key"></a>Vytvoření účtu a získání klíče

1. V levém horním rohu webu [Azure Portal](https://portal.azure.com) klikněte na **Vytvořit prostředek**.
2. Do pole *Hledat na Marketplace* zadejte **Maps**.
3. Ve *výsledcích* vyberte **Maps**. Klikněte na tlačítko **Vytvořit**, které se zobrazí pod mapou. 
4. Na stránce **Vytvořit účet Maps** zadejte následující hodnoty:
    - *Název* vašeho nového účtu. 
    - *Předplatné*, které chcete pro tento účet použít.
    - *Skupinu prostředků* pro tento účet. Můžete zvolit možnost *Vytvořit novou* nebo *Použít existující* skupinu prostředků.
    - Vyberte *Umístění skupiny prostředků*.
    - Přečtěte si *Licenční podmínky* a *Prohlášení o zásadách ochrany osobních údajů* a zaškrtnutím příslušného políčka podmínky přijměte. 
    - Nakonec klikněte na tlačítko **Vytvořit**.

    ![Vytvoření účtu Maps na portálu](./media/quick-demo-map-app/create-account.png)

5. Po úspěšném vytvoření účtu ho otevřete a v nabídce účtu vyhledejte část Nastavení. Kliknutím na **Klíče** zobrazte primární a sekundární klíč pro váš účet Azure Maps. Zkopírujte do místní schránky hodnotu **primárního klíče** pro použití v následující části. 

## <a name="download-the-application"></a>Stažení aplikace

1. Stáhněte nebo zkopírujte obsah souboru [interactiveSearch.html](https://github.com/Azure-Samples/azure-maps-samples/blob/master/src/interactiveSearch.html).
2. Uložte obsah tohoto souboru místně jako **AzureMapDemo.html** a otevřete ho v textovém editoru.
3. Vyhledejte řetězec `<insert-key>` a nahraďte ho hodnotou **primárního klíče**, kterou jste získali v předchozí části. 


## <a name="launch-the-application"></a>Spuštění aplikace

1. V libovolném prohlížeči otevřete soubor **AzureMapDemo.html**.
2. Prohlédněte si zobrazenou mapu města Los Angeles. Mapu můžete přiblížit nebo oddálit a ta pak v závislosti na úrovni přiblížení vykreslí více nebo méně informací. 
3. Změňte výchozí střed mapy. V souboru **AzureMapDemo.html** vyhledejte proměnnou **center**. Nahraďte hodnotu páru zeměpisné délky a šířky pro tuto proměnnou novými hodnotami **[-74.0060, 40.7128]**. Uložte soubor a aktualizujte prohlížeč. 
3. Vyzkoušejte interaktivní hledání. Ve vyhledávacím poli v levém horním rohu ukázkové webové aplikace vyhledejte **restaurants** (restaurace). 
4. Najeďte myší na seznam adres a umístění, který se zobrazí pod vyhledávacím polem, a všimněte si, jak u odpovídajícího špendlíku na mapě vyskočí informace o tomto umístění. Z důvodu zajištění ochrany soukromých podniků jsou zobrazené fiktivní názvy a adresy. 

    ![Webová aplikace interaktivního hledání](./media/quick-demo-map-app/interactive-search.png)


## <a name="clean-up-resources"></a>Vyčištění prostředků

Další kurzy obsahují podrobný popis použití a konfigurace Maps s vaším účtem. Pokud chcete pokračovat k těmto kurzům, neprovádějte čištění prostředků vytvořených v rámci tohoto rychlého startu. Pokud pokračovat nechcete, pomocí následujícího postupu odstraňte všechny prostředky vytvořené tímto rychlým startem.

1. Zavřete prohlížeč se spuštěnou webovou aplikací **AzureMapDemo.html**.
2. V nabídce vlevo na webu Azure Portal klikněte na **Všechny prostředky** a vyberte svůj účet Maps. V horní části okna **Všechny prostředky** klikněte na **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili účet Maps a spustili jste ukázkovou aplikaci. V následujícím kurzu se dozvíte, jak vytvořit vlastní aplikaci s využitím rozhraní Maps API.

> [!div class="nextstepaction"]
> [Hledání bodů zájmu s využitím Maps](./tutorial-search-location.md)
