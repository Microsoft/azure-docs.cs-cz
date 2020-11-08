---
title: Zabezpečení Azure Cognitive Services
titleSuffix: Azure Cognitive Services
description: Přečtěte si o různých faktorech zabezpečení pro Cognitive Services využití.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: erhopf
ms.custom: devx-track-python, devx-track-js, devx-track-csharp
ms.openlocfilehash: f14ac72443dedc8e33e607a82b2145c7ebf95ad2
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2020
ms.locfileid: "94368776"
---
# <a name="azure-cognitive-services-security"></a>Zabezpečení Azure Cognitive Services

Zabezpečení by se mělo při vývoji všech a všech aplikací brát v úvahu jako nejvyšší priorita. V případě aplikací s povoleným umělou logikou je zabezpečení ještě důležitější. V tomto článku jsou uvedené různé aspekty zabezpečení Azure Cognitive Services, jako je například použití zabezpečení transportní vrstvy, ověřování, bezpečná konfigurace citlivých dat a Customer Lockbox pro přístup k zákaznickým datům.

## <a name="transport-layer-security-tls"></a>Protokol TLS (Transport Layer Security)

Všechny koncové body Cognitive Services vystavené přes protokol HTTP vynutila TLS 1,2. S vynutilým protokolem zabezpečení se zákazníci, kteří se pokoušejí zavolat na Cognitive Services koncový bod, musí dodržovat tyto pokyny:

* Operační systém klienta (OS) musí podporovat protokol TLS 1,2.
* Jazyk (a platforma), který se používá k provedení volání HTTP, musí jako součást žádosti zadat TLS 1,2.
  * V závislosti na jazyku a platformě se protokol TLS provádí implicitně nebo explicitně.

Pro uživatele rozhraní .NET zvažte <a href="https://docs.microsoft.com/dotnet/framework/network-programming/tls" target="_blank">osvědčené postupy <span class="docon docon-navigate-external x-hidden-focus"></span> zabezpečení transportní vrstvy </a>.

## <a name="authentication"></a>Authentication

Při projednávání ověřování je k dispozici několik běžných konceptů. Ověřování a autorizace jsou často mezi sebou zaměňovány. Identita je také hlavní součástí zabezpečení. Identita je kolekce informací o <a href="https://en.wikipedia.org/wiki/Principal_(computer_security)" target="_blank">objektu zabezpečení <span class="docon docon-navigate-external x-hidden-focus"></span> </a>. Zprostředkovatelé identity (IdP) poskytují identity službám ověřování. Ověřování je aktem, který ověřuje identitu uživatele. Autorizace je specifikace přístupových práv a oprávnění k prostředkům pro danou identitu. Mezi některé z Cognitive Services nabídek patří řízení přístupu na základě role Azure (Azure RBAC). Azure RBAC se dá využít ke zjednodušení některých proceduryů, které se podílejí na ruční správě objektů zabezpečení. Další podrobnosti najdete v tématu [řízení přístupu na základě role v Azure pro prostředky Azure](../role-based-access-control/overview.md).

Další informace o ověřování pomocí klíčů předplatného, přístupových tokenů a Azure Active Directory (AAD) najdete v tématu <a href="https://docs.microsoft.com/azure/cognitive-services/authentication" target="_blank">ověření <span class="docon docon-navigate-external x-hidden-focus"></span> požadavků do Azure Cognitive Services</a>.

## <a name="environment-variables-and-application-configuration"></a>Proměnné prostředí a konfigurace aplikace

Proměnné prostředí jsou páry název-hodnota uložené v konkrétním prostředí. Bezpečnější alternativou k použití hodnot pevně zakódované pro citlivá data je použití proměnných prostředí. Hodnoty pevně zakódované jsou nezabezpečené a je třeba se jim vyhnout.

> [!CAUTION]
> Nepoužívejte hodnoty pevně zakódované pro citlivá data **. Jedná se** tedy o hlavní chybu zabezpečení.

> [!NOTE]
> I když jsou proměnné prostředí uložené v prostém textu, jsou izolované do prostředí. Pokud dojde k ohrožení zabezpečení prostředí, je to proto, že se jedná o proměnné s prostředím.

### <a name="set-environment-variable"></a>Nastavit proměnnou prostředí

Chcete-li nastavit proměnné prostředí, použijte jeden z následujících příkazů – kde `ENVIRONMENT_VARIABLE_KEY` je pojmenovaný klíč a `value` je hodnotou uloženou v proměnné prostředí.

# <a name="command-line"></a>[Příkazový řádek](#tab/command-line)

Vytvoří a přiřadí trvalou proměnnou prostředí s ohledem na hodnotu.

```CMD
:: Assigns the env var to the value
setx ENVIRONMENT_VARIABLE_KEY="value"
```

V nové instanci **příkazového řádku** si přečtěte proměnnou prostředí.

```CMD
:: Prints the env var value
echo %ENVIRONMENT_VARIABLE_KEY%
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Vytvoří a přiřadí trvalou proměnnou prostředí s ohledem na hodnotu.

```powershell
# Assigns the env var to the value
[System.Environment]::SetEnvironmentVariable('ENVIRONMENT_VARIABLE_KEY', 'value', 'User')
```

V nové instanci **Windows PowerShellu** si přečtěte proměnnou prostředí.

```powershell
# Prints the env var value
[System.Environment]::GetEnvironmentVariable('ENVIRONMENT_VARIABLE_KEY')
```

# <a name="bash"></a>[Bash](#tab/bash)

Vytvoří a přiřadí trvalou proměnnou prostředí s ohledem na hodnotu.

```Bash
# Assigns the env var to the value
echo export ENVIRONMENT_VARIABLE_KEY="value" >> /etc/environment && source /etc/environment
```

V nové instanci rozhraní **bash** si přečtěte proměnnou prostředí.

```Bash
# Prints the env var value
echo "${ENVIRONMENT_VARIABLE_KEY}"

# Or use printenv:
# printenv ENVIRONMENT_VARIABLE_KEY
```

---

> [!TIP]
> Po nastavení proměnné prostředí restartujte integrované vývojové prostředí (IDE), aby bylo zajištěno, že nově přidané proměnné prostředí budou k dispozici.

### <a name="get-environment-variable"></a>Získat proměnnou prostředí

Chcete-li získat proměnnou prostředí, musí být načtena do paměti. V závislosti na jazyku, který používáte, zvažte následující fragmenty kódu. Tyto fragmenty kódu ukazují, jak získat proměnnou prostředí dané `ENVIRONMENT_VARIABLE_KEY` a přiřadit k proměnné s názvem `value` .

# <a name="c"></a>[C#](#tab/csharp)

Další informace najdete na webu <a href="https://docs.microsoft.com/dotnet/api/system.environment.getenvironmentvariable" target="_blank">`Environment.GetEnvironmentVariable` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

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

Další informace najdete na webu <a href="https://docs.microsoft.com/cpp/c-runtime-library/reference/getenv-wgetenv" target="_blank">`getenv` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

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

Další informace najdete na webu <a href="https://docs.oracle.com/javase/7/docs/api/java/lang/System.html#getenv(java.lang.String)" target="_blank">`System.getenv` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

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

Další informace najdete na webu <a href="https://nodejs.org/api/process.html#process_process_env" target="_blank">`process.env` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

```javascript
// Get the named env var, and assign it to the value variable
const value =
    process.env.ENVIRONMENT_VARIABLE_KEY;
```

# <a name="python"></a>[Python](#tab/python)

Další informace najdete na webu <a href="https://docs.python.org/2/library/os.html#os.environ" target="_blank">`os.environ` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

```python
import os

# Get the named env var, and assign it to the value variable
value = os.environ['ENVIRONMENT_VARIABLE_KEY']
```

# <a name="objective-c"></a>[Objective-C](#tab/objective-c)

Další informace najdete na webu <a href="https://developer.apple.com/documentation/foundation/nsprocessinfo/1417911-environment?language=objc" target="_blank">`environment` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

```objectivec
// Get the named env var, and assign it to the value variable
NSString* value =
    [[[NSProcessInfo processInfo]environment]objectForKey:@"ENVIRONMENT_VARIABLE_KEY"];
```

---

## <a name="customer-lockbox"></a>Customer Lockbox

[Customer Lockbox pro Microsoft Azure](../security/fundamentals/customer-lockbox-overview.md) poskytuje rozhraní pro zákazníky, kteří budou kontrolovat a schvalovat nebo odmítat žádosti o přístup k datům zákazníků. Používá se v případech, kdy technici Microsoftu potřebují získat přístup k datům zákazníků během zpracování žádostí o podporu. Informace o tom, jak se iniciují žádosti o Customer Lockbox, sledování a uložení pro pozdější kontroly a audity, najdete v článku [Customer Lockbox](../security/fundamentals/customer-lockbox-overview.md). 

Customer Lockbox je k dispozici pro tuto službu pro rozpoznávání:

* Překladač

Pro následující služby nebudou Microsoft technici přistupovat k žádným zákaznickým datům ve vrstvě E0: 

* Language Understanding
* Tvář
* Content Moderator
* Personalizace

> [!IMPORTANT]
> V případě nástroje pro **rozpoznávání formulářů** nebudou Microsoft technici přistupovat k žádným zákaznickým datům v prostředcích vytvořených po 10. červenci 2020.

Chcete-li požádat o možnost použití skladové položky E0, vyplňte a odešlete [formulář žádosti](https://aka.ms/cogsvc-cmk). Bude to trvat přibližně 3-5 pracovních dnů, než se vrátí na stav vaší žádosti. V závislosti na poptávce můžete být do fronty zařazené a schválené, protože místo bude k dispozici. Po schválení pro použití skladové položky E0 s LUIS budete muset vytvořit nový prostředek z Azure Portal a vybrat E0 jako cenovou úroveň. Uživatelé nebudou moct upgradovat z F0 na novou SKLADOVOU položku E0.

Služba Speech v současné době nepodporuje Customer Lockbox. Zákaznická data ale můžou být uložená pomocí možnosti Přineste si vlastní úložiště (BYOS), což vám umožní dosáhnout podobných ovládacích prvků dat Customer Lockbox. Pamatujte, že data služby Speech Service zůstanou a jsou zpracována v oblasti, ve které byl prostředek řeči vytvořen. To platí pro veškerá data v klidovém režimu a data při přenosu. Pokud používáte funkce vlastního nastavení, jako je Custom Speech a vlastní hlas, všechna zákaznická data se přenesou, ukládají a zpracovávají ve stejné oblasti, ve které se nachází BYOS (Pokud se používá) a prostředek služby Speech.

> [!IMPORTANT]
> Společnost **Microsoft nepoužívá** zákaznická data ke zlepšení svých modelů řeči. Pokud je navíc protokolování koncových bodů zakázané a nepoužívá se žádné vlastní nastavení, neukládají se žádná zákaznická data. 

## <a name="next-steps"></a>Další kroky

* Prozkoumejte různé [Cognitive Services](./what-are-cognitive-services.md)
* Další informace o [Cognitive Services virtuálních sítích](cognitive-services-virtual-networks.md)