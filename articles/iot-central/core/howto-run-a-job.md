---
title: Vytvoření a spuštění úloh v aplikaci Azure IoT Central | Microsoft Docs
description: Úlohy Azure IoT Central umožňují využít možnosti správy hromadných zařízení, jako je například aktualizace vlastností nebo provádění příkazu.
ms.service: iot-central
services: iot-central
author: sarahhubbard
ms.author: sahubbar
ms.date: 07/08/2019
ms.topic: conceptual
manager: peterpr
ms.openlocfilehash: 2dd1eddc841cc484957c2124de3419799c4e59b7
ms.sourcegitcommit: 1fa2bf6d3d91d9eaff4d083015e2175984c686da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/01/2020
ms.locfileid: "78206784"
---
# <a name="create-and-run-a-job-in-your-azure-iot-central-application"></a>Vytvoření a spuštění úlohy v aplikaci Azure IoT Central

Pomocí Microsoft Azure IoT Central můžete spravovat připojená zařízení ve velkém měřítku pomocí úloh. Úlohy umožňují hromadné aktualizace vlastností zařízení a spouštění příkazů. V tomto článku se dozvíte, jak začít používat úlohy ve vaší vlastní aplikaci.

## <a name="create-and-run-a-job"></a>Vytvoření a spuštění úlohy

V této části se dozvíte, jak vytvořit a spustit úlohu. Ukazuje, jak nastavit prahovou hodnotu pro skupinu zařízení s logistickou bránou.

1. V levém podokně přejděte na **úlohy** .

2. Vyberte **+ Nová** a vytvořte novou úlohu:

    > [!div class="mx-imgBorder"]
    > ![vytvořit novou úlohu](./media/howto-run-a-job/createnewjob.png)

3. Zadejte název a popis pro identifikaci úlohy, kterou vytváříte.

4. Vyberte cílovou skupinu zařízení, na kterou se má vaše úloha vztahovat. V části **Souhrn** můžete zjistit, kolik zařízení vaše konfigurace úlohy používá.

5. Potom jako typ úlohy, kterou chcete nakonfigurovat, vyberte buď **vlastnost** , nebo **příkaz** . Chcete-li nastavit konfiguraci úlohy **vlastností** , vyberte vlastnost a nastavte její novou hodnotu. Chcete-li nastavit **příkaz**, nebo vyberte příkaz, který chcete spustit. Úloha vlastnosti může nastavit více vlastností:

    > [!div class="mx-imgBorder"]
    > ![konfiguraci](./media/howto-run-a-job/configurejob.png) úlohy

6. Po výběru zařízení zvolte **Spustit** nebo **Uložit**. Úloha se teď zobrazí na stránce hlavní **úlohy** . Na této stránce můžete vidět aktuálně spuštěnou úlohu a historii všech dříve spuštěných nebo uložených úloh. Uloženou úlohu můžete kdykoli znovu otevřít, aby ji bylo možné dál upravovat nebo spustit:

    > [!div class="mx-imgBorder"]
    > ](./media/howto-run-a-job/viewjob.png) ![zobrazení úlohy

    > [!NOTE]
    > Můžete zobrazit 30 dní historie pro dříve spuštěné úlohy.

7. Pokud chcete získat přehled o vaší úloze, vyberte úlohu, kterou chcete zobrazit v seznamu. Tento přehled obsahuje podrobnosti úlohy, zařízení a stavové hodnoty zařízení. Z tohoto přehledu můžete také vybrat **Stáhnout podrobnosti o úloze** a stáhnout soubor CSV s podrobnostmi o úloze, včetně zařízení a jejich stavových hodnot. Tyto informace mohou být užitečné při řešení potíží:

    > [!div class="mx-imgBorder"]
    > ![zobrazení stavu zařízení](./media/howto-run-a-job/downloaddetails.png)

### <a name="stop-a-running-job"></a>Zastavit běžící úlohu

Pokud chcete zastavit jednu ze spuštěných úloh, otevřete ji a vyberte **zastavit**. Stav úlohy se změní, aby odrážela zastavení úlohy. V části **Souhrn** se zobrazuje, která zařízení jsou dokončená, selhala nebo stále čekají:

    > [!div class="mx-imgBorder"]
    > ![Stop job](./media/howto-run-a-job/stopjob.png)

### <a name="run-a-stopped-job"></a>Spuštění zastavené úlohy

Pokud chcete spustit úlohu, která je aktuálně zastavená, vyberte ji a pak vyberte **Spustit**. Změna stavu úlohy se projeví v závislosti na tom, že úloha je nyní spuštěna. Část **Shrnutí** se stále aktualizuje s nejnovějším průběhem:

    > [!div class="mx-imgBorder"]
    > ![Resumed job](./media/howto-run-a-job/resumejob.png)

## <a name="copy-a-job"></a>Kopírování úlohy

Chcete-li zkopírovat jednu ze stávajících úloh, otevřete ji a vyberte možnost **Kopírovat**. Otevře se kopie konfigurace úlohy, která se má upravit, a **kopie** se připojí na konec názvu úlohy. Novou úlohu můžete uložit nebo spustit:

    > [!div class="mx-imgBorder"]
    > ![Copy job](./media/howto-run-a-job/copyjob.png)

## <a name="view-the-job-status"></a>Zobrazení stavu úlohy

Po vytvoření úlohy se sloupec **stav** aktualizuje o nejnovější stavovou zprávu úlohy. Hodnoty možných stavů jsou uvedené v následující tabulce:

| Zpráva o stavu       | Význam stavu                                          |
| -------------------- | ------------------------------------------------------- |
| Dokončeno            | Tato úloha se spustila na všech zařízeních.              |
| Neúspěch               | Tato úloha se nezdařila a na zařízeních se nedokončilo úplné spuštění.  |
| Čekající na vyřízení              | Tato úloha se ještě na zařízeních nezačala spouštět.         |
| Spuštěno              | Tato úloha se v tuto chvíli spouští na zařízeních.             |
| Zastaveno              | Tuto úlohu zastavil uživatel ručně.           |

Stavová zpráva je následován přehledem zařízení v úloze. Následující tabulka uvádí možné hodnoty stavu zařízení:

| Zpráva o stavu       | Význam stavu                                                     |
| -------------------- | ------------------------------------------------------------------ |
| Úspěch            | Počet zařízení, na kterých byla úloha úspěšně provedena.       |
| Neúspěch               | Počet zařízení, na kterých se úloha nedokázala spustit.       |

### <a name="view-the-device-status"></a>Zobrazit stav zařízení

Pokud chcete zobrazit stav úlohy a všechna postižená zařízení, vyberte úlohu. Pokud chcete stáhnout soubor CSV, který obsahuje podrobnosti úlohy, včetně seznamu zařízení a jejich stavových hodnot, vyberte **Stáhnout podrobnosti o úloze**. Vedle každého názvu zařízení se zobrazí jedna z následujících stavových zpráv:

| Zpráva o stavu       | Význam stavu                                                                |
| -------------------- | ----------------------------------------------------------------------------- |
| Dokončeno            | Úloha se spustila na tomto zařízení.                                     |
| Neúspěch               | U tohoto zařízení se nepovedlo spustit úlohu. V této chybové zprávě se zobrazí další informace.  |
| Čekající na vyřízení              | Tato úloha na tomto zařízení ještě nebyla spuštěna.                                   |

> [!NOTE]
> Pokud se zařízení odstranilo, nemůžete zařízení vybrat. Zobrazuje se jako odstraněný s ID zařízení.

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili vytvářet úlohy v aplikaci Azure IoT Central, tady je několik dalších kroků:

- [Správa zařízení](howto-manage-devices.md)
- [Verze šablony zařízení](howto-version-device-template.md)
