---
title: "Azure AD Connect: Ověřování průchozí – inteligentní uzamčení | Microsoft Docs"
description: "Tento článek popisuje, jak předávací ověřování Azure Active Directory (Azure AD) chrání vaše místní účty před útoky hrubou silou hesla v cloudu"
services: active-directory
keywords: "Azure AD Connect předávací ověřování, instalace služby Active Directory, požadované součásti pro Azure AD, jednotné přihlašování, jednotné přihlašování"
documentationcenter: 
author: swkrish
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2018
ms.author: billmath
ms.openlocfilehash: 9477d47824213d7ea15bcf6c6b615a220bae2e48
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2018
---
# <a name="azure-active-directory-pass-through-authentication-smart-lockout"></a>Předávací ověřování Azure Active Directory: Inteligentní uzamčení

## <a name="overview"></a>Přehled

Azure Active Directory (Azure AD) chrání před útoky hrubou silou heslo a zabrání uživatelům originálního zamykají mimo jejich aplikace Office 365 a SaaS. Tato funkce volána *inteligentní uzamčení*, je podporováno, pokud používáte ověřování průchozí jako způsob přihlášení. Inteligentní uzamčení povoleno ve výchozím nastavení pro všechny klienty, ne jenom klienty pomocí předávacího ověřování, a trvale chrání vaše uživatelské účty.

Inteligentní uzamčení uchovává informace o neúspěšných pokusů o přihlášení. Po určitou *prahové hodnoty počtu uzamčení*, spuštění *doba trvání uzamčení*. Inteligentní uzamčení odmítne všechny pokusy o přihlášení z útočník během doby trvání uzamčení. Pokud pokračuje útoku, následných neúspěšné pokusy o přihlášení po dobu trvání uzamčení končí výsledek v delší doby uzamčení.

>[!NOTE]
>Uzamčení výchozí prahová hodnota je 10 neúspěšných pokusů o přihlášení. Výchozí doba trvání uzamčení je 60 sekund.

Inteligentní uzamčení také rozlišuje mezi přihlášení z originálního uživatelů a přihlášení z útočníkům. Ve většině případů se zamezí pouze útočníkům. Tato funkce zabrání útočníkům neoprávněnému uzamčení uživatele originálního. Inteligentní uzamčení používá po přihlášení chování, zařízení a prohlížečů uživatelů a dalších signály odlišit uživatele originálního a útočníkům. Algoritmy jsou neustále vylepšení.

Předávací ověřování předává žádosti o ověření hesla k místní službě Active Directory, takže potřebujete zabránit útočníkům uzamykání účtů uživatelů služby Active Directory. Služby Active Directory má svou vlastní zásady uzamčení účtu, konkrétně [prahovou hodnotu uzamknutí účtu](https://technet.microsoft.com/library/hh994574(v=ws.11).aspx) a [resetování čítače uzamčení účtu po](https://technet.microsoft.com/library/hh994568(v=ws.11).aspx) zásady. Vhodně nakonfigurujte Azure AD uzamčení prahovou hodnotu a uzamčení hodnot trvání pro odfiltrování útoky v cloudu, než dosáhnou místní služby Active Directory.

>[!NOTE]
>>Funkce uzamčení inteligentní je zdarma a je _na_ ve výchozím nastavení pro všechny zákazníky. Úprava prahové hodnoty počtu uzamčení a doba trvání uzamčení hodnotami pomocí rozhraní Graph API služby Azure AD však musí být aktivovaná pro Azure AD Premium P2 vašeho klienta. 

K zajištění správně ochrany vašich uživatelů místní služby Active Directory účty, je třeba zajistit, aby:

   * Prahová hodnota pro uzamčení Azure AD je _méně_ než prahovou hodnotu uzamknutí účtu služby Active Directory. Nastavte hodnoty tak, aby prahovou hodnotu uzamknutí účtu služby Active Directory je minimálně dvě nebo tři dobu delší než prahové hodnoty počtu uzamčení Azure AD.
   * Doba trvání uzamčení Azure AD (vyjádřený v sekundách) je _delší_ než služby Active Directory resetu čítače uzamčení účtu po dobu trvání (vyjádřený v minutách).

>[!IMPORTANT]
>Správce aktuálně nemůžete odemknout uživatelské účty cloudu, pokud budou mít byl uzamčen schopnosti inteligentní uzamčení. Správce musí počkat doba trvání uzamčení vyprší.

## <a name="verify-your-active-directory-account-lockout-policies"></a>Zkontrolujte vaše zásady uzamčení účtu služby Active Directory

Postupujte podle následujících pokynů k ověření vaší zásady uzamčení účtů služby Active Directory:

1.  Spusťte nástroj pro správu zásad skupiny.
2.  Upravit zásady skupiny, které se použije pro všechny uživatele, například **výchozí zásady domény**.
3.  Přejděte do **konfigurace počítače** > **zásady** > **nastavení systému Windows** > **nastavení zabezpečení**   >  **Zásady účtů** > **zásada pro uzamčení účtu**.
4.  Ověřte vaší **prahovou hodnotu uzamknutí účtu** a **resetování čítače uzamčení účtu po** hodnoty.

![Zásady uzamčení účtů služby Active Directory](./media/active-directory-aadconnect-pass-through-authentication/pta5.png)

## <a name="use-the-graph-api-to-manage-your-tenants-smart-lockout-values-requires-a-premium-license"></a>Pomocí rozhraní Graph API ke správě vašeho klienta inteligentní uzamčení hodnoty (vyžaduje licenci Premium)

>[!IMPORTANT]
>Úprava Azure AD uzamčení prahovou hodnotu a uzamčení hodnot trvání pomocí rozhraní Graph API je funkce Azure AD Premium P2. Je také musí být globálním správcem na vašeho klienta.

Můžete použít [grafu Explorer](https://developer.microsoft.com/graph/graph-explorer) číst, nastavit a aktualizujte hodnoty Azure AD inteligentní uzamčení. Tyto operace můžete provést taky prostřednictvím kódu programu.

### <a name="view-smart-lockout-values"></a>Zobrazení hodnot inteligentní uzamčení

Postupujte podle těchto kroků k zobrazení hodnot inteligentní uzamčení vašeho klienta:

1. Přihlaste se do Průzkumníka grafu jako globální správce tenanta. Pokud se zobrazí výzva, udělení přístupu požadovaná oprávnění.
2. Vyberte **oprávnění k úpravě**a pak vyberte **Directory.ReadWrite.All** oprávnění.
3. Nakonfigurujte rozhraní Graph API požadavek takto: nastavte verzi **BETA**, typ požadavku na **získat**a adresu URL `https://graph.microsoft.com/beta/<your-tenant-domain>/settings`.
4. Vyberte **spustit dotaz** zobrazíte hodnoty inteligentní uzamčení vašeho klienta. Pokud jste nenastavili před hodnoty vašeho klienta, zobrazí prázdnou sadou.

### <a name="set-smart-lockout-values"></a>Nastavení hodnot inteligentní uzamčení

Postupujte podle těchto kroků můžete nastavte hodnoty inteligentní uzamčení vašeho klienta (vyžadováno pouze prvním):

1. Přihlaste se do Průzkumníka grafu jako globální správce tenanta. Pokud se zobrazí výzva, udělení přístupu požadovaná oprávnění.
2. Vyberte **oprávnění k úpravě**a pak vyberte **Directory.ReadWrite.All** oprávnění.
3. Nakonfigurujte rozhraní Graph API požadavek takto: nastavte verzi **BETA**, typ požadavku na **POST**a adresu URL `https://graph.microsoft.com/beta/<your-tenant-domain>/settings`.
4. Zkopírujte a vložte následující požadavku JSON do **textu žádosti** pole.
5. Vyberte **spustit dotaz** nastavit hodnoty inteligentní uzamčení vašeho klienta.

```
{
  "templateId": "5cf42378-d67d-4f36-ba46-e8b86229381d",
  "values": [
    {
      "name": "LockoutDurationInSeconds",
      "value": "300"
    },
    {
      "name": "LockoutThreshold",
      "value": "5"
    },
    {
      "name" : "BannedPasswordList",
      "value": ""
    },
    {
      "name" : "EnableBannedPasswordCheck",
      "value": "false"
    }
  ]
}
```

>[!NOTE]
>Pokud je nepoužíváte, můžete nechat **BannedPasswordList** a **EnableBannedPasswordCheck** hodnoty jako prázdný (**""**) a **false** v uvedeném pořadí.

Ověřte, že jste nastavili správně hodnoty inteligentní uzamčení vašeho klienta pomocí kroků v [inteligentní uzamčení zobrazení hodnoty](#view-smart-lockout-values).

### <a name="update-smart-lockout-values"></a>Aktualizujte hodnoty inteligentní uzamčení

Postupujte podle těchto kroků provedete aktualizaci hodnoty inteligentní uzamčení vašeho klienta (Pokud je před nastavit):

1. Přihlaste se do Průzkumníka grafu jako globální správce tenanta. Pokud se zobrazí výzva, udělení přístupu požadovaná oprávnění.
2. Vyberte **oprávnění k úpravě**a pak vyberte **Directory.ReadWrite.All** oprávnění.
3. [Postupujte podle těchto kroků k zobrazení vašeho klienta inteligentní uzamčení hodnoty](#view-smart-lockout-values). Kopírování `id` hodnota (GUID) položky s **displayName** jako **PasswordRuleSettings**.
4. Nakonfigurujte rozhraní Graph API požadavek takto: nastavte verzi **BETA**, typ požadavku na **oprava**a adresu URL pro `https://graph.microsoft.com/beta/<your-tenant-domain>/settings/<id>`. Použít identifikátor GUID z kroku 3 pro `<id>`.
5. Zkopírujte a vložte následující požadavku JSON do **textu žádosti** pole. Inteligentní uzamčení hodnoty podle potřeby změňte.
6. Vyberte **spustit dotaz** aktualizujte hodnoty inteligentní uzamčení vašeho klienta.

```
{
  "values": [
    {
      "name": "LockoutDurationInSeconds",
      "value": "30"
    },
    {
      "name": "LockoutThreshold",
      "value": "4"
    },
    {
      "name" : "BannedPasswordList",
      "value": ""
    },
    {
      "name" : "EnableBannedPasswordCheck",
      "value": "false"
    }
  ]
}
```

Ověřte, že jste aktualizovali vašeho klienta inteligentní uzamčení hodnoty správně pomocí kroků v [inteligentní uzamčení zobrazení hodnoty](#view-smart-lockout-values).

## <a name="next-steps"></a>Další postup
[UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): použijte fóru Azure Active Directory do souboru žádosti o nové funkce.
