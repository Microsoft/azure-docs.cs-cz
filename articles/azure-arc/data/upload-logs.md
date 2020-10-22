---
title: Nahrání protokolů do služby Azure Monitor
description: Nahrajte protokoly pro datové služby s podporou ARC Azure pro Azure Monitor
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
zone_pivot_groups: client-operating-system-macos-and-linux-windows-powershell
ms.openlocfilehash: f3f29ae1ab868a96e6f70ed964f79c47bc591c4d
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2020
ms.locfileid: "92376214"
---
# <a name="upload-logs-to-azure-monitor"></a>Nahrání protokolů do služby Azure Monitor

Pravidelně můžete protokoly exportovat a pak je nahrát do Azure. Při exportu a nahrávání protokolů se taky vytvoří a aktualizuje řadič dat, spravovaná instance SQL a PostgreSQL prostředky skupiny serverů v Azure.

> [!NOTE] 
> Během období Preview se neúčtují žádné náklady na používání datových služeb s podporou ARC Azure.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="before-you-begin"></a>Než začnete

Než budete moct nahrát protokoly, musíte: 

1. [Vytvoření pracovního prostoru Log Analytics](#create-a-log-analytics-workspace)
1. [Přiřazení ID a sdíleného klíče k proměnným prostředí](#assign-id-and-shared-key-to-environment-variables)

## <a name="create-a-log-analytics-workspace"></a>Vytvoření pracovního prostoru Log Analytics

Pokud chcete vytvořit pracovní prostor Log Analytics, spusťte tyto příkazy k vytvoření pracovního prostoru Log Analytics a nastavte přístupové informace na proměnné prostředí.

> [!NOTE]
> Tento krok přeskočte, pokud již máte pracovní prostor.

```azurecli
az monitor log-analytics workspace create --resource-group <resource group name> --workspace-name <some name you choose>
```

Příklad výstupu:

```output
{
  "customerId": "d6abb435-2626-4df1-b887-445fe44a4123",
  "eTag": null,
  "id": "/subscriptions/<Subscription ID>/resourcegroups/user-arc-demo/providers/microsoft.operationalinsights/workspaces/user-logworkspace",
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

Uložte analýzu pracovního prostoru protokolů `customerId` jako proměnnou prostředí pro pozdější použití:

::: zone pivot="client-operating-system-windows-command"

```console
SET WORKSPACE_ID=<customerId>
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```PowerShell
$Env:WORKSPACE_ID='<customerId>'
```
::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```console
export WORKSPACE_ID='<customerId>'
```
::: zone-end

Tento příkaz vrátí přístupové klíče vyžadované pro připojení k pracovnímu prostoru Log Analytics:

```azurecli
az monitor log-analytics workspace get-shared-keys --resource-group MyResourceGroup --workspace-name MyLogsWorkpace
```

Příklad výstupu:

```output
{
  "primarySharedKey": "JXzQp1RcGgjXFCDS3v0sXoxPvbgCoGaIv35lf11Km2WbdGFvLXqaydpaj1ByWGvKoCghL8hL4BRoypXxkLr123==",
  "secondarySharedKey": "p2XHSxLJ4o9IAqm2zINcEmx0UWU5Z5EZz8PQC0OHpFjdpuVaI0zsPbTv5VyPFgaCUlCZb2yEbkiR4eTuTSF123=="
}
```

Uložte primární klíč do proměnné prostředí pro pozdější použití:

::: zone pivot="client-operating-system-windows-command"

```console
SET WORKSPACE_SHARED_KEY=<primarySharedKey>
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```console
$Env:WORKSPACE_SHARED_KEY='<primarySharedKey>'
```
```
::: zone-end


::: zone pivot="client-operating-system-macos-and-linux"

```console
export WORKSPACE_SHARED_KEY='<primarySharedKey>'
```

::: zone-end

## <a name="set-final-environment-variables-and-confirm"></a>Nastavte konečné proměnné prostředí a potvrďte

Nastavení adresy URL autority SPN v proměnné prostředí:

::: zone pivot="client-operating-system-windows-command"

```console
SET SPN_AUTHORITY=https://login.microsoftonline.com
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```console
$Env:SPN_AUTHORITY='https://login.microsoftonline.com'
```

::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```console
export SPN_AUTHORITY='https://login.microsoftonline.com'
```

::: zone-end


## <a name="verify-environment-variables"></a>Ověřit proměnné prostředí

Ujistěte se, že jsou nastavené všechny požadované proměnné prostředí, pokud chcete:


::: zone pivot="client-operating-system-windows-command"

```console
echo %WORKSPACE_ID%
echo %WORKSPACE_SHARED_KEY%
echo %SPN_TENANT_ID%
echo %SPN_CLIENT_ID%
echo %SPN_CLIENT_SECRET%
echo %SPN_AUTHORITY%
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```PowerShell
$Env:WORKSPACE_ID
$Env:WORKSPACE_SHARED_KEY
$Env:SPN_TENANT_ID
$Env:SPN_CLIENT_ID
$Env:SPN_CLIENT_SECRET
$Env:SPN_AUTHORITY
```


::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```console
echo $WORKSPACE_ID
echo $WORKSPACE_SHARED_KEY
echo $SPN_TENANT_ID
echo $SPN_CLIENT_ID
echo $SPN_CLIENT_SECRET
echo $SPN_AUTHORITY
```

::: zone-end

Pomocí sady proměnných prostředí můžete nahrávat protokoly do pracovního prostoru protokol. 

## <a name="upload-logs-to-azure-monitor"></a>Nahrání protokolů do služby Azure Monitor

 Pokud chcete nahrát protokoly pro spravované instance SQL ARC a AzureArc povolené PostgreSQL skupiny serverů s vlastním škálováním, spusťte následující příkazy rozhraní příkazového řádku (CLI).

1. Přihlaste se k řadiči dat ARC Azure pomocí [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] .

   ```console
   azdata login
   ```

   Podle pokynů nastavte obor názvů, uživatelské jméno správce a heslo. 

1. Exportovat všechny protokoly do zadaného souboru:

   ```console
   azdata arc dc export --type logs --path logs.json
   ```

2. Nahrajte protokoly do pracovního prostoru Log Analytics v Azure monitor:

   ```console
   azdata arc dc upload --path logs.json
   ```

## <a name="view-your-logs-in-azure-portal"></a>Zobrazení protokolů v Azure Portal

Po nahrání protokolů byste je měli být schopni dotazovat pomocí Průzkumníka dotazů na protokoly, a to následujícím způsobem:

1. Otevřete Azure Portal a potom v horním panelu hledání vyhledejte svůj pracovní prostor podle jména a pak ho vyberte.
2. Na levém panelu vyberte protokoly.
3. Vyberte Začínáme (nebo vyberte odkazy na stránce Začínáme, kde se dozvíte víc o Log Analytics, pokud s ním ještě nezačínáte).
4. V tomto kurzu se dozvíte víc o Log Analytics, pokud použijete Log Analytics poprvé.
5. V dolní části seznamu tabulek rozbalte Vlastní protokoly a zobrazí se tabulka sql_instance_logs_CL.
6. Vyberte ikonu ' oko ' vedle názvu tabulky.
7. Vyberte tlačítko Zobrazit v editoru dotazů.
8. Teď budete mít dotaz v editoru dotazů, ve kterém se zobrazí nejnovější 10 událostí v protokolu.
9. Tady můžete experimentovat s dotazováním protokolů pomocí editoru dotazů, nastavit upozornění atd.

## <a name="automating-uploads-optional"></a>Automatizace nahrávání (volitelné)

Pokud chcete nahrávat metriky a protokoly na základě plánu, můžete vytvořit skript a spustit ho v časovači každých pár minut. Níže je příklad automatizace nahrávání pomocí skriptu prostředí systému Linux.

V oblíbeném editoru textu nebo kódu přidejte do souboru následující skript a uložte ho jako spustitelný soubor skriptu, jako je například. sh (Linux/Mac) nebo. cmd,. bat,. ps1.

```console
azdata arc dc export --type metrics --path metrics.json --force
azdata arc dc upload --path metrics.json
```

Nastavit soubor skriptu jako spustitelný

```console
chmod +x myuploadscript.sh
```

Spusťte skript každých 20 minut:

```console
watch -n 1200 ./myuploadscript.sh
```

Můžete také použít Plánovač úloh, jako je cron nebo Windows Plánovač úloh nebo Orchestrator, jako je Ansible, Puppet nebo.

## <a name="next-steps"></a>Další kroky

[Nahrajte metriky a protokoly do Azure Monitor](upload-metrics.md)

[Nahrajte data o využití, metriky a protokoly do Azure Monitor](upload-usage-data.md)

[Nahrání fakturačních dat do Azure a jejich zobrazení v Azure Portal](view-billing-data-in-azure.md)

[Zobrazit prostředek datového kontroleru Azure ARC v Azure Portal](view-data-controller-in-azure-portal.md)
