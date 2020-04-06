---
title: Obnovení virtuálních počítačů pomocí portálu Azure
description: Obnovení virtuálního počítače Azure z bodu obnovení pomocí webu Azure Portal
ms.reviewer: geg
ms.topic: conceptual
ms.date: 09/17/2019
ms.openlocfilehash: 4bb0a07db39f5f9953a1e41e55b8be28fcace71b
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668818"
---
# <a name="how-to-restore-azure-vm-data-in-azure-portal"></a>Jak obnovit data virtuálních virtuálních počítače Azure na webu Azure Portal

Tento článek popisuje, jak obnovit data virtuálních zařízení Azure z bodů obnovení uložených v trezorech služby [Azure Backup](backup-overview.md) Recovery Services.

## <a name="restore-options"></a>Možnosti obnovení

Azure Backup poskytuje řadu způsobů, jak obnovit virtuální počítač.

**Možnost obnovení** | **Podrobnosti**
--- | ---
**Vytvořte nový virtuální počítač.** | Rychle vytvoří a získá základní virtuální hod z provozu z bodu obnovení.<br/><br/> Můžete zadat název virtuálního počítače, vybrat skupinu prostředků a virtuální síť (VNet), do které bude umístěn, a určit účet úložiště pro obnovený virtuální počítač. Nový virtuální virtuální město musí být vytvořen ve stejné oblasti jako zdrojový virtuální hod.
**Obnovit disk** | Obnoví disk virtuálního počítače, který pak můžete použít k vytvoření nového virtuálního počítače.<br/><br/> Azure Backup poskytuje šablonu, která vám pomůže přizpůsobit a vytvořit virtuální počítač. <br/><br> Úloha obnovení generuje šablonu, kterou můžete stáhnout a použít k určení vlastního nastavení virtuálního počítače a k vytvoření virtuálního počítače.<br/><br/> Disky se zkopírují do zadané skupiny prostředků.<br/><br/> Případně můžete připojit disk k existujícímu virtuálnímu počítače nebo vytvořit nový virtuální počítače pomocí Prostředí PowerShell.<br/><br/> Tato možnost je užitečná, pokud chcete přizpůsobit virtuální hod, přidat nastavení konfigurace, které nebyly v době zálohování, nebo přidat nastavení, která musí být nakonfigurována pomocí šablony nebo prostředí PowerShell.
**Nahradit existujícího** | Disk můžete obnovit a použít ho k nahrazení disku na existujícím virtuálním počítači.<br/><br/> Aktuální virtuální virtuální byl konto. Pokud byla odstraněna, nelze tuto možnost použít.<br/><br/> Azure Backup pořídí snímek existujícího virtuálního počítače před nahrazením disku a uloží ho do určeného pracovního umístění. Existující disky připojené k virtuálnímu počítače se nahradí vybraným bodem obnovení.<br/><br/> Snímek je zkopírován do trezoru a zachován v souladu se zásadami uchovávání informací. <br/><br/> Po operaci nahradit disk je původní disk zachován ve skupině prostředků. Pokud nejsou potřebné, můžete je odstranit ručně. <br/><br/>Nahradit stávající je podporované pro nešifrované spravované virtuální chody. Není podporována pro nespravované disky, [generalizované virtuální počítače](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource)nebo pro virtuální počítače [vytvořené pomocí vlastních irek](https://azure.microsoft.com/resources/videos/create-a-custom-virtual-machine-image-in-azure-resource-manager-with-powershell/).<br/><br/> Pokud bod obnovení má více nebo méně disků než aktuální virtuální počítače, pak počet disků v bodě obnovení bude odrážet pouze konfiguraci virtuálního počítače.<br><br> Nahradit existující není podporována pro virtuální počítače s propojenými prostředky (jako [je uživatelem přiřazená spravovaná identita](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) nebo [trezor klíčů),](https://docs.microsoft.com/azure/key-vault/key-vault-overview)protože zálohovací klientská aplikace nemá oprávnění k těmto prostředkům při provádění obnovení.<br/><br/>Klasické virtuální počítače se nepodporují.
**Křížová oblast (sekundární oblast)** | Obnovení mezi oblastmi se dá použít k obnovení virtuálních počítačů Azure v sekundární oblasti, což je [spárovaná oblast Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions#what-are-paired-regions).<br><br> Můžete obnovit všechny virtuální počítače Azure pro vybraný bod obnovení, pokud se zálohování provádí v sekundární oblasti.<br><br> Tato funkce je k dispozici pro následující možnosti:<br> * [Vytvoření virtuálního virtuálního mísa](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#create-a-vm) <br> * [Obnovit disky](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#restore-disks) <br><br> V současné době nepodporujeme možnost [Nahradit existující disky.](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#replace-existing-disks)<br><br> Oprávnění<br> Operaci obnovení v sekundární oblasti mohou provádět správci zálohování a správci aplikací.

> [!NOTE]
> Můžete také obnovit konkrétní soubory a složky na virtuálním počítači Azure. [Další informace](backup-azure-restore-files-from-vm.md).
>
> Pokud používáte [nejnovější verzi](backup-instant-restore-capability.md) Azure Backup pro virtuální počítače Azure (označované jako Okamžité obnovení), snímky se uchovávají až sedm dní a virtuální počítač můžete obnovit ze snímků před odesláním dat zálohy do trezoru. Pokud chcete obnovit virtuální ho virtuálního počítače ze zálohy z posledních sedmi dnů, je rychlejší obnovit ze snímku a ne z trezoru.

## <a name="storage-accounts"></a>Účty úložiště

Některé podrobnosti o účtech úložiště:

- **Vytvořit virtuální ho:** Když vytvoříte nový virtuální účet, virtuální ho disponuje do zadanýho účtu úložiště.
- **Obnovení disku**: Při obnovení disku je disk zkopírován do zadaného účtu úložiště. Úloha obnovení generuje šablonu, kterou můžete stáhnout a použít k určení vlastního nastavení virtuálního počítače. Tato šablona je umístěna v zadaném účtu úložiště.
- **Nahradit disk**: Při nahrazení disku na existujícím virtuálním počítači, Azure Backup pořídí snímek existujícího virtuálního počítače před výměnou disku. Snímek je uložen v pracovní umístění (účet úložiště), který zadáte. Tento účet úložiště se používá k dočasnému uložení snímku během procesu obnovení a doporučujeme vytvořit nový účet k tomu, které lze snadno odstranit později.
- **Umístění účtu úložiště**: Účet úložiště musí být ve stejné oblasti jako úschovna. Zobrazí se pouze tyto účty. Pokud v umístění nejsou žádné účty úložiště, musíte je vytvořit.
- **Typ úložiště**: Úložiště objektů blob není podporované.
- **Redundance úložiště:** Zónově redundantní úložiště (ZRS) není podporováno. Informace o replikaci a redundanci účtu jsou zobrazeny v závorce za názvem účtu.
- **Prémiové skladování**:
  - Při obnovení neprémiových virtuálních počítačů nejsou podporované účty úložiště premium.
  - Při obnovování spravovaných virtuálních počítačů nejsou podporované účty úložiště premium nakonfigurované pomocí síťových pravidel.

## <a name="before-you-start"></a>Než začnete

Chcete-li obnovit virtuální hovirtuální ho (vytvořit nový virtuální hod), ujistěte se, že máte správná [oprávnění](backup-rbac-rs-vault.md#mapping-backup-built-in-roles-to-backup-management-actions) řízení přístupu na základě rolí (RBAC) pro operaci obnovení virtuálního zařízení.

Pokud nemáte oprávnění, můžete [obnovit disk](#restore-disks)a po obnovení disku můžete použít [šablonu,](#use-templates-to-customize-a-restored-vm) která byla vygenerována jako součást operace obnovení, k vytvoření nového virtuálního počítače.

## <a name="select-a-restore-point"></a>Výběr bodu obnovení

1. V trezoru přidruženém k virtuálnímu počítači, který chcete obnovit, klikněte na **Zálohovat položky** > **Virtuální počítač Azure**.
2. Klikněte na virtuální hod. Ve výchozím nastavení na řídicím panelu virtuálního počítači se zobrazí body obnovení za posledních 30 dní. Můžete zobrazit body obnovení starší než 30 dní nebo filtrovat a vyhledat body obnovení na základě dat, časových rozsahů a různých typů konzistence snímků.
3. Pokud chcete virtuální ho obnovit, klikněte na **Obnovit virtuální hod**.

    ![Bod obnovení](./media/backup-azure-arm-restore-vms/restore-point.png)

4. Vyberte bod obnovení, který chcete použít pro obnovení.

## <a name="choose-a-vm-restore-configuration"></a>Volba konfigurace obnovení virtuálního počítače

1. V **konfiguraci Obnovení**vyberte možnost obnovení:
    - **Vytvořit nový**: Tuto možnost použijte, pokud chcete vytvořit nový virtuální virtuální hod. Můžete vytvořit virtuální hospodařících s jednoduchým nastavením nebo obnovit disk a vytvořit vlastní virtuální počítače.
    - **Nahradit existující**: Tuto možnost použijte, pokud chcete nahradit disky na existujícím virtuálním počítači.

        ![Průvodce obnovením konfigurace](./media/backup-azure-arm-restore-vms/restore-configuration.png)

2. Určete nastavení vybrané možnosti obnovení.

## <a name="create-a-vm"></a>Vytvoření virtuálního počítače

Jako jednu z [možností obnovení](#restore-options)můžete rychle vytvořit virtuální hod se základním nastavením z bodu obnovení.

1. V **konfiguraci** > Obnovení**Vytvořit nový** > **typ obnovení**vyberte Vytvořit **virtuální počítač**.
2. V **názvu virtuálního počítače**zadejte virtuální počítač, který v předplatném neexistuje.
3. Ve **skupině prostředků**vyberte existující skupinu prostředků pro nový virtuální virtuální soud nebo vytvořte novou s globálně jedinečným názvem. Pokud přiřadíte název, který už existuje, Azure přiřadí skupině stejný název jako virtuální počítač.
4. Ve **virtuální síti**vyberte virtuální síť, do které bude virtuální počítač umístěn. Zobrazí se všechny virtuální sítě přidružené k předplatnému. Vyberte podsíť. Ve výchozím nastavení je vybrána první podsíť.
5. V **umístění úložiště**zadejte účet úložiště pro virtuální hod. [Další informace](#storage-accounts).

    ![Průvodce obnovením konfigurace](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard1.png)

6. V **konfiguraci Obnovení**vyberte **možnost OK**. V **části Obnovení**klepněte na tlačítko **Obnovit** a aktivujte operaci obnovení.

## <a name="restore-disks"></a>Obnovení disků

Jako jednu z [možností obnovení](#restore-options)můžete vytvořit disk z bodu obnovení. Pak s diskem můžete provést jednu z následujících akcí:

- Pomocí šablony, která je generována během operace obnovení přizpůsobit nastavení a aktivovat nasazení virtuálního počítače. Upravíte výchozí nastavení šablony a odešlete šablonu pro nasazení virtuálního počítače.
- [Připojte obnovené disky](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal) k existujícímu virtuálnímu počítače.
- [Vytvořte nový virtuální počítače](https://docs.microsoft.com/azure/backup/backup-azure-vms-automation#create-a-vm-from-restored-disks) z obnovených disků pomocí Prostředí PowerShell.

1. V **části Obnovit konfiguraci** > **Vytvořit nový** > **typ obnovení**vyberte možnost **Obnovit disky**.
2. Ve **skupině prostředků**vyberte existující skupinu prostředků pro obnovené disky nebo vytvořte novou skupinu s globálně jedinečným názvem.
3. V **účtu úložiště**zadejte účet, do kterého chcete zkopírovat virtuální diody. [Další informace](#storage-accounts).

    ![Konfigurace obnovení byla dokončena.](./media/backup-azure-arm-restore-vms/trigger-restore-operation1.png)

4. V **konfiguraci Obnovení**vyberte **možnost OK**. V **části Obnovení**klepněte na tlačítko **Obnovit** a aktivujte operaci obnovení.

Když váš virtuální počítač používá spravované disky a vyberete možnost **Vytvořit virtuální počítač,** Azure Backup nepoužívá zadaný účet úložiště. V případě **obnovení disků** a **okamžité obnovení**se účet úložiště používá pouze pro ukládání šablony. Spravované disky jsou vytvořeny v zadané skupině prostředků.
Když váš virtuální počítač používá nespravované disky, jsou obnoveny jako objekty BLOB do účtu úložiště.

### <a name="use-templates-to-customize-a-restored-vm"></a>Přizpůsobení obnoveného virtuálního počítače pomocí šablon

Po obnovení disku použijte šablonu, která byla vygenerována jako součást operace obnovení, a přizpůsobte a vytvořte nový virtuální počítače:

1. Otevřete **podrobnosti úlohy obnovení** pro příslušnou úlohu.

2. V **části Restore Job Details**vyberte Deploy **Template,** chcete-li zahájit nasazení šablony.

    ![Obnovení podrobností o úloze](./media/backup-azure-arm-restore-vms/restore-job-drill-down1.png)

3. Pokud chcete přizpůsobit nastavení virtuálního počítače uvedené v šabloně, klikněte na **Upravit šablonu**. Pokud chcete přidat další vlastní nastavení, klikněte na **Upravit parametry**.
    - [Přečtěte si další informace](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template) o nasazení prostředků z vlastní šablony.
    - [Přečtěte si další informace](../azure-resource-manager/templates/template-syntax.md) o vytváření šablon.

   ![Načíst nasazení šablony](./media/backup-azure-arm-restore-vms/edit-template1.png)

4. Zadejte vlastní hodnoty pro virtuální hod, přijměte **smluvní podmínky** a klikněte na **Koupit**.

   ![Odeslat nasazení šablony](./media/backup-azure-arm-restore-vms/submitting-template1.png)

## <a name="replace-existing-disks"></a>Nahrazení existujících disků

Jako jednu z [možností obnovení](#restore-options)můžete nahradit existující disk virtuálního počítače vybraným bodem obnovení. [Zkontrolujte](#restore-options) všechny možnosti obnovení.

1. V **konfiguraci Obnovení**klepněte na **tlačítko Nahradit existující**.
2. V **části Obnovit typ**vyberte nahradit **disk/s**. Toto je bod obnovení, který se použije nahradit existující disky virtuálního počítače.
3. V **pracovní umístění**určete, kde mají být během procesu obnovení uloženy snímky aktuálních spravovaných disků. [Další informace](#storage-accounts).

   ![Průvodce obnovením konfigurace nahradit existující](./media/backup-azure-arm-restore-vms/restore-configuration-replace-existing.png)

## <a name="cross-region-restore"></a>Obnovení oblasti kříže

Jako jednu z [možností obnovení](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#restore-options)umožňuje obnovení mezi oblastmi (CRR) obnovení virtuálních počítačích Azure v sekundární oblasti, což je spárovaná oblast Azure.

Chcete-li se k funkci při náhledu nalodit, přečtěte si [oddíl Před zahájením](https://docs.microsoft.com/azure/backup/backup-create-rs-vault#set-cross-region-restore).

Chcete-li zjistit, zda je funkce CRR povolena, postupujte podle pokynů v části [Konfigurovat obnovení mezi oblastmi.](backup-create-rs-vault.md#configure-cross-region-restore)

### <a name="view-backup-items-in-secondary-region"></a>Zobrazit položky záloh v sekundární oblasti

Pokud je povoleno crr, můžete zobrazit položky zálohy v sekundární oblasti.

1. Na portálu přejděte do **trezoru služby Recovery Services** > **Položky zálohování**
2. Kliknutím na **Sekundární oblast** zobrazíte položky v sekundární oblasti.

![Virtuální počítače v sekundární oblasti](./media/backup-azure-arm-restore-vms/secbackedupitem.png)

![Vybrat sekundární oblast](./media/backup-azure-arm-restore-vms/backupitems-sec.png)

### <a name="restore-in-secondary-region"></a>Obnovení v sekundární oblasti

Uživatelské prostředí obnovení sekundární oblasti bude podobné uživatelskému prostředí pro obnovení primární oblasti. Při konfiguraci podrobností v okně Obnovit konfiguraci pro konfiguraci obnovení budete vyzváni k zadání pouze sekundárních parametrů oblasti.

![Zvolte virtuální ms, který chcete obnovit.](./media/backup-azure-arm-restore-vms/sec-restore.png)

![Vybrat bod obnovení](./media/backup-azure-arm-restore-vms/sec-rp.png)

![Obnovit konfiguraci](./media/backup-azure-arm-restore-vms/rest-config.png)

![Oznámení o obnovení aktivace](./media/backup-azure-arm-restore-vms/restorenotifications.png)

- Pokud chcete obnovit a vytvořit virtuální hod, přečtěte si informace [o vytvoření virtuálního virtuálního virtuálního montovny](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#create-a-vm).
- Chcete-li jej obnovit jako disk, přečtěte si informace [o nástroji Obnovení disků](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#restore-disks).

>[!NOTE]
>Po aktivaci obnovení a ve fázi přenosu dat nelze úlohu obnovení zrušit.

### <a name="monitoring-secondary-region-restore-jobs"></a>Sledování úloh obnovení sekundární oblasti

1. Z portálu přejděte na **obnovení služby vault** > **zálohování úlohy**
2. Kliknutím na **Sekundární oblast** zobrazíte položky v sekundární oblasti.

![Filtrované úlohy zálohování](./media/backup-azure-arm-restore-vms/secbackupjobs.png)

## <a name="restore-vms-with-special-configurations"></a>Obnovení virtuálních počítačů pomocí speciálních konfigurací

Existuje celá řada běžných scénářů, ve kterých budete muset obnovit virtuální chod.

**Scénář** | **Doprovodné materiály**
--- | ---
**Obnovení virtuálních disponecí pomocí výhody hybridního použití** | Pokud virtuální počítače s Windows používají [hybridní licenci pro výhody použití (HUB),](../virtual-machines/windows/hybrid-use-benefit-licensing.md)obnovte disky a vytvořte nový virtuální virtuální počítače pomocí zadané šablony (s **typem licence** nastaveným na **Windows_Server**) nebo PowerShellem.  Toto nastavení lze také použít po vytvoření virtuálního virtuálního mísy.
**Obnovení virtuálních počítačů během havárie datového centra Azure** | Pokud trezor používá GRS a primární datové centrum pro virtuální počítač přejde, Azure Backup podporuje obnovení zálohovaných virtuálních počítačů do spárovaného datového centra. Vspárovaném datovém centru vyberete účet úložiště a obnovíte je jako obvykle. Azure Backup používá výpočetní službu ve spárované oblasti k vytvoření obnoveného virtuálního počítače. [Přečtěte si další informace](../resiliency/resiliency-technical-guidance-recovery-loss-azure-region.md) o odolnosti datového centra.
**Obnovení virtuálního virtuálního zařízení s jedním řadičem domény v jedné doméně** | Obnovte virtuální ho virtuálního soudu jako každý jiný virtuální virtuální ms. Poznámky:<br/><br/> Z hlediska služby Active Directory je virtuální počítač Azure jako každý jiný virtuální počítač.<br/><br/> K dispozici je také režim obnovení adresářových služeb (DSRM), takže jsou životaschopné všechny scénáře obnovení služby Active Directory. [Přečtěte si další informace](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#post-restore-steps) o aspektech zálohování a obnovení virtualizovaných řadičů domény.
**Obnovení více virtuálních počítačů řadiče domény v jedné doméně** | Pokud jsou v síti dostupné jiné řadiče domény ve stejné doméně, lze řadič domény obnovit jako jakýkoli virtuální virtuální ms. Pokud se jedná o poslední zbývající řadič domény v doméně nebo je provedena obnova v izolované síti, použijte [obnovení doménové struktury](https://docs.microsoft.com/windows-server/identity/ad-ds/manage/ad-forest-recovery-single-domain-in-multidomain-recovery).
**Obnovení více domén v jedné doménové struktuře** | Doporučujeme [obnovu lesa](https://docs.microsoft.com/windows-server/identity/ad-ds/manage/ad-forest-recovery-single-domain-in-multidomain-recovery).
**Obnova holého kovu** | Hlavní rozdíl mezi virtuálními počítači Azure a místními hypervisory spoáčy na tom, že v Azure není dostupná žádná konzola virtuálních zařízení. Konzola je vyžadována pro určité scénáře, jako je například obnovení pomocí zálohy typu úplné obnovení (BMR). Obnovení virtuálního média z trezoru je však úplnou náhradou za BMR.
**Obnovení virtuálních počítačů pomocí speciálních síťových konfigurací** | Speciální konfigurace sítě zahrnují virtuální počítače využívající interní nebo externí vyrovnávání zatížení, použití více síťových síťových síťových síťových adres nebo více vyhrazených adres IP. Tyto virtuální počítače můžete obnovit pomocí [možnosti obnovení disku](#restore-disks). Tato možnost vytvoří kopii virtuálních discích do zadaného účtu úložiště a pak můžete vytvořit virtuální počítač s [interním](https://azure.microsoft.com/documentation/articles/load-balancer-internal-getstarted/) nebo [externím](/azure/load-balancer/quickstart-create-standard-load-balancer-powershell) nástrojem pro vyrovnávání zatížení, [více nics](../virtual-machines/windows/multiple-nics.md)nebo [více vyhrazených IP adres](../virtual-network/virtual-network-multiple-ip-addresses-powershell.md), v souladu s vaší konfigurací.
**Skupina zabezpečení sítě (NSG) v síťové podsíti** | Zálohování virtuálních počítačích Azure podporuje zálohování a obnovení informací o síťovém zabezpečení na úrovni sítě, podsítě a síťové sítě.
**Připnuté virtuální jevy zóny** | Azure Backup podporuje zálohování a obnovení zónových vázaných virtuálních počítačích. [Další informace](https://azure.microsoft.com/global-infrastructure/availability-zones/)

## <a name="track-the-restore-operation"></a>Sledování operace obnovení

Po spuštění operace obnovení vytvoří služba zálohování úlohu pro sledování. Azure Backup zobrazuje oznámení o úloze na portálu. Pokud nejsou viditelné, vyberte symbol **Oznámení** a pak vyberte **Zobrazit všechny úlohy,** abyste viděli stav obnovení procesu.

![Obnovení se spustilo.](./media/backup-azure-arm-restore-vms/restore-notification1.png)

 Sledování obnovení takto:

1. Chcete-li zobrazit operace pro úlohu, klikněte na hypertextový odkaz oznámení. Případně v úschovně klikněte na **Zálohovat úlohy**a potom klikněte na příslušný virtuální účet.

    ![Seznam virtuálních klíčů v trezoru](./media/backup-azure-arm-restore-vms/restore-job-in-progress1.png)

2. Chcete-li sledovat průběh obnovení, klepněte na libovolnou úlohu obnovení se stavem **Probíhající**. Zobrazí se indikátor průběhu, který zobrazuje informace o průběhu obnovení:

    - **Odhadovaný čas obnovení**: Zpočátku poskytuje čas, který je třeba provést operaci obnovení. Jak operace postupuje, doba doby, která byla přijata, se po dokončení operace obnovení zkracuje a dosáhne nuly.
    - **Procento obnovení**. Zobrazuje procento provedené operace obnovení.
    - **Počet přenesených bajtů**: Pokud obnovujete vytvořením nového virtuálního účtu, zobrazí bajty, které byly převedeny proti celkovému počtu bajtů, které mají být převedeny.

## <a name="post-restore-steps"></a>Kroky po obnovení

Po obnovení virtuálního virtuálního účtu je třeba poznamenat několik věcí:

- Rozšíření přítomná během konfigurace zálohování jsou nainstalována, ale nejsou povolena. Pokud se problém zobrazí, přeinstalujte rozšíření.
- Pokud zálohovaný virtuální virtuální byl statický IP adresa, obnovený virtuální byl mít dynamickou IP adresu, aby se zabránilo konfliktu. Do [obnoveného virtuálního počítačů](https://docs.microsoft.com/powershell/module/az.network/set-aznetworkinterfaceipconfig?view=azps-3.5.0#description)můžete přidat statickou ADRESU IP .
- Obnovený virtuální virtuální ms nemá nastavenou dostupnost. Pokud použijete možnost obnovení disku, můžete [určit sadu dostupnosti](../virtual-machines/windows/tutorial-availability-sets.md) při vytváření virtuálního počítače z disku pomocí zadané šablony nebo prostředí PowerShell.
- Pokud používáte linuxovou distribuci založenou na cloud-init, jako je Ubuntu, z bezpečnostních důvodů je heslo po obnovení zablokováno. K obnovení hesla použijte rozšíření VMAccess na obnoveném virtuálním [počítači](../virtual-machines/linux/reset-password.md). Doporučujeme používat klíče SSH na těchto distribucích, takže po obnovení není nutné resetovat heslo.
- Pokud se vám nedaří získat přístup k virtuálnímu virtuálnímu mněmu po obnovení z důvodu, že virtuální ms přerušil vztah s řadičem domény, postupujte podle následujících kroků a virtuální ms zobrazíte:
  - Připojte disk operačního systému jako datový disk k obnovenému virtuálnímu počítače.
  - Ručně nainstalujte agenta virtuálního počítače, pokud je zjištěno, že Azure Agent neodpovídá pomocí tohoto [odkazu](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/install-vm-agent-offline).
  - Povolení přístupu konzoly Serial Console na virtuálním počítači umožňuje přístup příkazového řádku k virtuálnímu počítači

  ```cmd
    bcdedit /store <drive letter>:\boot\bcd /enum
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} displaybootmenu yes
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} timeout 5
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} bootems yes
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<<BOOT LOADER IDENTIFIER>>} ON
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
    ```

  - Když je virtuální počítač znovu sestaven, použijte portál Azure k resetování účtu místního správce a hesla
  - Použití přístupu konzoly Serial console a CMD k odpojovacímu virtuálnímu virtuálnímu míse od domény

    ```cmd
    cmd /c "netdom remove <<MachineName>> /domain:<<DomainName>> /userD:<<DomainAdminhere>> /passwordD:<<PasswordHere>> /reboot:10 /Force"
    ```

- Jakmile virtuální ho disjoined a restartován, budete moct úspěšně RDP na virtuální ms s pověřením místní správy a znovu virtuálního virtuálního mísu zpět do domény úspěšně.

## <a name="backing-up-restored-vms"></a>Zálohování obnovených virtuálních mích

- Pokud jste obnovili virtuální počítač do stejné skupiny prostředků se stejným názvem jako původně zálohovaný virtuální počítač, zálohování pokračuje na virtuálním počítači po obnovení.
- Pokud jste obnovili virtuální ho na jinou skupinu prostředků nebo jste pro obnovený virtuální virtuální účet zadali jiný název, je potřeba nastavit zálohu pro obnovený virtuální účet.

## <a name="next-steps"></a>Další kroky

- Pokud se během procesu obnovení setkáte s obtížemi, [zkontrolujte](backup-azure-vms-troubleshoot.md#restore) běžné problémy a chyby.
- Po obnovení virtuálního počítače se dozvíte o [správě virtuálních počítačů.](backup-azure-manage-vms.md)
