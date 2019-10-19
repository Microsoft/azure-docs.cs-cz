---
author: aahill
ms.author: aahi
ms.service: cognitive-services
ms.topic: include
ms.date: 10/08/2019
ms.openlocfilehash: a7ae6cb1231e4c202dfd0a39602c03b33099d088
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2019
ms.locfileid: "72554721"
---
Začněte používat službu anomálie detektor vytvořením jednoho z níže uvedených prostředků Azure.

* [Prostředek zkušební verze](https://azure.microsoft.com/try/cognitive-services/#decision) (není potřeba předplatné Azure): 
    * Platí po dobu sedmi dnů zdarma. Po registraci bude na [webu Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/)dostupný zkušební klíč a koncový bod. 
    * To je skvělý způsob, pokud chcete vyzkoušet detektor anomálií, ale nemáte předplatné Azure.

* [Prostředek detektoru anomálií](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector):
    * K dispozici prostřednictvím [Azure Portal](https://ms.portal.azure.com#blade/HubsExtension/BrowseResourceGroupBlade) , dokud prostředek neodstraníte.
    * Pomocí cenové úrovně Free můžete službu vyzkoušet a upgradovat ji později na placenou úroveň pro produkční prostředí.

### <a name="create-an-environment-variable"></a>Vytvoření proměnné prostředí

>[!NOTE]
> Koncové body pro prostředky nevyužívající zkušební verzi vytvořené po 1. červenci 2019 používají vlastní formát subdomény, který vidíte níže. Další informace a úplný seznam regionálních koncových bodů najdete v tématu [názvy vlastních subdomén pro Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains). 

Pomocí klíče a koncového bodu z prostředku, který jste vytvořili, vytvořte dvě proměnné prostředí pro ověřování:

* `ANOMALY_DETECTOR_KEY` – klíč prostředku pro ověřování vašich požadavků.
* `ANOMALY_DETECTOR_ENDPOINT` – koncový bod prostředku pro odesílání požadavků rozhraní API. Bude vypadat takto: 
  * `https://<your-custom-subdomain>.api.cognitive.microsoft.com` 

Použijte pokyny pro váš operační systém.

#### <a name="windowstabwindows"></a>[Windows](#tab/windows)

```console
setx ANOMALY_DETECTOR_KEY your-anomaly-detector-key
setx ANOMALY_DETECTOR_ENDPOINT your-anomaly-detector-endpoint
```

Po přidání proměnné prostředí restartujte okno konzoly.

#### <a name="linuxtablinux"></a>[Linux](#tab/linux)

```bash
export ANOMALY_DETECTOR_KEY=your-anomaly-detector-key
export ANOMALY_DETECTOR_ENDPOINT=your-anomaly-detector-endpoint
```

Po přidání proměnné prostředí spusťte v okně konzoly příkaz `source ~/.bashrc`, aby se změny projevily.

#### <a name="macostabunix"></a>[macOS](#tab/unix)

Upravte `.bash_profile` a přidejte proměnnou prostředí:

```bash
export ANOMALY_DETECTOR_KEY=your-anomaly-detector-key
export ANOMALY_DETECTOR_ENDPOINT=your-anomaly-detector-endpoint
```

Po přidání proměnné prostředí spusťte v okně konzoly příkaz `source .bash_profile`, aby se změny projevily.

***