---
title: Vytváření a spouštění úloh v aplikaci Azure IoT Central | Dokumentace Microsoftu
description: Azure IoT Central úlohy umožňují hromadně možnosti správy zařízení, jako je například aktualizace vlastností zařízení, nastavení nebo spuštění příkazu.
ms.service: iot-central
services: iot-central
author: sarahhubbard
ms.author: sahubbar
ms.date: 03/18/2019
ms.topic: conceptual
manager: peterpr
ms.openlocfilehash: ec7033719316bb186408ea78f6dabac43c383491
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60519237"
---
# <a name="create-and-run-a-job-in-your-azure-iot-central-application"></a>Vytvoření a spuštění úlohy v aplikaci Azure IoT Central

Microsoft Azure IoT Central můžete použít ke správě připojených zařízení ve velkém měřítku pomocí úlohy. Úlohy umožňují hromadné aktualizace vlastností zařízení, nastavení a příkazy. Tento článek vás provede a začněte využívat úlohy ve své aplikaci.

## <a name="create-and-run-a-job"></a>Vytvoření a spuštění úlohy

V této části se dozvíte, jak vytvořit a spustit úlohu. To ukazuje, jak zvýšit rychlost ventilátor pro více chladicí automaty.

1. V navigačním podokně přejděte do úlohy.

1. Vyberte **+ nová** k vytvoření nové úlohy.

    ![Vytvoření nové úlohy](./media/howto-run-a-job/createnewjob.png)

1. Zadejte název a popis pro identifikaci úlohy, kterou vytváříte.

1. Vyberte sadu zařízení chcete použít pro vaši úlohu. Po výběru zařízení nastavení, se zobrazí na pravé straně naplnit zařízení v sadě zařízení. Pokud vyberete sada přerušeno zařízení, zobrazit žádná zařízení a zobrazí se zpráva, že vaše zařízení sada je přerušeno.

1. Potom vyberte typ úlohy k definování (nastavení, vlastnost nebo příkaz). Vyberte **+** vedle typu úlohy vybraná a přidat své operace.

    ![Konfigurace úlohy](./media/howto-run-a-job/configurejob.png)

1. Na pravé straně zvolte zařízení, které chcete spustit úlohu. Zaškrtnutím políčka horní jsou vybrány všechna zařízení v sadě celého zařízení. Zaškrtnutím políčka vedle **název**, jsou vybrány všechna zařízení na aktuální stránce.

1. Po výběru zařízení, zvolte **spustit** nebo **Uložit**. Úloha se zobrazí na vaší hlavní **úlohy** stránky. V tomto zobrazení můžete zobrazit aktuálně spuštěné úlohy a historii některého dříve spouštění úloh. Spuštěná úloha se vždy zobrazí v horní části seznamu. Uložené úlohy lze otevřít znovu kdykoli chcete dál upravovat nebo ke spuštění.

    ![Zobrazit úlohu](./media/howto-run-a-job/viewjob.png)

    > [!NOTE]
    > Zobrazení historie dřív spuštěných úloh po dobu až 30 dnů.

1. Získat přehled o úlohy, vyberte úlohu chcete-li zobrazit ze seznamu. Tento přehled obsahuje podrobnosti o úloze, zařízení a hodnoty stavu zařízení. Z tohoto přehledu, můžete také vybrat **stáhnout podrobnosti o úloze** stáhnout soubor CSV si podrobnosti o úloze, včetně zařízení a jejich hodnoty stavu. Tyto informace mohou být užitečné při řešení potíží.

    ![Zobrazení stavu zařízení](./media/howto-run-a-job/downloaddetails.png)

### <a name="stop-a-running-job"></a>Zastavit spuštěné úlohy

Zastavit spuštěné úlohy, vyberte ho a zvolte **Zastavit** na panelu. Stav úlohy se změní podle úlohy zastavena.

   ![Zastavit úlohu](./media/howto-run-a-job/stopjob.png)

### <a name="run-a-stopped-job"></a>Spustit úlohu zastaveno

Pokud chcete spustit úlohu, která je nyní zastavena, vyberte zastavenou úlohu. Zvolte **spustit** na panelu. Změny stavu úlohy tak, aby odrážely úlohy je nyní spuštěna znovu.

   ![Obnovit úlohy](./media/howto-run-a-job/resumejob.png)

## <a name="copy-a-job"></a>Úloha kopírování

Pokud chcete zkopírovat existující úlohy, které jste vytvořili, vyberte ho ze stránky s hlavní úlohy a vyberte **kopírování**. Otevře novou kopii konfigurace úlohy můžete upravit. Můžete uložit nebo spustit novou úlohu. Pokud změny byly provedeny na sadu vybraných zařízení, budete se projeví v tento zkopírovaný úlohy můžete upravit.

   ![Úloha kopírování](./media/howto-run-a-job/copyjob.png)

## <a name="view-the-job-status"></a>Zobrazení stavu úlohy

Po vytvoření úlohy **stav** aktualizace sloupců s nejnovější stavová zpráva úlohy. Následující tabulka uvádí stav možné hodnoty:

| Zpráva o stavu       | Stav význam                                          |
| -------------------- | ------------------------------------------------------- |
| Dokončeno            | Tato úloha byla provedena na všech zařízeních.              |
| Selhalo               | Tato úloha má se nezdařilo a není plně proveden v zařízení.  |
| Čekající na vyřízení              | Tato úloha ještě nezačala provádění na zařízení.         |
| Spuštěno              | Tato úloha právě probíhá na zařízeních.             |
| Zastaveno              | Tato úloha byla ručně zastavena uživatelem.           |

Ve zprávě o stavu Následuje přehled zařízení v rámci úlohy. V následující tabulce jsou uvedeny hodnoty stavu zařízení:

| Zpráva o stavu       | Stav význam                                                     |
| -------------------- | ------------------------------------------------------------------ |
| Úspěch            | Počet zařízení, která se úloha úspěšně provedena na.       |
| Selhalo               | Počet zařízení, která úloha se nezdařila se promítnou u.       |

### <a name="view-the-device-status"></a>Zobrazení stavu zařízení

Chcete-li zobrazit stav úlohy a všech příslušných zařízení, vyberte úlohu. Chcete-li stáhnout soubor CSV obsahující podrobnosti o úlohy, včetně seznamu zařízení a jejich hodnoty stavu vyberte **stáhnout podrobnosti o úloze**. Vedle názvu každé zařízení se zobrazí jedna z následujících zpráv o stavu:

| Zpráva o stavu       | Stav význam                                                                |
| -------------------- | ----------------------------------------------------------------------------- |
| Dokončeno            | Úloha byla provedena na tomto zařízení.                                     |
| Selhalo               | Úlohu se nepodařilo spustit na tomto zařízení. Další informace se zobrazí chybová zpráva.  |
| Čekající na vyřízení              | Úloha ještě neprovedlo na tomto zařízení.                                   |

> [!NOTE]
> Pokud zařízení byla odstraněna, nelze vybrat zařízení a zobrazí se odstranil s ID zařízení.

## <a name="next-steps"></a>Další postup

Teď, když jsme zjistili, jak vytvářet úlohy v aplikaci Azure IoT Central, tady jsou některé další kroky:

- [Použití sad zařízení](howto-use-device-sets.md)
- [Správa zařízení](howto-manage-devices.md)
- [Verze šablony zařízení](howto-version-devicetemplate.md)
