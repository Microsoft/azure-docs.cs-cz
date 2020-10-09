---
title: 'Kurz: použití konfigurace aplikací Azure ke správě příznaků funkcí'
titleSuffix: Azure App Configuration
description: V tomto kurzu se naučíte spravovat příznaky funkcí odděleně od vaší aplikace pomocí Azure App Configuration.
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 04/19/2019
ms.author: lcozzens
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: dd816ebcf2a40e6a0b7febcc3fe5c1006dac20bb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88209949"
---
# <a name="tutorial-manage-feature-flags-in-azure-app-configuration"></a>Kurz: Správa příznaků funkcí v konfiguraci aplikací Azure

V konfiguraci aplikací Azure můžete ukládat všechny příznaky funkcí a spravovat je na jednom místě. Konfigurace aplikace má uživatelské rozhraní portálu nazvané **správce funkcí** navržené speciálně pro příznaky funkcí. Konfigurace aplikace také nativně podporuje schéma dat příznaků funkcí .NET Core.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Definování a Správa příznaků funkcí v konfiguraci aplikace.
> * Přístup k příznakům funkcí z vaší aplikace

## <a name="create-feature-flags"></a>Vytváření příznaků funkcí

Správce funkcí v konfiguraci aplikace Azure Portal for App poskytuje uživatelské rozhraní pro vytváření a správu příznaků funkcí, které používáte ve svých aplikacích.

Přidání nového příznaku funkce:

1. Vyberte **správce funkcí**  >  **+ Přidat** a přidejte příznak funkce.

    ![Seznam příznaků funkcí](./media/azure-app-configuration-feature-flags.png)

1. Zadejte jedinečný název klíče pro příznak funkce. Tento název budete potřebovat pro odkazování na příznak ve vašem kódu.

1. Pokud chcete, zadejte příznak funkce Popis.

1. Nastavte počáteční stav příznaku funkce. Tento stav je obvykle *vypnutý* nebo *zapnutý*. Pokud přidáte filtr do příznaku funkce, změní se stav *zapnuto* na *podmíněný* .

    ![Vytváření příznaků funkcí](./media/azure-app-configuration-feature-flag-create.png)

1. Když je stav *zapnutý*, vyberte **+ Přidat filtr** a určete další podmínky, které se mají uvést do stavu. Zadejte vestavěný nebo vlastní klíč filtru a pak vyberte **+ Přidat parametr** pro přidružení jednoho nebo více parametrů k filtru. Mezi předdefinované filtry patří:

    | Klíč | Parametry JSON |
    |---|---|
    | Microsoft. PERCENTAGE | {"Value": 0-100%} |
    | Microsoft. TimeWindow | {"Start": čas UTC, "konec": světový čas UTC} |

    ![Filtr příznaků funkcí](./media/azure-app-configuration-feature-flag-filter.png)

## <a name="update-feature-flag-states"></a>Aktualizace stavů příznaků funkcí

Změna hodnoty stavu příznaku funkce:

1. Vyberte **správce funkcí**.

1. Napravo od příznaku funkce, který chcete upravit, vyberte tři tečky (**...**) a pak vyberte **Upravit**.

1. Nastavte nový stav příznaku funkce.

## <a name="access-feature-flags"></a>Přístup k příznakům funkcí

Příznaky funkcí vytvořené správcem funkcí se ukládají a načítají jako běžné hodnoty klíčů. Jsou uchovávány v rámci speciální předpony oboru názvů `.appconfig.featureflag` . Chcete-li zobrazit základní hodnoty klíčů, použijte Průzkumníka konfigurace. Vaše aplikace může tyto hodnoty načíst pomocí zprostředkovatelů konfigurace aplikace, sad SDK, rozšíření příkazového řádku a rozhraní REST API.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak pomocí konfigurace aplikace spravovat příznaky funkcí a jejich stavy. Další informace o podpoře správy funkcí v konfiguraci aplikace a ASP.NET Core najdete v následujícím článku:

* [Používání příznaků funkcí v aplikaci ASP.NET Core](./use-feature-flags-dotnet-core.md)
