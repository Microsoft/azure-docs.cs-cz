---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 04/04/2020
ms.author: mimart
ms.openlocfilehash: ee63c1c0913baf3700d77e6fba1582fe7027ead7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80875661"
---
#### <a name="applications"></a>[Aplikace](#tab/applications/)

1. Vyberte **aplikace**a pak vyberte webovou aplikaci, která by měla mít přístup k rozhraní API. Například *WebApp1*.
1. Vyberte **přístup přes rozhraní API**a pak vyberte **Přidat**.
1. V rozevíracím seznamu **Vybrat rozhraní API** vyberte rozhraní API, ke kterému se má webová aplikace udělit přístup. Například *webapi1*.
1. V rozevíracím seznamu **Vybrat obory** vyberte obory, které jste definovali dříve. Například *demo. Read* a *demo. Write*.
1. Vyberte **OK**.

#### <a name="app-registrations-preview"></a>[Registrace aplikací (Preview)](#tab/app-reg-preview/)

1. Vyberte **Registrace aplikací (Preview)** a pak vyberte webovou aplikaci, která má mít přístup k rozhraní API. Například *WebApp1*.
1. V části **Spravovat**vyberte **oprávnění rozhraní API**.
1. V části **konfigurovaná oprávnění**vyberte **Přidat oprávnění**.
1. Vyberte kartu **Moje rozhraní API** .
1. Vyberte rozhraní API, ke kterému se má webová aplikace udělit přístup. Například *webapi1*.
1. V části **oprávnění**rozbalte **Ukázka**a pak vyberte obory, které jste definovali dříve. Například *demo. Read* a *demo. Write*.
1. Vyberte **Přidat oprávnění**.
1. Vyberte **udělit souhlas správce pro (název vašeho tenanta)**.
1. Pokud se zobrazí výzva k výběru účtu, vyberte aktuálně přihlášený účet správce nebo se přihlaste pomocí účtu ve Azure AD B2C klientovi, kterému byla přiřazena alespoň role *správce cloudové aplikace* .
1. Vyberte **Ano**.
1. Vyberte **aktualizovat**a pak ověřte, že "uděleno pro..." zobrazí se pod položkou **stav** pro oba obory.
