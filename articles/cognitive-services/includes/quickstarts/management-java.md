---
title: 'Rychlý Start: Klientská knihovna pro správu Azure pro Java'
description: V tomto rychlém startu Začínáme s klientskou knihovnou pro správu Azure pro Java.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/01/2020
ms.author: pafarley
ms.openlocfilehash: 346854d5990ac6861bd4eb93914bb1745b90bfa5
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "89321550"
---
[Referenční dokumentace](https://docs.microsoft.com/java/api/com.microsoft.azure.management.cognitiveservices?view=azure-java-stable)  |  [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cognitiveservices/mgmt-v2017_04_18/src/main/java/com/microsoft/azure/management/cognitiveservices/v2017_04_18)  |  [Balíček (Maven)](https://mvnrepository.com/artifact/com.microsoft.azure/azure-mgmt-cognitiveservices)

## <a name="prerequisites"></a>Předpoklady

* Platné předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/).
* Aktuální verze sady [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Nástroj Gradle Build](https://gradle.org/install/)nebo jiný správce závislostí.


[!INCLUDE [Create a service principal](./create-service-principal.md)]

[!INCLUDE [Create a resource group](./create-resource-group.md)]

## <a name="create-a-new-java-application"></a>Vytvoření nové aplikace Java

V okně konzoly (například cmd, PowerShell nebo bash) vytvořte nový adresář pro vaši aplikaci a přejděte na něj. 

```console
mkdir myapp && cd myapp
```

Spusťte `gradle init` příkaz z pracovního adresáře. Tento příkaz vytvoří základní soubory sestavení pro Gradle, včetně *Build. Gradle. kts* , který se používá za běhu k vytvoření a konfiguraci vaší aplikace.

```console
gradle init --type basic
```

Po zobrazení výzvy k výběru **DSL**vyberte **Kotlin**.

V pracovním adresáři spusťte následující příkaz:

```console
mkdir -p src/main/java
```

### <a name="install-the-client-library"></a>Instalace klientské knihovny

V tomto rychlém startu se používá správce závislostí Gradle. Knihovnu klienta a informace pro jiné správce závislostí najdete v [centrálním úložišti Maven](https://mvnrepository.com/artifact/com.azure/azure-ai-formrecognizer).

V souboru *Build. Gradle. kts* projektu přidejte klientskou knihovnu jako `implementation` příkaz spolu s požadovanými moduly plug-in a nastavením.

```kotlin
plugins {
    java
    application
}
application {
    mainClass.set("FormRecognizer")
}
repositories {
    mavenCentral()
}
dependencies {
    implementation(group = "com.microsoft.azure", name = "azure-mgmt-cognitiveservices", version = "1.10.0-beta")
}
```

### <a name="import-libraries"></a>Import knihoven

Přejděte do nové složky **Src/Main/Java** a vytvořte soubor s názvem *Management. Java*. Otevřete ho v preferovaném editoru nebo integrovaném vývojovém prostředí a přidejte následující `import` příkazy:

[!code-java[](~/cognitive-services-quickstart-code/java/azure_management_service/quickstart.java?name=snippet_imports)]

## <a name="authenticate-the-client"></a>Ověření klienta

Přidejte třídu v *Management. Java*a přidejte do ní následující pole a jejich hodnoty. Naplňte své hodnoty pomocí instančního objektu, který jste vytvořili, a dalších informací o účtu Azure.

[!code-java[](~/cognitive-services-quickstart-code/java/azure_management_service/quickstart.java?name=snippet_constants)]

Potom v metodě **Main** pomocí těchto hodnot Sestavte objekt **CognitiveServicesManager** . Tento objekt je potřebný pro všechny vaše operace správy Azure.

[!code-java[](~/cognitive-services-quickstart-code/java/azure_management_service/quickstart.java?name=snippet_auth)]

## <a name="call-management-methods"></a>Metody správy volání

Přidejte následující kód do metody **Main** pro vypsání dostupných prostředků, vytvořte ukázkový prostředek, uveďte seznam vlastněných prostředků a pak odstraňte ukázkový prostředek. Tyto metody definujete v dalších krocích.

[!code-java[](~/cognitive-services-quickstart-code/java/azure_management_service/quickstart.java?name=snippet_calls)]

## <a name="create-a-cognitive-services-resource"></a>Vytvoření prostředku služeb Cognitive Services

### <a name="choose-a-service-and-pricing-tier"></a>Výběr služby a cenové úrovně

Při vytváření nového prostředku budete muset znát "druh" služby, kterou chcete použít, spolu s [cenovou úrovní](https://azure.microsoft.com/pricing/details/cognitive-services/) (nebo SKU), kterou chcete. Tuto a další informace použijete jako parametry při vytváření prostředku. Seznam dostupného druhu služby pro rozpoznávání lze najít voláním následující metody:

[!code-java[](~/cognitive-services-quickstart-code/java/azure_management_service/quickstart.java?name=snippet_list_avail)]

[!INCLUDE [cognitive-services-subscription-types](../../../../includes/cognitive-services-subscription-types.md)]

[!INCLUDE [SKUs and pricing](./sku-pricing.md)]

## <a name="create-a-cognitive-services-resource"></a>Vytvoření prostředku služeb Cognitive Services

Pokud chcete vytvořit nový prostředek Cognitive Services a přihlásit se k jeho odběru, použijte metodu **Create** . Tato metoda přidá nový fakturovatelný prostředek do skupiny prostředků, kterou předáte. Při vytváření nového prostředku budete muset znát "druh" služby, kterou chcete použít, spolu s její cenovou úrovní (nebo SKU) a umístěním Azure. Následující metoda přebírá všechny tyto argumenty jako argumenty a vytvoří prostředek.

[!code-java[](~/cognitive-services-quickstart-code/java/azure_management_service/quickstart.java?name=snippet_create)]

## <a name="view-your-resources"></a>Zobrazení prostředků

Pokud chcete zobrazit všechny prostředky v rámci vašeho účtu Azure (napříč všemi skupinami prostředků), použijte tuto metodu:

[!code-java[](~/cognitive-services-quickstart-code/java/azure_management_service/quickstart.java?name=snippet_list)]

## <a name="delete-a-resource"></a>Odstranění prostředku

Následující metoda odstraní zadaný prostředek z dané skupiny prostředků.

[!code-java[](~/cognitive-services-quickstart-code/java/azure_management_service/quickstart.java?name=snippet_delete)]

## <a name="see-also"></a>Viz také

* [Referenční dokumentace k sadě Azure Management SDK](https://docs.microsoft.com/java/api/com.microsoft.azure.management.cognitiveservices?view=azure-java-stable)
* [Co je Azure Cognitive Services?](../../Welcome.md)
* [Ověřování požadavků do Azure Cognitive Services](../../authentication.md)
* [Vytvoření nového prostředku pomocí webu Azure Portal](../../cognitive-services-apis-create-account.md)