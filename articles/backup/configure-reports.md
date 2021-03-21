---
title: Konfigurace sestav Azure Backup
description: Konfigurace a zobrazení sestav pro Azure Backup pomocí Log Analytics a sešitů Azure
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: e9f3d9dfa33e71d827a338258001f2b52af62b06
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102509355"
---
# <a name="configure-azure-backup-reports"></a>Konfigurace sestav Azure Backup

Běžným požadavkem pro správce zálohování je získat přehled o zálohách na základě dat, která jsou delší dobu. Případy použití takového řešení zahrnují:

- Přidělování a prognózování spotřebovaného cloudového úložiště.
- Auditování záloh a obnovení.
- Identifikujte klíčové trendy v různých úrovních členitosti.

Dnes Azure Backup poskytuje řešení pro vytváření sestav, které používá [protokoly Azure monitor](../azure-monitor/logs/log-analytics-tutorial.md) a [sešity Azure](../azure-monitor/visualize/workbooks-overview.md). Tyto prostředky vám pomůžou získat přehled o vašich zálohách napříč celou záloze. Tento článek vysvětluje, jak konfigurovat a zobrazovat sestavy Azure Backup.

## <a name="supported-scenarios"></a>Podporované scénáře

- Sestavy zálohování se podporují pro virtuální počítače Azure, SQL ve virtuálních počítačích Azure, SAP HANA ve virtuálních počítačích Azure, Microsoft Azure Recovery Services (MARS) agent, Microsoft Azure Backup Server (MABS) a System Center Data Protection Manager (DPM). Pro zálohování sdílené složky Azure se zobrazí data pro záznamy vytvořené od 1. června 2020.
- V případě zálohování sdílené složky Azure se data v chráněných instancích zobrazí pro záznamy vytvořené po 1. únoru 2021 (výchozí hodnota je u starších záznamů nulová).
- Pro úlohy DPM jsou sestavy zálohování podporované pro DPM verze 5.1.363.0 a novější a verze agenta 2.0.9127.0 a vyšší.
- Pro úlohy MABS jsou sestavy zálohování podporované pro MABS verze 13.0.415.0 a vyšší a verze agenta 2.0.9170.0 a vyšší.
- Sestavy zálohování můžete zobrazit ve všech zálohovaných položkách, trezorech, předplatných a oblastech, pokud jsou data odesílána do Log Analyticsho pracovního prostoru, ke kterému má uživatel přístup. Chcete-li zobrazit sestavy pro sadu trezorů, stačí mít přístup čtenář k pracovnímu prostoru Log Analytics, do kterého trezory odesílají svá data. Nemusíte mít přístup k jednotlivým trezorům.
- Pokud jste uživatelem [Azure Lighthouse](../lighthouse/index.yml) , který má delegovaný přístup k předplatným vašich zákazníků, můžete pomocí těchto sestav s Azure Lighthouse zobrazit sestavy pro všechny klienty.
- V současné době je možné data zobrazit v sestavách zálohování v rámci maximálního počtu 100 Log Analytics pracovních prostorů (mezi klienty).
- Data pro úlohy zálohování protokolů aktuálně nejsou v sestavách zobrazená.

[!INCLUDE [backup-center.md](../../includes/backup-center.md)]

## <a name="get-started"></a>Začínáme

Chcete-li začít používat sestavy, postupujte podle těchto kroků.

### <a name="1-create-a-log-analytics-workspace-or-use-an-existing-one"></a>1. Vytvořte pracovní prostor Log Analytics nebo použijte existující.

Nastavte jeden nebo více pracovních prostorů Log Analytics pro uložení dat generování sestav zálohování. Umístění a předplatné, kde se dá tento Log Analytics pracovní prostor vytvořit, nezávisí na umístění a předplatném, kde existují vaše trezory.

Pokud chcete nastavit pracovní prostor Log Analytics, přečtěte si téma [Vytvoření pracovního prostoru Log Analytics v Azure Portal](../azure-monitor/logs/quick-create-workspace.md).

Ve výchozím nastavení jsou data v pracovním prostoru Log Analytics uchována po dobu 30 dnů. Chcete-li zobrazit data pro časový horizont delší dobu, změňte dobu uchování Log Analytics pracovního prostoru. Pokud chcete změnit dobu uchovávání, přečtěte si téma [Správa využití a nákladů pomocí protokolů Azure monitor](../azure-monitor/logs/manage-cost-storage.md).

### <a name="2-configure-diagnostics-settings-for-your-vaults"></a>2. Konfigurace nastavení diagnostiky pro vaše trezory

Azure Resource Manager prostředky, jako jsou například trezory Recovery Services, zaznamenávají informace o plánovaných operacích a uživatelem aktivované operace jako diagnostická data.

V části monitorování vašeho trezoru Recovery Services vyberte **nastavení diagnostiky** a zadejte cíl pro diagnostická data trezoru Recovery Services. Další informace o použití diagnostických událostí najdete v tématu [použití nastavení diagnostiky pro trezory Recovery Services](./backup-azure-diagnostic-events.md).

![Podokno nastavení diagnostiky](./media/backup-azure-configure-backup-reports/resource-specific-blade.png)

Azure Backup také poskytuje integrovanou Azure Policy definici, která automatizuje konfiguraci nastavení diagnostiky pro všechny trezory v daném oboru. Informace o tom, jak používat tuto zásadu, najdete v tématu [Konfigurace nastavení diagnostiky trezoru ve velkém měřítku](./azure-policy-configure-diagnostics.md).

> [!NOTE]
> Po dokončení konfigurace diagnostiky může trvat až 24 hodin, než se počáteční nabízení dat dokončí. Po zahájení toku dat do Log Analytics pracovního prostoru se data v sestavách nemusí okamžitě zobrazovat, protože v sestavách není zobrazená data pro aktuální částečný den. Další informace najdete v tématu [konvence používané v sestavách zálohování](#conventions-used-in-backup-reports). Doporučujeme, abyste začali zobrazovat sestavy dvou dnů po konfiguraci trezorů, aby odesílaly data do Log Analytics.

#### <a name="3-view-reports-in-the-azure-portal"></a>3. zobrazení sestav v Azure Portal

Po nakonfigurování trezorů pro posílání dat Log Analytics můžete zobrazit sestavy zálohování tak, že v podokně trezoru vyberete možnost **sestavy zálohování**.

![Řídicí panel trezoru](./media/backup-azure-configure-backup-reports/vault-dashboard.png)

Kliknutím na tento odkaz otevřete sešit zálohované sestavy.

> [!NOTE]
>
> - V současné době počáteční načtení sestavy může trvat až 1 minutu.
> - Trezor Recovery Services je pouze vstupním bodem pro sestavy zálohování. Po otevření sešitu sestavy zálohování z podokna trezoru vyberte příslušnou sadu Log Analytics pracovních prostorů, abyste viděli data agregovaná napříč všemi vašimi trezory.

Sestava obsahuje různé karty:

##### <a name="summary"></a>Souhrn

Tato karta vám umožní získat podrobný přehled o vaší nemovitosti k zálohování. Můžete získat rychlý přehled o celkovém počtu zálohovaných položek, celkovém využitém cloudovém úložišti, počtu chráněných instancí a četnosti úspěšnosti úlohy na jeden typ pracovního vytížení. Podrobnější informace o konkrétním typu artefaktu zálohování získáte, když přejdete na příslušné karty.

   ![Karta se shrnutím](./media/backup-azure-configure-backup-reports/summary.png)

##### <a name="backup-items"></a>Zálohované položky

Tato karta slouží k zobrazení informací a trendů v cloudovém úložišti spotřebovaného na úrovni záložních položek. Pokud například použijete SQL v zálohování virtuálního počítače Azure, můžete zobrazit cloudové úložiště spotřebované pro každou zálohovanou databázi SQL. Můžete se také rozhodnout, že se budou zobrazovat data pro záložní položky určitého stavu ochrany. Například když vyberete dlaždici **zastavená ochrana** v horní části karty, vyfiltruje všechny widgety pod tím, aby zobrazovaly data pouze pro zálohované položky ve stavu ochrana zastaveno.

   ![Karta zálohované položky](./media/backup-azure-configure-backup-reports/backup-items.png)

##### <a name="usage"></a>Využití

Pomocí této karty můžete zobrazit klíčové parametry fakturace pro vaše zálohy. Informace zobrazené na této kartě jsou na úrovni fakturačních entit (chráněných kontejnerů). Například pokud se server DPM zálohuje do Azure, můžete zobrazit trend chráněných instancí a cloudového úložiště spotřebovaného pro server DPM. Podobně platí, že pokud použijete SQL ve Azure Backup nebo SAP HANA v Azure Backup, tato karta poskytuje informace související s využitím na úrovni virtuálního počítače, ve kterém jsou tyto databáze obsažené.

   ![Karta použití](./media/backup-azure-configure-backup-reports/usage.png)

> [!NOTE]
> V případě úloh DPM se uživatelům může v porovnání s hodnotou agregovaného využití zobrazit lehký rozdíl (z pořadí 20 MB na server DPM) mezi hodnotami využití zobrazenými v sestavách, jak je znázorněno na kartě **přehled** Recovery Servicesového trezoru. Tento rozdíl je vydaný faktem, že každý server DPM, který je zaregistrován pro zálohování, má přidružený zdroj dat metadata, který není povrchový jako artefakt pro vytváření sestav.

##### <a name="jobs"></a>Úlohy

Tato karta slouží k zobrazení dlouhotrvajících trendů v úlohách, jako je počet neúspěšných úloh za den a nejvyšší příčiny selhání úlohy. Tyto informace můžete zobrazit jak na agregované úrovni, tak na úrovni záložních položek. Vyberte konkrétní položku zálohy v mřížce pro zobrazení podrobných informací o každé úloze, která byla aktivována u dané zálohované položky ve vybraném časovém rozsahu.

   ![Karta úlohy](./media/backup-azure-configure-backup-reports/jobs.png)

##### <a name="policies"></a>Zásady

Tato karta slouží k zobrazení informací o všech aktivních zásadách, například o počtu přidružených položek a celkovém cloudovém úložišti spotřebovanému položkami zálohovanými v rámci dané zásady. Vyberte konkrétní zásadu pro zobrazení informací o všech přidružených zálohovaných položkách.

   ![Karta Zásady](./media/backup-azure-configure-backup-reports/policies.png)

##### <a name="optimize"></a>Optimalizace

Tato karta slouží k získání přehledu o potenciálních příležitostech pro optimalizaci nákladů pro vaše zálohy. Tady jsou uvedené scénáře, pro které karta optimalizace aktuálně poskytuje přehledy:

###### <a name="inactive-resources"></a>Neaktivní prostředky

Pomocí tohoto zobrazení můžete identifikovat zálohované položky, které po delší dobu neobsahovaly úspěšnou zálohu. To může znamenat, že základní počítač, který se zálohuje, už neexistuje (a výsledkem je neúspěšné zálohování), nebo je nějaký problém s počítačem, který brání spolehlivému zálohování.

Chcete-li zobrazit neaktivní prostředky, přejděte na kartu **optimalizace** a vyberte dlaždici **neaktivní prostředky** . Kliknutím na tuto dlaždici zobrazíte mřížku, která obsahuje podrobnosti o všech neaktivních prostředcích, které existují ve vybraném oboru. Ve výchozím nastavení mřížka zobrazuje položky, které nemají bod obnovení za posledních sedm dnů. Pokud chcete najít neaktivní prostředky pro jiný časový rozsah, můžete upravit filtr **časového rozsahu** v horní části karty.

Po zjištění neaktivního prostředku můžete problém prozkoumat tak, že přejdete na řídicí panel zálohovacích položek nebo na podokno prostředků Azure pro daný prostředek (kdykoli je to možné). V závislosti na vašem scénáři můžete zvolit buď zastavení zálohování počítače (Pokud již neexistuje), a odstranit nepotřebné zálohy, které šetří náklady, nebo můžete opravit problémy v počítači, abyste měli jistotu, že jsou zálohování spolehlivá.

![Karta optimalizace – neaktivní prostředky](./media/backup-azure-configure-backup-reports/optimize-inactive-resources.png)

###### <a name="backup-items-with-a-large-retention-duration"></a>Zálohované položky s dlouhým uchováváním

Pomocí tohoto zobrazení můžete identifikovat položky, u kterých se zálohy uchovávají delší dobu, než vyžaduje vaše organizace.

Po výběru dlaždice **optimalizace zásad** , na které následuje dlaždice **optimalizace uchovávání** , se zobrazí mřížka obsahující všechny zálohované položky, pro které je uchovávání denních, týdenních, měsíčních nebo ročního bodu uchovávání (RP) větší než zadaná hodnota. Ve výchozím nastavení mřížka zobrazuje všechny zálohované položky ve vybraném oboru. Můžete použít filtry pro denní, týdenní, měsíční a roční uchování RP k dalšímu filtrování mřížky a identifikaci těchto položek, pro které by mohlo být ukládání potenciálně omezené, aby se ušetřily náklady na úložiště záloh.

U databázových úloh, jako je SQL a SAP HANA, se doby uchování zobrazené v mřížce shodují s dobami uchování úplných bodů zálohy, nikoli rozdílovým bodem zálohy. Totéž platí i pro filtry uchovávání informací.  

![Optimalizace optimalizace uchovávání karet](./media/backup-azure-configure-backup-reports/optimize-retention.png)

###### <a name="databases-configured-for-daily-full-backup"></a>Databáze nakonfigurované pro každodenní úplné zálohování

Pomocí tohoto zobrazení můžete identifikovat databázové úlohy, které byly nakonfigurovány pro každodenní úplnou zálohu. Použití denní rozdílové zálohy spolu s týdenním úplným zálohováním je často cenově výhodnější.

Po výběru dlaždice **optimalizace zásad** , na které následuje dlaždice **Optimalizace plánu zálohování** , se zobrazí mřížka obsahující všechny databáze se každodenní zásadou úplného zálohování. Můžete se rozhodnout přejít na konkrétní zálohovanou položku a upravit ji tak, aby používala každodenní rozdílovou zálohu s týdenním úplným zálohováním.

Filtr **typu správy zálohování** v horní části karty by měl mít položky **SQL na virtuálním počítači Azure** a **SAP HANA ve vybraných virtuálních počítačích Azure** , aby mřížka mohla zobrazovat databázové úlohy podle očekávání.

![Optimalizace karet – Optimalizace plánu zálohování](./media/backup-azure-configure-backup-reports/optimize-backup-schedule.png)

###### <a name="policy-adherence"></a>Dodržování zásad

Pomocí této karty můžete zjistit, jestli všechny instance služby Backup mají každý den alespoň jednu úspěšnou zálohu. U položek s týdenními zásadami zálohování můžete pomocí této karty zjistit, jestli všechny instance služby Backup mají alespoň jednu úspěšnou zálohu v týdnu.

K dispozici jsou dva typy zobrazení pro dodržování zásad:

* **Dodržování zásad podle časového období**: pomocí tohoto zobrazení můžete určit, kolik položek má mít alespoň jednu úspěšnou zálohu v daném dni a kolika se v daném dnu ještě neúspěšně zálohovaná. Kliknutím na řádek můžete zobrazit podrobnosti o všech úlohách zálohování, které byly aktivovány ve vybraném dni. Všimněte si, že pokud nastavíte časový rozsah na větší hodnotu, například v posledních 60 dnech, mřížka se vykreslí v týdenním zobrazení a zobrazí se počet všech položek, u kterých došlo k nejméně jednomu úspěšnému zálohování každý den v daném týdnu. Podobně existuje měsíční zobrazení pro větší časové rozsahy.

V případě položek, které jsou zálohovány týdně, Tato mřížka vám pomůže identifikovat všechny položky, které v daném týdnu obsahovaly alespoň jednu úspěšnou zálohu. V případě většího časového rozsahu, jako je například posledních 120 dní, se mřížka vykreslí v měsíčním zobrazení a v daném měsíci se zobrazí počet všech položek, které mají alespoň jednu úspěšnou zálohu. [Zásady používané v sestavách zálohování](https://docs.microsoft.com/azure/backup/configure-reports#conventions-used-in-backup-reports) vám pomají více podrobností o každodenních, týdenních a měsíčních zobrazeních.

![Dodržování zásad podle časového období](./media/backup-azure-configure-backup-reports/policy-adherence-by-time-period.png)

* **Dodržování zásad podle instance zálohy**: v tomto zobrazení můžete podrobnosti o dodržování zásad na úrovni instance zálohy. Buňka, která má zelenou poznámku, že v daném dni měla instance zálohování alespoň jednu úspěšnou zálohu. Buňka, která je červená, označuje, že instance zálohování neobsahovala v daném dni ještě jednu úspěšnou zálohu. Agregace denně, týdně a měsíčně následují stejné chování jako v zobrazení zásady pro dodržování zásad podle časových období. Kliknutím na libovolný řádek můžete zobrazit všechny úlohy zálohování v dané instanci zálohování ve vybraném časovém rozsahu.

![Dodržování zásad podle instance zálohování](./media/backup-azure-configure-backup-reports/policy-adherence-by-backup-instance.png)

###### <a name="email-azure-backup-reports"></a>Sestavy Azure Backup e-mailu

Pomocí funkce **Sestava e-mailu** dostupné v sestavách Backup můžete vytvářet automatizované úlohy pro příjem pravidelných sestav prostřednictvím e-mailu. Tato funkce funguje tak, že se v prostředí Azure nasadí aplikace logiky, která se dotazuje na data z vybraných pracovních prostorů Log Analytics (LA) na základě zadaných vstupů.

Jakmile se vytvoří aplikace logiky, budete muset autorizovat připojení k protokolům Azure Monitor a Office 365. Provedete to tak, že v Azure Portal přejdete na **Logic Apps** a vyhledáte název úlohy, kterou jste vytvořili. Výběrem položky nabídky **připojení rozhraní API** otevřete seznam připojení rozhraní API, která je potřeba autorizovat. [Přečtěte si další informace o tom, jak nakonfigurovat e-maily a řešit problémy](backup-reports-email.md).

###### <a name="customize-azure-backup-reports"></a>Přizpůsobení sestav Azure Backup

Sestavy zálohování využívají [systémové funkce v protokolech Azure monitor](backup-reports-system-functions.md). Tyto funkce pracují s daty v nezpracovaných Azure Backup tabulkách v LA a vracejí formátovaná data, která vám pomůžou snadno načíst informace o všech entitách souvisejících s zálohováním pomocí jednoduchých dotazů. 

Chcete-li vytvořit vlastní sešity vytváření sestav pomocí sestav zálohování jako základu, můžete přejít k sestavám zálohování, kliknout na **Upravit** v horní části sestavy a zobrazit nebo upravit dotazy používané v sestavách. Další informace o tom, jak vytvářet vlastní sestavy, najdete v [dokumentaci k sešitům Azure](https://docs.microsoft.com/azure/azure-monitor/visualize/workbooks-overview) . 

## <a name="export-to-excel"></a>Exportovat do aplikace Excel

Vyberte tlačítko se šipkou dolů v pravém horním rohu libovolného widgetu, jako je tabulka nebo graf, a exportujte obsah tohoto widgetu jako excelový list, protože se používá existující filtry. Chcete-li exportovat více řádků tabulky do aplikace Excel, můžete zvýšit počet řádků zobrazených na stránce pomocí šipky rozevíracího seznamu **řádky na stránce** v horní části každé mřížky.

## <a name="pin-to-dashboard"></a>Připnout na řídicí panel

Vyberte tlačítko připnout v horní části každého widgetu a připněte pomůcku na řídicí panel Azure Portal. Tato funkce vám pomůže vytvořit přizpůsobené řídicí panely, které budou zobrazovat nejdůležitější informace, které potřebujete.

## <a name="cross-tenant-reports"></a>Sestavy mezi klienty

Pokud používáte [Azure Lighthouse](../lighthouse/index.yml) s delegovaným přístupem k předplatným napříč více klientskými prostředími, můžete použít výchozí filtr předplatného. Vyberte tlačítko Filtr v pravém horním rohu Azure Portal a zvolte všechna předplatná, pro která chcete zobrazit data. Díky tomu můžete v rámci svých klientů vybrat Log Analytics pracovní prostory pro zobrazení víceklientské sestav.

## <a name="conventions-used-in-backup-reports"></a>Konvence používané v sestavách zálohování

- Filtry fungují zleva doprava a shora dolů na každé kartě. To znamená, že libovolný filtr platí pouze pro všechny widgety, které jsou umístěny vpravo od daného filtru nebo pod tímto filtrem.
- Výběr barevné dlaždice filtruje widgety pod dlaždicí pro záznamy, které se vztahují k hodnotě této dlaždice. Když například vyberete dlaždici **ochrana zastaveno** na kartě **zálohované položky** , vyfiltrují se v níže uvedené mřížce a grafy data zálohované položky ve stavu ochrana zastaveno.
- Dlaždice, které nejsou barevné, nelze vybrat.
- Data pro aktuální částečný den nejsou uvedena v sestavách. Takže když je vybraná hodnota **časového rozsahu** **Poslední 7 dní**, v sestavě se zobrazí záznamy za posledních sedmi dokončených dní. Aktuální den není zahrnutý.
- Tato sestava obsahuje podrobnosti o úlohách (kromě úloh protokolu), které se *aktivovaly* ve vybraném časovém rozsahu.
- Hodnoty zobrazené pro **cloudové úložiště** a **chráněné instance** jsou na *konci* vybraného časového rozsahu.
- Záložní položky zobrazené v sestavách jsou ty položky, které existují na *konci* vybraného časového rozsahu. Zálohované položky, které se odstranily uprostřed vybraného časového rozsahu, se nezobrazí. Stejné konvence platí i pro zásady zálohování.
- Pokud vybraný časový rozsah zahrnuje období kratší než 30 dní, grafy se vykreslují v denním zobrazení, kde je každý den jeden datový bod. Pokud časový rozsah zahrnuje období delší než 30 dní a menší nebo rovno 90 dní, grafy se vykreslují v týdenním zobrazení. V případě větších časových rozsahů se grafy vykreslují v měsíčním zobrazení. Týdenní nebo měsíční agregace dat pomáhá zlepšit výkon dotazů a jednodušší čitelnosti dat v grafech.
- Mřížka respektující zásady také sleduje podobnou logiku agregace, jak je popsáno výše. Existuje však několik menších rozdílů. Prvním rozdílem je to, že u položek s týdenními zásadami zálohování se nezobrazuje žádné denní zobrazení (k dispozici jsou jenom týdenní a měsíční zobrazení). V Gridech pro položky s týdenními zásadami zálohování se pak za měsíc považuje za dobu 4 týdne (28 dní) a ne 30 dnů, aby se vyloučily částečné týdny.

## <a name="query-load-times"></a>Doba načítání dotazů

Widgety v sestavě zálohování využívají dotazy Kusto, které se spouštějí v pracovních prostorech Log Analytics uživatele. Tyto dotazy obvykle zahrnují zpracování velkých objemů dat s více spojeními, které umožňují rozsáhlejší přehledy. V důsledku toho se widgety nemusí okamžitě načíst, když uživatel zobrazí sestavy v rámci rozsáhlého zálohování. Tato tabulka poskytuje přibližný odhad doby, kterou mohou různé widgety načítat, a to na základě počtu zálohovaných položek a časového rozsahu, pro který sestavu prohlížíte.

| **Počet zdrojů dat**                         | **Časový horizont** | **Přibližná doba načítání**                                              |
| --------------------------------- | ------------- | ------------------------------------------------------------ |
| ~ 5 K                       | 1 měsíc          | Dlaždice: 5-10 sekund <br> Mřížky: 5-10 sekund <br> Grafy: 5-10 sekund <br> Filtry na úrovni sestavy: 5-10 sekund|
| ~ 5 K                       | 3 měsíce          | Dlaždice: 5-10 sekund <br> Mřížky: 5-10 sekund <br> Grafy: 5-10 sekund <br> Filtry na úrovni sestavy: 5-10 sekund|
| ~ 10 K                       | 3 měsíce          | Dlaždice: 15-20 sekund <br> Mřížky: 15-20 sekund <br> Grafy: 1-2 min. <br> Filtry na úrovni sestavy: 25-30 sekund|
| ~ 15 K                       | 1 měsíc          | Dlaždice: 15-20 sekund <br> Mřížky: 15-20 sekund <br> Grafy: 50-60 sekund <br> Filtry na úrovni sestavy: 20-25 sekund|
| ~ 15 K                       | 3 měsíce          | Dlaždice: 20-30 sekund <br> Mřížky: 20-30 sekund <br> Grafy: 2-3 min. <br> Filtry na úrovni sestavy: 50-60 sekund |

## <a name="what-happened-to-the-power-bi-reports"></a>Co se stalo se sestavami Power BI? 

- Předchozí Power BI App Template pro vytváření sestav, která zdrojová data z účtu služby Azure Storage, je na cestě k vyřazení. Pro zobrazení sestav doporučujeme spustit odesílání diagnostických dat trezoru pro Log Analytics.

- Kromě toho [schéma v1](./backup-azure-diagnostics-mode-data-model.md#v1-schema-vs-v2-schema) odesílání diagnostických dat do účtu úložiště nebo v pracovním prostoru La je také na cestě k vyřazení. To znamená, že pokud jste napsali nějaké vlastní dotazy nebo automatizace založené na schématu V1, doporučujeme, abyste tyto dotazy aktualizovali na používání aktuálně podporovaného schématu v2.

## <a name="next-steps"></a>Další kroky

[Další informace o monitorování a vytváření sestav pomocí Azure Backup](./backup-azure-monitor-alert-faq.md)
