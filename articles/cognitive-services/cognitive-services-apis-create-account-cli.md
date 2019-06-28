---
title: Vytvoření účtu služeb Cognitive Services pomocí rozhraní příkazového řádku Azure
titlesuffix: Azure Cognitive Services
description: Postup vytvoření účtu služby Azure API služeb Cognitive Services pomocí rozhraní příkazového řádku Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 06/26/2019
ms.author: aahi
ms.openlocfilehash: 26f7f3ab60347d9ec5f2a144410ad3de436f5b5c
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67454892"
---
# <a name="create-a-cognitive-services-account-using-the-azure-command-line-interfacecli"></a>Vytvoření účtu služeb Cognitive Services pomocí Interface(CLI) příkazového řádku Azure

V tomto rychlém startu se dozvíte o postupu zaregistrovat do služby Azure Cognitive Services a vytvoření účtu, který má předplatné služby jednoho nebo víc služeb, použije [Interface(CLI) příkazového řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Tyto služby jsou reprezentované prostřednictvím Azure [prostředky](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal), které umožňují připojení k jednomu nebo více rozhraní API kognitivních služeb Azure.

## <a name="prerequisites"></a>Požadavky

* Platné předplatné Azure. [Vytvoření účtu](https://azure.microsoft.com/free/) zdarma.
* [Interface(CLI) rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)

[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="install-the-azure-cli-and-sign-in"></a>Instalace rozhraní příkazového řádku Azure a přihlášení 

Nainstalujte [rozhraní příkazového řádku Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Chcete-li přihlásit k místní instalaci rozhraní příkazového řádku, spusťte [az login](https://docs.microsoft.com/cli/azure/reference-index#az-login) příkaz:

```console
az login
```

Můžete také použít zelený **vyzkoušet** tlačítko pro spuštění těchto příkazů v prohlížeči.
 
## <a name="create-a-new-azure-cognitive-services-resource-group"></a>Vytvořit novou skupinu prostředků Azure Cognitive Services

Vaše předplatné služeb Cognitive Services jsou reprezentovány prostředků Azure. Každý účet služeb Cognitive Services (a jeho přidružený prostředek Azure), musí patřit do skupiny prostředků Azure.

### <a name="choose-your-resource-group-location"></a>Zvolte umístění skupiny prostředků

Vytvoření prostředku, budete potřebovat jeden z umístění Azure, které je k dispozici pro vaše předplatné. Můžete načíst seznam dostupných umístění s [az účet list-locations](/cli/azure/account#az_account_list) příkazu. Většina služeb Cognitive Services je možný z několika míst. Vyberte si ten, který je nejblíže k vám nebo zjistit, která umístění jsou dostupné pro službu.

> [!IMPORTANT]
> * Pamatovat si vaše oblast Azure, protože ji budete potřebovat při volání služeb Azure Cognitive Services.
> * Dostupnost některých služeb Cognitive Services se může lišit podle oblasti. Další informace najdete v tématu [produkty Azure podle oblastí](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services).  

```azurecli-interactive
az account list-locations \
    --query "[].{Region:name}" \
    --out table
```

Až budete mít vaše umístění azure, vytvořte novou skupinu prostředků pomocí Azure CLI [vytvořit skupiny az](/cli/azure/group#az_group_create) příkazu.

V následujícím příkladu nahraďte umístění azure `westus2` s jedním z umístění Azure, které je k dispozici pro vaše předplatné.

```azurecli-interactive
az group create \
    --name cognitive-services-resource-group \
    --location westus2
```

## <a name="create-a-cognitive-services-resource"></a>Vytvoření prostředku služeb Cognitive Services

### <a name="choose-a-cognitive-service-and-pricing-tier"></a>Zvolte cenovou úroveň a služby cognitive Services

Při vytváření nového prostředku, musíte znát "typ" služby, kterou chcete použít spolu s [cenovou úroveň](https://azure.microsoft.com/pricing/details/cognitive-services/) (nebo skladové položky) chcete. Tato a další informace bude používat jako parametry při vytváření prostředku.

> [!NOTE]
> Mnoho služeb Cognitive services mít úroveň free, která vám pomůže službu si můžete vyzkoušet. Pokud chcete použít na úrovni free, použijte `F0` jako sku pro prostředek.

### <a name="vision"></a>Obraz

| Služba                    | Typ                      |
|----------------------------|---------------------------|
| Počítačové zpracování obrazu            | `ComputerVision`          |
| Custom Vision – predikce | `CustomVision.Prediction` |
| Custom Vision – školení   | `CustomVision.Training`   |
| Rozhraní API pro rozpoznávání tváře                   | `Face`                    |
| Rozpoznávání formulářů            | `FormRecognizer`          |
| Rozpoznávání rukopisu             | `InkRecognizer`           |

### <a name="search"></a>Search

| Služba            | Typ                  |
|--------------------|-----------------------|
| Automatické návrhy Bingu   | `Bing.Autosuggest.v7` |
| Vlastní vyhledávání Bingu | `Bing.CustomSearch`   |
| Vyhledávání entit Bingu | `Bing.EntitySearch`   |
| Vyhledávání pomocí služby Bing        | `Bing.Search.v7`      |
| Kontrola pravopisu Bingu   | `Bing.SpellCheck.v7`  |

### <a name="speech"></a>Řeč

| Služba            | Typ                 |
|--------------------|----------------------|
| Hlasové služby    | `SpeechServices`     |
| Rozpoznávání řeči | `SpeakerRecognition` |

### <a name="language"></a>Jazyk

| Služba            | Typ                |
|--------------------|---------------------|
| Principy formuláře | `FormUnderstanding` |
| LUIS               | `LUIS`              |
| QnA Maker          | `QnAMaker`          |
| Analýza textu     | `TextAnalytics`     |
| Překlad textu   | `TextTranslation`   |

### <a name="decision"></a>Rozhodnutí

| Služba           | Typ               |
|-------------------|--------------------|
| Detektor anomálií  | `AnomalyDetector`  |
| Content Moderator | `ContentModerator` |
| Personalizace      | `Personalizer`     |

Můžete najít seznam dostupných služby Cognitive Services "typy" s [az cognitiveservices account list typy](https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-list-kinds) příkaz:

```azurecli-interactive
az cognitiveservices account list-kinds
```

### <a name="add-a-new-resource-to-your-resource-group"></a>Přidat nový prostředek do vaší skupiny prostředků

Chcete-li vytvořit a odběru na nový prostředek služeb Cognitive Services, použijte [vytvoření účtu cognitiveservices az](https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-create) příkazu. Tento příkaz přidá nový prostředek fakturovatelné ke skupině prostředků vytvořili dříve. Při vytváření nového prostředku, budete muset znát "druh" service, kterou chcete použít společně s jeho cenovou úroveň (nebo skladové položky) a umístění služby Azure:

Prostředek F0 (zdarma) můžete vytvořit pro detekce anomálií, s názvem `anomaly-detector-resource` pomocí následujícího příkazu.

```azurecli-interactive
az cognitiveservices account create \
    --name anomaly-detector-resource \
    --group cognitive-services-resource-group \
    --kind AnomalyDetector \
    --sku F0 \
    --location westus2 \
    --yes
```

## <a name="get-the-keys-for-your-subscription"></a>Získání klíčů předplatného

Přihlaste se k místní instalaci Interface(CLI) příkazového řádku, použijte [az login](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-login) příkazu.

```console
az login
```

Použití [az cognitiveservices account kláves seznamu](https://docs.microsoft.com/cli/azure/cognitiveservices/account/keys?view=azure-cli-latest#az-cognitiveservices-account-keys-list) příkazu získat klíče pro váš prostředek služby Cognitive Services.

```azurecli-interactive
    az cognitiveservices account keys list \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group
```

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit a odebrat předplatné služeb Cognitive Services, můžete odstranit prostředek nebo skupinu prostředků. Odstraněním skupiny prostředků se odstraní také všechny další prostředky spojené s vybranou skupinou prostředků.

Chcete-li odebrat skupinu prostředků a její přidružené prostředky, včetně nového účtu úložiště, použijte příkaz az group delete.

```azurecli-interactive
az group delete --name storage-resource-group
```

## <a name="see-also"></a>Další informace najdete v tématech

* [Ověření požadavků ve službě Azure Cognitive Services](authentication.md)
* [Co je Azure Cognitive Services?](Welcome.md)
* [Podpora přirozeného jazyka](language-support.md)
* [Podpora kontejnerů dockeru](cognitive-services-container-support.md)
