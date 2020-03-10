---
title: Kurz použití příznaků funkcí v aplikaci pro jarní spuštění – konfigurace aplikací Azure | Microsoft Docs
description: V tomto kurzu se naučíte implementovat příznaky funkcí v aplikacích pro jarní spouštění.
services: azure-app-configuration
documentationcenter: ''
author: mrm9084
manager: zhenlwa
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: java
ms.topic: tutorial
ms.date: 09/26/2019
ms.author: mametcal
ms.custom: mvc
ms.openlocfilehash: d519ac44d617f725aa9b3d3f11671122bd9477bc
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2020
ms.locfileid: "78944316"
---
# <a name="tutorial-use-feature-flags-in-a-spring-boot-app"></a>Kurz: používání příznaků funkcí v aplikaci pro jarní spuštění

Knihovny pro správu funkcí jádra pružinového spouštění poskytují podporu pro implementaci příznaků funkcí v aplikaci pro spouštění pružin. Tyto knihovny umožňují deklarativní Přidání příznaků funkcí do kódu.

Knihovny správy funkcí také spravují životní cykly příznaků funkcí na pozadí. Například knihovny aktualizace knihoven a příznak cache nebo zaručují, že stav příznaku by měl být během volání žádosti neměnný. Kromě toho knihovna pružinového spouštění nabízí integrace, včetně akcí kontroleru MVC, tras a middlewaru.

[Příznaky funkce Přidat k rychlému startu při spuštění aplikace](./quickstart-feature-flag-spring-boot.md) ukazují několik způsobů přidávání příznaků funkcí do aplikace pro spouštění pružiny. V tomto kurzu se tyto metody vysvětlují podrobněji.

V tomto kurzu se naučíte, jak:

> [!div class="checklist"]
> * Přidání příznaků funkcí v klíčových částech vaší aplikace pro řízení dostupnosti funkcí.
> * Integrujte s konfigurací aplikace, když ji používáte ke správě příznaků funkcí.

## <a name="set-up-feature-management"></a>Nastavení správy funkcí

Správce funkcí pro spouštění pružiny `FeatureManager` získá příznaky funkcí z nativního konfiguračního systému rozhraní. V důsledku toho můžete definovat příznaky funkcí vaší aplikace pomocí veškerého zdroje konfigurace, který služba pružinového spouštění podporuje, včetně místního souboru *bootstrap. yml* nebo proměnných prostředí. `FeatureManager` spoléhá na vkládání závislostí. Služby správy funkcí můžete zaregistrovat pomocí standardních konvencí:

```java
private FeatureManager featureManager;

public HelloController(FeatureManager featureManager) {
    this.featureManager = featureManager;
}
```

Doporučujeme zachovat příznaky funkcí mimo aplikaci a spravovat je samostatně. V takovém případě vám umožní kdykoli změnit stav příznaku a tyto změny se projeví v aplikaci hned. Konfigurace aplikací poskytuje centralizované místo pro organizování a řízení všech příznaků funkcí prostřednictvím vyhrazeného uživatelského rozhraní portálu. Konfigurace aplikace také poskytuje příznaky vaší aplikaci přímo prostřednictvím spouštěcích klientských knihoven pro jaře.

Nejjednodušší způsob, jak připojit aplikaci pro spouštění pružin ke konfiguraci aplikace, je prostřednictvím poskytovatele konfigurace:

### <a name="spring-cloud-11x"></a>Jarní Cloud 1.1. x

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-azure-feature-management-web</artifactId>
    <version>1.1.2</version>
</dependency>
```

### <a name="spring-cloud-12x"></a>Jarní Cloud 1.2. x

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-azure-feature-management-web</artifactId>
    <version>1.2.2</version>
</dependency>
```

## <a name="feature-flag-declaration"></a>Deklarace příznaku funkce

Každý příznak funkce má dvě části: název a seznam jednoho nebo více filtrů, které se používají k vyhodnocení, jestli je stav funkce *zapnutý* (to znamená, když je jeho hodnota `True`). Filtr definuje případ použití, kdy má být funkce zapnuta.

Pokud má příznak funkce více filtrů, seznam filtrů se prochází v pořadí, dokud jeden z filtrů neurčí, že by měla být funkce povolená. V tomto okamžiku je příznak funkce *zapnutý*a všechny zbývající výsledky filtru se přeskočí. Pokud žádný filtr neindikuje, že by měla být funkce povolená, příznak funkce je *vypnutý*.

Správce funkcí podporuje jako zdroj konfigurace pro příznaky funkcí *Application. yml* . Následující příklad ukazuje, jak nastavit příznaky funkcí v souboru YAML:

```yml
feature-management:
  feature-set:
    feature-a: true
    feature-b: false
    feature-c:
      enabled-for:
        -
          name: Percentage
          parameters:
            value: 50
```

Podle konvence se `feature-management` oddíl tohoto dokumentu YML používá pro nastavení příznaku funkce. Předchozí příklad ukazuje tři příznaky funkcí s jejich filtry definovanými ve vlastnosti `EnabledFor`:

* `feature-a` je *zapnuto*.
* `feature-b` je *vypnutý*.
* `feature-c` Určuje filtr s názvem `Percentage` s vlastností `parameters`. `Percentage` je konfigurovatelný filtr. V tomto příkladu `Percentage` určuje pravděpodobnost 50% pro příznak `feature-c`, který má být *zapnut*.

## <a name="feature-flag-checks"></a>Kontroly příznaků funkcí

Základním vzorem správy funkcí je nejprve zjistit, zda je příznak funkce nastaven na hodnotu *zapnuto*. V takovém případě správce funkcí spustí akce, které funkce obsahuje. Příklad:

```java
private FeatureManager featureManager;
...
if (featureManager.isEnabledAsync("feature-a").block()) {
    // Run the following code
}
```

## <a name="dependency-injection"></a>Injektáž závislosti

V případě jarního spuštění můžete k `FeatureManager` správce funkcí přistupovat prostřednictvím injektáže závislosti:

```java
@Controller
@ConfigurationProperties("controller")
public class HomeController {
    private FeatureManager featureManager;

    public HomeController(FeatureManager featureManager) {
        this.featureManager = featureManager;
    }
}
```

## <a name="controller-actions"></a>Akce kontroleru

V řadičích MVC pomocí atributu `@FeatureGate` určíte, jestli je povolená konkrétní akce. Následující akce `Index` vyžaduje, *aby předtím, než bude možné* spustit:, `feature-a`.

```java
@GetMapping("/")
@FeatureGate(feature = "feature-a")
public String index(Model model) {
    ...
}
```

Když je kontroler MVC nebo akce zablokovaná, protože příznak řídicí funkce je *vypnutý*, zavolá se registrované `IDisabledFeaturesHandler` rozhraní. Výchozí `IDisabledFeaturesHandler` rozhraní vrátí klientovi stav 404 bez těla odpovědi.

## <a name="mvc-filters"></a>Filtry MVC

Filtry MVC můžete nastavit tak, aby byly aktivovány na základě stavu příznaku funkce. Následující kód přidá filtr MVC s názvem `FeatureFlagFilter`. Tento filtr se aktivuje v rámci kanálu MVC jenom v případě, že je povolená možnost `feature-a`.

```java
@Component
public class FeatureFlagFilter implements Filter {

    @Autowired
    private FeatureManager featureManager;

    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain)
            throws IOException, ServletException {
        if(!featureManager.isEnabledAsync("feature-a").block()) {
            chain.doFilter(request, response);
            return;
        }
        ...
        chain.doFilter(request, response);
    }
}
```

## <a name="routes"></a>Trasy

Pomocí příznaků funkcí můžete přesměrovat trasy. Následující kód bude přesměrovat uživatele z `feature-a` je povolen:

```java
@GetMapping("/redirect")
@FeatureGate(feature = "feature-a", fallback = "/getOldFeature")
public String getNewFeature() {
    // Some New Code
}

@GetMapping("/getOldFeature")
public String getOldFeature() {
    // Some New Code
}
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak implementovat příznaky funkcí v aplikaci pro spouštění po jaře pomocí knihoven `spring-cloud-azure-feature-management-web`. Další informace o podpoře správy funkcí v konfiguraci jarního spuštění a aplikace najdete v následujících zdrojích informací:

* [Vzorový kód příznaku funkce pro spuštění pružiny](/azure/azure-app-configuration/quickstart-feature-flag-spring-boot)
* [Správa příznaků funkcí](./manage-feature-flags.md)
