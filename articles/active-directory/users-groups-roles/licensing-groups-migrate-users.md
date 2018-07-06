---
title: Migrace uživatelské licence uživatelů na licencování na základě skupiny v Azure Active Directory | Dokumentace Microsoftu
description: Jak přepínat z jednotlivých uživatelských licencí pro licencování na základě skupiny pomocí služby Azure Active Directory
services: active-directory
keywords: Licencování Azure AD
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.component: users-groups-roles
ms.date: 01/14/2018
ms.author: curtand
ms.custom: seohack1
ms.openlocfilehash: 10851990f26124ae89945d4b56058115cacb81ee
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2018
ms.locfileid: "37861690"
---
# <a name="how-to-add-licensed-users-to-a-group-for-licensing-in-azure-active-directory"></a>Jak přidat uživatele s licencí pro skupinu pro licencování v Azure Active Directory

Máte stávající licence k nasazení uživatelům v organizacích prostřednictvím "přímého přiřazení"; To znamená pomocí skriptů Powershellu nebo jiných nástrojů k přiřazení licencí jednotlivým uživatelům. Pokud chcete začít používat licencování na základě skupiny pro správu licencí ve vaší organizaci, musíte si plán migrace do bez problému nahradit stávající řešení s licencováním na základě skupin.

Nejdůležitějším principem brát v úvahu je, že byste se měli vyhnout situace, kdy migrace na licencování na základě skupiny způsobí uživatele dočasně ztráty aktuálně přiřazené licence. Chcete-li odebrat riziko ztráty přístupu ke službám a jejich data uživatelů, mělo by se vyhnout jakýkoli proces, který může mít za následek odebrání licencí.

## <a name="recommended-migration-process"></a>Proces migrace doporučené

1. Máte existující automatizace (například prostředí PowerShell) Správa licencí přiřazení a odebrání pro uživatele. Necháte spuštěné, jak je.

2. Vytvoření nové skupiny licencí (nebo rozhodnout, které existující skupiny, pro použití) a ujistěte se, že všechny požadované uživatelé přidávají jako členové.

3. Přiřadit požadované licence, které na tyto skupiny. vaším cílem mělo být tak, aby odrážely stejné licencování stavu, ve kterém se těmto uživatelům použití existující automatizace (například prostředí PowerShell).

4. Ověřte, že licence byla použita pro všechny uživatele v těchto skupinách. Tuto aplikaci můžete udělat tak, že zkontrolujete stav zpracování pro každou skupinu a kontrolou protokolů auditu.

  - Můžete místo zaškrtnutí jednotlivých uživatelů podle jejich podrobnosti licence. Uvidíte, že mají stejné licence přiřazené "přímo" a "dědí" ze skupin.

  - Můžete spustit skript prostředí PowerShell, který [ověřte přiřazení licencí uživatelům](licensing-group-advanced.md#use-powershell-to-see-who-has-inherited-and-direct-licenses).

  - Pokud licenci na stejný produkt je přiřazená uživateli i přímo nebo prostřednictvím skupiny, pouze jedna licence se spotřebovává uživatelem. Proto žádné další licence jsou potřebná k provedení migrace.

5. Ověřte, že se žádná přiřazení licencí se nezdařilo kontrolou každé skupiny uživatelů v chybovém stavu. Další informace najdete v tématu [určení a řešení problémů pro skupinu](licensing-groups-resolve-problems.md).

6. Zvažte odebrání původní přímého přiřazení můžete chtít provést postupně, "vlny", nejprve sledovat výsledek na určitou podskupinu uživatelů.

  Původní přímých přiřazení může nechat na uživatele, ale když uživatelé ponechte jejich licencované skupiny zůstanou původní licence, který může být, aby, že je vhodné.

## <a name="an-example"></a>Příklad

Organizace má 1000 uživateli současně. Všichni uživatelé potřebovat licencí Enterprise Mobility + Security (EMS). 200 uživatelů jsou z finančního oddělení a vyžadují licence Office 365 Enterprise E3. Organizace má momentálně Powershellový skript spuštěn v místním prostředí, přidávání a odebírání licence od uživatelů, dokud budou pocházet a přejít. Organizace potřebuje licencování založené na skupině, tak licence je možné spravovat pomocí služby Azure AD automaticky nahradit skriptu.

Tady je proces migrace by mohla vypadat:

1. Pomocí webu Azure portal, přiřadit licence EMS pro **všichni uživatelé** skupiny ve službě Azure AD. Přiřadit licenci E3, abyste **finančního oddělení** skupinu, která obsahuje všechny požadované uživatele.

2. Pro každou skupinu potvrďte, že byla dokončena přiřazení licencí pro všechny uživatele. Přejděte do okna pro každou skupinu, vyberte **licence**a zjistit stav zpracování v horní části **licence** okno.

  - Vyhledejte "Licence nejnovější změny se použily pro všechny uživatele" potvrďte zpracování bylo dokončeno.

  - Hledejte v horní části o všichni uživatelé, pro které licence možná nebyl přiřazen úspěšně oznámení. Jsme dostatek licencí pro některé uživatele? Mají někteří uživatelé konfliktní licence skladových položek, které jim zabránit dědění licencí skupiny?

3. Přímé zkontrolujte někteří uživatelé ověřit, že mají obě s přímým přístupem a skupinových licence použít. Přejděte do okna pro uživatele, vyberte **licence**a zkontrolovat stav licencí.

  - Tento stav byl očekáván typ user je během migrace:

      ![byl očekáván typ user stavu](./media/licensing-groups-migrate-users/expected-user-state.png)

  Tím potvrdíte, že uživatel má licence s přímým přístupem a zděděná. Vidíme, že oba **EMS** a **E3** jsou přiřazeny.

  - Vyberte každou licenci k zobrazení podrobností o povolené služby. To lze použít ke kontrole, pokud licence s přímým přístupem a skupinových povolit přesně stejné plánů služeb pro uživatele.

      ![Zkontrolujte plány služeb](./media/licensing-groups-migrate-users/check-service-plans.png)

4. Po potvrzení, zda jsou ekvivalentní s přímým přístupem a skupiny licencí, můžete začít odebrání přímé licencí od uživatelů. Můžete otestovat tak, že odeberete pro jednotlivé uživatele na portálu a potom spustit skripty pro automatizaci nechat na nich odebrat hromadně. Tady je příklad stejného uživatele s licencí s přímým přístupem odebrat na portálu. Všimněte si, že stav licence zůstane beze změny, ale už vidíme přímých přiřazení.

  ![přímé licence byly odebrány](./media/licensing-groups-migrate-users/direct-licenses-removed.png)


## <a name="next-steps"></a>Další postup

Další informace o další scénáře pro správu licencí pomocí skupin najdete v článku

* [Přiřazení licencí ke skupině ve službě Azure Active Directory](licensing-groups-assign.md)
* [Co je skupina založená na licencování v Azure Active Directory?](../fundamentals/active-directory-licensing-whatis-azure-portal.md)
* [Identifikace a řešení problémů pro skupinu v Azure Active Directory](licensing-groups-resolve-problems.md)
* [Azure Active Directory na základě skupin licencí další scénáře](licensing-group-advanced.md)
