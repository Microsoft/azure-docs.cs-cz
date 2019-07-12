---
title: Automatizujte migraci velkého počtu virtuálních počítačů do Azure | Dokumentace Microsoftu
description: Popisuje, jak pomocí skriptů můžete migrovat velký počet virtuálních počítačů pomocí Azure Site Recovery
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: article
ms.date: 04/01/2019
ms.author: snehaa
ms.openlocfilehash: b45a158569b3be8250728293c1bf73c1a860a0f6
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67808028"
---
# <a name="scale-migration-of-vms-using-azure-site-recovery"></a>Migrace škálovací sady virtuálních počítačů pomocí Azure Site Recovery

Tento článek pomáhá pochopit, jak pomocí skriptů můžete migrovat velký počet virtuálních počítačů pomocí Azure Site Recovery. Tyto skripty jsou k dispozici pro stahování na [ukázky Azure Powershellu](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/migrate-at-scale-with-site-recovery) úložišti na Githubu. Skripty lze použít k migraci VMware, AWS, GCP virtuálních počítačů a fyzických serverů do managed disks v Azure. Tyto skripty můžete také použít k migraci virtuálních počítačů Hyper-V, pokud provádíte migraci virtuálních počítačů jako s fyzickými servery. Skripty, které využívají Powershellu pro Azure Site Recovery jsou popsány [tady](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell).

## <a name="current-limitations"></a>Aktuální omezení:
- Nepodporuje určení statickou IP adresu jenom pro primární síťové rozhraní cílového virtuálního počítače
- Skripty nepřebírají související s programem Azure Hybrid Benefit vstupů, je potřeba ručně aktualizovat vlastnosti replikovaný virtuální počítač na portálu

## <a name="how-does-it-work"></a>Jak to funguje?

### <a name="prerequisites"></a>Požadavky
Než začnete, budete muset provést následující kroky:
- Ujistěte se, že trezor Site Recovery se vytvoří ve vašem předplatném Azure
- Ujistěte se, že konfigurační Server a procesový Server jsou nainstalovány ve zdrojovém prostředí a trezor je schopná vyhledávat prostředí
- Ujistěte se, že zásady replikace je vytvořena a přidružena konfiguračního serveru
- Ujistěte se, že jste přidali účet správce virtuálního počítače do konfiguračního serveru (který se použije k replikovat místní virtuální počítače)
- Ujistěte se, že se vytvoří cíl artefakty v Azure
    - Cílová skupina prostředků
    - Cílový účet úložiště (a jeho skupina prostředků) - vytvoření účtu služby premium storage, pokud plánujete migrovat na premium managed disks
    - Účet úložiště mezipaměti (a jeho skupina prostředků) - vytvořit účet úložiště úrovně standard ve stejné oblasti jako trezor
    - Cílová virtuální síť pro převzetí služeb při selhání (a jeho skupina prostředků)
    - Cílová podsíť
    - Cílová virtuální síť pro testovací převzetí služeb při selhání (a jeho skupina prostředků)
    - Skupina dostupnosti (v případě potřeby)
    - Cílová skupina zabezpečení sítě a jeho skupina prostředků
- Ujistěte se, že jste se rozhodli vlastnosti cílového virtuálního počítače
    - Název cílového virtuálního počítače
    - Cílovou velikost virtuálního počítače v Azure (může být rozhodnuto pomocí Azure Migrate posouzení)
    - Privátní IP adresu primárního síťového rozhraní ve virtuálním počítači
- Stáhněte si skripty z [ukázky Azure Powershellu](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/migrate-at-scale-with-site-recovery) úložišti na Githubu

### <a name="csv-input-file"></a>Sdílený svazek clusteru vstupního souboru
Jakmile budete mít všechny předpoklady dokončit, musíte vytvořit si soubor CSV, který obsahuje data pro každý zdrojový počítač, který chcete migrovat. Vstup sdíleného svazku clusteru musí mít záhlaví řádku s podrobnostmi o vstupu a v řádku podrobností pro každý počítač, který je třeba migrovat. Tyto skripty jsou navrženy pro práci na stejném souboru CSV. Ukázková šablona sdíleného svazku clusteru je k dispozici ve složce scripts pro vaši informaci.

### <a name="script-execution"></a>Provádění skriptu
Když sdílený svazek clusteru je připravená, můžete spustit následující kroky a provést migraci místních virtuálních počítačů:

**Krok #** | **Název skriptu** | **Popis**
--- | --- | ---
1 | asr_startmigration.ps1 | Povolení replikace pro všechny virtuální počítače uvedené ve sdíleném svazku clusteru, skript vytvoří výstupu CSV s podrobnostmi o úloze pro každý virtuální počítač
2 | asr_replicationstatus.ps1 | Zkontrolovat stav replikace, skript vytvoří sdílený svazek clusteru se stavem pro každý virtuální počítač
3 | asr_updateproperties.ps1 | Jakmile se virtuální počítače jsou replikovány nebo chráněným, pomocí tohoto skriptu můžete aktualizovat vlastnosti cílového virtuálního počítače (vlastností výpočty a síť)
4 | asr_propertiescheck.ps1 | Ověření, pokud jsou správně aktualizovat vlastnosti
5 | asr_testmigration.ps1 |  Spuštění testovacího převzetí služeb při selhání virtuálních počítačů uvedených ve sdíleném svazku clusteru, skript vytvoří výstupu CSV s podrobnostmi o úloze pro každý virtuální počítač
6 | asr_cleanuptestmigration.ps1 | Po ruční ověření virtuálních počítačů, které byly testování převzetím služeb při selhání, můžete použít tento skript k vyčištění testovacího převzetí služeb virtuálních počítačů
7 | asr_migration.ps1 | Provedení neplánovaného převzetí služeb při selhání pro virtuální počítače uvedené ve sdíleném svazku clusteru, skript vytvoří výstupu CSV s podrobnostmi o úloze pro každý virtuální počítač. Skript nevypne místní virtuální počítače před aktivací převzetí služeb při selhání pro zajištění konzistence aplikace, je doporučeno, že je ručně vypněte virtuální počítače před spuštěním skriptu.
8 | asr_completemigration.ps1 | Provedení operace potvrzení na virtuálních počítačích a odstranění entit Azure Site Recovery
9 | asr_postmigration.ps1 | Pokud hodláte přiřadit skupiny zabezpečení sítě síťových adaptérů post-převzetí služeb při selhání, můžete k tomu tento skript. Přiřadí skupinu zabezpečení sítě na jakékoli jeden síťový adaptér na cílovém virtuálním počítači.

## <a name="how-to-migrate-to-managed-disks"></a>Migrace na spravované disky?
Skript, ve výchozím nastavení, migruje se virtuální počítače na managed disks v Azure. Pokud zadaný cílový účet úložiště je účtem premium storage, budou vytvořeny premium managed disks po migraci. Účet úložiště mezipaměti může být stále standardní účet. Pokud cílový účet úložiště není účet úložiště úrovně standard, disky úrovně standard budou vytvořeny po migraci. 

## <a name="next-steps"></a>Další postup

[Další informace](https://docs.microsoft.com/azure/site-recovery/migrate-tutorial-on-premises-azure) o migraci serverů do Azure pomocí Azure Site Recovery
