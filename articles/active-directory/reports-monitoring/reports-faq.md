---
title: Nejčastější dotazy týkající se sestav Azure Active Directory | Dokumentace Microsoftu
description: Nejčastější quesitons kolem sestav Azure Active Directory.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
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
ms.openlocfilehash: d6062720e3b747966c60426a5db76e6dd18109a7
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54813012"
---
# <a name="frequently-asked-questions-around-azure-active-directory-reports"></a>Nejčastější dotazy týkající se sestav Azure Active Directory

Tento článek obsahuje odpovědi na nejčastější dotazy ohledně služby Azure Active Directory (Azure AD), vytváření sestav. Další informace najdete v článku [Generování sestav ve službě Azure Active Directory](overview-reports.md). 

## <a name="getting-started"></a>Začínáme 

**Otázka: Aktuálně používám https://graph.windows.net/&lt; název tenanta&gt;/reports/ koncový bod rozhraní API pro Azure AD o přijetí změn auditu a využití integrované aplikace zprávy na naše systémy pro generování sestav prostřednictvím kódu programu. Co by měl přepnout na?**

**ODPOVĚĎ:** Vyhledat [reference k rozhraní API](https://developer.microsoft.com/graph/) zobrazíte, jak můžete [pomocí rozhraní API pro přístup k sestavám aktivity](concept-reporting-api.md). Tento koncový bod má dvě sestavy (**auditu** a **přihlášení**) poskytující všechna data, které jste získali v původní koncový bod rozhraní API. Tento nový koncový bod má také sestavy přihlášení s licencí Azure AD Premium, který můžete použít k získání využití aplikace, využití zařízení a uživatele přihlašovací údaje.

--- 

**Otázka: Aktuálně používám https://graph.windows.net/&lt; název tenanta&gt;/reports/ koncový bod rozhraní API do naše systémy pro generování sestav o přijetí změn zprávy o zabezpečení Azure AD (konkrétní typy detekce, třeba uniklé přihlašovací údaje nebo přihlášení z anonymních IP adres) prostřednictvím kódu programu. Co by měl přepnout na?**

**ODPOVĚĎ:** Můžete použít [události rizika Identity Protection API](../identity-protection/graph-get-started.md) detekcí zabezpečení přístupu prostřednictvím Microsoft Graphu. Tento nový formát poskytuje větší flexibilitu v jak můžete dotazovat data pomocí rozšířené filtrování, výběr pole a další a do jednoho typu pro jednodušší integraci do sady Siem a další nástroje pro shromažďování dat standardizuje rizikové události. Protože data jsou v jiném formátu, je nelze nahradit nový dotaz pro staré dotazy. Ale [používá nové rozhraní API Microsoft Graphu](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityriskevent), což je standard Microsoft pro tato rozhraní API jako O365 nebo Azure AD. Takže práce vyžaduje buď rozšířit vaše stávající investice MS Graphu nebo nápovědy začnete přechod na tuto novou standardní platformu.

--- 

**Otázka: Jak získám licenci úrovně premium?**

**ODPOVĚĎ:** Zobrazit [Začínáme se službou Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) upgradovat edici Azure Active Directory.

---

**Otázka: Jak rychle zobrazit data aktivity po získání licence premium?**

**ODPOVĚĎ:** Pokud již máte data aktivity jako bezplatná licence, pak můžou zobrazit okamžitě. Pokud nemáte k dispozici žádná data, bude to trvat jeden nebo dva dny pro data se zobrazí v sestavách.

---

**Otázka: Můžete zobrazit data poslední měsíc po získání licenci Azure AD premium?**

**ODPOVĚĎ:** Pokud jste nedávno přešli na verzi Premium (včetně zkušební verze), uvidíte data až na 7 dní původně. Když se data hromadí, se zobrazí data za posledních 30 dnů.

---

**Otázka: Je nutné být globálním správcem zobrazíte přihlášení aktivity na webu Azure portal nebo k získání dat prostřednictvím rozhraní API?**

**ODPOVĚĎ:** Ne, dostanete také údaje o chybách prostřednictvím portálu nebo prostřednictvím rozhraní API služby jste-li **Čtenář zabezpečení** nebo **správce zabezpečení** pro příslušného tenanta. Samozřejmě **globální správci** bude také mít přístup k těmto datům.

---


## <a name="activity-logs"></a>Protokoly aktivit


**Otázka: Co je doba uchovávání dat protokoly aktivity (auditu nebo přihlášení) na webu Azure Portal?** 

**ODPOVĚĎ:** Následující tabulka uvádí dobu uchování dat protokolů aktivit. Další informace najdete v tématu [zásad uchovávání dat pro sestavy Azure AD](reference-reports-data-retention.md).

| Sestava                 | Azure AD Free | Azure AD Premium P1 | Azure AD Premium P2 |
| :--                    | :--           | :--                 | :--                 |
| Protokoly auditu             | 7 dní        | 30 dní             | 30 dní             |
| Přihlášení               | neuvedeno           | 30 dní             | 30 dní             |
| Použití Azure MFA        | 30 dní       | 30 dní             | 30 dní             |

--- 

**Otázka: Jak dlouho trvá až po dokončení úkol data aktivit vidět?**

**ODPOVĚĎ:** Protokoly auditu mají latenci od 15 minut až jednu hodinu. Protokoly aktivit přihlašování může trvat od 15 minut až 2 hodin pro některé záznamy.

---

**Otázka: Můžete získat informace o protokolu aktivit Office 365 na webu Azure portal?**

**ODPOVĚĎ:** I když protokoly aktivit Office 365 a Azure AD sdílejí velké množství prostředků adresáře, pokud chcete získat kompletní přehled protokolů aktivit Office 365, měli byste pro získání těchto informací přejít do centra pro správu Office 365.

---

**Otázka: Které rozhraní API se dá použít k získání informací o protokoly aktivit Office 365?**

**ODPOVĚĎ:** Použití [rozhraní API pro správu Office 365](https://docs.microsoft.com/office/office-365-management-api/office-365-management-apis-overview) pro přístup k protokolům aktivit Office 365 prostřednictvím rozhraní API.

---

**Otázka: Počet záznamů, můžete stáhnout z webu Azure portal?**

**ODPOVĚĎ:** Až 5000 záznamů si můžete stáhnout z webu Azure portal. Záznamy jsou seřazeny podle *nejnovější* ve výchozím nastavení, zobrazí se nejnovější 5000 záznamů.

---

## <a name="risky-sign-ins"></a>Riziková přihlášení

**Otázka: Ve službě Identity Protection je rizikovou událost, ale mi nezobrazují odpovídající přihlášení v sestavě přihlášení. Je to očekávání?**

**ODPOVĚĎ:** Ano, Identity Protection vyhodnocuje riziko pro všechny toky ověřování, zda interaktivní nebo jako neinteraktivní. Však všechny přihlášení pouze sestava zobrazí pouze interaktivní přihlášení.

---

**Otázka: Jak můžu zjistit, proč u přihlášení nebo uživatele byl příznakem rizikové na webu Azure Portal?**

**ODPOVĚĎ:** Pokud máte **Azure AD Premium** předplatného, další informace o základních rizikových událostech tak, že vyberete uživatele v **uživatelé označení příznakem rizika** nebo tak, že vyberete záznam v **rizikové přihlášení** sestavy. Pokud máte **Free** nebo **základní** předplatné a pak můžete zobrazit uživatele na rizika a rizikových přihlášení sestavy, ale nemůžete zobrazit základní informace o události rizika.

---

**Otázka: Jak se počítají IP adresy v sestavě rizikových přihlášení a přihlášení?**

**ODPOVĚĎ:** IP adresy se vydávají takovým způsobem, že neexistuje žádná konečné připojení mezi IP adresy a kdy je počítač s touto adresou fyzicky umístěn. Mapování IP adres je složité další faktory, jako jsou mobilní poskytovatelů a vydání z Centrální fondy IP adres, často velmi daleko od skutečně použití klientského zařízení sítě VPN. V sestavách služby Azure AD, převod IP adres na fyzické umístění je aktuálně pokusí na základě trasování, klíče registru, zpětného vyhledávání a další informace. 

---

**Otázka: Co dělá riziková událost "Přihlášení s dalšími riziky zjistil" místo?**

**ODPOVĚĎ:** Abyste získali přehled o rizikových přihlášení ve vašem prostředí, funkce "Přihlášení s dalšími riziky zjistil" jako zástupný symbol pro přihlášení pro zjištění, které jsou výhradně pro předplatitele Azure AD Identity Protection.

---

## <a name="conditional-access"></a>Podmíněný přístup

**Otázka: Co je nového v této funkce?**

**ODPOVĚĎ:** Zákazníci teď můžete řešit zásady podmíněného přístupu všechny sestavy přihlášení. Zákazníky můžete zkontrolovat stav podmíněného přístupu a informace o zásadách, které se použijí k přihlášení a výsledek pro jednotlivé zásady.

**Otázka: Jak mám začít?**

**ODPOVĚĎ:** Jak začít:
    * Přejděte na sestavu přihlášení [webu Azure portal](https://portal.azure.com). 
    * Klikněte na přihlášení, který chcete vyřešit.
    * Přejděte **podmíněného přístupu** kartu. Tady můžete zobrazit všechny zásady, které se to týká přihlášení a výsledek pro jednotlivé zásady. 
    
**Otázka: Jaké jsou všechny možné hodnoty pro stav podmíněného přístupu?**

**ODPOVĚĎ:** Stav podmíněného přístupu může mít následující hodnoty:
    * **Nebyly použity**: To znamená, že se bez zásad podmíněného přístupu s uživatelem a aplikace v oboru. 
    * **Úspěch**: To znamená, že byl zásad podmíněného přístupu s uživatelem a aplikace v oboru a zásad podmíněného přístupu byly úspěšně splněny. 
    * **Selhání**: To znamená, že došlo zásad podmíněného přístupu s uživatelem a aplikace v oboru a zásad podmíněného přístupu nebyly splněny. 
    
**Otázka: Jaké jsou všechny možné hodnoty ve výsledku zásady podmíněného přístupu?**

**ODPOVĚĎ:** Zásady podmíněného přístupu může mít následující výsledky:
    * **Úspěch**: Zásady byla úspěšně vyřešena.
    * **Selhání**: Zásada nebyla splněná.
    * **Nebyly použity**: To může být, protože nesplňuje podmínky zásad.
    * **Není povoleno**: Toto je z důvodu zásad v zakázaném stavu. 
    
**Otázka: Název zásady v sestavě všechna přihlášení neodpovídá názvu zásady v certifikační Autoritě. Proč?**

**ODPOVĚĎ:** Název zásady v sestavě všechna přihlášení vychází z názvu certifikační Autority zásad v době přihlášení. To může být konzistentní se název zásady v certifikační Autoritě, pokud jste aktualizovali název zásady později, tedy po přihlášení.

**Otázka: Moje přihlášení se zablokovala, protože zásady podmíněného přístupu, ale v sestavě aktivit přihlašování se zobrazí, že přihlášení bylo úspěšné. Proč?**

**ODPOVĚĎ:** Aktuálně přihlášení se v sestavě nemusí zobrazovat přesné výsledky pro Exchange ActiveSync scénáře při použití podmíněného přístupu. Můžou nastat případy, když výsledek přihlášení v sestavě se zobrazuje úspěšné přihlášení, ale přihlášení ve skutečnosti selhala z důvodu zásad podmíněného přístupu. 
