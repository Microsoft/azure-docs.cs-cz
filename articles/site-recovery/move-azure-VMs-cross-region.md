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
ms.openlocfilehash: 10966a7e658e02f04137b594fc12ec09cb676cf8
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2019
ms.locfileid: "65739606"
---
# <a name="move-azure-vms-to-another-region"></a>Přesun virtuálních počítačů Azure do jiné oblasti

Můžete chtít přesunout infrastruktury Azure jako služba (IaaS) virtuálních počítačů z jedné oblasti do jiného zlepšení spolehlivosti, dostupnosti, správy a zásad správného řízení. V tomto kurzu se dozvíte, jak přesunout virtuální počítače do jiné oblasti pomocí Azure Site Recovery. Dozvíte se, jak provést tyto akce:

> [!div class="checklist"]
> * Ověření požadavků
> * Příprava zdrojové virtuální počítače
> * Připravte cílové oblasti
> * Kopírování dat do cílové oblasti
> * Otestujte konfiguraci
> * Provedení přesunu
> * Zahodit prostředky ze zdrojové oblasti


> [!IMPORTANT]
> Tento článek popisuje, jak přesunout virtuální počítače Azure z jedné oblasti do jiného *je*. Pokud je vaším cílem je zvýšit dostupnost vaší infrastruktury přesun virtuálních počítačů do zóny dostupnosti, najdete v článku [přesunout virtuální počítače Azure do zóny dostupnosti](move-azure-vms-avset-azone.md).

## <a name="prerequisites"></a>Požadavky

- Ujistěte se, že máte virtuální počítače Azure v oblasti Azure, která chcete přesunout zdroj *z*.
- Ověřte, že podle vaší volby [podporované zdrojové oblasti cílové oblasti kombinaci](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support)a pečlivě určit cílové oblasti.
- Ujistěte se, že rozumíte [komponentám a architektuře řešení](azure-to-azure-architecture.md).
- Zkontrolujte [omezení podpory a požadavky](azure-to-azure-support-matrix.md).
- Ověření oprávnění k účtu. Pokud jste právě vytvořili bezplatný účet Azure, *vám* správcem předplatného. Pokud si nejste správce, požádejte správce o oprávnění, které budete potřebovat:
  -  Pokud chcete povolit replikaci pro virtuální počítač a kopírování dat do cílové pomocí Site Recovery, musí mít oprávnění k vytvoření virtuálního počítače s prostředky Azure. Tato oprávnění má předdefinovaná role Přispěvatel virtuálních počítačů. Oprávnění můžete:
        - Vytvoření virtuálního počítače ve vybrané skupině prostředků
        - Vytvoření virtuálního počítače ve vybrané virtuální síti
        - Zápis do vybraného účtu úložiště

  - Potřebujete také oprávnění ke správě operací Site Recovery. Role Přispěvatel Site Recovery má všechna oprávnění, která jsou nutná ke správě operací Site Recovery v trezoru služby Azure Recovery Services.

## <a name="prepare-the-source-vms"></a>Příprava zdrojové virtuální počítače

1. Zkontrolujte, zda virtuální počítače Azure, které máte v úmyslu přesunout nejnovější kořenové certifikáty. Pokud ne, z důvodu omezení zabezpečení nelze povolit kopírování dat do cílové oblasti.

    - Pro virtuální počítače s Windows nainstalujte nejnovější aktualizace Windows tak, aby důvěryhodných kořenových certifikátů na počítači. V odpojeném prostředí postupujte podle standardních procesů aktualizovat Windows a aktualizace certifikátu pro vaši organizaci.
    - Virtuální počítače s Linuxem postupujte podle pokynů od distributora Linuxu pro získání nejnovějších důvěryhodných kořenových certifikátů a seznamu odvolaných certifikátů.
2. Ujistěte se, že nepoužíváte k řízení síťového připojení ověřovací proxy server pro virtuální počítače, které máte v úmyslu přesunout.
3. Pokud chcete přesunout virtuální počítač nemá přístup k Internetu a používá proxy server brány firewall pro řízení odchozího přístupu, zkontrolujte [požadavky](azure-to-azure-tutorial-enable-replication.md#set-up-outbound-network-connectivity-for-vms).
4. Zdokumentujte zdrojové sítě rozložení a všechny prostředky, které aktuálně používáte, včetně (mimo jiné) načítání nástroje pro vyrovnávání, skupiny zabezpečení sítě a veřejné IP adresy pro ověřování.

## <a name="prepare-the-target-region"></a>Připravte cílové oblasti

1. Ve vašem předplatném Azure ověřte, že můžete vytvořit virtuální počítače v cílové oblasti, která se používá pro zotavení po havárii. Požádejte podporu o povolení požadované kvóty v případě potřeby.

2. Ujistěte se, že vaše předplatné má dostatek prostředků pro podporu velikostem zdrojových virtuálních počítačů. Pokud používáte Site Recovery pro kopírování dat do cíle, použije stejnou nebo nejbližší dostupné velikosti pro cílových virtuálních počítačů.

3. Ujistěte se, že vytvoříte cílový prostředek pro všechny komponenty služby, který jste identifikovali v rozložení síťové zdroje. Tím se zajistí, že vaše virtuální počítače budou mít všechny funkce a funkce v cílové oblasti, měli ve zdrojové oblasti.

   Azure Site Recovery automaticky zjistí a vytvoří virtuální síť a úložiště účtu při povolení replikace pro zdrojový virtuální počítač. Můžete také předem vytvořit tyto prostředky a přiřadit je k virtuálnímu počítači jako součást kroku povolení replikace. Ale musíte ručně vytvořit všechny prostředky v cílové oblasti. Přečtěte si následující dokumenty, které se nejčastěji používanou síťovým prostředkům na základě vytvořit vaší konfiguraci zdrojového počítače:

   - [Skupiny zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)
   - [Nástroje pro vyrovnávání zatížení](https://docs.microsoft.com/azure/load-balancer/#step-by-step-tutorials)
   - [Veřejná IP adresa](https://docs.microsoft.com/azure/load-balancer/#step-by-step-tutorials)
    
   Ostatními síťovými součástmi, najdete v článku [dokumentace k Azure sítě](https://docs.microsoft.com/azure/#pivot=products&panel=network). 

4. K testování této konfigurace předtím, než provedete přesunutí, ručně [vytvořit síť neprodukčním](https://docs.microsoft.com/azure/virtual-network/quick-create-portal) v cílové oblasti. Testování nastavení vytvoří minimálním narušením s produkčním prostředím, a My ho doporučujeme.
    
## <a name="copy-data-to-the-target-region"></a>Kopírování dat do cílové oblasti
Následující kroky použijte ke zkopírování dat do cílové oblasti Azure Site Recovery.

### <a name="create-the-vault-in-any-region-except-the-source"></a>Vytvořte trezor nejprve v libovolné oblasti s výjimkou zdroj

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) > **Recovery Services**.
2. Vyberte **vytvořit prostředek** > **nástroje pro správu** > **Backup a Site Recovery**.
3. Pro **název**, zadejte popisný název **ContosoVMVault**. Pokud máte více předplatných, vyberte příslušné předplatné.
4. Vytvořte skupinu prostředků **ContosoRG**.
5. Zadejte oblast Azure. Oblasti jsou podporované, najdete v části [Azure Site Recovery podrobnosti o cenách](https://azure.microsoft.com/pricing/details/site-recovery/).
6. Trezory služby Recovery Services, vyberte **přehled** > **ConsotoVMVault** > **+ replikovat**.
7. Pro **zdroj**vyberte **Azure**.
8. Pro **umístění zdroje**, vyberte zdroj oblast Azure, kde jsou vaše virtuální počítače aktuálně spuštěna.
9. Vyberte model nasazení Azure Resource Manageru. Vyberte **zdrojové předplatné** a **zdrojovou skupinu prostředků**.
10. Vyberte **OK** uložte nastavení.

### <a name="enable-replication-for-azure-vms-and-start-copying-the-data"></a>Povolíte replikaci pro virtuální počítače Azure a zahájit kopírování dat

Site Recovery načte seznam virtuálních počítačů, které jsou přidružené předplatné a skupinu prostředků.

1. Vyberte virtuální počítač, který chcete přesunout a pak vyberte **OK**.
2. Pro **nastavení**vyberte **zotavení po havárii**.
3. Pro **konfigurace zotavení po havárii** > **cílové oblasti**, vyberte, které replikujete do cílové oblasti.
4. Zvolte výchozí cílové prostředky nebo ty, které jste předem vytvořili.
5. Vyberte **povolit replikaci** spustíte úlohu.

   ![Povolení replikace](media/tutorial-migrate-azure-to-azure/settings.png)

 

## <a name="test-the-configuration"></a>Otestujte konfiguraci


1. Přejděte do trezoru. V **nastavení** > **replikované položky**, vyberte virtuální počítač, který chcete přesunout do cílové oblasti. Vyberte **testovací převzetí služeb při selhání**.
2. V **testovací převzetí služeb při selhání**, vyberte bod obnovení pro převzetí služeb:

   - **Nejnovější zpracovaný**: Převezme virtuálního počítače k nejnovějšímu bodu obnovení zpracovanému službou Site Recovery. Časové razítko je vidět. Se neztrácí žádný čas zpracováním dat, takže tato možnost poskytuje plánovaná doba nízké obnovení (RTO).
   - **Nejnovější konzistentní vzhledem k**: Převezme služby při selhání všech virtuálních počítačů k nejnovějšímu bodu obnovení s konzistentní aplikací. Časové razítko je vidět.
   - **Vlastní**: Zvolte jakýkoli bod obnovení.

3. Vyberte cíl Azure virtuální sítě, ke které chcete přesunout virtuální počítače Azure k testování této konfigurace.

   > [!IMPORTANT]
   > Doporučujeme použít samostatnou síť virtuálních počítačů Azure testovací převzetí služeb při selhání, ne produkční síti v cílové oblasti.

4. Chcete-li spustit testování přesunutí, vyberte **OK**. Pokud chcete sledovat průběh, vyberte virtuální počítač, otevřete jeho **vlastnosti.** Nebo vyberte **testovací převzetí služeb při selhání** úlohy v trezoru. Vyberte **nastavení** > **úlohy** > **úlohy Site Recovery**.
5. Po dokončení převzetí služeb při selhání se na portálu Azure Portal v části **Virtuální počítače** objeví replika virtuálního počítače Azure. Ujistěte se, že je virtuální počítač spuštěný, má odpovídající velikost a je připojený k odpovídající síti.
6. Pokud chcete odstranit virtuální počítač, který jste vytvořili pro účely testování, vyberte **vyčištění testovacího převzetí služeb při selhání** na replikovanou položku. Z **poznámky**si zaznamenejte a uložte jakékoli připomínky související s testu.

## <a name="perform-the-move-and-confirm"></a>Provedení přesunu a potvrzení

1. Přejděte do trezoru v **nastavení** > **replikované položky**, vyberte virtuální počítač a potom vyberte **převzetí služeb při selhání**.
1. Pro **převzetí služeb při selhání**vyberte **nejnovější**. 
2. Vyberte **Před spuštěním převzetí služeb při selhání vypnout počítač**. Site Recovery se pokusí o vypnout zdrojový virtuální počítač před aktivací převzetí služeb. Ale převzetí služeb při selhání pokračovat i v případě, že vypnutí nepovede. Průběh převzetí služeb při selhání můžete sledovat na stránce **Úlohy**.
3. Po dokončení úlohy zkontrolujte, že virtuální počítač zobrazuje v cílové oblasti Azure podle očekávání.
4. V **replikované položky**, klikněte pravým tlačítkem na virtuální počítač a vyberte **potvrzení**. Tím se dokončí přesunutí. Počkejte na dokončení úlohy potvrzení.

## <a name="discard-the-resources-from-the-source-region"></a>Zahodit prostředky ze zdrojové oblasti

- Přejděte na virtuální počítač a vyberte **zakázat replikaci**. Tím se zastaví proces kopírování dat pro virtuální počítač.

  > [!IMPORTANT]
  > Dokončení tohoto kroku, aby se vám účtovat replikace Site Recovery po přesunutí.

Pokud nemáte v úmyslu použít některý z prostředků zdroje, postupujte podle těchto kroků:

1. Odstranit všechny příslušné síťové prostředky ve zdrojové oblasti, který je uvedený v kroku 4 [Příprava zdrojové virtuální počítače](#prepare-the-source-vms).
2. K odstranění odpovídajícího účtu úložiště ve zdrojové oblasti.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste zjistili, jak přesunout virtuální počítače Azure do jiné oblasti Azure. Teď můžete nakonfigurovat zotavení po havárii pro tyto virtuální počítače.

> [!div class="nextstepaction"]
> [Nastavení zotavení po havárii po migraci](azure-to-azure-quickstart.md)

