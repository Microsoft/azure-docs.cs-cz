---
title: Co jsou kontroly přístupu? - Služba Azure Active Directory | Dokumenty společnosti Microsoft
description: Pomocí kontrol přístupu služby Azure Active Directory můžete řídit členství ve skupinách a přístup k aplikacím, abyste mohli ve vaší organizaci plnit iniciativy v oblasti zásad správného řízení, řízení rizik a dodržování předpisů.
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
ms.openlocfilehash: 5499c8808c3916842071df1f03a865efd98719f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262084"
---
# <a name="what-are-azure-ad-access-reviews"></a>Co jsou kontroly přístupu Azure AD?

Azure Active Directory (Azure AD) přístup recenze umožňují organizacím efektivně spravovat členství ve skupinách, přístup k podnikovým aplikacím a přiřazení rolí. Přístup uživatele lze pravidelně kontrolovat, aby bylo zajištěno, že pouze ti praví lidé mají trvalý přístup.

Tady je video, které poskytuje rychlý přehled o recenzích přístupu:

>[!VIDEO https://www.youtube.com/embed/kDRjQQ22Wkk]

## <a name="why-are-access-reviews-important"></a>Proč jsou kontroly přístupu důležité?

Azure AD umožňuje interně spolupracovat v rámci organizace a s uživateli z externích organizací, jako jsou partneři. Uživatelé se mohou připojit ke skupinám, pozvat hosty, připojit se ke cloudovým aplikacím a vzdáleně pracovat ze svých pracovních nebo osobních zařízení. Pohodlí využití výkonu samoobsluhy vedlo k potřebě lepších možností správy přístupu.

- Jak zajistíte, že noví zaměstnanci budou mít správný přístup k produktivitě?
- Jak lidé přesouvají týmy nebo opouštějí společnost, jak zajistíte, aby byl jejich starý přístup odstraněn, zejména pokud se jedná o hosty?
- Nadměrná přístupová práva mohou vést ke zjištěním auditu a kompromisům, protože naznačují nedostatečnou kontrolu nad přístupem.
- Musíte proaktivně spolupracovat s vlastníky prostředků, abyste zajistili, že pravidelně kontrolují, kdo má přístup k jejich prostředkům.

## <a name="when-to-use-access-reviews"></a>Kdy použít kontroly přístupu?

- **Příliš mnoho uživatelů v privilegovaných rolích:** Je vhodné zkontrolovat, kolik uživatelů má přístup pro správu, kolik z nich jsou globální správci a zda existují pozvaní hosté nebo partneři, kteří nebyli odebráni poté, co byli přiřazeni k úkolu správy. Můžete znovu certifikovat uživatele přiřazení rolí v [rolích Azure AD,](../privileged-identity-management/pim-how-to-perform-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) jako jsou globální správci nebo [role prostředků Azure,](../privileged-identity-management/pim-resource-roles-perform-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) jako je správce přístupu uživatelů v prostředí [Správy privilegovaných identit Azure AD (PIM).](../privileged-identity-management/pim-configure.md)
- **Pokud je automatizace neproveditelná:** Můžete vytvořit pravidla pro dynamické členství ve skupinách zabezpečení nebo skupinách Office 365, ale co když data hr nejsou ve službě Azure AD nebo pokud uživatelé stále potřebují přístup po opuštění skupiny, aby mohli trénovat jejich nahrazení? Potom můžete vytvořit recenzi na tuto skupinu, abyste zajistili, že ti, kteří stále potřebují přístup, budou mít trvalý přístup.
- **Pokud je skupina použita k novému účelu:** Pokud máte skupinu, která se bude synchronizovat s Azure AD, nebo pokud máte v plánu povolit aplikaci Salesforce pro všechny ve skupině prodejní tým, bylo by užitečné požádat vlastníka skupiny o kontrolu členství ve skupině před skupinou používá v jiném obsahu rizika.
- **Přístup k důležitým obchodním datům:** u určitých zdrojů může být nutné požádat osoby mimo IT, aby se pravidelně odhlašovali, a zdůvodnit, proč potřebují přístup pro účely auditu.
- **Chcete-li udržovat seznam výjimek zásady:** V ideálním světě by všichni uživatelé dodržovali zásady přístupu k zabezpečení přístupu k prostředkům vaší organizace. Někdy však existují obchodní případy, které vyžadují, abyste udělali výjimky. Jako správce IT můžete tento úkol spravovat, vyhnout se dohledu nad výjimkami zásad a poskytnout auditorům důkaz, že tyto výjimky jsou pravidelně kontrolovány.
- **Požádejte vlastníky skupin, aby potvrdili, že stále potřebují hosty ve svých skupinách:** Přístup zaměstnanců může být automatizován s některými místními IAM, ale ne pozvanými hosty. Pokud skupina poskytuje hostům přístup k firemnímu citlivému obsahu, je odpovědností vlastníka skupiny potvrdit, že hosté stále potřebují přístup.
- **Pravidelně se recenze opakují:** Můžete nastavit opakované kontroly přístupu uživatelů na nastavených frekvencích, jako je týdenní, měsíční, čtvrtletní nebo roční, a recenzenti budou upozorněni na začátku každé recenze. Recenzenti mohou schválit nebo odepřít přístup pomocí přátelského rozhraní a pomocí inteligentních doporučení.

## <a name="where-do-you-create-reviews"></a>Kde vytváříte recenze?

V závislosti na tom, co chcete zkontrolovat, vytvoříte kontrolu přístupu v azure ad kontroly přístupu, Azure AD podnikové aplikace (ve verzi preview) nebo Azure AD PIM.

| Přístupová práva uživatelů | Recenzenti mohou být | Recenze vytvořená v | Zkušenosti recenzenta |
| --- | --- | --- | --- |
| Členové skupiny zabezpečení</br>Členové skupiny Office | Zadali recenzenti</br>Vlastníci skupin</br>Vlastní recenze | Kontroly přístupu Azure AD</br>Skupiny Azure AD | Přístupový panel |
| Přiřazeno k připojené aplikaci | Zadali recenzenti</br>Vlastní recenze | Kontroly přístupu Azure AD</br>Podnikové aplikace Azure AD (ve verzi Preview) | Přístupový panel |
| Role Azure AD | Zadali recenzenti</br>Vlastní recenze | [Azure AD PIM](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) | portál Azure |
| Role prostředků Azure | Zadali recenzenti</br>Vlastní recenze | [Azure AD PIM](../privileged-identity-management/pim-resource-roles-start-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) | portál Azure |


## <a name="create-access-reviews"></a>Vytvoření kontrol přístupu

Chcete-li vytvořit kontroly přístupu, postupujte takto:

1. Přejděte na [portál Azure,](https://portal.azure.com) kde můžete spravovat kontroly přístupu a přihlásit se jako globální správce nebo správce uživatelů.

1. Vyhledejte a vyberte **Azure Active Directory**.

      ![Hledání azure portálu pro Azure Active Directory](media/access-reviews-overview/search-azure-active-directory.png)

1. Vyberte **zásady správného řízení identity**.

1. Na stránce Začínáme klikněte na tlačítko **Vytvořit kontrolu přístupu.**

   ![Úvodní stránka access recenze](./media/access-reviews-overview/access-reviews-overview-create-access-reviews.png) 


## <a name="learn-about-access-reviews"></a>Další informace o kontroly přístupu

Další informace o vytváření a provádění recenzí přístupu najdete v této krátké ukázce:

>[!VIDEO https://www.youtube.com/embed/6KB3TZ8Wi40]

Pokud jste připraveni nasadit kontroly přístupu ve vaší organizaci, postupujte podle následujících kroků ve videu na palubě, trénovat správce a vytvořit první kontrolu přístupu!

>[!VIDEO https://www.youtube.com/embed/X1SL2uubx9M]

## <a name="license-requirements"></a>Licenční požadavky

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

### <a name="how-many-licenses-must-you-have"></a>Kolik licencí musíte mít?

Ujistěte se, že váš adresář má alespoň tolik licencí Azure AD Premium P2, jako máte zaměstnance, kteří budou provádět následující úkoly:

- Uživatelé a uživatelé typu Host, kteří jsou přiřazeni jako recenzenti
- Uživatelé a uživatelé typu Host, kteří provádějí vlastní kontrolu
- Vlastníci skupin, kteří provádějí kontrolu přístupu
- Vlastníci aplikací, kteří provádějí kontrolu přístupu

Licence Azure AD Premium P2 **nejsou** vyžadovány pro následující úkoly:

- Pro uživatele s rolemi globálního správce nebo správce uživatelů, kteří nastavují kontroly přístupu, nastavují nastavení nebo používají rozhodnutí z recenzí, nejsou vyžadovány žádné licence.

Pro každou placenou licenci Azure AD Premium P2, kterou přiřadíte jednomu z uživatelů vlastní organizace, můžete použít azure ad business-to-business (B2B) k pozvání až pěti uživatelů typu Host v rámci příspěvku na externí uživatele. Tito uživatelé typu Host můžou taky používat funkce Azure AD Premium P2. Další informace najdete [v tématu Azure AD B2B pokyny k licencování spolupráce](../b2b/licensing-guidance.md).

Další informace o licencích najdete v tématu [Přiřazení nebo odebrání licencí pomocí portálu Azure Active Directory](../fundamentals/license-users-groups.md).

### <a name="example-license-scenarios"></a>Příklady scénářů licencí

Zde je několik příkladů licenčních scénářů, které vám pomohou určit počet licencí, které musíte mít.

| Scénář | Výpočet | Počet licencí |
| --- | --- | --- |
| Správce vytvoří kontrolu přístupu skupiny A se 75 uživateli a 1 vlastníkem skupiny a přiřadí vlastníka skupiny jako recenzenta. | 1 licence pro vlastníka skupiny jako recenzenta | 1 |
| Správce vytvoří kontrolu přístupu skupiny B s 500 uživateli a 3 vlastníky skupin a přiřadí 3 vlastníky skupin jako recenzenty. | 3 licence pro každého vlastníka skupiny jako recenzenty | 3 |
| Správce vytvoří kontrolu přístupu skupiny B s 500 uživateli. Dělá to sebehodnocení. | 500 licencí pro každého uživatele jako seberecenzentů | 500 |
| Správce vytvoří kontrolu přístupu skupiny C s 50 členskými uživateli a 25 uživateli typu Host. Dělá to sebehodnocení. | 50 licencí pro každého uživatele jako seberecenzentů.<br/>(Uživatelé typu Host jsou zahrnuti v požadovaném poměru 1:5) | 50 |
| Správce vytvoří kontrolu přístupu skupiny D se 6 členskými uživateli a 108 uživateli typu Host. Dělá to sebehodnocení. | 6 licencí pro každého uživatele jako seberecenzentů + 16 dalších licencí, které pokryjí všech 108 uživatelů typu Host v požadovaném poměru 1:5. 6 licencí, které\*pokrývají 6 5 = 30 uživatelů hosta. Pro zbývající (108-6\*5)=78 uživatelů typu Host je vyžadováno 78/5=16 dalších licencí. Celkem je tedy vyžadováno 6+16=22 licencí. | 22 |

## <a name="next-steps"></a>Další kroky

- [Vytvoření kontroly přístupu skupin nebo aplikací](create-access-review.md)
- [Vytváření kontroly přístupu uživatelů v roli správce Azure AD](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)
- [Kontrola přístupu ke skupinám nebo aplikacím](perform-access-review.md)
- [Vyplnění kontroly přístupu skupin nebo aplikací](complete-access-review.md)
