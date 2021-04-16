---
title: Rychlý Start – sestavení a nabízení imagí kontejnerů Java pro Azure Container Registry s využitím Maven a výložníku
description: Sestavte kontejnerovou aplikaci Java a nahrajte ji do Azure Container Registry pomocí modulu plug-in Maven pro výložník.
author: KarlErickson
ms.author: karler
ms.date: 02/26/2020
ms.topic: quickstart
ms.custom:
- devx-track-java
- devx-track-azurecli
- mode-api
ms.openlocfilehash: 4d805458d90c73de879a9b87d5b08c98a8f1a250
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537298"
---
# <a name="quickstart-build-and-push-java-container-images-to-azure-container-registry"></a>Rychlý Start: sestavení a vložení imagí kontejnerů Java do Azure Container Registry

V tomto rychlém startu se dozvíte, jak vytvořit kontejnerovou aplikaci Java a vložit ji do Azure Container Registry pomocí modulu plug-in Maven. Jedním z příkladů použití nástrojů pro vývojáře k interakci se službou Azure Container Registry je použití Maven a výložníku.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud zatím předplatné Azure nemáte, můžete si aktivovat [výhody předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) nebo si zaregistrovat [bezplatný účet Azure](https://azure.microsoft.com/pricing/free-trial).
* Rozhraní příkazového [řádku Azure Command-Line (CLI)](/cli/azure/overview).
* Podporovaná sada Java Development Kit (JDK). Další informace o sadách JDK, které můžete využít při vývoji v Azure, najdete tady: <https://aka.ms/azure-jdks>.
* Nástroj [Maven](http://maven.apache.org) Build pro Apache (verze 3 nebo vyšší).
* Klient [Git](https://git-scm.com).
* Klient [Docker](https://www.docker.com).
* [Pomocníka s přihlašovacími údaji ACR Docker](https://github.com/Azure/acr-docker-credential-helper)

## <a name="create-the-spring-boot-on-docker-getting-started-web-app"></a>Vytvoření webové aplikace Začínáme s aplikací Spring Boot v Dockeru

Následující kroky vás provedou sestavením webové aplikace Spring Boot a jejím místním otestováním.

1. Z příkazového řádku pomocí následujícího příkazu naklonujte [jarní spuštění v docker Začínáme](https://github.com/spring-guides/gs-spring-boot-docker) ukázkový projekt.

   ```bash
   git clone https://github.com/spring-guides/gs-spring-boot-docker.git
   ```

1. Přejděte do adresáře pro dokončený projekt.

   ```bash
   cd gs-spring-boot-docker/complete
   ```

1. Pomocí Mavenu sestavte a spusťte ukázkovou aplikaci.

   ```bash
   mvn package spring-boot:run
   ```

1. Otestujte webovou aplikaci tak, že přejdete na adresu `http://localhost:8080`, nebo pomocí následujícího příkazu `curl`:

   ```bash
   curl http://localhost:8080
   ```

Měla by se zobrazit následující zpráva: **Hello World**

## <a name="create-an-azure-container-registry-using-the-azure-cli"></a>Vytvoření registru kontejneru Azure pomocí Azure CLI

V dalším kroku vytvoříte skupinu prostředků Azure a své ACR pomocí následujících kroků:

1. Přihlaste se k účtu Azure pomocí následujícího příkazu:

   ```azurecli
   az login
   ```

1. Zadejte předplatné Azure, které se má použít:

   ```azurecli
   az account set -s <subscription ID>
   ```

1. Vytvořte skupinu prostředků pro prostředky Azure použité v tomto kurzu. V následujícím příkazu Nezapomeňte nahradit zástupné symboly vlastním názvem prostředku a umístěním, jako je například `eastus` .

   ```azurecli
   az group create \
       --name=<your resource group name> \
       --location=<location>
   ```

1. Pomocí následujícího příkazu vytvořte ve skupině prostředků privátní službu Azure Container Registry. Zástupné symboly Nezapomeňte nahradit skutečnými hodnotami. Kurz v pozdějších krocích předá ukázkovou aplikaci jako image Dockeru do tohoto registru.

   ```azurecli
   az acr create \
       --resource-group <your resource group name> \
       --location <location> \
       --name <your registry name> \
       --sku Basic
   ```

## <a name="push-your-app-to-the-container-registry-via-jib"></a>Předání aplikace do registru kontejneru pomocí Jib

Nakonec aktualizujete konfiguraci projektu a pomocí příkazového řádku sestavíte a nasadíte svou image.

> [!NOTE]
> Pokud se chcete přihlásit ke službě Azure Container Registry, kterou jste právě vytvořili, musíte mít spuštěný démon Docker. Pokud chcete nainstalovat Docker na váš počítač, najdete [tu oficiální dokumentaci k Docker](https://docs.docker.com/install/).

1. Přihlaste se ke svému Azure Container Registry z Azure CLI pomocí následujícího příkazu. Zástupný symbol je nutné nahradit vlastním názvem registru.

   ```azurecli
   az configure --defaults acr=<your registry name>
   az acr login
   ```

   `az configure`Příkaz nastaví výchozí název registru pro použití s `az acr` příkazy.

1. Přejděte do adresáře dokončeného projektu pro aplikaci Spring Boot (například "*C:\SpringBoot\gs-spring-boot-docker\complete*" nebo "*/users/robert/SpringBoot/gs-spring-boot-docker/complete*") a v textovém editoru otevřete soubor *pom.xml*.

1. Aktualizujte `<properties>` kolekci v souboru *pom.xml* pomocí následujícího kódu XML. Zástupný text nahraďte názvem registru a přidejte `<jib-maven-plugin.version>` vlastnost s hodnotou `2.2.0` nebo novější verzí [Maven-plugin](https://github.com/GoogleContainerTools/jib/tree/master/jib-maven-plugin).

   ```xml
   <properties>
      <docker.image.prefix><your registry name>.azurecr.io</docker.image.prefix>
      <java.version>1.8</java.version>
      <jib-maven-plugin.version>2.2.0</jib-maven-plugin.version>
   </properties>
   ```

1. Aktualizujte `<plugins>` kolekci v souboru *pom.xml* tak, aby `<plugin>` element obsahoval a položku pro `jib-maven-plugin` , jak je znázorněno v následujícím příkladu. Všimněte si, že používáme základní image ze sady Microsoft Container Registry (MCR): `mcr.microsoft.com/java/jdk:8-zulu-alpine` , která obsahuje oficiálně podporovanou JDK pro Azure. Další základní image MCR s oficiálně podporovaným sady JDK najdete v tématu [Java se JDK](https://hub.docker.com/_/microsoft-java-jdk), [Java se JRE](https://hub.docker.com/_/microsoft-java-jre), [Java se bez periferních JRE](https://hub.docker.com/_/microsoft-java-jre-headless)a [Java se JDK a Maven](https://hub.docker.com/_/microsoft-java-maven).

   ```xml
   <plugin>
     <artifactId>jib-maven-plugin</artifactId>
     <groupId>com.google.cloud.tools</groupId>
     <version>${jib-maven-plugin.version}</version>
     <configuration>
        <from>
            <image>mcr.microsoft.com/java/jdk:8-zulu-alpine</image>
        </from>
        <to>
            <image>${docker.image.prefix}/${project.artifactId}</image>
        </to>
     </configuration>
   </plugin>
   ```

1. Přejděte do adresáře dokončeného projektu pro aplikaci Spring Boot a spuštěním následujícího příkazu sestavte image a předejte ji do registru:

   ```bash
   az acr login && mvn compile jib:build
   ```

> [!NOTE]
>
> Z bezpečnostních důvodů platí, že pověření vytvořená nástrojem platí `az acr login` pouze pro 1 hodinu. Pokud obdržíte *neoprávněnou chybu 401* , můžete `az acr login -n <your registry name>` znovu spustit příkaz a znovu ověřit.

## <a name="verify-your-container-image"></a>Ověření image kontejneru

Gratulujeme! Teď máte svoji kontejnerovou aplikaci Java, která je v Azure podporovaná JDK, vložená do vaší ACR. Bitovou kopii teď můžete otestovat nasazením, aby se Azure App Service, nebo když ji nasadíte do místní příkazového řádku (nahraďte zástupný symbol):

```bash
docker pull <your registry name>.azurecr.io/gs-spring-boot-docker
```

## <a name="next-steps"></a>Další kroky

Další verze oficiálních imagí založených na jazyce Java podporovaných Microsoftem najdete v těchto tématech:

* [Java SE JDK](https://hub.docker.com/_/microsoft-java-jdk)
* [Java SE JRE](https://hub.docker.com/_/microsoft-java-jre)
* [Java SE JRE bez periferních zařízení](https://hub.docker.com/_/microsoft-java-jre-headless)
* [Java SE JDK a Maven](https://hub.docker.com/_/microsoft-java-maven)

Pokud se chcete dozvědět více o architektuře Spring a Azure, přejděte do centra dokumentace Spring v Azure.

> [!div class="nextstepaction"]
> [Spring v Azure](/azure/developer/java/spring-framework)

### <a name="additional-resources"></a>Další materiály

Další informace naleznete v následujících zdrojích:

* [Azure pro vývojáře v jazyce Java](/azure/java)
* [Práce s Azure DevOps a Javou](/azure/devops/java)
* [Začínáme s aplikací Spring Boot v Dockeru](https://spring.io/guides/gs/spring-boot-docker)
* [Spring Initializr](https://start.spring.io)
* [Nasazení aplikace Spring Boot do služby Azure App Service](/azure/developer/java/spring-framework/deploy-spring-boot-java-app-on-linux#configure-maven-to-build-image-to-your-azure-container-registry)
* [Použití vlastní image Dockeru pro webovou aplikaci Azure v Linuxu](../app-service/tutorial-custom-container.md)
