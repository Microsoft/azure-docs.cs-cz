---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: c737447c3a3bd2d76d3ed620b7c61aaa81250130
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "70393918"
---
## <a name="set-up"></a>Nastavit

### <a name="create-a-translator-text-resource"></a>Vytvoření prostředku Translator Text

Azure Cognitive Services jsou představovány prostředky Azure, ke kterým jste se přihlásili. Vytvořte prostředek pro Translator Text pomocí [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) nebo rozhraní příkazového [řádku Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) na vašem místním počítači. Můžete také:

* Získejte [zkušební klíč](https://azure.microsoft.com/try/cognitive-services) platný po dobu 7 dnů zdarma. Po registraci bude k dispozici na webu Azure.
* Zobrazení existujícího prostředku v [Azure Portal](https://portal.azure.com/).

Po získání klíče ze zkušebního předplatného nebo prostředku vytvořte dvě [proměnné prostředí](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication):

* `TRANSLATOR_TEXT_SUBSCRIPTION_KEY`– Klíč předplatného pro váš prostředek Translator Text.
* `TRANSLATOR_TEXT_ENDPOINT`– Globální koncový bod pro Translator Text. Použijte `https://api.cognitive.microsofttranslator.com/`.
