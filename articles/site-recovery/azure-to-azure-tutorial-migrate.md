---
title: Přesunutí virtuálních počítačů Azure do jiné oblasti pomocí Azure Site Recovery
description: Pomocí Azure Site Recovery přesuňte virtuální počítače Azure IaaS z jedné oblasti Azure do jiné.
services: site-recovery
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: 4882206692c334d6ab6af28feb5d2cba5277eea1
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "78303932"
---
# <a name="move-azure-vms-to-another-region"></a>Přesun virtuálních počítačů Azure do jiné oblasti

Existují různé scénáře, ve kterých byste chtěli přesunout vaše stávající virtuální počítače Azure IaaS (VMs) z jedné oblasti do druhé. Chcete například zlepšit spolehlivost a dostupnost stávajících virtuálních počítačů, zlepšit možnosti správy nebo přesunout z důvodů zásad správného řízení. Další informace najdete v tématu [přehled přesunutí virtuálního počítače Azure](azure-to-azure-move-overview.md). 

Službu [Azure Site Recovery](site-recovery-overview.md) můžete použít ke správě a orchestraci zotavení po havárii místních počítačů a virtuálních počítačů Azure pro kontinuitu podnikání a zotavení po havárii (BCDR). Obnovení webu můžete také použít ke správě přesunu virtuálních počítačů Azure do sekundární oblasti.

V tomto kurzu provedete následující:

> [!div class="checklist"]
> 
> * Ověření předpokladů pro přesun
> * Příprava zdrojových virtuálních počítačů a cílové oblasti
> * Kopírování dat a povolení replikace
> * Otestujte konfiguraci a proveďte přesun
> * Odstranění prostředků ve zdrojové oblasti
> 
> [!NOTE]
> Tento kurz ukazuje, jak přesunout virtuální počítače Azure z jedné oblasti do druhé, jak je. Pokud potřebujete zlepšit dostupnost přesunutím virtuálních počítače v dostupnosti nastavených na zóny připnuté virtuální počítače v jiné oblasti, přečtěte [si tématu Přesunutí virtuálních počítačů Azure do zóny dostupnosti kurzu](move-azure-vms-avset-azone.md).

## <a name="prerequisites"></a>Požadavky

- Ujistěte se, že virtuální počítače Azure jsou v oblasti Azure, ze které chcete přesunout.
- Ověřte, zda je podporována vaše volba [zdrojové oblasti – kombinace cílové oblasti](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support), a proveďte informované rozhodnutí o cílové oblasti.
- Ujistěte se, že rozumíte [komponentám a architektuře řešení](azure-to-azure-architecture.md).
- Zkontrolujte [omezení podpory a požadavky](azure-to-azure-support-matrix.md).
- Ověřte oprávnění účtu. Pokud jste si vytvořili bezplatný účet Azure, jste správcem předplatného. Pokud nejste správce předplatného, spolupracujte se správcem a přiřaďte oprávnění, která potřebujete. Chcete-li povolit replikaci pro virtuální počítač a v podstatě kopírovat data pomocí Azure Site Recovery, musíte mít:

    - Oprávnění k vytvoření virtuálního počítače v prostředcích Azure. Integrovaná role přispěvatele virtuálního počítače má tato oprávnění, mezi něž patří:
    - Oprávnění k vytvoření virtuálního počítače ve vybrané skupině prostředků
    - Oprávnění k vytvoření virtuálního počítače ve vybrané virtuální síti
    - Oprávnění zapisovat na vybraný účet úložiště
    
    - Oprávnění ke správě operací obnovení webu Azure. Role Přispěvatel obnovení webu má všechna oprávnění potřebná ke správě operací obnovení webu v trezoru služby Recovery Services.

- Ujistěte se, že všechny nejnovější kořenové certifikáty jsou na virtuálních počítačích Azure, které chcete přesunout. Pokud nejnovější kořenové certifikáty nejsou na virtuálním počítači, omezení zabezpečení zabrání kopírování dat do cílové oblasti.

- U virtuálních počítačů s Windows zajistíte přítomnost všech důvěryhodných kořenových certifikátů tím, že na ně nainstalujete všechny nejnovější aktualizace Windows. V odpojeném prostředí dodržujte při aktualizaci Windows a certifikátů standardní postupy uplatňované ve vaší organizaci.
    
- Pro virtuální počítače s Linuxem postupujte podle pokynů vašeho distributora Linuxu a získejte nejnovější důvěryhodné kořenové certifikáty a seznam odvolaných certifikátů na virtuálním počítači.
- Ujistěte se, že nepoužíváte ověřovací proxy server pro řízení připojení k síti pro virtuální servery, které chcete přesunout.

- Pokud virtuální virtuální počítače, který se pokoušíte přesunout nemá přístup k Internetu, nebo je to pomocí proxy firewall řídit odchozí přístup, [zkontrolujte požadavky](azure-to-azure-tutorial-enable-replication.md#set-up-outbound-network-connectivity-for-vms).

- Identifikujte rozložení zdrojové sítě a všechny prostředky, které právě používáte. To zahrnuje mimo jiné vykladače zatížení, skupiny zabezpečení sítě (NSG) a veřejné IP adresy.

- Ověřte, že vaše předplatné Azure umožňuje vytvářet virtuální počítače v cílové oblasti, která se používá pro zotavení po havárii. O povolení požadované kvóty požádejte podporu.

- Ujistěte se, že vaše předplatné má dostatek prostředků pro podporu virtuálních počítačů s velikostmi, které odpovídají vašim zdrojovým virtuálním počítačům. Pokud ke kopírování dat do cíle používáte Site Recovery, site recovery zvolí stejnou velikost nebo nejbližší možnou velikost pro cílový virtuální virtuální počítače.

- Ujistěte se, že vytvoříte cílový prostředek pro každou součást, která je identifikována v rozložení zdrojové sítě. Tento krok je důležité zajistit, aby vaše virtuální počítače mají všechny funkce a funkce v cílové oblasti, které jste měli ve zdrojové oblasti.

     > [!NOTE] 
     > Azure Site Recovery automaticky zjišťuje a vytvoří virtuální síť, když povolíte replikaci pro zdrojový virtuální počítač. Můžete také předem vytvořit síť a přiřadit ji k virtuálnímu virtuálnímu virtuálnímu serveru v toku uživatele pro povolení replikace. Jak již bylo zmíněno později, je třeba ručně vytvořit všechny další prostředky v cílové oblasti.

    Pokud chcete vytvořit nejčastěji používané síťové prostředky, které jsou pro vás relevantní na základě konfigurace zdrojového virtuálního počítače, přečtěte si následující dokumentaci:
    - [Skupiny zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)
    - [Služby vyrovnávání zatížení](https://docs.microsoft.com/azure/load-balancer)
    -  [Veřejná IP adresa](../virtual-network/virtual-network-public-ip-address.md)
    - Další síťové součásti naleznete v [dokumentaci](https://docs.microsoft.com/azure/?pivot=products&panel=network)k síti .



## <a name="prepare"></a>Příprava
Následující kroky ukazují, jak připravit virtuální počítač pro přesun pomocí Azure Site Recovery jako řešení. 

### <a name="create-the-vault-in-any-region-except-the-source-region"></a>Vytvoření úschovny v libovolné oblasti kromě zdrojové oblasti

1. Přihlaste se ke**službě Obnovení** [portálu](https://portal.azure.com) > Azure .
1. Vyberte **možnost Vytvořit** > **nástroje pro** > správu prostředků**Zálohování a obnovení lokality**.
1. Do pole **Název** zadejte popisný název **ContosoVMVault**. Pokud máte více předplatných, vyberte příslušné předplatné.
1. Vytvořte skupinu prostředků **ContosoRG**.
1. Zadejte oblast Azure. Pokud chcete zkontrolovat podporované oblasti, přečtěte si informace o geografické dostupnosti v [podrobnostech o cenách Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
1. V **trezorech služby Recovery Services**vyberte možnost **Přehled** > **contosoVMvault** > **+replikovat**.
1. V rozevíracím seznamu **Zdroj** vyberte **Azure**.
1. V rozevíracím seznamu **Umístění zdroje** vyberte zdrojovou oblast Azure, kde máte virtuální počítače aktuálně spuštěné.
1. Vyberte model nasazení Resource Manager. Pak vyberte **zdrojové předplatné** a **skupinu zdrojových prostředků**.
1. Chcete-li nastavení uložit, vyberte **ok.**

### <a name="enable-replication-for-azure-vms-and-start-copying-the-data"></a>Povolení replikace pro virtuální počítače Azure a zahájení kopírování dat

Site Recovery načte seznam virtuálních zařízení, které jsou přidruženy k předplatnému a skupině prostředků.

1. V dalším kroku vyberte virtuální ms, který chcete přesunout, a pak vyberte **OK**.
1. V **nastavení**vyberte **možnost Zotavení po havárii**.
1. V části Konfigurace > **cílové oblasti**pro zotavení **po havárii**vyberte cílovou oblast, do které budete replikovat.
1. U ostatních nastavení přijměte pro účely tohoto kurzu výchozí hodnoty.
1. Vyberte **povolit replikaci**. Tento krok spustí úlohu povolit replikaci pro virtuální ho.

    ![Povolení replikace](media/tutorial-migrate-azure-to-azure/settings.png)

## <a name="move"></a>Přesunout

Následující kroky ukazují, jak provést přesunutí do cílové oblasti.

1. Jdi do trezoru. V **nastavení** > **Replikované položky**vyberte virtuální ms a pak vyberte **převzetí služeb při selhání**.
2. V okně **Převzetí služeb při selhání** vyberte **Nejnovější**.
3. Vyberte **Před spuštěním převzetí služeb při selhání vypnout počítač**. Služba Site Recovery se před aktivací převzetí služeb při selhání pokusí zdrojový virtuální počítač vypnout. Převzetí služeb při selhání bude pokračovat i v případě, že se vypnutí nepovede. Průběh převzetí služeb při selhání můžete sledovat na stránce **Úlohy**.
4. Po dokončení úlohy zkontrolujte, zda se virtuální počítač zobrazí v cílové oblasti Azure podle očekávání.


## <a name="discard"></a>Zahodit 

V případě, že jste zkontrolovali přesunutý virtuální virtuální ms a potřebujete provést změnu na bod převzetí služeb při selhání nebo chcete se vrátit k předchozímu bodu, v **položkách Replikované**vyberte bod obnovení > **změnit virtuální ho.** Tento krok poskytuje možnost zadat jiný bod obnovení a převzetí služeb při selhání, které jeden. 


## <a name="commit"></a>Potvrzení 

Po kontrole přesunutého virtuálního virtuálního soudu a jsou připraveni k potvrzení změny, v **replikované položky**, vpravo vyberte virtuální ho > **potvrzení**. Tento krok dokončí proces přesunutí do cílové oblasti. Počkejte, až bude úloha potvrzení dokončena.

## <a name="clean-up"></a>Vyčištění

Následující kroky vás provedou tím, jak vyčistit zdrojovou oblast a související prostředky, které byly použity pro přesun.

Pro všechny zdroje, které byly použity pro přesun:

- Přejděte na virtuální m. Vyberte **zakázat replikaci**. Tento krok zastaví proces z kopírování dat pro virtuální hod.

   > [!IMPORTANT]
   > Je důležité provést tento krok, aby se zabránilo účtování poplatku za replikaci azure site recovery.

Pokud nemáte žádné plány na opakované použití některého ze zdrojových prostředků, proveďte tyto další kroky:

1. Odstraňte všechny příslušné síťové prostředky ve zdrojové oblasti, které jste identifikovali v [požadavcích](#prerequisites).
1. Odstraňte odpovídající účet úložiště ve zdrojové oblasti.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste přesunuli virtuální počítač Azure do jiné oblasti Azure. Teď můžete nakonfigurovat zotavení po havárii pro virtuální počítače, který jste přesunuli.

> [!div class="nextstepaction"]
> [Nastavení zotavení po havárii po migraci](azure-to-azure-quickstart.md)

