---
title: Uživatelé nejsou v mé aplikaci zřízeni.
description: Jak řešit běžné problémy s tím, jak se uživatelům nezobrazují v aplikaci Galerie Azure AD, kterou jste nakonfigurovali pro zřizování uživatelů pomocí Azure AD
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: how-to
ms.date: 12/03/2020
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 89a5838524daa1959ecf6b4fe3c17d6175ca8553
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2020
ms.locfileid: "96571772"
---
# <a name="no-users-are-being-provisioned"></a>Neprobíhá zřizování žádných uživatelů 
>[!NOTE]
>Od 04/16/2020 jsme změnili chování pro uživatele s přiřazenou Výchozí rolí přístupu. Podrobnosti najdete níže v části. 
>
Po nakonfigurování automatického zřizování pro aplikaci (včetně ověření, jestli jsou přihlašovací údaje, které se poskytly službě Azure AD pro připojení k aplikaci platné), budou uživatelé nebo skupiny zřízené v aplikaci. Zřizování je určeno následujícími postupy:

-   Kteří uživatelé a skupiny jsou **přiřazeni** k aplikaci. Upozorňujeme, že zřizování vnořených skupin se nepodporuje. Další informace o přiřazení najdete v tématu [přiřazení uživatele nebo skupiny k podnikové aplikaci v Azure Active Directory](../manage-apps/assign-user-or-group-access-portal.md).
-   Bez ohledu na to, jestli jsou **mapování atributů** povolená a nakonfigurovaná tak, aby synchronizoval platné atributy z Azure AD do aplikace. Další informace o mapování atributů najdete v tématu [Přizpůsobení mapování atributů zřizování uživatelů pro aplikace SaaS v Azure Active Directory](customize-application-attributes.md).
-   Bez ohledu na to, zda existuje **Filtr oboru** , který filtruje uživatele na základě konkrétních hodnot atributů. Další informace o filtrech oborů najdete v tématu [zřizování aplikací na základě atributů s filtry oborů](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
  
  
Pokud zjistíte, že uživatelé nejsou zřizování, přečtěte si článek [zřizování protokolů (Preview)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) ve službě Azure AD. Vyhledejte položky protokolu pro konkrétního uživatele.

K protokolům zřizování v Azure Portal můžete získat přístup tak, že **Azure Active Directory** &gt; v části aktivita vyberete Azure Active Directory protokoly zřizování **podnikových aplikací** &gt; **(Preview)** . **Activity** Data zřizování můžete vyhledat na základě jména uživatele nebo identifikátoru v systému zdrojového systému nebo v cílovém systému. Podrobnosti najdete v tématu [zřizování protokolů (Preview)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context). 

Protokoly zřizování zaznamenávají všechny operace prováděné službou zřizování, včetně dotazování Azure AD na přiřazené uživatele v oboru pro zřizování, dotazování cílové aplikace na existenci těchto uživatelů a porovnání uživatelských objektů mezi systémem. Pak na základě porovnání přidejte, aktualizujte nebo zakažte uživatelský účet v cílovém systému.

## <a name="general-problem-areas-with-provisioning-to-consider"></a>Obecné oblasti problému s zřizováním pro zvážení
Níže je uveden seznam obecných oblastí problémů, na které můžete přejít, pokud máte představu o tom, kde začít.

- [Služba zřizování se nezdá spustit](#provisioning-service-does-not-appear-to-start)
- [Zřizování protokolů – uživatelé se přeskočili a nezřídili, i když jsou přiřazeni.](#provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned)

## <a name="provisioning-service-does-not-appear-to-start"></a>Služba zřizování se nezdá spustit
Pokud nastavíte **stav zřizování** **v části** **Azure Active Directory &gt; podnikové aplikace Azure Portal &gt; \[ název aplikace \] &gt;** . Po dalším opětovném načtení se na této stránce nezobrazují žádné další podrobnosti o stavu, je ale nejspíš, že služba běží, ale ještě nedokončila počáteční cyklus. Zkontrolujte výše popsané **protokoly zřizování (Preview)** , abyste zjistili, jaké operace služba provádí, a pokud dojde k chybám.

>[!NOTE]
>Počáteční cyklus může trvat až 20 minut až několik hodin, v závislosti na velikosti adresáře Azure AD a počtu uživatelů v oboru pro zřizování. Následná synchronizace po počátečním cyklu, protože služba zřizování ukládá meze, které představují stav obou systémů po počátečním cyklu. Počáteční cyklus vylepšuje výkon následných synchronizací.
>


## <a name="provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned"></a>Zřizování protokolů – uživatelé se přeskočili a nezřídili, i když jsou přiřazeni.

Když se v protokolech zřizování zobrazí uživatel s názvem "přeskočeno", je důležité zjistit důvod pomocí karty **postup** v protokolu. Níže jsou uvedeny běžné důvody a řešení:

- Byl **nakonfigurován filtr oboru** **, který vyfiltruje uživatele na základě hodnoty atributu**. Další informace o filtrech oborů najdete v tématu věnovaném [filtrům](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)rozsahu.
- **Uživatel je "neefektivně oprávněný".** Pokud se zobrazí tato konkrétní chybová zpráva, je to proto, že došlo k potížím s záznamem přiřazení uživatele uloženým ve službě Azure AD. Pokud chcete tento problém vyřešit, zrušte přiřazení uživatele (nebo skupiny) z aplikace a znovu ho znovu přiřaďte. Další informace o přiřazení najdete v tématu [přiřazení přístupu uživatele nebo skupiny](../manage-apps/assign-user-or-group-access-portal.md).
- **Povinný atribut chybí nebo není vyplněný pro uživatele.** Při nastavování zřizování je důležité vzít v úvahu, že je potřeba zkontrolovat a nakonfigurovat mapování atributů a pracovní postupy, které definují, který uživatel (nebo skupinu) z Azure AD do aplikace přesměruje. Tato konfigurace zahrnuje nastavení "odpovídajících vlastností", která se používá k jednoznačné identifikaci uživatelů nebo skupin mezi oběma systémy a jejich porovnání. Další informace o tomto důležitém procesu najdete v tématu [Přizpůsobení mapování atributů zřizování uživatelů pro aplikace SaaS v Azure Active Directory](customize-application-attributes.md).
- **Mapování atributů pro skupiny:** Zřizování názvu skupiny a podrobností skupiny, a to i u členů, pokud jsou podporovány pro některé aplikace. Tuto funkci můžete povolit nebo zakázat povolením nebo zakázáním **mapování** pro objekty skupiny zobrazené na kartě **zřizování** . Pokud jsou skupiny zřizování povoleny, nezapomeňte zkontrolovat mapování atributů, aby se zajistilo, že se pro "odpovídající ID" používá příslušné pole. ID porovnání může být zobrazované jméno nebo e-mailový alias. Skupina a její členové se nezřídí, pokud je vlastnost odpovídajícího typu prázdná nebo není naplněná pro skupinu ve službě Azure AD.
## <a name="provisioning-users-assigned-to-the-default-access-role"></a>Zřizování uživatelů přiřazených k výchozí roli přístupu
Výchozí rolí aplikace z Galerie se nazývá role výchozí přístup. Historicky nejsou uživatelé přiřazení k této roli zřízeni a jsou v [protokolech zřizování](../reports-monitoring/concept-provisioning-logs.md) označeny jako vynechané, protože jsou "neoprávněným oprávněním". 

**Chování pro konfigurace zřizování vytvořené po 04/16/2020:** Uživatelé přiřazení k výchozí roli přístupu budou vyhodnoceni stejně jako všechny ostatní role. Uživatel, který je přiřazený k výchozímu přístupu, nebude přeskočen jako "neefektivně oprávněný". 

**Chování při zřizování konfigurací vytvořených před 04/16/2020:** V příštích 3 měsících bude chování pokračovat, jak je ještě dnes. Uživatelé s výchozí rolí přístupu se přeskočí jako neefektivně. Po červenci 2020 bude chování pro všechny aplikace jednotné. Nebudeme přeskočí zřizování uživatelů s výchozí rolí přístupu, protože to není efektivně oprávněné. Tuto změnu provede Microsoft, aniž by se vyžadovala žádná akce od zákazníka. Pokud chcete zajistit, aby se tito uživatelé i nadále přeskočili i po této změně, použijte prosím příslušné filtry oborů nebo zrušte přiřazení uživatele z aplikace, abyste zajistili, že jsou mimo rozsah.  

Pokud se o těchto změnách chcete zeptat, obraťte se na provisioningfeedback@microsoft.com
## <a name="next-steps"></a>Další kroky

[Azure AD Connect synchronizace: principy deklarativního zřizování](../hybrid/concept-azure-ad-connect-sync-declarative-provisioning.md)
