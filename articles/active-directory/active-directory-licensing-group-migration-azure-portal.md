---
title: Migraci uživatelů licence uživatele na základě skupiny licencí v Azure Active Directory pro | Microsoft Docs
description: Jak přepínat z jednotlivých uživatelských licencí na základě skupiny licencí pomocí služby Azure Active Directory pro
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
ms.openlocfilehash: ed95ba1329ba3052fab3d81327d23475e9902d8a
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
ms.locfileid: "33762216"
---
# <a name="how-to-add-licensed-users-to-a-group-for-licensing-in-azure-active-directory"></a>Postup přidání licencovaní uživatelé pro skupinu pro licencování v Azure Active Directory

Máte stávající licence, nasazení uživatelům v organizacích prostřednictvím "přímé přiřazení"; To znamená přiřadit licence pro jednotlivé uživatele pomocí skriptů prostředí PowerShell nebo jiných nástrojů. Pokud chcete začít používat na základě skupiny licencí ke správě licencí ve vaší organizaci, budete potřebovat plán migrace bezproblémově nahradit existující řešení na základě skupin licencí.

Nejdůležitější třeba vzít v úvahu je, že vyhněte situaci, kdy migrace na základě skupiny licencí pro nebudou uživatelé dočasně došlo ke ztrátě jejich aktuálně přiřazené licence. Chcete-li odebrat riziko ztráty přístupu ke službám a jejich data uživatelů je nutno jakýkoli proces, který může mít za následek odebrání licencí.

## <a name="recommended-migration-process"></a>Proces migrace doporučené

1. Máte existující automatizace (například prostředí PowerShell) Správa licencí přiřazení a odebrání pro uživatele. Necháte spuštěné, jako je.

2. Vytvořit novou skupinu licencování (nebo rozhodnout, které stávající skupiny pro použití) a ujistěte se, že všechny požadované uživatelé jsou přidány jako členové.

3. Přiřadit požadované licence na tyto skupiny. vaším cílem mělo být tak, aby odrážela stejné licencování stavu, ve kterém na uživatele, je použití vaší existující automatizace (například prostředí PowerShell).

4. Ověřte, že licence byla použita pro všechny uživatele v těchto skupinách. To lze provést kontrolu stavu zpracování pro každou skupinu a kontroly protokolů auditu.

  - Můžete místo kontrola jednotlivých uživatelů podle jejich licenčních podrobnosti. Zobrazí se, že mají stejný "přímo" přiřazené licence a "zděděná" ze skupin.

  - Můžete spustit skript prostředí PowerShell k [ověřte, jak jsou licence přiřazovat k uživatelům](active-directory-licensing-group-advanced.md#use-powershell-to-see-who-has-inherited-and-direct-licenses).

  - Když stejné licenci produktu je přiřazen k uživateli obě, přímo a prostřednictvím skupiny, je pouze jednu licenci spotřebovávána uživatele. Proto žádné další licence, které jsou potřebná k provedení migrace.

5. Ověřte, že žádná přiřazení licence se nezdařilo kontrolou každou skupinu pro uživatele v chybovém stavu. Další informace najdete v tématu [identifikuje a řešení potíží s licencí pro skupinu](active-directory-licensing-group-problem-resolution-azure-portal.md).

6. Zvažte odebrání původní přímé přiřazení; Můžete to udělat postupně, "témata", nejprve monitorování výsledek na určitou podskupinu uživatelů.

  Původní přímé přiřazení může nechat na uživatele, ale když uživatelé nechte jejich licencovanou skupiny stále zachovají původní licenci, která může být, aby, že chcete.

## <a name="an-example"></a>Příklad

Máme organizaci s 1 000 uživatelů. Všichni uživatelé vyžadují Enterprise Mobility + Security (EMS) licence. 200 uživatele z finančního oddělení a vyžadují Office 365 Enterprise E3 licence. Máme skript prostředí PowerShell, který je spuštěn místně přidávání a odebírání licence od uživatelů, dokud budou pocházet a přejděte. Chceme nahraďte skript na základě skupiny licencí, licence jsou spravovány automaticky službou Azure AD.

Zde je, jak může vypadat proces migrace:

1. Pomocí portálu Azure přiřadit licence EMS pro **všichni uživatelé** skupiny ve službě Azure AD. Přiřadit licence E3 na **finančního oddělení** skupinu, která obsahuje všechny požadované uživatele.

2. Pro každou skupinu potvrďte, že se dokončila přiřazení licence pro všechny uživatele. Přejděte do okna pro každou skupinu, vyberte **licence**a zkontrolujte stav zpracování v horní části **licence** okno.

  - Vyhledejte "Nejnovější licence změny provedeny pro všechny uživatele" potvrďte zpracování byla dokončena.

  - Hledejte v horní části o všechny uživatele, pro které licence pravděpodobně nebyla přiřazena úspěšně oznámení. Jsme dostatek licencí pro některé uživatele? Mají někteří uživatelé konfliktní licence SKU, které brání dědění skupiny licencí?

3. Místo zkontrolujte někteří uživatelé ověřit, zda mají obě direct a skupinu licencí použít. Přejděte do okna pro uživatele, vyberte **licence**a zkontrolujte stav licence.

  - Toto je očekávané uživatelské stavu během migrace:

      ![Stav očekávané uživatele](media/active-directory-licensing-group-migration-azure-portal/expected-user-state.png)

  Tím potvrdí, že uživatel má přímé a zděděné licence. Vidíte, jak **EMS** a **E3** jsou přiřazeny.

  - Vyberte každé licence a zobrazit podrobnosti o povolené služby. To slouží ke kontrole, pokud direct a skupinu licencí povolit přesně stejnou službu plány pro uživatele.

      ![Zkontrolujte plány služeb](media/active-directory-licensing-group-migration-azure-portal/check-service-plans.png)

4. Po potvrzení, že odpovídají direct a skupinu licencí, můžete začít odebrání přímé licence od uživatelů. Můžete si otestovat to jejich odebráním pro jednotlivé uživatele v portálu a spusťte skripty pro automatizaci tak, aby měl je v hromadné odebrána. Tady je příklad stejného uživatele s přímé licence odebrat prostřednictvím portálu. Všimněte si, že stav licence zůstává beze změny, ale už vidíme přímé přiřazení.

  ![odebrat přímý licencí](media/active-directory-licensing-group-migration-azure-portal/direct-licenses-removed.png)


## <a name="next-steps"></a>Další postup

Další informace o scénáře pro správu licencí pomocí skupin, přečtěte si téma

* [Přiřazování licencí pro skupinu v Azure Active Directory](active-directory-licensing-group-assignment-azure-portal.md)
* [Co je na základě skupin licencování v Azure Active Directory?](active-directory-licensing-whatis-azure-portal.md)
* [Identifikace a řešení potíží s licencí pro skupinu v Azure Active Directory](active-directory-licensing-group-problem-resolution-azure-portal.md)
* [Azure Active Directory na základě skupin licencí další scénáře](active-directory-licensing-group-advanced.md)
