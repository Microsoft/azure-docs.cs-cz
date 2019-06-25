---
title: Co jsou kontroly přístupu? – Azure Active Directory | Dokumentace Microsoftu
description: Pomocí kontrol přístupu Azure Active Directory, můžete řídit skupiny členství a přístup k aplikacím pro splnění zásad správného řízení, řízení rizik a dodržování předpisů iniciativy ve vaší organizaci.
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
ms.subservice: compliance
ms.date: 06/05/2019
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7fcc804db66430598e72e9ebf31a8837eda1cca6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67204594"
---
# <a name="what-are-azure-ad-access-reviews"></a>Co jsou Azure AD access kontroly?

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

- **Moc velký počet uživatelů v privilegovaných rolích:** Je vhodné zkontrolovat, kolik uživatelů má přístup pro správu, kolik z nich delegovaní správci jsou globální správci, a pokud tam nějaké jsou pozváni hosté nebo partnery, kteří nebyli odebráni po přiřazení k provedení administrativní úlohy. Můžete recertifikovat uživateli přiřazení role v [role Azure AD](../privileged-identity-management/pim-how-to-perform-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) jako je například globální správce, nebo [role prostředků Azure](../privileged-identity-management/pim-resource-roles-perform-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) jako je například správce uživatelských přístupů v [Azure AD Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) prostředí.
- **Pokud je nemožné automation:** Můžete vytvořit pravidla dynamického členství pro skupiny zabezpečení nebo skupiny Office 365, ale co když HR data není ve službě Azure AD nebo pokud uživatelé stále potřebují přístup po opuštění skupiny pro jejich nahrazení trénování? Potom můžete vytvořit recenzi této skupiny tak zajistěte, aby uživatelům, kteří stále potřebují přístup mají mít nadále přístup.
- **Když skupiny se používá pro nový účel:** Pokud máte vytvořenou skupinu, která se má synchronizovat s Azure AD nebo pokud budete chtít povolit aplikaci Salesforce pro všichni členové skupiny tým prodeje, bylo by vhodné požádat vlastníka skupiny zkontrolovat členství ve skupině před skupiny se používají v různých riziko co obsahem.
- **Obchodní důležitá data access:** pro některé prostředky, může být požadováno lidi mimo žádat IT pravidelně odhlášení a zadejte odůvodnění na proč potřebují přístup pro účely auditování.
- **Chcete-li udržovat seznam výjimek pro zásady:** Ideální světě všichni uživatelé by podle zásady přístupu k zabezpečení přístupu k prostředkům vaší organizace. Někdy existují však obchodních případů, které vyžadují, abyste provedli výjimky. Jako správce IT můžete spravovat tuto úlohu, vyhnout dohledu výjimky zásad a auditoři poskytnout důkaz, že tyto výjimky jsou pravidelně kontrolovány.
- **Požádejte vlastníci skupiny a ověřte, zda že stále potřebují hostů v jejich skupinách:** S některými v místním prostředí IAM, ale nebyl pozvaný hosté může automatizovat přístup zaměstnanců. Pokud skupinu poskytuje hostů přístup k obchodní citlivý obsah a pak její odpovědnosti vlastníka skupiny. potvrďte, hosté ještě legitimní obchodní potřebu přístupu.
- **Máte pravidelně opakované kontroly:** Můžete nastavit opakování kontroly přístupu uživatelů na sadu frekvencí například týdně, měsíčně, čtvrtletně nebo ročně a recenzenti budou upozorněni na začátku každé revizi. Revidující mohli schválit nebo odepřít přístup s rozhraním popisný a díky inteligentní doporučení.

## <a name="where-do-you-create-reviews"></a>Pokud vytvoříte revize

V závislosti na tom, co chcete zkontrolovat, se vytvoří vaše kontroly přístupu ve službě Azure AD přístup kontroly, podnikových aplikací Azure AD (ve verzi preview) nebo Azure AD PIM.

| Přístupová práva uživatelů | Recenzenti mohou být | Kontrola vytvořené v | Kontrolor prostředí |
| --- | --- | --- | --- |
| Členy skupiny zabezpečení</br>Členové skupiny Office | Zadaný revidující</br>Vlastníci skupiny</br>Svým přezkoumat | Kontroly přístupu Azure AD</br>Skupiny Azure AD | Přístupový panel |
| Přiřazené k připojené aplikaci | Zadaný revidující</br>Svým přezkoumat | Kontroly přístupu Azure AD</br>Azure AD podnikové aplikace (ve verzi preview) | Přístupový panel |
| Azure AD role | Zadaný revidující</br>Svým přezkoumat | [Azure AD PIM](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) | portál Azure |
| Role prostředků Azure | Zadaný revidující</br>Svým přezkoumat | [Azure AD PIM](../privileged-identity-management/pim-resource-roles-start-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) | portál Azure |

## <a name="which-users-must-have-licenses"></a>Kteří uživatelé musí mít licence?

Každý uživatel, který komunikuje s využitím kontrol přístupu musí mít placené licence Azure AD Premium P2. Příklady obsahují:

- Správci, kteří vytvoření kontroly přístupu
- Zkontrolovat vlastníci skupiny, kteří provádějí přístupového
- Uživatelům přiřadit jako revidující
- Uživatele, kteří provádějí svým přezkoumat

Můžete také zadávat do kontrolovat svůj vlastní přístup uživatelů typu Host. Každé placené Azure AD Premium P2 licencí, přiřadit jeden z uživatelů vaší vlastní organizaci můžete pozvat až pět uživatelů typu Host externí příspěvek uživatele Azure AD pro mezifiremní (B2B). Tyto uživatele typu Host můžete také použít funkce Azure AD Premium P2. Další informace najdete v tématu [doprovodné materiály k licencování spolupráce B2B ve službě Azure AD](../b2b/licensing-guidance.md).

Tady je několik ukázkových scénářů, která vám pomůže určit počet licencí, které potřebujete.

| Scénář | Výpočet | Požadovaný počet licencí |
| --- | --- | --- |
| Správce vytvoří kontroly přístupu a skupiny s 500 uživateli.<br/>Přiřadí 3 vlastníci skupiny jako revidující. | 1 Správce + 3 vlastníci skupiny | 4 |
| Správce vytvoří kontroly přístupu a skupiny s 500 uživateli.<br/>Díky svým přezkoumat. | 1 Správce + 500 uživatelů jako kontrolují sami sebe | 501 |
| Správce vytvoří kontroly přístupu a skupiny s 5 uživateli a 25 uživatelů typu Host.<br/>Díky svým přezkoumat. | 1 Správce + 5 uživatelů jako kontrolují sami sebe<br/>(uživatelé typu Host jsou popsané v poměru vyžaduje 1:5) | 6 |
| Správce vytvoří kontroly přístupu a skupiny s 5 uživateli a 28 uživatele typu Host.<br/>Díky svým přezkoumat. | 1 Správce + 5 uživatelů jako kontrolují sami sebe + 1 uživatel k pokrytí uživatelů typu Host v poměru vyžaduje 1:5 | 7 |

Informace o tom, jak přiřadit licence pro vaše používá, najdete v části [přiřazení nebo odebrání licencí pomocí portálu Azure Active Directory](../fundamentals/license-users-groups.md).

## <a name="learn-about-access-reviews"></a>Seznamte se s využitím kontroly přístupu

Další informace o vytvoření a provedení kontroly přístupu, podívejte se na tato krátká ukázka:

>[!VIDEO https://www.youtube.com/embed/6KB3TZ8Wi40]

Pokud jste připravení nasadit kontroly přístupu ve vaší organizaci, postupujte podle kroků v video k uvedení, trénování vašim správcům a vytvořit první kontrolu přístupu!

>[!VIDEO https://www.youtube.com/embed/X1SL2uubx9M]

## <a name="license-requirements"></a>Licenční požadavky

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

## <a name="next-steps"></a>Další postup

- [Vytvoření kontroly přístupu skupiny nebo aplikace](create-access-review.md)
- [Vytváření kontroly přístupu uživatelů v roli správce Azure AD](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)
- [Zkontrolovat přístup do skupiny nebo aplikace](perform-access-review.md)
- [Dokončení kontroly přístupu skupiny nebo aplikace](complete-access-review.md)
