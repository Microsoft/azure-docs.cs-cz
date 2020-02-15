---
title: 'Rychlý Start: interaktivní vyhledávání map pomocí Azure Maps | Mapy Microsoft Azure'
description: Naučte se vytvářet ukázkovou webovou aplikaci pro interaktivní vyhledávání v mapě pomocí webové sady SDK služby Microsoft Azure Maps.
author: farah-alyasari
ms.author: v-faalya
ms.date: 1/14/2020
ms.topic: quickstart
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 74539a7ed74d0216f1a4bb2d719983895d8b320f
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2020
ms.locfileid: "77209626"
---
# <a name="quickstart-create-an-interactive-search-map-by-using-azure-maps"></a>Rychlý Start: vytvoření interaktivní vyhledávací mapy pomocí Azure Maps

Tento článek ukazuje možnosti Azure Maps pro vytvoření mapy, která uživatelům nabídne interaktivní hledání. Provede vás těmito základními kroky:
* Vytvořte si vlastní účet Azure Maps.
* Získejte klíč účtu, který se použije v ukázkové webové aplikaci.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k webu [Portál Azure](https://portal.azure.com).

<a id="createaccount"></a>

## <a name="create-an-account-with-azure-maps"></a>Vytvoření účtu s Azure Maps

Pomocí následujících kroků vytvořte nový účet Maps:

1. V levém horním rohu webu [Azure Portal](https://portal.azure.com) klikněte na **Vytvořit prostředek**.
2. Do pole *Hledat na Marketplace* zadejte **Maps**.
3. Ve *výsledcích* vyberte **Maps**. Klikněte na tlačítko **Vytvořit**, které se zobrazí pod mapou.
4. Na stránce **Vytvořit účet Maps** zadejte následující hodnoty:
    * *Předplatné*, které chcete pro tento účet použít.
    * Název *skupiny prostředků* pro tento účet. Můžete zvolit možnost *Vytvořit novou* nebo *Použít existující* skupinu prostředků.
    * *Název* vašeho nového účtu.
    * *Cenová úroveň* pro tento účet.
    * Přečtěte si *Licenční podmínky* a *Prohlášení o zásadách ochrany osobních údajů* a zaškrtnutím příslušného políčka podmínky přijměte.
    * Klikněte na tlačítko **Vytvořit**.

![Vytvoření účtu Maps na portálu](./media/quick-demo-map-app/create-account.png)

<a id="getkey"></a>

## <a name="get-the-primary-key-for-your-account"></a>Získání primárního klíče pro váš účet

Po úspěšném vytvoření účtu Maps načtěte klíč, který vám umožní dotazovat rozhraní Maps API. Při volání služeb Azure Maps doporučujeme použít primární klíč vašeho účtu jako klíč předplatného.

1. Otevřete svůj účet Maps na portálu.
2. V části nastavení vyberte **ověřování**.
3. Zkopírujte do schránky **Primární klíč**. Místně ho uložte, abyste ho mohli použít později v tomto kurzu.

![Získání klíče primárního klíče Azure Maps v Azure Portal](./media/quick-demo-map-app/get-key.png)

## <a name="download-the-application"></a>Stažení aplikace

1. Přejděte na [interactiveSearch. html](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/interactiveSearch.html) a kliknutím na něj zobrazíte obsah v uživatelském rozhraní GitHubu. Klikněte pravým tlačítkem na tlačítko **nezpracované** a zkopírujte obsah souboru nebo Uložit jako a Stáhněte soubor.
2. Uložte obsah tohoto souboru místně jako **AzureMapDemo. html**. Otevřete ho v textovém editoru.
3. Vyhledejte řetězec `<Your Azure Maps Key>`. Nahraďte hodnotou **primárního klíče** z předchozí části.

## <a name="open-the-application"></a>Otevření aplikace

1. V libovolném prohlížeči otevřete soubor **AzureMapDemo.html**.
2. Sledujte mapu města Los Angeles. Mapu můžete přiblížit nebo oddálit a ta pak v závislosti na úrovni přiblížení vykreslí více nebo méně informací. 
3. Změňte výchozí střed mapy. V souboru **AzureMapDemo.html** vyhledejte proměnnou **center**. Nahraďte hodnotu páru zeměpisné délky a šířky pro tuto proměnnou novými hodnotami **[-74.0060, 40.7128]** . Uložte soubor a aktualizujte prohlížeč.
4. Vyzkoušejte interaktivní hledání. Do vyhledávacího pole v levém horním rohu ukázkové webové aplikace vyhledejte **restaurace**.
5. Přesuňte ukazatel myši na seznam adres a umístění, která se zobrazí pod vyhledávacím polem. Všimněte si, jak odpovídající kód PIN na mapě vychází z informací o tomto umístění. Z důvodu zajištění ochrany soukromých podniků jsou zobrazené fiktivní názvy a adresy.

    ![Webová aplikace interaktivního vyhledávání map](./media/quick-demo-map-app/interactive-search.png)

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
