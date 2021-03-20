---
title: Oprava rizik a odblokování uživatelů v Azure AD Identity Protection
description: Seznamte se s možnostmi zavření aktivních detekcí rizik.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 01/25/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: e322f78468c89a549955a01f73952e8cde7a13c8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98872820"
---
# <a name="remediate-risks-and-unblock-users"></a>Oprava rizik a odblokování uživatelů

Po dokončení [šetření](howto-identity-protection-investigate-risk.md)budete chtít provést akci k nápravě rizik nebo odblokování uživatelů. Organizace mají taky možnost Povolit automatizovanou nápravu pomocí zásad jejich [rizik](howto-identity-protection-configure-risk-policies.md). Organizace by se měli pokusit zavřít všechna zjišťování rizik, která se jim zobrazí, v časovém období, ve kterém je vaše organizace pohodlné. Společnost Microsoft doporučuje zavřít události co nejdříve, protože při práci s rizikem hrozí čas.

## <a name="remediation"></a>Náprava

Všechna aktivní zjišťování rizik přispívají k výpočtu hodnoty s názvem úroveň rizika uživatele. Úroveň rizika uživatele je indikátorem (nízká, střední, vysoká) pro pravděpodobnost, že došlo k ohrožení bezpečnosti účtu. Jako správce chcete získat uzavřená všechna zjištění rizik, aby postižení uživatelé už nehrozí.

Některá zjištění rizik můžou být označená ochranou identity jako uzavřená (systém), protože události už nejsou určené k riziku.

Správci můžou opravit tyto možnosti:

- Zásady pro automatické nápravy pomocí zásad rizik
- Ruční resetování hesla
- Zavřít riziko uživatele
- Ruční uzavření zjištění jednotlivých rizik

### <a name="self-remediation-with-risk-policy"></a>Zásady pro automatické nápravy pomocí zásad rizik

Pokud uživatelům povolíte samoobslužnou nápravu, s využitím Azure AD Multi-Factor Authentication (MFA) a samoobslužného resetování hesla (SSPR) ve svých zásadách rizik, můžou při zjištění rizika odblokovat. Tyto detekce se pak považují za zavřené. Uživatelé musí mít dřív zaregistrovaný pro Azure AD MFA a SSPR, aby je bylo možné použít, když se zjistí riziko.

Některé detekce nemusí vyvolávat riziko pro úroveň, kde by se vyžadovala samostatná náprava uživatele, ale Správci by tyto detekce měli ještě vyhodnotit. Správci mohou určit, že jsou potřeba další míry, jako je [blokování přístupu z umístění](../conditional-access/howto-conditional-access-policy-location.md) nebo snížení přijatelného rizika v jejich zásadách.

### <a name="manual-password-reset"></a>Ruční resetování hesla

Pokud se vyžaduje resetování hesla pomocí zásad rizik uživatelů, může správce zavřít všechna zjišťování rizik pro uživatele s ručním resetováním hesla.

Správcům se při resetování hesla pro uživatele přidávají dvě možnosti:

- **Vygenerovat dočasné heslo** – generováním dočasného hesla můžete okamžitě převést identitu zpátky do bezpečného stavu. Tato metoda vyžaduje kontaktování ovlivněných uživatelů, protože potřebují znát, co je dočasné heslo. Vzhledem k tomu, že je heslo dočasné, zobrazí se uživateli výzva ke změně hesla na něco nového při příštím přihlášení.

- **Vyžadovat, aby uživatel resetoval heslo** – vyžaduje, aby uživatelé resetování hesla povolili automatické obnovení bez kontaktování helpdesku nebo správce. Tato metoda se vztahuje jenom na uživatele, kteří jsou zaregistrovaní pro Azure AD MFA a SSPR. Pro uživatele, kteří nejsou zaregistrovaní, tato možnost není k dispozici.

### <a name="dismiss-user-risk"></a>Zavřít riziko uživatele

Pokud resetování hesla není pro vás možnost, protože jste například odstranili uživatele, můžete si vybrat, jestli chcete zrušit rozpoznávání rizik uživatelů.

Když kliknete na **Zrušit riziko uživatele**, všechny události se zavřou a ovlivněný uživatel už nehrozí. Vzhledem k tomu, že tato metoda nemá vliv na existující heslo, nepřinese související identitu zpátky do bezpečného stavu. 

### <a name="close-individual-risk-detections-manually"></a>Ruční uzavření zjištění jednotlivých rizik

Jednotlivá zjišťování rizik můžete uzavřít ručně. Pokud se detekce rizik uzavírá ručně, můžete snížit úroveň rizika uživatele. Detekce rizik je typicky uzavřená ručně v reakci na související šetření. Například při komunikaci s uživatelem se odhalí, že aktivní zjišťování rizik již není vyžadováno. 
 
Při ručním uzavírání detekce rizik můžete zvolit provedení libovolné z následujících akcí, abyste změnili stav detekce rizik:

- Potvrdit ohrožení uživatele
- Zavřít riziko uživatele
- Potvrzení bezpečného přihlášení
- Potvrďte narušení přihlášení.

## <a name="unblocking-users"></a>Odblokování uživatelů

Správce se může rozhodnout, že se přihlásí na základě jejich rizikových zásad nebo šetření. Blok může vycházet buď při přihlašování, nebo na základě rizika uživatele.

### <a name="unblocking-based-on-user-risk"></a>Odblokování na základě rizika uživatele

Aby měli správci odblokování účtu blokovanému z důvodu rizika uživatele, mají následující možnosti:

1. **Resetování hesla** – můžete resetovat heslo uživatele.
1. **Zrušit riziko uživatele** – zásady rizik uživatelů blokují uživatele, pokud byla dosažena nakonfigurovaná úroveň rizika uživatele pro blokování přístupu. Úroveň rizika uživatele můžete snížit odvoláním rizika uživatele nebo ručním ukončením oznámených detekcí rizik.
1. **Vyloučení uživatele ze zásad** – Pokud se domníváte, že aktuální konfigurace zásady přihlašování způsobuje problémy pro konkrétní uživatele, můžete z něj vyloučit uživatele. Další informace najdete v části vyloučení v článku [Postupy: konfigurace a povolení zásad rizik](howto-identity-protection-configure-risk-policies.md#exclusions).
1. **Zakázat zásady** – Pokud se domníváte, že konfigurace zásad způsobuje problémy pro všechny uživatele, můžete zásady zakázat. Další informace najdete v článku [How to: Configure and Enable rizikové zásady](howto-identity-protection-configure-risk-policies.md).

### <a name="unblocking-based-on-sign-in-risk"></a>Odblokování na základě rizika přihlašování

Aby správci odblokoval účet na základě rizika přihlašování, mají následující možnosti:

1. **Přihlaste se ze známého umístění nebo zařízení** – běžným důvodem pro blokované podezřelé přihlášení jsou pokusy o přihlášení z neznámých umístění nebo zařízení. Uživatelé mohou rychle zjistit, zda se jedná o důvod blokování, pokusit se o přihlášení ze známého umístění nebo zařízení.
1. **Vyloučení uživatele ze zásad** – Pokud se domníváte, že aktuální konfigurace zásady přihlašování způsobuje problémy pro konkrétní uživatele, můžete z něj vyloučit uživatele. Další informace najdete v části vyloučení v článku [Postupy: konfigurace a povolení zásad rizik](howto-identity-protection-configure-risk-policies.md#exclusions).
1. **Zakázat zásady** – Pokud se domníváte, že konfigurace zásad způsobuje problémy pro všechny uživatele, můžete zásady zakázat. Další informace najdete v článku [How to: Configure and Enable rizikové zásady](howto-identity-protection-configure-risk-policies.md).

## <a name="powershell-preview"></a>PowerShell Preview

Pomocí modulu Microsoft Graph PowerShell SDK Preview můžou organizace spravovat rizika pomocí PowerShellu. Moduly a ukázkový kód verze Preview najdete v [úložišti GitHub Azure AD](https://github.com/AzureAD/IdentityProtectionTools). 

`Invoke-AzureADIPDismissRiskyUser.ps1`Skript zahrnutý do úložiště umožňuje organizacím zrušit všechny rizikové uživatele ve svém adresáři.

## <a name="next-steps"></a>Další kroky

Přehled Azure AD Identity Protection najdete v tématu [Azure AD Identity Protection Overview](overview-identity-protection.md).
