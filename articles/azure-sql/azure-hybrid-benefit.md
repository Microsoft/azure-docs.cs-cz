---
title: Zvýhodněné hybridní využití Azure
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Pro Azure SQL Database a slevy spravované instance SQL použijte existující licence SQL Server.
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: sqldbrb=4
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake
ms.date: 11/13/2019
ms.openlocfilehash: 186721fbb52ae153bd51076a9fe3a7413f29d1b4
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92479115"
---
# <a name="azure-hybrid-benefit---azure-sql-database--sql-managed-instance"></a>Zvýhodněné hybridní využití Azure-Azure SQL Database & spravované instance SQL
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

V zřízené výpočetní úrovni v rámci nákupního modelu založeném na vCore můžete vyměňovat stávající licence pro zvýhodněné sazby Azure SQL Database a Azure SQL Managed instance pomocí [zvýhodněné hybridní využití Azure](https://azure.microsoft.com/pricing/hybrid-benefit/). Tato výhoda Azure vám umožní ušetřit až 30 procent nebo ještě vyšší na SQL Database & spravované instance SQL pomocí licencí SQL Server se Software Assurance. Stránka [zvýhodněné hybridní využití Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) obsahuje kalkulačku, která vám pomůže určit úspory.  Všimněte si, že Zvýhodněné hybridní využití Azure neplatí pro Azure SQL Database bez serveru.

> [!NOTE]
> Změna na Zvýhodněné hybridní využití Azure nevyžaduje žádné výpadky.

![cenová struktura Vcore](./media/azure-hybrid-benefit/pricing.png)

## <a name="choose-a-license-model"></a>Zvolit model licence

Pomocí Zvýhodněné hybridní využití Azure se můžete rozhodnout platit jenom za základní infrastrukturu Azure, a to pomocí své stávající licence SQL Server pro SQL Server databázového stroje (základní výpočetní prostředky), nebo můžete platit za základní infrastrukturu i pro SQL Serverou licenci (ceny zahrnuté v licenci).

Licenční model můžete vybrat nebo změnit v Azure Portal: 
- Pro nové databáze během vytváření vyberte na kartě **základy** možnost **Konfigurovat databázi** a vyberte možnost úspory peněz.
- V případě existujících databází vyberte v nabídce **Nastavení** položku **Konfigurovat** a vyberte možnost Uložit peníze.

Můžete také nakonfigurovat novou nebo existující databázi pomocí jednoho z následujících rozhraní API:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Chcete-li nastavit nebo aktualizovat typ licence pomocí prostředí PowerShell:

- [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)
- [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)
- [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance)
- [Set-AzSqlInstance](/powershell/module/az.sql/set-azsqlinstance)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Nastavení nebo aktualizace typu licence pomocí Azure CLI:

- [az sql db create](/cli/azure/sql/db#az-sql-db-create)
- [az sql db update](/cli/azure/sql/db#az-sql-db-update)
- [AZ SQL mi Create](/cli/azure/sql/mi#az-sql-mi-create)
- [AZ SQL mi Update](/cli/azure/sql/mi#az-sql-mi-update)

# <a name="rest-api"></a>[REST API](#tab/rest)

Chcete-li nastavit nebo aktualizovat typ licence pomocí REST API:

- [Databáze – vytvořit nebo aktualizovat](/rest/api/sql/databases/createorupdate)
- [Databáze – aktualizace](/rest/api/sql/databases/update)
- [Spravované instance – vytvořit nebo aktualizovat](/rest/api/sql/managedinstances/createorupdate)
- [Spravované instance – aktualizace](/rest/api/sql/managedinstances/update)

* * *


### <a name="azure-hybrid-benefit-questions"></a>Zvýhodněné hybridní využití Azure dotazy

#### <a name="are-there-dual-use-rights-with-azure-hybrid-benefit-for-sql-server"></a>Jsou k dispozici dvě práva pro použití s Zvýhodněné hybridní využití Azure pro SQL Server?

Máte 180 dnů od duálních práv k používání licence, abyste zajistili bezproblémové spouštění migrací. Po uplynutí 180 dne můžete použít jenom licenci SQL Server v cloudu v SQL Database. Už nemusíte mít k dispozici dvojí používání místních i cloudových práv.

#### <a name="how-does-azure-hybrid-benefit-for-sql-server-differ-from-license-mobility"></a>Jak se Zvýhodněné hybridní využití Azure SQL Server liší od mobility licencí?

SQL Server zákazníkům se Software Assurance nabízíme výhody mobility licencí. To umožňuje přeřazení svých licencí na sdílené servery partnera. Tuto výhodu můžete využít v Azure IaaS a AWS EC2.

Zvýhodněné hybridní využití Azure pro SQL Server se liší od mobility licencí ve dvou klíčových oblastech:

- Poskytuje ekonomické výhody pro přesun vysoce virtualizovaných úloh do Azure. Zákazníci edice SQL Server Enterprise můžou získat čtyři jádra v Azure v rámci Pro obecné účely SKU pro každé jádro, které vlastní místní pro vysoce virtualizované aplikace. Mobilita licencí neumožňuje žádné speciální náklady na přesun virtualizovaných úloh do cloudu.
- Poskytuje pro PaaS cíl v Azure (spravovaná instance SQL), která je vysoce kompatibilní s SQL Server.

#### <a name="what-are-the-specific-rights-of-the-azure-hybrid-benefit-for-sql-server"></a>Jaká jsou specifická práva Zvýhodněné hybridní využití Azure pro SQL Server?

Zákazníci SQL Database mají pro SQL Server k Zvýhodněné hybridní využití Azure přidružená tato práva:

|Nároky na licenci|K čemu Zvýhodněné hybridní využití Azure SQL Server získat přístup?|
|---|---|
|Zákazníci se základními edicemi SQL Server Enterprise s SA|<li>Může platit základní sazba pro Pro obecné účely nebo Pro důležité obchodní informace SKLADOVOU položku.</li><br><li>1 jádro v místním prostředí = 4 jádra v Pro obecné účely SKU</li><br><li>1 jádro v místním prostředí = 1 jádro v Pro důležité obchodní informace SKU</li>|
|Zákazníci se základními edicemi SQL Server Standard s SA|<li>Může platit základní sazba jenom pro Pro obecné účely SKU.</li><br><li>1 jádro v místním prostředí = 1 jádro v Pro obecné účely SKU</li>|
|||


## <a name="next-steps"></a>Další kroky

- Nápovědu k výběru možnosti nasazení Azure SQL najdete v tématu [Volba správné možnosti nasazení v Azure SQL](azure-sql-iaas-vs-paas-what-is-overview.md).
- Porovnání funkcí SQL Database a SQL Managed instance najdete v tématu [SQL Database & funkce spravované instance SQL](database/features-comparison.md).
