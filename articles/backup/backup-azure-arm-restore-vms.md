---
title: 'Zálohování Azure: Obnovení virtuálních počítačů pomocí portálu Azure'
description: Obnovení virtuálního počítače Azure z bodu obnovení pomocí portálu Azure
services: backup
author: markgalioto
manager: carmonm
keywords: obnovení zálohy; Postup obnovení; bod obnovení;
ms.service: backup
ms.topic: conceptual
ms.date: 09/04/2017
ms.author: markgal
ms.openlocfilehash: 52ca7cf4a04ace2a43f15433c05a2db26bde3602
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34605589"
---
# <a name="use-the-azure-portal-to-restore-virtual-machines"></a>Obnovení virtuálních počítačů pomocí portálu Azure
Ochrana dat pomocí snímky dat na definovaných intervalech. Tyto snímky jsou známé jako body obnovení a byly uloženy do trezory služeb zotavení. Pokud je nutné opravit nebo znovu vytvořit virtuální počítač (VM), můžete obnovit virtuální počítač z jakýchkoli bodů obnovení uložené. Pokud obnovíte bod obnovení, můžete:

* Vytvořte nový virtuální počítač, který je reprezentace v okamžiku zálohy virtuálního počítače.
* Obnovení disky a použít šablonu, která se dodává s proces přizpůsobení obnovený virtuální počítač nebo se obnovení jednotlivých souborů. 

Tento článek vysvětluje, jak obnovit virtuální počítač na nový virtuální počítač nebo obnovit všechny disky zálohy. Obnovení jednotlivých souborů, najdete v části [obnovit soubory ze zálohy virtuálního počítače Azure](backup-azure-restore-files-from-vm.md).

![Tři způsoby, jak obnovit ze zálohy virtuálního počítače](./media/backup-azure-arm-restore-vms/azure-vm-backup-restore.png)

> [!NOTE]
> Azure má dva modely nasazení pro vytváření a práci s prostředky: [Azure Resource Manager a klasický](../azure-resource-manager/resource-manager-deployment-model.md). Tento článek obsahuje informace a postupy, které slouží k obnovení nasazeny virtuální počítače pomocí modelu Resource Manager.
>
>

Obnovení virtuálního počítače nebo všechny disky z virtuálního počítače zálohování zahrnuje dva kroky:

* Vyberte bod obnovení pro obnovení.
* Vyberte typ obnovení, vytvoření nového virtuálního počítače nebo obnovení disků a zadejte požadované parametry. 

## <a name="select-a-restore-point-for-restore"></a>Vyberte bod obnovení pro obnovení
1. Přihlaste se k [portálu Azure](http://portal.azure.com/).

2. V nabídce Azure vyberte **Procházet**. V seznamu služeb, zadejte **služeb zotavení**. Seznam služeb přizpůsobí co zadáte. Až se zobrazí **trezory služeb zotavení**, vyberte ho.

    ![Trezor služby Recovery Services](./media/backup-azure-arm-restore-vms/open-recovery-services-vault.png)

    Zobrazí se seznam trezorů v rámci předplatného.

    ![Trezory seznam služeb zotavení](./media/backup-azure-arm-restore-vms/list-of-rs-vaults.png)
3. V seznamu vyberte trezor přidružený virtuálních počítačů, které chcete obnovit. Když vyberete trezoru, otevře se jeho řídicí panel.

    ![Vybrané trezor služeb zotavení](./media/backup-azure-arm-restore-vms/select-vault-open-vault-blade.png)
4. Na řídicím panelu trezoru na **zálohování položek** dlaždice, vyberte **virtuální počítače Azure**.

    ![panelu trezoru](./media/backup-azure-arm-restore-vms/vault-dashboard.png)

    **Zálohování položek** okno otevře a zobrazí seznam virtuálních počítačích Azure.

    ![Seznam virtuálních počítačů v trezoru](./media/backup-azure-arm-restore-vms/list-of-vms-in-vault.png)
5. V seznamu vyberte virtuální počítač otevřete řídicí panel. Otevře se řídicí panel virtuálních počítačů do oblasti monitorování, který obsahuje **body obnovení** dlaždici.

    ![Body obnovení](./media/backup-azure-arm-restore-vms/vm-blade.png)
6. V nabídce virtuálního počítače řídicí panel, vyberte **obnovení**.

    ![Tlačítko Obnovit](./media/backup-azure-arm-restore-vms/vm-blade-menu-restore.png)

    **Obnovení** otevře se okno.

    ![Obnovit okno](./media/backup-azure-arm-restore-vms/restore-blade.png)
7. Na **obnovení** vyberte **bod obnovení**. **Vyberte bod obnovení** otevře se okno.

    ![Vyberte bod obnovení](./media/backup-azure-arm-restore-vms/recovery-point-selector.png)

    Ve výchozím nastavení dialogové okno zobrazí všechny body obnovení z posledních 30 dnů. Použití **filtru** změnit časový rozsah bodů obnovení zobrazí. Ve výchozím nastavení zobrazí se všechny consistencies body obnovení. Změnit **všechny body obnovení** filtru vybrat konkrétní obnovení bod konzistence. Další informace o jednotlivých typech bodu obnovení najdete v tématu [konzistenci dat](backup-azure-vms-introduction.md#data-consistency).

    Bod konzistence možnosti obnovení:

     * Body obnovení konzistentní havárií
     * Body obnovení konzistentní aplikací
     * Body obnovení konzistentní systému souborů
     * Všechny body obnovení

8. Zvolte bod obnovení a vyberte **OK**.

    ![Možnosti bodu obnovení](./media/backup-azure-arm-restore-vms/select-recovery-point.png)

    **Obnovení** okno ukazuje, že je nastavena bodu obnovení.

9. Pokud jste již existuje, přejdete do **obnovení** okno. Ujistěte se, že [je vybrán bod obnovení](#select-a-restore-point-for-restore)a vyberte **obnovit konfiguraci**. **Obnovit konfiguraci** otevře se okno.

## <a name="choose-a-vm-restore-configuration"></a>Zvolte obnovení konfigurace virtuálního počítače
Po výběru bodu obnovení, vyberte takovou konfiguraci obnovení virtuálního počítače. Ke konfiguraci obnovený virtuální počítač, můžete portál Azure nebo PowerShell.

1. Pokud jste již existuje, přejdete do **obnovení** okno. Ujistěte se, že [je vybrán bod obnovení](#select-a-restore-point-for-restore)a vyberte **obnovit konfiguraci**. **Obnovit konfiguraci** otevře se okno.

    ![Obnovení Průvodce konfigurací](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard-recovery-type.png)
2. Na **obnovit konfiguraci** okno, máte dvě možnosti:

   * **Vytvoření virtuálního počítače**

   * **Obnovení disků**

Poskytuje portálu **rychle vytvořit** možnost pro obnovený virtuální počítač. K přizpůsobení konfigurace virtuálního počítače nebo názvy prostředky vytvořené při vytváření nového virtuálního počítače volba, použijte k obnovení zálohovaných disky prostředí PowerShell nebo portálu. Připojte je k zvoleného konfigurace virtuálního počítače použijte příkazy prostředí PowerShell. Nebo můžete pomocí této šablony, která se dodává s obnovenou disky, které chcete přizpůsobit obnoveným virtuálním Počítačem. Informace o tom, jak obnovit virtuální počítač, který má několik síťových adaptérů nebo pod nástrojem pro vyrovnávání zatížení najdete v tématu [obnovit virtuální počítač s konfigurací speciální síťových](#restore-vms-with-special-network-configurations). Pokud vaše virtuální počítač Windows používá [ROZBOČOVAČE licencování](../virtual-machines/windows/hybrid-use-benefit-licensing.md), obnovení disků a prostředí PowerShell nebo šablonu uvedeného v tomto článku použít k vytvoření virtuálního počítače. Ujistěte se, zda jste zadali **typ licence** jako "Windows_Server" při vytváření virtuálního počítače využívat výhody centra obnoveným virtuálním Počítačem. 
 
## <a name="create-a-new-vm-from-a-restore-point"></a>Vytvoření nového virtuálního počítače z bodu obnovení
1. Pokud si nejste již existuje, [vyberte bod obnovení](#restore-a vm-with-special-network-configurations) předtím, než začnete vytvářet nový virtuální počítač z bodu obnovení. Až vyberete bod obnovení na **obnovit konfiguraci** okno, zadejte nebo vyberte hodnoty pro každý z těchto polí:

    a. **Obnovit typu**. Vytvoří virtuální počítač.

    b. **Název virtuálního počítače**. Zadejte název pro virtuální počítač. Název musí být jedinečný do skupiny prostředků (pro nasazení Azure Resource Manager VM) nebo cloudové služby (pro klasické virtuální počítač). Virtuální počítač nelze nahradit, pokud již existuje v rámci předplatného.

    c. **Skupina prostředků**. Použít existující skupinu prostředků nebo vytvořte novou. Pokud se obnovení klasické virtuální počítač, použijte toto pole k zadání názvu novou cloudovou službu. Pokud vytváříte novou skupinu nebo cloudovou službu prostředku, musí být globálně jedinečný název. Obvykle název cloudové služby souvisí s adresou URL veřejné: například [cloudservice]. cloudapp.net. Pokud se pokusíte použít název skupiny nebo cloudové služby prostředků cloudu, které jsou již používán, Azure přiřadí prostředků skupiny nebo cloudové služby má stejný název jako virtuální počítač. Azure zobrazí prostředků skupiny nebo cloudové služby a virtuální počítače nejsou přiřazeny žádné skupiny vztahů. Další informace najdete v tématu [jak migrovat z skupiny vztahů regionální virtuální síť](../virtual-network/virtual-networks-migrate-to-regional-vnet.md).

    d. **Virtuální síť:** Vyberte virtuální síť, při vytváření virtuálního počítače. Pole poskytuje všechny virtuální sítě přidružený k odběru. Skupina prostředků virtuálního počítače se zobrazí v závorkách.

    e. **Podsíť**. Pokud virtuální sítě má podsítí, je ve výchozím nastavení vybrán první podsíť. Pokud existují další podsítě, vyberte podsíť, kterou chcete.

    f. **Účet úložiště**. Tato nabídka uvádí účty úložiště ve stejném umístění jako trezor služeb zotavení. Účty úložiště, které jsou zónově redundantní nejsou podporovány. Pokud nejsou žádné účty úložiště s stejné umístění jako trezor služeb zotavení, je musíte vytvořit před zahájením operace obnovení. Typ replikace účtu úložiště se zobrazí v závorkách.

    ![Obnovení Průvodce konfigurací](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard.png)

    > [!NOTE]
    > * Pokud obnovujete virtuálních počítačů nasazených Resource Managerem, musíte určit virtuální sítě. Virtuální síť je volitelný pro klasické virtuální počítač.

    > * Pokud obnovujete virtuální počítače s spravované disky, ujistěte se, že vybraný účet úložiště není povolen pro šifrování služby úložiště Azure v celé jeho životnosti.

    > * Na základě úložiště typu vybrané účtu úložiště (standard nebo premium), všechny disky, obnovení bude premium nebo standardní disky. Aktuálně nepodporujeme smíšený režim disků při obnovení.
    >
    >

2. Na **obnovit konfiguraci** vyberte **OK** pro dokončení konfigurace obnovení. Na **obnovení** vyberte **obnovení** pro spuštění operace obnovení.

## <a name="restore-backed-up-disks"></a>Obnovení zálohovaných disky
Přizpůsobení virtuálního počítače, kterou chcete vytvořit z disků zálohovaná liší od co se nachází ve **obnovit konfiguraci** vyberte **obnovit disky** hodnotu **obnovit typ**. Tato volba požádá o účet úložiště, které mají být zkopírovány disky ze zálohy. Pokud si zvolíte účet úložiště, vyberte účet, který sdílí stejné umístění jako trezor služeb zotavení. Účty úložiště, které jsou zónově redundantní nejsou podporovány. Pokud nejsou žádné účty úložiště s stejné umístění jako trezor služeb zotavení, je musíte vytvořit před zahájením operace obnovení. Typ replikace účtu úložiště se zobrazí v závorkách.

Po dokončení operace obnovení můžete provést tyto akce:

* [Šablona slouží k přizpůsobení obnoveným virtuálním Počítačem.](#use-templates-to-customize-restore-vm)
* [Použijte pro připojení k existující virtuální počítač obnovený disky](../virtual-machines/windows/attach-managed-disk-portal.md)
* [Vytvoření nového virtuálního počítače z obnovené disky pomocí prostředí PowerShell](./backup-azure-vms-automation.md#restore-an-azure-vm)

Na **obnovit konfiguraci** vyberte **OK** pro dokončení konfigurace obnovení. Na **obnovení** vyberte **obnovení** pro spuštění operace obnovení.

![Konfigurace obnovení byla dokončena](./media/backup-azure-arm-restore-vms/trigger-restore-operation.png)

## <a name="track-the-restore-operation"></a>Sledování operací obnovení
Po operaci obnovení spustíte, služba backup vytvoří úlohu pro sledování operaci obnovení. Služba backup také vytvoří a dočasně zobrazí oznámení v **oznámení** oblasti portálu. Pokud se nezobrazí oznámení, vyberte **oznámení** symbol, který chcete zobrazit oznámení.

![Spustí obnovení](./media/backup-azure-arm-restore-vms/restore-notification.png)

Chcete-li zobrazit operaci, když je zpracování nebo k jejímu zobrazení po dokončení otevřete **zálohování úloh** seznamu.

1. V nabídce Azure vyberte **Procházet**a v seznamu služeb, zadejte **služeb zotavení**. Seznam služeb přizpůsobí co zadáte. Až se zobrazí **trezory služeb zotavení**, vyberte ho.

    ![Otevřený trezor služeb zotavení](./media/backup-azure-arm-restore-vms/open-recovery-services-vault.png)

    Zobrazí se seznam trezorů v rámci předplatného.

    ![Trezory seznam služeb zotavení](./media/backup-azure-arm-restore-vms/list-of-rs-vaults.png)
2. V seznamu vyberte trezor spojené s virtuálním Počítačem můžete obnovit. Když vyberete trezoru, otevře se jeho řídicí panel.

3. V řídícím panelu trezoru na **úlohy zálohování** dlaždice, vyberte **virtuální počítače Azure** zobrazí úlohy přidruženého k úložišti.

    ![panelu trezoru](./media/backup-azure-arm-restore-vms/vault-dashboard-jobs.png)

    **Zálohování úloh** okno otevře a zobrazí se seznam úloh.

    ![Seznam virtuálních počítačů v trezoru](./media/backup-azure-arm-restore-vms/restore-job-in-progress.png)
    
## <a name="use-templates-to-customize-a-restored-vm"></a>Použití šablon k přizpůsobení obnoveným virtuálním Počítačem.
Po [po dokončení operace obnovení disky](#Track-the-restore-operation), použijte šablonu, která byla vygenerována v rámci operace obnovení k vytvoření nového virtuálního počítače s konfigurací liší od konfigurace zálohy. Také můžete jej přizpůsobit názvy prostředků, které byly vytvořeny během procesu vytváření nového virtuálního počítače z bodu obnovení. 

> [!NOTE]
> Šablony jsou přidány jako součást obnovení disky pro body obnovení po 1. března 2017 provedeny. Jsou dostupná pro nespravovaná disku virtuálních počítačů. Podpora pro spravovaných disků na virtuální počítače se připravuje a v budoucích verzích. 
>
>

Pokud chcete získat šablony, který byl vytvořen jako součást obnovení možnost disky:

1. Přejděte na podrobnosti úlohy obnovení odpovídající úlohy.

2. Na **podrobností o úloze obnovení** obrazovku, vyberte **nasazení šablony** k inicializaci nasazení šablony. 

     ![obnovit úlohu procházení](./media/backup-azure-arm-restore-vms/restore-job-drill-down.png)
   
3. Na **nasazení šablony** okně Vlastní nasazení, použijte nasazení šablony do [upravit a nasadit šablonu](../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template) nebo připojit další přizpůsobení podle [šablonu](../azure-resource-manager/resource-group-authoring-templates.md) před nasazením. 

   ![Nasazení šablony zatížení](./media/backup-azure-arm-restore-vms/loading-template.png)
   
4. Po zadání požadované hodnoty, přijměte **podmínky a ujednání** a vyberte **nákupu**.

   ![Odeslání nasazení šablony](./media/backup-azure-arm-restore-vms/submitting-template.png)

## <a name="post-restore-steps"></a>Po obnovení kroky
* Pokud používáte cloudové na init Linux distribuci, jako je například Ubuntu, z bezpečnostních důvodů, je blokován heslo post obnovení. Použití rozšíření VMAccess na obnovený virtuální počítač [resetování hesla](../virtual-machines/linux/reset-password.md). Doporučujeme, abyste pomocí klíče SSH na tyto distribuce, aby se zabránilo resetování post obnovení hesla.
* Při konfiguraci zálohy rozšíření jsou nainstalovány, ale nebude povolen. Pokud se problém, přeinstalujte rozšíření. 
* Pokud zálohované virtuální počítač má statickou IP post obnovení, obnovený virtuální počítač má dynamické IP, aby se zabránilo konfliktu při vytváření obnoveným virtuálním Počítačem. Další informace o tom, jak můžete [přidat statickou IP adresu pro virtuální počítač obnovený](../virtual-network/virtual-networks-reserved-private-ip.md#how-to-add-a-static-internal-ip-to-an-existing-vm).
* Obnovený virtuální počítač nemá hodnotu dostupnosti nastavit. Doporučujeme použít možnost obnovení disky [přidejte skupinu dostupnosti](../virtual-machines/windows/tutorial-availability-sets.md) při vytváření virtuálního počítače z prostředí PowerShell nebo šablon pomocí obnovit disky. 


## <a name="backup-for-restored-vms"></a>Zálohování pro obnovený virtuální počítače
Pokud jste virtuální počítač obnovit do stejné skupiny prostředků se stejným názvem jako virtuální počítač původně zálohovaná, zálohování bude pokračovat na post obnovení virtuálního počítače. Pokud obnovit virtuální počítač do jiné skupině prostředků nebo zadat jiný název pro obnovený virtuální počítač, virtuální počítač považuje, pokud je nový virtuální počítač. Musíte nastavit zálohování pro obnovený virtuální počítač.

## <a name="restore-a-vm-during-an-azure-datacenter-disaster"></a>Obnovení virtuálního počítače během datové centrum Azure po havárii
Zálohování Azure umožňuje obnovení zálohy virtuálních počítačů na spárovaném datovém centru, v případě, že primární datové centrum, kde jsou virtuální počítače spuštěné, dojde k havárii a jste nakonfigurovali jako geograficky redundantní úložiště záloh. Během takových scénářů vyberte účet úložiště, který se nachází ve spárovaném datovém centru. Zbytek procesu obnovení zůstává stejná. Zálohování používá výpočetní služba z spárované geograficky vytvořte obnovený virtuální počítač. Další informace najdete v tématu [datové centrum Azure odolnosti](../resiliency/resiliency-technical-guidance-recovery-loss-azure-region.md).

## <a name="restore-domain-controller-vms"></a>Obnovení řadiče domény virtuální počítače
Zálohování řadiče domény (DC) je podporované scénáře s zálohování virtuálních počítačů. Ale musíte být opatrní během procesu obnovení. Proces obnovení správný závisí na strukturu domény. V nejjednodušším případě máte u jednoho řadiče domény v jedné doméně. Běžně pro produkční zatížení, máte na jednu doménu s více řadičů domény, případně s některé řadiče domény na místě. Nakonec můžete mít doménová struktura s více domén. 

Z hlediska Active Directory je virtuální počítač Azure jako další virtuální počítač na moderní podporovaném hypervisoru. Hlavní rozdíl s místními hypervisory je, že je v Azure k dispozici žádné konzoly virtuálního počítače. Konzola je vyžadována pro určité scénáře, jako je obnovení pomocí obnovení úplné obnovení (BMR) – typ zálohování. Obnovení virtuálního počítače z trezoru záloh je však úplné nahrazení pro úplné obnovení systému. Directory režimu obnovení služeb (DSRM) je také k dispozici, proto všechny scénáře obnovení služby Active Directory je přijatelná. Další informace najdete v tématu [aspekty zálohování a obnovení pro virtualizované řadiče domény](https://technet.microsoft.com/library/virtual_active_directory_domain_controller_virtualization_hyperv(v=ws.10).aspx#backup_and_restore_considerations_for_virtualized_domain_controllers) a [plánování obnovení doménové struktury služby Active Directory](https://technet.microsoft.com/library/planning-active-directory-forest-recovery(v=ws.10).aspx).

### <a name="single-dc-in-a-single-domain"></a>Jednoho řadiče domény v jedné doméně
Virtuální počítač může být obnovena (např. žádné jiné virtuální počítače) z portálu Azure nebo pomocí prostředí PowerShell.

### <a name="multiple-dcs-in-a-single-domain"></a>Víc řadičů domény v jedné doméně
Pokud jiné řadiče domény do stejné domény dostupný přes síť, můžete je obnovit řadič domény jako žádné virtuální počítače. Pokud je poslední zbývající řadič domény v doméně, nebo se provádí obnovení v izolované síti, musí být sledována procedury obnovení doménové struktury.

### <a name="multiple-domains-in-one-forest"></a>V jedné doménové struktuře víc domén
Pokud jiné řadiče domény do stejné domény dostupný přes síť, můžete je obnovit řadič domény jako žádné virtuální počítače. Ve všech ostatních případech doporučujeme obnovení doménové struktury.

## <a name="restore-vms-with-special-network-configurations"></a>Obnovení virtuálních počítačů s zvláštní síťové konfigurace
Je možné k zálohování a obnovení virtuálních počítačů s následující zvláštní síťové konfigurace. Tyto konfigurace však vyžadují některé zvláštní pozornost při procházení v procesu obnovení:

* Virtuálních počítačů v rámci služby Vyrovnávání zatížení (interních a externích)
* Virtuální počítače s víc vyhrazených IP adres
* Virtuální počítače s více síťovými kartami

> [!IMPORTANT]
> Při vytváření konfigurace speciální sítě pro virtuální počítače, musíte použít PowerShell k vytvoření virtuálních počítačů z obnovené disků.
>
>

Pokud chcete znovu vytvořit plně virtuální počítače po obnovení na disku, postupujte takto:

1. Obnovení disky ze služeb zotavení trezoru pomocí [prostředí PowerShell](backup-azure-vms-automation.md#restore-an-azure-vm).

2. Vytvoření požadované konfigurace pro nástroj pro vyrovnávání zatížení virtuálního počítače nebo několik síťový adaptér nebo více vyhrazené IP adresy pomocí rutin prostředí PowerShell. Použijte k vytvoření virtuálního počítače s konfigurací, které chcete:

   a. Vytvoření virtuálního počítače v rámci cloudové služby s [nástroj pro vyrovnávání zatížení pro vnitřní](https://azure.microsoft.com/documentation/articles/load-balancer-internal-getstarted/).

   b. Vytvoření virtuálního počítače pro připojení k [nástroj pro vyrovnávání zatížení internetového](https://azure.microsoft.com/documentation/articles/load-balancer-internet-getstarted/).

   c. Vytvoření virtuálního počítače s [několik síťových adaptérů](https://azure.microsoft.com/documentation/articles/virtual-networks-multiple-nics/).

   d. Vytvoření virtuálního počítače s [více vyhrazené IP adresy](https://azure.microsoft.com/documentation/articles/virtual-networks-reserved-public-ip/).

## <a name="next-steps"></a>Další postup
Teď, když obnovíte virtuální počítače, najdete v článku Poradce při potížích informace na běžné chyby s virtuálními počítači. Navíc najdete v článku na správu úloh pomocí vašich virtuálních počítačů.

* [Řešení potíží s chybami](backup-azure-vms-troubleshoot.md#restore)
* [Správa virtuálních počítačů](backup-azure-manage-vms.md)
