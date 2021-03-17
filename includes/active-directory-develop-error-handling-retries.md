---
author: mmacy
ms.author: marsma
ms.date: 11/25/2020
ms.service: active-directory
ms.subservice: develop
ms.topic: include
ms.openlocfilehash: d0377e3e918a8431c35c1f5b15a1c4b54eeae3b4
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2020
ms.locfileid: "98760689"
---
## <a name="retrying-after-errors-and-exceptions"></a>Opakování po chybách a výjimkách

Očekáváte, že budete při volání MSAL implementovat vlastní zásady opakování. MSAL provádí volání HTTP do služby Azure AD a občas může dojít k chybám. Síť může například jít mimo provoz nebo je server přetížený.  

### <a name="http-429"></a>HTTP 429

Pokud je server tokenu služby (STS) přetížený s příliš velkým počtem požadavků, vrátí chybu HTTP 429 s pomocným parametrem o tom, jak dlouho se můžete pokusit znovu v `Retry-After` poli Response.