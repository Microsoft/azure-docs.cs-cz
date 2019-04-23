---
title: Přesun virtuálních počítačů Azure IaaS do jiné oblasti Azure jako zóna připnuté virtuálních počítačů pomocí služby Azure Site Recovery | Dokumentace Microsoftu
description: Přesun virtuálních počítačů Azure IaaS do jiné oblasti Azure jako zóna připnout virtuální počítače pomocí Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: b6107211f49978bbacd1a827a9adc37ccef60a5b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60196159"
---
# <a name="move-azure-vms-into-availability-zones"></a>Přesuňte virtuální počítače Azure do zóny dostupnosti
Zóny dostupnosti v Azure pomáhají chránit vaše aplikace a data před selháními datových center. Každou zónu dostupnosti tvoří jedno nebo několik datových center vybavených nezávislým napájením, chlazením a sítí. K zajištění odolnosti proti chybám, je minimálně tří samostatných zón ve všech oblastech, povolené. Fyzické oddělení zón dostupnosti v rámci oblasti chrání aplikace a data před selháními datových center. Zóny dostupnosti nabízí Azure smlouvu o úrovni služeb (SLA) 99,99 % provozuschopnost virtuálních počítačů (VM). Ve vybraných oblastech se podporují zóny dostupnosti, jak je uvedeno v [co jsou zóny dostupnosti v Azure?](https://docs.microsoft.com/azure/availability-zones/az-overview#regions-that-support-availability-zones).

Ve scénáři, kde jsou vaše virtuální počítače nasazené jako *jednu instanci* v konkrétní oblasti kde chcete zlepšit dostupnost přesunutím tyto virtuální počítače v zóně dostupnosti, můžete to provést pomocí Azure Site Recovery. Tato akce může dále rozdělená na:

- Přesuňte virtuální počítače s jednou instancí do zón dostupnosti v cílové oblasti
- Přesunout virtuální počítače ve skupině dostupnosti do zóny dostupnosti v cílové oblasti

> [!IMPORTANT]
> V současné době Azure Site Recovery podporuje přesun virtuálních počítačů z jedné oblasti do jiného, ale nepodporuje přesun v rámci oblasti.

## <a name="check-prerequisites"></a>Kontrola předpokladů

- Zkontrolujte, jestli má cílové oblasti [podpory pro zóny dostupnosti](https://docs.microsoft.com/azure/availability-zones/az-overview#regions-that-support-availability-zones). Zkontrolujte, že podle vaší volby [podporované zdrojové oblasti nebo cíle oblasti kombinaci](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support). Proveďte informované rozhodnutí v cílové oblasti.
- Ujistěte se, že rozumíte [komponentám a architektuře řešení](azure-to-azure-architecture.md).
- Zkontrolujte [omezení podpory a požadavky](azure-to-azure-support-matrix.md).
- Zkontrolujte oprávnění účtu. Pokud jste právě vytvořili bezplatný účet Azure, jste správcem vašeho předplatného. Pokud si nejste správce předplatného, pracujete s správce přiřazení potřebných oprávnění. Povolení replikace pro virtuální počítač a nakonec k cíli kopírování dat pomocí Azure Site Recovery, musíte mít:

    1. Oprávnění k vytvoření virtuálního počítače v prostředcích Azure. *Přispěvatel virtuálních počítačů* předdefinovaná role má tato oprávnění, mezi které patří:
        - Oprávnění k vytvoření virtuálního počítače ve vybrané skupině prostředků
        - Oprávnění k vytvoření virtuálního počítače ve vybrané virtuální síti
        - Oprávnění zapisovat na vybraný účet úložiště

    2. Oprávnění ke správě úloh Azure Site Recovery. *Přispěvatel Site Recovery* role má všechna oprávnění nutná ke správě akce Site Recovery v trezoru služby Recovery Services.

## <a name="prepare-the-source-vms"></a>Příprava zdrojové virtuální počítače

1. Vaše virtuální počítače musí používat spravované disky, pokud chcete, a přesunout jej do zóně dostupnosti s využitím Site Recovery. Můžete převést stávající virtuální počítače Windows, který použití nespravovaných disků použít spravované disky. Postupujte podle kroků uvedených v [převod virtuálního počítače s Windows z nespravovaných disků na managed disks](https://docs.microsoft.com/azure/virtual-machines/windows/convert-unmanaged-to-managed-disks). Ujistěte se, že skupina dostupnosti je nakonfigurovaný jako *spravované*.
2. Zkontrolujte, že jsou na virtuálních počítačích Azure, kterou chcete přesunout všechny nejnovější kořenové certifikáty. Pokud nejsou k dispozici nejnovější kořenové certifikáty, kopírování dat do cílové oblasti není možné kvůli omezením zabezpečení.

3. U virtuálních počítačů s Windows zajistíte přítomnost všech důvěryhodných kořenových certifikátů tím, že na ně nainstalujete všechny nejnovější aktualizace Windows. V odpojeném prostředí postupujte podle standardní Windows update a certifikát aktualizace procesy pro vaši organizaci.

4. Virtuální počítače s Linuxem postupujte podle pokynů, které jste dostali od distributora Linuxu pro získání nejnovějších důvěryhodných kořenových certifikátů a seznamu odvolaných certifikátů na virtuálním počítači.
5. Ujistěte se, že ověřovací proxy server nepoužíváte k řízení síťového připojení pro virtuální počítače, které chcete přesunout.

6. Pokud se pokoušíte přesunout virtuální počítač nemá přístup k Internetu a používá proxy server brány firewall k řízení odchozího přístupu, zkontrolujte požadavky na [ konfigurace odchozího síťového připojení](azure-to-azure-tutorial-enable-replication.md#configure-outbound-network-connectivity).

7. Určete rozložení síťové zdroje a prostředky, které aktuálně používáte službu pro ověření, včetně nástroje pro vyrovnávání zatížení, skupin zabezpečení sítě a veřejné IP adresy.

## <a name="prepare-the-target-region"></a>Připravte cílové oblasti

1. Zkontrolujte, že vaše předplatné Azure umožňuje vytvářet virtuální počítače v cílové oblasti používané ke zotavení po havárii. V případě potřeby požádejte podporu o povolení požadované kvóty.

2. Zkontrolujte, jestli máte v rámci předplatného k dispozici dostatek prostředků pro podporu virtuálních počítačů s takovými velikostmi, které odpovídají velikostem zdrojových virtuálních počítačů. Pokud použijete Site Recovery pro kopírování dat do cíle, použije stejnou nebo nejbližší možnou velikost pro cílový virtuální počítač.

3. Vytvořte cílový prostředek pro všechny komponenty jsou identifikované v rozložení síťové zdroje. Tím zajistíte, že po vyjmout v cílové oblasti vaše virtuální počítače mají všechny funkce a funkce, které jste měli ve zdroji.

    > [!NOTE]
    > Azure Site Recovery automaticky zjistí a vytvoří virtuální síť a úložiště účtu při povolení replikace pro zdrojový virtuální počítač. Můžete také předem vytvořit tyto prostředky a přiřadit k virtuálnímu počítači jako součást kroku povolení replikace. Ale pro všechny další prostředky, jak je uvedeno dále, budete muset vytvořit ručně v cílové oblasti.

     Následující dokumenty zjistit, jak vytvořit nejčastěji používanou síťovým prostředkům, které jsou relevantní pro vás v závislosti na konfiguraci zdrojového virtuálního počítače.

    - [Skupiny zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)
    - [Nástroje pro vyrovnávání zatížení](https://docs.microsoft.com/azure/load-balancer/#step-by-step-tutorials)
    - [Veřejná IP adresa](https://docs.microsoft.com/azure/load-balancer/#step-by-step-tutorials)
    
   Ostatními síťovými součástmi, najdete v části sítě [dokumentaci](https://docs.microsoft.com/azure/#pivot=products&panel=network).

    > [!IMPORTANT]
    > Ujistěte se, že použijete nástroj pro vyrovnávání zatížení zónově redundantní v cílové. Další informace na [Load balanceru úrovně Standard a zóny dostupnosti](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones).

4. Ručně [vytvořit síť neprodukčním](https://docs.microsoft.com/azure/virtual-network/quick-create-portal) v cílové oblasti, pokud chcete otestovat konfiguraci před vyjmout v cílové oblasti. Doporučujeme tento přístup, protože to způsobí, že minimálním narušením s produkčním prostředím.

## <a name="enable-replication"></a>Povolení replikace
Následující kroky vás provedou, když Pokud chcete povolit replikaci dat do cílové oblasti, než je nakonec přesunou do zón dostupnosti pomocí Azure Site Recovery.

> [!NOTE]
> Tyto kroky jsou pro jeden virtuální počítač. Stejné můžete rozšířit na více virtuálních počítačů. Přejděte do trezoru služby Recovery Services, vyberte **+ replikovat**a vyberte příslušné virtuálních počítačů dohromady.

1. Na webu Azure Portal, vyberte **virtuálních počítačů**a vyberte virtuální počítač, který chcete přesunout do zóny dostupnosti.
2. V **operace**vyberte **zotavení po havárii**.
3. V **konfigurace zotavení po havárii** > **cílové oblasti**, vyberte cílovou oblast, do kterého budete replikovat. Zkontrolujte tuto oblast [podporuje](https://docs.microsoft.com/azure/availability-zones/az-overview#regions-that-support-availability-zones) zóny dostupnosti.

    ![Výběr cílové oblasti](media/azure-vms-to-zones/enable-rep-1.PNG)

4. Vyberte **Další: Upřesňující nastavení**.
5. Zvolte příslušné hodnoty pro cílové předplatné, skupina prostředků cílového virtuálního počítače a virtuální sítě.
6. V **dostupnosti** zvolte zónu dostupnosti, do kterého chcete přesunout virtuální počítač. 
   > [!NOTE]
   > Pokud nevidíte možnost pro skupinu dostupnosti nebo dostupností zóny, ujistěte se, že [požadavky](#prepare-the-source-vms) splnění a [přípravy](#prepare-the-source-vms) zdrojové virtuální počítače je kompletní.
  
    ![Výběry pro výběr zóně dostupnosti](media/azure-vms-to-zones/enable-rep-2.PNG)

7. Vyberte **Povolit replikaci**. Tato akce spustí úlohu povolit replikaci pro virtuální počítač.

## <a name="check-settings"></a>Zkontrolujte nastavení

Po dokončení úlohy replikace můžete zkontrolovat stav replikace, upravit nastavení replikace a otestovat nasazení.

1. V nabídce virtuálního počítače vyberte **zotavení po havárii**.
2. Můžete zkontrolovat stav replikace, body obnovení, které byly vytvořeny a zdrojové a cílové oblasti na mapě.

   ![Stav replikace](media/azure-to-azure-quickstart/replication-status.png)

## <a name="test-the-configuration"></a>Otestujte konfiguraci

1. V nabídce virtuálního počítače vyberte **zotavení po havárii**.
2. Vyberte **testovací převzetí služeb při selhání** ikonu.
3. V **testovací převzetí služeb při selhání**, vyberte bod obnovení pro převzetí služeb:

   - **Nejnovější zpracovaný**: Převezme virtuálního počítače k nejnovějšímu bodu obnovení zpracovanému službou Site Recovery. Časové razítko je vidět. U této možnosti nedochází k prodlevě při zpracování dat, takže poskytuje nízkou plánovanou dobu obnovení (RTO).
   - **Nejnovější konzistentní vzhledem k**: Tato možnost převezme služby při selhání všech virtuálních počítačů k nejnovějšímu bodu obnovení s konzistentní aplikací. Časové razítko je vidět.
   - **Vlastní**: Zvolte jakýkoli bod obnovení.

3. Vybrat testovací cílové virtuální síť Azure ke kterému chcete přesunout virtuální počítače Azure k testování této konfigurace. 

    > [!IMPORTANT]
    > Doporučujeme použít samostatnou síť virtuálních počítačů Azure pro selhání testu a ne produkční síti v cílové oblasti, do kterého chcete přesunout virtuální počítače.

4. Chcete-li spustit testování přesunutí, vyberte **OK**. Pokud chcete sledovat průběh, vyberte virtuální počítač, otevřete jeho vlastnosti. Nebo můžete vybrat **testovací převzetí služeb při selhání** úlohy v části název_trezoru > **nastavení** > **úlohy** > **úlohy Site Recovery**.
5. Po dokončení převzetí služeb při selhání se na portálu Azure Portal v části **Virtuální počítače** objeví replika virtuálního počítače Azure. Ujistěte se, že je virtuální počítač spuštěný, má odpovídající velikost a je připojený k odpovídající síti.
6. Pokud chcete odstranit virtuální počítač vytvořen jako součást přesunu testování, vyberte možnost **vyčištění testovacího převzetí služeb při selhání** na replikovanou položku. V **poznámky**si zaznamenejte a uložte jakékoli připomínky související s testem.

## <a name="move-to-the-target-region-and-confirm"></a>Přesunout do cílové oblasti a potvrzení

1.  V nabídce virtuálního počítače vyberte **zotavení po havárii**.
2. Vyberte **převzetí služeb při selhání** ikonu.
3. V okně **Převzetí služeb při selhání** vyberte **Nejnovější**. 
4. Vyberte **Před spuštěním převzetí služeb při selhání vypnout počítač**. Služba Site Recovery se před aktivací převzetí služeb při selhání pokusí zdrojový virtuální počítač vypnout. Převzetí služeb při selhání bude pokračovat i v případě, že se vypnutí nepovede. Průběh převzetí služeb při selhání můžete sledovat na stránce **Úlohy**. 
5. Po dokončení úlohy zkontrolujte, že virtuální počítač zobrazuje v cílové oblasti Azure podle očekávání.
6. V části **Replikované položky** klikněte pravým tlačítkem na virtuální počítač a pak na **Potvrdit**. Tím se dokončí proces přesunu na cílové oblasti. Počkejte na dokončení úlohy potvrzení.

## <a name="discard-the-resource-in-the-source-region"></a>Zahodit prostředků ve zdrojové oblasti

Přejděte k virtuálnímu počítači. Vyberte **zakázat replikaci**. Tato akce zastaví proces kopírování dat pro virtuální počítač.  

> [!IMPORTANT]
> Proveďte předchozí krok, vyhněte se účtovat poplatky, a pro replikace Site Recovery po přesunutí. Automaticky se vyčistí nastavení replikace zdroje. Všimněte si, že rozšíření služby Site Recovery, který je nainstalován jako součást replikace není odebrán a musí být odstraněny ručně.

## <a name="next-steps"></a>Další postup

V tomto kurzu se zvýšit dostupnost virtuálního počítače Azure přesunete do skupiny dostupnosti nebo zónu dostupnosti. Nyní můžete nastavit zotavení po havárii pro přesunutý virtuální počítač.

> [!div class="nextstepaction"]
> [Nastavení zotavení po havárii po migraci](azure-to-azure-quickstart.md)


