---
title: Správa služby Azure Analysis Services pomocí PowerShellu | Dokumenty společnosti Microsoft
description: Popisuje rutiny prostředí PowerShell služby Azure Analysis Services pro běžné úlohy správy, jako je vytváření serverů, pozastavení operací nebo změna úrovně služeb.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: reference
ms.date: 10/28/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 2c8f4c0541d97a189087af692658cfe794eaaf7e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73572693"
---
# <a name="manage-azure-analysis-services-with-powershell"></a>Správa služby Azure Analysis Services pomocí PowerShellu

Tento článek popisuje rutiny prostředí PowerShell, které se používají k provádění úloh serveru Azure Analysis Services a správy databází. 

Úlohy správy prostředků serveru, jako je vytvoření nebo odstranění serveru, pozastavení nebo obnovení operací serveru nebo změna úrovně služby (vrstvy) používají rutiny služby Azure Analysis Services. Další úkoly pro správu databází, jako je přidávání nebo odebírání členů rolí, zpracování nebo dělení používají rutiny zahrnuté ve stejném modulu SqlServer jako sql server analysis services.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="permissions"></a>Oprávnění

Většina úloh prostředí PowerShell vyžaduje, abyste měli oprávnění správce na serveru Analysis Services, který spravujete. Naplánované úlohy prostředí PowerShell jsou bezobslužné operace. Účet nebo instanční objekt se spuštěným plánovačem musí mít oprávnění správce na serveru Analysis Services. 

Pro serverové operace pomocí rutin Azure PowerShell musí váš účet nebo plánovač spuštěného účtu patřit také do role Vlastník pro prostředek v [řízení přístupu na základě rolí Azure (RBAC).](../role-based-access-control/overview.md) 

## <a name="resource-and-server-operations"></a>Operace prostředků a serveru 

Instalační modul - [Az.AnalysisServices](https://www.powershellgallery.com/packages/Az.AnalysisServices)   
Dokumentace - [Odkaz Az.AnalysisServices](/powershell/module/az.analysisservices)

## <a name="database-operations"></a>Databázové operace

Operace databáze služby Azure Analysis Services používají stejný modul SqlServer jako služby SQL Server Analysis Services. Ne všechny rutiny jsou však podporovány pro Služby Azure Analysis Services. 

Modul SqlServer poskytuje rutiny správy databáze specifické pro úlohy a rutinu pro obecné účely Invoke-ASCmd, která přijímá dotaz nebo skript tabulkového jazyka skriptování (TMSL). Následující rutiny v modulu SqlServer jsou podporovány pro službu Azure Analysis Services.

Instalační modul - [SqlServer](https://www.powershellgallery.com/packages/SqlServer)   
Dokumentace – [odkaz na sqlserver](/powershell/module/sqlserver)

### <a name="supported-cmdlets"></a>Podporované rutiny

|Rutina|Popis|
|------------|-----------------| 
|[Člen role doplňku](https://docs.microsoft.com/powershell/module/sqlserver/Add-RoleMember)|Přidejte člena do role databáze.| 
|[Záložní ASDatabáze](https://docs.microsoft.com/powershell/module/sqlserver/backup-asdatabase)|Zálohování databáze služby Analysis Services.|  
|[Odebrat člen role](https://docs.microsoft.com/powershell/module/sqlserver/remove-rolemember)|Odeberte člena z databázové role.|   
|[Vyvolat-ASCmd](https://docs.microsoft.com/powershell/module/sqlserver/invoke-ascmd)|Spusťte skript TMSL.|
|[Vyvolat-ProcessASDatabáze](https://docs.microsoft.com/powershell/module/sqlserver/invoke-processasdatabase)|Zpracování databáze.|  
|[Vyvolat procesoddílu](https://docs.microsoft.com/powershell/module/sqlserver/invoke-processpartition)|Zpracování oddílu.| 
|[Vyvolat-ProcesovatItabulka](https://docs.microsoft.com/powershell/module/sqlserver/invoke-processtable)|Zpracování tabulky.|  
|[Slučovací oddíl](https://docs.microsoft.com/powershell/module/sqlserver/merge-partition)|Sloučit oddíl.|  
|[Obnovit databázi AS](https://docs.microsoft.com/powershell/module/sqlserver/restore-asdatabase)|Obnovte databázi služby Analysis Services.| 
  

## <a name="related-information"></a>Související informace

* [PowerShell pro SQL Server](https://docs.microsoft.com/sql/powershell/sql-server-powershell)      
* [Stažení modulu powershellu serveru SQL Server](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [Stáhnout SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   
* [Modul SqlServer v Galerii prostředí PowerShell](https://www.powershellgallery.com/packages/SqlServer)    
* [Programování tabulkových modelů pro úroveň kompatibility 1200 a vyšší](https://docs.microsoft.com/analysis-services/tabular-model-programming-compatibility-level-1200/tabular-model-programming-for-compatibility-level-1200)
