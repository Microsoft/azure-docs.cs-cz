---
title: Použití filtrů funkcí k povolení funkce pro podmnožinu uživatelů
titleSuffix: Azure App Configuration
description: Naučte se používat filtry funkcí k povolení funkce pro podmnožinu uživatelů
ms.service: azure-app-configuration
author: lisaguthrie
ms.author: lcozzens
ms.topic: conceptual
ms.date: 3/9/2020
ms.openlocfilehash: 181c97615985283011834dcf9145810b1563fb4a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80056997"
---
# <a name="use-feature-filters-to-enable-a-feature-for-a-subset-of-users"></a>Použití filtrů funkcí k povolení funkce pro podmnožinu uživatelů

Příznaky funkcí umožňují aktivovat nebo deaktivovat funkce v aplikaci. Příznak jednoduché funkce je zapnutý nebo vypnutý. Aplikace se vždy chová stejným způsobem. Můžete například zavést novou funkci za příznakem prvku. Pokud je příznak funkce povolen, všichni uživatelé uvidí novou funkci. Zakázáním příznaku prvku se nový prvek skryje.

Naproti tomu _příznak podmíněné funkce_ umožňuje dynamicky povolit nebo zakázat příznak funkce. Aplikace se může chovat odlišně v závislosti na kritériích příznaku prvku. Předpokládejme, že chcete novou funkci nejprve zobrazit malé podmnožině uživatelů. Příznak podmíněné funkce umožňuje povolit příznak funkce pro některé uživatele při jeho zakázání pro ostatní uživatele. _Filtry funkcí_ určují stav příznaku prvku při každém vyhodnocení.

Knihovna `Microsoft.FeatureManagement` obsahuje dva filtry funkcí:

- `PercentageFilter`povolí příznak prvku na základě procenta.
- `TimeWindowFilter`povolí příznak prvku během zadaného časového období.

Můžete také vytvořit vlastní filtr funkcí, který implementuje [rozhraní Microsoft.FeatureManagement.IFeatureFilter](/dotnet/api/microsoft.featuremanagement.ifeaturefilter).

## <a name="registering-a-feature-filter"></a>Registrace filtru funkcí

Filtr prvku můžete zaregistrovat `AddFeatureFilter` voláním metody a zadáním názvu filtru prvku. Například následující kód registry `PercentageFilter`:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllersWithViews();
    services.AddFeatureManagement().AddFeatureFilter<PercentageFilter>();
}
```

## <a name="configuring-a-feature-filter-in-azure-app-configuration"></a>Konfigurace filtru funkcí v konfiguraci aplikací Azure

Některé filtry funkcí mají další nastavení. Například `PercentageFilter` aktivuje funkci na základě procenta. Má nastavení definující procento, které má být používáno.

Tato nastavení můžete nakonfigurovat pro příznaky funkcí definované v konfiguraci aplikace Azure. Chcete-li například povolit `PercentageFilter` příznak funkce pro 50 % požadavků na webovou aplikaci, postupujte takto:

1. Postupujte podle pokynů na [úvodním panelu: Přidání příznaků funkcí do aplikace ASP.NET Core](./quickstart-feature-flag-aspnet-core.md) vytvořte webovou aplikaci s příznakem funkce.

1. Na webu Azure Portal přejděte do úložiště konfigurace a klikněte na **Správce funkcí**.

1. Klikněte na místní nabídku pro příznak Funkce *beta,* který jste vytvořili v rychlém startu. Klikněte na **Upravit**.

    > [!div class="mx-imgBorder"]
    > ![Upravit příznak funkce beta verze](./media/edit-beta-feature-flag.png)

1. Na obrazovce **Úpravy** vyberte **přepínací** tlačítko Zapnuto, pokud ještě není vybrané. Pak klikněte na tlačítko **Přidat filtr.** (Popisek tlačítka **Na** se změní na **podmíněný**.)

1. Do pole **Klíč** zadejte *Microsoft.Percentage*.

    > [!div class="mx-imgBorder"]
    > ![Přidat filtr prvků](./media/feature-flag-add-filter.png)

1. Klikněte na místní nabídku vedle klíče filtru funkcí. Klepněte na **tlačítko Upravit parametry**.

    > [!div class="mx-imgBorder"]
    > ![Upravit parametry filtru prvků](./media/feature-flag-edit-filter-parameters.png)

1. Najeďte na jenom pod záhlavím **Název,** aby se textová pole zobrazila v mřížce. Zadejte **název** *hodnoty* a **hodnotu** 50. Pole **Hodnota** označuje procento požadavků, pro které má být filtr prvku povolen.

    > [!div class="mx-imgBorder"]
    > ![Nastavení parametrů filtru prvku](./media/feature-flag-set-filter-parameters.png)

1. Kliknutím na **Použít** se vrátíte na obrazovku **příznaku upravit funkci.** Potom dalším kliknutím na **Použít** uložte nastavení příznaku prvku.

1. **Stav** příznaku prvku se nyní zobrazí jako *Podmíněný*. Tento stav označuje, že příznak funkce bude povolen nebo zakázán na základě požadavku na základě kritérií vynucených filtrem funkce.

    > [!div class="mx-imgBorder"]
    > ![Příznak podmíněné funkce](./media/feature-flag-filter-enabled.png)

## <a name="feature-filters-in-action"></a>Filtry funkcí v akci

Chcete-li zobrazit účinky tohoto příznaku funkce, spusťte aplikaci a několikrát stiskněte tlačítko **Aktualizovat** v prohlížeči. Zobrazí se, že se položka *beta* zobrazuje na panelu nástrojů přibližně v 50 % času. Zbytek času je skrytý, protože `PercentageFilter` deaktivuje funkci *Beta* pro podmnožinu požadavků. Následující video ukazuje toto chování v akci.

> [!div class="mx-imgBorder"]
> ![PercentageFilter v akci](./media/feature-flags-percentagefilter.gif)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Přehled správy funkcí](./concept-feature-management.md)
