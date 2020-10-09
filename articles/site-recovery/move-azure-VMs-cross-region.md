---
title: Přesuňte virtuální počítače Azure do jiné oblasti pomocí Azure Site Recovery
description: Pomocí Azure Site Recovery můžete přesunout virtuální počítače Azure IaaS z jedné oblasti Azure do jiné.
services: site-recovery
author: sideeksh
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: sideeksh
ms.custom: MVC
ms.openlocfilehash: 11767e7369648ad2f4dec4480fbad0f6218446fb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89425411"
---
# <a name="move-azure-vms-to-another-azure-region"></a>Přesun virtuálních počítačů Azure do jiné oblasti Azure

Možná budete chtít přesunout virtuální počítače infrastruktury Azure jako služby (IaaS) z jedné oblasti do druhé a zvýšit tak spolehlivost, dostupnost, správu nebo řízení. V tomto kurzu se dozvíte, jak přesunout virtuální počítače do jiné oblasti pomocí Azure Site Recovery. Dozvíte se, jak provést tyto akce:

> [!div class="checklist"]
> * Ověření požadavků
> * Příprava zdrojových virtuálních počítačů
> * Příprava cílové oblasti
> * Kopírovat data do cílové oblasti
> * Otestování konfigurace
> * Provést přesun
> * Zahodí prostředky ze zdrojové oblasti.


> [!IMPORTANT]
> Tento článek popisuje, jak přesunout virtuální počítače Azure z jedné oblasti do jiné *, jak je*to. Pokud je vaším cílem zlepšit dostupnost infrastruktury přesunutím virtuálních počítačů do zón dostupnosti, přečtěte si téma [přesunutí virtuálních počítačů Azure do zóny dostupnosti](move-azure-vms-avset-azone.md).

## <a name="prerequisites"></a>Požadavky

- Ujistěte se, že máte virtuální počítače Azure ve zdrojové oblasti Azure, *ze*které chcete přejít.
- Ověřte, zda [je podporována kombinace zdrojové oblasti a cílové](./azure-to-azure-support-matrix.md#region-support)oblasti a pečlivě vyberte cílovou oblast.
- Ujistěte se, že rozumíte [komponentám a architektuře řešení](azure-to-azure-architecture.md).
- Zkontrolujte [omezení podpory a požadavky](azure-to-azure-support-matrix.md).
- Ověřte oprávnění účtu. Pokud jste právě vytvořili bezplatný účet Azure, *jste* správcem předplatného. Pokud nejste správcem, můžete ve spolupráci se správcem získat potřebná oprávnění:
  -  Pokud chcete pro virtuální počítač povolit replikaci a kopírovat data do cíle pomocí Site Recovery, musíte mít oprávnění k vytvoření virtuálního počítače v prostředcích Azure. Tato oprávnění má předdefinovaná role Přispěvatel virtuálních počítačů. S oprávněními můžete:
        - Vytvoření virtuálního počítače ve vybrané skupině prostředků
        - Vytvoření virtuálního počítače ve vybrané virtuální síti
        - Zápis do vybraného účtu úložiště

  - Potřebujete také oprávnění ke správě Site Recoverych operací. Role Přispěvatel Site Recovery má všechna oprávnění, která jsou nutná ke správě operací Site Recovery v trezoru služby Azure Recovery Services.

## <a name="prepare-the-source-vms"></a>Příprava zdrojových virtuálních počítačů

1. Ověřte, že virtuální počítače Azure, které plánujete přesunout, mají nejnovější kořenové certifikáty. Pokud ne, nemůžete povolit kopírování dat do cílové oblasti z důvodu omezení zabezpečení.

    - Pro virtuální počítače s Windows nainstalujte nejnovější aktualizace Windows, aby se na počítači používaly všechny důvěryhodné kořenové certifikáty. V odpojeném prostředí použijte standardní web Windows Update a procesy aktualizace certifikátů pro vaši organizaci.
    - Pro virtuální počítače se systémem Linux postupujte podle pokynů od distributora Linux a získejte nejnovější důvěryhodné kořenové certifikáty a seznam odvolaných certifikátů.
2. Ujistěte se, že nepoužíváte ověřovací proxy server k řízení připojení k síti pro virtuální počítače, které chcete přesunout.
3. Pokud virtuální počítač, který chcete přesunout, nemá přístup k Internetu a používá proxy server brány firewall k řízení odchozího přístupu, Projděte si [požadavky](azure-to-azure-tutorial-enable-replication.md#set-up-outbound-network-connectivity-for-vms).
4. Zdokumentujte rozložení zdrojové sítě a všechny prostředky, které aktuálně používáte, včetně (ale ne omezené) nástrojů pro vyrovnávání zatížení, skupin zabezpečení sítě a veřejných IP adres pro ověřování.

## <a name="prepare-the-target-region"></a>Příprava cílové oblasti

1. V rámci vašeho předplatného Azure ověřte, že můžete vytvořit virtuální počítače v cílové oblasti, která se používá pro zotavení po havárii. Pokud je to nutné, obraťte se na podporu, aby byla požadovaná kvóta povolena.

2. Ujistěte se, že vaše předplatné má dostatek prostředků na podporu vašich zdrojových virtuálních počítačů. Pokud používáte Site Recovery ke kopírování dat do cíle, pro cílové virtuální počítače se vybere stejná velikost nebo nejbližší dostupná velikost.

3. Ujistěte se, že jste vytvořili cílový prostředek pro každou komponentu, kterou jste identifikovali v rozložení zdrojové sítě. Tím se zajistí, že vaše virtuální počítače budou mít všechny funkce a funkce v cílové oblasti, které měly ve zdrojové oblasti.

   Azure Site Recovery automaticky zjistí a vytvoří virtuální síť a účet úložiště, když povolíte replikaci pro zdrojový virtuální počítač. Tyto prostředky můžete také předem vytvořit a přiřadit k virtuálnímu počítači jako součást kroku povolení a replikace. Je však nutné ručně vytvořit všechny další prostředky v cílové oblasti. Informace o tom, jak vytvořit nejčastěji používané síťové prostředky v závislosti na konfiguraci zdrojového virtuálního počítače, najdete v následujících dokumentech:

   - [Skupiny zabezpečení sítě](../virtual-network/manage-network-security-group.md)
   - [Služby vyrovnávání zatížení](../load-balancer/index.yml)
   - [Veřejná IP adresa](../virtual-network/virtual-network-public-ip-address.md)
    
   Další síťové součásti najdete v [dokumentaci k síti Azure](../index.yml?pivot=products&panel=network). 

4. Chcete-li před provedením přesunutí otestovat konfiguraci, [vytvořte ručně neprodukční síť](../virtual-network/quick-create-portal.md) v cílové oblasti. Testování instalačního programu vytváří minimální interference s produkčním prostředím a doporučujeme ho.
    
## <a name="copy-data-to-the-target-region"></a>Kopírovat data do cílové oblasti
Následující kroky používají Azure Site Recovery ke kopírování dat do cílové oblasti.

### <a name="create-the-vault-in-any-region-except-the-source"></a>Vytvoření trezoru v libovolné oblasti s výjimkou zdroje

1. Přihlaste se [Azure portal](https://portal.azure.com)k  >  **Recovery Services**Azure Portal.
2. Vyberte **vytvořit**  >  **Nástroje pro správu**prostředků  >  **zálohování a Site Recovery**.
3. Do pole **název**zadejte popisný název **ContosoVMVault**. Pokud máte více předplatných, vyberte příslušné předplatné.
4. Vytvořte skupinu prostředků **ContosoRG**.
5. Zadejte oblast Azure. Pokud chcete zjistit podporované oblasti, přečtěte si téma [Podrobnosti o cenách Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
6. Pro Recovery Services trezory vyberte **Přehled**  >  **ConsotoVMVault**  >  **+ replikovat**.
7. Jako **zdroj**vyberte **Azure**.
8. V poli **zdrojové umístění**vyberte zdrojovou oblast Azure, ve které jsou aktuálně spuštěné vaše virtuální počítače.
9. Vyberte model nasazení Azure Resource Manager. Pak vyberte **zdrojové předplatné** a **skupinu prostředků zdroje**.
10. Kliknutím na **OK** uložte nastavení.

### <a name="enable-replication-for-azure-vms-and-start-copying-the-data"></a>Povolení replikace pro virtuální počítače Azure a zahájení kopírování dat

Site Recovery načte seznam virtuálních počítačů, které jsou přidružené k předplatnému a skupině prostředků.

1. Vyberte virtuální počítač, který chcete přesunout, a pak vyberte **OK**.
2. V **Nastavení**vyberte **zotavení po havárii**.
3. V části konfigurovat cílovou oblast **zotavení po havárii**  >  **Target region**vyberte cílovou oblast, do které se do ní budete replikovat.
4. Vyberte, jestli se mají používat výchozí cílové prostředky nebo ty, které jste vytvořili předem.
5. Chcete-li spustit úlohu, vyberte možnost **Povolit replikaci** .

   ![Povolení replikace](media/tutorial-migrate-azure-to-azure/settings.png)

 

## <a name="test-the-configuration"></a>Otestování konfigurace


1. Přejít do trezoru. V **Nastavení**  >  **replikované položky**vyberte virtuální počítač, který chcete přesunout do cílové oblasti. Pak vyberte **Test převzetí služeb při selhání**.
2. V části **testovací převzetí služeb při selhání**vyberte bod obnovení, který chcete použít pro převzetí služeb při selhání:

   - **Nejnovější zpracovaný:** Vrátí virtuální počítač k nejnovějšímu bodu obnovení zpracovanému službou Site Recovery. Zobrazí se časové razítko. Žádná doba nestrávila zpracováním dat, takže tato možnost poskytuje cíl s nízkou dobou obnovení (RTO).
   - **Nejnovější konzistentní vzhledem k aplikacím**: převzetí služeb při selhání všemi virtuálními počítači na nejnovější bod obnovení konzistentní vzhledem k aplikacím. Zobrazí se časové razítko.
   - **Vlastní**: Vyberete si libovolný bod obnovení.

3. Vyberte cílovou virtuální síť Azure, do které chcete virtuální počítače Azure přesunout a otestovat konfiguraci.

   > [!IMPORTANT]
   > Doporučujeme použít samostatnou síť virtuálních počítačů Azure pro testovací převzetí služeb při selhání, nikoli produkční síť v cílové oblasti.

4. Chcete-li zahájit testování přesunu, vyberte **OK**. Pokud chcete sledovat průběh, vyberte virtuální počítač a otevřete jeho **Vlastnosti.** Případně vyberte úlohu **testovací převzetí služeb při selhání** v trezoru. Pak vyberte **Nastavení**  >  **úlohy**  >  **Site Recovery úlohy**.
5. Po dokončení převzetí služeb při selhání se na portálu Azure Portal v části **Virtuální počítače** objeví replika virtuálního počítače Azure. Ujistěte se, že je virtuální počítač spuštěný, má odpovídající velikost a je připojený k odpovídající síti.
6. Pokud chcete odstranit virtuální počítač, který jste vytvořili pro testování, vyberte možnost **vyčistit testovací převzetí služeb při selhání** u replikované položky. Z **poznámek**, záznam a uložení jakýchkoli pozorování souvisejících s testem.

## <a name="perform-the-move-and-confirm"></a>Provedení přesunutí a potvrzení

1. V **Nastavení**  >  **replikované položky**v části nastavení úložiště vyberte virtuální počítač a pak vyberte **převzetí služeb při selhání**.
1. V případě **převzetí služeb při selhání**vyberte **nejnovější**. 
2. Vyberte **Před spuštěním převzetí služeb při selhání vypnout počítač**. Site Recovery se před aktivací převzetí služeb při selhání pokusí vypnout zdrojový virtuální počítač. Ale převzetí služeb při selhání pokračuje i v případě selhání vypnutí. Průběh převzetí služeb při selhání můžete sledovat na stránce **úlohy** .
3. Až se úloha dokončí, ověřte, že se virtuální počítač zobrazuje v cílové oblasti Azure podle očekávání.
4. V části **replikované položky**klikněte pravým tlačítkem na virtuální počítač a vyberte **Potvrdit**. Tím se přesun dokončí. Počkejte, než se dokončí úloha potvrzení změn.

## <a name="discard-the-resources-from-the-source-region"></a>Zahodí prostředky ze zdrojové oblasti.

- Přejít na virtuální počítač a vybrat **Zakázat replikaci**. Tím se zastaví proces kopírování dat pro virtuální počítač.

  > [!IMPORTANT]
  > Dokončete tento krok, abyste se vyhnuli účtování Site Recovery replikace po přesunutí.

Pokud neplánujete znovu použít žádný ze zdrojových prostředků, postupujte podle následujících kroků:

1. Odstraňte všechny relevantní síťové prostředky ve zdrojové oblasti, které jsou uvedené v kroku 4 [Příprava zdrojových virtuálních počítačů](#prepare-the-source-vms).
2. Odstraňte odpovídající účet úložiště ve zdrojové oblasti.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak přesunout virtuální počítače Azure do jiné oblasti Azure. Pro tyto virtuální počítače teď můžete nakonfigurovat zotavení po havárii.

> [!div class="nextstepaction"]
> [Nastavení zotavení po havárii po migraci](azure-to-azure-quickstart.md)
