---
title: Instalace a spouštění kontejnerů
titlesuffix: Computer Vision - Cognitive Services - Azure
description: Jak si stáhnout, nainstalovat a spouštění kontejnerů v tomto kurzu návod pro počítačové zpracování obrazu.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: article
ms.date: 11/14/2018
ms.author: diberry
ms.openlocfilehash: 67dbf3bdf6631785fc876283847e36349e857a77
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2018
ms.locfileid: "52634638"
---
# <a name="install-and-run-containers"></a>Instalace a spuštění kontejnerů

Kontejnerizace je přístup k distribuci softwaru, ve kterém je zabalená aplikace nebo služby, jako image kontejneru. Konfigurace a závislosti pro aplikaci nebo službě jsou součástí image kontejneru. Image kontejneru je potom nasadit na hostiteli s téměř nebo vůbec žádné změny. Kontejnery jsou izolované od sebe navzájem a základní operační systém s menší nároky na místo než virtuální počítač. Kontejnery můžete vytvořené z imagí kontejneru pro krátkodobé úlohy a odebrat, pokud už je nepotřebujete.

Rozpoznávání textu část pro počítačové zpracování obrazu je také k dispozici jako kontejner Dockeru. Umožňuje detekovat a extrahovat tištěný text z obrázků s různými povrchy a pozadími, jako je potvrzení a plakáty nebo vizitky různé objekty.  
> [!IMPORTANT]
> Kontejner rozpoznat Text v současné době používá pouze angličtinu.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="preparation"></a>Příprava

Před použitím rozpoznat Text kontejner musí splňovat následující požadavky:

**Modul docker**: musíte mít lokálně nainstalovaný modul Docker. Docker nabízí balíčky, které nakonfigurují prostředí Dockeru na [macOS](https://docs.docker.com/docker-for-mac/), [Linux](https://docs.docker.com/engine/installation/#supported-platforms), a [Windows](https://docs.docker.com/docker-for-windows/). Na Windows Docker musí být nakonfigurované pro podporu kontejnerů Linuxu. Kontejnery dockeru je také možné nasadit přímo do [Azure Kubernetes Service](/azure/aks/), [Azure Container Instances](/azure/container-instances/), nebo [Kubernetes](https://kubernetes.io/) clusteru nasadí do [Azure Stack](/azure/azure-stack/). Další informace o nasazení Kubernetes pro Azure Stack najdete v tématu [nasazení Kubernetes pro Azure Stack](/azure/azure-stack/user/azure-stack-solution-template-kubernetes-deploy).

Docker je třeba nastavit umožňující kontejnery a spojte se s odesílat fakturačních dat do Azure.

**Znalost společnosti Microsoft Container Registry a Docker**: byste měli mít základní znalost konceptů Microsoft Container Registry a Docker, jako je registrů, úložiště, kontejnery a Image kontejneru, jakož i znalost základní `docker` příkazy.  

Základy Dockeru a kontejnerech základní informace o najdete v článku [přehled Dockeru](https://docs.docker.com/engine/docker-overview/).

### <a name="server-requirements-and-recommendations"></a>Server – požadavky a doporučení

Zásobník textu rozpoznat vyžaduje minimálně 1 jádra procesoru, alespoň 2.6 gigahertz (GHz) nebo rychlejší a 8 gigabajtů (GB) přidělené paměti, ale My doporučujeme aspoň 2 jádra procesoru a 8 GB přidělené paměti.

## <a name="request-access-to-the-private-container-registry"></a>Požádat o přístup k registru kontejneru soukromého

Nejprve musíte vyplňte a odešlete [formulář žádosti o kontejnery pro zpracování obrazu Cognitive Services](https://aka.ms/VisionContainersPreview) chcete požádat o přístup ke kontejneru rozpoznat Text. Formulář požádá o informace o vás, vaše společnost a uživatelský scénář, který budete používat kontejneru. Po odeslání týmu Azure Cognitive Services zkontroluje formulář pro ověření, že splňujete kritéria pro přístup k registru kontejneru soukromého.

> [!IMPORTANT]
> Musíte použít e-mailovou adresu, přidružené k účtu Microsoft (MSA) nebo Azure Active Directory (Azure AD) účtu ve formuláři.

Pokud vaše žádost se schválí, pak dostanete e-mail s pokyny popisují, jak získat vaše přihlašovací údaje a přístup privátním registru kontejneru.

## <a name="create-a-computer-vision-resource-on-azure"></a>Vytvoření prostředku pro počítačové zpracování obrazu v Azure

Pokud chcete použít kontejner rozpoznat Text, musíte vytvořit prostředek pro počítačové zpracování obrazu v Azure. Po vytvoření prostředku, pak použijte adresu URL předplatné key a koncového bodu z prostředku nástroje pro vytvoření instance kontejneru. Další informace o vytvoření instance kontejneru najdete v tématu [vytvořit instanci kontejneru z image kontejneru stažené](#instantiate-a-container-from-a-downloaded-container-image).

Proveďte následující kroky k vytvoření a načtení informací z prostředku Azure:

1. Vytvoření prostředku Azure na webu Azure Portal.  
   Pokud chcete použít kontejner rozpoznat Text, musíte nejprve vytvořit odpovídající prostředek pro počítačové zpracování obrazu na webu Azure Portal. Další informace najdete v tématu [rychlý start: vytvoření účtu služeb Cognitive Services na webu Azure Portal](../cognitive-services-apis-create-account.md).

1. Získáte klíče koncového bodu adresy URL a předplatného pro prostředky Azure.  
   Po vytvoření prostředku Azure musíte použít koncový bod adresy URL a klíč předplatného z tohoto prostředku pro vytvoření instance kontejneru odpovídající rozpoznat Text. Koncový bod adresy URL a klíč předplatného můžete zkopírovat z v uvedeném pořadí, rychlý Start a klíče stránky pro počítačové zpracování obrazu prostředků na portálu Azure portal.

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

Image kontejneru pro kontejner rozpoznat Text je k dispozici z privátního registru kontejnerů Dockeru s názvem `containerpreview.azurecr.io`, ve službě Azure Container Registry. Image kontejneru pro kontejner rozpoznat Text musí stáhnout z úložiště pro místní spuštění kontejneru.

Použití [operace docker pull](https://docs.docker.com/engine/reference/commandline/pull/) příkaz Stáhnout image kontejneru z úložiště. Například chcete-li stáhnout nejnovější image kontejneru rozpoznat Text z úložiště, použijte následující příkaz:

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest
```

Úplný popis dostupných značek pro kontejner rozpoznání textu, naleznete v tématu [rozpoznat Text](https://go.microsoft.com/fwlink/?linkid=2018655) na Docker Hubu.

> [!TIP]
> Můžete použít [imagí dockeru](https://docs.docker.com/engine/reference/commandline/images/) příkazu zobrazte výpis imagí kontejnerů stažené. Například následující příkaz zobrazí seznam ID, úložiště a značka image každý stažený kontejneru, formátovaná jako tabulka:
>
>  ```Docker
>  docker images --format "table {{.ID}}\t{{.Repository}}\t{{.Tag}}"
>  ```
>

## <a name="instantiate-a-container-from-a-downloaded-container-image"></a>Vytvořit instanci kontejneru z image kontejneru stažené

Použití [dockeru spustit](https://docs.docker.com/engine/reference/commandline/run/) příkaz pro vytvoření instance kontejner z image kontejneru stažené. Například následující příkaz:

* Vytvoří kontejner z image kontejneru rozpoznat Text
* Přidělí dvě jádra procesoru a 8 gigabajtů (GB) paměti
* Zpřístupňuje TCP port 5000 a přiděluje pseudo-TTY pro kontejner
* Po ukončení automaticky odstraní kontejner

```docker
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text Eula=accept Billing=https://westus.api.cognitive.microsoft.com/vision/v2.0 ApiKey=0123456789
```

Po vytvoření instance, můžete operace volat z kontejneru pomocí kontejneru hostitele identifikátoru URI. Například následující identifikátor URI hostitele představuje kontejner rozpoznat Text, který byla vytvořena instance v předchozím příkladu:

```http
http://localhost:5000/
```

> [!IMPORTANT]
> Můžete přistupovat [specifikace OpenAPI](https://swagger.io/docs/specification/about/) (dříve specifikace Swagger), popisující operací podporované vytvořenou instanci kontejneru z `/swagger` relativní identifikátor URI pro tento kontejner. Například následující identifikátor URI poskytuje přístup k specifikace OpenAPI pro kontejner rozpoznat Text, který byla vytvořena instance v předchozím příkladu:
>
>  ```http
>  http://localhost:5000/swagger
>  ```

Můžete buď [volání operací REST API](https://docs.microsoft.com/azure/cognitive-services/computer-vision/vision-api-how-to-topics/howtocallvisionapi) dostupný z vašeho kontejneru pro synchronní nebo asynchronní rozpoznávání textu, nebo použijte [Azure Cognitive Services počítače Vision SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision) klienta Knihovna volání těchto operací.  
> [!IMPORTANT]
> Musíte mít Azure Cognitive Services počítače Vision SDK verze 3.2.0 nebo novější, pokud chcete použít klientskou knihovnu pro kontejner.

### <a name="asynchronous-text-recognition"></a>Rozpoznávání asynchronní textu

Můžete použít `POST /vision/v2.0/recognizeText` a `GET /vision/v2.0/textOperations/*{id}*` operací ve vzájemné součinnosti asynchronně rozpoznat tištěný text v obrázku, podobně jako na používání těchto odpovídající operace REST služby pro počítačové zpracování obrazu. Zásobník rozpoznání textu rozpozná pouze tištěný text, ne rukou psaný text v současné době proto `mode` parametr obvykle zadaný pro operaci služby pro počítačové zpracování obrazu je ignorován v kontejneru rozpoznat Text.

### <a name="synchronous-text-recognition"></a>Rozpoznávání synchronní textu

Můžete použít `POST /vision/v2.0/recognizeTextDirect` operace synchronně rozpoznat tištěný text v obrázku. Protože tato operace je synchronní, text požadavku pro tuto operaci je stejný jako u `POST /vision/v2.0/recognizeText` operace, ale odpověď body pro tuto operaci je stejný jako vrácené `GET /vision/v2.0/textOperations/*{id}*` operace.

### <a name="billing"></a>Fakturace

Zásobník textu rozpoznat odesílá informace o fakturaci do Azure, pomocí odpovídající prostředek pro počítačové zpracování obrazu v účtu Azure. Kontejner rozpoznat Text pro účely fakturace používají následující možnosti:

| Možnost | Popis |
|--------|-------------|
| `ApiKey` | Klíč rozhraní API pro počítačové zpracování obrazu prostředku lze sledovat fakturační údaje.<br/>Hodnota této možnosti musí být nastavená na klíče rozhraní API pro zřízené počítače vizi Azure prostředek určený v `Billing`. |
| `Billing` | Koncový bod pro počítačové zpracování obrazu prostředek, který používá ke sledování fakturační údaje.<br/>Hodnota této možnosti musí nastavena na identifikátor URI zřízené počítače vizi Azure prostředku koncového bodu.|
| `Eula` | Označuje, že jste přijali licenční pro kontejner.<br/>Hodnota této možnosti musí být nastavená na `accept`. |

> [!IMPORTANT]
> Všechny tři možnosti je nutné zadat platnou hodnotou nebo kontejneru se nespustí.

Další informace o těchto možnostech najdete v tématu [konfigurace kontejnery](computer-vision-resource-container-config.md).

## <a name="summary"></a>Souhrn

V tomto článku jste zjistili, koncepty a pracovní postup pro stažení, instalaci a používání kontejnerů pro počítačové zpracování obrazu. Souhrn:

* Počítačové zpracování obrazu poskytuje kontejnery Linuxu pro Dockeru detekovat a extrahovat tištěný text.
* Image kontejneru se stahují ze soukromého registru kontejnerů v Azure.
* Spuštění imagí kontejnerů v Dockeru.
* Rozhraní REST API nebo sady SDK můžete použít k volání operací v kontejnerech pro počítačové zpracování obrazu tak, že zadáte identifikátor URI kontejneru hostitele.
* Při vytváření instance kontejneru, je nutné zadat fakturační informace.
* ** Cognitive Services kontejnery nejsou licencované k používání bez připojení k Azure pro monitorování míry využívání. Zákazníci musí umožňují používání kontejnerů ke komunikaci fakturační údaje ke službě monitorování míry využití po celou dobu. Cognitive Services kontejnery Neodesílat data zákazníků (třeba image nebo text, který je analyzován) společnosti Microsoft.  

## <a name="next-steps"></a>Další postup

* Kontrola [konfigurace kontejnery](computer-vision-resource-container-config.md) nastavení konfigurace
* Kontrola [přehled pro počítačové zpracování obrazu](Home.md) Další informace o rozpoznávání tištěné a rukou psaný text  
* Odkazovat [rozhraní API pro počítačové zpracování obrazu](//westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) podrobné informace o metodách podporuje kontejneru.
* Odkazovat na [– nejčastější dotazy (FAQ)](FAQ.md) k vyřešení problémů týkajících se funkce pro počítačové zpracování obrazu.
