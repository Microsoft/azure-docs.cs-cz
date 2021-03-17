---
title: Povolení připraveného zavedení funkcí pro cílové cílové skupiny
titleSuffix: Azure App Configuration
description: Naučte se, jak povolit připravené zavedení funkcí pro cílové cílové skupiny.
ms.service: azure-app-configuration
author: AlexandraKemperMS
ms.author: alkemper
ms.topic: conceptual
ms.date: 11/20/2020
ms.openlocfilehash: c415eaeab2edd0a1b324bba4266266201cb50cbf
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/09/2020
ms.locfileid: "96929680"
---
# <a name="enable-staged-rollout-of-features-for-targeted-audiences"></a>Povolení připraveného zavedení funkcí pro cílové cílové skupiny

Příznaky funkcí umožňují v aplikaci dynamicky aktivovat nebo deaktivovat funkce. Filtry funkcí určují stav příznaku funkce pokaždé, když se vyhodnotí. `Microsoft.FeatureManagement`Knihovna obsahuje `TargetingFilter` , která umožňuje příznak funkce pro zadaný seznam uživatelů a skupin nebo pro zadané procento uživatelů. `TargetingFilter` je "rychlé". To znamená, že jakmile jednotliví uživatelé obdrží funkci, uvidí tuto funkci i u všech budoucích požadavků. Nástroj můžete použít `TargetingFilter` k povolení funkce pro určitý účet během ukázky, pro postupně se zavádějí nové funkce uživatelům v různých skupinách nebo "zazvoněních" a mnohem víc.

V tomto článku se dozvíte, jak zavést novou funkci ve ASP.NET Core webové aplikaci do určených uživatelů a skupin pomocí nástroje `TargetingFilter` s konfigurací aplikací Azure.

## <a name="create-a-web-application-with-feature-flags-and-authentication"></a>Vytvoření webové aplikace s příznaky funkcí a ověřováním

K uvedení funkcí na základě uživatelů a skupin budete potřebovat webovou aplikaci, která uživatelům umožní přihlásit se.

1. Vytvořte webovou aplikaci, která se ověřuje s použitím místní databáze pomocí následujícího příkazu:

   ```dotnetcli
   dotnet new mvc --auth Individual -o TestFeatureFlags
   ```

1. Sestavte a spusťte a pak vyberte odkaz **Registrovat** v pravém horním rohu pro vytvoření nového uživatelského účtu. Použijte e-mailovou adresu `test@contoso.com` . Na obrazovce **potvrzení registrace** vyberte **kliknutím sem potvrďte svůj účet**.

1. Postupujte podle pokynů v [rychlém startu: Přidání příznaků funkcí do aplikace ASP.NET Core](./quickstart-feature-flag-aspnet-core.md) pro přidání příznaku funkce do nové webové aplikace.

1. Zapne příznak funkce v konfiguraci aplikace. Ověřte, zda tato akce řídí viditelnost položky **beta** v navigačním panelu.

## <a name="update-the-web-application-code-to-use-targetingfilter"></a>Aktualizace kódu webové aplikace pro použití TargetingFilter

V tomto okamžiku můžete použít příznak funkce k povolení nebo zakázání `Beta` funkce pro všechny uživatele. Chcete-li povolit příznak funkce pro některé uživatele při jejich zakázání pro ostatní, aktualizujte kód, který chcete použít `TargetingFilter` . V tomto příkladu použijete jako ID uživatele e-mailovou adresu přihlášeného uživatele a jako skupinu určíte část název domény e-mailové adresy. Přidáte uživatele a skupinu do `TargetingContext` . `TargetingFilter`Pomocí tohoto kontextu určí stav příznaku funkce pro každý požadavek.

1. Aktualizujte na nejnovější verzi `Microsoft.FeatureManagement.AspNetCore` balíčku.

   ```dotnetcli
   dotnet add package Microsoft.FeatureManagement.AspNetCore
   ```

1. Přidat soubor *TestTargetingContextAccessor.cs* :

    ```csharp
    using Microsoft.AspNetCore.Http;
    using Microsoft.FeatureManagement.FeatureFilters;
    using System;
    using System.Collections.Generic;
    using System.Threading.Tasks;

    namespace TestFeatureFlags
    {
        public class TestTargetingContextAccessor : ITargetingContextAccessor
        {
            private const string TargetingContextLookup = "TestTargetingContextAccessor.TargetingContext";
            private readonly IHttpContextAccessor _httpContextAccessor;

            public TestTargetingContextAccessor(IHttpContextAccessor httpContextAccessor)
            {
                _httpContextAccessor = httpContextAccessor ?? throw new ArgumentNullException(nameof(httpContextAccessor));
            }

            public ValueTask<TargetingContext> GetContextAsync()
            {
                HttpContext httpContext = _httpContextAccessor.HttpContext;
                if (httpContext.Items.TryGetValue(TargetingContextLookup, out object value))
                {
                    return new ValueTask<TargetingContext>((TargetingContext)value);
                }
                List<string> groups = new List<string>();
                if (httpContext.User.Identity.Name != null)
                {
                    groups.Add(httpContext.User.Identity.Name.Split("@", StringSplitOptions.None)[1]);
                }
                TargetingContext targetingContext = new TargetingContext
                {
                    UserId = httpContext.User.Identity.Name,
                    Groups = groups
                };
                httpContext.Items[TargetingContextLookup] = targetingContext;
                return new ValueTask<TargetingContext>(targetingContext);
            }
        }
    }
    ```

1. Do *Startup.cs* přidejte odkaz na obor názvů *Microsoft. FeatureManagement. FeatureFilters* :

    ```csharp
    using Microsoft.FeatureManagement.FeatureFilters;
    ```

1. Aktualizujte metodu *ConfigureServices* pro registraci za `TargetingFilter` voláním `AddFeatureManagement()` :

    ```csharp
    services.AddFeatureManagement()
            .AddFeatureFilter<TargetingFilter>();
    ```

1. Aktualizujte metodu *ConfigureServices* a přidejte ji do kolekce služeb, kterou jste `TestTargetingContextAccessor` vytvořili v předchozím kroku. *TargetingFilter* ho používá k určení kontextu cílení pokaždé, když je vyhodnocen příznak funkce.

    ```csharp
      services.AddSingleton<ITargetingContextAccessor, TestTargetingContextAccessor>();
    ```

Celá metoda *ConfigureServices* bude vypadat takto:

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));
    services.AddDefaultIdentity<IdentityUser>(options => options.SignIn.RequireConfirmedAccount = true)
            .AddEntityFrameworkStores<ApplicationDbContext>();
    services.AddControllersWithViews();
    services.AddRazorPages();

    // Add feature management, targeting filter, and ITargetingContextAccessor to service collection
    services.AddFeatureManagement().AddFeatureFilter<TargetingFilter>();
    services.AddSingleton<ITargetingContextAccessor, TestTargetingContextAccessor>();
    }
```

## <a name="update-the-feature-flag-to-use-targetingfilter"></a>Aktualizace příznaku funkce pro použití TargetingFilter

1. V Azure Portal přejdete do konfiguračního úložiště aplikace a vyberete **správce funkcí**.

1. Vyberte kontextovou nabídku pro příznak funkce *beta* , který jste vytvořili v rychlém startu. Vyberte **Edit** (Upravit).

    > [!div class="mx-imgBorder"]
    > ![Upravit příznak funkce beta verze](./media/edit-beta-feature-flag.png)

1. Na obrazovce pro **Úpravy** zaškrtněte políčko **Povolit příznak funkce** , pokud již není vybráno. Pak zaškrtněte políčko **použít filtr funkcí** .

1. Vyberte přepínač **cílení** .

1. Zadejte následující možnosti:

    - **Výchozí procento**: 0
    - **Skupiny**: zadejte **název** _contoso.com_ a **procento** _50_ .
    - **Uživatelé**: `test@contoso.com`

    Obrazovka filtru funkcí bude vypadat takto:

    > [!div class="mx-imgBorder"]
    > ![Příznak podmíněné funkce](./media/feature-flag-filter-enabled.png)

    Výsledkem těchto nastavení je následující chování:

    - Příznak funkce je vždy povolen pro uživatele `test@contoso.com` , protože `test@contoso.com` je uveden v části _Uživatelé_ .
    - Příznak funkce je povolený pro 50% dalších uživatelů ve skupině _contoso.com_ , protože _contoso.com_ je uvedený v oddílu _groups_ s _procentem_ _50_.
    - Tato funkce je vždycky zakázaná pro všechny ostatní uživatele, protože _výchozí procento_ je nastavené na _0_.

1. Výběrem možnosti **použít** uložíte tato nastavení a vrátíte se na obrazovku **správce funkcí** .

1. **Filtr funkcí** pro příznak funkce se teď zobrazuje jako *cílený*. Tento stav indikuje, že příznak funkce bude povolený nebo zakázaný podle jednotlivých požadavků, a to na základě kritérií vydaných filtrem funkcí *cílení* .

## <a name="targetingfilter-in-action"></a>TargetingFilter v akci

Chcete-li zobrazit účinky tohoto příznaku funkce, sestavte a spusťte aplikaci. Zpočátku se položka *beta* nezobrazí na panelu nástrojů, protože _výchozí procento_ je nastavené na 0.

Teď se přihlaste jako `test@contoso.com` a použijte heslo, které jste nastavili při registraci. Položka *beta verze* se nyní zobrazí na panelu nástrojů, protože `test@contoso.com` je zadána jako cílový uživatel.

Následující video ukazuje toto chování v akci.

> [!div class="mx-imgBorder"]
> ![TargetingFilter v akci](./media/feature-flags-targetingfilter.gif)

Můžete vytvořit další uživatele s `@contoso.com` e-mailovými adresami, abyste viděli chování nastavení skupiny. 50% těchto uživatelů uvidí položku *beta* . Druhá 50% neuvidí položku *beta verze* .

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Přehled správy funkcí](./concept-feature-management.md)