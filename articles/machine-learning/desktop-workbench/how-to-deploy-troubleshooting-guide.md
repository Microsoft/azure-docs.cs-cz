---
title: Průvodce odstraňováním potíží nasazení v Azure Machine Learning | Dokumentace Microsoftu
description: Průvodce odstraňováním potíží pro nasazení a vytvoření služby
services: machine-learning
author: aashishb
ms.author: aashishb
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 11/16/2017
ROBOTS: NOINDEX
ms.openlocfilehash: a2867545e454e2b13360d87f5282e684753d6476
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46994492"
---
# <a name="troubleshooting-service-deployment-and-environment-setup"></a>Řešení potíží s nasazení služby a nastavení prostředí

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)]

Následující informace vám pomůže určit příčinu chyby při nastavování prostředí pro správu modelu.

## <a name="model-management-environment"></a>Prostředí pro správu modelů
### <a name="contributor-permission-required"></a>Požadováno oprávnění přispěvatele
Potřebujete přístup přispěvatele předplatného nebo skupiny prostředků pro nastavení clusteru pro nasazení vašich webových služeb.

### <a name="resource-availability"></a>Dostupnost prostředků
Musíte mít dostatek prostředků k dispozici v rámci vašeho předplatného, tak můžete poskytovat prostředky prostředí.

### <a name="subscription-caps"></a>Limity předplatného
Vaše předplatné může mít zakončení na fakturaci, která by mohla by vám bránily v zřizování prostředků prostředí. Odeberte cap k zapnutí zřizování.

### <a name="enable-debug-and-verbose-options"></a>Povolit ladění a podrobné možnosti
Použití `--debug` a `--verbose` příznaky v příkazu instalace se zobrazit informace o ladění a trasování, protože prostředí se zřizuje.

```
az ml env setup -l <location> -n <name> -c --debug --verbose 
```

## <a name="service-deployment"></a>Nasazení služby
Následující informace vám pomůže určit příčinu chyby během nasazování nebo při volání webové služby.

### <a name="service-logs"></a>Protokoly služby
`logs` Možnost služby rozhraní příkazového řádku poskytuje data protokolu z Dockeru a Kubernetes.

```
az ml service logs realtime -i <web service id>
```

Nastavení dalších protokolů, použijte `--help` (nebo `-h`) možnost.

```
az ml service logs realtime -h
```

### <a name="debug-and-verbose-options"></a>Ladění a podrobné možnosti
Použití `--debug` příznak se zobrazit protokoly ladění, protože během nasazování služby.

```
az ml service create realtime -m <modelfile>.pkl -f score.py -n <service name> -r python --debug
```

Použití `--verbose` příznak zobrazíte další podrobnosti, jak během nasazování služby.

```
az ml service create realtime -m <modelfile>.pkl -f score.py -n <service name> -r python --verbose
```

### <a name="enable-request-logging-in-app-insights"></a>Povolit žádosti o přihlášení App Insights
Nastavte `-l` příznak na hodnotu true při vytváření webové služby povolit žádosti o úroveň protokolování. Žádost o protokoly zapisují na instanci App Insights pro vaše prostředí v Azure. Hledání pro tuto instanci pomocí názvu prostředí, který jste použili při použití `az ml env setup` příkazu.

- Nastavte `-l` na hodnotu true při vytváření služby.
- Otevřete App Insights na webu Azure portal. Použijte název vašeho prostředí a najděte instanci App Insights.
- Jednou v App Insights, klikněte na Search v horní nabídce zobrazíte výsledky.
- Nebo můžete přejít na `Analytics`  >  `Exceptions`  >  `exceptions take | 10`.


### <a name="add-error-handling-in-scoring-script"></a>Přidání zpracování chyb v vyhodnocení skriptu
Použijte zpracování výjimek v vaše `scoring.py` skriptu **spustit** funkce vrací chybovou zprávu jako součást webové služby výstupu.

Příklad v Pythonu:
```
    try:
        <code to load model and score>
   except Exception as e:
        return(str(e))
```

## <a name="other-common-problems"></a>Ostatní problémy
- Pokud pip install azure-cli-ml selže s chybou `cannot find the path specified` na počítač s Windows, musíte povolit dlouhé cesty podporu. Zobrazit https://blogs.msdn.microsoft.com/jeremykuhne/2016/07/30/net-4-6-2-and-long-paths-on-windows-10/. 
- Pokud `env setup` příkaz selže s `LocationNotAvailableForResourceType`, pravděpodobně používáte nesprávné umístění (oblast) pro počítač studijních materiálů. Ujistěte se, že vaše umístění určeném pomocí `-l` parametr `eastus2`, `westcentralus`, nebo `australiaeast`.
- Pokud `env setup` příkaz selže s `Resource quota limit exceeded`, ujistěte se, že máte dostatek jader dostupných v rámci vašeho předplatného a nepoužívají prostředky až do jiných procesů.
- Pokud `env setup` příkaz selže s `Invalid environment name. Name must only contain lowercase alphanumeric characters`, ujistěte se, že název služby neobsahuje velká písmena, symboly nebo podtržítko (_) (jako v *my_environment*).
- Pokud `service create` příkaz selže s `Service Name: [service_name] is invalid. The name of a service must consist of lower case alphanumeric characters (etc.)`, ujistěte se, že název služby je dlouhý 3 až 32 znaků; začíná a končí malé alfanumerické znaky; a nesmí obsahovat velká písmena, symboly než spojovníku (-) a období ( . ), nebo podtržítko (_) (jako v *my_webservice*).
- Zkuste to znovu, pokud se zobrazí `502 Bad Gateway` při volání webové služby došlo k chybě. Obvykle znamená to, že kontejner nebyla nasazená do clusteru.
- Pokud se zobrazí `CrashLoopBackOff` Chyba při vytváření služby, najdete v protokolech. Obvykle je výsledkem chybějící závislosti v **init** funkce.
