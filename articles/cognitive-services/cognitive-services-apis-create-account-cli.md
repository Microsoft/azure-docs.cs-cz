---
title: Vytvoření prostředku služeb Cognitive Services pomocí příkazového příkazového příkazu Azure
titleSuffix: Azure Cognitive Services
description: Můžete začít s Azure Cognitive Services vytvořením a přihlášením se k odběru prostředku pomocí rozhraní příkazového řádku Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 10/04/2019
ms.author: aahi
ms.openlocfilehash: 72b00d78d19ed0e963b4dad01b82033c659e1efd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219607"
---
# <a name="create-a-cognitive-services-resource-using-the-azure-command-line-interfacecli"></a>Vytvoření prostředku služeb Cognitive Services pomocí rozhraní Příkazového řádku Azure (CLI)

Tento rychlý start můžete začít používat služby Azure Cognitive Services pomocí [rozhraní CLI (Azure Command Line Interface).](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) Služby Cognitive Services jsou reprezentované [prostředky](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal) Azure, které vytvoříte ve svém předplatném Azure. Po vytvoření prostředku použijte klíče a koncový bod generovaný k ověření aplikací. 


V tomto rychlém startu se dozvíte, jak se zaregistrovat do služby Azure Cognitive Services a vytvořit účet, který má předplatné s jednou službou nebo s více službami, pomocí [rozhraní Azure Command Line Interface (CLI)](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Tyto služby jsou reprezentovány [prostředky](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal)Azure , které umožňují připojení k jedné nebo více virtuálních zařízení Azure Cognitive Services.

[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="prerequisites"></a>Požadavky

* Platné předplatné Azure – [vytvořte si ho](https://azure.microsoft.com/free/) zdarma.
* Rozhraní [příkazového řádku Azure (CLI)](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)

## <a name="install-the-azure-cli-and-sign-in"></a>Instalace příkazového příkazového příkazu k Azure a přihlášení 

Nainstalujte [rozhraní příkazového řádku Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Chcete-li se přihlásit k místní instalaci příkazu příkazu cli, spusťte příkaz [az login:](https://docs.microsoft.com/cli/azure/reference-index#az-login)

```azurecli-interactive
az login
```

Tyto příkazy můžete spustit také v prohlížeči pomocí zeleného tlačítka **Try It.**
 
## <a name="create-a-new-azure-cognitive-services-resource-group"></a>Vytvoření nové skupiny prostředků Azure Cognitive Services

Před vytvořením prostředku služeb Cognitive Services musíte mít skupinu prostředků Azure, která prostředek obsahuje. Při vytváření nového prostředku máte možnost buď vytvořit novou skupinu prostředků, nebo použít existující prostředek. Tento článek ukazuje, jak vytvořit novou skupinu prostředků.

### <a name="choose-your-resource-group-location"></a>Výběr umístění skupiny prostředků

K vytvoření prostředku budete potřebovat jedno z umístění Azure, které je k dispozici pro vaše předplatné. Pomocí příkazu [az seznam účtů](/cli/azure/account#az-account-list-locations) můžete načíst seznam dostupných umístění. Většina služeb Cognitive Services je přístupná z několika umístění. Vyberte si nejbližší, nebo zjistěte, která místa jsou pro službu dostupná.

> [!IMPORTANT]
> * Zapamatujte si svou polohu Azure, protože ji budete potřebovat při volání služby Azure Cognitive Services.
> * Dostupnost některých služeb Cognitive Services se může lišit podle oblasti. Další informace najdete v tématu [Produkty Azure podle oblasti](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services).  

```azurecli-interactive
az account list-locations \
    --query "[].{Region:name}" \
    --out table
```

Po nastavení azure vytvořte novou skupinu prostředků v azure cli pomocí příkazu [az group create.](/cli/azure/group#az-group-create)

V příkladu níže nahraďte umístění `westus2` azure jedním z umístění Azure, které je k dispozici pro vaše předplatné.

```azurecli-interactive
az group create \
    --name cognitive-services-resource-group \
    --location westus2
```

## <a name="create-a-cognitive-services-resource"></a>Vytvoření prostředku služeb Cognitive Services

### <a name="choose-a-cognitive-service-and-pricing-tier"></a>Výběr kognitivní ch služeb a cenové úrovně

Při vytváření nového prostředku budete potřebovat znát "druh" služby, kterou chcete použít, spolu s [cenovou úrovní](https://azure.microsoft.com/pricing/details/cognitive-services/) (nebo sku), kterou chcete použít. Tyto a další informace použijete jako parametry při vytváření prostředku.

### <a name="multi-service"></a>Víceslužebná služba

| Služba                    | Druh                      |
|----------------------------|---------------------------|
| Několik služeb. Další podrobnosti najdete na stránce [s cenami.](https://azure.microsoft.com/pricing/details/cognitive-services/)            | `CognitiveServices`     |


> [!NOTE]
> Mnoho služeb Cognitive Services níže má bezplatnou úroveň, kterou můžete použít k vyzkoušení služby. Chcete-li použít úroveň `F0` free, použijte jako sku pro váš prostředek.

### <a name="vision"></a>Obraz

| Služba                    | Druh                      |
|----------------------------|---------------------------|
| Počítačové zpracování obrazu            | `ComputerVision`          |
| Vlastní vize - Predikce | `CustomVision.Prediction` |
| Vlastní vize - školení   | `CustomVision.Training`   |
| Tvář                       | `Face`                    |
| Rozpoznávání formulářů            | `FormRecognizer`          |
| Rozpoznávání rukopisu             | `InkRecognizer`           |

### <a name="search"></a>Search

| Služba            | Druh                  |
|--------------------|-----------------------|
| Automatické návrhy Bingu   | `Bing.Autosuggest.v7` |
| Vlastní vyhledávání Bingu | `Bing.CustomSearch`   |
| Vyhledávání entit Bingu | `Bing.EntitySearch`   |
| Vyhledávání pomocí služby Bing        | `Bing.Search.v7`      |
| Kontrola pravopisu Bingu   | `Bing.SpellCheck.v7`  |

### <a name="speech"></a>Řeč

| Služba            | Druh                 |
|--------------------|----------------------|
| Hlasové služby    | `SpeechServices`     |
| Rozpoznávání řeči | `SpeakerRecognition` |

### <a name="language"></a>Jazyk

| Služba            | Druh                |
|--------------------|---------------------|
| Porozumění formuláři | `FormUnderstanding` |
| LUIS               | `LUIS`              |
| QnA Maker          | `QnAMaker`          |
| Analýza textu     | `TextAnalytics`     |
| Překlad textu   | `TextTranslation`   |

### <a name="decision"></a>Rozhodnutí

| Služba           | Druh               |
|-------------------|--------------------|
| Detektor anomálií  | `AnomalyDetector`  |
| Content Moderator | `ContentModerator` |
| Personalizace      | `Personalizer`     |

Seznam dostupných "druhů služby Cognitive Service" najdete pomocí příkazu [az cognitiveservices seznam typů účtů:](https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-list-kinds)

```azurecli-interactive
az cognitiveservices account list-kinds
```

### <a name="add-a-new-resource-to-your-resource-group"></a>Přidání nového prostředku do skupiny prostředků

Chcete-li vytvořit a přihlásit se k odběru nového prostředku služeb Cognitive Services, použijte příkaz [vytvořit účet az cognitiveservices.](https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-create) Tento příkaz přidá nový fakturovatelný prostředek do skupiny prostředků vytvořené dříve. Při vytváření nového prostředku budete potřebovat znát "druh" služby, kterou chcete použít, spolu s jeho cenovou úrovní (nebo sku) a umístěním Azure:

Můžete vytvořit F0 (free) prostředek pro detektor `anomaly-detector-resource` anomálií, pojmenovaný pomocí následujícího příkazu.

```azurecli-interactive
az cognitiveservices account create \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group \
    --kind AnomalyDetector \
    --sku F0 \
    --location westus2 \
    --yes
```

## <a name="get-the-keys-for-your-resource"></a>Získání klíčů pro váš zdroj

Chcete-li se přihlásit k místní instalaci rozhraní příkazového řádku (CLI), použijte příkaz [az login.](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-login)

```azurecli-interactive
az login
```

Pomocí příkazu [az cognitiveservices klíče klíče seznam](https://docs.microsoft.com/cli/azure/cognitiveservices/account/keys?view=azure-cli-latest#az-cognitiveservices-account-keys-list) získat klíče pro prostředek služby Cognitive Service.

```azurecli-interactive
    az cognitiveservices account keys list \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group
```

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="pricing-tiers-and-billing"></a>Cenové úrovně a fakturace

Cenové úrovně (a částka, která vám bude účtována) jsou založeny na počtu transakcí, které odesíláte pomocí ověřovacích informací. Každá cenová úroveň určuje:
* maximální počet povolených transakcí za sekundu (TPS).
* funkce služby povolené v rámci cenové úrovně.
* Náklady na předdefinovanou částku transakcí. Pokud převyšujete tuto částku, bude vám účtován příplatek, jak je uvedeno v [podrobnostech o cenách](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) vaší služby.

## <a name="get-current-quota-usage-for-your-resource"></a>Získání aktuálního využití kvóty pro váš prostředek

Pomocí příkazu [az cognitiveservices seznam seznamu a využití získat](https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-list-usage) využití pro prostředek služby Cognitive Service.

```azurecli-interactive
az cognitiveservices account list-usage \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group \
    --subscription subscription-name
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit a odebrat prostředek služeb Cognitive Services, můžete jej odstranit nebo skupinu prostředků. Odstraněním skupiny prostředků se odstraní také všechny ostatní prostředky obsažené ve skupině.

Chcete-li odebrat skupinu prostředků a její přidružené prostředky, použijte příkaz delete skupiny az.

```azurecli-interactive
az group delete --name cognitive-services-resource-group
```

## <a name="see-also"></a>Viz také

* [Ověřování požadavků na služby Azure Cognitive Services](authentication.md)
* [Co je Azure Cognitive Services?](Welcome.md)
* [Podpora přirozeného jazyka](language-support.md)
* [Podpora kontejnerů Dockeru](cognitive-services-container-support.md)
