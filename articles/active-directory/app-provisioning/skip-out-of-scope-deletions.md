---
title: Přeskočit odstranění mimo obor uživatelů | Microsoft Docs
description: Přečtěte si, jak potlačit výchozí chování při zrušení zřizování z oboru uživatelů.
services: active-directory
author: cmmdesai
documentationcenter: na
manager: daveba
ms.assetid: 1a2c375a-1bb1-4a61-8115-5a69972c6ad6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/10/2019
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3b1261e641afe1be1b298b4b19198782bf57eaa1
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2020
ms.locfileid: "77066158"
---
# <a name="skip-deletion-of-user-accounts-that-go-out-of-scope"></a>Přeskočit odstranění uživatelských účtů, které přesahují rozsah

Ve výchozím nastavení modul zřizování Azure AD Soft odstraní nebo zakáže uživatele, kteří se nacházejí mimo rozsah. U některých scénářů, jako je například Workday pro příchozí zřizování uživatelů v rámci služby Active Directory, nemusí být toto chování očekávané a pravděpodobně budete chtít přepsat toto výchozí chování.  

V této příručce se dozvíte, jak pomocí rozhraní Microsoft Graph API a Microsoft Graph Průzkumníku API nastavit příznak ***SkipOutOfScopeDeletions*** , který řídí zpracování účtů, které se nacházejí mimo rozsah. 
* Pokud je ***SkipOutOfScopeDeletions*** nastavené na 0 (NEPRAVDA), pak se účty, které se přestanou z oboru, budou v cíli zakázané.
* Pokud je ***SkipOutOfScopeDeletions*** nastavené na 1 (true), pak se účty, které se nacházejí mimo rozsah, nebudou v cíli zablokovat, tento příznak se nastaví na úrovni *aplikace pro zřizování* a dá se nakonfigurovat pomocí Graph API. 

Vzhledem k tomu, že se tato konfigurace v tuto konfiguraci používá v rámci *služby Active Directory pro zřizování uživatelů* , následující kroky zahrnují snímky obrazovky aplikace v Workday. To se ale dá použít i u **všech ostatních aplikací** (ServiceNow, Salesforce, Dropbox atd.).

## <a name="step-1-retrieve-your-provisioning-app-service-principal-id-object-id"></a>Krok 1: načtení ID objektu zabezpečení zřizování App Service (ID objektu)

1. Spusťte [Azure Portal](https://portal.azure.com)a přejděte do části vlastnosti vaší aplikace zřizování. Například pokud chcete exportovat pracovní *postup do mapování aplikace pro zřizování uživatelů služby AD* , přejděte do části vlastnosti této aplikace. 
1. V části vlastnosti vaší aplikace pro zřizování Zkopírujte hodnotu identifikátoru GUID přidruženou k poli *ID objektu* . Tato hodnota se také označuje jako **ServicePrincipalId** vaší aplikace a bude se používat v rámci operací Graph Exploreru.

   ![ID objektu App Service Workday](./media/skip-out-of-scope-deletions/wd_export_01.png)

## <a name="step-2-sign-into-microsoft-graph-explorer"></a>Krok 2: Přihlaste se k Průzkumníkovi Microsoft Graph

1. Spustit [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)
1. Klikněte na tlačítko Přihlásit se pomocí Microsoft a přihlaste se pomocí účtu globálního správce Azure AD nebo přihlašovacích údajů správce aplikací.

    ![Přihlášení do grafu](./media/skip-out-of-scope-deletions/wd_export_02.png)

1. Po úspěšném přihlášení se zobrazí podrobnosti o uživatelském účtu v levém podokně.

## <a name="step-3-get-existing-app-credentials-and-connectivity-details"></a>Krok 3: získání přihlašovacích údajů pro existující aplikace a podrobnosti o připojení

V Průzkumníku Microsoft Graph spusťte následující příkaz GET Query, který nahradí [servicePrincipalId] **servicePrincipalId** extrahováným z [kroku 1](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id).

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/secrets
```

   ![ZÍSKAT dotaz na úlohu](./media/skip-out-of-scope-deletions/skip-03.png)

Zkopírujte odpověď do textového souboru. Bude vypadat jako text JSON zobrazený níže a hodnoty zvýrazněné žlutě specifické pro vaše nasazení. Přidejte zvýrazněné řádky zeleně na konec a aktualizujte heslo připojení k pracovní den zvýrazněné modře. 

   ![ZÍSKAT odpověď úlohy](./media/skip-out-of-scope-deletions/skip-04.png)

Tady je blok JSON, který se má přidat do mapování. 

```json
        {
            "key": "SkipOutOfScopeDeletions",
            "value": "True"
        }
```

## <a name="step-4-update-the-secrets-endpoint-with-the-skipoutofscopedeletions-flag"></a>Krok 4: aktualizace koncového bodu tajných kódů pomocí příznaku SkipOutOfScopeDeletions

V Průzkumníku grafů spusťte následující příkaz, který aktualizuje koncový bod tajných kódů pomocí příznaku ***SkipOutOfScopeDeletions*** . 

V adrese URL níže nahraďte [servicePrincipalId] **servicePrincipalId** extrahovanou z [kroku 1](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id). 

```http
   PUT https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/secrets
```
Zkopírujte aktualizovaný text z kroku 3 do části "text žádosti" a v části "hlavičky žádosti" nastavte hlavičku "Content-Type" na "Application/JSON". 

   ![Žádost o vložení](./media/skip-out-of-scope-deletions/skip-05.png)

Klikněte na spustit dotaz. 

Výstup byste měli získat jako "úspěch – stavový kód 204". 

   ![Odpověď PUT](./media/skip-out-of-scope-deletions/skip-06.png)

## <a name="step-5-verify-that-out-of-scope-users-dont-get-disabled"></a>Krok 5: ověření, jestli nechcete, aby uživatelé měli zakázaný rozsah

Pomocí aktualizace pravidel oboru pro přeskočení konkrétního uživatele můžete otestovat tento příznak výsledky při očekávaném chování. V následujícím příkladu vyloučíme zaměstnance s ID 21173 (který byl dříve v oboru) přidáním nového pravidla oboru: 

   ![Příklad oboru](./media/skip-out-of-scope-deletions/skip-07.png)

V dalším cyklu zřizování služba Azure AD Provisioning zjistí, že se uživateli 21173 dostalo mimo rozsah a jestli je povolená vlastnost SkipOutOfScopeDeletions, a pravidlo synchronizace pro tohoto uživatele zobrazí zprávu, jak je uvedeno níže: 

   ![Příklad oboru](./media/skip-out-of-scope-deletions/skip-08.png)


