---
title: Konfigurace kontejnerů
titlesuffix: Face - Cognitive Services - Azure
description: Nastavení konfigurace pro kontejnery.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: conceptual
ms.date: 11/14/2018
ms.author: diberry
ms.openlocfilehash: 9410d895c791cd860c53542091608263f601b225
ms.sourcegitcommit: cd0a1514bb5300d69c626ef9984049e9d62c7237
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2018
ms.locfileid: "52682482"
---
# <a name="configure-containers"></a>Konfigurace kontejnerů

Kontejner pro rozpoznávání tváře používá společnou architekturu konfigurace, takže můžete snadno konfigurovat a spravovat úložiště, protokolování a telemetrická data a nastavení zabezpečení pro vaše kontejnery.

## <a name="configuration-settings"></a>Nastavení konfigurace

Nastavení konfigurace v kontejneru pro rozpoznávání tváře, jsou hierarchická a všechny kontejnery, které používají sdílené hierarchii, podle následující strukturu nejvyšší úrovně:

* [ApiKey](#apikey-configuration-setting)
* [ApplicationInsights](#applicationinsights-configuration-settings)
* [Ověřování](#authentication-configuration-settings)
* [Fakturace](#billing-configuration-setting)
* [CloudAI](#cloudai-configuration-settings)
* [Smlouva EULA](#eula-configuration-setting)
* [Fluentd](#fluentd-configuration-settings)
* [Protokolování](#logging-configuration-settings)
* [Připojí](#mounts-configuration-settings)

Můžete použít buď [proměnné prostředí](#configuration-settings-as-environment-variables) nebo [argumenty příkazového řádku](#configuration-settings-as-command-line-arguments) ke specifikaci nastavení konfigurace, při vytváření instance kontejneru pro rozpoznávání tváře.

Hodnoty proměnných prostředí přepsat hodnoty argumentů příkazového řádku, které pak přepsat výchozí hodnoty pro image kontejneru. Jinými slovy Pokud zadáte jiné hodnoty v proměnné prostředí a argument příkazového řádku pro stejné nastavení konfigurace, jako například `Logging:Disk:LogLevel`, pak vytvoření instance kontejneru, je použita hodnota v proměnné prostředí podle instance kontejner.

### <a name="configuration-settings-as-environment-variables"></a>Nastavení konfigurace jako proměnné prostředí

Můžete použít [syntaxi proměnných prostředí ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.1&tabs=basicconfiguration#configuration-by-environment) ke specifikaci nastavení konfigurace.

Kontejner přečte uživatelských proměnných prostředí při vytváření instance kontejneru. Pokud proměnná prostředí existuje, přepíše hodnotu proměnné prostředí výchozí hodnota pro nastavení konfigurace. Výhodou použití proměnných prostředí je, že více nastavení konfigurace můžete nastavit před vytvořením instance kontejnerů a několik kontejnerů může automaticky použít stejnou sadu nastavení konfigurace.

Například následující příkazy použijte proměnnou prostředí nakonfigurovat úroveň protokolování konzoly do [LogLevel.Information](https://msdn.microsoft.com), pak vytvoří kontejner z image kontejneru pro rozpoznávání tváře. Hodnota proměnné prostředí, přepíše výchozí nastavení konfigurace.

  ```Docker
  SET Logging:Console:LogLevel=Information
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 containerpreview.azurecr.io/microsoft/cognitive-services-face Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/face/v1.0 ApiKey=0123456789
  ```

### <a name="configuration-settings-as-command-line-arguments"></a>Nastavení konfigurace jako argumenty příkazového řádku

Můžete použít [syntaxi ASP.NET Core argument příkazového řádku](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.1&tabs=basicconfiguration#arguments) ke specifikaci nastavení konfigurace.

Můžete určit nastavení konfigurace volitelné `ARGS` parametr [dockeru spustit](https://docs.docker.com/engine/reference/commandline/run/) příkazu používaný k vytváření instancí kontejner z image kontejneru stažené. Výhodou použití argumentů příkazového řádku je, že každý kontejner můžete použít různé, vlastní sadu nastavení konfigurace.

Například následující příkaz vytvoří kontejner z image kontejneru pro rozpoznávání tváře a nakonfiguruje úroveň protokolování pro LogLevel.Information přepisuje výchozí nastavení konfigurace konzoly.

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 containerpreview.azurecr.io/microsoft/cognitive-services-face Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/face/v1.0 ApiKey=0123456789 Logging:Console:LogLevel=Information
  ```

## <a name="apikey-configuration-setting"></a>Nastavení konfigurace ApiKey

`ApiKey` Nastavení konfigurace určuje konfigurační klíč prostředku pro rozpoznávání tváře na Azure, která slouží ke sledování fakturačních údajů pro kontejner. Musíte zadat hodnotu pro toto nastavení konfigurace, a hodnota musí být platný konfigurační klíč pro zadaný pro prostředek pro rozpoznávání tváře [ `Billing` ](#billing-configuration-setting) nastavení konfigurace.

> [!IMPORTANT]
> [ `ApiKey` ](#apikey-configuration-setting), [ `Billing` ](#billing-configuration-setting), A [ `Eula` ](#eula-configuration-setting) nastavení konfigurace se používají společně a je nutné zadat platné hodnoty pro všechny tři je. jinak nebude možné spustit kontejner. Další informace o používání těchto nastavení konfigurace pro vytvoření instance kontejneru najdete v tématu [fakturace](face-how-to-install-containers.md#billing).

## <a name="applicationinsights-configuration-settings"></a>Nastavení konfigurace ApplicationInsights

Nastavení konfigurace `ApplicationInsights` části slouží k přidání [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) podporu telemetrická data do kontejneru. Služba Application Insights nabízí podrobné monitorování vašeho kontejneru na úrovni kódu. Umožňuje snadné monitorování vašeho kontejneru dostupnosti, výkonu a využití. Můžete také rychle identifikovat a diagnostikovat chyby ve vašem kontejneru bez čekání na jejich nahlášení uživatelem.

V následující tabulce jsou popsaná nastavení konfigurace podporované v rámci `ApplicationInsights` oddílu.

| Název | Typ dat | Popis |
|------|-----------|-------------|
| `InstrumentationKey` | Řetězec | Instrumentační klíč Application Insights instance, do jaké telemetrická data pro kontejner se odesílají. Další informace najdete v tématu [Application Insights pro ASP.NET Core](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-core). |

## <a name="authentication-configuration-settings"></a>Nastavení konfigurace ověřování

`Authentication` Nastavení konfigurace poskytují možnosti zabezpečení Azure pro váš kontejner. I když nastavení konfigurace v této části jsou k dispozici, kontejner pro rozpoznávání tváře nepoužívá v této části.

| Název | Typ dat | Popis |
|------|-----------|-------------|
| `Storage` | Řetězec | Instrumentační klíč Application Insights instance, do jaké telemetrická data pro kontejner se odesílají. Další informace najdete v tématu [Application Insights pro ASP.NET Core](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-core). |

## <a name="billing-configuration-setting"></a>Konfigurace nastavení fakturace

`Billing` Nastavení konfigurace Určuje koncový bod identifikátoru URI prostředku pro rozpoznávání tváře na Azure používá ke sledování fakturačních údajů pro kontejner. Musíte zadat hodnotu pro toto nastavení konfigurace a hodnota musí být platný koncový bod identifikátoru URI pro prostředek pro rozpoznávání tváře na Azure.

> [!IMPORTANT]
> [ `ApiKey` ](#apikey-configuration-setting), [ `Billing` ](#billing-configuration-setting), A [ `Eula` ](#eula-configuration-setting) nastavení konfigurace se používají společně a je nutné zadat platné hodnoty pro všechny tři je. jinak nebude možné spustit kontejner. Další informace o používání těchto nastavení konfigurace pro vytvoření instance kontejneru najdete v tématu [fakturace](face-how-to-install-containers.md#billing).

## <a name="cloudai-configuration-settings"></a>Nastavení konfigurace CloudAI

Nastavení konfigurace `CloudAI` části poskytují možnosti specifické pro kontejner jedinečných do kontejneru. Jsou podporovány následující nastavení a objekty pro kontejner pro rozpoznávání tváře ve `CloudAI` oddílu

| Název | Typ dat | Popis |
|------|-----------|-------------|
| `Storage` | Objekt | Scénář úložiště používané pro rozpoznávání tváře kontejneru. Další informace o scénáře využití služby storage a související nastavení `Storage` objektu, najdete v článku [scénář nastavení úložiště](#storage-scenario-settings) |

### <a name="storage-scenario-settings"></a>Scénář nastavení úložiště

Kontejner pro rozpoznávání tváře ukládá objekt blob, mezipaměť, metadata a data fronty, podle toho, co je právě uložili. Například školení indexy a výsledky pro velké osoby skupiny se ukládají jako data objektů blob. Kontejner pro rozpoznávání tváře nabízí dva různé scénáře využití služby storage při práci s a ukládání tyto typy dat:

* Memory (Paměť)  
  Všechny čtyři typy dat jsou uložené v paměti. Nejsou distribuovány ani jsou trvalé. Pokud je zastavena nebo odebrání kontejneru pro rozpoznávání tváře je zničen všechna data v úložišti pro tento kontejner.  
  Toto je výchozí scénář úložiště pro kontejner pro rozpoznávání tváře.
* Azure  
  Kontejner pro rozpoznávání tváře využívá Azure Storage a Azure Cosmos DB k distribuci tyto čtyři typy dat napříč trvalého úložiště. Zpracování dat objektů BLOB a fronty Azure Storage. Metadata a mezipaměti dat zařizuje služba Azure Cosmos DB pomocí rozhraní MongoDB API. Pokud kontejner pro rozpoznávání tváře je zastavena nebo odebrán, všechna data v úložišti pro tento kontejner zůstane uložená v Azure Storage a Azure Cosmos DB.  
  Prostředky využívané třídou scénáře služby Azure storage mají následující další požadavky
  * Prostředek služby Azure Storage, musíte použít StorageV2 druh účtu
  * Prostředek služby Azure Cosmos DB, musíte použít rozhraní MongoDB API

Scénáře využití služby storage a související konfigurační nastavení jsou spravovány `Storage` objektu v oblasti `CloudAI` konfigurační oddíl. Jsou k dispozici v následujícím nastavením konfigurace `Storage` objektu:

| Název | Typ dat | Popis |
|------|-----------|-------------|
| `StorageScenario` | Řetězec | Scénář úložiště podporuje kontejneru. Následující hodnoty jsou k dispozici<br/>`Memory` – Výchozí hodnota. Kontejner používá dočasné nedistribuovaná a v paměti úložiště pro použití s jedním uzlem, dočasné. Pokud je zastavena nebo odebrání kontejneru úložiště pro tento kontejner je zničen.<br/>`Azure` -Kontejner využívá úložiště prostředků Azure. Pokud je zastavena nebo odebrání kontejneru úložiště pro tento kontejner se ukládají.|
| `ConnectionStringOfAzureStorage` | Řetězec | Připojovací řetězec pro prostředek služby Azure Storage používaný kontejneru.<br/>Toto nastavení platí jenom v případě `Azure` je určená pro `StorageScenario` nastavení konfigurace. |
| `ConnectionStringOfCosmosMongo` | Řetězec | Připojovacího řetězce MongoDB pro prostředek služby Azure Cosmos DB používá kontejneru.<br/>Toto nastavení platí jenom v případě `Azure` je určená pro `StorageScenario` nastavení konfigurace. |

Například následující příkaz určuje scénář úložiště Azure a poskytuje ukázkové připojovací řetězce pro prostředky Azure Storage a Cosmos DB používá k ukládání dat pro kontejner pro rozpoznávání tváře.

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 containerpreview.azurecr.io/microsoft/cognitive-services-face Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/face/v1.0 ApiKey=0123456789 CloudAI:Storage:StorageScenario=Azure CloudAI:Storage:ConnectionStringOfCosmosMongo="mongodb://samplecosmosdb:0123456789@samplecosmosdb.documents.azure.com:10255/?ssl=true&replicaSet=globaldb" CloudAI:Storage:ConnectionStringOfAzureStorage="DefaultEndpointsProtocol=https;AccountName=sampleazurestorage;AccountKey=0123456789;EndpointSuffix=core.windows.net"
  ```

Scénář úložiště je zpracovávat odděleně od připojení vstupní a výstupní připojení. Můžete zadat kombinací těchto funkcí pro jeden kontejner. Například následující příkaz definuje přípojný vazby Dockeru k `D:\Output` složky na hostitelském počítači jako výstup připojení, pak vytvoří kontejner z image kontejneru pro rozpoznávání tváře a ukládání souborů protokolu ve formátu JSON do výstupu připojení. Příkaz také určuje scénář úložiště Azure a poskytuje ukázkové připojovací řetězce pro prostředky Azure Storage a Cosmos DB používá k ukládání dat pro kontejner pro rozpoznávání tváře.

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 --mount type=bind,source=D:\Output,destination=/output containerpreview.azurecr.io/microsoft/cognitive-services-face Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/face/v1.0 ApiKey=0123456789 Logging:Disk:Format=json CloudAI:Storage:StorageScenario=Azure CloudAI:Storage:ConnectionStringOfCosmosMongo="mongodb://samplecosmosdb:0123456789@samplecosmosdb.documents.azure.com:10255/?ssl=true&replicaSet=globaldb" CloudAI:Storage:ConnectionStringOfAzureStorage="DefaultEndpointsProtocol=https;AccountName=sampleazurestorage;AccountKey=0123456789;EndpointSuffix=core.windows.net"
  ```

## <a name="eula-configuration-setting"></a>Nastavení konfigurace smlouvy EULA

`Eula` Nastavení konfigurace označuje, že jste přijali licenční pro kontejner. Musíte zadat hodnotu pro toto nastavení konfigurace, a hodnota musí být nastavena na `accept`.

> [!IMPORTANT]
> [ `ApiKey` ](#apikey-configuration-setting), [ `Billing` ](#billing-configuration-setting), A [ `Eula` ](#eula-configuration-setting) nastavení konfigurace se používají společně a je nutné zadat platné hodnoty pro všechny tři je. jinak nebude možné spustit kontejner. Další informace o používání těchto nastavení konfigurace pro vytvoření instance kontejneru najdete v tématu [fakturace](face-how-to-install-containers.md#billing).

## <a name="fluentd-configuration-settings"></a>Nastavení konfigurace Fluentd

`Fluentd` Bodu spravuje konfiguraci komponent pro [Fluentd](https://www.fluentd.org), kolekce služby otevřít zdroj dat pro jednotné přihlašování. kontejner pro rozpoznávání tváře zahrnuje poskytovatele Fluentd protokolování, který umožňuje kontejneru pro zápis protokolů a volitelně data metriky Fluentd server.

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

`Logging` Podporu protokolování ASP.NET Core pro váš kontejner spravovat nastavení konfigurace. Pro váš kontejner, který je možné pro aplikace ASP.NET Core můžete použít stejný konfigurační nastavení a hodnoty. Podporuje následující zprostředkovatele protokolování kontejneru pro rozpoznávání tváře:

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

Kontejnery Docker poskytuje pro rozpoznávání tváře jsou navrženy jako bezstavové a neměnné. Jinými slovy soubory vytvořené uvnitř kontejneru se ukládají ve vrstvě zapisovatelné kontejneru, který ukládá pouze, když kontejner běží a je nepřístupný snadno. Pokud tento kontejner je zastavena nebo odebrat, jsou zničeny soubory vytvořené v tomto kontejneru s ním.

Nicméně protože jsou kontejnery Dockeru, můžete použít možnosti úložiště Dockeru, jako jsou svazky a navázat připojení ke čtení a zápisu trvalých dat mimo svůj kontejner, pokud kontejner podporuje. Další informace o tom, jak určit a spravovat možnosti úložiště Dockeru najdete v tématu [spravovat data v Dockeru](https://docs.docker.com/storage/).

> [!NOTE]
> Obvykle nemusíte měnit hodnoty pro nastavení konfigurace. Místo toho použijete hodnoty zadané v nastavení konfigurace jako cíle při zadávání vstupních a výstupních připojení pro váš kontejner. Další informace o zadávání vstupních a výstupních připojí, naleznete v tématu [vstupní a výstupní připojí](#input-and-output-mounts).

V následující tabulce jsou popsaná nastavení konfigurace podporované v rámci `Mounts` oddílu.

| Název | Typ dat | Popis |
|------|-----------|-------------|
| `Input` | Řetězec | Cíl vstupní připojení. Výchozí hodnota je `/input`. |
| `Output` | Řetězec | Cíl připojení výstupu. Výchozí hodnota je `/output`. |

### <a name="input-and-output-mounts"></a>Vstupní a výstupní připojení

Ve výchozím nastavení, může podporovat všechny kontejnery *vstupní připojení*, ze kterého může kontejneru číst data a *připojení výstupu*, které můžete kontejneru zapsat data. Kontejnery nejsou vyžadovány pro podporu vstupu nebo výstupu připojení, ale a každý kontejner můžete použít vstupní i výstupní připojení pro konkrétní kontejner účely kromě protokolování možností podporovaných příkazem kontejneru pro rozpoznávání tváře.

Kontejner pro rozpoznávání tváře nepodporuje vstupní připojení a volitelně podporuje výstup připojení.

Můžete určit vstupní připojení nebo připojení výstup tak, že zadáte `--mount` možnost [dockeru spustit](https://docs.docker.com/engine/reference/commandline/run/) příkazu používaný k vytváření instancí kontejner z image kontejneru stažené. Ve výchozím nastavení, vstupní připojení používá `/input` jako svůj cíl a výstup připojení používá `/output` jako svůj cíl. Možnost hostitel s kontejnerem Dockeru k dispozici žádné Docker úložiště se dá nastavit v `--mount` možnost.

Například následující příkaz definuje přípojný vazby Dockeru k `D:\Output` složky na hostitelském počítači jako výstup připojení, pak vytvoří kontejner z image kontejneru pro rozpoznávání tváře a ukládání souborů protokolu ve formátu JSON do výstupu připojení.

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 --mount type=bind,source=D:\Output,destination=/output containerpreview.azurecr.io/microsoft/cognitive-services-face Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/face/v1.0 ApiKey=0123456789 Logging:Disk:Format=json
  ```

Kontejner pro rozpoznávání tváře nepoužívá vstup nebo výstup připojí k ukládání dat školení nebo databáze. Kontejner pro rozpoznávání tváře místo toho poskytuje scénáře využití služby storage pro správu dat pro trénování a databáze. Další informace o používání scénáře využití služby storage najdete v tématu [nastavení scénář úložiště](#storage-scenario-settings).
