---
title: Přesun virtuálních počítačů Azure IaaS do jiné oblasti Azure pomocí služby Azure Site Recovery | Dokumentace Microsoftu
description: Pomocí Azure Site Recovery pro přesun virtuálních počítačů Azure IaaS z jedné oblasti Azure do jiného.
services: site-recovery
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: 636993879422edaf08051be61b9d4770effdcae0
ms.sourcegitcommit: 9e8dfa1169a55c3c8af93a6c5f4e0dace4de48b2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/13/2019
ms.locfileid: "65556076"
---
# <a name="move-azure-vms-to-another-region"></a>Přesun virtuálních počítačů Azure do jiné oblasti

Existují různé scénáře, ve kterých chcete přesunout stávající virtuální počítače (VM) Azure IaaS z jedné oblasti do jiného. Například chcete zvýšit spolehlivost a dostupnost vašich stávajících virtuálních počítačů, ke zlepšení možností správy, nebo přesunout z důvodu zásad správného řízení. Další informace najdete v tématu [přesunout virtuální počítač Azure přehled](azure-to-azure-move-overview.md). 

Můžete použít [Azure Site Recovery](site-recovery-overview.md) služby ke správě a orchestraci zotavení po havárii místních počítačů a virtuálních počítačů Azure pro obchodní kontinuity podnikových procesů a zotavení po havárii (BCDR). Site Recovery můžete také použít ke správě přesun virtuálních počítačů Azure do sekundární oblasti.

V tomto kurzu provedete následující:

> [!div class="checklist"]
> 
> * Ověřte požadavky pro přesunutí
> * Příprava zdrojových virtuálních počítačů a cílová oblast
> * Zkopírujte data a povolení replikace
> * Otestujte konfiguraci a provedení přesunu
> * Odstranit prostředky ve zdrojové oblasti
> 
> [!NOTE]
> V tomto kurzu se dozvíte, jak přesunout virtuální počítače Azure z jedné oblasti do jiného je. Pokud potřebujete kvůli zlepšení dostupnosti díky přesunu virtuálních počítačů ve skupině dostupnosti zóny připojené virtuální počítače v jiné oblasti, najdete v článku [přesunout virtuální počítače Azure do zóny dostupnosti kurzu](move-azure-vms-avset-azone.md).

## <a name="prerequisites"></a>Požadavky

- Ujistěte se, že jsou virtuální počítače Azure v oblasti Azure, ze kterého chcete přesunout.
- Ověřte, že podle vaší volby [podporované zdrojové oblasti – kombinace cílové oblasti](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support)a provést informované rozhodnutí o cílové oblasti.
- Ujistěte se, že rozumíte [komponentám a architektuře řešení](azure-to-azure-architecture.md).
- Zkontrolujte [omezení podpory a požadavky](azure-to-azure-support-matrix.md).
- Ověření oprávnění k účtu. Pokud jste vytvořili bezplatný účet Azure, jste správcem předplatného. Pokud si nejste správce předplatného, požádejte správce o přiřazení oprávnění, které potřebujete. Pokud chcete povolit replikaci pro virtuální počítač a v podstatě kopírování dat pomocí Azure Site Recovery, musíte mít:

    * Oprávnění k vytvoření virtuálního počítače v prostředcích Azure. Předdefinovaná role Přispěvatel virtuálních počítačů má tato oprávnění, mezi které patří:
        - Oprávnění k vytvoření virtuálního počítače ve vybrané skupině prostředků
        - Oprávnění k vytvoření virtuálního počítače ve vybrané virtuální síti
        - Oprávnění zapisovat na vybraný účet úložiště

    * Oprávnění ke správě operací Azure Site Recovery. Role Přispěvatel Site Recovery má všechna oprávnění, která jsou nutná ke správě operací Site Recovery v trezoru služby Recovery Services.

## <a name="prepare-the-source-vms"></a>Příprava zdrojové virtuální počítače

1. Ujistěte se, že jsou na virtuálních počítačích Azure, který chcete přesunout všechny nejnovější kořenové certifikáty. Pokud nejnovější kořenové certifikáty nejsou ve virtuálním počítači, omezení zabezpečení zabrání kopírování dat do cílové oblasti.

    - U virtuálních počítačů s Windows zajistíte přítomnost všech důvěryhodných kořenových certifikátů tím, že na ně nainstalujete všechny nejnovější aktualizace Windows. V odpojeném prostředí dodržujte při aktualizaci Windows a certifikátů standardní postupy uplatňované ve vaší organizaci.
    - Virtuální počítače s Linuxem postupujte podle pokynů, které jste dostali od distributora Linuxu pro získání nejnovějších důvěryhodných kořenových certifikátů a seznamu odvolaných certifikátů na virtuálním počítači.
1. Ujistěte se, že nepoužíváte k řízení síťového připojení ověřovací proxy server pro virtuální počítače, které chcete přesunout.
1. Pokud virtuální počítač, který se pokoušíte přesunout nemá přístup k Internetu, nebo proxy server brány firewall používá k řízení odchozího přístupu [Zkontrolujte požadavky na](azure-to-azure-tutorial-enable-replication.md#configure-outbound-network-connectivity).
1. Identifikujte zdroj sítě rozložení a všechny prostředky, které aktuálně používáte. To zahrnuje, avšak není omezeno na nástroje pro vyrovnávání zatížení, skupiny zabezpečení sítě (Nsg) a veřejné IP adresy.

## <a name="prepare-the-target-region"></a>Připravte cílové oblasti

1. Ověřte, že vaše předplatné Azure umožňuje vytvářet virtuální počítače v cílové oblasti, která se používá pro zotavení po havárii. O povolení požadované kvóty požádejte podporu.

1. Ujistěte se, že vaše předplatné má dostatek prostředků pro podporu virtuálních počítačů s velikostí, které odpovídají velikostem zdrojových virtuálních počítačů. Pokud používáte Site Recovery pro kopírování dat do cíle, Site Recovery vybere stejnou nebo nejbližší možnou velikost pro cílový virtuální počítač.

1. Ujistěte se, že vytvoříte cílový prostředek pro všechny komponenty služby, který je identifikován v rozložení síťové zdroje. Tento krok je potřeba zajistit, aby vaše virtuální počítače měly všechny funkce a funkce v cílové oblasti, které jste měli ve zdrojové oblasti.

    > [!NOTE]
    > Azure Site Recovery automaticky zjistí a vytvoří virtuální síť, když povolíte replikaci pro zdrojový virtuální počítač. Můžete také předem vytvořit síť a přiřaďte ho k virtuálnímu počítači v toku uživatele pro povolení replikace. Jak už bylo zmíněno později, budete muset ručně vytvořit všechny prostředky v cílové oblasti.

     Vytvořit většinu běžně používá síťové prostředky, které jsou relevantní pro vás v závislosti na konfiguraci zdrojového virtuálního počítače, naleznete v následující dokumentaci:

   - [Skupiny zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)
   - [Nástroje pro vyrovnávání zatížení](https://docs.microsoft.com/azure/load-balancer/#step-by-step-tutorials)
   - [Veřejná IP adresa](../virtual-network/virtual-network-public-ip-address.md)
    
     Ostatními síťovými součástmi, najdete v článku [sítě dokumentaci](https://docs.microsoft.com/azure/#pivot=products&panel=network).

1. Ručně [vytvořit síť neprodukčním](https://docs.microsoft.com/azure/virtual-network/quick-create-portal) v cílové oblasti, pokud chcete otestovat konfiguraci, před provedením finální přechod na cílové oblasti. Tento krok doporučujeme, protože zajišťuje minimální narušení produkční sítě.

## <a name="copy-data-to-the-target-region"></a>Kopírování dat do cílové oblasti
Následující kroky ukazují, jak pomocí Azure Site Recovery ke zkopírování dat do cílové oblasti.

### <a name="create-the-vault-in-any-region-except-the-source-region"></a>Vytvořte trezor nejprve v libovolné oblasti, s výjimkou zdrojové oblasti

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) > **Recovery Services**.
1. Vyberte **vytvořit prostředek** > **nástroje pro správu** > **Backup a Site Recovery**.
1. Do pole **Název** zadejte popisný název **ContosoVMVault**. Pokud máte více předplatných, vyberte příslušné předplatné.
1. Vytvořte skupinu prostředků **ContosoRG**.
1. Zadejte oblast Azure. Oblasti jsou podporované, najdete v části s geografickou dostupností v [podrobnosti o cenách Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
1. V **trezory služby Recovery Services**vyberte **přehled** > **ContosoVMVault** > **+ replikovat**.
1. V rozevíracím seznamu **Zdroj** vyberte **Azure**.
1. V rozevíracím seznamu **Umístění zdroje** vyberte zdrojovou oblast Azure, kde máte virtuální počítače aktuálně spuštěné.
1. Vyberte model nasazení Resource Manager. Vyberte **zdrojové předplatné** a **zdrojovou skupinu prostředků**.
1. Vyberte **OK** uložte nastavení.

### <a name="enable-replication-for-azure-vms-and-start-copying-the-data"></a>Povolíte replikaci pro virtuální počítače Azure a zahájit kopírování dat

Site Recovery načte seznam virtuálních počítačů, které jsou přidružené předplatné a skupinu prostředků.

1. V dalším kroku vyberte virtuální počítač, který chcete přesunout a pak vyberte **OK**.
1. V **nastavení**vyberte **zotavení po havárii**.
1. V **konfigurace zotavení po havárii** > **cílové oblasti**, vyberte cílovou oblast, do kterého budete replikovat.
1. U ostatních nastavení přijměte pro účely tohoto kurzu výchozí hodnoty.
1. Vyberte **povolit replikaci**. Tento krok spustí úlohu povolit replikaci pro virtuální počítač.

    ![Povolení replikace](media/tutorial-migrate-azure-to-azure/settings.png)

## <a name="test-the-configuration"></a>Otestujte konfiguraci

1. Přejděte do trezoru. V **nastavení** > **replikované položky**, vyberte virtuální počítač, který chcete přesunout do cílové oblasti a pak vyberte **+ testovací převzetí služeb při selhání**.
1. V **testovací převzetí služeb při selhání**, vyberte bod obnovení pro převzetí služeb:

   - **Nejnovější zpracovaný**: Převezme virtuálního počítače k nejnovějšímu bodu obnovení zpracovanému službou Site Recovery. Časové razítko je vidět. S touto možností se neztrácí žádný čas zpracováním dat, takže poskytuje nízkou cíl času obnovení (RTO).
   - **Nejnovější konzistentní vzhledem k**: Tato možnost převezme služby při selhání všech virtuálních počítačů k nejnovějšímu bodu obnovení s konzistentní aplikací. Časové razítko je vidět.
   - **Vlastní**: Zvolte jakýkoli bod obnovení.

1. Vyberte cíl Azure virtuální sítě, ke které chcete přesunout virtuální počítače Azure k testování této konfigurace.
    > [!IMPORTANT]
    > Doporučujeme použít samostatnou síť virtuálních počítačů Azure pro testovací převzetí služeb. Nepoužívejte produkční sítě, kterou jste nastavili při povolování replikace a chcete přesunout virtuální počítače do nakonec.
1. Pro začátek testování přesunutí, klikněte na tlačítko **OK**. Pokud chcete sledovat průběh, kliknutím na virtuální počítač otevřete jeho vlastnosti. Případně můžete kliknout na úlohu **Testovací převzetí služeb při selhání** v části název_trezoru > **Nastavení** > **Úlohy** > **Úlohy Site Recovery**.
1. Po dokončení převzetí služeb při selhání se na portálu Azure Portal v části **Virtuální počítače** objeví replika virtuálního počítače Azure. Ujistěte se, že virtuální počítač běží, má odpovídající velikost a je připojený k odpovídající síti.
1. Pokud chcete odstranit tha virtuální počítač byl vytvořen jako součást přesunu testování, klikněte na **vyčištění testovacího převzetí služeb při selhání** na replikovanou položku. V **poznámky**si zaznamenejte a uložte jakékoli připomínky, které jsou spojené s testem.

## <a name="perform-the-move-to-the-target-region-and-confirm"></a>Provedení přesunu do cílové oblasti a potvrzení

1. Přejděte do trezoru. V **nastavení** > **replikované položky**, vyberte virtuální počítač a potom vyberte **převzetí služeb při selhání**.
1. V okně **Převzetí služeb při selhání** vyberte **Nejnovější**.
1. Vyberte **Před spuštěním převzetí služeb při selhání vypnout počítač**. Služba Site Recovery se před aktivací převzetí služeb při selhání pokusí zdrojový virtuální počítač vypnout. Převzetí služeb při selhání bude pokračovat i v případě, že se vypnutí nepovede. Průběh převzetí služeb při selhání můžete sledovat na stránce **Úlohy**.
1. Po dokončení úlohy zkontrolujte, že virtuální počítač zobrazuje v cílové oblasti Azure podle očekávání.
1. V **replikované položky**, stisknutém pravém tlačítku vyberte virtuální počítač > **potvrzení**. Tento krok dokončí proces přesunu na cílové oblasti. Počkejte na dokončení úlohy potvrzení.

## <a name="delete-the-resource-in-the-source-region"></a>Odstranit prostředek ve zdrojové oblasti

Přejděte k virtuálnímu počítači. Vyberte **zakázat replikaci**. Tento krok zastaví proces kopírování dat pro virtuální počítač.

> [!IMPORTANT]
> Je důležité provést tento krok a vyhněte se účtuje replikace Azure Site Recovery.

Pokud je nemáte v úmyslu použít některý z prostředků zdroje, postupujte podle těchto kroků:

1. Odstranit všechny příslušné síťové prostředky ve zdrojové oblasti, která vypsali jako součást kroku 4 v [Příprava zdrojové virtuální počítače](#prepare-the-source-vms).
1. K odstranění odpovídajícího účtu úložiště ve zdrojové oblasti.

## <a name="next-steps"></a>Další postup

V tomto kurzu se přesunout virtuální počítač Azure do jiné oblasti Azure. Teď můžete nakonfigurovat zotavení po havárii pro virtuální počítač, který jste přesunuli.

> [!div class="nextstepaction"]
> [Nastavení zotavení po havárii po migraci](azure-to-azure-quickstart.md)

