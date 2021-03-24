---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: 3ebe1ec4c0292a530e5ef2c754e9b002e931300e
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2021
ms.locfileid: "95994300"
---
#### <a name="app-registrations"></a>[Registrace aplikací](#tab/app-reg-ga/) 

1. Vyberte **Registrace aplikací**.
1. Výběrem aplikace *webapi1* otevřete její stránku **Přehled** .
1. V části **Spravovat** vyberte **zveřejnit rozhraní API**.
1. Vedle pole **identifikátor URI ID aplikace** vyberte odkaz **nastavit** .
1. Nahraďte výchozí hodnotu (identifikátor GUID) hodnotou `api` a potom vyberte **Uložit**. Zobrazí se úplný identifikátor URI a měl by být ve formátu `https://your-tenant-name.onmicrosoft.com/api` . Pokud vaše webová aplikace požaduje přístupový token pro rozhraní API, měl by tento identifikátor URI přidat jako předponu pro každý obor, který definujete pro rozhraní API.
1. V části **obory definované tímto rozhraním API** vyberte **Přidat obor**.
1. Zadejte následující hodnoty pro vytvoření oboru, který definuje přístup pro čtení k rozhraní API, a pak vyberte **Přidat obor**:
    1. **Název oboru**: `demo.read`
    1. **Zobrazovaný název souhlasu správce**: `Read access to demo API`
    1. **Popis souhlasu správce**: `Allows read access to the demo API`
1. Vyberte **Přidat obor**, zadejte následující hodnoty a přidejte tak obor definující přístup pro zápis do rozhraní API a pak vyberte **Přidat obor**:
    1. **Název oboru**: `demo.write`
    1. **Zobrazovaný název souhlasu správce**: `Write access to demo API`
    1. **Popis souhlasu správce**: `Allows write access to the demo API`

#### <a name="applications-legacy"></a>[Aplikace (starší verze)](#tab/applications-legacy/)

1. Vyberte **aplikace (starší verze)**.
1. Výběrem aplikace *webapi1* otevřete její stránku **vlastností** .
1. Vyberte **publikované obory**. Publikované obory lze použít pro udělení určitých oprávnění k webovému rozhraní API klientské aplikaci.
1. Pro **Rozsah** zadejte, `demo.read` a pro **Popis** zadejte `Read access to the web API` .
1. Pro **Rozsah** zadejte, `demo.write` a pro **Popis** zadejte `Write access to the web API` .
1. Vyberte **Uložit**.