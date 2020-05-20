---
title: Automatizace úloh Azure Analysis Services s instančními objekty | Microsoft Docs
description: Naučte se, jak vytvořit instanční objekt pro automatizaci Azure Analysis Services úloh správy.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 1ea9b978cce22ae6d99cbce5b4b85558f2a47b59
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2020
ms.locfileid: "83699076"
---
# <a name="automation-with-service-principals"></a>Automatizace s využitím instančních objektů

Instanční objekty jsou prostředky aplikací služby Azure Active Directory, které vytvoříte v tenantovi za účelem provedení bezobslužných operací na úrovni prostředků a služeb. Jsou to jedinečný typ *identity uživatele* s ID aplikace a heslem nebo certifikátem. Instanční objekt má pouze ta oprávnění, která jsou nutná pro provádění úloh definovaných rolemi a oprávněními, ke kterým je přiřazena. 

V Analysis Services se instanční objekty používají s Azure Automation, bezobslužným režimem PowerShellu, vlastními klientskými aplikacemi a webovými aplikacemi pro automatizaci běžných úloh. Například zřizování serverů, nasazování modelů, aktualizace dat, horizontální navýšení nebo snížení kapacity a pozastavení/obnovení je možné automatizovat pomocí instančních objektů. Oprávnění se přiřazují instančním objektům prostřednictvím členství v rolích, podobně jako běžné účty hlavního názvu uživatele (UPN) služby Azure AD.

Analysis Services taky podporuje operace prováděné spravovanými identitami pomocí instančních objektů. Další informace najdete v tématu [spravované identity pro prostředky Azure](../active-directory/managed-identities-azure-resources/overview.md) a [služby Azure, které podporují ověřování Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-analysis-services).  

## <a name="create-service-principals"></a>Vytvoření instančních objektů
 
Instanční objekty se dají vytvořit v Azure Portal nebo pomocí PowerShellu. Další informace naleznete v tématu:

[Vytvoření instančního objektu – Azure Portal](../active-directory/develop/howto-create-service-principal-portal.md)   
[Vytvoření instančního objektu – PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="store-credential-and-certificate-assets-in-azure-automation"></a>Ukládat přihlašovací údaje a prostředky certifikátů v Azure Automation

Přihlašovací údaje instančního objektu a certifikáty je možné bezpečně ukládat v Azure Automation pro operace sady Runbook. Další informace naleznete v tématu:

[Prostředky přihlašovacích údajů v Azure Automation](../automation/automation-credentials.md)   
[Prostředky certifikátů ve službě Azure Automation](../automation/automation-certificates.md)

## <a name="add-service-principals-to-server-admin-role"></a>Přidání instančních objektů do role správce serveru

Než budete moct použít instanční objekt pro Analysis Services operace správy serveru, musíte ho přidat do role Správci serveru. Další informace najdete v tématu [Přidání instančního objektu k roli správce serveru](analysis-services-addservprinc-admins.md).

## <a name="service-principals-in-connection-strings"></a>Objekty služby v připojovacích řetězcích

V připojovacích řetězcích se dá použít identifikátor appID a heslo nebo certifikát, který je stejný jako hlavní název uživatele (UPN).

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

#### <a name="using-azanalysisservices-module"></a><a name="azmodule" />Použití modulu AZ. AnalysisServices

Při použití instančního objektu pro operace správy prostředků pomocí modulu [AZ. AnalysisServices](/powershell/module/az.analysisservices) použijte `Connect-AzAccount` rutinu. 

V následujícím příkladu appID a Password slouží k provádění operací roviny řízení pro synchronizaci do replik jen pro čtení a horizontálního navýšení nebo navýšení kapacity:

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

#### <a name="using-sqlserver-module"></a>Použití modulu SQLServer

V následujícím příkladu se k provedení operace aktualizace databáze modelu používá appID a heslo:

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

### <a name="amo-and-adomd"></a>AMO a ADOMD 

Při připojování k klientským aplikacím a webovým aplikacím můžou [klientské knihovny AMO a ADOMD](analysis-services-data-providers.md) verze 15.0.2 a novější instalovat balíčky z NuGet podporovat instanční objekty v připojovacích řetězcích pomocí následující syntaxe: `app:AppID` a hesla nebo `cert:thumbprint` . 

V následujícím příkladu `appID` a a `password` slouží k provedení operace aktualizace databáze modelu:

```csharp
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

## <a name="next-steps"></a>Další kroky
[Přihlaste se pomocí Azure PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps)   
[Přidání instančního objektu k roli správce serveru](analysis-services-addservprinc-admins.md)  
[Automatizace úloh Power BI Premium pracovního prostoru a datové sady s instančními objekty](https://docs.microsoft.com/power-bi/admin/service-premium-service-principal) 
