---
title: Konfigurace kontejnerů
titlesuffix: Text Analytics - Azure Cognitive Services
description: Rozhraní text Analytics poskytuje každý kontejner se společnou architekturu konfigurace, takže můžete snadno konfigurovat a spravovat úložiště, protokolování a telemetrická data a nastavení zabezpečení pro vaše kontejnery.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: conceptual
ms.date: 11/14/2018
ms.author: diberry
ms.openlocfilehash: 7e993b9ccc57359ac64186765b7b704535eb5a57
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53086670"
---
# <a name="configure-containers"></a>Konfigurace kontejnerů

Rozhraní text Analytics poskytuje každý kontejner se společnou architekturu konfigurace, takže můžete snadno konfigurovat a spravovat úložiště, protokolování a telemetrická data a nastavení zabezpečení pro vaše kontejnery.

## <a name="configuration-settings"></a>Nastavení konfigurace

Nastavení konfigurace v kontejnerech pro analýzu textu jsou hierarchické a všechny kontejnery, které používají sdílené hierarchii, podle následující strukturu nejvyšší úrovně:

* [ApiKey](#apikey-configuration-setting)
* [ApplicationInsights](#applicationinsights-configuration-settings)
* [Ověřování](#authentication-configuration-settings)
* [Fakturace](#billing-configuration-setting)
* [Smlouva EULA](#eula-configuration-setting)
* [Fluentd](#fluentd-configuration-settings)
* [Protokolování](#logging-configuration-settings)
* [Připojí](#mounts-configuration-settings)

Můžete použít buď [proměnné prostředí](#configuration-settings-as-environment-variables) nebo [argumenty příkazového řádku](#configuration-settings-as-command-line-arguments) ke specifikaci nastavení konfigurace, při vytváření instance kontejneru z kontejnerů pro analýzu textu.

Hodnoty proměnných prostředí přepsat hodnoty argumentů příkazového řádku, které pak přepsat výchozí hodnoty pro image kontejneru. Jinými slovy Pokud zadáte jiné hodnoty v proměnné prostředí a argument příkazového řádku pro stejné nastavení konfigurace, jako například `Logging:Disk:LogLevel`, pak vytvoření instance kontejneru, je použita hodnota v proměnné prostředí podle instance kontejner.

### <a name="configuration-settings-as-environment-variables"></a>Nastavení konfigurace jako proměnné prostředí

Můžete použít [syntaxi proměnných prostředí ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.1&tabs=basicconfiguration#configuration-by-environment) ke specifikaci nastavení konfigurace.

Kontejner přečte uživatelských proměnných prostředí při vytváření instance kontejneru. Pokud proměnná prostředí existuje, přepíše hodnotu proměnné prostředí výchozí hodnota pro nastavení konfigurace. Výhodou použití proměnných prostředí je, že více nastavení konfigurace můžete nastavit před vytvořením instance kontejnerů a několik kontejnerů může automaticky použít stejnou sadu nastavení konfigurace.

Například následující příkazy použijte proměnnou prostředí nakonfigurovat úroveň protokolování konzoly do [LogLevel.Information](https://msdn.microsoft.com), pak vytvoří kontejner z image kontejneru analýzu subjektivního hodnocení. Hodnota proměnné prostředí, přepíše výchozí nastavení konfigurace.

  ```Docker
  SET Logging:Console:LogLevel=Information
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 mcr.microsoft.com/azure-cognitive-services/sentiment Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.0 ApiKey=0123456789
  ```

### <a name="configuration-settings-as-command-line-arguments"></a>Nastavení konfigurace jako argumenty příkazového řádku

Můžete použít [syntaxi ASP.NET Core argument příkazového řádku](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.1&tabs=basicconfiguration#arguments) ke specifikaci nastavení konfigurace.

Můžete určit nastavení konfigurace volitelné `ARGS` parametr [dockeru spustit](https://docs.docker.com/engine/reference/commandline/run/) příkazu používaný k vytváření instancí kontejner z image kontejneru stažené. Výhodou použití argumentů příkazového řádku je, že každý kontejner můžete použít různé, vlastní sadu nastavení konfigurace.

Například následující příkaz vytvoří kontejner z image kontejneru analýzu subjektivního hodnocení a nakonfiguruje úroveň protokolování pro LogLevel.Information přepisuje výchozí nastavení konfigurace konzoly.

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 mcr.microsoft.com/azure-cognitive-services/sentiment Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.0 ApiKey=0123456789 Logging:Console:LogLevel=Information
  ```

## <a name="apikey-configuration-setting"></a>Nastavení konfigurace ApiKey

`ApiKey` Nastavení konfigurace určuje konfigurační klíč prostředku pro analýzu textu na Azure, která slouží ke sledování fakturačních údajů pro kontejner. Musíte zadat hodnotu pro toto nastavení konfigurace, a hodnota musí být platný konfigurační klíč pro zadaný pro prostředek pro analýzu textu [ `Billing` ](#billing-configuration-setting) nastavení konfigurace.

> [!IMPORTANT]
> [ `ApiKey` ](#apikey-configuration-setting), [ `Billing` ](#billing-configuration-setting), A [ `Eula` ](#eula-configuration-setting) nastavení konfigurace se používají společně a je nutné zadat platné hodnoty pro všechny tři je. jinak nebude možné spustit kontejner. Další informace o používání těchto nastavení konfigurace pro vytvoření instance kontejneru najdete v tématu [fakturace](how-tos/text-analytics-how-to-install-containers.md#billing).

## <a name="applicationinsights-configuration-settings"></a>Nastavení konfigurace ApplicationInsights

Nastavení konfigurace `ApplicationInsights` části slouží k přidání [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) podporu telemetrická data do kontejneru. Služba Application Insights nabízí podrobné monitorování vašeho kontejneru na úrovni kódu. Umožňuje snadné monitorování vašeho kontejneru dostupnosti, výkonu a využití. Můžete také rychle identifikovat a diagnostikovat chyby ve vašem kontejneru bez čekání na jejich nahlášení uživatelem.

V následující tabulce jsou popsaná nastavení konfigurace podporované v rámci `ApplicationInsights` oddílu.

| Název | Typ dat | Popis |
|------|-----------|-------------|
| `InstrumentationKey` | Řetězec | Instrumentační klíč Application Insights instance, do jaké telemetrická data pro kontejner se odesílají. Další informace najdete v tématu [Application Insights pro ASP.NET Core](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-core). |

## <a name="authentication-configuration-settings"></a>Nastavení konfigurace ověřování

`Authentication` Nastavení konfigurace poskytují možnosti zabezpečení Azure pro váš kontejner. I když nastavení konfigurace v této části jsou k dispozici pro všechny kontejnery v kontejnerech pro analýzu textu, způsobem, ve kterém se používají nastavení hodnot konfigurace je specifická pro každý kontejner a kontejnery nemusí vůbec používat v této části.

V následující tabulce jsou popsaná nastavení konfigurace podporované v rámci `Authentication` oddílu.

| Název | Typ dat | Popis |
|------|-----------|-------------|
| `ApiKey` | řetězec nebo pole | Předplatné Azure klíče používané kontejnerem pro přístup k jiným prostředkům Azure, v případě potřeby kontejnerem.<br/> Pokud více než jeden klíč předplatného se používá kontejnerem, pak tato hodnota je zadána jako pole řetězců; v opačném případě hodnotu řetězce slouží k určení jednoho předplatného klíč používaný kontejnerem. |

## <a name="billing-configuration-setting"></a>Konfigurace nastavení fakturace

`Billing` Nastavení konfigurace Určuje koncový bod identifikátoru URI prostředku pro analýzu textu v Azure umožňuje měřit fakturačních údajů pro kontejner. Musíte zadat hodnotu pro toto nastavení konfigurace a hodnota musí být platný koncový bod identifikátoru URI pro prostředek pro analýzu textu v Azure.

> [!IMPORTANT]
> [ `ApiKey` ](#apikey-configuration-setting), [ `Billing` ](#billing-configuration-setting), A [ `Eula` ](#eula-configuration-setting) nastavení konfigurace se používají společně a je nutné zadat platné hodnoty pro všechny tři je. jinak nebude možné spustit kontejner. Další informace o používání těchto nastavení konfigurace pro vytvoření instance kontejneru najdete v tématu [fakturace](how-tos/text-analytics-how-to-install-containers.md#billing).

## <a name="eula-configuration-setting"></a>Nastavení konfigurace smlouvy EULA

`Eula` Nastavení konfigurace označuje, že jste přijali licenční pro kontejner. Musíte zadat hodnotu pro toto nastavení konfigurace, a hodnota musí být nastavena na `accept`.

> [!IMPORTANT]
> [ `ApiKey` ](#apikey-configuration-setting), [ `Billing` ](#billing-configuration-setting), A [ `Eula` ](#eula-configuration-setting) nastavení konfigurace se používají společně a je nutné zadat platné hodnoty pro všechny tři je. jinak nebude možné spustit kontejner. Další informace o používání těchto nastavení konfigurace pro vytvoření instance kontejneru najdete v tématu [fakturace](how-tos/text-analytics-how-to-install-containers.md#billing).

Kontejnery služby cognitive Services se licencují pod [vaší smlouvě](https://go.microsoft.com/fwlink/?linkid=2018657) řídící používání Azure. Pokud nemáte stávající smlouvy řídící používání Azure, souhlasíte s tím, že je vaší smlouvě upravující používání Azure [Microsoft Online Subscription Agreement](https://go.microsoft.com/fwlink/?linkid=2018755) (která zahrnuje [podmínky Online služeb ](https://go.microsoft.com/fwlink/?linkid=2018760)). Pro verze Preview, souhlasíte také s [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://go.microsoft.com/fwlink/?linkid=2018815). Pomocí kontejneru vyjadřujete souhlas s těmito podmínkami.

## <a name="fluentd-configuration-settings"></a>Nastavení konfigurace Fluentd

`Fluentd` Bodu spravuje konfiguraci komponent pro [Fluentd](https://www.fluentd.org), kolekce služby otevřít zdroj dat pro jednotné přihlašování. Kontejnery text Analytics zahrnuje poskytovatele Fluentd protokolování, který umožňuje kontejneru pro zápis protokolů a volitelně data metriky Fluentd server.

V následující tabulce jsou popsaná nastavení konfigurace podporované v rámci `Fluentd` oddílu.

| Název | Typ dat | Popis |
|------|-----------|-------------|
| `Host` | Řetězec | IP adresa nebo název hostitele DNS Fluentd serveru. |
| `Port` | Integer | Port serveru Fluentd.<br/> Výchozí hodnota je 24224. |
| `HeartbeatMs` | Integer | Interval prezenčního signálu v milisekundách. Pokud žádný provoz událostí byl odeslán před vypršením platnosti tento interval, na Fluentd server přijde prezenční signál. Výchozí hodnota je 60000 milisekund (1 minuta). |
| `SendBufferSize` | Integer | Vyrovnávací paměť sítě v bajtů přidělených pro operace odeslání. Výchozí hodnota je 32768 bajtů (32 kilobajtů). |
| `TlsConnectionEstablishmentTimeoutMs` | Integer | Časový limit v milisekundách pro navázání připojení SSL/TLS s Fluentd serveru. Výchozí hodnota je 10000 milisekund (10 sekund).<br/> Pokud `UseTLS` je nastavena na hodnotu false, tato hodnota se ignoruje. |
| `UseTLS` | Logická hodnota | Označuje, zda kontejner musí používat protokol SSL/TLS pro komunikaci se serverem Fluentd. Výchozí hodnota je false. |

## <a name="logging-configuration-settings"></a>Konfigurace nastavení protokolování

`Logging` Podporu protokolování ASP.NET Core pro váš kontejner spravovat nastavení konfigurace. Pro váš kontejner, který je možné pro aplikace ASP.NET Core můžete použít stejný konfigurační nastavení a hodnoty. Následující zprostředkovatele protokolování podporuje kontejnery pro analýzu textu:

* [Console](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#console-provider)  
  ASP.NET Core `Console` zprostředkovatele. Všechna nastavení konfigurace ASP.NET Core a výchozí hodnoty pro tohoto zprostředkovatele protokolování jsou podporovány.
* [Ladění](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#debug-provider)  
  ASP.NET Core `Debug` zprostředkovatele. Všechna nastavení konfigurace ASP.NET Core a výchozí hodnoty pro tohoto zprostředkovatele protokolování jsou podporovány.
* Disk  
  JSON zprostředkovatele. Tento zprostředkovatel protokolování zapíše data protokolu připojení výstupu.  
  `Disk` Protokolování zprostředkovatel podporuje následující nastavení:  

  | Název | Typ dat | Popis |
  |------|-----------|-------------|
  | `Format` | Řetězec | Výstupní formát souborů protokolu.<br/> **Poznámka:** tato hodnota musí být nastavená na `json` povolit zprostředkovatele. Pokud tato hodnota je spustit bez úkolu připojení výstupu při vytvoření instance kontejneru, dojde k chybě. |
  | `MaxFileSize` | Integer | Maximální velikost v megabajtech (MB), soubor protokolu. Když velikost aktuálního souboru protokolu splňuje nebo překročí tuto hodnotu, nový soubor protokolu je spuštěn poskytovatel protokolování. Pokud není zadána hodnota -1, velikost souboru protokolu je omezen pouze maximální velikost souboru, pokud existuje, pro výstupní připojení. Výchozí hodnota je 1. |

Další informace o konfiguraci protokolování podpora ASP.NET Core najdete v tématu [konfigurační soubor nastavení](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#settings-file-configuration).

## <a name="mounts-configuration-settings"></a>Konfigurace nastavení připojení

Kontejnery Docker poskytuje kontejnery pro analýzu textu jsou navrženy jako bezstavové a neměnné. Jinými slovy soubory vytvořené uvnitř kontejneru se ukládají ve vrstvě zapisovatelné kontejneru, který ukládá pouze, když kontejner běží a je nepřístupný snadno. Pokud tento kontejner je zastavena nebo odebrat, jsou zničeny soubory vytvořené v tomto kontejneru s ním.

Nicméně protože jsou kontejnery Dockeru, můžete použít možnosti úložiště Dockeru, jako jsou svazky a navázat připojení ke čtení a zápisu trvalých dat mimo svůj kontejner, pokud kontejner podporuje. Další informace o tom, jak určit a spravovat možnosti úložiště Dockeru najdete v tématu [spravovat data v Dockeru](https://docs.docker.com/storage/).

> [!NOTE]
> Obvykle nemusíte měnit hodnoty pro nastavení konfigurace. Místo toho použijete hodnoty zadané v nastavení konfigurace jako cíle při zadávání vstupních a výstupních připojení pro váš kontejner. Další informace o zadávání vstupních a výstupních připojí, naleznete v tématu [vstupní a výstupní připojí](#input-and-output-mounts).

V následující tabulce jsou popsaná nastavení konfigurace podporované v rámci `Mounts` oddílu.

| Název | Typ dat | Popis |
|------|-----------|-------------|
| `Input` | Řetězec | Cíl vstupní připojení. Výchozí hodnota je `/input`. |
| `Output` | Řetězec | Cíl připojení výstupu. Výchozí hodnota je `/output`. |

### <a name="input-and-output-mounts"></a>Vstupní a výstupní připojení

Ve výchozím nastavení, může podporovat všechny kontejnery *vstupní připojení*, ze kterého může kontejneru číst data a *připojení výstupu*, které můžete kontejneru zapsat data. Kontejnery nejsou vyžadovány pro podporu vstupu nebo výstupu připojení, ale a každý kontejner můžete použít vstupní i výstupní připojení pro konkrétní kontejner účely kromě možnosti přihlašování podporuje kontejnery pro analýzu textu. Následující tabulka seznamy vstup a výstup připojit podporu pro každý kontejner v kontejnerech pro analýzu textu.

| Kontejner | Vstupní připojení | Připojení výstupu |
|-----------|-------------|--------------|
|[Extrakce klíčových frází](#working-with-key-phrase-extraction) | Nepodporuje se | Nepovinné |
|[Rozpoznávání jazyka](#working-with-language-detection) | Nepodporuje se | Nepovinné |
|[Analýza subjektivního hodnocení](#working-with-sentiment-analysis) | Nepodporuje se | Nepovinné |

Můžete určit vstupní připojení nebo připojení výstup tak, že zadáte `--mount` možnost [dockeru spustit](https://docs.docker.com/engine/reference/commandline/run/) příkazu používaný k vytváření instancí kontejner z image kontejneru stažené. Ve výchozím nastavení, vstupní připojení používá `/input` jako svůj cíl a výstup připojení používá `/output` jako svůj cíl. Možnost hostitel s kontejnerem Dockeru k dispozici žádné Docker úložiště se dá nastavit v `--mount` možnost.

Například následující příkaz definuje přípojný vazby Dockeru k `D:\Output` složky na hostitelském počítači jako výstup připojení, pak vytvoří kontejner z image kontejneru analýzu subjektivního hodnocení a ukládání souborů protokolu ve formátu JSON do výstupu připojení.

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 --mount type=bind,source=D:\Output,destination=/output mcr.microsoft.com/azure-cognitive-services/sentiment Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.0 ApiKey=0123456789 Logging:Disk:Format=json
  ```
