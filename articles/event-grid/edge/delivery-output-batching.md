---
title: Výstup dávkování v Azure Event Grid IoT Edge | Microsoft Docs
description: Výstup dávkování v Event Grid IoT Edge.
author: HiteshMadan
manager: rajarv
ms.author: himad
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 7d6b83354baf3db5ddb65f94fee1c3dce2dcca94
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992454"
---
# <a name="output-batching"></a>Výstup dávkování

Event Grid podporuje poskytování více než jedné události v rámci jedné žádosti o doručení. Tato funkce umožňuje zvýšit celkovou propustnost doručení bez placení režijních nákladů na požadavek HTTP. Dávkování je ve výchozím nastavení vypnuté a dá se zapnout pro každé předplatné.

> [!WARNING]
> Maximální povolená doba pro zpracování jednotlivých žádostí o doručení se nemění, i když kód předplatitele potenciálně potřebuje k provedení více práce na základě dávky. Časový limit doručení je ve výchozím nastavení nastaven na 60 sekund.

## <a name="batching-policy"></a>Zásady dávkování

Chování dávkování Event Grid se dá přizpůsobit pro každého předplatitele pomocí přizpůsobení následujících dvou nastavení:

* Maximální počet událostí na dávku

  Toto nastavení nastaví horní limit počtu událostí, které lze přidat do dávkového požadavku na doručení.

* Preferovaná velikost dávky v kilobajtech

  Tento knoflík slouží k dalšímu řízení maximálního počtu kilobajtů, které je možné odeslat za požadavek na doručení.

## <a name="batching-behavior"></a>Chování dávkování

* Vše nebo žádné

  Event Grid pracuje se sémantikou All-nebo-None. Neumožňuje částečnou úspěšnost dávkového doručování. Předplatitelé by měli být opatrní, aby se na každou dávku žádali jenom tolik událostí, protože můžou v 60 sekundách rozumně zvládnout.

* Optimistické dávkování

  Nastavení zásad dávkového zpracování není přísné meze pro chování dávkování a jsou dodržovány na základě nejlepšího úsilí. Při nízkých sazbách událostí často obdržíte velikost dávky menší než požadované maximum událostí na dávku.

* Výchozí nastavení je vypnuto.

  Ve výchozím nastavení Event Grid do každé žádosti o doručení přidat jenom jednu událost. Postup zapnutí dávkového zpracování je nastavení jednoho z výše uvedených nastavení v tomto článku v souboru JSON pro odběr události.

* Výchozí hodnoty

  Při vytváření odběru událostí není nutné zadávat nastavení (maximální počet událostí na dávku a přibližnou velikost dávky v kilobajtech). Pokud je nastavené jenom jedno nastavení, Event Grid použije (konfigurovatelné) výchozí hodnoty. V následujících částech jsou uvedeny výchozí hodnoty a jejich přepsání.

## <a name="turn-on-output-batching"></a>Zapnout dávkování výstupu

```json
{
    "properties":
    {
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
             {
                "endpointUrl": "<your_webhook_url>",
                "maxEventsPerBatch": 10,
                "preferredBatchSizeInKilobytes": 64
             }
        },
    }
}
```

## <a name="configuring-maximum-allowed-values"></a>Konfigurace maximálních povolených hodnot

Následující nastavení času nasazení určuje maximální hodnotu povolenou při vytváření odběru události.

| Název vlastnosti | Popis |
| ------------- | ----------- | 
| `api:deliveryPolicyLimits:maxpreferredBatchSizeInKilobytes` | Maximální povolená hodnota ovladače `PreferredBatchSizeInKilobytes`. Výchozí `1033`.
| `api:deliveryPolicyLimits:maxEventsPerBatch` | Maximální povolená hodnota ovladače `MaxEventsPerBatch`. Výchozí `50`.

## <a name="configuring-runtime-default-values"></a>Konfigurace výchozích hodnot modulu runtime

Následující nastavení času nasazení řídí výchozí hodnotu modulu runtime každého ovladače, pokud není zadána v odběru události. Chcete-li provést iteraci, musí být v odběru událostí nastaven alespoň jeden ovladač, aby bylo možné zapnout dávkování chování.

| Název vlastnosti | Popis |
| ------------- | ----------- |
| `broker:defaultMaxBatchSizeInBytes` | Maximální velikost žádosti o doručení, je-li zadána pouze `MaxEventsPerBatch`. Výchozí `1_058_576`.
| `broker:defaultMaxEventsPerBatch` | Maximální počet událostí, které mají být přidány do dávky, pokud je zadána pouze `MaxBatchSizeInBytes`. Výchozí `10`.
