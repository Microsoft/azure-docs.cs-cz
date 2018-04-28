---
title: Azure AD vrstvené heslo zabezpečení | Microsoft Docs
description: Vysvětluje, jak Azure AD vynucuje silná hesla a chrání hesla uživatelů z zločinci,
services: active-directory
documentationcenter: ''
author: barlanmsft
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: barlan
ms.openlocfilehash: 04e437eb5a040f418df22e0857cb96d7ef99bbbb
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
---
# <a name="a-multi-tiered-approach-to-azure-ad-password-security"></a>Víceúrovňová způsob zabezpečení hesel Azure AD

Tento článek popisuje některé z osvědčených postupů můžete provést jako uživatel, nebo jako správce k ochraně služby Azure Active Directory (Azure AD) nebo Account Microsoft.

 > [!NOTE]
 > **Jste tady, protože máte potíže s přihlášením?** Pokud ano, [přečtěte si informace o tom, jak můžete změnit a resetovat vlastní heslo](active-directory-passwords-update-your-own-password.md).
 >
 > Správci služby Azure AD můžete resetovat hesla uživatelů pomocí pokynů v článku [resetování hesla pro uživatele v Azure Active Directory](active-directory-users-reset-password-azure-portal.md).
 >

## <a name="password-requirements"></a>Požadavky na heslo

Azure AD zahrnuje následující běžné přístupy k zabezpečení hesel:

* Požadavky na délku hesla
* Požadavky na složitost hesla
* Pravidelné a opakované vypršení platnosti hesla

Informace o vytvoření nového ve službě Azure Active Directory, naleznete v tématu [Azure AD samoobslužného resetování hesla pro IT profesionály](active-directory-passwords-update-your-own-password.md).

## <a name="azure-ad-password-protections"></a>Ochrana hesla služby Azure AD

Azure AD a systémem účtů Microsoft používat odvětví ověřené přístupy k zajištění zabezpečení ochranu uživatele a správce hesel, včetně:

* Dynamicky zakázaná hesla
* Inteligentní uzamčení hesla

Informace o správě hesel podle aktuální research, najdete v dokumentu White Paper [heslo pokyny](https://aka.ms/passwordguidance).

### <a name="dynamically-banned-passwords"></a>Dynamicky zakázaná hesla

Azure AD a Accounts Microsoft ochrana heslem zabezpečit pomocí dynamicky zakazování běžně používané hesla. Tým služby Azure AD Identity Protection pravidelně analyzuje seznamů zakázaných heslo, brání uživatelům v výběr běžně používané hesla. Tato služba je k dispozici pro zákazníky Azure AD a Microsoft Account Service.

Při vytváření hesel, je důležité pro správce k podpoře uživatelům zvolit frází heslo, které zahrnují jedinečnou kombinaci písmena, čísla, znaky nebo slova. Tento přístup umožňuje, aby téměř možné být ohrožené ale usnadňuje uživatelům mějte na paměti, aby uživatelská hesla.

#### <a name="password-breaches"></a>Narušení heslo

Společnost Microsoft pracuje vždy zůstala krok před zločinci.

Tým Azure AD Identity Protection průběžně analyzuje hesla, která se běžně používají. Kybernetičtí zločinci také používají podobné strategie k úpravě svých útoků, jako je například vytváření [duhové tabulky](https://en.wikipedia.org/wiki/Rainbow_table) pro prolamování hodnot hash hesel.

Microsoft průběžně analyzuje [úniky dat](https://www.privacyrights.org/data-breaches) a udržuje dynamicky aktualizovaný seznam zakázaných hesel, který zajišťuje, že citlivá hesla jsou zakázána dřív, než se stanou skutečnou hrozbou zákazníkům služby Azure AD. Další informace o našem aktuálním úsilí týkajícím se zabezpečení najdete ve zprávě [Microsoft Security Intelligence Report](https://www.microsoft.com/security/sir/default.aspx).

### <a name="smart-password-lockout"></a>Inteligentní uzamčení hesla

Když Azure AD zjistí, že potenciální kybernetický zločinec se snaží o prolomení uživatelského hesla, uživatelský účet se uzamkne pomocí inteligentního uzamčení hesla. Azure AD vyhodnocuje riziko spojené s konkrétními přihlašovacími relacemi. Potom pomocí nejnovější data zabezpečení použijeme uzamčení sémantiku zastavit internetových hrozeb.

Pokud se uživatel uzamkne mimo Azure AD, jejich obrazovky vypadá podobně jako následující:

  ![Uzamčení a vyloučení z Azure AD](./media/active-directory-secure-passwords/locked-out-azuread.png)

Pro ostatní účty Microsoft jejich obrazovky vypadá podobně jako následující:

  ![Uzamčení a vyloučení z účtu Microsoft](./media/active-directory-secure-passwords/locked-out-ms-accounts.png)

Informace o vytvoření nového ve službě Azure Active Directory, naleznete v tématu [Azure AD samoobslužného resetování hesla pro IT profesionály](active-directory-passwords-update-your-own-password.md).

  >[!NOTE]
  >Pokud jste správce Azure AD, můžete chtít použít [Windows Hello](https://www.microsoft.com/windows/windows-hello), aby vaši uživatelé přestali vytvářet tradiční hesla úplně.
  >

## <a name="next-steps"></a>Další postup

* [Postup aktualizace vlastního hesla](active-directory-passwords-update-your-own-password.md)
* [Základy správy identit Azure](fundamentals-identity.md)
* [Aktivita resetování zprávu o heslo](authentication/howto-sspr-reporting.md)
