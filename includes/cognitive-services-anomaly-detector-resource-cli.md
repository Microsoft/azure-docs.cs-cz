---
author: aahill
ms.author: aahi
ms.service: cognitive-services
ms.topic: include
ms.date: 10/08/2019
ms.openlocfilehash: 5089af4a4e1714d49b844a1b6823487a3f6a8dcf
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74483008"
---
Začněte používat službu Detektor anomálií vytvořením jednoho z prostředků Azure níže.

* <a href="https://azure.microsoft.com/try/cognitive-services/#decision" target="_blank" rel="noopener">Vytvoření zkušebního prostředku (otevře se na nové kartě)</a>
    * Není potřeba žádné předplatné Azure: 
    * Platí sedm dní, zdarma. Po registraci bude zkušební klíč a koncový bod k dispozici na [webu Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/). 
    * To je skvělá volba, pokud chcete vyzkoušet detektor anomálií, ale nemáte předplatné Azure.

* <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector" target="_blank" rel="noopener">Vytvořit prostředek detektoru anomálií (otevře se na nové kartě):</a>
    * K dispozici prostřednictvím portálu Azure, dokud neodstraníte prostředek.
    * Využijte bezplatnou cenovou úroveň k vyzkoušení služby a upgradujte později na placenou úroveň pro produkční prostředí.



### <a name="create-an-environment-variable"></a>Vytvoření proměnné prostředí

>[!NOTE]
> Koncové body pro nezkušební prostředky vytvořené po 1. Další informace a úplný seznam místních koncových bodů naleznete [v tématu Vlastní názvy subdomén pro služby Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains). 

Pomocí klíče a koncového bodu z prostředku, který jste vytvořili, vytvořte dvě proměnné prostředí pro ověřování:

* `ANOMALY_DETECTOR_KEY`- Klíč zdroje pro ověřování vašich požadavků.
* `ANOMALY_DETECTOR_ENDPOINT`- Koncový bod prostředku pro odesílání požadavků rozhraní API. Bude to vypadat takto: 
  * `https://<your-custom-subdomain>.api.cognitive.microsoft.com` 

Použijte pokyny pro váš operační systém.

#### <a name="windows"></a>[Windows](#tab/windows)

```console
setx ANOMALY_DETECTOR_KEY <replace-with-your-anomaly-detector-key>
setx ANOMALY_DETECTOR_ENDPOINT <replace-with-your-anomaly-detector-endpoint>
```

Po přidání proměnné prostředí restartujte okno konzoly.

#### <a name="linux"></a>[Linux](#tab/linux)

```bash
export ANOMALY_DETECTOR_KEY=<replace-with-your-anomaly-detector-key>
export ANOMALY_DETECTOR_ENDPOINT=<replace-with-your-anomaly-detector-endpoint>
```

Po přidání proměnné prostředí spusťte v okně konzoly příkaz `source ~/.bashrc`, aby se změny projevily.

#### <a name="macos"></a>[Macos](#tab/unix)

Upravte `.bash_profile`aplikaci a přidejte proměnnou prostředí:

```bash
export ANOMALY_DETECTOR_KEY=<replace-with-your-anomaly-detector-key>
export ANOMALY_DETECTOR_ENDPOINT=<replace-with-your-anomaly-detector-endpoint>
```

Po přidání proměnné prostředí spusťte v okně konzoly příkaz `source .bash_profile`, aby se změny projevily.

***