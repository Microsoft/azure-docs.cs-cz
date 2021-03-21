---
title: Nahrát metriky do Azure Monitor
description: Nahrajte metriky datových služeb s podporou ARC Azure do Azure Monitor
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
zone_pivot_groups: client-operating-system-macos-and-linux-windows-powershell
ms.openlocfilehash: d7c611f1cdb5e3294e38f87c0534003813e50388
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100575696"
---
# <a name="upload-metrics-to-azure-monitor"></a>Nahrát metriky do Azure Monitor

Pravidelně můžete exportovat metriky monitorování a pak je nahrát do Azure. Export a nahrávání dat taky vytvoří a aktualizuje řadič dat, spravovanou instanci SQL a PostgreSQL prostředky skupiny serverů v Azure.

> [!NOTE] 
> Během období Preview se neúčtují žádné náklady na používání datových služeb s podporou ARC Azure.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>Předpoklady

Než budete pokračovat, ujistěte se, že jste vytvořili požadovaný instanční objekt a přiřadili ho příslušné roli. Podrobnosti najdete tady:
* [Vytvoření instančního objektu](upload-metrics-and-logs-to-azure-monitor.md#create-service-principal)
* [Přiřazení rolí k instančnímu objektu](upload-metrics-and-logs-to-azure-monitor.md#assign-roles-to-the-service-principal)

## <a name="upload-metrics"></a>Odesílání metrik

S datovými službami Azure ARC můžete volitelně nahrát metriky do Azure Monitor, abyste mohli agregovat a analyzovat metriky, vyvolávat výstrahy, odesílat oznámení nebo aktivovat automatizované akce. 

Odesílání dat do Azure Monitor umožňuje také ukládat data metrik mimo lokalitu a v obrovských škálování, což umožňuje dlouhodobé uložení dat pro pokročilou analýzu.

Pokud máte více lokalit, které mají datové služby Azure ARC, můžete použít Azure Monitor jako centrální umístění ke shromáždění všech protokolů a metrik napříč vašimi lokalitami.

## <a name="set-final-environment-variables-and-confirm"></a>Nastavte konečné proměnné prostředí a potvrďte

Nastavení adresy URL autority SPN v proměnné prostředí:

::: zone pivot="client-operating-system-windows-command"

```console
SET SPN_AUTHORITY=https://login.microsoftonline.com
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```PowerShell
$Env:SPN_AUTHORITY='https://login.microsoftonline.com'
```

::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```console
export SPN_AUTHORITY='https://login.microsoftonline.com'
```

::: zone-end

Ujistěte se, že jsou nastavené všechny požadované proměnné prostředí, pokud chcete:


::: zone pivot="client-operating-system-powershell"

```PowerShell
$Env:SPN_TENANT_ID
$Env:SPN_CLIENT_ID
$Env:SPN_CLIENT_SECRET
$Env:SPN_AUTHORITY
```


::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```console
echo $SPN_TENANT_ID
echo $SPN_CLIENT_ID
echo $SPN_CLIENT_SECRET
echo $SPN_AUTHORITY
```

::: zone-end

::: zone pivot="client-operating-system-windows-command"

```console
echo %SPN_TENANT_ID%
echo %SPN_CLIENT_ID%
echo %SPN_CLIENT_SECRET%
echo %SPN_AUTHORITY%
```

::: zone-end

## <a name="upload-metrics-to-azure-monitor"></a>Nahrát metriky do Azure Monitor

Pokud chcete nahrát metriky pro spravované instance Azure s podporou ARC a Azure ARC s povoleným PostgreSQL, spusťte následující příkazy rozhraní příkazového řádku:

1. Přihlaste se k řadiči dat pomocí `azdata` .
 
1. Exportovat všechny metriky do zadaného souboru:

   ```console
   azdata arc dc export --type metrics --path metrics.json
   ```

2. Nahrajte metriky do Azure monitoru:

   ```console
   azdata arc dc upload --path metrics.json
   ```

   >[!NOTE]
   >Po prvním odeslání vyčkejte aspoň 30 minut, než se vytvoří instance dat s povoleným ARC Azure.
   >
   >Ujistěte `upload` se, že metriky hned po `export` Azure monitor akceptují jenom metriky za posledních 30 minut. [Další informace](../../azure-monitor/essentials/metrics-store-custom-rest-api.md#troubleshooting).


Pokud se při exportu zobrazí nějaké chyby znamenající "selhání získání metrik", zkontrolujte, jestli je shromažďování dat nastavené `true` spuštěním tohoto příkazu:

```console
azdata arc dc config show
```

Podívejte se na část zabezpečení.

```output
 "security": {
      "allowDumps": true,
      "allowNodeMetricsCollection": true,
      "allowPodMetricsCollection": true,
      "allowRunAsRoot": false
    },
```

Ověřte, zda `allowNodeMetricsCollection` `allowPodMetricsCollection` jsou vlastnosti a nastaveny na hodnotu `true` .

## <a name="view-the-metrics-in-the-portal"></a>Zobrazení metrik na portálu

Jakmile budou vaše metriky nahrány, můžete je zobrazit z Azure Portal.
> [!NOTE]
> Všimněte si, že může trvat několik minut, než se nahraná data zpracují, než budete moct zobrazit metriky na portálu.


Pokud chcete metriky zobrazit na portálu, pomocí tohoto odkazu otevřete portál: <https://portal.azure.com> pak na panelu hledání vyhledejte instanci databáze podle názvu:

Využití CPU můžete zobrazit na stránce Přehled, nebo pokud chcete podrobnější metriky, můžete v levém navigačním panelu kliknout na metriky.

Jako obor názvů metriky vyberte SQL Server:

Vyberte metriku, kterou chcete vizualizovat (můžete také vybrat více):

Změňte četnost na posledních 30 minut:

> [!NOTE]
> Metriky můžete nahrávat jenom za posledních 30 minut. Azure Monitor odmítne metriky starší než 30 minut.

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

## <a name="general-guidance-on-exporting-and-uploading-usage-metrics"></a>Obecné pokyny pro export a nahrávání využití, metriky

Operace vytvoření, čtení, aktualizace a odstranění (CRUD) na datových službách s podporou ARC Azure jsou protokolovány pro účely fakturace a monitorování. K dispozici jsou služby na pozadí, které sledují tyto operace CRUD a patřičně vypočítávají spotřebu. Skutečný výpočet využití nebo spotřeby probíhá na plánovaném základu a provede se na pozadí. 

Během období Preview se tento proces děje v noci. Obecně platí, že je možné nahrávat použití jenom jednou za den. Pokud se informace o použití exportují a nahrají víckrát během jednoho období 24 hodin, aktualizuje se v Azure Portal jenom inventář prostředků, ale ne využití prostředků.

V případě nahrávání metrik akceptuje Azure monitor jenom posledních 30 minut dat ([Další informace](../../azure-monitor/essentials/metrics-store-custom-rest-api.md#troubleshooting)). Pokyny pro nahrávání metrik je odeslání metrik ihned po vytvoření souboru exportu, takže můžete zobrazit celou sadu dat v Azure Portal. Pokud jste například exportovali metriky na 2:00 PM a spustili jste příkaz pro nahrání na 2:50 odp. Vzhledem k tomu, že Azure Monitor akceptuje jenom data za posledních 30 minut, na portálu se nemusí zobrazovat žádná data. 

## <a name="next-steps"></a>Další kroky

[Odeslat protokoly do Azure Monitor](upload-logs.md)

[Nahrajte data o využití, metriky a protokoly do Azure Monitor](upload-usage-data.md)

[Nahrání fakturačních dat do Azure a jejich zobrazení v Azure Portal](view-billing-data-in-azure.md)

[Zobrazit prostředek datového kontroleru Azure ARC v Azure Portal](view-data-controller-in-azure-portal.md)
