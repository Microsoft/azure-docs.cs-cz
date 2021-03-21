---
title: Jak spravovat nastavení? – Vlastní Překladatel
titleSuffix: Azure Cognitive Services
description: Jak spravovat nastavení, vytvářet pracovní prostory, sdílet pracovní prostor a spravovat klíč předplatného ve vlastním překladateli.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: lajanuar
ms.topic: conceptual
ms.openlocfilehash: 49801eddd748a88109bb7f6d075def03cd798754
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98895777"
---
# <a name="how-to-manage-settings"></a>Správa nastavení

Na stránce vlastní nastavení překladatele můžete sdílet svůj pracovní prostor, upravit klíč předplatného překladatele a odstranit pracovní prostor.

Přístup k stránce nastavení:

1. Přihlaste se k portálu [vlastního překladatele](https://portal.customtranslator.azure.ai/) .
2. Na portálu Custom Translator klikněte na ikonu ozubeného kolečka na bočním panelu.

    ![Nastavení odkazu](media/how-to/how-to-settings.png)

## <a name="associating-translator-subscription"></a>Přidružuje se odběr překladatele.

Ke školení nebo nasazení modelů potřebujete ke svému pracovnímu prostoru přidružit klíč předplatného překladatele.

Pokud předplatné nemáte, postupujte podle následujících kroků:

1. Přihlaste se k odběru a vytvořte prostředek překladatele. Postupujte podle pokynů, [jak se zaregistrovat k odběru překladatelů](../translator-how-to-signup.md) a získat klíč překladatele.
2. Poznamenejte si klíč k vašemu předplatnému překladatele. Obě Klíč1 nebo key2 jsou přijatelné.
3. Přejděte zpátky na vlastní portál překladatelů.

## <a name="create-a-new-workspace"></a>Vytvoření nového pracovního prostoru

1. Klikněte na tlačítko + vytvořit pracovní prostor v postranním panelu vlastní překladatel.

    ![Vytvořit nový pracovní prostor](media/how-to/create-new-workspace.png)

2. V dialogovém okně zadejte název nového pracovního prostoru.
3. Klikněte na tlačítko Další.
4. Vyberte typ předplatného.
5. Vyberte oblast předplatného. Oblast se musí shodovat s vybranou oblastí, když se vytvořil klíč prostředku překladatele.
6. Zadejte klíč pro odběr překladatele a potom klikněte na tlačítko Uložit.

    ![Dialog vytvořit nový pracovní prostor](media/how-to/create-new-workspace-dialog.png)

>[!Note]
>Vlastní Překladatel nepodporuje vytváření pracovních prostorů pro prostředek Translator Text API (označuje se také jako Klíč předplatného Azure), který byl vytvořen v rámci [povolené virtuální](../../../api-management/api-management-using-with-vnet.md)sítě.

### <a name="modify-existing-key"></a>Upravit existující klíč

1. Přejděte na stránku nastavení pro váš pracovní prostor.
2. Klikněte na změnit klíč.

    ![Jak přidat klíč předplatného](media/how-to/how-to-add-subscription-key.png)

3. V dialogovém okně zadejte klíč pro vaše předplatné překladatele a potom klikněte na tlačítko Uložit.

    ![Dialog Přidat klíč předplatného](media/how-to/how-to-add-subscription-key-dialog.png)

## <a name="manage-your-workspace"></a>Správa pracovního prostoru

Pracovní prostor je pracovní oblast pro vytváření a vytváření vlastního systému překladu. Pracovní prostor může obsahovat více projektů, modelů a dokumentů.

Pokud je třeba jinou část práce sdílet s různými lidmi, může být užitečné vytvořit několik pracovních prostorů.

## <a name="share-your-workspace"></a>Sdílení pracovního prostoru

Ve vlastním překladateli můžete sdílet svůj pracovní prostor s ostatními, pokud je potřeba sdílet jinou část vaší práce s různými lidmi.

1. Přejděte na stránku nastavení pracovního prostoru.
2. V části nastavení sdílení klikněte na tlačítko Přidat lidi.

    ![Sdílet pracovní prostor](media/how-to/share-workspace.png)

3. V dialogovém okně Zadejte čárkami oddělený seznam e-mailových adres, které chcete sdílet s tímto pracovním prostorem. Ujistěte se, že sdílíte s e-mailovou adresou, kterou osoba používá pro přihlášení k vlastnímu překladateli. Pak vyberte odpovídající úroveň oprávnění ke sdílení a klikněte na tlačítko Uložit.

    ![Dialogové okno sdílet pracovní prostor](media/how-to/share-workspace-dialog.png)

4. Pokud má váš pracovní prostor stále výchozí název "pracovní prostor", budete ho muset před sdílením pracovního prostoru změnit.
5. Klikněte na Uložit.

## <a name="sharing-permissions"></a>Oprávnění ke sdílení

1. **Čtecí modul:** Čtenář v pracovním prostoru bude moci zobrazit všechny informace v pracovním prostoru.

2. **Editor:** Editor v pracovním prostoru bude moci přidávat dokumenty, naučit modely a odstraňovat dokumenty a projekty. Můžou přidat klíč předplatného, ale nemůžou upravovat, na koho se pracovní prostor sdílí, odstranit pracovní prostor nebo změnit název pracovního prostoru.

3. **Vlastník:** Vlastník má k pracovnímu prostoru úplná oprávnění.

## <a name="change-sharing-permission"></a>Změnit oprávnění ke sdílení

Když se pracovní prostor sdílí, v části nastavení sdílení se zobrazí všechny e-mailové adresy, se kterými se tento pracovní prostor sdílí. Pokud máte přístup vlastníka k pracovnímu prostoru, můžete změnit stávající oprávnění ke sdílení pro každou e-mailovou adresu.

1. V části nastavení sdílení pro každý e-mail zobrazuje rozevírací nabídka aktuální úroveň oprávnění.

2. Klikněte na rozevírací nabídku a vyberte novou úroveň oprávnění, kterou chcete přiřadit k této e-mailové adrese.

    ![Sdílení nastavení oprávnění](media/how-to/sharing-permission-settings.png)

## <a name="pin-your-workspace"></a>Připnutí pracovního prostoru

Váš první vytvořený pracovní prostor je ve výchozím nastavení připnutý. Pokaždé, když se přihlásíte, připnuté pracovní prostor se zobrazí při zatížení webu. Pokud jste vytvořili spoustu pracovních prostorů a přejete si, abyste při přihlášení nastavili jeden z nich jako výchozí, budete ho muset připnout.

1. Na bočním panelu klikněte na název pracovního prostoru, který chcete připnout.
2. Přejděte na stránku nastavení pro váš pracovní prostor.
3. Klikněte na ikonu připnutí.

    ![Připnout pracovní prostor](media/how-to/how-to-pin-workspace.png)

## <a name="next-steps"></a>Další kroky

- Informace [o tom, jak vytvořit pracovní prostor a projekty](workspace-and-project.md)