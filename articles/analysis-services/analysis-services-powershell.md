---
title: Správa služby Azure Analysis Services pomocí Powershellu | Dokumentace Microsoftu
description: Správa Azure Analysis Services pomocí Powershellu.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: reference
ms.date: 12/19/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: b4aec7ff560b0695175a18c9c7e429a8ab733345
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/19/2018
ms.locfileid: "53633704"
---
# <a name="manage-azure-analysis-services-with-powershell"></a>Správa služby Azure Analysis Services pomocí Powershellu

Tento článek popisuje rutiny prostředí PowerShell použít k serveru Azure Analysis Services a úlohy správy databází. 

Úlohy správy serveru jako je vytvoření nebo odstranění serveru, pozastavení nebo obnovení operací serveru nebo změna úrovně služby (vrstvy) pomocí rutiny Azure Resource Manageru (prostředků) a rutiny služby Analysis Services (server). Další úlohy správy databází, jako jsou přidávání nebo odebírání členů rolí, zpracování nebo dělení používají rutiny v modulu SqlServer jako SQL Server Analysis Services.

## <a name="permissions"></a>Oprávnění
Většina úkolů prostředí PowerShell vyžaduje, že máte oprávnění správce na serveru Analysis Services, které spravujete. Naplánované úlohy prostředí PowerShell jsou Bezobslužná operace. Princip účtu nebo službě spuštěný Plánovač musí mít oprávnění správce na serveru Analysis Services. 

Pro operace serveru pomocí rutin AzureRm, váš účet nebo účet, který spouští Plánovač musí také patřit do role vlastníka prostředku v [Azure Role-Based řízení přístupu (RBAC)](../role-based-access-control/overview.md). 

## <a name="resource-management-operations"></a>Operace správy zdrojů 
Modul – [AzureRM.AnalysisServices](https://www.powershellgallery.com/packages/AzureRM.AnalysisServices)

|Rutina|Popis| 
|------------|-----------------| 
|[Get-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/get-azurermanalysisservicesserver)|Získá podrobnosti o instanci serveru.|  
|[New-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/new-azurermanalysisservicesserver)|Vytvoří instanci serveru.|   
|[Nové AzureRmAnalysisServicesFirewallConfig](/powershell/module/azurerm.analysisservices/new-azurermanalysisservicesfirewallconfig)|Vytvoří nová konfigurace brány firewall služby Analysis Services.|   
|[Nové AzureRmAnalysisServicesFirewallRule](/powershell/module/azurerm.analysisservices/new-azurermanalysisservicesfirewallrule)|Vytvoří nové pravidlo brány firewall služby Analysis Services.|   
|[Remove-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/remove-azurermanalysisservicesserver)|Odebere instanci serveru.|  
|[Resume-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/resume-azurermanalysisservicesserver)|Obnoví instanci serveru.|  
|[Suspend-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/suspend-azurermanalysisservicesserver)|Pozastaví instanci serveru.| 
|[Set-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/set-azurermanalysisservicesserver)|Upraví instanci serveru.|   
|[Test-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/test-azurermanalysisservicesserver)|Otestuje existenci instance serveru.| 

## <a name="server-management-operations"></a>Operace správy serveru

Modul – [Azure.AnalysisServices](https://www.powershellgallery.com/packages/Azure.AnalysisServices)

|Rutina|Popis| 
|------------|-----------------| 
|[Add-AzureAnalysisServicesAccount](/powershell/module/azure.analysisservices/add-azureanalysisservicesaccount)|Přidá účet ověřený použitého pro požadavky rutiny serveru Azure Analysis Services.| 
|[Export AzureAnalysisServicesInstance](/powershell/module/azurerm.analysisservices/export-azureanalysisservicesinstancelog)|Exportuje protokol z instance serveru Analysis Services v tuto chvíli přihlášeného prostředí jako zadaný v příkazu Add-AzureAnalysisServicesAccount|  
|[Restart-AzureAnalysisServicesInstance](/powershell/module/azurerm.analysisservices/restart-azureanalysisservicesinstance)|Restartování instance serveru Analysis Services v aktuálně přihlášený prostředí. zadané v příkazu Add-AzureAnalysisServicesAccount.|  
|[Synchronizace AzureAnalysisServicesInstance](/powershell/module/azurerm.analysisservices/restart-azureanalysisservicesinstance)|Zadaná databáze na zadané instanci serveru Analysis Services pro všechny instance horizontální navýšení kapacity dotazu v aktuálně přihlášeného prostředí jako zadaný v příkazu Add-AzureAnalysisServicesAccount synchronizuje|  

## <a name="database-operations"></a>Databázové operace

Operace databáze Azure Analysis Services používají stejné [modulu SqlServer](https://www.powershellgallery.com/packages/SqlServer) jako SQL Server Analysis Services. Ne všechny rutiny jsou však podporovány pro Azure Analysis Services. Další informace najdete v tématu [prostředí PowerShell pro SQL Server](https://docs.microsoft.com/sql/powershell/sql-server-powershell).

V modulu SqlServer poskytuje rutiny pro správu databáze specifické pro úlohy, jakož i pro obecné účely rutinu Invoke-ASCmd, který přijímá dotazu tabulkový Model skriptování jazyk TMSL () nebo skript. Pro Azure Analysis Services se podporují následující rutiny v modulu SqlServer.

  
|Rutina|Popis|
|------------|-----------------| 
|[Přidat RoleMember](https://docs.microsoft.com/powershell/module/sqlserver/Add-RoleMember)|Přidáte člena do role databáze.| 
|[Backup-ASDatabase](https://docs.microsoft.com/powershell/module/sqlserver/backup-asdatabase)|Zálohujte databázi služby Analysis Services.|  
|[Odebrat RoleMember](https://docs.microsoft.com/powershell/module/sqlserver/remove-rolemember)|Odebrání člena z role databáze.|   
|[Invoke-ASCmd](https://docs.microsoft.com/powershell/module/sqlserver/invoke-ascmd)|Spusťte skript TMSL.|
|[Invoke-ProcessASDatabase](https://docs.microsoft.com/powershell/module/sqlserver/invoke-processasdatabase)|Zpracujte databázi.|  
|[Vyvolání ProcessPartition](https://docs.microsoft.com/powershell/module/sqlserver/invoke-processpartition)|Zpracování oddílu.| 
|[Vyvolání ProcessTable](https://docs.microsoft.com/powershell/module/sqlserver/invoke-processtable)|Zpracovat tabulku.|  
|[Sloučení oddílů](https://docs.microsoft.com/powershell/module/sqlserver/merge-partition)|Sloučit oddíl.|  
|[Obnovení ASDatabase](https://docs.microsoft.com/powershell/module/sqlserver/restore-asdatabase)|Obnovení databáze služby Analysis Services.| 
  

## <a name="related-information"></a>Související informace

* [Stáhnout modul prostředí PowerShell pro Server SQL](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [Stažení aplikace SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   
* [Modul systému SQL Server v galerii prostředí PowerShell](https://www.powershellgallery.com/packages/SqlServer)    
* [Tabulkový Model programování pro 1200 úroveň kompatibility a vyšší](https://msdn.microsoft.com/library/mt712541.aspx)
