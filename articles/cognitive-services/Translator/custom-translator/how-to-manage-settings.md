---
title: Jak spravovat nastavení? – Vlastní Překladatel
titleSuffix: Azure Cognitive Services
description: Jak spravovat nastavení, vytvářet pracovní prostory, sdílet pracovní prostor a spravovat klíč předplatného ve vlastním překladateli.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 05/26/2020
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: af46b48d47a53d5a8a19db7c50a9356824607a5d
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2020
ms.locfileid: "85962947"
---
# <a name="how-to-manage-settings"></a>Správa nastavení

Na stránce vlastní nastavení překladatele můžete vytvořit nový pracovní prostor, sdílet svůj pracovní prostor a přidat nebo upravit klíč předplatného služby Microsoft Translation.

Přístup k stránce nastavení:

1. Přihlaste se k portálu [vlastního překladatele](https://portal.customtranslator.azure.ai/) .
2. Na portálu Custom Translator klikněte na ikonu ozubeného kolečka na bočním panelu.

    ![Nastavení odkazu](media/how-to/how-to-settings.png)

## <a name="associating-translator-subscription"></a>Přidružuje se odběr překladatele.

Ke školení nebo nasazení modelů potřebujete ke svému pracovnímu prostoru přidružit klíč předplatného překladatele.

Pokud předplatné nemáte, postupujte podle následujících kroků:

1. Přihlaste se k odběru překladatele. Tento článek ukazuje, jak se přihlásit k odběru překladatele.
2. Poznamenejte si klíč k vašemu předplatnému překladatele. Obě Klíč1 nebo key2 jsou přijatelné.
3. Přejděte zpátky na vlastní portál překladatelů.

### <a name="add-existing-key"></a>Přidat existující klíč

1.    Přejděte na stránku nastavení pro váš pracovní prostor.
2.    Klikněte na Přidat klíč.

   ![Jak přidat klíč předplatného](media/how-to/how-to-add-subscription-key.png)

3. V dialogovém okně zadejte klíč pro vaše předplatné překladatele a potom klikněte na tlačítko Přidat.

    ![Jak přidat klíč předplatného](media/how-to/how-to-add-subscription-key-dialog.png)
4.    Po přidání klíče můžete klíč kdykoli upravit nebo odstranit.

   ![Klíč předplatného po přidání](media/how-to/subscription-key-after-add.png)

## <a name="manage-your-workspace"></a>Správa pracovního prostoru

Pracovní prostor je pracovní oblast pro vytváření a vytváření vlastního systému překladu. Pracovní prostor může obsahovat více projektů, modelů a dokumentů.

Pokud je třeba jinou část práce sdílet s různými lidmi, může být užitečné vytvořit několik pracovních prostorů.

## <a name="create-a-new-workspace"></a>Vytvoření nového pracovního prostoru

1.    Přejděte na stránku nastavení pracovního prostoru.
2.    V části vytvořit nový pracovní prostor klikněte na tlačítko Nový pracovní prostor.

   ![Vytvořit nový pracovní prostor](media/how-to/create-new-workspace.png)

4.    V dialogovém okně zadejte název nového pracovního prostoru.
5.    Klikněte na vytvořit.

   ![Dialog vytvořit nový pracovní prostor](media/how-to/create-new-workspace-dialog.png)

## <a name="share-your-workspace"></a>Sdílení pracovního prostoru

Ve vlastním překladateli můžete sdílet svůj pracovní prostor s ostatními, pokud je potřeba sdílet jinou část vaší práce s různými lidmi.

1.    Přejděte na stránku nastavení pracovního prostoru.
2.    V části nastavení sdílení klikněte na tlačítko sdílet.

   ![Sdílet pracovní prostor](media/how-to/share-workspace.png)

3.    V dialogovém okně Zadejte čárkami oddělený seznam e-mailových adres, které chcete sdílet s tímto pracovním prostorem. Ujistěte se, že sdílíte s e-mailovou adresou, kterou osoba používá pro přihlášení k vlastnímu překladateli. Pak vyberte odpovídající úroveň oprávnění ke sdílení.

4.    Pokud má váš pracovní prostor stále výchozí název "pracovní prostor", budete ho muset před sdílením pracovního prostoru změnit.
5.    Klikněte na Uložit.

## <a name="sharing-permissions"></a>Oprávnění ke sdílení

1.    **Čtecí modul:** Čtenář v pracovním prostoru bude moci zobrazit všechny informace v pracovním prostoru.

2.    **Editor:** Editor v pracovním prostoru bude moci přidávat dokumenty, naučit modely a odstraňovat dokumenty a projekty. Můžou přidat klíč předplatného, ale nemůžou upravovat, na koho se pracovní prostor sdílí, odstranit pracovní prostor nebo změnit název pracovního prostoru.

3.    **Vlastník:** Vlastník má k pracovnímu prostoru úplná oprávnění.

## <a name="change-sharing-permission"></a>Změnit oprávnění ke sdílení

Když se pracovní prostor sdílí, v části nastavení sdílení se zobrazí všechny e-mailové adresy, se kterými se tento pracovní prostor sdílí. Pokud máte přístup vlastníka k pracovnímu prostoru, můžete změnit stávající oprávnění ke sdílení pro každou e-mailovou adresu.

1.    V části nastavení sdílení pro každý e-mail zobrazuje rozevírací nabídka aktuální úroveň oprávnění.

2.    Klikněte na rozevírací nabídku a vyberte novou úroveň oprávnění, kterou chcete přiřadit k této e-mailové adrese.

   ![Sdílení nastavení oprávnění](media/how-to/sharing-permission-settings.png)

## <a name="next-steps"></a>Další kroky

- Naučte [se migrovat pracovní prostor a projekt](how-to-migrate.md) z [centra Microsoft Translator](https://hub.microsofttranslator.com) .
