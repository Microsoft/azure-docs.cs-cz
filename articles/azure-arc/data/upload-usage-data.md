---
title: Odeslat data o využití do Azure Monitor
description: Nahrajte data datových služeb s využitím ARC Azure do Azure Monitor
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
zone_pivot_groups: client-operating-system-macos-and-linux-windows-powershell
ms.openlocfilehash: 578f0d1ca742fe4445b8aeed6876d1a73fd3f79e
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2020
ms.locfileid: "92376207"
---
# <a name="upload-usage-data-to-azure-monitor"></a>Odeslat data o využití do Azure Monitor

Informace o použití můžete pravidelně exportovat. Export a nahrávání těchto informací vytvoří a aktualizuje řadič dat, spravovanou instanci SQL a PostgreSQL prostředky skupiny serverů v Azure.

> [!NOTE] 
> Během období Preview se neúčtují žádné náklady na používání datových služeb s podporou ARC Azure.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]


> [!NOTE]
> Před odesláním dat o využití počkejte aspoň 24 hodin po vytvoření řadiče dat ARC Azure.

## <a name="create-service-principal-and-assign-roles"></a>Vytvoření instančního objektu a přiřazení rolí

Než budete pokračovat, ujistěte se, že jste vytvořili požadovaný instanční objekt a přiřadili ho příslušné roli. Podrobnosti najdete tady:
* [Vytvoření instančního objektu](upload-metrics-and-logs-to-azure-monitor.md#create-service-principal)
* [Přiřazení rolí k instančnímu objektu](upload-metrics-and-logs-to-azure-monitor.md#assign-roles-to-the-service-principal)

## <a name="upload-usage-data"></a>Odeslat data o využití

Informace o využití, jako jsou inventarizace a využití prostředků, se dají do Azure nahrát v následujícím dvoufázovém postupu:

1. Přihlaste se k řadiči dat. Zadejte hodnoty na příkazovém řádku. 

   ```console
   azdata login
   ```

1. Pomocí příkazu exportujte data o využití následujícím `azdata arc dc export` způsobem:

   ```console
   azdata arc dc export --type usage --path usage.json
   ```
 
   Tento příkaz vytvoří `usage.json` soubor se všemi datovými zdroji s povoleným obloukem Azure, jako jsou spravované instance SQL, PostgreSQL instancemi s ochranou velkého rozsahu atd., které se vytvářejí na řadiči dat.

2. Nahrání dat o využití pomocí ```azdata upload``` příkazu

   ```console
   azdata arc dc upload --path usage.json
   ```

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

[Odeslat protokoly do Azure Monitor](upload-logs.md)

[Nahrání fakturačních dat do Azure a jejich zobrazení v Azure Portal](view-billing-data-in-azure.md)

[Zobrazit prostředek datového kontroleru Azure ARC v Azure Portal](view-data-controller-in-azure-portal.md)
