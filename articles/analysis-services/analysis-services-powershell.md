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
ms.openlocfilehash: 1f9c30f1c914f6c8d42967e014d967ba0d5b85cc
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "58893839"
---
# <a name="manage-azure-analysis-services-with-powershell"></a>Správa služby Azure Analysis Services pomocí Powershellu

Tento článek popisuje rutiny prostředí PowerShell použít k serveru Azure Analysis Services a úlohy správy databází. 

Úlohy správy serveru jako je vytvoření nebo odstranění serveru, pozastavení nebo obnovení operací serveru nebo změna úrovně služby (vrstvy) pomocí rutiny Azure Resource Manageru (prostředků) a rutiny služby Analysis Services (server). Další úlohy správy databází, jako jsou přidávání nebo odebírání členů rolí, zpracování nebo dělení používají rutiny v modulu SqlServer jako SQL Server Analysis Services.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="permissions"></a>Oprávnění

Většina úkolů prostředí PowerShell vyžaduje, že máte oprávnění správce na serveru Analysis Services, které spravujete. Naplánované úlohy prostředí PowerShell jsou Bezobslužná operace. Účet nebo instanční objekt s plánovači musí mít oprávnění správce na serveru Analysis Services. 

Pro operace serveru pomocí rutin prostředí Azure PowerShell, váš účet nebo účet, který spouští Plánovač musí také patřit do role vlastníka prostředku v [Azure Role-Based řízení přístupu (RBAC)](../role-based-access-control/overview.md). 

## <a name="resource-management-operations"></a>Operace správy zdrojů 

Module - [Az.AnalysisServices](/powershell/module/az.analysisservices)

|Rutina|Popis| 
|------------|-----------------| 
|[Get-AzAnalysisServicesServer](/powershell/module/az.analysisservices/get-azanalysisservicesserver)|Získá podrobnosti o instanci serveru.|  
|[New-AzAnalysisServicesServer](/powershell/module/az.analysisservices/new-azanalysisservicesserver)|Vytvoří instanci serveru.|   
|[New-AzAnalysisServicesFirewallConfig](/powershell/module/az.analysisservices/new-azanalysisservicesfirewallconfig)|Vytvoří nová konfigurace brány firewall služby Analysis Services.|   
|[New-AzAnalysisServicesFirewallRule](/powershell/module/az.analysisservices/new-azanalysisservicesfirewallrule)|Vytvoří nové pravidlo brány firewall služby Analysis Services.|   
|[Remove-AzAnalysisServicesServer](/powershell/module/az.analysisservices/remove-azanalysisservicesserver)|Odebere instanci serveru.|  
|[Resume-AzAnalysisServicesServer](/powershell/module/az.analysisservices/resume-azanalysisservicesserver)|Obnoví instanci serveru.|  
|[Suspend-AzAnalysisServicesServer](/powershell/module/az.analysisservices/suspend-azanalysisservicesserver)|Pozastaví instanci serveru.| 
|[Set-AzAnalysisServicesServer](/powershell/module/az.analysisservices/set-azanalysisservicesserver)|Upraví instanci serveru.|   
|[Test-AzAnalysisServicesServer](/powershell/module/az.analysisservices/test-azanalysisservicesserver)|Otestuje existenci instance serveru.| 

## <a name="server-management-operations"></a>Operace správy serveru

Modul – [Azure.AnalysisServices](https://www.powershellgallery.com/packages/Azure.AnalysisServices)

|Rutina|Popis| 
|------------|-----------------| 
|[Add-AzAnalysisServicesAccount](/powershell/module/az.analysisservices/add-AzAnalysisServicesaccount)|Přidá účet ověřený použitého pro požadavky rutiny serveru Azure Analysis Services.| 
|[Export-AzAnalysisServicesInstance](/powershell/module/az.analysisservices/export-AzAnalysisServicesinstancelog)|Exportuje protokol z instance serveru Analysis Services v aktuálně přihlášeného prostředí jako zadaný v příkazu Add-AzAnalysisServicesAccount|  
|[Restart-AzAnalysisServicesInstance](/powershell/module/az.analysisservices/restart-AzAnalysisServicesinstance)|Restartování instance serveru Analysis Services v aktuálně přihlášený prostředí. zadané v příkazu Add-AzAnalysisServicesAccount.|  
|[Sync-AzAnalysisServicesInstance](/powershell/module/az.analysisservices/restart-AzAnalysisServicesinstance)|Zadaná databáze na zadané instanci serveru Analysis Services pro všechny instance horizontální navýšení kapacity dotazu v aktuálně přihlášeného prostředí jako zadaný v příkazu Add-AzAnalysisServicesAccount synchronizuje|  

## <a name="database-operations"></a>Databázové operace

Operace databáze Azure Analysis Services používají stejné [modulu SqlServer](https://www.powershellgallery.com/packages/SqlServer) jako SQL Server Analysis Services. Ne všechny rutiny jsou však podporovány pro Azure Analysis Services. Další informace najdete v tématu [prostředí PowerShell pro SQL Server](https://docs.microsoft.com/sql/powershell/sql-server-powershell).

V modulu SqlServer poskytuje rutiny pro správu databáze specifické pro úlohy, jakož i pro obecné účely rutinu Invoke-ASCmd, který přijímá dotazu tabulkový Model skriptování jazyk TMSL () nebo skript. Pro Azure Analysis Services se podporují následující rutiny v modulu SqlServer.

  
|Rutina|Popis|
|------------|-----------------| 
|[Add-RoleMember](https://docs.microsoft.com/powershell/module/sqlserver/Add-RoleMember)|Přidáte člena do role databáze.| 
|[Backup-ASDatabase](https://docs.microsoft.com/powershell/module/sqlserver/backup-asdatabase)|Zálohujte databázi služby Analysis Services.|  
|[Remove-RoleMember](https://docs.microsoft.com/powershell/module/sqlserver/remove-rolemember)|Odebrání člena z role databáze.|   
|[Invoke-ASCmd](https://docs.microsoft.com/powershell/module/sqlserver/invoke-ascmd)|Spusťte skript TMSL.|
|[Invoke-ProcessASDatabase](https://docs.microsoft.com/powershell/module/sqlserver/invoke-processasdatabase)|Zpracujte databázi.|  
|[Vyvolání ProcessPartition](https://docs.microsoft.com/powershell/module/sqlserver/invoke-processpartition)|Zpracování oddílu.| 
|[Vyvolání ProcessTable](https://docs.microsoft.com/powershell/module/sqlserver/invoke-processtable)|Zpracovat tabulku.|  
|[Merge-Partition](https://docs.microsoft.com/powershell/module/sqlserver/merge-partition)|Sloučit oddíl.|  
|[Restore-ASDatabase](https://docs.microsoft.com/powershell/module/sqlserver/restore-asdatabase)|Obnovení databáze služby Analysis Services.| 
  

## <a name="related-information"></a>Související informace

* [Stáhnout modul prostředí PowerShell pro Server SQL](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [Stažení aplikace SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   
* [Modul systému SQL Server v galerii prostředí PowerShell](https://www.powershellgallery.com/packages/SqlServer)    
* [Tabulkový Model programování pro 1200 úroveň kompatibility a vyšší](/sql/analysis-services/tabular-model-programming-compatibility-level-1200/tabular-model-programming-for-compatibility-level-1200)
