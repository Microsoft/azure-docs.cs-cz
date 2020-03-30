---
title: Přidání uživatelů s přímými licencemi do skupinových licencí – Azure AD | Dokumenty společnosti Microsoft
description: Jak migrovat z licencí jednotlivých uživatelů do skupinových licencí pomocí služby Azure Active Directory
services: active-directory
keywords: Licencování Azure AD
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: users-groups-roles
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: sumitp
ms.custom: seohack1;it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6c06d81f2f3f6cee781889d05ae08a1fd125df52
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74025686"
---
# <a name="how-to-migrate-users-with-individual-licenses-to-groups-for-licensing"></a>Jak migrovat uživatele s jednotlivými licencemi do skupin pro licencování

Můžete mít existující licence nasazené uživatelům v organizacích prostřednictvím přímého přiřazení. to znamená pomocí skriptů prostředí PowerShell nebo jiných nástrojů pro přiřazení licencí jednotlivých uživatelů. Než začnete ke správě licencí ve vaší organizaci používat skupinové licencování, můžete pomocí tohoto plánu migrace bez problémů nahradit stávající řešení skupinovými licencemi.

Nejdůležitější je, že byste se měli vyhnout situaci, kdy migrace na skupinové licencování způsobí, že uživatelé dočasně ztratí své aktuálně přiřazené licence. Je třeba se vyhnout jakémukoli procesu, který může vést k odebrání licencí, aby se zabránilo riziku ztráty přístupu uživatelů ke službám a jejich datům.

## <a name="recommended-migration-process"></a>Doporučený proces migrace

1. Máte existující automatizace (například PowerShell) správa přiřazení licencí a odebrání pro uživatele. Nech to běžet tak, jak to je.

1. Vytvořte novou skupinu licencí (nebo se rozhodněte, které existující skupiny chcete použít) a ujistěte se, že všichni požadovaní uživatelé jsou přidáni jako členové.

1. Přiřaďte těmto skupinám požadované licence. Vaším cílem by mělo být odrážet stejný stav licencování, který se na tyto uživatele vztahuje vaše stávající automatizace (například PowerShell).

1. Ověřte, zda byly licence použity pro všechny uživatele v těchto skupinách. Tuto aplikaci lze provést kontrolou stavu zpracování v každé skupině a kontrolou protokolů auditu.

   - Můžete na místě zkontrolovat jednotlivé uživatele při pohledu na jejich licenční údaje. Uvidíte, že mají stejné licence přiřazené "přímo" a "zděděné" ze skupin.

   - Můžete spustit skript prostředí PowerShell a [ověřit, jak jsou licence přiřazovány uživatelům](licensing-group-advanced.md#use-powershell-to-see-who-has-inherited-and-direct-licenses).

   - Pokud je uživateli přiřazena stejná licence produktu přímo i prostřednictvím skupiny, uživatel spotřebuje pouze jednu licenci. Proto nejsou k provedení migrace vyžadovány žádné další licence.

1. Zkontrolujte, zda se nepodařilo nedatovat žádná přiřazení licencí, a to tak, že jednotlivé skupiny nezkontrolují uživatele v chybovém stavu. Další informace naleznete [v tématu Identifikace a řešení problémů s licencemi pro skupinu](licensing-groups-resolve-problems.md).

Zvažte odebrání původních přímých přiřazení. Doporučujeme, abyste to dělali postupně a nejprve sledovali výsledek na podmnožině uživatelů. Pokud byste mohli ponechat původní přímá přiřazení na uživatele, ale když uživatelé opustí své licencované skupiny, ponechávají si přímo přiřazené licence, což nemusí být to, co chcete.

## <a name="an-example"></a>Příklad

Organizace má 1 000 uživatelů. Všichni uživatelé vyžadují licence Office 365 Enterprise E3. V současné době má organizace skript prostředí PowerShell spuštěný místně, přidávání a odebírání licencí uživatelům, jakmile přicházejí a odcházejí. Organizace však chce nahradit skript licencí na základě skupiny, takže licence lze spravovat automaticky azure ad.

Proces migrace by mohl vypadat takto:

1. Pomocí portálu Azure přiřaďte licenci Office 365 E3 skupině **Všichni uživatelé** ve službě Azure AD.

1. Potvrďte, že přiřazení licence bylo dokončeno pro všechny uživatele. Přejděte na stránku s přehledem **skupiny,** vyberte Licence a zkontrolujte stav zpracování v horní části okna **Licence.**

   - Vyhledejte "Nejnovější změny licencí byly použity pro všechny uživatele" potvrdit zpracování bylo dokončeno.

   - Podívejte se na oznámení o všech uživatelích, pro které nebyly licence úspěšně přiřazeny. Doširoka některým uživatelům doširoka vypácejí licence? Mají někteří uživatelé konfliktní licenční plány, které jim brání v dědění skupinových licencí?

1. Namístě zkontrolujte některé uživatele a ověřte, zda mají použity přímé i skupinové licence. Přejděte na stránku profilu uživatele, vyberte **licence**a zkontrolujte stav licencí.

   - Toto je očekávaný stav uživatele během migrace:

      ![očekávaný stav uživatele během migrace](./media/licensing-groups-migrate-users/expected-user-state.png)

     Tím se potvrdí, že uživatel má přímé i zděděné licence. Vidíme, že je přiřazen Ýk3.

   - Vyberte jednotlivé licence a podívejte se, které služby jsou povoleny. Chcete-li ověřit, zda přímé a skupinové licence umožňují uživateli přesně stejné služby, vyberte možnost **Přiřazení**.

1. Po potvrzení, že přímé i skupinové licence jsou rovnocenné, můžete začít odebírat přímé licence uživatelům. Můžete otestovat odebráním pro jednotlivé uživatele na portálu a pak spustit skripty automatizace, aby byly odebrány hromadně. Zde je příklad stejného uživatele s přímými licencemi odebranými prostřednictvím portálu. Všimněte si, že stav licence zůstává beze změny, ale již nevidíme přímá přiřazení.

   ![zkontrolujte, zda jsou přímé licence odebrány](./media/licensing-groups-migrate-users/direct-licenses-removed.png)

## <a name="next-steps"></a>Další kroky

Další informace o dalších scénářích pro správu licencí skupiny:

- [Co je skupinové licencování ve službě Azure Active Directory?](../fundamentals/active-directory-licensing-whatis-azure-portal.md)
- [Přiřazení licencí ke skupině v Azure Active Directory](licensing-groups-assign.md)
- [Identifikace a řešení potíží s licencemi pro skupinu v Azure Active Directory](licensing-groups-resolve-problems.md)
- [Jak migrovat uživatele mezi licencemi produktů pomocí skupinových licencí ve službě Azure Active Directory](licensing-groups-change-licenses.md)
- [Další scénáře licencování na základě skupin v Azure Active Directory](licensing-group-advanced.md)
- [Příklady Prostředí PowerShell pro skupinové licencování ve službě Azure Active Directory](licensing-ps-examples.md)
