---
title: Recepty pro kontejnery Dockeru
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak vytvářet, testovat a ukládat kontejnery s některými nebo všemi nastaveními konfigurace pro nasazení a opakované použití.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 7380ff58d033a68565de7e419ff318f7bdec121d
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80875074"
---
# <a name="create-containers-for-reuse"></a>Vytváření kontejnerů pro opakované použití

Pomocí těchto receptů kontejneru vytvořit kontejnery služeb Cognitive Services, které lze znovu použít. Kontejnery mohou být sestaveny s některými nebo všemi nastaveními konfigurace tak, aby _nebyly_ potřebné při spuštění kontejneru.

Jakmile budete mít tuto novou vrstvu kontejneru (s nastavením) a testovali jste ji místně, můžete kontejner uložit do registru kontejneru. Při spuštění kontejneru bude potřebovat pouze nastavení, která nejsou aktuálně uložena v kontejneru. Kontejner soukromého registru poskytuje konfigurační prostor pro předání těchto nastavení.

## <a name="docker-run-syntax"></a>Syntaxe spuštění Dockeru

Všechny `docker run` příklady v tomto dokumentu předpokládají `^` konzolu systému Windows se znakem pokračování řádku. Zvažte následující pro vlastní potřebu:

* Neměňte pořadí argumentů, pokud nejste velmi dobře obeznámeni s kontejnery dockeru.
* Pokud používáte jiný operační systém než Windows nebo jinou konzolu než konzolu systému Windows, použijte správnou konzolu/terminál, syntaxi složky pro připojení a znak pokračování řádku konzoly a systému.  Vzhledem k tomu, že kontejner Cognitive Services je operační systém Linux, cílové připojení používá syntaxi složky ve stylu Linuxu.
* `docker run`Příklady používají adresář `c:` mimo jednotku, aby se zabránilo konfliktům oprávnění v systému Windows. Pokud potřebujete použít konkrétní adresář jako vstupní adresář, budete možná muset udělit oprávnění služby dockeru.

## <a name="store-no-configuration-settings-in-image"></a>V bitové kopii není ukládat žádná nastavení konfigurace

Ukázkové `docker run` příkazy pro každou službu neukládají žádné nastavení konfigurace v kontejneru. Při spuštění kontejneru z konzoly nebo služby registru, tato nastavení konfigurace je třeba předat. Kontejner soukromého registru poskytuje konfigurační prostor pro předání těchto nastavení.

## <a name="reuse-recipe-store-all-configuration-settings-with-container"></a>Opakované použití recept: uložit všechna nastavení konfigurace s kontejnerem

Chcete-li uložit všechna nastavení `Dockerfile` konfigurace, vytvořte s těmito nastaveními.

Problémy s tímto přístupem:

* Nový kontejner má samostatný název a značku z původního kontejneru.
* Chcete-li změnit tato nastavení, budete muset změnit hodnoty Dockerfile, znovu vytvořit bitovou kopii a znovu publikovat do registru.
* Pokud někdo získá přístup k registru kontejneru nebo místního hostitele, může spustit kontejner a použít koncové body služeb Cognitive Services.
* Pokud vaše kognitivní služba nevyžaduje vstupní připojení, nepřidávejte řádky do souboru `COPY` Dockerfile.

Vytvořte Dockerfile, vytažení z existujícího kontejneru Cognitive Services, který chcete použít, a pak použijte příkazy dockeru v souboru Dockerfile k nastavení nebo vyžádat informace, které kontejner potřebuje.

Tento příklad:

* Nastaví koncový bod `{BILLING_ENDPOINT}` fakturace z klíče `ENV`prostředí hostitele pomocí .
* Nastaví fakturační klíč `{ENDPOINT_KEY}` ROZHRANÍ API z klíče prostředí hostitele pomocí env.

### <a name="reuse-recipe-store-billing-settings-with-container"></a>Opakované použití recept: uložit nastavení fakturace s kontejnerem

Tento příklad ukazuje, jak vytvořit kontejner mínění analýzy textu z dockerfile.

```Dockerfile
FROM mcr.microsoft.com/azure-cognitive-services/sentiment:latest
ENV billing={BILLING_ENDPOINT}
ENV apikey={ENDPOINT_KEY}
ENV EULA=accept
```

Vytvořte a spusťte kontejner [místně](#how-to-use-container-on-your-local-host) nebo z [kontejneru soukromého registru](#how-to-add-container-to-private-registry) podle potřeby.

### <a name="reuse-recipe-store-billing-and-mount-settings-with-container"></a>Opětovné použití recept: uložit nastavení fakturace a připojení s kontejnerem

Tento příklad ukazuje, jak používat language understanding, ukládání fakturace a modely z Dockerfile.

* Zkopíruje soubor modelu Language Understanding (LUIS) ze `COPY`systému souborů hostitele pomocí .
* Kontejner LUIS podporuje více než jeden model. Pokud jsou všechny modely uloženy ve stejné `COPY` složce, potřebujete všichni jeden příkaz.
* Spusťte soubor dockeru z relativního nadřazeného vstupního adresáře modelu. V následujícím příkladu `docker build` spusťte příkazy `docker run` a `/input`z relativního nadřazeného příkazu . První `/input` na `COPY` příkaz je adresář hostitelského počítače. Druhý `/input` je adresář kontejneru.

```Dockerfile
FROM <container-registry>/<cognitive-service-container-name>:<tag>
ENV billing={BILLING_ENDPOINT}
ENV apikey={ENDPOINT_KEY}
ENV EULA=accept
COPY /input /input
```

Vytvořte a spusťte kontejner [místně](#how-to-use-container-on-your-local-host) nebo z [kontejneru soukromého registru](#how-to-add-container-to-private-registry) podle potřeby.

## <a name="how-to-use-container-on-your-local-host"></a>Jak používat kontejner na místním hostiteli

Chcete-li vytvořit soubor `<your-image-name>` Dockeru, nahraďte novým názvem bitové kopie a pak použijte:

```console
docker build -t <your-image-name> .
```

Spuštění bitové kopie a jeho odebrání`--rm`při zastavení kontejneru ( ):

```console
docker run --rm <your-image-name>
```

## <a name="how-to-add-container-to-private-registry"></a>Jak přidat kontejner do soukromého registru

Následujícím postupem použijte Dockerfile a umístěte novou bitovou kopii do registru soukromého kontejneru.  

1. Vytvořte `Dockerfile` s textem z opakovaného použití receptu. A `Dockerfile` nemá prodloužení.

1. Nahraďte všechny hodnoty v úhlových závorkách vlastními hodnotami.

1. Vytvořte soubor do obrazu na příkazovém řádku nebo terminálu pomocí následujícího příkazu. Nahraďte hodnoty v úhlových závorkách `<>`, vlastním názvem kontejneru a značkou.  

    Možnost značky `-t`, je způsob, jak přidat informace o tom, co jste změnili pro kontejner. Například název kontejneru `modified-LUIS` označuje, že původní kontejner byl vrstvený. Název značky `with-billing-and-model` označuje, jak byl změněn kontejner jazyka (LUIS).

    ```Bash
    docker build -t <your-new-container-name>:<your-new-tag-name> .
    ```

1. Přihlaste se k Azure CLI z konzoly. Tento příkaz otevře prohlížeč a vyžaduje ověření. Po ověření můžete zavřít prohlížeč a pokračovat v práci v konzole.

    ```azurecli
    az login
    ```

1. Přihlaste se k privátnímu registru pomocí azure cli z konzoly.

    Nahraďte hodnoty v úhlových závorkách `<my-registry>`, vlastním názvem registru.  

    ```azurecli
    az acr login --name <my-registry>
    ```

    Můžete se také přihlásit pomocí přihlášení dockeru, pokud je vám přiřazen instanční objekt.

    ```Bash
    docker login <my-registry>.azurecr.io
    ```

1. Označte kontejner soukromým umístěním registru. Nahraďte hodnoty v úhlových závorkách `<my-registry>`, vlastním názvem registru. 

    ```Bash
    docker tag <your-new-container-name>:<your-new-tag-name> <my-registry>.azurecr.io/<your-new-container-name-in-registry>:<your-new-tag-name>
    ```

    Pokud nepoužíváte název značky, `latest` je implicitní.

1. Zasuňte novou bitovou kopii do registru soukromého kontejneru. Při zobrazení registru privátní kontejner, název kontejneru použitý v následujícím příkazu příkazu příkazu příkazu cli bude název úložiště.

    ```Bash
    docker push <my-registry>.azurecr.io/<your-new-container-name-in-registry>:<your-new-tag-name>
    ```

## <a name="next-steps"></a>Další kroky

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