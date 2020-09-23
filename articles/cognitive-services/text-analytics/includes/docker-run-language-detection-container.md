---
title: Příklad spuštění kontejneru příkazu Docker run
titleSuffix: Azure Cognitive Services
description: Příkaz pro spuštění Docker pro kontejner Rozpoznávání jazyka
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/29/2020
ms.author: aahi
ms.openlocfilehash: f1f36cc730ceca9a961769775d17de6be8d26948
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90906112"
---
Chcete-li spustit kontejner *rozpoznávání jazyka* , spusťte následující `docker run` příkaz. Nahraďte níže uvedené zástupné symboly vlastními hodnotami:

| Zástupný symbol | Hodnota | Formát nebo příklad |
|-------------|-------|---|
| **{API_KEY}** | Klíč pro prostředek Analýza textu. Můžete ji najít na stránce **klíč a koncový bod** prostředku na Azure Portal. |`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`|
| **{ENDPOINT_URI}** | Koncový bod pro přístup k rozhraní API pro analýzu textu. Můžete ji najít na stránce **klíč a koncový bod** prostředku na Azure Portal. | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |


```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/textanalytics/language \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Tento příkaz:

* Spustí kontejner *rozpoznávání jazyka* z image kontejneru.
* Přiděluje jedno PROCESORové jádro a 4 gigabajty (GB) paměti.
* Zveřejňuje port TCP 5000 a přiděluje pro kontejner pseudo TTY.
* Po ukončení automaticky odstraní kontejner. Bitová kopie kontejneru je stále k dispozici na hostitelském počítači.