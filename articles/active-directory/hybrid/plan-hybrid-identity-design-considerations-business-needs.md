---
title: Požadavky na identitu pro návrh hybridní identity cloudu Azure | Microsoft Docs
description: Identifikujte obchodní potřeby společnosti, které vám povedou k definování požadavků pro návrh hybridní identity.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: de690978-84ef-41ad-9dfe-785722d343a1
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 074c203a0a5688855d4f7607a877b25ce6ee6ad7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89660571"
---
# <a name="determine-identity-requirements-for-your-hybrid-identity-solution"></a>Určení požadavků na identitu pro vaše řešení hybridní identity
Prvním krokem při návrhu řešení hybridní identity je určení požadavků pro obchodní organizaci, které budou využívat toto řešení.  Hybridní identita se spouští jako podpůrná role (podporuje všechna ostatní cloudová řešení tím, že poskytuje ověřování) a přejde k poskytování nových a zajímavých možností, které pro uživatele odemknou nové úlohy.  Tyto úlohy nebo služby, které chcete pro uživatele přijmout, budou určovat požadavky na návrh hybridní identity.  Tyto služby a úlohy potřebují využívat hybridní identitu místně i v cloudu.  

Abyste se seznámili s tím, co je teď požadavkem a co se v budoucnu plánuje společnost, musíte projít tyto klíčové aspekty firmy. Pokud nevidíte dlouhodobou strategii pro návrh hybridních identit, je pravděpodobné, že vaše řešení nebude škálovatelné podle toho, jak se budou obchodní potřeby růst a měnit. Následující diagram ukazuje příklad architektury hybridní identity a úloh, které jsou pro uživatele odemknuté. Toto je jenom příklad všech nových možností, které se dají odemknout a doručovat pomocí plné strategie hybridních identit. 

Některé součásti, které jsou součástí architektury hybridní identity architektury hybridní identity ![](./media/plan-hybrid-identity-design-considerations/hybrid-identity-architechture.png)

## <a name="determine-business-needs"></a>Určení obchodních potřeb
Každá společnost bude mít různé požadavky, i když jsou tyto společnosti součástí stejného odvětví, skutečné obchodní požadavky se mohou lišit. Můžete i nadále využívat osvědčené postupy z odvětví, ale v konečném případě se jedná o obchodní potřeby společnosti, které vám pomohou určit požadavky pro návrh hybridní identity. 

Nezapomeňte zodpovědět následující otázky, abyste identifikovali vaše obchodní potřeby:

* Má vaše společnost vyjímat provozní náklady?
* Hledají vaše společnost zabezpečení cloudových prostředků (aplikace SaaS, infrastruktura)?
* Vyhledává vaše společnost modernizovat?
  * Jsou vaši uživatelé větší mobilní a náročné na to, aby v DMZ vytvářeli výjimky, aby pro přístup k různým prostředkům mohl jiný typ provozu?
  * Má vaše společnost starší verze aplikací, které je potřeba publikovat u těchto moderních uživatelů, ale nemůžete je snadno přepsat?
  * Potřebuje vaše společnost plnit všechny tyto úlohy a přivést ji pod kontrolu ve stejnou dobu?
* Je vaše společnost, která se pokouší zabezpečit identity uživatelů, a snižuje riziko tím, že přináší nové nástroje, které využijí odbornosti odbornosti zabezpečení Azure v místním prostředí?
* Snaží se vaše společnost zbavit účty Dreaded "externí" v místním prostředí a přemístit je do cloudu, ve kterém už nepředstavuje neklidovou hrozbu v místním prostředí?

## <a name="analyze-on-premises-identity-infrastructure"></a>Analýza místní infrastruktury identit
Teď, když máte představu o podnikových požadavcích vaší společnosti, musíte vyhodnotit svou místní infrastrukturu identity. Toto hodnocení je důležité pro definování technických požadavků pro integraci aktuálního řešení identity do systému správy cloudových identit. Nezapomeňte odpovědět na následující otázky:

* Jaké řešení ověřování a autorizace vaše společnost používá místně? 
* Má vaše společnost nyní nějaké místní synchronizační služby?
* Používá vaše společnost poskytovatele identity třetích stran (IdP)?

Musíte být také vědomi cloudových služeb, které vaše společnost může mít. V rámci posouzení je důležité pochopit aktuální integraci s SaaS, IaaS nebo PaaS modely ve vašem prostředí. Během tohoto posouzení nezapomeňte odpovědět na tyto otázky:

* Má vaše společnost nějakou integraci s poskytovatelem cloudových služeb?
* Pokud ano, jaké služby se používají?
* Je tato integrace aktuálně v produkčním prostředí, nebo se jedná o pilotní nasazení?

> [!NOTE]
> Cloud Discovery analyzuje protokoly přenosů s využitím katalogu cloudových aplikací Microsoft Cloud App Security více než 16 000 cloudových aplikací, které jsou seřazené a vyhodnoceny na základě více než 70 rizikových faktorů, aby vám poskytovaly průběžné přehledy o používání cloudu, stínovém IT a rizikovém stínu, které do vaší organizace působí. Informace o tom, jak začít, najdete v tématu [nastavení Cloud Discovery](/cloud-app-security/set-up-cloud-discovery).
> 
> 

## <a name="evaluate-identity-integration-requirements"></a>Vyhodnotit požadavky na integraci identity
Dále je nutné vyhodnotit požadavky na integraci identity. Toto vyhodnocení je důležité pro definování technických požadavků na to, jak se budou uživatelé ověřovat, jak bude činnost vaší organizace Hledat v cloudu, jak bude organizace umožňovat autorizaci a co bude činnost koncového uživatele. Nezapomeňte odpovědět na následující otázky:

* Bude vaše organizace používat federaci, standardní ověřování nebo obojí?
* Je federace požadavek?  Z následujícího důvodu:
  * Jednotné přihlašování založené na protokolu Kerberos
  * Vaše společnost má místní aplikace (buď integrované, nebo třetí strany), které využívají funkce SAML nebo podobné federace.
  * MFA prostřednictvím čipových karet. RSA SecurID atd.
  * Pravidla přístupu klienta, která řeší otázky níže:
    1. Je možné blokovat veškerý externí přístup k Microsoft 365 na základě IP adresy klienta?
    2. Můžu blokovat veškerý externí přístup k Microsoft 365, s výjimkou Exchange ActiveSync?
    3. Můžu zablokovat veškerý externí přístup k Microsoft 365, s výjimkou aplikací založených na prohlížeči (OWA, SPO).
    4. Můžu zablokovat všem externím přístupům Microsoft 365 pro členy určených skupin AD.
* Otázky zabezpečení/auditování
* Již existující investice do federovaného ověřování
* Jaký název bude naše organizace používat pro naši doménu v cloudu?
* Má organizace vlastní doménu?
  1. Je doména veřejná a snadno ověřitelná přes DNS?
  2. Pokud ne, máte k dispozici veřejnou doménu, kterou je možné použít k registraci alternativního názvu UPN ve službě AD?
* Jsou identifikátory uživatelů konzistentní pro cloudové vyjádření? 
* Má organizace aplikace, které vyžadují integraci s Cloud Services?
* Má organizace více domén a bude používat standardní nebo federované ověřování?

## <a name="evaluate-applications-that-run-in-your-environment"></a>Vyhodnotit aplikace, které běží ve vašem prostředí
Teď, když máte představu o vaší místní i cloudové infrastruktuře, potřebujete vyhodnotit aplikace, které běží v těchto prostředích. Toto vyhodnocení je důležité pro definování technických požadavků pro integraci těchto aplikací do systému správy cloudových identit. Nezapomeňte odpovědět na následující otázky:

* Kde budou naše aplikace živé?
* Budou uživatelé přistupovat k místním aplikacím?  V cloudu? Nebo obojí?
* Existují nějaké plány, jak existující aplikační úlohy převzít a přesunout do cloudu?
* Existují plány pro vývoj nových aplikací, které se budou nacházet v místním prostředí nebo v cloudu, které budou používat cloudové ověřování?

## <a name="evaluate-user-requirements"></a>Zhodnotit požadavky uživatelů
Je také nutné vyhodnotit požadavky uživatele. Toto vyhodnocení je důležité pro definování kroků, které budou potřeba k tomu, aby se daly při přechodu do cloudu pomáhat uživatelům. Nezapomeňte odpovědět na následující otázky:

* Budou uživatelé přistupují k aplikacím v místním prostředí?
* Budou uživatelé přistupovat k aplikacím v cloudu?
* Jak se uživatelé obvykle přihlašují k místnímu prostředí?
* Jak se uživatelé budou přihlašovat ke cloudu?

> [!NOTE]
> Každou odpověď si poznamenejte a ujistěte se, že dobře chápete důvody, které vás k ní vedly. [Určení požadavků na reakci na incidenty](plan-hybrid-identity-design-considerations-incident-response-requirements.md) přejdou možnosti, které jsou k dispozici, a pro jednotlivé možnosti vyberte možnost odborníci/nevýhody.  Po zodpovězení těchto otázek si vyberete, která možnost nejlépe vyhovuje vašim obchodním potřebám.
> 
> 

## <a name="next-steps"></a>Další kroky
[Určení požadavků na synchronizaci adresářů](plan-hybrid-identity-design-considerations-directory-sync-requirements.md)

## <a name="see-also"></a>Viz také
[Přehled otázek návrhu](plan-hybrid-identity-design-considerations-overview.md)

