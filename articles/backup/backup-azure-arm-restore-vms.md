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
ms.openlocfilehash: eb47b1d8d3f2859b2b5c0b79633b2d37e5a40756
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2018
ms.locfileid: "44380161"
---
# <a name="use-the-azure-portal-to-restore-virtual-machines"></a>Obnovení virtuálních počítačů pomocí webu Azure portal
Chraňte vaše data pořizování snímků dat definovaných intervalech. Tyto snímky jsou označovány jako body obnovení a jsou uloženy v trezorech služby Recovery Services. Pokud je nutné opravit nebo znovu vytvořit virtuální počítač (VM), můžete obnovit virtuální počítač z některé z bodů obnovení uložených. Při obnovení bodu obnovení můžete provést tyto akce:

* Vytvořte nový virtuální počítač, který je reprezentace vašeho virtuálního počítače zálohovanou bodu v čase.
* Obnovit disky a použít šablonu, která se dodává s procesem přizpůsobení obnovený virtuální počítač nebo provést obnovení jednotlivých souborů. 

Tento článek vysvětluje postup obnovení virtuálního počítače do nového virtuálního počítače nebo obnovení všech disků zálohovaných. Obnovení jednotlivých souborů, najdete v části [obnovení souborů ze zálohy virtuálního počítače Azure](backup-azure-restore-files-from-vm.md).

![Tři způsoby, jak obnovit ze zálohy virtuálního počítače](./media/backup-azure-arm-restore-vms/azure-vm-backup-restore.png)

> [!NOTE]
> Azure nabízí dva modely nasazení pro vytváření a práci s prostředky: [Azure Resource Manageru a Klasický model](../azure-resource-manager/resource-manager-deployment-model.md). Tento článek obsahuje informace a postupy použít k obnovení nasazení virtuálních počítačů pomocí modelu Resource Manager.
>
>

Obnovení virtuálního počítače nebo všechny disky z virtuálního počítače záloha zahrnuje dva kroky:

* Vyberte bod obnovení pro obnovení.
* Vybrat typ obnovení, vytvořit nový virtuální počítač nebo obnovte disky a zadejte požadované parametry. 

## <a name="select-a-restore-point-for-restore"></a>Vyberte bod obnovení pro obnovení
1. Přihlaste se k [portálu Azure](http://portal.azure.com/).

1. V nabídce Azure zvolte **Procházet**. V seznamu služeb zadejte **služby Recovery Services**. Seznam služeb se přizpůsobí, co zadáte. Když se zobrazí **trezory služby Recovery Services**, vyberte ji.

    ![Trezor služby Recovery Services](./media/backup-azure-arm-restore-vms/open-recovery-services-vault.png)

    Zobrazí se seznam trezorů v předplatném.

    ![Trezory služby Recovery Services seznam](./media/backup-azure-arm-restore-vms/list-of-rs-vaults.png)
1. V seznamu vyberte trezor přidružený k virtuálnímu počítači, který chcete provést obnovení. Po výběru trezoru se otevře jeho řídicí panel.

    ![Vybraný trezor služby Recovery Services](./media/backup-azure-arm-restore-vms/select-vault-open-vault-blade.png)
1. Na řídicím panelu trezoru na **zálohování položek** dlaždice, vyberte **Azure Virtual Machines**.

    ![Řídicího panelu trezoru](./media/backup-azure-arm-restore-vms/vault-dashboard.png)

    **Zálohování položek** okně se otevře a zobrazí seznam virtuálních počítačů Azure.

    ![Seznam virtuálních počítačů v trezoru](./media/backup-azure-arm-restore-vms/list-of-vms-in-vault.png)
1. V seznamu vyberte virtuální počítač otevřete řídicí panel. Otevře se řídicí panel virtuálního počítače do oblasti monitorování, která obsahuje **body obnovení** dlaždici.

    ![Body obnovení](./media/backup-azure-arm-restore-vms/vm-blade.png)
1. V nabídce řídicího panelu virtuálního počítače, vyberte **obnovení**.

    ![Tlačítko Obnovit](./media/backup-azure-arm-restore-vms/vm-blade-menu-restore.png)

    **Obnovení** se otevře okno.

    ![Obnovit okno](./media/backup-azure-arm-restore-vms/restore-blade.png)
1. Na **obnovení** okně vyberte **bod obnovení**. **Vyberte bod obnovení** se otevře okno.

    ![Vyberte bod obnovení.](./media/backup-azure-arm-restore-vms/recovery-point-selector.png)

    Ve výchozím nastavení dialogové okno zobrazí všechny body obnovení za posledních 30 dní. Použití **filtr** změnit časový rozsah bodů obnovení zobrazí. Ve výchozím nastavení se zobrazují všechny už body obnovení. Upravit **všechny body obnovení** filtr k výběru konzistence bodu obnovení konkrétní. Další informace o jednotlivých typech bodu obnovení najdete v tématu [konzistence dat](backup-azure-vms-introduction.md#data-consistency).

    Možnosti konzistence bodu obnovení:

     * Selhání body obnovení konzistentní vzhledem k aplikacím
     * Body obnovení konzistentní aplikací
     * Body obnovení konzistentní systému souborů
     * Všechny body obnovení

1. Zvolte bod obnovení a vyberte **OK**.

    ![Možnosti bodu obnovení](./media/backup-azure-arm-restore-vms/select-recovery-point.png)

    **Obnovení** okno, že je nastavena bod obnovení.

1. Pokud jste ještě existuje, přejdete do **obnovení** okno. Ujistěte se, že [vybraný bod obnovení](#select-a-restore-point-for-restore)a vyberte **obnovit konfiguraci**. **Obnovit konfiguraci** se otevře okno.

## <a name="choose-a-vm-restore-configuration"></a>Vyberte konfiguraci obnovení virtuálního počítače
Jakmile vyberete bod obnovení, zvolte možnost obnovit konfiguraci virtuálního počítače. Pokud chcete nakonfigurovat obnovený virtuální počítač, můžete na webu Azure portal nebo Powershellu.

1. Pokud jste ještě existuje, přejdete do **obnovení** okno. Ujistěte se, že [vybraný bod obnovení](#select-a-restore-point-for-restore)a vyberte **obnovit konfiguraci**. **Obnovit konfiguraci** se otevře okno.

    ![Obnovení Průvodce konfigurací](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard-recovery-type.png)
1. Na **obnovit konfiguraci** okno, máte dvě možnosti:

   * **Vytvoření virtuálního počítače**

   * **Obnovit disky**

Portál poskytuje **rychlé vytvoření** možnost pro obnovený virtuální počítač. K přizpůsobení konfigurace virtuálního počítače nebo názvy prostředků vytvořených jako součást vytváření nové výběr VM, obnovte zálohovanou disky pomocí Powershellu nebo na portálu. Použijte příkazy prostředí PowerShell pro připojení k podle vašeho výběru konfigurace virtuálního počítače. Nebo můžete použít šablonu, která se dodává s obnovených disků k přizpůsobení obnovený virtuální počítač. Informace o tom, jak obnovit virtuální počítač, který má několik síťových karet nebo je v části nástroje pro vyrovnávání zatížení najdete v tématu [obnovit virtuální počítač se speciální konfigurací sítě](#restore-vms-with-special-network-configurations). Pokud váš virtuální počítač Windows používá [centra licencování](../virtual-machines/windows/hybrid-use-benefit-licensing.md), obnovte disky a pomocí Powershellu/šablony uvedená v tomto článku můžete vytvořit virtuální počítač. Ověřte, že jste zadali **typ licence** jako "Windows_Server" při vytváření virtuálního počítače, pokud chcete využít výhod centra na obnovený virtuální počítač. 
 
## <a name="create-a-new-vm-from-a-restore-point"></a>Vytvořit nový virtuální počítač z bodu obnovení
1. Pokud si nejste již existuje, [vyberte bod obnovení](#restore-a vm-with-special-network-configurations) předtím, než začnete vytvářet nový virtuální počítač z bodu obnovení. Až vyberete bod obnovení **obnovit konfiguraci** okně zadejte nebo vyberte hodnoty pro každou z těchto polí:

    a. **Typ obnovení**. Vytvoří virtuální počítač.

    b. **Název virtuálního počítače**. Zadejte název pro virtuální počítač. Název musí být jedinečné pro skupinu prostředků (pro virtuální počítač nasazení Azure Resource Manageru) nebo cloudové služby (v případě klasického virtuálního počítače). Virtuální počítač nelze nahradit, pokud již existuje v rámci předplatného.

    c. **Skupina prostředků**. Použít existující skupinu prostředků nebo vytvořte novou. Pokud při obnovování klasického virtuálního počítače, použijte toto pole k zadání názvu nové cloudové služby. Pokud vytváříte novou skupinu nebo cloudovou službu prostředku, musí být globálně jedinečný název. Název cloudové služby je obvykle spojené s veřejnou adresu URL: například [cloudservice]. cloudapp.net. Pokud se pokusíte použít název skupiny nebo cloudových služeb cloudových prostředků, které jsou již používá, Azure přiřadí prostředku skupiny nebo cloudových služeb se stejným názvem jako virtuální počítač. Azure se zobrazí v prostředku skupiny/cloud services a virtuální počítače nejsou přidružené žádné skupiny vztahů. Další informace najdete v tématu [migrace ze skupiny vztahů do regionální virtuální síť](../virtual-network/virtual-networks-migrate-to-regional-vnet.md).

    d. **Virtuální síť:** Při vytváření virtuálního počítače, vyberte virtuální síť. Pole obsahuje všechny virtuální sítě přidruženou k odběru. Skupinu prostředků virtuálního počítače se zobrazí v závorkách.

    e. **Podsíť**. Pokud má virtuální síť podsítí, je standardně vybraná první podsíť. Pokud existují další podsítě, vyberte podsíť, ve které chcete.

    f. **Účet úložiště**. Tato nabídka obsahuje seznam účtů úložiště ve stejném umístění jako trezor služby Recovery Services. Účty úložiště, které nejsou zónově redundantní nejsou podporovány. Pokud nejsou žádné účty úložiště pomocí stejného umístění jako trezor služby Recovery Services, můžete třeba vytvořit před spuštěním operace obnovení. Typ replikace účtu úložiště se zobrazí v závorkách.

    ![Obnovení Průvodce konfigurací](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard.png)

    > [!NOTE]
    > * Pokud obnovujete virtuálních počítačů nasazených Resource Managerem, musíte určit virtuální sítě. Virtuální síť je nepovinné pro klasický virtuální počítač.

    > * Pokud obnovujete virtuální počítače se spravovanými disky, ujistěte se, že vybraný účet úložiště není povolen pro šifrování služby Azure Storage v průběhu své životnosti.

    > * Podle typu úložiště vybraný účet úložiště (premium nebo standard), obnovit všechny disky budou disky úrovně standard nebo premium. Aktuálně nepodporujeme smíšený režim disků po obnovení.
    >
    >

1. Na **obnovit konfiguraci** okně vyberte **OK** dokončete konfiguraci obnovení. Na **obnovení** okně vyberte **obnovení** pro aktivaci obnovení.

## <a name="restore-backed-up-disks"></a>Obnovte zálohovanou disky
K přizpůsobení virtuálního počítače, které chcete vytvořit z disků zálohovaných liší od co je součástí **obnovit konfiguraci** okno, vyberte **obnovit disky** hodnotu **obnovit typ**. Tato volba vyzve k zadání účtu úložiště, ve kterém mají být zkopírovány disků ze záloh. Pokud si zvolíte účet úložiště, vyberte účet, který sdílí stejné umístění jako trezor služby Recovery Services. Účty úložiště, které nejsou zónově redundantní nejsou podporovány. Pokud nejsou žádné účty úložiště pomocí stejného umístění jako trezor služby Recovery Services, můžete třeba vytvořit před spuštěním operace obnovení. Typ replikace účtu úložiště se zobrazí v závorkách.

Po dokončení operace obnovení, můžete:

* [Použití šablony k přizpůsobení obnovený virtuální počítač](#use-templates-to-customize-restore-vm)
* [Slouží k připojení k existujícímu virtuálnímu počítači obnovených disků](../virtual-machines/windows/attach-managed-disk-portal.md)
* [Vytvoření nového virtuálního počítače pomocí prostředí PowerShell z obnovených disků](./backup-azure-vms-automation.md#restore-an-azure-vm)

Na **obnovit konfiguraci** okně vyberte **OK** dokončete konfiguraci obnovení. Na **obnovení** okně vyberte **obnovení** pro aktivaci obnovení.

![Dokončení konfigurace obnovení](./media/backup-azure-arm-restore-vms/trigger-restore-operation.png)

## <a name="track-the-restore-operation"></a>Sledovat operaci obnovení
Po aktivaci operace obnovení, služba backup vytvoří úlohu pro sledování operaci obnovení. Služba backup také vytvoří a dočasně zobrazí oznámení v **oznámení** části portálu. Pokud se nezobrazí oznámení, vyberte **oznámení** symbol zobrazíte oznámení.

![Aktivuje obnovení](./media/backup-azure-arm-restore-vms/restore-notification.png)

Chcete-li zobrazit operaci zpracování, nebo ji zobrazit až po dokončení, otevřete **úlohy zálohování** seznamu.

1. V nabídce Azure zvolte **Procházet**a v seznamu služeb zadejte **služby Recovery Services**. Seznam služeb se přizpůsobí, co zadáte. Když se zobrazí **trezory služby Recovery Services**, vyberte ji.

    ![Otevřete trezor služby Recovery Services](./media/backup-azure-arm-restore-vms/open-recovery-services-vault.png)

    Zobrazí se seznam trezorů v předplatném.

    ![Trezory služby Recovery Services seznam](./media/backup-azure-arm-restore-vms/list-of-rs-vaults.png)
1. V seznamu vyberte trezor přidružený k virtuálnímu počítači jste obnovili. Po výběru trezoru se otevře jeho řídicí panel.

1. Na řídicím panelu trezoru na **úlohy zálohování** dlaždice, vyberte **virtuálních počítačů Azure** zobrazíte úloh přidružených k trezoru.

    ![Řídicího panelu trezoru](./media/backup-azure-arm-restore-vms/vault-dashboard-jobs.png)

    **Úlohy zálohování** otevře se okno seznam úloh.

    ![Seznam virtuálních počítačů v trezoru](./media/backup-azure-arm-restore-vms/restore-job-in-progress.png)
    
## <a name="use-templates-to-customize-a-restored-vm"></a>Použijte šablony k přizpůsobení obnovený virtuální počítač
Po [dokončení obnovení disků zopakovat](#Track-the-restore-operation), použijte šablonu, která byla generována jako součást operace obnovení k vytvoření nového virtuálního počítače s konfigurací totéž konfiguraci zálohování. Také vám pomůže ho přizpůsobit názvy prostředků, které byly vytvořeny během procesu vytváření nového virtuálního počítače z bodu obnovení. 

> [!NOTE]
> Šablony jsou přidány jako součást obnovení disků pro body obnovení po 1. března 2017. Jsou to platí pro nespravovaná disků virtuálních počítačů. Podpora pro virtuální počítače se chystají v nadcházejících vydáních. 
>
>

Pokud chcete získat šablonu, která byla generována jako součást možnost obnovit disky:

1. Přejdete na podrobnosti o úloze obnovení odpovídající úloha.

1. Na **podrobností o úloze obnovení** obrazovky, vyberte **nasazení šablony** k inicializaci nasazení šablony. 

     ![Procházení podrobností úloh obnovení](./media/backup-azure-arm-restore-vms/restore-job-drill-down.png)
   
1. Na **nasadit šablonu** okně Vlastní nasazení, použijte šablonu nasazení [upravit a pak šablonu nasaďte](../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template) nebo připojit víc úpravy podle [šablonu](../azure-resource-manager/resource-group-authoring-templates.md) před nasazením. 

   ![Načtení šablony nasazení](./media/backup-azure-arm-restore-vms/loading-template.png)
   
1. Jakmile zadáte požadované hodnoty, přijměte **podmínky a ujednání** a vyberte **nákupní**.

   ![Odeslání šablony nasazení](./media/backup-azure-arm-restore-vms/submitting-template.png)

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

   c. Vytvoření virtuálního počítače s [několik síťových karet](https://azure.microsoft.com/documentation/articles/virtual-networks-multiple-nics/).

   d. Vytvoření virtuálního počítače s [více vyhrazené IP adresy](https://azure.microsoft.com/documentation/articles/virtual-networks-reserved-public-ip/).

## <a name="next-steps"></a>Další postup
Teď můžete obnovit vaše virtuální počítače, najdete v článku řešení potíží najdete informace o běžných chybách s virtuálními počítači. Také přečtěte si článek o správu úloh pomocí vašich virtuálních počítačů.

* [Řešení potíží s chybami](backup-azure-vms-troubleshoot.md#restore)
* [Správa virtuálních počítačů](backup-azure-manage-vms.md)
