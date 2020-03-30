---
title: Vytváření a spouštění úloh v aplikaci Azure IoT Central | Dokumenty společnosti Microsoft
description: Úlohy Azure IoT Central umožňují možnosti hromadné správy zařízení, jako je například aktualizace vlastností nebo spuštění příkazu.
ms.service: iot-central
services: iot-central
author: sarahhubbard
ms.author: sahubbar
ms.date: 03/03/2020
ms.topic: how-to
manager: peterpr
ms.openlocfilehash: c9d5a0daa364b09e45699e898511c28d4b4d92ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80157751"
---
# <a name="create-and-run-a-job-in-your-azure-iot-central-application"></a>Vytvoření a spuštění úlohy v aplikaci Azure IoT Central

Microsoft Azure IoT Central můžete použít ke správě připojených zařízení ve velkém měřítku pomocí úloh. Úlohy umožňují hromadné aktualizace vlastností zařízení a spouštění příkazů. Tento článek ukazuje, jak začít používat úlohy ve vlastní aplikaci.

## <a name="create-and-run-a-job"></a>Vytvoření a spuštění úlohy

V této části se zobrazí způsob vytvoření a spuštění úlohy. Ukazuje, jak nastavit prahovou hodnotu světla pro skupinu logistických zařízení brány.

1. Přejděte na **Úlohy** z levého podokna.

2. Chcete-li vytvořit novou úlohu, vyberte **možnost + Nový:**

    ![Vytvořit novou úlohu](./media/howto-run-a-job/createnewjob.png)

3. Zadejte název a popis, abyste identifikovali úlohu, kterou vytváříte.

4. Vyberte skupinu cílových zařízení, na kterou se má úloha vztahovat. V části **Souhrn** uvidíte, na kolik zařízení se vztahuje konfigurace úlohy.

5. Dále zvolte buď **Vlastnost Cloud**, **Vlastnost** nebo **Příkaz** jako typ úlohy ke konfiguraci. Chcete-li nastavit konfiguraci **úlohy vlastnosti,** vyberte vlastnost a nastavte její novou hodnotu. Chcete-li nastavit **příkaz**, zvolte příkaz, který chcete spustit. Úloha vlastnosti může nastavit více vlastností:

    ![Konfigurace úlohy](./media/howto-run-a-job/configurejob.png)

6. Po vytvoření úlohy zvolte **Spustit** nebo **Uložit**. Úloha se nyní zobrazí na hlavní stránce **Úlohy.** Na této stránce můžete zobrazit aktuálně spuštěnou úlohu a historii všech dříve spuštěných nebo uložených úloh. Uloženou úlohu lze kdykoli znovu otevřít a pokračovat v úpravách nebo ji spustit:

    ![Zobrazit úlohu](./media/howto-run-a-job/viewjob.png)

    > [!NOTE]
    > Pro dříve spuštěné úlohy můžete zobrazit až 30 denní historii.

7. Chcete-li získat přehled o úloze, vyberte úlohu, kterou chcete zobrazit ze seznamu. Tento přehled obsahuje podrobnosti o úloze, zařízení a hodnoty stavu zařízení. V tomto přehledu můžete také vybrat **možnost Stáhnout podrobnosti o úloze** a stáhnout soubor CSV s podrobnostmi o úloze, včetně zařízení a jejich hodnot stavu. Tyto informace mohou být užitečné při řešení potíží:

    ![Zobrazení stavu zařízení](./media/howto-run-a-job/downloaddetails.png)

### <a name="manage-a-job"></a>Správa úlohy

Chcete-li zastavit jednu ze spuštěných úloh, otevřete ji a vyberte **zastavit**. Stav úlohy se změní tak, aby odrážel úlohu, je zastaven. Část **Souhrn** ukazuje, která zařízení byla dokončena, selhala nebo stále čekají na vyřízení.

Chcete-li spustit úlohu, která je aktuálně zastavená, vyberte ji a pak vyberte **Spustit**. Stav úlohy se změní tak, aby odrážel úlohu, je nyní znovu spuštěn. Část **Souhrn** pokračuje v aktualizaci s nejnovějším pokrokem.

![Spravovat úlohu](./media/howto-run-a-job/managejob.png)

## <a name="copy-a-job"></a>Kopírování úlohy

Chcete-li zkopírovat jednu ze svých existujících úloh, vyberte ji na stránce **Úlohy** a vyberte **Kopírovat**. Otevře se kopie konfigurace úlohy, kterou můžete upravit, a k názvu úlohy je připojena **kopie.** Novou úlohu můžete uložit nebo spustit:

![Kopírovat úlohu](./media/howto-run-a-job/copyjob.png)

## <a name="view-the-job-status"></a>Zobrazit stav úlohy

Po vytvoření úlohy se sloupec **Stav** aktualizuje s nejnovější zprávou o stavu úlohy. V následující tabulce jsou uvedeny možné hodnoty stavu:

| Zpráva o stavu       | Význam stavu                                          |
| -------------------- | ------------------------------------------------------- |
| Dokončeno            | Tato úloha byla provedena na všech zařízeních.              |
| Failed               | Tato úloha se nezdařila a není plně provedena na zařízeních.  |
| Čekající na vyřízení              | Tato úloha ještě nezačala provádět na zařízeních.         |
| Spuštěno              | Tato úloha je aktuálně spuštěna na zařízeních.             |
| Zastaveno              | Tato úloha byla ručně zastavena uživatelem.           |

Po zprávě o stavu následuje přehled zařízení v úloze. V následující tabulce jsou uvedeny možné hodnoty stavu zařízení:

| Zpráva o stavu       | Význam stavu                                                     |
| -------------------- | ------------------------------------------------------------------ |
| Úspěch            | Počet zařízení, na kterých byla úloha úspěšně spuštěna.       |
| Failed               | Počet zařízení, na kterých se úloha nepodařilo spustit.       |

### <a name="view-the-device-status"></a>Zobrazení stavu zařízení

Chcete-li zobrazit stav úlohy a všech ohrožených zařízení, otevřete úlohu. Chcete-li stáhnout soubor CSV, který obsahuje podrobnosti o úloze, včetně seznamu zařízení a jejich hodnot stavu, vyberte **položku Stáhnout podrobnosti o úloze**. Vedle každého názvu zařízení se zobrazí jedna z následujících stavových zpráv:

| Zpráva o stavu       | Význam stavu                                                                |
| -------------------- | ----------------------------------------------------------------------------- |
| Dokončeno            | Úloha byla provedena na tomto zařízení.                                     |
| Failed               | Úloha se v tomto zařízení nepodařilo provést. Chybová zpráva zobrazuje další informace.  |
| Čekající na vyřízení              | Úloha na tomto zařízení ještě nebyla provedena.                                   |

> [!NOTE]
> Pokud bylo zařízení odstraněno, nemůžete ho vybrat. Zobrazí se jako odstraněné s ID zařízení.

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili vytvářet pracovní místa v aplikaci Azure IoT Central, tady jsou některé další kroky:

- [Správa zařízení](howto-manage-devices.md)
- [Verze šablony zařízení](howto-version-device-template.md)
