---
title: Výstupní dávkování v Síti událostí Azure IoT Edge | Dokumenty společnosti Microsoft
description: Výstupní dávkování v mřížce událostí na IoT Edge.
author: HiteshMadan
manager: rajarv
ms.author: himad
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: a6f033af34088081090251f2e5e7cd4a07ce43cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76841743"
---
# <a name="output-batching"></a>Dávkování výstupu

Event Grid má podporu pro doručení více než jedné události v jedné žádosti o doručení. Tato funkce umožňuje zvýšit celkovou propustnost doručení bez placení režie HTTP na požadavek. Dávkování je ve výchozím nastavení vypnuté a lze zapnout na jedno předplatné.

> [!WARNING]
> Maximální povolená doba trvání zpracování každého požadavku na doručení se nezmění, i když kód odběratele potenciálně musí provést více práce na dávkový požadavek. Časový limit doručení je výchozí na 60 sekund.

## <a name="batching-policy"></a>Zásady dávkování

Dávkové chování event gridu lze přizpůsobit na odběratele, a to vyladěním následujících dvou nastavení:

* Maximální počet událostí na dávku

  Toto nastavení nastaví horní limit počtu událostí, které lze přidat do dávkového požadavku na doručení.

* Upřednostňovaná velikost dávky v kilobajtech

  Tento knoflík se používá k dalšímu řízení maximálního počtu kilobajtů, které lze odeslat na žádost o doručení

## <a name="batching-behavior"></a>Dávkové chování

* Všechny nebo žádné

  Event Grid pracuje s sémantikou "vše nebo žádná". Nepodporuje částečný úspěch dávkové dodávky. Odběratelé by měli být opatrní pouze požádat o tolik událostí na dávku, jak mohou rozumně zpracovat za 60 sekund.

* Optimistické dávkování

  Nastavení zásad dávkování nejsou přísné hranice na dávkování chování a jsou respektovány na základě nejlepší úsilí. Při nízkých sazbách událostí často zjistíte, že velikost dávky je menší než požadovaná maximální událost na dávku.

* Výchozí hodnota je nastavena na hodnotu VYPNUTO.

  Ve výchozím nastavení aplikace Event Grid přidá ke každé žádosti o doručení pouze jednu událost. Způsob, jak zapnout dávkování je nastavit jedno z nastavení uvedených výše v článku v odběru událostí JSON.

* Výchozí hodnoty

  Při vytváření předplatného událostí není nutné zadávat nastavení (Maximální počet událostí na dávku a Přibližná velikost dávky v kilobajtech). Pokud je nastaveno pouze jedno nastavení, funkce Event Grid používá (konfigurovatelné) výchozí hodnoty. Výchozí hodnoty a postup jejich přepsání naleznete v následujících částech.

## <a name="turn-on-output-batching"></a>Zapnutí výstupního dávkování

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

Následující nastavení času nasazení řídí maximální povolenou hodnotu při vytváření odběru událostí.

| Název vlastnosti | Popis |
| ------------- | ----------- | 
| `api__deliveryPolicyLimits__maxpreferredBatchSizeInKilobytes` | Maximální povolená `PreferredBatchSizeInKilobytes` hodnota knoflíku. Výchozí `1033`nastavení .
| `api__deliveryPolicyLimits__maxEventsPerBatch` | Maximální povolená `MaxEventsPerBatch` hodnota knoflíku. Výchozí `50`nastavení .

## <a name="configuring-runtime-default-values"></a>Konfigurace výchozích hodnot za běhu

Následující nastavení času nasazení řídí výchozí hodnotu runtime každého knoflíku, pokud není zadána v předplatném událostí. Chcete-li zopakovat, musí být v předplatném události nastaven alespoň jeden knoflík, aby bylo možné zapnout dávkování chování.

| Název vlastnosti | Popis |
| ------------- | ----------- |
| `broker__defaultMaxBatchSizeInBytes` | Maximální velikost požadavku `MaxEventsPerBatch` na doručení, pokud je zadán pouze. Výchozí `1_058_576`nastavení .
| `broker__defaultMaxEventsPerBatch` | Maximální počet událostí, které mají `MaxBatchSizeInBytes` být zahrnuty do dávky, pokud je zadán pouze. Výchozí `10`nastavení .
