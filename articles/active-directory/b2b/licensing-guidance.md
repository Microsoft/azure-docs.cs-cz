---
title: Spolupráce Azure Active Directory s B2B licencování pokyny | Microsoft Docs
description: Azure Active Directory s B2B, které nevyžaduje žádný spolupráce placené licence služby Azure AD, ale můžete také platby funkce za uživatele typu Host B2B
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 08/09/2017
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: beeb7801465d38743fdd654496c7b1b8e3ba1cc3
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2018
ms.locfileid: "34267375"
---
# <a name="azure-active-directory-b2b-collaboration-licensing-guidance"></a>Doprovodné materiály k licencování spolupráce Azure Active Directory s B2B

Možnosti spolupráce Azure AD B2B můžete pozvat uživatele typu Host do vašeho klienta Azure AD a povolení jejich přístupu k služby Azure AD a dalším prostředkům ve vaší organizaci. Pokud chcete poskytnout přístup k placené funkce Azure AD, vhodné musí mít licenci uživatele typu Host spolupráce B2B Azure AD licence. 

Zejména:
* Volné možnosti Azure AD jsou k dispozici pro uživatele typu Host bez další licence.
* Pokud chcete poskytnout přístup k placené funkce Azure AD s B2B uživatelům, musíte mít dostatek licencí pro podporu těchto uživatelů typu Host B2B.
* Klient služby pozváním s placenou licenci Azure AD má spolupráce B2B používat práva dalším pět B2B hosta uživatelům pozvánku, abyste klienta.
* Zákazníka, který vlastní pozváním klienta musí být k určení, kolik spolupráce B2B uživatelé potřebují placené možnosti služby Azure AD. V závislosti na placené služby Azure AD funkce, které chcete použít pro vaše uživatele typu Host, musí mít dostatečně Azure AD placené licence pro uživatele spolupráce B2B ve stejné poměr 5:1.

Uživatel guest spolupráce B2B je přidána jako uživatel z do partnerské firmy, není zaměstnanec vaší organizace nebo zaměstnanec různých obchodních ve vašem konglomerátu. Uživatel guest B2B můžete přihlásit pomocí externí přihlašovací nebo přihlašovací údaje ve vlastnictví ve vaší organizaci, jak je popsáno v tomto článku. 

Jinými slovy B2B licencování nastavena samo o tom, jak se uživatel ověřuje, ale spíše vztahu uživatele pro vaši organizaci. Pokud tito uživatelé nejsou partnery, zachází jinak s podmínkami licenční smlouvy. Nejsou považovány za jako uživatel spolupráce B2B pro účely licencování i v případě, že jejich UserType je označena jako "Guest." By měly být licencované za normálních okolností na jednu licenci na uživatele. Mezi tyto uživatele patří:
* Vaši zaměstnanci
* Zaměstnanci přihlášení pomocí externí identity
* Zaměstnanec různých obchodních ve vašem konglomerátu


## <a name="licensing-examples"></a>Licencování příklady
- Chce zákazník pozvat 100 uživatelů spolupráce B2B jeho klienta Azure AD. Zákazník přiřadí správu přístupu a zřizování pro všechny uživatele, ale 50 uživatelů také vyžadovat vícefaktorové ověřování a podmíněného přístupu. 10 licence Azure AD Basic a 10 licence Azure AD Premium P1 tak, aby pokrývalo tito uživatelé B2B správně, musí si koupit tohoto zákazníka. Pokud zákazník plánuje využívat funkce ochrany identit s B2B uživateli, se musí mít licenci Azure AD Premium P2 tak, aby pokrývalo pozvané uživatele ve stejné poměr 5:1.
- Zákazník má 10 zaměstnanci, kteří mají všechny aktuálně licenci s Azure AD Premium P1. Chtějí pozvat 60 B2B uživatelé, kteří vyžadovat vícefaktorové ověřování (MFA). V části pravidla licencování 5:1 zákazník musí mít nejméně 12 licenci Azure AD Premium P1 tak, aby pokrývalo všichni uživatelé 60 spolupráce B2B. Vzhledem k tomu, že už mají 10 Premium P1 licence pro jejich 10 zaměstnanci, mají práva k pozvat 50 uživatelů s B2B s Premium P1 funkcí, jako je vícefaktorové ověřování. V tomto příkladu pak se musí zakoupit 2 Další Premium P1 licence tak, aby pokrývalo zbývající 10 uživatelů spolupráce B2B.

> [!NOTE]
> Neexistuje žádný způsob ještě chcete přiřadit licence přímo na B2B uživatele pro povolení těchto uživatelských práv spolupráce B2B.

Zákazníka, který vlastní pozváním klienta musí být k určení, kolik spolupráce B2B uživatelé potřebují placené možnosti služby Azure AD. V závislosti na tom, které placené funkce Azure AD, kterou chcete použít pro vaše uživatele typu Host musí mít dostatečně Azure AD placené licence pro uživatele spolupráce B2B ve poměr 5:1. 

## <a name="additional-licensing-details"></a>Další podrobnosti o licencování
- Není nutné ve skutečnosti přiřadit licence uživatelským účtům, B2B. Podle toho, poměr 5:1, licencování je automaticky vypočítá a.
- Pokud ne, placené licence služby Azure AD existuje v klientovi, každého pozvané uživatele získá oprávnění, které nabízí bezplatná edice Azure AD.
- Pokud spolupráce uživatel už má placené služby Azure AD B2B licence z jejich organizace, že jeden licencí spolupráce B2B pozváním klienta využívat není.

## <a name="advanced-discussion-what-are-the-licensing-considerations-when-we-add-users-from-a-conglomerate-organization-as-members-using-your-apis"></a>Rozšířené diskusní: co jsou licenční požadavky při nemůžeme přidat uživatele z konglomerátu organizace jako "členy" pomocí vašich rozhraní API?
Uživatel typu Host B2B je ten, který je pro práci s organizace hostitele pozvat od organizace partnera. Obvykle všech ostatních případech není způsobilá jako B2B i ji používá funkce B2B. Podívejme se na dvou případech konkrétně:

1. Pokud hostitel žádostí zaměstnanec pomocí adresy příjemce
  * Tento scénář není kompatibilní se zásadami naše licencování a nedoporučuje se používat.

2. Pokud organizace hostitele přidá uživatele z jiné organizace, konglomerátu
  1. V takovém případě je uživatel vyzván pomocí rozhraní API B2B, ale tento případ se nedá tradičně B2B. V ideálním případě by měl máme těchto organizací jiných orgs uživatele pozvat jako členy (našem rozhraní API umožňuje které). V takovém případě mít licence pro přiřazení ke tito členové jim přístup k prostředkům v organizaci pozváním.

  2. Některé organizace chtít přidat uživatele jiné organizace, které budou přidány jako "Guest" jako zásada. Jsou zde dva případy:
      * Již konglomerátu organizace používá Azure AD a jsou v jiné organizaci licenci pozvané uživatele: v tomto případě není Očekáváme, že pozvaným uživatelům muset postupovat podle 1:5 vzorec nastíněny dříve v tomto dokumentu. 

      * Konglomerátu organizace nepoužívá Azure AD nebo nemá dostatek licencí: V tomto případě postupujte podle 1:5 vzorec nastíněny dříve v tomto dokumentu.

## <a name="next-steps"></a>Další postup

Na spolupráci Azure AD B2B najdete v následujících článcích články:

* [Co je spolupráce B2B ve službě Azure AD?](what-is-b2b.md)
* [Spolupráce Azure Active Directory s B2B nejčastější dotazy (FAQ)](faq.md)
