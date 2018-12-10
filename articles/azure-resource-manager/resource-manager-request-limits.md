---
title: Omezení počtu požadavků a omezení – Azure Resource Manageru
description: Popisuje způsob použití omezení požadavků Azure Resource Manageru a pokud bylo dosaženo omezení předplatného.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.assetid: e1047233-b8e4-4232-8919-3268d93a3824
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/09/2018
ms.author: tomfitz
ms.custom: seodec18
ms.openlocfilehash: 0ba4a1a4119db515e10c0b704b0a10501fe79682
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53136885"
---
# <a name="throttling-resource-manager-requests"></a>Omezování požadavků Resource Manageru
Pro každé předplatné Azure a tenanta Resource Manager umožňuje až 12 000 požadavků za hodinu na čtení a zápis 1 200 žádosti za hodinu. Tato omezení jsou omezená požadavků ID objektu zabezpečení a ID předplatného nebo tenanta ID. Pokud vaše požadavky pocházejí z více než jeden ID instančního objektu, je větší než 12 000 a 1 200 za hodinu limitu vašeho předplatného nebo tenanta.

Požadavky se použijí pro vaše předplatné nebo vašeho tenanta. Žádosti jsou ty zahrnují předání vašeho předplatného, ID, třeba načítání skupin prostředků ve vašem předplatném. Požadavky klientů jsou ID vašeho předplatného, třeba načítání platných umístění Azure.

Tato omezení platí pro každou instanci Azure Resource Manageru. Existuje více instancí ve všech oblastech Azure a Azure Resource Manageru je nasazený do všech oblastí Azure.  Takže v praxi, jsou efektivně mnohem vyšší než tato omezení limitů, jako uživatel požadavky jsou obvykle obsluhovány pomocí mnoha různých instancích.

Pokud vaše aplikace nebo skript dosáhne těchto limitů, budete muset omezení požadavků. V tomto článku se dozvíte, jak určit zbývajících požadavků, které je nutné před dosažením limitu a týkající se reakce, když jste dosáhli limitu.

Pokud limit překročíte, obdržíte kód stavu HTTP **429 příliš mnoho požadavků**.

## <a name="remaining-requests"></a>Zbývajících požadavků
Počet zbývajících požadavků, které můžete určit tím, že kontroluje hlavičky odpovědi. Každý požadavek obsahuje hodnoty pro počet zbývajících pro čtení a požadavky na zápis. Následující tabulka popisuje hlavičky odpovědi, které můžete zkontrolovat pro tyto hodnoty:

| Hlavička odpovědi | Popis |
| --- | --- |
| x-ms-ratelimit-remaining-subscription-reads |Předplatné s rozsahem načte zbývající. Tato hodnota se vrátí na operace čtení. |
| x-ms-ratelimit-remaining-subscription-writes |Předplatné s rozsahem zapíše zbývající. Tato hodnota se vrátí na operace zápisu. |
| x-ms-ratelimit-remaining-tenant-reads |Tenant s rozsahem načte zbývající |
| x-ms-ratelimit-remaining-tenant-writes |Zapíše tenanta s rozsahem zbývající |
| x-ms-ratelimit-remaining-subscription-resource-requests |Zbývající žádosti typu prostředku v oboru předplatného.<br /><br />Hodnotu této hlavičky je vrácena pouze v případě, že služba má přepsat výchozí omezení. Resource Manager přidá tuto hodnotu místo předplatné čtení nebo zápisu. |
| x-ms-ratelimit-remaining-subscription-resource-entities-read |Zbývající požadavky kolekce typu prostředku v oboru předplatného.<br /><br />Hodnotu této hlavičky je vrácena pouze v případě, že služba má přepsat výchozí omezení. Tato hodnota představuje počet zbývajících požadavků kolekce (výpis prostředků). |
| x-MS-ratelimit-Remaining-tenant-Resource-Requests |Zbývající žádosti typu prostředku v oboru tenanta.<br /><br />Tato hlavička se přidá pouze pro požadavky na úrovni tenanta a pouze v případě služby má přepsat výchozí omezení. Resource Manager přidá tuto hodnotu místo tenanta čtení nebo zápisu. |
| x-ms-ratelimit-remaining-tenant-resource-entities-read |Zbývající požadavky kolekce typu prostředku v oboru tenanta.<br /><br />Tato hlavička se přidá pouze pro požadavky na úrovni tenanta a pouze v případě služby má přepsat výchozí omezení. |

## <a name="retrieving-the-header-values"></a>Načítání hodnoty hlavičky
Načítají se tyto hodnoty hlavičky v kódu nebo skriptu se nijak neliší od načítání libovolnou hodnotu hlavičky. 

Například v **jazyka C#**, načíst hodnota hlavičky ze **HttpWebResponse** objekt s názvem **odpovědi** následujícím kódem:

```cs
response.Headers.GetValues("x-ms-ratelimit-remaining-subscription-reads").GetValue(0)
```

V **Powershellu**, načíst hodnotu hlavičky z operace Invoke-WebRequest.

```powershell
$r = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/{guid}/resourcegroups?api-version=2016-09-01 -Method GET -Headers $authHeaders
$r.Headers["x-ms-ratelimit-remaining-subscription-reads"]
```

Kompletní příklad Powershellu najdete v části [zkontrolujte omezení Resource Manageru a předplatné](https://github.com/Microsoft/csa-misc-utils/tree/master/psh-GetArmLimitsViaAPI).

Pokud chcete zobrazit zbývající požadavky pro ladění, můžete zadat **– ladění** parametr na vaši **Powershellu** rutiny.

```powershell
Get-AzureRmResourceGroup -Debug
```

Který vrátí více hodnot, včetně následujících hodnota odpovědi:

```powershell
DEBUG: ============================ HTTP RESPONSE ============================

Status Code:
OK

Headers:
Pragma                        : no-cache
x-ms-ratelimit-remaining-subscription-reads: 14999
```

Zápis omezení získáte pomocí operace zápisu: 

```powershell
New-AzureRmResourceGroup -Name myresourcegroup -Location westus -Debug
```

Který vrátí více hodnot, včetně následujících hodnot:

```powershell
DEBUG: ============================ HTTP RESPONSE ============================

Status Code:
Created

Headers:
Pragma                        : no-cache
x-ms-ratelimit-remaining-subscription-writes: 1199
```

V **rozhraní příkazového řádku Azure**, načtete pomocí možnosti podrobnější hodnotu hlavičky.

```azurecli
az group list --verbose --debug
```

Který vrátí více hodnot, včetně následujících hodnot:

```azurecli
msrest.http_logger : Response status: 200
msrest.http_logger : Response headers:
msrest.http_logger :     'Cache-Control': 'no-cache'
msrest.http_logger :     'Pragma': 'no-cache'
msrest.http_logger :     'Content-Type': 'application/json; charset=utf-8'
msrest.http_logger :     'Content-Encoding': 'gzip'
msrest.http_logger :     'Expires': '-1'
msrest.http_logger :     'Vary': 'Accept-Encoding'
msrest.http_logger :     'x-ms-ratelimit-remaining-subscription-reads': '14998'
```

Zápis omezení získáte pomocí operace zápisu: 

```azurecli
az group create -n myresourcegroup --location westus --verbose --debug
```

Který vrátí více hodnot, včetně následujících hodnot:

```azurecli
msrest.http_logger : Response status: 201
msrest.http_logger : Response headers:
msrest.http_logger :     'Cache-Control': 'no-cache'
msrest.http_logger :     'Pragma': 'no-cache'
msrest.http_logger :     'Content-Length': '163'
msrest.http_logger :     'Content-Type': 'application/json; charset=utf-8'
msrest.http_logger :     'Expires': '-1'
msrest.http_logger :     'x-ms-ratelimit-remaining-subscription-writes': '1199'
```

## <a name="waiting-before-sending-next-request"></a>Čekání před odesláním další požadavek
Při dosažení limitu požadavku Resource Manageru vrátí **429** stavový kód HTTP a **Retry-After** hodnotou v hlavičce. **Retry-After** hodnota určuje počet sekund, které vaše aplikace by měl čekat (nebo v režimu spánku) než pošle další požadavek. Pokud odešlete žádost o před uplynutí hodnota pro opakovaný pokus, váš požadavek není zpracován a vrátí se nová hodnota pro opakovaný pokus.

## <a name="next-steps"></a>Další postup

* Kompletní příklad Powershellu najdete v části [zkontrolujte omezení Resource Manageru a předplatné](https://github.com/Microsoft/csa-misc-utils/tree/master/psh-GetArmLimitsViaAPI).
* Další informace o omezení a kvóty najdete v tématu [předplatného Azure a limity, kvóty a omezení](../azure-subscription-service-limits.md).
* Další informace o zpracování asynchronního požadavky REST, naleznete v tématu [sledování asynchronních operací Azure](resource-manager-async-operations.md).
