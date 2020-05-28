---
title: Co jsou kontroly přístupu? -Azure Active Directory | Microsoft Docs
description: Pomocí kontrol přístupu Azure Active Directory můžete řídit členství ve skupinách a přístup k aplikacím, aby splňovaly iniciativy zásad správného řízení, řízení rizik a dodržování předpisů ve vaší organizaci.
services: active-directory
documentationcenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 01/10/2020
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: a711fa522f6b105eb98d57d4f63afb90be255b3f
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2020
ms.locfileid: "83995033"
---
# <a name="what-are-azure-ad-access-reviews"></a>Co jsou kontroly přístupu Azure AD?

Kontroly přístupu Azure Active Directory (Azure AD) umožňují organizacím efektivně spravovat členství ve skupinách, přístup k podnikovým aplikacím a přiřazování rolí. Přístup uživatele se může pravidelně kontrolovat, aby se zajistilo, že budou mít přístup jenom přípravní lidé.

Tady je video, které poskytuje rychlý přehled kontrol přístupu:

>[!VIDEO https://www.youtube.com/embed/kDRjQQ22Wkk]

## <a name="why-are-access-reviews-important"></a>Proč jsou recenze přístupů důležité?

Azure AD umožňuje interně spolupracovat v rámci vaší organizace a s uživateli z externích organizací, jako jsou partneři. Uživatelé můžou připojit skupiny, pozvat hosty, připojit se ke cloudovým aplikacím a vzdáleně pracovat ze svých pracovních nebo osobních zařízení. Pohodlí využívání možnosti samoobslužné služby vedlo k nutnosti lepšího řízení přístupu.

- Když se noví zaměstnanci připojí, jak zajistit, že mají správný přístup k produktivitě?
- Když lidé přesunou týmy nebo odejdou ze společnosti, jak si zaručíte, že jejich starý přístup se odebere, obzvláště když zahrnuje hosty?
- Nadměrně přístupná práva můžou vést k tomu, že auditují zjištění a ohrožení bezpečnosti, protože označují nedostatečné řízení přístupu.
- Je nutné proaktivně zapojit vlastníky prostředků, aby bylo zajištěno, že budou pravidelně kontrolovat, kdo má přístup ke svým prostředkům.

## <a name="when-to-use-access-reviews"></a>Kdy použít recenze přístupu?

- **Příliš mnoho uživatelů v privilegovaných rolích:** Je vhodné zjistit, kolik uživatelů má přístup pro správu, kolik z nich je globálních správců, a pokud existují pozvaní hostů nebo partneři, kteří se po přiřazení k úloze správy neodebrali. V [rolích Azure AD](../privileged-identity-management/pim-how-to-perform-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) , jako jsou globální správci nebo [role prostředků Azure](../privileged-identity-management/pim-resource-roles-perform-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) , jako je například správce přístupu uživatele, můžete znovu certifikovat přiřazení rolí uživatelům v prostředí [Azure AD Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) .
- **Pokud je automatizace neproveditelná:** Můžete vytvořit pravidla pro dynamické členství ve skupinách zabezpečení nebo skupinách Office 365, ale co když data o PERSONÁLNÍm oddělení nejsou ve službě Azure AD, nebo pokud uživatelé stále potřebují přístup, když opustí skupinu a vyškolí jejich náhradu? Pak můžete vytvořit revizi této skupiny, abyste měli jistotu, že budou mít přístup i nadále potřebují mít přístup.
- **Když se skupina používá pro nový účel:** Pokud máte skupinu, která bude synchronizovaná s Azure AD, nebo pokud chcete povolit službu Salesforce pro všechny uživatele ve skupině prodejního týmu, je vhodné požádat vlastníka skupiny, aby zkontroloval členství ve skupině před tím, než se skupina používá v jiném rizikovém obsahu.
- **Přístup k důležitým podnikovým datům:** u určitých prostředků se může vyžadovat, aby uživatelé mimo ni požádali, aby se pravidelně odhlásili a měli odůvodněné důvody k tomu, proč potřebují přístup pro účely auditování.
- **Chcete-li zachovat seznam výjimek zásad:** V ideálním světě by všichni uživatelé za účelem zabezpečení přístupu k prostředkům vaší organizace dodržovali zásady přístupu. Někdy ale existují obchodní případy, které vyžadují, abyste měli výjimky. Jako správce IT můžete spravovat tuto úlohu, vyhnout se nedohledu nad výjimkami zásad a poskytovat auditorům kontrolu nad tím, že se tyto výjimky pravidelně kontrolují.
- **Požádejte vlastníky skupiny, abyste potvrdili, že stále potřebují hosty ve svých skupinách:** Přístup zaměstnanců může být automatizovaný s některými místními nástroji IAM, ale nezvanými hosty. Pokud skupina poskytuje přístup hostů k firemnímu citlivému obsahu, pak je zodpovědností vlastníka skupiny na potvrzení, že hosté mají k dispozici legitimní obchodní nutnost přístupu.
- **Pravidelně opakují se recenze:** Můžete nastavit opakované kontroly přístupu uživatelů při nastavených frekvencích, jako je týdenní, měsíční, čtvrtletní nebo roční a kontroloři se budou informovat na začátku každé recenze. Recenzenti mohou schvalovat nebo odmítat přístup pomocí popisného rozhraní a s využitím inteligentních doporučení.

## <a name="where-do-you-create-reviews"></a>Kde můžete vytvářet recenze?

V závislosti na tom, co chcete zkontrolovat, vytvoříte kontrolu přístupu v rámci kontrol přístupu Azure AD, Azure AD Enterprise Apps (ve verzi Preview) nebo Azure AD PIM.

| Přístupová práva uživatelů | Revidující mohou být | Kontrola vytvořená v | Možnosti kontrolora |
| --- | --- | --- | --- |
| Členové skupiny zabezpečení</br>Členové skupiny Office | Určení kontroloři</br>Vlastníci skupiny</br>Samoobslužné kontroly | Kontroly přístupu Azure AD</br>Skupiny Azure AD | Přístupový panel |
| Přiřazeno k připojené aplikaci | Určení kontroloři</br>Samoobslužné kontroly | Kontroly přístupu Azure AD</br>Aplikace Azure AD Enterprise (ve verzi Preview) | Přístupový panel |
| Role Azure AD | Určení kontroloři</br>Samoobslužné kontroly | [Azure AD PIM](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) | portál Azure |
| Role prostředku Azure | Určení kontroloři</br>Samoobslužné kontroly | [Azure AD PIM](../privileged-identity-management/pim-resource-roles-start-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) | portál Azure |


## <a name="create-access-reviews"></a>Vytvořit kontroly přístupu

Chcete-li vytvořit kontroly přístupu, postupujte podle následujících kroků:

1. Přejděte na [Azure Portal](https://portal.azure.com) a spravujte kontroly přístupu a přihlaste se jako globální správce nebo Správce uživatelů.

1. Vyhledejte a vyberte **Azure Active Directory**.

      ![Azure Portal hledání Azure Active Directory](media/access-reviews-overview/search-azure-active-directory.png)

1. Vyberte zásady **správného řízení identity**.

1. Na úvodní stránce klikněte na tlačítko **vytvořit kontrolu přístupu** .

   ![Úvodní stránka kontroly přístupu](./media/access-reviews-overview/access-reviews-overview-create-access-reviews.png) 

### <a name="creating-access-review-on-a-group-that-can-be-assigned-to-azure-ad-role"></a>Vytváření kontroly přístupu u skupiny, kterou je možné přiřadit k roli Azure AD
Pokud pracujete na nejnovější verzi kontrol přístupu (ve výchozím nastavení jsou vaši kontroloři přesměrováni na **můj přístup** ), pak jenom globální správce může vytvořit kontrolu přístupu pro skupiny, které umožňují přiřazení role. Pokud ale používáte starší verzi kontrol přístupu (ve výchozím nastavení jsou vaši kontroloři přesměrováni na **přístupový panel** ), pak globální správce i Správce uživatelů můžou zkontrolovat přístup pro skupiny s přiřazením rolí.  

Nové prostředí bude zavedeno pro všechny zákazníky od 1. srpna 2020, ale pokud byste chtěli provést upgrade dřív, uveďte prosím požadavek v tématu kontroly [přístupu Azure AD – aktualizované možnosti kontrolora v registraci přístupu](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR5dv-S62099HtxdeKIcgO-NUOFJaRDFDWUpHRk8zQ1BWVU1MMTcyQ1FFUi4u).

[Přečtěte si další informace o přiřazování skupin k rolím Azure AD](https://go.microsoft.com/fwlink/?linkid=2103037).

## <a name="learn-about-access-reviews"></a>Další informace o kontrolách přístupu

Pokud chcete získat další informace o vytváření a provádění kontrol přístupu, podívejte se do této krátké ukázky:

>[!VIDEO https://www.youtube.com/embed/6KB3TZ8Wi40]

Pokud jste připraveni nasadit kontroly přístupu ve vaší organizaci, postupujte podle těchto kroků ve videu, Projděte si správce a vytvořte svou první kontrolu přístupu.

>[!VIDEO https://www.youtube.com/embed/X1SL2uubx9M]

## <a name="license-requirements"></a>Licenční požadavky

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

### <a name="how-many-licenses-must-you-have"></a>Kolik licencí potřebujete?

Zajistěte, aby měl váš adresář aspoň tolik licencí Azure AD Premium P2, protože máte zaměstnance, kteří budou provádět následující úlohy:

- Členové a uživatelé typu Host, kteří jsou přiřazeni jako kontroloři
- Členové a uživatelé typu Host, kteří provádějí samy si kontrolu
- Vlastníci skupiny, kteří provádějí kontrolu přístupu
- Vlastníci aplikace, kteří provádějí kontrolu přístupu

Licence Azure AD Premium P2 nejsou **požadovány** pro následující úlohy:

- Pro uživatele s rolemi globálního správce nebo Správce uživatelů nejsou vyžadovány žádné licence, které nastavují kontroly přístupu, konfigurují nastavení nebo používají rozhodnutí z recenzí.

Pro každou placená licenci Azure AD Premium P2, kterou přiřadíte k jednomu z uživatelů vaší organizace, můžete použít Azure AD Business-to-Business (B2B) k pozvání až pěti uživatelů typu Host v rámci osvobození od externích uživatelů. Tito uživatelé typu Host můžou také používat funkce Azure AD Premium P2. Další informace najdete v tématu [pokyny k licencování spolupráce Azure AD B2B](../b2b/licensing-guidance.md).

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
