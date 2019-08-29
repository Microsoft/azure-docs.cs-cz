---
title: Vytvoření strategie odolného řízení přístupu – Azure Active Directory
description: Tento dokument obsahuje pokyny k strategiím, které by organizace měla přijmout pro zajištění odolnosti proti riziku uzamčení během nepředvídatelných výpadků.
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 12/19/2018
ms.author: martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: 675e970bbdaeb035273eb87394dda610e070aa39
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70125114"
---
# <a name="create-a-resilient-access-control-management-strategy-with-azure-active-directory"></a>Vytvoření odolné strategie správy řízení přístupu pomocí Azure Active Directory

>[!NOTE]
> Informace obsažené v tomto dokumentu představují aktuální pohled společnosti Microsoft Corporation na problémy, které jsou popsány k datu publikování. Vzhledem k tomu, že Microsoft musí reagovat na měnící se podmínky na trhu, neměl by být interpretován jako závazek společnosti Microsoft a společnost Microsoft nemůže zaručit přesnost všech informací, které jsou uvedeny po datu publikování.

Organizace, které spoléhají na jeden řízení přístupu, jako je Multi-Factor Authentication (MFA) nebo jediné síťové umístění, jsou pro zabezpečení svých systémů IT náchylné k selhání přístupu ke svým aplikacím a prostředkům, pokud je tato jediná kontrola přístupu nedostupná. nebo nesprávně nakonfigurované. Přírodní havárie může například způsobit nedostupnost velkých segmentů telekomunikačních infrastruktur nebo podnikových sítí. Takové přerušení by mohlo zabránit tomu, aby se koncoví uživatelé a správci mohli přihlásit.

Tento dokument obsahuje pokyny k strategiím, které by organizace měla přijmout pro zajištění odolnosti proti riziku uzamčení během nepředvídatelného narušení, a to v následujících případech:

 1. Organizace mohou zvýšit jejich odolnost a snížit tak riziko uzamčení **před přerušením** implementací strategií zmírnění nebo pohotovostních plánů.
 2. Organizace můžou dál přistupovat k aplikacím a prostředkům, které si vyberou **během přerušení** , tím, že budou mít strategie zmírnění a pohotovostní plány na místě.
 3. Organizace by se měli ujistit, že uchovávají informace, jako jsou protokoly, **po přerušení** a předtím, než vrátí případné nepotřebné okolnosti.
 4. Organizace, které neimplementovaly strategie prevence nebo alternativní plány, můžou implementovat **nouzové možnosti** , které se zabývají přerušením.

## <a name="key-guidance"></a>Klíčové pokyny

V tomto dokumentu jsou čtyři klíčové poznatky:

* Vyhněte se uzamknutí správce pomocí účtů pro nouzový přístup.
* Implementujte vícefaktorové ověřování pomocí podmíněného přístupu (CA), nikoli MFA pro uživatele.
* Zmírnění uzamčení uživatelů pomocí více ovládacích prvků podmíněného přístupu (CA).
* Omezení uzamčení uživatelů tím, že zřizujete více metod ověřování nebo ekvivalenty pro každého uživatele.

## <a name="before-a-disruption"></a>Před přerušením

Zmírnění skutečného přerušení musí být hlavním cílem organizace při řešení problémů s řízením přístupu, ke kterým může dojít. Omezení rizik zahrnuje plánování skutečné události a implementaci strategií, aby se zajistilo, že řízení přístupu a operace nebudou při přerušení ovlivněny.

### <a name="why-do-you-need-resilient-access-control"></a>Proč potřebujete odolnější řízení přístupu?

 Identita je řídicí rovina uživatelů, kteří přistupují k aplikacím a prostředkům. Váš systém identit řídí, kteří uživatelé a za jakých podmínek, například řízení přístupu nebo požadavky na ověřování, uživatelé získají přístup k aplikacím. V případě, že jeden nebo více požadavků na ověření nebo řízení přístupu není k dispozici pro uživatele, kteří se budou moci ověřit z důvodu neočekávaných okolností, mohou organizace zaznamenat jeden nebo oba následující problémy:

* **Uzamčení správce:** Správci nemůžou spravovat tenanta ani služby.
* **Uzamčení uživatele:** Uživatelé nemají přístup k aplikacím nebo prostředkům.

### <a name="administrator-lockout-contingency"></a>Záhotovost uzamčení správce

Chcete-li odemknout přístup správce k vašemu tenantovi, měli byste vytvořit účty pro nouzový přístup. Tyto účty pro nouzový přístup, označované také jako účty pro objednání, umožňují přístup ke správě konfigurace služby Azure AD, když nejsou k dispozici normální přístupové procedury privilegovaného účtu. Po doporučeních pro [účet pro nouzový přístup]( https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-emergency-access)by se měly vytvořit aspoň dva účty pro nouzový přístup.

### <a name="mitigating-user-lockout"></a>Zmírnění uzamčení uživatele

 Pokud chcete zmírnit riziko uzamknutí uživatelů, použijte zásady podmíněného přístupu s několika ovládacími prvky, které uživatelům umožní zvolit způsob, jakým budou mít přístup k aplikacím a prostředkům. Když uživateli vyberete možnost volby, například přihlašování pomocí MFA **nebo** přihlášení ze spravovaného zařízení **nebo** přihlášení z podnikové sítě, pokud je jedna z ovládacích prvků přístupu nedostupná, má uživatel další možnosti, jak pokračovat v práci.

#### <a name="microsoft-recommendations"></a>Doporučení Microsoftu

Do stávajících zásad podmíněného přístupu pro organizaci zahrňte následující řízení přístupu:

1. Zřizování více metod ověřování pro každého uživatele, který spoléhá na různé komunikační kanály, například na aplikaci Microsoft Authenticator (Internet), token OATH (generovaný na zařízení) a SMS (Telephonic).
2. Nasaďte Windows Hello pro firmy na zařízeních s Windows 10, abyste vyhověli požadavkům na MFA přímo ze zařízení pro přihlášení.
3. Použijte důvěryhodná zařízení přes [hybridní připojení Azure AD](https://docs.microsoft.com/azure/active-directory/devices/overview) nebo [Microsoft Intune spravovaná zařízení](https://docs.microsoft.com/intune/planning-guide). Důvěryhodná zařízení vylepšit uživatelské prostředí, protože vlastní důvěryhodné zařízení může splnit požadavky zásad silného ověřování, aniž by museli uživateli vyvolávat výzvu MFA. Vícefaktorové ověřování se pak bude vyžadovat při registraci nového zařízení a při přístupu k aplikacím nebo prostředkům z nedůvěryhodných zařízení.
4. Využijte zásady založené na riziku služby Azure AD Identity Protection, které zabraňují v přístupu, když se uživatel nebo přihlašování nejedná o riziko pevně stanovených zásad MFA.

>[!NOTE]
> Zásady založené na rizicích vyžadují [Azure AD Premium licence P2](https://azure.microsoft.com/pricing/details/active-directory/) .

Následující příklad popisuje zásady, které je třeba vytvořit, aby pro uživatele poskytovaly odolné řízení přístupu pro přístup k jejich aplikacím a prostředkům. V tomto příkladu budete potřebovat skupinu zabezpečení **AppUsers** s cílovými uživateli, kterým chcete udělit přístup, skupině s názvem **CoreAdmins** s hlavními správci a skupiny s názvem **EmergencyAccess** s účty pro nouzový přístup.
Tato ukázková sada zásad uděluje vybraným uživatelům v **AppUsers**, přístup k vybraným aplikacím, pokud se připojují z důvěryhodného zařízení nebo poskytuje silné ověřování, například MFA. Vyloučí účty v nouzi a základní správce.

**Sada zásad pro zmírnění rizik CA:**

* Zásada 1: Zablokovat přístup lidem mimo cílové skupiny
  * Uživatelé a skupiny: Zahrňte všechny uživatele. Vyloučení AppUsers, CoreAdmins a EmergencyAccess
  * Cloudové aplikace: Zahrnout všechny aplikace
  * Stavu NTato
  * Udělit řízení: Zablokovat
* Zásada 2: Udělte přístup k AppUsers vyžadujícímu MFA nebo důvěryhodnému zařízení.
  * Uživatelé a skupiny: Zahrnout AppUsers Vyloučit CoreAdmins a EmergencyAccess
  * Cloudové aplikace: Zahrnout všechny aplikace
  * Stavu NTato
  * Udělit řízení: Udělit přístup, vyžadovat vícefaktorové ověřování, vyžadovat, aby zařízení splňovalo předpisy. Pro více ovládacích prvků: Vyžadovat jeden z vybraných ovládacích prvků.

### <a name="contingencies-for-user-lockout"></a>Nepředvídané události pro uzamčení uživatele

Případně může vaše organizace také vytvářet pohotovostní zásady. Pokud chcete vytvořit pohotovostní zásady, musíte definovat kritéria kompromisů mezi provozní kontinuitou, provozními náklady, finančními náklady a bezpečnostními riziky. Můžete například aktivovat pohotovostní zásadu pouze pro podmnožinu uživatelů, pro podmnožinu aplikací, pro podmnožinu klientů nebo z podmnožiny umístění. Pohotovostní zásady poskytnou správcům a koncovým uživatelům přístup k aplikacím a prostředkům během přerušení, kdy nebyla implementována žádná metoda zmírnění.
Porozumění vaší expozici během přerušení pomáhá snížit vaše riziko a je zásadní součástí procesu plánování. Pokud chcete vytvořit svůj pohotovostní plán, nejdřív určete následující obchodní požadavky vaší organizace:

1. Určení nejdůležitějších aplikací, které jsou v provozu předem: Jaké jsou aplikace, pro které musíte poskytnout přístup, a to i s nižším rizikem a stav zabezpečení? Sestavte seznam těchto aplikací a zajistěte, aby ostatní účastníci (podnikání, zabezpečení, právní, vedoucí) souhlasili s tím, že pokud všechny řízení přístupu vzroste, musí tyto aplikace nadále běžet. Pravděpodobně budete končit kategoriemi:
   * **Kategorie 1 má důležité aplikace** , které nemůžou být dostupné déle než několik minut, například aplikace, které přímo ovlivňují tržby organizace.
   * **Kategorie 2: důležité aplikace** , ke kterým musí být podnik přístupný během pár hodin.
   * **Kategorie 3 aplikace s nízkou prioritou** , které můžou vydržet přerušení několika dní.
2. Pro aplikace v kategorii 1 a 2 Společnost Microsoft doporučuje předem naplánovat, jaký typ úrovně přístupu chcete udělit:
   * Chcete povolit úplný přístup nebo jenom omezenou relaci, jako je třeba omezit stahování?
   * Chcete povolit přístup k části aplikace, ale ne k celé aplikaci?
   * Chcete povolit přístup k informačnímu pracovníkovi a zablokovat přístup správce, dokud se neobnoví řízení přístupu?
3. Pro tyto aplikace Microsoft také doporučuje plánování, které cesty vedoucí přístupu záměrně otevřete a které budete uzavřít:
   * Chcete povolit, aby prohlížeč měl přístup jenom k blokovaným klientům, kteří můžou ukládat offline data?
   * Chcete povolit přístup jenom uživatelům v podnikové síti a zablokovat si ho i externí uživatelé?
   * Chcete povolit přístup z určitých zemí nebo oblastí pouze během přerušení?
   * Chcete zásady pro pohotovostní zásady, zejména pro kritické aplikace, neúspěšné nebo úspěšné, pokud není k dispozici alternativní řízení přístupu?

#### <a name="microsoft-recommendations"></a>Doporučení Microsoftu

Pohotovostní zásada podmíněného přístupu je **zakázaná zásada** , která nezahrnuje Azure MFA, vícefaktorové ověřování od jiných výrobců nebo řízení na základě zařízení. Pak, když se vaše organizace rozhodne aktivovat svůj pohotovostní plán, správci můžou zásadu Povolit a zakázat běžné zásady založené na řízení.

>[!IMPORTANT]
> Zakázáním zásad, které vynutily zabezpečení pro vaše uživatele, dojde k omezení stav zabezpečení i v případě, že je plán pohotovostní.

* Nakonfigurujte sadu záložních zásad, pokud dojde k výpadku jednoho typu přihlašovacích údajů nebo jednoho mechanismu řízení přístupu, který má vliv na přístup k vašim aplikacím. Nakonfigurujte zásady v zakázaném stavu, který vyžaduje připojení k doméně jako řízení, jako zálohu aktivní zásady, která vyžaduje poskytovatele vícefaktorového ověřování od jiného výrobce.
* Pomocí postupů uvedených v dokumentu White Paper s [pokyny](https://aka.ms/passwordguidance) k heslům snížíte riziko chybných aktérů, které se týkají pokusů o hesla.
* Nasaďte [Samoobslužné resetování hesel Azure AD (SSPR)](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr) a [Azure AD Password Protection](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-ban-bad-on-premises-deploy) , abyste se ujistili, že uživatelé nepoužívají běžné heslo a výrazy, které se rozhodnete zakázat.
* Používejte zásady, které omezují přístup v rámci aplikací, Pokud nedosáhnete určité úrovně ověřování, místo toho, abyste museli jednoduše vracet přístup k úplnému přístupu. Příklad:
  * Nakonfigurujte zásady zálohování, které odesílají deklaraci omezené relace na Exchange a SharePoint.
  * Pokud vaše organizace používá Microsoft Cloud App Security, zvažte návrat k zásadám, které MCAS a pak MCAS povolí přístup jen pro čtení, ale ne nahrávání.
* Pojmenujte zásady, abyste se ujistili, že je budete moct snadno najít při přerušení. Do názvu zásady zahrňte tyto prvky:
  * *Číslo popisku* pro zásadu.
  * Text, který se má zobrazit, tato zásada je určena pouze pro mimořádné události. Příklad: **POVOLIT V NOUZI**
  * *Přerušení* , ke kterému se vztahuje. Příklad: **Při přerušení MFA**
  * *Pořadové číslo* pro zobrazení pořadí, v jakém je nutné zásady aktivovat.
  * *Aplikace* , na které se vztahuje
  * *Ovládací prvky* , které budou použity.
  * *Podmínky* , které vyžaduje.
  
Tato standardní pojmenování pro pohotovostní zásady bude následující: 

```
EMnnn - ENABLE IN EMERGENCY: [Disruption][i/n] - [Apps] - [Controls] [Conditions]
```

Následující příklad: **Příkladem nepostradatelné zásady certifikační autority pro obnovení přístupu k důležitým aplikacím pro spolupráci**je typický podnikový pohotovostní. V tomto scénáři organizace obvykle vyžaduje MFA pro všechna přístup k Exchangi Online a SharePointu Online a přerušení v tomto případě je tím, že poskytovatel MFA pro zákazníka má výpadek (ať už jde o Azure MFA, místní poskytovatel MFA nebo vícefaktorové ověřování třetí strany). Tato zásada tento výpadek omezuje tím, že umožňuje konkrétním cílovým uživatelům přistupovat k těmto aplikacím z důvěryhodných zařízení s Windows jenom v případě, že k aplikaci přistupuje z důvěryhodné podnikové sítě. Z těchto omezení taky vyloučí účty v nouzi a základní správce. Cíloví uživatelé pak získají přístup k Exchangi Online a SharePointu Online, zatímco jiní uživatelé stále nebudou mít přístup k aplikacím z důvodu výpadku. Tento příklad bude vyžadovat pojmenované síťové umístění **CorpNetwork** a skupinu zabezpečení **ContingencyAccess** s cílovými uživateli, skupinou s názvem **CoreAdmins** a základními správci a skupinou s názvem **EmergencyAccess** with. účty pro nouzový přístup. Pohotovostní modul vyžaduje pro poskytnutí požadovaného přístupu čtyři zásady. 

**Příklad zásad certifikační autority s pohotovostním přístupem pro obnovení přístupu ke klíčovým aplikacím pro spolupráci:**

* Zásada 1: Vyžadovat zařízení připojená k doméně pro Exchange a SharePoint
  * Název: EM001 – POVOLENÍ V NOUZI: Přerušení MFA [1/4]-Exchange SharePoint – vyžaduje připojení k hybridní službě Azure AD.
  * Uživatelé a skupiny: Zahrnout ContingencyAccess Vyloučit CoreAdmins a EmergencyAccess
  * Cloudové aplikace: Exchange Online a SharePoint Online
  * Stavu Any
  * Udělit řízení: Vyžadovat připojené k doméně
  * Stav: Zakázáno
* Zásada 2: Blokovat jiné platformy než Windows
  * Název: EM002 – POVOLENÍ V NOUZI: Přerušení vícefaktorového ověřování [2/4]-Exchange SharePoint – blokovat přístup s výjimkou Windows
  * Uživatelé a skupiny: Zahrňte všechny uživatele. Vyloučit CoreAdmins a EmergencyAccess
  * Cloudové aplikace: Exchange Online a SharePoint Online
  * Stavu Platforma zařízení zahrnuje všechny platformy, vyloučení Windows
  * Udělit řízení: Zablokovat
  * Stav: Zakázáno
* Zásada 3: Blokovat jiné sítě než CorpNetwork
  * Název: EM003 – POVOLENÍ V NOUZI: Přerušení vícefaktorového ověřování [3/4]-Exchange SharePoint – blokování přístupu s výjimkou podnikové sítě
  * Uživatelé a skupiny: Zahrňte všechny uživatele. Vyloučit CoreAdmins a EmergencyAccess
  * Cloudové aplikace: Exchange Online a SharePoint Online
  * Stavu Umístění zahrnují všechna umístění, vyloučení CorpNetwork
  * Udělit řízení: Zablokovat
  * Stav: Zakázáno
* Zásada 4: Explicitní blokování EAS
  * Název: EM004 – POVOLENÍ V NOUZI: Přerušení MFA [4/4]-Exchange-Block EAS pro všechny uživatele
  * Uživatelé a skupiny: Zahrnout všechny uživatele
  * Cloudové aplikace: Zahrnout Exchange Online
  * Stavu Klientské aplikace: Exchange Active Sync
  * Udělit řízení: Zablokovat
  * Stav: Zakázáno

Pořadí aktivace:

1. Vylučte z existujících zásad MFA ContingencyAccess, CoreAdmins a EmergencyAccess. Ověřte, že uživatel v ContingencyAccess má přístup k SharePointu Online a Exchange Online.
2. Povolit zásadu 1: Ověřte, jestli uživatelé na zařízeních připojených k doméně, kteří nejsou ve skupinách vyloučení, mají přístup k Exchangi Online a SharePointu Online. Ověřte, že uživatelé ve skupině vyloučení mají přístup k SharePointu Online a Exchange z libovolného zařízení.
3. Povolit zásadu 2: Ověřte, že uživatelé, kteří nejsou ve skupině vyloučení, nemůžou získat přístup k SharePointu Online a Exchange Online ze svých mobilních zařízení. Ověřte, že uživatelé ve skupině vyloučení mají přístup k SharePointu a Exchange z libovolného zařízení (Windows/iOS/Android).
4. Povolit zásadu 3: Ověřte, že uživatelé, kteří nejsou ve skupinách vyloučení, nemají přístup k SharePointu a Exchangi mimo podnikovou síť, a to ani na počítači připojeném k doméně. Ověřte, že uživatelé ve skupině vyloučení mají přístup k SharePointu a Exchange z libovolné sítě.
5. Povolit zásadu 4: Ověřte, že všichni uživatelé nemůžou na mobilních zařízeních získat Exchange Online z nativních e-mailových aplikací.
6. Zakažte stávající zásady vícefaktorového ověřování pro SharePoint Online a Exchange Online.

V tomto dalším příkladu **příkladem B-pohotovostní zásady certifikační autority, které umožňují mobilní přístup k Salesforce**, se obnoví přístup obchodní aplikace. V tomto scénáři zákazník obvykle vyžaduje, aby jejich zaměstnanci prodeje měli přístup k Salesforce (nakonfigurovanému pro jednotné přihlašování pomocí Azure AD) z mobilních zařízení, aby je bylo možné povolit jenom ze zařízení dodržujících předpisy. Přerušení v tomto případě znamená, že došlo k potížím při vyhodnocování dodržování předpisů zařízením a výpadek se děje v citlivé době, kdy prodejní tým potřebuje přístup k Salesforce a uzavřít obchody. Tyto pohotovostní zásady udělí důležitému uživateli přístup k Salesforce z mobilního zařízení, aby mohli dál uzavírat obchody a Nerušit činnost podniku. V tomto příkladu **SalesforceContingency** obsahuje všechny zaměstnance prodejů, kteří potřebují zachovat přístup a **SalesAdmins** obsahuje potřebné Správce Salesforce.

**Příklad B-pohotovostní zásady certifikační autority:**

* Zásada 1: Blokovat všem, kteří nejsou v týmu SalesContingency
  * Název: EM001 – POVOLENÍ V NOUZI: Přerušení dodržování předpisů zařízením [1/2]-Salesforce – zablokuje všechny uživatele s výjimkou SalesforceContingency
  * Uživatelé a skupiny: Zahrňte všechny uživatele. Vyloučit SalesAdmins a SalesforceContingency
  * Cloudové aplikace: Produktu.
  * Stavu Žádné
  * Udělit řízení: Zablokovat
  * Stav: Zakázáno
* Zásada 2: Blokování prodejního týmu z jakékoli jiné platformy než mobilní (pro omezení oblasti útoku)
  * Název: EM002 – POVOLENÍ V NOUZI: Přerušení dodržování předpisů zařízením [2/2]-Salesforce – zablokuje všechny platformy kromě iOS a Androidu.
  * Uživatelé a skupiny: Zahrnout SalesforceContingency Vyloučit SalesAdmins
  * Cloudové aplikace: Salesforce
  * Stavu Platforma zařízení zahrnuje všechny platformy, vyloučení iOS a Androidu.
  * Udělit řízení: Zablokovat
  * Stav: Zakázáno

Pořadí aktivace:

1. Vylučte SalesAdmins a SalesforceContingency z existujících zásad dodržování předpisů zařízeními pro Salesforce. Ověřte, že uživatel ve skupině SalesforceContingency má přístup k Salesforce.
2. Povolit zásadu 1: Ověřte, že uživatelé mimo SalesContingency nemají přístup k Salesforce. Ověřte, že uživatelé v SalesAdmins a SalesforceContingency mají přístup k Salesforce.
3. Povolit zásadu 2: Ověřte, že uživatelé ve skupině SalesContingency nemají přístup k Salesforce ze svých přenosných počítačů s Windows/Mac, ale mají stále přístup ze svých mobilních zařízení. Ověřte, že SalesAdmin má stále přístup k Salesforce z libovolného zařízení.
4. Zakažte stávající zásady dodržování předpisů zařízeními pro Salesforce.

### <a name="deploy-password-hash-sync-even-if-you-are-federated-or-use-pass-through-authentication"></a>Nasadit synchronizaci hodnot hash hesel i v případě, že jste federované nebo používáte předávací ověřování

K uzamčení uživatele může dojít také v případě, že jsou splněné následující podmínky:

- Vaše organizace používá řešení hybridní identity s předávacím ověřováním nebo federaci.
- Vaše místní systémy identit (například služba Active Directory, AD FS nebo závislá součást) nejsou k dispozici. 
 
Aby byla vaše organizace pružnější, měla by [Povolit synchronizaci hodnot hash hesel](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn), protože umožňuje [Přepnout na použití synchronizace hodnot hash hesel](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-user-signin) v případě, že vaše místní systémy identity nejsou funkční.

#### <a name="microsoft-recommendations"></a>Doporučení Microsoftu
 Povolte synchronizaci hodnot hash hesel pomocí Průvodce Azure AD Connect bez ohledu na to, jestli vaše organizace používá federaci nebo předávací ověřování.

>[!IMPORTANT]
> Pro použití synchronizace hodnot hash hesel není nutné převádět uživatele ze federovaného na spravované ověřování.

## <a name="during-a-disruption"></a>Při přerušení

Pokud jste se rozhodli pro implementaci plánu zmírnění rizik, budete moct automaticky přerušit jednotlivé přerušení řízení přístupu. Pokud jste se ale rozhodli vytvořit plán pro nepředvídané řešení, budete moci aktivovat své pohotovostní zásady během přerušení řízení přístupu:

1. Povolte své pohotovostní zásady, které udělí cílovým uživatelům přístup ke konkrétním aplikacím z konkrétních sítí.
2. Zakážete své běžné zásady založené na ovládacím prvku.

### <a name="microsoft-recommendations"></a>Doporučení Microsoftu

V závislosti na tom, jaké zmírnění nebo nečinnosti se při přerušení používají, může vaše organizace udělit přístup jenom s heslem. Žádná ochrana není značným bezpečnostním rizikem, které je třeba pečlivě zvážit. Organizace musí:

1. V rámci strategie řízení změn zdokumentujte každou změnu a předchozí stav, abyste mohli vrátit zpět všechny nedokončené okolnosti, jakmile jsou ovládací prvky přístupu plně funkční.
2. Předpokládá se, že se zlomyslné aktéry pokusí o vybírání hesel prostřednictvím útoků pomocí postřiku hesel nebo útoků phishing při zakázání MFA Chybné objekty actor mohou již mít hesla, která dříve neudělila přístup k jakémukoli prostředku, který lze během tohoto okna provést. U kritických uživatelů, jako jsou například vedoucí pracovníci, můžete toto riziko částečně zmírnit tím, že si resetujete jejich hesla, než je pro ně zakážete MFA.
3. Archivujte veškerou přihlašovací aktivitu a určete, kdo má přístup k čemu v době zakázání MFA.
4. [Třídění všech zjištění rizik hlášených](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins) v rámci tohoto okna.

## <a name="after-a-disruption"></a>Po přerušení

Po obnovení služby, která způsobila přerušení, vraťte změny, které jste provedli v rámci aktivovaného plánu řešení pro nepředvídané účely. 

1. Povolit běžné zásady
2. Zakažte zásady pro nepředvídané pracovní postupy. 
3. Vraťte všechny další změny, které jste provedli a popsali během přerušení.
4. Pokud jste použili účet pro nouzový přístup, nezapomeňte znovu vygenerovat přihlašovací údaje a fyzicky zabezpečit nové přihlašovací údaje v rámci postupů vašeho účtu pro nouzový přístup.
5. Pokračujte v [třídění všech zjištění rizik hlášených](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins) po přerušení podezřelé aktivity.
6. Odvolat všechny obnovovací tokeny, které byly vydány [pomocí prostředí PowerShell](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0) pro cílení na skupinu uživatelů. Odvolání všech aktualizačních tokenů je důležité pro privilegované účty používané při přerušení a v důsledku toho vynutí opětovné ověření a splnění kontroly nad obnovenými zásadami.

## <a name="emergency-options"></a>Nouzové možnosti

 V případě nouze a vaše organizace předtím neimplementovala plán zmírňování nebo řešení nepředvídaných událostí, pokud už používají zásady podmíněného přístupu k vymáhání MFA, postupujte podle doporučení v části neškodné volání [uživatele](#contingencies-for-user-lockout) .
Pokud vaše organizace používá starší zásady vícefaktorového ověřování pro uživatele, můžete zvážit následující alternativy:

1. Pokud máte odchozí IP adresu podnikové sítě, můžete je přidat jako důvěryhodné IP adresy, abyste mohli ověřování povolit jenom pro podnikovou síť.
   1. Pokud nemáte inventarizaci odchozích IP adres nebo potřebujete povolit přístup v podnikové síti i mimo ni, můžete přidat celý adresní prostor IPv4 jako důvěryhodné IP adresy zadáním 0.0.0.0/1 a 128.0.0.0/1.

>[!IMPORTANT]
 > Pokud rozpoznáváte důvěryhodné IP adresy na přístup odblokování, negenerují se detekce rizik přidružená k IP adresám (například nemožná cesta nebo neznámá umístění).

>[!NOTE]
 > Konfigurace [důvěryhodných IP adres](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-mfasettings) pro Azure MFA je dostupná jenom pro [licence Azure AD Premium](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-licensing).

## <a name="learn-more"></a>Víc se uč

* [Dokumentace k ověřování Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-iis)
* [Spravovat účty pro správu nouzovou přístup ve službě Azure AD](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-emergency-access)
* [Konfigurace pojmenovaných umístění v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)
  * [Set-MsolDomainFederationSettings](https://docs.microsoft.com/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0)
* [Jak nakonfigurovat zařízení připojená k hybridnímu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-plan)
* [Průvodce nasazením Windows Hello pro firmy](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-deployment-guide)
  * [Pokyny k heslu – Microsoft Research](https://research.microsoft.com/pubs/265143/microsoft_password_guidance.pdf)
* [Jaké jsou podmínky v Azure Active Directory podmíněný přístup?](https://docs.microsoft.com/azure/active-directory/conditional-access/conditions)
* [Co jsou ovládací prvky přístupu v Azure Active Directory podmíněný přístup?](https://docs.microsoft.com/azure/active-directory/conditional-access/controls)
