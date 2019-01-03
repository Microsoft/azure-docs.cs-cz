---
title: 'Azure Backup: Obnovení virtuálních počítačů pomocí webu Azure portal'
description: Obnovení virtuálního počítače Azure z bodu obnovení pomocí webu Azure portal
services: backup
author: geethalakshmig
manager: vijayts
keywords: obnovení zálohy; Postup při obnovení; bod obnovení;
ms.service: backup
ms.topic: conceptual
ms.date: 09/04/2017
ms.author: geg
ms.openlocfilehash: ac0c727e41ad9b361ea9f558a97f16446c12ef0e
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/27/2018
ms.locfileid: "53793372"
---
# <a name="use-the-azure-portal-to-restore-virtual-machines"></a>Obnovení virtuálních počítačů pomocí webu Azure portal
Chraňte vaše data pořizování snímků dat definovaných intervalech. Tyto snímky jsou označovány jako body obnovení a jsou uloženy v trezorech služby Recovery Services. Pokud je nutné opravit nebo znovu vytvořit virtuální počítač (VM), můžete obnovit virtuální počítač z některé z bodů obnovení uložených. Při obnovení bodu obnovení můžete provést tyto akce:

* Vytvoření nového virtuálního počítače: Z bodu obnovení bodu v čase zálohování virtuálního počítače.
* Obnovte disky: Použít šablonu, která se dodává s procesem přizpůsobení obnovený virtuální počítač nebo provést obnovení jednotlivých souborů.

Tento článek vysvětluje postup obnovení virtuálního počítače do nového virtuálního počítače nebo obnovení všech disků zálohovaných. Obnovení jednotlivých souborů, najdete v části [obnovení souborů ze zálohy virtuálního počítače Azure](backup-azure-restore-files-from-vm.md).

![Tři způsoby, jak obnovit ze zálohy virtuálního počítače](./media/backup-azure-arm-restore-vms/azure-vm-backup-restore.png)


Obnovení virtuálního počítače nebo všechny disky z virtuálního počítače záloha zahrnuje dva kroky:

* Vyberte bod obnovení pro obnovení.
* Vybrat typ obnovení
    - Možnost 1: Vytvoření nového virtuálního počítače
    - Možnost 2: Obnovte disky.

## <a name="select-a-restore-point-for-restore"></a>Vyberte bod obnovení pro obnovení
1. Přihlaste se k webu [Azure Portal](http://portal.azure.com/).

2. V nabídce Azure zvolte **všechny služby**. V seznamu služeb zadejte **služby Recovery Services** nebo přejděte na **úložiště** kde **trezory služby Recovery** je uvedená, vyberte ji.

    ![Trezor služby Recovery Services](./media/backup-azure-arm-restore-vms/open-recovery-services-vault1.png)

3. Zobrazí se seznam trezorů v předplatném.

    ![Trezory služby Recovery Services seznam](./media/backup-azure-arm-restore-vms/list-of-rs-vaults1.png)

4. Ze seznamu trezorů služeb zotavení vyberte trezor přidružený k virtuálnímu počítači, který chcete provést obnovení. Po výběru trezoru se otevře jeho řídicí panel.

    ![Vybraný trezor služby Recovery Services](./media/backup-azure-arm-restore-vms/select-vault-open-vault-blade1.png)

5. Na řídicím panelu trezoru na **zálohování položek** dlaždice, vyberte **virtuálního počítače Azure**.

    ![Řídicího panelu trezoru](./media/backup-azure-arm-restore-vms/vault-dashboard1.png)

6. **Zálohování položek** otevře se okno se seznamem virtuálních počítačů Azure.

    ![Seznam virtuálních počítačů v trezoru](./media/backup-azure-arm-restore-vms/list-of-vms-in-vault1.png)

7. V seznamu vyberte virtuální počítač otevřete řídicí panel. Otevře se řídicí panel virtuálního počítače do oblasti monitorování, která obsahuje **body obnovení**. Všechny operace na úrovni virtuálního počítače, jako jsou **zálohovat nyní**, **souboru obnovení**, **Zastavit zálohování** lze provádět z tohoto okna.
Ve spoustě ohledů z tohoto okna, je možné obnovení provést. Všimněte si, že toto okno obsahuje pouze posledních 30 dní body obnovení.

    - Provést obnovení pomocí bodu obnovení za posledních 30 dní, klikněte pravým tlačítkem na virtuální počítač > **obnovení virtuálního počítače**.
    - Provést obnovení pomocí bod obnovení starší než 30 dní, klikněte na odkaz v části **body obnovení**.
    - Na seznamu a filtrování virtuálních počítačů s vlastní data, klikněte na tlačítko **obnovení virtuálního počítače** v nabídce. Upravit časový rozsah pro body obnovení zobrazených pomocí filtru. Můžete také filtrovat na různé typy konzistence dat.
8. Projděte si nastavení bodu obnovení:
    - Konzistence daty se dozvíte [typu konzistence](backup-azure-vms-introduction.md#consistency-types) v bodu obnovení.
    - **Typ obnovení** ukazuje, kde je uložený bod (v účtu úložiště, v trezoru, nebo obojí. [Další informace](https://azure.microsoft.com/blog/large-disk-support/) body okamžité obnovení.

  ![Body obnovení](./media/backup-azure-arm-restore-vms/vm-blade1.png)
9. Vyberte bod obnovení.

10. Vyberte **obnovit konfiguraci**. **Obnovit konfiguraci** se otevře okno.

## <a name="choose-a-vm-restore-configuration"></a>Vyberte konfiguraci obnovení virtuálního počítače
Jakmile vyberete bod obnovení, zvolte možnost obnovit konfiguraci virtuálního počítače. Pokud chcete nakonfigurovat obnovený virtuální počítač, můžete na webu Azure portal nebo Powershellu.

### <a name="restore-options"></a>Možnosti obnovení

**Možnost** | **Podrobnosti**
--- | ---
**Vytvořit nové vytvoření virtuálního počítače** | Ekvivalent rychlé vytvoření virtuálního počítače. Získá základní virtuální počítač zprovozněný z bodu obnovení.<br/><br/> Nastavení virtuálního počítače lze upravovat jako součást procesu obnovení.
**Vytvoření nové obnovení disku** | Vytvoří virtuální počítač, který si můžete přizpůsobit jako součást procesu obnovení.<br/><br/> Tato možnost zkopíruje virtuální pevné disky do účtu úložiště, které zadáte.<br/><br/> -Účet úložiště by měl být ve stejném umístění jako trezor.<br/><br/> Pokud nemáte účet odpovídající úložiště, že které potřebujete k jejímu vytvoření.<br/><br/> Typ replikace účtu úložiště se zobrazí v závorkách. Zónově redundantní úložiště (ZRS) se nepodporuje.<br/><br/> Z účtu úložiště, buď obnovené disky připojit k existujícímu virtuálnímu počítači nebo vytvořit nový virtuální počítač z obnovených disků pomocí Powershellu.
**Nahradit stávající** | Když je tato možnost není nutné k vytvoření virtuálního počítače.<br/><br/> Musí existovat aktuálního virtuálního počítače. Pokud byl odstraněn tento parametr nelze použít.<br/><br/> Azure Backup pořídí snímek existujícího virtuálního počítače a uloží je v zadané pracovní umístění. Existující disky připojené k virtuálnímu počítači jsou potom nahrazeny vybraném bodu obnovení. Snímek vytvořený dříve se zkopíruje do trezoru a ukládá jako bod obnovení v souladu se zásadami uchovávání záloh.<br/><br/> Nahradit existující se podporuje pro nešifrované spravované virtuální počítače. Není podporováno pro nespravované disky, [zobecněný virtuální počítače](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource), nebo pro virtuální počítače [vytvořené pomocí vlastních imagí](https://azure.microsoft.com/resources/videos/create-a-custom-virtual-machine-image-in-azure-resource-manager-with-powershell/).<br/><br/> Pokud bod obnovení obsahuje víc nebo míň disky než aktuální virtuální počítač, pak počet disků v bodu obnovení pouze projeví se virtuální počítač.

> [!NOTE]
> Pokud chcete obnovit virtuální počítač pomocí pokročilé nastavení sítě, například pokud spravovaná přes nástroje pro vyrovnávání zatížení interní nebo externí nebo mít více síťových rozhraní nebo několik rezervovaných IP adres, obnovte pomocí Powershellu. [Další informace](#restore-vms-with-special-network-configurations).
> Pokud virtuální počítač Windows používá [centra licencování](../virtual-machines/windows/hybrid-use-benefit-licensing.md), použijte **vytvořit nové obnovení disku** možnost a potom pomocí Powershellu nebo obnovení šablony k vytvoření virtuálního počítače s **typ licence** nastavena na **Windows_Server**. Toto nastavení lze také použít po vytvoření virtuálního počítače.


Zadejte obnovení nastavení následujícím způsobem:

1. V **obnovení**, vyberte [bod obnovení](#select-a-restore-point-for-restore) > **obnovit konfiguraci**.
2. V **obnovit konfiguraci**, vyberte, jakým způsobem chcete obnovit virtuální počítač v souladu s nastavení shrnutá v tabulce výše.

    ![Obnovení Průvodce konfigurací](./media/backup-azure-arm-restore-vms/restore-configuration-create-new1.png)


## <a name="create-new-create-a-vm"></a>Vytvořit nové vytvoření virtuálního počítače

1. V **obnovit konfiguraci** > **vytvořit nový** > **obnovit typ**vyberte **vytvoření virtuálního počítače** .
2. V **název virtuálního počítače**, zadejte virtuální počítač, který neexistuje v odběru.
3. V **skupiny prostředků**, vyberte existující skupinu prostředků pro nový virtuální počítač, nebo vytvořte novou globálně jedinečným názvem. Pokud přiřadíte název tohoto již existující, Azure přiřadí skupinu stejný název jako virtuální počítač.
4. V **virtuální síť**, vyberte virtuální síť, ve které se umístí virtuální počítač. Zobrazí se všechny virtuální sítě přidruženou k odběru. Vyberte podsíť. První podsíť je standardně vybraná.
5. V **umístění úložiště**, zadat typ úložiště pro virtuální počítač.

    ![Obnovení Průvodce konfigurací](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard1.png)

6. V **obnovit konfiguraci**vyberte **OK**. V **obnovení**, klikněte na tlačítko **obnovení** pro aktivaci obnovení.



## <a name="create-new-restore-disks"></a>Vytvořit nové obnovení disků

1. V **obnovit konfiguraci** > **vytvořit nový** > **obnovit typ**vyberte **obnovit disky**.
2. V **skupiny prostředků**, vyberte existující skupinu prostředků pro obnovených disků, nebo vytvořte novou globálně jedinečným názvem.
3. V **účtu úložiště**, zadejte účet, do kterého chcete kopírovat virtuální pevné disky. Ujistěte se, že účet je ve stejné oblasti jako trezor.

    ![Dokončení konfigurace obnovení](./media/backup-azure-arm-restore-vms/trigger-restore-operation1.png)

4. V **obnovit konfiguraci**vyberte **OK**. V **obnovení**, klikněte na tlačítko **obnovení** pro aktivaci obnovení.
5. Po obnovení na disku, můžete provést některý z následujících k dokončení operace obnovení virtuálního počítače.

    - Šablona, která byla vygenerována jako součást operace obnovení nastavení a aktivovat nasazení virtuálního počítače. Upravit nastavení výchozí šablony a odeslání šablony pro nasazení virtuálního počítače.
    - Je možné [obnovené disky připojit](https://docs.microsoft.com/azure/virtual-machines/windows/attach-disk-ps) existujícího virtuálního počítače.
    - Je možné [vytvořit nový virtuální počítač](backup-azure-vms-automation.md#restore-an-azure-vm) z obnovených disků pomocí Powershellu.


## <a name="replace-existing-disks"></a>Nahraďte existující disky

Tuto možnost použijte, pokud chcete nahradit existující disky v aktuální virtuální počítač s bodem obnovení.

1. V **obnovit konfiguraci**, klikněte na tlačítko **nahradit stávající**.
2. V **obnovit typ**vyberte **nahradit disku/s** (bod obnovení, který nahradí existující virtuální počítač disku nebo disků).
3. V **pracovní umístění**, zadejte, kam by se měla uložit snímek aktuální spravované disky.

   ![Obnovení Průvodce konfigurací nahradit existující](./media/backup-azure-arm-restore-vms/restore-configuration-replace-existing.png)


## <a name="track-the-restore-operation"></a>Sledovat operaci obnovení
Po aktivaci operace obnovení, služba backup vytvoří úlohu pro sledování operaci obnovení. Služba backup také vytvoří a dočasně zobrazí oznámení v **oznámení** části portálu. Pokud se nezobrazí oznámení, vyberte **oznámení** symbol zobrazíte oznámení.

![Aktivuje obnovení](./media/backup-azure-arm-restore-vms/restore-notification1.png)

Klikněte na hypertextový odkaz na oznámení přejdete na **BackupJobs** seznamu. Všechny podrobnosti operací pro danou úlohu je k dispozici v **BackupJobs** seznamy. Můžete přejít na **BackupJobs** dlaždici na řídicím panelu trezoru kliknutím úlohy zálohování, vyberte **virtuálních počítačů Azure** zobrazíte úloh přidružených k trezoru.

**Úlohy zálohování** otevře se okno seznam úloh.

![Seznam virtuálních počítačů v trezoru](./media/backup-azure-arm-restore-vms/restore-job-in-progress1.png)

**Indikátor průběhu** je teď dostupná v **podrobnosti obnovení** okno. **Podrobnosti obnovení** okna můžete otevřít kliknutím na všechny úlohy obnovení, který má stav **probíhá**. **Indikátor průběhu** je k dispozici v všechny varianty obnoví jako **vytvořit nový**, **obnovení disků** a **nahradit stávající**. Podrobnosti v indikátoru průběhu obnovení jsou **odhadovaný čas obnovení**, **procento obnovení** a **počet bajtů přenesených**.

Obnovte indikátor průběhu, který podrobnosti jsou uvedeny níže:

- **Odhadovaný čas obnovení** zpočátku obsahuje čas potřebný k dokončení operace obnovení. Při operaci postupuje, doba trvání snižuje a dosáhne hodnoty 0 dokončení operace obnovení.
- **Procento obnovení** poskytuje data procentem operaci obnovení je dokončena.
- **Počet bajtů přenesených** je k dispozici v dílčí úkol, když se stane obnovení prostřednictvím vytvoření nového virtuálního počítače. Poskytuje že podrobnosti o tom, kolik počtu bajtů bylo převedeno na celkový počet bajtů, které mají být převedeny.


## <a name="use-templates-to-customize-a-restored-vm"></a>Použijte šablony k přizpůsobení obnovený virtuální počítač
Po [dokončení obnovení disků zopakovat](#Track-the-restore-operation), použijte šablonu, která byla generována jako součást operace obnovení k vytvoření nového virtuálního počítače s konfigurací totéž konfiguraci zálohování. Také vám pomůže ho přizpůsobit názvy prostředků, které byly vytvořeny během procesu vytváření nového virtuálního počítače z bodu obnovení.

![Procházení podrobností úloh obnovení](./media/backup-azure-arm-restore-vms/restore-job-drill-down1.png)

Pokud chcete získat šablonu, která byla generována jako součást možnost obnovit disky:

1. Přejděte **podrobností o úloze obnovení** odpovídající úloha.

2. Na **podrobností o úloze obnovení** obrazovky, vyberte **nasazení šablony** k inicializaci nasazení šablony.

3. Na **nasadit šablonu** okně Vlastní nasazení, použijte šablonu nasazení [upravit a pak šablonu nasaďte](../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template) nebo připojit víc úpravy podle [šablonu](../azure-resource-manager/resource-group-authoring-templates.md) před nasazením.

  ![Načtení šablony nasazení](./media/backup-azure-arm-restore-vms/edit-template1.png)

4. Jakmile zadáte požadované hodnoty, přijměte **podmínky a ujednání** a vyberte **nákupní**.

  ![Odeslání šablony nasazení](./media/backup-azure-arm-restore-vms/submitting-template1.png)

## <a name="post-restore-steps"></a>Kroky po obnovení
* Pokud používáte cloud-init-based Linuxová distribuce, jako je Ubuntu, z bezpečnostních důvodů, heslo blokovaný příspěvku obnovení. Použití rozšíření VMAccess na obnovený virtuální počítač [resetovat heslo](../virtual-machines/linux/reset-password.md). Doporučujeme používat klíče SSH v těchto distribucích, aby obnovení příspěvek obnovení hesla.
* Rozšíření, které jsou k dispozici při konfiguraci zálohování jsou nainstalovány, ale nebude povolena. Pokud narazíte na problém, přeinstalujte rozšíření.
* Pokud zálohování virtuálních počítačů má obnovení příspěvek statické IP, obnovený virtuální počítač má dynamickou IP, aby nedošlo ke konfliktu při vytváření obnovený virtuální počítač. Další informace o tom, jak [přidat statická IP adresa pro obnovený virtuální počítač](../virtual-network/virtual-networks-reserved-private-ip.md#how-to-add-a-static-internal-ip-to-an-existing-vm).
* Obnovený virtuální počítač nemá nastavenou dostupnost. Doporučujeme použít možnost obnovení disků [přidejte skupinu dostupnosti](../virtual-machines/windows/tutorial-availability-sets.md) při vytváření virtuálního počítače z Powershellu nebo šablony pomocí obnovení disků.


## <a name="backup-for-restored-vms"></a>Zálohování pro obnovený virtuální počítače
Pokud jste obnovili virtuální počítač do stejné skupiny prostředků se stejným názvem jako virtuální počítač původně zálohování, zálohování bude pokračovat na příspěvek obnovení virtuálního počítače. Pokud jste obnovili virtuálního počítače do jiné skupiny prostředků nebo se musí zadat jiný název pro obnovený virtuální počítač, virtuální počítač počítá, pokud je nový virtuální počítač. Musíte nastavit zálohování pro obnovený virtuální počítač.

## <a name="restore-a-vm-during-an-azure-datacenter-disaster"></a>Obnovení virtuálního počítače během datové centrum Azure po havárii
Azure Backup umožňuje obnovení zálohovaných virtuálních počítačů na spárovaném datovém centru, v případě dojde k havárii primární datové centrum, kde jsou virtuální počítače spuštěné a jste nakonfigurovali geograficky redundantní úložiště záloh. Průběhu scénářů vyberte účet úložiště, který je k dispozici ve spárovaném datovém centru. Zbytek procesu obnovení zůstává stejná. Zálohování pomocí výpočetní služby z geograficky spárované vytvořit obnovený virtuální počítač. Další informace najdete v tématu [odolnost proti chybám datové centrum Azure](../resiliency/resiliency-technical-guidance-recovery-loss-azure-region.md).

## <a name="restore-domain-controller-vms"></a>Obnovení virtuálních počítačů řadičů domény
Zálohování řadiče domény (DC) je podporovaný scénář se zálohováním virtuálních počítačů. Ale musíte být opatrní během procesu obnovení. Proces správné obnovení závisí na struktuře domény. V nejjednodušším případě je nutné u jednoho řadiče domény v jedné doméně. Běžně pro produkční zatížení, je nutné jednu doménu s více řadičů domény, možná některé řadiče domény v místním. Nakonec můžete mít doménovou strukturu s více doménami.

Z hlediska služby Active Directory je virtuální počítač Azure stejně jako jakýkoli jiný virtuální počítač na moderní podporovaném hypervisoru. Hlavní rozdíl oproti místní hypervisory je, že nemá žádné konzoly virtuálního počítače v Azure k dispozici. Konzola se vyžaduje pro určité scénáře, jako je obnovení pomocí úplného obnovení (BMR) – typ zálohování. Obnovení virtuálního počítače z úložiště záloh je však jako úplná náhrada za úplné obnovení systému. Adresář režimu obnovení služeb (DSRM) je k dispozici, také tak, aby byly všechny scénáře obnovení služby Active Directory přijatelné. Další informace najdete v tématu [aspekty zálohování a obnovení pro virtualizované řadiče domény](https://technet.microsoft.com/library/virtual_active_directory_domain_controller_virtualization_hyperv(v=ws.10).aspx#backup_and_restore_considerations_for_virtualized_domain_controllers) a [plánování obnovení doménové struktury služby Active Directory](https://technet.microsoft.com/library/planning-active-directory-forest-recovery(v=ws.10).aspx).

### <a name="single-dc-in-a-single-domain"></a>Jeden řadič domény v jedné doméně
Virtuální počítač je možné obnovit (stejně jako jakýkoli jiný virtuální počítač) z portálu Azure portal nebo Powershellu.

### <a name="multiple-dcs-in-a-single-domain"></a>Víc řadičů domény v jedné doméně
Po dosažení jiných řadičů domény ze stejné domény v síti řadiče domény je možné obnovit jako jakýkoli virtuální počítač. Pokud je poslední zbývající řadiče domény v doméně, nebo Probíhá obnovení v izolované síti, musí být následován postup obnovení doménové struktury.

### <a name="multiple-domains-in-one-forest"></a>Více domén v jedné doménové struktuře
Po dosažení jiných řadičů domény ze stejné domény v síti řadiče domény je možné obnovit jako jakýkoli virtuální počítač. Ve všech ostatních případech doporučujeme obnovení doménové struktury.

## <a name="restore-vms-with-special-network-configurations"></a>Obnovení virtuálních počítačů se speciální konfigurací sítě
Je možné k zálohování a obnovení virtuálních počítačů se následující speciální konfigurací sítě. Tyto konfigurace však vyžadují některé zvláštní pozornost při procházení těmito proces obnovení:

* Virtuální počítače v části nástroje pro vyrovnávání zatížení (interní a externí)
* Virtuální počítače s víc vyhrazených IP adres
* Virtuální počítače s několika síťovými kartami

> [!IMPORTANT]
> Při vytváření konfigurace speciální sítě pro virtuální počítače, musíte použít PowerShell k vytvoření virtuálních počítačů z obnovených disků.
>
>

Znovu vytvořit plně virtuální počítače po obnovení na disku, postupujte podle těchto kroků:

1. Obnovení disků ze služby Recovery Services vault pomocí [Powershellu](backup-azure-vms-automation.md#restore-an-azure-vm).

1. Vytvoření požadované konfigurace pro nástroj pro vyrovnávání zatížení virtuálních počítačů nebo několik NIC/více vyhrazené IP pomocí rutin prostředí PowerShell. Použijte ji k vytvoření virtuálního počítače s konfigurací, které chcete:

   a. Vytvoření virtuálního počítače v cloudové službě s [interního nástroje load balancer](https://azure.microsoft.com/documentation/articles/load-balancer-internal-getstarted/).

   b. Vytvoření virtuálního počítače pro připojení k [nástroje pro vyrovnávání zatížení s přístupem k Internetu](https://azure.microsoft.com/documentation/articles/load-balancer-internet-getstarted/).

   c. Vytvoření virtuálního počítače s [několik síťových karet](../virtual-machines/windows/multiple-nics.md).

   d. Vytvoření virtuálního počítače s [více vyhrazené IP adresy](../virtual-network/virtual-network-multiple-ip-addresses-powershell.md).

## <a name="next-steps"></a>Další postup
Teď můžete obnovit vaše virtuální počítače, najdete v článku řešení potíží najdete informace o běžných chybách s virtuálními počítači. Také přečtěte si článek o správu úloh pomocí vašich virtuálních počítačů.

* [Řešení potíží s chybami](backup-azure-vms-troubleshoot.md#restore)
* [Správa virtuálních počítačů](backup-azure-manage-vms.md)
