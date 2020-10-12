---
title: Automatizace úloh Azure Analysis Services s instančními objekty | Microsoft Docs
description: Naučte se, jak vytvořit instanční objekt pro automatizaci Azure Analysis Services úloh správy.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 28947d1fa4ece5d6285651ef07342cae06ad8bc8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86077367"
---
# <a name="automation-with-service-principals"></a>Automatizace s využitím instančních objektů

Instanční objekty jsou prostředky aplikací služby Azure Active Directory, které vytvoříte v tenantovi za účelem provedení bezobslužných operací na úrovni prostředků a služeb. Jsou to jedinečný typ *identity uživatele* s ID aplikace a heslem nebo certifikátem. Instanční objekt má pouze ta oprávnění, která jsou nutná pro provádění úloh definovaných rolemi a oprávněními, ke kterým je přiřazena. 

V Analysis Services se instanční objekty používají s Azure Automation, bezobslužným režimem PowerShellu, vlastními klientskými aplikacemi a webovými aplikacemi pro automatizaci běžných úloh. Například zřizování serverů, nasazování modelů, aktualizace dat, horizontální navýšení nebo snížení kapacity a pozastavení/obnovení je možné automatizovat pomocí instančních objektů. Oprávnění se přiřazují instančním objektům prostřednictvím členství v rolích, podobně jako běžné účty hlavního názvu uživatele (UPN) služby Azure AD.

Analysis Services taky podporuje operace prováděné spravovanými identitami pomocí instančních objektů. Další informace najdete v tématu [spravované identity pro prostředky Azure](../active-directory/managed-identities-azure-resources/overview.md) a [služby Azure, které podporují ověřování Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-analysis-services).    

## <a name="create-service-principals"></a>Vytvoření instančních objektů
 
Instanční objekty se dají vytvořit v Azure Portal nebo pomocí PowerShellu. Další informace najdete v následujících tématech:

[Vytvoření instančního objektu – Azure Portal](../active-directory/develop/howto-create-service-principal-portal.md)   
[Vytvoření instančního objektu – PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="store-credential-and-certificate-assets-in-azure-automation"></a>Ukládat přihlašovací údaje a prostředky certifikátů v Azure Automation

Přihlašovací údaje instančního objektu a certifikáty je možné bezpečně ukládat v Azure Automation pro operace sady Runbook. Další informace najdete v následujících tématech:

[Prostředky přihlašovacích údajů v Azure Automation](../automation/automation-credentials.md)   
[Prostředky certifikátů ve službě Azure Automation](../automation/automation-certificates.md)

## <a name="add-service-principals-to-server-admin-role"></a>Přidání instančních objektů do role správce serveru

Než budete moct použít instanční objekt pro Analysis Services operace správy serveru, musíte ho přidat do role Správci serveru. Instanční objekty musí být přidané přímo do role správce serveru. Přidání instančního objektu do skupiny zabezpečení a následné přidání této skupiny zabezpečení do role správce serveru není podporováno. Další informace najdete v tématu [Přidání instančního objektu k roli správce serveru](analysis-services-addservprinc-admins.md).

## <a name="service-principals-in-connection-strings"></a>Objekty služby v připojovacích řetězcích

V připojovacích řetězcích se dá použít identifikátor appID a heslo nebo certifikát, který je stejný jako hlavní název uživatele (UPN).

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

#### <a name="using-azanalysisservices-module"></a><a name="azmodule"></a>Použití modulu AZ. AnalysisServices

Při použití instančního objektu pro operace správy prostředků pomocí modulu [AZ. AnalysisServices](/powershell/module/az.analysisservices)  použijte `Connect-AzAccount` rutinu. 

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

#### <a name="using-sqlserver-module"></a>Používání modulu SQLServeru

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

Při připojování v klientských aplikacích a webových aplikacích podporují instalovatelné balíčky [klientských knihoven AMO a ADOMD](https://docs.microsoft.com/analysis-services/client-libraries?view=azure-analysis-services-current) verze 15.0.2 a vyšší z NuGetu instanční objekty v připojovacích řetězcích pomocí následující syntaxe: `app:AppID` a heslo nebo `cert:thumbprint`. 

V následujícím příkladu se `appID` a `password` používají k provedení operace aktualizace modelové databáze:

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
[Aktualizace pomocí Logic Apps](analysis-services-refresh-logic-app.md)  
[Aktualizace s využitím služby Azure Automation](analysis-services-refresh-azure-automation.md)  
[Přidání instančního objektu k roli správce serveru](analysis-services-addservprinc-admins.md)  
[Automatizace úloh Power BI Premium pracovního prostoru a datové sady s instančními objekty](https://docs.microsoft.com/power-bi/admin/service-premium-service-principal) 
