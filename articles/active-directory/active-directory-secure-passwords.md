---
title: Vrstvené zabezpečení hesel Azure AD | Microsoft Docs
description: Vysvětluje, jak Azure AD vynucuje silná hesla a chrání hesla uživatelů před kybernetickými zločinci.
services: active-directory
documentationcenter: ''
author: eross-msft
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 08/28/2017
ms.author: lizross
ms.openlocfilehash: e3e97a5a9b768b3b9d4a36627d28955be1f3d9df
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/14/2018
ms.locfileid: "39056915"
---
# <a name="a-multi-tiered-approach-to-azure-ad-password-security"></a>Vícevrstvý přístup k zabezpečení hesel Azure AD

Tento článek popisuje některé osvědčené postupy, které můžete použít jako uživatel nebo jako správce k ochraně služby Azure Active Directory (Azure AD) a účtu Microsoft.

 > [!NOTE]
 > **Jste tady, protože máte potíže s přihlášením?** Pokud ano, [přečtěte si informace o tom, jak můžete změnit a resetovat vlastní heslo](user-help/active-directory-passwords-update-your-own-password.md).
 >
 > Správci služby Azure AD můžou resetovat hesla uživatelů pomocí pokynů v článku popisujícím [resetování hesla pro uživatele v Azure Active Directory](fundamentals/active-directory-users-reset-password-azure-portal.md).
 >

## <a name="password-requirements"></a>Požadavky na heslo

Azure AD zahrnuje následující běžné přístupy k zabezpečení hesel:

* Požadavky na délku hesla
* Požadavky na složitost hesla
* Pravidelné a opakované vypršení platnosti hesla

Informace o resetování hesla ve službě Azure Active Directory najdete v tématu s popisem [samoobslužného resetování hesla Azure AD pro IT profesionály](user-help/active-directory-passwords-update-your-own-password.md).

## <a name="azure-ad-password-protections"></a>Ochrany hesel Azure AD

Azure AD a Microsoft Account System používají ověřené přístupy k zajištění bezpečné ochrany hesel uživatelů a správců, mezi které patří:

* Dynamicky zakázaná hesla
* Inteligentní uzamčení hesla

Informace o správě hesel na základě současných výzkumů najdete v dokumentu white paper [Password Guidance](https://aka.ms/passwordguidance) (Pokyny k heslům).

### <a name="dynamically-banned-passwords"></a>Dynamicky zakázaná hesla

Azure AD a účty Microsoft provádí ochranu hesel pomocí dynamického zakazování běžně používaných hesel. Tým Azure AD Identity Protection pravidelně analyzuje seznamy zakázaných hesel, aby bránil uživatelům ve zvolení běžně používaných hesel. Tato služba je k dispozici pro zákazníky Azure AD a Microsoft Account Service.

Při vytváření hesel je pro správce důležité nutit uživatele k volbě hesel, která zahrnují jedinečné kombinace písmen, číslic, znaků nebo slov. Tento přístup pomáhá vytvářet hesla uživatelů, která je téměř nemožné prolomit, ale která si můžou uživatelé snadněji zapamatovat.

#### <a name="password-breaches"></a>Prolomení hesel

Microsoft se vždy snaží být o krok před kybernetickými zločinci.

Tým Azure AD Identity Protection průběžně analyzuje hesla, která se běžně používají. Kybernetičtí zločinci také používají podobné strategie k úpravě svých útoků, jako je například vytváření [duhové tabulky](https://en.wikipedia.org/wiki/Rainbow_table) pro prolamování hodnot hash hesel.

Microsoft průběžně analyzuje [úniky dat](https://www.privacyrights.org/data-breaches) a udržuje dynamicky aktualizovaný seznam zakázaných hesel, který zajišťuje, že citlivá hesla jsou zakázána dřív, než se stanou skutečnou hrozbou zákazníkům služby Azure AD. Další informace o našem aktuálním úsilí týkajícím se zabezpečení najdete ve zprávě [Microsoft Security Intelligence Report](https://www.microsoft.com/security/sir/default.aspx).

### <a name="smart-password-lockout"></a>Inteligentní uzamčení hesla

Když Azure AD zjistí, že potenciální kybernetický zločinec se snaží o prolomení uživatelského hesla, uživatelský účet se uzamkne pomocí inteligentního uzamčení hesla. Azure AD vyhodnocuje riziko spojené s konkrétními přihlašovacími relacemi. Pak pomocí nejnovějších dat zabezpečení použijeme pro kybernetické hrozby sémantiku uzamčení.

Pokud je uživatel uzamčený a vyloučený z Azure AD, vypadá jeho obrazovka podobně jako následující:

  ![Uzamčení a vyloučení z Azure AD](./media/active-directory-secure-passwords/locked-out-azuread.png)

Pro ostatní účty Microsoft vypadá jeho obrazovka podobně jako následující:

  ![Uzamčení a vyloučení z účtu Microsoft](./media/active-directory-secure-passwords/locked-out-ms-accounts.png)

Informace o resetování hesla ve službě Azure Active Directory najdete v tématu s popisem [samoobslužného resetování hesla Azure AD pro IT profesionály](user-help/active-directory-passwords-update-your-own-password.md).

  >[!NOTE]
  >Pokud jste správce Azure AD, můžete chtít použít [Windows Hello](https://www.microsoft.com/windows/windows-hello), aby vaši uživatelé přestali vytvářet tradiční hesla úplně.
  >

## <a name="next-steps"></a>Další kroky

* [Postup aktualizace vlastního hesla](user-help/active-directory-passwords-update-your-own-password.md)
* [Základy správy identit Azure](fundamentals-identity.md)
* [Hlášení aktivity resetování hesla](authentication/howto-sspr-reporting.md)
