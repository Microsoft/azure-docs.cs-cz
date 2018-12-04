---
title: Co jsou kontroly přístupu Azure AD? | Dokumenty Microsoft
description: Pomocí Azure Active Directory kontrol přístupu můžete řídit skupiny členství a přístup k aplikacím pro splnění zásad správného řízení, řízení rizik a dodržování předpisů iniciativy ve vaší organizaci.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: compliance
ms.date: 11/19/2018
ms.author: rolyon
ms.reviewer: mwahl
ms.openlocfilehash: ecaceea42d64ab15676a9cb5a42ee8659e40d517
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52847282"
---
# <a name="what-are-azure-ad-access-reviews"></a>Co jsou kontroly přístupu Azure AD?

Kontroly přístupu Azure Active Directory (Azure AD) umožňují organizacím efektivně spravovat členství ve skupinách, přístup k podnikovým aplikacím a přiřazení rolí. Přístup uživatele můžete zkontrolovat v pravidelných intervalech, abyste měli jistotu, že pouze lidé mít nadále přístup.

Tady je videa, která poskytuje rychlý přehled kontrol přístupu:

>[!VIDEO https://www.youtube.com/embed/kDRjQQ22Wkk]

## <a name="why-are-access-reviews-important"></a>Proč jsou přístup kontroly důležité?

Azure AD umožňuje spolupráci interně ve vaší organizaci a s uživateli z externími organizacemi, jako jsou partneři. Uživatelé můžete připojit skupiny, zvát hosty, připojení ke cloudovým aplikacím a pracovat vzdáleně ze svůj pracovní nebo osobní zařízení. Výhodou využívá širokých možností samoobslužné funkce vedlo k potřebu lepší přístup k funkcím správy.

- Jak připojit k nové zaměstnance, jak zajistíte, že budou mít správný přístup k produktivně pracovat?
- Lidé přesunout týmy nebo ze společnosti odejít, jak můžete Ujistěte se, že jejich původní přístup se odebere, zejména v případě, že zahrnuje hosté?
- Nadměrné přístupová práva, může vést k auditu zjištění a ohrožení, jako jsou to naznačovat nedostatek řízení přístupu.
- Budete muset proaktivní zapojení s vlastníky prostředků k zajištění, že se pravidelně zkontrolovat, kdo má přístup ke svým prostředkům.

## <a name="when-to-use-access-reviews"></a>Kdy použít přístup kontroly?

- **Moc velký počet uživatelů v privilegovaných rolích:** je vhodné zkontrolovat, kolik uživatelů má přístup pro správu, kolik z nich jsou č. globálních správců, a pokud tam nějaké jsou pozváni hosté nebo partnery, kteří nebyli odebráni po přiřazení se Správce úloh. Můžete recertifikovat uživateli přiřazení role v [role adresáře Azure AD](../privileged-identity-management/pim-how-to-perform-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) jako je například globální správce, nebo [role prostředků Azure](../privileged-identity-management/pim-resource-roles-perform-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) jako je například správce uživatelských přístupů v [Azure AD Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) prostředí.
- **Když služba automation je nemožné:** můžete vytvořit pravidla pro dynamické členství pro skupiny zabezpečení nebo skupiny Office 365, ale co když HR data není ve službě Azure AD nebo pokud uživatelé stále potřebují přístup po opuštění skupiny pro jejich nahrazení trénování? Potom můžete vytvořit recenzi této skupiny tak zajistěte, aby uživatelům, kteří stále potřebují přístup mají mít nadále přístup.
- **Když skupiny se používá pro nový účel:** Pokud máte skupinu, která se bude synchronizovat s Azure AD, nebo pokud budete chtít povolit aplikaci Salesforce pro všechny uživatele ve skupině pro prodejní tým, se hodí požádat vlastníka skupiny ke kontrole membershi skupiny p před skupině se používá v různých riziko obsahu.
- **Obchodní důležitá data access:** pro některé prostředky, může být požadováno lidi mimo žádat IT pravidelně odhlášení a zadejte odůvodnění na proč potřebují přístup pro účely auditování.
- **Udržovat seznam výjimek pro zásady:** ideální světě, všemi uživateli proveďte následujícím způsobem přístup zásady zabezpečení přístupu k prostředkům vaší organizace. Někdy existují však obchodních případů, které vyžadují, abyste provedli výjimky. Jako správce IT můžete spravovat tuto úlohu, vyhnout dohledu výjimky zásad a auditoři poskytnout důkaz, že tyto výjimky jsou pravidelně kontrolovány.
- **Dotaz vlastníkům potvrďte stále potřebují hostů v jejich skupinách:** přístup zaměstnanců může automatizovat některé IAM v místním prostředí, ale nebyl pozvaný hosty. Pokud skupinu poskytuje hostů přístup k obchodní citlivý obsah a pak její odpovědnosti vlastníka skupiny. potvrďte, hosté ještě legitimní obchodní potřebu přístupu.
- **Mít revize pravidelně opakovat:** můžete nastavit opakování kontroly přístupu uživatelů na sadu frekvencí, například týdně, měsíčně, čtvrtletně nebo ročně a recenzenti budou upozorněni na začátku každé revizi. Revidující mohli schválit nebo odepřít přístup s rozhraním popisný a díky inteligentní doporučení.

## <a name="where-do-you-create-reviews"></a>Pokud vytvoříte revize

V závislosti na tom, co chcete zkontrolovat vytvoříte vaše kontroly přístupu v kontrol přístupu Azure AD, Azure AD podnikové aplikace (ve verzi preview) nebo Azure AD PIM.

| Přístupová práva uživatelů | Recenzenti mohou být | Kontrola vytvořené v | Kontrolor prostředí |
| --- | --- | --- | --- |
| Členy skupiny zabezpečení</br>Členové skupiny Office | Zadaný revidující</br>Vlastníci skupiny</br>Vlastní kontroly | Kontroly přístupu Azure AD</br>Skupiny Azure AD | Přístupový panel |
| Přiřazené k připojené aplikaci | Zadaný revidující</br>Vlastní kontroly | Kontroly přístupu Azure AD</br>Azure AD podnikové aplikace (ve verzi preview) | Přístupový panel |
| Role adresáře Azure AD | Zadaný revidující</br>Vlastní kontroly | Azure AD PIM | portál Azure |
| Role prostředků Azure | Zadaný revidující</br>Vlastní kontroly | Azure AD PIM | portál Azure |

## <a name="prerequisites"></a>Požadavky

Pomocí kontrol přístupu, musí mít jedno z těchto licencí:

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5 licence

Další informace najdete v tématu [postupy: registrace pro Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) nebo [Enterprise Mobility + Security E5 zkušební](https://aka.ms/emse5trial).

## <a name="get-started-with-access-reviews"></a>Začínáme s využitím kontroly přístupu

Další informace o vytvoření a provedení kontroly přístupu, podívejte se na tato krátká ukázka:

>[!VIDEO https://www.youtube.com/embed/6KB3TZ8Wi40]

Pokud jste připravení nasadit kontroly přístupu ve vaší organizaci, postupujte podle kroků v video k uvedení, trénování vašim správcům a vytvořit první kontrolu přístupu!

>[!VIDEO https://www.youtube.com/embed/X1SL2uubx9M]

## <a name="enable-access-reviews"></a>Povolení kontroly přístupu

Povolit kontroly přístupu, postupujte podle těchto kroků.

1. Jako globální správce nebo správce uživatelských účtů, přihlaste se k [webu Azure portal](https://portal.azure.com) ve které chcete použít přístup kontroly.

1. Klikněte na tlačítko **všechny služby** a najít přístup kontroly služby.

    ![Všechny služby - kontroly přístupu](./media/access-reviews-overview/all-services-access-reviews.png)

1. Klikněte na tlačítko **kontrol přístupu**.

    ![Připojení kontroly přístupu](./media/access-reviews-overview/onboard-button.png)

1. V navigačním seznamu klikněte na tlačítko **připojení** otevřít **zavést kontroly přístupu** stránky.

    ![Zavést kontroly přístupu](./media/access-reviews-overview/onboard-access-reviews.png)

1. Klikněte na tlačítko **vytvořit** umožňuje přístup kontroly v aktuálním adresáři. Při příštím spuštění kontroly přístupu, možnosti se povolí.

    ![Kontroly přístupu povolena](./media/access-reviews-overview/access-reviews-enabled.png)

## <a name="next-steps"></a>Další postup

- [Vytváření kontroly přístupu pro členy skupiny nebo přístupu k aplikaci](create-access-review.md)
- [Vytváření kontroly přístupu uživatelů v roli správce Azure AD](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)
- [Provádění kontroly přístupu pomocí kontrol přístupu Azure AD](perform-access-review.md)
- [Dokončení kontroly přístupu členů skupiny či přístup uživatelů k aplikaci ve službě Azure AD](complete-access-review.md)
