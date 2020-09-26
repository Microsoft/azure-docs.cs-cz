---
title: Pomocí skupin cloudu můžete spravovat přiřazení rolí v Azure Active Directory | Microsoft Docs
description: Zobrazte si náhled vlastních rolí Azure AD pro delegování správy identit. Správa přiřazení rolí Azure v Azure Portal, PowerShellu nebo Graph API.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 08/11/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1c3143dac79b37ff6dac04856311394440dec9a1
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91317408"
---
# <a name="use-cloud-groups-to-manage-role-assignments-in-azure-active-directory-preview"></a>Použití skupin cloudů ke správě přiřazení rolí v Azure Active Directory (Preview)

Azure Active Directory (Azure AD) zavádí verzi Public Preview, ve které můžete přiřadit cloudovou skupinu k předdefinovaným rolím Azure AD. Pomocí této funkce můžete skupiny použít k udělení přístupu správce ve službě Azure AD s minimálním úsilím ze svých globálních správců a privilegovaných rolí.

Vezměte v úvahu tento příklad: contoso přijal lidem v geografických oblastech správu a resetování hesel pro zaměstnance ve své organizaci Azure AD. Místo vystavení privilegované role správce nebo globálního správce pro přiřazení role správce technické podpory každému uživateli můžou vytvořit skupinu Contoso_Helpdesk_Administrators a přiřadit ji k roli. Když se uživatelé připojí ke skupině, přiřadí se role nepřímo. Váš stávající pracovní postup zásad správného řízení se pak může postarat o proces schvalování a auditování členství ve skupině, aby se zajistilo, že pouze legitimní uživatelé jsou členy skupiny a že jsou tak přiřazeni k roli správce helpdesku.

## <a name="how-this-feature-works"></a>Jak tato funkce funguje

Vytvořte novou Microsoft 365 nebo skupinu zabezpečení s vlastností ' isAssignableToRole ' nastavenou na hodnotu ' true '. Tuto vlastnost můžete také povolit při vytváření skupiny v Azure Portal zapnutím **rolí Azure AD je můžete přiřadit ke skupině**. V obou případech můžete skupinu přiřadit k jedné nebo více rolím Azure AD stejným způsobem, jako přiřadíte role uživatelům. V jedné organizaci Azure AD (tenant) se dá vytvořit maximálně 200 skupin s přiřazením role.

Pokud nechcete, aby členové skupiny měli stálý přístup k této roli, můžete použít Azure AD Privileged Identity Management. Přiřaďte skupinu jako oprávněného člena role Azure AD. Každý člen skupiny pak má nárok na aktivaci jejich přiřazení pro roli, ke které je skupina přiřazená. Pak můžou aktivovat přiřazení rolí po dobu trvání pevného času.

> [!Note]
> Aby bylo možné přiřadit skupinu k roli Azure AD prostřednictvím PIM, musíte být v aktualizované verzi Privileged Identity Management. Můžete být na starší verzi PIM, protože vaše organizace Azure AD využívá rozhraní Privileged Identity Management API. Pokud chcete pim_preview@microsoft.com svou organizaci přesunout a aktualizovat své rozhraní API, kontaktujte prosím alias. Další informace najdete v informacích o [rolích a funkcích Azure AD v PIM](../privileged-identity-management/azure-ad-roles-features.md).

## <a name="why-we-enforce-creation-of-a-special-group-for-assigning-it-to-a-role"></a>Proč vynutili vytvoření speciální skupiny pro přiřazení k roli

Pokud je skupině přiřazena role, může správce IT, který může spravovat členství ve skupině, nepřímo spravovat členství této role. Předpokládejme například, že je skupina Contoso_User_Administrators přiřazena k roli správce uživatelských účtů. Správce serveru Exchange, který může změnit členství ve skupině, by se mohl přidat do skupiny Contoso_User_Administrators a tímto způsobem se stane správcem účtu uživatele. Jak vidíte, může správce zvýšit svoje oprávnění způsobem, který jste nechtěli.

Azure AD umožňuje chránit skupinu přiřazenou k roli pomocí nové vlastnosti s názvem isAssignableToRole pro skupiny. Do role se dají přiřadit jenom skupiny cloudu, které mají vlastnost isAssignableToRole nastavenou na hodnotu true v době vytváření. Tato vlastnost je neproměnlivá; Jakmile je skupina vytvořena s touto vlastností nastavenou na hodnotu true, nelze ji změnit. Vlastnost nelze nastavit v existující skupině.
Navrhli jsme, jak se přiřazují skupiny k rolím, aby se zabránilo tomu, že by se mohlo vyřadit.

- Pouze globální správci a správci privilegovaných rolí můžou vytvořit skupinu s přiřazením rolí (s povolenou vlastností "isAssignableToRole").
- Nemůže to být dynamická skupina Azure AD; To znamená, že musí mít přiřazený typ členství. Automatické naplnění dynamických skupin by mohlo vést k přidání nechtěného účtu do skupiny, který je přiřazený k roli.
- Ve výchozím nastavení mohou spravovat členství ve skupině, která je přiřazena rolí, pouze globální správci a správci privilegovaných rolí, ale můžete delegovat správu skupin s možností přiřazení role přidáním vlastníků skupin.
- Aby se zabránilo zvýšení oprávnění, můžete přihlašovací údaje členů a vlastníků skupiny přiřadit roli změnit pouze pomocí Správce privilegovaného ověřování nebo globálního správce.
- Žádné vnořování. Skupinu nelze přidat jako člena skupiny přiřazení role.

## <a name="limitations"></a>Omezení

Následující scénáře se teď nepodporují:  

- Přiřazení cloudových skupin ke vlastním rolím Azure AD
- Přiřaďte cloudové skupiny k rolím Azure AD (integrovaným nebo vlastním) prostřednictvím jednotky pro správu nebo rozsahu aplikace.
- Přiřazení místních skupin k rolím Azure AD (předdefinované nebo vlastní)

## <a name="known-issues"></a>Známé problémy

- Dynamickou skupinu nemůžete vytvořit ani upravit, pokud je role přiřazena přes skupinu.
- Funkce **povolit dvoufázové zavedení pro funkci přihlašování spravovaného uživatele** nepodporuje přiřazování přes skupinu.
- *Jenom licencovaní zákazníci Azure AD P2*: nepřiřazujte skupinu jako aktivní k roli prostřednictvím Azure AD a PRIVILEGED Identity Management (PIM). Konkrétně nepřiřazujte roli skupině přiřazení role, když se vytváří *, a* přiřaďte ji ke skupině pomocí Správce cloudu později. To povede k problémům, kde uživatelé nevidí jejich aktivní přiřazení role v PIM a zároveň neumožňují odebrání tohoto přiřazení PIM. V tomto scénáři nejsou ovlivněna oprávněná přiřazení. Pokud se pokusíte toto přiřazení provést, může se zobrazit neočekávané chování, například:
  - Čas ukončení přiřazení role se může zobrazit nesprávně.
  - Na portálu PIM můžou **Moje role** zobrazovat jenom jedno přiřazení role bez ohledu na to, kolik metod má přiřazení uděleno (přes jednu nebo víc skupin a přímo).
- *Jenom licencovaní zákazníci Azure AD P2* I po odstranění skupiny se stále zobrazuje oprávněný člen role v uživatelském rozhraní PIM. Funkční není žádný problém. je to jen problém s mezipamětí v Azure Portal.  
- Použijte nové [Centrum pro správu Exchange](https://admin.exchange.microsoft.com/) pro přiřazení rolí prostřednictvím členství ve skupině. Původní centrum pro správu Exchange tuto funkci ještě nepodporuje. Rutiny prostředí Exchange PowerShell budou fungovat podle očekávání.
- Portál Azure Information Protection (portál Classic) nerozpozná členství v rolích prostřednictvím skupiny. Můžete [migrovat na sjednocenou platformu označování citlivostí](/azure/information-protection/configure-policy-migrate-labels) a potom použít Centrum kompatibility Office 365 Security & k použití přiřazení skupin pro správu rolí.

Opravujeme tyto problémy.

## <a name="required-license-plan"></a>Požadovaný licenční plán

Použití této funkce vyžaduje, abyste v organizaci Azure AD měli k dispozici dostupnou licenci Azure AD Premium P1. Pokud chcete použít taky Privileged Identity Management pro aktivaci role za běhu, musíte mít dostupnou licenci Azure AD Premium P2. Správnou licenci pro vaše požadavky najdete v tématu [porovnání všeobecně dostupných funkcí plánů Free a Premium](../fundamentals/active-directory-whatis.md#what-are-the-azure-ad-licenses).

## <a name="next-steps"></a>Další kroky

- [Vytvoření skupiny s možností přiřazení rolí](roles-groups-create-eligible.md)
- [Přiřazení role k skupině přiřazení rolí](roles-groups-assign-role.md)
