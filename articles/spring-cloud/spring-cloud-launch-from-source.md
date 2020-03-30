---
title: Úvodní příručka – Spuštění aplikace Spring Cloud ze zdrojového kódu
description: V tomto rychlém startu se dozvíte, jak spustit aplikaci Azure Spring Cloud přímo ze zdrojového kódu.
author: bmitchell287
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/30/2019
ms.author: brendm
ms.openlocfilehash: 3ab4b1729ea380671b72a9bb01740930a186d5c3
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "79470790"
---
# <a name="quickstart-launch-your-spring-cloud-application-from-source-code"></a>Úvodní příručka: Spuštění aplikace Spring Cloud ze zdrojového kódu

Azure Spring Cloud umožňuje snadno spouštět aplikace mikroslužeb založené na Spring Cloudu v Azure.

Azure Spring Cloud umožňuje spouštět aplikaci přímo z vašeho zdrojového kódu java nebo z předem sestaveného JAR. Tento článek vás provede požadovanými kroky.

Po tomto rychlém startu se dozvíte, jak:

> [!div class="checklist"]
> * Zřízení instance služby
> * Nastavení konfiguračního serveru pro instanci
> * Místní vytvoření aplikace mikroslužeb
> * Nasazení jednotlivých mikroslužeb
> * Přiřazení veřejného koncového bodu pro vaši aplikaci

## <a name="prerequisites"></a>Požadavky

>[!Note]
> Azure Spring Cloud se momentálně nabízí jako veřejná verze Preview. Nabídky ve verzi Public Preview umožňují zákazníkům experimentovat s novými funkcemi před jejich oficiálním vydáním.  Funkce a služby veřejné verze Preview nejsou určeny pro produkční použití.  Další informace o podpoře během náhledů najdete v [nejčastějších dotazech](https://azure.microsoft.com/support/faq/) nebo najdete [žádost o podporu,](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) kde se dozvíte další informace.

Než začnete, ujistěte se, že vaše předplatné Azure obsahuje požadované závislosti:

1. [Nainstalovat Git](https://git-scm.com/).
2. [Instalace JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
3. [Instalace maven 3.0 nebo vyšší](https://maven.apache.org/download.cgi)
4. [Instalace příkazového příkazového příkazu Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
5. [Registrace předplatného Azure](https://azure.microsoft.com/free/)

> [!TIP]
> Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku.  Má společné nástroje Azure předinstalované, včetně nejnovějších verzí Git, JDK, Maven a Azure CLI. Pokud jste přihlášení k předplatnému Azure, spusťte [Azure Cloud Shell](https://shell.azure.com) z shell.azure.com.  Další informace o Azure Cloud Shellu najdete v [naší dokumentaci](../cloud-shell/overview.md)

## <a name="install-the-azure-cli-extension"></a>Instalace rozšíření Azure CLI

Instalace rozšíření Azure Spring Cloud pro azure cli pomocí následujícího příkazu

```azurecli
az extension add --name spring-cloud
```

## <a name="provision-a-service-instance-using-the-azure-cli"></a>Zřízení instance služby pomocí příkazového příkazu k webu Azure

Login to the Azure CLI and choose your active subscription. Nezapomeňte si vybrat aktivní předplatné, které je na seznamu povolených pro Azure Spring Cloud.

```azurecli
az login
az account list -o table
az account set --subscription
```

Vytvořte skupinu prostředků, která bude obsahovat vaši službu Azure Spring Cloud. Další informace o [skupinách prostředků Azure](../azure-resource-manager/management/overview.md).

```azurecli
az group create --location eastus --name <resource group name>
```

Spusťte následující příkazy pro zřízení instance Azure Spring Cloud. Připravte si název pro vaši službu Azure Spring Cloud. Název musí být dlouhý 4 až 32 znaků a může obsahovat pouze malá písmena, číslice a pomlčky. První znak názvu služby musí být písmeno a poslední znak musí být písmeno nebo číslo.

```azurecli
az spring-cloud create -n <resource name> -g <resource group name>
```

Nasazení instance služby bude trvat přibližně pět minut.

Nastavte výchozí název skupiny prostředků a název clusteru pomocí následujících příkazů:

```azurecli
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
```

> [!div class="nextstepaction"]
> [Narazil(a) jsem na problém](https://www.research.net/r/javae2e?tutorial=asc-source-quickstart&step=provision)

## <a name="create-the-spring-cloud-application"></a>Vytvoření aplikace Spring Cloud

Následující příkaz vytvoří aplikaci Spring Cloud ve vašem předplatném.  Tím se vytvoří prázdná služba Spring Cloud, do které můžeme nahrát naši aplikaci.

```azurecli
az spring-cloud app create -n <app-name>
```

## <a name="deploy-your-spring-cloud-application"></a>Nasazení aplikace Spring Cloud

Aplikaci můžete nasadit z předem sestavené jar nebo z úložiště Gradle nebo Maven.  Níže naleznete pokyny pro každý případ.

### <a name="deploy-a-built-jar"></a>Nasazení vytvořeného jar

Chcete-li nasadit z JAR postavené na místním počítači, ujistěte se, že vaše sestavení vytváří [tuk JAR](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-build.html#howto-create-an-executable-jar-with-maven).

Nasazení fat-JAR do aktivního nasazení

```azurecli
az spring-cloud app deploy -n <app-name> --jar-path <path-to-fat-JAR>
```

Nasazení fat-JAR do konkrétního nasazení

```azurecli
az spring-cloud app deployment create --app <app-name> -n <deployment-name> --jar-path <path-to-built-jar>
```

### <a name="deploy-from-source-code"></a>Nasazení ze zdrojového kódu

Azure Spring Cloud používá [kvytvoření kpacku](https://github.com/pivotal/kpack) k vytvoření projektu.  Pomocí azure cli můžete nahrát zdrojový kód, vytvořit projekt pomocí kpack a nasadit jej do cílové aplikace.

> [!WARNING]
> Projekt musí vytvořit pouze jeden `main-class` soubor JAR `MANIFEST.MF` `target` se vstupem v (pro nasazení Maven nebo `build/libs` (pro nasazení Gradle).  Více souborů `main-class` JAR s položkami způsobí selhání nasazení.

Pro jednomodulové projekty Maven / Gradle:

```azurecli
cd <path-to-maven-or-gradle-source-root>
az spring-cloud app deploy -n <app-name>
```

U projektů Maven / Gradle s více moduly opakujte pro každý modul:

```azurecli
cd <path-to-maven-or-gradle-source-root>
az spring-cloud app deploy -n <app-name> --target-module <relative-path-to-module>
```

### <a name="show-deployment-logs"></a>Zobrazit protokoly nasazení

Zkontrolujte protokoly sestavení kpack pomocí následujícího příkazu:

```azurecli
az spring-cloud app show-deploy-log -n <app-name> [-d <deployment-name>]
```

> [!NOTE]
> Protokoly kpack zobrazí pouze nejnovější nasazení, pokud toto nasazení bylo vytvořeno ze zdroje pomocí kpack.

> [!div class="nextstepaction"]
> [Narazil(a) jsem na problém](https://www.research.net/r/javae2e?tutorial=asc-source-quickstart&step=deploy)

## <a name="assign-a-public-endpoint-to-gateway"></a>Přiřazení veřejného koncového bodu k bráně

1. Otevřete stránku **Řídicí panel aplikace.**
2. Vyberte `gateway` aplikaci, chcete-li zobrazit stránku **Podrobnosti o aplikaci.**
3. Vyberte **Přiřadit doménu,** chcete-li bráně přiřadit veřejný koncový bod. To může několik minut. 
4. Zadejte přiřazenou veřejnou IP adresu do svého prohlížeče a zobrazte si spuštěnou aplikaci.

> [!div class="nextstepaction"]
> [Narazil(a) jsem na problém](https://www.research.net/r/javae2e?tutorial=asc-source-quickstart&step=public-endpoint)

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se naučili:

> [!div class="checklist"]
> * Zřízení instance služby
> * Nastavení konfiguračního serveru pro instanci
> * Místní vytvoření aplikace mikroslužeb
> * Nasazení jednotlivých mikroslužeb
> * Úprava proměnných prostředí pro aplikace
> * Přiřazení veřejné IP adresy pro vaši aplikační bránu

> [!div class="nextstepaction"]
> [Příprava aplikace Azure Spring Cloud pro nasazení](spring-cloud-tutorial-prepare-app-deployment.md)

Další ukázky jsou k dispozici na [GitHubu: Ukázky Azure Spring Cloud](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql).
