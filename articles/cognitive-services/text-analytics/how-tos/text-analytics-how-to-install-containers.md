---
title: Instalace a spuštění kontejnerů
titleSuffix: Text Analytics -  Azure Cognitive Services
description: Postup stažení, instalaci a spouštění kontejnerů v tomto kurzu návod pro analýzu textu.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: article
ms.date: 11/14/2018
ms.author: diberry
ms.openlocfilehash: 11798c3bfd4032ad10c738032a816a2a0488ce67
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53090529"
---
# <a name="install-and-run-containers"></a>Instalace a spuštění kontejnerů

Kontejnerizace je přístup k distribuci softwaru, ve kterém je zabalená aplikace nebo služby, jako image kontejneru. Konfigurace a závislosti pro aplikaci nebo službě jsou součástí image kontejneru. Image kontejneru je potom nasadit na hostiteli s téměř nebo vůbec žádné změny. Kontejnery jsou izolované od sebe navzájem a základní operační systém s menší nároky na místo než virtuální počítač. Kontejnery můžete vytvořené z imagí kontejneru pro krátkodobé úlohy a odebrat, pokud už je nepotřebujete.

Rozhraní text Analytics poskytuje následující sadu kontejnerů Dockeru, z nichž každý obsahuje podmnožinu funkcí:

| Kontejner| Popis |
|----------|-------------|
|Extrakce klíčových frází | Extrahuje klíčových frází pro identifikaci hlavních bodů. Například pro vstupní text „The food was delicious and there were wonderful staff“ (Jídlo bylo výborné a personál byl úžasný),vrací rozhraní API hlavní body: „food“ (jídlo) a „wonderful staff“ (úžasný personál). |
|Rozpoznávání jazyka | Až 120 jazyků, zjišťuje a vykazuje v jakém jazyce je zapsána vstupního textu. Kontejner sestavy jeden jazyk kódu pro každý dokument, který je součástí požadavku. Kód jazyka spárovaný se skóre označuje sílu skóre. |
|Analýza mínění | Analyzuje nezpracovaný text pro příčiny o pozitivní nebo negativní zabarvení. Toto rozhraní API vrátí pro každý dokument skóre mínění mezi 0 a 1, přičemž 1 je mez pro nejvíce kladné hodnocení. Modely analýzu jsou předem trénuje pomocí rozsáhlé tělo technologií text a přirozeného jazyka od Microsoftu. Pro [vybrané jazyky](../language-support.md) může rozhraní API analyzovat a stanovit skóre jakéhokoliv nezpracovaného textu, který zadáte, přičemž vrátí výsledky přímo do volající aplikace. |

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="preparation"></a>Příprava

Před použitím kontejnerů pro analýzu textu, musí splňovat následující požadavky:

**Modul docker**: musíte mít lokálně nainstalovaný modul Docker. Docker nabízí balíčky, které nakonfigurují prostředí Dockeru na [macOS](https://docs.docker.com/docker-for-mac/), [Linux](https://docs.docker.com/engine/installation/#supported-platforms), a [Windows](https://docs.docker.com/docker-for-windows/). Na Windows Docker musí být nakonfigurované pro podporu kontejnerů Linuxu. Kontejnery dockeru je také možné nasadit přímo do [Azure Kubernetes Service](/azure/aks/), [Azure Container Instances](/azure/container-instances/), nebo [Kubernetes](https://kubernetes.io/) clusteru nasadí do [Azure Stack](/azure/azure-stack/). Další informace o nasazení Kubernetes pro Azure Stack najdete v tématu [nasazení Kubernetes pro Azure Stack](/azure/azure-stack/user/azure-stack-solution-template-kubernetes-deploy).

Docker je třeba nastavit umožňující kontejnery a spojte se s odesílat fakturačních dat do Azure.

**Znalost společnosti Microsoft Container Registry a Docker**: byste měli mít základní znalost konceptů Microsoft Container Registry a Docker, jako je registrů, úložiště, kontejnery a Image kontejneru, jakož i znalost základní `docker` příkazy.  

Základy Dockeru a kontejnerech základní informace o najdete v článku [přehled Dockeru](https://docs.docker.com/engine/docker-overview/).

### <a name="container-requirements-and-recommendations"></a>Požadavků na kontejner a doporučení

Následující tabulka popisuje minimální a doporučené Procesorových jader a aspoň 2.6 gigahertz (GHz) nebo rychlejší a paměti v gigabajtech (GB), přidělit pro každý kontejner pro analýzu textu.

| Kontejner | Minimální | Doporučené |
|-----------|---------|-------------|
|Extrakce klíčových frází | 1 jádro, 2 GB paměti | 1 jádro, 4 GB paměti |
|Rozpoznávání jazyka | 1 jádro, 2 GB paměti | 1 jádro, 4 GB paměti |
|Analýza mínění | 1 jádro, 2 GB paměti | 1 jádro, 4 GB paměti |

## <a name="download-container-images-from-microsoft-container-registry"></a>Stáhněte si Image kontejneru z registru kontejneru Microsoft

Ze služby Microsoft Container Registry jsou dostupné Image kontejneru pro analýzu textu. V následující tabulce jsou uvedeny úložiště nejsou k dispozici ze služby Microsoft Container Registry kontejnerů pro analýzu textu. Každé úložiště obsahuje image kontejneru, který je třeba stáhnout do místní spuštění kontejneru.

| Kontejner | Úložiště |
|-----------|------------|
|Extrakce klíčových frází | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |
|Rozpoznávání jazyka | `mcr.microsoft.com/azure-cognitive-services/language` |
|Analýza mínění | `mcr.microsoft.com/azure-cognitive-services/sentiment` |

Použití [operace docker pull](https://docs.docker.com/engine/reference/commandline/pull/) příkaz Stáhnout image kontejneru z úložiště. Například chcete-li stáhnout nejnovější image kontejneru extrakce frází klíč z úložiště, použijte následující příkaz:

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/keyphrase:latest
```

Úplný popis dostupných značek pro kontejnery pro analýzu textu naleznete v následujících kontejnerech na úložiště Docker Hub:

* [Extrakce klíčových frází](https://go.microsoft.com/fwlink/?linkid=2018757)
* [Rozpoznávání jazyka](https://go.microsoft.com/fwlink/?linkid=2018759)
* [Analýza subjektivního hodnocení](https://go.microsoft.com/fwlink/?linkid=2018654)

> [!TIP]
> Můžete použít [imagí dockeru](https://docs.docker.com/engine/reference/commandline/images/) příkazu zobrazte výpis imagí kontejnerů stažené. Například následující příkaz zobrazí seznam ID, úložiště a značka image každý stažený kontejneru, formátovaná jako tabulka:
>
>  ```Docker
>  docker images --format "table {{.ID}}\t{{.Repository}}\t{{.Tag}}"
>  ```
>

## <a name="instantiate-a-container-from-a-downloaded-container-image"></a>Vytvořit instanci kontejneru z image kontejneru stažené

Použití [dockeru spustit](https://docs.docker.com/engine/reference/commandline/run/) příkaz pro vytvoření instance kontejner z image kontejneru stažené. Například následující příkaz:

* Vytvoří kontejner z image kontejneru analýzu subjektivního hodnocení
* Přidělí jedno Procesorové jádro a 8 gigabajtů (GB) paměti
* Zpřístupňuje TCP port 5000 a přiděluje pseudo-TTY pro kontejner
* Po ukončení automaticky odstraní kontejner

```Docker
docker run --rm -it -p 5000:5000 --memory 8g --cpus 1 mcr.microsoft.com/azure-cognitive-services/sentiment Eula=accept Billing=https://westus.api.cognitive.microsoft.com/text/analytics/v2.0 ApiKey=0123456789

```

> [!IMPORTANT]
> `Eula`, `Billing`, A `ApiKey` možnosti příkazového řádku musí být zadán pro vytvoření instance kontejneru; v opačném případě nebude spuštění kontejneru.  Další informace najdete v tématu [fakturace](#billing).

Po vytvoření instance, můžete operace volat z kontejneru pomocí kontejneru hostitele identifikátoru URI. Například následující identifikátor URI hostitele představuje kontejner analýzu subjektivního hodnocení, která byla vytvořena instance v předchozím příkladu:

```http
http://localhost:5000/
```

> [!TIP]
> Můžete přistupovat [specifikace OpenAPI](https://swagger.io/docs/specification/about/) (dříve specifikace Swagger), popisující operací podporované vytvořenou instanci kontejneru z `/swagger` relativní identifikátor URI pro tento kontejner. Například následující identifikátor URI poskytuje přístup k specifikace OpenAPI pro analýzu mínění kontejneru, která byla vytvořena instance v předchozím příkladu:
>
>  ```http
>  http://localhost:5000/swagger
>  ```

Můžete buď [volání operací REST API](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-call-api) z kontejneru, nebo použijte k dispozici [Azure Cognitive Services Text Analytics SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics) klientskou knihovnu pro volání těchto operací.  
> [!IMPORTANT]
> Musíte mít Azure Cognitive Services Text Analytics SDK verze 2.1.0 nebo novější, pokud chcete použít klientskou knihovnu pro kontejner.

Jediný rozdíl mezi voláním danou operaci z vašeho kontejneru a volání, je stejné operace z odpovídající služby v Azure, použijte identifikátor URI vašeho kontejneru hostitele, nikoli identifikátor URI z oblasti Azure, hostitele k volání operace. Například Kdybyste chtěli použít instanci rozhraní Text Analytics běžící v oblasti Azure USA – Západ, by volat následující operaci rozhraní REST API:

```http
POST https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/keyPhrases
```

Pokud jste chtěli použít kontejner klíče extrakce frází spuštěna na místním počítači v její výchozí konfiguraci, by volat následující operaci rozhraní REST API:

```http
POST http://localhost:5000/text/analytics/v2.0/keyPhrases
```

### <a name="billing"></a>Fakturace

Kontejnery pro analýzu textu odeslání fakturační údaje do Azure, použijte odpovídající prostředek pro analýzu textu ve vašem účtu Azure. Kontejnery pro analýzu textu pro účely fakturace používají následující možnosti příkazového řádku:

| Možnost | Popis |
|--------|-------------|
| `ApiKey` | Klíč rozhraní API pro analýzu textu prostředku lze sledovat fakturační údaje.<br/>Hodnota této možnosti musí být nastavená na klíče rozhraní API pro zřízené Text Analytics Azure prostředek určený v `Billing`. |
| `Billing` | Koncový bod prostředků pro analýzu textu lze sledovat fakturační údaje.<br/>Hodnota této možnosti musí ke koncovému bodu identifikátor URI se zřídil prostředek Azure pro analýzu textu.|
| `Eula` | Označuje, že jste přijali licenční pro kontejner.<br/>Hodnota této možnosti musí být nastavená na `accept`. |

> [!IMPORTANT]
> Všechny tři možnosti je nutné zadat platnou hodnotou nebo kontejneru se nespustí.

Další informace o těchto možnostech najdete v tématu [konfigurace kontejnery](../text-analytics-resource-container-config.md).

## <a name="summary"></a>Souhrn

V tomto článku jste zjistili, koncepty a pracovní postup pro stažení, instalaci a používání kontejnerů pro analýzu textu. Souhrn:

* Rozhraní text Analytics poskytuje tři kontejnery Linuxu pro Docker, zapouzdření extrakce klíčových frází, rozpoznávání jazyka a analýzy subjektivního hodnocení.
* Image kontejneru se stáhnou z kontejneru registru Microsoft (MCR) v Azure.
* Spuštění imagí kontejnerů v Dockeru.
* Rozhraní REST API nebo sady SDK můžete použít k volání operací v kontejnerech rozhraní Text Analytics tak, že zadáte identifikátor URI kontejneru hostitele.
* Při vytváření instance kontejneru, je nutné zadat fakturační informace.

> [!IMPORTANT]
> Cognitive Services kontejnery nejsou licencované k používání bez připojení k Azure pro monitorování míry využívání. Zákazníci musí umožňují používání kontejnerů ke komunikaci fakturační údaje ke službě monitorování míry využití po celou dobu. Cognitive Services kontejnery Neodesílat data zákazníků (třeba image nebo text, který je analyzován) společnosti Microsoft.

## <a name="next-steps"></a>Další postup

* Kontrola [konfigurace kontejnery](../text-analytics-resource-container-config.md) nastavení konfigurace
* Kontrola [přehled rozhraní Text Analytics](../overview.md) získat další informace o detekce klíčových frází, rozpoznávání jazyka a analýzy mínění  
* Odkazovat [rozhraní Text Analytics API](//westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6) podrobné informace o metodách podporuje kontejneru.
* Odkazovat na [– nejčastější dotazy (FAQ)](../text-analytics-resource-faq.md) k vyřešení problémů týkajících se funkce pro počítačové zpracování obrazu.