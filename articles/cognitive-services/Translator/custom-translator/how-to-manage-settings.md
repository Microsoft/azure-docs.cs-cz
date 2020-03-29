---
title: Jak spravovat nastavení? - Vlastní Překladatel
titleSuffix: Azure Cognitive Services
description: Jak spravovat nastavení, vytvářet pracovní prostor, sdílet pracovní prostor a spravovat klíč předplatného v překladači vlastních.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: f90ada6b6cd1760b3b779010625f252533fa611d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219508"
---
# <a name="how-to-manage-settings"></a>Jak spravovat nastavení

Na stránce Nastavení vlastního překladače můžete vytvořit nový pracovní prostor, sdílet pracovní prostor a přidat nebo upravit klíč předplatného microsoft translation.

Přístup ke stránce nastavení:

1. Přihlaste se k portálu [Vlastní překladač.](https://portal.customtranslator.azure.ai/)
2. Na portálu Custom Translator klikněte na ikonu ozubeného kola v postranním panelu.

    ![Nastavení odkazu](media/how-to/how-to-settings.png)

## <a name="associating-microsoft-translator-subscription"></a>Připojení předplatného microsoft translatoru

Chcete-li trénovat nebo nasazovat modely, musíte mít klíč předplatného rozhraní Microsoft Translator Text API přidružený k vašemu pracovnímu prostoru.

Pokud nemáte předplatné, postupujte podle následujících kroků:

1. Přihlaste se k odběru textového rozhraní Microsoft Translator API. Tento článek ukazuje, jak se přihlásit k odběru rozhraní Microsoft Translator Text API.
2. Poznámka: klíč pro předplatné překladatele. Key1 nebo Key2 jsou přijatelné.
3. Přejděte zpět na portál Vlastní překladač.

### <a name="add-existing-key"></a>Přidání existujícího klíče

1.  Přejděte na stránku Nastavení pracovního prostoru.
2.  Klikněte na Přidat klíč.

    ![Jak přidat klíč předplatného](media/how-to/how-to-add-subscription-key.png)

3. V dialogovém okně zadejte klíč pro předplatné překladatele a klikněte na tlačítko "Přidat".

    ![Jak přidat klíč předplatného](media/how-to/how-to-add-subscription-key-dialog.png)
4.  Po přidání klíče můžete klíč kdykoli upravit nebo odstranit.

    ![Klíč předplatného po přidání](media/how-to/subscription-key-after-add.png)

## <a name="manage-your-workspace"></a>Správa pracovního prostoru

Pracovní prostor je pracovní plocha pro vytváření a vytváření vlastního překladatelského systému. Pracovní prostor může obsahovat více projektů, modelů a dokumentů.

Pokud je potřeba sdílet jinou část vaší práce s různými lidmi, může být užitečné vytvořit více pracovních prostorů.

## <a name="create-a-new-workspace"></a>Vytvoření nového pracovního prostoru

1.  Přejděte na stránku Nastavení pracovního prostoru.
2.  Klikněte na tlačítko "Nový pracovní prostor" v části "Vytvořit nový pracovní prostor".

    ![Vytvoření nového pracovního prostoru](media/how-to/create-new-workspace.png)

4.  V dialogovém okně zadejte název nového pracovního prostoru.
5.  Klikněte na tlačítko "Vytvořit".

    ![Dialogové okno Vytvořit nový pracovní prostor](media/how-to/create-new-workspace-dialog.png)

## <a name="share-your-workspace"></a>Sdílení pracovního prostoru

V aplikaci Custom Translator můžete sdílet svůj pracovní prostor s ostatními, pokud je třeba sdílet jinou část vaší práce s různými lidmi.

1.  Přejděte na stránku Nastavení pracovního prostoru.
2.  Klikněte na tlačítko "Sdílet" v části "Nastavení sdílení".

    ![Sdílení pracovního prostoru](media/how-to/share-workspace.png)

3.  V dialogovém okně zadejte seznam e-mailových adres oddělených čárkami, se kterými má tento pracovní prostor sdílet. Ujistěte se, že sdílíte s e-mailovou adresu, kterou tato osoba používá k přihlášení k vlastní překladač s. Potom vyberte příslušnou úroveň oprávnění ke sdílení.

4.  Pokud váš pracovní prostor stále obsahuje výchozí název "Můj pracovní prostor", budete muset před sdílením pracovního prostoru jej změnit.
5.  Klikněte na tlačítko "Uložit".

## <a name="sharing-permissions"></a>Oprávnění ke sdílení

1.  **Čtečka:** Čtenář v pracovním prostoru bude moci zobrazit všechny informace v pracovním prostoru.

2.  **Editor:** Editor v pracovním prostoru bude moci přidávat dokumenty, trénování modelů a odstraňovat dokumenty a projekty. Mohou přidat klíč předplatného, ale nemohou změnit, s kým je pracovní prostor sdílen, odstranit pracovní prostor nebo změnit název pracovního prostoru.

3.  **Vlastník:** Vlastník má úplná oprávnění k pracovnímu prostoru.

## <a name="change-sharing-permission"></a>Změna oprávnění ke sdílení

Při sdílení pracovního prostoru se v části Nastavení sdílení zobrazují všechny e-mailové adresy, se kterými je tento pracovní prostor sdílen. Pokud máte přístup vlastníka k pracovnímu prostoru, můžete změnit stávající oprávnění ke sdílení pro každou e-mailovou adresu.

1.  V části "Nastavení sdílení" pro každý e-mail se v rozevírací nabídce zobrazí aktuální úroveň oprávnění.

2.  Klikněte na rozevírací nabídku a vyberte novou úroveň oprávnění, kterou chcete této e-mailové adrese přiřadit.

    ![Nastavení oprávnění ke sdílení](media/how-to/sharing-permission-settings.png)

## <a name="next-steps"></a>Další kroky

- [Zjistěte, jak migrovat pracovní prostor a projekt](how-to-migrate.md) z centra Microsoft Translator [Hub](https://hub.microsofttranslator.com)
