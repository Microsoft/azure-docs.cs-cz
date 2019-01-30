---
title: Jak spravovat nastavení? – Vlastní Translator
titleSuffix: Azure Cognitive Services
description: Jak spravovat nastavení, vytvořte pracovní prostor, sdílení pracovního prostoru a klíč předplatného ve vlastní Translator spravovat.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.subservice: custom-translator
ms.date: 11/13/2018
ms.author: v-rada
ms.topic: article
ms.openlocfilehash: 6fabbb57538f5e10e6cd91ebc109707045e54033
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55227076"
---
# <a name="how-to-manage-settings"></a>Jak spravovat nastavení

Na stránce nastavení vlastní Translator můžete vytvořit nový pracovní prostor, sdílení pracovního prostoru a přidat nebo upravit váš klíč předplatného Microsoft Translation.

Pro přístup ke stránce nastavení:

1. Přihlaste se k [vlastní Translator](https://portal.customtranslator.azure.ai/) portálu.
2. Na portálu vlastní Translator klikněte na ikonu ozubeného kola na bočním panelu.

    ![Odkaz nastavení](media/how-to/how-to-settings.png)

## <a name="associating-microsoft-translator-subscription"></a>Přidružením předplatného Microsoft Translatoru

Musíte mít předplatné Microsoft Translator Text API klíč přidružený k vašemu pracovnímu prostoru kvůli trénování nebo nasazení modelů.

Pokud předplatné nemáte, postupujte podle následujících kroků:

1. Předplaťte Microsoft Translator Text API. Tento článek ukazuje vytvoření odběru pro rozhraní Microsoft Translator Text API.
2. Poznačte si klíč pro vaše předplatné translator. Buď Key1 a Key2 jsou přijatelné.
3. Přejděte zpět na portál Translatoru Custom.

### <a name="add-existing-key"></a>Přidat existující klíč

1.  Přejděte na stránku "Nastavení" pro váš pracovní prostor.
2.  Klikněte na tlačítko Přidat klíč

    ![Jak přidat klíč předplatného](media/how-to/how-to-add-subscription-key.png)

3. V dialogovém okně zadejte klíč pro vaše předplatné translator a potom klikněte na tlačítko "Přidat".
 
    ![Jak přidat klíč předplatného](media/how-to/how-to-add-subscription-key-dialog.png)
4.  Po přidání klíče, můžete upravit nebo odstranit klíč v každém okamžiku.

    ![Přidat klíč předplatného po](media/how-to/subscription-key-after-add.png)

## <a name="manage-your-workspace"></a>Správa pracovního prostoru

Pracovní prostor je pracovní oblast pro sestavování a vytváření vlastních překladový systém. Pracovní prostor může obsahovat více projektů, modely a dokumenty. 

Pokud různé části vaší práce je potřeba sdílet s různým lidem, může být užitečné pak vytvořit víc pracovních prostorů. 

## <a name="create-a-new-workspace"></a>Vytvořit nový pracovní prostor

1.  Přejděte na stránku "Nastavení" pracovní prostor.
2.  Klikněte na "nový pracovní prostor" tlačítko v části "Vytvořit nový pracovní prostor".
    
    ![Vytvořit nový pracovní prostor](media/how-to/create-new-workspace.png)

4.  V dialogovém okně zadejte název nového pracovního prostoru.
5.  Klikněte na možnost "Vytvořit".
    
    ![Vytvořit dialogové okno Nový pracovní prostor](media/how-to/create-new-workspace-dialog.png)

## <a name="share-your-workspace"></a>Sdílení pracovního prostoru

Ve vlastní Translator můžete sdílet váš pracovní prostor s jinými uživateli, pokud jinou částí vaší práce je potřeba sdílet s různým lidem. 

1.  Přejděte na stránku "Nastavení" pracovní prostor.
2.  Klikněte na tlačítko "Sdílení" v části "Nastavení sdílení".
    
    ![Sdílení pracovního prostoru](media/how-to/share-workspace.png)

3.  V dialogovém okně zadejte čárkou oddělený seznam e-mailové adresy, které chcete, aby tento pracovní prostor dostaly. Ujistěte se, že sdílíte s e-mailovou adresu, uživatel používá pro přihlášení k vlastní Translator s. Potom vyberte vhodnou úroveň sdílení oprávnění.

4.  Pokud váš pracovní prostor stále obsahuje výchozí název "Můj pracovní prostor", bude muset změnit před sdílením pracovního prostoru.
5.  Klikněte na možnost "Uložit".

## <a name="sharing-permissions"></a>Oprávnění ke sdílení

1.  **Čtenář:** Čtečku v pracovním prostoru budou moct prohlížet všechny informace v pracovním prostoru. 

2.  **Editor:** V pracovním prostoru editoru budete moci přidat dokumenty, trénování modelů a odstraňování dokumentů a projektů. Můžete přidat klíč předplatného, ale nelze upravit, který pracovní prostor se sdílí s, odstranit pracovní prostor nebo změnit název pracovního prostoru.

3.  **Vlastník:** Vlastníka má úplná oprávnění k pracovnímu prostoru.

## <a name="change-sharing-permission"></a>Změnit oprávnění pro sdílení obsahu

Při sdílení pracovního prostoru v části "Nastavení sdílení" ukazuje všechny e-mailové adresy, které tento pracovní prostor se sdílí s. Můžete změnit existující sdílení oprávnění pro každou e-mailovou adresu, pokud máte přístup vlastníka do pracovního prostoru.

1.  V části "Nastavení sdílení" pro každého e-mailu do rozevírací nabídky zobrazí aktuální úroveň oprávnění.

2.  Klikněte na rozevírací nabídku a vyberte novou úroveň oprávnění, kterou chcete přiřadit tuto e-mailovou adresu.

    ![Nastavení oprávnění pro sdílení obsahu](media/how-to/sharing-permission-settings.png)

## <a name="next-steps"></a>Další postup

- Přečtěte si [migrace váš pracovní prostor a projekt](how-to-migrate.md) z [centra Microsoft Translator](https://hub.microsofttranslator.com)
