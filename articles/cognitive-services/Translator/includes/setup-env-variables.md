---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: c737447c3a3bd2d76d3ed620b7c61aaa81250130
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "70393918"
---
## <a name="set-up"></a>Nastavit

### <a name="create-a-translator-text-resource"></a>Vytvoření prostředku překladače textu

Azure Cognitive Services jsou reprezentované prostředky Azure, které si předplatíte. Vytvořte prostředek pro překladač text pomocí [portálu Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) nebo [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) na místním počítači. Můžete také:

* Získejte [zkušební klíč](https://azure.microsoft.com/try/cognitive-services) platný po dobu 7 dnů zdarma. Po registraci bude dostupná na webu Azure.
* Zobrazení existujícího prostředku na [webu Azure Portal](https://portal.azure.com/).

Po získání klíče z zkušebního předplatného nebo prostředku vytvořte dvě [proměnné prostředí](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication):

* `TRANSLATOR_TEXT_SUBSCRIPTION_KEY`- Klíč předplatného pro váš prostředek překladače textu.
* `TRANSLATOR_TEXT_ENDPOINT`- Globální koncový bod pro text překladače. Použijte `https://api.cognitive.microsofttranslator.com/`.
