---
title: 'Kurz: Ke správě příznaků funkcí použijte konfiguraci aplikací Azure'
titleSuffix: Azure App Configuration
description: V tomto kurzu se dozvíte, jak spravovat příznaky funkcí odděleně od vaší aplikace pomocí konfigurace aplikace Azure.
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
ms.custom: mvc
ms.openlocfilehash: ccab8014000f9f684249bf2c1f800f74c92e7369
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "76899365"
---
# <a name="tutorial-manage-feature-flags-in-azure-app-configuration"></a>Kurz: Správa příznaků funkcí v konfiguraci aplikací Azure

Všechny příznaky funkcí můžete uložit do konfigurace aplikací Azure a spravovat je z jednoho místa. Konfigurace aplikace má uživatelské uživatelské nastavení portálu s názvem **Správce funkcí,** který je navržen speciálně pro příznaky funkcí. Konfigurace aplikace také nativně podporuje schéma dat s příznakem funkce .NET Core.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Definujte a spravujte příznaky funkcí v konfiguraci aplikace.
> * Přístup k příznakům funkcí z vaší aplikace.

## <a name="create-feature-flags"></a>Vytvořit příznaky prvků

Správce funkcí na portálu Azure pro konfiguraci aplikací poskytuje nové nastavení pro vytváření a správu příznaků funkcí, které používáte ve svých aplikacích.

Přidání nového příznaku funkce:

1. Vyberte **Správce** > funkcí **+Přidat,** chcete-li přidat příznak prvku.

    ![Seznam příznaků funkcí](./media/azure-app-configuration-feature-flags.png)

1. Zadejte jedinečný název klíče příznaku prvku. Tento název potřebujete k odkazování na příznak ve vašem kódu.

1. Pokud chcete, zadejte popis příznaku prvku.

1. Nastavte počáteční stav příznaku prvku. Tento stav je obvykle *vypnuto* nebo *zapnuto*. *Stav Zapnuto* se změní na *Podmíněný,* pokud přidáte filtr do příznaku prvku.

    ![Vytvoření příznaku prvku](./media/azure-app-configuration-feature-flag-create.png)

1. Pokud je stav *Zapnuto*, vyberte **+Přidat filtr** a určete další podmínky pro kvalifikaci stavu. Zadejte předdefinovaný nebo vlastní klíč filtru a pak vyberte **+Přidat parametr** pro přidružení jednoho nebo více parametrů k filtru. Mezi vestavěné filtry patří:

    | Klíč | Parametry JSON |
    |---|---|
    | Microsoft.Procento | {"Hodnota": 0-100 procent} |
    | Microsoft.TimeWindow | {"Start": Čas UTC, "Konec": Čas UTC} |

    ![Filtr příznaku prvku](./media/azure-app-configuration-feature-flag-filter.png)

## <a name="update-feature-flag-states"></a>Aktualizovat stavy příznaku funkce

Změna hodnoty stavu příznaku prvku:

1. Vyberte **Správce funkcí**.

1. Vpravo od příznaku prvku, který chcete upravit, vyberte tři tečky (**...**) a pak vyberte **Upravit**.

1. Nastavte nový stav příznaku prvku.

## <a name="access-feature-flags"></a>Přístup k příznakům funkcí

Příznaky prvků vytvořené Správcem funkcí jsou uloženy a načteny jako běžné hodnoty klíče. Jsou uchovávány pod předponou `.appconfig.featureflag`zvláštního oboru názvů . Chcete-li zobrazit hodnoty podkladových klíčů, použijte Průzkumník konfigurace. Aplikace můžete načíst tyto hodnoty pomocí zprostředkovatelů konfigurace konfigurace aplikace, sady SDK, rozšíření příkazového řádku a REST API.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili spravovat příznaky funkcí a jejich stavy pomocí konfigurace aplikace. Další informace o podpoře správy funkcí v aplikaci Konfigurace a ASP.NET Core najdete v následujícím článku:

* [Použití příznaků funkcí v aplikaci ASP.NET Core](./use-feature-flags-dotnet-core.md)
