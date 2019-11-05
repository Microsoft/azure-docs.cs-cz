---
author: mmacy
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: marsma
ms.openlocfilehash: 462c1fca0ecd706c1bf04ac5a0ef8561321e05bc
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73474872"
---
#### <a name="applicationstabapplications"></a>[Aplikace](#tab/applications/)

1. Vyberte **aplikace**a pak vyberte webovou aplikaci, která by měla mít přístup k rozhraní API. Například *WebApp1*.
1. Vyberte **přístup přes rozhraní API**a pak vyberte **Přidat**.
1. V rozevíracím seznamu **Vybrat rozhraní API** vyberte rozhraní API, ke kterému se má webová aplikace udělit přístup. Například *webapi1*.
1. V rozevíracím seznamu **Vybrat obory** vyberte obory, které jste definovali dříve. Například *demo. Read* a *demo. Write*.
1. Vyberte **OK**.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[Registrace aplikací (Preview)](#tab/app-reg-preview/)

1. Vyberte **Registrace aplikací (Preview)** a pak vyberte webovou aplikaci, která má mít přístup k rozhraní API. Například *WebApp1*.
1. V části **Spravovat**vyberte **oprávnění rozhraní API**.
1. V části **konfigurovaná oprávnění**vyberte **Přidat oprávnění**.
1. Vyberte kartu **Moje rozhraní API** .
1. Vyberte rozhraní API, ke kterému se má webová aplikace udělit přístup. Například *webapi1*.
1. V části **oprávnění**rozbalte **Ukázka**a pak vyberte obory, které jste definovali dříve. Například *demo. Read* a *demo. Write*.
1. Vyberte **Přidat oprávnění**. Jak je směrované, počkejte několik minut, než budete pokračovat k dalšímu kroku.
1. Vyberte **udělit souhlas správce pro (název vašeho tenanta)** .
1. Vyberte aktuálně přihlášený účet správce nebo se přihlaste pomocí účtu v Azure AD B2C tenantovi, kterému byla přiřazena alespoň role *správce cloudové aplikace* .
1. Vyberte **Přijmout**.
1. Vyberte **aktualizovat**a pak ověřte, že "uděleno pro..." zobrazí se pod položkou **stav** pro oba obory. Rozšíření oprávnění může trvat několik minut.