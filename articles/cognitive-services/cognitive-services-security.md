---
title: Zabezpečení
titleSuffix: Azure Cognitive Services
description: Seznamte se s různými aspekty zabezpečení pro využití služeb Cognitive Services.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: dapine
ms.openlocfilehash: c86d806c408c2e8226e632a0b15e1e8729c987f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131534"
---
# <a name="azure-cognitive-services-security"></a>Zabezpečení azure kognitivních služeb

Bezpečnost by měla být považována za nejvyšší prioritu při vývoji všech aplikací. S nástupem aplikací s podporou umělé inteligence je zabezpečení ještě důležitější. V tomto článku jsou popsány různé aspekty zabezpečení Azure Cognitive Services, jako je například použití zabezpečení transportní vrstvy, ověřování a bezpečné konfigurace citlivých dat.

## <a name="transport-layer-security-tls"></a>Protokol TLS (Transport Layer Security)

Všechny koncové body služeb Cognitive Services vystavené přes PROTOKOL HTTP vynucují protokol TLS 1.2. S vynuceným protokolem zabezpečení by spotřebitelé, kteří se pokoušejí volat koncový bod služeb Cognitive Services, měli dodržovat tyto pokyny:

* Klientský operační systém (OS) musí podporovat TLS 1.2
* Jazyk (a platforma) použitý k volání HTTP musí jako součást požadavku zadat TLS 1.2
  * V závislosti na jazyce a platformě se určení TLS provádí implicitně nebo explicitně

Pro uživatele rozhraní .NET zvažte <a href="https://docs.microsoft.com/dotnet/framework/network-programming/tls" target="_blank">doporučené postupy zabezpečení transportní vrstvy <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.

## <a name="authentication"></a>Ověřování

Při diskusi o ověřování existuje několik běžných mylných představ. Ověřování a autorizace jsou často zaměňovány jeden za druhého. Identita je také hlavní součástí zabezpečení. Identita je kolekce informací o <a href="https://en.wikipedia.org/wiki/Principal_(computer_security)" target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"> </span>objektu zabezpečení </a>. Zprostředkovatelé identit (IdP) poskytují identity ověřovacích služeb. Ověřování je ověření identity uživatele. Autorizace je specifikace přístupových práv a oprávnění k prostředkům pro danou identitu. Několik nabídek služeb Cognitive Services zahrnuje řízení přístupu na základě rolí (RBAC). RBAC by mohl být použit ke zjednodušení některých obřadu spojené s ruční správu principy. Další podrobnosti najdete v [tématu řízení přístupu na základě rolí pro prostředky Azure](../role-based-access-control/overview.md).

Další informace o ověřování pomocí klíčů předplatného, přístupových tokenů a Služby Azure Active Directory (AAD) najdete v <a href="https://docs.microsoft.com/azure/cognitive-services/authentication" target="_blank">tématu ověřování požadavků na služby<span class="docon docon-navigate-external x-hidden-focus"></span>Azure Cognitive Services</a>.

## <a name="environment-variables-and-application-configuration"></a>Proměnné prostředí a konfigurace aplikace

Proměnné prostředí jsou dvojice název-hodnota, uložené v určitém prostředí. Bezpečnější alternativou k použití pevně zakódovaných hodnot pro citlivá data je použití proměnných prostředí. Pevně zakódované hodnoty jsou nezabezpečené a je třeba se jim vyhnout.

> [!CAUTION]
> **Nepoužívejte** pevně zakódované hodnoty pro citlivá data, jedná se o závažnou chybu zabezpečení.

> [!NOTE]
> Zatímco proměnné prostředí jsou uloženy ve formátu prostého textu, jsou izolovány do prostředí. Pokud je ohroženo prostředí, tak příliš jsou proměnné s prostředím.

### <a name="set-environment-variable"></a>Nastavit proměnnou prostředí

Chcete-li nastavit proměnné prostředí, použijte jeden `ENVIRONMENT_VARIABLE_KEY` z následujících `value` příkazů - kde je pojmenovaný klíč a je hodnota uložená v proměnné prostředí.

# <a name="command-line"></a>[Příkazový řádek](#tab/command-line)

Vytvořte a přiřaďte trvalou proměnnou prostředí za danou hodnotu.

```CMD
:: Assigns the env var to the value
setx ENVIRONMENT_VARIABLE_KEY="value"
```

V nové instanci **příkazového řádku**si přečtěte proměnnou prostředí.

```CMD
:: Prints the env var value
echo %ENVIRONMENT_VARIABLE_KEY%
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Vytvořte a přiřaďte trvalou proměnnou prostředí za danou hodnotu.

```powershell
# Assigns the env var to the value
[System.Environment]::SetEnvironmentVariable('ENVIRONMENT_VARIABLE_KEY', 'value', 'User')
```

V nové instanci **prostředí Windows PowerShell**si přečtěte proměnnou prostředí.

```powershell
# Prints the env var value
[System.Environment]::GetEnvironmentVariable('ENVIRONMENT_VARIABLE_KEY')
```

# <a name="bash"></a>[Bash](#tab/bash)

Vytvořte a přiřaďte trvalou proměnnou prostředí za danou hodnotu.

```Bash
# Assigns the env var to the value
echo export ENVIRONMENT_VARIABLE_KEY="value" >> /etc/environment && source /etc/environment
```

V nové instanci **Bash**, přečtěte si proměnnou prostředí.

```Bash
# Prints the env var value
echo "${ENVIRONMENT_VARIABLE_KEY}"

# Or use printenv:
# printenv ENVIRONMENT_VARIABLE_KEY
```

---

> [!TIP]
> Po nastavení proměnné prostředí restartujte integrované vývojové prostředí (IDE), abyste zajistili, že jsou k dispozici nově přidané proměnné prostředí.

### <a name="get-environment-variable"></a>Získat proměnnou prostředí

Chcete-li získat proměnnou prostředí, musí být přečtena do paměti. V závislosti na jazyku, který používáte, zvažte následující fragmenty kódu. Tyto fragmenty kódu ukazují, jak získat `ENVIRONMENT_VARIABLE_KEY` proměnnou prostředí dané `value`a přiřadit proměnné s názvem .

# <a name="c"></a>[C #](#tab/csharp)

Další informace naleznete <a href="https://docs.microsoft.com/dotnet/api/system.environment.getenvironmentvariable" target="_blank"> `Environment.GetEnvironmentVariable` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>v tématu .

```csharp
using static System.Environment;

class Program
{
    static void Main()
    {
        // Get the named env var, and assign it to the value variable
        var value =
            GetEnvironmentVariable(
                "ENVIRONMENT_VARIABLE_KEY");
    }
}
```

# <a name="c"></a>[C++](#tab/cpp)

Další informace naleznete <a href="https://docs.microsoft.com/cpp/c-runtime-library/reference/getenv-wgetenv" target="_blank"> `getenv` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>v tématu .

```cpp
#include <stdlib.h>

int main()
{
    // Get the named env var, and assign it to the value variable
    auto value =
        getenv("ENVIRONMENT_VARIABLE_KEY");
}
```

# <a name="java"></a>[Java](#tab/java)

Další informace naleznete <a href="https://docs.oracle.com/javase/7/docs/api/java/lang/System.html#getenv(java.lang.String)" target="_blank"> `System.getenv` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>v tématu .

```java
import java.lang.*;

public class Program {
   public static void main(String[] args) throws Exception {
    // Get the named env var, and assign it to the value variable
    String value =
        System.getenv(
            "ENVIRONMENT_VARIABLE_KEY")
   }
}
```

# <a name="nodejs"></a>[Node.js](#tab/node-js)

Další informace naleznete <a href="https://nodejs.org/api/process.html#process_process_env" target="_blank"> `process.env` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>v tématu .

```javascript
// Get the named env var, and assign it to the value variable
const value =
    process.env.ENVIRONMENT_VARIABLE_KEY;
```

# <a name="python"></a>[Python](#tab/python)

Další informace naleznete <a href="https://docs.python.org/2/library/os.html#os.environ" target="_blank"> `os.environ` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>v tématu .

```python
import os

# Get the named env var, and assign it to the value variable
value = os.environ['ENVIRONMENT_VARIABLE_KEY']
```

# <a name="objective-c"></a>[Objective-C](#tab/objective-c)

Další informace naleznete <a href="https://developer.apple.com/documentation/foundation/nsprocessinfo/1417911-environment?language=objc" target="_blank"> `environment` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>v tématu .

```objectivec
// Get the named env var, and assign it to the value variable
NSString* value =
    [[[NSProcessInfo processInfo]environment]objectForKey:@"ENVIRONMENT_VARIABLE_KEY"];
```

---

## <a name="next-steps"></a>Další kroky

* Prozkoumejte různé [služby Cognitive Services](welcome.md)
* Další informace o [virtuálních sítích kognitivních služeb](cognitive-services-virtual-networks.md)
