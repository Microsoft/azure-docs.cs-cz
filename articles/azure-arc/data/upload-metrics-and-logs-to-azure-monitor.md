---
title: Nahrajte data o využití, metriky a protokoly do Azure Monitor
description: Nahrajte inventář prostředků, data o využití, metriky a protokoly a Azure Monitor
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
zone_pivot_groups: client-operating-system-macos-and-linux-windows-powershell
ms.openlocfilehash: ac9c8efbe29bf1420a94d486b650758cc22bec2f
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100575762"
---
# <a name="upload-usage-data-metrics-and-logs-to-azure-monitor"></a>Nahrajte data o využití, metriky a protokoly do Azure Monitor

Pravidelně můžete exportovat informace o využití pro účely fakturace, monitorovat metriky a protokoly a pak je nahrát do Azure. Export a nahrávání kteréhokoli z těchto tří typů dat taky vytvoří a aktualizuje řadič dat, spravovanou instanci SQL a PostgreSQL prostředky skupiny serverů v Azure.

> [!NOTE] 
> Během období Preview se neúčtují žádné náklady na používání datových služeb s podporou ARC Azure.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

Než budete moct nahrát data o využití, metriky nebo protokoly, které potřebujete:

* Instalace nástrojů 
* [Registrace `Microsoft.AzureData` poskytovatele prostředků](#register-the-resource-provider) 
* [Vytvoření instančního objektu](#create-service-principal)

## <a name="install-tools"></a>Instalace nástrojů

Mezi požadované nástroje patří: 
* Rozhraní příkazového řádku Azure (AZ) 
* [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] 

Viz [Instalace nástrojů](./install-client-tools.md).

## <a name="register-the-resource-provider"></a>Registrace poskytovatele prostředků

Předtím, než do Azure nahrajete metriky nebo uživatelská data, musíte zajistit, aby vaše předplatné Azure mělo `Microsoft.AzureArcData` zaregistrovaný poskytovatel prostředků.

Chcete-li ověřit poskytovatele prostředků, spusťte následující příkaz:

```azurecli
az provider show -n Microsoft.AzureArcData -o table
```

Pokud poskytovatel prostředků není v současné době zaregistrován v rámci vašeho předplatného, můžete ho zaregistrovat. Pokud ho chcete zaregistrovat, spusťte následující příkaz.  Dokončení příkazu může trvat minutu či dvě.

```azurecli
az provider register -n Microsoft.AzureArcData --wait
```

## <a name="create-service-principal"></a>Vytvoření instančního objektu

Instanční objekt slouží k odesílání dat o využití a metrikách.

Pomocí těchto příkazů vytvořte objekt služby nahrávání metrik:

> [!NOTE]
> Vytvoření instančního objektu vyžaduje [určitá oprávnění v Azure](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app).

Chcete-li vytvořit instanční objekt, aktualizujte následující příklad. Nahraďte `<ServicePrincipalName>` názvem objektu služby a spusťte příkaz:

```azurecli
az ad sp create-for-rbac --name <ServicePrincipalName>
``` 

Pokud jste instanční objekt dříve vytvořili a stačí získat aktuální přihlašovací údaje, spusťte následující příkaz, kterým přihlašovací údaje resetujete.

```azurecli
az ad sp credential reset --name <ServicePrincipalName>
```

Pokud například chcete vytvořit instanční objekt s názvem `azure-arc-metrics` , spusťte následující příkaz.

```
az ad sp create-for-rbac --name azure-arc-metrics
```

Příklad výstupu:

```output
"appId": "2e72adbf-de57-4c25-b90d-2f73f126e123",
"displayName": "azure-arc-metrics",
"name": "http://azure-arc-metrics",
"password": "5039d676-23f9-416c-9534-3bd6afc78123",
"tenant": "72f988bf-85f1-41af-91ab-2d7cd01ad1234"
```

Uložte `appId` hodnoty, `password` a `tenant` v proměnné prostředí pro pozdější použití. 

::: zone pivot="client-operating-system-windows-command"

```console
SET SPN_CLIENT_ID=<appId>
SET SPN_CLIENT_SECRET=<password>
SET SPN_TENANT_ID=<tenant>
```

::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```console
export SPN_CLIENT_ID='<appId>'
export SPN_CLIENT_SECRET='<password>'
export SPN_TENANT_ID='<tenant>'
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```console
$Env:SPN_CLIENT_ID="<appId>"
$Env:SPN_CLIENT_SECRET="<password>"
$Env:SPN_TENANT_ID="<tenant>"
```

::: zone-end

Po vytvoření instančního objektu přiřaďte instančnímu objektu příslušnou roli. 

## <a name="assign-roles-to-the-service-principal"></a>Přiřazení rolí k instančnímu objektu

Spuštěním tohoto příkazu přiřaďte instanční objekt k `Monitoring Metrics Publisher` roli v předplatném, kde jsou umístěny prostředky vaší instance databáze:

::: zone pivot="client-operating-system-windows-command"

> [!NOTE]
> Při spuštění z prostředí systému Windows je třeba použít pro názvy rolí dvojité uvozovky.

```azurecli
az role assignment create --assignee <appId> --role "Monitoring Metrics Publisher" --scope subscriptions/<Subscription ID>
az role assignment create --assignee <appId> --role "Contributor" --scope subscriptions/<Subscription ID>
```
::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```azurecli
az role assignment create --assignee <appId> --role 'Monitoring Metrics Publisher' --scope subscriptions/<Subscription ID>
az role assignment create --assignee <appId> --role 'Contributor' --scope subscriptions/<Subscription ID>
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```powershell
az role assignment create --assignee <appId> --role 'Monitoring Metrics Publisher' --scope subscriptions/<Subscription ID>
az role assignment create --assignee <appId> --role 'Contributor' --scope subscriptions/<Subscription ID>
```

::: zone-end

Příklad výstupu:

```output
{
  "canDelegate": null,
  "id": "/subscriptions/<Subscription ID>/providers/Microsoft.Authorization/roleAssignments/f82b7dc6-17bd-4e78-93a1-3fb733b912d",
  "name": "f82b7dc6-17bd-4e78-93a1-3fb733b9d123",
  "principalId": "5901025f-0353-4e33-aeb1-d814dbc5d123",
  "principalType": "ServicePrincipal",
  "roleDefinitionId": "/subscriptions/<Subscription ID>/providers/Microsoft.Authorization/roleDefinitions/3913510d-42f4-4e42-8a64-420c39005123",
  "scope": "/subscriptions/<Subscription ID>",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

S instančním objektem přiřazeným k příslušné roli můžete pokračovat v odesílání metrik nebo dat uživatele. 

## <a name="upload-logs-metrics-or-user-data"></a>Odeslat protokoly, metriky nebo uživatelská data

Konkrétní kroky pro nahrávání protokolů, metrik nebo uživatelských dat se liší v závislosti na typu informací, které nahráváte. 

[Odeslat protokoly do Azure Monitor](upload-logs.md)

[Nahrát metriky do Azure Monitor](upload-metrics.md)

[Odeslat data o využití do Azure Monitor](upload-usage-data.md)

## <a name="general-guidance-on-exporting-and-uploading-usage-metrics"></a>Obecné pokyny pro export a nahrávání využití, metriky

Operace vytvoření, čtení, aktualizace a odstranění (CRUD) na datových službách s podporou ARC Azure jsou protokolovány pro účely fakturace a monitorování. K dispozici jsou služby na pozadí, které sledují tyto operace CRUD a patřičně vypočítávají spotřebu. Skutečný výpočet využití nebo spotřeby probíhá na plánovaném základu a provede se na pozadí. 

Během období Preview se tento proces děje v noci. Obecně platí, že je možné nahrávat použití jenom jednou za den. Pokud se informace o použití exportují a nahrají víckrát během jednoho období 24 hodin, aktualizuje se v Azure Portal jenom inventář prostředků, ale ne využití prostředků.

V případě nahrávání metrik akceptuje Azure monitor jenom posledních 30 minut dat ([Další informace](../../azure-monitor/essentials/metrics-store-custom-rest-api.md#troubleshooting)). Pokyny pro nahrávání metrik je odeslání metrik ihned po vytvoření souboru exportu, takže můžete zobrazit celou sadu dat v Azure Portal. Pokud jste například exportovali metriky na 2:00 PM a spustili jste příkaz pro nahrání na 2:50 odp. Vzhledem k tomu, že Azure Monitor akceptuje jenom data za posledních 30 minut, na portálu se nemusí zobrazovat žádná data. 

## <a name="next-steps"></a>Další kroky

[Další informace o instančních objektech](/powershell/azure/azurerm/create-azure-service-principal-azureps#what-is-a-service-principal)

[Nahrání fakturačních dat do Azure a jejich zobrazení v Azure Portal](view-billing-data-in-azure.md)

[Zobrazit prostředek datového kontroleru Azure ARC v Azure Portal](view-data-controller-in-azure-portal.md)