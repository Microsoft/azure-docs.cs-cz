---
title: Nejčastější dotazy týkající se sestav Azure Active Directory | Dokumentace Microsoftu
description: Nejčastější quesitons kolem sestav Azure Active Directory.
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
ms.component: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 5cbf0895274672c053158cf07acb344908b37831
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2018
ms.locfileid: "51623464"
---
# <a name="frequently-asked-questions-around-azure-active-directory-reports"></a>Nejčastější dotazy týkající se sestav Azure Active Directory

Tento článek obsahuje odpovědi na nejčastější dotazy ohledně služby Azure Active Directory (Azure AD), vytváření sestav. Další informace najdete v článku [Generování sestav ve službě Azure Active Directory](overview-reports.md). 

## <a name="getting-started"></a>Začínáme 

**Otázka: nemůžu používají https://graph.windows.net/&lt; název tenanta&gt;/reports/ koncový bod rozhraní API pro Azure AD o přijetí změn auditu a využití integrované aplikace zprávy na naše systémy pro generování sestav prostřednictvím kódu programu. Co by měl přepnout na?**

**O:** vyhledat [reference k rozhraní API](https://developer.microsoft.com/graph/) zobrazíte, jak můžete [pomocí rozhraní API pro přístup k sestavám aktivity](concept-reporting-api.md). Tento koncový bod má dvě sestavy (**auditu** a **přihlášení**) poskytující všechna data, které jste získali v původní koncový bod rozhraní API. Tento nový koncový bod má také sestavy přihlášení s licencí Azure AD Premium, který můžete použít k získání využití aplikace, využití zařízení a uživatele přihlašovací údaje.

--- 

**Otázka: nemůžu používají https://graph.windows.net/&lt; název tenanta&gt;/reports/ koncový bod rozhraní API do naše systémy pro generování sestav o přijetí změn zprávy o zabezpečení Azure AD (konkrétní typy detekce, třeba uniklé přihlašovací údaje nebo přihlášení z anonymních IP adres) prostřednictvím kódu programu. Co by měl přepnout na?**

**Odpověď:** můžete použít [události rizika Identity Protection API](../identity-protection/graph-get-started.md) detekcí zabezpečení přístupu prostřednictvím Microsoft Graphu. Tento nový formát poskytuje větší flexibilitu v jak můžete dotazovat data pomocí rozšířené filtrování, výběr pole a další a do jednoho typu pro jednodušší integraci do sady Siem a další nástroje pro shromažďování dat standardizuje rizikové události. Protože data jsou v jiném formátu, je nelze nahradit nový dotaz pro staré dotazy. Ale [používá nové rozhraní API Microsoft Graphu](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityriskevent), což je standard Microsoft pro tato rozhraní API jako O365 nebo Azure AD. Takže práce vyžaduje buď rozšířit vaše stávající investice MS Graphu nebo nápovědy začnete přechod na tuto novou standardní platformu.

--- 

**Otázka: Jak mohu získat licenci premium?**

**Odpověď:** naleznete v tématu [Začínáme se službou Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) upgradovat edici Azure Active Directory.

---

**Otázka: jak brzy by měl zobrazit data aktivity po získání licence premium?**

**Odpověď:** Pokud již máte data aktivity jako bezplatná licence, pak můžou zobrazit okamžitě. Pokud nemáte k dispozici žádná data, bude to trvat jeden nebo dva dny pro data se zobrazí v sestavách.

---

**Dotaz: lze zobrazit data poslední měsíc po získání licenci Azure AD premium?**

**Odpověď:** Pokud jste nedávno přešli na verzi Premium (včetně zkušební verze), zobrazí se data až na 7 dní zpočátku. Když se data hromadí, se zobrazí data za posledních 30 dnů.

---

**Otázka: musím být globálním správcem zobrazíte přihlášení aktivity na webu Azure portal nebo k získání dat prostřednictvím rozhraní API?**

**Odpověď:** Ne, dostanete také údaje o chybách prostřednictvím portálu nebo prostřednictvím rozhraní API služby jste-li **Čtenář zabezpečení** nebo **správce zabezpečení** pro příslušného tenanta. Samozřejmě **globální správci** bude také mít přístup k těmto datům.

---


## <a name="activity-logs"></a>Protokoly aktivit


**Otázka: co je doba uchovávání dat protokoly aktivity (auditu nebo přihlášení) na webu Azure Portal?** 

**Odpověď:** v následující tabulce jsou uvedeny období uchovávání dat pro protokoly aktivit. Další informace najdete v tématu [zásad uchovávání dat pro sestavy Azure AD](reference-reports-data-retention.md).

| Sestava                 | Azure AD Free | Azure AD Premium P1 | Azure AD Premium P2 |
| :--                    | :--           | :--                 | :--                 |
| Protokoly auditu             | 7 dní        | 30 dní             | 30 dní             |
| Přihlášení               | neuvedeno           | 30 dní             | 30 dní             |
| Použití Azure MFA        | 30 dní       | 30 dní             | 30 dní             |

--- 

**Otázka: jak dlouho trvá až po dokončení úkol data aktivit vidět?**

**Odpověď:** protokoly auditu mají latenci od 15 minut až jednu hodinu. Protokoly aktivit přihlašování může trvat od 15 minut až 2 hodin pro některé záznamy.

---

**Dotaz: lze získat informace o protokolu aktivit Office 365 na webu Azure portal?**

**Odpověď:** Přestože aktivit Office 365 a Azure AD aktivity protokoly sdílejí velké množství prostředků adresáře, pokud chcete, aby úplný přehled protokolů aktivit Office 365, by měl přejdete do centra pro správu Office 365 se získat informace protokolu aktivit Office 365.

---

**Otázka: které rozhraní API se dá použít k získání informací o protokoly aktivit Office 365?**

**Odpověď:** použití [rozhraní API pro správu Office 365](https://msdn.microsoft.com/office-365/office-365-managment-apis-overview) pro přístup k protokolům aktivit Office 365 prostřednictvím rozhraní API.

---

**Otázka: kolik záznamy můžete stáhnout z webu Azure portal?**

**Odpověď:** až 5000 záznamů si můžete stáhnout z webu Azure portal. Záznamy jsou seřazeny podle *nejnovější* ve výchozím nastavení, zobrazí se nejnovější 5000 záznamů.

---

## <a name="risky-sign-ins"></a>Riziková přihlášení

**Otázka: je riziková událost v Identity Protection, ale mi nezobrazují odpovídající přihlášení v sestavě přihlášení. Je to očekávání?**

**Odpověď:** Ano, Identity Protection vyhodnocuje riziko pro všechny toky ověřování, zda interaktivní nebo jako neinteraktivní. Však všechny přihlášení pouze sestava zobrazí pouze interaktivní přihlášení.

---

**Otázka: Jak zjistím, proč u přihlášení nebo uživatele byl příznakem rizikové na webu Azure Portal?**

**O:** Pokud máte **Azure AD Premium** předplatného, další informace o základních rizikových událostech tak, že vyberete uživatele v **uživatelé označení příznakem rizika** nebo tak, že vyberete záznam v  **Riziková přihlášení** sestavy. Pokud máte **Free** nebo **základní** předplatné a pak můžete zobrazit uživatele na rizika a rizikových přihlášení sestavy, ale nemůžete zobrazit základní informace o události rizika.

---

**Otázka: jak se počítají IP adresy v sestavě rizikových přihlášení a přihlášení?**

**Odpověď:** IP adresy vydávají takovým způsobem, že neexistuje žádná konečné připojení mezi IP adresy a kdy je počítač s touto adresou fyzicky umístěn. Mapování IP adres je složité další faktory, jako jsou mobilní poskytovatelů a vydání z Centrální fondy IP adres, často velmi daleko od skutečně použití klientského zařízení sítě VPN. V sestavách služby Azure AD, převod IP adres na fyzické umístění je aktuálně pokusí na základě trasování, klíče registru, zpětného vyhledávání a další informace. 

---

**Otázka: Co znamená riziková událost "Přihlášení s dalšími riziky zjistil" místo?**

**Odpověď:** získáte přehled o všech rizikových přihlášení ve vašem prostředí, "přihlásit se s dalšími riziky zjistil" slouží jako zástupný symbol pro přihlášení pro zjištění, které jsou výhradně pro předplatitele Azure AD Identity Protection.

---

## <a name="conditional-access"></a>Podmíněný přístup

**Otázka: co je nového v této funkce?**

**Odpověď:** zákazníci teď můžete řešit zásady podmíněného přístupu všechny sestavy přihlášení. Zákazníky můžete zkontrolovat stav podmíněného přístupu a informace o zásadách, které se použijí k přihlášení a výsledek pro jednotlivé zásady.

**Otázka: Jak mám začít?**

**Odpověď:** začít:
    * Přejděte na sestavu přihlášení [webu Azure portal](https://portal.azure.com). 
    * Klikněte na přihlášení, který chcete vyřešit.
    * Přejděte **podmíněného přístupu** kartu. Tady můžete zobrazit všechny zásady, které se to týká přihlášení a výsledek pro jednotlivé zásady. 
    
**Otázka: jaké jsou všechny možné hodnoty pro stav podmíněného přístupu?**

**Odpověď:** stav podmíněného přístupu může mít následující hodnoty:
    * **Nebyly použity**: to znamená, že se bez zásad podmíněného přístupu s uživatelem a aplikace v oboru. 
    * **Úspěch**: to znamená, že byl zásad podmíněného přístupu s uživatelem a aplikace v oboru a zásad podmíněného přístupu byly úspěšně splněny. 
    * **Selhání**: to znamená, že byl zásad podmíněného přístupu s uživatelem a aplikace v oboru a zásad podmíněného přístupu nebyly splněny. 
    
**Otázka: jaké jsou všechny možné hodnoty ve výsledku zásady podmíněného přístupu?**

**Odpověď:** zásady podmíněného přístupu může mít následující výsledky:
    * **Úspěch**: zásada byla úspěšně vyřešena.
    * **Selhání**: zásada nebyla splněná.
    * **Nebyly použity**: může to být způsobeno nesplňuje podmínky zásad.
    * **Není povoleno**: je to z důvodu zásad v zakázaném stavu. 
    
**Otázka: název zásady v sestavě všechna přihlášení neodpovídá názvu zásady v certifikační Autoritě. Proč?**

**Odpověď:** název zásady v sestavě všechna přihlášení podle názvu certifikační Autority zásad v době přihlášení. To může být konzistentní se název zásady v certifikační Autoritě, pokud jste aktualizovali název zásady později, tedy po přihlášení.

**Otázka: Moje přihlášení se zablokovala, protože zásady podmíněného přístupu, ale v sestavě aktivit přihlašování se zobrazí, že přihlášení bylo úspěšné. Proč?**

**Odpověď:** aktuálně přihlášení se v sestavě nemusí zobrazovat přesné výsledky pro Exchange ActiveSync scénáře při použití podmíněného přístupu. Můžou nastat případy, když výsledek přihlášení v sestavě se zobrazuje úspěšné přihlášení, ale přihlášení ve skutečnosti selhala z důvodu zásad podmíněného přístupu. 
