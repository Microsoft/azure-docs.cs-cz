---
title: Limity požadavků a omezování
description: Popisuje, jak používat omezení s požadavky Azure Resource Manager u požadavků předplatného bylo dosaženo.
ms.topic: conceptual
ms.date: 03/24/2020
ms.custom: seodec18
ms.openlocfilehash: 4d387749261747eb9ea1ea26629ade4fe8729856
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80239362"
---
# <a name="throttling-resource-manager-requests"></a>Omezování požadavků Resource Manageru

Tento článek popisuje, jak Azure Resource Manager omezuje požadavky. Ukazuje, jak sledovat počet požadavků, které zůstávají před dosažením limitu, a jak reagovat, když dosáhnete limitu.

Škrcení se děje na dvou úrovních. Azure Resource Manager omezuje požadavky na předplatné a klienta. Pokud je požadavek pod omezení omezení pro předplatné a klienta, Resource Manager směruje požadavek na poskytovatele prostředků. Zprostředkovatel prostředků použije omezení omezení, které jsou přizpůsobeny jeho operacím. Následující obrázek ukazuje, jak omezení se použije jako požadavek přejde od uživatele do Správce prostředků Azure a poskytovatele prostředků.

![Požadavek na omezení](./media/request-limits-and-throttling/request-throttling.svg)

## <a name="subscription-and-tenant-limits"></a>Omezení předplatného a tenanta

Na každou operaci na úrovni předplatného a na úrovni tenanta se vztahují omezení omezení. Požadavky na předplatné jsou ty, které zahrnují předávání ID předplatného, jako je například načítání skupin prostředků ve vašem předplatném. Požadavky klienta nezahrnují id předplatného, jako je například načítání platných umístění Azure.

Výchozí omezení omezení za hodinu jsou uvedeny v následující tabulce.

| Rozsah | Provoz | Omezení |
| ----- | ---------- | ------- |
| Předplatné | Čte | 12000 |
| Předplatné | Odstraní | 15 000 |
| Předplatné | Píše | 1200 |
| Tenant | Čte | 12000 |
| Tenant | Píše | 1200 |

Tyto limity se vztahují na objekt zabezpečení (uživatele nebo aplikaci), který zadává požadavky, a na ID předplatného nebo ID tenanta. Pokud vaše požadavky pocházejí z více objektů zabezpečení, bude váš limit v rámci daného předplatného nebo tenanta větší než 12 000 a 1 200 za hodinu.

Tato omezení platí pro každou instanci Azure Resource Manager. V každé oblasti Azure je víc instancí a Azure Resource Manager se nasadí do všech oblastí Azure.  V praxi jsou tedy limity vyšší než tyto limity. Požadavky od uživatele jsou obvykle zpracovány různými instancemi Správce prostředků Azure.

## <a name="resource-provider-limits"></a>Omezení zprostředkovatele prostředků

Zprostředkovatelé prostředků použít vlastní omezení omezení. Vzhledem k tomu, že Správce prostředků omezuje podle ID primárního a instance Správce prostředků, může poskytovatel prostředků obdržet více požadavků než výchozí limity v předchozí části.

Tato část popisuje omezení omezení některých široce používaných zprostředkovatelů prostředků.

### <a name="storage-throttling"></a>Omezení úložiště

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

### <a name="network-throttling"></a>Omezení sítě

Poskytovatel prostředků microsoft.network používá následující omezení omezení:

| Operace | Omezení |
| --------- | ----- |
| zápis / smazat (PUT) | 1000 za 5 minut |
| čtení (GET) | 10000 za 5 minut |

### <a name="compute-throttling"></a>Omezení výpočtu

Informace o omezení omezení výpočetních operací, najdete [v tématu poradce při potížích s chybami omezení rozhraní API - výpočetní prostředky](../../virtual-machines/troubleshooting/troubleshooting-throttling-errors.md).

Pro kontrolu instancí virtuálních strojů v rámci škálovací sady virtuálních strojů použijte [operace škálovací sady virtuálních strojů](/rest/api/compute/virtualmachinescalesetvms). Například použijte [virtuální počítače Škálovací sada virtuálních počítačů - seznam](/rest/api/compute/virtualmachinescalesetvms/list) s parametry ke kontrole stavu napájení instancí virtuálních strojů. Toto rozhraní API snižuje počet požadavků.

### <a name="azure-resource-graph-throttling"></a>Omezení grafu prostředků Azure

[Azure Resource Graph](../../governance/resource-graph/overview.md) omezuje počet požadavků na jeho operace. Kroky v tomto článku k určení zbývající požadavky a jak reagovat při dosažení limitu platí také pro graf prostředků. Aplikace Resource Graph však nastaví vlastní limit a rychlost obnovení. Další informace naleznete v tématu [Omezení záhlaví grafu prostředků](../../governance/resource-graph/concepts/guidance-for-throttled-requests.md#understand-throttling-headers).

## <a name="error-code"></a>Kód chyby

Po dosažení limitu obdržíte stavový kód HTTP **429 Příliš mnoho požadavků**. Odpověď obsahuje hodnotu **Retry-After,** která určuje počet sekund, po které by aplikace měla čekat (nebo v režimu spánku) před odesláním dalšího požadavku. Pokud odešlete požadavek před uplynutím hodnoty opakování, váš požadavek není zpracován a je vrácena nová hodnota opakování.

Po čekání na zadaný čas můžete také zavřít a znovu otevřít připojení k Azure. Resetováním připojení se můžete připojit k jiné instanci Správce prostředků Azure.

Pokud používáte Azure SDK, sada SDK může mít konfiguraci automatického opakování. Další informace najdete [v tématu Opakování pokyny pro služby Azure](/azure/architecture/best-practices/retry-service-specific).

Někteří poskytovatelé prostředků vrátí 429 nahlásit dočasný problém. Problém může být přetížení podmínku, která není přímo způsobena váš požadavek. Nebo může představovat dočasnou chybu o stavu cílového prostředku nebo závislého prostředku. Například poskytovatel síťového prostředku vrátí 429 s chybovým kódem **RetryableErrorDueToAnotherOperation,** když je cílový prostředek uzamčen jinou operací. Chcete-li zjistit, zda chyba pochází z omezení nebo dočasné podmínky, zobrazit podrobnosti o chybě v odpovědi.

## <a name="remaining-requests"></a>Zbývající požadavky

Počet zbývajících požadavků můžete určit kontrolou záhlaví odpovědí. Požadavky na čtení vrátí hodnotu v hlavičce pro počet zbývajících požadavků na čtení. Požadavky na zápis obsahují hodnotu počtu zbývajících požadavků na zápis. Následující tabulka popisuje záhlaví odpovědí, které můžete prozkoumat pro tyto hodnoty:

| Hlavička odpovědi | Popis |
| --- | --- |
| x-ms-ratelimit-remaining-subscription-reads |Odběr rozsahem čtení zbývající. Tato hodnota je vrácena při operacích čtení. |
| x-ms-ratelimit-remaining-subscription-writes |Zbývající zápisy s rozsahem předplatného. Tato hodnota je vrácena při operacích zápisu. |
| x-ms-ratelimit-remaining-tenant-reads |Zbývající čtení s rozsahem klienta |
| x-ms-ratelimit-remaining-tenant-writes |Zbývající zápisy s rozsahem klienta |
| x-ms-ratelimit-remain-subscription-resource-requests x-ms-ratelimit-remaining-subscription-resource-requests x-ms |Zbývající požadavky typu prostředku s rozsahem předplatného.<br /><br />Tato hodnota záhlaví je vrácena pouze v případě, že služba přepsala výchozí limit. Resource Manager přidá tuto hodnotu namísto odběr čtení nebo zápisy. |
| x-ms-ratelimit-remain-subscription-resource-entities-read |Zbývající požadavky na shromažďování typů prostředků s rozsahem předplatného.<br /><br />Tato hodnota záhlaví je vrácena pouze v případě, že služba přepsala výchozí limit. Tato hodnota poskytuje počet zbývajících požadavků na kolekci (zdroje seznamu). |
| x-ms-ratelimit-remaining-tenant-resource-requests x-ms-ratelimit-remaining-tenant-resource-requests x-ms |Zbývající požadavky typu prostředku s rozsahem klienta.<br /><br />Tato hlavička je přidána pouze pro požadavky na úrovni klienta a pouze v případě, že služba přepsala výchozí limit. Správce prostředků přidá tuto hodnotu namísto klienta čte nebo zapisuje. |
| x-ms-ratelimit-remaining-tenant-resource-entities-read |Zbývající požadavky na shromažďování typů prostředků s oborem klienta zůstávají.<br /><br />Tato hlavička je přidána pouze pro požadavky na úrovni klienta a pouze v případě, že služba přepsala výchozí limit. |

Poskytovatel prostředků může také vrátit hlavičky odpovědí s informacemi o zbývajících požadavcích. Informace o hlavičkách odpovědí vrácených poskytovatelem výpočetních prostředků naleznete v tématu [Volání informačních hlaviček odpovědí](../../virtual-machines/troubleshooting/troubleshooting-throttling-errors.md#call-rate-informational-response-headers).

## <a name="retrieving-the-header-values"></a>Načítání hodnot záhlaví

Načítání těchto hodnot záhlaví v kódu nebo skriptu se neliší od načítání libovolné hodnoty záhlaví. 

Například v **c#** načtete hodnotu záhlaví z objektu **HttpWebResponse** s názvem **odpověď** s následujícím kódem:

```cs
response.Headers.GetValues("x-ms-ratelimit-remaining-subscription-reads").GetValue(0)
```

V **prostředí PowerShell**načtete hodnotu záhlaví z operace Invoke-WebRequest.

```powershell
$r = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/{guid}/resourcegroups?api-version=2016-09-01 -Method GET -Headers $authHeaders
$r.Headers["x-ms-ratelimit-remaining-subscription-reads"]
```

Úplný příklad Prostředí PowerShell najdete v [tématu Kontrola omezení správce prostředků pro předplatné](https://github.com/Microsoft/csa-misc-utils/tree/master/psh-GetArmLimitsViaAPI).

Pokud chcete zobrazit zbývající požadavky na ladění, můžete zadat parametr **-Debug** v rutině **prostředí PowerShell.**

```powershell
Get-AzResourceGroup -Debug
```

Který vrátí mnoho hodnot, včetně následující hodnoty odpovědi:

```output
DEBUG: ============================ HTTP RESPONSE ============================

Status Code:
OK

Headers:
Pragma                        : no-cache
x-ms-ratelimit-remaining-subscription-reads: 11999
```

Chcete-li získat omezení zápisu, použijte operaci zápisu: 

```powershell
New-AzResourceGroup -Name myresourcegroup -Location westus -Debug
```

Který vrací mnoho hodnot, včetně následujících hodnot:

```output
DEBUG: ============================ HTTP RESPONSE ============================

Status Code:
Created

Headers:
Pragma                        : no-cache
x-ms-ratelimit-remaining-subscription-writes: 1199
```

V **azure cli**načíst hodnotu záhlaví pomocí podrobnější možnost.

```azurecli
az group list --verbose --debug
```

Který vrací mnoho hodnot, včetně následujících hodnot:

```output
msrest.http_logger : Response status: 200
msrest.http_logger : Response headers:
msrest.http_logger :     'Cache-Control': 'no-cache'
msrest.http_logger :     'Pragma': 'no-cache'
msrest.http_logger :     'Content-Type': 'application/json; charset=utf-8'
msrest.http_logger :     'Content-Encoding': 'gzip'
msrest.http_logger :     'Expires': '-1'
msrest.http_logger :     'Vary': 'Accept-Encoding'
msrest.http_logger :     'x-ms-ratelimit-remaining-subscription-reads': '11998'
```

Chcete-li získat omezení zápisu, použijte operaci zápisu: 

```azurecli
az group create -n myresourcegroup --location westus --verbose --debug
```

Který vrací mnoho hodnot, včetně následujících hodnot:

```output
msrest.http_logger : Response status: 201
msrest.http_logger : Response headers:
msrest.http_logger :     'Cache-Control': 'no-cache'
msrest.http_logger :     'Pragma': 'no-cache'
msrest.http_logger :     'Content-Length': '163'
msrest.http_logger :     'Content-Type': 'application/json; charset=utf-8'
msrest.http_logger :     'Expires': '-1'
msrest.http_logger :     'x-ms-ratelimit-remaining-subscription-writes': '1199'
```

## <a name="next-steps"></a>Další kroky

* Úplný příklad Prostředí PowerShell najdete v [tématu Kontrola omezení správce prostředků pro předplatné](https://github.com/Microsoft/csa-misc-utils/tree/master/psh-GetArmLimitsViaAPI).
* Další informace o omezeních a kvótách najdete v [tématu Limity předplatného a služeb Azure, kvóty a omezení](../../azure-resource-manager/management/azure-subscription-service-limits.md).
* Další informace o zpracování asynchronních požadavků REST najdete v tématu [Sledování asynchronních operací Azure](async-operations.md).
