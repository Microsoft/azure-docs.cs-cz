---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 08/07/2020
ms.author: glenga
ms.openlocfilehash: a4f03223b5067d18f5d6e747b3bb630a86031b8f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96020336"
---
Pokud nemůžete použít rozšiřující balíčky, můžete použít Azure Functions Core Tools lokálně k instalaci specifických balíčků rozšíření vyžadovaných vaším projektem.

> [!IMPORTANT]
> Rozšíření nemůžete explicitně nainstalovat do aplikace Function App, která používá sady rozšíření. `extensionBundle`Před explicitní instalací rozšíření odeberte část v *host.js* .

Následující položky popisují některé důvody, proč možná budete muset nainstalovat rozšíření ručně:

* Je potřeba, abyste měli přístup ke konkrétní verzi rozšíření, která není v balíčku k dispozici.
* Musíte mít přístup k vlastnímu rozšíření, které není k dispozici v sadě prostředků.
* Je potřeba, abyste měli přístup k určité kombinaci rozšíření, která nejsou v jednom svazku k dispozici.

> [!NOTE]
> Chcete-li ručně nainstalovat rozšíření pomocí základních nástrojů, je nutné mít nainstalovanou [sadu .NET Core 2. x SDK](https://dotnet.microsoft.com/download) . .NET Core SDK používá Azure Functions Core Tools k instalaci rozšíření ze sady NuGet. Pro použití rozšíření Azure Functions nemusíte znát .NET.

Při explicitní instalaci rozšíření se do kořenového adresáře projektu přidá soubor projektu .NET s názvem Extensions. csproj. Tento soubor definuje sadu balíčků NuGet vyžadovaných vašimi funkcemi. I když můžete pracovat s [odkazy na balíček NuGet](/nuget/consume-packages/package-references-in-project-files) v tomto souboru, nástroj Core Tools vám umožní nainstalovat rozšíření bez nutnosti ručně upravovat soubor.

Existuje několik způsobů, jak pomocí základních nástrojů nainstalovat požadovaná rozšíření do svého místního projektu. 

#### <a name="install-all-extensions"></a>Nainstalovat všechna rozšíření 

Pomocí následujícího příkazu můžete automaticky přidat všechny balíčky rozšíření používané vazbami v místním projektu:

```dotnetcli
func extensions install
```
Příkaz přečte *function.jsv* souboru, kde zjistíte, které balíčky potřebujete, nainstaluje a znovu sestaví projekt rozšíření (Extensions. csproj). Přidává nové vazby v aktuální verzi, ale neaktualizuje stávající vazby. Použijte `--force` možnost aktualizace existujících vazeb na nejnovější verzi při instalaci nových.

Pokud vaše aplikace Function App používá vazby, které základní nástroje nerozpoznají, je nutné ručně nainstalovat konkrétní rozšíření.

#### <a name="install-a-specific-extension"></a>Instalace konkrétního rozšíření

Pomocí následujícího příkazu nainstalujte konkrétní balíček rozšíření v konkrétní verzi, v tomto případě rozšíření úložiště:

```dotnetcli
func extensions install --package Microsoft.Azure.WebJobs.Extensions.Storage --version 4.0.2
```
