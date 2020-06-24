---
title: Ceny služby Azure Backup
description: Naučte se odhadnout náklady na rozpočtování Azure Backup cen.
ms.topic: conceptual
ms.date: 06/16/2020
ms.openlocfilehash: d88587cfdbb4f60d0da8641fc0362b8f763779ad
ms.sourcegitcommit: 34eb5e4d303800d3b31b00b361523ccd9eeff0ab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/17/2020
ms.locfileid: "84908083"
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
  Změny odkazují na množství změn v datech. Pokud byste například měli virtuální počítač s 200 GB dat, která se mají zálohovat, a pokud se každý den změní 10 GB, denní změna je 5%.

  - Vyššími změnami bude znamenat, že zálohujete další data.

  - Zvolte **Nízká** nebo **střední** pro souborové servery a **vysokou** , pokud používáte databáze.

  - Pokud víte, že vaše změny jsou v **procentech**, můžete použít možnost **zadat vlastní%** .

- Zvolit zásadu zálohování

  - Jak dlouho očekáváte uchovávání každodenních záloh? (ve dnech)

  - Jak dlouho očekáváte, že se mají uchovat zálohy "týdně"? (v týdnech)

  - Jak dlouho očekáváte zachování "měsíčních" záloh? (v měsících)

  - Jak dlouho očekáváte, že se mají uchovávat roční zálohy? (v letech)

  - Jak dlouho očekáváte, že se mají zachovat snímky pro okamžité obnovení? (1-7 dní)

    - Tato možnost umožňuje rychlé obnovení z hlediska až sedmi dnů pomocí snímků uložených na discích.

- **Volitelné** – selektivní zálohování disku

  - Pokud při zálohování virtuálních počítačů Azure používáte možnost **zálohování na disk** , vyberte možnost **vyloučit disk** a zadejte procento disků vyloučených ze zálohy s ohledem na velikost. Například pokud máte virtuální počítač připojený ke třem diskům s 200 GB, který se používá na každém disku, a pokud chcete vyloučit dva z nich z zálohování, zadejte 66,7%.

- **Volitelné** – redundance záložního úložiště

  - To indikuje redundanci účtu úložiště, do kterého se data zálohují. K nejvyšší dostupnosti doporučujeme používat **GRS** . Vzhledem k tomu, že je kopie zálohovaných dat uchována v jiné oblasti, pomáhá splnit několik standardů dodržování předpisů. Pokud zálohujete vývojové nebo testovací prostředí, která nepotřebují zálohování na podnikové úrovni, změňte redundanci na **LRS** . Pokud chcete povolit **obnovení mezi oblastmi** pro vaše zálohy, vyberte **RAGRS** .

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

  - Jak dlouho očekáváte uchovávání záloh protokolu? (ve dnech) [1-35]

  - Jak dlouho očekáváte uchovávání každodenních záloh? (ve dnech)

  - Jak dlouho očekáváte, že se mají uchovat zálohy "týdně"? (v týdnech)

  - Jak dlouho očekáváte zachování "měsíčních" záloh? (v měsících)

  - Jak dlouho očekáváte, že se mají uchovávat roční zálohy? (v letech)

- **Volitelné** – redundance záložního úložiště

  - To indikuje redundanci účtu úložiště, do kterého se data zálohují. K nejvyšší dostupnosti doporučujeme používat **GRS** . Vzhledem k tomu, že je kopie zálohovaných dat uchována v jiné oblasti, pomáhá splnit několik standardů dodržování předpisů. Pokud zálohujete vývojové nebo testovací prostředí, která nepotřebují zálohování na podnikové úrovni, změňte redundanci na **LRS** .

- **Volitelné** – změňte regionální ceny nebo použijte zvýhodněné sazby.

  - Pokud chcete kontrolovat odhady pro jinou oblast nebo zlevněné sazby, vyberte u **odhadů vyzkoušet pro jinou oblast** možnost **Ano** a zadejte sazby, u kterých chcete spustit odhady.

## <a name="estimate-costs-for-backing-up-sap-hana-servers-in-azure-vms"></a>Odhad nákladů na zálohování SAP HANA serverů ve virtuálních počítačích Azure

Odhad nákladů na záložní SAP HANA servery na virtuálních počítačích Azure je jako odhad pro SQL servery. Kromě komprese SQL můžete použít stejné proměnné, jak je uvedeno v předchozí části.

## <a name="next-steps"></a>Další kroky

[Co je služba Azure Backup?](backup-overview.md)
