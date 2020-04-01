---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: 9d394b07ff17ab24305b2580efc8bedfc5e7d384
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183367"
---
#### <a name="applications"></a>[Aplikace](#tab/applications/)

1. Vyberte **možnost Aplikace**.
1. Vyberte aplikaci *webapi1* a otevřete její stránku **Vlastnosti.**
1. Vyberte **publikované obory**. Publikované obory lze udělit klientské aplikaci určitá oprávnění k webovému rozhraní API.
1. V **případě** `demo.read`SCOPE uveďte a `Read access to the web API`pro **popis**.
1. V **případě** `demo.write`SCOPE uveďte a `Write access to the web API`pro **popis**.
1. Vyberte **Uložit**.

#### <a name="app-registrations-preview"></a>[Registrace aplikací (náhled)](#tab/app-reg-preview/)

1. Vyberte **registrace aplikací (náhled).**
1. Vyberte aplikaci *webapi1* a otevřete její stránku **Přehled.**
1. V části **Manage**vyberte **Vystavit rozhraní API**.
1. Vedle **identifikátoru URI ID aplikace**vyberte odkaz **Nastavit.**
1. Nahraďte výchozí hodnotu (identifikátor GUID) `api`a pak vyberte **Uložit**. Zobrazí se úplný identifikátor URI a `https://your-tenant-name.onmicrosoft.com/api`měl by být ve formátu . Když vaše webová aplikace požaduje přístupový token pro rozhraní API, měla by přidat tento identifikátor URI jako předponu pro každý obor, který definujete pro rozhraní API.
1. V části **Obory definované tímto rozhraním API**vyberte **Přidat obor**.
1. Zadejte následující hodnoty pro vytvoření oboru, který definuje přístup pro čtení do rozhraní API, a pak vyberte **Přidat obor**:
    1. **Název oboru**:`demo.read`
    1. **Zobrazovaný název souhlasu správce**:`Read access to demo API`
    1. **Popis souhlasu správce**:`Allows read access to the demo API`
1. Vyberte **Přidat obor**, zadejte následující hodnoty pro přidání oboru, který definuje přístup pro zápis do rozhraní API, a pak vyberte **Přidat obor**:
    1. **Název oboru**:`demo.write`
    1. **Zobrazovaný název souhlasu správce**:`Write access to demo API`
    1. **Popis souhlasu správce**:`Allows write access to the demo API`