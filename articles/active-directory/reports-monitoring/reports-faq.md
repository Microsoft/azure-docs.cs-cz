---
title: Nejčastější dotazy k Azure Active Directoryům sestav | Microsoft Docs
description: Nejčastější dotazy k Azure Active Directory sestavování quesitons.
services: active-directory
documentationcenter: ''
author: cawrites
manager: daveba
ms.assetid: 534da0b1-7858-4167-9986-7a62fbd10439
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: e8c3138b82c7dc4a7217e8cb67448a5d824398ba
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70127011"
---
# <a name="frequently-asked-questions-around-azure-active-directory-reports"></a>Nejčastější dotazy týkající se Azure Active Directory sestav

Tento článek obsahuje odpovědi na nejčastější dotazy týkající se vytváření sestav Azure Active Directory (Azure AD). Další informace najdete v článku [Generování sestav ve službě Azure Active Directory](overview-reports.md). 

## <a name="getting-started"></a>Začínáme 

**Otázka: Teď používám `https://graph.windows.net/<tenant-name>/reports/` rozhraní API koncového bodu k získání sestav Azure AD audit a integrovaných použití aplikací do našich systémů vytváření sestav prostřednictvím kódu programu. Co mám přepnout na?**

**Odpověď:** Vyhledejte Reference k [rozhraní API](https://developer.microsoft.com/graph/) a podívejte se, jak můžete [používat rozhraní API pro přístup k sestavám aktivit](concept-reporting-api.md). Tento koncový bod obsahuje dvě sestavy (**audit** a **přihlášení**), které poskytují všechna data, která jste získali ve starém koncovém bodu rozhraní API. Tento nový koncový bod má také sestavu přihlášení s licencí Azure AD Premium, kterou můžete použít k získání informací o využití aplikace, použití zařízení a přihlašovacích údajích uživatele.

---

**Otázka: Nyní používám `https://graph.windows.net/<tenant-name>/reports/` rozhraní API koncového bodu ke stažení sestav zabezpečení Azure AD (specifických typů detekcí, jako jsou nevrácená pověření nebo přihlášení z anonymních IP adres) do našich systémů vytváření sestav prostřednictvím kódu programu. Co mám přepnout na?**

**Odpověď:**   [Rozhraní API detekce rizik v rámci Identity Protection](../identity-protection/graph-get-started.md)můžete použít k přístupu k detekcím zabezpečení prostřednictvím Microsoft Graph. Tento nový formát poskytuje větší flexibilitu v tom, jak můžete zadávat dotazy na data, s využitím pokročilého filtrování, výběru polí a dalších možností a standardizovat detekci rizik do jednoho typu pro snazší integraci do systémů Siem a dalších nástrojů pro shromažďování dat. Vzhledem k tomu, že data jsou v jiném formátu, nemůžete pro staré dotazy použít nový dotaz. [Nové rozhraní API ale používá Microsoft Graph](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityriskevent), což je standard Microsoftu pro taková rozhraní API jako O365 nebo Azure AD. To, co je potřeba, může buď zvětšit vaše aktuální investice do MS graphu, nebo vám pomůžou začít s přechodem na tuto novou standardní platformu.

---

**Otázka: Návody získat licenci na verzi Premium?**

**Odpověď:** Pokud chcete upgradovat edici Azure Active Directory, přečtěte si téma [Začínáme se Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) .

---

**Otázka: Jak brzo mám zobrazit data o aktivitách po získání licence na prémii?**

**Odpověď:** Pokud již máte data o aktivitách jako bezplatnou licenci, pak ji můžete okamžitě zobrazit. Pokud nemáte žádná data, pak se data budou zobrazovat v sestavách za dobu jednoho nebo dvou dnů.

---

**Otázka: Můžu zobrazit data z posledního měsíce po získání licence Azure AD Premium?**

**Odpověď:** Pokud jste nedávno přešli na verzi Premium (včetně zkušební verze), můžete nejdřív zobrazit data až 7 dní. Po shromáždění dat můžete zobrazit data za posledních 30 dní.

---

**Otázka: Potřebuji být globálním správcem, aby se zobrazily přihlašovací údaje aktivity k Azure Portal nebo aby bylo možné získat data prostřednictvím rozhraní API?**

**Odpověď:** Ne. k datům sestav můžete také přistupovat prostřednictvím portálu nebo přes rozhraní API, pokud jste **Čtenář zabezpečení** nebo **Správce zabezpečení** pro tenanta. K těmto datům má samozřejmě přístup i **globální správci** .

---


## <a name="activity-logs"></a>Protokoly aktivit


**Otázka: Jaká je doba uchovávání dat v protokolech aktivit (audit a přihlášení) v Azure Portal?** 

**Odpověď:** Následující tabulka uvádí dobu uchovávání dat pro protokoly aktivit. Další informace najdete v tématu [zásady uchovávání dat pro sestavy služby Azure AD](reference-reports-data-retention.md).

| Zpráva                 | Azure AD Free | Azure AD Premium P1 | Azure AD Premium P2 |
| :--                    | :--           | :--                 | :--                 |
| Protokoly auditu             | 7 dní        | 30 dní             | 30 dní             |
| Přihlášení               | Není k dispozici           | 30 dní             | 30 dní             |
| Využití Azure MFA        | 30 dní       | 30 dní             | 30 dní             |

---

**Otázka: Jak dlouho trvá, než po dokončení úkolu uvidí data aktivity?**

**Odpověď:** Protokoly auditu mají latenci v rozmezí od 15 minut po hodinu. Protokoly aktivit přihlašování můžou po dobu 15 minut trvat až 2 hodiny u některých záznamů.

---

**Otázka: Můžu prostřednictvím Azure Portal získat informace o protokolu aktivit Office 365?**

**Odpověď:** I když aktivity Office 365 a protokoly aktivit služby Azure AD sdílejí spoustu prostředků adresáře, pokud chcete zobrazit úplné zobrazení protokolů aktivit Office 365, měli byste přejít do [centra pro správu Microsoft 365](https://admin.microsoft.com) a získat informace o protokolu aktivit Office 365.

---

**Otázka: Jaká rozhraní API mám použít k získání informací o protokolech aktivit Office 365?**

**Odpověď:** Použijte [rozhraní API pro správu sady office 365](https://docs.microsoft.com/office/office-365-management-api/office-365-management-apis-overview) k přístupu k protokolům aktivit Office 365 prostřednictvím rozhraní API.

---

**Otázka: Kolik záznamů lze stáhnout z Azure Portal?**

**Odpověď:** Z Azure Portal můžete stáhnout záznamy až 5000. Záznamy jsou seřazené podle nejnovějších a ve výchozím nastavení se vám zobrazí nejnovější záznamy 5000.

---

## <a name="risky-sign-ins"></a>Riziková přihlášení

**Otázka: V rámci Identity Protection se detekuje riziko, ale v sestavě přihlášení se nezobrazuje odpovídající přihlášení. Je to očekávané?**

**Odpověď:** Ano, ochrana identity vyhodnocuje riziko pro všechny toky ověřování, ať už interaktivní, nebo neinteraktivní. Všechna přihlášení však pouze zobrazí pouze interaktivní přihlášení.

---

**Otázka: Návody zjistit, proč přihlášení nebo uživatel označil v Azure Portal rizikové?**

**Odpověď:** Pokud máte předplatné **Azure AD Premium** , můžete získat další informace o základních detekcích rizik výběrem uživatele v **označení příznakem rizika** nebo výběrem záznamu v sestavě **rizikové přihlašování** . Máte-li **bezplatné** předplatné nebo **základní** předplatné, můžete zobrazit sestavy rizikových a rizikových přihlášení uživatelů, ale nevidíte základní informace o detekci rizika.

---

**Otázka: Jak se počítají IP adresy v sestavě přihlášení a rizikových přihlášení?**

**Odpověď:** IP adresy se vydávají takovým způsobem, že mezi IP adresou neexistuje konečné připojení a kde je počítač s touto adresou fyzicky umístěný. Mapování IP adres je dále obtížné díky faktorům, jako jsou například poskytovatelé mobilních služeb a sítě VPN, které vydávají IP adresy z centrálních fondů často příliš daleko od místa, kde je klientské zařízení skutečně použito. V současné době se v sestavách Azure AD převádějí IP adresa na fyzické místo, což je nejlepší úsilí na základě trasování, dat registru, zpětného vyhledávání a dalších informací. 

---

**Otázka: Co znamená, že při zjišťování rizik se zjistilo, že se objevilo dodatečné riziko?**

**Odpověď:** Pokud chcete mít přehled o všech rizikových přihlášeních ve vašem prostředí, přihlaste se s dalšími zjištěnými riziky jako zástupné symboly pro přihlášení k detekcím, které jsou výhradně Azure AD Identity Protection odběratelům.

---

## <a name="conditional-access"></a>Podmíněný přístup

**Otázka: Co je nového u této funkce?**

**Odpověď:** Zákazníci teď můžou řešit zásady podmíněného přístupu prostřednictvím všech přihlašovacích sestav. Zákazníci si můžou prohlédnout stav podmíněného přístupu a podrobně se na podrobnosti o zásadách, které se pro přihlášení použili, a výsledku pro jednotlivé zásady.

**Otázka: Návody začít?**

**Odpověď:** Jak začít:

* Přejděte do sestavy přihlášení v [Azure Portal](https://portal.azure.com).
* Klikněte na přihlášení, které chcete řešit.
* Přejděte na kartu **podmíněný přístup** . Tady můžete zobrazit všechny zásady, které mají vliv na přihlášení a výsledek pro jednotlivé zásady. 
    
**Otázka: Jaké jsou všechny možné hodnoty pro stav podmíněného přístupu?**

**Odpověď:** Stav podmíněného přístupu může mít následující hodnoty:

* Nepoužito: To znamená, že pro uživatele a aplikaci v oboru neexistovala žádná zásada certifikační autority. 
* **Úspěch**: To znamená, že existovala zásada certifikační autority s uživatelem a aplikací v oboru a zásady certifikační autority byly úspěšně splněné. 
* **Selhání**: To znamená, že existovala zásada certifikační autority s uživatelem a aplikací v oboru a zásady certifikační autority nebyly splněné. 
    
**Otázka: Jaké jsou všechny možné hodnoty pro výsledek zásad podmíněného přístupu?**

**Odpověď:** Zásada podmíněného přístupu může mít následující výsledky:

* **Úspěch**: Zásady se úspěšně splnily.
* **Selhání**: Zásada nebyla splněna.
* Nepoužito: Důvodem může být to, že podmínky zásad nebyly splněny.
* **Nepovoleno**: Důvodem je zásada v zakázaném stavu. 
    
**Otázka: Název zásad v sestavě všechna přihlášení se neshoduje s názvem zásady v certifikační autoritě. Proč?**

**Odpověď:** Název zásad v sestavě všechna přihlášení je založený na názvu zásady certifikační autority v době přihlášení. To může být nekonzistentní s názvem zásady v certifikační autoritě, pokud jste později aktualizovali název zásady, tedy po přihlášení.

**Otázka: Moje přihlášení bylo zablokováno kvůli zásadám podmíněného přístupu, ale sestava přihlašovací aktivity ukazuje, že přihlášení bylo úspěšné. Proč?**

**Odpověď:** V současné době se při použití podmíněného přístupu v této sestavě nemusí zobrazovat přesné výsledky scénářů Exchange ActiveSync. Můžou nastat případy, kdy se při přihlašování v sestavě zobrazuje úspěšné přihlášení, ale přihlášení se kvůli zásadám podmíněného přístupu skutečně nezdařilo. 
