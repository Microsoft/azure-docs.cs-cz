---
title: Vytvoření a spuštění úloh v aplikaci Azure IoT Central | Microsoft Docs
description: Úlohy Azure IoT Central umožňují využít možnosti správy hromadných zařízení, jako je například aktualizace vlastností nebo provádění příkazu.
ms.service: iot-central
services: iot-central
author: sarahhubbard
ms.author: sahubbar
ms.date: 06/08/2020
ms.topic: how-to
ms.openlocfilehash: dec9abc38bc0354ef3d22994a7988bfb006f5769
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "84609690"
---
# <a name="create-and-run-a-job-in-your-azure-iot-central-application"></a>Vytvoření a spuštění úlohy v aplikaci Azure IoT Central

Pomocí Microsoft Azure IoT Central můžete spravovat připojená zařízení ve velkém měřítku pomocí úloh. Úlohy umožňují hromadné aktualizace vlastností zařízení a spouštění příkazů. V tomto článku se dozvíte, jak začít používat úlohy ve vaší vlastní aplikaci.

## <a name="create-and-run-a-job"></a>Vytvoření a spuštění úlohy

V této části se dozvíte, jak vytvořit a spustit úlohu. Ukazuje, jak nastavit prahovou hodnotu pro skupinu zařízení s logistickou bránou.

1. V levém podokně přejděte na **úlohy** .

2. Vyberte **+ Nová** a vytvořte novou úlohu:

    ![Vytvořit novou úlohu](./media/howto-run-a-job/create-new-job.png)

3. Zadejte název a popis pro identifikaci úlohy, kterou vytváříte.

4. Vyberte cílovou skupinu zařízení, na kterou se má vaše úloha vztahovat. V části **Souhrn** můžete zjistit, kolik zařízení vaše konfigurace úlohy používá.

5. Potom jako typ úlohy, kterou chcete nakonfigurovat, vyberte buď **vlastnost cloudu**, **vlastnost**, nebo **příkaz** . Chcete-li nastavit konfiguraci úlohy **vlastností** , vyberte vlastnost a nastavte její novou hodnotu. Pokud chcete nastavit **příkaz**, vyberte příkaz, který se má spustit. Úloha vlastnosti může nastavit více vlastností:

    ![Konfigurovat úlohu](./media/howto-run-a-job/configure-job.png)

6. Po vytvoření úlohy klikněte na tlačítko **Spustit** nebo **Uložit**. Úloha se teď zobrazí na stránce hlavní **úlohy** . Na této stránce můžete vidět aktuálně spuštěnou úlohu a historii všech dříve spuštěných nebo uložených úloh. Uloženou úlohu můžete kdykoli znovu otevřít, aby ji bylo možné dál upravovat nebo spustit:

    ![Zobrazit úlohu](./media/howto-run-a-job/view-job.png)

    > [!NOTE]
    > Můžete zobrazit 30 dní historie pro dříve spuštěné úlohy.

7. Pokud chcete získat přehled o vaší úloze, vyberte úlohu, kterou chcete zobrazit v seznamu. Tento přehled obsahuje podrobnosti úlohy, zařízení a stavové hodnoty zařízení. Z tohoto přehledu můžete také vybrat **Stáhnout podrobnosti o úloze** a stáhnout soubor CSV s podrobnostmi o úloze, včetně zařízení a jejich stavových hodnot. Tyto informace mohou být užitečné při řešení potíží:

    ![Zobrazení stavu zařízení](./media/howto-run-a-job/download-details.png)

## <a name="manage-jobs"></a>Správa úloh

Pokud chcete zastavit jednu ze spuštěných úloh, otevřete ji a vyberte **zastavit**. Stav úlohy se změní, aby odrážela zastavení úlohy. V části **Souhrn** se zobrazuje, která zařízení jsou dokončená, neúspěšná nebo pořád čekají na vyřízení.

Pokud chcete spustit úlohu, která je aktuálně zastavená, vyberte ji a pak vyberte **Spustit**. Změna stavu úlohy se projeví v závislosti na tom, že úloha je nyní spuštěna. Oddíl **summary (souhrn** ) se stále aktualizuje s nejnovějším průběhem.

![Spravovat úlohu](./media/howto-run-a-job/manage-job.png)

## <a name="copy-a-job"></a>Kopírování úlohy

Chcete-li zkopírovat jednu ze stávajících úloh, vyberte ji na stránce **úlohy** a vyberte možnost **Kopírovat**. Otevře se kopie konfigurace úlohy, která se má upravit, a **kopie** se připojí k názvu úlohy. Novou úlohu můžete uložit nebo spustit:

![Kopírovat úlohu](./media/howto-run-a-job/copy-job.png)

## <a name="view-job-status"></a>Zobrazení stavu úlohy

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

### <a name="view-the-device-status-values"></a>Zobrazit hodnoty stavu zařízení

Pokud chcete zobrazit stav úlohy a všechna postižená zařízení, otevřete úlohu. Vedle každého názvu zařízení se zobrazí jedna z následujících stavových zpráv:

| Zpráva o stavu       | Význam stavu                                                                |
| -------------------- | ----------------------------------------------------------------------------- |
| Dokončeno            | Úloha se spustila na tomto zařízení.                                     |
| Failed               | V tomto zařízení se nepovedlo spustit úlohu. V této chybové zprávě se zobrazí další informace.  |
| Čekající na vyřízení              | Tato úloha na tomto zařízení ještě nebyla spuštěna.                                   |

Pokud chcete stáhnout soubor CSV, který obsahuje podrobnosti úlohy, a seznam zařízení a jejich hodnoty stavu, vyberte **Stáhnout**.

### <a name="filter-the-list-of-devices"></a>Filtrování seznamu zařízení

Seznam zařízení můžete filtrovat na stránce podrobností úlohy výběrem ikony filtru. Můžete filtrovat podle **ID zařízení** nebo **stavových** polí:

:::image type="content" source="media/howto-run-a-job/filter.png" alt-text="Filtrování seznamu zařízení":::

### <a name="customize-columns-in-the-device-list"></a>Přizpůsobení sloupců v seznamu zařízení

Můžete zvolit další sloupce, které se mají zobrazit v seznamu zařízení, a to tak, že vyberete ikonu Možnosti sloupce:

:::image type="content" source="media/howto-run-a-job/column-options.png" alt-text="Možnosti sloupců":::

Zobrazí se dialogové okno, které vám umožní vybrat sloupce, které se mají zobrazit v seznamu zařízení. Vyberte sloupce, které chcete zobrazit, vyberte ikonu šipky vpravo a vyberte **OK**. Chcete-li vybrat všechny dostupné sloupce, zaškrtněte políčko **Vybrat vše**:

:::image type="content" source="media/howto-run-a-job/column-picker-popup.png" alt-text="Dialog pro výběr sloupců":::

Vybrané sloupce se zobrazí v seznamu zařízení:

:::image type="content" source="media/howto-run-a-job/column-picker-column-selected.png" alt-text="Vybrat sloupce":::

Vybrané sloupce se chovají napříč uživatelskou relací nebo mezi uživateli, kteří mají přístup k aplikaci.

## <a name="rerun-jobs"></a>Znovu spustit úlohy

Můžete znovu spustit úlohu, která má neúspěšná zařízení. Vyberte **znovu spustit**:

:::image type="content" source="media/howto-run-a-job/rerun.png" alt-text="Znovu spustit úlohu":::

Zadejte název a popis úlohy a pak vyberte **znovu spustit úlohu**. Odešle se nová úloha, která bude opakovat akci u neúspěšných zařízení:

:::image type="content" source="media/howto-run-a-job/rerun-failed.png" alt-text="Znovu spustit neúspěšná zařízení":::

> [!NOTE]
> Z aplikace IoT Central nemůžete současně spustit více než pět úloh.

> [!NOTE]
> Po dokončení úlohy a odstranění zařízení, které je v seznamu zařízení úlohy, se položka zařízení zobrazí jako Odstraněná v odkazu název zařízení a podrobnosti o zařízení není pro odstraněné zařízení k dispozici.

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili vytvářet úlohy v aplikaci Azure IoT Central, tady je několik dalších kroků:

- [správu zařízení](howto-manage-devices.md)
- [Verze šablony zařízení](howto-version-device-template.md)
