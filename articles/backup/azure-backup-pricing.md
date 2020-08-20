---
title: Ceny služby Azure Backup
description: Naučte se odhadnout náklady na rozpočtování Azure Backup cen.
ms.topic: conceptual
ms.date: 06/16/2020
ms.openlocfilehash: 03ec0076d3089562ddaace6db413fb3f1ba949a6
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2020
ms.locfileid: "88654527"
---
# <a name="azure-backup-pricing"></a>Ceny služby Azure Backup

Další informace o cenách Azure Backup najdete na [stránce Azure Backup ceny](https://azure.microsoft.com/pricing/details/backup/).

## <a name="download-detailed-estimates-for-azure-backup-pricing"></a>Stažení podrobných odhadů pro Azure Backup ceny

Pokud chcete odhadnout náklady na rozpočtování a porovnání nákladů, Stáhněte si podrobné [Azure Backup cenové Estimator](https://aka.ms/AzureBackupCostEstimates).  

### <a name="what-does-the-estimator-contain"></a>Co Estimator obsahuje?

List Azure Backup cost Estimator má možnost odhadnout všechny možné úlohy, které se snažíte zálohovat pomocí Azure Backup. Mezi tyto úlohy patří:

- Virtuální počítače Azure
- Místní servery
- SQL ve virtuálních počítačích Azure
- SAP HANA ve virtuálních počítačích Azure
- Sdílené složky Azure Files

## <a name="estimate-costs-for-backing-up-azure-vms-or-on-premises-servers"></a>Odhad nákladů na zálohování virtuálních počítačů Azure nebo místních serverů

Pokud chcete odhadnout náklady na zálohování virtuálních počítačů Azure nebo místních serverů pomocí Azure Backup, budete potřebovat následující parametry:

- Velikost virtuálních počítačů nebo místních serverů, které se pokoušíte zálohovat
  - Zadejte "použitou velikost" disků nebo serverů potřebných k zálohování.

- Počet serverů s touto velikostí

- Jaké jsou očekávané množství změn dat na těchto serverech?<br>
  Změny odkazují na množství změn v datech. Pokud byste například měli virtuální počítač s 200 GB dat, která se mají zálohovat, a 10 GB změn se každý den změní, denní změna je 5%.

  - Vyššími změnami bude znamenat, že zálohujete další data.

  - Zvolte **Nízká** nebo **střední** pro souborové servery a **vysokou** , pokud používáte databáze.

  - Pokud víte, že vaše změny jsou v **procentech**, můžete použít možnost **zadat vlastní%** .

- Zvolit zásadu zálohování

  - Jak dlouho očekáváte uchovávání každodenních záloh? (ve dnech)

  - Jak dlouho očekáváte, že se mají uchovat zálohy "týdně"? (v týdnech)

  - Jak dlouho očekáváte zachování "měsíčních" záloh? (v měsících)

  - Jak dlouho očekáváte, že se mají uchovávat roční zálohy? (v letech)

  - Jak dlouho očekáváte, že se mají zachovat snímky pro okamžité obnovení? (1-5 dní)

    - Tato možnost umožňuje rychlé obnovení z až sedmi dnů pomocí snímků uložených na discích.

- **Volitelné** – selektivní zálohování disku

  - Pokud při zálohování virtuálních počítačů Azure používáte možnost **zálohování na disk** , vyberte možnost **vyloučit disk** a zadejte procento disků vyloučených ze zálohy s ohledem na velikost. Například pokud máte virtuální počítač připojený ke třem diskům s 200 GB, který se používá na každém disku, a pokud chcete vyloučit dva z nich z zálohování, zadejte 66,7%.

- **Volitelné** – redundance záložního úložiště

  - To indikuje redundanci účtu úložiště, do kterého se data zálohují. K nejvyšší dostupnosti doporučujeme používat **GRS** . Vzhledem k tomu, že je kopie zálohovaných dat uchována v jiné oblasti, pomáhá splnit několik standardů dodržování předpisů. Pokud zálohujete vývojové nebo testovací prostředí, která nepotřebují zálohování na podnikové úrovni, změňte redundanci na **LRS** . V listu vyberte možnost **RAGRS** , pokud chcete pochopit náklady v případě, že je pro vaše zálohování povoleno [obnovení mezi oblastmi](backup-azure-arm-restore-vms.md#cross-region-restore) .

- **Volitelné** – změňte regionální ceny nebo použijte zvýhodněné sazby.

  - Pokud chcete kontrolovat odhady pro jinou oblast nebo zlevněné sazby, vyberte u **odhadů vyzkoušet pro jinou oblast** možnost **Ano** a zadejte sazby, u kterých chcete spustit odhady.

## <a name="estimate-costs-for-backing-up-sql-servers-in-azure-vms"></a>Odhad nákladů na zálohování SQL serverů ve virtuálních počítačích Azure

Pokud chcete odhadnout náklady na zálohování SQL serverů běžících na virtuálních počítačích Azure pomocí Azure Backup, budete potřebovat následující parametry:

- Velikost serverů SQL, na které se pokoušíte zálohovat

- Počet serverů SQL s výše uvedenou velikostí

- Jaká je očekávaná komprese zálohovaných dat SQL serveru?

  - Většina Azure Backup zákazníkům uvidí, že data zálohy mají v porovnání s velikostí SQL serveru v době, kdy je **povolená**komprese SQL, kompresi 80%.

  - Pokud očekáváte, že se zobrazí jiná komprese, zadejte číslo do tohoto pole.

- Jaká je očekávaná velikost záloh protokolu?

  - % Označuje denní velikost protokolu jako% velikosti SQL serveru.

- Jaké je očekávané množství každodenních změn dat na těchto serverech?

  - Databáze obvykle mají "vysokou" četnost změn.

  - Pokud víte, že vaše změny jsou v **procentech**, můžete použít možnost **zadat vlastní%** .

- Zvolit zásadu zálohování

  - Typ zálohování

    - Nejúčinnější zásady, které si můžete vybrat, jsou **denní rozdíly** s týdenním/měsíčním/ročním úplným zálohováním. Azure Backup lze obnovit z rozdílů také pomocí jediného kliknutí.

    - Můžete se také rozhodnout, že budete mít k dispozici zásadu s úplnými zálohami denně, týdně nebo měsíčně. Tato možnost spotřebuje mírně větší úložiště než první možnost.

  - Jak dlouho očekáváte uchovávání záloh protokolu? (ve dnech) [7-35]

  - Jak dlouho očekáváte uchovávání každodenních záloh? (ve dnech)

  - Jak dlouho očekáváte, že se mají uchovat zálohy "týdně"? (v týdnech)

  - Jak dlouho očekáváte zachování "měsíčních" záloh? (v měsících)

  - Jak dlouho očekáváte, že se mají uchovávat roční zálohy? (v letech)

- **Volitelné** – redundance záložního úložiště

  - To indikuje redundanci účtu úložiště, do kterého se data zálohují. K nejvyšší dostupnosti doporučujeme používat **GRS** . Vzhledem k tomu, že je kopie zálohovaných dat uchována v jiné oblasti, pomáhá splnit několik standardů dodržování předpisů. Pokud zálohujete vývojové nebo testovací prostředí, která nepotřebují zálohování na podnikové úrovni, změňte redundanci na **LRS** .

- **Volitelné** – změňte regionální ceny nebo použijte zvýhodněné sazby.

  - Pokud chcete kontrolovat odhady pro jinou oblast nebo zlevněné sazby, vyberte u **odhadů vyzkoušet pro jinou oblast** možnost **Ano** a zadejte sazby, u kterých chcete spustit odhady.

## <a name="estimate-costs-for-backing-up-sap-hana-servers-in-azure-vms"></a>Odhad nákladů na zálohování SAP HANA serverů ve virtuálních počítačích Azure

Pokud chcete odhadnout náklady na zálohování SAP HANA serverů běžících na virtuálních počítačích Azure pomocí Azure Backup, budete potřebovat následující parametry:

- Celková velikost SAP HANAch databází, které se pokoušíte zálohovat Mělo by se jednat o součet velikosti úplných záloh každé databáze, jak je uvedeno SAP HANA.
- Počet SAP HANA serverů s výše uvedenou velikostí
- Jaká je očekávaná velikost záloh protokolu?
  
  - % Udává průměrnou velikost denního protokolu jako% celkové velikosti SAP HANA databází, které zálohujete na SAP HANA serveru.
- Jaké je očekávané množství každodenních změn dat na těchto serverech?
  - % Indikuje průměrnou velikost denních změn jako% celkové velikosti SAP HANA databází, které zálohujete na SAP HANA serveru.
  - Databáze obvykle mají "vysokou" četnost změn.
  - Pokud víte, že vaše změny jsou v **procentech**, můžete použít možnost **zadat vlastní%** .
- Zvolit zásadu zálohování
  - Typ zálohování
    - Nejúčinnější zásady, které si můžete vybrat, jsou **denní rozdíly** s **týdenním/měsíčním/ročním** úplným zálohováním. Azure Backup lze obnovit z rozdílů také pomocí jediného kliknutí.
    - Můžete se také rozhodnout, že budete mít k dispozici zásadu s úplnými zálohami **denně, týdně nebo měsíčně** . Tato možnost spotřebuje mírně větší úložiště než první možnost.
  - Jak dlouho očekáváte uchovávání záloh protokolu? (ve dnech) [7-35]
  - Jak dlouho očekáváte uchovávání každodenních záloh? (ve dnech)
  - Jak dlouho očekáváte, že se mají uchovat zálohy "týdně"? (v týdnech)
  - Jak dlouho očekáváte zachování "měsíčních" záloh? (v měsících)
  - Jak dlouho očekáváte, že se mají uchovávat roční zálohy? (v letech)
- **Volitelné** – redundance záložního úložiště
  
  - To indikuje redundanci účtu úložiště, do kterého se data zálohují. K nejvyšší dostupnosti doporučujeme používat **GRS** . Vzhledem k tomu, že je kopie zálohovaných dat uchována v jiné oblasti, pomáhá splnit několik standardů dodržování předpisů. Pokud zálohujete vývojové nebo testovací prostředí, která nepotřebují zálohování na podnikové úrovni, změňte redundanci na **LRS** .
- **Volitelné** – změňte regionální ceny nebo použijte zvýhodněné sazby.
  
  - Pokud chcete kontrolovat odhady pro jinou oblast nebo zlevněné sazby, vyberte u **odhadů vyzkoušet pro jinou oblast** možnost **Ano** a zadejte sazby, u kterých chcete spustit odhady.
  
## <a name="estimate-costs-for-backing-up-azure-file-shares"></a>Odhadované náklady na zálohování sdílených složek Azure

Pokud chcete odhadnout náklady na zálohování sdílených složek Azure pomocí [řešení zálohování založeného na snímku](azure-file-share-backup-overview.md) , které nabízí Azure Backup, budete potřebovat následující parametry:

- Velikost (**v GB**) sdílených složek souborů, které chcete zálohovat.

- Pokud chcete zálohovat sdílené složky mezi několika účty úložiště, určete počet účtů úložiště, které hostují sdílené složky s výše uvedenou velikostí.

- U sdílených složek, které chcete zálohovat, se očekávalo množství změn dat. <br>Změny odkazují na množství změn v datech a přímo ovlivní velikost úložiště snímků. Například pokud máte sdílenou složku s 200 GB dat, která se mají zálohovat, a 10 GB změn se každý den změní, denní změna je 5%.
  - Vyšší změna znamená, že množství změn dat v obsahu sdílené složky každý den je vysoké, takže přírůstkový snímek (zachytávání pouze změn dat) by měl být také větší.
  - Vyberte nízká (1%), střední (3%) nebo vysoká (5%) podle vlastností a využití sdílené složky.
  - Pokud **znáte přesnou** variantu pro sdílenou složku, můžete v rozevíracím seznamu vybrat možnost **zadat vlastní%** . Zadejte hodnoty (v%) pro denní, týdenní, měsíční a roční změny.

- Zadejte účet úložiště (Standard nebo Premium) a nastavení redundance úložiště účtu úložiště hostujícího zálohovanou sdílenou složku. <br>V aktuálním řešení zálohování pro sdílené složky Azure se snímky ukládají do stejného účtu úložiště jako zálohovaná sdílená složka. Takže náklady na úložiště spojené s snímky se účtují jako součást služby soubory Azure, a to na základě cen snímků pro typ účtu a nastavení redundance účtu úložiště hostujícího zálohovanou sdílenou složku a snímky.

- Uchovávání pro různé zálohy
  - Jak dlouho očekáváte uchovávání každodenních záloh? (ve dnech)
  - Jak dlouho očekáváte, že se mají uchovat zálohy "týdně"? (v týdnech)
  - Jak dlouho očekáváte zachování "měsíčních" záloh? (v měsících)
  - Jak dlouho očekáváte, že se mají uchovávat roční zálohy? (v letech)

  Maximální podporované hodnoty uchování v každé kategorii najdete v [matici podpory Azure File Share support](azure-file-share-support-matrix.md#retention-limits) .

- **Volitelné** – změňte regionální ceny nebo použijte zvýhodněné sazby.
  - Výchozí hodnoty nastavené pro náklady na úložiště snímků za GB a náklady na chráněnou instanci ve Estimator jsou pro oblast Východní USA. Pokud chcete kontrolovat své odhady pro jinou oblast nebo zlevněné sazby, vyberte u možnosti **vyzkoušet odhady pro jinou oblast?** možnost **Ano** a zadejte sazby, u kterých chcete odhady spustit.

## <a name="next-steps"></a>Další kroky

[Co je služba Azure Backup?](backup-overview.md)
