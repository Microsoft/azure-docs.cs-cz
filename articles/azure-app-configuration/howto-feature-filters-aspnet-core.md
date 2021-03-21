---
title: Použití filtrů funkcí k zapnutí příznaků podmíněné funkce
titleSuffix: Azure App Configuration
description: Naučte se používat filtry funkcí k zapnutí příznaků podmíněné funkce.
ms.service: azure-app-configuration
ms.custom: devx-track-csharp
author: AlexandraKemperMS
ms.author: alkemper
ms.topic: conceptual
ms.date: 3/9/2020
ms.openlocfilehash: 39455c4bc193cce036bd169c702b5c020d53d2f6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98602245"
---
# <a name="use-feature-filters-to-enable-conditional-feature-flags"></a>Použití filtrů funkcí k zapnutí příznaků podmíněné funkce

Příznaky funkcí umožňují aktivovat nebo deaktivovat funkce v aplikaci. Jednoduchý příznak funkce je buď zapnutý, nebo vypnutý. Aplikace se vždy chová stejným způsobem. Můžete například zavést novou funkci za příznakem funkce. Když je příznak funkce povolený, všichni uživatelé uvidí novou funkci. Zakázáním příznaku funkce se nová funkce skryje.

Naproti tomu _příznak podmíněné funkce_ umožňuje, aby byl příznak funkce povolen nebo zakázán dynamicky. Aplikace se může chovat odlišně v závislosti na kritériích příznaků funkcí. Předpokládejme, že chcete novou funkci Zobrazit pro malou podmnožinu uživatelů v prvním. Příznak podmíněné funkce vám umožní povolit příznak funkce pro některé uživatele a zároveň ho zakázat ostatním uživatelům. _Filtry funkcí_ určují stav příznaku funkce pokaždé, když se vyhodnotí.

`Microsoft.FeatureManagement`Knihovna obsahuje tři filtry funkcí:

- `PercentageFilter` Povolí příznak funkce na základě procenta.
- `TimeWindowFilter` Povolí příznak funkce během zadaného časového období.
- `TargetingFilter` Povolí příznak funkce pro zadané uživatele a skupiny.

Můžete také vytvořit vlastní filtr funkcí, který implementuje [rozhraní Microsoft. FeatureManagement. IFeatureFilter](/dotnet/api/microsoft.featuremanagement.ifeaturefilter).

## <a name="registering-a-feature-filter"></a>Registrace filtru funkcí

Zaregistrujte filtr funkcí voláním `AddFeatureFilter` metody a zadáním názvu typu požadovaného filtru funkce. Například následující Registry kódu `PercentageFilter` :

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

1. Na obrazovce pro **Úpravy** zaškrtněte políčko **Povolit příznak funkce** , pokud již není povoleno. Potom zaškrtněte políčko **použít filtr funkcí** a vyberte **vlastní**. 

1. V poli **název** vyberte *Microsoft. PERCENTAGE*.

    > [!div class="mx-imgBorder"]
    > ![Přidat filtr funkcí](./media/feature-flag-add-filter.png)

1. Klikněte na místní nabídku vedle názvu filtru funkce. Klikněte na **Upravit parametry filtru**.

    > [!div class="mx-imgBorder"]
    > ![Upravit parametry filtru funkcí](./media/feature-flags-edit-filter-parameters.png)

1. Zadejte **název** *hodnoty* a **hodnotu** 50. Pole **hodnota** označuje procento požadavků, u kterých má být filtr funkcí povolen.

    > [!div class="mx-imgBorder"]
    > ![Nastavení parametrů filtru funkcí](./media/feature-flag-set-filter-parameters.png)

1. Klikněte na **použít** a vraťte se na obrazovku **příznaku pro úpravu funkce** . Pak klikněte na **použít** znovu a uložte nastavení příznaku funkce.

1. Příznak funkce na stránce **správce funkcí** má nyní hodnotu **filtru funkce** *Custom*. 

    > [!div class="mx-imgBorder"]
    > ![Příznak funkce uvedený v hodnotě filtru funkce Custom (vlastní)](./media/feature-flag-filter-custom.png)

## <a name="feature-filters-in-action"></a>Filtry funkcí v akci

Chcete-li zobrazit účinky tohoto příznaku funkce, spusťte aplikaci a stiskněte tlačítko **aktualizovat** v prohlížeči několikrát. Uvidíte, že se položka *beta* zobrazí na panelu nástrojů o 50% času. To je skryto zbývající část času, protože `PercentageFilter` deaktivuje funkci *beta* pro podmnožinu požadavků. Následující video ukazuje toto chování v akci.

> [!div class="mx-imgBorder"]
> ![TargetingFilter v akci](./media/feature-flags-percentagefilter.gif)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Povolení připraveného zavedení funkcí pro cílové cílové skupiny](./howto-targetingfilter-aspnet-core.md)
