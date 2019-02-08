---
title: Automatizujte migraci velkého počtu virtuálních počítačů do Azure | Dokumentace Microsoftu
description: Popisuje, jak pomocí skriptů můžete migrovat velký počet virtuálních počítačů pomocí Azure Site Recovery
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: article
ms.date: 02/07/2019
ms.author: snehaa
ms.openlocfilehash: c0fc4fa0bdd58b8ecdf4f26051d60324118c4b21
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2019
ms.locfileid: "55896655"
---
# <a name="scale-migration-of-vms-using-azure-site-recovery"></a>Migrace škálovací sady virtuálních počítačů pomocí Azure Site Recovery

Tento článek vám pomůže porozumět procesu pomocí skriptů pro migraci velkého počtu virtuálních počítačů pomocí Azure Site Recovery. Tyto skripty jsou k dispozici pro stahování na [ukázky Azure Powershellu](https://github.com/Azure/azure-docs-powershell-samples) úložišti na Githubu. Skripty lze použít k migraci VMware, AWS, GCP virtuálních počítačů a fyzických serverů do Azure. Tyto skripty můžete také použít k migraci virtuálních počítačů Hyper-V, pokud provádíte migraci virtuálních počítačů jako s fyzickými servery. Skripty využívají zdokumentované Powershellu pro Azure Site Recovery [tady](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell).

## <a name="current-limitations"></a>Aktuální omezení:
- Skripty aktuálně podporují migrace pro nespravované disky jenom
- Podpora migrace na pouze standardní disky
- Nepodporuje určení statickou IP adresu jenom pro primární síťové rozhraní cílového virtuálního počítače
- Skripty nepřebírají související s programem Azure Hybrid Benefit vstupů, je potřeba ručně aktualizovat vlastnosti replikovaný virtuální počítač na portálu

## <a name="how-does-it-work"></a>Jak to funguje?

### <a name="prerequisites"></a>Požadavky
Než začnete, budete muset provést následující kroky:
- Ujistěte se, že trezor Site Recovery se vytvoří ve vašem předplatném Azure
- Ujistěte se, že konfigurační Server a procesový Server jsou nainstalovány ve zdrojovém prostředí a trezor je schopná vyhledávat prostředí
- Ujistěte se, že zásady replikace je vytvořena a přidružena konfiguračního serveru
- Ujistěte se, že jste přidali účet správce virtuálního počítače do konfiguračního serveru (který se použije pro replikaci virtuálních počítačů v místním prostředí)
- Ujistěte se, že jsou vytvořeny cílové artefaktů v Azure
    - Cílová skupina prostředků
    - Cílení na účet úložiště (a jeho skupina prostředků)
    - Cílová virtuální síť pro převzetí služeb při selhání (a jeho skupina prostředků)
    - Cílová podsíť
    - Cílová virtuální síť pro testovací převzetí služeb při selhání (a jeho skupina prostředků)
    - Skupina dostupnosti (v případě potřeby)
    - Cílová skupina zabezpečení sítě a jeho skupina prostředků
- Ujistěte se, že jste se rozhodli vlastnosti cílového virtuálního počítače
    - Název cílového virtuálního počítače
    - Cílovou velikost virtuálního počítače v Azure (může být rozhodnuto pomocí Azure Migrate posouzení)
    - Privátní IP adresu primárního síťového rozhraní ve virtuálním počítači
- Stáhněte si skripty z [ukázky Azure Powershellu](https://github.com/Azure/azure-docs-powershell-samples) úložišti na Githubu

### <a name="csv-input-file"></a>Sdílený svazek clusteru vstupního souboru
Jakmile budete mít všechny předpoklady dokončit, musíte vytvořit soubor CSV, který obsahuje data pro každý zdrojový počítač, který chcete migrovat. Vstup sdíleného svazku clusteru musí mít záhlaví řádku s podrobnostmi o vstupu a v řádku podrobností pro každý počítač, který je třeba migrovat. Tyto skripty jsou navrženy pro práci na stejném souboru CSV. Ukázková šablona sdíleného svazku clusteru je k dispozici ve složce scripts pro vaši informaci.

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
7 | asr_migration.ps1 | Provedení neplánovaného převzetí služeb při selhání pro virtuální počítače uvedené ve sdíleném svazku clusteru, skript vytvoří výstupu CSV s podrobnostmi o úloze pro každý virtuální počítač. Tento skript nemá vypnutí virtuální počítače v místním prostředí před aktivací převzetí služeb při selhání pro zajištění konzistence aplikace, je doporučeno, že je ručně vypněte virtuální počítače před spuštěním skriptu.
8 | asr_completemigration.ps1 | Provedení operace potvrzení na virtuálních počítačích a odstranění entit Azure Site Recovery
9 | asr_postmigration.ps1 | Pokud hodláte přiřadit skupiny zabezpečení sítě síťových adaptérů post-převzetí služeb při selhání, můžete k tomu tento skript. Přiřadí NSG na všechny jeden síťový adaptér na cílovém virtuálním počítači.

## <a name="next-steps"></a>Další kroky

[Další informace](https://docs.microsoft.com/azure/site-recovery/migrate-tutorial-on-premises-azure) o migraci serverů do Azure pomocí Azure Site Recovery
