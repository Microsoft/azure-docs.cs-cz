---
title: Vytvoření aplikace pro funkce Azure s Javou a Eclipse
description: Návod k vytvoření a publikování jednoduché aplikace bez serveru aktivované http pomocí Java a Eclipse do Azure Functions.
author: jeffhollan
ms.topic: conceptual
ms.date: 07/01/2018
ms.author: jehollan
ms.custom: mvc, devcenter
ms.openlocfilehash: c82432a3d76a84eba1ad921d936b2f3ba064e2ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79136829"
---
# <a name="create-your-first-function-with-java-and-eclipse"></a>Vytvořte si první funkci s Javou a Eclipse 

Tento článek ukazuje, jak vytvořit projekt funkce [bez serveru](https://azure.microsoft.com/solutions/serverless/) s Eclipse IDE a Apache Maven, otestovat a ladit a pak ho nasadit do Funkce Azure. 

<!-- TODO ![Access a Hello World function from the command line with cURL](media/functions-create-java-maven/hello-azure.png) -->

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-development-environment"></a>Nastavení vývojového prostředí

Chcete-li vyvinout aplikaci funkcí s Javou a Eclipse, musíte mít nainstalované následující:

-  [Java Developer Kit](https://www.azul.com/downloads/zulu/), verze 8.
-  [Apache Maven](https://maven.apache.org), verze 3.0 nebo vyšší.
-  [Eclipse](https://www.eclipse.org/downloads/packages/), s podporou Javy a Maven.
-  [Azure CLI](https://docs.microsoft.com/cli/azure)

> [!IMPORTANT] 
> Pro dokončení tohoto rychlého startu musí být proměnná prostředí JAVA_HOME nastavená na umístění instalace sady JDK.

Důrazně doporučujeme nainstalovat také [nástroje Azure Functions Core Tools, verze 2](functions-run-local.md#v2), které poskytují místní prostředí pro spouštění a ladění funkcí Azure. 

## <a name="create-a-functions-project"></a>Vytvoření projektu Functions

1. V části Eclipse vyberte nabídku **Soubor a** pak vyberte Nový projekt **-&gt; Maven**. 
1. Přijměte výchozí hodnoty v dialogu **Nový projekt Maven** a vyberte **možnost Další**.
1. Vyberte **Přidat archetyp** a přidejte položky pro [azurové funkce-archetype](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype).
    - ID skupiny archetype: com.microsoft.azure
    - Archetype Artefakt ID: azurové funkce-archetyp
    - Verze: Zkontrolujte a používejte nejnovější verzi z [centrálního úložiště](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype)
    ![Eclipse Maven vytvořit](media/functions-create-first-java-eclipse/functions-create-eclipse.png)  
1. Klepněte na tlačítko **OK** a potom na tlačítko **Další**.  Nezapomeňte vyplnit hodnoty pro všechna pole `resourceGroup`včetně `appName`, `appRegion` a (použijte jiný appName než **fabrikam-function-20170920120101928**) a nakonec **Dokončit**.
    ![Eclipse Maven create2](media/functions-create-first-java-eclipse/functions-create-eclipse2.png)  

Maven přesune soubory projektu do nové složky s názvem _artifactId_. Generovaný kód v projektu je jednoduchá funkce [spouštěná protokolem HTTP,](/azure/azure-functions/functions-bindings-http-webhook) která odráží text spouštěcího požadavku HTTP.

## <a name="run-functions-locally-in-the-ide"></a>Spustit funkce místně v prostředí IDE

> [!NOTE]
> [Nástroje Jádra funkcí Azure, verze 2](functions-run-local.md#v2) musí být nainstalována pro místní spuštění a ladění funkcí.

1. Klikněte pravým tlačítkem myši na vygenerovaný projekt a pak zvolte **Spustit jako** a **Maven sestavení**.
1. V dialogovém okně `package` Upravit **konfiguraci** zadejte do polí **Cíle** a **Název** a pak vyberte **Spustit**. Tím se vytvoří a zabalí kód funkce.
1. Po dokončení sestavení vytvořte jinou konfiguraci `azure-functions:run` spustit, jak je uvedeno výše, pomocí jako cíl a název. Chcete-li spustit funkci v ide, vyberte **spustit.**

Po dokončení testování funkce ukončete běhový čas v okně konzoly. Pouze jeden hostitel funkce může být aktivní a běží místně najednou.

### <a name="debug-the-function-in-eclipse"></a>Ladění funkce v Eclipse

V konfiguraci **Spustit jako** nastavenou v `azure-functions:run` `azure-functions:run -DenableDebug` předchozím kroku změňte a spusťte aktualizovanou konfiguraci a spusťte aplikaci funkce v režimu ladění.

Vyberte nabídku **Spustit** a otevřete **možnost Konfigurace ladění**. Zvolte **vzdálenou aplikaci Java** a vytvořte novou. Pojmenujte svou konfiguraci a vyplňte nastavení. Port by měl být konzistentní s ladicím portem `5005`otevřeným hostitelem funkce, což je ve výchozím nastavení . Po nastavení, `Debug` klikněte na začít ladění.

![Ladicí funkce v eclipse](media/functions-create-first-java-eclipse/debug-configuration-eclipse.PNG)

Nastavte zarážky a zkontrolujte objekty ve funkci pomocí ide. Po dokončení zastavte ladicí program a hostitele spuštěných funkcí. Pouze jeden hostitel funkce může být aktivní a běží místně najednou.

## <a name="deploy-the-function-to-azure"></a>Nasazení funkce do Azure

V procesu nasazení do služby Azure Functions se používají přihlašovací údaje účtu z Azure CLI. Před pokračováním v příkazovém řádku počítače se přihlaste pomocí příkazového řádku [Azure CLI.](/cli/azure/authenticate-azure-cli?view=azure-cli-latest)

```azurecli
az login
```

Nasaďte kód do nové aplikace `azure-functions:deploy` Function pomocí cíle Maven v nové konfiguraci **Spustit jako.**

Po dokončení nasazení se zobrazí adresa URL, pomocí které můžete přistupovat k vaší aplikaci funkcí Azure:

```output
[INFO] Successfully deployed Function App with package.
[INFO] Deleting deployment package from Azure Storage...
[INFO] Successfully deleted deployment package fabrikam-function-20170920120101928.20170920143621915.zip
[INFO] Successfully deployed Function App at https://fabrikam-function-20170920120101928.azurewebsites.net
[INFO] ------------------------------------------------------------------------
```

## <a name="next-steps"></a>Další kroky

- V [příručce pro vývojáře funkcí v Javě](functions-reference-java.md) najdete další informace o vývoji funkcí v Javě.
- Do svého projektu můžete přidat další funkce s jinými triggery s použitím cíle Maven `azure-functions:add`.
