---
title: Správa Azure Analysis Services s využitím PowerShellu | Microsoft Docs
description: Popisuje Azure Analysis Services rutiny prostředí PowerShell pro běžné úlohy správy, například vytváření serverů, pozastavení operací nebo změna úrovně služby.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 28f414c5eaaea7b987f2c3694cb8fc73b70838e9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92018759"
---
# <a name="manage-azure-analysis-services-with-powershell"></a>Správa služby Azure Analysis Services pomocí PowerShellu

Tento článek popisuje rutiny prostředí PowerShell, které se používají k provádění úloh správy Azure Analysis Services serveru a databáze. 

Úlohy správy prostředků serveru, jako je vytvoření nebo odstranění serveru, pozastavení nebo obnovení operací serveru nebo změna úrovně služby (vrstvy) pomocí rutin Azure Analysis Services. Další úlohy týkající se správy databází, jako je přidání nebo odebrání členů role, zpracování nebo dělení, obsahují rutiny, které jsou součástí stejného modulu SqlServer jako Služba Analysis Services serveru SQL.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="permissions"></a>Oprávnění

Většina úloh prostředí PowerShell vyžaduje oprávnění správce na spravovaném serveru Analysis Services. Naplánované úlohy PowerShellu jsou bezobslužné operace. Účet nebo instanční objekt, na kterém běží Plánovač, musí mít na serveru Analysis Services oprávnění správce. 

Pro operace se serverem, které používají rutiny Azure PowerShell, musí účet nebo účet spouštějící Plánovač taky patřit do role vlastníka pro prostředek v [řízení přístupu na základě role Azure (Azure RBAC)](../role-based-access-control/overview.md). 

## <a name="resource-and-server-operations"></a>Operace s prostředky a servery 

Instalace modulu – [AZ. AnalysisServices](https://www.powershellgallery.com/packages/Az.AnalysisServices)   
Dokumentace – [reference AZ. AnalysisServices](/powershell/module/az.analysisservices)

## <a name="database-operations"></a>Databázové operace

Operace Azure Analysis Services Database používají stejný modul SqlServer jako Služba Analysis Services serveru SQL. Některé rutiny ale nejsou podporované pro Azure Analysis Services. 

Modul SqlServer poskytuje rutiny pro správu databáze specifické pro úlohu a také rutinu pro obecné účely Invoke-ASCmd, která přijímá dotaz nebo skript TMSL (Tabular model Scripting Language). Pro Azure Analysis Services jsou podporovány následující rutiny v modulu SqlServer.

Nainstalovat modul – [SQLServer](https://www.powershellgallery.com/packages/SqlServer)   
Dokumentace – [Reference k SQLServer](/powershell/module/sqlserver)

### <a name="supported-cmdlets"></a>Podporované rutiny

|Rutina|Popis|
|------------|-----------------| 
|[Add-RoleMember](/powershell/module/sqlserver/Add-RoleMember)|Přidejte člena do databázové role.| 
|[Backup-ASDatabase](/powershell/module/sqlserver/backup-asdatabase)|Zálohuje databázi Analysis Services.|  
|[Remove-RoleMember](/powershell/module/sqlserver/remove-rolemember)|Odebere člena z databázové role.|   
|[Invoke – ASCmd](/powershell/module/sqlserver/invoke-ascmd)|Spusťte skript TMSL.|
|[Invoke – ProcessASDatabase](/powershell/module/sqlserver/invoke-processasdatabase)|Zpracování databáze.|  
|[Invoke – ProcessPartition](/powershell/module/sqlserver/invoke-processpartition)|Zpracuje oddíl.| 
|[Invoke – procesní](/powershell/module/sqlserver/invoke-processtable)|Zpracuje tabulku.|  
|[Sloučení – oddíl](/powershell/module/sqlserver/merge-partition)|Sloučí oddíl.|  
|[Restore-ASDatabase](/powershell/module/sqlserver/restore-asdatabase)|Obnovení databáze Analysis Services.| 
  

## <a name="related-information"></a>Související informace

* [SQL Server PowerShell](/sql/powershell/sql-server-powershell)      
* [Stáhnout SQL Server modul PowerShellu](/sql/ssms/download-sql-server-ps-module)   
* [Stáhnout SSMS](/sql/ssms/download-sql-server-management-studio-ssms)   
* [Modul SqlServer v Galerie prostředí PowerShell](https://www.powershellgallery.com/packages/SqlServer)    
* [Programování tabulkových modelů pro úroveň kompatibility 1200 a vyšší](/analysis-services/tabular-model-programming-compatibility-level-1200/tabular-model-programming-for-compatibility-level-1200)