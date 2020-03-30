---
title: 'Úvodní příručka: Interaktivní vyhledávání map pomocí Azure Maps | Mapy Microsoft Azure'
description: Zjistěte, jak vytvořit ukázkovou webovou aplikaci pro interaktivní vyhledávání map pomocí sady Microsoft Azure Maps web SDK.
author: philmea
ms.author: philmea
ms.date: 1/14/2020
ms.topic: quickstart
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 13dc5f6c7175e1ed568199abcbaa4c5d9a20fa7f
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80334420"
---
# <a name="quickstart-create-an-interactive-search-map-by-using-azure-maps"></a>Úvodní příručka: Vytvoření interaktivní mapy vyhledávání pomocí Azure Maps

Tento článek ukazuje možnosti Azure Maps pro vytvoření mapy, která uživatelům nabídne interaktivní hledání. Provede vás těmito základními kroky:
* Vytvořte si vlastní účet Azure Maps.
* Získejte klíč svého účtu k použití v demo webové aplikaci.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k [portálu Azure](https://portal.azure.com).

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

Po úspěšném vytvoření účtu Maps načtěte klíč, který vám umožní dotazovat rozhraní Maps API. Doporučujeme používat primární klíč vašeho účtu jako klíč předplatného při volání služeb Azure Maps.

1. Otevřete svůj účet Maps na portálu.
2. V části nastavení vyberte **Možnost Ověřování**.
3. Zkopírujte do schránky **Primární klíč**. Místně ho uložte, abyste ho mohli použít později v tomto kurzu.

![Získání klíče Map Azure primárního klíče na webu Azure Portal](./media/quick-demo-map-app/get-key.png)

## <a name="download-the-application"></a>Stažení aplikace

1. Přejděte na [interactiveSearch.html](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/interactiveSearch.html) a kliknutím na něj zobrazte obsah v uživatelském rozhraní GitHubu. Klikněte pravým tlačítkem myši na tlačítko **Raw** a zkopírujte obsah souboru nebo "Uložit jako" pro stažení souboru.
2. Obsah tohoto souboru uložte místně jako **AzureMapDemo.html**. Otevřete ji v textovém editoru.
3. Vyhledejte řetězec `<Your Azure Maps Key>`. Nahraďte ji hodnotou **Primární klíč** z předchozíčásti.

## <a name="open-the-application"></a>Otevření aplikace

1. V libovolném prohlížeči otevřete soubor **AzureMapDemo.html**.
2. Sledujte mapu města Los Angeles. Mapu můžete přiblížit nebo oddálit a ta pak v závislosti na úrovni přiblížení vykreslí více nebo méně informací. 
3. Změňte výchozí střed mapy. V souboru **AzureMapDemo.html** vyhledejte proměnnou **center**. Nahraďte hodnotu páru zeměpisné délky a šířky pro tuto proměnnou novými hodnotami **[-74.0060, 40.7128]**. Uložte soubor a aktualizujte prohlížeč.
4. Vyzkoušejte interaktivní hledání. Ve vyhledávacím poli v levém horním rohu ukázkové webové aplikace vyhledejte **restaurace**.
5. Přesuňte ukazatel myši na seznam adres a míst, která se zobrazí pod vyhledávacím polem. Všimněte si, jak se odpovídající pin na mapě objeví informace o daném místě. Z důvodu zajištění ochrany soukromých podniků jsou zobrazené fiktivní názvy a adresy.

    ![Webová aplikace pro interaktivní vyhledávání map](./media/quick-demo-map-app/interactive-search.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Kurzy podrobně popisují, jak používat a konfigurovat Azure Maps s vaším účtem. Nečistěte prostředky vytvořené v tomto rychlém startu, pokud máte v plánu pokračovat v kurzech. Pokud neplánujete pokračovat, postupujte takto, abyste prostředky vyčistili:

1. Zavřete prohlížeč, ve kterému běží webová aplikace **AzureMapDemo.html.**
2. V levé nabídce na webu Azure Portal vyberte **Všechny prostředky**. Pak vyberte svůj účet Azure Maps. V horní části okna **Všechny prostředky** vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste si vytvořili účet Azure Maps a vytvořili ukázkovou aplikaci. Podívejte se na následující kurzy, kde se dozvíte o Azure Maps:

> [!div class="nextstepaction"]
> [Vyhledávání v okolí bodů zájmu pomocí Azure Maps](tutorial-search-location.md)

Další příklady kódu a interaktivní kódování, naleznete v těchto průvodcích:

> [!div class="nextstepaction"]
> [Vyhledání adresy pomocí vyhledávací služby Azure Maps](how-to-search-for-address.md)

> [!div class="nextstepaction"]
> [Použití ovládacího prvku mapy Azure](how-to-use-map-control.md)
