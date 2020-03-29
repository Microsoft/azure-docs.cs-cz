---
title: Konfigurace sestav Azure Backup
description: Konfigurace a zobrazení sestav pro Azure Backup pomocí Log Analytics a Azure Workbooků
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: 651d1383f0f292895ed95c91bafd5206d4f04c2c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78161197"
---
# <a name="configure-azure-backup-reports"></a>Konfigurace sestav Azure Backup

Běžným požadavkem pro správce záloh je získání přehledů o zálohách na základě dat trvajících dlouhou dobu. Pro takové řešení může existovat více případů použití – přidělování a předvídání spotřebovaného cloudového úložiště, auditování záloh a obnovení a identifikace klíčových trendů na různých úrovních rozlišovací schopnosti.

Služba Azure Backup dnes poskytuje řešení pro vytváření sestav, které využívá [protokoly monitorování Azure](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal) a [sešity Azure](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks)a pomáhá vám získat bohaté přehledy o vašich zálohách v celém vašem kombi záloh. Tento článek vysvětluje, jak nakonfigurovat a zobrazit sestavy zálohování.

## <a name="supported-scenarios"></a>Podporované scénáře

* Sestavy zálohování jsou podporované pro virtuální počítače Azure, SQL ve virtuálních počítačích Azure, SAP HANA/ASE ve virtuálních počítačích Azure, Agent zálohování Azure (MARS), Azure Backup Server (MABS) a System Center DPM.
* Pro úlohy aplikace DPM jsou podporovány sestavy zálohování pro dpm verze 5.1.363.0 a vyšší a agent verze 2.0.9127.0 a vyšší.
* Pro úlohy MABS jsou podporovány sestavy zálohování pro MABS verze 13.0.415.0 a vyšší a agent verze 2.0.9170.0 a vyšší.
* Sestavy zálohování lze zobrazit ve všech položkách zálohování, trezorech, předplatných a oblastech, pokud jsou jejich data odesílána do pracovního prostoru Analýzy protokolů (LA), ke kterému má uživatel přístup. 
* Pokud jste uživatel [Služby Azure Lighthouse](https://docs.microsoft.com/azure/lighthouse/) s delegovaným přístupem k předplatným vašich zákazníků, můžete tyto sestavy pomocí služby Azure Lighthouse použít k zobrazení sestav napříč všemi klienty.
* Data pro úlohy zálohování protokolu nejsou aktuálně zobrazena v sestavách.

## <a name="getting-started"></a>Začínáme

Chcete-li začít používat sestavy, postupujte podle následujících tří kroků:

1. **Vytvořte pracovní prostor Analýzy protokolů (LA) (nebo použijte existující pracovní prostor):**

Pro ukládání záložních sestavových dat je třeba nastavit jeden nebo více pracovních prostorů LA. Umístění a odběr, kde lze vytvořit tento pracovní prostor LA, je nezávislé na umístění a předplatném, kde existují vaše trezory. 

Podívejte se na následující článek: [Vytvoření pracovního prostoru analýzy protokolů](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace) na webu Azure Portal a nastavení pracovního prostoru LA.

Ve výchozím nastavení jsou data v pracovním prostoru LA zachována po dobu 30 dnů. Chcete-li zobrazit data pro delší časový horizont, změňte dobu uchování pracovního prostoru LA. Chcete-li změnit dobu uchování, naleznete v následujícím článku: [Správa využití a nákladů pomocí protokolů monitorování Azure](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage).

2. **Konfigurace nastavení diagnostiky pro vaše trezory:**

Prostředky Azure Resource Manager, jako jsou trezory služby Recovery Services, zaznamenávají informace o plánovaných operacích a operacích spouštěných uživatelem jako diagnostická data. 

V části monitorování trezoru služby Recovery Services vyberte **nastavení diagnostiky** a určete cíl diagnostických dat úložiště služby Recovery Services. [Další informace o používání diagnostických událostí](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events).

![Okno nastavení diagnostiky](./media/backup-azure-configure-backup-reports/resource-specific-blade.png)

Azure Backup také poskytuje integrované zásady Azure, které automatizují konfiguraci nastavení diagnostiky pro všechny trezory v daném oboru. V následujícím článku se dozvíte, jak používat tuto zásadu: [Konfigurace nastavení diagnostiky úložiště ve velkém měřítku](https://docs.microsoft.com/azure/backup/azure-policy-configure-diagnostics)

> [!NOTE]
> Po konfiguraci diagnostiky může trvat až 24 hodin pro počáteční nabízený datový požadavek na dokončení. Jakmile data začnou proudit do pracovního prostoru LA, nemusí být možné zobrazit data v sestavách okamžitě, protože data pro aktuální částečný den nejsou zobrazeny v sestavách (další podrobnosti [zde](https://docs.microsoft.com/azure/backup/configure-reports#conventions-used-in-backup-reports)). Proto se doporučuje začít prohlížet sestavy 2 dny po konfiguraci trezorů pro odesílání dat do Log Analytics.

3. **Zobrazení sestav na webu Azure Portal:**

Po konfiguraci trezorů pro odesílání dat do LA se zobrazí sestava zálohování tak, že přejdete do libovolného okna trezoru a kliknete na položku nabídky **Sestavy zálohování.** 

![Řídicí panel trezoru](./media/backup-azure-configure-backup-reports/vault-dashboard.png)

Kliknutím na tento odkaz se otevře sešit Sestavy zálohování.

> [!NOTE]
> * V současné době může počáteční zatížení sestavy trvat až 1 minutu.
> * Trezor služby Recovery Services je pouze vstupním bodem pro sestavy zálohování. Jakmile se sešit Sestavy zálohování otevře z okna trezoru, budete moci zobrazit data agregovaná ve všech trezorech (výběrem příslušné sady pracovních prostorů LA).

Níže je uveden popis různých karet, které sestava obsahuje:

* **Shrnutí** – karta Souhrn poskytuje přehled na vysoké úrovni o vašem zálohovacím kombi. Na kartě Souhrn můžete získat rychlý přehled o celkovém počtu položek zálohování, celkovém spotřebovaném cloudovém úložišti, počtu chráněných instancí a úspěšnosti úlohy na typ pracovního vytížení. Podrobnější informace o konkrétním typu artefaktu zálohování získáte na příslušných kartách.

![Karta Souhrn](./media/backup-azure-configure-backup-reports/summary.png)

* **Položky zálohování** – karta Položky zálohování umožňuje zobrazit informace a trendy v cloudovém úložišti spotřebovaném na úrovni položky zálohování. Například pokud používáte SQL v zálohování virtuálních zařízení Azure, uvidíte cloudové úložiště spotřebované pro každou databázi SQL, která se zálohuje. Můžete také zobrazit data pro záložní položky určitého stavu ochrany. Pokud například kliknete na dlaždici **Ochrana zastaveno** v horní části karty, vyfiltruje všechny níže uvedené widgety, aby se zobrazila data pouze pro položky zálohování v zastaveném stavu Ochrana.

![Karta Zálohovat položky](./media/backup-azure-configure-backup-reports/backup-items.png)

* **Využití** – karta Využití vám pomůže zobrazit klíčové parametry fakturace pro zálohy. Informace zobrazené na této kartě jsou na úrovni entity fakturace (chráněného kontejneru). Například v případě, že server DPM je zálohován do Azure, můžete zobrazit trend chráněných instancí a cloudového úložiště spotřebovaného pro server DPM. Podobně pokud používáte SQL v Azure Backup nebo SAP HANA v Azure Backup, tato karta poskytuje informace související s využitím na úrovni virtuálního počítače, které jsou obsaženy v těchto databázích.

![Karta Využití](./media/backup-azure-configure-backup-reports/usage.png)

* **Úlohy** – Karta Úlohy umožňuje zobrazit dlouhotrvající trendy úloh, jako je počet neúspěšných úloh za den a hlavní příčiny selhání úlohy. Tyto informace můžete zobrazit na úrovni agregace i na úrovni položky zálohování. Kliknutím na konkrétní položku zálohy v mřížce můžete zobrazit podrobné informace o každé úloze, která byla spuštěna u této položky zálohy ve vybraném časovém rozsahu.

![Karta Úlohy](./media/backup-azure-configure-backup-reports/jobs.png)

* **Zásady** – Karta Zásady umožňuje zobrazit informace o všech aktivních zásadách, jako je počet přidružených položek a celkové cloudové úložiště spotřebované položkami zálohovanými v rámci dané zásady. Kliknutím na konkrétní zásadu můžete zobrazit informace o jednotlivých přidružených položkách zálohování.

![Karta Zásady](./media/backup-azure-configure-backup-reports/policies.png)

## <a name="exporting-to-excel"></a>Export do aplikace Excel

Kliknutím na tlačítko se šipkou dolů v pravém horním rohu libovolného widgetu (tabulka/graf) exportujeobsah tohoto widgetu jako list aplikace Excel, jak je to s existujícími filtry. Chcete-li exportovat více řádků tabulky do aplikace Excel, můžete zvýšit počet řádků zobrazených na stránce pomocí rozevíracího souboru **Řádky na stránku** v horní části každé mřížky.

## <a name="pinning-to-dashboard"></a>Připnutí na řídicí panel

Kliknutím na ikonu Pin v horní části každého widgetu připněte widget na řídicí panel portálu Azure. To vám pomůže vytvořit přizpůsobené řídicí panely přizpůsobené tak, aby zobrazovaly nejdůležitější informace, které potřebujete.

## <a name="cross-tenant-reports"></a>Sestavy napříč tenanty

Pokud jste uživatel [Služby Azure Lighthouse](https://docs.microsoft.com/azure/lighthouse/) s delegovaným přístupem k předplatným ve více klientských prostředích, můžete použít výchozí filtr předplatného (kliknutím na ikonu filtru v pravém horním rohu portálu Azure) a vybrat všechna předplatná, pro která chcete zobrazit data. To vám umožní vybrat LA Workspaces napříč klienty pro zobrazení vícenájemních sestav.

## <a name="conventions-used-in-backup-reports"></a>Konvence používané v sestavách zálohování

* Filtry fungují zleva doprava a shora dolů na každé kartě, to znamená, že každý filtr se vztahuje pouze na všechny widgety, které jsou umístěny buď vpravo od tohoto filtru, nebo pod tímto filtrem. 
* Kliknutím na barevnou dlaždici se widgety pod dlaždicí vyfiltrují pro záznamy týkající se hodnoty této dlaždice. Pokud například kliknete na dlaždici *Ochrana zastaveno* na kartě Položky zálohování, zobrazí se níže mřížky a grafy, aby se zobrazila data pro položky zálohování ve stavu Ochrana zastavena.
* Na dlaždice, které nejsou barevné, nelze kliknout.
* Data pro aktuální den se v sestavách nezobrazují. Pokud je tedy vybraná hodnota **časového rozsahu** ***Posledních 7 dní***, sestava zobrazuje záznamy za posledních 7 dokončených dnů (které nezahrnují aktuální den).
* Sestava zobrazuje podrobnosti o úlohách (kromě úloh protokolu), které byly **spuštěny** ve vybraném časovém rozsahu. 
* Hodnoty zobrazené pro cloudové úložiště a chráněné instance jsou na **konci** vybraného časového rozsahu.
* Položky zálohování zobrazené v sestavách jsou položky, které existují na **konci** vybraného časového rozsahu. Položky zálohování, které byly odstraněny uprostřed vybraného časového rozsahu, se nezobrazí. Stejná konvence platí i pro zásady zálohování.

## <a name="query-load-times"></a>Časy načítání dotazu

Widgety v seestavě zálohování jsou napájeny dotazy Kusto, které běží na pracovních prostorech LA uživatele. Vzhledem k tomu, že tyto dotazy obvykle zahrnují zpracování velkého množství dat s více spojeními, které umožňují bohatší přehledy, widgety se nemusí načítat okamžitě, když uživatel prohlíží sestavy ve velkém zálohovacím kombi. Níže uvedená tabulka poskytuje hrubý odhad doby, kterou mohou různé widgety načíst, na základě počtu položek zálohování a časového rozsahu, pro který je sestava zobrazena:

| **# Zdroje dat**                         | **Časový horizont** | **Doba zatížení (cca)**                                              |
| --------------------------------- | ------------- | ------------------------------------------------------------ |
| ~5 K                       | 1 měsíc          | Dlaždice: 5-10 sekund <br> Mřížky: 5-10 sekund <br> Grafy: 5-10 sekund <br> Filtry na úrovni sestavy: 5-10 sekund|
| ~5 K                       | 3 měsíce          | Dlaždice: 5-10 sekund <br> Mřížky: 5-10 sekund <br> Grafy: 5-10 sekund <br> Filtry na úrovni sestavy: 5-10 sekund|
| ~10 k                       | 3 měsíce          | Dlaždice: 15-20 sekund <br> Mřížky: 15-20 sekund <br> Grafy: 1-2 min <br> Filtry na úrovni sestavy: 25-30 sekund|
| ~15 K                       | 1 měsíc          | Dlaždice: 15-20 sekund <br> Mřížky: 15-20 sekund <br> Grafy: 50-60 sekund <br> Filtry na úrovni sestavy: 20-25 sekund|
| ~15 K                       | 3 měsíce          | Dlaždice: 20-30 sekund <br> Mřížky: 20-30 sekund <br> Grafy: 2-3 minuty <br> Filtry na úrovni sestavy: 50-60 sekund |

## <a name="what-happened-to-the-power-bi-reports"></a>Co se stalo s sestavami Power BI?
* Naše dřívější aplikace šablony Power BI pro vytváření sestav (která získala data z účtu úložiště Azure) je na cestě k vyřazení. Doporučujeme začít odesílat diagnostická data trezoru do analýzy protokolů, jak je popsáno výše, pro zobrazení sestav.

* Kromě toho schéma V1 odesílání diagnostických dat do účtu úložiště nebo la workspace je také na cestě vyřazení. To znamená, že pokud jste napsali nějaké vlastní dotazy nebo automatizace založené na schématu V1, doporučujeme aktualizovat tyto dotazy tak, aby používaly aktuálně podporované schéma V2.

## <a name="next-steps"></a>Další kroky
[Další informace o monitorování a vytváření sestav pomocí Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-monitor-alert-faq)