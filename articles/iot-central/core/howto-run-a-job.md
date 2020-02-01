---
title: Vytvoření a spuštění úloh v aplikaci Azure IoT Central | Microsoft Docs
description: Úlohy Azure IoT Central umožňují využít možnosti správy hromadných zařízení, jako je aktualizace vlastnosti zařízení, nastavení nebo spuštění příkazu.
ms.service: iot-central
services: iot-central
author: sarahhubbard
ms.author: sahubbar
ms.date: 07/08/2019
ms.topic: conceptual
manager: peterpr
ms.openlocfilehash: 114946fa37ae161aeb2efd5b7cd50444c5df4c2b
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2020
ms.locfileid: "76906706"
---
# <a name="create-and-run-a-job-in-your-azure-iot-central-application"></a>Vytvoření a spuštění úlohy v aplikaci Azure IoT Central

Pomocí Microsoft Azure IoT Central můžete spravovat připojená zařízení ve velkém měřítku pomocí úloh. Úlohy umožňují hromadné aktualizace vlastností a příkazů zařízení. Tento článek vás seznámí s postupem, jak začít používat úlohy ve vaší vlastní aplikaci.


## <a name="create-and-run-a-job"></a>Vytvoření a spuštění úlohy

V této části se dozvíte, jak vytvořit a spustit úlohu. Ukazuje, jak zvýšit rychlost ventilátoru pro více chladírenských prodejních počítačů.

1. V navigačním podokně přejděte na úlohy.

2. Vyberte **+ Nová** a vytvořte novou úlohu.

    ![Vytvořit novou úlohu](./media/howto-run-a-job/createnewjob.png)

3. Zadejte název a popis pro identifikaci úlohy, kterou vytváříte.

4. Vyberte skupinu zařízení, na kterou se má vaše úloha vztahovat. V části Souhrn můžete zjistit, kolik zařízení vaše konfigurace úlohy bude platit. 

5. Dále vyberte typ úlohy, která se má definovat (vlastnost nebo příkaz). Nastavte konfiguraci úlohy tak, že vyberete vlastnost a nastavíte nové hodnoty nebo zvolíte příkaz. Současně lze přidat více vlastností.

    ![Konfigurovat úlohu](./media/howto-run-a-job/configurejob.png)

6. Po výběru zařízení zvolte **Spustit** nebo **Uložit**. Úloha se teď zobrazí na stránce hlavní **úlohy** . V tomto zobrazení uvidíte aktuálně spuštěnou úlohu a historii všech dříve spuštěných úloh. Spuštěná úloha se vždycky zobrazuje v horní části seznamu. Uloženou úlohu můžete kdykoli znovu otevřít, aby bylo možné pokračovat v úpravách nebo spouštění.

    ![Zobrazit úlohu](./media/howto-run-a-job/viewjob.png)

    > [!NOTE]
    > Historii dříve spuštěných úloh můžete zobrazit po dobu až 30 dnů.

7. Pokud chcete získat přehled o vaší úloze, vyberte úlohu, kterou chcete zobrazit v seznamu. Tento přehled obsahuje podrobnosti úlohy, zařízení a stavové hodnoty zařízení. Z tohoto přehledu můžete také vybrat **Stáhnout podrobnosti o úloze** a stáhnout soubor. csv s podrobnostmi o úloze, včetně zařízení a jejich stavových hodnot. Tyto informace mohou být užitečné při řešení potíží.

    ![Zobrazení stavu zařízení](./media/howto-run-a-job/downloaddetails.png)

### <a name="stop-a-running-job"></a>Zastavit běžící úlohu

Chcete-li zastavit běžící úlohu, vyberte ji a zvolte možnost **zastavit**. Stav úlohy se změní, aby odrážela zastavení úlohy.

   ![Zastavit úlohu](./media/howto-run-a-job/stopjob.png)

### <a name="run-a-stopped-job"></a>Spuštění zastavené úlohy

Pokud chcete spustit úlohu, která je aktuálně zastavená, vyberte zastavenou úlohu. Na panelu vyberte **Spustit** . Změna stavu úlohy se projeví v závislosti na tom, že úloha je nyní spuštěna.

   ![Obnovená úloha](./media/howto-run-a-job/resumejob.png)

## <a name="copy-a-job"></a>Kopírování úlohy

Chcete-li zkopírovat existující úlohu, kterou jste vytvořili, otevřete úlohu, která byla vytvořena, a vyberte možnost **Kopírovat**. Otevře se nová kopie konfigurace úlohy, kterou můžete upravit. Novou úlohu můžete uložit nebo spustit. 

   ![Kopírovat úlohu](./media/howto-run-a-job/copyjob.png)

## <a name="view-the-job-status"></a>Zobrazení stavu úlohy

Po vytvoření úlohy se sloupec **stav** aktualizuje o nejnovější stavovou zprávu úlohy. Hodnoty možných stavů jsou uvedené v následující tabulce:

| Zpráva o stavu       | Význam stavu                                          |
| -------------------- | ------------------------------------------------------- |
| Dokončeno            | Tato úloha se spustila na všech zařízeních.              |
| Selhalo               | Tato úloha se nezdařila a na zařízeních se nedokončilo úplné spuštění.  |
| Čekající na vyřízení              | Tato úloha se ještě na zařízeních nezačala spouštět.         |
| Spuštěno              | Tato úloha se v tuto chvíli spouští na zařízeních.             |
| Zastaveno              | Tuto úlohu zastavil uživatel ručně.           |

Stavová zpráva je následován přehledem zařízení v úloze. Následující tabulka uvádí možné hodnoty stavu zařízení:

| Zpráva o stavu       | Význam stavu                                                     |
| -------------------- | ------------------------------------------------------------------ |
| Úspěch            | Počet zařízení, na kterých byla úloha úspěšně provedena.       |
| Selhalo               | Počet zařízení, na kterých se úloha nedokázala spustit.       |

### <a name="view-the-device-status"></a>Zobrazit stav zařízení

Pokud chcete zobrazit stav úlohy a všechna postižená zařízení, vyberte úlohu. Pokud chcete stáhnout soubor. csv, který obsahuje podrobnosti úlohy, včetně seznamu zařízení a jejich stavových hodnot, vyberte **Stáhnout podrobnosti o úloze**. Vedle každého názvu zařízení se zobrazí jedna z následujících stavových zpráv:

| Zpráva o stavu       | Význam stavu                                                                |
| -------------------- | ----------------------------------------------------------------------------- |
| Dokončeno            | Úloha se spustila na tomto zařízení.                                     |
| Selhalo               | U tohoto zařízení se nepovedlo spustit úlohu. V této chybové zprávě se zobrazí další informace.  |
| Čekající na vyřízení              | Tato úloha na tomto zařízení ještě nebyla spuštěna.                                   |

> [!NOTE]
> Pokud se zařízení odstranilo, nemůžete zařízení vybrat a zobrazí se jako odstraněné s ID zařízení.

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili vytvářet úlohy v aplikaci Azure IoT Central, tady je několik dalších kroků:

- [Použití sad zařízení](howto-use-device-sets.md)
- [Správa zařízení](howto-manage-devices.md)
- [Verze šablony zařízení](howto-version-device-template.md)
