---
title: Automatizace úloh služby Azure Analysis Services pomocí instančních objektů | Dokumenty společnosti Microsoft
description: Zjistěte, jak vytvořit instanční objekt pro automatizaci úloh správy služby Azure Analysis Services.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: dc163de9a7fb46d62f4bc2983e040e68bbf9231c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266140"
---
# <a name="automation-with-service-principals"></a>Automatizace s využitím instančních objektů

Instanční objekty jsou prostředky aplikací služby Azure Active Directory, které vytvoříte v tenantovi za účelem provedení bezobslužných operací na úrovni prostředků a služeb. Jedná se o jedinečný typ *identity uživatele* s ID aplikace a heslem nebo certifikátem. Instanční objekt má pouze oprávnění potřebná k provádění úloh definovaných rolemi a oprávněními, pro které je přiřazen. 

Ve službě Analysis Services se instanční objekty používají s Azure Automation, bezobslužným režimem PowerShellu, vlastními klientskými aplikacemi a webovými aplikacemi k automatizaci běžných úloh. Například zřizování serverů, nasazování modelů, aktualizace dat, škálování nahoru/dolů a pozastavení/obnovení lze automatizovat pomocí instančních objektů. Oprávnění jsou přiřazena k objektům zabezpečení služeb prostřednictvím členství v rolích, podobně jako běžné účty Upn Azure AD.

Analysis Services také podporuje operace prováděné spravované identity pomocí instančních objektů. Další informace najdete v [tématu Spravované identity pro prostředky Azure](../active-directory/managed-identities-azure-resources/overview.md) a [služby Azure, které podporují ověřování Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-analysis-services).  

## <a name="create-service-principals"></a>Vytvoření instančních objektů
 
Instanční objekty lze vytvořit na webu Azure portal nebo pomocí PowerShellu. Další informace naleznete v tématu:

[Vytvoření instančního objektu – portál Azure](../active-directory/develop/howto-create-service-principal-portal.md)   
[Vytvoření instančního objektu – PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="store-credential-and-certificate-assets-in-azure-automation"></a>Ukládání přihlašovacích údajů a prostředků certifikátů v Azure Automation

Pověření a certifikáty hlavního účtu služeb lze bezpečně uložit v Azure Automation pro operace sady Runbook. Další informace naleznete v tématu:

[Prostředky přihlašovacích údajů v Azure Automation](../automation/automation-credentials.md)   
[Prostředky certifikátů ve službě Azure Automation](../automation/automation-certificates.md)

## <a name="add-service-principals-to-server-admin-role"></a>Přidání instančních objektů do role správce serveru

Před použitím instančního objektu pro operace správy serveru služby Analysis Services je nutné jej přidat do role správců serveru. Další informace naleznete [v tématu Přidání instančního objektu do role správce serveru](analysis-services-addservprinc-admins.md).

## <a name="service-principals-in-connection-strings"></a>Instanční objekty v připojovacích řetězcích

Id instančního objektu service id a heslo nebo certifikát lze použít v připojovacích řetězcích podobně jako hlavní název služby.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

#### <a name="using-azanalysisservices-module"></a><a name="azmodule" />Použití modulu Az.AnalysisServices

Při použití instančního objektu pro operace správy prostředků `Connect-AzAccount` s modulem [Az.AnalysisServices](/powershell/module/az.analysisservices) použijte rutinu. 

V následujícím příkladu appID a heslo se používají k provádění operací roviny řízení pro synchronizaci replik jen pro čtení a vertikálně navýšit/vyřadit:

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

V následujícím příkladu appID a heslo se používají k provedení operace aktualizace databáze modelu:

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

Při připojování s klientskými aplikacemi a webovými aplikacemi, [AMO a ADOMD klientské knihovny](analysis-services-data-providers.md) verze 15.0.2 `app:AppID` a `cert:thumbprint`vyšší instalovatelné balíčky z objektů podpory NuGet v připojovacích řetězcích pomocí následující syntaxe: a heslo nebo . 

V následujícím příkladu `appID` `password` a a se používají k provedení operace aktualizace databáze modelu:

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
[Přihlášení pomocí Azure PowerShellu](https://docs.microsoft.com/powershell/azure/authenticate-azureps)   
[Přidání objektu zabezpečení služby do role správce serveru](analysis-services-addservprinc-admins.md)   
