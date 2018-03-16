---
title: "Průvodce odstraňováním potíží s nasazení Azure Machine Learning | Microsoft Docs"
description: "Řešení potíží s Příručka pro nasazení a vytvoření služby"
services: machine-learning
author: AashishB
ms.author: AashishB
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 11/16/2017
ms.openlocfilehash: 614767840f8781c3c30c358dcf3ffc366aa3c0d6
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="troubleshooting-service-deployment-and-environment-setup"></a>Nasazení služby a nastavení prostředí pro řešení potíží
Následující informace vám mohou pomoci určit příčinu chyby při nastavování prostředí správy modelu.

## <a name="model-management-environment"></a>Model správy prostředí
### <a name="contributor-permission-required"></a>Potřebná oprávnění přispěvatele
Je nutné použít Přispěvatel pro předplatné nebo skupinu prostředků nastavit cluster pro nasazení webových služeb.

### <a name="resource-availability"></a>Dostupnost prostředků
Musíte mít k dispozici ve vašem předplatném dostatek prostředků, můžete zřídit prostředky prostředí.

### <a name="subscription-caps"></a>Předplatné CAP k vzdálené ploše
Vaše předplatné může mít zakončení na fakturace, která by mohla zabránit vám v zřizování prostředků prostředí. Odeberte cap povolit zřizování.

### <a name="enable-debug-and-verbose-options"></a>Povolit ladění a podrobné možnosti
Použití `--debug` a `--verbose` příznaky v možnosti instalačního příkazu zobrazíte informace o ladění a trasování, jak se připravuje prostředí.

```
az ml env setup -l <location> -n <name> -c --debug --verbose 
```

## <a name="service-deployment"></a>Nasazení služby
Následující informace vám mohou pomoci určit příčinu chyby během nasazení nebo při volání metody webové služby.

### <a name="service-logs"></a>protokoly služby
`logs` Možnost služby rozhraní příkazového řádku obsahuje data protokolu z Docker a Kubernetes.

```
az ml service logs realtime -i <web service id>
```

Pro nastavení dalších protokolů, použijte `--help` (nebo `-h`) možnost.

```
az ml service logs realtime -h
```

### <a name="debug-and-verbose-options"></a>Ladění a podrobné možnosti
Použití `--debug` příznak, který zobrazit protokoly pro ladění, protože se nasazení služby.

```
az ml service create realtime -m <modelfile>.pkl -f score.py -n <service name> -r python --debug
```

Použití `--verbose` příznak zobrazíte další podrobnosti, jako je nasazení služby.

```
az ml service create realtime -m <modelfile>.pkl -f score.py -n <service name> -r python --verbose
```

### <a name="enable-request-logging-in-app-insights"></a>Povolit protokolování ve službě App Insights požadavku
Nastavte `-l` příznak na hodnotu true při vytváření webovou službu, kterou úroveň protokolování požadavku. Žádost o protokoly se zapisují do instance aplikace statistiky pro vaše prostředí v Azure. Hledání pro tuto instanci pomocí názvu prostředí jste použili při použití `az ml env setup` příkaz.

- Nastavit `-l` na hodnotu true při vytváření služby.
- Otevřete App Insights na portálu Azure. Použijte název prostředí hledání instance Statistika aplikace.
- Jednou v App Insights, klikněte na Hledat v horní nabídce zobrazíte výsledky.
- Nebo přejděte na `Analytics`  >  `Exceptions`  >  `exceptions take | 10`.


### <a name="add-error-handling-in-scoring-script"></a>Přidání zpracování chyb v vyhodnocování skriptu
Použití výjimek v vaše `scoring.py` skriptu **spustit** funkce, která se vrátí chybovou zprávu jako součást webové služby výstupu.

Příklad Python:
```
    try:
        <code to load model and score>
   except Exception as e:
        return(str(e))
```

## <a name="other-common-problems"></a>Další běžné problémy
- Pokud pip instalace rozhraní příkazového řádku. azure ml selže s chybou `cannot find the path specified` na počítači s Windows, musíte povolit podporu dlouhé cesty. V tématu https://blogs.msdn.microsoft.com/jeremykuhne/2016/07/30/net-4-6-2-and-long-paths-on-windows-10/. 
- Pokud `env setup` příkaz selže s `LocationNotAvailableForResourceType`, pravděpodobně používáte nesprávný umístěním (oblastí) strojového učení prostředky. Zajistěte, aby vaše umístění určeném pomocí `-l` parametr `eastus2`, `westcentralus`, nebo `australiaeast`.
- Pokud `env setup` příkaz selže s `Resource quota limit exceeded`, ujistěte se, máte dostatek jader ve vašem předplatném dostupná a že vaše prostředky nejsou používány až v jiné procesy.
- Pokud `env setup` příkaz selže s `Invalid environment name. Name must only contain lowercase alphanumeric characters`, ujistěte se, velká písmena, symboly nebo podtržítko (_) neobsahuje název služby (jako v *my_environment*).
- Pokud `service create` příkaz selže s `Service Name: [service_name] is invalid. The name of a service must consist of lower case alphanumeric characters (etc.)`, zkontrolujte, zda název služby je 3 až 32 znaků; spustí a končí malé alfanumerické znaky; a neobsahuje velká písmena, symboly než pomlčku (-) a období ( . ), nebo podtržítko (_) (jako v *my_webservice*).
- Opakovat, pokud dojde `502 Bad Gateway` Chyba při volání metody webové služby. Obvykle znamená to, že kontejner nebyla do clusteru ještě nasazena.
- Pokud se zobrazí `CrashLoopBackOff` chyby při vytváření služby, najdete v protokolech. Obvykle je výsledkem chybějící závislosti v **init** funkce.
