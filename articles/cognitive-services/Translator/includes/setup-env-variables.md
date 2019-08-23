---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: 156486f4f4f0df3d4bb4ab76492709bbecfb8eb5
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2019
ms.locfileid: "69906548"
---
## <a name="set-up"></a>Nastavení

### <a name="create-a-translator-text-resource"></a>Vytvoření prostředku Translator Text

Azure Cognitive Services jsou představovány prostředky Azure, ke kterým jste se přihlásili. Vytvořte prostředek pro Translator Text pomocí [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) nebo rozhraní příkazového [řádku Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) na vašem místním počítači. Můžete také:

* Získejte [zkušební klíč](https://azure.microsoft.com/try/cognitive-services) platný po dobu 7 dnů zdarma. Po registraci bude k dispozici na webu Azure.
* Zobrazení existujícího prostředku v [Azure Portal](https://portal.azure.com/).

Po získání klíče ze zkušebního předplatného nebo prostředku vytvořte dvě [proměnné prostředí](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication):

* `TRANSLATOR_TEXT_SUBSCRIPTION_KEY`– Klíč předplatného pro váš prostředek Translator Text.
* `TRANSLATOR_TEXT_ENDPOINT`– Místní koncový bod nebo název vlastní subdomény pro váš prostředek.
