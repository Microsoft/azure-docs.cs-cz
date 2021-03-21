---
author: mmacy
ms.author: marsma
ms.date: 11/25/2020
ms.service: active-directory
ms.subservice: develop
ms.topic: include
ms.openlocfilehash: d0377e3e918a8431c35c1f5b15a1c4b54eeae3b4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98760689"
---
## <a name="retrying-after-errors-and-exceptions"></a>Opakování po chybách a výjimkách

Očekáváte, že budete při volání MSAL implementovat vlastní zásady opakování. MSAL provádí volání HTTP do služby Azure AD a občas může dojít k chybám. Síť může například jít mimo provoz nebo je server přetížený.  

### <a name="http-429"></a>HTTP 429

Pokud je server tokenu služby (STS) přetížený s příliš velkým počtem požadavků, vrátí chybu HTTP 429 s pomocným parametrem o tom, jak dlouho se můžete pokusit znovu v `Retry-After` poli Response.