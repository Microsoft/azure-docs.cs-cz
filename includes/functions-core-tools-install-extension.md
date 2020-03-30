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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "75564671"
---
## <a name="register-extensions"></a>Rozšíření registru

S výjimkou aktivačních událostí HTTP a časovače funkce vazby v runtime verze 2.x a vyšší jsou implementovány jako balíčky rozšíření. Ve verzi 2.x a mimo prostředí Azure Functions runtime, budete muset explicitně zaregistrovat rozšíření pro typy vazeb používané ve vašich funkcích. Výjimky z tohoto jsou http vazby a časovač aktivační události, které nevyžadují rozšíření.

Můžete nainstalovat rozšíření vazby jednotlivě, nebo můžete přidat odkaz na rozšíření sady rozšíření do souboru projektu host.json. Rozšíření svazky odstraňuje pravděpodobnost, že problémy s kompatibilitou balíčku při použití více typů vazby. Je to doporučený přístup pro registraci rozšíření vazby. Rozšíření svazky také odebere požadavek na instalaci .NET Core 2.x SDK. 

### <a name="extension-bundles"></a>Rozšiřující balíčky

[!INCLUDE [Register extensions](functions-extension-bundles.md)]

Další informace najdete [v tématu Register Azure Functions vazby rozšíření](../articles/azure-functions/functions-bindings-register.md#extension-bundles). Před přidáním vazeb do souboru function.json byste měli do souboru host.json přidat balíčky rozšíření.

### <a name="register-individual-extensions"></a>Registrace jednotlivých rozšíření

Pokud potřebujete nainstalovat rozšíření, která nejsou v sadě, můžete ručně zaregistrovat jednotlivé balíčky rozšíření pro konkrétní vazby. 

> [!NOTE]
> Chcete-li ručně zaregistrovat `func extensions install`rozšíření pomocí aplikace , musíte mít nainstalovanou sadu .NET Core 2.x SDK.

Po aktualizaci souboru *function.json* tak, aby zahrnoval všechny vazby, které vaše funkce potřebuje, spusťte následující příkaz ve složce projektu.

```bash
func extensions install
```

Příkaz přečte soubor *function.json,* aby zjistil, které balíčky potřebujete, nainstaluje je a znovu vytvoří projekt rozšíření. Přidá všechny nové vazby v aktuální verzi, ale neaktualizuje existující vazby. Pomocí `--force` možnosti aktualizujte existující vazby na nejnovější verzi při instalaci nových.
