---
title: Co je správa privilegovaných identit? - Azure AD | Dokumenty společnosti Microsoft
description: Obsahuje přehled správy privilegovaných identit Azure AD (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: pim
ms.topic: overview
ms.date: 04/21/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: bfb0cc61b61328df86c27498a1007f2372fb9548
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81867452"
---
# <a name="what-is-azure-ad-privileged-identity-management"></a>Co je Azure AD Privileged Identity Management?

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) je služba, která umožňuje spravovat, řídit a monitorovat přístup k důležitým prostředkům ve vaší organizaci. Tyto prostředky zahrnují prostředky ve službě Azure AD, Azure a dalších službách Microsoft Online Services, jako je Office 365 nebo Microsoft Intune.

## <a name="reasons-to-use"></a>Důvody k použití

Organizace chtějí minimalizovat počet osob, které mají přístup k zabezpečeným informacím nebo prostředkům, protože to snižuje pravděpodobnost, že škodlivý objekt actor získá tento přístup nebo oprávněný uživatel neúmyslně ovlivní citlivý prostředek. Uživatelé však i nadále potřebují provádět privilegované operace v Azure AD, Azure, Office 365 nebo aplikacích SaaS. Organizace můžou uživatelům udělit privilegovaný přístup podle potřeby (JIT) k prostředkům Azure a k Azure AD. Je třeba dohledu nad tím, co tito uživatelé dělají se svými oprávněními správce.

## <a name="what-does-it-do"></a>Co to dělá?

Správa privilegovaných identit poskytuje aktivaci rolí na základě času a na schválení, aby se zmírnila rizika nadměrných, zbytečných nebo zneužitých přístupových oprávnění k prostředkům, které vás zajímají. Zde jsou některé z klíčových funkcí správy privilegované identity:

- Poskytněte privilegovaný přístup k prostředkům Azure AD a Azure **v čase**
- Přiřazení **časově vázaného** přístupu k prostředkům pomocí počátečního a koncového data
- Vyžadovat **schválení** k aktivaci privilegovaných rolí
- Vynucení **vícefaktorového ověřování** pro aktivaci libovolné role
- Použití **odůvodnění** k pochopení, proč uživatelé aktivují
- Dostávat **oznámení** při aktivaci privilegovaných rolí
- Provádění **kontrol přístupu,** aby se zajistilo, že uživatelé stále potřebují role
- Stažení **historie auditu** pro interní nebo externí audit

## <a name="what-can-i-do-with-it"></a>Co s tím můžu dělat?

Po nastavení správy privilegovaných identit se v levé navigační nabídce zobrazí **možnosti Úkoly**, **Správa**a **Aktivita.** Jako správce si vyberete mezi správou **rolí Azure AD** a role **prostředků Azure.** Když zvolíte typ rolí, které chcete spravovat, zobrazí se podobná sada možností pro tento typ role.

![Snímek obrazovky se správou privilegovaných identit na webu Azure Portal](./media/pim-configure/pim-quickstart.png)

## <a name="who-can-do-what"></a>Kdo může co dělat?

Pro role Azure AD ve správě privilegovaných identit může přiřazení pro ostatní správce spravovat pouze uživatel, který je ve správci privilegovaných rolí nebo v roli globálního správce. Můžete [udělit přístup ostatním správcům ke správě privilegovaných identit](pim-how-to-give-access-to-pim.md). Globální správci, správci zabezpečení, globální čtenáři a čtenáři zabezpečení mohou také zobrazit přiřazení k rolím Azure AD ve správě privilegovaných identit.

U rolí prostředků Azure ve správě privilegovaných identit může přiřazení pro ostatní správce spravovat pouze správce předplatného, vlastník prostředků nebo správce přístupu uživatelů k prostředkům. Uživatelé, kteří jsou správci privilegovaných rolí, správci zabezpečení nebo čtenáři zabezpečení, nemají ve výchozím nastavení přístup k zobrazení přiřazení rolí prostředků Azure v privilegované správě identit.

## <a name="scenarios"></a>Scénáře

Privileged Identity Management podporuje následující scénáře:

### <a name="privileged-role-administrator-permissions"></a>Oprávnění správce privilegovaných rolí

- Povolit schvalování pro konkrétní role
- Určení uživatelů nebo skupin schvalovatele pro schválení žádostí
- Zobrazit historii žádostí a schválení pro všechny privilegované role

### <a name="approver-permissions"></a>Oprávnění schvalovatele

- Zobrazit žádosti čekající na schválení
- Schválení nebo odmítnutí žádostí o zvýšení role (jednoduché a hromadné)
- Uveďte odůvodnění mého schválení nebo zamítnutí

### <a name="eligible-role-user-permissions"></a>Oprávnění uživatele role

- Požádat o aktivaci role, která vyžaduje schválení
- Zobrazit stav vaší žádosti o aktivaci
- Dokončit požadovanou úlohu v Azure AD, pokud se aktivace schválí

## <a name="terminology"></a>Terminologie

Chcete-li lépe porozumět správě privilegovaných identit a její dokumentaci, měli byste si přečíst následující podmínky.

| Termín nebo koncept | Kategorie přiřazení role | Popis |
| --- | --- | --- |
| Způsobilé | Typ | Přiřazení role, které vyžaduje, aby uživatel provedl jednu nebo více akcí k použití role. Pokud uživatel byl učiněn nárok na roli, to znamená, že mohou aktivovat roli, když potřebují provádět privilegované úkoly. Neexistuje žádný rozdíl v přístupu k někomu, kdo má trvalé versus způsobilé přiřazení role. Jediný rozdíl je v tom, že někteří lidé nepotřebují, aby přístup po celou dobu. |
| Aktivní | Typ | Přiřazení role, které nevyžaduje, aby uživatel provedl žádnou akci k použití role. Uživatelům přiřazeným jako aktivní mají oprávnění přiřazená k roli. |
| aktivovat |  | Proces provádění jedné nebo více akcí za účelem použití role, pro kterou má uživatel nárok. Akce mohou zahrnovat provedení kontroly vícefaktorového ověřování (MFA), poskytnutí obchodního odůvodnění nebo vyžádání schválení od určených schvalovatelů. |
| Přiřazen | Stav | Uživatel, který má aktivní přiřazení role. |
| Aktivován | Stav | Uživatel, který má způsobilé přiřazení role, provedl akce k aktivaci role a je nyní aktivní.  Po aktivaci může uživatel použít roli pro předkonfigurované časové období, než bude muset znovu aktivovat. |
| trvalé způsobilé | Doba trvání | Přiřazení role, kde je uživatel vždy způsobilý k aktivaci role. |
| trvalé aktivní | Doba trvání | Přiřazení role, kde uživatel může vždy použít roli bez provedení jakékoli akce. |
| vyprší způsobilé | Doba trvání | Přiřazení role, kde je uživatel způsobilý k aktivaci role v rámci zadaného počátečního a koncového data. |
| vyprší aktivní | Doba trvání | Přiřazení role, kde může uživatel použít roli bez provedení jakýchkoli akcí v rámci zadaného počátečního a koncového data. |
| přístup just-in-time (JIT) |  | Model, ve kterém uživatelé získají dočasná oprávnění k provádění privilegovaných úloh, což brání škodlivým nebo neoprávněným uživatelům v získání přístupu po vypršení platnosti oprávnění. Přístup je udělen pouze v případě, že jej uživatelé potřebují. |
| zásada přístupu k nejnižším oprávněním |  | Doporučený postup zabezpečení, ve kterém je každému uživateli poskytnuta pouze minimální oprávnění potřebná k provedení úkolů, k jejichž provádění je oprávněn. Tento postup minimalizuje počet globálních správců a místo toho používá konkrétní role správce pro určité scénáře. |

## <a name="license-requirements"></a>Licenční požadavky

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

Informace o licencích pro uživatele naleznete v [tématu Licenční požadavky na používání správy privilegovaných identit](subscription-requirements.md).

## <a name="next-steps"></a>Další kroky

- [Licenční požadavky na používání správy privilegovaných identit](subscription-requirements.md)
- [Zabezpečení privilegovaného přístupu pro hybridní a cloudová nasazení v Azure AD](../users-groups-roles/directory-admin-roles-secure.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json)
- [Nasazení technologie Privileged Identity Management](pim-deployment-plan.md)
