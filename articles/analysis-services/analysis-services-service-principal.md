---
title: Automatizace úloh služby Azure Analysis Services pomocí instančních objektů | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit instanční objekty pro automatizaci úloh služby Azure Analysis Services.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: c034ed7164e67183b9a848d5210dcaf377476c6a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65518163"
---
# <a name="automation-with-service-principals"></a>Automatizace s využitím instančních objektů

Instanční objekty jsou prostředky aplikací služby Azure Active Directory, které vytvoříte v tenantovi za účelem provedení bezobslužných operací na úrovni prostředků a služeb. Jsou jedinečný typ *identitu uživatele* pomocí ID aplikace a heslo nebo certifikát. Instanční objekt služby má pouze oprávnění nezbytná k provádění úloh definovaných podle role a oprávnění, pro které je přiřazen. 

Ve službě Analysis Services instanční objekty se používají s Azure Automation, PowerShell bezobslužném režimu, vlastní klientské aplikace a webové aplikace k automatizaci běžných úkolů. Například zřizování serverů, nasazení modelů, aktualizace dat, škálování směrem nahoru nebo dolů, a pozastavit/obnovit je všechny možné automatizovat pomocí instančních objektů. Oprávnění se přiřazují instanční objekty prostřednictvím členství v roli, podobně jako regulární účty Azure AD (UPN).

Analysis Services podporuje také operací provedených metodou spravovaných identit pomocí instančních objektů. Další informace najdete v tématu [spravovaných identit pro prostředky Azure](../active-directory/managed-identities-azure-resources/overview.md) a [služby Azure, že podpora Azure AD ověřování](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-analysis-services).

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

#### <a name="a-nameazmodule-using-azanalysisservices-module"></a><a name="azmodule" />Pomocí modulu Az.AnalysisServices

Při použití instančního objektu pro operace správy zdrojů se [Az.AnalysisServices](/powershell/module/az.analysisservices) modul, použijte `Connect-AzAccount` rutiny. 

V následujícím příkladu appID a heslo slouží k provedení operace roviny řízení pro synchronizaci, aby se repliky jen pro čtení a škálovat nahoru/out:

```powershell
Param (
        [Parameter(Mandatory=$true)] [String] $AppId,
        [Parameter(Mandatory=$true)] [String] $PlainPWord,
        [Parameter(Mandatory=$true)] [String] $TenantId
       )
$PWord = ConvertTo-SecureString -String $PlainPWord -AsPlainText -Force
$Credential = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList $AppId, $PWord

# Connect using Az module
Connect-AzAccount -Credential $Credential -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx"

# Syncronize a database for query scale out
Sync-AzAnalysisServicesInstance -Instance "asazure://westus.asazure.windows.net/testsvr" -Database "testdb"

# Scale up the server to an S1, set 2 read-only replicas, and remove the primary from the query pool. The new replicas will hydrate from the synchronized data.
Set-AzAnalysisServicesServer -Name "testsvr" -ResourceGroupName "testRG" -Sku "S1" -ReadonlyReplicaCount 2 -DefaultConnectionMode Readonly
```

#### <a name="using-sqlserver-module"></a>Pomocí modulu SQLServer

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
