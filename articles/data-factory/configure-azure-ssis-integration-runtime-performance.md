---
title: Konfigurace výkonu prostředí Azure-SSIS Integration runtime | Dokumentace Microsoftu
description: Další informace o konfiguraci vlastností prostředí Azure-SSIS Integration Runtime pro vysoký výkon
services: data-factory
ms.date: 01/10/2018
ms.topic: conceptual
ms.service: data-factory
ms.workload: data-services
author: swinarko
ms.author: sawinark
ms.reviewer: ''
manager: craigg
ms.openlocfilehash: 42c69653a002446552da998320a43730dfdaadf5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65232525"
---
# <a name="configure-the-azure-ssis-integration-runtime-for-high-performance"></a>Konfigurace prostředí Azure-SSIS Integration Runtime pro vysoký výkon

Tento článek popisuje, jak nakonfigurovat Azure-SSIS Integration Runtime (IR) pro vysoce výkonné. Prostředí Azure-SSIS IR umožňuje nasazení a spouštění balíčků SQL Server Integration Services (SSIS) v Azure. Další informace o Azure-SSIS IR najdete v tématu [prostředí Integration runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime) článku. Informace o nasazování a spouštění balíčků služby SSIS v Azure najdete v tématu [Lift and shift úloh SQL Server Integration Services do cloudu](/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview).

> [!IMPORTANT]
> Tento článek obsahuje výsledky výkonu a připomínek interní testování provádí členové vývojového týmu prostředí SSIS. Vaše výsledky se mohou lišit. Proveďte vlastní testování před další změny nastavení konfigurace, které ovlivňují náklady a výkon.

## <a name="properties-to-configure"></a>Vlastnosti konfigurace

Následující část skript konfigurace jsou uvedeny vlastnosti, které můžete nakonfigurovat při vytváření prostředí Azure-SSIS Integration Runtime. Celý skript prostředí PowerShell a popis najdete v tématu [balíčků nasazení SQL Server Integration Services do Azure](tutorial-deploy-ssis-packages-azure-powershell.md).

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

## <a name="azuressislocation"></a>AzureSSISLocation
**AzureSSISLocation** je umístění pro pracovníka uzel integration runtime. Pracovní uzel udržuje stálé připojení k databázi katalogu služby SSIS (SSISDB) ve službě Azure SQL database. Nastavte **AzureSSISLocation** do stejného umístění jako databáze SQL serveru, který je hostitelem databáze SSISDB, což umožňuje prostředí integration runtime jako efektivní práce.

## <a name="azuressisnodesize"></a>AzureSSISNodeSize
Data Factory, včetně Azure-SSIS IR podporuje následující možnosti:
-   Standard\_A4\_v2
-   Standard\_A8\_v2
-   Standard\_D1\_v2
-   Standard\_D2\_v2
-   Standard\_D3\_v2
-   Standard\_D4\_v2
-   Standard\_D2\_v3
-   Standard\_D4\_v3
-   Standard\_D8\_v3
-   Standard\_D16\_v3
-   Standard\_D32\_v3
-   Standard\_D64\_v3
-   Standard\_E2\_v3
-   Standard\_E4\_v3
-   Standard\_E8\_v3
-   Standard\_E16\_v3
-   Standard\_E32\_v3
-   Standard\_E64\_v3

V neoficiální interní testování pomocí služby SSIS technický tým, řady D series pravděpodobně vhodnější pro spouštění balíčku služby SSIS než řady A series.

-   Výkon a cenu poměr řady D series je vyšší než řady A series a výkon a cenu poměr řady v3 je vyšší než dv2 series.
-   Propustnost pro řady D series je vyšší než řada A za stejnou cenu a propustnosti pro řadu v3 je vyšší než dv2 series za stejnou cenu.
-   Uzly řady v2 prostředí Azure-SSIS IR nejsou vhodné pro vlastní nastavení, takže místo toho použijte uzly řady v3. Pokud už používáte uzly řady v2, přepněte na co nejdříve použít uzly řady v3.
-   Řada E je paměťově optimalizované velikosti virtuálních počítačů, které poskytuje vyšší poměr paměti k procesoru než jiné počítače. Pokud balíček vyžaduje velké množství paměti, můžete zvážit, vyberete virtuální počítače E-series.

### <a name="configure-for-execution-speed"></a>Konfigurace pro rychlost provádění
Pokud nemáte mnoho balíčky ke spuštění a chcete, aby balíčky pro rychlé spouštění, použijte informace v následující tabulce vybrat typ virtuálního počítače, který je vhodný pro váš scénář.

Tato data představuje jeden balíček provádění na jeden pracovního uzlu. Balíček načte 3 miliony záznamů s názvem prvního a posledního sloupce z Azure Blob Storage vygeneruje úplný název sloupce a zapíše záznamy, které mají příjmení delší než 20 znaků do Azure Blob Storage.

![Rychlost provádění balíčků služby SSIS Integration Runtime](media/configure-azure-ssis-integration-runtime-performance/ssisir-execution-speedV2.png)

### <a name="configure-for-overall-throughput"></a>Konfigurace pro celkovou propustnost

Pokud máte velké balíčky ke spuštění a vám nejvíce jde o celkovou propustnost, vyberte typ virtuálního počítače, který je vhodný pro váš scénář pomocí informací v následující tabulce.

![Maximální celkovou propustnost služby SSIS Integration Runtime](media/configure-azure-ssis-integration-runtime-performance/ssisir-overall-throughputV2.png)

## <a name="azuressisnodenumber"></a>AzureSSISNodeNumber

**AzureSSISNodeNumber** upraví škálovatelnost prostředí integration runtime. Propustnost modulu runtime integrace je přímo úměrný **AzureSSISNodeNumber**. Nastavte **AzureSSISNodeNumber** malou hodnotu při prvním sledovat propustnost prostředí integration runtime a pak upravte hodnotu pro váš scénář. Změna konfigurace počet uzlů pracovního procesu, naleznete v tématu [správě prostředí Azure-SSIS integration runtime](manage-azure-ssis-integration-runtime.md).

## <a name="azuressismaxparallelexecutionspernode"></a>AzureSSISMaxParallelExecutionsPerNode

Pokud už používáte výkonné pracovního uzlu ke spouštění balíčků, zvýšení **AzureSSISMaxParallelExecutionsPerNode** může zvýšit celkovou propustnost prostředí integration runtime. 1 – 4 paralelních úloh na uzlu Standard_D1_v2 uzly, jsou podporovány. Pro všechny ostatní typy uzlů se podporují 1 max(2 x number of cores, 8) paralelních úloh na uzlu. Chcete-li **AzureSSISMaxParallelExecutionsPerNode** mimo maximální hodnota podporujeme, můžete otevřít lístek podpory a můžeme zvýšit maximální hodnota pro vás a po, který je třeba aktualizovat pomocí prostředí Azure Powershell  **AzureSSISMaxParallelExecutionsPerNode**.
Chcete-li odhadnout příslušnou hodnotu na základě nákladů balíčku a následujících konfigurací pracovních uzlů. Další informace najdete v tématu [velikostí virtuálních počítačů pro obecné účely](../virtual-machines/windows/sizes-general.md).

| Velikost             | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Maximální propustnost dočasného úložiště: IOPS / čtení v MB/s / zápis v MB/s | Maximální počet datových disků nebo propustnost: IOPS | Max. počet síťových karet / Očekávaný výkon sítě (Mb/s) |
|------------------|------|-------------|------------------------|------------------------------------------------------------|-----------------------------------|------------------------------------------------|
| Standard\_D1\_v2 | 1    | 3,5         | 50                     | 3000 / 46 / 23                                             | 2 / 2×500                         | 2 / 750                                        |
| Standard\_D2\_v2 | 2    | 7           | 100                    | 6000 / 93 / 46                                             | 4 / 4×500                         | 2 / 1 500                                       |
| Standard\_D3\_v2 | 4    | 14          | 200                    | 12000 / 187 / 93                                           | 8 / 8×500                         | 4 / 3 000                                       |
| Standard\_D4\_v2 | 8    | 28          | 400                    | 24000 / 375 / 187                                          | 16 / 16×500                       | 8 / 6 000                                       |
| Standard\_A4\_v2 | 4    | 8           | 40                     | 4000 / 80 / 40                                             | 8 / 8×500                         | 4 / 1 000                                       |
| Standard\_A8\_v2 | 8    | 16          | 80                     | 8000 / 160 / 80                                            | 16 / 16×500                       | 8 / 2 000                                       |
| Standard\_D2\_v3 | 2    | 8           | 50                     | 3000 / 46 / 23                                             | 4 / 6 x 500                         | 2 / 1 000                                       |
| Standard\_D4\_v3 | 4    | 16          | 100                    | 6000 / 93 / 46                                             | 8 / 12x500                        | 2 / 2 000                                       |
| Standard\_D8\_v3 | 8    | 32          | 200                    | 12000 / 187 / 93                                           | 16 / 24 x 500                       | 4 / 4000                                       |
| Standard\_D16\_v3| 16   | 64          | 400                    | 24000 / 375 / 187                                          | 32 / 48 x 500                        | 8 / 8000                                       |
| Standard\_D32\_v3| 32   | 128         | 800                    | 48000 / 750 / 375                                          | 32 / 96 x 500                       | 8 / 16 000                                      |
| Standard\_D64\_v3| 64   | 256         | 1600                   | 96000 / 1000 / 500                                         | 32 / 192 x 500                      | 8 / 30000                                      |
| Standard\_E2\_v3 | 2    | 16          | 50                     | 3000 / 46 / 23                                             | 4 / 6 x 500                         | 2 / 1 000                                       |
| Standard\_E4\_v3 | 4    | 32          | 100                    | 6000 / 93 / 46                                             | 8 / 12x500                        | 2 / 2 000                                       |
| Standard\_E8\_v3 | 8    | 64          | 200                    | 12000 / 187 / 93                                           | 16 / 24 x 500                       | 4 / 4000                                       |
| Standard\_E16\_v3| 16   | 128         | 400                    | 24000 / 375 / 187                                          | 32 / 48x500                       | 8 / 8000                                       |
| Standard\_E32\_v3| 32   | 256         | 800                    | 48000 / 750 / 375                                          | 32 / 96 x 500                       | 8 / 16 000                                      |
| Standard\_E64\_v3| 64   | 432         | 1600                   | 96000 / 1000 / 500                                         | 32 / 192 x 500                      | 8 / 30000                                      |

Tady jsou pokyny pro nastavení správné hodnoty pro **AzureSSISMaxParallelExecutionsPerNode** vlastnost: 

1. Nastavte malou hodnotu při prvním.
2. Zvýšit o malou část ke kontrole, jestli je lepší celkovou propustnost.
3. Zastavte, pokud celková propustnost nedosáhne maximální hodnoty zvýšit hodnotu.

## <a name="ssisdbpricingtier"></a>SSISDBPricingTier

**SSISDBPricingTier** je cenovou úroveň pro databázi katalogu služby SSIS (SSISDB) ve službě Azure SQL database. Toto nastavení má vliv na maximální počet pracovních procesů v instanci reakcí na Incidenty, rychlost do fronty spouštění balíčku a rychlost pro načtení protokolu spuštění.

-   Pokud vám nezáleží na rychlost spouštění balíčku fronty a pro načtení protokolu spuštění, můžete databázi nejnižší cenovou úroveň. Základní ceny za Azure SQL Database podporuje 8 pracovních procesů v instanci modulu runtime integrace.

-   Zvolte databázi výkonnější než základní, pokud počet pracovních procesů je víc než 8, nebo počet jader je více než 50. Jinak databáze se stane kritickým bodem instance modulu runtime integrace a je celkový výkon negativně ovlivněn.

-   Pokud úroveň protokolování je nastavená na podrobné, zvolte výkonnější databáze, jako je například s3. Podle našich neoficiální interní testování, cenovou úroveň s3 podporují spouštění balíčku služby SSIS se 2 uzly, 128 paralelní počty a úroveň podrobné protokolování.

Můžete také upravit databázi cenovou úroveň na základě [jednotky transakcí databáze](../sql-database/sql-database-what-is-a-dtu.md) (DTU) informace o využití na portálu Azure portal.

## <a name="design-for-high-performance"></a>Návrh pro vysoký výkon
Navrhování balíčku SSIS v Azure se liší od návrhu balíčku pro místní spuštění. Místo kombinování více nezávislých úloh, které ve stejném balíčku, oddělte je do několika balíčků pro efektivnější spouštění v prostředí Azure-SSIS IR. Spouštění balíčků pro každý balíček, vytvořte tak, aby si uživatelé nebudou muset čekat na jiných na dokončení. Tento přístup těží z prostředí Azure-SSIS integration runtime škálovatelnost a zlepšuje celkovou propustnost.

## <a name="next-steps"></a>Další postup
Další informace o Azure-SSIS Integration Runtime. Zobrazit [prostředí Azure-SSIS Integration Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime).
