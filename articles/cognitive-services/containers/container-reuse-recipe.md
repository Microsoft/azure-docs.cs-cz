---
title: Recepty pro kontejnery Docker
titleSuffix: Azure Cognitive Services
description: Pomocí těchto receptů kontejnerů můžete vytvářet Cognitive Services kontejnery, které se dají znovu použít. Kontejnery lze vytvořit s některými nebo všemi nastaveními konfigurace, aby při spuštění kontejneru nebyly nutné. Jakmile budete mít tuto novou vrstvu kontejneru (s nastaveními) a otestujete ji místně, můžete kontejner Uložit do registru kontejneru. Když se kontejner spustí, bude potřebovat jenom ta nastavení, která se v kontejneru aktuálně neukládají.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: a8162f96051a73b9f6e6a6fe3ece020e0a94f08f
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70068827"
---
# <a name="create-containers-for-reuse"></a>Vytváření kontejnerů pro opakované použití

Pomocí těchto receptů kontejnerů můžete vytvářet Cognitive Services kontejnery, které se dají znovu použít. Kontejnery lze vytvořit s některými nebo všemi nastaveními konfigurace, aby při spuštění kontejneru _nebyly_ nutné.

Jakmile budete mít tuto novou vrstvu kontejneru (s nastaveními) a otestujete ji místně, můžete kontejner Uložit do registru kontejneru. Když se kontejner spustí, bude potřebovat jenom ta nastavení, která se v kontejneru aktuálně neukládají. Kontejner privátního registru poskytuje prostor pro konfiguraci, ve kterém můžete tato nastavení předat.

## <a name="docker-run-syntax"></a>Syntaxe příkazu Docker

Všechny `docker run` příklady v tomto dokumentu předpokládají konzolu Windows se znakem pro `^` pokračování řádku. Pro vlastní použití Vezměte v úvahu následující:

* Pořadí argumentů nezmění, pokud máte velmi zkušenosti s kontejnery dockeru.
* Pokud používáte jiný operační systém než Windows nebo jinou konzolu než konzolu Windows, použijte správnou konzolu/terminál, syntaxi složky pro připojení a znak pro pokračování řádku pro konzolu a systém.  Vzhledem k tomu, že kontejner Cognitive Services je operačním systémem Linux, cílový připojení používá syntaxi složky ve stylu systému Linux.
* `docker run`příklady používají adresář mimo `c:` jednotku, aby nedocházelo ke konfliktům oprávnění ve Windows. Pokud je potřeba použít konkrétní adresář jako vstupní adresář, budete muset udělit dockeru služby oprávnění.

## <a name="store-no-configuration-settings-in-image"></a>Uložení nastavení konfigurace v imagi

Ukázkové `docker run` příkazy pro každou službu neukládají do kontejneru žádná nastavení konfigurace. Při spuštění kontejneru z konzoly nebo služby registru musí být tato nastavení konfigurace předána. Kontejner privátního registru poskytuje prostor pro konfiguraci, ve kterém můžete tato nastavení předat.

## <a name="reuse-recipe-store-all-configuration-settings-with-container"></a>Znovu použít recept: uložit všechna nastavení konfigurace s kontejnerem

Chcete-li uložit všechna nastavení konfigurace, vytvořte `Dockerfile` pomocí těchto nastavení.

Problémy s tímto přístupem:

* Nový kontejner má samostatný název a značku z původního kontejneru.
* Chcete-li změnit tato nastavení, budete muset změnit hodnoty souboru Dockerfile, znovu sestavit image a znovu publikovat do registru.
* Pokud někdo získá přístup k vašemu registru kontejneru nebo vašemu místnímu hostiteli, může spustit kontejner a použít koncové body Cognitive Services.
* Pokud vaše služba rozpoznávání nevyžaduje vstupní připojení, nepřidávejte `COPY` řádky do souboru Dockerfile.

Vytvořte souboru Dockerfile, nahlaste se z existujícího kontejneru Cognitive Services, který chcete použít, a pak pomocí příkazů Docker v souboru Dockerfile nastavte nebo získejte informace, které kontejner potřebuje.

Tento příklad:

* Nastaví koncový bod `{BILLING_ENDPOINT}` fakturace z klíče prostředí hostitele pomocí `ENV`.
* Nastaví klíč `{ENDPOINT_KEY}` rozhraní API pro fakturaci z klíče prostředí hostitele pomocí env.

### <a name="reuse-recipe-store-billing-settings-with-container"></a>Použít recept: Uložit nastavení fakturace s kontejnerem

Tento příklad ukazuje, jak sestavit Analýza textu kontejner mínění z souboru Dockerfile.

```Dockerfile
FROM mcr.microsoft.com/azure-cognitive-services/sentiment:latest
ENV billing={BILLING_ENDPOINT}
ENV apikey={ENDPOINT_KEY}
ENV EULA=accept
```

V případě potřeby Sestavte a spusťte kontejner [místně](#how-to-use-container-on-your-local-host) nebo z [kontejneru privátního registru](#how-to-add-container-to-private-registry) .

### <a name="reuse-recipe-store-billing-and-mount-settings-with-container"></a>Znovu použít recept: nastavení fakturace a připojení úložiště pomocí kontejneru

Tento příklad ukazuje, jak použít Language Understanding, a to uložením fakturace a modelů z souboru Dockerfile.

* Zkopíruje soubor modelu Language Understanding (LUIS) ze systému souborů hostitele pomocí `COPY`.
* Kontejner LUIS podporuje více než jeden model. Pokud jsou všechny modely uloženy ve stejné složce, budete potřebovat jeden `COPY` příkaz.
* Spusťte soubor Docker z relativního nadřazeného objektu vstupního adresáře modelu. V následujícím příkladu spusťte `docker build` příkazy a `docker run` z relativního nadřazeného prvku `/input`. První `/input` příkaz`COPY` v příkazu je adresář hostitelského počítače. Druhým `/input` je adresář kontejneru.

```Dockerfile
FROM <container-registry>/<cognitive-service-container-name>:<tag>
ENV billing={BILLING_ENDPOINT}
ENV apikey={ENDPOINT_KEY}
ENV EULA=accept
COPY /input /input
```

V případě potřeby Sestavte a spusťte kontejner [místně](#how-to-use-container-on-your-local-host) nebo z [kontejneru privátního registru](#how-to-add-container-to-private-registry) .

## <a name="how-to-use-container-on-your-local-host"></a>Jak používat kontejner na místním hostiteli

Pokud chcete sestavit soubor Docker, nahraďte `<your-image-name>` novým názvem Image a pak použijte:

```console
docker build -t <your-image-name> .
```

Spuštění image a její odebrání po zastavení kontejneru (`--rm`):

```console
docker run --rm <your-image-name>
```

## <a name="how-to-add-container-to-private-registry"></a>Postup přidání kontejneru do privátního registru

Použijte následující postup, chcete-li použít souboru Dockerfile a umístit novou bitovou kopii do privátního registru kontejneru.  

1. `Dockerfile` Vytvořte s textem z opakovaného použití návodu. `Dockerfile` Přípona nemá.

1. Nahraďte všechny hodnoty v lomených závorkách vlastními hodnotami.

1. Sestavte soubor do bitové kopie v příkazovém řádku nebo terminálu pomocí následujícího příkazu. Hodnoty v lomených závorkách `<>`nahraďte vlastním názvem a označením kontejneru.  

    Možnost značky je způsob `-t`, jak přidat informace o tom, co jste u kontejneru změnili. Například název `modified-LUIS` kontejneru označuje, že byl původní kontejner vrstven. Název `with-billing-and-model` značky indikuje, jak byl upraven kontejner Language Understanding (Luis).

    ```Bash
    docker build -t <your-new-container-name>:<your-new-tag-name> .
    ```

1. Přihlaste se k Azure CLI z konzoly. Tento příkaz otevře prohlížeč a vyžaduje ověření. Po ověření můžete prohlížeč zavřít a pokračovat v práci v konzole nástroje.

    ```azurecli
    az login
    ```

1. Přihlaste se ke svému privátnímu registru pomocí Azure CLI z konzoly.

    Hodnoty v lomených závorkách `<my-registry>`nahraďte vlastním názvem registru.  

    ```azurecli
    az acr login --name <my-registry>
    ```

    Pokud jste přidělili instanční objekt, můžete se také přihlásit pomocí služby Docker Login.

    ```Bash
    docker login <my-registry>.azurecr.io
    ```

1. Označte kontejner pomocí privátního umístění registru. Hodnoty v lomených závorkách `<my-registry>`nahraďte vlastním názvem registru. 

    ```Bash
    docker tag <your-new-container-name>:<your-new-tag-name> <my-registry>.azurecr.io/<your-new-container-name-in-registry>:<your-new-tag-name>
    ```

    Pokud nepoužijete název značky, `latest` bude předpokládaná.

1. Nahrajte nový obrázek do svého privátního registru kontejnerů. Při zobrazení soukromého registru kontejnerů bude název kontejneru, který se používá v následujícím příkazu rozhraní příkazového řádku, název úložiště.

    ```Bash
    docker push <my-registry>.azurecr.io/<your-new-container-name-in-registry>:<your-new-tag-name>
    ```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Vytvoření a použití instance kontejneru Azure](azure-container-instance-recipe.md)

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