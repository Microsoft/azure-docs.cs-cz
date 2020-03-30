---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 02/11/2020
ms.author: mimart
ms.openlocfilehash: ef08f1adc15475cd5dd38548ed39dc57532c0ef5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78185827"
---
## <a name="guidelines-for-using-javascript"></a>Pokyny pro používání JavaScriptu

Při přizpůsobování rozhraní aplikace pomocí JavaScriptu postupujte podle těchto pokynů:

- Nespoutejte událost kliknutí `<a>` na elementy HTML.
- Neužívejte závislost na kódu Azure AD B2C nebo komentářích.
- Neměňte pořadí nebo hierarchii prvků HTML Azure AD B2C. Pomocí zásadazure AD B2C řídit pořadí prvků uživatelského rozhraní.
- Můžete volat jakoukoli službu RESTful s těmito poznámkami:
    - Možná budete muset nastavit restful služby CORS povolit volání HTTP na straně klienta.
    - Ujistěte se, že vaše služba RESTful je bezpečná a používá pouze protokol HTTPS.
    - Nepoužívejte JavaScript přímo k volání koncových bodů Azure AD B2C.
- JavaScript můžete vložit nebo můžete propojit s externími soubory JavaScriptu. Při použití externího souboru JavaScriptu nezapomeňte použít absolutní adresu URL a nikoli relativní adresu URL.
- JavaScriptové architektury:
    - Azure AD B2C používá konkrétní verzi jQuery. Nezahrnejte jinou verzi jQuery. Použití více než jedné verze na stejné stránce způsobuje problémy.
    - Použití RequireJS není podporováno.
    - Většina rozhraní JavaScript u Azure AD B2C nepodporuje.
- Nastavení Azure AD B2C lze `window.SETTINGS` `window.CONTENT` číst voláním , objekty, jako je například aktuální jazyk ui. Neměňte hodnotu těchto objektů.
- Chcete-li přizpůsobit chybovou zprávu Azure AD B2C, použijte lokalizaci v zásadách.
- Pokud lze něco dosáhnout pomocí zásady, obecně je to doporučený způsob.
