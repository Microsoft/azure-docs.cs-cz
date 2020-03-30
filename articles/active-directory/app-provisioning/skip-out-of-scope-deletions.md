---
title: Přeskočit odstranění uživatelů mimo rozsah | Dokumenty společnosti Microsoft
description: Přečtěte si, jak přepsat výchozí chování zrušení zřizování uživatelů mimo rozsah.
services: active-directory
author: cmmdesai
documentationcenter: na
manager: daveba
ms.assetid: 1a2c375a-1bb1-4a61-8115-5a69972c6ad6
ms.service: active-directory
ms.subservice: app-provisioning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/10/2019
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: a1668c022a0f067a381ba09b397c7d38c99ad074
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522445"
---
# <a name="skip-deletion-of-user-accounts-that-go-out-of-scope"></a>Přeskočit odstranění uživatelských účtů, které jsou mimo rozsah

Ve výchozím nastavení software zřizovacího modulu Azure AD odstraní nebo zakáže uživatele, kteří přejdou mimo rozsah. Však pro některé scénáře, jako je Workday do AD uživatele příchozí zřizování toto chování nemusí být očekávané a můžete chtít přepsat toto výchozí chování.  

Tato příručka popisuje, jak pomocí rozhraní Microsoft Graph API a průzkumníku rozhraní MICROSOFT Graph API nastavit příznak ***SkipOutOfScopeDeletions,*** který řídí zpracování účtů, které jsou mimo rozsah. 
* Pokud ***SkipOutOfScopeDeletions*** je nastavena na 0 (false), pak účty, které jdou mimo rozsah bude zakázánv cílové
* Pokud ***SkipOutOfScopeDeletions*** je nastavena na 1 (true), pak účty, které jdou mimo rozsah získá zakázána v cíl Tento příznak je nastavena na úrovni *zřizování aplikace* a lze nakonfigurovat pomocí rozhraní API grafu. 

Vzhledem k tomu, že tato konfigurace je široce používána s aplikací *zřizování uživatelů služby Workday to Active Directory,* níže uvedené kroky zahrnují snímky obrazovky aplikace Workday. To však může být také použito se **všemi ostatními aplikacemi,** jako je ServiceNow, Salesforce, Dropbox atd.).

## <a name="step-1-retrieve-your-provisioning-app-service-principal-id-object-id"></a>Krok 1: Načtení ID instancí service service zřizování (ID objektu)

1. Spusťte [portál Azure](https://portal.azure.com)a přejděte do části Vlastnosti vaší zřizovací aplikace. Pokud například chcete exportovat pracovní den do mapování *aplikací zřizování uživatelů služby AD,* přejděte do části Vlastnosti této aplikace. 
1. V části Vlastnosti zřizovací aplikace zkopírujte hodnotu GUID přidruženou k poli *ID objektu.* Tato hodnota se také nazývá **ServicePrincipalId** vaší aplikace a bude použita v operacích průzkumníka grafu.

   ![ID hlavního povinného spuštění aplikace pracovního dne](./media/skip-out-of-scope-deletions/wd_export_01.png)

## <a name="step-2-sign-into-microsoft-graph-explorer"></a>Krok 2: Přihlášení do Aplikace Microsoft Graph Explorer

1. Spuštění [aplikace Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)
1. Klikněte na tlačítko "Přihlásit se s Microsoftem" a přihlásit se pomocí přihlašovacích údajů Azure AD Global Admin nebo App Admin.

    ![Přihlášení do grafu](./media/skip-out-of-scope-deletions/wd_export_02.png)

1. Po úspěšném přihlášení se podrobnosti o uživatelském účtu zobrazí v levém podokně.

## <a name="step-3-get-existing-app-credentials-and-connectivity-details"></a>Krok 3: Získání existujících přihlašovacích údajů k aplikaci a podrobností o připojení

V aplikaci Microsoft Graph Explorer spusťte následující dotaz GET, který nahradí [servicePrincipalId] **id ServicePrincipalId** extrahovaným z [kroku 1](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id).

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/secrets
```

   ![Dotaz na úlohu GET](./media/skip-out-of-scope-deletions/skip-03.png)

Zkopírujte odpověď do textového souboru. Bude vypadat jako níže uvedený text JSON s hodnotami zvýrazněnými žlutě specifickými pro vaše nasazení. Přidejte na konec zvýrazněné zelené řádky a aktualizujte heslo pro připojení pracovní den zvýrazněné modře. 

   ![Odpověď na úlohu GET](./media/skip-out-of-scope-deletions/skip-04.png)

Zde je blok JSON přidat do mapování. 

```json
        {
            "key": "SkipOutOfScopeDeletions",
            "value": "True"
        }
```

## <a name="step-4-update-the-secrets-endpoint-with-the-skipoutofscopedeletions-flag"></a>Krok 4: Aktualizace koncového bodu tajných klíčů příznakem SkipOutOfScopeDeletions

V Průzkumníku grafů spusťte následující příkaz a aktualizujte koncový bod tajných kódů příznakem ***SkipOutOfScopeDeletions.*** 

V níže uvedené adrese URL nahraďte [servicePrincipalId] **id ServicePrincipalId** extrahovaným z [kroku 1](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id). 

```http
   PUT https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/secrets
```
Zkopírujte aktualizovaný text z kroku 3 do "Tělo požadavku" a nastavte záhlaví "Content-Type" na "application/json" v "Request Headers". 

   ![Požadavek PUT](./media/skip-out-of-scope-deletions/skip-05.png)

Klikněte na "Spustit dotaz". 

Měli byste získat výstup jako "Úspěch – Stavový kód 204". 

   ![PUT odpověď](./media/skip-out-of-scope-deletions/skip-06.png)

## <a name="step-5-verify-that-out-of-scope-users-dont-get-disabled"></a>Krok 5: Ověřte, že uživatelé mimo rozsah nejsou zakázáni

Tento příznak můžete otestovat očekávané chování aktualizací pravidel oboru přeskočit konkrétního uživatele. V níže uvedeném příkladu vylučujeme zaměstnance s ID 21173 (který byl dříve v oboru) přidáním nového pravidla oboru: 

   ![Příklad vymezení rozsahu](./media/skip-out-of-scope-deletions/skip-07.png)

V dalším cyklu zřizování služba zřizování Azure AD zjistí, že uživatel 21173 má za sebou mimo rozsah a pokud skipOutOfScopeDeletions vlastnost je povolena, pak pravidlo synchronizace pro tohoto uživatele zobrazí zprávu, jak je znázorněno níže: 

   ![Příklad vymezení rozsahu](./media/skip-out-of-scope-deletions/skip-08.png)


