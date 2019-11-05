---
author: mmacy
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: marsma
ms.openlocfilehash: 59cf0b81363c724d709d85105525bece13743f26
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73474856"
---
#### <a name="applicationstabapplications"></a>[Aplikace](#tab/applications/)

1. Vyberte **aplikace**.
1. Výběrem aplikace *webapi1* otevřete její stránku **vlastností** .
1. Vyberte **publikované obory**. Publikované obory lze použít pro udělení určitých oprávnění k webovému rozhraní API klientské aplikaci.
1. Jako **Rozsah**zadejte `demo.read`a **Popis**zadejte `Read access to the web API`.
1. Jako **Rozsah**zadejte `demo.write`a **Popis**zadejte `Write access to the web API`.
1. Vyberte **Uložit**.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[Registrace aplikací (Preview)](#tab/app-reg-preview/)

1. Vyberte **Registrace aplikací (Preview)** .
1. Výběrem aplikace *webapi1* otevřete její stránku **Přehled** .
1. V části **Spravovat**vyberte **zveřejnit rozhraní API**.
1. Vedle pole **identifikátor URI ID aplikace**vyberte odkaz **nastavit** .
1. Nahraďte výchozí hodnotu (identifikátor GUID) hodnotou `api`a pak vyberte **Save (Uložit**). Zobrazí se úplný identifikátor URI a měl by být ve formátu `https://your-tenant-name.onmicrosoft.com/api`. Pokud vaše webová aplikace požaduje přístupový token pro rozhraní API, měl by tento identifikátor URI přidat jako předponu pro každý obor, který definujete pro rozhraní API.
1. V části **obory definované tímto rozhraním API**vyberte **Přidat obor**.
1. Zadejte následující hodnoty pro vytvoření oboru, který definuje přístup pro čtení k rozhraní API, a pak vyberte **Přidat obor**:
    1. **Název oboru**: `demo.read`
    1. **Zobrazovaný název souhlasu správce**: `Read access to demo API`
    1. **Popis souhlasu správce**: `Allows read access to the demo API`
1. Vyberte **Přidat obor**, zadejte následující hodnoty a přidejte tak obor definující přístup pro zápis do rozhraní API a pak vyberte **Přidat obor**:
    1. **Název oboru**: `demo.write`
    1. **Zobrazovaný název souhlasu správce**: `Write access to demo API`
    1. **Popis souhlasu správce**: `Allows write access to the demo API`