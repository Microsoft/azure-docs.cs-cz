---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 04/04/2020
ms.author: mimart
ms.openlocfilehash: ee63c1c0913baf3700d77e6fba1582fe7027ead7
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80875661"
---
#### <a name="applications"></a>[Aplikace](#tab/applications/)

1. Vyberte **Aplikace**a pak vyberte webovou aplikaci, která by měla mít přístup k rozhraní API. Například *webapp1*.
1. Vyberte **přístup k rozhraní API**a pak vyberte **Přidat**.
1. V rozevíracím souboru **Select API** vyberte rozhraní API, ke kterému webové aplikaci má být udělen přístup. Například *webapi1*.
1. V rozevíracím souboru **Vybrat obory** vyberte obory, které jste definovali dříve. Například *demo.read* a *demo.write*.
1. Vyberte **OK**.

#### <a name="app-registrations-preview"></a>[Registrace aplikací (náhled)](#tab/app-reg-preview/)

1. Vyberte **Registrace aplikací (Preview)** a pak vyberte webovou aplikaci, která by měla mít přístup k rozhraní API. Například *webapp1*.
1. V části **Manage**vyberte **oprávnění rozhraní API**.
1. V části **Nakonfigurovaná oprávnění**vyberte **Přidat oprávnění**.
1. Vyberte kartu **Moje api.**
1. Vyberte rozhraní API, ke kterému má být webové aplikaci udělen přístup. Například *webapi1*.
1. V části **Oprávnění**rozbalte **ukázku**a vyberte obory, které jste definovali dříve. Například *demo.read* a *demo.write*.
1. Vyberte **Přidat oprávnění**.
1. Vyberte **Udělit souhlas správce (název vašeho klienta).**
1. Pokud se zobrazí výzva k výběru účtu, vyberte aktuálně přihlášený účet správce nebo se přihlaste pomocí účtu ve vašem tenantovi Azure AD B2C, kterému byla přiřazena alespoň role *správce cloudových aplikací.*
1. Vyberte **ano**.
1. Vyberte **aktualizovat**a ověřte, zda je povoleno "Uděleno pro ...". zobrazí v části **Stav** pro oba obory.
