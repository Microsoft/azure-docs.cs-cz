---
title: 'Rychlý Start: spuštění vaší jarní cloudové aplikace ze zdrojového kódu'
description: Naučte se, jak spustit cloudovou aplikaci Azure pro jaře přímo ze zdrojového kódu.
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 9/27/2019
ms.author: jeconnoc
ms.openlocfilehash: 445cac1494828362d54a8c15e68d27f01b165841
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2019
ms.locfileid: "72170535"
---
# <a name="launch-your-spring-cloud-application-from-source-code"></a>Spustit svou jarní cloudovou aplikaci ze zdrojového kódu

Jarní cloud Azure umožňuje spustit aplikaci přímo ze zdrojového kódu Java nebo z předem připraveného JAR. Tento článek vás provede potřebnými kroky.

## <a name="prerequisites"></a>Předpoklady

>[!Note]
> Před zahájením tohoto rychlého startu se ujistěte, že vaše předplatné Azure má přístup k jarnímu cloudu Azure.  Jako služba ve verzi Preview se na nás zeptáme, abychom vám mohli přidat vaše předplatné do seznamu povolených adres.  Pokud chcete prozkoumat možnosti Azure jarního cloudu, [vyplňte prosím tento formulář](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR-LA2geqX-ZLhi-Ado1LD3tUNDk2VFpGUzYwVEJNVkhLRlcwNkZFUFZEUS4u
).

Než začnete, ujistěte se, že vaše předplatné Azure má požadované závislosti:

1. [Nainstalovat Git](https://git-scm.com/).
2. [Nainstalovat JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
3. [Nainstalujte Maven 3,0 nebo novější.](https://maven.apache.org/download.cgi)
4. [Instalace rozhraní příkazového řádku Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
5. [Registrace předplatného Azure](https://azure.microsoft.com/free/)

> [!TIP]
> Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku.  Má předinstalované běžné nástroje Azure, včetně nejnovějších verzí Git, JDK, Maven a Azure CLI. Pokud jste přihlášeni ke svému předplatnému Azure, spusťte [Azure Cloud Shell](https://shell.azure.com) z Shell.Azure.com.  Další informace o Azure Cloud Shell najdete v [naší dokumentaci](../cloud-shell/overview.md) .

## <a name="install-the-azure-cli-extension"></a>Instalace rozšíření Azure CLI

Pomocí následujícího příkazu nainstalujte rozšíření Azure jaře Cloud pro rozhraní příkazového řádku Azure.

```Azure CLI
az extension add -y --source https://azureclitemp.blob.core.windows.net/spring-cloud/spring_cloud-0.1.0-py2.py3-none-any.whl
```

## <a name="provision-a-service-instance-using-the-azure-cli"></a>Zřízení instance služby pomocí Azure CLI

Přihlaste se k Azure CLI a vyberte své aktivní předplatné. Nezapomeňte zvolit aktivní předplatné, které je na seznamu povolených pro Azure jaře Cloud.

```Azure CLI
az login
az account list -o table
az account set --subscription
```

Otevřete okno Azure CLI a spusťte následující příkazy, abyste zřídili instanci Azure Pramenitého cloudu. Všimněte si, že vám také řekne Azure jarní Cloud, který tady přiřadí veřejnou doménu.

```azurecli
    az spring-cloud create -n <resource name> -g <resource group name> --is-public true
```

Nasazení instance služby bude trvat přibližně pět minut.

Pomocí následujících příkazů nastavte výchozí název skupiny prostředků a název clusteru:

```azurecli
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
```

## <a name="create-the-spring-cloud-application"></a>Vytvoření aplikace pro jarní Cloud

Následující příkaz vytvoří ve svém předplatném novou jarní cloudovou aplikaci.  Tím se vytvoří prázdná jarní cloudová služba, do které můžeme nahrát aplikaci.

```azurecli
az spring-cloud app create -n <app-name>
```

## <a name="deploy-your-spring-cloud-application"></a>Nasazení vaší jarní cloudové aplikace

Aplikaci můžete nasadit z předem připraveného JAR nebo z úložiště Gradle nebo Maven.  Pokyny pro každý případ najdete níže.

### <a name="deploy-a-built-jar"></a>Nasazení postaveného JAR

Chcete-li nasadit z JAR postaveného na místním počítači, ujistěte se, že vaše sestavení vytváří [sklenici se systémem souborů](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-build.html#howto-create-an-executable-jar-with-maven).

Nasazení tukového JAR na aktivní nasazení

```azurecli
az spring-cloud app deploy -n <app-name> --jar-path <path-to-fat-JAR>
```

Nasazení tukového JAR do konkrétního nasazení

```azurecli
az spring-cloud app deployment create --app <app-name> -n <deployment-name> --jar-path <path-to-built-jar>
```

### <a name="deploy-from-source-code"></a>Nasazení ze zdrojového kódu

Azure jaře Cloud používá [kpack](https://github.com/pivotal/kpack) k sestavení projektu.  Pomocí Azure CLI můžete nahrát svůj zdrojový kód, sestavit projekt pomocí kpack a nasadit ho do cílové aplikace.

> [!WARNING]
> Projekt musí vytvořit pouze jeden soubor JAR s položkou `main-class` v `MANIFEST.MF` v `target` (pro nasazení Maven nebo `build/libs` (pro Gradle nasazení).  Více souborů JAR s položkami `main-class` způsobí selhání nasazení.

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
2. Vyberte aplikaci `gateway` pro zobrazení stránky s **podrobnostmi o aplikaci** .
3. Vyberte **přiřadit doménu** a přiřaďte k bráně veřejný koncový bod. Může to pár minut trvat. 
4. Pokud chcete zobrazit spuštěnou aplikaci, zadejte přiřazenou veřejnou IP adresu do prohlížeče.

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
> [Příprava aplikace pro jarní cloudy Azure pro nasazení](spring-cloud-tutorial-prepare-app-deployment.md)
