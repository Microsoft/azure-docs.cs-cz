---
title: Přesun virtuálních počítačů Azure IaaS do jiné oblasti Azure pomocí služby Azure Site Recovery | Dokumentace Microsoftu
description: Pomocí Azure Site Recovery pro přesun virtuálních počítačů Azure IaaS z jedné oblasti Azure do jiného.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 12/27/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: dc27e49cc67a902bb45b1d889bb61b1f4b3aab83
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/27/2018
ms.locfileid: "53788765"
---
# <a name="move-azure-vms-to-another-region"></a>Přesun virtuálních počítačů Azure do jiné oblasti

Kromě použití [Azure Site Recovery](site-recovery-overview.md) služby ke správě a orchestraci zotavení po havárii místních počítačů a virtuálních počítačů Azure pro účely provozní kontinuitu a zotavení po havárii (BCDR), můžete použít také lokality Obnovení pro správu přesune virtuální počítače Azure do sekundární oblasti. Pokud chcete přesunout virtuální počítače Azure, pro ně povolíte replikaci a převzít z primární oblasti do sekundární oblasti podle vašeho výběru.

V tomto kurzu se dozvíte, jak přesunout virtuální počítače Azure do jiné oblasti. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořit trezor služby Recovery Services
> * Povolit replikaci virtuálního počítače
> * Spuštění převzetí služeb při selhání pro přesun virtuálního počítače

V tomto kurzu se předpokládá, že už máte předplatné Azure. Pokud ho nemáte, tak si ze všeho nejdřív vytvořte [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).





## <a name="prerequisites"></a>Požadavky

- Ujistěte se, že máte virtuální počítače Azure v oblasti Azure, ze kterého chcete přesunout.
- Ujistěte se, že rozumíte [komponentám a architektuře řešení](azure-to-azure-architecture.md).
- Zkontrolujte [omezení podpory a požadavky](azure-to-azure-support-matrix.md).



## <a name="before-you-start"></a>Než začnete

Před nastavením replikace dokončete tyto kroky.


### <a name="verify-target-resources"></a>Ověření cílových prostředků

1. Ověřte, že vám předplatné Azure umožňuje vytvářet virtuální počítače v cílové oblasti používané ke zotavení po havárii. O povolení požadované kvóty požádejte podporu.

2. Zkontrolujte, jestli máte v rámci předplatného k dispozici dostatek prostředků pro podporu virtuálních počítačů s takovými velikostmi, které odpovídají velikostem zdrojových virtuálních počítačů. Služba Site Recovery vybere pro cílový virtuální počítač stejnou nebo nejbližší možnou velikost.


### <a name="verify-account-permissions"></a>Ověření oprávnění k účtu

Pokud jste si právě vytvořili bezplatný účet Azure, pak jste správcem předplatného. Jestliže správcem předplatného nejste, požádejte správce o přiřazení potřebných oprávnění. Pokud chcete pro nový virtuální počítač povolit replikaci, musíte mít:

1. Oprávnění k vytvoření virtuálního počítače v prostředcích Azure. Tato oprávnění má předdefinovaná role Přispěvatel virtuálních počítačů. Patří k nim:
    - Oprávnění k vytvoření virtuálního počítače ve vybrané skupině prostředků
    - Oprávnění k vytvoření virtuálního počítače ve vybrané virtuální síti
    - Oprávnění zapisovat na vybraný účet úložiště

2. Potřebujete také oprávnění ke správě operací služby Azure Site Recovery. Všechna oprávnění nutná ke správě operací služby Site Recovery v  trezoru služby Recovery Services má role Přispěvatel Site Recovery.


### <a name="verify-vm-outbound-access"></a>Ověření odchozího přístupu k virtuálním počítačům

1. Ujistěte se, že ověřovací proxy server nepoužíváte k řízení síťového připojení pro virtuální počítače chcete přesunout. 
2. Pro účely tohoto kurzu předpokládáme, že virtuální počítače, které chcete přesunout přístup k Internetu a k řízení odchozího přístupu nepoužíváte proxy server brány firewall. Pokud ano, podívejte se na požadavky, které jsou uvedené [tady](azure-to-azure-tutorial-enable-replication.md#configure-outbound-network-connectivity).

### <a name="verify-vm-certificates"></a>Ověření certifikátů virtuálních počítačů

Zkontrolujte, že jsou na virtuálních počítačích Azure, které chcete přesunout všechny nejnovější kořenové certifikáty. Pokud tam nejnovější kořenové certifikáty nejsou, nepůjde příslušný virtuální počítač kvůli omezení zabezpečení zaregistrovat ve službě Site Recovery.

- U virtuálních počítačů s Windows zajistíte přítomnost všech důvěryhodných kořenových certifikátů tím, že na ně nainstalujete všechny nejnovější aktualizace Windows. V odpojeném prostředí dodržujte při aktualizaci Windows a certifikátů standardní postupy uplatňované ve vaší organizaci.
- U virtuálních počítačů s Linuxem postupujte při získávání nejnovějších důvěryhodných kořenových certifikátů a seznamu odvolaných certifikátů na virtuálním počítači podle pokynů, které jste dostali od distributora Linuxu.



## <a name="create-a-vault"></a>Vytvoření trezoru

V libovolné oblasti (s výjimkou zdrojové oblasti) vytvořte trezor.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) > **Recovery Services**.
2. Klikněte na **Vytvořit prostředek** > **Monitorování a správa** > **Backup a Site Recovery**.
3. Do pole **Název** zadejte popisný název **ContosoVMVault**. Pokud máte více předplatných, vyberte příslušné předplatné.
4. Vytvořte skupinu prostředků **ContosoRG**.
5. Zadejte oblast Azure. Informace o tom, které oblasti jsou podporované, najdete v části s geografickou dostupností v tématu s [podrobnostmi o cenách Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
6. Pokud chcete mít možnost rychle se dostat k trezoru z řídicího panelu, klikněte na **Připnout na řídicí panel** a potom klikněte na **Vytvořit**.

   ![Nový trezor](./media/tutorial-migrate-azure-to-azure/azure-to-azure-vault.png)

Nový trezor se přidá do oblasti **Řídicí panel** v části **Všechny prostředky** a na hlavní stránku **Trezory Recovery Services**.






## <a name="select-the-source"></a>Výběr zdroje

1. Na stránce Trezory Recovery Services klikněte na **ConsotoVMVault** > **+Replikovat**.
2. V rozevíracím seznamu **Zdroj** vyberte **Azure**.
3. V rozevíracím seznamu **Umístění zdroje** vyberte zdrojovou oblast Azure, kde máte virtuální počítače aktuálně spuštěné.
4. Vyberte model nasazení Resource Manager. Potom vyberte **Zdrojová skupina prostředků**.
5. Kliknutím na **OK** uložte nastavení.


## <a name="enable-replication-for-azure-vms"></a>Povolení replikace pro virtuální počítače Azure

Služba Site Recovery načte seznam virtuálních počítačů přidružených k předplatnému a skupinu prostředků.


1. Na portálu Azure Portal klikněte na **Virtuální počítače**.
2. Vyberte virtuální počítač, který chcete přesunout. Pak klikněte na **OK**.
3. V části **Nastavení** klikněte na **Zotavení po havárii**.
4. V části **Konfigurovat zotavení po havárii** > **Cílová oblast** vyberte cílovou oblast, do které chcete replikaci provést.
5. U ostatních nastavení přijměte pro účely tohoto kurzu výchozí hodnoty.
6. Klikněte na **Povolit replikaci**. Tím se spustí úloha, která povolí replikaci pro daný virtuální počítač.

    ![Povolení replikace](media/tutorial-migrate-azure-to-azure/settings.png)

 

## <a name="run-a-failover"></a>Spuštění převzetí služeb při selhání

1. V části **Nastavení** > **Replikované položky** klikněte na požadovaný počítač a potom na **Převzetí služeb při selhání**.
2. V okně **Převzetí služeb při selhání** vyberte **Nejnovější**. Nastavení šifrovacího klíče není pro tento scénář podstatné.
3. Vyberte **Před spuštěním převzetí služeb při selhání vypnout počítač**. Služba Site Recovery se před aktivací převzetí služeb při selhání pokusí zdrojový virtuální počítač vypnout. Převzetí služeb při selhání bude pokračovat i v případě, že se vypnutí nepovede. Průběh převzetí služeb při selhání můžete sledovat na stránce **Úlohy**.
4. Zkontrolujte, že se virtuální počítač Azure zobrazuje v Azure podle očekávání.
5. V části **Replikované položky** klikněte pravým tlačítkem na virtuální počítač a pak na **Potvrdit**. Tím se dokončí proces migrace.
6. Po potvrzení klikněte na **Zakázat replikaci**.  Tím se zastaví replikace virtuálního počítače.



## <a name="next-steps"></a>Další postup

V tomto kurzu přesunout virtuální počítač Azure do jiné oblasti Azure. Teď můžete nakonfigurovat zotavení po havárii pro přesunutý virtuální počítač.

> [!div class="nextstepaction"]
> [Nastavení zotavení po havárii po migraci](azure-to-azure-quickstart.md)

