---
title: Omezení požadavků a omezení
description: Popisuje způsob použití omezení požadavků Azure Resource Manageru a pokud bylo dosaženo omezení předplatného.
ms.topic: conceptual
ms.date: 10/26/2019
ms.custom: seodec18
ms.openlocfilehash: b47943d69d209f5a0406c293b5a24c6ac0ad0c10
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150468"
---
# <a name="throttling-resource-manager-requests"></a>Omezování požadavků Resource Manageru

Tento článek popisuje, jak Azure Resource Manager omezuje požadavky. Ukazuje, jak sledovat počet požadavků, které zůstanou před dosažením limitu, a jak reagovat na to, kdy jste dosáhli limitu.

K omezování dochází na dvou úrovních. Azure Resource Manager omezuje požadavky na předplatné a tenanta. Pokud se žádost nachází v omezeních omezování pro předplatné a tenanta, Správce prostředků směruje požadavek poskytovateli prostředků. Poskytovatel prostředků používá omezení omezování, která jsou přizpůsobená jeho operacím. Následující obrázek ukazuje, jak se použití omezování aplikuje jako požadavek od uživatele po Azure Resource Manager a poskytovatele prostředků.

![Omezování požadavků](./media/resource-manager-request-limits/request-throttling.svg)

## <a name="subscription-and-tenant-limits"></a>Předplatné a omezení tenanta

Každá operace na úrovni předplatného a tenanta na úrovni klienta podléhá omezením omezení. Žádosti o předplatné jsou ty, které zahrnují předávání ID předplatného, jako je například načtení skupin prostředků ve vašem předplatném. Požadavky klientů jsou ID vašeho předplatného, třeba načítání platných umístění Azure.

V následující tabulce jsou uvedeny výchozí limity omezení za hodinu.

| Rozsah | Operace | Omezení |
| ----- | ---------- | ------- |
| Předplatné | operace | 12000 |
| Předplatné | odstraníte | 15 000 |
| Předplatné | pisoval | 1200 |
| Tenant | operace | 12000 |
| Tenant | pisoval | 1200 |

Tyto limity se vztahují na objekt zabezpečení (uživatele nebo aplikaci), který zadává požadavky, a na ID předplatného nebo ID tenanta. Pokud vaše požadavky pocházejí z více objektů zabezpečení, bude váš limit v rámci daného předplatného nebo tenanta větší než 12 000 a 1 200 za hodinu.

Tato omezení platí pro každou instanci Azure Resource Manageru. Existuje více instancí ve všech oblastech Azure a Azure Resource Manageru je nasazený do všech oblastí Azure.  V praxi proto omezení jsou vyšší než tato omezení. Žádosti od uživatele jsou obvykle zpracovávány různými instancemi Azure Resource Manager.

## <a name="resource-provider-limits"></a>Omezení poskytovatele prostředků

Poskytovatelé prostředků používají vlastní omezení omezení. Vzhledem k tomu, že Správce prostředků omezení podle ID objektu zabezpečení a podle instance Správce prostředků, může poskytovatel prostředků obdržet více požadavků než výchozí omezení v předchozí části.

Tato část popisuje omezení omezení některých široce používaných poskytovatelů prostředků.

### <a name="storage-throttling"></a>Omezování úložiště

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../includes/azure-storage-limits-azure-resource-manager.md)]

### <a name="network-throttling"></a>Omezení sítě

Poskytovatel prostředků Microsoft. Network používá následující omezení omezení:

| Operace | Omezení |
| --------- | ----- |
| zapsat/odstranit (PUT) | 1000 za 5 minut |
| čtení (GET) | 10000 za 5 minut |

### <a name="compute-throttling"></a>Omezení výpočetní kapacity

Informace o omezeních omezování pro výpočetní operace najdete v tématu [řešení chyb při omezování rozhraní API – COMPUTE](../virtual-machines/troubleshooting/troubleshooting-throttling-errors.md).

Pro kontrolu instancí virtuálních počítačů v rámci sady škálování virtuálních počítačů použijte [Virtual Machine Scale Sets operace](/rest/api/compute/virtualmachinescalesetvms). Můžete například použít virtuální počítače [sady škálování virtuálního počítače – seznam](/rest/api/compute/virtualmachinescalesetvms/list) s parametry pro kontrolu stavu napájení instancí virtuálních počítačů. Toto rozhraní API snižuje počet požadavků.

### <a name="azure-resource-graph-throttling"></a>Omezení Azure Resource graphu

Graf Azure Resource omezuje počet požadavků na jeho operace. Kroky v tomto článku k určení zbývajících požadavků a způsobu, jak reagovat na to, jak se dosáhlo limitu, platí také pro graf prostředků. Graf prostředků ale nastaví svůj vlastní limit a rychlost resetování. Další informace najdete v tématu [Omezení požadavků ve službě Azure Resource Graph](../governance/resource-graph/overview.md#throttling).

## <a name="request-increase"></a>Zvýšení žádosti

V některých případech je možné zvýšit omezení omezení. Pokud chcete zjistit, jestli se omezení omezování pro váš scénář dá zvýšit, vytvořte žádost o podporu. Podrobnosti o způsobu volání budou vyhodnoceny.

## <a name="error-code"></a>Kód chyby

Pokud limit překročíte, obdržíte kód stavu HTTP **429 příliš mnoho požadavků**. Odpověď obsahuje hodnotu opakovat, která určuje počet sekund, **po** jejichž uplynutí má aplikace čekat (nebo přejít do režimu spánku) před odesláním dalšího požadavku. Pokud odešlete žádost o před uplynutí hodnota pro opakovaný pokus, váš požadavek není zpracován a vrátí se nová hodnota pro opakovaný pokus.

Po uplynutí určité doby můžete také zavřít a znovu otevřít připojení k Azure. Resetováním tohoto připojení se můžete připojit k jiné instanci Azure Resource Manager.

Pokud používáte sadu Azure SDK, sada SDK může mít konfiguraci automatického opakování. Další informace najdete v [pokynech k opakování služeb Azure](/azure/architecture/best-practices/retry-service-specific).

Někteří poskytovatelé prostředků vrátí 429, aby nahlásily dočasný problém. Problémem může být podmínka přetížení, která není přímo způsobena vaší žádostí. Nebo může představovat dočasnou chybu týkající se stavu cílového prostředku nebo závislého prostředku. Například poskytovatel síťových prostředků vrátí 429 s kódem chyby **RetryableErrorDueToAnotherOperation** , když je cílový prostředek uzamčen jinou operací. Pokud chcete zjistit, jestli chyba pochází z omezení nebo dočasné podmínky, Prohlédněte si podrobnosti o chybě v odpovědi.

## <a name="remaining-requests"></a>Zbývajících požadavků

Počet zbývajících požadavků, které můžete určit tím, že kontroluje hlavičky odpovědi. Žádosti o čtení vrátí hodnotu v hlavičce pro počet zbývajících požadavků na čtení. Požadavky na zápis obsahují hodnotu pro počet zbývajících požadavků na zápis. Následující tabulka popisuje hlavičky odpovědi, které můžete zkontrolovat pro tyto hodnoty:

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

Poskytovatel prostředků může také vracet hlavičky odpovědí s informacemi o zbývajících požadavcích. Informace o hlavičkách odpovědí vrácených zprostředkovatelem výpočetních prostředků najdete v tématu přehledové [odezvy – hlavičky informativních odpovědí](../virtual-machines/troubleshooting/troubleshooting-throttling-errors.md#call-rate-informational-response-headers).

## <a name="retrieving-the-header-values"></a>Načítání hodnoty hlavičky

Načítají se tyto hodnoty hlavičky v kódu nebo skriptu se nijak neliší od načítání libovolnou hodnotu hlavičky. 

Například v **jazyka C#** , načíst hodnota hlavičky ze **HttpWebResponse** objekt s názvem **odpovědi** následujícím kódem:

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
Get-AzResourceGroup -Debug
```

Který vrátí více hodnot, včetně následujících hodnota odpovědi:

```powershell
DEBUG: ============================ HTTP RESPONSE ============================

Status Code:
OK

Headers:
Pragma                        : no-cache
x-ms-ratelimit-remaining-subscription-reads: 11999
```

Zápis omezení získáte pomocí operace zápisu: 

```powershell
New-AzResourceGroup -Name myresourcegroup -Location westus -Debug
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
msrest.http_logger :     'x-ms-ratelimit-remaining-subscription-reads': '11998'
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

## <a name="next-steps"></a>Další kroky

* Kompletní příklad Powershellu najdete v části [zkontrolujte omezení Resource Manageru a předplatné](https://github.com/Microsoft/csa-misc-utils/tree/master/psh-GetArmLimitsViaAPI).
* Další informace o omezení a kvóty najdete v tématu [předplatného Azure a limity, kvóty a omezení](../azure-subscription-service-limits.md).
* Další informace o zpracování asynchronního požadavky REST, naleznete v tématu [sledování asynchronních operací Azure](resource-manager-async-operations.md).
