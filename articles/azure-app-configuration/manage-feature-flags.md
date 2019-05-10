---
title: Kurz Správa příznaků funkcí přes konfiguraci aplikací Azure | Dokumentace Microsoftu
description: V tomto kurzu se dozvíte, jak spravovat příznaky funkcí samostatně z vaší aplikace pomocí konfigurace aplikace pro Azure
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
ms.openlocfilehash: d995a2e9f0d05dc3b0853036e8fb3c04ccdfab96
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2019
ms.locfileid: "65412340"
---
# <a name="tutorial-manage-feature-flags-in-azure-app-configuration"></a>Kurz: Spravovat příznaky funkcí v konfiguraci aplikací Azure

Můžete ukládat všechny příznaky funkcí v konfiguraci aplikací Azure a spravovat z jednoho místa. Má portál uživatelského rozhraní, volá **funkce správce**, který je určený speciálně pro příznaky funkcí. Kromě toho konfigurace aplikací podporuje nativně schéma dat příznak funkce .NET Core.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Definování a správa příznaků funkcí v konfiguraci aplikací.
> * Příznaky funkcí přístup z vaší aplikace.

## <a name="create-feature-flags"></a>Vytvoření příznaky funkcí

**Funkce správce** na webu Azure Portal pro konfiguraci aplikací poskytuje uživatelské rozhraní pro vytváření a Správa funkce příznaky, které používáte ve vaší aplikaci. Postupujte podle následujících kroků přidáte nový příznak funkce.

1. Vyberte **funkce správce** > **+ vytvořit** přidat příznak funkce.

    ![Příznak seznam funkcí](./media/azure-app-configuration-feature-flags.png)

2. Zadejte jedinečný název klíče pro příznak funkce. Musíte tento název k odkazování na příznak ve vašem kódu.

3. Příznak funkce volitelně zadejte popis lidských skvěle.

4. Nastavte počáteční stav příznak funkce. Je obvykle stačí *na* nebo *vypnout*.

    ![Vytvoření příznak funkce](./media/azure-app-configuration-feature-flag-create.png)

5. Pokud je stav *na*, volitelně zadejte jakékoli další podmínku, která má kvalifikaci s **přidat filtr**. Zadejte klíč integrovaného nebo vlastního filtru a přidružit parametrů. Integrované filtry zahrnují:

    | Klíč | Parametry JSON |
    |---|---|
    | Microsoft.Percentage | {"Value": 0 – 100 %} |
    | Microsoft.TimeWindow | {"Start": Čas UTC, "Konec": Čas UTC} |

    ![Filtr příznak funkce](./media/azure-app-configuration-feature-flag-filter.png)

## <a name="update-feature-flag-states"></a>Aktualizace stavů příznak funkce

Postupujte podle následujících kroků ke změně hodnoty stavu příznak funkce.

1. Vyberte **funkce správce**.

2. Klikněte na **...**   >  **Upravit** napravo od příznak funkce, kterou chcete upravit.

3. Nastavení nového stavu pro příznak funkce.

## <a name="access-feature-flags"></a>Příznaky funkcí přístup

Vytvořené příznaků funkcí **funkce správce** jsou uložené a načítají jako regulární hodnoty klíče. Které zůstanou v rámci předponu oboru názvů zvláštní *. appconfig.featureflag*. Můžete zobrazit základní klíč hodnota pomocí **Průzkumník konfigurací**. Aplikace může načíst pomocí zprostředkovatele konfigurace pro konfiguraci aplikací, sady SDK, rozšíření příkazového řádku a rozhraní REST API.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste zjistili, jak spravovat příznaky funkcí a jejich stavů pomocí konfigurace aplikace. Viz následující prostředky pro další informace o podporovaných funkcích správy v konfiguraci aplikací a ASP.NET Core.

* [Použití příznaků funkcí v aplikaci ASP.NET Core](./use-feature-flags-dotnet-core.md)
