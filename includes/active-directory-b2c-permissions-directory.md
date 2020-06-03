---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 02/12/2020
ms.author: mimart
ms.openlocfilehash: 587a4bfed00415499bcca6d6054d4ab25cddf0b8
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/02/2020
ms.locfileid: "84298909"
---
#### <a name="app-registrations-preview"></a>[Registrace aplikací (Preview)](#tab/app-reg-preview/)

1. V části **Spravovat**vyberte **oprávnění rozhraní API**.
1. V části **konfigurovaná oprávnění**vyberte **Přidat oprávnění**.
1. Vyberte kartu **rozhraní Microsoft API** a pak vyberte **Microsoft Graph**.
1. Vyberte **oprávnění aplikace**.
1. Rozbalte příslušnou skupinu oprávnění a zaškrtněte políčko oprávnění pro udělení vaší aplikaci pro správu. Například:
    * **AuditLog**  >  **AuditLog. Read. All**: pro čtení protokolů auditu adresáře.
    * **Adresář**  >  **Directory.** prokládání. All: pro scénáře migrace uživatelů nebo správy uživatelů.
    * **Zásada**  >  **Policy. TrustFramework**: pro scénáře průběžné integrace a průběžného doručování (CI/CD). Například nasazení vlastních zásad pomocí Azure Pipelines.
1. Vyberte **Přidat oprávnění**. Jak je směrované, počkejte několik minut, než budete pokračovat k dalšímu kroku.
1. Vyberte **udělit souhlas správce pro (název vašeho tenanta)**.
1. Vyberte aktuálně přihlášený účet správce nebo se přihlaste pomocí účtu v Azure AD B2C tenantovi, kterému byla přiřazena alespoň role *správce cloudové aplikace* .
1. Vyberte **Přijmout**.
1. Vyberte **aktualizovat**a pak ověřte, že "uděleno pro..." zobrazí se pod položkou **stav**. Rozšíření oprávnění může trvat několik minut.

#### <a name="applications"></a>[Aplikace](#tab/applications/)

1. Na stránce Přehled **zaregistrovaných aplikací** vyberte **Nastavení**.
1. V části **přístup přes rozhraní API**vyberte **požadovaná oprávnění**.
1. Vyberte **Microsoft Graph**.
1. V části **oprávnění aplikace**zaškrtněte políčko oprávnění pro udělení vaší aplikaci pro správu. Například:
    * **Číst všechna data protokolu auditu**: vyberte toto oprávnění pro čtení protokolů auditu adresáře.
    * **Čtení a zápis dat adresáře**: Toto oprávnění vyberte pro scénáře migrace uživatelů nebo správy uživatelů.
    * **Čtení a zápis zásad pro vztahy důvěryhodnosti vaší organizace**: Toto oprávnění vyberte pro scénáře průběžné integrace/průběžného doručování (CI/CD). Například nasazení vlastních zásad pomocí Azure Pipelines.
1. Vyberte **Uložit**.
1. Vyberte **udělit oprávnění**a pak vyberte **Ano**. Aby bylo možné plně šířit oprávnění, může trvat několik minut.
