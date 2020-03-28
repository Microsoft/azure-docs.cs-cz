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
ms.openlocfilehash: dc37cb6fa05a2be56de7bf5536d7274190257d85
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "78303915"
---
# <a name="move-azure-vms-to-another-azure-region"></a>Přesunutí virtuálních počítačů Azure do jiné oblasti Azure

Můžete chtít přesunout infrastrukturu Azure jako služby (IaaS) virtuální počítače z jedné oblasti do druhé, abyste zlepšili spolehlivost, dostupnost, správu nebo zásady správného řízení. Tento kurz ukazuje, jak přesunout virtuální počítače do jiné oblasti pomocí Azure Site Recovery. Dozvíte se, jak provést tyto akce:

> [!div class="checklist"]
> * Ověření požadavků
> * Příprava zdrojových virtuálních počítačů
> * Příprava cílové oblasti
> * Kopírování dat do cílové oblasti
> * Otestujte konfiguraci
> * Provedení přesunu
> * Zahodit prostředky ze zdrojové oblasti


> [!IMPORTANT]
> Tento článek popisuje, jak přesunout virtuální počítače Azure z jedné oblasti do jiné, *jak je*. Pokud je vaším cílem zlepšit dostupnost infrastruktury přesunutím virtuálních počítačů do zón dostupnosti, přečtěte si část [Přesun virtuálních počítačů Azure do zón dostupnosti](move-azure-vms-avset-azone.md).

## <a name="prerequisites"></a>Požadavky

- Ujistěte se, že máte virtuální počítače Azure ve zdrojové oblasti Azure, *ze*které chcete přejít.
- Ověřte, zda je podporována vaše volba [kombinace zdrojové oblasti a cílové oblasti](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support), a pečlivě zvolte cílovou oblast.
- Ujistěte se, že rozumíte [komponentám a architektuře řešení](azure-to-azure-architecture.md).
- Zkontrolujte [omezení podpory a požadavky](azure-to-azure-support-matrix.md).
- Ověřte oprávnění účtu. Pokud jste si právě vytvořili bezplatný účet Azure, *jste* správcem předplatného. Pokud nejste správce, spolupracujte se správcem a získejte potřebná oprávnění:
  -  Chcete-li povolit replikaci pro virtuální počítač a kopírovat data do cíle pomocí site recovery, musíte mít oprávnění k vytvoření virtuálního počítače ve vašich prostředcích Azure. Tato oprávnění má předdefinovaná role Přispěvatel virtuálních počítačů. S oprávněními můžete:
        - Vytvoření virtuálního počítače ve vybrané skupině prostředků
        - Vytvoření virtuálního počítače ve vybrané virtuální síti
        - Zápis do vybraného účtu úložiště

  - Ke správě operací obnovení webu potřebujete také oprávnění. Role Přispěvatel obnovení webu má všechna oprávnění, která jsou vyžadována ke správě operací obnovení webu v trezoru služby Azure Recovery Services.

## <a name="prepare-the-source-vms"></a>Příprava zdrojových virtuálních počítačů

1. Zkontrolujte, zda virtuální počítače Azure, které chcete přesunout, mají nejnovější kořenové certifikáty. Pokud tomu tak není, nelze povolit kopírování dat do cílové oblasti z důvodu omezení zabezpečení.

    - Pro virtuální počítače se systémem Windows nainstalujte nejnovější aktualizace systému Windows, aby byly v počítači všechny důvěryhodné kořenové certifikáty. V odpojeném prostředí postupujte podle standardních procesů windows update a aktualizace certifikátů pro vaši organizaci.
    - U virtuálních počítačů s Linuxem postupujte podle pokynů od distributora Linuxu a získejte nejnovější důvěryhodné kořenové certifikáty a seznam odvolaných certifikátů.
2. Ujistěte se, že nepoužíváte ověřovací proxy server k řízení připojení k síti pro virtuální servery, které chcete přesunout.
3. Pokud virtuální virtuální počítače, který chcete přesunout, nemá přístup k Internetu a používá proxy firewall k řízení odchozího přístupu, zkontrolujte [požadavky](azure-to-azure-tutorial-enable-replication.md#set-up-outbound-network-connectivity-for-vms).
4. Zdokumentujte rozložení zdrojové sítě a všechny prostředky, které právě používáte, včetně (ale nejen) nástrojů pro vyrovnávání zatížení, skupin zabezpečení sítě a veřejných IP adres pro ověření.

## <a name="prepare-the-target-region"></a>Příprava cílové oblasti

1. Ve svém předplatném Azure ověřte, že můžete vytvářet virtuální počítače v cílové oblasti, která se používá pro zotavení po havárii. V případě potřeby kontaktujte podporu a povolte požadovanou kvótu.

2. Ujistěte se, že vaše předplatné má dostatek prostředků pro podporu vašich zdrojových virtuálních počítačů. Pokud ke kopírování dat do cíle používáte Site Recovery, vybere stejnou velikost nebo nejbližší dostupnou velikost pro cílové virtuální počítače.

3. Ujistěte se, že vytvoříte cílový prostředek pro každou součást, kterou jste identifikovali v rozložení zdrojové sítě. Tím zajistíte, že vaše virtuální počítače budou mít všechny funkce a funkce v cílové oblasti, které měli ve zdrojové oblasti.

   Azure Site Recovery automaticky zjišťuje a vytváří účet virtuální sítě a úložiště, když povolíte replikaci pro zdrojový virtuální počítač. Můžete také předem vytvořit tyto prostředky a přiřadit je k virtuálnímu virtuálnímu virtuálnímu soudu jako součást kroku povolit replikaci. Je však nutné ručně vytvořit všechny ostatní prostředky v cílové oblasti. V následujících dokumentech můžete vytvořit nejčastěji používané síťové prostředky na základě zdrojové konfigurace virtuálního počítače:

   - [Skupiny zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)
   - [Služby vyrovnávání zatížení](https://docs.microsoft.com/azure/load-balancer)
   - [Veřejná IP adresa](../virtual-network/virtual-network-public-ip-address.md)
    
   Další síťové součásti najdete v [dokumentaci k síti Azure](https://docs.microsoft.com/azure/?pivot=products&panel=network). 

4. Chcete-li otestovat konfiguraci před přesunutím, ručně [vytvořte neprodukční síť](https://docs.microsoft.com/azure/virtual-network/quick-create-portal) v cílové oblasti. Testování nastavení vytváří minimální rušení s produkčním prostředím a doporučujeme ho.
    
## <a name="copy-data-to-the-target-region"></a>Kopírování dat do cílové oblasti
Následující kroky slouží ke kopírování dat do cílové oblasti pomocí Azure Site Recovery.

### <a name="create-the-vault-in-any-region-except-the-source"></a>Vytvoření úschovny v libovolné oblasti kromě zdroje

1. Přihlaste se ke**službě Obnovení** [portálu](https://portal.azure.com) > Azure .
2. Vyberte **možnost Vytvořit** > **nástroje pro** > správu prostředků**Zálohování a obnovení lokality**.
3. V **poli Název**zadejte popisný název **ContosoVMVault**. Pokud máte více předplatných, vyberte příslušné předplatné.
4. Vytvořte skupinu prostředků **ContosoRG**.
5. Zadejte oblast Azure. Informace o podporovaných oblastech najdete v tématu [Podrobnosti o cenách obnovení webu Azure](https://azure.microsoft.com/pricing/details/site-recovery/).
6. V trezorech služby Recovery Services vyberte **možnost Přehled** > **consotoVMVault** > **+Replikovat**.
7. V **popřípadě zdroj**vyberte **Azure**.
8. Pro **zdrojové umístění**vyberte zdrojovou oblast Azure, kde jsou vaše virtuální počítače aktuálně spuštěné.
9. Vyberte model nasazení Azure Resource Manageru. Potom vyberte **zdrojové předplatné** a **skupinu zdrojových prostředků**.
10. Chcete-li nastavení uložit, vyberte **ok.**

### <a name="enable-replication-for-azure-vms-and-start-copying-the-data"></a>Povolení replikace pro virtuální počítače Azure a zahájení kopírování dat

Site Recovery načte seznam virtuálních zařízení, které jsou přidruženy k předplatnému a skupině prostředků.

1. Vyberte virtuální hod, který chcete přesunout, a pak vyberte **OK**.
2. V **části Nastavení**vyberte možnost Zotavení po **havárii**.
3. V části Konfigurovat**cílovou oblast** **zotavení po** > havárii vyberte cílovou oblast, do které se replikujete.
4. Zvolte, zda chcete použít výchozí cílové prostředky nebo prostředky, které jste předem vytvořili.
5. Chcete-li úlohu spustit, vyberte **povolit replikaci.**

   ![Povolení replikace](media/tutorial-migrate-azure-to-azure/settings.png)

 

## <a name="test-the-configuration"></a>Otestujte konfiguraci


1. Jdi do trezoru. V **nastavení** > **Replikované položky**vyberte virtuální počítač, který chcete přesunout do cílové oblasti. Potom vyberte **testovat převzetí služeb při selhání**.
2. V **části Test Failover**vyberte bod obnovení, který chcete použít pro převzetí služeb při selhání:

   - **Nejnovější zpracovaný:** Vrátí virtuální počítač k nejnovějšímu bodu obnovení zpracovanému službou Site Recovery. Zobrazí se časové razítko. Zpracování dat netráví žádný čas, takže tato možnost poskytuje cíl nízké doby obnovení (RTO).
   - **Nejnovější konzistentní aplikace**: Poselhání všech virtuálních připojení na nejnovější bod obnovení konzistentní s aplikací. Zobrazí se časové razítko.
   - **Vlastní**: Vyberete si libovolný bod obnovení.

3. Vyberte cílovou virtuální síť Azure, do které chcete přesunout virtuální počítače Azure a otestovat konfiguraci.

   > [!IMPORTANT]
   > Doporučujeme použít samostatnou síť virtuálních počítačích Azure pro převzetí služeb při selhání testu, nikoli produkční síť v cílové oblasti.

4. Chcete-li zahájit testování přesunu, vyberte **možnost OK**. Chcete-li sledovat průběh, vyberte virtuální počítače otevřít jeho **vlastnosti.** Nebo vyberte testovací úlohu **převzetí služeb při selhání** v trezoru. Potom vyberte **Nastavení** > **úloh** > **obnovení webu .**
5. Po dokončení převzetí služeb při selhání se na portálu Azure Portal v části **Virtuální počítače** objeví replika virtuálního počítače Azure. Ujistěte se, že je virtuální počítač spuštěný, má odpovídající velikost a je připojený k odpovídající síti.
6. Pokud chcete odstranit virtuální počítač, který jste vytvořili pro testování, vyberte převzetí **služeb při selhání testu vyčištění** u replikované položky. Z **poznámek**zaznamenáte a uložte všechna pozorování související s testem.

## <a name="perform-the-move-and-confirm"></a>Provedení přesunu a potvrzení

1. Přejděte do trezoru v **nastavení** > **Replikovaných položek**, vyberte virtuální počítač a pak vyberte Převzetí služeb při **selhání**.
1. V **posílce 12 000**vyberte **nejnovější**. 
2. Vyberte **Před spuštěním převzetí služeb při selhání vypnout počítač**. Obnovení lokality se pokusí vypnout zdrojový virtuální hod před aktivací převzetí služeb při selhání. Převzetí služeb při selhání však pokračuje i v případě, že se vypnutí nezdaří. Průběh převzetí služeb při selhání můžete sledovat na stránce **Úlohy**.
3. Po dokončení úlohy zkontrolujte, zda se virtuální počítač zobrazí v cílové oblasti Azure podle očekávání.
4. V **části Replikované položky**klikněte pravým tlačítkem myši na virtuální ms a vyberte **Potvrdit**. Tím se tah dokončí. Počkejte, až bude úloha potvrzení dokončena.

## <a name="discard-the-resources-from-the-source-region"></a>Zahodit prostředky ze zdrojové oblasti

- Přejděte na virtuální ms a vyberte **Zakázat replikaci**. Tím se zastaví proces kopírování dat pro virtuální hod.

  > [!IMPORTANT]
  > Tento krok proveďte, abyste se vyhnuli účtování za replikaci obnovení sítě po přesunutí.

Pokud neplánujete znovu použít žádný ze zdrojových prostředků, postupujte takto:

1. Odstraňte všechny příslušné síťové prostředky ve zdrojové oblasti, kterou jste uvedli v kroku 4 [připravit zdrojové virtuální počítačky](#prepare-the-source-vms).
2. Odstraňte odpovídající účet úložiště ve zdrojové oblasti.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili, jak přesunout virtuální počítače Azure do jiné oblasti Azure. Teď můžete nakonfigurovat zotavení po havárii pro tyto virtuální počítače.

> [!div class="nextstepaction"]
> [Nastavení zotavení po havárii po migraci](azure-to-azure-quickstart.md)

