---
title: Požadavky na identit pro hybridní cloudové identity návrhu Azure | Microsoft Docs
description: Identifikace obchodních potřeb společnosti, které povede k definování požadavky návrhu hybridní identity.
documentationcenter: ''
services: active-directory
author: billmath
manager: mtillman
editor: ''
ms.assetid: de690978-84ef-41ad-9dfe-785722d343a1
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/18/2017
ms.component: hybrid
ms.author: billmath
ms.custom: seohack1
ms.openlocfilehash: 68a7e71cf374f10b4c5ced3e64da36138b0d2e4d
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2018
ms.locfileid: "34800720"
---
# <a name="determine-identity-requirements-for-your-hybrid-identity-solution"></a>Stanovení požadavků na identit pro hybridní řešení identit
Prvním krokem při navrhování řešení hybridní identity je určit požadavky na obchodní organizace, která bude mít využívání tohoto řešení.  Hybridní identita se spustí jako podpůrné role (podporuje jiných řešení cloud tím, že poskytuje ověřování) a přejde na, zadejte nové a zajímavé funkce, které odemknutí nové úlohy pro uživatele.  Tyto úlohy nebo služby, které chcete použít pro vaše uživatele se určují požadavky na návrh hybridní identity.  Tyto služby a úlohy muset využívají hybridní identita jak místně a v cloudu.  

Musíte si projít klíčové aspekty podniku, aby porozumět, co je nyní požadavek a co společnosti plánuje do budoucna. Pokud nemáte dlouhodobou strategii pro návrhu hybridní identity viditelnost, je pravděpodobné, že vaše řešení nebude škálovatelné podle podnikání růst a měnit.   T mu obrázek níže znázorňuje příklad architektury hybridní identity a úlohy, které jsou odemykají pro uživatele. Toto je jenom jako příklad nových možností, které můžete odemknout a doručit s strategie plnou hybridní identity. 

Některé součásti, které jsou součástí architektury hybridní identity ![](./media/hybrid-id-design-considerations/hybrid-identity-architechture.png)

## <a name="determine-business-needs"></a>Určení obchodních potřeb
Každá společnost má jiné požadavky, i když jsou tyto společnosti součástí stejného odvětví, skutečné obchodní, které požadavky se mohou lišit. Vždy můžete využít osvědčené postupy v odvětví, ale výsledku jsou to obchodní potřeby společnosti, které povede k definování požadavky návrhu hybridní identity. 

Ujistěte se, že odpoví na následující otázky identifikace obchodních potřeb:

* Je vaše společnost hledáte vyjímání provozní náklady na IT?
* Je vaše společnost hledáte zabezpečit cloudové prostředky (aplikace SaaS, infrastruktury)?
* Je hledáte modernizovat IT vaší společnosti?
  * Jsou vaši uživatelé mobilních a náročné IT oddělení vytvářet výjimky do vaší hraniční sítě umožňuje jiný typ provozu pro přístup k různým prostředkům?
  * Má vaše společnost starší verze aplikace, které musela být publikována do těchto moderní uživatelů, ale nejsou snadno přepsání?
  * Potřebuje vaše společnost k provedení těchto úloh a současně ho převést pod ovládací prvek?
* Je vaše společnost hledáte k zabezpečení identity uživatelů a snížení rizika tak, že převedou nové nástroje, které využívají odborné znalosti společnosti Microsoft Azure zabezpečení znalosti místně?
* Vaše společnost pokouší zbavit dreaded "externí" účty místně a přesuňte je do cloudu, které již nejsou spících hrozeb v místním prostředí?

## <a name="analyze-on-premises-identity-infrastructure"></a>Analýza na místní infrastrukturu identity
Nyní když máte představu o obchodních požadavků vaší společnosti, budete muset vyhodnocení na místní infrastruktuře identity. Toto testování je důležité pro definování technické požadavky pro integraci vaše aktuální řešení identity systému pro správu identity cloudu. Ujistěte se, že odpovězte si na následující otázky:

* Jaké řešení ověřování a autorizace nemá vaše společnost používat místní? 
* Vaše společnost aktuálně nemá žádné místní služby synchronizace?
* Používá vaše společnost všech poskytovatelů identit třetích stran (IdP)?

Musíte taky zajímat, cloudové služby, které vaše společnost může mít. Provádění posouzení zjistit aktuální integraci s modely SaaS, IaaS a PaaS ve vašem prostředí je velmi důležité. Ujistěte se, že během hodnocení odpovězte si na následující otázky:

* Má vaše společnost žádnou integraci s poskytovatele cloudové služby?
* Pokud ano, které služby jsou používány?
* Tato integrace je aktuálně v produkčním prostředí, nebo je pilotní nasazení?

> [!NOTE]
> Pokud nemáte přesné mapování u všech aplikací a cloudové služby, můžete použít nástroj Cloud App Discovery. Tento nástroj můžete poskytnout přehled o všech vaší organizace a příjemce cloudových aplikací vaše IT oddělení. Díky tomu se dají mnohem jednodušeji než dřív objevit ve vaší organizaci skryté IT činnosti, včetně vzorců používání a všech přístupů uživatelů ke cloudovým aplikacím. Získat Začínáme najdete [Cloud app discovery](manage-apps/cloud-app-discovery.md).
> 
> 

## <a name="evaluate-identity-integration-requirements"></a>Vyhodnotit požadavky na integraci identity
Dále musíte vyhodnotit požadavky na integraci identity. Je důležité určit technické požadavky na tom, jak se uživatelé ověřují, vzhled přítomnosti organizace v cloudu, jak organizace vám umožní autorizace a co činnost koncového uživatele bude toto testování. Ujistěte se, že odpovězte si na následující otázky:

* Bude vaše organizace používat federační, standardní ověřování nebo obojí?
* Je federation požadavek?  Z důvodu následující:
  * Jednotné přihlašování založené na protokolu Kerberos
  * Má vaše společnost místní aplikace (buď vytvořen interní nebo 3. stran), která používá SAML nebo podobné možnosti federace.
  * Vícefaktorové ověřování pomocí čipové karty. RSA SecurID atd.
  * Pravidla klientské přístupu, které řeší níže uvedené otázky:
    1. Můžete blokovat všechny externí přístup k Office 365 na základě IP adresy klienta?
    2. Můžete blokovat všechny externí přístup k Office 365, kromě Exchange ActiveSync?
    3. Můžete blokovat všechny externí přístup k Office 365, s výjimkou aplikace založené na prohlížeči (OWA, SPO)
    4. Můžete blokovat všechny externí přístup k Office 365 pro členy skupiny určené AD
* Otázky zabezpečení nebo auditování
* Stávající investice do federovaného ověřování
* Zadejte název bude naše organizace používat pro naše doménu v cloudu?
* Má organizace vlastní doménu?
  1. Veřejné a snadno ověřitelný pomocí DNS, je této domény?
  2. Pokud není, pak máte veřejné domény, který slouží k registraci alternativní UPN v AD?
* Jsou identifikátory uživatele pro cloud reprezentace konzistentní? 
* Má organizace aplikací, které vyžadují integraci s cloudovými službami?
* Organizace mají několik domén a použije všechny standardní nebo federovaného ověřování?

## <a name="evaluate-applications-that-run-in-your-environment"></a>Hodnocení aplikace, které běží ve vašem prostředí
Teď, když máte představu o místní a cloudové infrastruktury, musíte vyhodnotit aplikace, které běží v těchto prostředích. Toto testování je důležité určit požadavky na technické integrovat tyto aplikace do systému správy identity cloudu. Ujistěte se, že odpovězte si na následující otázky:

* Bydlišti bude naše aplikace
* Budou uživatelé získávat přístup k místním aplikacím?  V cloudu? Nebo obojí?
* Existují plány, které trvat existující úlohy aplikací a přesuňte je do cloudu?
* Plánujete vývoj nových aplikací, které se nacházejí místně nebo v cloudu, který bude používat cloudové ověřování?

## <a name="evaluate-user-requirements"></a>Vyhodnocení požadavků uživatele
Máte také k vyhodnocení požadavků na uživatele. Toto testování je důležité určit kroky, které bude potřeba pro Startovní a jak se přechodu do cloudu, které uživatelé. Ujistěte se, že odpovězte si na následující otázky:

* Budou uživatelé získávat přístup k aplikacím v místě?
* Budou uživatelé přistupovat k aplikacím v cloudu?
* Jak uživatelé obvykle přihlášení k jejich místní prostředí?
* Jak budou uživatelé přihlášení do cloudu?

> [!NOTE]
> Zajistěte, aby poznamenejte každou odpověď a pochopit důvody odpověď. [Stanovení požadavků na reakce na incidenty](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md) budou přenášeny po dostupných možností a profesionálové/nevýhody jednotlivých možností.  Po zodpovězení těchto otázek, které vyberete která možnost nejlépe vyhovuje vaší firmě potřebuje.
> 
> 

## <a name="next-steps"></a>Další postup
[Určení požadavků na synchronizaci adresáře](active-directory-hybrid-identity-design-considerations-directory-sync-requirements.md)

## <a name="see-also"></a>Další informace najdete v tématech
[Přehled aspektů návrhu](active-directory-hybrid-identity-design-considerations-overview.md)

