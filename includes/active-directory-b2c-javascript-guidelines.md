---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 02/11/2020
ms.author: mimart
ms.openlocfilehash: ef08f1adc15475cd5dd38548ed39dc57532c0ef5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "78185827"
---
## <a name="guidelines-for-using-javascript"></a>Pokyny pro používání JavaScriptu

Pokud přizpůsobíte rozhraní aplikace pomocí JavaScriptu, postupujte podle těchto pokynů:

- Nevážete událost kliknutí na `<a>` prvky jazyka HTML.
- Nepoužívejte závislost na Azure AD B2C kód nebo komentáře.
- Neměňte pořadí ani hierarchii Azure AD B2C prvků HTML. Použijte zásady Azure AD B2C k řízení pořadí prvků uživatelského rozhraní.
- Můžete volat libovolnou službu RESTful s těmito informacemi:
    - Možná budete muset nastavit CORS služby RESTful tak, aby umožňovala volání HTTP na straně klienta.
    - Ujistěte se, že je služba RESTful zabezpečená a používá jenom protokol HTTPS.
    - Nepoužívejte JavaScript přímo pro volání Azure AD B2Cch koncových bodů.
- Můžete vložit JavaScript nebo můžete propojit s externími soubory JavaScriptu. Při použití externího souboru JavaScriptu je nutné použít absolutní adresu URL, nikoli relativní adresu URL.
- Rozhraní JavaScript:
    - Azure AD B2C používá specifickou verzi jQuery. Nezahrnujte jinou verzi jQuery. Používání více než jedné verze na stejné stránce způsobuje problémy.
    - Použití RequireJS se nepodporuje.
    - Azure AD B2C nepodporuje většinu platforem JavaScript.
- Nastavení Azure AD B2C lze číst voláním `window.SETTINGS` , `window.CONTENT` objektů, jako je aktuální jazyk uživatelského rozhraní. Neměňte hodnotu těchto objektů.
- Pokud chcete přizpůsobit chybovou zprávu Azure AD B2C, použijte lokalizaci v zásadě.
- Pokud se dá cokoli dosáhnout pomocí zásad, obvykle se jedná o doporučený způsob.
