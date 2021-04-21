---
title: Vytvoření prostředku Cognitive Services pomocí Azure CLI
titleSuffix: Azure Cognitive Services
description: Začněte s Azure Cognitive Services vytvořením a přihlášením k odběru prostředku pomocí rozhraní příkazového řádku Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
keywords: služby pro rozpoznávání, rozpoznávání, rozpoznávání, rozpoznávání, služby AI
ms.topic: quickstart
ms.date: 3/22/2021
ms.author: aahi
ms.openlocfilehash: 26e3b264b7268f7a9ffdb592beef7d76844646f5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789136"
---
# <a name="quickstart-create-a-cognitive-services-resource-using-the-azure-command-line-interfacecli"></a>Rychlý Start: vytvoření prostředku Cognitive Services pomocí rozhraní příkazového řádku Azure Command-Line (CLI)

V tomto rychlém startu můžete začít s Azure Cognitive Services pomocí [rozhraní příkazového řádku Azure (CLI)](/cli/azure/install-azure-cli).

Azure Cognitive Services jsou cloudové služby s rozhraními REST API a sady SDK klientské knihovny, které vývojářům umožňují vytvářet v aplikacích rozpoznávání dat bez nutnosti přímých dovedností (AI) nebo znalostí v oblasti datových věd. Azure Cognitive Services umožňuje vývojářům snadno přidat funkce rozpoznávání do svých aplikací s rozpoznáváním řešení, která mohou vidět, slyšet, mluvit, pochopit a dokonce i začít.

Cognitive Services jsou reprezentovány [prostředky](../azure-resource-manager/management/manage-resources-portal.md) Azure, které vytvoříte ve svém předplatném Azure. Po vytvoření prostředku použijte k ověření vašich aplikací klíče a koncový bod, který jste vygenerovali.

V tomto rychlém startu se dozvíte, jak se zaregistrovat k Azure Cognitive Services a vytvořit účet, který má předplatné s jednou nebo více službami, pomocí [rozhraní příkazového řádku Azure (CLI)](/cli/azure/install-azure-cli). Tyto služby jsou reprezentovány [prostředky](../azure-resource-manager/management/manage-resources-portal.md)Azure, které vám umožňují připojit se k jednomu nebo více rozhraní API služeb Cognitive Services Azure.

[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="prerequisites"></a>Požadavky

* Platné předplatné Azure – [Vytvořte si ho](https://azure.microsoft.com/free/cognitive-services) zdarma.
* [Rozhraní příkazového řádku Azure (CLI)](/cli/azure/install-azure-cli)

## <a name="install-the-azure-cli-and-sign-in"></a>Instalace rozhraní příkazového řádku Azure a přihlášení

Nainstalujte rozhraní příkazového [řádku Azure CLI](/cli/azure/install-azure-cli). K místní instalaci rozhraní příkazového řádku se přihlaste spuštěním příkazu [AZ Login](/cli/azure/reference-index#az_login) :

```azurecli-interactive
az login
```

Můžete také použít zelené tlačítko **vyzkoušet** ke spuštění těchto příkazů v prohlížeči.

## <a name="create-a-new-azure-cognitive-services-resource-group"></a>Vytvoří novou skupinu prostředků Azure Cognitive Services.

Před vytvořením prostředku Cognitive Services musíte mít skupinu prostředků Azure, která má prostředek obsahovat. Při vytváření nového prostředku máte možnost vytvořit novou skupinu prostředků, nebo použít existující. V tomto článku se dozvíte, jak vytvořit novou skupinu prostředků.

### <a name="choose-your-resource-group-location"></a>Zvolit umístění skupiny prostředků

K vytvoření prostředku budete potřebovat jedno z umístění Azure dostupného pro vaše předplatné. Seznam dostupných umístění můžete načíst pomocí příkazu [AZ Account list-Locations](/cli/azure/account#az_account_list_locations) . K většině Cognitive Services se dá dostat z několika míst. Vyberte tu, která je pro vás nejblíže, nebo zjistěte, která umístění jsou pro tuto službu k dispozici.

> [!IMPORTANT]
> * Zapamatujte si umístění Azure, jak ho budete potřebovat při volání Azure Cognitive Services.
> * Dostupnost některých Cognitive Services se může v jednotlivých oblastech lišit. Další informace najdete v tématu [produkty Azure podle oblasti](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services).

```azurecli-interactive
az account list-locations \
    --query "[].{Region:name}" \
    --out table
```

Po umístění Azure vytvořte novou skupinu prostředků v rozhraní příkazového řádku Azure pomocí příkazu [AZ Group Create](/cli/azure/group#az_group_create) .

V následujícím příkladu nahraďte umístění Azure `westus2` jedním z umístění Azure dostupných pro vaše předplatné.

```azurecli-interactive
az group create \
    --name cognitive-services-resource-group \
    --location westus2
```

## <a name="create-a-cognitive-services-resource"></a>Vytvoření prostředku služeb Cognitive Services

### <a name="choose-a-cognitive-service-and-pricing-tier"></a>Výběr služby pro rozpoznávání a cenové úrovně

Při vytváření nového prostředku budete muset znát "druh" služby, kterou chcete použít, spolu s [cenovou úrovní](https://azure.microsoft.com/pricing/details/cognitive-services/) (nebo SKU), kterou chcete. Při vytváření prostředku budete používat tuto a další informace jako parametry.

### <a name="multi-service"></a>Více služeb

| Služba                    | Druh                      |
|----------------------------|---------------------------|
| Několik služeb. Další podrobnosti najdete na stránce s [cenami](https://azure.microsoft.com/pricing/details/cognitive-services/) .            | `CognitiveServices`     |


> [!NOTE]
> Mnohé z Cognitive Services níže mají bezplatnou úroveň, kterou můžete použít k vyzkoušení služby. Pokud chcete používat úroveň Free, použijte `F0` jako SKU pro váš prostředek.

### <a name="vision"></a>Obraz

| Služba                    | Druh                      |
|----------------------------|---------------------------|
| Počítačové zpracování obrazu            | `ComputerVision`          |
| Custom Vision – předpověď | `CustomVision.Prediction` |
| Custom Vision – školení   | `CustomVision.Training`   |
| Tvář                       | `Face`                    |
| Rozpoznávání formulářů            | `FormRecognizer`          |
| Rozpoznávání rukopisu             | `InkRecognizer`           |

### <a name="speech"></a>Řeč

| Služba            | Druh                 |
|--------------------|----------------------|
| Služby Speech    | `SpeechServices`     |
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

Seznam dostupných druhů rozpoznávání získáte pomocí příkazu [AZ cognitiveservices Account Account list-druhu](/cli/azure/cognitiveservices/account#az_cognitiveservices_account_list_kinds) :

```azurecli-interactive
az cognitiveservices account list-kinds
```

### <a name="add-a-new-resource-to-your-resource-group"></a>Přidání nového prostředku do skupiny prostředků

Pokud chcete vytvořit nový prostředek Cognitive Services a přihlásit se k jeho odběru, použijte příkaz [AZ cognitiveservices Account Account Create](/cli/azure/cognitiveservices/account#az_cognitiveservices_account_create) . Tento příkaz přidá nový fakturovatelný prostředek do skupiny prostředků vytvořené dříve. Při vytváření nového prostředku budete potřebovat znát "druh" služby, kterou chcete použít, spolu s její cenovou úrovní (nebo SKU) a umístěním Azure:

Můžete vytvořit F0 (Free) prostředek pro detektor anomálií s názvem `anomaly-detector-resource` pomocí příkazu níže.

```azurecli-interactive
az cognitiveservices account create \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group \
    --kind AnomalyDetector \
    --sku F0 \
    --location westus2 \
    --yes
```

[!INCLUDE [Register Azure resource for subscription](./includes/register-resource-subscription.md)]

## <a name="get-the-keys-for-your-resource"></a>Získat klíče pro svůj prostředek

Pokud se chcete přihlásit k místní instalaci rozhraní příkazového řádku (CLI) Command-Line, použijte příkaz [AZ Login](/cli/azure/reference-index#az_login) .

```azurecli-interactive
az login
```

Pomocí příkazu [AZ cognitiveservices Account Account Keys list](/cli/azure/cognitiveservices/account/keys#az_cognitiveservices_account_keys_list) získáte klíče pro prostředek služby vnímání.

```azurecli-interactive
    az cognitiveservices account keys list \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group
```

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="pricing-tiers-and-billing"></a>Cenové úrovně a fakturace

Cenové úrovně (a množství, které se vám bude účtovat), vycházejí z počtu transakcí, které odešlete pomocí ověřovacích informací. Každá cenová úroveň určuje:
* maximální počet povolených transakcí za sekundu (TPS).
* funkce služby povolené v rámci cenové úrovně.
* Náklady na předdefinovaný počet transakcí. Po překročení této částky se bude účtovat další poplatek, který je uvedený v [podrobnostech o cenách](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) vaší služby.

## <a name="get-current-quota-usage-for-your-resource"></a>Získat aktuální využití kvóty pro váš prostředek

Pomocí příkazu [AZ cognitiveservices Account Account list-Usage](/cli/azure/cognitiveservices/account#az_cognitiveservices_account_list_usage) Získejte využití prostředku služby vnímání.

```azurecli-interactive
az cognitiveservices account list-usage \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group \
    --subscription subscription-name
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit a odebrat prostředek Cognitive Services, můžete ho odstranit nebo skupinu prostředků. Odstraněním skupiny prostředků dojde také k odstranění všech dalších prostředků obsažených ve skupině.

Pokud chcete odebrat skupinu prostředků a její přidružené prostředky, použijte příkaz AZ Group DELETE.

```azurecli-interactive
az group delete --name cognitive-services-resource-group
```

## <a name="see-also"></a>Viz také

* Informace o tom, jak bezpečně pracovat s Cognitive Services, najdete v tématu **[ověření požadavků do Azure Cognitive Services](authentication.md)** .
* V tématu **[co je Azure Cognitive Services?](./what-are-cognitive-services.md)** získáte seznam různých kategorií v rámci Cognitive Services.
* Seznam přirozených jazyků, které Cognitive Services podporuje, najdete v článku **[Podpora přirozeného jazyka](language-support.md)** .
* Pokud chcete pochopit, jak používat Cognitive Services on-Prem, přečtěte si téma **[použití Cognitive Services jako kontejnerů](cognitive-services-container-support.md)** .
* Odhad nákladů na používání Cognitive Services najdete v tématu **[plánování a Správa nákladů na Cognitive Services](plan-manage-costs.md)** .
