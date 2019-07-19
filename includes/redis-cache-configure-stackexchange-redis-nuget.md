---
author: yegu-ms
ms.service: redis-cache
ms.topic: include
ms.date: 11/09/2018
ms.author: yegu
ms.openlocfilehash: 61e93e3700b9a396d2ac4fdcbb51fc5c874cf9cb
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/16/2019
ms.locfileid: "68286192"
---
Aplikace .NET mohou použít klienta mezipaměti **StackExchange.Redis**, kterého lze nakonfigurovat v aplikaci Visual Studio pomocí balíčku NuGet, který zjednodušuje konfiguraci aplikací klientů mezipaměti. 

> [!NOTE]
> Další informace najdete na stránce GitHubu [stackexchange. Redis](https://github.com/StackExchange/StackExchange.Redis) a v [mezipaměti stackexchange. Azure pro Redis klienta dokumentace](https://github.com/StackExchange/StackExchange.Redis#documentation).
>
>

Chcete-li konfigurovat klientskou aplikaci v sadě Visual Studio pomocí balíčku StackExchange.Redis NuGet, klikněte pravým tlačítkem na projekt v **Průzkumníku řešení** a vyberte**Správa balíčků NuGet**. 

![Správa balíčků NuGet](media/redis-cache-configure-stackexchange-redis-nuget/redis-cache-manage-nuget-menu.png)

Do textového pole pro vyhledávání zadejte **StackExchange.Redis** nebo **StackExchange.Redis.StrongName**, vyberte z výsledků požadovanou verzi, a klikněte na **Instalovat**.

> [!NOTE]
> Pokud upřednostňujete použití verze klientské knihovny **StackExchange.Redis** se silným názvem, vyberte **StackExchange.Redis.StrongName**, jinak vyberte **StackExchange.Redis**.
>
>

![Balíček StackExchange.Redis NuGet](media/redis-cache-configure-stackexchange-redis-nuget/redis-cache-stackexchange-redis.png)

Balíček NuGet se stáhne a přidá odkazy na požadovaná sestavení pro vaši klientskou aplikaci pro přístup k mezipaměti Azure pro Redis s mezipamětí StackExchange.Azure pro klienta Redis.

> [!NOTE]
> Pokud jste dříve nakonfigurovali projekt pro použití StackExchange.Redis, můžete zkontrolovat aktualizace do balíčku ze **Správce balíčků NuGet**. Chcete-li kontrolovat a instalovat aktualizované verze balíčku StackExchange.Redis NuGet, klikněte na tlačítko **aktualizace** v **Správce balíčků NuGet** okna. Pokud je k dispozici aktualizace pro balíček StackExchange.Redis NuGet, můžete aktualizovat projekt pro použití aktualizované verze.
>
>

Balíček StackExchange.Redis NuGet můžete nainstalovat také kliknutím na **Správce balíčků NuGet**, **Konzola Správce balíčků** v nabídce **Nástroje** a spuštěním následujícího příkazu z okna **Konzola Správce balíčků**.

```
Install-Package StackExchange.Redis
```
