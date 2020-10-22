---
title: Sdílení a souhlas s daty LinkedIn – Azure Active Directory | Microsoft Docs
description: Vysvětluje, jak integrace LinkedInu sdílí data přes aplikace Microsoftu v Azure Active Directory
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: how-to
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5d90391dd5512b08ee6995a32bcd0b0f111b0f2b
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2020
ms.locfileid: "92375919"
---
# <a name="linkedin-account-connections-data-sharing-and-consent"></a>Účet LinkedIn připojení ke sdílení dat a jejich souhlas

Uživatelům v organizaci služby Active Directory (Azure AD) můžete povolit souhlas s připojením svého pracovního nebo školního účtu Microsoftu k účtu LinkedIn. Jakmile uživatel připojí své účty, informace a nejdůležitější informace z LinkedInu jsou k dispozici v některých aplikacích a službách společnosti Microsoft. Uživatelé taky můžou očekávat, že se síťové prostředí na LinkedInu zlepší a obohacuje s informacemi od Microsoftu.

Chcete-li zobrazit informace LinkedInu v aplikacích a službách společnosti Microsoft, musí uživatelé souhlasit s připojením vlastních účtů Microsoft a LinkedIn. Uživatelům se zobrazí výzva k připojení svých účtů při prvním kliknutí, aby viděli informace o LinkedInu na kartě profilu v Outlooku, OneDrivu nebo SharePointu Online. Připojení k účtu LinkedIn nejsou pro vaše uživatele plně povolena, dokud nebudou souhlasit s prostředím a připojíte své účty.

[!INCLUDE [active-directory-gdpr-note](../../../includes/gdpr-hybrid-note.md)]

## <a name="benefits-of-sharing-linkedin-information"></a>Výhody sdílení informací o LinkedInu

Přístup k informacím LinkedIn v aplikacích a službách Microsoftu usnadňuje uživatelům připojení, zapojení a sestavování profesionálních vztahů s kolegy, zákazníky a partnery v rámci vaší organizace i mimo ni. Noví uživatelé můžou zrychlit rychlejším připojením k kolegům, učit se o nich více a snadno získat další informace. Tady je příklad, jak se informace LinkedInu zobrazují na kartě Profil v aplikacích Microsoftu:

![Povolení integrace LinkedInu ve vaší organizaci](./media/linkedin-user-consent/display-example.png)

## <a name="enable-and-announce-linkedin-integration"></a>Povolení a oznámení integrace LinkedInu

Abyste mohli spravovat nastavení vaší organizace, musíte být správcem Azure Active Directory. Můžete ji povolit pro všechny uživatele nebo pro konkrétní skupinu uživatelů.

1. Pokud chcete povolit nebo zakázat integraci, postupujte podle kroků v části [vyjádření souhlasu s integrací LinkedIn pro vaši organizaci Azure AD](linkedin-integration.md).
2. Když oznamujete integraci LinkedInu ve vaší organizaci, nasměrujte uživatele na nejčastější dotazy týkající se [informací o LinkedInu v aplikacích a službách Microsoftu](https://support.office.com/article/about-linkedin-information-and-features-in-microsoft-apps-and-services-dc81cc70-4d64-4755-9f1c-b9536e34d381). Tento článek obsahuje informace o tom, kde se zobrazují informace o LinkedInu, [sdílení dat a ochraně osobních údajů](https://support.microsoft.com/office/your-data-ae9c08a7-4d06-45b5-a065-320a97bc1400), [Jak propojit účty](https://support.microsoft.com/office/connect-your-linkedin-and-work-or-school-accounts-c7c245f2-fa56-4c9b-ba20-3fceb23c5772) a další.

Integraci LinkedInu musíte oznámit uživatelům, kteří jim poskytnou všechny informace týkající se [sdílení a ochrany dat s integrací LinkedInu](https://support.microsoft.com/office/your-data-ae9c08a7-4d06-45b5-a065-320a97bc1400). 

## <a name="user-consent-for-data-access-in-microsoft-and-linkedin"></a>Souhlas uživatele s přístupem k datům v Microsoft a LinkedInu

Data, ke kterým se dostanete z LinkedInu, se na služby Microsoftu neukládají trvale. Data, ke kterým se dostanete od Microsoftu, se neukládají trvale pomocí LinkedInu s výjimkou kontaktů. Kontakty společnosti Microsoft jsou uloženy na LinkedIn, dokud je uživatelé neodstraní, jak je popsáno v tématu [odstranění importovaných kontaktů z LinkedIn](https://www.linkedin.com/help/linkedin/answer/43377).

Když uživatelé připojí své účty, informace a přehledy z LinkedInu jsou k dispozici v některých aplikacích Microsoftu, jako je karta profilace. Uživatelé taky můžou očekávat, že se síťové prostředí na LinkedInu zlepší a obohacuje s informacemi od Microsoftu.
Když uživatelé ve vaší organizaci připojí své pracovní nebo školní účty pro LinkedIn a Microsoft, mají dvě možnosti:

* Udělte oprávnění pro přístup k datům z obou účtů. To znamená, že poskytují oprávnění pro svůj účet Microsoft nebo pracovní postup pro přístup k datům z jejich účtu LinkedIn a pro [svůj účet LinkedIn pro přístup k datům ze svého pracovního nebo školního účtu Microsoftu](https://www.linkedin.com/help/linkedin/answer/84077).
* Poskytněte oprávnění pouze pro přístup k datům LinkedIn, ke kterým mají přístup jejich pracovní a školní účet Microsoftu.

Uživatelé můžou odpojit účty a kdykoli odebrat oprávnění k přístupu k datům a [Uživatelé můžou řídit, jak se bude zobrazovat vlastní linkedinový profil](https://www.linkedin.com/help/linkedin/answer/83), včetně toho, jestli se jejich profil dá zobrazit v aplikacích Microsoftu.

### <a name="linkedin-account-data"></a>Data účtu LinkedIn

Po připojení účtů Microsoft a LinkedInu vám LinkedIn umožní poskytovat Microsoftu tato data:

* Data profilu – zahrnuje identitu LinkedIn, kontaktní údaje a informace, které sdílíte s ostatními v [profilu LinkedIn](https://www.linkedin.com/help/linkedin/answer/15493).
* Data o zájmech – zahrnuje podíly na LinkedInu, jako jsou lidé a témata, které sledujete, skupiny kurzů a obsah, který chcete sdílet.
* Data předplatných – zahrnuje odběry aplikací a služeb LinkedIn spolu s přidruženými daty. 
* Data připojení – zahrnuje vaši [síť LinkedIn](https://www.linkedin.com/help/linkedin/answer/110) včetně profilů a kontaktních informací o připojeních od prvního stupně.

Data, ke kterým se dostanete z LinkedInu, se na služby Microsoftu neukládají trvale. Další informace o používání osobních údajů od Microsoftu najdete v tématu [prohlášení o zásadách ochrany osobních údajů společnosti](https://privacy.microsoft.com/privacystatement/)Microsoft.

### <a name="microsoft-work-or-school-account-data"></a>Data pracovního nebo školního účtu Microsoft

Když propojíte účty Microsoft a LinkedIn, umožníte Microsoftu, aby na LinkedInu poskytoval následující data:

* Data profilu – obsahuje informace, jako je vaše křestní jméno, příjmení, fotografie profilu, e-mailová adresa, nadřízený a lidé, které spravujete.
* Data kalendáře – obsahuje schůzky v kalendářích, jejich časy, umístění a kontaktní údaje účastníků. Údaje o schůzce, jako je agenda, obsah nebo název schůzky, nejsou zahrnuté do kalendářních dat.
* Data o zájmech – zahrnuje zájmy spojené s vaším účtem, a to na základě vašeho používání služeb Microsoftu, jako je Cortana a Bing pro firmy.
* Data předplatných – včetně předplatných poskytovaných vaší organizací pro aplikace a služby Microsoftu, jako je například Microsoft 365.
* Data kontaktů – obsahuje seznamy kontaktů v Outlooku, Skypu a dalších službách účet Microsoft, včetně kontaktních informací pro lidi, se kterými často komunikujete nebo spolupracujete. Kontakty se pravidelně importují, ukládají a používají LinkedIn, například k navrhování připojení, k organizování kontaktů a zobrazování aktualizací kontaktů.

Data, ke kterým se dostanete od Microsoftu, se neukládají trvale pomocí LinkedInu s výjimkou kontaktů. Kontakty společnosti Microsoft jsou uloženy na LinkedIn, dokud je uživatelé neodstraní. Přečtěte si další informace o [odstraňování importovaných kontaktů z LinkedInu](https://www.linkedin.com/help/linkedin/answer/43377).

Další informace o použití osobních údajů na LinkedInu najdete v tématu [Zásady ochrany osobních údajů na LinkedInu](https://www.linkedin.com/legal/privacy-policy). U LinkedIn Services, přenosu dat a úložiště můžou data přenášet z evropského sjednocení do USA a zpátky a vaše ochrana osobních údajů je chráněná, jak je popsáno v tématu [datové přenosy v Evropské unii](https://www.linkedin.com/help/linkedin/answer/62533).

## <a name="next-steps"></a>Další kroky

* [LinkedIn v aplikacích Microsoftu pomocí pracovního nebo školního účtu](https://www.linkedin.com/help/linkedin/answer/84077)
