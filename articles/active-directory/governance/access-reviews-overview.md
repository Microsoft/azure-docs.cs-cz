---
title: Co jsou kontroly přístupu? -Azure Active Directory | Microsoft Docs
description: Pomocí kontrol přístupu Azure Active Directory můžete řídit členství ve skupinách a přístup k aplikacím, aby splňovaly iniciativy zásad správného řízení, řízení rizik a dodržování předpisů ve vaší organizaci.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.subservice: compliance
ms.date: 08/18/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.custom: contperfq1
ms.openlocfilehash: c69a5e153377eee86eaf0c43d6c982dee2938ddf
ms.sourcegitcommit: 9c3cfbe2bee467d0e6966c2bfdeddbe039cad029
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/24/2020
ms.locfileid: "88783667"
---
# <a name="what-are-azure-ad-access-reviews"></a>Co jsou kontroly přístupu Azure AD?

Kontroly přístupu Azure Active Directory (Azure AD) umožňují organizacím efektivně spravovat členství ve skupinách, přístup k podnikovým aplikacím a přiřazování rolí. Přístup uživatele se může pravidelně kontrolovat, aby se zajistilo, že budou mít přístup jenom přípravní lidé.

Tady je video, které poskytuje rychlý přehled kontrol přístupu:

>[!VIDEO https://www.youtube.com/embed/kDRjQQ22Wkk]

## <a name="why-are-access-reviews-important"></a>Proč jsou recenze přístupů důležité?

Azure AD umožňuje spolupracovat s uživateli ve vaší organizaci a s externími uživateli. Uživatelé můžou připojit skupiny, pozvat hosty, připojit se ke cloudovým aplikacím a vzdáleně pracovat ze svých pracovních nebo osobních zařízení. Pohodlí používání samoobslužné služby vedlo k nutnosti lepších funkcí správy přístupu.

- Jak se noví zaměstnanci připojí, jak zajistit, aby měli přístup, který musí být produktivní?
- Když lidé přesunou týmy nebo odejdou ze společnosti, jak se stará přístup odeberou?
- Nadměrné oprávnění k přístupu může vést k ohrožení zabezpečení.
- Nenadměrný přístup může také vést k tomu, že se výsledky auditu označují jako nedostatečné řízení přístupu.
- Je nutné proaktivně zapojit vlastníky prostředků, aby bylo zajištěno, že budou pravidelně kontrolovat, kdo má přístup ke svým prostředkům.

## <a name="when-should-you-use-access-reviews"></a>Kdy byste měli používat recenze přístupu?

- **Příliš mnoho uživatelů v privilegovaných rolích:** Je vhodné zjistit, kolik uživatelů má přístup pro správu, kolik z nich je globálních správců, a pokud existují pozvaní hostů nebo partneři, kteří se po přiřazení k úloze správy neodebrali. V [rolích Azure AD](../privileged-identity-management/pim-how-to-perform-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) , jako jsou globální správci nebo [role prostředků Azure](../privileged-identity-management/pim-resource-roles-perform-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) , jako je například správce přístupu uživatele, můžete znovu certifikovat přiřazení rolí uživatelům v prostředí [Azure AD Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) .
- **Když Automation není možné:** Můžete vytvořit pravidla pro dynamické členství ve skupinách zabezpečení nebo skupinách Office 365, ale co když data o PERSONÁLNÍm oddělení nejsou ve službě Azure AD, nebo pokud uživatelé stále potřebují přístup, když opustí skupinu a vyškolí jejich náhradu? Pak můžete vytvořit revizi této skupiny, abyste měli jistotu, že budou mít přístup i nadále potřebují mít přístup.
- **Když se skupina používá pro nový účel:** Pokud máte skupinu, která bude synchronizovaná s Azure AD, nebo pokud chcete povolit službu Salesforce pro všechny uživatele ve skupině prodejního týmu, je vhodné požádat vlastníka skupiny, aby zkontroloval členství ve skupině před tím, než se skupina používá v jiném rizikovém obsahu.
- **Přístup k důležitým podnikovým datům:** u určitých prostředků se může vyžadovat, aby uživatelé mimo ni požádali, aby se pravidelně odhlásili a měli odůvodněné důvody k tomu, proč potřebují přístup pro účely auditování.
- **Chcete-li zachovat seznam výjimek zásad:** V ideálním světě by všichni uživatelé za účelem zabezpečení přístupu k prostředkům vaší organizace dodržovali zásady přístupu. Někdy ale existují obchodní případy, které vyžadují, abyste měli výjimky. Jako správce IT můžete spravovat tuto úlohu, vyhnout se nedohledu nad výjimkami zásad a poskytovat auditorům kontrolu nad tím, že se tyto výjimky pravidelně kontrolují.
- **Požádejte vlastníky skupiny, abyste potvrdili, že stále potřebují hosty ve svých skupinách:** Přístup zaměstnanců může být automatizovaný s některými místními identitami a správou přístupu (IAM), ale ne s pozvanými hosty. Pokud skupina poskytuje přístup hostů k firemnímu citlivému obsahu, pak je zodpovědností vlastníka skupiny na potvrzení, že hosté mají k dispozici legitimní obchodní nutnost přístupu.
- **Pravidelně opakují se recenze:** Můžete nastavit opakované kontroly přístupu uživatelů při nastavených frekvencích, jako je týdenní, měsíční, čtvrtletní nebo roční a kontroloři se budou informovat na začátku každé recenze. Recenzenti mohou schvalovat nebo odmítat přístup pomocí popisného rozhraní a s využitím inteligentních doporučení.

>[!NOTE]
>Pokud jste připraveni vyzkoušet kontroly přístupu, přečtěte si [téma Vytvoření kontroly přístupu skupin nebo aplikací](create-access-review.md) .

## <a name="where-do-you-create-reviews"></a>Kde můžete vytvářet recenze?

V závislosti na tom, co chcete zkontrolovat, vytvoříte kontrolu přístupu v rámci kontrol přístupu Azure AD, Azure AD Enterprise Apps (ve verzi Preview) nebo Azure AD PIM.

| Přístupová práva uživatelů | Revidující mohou být | Kontrola vytvořená v | Možnosti kontrolora |
| --- | --- | --- | --- |
| Členové skupiny zabezpečení</br>Členové skupiny Office | Určení kontroloři</br>Vlastníci skupiny</br>Samoobslužné kontroly | Kontroly přístupu Azure AD</br>Skupiny Azure AD | Přístupový panel |
| Přiřazeno k připojené aplikaci | Určení kontroloři</br>Samoobslužné kontroly | Kontroly přístupu Azure AD</br>Aplikace Azure AD Enterprise (ve verzi Preview) | Přístupový panel |
| Role Azure AD | Určení kontroloři</br>Samoobslužné kontroly | [Azure AD PIM](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) | portál Azure |
| Role prostředku Azure | Určení kontroloři</br>Samoobslužné kontroly | [Azure AD PIM](../privileged-identity-management/pim-resource-roles-start-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) | portál Azure |

## <a name="license-requirements"></a>Licenční požadavky

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

### <a name="how-many-licenses-must-you-have"></a>Kolik licencí potřebujete?

Váš adresář potřebuje aspoň tolik licencí Azure AD Premium P2 jako počet zaměstnanců, kteří budou provádět následující úlohy:

- Členové a uživatelé typu Host, kteří jsou přiřazeni jako kontroloři
- Členové a uživatelé typu Host, kteří provádějí samy si kontrolu
- Vlastníci skupiny, kteří provádějí kontrolu přístupu
- Vlastníci aplikace, kteří provádějí kontrolu přístupu

Licence Azure AD Premium P2 nejsou **vyžadovány** pro uživatele s rolemi globální správce nebo Správce uživatelů, kteří nastavili kontroly přístupu, nakonfigurují nastavení nebo použijí rozhodnutí z recenzí.

Pro každou placená licenci Azure AD Premium P2, kterou přiřadíte k jednomu z uživatelů vaší organizace, můžete použít Azure AD Business-to-Business (B2B) k pozvání až pěti uživatelů typu Host v rámci osvobození od externích uživatelů. Tito uživatelé typu Host můžou také používat funkce Azure AD Premium P2. Další informace najdete v tématu [pokyny k licencování spolupráce Azure AD B2B](../external-identities/licensing-guidance.md).

Další informace o licencích najdete v tématu [přiřazení nebo odebrání licencí pomocí portálu Azure Active Directory](../fundamentals/license-users-groups.md).

### <a name="example-license-scenarios"></a>Příklady scénářů licencí

Tady je několik ukázkových scénářů licencí, které vám pomůžou určit počet licencí, které musíte mít.

| Scénář | Výpočet | Počet licencí |
| --- | --- | --- |
| Správce vytvoří kontrolu přístupu skupiny A s 75 uživateli a 1 vlastníkem skupiny a přiřadí vlastníka skupiny jako kontrolora. | 1 licence pro vlastníka skupiny jako kontrolor | 1 |
| Správce vytvoří kontrolu přístupu skupiny B s 500 uživateli a 3 vlastníky skupiny a přiřadí 3 vlastníky skupiny jako kontrolory. | 3 licence pro každého vlastníka skupiny jako kontroloři | 3 |
| Správce vytvoří kontrolu přístupu skupiny B s 500 uživateli. Provede si ho samy se změnami. | licence 500 pro každého uživatele jako kontroloři samoobslužné. | 500 |
| Správce vytvoří kontrolu přístupu skupiny C s 50 členskými uživateli a 25 uživateli typu Host. Provede si ho samy se změnami. | licence 50 pro každého uživatele jako kontroloři pro sebe.<br/>(uživatelé typu Host jsou zahrnuti v požadovaném poměru 1:5.) | 50 |
| Správce vytvoří kontrolu přístupu skupiny D s 6 členskými uživateli a 108 uživateli typu Host. Provede si ho samy se změnami. | 6 licencí pro každého uživatele jako samoobslužných revidujících + 16 dalších licencí, které pokrývají všechny 108 uživatelů typu Host v požadované 1:5 poměru. 6 licencí, které zahrnují 6 \* 5 = 30 uživatelů typu Host. Pro zbývající (108-6 \* 5) = 78 uživatelů typu Host se vyžaduje 78/5 = 16 dalších licencí. Proto jsou vyžadovány celkem 6 + 16 = 22 licencí. | 22 |

## <a name="next-steps"></a>Další kroky

- [Vytvoření kontroly přístupu skupin nebo aplikací](create-access-review.md)
- [Vytváření kontroly přístupu uživatelů v roli správce Azure AD](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)
- [Kontrola přístupu ke skupinám nebo aplikacím](perform-access-review.md)
- [Dokončení kontroly přístupu skupin nebo aplikací](complete-access-review.md)