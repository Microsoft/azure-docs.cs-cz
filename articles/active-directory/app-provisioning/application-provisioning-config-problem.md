---
title: Problém s konfigurací zřizování uživatelů pro aplikaci Galerie Azure AD
description: Řešení běžných problémů při konfiguraci zřizování uživatelů pro aplikaci, která je už uvedená v galerii aplikací Azure AD
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 09/03/2019
ms.author: kenwith
ms.reviewer: asteen
ms.openlocfilehash: 306c3771c0392bbc97260a726e153cfd385cebcd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84782122"
---
# <a name="problem-configuring-user-provisioning-to-an-azure-ad-gallery-application"></a>Problém s konfigurací zřizování uživatelů pro aplikaci Galerie Azure AD

Konfigurace [automatického zřizování uživatelů](user-provisioning.md) pro aplikaci (tam, kde se podporuje) vyžaduje, aby se při přípravě aplikace na Automatické zřizování použily konkrétní pokyny. Potom můžete pomocí Azure Portal nakonfigurovat službu zřizování k synchronizaci uživatelských účtů s aplikací.

Vždy byste měli začít hledáním kurzu nastavení, který je specifický pro nastavení vaší aplikace. Pak postupujte podle těchto kroků a nakonfigurujte aplikaci i službu Azure AD, aby vytvořila zřizovací připojení. Seznam kurzů aplikací najdete v tématu o tom, [jak integrovat aplikace SaaS s Azure Active Directory](../saas-apps/tutorial-list.md).

## <a name="how-to-see-if-provisioning-is-working"></a>Jak zjistit, jestli zřizování funguje 

Jakmile je služba nakonfigurovaná, dají se většinu přehledů provozu služby vykreslovat ze dvou míst:

-   **Protokoly zřizování (Preview)** – [protokoly zřizování](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) zaznamenávají všechny operace prováděné službou zřizování, včetně dotazování Azure AD pro přiřazené uživatele, kteří jsou v oboru pro zřizování. Dotazování cílové aplikace na existenci těchto uživatelů, porovnání uživatelských objektů mezi systémem. Pak na základě porovnání přidejte, aktualizujte nebo zakažte uživatelský účet v cílovém systému. K protokolům zřizování v Azure Portal můžete získat přístup tak, že **Azure Active Directory** &gt; v části aktivita vyberete Azure Active Directory protokoly zřizování **podnikových aplikací** &gt; **(Preview)** . **Activity**

-   **Aktuální stav –** Souhrn posledního spuštění zřizování pro danou aplikaci najdete v části **Azure Active Directory &gt; podnikové aplikace &gt; \[ název aplikace název aplikace \] &gt; ** , v dolní části obrazovky pod nastavením služby. V části aktuální stav se zobrazuje, zda zřizovací cyklus zahájil zřizování uživatelských účtů. Průběh cyklu můžete sledovat, zjistit, kolik uživatelů a skupin se zřídilo, a zjistit, kolik rolí se vytvořilo. Pokud dojde k chybám, podrobnosti najdete v [zřizovacích protokolech (. /Reports-monitoring/Concept-Provisioning-Logs.MD? Context = Azure/Active-Directory/Manage-Apps/Context/Manage-Apps-Context).

## <a name="general-problem-areas-with-provisioning-to-consider"></a>Obecné oblasti problému s zřizováním pro zvážení

Níže je uveden seznam obecných oblastí problémů, na které můžete přejít, pokud máte představu o tom, kde začít.

* [Služba zřizování se nezdá spustit](#provisioning-service-does-not-appear-to-start)
* Nejde Uložit konfiguraci z důvodu nefunkčnosti přihlašovacích údajů aplikace.
* [Zřizování protokolů – uživatelé jsou "přeskočeni" a nejsou zřízeni, i když jsou přiřazeni.](#provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned)

## <a name="provisioning-service-does-not-appear-to-start"></a>Služba zřizování se nezdá spustit

Pokud nastavíte **stav zřizování** **v části** **Azure Active Directory &gt; podnikové aplikace Azure Portal &gt; \[ název aplikace \] &gt; ** . Po dalším opětovném načtení se na této stránce nezobrazují žádné další podrobnosti o stavu. Je nejspíš, že služba běží, ale ještě nedokončila počáteční cyklus. Zkontrolujte **protokoly zřizování** popsané výše a určete, jaké operace služba provádí, a pokud dojde k chybám.

>[!NOTE]
>Počáteční cyklus může trvat až 20 minut až několik hodin, v závislosti na velikosti adresáře Azure AD a počtu uživatelů v oboru pro zřizování. Následná synchronizace po počátečním cyklu, protože služba zřizování ukládá meze, které reprezentují stav obou systémů po počátečním cyklu, což zvyšuje výkon následných synchronizací.
>
>

## <a name="cant-save-configuration-due-to-app-credentials-not-working"></a>Nejde Uložit konfiguraci z důvodu nefunkčnosti přihlašovacích údajů aplikace.

Aby zřizování fungovalo, vyžaduje Azure AD platné přihlašovací údaje, které jim umožní připojit se k rozhraní API pro správu uživatelů, které poskytuje tato aplikace. Pokud tyto přihlašovací údaje nefungují nebo neznáte, co jsou, přečtěte si kurz nastavení této aplikace popsané výše.

## <a name="provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned"></a>Zřizování protokolů – uživatelé se přeskočili a nezřídili, i když jsou přiřazeni.

Pokud se uživateli v protokolech zřizování zobrazí zpráva "přeskočeno", je velmi důležité si přečíst rozšířené podrobnosti v protokolu, abyste zjistili důvod. Níže jsou uvedeny běžné důvody a řešení:

- Byl **nakonfigurován filtr oboru** **, který vyfiltruje uživatele na základě hodnoty atributu**. Další informace najdete v tématu [zřizování aplikace na základě atributů s filtry oborů](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

- **Uživatel je "neefektivně oprávněný".** Pokud se zobrazí tato konkrétní chybová zpráva, je to proto, že došlo k potížím s záznamem přiřazení uživatele uloženým ve službě Azure AD. Pokud chcete tento problém vyřešit, zrušte přiřazení uživatele (nebo skupiny) z aplikace a znovu ho přiřaďte. Další informace najdete v tématu [přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md).

- **Povinný atribut chybí nebo není vyplněný pro uživatele.** Důležitou věcí, kterou je potřeba zvážit při nastavování zřizování, je zkontrolovat a nakonfigurovat mapování atributů a pracovní postupy, které definují, který uživatel (nebo skupinu) z Azure AD do aplikace přesměruje. Zahrnuje nastavení "odpovídající vlastnost", která se používá k jednoznačné identifikaci uživatelů nebo skupin mezi oběma systémy a jejich porovnání. Další informace o tomto důležitém procesu najdete v tématu [přizpůsobení atributů zřizování uživatelů – mapování](../app-provisioning/customize-application-attributes.md).

  * **Mapování atributů pro skupiny:** Zřizování názvu skupiny a podrobností skupiny, a to i u členů, pokud jsou podporovány pro některé aplikace. Tuto funkci můžete povolit nebo zakázat povolením nebo zakázáním **mapování** pro objekty skupiny zobrazené na kartě **zřizování** . Pokud jsou skupiny zřizování povoleny, nezapomeňte zkontrolovat mapování atributů, aby se zajistilo, že se pro "odpovídající ID" používá příslušné pole. Může to být zobrazované jméno nebo e-mailový alias), protože skupina a její členové se nezřídí, pokud je vlastnost Matching prázdná nebo není naplněná pro skupinu ve službě Azure AD.

## <a name="next-steps"></a>Další kroky
[Automatizace zřizování a rušení uživatelů pro aplikace SaaS pomocí Azure Active Directory](user-provisioning.md)
