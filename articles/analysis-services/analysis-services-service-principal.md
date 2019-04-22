---
title: Automatizace úloh služby Azure Analysis Services pomocí instančních objektů | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit instanční objekty pro automatizaci úloh služby Azure Analysis Services.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: b10be061e015686c68684723fd2d73c1431c7266
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2019
ms.locfileid: "59699402"
---
# <a name="automation-with-service-principals"></a>Automatizace s využitím instančních objektů

Instanční objekty jsou prostředky aplikací služby Azure Active Directory, které vytvoříte v tenantovi za účelem provedení bezobslužných operací na úrovni prostředků a služeb. Jsou jedinečný typ *identitu uživatele* pomocí ID aplikace a heslo nebo certifikát. Instanční objekt služby má pouze oprávnění nezbytná k provádění úloh definovaných podle role a oprávnění, pro které je přiřazen. 

Ve službě Analysis Services instanční objekty se používají s Azure Automation, PowerShell bezobslužném režimu, vlastní klientské aplikace a webové aplikace k automatizaci běžných úkolů. Například zřizování serverů, nasazení modelů, aktualizace dat, škálování směrem nahoru nebo dolů, a pozastavit/obnovit je všechny možné automatizovat pomocí instančních objektů. Oprávnění se přiřazují instanční objekty prostřednictvím členství v roli, podobně jako regulární účty Azure AD (UPN).

## <a name="create-service-principals"></a>Vytvoření instančních objektů
 
Instanční objekty lze vytvořit na webu Azure Portal nebo pomocí prostředí PowerShell. Další informace naleznete v tématu:

[Vytvoření instančního objektu – Azure portal](../active-directory/develop/howto-create-service-principal-portal.md)   
[Vytvoření instančního objektu – PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="store-credential-and-certificate-assets-in-azure-automation"></a>Store assety přihlašovacích údajů a certifikátu ve službě Azure Automation

Přihlašovací údaje instančního objektu služby a certifikáty může být bezpečně uložen ve službě Azure Automation pro operace s runbooky. Další informace naleznete v tématu:

[Assety přihlašovacích údajů ve službě Azure Automation](../automation/automation-credentials.md)   
[Prostředky certifikátů ve službě Azure Automation](../automation/automation-certificates.md)

## <a name="add-service-principals-to-server-admin-role"></a>Přidejte instanční objekty do role správce serveru

Před použitím instančního objektu pro operace správy serveru služby Analysis Services, je nutné přidat do role správce serveru. Další informace najdete v tématu [přidání hlavního názvu služby k roli správce serveru](analysis-services-addservprinc-admins.md).

## <a name="service-principals-in-connection-strings"></a>Instanční objekty v připojovacích řetězců

ID aplikace instančního objektu služby a heslo nebo certifikát je možné v připojovací řetězce skoro stejné jako název UPN.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Při použití instančního objektu pro operace správy zdrojů se [Az.AnalysisServices](/powershell/module/az.analysisservices) modul, použijte `Connect-AzAccount` rutiny. Při použití instančního objektu pro operace serveru se [SQLServer](https://www.powershellgallery.com/packages/SqlServer) modul, použijte `Add-AzAnalysisServicesAccount` rutiny. 

V následujícím příkladu appID a heslo slouží k provedení operace obnovení databáze modelu:

```powershell
Param (

        [Parameter(Mandatory=$true)] [String] $AppId,
        [Parameter(Mandatory=$true)] [String] $PlainPWord,
        [Parameter(Mandatory=$true)] [String] $TenantId
       )
$PWord = ConvertTo-SecureString -String $PlainPWord -AsPlainText -Force

$Credential = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList $AppId, $PWord

Invoke-ProcessTable -Server "asazure://westcentralus.asazure.windows.net/myserver" -TableName "MyTable" -Database "MyDb" -RefreshType "Full" -ServicePrincipal -ApplicationId $AppId -TenantId $TenantId -Credential $Credential
```

### <a name="amo-and-adomd"></a>Sada AMO a ADOMD 

Při připojování pomocí klientských aplikací a webových aplikací, [klientské knihovny AMO a ADOMD](analysis-services-data-providers.md) verze 15.0.2 a vyšší Instalovatelné balíčky NuGet pro podporu instanční objekty v připojovacích řetězců pomocí následující syntaxe: `app:AppID` a heslo nebo `cert:thumbprint`. 

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
[Přihlaste se pomocí Azure Powershellu](https://docs.microsoft.com/powershell/azure/authenticate-azureps)   
[Přidání instančního objektu k roli správce serveru](analysis-services-addservprinc-admins.md)   
