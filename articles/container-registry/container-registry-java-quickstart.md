---
title: Úvodní příručka – vytváření a nabízení ibi kontejnerů Java do registru kontejnerů Azure pomocí Maven a Jib
description: Vytvořte kontejnerizovanou aplikaci Java a zatlačte ji do registru kontejnerů Azure pomocí pluginu Maven Jib.
author: KarlErickson
ms.author: karler
ms.topic: quickstart
ms.date: 02/26/2020
ms.openlocfilehash: ef933ae9a6b0a34529c7ec145b13c023728a3457
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81731863"
---
# <a name="quickstart-build-and-push-java-container-images-to-azure-container-registry"></a>Úvodní příručka: Vytváření a nabízení ibi kontejnerů Java do registru kontejnerů Azure

Tento rychlý start ukazuje, jak vytvořit kontejnerizovanou aplikaci Java a zatlačit ji do registru kontejnerů Azure pomocí pluginu Maven Jib. Použití Maven a Jib je jedním z příkladů použití vývojářských nástrojů pro interakci s registrem kontejnerů Azure.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud zatím předplatné Azure nemáte, můžete si aktivovat [výhody předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) nebo si zaregistrovat [bezplatný účet Azure](https://azure.microsoft.com/pricing/free-trial).
* [Rozhraní příkazového řádku Azure (CLI)](/cli/azure/overview)
* Podporovaná sada Java Development Kit (JDK). Další informace o sadách JDK, které můžete využít při vývoji v Azure, najdete tady: <https://aka.ms/azure-jdks>.
* Apache [Maven](http://maven.apache.org) sestavení nástroj (verze 3 nebo vyšší).
* Klient [Git](https://git-scm.com).
* Klient [Docker](https://www.docker.com).
* [Pomocník pověření ACR Docker .](https://github.com/Azure/acr-docker-credential-helper)

## <a name="create-the-spring-boot-on-docker-getting-started-web-app"></a>Vytvoření webové aplikace Začínáme s aplikací Spring Boot v Dockeru

Následující kroky vás provedou sestavením webové aplikace Spring Boot a jejím místním otestováním.

1. Na příkazovém řádku můžete pomocí následujícího příkazu klonovat ukázkový projekt [Jarní spuštění v Dockeru Začínáme.](https://github.com/spring-guides/gs-spring-boot-docker)

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

Měla by se zobrazit následující zpráva: **Hello Docker World**

## <a name="create-an-azure-container-registry-using-the-azure-cli"></a>Vytvoření registru kontejneru Azure pomocí Azure CLI

Dále vytvoříte skupinu prostředků Azure a acr pomocí následujících kroků:

1. Přihlaste se ke svému účtu Azure pomocí následujícího příkazu:

   ```azurecli
   az login
   ```

1. Zadejte předplatné Azure, které se má používat:

   ```azurecli
   az account set -s <subscription ID>
   ```

1. Vytvořte skupinu prostředků pro prostředky Azure použité v tomto kurzu. V následujícím příkazu nezapomeňte nahradit zástupné symboly vlastním názvem prostředku `eastus`a umístěním, například .

   ```azurecli
   az group create \
       --name=<your resource group name> \
       --location=<location>
   ```

1. Vytvořte soukromý registr kontejnerů Azure ve skupině prostředků pomocí následujícího příkazu. Nezapomeňte nahradit zástupné symboly skutečnými hodnotami. Kurz v pozdějších krocích předá ukázkovou aplikaci jako image Dockeru do tohoto registru.

   ```azurecli
   az acr create \
       --resource-group <your resource group name> \
       --location <location> \
       --name <your registry name> \
       --sku Basic
   ```

## <a name="push-your-app-to-the-container-registry-via-jib"></a>Předání aplikace do registru kontejneru pomocí Jib

Nakonec aktualizujete konfiguraci projektu a pomocí příkazového řádku vytvoříte a nasadíte svou bitovou kopii.

1. Přihlaste se k registru kontejnerů Azure z azure cli pomocí následujícího příkazu. Zástupný symbol nezapomeňte nahradit vlastním názvem registru.

   ```azurecli
   az configure --defaults acr=<your registry name>
   az acr login
   ```

   Příkaz `az configure` nastaví výchozí název registru `az acr` pro použití s příkazy.

1. Přejděte do adresáře dokončeného projektu pro aplikaci Spring Boot (například "*C:\SpringBoot\gs-spring-boot-docker\complete*" nebo "*/users/robert/SpringBoot/gs-spring-boot-docker/complete*") a v textovém editoru otevřete soubor *pom.xml*.

1. Aktualizujte `<properties>` kolekci v souboru *pom.xml* pomocí následujícího xml. Nahraďte zástupný symbol názvem registru `<jib-maven-plugin.version>` a aktualizujte hodnotu nejnovější verzí [modulu plug-in jib-maven](https://github.com/GoogleContainerTools/jib/tree/master/jib-maven-plugin).

   ```xml
   <properties>
      <docker.image.prefix><your registry name>.azurecr.io</docker.image.prefix>
      <jib-maven-plugin.version>1.8.0</jib-maven-plugin.version>
      <java.version>1.8</java.version>
   </properties>
   ```

1. Aktualizujte `<plugins>` kolekci v souboru *pom.xml* tak, aby `<plugin>` prvek obsahoval a pozadáním `jib-maven-plugin`pro , jak je znázorněno v následujícím příkladu. Všimněte si, že používáme základní bitovou kopii z registru kontejnerů Microsoft (MCR): `mcr.microsoft.com/java/jdk:8-zulu-alpine`, která obsahuje oficiálně podporovanou sadu JDK pro Azure. Další základní obrázky MCR s oficiálně podporovanými JDK viz [Java SE JDK](https://hub.docker.com/_/microsoft-java-jdk), [Java SE JRE](https://hub.docker.com/_/microsoft-java-jre), [Java SE Headless JRE](https://hub.docker.com/_/microsoft-java-jre-headless)a [Java SE JDK a Maven](https://hub.docker.com/_/microsoft-java-maven).

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
   mvn compile jib:build
   ```

> [!NOTE]
>
> Z bezpečnostních důvodů je `az acr login` pověření vytvořená pouze 1 hodinu. Pokud se zobrazí chyba *401 Neautorizovaný,* můžete `az acr login -n <your registry name>` příkaz spustit znovu a znovu ověřit.

## <a name="verify-your-container-image"></a>Ověření obrázku kontejneru

Blahopřejeme! Teď máte kontejnerizované Java App sestavení na Azure podporované JDK zasunuté do acr. Nyní můžete otestovat image nasazením do služby Azure App Service nebo vytažením do místní ho pomocí příkazu (nahrazení zástupného symbolu):

```bash
docker pull <your registry name>.azurecr.io/gs-spring-boot-docker:v1
```

## <a name="next-steps"></a>Další kroky

Další verze oficiálních základních bitových kopií Jazyka Java podporovaných společností Microsoft naleznete v následujících tématech:

* [Java SE JDK](https://hub.docker.com/_/microsoft-java-jdk)
* [Java SE JRE](https://hub.docker.com/_/microsoft-java-jre)
* [Java SE Bezhlavý JRE](https://hub.docker.com/_/microsoft-java-jre-headless)
* [Java SE JDK a Maven](https://hub.docker.com/_/microsoft-java-maven)

Pokud se chcete dozvědět více o architektuře Spring a Azure, přejděte do centra dokumentace Spring v Azure.

> [!div class="nextstepaction"]
> [Spring v Azure](/azure/developer/java/spring-framework)

### <a name="additional-resources"></a>Další zdroje

Další informace najdete v následujících materiálech:

* [Azure pro vývojáře Vivy](/azure/java)
* [Práce s Azure DevOps a Javou](/azure/devops/java)
* [Začínáme s aplikací Spring Boot v Dockeru](https://spring.io/guides/gs/spring-boot-docker)
* [Spring Initializr](https://start.spring.io)
* [Nasazení aplikace Spring Boot do služby Azure App Service](/azure/developer/java/spring-framework/deploy-spring-boot-java-app-from-container-registry-using-maven-plugin)
* [Použití vlastní image Dockeru pro webovou aplikaci Azure v Linuxu](/azure/app-service-web/app-service-linux-using-custom-docker-image)
