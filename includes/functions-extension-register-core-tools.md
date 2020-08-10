---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 08/07/2020
ms.author: glenga
ms.openlocfilehash: 2936d22eacef73daef4433b3fd296dd4757fa410
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/10/2020
ms.locfileid: "88031115"
---
Pokud nemůžete použít rozšiřující balíčky, můžete použít Azure Functions Core Tools lokálně k instalaci specifických balíčků rozšíření vyžadovaných vaším projektem. 

> [!NOTE]
> Chcete-li ručně nainstalovat rozšíření pomocí základních nástrojů, je nutné mít nainstalovanou sadu .NET Core 2. x SDK.

Při explicitní instalaci rozšíření se do kořenového adresáře projektu přidá soubor projektu .NET s názvem Extensions. csproj. Tento soubor definuje sadu balíčků NuGet vyžadovaných vašimi funkcemi. I když můžete pracovat s [odkazy na balíček NuGet](/nuget/consume-packages/package-references-in-project-files) v tomto souboru, nástroj Core Tools vám umožní nainstalovat rozšíření bez nutnosti ručně upravovat soubor.

Existuje několik způsobů, jak pomocí základních nástrojů nainstalovat požadovaná rozšíření do svého místního projektu. 

#### <a name="install-all-extensions"></a>Nainstalovat všechna rozšíření 

Pomocí následujícího příkazu můžete automaticky přidat všechny balíčky rozšíření používané vazbami v místním projektu:

```dotnetcli
func extensions install
```
Příkaz přečte *function.jsv* souboru, kde zjistíte, které balíčky potřebujete, nainstaluje a znovu sestaví projekt rozšíření (Extensions. csproj). Přidává nové vazby v aktuální verzi, ale neaktualizuje stávající vazby. Použijte `--force` možnost aktualizace existujících vazeb na nejnovější verzi při instalaci nových.

#### <a name="install-a-specific-extension"></a>Instalace konkrétního rozšíření

Pomocí následujícího příkazu nainstalujte konkrétní balíček rozšíření v konkrétní verzi, v tomto případě rozšíření úložiště:

```dotnetcli
func extensions install --package Microsoft.Azure.WebJobs.Extensions.Storage --version 4.0.2
```