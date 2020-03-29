---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 02/12/2020
ms.author: mimart
ms.openlocfilehash: bf8b9370abc58902d9c751505f633dee852210a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78184336"
---
#### <a name="applications"></a>[Aplikace](#tab/applications/)

1. Na stránce **Přehled registrované aplikace** vyberte **Nastavení**.
1. V části **Přístup k rozhraní API**vyberte **Požadovaná oprávnění**.
1. Vyberte **microsoft graph**.
1. V **části Oprávnění aplikace**zaškrtněte políčko oprávnění k udělení vaší aplikace pro správu. Například:
    * **Čtení všech dat protokolu auditu**: Toto oprávnění vyberte pro čtení protokolů auditu adresáře.
    * **Čtení a zápis dat adresáře**: Vyberte toto oprávnění pro scénáře migrace uživatelů nebo správy uživatelů.
    * **Čtení a psaní zásad architektury důvěryhodnosti vaší organizace**: Vyberte toto oprávnění pro scénáře průběžné integrace/průběžného doručování (CI/CD). Například nasazení vlastních zásad s Azure Pipelines.
1. Vyberte **Uložit**.
1. Vyberte **Udělit oprávnění**a pak vyberte **Ano**. Může trvat několik minut, než se oprávnění plně rozšíří.

#### <a name="app-registrations-preview"></a>[Registrace aplikací (náhled)](#tab/app-reg-preview/)

1. V části **Manage**vyberte **oprávnění rozhraní API**.
1. V části **Nakonfigurovaná oprávnění**vyberte **Přidat oprávnění**.
1. Vyberte kartu **Microsoft API a** pak vyberte Microsoft **Graph**.
1. Vyberte **oprávnění aplikace**.
1. Rozbalte příslušnou skupinu oprávnění a zaškrtněte políčko oprávnění k udělení vaší žádosti o správu. Například:
    * **AuditLog** > **AuditLog.Read.All**: Pro čtení protokolů auditu adresáře.
    * **Directory** > **Directory.ReadWrite.All**: Pro scénáře migrace uživatelů nebo správy uživatelů.
    * **Policy** > **Policy.ReadWrite.TrustFramework**: Pro scénáře průběžné integrace/průběžného doručování (CI/CD). Například nasazení vlastních zásad s Azure Pipelines.
1. Vyberte **Přidat oprávnění**. Podle pokynů počkejte několik minut, než přejdete k dalšímu kroku.
1. Vyberte **Udělit souhlas správce (název vašeho klienta).**
1. Vyberte aktuálně přihlášený účet správce nebo se přihlaste pomocí účtu v tenantovi Azure AD B2C, kterému byla přiřazena alespoň role *správce cloudových aplikací.*
1. Vyberte **Přijmout**.
1. Vyberte **aktualizovat**a ověřte, zda je povoleno "Uděleno pro ...". zobrazí v části **Stav**. Může trvat několik minut, než se oprávnění rozšíří.
