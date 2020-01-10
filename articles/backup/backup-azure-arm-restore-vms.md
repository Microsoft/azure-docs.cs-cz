---
title: Obnovení virtuálních počítačů pomocí Azure Portal
description: Obnovení virtuálního počítače Azure z bodu obnovení pomocí Azure Portal
ms.reviewer: geg
ms.topic: conceptual
ms.date: 09/17/2019
ms.openlocfilehash: 98101639d82ede2a6c625ea9da413bcf93f6a185
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2020
ms.locfileid: "75753931"
---
# <a name="how-to-restore-azure-vm-data-in-azure-portal"></a>Postup obnovení dat virtuálního počítače Azure v Azure Portal

Tento článek popisuje, jak obnovit data virtuálních počítačů Azure z bodů obnovení uložených v [Azure Backup](backup-overview.md) Recovery Services trezory.

## <a name="restore-options"></a>Možnosti obnovení

Azure Backup poskytuje několik způsobů, jak obnovit virtuální počítač.

**Možnost obnovení** | **Podrobnosti**
--- | ---
**Vytvořit nový virtuální počítač** | Rychle vytvoří a načte základní virtuální počítač v bodu obnovení a spustí ho.<br/><br/> Můžete zadat název virtuálního počítače, vybrat skupinu prostředků a virtuální síť (VNet), do které se bude umístit, a zadat účet úložiště pro obnovený virtuální počítač. Nový virtuální počítač musí být vytvořený ve stejné oblasti jako zdrojový virtuální počítač.
**Obnovit disk** | Obnoví disk virtuálního počítače, který se pak dá použít k vytvoření nového virtuálního počítače.<br/><br/> Azure Backup poskytuje šablonu, která vám umožní přizpůsobit a vytvořit virtuální počítač. <br/><br> Úloha obnovení vygeneruje šablonu, kterou si můžete stáhnout a použít k určení vlastního nastavení virtuálního počítače, a vytvořit virtuální počítač.<br/><br/> Disky se zkopírují do skupiny prostředků, kterou zadáte.<br/><br/> Případně můžete disk připojit k existujícímu virtuálnímu počítači nebo vytvořit nový virtuální počítač pomocí prostředí PowerShell.<br/><br/> Tato možnost je užitečná, pokud chcete virtuální počítač přizpůsobit, přidat nastavení konfigurace, která se v době zálohování nevyskytla, nebo přidat nastavení, která se musí nakonfigurovat pomocí šablony nebo PowerShellu.
**Nahradit existujícího** | Disk můžete obnovit a použít ho k nahrazení disku na stávajícím virtuálním počítači.<br/><br/> Aktuální virtuální počítač musí existovat. Pokud je tato možnost odstraněna, nelze ji použít.<br/><br/> Před nahrazením disku Azure Backup pořizuje stávající virtuální počítač a uloží ho do pracovního umístění, které zadáte. Existující disky připojené k virtuálnímu počítači se nahradily vybraným bodem obnovení.<br/><br/> Snímek se zkopíruje do trezoru a zachová se podle zásad uchovávání informací. <br/><br/> Po operaci nahradit disk se původní disk zachová ve skupině prostředků. Pokud nepotřebujete, můžete původní disky odstranit ručně. <br/><br/>Nahradit existující se podporuje pro nešifrované spravované virtuální počítače. U nespravovaných disků, [zobecněných virtuálních počítačů](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource)nebo u virtuálních počítačů [vytvořených pomocí vlastních imagí](https://azure.microsoft.com/resources/videos/create-a-custom-virtual-machine-image-in-azure-resource-manager-with-powershell/)se nepodporuje.<br/><br/> Pokud má bod obnovení více nebo méně disků než aktuální virtuální počítač, bude počet disků v bodu obnovení odpovídat pouze konfiguraci virtuálního počítače.
**Mezi oblastmi (sekundární oblast)** | Obnovení mezi oblastmi se dá použít k obnovení virtuálních počítačů Azure v sekundární oblasti, která je [spárované v oblasti Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions#what-are-paired-regions).<br><br> Pokud se zálohování provádí v sekundární oblasti, můžete obnovit všechny virtuální počítače Azure pro vybraný bod obnovení.<br><br> Tato funkce je k dispozici pro následující možnosti:<br> * [Vytvoření virtuálního počítače](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#create-a-vm) <br> * [obnovení disků](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#restore-disks) <br><br> Momentálně nepodporujeme možnost [nahradit existující disky](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#replace-existing-disks) .<br><br> Oprávnění<br> Operaci obnovení v sekundární oblasti můžou provádět správci zálohování a správci aplikací.

> [!NOTE]
> Můžete také obnovit konkrétní soubory a složky na virtuálním počítači Azure. [Další informace](backup-azure-restore-files-from-vm.md).
>
> Pokud používáte [nejnovější verzi](backup-instant-restore-capability.md) Azure Backup pro virtuální počítače Azure (označované jako okamžité obnovení), snímky se uchovávají po dobu až sedmi dnů a můžete obnovit virtuální počítač ze snímků před odesláním dat zálohy do trezoru. Pokud chcete obnovit virtuální počítač ze zálohy za posledních 7 dní, je rychlejší ho obnovit ze snímku a nikoli z trezoru.

## <a name="storage-accounts"></a>Účty úložiště

Některé podrobnosti o účtech úložiště:

- **Vytvoření virtuálního**počítače: Když vytváříte nový virtuální počítač, virtuální počítač se umístí do zadaného účtu úložiště.
- **Obnovit disk**: Když obnovíte disk, disk se zkopíruje do zadaného účtu úložiště. Úloha obnovení vygeneruje šablonu, kterou můžete stáhnout a použít k určení vlastního nastavení virtuálního počítače. Tato šablona je umístěná v zadaném účtu úložiště.
- **Výměna disku**: když nahradíte disk na EXISTUJÍCÍm virtuálním počítači, Azure Backup před nahrazením disku vybere snímek existujícího virtuálního počítače. Snímek je uložený v pracovním umístění (účet úložiště), které zadáte. Tento účet úložiště se používá k dočasnému uložení snímku během procesu obnovení a doporučujeme vám vytvořit nový účet, který pak bude možné snadno odebrat.
- **Umístění účtu úložiště**: účet úložiště musí být ve stejné oblasti jako trezor. Zobrazí se pouze tyto účty. Pokud v tomto umístění nejsou žádné účty úložiště, budete ho muset vytvořit.
- **Typ úložiště**: BLOB Storage není podporovaný.
- **Redundance úložiště**: zóna redundantní úložiště (ZRS) se nepodporuje. Informace o replikaci a redundanci účtu se zobrazí v závorkách za názvem účtu.
- **Premium Storage**:
  - Při obnovování virtuálních počítačů, které nejsou Premium, se nepodporuje účty úložiště úrovně Premium.
  - Při obnovování spravovaných virtuálních počítačů se účty Premium Storage nakonfigurované pomocí pravidel sítě nepodporují.

## <a name="before-you-start"></a>Než začnete

Pokud chcete obnovit virtuální počítač (vytvořte nový virtuální počítač), ujistěte se, že máte správná [oprávnění](backup-rbac-rs-vault.md#mapping-backup-built-in-roles-to-backup-management-actions) řízení přístupu na základě role (RBAC) pro operaci obnovení virtuálního počítače.

Pokud nemáte oprávnění, můžete [disk obnovit](#restore-disks)a po obnovení disku můžete [použít šablonu](#use-templates-to-customize-a-restored-vm) , která byla vygenerována jako součást operace obnovení, a vytvořit tak nový virtuální počítač.

## <a name="select-a-restore-point"></a>Vybrat bod obnovení

1. V trezoru přidruženého k virtuálnímu počítači, který chcete obnovit, klikněte na položku **zálohované položky** > **virtuální počítač Azure**.
2. Klikněte na virtuální počítač. Ve výchozím nastavení se na řídicím panelu virtuálních počítačů zobrazí body obnovení za posledních 30 dní. Můžete zobrazit body obnovení starší než 30 dní nebo filtrovat a vyhledat body obnovení na základě dat, časových rozsahů a různých typů konzistence snímků.
3. Pokud chcete virtuální počítač obnovit, klikněte na **obnovit virtuální počítač**.

    ![Bod obnovení](./media/backup-azure-arm-restore-vms/restore-point.png)

4. Vyberte bod obnovení, který se má použít pro obnovení.

## <a name="choose-a-vm-restore-configuration"></a>Zvolit konfiguraci obnovení virtuálního počítače

1. V možnosti **obnovit konfiguraci**vyberte možnost obnovení:
    - **Vytvořit novou**: tuto možnost použijte, pokud chcete vytvořit nový virtuální počítač. Můžete vytvořit virtuální počítač s jednoduchým nastavením nebo obnovit disk a vytvořit přizpůsobený virtuální počítač.
    - **Nahradit existující**: tuto možnost použijte, pokud chcete nahradit disky na EXISTUJÍCÍm virtuálním počítači.

        ![Průvodce obnovením konfigurace](./media/backup-azure-arm-restore-vms/restore-configuration.png)

2. Zadejte nastavení pro vybranou možnost obnovení.

## <a name="create-a-vm"></a>Vytvoření virtuálního počítače

Jako jednu z [možností obnovení](#restore-options)můžete vytvořit virtuální počítač rychle pomocí základního nastavení z bodu obnovení.

1. V **nastavení obnovit konfiguraci** > **vytvořit nový** > **obnovení typu**vyberte **vytvořit virtuální počítač**.
2. V části **název virtuálního počítače**zadejte virtuální počítač, který v předplatném neexistuje.
3. V části **Skupina prostředků**vyberte existující skupinu prostředků pro nový virtuální počítač, nebo vytvořte novou s globálně jedinečným názvem. Pokud přiřadíte název, který už existuje, Azure přiřadí skupině stejný název jako virtuální počítač.
4. Ve **virtuální síti**vyberte virtuální síť, do které se virtuální počítač umístí. Zobrazí se všechny virtuální sítě přidružené k předplatnému. Vyberte podsíť. Ve výchozím nastavení je vybraná první podsíť.
5. Do pole **umístění úložiště**zadejte účet úložiště pro virtuální počítač. [Další informace](#storage-accounts).

    ![Průvodce obnovením konfigurace](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard1.png)

6. V **nastavení obnovit konfiguraci**vyberte **OK**. V části **obnovit**klikněte na **obnovit** , aby se spustila operace obnovení.

## <a name="restore-disks"></a>Obnovit disky

Jako jednu z [možností obnovení](#restore-options)můžete vytvořit disk z bodu obnovení. Pak s diskem můžete provést jednu z následujících akcí:

- Použijte šablonu generovanou během operace obnovení k přizpůsobení nastavení a aktivaci nasazení virtuálního počítače. Můžete upravit výchozí nastavení šablony a odeslat šablonu pro nasazení virtuálního počítače.
- [Připojte obnovené disky](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal) k EXISTUJÍCÍmu virtuálnímu počítači.
- [Vytvořte nový virtuální počítač](https://docs.microsoft.com/azure/backup/backup-azure-vms-automation#create-a-vm-from-restored-disks) z obnovených disků pomocí PowerShellu.

1. V **nastavení obnovit konfiguraci** > **vytvořit nový** > **obnovení typu**vyberte **obnovit disky**.
2. V části **Skupina prostředků**vyberte existující skupinu prostředků pro obnovené disky nebo vytvořte novou s globálně jedinečným názvem.
3. V části **účet úložiště**zadejte účet, do kterého se mají kopírovat virtuální pevné disky. [Další informace](#storage-accounts).

    ![Konfigurace obnovení se dokončila.](./media/backup-azure-arm-restore-vms/trigger-restore-operation1.png)

4. V **nastavení obnovit konfiguraci**vyberte **OK**. V části **obnovit**klikněte na **obnovit** , aby se spustila operace obnovení.

Když virtuální počítač používá spravované disky a vyberete možnost **vytvořit virtuální počítač** , Azure Backup nepoužije zadaný účet úložiště. V případě **obnovení disků** a **okamžitého obnovení**se účet úložiště používá jenom pro ukládání šablony. Spravované disky se vytvářejí v zadané skupině prostředků.
Když virtuální počítač používá nespravované disky, obnoví se jako objekty blob do účtu úložiště.

### <a name="use-templates-to-customize-a-restored-vm"></a>Přizpůsobení obnoveného virtuálního počítače pomocí šablon

Po obnovení disku použijte šablonu generovanou jako součást operace obnovení a přizpůsobte a vytvořte nový virtuální počítač:

1. Otevřete položku **obnovit podrobnosti úlohy** pro příslušnou úlohu.

2. V v **podrobnostech úlohy obnovení**vyberte **nasadit šablonu** pro zahájení nasazování šablony.

    ![Přejít k podrobnostem úlohy obnovení](./media/backup-azure-arm-restore-vms/restore-job-drill-down1.png)

3. Pokud chcete upravit nastavení virtuálního počítače, které jste zadali v šabloně, klikněte na **Upravit šablonu**. Pokud chcete přidat další přizpůsobení, klikněte na **Upravit parametry**.
    - [Přečtěte si další informace](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template) o nasazení prostředků z vlastní šablony.
    - [Přečtěte si další informace](../azure-resource-manager/templates/template-syntax.md) o vytváření šablon.

   ![Načíst nasazení šablony](./media/backup-azure-arm-restore-vms/edit-template1.png)

4. Zadejte vlastní hodnoty pro virtuální počítač, přijměte **podmínky a** klikněte na **koupit**.

   ![Odeslat nasazení šablony](./media/backup-azure-arm-restore-vms/submitting-template1.png)

## <a name="replace-existing-disks"></a>Nahradit existující disky

Jako jednu z [možností obnovení](#restore-options)můžete stávající disk virtuálního počítače nahradit vybraným bodem obnovení. [Zkontrolujte](#restore-options) všechny možnosti obnovení.

1. V nabídce **obnovit konfiguraci**klikněte na **nahradit existující**.
2. V **typu obnovení**vyberte **nahradit disk/s**. Toto je bod obnovení, který bude použit k nahrazení existujících disků virtuálního počítače.
3. V části **pracovní umístění**určete, kam se mají během procesu obnovení ukládat snímky aktuálních spravovaných disků. [Další informace](#storage-accounts).

   ![Průvodce obnovením konfigurace nahradit existující](./media/backup-azure-arm-restore-vms/restore-configuration-replace-existing.png)

## <a name="cross-region-restore"></a>Obnovení mezi oblastmi

Jedna z [možností obnovení](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#restore-options)(crr) umožňuje obnovení virtuálních počítačů Azure v sekundární oblasti, která je spárována se službou Azure.

Při připojování k funkci ve verzi Preview si přečtěte [část než začnete](https://docs.microsoft.com/azure/backup/backup-create-rs-vault#set-cross-region-restore).

Pokud chcete zjistit, jestli je CRR povolený, postupujte podle pokynů v tématu [Konfigurace obnovení mezi oblastmi](backup-create-rs-vault.md#configure-cross-region-restore) .

### <a name="view-backup-items-in-secondary-region"></a>Zobrazit zálohované položky v sekundární oblasti

Pokud je povolená možnost CRR, můžete zobrazit zálohované položky v sekundární oblasti.

1. Na portálu přejdete na **Recovery Services trezor** > **zálohované položky** .
2. Kliknutím na **Sekundární oblast** zobrazíte položky v sekundární oblasti.

![Virtuální počítače v sekundární oblasti](./media/backup-azure-arm-restore-vms/secbackedupitem.png)

![Vybrat sekundární oblast](./media/backup-azure-arm-restore-vms/backupitems-sec.png)

### <a name="restore-in-secondary-region"></a>Obnovení v sekundární oblasti

Funkce obnovení koncového uživatele v sekundární oblasti bude podobná primární oblasti obnovení uživatelského prostředí. Při konfiguraci podrobností v okně obnovit konfiguraci pro konfiguraci obnovení se zobrazí výzva k zadání pouze sekundárních parametrů oblasti.

![Vyberte virtuální počítač, který chcete obnovit.](./media/backup-azure-arm-restore-vms/sec-restore.png)

![Vybrat bod obnovení](./media/backup-azure-arm-restore-vms/sec-rp.png)

![Obnovit konfiguraci](./media/backup-azure-arm-restore-vms/rest-config.png)

![Oznámení o průběhu obnovení aktivační události](./media/backup-azure-arm-restore-vms/restorenotifications.png)

- Informace o tom, jak obnovit a vytvořit virtuální počítač, najdete v tématu [Vytvoření virtuálního počítače](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#create-a-vm).
- Informace o obnovení jako disku najdete v části [obnovení disků](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#restore-disks).

### <a name="monitoring-secondary-region-restore-jobs"></a>Monitorování úloh obnovení sekundární oblasti

1. Na portálu přejdete do služby **Recovery Services trezor** > **úlohy zálohování** .
2. Kliknutím na **Sekundární oblast** zobrazíte položky v sekundární oblasti.

![Filtrované úlohy zálohování](./media/backup-azure-arm-restore-vms/secbackupjobs.png)

## <a name="restore-vms-with-special-configurations"></a>Obnovení virtuálních počítačů se speciálními konfiguracemi

K dispozici je řada běžných scénářů, ve kterých může být nutné obnovení virtuálních počítačů.

**Scénář** | **Doprovodné materiály**
--- | ---
**Obnovení virtuálních počítačů pomocí zvýhodněné hybridní využití** | Pokud virtuální počítač s Windows používá [licencování programu zvýhodněné hybridní využití (centra)](../virtual-machines/windows/hybrid-use-benefit-licensing.md), obnovte disky a vytvořte nový virtuální počítač pomocí zadané šablony (s **typem licence** nastavenou na **Windows_Server**) nebo v PowerShellu.  Toto nastavení se dá použít i po vytvoření virtuálního počítače.
**Obnovení virtuálních počítačů během havárie datacentra Azure** | Pokud trezor používá GRS a primární datacentrum pro virtuální počítač funguje, Azure Backup podporuje obnovení zálohovaných virtuálních počítačů do spárovaného datového centra. V spárovaném datovém centru vyberete účet úložiště a obnovíte ho jako normální. Azure Backup používá výpočetní službu v spárované oblasti k vytvoření obnoveného virtuálního počítače. [Přečtěte si další informace](../resiliency/resiliency-technical-guidance-recovery-loss-azure-region.md) o odolnosti datového centra.
**Obnovení virtuálního počítače s jedním řadičem domény v jedné doméně** | Obnovte virtuální počítač stejným způsobem jako jakýkoli jiný virtuální počítač. Poznámky:<br/><br/> V perspektivě služby Active Directory je virtuální počítač Azure podobně jako jakýkoli jiný virtuální počítač.<br/><br/> K dispozici je také režim obnovení adresářových služeb (DSRM), aby všechny scénáře obnovení služby Active Directory byly životaschopné. [Přečtěte si další](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#post-restore-steps) informace o požadavcích na zálohování a obnovení u virtualizovaných řadičů domény.
**Obnovení virtuálních počítačů s několika řadiči domény v jedné doméně** | Pokud je možné přes síť dosáhnout jiných řadičů domény ve stejné doméně, může být řadič domény obnovený jako libovolný virtuální počítač. Pokud se jedná o poslední zbývající řadič domény v doméně, nebo se provede obnovení v izolované síti, použijte [obnovení doménové struktury](https://docs.microsoft.com/windows-server/identity/ad-ds/manage/ad-forest-recovery-single-domain-in-multidomain-recovery).
**Obnovení více domén v jedné doménové struktuře** | Doporučujeme [obnovení doménové struktury](https://docs.microsoft.com/windows-server/identity/ad-ds/manage/ad-forest-recovery-single-domain-in-multidomain-recovery).
**Úplné obnovení** | Hlavním rozdílem mezi virtuálními počítači Azure a místními hypervisory je, že v Azure není k dispozici žádná konzola virtuálních počítačů. Pro určité scénáře se vyžaduje konzola, jako je třeba obnovení pomocí zálohování typu úplného obnovení systému (BMR). Obnovení virtuálního počítače z trezoru je ale úplná náhrada pro BMR.
**Obnovení virtuálních počítačů se speciálními síťovými konfiguracemi** | Mezi speciální síťové konfigurace patří virtuální počítače s využitím interního nebo externího vyrovnávání zatížení, použití více síťových adaptérů nebo více rezervovaných IP adres. Tyto virtuální počítače obnovíte pomocí [možnosti obnovit disk](#restore-disks). Tato možnost vytvoří kopii VHD do zadaného účtu úložiště a pak můžete vytvořit virtuální počítač s [interním](https://azure.microsoft.com/documentation/articles/load-balancer-internal-getstarted/) nebo [externím](https://azure.microsoft.com/documentation/articles/load-balancer-internet-getstarted/) nástrojem pro vyrovnávání zatížení, [několika síťovými kartami](../virtual-machines/windows/multiple-nics.md)nebo [více rezervovanými IP adresami](../virtual-network/virtual-network-multiple-ip-addresses-powershell.md)v souladu s vaší konfigurací.
**Skupina zabezpečení sítě (NSG) na síťové kartě nebo podsíti** | Zálohování virtuálních počítačů Azure podporuje zálohování a obnovení NSG informací na úrovni virtuální sítě, podsítě a síťové karty.
**Virtuální počítače připojené k zóně** | Azure Backup podporuje zálohování a obnovení připnutých virtuálních počítačů v zóně. [Další informace](https://azure.microsoft.com/global-infrastructure/availability-zones/)

## <a name="track-the-restore-operation"></a>Sledovat operaci obnovení

Po aktivaci operace obnovení vytvoří služba Backup úlohu pro sledování. Azure Backup zobrazuje oznámení o úloze na portálu. Pokud nejsou viditelné, vyberte symbol **oznámení** a pak výběrem **Zobrazit všechny úlohy** zobrazte stav procesu obnovení.

![Obnovení aktivované](./media/backup-azure-arm-restore-vms/restore-notification1.png)

 Sledujte obnovení následujícím způsobem:

1. Chcete-li zobrazit operace pro úlohu, klikněte na hypertextový odkaz oznámení. Případně klikněte v trezoru na **úlohy zálohování**a pak klikněte na příslušný virtuální počítač.

    ![Seznam virtuálních počítačů v trezoru](./media/backup-azure-arm-restore-vms/restore-job-in-progress1.png)

2. Pokud chcete monitorovat průběh obnovení, klikněte na libovolnou úlohu obnovení se stavem **probíhá.** Tím se zobrazí indikátor průběhu, který zobrazí informace o průběhu obnovení:

    - **Odhadovaný čas obnovení**: zpočátku poskytuje čas potřebný k dokončení operace obnovení. V průběhu operace se čas zmenšuje a při dokončení operace obnovení se dosáhne nuly.
    - **Procento obnovení**. Zobrazuje procento dokončené operace obnovení.
    - **Počet přenesených bajtů**: Pokud provádíte obnovení vytvořením nového virtuálního počítače, zobrazí se bajty přenesené na celkový počet bajtů, které se mají přenést.

## <a name="post-restore-steps"></a>Kroky po obnovení

Po obnovení virtuálního počítače je potřeba poznamenat si několik věcí:

- Rozšíření, která jsou přítomna během konfigurace zálohování, jsou nainstalována, ale nejsou povolena. Pokud se zobrazí problém, přeinstalujte rozšíření.
- Pokud má zálohovaný virtuální počítač statickou IP adresu, obnovený virtuální počítač bude mít dynamickou IP adresu, aby se předešlo konfliktu. [Do obnoveného virtuálního počítače můžete přidat STATICKOU IP adresu](../virtual-network/virtual-networks-reserved-private-ip.md#how-to-add-a-static-internal-ip-to-an-existing-vm).
- Obnovený virtuální počítač nemá skupinu dostupnosti. Pokud použijete možnost obnovit disk, můžete [zadat skupinu dostupnosti](../virtual-machines/windows/tutorial-availability-sets.md) při vytváření virtuálního počítače z disku pomocí zadané šablony nebo PowerShellu.
- Pokud používáte distribuci Linux založenou na cloudu, jako je například Ubuntu, z důvodu zabezpečení je heslo po obnovení zablokované. K [resetování hesla](../virtual-machines/linux/reset-password.md)použijte rozšíření VMAccess na OBNOVENém virtuálním počítači. V těchto distribucích doporučujeme používat klíče SSH, takže po obnovení nemusíte heslo resetovat.
- Pokud nemůžete získat přístup k virtuálnímu počítači po obnovení z důvodu přerušeného vztahu k virtuálnímu počítači s řadičem domény, postupujte podle následujících kroků a zajistěte si virtuální počítač:
  - Připojte disk s operačním systémem jako datový disk k obnovenému virtuálnímu počítači.
  - Ručně nainstalujte agenta virtuálního počítače, pokud se zjistí, že agent Azure přestane reagovat pomocí tohoto [odkazu](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/install-vm-agent-offline).
  - Povolení přístupu k virtuálnímu počítači z příkazového řádku na virtuálním počítači přes sériovou konzolu

  ```cmd
    bcdedit /store <drive letter>:\boot\bcd /enum
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} displaybootmenu yes
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} timeout 5
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} bootems yes
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<<BOOT LOADER IDENTIFIER>>} ON
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
    ```

  - Při opakovaném sestavení virtuálního počítače použijte Azure Portal k resetování účtu místního správce a hesla
  - K odpojení virtuálního počítače z domény použijte Sériová konzola přístup a CMD.

    ```cmd
    cmd /c "netdom remove <<MachineName>> /domain:<<DomainName>> /userD:<<DomainAdminhere>> /passwordD:<<PasswordHere>> /reboot:10 /Force"
    ```

- Až se virtuální počítač odpojí a restartuje, budete moct úspěšně RDP na virtuální počítač s přihlašovacími údaji místního správce a úspěšně znovu připojit virtuální počítač k doméně.

## <a name="backing-up-restored-vms"></a>Zálohování obnovených virtuálních počítačů

- Pokud jste virtuální počítač obnovili do stejné skupiny prostředků, která má stejný název jako původně zálohovaný virtuální počítač, zálohování pokračuje po obnovení na VIRTUÁLNÍm počítači.
- Pokud jste virtuální počítač obnovili do jiné skupiny prostředků nebo jste pro obnovený virtuální počítač určili jiný název, musíte pro obnovený virtuální počítač nastavit zálohu.

## <a name="next-steps"></a>Další kroky

- Pokud se během procesu obnovení setkáte s problémy, [Projděte si](backup-azure-vms-troubleshoot.md#restore) běžné problémy a chyby.
- Po obnovení virtuálního počítače se dozvíte víc o [správě virtuálních počítačů](backup-azure-manage-vms.md) .
