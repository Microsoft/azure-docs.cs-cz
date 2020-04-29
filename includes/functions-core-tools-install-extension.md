---
title: zahrnout soubor
description: zahrnout soubor
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/25/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 94cac0932da5880e5e7b8a8fac3870b5bc464af9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "75564671"
---
## <a name="register-extensions"></a>Registrovat rozšíření

S výjimkou aktivačních událostí protokolu HTTP a časovače jsou vazby funkcí v modulu runtime verze 2. x a vyšší implementovány jako balíčky rozšíření. Ve verzi 2. x a mimo Azure Functions runtime musíte explicitně zaregistrovat rozšíření pro typy vazeb používané ve vašich funkcích. Výjimkou jsou vazby HTTP a triggery časovače, které nevyžadují rozšíření.

Můžete zvolit instalaci rozšíření vazby jednotlivě nebo můžete přidat odkaz na balíček rozšíření do souboru projektu Host. JSON. Sady rozšíření odstraňují možnost mít problémy s kompatibilitou balíčků při použití více typů vazeb. Je doporučený postup pro registraci rozšíření vazby. Sady rozšíření také odstraňují požadavek na instalaci sady .NET Core 2. x SDK. 

### <a name="extension-bundles"></a>Sady rozšíření

[!INCLUDE [Register extensions](functions-extension-bundles.md)]

Další informace najdete v tématu [Registrace rozšíření vazby Azure Functions](../articles/azure-functions/functions-bindings-register.md#extension-bundles). Před přidáním vazeb do souboru Function. JSON byste měli do souboru Host. JSON přidat rozšiřující balíčky.

### <a name="register-individual-extensions"></a>Registrovat jednotlivá rozšíření

Pokud potřebujete nainstalovat rozšíření, která nejsou součástí sady, můžete ručně zaregistrovat jednotlivé balíčky rozšíření pro konkrétní vazby. 

> [!NOTE]
> Chcete-li ručně registrovat rozšíření `func extensions install`pomocí nástroje, je nutné mít nainstalovanou sadu .NET Core 2. x SDK.

Po aktualizaci souboru *Function. JSON* pro zahrnutí všech vazeb, které vaše funkce potřebuje, spusťte následující příkaz ve složce projektu.

```bash
func extensions install
```

Příkaz přečte soubor *Function. JSON* , který zjistí, které balíčky potřebujete, nainstaluje a znovu sestaví projekt rozšíření. Přidává nové vazby v aktuální verzi, ale neaktualizuje stávající vazby. Použijte `--force` možnost aktualizace existujících vazeb na nejnovější verzi při instalaci nových.
