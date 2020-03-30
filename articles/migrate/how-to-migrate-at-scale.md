---
title: Automatizace migrace migrace počítače v Azure Migrate
description: Popisuje, jak používat skripty k migraci velkého počtu počítačů v Azure Migrate.
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: article
ms.date: 04/01/2019
ms.author: snehaa
ms.openlocfilehash: 317b6e8aa799b7982e9897c6a504d6092491c7ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74196369"
---
# <a name="scale-migration-of-vms"></a>Migrace virtuálních vojín škálovat 

Tento článek vám pomůže pochopit, jak používat skripty k migraci velkého počtu virtuálních počítačů. Chcete-li migraci škálovat, použijte [Azure Site Recovery](../site-recovery/site-recovery-overview.md). 

Skripty pro obnovení webu jsou k dispozici ke stažení na azure [powershellové ukázky](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/migrate-at-scale-with-site-recovery) úložiště na GitHubu. Skripty lze použít k migraci virtuálních počítačů VMware, AWS, GCP a fyzických serverů na spravované disky v Azure. Tyto skripty můžete také použít k migraci virtuálních virtuálních zařízení Hyper-V, pokud migrujete virtuální aplikace jako fyzické servery. Skripty, které využívají Azure Site Recovery PowerShell jsou zdokumentovány [zde](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell).

## <a name="current-limitations"></a>Aktuální omezení
- Podpora určení statické IP adresy jenom pro primární nic cílového virtuálního počítačů
- Skripty neberou Azure Hybridní výhody související vstupy, je třeba ručně aktualizovat vlastnosti replikovaného virtuálního počítače na portálu

## <a name="how-does-it-work"></a>Jak to funguje?

### <a name="prerequisites"></a>Požadavky
Než začnete, musíte provést následující kroky:
- Ujistěte se, že trezor site recovery je vytvořen ve vašem předplatném Azure
- Ujistěte se, že konfigurační server a procesní server jsou nainstalovány ve zdrojovém prostředí a úschovna je schopna zjistit prostředí
- Ujistěte se, že je vytvořena zásada replikace a přidružena ke konfiguračnímu serveru
- Ujistěte se, že jste přidali účet správce virtuálního počítače na konfigurační server (který se použije k replikaci místních virtuálních počítačů).
- Ujistěte se, že jsou vytvořeny cílové artefakty v Azure
    - Cílová skupina prostředků
    - Cílový účet úložiště (a jeho skupina prostředků) – vytvořte účet úložiště premium, pokud plánujete migraci na disky s spravovaným prémií
    - Účet úložiště mezipaměti (a jeho skupina prostředků) – vytvoření standardního účtu úložiště ve stejné oblasti jako trezor
    - Cílová virtuální síť pro převzetí služeb při selhání (a její skupina prostředků)
    - Cílová podsíť
    - Cílová virtuální síť pro převzetí služeb při selhání testu (a její skupina prostředků)
    - Sada dostupnosti (v případě potřeby)
    - Cílová skupina zabezpečení sítě a její skupina prostředků
- Ujistěte se, že jste se rozhodli pro vlastnosti cílového virtuálního počítače.
    - Název cílového virtuálního_kma
    - Cílová velikost virtuálního počítače v Azure (o tom se rozhoduje pomocí vyhodnocení migrace Azure)
    - Privátní IP adresa primární nic ve virtuálním mísu
- Stažení skriptů z úložiště [ukázek Azure PowerShell](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/migrate-at-scale-with-site-recovery) na GitHubu

### <a name="csv-input-file"></a>Vstupní soubor CSV
Po dokončení všech předpokladů je třeba vytvořit soubor CSV, který obsahuje data pro každý zdrojový počítač, který chcete migrovat. Vstupní soubor CSV musí mít řádek záhlaví se vstupními podrobnostmi a řádek s podrobnostmi pro každý počítač, který je třeba migrovat. Všechny skripty jsou navrženy tak, aby fungovaly na stejném souboru CSV. Ukázková šablona CSV je k dispozici ve složce skripty pro vaši referenci.

### <a name="script-execution"></a>Spuštění skriptu
Jakmile je csv připraven, můžete provést následující kroky k provedení migrace místních virtuálních počítačů:

**Krok #** | **Název skriptu** | **Popis**
--- | --- | ---
1 | asr_startmigration.ps1 | Povolit replikaci pro všechny virtuální počítače uvedené v csv, skript vytvoří výstup CSV s podrobnostmi o úloze pro každý virtuální počítač
2 | asr_replicationstatus.ps1 | Zkontrolujte stav replikace, skript vytvoří csv se stavem pro každý virtuální počítač
3 | asr_updateproperties.ps1 | Po replikaci nebo chráněné virtuální počítače, použijte tento skript k aktualizaci cílových vlastností virtuálního počítače (výpočetní a síťové vlastnosti)
4 | asr_propertiescheck.ps1 | Ověřte, zda jsou vlastnosti odpovídajícím způsobem aktualizovány
5 | asr_testmigration.ps1 |  Spuštění testu převzetí služeb při selhání virtuálních počítačů uvedených v csv, skript vytvoří výstup CSV s podrobnostmi o úloze pro každý virtuální počítač
6 | asr_cleanuptestmigration.ps1 | Po ručníověření virtuálních stránek, které byly test převzetí služeb při selhání, můžete použít tento skript k vyčištění test převzetí služeb při selhání virtuálních disekonů
7 | asr_migration.ps1 | Proveďte neplánované převzetí služeb při selhání pro virtuální počítače uvedené v csv, skript vytvoří výstup CSV s podrobnostmi o úloze pro každý virtuální počítač. Skript nevypne místní virtuální počítačové služby před aktivací převzetí služeb při selhání, pro konzistenci aplikace, je doporučeno ručně vypnout virtuální chod před spuštěním skriptu.
8 | asr_completemigration.ps1 | Provedení operace potvrzení na virtuálních počítačích a odstranění entit Azure Site Recovery
9 | asr_postmigration.ps1 | Pokud plánujete přiřadit skupiny zabezpečení sítě k síťovým připojením po převzetí služeb při selhání, můžete k tomu použít tento skript. Přiřadí skupinu nsg libovolné nic v cílovém virtuálním virtuálním míse.

## <a name="how-to-migrate-to-managed-disks"></a>Jak migrovat na spravované disky?
Skript ve výchozím nastavení migruje virtuální počítače na spravované disky v Azure. Pokud je zadaný cílový účet úložiště účet premium úložiště, premium spravované disky se vytvoří po migraci. Účet úložiště mezipaměti může být stále standardní účet. Pokud je cílový účet úložiště standardní účet úložiště, standardní disky jsou vytvořeny po migraci. 

## <a name="next-steps"></a>Další kroky

[Další informace](https://docs.microsoft.com/azure/site-recovery/migrate-tutorial-on-premises-azure) o migraci serverů do Azure pomocí Azure Site Recovery
