---
title: Kurz Správa příznaků funkcí přes konfiguraci aplikací Azure | Dokumentace Microsoftu
description: V tomto kurzu se dozvíte, jak spravovat příznaky funkcí samostatně z vaší aplikace s použitím konfigurace aplikace Azure.
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 04/19/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: b7fbf9add67a45c0db89fc11cee5c10bc537ab63
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393576"
---
# <a name="tutorial-manage-feature-flags-in-azure-app-configuration"></a>Kurz: Spravovat příznaky funkcí v konfiguraci aplikací Azure

Můžete ukládat všechny příznaky funkcí v konfiguraci aplikací Azure a spravovat z jednoho místa. Konfigurace aplikace má portál uživatelského rozhraní s názvem **funkce správce** , který je určený speciálně pro příznaky funkcí. Konfigurace aplikací také nativně podporuje datové schéma příznak funkce .NET Core.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Definování a správa příznaků funkcí v konfiguraci aplikací.
> * Příznaky funkcí přístup z vaší aplikace.

## <a name="create-feature-flags"></a>Vytvoření příznaky funkcí

Správce funkce na webu Azure Portal pro konfiguraci aplikací poskytuje uživatelské rozhraní pro vytváření a správa příznaků funkcí, které můžete použít ve svých aplikacích.

Chcete-li přidat nový příznak funkce:

1. Vyberte **funkce správce** >  **+ přidat** přidat příznak funkce.

    ![Příznak seznam funkcí](./media/azure-app-configuration-feature-flags.png)

1. Zadejte jedinečný název klíče pro příznak funkce. Musíte tento název k odkazování na příznak ve vašem kódu.

1. Pokud chcete, zadejte popis příznak funkce.

1. Nastavte počáteční stav příznak funkce. Tento stav se obvykle *vypnout* nebo *na*. *Na* stav se změní na *podmíněného* Pokud přidáte filtr do příznak funkce.

    ![Vytvoření příznak funkce](./media/azure-app-configuration-feature-flag-create.png)

1. Pokud je stav *na*vyberte **+ přidat filtr** k určení jakékoliv další podmínky k určení stavu. Zadejte klíč integrovaného nebo vlastního filtru a pak vyberte **+ přidat parametr** přidružit jeden nebo více parametrů filtru. Integrované filtry zahrnují:

    | Klíč | Parametry JSON |
    |---|---|
    | Microsoft.Percentage | {"Value": 0 – 100 %} |
    | Microsoft.TimeWindow | {"Start": Čas UTC, "Konec": Čas UTC} |

    ![Filtr příznak funkce](./media/azure-app-configuration-feature-flag-filter.png)

## <a name="update-feature-flag-states"></a>Aktualizace stavů příznak funkce

Chcete-li změnit hodnotu stavu příznak funkce:

1. Vyberte **funkce správce**.

1. Napravo od příznak funkce, kterou chcete upravit, vyberte tři tečky ( **...** ) a pak vyberte **upravit**.

1. Nastavení nového stavu pro příznak funkce.

## <a name="access-feature-flags"></a>Příznaky funkcí přístup

Příznaky funkcí, které jsou vytvořené správcem funkce jsou uložené a načítají jako regulární hodnoty klíče. Budete uchovávat v předponu oboru názvů zvláštní `.appconfig.featureflag`. Chcete-li zobrazit podkladovým hodnotám klíče, pomocí Průzkumníka konfigurace. Aplikace může načíst tyto hodnoty pomocí zprostředkovatele konfigurace pro konfiguraci aplikací, sady SDK, rozšíření příkazového řádku a rozhraní REST API.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste zjistili, jak spravovat příznaky funkcí a jejich stavů pomocí konfigurace aplikace. Další informace o podpoře funkce správy v konfiguraci aplikací a ASP.NET Core najdete v následujícím článku:

* [Použití příznaků funkcí v aplikaci ASP.NET Core](./use-feature-flags-dotnet-core.md)
