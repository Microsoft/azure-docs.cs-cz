---
title: Nahrajte inventář prostředků, data o využití, metriky a protokoly a Azure Monitor
description: Nahrajte inventář prostředků, data o využití, metriky a protokoly a Azure Monitor
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: ac6ffd2b5bf48079db6a0cd261dbe2535e1821ac
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90936039"
---
# <a name="upload-resource-inventory-usage-data-metrics-and-logs-to-azure-monitor"></a>Nahrajte inventář prostředků, data o využití, metriky a protokoly a Azure Monitor

S datovými službami ARC v Azure můžete *volitelně* nahrát metriky a protokoly, které Azure monitor, takže můžete agregovat a analyzovat metriky, protokoly, vyvolat výstrahy, odesílat oznámení nebo aktivovat automatizované akce. Odesílání dat do Azure Monitor umožňuje také ukládat data monitorování a protokolů mimo lokalitu a ve velkém měřítku, což umožňuje dlouhodobé uložení dat pro pokročilou analýzu.  Pokud máte více lokalit, které mají datové služby Azure ARC, můžete použít Azure Monitor jako centrální umístění ke shromáždění všech protokolů a metrik napříč vašimi lokalitami.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="before-you-begin"></a>Než začnete

Pro povolení scénářů nahrávání protokolů a metrik je potřeba pár časových kroků pro instalaci:

1) Vytvořte instanční objekt/Azure Active Directory aplikaci, včetně vytvoření tajného klíče klienta, a přiřaďte tento instanční objekt k roli "monitorovat metriky" v předplatných, kde se nachází vaše prostředky instance databáze.
2) Vytvořte pracovní prostor Log Analytics a získejte klíče a nastavte informace v proměnných prostředí.

K odeslání metrik se vyžaduje první položka a druhá z nich se vyžaduje k nahrání protokolů.

Pomocí těchto příkazů vytvořte metriky pro nahrání objektu služby a přiřaďte ho k rolím monitor metriky monitorování a přispěvatele, aby instanční objekt mohl nahrávat metriky a provádět operace vytvoření a nahrání.

## <a name="create-service-principal-and-assign-roles"></a>Vytvoření instančního objektu a přiřazení rolí

Pomocí těchto příkazů vytvořte metriky pro nahrání instančního objektu a přiřaďte ho k roli monitorování metrik monitorování:

Chcete-li vytvořit instanční objekt, spusťte tento příkaz:

> [!NOTE]
> Vytvoření instančního objektu vyžaduje [určitá oprávnění v Azure](/active-directory/develop/howto-create-service-principal-portal#required-permissions).

```console
az ad sp create-for-rbac --name <a name you choose>

#Example:
#az ad sp create-for-rbac --name azure-arc-metrics
```

Příklad výstupu:

```console
"appId": "2e72adbf-de57-4c25-b90d-2f73f126e123",
"displayName": "azure-arc-metrics",
"name": "http://azure-arc-metrics",
"password": "5039d676-23f9-416c-9534-3bd6afc78123",
"tenant": "72f988bf-85f1-41af-91ab-2d7cd01ad1234"
```

Uložte hodnoty appId a tenant do proměnné prostředí pro pozdější použití:

```console
#PowerShell

$Env:SPN_CLIENT_ID='<the 'appId' value from the output of the 'az ad sp create-for-rbac' command above>'
$Env:SPN_CLIENT_SECRET='<the 'password' value from the output of the 'az ad sp create-for-rbac' command above>'
$Env:SPN_TENANT_ID='<the 'tenant' value from the output of the 'az ad sp create-for-rbac' command above>'

#Linux/macOS

export SPN_CLIENT_ID='<the 'appId' value from the output of the 'az ad sp create-for-rbac' command above>'
export SPN_CLIENT_SECRET='<the 'password' value from the output of the 'az ad sp create-for-rbac' command above>'
export SPN_TENANT_ID='<the 'tenant' value from the output of the 'az ad sp create-for-rbac' command above>'

#Example (using Linux):
export SPN_CLIENT_ID='2e72adbf-de57-4c25-b90d-2f73f126e123'
export SPN_CLIENT_SECRET='5039d676-23f9-416c-9534-3bd6afc78123'
export SPN_TENANT_ID='72f988bf-85f1-41af-91ab-2d7cd01ad1234'
```

Spuštěním tohoto příkazu přiřaďte instanční objekt k roli monitorování metrik sledování v předplatném, kde jsou umístěné prostředky vaší instance databáze:

```console
az role assignment create --assignee <appId value from output above> --role 'Monitoring Metrics Publisher' --scope subscriptions/<sub ID>
az role assignment create --assignee <appId value from output above> --role 'Contributor' --scope subscriptions/<sub ID>

#Example:
#az role assignment create --assignee 2e72adbf-de57-4c25-b90d-2f73f126ede5 --role 'Monitoring Metrics Publisher' --scope subscriptions/182c901a-129a-4f5d-56e4-cc6b29459123
#az role assignment create --assignee 2e72adbf-de57-4c25-b90d-2f73f126ede5 --role 'Contributor' --scope subscriptions/182c901a-129a-4f5d-56e4-cc6b29459123
```

Příklad výstupu:

```console
{
  "canDelegate": null,
  "id": "/subscriptions/182c901a-129a-4f5d-86e4-cc6b29459123/providers/Microsoft.Authorization/roleAssignments/f82b7dc6-17bd-4e78-93a1-3fb733b912d",
  "name": "f82b7dc6-17bd-4e78-93a1-3fb733b9d123",
  "principalId": "5901025f-0353-4e33-aeb1-d814dbc5d123",
  "principalType": "ServicePrincipal",
  "roleDefinitionId": "/subscriptions/182c901a-129a-4f5d-86e4-cc6b29459123/providers/Microsoft.Authorization/roleDefinitions/3913510d-42f4-4e42-8a64-420c39005123",
  "scope": "/subscriptions/182c901a-129a-4f5d-86e4-cc6b29459123",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="create-a-log-analytics-workspace"></a>Vytvoření pracovního prostoru Log Analytics

Potom spuštěním těchto příkazů vytvořte pracovní prostor Log Analytics a nastavte přístupové informace na proměnné prostředí.

> [!NOTE]
> Tento krok přeskočte, pokud již máte pracovní prostor.

```console
az monitor log-analytics workspace create --resource-group <resource group name> --name <some name you choose>

#Example:
#az monitor log-analytics workspace create --resource-group MyResourceGroup --name MyLogsWorkpace
```

Příklad výstupu:

```console
{
  "customerId": "d6abb435-2626-4df1-b887-445fe44a4123",
  "eTag": null,
  "id": "/subscriptions/182c901a-129a-4f5d-86e4-cc6b29459123/resourcegroups/user-arc-demo/providers/microsoft.operationalinsights/workspaces/user-logworkspace",
  "location": "eastus",
  "name": "user-logworkspace",
  "portalUrl": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "user-arc-demo",
  "retentionInDays": 30,
  "sku": {
    "lastSkuUpdate": "Thu, 30 Jul 2020 22:37:53 GMT",
    "maxCapacityReservationLevel": 3000,
    "name": "pergb2018"
  },
  "source": "Azure",
  "tags": null,
  "type": "Microsoft.OperationalInsights/workspaces"
}
```

## <a name="assign-id-and-shared-key-to-environment-variables"></a>Přiřazení ID a sdíleného klíče k proměnným prostředí

Uložte ID zákazníka (ID pracovního prostoru) jako proměnnou prostředí, která se má použít později:

```console
#PowerShell
$Env:WORKSPACE_ID='<the customerId from the 'log-analytics workspace create' command output above>'

#Linux/macOS
export WORKSPACE_ID='<the customerId from the 'log-analytics workspace create' command output above>'

#Example (using Linux)
#export WORKSPACE_ID='d6abb435-2626-4df1-b887-445fe44a4123'
```

Tento příkaz vytiskne přístupové klíče vyžadované pro připojení k pracovnímu prostoru Log Analytics:

```console
az monitor log-analytics workspace get-shared-keys --resource-group MyResourceGroup --name MyLogsWorkpace
```

Příklad výstupu:

```console
{
  "primarySharedKey": "JXzQp1RcGgjXFCDS3v0sXoxPvbgCoGaIv35lf11Km2WbdGFvLXqaydpaj1ByWGvKoCghL8hL4BRoypXxkLr123==",
  "secondarySharedKey": "p2XHSxLJ4o9IAqm2zINcEmx0UWU5Z5EZz8PQC0OHpFjdpuVaI0zsPbTv5VyPFgaCUlCZb2yEbkiR4eTuTSF123=="
}
```

Uložte primární klíč do proměnné prostředí pro pozdější použití:

```console
#PowerShell:
$Env:WORKSPACE_SHARED_KEY='<the primarySharedKey value from the 'get-shared-keys' command above'

#Linux/macOS:
export WORKSPACE_SHARED_KEY='<the primarySharedKey value from the 'get-shared-keys' command above'

#Example (using Linux):
export WORKSPACE_SHARED_KEY='JXzQp1RcGgjXFCDS3v0sXoxPvbgCoGaIv35lf11Km2WbdGFvLXqaydpaj1ByWGvKoCghL8hL4BRoypXxkLr123=='

```

## <a name="set-final-environment-variables-and-confirm"></a>Nastavte konečné proměnné prostředí a potvrďte

Nastavení adresy URL autority SPN v proměnné prostředí:

```console
#PowerShell
$Env:SPN_AUTHORITY='https://login.microsoftonline.com'

#Linux/macOS:
export SPN_AUTHORITY='https://login.microsoftonline.com'
```

Ujistěte se, že jsou nastavené všechny požadované proměnné prostředí, pokud chcete:

```console
#PowerShell
$Env:WORKSPACE_ID
$Env:WORKSPACE_SHARED_KEY
$Env:SPN_TENANT_ID
$Env:SPN_CLIENT_ID
$Env:SPN_CLIENT_SECRET
$Env:SPN_AUTHORITY

#Linux/macOS
echo $WORKSPACE_ID
echo $WORKSPACE_SHARED_KEY
echo $SPN_TENANT_ID
echo $SPN_CLIENT_ID
echo $SPN_CLIENT_SECRET
echo $SPN_AUTHORITY
```

## <a name="upload-metrics-to-azure-monitor"></a>Nahrát metriky do Azure Monitor

Pokud chcete nahrát metriky pro vaše spravované instance Azure SQL a Azure Database for PostgreSQL spouštět skupiny serverů s vlastním škálováním, použijte následující příkazy rozhraní příkazového řádku:

Tento příkaz exportuje všechny metriky do zadaného souboru:

```console
azdata arc dc export -t metrics --path metrics.json
```

Tímto příkazem se nahrají metriky do služby Azure Monitor:

```console
azdata arc dc upload --path metrics.json
```

## <a name="view-the-metrics-in-the-portal"></a>Zobrazení metrik na portálu

Po nahrání metrik byste je měli být schopni vizualizovat na webu Azure Portal.

Pokud chcete metriky zobrazit na portálu, pomocí tohoto speciálního odkazu otevřete portál: pak na <https://portal.azure.com> panelu hledání vyhledejte instanci databáze podle názvu:

Využití CPU můžete zobrazit na stránce Přehled, nebo pokud chcete podrobnější metriky, můžete v levém navigačním panelu kliknout na metriky.

Jako obor názvů metriky vyberte SQL Server:

Vyberte metriku, kterou chcete vizualizovat (můžete také vybrat více):

Změňte četnost na posledních 30 minut:

> [!NOTE]
> Metriky můžete nahrávat jenom za posledních 30 minut. Azure Monitor odmítne metriky starší než 30 minut.

## <a name="upload-logs-to-azure-monitor"></a>Nahrání protokolů do služby Azure Monitor

 Pokud chcete nahrát protokoly pro spravované instance Azure SQL a Azure Database for PostgreSQL skupiny serverů s vlastním škálováním, spusťte následující příkazy rozhraní příkazového řádku (CLI).

Tento příkaz exportuje všechny protokoly do zadaného souboru:

```console
azdata arc dc export -t logs --path logs.json
```

Tato akce nahraje protokoly do pracovního prostoru Log Analytics v Azure monitor:

```console
azdata arc dc upload --path logs.json
```

## <a name="view-your-logs-in-azure-portal"></a>Zobrazení protokolů v Azure Portal

Po nahrání protokolů byste je měli být schopni dotazovat pomocí Průzkumníka dotazů na protokoly, a to následujícím způsobem:

1. Otevřete Azure Portal a potom v horním panelu hledání vyhledejte svůj pracovní prostor podle jména a pak ho vyberte.
2. Na levém panelu klikněte na Protokoly.
3. Kliknutím na Začínáme (nebo kliknutím na odkazy na stránce Začínáme se dozvíte další informace o Log Analytics, pokud s ním ještě nezačínáte.)
4. V tomto kurzu se dozvíte víc o Log Analytics, pokud je to váš první čas.
5. V dolní části seznamu tabulek rozbalte Vlastní protokoly a zobrazí se tabulka sql_instance_logs_CL.
6. Klikněte na ikonu oka vedle názvu tabulky.
7. Klikněte na tlačítko Zobrazit v editoru dotazů.
8. Editor dotazů teď bude obsahovat dotaz, který zobrazí 10 nejnovějších událostí v protokolu.
9. Tady můžete experimentovat s dotazováním protokolů pomocí editoru dotazů, nastavit upozornění atd.

## <a name="automating-metrics-and-logs-uploads-optional"></a>Automatizace metrik a protokolů – nahrávání (volitelné)

Pokud chcete průběžně nahrávat metriky a protokoly, můžete vytvořit skript a spustit ho v časovači každých pár minut.  Níže je příklad automatizace nahrávání pomocí skriptu prostředí systému Linux.

V oblíbeném editoru textu nebo kódu přidejte do souboru obsah skriptu a uložte ho jako spustitelný soubor skriptu, jako je například. sh (Linux/Mac) nebo. cmd,. bat,. ps1.

```console
azdata arc dc export --type metrics --path metrics.json --force
azdata arc dc upload --path metrics.json
```

Nastavit soubor skriptu jako spustitelný

```console
chmod +x myuploadscript.sh
```

Spusťte skript každé 2 minuty:

```console
watch -n 120 ./myuploadscript.sh
```

Můžete také použít Plánovač úloh, jako je cron nebo Windows Plánovač úloh nebo Orchestrator, jako je Ansible, Puppet nebo.
