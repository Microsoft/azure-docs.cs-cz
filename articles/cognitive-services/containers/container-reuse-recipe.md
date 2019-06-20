---
title: Recepty pro kontejnery Dockeru
titleSuffix: Azure Cognitive Services
description: Pomocí těchto návodů kontejneru pro vytvoření Cognitive Services kontejnerů, které je možné využít znovu. Kontejnery se dají vytvářet pomocí některé nebo všechny konfigurace nastavení tak, že podle potřeby při spuštění kontejneru. Jakmile máte tato nová vrstva kontejneru (nastavení) a jste ho místně otestovali, můžete uložit kontejneru v registru kontejneru. Při spuštění kontejneru potřebovat jenom ta nastavení, která nejsou aktuálně ukládat do kontejneru.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: dapine
ms.openlocfilehash: 8a9d0d9f35c30636f4b9150c63b64098071e12e9
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2019
ms.locfileid: "67271489"
---
# <a name="create-containers-for-reuse"></a>Vytvoření kontejnerů pro opakované použití

Pomocí těchto návodů kontejneru pro vytvoření Cognitive Services kontejnerů, které je možné využít znovu. Kontejnery se dají vytvářet pomocí některé nebo všechny konfigurace nastavení tak, aby byly _není_ potřebné při spuštění kontejneru.

Jakmile máte tato nová vrstva kontejneru (nastavení) a jste ho místně otestovali, můžete uložit kontejneru v registru kontejneru. Při spuštění kontejneru potřebovat jenom ta nastavení, která nejsou aktuálně ukládat do kontejneru. Privátní registr kontejneru místo konfigurace k předání těchto nastavení v.

## <a name="docker-run-syntax"></a>Docker, spusťte syntaxe

Žádné `docker run` příklady v tomto dokumentu předpokládají konzola Windows s `^` řádek znak pro pokračování. Vezměte v úvahu následující pro vlastní použití:

* Pořadí argumentů nezmění, pokud máte velmi zkušenosti s kontejnery dockeru.
* Pokud používáte operační systém než Windows nebo konzoly než konzoly Windows, použijte pro připojení a znak pro pokračování řádku konzoly a systém správné konzole a Terminálové, syntaxe složky.  Protože kontejner služeb Cognitive Services je operační systém Linux, cíl připojení používá syntaxi složky Linux-style.
* `docker run` Příklady použití adresáře vypnout `c:` disku, aby se zabránilo konfliktům oprávnění na Windows. Pokud je potřeba použít konkrétní adresář jako vstupní adresář, budete muset udělit dockeru služby oprávnění.

## <a name="store-no-configuration-settings-in-image"></a>Store žádné nastavení konfigurace v bitové kopii

V příkladu `docker run` příkazy pro každou službu neukládejte nastavení konfigurace v kontejneru. Při spuštění kontejneru z konzoly nebo registru služby, je potřeba předat v těchto nastavení konfigurace. Privátní registr kontejneru místo konfigurace k předání těchto nastavení v.

## <a name="reuse-recipe-store-all-configuration-settings-with-container"></a>Znovu použít předpisu: uložit všechna nastavení konfigurace s kontejnerem

Aby bylo možné uložit všechna nastavení konfigurace, vytvoření `Dockerfile` s těmito nastaveními.

Problémy s tímto přístupem:

* Nový kontejner má odlišný název a značka původní kontejneru.
* Chcete-li změnit tato nastavení, budete muset změnit hodnoty souboru Dockerfile, opětovné sestavení image a znovu publikovat do svého registru.
* Pokud někdo získá přístup k registru kontejneru nebo místního hostitele, že spuštění kontejneru a využívají koncové body služeb Cognitive Services.
* Pokud vstupní připojení nevyžaduje, aby vaše služby Cognitive Services, nepřidávejte `COPY` řádků na vašem souboru Dockerfile.

Vytvoření souboru Dockerfile a přebírání z existující kontejner služeb Cognitive Services, kterou chcete použít, a pak pomocí příkazy dockeru v souboru Dockerfile nastavit nebo získat informace kontejner potřebuje.

V tomto příkladu:

* Nastaví fakturační koncový bod, `{BILLING_ENDPOINT}` z hostitele prostředí klíč v nástrojích `ENV`.
* Nastaví fakturační API-key, `{ENDPOINT_KEY}` z hostitele v klíči prostředí pomocí ' ENV.

### <a name="reuse-recipe-store-billing-settings-with-container"></a>Znovu použít předpisu: ukládání fakturační nastavení s kontejnerem

Tento příklad ukazuje, jak vytvořit kontejner mínění rozhraní Text Analytics ze souboru Dockerfile.

```Dockerfile
FROM mcr.microsoft.com/azure-cognitive-services/sentiment:latest
ENV billing={BILLING_ENDPOINT}
ENV apikey={ENDPOINT_KEY}
ENV EULA=accept
```

Sestavte a spusťte kontejner [místně](#how-to-use-container-on-your-local-host) nebo z vaší [privátního registru kontejneru](#how-to-add-container-to-private-registry) podle potřeby.

### <a name="reuse-recipe-store-billing-and-mount-settings-with-container"></a>Znovu použít předpisu: ukládání fakturace a nastavení s kontejnerem připojování

Tento příklad ukazuje způsob použití Language Understanding, ukládání a modely fakturace ze souboru Dockerfile.

* Zkopírování souboru modelu Language Understanding (LUIS) z hostitele souboru systému pomocí `COPY`.
* Služba LUIS kontejner podporuje více než jeden model. Pokud všechny modely jsou uloženy ve stejné složce, všechny budete potřebovat jeden `COPY` příkazu.
* Spusťte soubor docker ze relativní nadřazené do vstupního adresáře modelu. Následující příklad, spusťte `docker build` a `docker run` příkazy z relativní nadřazeného `/input`. První `/input` na `COPY` příkaz je hostitelském počítači adresář. Druhá `/input` je adresář kontejneru.

```Dockerfile
FROM <container-registry>/<cognitive-service-container-name>:<tag>
ENV billing={BILLING_ENDPOINT}
ENV apikey={ENDPOINT_KEY}
ENV EULA=accept
COPY /input /input
```

Sestavte a spusťte kontejner [místně](#how-to-use-container-on-your-local-host) nebo z vaší [privátního registru kontejneru](#how-to-add-container-to-private-registry) podle potřeby.

## <a name="how-to-use-container-on-your-local-host"></a>Jak používat kontejneru na místního hostitele

Pokud chcete vytvořit soubor Docker, nahraďte `<your-image-name>` s novým názvem obrázku, pak použijte:

```console
docker build -t <your-image-name> .
```

Spusťte image a odeberte ho, když se kontejner zastaví (`--rm`):

```console
docker run --rm <your-image-name>
```

## <a name="how-to-add-container-to-private-registry"></a>Jak přidat do privátního registru kontejneru

Použijte následující postup použijte soubor Dockerfile a následné uložení nové image váš privátní registr kontejnerů.  

1. Vytvoření `Dockerfile` s textem předpisu opakované použití. A `Dockerfile` nemá příponu.

1. Všechny hodnoty v lomených závorkách nahraďte vlastními hodnotami.

1. Zahrnout soubor do bitové kopie na příkazovém řádku nebo pomocí následujícího příkazu terminálu. Nahraďte hodnoty v lomených závorkách `<>`, s vlastní název kontejneru a značky.  

    Možnosti značky `-t`, je způsob, jak přidat informace o tom, co jste změnili pro kontejner. Například název kontejneru `modified-LUIS` označuje byl vrstvený původním kontejneru. Název značky `with-billing-and-model` Určuje, jak byl změněn kontejneru Language Understanding (LUIS).

    ```Bash
    docker build -t <your-new-container-name>:<your-new-tag-name> .
    ```

1. Přihlaste se k Azure CLI z konzoly. Tento příkaz otevře prohlížeč a vyžaduje ověření. Po ověření můžete zavřít prohlížeč a pokračovat v práci v konzole.

    ```azure-cli
    az login
    ```

1. Přihlaste se do privátního registru pomocí Azure CLI z konzoly.

    Nahraďte hodnoty v lomených závorkách `<my-registry>`, s názvem svého registru.  

    ```azure-cli
    az acr login --name <my-registry>
    ```

    Můžete také Přihlaste se pomocí docker login Pokud vám někdo přiřadí instančního objektu.

    ```Bash
    docker login <my-registry>.azurecr.io
    ```

1. Značka umístění privátního registru kontejneru. Nahraďte hodnoty v lomených závorkách `<my-registry>`, s názvem svého registru. 

    ```Bash
    docker tag <your-new-container-name>:<your-new-tag-name> <my-registry>.azurecr.io/<your-new-container-name-in-registry>:<your-new-tag-name>
    ```

    Pokud nepoužíváte název značky, `latest` je vyjádřena.

1. Nahrajte nový image pro váš privátní registr kontejnerů. Když zobrazujete váš privátní registr kontejnerů, bude název kontejneru používá následující příkaz rozhraní příkazového řádku v název úložiště.

    ```Bash
    docker push <my-registry>.azurecr.io/<your-new-container-name-in-registry>:<your-new-tag-name>
    ```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Vytvoření a použití Instance kontejneru Azure](azure-container-instance-recipe.md)

<!--
## Store input and output configuration settings

Bake in input params only

FROM containerpreview.azurecr.io/microsoft/cognitive-services-luis:<tag>
COPY luisModel1 /input/
COPY luisModel2 /input/

## Store all configuration settings

If you are a single manager of the container, you may want to store all settings in the container. The new, resulting container will not need any variables passed in to run. 

Issues with this approach:

* In order to change these settings, you will have to change the values of the Dockerfile and rebuild the file. 
* If someone gets access to your container registry or your local host, they can run the container and use the Cognitive Services endpoints. 

The following _partial_ Dockerfile shows how to statically set the values for billing and model. This example uses the 

```Dockerfile
FROM <container-registry>/<cognitive-service-container-name>:<tag>
ENV billing=<billing value>
ENV apikey=<apikey value>
COPY luisModel1 /input/
COPY luisModel2 /input/
```

->