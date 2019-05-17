---
title: Fondu elastické databáze Azure SQL příklad přesunutí prostředí PowerShell | Dokumentace Microsoftu
description: Ukázkový skript Azure PowerShellu pro přesun databáze SQL mezi elastickými fondy pomocí PowerShellu
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: stevestein
ms.reviewer: ''
ms.author: sstein
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: cd31d11dad0b244cf5be53c00e6426d7f2bbab26
ms.sourcegitcommit: 3ced637c8f1f24256dd6ac8e180fff62a444b03c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2019
ms.locfileid: "65834498"
---
# <a name="use-powershell-to-create-elastic-pools-and-move-databases-between-elastic-pools"></a>Vytvoření elastických fondů a přesun databází mezi elastickými fondy pomocí PowerShellu

Tento ukázkový skript PowerShellu vytvoří dva elastické fondy, přesune databázi z jednoho elastického fondu do druhého elastického fondu a pak přesune databázi z elastického fondu na velikost výpočetních prostředků izolované databáze.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Pokud se rozhodnete nainstalovat a používat PowerShell místně, v tomto kurzu vyžaduje AZ PowerShell 1.4.0 nebo novější. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzAccount` pro vytvoření připojení k Azure.

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/move-database-between-pools-and-standalone/move-database-between-pools-and-standalone.ps1?highlight=18-19 "Move database between pools")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení

Použijte následující příkaz k odebrání skupiny prostředků a všechny prostředky, které s ním spojená.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Vytvoří server služby SQL Database, který je hostitelem jedné databáze nebo elastického fondu. |
| [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | Vytvoří elastický fond. |
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Vytvoří databázi na serveru SQL Database jako samostatný nebo databázi ve fondu. |
| [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) | Aktualizuje vlastnosti databáze nebo přesune databázi do nebo z elastického fondu nebo mezi elastickými fondy. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |
|||

## <a name="next-steps"></a>Další postup

Další informace o Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/overview).

Další ukázkové skripty PowerShellu pro službu SQL Database najdete v tématu [Skripty PowerShellu pro službu Azure SQL Database](../sql-database-powershell-samples.md).
