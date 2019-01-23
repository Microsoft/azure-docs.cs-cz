---
title: Požadavky na identitu pro hybridní cloudové identity návrhu Azure | Dokumentace Microsoftu
description: Identifikace obchodních potřeb vaší společnosti, které by vedly definovat požadavky na návrh hybridní identity.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: de690978-84ef-41ad-9dfe-785722d343a1
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/27/2018
ms.component: hybrid
ms.author: billmath
ms.custom: seohack1
ms.openlocfilehash: 9be8ddb6869a4872ff3eae9b589584f850c7dc96
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54463601"
---
# <a name="determine-identity-requirements-for-your-hybrid-identity-solution"></a>Určení požadavků na identity pro vaše řešení hybridní identity
Prvním krokem při navrhování řešení s hybridní identitou je určit požadavky organizace obchodní, který bude využívání tohoto řešení.  Hybridní identita Spustí podpůrnou roli (podporuje jiná řešení cloudových tím, že poskytuje ověřování) a přejde k poskytuje nové a zajímavé funkce, které odhalují nových úloh pro uživatele.  Požadavky na návrh hybridní identity, bude určovat tyto úlohy nebo služby, které chcete přijmout pro vaše uživatele.  Tyto služby a úlohy muset využít hybridní identity v místním prostředí i v cloudu.  

Musíte si projít klíčové aspekty podniku, aby co je nyní požadavek a co společnost plánuje do budoucna. Pokud nemáte dlouhodobou strategii pro návrhu hybridní identity, zda je pravděpodobné, že vaše řešení nebude škálovatelné podle mění a rostou potřeby firmy.   T má obrázek níže ukazuje příklad hybridní identity architektury a úlohy, které jsou odemykají pro uživatele. Toto je jenom pro příklad nové možnosti, které můžete odemknout a dodávají s strategii plné hybridní identity. 

Některé součásti, které jsou součástí architektury hybridní identity ![](./media/plan-hybrid-identity-design-considerations/hybrid-identity-architechture.png)

## <a name="determine-business-needs"></a>Určit, potřeb firmy
Každá společnost má jiné požadavky, i když jsou tyto společnosti součástí stejného odvětví, skutečné obchodní požadavky může lišit. Vždy můžete využít osvědčené postupy v odvětví, ale výsledku jsou to obchodní potřeby společnosti, které vedou definovat požadavky na návrh hybridní identity. 

Ujistěte se, že odpovědět na následující otázky k identifikaci vašich obchodních potřeb:

* Je vaší společnosti Pokud chcete vyjmout provozní náklady na IT?
* Je vaší společnosti Pokud chcete zabezpečit cloudové prostředky (aplikace SaaS, infrastruktury)?
* Je vaší společnosti chtějí modernizovat vaše IT?
  * Uživatelé jsou mobilní a náročné IT oddělení vytvářet výjimky do vaší hraniční síti, aby jiný typ provozu pro přístup k různým prostředkům?
  * Má vaše společnost nějaké starší verze aplikací, které potřebné k publikování pro tyto uživatele moderní, ale nejsou snadno přepsat?
  * Potřebuje vaše společnost provádět tyto úlohy a přiřaďte ho pod ovládací prvek ve stejnou dobu?
* Je vaší společnosti Pokud chcete zabezpečit identity uživatelů a snižuje riziko přináší nové nástroje, které využívají odborných znalostí společnosti Microsoft Azure security odborných znalostí v místním?
* Vaše společnost pokouší vyřadit dreaded "externí" účty v místním prostředí a přesunout do cloudu, kde už nejsou neaktivní před internetovými útoky v místním prostředí?

## <a name="analyze-on-premises-identity-infrastructure"></a>Analýza v místní infrastruktuře identity
Teď, když máte představu o obchodních požadavků vaší společnosti, budete muset vyhodnotit vaší místní infrastruktury identit. Toto testování je důležité pro technické požadavky pro integraci vašeho aktuálního řešení identity do systému správy identit v cloudu. Ujistěte se, že odpovědět na následující otázky:

* Jaká řešení ověřování a autorizaci provádí vaší společnosti používají místně? 
* Má vaše společnost aktuálně všechny místní služby synchronizace?
* Používá vaše společnost jakékoli třetí strany zprostředkovatelů Identity (IdP)?

Budete potřebovat znát cloudových služeb, které vaše společnost může mít. Probíhá posouzení abyste pochopili aktuální integraci s SaaS a PaaS a IaaS modely ve vašem prostředí je velmi důležité. Ujistěte se, že během tohoto vyhodnocení odpovědět na následující otázky:

* Má vaše společnost nějaké žádnou integraci s poskytovatele cloudové služby?
* Pokud ano, služby, které se používají?
* Tato integrace je aktuálně v produkčním prostředí nebo je pilotní nasazení?

> [!NOTE]
> Cloud Discovery analyzuje protokoly přenosů oproti katalogu cloudových aplikací Microsoft Cloud App Security z více než 16 000 cloudových aplikací s řazením a skóre na základě více než 70 rizikových faktorů, poskytovat přehled o cloudových použití, stínového IT a riziko Představuje stínové IT ve vaší organizaci. Získat Začínáme viz [nastavení Cloud Discovery](/cloud-app-security/set-up-cloud-discovery).
> 
> 

## <a name="evaluate-identity-integration-requirements"></a>Vyhodnotit požadavky na integraci identity
V dalším kroku je nutné vyhodnotit požadavky na integraci identity. Toto testování je důležité určit, jak se uživatelé ověřují, vzhled přítomnost organizace v cloudu, jak organizace vám umožní autorizace a co se děje činnost koncového uživatele bude technické požadavky. Ujistěte se, že odpovědět na následující otázky:

* Bude vaše organizace používat federace, standardní ověřování nebo obojí?
* Vyžaduje se federace?  Z důvodu následující:
  * Jednotné přihlašování založené na protokolu Kerberos
  * Vaše společnost má místní aplikace (buď vytvořené interně nebo 3. stran), které používá SAML nebo podobné možnosti federace.
  * Vícefaktorové ověřování pomocí čipové karty. RSA SecurID atd.
  * Pravidla přístupu klienta, které řeší tyto otázky:
    1. Můžete blokovat všechny externího přístupu k Office 365 na základě IP adresy klienta?
    2. Můžete blokovat všechny externího přístupu k Office 365, s výjimkou Exchange ActiveSync?
    3. Můžete blokovat všechny externího přístupu k Office 365, s výjimkou aplikace založené na prohlížeči (aplikace OWA, SPO)
    4. Může blokovat všechny externí přístup k Office 365 pro členem určené skupiny AD
* Aspekty zabezpečení a auditování
* Již existující investici do federovaného ověřování
* Zadejte název naší organizace použije pro naše domény v cloudu?
* Má organizace vlastní doménu?
  1. Tuto doménu je veřejná a snadno ověřit pomocí DNS?
  2. Pokud není, pak máte, který slouží k registraci ve službě AD alternativní UPN veřejné domény?
* Identifikátory uživatele jsou konzistentní vzhledem k aplikacím pro reprezentaci cloudu? 
* Má organizace aplikace, které vyžadují integraci s cloudovými službami?
* Organizace mají několik domén a použije všechny standardní nebo federovaného ověřování?

## <a name="evaluate-applications-that-run-in-your-environment"></a>Hodnocení aplikace, které běží ve vašem prostředí
Teď, když máte představu o místní a cloudovou infrastrukturu, je nutné vyhodnotit spouštěné v těchto prostředích. Toto testování je důležité definovat technické požadavky, můžete integrovat tyto aplikace do systému správy identit v cloudu. Ujistěte se, že odpovědět na následující otázky:

* Pokud naše aplikace pracovat?
* Budou uživatelé přistupovat k místním aplikacím?  V cloudu? Nebo obojí?
* Existují plány využít stávající úlohy aplikace a přesunout do cloudu?
* Existují plány pro vývoj nové aplikace, které se nacházejí na místní nebo v cloudu, který bude používat cloudové ověřování?

## <a name="evaluate-user-requirements"></a>Vyhodnocení požadavků na uživatele
Také je nutné vyhodnotit požadavky uživatelů. Toto testování je důležité určit kroky, které budou potřebné pro zavádění a pomáhám uživatelů podle jejich přechodu do cloudu. Ujistěte se, že odpovědět na následující otázky:

* Budou uživatelé přistupovat k aplikacím v místním?
* Budou uživatelé přistupovat k aplikacím v cloudu?
* Jak uživatelé obvykle Přihlaste se k jejich v místním prostředí?
* Jak se uživatelé přihlásí do cloudu?

> [!NOTE]
> Ujistěte se, že každá odpověď a pochopení odůvodnění odpověď. [Určení požadavků na reakce na incidenty](plan-hybrid-identity-design-considerations-incident-response-requirements.md) dostupné možnosti a v oblasti IT a nevýhody jednotlivých možností.  Po zodpovězení těchto otázek, které vyberete která možnost nejlépe vyhovuje stylu vaší firmě potřebuje.
> 
> 

## <a name="next-steps"></a>Další postup
[Určete požadavky na synchronizaci adresáře](plan-hybrid-identity-design-considerations-directory-sync-requirements.md)

## <a name="see-also"></a>Další informace najdete v tématech
[Přehled aspektů návrhu](plan-hybrid-identity-design-considerations-overview.md)

