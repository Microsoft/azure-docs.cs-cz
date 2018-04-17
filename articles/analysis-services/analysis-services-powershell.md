---
title: Správa služby Azure Analysis Services pomocí prostředí PowerShell | Microsoft Docs
description: Správa Azure Analysis Services pomocí prostředí PowerShell.
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: reference
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: c7315835bca446c4cae592f4bdd58a733b203655
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="manage-azure-analysis-services-with-powershell"></a>Správa služby Azure Analysis Services pomocí prostředí PowerShell

Tento článek popisuje rutiny prostředí PowerShell použít k provádění serveru Azure Analysis Services a úlohy správy databáze. 

Úlohy správy serveru jako je například vytváření nebo odstraňování serveru, pozastavení nebo obnovení operací serveru nebo změna úrovně služeb (vrstvy) použít rutiny Azure Resource Manager (AzureRM). Další úlohy pro správu databáze například přidáním nebo odebráním členy role zpracování nebo rozdělení do oddílů pomocí rutiny zahrnuté ve stejném modulu SqlServer jako SQL Server Analysis Services.

## <a name="permissions"></a>Oprávnění
Většinu úloh prostředí PowerShell vyžadují, že abyste měli oprávnění správce na serveru služby Analysis Services, který spravujete. Naplánované úlohy prostředí PowerShell jsou bezobslužné operace. Účet, který spouští Plánovač musí mít oprávnění správce na serveru služby Analysis Services. 

Operace serveru pomocí rutin AzureRm, váš účet nebo účet spouštějící scheduler musíte také zařadit do roli vlastníka na zdroj v [řízení řízení přístupu (RBAC)](../role-based-access-control/overview.md). 

## <a name="server-operations"></a>Operace serveru 
Rutiny Azure Analysis Services jsou součástí [AzureRM.AnalysisServices](https://www.powershellgallery.com/packages/AzureRM.AnalysisServices) součást modulu. Instalaci moduly rutin AzureRM naleznete v tématu [rutiny Azure Resource Manager](/powershell/azure/overview) v galerii prostředí PowerShell.

|Rutina|Popis| 
|------------|-----------------| 
|[Add-AzureAnalysisServicesAccount](/powershell/module/azurerm.analysisservices/add-azureanalysisservicesaccount)|Přidá ověřený účet, který chcete použít pro požadavky rutiny serveru Azure Analysis Services.| 
|[Get-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/get-azurermanalysisservicesserver)|Získá podrobnosti instance serveru.|  
|[New-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/new-azurermanalysisservicesserver)|Vytvoří instanci serveru.|   
|[Remove-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/remove-azurermanalysisservicesserver)|Odebere instanci serveru.|  
|[Restart-AzureAnalysisServicesInstance](/powershell/module/azurerm.analysisservices/restart-azureanalysisservicesinstance)|Restartování instance serveru služby Analysis Services v aktuálně přihlášeného prostředí; zadané v příkazu Add-AzureAnalysisServicesAccount.|  
|[Resume-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/resume-azurermanalysisservicesserver)|Obnoví instanci serveru.|  
|[Suspend-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/suspend-azurermanalysisservicesserver)|Pozastaví instanci serveru.| 
|[Set-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/set-azurermanalysisservicesserver)|Upravuje instanci serveru.|   
|[Test-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/test-azurermanalysisservicesserver)|Testy existence instance serveru.| 

## <a name="database-operations"></a>Operace databáze

Operace databáze služby Analysis Services v Azure používají stejné [SqlServer](https://www.powershellgallery.com/packages/SqlServer) modulu jako SQL Server Analysis Services. Ne všechny rutiny jsou však podporovány pro Azure Analysis Services. 

Modul SQL Server poskytuje rutiny správy specifických úkolů databáze a také pro obecné účely rutiny Invoke-ASCmd, která přijímá dotazu tabulkový Model skriptovací jazyk (TMSL) nebo skriptu. Následující rutiny v modulu SQL Server jsou podporovány pro Azure Analysis Services.

  
|Rutina|Popis|
|------------|-----------------| 
|[Přidat RoleMember](https://msdn.microsoft.com/library/hh510167.aspx)|Přidáte člena do role databáze.| 
|[Backup-ASDatabase](https://docs.microsoft.com/sql/analysis-services/powershell/backup-asdatabase-cmdlet)|Zálohování databáze služby Analysis Services.|  
|[Odebrat RoleMember](https://msdn.microsoft.com/library/hh510173.aspx)|Odebrání člena z databázové role.|   
|[Invoke-ASCmd](https://msdn.microsoft.com/library/hh479579.aspx)|Spuštění skriptu TMSL.|
|[Vyvolání ProcessASDatabase](https://msdn.microsoft.com/library/mt651773.aspx)|Zpracování databáze.|  
|[Vyvolání ProcessPartition](https://msdn.microsoft.com/library/hh510164.aspx)|Zpracování oddílu.| 
|[Vyvolání ProcessTable](https://msdn.microsoft.com/library/mt651774.aspx)|Proces tabulku.|  
|[Sloučení oddílů](https://msdn.microsoft.com/library/hh479576.aspx)|Sloučit oddíl.|  
|[Obnovení ASDatabase](https://docs.microsoft.com/sql/analysis-services/powershell/restore-asdatabase-cmdlet)|Obnovte databázi služby Analysis Services.| 
  

## <a name="related-information"></a>Související informace

* [Stáhnout modul prostředí PowerShell serveru SQL](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [Stažení aplikace SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   
* [Modul SQL Server v galerii prostředí PowerShell](https://www.powershellgallery.com/packages/SqlServer)    
* [Tabulkový Model programování pro 1200 úroveň kompatibility a vyšší](https://msdn.microsoft.com/library/mt712541.aspx)
