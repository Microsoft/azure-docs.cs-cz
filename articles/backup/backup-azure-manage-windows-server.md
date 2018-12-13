---
title: Správa serverů a trezorů služeb zotavení Azure
description: Spravujte úlohy a výstrahy v trezoru služby Azure Recovery Services.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 8/21/2018
ms.author: raynew
ms.openlocfilehash: 716ddcaf61c4d7db40821056dc759667f9376023
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52871318"
---
# <a name="monitor-and-manage-recovery-services-vaults"></a>Monitorování a správa trezorů služby Recovery Services

Tento článek vysvětluje, jak pomocí trezoru služby Recovery Services **přehled** řídicí panel chcete sledovat a spravovat vaše trezory služby Recovery Services. Když otevřete trezor služby Recovery Services ze seznamu **přehled** otevře se řídicí panel vybraného trezoru. Řídicí panel poskytuje různé podrobnosti o trezoru. Existují *dlaždice* , které zobrazují: stav kritické a upozorňující výstrahy, probíhající a selhání úloh zálohování a množství místně redundantní úložiště (LRS) a geograficky redundantní úložiště (GRS). Pokud zálohujete virtuální počítače Azure do trezoru, [ **stav předběžné kontroly zálohování** dlaždici se zobrazuje všechny položky kritického nebo výstražného](https://azure.microsoft.com/blog/azure-vm-backup-pre-checks/). Na následujícím obrázku je **přehled** řídicí panel pro **Contoso-vault**. **Zálohování položek** dlaždice ukazuje existuje devět položky se zaregistrují do trezoru.

![řídicí panel Recovery services vault](./media/backup-azure-manage-windows-server/rs-vault-blade.png)

Předpoklady pro tento článek je: předplatné Azure, trezor služby Recovery Services a že v něm je nejméně jedné zálohované položky nakonfigurovaný pro trezor.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]


## <a name="open-a-recovery-services-vault"></a>Otevřete trezor služby Recovery Services

Chcete-li monitorování výstrah nebo zobrazení dat správy o trezoru služby Recovery Services, otevřete v trezoru.

1. Přihlaste se k [webu Azure Portal](https://portal.azure.com/) pomocí svého předplatného Azure.

2. Na portálu klikněte na tlačítko **všechny služby**.

   ![Otevřít seznam krok1 trezory služby Recovery Services](./media/backup-azure-manage-windows-server/open-rs-vault-list.png)

3. V **všechny služby** dialogovém okně **služby Recovery Services**. Seznam se průběžně filtruje podle zadávaného textu. Když **trezory služby Recovery Services** možnost se zobrazí, klikněte na něj pro otevření seznamu trezorů služby Recovery Services v rámci vašeho předplatného.

    ![Vytvoření trezoru Recovery Services – krok 1](./media/backup-azure-manage-windows-server/list-of-rs-vaults.png) <br/>

4. V seznamu trezorů, klikněte na trezor otevřete jeho **přehled** řídicího panelu. 

    ![řídicí panel Recovery services vault](./media/backup-azure-manage-windows-server/rs-vault-blade.png) <br/>

    Přehled řídicího panelu používá dlaždice poskytovat upozornění a data úlohy zálohování.

## <a name="monitor-backup-jobs-and-alerts"></a>Výstrahy a monitorování úloh zálohování

Trezor služby Recovery Services **přehled** řídicí panel obsahuje dlaždice pro informace o monitorování a využití. Dlaždice v části zobrazení monitorování kritický a upozorňující výstrahy a v probíhající úlohy a úlohy se nezdařilo. Kliknutím na konkrétní výstraha nebo úloha otevřete nabídku zálohování upozornění nebo úlohy zálohování filtrují pro tuto úlohu nebo výstrahy.

![Úlohy zálohování řídicí panel](./media/backup-azure-manage-windows-server/monitor-dashboard-tiles-warning.png)

V části monitorování zobrazuje výsledky předdefinované **výstrahy zálohování** a **úlohy zálohování** dotazy. Dlaždice monitorování poskytují aktuální informace o:

* Kritická a upozornění výstrahy pro úlohy zálohování (za posledních 24 hodin)
* Stav předběžné kontroly pro virtuální počítače Azure – na stav předběžné kontroly podrobnější informace najdete v článku [blogu zálohování na stav předběžné kontroly zálohování](https://azure.microsoft.com/blog/azure-vm-backup-pre-checks/).
* Úlohy zálohování v průběhu a úlohy, které selhaly (za posledních 24 hodin).

Dlaždice využití poskytují:

* Počet zálohování položek nakonfigurovaných pro trezor.
* Úložiště Azure (oddělené symbolem LRS a GRS) spotřebovává trezor.

Klikněte na dlaždice (s výjimkou úložiště záloh) otevřete příslušné nabídky. Na obrázku výše zobrazí na dlaždici výstrahy zálohování tři kritické výstrahy. Klikněte na řádek kritické výstrahy na dlaždici výstrahy zálohování, otevře se výstrahy zálohování filtrují pro kritické výstrahy.

![Výstrahy záloh nabídky filtrují pro kritické výstrahy](./media/backup-azure-manage-windows-server/critical-backup-alerts.png)

V nabídce výstrahy zálohování, na obrázku výše, se filtrují podle: stav je aktivní, závažnost je důležité a čas je předchozích 24 hodin.

## <a name="manage-backup-alerts"></a>Spravovat výstrahy zálohování

Pro přístup k nabídce výstrahy zálohování v nabídce trezoru služby Recovery Services klikněte na tlačítko **výstrahy zálohování**.

![Výstrahy záloh](./media/backup-azure-manage-windows-server/backup-alerts-menu.png)

Sestava výstrahy zálohování obsahuje výstrahy pro trezor. 

![Výstrahy záloh](./media/backup-azure-manage-windows-server/backup-alerts.png)

### <a name="alerts"></a>Výstrahy

Výstrahy zálohování seznamu zobrazí vybrané informace o filtrované výstrahy. V nabídce Backup, upozornění můžete filtrovat výstrahy kritický nebo upozornění.

| Úroveň výstrahy | Události, které generují výstrahy |
| ----------- | ----------- |
| Kritická | Obdržíte kritické upozornění pro případ: zálohování úlohy převzetí služeb při selhání úloh obnovení a při zastavení ochrany na serveru, ale zachovat data.|
| Upozornění | Zobrazí se upozornění upozornění pro případ: dokončení úloh zálohování s upozorněními, třeba když méně než 100 souborů nejsou zálohovány z důvodu problémů s poškozením, nebo pokud je to více než 1 000 000 soubory úspěšně zazálohovala). |
| Informační | používá se v současné době nejsou žádné informační výstrahy. |

### <a name="viewing-alert-details"></a>Zobrazení podrobností výstrah

Sestava výstrahy zálohování sleduje osm podrobnosti o jednotlivých výstrahách. Použití **zvolit sloupce** tlačítko pro úpravu podrobností v sestavě.

![Výstrahy záloh](./media/backup-azure-manage-windows-server/backup-alerts.png)

Ve výchozím nastavení jsou všechny informace, s výjimkou **čas posledního výskytu**, se zobrazí v sestavě.

* Výstrahy
* Zálohovaná položka
* Chráněný Server
* Severity
* Doba trvání
* Čas vytvoření
* Status
* Čas posledního výskytu

### <a name="change-the-details-in-alerts-report"></a>Změnit podrobnosti v sestavu výstrah

1. Chcete-li změnit informace o sestavy, v **výstrahy zálohování** nabídky, klikněte na tlačítko **zvolit sloupce**.

   ![Výstrahy záloh](./media/backup-azure-manage-windows-server/alerts-menu-choose-columns.png)

   **Zvolit sloupce** otevře se nabídka.

2. V **zvolit sloupce** nabídce zvolte možnost podrobnosti, které se mají zobrazit v sestavě.

    ![Zvolte sloupce nabídky](./media/backup-azure-manage-windows-server/choose-columns-menu.png)

3. Klikněte na tlačítko **provádí** uložte provedené změny a zavřete nabídky zvolte sloupce.

   Je-li provést změny, ale nechcete zachovat změny, klikněte na tlačítko **resetování** vrátit vybrané na poslední uložit konfiguraci.

### <a name="change-the-filter-in-alerts-report"></a>Změňte filtr na sestavu výstrah

Použití **filtr** změnit závažnost, stav, čas zahájení a ukončení pro výstrahy. 

> [!NOTE]
> Úpravy výstrah zálohování filtr nezmění kritický nebo upozornění upozornění v trezoru řídicího panelu s přehledem.
>  

1. Chcete-li změnit filtr výstrahy zálohování, v nabídce výstrahy zálohování klikněte na tlačítko **filtr**.

   ![Zvolte nabídku filtru](./media/backup-azure-manage-windows-server/alerts-menu-choose-filter.png)

   Zobrazí se nabídka filtru.

   ![Zvolte nabídku filtru](./media/backup-azure-manage-windows-server/filter-alert-menu.png)

2. Upravit závažnost, stav, čas začátku nebo čas ukončení a klikněte na tlačítko **provádí** uložte provedené změny.

## <a name="configuring-notifications-for-alerts"></a>Konfigurace oznámení pro výstrahy

Konfigurace oznámení pro generování e-mailů, když dojde k upozornění nebo kritických výstrah. Můžete odesílat e-mailová upozornění každou hodinu nebo když nastane konkrétní výstrahy.

   ![Filtrování výstrah](./media/backup-azure-manage-windows-server/configure-notification.png)

Ve výchozím nastavení, e-mailová oznámení jsou **na**. Klikněte na tlačítko **vypnout** zastavit e-mailová oznámení.

Na **upozornění** ovládací prvek, zvolte **za upozornění** Pokud nechcete, aby seskupení nebo není k dispozici mnoho položek, které by mohla Generovat výstrahy. Každá výstraha výsledkem jedno oznámení (výchozí nastavení), a okamžitě se odešle e-mail s řešení.

Pokud vyberete **hodinový přehled**, odešle e-mail příjemcům s vysvětlením nevyřešené výstrahy vygenerované za poslední hodinu. Navýšení kapacity na konec hodiny se odešle e-mail s řešení.

Vyberte závažnost výstrahy (kritický nebo varování) sloužícího ke generování e-mailu. Aktuálně nejsou žádné informace výstrahy.

## <a name="manage-backup-items"></a>Správa zálohování položek

Trezor služby Recovery Services obsahuje mnoho typů dat záloh. Úplný seznam typů zálohování, naleznete v tématu [které aplikace a úlohy lze zálohovat](backup-introduction-to-azure-backup.md#which-azure-backup-components-should-i-use). Ke správě různých serverů, počítačů, databází a úloh, klikněte na tlačítko **zálohování položek** dlaždici zobrazíte obsah trezoru.

![Zálohované položky dlaždice](./media/backup-azure-manage-windows-server/backup-items.png)

Otevře se seznam zálohovaných položek, uspořádané podle typu správy zálohování.

![seznam zálohovaných položek](./media/backup-azure-manage-windows-server/list-backup-items.png)

Prozkoumat konkrétní typ chráněné instance, klikněte na položku ve sloupci Typ správy zálohování. Například ve výše uvedeném, existují dva virtuální počítače Azure chráněný v trezoru. Kliknutím na **virtuálního počítače Azure**, otevře se seznam chráněných virtuálních počítačů v tomto trezoru.

![seznam typů zálohy](./media/backup-azure-manage-windows-server/list-of-protected-virtual-machines.png)

Seznam virtuálních počítačů má data na užitečné: přidruženou skupinu prostředků, předchozí [předběžné kontroly zálohování](https://azure.microsoft.com/blog/azure-vm-backup-pre-checks/), stav poslední zálohy a data poslední bod obnovení. Na tři tečky v posledním sloupci otevře nabídku aktivovat běžných úloh. Užitečná data zadaná ve sloupcích, se liší pro každý typ zálohování.

![seznam typů zálohy](./media/backup-azure-manage-windows-server/ellipsis-menu.png)

## <a name="manage-backup-jobs"></a>Správa úloh zálohování

**Úlohy zálohování** dlaždici na řídicím panelu trezoru zobrazuje počet úloh, které jsou v průběhu nebo Failed za posledních 24 hodin. Na dlaždici poskytuje balíčku glimpse do nabídky úlohy zálohování.

![Zálohované položky z nastavení](./media/backup-azure-manage-windows-server/backup-jobs-tile.png)

Chcete-li zobrazit další podrobnosti o úlohách, klikněte na tlačítko **probíhá** nebo **neúspěšné** otevřete nabídku zálohování úloh filtrují pro tento stav.

### <a name="backup-jobs-menu"></a>Nabídka úlohy zálohování

**Úlohy zálohování** nabídce se zobrazí informace o položky typu, operace, stav, čas spuštění a doba trvání.  

V nabídce úlohy zálohování v hlavní nabídce trezoru, klikněte na tlačítko **úlohy zálohování**. 

![Zálohované položky z nastavení](./media/backup-azure-manage-windows-server/backup-jobs-menu-item.png)

Otevře se seznam úloh zálohování.

![Zálohované položky z nastavení](./media/backup-azure-manage-windows-server/backup-jobs-list.png)

V nabídce úlohy zálohování se zobrazuje stav pro všechny operace na všechny typy zálohování, za posledních 24 hodin. Použití **filtr** změnit filtry. Filtry jsou vysvětlené v následujících částech.

Chcete-li změnit filtry:

1. V nabídce úlohy zálohování trezoru, klikněte na tlačítko **filtr**.

   ![Zálohované položky z nastavení](./media/backup-azure-manage-windows-server/vault-backup-job-menu-filter.png)

    Otevře se nabídka filtru.

   ![Zálohované položky z nastavení](./media/backup-azure-manage-windows-server/filter-menu-backup-jobs.png)

2. Zvolte nastavení filtru a klikněte na tlačítko **provádí**. Filtrovaný seznam aktualizuje, na základě nového nastavení.

#### <a name="item-type"></a>Typ položky

Typ položky je typ správy zálohování chráněné instance. Existují čtyři typy; viz následující seznam. Můžete zobrazit všechny typy položek nebo jedním typem položky. Nelze vybrat dvě nebo tři typy položek. K dispozici typů položek jsou:

* Všechny typy položek
* Virtuální počítače Azure
* Soubory a složky
* Azure Storage
* Úloha Azure

#### <a name="operation"></a>Operace

Zobrazí se jedné operace nebo všechny operace. Nelze vybrat dvě nebo tři operace. Jsou k dispozici operace:

* Všechny operace
* Registrace
* Konfigurace zálohování
* Backup
* Obnovení
* Zakázat zálohování
* Odstranit data zálohy

#### <a name="status"></a>Status

Můžete zobrazit všechny stavy nebo jeden. Nelze vybrat dvě nebo tři stavy. K dispozici stavy jsou:

* Všechny stavy
* Dokončeno
* Probíhá zpracování
* Selhalo
* Zrušeno
* Dokončeno s upozorněními

#### <a name="start-time"></a>Čas spuštění

Datum a čas, který začíná dotazu. Výchozí hodnota je 24 hodin.

#### <a name="end-time"></a>Koncový čas

Datum a čas ukončení dotazu.

### <a name="export-jobs"></a>Exportovat úlohy

Použití **exportovat úlohy** vytvořit tabulku obsahující všechny informace o nabídce úlohy. Tabulka má jeden seznam, který obsahuje souhrn všech úloh a jednotlivým listům pro každou úlohu.

Export úloh informace do tabulky, klikněte na tlačítko **exportovat úlohy**. Služba vytvoří speadsheet pomocí názvu trezor data a času, ale můžete změnit název.

## <a name="monitor-backup-usage"></a>Monitorovat využití zálohování

Úložiště záloh dlaždice na řídicím panelu zobrazí úložiště spotřebovaného v Azure. Využití úložiště je k dispozici pro:

* Použití úložiště LRS cloudové přidružených k trezoru
* Využití úložiště GRS cloudu přidružených k trezoru


## <a name="frequently-asked-questions"></a>Nejčastější dotazy

**Q1. Jak dlouho trvá stav úlohy agenta Azure backup tak, aby odrážely na portálu?**

Odpověď 1. Na webu Azure portal může trvat až 15 minut tak, aby odrážel stav úlohy agenta Azure backup.

**2. ČTVRTLETÍ. Pokud se úloha zálohování nezdaří, jak dlouho trvá vydat výstrahu?**

Odpověď 2. V rámci 20 minut selhání zálohování Azure je vydána výstraha.

**Q3. Je případ, kdy e-mail neodešle, pokud jsou oznámení nakonfigurovaná?**

Odpověď 3. Ano. V následujících situacích nedocházelo k jejich odesílání.

* Pokud jsou oznámení nakonfigurovaná každou hodinu a výstraha se vyvolá a vyřeší během hodiny.
* Když se zruší úlohu
* Pokud se druhá úloha zálohování se nezdaří, protože probíhá původní úlohy zálohování

## <a name="troubleshooting-monitoring-issues"></a>Řešení potíží s problémy s monitorováním

**Problém:** úlohy nebo výstrahy z agenta Azure Backup se nezobrazují na portálu.

**Řešení potíží:** procesu ```OBRecoveryServicesManagementAgent```, odešle data úlohy a výstrahy ve službě Azure Backup. Čas od času může tento proces zablokovány nebo vypnutí.

1. Chcete-li ověřit, proces není spuštěn, otevřete **Správce úloh**a zkontrolujte ```OBRecoveryServicesManagementAgent``` běží.

2. Pokud není spuštěný proces, otevřete **ovládací panely**a vyhledejte v seznamu služeb. Spusťte nebo restartujte **agenta Microsoft Azure Recovery Services Management**.

    Další informace vyhledejte protokoly v:<br/>
   `<AzureBackup_agent_install_folder>\Microsoft Azure Recovery Services Agent\Temp\GatewayProvider*` Příklad:<br/>
   `C:\Program Files\Microsoft Azure Recovery Services Agent\Temp\GatewayProvider0.errlog`

## <a name="next-steps"></a>Další postup
* [Obnovení systému Windows Server nebo klienta Windows z Azure](backup-azure-restore-windows-server.md)
* Další informace o službě Azure Backup najdete v tématu [Přehled služby Azure Backup](backup-introduction-to-azure-backup.md)
* Přejděte [fórum Azure Backup](https://go.microsoft.com/fwlink/p/?LinkId=290933)
