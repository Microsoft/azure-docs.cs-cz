---
title: Použití filtrů funkcí k povolení funkce pro podmnožinu uživatelů
titleSuffix: Azure App Configuration
description: Naučte se používat filtry funkcí k povolení funkce pro podmnožinu uživatelů.
ms.service: azure-app-configuration
ms.custom: devx-track-csharp
author: lisaguthrie
ms.author: lcozzens
ms.topic: conceptual
ms.date: 3/9/2020
ms.openlocfilehash: 5b2eb942581f6e4163012b0f767d04c02689bb7b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88206760"
---
# <a name="use-feature-filters-to-enable-a-feature-for-a-subset-of-users"></a>Použití filtrů funkcí k povolení funkce pro podmnožinu uživatelů

Příznaky funkcí umožňují aktivovat nebo deaktivovat funkce v aplikaci. Jednoduchý příznak funkce je buď zapnutý, nebo vypnutý. Aplikace se vždy chová stejným způsobem. Můžete například zavést novou funkci za příznakem funkce. Když je příznak funkce povolený, všichni uživatelé uvidí novou funkci. Zakázáním příznaku funkce se nová funkce skryje.

Naproti tomu _příznak podmíněné funkce_ umožňuje, aby byl příznak funkce povolen nebo zakázán dynamicky. Aplikace se může chovat odlišně v závislosti na kritériích příznaků funkcí. Předpokládejme, že chcete novou funkci Zobrazit pro malou podmnožinu uživatelů v prvním. Příznak podmíněné funkce vám umožní povolit příznak funkce pro některé uživatele a zároveň ho zakázat ostatním uživatelům. _Filtry funkcí_ určují stav příznaku funkce pokaždé, když se vyhodnotí.

`Microsoft.FeatureManagement`Knihovna obsahuje dva filtry funkcí:

- `PercentageFilter` Povolí příznak funkce na základě procenta.
- `TimeWindowFilter` Povolí příznak funkce během zadaného časového období.

Můžete také vytvořit vlastní filtr funkcí, který implementuje [rozhraní Microsoft. FeatureManagement. IFeatureFilter](/dotnet/api/microsoft.featuremanagement.ifeaturefilter).

## <a name="registering-a-feature-filter"></a>Registrace filtru funkcí

Filtr funkcí zaregistrujete voláním `AddFeatureFilter` metody a zadáním názvu filtru funkce. Například následující Registry kódu `PercentageFilter` :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllersWithViews();
    services.AddFeatureManagement().AddFeatureFilter<PercentageFilter>();
}
```

## <a name="configuring-a-feature-filter-in-azure-app-configuration"></a>Konfigurace filtru funkcí v konfiguraci aplikací Azure

Některé filtry funkcí mají další nastavení. Například `PercentageFilter` aktivuje funkci na základě procenta. Má nastavení definující procentuální hodnotu, která se má použít.

Tato nastavení můžete nakonfigurovat pro příznaky funkcí definované v konfiguraci aplikace Azure. Pomocí následujících kroků můžete například `PercentageFilter` Povolit příznak funkce pro 50% žádostí na webovou aplikaci:

1. Postupujte podle pokynů v [rychlém startu: Přidání příznaků funkcí do aplikace ASP.NET Core](./quickstart-feature-flag-aspnet-core.md) pro vytvoření webové aplikace s příznakem funkce.

1. V Azure Portal přejděte do úložiště konfigurace a klikněte na **správce funkcí**.

1. Klikněte na kontextovou nabídku pro příznak funkce *beta* , který jste vytvořili v rychlém startu. Klikněte na **Upravit**.

    > [!div class="mx-imgBorder"]
    > ![Upravit příznak funkce beta verze](./media/edit-beta-feature-flag.png)

1. Na obrazovce pro **Úpravy** vyberte přepínač **zapnuto** , pokud ještě není vybraný. Pak klikněte na tlačítko **Přidat filtr** . (Popisek **na** přepínači se změní na číst **podmíněné**.)

1. Do pole **klíč** zadejte *Microsoft. PERCENTAGE*.

    > [!div class="mx-imgBorder"]
    > ![Přidat filtr funkcí](./media/feature-flag-add-filter.png)

1. Klikněte na místní nabídku vedle klíče filtru funkcí. Klikněte na **Upravit parametry**.

    > [!div class="mx-imgBorder"]
    > ![Upravit parametry filtru funkcí](./media/feature-flag-edit-filter-parameters.png)

1. Najeďte myší pod záhlavím **název** tak, aby se textová pole zobrazovala v mřížce. Zadejte **název** *hodnoty* a **hodnotu** 50. Pole **hodnota** označuje procento požadavků, u kterých má být filtr funkcí povolen.

    > [!div class="mx-imgBorder"]
    > ![Nastavení parametrů filtru funkcí](./media/feature-flag-set-filter-parameters.png)

1. Klikněte na **použít** a vraťte se na obrazovku **příznaku pro úpravu funkce** . Pak klikněte na **použít** znovu a uložte nastavení příznaku funkce.

1. **Stav** příznaku funkce se teď zobrazuje jako *podmíněný*. Tento stav indikuje, že příznak funkce bude povolený nebo zakázaný podle jednotlivých požadavků, a to na základě kritérií vydaných filtrem funkcí.

    > [!div class="mx-imgBorder"]
    > ![Příznak podmíněné funkce](./media/feature-flag-filter-enabled.png)

## <a name="feature-filters-in-action"></a>Filtry funkcí v akci

Chcete-li zobrazit účinky tohoto příznaku funkce, spusťte aplikaci a stiskněte tlačítko **aktualizovat** v prohlížeči několikrát. Uvidíte, že se položka *beta* zobrazí na panelu nástrojů o 50% času. To je skryto zbývající část času, protože `PercentageFilter` deaktivuje funkci *beta* pro podmnožinu požadavků. Následující video ukazuje toto chování v akci.

> [!div class="mx-imgBorder"]
> ![PercentageFilter v akci](./media/feature-flags-percentagefilter.gif)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Přehled správy funkcí](./concept-feature-management.md)
