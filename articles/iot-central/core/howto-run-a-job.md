---
title: Vytvoření a spuštění úloh v aplikaci Azure IoT Central | Microsoft Docs
description: Úlohy Azure IoT Central umožňují využít možnosti správy hromadných zařízení, jako je například aktualizace vlastností nebo provádění příkazu.
ms.service: iot-central
services: iot-central
author: sarahhubbard
ms.author: sahubbar
ms.date: 03/03/2020
ms.topic: how-to
manager: peterpr
ms.openlocfilehash: c9d5a0daa364b09e45699e898511c28d4b4d92ce
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80157751"
---
# <a name="create-and-run-a-job-in-your-azure-iot-central-application"></a>Vytvoření a spuštění úlohy v aplikaci Azure IoT Central

Pomocí Microsoft Azure IoT Central můžete spravovat připojená zařízení ve velkém měřítku pomocí úloh. Úlohy umožňují hromadné aktualizace vlastností zařízení a spouštění příkazů. V tomto článku se dozvíte, jak začít používat úlohy ve vaší vlastní aplikaci.

## <a name="create-and-run-a-job"></a>Vytvoření a spuštění úlohy

V této části se dozvíte, jak vytvořit a spustit úlohu. Ukazuje, jak nastavit prahovou hodnotu pro skupinu zařízení s logistickou bránou.

1. V levém podokně přejděte na **úlohy** .

2. Vyberte **+ Nová** a vytvořte novou úlohu:

    ![Vytvořit novou úlohu](./media/howto-run-a-job/createnewjob.png)

3. Zadejte název a popis pro identifikaci úlohy, kterou vytváříte.

4. Vyberte cílovou skupinu zařízení, na kterou se má vaše úloha vztahovat. V části **Souhrn** můžete zjistit, kolik zařízení vaše konfigurace úlohy používá.

5. Potom jako typ úlohy, kterou chcete nakonfigurovat, vyberte buď **vlastnost cloudu**, **vlastnost** nebo **příkaz** . Chcete-li nastavit konfiguraci úlohy **vlastností** , vyberte vlastnost a nastavte její novou hodnotu. Pokud chcete nastavit **příkaz**, vyberte příkaz, který se má spustit. Úloha vlastnosti může nastavit více vlastností:

    ![Konfigurovat úlohu](./media/howto-run-a-job/configurejob.png)

6. Po vytvoření úlohy klikněte na tlačítko **Spustit** nebo **Uložit**. Úloha se teď zobrazí na stránce hlavní **úlohy** . Na této stránce můžete vidět aktuálně spuštěnou úlohu a historii všech dříve spuštěných nebo uložených úloh. Uloženou úlohu můžete kdykoli znovu otevřít, aby ji bylo možné dál upravovat nebo spustit:

    ![Zobrazit úlohu](./media/howto-run-a-job/viewjob.png)

    > [!NOTE]
    > Můžete zobrazit 30 dní historie pro dříve spuštěné úlohy.

7. Pokud chcete získat přehled o vaší úloze, vyberte úlohu, kterou chcete zobrazit v seznamu. Tento přehled obsahuje podrobnosti úlohy, zařízení a stavové hodnoty zařízení. Z tohoto přehledu můžete také vybrat **Stáhnout podrobnosti o úloze** a stáhnout soubor CSV s podrobnostmi o úloze, včetně zařízení a jejich stavových hodnot. Tyto informace mohou být užitečné při řešení potíží:

    ![Zobrazení stavu zařízení](./media/howto-run-a-job/downloaddetails.png)

### <a name="manage-a-job"></a>Správa úlohy

Pokud chcete zastavit jednu ze spuštěných úloh, otevřete ji a vyberte **zastavit**. Stav úlohy se změní, aby odrážela zastavení úlohy. V části **Souhrn** se zobrazuje, která zařízení jsou dokončená, neúspěšná nebo pořád čekají na vyřízení.

Pokud chcete spustit úlohu, která je aktuálně zastavená, vyberte ji a pak vyberte **Spustit**. Změna stavu úlohy se projeví v závislosti na tom, že úloha je nyní spuštěna. Oddíl **summary (souhrn** ) se stále aktualizuje s nejnovějším průběhem.

![Spravovat úlohu](./media/howto-run-a-job/managejob.png)

## <a name="copy-a-job"></a>Kopírování úlohy

Chcete-li zkopírovat jednu ze stávajících úloh, vyberte ji na stránce **úlohy** a vyberte možnost **Kopírovat**. Otevře se kopie konfigurace úlohy, která se má upravit, a **kopie** se připojí k názvu úlohy. Novou úlohu můžete uložit nebo spustit:

![Kopírovat úlohu](./media/howto-run-a-job/copyjob.png)

## <a name="view-the-job-status"></a>Zobrazení stavu úlohy

Po vytvoření úlohy se sloupec **stav** aktualizuje o nejnovější stavovou zprávu úlohy. Hodnoty možných stavů jsou uvedené v následující tabulce:

| Zpráva o stavu       | Význam stavu                                          |
| -------------------- | ------------------------------------------------------- |
| Dokončeno            | Tato úloha se spustila na všech zařízeních.              |
| Failed               | Tato úloha se nezdařila a na zařízeních se nedokončilo úplné spuštění.  |
| Čekající na vyřízení              | Tato úloha se ještě na zařízeních nezačala spouštět.         |
| Spuštěno              | Tato úloha se v tuto chvíli spouští na zařízeních.             |
| Zastaveno              | Tuto úlohu zastavil uživatel ručně.           |

Stavová zpráva je následován přehledem zařízení v úloze. Následující tabulka uvádí možné hodnoty stavu zařízení:

| Zpráva o stavu       | Význam stavu                                                     |
| -------------------- | ------------------------------------------------------------------ |
| Úspěch            | Počet zařízení, na kterých byla úloha úspěšně provedena.       |
| Failed               | Počet zařízení, na kterých se úloha nedokázala spustit.       |

### <a name="view-the-device-status"></a>Zobrazit stav zařízení

Pokud chcete zobrazit stav úlohy a všechna postižená zařízení, otevřete úlohu. Pokud chcete stáhnout soubor CSV, který obsahuje podrobnosti úlohy, včetně seznamu zařízení a jejich stavových hodnot, vyberte **Stáhnout podrobnosti o úloze**. Vedle každého názvu zařízení se zobrazí jedna z následujících stavových zpráv:

| Zpráva o stavu       | Význam stavu                                                                |
| -------------------- | ----------------------------------------------------------------------------- |
| Dokončeno            | Úloha se spustila na tomto zařízení.                                     |
| Failed               | U tohoto zařízení se nepovedlo spustit úlohu. V této chybové zprávě se zobrazí další informace.  |
| Čekající na vyřízení              | Tato úloha na tomto zařízení ještě nebyla spuštěna.                                   |

> [!NOTE]
> Pokud se zařízení odstranilo, nemůžete zařízení vybrat. Zobrazuje se jako odstraněný s ID zařízení.

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili vytvářet úlohy v aplikaci Azure IoT Central, tady je několik dalších kroků:

- [správu zařízení](howto-manage-devices.md)
- [Verze šablony zařízení](howto-version-device-template.md)
