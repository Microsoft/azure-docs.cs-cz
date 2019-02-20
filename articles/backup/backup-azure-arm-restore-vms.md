---
title: 'Azure Backup: Obnovení virtuálních počítačů pomocí webu Azure portal'
description: Obnovení virtuálního počítače Azure z bodu obnovení pomocí webu Azure portal
services: backup
author: geethalakshmig
manager: vijayts
keywords: obnovení zálohy; Postup při obnovení; bod obnovení;
ms.service: backup
ms.topic: conceptual
ms.date: 02/18/2019
ms.author: geg
ms.openlocfilehash: b919adbaf665055ee19df9b9167984cc29388032
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/20/2019
ms.locfileid: "56428734"
---
# <a name="restore-azure-vms"></a>Obnovení virtuálních počítačů Azure

Tento článek popisuje, jak obnovit virtuální počítač Azure data z bodů obnovení uložených v [Azure Backup](backup-overview.md) trezory služby Recovery Services.

### <a name="restore-options"></a>Možnosti obnovení

Azure Backup poskytuje několik způsobů, jak obnovit virtuální počítač.

**Možnost obnovení** | **Podrobnosti**
--- | ---
**Vytvoření nového virtuálního počítače** | Rychle vytvoří a získá základní virtuální počítač zprovozněný z bodu obnovení.<br/><br/> Můžete zadat název virtuálního počítače, vyberte skupinu prostředků a virtuální síť (VNet), ve kterém budou umístěny a zadat typ úložiště.
**Obnovení disku** | Obnovení disku virtuálního počítače, který můžete použít k vytvoření nového virtuálního počítače.<br/><br/> Azure Backup poskytuje šablonu, která vám pomůžou stanovit a vytvoření virtuálního počítače. <br/><br/> Tato možnost zkopíruje virtuální pevné disky do účtu úložiště, které zadáte. Úloha obnovení vytvoří šablonu můžete stáhnout a použít k zadání vlastního nastavení virtuálních počítačů a vytvoření virtuálního počítače.<br/><br/> -Účet úložiště by měl být ve stejném umístění jako trezor. Pokud ho nemáte, vytvořte účet úložiště.<br/><br/> Zobrazí se typ replikace účtu úložiště. Zónově redundantní úložiště (ZRS) se nepodporuje.<br/><br/> Alternativně můžete připojte disk k existujícímu virtuálnímu počítači nebo vytvoření nového virtuálního počítače pomocí Powershellu.<br/><br/> Tato možnost je užitečná, pokud chcete přizpůsobit virtuální počítač, přidejte nastavení konfigurace, které nebyly existuje v době zálohování nebo nastavení, která musí být nakonfigurovaný pomocí šablony nebo prostředí PowerShell.
**Nahradit stávající** | Můžete obnovit disk a použít k nahrazení disku v existující virtuální počítač.<br/><br/> Musí existovat aktuálního virtuálního počítače. Pokud byl odstraněn tento parametr nelze použít.<br/><br/> Azure Backup pořídí snímek existující virtuální počítač před nahrazením disku. Snímek je uložen v pracovní umístění, které zadáte. Existující disky připojené k virtuálnímu počítači se pak nahradí pomocí bodu obnovení. Snímku je zkopírován do trezoru a zachována v souladu se zásadami uchovávání.<br/><br/> Nahradit existující se podporuje pro nešifrované spravované virtuální počítače. Není podporováno pro nespravované disky, [zobecněný virtuální počítače](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource), nebo pro virtuální počítače [vytvořené pomocí vlastních imagí](https://azure.microsoft.com/resources/videos/create-a-custom-virtual-machine-image-in-azure-resource-manager-with-powershell/).<br/><br/> Pokud bod obnovení obsahuje víc nebo míň disky než aktuální virtuální počítač, pak počet disků v bodu obnovení bude reflektovat pouze konfiguraci virtuálního počítače.



> [!NOTE]
> Můžete také obnovit určité soubory a složky na Virtuálním počítači Azure. [Další informace](backup-azure-restore-files-from-vm.md).
>
> Pokud máte spuštěný [nejnovější verzi](backup-instant-restore-capability.md) z Azure Backup pro virtuální počítače Azure (označované jako rychlé obnovení), jsou snímky uchovávat až sedm dní, obnovení virtuálního počítače ze snímků před zálohovaná data se odesílají do trezoru. Pokud chcete obnovit virtuální počítač ze zálohy z posledních sedmi dnů, je rychlejší obnovení ze snímku a ne z trezoru.


## <a name="select-a-restore-point"></a>Vyberte bod obnovení 

1. V trezoru přidružený k virtuálnímu počítači, který chcete provést obnovení, klikněte na tlačítko **zálohování položek** > **virtuálního počítače Azure**.
2. Klikněte na virtuální počítač. Ve výchozím nastavení na řídicím panelu virtuálních počítačů se zobrazí body obnovení za posledních 30 dní. Můžete zobrazit body obnovení starší než 30 dnů nebo filtru k vyhledání bodů obnovení založených na data, časových rozsahů a různé typy konzistence snímku.
3. Pokud chcete obnovit virtuální počítač, klikněte na tlačítko **obnovení virtuálního počítače**.
    ![Bod obnovení](./media/backup-azure-arm-restore-vms/restore-point.png)
4. Vyberte bod obnovení pro obnovení.



## <a name="choose-a-vm-restore-configuration"></a>Vyberte konfiguraci obnovení virtuálního počítače

1. V **obnovit konfiguraci**, vyberte možnost obnovení:
    - **Vytvořte novou**. Tuto možnost použijte, pokud chcete vytvořit nový virtuální počítač. Můžete vytvořit virtuální počítač s jednoduchým nastavením, nebo obnovení disku a vytvořit vlastní virtuální počítač.
    - **Nahradit stávající**: Tuto možnost použijte, pokud mají být nahrazeny disky na existující virtuální počítač.
        ![Obnovení Průvodce konfigurací](./media/backup-azure-arm-restore-vms/restore-configuration.png)
2. Zadejte nastavení pro vaší možnosti obnovení.

## <a name="create-new-create-a-vm"></a>Vytvořit nové vytvoření virtuálního počítače

Mezi [možnosti obnovení](#restore-options), virtuálního počítače můžete rychle vytvořit pomocí základní nastavení z bodu obnovení. 

1. V **obnovit konfiguraci** > **vytvořit nový** > **obnovit typ**vyberte **vytvoření virtuálního počítače** .
2. V **název virtuálního počítače**, zadejte virtuální počítač, který neexistuje v odběru.
3. V **skupiny prostředků**, vyberte existující skupinu prostředků pro nový virtuální počítač, nebo vytvořte novou globálně jedinečným názvem. Pokud přiřadíte název, který již existuje, Azure přiřadí skupinu stejný název jako virtuální počítač.
4. V **virtuální síť**, vyberte virtuální síť, ve které se umístí virtuální počítač. Zobrazí se všechny virtuální sítě přidruženou k odběru. Vyberte podsíť. První podsíť je standardně vybraná.
5. V **umístění úložiště**, zadat typ úložiště pro virtuální počítač.

    ![Obnovení Průvodce konfigurací](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard1.png)

6. V **obnovit konfiguraci**vyberte **OK**. V **obnovení**, klikněte na tlačítko **obnovení** pro aktivaci obnovení.


## <a name="create-new-restore-disks"></a>Vytvořit nové obnovení disků

Mezi [možnosti obnovení](#restore-options), můžete vytvořit disk z bodu obnovení. Potom s diskem, máte jednu z následujících akcí:

- Pomocí šablony, který je generován během operace obnovení nastavení a aktivovat nasazení virtuálního počítače. Upravit nastavení výchozí šablony a odeslání šablony pro nasazení virtuálního počítače.
- [Připojit obnovených disků](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal#option-2-attach-an-existing-disk) existujícího virtuálního počítače.
- [Vytvoření nového virtuálního počítače] (https://docs.microsoft.com/azure/backup/backup-azure-vms-automation#create-a-vm-from-restored-disks z obnovených disků pomocí Powershellu.


1. V **obnovit konfiguraci** > **vytvořit nový** > **obnovit typ**vyberte **obnovit disky**.
2. V **skupiny prostředků**, vyberte existující skupinu prostředků pro obnovených disků, nebo vytvořte novou globálně jedinečným názvem.
3. V **účtu úložiště**, zadejte účet, do kterého chcete kopírovat virtuální pevné disky. Ujistěte se, že účet je ve stejné oblasti jako trezor.

    ![Dokončení konfigurace obnovení](./media/backup-azure-arm-restore-vms/trigger-restore-operation1.png)

4. V **obnovit konfiguraci**vyberte **OK**. V **obnovení**, klikněte na tlačítko **obnovení** pro aktivaci obnovení.


### <a name="use-templates-to-customize-a-restored-vm"></a>Použijte šablony k přizpůsobení obnovený virtuální počítač

Po obnovení na disku, použijte šablonu, která byla vygenerována jako součást operace obnovení při přizpůsobení a vytváření nového virtuálního počítače:

1. Otevřít **podrobností o úloze obnovení** příslušné úlohy.

2. V **podrobností o úloze obnovení**vyberte **nasazení šablony** k inicializaci nasazení šablony.

    ![Procházení podrobností úloh obnovení](./media/backup-azure-arm-restore-vms/restore-job-drill-down1.png)

3. Chcete-li přizpůsobit nastavení virtuálních počítačů zadané v šabloně, klikněte na tlačítko **úpravy šablony**. Pokud chcete přidat další vlastní nastavení, klikněte na tlačítko **upravit parametry**.
    - [Další informace](../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template) o nasazení prostředků z vlastní šablony.
    - [Další informace](../azure-resource-manager/resource-group-authoring-templates.md) informace o vytváření šablon.

  ![Načtení šablony nasazení](./media/backup-azure-arm-restore-vms/edit-template1.png)

4. Zadejte vlastní hodnoty pro virtuální počítač, přijměte **podmínky a ujednání** a klikněte na tlačítko **nákupní**.

  ![Odeslání šablony nasazení](./media/backup-azure-arm-restore-vms/submitting-template1.png)


## <a name="replace-existing-disks"></a>Nahraďte existující disky

Mezi [možnosti obnovení](#restore-options), můžete nahradit stávající disk virtuálního počítače vybraném bodu obnovení. [Kontrola](#restore-options) všechny možnosti obnovení.

1. V **obnovit konfiguraci**, klikněte na tlačítko **nahradit stávající**.
2. V **obnovit typ**vyberte **nahradit disku/s**. Toto je bod obnovení, který se používá nahradit stávající disky virtuálních počítačů.
3. V **pracovní umístění**, zadejte, kam by se měla uložit snímek aktuální spravované disky.

   ![Obnovení Průvodce konfigurací nahradit existující](./media/backup-azure-arm-restore-vms/restore-configuration-replace-existing.png)
   
  
## <a name="restore-vms-with-special-network-configurations"></a>Obnovení virtuálních počítačů se speciální konfigurací sítě

Existuje mnoho běžných scénářů, ve kterých možná budete muset obnovit virtuální počítače.

**Scénář** | **Doprovodné materiály**
--- | ---
**Obnovení virtuálních počítačů pomocí programu Hybrid Use Benefit** | Pokud virtuální počítač Windows používá [výhody použití hybridních (HUB) licencování](../virtual-machines/windows/hybrid-use-benefit-licensing.md)obnovit disky a vytvořte nový virtuální počítač s použitím zadané šablony (s **typ licence** nastavena na **Windows_Server**) , nebo prostředí PowerShell.  Toto nastavení lze také použít po vytvoření virtuálního počítače.
**Obnovení virtuálních počítačů během datové centrum Azure po havárii** | Pokud úložiště používá GRS a pro virtuální počítač primární datové centrum ocitne mimo provoz, Azure Backup podporuje obnovení zálohovaných virtuálních počítačů na spárovaném datovém centru. Vyberte účet úložiště ve spárovaném datovém centru a obnovení jako obvykle. Azure Backup používá služby compute ve spárovaném umístění vytvořit obnovený virtuální počítač. [Další informace](../resiliency/resiliency-technical-guidance-recovery-loss-azure-region.md) o odolnosti proti chybám datových center.
**Obnovit jeden řadič domény virtuálního počítače v jedné doméně** | Obnovení virtuálního počítače stejně jako jakýkoli jiný virtuální počítač. Poznámky:<br/><br/> paměť ROM perspektivy Active Directory, virtuální počítač Azure je stejně jako jakýkoli jiný virtuální počítač.<br/><br/> Adresář režimu obnovení služeb (DSRM) je k dispozici, také tak, aby byly všechny scénáře obnovení služby Active Directory přijatelné. [Další informace](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/virtualized-domain-controllers-hyper-v) o zálohování a obnovení pro virtualizované řadiče domény. 
**Obnovit více virtuálních počítačů v jedné doméně řadičů domény** | f dostupné jiné řadiče domény ve stejné doméně v síti, stejně jako jakýkoli virtuální počítač se dá obnovit řadič domény. Pokud je poslední zbývající řadič domény v doméně, nebo Probíhá obnovení v izolované síti, použijte [obnovení doménové struktury](https://docs.microsoft.com/windows-server/identity/ad-ds/manage/ad-forest-recovery-single-domain-in-multidomain-recovery).
**Obnovení několika domén v jedné doménové struktuře** | Doporučujeme, abyste [obnovení doménové struktury](https://docs.microsoft.com/windows-server/identity/ad-ds/manage/ad-forest-recovery-single-domain-in-multidomain-recovery).
**Obnovení úplné obnovení systému** | Hlavní rozdíl mezi virtuálními počítači Azure a místní hypervisory je že je k dispozici v Azure bez konzoly virtuálního počítače. Konzola se vyžaduje pro určité scénáře, jako je obnovení pomocí úplného obnovení (BMR) – typ zálohování. Obnovení virtuálního počítače z trezoru je ale jako úplná náhrada za úplné obnovení systému.
**Obnovení virtuálních počítačů se speciální konfigurací sítě** | Speciální konfigurací sítě zahrnují virtuální počítače s pomocí externího nebo interního Vyrovnávání zatížení, použití více síťovými KARTAMI nebo více vyhrazených IP adres. Obnovit tyto virtuální počítače pomocí [obnovení možnost disku](#create-new-restore-disks). Tato možnost umožňuje zkopírovat virtuální pevné disky do zadaný účet úložiště, a pak můžete vytvořit virtuální počítač s [interní]https://azure.microsoft.com/documentation/articles/load-balancer-internal-getstarted/) nebo [externí]https://azure.microsoft.com/documentation/articles/load-balancer-internet-getstarted/ pro load balancer, [několik síťových karet](../virtual-machines/windows/multiple-nics.md), nebo [více vyhrazené IP adresy](../virtual-network/virtual-network-multiple-ip-addresses-powershell.md), v souladu s vaší konfigurace. 


## <a name="track-the-restore-operation"></a>Sledovat operaci obnovení
Po aktivaci operace obnovení, služba backup vytvoří úlohu pro sledování. Azure Backup se zobrazí oznámení o úloze v portálu. Pokud se nezobrazí, klikněte na **oznámení** symbol neuvidíte.

![Aktivuje obnovení](./media/backup-azure-arm-restore-vms/restore-notification1.png)
 
 Sledujte obnovení následujícím způsobem:

1. Chcete-li zobrazit operace pro úlohu, klikněte na hypertextový odkaz oznámení. Další možností v trezoru, klikněte na tlačítko **úlohy zálohování**a potom klikněte na odpovídající virtuální počítač.

    ![Seznam virtuálních počítačů v trezoru](./media/backup-azure-arm-restore-vms/restore-job-in-progress1.png)

2. Pokud chcete monitorovat průběh obnovení, klikněte na úlohu obnovení se stavem **probíhá**. Zobrazí indikátor průběhu, který zobrazuje informace o průběhu obnovení:

    - **Odhadovaný čas obnovení**: Zpočátku obsahuje čas potřebný k dokončení operace obnovení. Při operaci postupuje, doba trvání snižuje a dosáhne nuly, až se dokončí operace obnovení.
    - **Procento obnovení**. Zobrazuje procentuální podíl operaci obnovení, který se má provést.
    - **Počet bajtů přenesených**: Pokud se obnovení tak, že vytvoříte nový virtuální počítač, zobrazí počet bajtů, které byly převedeny na celkový počet bajtů, které mají být převedeny.

## <a name="post-restore-steps"></a>Kroky po obnovení

Existuje mnoho věcí, abyste Poznámka: po obnovení virtuálního počítače:

- Rozšíření, které jsou k dispozici při konfiguraci zálohování jsou nainstalovány, ale nejsou povolené. Pokud narazíte na problém, přeinstalujte rozšíření.
- Pokud zálohované virtuální počítač má statickou IP adresu, bude mít obnovený virtuální počítač s dynamickou adresou IP, aby nedošlo ke konfliktu. Je možné [přidat statickou IP adresu pro obnovený virtuální počítač](../virtual-network/virtual-networks-reserved-private-ip.md#how-to-add-a-static-internal-ip-to-an-existing-vm).
- Obnovený virtuální počítač nemá dostupnosti nastavena. Pokud organizační jednotky použít možnost obnovení disku, můžete [určete skupinu dostupnosti](../virtual-machines/windows/tutorial-availability-sets.md) při vytváření virtuálního počítače z disku s použitím zadané šablony nebo Powershellu.
* Pokud používáte cloud-init-based Linuxová distribuce, jako je Ubuntu, z bezpečnostních důvodů hesla blokovaný po obnovení. Použití rozšíření VMAccess na obnovený virtuální počítač [resetovat heslo](../virtual-machines/linux/reset-password.md). Doporučujeme používat klíče SSH na tyto distribuce, takže není nutné k resetování hesla po obnovení. 


## <a name="backing-up-restored-vms"></a>Zálohování obnovené virtuálních počítačů

- Pokud jste obnovili virtuální počítač do stejné skupiny prostředků se stejným názvem jako virtuální počítač původně zálohovanou, pokračuje v zálohování na virtuálním počítači po obnovení.
- Pokud jste obnovili virtuálního počítače do jiné skupiny prostředků nebo se musí zadat jiný název pro obnovený virtuální počítač, musíte nastavit zálohování pro obnovený virtuální počítač.



## <a name="next-steps"></a>Další postup


- Pokud zaznamenáte potíže během procesu obnovení [zkontrolujte](backup-azure-vms-troubleshoot.md#restore) běžné problémy a chyby.
- Po obnovení virtuálního počítače, přečtěte si o [správu virtuálních počítačů](backup-azure-manage-vms.md)


