---
title: Konfigurace překladu
titleSuffix: Azure Cognitive Services
description: Tento článek vám ukáže, jak nakonfigurovat různé možnosti pro překlad.
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: metang
ms.openlocfilehash: 0a6ed6ecea216a36cfc9da4ef36a2bddc69cc6a8
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2020
ms.locfileid: "92633140"
---
# <a name="how-to-configure-translation"></a>Postup konfigurace překladu

Tento článek ukazuje, jak nakonfigurovat různé možnosti pro překlad v moderní čtečce.

## <a name="configure-translation-language"></a>Konfigurovat jazyk překladu

`options`Parametr obsahuje všechny příznaky, které lze použít ke konfiguraci překladu. Nastavte `language` parametr na jazyk, na který chcete překládat. Úplný seznam podporovaných jazyků najdete v části [jazyková podpora](./language-support.md) .

```typescript
const options = {
    translationOptions: {
        language: 'fr-FR'
    }
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="automatically-translate-the-document-on-load"></a>Při načtení automaticky přeložit dokument

Nastavte `autoEnableDocumentTranslation` na `true` Povolit automatické překladu celého dokumentu, když se načtou moderní čtečka.

```typescript
const options = {
    translationOptions: {
        autoEnableDocumentTranslation: true
    }
};
```

## <a name="automatically-enable-word-translation"></a>Automaticky povolit Překlad slov

Nastavte `autoEnableWordTranslation` na `true` Povolit převod na jeden Word.

```typescript
const options = {
    translationOptions: {
        autoEnableWordTranslation: true
    }
};
```

## <a name="next-steps"></a>Další kroky

* Prozkoumejte [referenční materiály k sadě pro moderní čtečku](./reference.md)