---
title: Obnovení virtuálních počítačů pomocí Azure Portal
description: Obnovte virtuální počítač Azure z bodu obnovení pomocí Azure Portal, včetně funkce pro obnovení mezi oblastmi.
ms.reviewer: geg
ms.topic: conceptual
ms.date: 08/02/2020
ms.openlocfilehash: 4be107a1647ac1aa95aea9c1c68e6f024be82b1c
ms.sourcegitcommit: 6e2d37afd50ec5ee148f98f2325943bafb2f4993
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/23/2020
ms.locfileid: "97746401"
---
# <a name="how-to-restore-azure-vm-data-in-azure-portal"></a>Postup obnovení dat virtuálního počítače Azure v Azure Portal

Tento článek popisuje, jak obnovit data virtuálních počítačů Azure z bodů obnovení uložených v [Azure Backup](backup-overview.md) Recovery Services trezory.

## <a name="restore-options"></a>Možnosti obnovení

Azure Backup poskytuje několik způsobů obnovení virtuálního počítače.

**Možnost obnovení** | **Podrobnosti**
--- | ---
**Vytvoření nového virtuálního počítače** | Rychle vytvoří a spustí základní virtuální počítač z bodu obnovení.<br/><br/> Můžete zadat název virtuálního počítače, vybrat skupinu prostředků a virtuální síť (VNet), do které se bude umístit, a zadat účet úložiště pro obnovený virtuální počítač. Nový virtuální počítač se musí vytvořit ve stejné oblasti jako zdrojový virtuální počítač.<br><br>Pokud se obnovení virtuálního počítače nepodaří, protože skladová položka virtuálního počítače Azure nebyla dostupná v zadané oblasti Azure, nebo kvůli jakýmkoli jiným problémům, Azure Backup pořád obnovit disky v zadané skupině prostředků.
**Obnovení disku** | Obnoví disk virtuálního počítače, který se pak dá použít k vytvoření nového virtuálního počítače.<br/><br/> Azure Backup poskytuje šablonu, která vám pomůže s přizpůsobením a vytvořením virtuálního počítače. <br/><br> Úloha obnovení vygeneruje šablonu, kterou si můžete stáhnout a použít k určení vlastního nastavení virtuálního počítače, a vytvořit virtuální počítač.<br/><br/> Disky se zkopírují do vámi určené skupiny prostředků.<br/><br/> Případně můžete disk připojit k existujícímu virtuálnímu počítači nebo vytvořit nový virtuální počítač pomocí prostředí PowerShell.<br/><br/> Tato možnost je užitečná, pokud chcete přizpůsobit virtuální počítač, přidat nastavení konfigurace, která v době zálohování neexistovala, nebo přidat nastavení, která se musí nakonfigurovat pomocí šablony nebo PowerShellu.
**Nahrazení existujícího** | Disk můžete obnovit a použít ho k nahrazení disku na stávajícím virtuálním počítači.<br/><br/> Aktuální virtuální počítač musí existovat. Pokud byl odstraněn, nelze tuto možnost použít.<br/><br/> Před nahrazením disku Azure Backup pořizuje stávající virtuální počítač a uloží ho do pracovního umístění, které zadáte. Existující disky připojené k virtuálnímu počítači se nahradí vybranými body obnovení.<br/><br/> Snímek se zkopíruje do trezoru a zachová se podle zásad uchovávání informací. <br/><br/> Po operaci nahradit disk se původní disk zachová ve skupině prostředků. Pokud nepotřebujete, můžete původní disky odstranit ručně. <br/><br/>Nahradit existující se podporuje u nešifrovaných spravovaných virtuálních počítačů, včetně virtuálních počítačů [vytvořených pomocí vlastních imagí](https://azure.microsoft.com/resources/videos/create-a-custom-virtual-machine-image-in-azure-resource-manager-with-powershell/). U klasických virtuálních počítačů se nepodporuje.<br/><br/> Pokud má bod obnovení více nebo méně disků než aktuální virtuální počítač, bude počet disků v bodu obnovení odpovídat pouze konfiguraci virtuálního počítače.<br><br> Nahradit existující se podporuje taky pro virtuální počítače s propojenými prostředky, jako je například spravovaná identita nebo [Key Vault](../key-vault/general/overview.md) [přiřazená uživatelem](../active-directory/managed-identities-azure-resources/overview.md) .
**Mezi oblastmi (sekundární oblast)** | Obnovení mezi oblastmi se dá použít k obnovení virtuálních počítačů Azure v sekundární oblasti, která je [spárované v oblasti Azure](../best-practices-availability-paired-regions.md#what-are-paired-regions).<br><br> Pokud se zálohování provádí v sekundární oblasti, můžete obnovit všechny virtuální počítače Azure pro vybraný bod obnovení.<br><br> Tato funkce je k dispozici pro následující možnosti:<br> <li> [Vytvořit virtuální počítač](#create-a-vm) <br> <li> [Obnovit disky](#restore-disks) <br><br> Momentálně nepodporujeme možnost [nahradit existující disky](#replace-existing-disks) .<br><br> Oprávnění<br> Operaci obnovení v sekundární oblasti můžou provádět správci zálohování a správci aplikací.

> [!NOTE]
> Můžete také obnovit konkrétní soubory a složky na virtuálním počítači Azure. [Přečtěte si další informace](backup-azure-restore-files-from-vm.md).

## <a name="storage-accounts"></a>Účty úložiště

Některé podrobnosti o účtech úložiště:

- **Vytvoření virtuálního** počítače: Když vytváříte nový virtuální počítač, virtuální počítač se umístí do zadaného účtu úložiště.
- **Obnovit disk**: Když obnovíte disk, disk se zkopíruje do zadaného účtu úložiště. Úloha obnovení vygeneruje šablonu, kterou můžete stáhnout a použít k určení vlastního nastavení virtuálního počítače. Tato šablona je umístěná v zadaném účtu úložiště.
- **Výměna disku**: když nahradíte disk v EXISTUJÍCÍm virtuálním počítači, Azure Backup před nahrazením disku vybere snímek existujícího virtuálního počítače. Snímek se také zkopíruje do trezoru Recovery Services prostřednictvím přenosu dat jako proces na pozadí. Po dokončení fáze snímku se ale spustí operace nahradit disky. Po operaci nahradit disk jsou disky zdrojového virtuálního počítače Azure ponechány v zadané skupině prostředků pro vaši operaci a virtuální pevné disky budou uloženy v zadaném účtu úložiště. Můžete zvolit, že tyto virtuální pevné disky a disky chcete odstranit nebo zachovat.
- **Umístění účtu úložiště**: účet úložiště musí být ve stejné oblasti jako trezor. Zobrazí se pouze tyto účty. Pokud v tomto umístění nejsou žádné účty úložiště, budete ho muset vytvořit.
- **Typ úložiště**: BLOB Storage není podporovaný.
- **Redundance úložiště**: zóna redundantní úložiště (ZRS) se nepodporuje. Informace o replikaci a redundanci účtu se zobrazí v závorkách za názvem účtu.
- **Premium Storage**:
  - Při obnovování virtuálních počítačů, které nejsou Premium, se nepodporuje účty úložiště úrovně Premium.
  - Při obnovování spravovaných virtuálních počítačů se účty Premium Storage nakonfigurované pomocí pravidel sítě nepodporují.

## <a name="before-you-start"></a>Než začnete

Pokud chcete obnovit virtuální počítač (vytvořte nový virtuální počítač), ujistěte se, že máte správná [oprávnění](backup-rbac-rs-vault.md#mapping-backup-built-in-roles-to-backup-management-actions) řízení přístupu na základě role Azure (Azure RBAC) pro operaci obnovení virtuálního počítače.

Pokud nemáte oprávnění, můžete [disk obnovit](#restore-disks)a po obnovení disku můžete [použít šablonu](#use-templates-to-customize-a-restored-vm) , která byla vygenerována jako součást operace obnovení, a vytvořit tak nový virtuální počítač.

[!INCLUDE [backup-center.md](../../includes/backup-center.md)]

## <a name="select-a-restore-point"></a>Vybrat bod obnovení

1. V trezoru přidruženého k virtuálnímu počítači, který chcete obnovit, vyberte **zálohované položky**  >  **virtuální počítač Azure**.
1. Vyberte virtuální počítač. Ve výchozím nastavení se na řídicím panelu virtuálních počítačů zobrazí body obnovení za posledních 30 dní. Můžete zobrazit body obnovení starší než 30 dní nebo filtrovat a vyhledat body obnovení na základě dat, časových rozsahů a různých typů konzistence snímků.
1. Pokud chcete virtuální počítač obnovit, vyberte **obnovit virtuální počítač**.

    ![Bod obnovení](./media/backup-azure-arm-restore-vms/restore-point.png)

1. Vyberte bod obnovení, který se má použít pro obnovení.

## <a name="choose-a-vm-restore-configuration"></a>Zvolit konfiguraci obnovení virtuálního počítače

1. V možnosti **obnovit virtuální počítač** vyberte možnost obnovení:
    - **Vytvořit novou**: tuto možnost použijte, pokud chcete vytvořit nový virtuální počítač. Můžete vytvořit virtuální počítač s jednoduchým nastavením nebo obnovit disk a vytvořit přizpůsobený virtuální počítač.
    - **Nahradit existující**: tuto možnost použijte, pokud chcete nahradit disky na EXISTUJÍCÍm virtuálním počítači.

        ![Průvodce obnovením konfigurace virtuálního počítače](./media/backup-azure-arm-restore-vms/restore-configuration.png)

1. Zadejte nastavení pro vybranou možnost obnovení.

## <a name="create-a-vm"></a>Vytvoření virtuálního počítače

Jako jednu z [možností obnovení](#restore-options)můžete vytvořit virtuální počítač rychle pomocí základního nastavení z bodu obnovení.

1. V nástroji **obnovit virtuální počítač**  >  **vytvořit nový**  >  **typ obnovení** vyberte **vytvořit virtuální počítač**.
1. V části **název virtuálního počítače** zadejte virtuální počítač, který v předplatném neexistuje.
1. V části **Skupina prostředků** vyberte existující skupinu prostředků pro nový virtuální počítač, nebo vytvořte novou s globálně jedinečným názvem. Pokud přiřadíte název, který už existuje, Azure přiřadí skupině stejný název jako virtuální počítač.
1. Ve **virtuální síti** vyberte virtuální síť, do které se virtuální počítač umístí. Zobrazí se všechny virtuální sítě přidružené k předplatnému. Vyberte podsíť. Ve výchozím nastavení je vybraná první podsíť.
1. Do pole **pracovní umístění** zadejte účet úložiště pro virtuální počítač. [Přečtěte si další informace](#storage-accounts).

    ![Průvodce obnovením konfigurace – výběr možností obnovení](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard1.png)

1. Vyberte **obnovit** pro aktivaci operace obnovení.

## <a name="restore-disks"></a>Obnovit disky

Jako jednu z [možností obnovení](#restore-options)můžete vytvořit disk z bodu obnovení. Pak s diskem můžete udělat jednu z následujících akcí:

- Použijte šablonu generovanou během operace obnovení k přizpůsobení nastavení a aktivaci nasazení virtuálního počítače. Můžete upravit výchozí nastavení šablony a odeslat šablonu pro nasazení virtuálního počítače.
- [Připojte obnovené disky](../virtual-machines/windows/attach-managed-disk-portal.md) k EXISTUJÍCÍmu virtuálnímu počítači.
- [Vytvořte nový virtuální počítač](./backup-azure-vms-automation.md#create-a-vm-from-restored-disks) z obnovených disků pomocí PowerShellu.

1. V **nastavení obnovit konfiguraci**  >  **vytvořit nový**  >  **typ obnovení** vyberte **obnovit disky**.
1. V části **Skupina prostředků** vyberte existující skupinu prostředků pro obnovené disky nebo vytvořte novou s globálně jedinečným názvem.
1. Do pole **pracovní umístění** zadejte účet úložiště, do kterého se mají kopírovat virtuální pevné disky. [Přečtěte si další informace](#storage-accounts).

    ![Vybrat skupinu prostředků a pracovní umístění](./media/backup-azure-arm-restore-vms/trigger-restore-operation1.png)

1. Vyberte **obnovit** pro aktivaci operace obnovení.

Když virtuální počítač používá spravované disky a vyberete možnost **vytvořit virtuální počítač** , Azure Backup nepoužije zadaný účet úložiště. V případě **obnovení disků** a **okamžitého obnovení** se účet úložiště používá jenom pro ukládání šablony. Spravované disky se vytvářejí v zadané skupině prostředků.
Když virtuální počítač používá nespravované disky, obnoví se jako objekty blob do účtu úložiště.

### <a name="use-templates-to-customize-a-restored-vm"></a>Přizpůsobení obnoveného virtuálního počítače pomocí šablon

Po obnovení disku použijte šablonu generovanou jako součást operace obnovení a přizpůsobte a vytvořte nový virtuální počítač:

1. V části **úlohy zálohování** vyberte příslušnou úlohu obnovení.

1. V **obnovení** vyberte **nasadit šablonu** pro zahájení nasazování šablony.

    ![Přejít k podrobnostem úlohy obnovení](./media/backup-azure-arm-restore-vms/restore-job-drill-down1.png)

1. Pokud chcete upravit nastavení virtuálního počítače v šabloně, vyberte **Upravit šablonu**. Pokud chcete přidat další úpravy, vyberte **Upravit parametry**.
    - [Přečtěte si další informace](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template) o nasazení prostředků z vlastní šablony.
    - [Přečtěte si další informace](../azure-resource-manager/templates/template-syntax.md) o vytváření šablon.

   ![Načíst nasazení šablony](./media/backup-azure-arm-restore-vms/edit-template1.png)

1. Zadejte vlastní hodnoty pro virtuální počítač, přijměte **podmínky a** zvolte **koupit**.

   ![Odeslat nasazení šablony](./media/backup-azure-arm-restore-vms/submitting-template1.png)

## <a name="replace-existing-disks"></a>Nahradit existující disky

Jako jednu z [možností obnovení](#restore-options)můžete stávající disk virtuálního počítače nahradit vybraným bodem obnovení. [Zkontrolujte](#restore-options) všechny možnosti obnovení.

1. V **nastavení obnovit konfiguraci** vyberte **nahradit existující**.
1. V **typu obnovení** vyberte **nahradit disk/s**. Toto je bod obnovení, který bude použit k nahrazení existujících disků virtuálního počítače.
1. V části **pracovní umístění** určete, kam se mají během procesu obnovení ukládat snímky aktuálních spravovaných disků. [Přečtěte si další informace](#storage-accounts).

   ![Průvodce obnovením konfigurace nahradit existující](./media/backup-azure-arm-restore-vms/restore-configuration-replace-existing.png)

## <a name="cross-region-restore"></a>Obnovení mezi oblastmi

Jedna z [možností obnovení](#restore-options)(crr) umožňuje obnovení virtuálních počítačů Azure v sekundární oblasti, která je spárována se službou Azure.

Pokud chcete připojit funkci během verze Preview, přečtěte si [část než začnete](./backup-create-rs-vault.md#set-cross-region-restore).

Pokud chcete zjistit, jestli je CRR povolený, postupujte podle pokynů v části [Konfigurace obnovení mezi oblastmi](backup-create-rs-vault.md#configure-cross-region-restore).

### <a name="view-backup-items-in-secondary-region"></a>Zobrazit zálohované položky v sekundární oblasti

Pokud je povolená možnost CRR, můžete zobrazit zálohované položky v sekundární oblasti.

1. Na portálu přejdete do části **Recovery Services trezoru**  >  **zálohované položky**.
1. Vyberte **Sekundární oblast** pro zobrazení položek v sekundární oblasti.

>[!NOTE]
>V seznamu se zobrazí pouze typy správy zálohování podporující funkci CRR. V současné době je povolena pouze podpora obnovení dat sekundární oblasti do sekundární oblasti.

![Virtuální počítače v sekundární oblasti](./media/backup-azure-arm-restore-vms/secbackedupitem.png)

![Vybrat sekundární oblast](./media/backup-azure-arm-restore-vms/backupitems-sec.png)

### <a name="restore-in-secondary-region"></a>Obnovení v sekundární oblasti

Funkce obnovení koncového uživatele v sekundární oblasti bude podobná primární oblasti obnovení uživatelského prostředí. Při konfiguraci podrobností v podokně obnovit konfiguraci pro konfiguraci obnovení se zobrazí výzva k zadání pouze sekundárních parametrů oblasti.

![Vyberte virtuální počítač, který chcete obnovit.](./media/backup-azure-arm-restore-vms/sec-restore.png)

![Vybrat bod obnovení](./media/backup-azure-arm-restore-vms/sec-rp.png)

![Obnovit konfiguraci](./media/backup-azure-arm-restore-vms/rest-config.png)

![Oznámení o průběhu obnovení aktivační události](./media/backup-azure-arm-restore-vms/restorenotifications.png)

- Informace o tom, jak obnovit a vytvořit virtuální počítač, najdete v tématu [Vytvoření virtuálního počítače](#create-a-vm).
- Informace o obnovení jako disku najdete v části [obnovení disků](#restore-disks).

>[!NOTE]
>
>- Po aktivaci obnovení a ve fázi přenosu dat nelze úlohu obnovení zrušit.
>- Funkce obnovení mezi oblastmi obnovuje CMK (klíče spravované zákazníky) s povolenými virtuálními počítači Azure, které se nezálohují do trezoru Recovery Services s povoleným CMK, jako virtuální počítače s povoleným CMK v sekundární oblasti.
>- Role Azure potřebné k obnovení v sekundární oblasti jsou stejné jako v primární oblasti.

### <a name="monitoring-secondary-region-restore-jobs"></a>Monitorování úloh obnovení sekundární oblasti

1. Na portálu přejdete na   >  **úlohy zálohování** služby Recovery Services trezor.
1. Vyberte **Sekundární oblast** pro zobrazení položek v sekundární oblasti.

    ![Filtrované úlohy zálohování](./media/backup-azure-arm-restore-vms/secbackupjobs.png)

## <a name="restoring-unmanaged-vms-and-disks-as-managed"></a>Obnovení nespravovaných virtuálních počítačů a disků jako spravovaných

Při obnovení máte k dispozici možnost obnovit [nespravované disky](../storage/common/storage-disaster-recovery-guidance.md#azure-unmanaged-disks) jako [spravované disky](../virtual-machines/managed-disks-overview.md) . Ve výchozím nastavení se nespravované virtuální počítače/disky obnoví jako nespravované virtuální počítače nebo disky. Pokud se ale rozhodnete je obnovit jako spravované virtuální počítače nebo disky, je teď možné to udělat. Tato obnovení nejsou aktivována ze fáze snímku, ale pouze ze fáze trezoru. Tato funkce není k dispozici pro nespravované šifrované virtuální počítače.

![Obnovit jako spravované disky](./media/backup-azure-arm-restore-vms/restore-as-managed-disks.png)

## <a name="restore-vms-with-special-configurations"></a>Obnovení virtuálních počítačů se speciálními konfiguracemi

K dispozici je řada běžných scénářů, ve kterých může být nutné obnovení virtuálních počítačů.

**Scénář** | **Pokyny**
--- | ---
**Obnovení virtuálních počítačů pomocí zvýhodněné hybridní využití** | Pokud virtuální počítač s Windows používá [licencování programu zvýhodněné hybridní využití (centra)](../virtual-machines/windows/hybrid-use-benefit-licensing.md), obnovte disky a vytvořte nový virtuální počítač pomocí zadané šablony (s **typem licence** nastavenou na **Windows_Server**) nebo v PowerShellu.  Toto nastavení se dá použít i po vytvoření virtuálního počítače.
**Obnovení virtuálních počítačů během havárie datacentra Azure** | Pokud trezor používá GRS a primární datacentrum pro virtuální počítač funguje, Azure Backup podporuje obnovení zálohovaných virtuálních počítačů do spárovaného datového centra. V spárovaném datovém centru vyberete účet úložiště a obnovíte ho jako normální. Azure Backup používá výpočetní službu v spárované oblasti k vytvoření obnoveného virtuálního počítače. [Přečtěte si další informace](/azure/architecture/resiliency/recovery-loss-azure-region) o odolnosti datového centra.<br><br> Pokud trezor používá GRS, můžete si vybrat novou funkci – [obnovení mezi oblastmi](#cross-region-restore). To vám umožňuje obnovení do druhé oblasti ve scénářích úplného nebo částečného výpadku, nebo i v případě, že vůbec nedochází k výpadku.
**Úplné obnovení** | Hlavním rozdílem mezi virtuálními počítači Azure a místními hypervisory je, že v Azure není k dispozici žádná konzola virtuálních počítačů. Pro určité scénáře se vyžaduje konzola, jako je třeba obnovení pomocí zálohování typu úplného obnovení systému (BMR). Obnovení virtuálního počítače z trezoru je ale úplná náhrada pro BMR.
**Obnovení virtuálních počítačů se speciálními síťovými konfiguracemi** | Mezi speciální síťové konfigurace patří virtuální počítače s využitím interního nebo externího vyrovnávání zatížení, použití více síťových adaptérů nebo více rezervovaných IP adres. Tyto virtuální počítače obnovíte pomocí [možnosti obnovit disk](#restore-disks). Tato možnost vytvoří kopii VHD do zadaného účtu úložiště a pak můžete vytvořit virtuální počítač s [interním](../load-balancer/quickstart-load-balancer-standard-internal-powershell.md) nebo [externím](../load-balancer/quickstart-load-balancer-standard-public-powershell.md) nástrojem pro vyrovnávání zatížení, [několika síťovými kartami](../virtual-machines/windows/multiple-nics.md)nebo [více rezervovanými IP adresami](../virtual-network/virtual-network-multiple-ip-addresses-powershell.md)v souladu s vaší konfigurací.
**Skupina zabezpečení sítě (NSG) na síťové kartě nebo podsíti** | Zálohování virtuálních počítačů Azure podporuje zálohování a obnovení NSG informací na úrovni virtuální sítě, podsítě a síťové karty.
**Virtuální počítače připojené k zóně** | Pokud zálohujete virtuální počítač Azure, který je připnuté k zóně (pomocí Azure Backup), můžete ho obnovit ve stejné zóně, kde byla připnutá. [Další informace](../availability-zones/az-overview.md)
**Obnovit virtuální počítač v libovolné skupině dostupnosti** | Při obnovení virtuálního počítače z portálu není k dispozici možnost výběru skupiny dostupnosti. Obnovený virtuální počítač nemá skupinu dostupnosti. Pokud použijete možnost obnovit disk, můžete [zadat skupinu dostupnosti](../virtual-machines/windows/tutorial-availability-sets.md) při vytváření virtuálního počítače z disku pomocí zadané šablony nebo PowerShellu.
**Obnovení speciálních virtuálních počítačů, jako jsou třeba virtuální počítače SQL** | Pokud zálohujete virtuální počítač s SQL pomocí zálohování virtuálních počítačů Azure a pak použijete možnost obnovit virtuální počítač nebo po obnovení disků vytvořit virtuální počítač, musí být nově vytvořený virtuální počítač zaregistrován u poskytovatele SQL, jak je uvedeno [zde](../azure-sql/virtual-machines/windows/sql-agent-extension-manually-register-single-vm.md?tabs=azure-cli%2cbash). Tím se obnovený virtuální počítač převede na virtuální počítač SQL.

### <a name="restore-domain-controller-vms"></a>Obnovení virtuálních počítačů řadiče domény

**Scénář** | **Pokyny**
--- | ---
**Obnovení virtuálního počítače s jedním řadičem domény v jedné doméně** | Obnovte virtuální počítač stejným způsobem jako jakýkoli jiný virtuální počítač. Poznámky:<br/><br/> V perspektivě služby Active Directory je virtuální počítač Azure podobně jako jakýkoli jiný virtuální počítač.<br/><br/> K dispozici je také režim obnovení adresářových služeb (DSRM), aby všechny scénáře obnovení služby Active Directory byly životaschopné. [Přečtěte si další](#post-restore-steps) informace o požadavcích na zálohování a obnovení u virtualizovaných řadičů domény.
**Obnovení virtuálních počítačů s několika řadiči domény v jedné doméně** | Pokud je možné přes síť dosáhnout jiných řadičů domény ve stejné doméně, může být řadič domény obnovený jako libovolný virtuální počítač. Pokud se jedná o poslední zbývající řadič domény v doméně, nebo se provede obnovení v izolované síti, použijte [obnovení doménové struktury](/windows-server/identity/ad-ds/manage/ad-forest-recovery-single-domain-in-multidomain-recovery).
**Obnovení virtuálního počítače s jedním řadičem domény v konfiguraci s více doménami** |  Obnovení disků a vytvoření virtuálního počítače [pomocí PowerShellu](backup-azure-vms-automation.md#restore-the-disks)  
**Obnovení více domén v jedné doménové struktuře** | Doporučujeme [obnovení doménové struktury](/windows-server/identity/ad-ds/manage/ad-forest-recovery-single-domain-in-multidomain-recovery).

Další informace najdete v tématu [zálohování a obnovení řadičů domény služby Active Directory](active-directory-backup-restore.md).

## <a name="track-the-restore-operation"></a>Sledovat operaci obnovení

Po aktivaci operace obnovení vytvoří služba Backup úlohu pro sledování. Azure Backup zobrazuje oznámení o úloze na portálu. Pokud nejsou viditelné, vyberte symbol **oznámení** a potom **v protokolu aktivit vyberte další události** , abyste viděli stav procesu obnovení.

![Obnovení aktivované](./media/backup-azure-arm-restore-vms/restore-notification1.png)

 Sledujte obnovení následujícím způsobem:

1. Chcete-li zobrazit operace pro úlohu, vyberte hypertextový odkaz oznámení. Případně můžete v trezoru vybrat **úlohy zálohování** a pak vybrat příslušný virtuální počítač.

    ![Seznam virtuálních počítačů v trezoru](./media/backup-azure-arm-restore-vms/restore-job-in-progress1.png)

1. Pokud chcete monitorovat průběh obnovení, vyberte libovolnou úlohu obnovení se stavem **probíhá.** Tím se zobrazí indikátor průběhu, který zobrazí informace o průběhu obnovení:

    - **Odhadovaný čas obnovení**: zpočátku poskytuje čas potřebný k dokončení operace obnovení. V průběhu operace se čas zmenšuje a při dokončení operace obnovení se dosáhne nuly.
    - **Procento obnovení**. Zobrazuje procento dokončené operace obnovení.
    - **Počet přenesených bajtů**: Pokud provádíte obnovení vytvořením nového virtuálního počítače, zobrazí se bajty přenesené na celkový počet bajtů, které se mají přenést.

## <a name="post-restore-steps"></a>Kroky po obnovení

Po obnovení virtuálního počítače je potřeba poznamenat si několik věcí:

- Rozšíření, která jsou přítomna během konfigurace zálohování, jsou nainstalována, ale nejsou povolena. Pokud se zobrazí problém, přeinstalujte rozšíření.
- Pokud má zálohovaný virtuální počítač statickou IP adresu, obnovený virtuální počítač bude mít dynamickou IP adresu, aby se předešlo konfliktu. [Do obnoveného virtuálního počítače můžete přidat STATICKOU IP adresu](/powershell/module/az.network/set-aznetworkinterfaceipconfig#description).
- Obnovený virtuální počítač nemá skupinu dostupnosti. Pokud použijete možnost obnovit disk, můžete [zadat skupinu dostupnosti](../virtual-machines/windows/tutorial-availability-sets.md) při vytváření virtuálního počítače z disku pomocí zadané šablony nebo PowerShellu.
- Pokud používáte distribuci Linux založenou na cloudu, jako je například Ubuntu, z důvodu zabezpečení je heslo po obnovení zablokované. K [resetování hesla](../virtual-machines/troubleshooting/reset-password.md)použijte rozšíření VMAccess na OBNOVENém virtuálním počítači. V těchto distribucích doporučujeme používat klíče SSH, takže po obnovení nemusíte heslo resetovat.
- Pokud po obnovení nemůžete získat přístup k virtuálnímu počítači, protože virtuální počítač má přerušenou relaci s řadičem domény, postupujte podle následujících kroků a zajistěte si virtuální počítač:
  - Připojte disk s operačním systémem jako datový disk k obnovenému virtuálnímu počítači.
  - Ručně nainstalujte agenta virtuálního počítače, pokud se zjistí, že agent Azure přestane reagovat pomocí tohoto [odkazu](../virtual-machines/troubleshooting/install-vm-agent-offline.md).
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
