---
title: 'Rychlý Start: interaktivní vyhledávání map pomocí Azure Maps'
description: 'Rychlý Start: Naučte se vytvářet interaktivní a prohledávatelné mapy. Přečtěte si, jak vytvořit účet Azure Maps, získat primární klíč a použít web SDK k nastavení map aplikací.'
author: anastasia-ms
ms.author: v-stharr
ms.date: 7/10/2020
ms.topic: quickstart
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 99257b7bef83d0acef484118c7d53aea8eda168c
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91264282"
---
# <a name="quickstart-create-an-interactive-search-map-with-azure-maps"></a>Rychlý Start: vytvoření interaktivní vyhledávací mapy pomocí Azure Maps

V tomto článku se dozvíte, jak pomocí Azure Maps vytvořit mapu, která uživatelům poskytuje interaktivní možnosti vyhledávání. Provede vás těmito základními kroky:

* Vytvořte si vlastní účet Azure Maps.
* Získejte primární klíč, který se použije v ukázkové webové aplikaci.
* Stáhněte a otevřete ukázkovou aplikaci s mapou.

## <a name="prerequisites"></a>Požadavky

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

* Přihlaste se k webu [Azure Portal](https://portal.azure.com).

<a id="createaccount"></a>

## <a name="create-an-azure-maps-account"></a>Vytvoření účtu Azure Maps

Vytvořte nový Azure Maps účet pomocí následujících kroků:

1. V levém horním rohu webu [Azure Portal](https://portal.azure.com) klikněte na **Vytvořit prostředek**.
2. Do pole *Hledat na Marketplace* zadejte **Azure Maps**.
3. Z *výsledků*vyberte **Azure Maps**. Klikněte na tlačítko **Vytvořit**, které se zobrazí pod mapou.
4. Na stránce **Vytvořit účet Maps** zadejte následující hodnoty:
    * *Předplatné*, které chcete pro tento účet použít.
    * Název *skupiny prostředků* pro tento účet. Můžete zvolit možnost *Vytvořit novou* nebo *Použít existující* skupinu prostředků.
    * *Název* vašeho nového účtu.
    * *Cenová úroveň* pro tento účet.
    * Přečtěte si *Licenční podmínky* a *Prohlášení o zásadách ochrany osobních údajů* a zaškrtnutím příslušného políčka podmínky přijměte.
    * Klikněte na tlačítko **Vytvořit**.

    :::image type="content" source="./media/quick-demo-map-app/create-account.png" alt-text="Vytvoření účtu Maps na portálu":::

<a id="getkey"></a>

## <a name="get-the-primary-key-for-your-account"></a>Získání primárního klíče pro váš účet

Po úspěšném vytvoření účtu Maps načtěte primární klíč, který vám umožní dotazovat se na rozhraní API mapy.

1. Otevřete svůj účet Maps na portálu.
2. V části nastavení vyberte **ověřování**.
3. Zkopírujte do schránky **Primární klíč**. Místně ho uložte, abyste ho mohli použít později v tomto kurzu.

>[!NOTE]
> Pokud místo primárního klíče použijete klíč předplatného, nebude se vaše mapa správně vykreslovat. Z bezpečnostních důvodů se navíc doporučuje střídat mezi primárním a sekundárním klíčem. Pokud chcete otočit klíče, aktualizujte aplikaci tak, aby používala sekundární klíč, nasaďte a pak stisknutím tlačítka cyklus/obnovení vedle primárního klíče Vygenerujte nový primární klíč. Starý primární klíč bude zakázán. Další informace o rotaci klíčů najdete v tématu [nastavení Azure Key Vault s použitím rotace a auditování klíčů](https://docs.microsoft.com/azure/key-vault/secrets/key-rotation-log-monitoring) .

:::image type="content" source="./media/quick-demo-map-app/get-key.png" alt-text="Získání klíče primárního klíče Azure Maps v Azure Portal":::

## <a name="download-the-demo-application"></a>Stažení ukázkové aplikace

1. Přejít na [interactiveSearch.html](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/interactiveSearch.html). Zkopírujte obsah souboru.
2. Obsah tohoto souboru uložte místně jako **AzureMapDemo.html**. Otevřete ho v textovém editoru.
3. Vyhledejte řetězec `<Your Azure Maps Key>` . Nahraďte hodnotou **primárního klíče** z předchozí části.

## <a name="open-the-demo-application"></a>Otevřete ukázkovou aplikaci.

1. V libovolném prohlížeči otevřete soubor **AzureMapDemo.html**.
2. Sledujte mapu města Los Angeles. Mapu můžete přiblížit nebo oddálit a ta pak v závislosti na úrovni přiblížení vykreslí více nebo méně informací.
3. Změňte výchozí střed mapy. V souboru **AzureMapDemo.html** vyhledejte proměnnou **center**. Nahraďte hodnotu páru zeměpisné délky a šířky pro tuto proměnnou novými hodnotami **[-74.0060, 40.7128]**. Uložte soubor a aktualizujte prohlížeč.
4. Vyzkoušejte interaktivní hledání. Do vyhledávacího pole v levém horním rohu ukázkové webové aplikace vyhledejte **restaurace**.
5. Přesuňte ukazatel myši na seznam adres a umístění, která se zobrazí pod vyhledávacím polem. Všimněte si, jak odpovídající kód PIN na mapě vychází z informací o tomto umístění. Z důvodu zajištění ochrany soukromých podniků jsou zobrazené fiktivní názvy a adresy.

    :::image type="content" source="./media/quick-demo-map-app/interactive-search.png" alt-text="Webová aplikace interaktivního vyhledávání map":::


## <a name="clean-up-resources"></a>Vyčištění prostředků

>[!WARNING]
>Kurzy uvedené v části [Další kroky](#next-steps) podrobně popisují, jak použít a nakonfigurovat Azure Maps s vaším účtem. Pokud se chystáte pokračovat v kurzech, neprovádějte čištění prostředků vytvořených v rámci tohoto rychlého startu.

Pokud neplánujete pokračovat v kurzech, proveďte tyto kroky a vyčistěte prostředky:

1. Zavřete prohlížeč, na kterém běží webová aplikace **AzureMapDemo.html** .
2. Přejděte na stránku Azure Portal. Na hlavní stránce portálu vyberte **všechny prostředky** . Případně klikněte na ikonu nabídky v levém horním rohu. Vyberte **Všechny prostředky**.
3. Klikněte na účet Azure Maps. V horní části stránky klikněte na **Odstranit**.

Další příklady kódu a interaktivní prostředí pro kódování najdete v těchto příručkách:

[Najít adresu pomocí služby Azure Maps Search Service](how-to-search-for-address.md)

[Použití Azure Maps Ovládací prvek Mapa](how-to-use-map-control.md)

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili účet Azure Maps a vytvořili jste ukázkovou aplikaci. Další informace o Azure Maps najdete v následujících kurzech:

> [!div class="nextstepaction"]
> [Prohledejte okolní body zájmu pomocí Azure Maps](tutorial-search-location.md)