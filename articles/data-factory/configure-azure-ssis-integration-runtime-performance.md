---
title: Konfigurace výkonu pro runtime integrace Azure-SSIS
description: Zjistěte, jak nakonfigurovat vlastnosti prostředí Azure-SSIS Integration Runtime pro vysoký výkon
services: data-factory
ms.date: 01/10/2018
ms.topic: conceptual
ms.service: data-factory
ms.workload: data-services
author: swinarko
ms.author: sawinark
ms.reviewer: ''
manager: anandsub
ms.openlocfilehash: ca88e42438c7cb48b062aa67d82053afbb9244bf
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418282"
---
# <a name="configure-the-azure-ssis-integration-runtime-for-high-performance"></a>Konfigurace runtime integrace Azure-SSIS pro vysoký výkon

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]


Tento článek popisuje, jak nakonfigurovat prostředí Integrace Azure-SSIS (IR) pro vysoký výkon. Azure-SSIS IR umožňuje nasadit a spustit balíčky SQL Server Integration Services (SSIS) v Azure. Další informace o Azure-SSIS IR najdete v článku [integrační běhový](concepts-integration-runtime.md#azure-ssis-integration-runtime) čas. Informace o nasazení a spouštění balíčků SSIS v Azure najdete v [tématu výtah a přesun úloh služby SQL Server Integration Services do cloudu](/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview).

> [!IMPORTANT]
> Tento článek obsahuje výsledky výkonu a pozorování z interního testování provedenéčleny vývojového týmu SSIS. Vaše výsledky se mohou lišit. Před dokončením nastavení konfigurace proveďte vlastní testování, které ovlivní náklady i výkon.

## <a name="properties-to-configure"></a>Vlastnosti, které chcete konfigurovat

Následující část konfiguračního skriptu zobrazuje vlastnosti, které můžete nakonfigurovat při vytváření prostředí Azure-SSIS Integration Runtime. Úplný skript a popis Prostředí PowerShell najdete v tématu [Nasazení balíčků služby SQL Server Integration Services do Azure](tutorial-deploy-ssis-packages-azure-powershell.md).

```powershell
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime information - This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "[specify a name for your Azure-SSIS IR]"
$AzureSSISDescription = "[specify a description for your Azure-SSIS IR]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2
# Azure-SSIS IR edition/license info: Standard or Enterprise
$AzureSSISEdition = "Standard" # Standard by default, while Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, while BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, up to 4 parallel executions per node are supported, but for other nodes, up to max(2 x number of cores, 8) are currently supported
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide SAS URI of blob container where your custom setup script and its associated files are stored
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use Azure SQL Database with virtual network service endpoints/Managed Instance/on-premises data, Azure Resource Manager virtual network is recommended, Classic virtual network will be deprecated soon
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Please use the same subnet as the one used with your Azure SQL Database with virtual network service endpoints or a different subnet than the one used for your Managed Instance

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name or Managed Instance name.DNS prefix].database.windows.net" # WARNING: Please ensure that there is no existing SSISDB, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure Active Directory (AAD)
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for AAD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for AAD authentication]"
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database or leave it empty for Managed Instance]"
```

## <a name="azuressislocation"></a>AzuressISUmístění
**AzureSSISLocation** je umístění pro pracovní uzel pracující za běhu integrace. Pracovní uzel udržuje konstantní připojení k databázi katalogu SSIS (SSISDB) v databázi Azure SQL. Nastavte **AzureSSISLocation** do stejného umístění jako sql database server, který je hostitelem SSISDB, který umožňuje modul runtime integrace pracovat co nejefektivněji.

## <a name="azuressisnodesize"></a>AzureSSISNodeVelikost
Data Factory, včetně Azure-SSIS IR, podporuje následující možnosti:
-   Standardní\_A4\_v2
-   Standardní\_A8\_v2
-   Standardní\_D1\_v2
-   Standardní\_D2\_v2
-   Standardní\_D3\_v2
-   Standardní\_D4\_v2
-   Standardní\_D2\_v3
-   Standardní\_D4\_v3
-   Standardní\_D8\_v3
-   Standardní\_D16\_v3
-   Standardní\_D32\_v3
-   Standardní\_D64\_v3
-   Standardní\_E2\_v3
-   Standardní\_E4\_v3
-   Standardní\_E8\_v3
-   Standardní\_E16\_v3
-   Standardní\_E32\_v3
-   Standardní\_E64\_v3

Při neoficiálním interním testování technickým týmem SSIS se zdá, že řada D je vhodnější pro provedení balíčků SSIS než řada A.

-   Poměr výkonu a ceny řady D je vyšší než u řady A a poměr výkonu a ceny řady v3 je vyšší než poměr řady v2.
-   Propustnost pro řadu D je vyšší než řada A za stejnou cenu a propustnost pro řadu v3 je vyšší než řada v2 za stejnou cenu.
-   Uzly řady v2 azure-SSIS IR nejsou vhodné pro vlastní nastavení, proto místo toho použijte uzly řady v3. Pokud již používáte uzly řady v2, přepněte na co nejrychlejší použití uzlů řady v3.
-   Řada E je velikost virtuálních počítačů optimalizovaná pro paměť, která poskytuje vyšší poměr paměti k procesoru než ostatní počítače. Pokud váš balíček vyžaduje hodně paměti, můžete zvážit výběr virtuálního počítače řady E.

### <a name="configure-for-execution-speed"></a>Konfigurace pro rychlost spuštění
Pokud nemáte mnoho balíčků ke spuštění a chcete, aby balíčky běžely rychle, použijte informace v následujícím grafu a zvolte typ virtuálního počítače vhodný pro váš scénář.

Tato data představují spuštění jednoho balíčku na jednom pracovním uzlu. Balíček načte 3 miliony záznamů se sloupcemi s křestním jménem a příjmením ze služby Azure Blob Storage, vygeneruje sloupec s úplným názvem a zapíše do úložiště objektů blob Azure, které mají celý název delší než 20 znaků.

![Rychlost spuštění balíčku integrace SSIS](media/configure-azure-ssis-integration-runtime-performance/ssisir-execution-speedV2.png)

### <a name="configure-for-overall-throughput"></a>Konfigurace celkové propustnosti

Pokud máte velké množství balíčků ke spuštění a nejvíce se staráte o celkovou propustnost, použijte informace v následujícím grafu a zvolte typ virtuálního počítače vhodný pro váš scénář.

![Maximální celková propustnost za běhu integrace SSIS](media/configure-azure-ssis-integration-runtime-performance/ssisir-overall-throughputV2.png)

## <a name="azuressisnodenumber"></a>Číslo AzureSSISNode

**AzureSSISNodeNumber** upravuje škálovatelnost integračního běhu. Propustnost integračního běhu je úměrná **azuresisisnodenumber**. Nastavte **AzureSSISNodeNumber** na malou hodnotu na první, monitorování propustnost integrace runtime a pak upravit hodnotu pro váš scénář. Pokud chcete překonfigurovat počet pracovních uzlů, [přečtěte si informace o správě za běhu integrace Azure-SSIS](manage-azure-ssis-integration-runtime.md).

## <a name="azuressismaxparallelexecutionspernode"></a>AzureSSISMaxParallelExecutionsPerNode

Pokud již používáte výkonný pracovní uzel ke spuštění balíčků, zvýšení **AzureSSISMaxParallelExecutionsPerNode** může zvýšit celkovou propustnost integračního běhu. Pro Standard_D1_v2 uzly jsou podporovány 1-4 paralelní spuštění na uzel. Pro všechny ostatní typy uzlů jsou podporovány paralelní spuštění 1-max(2 x počet jader, 8) na uzel. Pokud chcete **AzureSSISMaxParallelExecutionsPerNode** nad rámec maximální hodnoty, kterou jsme podpořili, můžete otevřít lístek podpory a můžeme zvýšit maximální hodnotu pro vás a poté budete potřebovat použít Azure Powershell k aktualizaci **AzureSSISMaxParallelExecutionsPerNode**.
Můžete odhadnout příslušnou hodnotu na základě nákladů na balíček a následujícíkonfigurace pro pracovní uzly. Další informace naleznete [v tématu Univerzální velikosti virtuálních strojů](../virtual-machines/windows/sizes-general.md).

| Velikost             | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Maximální propustnost dočasného úložiště: IOPS / čtení v MB/s / zápis v MB/s | Maximální propustnost datových disků: IOPS | Max. počet síťových karet / Očekávaný výkon sítě (Mb/s) |
|------------------|------|-------------|------------------------|------------------------------------------------------------|-----------------------------------|------------------------------------------------|
| Standardní\_D1\_v2 | 1    | 3,5         | 50                     | 3000 / 46 / 23                                             | 2 / 2×500                         | 2 / 750                                        |
| Standardní\_D2\_v2 | 2    | 7           | 100                    | 6000 / 93 / 46                                             | 4 / 4×500                         | 2 / 1 500                                       |
| Standardní\_D3\_v2 | 4    | 14          | 200                    | 12000 / 187 / 93                                           | 8 / 8×500                         | 4 / 3 000                                       |
| Standardní\_D4\_v2 | 8    | 28          | 400                    | 24000 / 375 / 187                                          | 16 / 16×500                       | 8 / 6 000                                       |
| Standardní\_A4\_v2 | 4    | 8           | 40                     | 4000 / 80 / 40                                             | 8 / 8×500                         | 4 / 1 000                                       |
| Standardní\_A8\_v2 | 8    | 16          | 80                     | 8000 / 160 / 80                                            | 16 / 16×500                       | 8 / 2 000                                       |
| Standardní\_D2\_v3 | 2    | 8           | 50                     | 3000 / 46 / 23                                             | 4 / 6x500                         | 2 / 1 000                                       |
| Standardní\_D4\_v3 | 4    | 16          | 100                    | 6000 / 93 / 46                                             | 8 / 12x500                        | 2 / 2 000                                       |
| Standardní\_D8\_v3 | 8    | 32          | 200                    | 12000 / 187 / 93                                           | 16 / 24x500                       | 4 / 4000                                       |
| Standardní\_D16\_v3| 16   | 64          | 400                    | 24000 / 375 / 187                                          | 32/ 48x500                        | 8 / 8000                                       |
| Standardní\_D32\_v3| 32   | 128         | 800                    | 48000 / 750 / 375                                          | 32 / 96x500                       | 8 / 16 000                                      |
| Standardní\_D64\_v3| 64   | 256         | 1600                   | 96000 / 1000 / 500                                         | 32 / 192x500                      | 8 / 30000                                      |
| Standardní\_E2\_v3 | 2    | 16          | 50                     | 3000 / 46 / 23                                             | 4 / 6x500                         | 2 / 1 000                                       |
| Standardní\_E4\_v3 | 4    | 32          | 100                    | 6000 / 93 / 46                                             | 8 / 12x500                        | 2 / 2 000                                       |
| Standardní\_E8\_v3 | 8    | 64          | 200                    | 12000 / 187 / 93                                           | 16 / 24x500                       | 4 / 4000                                       |
| Standardní\_E16\_v3| 16   | 128         | 400                    | 24000 / 375 / 187                                          | 32 / 48x500                       | 8 / 8000                                       |
| Standardní\_E32\_v3| 32   | 256         | 800                    | 48000 / 750 / 375                                          | 32 / 96x500                       | 8 / 16 000                                      |
| Standardní\_E64\_v3| 64   | 432         | 1600                   | 96000 / 1000 / 500                                         | 32 / 192x500                      | 8 / 30000                                      |

Zde jsou pokyny pro nastavení správné hodnoty pro **AzureSSISMaxParallelExecutionsPerNode** vlastnost: 

1. Nejprve nastavte malou hodnotu.
2. Zvyšte ji o malou částku a zkontrolujte, zda je celková propustnost vylepšena.
3. Ukončení zvyšování hodnoty, když celková propustnost dosáhne maximální hodnoty.

## <a name="ssisdbpricingtier"></a>SSISDBPricingTier

**SSISDBPricingTier** je cenová úroveň pro databázi katalogu SSIS (SSISDB) v databázi Azure SQL. Toto nastavení ovlivňuje maximální počet pracovníků v instanci infračerveného přenosu, rychlost do fronty spuštění balíčku a rychlost načtení protokolu spuštění.

-   Pokud se nestaráte o rychlost zařadit balíček do fronty a načíst protokol spuštění, můžete zvolit nejnižší cenovou úroveň databáze. Azure SQL Database se základními cenami podporuje 8 pracovníků v instanci modulu runtime integrace.

-   Zvolte výkonnější databázi než Basic, pokud počet pracovníků je větší než 8 nebo počet jádra je více než 50. V opačném případě se databáze stane kritickým bodem instancí integračního běhu a celkový výkon je negativně ovlivněn.

-   Zvolte výkonnější databáze, jako je například s3, pokud je úroveň protokolování nastavena na podrobné. Podle našeho neoficiálního interního testování může cenová úroveň s3 podporovat provádění balíčků SSIS se 2 uzly, paralelními počty 128 a verbose úrovní protokolování.

Můžete také upravit databázovou cenovou úroveň na základě informací o [využití jednotky transakcí databáze](../sql-database/sql-database-what-is-a-dtu.md) (DTU), které jsou k dispozici na webu Azure Portal.

## <a name="design-for-high-performance"></a>Návrh pro vysoký výkon
Navrhování balíčku SSIS pro spuštění v Azure se liší od navrhování balíčku pro místní spuštění. Místo kombinování více nezávislých úloh ve stejném balíčku je rozdělte do několika balíčků pro efektivnější spuštění v infračerveném přenosu služby Azure-SSIS. Vytvořte spuštění balíčku pro každý balíček tak, aby nemuseli čekat na dokončení. Tento přístup těží z škálovatelnosti prostředí integrace Azure-SSIS a zlepšuje celkovou propustnost.

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o prostředí Azure-SSIS Integration Runtime. Viz [Azure-SSIS Integration Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime).
