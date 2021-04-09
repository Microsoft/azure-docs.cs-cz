---
title: Konfigurace čtení nahlas
titleSuffix: Azure Cognitive Services
description: V tomto článku se dozvíte, jak nakonfigurovat různé možnosti pro hlasité čtení.
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: how-to
ms.date: 06/29/2020
ms.author: metang
ms.openlocfilehash: 8b45fe07b4bd42059199197bc5b99f20f6b2a8cf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102608712"
---
# <a name="how-to-configure-read-aloud"></a>Jak nakonfigurovat čtení nahlasu

Tento článek ukazuje, jak nakonfigurovat různé možnosti pro čtení nahlasování v moderní čtečce.

## <a name="automatically-start-read-aloud"></a>Automaticky začít číst nahlas

`options`Parametr obsahuje všechny příznaky, které lze použít ke konfiguraci čtení Nahlasu. Nastavte `autoplay` na `true` , pokud chcete povolit automatické spouštění čtení po spuštění moderního čtecího zařízení.

```typescript
const options = {
    readAloudOptions: {
        autoplay: true
    }
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

> [!NOTE]
> Z důvodu omezení prohlížeče není automatické přehrávání v Safari podporováno.

## <a name="configure-the-voice"></a>Konfigurace hlasu

Nastavte `voice` na buď `male` nebo `female` . Ne všechny jazyky podporují jak hlasy. Další informace najdete na stránce [Podpora jazyků](./language-support.md) .

```typescript
const options = {
    readAloudOptions: {
        voice: 'female'
    }
};
```

## <a name="configure-playback-speed"></a>Konfigurovat rychlost přehrávání

Nastavit `speed` na číslo mezi `0.5` (50%) a `2.5` (250%) zahrnut. Hodnoty mimo tento rozsah budou přesunuty do 0,5 nebo 2,5.

```typescript
const options = {
    readAloudOptions: {
        speed: 1.5
    }
};
```

## <a name="next-steps"></a>Další kroky

* Prozkoumejte [referenční materiály k sadě pro moderní čtečku](./reference.md)