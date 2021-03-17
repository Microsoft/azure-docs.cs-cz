---
title: Výstup dávkování v Azure Event Grid IoT Edge | Microsoft Docs
description: Výstup dávkování v Event Grid IoT Edge.
author: HiteshMadan
manager: rajarv
ms.author: himad
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 0ae2261f8278c4d5e1944b01a9731afd293df20b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86171631"
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

| Název vlastnosti | Description |
| ------------- | ----------- | 
| `api__deliveryPolicyLimits__maxpreferredBatchSizeInKilobytes` | Maximální povolená hodnota `PreferredBatchSizeInKilobytes` ovladače Výchozí hodnota `1033` .
| `api__deliveryPolicyLimits__maxEventsPerBatch` | Maximální povolená hodnota `MaxEventsPerBatch` ovladače Výchozí hodnota `50` .

## <a name="configuring-runtime-default-values"></a>Konfigurace výchozích hodnot modulu runtime

Následující nastavení času nasazení řídí výchozí hodnotu modulu runtime každého ovladače, pokud není zadána v odběru události. Chcete-li provést iteraci, musí být v odběru událostí nastaven alespoň jeden ovladač, aby bylo možné zapnout dávkování chování.

| Název vlastnosti | Description |
| ------------- | ----------- |
| `broker__defaultMaxBatchSizeInBytes` | Maximální velikost žádosti o doručení `MaxEventsPerBatch` , pokud je určena pouze. Výchozí hodnota `1_058_576` .
| `broker__defaultMaxEventsPerBatch` | Maximální počet událostí, které mají být přidány do dávky, pokud `MaxBatchSizeInBytes` je určena pouze hodnota. Výchozí hodnota `10` .
