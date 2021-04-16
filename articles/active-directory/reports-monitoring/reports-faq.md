---
title: Nejčastější dotazy k Azure Active Directoryům sestav | Microsoft Docs
description: Nejčastější dotazy týkající se Azure Active Directory sestav.
services: active-directory
documentationcenter: ''
author: cawrites
manager: MarkusVi
ms.assetid: 534da0b1-7858-4167-9986-7a62fbd10439
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: report-monitor
ms.date: 05/12/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4da0083a236900037b388798d825515e94613c20
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533721"
---
# <a name="frequently-asked-questions-around-azure-active-directory-reports"></a>Nejčastější dotazy týkající se Azure Active Directory sestav

Tento článek obsahuje odpovědi na nejčastější dotazy týkající se vytváření sestav Azure Active Directory (Azure AD). Další informace najdete v článku [Generování sestav ve službě Azure Active Directory](overview-reports.md). 

## <a name="getting-started"></a>Začínáme 

**Otázka: nyní používám `https://graph.windows.net/<tenant-name>/reports/` rozhraní API koncového bodu k vygenerování sestav Azure AD audit a integrovaných použití aplikací do našich systémů vytváření sestav prostřednictvím kódu programu. Co mám přepnout na?**

**A:** Vyhledejte Reference k [rozhraní API](https://developer.microsoft.com/graph/) a podívejte se, jak můžete [používat rozhraní API pro přístup k sestavám aktivit](concept-reporting-api.md). Tento koncový bod obsahuje dvě sestavy (**audit** a **přihlášení**), které poskytují všechna data, která jste získali ve starém koncovém bodu rozhraní API. Tento nový koncový bod má také sestavu přihlášení s licencí Azure AD Premium, kterou můžete použít k získání informací o využití aplikace, použití zařízení a přihlašovacích údajích uživatele.

---

**Otázka: teď používáme `https://graph.windows.net/<tenant-name>/reports/` rozhraní API koncových bodů k vyžádání sestav zabezpečení Azure AD (konkrétní typy detekcí, jako jsou nevrácená pověření nebo přihlášení z anonymních IP adres) do našich systémů vytváření sestav prostřednictvím kódu programu. Co mám přepnout na?**

**A:** [Rozhraní API detekce rizik v rámci Identity Protection](../identity-protection/howto-identity-protection-graph-api.md) můžete použít k přístupu k detekcím zabezpečení prostřednictvím Microsoft Graph. Tento nový formát poskytuje větší flexibilitu v tom, jak můžete zadávat dotazy na data, s využitím pokročilého filtrování, výběru polí a dalších možností a standardizovat detekci rizik do jednoho typu pro snazší integraci do systémů Siem a dalších nástrojů pro shromažďování dat. Vzhledem k tomu, že data jsou v jiném formátu, nemůžete pro staré dotazy použít nový dotaz. [Nové rozhraní API ale používá Microsoft Graph](/graph/api/resources/identityriskevent?view=graph-rest-beta&preserve-view=true), což je standard Microsoftu pro taková rozhraní api jako Microsoft 365 nebo Azure AD. To znamená, že požadovaná práce může buď rozšířila vaše aktuální Microsoft Graph investice, nebo vám umožní začít přejít na tuto novou standardní platformu.

---

**Otázka: Návody získat licenci Premium?**

**A:** Pokud chcete upgradovat edici Azure Active Directory, přečtěte si téma [Začínáme se Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) .

---

**Otázka: jak brzy bych po získání licence na verzi Premium viděli data o aktivitách?**

**A:** Pokud již máte data o aktivitách jako bezplatnou licenci, pak ji můžete okamžitě zobrazit. Pokud nemáte žádná data, bude trvat až 3 dny, než se data zobrazí v sestavách.

---

**Otázka: můžu zobrazit data z posledního měsíce po získání licence Azure AD Premium?**

**A:** Pokud jste nedávno přešli na verzi Premium (včetně zkušební verze), můžete nejdřív zobrazit data až 7 dní. Po shromáždění dat můžete zobrazit data za posledních 30 dní.

---

**Otázka: Potřebuji být globálním správcem, aby se zobrazily přihlašovací údaje aktivity k Azure Portal nebo aby bylo možné získat data prostřednictvím rozhraní API?**

**A:** Ne. k datům sestav můžete také přistupovat prostřednictvím portálu nebo přes rozhraní API, pokud jste **Čtenář zabezpečení** nebo **Správce zabezpečení** pro tenanta. K těmto datům má samozřejmě přístup i **globální správci** .

---


## <a name="activity-logs"></a>Protokoly aktivit


**Otázka: jaká je doba uchovávání dat v protokolech aktivit (audit a přihlášení) v Azure Portal?** 

**A:** Další informace najdete v tématu [zásady uchovávání dat pro sestavy služby Azure AD](reference-reports-data-retention.md).

---

**Otázka: jak dlouho trvá, než po dokončení úkolu uvidí data aktivity?**

**A:** Protokoly auditu mají latenci v rozmezí od 15 minut po hodinu. Protokoly aktivit přihlašování můžou po dobu 15 minut trvat až 2 hodiny u některých záznamů.

---

**Otázka: mohu získat informace o Microsoft 365 protokolu aktivit prostřednictvím Azure Portal?**

**A:** I když Microsoft 365 aktivity a protokoly aktivit služby Azure AD sdílejí spoustu prostředků adresáře, pokud chcete úplné zobrazení protokolů aktivit Microsoft 365, měli byste přejít do [centra pro správu Microsoft 365](https://admin.microsoft.com) a získat informace o protokolu aktivit Office 365.

---

**Otázka: jaká rozhraní API mám použít k získání informací o Microsoft 365ch protokolů aktivit?**

**A:** [Rozhraní API pro správu Microsoft 365](/office/office-365-management-api/office-365-management-apis-overview) slouží k přístupu k protokolům aktivit Microsoft 365 prostřednictvím rozhraní API.

---

**Otázka: kolik záznamů Mohu stahovat z Azure Portal?**

**A:** Z Azure Portal můžete stáhnout záznamy až 5000. Záznamy jsou seřazené podle nejnovějších *a ve* výchozím nastavení se vám zobrazí nejnovější záznamy 5000.

---

## <a name="risky-sign-ins"></a>Riziková přihlášení

**Otázka: existuje detekce rizik v rámci Identity Protection, ale v sestavě přihlášení se nezobrazuje odpovídající přihlášení. Je to očekávané?**

**A:** Ano, ochrana identity vyhodnocuje riziko pro všechny toky ověřování, ať už interaktivní, nebo neinteraktivní. Všechna přihlášení však pouze zobrazí pouze interaktivní přihlášení.

---

**Otázka: Návody zjistit, proč přihlášení nebo uživatel označil v Azure Portal rizikové?**

**A:** Pokud máte předplatné **Azure AD Premium** , můžete získat další informace o základních detekcích rizik výběrem uživatele v **označení příznakem rizika** nebo výběrem záznamu v sestavě **rizikové přihlašování** . Máte-li **bezplatné** předplatné nebo **základní** předplatné, můžete zobrazit sestavy rizikových a rizikových přihlášení uživatelů, ale nevidíte základní informace o detekci rizika.

---

**Otázka: jak se počítají IP adresy v sestavě přihlášení a rizikových přihlášení?**

**A:** IP adresy se vydávají takovým způsobem, že mezi IP adresou neexistuje konečné připojení a kde je počítač s touto adresou fyzicky umístěný. Mapování IP adres je dále obtížné díky faktorům, jako jsou například poskytovatelé mobilních služeb a sítě VPN, které vydávají IP adresy z centrálních fondů často příliš daleko od místa, kde je klientské zařízení skutečně použito. V současné době se v sestavách Azure AD převádějí IP adresa na fyzické místo, což je nejlepší úsilí na základě trasování, dat registru, zpětného vyhledávání a dalších informací. 

---

**Otázka: co znamená, že při zjišťování rizik se zjistilo, že se objevilo dodatečné riziko?**

**A:** Pokud chcete mít přehled o všech rizikových přihlášeních ve vašem prostředí, přihlaste se s dalšími zjištěnými riziky jako zástupné symboly pro přihlášení k detekcím, které jsou výhradně Azure AD Identity Protection odběratelům.

---

## <a name="conditional-access"></a>Podmíněný přístup

**Otázka: co je nového u této funkce?**

**A:** Zákazníci teď můžou řešit zásady podmíněného přístupu prostřednictvím všech přihlašovacích sestav. Zákazníci si můžou prohlédnout stav podmíněného přístupu a podrobně se na podrobnosti o zásadách, které se pro přihlášení použili, a výsledku pro jednotlivé zásady.

**Otázka: Návody začít?**

**A:** Začněte tím, že:

* Přejděte do sestavy přihlášení v [Azure Portal](https://portal.azure.com).
* Klikněte na přihlášení, které chcete řešit.
* Přejděte na kartu **podmíněný přístup** . Tady můžete zobrazit všechny zásady, které mají vliv na přihlášení a výsledek pro jednotlivé zásady. 
    
**Otázka: Jaké jsou všechny možné hodnoty pro stav podmíněného přístupu?**

**A:** Stav podmíněného přístupu může mít následující hodnoty:

* **Nepoužito**: to znamená, že pro uživatele a aplikaci v oboru neexistovaly žádné zásady podmíněného přístupu. 
* **Úspěch**: to znamená, že existovaly zásady podmíněného přístupu s uživatelem a aplikací v oboru a zásady podmíněného přístupu byly úspěšně splněné. 
* **Selhání**: přihlášení splnilo podmínku pro uživatele a aplikaci alespoň pro jednu zásadu podmíněného přístupu a udělení těchto ovládacích prvků není splněno nebo je nastaveno pro blokování přístupu.
    
**Otázka: Jaké jsou všechny možné hodnoty pro výsledek zásad podmíněného přístupu?**

**A:** Zásada podmíněného přístupu může mít následující výsledky:

* **Úspěch**: zásada byla úspěšně splněna.
* **Chyba**: zásada nebyla splněna.
* **Nepoužito**: to může být způsobeno tím, že podmínky zásad nebyly splněny.
* **Nepovoleno**: příčinou je zásada v zakázaném stavu. 
    
**Otázka: název zásady v sestavě všech přihlášení se neshoduje s názvem zásady v certifikační autoritě. Proč?**

**A:** Název zásad v sestavě všechna přihlášení je založený na názvu zásad podmíněného přístupu v době přihlášení. To může být nekonzistentní s názvem zásady v certifikační autoritě, pokud jste později aktualizovali název zásady, tedy po přihlášení.

**Otázka: moje přihlášení bylo zablokováno kvůli zásadám podmíněného přístupu, ale v sestavě přihlašovací aktivity se zobrazí zpráva o úspěšném přihlášení. Proč?**

**A:** V současné době se při použití podmíněného přístupu v této sestavě nemusí zobrazovat přesné výsledky scénářů Exchange ActiveSync. Můžou nastat případy, kdy se při přihlašování v sestavě zobrazuje úspěšné přihlášení, ale přihlášení se kvůli zásadám podmíněného přístupu skutečně nezdařilo.
