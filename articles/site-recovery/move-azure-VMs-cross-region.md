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
ms.openlocfilehash: bbede01844f20c0240b154fd319b818a43463131
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55823356"
---
# <a name="move-azure-vms-to-another-region"></a>Přesun virtuálních počítačů Azure do jiné oblasti

Existují různé scénáře, ve kterém chcete přesunout vaše stávající virtuální počítače Azure IaaS z jedné oblasti do jiného – pro zvýšení spolehlivosti a dostupnosti vašich stávajících virtuálních počítačů nebo k vylepšení možností správy nebo z nějakého důvodu podobně, jak je uvedeno, zásad správného řízení. 

V tomto kurzu se dozvíte, jak přesunout virtuální počítače Azure do jiné oblasti pomocí Azure Site Recovery. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * [Ověřte požadavky](#verify-prerequisites)
> * [Příprava zdrojové virtuální počítače](#prepare-the-source-vms)
> * [Připravte cílové oblasti](#prepare-the-target-region)
> * [Kopírování dat do cílové oblasti](#copy-data-to-the-target-region)
> * [Otestujte konfiguraci ](#test-the-configuration)
> * [Provedení přesunu ](#perform-the-move-to-the-target-region-and-confirm)
> * [Zahodit prostředků ve zdrojové oblasti ](#discard-the-resource-in-the-source-region)

> [!IMPORTANT]
> Tento dokument vás přesunout virtuální počítače Azure z jedné oblasti do jiného, jak je, pokud váš požadavek se má zvýšit dostupnost vaší infrastruktury přesun virtuálních počítačů do zóny dostupnosti, podívejte se na kurz tady.

## <a name="verify-prerequisites"></a>Ověřte požadavky

- Ujistěte se, že máte virtuální počítače Azure ve zdrojové oblasti Azure, ze které chcete přesunout.
- Ověření, jestli podle vaší volby [podporované zdrojové oblasti – kombinace cílové oblasti](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support) a informovaně rozhodnout, jestli v cílové oblasti.
- Ujistěte se, že rozumíte [komponentám a architektuře řešení](azure-to-azure-architecture.md).
- Zkontrolujte [omezení podpory a požadavky](azure-to-azure-support-matrix.md).
- Ověření oprávnění účtu: Pokud jste si právě vytvořili bezplatný účet Azure, pak jste správcem předplatného. Jestliže správcem předplatného nejste, požádejte správce o přiřazení potřebných oprávnění. Povolení replikace pro virtuální počítač a nakonec kopírování dat k cíli pomocí Azure Site Recovery, musíte mít:

    1. Oprávnění k vytvoření virtuálního počítače v prostředcích Azure. Tato oprávnění má předdefinovaná role Přispěvatel virtuálních počítačů. Patří k nim:
        - Oprávnění k vytvoření virtuálního počítače ve vybrané skupině prostředků
        - Oprávnění k vytvoření virtuálního počítače ve vybrané virtuální síti
        - Oprávnění zapisovat na vybraný účet úložiště

    2. Potřebujete také oprávnění ke správě operací služby Azure Site Recovery. Všechna oprávnění nutná ke správě operací služby Site Recovery v  trezoru služby Recovery Services má role Přispěvatel Site Recovery.

## <a name="prepare-the-source-vms"></a>Příprava zdrojové virtuální počítače

1. Zkontrolujte, že jsou na virtuálních počítačích Azure, kterou chcete přesunout všechny nejnovější kořenové certifikáty. Pokud nejsou k dispozici nejnovější kořenové certifikáty, kopírování dat do cílové oblasti není možné kvůli omezení zabezpečení.

    - U virtuálních počítačů s Windows zajistíte přítomnost všech důvěryhodných kořenových certifikátů tím, že na ně nainstalujete všechny nejnovější aktualizace Windows. V odpojeném prostředí dodržujte při aktualizaci Windows a certifikátů standardní postupy uplatňované ve vaší organizaci.
    - U virtuálních počítačů s Linuxem postupujte při získávání nejnovějších důvěryhodných kořenových certifikátů a seznamu odvolaných certifikátů na virtuálním počítači podle pokynů, které jste dostali od distributora Linuxu.
2. Ujistěte se, že ověřovací proxy server nepoužíváte k řízení síťového připojení pro virtuální počítače chcete přesunout.
3. Pokud se pokoušíte přesunout virtuální počítač nemá přístup k Internetu a k řízení odchozího přístupu používá proxy server brány firewall, Zkontrolujte prosím požadavky [tady](azure-to-azure-tutorial-enable-replication.md#configure-outbound-network-connectivity).
4. Identifikujte a seznamu dolů zdrojové sítě rozložení a všechny prostředky, které aktuálně používáte – včetně, ale mimo jiné nástroje pro vyrovnávání, skupin zabezpečení sítě, public atd IP pro ověření zatížení.

## <a name="prepare-the-target-region"></a>Připravte cílové oblasti

1. Ověřte, že vám předplatné Azure umožňuje vytvářet virtuální počítače v cílové oblasti používané ke zotavení po havárii. Požádejte podporu o povolení požadované kvóty, pokud je to nutné.

2. Zkontrolujte, jestli máte v rámci předplatného k dispozici dostatek prostředků pro podporu virtuálních počítačů s takovými velikostmi, které odpovídají velikostem zdrojových virtuálních počítačů. Pokud použijete Site Recovery pro kopírování dat do cíle, použije stejnou nebo nejbližší možnou velikost pro cílový virtuální počítač.

3. Ujistěte se, že vytvoříte cílový prostředek pro všechny komponenty jsou identifikované v rozložení síťové zdroje. To je důležité k zajištění toho, aby, účtovat společné cílové oblasti, vaše virtuální počítače mají všechny funkce a funkce, které jste měli ve zdroji.

    > [!NOTE]
    > Azure Site Recovery automaticky zjistí a vytvoří virtuální síť a účet úložiště, když povolíte replikaci pro zdrojový virtuální počítač, nebo také můžete předem vytvořit tyto prostředky a přiřadit k virtuálnímu počítači jako součást kroku povolení replikace. Ale pro všechny další prostředky, jak je uvedeno níže, je potřeba vytvořit ručně v cílové oblasti.

     Najdete v následujících dokumentech k vytvoření nejčastěji používanou síťovým prostředkům relevantní pro vás v závislosti na konfiguraci zdrojového virtuálního počítače.

    - [Skupiny zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)
    - [Nástroje pro vyrovnávání zatížení](https://docs.microsoft.com/azure/load-balancer/#step-by-step-tutorials)
    - [Veřejná IP adresa ](https://docs.microsoft.com/azure/load-balancer/#step-by-step-tutorials)
    
    Ostatními síťovými součástmi, najdete v části sítě [dokumentaci.](https://docs.microsoft.com/azure/#pivot=products&panel=network) 

4. Ručně [vytvořit síť neprodukčním](https://docs.microsoft.com/azure/virtual-network/quick-create-portal) v cílové oblasti, pokud chcete otestovat konfiguraci, před provedením konečné vyjmutí v cílové oblasti. Tím se vytvoří minimálním narušením s produkčním prostředím a doporučuje se.
    
## <a name="copy-data-to-the-target-region"></a>Kopírování dat do cílové oblasti
Níže uvedený postup se dozvíte, jak pomocí Azure Site Recovery ke zkopírování dat do cílové oblasti.

### <a name="create-the-vault-in-any-region-except-the-source-region"></a>V libovolné oblasti (s výjimkou zdrojové oblasti) vytvořte trezor.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) > **Recovery Services**.
2. Klikněte na tlačítko **vytvořit prostředek** > **nástroje pro správu** > **Backup a Site Recovery**.
3. Do pole **Název** zadejte popisný název **ContosoVMVault**. Pokud máte více než jeden. předplatné, vyberte příslušné předplatné.
4. Vytvořte skupinu prostředků **ContosoRG**.
5. Zadejte oblast Azure. Informace o tom, které oblasti jsou podporované, najdete v části s geografickou dostupností v tématu s [podrobnostmi o cenách Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
6. V trezorech služby Recovery Services, klikněte na tlačítko **přehled** > **ConsotoVMVault** > **+ replikovat**.
7. V rozevíracím seznamu **Zdroj** vyberte **Azure**.
8. V rozevíracím seznamu **Umístění zdroje** vyberte zdrojovou oblast Azure, kde máte virtuální počítače aktuálně spuštěné.
9. Vyberte model nasazení Resource Manager. Vyberte **zdrojové předplatné** a **zdrojovou skupinu prostředků**.
10. Kliknutím na **OK** uložte nastavení.

### <a name="enable-replication-for-azure-vms-and-start-copying-the-data"></a>Povolení replikace pro virtuální počítače Azure a zahájit kopírování data.

Služba Site Recovery načte seznam virtuálních počítačů přidružených k předplatnému a skupinu prostředků.

1. V dalším kroku. Vyberte virtuální počítač, který chcete přesunout. Pak klikněte na **OK**.
3. V části **Nastavení** klikněte na **Zotavení po havárii**.
4. V části **Konfigurovat zotavení po havárii** > **Cílová oblast** vyberte cílovou oblast, do které chcete replikaci provést.
5. Můžete buď využít výchozí cílové prostředky nebo použít ty, které jste předem vytvořili.
6. Klikněte na **Povolit replikaci**. Tím se spustí úloha, která povolí replikaci pro daný virtuální počítač.

    ![Povolení replikace](media/tutorial-migrate-azure-to-azure/settings.png)

 

## <a name="test-the-configuration"></a>Otestujte konfiguraci


1. Přejděte do trezoru, v **nastavení** > **replikované položky**, klikněte na virtuální počítač, který máte v úmyslu přesunout do cílové oblasti, klikněte na tlačítko **+ testovací převzetí služeb při selhání** ikona.
2. V části **Testovací převzetí služeb při selhání** vyberte bod obnovení, který se má pro převzetí služeb při selhání použít:

   - **Nejnovější zpracovaný**: Převezme virtuálního počítače k nejnovějšímu bodu obnovení zpracovanému službou Site Recovery. Časové razítko je vidět. S touto možností se neztrácí žádný čas zpracováním dat, takže poskytuje nízkou plánovanou dobu obnovení (RTO).
   - **Nejnovější konzistentní vzhledem k**: Tato možnost převezme služby při selhání všech virtuálních počítačů k nejnovějšímu bodu obnovení s konzistentní aplikací. Časové razítko je vidět.
   - **Vlastní**: Zvolte jakýkoli bod obnovení.

3. Vyberte cíl Azure virtuální sítě, ke které chcete přesunout virtuální počítače Azure k testování této konfigurace. 

> [!IMPORTANT]
> Doporučujeme použít samostatnou síť virtuálních počítačů Azure pro selhání testu a ne produkční síti v cílové oblasti, do kterého chcete nakonec přesunout virtuální počítače.

4. Pro začátek testování přesunutí, klikněte na tlačítko **OK**. Pokud chcete sledovat průběh, kliknutím na virtuální počítač otevřete jeho vlastnosti. Případně můžete kliknout na úlohu **Testovací převzetí služeb při selhání** v části název_trezoru > **Nastavení** > **Úlohy** > **Úlohy Site Recovery**.
5. Po dokončení převzetí služeb při selhání se replika virtuálního počítače Azure zobrazí na webu Azure Portal v části **Virtuální počítače**. Ujistěte se, že je virtuální počítač spuštěný, má odpovídající velikost a je připojený k odpovídající síti.
6. Pokud chcete odstranit virtuální počítač vytvořen jako součást přesunu testování, klikněte na tlačítko **vyčištění testovacího převzetí služeb při selhání** na replikovanou položku. V **poznámky**si zaznamenejte a uložte jakékoli připomínky související s testem.

## <a name="perform-the-move-to-the-target-region-and-confirm"></a>Provedení přesunu do cílové oblasti a potvrďte.

1.  Přejděte do trezoru, v **nastavení** > **replikované položky**, klikněte na virtuální počítač a potom klikněte na tlačítko **převzetí služeb při selhání**.
2. V okně **Převzetí služeb při selhání** vyberte **Nejnovější**. 
3. Vyberte **Před spuštěním převzetí služeb při selhání vypnout počítač**. Služba Site Recovery se před aktivací převzetí služeb při selhání pokusí zdrojový virtuální počítač vypnout. Převzetí služeb při selhání bude pokračovat i v případě, že se vypnutí nepovede. Průběh převzetí služeb při selhání můžete sledovat na stránce **Úlohy**. 
4. Po dokončení úlohy zkontrolujte, že virtuální počítač zobrazuje v cílové oblasti Azure podle očekávání.
5. V části **Replikované položky** klikněte pravým tlačítkem na virtuální počítač a pak na **Potvrdit**. Tím se dokončí proces přesunu na cílové oblasti. Počkejte, až se dokončí úloha potvrzení.

## <a name="discard-the-resource-in-the-source-region"></a>Zahodit prostředků ve zdrojové oblasti 

1. Přejděte k virtuálnímu počítači.  Klikněte na **zakázat replikaci**.  Tím se zastaví proces kopírování dat pro virtuální počítač.  

> [!IMPORTANT]
> Je důležité provést z předchozího kroku, aby se zabránilo odepsání pro metodu post replikace Site Recovery přesunutí.

V případě, že nemáte v úmyslu použít některý z prostředků zdroj prosím pokračujte další sadů kroků.

1. Přejít k odstranění všech příslušných síťových prostředků ve zdrojové oblasti, která vypsali jako součást kroku 4 v [Příprava zdrojové virtuální počítače](#prepare-the-source-vms) 
2. K odstranění odpovídajícího účtu úložiště ve zdrojové oblasti.

## <a name="next-steps"></a>Další postup

V tomto kurzu přesunout virtuální počítač Azure do jiné oblasti Azure. Teď můžete nakonfigurovat zotavení po havárii pro přesunutý virtuální počítač.

> [!div class="nextstepaction"]
> [Nastavení zotavení po havárii po migraci](azure-to-azure-quickstart.md)

