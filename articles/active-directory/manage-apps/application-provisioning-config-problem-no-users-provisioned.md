---
title: Pro aplikaci Galerie Azure AD se nezřídí žádní uživatelé | Microsoft Docs
description: Jak řešit běžné problémy s tím, jak se uživatelům nezobrazují v aplikaci Galerie Azure AD, kterou jste nakonfigurovali pro zřizování uživatelů pomocí Azure AD
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: b80539181e6614283b6170b9cd9d4db85f812a5f
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2019
ms.locfileid: "68879899"
---
# <a name="no-users-are-being-provisioned-to-an-azure-ad-gallery-application"></a>Pro aplikaci Galerie Azure AD se nezřídí žádní uživatelé.
Po nakonfigurování automatického zřizování pro aplikaci (včetně ověření, jestli jsou přihlašovací údaje, které se poskytly službě Azure AD pro připojení k aplikaci platné), budou uživatelé nebo skupiny zřízené v aplikaci. Zřizování je určeno následujícími postupy:

-   Kteří uživatelé a skupiny jsou **přiřazeni** k aplikaci. Upozorňujeme, že zřizování vnořených skupin nebo skupin Office 365 se nepodporuje. Další informace o přiřazení najdete v tématu [přiřazení uživatele nebo skupiny k podnikové aplikaci v Azure Active Directory](assign-user-or-group-access-portal.md).
-   Bez ohledu na to, jestli jsou **mapování atributů** povolená a nakonfigurovaná tak, aby synchronizoval platné atributy z Azure AD do aplikace. Další informace o mapování atributů najdete v tématu [Přizpůsobení mapování atributů zřizování uživatelů pro aplikace SaaS v Azure Active Directory](customize-application-attributes.md).
-   Bez ohledu na to, zda existuje **Filtr oboru** , který filtruje uživatele na základě konkrétních hodnot atributů. Další informace o filtrech oborů najdete v tématu [zřizování aplikací na základě atributů s filtry oborů](define-conditional-rules-for-provisioning-user-accounts.md).
  
Pokud zjistíte, že uživatelé nejsou zřizování, přečtěte si protokoly auditu v Azure AD. Vyhledejte položky protokolu pro konkrétního uživatele.

K protokolům auditu zřizování se dá v Azure Portal přihlašovat na kartě  **&gt; Azure Active Directory podnikové aplikace\] &gt; \[ &gt; název aplikace** . Vyfiltrujte protokoly v kategorii **zřizování účtů** , aby se zobrazily jenom události zřizování pro tuto aplikaci. Uživatele můžete vyhledat na základě "odpovídajícího ID", které bylo pro ně nakonfigurováno v mapování atributů. Pokud jste například nakonfigurovali atribut "hlavní název uživatele" nebo "e-mailová adresa" jako atribut pro porovnání na straně Azure AD a uživatel, který není zřizovánímaudrey@contoso.com, vyhledá v protokolech auditu "audrey@contoso.com" a zkontroluje položky. vrátil.

Protokoly zřizování zaznamenají všechny operace prováděné službou zřizování, včetně dotazování Azure AD na přiřazené uživatele v oboru pro zřizování, dotazování cílové aplikace na existenci těchto uživatelů a porovnání uživatelských objektů. mezi systémem. Pak na základě porovnání přidejte, aktualizujte nebo zakažte uživatelský účet v cílovém systému.

## <a name="general-problem-areas-with-provisioning-to-consider"></a>Obecné oblasti problému s zřizováním pro zvážení
Níže je uveden seznam obecných oblastí problémů, na které můžete přejít, pokud máte představu o tom, kde začít.

- [Služba zřizování se nezdá spustit](#provisioning-service-does-not-appear-to-start)
- [Protokoly auditu – uživatelé se přeskočí a nezřídí, i když se jim přiřadí.](#audit-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned)

## <a name="provisioning-service-does-not-appear-to-start"></a>Služba zřizování se nezdá spustit
Pokud nastavíte **stav zřizování** na zapnuto, v části **Azure Active Directory &gt; podnikové\] aplikace &gt; \[název &gt;aplikace** Azure Portal . Po dalším opětovném načtení se na této stránce nezobrazují žádné další podrobnosti o stavu, je ale nejspíš, že služba běží, ale ještě nedokončila počáteční synchronizaci. Zkontrolujte **protokoly auditu** popsané výše a určete, jaké operace služba provádí, a pokud dojde k chybám.

>[!NOTE]
>Počáteční synchronizace může trvat až 20 minut až několik hodin, v závislosti na velikosti adresáře Azure AD a počtu uživatelů v oboru pro zřizování. Následná synchronizace po počáteční synchronizaci je rychlejší, protože služba zřizování ukládá meze, které představují stav obou systémů po počáteční synchronizaci. Počáteční synchronizace vylepšuje výkon následných synchronizací.
>


## <a name="audit-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned"></a>Protokoly auditu – uživatelé se přeskočili a nezřídili, i když jsou přiřazeni.

Pokud se uživateli v protokolech auditu zobrazí zpráva "přeskočeno", je důležité si přečíst rozšířené podrobnosti v protokolu, abyste zjistili důvod. Níže jsou uvedeny běžné důvody a řešení:

- Byl **nakonfigurován filtr oborů** . **který filtruje uživatele na základě hodnoty atributu**. Další informace o filtrech oborů najdete v [](define-conditional-rules-for-provisioning-user-accounts.md)tématu věnovaném filtrům rozsahu.
- **Uživatel je "neefektivně oprávněný".** Pokud se zobrazí tato konkrétní chybová zpráva, je to proto, že došlo k potížím s záznamem přiřazení uživatele uloženým ve službě Azure AD. Pokud chcete tento problém vyřešit, zrušte přiřazení uživatele (nebo skupiny) z aplikace a znovu ho znovu přiřaďte. Další informace o přiřazení najdete v tématu [přiřazení přístupu uživatele nebo skupiny](assign-user-or-group-access-portal.md).
- **Povinný atribut chybí nebo není vyplněný pro uživatele.** Při nastavování zřizování je důležité vzít v úvahu, že je potřeba zkontrolovat a nakonfigurovat mapování atributů a pracovní postupy, které definují, který uživatel (nebo skupinu) z Azure AD do aplikace přesměruje. Tato konfigurace zahrnuje nastavení "odpovídajících vlastností", která se používá k jednoznačné identifikaci uživatelů nebo skupin mezi oběma systémy a jejich porovnání. Další informace o tomto důležitém procesu najdete v tématu [Přizpůsobení mapování atributů zřizování uživatelů pro aplikace SaaS v Azure Active Directory](customize-application-attributes.md).
- **Mapování atributů pro skupiny:** Zřizování názvu skupiny a podrobností skupiny, a to i u členů, pokud jsou podporovány pro některé aplikace. Tuto funkci můžete povolit nebo zakázat povolením nebo zakázáním **mapování** pro objekty skupiny zobrazené na kartě **zřizování** . Pokud jsou skupiny zřizování povoleny, nezapomeňte zkontrolovat mapování atributů, aby se zajistilo, že se pro "odpovídající ID" používá příslušné pole. ID porovnání může být zobrazované jméno nebo e-mailový alias. Skupina a její členové se nezřídí, pokud je vlastnost odpovídajícího typu prázdná nebo není naplněná pro skupinu ve službě Azure AD.

## <a name="next-steps"></a>Další postup

[Synchronizace Azure AD Connect: Principy deklarativního zřizování](../hybrid/concept-azure-ad-connect-sync-declarative-provisioning.md)
