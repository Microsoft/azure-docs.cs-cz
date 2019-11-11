---
title: 'Rychlý Start: interaktivní vyhledávání map pomocí Azure Maps'
description: Rychlý Start Azure – Vytvoření ukázky interaktivního hledání v mapě pomocí Azure Maps
author: walsehgal
ms.author: v-musehg
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 1b9d8c98391e7e2bac3492dcf696f098f5c6cf57
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/10/2019
ms.locfileid: "73903184"
---
# <a name="quickstart-create-an-interactive-search-map-by-using-azure-maps"></a>Rychlý Start: vytvoření interaktivní vyhledávací mapy pomocí Azure Maps

Tento článek ukazuje možnosti Azure Maps pro vytvoření mapy, která uživatelům nabídne interaktivní hledání. Provede vás těmito základními kroky:
* Vytvořte si vlastní účet Azure Maps.
* Získejte klíč účtu, který se použije v ukázkové webové aplikaci.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlásit se na [Azure Portal](https://portal.azure.com/).

## <a name="create-an-account-and-get-your-key"></a>Vytvoření účtu a získání klíče

1. V levém horním rohu [Azure Portal](https://portal.azure.com)vyberte **vytvořit prostředek**.
2. Do pole **Hledat na Marketplace** zadejte **Maps**.
3. Ve **výsledcích** vyberte **Maps**. Vyberte tlačítko **vytvořit** , které se zobrazí pod mapou.
4. Na stránce **vytvořit Azure Maps účet** zadejte následující hodnoty:
   - **Název** vašeho nového účtu.
   - **Předplatné**, které chcete pro tento účet použít.
   - **Skupinu prostředků** pro tento účet. Můžete se rozhodnout **vytvořit novou** nebo **použít existující** skupinu prostředků.
   - Vyberte **cenovou úroveň** dle vašeho výběru.
   - Přečtěte si prohlášení o **licencích** a **zásadách ochrany osobních údajů**. Zaškrtnutím políčka přijměte podmínky.
   - Nakonec klikněte na tlačítko **vytvořit** .

     ![Vytvoření účtu Azure Maps na portálu](./media/quick-demo-map-app/create-account.png)

5. Po úspěšném vytvoření účtu ho otevřete a vyhledejte část nastavení v nabídce účet. Vyberte **ověřování** pro zobrazení primárního a sekundárního klíče pro váš účet Azure Maps. Zkopírujte do místní schránky hodnotu **primárního klíče** pro použití v následující části.

## <a name="download-the-application"></a>Stažení aplikace

1. Stáhněte nebo zkopírujte obsah souboru [interactiveSearch.html](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/interactiveSearch.html).
2. Uložte obsah tohoto souboru místně jako **AzureMapDemo. html**. Otevřete ho v textovém editoru.
3. Vyhledejte řetězec `<Your Azure Maps Key>`. Nahraďte hodnotou **primárního klíče** z předchozí části.

## <a name="open-the-application"></a>Otevření aplikace

1. V libovolném prohlížeči otevřete soubor **AzureMapDemo.html**.
2. Sledujte mapu města Los Angeles. Mapu můžete přiblížit nebo oddálit a ta pak v závislosti na úrovni přiblížení vykreslí více nebo méně informací. 
3. Změňte výchozí střed mapy. V souboru **AzureMapDemo.html** vyhledejte proměnnou **center**. Nahraďte hodnotu páru zeměpisné délky a šířky pro tuto proměnnou novými hodnotami **[-74.0060, 40.7128]** . Uložte soubor a aktualizujte prohlížeč.
4. Vyzkoušejte interaktivní hledání. Do vyhledávacího pole v levém horním rohu ukázkové webové aplikace vyhledejte **restaurace**.
5. Přesuňte ukazatel myši na seznam adres a umístění, která se zobrazí pod vyhledávacím polem. Všimněte si, jak odpovídající kód PIN na mapě vychází z informací o tomto umístění. Z důvodu zajištění ochrany soukromých podniků jsou zobrazené fiktivní názvy a adresy.

    ![Webová aplikace interaktivního hledání](./media/quick-demo-map-app/interactive-search.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

V kurzech najdete podrobné informace o používání a konfiguraci Azure Maps s vaším účtem. Pokud se chystáte pokračovat v kurzech, neprovádějte čištění prostředků vytvořených v rámci tohoto rychlého startu. Pokud pokračovat nechcete, proveďte následující kroky k vyčištění prostředků:

1. Zavřete prohlížeč, na kterém běží webová aplikace **AzureMapDemo. html** .
2. V nabídce vlevo v Azure Portal vyberte **všechny prostředky**. Pak vyberte svůj účet Azure Maps. V horní části okna **všechny prostředky** vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili účet Azure Maps a vytvořili jste ukázkovou aplikaci. Další informace o Azure Maps najdete v následujících kurzech:

> [!div class="nextstepaction"]
> [Prohledejte okolní body zájmu pomocí Azure Maps](tutorial-search-location.md)

Další příklady kódu a interaktivní prostředí pro kódování najdete v těchto příručkách:

> [!div class="nextstepaction"]
> [Vyhledání adresy pomocí služby Azure Maps Search](how-to-search-for-address.md)

> [!div class="nextstepaction"]
> [Použití Azure Maps Ovládací prvek Mapa](how-to-use-map-control.md)
