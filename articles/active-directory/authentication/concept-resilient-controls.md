---
title: Vytvořit strategie správy řízení odolné přístupu v Azure Active Directory
description: Tento dokument obsahuje pokyny ke strategiím, což je organizace by měl přijmout zajistit odolnost, aby se snížilo riziko uzamčení při přerušení nepředvídatelné
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 12/19/2018
ms.author: martincoetzer
ms.collection: M365-identity-device-management
ms.openlocfilehash: e5fb263819a5bb96175f636f53a16c28649a3f39
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2019
ms.locfileid: "56339545"
---
# <a name="create-a-resilient-access-control-management-strategy-with-azure-active-directory"></a>Vytvořit strategie správy řízení odolné přístupu v Azure Active Directory

>[!NOTE]
> Informace obsažené v tomto dokumentu představují aktuální pohled společnosti Microsoft Corporation na tyto problémy k datu publikování. Protože Microsoft reagovat na měnící se podmínky na trhu, neměly by být vykládány jako závazek Microsoftu a společnost Microsoft nemůže zaručit přesnost jakýchkoli informací, které jsou prezentovány po provedení datu publikování.

Pokud toto řízení přístupu jednoho přestane být k dispozici jsou však náchylné k selhání přístupu k jejich aplikacím a prostředkům organizace, které využívají přístup pomocí jednotného ovládací prvek, jako je například vícefaktorové ověřování (MFA) nebo jednoho umístění sítě, zabezpečení IT systémů nebo není správně nakonfigurovaný. Například přírodní katastrofě může způsobit nedostupnost velké segmenty telekomunikační infrastruktury nebo podnikové sítě. Takové přerušení může bránit koncovým uživatelům a správcům tomu nebudou moct přihlásit.

Tento dokument obsahuje pokyny ke strategiím, což je organizace by měl přijmout zajistit odolnost, aby se snížilo riziko uzamčení při přerušení nepředvídatelné s následující scénáře:

 1. Organizace může zvýšit jejich odolnost proti chybám, aby se snížilo riziko uzamčení **před přerušení** implementací strategie omezení rizik nebo plánů řešení nepředvídaných událostí.
 2. Organizace může získat přístup k aplikacím a prostředkům rozhodne **během přerušení** tím, že strategie omezení rizik a plánů řešení nepředvídaných událostí na místě.
 3. Organizace by měly Ujistěte se, že budou udržovat informace, jako jsou protokoly, **po přerušení** a předtím, než se vrátit zpět všechny pojistné události jsou implementovány.
 4. Organizace, které nebyly implementovat strategie ochrany před únikem informací nebo alternativní plány mohou být schopni implementovat **nouzový možnosti** řešit narušení.

## <a name="key-guidance"></a>Klíče pokyny

Existují čtyři stěžejní v tomto dokumentu:

* Použití účtů pro nouzový přístup se vyhněte uzamčení správce.
* Implementovat vícefaktorové ověřování pomocí podmíněného přístupu (CA) namísto jednotlivých uživatelů MFA.
* Zmírnění uzamčení uživatelů s využitím více ovládacích prvků podmíněného přístupu (CA).
* Zmírnění uzamčení uživatelů tím, že zajistíte více metod ověřování nebo ekvivalenty pro každého uživatele.

## <a name="before-a-disruption"></a>Před přerušení

Zmírnění skutečný přerušení musí být v organizaci hlavním cílem při vyřizování potíže s přístupem k řízení, které mohou vzniknout. Zmírnění zahrnuje plánování skutečné události a implementaci strategie Ujistěte se, že řízení přístupu a operace nejsou ovlivněny při přerušení služeb.

### <a name="why-do-you-need-resilient-access-control"></a>Proč je potřeba řízení přístupu na odolná?

 Identita je roviny řízení uživatelům přístup k aplikacím a prostředkům. Vašeho systému identit řídí, kteří uživatelé a za jakých podmínek, jako je řízení přístupu nebo požadavky na ověřování, uživatelé získají přístup k aplikacím. Pokud jeden nebo více ověřování nebo přístup požadavky na řízení nejsou k dispozici pro uživatele bude možné ověřit z důvodu nepředpokládaného okolností, organizace může docházet jedno nebo obě z následujících problémů:

* **Správce uzamčení:** Správce nemůže spravovat klienta nebo služby.
* **Uzamčení uživatelů:** Uživatelé nemají přístup k aplikacím nebo prostředkům.

### <a name="administrator-lockout-contingency"></a>Správce uzamčení řešení nepředvídaných událostí

Abyste mohli používat přístup správce pro vašeho tenanta, byste měli vytvořit účty pro nouzový přístup. Tyto účty pro nouzový přístup, označované také jako *pohotovostní* účty, umožňují přístup ke správě konfigurace služby Azure AD, pokud nejsou k dispozici, postupy normální privilegovaný účet přístup. Aspoň dva účty pro nouzový přístup by měl být vytvořen po [doporučení pro nouzový přístup účet]( https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-emergency-access).

### <a name="mitigating-user-lockout"></a>Zmírnění uzamčení uživatelů

 Ke zmírnění nebezpečí uzamčení uživatelů, použijte zásady podmíněného přístupu s více ovládacími prvky a poskytuje tak uživatelům možnost jak se budou přistupovat k aplikacím a prostředkům. Tím, že uživatel volby, například přihlašování přes vícefaktorové ověřování **nebo** přihlášení ze spravovaných zařízení **nebo** přihlašování z podnikové sítě, pokud není k dispozici jeden z ovládacích prvků přístupu má uživatel Další možnosti pokračovat v práci.

#### <a name="microsoft-recommendations"></a>Doporučení Microsoftu

Zahrnuje následující řízení přístupu ve vaší stávající zásady podmíněného přístupu pro organizaci:

1. Zřízení více metod ověřování pro každého uživatele, které jsou závislé na různých komunikačních kanálech, například aplikace Microsoft Authenticator (internetový), token OATH (vygenerovaný na zařízení) a serveru SMS (stykem).
2. Nasazení Windows Hello pro firmy na zařízeních s Windows 10 splňovat požadavky na vícefaktorové ověřování přímo ze zařízení přihlásit.
3. Používat důvěryhodná zařízení prostřednictvím [připojení k Azure AD hybridní](https://docs.microsoft.com/azure/active-directory/devices/overview) nebo [zařízení spravovaná pomocí Microsoft Intune](https://docs.microsoft.com/intune/planning-guide). Důvěryhodná zařízení bude vylepšit uživatelské prostředí, protože samotné důvěryhodné zařízení může stát odpovědí na požadavky na silné ověřování zásad bez výzvu MFA pro uživatele. Vícefaktorové ověřování se bude vyžadovat, pak při registraci nového zařízení a při přístupu k aplikacím nebo prostředkům z nedůvěryhodných zařízení.
4. Použijte zásady Azure AD identity protection na základě rizik, které brání přístupu, když uživatele nebo přihlášení ohrožen místo pevné zásad vícefaktorového ověřování.

>[!NOTE]
> Zásady na základě rizik vyžadují [Azure AD Premium P2](https://azure.microsoft.com/pricing/details/active-directory/) licence.

Následující příklad popisuje zásady, musíte vytvořit k poskytování řízení odolné přístupu pro uživatele pro přístup k jejich aplikacím a prostředkům. V tomto příkladu budete potřebovat skupinu zabezpečení **AppUsers** se cíl uživateli, kterému chcete udělit přístup k, vytvoří skupina s názvem **CoreAdmins** správcům jádra a skupina s názvem  **EmergencyAccess** s účty pro nouzový přístup.
Tato sada zásad příklad udělí vybraných uživatelů v **AppUsers**, přístup k vybrané aplikace, pokud se připojujete z důvěryhodného zařízení nebo zadejte silné ověřování, například vícefaktorové ověřování. Vyloučí nouzový účtů a správců core.

**Nastavení zásad podmíněného přístupu na omezení rizik:**

* Zásady 1: Blokovat přístup k lidem, kteří nejsou cílové skupiny
  * Uživatelé a skupiny: Zahrňte všechny uživatele. Vyloučit AppUsers CoreAdmins a EmergencyAccess
  * Cloudové aplikace: Zahrnout všechny aplikace
  * Podmínky: (Žádný)
  * Udělit řízení: Zablokovat
* Zásady 2: Udělení přístupu k AppUsers vyžadování vícefaktorového ověřování nebo důvěryhodné zařízení.
  * Uživatelé a skupiny: Zahrnout AppUsers. Vyloučit CoreAdmins a EmergencyAccess
  * Cloudové aplikace: Zahrnout všechny aplikace
  * Podmínky: (Žádný)
  * Udělit řízení: Udělit přístup, vyžadovat vícefaktorové ověřování, vyžadují, aby zařízení dodržovalo předpisy. Pro více ovládacích prvků: Vyžadovat jeden z vybraných ovládacích prvků.

### <a name="contingencies-for-user-lockout"></a>Pojistné události pro uzamčení uživatelů

Vaše organizace také můžete vytvořit taky zásady řešení nepředvídaných událostí. Pokud chcete vytvořit řešení nepředvídaných událostí zásady, je nutné definovat kritéria kompromis mezi kontinuita, provozní náklady, finančních nákladů a bezpečnostní rizika. Můžete například aktivovat řešení nepředvídaných událostí zásady jenom na určitou podskupinu uživatelů pro celou dílčí sadu aplikací, pro podmnožinu klientů, nebo z určité podmnožiny umístění. Řešení nepředvídaných událostí zásad vám poskytne správce a koncoví uživatelé přístup k aplikacím a prostředkům, při přerušení, když bylo implementováno žádná metoda omezení rizik.
Principy vystavení během přerušení pomáhá snižovat riziko a je zásadní součástí procesu plánování. Chcete-li vytvořit plán řešení nepředvídaných událostí, nejprve určete následující podnikové požadavky vaší organizace:

1. Určení vaší stěžejní aplikace předem: Co jsou aplikace, je nutné udělit přístup, i s nižší stav rizika/zabezpečení? Sestavte seznam těchto aplikací a ujistěte se, že vaše další zainteresované uživatele (business, zabezpečení, právní, vedení) všechny svůj souhlas, že pokud všechny řízení přístupu zmizí, tyto aplikace stále nemusí dál běžet. Pravděpodobně budete za účelem s kategorií:
  * **Kategorie 1 stěžejní aplikace** , který nemůže být více než několik minut, například aplikace, které mají bezprostřední vliv na výnosy z organizace není k dispozici.
  * **Kategorie 2 důležitých aplikací** , podnik musí být přístupné během pár hodin.
  * **Aplikace s nízkou prioritou kategorie 3** , který dokázal zvládnout situaci přerušení několik dní.
2. Pro aplikace v kategorii 1 a 2 Microsoft doporučuje že dopředu plánovat, jaké úroveň přístupu, která chcete povolit:
  * Opravdu chcete povolit úplný přístup, nebo s omezeným přístupem relace, jako jsou omezení soubory ke stažení?
  * Opravdu chcete povolit přístup k části aplikace, ale ne celou aplikaci?
  * Opravdu chcete povolit přístup k informacím pracovního procesu a blokovat přístup správce, dokud se obnovení řízení přístupu?
3. Pro tyto aplikace Microsoft také doporučuje že naplánovat cesty, které vedoucí přístupu záměrně otevřete a ty, které se zavře:
  * Opravdu chcete povolit prohlížeči pouze přístupu a blokovat bohatých klientů, které můžete uložit data v režimu offline?
  * Opravdu chcete povolit přístup jenom pro uživatele v podnikové síti a zachovat mimo uživatelům Zablokovaná?
  * Opravdu chcete povolit přístup z určitých zemí nebo oblastí pouze během narušení?
  * Chcete, aby zásady, které řešení nepředvídaných událostí zásady, zejména u zásadně důležitých aplikací, neúspěšné nebo úspěšné, pokud ovládací prvek alternativní přístup není k dispozici?

#### <a name="microsoft-recommendations"></a>Doporučení Microsoftu

Zásady pohotovostní podmíněného přístupu je **zakázali zásadu** , která vynechává Azure MFA, používá služba MFA třetích stran, na základě rizik nebo na základě zařízení ovládacích prvků. Potom, pokud vaše organizace rozhodne aktivovat váš plán řešení nepředvídaných událostí, správci můžou zásadu povolit a zakázat regulární zásad založených na ovládací prvek.

>[!IMPORTANT]
> Zakázání zásady, které vynutit zabezpečení pro vaše uživatele, i dočasně sníží tak stav zabezpečení plán řešení nepředvídaných událostí je na místě.

* Konfigurace sady zásad pro použití náhradní lokality Pokud přerušení v jeden typ přihlašovacích údajů nebo jeden přístup ovládací prvek mechanismus důsledky pro přístup k vašim aplikacím. Nakonfigurujte zásady v zakázaném stavu, který vyžaduje připojení k doméně jako ovládací prvek jako záložní pro aktivní zásady, která vyžaduje poskytovatele MFA třetích stran.
* Snížit riziko nesprávnými účastníky uhodnutí hesla, když není potřeba vícefaktorové ověřování, pomocí následujících postupů v [heslo pokyny](https://aka.ms/passwordguidance) dokument white paper.
* Nasazení [Azure AD samoobslužný heslo resetovat (SSPR)](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr) a [ochrana hesel Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-ban-bad-on-premises-deploy) k Ujistěte se, že uživatelé nepoužívejte běžné heslo a podmínky, budete chtít zakázat.
* Použití zásad, které omezují přístup v rámci aplikace, pokud je určitá úroveň ověřování není dosaženo místo jednoduše nouzové přepnutí na úplný přístup. Příklad:
  * Nakonfigurujte zásady zálohování, které odešle deklaraci identity s omezeným přístupem relace ke službě Exchange a SharePoint.
  * Pokud vaše organizace používá Microsoft Cloud App Security, zvažte nouzové přepnutí na zásadu, která zaujme MCAS a potom MCAS umožňuje přístup jen pro čtení, ale ne nahraje.
* Zadejte název a ujistěte se, že se dají snadno najít je během přerušení. Název zásad zahrnout následující prvky:
  * A *číslo popisku* zásady.
  * Text, který má zobrazit, tato zásada je určená pro nouzové situace co pouze. Příklad: **POVOLIT NOUZOVÉ**
  * *Přerušení* se vztahuje na. Příklad: **Během výpadků MFA**
  * A *pořadové číslo* -li zobrazit pořadí, je nutné aktivovat zásady.
  * *Aplikace* se vztahuje na.
  * *Ovládací prvky* použije.
  * *Podmínky* vyžaduje.
  
Toto standardní pojmenování pro pohotovostní zásady budou následujícím způsobem: 

`
EMnnn - ENABLE IN EMERGENCY: [Disruption][i/n] - [Apps] - [Controls] [Conditions]
`

V následujícím příkladu: **Příklad A – řešení nepředvídaných událostí certifikační Autority zásad obnovit přístup k životně důležitého aplikace pro spolupráci**, je typické podnikové řešení nepředvídaných událostí. V tomto scénáři organizaci obvykle vyžadují vícefaktorové ověřování pro veškerý přístup pro Exchange Online a SharePoint Online a narušení v tomto případě je, že má poskytovatel MFA pro zákazníka výpadku (ať už s Azure MFA v místním poskytovatele MFA nebo vícefaktorové ověřování třetích stran). Tato zásada omezuje takového výpadku tím, že konkrétní cíloví uživatelé přístup k těmto aplikacím z důvěryhodných zařízení Windows pouze v případě, že jejich přístupu k aplikaci z důvěryhodných podnikové síti. Také se vyloučí nouzový účtů a správců core z těchto omezení. Cíloví uživatelé pak získávat přístup k Exchangi Online a SharePoint Online, zatímco jiné nebudou mít uživatelé dál přístup k aplikacím z důvodu výpadek. V tomto příkladu bude vyžadovat umístění v síti s názvem **CorpNetwork** a skupinu zabezpečení **ContingencyAccess** s cílových uživatelů s názvem skupiny **CoreAdmins** s Správci jádra a skupina s názvem **EmergencyAccess** s účty pro nouzový přístup. Pohotovostní vyžaduje čtyři zásady a zajistit tak požadovaný přístup. 

**Příklad A – řešení nepředvídaných událostí certifikační Autority zásad obnovit přístup k životně důležitého spolupráci aplikací:**

* Zásady 1: Vyžadovat zařízení připojené k doméně pro Exchange a SharePoint
  * Název: EM001 - POVOLIT NOUZOVÉ: MFA přerušení [1/4] - Exchange SharePoint - vyžadovala připojení k hybridní službě Azure AD
  * Uživatelé a skupiny: Zahrnout ContingencyAccess. Vyloučit CoreAdmins a EmergencyAccess
  * Cloudové aplikace: Exchange Online a SharePoint Online
  * Podmínky: Všechny
  * Udělit řízení: Vyžadovat připojených k doméně
  * Stav: Zakázáno
* Zásady 2: Blok jiných platformách než Windows
  * Název: EM002 - POVOLIT NOUZOVÉ: Přerušení MFA [2/4] - Exchange SharePoint – zablokuje přístup s výjimkou Windows
  * Uživatelé a skupiny: Zahrňte všechny uživatele. Vyloučit CoreAdmins a EmergencyAccess
  * Cloudové aplikace: Exchange Online a SharePoint Online
  * Podmínky: Platforma zahrnout všechny platformy zařízení, vyloučit Windows
  * Udělit řízení: Zablokovat
  * Stav: Zakázáno
* Zásady 3: Sítí bloku než CorpNetwork
  * Název: EM003 - POVOLIT NOUZOVÉ: Přerušení MFA [3/4] - Exchange SharePoint – zablokuje přístup s výjimkou podnikové sítě
  * Uživatelé a skupiny: Zahrňte všechny uživatele. Vyloučit CoreAdmins a EmergencyAccess
  * Cloudové aplikace: Exchange Online a SharePoint Online
  * Podmínky: Umístění obsahovat jakékoli umístění, vyloučit CorpNetwork
  * Udělit řízení: Zablokovat
  * Stav: Zakázáno
* Zásady 4: Explicitně Block EAS
  * Název: EM004 - POVOLIT NOUZOVÉ: MFA přerušení [4/4] - Exchange - Block EAS pro všechny uživatele
  * Uživatelé a skupiny: Zahrnout všechny uživatele
  * Cloudové aplikace: Zahrnout Exchange Online
  * Podmínky: Klientské aplikace: Protokolu Exchange Active Sync
  * Udělit řízení: Zablokovat
  * Stav: Zakázáno

Pořadí aktivace:

1. Vylučte ContingencyAccess CoreAdmins a EmergencyAccess z existujících zásad vícefaktorového ověřování. Ověřte, že uživatel v ContingencyAccess můžete přistupovat k Sharepointu Online a Exchange Online.
2. Povolte zásady 1: Ověřte, že uživatelé na zařízeních připojených k doméně, kteří nejsou v vyloučení skupin budou mít přístup k Exchangi Online a SharePoint Online. Ověřte, že uživatelé ve skupině pro vyloučení můžete přístup k Sharepointu Online a Exchange z libovolného zařízení.
3. Povolte zásady 2: Ověřte, že uživatelé, kteří nejsou ve skupině pro vyloučení nemůže dostat k Sharepointu Online a Exchange Online ze svých mobilních zařízení. Ověřte, že uživatelé ve skupině pro vyloučení můžete přístup k Sharepointu a Exchange z libovolného zařízení (Windows/iOS/Android).
4. Povolte zásady 3: Ověření uživatelé, kteří nejsou v vyloučení skupin nemá přístup k Sharepointu a Exchange mimo podnikovou síť, i s doménou připojená k počítači. Ověřte, že uživatelé ve skupině pro vyloučení můžete přístup k Sharepointu a Exchange z libovolné sítě.
5. Povolte zásady 4: Ověřte, že všichni uživatelé nemůže získat Exchangi Online z nativní poštovní aplikace na mobilních zařízeních.
6. Zakážete existující zásady vícefaktorového ověřování pro SharePoint Online a Exchange Online.

V tomto příkladu Další **příklad B - zásad podmíněného přístupu řešení nepředvídaných událostí povolit mobilní přístup k Salesforce**, obchodní aplikace přístup se obnoví. V tomto scénáři nastavení zákazník obvykle vyžaduje jejich zaměstnanci prodeje přístup k Salesforce (nakonfigurovanou pro jednotné přihlašování v Azure AD) z mobilních zařízení a povolený jenom z kompatibilních zařízení. Narušení v tomto případě je, že se vyskytl problém s vyhodnocování dodržování předpisů zařízením a výpadek dochází po jednom citlivé kde prodejní tým musí přístup k Salesforce zavřete obchody. Tyto zásady řešení nepředvídaných událostí bude důležité uživatelům udělit přístup k Salesforce z mobilního zařízení, aby mohl pokračovat zavřete zakázek a nemá žádný vliv na podnikání. V tomto příkladu **SalesforceContingency** obsahuje všechny prodejní zaměstnance, kteří potřebují přístup a **SalesAdmins** obsahuje nezbytné správci služby Salesforce.

**Příklad B - řešení nepředvídaných událostí certifikační Autority zásad:**

* Zásady 1: Blokovat všem není v týmu SalesContingency
  * Název: EM001 - POVOLIT NOUZOVÉ: Narušení dodržování předpisů zařízení [1/2] - Salesforce – blokovat všechny uživatele kromě SalesforceContingency
  * Uživatelé a skupiny: Zahrňte všechny uživatele. Vyloučit SalesAdmins a SalesforceContingency
  * Cloudové aplikace: Salesforce.
  * Podmínky: Žádný
  * Udělit řízení: Zablokovat
  * Stav: Zakázáno
* Zásady 2: Blokovat prodejního týmu z jakékoli platformy, než mobilní zařízení (ke snížení plochy útoku)
  * Název: EM002 - POVOLIT NOUZOVÉ: Narušení dodržování předpisů zařízení [2/2] - Salesforce – blokovat všechny platformy kromě zařízení s iOS a Android
  * Uživatelé a skupiny: Zahrnout SalesforceContingency. Vyloučit SalesAdmins
  * Cloudové aplikace: Salesforce
  * Podmínky: Vyloučit platformy zahrnout všechny platformy zařízení, zařízení s iOS a Android
  * Udělit řízení: Zablokovat
  * Stav: Zakázáno

Pořadí aktivace:

1. Vylučte SalesAdmins a SalesforceContingency ze stávající zásady dodržování předpisů zařízení pro služby Salesforce. Ověřte, že uživatel ve skupině SalesforceContingency můžete přistupovat k Salesforce.
2. Povolte zásady 1: Ověřte, že uživatelé mimo SalesContingency nemají přístup k Salesforce. Ověřování uživatelů v SalesAdmins a SalesforceContingency přístup k Salesforce.
3. Povolte zásady 2: Ověření uživatelů ve skupině SalesContingency nemohou přistupovat k Salesforce z jejich přenosné počítače Windows nebo Mac, ale můžete pořád přístup z mobilních zařízení. Ověřte, že SalesAdmin můžete pořád přístup k Salesforce z libovolného zařízení.
4. Zakážete existující zásady dodržování předpisů zařízením pro Salesforce.

### <a name="deploy-password-hash-sync-even-if-you-are-federated-or-use-pass-through-authentication"></a>Nasadit synchronizace hodnot hash hesel, i když jsou federované nebo pomocí předávacího ověřování

Uzamčení uživatelů může také dojít, pokud jsou splněny následující podmínky:

- Vaše organizace používá řešení s hybridní identitou předávací ověřování nebo federace.
- Vaše místní systémy identit (například služby Active Directory, služby AD FS nebo závislá komponenta) nejsou k dispozici. 
 
Bude odolnější, musí vaše organizace [povolení synchronizace hodnot hash hesel](https://docs.microsoft.com/azure/security/azure-ad-choose-authn), protože umožňuje [přejít k používání synchronizace hodnot hash hesel](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-user-signin) Pokud vašich místních systémů identit jsou vypnuté.

#### <a name="microsoft-recommendations"></a>Doporučení Microsoftu
 Povolení synchronizace hodnot hash hesel pomocí Průvodce Azure AD Connect, bez ohledu na to, jestli vaše organizace používá federaci nebo předávací ověřování.

>[!IMPORTANT]
> Není potřeba a převést uživatele z federovaných na spravované ověřování používat synchronizace hodnot hash hesel.

## <a name="during-a-disruption"></a>Během přerušení

Když jste se rozhodli pro implementaci plán na omezení rizik, bude moci automaticky byly zachovány při přerušení jednoho přístupového ovládacího prvku. Ale pokud jste se rozhodli vytvořit plán řešení nepředvídaných událostí, vám bude moci uživatel aktivovat řešení nepředvídaných událostí zásady během výpadků řízení přístupu:

1. Povolení řešení nepředvídaných událostí zásady, které udělují cíloví uživatelé, přístup ke konkrétním aplikacím z konkrétní sítě.
2. Zakážete regulární zásady založené na ovládacím prvku.

### <a name="microsoft-recommendations"></a>Doporučení Microsoftu

V závislosti na tom, které jejich zmírnění nebo pojistné události se používají při přerušení může vaše organizace udělení přístupu jenom hesel. Žádná ochrana je značné riziko zabezpečení, který musí být pečlivě zvážit. Organizace musí:

1. Jako součást vaší strategie řízení změn zdokumentujte každé změně a předchozího stavu, abyste mohli vrátit zpět všechny pojistné události, které jste implementovali jako ovládací prvky přístupu jsou plně funkční.
2. Předpokládejme, že útočníky pokusí získávání hesel heslo zařízení nebo phishingovým útokům zakázáno vícefaktorové ověřování. Navíc nesprávnými účastníky již můžete mít hesel, která se dříve nezajistila přístup k jakémukoli prostředku, který můžete se pokusit během tohoto časového období. Pro kritické uživatele jako třeba vedoucí pracovníky můžete toto riziko snížit částečně resetováním hesla před deaktivací vícefaktorové ověřování pro ně.
3. Archivujte všech aktivit přihlašování identifikovat, kdo co přístup po dobu, kdy byl zakázán vícefaktorové ověřování.
4. [Třídění všechny rizikové události hlásí](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins) během tohoto časového období.

## <a name="after-a-disruption"></a>Po přerušení

Vrátit zpět změny provedené v rámci aktivované plán řešení nepředvídaných událostí po obnovení služby, která způsobila narušení. 

1. Povolení regulární zásad
2. Zakážete řešení nepředvídaných událostí zásady. 
3. Vrátit zpět změny provedené a uvádí během narušení.
4. Pokud jste použili účet pro nouzový přístup, nezapomeňte znovu vygenerovat přihlašovací údaje a fyzicky zabezpečené nové podrobnosti přihlašovací údaje jako součást vašich procedur nouzový přístup účtu.
5. I nadále [třídění všechny rizikové události hlásí](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins) po přerušení pro podezřelé aktivity.
6. Odvolat všechny obnovovací tokeny, které byly vydány [pomocí prostředí PowerShell](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0) cílit na skupinu uživatelů. Odvolání všechny obnovovací tokeny je důležité pro privilegované účty používané během narušení a teď už vynutí jejich donutit a kontrolu nad obnovené zásady.

## <a name="emergency-options"></a>Nouzový možnosti

 V případě nouze a vaše organizace dříve implementovat omezení rizik nebo plán řešení nepředvídaných událostí, postupujte podle doporučení v [pojistné události pro uzamčení uživatelů](#contingencies-for-user-lockout) části, pokud již používají podmíněný přístup zásad pro vynucování vícefaktorového ověřování.
Pokud vaše organizace používá starší verzi zásad MFA na uživatele, můžete zvážit následující alternativní:

1. Pokud máte podnikové síti odchozí IP adresa, můžete je přidat jako důvěryhodné IP adresy, pokud chcete povolit ověřování pouze k podnikové síti.
 2. Pokud nemáte inventáře odchozí IP adresy nebo vyžaduje pro povolení přístupu uvnitř i mimo podnikovou síť, můžete přidat celého adresního prostoru IPv4 jako důvěryhodné IP adresy zadáním 0.0.0.0/1 a 128.0.0.0/1.

>[!IMPORTANT]
 > Pokud můžete rozšířit důvěryhodné IP adresy pro odblokování přístupu, nebude vygenerováno rizikové události přidružené IP adresy (například neuskutečnitelné cesty nebo neznámých míst).

>[!NOTE]
 > Konfigurace [důvěryhodné IP adresy](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-mfasettings) pro Azure MFA je k dispozici pouze [licence Azure AD Premium](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-licensing).

## <a name="learn-more"></a>Další informace

* [Dokumentace ke službě Azure AD Authentication](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-iis)
* [Spravovat účty pro správu nouzovou přístup ve službě Azure AD](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-emergency-access)
* [Konfigurace pojmenovaných umístění ve službě Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)
 * [Set-MsolDomainFederationSettings](https://docs.microsoft.com/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0)
* [Jak nakonfigurovat zařízení Azure Active Directory připojená k hybridní](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-plan)
* [Průvodce nasazením Windows Hello pro firmy](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-deployment-guide)
 * [Heslo – doprovodné materiály týmem Microsoft Research.](https://research.microsoft.com/pubs/265143/microsoft_password_guidance.pdf)
* [Jaké jsou podmínky podmíněného přístupu Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/conditions)
* [Co jsou řízení přístupu v Azure Active Directory podmíněného přístupu?](https://docs.microsoft.com/azure/active-directory/conditional-access/controls)
