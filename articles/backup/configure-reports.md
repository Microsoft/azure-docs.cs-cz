---
title: Konfigurace sestav Azure Backup
description: Konfigurace a zobrazení sestav pro Azure Backup pomocí Log Analytics a sešitů Azure
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: 651d1383f0f292895ed95c91bafd5206d4f04c2c
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/28/2020
ms.locfileid: "78161197"
---
# <a name="configure-azure-backup-reports"></a>Konfigurace sestav Azure Backup

Běžným požadavkem pro správce zálohování je získat přehled o zálohách na základě dat, která jsou rozložená na dlouhou dobu. K dispozici je několik případů použití pro toto řešení, které přiděluje a předpovídá využití cloudového úložiště, auditování záloh a obnovení a určení klíčových trendů v různých úrovních členitosti.

V současné době Azure Backup poskytuje řešení pro vytváření sestav, které využívá [protokoly Azure monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal) a [sešity Azure](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks), což vám pomůže získat přehled o vašich zálohách napříč celou záloze. Tento článek vysvětluje, jak nakonfigurovat a zobrazit sestavy zálohování.

## <a name="supported-scenarios"></a>Podporované scénáře

* Sestavy zálohování se podporují pro virtuální počítače Azure, SQL ve virtuálních počítačích Azure, SAP HANA/pomocného programu na virtuálních počítačích Azure, Azure Backup Agent (MARS), Azure Backup Server (MABS) a System Center DPM.
* Pro úlohy DPM jsou sestavy zálohování podporované pro DPM verze 5.1.363.0 a novější a Agent verze 2.0.9127.0 a vyšší.
* Pro úlohy MABS jsou sestavy zálohování podporované pro MABS verze 13.0.415.0 a vyšší a verze agenta 2.0.9170.0 a vyšší.
* Sestavy zálohování můžete zobrazit ve všech zálohovaných položkách, trezorech, předplatných a oblastech, pokud jsou data odesílána do Log Analytics (LA) pracovního prostoru, ke kterému má uživatel přístup. 
* Pokud jste uživatelem [Azure Lighthouse](https://docs.microsoft.com/azure/lighthouse/) , který má delegovaný přístup k předplatným vašich zákazníků, můžete tyto sestavy použít v Azure Lighthouse k zobrazení sestav ve všech klientech.
* Data pro úlohy zálohování protokolů se v současnosti v sestavách nezobrazují.

## <a name="getting-started"></a>Začínáme

Pokud chcete začít používat sestavy, postupujte podle tří kroků popsaných níže:

1. **Vytvořit pracovní prostor Log Analytics (LA) (nebo použít existující):**

Pro uložení dat generování sestav zálohování je nutné nastavit jeden nebo více pracovních prostorů LA. Umístění a předplatné, kde se dá tento pracovní prostor LA vytvořit, nezávisí na umístění a předplatném, kde existují vaše trezory. 

Informace najdete v následujícím článku: [Vytvoření pracovního prostoru Log Analytics v Azure Portal](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace) k nastavení pracovního prostoru La.

Ve výchozím nastavení se data v pracovním prostoru LA uchovávají po dobu 30 dnů. Chcete-li zobrazit data pro časový horizont delšího času, změňte dobu uchovávání pracovního prostoru LA. Chcete-li změnit dobu uchovávání, přečtěte si následující článek: [Správa využití a nákladů pomocí protokolů Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage).

2. **Nakonfigurujte nastavení diagnostiky pro vaše trezory:**

Azure Resource Manager prostředky, jako jsou například trezory Recovery Services, zaznamenávají informace o plánovaných operacích a uživatelem aktivované operace jako diagnostická data. 

V části monitorování vašeho trezoru Recovery Services vyberte **nastavení diagnostiky** a zadejte cíl pro diagnostická data trezoru Recovery Services. [Přečtěte si další informace o použití diagnostických událostí](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events).

![Okno nastavení diagnostiky](./media/backup-azure-configure-backup-reports/resource-specific-blade.png)

Azure Backup také poskytuje integrovaný Azure Policy, který automatizuje konfiguraci nastavení diagnostiky pro všechny trezory v daném oboru. Informace o tom, jak používat tyto zásady, najdete v následujícím článku: [Konfigurace nastavení diagnostiky trezoru ve velkém měřítku](https://docs.microsoft.com/azure/backup/azure-policy-configure-diagnostics) .

> [!NOTE]
> Po nakonfigurování diagnostiky může trvat až 24 hodin, než se počáteční nabízení dat dokončí. Jakmile se data začnou přecházet do pracovního prostoru LA, možná nebude možné v sestavách zobrazit data hned, protože data pro aktuální částečný den nejsou uvedena v sestavách ( [Další podrobnosti)](https://docs.microsoft.com/azure/backup/configure-reports#conventions-used-in-backup-reports). Proto se doporučuje začít zobrazovat sestavy 2 dny po konfiguraci trezorů, aby odesílaly data do Log Analytics.

3. **Zobrazit sestavy na Azure Portal:**

Po nakonfigurování trezorů pro posílání dat na LA si můžete zobrazit sestavy zálohování tak, že přejdete do okna trezoru a kliknete na položku nabídky **sestavy zálohování** . 

![Řídicí panel trezoru](./media/backup-azure-configure-backup-reports/vault-dashboard.png)

Kliknutím na tento odkaz otevřete sešit zálohovaná sestava.

> [!NOTE]
> * V současné době počáteční načtení sestavy může trvat až 1 minutu.
> * Trezor Recovery Services je pouze vstupním bodem pro sestavy zálohování. Jakmile se sešit zálohovacích sestav otevře v okně trezoru, uvidíte data agregovaná napříč všemi trezory (výběrem příslušné sady pracovních prostorů LA).

Níže je uveden popis různých karet, které sestava obsahuje:

* **Shrnutí** – karta Souhrn poskytuje podrobný přehled vaší služby zálohování. Na kartě Souhrn můžete získat rychlý přehled o celkovém počtu zálohovaných položek, celkovém využitém cloudovém úložišti, počtu chráněných instancí a úspěšnosti úlohy na jeden typ pracovního vytížení. Podrobnější informace o konkrétním typu artefaktu zálohování získáte tak, že přejdete na příslušné karty.

![Karta souhrn](./media/backup-azure-configure-backup-reports/summary.png)

* **Zálohované položky** – karta zálohované položky vám umožní zobrazit informace a trendy v cloudovém úložišti spotřebovaném na úrovni zálohované položky. Pokud například používáte SQL v zálohování virtuálních počítačů Azure, můžete zobrazit cloudové úložiště spotřebované pro každou zálohovanou databázi SQL. Můžete se také rozhodnout, že se budou zobrazovat data pro záložní položky určitého stavu ochrany. Například kliknutím na dlaždici **zastavená ochrana** v horní části karty se vyfiltrují všechny níže uvedené pomůcky, aby se zobrazila data pouze pro zálohované položky ve stavu ochrana zastaveno.

![Karta zálohované položky](./media/backup-azure-configure-backup-reports/backup-items.png)

* **Využití** – karta použití vám pomůže zobrazit parametry fakturace klíče pro vaše zálohy. Informace zobrazené na této kartě jsou na úrovni fakturačních entit (chráněných kontejnerů). Například v případě zálohování serveru DPM do Azure můžete zobrazit trend chráněných instancí a cloudového úložiště spotřebovaného pro server DPM. Podobně platí, že pokud používáte SQL Server v Azure Backup nebo SAP HANA v Azure Backup, tato karta poskytuje informace související s využitím na úrovni virtuálního počítače, ve kterém jsou tyto databáze obsažené.

![Karta použití](./media/backup-azure-configure-backup-reports/usage.png)

* **Úlohy** – karta úlohy umožňuje zobrazit dlouho běžící trendy na úlohách, jako je počet neúspěšných úloh za den a nejvyšší příčiny selhání úlohy. Tyto informace můžete zobrazit jak na agregované úrovni, tak na úrovni zálohované položky. Kliknutím na konkrétní zálohovanou položku v mřížce můžete zobrazit podrobné informace o jednotlivých úlohách, které se aktivovaly u dané zálohované položky ve vybraném časovém rozsahu.

![Karta Úlohy](./media/backup-azure-configure-backup-reports/jobs.png)

* **Zásady** – karta zásady umožňuje zobrazit informace o všech aktivních zásadách, jako je počet přidružených položek, a celkové cloudové úložiště spotřebované položkami zálohovanými v rámci dané zásady. Kliknutím na konkrétní zásadu můžete zobrazit informace o všech přidružených zálohovaných položkách.

![Karta Zásady](./media/backup-azure-configure-backup-reports/policies.png)

## <a name="exporting-to-excel"></a>Export do aplikace Excel

Kliknutí na tlačítko se šipkou dolů v pravém horním rohu libovolného widgetu (tabulka/graf) exportuje obsah tohoto widgetu jako excelový list, protože se používá existující filtry. Chcete-li exportovat více řádků tabulky do aplikace Excel, můžete zvýšit počet řádků zobrazených na stránce pomocí rozevíracího seznamu **řádky na stránce** v horní části každé mřížky.

## <a name="pinning-to-dashboard"></a>Připnutí na řídicí panel

Kliknutím na ikonu připnutí v horní části každého widgetu připnete pomůcku na řídicí panel Azure Portal. To vám pomůže vytvořit přizpůsobené řídicí panely, které budou zobrazovat nejdůležitější informace, které potřebujete.

## <a name="cross-tenant-reports"></a>Sestavy mezi klienty

Pokud jste uživatelem [Azure Lighthouse](https://docs.microsoft.com/azure/lighthouse/) , který má delegovaný přístup k předplatným napříč několika klientskými prostředími, můžete použít výchozí filtr předplatného (kliknutím na ikonu filtru v pravém horním rohu Azure Portal) a zvolíte všechna předplatná, pro která chcete zobrazit data. Tím umožníte, aby se v rámci klientů vybrali LA pracovní prostory pro zobrazení sestav s více klienty.

## <a name="conventions-used-in-backup-reports"></a>Konvence používané v sestavách zálohování

* Filtry fungují zleva doprava a shora dolů na každé kartě, to znamená, že libovolný filtr platí jenom pro všechny widgety, které jsou umístěné vpravo od tohoto filtru nebo pod tímto filtrem. 
* Kliknutím na barevnou dlaždici zobrazíte widgety pod dlaždicí záznamů, které se vztahují k hodnotě této dlaždice. Například kliknutím na dlaždici *zastavená ochrana* na kartě zálohované položky filtrujete mřížky a grafy níže, aby se zobrazila data zálohované položky ve stavu ochrana zastaveno.
* Dlaždice, u kterých není barva, nelze kliknout.
* Data pro aktuální den se v sestavách nezobrazují. Proto když je vybraná hodnota **časového rozsahu** , ***posledních 7 dní***, sestava zobrazuje záznamy za posledních 7 dokončených dnů (což nezahrnuje aktuální den).
* Tato sestava obsahuje podrobnosti o úlohách (kromě úloh protokolu), které se **aktivovaly** ve vybraném časovém rozsahu. 
* Hodnoty zobrazené pro cloudové úložiště a chráněné instance jsou na **konci** vybraného časového rozsahu.
* Záložní položky zobrazené v sestavách jsou ty položky, které existují na **konci** vybraného časového rozsahu. Zálohované položky, které se odstranily uprostřed vybraného časového rozsahu, se nezobrazují. Stejné konvence platí i pro zásady zálohování.

## <a name="query-load-times"></a>Doba načítání dotazů

Widgety v sestavě zálohování využívají dotazy Kusto, které běží v pracovních prostorech uživatele LA. Vzhledem k tomu, že tyto dotazy obvykle zahrnují zpracování velkých objemů dat, s více spojeními, které umožňují rozsáhlejší přehledy, se widgety nemusí načíst okamžitě, pokud uživatel zobrazuje sestavy v rámci velké části zálohy. V následující tabulce najdete přibližný odhad doby, kterou mohou různé widgety načítat, a to na základě počtu zálohovaných položek a časového rozsahu, pro který sestavu vyhlížíte:

| **Počet zdrojů dat**                         | **Časový horizont** | **Doba načítání (přibližně)**                                              |
| --------------------------------- | ------------- | ------------------------------------------------------------ |
| ~ 5 K                       | 1 měsíc          | Dlaždice: 5-10 sekund <br> Mřížky: 5-10 sekund <br> Grafy: 5-10 sekund <br> Filtry na úrovni sestavy: 5-10 sekund|
| ~ 5 K                       | 3 měsíce          | Dlaždice: 5-10 sekund <br> Mřížky: 5-10 sekund <br> Grafy: 5-10 sekund <br> Filtry na úrovni sestavy: 5-10 sekund|
| ~ 10 K                       | 3 měsíce          | Dlaždice: 15-20 sekund <br> Mřížky: 15-20 sekund <br> Grafy: 1-2 min. <br> Filtry na úrovni sestavy: 25-30 sekund|
| ~ 15 K                       | 1 měsíc          | Dlaždice: 15-20 sekund <br> Mřížky: 15-20 sekund <br> Grafy: 50-60 sekund <br> Filtry na úrovni sestavy: 20-25 sekund|
| ~ 15 K                       | 3 měsíce          | Dlaždice: 20-30 sekund <br> Mřížky: 20-30 sekund <br> Grafy: 2-3 min. <br> Filtry na úrovni sestavy: 50-60 sekund |

## <a name="what-happened-to-the-power-bi-reports"></a>Co se stalo se sestavami Power BI?
* Náš dřívější Power BI App Template pro vytváření sestav (zdrojová data z účtu Azure Storage) se nachází na cestě pro vyřazení. Doporučuje se začít odesílat diagnostická data trezoru, aby Log Analytics, jak je popsáno výše, k zobrazení sestav.

* Kromě toho schéma v1 odesílání diagnostických dat do účtu úložiště nebo v pracovním prostoru LA je také na cestě k vyřazení. To znamená, že pokud jste napsali vlastní dotazy nebo automatizace založené na schématu V1, doporučujeme tyto dotazy aktualizovat tak, aby používaly aktuálně podporované schéma v2.

## <a name="next-steps"></a>Další kroky
[Další informace o monitorování a vytváření sestav pomocí Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-monitor-alert-faq)