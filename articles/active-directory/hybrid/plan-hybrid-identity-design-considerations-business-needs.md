---
title: Požadavky na identitu pro návrh hybridní chod identity cloudu Azure | Dokumenty společnosti Microsoft
description: Identifikujte obchodní potřeby společnosti, které vás povedou k definování požadavků na návrh hybridní identity.
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
ms.openlocfilehash: 9ecc90e13f49c231d8d3ab0cff1de91443b80f21
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "65950893"
---
# <a name="determine-identity-requirements-for-your-hybrid-identity-solution"></a>Určení požadavků na identitu pro řešení hybridní identity
Prvním krokem při navrhování řešení hybridní identity je určení požadavků pro obchodní organizaci, která bude využívat toto řešení.  Hybridní identita začíná jako podpůrná role (podporuje všechna ostatní cloudová řešení poskytováním ověřování) a dále poskytuje nové a zajímavé funkce, které odemknou nové úlohy pro uživatele.  Tyto úlohy nebo služby, které chcete přijmout pro uživatele bude diktovat požadavky na návrh hybridní identity.  Tyto služby a úlohy je potřeba využít hybridní identity v místním prostředí i v cloudu.  

Musíte projít tyto klíčové aspekty podnikání pochopit, co je požadavek nyní a co společnost plánuje do budoucna. Pokud nemáte viditelnost dlouhodobé strategie pro návrh hybridní identity, je pravděpodobné, že vaše řešení nebude škálovatelné, protože obchodní potřeby rostou a mění se. Následující diagram ukazuje příklad architektury hybridní identity a úlohy, které jsou odemknuté pro uživatele. To je jen příklad všech nových možností, které lze odemknout a dodat s pevnou hybridní identitou strategie. 

Některé součásti, které jsou součástí ![hybridní architektury identity hybridní identity architektura identity](./media/plan-hybrid-identity-design-considerations/hybrid-identity-architechture.png)

## <a name="determine-business-needs"></a>Určení obchodních potřeb
Každá společnost bude mít různé požadavky, i když jsou tyto společnosti součástí stejného odvětví, skutečné obchodní požadavky se mohou lišit. Stále můžete využít osvědčené postupy z odvětví, ale nakonec je to obchodní potřeby společnosti, které vás povedou k definování požadavků na návrh hybridní identity. 

Ujistěte se, že jste odpověděli na následující otázky, abyste zjistili vaše obchodní potřeby:

* Chce vaše společnost snížit provozní náklady na IT?
* Hledá vaše společnost zabezpečení cloudových prostředků (aplikace SaaS, infrastruktura)?
* Chce vaše společnost modernizovat vaše IT?
  * Jsou vaši uživatelé mobilnější a požadují, aby IT vytvořilo výjimky do vašeho DMZ, aby umožnily přístup k různým zdrojům různého typu provozu?
  * Má vaše společnost starší aplikace, které je třeba publikovat pro tyto moderní uživatele, ale není snadné je přepsat?
  * Musí vaše společnost splnit všechny tyto úkoly a zároveň je dostat pod kontrolu?
* Snaží se vaše společnost zabezpečit identitu uživatelů a snížit riziko tím, že přinese nové nástroje, které využívají odborné znalosti Microsoftu v oblasti zabezpečení Azure v místním prostředí?
* Snaží se vaše společnost zbavit obávaných "externích" účtů v místním prostředí a přesunout je do cloudu, kde už nejsou spící hrozbou ve vašem místním prostředí?

## <a name="analyze-on-premises-identity-infrastructure"></a>Analýza místní infrastruktury identit
Teď, když máte představu o vašich obchodních požadavcích, musíte vyhodnotit místní infrastrukturu identit. Toto hodnocení je důležité pro definování technických požadavků na integraci aktuálního řešení identity do systému správy cloudových identit. Ujistěte se, že jste odpověděli na následující otázky:

* Jaké řešení ověřování a autorizace používá vaše společnost místně? 
* Má vaše společnost aktuálně nějaké místní synchronizační služby?
* Používá vaše společnost nějaké poskytovatele identit (IdP) třetích stran?

Musíte si také být vědomi cloudových služeb, které vaše společnost může mít. Provedení hodnocení pochopit aktuální integraci s Modely SaaS, IaaS nebo PaaS ve vašem prostředí je velmi důležité. Během tohoto hodnocení nezapomeňte odpovědět na následující otázky:

* Má vaše společnost nějakou integraci s poskytovatelem cloudových služeb?
* Pokud ano, které služby jsou používány?
* Je tato integrace v současné době ve výrobě, nebo je to pilot?

> [!NOTE]
> Cloud Discovery analyzuje vaše záznamy o provozu podle katalogu cloudových aplikací microsoft cloudapp security více než 16 000 cloudových aplikací, které jsou hodnoceny a hodnoceny na základě více než 70 rizikových faktorů, aby vám poskytly průběžný přehled o používání cloudu, stínovém IT a riziku, které stínové IT představuje ve vaší organizaci. Další informace najdete v [tématu Nastavení zjišťování cloudu](/cloud-app-security/set-up-cloud-discovery).
> 
> 

## <a name="evaluate-identity-integration-requirements"></a>Vyhodnocení požadavků na integraci identity
Dále je třeba vyhodnotit požadavky na integraci identity. Toto hodnocení je důležité definovat technické požadavky pro ověřování uživatelů, jak bude vypadat přítomnost organizace v cloudu, jak organizace umožní autorizaci a jaké bude uživatelské prostředí. Ujistěte se, že jste odpověděli na následující otázky:

* Bude vaše organizace používat federaci, standardní ověřování nebo obojí?
* Je federace požadavek?  Z následujících důvodu:
  * Jednotné přihlašování založené na protokolu Kerberos
  * Vaše společnost má místní aplikace (buď integrované interní nebo třetí strany), které používají saml nebo podobné federační funkce.
  * Vícefaktorové finanční karty prostřednictvím čipových karet. RSA SecurID, atd.
  * Pravidla přístupu klienta, která řeší následující otázky:
    1. Můžu zablokovat veškerý externí přístup k Office 365 na základě IP adresy klienta?
    2. Můžu blokovat veškerý externí přístup k Office 365 kromě Exchange ActiveSync?
    3. Můžu zablokovat veškerý externí přístup k Office 365, s výjimkou aplikací založených na prohlížeči (OWA, SPO)
    4. Můžu zablokovat veškerý externí přístup k Office 365 pro členy určených skupin AD
* Obavy týkající se bezpečnosti/auditu
* Již existující investice do federovaného ověřování
* Jaký název bude naše organizace používat pro naši doménu v cloudu?
* Má organizace vlastní doménu?
  1. Je tato doména veřejná a snadno ověřitelná prostřednictvím SLUŽBY DNS?
  2. Pokud tomu tak není, máte veřejnou doménu, kterou lze použít k registraci alternativního hlavního názvu domény ve službách AD?
* Jsou identifikátory uživatelů konzistentní pro reprezentaci cloudu? 
* Má organizace aplikace, které vyžadují integraci s cloudovými službami?
* Má organizace více domén a budou všechny používat standardní nebo federované ověřování?

## <a name="evaluate-applications-that-run-in-your-environment"></a>Vyhodnocení aplikací spuštěné ve vašem prostředí
Teď, když máte představu o vaší místní a cloudové infrastruktuře, musíte vyhodnotit aplikace, které běží v těchto prostředích. Toto hodnocení je důležité definovat technické požadavky na integraci těchto aplikací do systému správy cloudových identit. Ujistěte se, že jste odpověděli na následující otázky:

* Kde budou naše přihlášky žít?
* Budou uživatelé přistupovat k místním aplikacím?  V cloudu? Nebo obojí?
* Plánujete převzít existující úlohy aplikace a přesunout je do cloudu?
* Existují plány na vývoj nových aplikací, které budou umístěny místně nebo v cloudu, který bude používat cloudové ověřování?

## <a name="evaluate-user-requirements"></a>Vyhodnocení požadavků uživatelů
Musíte také vyhodnotit požadavky uživatelů. Toto hodnocení je důležité definovat kroky, které budou potřebné pro on-boarding a pomoc uživatelům při přechodu do cloudu. Ujistěte se, že jste odpověděli na následující otázky:

* Budou uživatelé přistupovat k místním aplikacím?
* Budou uživatelé přistupovat k aplikacím v cloudu?
* Jak se uživatelé obvykle přihlašují do svého místního prostředí?
* Jak se uživatelé přihlásí do cloudu?

> [!NOTE]
> Každou odpověď si poznamenejte a ujistěte se, že dobře chápete důvody, které vás k ní vedly. [Určit požadavky na reakci na incidenty](plan-hybrid-identity-design-considerations-incident-response-requirements.md) půjde přes možnosti k dispozici a klady / zápory každé možnosti.  Tím, že odpověděl na tyto otázky, budete vybírat, která možnost nejlépe vyhovuje vašim obchodním potřebám.
> 
> 

## <a name="next-steps"></a>Další kroky
[Určení požadavků na synchronizaci adresářů](plan-hybrid-identity-design-considerations-directory-sync-requirements.md)

## <a name="see-also"></a>Viz také
[Přehled aspekty návrhu](plan-hybrid-identity-design-considerations-overview.md)

