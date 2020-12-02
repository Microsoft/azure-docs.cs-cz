---
title: Automatizace migrace VMware bez agentů v Azure Migrate
description: Popisuje, jak používat skripty k migraci velkého počtu virtuálních počítačů VMware v Azure Migrate
author: rahulgup
ms.service: azure-migrate
ms.topic: article
ms.date: 10/30/2020
ms.author: rahugup
ms.openlocfilehash: 4ef4d3989f063e9d31718bc7143662c4f20cc90b
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2020
ms.locfileid: "96500814"
---
# <a name="scale-migration-of-vmware-vms"></a>Škálování migrace virtuálních počítačů VMware 

Tento článek vám pomůže pochopit, jak používat skripty k migraci velkého počtu virtuálních počítačů VMware pomocí metody bez agenta. Pokud chcete škálovat migrace, použijte [Azure Migrate modul PowerShellu](./tutorial-migrate-vmware-powershell.md). 

Azure Migrate skripty pro automatizaci migrace VMware jsou k dispozici ke stažení na webu pro [Azure PowerShell Samples](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/migrate-at-scale-vmware-agentles) na GitHubu. Skripty se dají použít k migraci virtuálních počítačů VMware do Azure pomocí metody migrace bez agenta. Následující příkazy Azure Migrate PowerShellu používané v těchto skriptech jsou popsané [tady](./tutorial-migrate-vmware-powershell.md).

## <a name="current-limitations"></a>Aktuální omezení
- Tyto skripty podporují migraci virtuálních počítačů VMware se všemi disky. Skripty můžete aktualizovat, pokud chcete selektivně replikovat disky připojené k virtuálnímu počítači VMware. 
- Skripty podporují použití doporučení pro posouzení. Pokud se doporučení k vyhodnocování nepoužívají, budou všechny disky připojené k virtuálnímu počítači VMware migrovány na stejný typ spravovaného disku (Standard nebo Premium). Můžete aktualizovat skripty, pokud chcete použít více typů spravovaných disků se stejným virtuálním počítačem.

## <a name="prerequisites"></a>Předpoklady

- [Dokončete kurz zjišťování](tutorial-discover-vmware.md) , abyste mohli připravit Azure a VMware pro migraci.
- Doporučujeme, abyste dokončili druhý kurz pro [vyhodnocení virtuálních počítačů VMware](./tutorial-assess-vmware-azure-vm.md) před jejich migrací do Azure.
- Máte `Az` modul Azure PowerShell. Pokud potřebujete nainstalovat nebo upgradovat Azure PowerShell, postupujte podle pokynů v tomto [Průvodci a nainstalujte a nakonfigurujte Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="install-azure-migrate-powershell-module"></a>Instalace modulu Azure Migrate PowerShellu

Modul prostředí PowerShell pro Azure Migrate je k dispozici ve verzi Preview. Modul PowerShell budete muset nainstalovat pomocí následujícího příkazu. 

```azurepowershell
Install-Module -Name Az.Migrate 
```

## <a name="csv-input-file"></a>Vstupní soubor CSV
Po dokončení všech požadavků budete muset vytvořit soubor CSV, který obsahuje data pro každý zdrojový virtuální počítač, který chcete migrovat. Všechny skripty jsou navržené tak, aby fungovaly ve stejném souboru CSV. Ukázková šablona CSV je k dispozici ve složce Scripts pro váš odkaz. Soubor CSV je možné nakonfigurovat tak, abyste mohli použít doporučení pro posouzení a dokonce určit, jestli se některé operace nespouštějí pro konkrétní virtuální počítač. 

> [!NOTE]
> K migraci virtuálních počítačů ve více Azure Migrate projektech se dá použít stejný soubor CSV.

### <a name="csv-file-schema"></a>Schéma souboru CSV

**Záhlaví sloupce** | **Popis**
--- | ---
AZMIGRATEPROJECT_SUBSCRIPTION_ID | Zadejte ID předplatného Azure Migrate projektu.
AZMIGRATEPROJECT_RESOURCE_GROUP_NAME | Zadejte Azure Migrate název skupiny prostředků.
AZMIGRATEPROJECT_NAME | Zadejte název projektu Azure Migrate, do kterého chcete migrovat servery. 
SOURCE_MACHINE_NAME | Zadejte popisný název (zobrazovaný název) zjištěného virtuálního počítače v projektu Azure Migrate.
AZMIGRATEASSESSMENT_NAME | Zadejte název posouzení, které je potřeba využít k migraci.
AZMIGRATEGROUP_NAME | Zadejte název skupiny, která se použila pro vyhodnocení Azure Migrate.
TARGET_RESOURCE_GROUP_NAME | Zadejte název skupiny prostředků Azure, do které se virtuální počítač musí migrovat.
TARGET_VNET_NAME| Zadejte název Virtual Network Azure, který by měl migrovaný virtuální počítač použít.
TARGET_SUBNET_NAME | Zadejte název podsítě v cílové virtuální síti, kterou by měl migrovaný virtuální počítač použít. Pokud je ponecháno prázdné, bude použita "výchozí" podsíť.
TARGET_MACHINE_NAME | Zadejte název, který bude migrovaný virtuální počítač používat v Azure. Pokud necháte pole prázdné, použije se název zdrojového počítače.  
TARGET_MACHINE_SIZE | Zadejte skladovou jednotku, kterou by měl virtuální počítač používat v Azure. Pokud chcete migrovat virtuální počítač, aby se D2_v2 virtuální počítač v Azure, zadejte hodnotu v tomto poli jako "Standard_D2_v2". Použijete-li posouzení, bude tato hodnota odvozena podle doporučení posouzení.
LICENSE_TYPE | Určete, jestli chcete použít Zvýhodněné hybridní využití Azure pro virtuální počítače s Windows serverem. K využití Zvýhodněné hybridní využití Azure použijte hodnotu "WindowsServer". Jinak ponechte prázdné nebo použijte "NoLicenseType".
OS_DISK_ID | Zadejte ID disku operačního systému pro virtuální počítač, který se má migrovat. ID disku, které se má použít, je vlastnost jedinečného identifikátoru (UUID) pro disk načtený pomocí rutiny Get-AzMigrateServer. Skript použije první disk virtuálního počítače jako disk s operačním systémem pro případ, že není zadána žádná hodnota.
TARGET_DISKTYPE | Zadejte typ disku, který se má použít pro všechny disky virtuálního počítače v Azure. Pro disky se spravovanými na úrovni Premium, ' StandardSSD_LRS ' pro disky SSD a ' Standard_LRS ' pro použití standardních disků HDD použijte ' Premium_LRS '. Pokud se rozhodnete použít posouzení, skript bude upřednostňovat použití doporučených typů disků pro každý disk virtuálního počítače. Pokud nepoužíváte posouzení ani nezadáte žádnou hodnotu, skript bude ve výchozím nastavení používat standardní disky HDD.    
AVAILABILITYZONE_NUMBER | Zadejte číslo zóny dostupnosti, která se má použít pro migrovaný virtuální počítač. Pro případ, že nechcete používat zóny dostupnosti, můžete ponechat toto prázdné. 
AVAILABILITYSET_NAME | Zadejte název skupiny dostupnosti, která se má použít pro migrovaný virtuální počítač. Pro případ, že nechcete používat skupinu dostupnosti, můžete ponechat toto prázdné.
TURNOFF_SOURCESERVER | Pokud chcete vypnout zdrojový virtuální počítač v době migrace, zadejte ' Y '. Použijte v opačném případě ' N '. Pokud je ponecháno prázdné, bude skript předpokládat hodnotu jako N.
TESTMIGRATE_VNET_NAME | Zadejte název virtuální sítě, která se má použít k testování migrace.
UPDATED_TARGET_RESOURCE_GROUP_NAME | Pokud chcete aktualizovat skupinu prostředků, která se má použít u migrovaného virtuálního počítače v Azure, zadejte název skupiny prostředků Azure, jinak ponechte prázdné. 
UPDATED_TARGET_VNET_NAME | Pokud chcete aktualizovat Virtual Network pro použití migrovaného virtuálního počítače v Azure, zadejte název Virtual Network Azure, jinak ponechte prázdné.
UPDATED_TARGET_MACHINE_NAME | Pokud chcete aktualizovat název, který bude používat migrovaný virtuální počítač v Azure, zadejte nový název, který se má použít, jinak ponechte prázdné.
UPDATED_TARGET_MACHINE_SIZE | Pokud chcete aktualizovat skladovou jednotku, aby ji používal migrovaný virtuální počítač v Azure, zadejte novou SKLADOVOU položku, která se má použít, jinak ponechte prázdné.
UPDATED_AVAILABILITYZONE_NUMBER | Pokud chcete zónu dostupnosti aktualizovat tak, aby se použila migrovanému virtuálnímu počítači v Azure, zadejte novou zónu dostupnosti, která se má použít, jinak ponechte prázdné.
UPDATED_AVAILABILITYSET_NAME | Pokud chcete aktualizovat skupinu dostupnosti, která se má použít u migrovaného virtuálního počítače v Azure, zadejte novou skupinu dostupnosti, která se má použít, jinak ponechte prázdné.
UPDATE_NIC1_ID | Zadejte ID síťového adaptéru, který se má aktualizovat. Pokud je ponecháno prázdné, bude skript předpokládat, že se jedná o první síťovou kartu zjištěného virtuálního počítače. Pokud nechcete aktualizovat NIC virtuálního počítače, ponechte všechna pole obsahující název síťové karty prázdná. 
UPDATED_TARGET_NIC1_SELECTIONTYPE | Zadejte hodnotu, která se má použít pro tuto síťovou kartu. Použijte "primární", "sekundární" nebo "DoNotCreate", chcete-li určit, zda by měla být tato síťová karta primární, sekundární nebo by neměla být vytvořena na migrovaném virtuálním počítači. Jako primární síťová karta pro virtuální počítač lze zadat pouze jednu síťovou kartu. Pokud nechcete aktualizovat, ponechte pole prázdné.
UPDATED_TARGET_NIC1_SUBNET_NAME | Zadejte název podsítě, která se má použít pro síťovou kartu na migrovaném virtuálním počítači. Pokud nechcete aktualizovat, ponechte pole prázdné.
UPDATED_TARGET_NIC1_IP | Pokud chcete používat statickou IP adresu, zadejte adresu IPv4, kterou má síťová karta použít u migrovaného virtuálního počítače. Pokud chcete automaticky přiřadit IP adresu, použijte možnost auto. Pokud nechcete aktualizovat, ponechte pole prázdné.
UPDATE_NIC2_ID | Zadejte ID síťového adaptéru, který se má aktualizovat. Pokud je ponecháno prázdné, bude skript předpokládat, že hodnota bude druhou síťovou kartou zjištěného virtuálního počítače. Pokud nechcete aktualizovat NIC virtuálního počítače, ponechte všechna pole obsahující název síťové karty prázdná.
UPDATED_TARGET_NIC2_SELECTIONTYPE | Zadejte hodnotu, která se má použít pro tuto síťovou kartu. Použijte "primární", "sekundární" nebo "DoNotCreate", chcete-li určit, zda by měla být tato síťová karta primární, sekundární nebo by neměla být vytvořena na migrovaném virtuálním počítači. Jako primární síťová karta pro virtuální počítač lze zadat pouze jednu síťovou kartu. Pokud nechcete aktualizovat, ponechte pole prázdné.
UPDATED_TARGET_NIC2_SUBNET_NAME | Zadejte název podsítě, která se má použít pro síťovou kartu na migrovaném virtuálním počítači. Pokud nechcete aktualizovat, ponechte pole prázdné.
UPDATED_TARGET_NIC2_IP | Pokud chcete používat statickou IP adresu, zadejte adresu IPv4, kterou má síťová karta použít u migrovaného virtuálního počítače. Pokud chcete automaticky přiřadit IP adresu, použijte možnost auto. Pokud nechcete aktualizovat, ponechte pole prázdné.
OK_TO_UPDATE | Pomocí Y určete, jestli se při spuštění skriptu AzMigrate_UpdateMachineProperties musí aktualizovat vlastnosti virtuálního počítače. Použijte ' N ' nebo v opačném případě nechejte prázdné.
OK_TO_MIGRATE | Pomocí Y určete, jestli se má při spuštění skriptu AzMigrate_StartMigration migrovat virtuální počítač. Pokud nechcete migrovat virtuální počítač, použijte ' N ' nebo ponechte prázdné. 
OK_TO_USE_ASSESSMENT | Pomocí ' Y ' určete, zda má virtuální počítač spustit replikaci pomocí doporučení pro vyhodnocení při spuštění skriptu AzMigrate_StartReplication. Tato akce přepíše hodnoty TARGET_MACHINE_SIZE a TARGET_DISKTYPE v souboru CSV. Pokud nechcete používat doporučení pro vyhodnocení, použijte ' N ' nebo ponechte prázdné.
OK_TO_TESTMIGRATE | Pomocí ' Y ' určete, zda má být při spuštění skriptu AzMigrate_StartTestMigration testována migrace virtuálního počítače. Pokud nechcete testovat migraci virtuálního počítače, použijte ' N ' nebo ponechte prázdné. 
OK_TO_RETRIEVE_REPLICATIONSTATUS | Pomocí Y určete, jestli se má při spuštění skriptu AzMigrate_ReplicationStatus aktualizovat stav replikace virtuálního počítače. Pokud nechcete aktualizovat stav replikace, použijte ' N ' nebo ponechte prázdné.
OK_TO_CLEANUP | Pomocí Y určete, jestli se má při spuštění skriptu AzMigrate_StopReplication vyčistit replikace virtuálního počítače. Použijte ' N ' nebo v opačném případě nechejte prázdné.
OK_TO_TESTMIGRATE_CLEANUP | Pomocí Y určete, jestli se má při spuštění skriptu AzMigrate_CleanUpTestMigration vyčistit testovací migrace pro virtuální počítač. Použijte ' N ' nebo v opačném případě nechejte prázdné.


## <a name="script-execution"></a>Spuštění skriptu

Jakmile je sdílený svazek clusteru připravený, můžete provést následující kroky k migraci místních virtuálních počítačů VMware.

**Krok #** | **Název skriptu** | **Popis**
--- | --- | ---
1 | AzMigrate_StartReplication.ps1 | Povolí replikaci pro všechny virtuální počítače uvedené ve sdíleném svazku clusteru, skript vytvoří výstup sdíleného svazku clusteru a soubor protokolu pro řešení potíží.
2 | AzMigrate_ReplicationStatus.ps1 | Zkontroluje stav replikace. skript vytvoří výstup sdíleného svazku clusteru se stavem pro každý virtuální počítač a soubor protokolu pro řešení potíží.
3 | AzMigrate_UpdateMachineProperties.ps1 | Po dokončení počáteční replikace virtuálních počítačů můžete pomocí tohoto skriptu aktualizovat cílové vlastnosti virtuálního počítače (vlastnosti výpočtů a sítě). Skript vytvoří výstup sdíleného svazku clusteru s podrobnostmi o úloze pro každý virtuální počítač.
4 | AzMigrate_StartTestMigration.ps1 |  Spusťte testovací převzetí služeb při selhání pro všechny virtuální počítače uvedené ve sdíleném svazku clusteru, které jsou nakonfigurované pro testování migrace. Skript vytvoří výstup sdíleného svazku clusteru s podrobnostmi o úloze pro každý virtuální počítač.
5 | AzMigrate_CleanUpTestMigration.ps1 | Po manuálním ověření virtuálních počítačů, u kterých došlo k převzetí služeb při selhání, můžete pomocí tohoto skriptu vyčistit virtuální počítače testovacího převzetí služeb při selhání pro všechny virtuální počítače uvedené ve sdíleném svazku clusteru, které jsou nakonfigurovány pro vyčištění testů migrace. Skript vytvoří výstup sdíleného svazku clusteru s podrobnostmi o úloze pro každý virtuální počítač.
6 | AzMigrate_StartMigration.ps1 | Zahajte migraci všech virtuálních počítačů uvedených ve sdíleném svazku clusteru, které jsou nakonfigurované pro migraci. Skript vytvoří výstup sdíleného svazku clusteru s podrobnostmi o úloze pro každý virtuální počítač.
7 | AzMigrate_StopReplication.ps1 | Po úspěšné migraci zastaví replikaci virtuálního počítače, nebo pokud chcete replikaci zrušit z jiných důvodů. Skript vytvoří výstup sdíleného svazku clusteru s podrobnostmi o úloze pro každý virtuální počítač.


Následující skripty jsou vyvolány jinými skripty pro všechny operace Azure Migrate, jako je povolení replikace, spuštění testovací migrace, aktualizace vlastností virtuálního počítače atd. Zajistěte, aby všechny skripty byly přítomné ve stejné složce nebo cestě. 

**Krok #** | **Název skriptu** | **Popis**
--- | --- | ---
1 | AzMigrate_Shared.ps1 | Běžný skript obsahující funkce pro načítání vlastností posouzení (prostřednictvím rozhraní API), zjištěných virtuálních počítačů a replikace virtuálních počítačů. 
2 | AzMigrate_CSV_Processor.ps1 | Běžný skript obsahující funkce používané pro operace se soubory CSV, včetně načítání, čtení a tisku protokolů. 
3 | AzMigrate_Logger.ps1 | Společný skript vyvolaný pro generování souboru protokolu pro Azure Migrate operace automatizace Soubor protokolu bude log.Scriptname.Datetime.txt formátu.

Kromě výše uvedeného obsahuje složka také AzMigrate_Template.ps1, která obsahuje kostru rozhraní pro vytváření vlastních skriptů pro různé Azure Migrate operace. 

### <a name="script-execution-syntax"></a>Syntaxe spuštění skriptu

Po stažení skriptů se skripty dají spustit následujícím způsobem.

Pokud chcete spustit skript pro spuštění replikace virtuálních počítačů pomocí souboru Input.csv, použijte následující syntaxi. 

```azurepowershell
".\AzMigrate_StartReplication.ps1" .\Input.csv 
```

Další informace o použití Azure PowerShell pro migraci virtuálních počítačů VMware pomocí Azure Migrate najdete v tomto [kurzu](./tutorial-migrate-vmware-powershell.md).