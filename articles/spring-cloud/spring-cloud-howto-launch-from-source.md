---
title: Jak spustit svou jarní cloudovou aplikaci ze zdrojového kódu
description: V tomto rychlém startu se dozvíte, jak spustit cloudovou aplikaci Azure jaře přímo ze zdrojového kódu.
author: MikeDodaro
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 09/03/2020
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: ee64343a040f4ed3288f8c4addb64c1ef2437cc1
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91326180"
---
# <a name="how-to-launch-your-spring-cloud-application-from-source-code"></a>Jak spustit svou jarní cloudovou aplikaci ze zdrojového kódu

**Tento článek se týká:** ✔️ Java

Jarní cloud Azure umožňuje aplikacím mikroslužeb založeným na mikroslužbách v Azure na jaře.

Můžete spouštět aplikace přímo ze zdrojového kódu Java nebo z předem připraveného JAR. Tento článek vysvětluje postupy nasazení.

V tomto rychlém startu se dozvíte, jak:

> [!div class="checklist"]
> * Zřízení instance služby
> * Nastavení konfiguračního serveru pro instanci
> * Místní sestavení aplikace mikroslužeb
> * Nasazení jednotlivých mikroslužeb
> * Přiřazení veřejného koncového bodu vaší aplikaci

## <a name="prerequisites"></a>Předpoklady
Než začnete, ujistěte se, že vaše předplatné Azure má požadované závislosti:

1. [Nainstalovat Git](https://git-scm.com/).
2. [Nainstalovat JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
3. [Nainstalujte Maven 3,0 nebo novější.](https://maven.apache.org/download.cgi)
4. [Instalace Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
5. [Registrace předplatného Azure](https://azure.microsoft.com/free/)

> [!TIP]
> Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku.  Má předinstalované běžné nástroje Azure, včetně nejnovějších verzí Git, JDK, Maven a Azure CLI. Pokud jste přihlášeni ke svému předplatnému Azure, spusťte [Azure Cloud Shell](https://shell.azure.com) z Shell.Azure.com.  Další informace o Azure Cloud Shell najdete v [naší dokumentaci](../cloud-shell/overview.md) .

## <a name="install-the-azure-cli-extension"></a>Instalace rozšíření Azure CLI

Pomocí následujícího příkazu nainstalujte rozšíření Azure jaře Cloud pro rozhraní příkazového řádku Azure.

```azurecli
az extension add --name spring-cloud
```

## <a name="provision-a-service-instance-using-the-azure-cli"></a>Zřízení instance služby pomocí Azure CLI

Přihlaste se k Azure CLI a vyberte své aktivní předplatné. 

```azurecli
az login
az account list -o table
az account set --subscription
```

Vytvořte skupinu prostředků, která bude obsahovat službu pro jarní cloudovou službu Azure. Můžete se dozvědět víc o [skupinách prostředků Azure](../azure-resource-manager/management/overview.md).

```azurecli
az group create --location eastus --name <resource group name>
```

Spuštěním následujících příkazů zřídíte instanci služby Azure jaře Cloud. Připravte si název služby pro jarní cloudovou službu Azure. Název musí mít 4 až 32 znaků a může obsahovat jenom malá písmena, číslice a spojovníky. První znak názvu služby musí být písmeno a poslední znak musí být písmeno nebo číslo.

```azurecli
az spring-cloud create -n <resource name> -g <resource group name>
```

Nasazení instance služby bude trvat přibližně pět minut.

Pomocí následujících příkazů nastavte výchozí název skupiny prostředků a název instance Azure jaře cloudu:

```azurecli
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
```

## <a name="create-the-azure-spring-cloud-application"></a>Vytvoření cloudové aplikace Azure pro jaře

Následující příkaz vytvoří ve svém předplatném cloudovou aplikaci Azure ve formě pružiny.  Tím se vytvoří prázdná služba, na kterou můžeme nahrát aplikaci.

```azurecli
az spring-cloud app create -n <app-name>
```

## <a name="deploy-your-spring-cloud-application"></a>Nasazení vaší jarní cloudové aplikace

Aplikaci můžete nasadit z předem připraveného JAR nebo z úložiště Gradle nebo Maven.  Pokyny pro každý případ najdete níže.

### <a name="deploy-a-built-jar"></a>Nasazení postaveného JAR

Chcete-li nasadit z JAR postaveného na místním počítači, ujistěte se, že vaše sestavení vytváří [sklenici se systémem souborů](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-build.html#howto-create-an-executable-jar-with-maven).

Nasazení tukového JAR na aktivní nasazení

```azurecli
az spring-cloud app deploy -n <app-name> --jar-path <path-to-fat-JAR e.g. "target\hellospring-0.0.1-SNAPSHOT.jar">
```

Nasazení tukového JAR do konkrétního nasazení

```azurecli
az spring-cloud app deployment create --app <app-name> -n <deployment-name> --jar-path <path-to-fat-JAR e.g. "target\hellospring-0.0.1-SNAPSHOT.jar">
```

### <a name="deploy-from-source-code"></a>Nasazení ze zdrojového kódu

Azure jaře Cloud používá [kpack](https://github.com/pivotal/kpack) k sestavení projektu.  Pomocí Azure CLI můžete nahrát svůj zdrojový kód, sestavit projekt pomocí kpack a nasadit ho do cílové aplikace.

> [!WARNING]
> Projekt musí vytvořit pouze jeden soubor JAR s `main-class` položkou v `MANIFEST.MF` `target` (pro nasazení Maven) nebo `build/libs` (pro Gradle nasazení).  Více souborů JAR s `main-class` položkami způsobí selhání nasazení.

Pro projekty Maven/Gradle s jedním modulem:

```azurecli
cd <path-to-maven-or-gradle-source-root>
az spring-cloud app deploy -n <app-name>
```

Pro projekty Maven/Gradle s několika moduly opakujte akci pro každý modul:

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
> V protokolech kpack se zobrazí jenom nejnovější nasazení, pokud se toto nasazení sestavilo ze zdroje pomocí kpack.

## <a name="assign-a-public-endpoint-to-gateway"></a>Přiřazení veřejného koncového bodu k bráně

1. Otevřete stránku **řídicího panelu aplikace** .
2. Vyberte `gateway` aplikaci, aby se zobrazila stránka s **podrobnostmi o aplikaci** .
3. Vyberte **přiřadit koncový bod** pro přiřazení veřejného koncového bodu k bráně. Může to trvat několik minut. 
4. Pokud chcete zobrazit spuštěnou aplikaci, zadejte přiřazenou veřejnou IP adresu do prohlížeče.

> [!div class="nextstepaction"]
> [Narazil(a) jsem na problém](https://www.research.net/r/javae2e?tutorial=asc-source-quickstart&step=public-endpoint)

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se naučili:

> [!div class="checklist"]
> * Zřízení instance služby
> * Nastavení konfiguračního serveru pro instanci
> * Místní sestavení aplikace mikroslužeb
> * Nasazení jednotlivých mikroslužeb
> * Úprava proměnných prostředí pro aplikace
> * Přiřazení veřejné IP adresy vaší aplikační bráně

> [!div class="nextstepaction"]
> [Jarní cloudové protokoly, metriky, trasování](spring-cloud-quickstart-logs-metrics-tracing.md)

Další ukázky jsou k dispozici na GitHubu: [ukázky Azure pro jarní Cloud](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql).
