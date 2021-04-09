---
title: Přidání uživatelů s přímými licencemi do skupin licencování – Azure AD | Microsoft Docs
description: Postup migrace z individuálních uživatelských licencí na licencování na základě skupin pomocí Azure Active Directory
services: active-directory
keywords: Licencování Azure AD
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: enterprise-users
ms.topic: how-to
ms.workload: identity
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: sumitp
ms.custom: seohack1;it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: adc80cf579ce3086abd4171b065f859acd0b9294
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96546501"
---
# <a name="how-to-migrate-users-with-individual-licenses-to-groups-for-licensing"></a>Postup migrace uživatelů s jednotlivými licencemi do skupin pro licencování

Mohli jste mít k uživatelům v organizacích nasazené stávající licence prostřednictvím přímého přiřazení; To znamená, že pomocí skriptů PowerShellu nebo jiných nástrojů přiřadíte jednotlivé uživatelské licence. Než začnete používat licencování na základě skupin pro správu licencí ve vaší organizaci, můžete tento plán migrace použít k bezproblémovému nahrazení stávajících řešení pomocí licencování na základě skupin.

Je důležité si uvědomit, že byste se měli vyhnout situaci, kdy se migrace na licencování na základě skupin bude povést k tomu, že uživatelé dočasně ztratí své aktuálně přiřazené licence. Jakýkoli proces, který by mohl způsobit odebrání licencí, by se zabránilo odebrání rizika, že uživatelé ztratili přístup ke službám a jejich datům.

## <a name="recommended-migration-process"></a>Doporučený proces migrace

1. Máte existující automatizaci (například PowerShell) pro správu přiřazení licencí a odebírání uživatelů. Ponechte ho spuštěný tak, jak je.

1. Vytvořte novou skupinu licencí (nebo se rozhodněte, které existující skupiny se mají použít) a ujistěte se, že jsou všichni povinní Uživatelé přidáni jako členové.

1. Přiřaďte těmto skupinám požadované licence. Váš cíl by měl odpovídat stejnému stavu licencování, že se pro tyto uživatele používá vaše stávající automatizace (například PowerShell).

1. Ověřte, zda byly licence aplikovány na všechny uživatele v těchto skupinách. Tato aplikace se dá udělat tak, že zkontrolujete stav zpracování v každé skupině a zkontrolujete protokoly auditu.

   - Kontrolu jednotlivých uživatelů si můžete prohlédnout na základě jejich licenčních podrobností. Uvidíte, že mají stejné licence přiřazené přímo a zděděné ze skupin.

   - Můžete spustit skript prostředí PowerShell a ověřit tak, [jak jsou licence přiřazeny uživatelům](licensing-group-advanced.md#use-powershell-to-see-who-has-inherited-and-direct-licenses).

   - Pokud k uživateli přiřadíte stejnou licenci k produktu přímo i prostřednictvím skupiny, bude uživatel používat jenom jednu licenci. Proto pro provádění migrace nejsou potřeba žádné další licence.

1. Ověřte, že se nezdařila žádná přiřazení licencí, a to zaškrtnutím každé skupiny pro uživatele v chybovém stavu. Další informace najdete v tématu [identifikace a řešení problémů s licencí pro skupinu](licensing-groups-resolve-problems.md).

Zvažte odebrání původních přímých přiřazení. Doporučujeme vám postupovat postupně a monitorovat výsledek u podmnožiny uživatelů jako první. Pokud byste mohli ponechat původní Přímá přiřazení pro uživatele, ale když uživatelé odejdou z licencovaných skupin, uchovávají jim přímo přiřazené licence, které nemusí být vhodné.

## <a name="an-example"></a>Příklad

Organizace má 1 000 uživatelů. Všichni uživatelé vyžadují licence Office 365 Enterprise E3. V současné době má organizace spuštěný skript PowerShellu v místním prostředí a při přidávání a odebírání licencí od uživatelů po jejich uvedení na adresu. Organizace ale chce nahradit skript licencováním na základě skupin, aby bylo možné licence spravovat automaticky pomocí Azure AD.

Postup migrace by měl vypadat takto:

1. Pomocí Azure Portal přiřaďte licenci Office 365 E3 skupině **Všichni uživatelé** ve službě Azure AD.

1. Potvrďte, že přiřazení licence bylo dokončeno pro všechny uživatele. Pro skupinu klikněte na stránku Přehled, vyberte **licence** a v horní části okna **licence** zaškrtněte stav zpracování.

   - Vyhledejte "poslední změny licencí byly aplikovány na všechny uživatele" pro potvrzení dokončení zpracování.

   - Vyhledejte oznámení v horní části o všech uživatelích, pro které se licence pravděpodobně úspěšně nepřiřadily. Vyčerpali jsme licence pro některé uživatele? Mají někteří uživatelé konfliktní licenční plány, které jim brání v dědění skupinových licencí?

1. Pokud si chcete ověřit, že se používají přímé i skupinové licence, zkontrolujte, jestli někteří uživatelé mají. Přejít na stránku profil pro uživatele, vyberte **licence** a prověřte stav licencí.

   - Toto je očekávaný stav uživatele při migraci:

      ![očekávaný stav uživatele během migrace](./media/licensing-groups-migrate-users/expected-user-state.png)

     Tím se potvrdí, že uživatel má přímé i zděděné licence. Vidíte, že je přiřazená sada Office 365 E3.

   - Vyberte jednotlivé licence, abyste viděli, které služby jsou povolené. Chcete-li ověřit, že licence Direct a Group pro uživatele umožňují přesně stejné služby, vyberte možnost **přiřazení**.

1. Po potvrzení, že jsou přímé i skupinové licence rovnocenné, můžete začít s odebráním přímých licencí od uživatelů. Můžete to otestovat tak, že je odeberete pro jednotlivé uživatele na portálu a potom spustíte skripty pro automatizaci, aby se odebraly hromadně. Tady je příklad stejného uživatele s přímými licencemi odebranými prostřednictvím portálu. Všimněte si, že stav licence zůstane beze změny, ale už nevidíme Přímá přiřazení.

   ![potvrzení odebrání přímých licencí](./media/licensing-groups-migrate-users/direct-licenses-removed.png)

## <a name="next-steps"></a>Další kroky

Další informace o dalších scénářích pro správu licencí skupin:

- [Co je licencování na základě skupin v Azure Active Directory?](../fundamentals/active-directory-licensing-whatis-azure-portal.md)
- [Přiřazení licencí ke skupině v Azure Active Directory](licensing-groups-assign.md)
- [Identifikace a řešení potíží s licencemi pro skupinu v Azure Active Directory](licensing-groups-resolve-problems.md)
- [Postup migrace uživatelů mezi licencemi k produktu pomocí licencování na základě skupin v Azure Active Directory](licensing-groups-change-licenses.md)
- [Další scénáře licencování na základě skupin v Azure Active Directory](licensing-group-advanced.md)
- [Příklady prostředí PowerShell pro licencování na základě skupin v Azure Active Directory](licensing-ps-examples.md)
