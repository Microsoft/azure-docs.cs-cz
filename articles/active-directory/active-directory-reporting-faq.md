---
title: Azure Active Directory, vytváření sestav – nejčastější dotazy | Microsoft Docs
description: Nejčastější dotazy týkající se vytváření sestav Azure Active Directory.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
ms.assetid: 534da0b1-7858-4167-9986-7a62fbd10439
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: compliance-reports
ms.date: 05/10/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 8d627abfe7b686eeeb5a65c4515e184f4ce62f4e
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "36335053"
---
# <a name="azure-active-directory-reporting-faq"></a>Nejčastější dotazy týkající se vytváření sestav Azure Active Directory.

Tento článek obsahuje odpovědi na nejčastější dotazy k Azure Active Directory (Azure AD), vytváření sestav. Další informace najdete v článku [Generování sestav ve službě Azure Active Directory](active-directory-reporting-azure-portal.md). 

**Otázka: je použito https://graph.windows.net/&lt; název klienta&gt;/reports/ koncový bod rozhraní API pro audit vyžádání Azure AD a využití integrované aplikace sestavy do našich reporting systémů prostřednictvím kódu programu. Co je měli přepnout do?**

**Odpověď:** vyhledat [referenční dokumentace rozhraní API](https://developer.microsoft.com/graph/) zobrazíte použití nových rozhraní API pro přístup k [sestavy aktivit](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal). Tento koncový bod má dva podřízené (auditu a přihlášení), které poskytují všechna data, která jste získali v původním koncový bod rozhraní API. Tento nový koncový bod má také sestavy přihlášení s licencí Azure AD Premium, který můžete použít k získání využití aplikací, využití zařízení a přihlašovací údaje uživatele.


--- 

**Otázka: je použito https://graph.windows.net/&lt; název klienta&gt;/reports/ koncový bod rozhraní API do našich reporting systémů načítat sestavy zabezpečení Azure AD (specifické typy detekce, třeba uniklé přihlašovací údaje nebo přihlášení z anonymních IP adres) prostřednictvím kódu programu. Co je měli přepnout do?**

**Odpověď:** můžete použít [Identity Protection rizikových událostech rozhraní API](active-directory-identityprotection-graph-getting-started.md) k zjištění zabezpečení přístup prostřednictvím Microsoft Graph. Tento nový formát, poskytují větší flexibilitu v tom, jak můžete dát dotaz na data s pokročilé filtrování, výběr pole a další a standardizuje rizikových událostí do jednoho typu pro snazší integrace do systémů Siem a dalších nástrojů pro shromažďování dat. Protože data jsou v jiném formátu, nelze je nový dotaz nahradit své staré dotazy. Ale [nového rozhraní API používá Microsoft Graph](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityriskevent), což je standard Microsoft pro takové rozhraní API jako O365 nebo Azure AD. Takže práce potřebné, můžete buď rozšířit vaše investice do aktuální MS Graph nebo nápovědy začnete přecházíte na tuto novou standardní platformu.

--- 

**Otázka: co je uchovávání dat protokoly aktivity (auditu a přihlášení) na portálu Azure?** 

**Odpověď:** najdete v části [je jak dlouho shromážděná data uložená?](active-directory-reporting-retention.md#q-for-how-long-is-the-collected-data-stored) pro odpověď na tuto otázku.

--- 

**Otázka: jak dlouho trvá až po dokončení Moje úloh data aktivit uvidí?**

**Odpověď:** protokoly auditu aktivity, jejichž latence od 15 minut až hodinu. Protokoly přihlašovací aktivity může trvat od 15 minut až 2 hodiny pro některé záznamy.

---

**Otázka: Potřebuji jako globální správce najdete v části přihlášení aktivity portálu Azure a získat data prostřednictvím rozhraní API?**

**Odpověď:** Ne. Musí být **zabezpečení čtečky**, **správce zabezpečení**, nebo **globálního správce** na získat data na portálu Azure nebo prostřednictvím rozhraní API pro vytváření sestav.

---

**Otázka: je možné získat informace o protokolu činnosti Office 365 prostřednictvím portálu Azure?**

**Odpověď:** i když aktivita Office 365 a Azure AD aktivity protokoly sdílení velké množství prostředků adresáře, pokud chcete, aby ucelený pohled na protokoly aktivity Office 365, by měl přejdete do do centra pro správu Office 365 se získat informace o protokolu Office 365 aktivity.

---


**Otázka: které rozhraní API se používá k načtení informací o protokoly aktivity Office 365?**

**Odpověď:** používat pro přístup k rozhraní API pro správu Office 365 [Office 365 aktivity protokoly prostřednictvím rozhraní API](https://msdn.microsoft.com/office-365/office-365-managment-apis-overview).

---

**Otázka: počet záznamů, můžete stáhnout z portálu Azure?**

**Odpověď:** až 5000 záznamy si můžete stáhnout z portálu Azure. Záznamy jsou seřazené podle *nejnovější* a ve výchozím nastavení, můžete získat nejnovější 5000 záznamy.

---

**Otázka: počet záznamů, můžete dotazovat pomocí aktivity rozhraní API?**

**Odpověď:** můžete dát dotaz na záznamy až 1 milion (Pokud nechcete použít operátor top, která seřadí záznam většina poslední). Pokud používáte operátor "top", se můžete dotazovat až 500 kB záznamy. Můžete najít ukázkové dotazy týkající se používání rozhraní API [zde](active-directory-reporting-api-getting-started.md).

---

**Otázka: jak lze získat licenci premium?**

**Odpověď:** najdete v části [Začínáme s Azure Active Directory Premium](fundamentals/active-directory-get-started-premium.md) pro odpověď na tuto otázku.

---

**Otázka: jak brzy by měl zobrazit data aktivity po získání licence premium?**

**Odpověď:** Pokud již máte data aktivity, jako volné licenci, pak můžete zobrazit stejná data. Pokud nemáte k dispozici žádná data, pak bude trvat jeden nebo dva dny.

---

**Otázka: Po získání licenci Azure AD premium zobrazit data poslední měsíc?**

**Odpověď:** Pokud Přepnuli jste nedávno na verzi Premium (včetně zkušební verzi), zobrazí se data až do 7 dnů původně. Když se data hromadí, zobrazí se až 30 dnů.

---

**Otázka: je riziko událostí v ochrany identit, ale nejsou zobrazeny odpovídající přihlášení v všechny přihlášení. Je to očekávané?**

**Odpověď:** Ano, Identity Protection vyhodnotí riziko pro všechny toky ověřování, zda interaktivní nebo neinteraktivní. Ale všechny přihlášení pouze sestava zobrazí jenom interaktivní přihlášení.

---

**Otázka: jak můžete stáhnout "Uživatelé označení příznakem rizik" sestav na portálu Azure?**

**Odpověď:** možnost stažení *uživatelé označení příznakem rizik* sestavy přidá brzy.

---

**Otázka: Jak poznám, proč přihlášení, nebo pro uživatele byla příznakem rizikové na portálu Azure?**

**Odpověď:** Premium edition zákazníkům další informace o základní rizikových událostech kliknutím na uživatele v "Uživatelé označení příznakem rizik" nebo kliknutím na "rizikové přihlášení". Zákazníci volné a Základní edice získat zobrazíte rizikové uživatele a přihlášení bez základní informace o události riziko.

---

**Otázka: jak jsou vypočítávány IP adresy v sestavě rizikové přihlášení a přihlášení?**

**Odpověď:** IP adresy vydávají tak, že není spolehlivý připojení mezi IP adresu a kde se fyzicky nacházejí počítači s touto adresou. To ztěžuje faktorech například mobilní poskytovatelů a vydání z Centrální fondy IP adres velmi často daleko od skutečně použití klientské zařízení sítě VPN. Z výše uvedených převodu IP adresu na fyzické umístění je nejlepší úsilí na základě trasování, data registru, zpětné vyhledání a další informace. 

---

**Otázka: Co znamená riziko události "Přihlášení s další riziko zjistil" označují?**

**Odpověď:** získáte přehled o všech rizikové přihlášení ve vašem prostředí, "přihlásit se s další riziko zjistil" funguje jako zástupný symbol pro přihlášení pro zjištění, které jsou výhradní předplatitelům služby Azure AD Identity Protection.

---
