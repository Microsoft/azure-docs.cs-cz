---
author: aahill
ms.author: aahi
ms.service: cognitive-services
ms.topic: include
ms.date: 10/08/2019
ms.openlocfilehash: 5089af4a4e1714d49b844a1b6823487a3f6a8dcf
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2019
ms.locfileid: "74483008"
---
Začněte používat službu anomálie detektor vytvořením jednoho z níže uvedených prostředků Azure.

* <a href="https://azure.microsoft.com/try/cognitive-services/#decision" target="_blank" rel="noopener">Vytvoření prostředku zkušební verze (otevře se na nové kartě)</a>
    * Není potřeba žádné předplatné Azure: 
    * Platí po dobu sedmi dnů zdarma. Po registraci bude na [webu Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/)dostupný zkušební klíč a koncový bod. 
    * To je skvělý způsob, pokud chcete vyzkoušet detektor anomálií, ale nemáte předplatné Azure.

* <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector" target="_blank" rel="noopener">Vytvoření prostředku detektoru anomálií (otevře se na nové kartě)</a>:
    * K dispozici prostřednictvím Azure Portal, dokud prostředek neodstraníte.
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
setx ANOMALY_DETECTOR_KEY <replace-with-your-anomaly-detector-key>
setx ANOMALY_DETECTOR_ENDPOINT <replace-with-your-anomaly-detector-endpoint>
```

Po přidání proměnné prostředí restartujte okno konzoly.

#### <a name="linuxtablinux"></a>[Linux](#tab/linux)

```bash
export ANOMALY_DETECTOR_KEY=<replace-with-your-anomaly-detector-key>
export ANOMALY_DETECTOR_ENDPOINT=<replace-with-your-anomaly-detector-endpoint>
```

Po přidání proměnné prostředí spusťte v okně konzoly příkaz `source ~/.bashrc`, aby se změna projevila.

#### <a name="macostabunix"></a>[macOS](#tab/unix)

Upravte `.bash_profile`a přidejte proměnnou prostředí:

```bash
export ANOMALY_DETECTOR_KEY=<replace-with-your-anomaly-detector-key>
export ANOMALY_DETECTOR_ENDPOINT=<replace-with-your-anomaly-detector-endpoint>
```

Po přidání proměnné prostředí spusťte v okně konzoly příkaz `source .bash_profile`, aby se změna projevila.

***