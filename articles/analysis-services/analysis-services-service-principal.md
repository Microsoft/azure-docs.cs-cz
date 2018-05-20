---
title: Automatizace Azure Analysis Services úloh s objekty služby | Microsoft Docs
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 05/15/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 2b49b85d39f55052e112fd9f4f0e28bdc6c91637
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="automation-with-service-principals"></a>Automatizace s objekty služby

Objekty služby jsou prostředek aplikace služby Azure Active Directory vytvoříte v rámci vašeho klienta k provedení bezobslužné prostředků a operací úrovně služby. Jsou jedinečné typ *identitu uživatele* s ID aplikace a heslo nebo certifikát. Objekt služby má pouze oprávnění nezbytná k provádění úloh, které jsou definované rolí a oprávnění, pro které je přiřazen. 

Ve službě Analysis Services jsou objekty služby použít s Azure Automation, bezobslužném režimu prostředí PowerShell, vlastní klientské aplikace a webové aplikace pro automatizaci běžných úkolů. Například číselník zřizování servery, nasazení modelů, aktualizace dat, měřítko a pozastavení nebo obnovení všechny možné automatizovat pomocí objekty služby. Oprávnění jsou přiřazeny k objekty služby prostřednictvím členství v rolích, podobně jako regulární účty Azure AD (UPN).

## <a name="create-service-principals"></a>Vytvoření instančních objektů
 
Objekty služby lze vytvořit na portálu Azure nebo pomocí prostředí PowerShell. Další informace naleznete v tématu:

[Vytvoření instančního objektu – portál Azure](../azure-resource-manager/resource-group-create-service-principal-portal.md)   
[Vytvoření instančního objektu – PowerShell](../azure-resource-manager/resource-group-authenticate-service-principal.md)

## <a name="store-credential-and-certificate-assets-in-azure-automation"></a>Uložení přihlašovacích údajů a certifikátu prostředky ve službě Azure Automation

Hlavní přihlašovací údaje služby a certifikáty mohou být uloženy bezpečně ve službě Azure Automation pro operace sady runbook. Další informace naleznete v tématu:

[Prostředků přihlašovacích údajů ve službě Azure Automation](../automation/automation-credentials.md)   
[Certifikát prostředky ve službě Azure Automation](../automation/automation-certificates.md)

## <a name="add-service-principals-to-server-admin-role"></a>Přidat objekty služby do role správce serveru

Než budete moct použít objekt služby pro operace správy serveru služby Analysis Services, třeba přidat ji do role správce serveru. Další informace najdete v tématu [přidat hlavní název služby do role správce serveru](analysis-services-addservprinc-admins.md).

## <a name="service-principals-in-connection-strings"></a>Objekty služby v připojovacích řetězců

Hlavní appID služby a heslo nebo certifikát mohou být používány připojovací řetězce, podobně jako UPN.

### <a name="powershell"></a>PowerShell

Při použití objektu služby pro operace správy prostředků s [AzureRM.AnalysisServices](https://www.powershellgallery.com/packages/AzureRM.AnalysisServices) modulu, použijte `Login-AzureRmAccount` rutiny. Při použití objektu služby pro operace serverů s [SQLServer](https://www.powershellgallery.com/packages/SqlServer) modulu, použijte `Add-AzureAnalysisServicesAccount` rutiny. 

V následujícím příkladu appID a heslo slouží k provedení operace obnovení databáze modelu:

```PowerShell
Param (

        [Parameter(Mandatory=$true)] [String] $AppId,
        [Parameter(Mandatory=$true)] [String] $PlainPWord,
        [Parameter(Mandatory=$true)] [String] $TenantId
       )
$PWord = ConvertTo-SecureString -String $PlainPWord -AsPlainText -Force

$Credential = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList $AppId, $PWord

Add-AzureAnalysisServicesAccount -Credential $Credential -ServicePrincipal -TenantId $TenantId -RolloutEnvironment "westcentralus.asazure.windows.net"

Invoke-ProcessTable -Server "asazure://westcentralus.asazure.windows.net/myserver" -TableName "MyTable" -Database "MyDb" -RefreshType "Full"
```

### <a name="amo-and-adomd"></a>AMO a ADOMD 

Při připojování pomocí klientské aplikace a webové aplikace, [AMO a ADOMD klientské knihovny](analysis-services-data-providers.md) verze 15.0.2 a vyšší instalovat balíčky NuGet podporují objekty služby v připojovacích řetězců pomocí následující syntaxe: `app:AppID` a heslo nebo `cert:thumbprint`. 

V následujícím příkladu `appID` a `password` se používají k provedení operace obnovení databáze modelu:

```C#
string appId = "xxx";
string authKey = "yyy";
string connString = $"Provider=MSOLAP;Data Source=asazure://westus.asazure.windows.net/<servername>;User ID=app:{appId};Password={authKey};";
Server server = new Server();
server.Connect(connString);
Database db = server.Databases.FindByName("adventureworks");
Table tbl = db.Model.Tables.Find("DimDate");
tbl.RequestRefresh(RefreshType.Full);
db.Model.SaveChanges();
```

## <a name="next-steps"></a>Další postup
[Přihlaste se pomocí Azure PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps)   
[Přidat hlavní název služby do role správce serveru](analysis-services-addservprinc-admins.md)   