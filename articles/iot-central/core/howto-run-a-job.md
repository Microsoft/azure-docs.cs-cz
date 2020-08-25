---
title: Vytvoření a spuštění úloh v aplikaci Azure IoT Central | Microsoft Docs
description: Úlohy Azure IoT Central umožňují využít možnosti správy hromadných zařízení, jako je například aktualizace vlastností nebo spuštění příkazu.
ms.service: iot-central
services: iot-central
author: sarahhubbard
ms.author: sahubbar
ms.date: 06/08/2020
ms.topic: how-to
ms.openlocfilehash: 406881a9131aae35b91dcab248745bb426cecf0e
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/25/2020
ms.locfileid: "88797832"
---
# <a name="create-and-run-a-job-in-your-azure-iot-central-application"></a>Vytvoření a spuštění úlohy v aplikaci Azure IoT Central

Pomocí Microsoft Azure IoT Central můžete spravovat připojená zařízení ve velkém měřítku prostřednictvím úloh. Úlohy umožňují hromadné aktualizace vlastností zařízení a spouštění příkazů. V tomto článku se dozvíte, jak začít s používáním úloh ve vaší vlastní aplikaci.

## <a name="create-and-run-a-job"></a>Vytvoření a spuštění úlohy

V této části se dozvíte, jak vytvořit a spustit úlohu ve formě nastavení mezní hodnoty světla pro skupinu zařízení logistické brány.

1. V levém podokně vyberte **úlohy**.

2. Vyberte **+ Nový**.

   ![Snímek obrazovky zobrazující výběry pro vytvoření úlohy](./media/howto-run-a-job/create-new-job.png)

3. Zadejte název a popis pro identifikaci úlohy, kterou vytváříte.

4. Vyberte cílovou skupinu zařízení, na kterou se má vaše úloha vztahovat. V části **Souhrn** můžete zjistit, kolik zařízení vaše konfigurace úlohy používá.

5. Jako typ úlohy, kterou chcete nakonfigurovat, vyberte **cloudová vlastnost**, **vlastnost**nebo **příkaz** . 

   Chcete-li nastavit konfiguraci úlohy **vlastností** , vyberte vlastnost a nastavte její novou hodnotu. Chcete-li nastavit konfiguraci úloh **příkazu** , vyberte příkaz, který chcete spustit. Úloha vlastnosti může nastavit více vlastností.

   ![Snímek obrazovky zobrazující výběry pro vytvoření úlohy vlastností s názvem prahová hodnota nastavené světla](./media/howto-run-a-job/configure-job.png)

6. Vyberte **Spustit** nebo **Uložit**. Úloha se teď zobrazí na stránce hlavní **úlohy** . Na této stránce můžete vidět aktuálně spuštěnou úlohu a historii všech dříve spuštěných nebo uložených úloh. Uloženou úlohu můžete kdykoli znovu otevřít, aby bylo možné pokračovat v její úpravě nebo spuštění.

   ![Snímek obrazovky, který zobrazuje název, stav a popis vytvořené úlohy.](./media/howto-run-a-job/view-job.png)

   > [!NOTE]
   > Můžete zobrazit 30 dní historie pro dříve spuštěné úlohy.

7. Vyberte uloženou úlohu a spusťte ji výběrem tlačítka **Spustit** . 

   Zobrazí se dialogové okno **Spustit vaši úlohu?** . Potvrďte výběrem tlačítka **Spustit úlohu** . 

   ![Snímek obrazovky dialogového okna s potvrzením, že chcete spustit úlohu.](./media/howto-run-a-job/run-job.png)

8. Úloha projde fázemi, které čekají, běží a jsou dokončeny. Podrobnosti provádění úlohy obsahují metriky výsledků, podrobnosti o době trvání a mřížku seznamu zařízení. 

   Z tohoto přehledu můžete také vybrat **protokol výsledků** a stáhnout soubor CSV s podrobnostmi o úloze, včetně zařízení a jejich stavových hodnot. Tyto informace mohou být užitečné při řešení potíží.

   ![Snímek obrazovky, který zobrazuje stav zařízení.](./media/howto-run-a-job/download-details.png)

## <a name="manage-jobs"></a>Správa úloh

Pokud chcete zastavit běžící úlohu, otevřete ji a vyberte **zastavit**. Stav úlohy se změní tak, aby odrážel, že se úloha zastavila. V části **Souhrn** se dozvíte, která zařízení byla dokončena, nebyla úspěšná nebo stále čekají.

![Snímek obrazovky, který zobrazuje běžící úlohu a tlačítko pro zastavení úlohy.](./media/howto-run-a-job/manage-job.png)

Když je úloha v zastaveném stavu, můžete vybrat **pokračovat** a pokračovat v běhu úlohy. Stav úlohy se změní, aby se projevilo, že úloha je nyní spuštěna. Oddíl **summary (souhrn** ) se stále aktualizuje s nejnovějším průběhem.

![Snímek obrazovky, který zobrazuje zastavenou úlohu a tlačítko pro pokračování úlohy.](./media/howto-run-a-job/stopped-job.png)

## <a name="copy-a-job"></a>Kopírování úlohy

Pokud chcete zkopírovat jednu ze stávajících úloh, vyberte ji na stránce **úlohy** a vyberte **Podrobnosti o úloze**. Zobrazí se stránka **Podrobnosti úlohy** . 

![Snímek obrazovky zobrazující stránku s podrobnostmi o úloze](./media/howto-run-a-job/job-details.png)

Vyberte **Kopírovat**.

![Snímek obrazovky zobrazující tlačítko pro kopírování](./media/howto-run-a-job/job-details-copy.png)

Otevře se kopie konfigurace úlohy, která se má upravit, a **kopie** se připojí k názvu úlohy. Novou úlohu můžete uložit nebo spustit.

![Snímek obrazovky, který zobrazuje kopii konfigurace úlohy.](./media/howto-run-a-job/copy-job.png)

## <a name="view-job-status"></a>Zobrazení stavu úlohy

Po vytvoření úlohy se sloupec **stav** aktualizuje o nejnovější stavovou zprávu pro úlohu. V následující tabulce jsou uvedeny možné hodnoty stavu úlohy:

| Zpráva o stavu       | Význam stavu                                          |
| -------------------- | ------------------------------------------------------- |
| Dokončeno            | Tato úloha se spustila na všech zařízeních.              |
| Neúspěšný               | Tato úloha se nezdařila a v zařízeních nebyla plně spuštěna.  |
| Čekající              | Tato úloha se na zařízeních ještě nezačala používat.         |
| Spuštěno              | Tato úloha v tuto chvíli běží na zařízeních.             |
| Zastaveno              | Uživatel tuto úlohu zastavil ručně.           |

Stavová zpráva je následován přehledem zařízení v úloze. Následující tabulka uvádí možné hodnoty stavu zařízení:

| Zpráva o stavu       | Význam stavu                                                     |
| -------------------- | ------------------------------------------------------------------ |
| Úspěšný            | Počet zařízení, na kterých byla úloha úspěšně spuštěna.       |
| Neúspěšný               | Počet zařízení, na kterých se úloha nedokázala spustit.       |

Pokud chcete zobrazit stav úlohy a všechna postižená zařízení, otevřete úlohu. Vedle každého názvu zařízení se zobrazí jedna z následujících stavových zpráv:

| Zpráva o stavu       | Význam stavu                                                                |
| -------------------- | ----------------------------------------------------------------------------- |
| Dokončeno            | Úloha byla spuštěna na tomto zařízení.                                     |
| Neúspěšný               | Úlohu se nepovedlo spustit na tomto zařízení. V této chybové zprávě se zobrazí další informace.  |
| Čekající              | Tato úloha na tomto zařízení ještě neběží.                                   |

Pokud chcete stáhnout soubor CSV, který obsahuje podrobnosti úlohy, a seznam zařízení a jejich hodnoty stavu, vyberte **Stáhnout**.

## <a name="filter-the-device-list"></a>Filtrování seznamu zařízení

Seznam zařízení můžete filtrovat na stránce **podrobností úlohy** výběrem ikony filtru. Můžete filtrovat podle **ID zařízení** nebo pole **stav** .

:::image type="content" source="media/howto-run-a-job/filter.png" alt-text="Snímek obrazovky zobrazující výběry pro filtrování seznamu zařízení.":::

## <a name="customize-columns-in-the-device-list"></a>Přizpůsobení sloupců v seznamu zařízení

Výběrem ikony možnosti sloupce můžete vybrat další sloupce, které se mají zobrazit v seznamu zařízení.

:::image type="content" source="media/howto-run-a-job/column-options.png" alt-text="Snímek obrazovky zobrazující ikonu možností sloupce":::

Dialogové okno umožňuje vybrat sloupce, které se mají zobrazit v seznamu zařízení. Vyberte sloupce, které chcete zobrazit, vyberte šipku vpravo a pak vyberte **OK**. Chcete-li vybrat všechny dostupné sloupce, zaškrtněte políčko **Vybrat vše**.

:::image type="content" source="media/howto-run-a-job/column-picker-popup.png" alt-text="Snímek obrazovky, který zobrazuje dialogové okno pro výběr sloupců, které se mají zobrazit":::

Vybrané sloupce se zobrazí v seznamu zařízení.

:::image type="content" source="media/howto-run-a-job/column-picker-column-selected.png" alt-text="Snímek obrazovky, který zobrazuje vybrané sloupce v seznamu zařízení.":::

Vybrané sloupce se chovají napříč uživatelskou relací nebo mezi uživateli, kteří mají přístup k aplikaci.

## <a name="rerun-jobs"></a>Znovu spustit úlohy

Můžete znovu spustit úlohu, která má neúspěšná zařízení. Vyberte **znovu spustit při selhání**.

:::image type="content" source="media/howto-run-a-job/rerun.png" alt-text="Snímek obrazovky, který zobrazuje tlačítko pro spuštění úlohy na nezdařených zařízeních.":::

Zadejte název a popis úlohy a pak vyberte **znovu spustit úlohu**. Odešle se nová úloha, která bude opakovat akci u neúspěšných zařízení.

:::image type="content" source="media/howto-run-a-job/rerun-failed.png" alt-text="Snímek obrazovky, který zobrazuje dialogové okno pro reprovozování neúspěšných zařízení.":::

> [!NOTE]
> Z aplikace IoT Central Azure nemůžete současně spustit více než pět úloh.
>
> Po dokončení úlohy a odstranění zařízení, které je v seznamu zařízení úlohy, se položka zařízení zobrazí jako Odstraněná v názvu zařízení. Odkaz Podrobnosti není pro odstraněné zařízení k dispozici.

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili vytvářet úlohy v aplikaci Azure IoT Central, tady je několik dalších kroků:

- [správu zařízení](howto-manage-devices.md)
- [Verze šablony zařízení](howto-version-device-template.md)
