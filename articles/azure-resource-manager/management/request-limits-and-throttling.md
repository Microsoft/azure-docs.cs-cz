---
title: Limity požadavků a omezování
description: Popisuje, jak používat omezování s požadavky na Azure Resource Manager, když se dosáhlo limitu předplatného.
ms.topic: conceptual
ms.date: 12/15/2020
ms.custom: seodec18
ms.openlocfilehash: c1fd707bf9f13ed88bf9acb5d2ad70d56c453a90
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105024621"
---
# <a name="throttling-resource-manager-requests"></a>Omezování požadavků Resource Manageru

Tento článek popisuje, jak Azure Resource Manager omezuje požadavky. Ukazuje, jak sledovat počet požadavků, které zůstanou před dosažením limitu, a jak reagovat na to, kdy jste dosáhli limitu.

K omezování dochází na dvou úrovních. Azure Resource Manager omezuje požadavky na předplatné a tenanta. Pokud se žádost nachází v omezeních omezování pro předplatné a tenanta, Správce prostředků směruje požadavek poskytovateli prostředků. Poskytovatel prostředků používá omezení omezování, která jsou přizpůsobená jeho operacím. Následující obrázek ukazuje, jak se použití omezování aplikuje jako požadavek od uživatele po Azure Resource Manager a poskytovatele prostředků.

![Omezování požadavků](./media/request-limits-and-throttling/request-throttling.svg)

## <a name="subscription-and-tenant-limits"></a>Předplatné a omezení tenanta

Každá operace na úrovni předplatného a tenanta na úrovni klienta podléhá omezením omezení. Žádosti o předplatné jsou ty, které zahrnují předávání ID předplatného, jako je například načtení skupin prostředků ve vašem předplatném. Žádosti klientů neobsahují vaše ID předplatného, například načtení platných umístění Azure.

V následující tabulce jsou uvedeny výchozí limity omezení za hodinu.

| Obor | Operace | Omezení |
| ----- | ---------- | ------- |
| Předplatné | operace | 12000 |
| Předplatné | odstraníte | 15 000 |
| Předplatné | pisoval | 1200 |
| Tenant | operace | 12000 |
| Tenant | pisoval | 1200 |

Tyto limity se vztahují na objekt zabezpečení (uživatele nebo aplikaci), který zadává požadavky, a na ID předplatného nebo ID tenanta. Pokud vaše požadavky pocházejí z více objektů zabezpečení, bude váš limit v rámci daného předplatného nebo tenanta větší než 12 000 a 1 200 za hodinu.

Tato omezení platí pro jednotlivé instance Azure Resource Manager. Každá oblast Azure obsahuje několik instancí a Azure Resource Manager je nasazená do všech oblastí Azure.  V praxi proto omezení jsou vyšší než tato omezení. Žádosti od uživatele jsou obvykle zpracovávány různými instancemi Azure Resource Manager.

## <a name="resource-provider-limits"></a>Omezení poskytovatele prostředků

Poskytovatelé prostředků používají vlastní omezení omezení. Vzhledem k tomu, že Správce prostředků omezení podle ID objektu zabezpečení a podle instance Správce prostředků, může poskytovatel prostředků obdržet více požadavků než výchozí omezení v předchozí části.

Tato část popisuje omezení omezení některých široce používaných poskytovatelů prostředků.

### <a name="storage-throttling"></a>Omezování úložiště

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

### <a name="network-throttling"></a>Omezení sítě

Poskytovatel prostředků Microsoft. Network používá následující omezení omezení:

| Operace | Omezení |
| --------- | ----- |
| zapsat/odstranit (PUT) | 1000 za 5 minut |
| čtení (GET) | 10000 za 5 minut |

### <a name="compute-throttling"></a>Omezení výpočetní kapacity

Informace o omezeních omezování pro výpočetní operace najdete v tématu [řešení chyb při omezování rozhraní API – COMPUTE](/troubleshoot/azure/virtual-machines/troubleshooting-throttling-errors).

Pro kontrolu instancí virtuálních počítačů v rámci sady škálování virtuálních počítačů použijte [Virtual Machine Scale Sets operace](/rest/api/compute/virtualmachinescalesetvms). Můžete například použít virtuální počítače [sady škálování virtuálního počítače – seznam](/rest/api/compute/virtualmachinescalesetvms/list) s parametry pro kontrolu stavu napájení instancí virtuálních počítačů. Toto rozhraní API snižuje počet požadavků.

### <a name="azure-resource-graph-throttling"></a>Omezení Azure Resource graphu

[Graf Azure Resource](../../governance/resource-graph/overview.md) omezuje počet požadavků na jeho operace. Kroky v tomto článku k určení zbývajících požadavků a způsobu, jak reagovat na to, jak se dosáhlo limitu, platí také pro graf prostředků. Graf prostředků ale nastaví svůj vlastní limit a rychlost resetování. Další informace najdete v tématu [hlavičky omezení grafu prostředků](../../governance/resource-graph/concepts/guidance-for-throttled-requests.md#understand-throttling-headers).

### <a name="other-resource-providers"></a>Jiní poskytovatelé prostředků

Informace o omezování v jiných poskytovatelích prostředků najdete v těchto tématech:

* [Pokyny k omezování služby Azure Key Vault](../../key-vault/general/overview-throttling.md)
* [Řešení potíží s AKS](../../aks/troubleshooting.md#im-receiving-429---too-many-requests-errors)

## <a name="error-code"></a>Kód chyby

Pokud dosáhnete limitu, obdržíte stavový kód HTTP **429 příliš mnoho požadavků**. Odpověď obsahuje hodnotu opakovat, která určuje počet sekund, **po** jejichž uplynutí má aplikace čekat (nebo přejít do režimu spánku) před odesláním dalšího požadavku. Pokud odešlete žádost před uplynutím hodnoty opakování, vaše žádost se nezpracuje a vrátí se nová hodnota opakování.

Po uplynutí určité doby můžete také zavřít a znovu otevřít připojení k Azure. Resetováním tohoto připojení se můžete připojit k jiné instanci Azure Resource Manager.

Pokud používáte sadu Azure SDK, sada SDK může mít konfiguraci automatického opakování. Další informace najdete v [pokynech k opakování služeb Azure](/azure/architecture/best-practices/retry-service-specific).

Někteří poskytovatelé prostředků vrátí 429, aby nahlásily dočasný problém. Problémem může být podmínka přetížení, která není přímo způsobena vaší žádostí. Nebo může představovat dočasnou chybu týkající se stavu cílového prostředku nebo závislého prostředku. Například poskytovatel síťových prostředků vrátí 429 s kódem chyby **RetryableErrorDueToAnotherOperation** , když je cílový prostředek uzamčen jinou operací. Pokud chcete zjistit, jestli chyba pochází z omezení nebo dočasné podmínky, Prohlédněte si podrobnosti o chybě v odpovědi.

## <a name="remaining-requests"></a>Zbývající požadavky

Počet zbývajících požadavků můžete určit vyzkoumáním hlaviček odpovědi. Žádosti o čtení vrátí hodnotu v hlavičce pro počet zbývajících požadavků na čtení. Požadavky na zápis obsahují hodnotu pro počet zbývajících požadavků na zápis. Následující tabulka popisuje hlavičky odpovědí, které můžete u těchto hodnot ověřit:

| Hlavička odpovědi | Description |
| --- | --- |
| x-MS-ratelimit-zbývající předplatné – čtení |Zbývající čtení v oboru předplatného. Tato hodnota je vrácena při operacích čtení. |
| x-MS-ratelimit-zbývající předplatné-zápisy |Zbývající zápisy v oboru předplatného Tato hodnota je vrácena při operacích zápisu. |
| x-MS-ratelimit-zbývající – čtení z tenanta |Zbývající čtení v oboru klienta |
| x-MS-ratelimit-zbývající – zápisy klientů |Zbývající zápisy v oboru klienta |
| x-MS-ratelimit-zbývá-předplatné-požadavky na prostředek |Zbývající požadavky na typ prostředku v oboru předplatného<br /><br />Tato hodnota hlavičky se vrátí jenom v případě, že služba přepsala výchozí limit. Správce prostředků přidá tuto hodnotu namísto čtení nebo zápisu daného předplatného. |
| x-MS-ratelimit-zbývá-předplatné-prostředek-entity – číst |Zbývá zbývajících požadavků shromažďování typů prostředků v oboru předplatného.<br /><br />Tato hodnota hlavičky se vrátí jenom v případě, že služba přepsala výchozí limit. Tato hodnota udává počet zbývajících požadavků kolekce (seznam prostředků). |
| x-MS-ratelimit-zbývající – tenant-Resource-požadavky |Zbývá požadavků na typ prostředku v oboru klienta.<br /><br />Tato hlavička se přidávají jenom pro požadavky na úrovni tenanta a jenom v případě, že služba přepsala výchozí limit. Správce prostředků přidá tuto hodnotu namísto čtení nebo zápisu klienta. |
| x-MS-ratelimit-zbývá-tenant-Resource-Entities – čtení |Zbývá zbývajících požadavků shromažďování typů prostředků v oboru klienta.<br /><br />Tato hlavička se přidávají jenom pro požadavky na úrovni tenanta a jenom v případě, že služba přepsala výchozí limit. |

Poskytovatel prostředků může také vracet hlavičky odpovědí s informacemi o zbývajících požadavcích. Informace o hlavičkách odpovědí vrácených zprostředkovatelem výpočetních prostředků najdete v tématu přehledové [odezvy – hlavičky informativních odpovědí](/troubleshoot/azure/virtual-machines/troubleshooting-throttling-errors#call-rate-informational-response-headers).

## <a name="retrieving-the-header-values"></a>Načítání hodnot hlaviček

Načítání těchto hodnot hlaviček ve vašem kódu nebo skriptu se neliší od načtení jakékoli hodnoty záhlaví. 

Například v **jazyce C#** načtete hodnotu hlavičky z objektu **HttpWebResponse** s názvem **Response** s následujícím kódem:

```cs
response.Headers.GetValues("x-ms-ratelimit-remaining-subscription-reads").GetValue(0)
```

V **PowerShellu** načtete hodnotu hlavičky z operace Invoke-WebRequest.

```powershell
$r = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/{guid}/resourcegroups?api-version=2016-09-01 -Method GET -Headers $authHeaders
$r.Headers["x-ms-ratelimit-remaining-subscription-reads"]
```

Úplný příklad prostředí PowerShell najdete v tématu [Check správce prostředků Limits pro předplatné](https://github.com/Microsoft/csa-misc-utils/tree/master/psh-GetArmLimitsViaAPI).

Pokud chcete zobrazit zbývající požadavky na ladění, můžete zadat parametr **-Debug** v rutině **prostředí PowerShell** .

```powershell
Get-AzResourceGroup -Debug
```

Který vrací mnoho hodnot včetně následující hodnoty odpovědi:

```output
DEBUG: ============================ HTTP RESPONSE ============================

Status Code:
OK

Headers:
Pragma                        : no-cache
x-ms-ratelimit-remaining-subscription-reads: 11999
```

Chcete-li získat omezení pro zápis, použijte operaci zápisu: 

```powershell
New-AzResourceGroup -Name myresourcegroup -Location westus -Debug
```

Což vrátí mnoho hodnot, včetně následujících hodnot:

```output
DEBUG: ============================ HTTP RESPONSE ============================

Status Code:
Created

Headers:
Pragma                        : no-cache
x-ms-ratelimit-remaining-subscription-writes: 1199
```

V rozhraní příkazového **řádku Azure CLI** načtěte hodnotu hlavičky pomocí možnosti Podrobnější informace.

```azurecli
az group list --verbose --debug
```

Což vrátí mnoho hodnot, včetně následujících hodnot:

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

Chcete-li získat omezení pro zápis, použijte operaci zápisu: 

```azurecli
az group create -n myresourcegroup --location westus --verbose --debug
```

Což vrátí mnoho hodnot, včetně následujících hodnot:

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

* Úplný příklad prostředí PowerShell najdete v tématu [Check správce prostředků Limits pro předplatné](https://github.com/Microsoft/csa-misc-utils/tree/master/psh-GetArmLimitsViaAPI).
* Další informace o omezeních a kvótách najdete v tématu [limity, kvóty a omezení předplatného a služeb Azure](../../azure-resource-manager/management/azure-subscription-service-limits.md).
* Další informace o zpracování asynchronních žádostí REST najdete v tématu [sledování asynchronních operací Azure](async-operations.md).