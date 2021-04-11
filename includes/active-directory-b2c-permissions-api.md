---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 04/04/2020
ms.author: mimart
ms.openlocfilehash: dd301cb3b18df5d3eb57ac38e9fb6432411d084b
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106073129"
---
#### <a name="app-registrations"></a>[Registrace aplikací](#tab/app-reg-ga/) 

1. Vyberte **Registrace aplikací** a pak vyberte webovou aplikaci, která by měla mít přístup k rozhraní API. Například *WebApp1*.
1. V části **Spravovat** vyberte **oprávnění rozhraní API**.
1. V části **konfigurovaná oprávnění** vyberte **Přidat oprávnění**.
1. Vyberte kartu **Moje rozhraní API** .
1. Vyberte rozhraní API, ke kterému se má webová aplikace udělit přístup. Například *webapi1*.
1. V části **oprávnění** rozbalte **Ukázka** a pak vyberte obory, které jste definovali dříve. Například *demo. Read* a *demo. Write*.
1. Vyberte **Přidat oprávnění**.
1. Vyberte **udělit souhlas správce pro (název vašeho tenanta)**.
1. Pokud se zobrazí výzva k výběru účtu, vyberte aktuálně přihlášený účet správce nebo se přihlaste pomocí účtu ve Azure AD B2C klientovi, kterému byla přiřazena alespoň role *správce cloudové aplikace* .
1. Vyberte **Ano**.
1. Vyberte **aktualizovat** a pak ověřte, že "uděleno pro..." zobrazí se pod položkou **stav** pro oba obory.

#### <a name="applications-legacy"></a>[Aplikace (starší verze)](#tab/applications-legacy/)

1. Vyberte **aplikace (starší verze)** a pak vyberte webovou aplikaci, která má mít přístup k rozhraní API. Například *WebApp1*.
1. Vyberte **přístup přes rozhraní API** a pak vyberte **Přidat**.
1. V rozevíracím seznamu **Vybrat rozhraní API** vyberte rozhraní API, ke kterému se má webová aplikace udělit přístup. Například *webapi1*.
1. V rozevíracím seznamu **Vybrat obory** vyberte obory, které jste definovali dříve. Například *demo. Read* a *demo. Write*.
1. Vyberte **OK**.
