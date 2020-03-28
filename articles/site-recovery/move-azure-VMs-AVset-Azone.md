---
title: Přesunutí virtuálních počítačů do oblasti Azure s zónami dostupnosti pomocí Azure Site Recovery
services: site-recovery
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: 5728ce7125695b191de4f91d5bd9003384f428a7
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "78298301"
---
# <a name="move-azure-vms-into-availability-zones"></a>Přesun virtuálních počítačů Azure do zón dostupnosti
Zóny dostupnosti v Azure pomáhají chránit vaše aplikace a data před selháními datových center. Každou zónu dostupnosti tvoří jedno nebo několik datových center vybavených nezávislým napájením, chlazením a sítí. Aby byla zajištěna odolnost proti chybám, jsou ve všech povolených oblastech minimálně tři samostatné zóny. Fyzické oddělení zón dostupnosti v rámci oblasti pomáhá chránit aplikace a data před selháním datového centra. Díky zónám dostupnosti nabízí Azure smlouvu o úrovni služeb (SLA) ve výši 99,99 % pro dostupnost virtuálních počítačů. Zóny dostupnosti jsou podporované ve vybraných oblastech, jak je uvedeno v co [jsou zóny dostupnosti v Azure?](https://docs.microsoft.com/azure/availability-zones/az-overview#services-support-by-region).

Ve scénáři, kde jsou vaše virtuální počítače nasazené jako *jedna instance* do určité oblasti a chcete zlepšit dostupnost přesunutím těchto virtuálních počítačů do zóny dostupnosti, můžete tak učinit pomocí Azure Site Recovery. Tuto akci lze dále rozdělit do:

- Přesunutí virtuálních uživatelů s jednou instancí do zón dostupnosti v cílové oblasti
- Přesunutí virtuálních uživatelů v sadě dostupnosti do zón dostupnosti v cílové oblasti

> [!IMPORTANT]
> V současné době Azure Site Recovery podporuje přesun virtuálních počítačů z jedné oblasti do druhé, ale nepodporuje přesun v rámci oblasti.

## <a name="check-prerequisites"></a>Kontrola požadavků

- Zkontrolujte, zda cílová oblast podporuje [zóny dostupnosti](https://docs.microsoft.com/azure/availability-zones/az-overview#services-support-by-region). Zkontrolujte, zda je podporována vaše volba [kombinace zdrojové oblasti nebo cílové oblasti](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support). Učinit informované rozhodnutí o cílové oblasti.
- Ujistěte se, že rozumíte [komponentám a architektuře řešení](azure-to-azure-architecture.md).
- Zkontrolujte [omezení podpory a požadavky](azure-to-azure-support-matrix.md).
- Zkontrolujte oprávnění účtu. Pokud jste si právě vytvořili bezplatný účet Azure, jste správcem předplatného. Pokud nejste správce předplatného, spolupracujte s správcem a přiřaďte oprávnění, která potřebujete. Chcete-li povolit replikaci pro virtuální počítač a nakonec zkopírovat data do cíle pomocí Azure Site Recovery, musíte mít:

    1. Oprávnění k vytvoření virtuálního počítače ve prostředcích Azure. Integrovaná role *přispěvatele virtuálního počítače* má tato oprávnění, mezi něž patří:
        - Oprávnění k vytvoření virtuálního počítače ve vybrané skupině prostředků
        - Oprávnění k vytvoření virtuálního počítače ve vybrané virtuální síti
        - Oprávnění zapisovat na vybraný účet úložiště

    2. Oprávnění ke správě úloh azure site recovery. Role *Přispěvatel obnovení webu* má všechna oprávnění potřebná ke správě akcí obnovení webu v trezoru služby Recovery Services.

## <a name="prepare-the-source-vms"></a>Příprava zdrojových virtuálních počítačů

1. Virtuální počítače by měly používat spravované disky, pokud je chcete přesunout do zóny dostupnosti pomocí site recovery. Můžete převést existující virtuální počítače se systémem Windows, které používají nespravované disky, a použít spravované disky. Postupujte podle pokynů v [části Převod virtuálního počítače se systémem Windows z nespravovaných disků na spravované disky](https://docs.microsoft.com/azure/virtual-machines/windows/convert-unmanaged-to-managed-disks). Ujistěte se, že je sada dostupnosti nakonfigurována jako *spravovaná*.
2. Zkontrolujte, jestli jsou všechny nejnovější kořenové certifikáty na virtuálních počítačích Azure, které chcete přesunout. Pokud nejnovější kořenové certifikáty nejsou k dispozici, nelze povolené kopírování dat do cílové oblasti z důvodu omezení zabezpečení.

3. U virtuálních počítačů s Windows zajistíte přítomnost všech důvěryhodných kořenových certifikátů tím, že na ně nainstalujete všechny nejnovější aktualizace Windows. V odpojeném prostředí postupujte podle standardních procesů aktualizace systému Windows a aktualizací certifikátů pro vaši organizaci.

4. Pro virtuální počítače s Linuxem postupujte podle pokynů vašeho distributora Linuxu a získejte nejnovější důvěryhodné kořenové certifikáty a seznam odvolaných certifikátů na virtuálním počítači.
5. Ujistěte se, že nepoužíváte ověřovací proxy server k řízení připojení k síti pro virtuální servery, které chcete přesunout.

6. Pokud virtuální virtuální počítače, který se pokoušíte přesunout, nemá přístup k Internetu a používá proxy firewall k řízení odchozího přístupu, zkontrolujte požadavky na [adrese Konfigurace odchozího připojení k síti](azure-to-azure-tutorial-enable-replication.md#set-up-outbound-network-connectivity-for-vms).

7. Identifikujte rozložení zdrojové sítě a prostředky, které aktuálně používáte k ověření, včetně nástrojů pro vyrovnávání zatížení, cílů sítě nsg a veřejné IP adresy.

## <a name="prepare-the-target-region"></a>Příprava cílové oblasti

1. Zkontrolujte, zda vaše předplatné Azure umožňuje vytvářet virtuální počítače v cílové oblasti používané pro zotavení po havárii. V případě potřeby kontaktujte podporu a povolte požadovanou kvótu.

2. Zkontrolujte, jestli máte v rámci předplatného k dispozici dostatek prostředků pro podporu virtuálních počítačů s takovými velikostmi, které odpovídají velikostem zdrojových virtuálních počítačů. Pokud ke kopírování dat do cíle použijete Site Recovery, vybere stejnou velikost nebo nejbližší možnou velikost pro cílový virtuální virtuální počítače.

3. Vytvořte cílový prostředek pro každou součást identifikovanou v rozložení zdrojové sítě. Tato akce zajistí, že po přeříznutá do cílové oblasti, vaše virtuální počítače mají všechny funkce a funkce, které jste měli ve zdroji.

    > [!NOTE]
    > Azure Site Recovery automaticky zjišťuje a vytváří účet virtuální sítě a úložiště, když povolíte replikaci pro zdrojový virtuální počítač. Můžete také předem vytvořit tyto prostředky a přiřadit k virtuálnímu virtuálnímu soudu jako součást kroku povolit replikaci. Ale pro všechny ostatní prostředky, jak je uvedeno později, je třeba ručně vytvořit v cílové oblasti.

     Následující dokumenty říkají, jak vytvořit nejčastěji používané síťové prostředky, které jsou pro vás relevantní, na základě konfigurace zdrojového virtuálního počítače.

    - [Skupiny zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)
    - [Služby vyrovnávání zatížení](https://docs.microsoft.com/azure/load-balancer)
    - [Veřejná IP adresa](../virtual-network/virtual-network-public-ip-address.md)
    
   Další síťové součásti naleznete v [dokumentaci](https://docs.microsoft.com/azure/?pivot=products&panel=network)k síti .

    > [!IMPORTANT]
    > Ujistěte se, že v cíli používáte redundantní systém vyrovnávání zatížení. Můžete si přečíst více na [standardní vyrovnávání zatížení a dostupnost zóny](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones).

4. Chcete-li otestovat konfiguraci před přeškrtnutím do cílové oblasti, vytvořte ručně [neprodukční síť](https://docs.microsoft.com/azure/virtual-network/quick-create-portal) v cílové oblasti. Doporučujeme tento přístup, protože způsobuje minimální rušení v produkčním prostředí.

## <a name="enable-replication"></a>Povolení replikace
Následující kroky vás provedou při použití Azure Site Recovery k povolení replikace dat do cílové oblasti, než je nakonec přesunete do zón dostupnosti.

> [!NOTE]
> Tyto kroky jsou pro jeden virtuální virtuální podnik. Můžete rozšířit stejné na více virtuálních počítačů. Přejděte do trezoru služby Recovery Services, vyberte **+ Replikovat**a vyberte příslušné virtuální aplikace společně.

1. Na webu Azure Portal vyberte **Virtuální počítače**a vyberte virtuální počítač, který chcete přesunout do zón dostupnosti.
2. V části **Operace** vyberte **Zotavení po havárii**.
3. V části Konfigurace > **cílové oblasti**pro zotavení **po havárii**vyberte cílovou oblast, do které budete replikovat. Ujistěte se, že tato oblast [podporuje](https://docs.microsoft.com/azure/availability-zones/az-overview#services-support-by-region) zóny dostupnosti.

    ![Výběr cílové oblasti](media/azure-vms-to-zones/enable-rep-1.PNG)

4. Vyberte **další: Upřesnit nastavení**.
5. Zvolte příslušné hodnoty pro cílové předplatné, cílovou skupinu prostředků virtuálního počítače a virtuální síť.
6. V části **Dostupnost** vyberte zónu dostupnosti, do které chcete virtuální ho přesunout. 
   > [!NOTE]
   > Pokud nevidíte možnost pro dostupnost sady nebo dostupnost zóny, ujistěte se, že [jsou splněny požadavky](#prepare-the-source-vms) a [příprava](#prepare-the-source-vms) zdrojových virtuálních aplikací je kompletní.
  
    ![Výběry pro výběr zóny dostupnosti](media/azure-vms-to-zones/enable-rep-2.PNG)

7. Vyberte **Povolit replikaci**. Tato akce spustí úlohu povolit replikaci pro virtuální ho.

## <a name="check-settings"></a>Zkontrolovat nastavení

Po dokončení úlohy replikace můžete zkontrolovat stav replikace, upravit nastavení replikace a otestovat nasazení.

1. V nabídce virtuálního počítače vyberte **Zotavení po havárii**.
2. Můžete zkontrolovat stav replikace, body obnovení, které byly vytvořeny a zdroj a cílové oblasti na mapě.

   ![Stav replikace](media/azure-to-azure-quickstart/replication-status.png)

## <a name="test-the-configuration"></a>Otestujte konfiguraci

1. V nabídce virtuálního počítače vyberte **zotavení po havárii**.
2. Vyberte ikonu **Test failover.**
3. V **části Test Failover**vyberte bod obnovení, který chcete použít pro převzetí služeb při selhání:

   - **Nejnovější zpracovaný:** Vrátí virtuální počítač k nejnovějšímu bodu obnovení zpracovanému službou Site Recovery. Zobrazí se časové razítko. U této možnosti nedochází k prodlevě při zpracování dat, takže poskytuje nízkou plánovanou dobu obnovení (RTO).
   - **Nejnovější konzistentní vzhledem k aplikacím**: Tato možnost vrátí všechny virtuální počítače k nejnovějšímu konzistentnímu bodu obnovení vzhledem k aplikacím. Zobrazí se časové razítko.
   - **Vlastní**: Vyberete si libovolný bod obnovení.

3. Vyberte testovací cíl virtuální sítě Azure, do které chcete přesunout virtuální počítače Azure k testování konfigurace. 

    > [!IMPORTANT]
    > Doporučujeme použít samostatnou síť virtuálních počítačích Azure pro selhání testu a ne produkční síť v cílové oblasti, do které chcete přesunout virtuální počítače.

4. Chcete-li zahájit testování přesunu, vyberte **možnost OK**. Chcete-li sledovat průběh, vyberte virtuální počítače otevřít jeho vlastnosti. Nebo můžete vybrat **úlohu Test failover** u převzetí služeb při selhání v názvu trezoru >**úlohy** > **obnovení webu** **nastavení** > .
5. Po dokončení převzetí služeb při selhání se na portálu Azure Portal v části **Virtuální počítače** objeví replika virtuálního počítače Azure. Ujistěte se, že je virtuální počítač spuštěný, má odpovídající velikost a je připojený k odpovídající síti.
6. Pokud chcete odstranit virtuální počítač vytvořený jako součást testování přesunutí, vyberte **možnost Převzetí služeb při selhání testu vyčištění** u replikované položky. V **poznámkách**zaznamenáte a uložte všechna pozorování spojená s testem.

## <a name="move-to-the-target-region-and-confirm"></a>Přesunout do cílové oblasti a potvrdit

1.  V nabídce virtuálního počítače vyberte **zotavení po havárii**.
2. Vyberte ikonu **převzetí služeb při selhání.**
3. V okně **Převzetí služeb při selhání** vyberte **Nejnovější**. 
4. Vyberte **Před spuštěním převzetí služeb při selhání vypnout počítač**. Služba Site Recovery se před aktivací převzetí služeb při selhání pokusí zdrojový virtuální počítač vypnout. Převzetí služeb při selhání bude pokračovat i v případě, že se vypnutí nepovede. Průběh převzetí služeb při selhání můžete sledovat na stránce **Úlohy**. 
5. Po dokončení úlohy zkontrolujte, zda se virtuální počítač zobrazí v cílové oblasti Azure podle očekávání.
6. V části **Replikované položky** klikněte pravým tlačítkem na virtuální počítač a pak na **Potvrdit**. Tím dokončíte proces přesunutí do cílové oblasti. Počkejte, až bude úloha potvrzení dokončena.

## <a name="discard-the-resource-in-the-source-region"></a>Zahodit prostředek ve zdrojové oblasti

Přejděte na virtuální m. Vyberte **zakázat replikaci**. Tato akce zastaví proces kopírování dat pro virtuální hod.  

> [!IMPORTANT]
> Proveďte předchozí krok, abyste se vyhnuli účtování za replikaci obnovení lokality po přesunutí. Automaticky se vyčistí nastavení replikace zdroje. Všimněte si, že rozšíření site recovery, která je nainstalována jako součást replikace není odebrána a je třeba odebrat ručně.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zvýšili dostupnost virtuálního počítače Azure přesunutím do skupiny dostupnosti nebo zóny dostupnosti. Teď můžete nastavit zotavení po havárii pro přesunutý virtuální virtuální ms.

> [!div class="nextstepaction"]
> [Nastavení zotavení po havárii po migraci](azure-to-azure-quickstart.md)


