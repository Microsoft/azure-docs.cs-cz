---
title: Sdílení a souhlas LinkedIn s daty – Azure Active Directory | Dokumenty společnosti Microsoft
description: Vysvětluje, jak integrace LinkedIn sdílí data prostřednictvím aplikací Microsoftu ve službě Azure Active Directory
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: a21c986ccfe96bae5d341e0ba2ee6622d998d076
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74025389"
---
# <a name="linkedin-account-connections-data-sharing-and-consent"></a>Sdílení dat a souhlas připojení k účtu LinkedIn

Uživatelům ve vaší organizaci služby Active Directory (Azure AD) můžete povolit souhlas s připojením svého pracovního nebo školního účtu Microsoft u svého účtu LinkedIn. Po připojení svých účtů jsou v některých aplikacích a službách Microsoftu k dispozici informace a nejdůležitější informace z LinkedInu. Uživatelé mohou také očekávat, že jejich síťové zkušenosti na LinkedIn budou vylepšeny a obohaceny o informace od společnosti Microsoft.

Aby uživatelé viděli informace o LinkedIn u aplikací a služeb Microsoftu, musí souhlasit s připojením svých vlastních účtů Microsoft a LinkedIn. Uživatelé se při prvním kliknutí zobrazí výzva k připojení svých účtů, aby viděli informace o LinkedInu na kartě profilu v Outlooku, OneDrivu nebo SharePointu Online. Připojení k účtům LinkedIn nejsou pro vaše uživatele plně povolena, dokud neodsouhlasí prostředí a nepřipojí své účty.

[!INCLUDE [active-directory-gdpr-note](../../../includes/gdpr-hybrid-note.md)]

## <a name="benefits-of-sharing-linkedin-information"></a>Výhody sdílení informací linkedin

Přístup k informacím na LinkedIn v aplikacích a službách Microsoftu usnadňuje uživatelům připojení, zapojení a budování profesionálních vztahů s kolegy, zákazníky a partnery ve vaší organizaci i mimo ni. Noví uživatelé se mohou rychleji spojit díky spojení s kolegy, dozvědět se o nich více a snadno získat více informací. Tady je příklad zobrazení informací linkedinu na kartě profilu v aplikacích Microsoftu:

![Povolení integrace LinkedIn ve vaší organizaci](./media/linkedin-user-consent/display-example.png)

## <a name="enable-and-announce-linkedin-integration"></a>Povolení a oznámení integrace LinkedIn

Chcete-li spravovat nastavení pro vaši organizaci, musíte být správcem služby Azure Active Directory. Můžete jej povolit pro všechny uživatele nebo pro určitou sadu uživatelů.

1. Pokud chcete integraci povolit nebo zakázat, postupujte podle pokynů v [části Souhlas s integrací LinkedIn pro vaši organizaci Azure AD](linkedin-integration.md).
2. Když oznámíte integraci LinkedIn ve vaší organizaci, namiřte uživatele na nejčastější dotazy týkající se [informací linkedinu v aplikacích a službách společnosti Microsoft](https://support.office.com/article/about-linkedin-information-and-features-in-microsoft-apps-and-services-dc81cc70-4d64-4755-9f1c-b9536e34d381). Článek obsahuje informace o tom, kde se zobrazují informace linkedinu, jak propojit účty a další informace.

## <a name="user-consent-for-data-access-in-microsoft-and-linkedin"></a>Souhlas uživatele s přístupem k datům v Microsoftu a LinkedInu

Data, ke kterým přistupujete z LinkedIn, nejsou trvale uložena ve službách společnosti Microsoft. Data, ke kterým se přistupuje od společnosti Microsoft, nejsou trvale uložena s LinkedIn, s výjimkou kontaktů. Kontakty společnosti Microsoft jsou uloženy na linkedinu, dokud je uživatelé neodeberou, jak je popsáno v [odstranění importovaných kontaktů z LinkedIn](https://www.linkedin.com/help/linkedin/answer/43377).

Když uživatelé propojují své účty, informace a přehledy z LinkedIn jsou k dispozici v některých aplikacích Microsoftu, jako je karta profilu. Uživatelé mohou také očekávat, že jejich síťové zkušenosti na LinkedIn budou vylepšeny a obohaceny o informace od společnosti Microsoft.
Když uživatelé ve vaší organizaci propojují své pracovní nebo školní účty LinkedIn a Microsoft, mají dvě možnosti:

* Udělit oprávnění pro přístup k datům z obou účtů. To znamená, že udělit oprávnění pro jejich Microsoft nebo pracovní účet pro přístup k datům ze svého účtu LinkedIn, a pro [jejich LinkedIn účet pro přístup k datům z jejich microsoft pracovní nebo školní účet](https://www.linkedin.com/help/linkedin/answer/84077).
* Udělit oprávnění pouze k datům LinkedIn, ke kterým mají přístup jejich pracovní a školní účet Microsoft.

Uživatelé mohou kdykoli odpojit účty a odebrat oprávnění k přístupu k datům a [uživatelé mohou řídit způsob zobrazení vlastního profilu linkedinu](https://www.linkedin.com/help/linkedin/answer/83), včetně toho, zda lze jejich profil zobrazit v aplikacích společnosti Microsoft.

### <a name="linkedin-account-data"></a>Údaje o účtu LinkedIn

Když připojíte své účty Microsoft a LinkedIn, povolíte společnosti LinkedIn poskytovat společnosti Microsoft následující data:

* Profilová data – zahrnuje identitu LinkedIn, kontaktní informace a informace, které sdílíte s ostatními na vašem [profilu na LinkedIn](https://www.linkedin.com/help/linkedin/answer/15493).
* Údaje o zájmech – zahrnují zájmy na LinkedIn, jako jsou lidé a témata, která sledujete, skupiny kurzů a obsah, který se vám líbí a který sdílíte.
* Data předplatného – zahrnují předplatná aplikací a služeb LinkedIn spolu s přidruženými daty. 
* Data připojení – zahrnují vaši [síť LinkedIn](https://www.linkedin.com/help/linkedin/answer/110) včetně profilů a kontaktních informací o připojení chodu prvního stupně.

Data, ke kterým přistupujete z LinkedIn, nejsou trvale uložena ve službách společnosti Microsoft. Další informace o používání osobních údajů společností Microsoft naleznete v Prohlášení společnosti [Microsoft o zásadách ochrany osobních údajů](https://privacy.microsoft.com/privacystatement/).

### <a name="microsoft-work-or-school-account-data"></a>Data pracovního nebo školního účtu Microsoft

Když připojíte své účty Microsoft a LinkedIn, umožníte společnosti Microsoft poskytovat na LinkedIn následující data:

* Profilová data – zahrnují informace, jako je vaše jméno, příjmení, profilová fotka, e-mailová adresa, manažer a lidé, které spravujete.
* Data kalendáře – zahrnují schůzky v kalendářích, jejich časy, místa a kontaktní informace účastníků. Informace o schůzce, například o agendě, obsahu nebo názvu schůzky, nejsou v datech kalendáře zahrnuty.
* Údaje o zájmech – zahrnují zájmy spojené s vaším účtem na základě vašeho používání služeb Microsoftu, jako je Cortana a Bing pro firmy.
* Data předplatných – zahrnují předplatná poskytovaná vaší organizací aplikacím a službám Microsoftu, jako je Office 365.
* Data kontaktů – zahrnují seznamy kontaktů v Outlooku, Skypu a dalších službách účtu Microsoft, včetně kontaktních informací pro lidi, se kterými často komunikujete nebo s čím spolupracujete. LinkedIn bude pravidelně importovat, ukládat a používat kontakty, například k navrhnutí připojení, organizaci kontaktů a zobrazování aktualizací o kontaktech.

Data, ke kterým se přistupuje od společnosti Microsoft, nejsou trvale uložena s LinkedIn, s výjimkou kontaktů. Kontakty společnosti Microsoft jsou uloženy na linkedinu, dokud je uživatelé neodeberou. Přečtěte si další informace o [odstranění importovaných kontaktů z LinkedIn](https://www.linkedin.com/help/linkedin/answer/43377).

Další informace o používání osobních údajů společností LinkedIn naleznete v [zásadách ochrany osobních údajů linkedinu](https://www.linkedin.com/legal/privacy-policy). U služeb LinkedIn, přenosu dat a ukládání mohou data proudit z Evropské unie do Spojených států a zpět a vaše soukromí je chráněno tak, jak je popsáno v [přenosech dat](https://www.linkedin.com/help/linkedin/answer/62533)v Evropské unii .

## <a name="next-steps"></a>Další kroky

* [LinkedIn v aplikacích Microsoftu s pracovním nebo školním účtem](https://www.linkedin.com/help/linkedin/answer/84077)
