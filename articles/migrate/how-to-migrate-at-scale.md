---
title: Automatizace migrace počítačů migrace v Azure Migrate
description: Popisuje, jak používat skripty k migraci velkého počtu počítačů v Azure Migrate
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: how-to
ms.date: 04/01/2019
ms.openlocfilehash: 01196b23ce5bf9ce842da89d0773a77e6a1d5107
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96754228"
---
# <a name="scale-migration-of-vms"></a>Škálování migrace virtuálních počítačů 

Tento článek vám pomůže pochopit, jak používat skripty k migraci velkého počtu virtuálních počítačů (VM). K škálování migrace slouží [Azure Site Recovery](../site-recovery/site-recovery-overview.md). 

Skripty Site Recovery jsou k dispozici ke stažení na [Azure PowerShell Samples](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/migrate-at-scale-with-site-recovery) na GitHubu. Skripty se dají použít k migraci virtuálních počítačů VMware, AWS, GCP a fyzických serverů na spravované disky v Azure. Tyto skripty můžete také použít k migraci virtuálních počítačů Hyper-V, pokud virtuální počítače migrujete jako fyzické servery. Skripty, které využívají Azure Site Recovery PowerShell, jsou popsané [tady](../site-recovery/vmware-azure-disaster-recovery-powershell.md).

## <a name="current-limitations"></a>Aktuální omezení
- Podpora zadání statické IP adresy jenom pro primární síťovou kartu cílového virtuálního počítače
- Skripty nevezmou Zvýhodněné hybridní využití Azure související vstupy, musíte ručně aktualizovat vlastnosti replikovaného virtuálního počítače na portálu.

## <a name="how-does-it-work"></a>Jak to funguje?

### <a name="prerequisites"></a>Požadavky
Než začnete, musíte provést následující kroky:
- Ujistěte se, že je ve vašem předplatném Azure vytvořený trezor Site Recovery.
- Ujistěte se, že je konfigurační server a procesový Server nainstalovaný ve zdrojovém prostředí a že je trezor schopný zjistit prostředí.
- Zajistěte, aby se vytvořila zásada replikace a přidružil ke konfiguračnímu serveru.
- Ujistěte se, že jste přidali účet správce virtuálního počítače do konfiguračního serveru (který se použije pro replikaci místních virtuálních počítačů).
- Ujistěte se, že jsou vytvořené cílové artefakty v Azure.
    - Cílová skupina prostředků
    - Cílový účet úložiště (a jeho skupina prostředků) – Pokud plánujete migrovat na disky spravované na úrovni Premium, vytvořte účet služby Premium Storage.
    - Účet úložiště mezipaměti (a jeho skupina prostředků) – vytvoří účet Standard Storage ve stejné oblasti jako trezor.
    - Cílová Virtual Network pro převzetí služeb při selhání (a její skupinu prostředků)
    - Cílová podsíť
    - Cílová Virtual Network pro testovací převzetí služeb při selhání (a její skupinu prostředků)
    - Skupina dostupnosti (v případě potřeby)
    - Cílová skupina zabezpečení sítě a její skupina prostředků
- Ujistěte se, že jste se rozhodli o vlastnosti cílového virtuálního počítače.
    - Název cílového virtuálního počítače
    - Velikost cílového virtuálního počítače v Azure (dá se rozhodnout pomocí posouzení Azure Migrate)
    - Privátní IP adresa primárního síťového rozhraní ve virtuálním počítači
- Stažení skriptů z [Azure PowerShellch ukázek](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/migrate-at-scale-with-site-recovery) úložiště na GitHubu

### <a name="csv-input-file"></a>Vstupní soubor CSV
Po dokončení všech požadavků budete muset vytvořit soubor CSV, který obsahuje data pro každý zdrojový počítač, který chcete migrovat. Vstupní sdílený svazek clusteru musí obsahovat řádek záhlaví s podrobnostmi o vstupu a řádek s podrobnostmi pro každý počítač, který je třeba migrovat. Všechny skripty jsou navržené tak, aby fungovaly ve stejném souboru CSV. Ukázková šablona CSV je k dispozici ve složce Scripts pro váš odkaz.

### <a name="script-execution"></a>Spuštění skriptu
Jakmile je sdílený svazek clusteru připravený, můžete provést následující kroky, aby se prováděla migrace místních virtuálních počítačů:

**Krok #** | **Název skriptu** | **Popis**
--- | --- | ---
1 | asr_startmigration.ps1 | Povolí replikaci pro všechny virtuální počítače uvedené ve sdíleném svazku clusteru, skript vytvoří výstup sdíleného svazku clusteru s podrobnostmi o úloze pro každý virtuální počítač.
2 | asr_replicationstatus.ps1 | Zkontroluje stav replikace. skript vytvoří sdílený svazek clusteru se stavem pro každý virtuální počítač.
3 | asr_updateproperties.ps1 | Po replikaci nebo ochraně virtuálních počítačů můžete pomocí tohoto skriptu aktualizovat cílové vlastnosti virtuálního počítače (vlastnosti výpočtů a sítě).
4 | asr_propertiescheck.ps1 | Ověřte, jestli jsou vlastnosti vhodně aktualizované.
5 | asr_testmigration.ps1 |  Spusťte testovací převzetí služeb při selhání virtuálních počítačů uvedených ve sdíleném svazku clusteru, skript vytvoří výstup sdíleného svazku clusteru s podrobnostmi o úloze pro každý virtuální počítač.
6 | asr_cleanuptestmigration.ps1 | Po manuálním ověření virtuálních počítačů, u kterých došlo k převzetí služeb při selhání, můžete pomocí tohoto skriptu vyčistit virtuální počítače testovacího převzetí služeb při selhání.
7 | asr_migration.ps1 | Proveďte neplánované převzetí služeb při selhání u virtuálních počítačů uvedených ve sdíleném svazku clusteru, skript vytvoří výstup sdíleného svazku clusteru s podrobnostmi o úloze pro každý virtuální počítač. Skript před aktivací převzetí služeb při selhání neukončí místní virtuální počítače. kvůli konzistenci aplikací doporučujeme, abyste virtuální počítače před spuštěním skriptu ručně vypnuli.
8 | asr_completemigration.ps1 | Proveďte operaci potvrzení u virtuálních počítačů a odstraňte entity Azure Site Recovery.
9 | asr_postmigration.ps1 | Pokud plánujete přiřadit skupiny zabezpečení sítě k síťovým kartám po převzetí služeb při selhání, můžete k tomu použít tento skript. Přiřadí NSG k libovolnému síťovému rozhraní v cílovém virtuálním počítači.

## <a name="how-to-migrate-to-managed-disks"></a>Jak migrovat na Managed disks?
Ve výchozím nastavení skript migruje virtuální počítače na spravované disky v Azure. Pokud je zadaný cílový účet úložiště účet Premium Storage, Služba Managed disks úrovně Premium se vytvoří po migraci. Účet úložiště mezipaměti může být stále standardní účet. Pokud je cílový účet úložiště standardní účet úložiště, vytvoří se na standardní disky migrace po migraci. 

## <a name="next-steps"></a>Další kroky

[Další informace](../site-recovery/migrate-tutorial-on-premises-azure.md) o migraci serverů do Azure pomocí Azure Site Recovery
