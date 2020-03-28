---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: 71a6654acd436c27bd2370646dede81778113860
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78186133"
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
1. Vyberte **Přidat oprávnění**. Podle pokynů počkejte několik minut, než přejdete k dalšímu kroku.
1. Vyberte **Udělit souhlas správce (název vašeho klienta).**
1. Vyberte aktuálně přihlášený účet správce nebo se přihlaste pomocí účtu v tenantovi Azure AD B2C, kterému byla přiřazena alespoň role *správce cloudových aplikací.*
1. Vyberte **Přijmout**.
1. Vyberte **aktualizovat**a ověřte, zda je povoleno "Uděleno pro ...". zobrazí v části **Stav** pro oba obory. Může trvat několik minut, než se oprávnění rozšíří.