---
title: Vytvoření a spuštění úloh v aplikaci Azure IoT Central | Microsoft Docs
description: Úlohy Azure IoT Central umožňují využít možnosti správy hromadných zařízení, jako je například aktualizace vlastností nebo spuštění příkazu.
ms.service: iot-central
services: iot-central
author: philmea
ms.author: philmea
ms.date: 09/30/2020
ms.topic: how-to
ms.openlocfilehash: b8106c154a91d1e823a124a90f7571b7f52ae8cb
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2020
ms.locfileid: "94682119"
---
# <a name="create-and-run-a-job-in-your-azure-iot-central-application"></a>Vytvoření a spuštění úlohy v aplikaci Azure IoT Central

Pomocí služby Azure IoT Central můžete spravovat připojená zařízení ve velkém měřítku prostřednictvím úloh. Úlohy umožňují provádět hromadné aktualizace vlastností zařízení a cloudu a příkazy spustit. V tomto článku se dozvíte, jak začít s používáním úloh ve vaší vlastní aplikaci.

## <a name="create-and-run-a-job"></a>Vytvoření a spuštění úlohy

Následující příklad ukazuje, jak vytvořit a spustit úlohu pro nastavení mezní hodnoty světla pro skupinu zařízení logistické brány. Průvodce úlohou slouží k vytváření a spouštění úloh. Můžete uložit úlohu, která se má spustit později.

1. V levém podokně vyberte **úlohy**.

1. Vyberte **+ Nová úloha**.

1. Na stránce **Konfigurovat svoji úlohu** zadejte název a popis pro identifikaci úlohy, kterou vytváříte.

1. Vyberte cílovou skupinu zařízení, na kterou se má vaše úloha vztahovat. Můžete zjistit, kolik zařízení vaše konfigurace úlohy používá, podle výběru **skupiny zařízení** .

1. Jako **typ úlohy** vyberte **vlastnost cloudu**, **vlastnost** nebo **příkaz** :

    Chcete-li nakonfigurovat úlohu **vlastností** , vyberte vlastnost a nastavte její novou hodnotu. Chcete-li konfigurovat **příkazovou** úlohu, vyberte příkaz, který chcete spustit. Úloha vlastnosti může nastavit více vlastností.

    :::image type="content" source="media/howto-run-a-job/configure-job.png" alt-text="Snímek obrazovky zobrazující výběry pro vytvoření úlohy vlastností s názvem prahová hodnota nastavené světla":::

    Vyberte **Uložit a ukončit** a přidejte úlohu do seznamu uložených úloh na stránce **úlohy** . Později se můžete vrátit k úloze ze seznamu uložených úloh.

1. Kliknutím na tlačítko **Další** přejdete na stránku **Možnosti doručení** . Stránka **Možnosti doručení** umožňuje nastavit možnosti doručení pro tuto úlohu: **dávky** a **zrušení prahové hodnoty**.

    Dávky umožňují rozdělit úlohy na velký počet zařízení. Úloha je rozdělena do několika dávek a každá dávka obsahuje podmnožinu zařízení. Dávky jsou zařazeny do fronty a spuštěny v pořadí.

    Prahová hodnota zrušení umožňuje automaticky zrušit úlohu, pokud počet chyb překročí nastavený limit. Prahová hodnota se může vztahovat na všechna zařízení v úloze nebo na jednotlivé dávky.

    :::image type="content" source="media/howto-run-a-job/job-wizard-delivery-options.png" alt-text="Snímek obrazovky se stránkou možností doručení Průvodce úlohou":::

1. Kliknutím na tlačítko **Další** přejdete na stránku **plánu** . Na stránce **plán** můžete povolit, aby plán spouštěl úlohu v budoucnu:

    Vyberte možnost opakování pro plán. Můžete nastavit úlohu, která se má spustit:

    * Jednorázová
    * denně
    * týdně

    Nastavte počáteční datum a čas pro naplánovanou úlohu. Datum a čas jsou specifické pro vaše časové pásmo, nikoli pro místní čas zařízení.

    Chcete-li ukončit opakovaný plán, vyberte:

    * **V tomto dni** nastavte koncové datum pro plán.
    * **Poté** nastavte počet pokusů o spuštění úlohy.

    Naplánované úlohy se vždycky spouštějí na zařízeních ve skupině zařízení, a to i v případě, že se členství ve skupině zařízení v průběhu času mění.

    :::image type="content" source="media/howto-run-a-job/job-wizard-schedule.png" alt-text="Snímek obrazovky se stránkou možností plánování Průvodce úlohou":::

1. Kliknutím na tlačítko **Další** přejděte na stránku **Revize** . Na stránce **Kontrola** se zobrazí podrobnosti o konfiguraci úlohy. Vyberte **plán** pro naplánování úlohy:

    :::image type="content" source="media/howto-run-a-job/job-wizard-schedule-review.png" alt-text="Snímek obrazovky s průvodcem plánovanými úlohami – stránka pro kontrolu":::

1. Na stránce Podrobnosti úlohy se zobrazují informace o plánovaných úlohách. Když se naplánovaná úloha spustí, zobrazí se seznam instancí úlohy. Naplánované spuštění úlohy je také součástí **posledních 30 dnů** seznamu úloh.

    Na této stránce můžete **naplánovat** úlohu nebo **Upravit** naplánovanou úlohu. Do naplánované úlohy se můžete vrátit ze seznamu naplánovaných úloh.

    :::image type="content" source="media/howto-run-a-job/job-schedule-details.png" alt-text="Snímek stránky s podrobnostmi naplánované úlohy":::

1. V Průvodci úlohou se můžete rozhodnout, že neplánujete úlohu, a okamžitě ji spustit. Následující snímek obrazovky ukazuje úlohu bez plánu, který je připravený ke spuštění hned. Výběrem **Spustit** spusťte úlohu:

    :::image type="content" source="media/howto-run-a-job/job-wizard-schedule-immediate.png" alt-text="Snímek obrazovky s průvodcem úlohy – Stránka s přehledem":::

1. Úloha prochází přes fáze *čekání*, *spuštění* a *dokončení* . Podrobnosti provádění úlohy obsahují metriky výsledků, podrobnosti o době trvání a mřížku seznamu zařízení.

    Po dokončení úlohy můžete vybrat **protokol výsledků** a stáhnout soubor CSV s podrobnostmi o úloze, včetně zařízení a jejich stavových hodnot. Tyto informace mohou být užitečné při řešení potíží.

    :::image type="content" source="media/howto-run-a-job/download-details.png" alt-text="Snímek obrazovky, který zobrazuje stav zařízení":::

1. Úloha se nyní zobrazí v seznamu **posledních 30 dnů** na stránce **úlohy** . Tato stránka zobrazuje aktuálně spuštěné úlohy a historii všech dříve spuštěných nebo uložených úloh.

    > [!NOTE]
    > Můžete zobrazit 30 dní historie pro dříve spuštěné úlohy.

## <a name="manage-jobs"></a>Správa úloh

Pokud chcete zastavit běžící úlohu, otevřete ji a vyberte **zastavit**. Stav úlohy se změní tak, aby odrážel, že se úloha zastavila. V části **Souhrn** se dozvíte, která zařízení byla dokončena, nebyla úspěšná nebo stále čekají.

:::image type="content" source="media/howto-run-a-job/manage-job.png" alt-text="Snímek obrazovky zobrazující běžící úlohu a tlačítko pro zastavení úlohy":::

Když je úloha v zastaveném stavu, můžete vybrat **pokračovat** a pokračovat v běhu úlohy. Stav úlohy se změní, aby se projevilo, že úloha je nyní spuštěna. Oddíl **summary (souhrn** ) se stále aktualizuje s nejnovějším průběhem.

:::image type="content" source="media/howto-run-a-job/stopped-job.png" alt-text="Snímek obrazovky, který zobrazuje zastavenou úlohu a tlačítko pro pokračování úlohy":::

## <a name="copy-a-job"></a>Kopírování úlohy

Chcete-li zkopírovat existující úlohu, vyberte spuštěnou úlohu. Na stránce výsledky úlohy nebo na stránce Podrobnosti o úlohách vyberte **Kopírovat** .

:::image type="content" source="media/howto-run-a-job/job-details-copy.png" alt-text="Snímek obrazovky, který zobrazuje tlačítko pro kopírování":::

Otevře se kopie konfigurace úlohy, která se má upravit, a **kopie** se připojí k názvu úlohy.

## <a name="view-job-status"></a>Zobrazení stavu úlohy

Po vytvoření úlohy se sloupec **stav** aktualizuje s nejnovější zprávou o stavu úlohy. V následující tabulce jsou uvedeny možné hodnoty *stavu úlohy* :

| Zpráva o stavu       | Význam stavu                                          |
| -------------------- | ------------------------------------------------------- |
| Dokončeno            | Tato úloha se spustila na všech zařízeních.              |
| Neúspěšný               | Tato úloha se nezdařila a na zařízeních se nedokončilo úplné spuštění.  |
| Čekající              | Tato úloha se na zařízeních ještě nezačala používat.         |
| Spuštěný              | Tato úloha v tuto chvíli běží na zařízeních.             |
| Zastaveno              | Uživatel tuto úlohu zastavil ručně.           |
| Zrušeno             | Tato úloha byla zrušena, protože byla překročena prahová hodnota nastavená na stránce **Možnosti doručení** . |

Stavová zpráva je následován přehledem zařízení v úloze. Následující tabulka uvádí možné hodnoty *stavu zařízení* :

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

Pokud chcete stáhnout soubor CSV, který obsahuje podrobnosti úlohy, a seznam zařízení a jejich hodnoty stavu, vyberte **protokol výsledků**.

## <a name="filter-the-device-list"></a>Filtrování seznamu zařízení

Seznam zařízení můžete filtrovat na stránce **podrobností úlohy** výběrem ikony filtru. Můžete filtrovat podle **ID zařízení** nebo pole **stav** :

:::image type="content" source="media/howto-run-a-job/filter.png" alt-text="Snímek obrazovky zobrazující výběry pro filtrování seznamu zařízení.":::

## <a name="customize-columns-in-the-device-list"></a>Přizpůsobení sloupců v seznamu zařízení

Do seznamu zařízení můžete přidat sloupce tak, že vyberete ikonu Možnosti sloupce:

:::image type="content" source="media/howto-run-a-job/column-options.png" alt-text="Snímek obrazovky zobrazující ikonu možností sloupce":::

Pomocí dialogového okna **Možnosti sloupce** můžete zvolit sloupce seznamu zařízení. Vyberte sloupce, které chcete zobrazit, vyberte šipku vpravo a pak vyberte **OK**. Chcete-li vybrat všechny dostupné sloupce, zvolte **možnost Vybrat vše**. Vybrané sloupce se zobrazí v seznamu zařízení.

Vybrané sloupce zůstanou v relaci uživatele nebo mezi uživateli, které mají přístup k aplikaci.

## <a name="rerun-jobs"></a>Znovu spustit úlohy

Můžete znovu spustit úlohu, která má neúspěšná zařízení. Vyberte **znovu spustit při selhání**:

:::image type="content" source="media/howto-run-a-job/rerun.png" alt-text="Snímek obrazovky, který zobrazuje tlačítko pro spuštění úlohy na nezdařených zařízeních.":::

Zadejte název a popis úlohy a pak vyberte **znovu spustit úlohu**. Odešle se nová úloha, která bude opakovat akci u neúspěšných zařízení.

> [!NOTE]
> Z aplikace IoT Central Azure nemůžete současně spustit více než pět úloh.
>
> Po dokončení úlohy a odstranění zařízení, které je v seznamu zařízení úlohy, se položka zařízení zobrazí jako Odstraněná v názvu zařízení. Odkaz Podrobnosti není pro odstraněné zařízení k dispozici.

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili vytvářet úlohy v aplikaci Azure IoT Central, tady je několik dalších kroků:

- [správu zařízení](howto-manage-devices.md)
- [Verze šablony zařízení](howto-version-device-template.md)
