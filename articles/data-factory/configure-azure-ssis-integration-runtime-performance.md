---
title: Konfigurace modulu Runtime Azure SSIS integrace pro vysoký výkon | Microsoft Docs
description: Zjistěte, jak konfigurovat vlastnosti Runtime integrace Azure SSIS pro vysoký výkon
services: data-factory
ms.date: 01/10/2018
ms.topic: article
ms.service: data-factory
ms.workload: data-services
author: douglaslMS
ms.author: douglasl
manager: craigg
ms.openlocfilehash: 3c19ebc601136653a88ca8d2a3073d897af863f9
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="configure-the-azure-ssis-integration-runtime-for-high-performance"></a>Konfigurace modulu Runtime Azure SSIS integrace pro vysoký výkon

Tento článek popisuje postup konfigurace služby Azure SSIS integrace modulu Runtime (IR) pro vysoký výkon. IR Azure SSIS umožňuje nasadit a spustit balíčky integrační služby SSIS (SQL Server) v Azure. Další informace o Azure SSIS IR najdete v tématu [integrace runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime) článku. Informace o nasazení a spouštění balíčků SSIS v Azure najdete v tématu [navýšení a shift SQL Server Integration Services úloh na cloud](/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview).

> [!IMPORTANT]
> Tento článek obsahuje výsledky a připomínky z interní testování provádí členové týmu pro vývoj služby SSIS. Výsledky se může lišit. Proveďte vlastní testování před dokončení nastavení konfigurace, které ovlivňují nákladů a výkonu.

## <a name="properties-to-configure"></a>Vlastnosti konfigurace

Následující část konfigurační skript zobrazuje vlastnosti, které můžete nakonfigurovat při vytváření modulu Runtime integrace Azure SSIS. Dokončení skriptu prostředí PowerShell a popis najdete v tématu [balíčky nasazení SQL Server Integration Services do Azure](tutorial-deploy-ssis-packages-azure-powershell.md).

```powershell
$SubscriptionName = "<Azure subscription name>"
$ResourceGroupName = "<Azure resource group name>"
# Data factory name. Must be globally unique
$DataFactoryName = "<Data factory name>" 
$DataFactoryLocation = "EastUS" 

# Azure-SSIS integration runtime information. This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "<Specify a name for your Azure-SSIS IR>"
$AzureSSISDescription = "<Specify description for your Azure-SSIS IR"
# In public preview, only EastUS, NorthEurope, and WestEurope are supported.
$AzureSSISLocation = "EastUS" 
# In public preview, only Standard_A4_v2, Standard_A8_v2, Standard_D1_v2, Standard_D2_v2, Standard_D3_v2, Standard_D4_v2 are supported
$AzureSSISNodeSize = "Standard_D3_v2"
# In public preview, only 1-10 nodes are supported.
$AzureSSISNodeNumber = 2 
# For a Standard_D1_v2 node, 1-4 parallel executions per node are supported. For other nodes, it's 1-8.
$AzureSSISMaxParallelExecutionsPerNode = 2 

# SSISDB info
$SSISDBServerEndpoint = "<Azure SQL server name>.database.windows.net"
$SSISDBServerAdminUserName = "<Azure SQL server - user name>"
$SSISDBServerAdminPassword = "<Azure SQL server - user password>"
# Remove the SSISDBPricingTier variable if you are using Azure SQL Managed Instance (private preview)
# This parameter applies only to Azure SQL Database. For the basic pricing tier, specify "Basic", not "B". For standard tiers, specify "S0", "S1", "S2", 'S3", etc.
$SSISDBPricingTier = "<pricing tier of your Azure SQL server. Examples: Basic, S0, S1, S2, S3, etc.>"
```

## <a name="azuressislocation"></a>AzureSSISLocation
**AzureSSISLocation** je umístění integrace modulu runtime pracovního uzlu. Pracovního uzlu udržuje konstantní připojení k databázi katalogu služby SSIS (SSISDB) na Azure SQL database. Nastavte **AzureSSISLocation** do stejného umístění jako databázového SQL serveru, který je hostitelem SSISDB, které umožní integrace modulu runtime jako efektivní práci.

## <a name="azuressisnodesize"></a>AzureSSISNodeSize
Verzi public preview služby Azure Data Factory v2, včetně Azure SSIS IR, podporuje následující možnosti:
-   Standardní\_A4\_v2
-   Standard\_A8\_v2
-   Standardní\_D1\_v2
-   Standardní\_D2\_v2
-   Standardní\_D3\_v2
-   Standardní\_D4\_v2.

V neoficiální interní testování technický tým služby SSIS, řady D pravděpodobně vhodnější pro spouštění balíčku služby SSIS než A řady.

-   Poměr výkonu nebo cena série D je vyšší než A řady.
-   Propustnost pro řady D je vyšší než řady A za stejnou cenu.

### <a name="configure-for-execution-speed"></a>Konfigurace pro rychlost provádění
Pokud nemáte mnoho balíčky ke spuštění a mají rychlé spuštění balíčky, použijte informace v následující tabulce vybrat vhodnou pro váš scénář typu virtuálního počítače.

Tato data představuje jeden balíček spuštění na jedné pracovního uzlu. Balíček načte 10 milionů záznamy s názvem první a poslední název sloupce z Azure Blob Storage, generuje úplný název sloupce a zapíše záznamy, které mají úplný název, který je delší než 20 znaků do úložiště objektů Blob Azure.

![Rychlost provádění balíčku služby SSIS integrace Runtime](media/configure-azure-ssis-integration-runtime-performance/ssisir-execution-speed.png)

### <a name="configure-for-overall-throughput"></a>Konfigurace pro celkovou propustnost

Pokud máte velké balíčky ke spuštění a vám nejvíc záleží celkovou propustnost, použijte informace v následující tabulce vybrat vhodnou pro váš scénář typu virtuálního počítače.

![Maximální celková propustnost Runtime integrační služby SSIS](media/configure-azure-ssis-integration-runtime-performance/ssisir-overall-throughput.png)

## <a name="azuressisnodenumber"></a>AzureSSISNodeNumber

**AzureSSISNodeNumber** upraví škálovatelnost integrace modulu runtime. Propustnost běhového modulu integrace je úměrná **AzureSSISNodeNumber**. Nastavte **AzureSSISNodeNumber** na malou hodnotu nejprve monitorování propustnost integrace modulu runtime a pak upravte hodnotu pro váš scénář. Chcete-li překonfigurovat počet uzlů pracovního procesu, přečtěte si téma [spravovat modulu runtime integrace Azure SSIS](manage-azure-ssis-integration-runtime.md).

## <a name="azuressismaxparallelexecutionspernode"></a>AzureSSISMaxParallelExecutionsPerNode

Pokud už používáte výkonné pracovního uzlu ke spuštění balíčky, zvýšení **AzureSSISMaxParallelExecutionsPerNode** může zvýšit celkovou propustnost integrace modulu runtime. Paralelní spuštění 1 – 4 na uzel Standard_D1_v2 uzly jsou podporovány. Pro všechny ostatní typy uzlů jsou podporovány paralelní spuštěních 1 – 8 na každém uzlu.
Chcete-li odhadnout odpovídající hodnotu na základě nákladů vašeho balíčku a následující konfigurace pro uzly pracovního procesu. Další informace najdete v tématu [velikostí virtuálních počítačů pro obecné účely](../virtual-machines/windows/sizes-general.md).

| Velikost             | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Maximální propustnost dočasného úložiště: IOPS / čtení v MB/s / zápis v MB/s | Maximální propustnost datových disků: IOPS | Max. počet síťových karet / Očekávaný výkon sítě (Mb/s) |
|------------------|------|-------------|------------------------|------------------------------------------------------------|-----------------------------------|------------------------------------------------|
| Standardní\_D1\_v2 | 1    | 3,5         | 50                     | 3000 / 46 / 23                                             | 2 / 2×500                         | 2 / 750                                        |
| Standardní\_D2\_v2 | 2    | 7           | 100                    | 6000 / 93 / 46                                             | 4 / 4×500                         | 2 / 1 500                                       |
| Standardní\_D3\_v2 | 4    | 14          | 200                    | 12000 / 187 / 93                                           | 8 / 8×500                         | 4 / 3 000                                       |
| Standardní\_D4\_v2 | 8    | 28          | 400                    | 24000 / 375 / 187                                          | 16 / 16×500                       | 8 / 6 000                                       |
| Standardní\_A4\_v2 | 4    | 8           | 40                     | 4000 / 80 / 40                                             | 8 / 8×500                         | 4 / 1 000                                       |
| Standard\_A8\_v2 | 8    | 16          | 80                     | 8000 / 160 / 80                                            | 16 / 16×500                       | 8 / 2 000                                       |

Tady najdete pokyny pro nastavení správné hodnoty **AzureSSISMaxParallelExecutionsPerNode** vlastnost: 

1. Nastavte na hodnotu malou na první.
2. Zvýšit ji malou zkontrolujte, zda je vyšší, celkovou propustnost.
3. Zastavte, zvýšení hodnoty, když celkovou propustnost dosáhne maximální hodnota.

## <a name="ssisdbpricingtier"></a>SSISDBPricingTier

**SSISDBPricingTier** je cenovou úroveň pro databázi katalogu služby SSIS (SSISDB) na Azure SQL database. Toto nastavení ovlivňuje maximální počet pracovních procesů v instanci reakcí na Incidenty, rychlosti do fronty spouštění balíčku a rychlosti načíst protokolu spuštění.

-   Pokud vám nezáleží rychlosti fronty balíček spouštění a načtení protokolu spuštění, můžete vybrat nejnižší cenová úroveň databáze. Databáze SQL Azure s základní ceny podporuje 8 pracovních procesů v instanci integrace modulu runtime.

-   Zvolte výkonnější databáze než základní, pokud počet pracovního procesu je větší než 8, nebo počet jader je více než 50. V opačném případě problémové místo integrace modulu runtime instance se změní na databázi a je mít negativní dopad na celkový výkon.

Můžete také upravit databázi cenová úroveň na základě [jednotky transakcí databáze](../sql-database/sql-database-what-is-a-dtu.md) (DTU) informace o využití dostupné na portálu Azure.

## <a name="design-for-high-performance"></a>Návrh pro vysoký výkon
Navrhování balíčku služby SSIS ke spuštění na Azure se liší od návrhu balíček pro místní spuštění. Místo kombinace více nezávislých úloh ve stejném balíčku, oddělte je do více balíčků pro více efektivní provádění v infračerveného signálu Azure SSIS. Vytvořte spouštění balíčku pro každý balíček, takže není nutné čekat na Další ukončíte. Tento přístup výhody z škálovatelnost runtime integrace Azure SSIS a zlepšuje celkovou propustnost.

## <a name="next-steps"></a>Další postup
Další informace o běhu integrace Azure SSIS. V tématu [Runtime integrace Azure SSIS](concepts-integration-runtime.md#azure-ssis-integration-runtime).