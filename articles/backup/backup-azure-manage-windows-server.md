---
title: Správa trezorů a serverů služby Azure Recovery Services
description: V tomto článku se dozvíte, jak pomocí řídicího panelu Přehled úložiště služby Recovery Services sledovat a spravovat trezory služby Recovery Services.
ms.topic: conceptual
ms.date: 07/08/2019
ms.openlocfilehash: 1a4d23c157700f42422cfe7ca8fa1c49e2cf128a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131977"
---
# <a name="monitor-and-manage-recovery-services-vaults"></a>Monitorování a správa trezorů služby Recovery Services

Tento článek vysvětluje, jak pomocí řídicího panelu **Přehled** úložiště služby Recovery Services sledovat a spravovat trezory služby Recovery Services. Když ze seznamu otevřete trezor služby Recovery Services, otevře se řídicí panel **Přehled** pro vybraný trezor. Řídicí panel poskytuje různé podrobnosti o úschovně. Existují *dlaždice,* které ukazují: stav kritických a upozornění, probíhající a neúspěšné úlohy zálohování a množství místně redundantního úložiště (LRS) a geograficky redundantního úložiště (GRS). Pokud zálohujete virtuální počítače Azure do trezoru, zobrazí se na [dlaždici **Stav před kontrolou zálohování** všechny důležité nebo varovné položky](https://docs.microsoft.com/azure/backup/backup-azure-manage-windows-server#backup-pre-check-status). Následující obrázek je řídicí panel **Přehled** pro **contoso-vault**. Dlaždice **Položky zálohování** ukazuje, že v úschovně je registrováno devět položek.

![Řídicí panel trezoru služby obnovení](./media/backup-azure-manage-windows-server/rs-vault-blade.png)

Předpoklady pro tento článek jsou: předplatné Azure, trezor služby Recovery Services a že pro trezor je nakonfigurovaná alespoň jedna položka zálohování.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="open-a-recovery-services-vault"></a>Otevření trezoru služby Recovery Services

Chcete-li sledovat výstrahy nebo zobrazit data správy týkající se trezoru služby Recovery Services, otevřete trezor.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/) pomocí svého předplatného Azure.

2. Na portálu klikněte na **Všechny služby**.

   ![Otevřít seznam trezorů služby Recovery Services krok 1](./media/backup-azure-manage-windows-server/open-rs-vault-list.png)

3. V dialogovém okně **Všechny služby** zadejte **služby obnovení**. Seznam se průběžně filtruje podle zadávaného textu. Po zobrazení možnosti **trezory služby Recovery Services** na ni kliknutím otevřete seznam trezorů služby Recovery Services ve vašem předplatném.

    ![Vytvoření trezoru Recovery Services – krok 1](./media/backup-azure-manage-windows-server/list-of-rs-vaults.png) <br/>

4. V seznamu úschoven kliknutím na úschovnu otevřete řídicí panel **Přehled.**

    ![Řídicí panel trezoru služby obnovení](./media/backup-azure-manage-windows-server/rs-vault-blade.png) <br/>

    Řídicí panel Přehled používá dlaždice k poskytování výstrah a dat úloh zálohování.

## <a name="monitor-backup-jobs-and-alerts"></a>Sledování úloh a výstrah zálohování

Řídicí panel **Přehled** úložiště služby Recovery Services poskytuje dlaždice pro informace o monitorování a používání. Dlaždice v části Monitorování zobrazují kritické a varovné výstrahy a probíhající úlohy a neúspěšné úlohy. Kliknutím na konkrétní výstrahu nebo úlohu otevřete nabídku Zálohování výstrah nebo úloh y zálohování filtrované pro tuto úlohu nebo výstrahu.

![Úlohy řídicího panelu zálohování](./media/backup-azure-manage-windows-server/monitor-dashboard-tiles-warning.png)

Část Monitorování zobrazuje výsledky předdefinovaných **dotazů na výstrahy zálohování** a **úlohy zálohování.** Dlaždice Monitorování poskytují aktuální informace o:

* Kritická a varovná upozornění pro úlohy zálohování (za posledních 24 hodin)
* Předběžná kontrola stavu virtuálních počítačích Azure. Úplné informace o stavu předběžné kontroly naleznete v [tématu Backup Pre-Check Status](#backup-pre-check-status).
* Probíhá zálohování a úlohy, které se nezdařily (za posledních 24 hodin).

Dlaždice Využití poskytují:

* Počet položek zálohování nakonfigurovaných pro úschovnu.
* Úložiště Azure (oddělené LRS a GRS) spotřebované trezoru.

Kliknutím na dlaždice (kromě úložiště zálohování) otevřete přidruženou nabídku. Na obrázku výše se na dlaždici Výstrahy zálohování zobrazují tři kritické výstrahy. Kliknutím na řádek Kritické výstrahy na dlaždici Výstrahy zálohování se otevřou výstrahy zálohování filtrované pro kritické výstrahy.

![Nabídka Výstrahy zálohování filtrovaná pro kritické výstrahy](./media/backup-azure-manage-windows-server/critical-backup-alerts.png)

Nabídka Výstrahy zálohování na obrázku výše je filtrována podle: Stav je aktivní, závažnost je kritická a čas je předchozích 24 hodin.

### <a name="backup-pre-check-status"></a>Stav předběžné kontroly zálohování

Předběžné kontroly zálohování zkontrolujte konfiguraci virtuálních počítačů, zda nenajdete problémy, které mohou nepříznivě ovlivnit zálohování. Tyto informace agregují, takže je můžete zobrazit přímo z řídicího panelu úložiště služby Recovery Services a poskytnout doporučení pro nápravná opatření k zajištění úspěšných záloh konzistentních souborů nebo konzistentních s aplikacemi. Nevyžadují žádnou infrastrukturu a nemají žádné další náklady.  

Předběžné kontroly zálohování se spustí jako součást naplánovaných operací zálohování pro vaše virtuální počítače Azure. Končí jedním z těchto stavů:

* **Předáno**: Tento stav označuje, že konfigurace virtuálního počítače by měla vést k úspěšné zálohy a není třeba přijmout žádná nápravná opatření.
* **Upozornění**: Tento stav označuje jeden nebo více problémů v konfiguraci virtuálního počítače, které *mohou* vést k selhání zálohování. Poskytuje *doporučené* kroky k zajištění úspěšného zálohování. Například není nainstalován nejnovější agent virtuálního počítače může způsobit zálohování selhání občas. Tato situace by poskytla stav upozornění.
* **Kritický**: Tento stav označuje jeden nebo více kritických problémů v konfiguraci virtuálního počítače, které *povedou* k selhání zálohování a poskytuje *požadované* kroky k zajištění úspěšných záloh. Například problém se sítí způsobený aktualizací pravidel sítě zabezpečení sítě virtuálního počítače způsobí selhání zálohování, protože brání virtuálnímu počítači v komunikaci se službou Azure Backup. Tato situace by poskytla kritický stav.

Podle následujících kroků začněte řešit všechny problémy hlášené zálohováním předem kontroly záloh virtuálních počítačích v trezoru služby Recovery Services.

* Vyberte **dlaždici Stav před kontrolou zálohování (virtuální počítače Azure)** na řídicím panelu trezoru služby Recovery Services.
* Vyberte libovolný virtuální virtuální počítače se stavem Před kontrolou zálohování **kritické** nebo **upozornění**. Tato akce otevře podokno **podrobností virtuálního soudu.**
* Vyberte oznámení podokna v horní části podokna, abyste odhalili popis problému konfigurace a nápravné kroky.

## <a name="manage-backup-alerts"></a>Správa výstrah zálohování

Chcete-li získat přístup k nabídce Výstrahy zálohování, klepněte v nabídce trezoru služby Recovery Services na **položku Výstrahy zálohování**.

![Výstrahy zálohování](./media/backup-azure-manage-windows-server/backup-alerts-menu.png)

Sestava Výstrahy zálohování obsahuje seznam výstrah pro úschovnu.

![Výstrahy zálohování](./media/backup-azure-manage-windows-server/backup-alerts.png)

### <a name="alerts"></a>Výstrahy

V seznamu Výstrahy zálohování se zobrazí vybrané informace pro filtrované výstrahy. V nabídce Zálohování výstrah můžete filtrovat kritické výstrahy nebo upozornění.

| Alert Level | Události, které generují výstrahy |
| ----------- | ----------- |
| Kritická | Obdržíte kritické výstrahy, když: Úlohy zálohování se nezdaří, úlohy obnovení se nezdaří a když zastavíte ochranu na serveru, ale zachováte data.|
| Upozornění | Zobrazí se upozornění, když: Úlohy zálohování s úplnými upozorněními, například pokud není zálohováno méně než 100 souborů z důvodu problémů s poškozením nebo pokud je úspěšně zálohováno více než 1 000 000 souborů). |
| Informační | v současné době nejsou používána žádná informační upozornění. |

### <a name="viewing-alert-details"></a>Zobrazení podrobností výstrah

Sestava výstrah zálohování sleduje osm podrobností o každé výstrahě. Pomocí tlačítka **Vybrat sloupce** upravte podrobnosti v sestavě.

![Výstrahy zálohování](./media/backup-azure-manage-windows-server/backup-alerts.png)

Ve výchozím nastavení se v sestavě zobrazí všechny podrobnosti kromě **posledního času výskytu**.

* Výstrahy
* Položka zálohování
* Chráněný server
* Severity
* Doba trvání
* Čas vytvoření
* Status
* Čas posledního výskytu

### <a name="change-the-details-in-alerts-report"></a>Změna podrobností v sestavě upozornění

1. Chcete-li změnit informace sestavy, klikněte v nabídce **Zálohování upozornění** na tlačítko **Vybrat sloupce**.

   ![Výstrahy zálohování](./media/backup-azure-manage-windows-server/alerts-menu-choose-columns.png)

   Otevře se nabídka **Vybrat sloupce.**

2. V nabídce **Zvolit sloupce** zvolte podrobnosti, které se mají v sestavě zobrazit.

    ![Nabídka Vybrat sloupce](./media/backup-azure-manage-windows-server/choose-columns-menu.png)

3. Kliknutím na **Hotovo** uložte změny a zavřete nabídku Vybrat sloupce.

   Pokud provedete změny, ale nechcete je zachovat, kliknutím na **Obnovit** vrátíte vybranou vybranou vybranou konfiguraci do poslední uložené konfigurace.

### <a name="change-the-filter-in-alerts-report"></a>Změna filtru v sestavě výstrah

Pomocí nabídky **Filtr** můžete změnit závažnost, stav, čas zahájení a čas ukončení výstrah.

> [!NOTE]
> Úprava filtru Výstrahy zálohování nezmění kritické výstrahy nebo upozornění v řídicím panelu Přehled úschovny.
>  

1. Chcete-li změnit filtr Upozornění na zálohování, klepněte v nabídce Výstrahy zálohování na **tlačítko Filtrovat**.

   ![Zvolte nabídku filtru](./media/backup-azure-manage-windows-server/alerts-menu-choose-filter.png)

   Zobrazí se nabídka Filtr.

   ![Zvolte nabídku filtru](./media/backup-azure-manage-windows-server/filter-alert-menu.png)

2. Upravte závažnost, stav, čas zahájení nebo čas ukončení a kliknutím na **Hotovo** uložte změny.

## <a name="configuring-notifications-for-alerts"></a>Konfigurace oznámení pro výstrahy

Nakonfigurujte oznámení tak, aby generovala e-maily, když dojde k upozornění nebo kritické výstraze. E-mailová upozornění můžete odesílat každou hodinu nebo v případě, že dojde k určitému upozornění.

   ![Upozornění na filtry](./media/backup-azure-manage-windows-server/configure-notification.png)

Ve výchozím nastavení jsou e-mailová oznámení **zapnuta**. Kliknutím na **Vypnuto** zastavíte e-mailová oznámení.

V ovládacím prvku **Upozornit** zvolte **Na výstrahu,** pokud nechcete seskupit nebo nemáte mnoho položek, které by mohly generovat výstrahy. Každá výstraha má za následek jedno oznámení (výchozí nastavení) a e-mail s rozlišením je odeslán okamžitě.

Pokud vyberete **hodinovou digest**, bude příjemcům odeslán e-mail s vysvětlením nevyřešených výstrah generovaných za poslední hodinu. Na konci hodiny je odeslán e-mail s rozlišením.

Zvolte závažnost výstrahy (kritická nebo upozornění) použitá ke generování e-mailu. V současné době neexistují žádné informační výstrahy.

## <a name="manage-backup-items"></a>Správa položek zálohování

Trezor služby Recovery Services obsahuje mnoho typů záložních dat. [Přečtěte si další informace](backup-overview.md#what-can-i-back-up) o tom, co můžete zálohovat. Chcete-li spravovat různé servery, počítače, databáze a úlohy, zobrazte obsah trezoru klepnutím na dlaždici **Zálohovat položky.**

![Dlaždice zálohovacích položek](./media/backup-azure-manage-windows-server/backup-items.png)

Otevře se seznam položek zálohování uspořádaný podle typu správy zálohování.

![seznam položek zálohování](./media/backup-azure-manage-windows-server/list-backup-items.png)

Chcete-li prozkoumat určitý typ chráněné instance, klepněte na položku ve sloupci Typ správy zálohování. Například ve výše uvedené bitové kopii existují dva virtuální počítače Azure chráněné v tomto trezoru. Kliknutím na **Azure Virtual Machine**otevře seznam chráněných virtuálních počítačů v tomto trezoru.

![seznam typu Zálohování](./media/backup-azure-manage-windows-server/list-of-protected-virtual-machines.png)

Seznam virtuálních počítačů obsahuje užitečná data: přidružená skupina prostředků, předchozí [předběžná kontrola zálohování](https://docs.microsoft.com/azure/backup/backup-azure-manage-windows-server#backup-pre-check-status), stav poslední zálohy a datum posledního bodu obnovení. Tři tečky v posledním sloupci otevřou nabídku pro spuštění běžných úloh. Užitečná data uvedená ve sloupcích se liší pro každý typ zálohování.

![seznam typu Zálohování](./media/backup-azure-manage-windows-server/ellipsis-menu.png)

## <a name="manage-backup-jobs"></a>Správa úloh zálohování

Dlaždice **Úlohy zálohování** na řídicím panelu úschovny zobrazuje počet probíhajících úloh nebo neúspěšných za posledních 24 hodin. Dlaždice poskytuje pohled do nabídky Úlohy zálohování.

![Zálohování položek z nastavení](./media/backup-azure-manage-windows-server/backup-jobs-tile.png)

Chcete-li zobrazit další podrobnosti o úlohách, klepněte na tlačítko **Probíhá** nebo **se nezdařilo** otevření nabídky Zálohování úloh filtrované pro tento stav.

### <a name="backup-jobs-menu"></a>Nabídka úloh zálohování

V nabídce **Úlohy zálohování** se zobrazují informace o typu položky, operaci, stavu, čase zahájení a době trvání.  

Chcete-li otevřít nabídku Úlohy zálohování, klepněte v hlavní nabídce úschovny na položku **Zálohování úloh**.

![Zálohování položek z nastavení](./media/backup-azure-manage-windows-server/backup-jobs-menu-item.png)

Otevře se seznam úloh zálohování.

![Zálohování položek z nastavení](./media/backup-azure-manage-windows-server/backup-jobs-list.png)

Nabídka Úlohy zálohování zobrazuje stav pro všechny operace, na všech typech zálohování za posledních 24 hodin. Pomocí **filtru** můžete filtry změnit. Filtry jsou vysvětleny v následujících částech.

Změna filtrů:

1. V nabídce Úlohy zálohování trezoru klepněte na **tlačítko Filtrovat**.

   ![Zálohování položek z nastavení](./media/backup-azure-manage-windows-server/vault-backup-job-menu-filter.png)

    Otevře se nabídka Filtr.

   ![Zálohování položek z nastavení](./media/backup-azure-manage-windows-server/filter-menu-backup-jobs.png)

2. Zvolte nastavení filtru a klepněte na **tlačítko Hotovo**. Filtrovaný seznam se aktualizuje na základě nového nastavení.

#### <a name="item-type"></a>Typ položky

Typ Item je typ správy zálohování chráněné instance. Existují čtyři typy; naleznete v následujícím seznamu. Můžete zobrazit všechny typy položek nebo jeden typ položky. Nelze vybrat dva nebo tři typy položek. Dostupné typy položek jsou:

* Všechny typy položek
* Virtuální počítač Azure
* Soubory a složky
* Azure Storage
* Pracovní vytížení Azure

#### <a name="operation"></a>Operace

Můžete zobrazit jednu operaci nebo všechny operace. Nelze vybrat dvě nebo tři operace. Dostupné operace jsou:

* Všechny operace
* Zaregistrovat
* Konfigurace zálohování
* Zálohování
* Obnovení
* Zakázat zálohování
* Odstranění zálohovaných dat

#### <a name="status"></a>Status

Můžete zobrazit veškerý stav nebo jeden. Nelze vybrat dva nebo tři stavy. Dostupné stavy jsou:

* Veškerý stav
* Dokončeno
* Probíhá
* Failed
* Zrušeno
* Doplněno varováním

#### <a name="start-time"></a>Čas spuštění

Den a čas, kdy dotaz začíná. Výchozí hodnota je 24 hodin.

#### <a name="end-time"></a>Čas ukončení

Den a čas ukončení dotazu.

### <a name="export-jobs"></a>Export úlohy

Pomocí **exportních úloh** vytvořte tabulku obsahující všechny informace o nabídce úloh. Tabulka obsahuje jeden list, který obsahuje souhrn všech úloh a jednotlivé listy pro každou úlohu.

Chcete-li exportovat informace o úlohách do tabulky, klepněte na **položku Exportovat úlohy**. Služba vytvoří tabulku s názvem trezoru a data, ale název můžete změnit.

## <a name="monitor-backup-usage"></a>Sledování využití záloh

Dlaždice úložiště zálohování na řídicím panelu zobrazuje úložiště spotřebované v Azure. Využití úložiště je zajištěno:

* Využití úložiště LRS v cloudu přidružené k trezoru
* Využití úložiště CLOUD GRS přidružené k trezoru

## <a name="troubleshooting-monitoring-issues"></a>Poradce při potížích s monitorováním

**Problém:** Úlohy nebo výstrahy od agenta azure zálohování se nezobrazují na portálu.

**Postup řešení potíží:** Proces , ```OBRecoveryServicesManagementAgent```odešle data úlohy a výstrahy do služby Azure Backup. V některých se tento proces může zaseknout nebo vypnout.

1. Chcete-li ověřit, zda proces není spuštěn, ```OBRecoveryServicesManagementAgent``` otevřete Správce **úloh**a kontrola je spuštěna.

2. Pokud proces není spuštěn, otevřete **Ovládací panely**a projděte seznam služeb. Spusťte nebo restartujte **agenta microsoft azure recovery services management agenta**.

    Další informace naleznete v protokolech na adrese:<br/>
   `<AzureBackup_agent_install_folder>\Microsoft Azure Recovery Services Agent\Temp\GatewayProvider*`Například:<br/>
   `C:\Program Files\Microsoft Azure Recovery Services Agent\Temp\GatewayProvider0.errlog`

## <a name="next-steps"></a>Další kroky

* [Obnovení windows serveru nebo klienta Windows z Azure](backup-azure-restore-windows-server.md)
* Další informace o Azure Backup najdete v [tématu Přehled zálohování Azure](backup-introduction-to-azure-backup.md)
