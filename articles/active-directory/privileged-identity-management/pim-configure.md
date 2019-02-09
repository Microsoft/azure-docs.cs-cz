---
title: Co je Azure AD Privileged Identity Management? | Dokumenty Microsoft
description: Tento článek obsahuje přehled služby Azure Active Directory Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: pim
ms.topic: overview
ms.date: 01/16/2019
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 7c2c8967cbdb404ca23f860f019cfbb6cce5415b
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2019
ms.locfileid: "55982722"
---
# <a name="what-is-azure-ad-privileged-identity-management"></a>Co je Azure AD Privileged Identity Management?

Privileged Identity Management (PIM) ve Azure Active Directory (Azure AD) je služba, která vám umožní spravovat, řídit a monitorovat přístup k důležitým prostředkům ve vaší organizaci. To zahrnuje přístup k prostředkům v Azure AD, prostředkům Azure a dalším online službám Microsoftu jako Office 365 nebo Microsoft Intune.

## <a name="why-should-i-use-pim"></a>Proč by měl používat PIM?

Organizace chcete minimalizovat počet uživatelů, kteří mají přístup k zabezpečeným informace nebo materiály, protože, které snižují riziko škodlivý objekt actor tento přístup nebo neúmyslně vliv na prostředek citlivé autorizovaný uživatel. Uživatelé však i nadále potřebují provádět privilegované operace v Azure AD, Azure, Office 365 nebo aplikacích SaaS. Uživatelům můžete udělit organizace just-in-time (JIT) privilegovaný přístup k prostředkům Azure a Azure AD. Je potřeba pro dohled nad pro tyto činnosti uživatelů s jejich oprávněními správce. PIM pomáhá zmírnit riziko nadměrné, zbytečným nebo potenciálně nebezpečného přístupová práva.

## <a name="what-can-i-do-with-pim"></a>Co můžu dělat s PIM?

PIM v podstatě umožňuje správu na kdo, co, kdy, kde a proč pro prostředky, které vás zajímají. Tady jsou některé klíčové funkce PIM:

- Zadejte **just-in-time** privilegovaný přístup k Azure AD a prostředků Azure
- Přiřadit **časově omezenou** přístupu k prostředkům pomocí počáteční a koncové datum
- Vyžadovat **schválení** aktivovat privilegované role
- Vynutit **ověřování službou Multi-Factor Authentication** aktivaci všechny role
- Použití **odůvodnění** pochopit, proč uživatelé aktivovat
- Získat **oznámení** při aktivaci privilegované role
- Chování **kontrol přístupu** zajistit uživatelé stále potřebují role
- Stáhněte si **historie auditu** pro interní nebo externí auditu

## <a name="prerequisites"></a>Požadavky

Pokud chcete používat PIM, musí mít jeden z následujících placené nebo zkušební licence. Další informace najdete v tématu [Co je Azure Active Directory?](../fundamentals/active-directory-whatis.md).

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5

Informace o licencích pro uživatele najdete v tématu [licenční požadavky pro použití PIM](subscription-requirements.md).

## <a name="terminology"></a>Terminologie

Abyste lépe pochopili PIM a jeho dokumentace, měli byste zkontrolovat následující podmínky.

| Ukončení nebo koncept | Kategorie přiřazení role | Popis |
| --- | --- | --- |
| Oprávněné | Type | Přiřazení role, které musí uživatel provést jeden nebo více akcí umožňujících využití roli. Pokud uživatel provedl oprávněné pro roli, znamená to, že se aktivovat roli, kdy potřebují k provedení privilegovaných úloh. Není žádný rozdíl v přístupu k někdo s trvalou oproti přiřazení oprávněné role. Jediným rozdílem je, že někteří uživatelé nepotřebují tento přístup neustále. |
| aktivní | Type | Přiřazení role, která nevyžaduje, aby uživatel podnikat žádné kroky, chcete-li použít roli. Přiřazeno jako aktivní uživatelé mají oprávnění přiřazená k roli. |
| aktivovat |  | Proces provedení jedné nebo více akcí umožňujících využití roli, jejímž je uživatel nárok. Akce může zahrnovat provedení kontroly ověřování službou Multi-Factor Authentication (MFA), poskytuje obchodní odůvodnění, nebo z určených schvalovatelů odesílání žádostí o schválení. |
| Přiřazeno | Stav | Uživatel, který má aktivní přiřazení role. |
| Aktivovat | Stav | Jako uživatel, který má přiřazení oprávněné role provádět akce, které chcete aktivovat roli a je teď aktivní.  Po aktivaci, může uživatel použít roli zadanou dobu období sady-předtím, než je nutné znovu aktivovat. |
| trvalé oprávněné | Doba trvání | Přiřazení role, kde je uživatel vždy nárok na aktivaci role. |
| Trvalá aktivní | Doba trvání | Přiřazení role, ve kterém může uživatel vždy používat roli bez provedení nějaké akce. |
| vypršení platnosti oprávnění | Doba trvání | Přiřazení role, kde je uživatel nárok na aktivaci role v rámci zadané počáteční a koncové datum. |
| vypršení platnosti je aktivní | Doba trvání | Přiřazení role, ve kterém může uživatel používat roli bez provedení libovolné akce v rámci zadané počáteční a koncové datum. |
| přístup k just-in-time (JIT) |  | Model ve kterém uživatelé dostanou dočasné oprávnění k provedení privilegovaných úlohy, což zabrání zlými úmysly nebo neoprávněným uživatelům v získání přístupu po vypršení platnosti oprávnění. Přístup je udělen jenom v případě, že ji uživatelé potřebovat. |
| Princip nejnižších oprávnění přístupu |  | Doporučené zabezpečení praxe, ve kterém je každý uživatel součástí pouze minimální oprávnění jsou nutné k provedení úlohy, které mají oprávnění k provedení. Tento postup minimalizuje počet globálních správců a místo toho používá konkrétní správce role pro určité scénáře. |

## <a name="what-does-pim-look-like"></a>Jak vypadá PIM?

Po nastavení PIM, zobrazí se vám **úlohy**, **spravovat**, a **aktivity** možnosti v levé navigační nabídce. Jako správce, vyberete možnost mezi správou **role Azure AD** a **prostředků Azure** role. Když vyberete typ role pro správu, uvidíte podobnou sadu možností pro daný typ role.

![Snímek obrazovky s PIM na portálu Azure portal](./media/pim-configure/pim-overview.png)

## <a name="who-can-do-what-in-pim"></a>Kdo může co dělat v PIM?

Pokud jste první, kdo používat PIM, se automaticky přiřazují [správce zabezpečení](../users-groups-roles/directory-assign-admin-roles.md#security-administrator) a [správce privilegovaných rolí](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) v daném adresáři.

Pro role Azure AD jenom uživatel, který je v roli správce privilegovaných rolí můžou Spravovat přiřazení pro jiní správci v PIM. Je možné [udělit přístup na jiné správce ke správě PIM](pim-how-to-give-access-to-pim.md). Globální správci, správci zabezpečení a čtenáři zabezpečení můžete zobrazit přiřazení role Azure AD PIM.

Pro role prostředků Azure, pouze správce předplatného, vlastník prostředku nebo prostředku správce přístupu uživatelů můžou Spravovat přiřazení pro jiní správci v PIM. Uživatelé, kteří jsou správci privilegovaných rolí, správci zabezpečení nebo čtenáře zabezpečení není ve výchozím nastavení mají přístup k zobrazení přiřazení role prostředků Azure v PIM.

## <a name="scenarios"></a>Scénáře

PIM podporuje následující scénáře:

**Jako správce privilegovaných rolí můžete:**

- Povolit schvalování pro konkrétní role
- Určit schvalovatele (uživatele nebo skupiny) pro schvalování žádostí
- Zobrazit historii žádostí a schválení pro všechny privilegované role

**Jako schvalovatele můžete:**

- Zobrazit žádosti čekající na schválení
- Schvalovat nebo zamítat žádosti o zvýšení oprávnění role (jednotně nebo hromadně)
- Uvést odůvodnění schválení nebo zamítnutí 

**Jako uživatel s oprávněné role vám umožňuje:**

- Požádat o aktivaci role, která vyžaduje schválení
- Zobrazit stav vaší žádosti o aktivaci
- Dokončit požadovanou úlohu v Azure AD, pokud se aktivace schválí

## <a name="next-steps"></a>Další postup

- [Licenční požadavky pro použití PIM](subscription-requirements.md)
- [Zabezpečení privilegovaného přístupu pro hybridní a cloudová nasazení v Azure AD](../users-groups-roles/directory-admin-roles-secure.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json)
- [Nasazení PIM](pim-deployment-plan.md)
