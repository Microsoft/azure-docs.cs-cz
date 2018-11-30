---
title: Instalace a spouštění kontejnerů
titlesuffix: Face - Cognitive Services - Azure
description: Jak si stáhnout, nainstalovat a spouštění kontejnerů v tomto kurzu návod pro rozpoznávání tváře.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: article
ms.date: 11/14/2018
ms.author: diberry
ms.openlocfilehash: 1d13e2ccbbc1d5c1bc80dffc260a3759fe378d7d
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2018
ms.locfileid: "52634536"
---
# <a name="install-and-run-containers"></a>Instalace a spuštění kontejnerů

Kontejnerizace je přístup k distribuci softwaru, ve kterém je zabalená aplikace nebo služby, jako image kontejneru. Konfigurace a závislosti pro aplikaci nebo službě jsou součástí image kontejneru. Image kontejneru je potom nasadit na hostiteli s téměř nebo vůbec žádné změny. Kontejnery jsou izolované od sebe navzájem a základní operační systém s menší nároky na místo než virtuální počítač. Kontejnery můžete vytvořené z imagí kontejneru pro krátkodobé úlohy a odebrat, pokud už je nepotřebujete.

Rozpoznávání tváře poskytuje standardizované kontejneru Linuxu pro Docker, s názvem pro rozpoznávání tváře, detekuje lidské tváře v obrázcích, který určuje atributy, včetně orientačních bodů pro rozpoznávání tváře (například ústa a oči), pohlaví, věk a další funkce rozpoznávání obličeje předpovědět počítače. Kromě zjišťování můžete pro rozpoznávání tváře zkontrolujte, jestli dvě tváře na stejnou bitovou kopii nebo jinou Image jsou stejné s použitím skóre spolehlivosti nebo porovnání proti databázi a zjistěte, jestli podobně vypadajících tváří, nebo identické pro rozpoznávání tváře již existuje. Můžete také uspořádat podobných tváří do skupin pomocí sdílené visual vlastností.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="preparation"></a>Příprava

Před použitím kontejnerů pro rozpoznávání tváře, musí splňovat následující požadavky:

**Modul docker**: musíte mít lokálně nainstalovaný modul Docker. Docker nabízí balíčky, které nakonfigurují prostředí Dockeru na [macOS](https://docs.docker.com/docker-for-mac/), [Linux](https://docs.docker.com/engine/installation/#supported-platforms), a [Windows](https://docs.docker.com/docker-for-windows/). Na Windows Docker musí být nakonfigurované pro podporu kontejnerů Linuxu. Kontejnery dockeru je také možné nasadit přímo do [Azure Kubernetes Service](/azure/aks/), [Azure Container Instances](/azure/container-instances/), nebo [Kubernetes](https://kubernetes.io/) clusteru nasadí do [Azure Stack](/azure/azure-stack/). Další informace o nasazení Kubernetes pro Azure Stack najdete v tématu [nasazení Kubernetes pro Azure Stack](/azure/azure-stack/user/azure-stack-solution-template-kubernetes-deploy).

Docker je třeba nastavit umožňující kontejnery a spojte se s odesílat fakturačních dat do Azure.

**Znalost společnosti Microsoft Container Registry a Docker**: byste měli mít základní znalost konceptů Microsoft Container Registry a Docker, jako je registrů, úložiště, kontejnery a Image kontejneru, jakož i znalost základní `docker` příkazy.  

Základy Dockeru a kontejnerech základní informace o najdete v článku [přehled Dockeru](https://docs.docker.com/engine/docker-overview/).

### <a name="server-requirements-and-recommendations"></a>Server – požadavky a doporučení

Kontejner pro rozpoznávání tváře vyžaduje minimálně 1 jádra procesoru, alespoň 2.6 gigahertz (GHz) nebo rychlejší a 4 gigabajty (GB) přidělené paměti, ale My doporučujeme aspoň 2 Procesorových jader a 6 GB přidělené paměti.

## <a name="request-access-to-the-private-container-registry"></a>Požádat o přístup k registru kontejneru soukromého

Nejprve musíte vyplňte a odešlete [formulář žádosti o kontejnery pro zpracování obrazu Cognitive Services](https://aka.ms/VisionContainersPreview) chcete požádat o přístup ke kontejneru pro rozpoznávání tváře. Formulář požádá o informace o vás, vaše společnost a uživatelský scénář, který budete používat kontejneru. Po odeslání týmu Azure Cognitive Services zkontroluje formulář pro ověření, že splňujete kritéria pro přístup k registru kontejneru soukromého.

> [!IMPORTANT]
> Musíte použít e-mailovou adresu, přidružené k účtu Microsoft (MSA) nebo Azure Active Directory (Azure AD) účtu ve formuláři.

Pokud vaše žádost se schválí, pak dostanete e-mail s pokyny popisují, jak získat vaše přihlašovací údaje a přístup privátním registru kontejneru.

## <a name="create-a-face-resource-on-azure"></a>Vytvoření prostředku pro rozpoznávání tváře na Azure

Pokud chcete použít pro rozpoznávání tváře kontejner, musíte vytvořit prostředek pro rozpoznávání tváře na Azure. Po vytvoření prostředku, pak použijte adresu URL předplatné key a koncového bodu z prostředku nástroje pro vytvoření instance kontejneru. Další informace o vytvoření instance kontejneru najdete v tématu [vytvořit instanci kontejneru z image kontejneru stažené](#instantiate-a-container-from-a-downloaded-container-image).

Proveďte následující kroky k vytvoření a načtení informací z prostředku pro rozpoznávání tváře:

1. Vytvoření prostředku pro rozpoznávání tváře na webu Azure Portal.  
   Pokud chcete použít pro rozpoznávání tváře kontejner, musíte nejprve vytvořit odpovídající prostředek pro rozpoznávání tváře na webu Azure Portal. Další informace najdete v tématu [rychlý start: vytvoření účtu služeb Cognitive Services na webu Azure Portal](../cognitive-services-apis-create-account.md).

1. Získáte klíče koncového bodu adresy URL a předplatného pro prostředky Azure.  
   Po vytvoření prostředku Azure musíte použít koncový bod adresy URL a klíč předplatného z tohoto prostředku pro vytvoření instance kontejneru odpovídající pro rozpoznávání tváře. Koncový bod adresy URL a klíč předplatného můžete zkopírovat z prostředku pro rozpoznávání tváře na portálu Azure portal, rychlý Start a klíče stránky.

## <a name="log-in-to-the-private-container-registry"></a>Přihlásit do soukromého registru kontejnerů

Existuje několik způsobů, jak ověřování pomocí kontejneru soukromého registru kontejnerů Cognitive Services, ale je doporučená metoda z příkazového řádku s použitím [rozhraní příkazového řádku Dockeru](https://docs.docker.com/engine/reference/commandline/cli/).

Použití [docker login](https://docs.docker.com/engine/reference/commandline/login/) příkaz, jak je znázorněno v následujícím příkladu, pro přihlášení k `containerpreview.azurecr.io`, privátní registr kontejnerů Cognitive Services. Nahraďte *\<uživatelské jméno\>* s uživatelským jménem a *\<heslo\>* s heslo zadané přihlašovací údaje, který jste dostali od Azure Týmu služeb cognitive Services.

```docker
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Pokud máte zabezpečený svoje přihlašovací údaje do textového souboru, lze zřetězit obsah textu soubor, pomocí `cat` příkaz, `docker login` příkaz, jak je znázorněno v následujícím příkladu. Nahraďte *\<passwordFile\>* cestou a názvem textového souboru, který obsahuje heslo a *\<uživatelské jméno\>* s uživatelským jménem zadat svoje přihlašovací údaje.

```docker
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```

## <a name="download-container-images-from-the-private-container-registry"></a>Stáhněte si Image kontejneru z registru kontejneru soukromého

Image kontejneru pro kontejner pro rozpoznávání tváře je k dispozici z privátního registru kontejnerů Dockeru s názvem `containerpreview.azurecr.io`, ve službě Azure Container Registry. Image kontejneru pro rozpoznávání tváře kontejner musí stáhnout z úložiště pro místní spuštění kontejneru.

Použití [operace docker pull](https://docs.docker.com/engine/reference/commandline/pull/) příkaz Stáhnout image kontejneru z úložiště. Například chcete-li stáhnout nejnovější image kontejneru pro rozpoznávání tváře z úložiště, použijte následující příkaz:

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-face:latest
```

Úplný popis dostupných značek pro rozpoznávání tváře kontejneru najdete v tématu [rozpoznat Text](https://go.microsoft.com/fwlink/?linkid=2018655) na Docker Hubu.

> [!TIP]
> Můžete použít [imagí dockeru](https://docs.docker.com/engine/reference/commandline/images/) příkazu zobrazte výpis imagí kontejnerů stažené. Například následující příkaz zobrazí seznam ID, úložiště a značka image každý stažený kontejneru, formátovaná jako tabulka:
>
>  ```Docker
>  docker images --format "table {{.ID}}\t{{.Repository}}\t{{.Tag}}"
>  ```
>

## <a name="instantiate-a-container-from-a-downloaded-container-image"></a>Vytvořit instanci kontejneru z image kontejneru stažené

Použití [dockeru spustit](https://docs.docker.com/engine/reference/commandline/run/) příkaz pro vytvoření instance kontejner z image kontejneru stažené. Například následující příkaz:

* Vytvoří kontejner z image kontejneru pro rozpoznávání tváře
* Přidělí dvě jader procesoru a paměti 6 gigabajtů (GB)
* Zpřístupňuje TCP port 5000 a přiděluje pseudo-TTY pro kontejner
* Po ukončení automaticky odstraní kontejner

```Docker
docker run --rm -it -p 5000:5000 --memory 6g --cpus 2 containerpreview.azurecr.io/microsoft/cognitive-services-face Eula=accept Billing=https://westus.api.cognitive.microsoft.com/face/v1.0 ApiKey=0123456789
```

> [!IMPORTANT]
> `Eula`, `Billing`, A `ApiKey` musí být zadané možnosti pro vytvoření instance kontejneru; v opačném případě nebude spuštění kontejneru.  Další informace najdete v tématu [fakturace](#billing).

Po vytvoření instance, můžete operace volat z kontejneru pomocí kontejneru hostitele identifikátoru URI. Například následující identifikátor URI hostitele představuje kontejner pro rozpoznávání tváře, která byla vytvořena instance v předchozím příkladu:

```http
http://localhost:5000/
```

> [!TIP]
> Můžete přistupovat [specifikace OpenAPI](https://swagger.io/docs/specification/about/) (dříve specifikace Swagger), popisující operací podporované vytvořenou instanci kontejneru z `/swagger` relativní identifikátor URI pro tento kontejner. Například následující identifikátor URI poskytuje přístup ke specifikaci OpenAPI pro rozpoznávání tváře kontejneru, která byla vytvořena instance v předchozím příkladu:
>
>  ```http
>  http://localhost:5000/swagger
>  ```

Můžete buď [volání operací REST API](https://docs.microsoft.com/azure/cognitive-services/face/face-api-how-to-topics/howtodetectfacesinimage) z kontejneru, nebo použijte k dispozici [Azure Cognitive Services Face Klientská knihovna](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/) klientskou knihovnu pro volání těchto operací.  
> [!IMPORTANT]
> Pokud chcete použít klientskou knihovnu pro kontejner, musíte mít Cognitive Services Face klientské knihovny Azure verze 2.0 nebo novější.

Jediný rozdíl mezi voláním danou operaci z vašeho kontejneru a volání, je stejné operace z odpovídající služby v Azure, použijte identifikátor URI vašeho kontejneru hostitele, nikoli identifikátor URI z oblasti Azure, hostitele k volání operace. Například Kdybyste chtěli použít pro rozpoznávání tváře instanci běžící v oblasti Azure USA – západ k rozpoznávání tváří, by volat následující operaci rozhraní REST API:

```http
POST https://westus.api.cognitive.microsoft.com/face/v1.0/detect
```

Pokud jste chtěli použít kontejner pro rozpoznávání tváře se spuštěnou v místním počítači v její výchozí konfiguraci pro rozpoznávání tváří, by volat následující operaci rozhraní REST API:

```http
POST http://localhost:5000/face/v1.0/detect
```

### <a name="billing"></a>Fakturace

Kontejner pro rozpoznávání tváře odešle informace o fakturaci Azure, pomocí odpovídající prostředek pro rozpoznávání tváře na účtu Azure. Následující možnosti příkazového řádku se používají pro rozpoznávání tváře kontejnerem pro účely fakturace:

| Možnost | Popis |
|--------|-------------|
| `ApiKey` | Klíč rozhraní API pro rozpoznávání tváře prostředku lze sledovat fakturační údaje.<br/>Hodnota této možnosti musí být nastavená na klíče rozhraní API pro zřízené Azure pro rozpoznávání tváře prostředek určený v `Billing`. |
| `Billing` | Koncový bod pro rozpoznávání tváře prostředek, který používá ke sledování fakturační údaje.<br/>Hodnota této možnosti musí identifikátor URI se zřídil prostředek Azure pro rozpoznávání tváře na koncový bod.|
| `Eula` | Označuje, že jste přijali licenční pro kontejner.<br/>Hodnota této možnosti musí být nastavená na `accept`. |

> [!IMPORTANT]
> Všechny tři možnosti je nutné zadat platnou hodnotou nebo kontejneru se nespustí.

Další informace o těchto možnostech najdete v tématu [konfigurace kontejnery](face-resource-container-config.md).

## <a name="summary"></a>Souhrn

V tomto článku jste zjistili, koncepty a pracovní postup pro stažení, instalaci a používání kontejnerů pro rozpoznávání tváře. Souhrn:

* Rozpoznávání tváře poskytuje jeden kontejner Linuxu pro Docker, s názvem pro rozpoznávání tváře, rozpoznávání tváří nebo identifikace tváře lidí databáze.
* Image kontejneru se stahují ze soukromého registru kontejnerů v Azure.
* Spuštění imagí kontejnerů v Dockeru.
* Rozhraní REST API nebo sady SDK můžete použít k volání operací v kontejnerech pro rozpoznávání tváře tak, že zadáte identifikátor URI kontejneru hostitele.
* Při vytváření instance kontejneru, je nutné zadat fakturační informace.
* ** Cognitive Services kontejnery nejsou licencované k používání bez připojení k Azure pro monitorování míry využívání. Zákazníci musí umožňují používání kontejnerů ke komunikaci fakturační údaje ke službě monitorování míry využití po celou dobu. Cognitive Services kontejnery Neodesílat data zákazníků (třeba image nebo text, který je analyzován) společnosti Microsoft.  

## <a name="next-steps"></a>Další postup

* Kontrola [konfigurace kontejnery](face-resource-container-config.md) nastavení konfigurace
* Kontrola [přehled pro rozpoznávání tváře](Overview.md) získat další informace o zjištění a identifikaci tváře  
* Odkazovat [API pro rozpoznávání tváře](//westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) podrobné informace o metodách podporuje kontejneru.
* Odkazovat na [– nejčastější dotazy (FAQ)](FAQ.md) k vyřešení problémů týkajících se funkce rozpoznávání tváře.
