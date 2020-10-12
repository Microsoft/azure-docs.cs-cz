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
ms.author: metan
ms.openlocfilehash: ea066fab713100309103a040d309bac5b984fb99
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85486911"
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