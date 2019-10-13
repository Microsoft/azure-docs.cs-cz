---
title: Správa Azure Analysis Services s využitím PowerShellu | Microsoft Docs
description: Správa Azure Analysis Services pomocí prostředí PowerShell.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: reference
ms.date: 07/01/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: bfe1bfc2828347e34fa92564c26d005998e14ece
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2019
ms.locfileid: "72294734"
---
# <a name="manage-azure-analysis-services-with-powershell"></a>Správa Azure Analysis Services s využitím PowerShellu

Tento článek popisuje rutiny prostředí PowerShell, které se používají k provádění úloh správy Azure Analysis Services serveru a databáze. 

Úlohy správy prostředků serveru, jako je vytvoření nebo odstranění serveru, pozastavení nebo obnovení operací serveru nebo změna úrovně služby (vrstvy) pomocí rutin Azure Analysis Services. Další úlohy týkající se správy databází, jako je přidání nebo odebrání členů role, zpracování nebo dělení, obsahují rutiny, které jsou součástí stejného modulu SqlServer jako SQL Server Analysis Services.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="permissions"></a>Oprávnění

Většina úloh prostředí PowerShell vyžaduje oprávnění správce na spravovaném serveru Analysis Services. Naplánované úlohy PowerShellu jsou bezobslužné operace. Účet nebo instanční objekt, na kterém běží Plánovač, musí mít na serveru Analysis Services oprávnění správce. 

Pro operace se serverem, které používají rutiny Azure PowerShell, musí účet nebo účet spouštějící Plánovač také patřit do role vlastníka pro prostředek v [Access Control na základě role (RBAC) založené na rolích Azure](../role-based-access-control/overview.md). 

## <a name="resource-and-server-operations"></a>Operace s prostředky a servery 

Instalace modulu – [AZ. AnalysisServices](https://www.powershellgallery.com/packages/Az.AnalysisServices)   
Dokumentace – [reference AZ. AnalysisServices](/powershell/module/az.analysisservices)

## <a name="database-operations"></a>Databázové operace

Operace Azure Analysis Services Database používají stejný modul SqlServer jako SQL Server Analysis Services. Některé rutiny ale nejsou podporované pro Azure Analysis Services. 

Modul SqlServer poskytuje rutiny pro správu databáze specifické pro úlohu a také rutinu Invoke ASCmd pro obecné účely, která přijímá dotaz nebo skript TMSL (Tabular model Scripting Language). Pro Azure Analysis Services jsou podporovány následující rutiny v modulu SqlServer.

Instalace modulu – [SqlServer](https://www.powershellgallery.com/packages/SqlServer)   
Dokumentace – [Reference k SQLServer](/powershell/module/sqlserver)

### <a name="supported-cmdlets"></a>Podporované rutiny

|Rutina|Popis|
|------------|-----------------| 
|[Add-RoleMember](https://docs.microsoft.com/powershell/module/sqlserver/Add-RoleMember)|Přidejte člena do databázové role.| 
|[Backup – ASDatabase](https://docs.microsoft.com/powershell/module/sqlserver/backup-asdatabase)|Zálohuje databázi Analysis Services.|  
|[Remove-RoleMember](https://docs.microsoft.com/powershell/module/sqlserver/remove-rolemember)|Odebere člena z databázové role.|   
|[Invoke – ASCmd](https://docs.microsoft.com/powershell/module/sqlserver/invoke-ascmd)|Spusťte skript TMSL.|
|[Invoke – ProcessASDatabase](https://docs.microsoft.com/powershell/module/sqlserver/invoke-processasdatabase)|Zpracování databáze.|  
|[Invoke – ProcessPartition](https://docs.microsoft.com/powershell/module/sqlserver/invoke-processpartition)|Zpracuje oddíl.| 
|[Invoke – procesní](https://docs.microsoft.com/powershell/module/sqlserver/invoke-processtable)|Zpracuje tabulku.|  
|[Sloučení – oddíl](https://docs.microsoft.com/powershell/module/sqlserver/merge-partition)|Sloučí oddíl.|  
|[Obnovit – ASDatabase](https://docs.microsoft.com/powershell/module/sqlserver/restore-asdatabase)|Obnovení databáze Analysis Services.| 
  

## <a name="related-information"></a>Související informace

* [SQL Server PowerShell](https://docs.microsoft.com/sql/powershell/sql-server-powershell)      
* [Stáhnout SQL Server modul PowerShellu](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [Stáhnout SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   
* [Modul SqlServer v Galerie prostředí PowerShell](https://www.powershellgallery.com/packages/SqlServer)    
* [Programování tabulkových modelů pro úroveň kompatibility 1200 a vyšší](https://docs.microsoft.com/analysis-services/tabular-model-programming-compatibility-level-1200/tabular-model-programming-for-compatibility-level-1200)
