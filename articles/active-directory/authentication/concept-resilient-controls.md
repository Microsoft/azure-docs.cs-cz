---
title: Vytvoření odolné strategie správy řízení přístupu – Azure AD
description: Tento dokument obsahuje pokyny k strategiím, které by organizace měla přijmout pro zajištění odolnosti proti riziku uzamčení během nepředvídatelných výpadků.
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.workload: identity
ms.date: 06/08/2020
ms.author: martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: 95f70005f2c7f53833163dcd5f0d2ee89b3db37c
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2020
ms.locfileid: "96861285"
---
# <a name="create-a-resilient-access-control-management-strategy-with-azure-active-directory"></a>Vytvoření odolné strategie správy řízení přístupu pomocí Azure Active Directory

>[!NOTE]
> The information contained in this document represents the current view of Microsoft Corporation on the issues discussed as of the date of publication. Vzhledem k tomu, že Microsoft musí reagovat na měnící se podmínky na trhu, neměl by být interpretován jako závazek společnosti Microsoft a společnost Microsoft nemůže zaručit přesnost všech informací, které jsou uvedeny po datu publikování.

Organizace, které spoléhají na jeden ovládací prvek přístupu, jako je Multi-Factor Authentication (MFA) nebo jediné síťové umístění, jsou pro zabezpečení svých systémů IT náchylné k selhání přístupu k aplikacím a prostředkům, pokud dojde k nedostupnosti nebo chybně nakonfigurovanému řízení přístupu. Přírodní havárie může například způsobit nedostupnost velkých segmentů telekomunikačních infrastruktur nebo podnikových sítí. Takové přerušení by mohlo zabránit tomu, aby se koncoví uživatelé a správci mohli přihlásit.

Tento dokument obsahuje pokyny k strategiím, které by organizace měla přijmout pro zajištění odolnosti proti riziku uzamčení během nepředvídatelného narušení, a to v následujících případech:

 1. Organizace mohou zvýšit jejich odolnost a snížit tak riziko uzamčení **před přerušením** implementací strategií zmírnění nebo pohotovostních plánů.
 2. Organizace můžou dál přistupovat k aplikacím a prostředkům, které si vyberou **během přerušení** , tím, že budou mít strategie zmírnění a pohotovostní plány na místě.
 3. Organizace by se měli ujistit, že uchovávají informace, jako jsou protokoly,  **po přerušení** a předtím, než vrátí případné nepotřebné okolnosti.
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

Chcete-li odemknout přístup správce k vašemu tenantovi, měli byste vytvořit účty pro nouzový přístup. Tyto účty pro nouzový přístup, označované *také jako účty* pro objednání, umožňují přístup ke správě konfigurace služby Azure AD, když nejsou k dispozici normální přístupové procedury privilegovaného účtu. Po [doporučeních pro účet pro nouzový přístup]( ../users-groups-roles/directory-emergency-access.md)by se měly vytvořit aspoň dva účty pro nouzový přístup.

### <a name="mitigating-user-lockout"></a>Zmírnění uzamčení uživatele

 Pokud chcete zmírnit riziko uzamknutí uživatelů, použijte zásady podmíněného přístupu s několika ovládacími prvky, které uživatelům umožní zvolit způsob, jakým budou mít přístup k aplikacím a prostředkům. Když uživateli vyberete možnost volby, například přihlašování pomocí MFA **nebo** přihlášení ze spravovaného zařízení **nebo** přihlášení z podnikové sítě, pokud je jedna z ovládacích prvků přístupu nedostupná, má uživatel další možnosti, jak pokračovat v práci.

#### <a name="microsoft-recommendations"></a>Doporučení Microsoftu

Do stávajících zásad podmíněného přístupu pro organizaci zahrňte následující řízení přístupu:

1. Zřizování více metod ověřování pro každého uživatele, který spoléhá na různé komunikační kanály, například na aplikaci Microsoft Authenticator (Internet), token OATH (generovaný na zařízení) a SMS (Telephonic). Následující skript prostředí PowerShell vám pomůže určit předem, které další metody by měly uživatelé zaregistrovat: [skript pro analýzu metody ověřování Azure AD MFA](/samples/azure-samples/azure-mfa-authentication-method-analysis/azure-mfa-authentication-method-analysis/).
2. Nasaďte Windows Hello pro firmy na zařízeních s Windows 10, abyste vyhověli požadavkům na MFA přímo ze zařízení pro přihlášení.
3. Použijte důvěryhodná zařízení přes [hybridní připojení Azure AD](../devices/overview.md) nebo [Microsoft Intune spravovaná zařízení](/intune/planning-guide). Důvěryhodná zařízení vylepšit uživatelské prostředí, protože vlastní důvěryhodné zařízení může splnit požadavky zásad silného ověřování, aniž by museli uživateli vyvolávat výzvu MFA. Vícefaktorové ověřování se pak bude vyžadovat při registraci nového zařízení a při přístupu k aplikacím nebo prostředkům z nedůvěryhodných zařízení.
4. Využijte zásady založené na riziku služby Azure AD Identity Protection, které zabraňují v přístupu, když se uživatel nebo přihlašování nejedná o riziko pevně stanovených zásad MFA.
5. Pokud chráníte přístup k síti VPN pomocí rozšíření Azure AD MFA NPS, zvažte federování řešení VPN jako [aplikaci SAML](../manage-apps/view-applications-portal.md) a určete kategorii aplikace podle doporučení níže. 

>[!NOTE]
> Zásady založené na rizicích vyžadují [Azure AD Premium licence P2](https://azure.microsoft.com/pricing/details/active-directory/) .

Následující příklad popisuje zásady, které je třeba vytvořit, aby pro uživatele poskytovaly odolné řízení přístupu pro přístup k jejich aplikacím a prostředkům. V tomto příkladu budete potřebovat skupinu zabezpečení **AppUsers** s cílovými uživateli, kterým chcete udělit přístup, skupině s názvem **CoreAdmins** s hlavními správci a skupiny s názvem **EmergencyAccess** s účty pro nouzový přístup.
Tato ukázková sada zásad uděluje vybraným uživatelům v **AppUsers**, přístup k vybraným aplikacím, pokud se připojují z důvěryhodného zařízení nebo poskytuje silné ověřování, například MFA. Vyloučí účty v nouzi a základní správce.

**Sada zásad pro zmírnění rizik CA:**

* Zásady 1: blokování přístupu lidem mimo cílové skupiny
  * Uživatelé a skupiny: včetně všech uživatelů. Vyloučení AppUsers, CoreAdmins a EmergencyAccess
  * Cloudové aplikace: zahrnout všechny aplikace
  * Podmínky: (žádné)
  * Udělení řízení: blok
* Zásada 2: Udělte přístup k AppUsers vyžadujícímu MFA nebo důvěryhodnému zařízení.
  * Uživatelé a skupiny: zahrnují AppUsers. Vyloučit CoreAdmins a EmergencyAccess
  * Cloudové aplikace: zahrnout všechny aplikace
  * Podmínky: (žádné)
  * Udělit řízení: udělit přístup, vyžadovat službu Multi-Factor Authentication, vyžadovat, aby zařízení splňovalo předpisy. Pro více ovládacích prvků: vyžadovat jeden z vybraných ovládacích prvků.

### <a name="contingencies-for-user-lockout"></a>Nepředvídané události pro uzamčení uživatele

Případně může vaše organizace také vytvářet pohotovostní zásady. Pokud chcete vytvořit pohotovostní zásady, musíte definovat kritéria kompromisů mezi provozní kontinuitou, provozními náklady, finančními náklady a bezpečnostními riziky. Můžete například aktivovat pohotovostní zásadu pouze pro podmnožinu uživatelů, pro podmnožinu aplikací, pro podmnožinu klientů nebo z podmnožiny umístění. Pohotovostní zásady poskytnou správcům a koncovým uživatelům přístup k aplikacím a prostředkům během přerušení, kdy nebyla implementována žádná metoda zmírnění. Microsoft doporučuje povolit pohotovostní zásady v [režimu jenom pro sestavy](../conditional-access/howto-conditional-access-insights-reporting.md) , pokud se nepoužívá, takže správci můžou monitorovat potenciální dopad zásad, aby je bylo potřeba zapnout.

 Porozumění vaší expozici během přerušení pomáhá snížit vaše riziko a je zásadní součástí procesu plánování. Pokud chcete vytvořit svůj pohotovostní plán, nejdřív určete následující obchodní požadavky vaší organizace:

1. Určete nejdůležitější aplikace předem: Jaké jsou aplikace, pro které musíte poskytnout přístup, a to i s nižším rizikem a stav zabezpečení? Sestavte seznam těchto aplikací a zajistěte, aby ostatní účastníci (podnikání, zabezpečení, právní, vedoucí) souhlasili s tím, že pokud všechny řízení přístupu vzroste, musí tyto aplikace nadále běžet. Pravděpodobně budete končit kategoriemi:
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

Pohotovostní zásada podmíněného přístupu je **zásada zálohování** , která vynechává Azure AD MFA, vícefaktorové ověřování od jiných výrobců nebo řízení na základě zařízení. Aby se minimalizovalo neočekávané přerušení v případě, že je povolená zásada pro nepředvídané účely, zásada by měla zůstat v režimu pouze pro sestavy, pokud se nepoužívá. Správci můžou monitorovat potenciální dopad na své pohotovostní zásady pomocí sešitu pro podmíněný přístup. Pokud se vaše organizace rozhodne aktivovat svůj pohotovostní plán, správci můžou zásadu Povolit a zakázat běžné zásady založené na řízení.

>[!IMPORTANT]
> Zakázáním zásad, které vynutily zabezpečení pro vaše uživatele, dojde k omezení stav zabezpečení i v případě, že je plán pohotovostní.

* Nakonfigurujte sadu záložních zásad, pokud dojde k výpadku jednoho typu přihlašovacích údajů nebo jednoho mechanismu řízení přístupu, který má vliv na přístup k vašim aplikacím. Nakonfigurujte zásady ve stavu pouze sestavy, který vyžaduje připojení k doméně jako řízení, jako zálohu aktivní zásady, která vyžaduje poskytovatele vícefaktorového ověřování od jiného výrobce.
* Pomocí postupů uvedených v dokumentu White Paper s [pokyny k heslům](https://aka.ms/passwordguidance) snížíte riziko chybných aktérů, které se týkají pokusů o hesla.
* Nasaďte [Azure ad Self-Service resetování hesla (SSPR)](./tutorial-enable-sspr.md) a [ochranu heslem Azure AD](./howto-password-ban-bad-on-premises-deploy.md) , abyste se ujistili, že uživatelé nepoužívají běžné heslo a výrazy, které se rozhodnete zakázat.
* Používejte zásady, které omezují přístup v rámci aplikací, Pokud nedosáhnete určité úrovně ověřování, místo toho, abyste museli jednoduše vracet přístup k úplnému přístupu. Například:
  * Nakonfigurujte zásady zálohování, které odesílají deklaraci omezené relace na Exchange a SharePoint.
  * Pokud vaše organizace používá Microsoft Cloud App Security, zvažte návrat k zásadám, které MCAS a pak MCAS povolí přístup jen pro čtení, ale ne nahrávání.
* Pojmenujte zásady, abyste se ujistili, že je budete moct snadno najít při přerušení. Do názvu zásady zahrňte tyto prvky:
  * *Číslo popisku* pro zásadu.
  * Text, který se má zobrazit, tato zásada je určena pouze pro mimořádné události. Příklad: **povolení v nouzi**
  * *Přerušení* , ke kterému se vztahuje. Například: **během výpadku MFA**
  * *Pořadové číslo* pro zobrazení pořadí, v jakém je nutné zásady aktivovat.
  * *Aplikace* , na které se vztahuje
  * *Ovládací prvky* , které budou použity.
  * *Podmínky* , které vyžaduje.
  
Tato standardní pojmenování pro pohotovostní zásady bude následující: 

```
EMnnn - ENABLE IN EMERGENCY: [Disruption][i/n] - [Apps] - [Controls] [Conditions]
```

Následující příklad: **příklad zásady CA s pohotovostním oprávněním pro obnovení přístupu k důležitým aplikacím pro spolupráci**, je typický podnikový pohotovostní. V tomto scénáři organizace obvykle vyžaduje MFA pro všechna přístup k Exchangi Online a SharePointu Online a přerušení v tomto případě je tím, že poskytovatel MFA pro zákazníka má výpadek (ať už jde o Azure AD MFA, místní poskytovatel MFA nebo MFA). Tato zásada tento výpadek omezuje tím, že umožňuje konkrétním cílovým uživatelům přistupovat k těmto aplikacím z důvěryhodných zařízení s Windows jenom v případě, že k aplikaci přistupuje z důvěryhodné podnikové sítě. Z těchto omezení taky vyloučí účty v nouzi a základní správce. Cíloví uživatelé pak získají přístup k Exchangi Online a SharePointu Online, zatímco jiní uživatelé stále nebudou mít přístup k aplikacím z důvodu výpadku. Tento příklad bude vyžadovat pojmenované síťové umístění **CorpNetwork** a skupinu zabezpečení **ContingencyAccess** s cílovými uživateli, skupinou s názvem **CoreAdmins** a základními správci a skupinou s názvem **EmergencyAccess** s účty pro nouzový přístup. Pohotovostní modul vyžaduje pro poskytnutí požadovaného přístupu čtyři zásady. 

**Příklad zásad certifikační autority s pohotovostním přístupem pro obnovení přístupu ke klíčovým aplikacím pro spolupráci:**

* Zásady 1: vyžadování zařízení připojených k doméně pro Exchange a SharePoint
  * Název: EM001-ENABLE v nouzi: přerušení MFA [1/4]-Exchange SharePoint – vyžadovat připojení k hybridní službě Azure AD
  * Uživatelé a skupiny: zahrnují ContingencyAccess. Vyloučit CoreAdmins a EmergencyAccess
  * Cloudové aplikace: Exchange Online a SharePoint Online
  * Podmínky: Any
  * Udělení řízení: vyžadovat připojení k doméně
  * Stav: pouze sestava
* Zásada 2: blokování jiných platforem než Windows
  * Název: EM002-ENABLE v nouzi: přerušení MFA [2/4]-Exchange SharePoint – blokovat přístup s výjimkou Windows
  * Uživatelé a skupiny: včetně všech uživatelů. Vyloučit CoreAdmins a EmergencyAccess
  * Cloudové aplikace: Exchange Online a SharePoint Online
  * Podmínky: platforma zařízení zahrnuje všechny platformy, vyloučení Windows
  * Udělení řízení: blok
  * Stav: pouze sestava
* Zásady 3: blokování jiných sítí než CorpNetwork
  * Název: EM003-ENABLE v nouzi: přerušení MFA [3/4]-Exchange SharePoint – blokovat přístup s výjimkou podnikové sítě
  * Uživatelé a skupiny: včetně všech uživatelů. Vyloučit CoreAdmins a EmergencyAccess
  * Cloudové aplikace: Exchange Online a SharePoint Online
  * Podmínky: umístění zahrnují všechna umístění, vyloučit CorpNetwork
  * Udělení řízení: blok
  * Stav: pouze sestava
* Zásada 4: explicitní blokování EAS
  * Název: EM004-ENABLE v nouzi: přerušení MFA [4/4]-Exchange-Block EAS pro všechny uživatele
  * Uživatelé a skupiny: zahrnout všechny uživatele
  * Cloudové aplikace: zahrnout Exchange Online
  * Podmínky: klientské aplikace: Exchange Active Sync
  * Udělení řízení: blok
  * Stav: pouze sestava

Pořadí aktivace:

1. Vylučte z existujících zásad MFA ContingencyAccess, CoreAdmins a EmergencyAccess. Ověřte, že uživatel v ContingencyAccess má přístup k SharePointu Online a Exchange Online.
2. Povolit zásadu 1: Ověřte, že uživatelé na zařízeních připojených k doméně, kteří nejsou ve skupinách vyloučení, mají přístup k Exchangi Online a SharePointu Online. Ověřte, že uživatelé ve skupině vyloučení mají přístup k SharePointu Online a Exchange z libovolného zařízení.
3. Povolit zásadu 2: Ověřte, že uživatelé, kteří nejsou ve skupině vyloučení, nemůžou získat přístup k SharePointu Online a Exchange Online ze svých mobilních zařízení. Ověřte, že uživatelé ve skupině vyloučení mají přístup k SharePointu a Exchange z libovolného zařízení (Windows/iOS/Android).
4. Povolit zásadu 3: Ověřte, že uživatelé, kteří nejsou ve skupinách vyloučení, nemají přístup k SharePointu a Exchangi mimo podnikovou síť, a to ani na počítači připojeném k doméně. Ověřte, že uživatelé ve skupině vyloučení mají přístup k SharePointu a Exchange z libovolné sítě.
5. Povolit zásadu 4: Ověřte, že všichni uživatelé nemůžou získat Exchange Online z nativních e-mailových aplikací na mobilních zařízeních.
6. Zakažte stávající zásady vícefaktorového ověřování pro SharePoint Online a Exchange Online.

V tomto dalším příkladu **příkladem B-pohotovostní zásady certifikační autority, které umožňují mobilní přístup k Salesforce**, se obnoví přístup obchodní aplikace. V tomto scénáři zákazník obvykle vyžaduje, aby jejich zaměstnanci prodeje měli přístup k Salesforce (nakonfigurovanému pro jednotné přihlašování pomocí Azure AD) z mobilních zařízení, aby je bylo možné povolit jenom ze zařízení dodržujících předpisy. Přerušení v tomto případě znamená, že došlo k potížím při vyhodnocování dodržování předpisů zařízením a výpadek se děje v citlivé době, kdy prodejní tým potřebuje přístup k Salesforce a uzavřít obchody. Tyto pohotovostní zásady udělí důležitému uživateli přístup k Salesforce z mobilního zařízení, aby mohli dál uzavírat obchody a Nerušit činnost podniku. V tomto příkladu **SalesforceContingency** obsahuje všechny zaměstnance prodejů, kteří potřebují zachovat přístup a **SalesAdmins** obsahuje potřebné Správce Salesforce.

**Příklad B-pohotovostní zásady certifikační autority:**

* Zásady 1: zablokovat všem, kteří nejsou v týmu SalesContingency
  * Název: EM001 – povolení v nouzi: přerušení dodržování předpisů zařízením [1/2]-Salesforce – zablokuje všechny uživatele kromě SalesforceContingency.
  * Uživatelé a skupiny: včetně všech uživatelů. Vyloučit SalesAdmins a SalesforceContingency
  * Cloudové aplikace: Salesforce.
  * Podmínky: Žádné
  * Udělení řízení: blok
  * Stav: pouze sestava
* Zásada 2: blokování prodejního týmu z jakékoli jiné platformy než mobilní (pro omezení oblasti útoku)
  * Název: EM002 – povolení v nouzi: přerušení dodržování předpisů zařízením [2/2]-Salesforce – zablokuje všechny platformy kromě iOS a Androidu.
  * Uživatelé a skupiny: zahrnují SalesforceContingency. Vyloučit SalesAdmins
  * Cloudové aplikace: Salesforce
  * Podmínky: platforma zařízení zahrnuje všechny platformy, vyloučení iOS a Androidu.
  * Udělení řízení: blok
  * Stav: pouze sestava

Pořadí aktivace:

1. Vylučte SalesAdmins a SalesforceContingency z existujících zásad dodržování předpisů zařízeními pro Salesforce. Ověřte, že uživatel ve skupině SalesforceContingency má přístup k Salesforce.
2. Povolit zásadu 1: Ověřte, že uživatelé mimo SalesContingency nemají přístup k Salesforce. Ověřte, že uživatelé v SalesAdmins a SalesforceContingency mají přístup k Salesforce.
3. Povolit zásadu 2: Ověřte, že uživatelé ve skupině SalesContingency nemají přístup k Salesforce ze svých přenosných počítačů s Windows/Mac, ale mají stále přístup ze svých mobilních zařízení. Ověřte, že SalesAdmin má stále přístup k Salesforce z libovolného zařízení.
4. Zakažte stávající zásady dodržování předpisů zařízeními pro Salesforce.

### <a name="contingencies-for-user-lockout-from-on-prem-resources-nps-extension"></a>Nepředvídané události pro uzamknutí uživatele z premch prostředků (rozšíření NPS)

Pokud chráníte přístup k síti VPN pomocí rozšíření Azure AD MFA NPS, zvažte federování řešení VPN jako [aplikaci SAML](../manage-apps/view-applications-portal.md) a určete kategorii aplikace podle doporučení níže. 

Pokud jste nasadili rozšíření Azure AD MFA NPS pro ochranu premch prostředků, jako jsou VPN a Brána vzdálené plochy s MFA – měli byste zvážit předem, pokud jste v případě nouze připraveni na zakázání vícefaktorového ověřování.

V takovém případě můžete zakázat rozšíření serveru NPS. Server NPS proto ověří jenom primární ověřování a neuplatní MFA pro uživatele.

Zakázat rozšíření serveru NPS: 
-   Exportujte klíč registru HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\AuthSrv\Parameters jako zálohu. 
-   Odstraňte hodnoty registru "AuthorizationDLLs" a "ExtensionDLLs", nikoli klíč parametrů. 
-   Restartujte službu NPS (Network Policy Service), aby se změny projevily. 
-   Zjistěte, jestli je primární ověřování pro síť VPN úspěšné.

Jakmile se služba obnoví a jste připraveni znovu vymáhat MFA pro uživatele, povolte rozšíření serveru NPS: 
-   Importujte klíč registru ze zálohy HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\AuthSrv\Parameters 
-   Restartujte službu NPS (Network Policy Service), aby se změny projevily. 
-   Určete, zda je primární ověřování a také sekundární ověřování pro síť VPN úspěšné.
-   Zkontrolujte servery NPS a protokol VPN a určete, kteří uživatelé se přihlásili během nouzového okna.

### <a name="deploy-password-hash-sync-even-if-you-are-federated-or-use-pass-through-authentication"></a>Nasadit synchronizaci hodnot hash hesel i v případě, že jste federované nebo používáte předávací ověřování

K uzamčení uživatele může dojít také v případě, že jsou splněné následující podmínky:

- Vaše organizace používá řešení hybridní identity s předávacím ověřováním nebo federaci.
- Vaše místní systémy identit (například služba Active Directory, AD FS nebo závislá součást) nejsou k dispozici. 
 
Aby byla vaše organizace pružnější, měla by [Povolit synchronizaci hodnot hash hesel](../hybrid/choose-ad-authn.md), protože umožňuje [Přepnout na použití synchronizace hodnot hash hesel](../hybrid/plan-connect-user-signin.md) v případě, že vaše místní systémy identity nejsou funkční.

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
4. [Třídění všech zjištění rizik hlášených](../reports-monitoring/concept-sign-ins.md) v rámci tohoto okna.

## <a name="after-a-disruption"></a>Po přerušení

Po obnovení služby, která způsobila přerušení, vraťte změny, které jste provedli v rámci aktivovaného plánu řešení pro nepředvídané účely. 

1. Povolit běžné zásady
2. Deaktivujte své pohotovostní zásady zpátky do režimu pouze pro sestavy. 
3. Vraťte všechny další změny, které jste provedli a popsali během přerušení.
4. Pokud jste použili účet pro nouzový přístup, nezapomeňte znovu vygenerovat přihlašovací údaje a fyzicky zabezpečit nové přihlašovací údaje v rámci postupů vašeho účtu pro nouzový přístup.
5. Pokračujte v [třídění všech zjištění rizik hlášených](../reports-monitoring/concept-sign-ins.md) po přerušení podezřelé aktivity.
6. Odvolat všechny obnovovací tokeny, které byly vydány [pomocí prostředí PowerShell](/powershell/module/azuread/revoke-azureaduserallrefreshtoken) pro cílení na skupinu uživatelů. Odvolání všech aktualizačních tokenů je důležité pro privilegované účty používané při přerušení a v důsledku toho vynutí opětovné ověření a splnění kontroly nad obnovenými zásadami.

## <a name="emergency-options"></a>Nouzové možnosti

 V případě nouze a vaše organizace předtím neimplementovala plán zmírňování nebo řešení nepředvídaných událostí, pokud už používají zásady podmíněného přístupu k vymáhání MFA, postupujte podle doporučení v části neškodné volání [uživatele](#contingencies-for-user-lockout) .
Pokud vaše organizace používá starší zásady vícefaktorového ověřování pro uživatele, můžete zvážit následující alternativy:

1. Pokud máte odchozí IP adresu podnikové sítě, můžete je přidat jako důvěryhodné IP adresy, abyste mohli ověřování povolit jenom pro podnikovou síť.
   1. Pokud nemáte inventarizaci odchozích IP adres nebo potřebujete povolit přístup v podnikové síti i mimo ni, můžete přidat celý adresní prostor IPv4 jako důvěryhodné IP adresy zadáním 0.0.0.0/1 a 128.0.0.0/1.

>[!IMPORTANT]
 > Pokud rozpoznáváte důvěryhodné IP adresy na přístup odblokování, negenerují se detekce rizik přidružená k IP adresám (například nemožná cesta nebo neznámá umístění).

>[!NOTE]
 > Konfigurace [důvěryhodných IP adres](./howto-mfa-mfasettings.md) pro Azure AD MFA je dostupná jenom u [licencí Azure AD Premium](./concept-mfa-licensing.md).

## <a name="learn-more"></a>Další informace

* [Dokumentace k ověřování Azure AD](./howto-mfaserver-iis.md)
* [Správa účtů pro správu pro nouzový přístup v Azure AD](../roles/security-emergency-access.md)
* [Konfigurace pojmenovaných umístění v Azure Active Directory](../reports-monitoring/quickstart-configure-named-locations.md)
  * [Set-MsolDomainFederationSettings](/powershell/module/msonline/set-msoldomainfederationsettings)
* [Jak nakonfigurovat zařízení připojená k hybridnímu Azure Active Directory](../devices/hybrid-azuread-join-plan.md)
* [Průvodce nasazením Windows Hello pro firmy](/windows/security/identity-protection/hello-for-business/hello-deployment-guide)
  * [Pokyny k heslu – Microsoft Research](https://research.microsoft.com/pubs/265143/microsoft_password_guidance.pdf)
* [Jaké jsou podmínky v Azure Active Directory podmíněný přístup?](../conditional-access/concept-conditional-access-conditions.md)
* [Co jsou ovládací prvky přístupu v Azure Active Directory podmíněný přístup?](../conditional-access/controls.md)
* [Co je režim pouze pro sestavy podmíněného přístupu?](../conditional-access/concept-conditional-access-report-only.md)
