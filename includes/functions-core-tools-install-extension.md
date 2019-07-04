---
title: zahrnout soubor
description: zahrnout soubor
services: functions
author: ggailey777
ms.service: functions
ms.topic: include
ms.date: 05/25/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: d62da82b4a4dd35532dd8776a9111689db469201
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67448370"
---
## <a name="register-extensions"></a>Registrace rozšíření

S výjimkou aktivační události HTTP a časovač, funkce vazby modulu runtime verze 2.x jsou implementovány jako balíčky rozšíření. Ve verzi 2.x modulu runtime Azure Functions, je nutné provést explicitně registraci rozšíření pro vazbu typy používané ve vaší funkce. Výjimkou jsou HTTP vazbách a aktivačních událostech časovače, které nevyžadují rozšíření.

Můžete také nainstalovat rozšíření vazby jednotlivě, nebo můžete přidat odkaz na rozšíření sady do souboru projektu host.json. Rozšíření sady odebere riziko s problémy s kompatibilitou balíčků při použití více typů vazeb. Je doporučený postup pro registraci rozšíření vazby. Rozšíření sady také eliminuje nutnost instalace .NET Core 2.x SDK. 

### <a name="extension-bundles"></a>rozšíření sady

[!INCLUDE [Register extensions](functions-extension-bundles.md)]

Další informace najdete v tématu [rozšíření vazby registraci Azure Functions](../articles/azure-functions/functions-bindings-register.md#extension-bundles). Předtím, než přidáte do souboru souboru functions.json vazby, měli byste k host.json přidat rozšíření sady.

### <a name="register-individual-extensions"></a>Rozšíření pro jednotlivé registrace

Pokud je potřeba nainstalovat rozšíření, která nejsou v sadě, můžete ručně registrovat jednotlivá rozšíření balíčků pro konkrétní vazby. 

> [!NOTE]
> K ruční registraci rozšíření pomocí `func extensions install`, musí mít .NET Core 2.x nainstalované sady SDK.

Po aktualizaci vašich *function.json* soubor zahrnout všechny vazby, které vaše funkce vyžaduje, spusťte následující příkaz ve složce projektu.

```bash
func extensions install
```

Tento příkaz načte *function.json* souboru a zjistěte jaké balíčky, které potřebujete, nainstaluje je a znovu sestaví projekt rozšíření. Přidá všechny nové vazby na aktuální verzi, ale neaktualizuje existující vazby. Použití `--force` možnost aktualizovat existující vazby na nejnovější verzi při instalaci nové značky.