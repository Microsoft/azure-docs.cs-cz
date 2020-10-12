---
title: Příklad spuštění kontejneru příkazu Docker run
titleSuffix: Azure Cognitive Services
description: Příkaz Spustit jako Docker pro kontejner stavu
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 07/07/2020
ms.author: aahi
ms.openlocfilehash: 72a877ab8cfda126016376017c0a7eb42b1186d6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86108904"
---
Pokud chcete spustit kontejner, nejdřív Najděte jeho ID Image:
 
```bash
docker images --format "table {{.ID}}\t{{.Repository}}\t{{.Tag}}"
```

Spusťte následující `docker run` příkaz. Nahraďte níže uvedené zástupné symboly vlastními hodnotami:

| Zástupný symbol | Hodnota | Formát nebo příklad |
|-------------|-------|---|
| **{API_KEY}** | Klíč pro prostředek Analýza textu. Můžete ji najít na stránce **klíč a koncový bod** prostředku na Azure Portal. |`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`|
| **{ENDPOINT_URI}** | Koncový bod pro přístup k rozhraní API pro analýzu textu. Můžete ji najít na stránce **klíč a koncový bod** prostředku na Azure Portal. | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |
| **{IMAGE_ID}** | ID image pro váš kontejner. | `1.1.011300001-amd64-preview` |
| **{INPUT_DIR}** | Vstupní adresář pro kontejner. | Systému `C:\healthcareMount` <br> Linux/MacOS: `/home/username/input` |

```bash
docker run --rm -it -p 5000:5000 --cpus 6 --memory 12g \
--mount type=bind,src={INPUT_DIR},target=/output {IMAGE_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Disk:Format=json
```

Tento příkaz:

- Předpokládá, že vstupní adresář v hostitelském počítači existuje.
- Spustí Analýza textu pro kontejner stavů z image kontejneru.
- Přiděluje 6 PROCESORových jader a 12 gigabajtů (GB) paměti.
- Zveřejňuje port TCP 5000 a přiděluje pro kontejner pseudo TTY.
- Po ukončení automaticky odstraní kontejner. Bitová kopie kontejneru je stále k dispozici na hostitelském počítači.
