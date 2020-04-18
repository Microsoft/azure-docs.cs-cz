---
title: Konfigurace sestav Azure Backup
description: Konfigurace a zobrazení sestav pro Azure Backup pomocí Log Analytics a sešitů Azure
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: bcc87deb19190149329369ca58f54b45b62b41fe
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617820"
---
# <a name="configure-azure-backup-reports"></a>Konfigurace sestav Azure Backup

Běžným požadavkem pro správce záloh je získání přehledů o zálohách založených na datech, která pokrývají dlouhou dobu. Případy použití takového řešení zahrnují:

 - Přidělování a předpovídání spotřebovaného cloudového úložiště.
 - Auditování záloh a obnovení.
 - Identifikace klíčových trendů na různých úrovních rozlišovací schopnosti.

Azure Backup dnes poskytuje řešení pro vytváření sestav, které používá [protokoly Azure Monitoru](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal) a [sešity Azure](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks). Tyto prostředky vám pomohou získat bohaté přehledy o vašich zálohách v celém zálohovacím kombi. Tento článek vysvětluje, jak nakonfigurovat a zobrazit sestavy azure backup.

## <a name="supported-scenarios"></a>Podporované scénáře

* Sestavy zálohování jsou podporované pro virtuální počítače Azure, SQL ve virtuálních počítačích Azure, SAP HANA/ASE ve virtuálních počítačích Azure, agenta Služby microsoft azure recovery services (MARS), Microsoft Azure Backup Server (MABS) a Správce ochrany dat system center (DPM).
* Pro úlohy Aplikace DPM jsou podporovány sestavy zálohování pro dpm verze 5.1.363.0 a vyšší a agent verze 2.0.9127.0 a vyšší.
* Pro úlohy MABS jsou podporovány sestavy zálohování pro MABS verze 13.0.415.0 a vyšší a agent verze 2.0.9170.0 a vyšší.
* Sestavy zálohování lze zobrazit ve všech položkách zálohování, trezorech, předplatných a oblastech, pokud jsou jejich data odesílána do pracovního prostoru Analýzy protokolů, ke kterému má uživatel přístup. Chcete-li zobrazit sestavy pro sadu trezorů, stačí mít přístup čtenáře k pracovnímu prostoru Log Analytics, do kterého trezory odesílají svá data. Nemusíte mít přístup k jednotlivým trezorům.
* Pokud jste uživatel [Služby Azure Lighthouse](https://docs.microsoft.com/azure/lighthouse/) s delegovaným přístupem k předplatným vašich zákazníků, můžete tyto sestavy pomocí služby Azure Lighthouse použít k zobrazení sestav napříč všemi klienty.
* Data pro úlohy zálohování protokolu aktuálně nejsou zobrazena v sestavách.

## <a name="get-started"></a>Začínáme

Chcete-li začít sestavy používat, postupujte takto.

#### <a name="1-create-a-log-analytics-workspace-or-use-an-existing-one"></a>1. Vytvoření pracovního prostoru Analýzy protokolů nebo použití existujícího pracovního prostoru

Nastavte jeden nebo více pracovních prostorů Log Analytics pro ukládání dat sestav zálohování. Umístění a odběr, kde lze vytvořit tento pracovní prostor Log Analytics je nezávislý na umístění a předplatné, kde existují vaše trezory. 

Pokud chcete nastavit pracovní prostor Analýzy protokolů, přečtěte si [tématu Vytvoření pracovního prostoru Analýzy protokolů na webu Azure Portal](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

Ve výchozím nastavení jsou data v pracovním prostoru Analýzy protokolů uchovávána po dobu 30 dnů. Chcete-li zobrazit data pro delší časový horizont, změňte dobu uchování pracovního prostoru Log Analytics. Pokud chcete změnit dobu uchovávání informací, přečtěte si informace [o správě využití a nákladů pomocí protokolů Azure Monitor .](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage)

#### <a name="2-configure-diagnostics-settings-for-your-vaults"></a>2. Konfigurace nastavení diagnostiky pro trezory

Prostředky Azure Resource Manager, jako jsou trezory služby Recovery Services, zaznamenávají informace o plánovaných operacích a operacích spouštěných uživatelem jako diagnostická data. 

V části monitorování trezoru služby Recovery Services vyberte **nastavení diagnostiky** a určete cíl diagnostických dat úložiště služby Recovery Services. Další informace o používání diagnostických událostí naleznete v [tématu Použití nastavení diagnostiky pro trezory služby Recovery Services](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events).

![Podokno nastavení diagnostiky](./media/backup-azure-configure-backup-reports/resource-specific-blade.png)

Azure Backup také poskytuje integrované zásady Azure, které automatizují konfiguraci nastavení diagnostiky pro všechny trezory v daném oboru. Informace o použití této zásady naleznete [v tématu Konfigurace nastavení diagnostiky trezoru ve velkém měřítku](https://docs.microsoft.com/azure/backup/azure-policy-configure-diagnostics).

> [!NOTE]
> Po konfiguraci diagnostiky může trvat až 24 hodin pro počáteční nabízená data. Po spuštění dat do pracovního prostoru Log Analytics se data v sestavách nemusí zobrazit okamžitě, protože data pro aktuální částečný den se v sestavách nezobrazují. Další informace naleznete [v tématu Konvence používané v sestavách zálohování](https://docs.microsoft.com/azure/backup/configure-reports#conventions-used-in-backup-reports). Doporučujeme začít prohlížet sestavy dva dny po konfiguraci trezorů pro odesílání dat do log analytics.

#### <a name="3-view-reports-in-the-azure-portal"></a>3. Zobrazení sestav na webu Azure Portal

Po konfiguraci trezorů tak, aby odesílalo data do log analytics, zobrazte sestavy zálohování tak, že přejdete do podokna libovolného úložiště a vyberete **sestavy zálohování**.

![Řídicí panel trezoru](./media/backup-azure-configure-backup-reports/vault-dashboard.png)

Vyberte tento odkaz, chcete-li otevřít sešit sestavy Zálohování.

> [!NOTE]
> * V současné době může počáteční zatížení sestavy trvat až 1 minutu.
> * Trezor služby Recovery Services je pouze vstupním bodem pro sestavy zálohování. Po otevření sešitu sestavy Zálohování z podokna úschovny vyberte příslušnou sadu pracovních prostorů Analýzy protokolů, abyste viděli data agregovaná ve všech trezorech.

Sestava obsahuje různé karty:

* **Shrnutí**: Na této kartě získáte přehled na vysoké úrovni o vašem zálohovacím kombi. Můžete získat rychlý přehled o celkovém počtu položek zálohování, celkové spotřebované cloudové úložiště, počet chráněných instancí a úspěšnost úlohy na typ pracovního vytížení. Podrobnější informace o konkrétním typu artefaktu zálohování naleznete na příslušných kartách.

   ![Karta Souhrn](./media/backup-azure-configure-backup-reports/summary.png)

* **Položky zálohování**: Na této kartě můžete zobrazit informace a trendy v cloudovém úložišti spotřebovaném na úrovni položky zálohování. Například pokud používáte SQL v záloha virtuálních počítačích Azure, uvidíte cloudové úložiště spotřebované pro každou databázi SQL, která je zálohována. Můžete také zobrazit data pro záložní položky určitého stavu ochrany. Například výběrem dlaždice **Ochrana zastavena** v horní části karty filtruje všechny widgety pod zobrazit data pouze pro položky zálohování ve stavu Zastaveno ochrany.

   ![Karta Zálohovat položky](./media/backup-azure-configure-backup-reports/backup-items.png)

* **Použití**: Na této kartě můžete zobrazit klíčové parametry fakturace pro zálohy. Informace zobrazené na této kartě jsou na úrovni fakturační entity (chráněného kontejneru). Například v případě, že server DPM je zálohován do Azure, můžete zobrazit trend chráněných instancí a cloudového úložiště spotřebovaného pro server DPM. Podobně pokud používáte SQL v Azure Backup nebo SAP HANA v Azure Backup, tato karta poskytuje informace související s využitím na úrovni virtuálního počítače, ve kterém jsou tyto databáze obsaženy.

   ![Karta Využití](./media/backup-azure-configure-backup-reports/usage.png)

* **Úlohy**: Na této kartě můžete zobrazit dlouhotrvající trendy úloh, jako je počet neúspěšných úloh za den a hlavní příčiny selhání úlohy. Tyto informace můžete zobrazit na úrovni agregace i na úrovni položky zálohování. Vyberte konkrétní položku zálohy v mřížce, chcete-li zobrazit podrobné informace o každé úloze, která byla spuštěna u této položky zálohování ve vybraném časovém rozsahu.

   ![Karta Úlohy](./media/backup-azure-configure-backup-reports/jobs.png)

* **Zásady**: Na této kartě můžete zobrazit informace o všech aktivních zásadách, jako je počet přidružených položek a celkové cloudové úložiště spotřebované položkami zálohovanými v rámci dané zásady. Vyberte konkrétní zásadu, chcete-li zobrazit informace o jednotlivých přidružených položkách zálohování.

   ![Karta Zásady](./media/backup-azure-configure-backup-reports/policies.png)

## <a name="export-to-excel"></a>Exportovat do aplikace Excel

Vyberte tlačítko se šipkou dolů v pravém horním horním tlačítku libovolného widgetu, například v tabulce nebo grafu, chcete-li exportovat obsah tohoto widgetu jako list aplikace Excel tak, jak je s existujícími filtry. Chcete-li exportovat více řádků tabulky do aplikace Excel, můžete zvýšit počet řádků zobrazených na stránce pomocí rozevírací šipky **Řádky na stránku** v horní části každé mřížky.

## <a name="pin-to-dashboard"></a>Připnout na řídicí panel

Vyberte tlačítko pin v horní části každého widgetu a připněte widget na řídicí panel portálu Azure. Tato funkce vám pomůže vytvořit přizpůsobené řídicí panely přizpůsobené tak, aby zobrazovaly nejdůležitější informace, které potřebujete.

## <a name="cross-tenant-reports"></a>Sestavy napříč tenanty

Pokud používáte [Azure Lighthouse](https://docs.microsoft.com/azure/lighthouse/) s delegovaným přístupem k předplatným ve více klientských prostředích, můžete použít výchozí filtr předplatného. Vyberte tlačítko filtru v pravém horním rohu portálu Azure a vyberte všechna předplatná, pro která chcete zobrazit data. Díky tomu můžete vybrat pracovní prostory Analýzy protokolů napříč klienty a zobrazit víceklientské sestavy.

## <a name="conventions-used-in-backup-reports"></a>Konvence používané v sestavách zálohování

* Filtry fungují zleva doprava a shora dolů na každé kartě. To znamená, že jakýkoli filtr se vztahuje pouze na všechny widgety, které jsou umístěny buď napravo od tohoto filtru, nebo pod tímto filtrem. 
* Výběrem barevné dlaždice se widgety pod dlaždicí vyfiltrují pro záznamy, které se připomenou k hodnotě této dlaždice. Například výběrem dlaždice **Ochrana zastavena** na kartě **Položky zálohování** se vyfiltruje mřížky a grafy níže, aby se zobrazila data pro zálohovací položky ve stavu Zastaveno ochrany.
* Na dlaždice, které nejsou barevné, nelze kliknout.
* Data pro aktuální částečný den se v sestavách nezobrazují. Pokud je tedy vybraná hodnota **časového rozsahu Posledních** **7 dní**, sestava zobrazuje záznamy za posledních sedm dokončených dnů. Aktuální den není zahrnut.
* Sestava zobrazuje podrobnosti o úlohách (kromě úloh protokolu), které byly *spuštěny* ve vybraném časovém rozsahu. 
* Hodnoty zobrazené pro **cloudové úložiště** a **chráněné instance** jsou na *konci* vybraného časového rozsahu.
* Položky zálohování zobrazené v sestavách jsou položky, které existují na *konci* vybraného časového rozsahu. Položky zálohy, které byly odstraněny uprostřed vybraného časového rozsahu, se nezobrazí. Stejná konvence platí také pro zásady zálohování.

## <a name="query-load-times"></a>Časy načítání dotazu

Widgety v sestavě Zálohování jsou napájeny dotazy Kusto, které běží v pracovních prostorech analýzy protokolů uživatele. Tyto dotazy obvykle zahrnují zpracování velkého množství dat s více spojeními, které umožňují bohatší přehledy. V důsledku toho se widgety nemusí načíst okamžitě, když uživatel zobrazí sestavy přes velké zálohovací kombi. Tato tabulka obsahuje hrubý odhad doby, kterou mohou různé widgety načíst, na základě počtu položek zálohování a časového rozsahu, pro který je sestava zobrazena.

| **# Zdroje dat**                         | **Časový horizont** | **Přibližné doby zatížení**                                              |
| --------------------------------- | ------------- | ------------------------------------------------------------ |
| ~5 K                       | 1 měsíc          | Dlaždice: 5-10 sekund <br> Mřížky: 5-10 sekund <br> Grafy: 5-10 sekund <br> Filtry na úrovni sestavy: 5-10 sekund|
| ~5 K                       | 3 měsíce          | Dlaždice: 5-10 sekund <br> Mřížky: 5-10 sekund <br> Grafy: 5-10 sekund <br> Filtry na úrovni sestavy: 5-10 sekund|
| ~10 k                       | 3 měsíce          | Dlaždice: 15-20 sekund <br> Mřížky: 15-20 sekund <br> Grafy: 1-2 min <br> Filtry na úrovni sestavy: 25-30 sekund|
| ~15 K                       | 1 měsíc          | Dlaždice: 15-20 sekund <br> Mřížky: 15-20 sekund <br> Grafy: 50-60 sekund <br> Filtry na úrovni sestavy: 20-25 sekund|
| ~15 K                       | 3 měsíce          | Dlaždice: 20-30 sekund <br> Mřížky: 20-30 sekund <br> Grafy: 2-3 minuty <br> Filtry na úrovni sestavy: 50-60 sekund |

## <a name="what-happened-to-the-power-bi-reports"></a>Co se stalo se sestavami Power BI? 
* Dřívější aplikace šablony Power BI pro vytváření sestav, která získávala data z účtu úložiště Azure, je na cestě k vyřazení. Doporučujeme začít odesílat diagnostická data trezoru do analýzy protokolů a zobrazit sestavy.

* Kromě toho schéma V1 odesílání diagnostických dat do účtu úložiště nebo pracovního prostoru Log Analytics je také na cestě vyřazení. Pokud jste napsali nějaké vlastní dotazy nebo automatizace založené na schématu V1, doporučujeme aktualizovat tyto dotazy tak, aby používaly aktuálně podporované schéma V2.

## <a name="next-steps"></a>Další kroky
[Další informace o monitorování a vytváření sestav pomocí Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-monitor-alert-faq)
