---
title: Ceny služby Azure Backup
description: Zjistěte, jak odhadnout náklady na službu Azure Backup, abyste mohli naplánovat rozpočet.
ms.topic: conceptual
ms.date: 06/16/2020
ms.openlocfilehash: 03ec0076d3089562ddaace6db413fb3f1ba949a6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "88654527"
---
# <a name="azure-backup-pricing"></a>Ceny služby Azure Backup

Další informace o cenách služby Azure Backup najdete na [stránce s cenami služby Azure Backup](https://azure.microsoft.com/pricing/details/backup/).

## <a name="download-detailed-estimates-for-azure-backup-pricing"></a>Stažení podrobných odhadů cen služby Azure Backup

Pokud chcete odhadnout náklady pro účely plánování rozpočtu nebo porovnání nákladů, stáhněte si podrobný [kalkulátor cen služby Azure Backup](https://aka.ms/AzureBackupCostEstimates).  

### <a name="what-does-the-estimator-contain"></a>Co kalkulátor obsahuje?

Tabulka kalkulátoru nákladů na službu Azure Backup nabízí možnosti pro odhad nákladů na všechny možné úlohy, které můžete chtít zálohovat pomocí služby Azure Backup. Mezi tyto úlohy patří:

- Virtuální počítače Azure
- Místní servery
- SQL na virtuálních počítačích Azure
- SAP HANA na virtuálních počítačích Azure
- Sdílené složky Azure

## <a name="estimate-costs-for-backing-up-azure-vms-or-on-premises-servers"></a>Odhad nákladů na zálohování virtuálních počítačů Azure nebo místních serverů

Pokud chcete získat odhad nákladů na zálohování virtuálních počítačů Azure nebo místních serverů pomocí služby Azure Backup, budete potřebovat následující parametry:

- Velikost virtuálních počítačů nebo místních serverů, které se pokoušíte zálohovat
  - Zadejte využitou velikost disků nebo serverů, které je potřeba zálohovat.

- Počet serverů s touto velikostí

- Jaká je očekávaná četnost změn dat na těchto serverech?<br>
  Četnost změn odkazuje na množství změn dat. Například pokud máte virtuální počítač s 200 GB dat, která se mají zálohovat, a každý den se změní 10 GB těchto dat, denní četnost změn je 5 %.

  - Vyšší četnost změn znamená, že zálohujete více dat.

  - Pro souborové servery vyberte **nízkou** nebo **střední** četnost změn, a pokud používáte databáze, vyberte **vysokou** četnost změn.

  - Pokud znáte vaši **procentní četnost změn**, můžete použít možnost **Zadat vlastní procentní hodnotu**.

- Zvolte zásady zálohování.

  - Jak dlouho očekáváte, že se budou uchovávat denní zálohy? (ve dnech)

  - Jak dlouho očekáváte, že se budou uchovávat týdenní zálohy? (v týdnech)

  - Jak dlouho očekáváte, že se budou uchovávat měsíční zálohy? (v měsících)

  - Jak dlouho očekáváte, že se budou uchovávat roční zálohy? (v letech)

  - Jak dlouho očekáváte, že se budou uchovávat snímky pro okamžité obnovení? (1–5 dnů)

    - Tato možnost umožňuje rychle provést obnovení pomocí snímků uložených na discích, a to až sedm dnů nazpět.

- **Volitelné** – Selektivní zálohování disků

  - Pokud při zálohování virtuálních počítačů Azure využíváte možnost **Selektivní zálohování disků**, zvolte možnost **Vyloučit disk** a zadejte procento disků vyloučených ze zálohování z hlediska velikosti. Například pokud máte virtuální počítač připojený ke třem diskům s využitou velikostí 200 GB a chcete dva z nich vyloučit ze zálohování, zadejte 66,7 %.

- **Volitelné** – Redundance úložiště zálohování

  - Tento parametr značí redundanci účtu úložiště, do kterého se zálohují vaše data. Pokud chcete zajistit nejvyšší dostupnost, doporučujeme použít **GRS**. Tento typ redundance zajišťuje uchovávání kopie zálohovaných dat v jiné oblasti, a tím pomáhá plnit různé standardy dodržování předpisů. Pokud zálohujete vývojová nebo testovací prostředí, která nevyžadují zálohování na podnikové úrovni, změňte redundanci na **LRS**. Pokud chcete porozumět nákladům při povolení [obnovení záloh mezi oblastmi](backup-azure-arm-restore-vms.md#cross-region-restore), vyberte v tabulce možnost **RA-GRS**.

- **Volitelné** – Úprava regionálních cen nebo používání zvýhodněných sazeb

  - Pokud si chcete projít odhady pro jinou oblast nebo zvýhodněné sazby, u možnosti **Vyzkoušet odhady pro jinou oblast?** vyberte **Ano** a zadejte sazby, pro které chcete získat odhady.

## <a name="estimate-costs-for-backing-up-sql-servers-in-azure-vms"></a>Odhad nákladů na zálohování SQL Serverů na virtuálních počítačích Azure

Pokud chcete získat odhad nákladů na zálohování SQL Serverů na virtuálních počítačích Azure pomocí služby Azure Backup, budete potřebovat následující parametry:

- Velikost SQL Serverů, které se pokoušíte zálohovat

- Počet SQL Serverů s výše uvedenou velikostí

- Jaká je očekávaná komprese zálohovaných dat vašich SQL Serverů?

  - Většina zákazníků se službou Azure Backup při **povolení** komprese SQL zaznamená 80% kompresi zálohovaných dat v porovnání s původní velikostí SQL Serveru.

  - Pokud očekáváte jinou úroveň komprese, zadejte do tohoto pole příslušné číslo.

- Jaká je očekávaná velikost záloh protokolů?

  - Tato procentní hodnota značí denní velikost protokolů jako procento velikosti SQL Serveru.

- Jaká je očekávaná denní četnost změn dat na těchto serverech?

  - Databáze obvykle mají vysokou četnost změn.

  - Pokud znáte vaši **procentní četnost změn**, můžete použít možnost **Zadat vlastní procentní hodnotu**.

- Zvolte zásady zálohování.

  - Typ zálohování

    - Nejefektivnější zásady, které můžete vybrat, jsou **Denní rozdílové zálohování** s týdenním, měsíčním nebo ročním úplným zálohováním. Azure Backup umožňuje obnovení jedním kliknutím i z rozdílových záloh.

    - Můžete zvolit také zásady s denním, týdenním, měsíčním nebo ročním úplným zálohováním. Tato možnost bude využívat o něco více úložiště než první možnost.

  - Jak dlouho očekáváte, že se budou uchovávat zálohy protokolů? (ve dnech) [7–35]

  - Jak dlouho očekáváte, že se budou uchovávat denní zálohy? (ve dnech)

  - Jak dlouho očekáváte, že se budou uchovávat týdenní zálohy? (v týdnech)

  - Jak dlouho očekáváte, že se budou uchovávat měsíční zálohy? (v měsících)

  - Jak dlouho očekáváte, že se budou uchovávat roční zálohy? (v letech)

- **Volitelné** – Redundance úložiště zálohování

  - Tento parametr značí redundanci účtu úložiště, do kterého se zálohují vaše data. Pokud chcete zajistit nejvyšší dostupnost, doporučujeme použít **GRS**. Tento typ redundance zajišťuje uchovávání kopie zálohovaných dat v jiné oblasti, a tím pomáhá plnit různé standardy dodržování předpisů. Pokud zálohujete vývojová nebo testovací prostředí, která nevyžadují zálohování na podnikové úrovni, změňte redundanci na **LRS**.

- **Volitelné** – Úprava regionálních cen nebo používání zvýhodněných sazeb

  - Pokud si chcete projít odhady pro jinou oblast nebo zvýhodněné sazby, u možnosti **Vyzkoušet odhady pro jinou oblast?** vyberte **Ano** a zadejte sazby, pro které chcete získat odhady.

## <a name="estimate-costs-for-backing-up-sap-hana-servers-in-azure-vms"></a>Odhad nákladů na zálohování serverů SAP HANA na virtuálních počítačích Azure

Pokud chcete získat odhad nákladů na zálohování serverů SAP HANA na virtuálních počítačích Azure pomocí služby Azure Backup, budete potřebovat následující parametry:

- Celková velikost databází SAP HANA, které se pokoušíte zálohovat. Mělo by se jednat o součet velikostí úplných záloh všech databází, jak uvádí SAP HANA.
- Počet serverů SAP HANA s výše uvedenou velikostí
- Jaká je očekávaná velikost záloh protokolů?
  
  - Tato procentní hodnota značí průměrnou denní velikost protokolů jako procento celkové velikosti databází SAP HANA, které zálohujete na serveru SAP HANA.
- Jaká je očekávaná denní četnost změn dat na těchto serverech?
  - Tato procentní hodnota značí průměrnou denní četnost změn jako procento celkové velikosti databází SAP HANA, které zálohujete na serveru SAP HANA.
  - Databáze obvykle mají vysokou četnost změn.
  - Pokud znáte vaši **procentní četnost změn**, můžete použít možnost **Zadat vlastní procentní hodnotu**.
- Zvolte zásady zálohování.
  - Typ zálohování
    - Nejefektivnější zásady, které můžete vybrat, jsou **Denní rozdílové zálohování** s **týdenním, měsíčním nebo ročním** úplným zálohováním. Azure Backup umožňuje obnovení jedním kliknutím i z rozdílových záloh.
    - Můžete zvolit také zásady s **denním, týdenním, měsíčním nebo ročním** úplným zálohováním. Tato možnost bude využívat o něco více úložiště než první možnost.
  - Jak dlouho očekáváte, že se budou uchovávat zálohy protokolů? (ve dnech) [7–35]
  - Jak dlouho očekáváte, že se budou uchovávat denní zálohy? (ve dnech)
  - Jak dlouho očekáváte, že se budou uchovávat týdenní zálohy? (v týdnech)
  - Jak dlouho očekáváte, že se budou uchovávat měsíční zálohy? (v měsících)
  - Jak dlouho očekáváte, že se budou uchovávat roční zálohy? (v letech)
- **Volitelné** – Redundance úložiště zálohování
  
  - Tento parametr značí redundanci účtu úložiště, do kterého se zálohují vaše data. Pokud chcete zajistit nejvyšší dostupnost, doporučujeme použít **GRS**. Tento typ redundance zajišťuje uchovávání kopie zálohovaných dat v jiné oblasti, a tím pomáhá plnit různé standardy dodržování předpisů. Pokud zálohujete vývojová nebo testovací prostředí, která nevyžadují zálohování na podnikové úrovni, změňte redundanci na **LRS**.
- **Volitelné** – Úprava regionálních cen nebo používání zvýhodněných sazeb
  
  - Pokud si chcete projít odhady pro jinou oblast nebo zvýhodněné sazby, u možnosti **Vyzkoušet odhady pro jinou oblast?** vyberte **Ano** a zadejte sazby, pro které chcete získat odhady.
  
## <a name="estimate-costs-for-backing-up-azure-file-shares"></a>Odhad nákladů na zálohování sdílených složek Azure

Pokud chcete získat odhad nákladů na zálohování sdílených složek Azure pomocí [řešení zálohování založeného na snímcích](azure-file-share-backup-overview.md), které nabízí Azure Backup, budete potřebovat následující parametry:

- Velikost (**v GB**) sdílených složek, které chcete zálohovat.

- Pokud chcete zálohovat sdílené složky rozprostřené mezi několika účty úložiště, zadejte počet účtů úložiště, které jsou hostiteli sdílených složek s výše uvedenou velikostí.

- Očekávaná četnost změn dat ve sdílených složkách, které chcete zálohovat <br>Četnost změn odkazuje na množství změn dat a přímo ovlivňuje velikost úložiště snímků. Například pokud máte sdílenou složku s 200 GB dat, která se mají zálohovat, a každý den se změní 10 GB těchto dat, denní četnost změn je 5 %.
  - Vyšší četnost změn znamená velké množství denních změn dat v obsahu sdílených složek, což způsobuje také větší velikost přírůstkových snímků (zachytávajících pouze změny dat).
  - V závislosti na charakteristice a využití vašich sdílených složek vyberte nízkou (1 %), střední (3 %) nebo vysokou (5 %) četnost změn.
  - Pokud znáte přesnou **procentní četnost změn** ve vaší sdílené složce, můžete v rozevírací nabídce vybrat možnost **Zadat vlastní procentní hodnotu**. Zadejte hodnoty (v procentech) denní, týdenní, měsíční a roční četnosti změn.

- Typ účtu úložiště (Standard nebo Premium) a nastavení redundance úložiště účtu úložiště, který je hostitelem zálohované sdílené složky <br>V aktuálním řešení zálohování sdílených složek Azure se snímky ukládají ve stejném účtu úložiště jako zálohovaná sdílená složka. Proto se náklady na úložiště spojené se snímky účtují v rámci faktury za Azure Files, a to v závislosti na cenách snímků pro typ účtu a nastavení redundance účtu úložiště, který je hostitelem zálohovaných sdílených složek a snímků.

- Uchovávání různých záloh
  - Jak dlouho očekáváte, že se budou uchovávat denní zálohy? (ve dnech)
  - Jak dlouho očekáváte, že se budou uchovávat týdenní zálohy? (v týdnech)
  - Jak dlouho očekáváte, že se budou uchovávat měsíční zálohy? (v měsících)
  - Jak dlouho očekáváte, že se budou uchovávat roční zálohy? (v letech)

  Maximální hodnoty doby uchovávání v jednotlivých kategoriích najdete v [matici podpory pro sdílené složky Azure](azure-file-share-support-matrix.md#retention-limits).

- **Volitelné** – Úprava regionálních cen nebo používání zvýhodněných sazeb
  - Výchozí hodnoty nastavené pro náklady na úložiště snímků za GB a náklady na chráněné instance v kalkulátoru se vztahují na oblast USA – východ. Pokud si chcete projít odhady pro jinou oblast nebo zvýhodněné sazby, u možnosti **Vyzkoušet odhady pro jinou oblast?** vyberte **Ano** a zadejte sazby, pro které chcete získat odhady.

## <a name="next-steps"></a>Další kroky

[Co je služba Azure Backup?](backup-overview.md)
