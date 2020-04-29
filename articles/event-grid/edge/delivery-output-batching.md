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
ms.openlocfilehash: a6f033af34088081090251f2e5e7cd4a07ce43cc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "76841743"
---
# <a name="output-batching"></a>Dávkování výstupu

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
| `api__deliveryPolicyLimits__maxpreferredBatchSizeInKilobytes` | Maximální povolená hodnota `PreferredBatchSizeInKilobytes` ovladače Výchozí `1033`hodnota.
| `api__deliveryPolicyLimits__maxEventsPerBatch` | Maximální povolená hodnota `MaxEventsPerBatch` ovladače Výchozí `50`hodnota.

## <a name="configuring-runtime-default-values"></a>Konfigurace výchozích hodnot modulu runtime

Následující nastavení času nasazení řídí výchozí hodnotu modulu runtime každého ovladače, pokud není zadána v odběru události. Chcete-li provést iteraci, musí být v odběru událostí nastaven alespoň jeden ovladač, aby bylo možné zapnout dávkování chování.

| Název vlastnosti | Popis |
| ------------- | ----------- |
| `broker__defaultMaxBatchSizeInBytes` | Maximální velikost žádosti o doručení, `MaxEventsPerBatch` Pokud je určena pouze. Výchozí `1_058_576`hodnota.
| `broker__defaultMaxEventsPerBatch` | Maximální počet událostí, které mají být přidány do dávky, `MaxBatchSizeInBytes` Pokud je určena pouze hodnota. Výchozí `10`hodnota.
