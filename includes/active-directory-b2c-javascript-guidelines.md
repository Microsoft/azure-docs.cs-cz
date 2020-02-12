---
author: mmacy
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 02/11/2020
ms.author: marsma
ms.openlocfilehash: 687853720e8f963d5a58093b824bb36e8063bcf7
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2020
ms.locfileid: "77149064"
---
## <a name="guidelines-for-using-javascript"></a>Pokyny k používání jazyka JavaScript

Při přizpůsobování rozhraní vaší aplikace pomocí jazyka JavaScript, postupujte podle následujících pokynů:

- Nevytvářejte vazby na událost Click u `<a>` prvků HTML.
- Nevyřídí závislost na Azure AD B2C kódu a v komentářích.
- Neměnit pořadí nebo hierarchie elementů HTML v Azure AD B2C. Pomocí zásady služby Azure AD B2C můžete řídit pořadí prvků uživatelského rozhraní.
- Můžete volat jakékoli služby RESTful těchto aspektů:
    - Budete muset nastavit služby RESTful CORS povolit volání HTTP na straně klienta.
    - Ujistěte se, že vaše služba RESTful je zabezpečené a využívá protokol HTTPS.
    - Nepoužívejte JavaScriptu přímo k volání koncových bodů Azure AD B2C.
- Můžete vložit JavaScript nebo můžete propojit na externí soubory jazyka JavaScript. Pokud používáte externí soubor jazyka JavaScript, nezapomeňte použít absolutní adresu URL a ne relativní adresu URL.
- Rozhraní jazyka JavaScript:
    - Azure AD B2C používá specifickou verzi jQuery. Nezahrnují jinou verzi jQuery. Použití více než jedna verze na stejné stránce způsobí problémy.
    - Použití RequireJS není podporováno.
    - Většina architektury JavaScriptu nejsou podporovány službou Azure AD B2C.
- Nastavení Azure AD B2C lze číst voláním `window.SETTINGS`, `window.CONTENT` objektů, jako je aktuální jazyk uživatelského rozhraní. Neměňte hodnotu z těchto objektů.
- Pokud chcete přizpůsobit chybovou zprávu Azure AD B2C, použijte lokalizaci zásad.
- Pokud nic jde dosáhnout s použitím zásad, obecně je doporučený postup.
