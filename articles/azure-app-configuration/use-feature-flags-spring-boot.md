---
title: Kurz pro používání příznaků funkcí v aplikaci spring boot – konfigurace aplikace Azure | Dokumenty společnosti Microsoft
description: V tomto kurzu se dozvíte, jak implementovat příznaky funkcí v aplikacích jarního spuštění.
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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "78944316"
---
# <a name="tutorial-use-feature-flags-in-a-spring-boot-app"></a>Kurz: Použití příznaků funkcí v aplikaci Spring Boot

Knihovny Správy funkcí jádra základního spouštění jarního spouštění poskytují podporu pro implementaci příznaků funkcí v aplikaci spring boot. Tyto knihovny umožňují deklarativně přidat příznaky funkce do kódu.

Knihovny správy funkcí také spravují životní cykly příznaku funkcí na pozadí. Například knihovny aktualizovat a cache příznak stavy nebo zaručit stav příznaku neměnné během volání požadavku. Knihovna Spring Boot navíc nabízí integrace, včetně akcí řadiče MVC, tras a middlewaru.

Příznaky [funkce Přidat do aplikace Spring Boot Úvodní příručka](./quickstart-feature-flag-spring-boot.md) ukazuje několik způsobů, jak přidat příznaky funkcí v aplikaci jarního spuštění. Tento kurz vysvětluje tyto metody podrobněji.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Přidejte příznaky funkcí v klíčových částech aplikace k řízení dostupnosti funkcí.
> * Integrujte s konfigurací aplikace, když ji používáte ke správě příznaků funkcí.

## <a name="set-up-feature-management"></a>Nastavit správu funkcí

Správce `FeatureManager` funkcí jarního spuštění získává příznaky funkcí z nativního konfiguračního systému rozhraní. V důsledku toho můžete definovat příznaky funkcí aplikace pomocí libovolného zdroje konfigurace, který podporuje jarní spuštění, včetně místních proměnných *souboru bootstrap.yml* nebo prostředí. `FeatureManager`spoléhá na vkládání závislostí. Služby správy funkcí můžete zaregistrovat pomocí standardních konvencí:

```java
private FeatureManager featureManager;

public HelloController(FeatureManager featureManager) {
    this.featureManager = featureManager;
}
```

Doporučujeme zachovat příznaky funkcí mimo aplikaci a spravovat je samostatně. To vám umožní kdykoli upravit stavy příznaku a tyto změny se projeví v aplikaci ihned. Konfigurace aplikace poskytuje centralizované místo pro uspořádání a řízení všech příznaků funkcí prostřednictvím vyhrazeného portálového uznatého. Konfigurace aplikace také doručuje příznaky do vaší aplikace přímo prostřednictvím svých knihoven klienta Spring Boot.

Nejjednodušší způsob, jak připojit aplikaci spring boot ke konfiguraci aplikace, je prostřednictvím poskytovatele konfigurace:

### <a name="spring-cloud-11x"></a>Jarní mrak 1.1.x

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-azure-feature-management-web</artifactId>
    <version>1.1.2</version>
</dependency>
```

### <a name="spring-cloud-12x"></a>Jarní mrak 1.2.x

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-azure-feature-management-web</artifactId>
    <version>1.2.2</version>
</dependency>
```

## <a name="feature-flag-declaration"></a>Deklarace příznaku prvku

Každý příznak prvku má dvě části: název a seznam jednoho nebo více filtrů, které se používají k vyhodnocení, zda je stav prvku *zapnutý* (tj. pokud je `True`jeho hodnota ). Filtr definuje případ použití, kdy má být prvek zapnutý.

Pokud má příznak prvku více filtrů, je seznam filtrů provázán v pořadí, dokud jeden z filtrů neurčí, že by měla být funkce povolena. V tomto okamžiku je příznak prvku *zapnutý*a všechny zbývající výsledky filtru jsou přeskočeny. Pokud žádný filtr neindikuje, že by měl být prvek povolen, je příznak prvku *vypnutý*.

Správce funkcí podporuje *soubor application.yml* jako zdroj konfigurace pro příznaky funkcí. Následující příklad ukazuje, jak nastavit příznaky prvků v souboru YAML:

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

Podle konvence `feature-management` se část tohoto dokumentu YML používá pro nastavení příznaku funkce. Předchozí příklad ukazuje tři příznaky funkce s `EnabledFor` jejich filtry definované ve vlastnosti:

* `feature-a`je *zapnuta*.
* `feature-b`je *vypnuto*.
* `feature-c`určuje filtr pojmenovaný `Percentage` s `parameters` vlastností. `Percentage`je konfigurovatelný filtr. V tomto `Percentage` příkladu určuje 50% pravděpodobnost `feature-c` příznaku, který má být *na*.

## <a name="feature-flag-checks"></a>Kontroly příznaku funkce

Základní vzor správy prvků je nejprve zkontrolovat, zda je příznak prvku nastaven *na*. Pokud ano, správce funkcí pak spustí akce, které funkce obsahuje. Například:

```java
private FeatureManager featureManager;
...
if (featureManager.isEnabledAsync("feature-a").block()) {
    // Run the following code
}
```

## <a name="dependency-injection"></a>Injektáž závislosti

Při jarním spuštění můžete přistupovat ke správci `FeatureManager` funkcí prostřednictvím vkládání závislostí:

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

## <a name="controller-actions"></a>Akce řadiče

V řadičích MVC můžete `@FeatureGate` pomocí atributu určit, zda je povolena určitá akce. Před `Index` spuštěním `feature-a` je třeba *ji zapnout* následující akce:

```java
@GetMapping("/")
@FeatureGate(feature = "feature-a")
public String index(Model model) {
    ...
}
```

Pokud je řadič nebo akce MVC blokována, protože `IDisabledFeaturesHandler` je *vypnutý*příznak ovládací funkce , je voláno registrované rozhraní. Výchozí `IDisabledFeaturesHandler` rozhraní vrátí 404 stavový kód klientovi bez těla odezvy.

## <a name="mvc-filters"></a>Filtry MVC

Filtry MVC můžete nastavit tak, aby byly aktivovány na základě stavu příznaku prvku. Následující kód přidá filtr MVC s názvem `FeatureFlagFilter`. Tento filtr se aktivuje v rámci `feature-a` kanálu MVC pouze v případě, že je povolena.

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

Příznaky prvků můžete použít k přesměrování tras. Následující kód přesměruje uživatele `feature-a` z je povolena:

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

V tomto kurzu jste se naučili, jak implementovat `spring-cloud-azure-feature-management-web` příznaky funkcí v aplikaci spring boot pomocí knihoven. Další informace o podpoře správy funkcí v nástroji Spring Boot a Konfigurace aplikací naleznete v následujících zdrojích:

* [Ukázkový kód příznaku funkce pružinového spuštění](/azure/azure-app-configuration/quickstart-feature-flag-spring-boot)
* [Správa příznaků funkcí](./manage-feature-flags.md)
