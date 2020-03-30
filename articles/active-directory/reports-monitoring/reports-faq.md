---
title: Nejčastější dotazy k přehledovým zprávám služby Azure Active Directory | Dokumenty společnosti Microsoft
description: Nejčastější dotazy týkající se sestav služby Azure Active Directory.
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
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 273fdb80475defb0576bcd29d1944c5f6c595cfc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266504"
---
# <a name="frequently-asked-questions-around-azure-active-directory-reports"></a>Nejčastější dotazy týkající se sestav služby Azure Active Directory

Tento článek obsahuje odpovědi na nejčastější dotazy týkající se vytváření sestav služby Azure Active Directory (Azure AD). Další informace najdete v článku [Generování sestav ve službě Azure Active Directory](overview-reports.md). 

## <a name="getting-started"></a>Začínáme 

**Otázka: V současné `https://graph.windows.net/<tenant-name>/reports/` době používám koncová nastavení API k vymáčkání auditu Azure AD a integrovaných sestav využití aplikací do našich systémů vytváření sestav programově. Na co mám přepnout?**

**A:** Vyhledejte [odkaz na rozhraní API](https://developer.microsoft.com/graph/) a zjistěte, jak můžete [použít rozhraní API pro přístup k sestavám aktivit](concept-reporting-api.md). Tento koncový bod má dvě sestavy **(Audit** a **Přihlášení),** které poskytují všechna data, která jste získali ve starém koncovém bodu rozhraní API. Tento nový koncový bod má také sestavu přihlášení s licencí Azure AD Premium, kterou můžete použít k získání využití aplikací, využití zařízení a přihlašovacích informací uživatelů.

---

**Otázka: V současné `https://graph.windows.net/<tenant-name>/reports/` době používám koncová úložiště API k vyžádání sestav zabezpečení Azure AD (konkrétní typy zjišťování, jako jsou neuniklá pověření nebo přihlášení z anonymních IP adres) do našich systémů vykazování programově. Na co mám přepnout?**

**A:** Rozhraní API [pro detekci rizik ochrany identity](../identity-protection/graph-get-started.md) můžete použít k přístupu k detekci zabezpečení prostřednictvím aplikace Microsoft Graph. Tento nový formát poskytuje větší flexibilitu v tom, jak můžete dotazovat data, s pokročilým filtrováním, výběrem polí a dalšími a standardizuje detekci rizik do jednoho typu pro snadnější integraci do siem a dalších nástrojů pro shromažďování dat. Vzhledem k tomu, že data jsou v jiném formátu, nelze nahradit nový dotaz pro staré dotazy. Nové [rozhraní API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityriskevent)však používá Microsoft Graph , což je standard Microsoftu pro rozhraní API jako O365 nebo Azure AD. Takže požadovaná práce může buď rozšířit vaše aktuální investice do aplikace Microsoft Graph, nebo vám pomůže zahájit přechod na tuto novou standardní platformu.

---

**Otázka: Jak získám prémiovou licenci?**

**A:** Podívejte [se na téma Začínáme s Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) a upgradujte edici Azure Active Directory.

---

**Otázka: Za jak dlouho mám po získání prémiové licence zobrazit údaje o aktivitách?**

**A:** Pokud již máte údaje o aktivitách jako bezplatnou licenci, můžete je okamžitě vidět. Pokud nemáte žádná data, bude trvat jeden nebo dva dny, než se data zobrazí v přehledech.

---

**Otázka: Můžu vidět data z minulého měsíce po získání licence Azure AD premium?**

**A:** Pokud jste nedávno přešli na prémiovou verzi (včetně zkušební verze), můžete zpočátku zobrazit data až 7 dní. Když se data hromadí, můžete zobrazit data za posledních 30 dní.

---

**Otázka: Musím být globální správce vidět přihlášení k aktivitám na webu Azure Portal nebo získat data prostřednictvím rozhraní API?**

**A:** Ne, k datům sestav můžete přistupovat také prostřednictvím portálu nebo rozhraní API, pokud jste **čtečkou zabezpečení** nebo **správcem zabezpečení** pro klienta. Samozřejmě, **globální správci** budou mít také přístup k těmto datům.

---


## <a name="activity-logs"></a>Protokoly aktivit


**Otázka: Co je uchovávání dat pro protokoly aktivit (audit a přihlášení) na webu Azure Portal?** 

**A:** V následující tabulce je uvedena doba uchovávání dat pro protokoly aktivit. Další informace najdete v [tématu zásady uchovávání dat pro sestavy Azure AD](reference-reports-data-retention.md).

| Sestava                 | Azure AD Free | Azure AD Premium P1 | Azure AD Premium P2 |
| :--                    | :--           | :--                 | :--                 |
| Protokoly auditu             | 7 dní        | 30 dní             | 30 dní             |
| Přihlášení               | Není dostupné.           | 30 dní             | 30 dní             |
| Využití Azure MFA        | 30 dní       | 30 dní             | 30 dní             |

---

**Otázka: Jak dlouho trvá, než se po dokončení úkolu zobrazí data o aktivitě?**

**A:** Protokoly auditu mají latenci v rozsahu od 15 minut do hodiny. Protokoly aktivit přihlášení může trvat od 15 minut až 2 hodiny pro některé záznamy.

---

**Otázka: Můžu získat informace protokolu aktivit Office 365 prostřednictvím portálu Azure?**

**A:** I když aktivity Office 365 a protokoly aktivit Azure AD sdílejí velké množství prostředků adresáře, pokud chcete úplné zobrazení protokolů aktivit Office 365, měli byste přejít do [Centra pro správu Microsoftu 365](https://admin.microsoft.com) a získat informace protokolu aktivit Office 365.

---

**Otázka: Která api se používají k získání informací o protokolech aktivit Office 365?**

**A:** Pomocí [rozhraní API pro správu Office 365](https://docs.microsoft.com/office/office-365-management-api/office-365-management-apis-overview) můžete přistupovat k protokolům aktivit Office 365 prostřednictvím rozhraní API.

---

**Otázka: Kolik záznamů si můžu stáhnout z Azure Portal?**

**A:** Z webu Azure Portal si můžete stáhnout až 5000 záznamů. Záznamy jsou seřazeny podle *nejnovějších* a ve výchozím nastavení získáte posledních 5000 záznamů.

---

## <a name="risky-sign-ins"></a>Riziková přihlášení

**Otázka: V ochraně identity je detekce rizik, ale v sestavě přihlášení se mi nezobrazuje odpovídající přihlášení. Očekává se to?**

**A:** Ano, Identity Protection vyhodnocuje riziko pro všechny toky ověřování, ať už interaktivní nebo neinteraktivní. Všechny sestavy přihlášení však zobrazují pouze interaktivní přihlášení.

---

**Otázka: Jak se dozvím, proč bylo přihlášení nebo uživatel označeno jako rizikové na webu Azure Portal?**

**A:** Pokud máte předplatné **Azure AD Premium,** můžete získat další informace o základní zjišťování rizik a vybrat uživatele v **části Uživatelé označeni pro riziko** nebo výběrem záznamu v sestavě rizikové **přihlášení.** Pokud máte **bezplatné** nebo **základní** předplatné, můžete zobrazit ohrožené uživatele a sestavy rizikových přihlášení, ale nemůžete zobrazit základní informace o zjišťování rizik.

---

**Otázka: Jak se vypočítají IP adresy v sestavě přihlášení a rizikových přihlášení?**

**A:** Ip adresy jsou vydávány takovým způsobem, že neexistuje žádné konečné spojení mezi adresou IP a místem, kde je počítač s touto adresou fyzicky umístěn. Mapování IP adres je dále komplikováno faktory, jako jsou mobilní poskytovatelé a virtuální ny vydávající IP adresy z centrálních fondů, často velmi daleko od místa, kde se klientské zařízení skutečně používá. V současné době v sestavách Azure AD je převod IP adresy na fyzické umístění nejlepší úsilí založené na trasování, datech registru, zpětném vyhledávání a dalších informacích. 

---

**Otázka: Co znamená detekce rizika "Přihlášení s dalším zjištěným rizikem"?**

**A:** Abychom vám poskytli přehled o všech rizikových přihlášeních ve vašem prostředí, funguje "Přihlášení s dalším zjištěným rizikem" jako zástupný symbol pro přihlášení pro zjišťování, která jsou výhradní pro předplatitele Azure AD Identity Protection.

---

## <a name="conditional-access"></a>Podmíněný přístup

**Otázka: Co je nového s touto funkcí?**

**A:** Zákazníci teď můžou řešit zásady podmíněného přístupu prostřednictvím sestavy všech přihlášení. Zákazníci si mohou zkontrolovat stav podmíněného přístupu a ponořit se do podrobností o zásadách, které se vztahují k přihlášení, a výsledku pro každou zásadu.

**Otázka: Jak začít?**

**A:** Chcete-li začít:

* Přejděte na sestavu přihlášení na [webu Azure Portal](https://portal.azure.com).
* Klikněte na přihlášení, které chcete řešit.
* Přejděte na kartu **Podmíněný přístup.** Zde můžete zobrazit všechny zásady, které ovlivnily přihlášení a výsledek pro každou zásadu. 
    
**Otázka: Jaké jsou všechny možné hodnoty pro stav podmíněného přístupu?**

**A:** Stav podmíněného přístupu může mít následující hodnoty:

* **Nepoužito**: To znamená, že u uživatele a aplikace v oboru neexistovaly žádné zásady certifikační autority. 
* **Úspěch**: To znamená, že existovala zásada certifikační autority s uživatelem a aplikací v oboru a zásady certifikační autority byly úspěšně splněny. 
* **Selhání**: To znamená, že existovala zásada certifikační autority s uživatelem a aplikací v oboru a zásady certifikační autority nebyly splněny. 
    
**Otázka: Jaké jsou všechny možné hodnoty pro výsledek zásad podmíněného přístupu?**

**A:** Zásady podmíněného přístupu mohou mít následující výsledky:

* **Úspěch**: Politika byla úspěšně splněna.
* **Selhání**: Zásada nebyla splněna.
* **Nepoužito**: Může to být způsobeno tím, že podmínky zásad nesplňovaly.
* **Není povoleno**: Je to způsobeno zásadou v zakázaném stavu. 
    
**Otázka: Název zásady v sestavě všech přihlášení neodpovídá názvu zásady v certifikační autoritě. Proč?**

**A:** Název zásady v sestavě všech přihlášení je založen na názvu zásady certifikační autority v době přihlášení. To může být v rozporu s názvem zásad v certifikační autoritě, pokud jste název zásady aktualizovali později, tj.

**Otázka: Moje přihlášení bylo zablokováno z důvodu zásad podmíněného přístupu, ale sestava aktivit přihlášení ukazuje, že přihlášení proběhlo úspěšně. Proč?**

**A:** V současné době semůže, že sestavu přihlášení nemusí zobrazit přesné výsledky pro scénáře Exchange ActiveSync při použití podmíněného přístupu. Mohou napovědět případy, kdy výsledek přihlášení v sestavě zobrazí úspěšné přihlášení, ale přihlášení se ve skutečnosti nezdařilo z důvodu zásady podmíněného přístupu. 
