---
title: Vytvoření aplikace funkcí Azure pomocí Java a zatmění
description: Průvodce vytvořením a publikováním jednoduché aplikace bez serveru s protokolem HTTP, která se spouští pomocí jazyka Java a zatmění pro Azure Functions.
author: jeffhollan
ms.topic: how-to
ms.date: 07/01/2018
ms.author: jehollan
ms.custom: mvc, devcenter, devx-track-java
ms.openlocfilehash: 7dd881d130b9df19335ac64be501553af99d58d8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102179540"
---
# <a name="create-your-first-function-with-java-and-eclipse"></a>Vytvoření první funkce pomocí Java a zatmění 

V tomto článku se dozvíte, jak vytvořit projekt funkce bez [serveru](https://azure.microsoft.com/solutions/serverless/) s využitím integrovaného vývojového prostředí (IDE) a Apache Maven, otestovat a ladit ho a pak ho nasadit do Azure Functions. 

<!-- TODO ![Access a Hello World function from the command line with cURL](media/functions-create-java-maven/hello-azure.png) -->

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-development-environment"></a>Nastavíte vývojové prostředí

K vývoji aplikace Functions pomocí Java a zatmění je nutné mít nainstalované následující:

-  [Java Developer Kit](https://www.azul.com/downloads/zulu/), verze 8.
-  [Apache Maven](https://maven.apache.org)verze 3,0 nebo vyšší.
-  [Zatmění](https://www.eclipse.org/downloads/packages/)s podporou Java a Maven.
-  [Azure CLI](/cli/azure)

> [!IMPORTANT] 
> Pro dokončení tohoto rychlého startu musí být proměnná prostředí JAVA_HOME nastavená na umístění instalace sady JDK.

Doporučujeme také nainstalovat [Azure Functions Core Tools, verzi 2](functions-run-local.md#v2), která poskytuje místní prostředí pro spouštění a ladění Azure Functions. 

## <a name="create-a-functions-project"></a>Vytvoření projektu Functions

1. V části zatmění vyberte nabídku **soubor** a pak vyberte **projekt New- &gt; Maven**. 
1. Přijměte výchozí hodnoty v dialogovém okně **Nový projekt Maven** a vyberte **Další**.
1. Vyhledejte a vyberte [Azure-Functions-Archetype](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype) a klikněte na **Další**.
1. Nezapomeňte vyplnit hodnoty pro všechna pole `resourceGroup` , včetně, `appName` a `appRegion` (použijte jiný název_aplikace kromě **fabrikam-Function-20170920120101928**) a nakonec **dokončete**.
    ![Create2 Maven pro zatmění](media/functions-create-first-java-eclipse/functions-create-eclipse2.png)  

Maven přesune soubory projektu do nové složky s názvem _artifactId_. Generovaný kód v projektu je jednoduchá funkce [aktivovaná protokolem HTTP](./functions-bindings-http-webhook.md) , která vypisuje tělo triggeru požadavku HTTP.

## <a name="run-functions-locally-in-the-ide"></a>Místní spuštění funkcí v integrovaném vývojovém prostředí

> [!NOTE]
> Azure Functions Core Tools musí být nainstalována [verze 2](functions-run-local.md#v2) pro místní spouštění a ladění funkcí.

1. Klikněte pravým tlačítkem na vygenerovaný projekt a pak zvolte **Spustit jako** a **Maven sestavení**.
1. V dialogovém okně **Upravit konfiguraci** zadejte `package` do polí **cíle** a **název** a pak vyberte **Spustit**. Tím se sestaví a zabalí kód funkce.
1. Po dokončení sestavení vytvořte další konfiguraci spuštění výše pomocí `azure-functions:run` jako cíl a název. Vyberte **Spustit** a spusťte funkci v integrovaném vývojovém prostředí (IDE).

Až skončíte s testováním funkce, ukončete modul runtime v okně konzoly. Pouze jeden hostitel funkce může být aktivní a spuštěn místně v jednom okamžiku.

### <a name="debug-the-function-in-eclipse"></a>Ladění funkce v zatmění

V nastavení konfigurace **Spustit jako** v předchozím kroku změňte `azure-functions:run` na `azure-functions:run -DenableDebug` a spusťte aktualizovanou konfiguraci, aby se spustila aplikace Function App v režimu ladění.

Vyberte nabídku **Spustit** a otevřete **Konfigurace ladění**. Vyberte možnost **Vzdálená aplikace Java** a vytvořte novou. Zadejte název vaší konfigurace a vyplňte nastavení. Port by měl být konzistentní s portem ladění otevřeným hostitelem funkce, který je ve výchozím nastavení `5005` . Po instalaci kliknutím na zapnout `Debug` Spusťte ladění.

![Ladění funkcí v zatmění](media/functions-create-first-java-eclipse/debug-configuration-eclipse.PNG)

Nastavte zarážky a prozkoumejte objekty ve funkci pomocí rozhraní IDE. Po dokončení ukončete ladicí program a běžícího hostitele funkcí. Pouze jeden hostitel funkce může být aktivní a spuštěn místně v jednom okamžiku.

## <a name="deploy-the-function-to-azure"></a>Nasazení funkce do Azure

V procesu nasazení do služby Azure Functions se používají přihlašovací údaje účtu z Azure CLI. Před pokračováním v používání příkazového řádku počítače [se přihlaste pomocí Azure CLI](/cli/azure/authenticate-azure-cli) .

```azurecli
az login
```

Nasaďte svůj kód do nové aplikace Function App pomocí `azure-functions:deploy` cíle Maven v nové konfiguraci **Spustit jako** .

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
