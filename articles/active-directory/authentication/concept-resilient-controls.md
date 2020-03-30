---
title: Vytvoření strategie správy řízení přístupu odolné – Azure AD
description: Tento dokument obsahuje pokyny ke strategiím, které by organizace měla přijmout, aby zajistila odolnost ke snížení rizika výluky během nepředvídaných narušení.
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.workload: identity
ms.date: 01/29/2020
ms.author: martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0ca5817e744ff81efcd549bc328d7ce5eeedb2d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76908730"
---
# <a name="create-a-resilient-access-control-management-strategy-with-azure-active-directory"></a>Vytvoření odolné strategie správy řízení přístupu pomocí Služby Azure Active Directory

>[!NOTE]
> The information contained in this document represents the current view of Microsoft Corporation on the issues discussed as of the date of publication. Vzhledem k tomu, že společnost Microsoft musí reagovat na měnící se podmínky na trhu, neměla by být vykládána jako závazek ze strany společnosti Microsoft a společnost Microsoft nemůže zaručit přesnost jakýchkoli informací předložených po datu zveřejnění.

Organizace, které spoléhají na jeden řízení přístupu, jako je vícefaktorové ověřování (MFA) nebo jedno síťové umístění, k zabezpečení svých it systémů jsou náchylné k selhání přístupu k jejich aplikacím a prostředkům, pokud tento jediný řízení přístupu přestane být k dispozici nebo nesprávně nakonfigurované. Přírodní katastrofa může například vést k nedostupnosti velkých segmentů telekomunikační infrastruktury nebo podnikových sítí. Takové narušení by mohlo zabránit koncovým uživatelům a správcům v přihlášení.

Tento dokument obsahuje pokyny ke strategiím, které by organizace měla přijmout, aby zajistila odolnost ke snížení rizika uzamčení při nepředvídaných narušeních pomocí následujících scénářů:

 1. Organizace mohou zvýšit svou odolnost, aby se snížilo riziko uzamčení **před narušením** implementací strategií zmírňování nebo pohotovostních plánů.
 2. Organizace mohou nadále přistupovat k aplikacím a **prostředkům,** které si zvolí během přerušení, a to tak, že budou mít zavedeny strategie pro zmírnění rizik a pohotovostní plány.
 3. Organizace by se měly ujistit, že uchovávají informace, například protokoly, **po přerušení** a před vrácením zpět všechny nepředvídané události, které implementovaly.
 4. Organizace, které nezavedly strategie prevence nebo alternativní plány, mohou být schopny implementovat **nouzové možnosti,** jak se vypořádat s narušením.

## <a name="key-guidance"></a>Klíčové pokyny

V tomto dokumentu jsou čtyři klíčové stánek s jídlem:

* Vyhněte se uzamčení správce pomocí účtů nouzového přístupu.
* Implementujte vícefaktorové ověřování pomocí podmíněného přístupu (CA) spíše než na uživatele MFA.
* Zmírňte uzamčení uživatelů pomocí více ovládacích prvků podmíněného přístupu (CA).
* Zmírňte uzamčení uživatelů zřizováním více metod ověřování nebo ekvivalentů pro každého uživatele.

## <a name="before-a-disruption"></a>Před narušením

Zmírnění skutečné horečné narušení musí být primárně zaměřena organizace při řešení problémů řízení přístupu, které mohou vzniknout. Zmírnění zahrnuje plánování skutečné události a provádění strategií, které zajistí, že kontroly přístupu a operace nebudou během přerušení ovlivněny.

### <a name="why-do-you-need-resilient-access-control"></a>Proč potřebujete odolné řízení přístupu?

 Identita je rovina ovládacího prvku uživatelů, kteří přistupují k aplikacím a prostředkům. Váš systém identit řídí, kteří uživatelé a za jakých podmínek, jako jsou ovládací prvky přístupu nebo požadavky na ověřování, uživatelé získají přístup k aplikacím. Pokud jeden nebo více požadavků na ověřování nebo řízení přístupu není k dispozici pro uživatele k ověření z důvodu nepředvídaných okolností, organizace mohou zaznamenat jeden nebo oba z následujících problémů:

* **Uzamčení správce:** Správci nemohou spravovat klienta nebo služby.
* **Uzamčení uživatele:** Uživatelé nemají přístup k aplikacím nebo prostředkům.

### <a name="administrator-lockout-contingency"></a>Nepředvídaná událost uzamčení správce

Chcete-li odemknout přístup správce k vašemu tenantovi, měli byste vytvořit účty pro nouzový přístup. Tyto účty nouzového přístupu, označované také jako break *glass* účty, umožňují přístup ke správě konfigurace Azure AD, když nejsou k dispozici běžné postupy přístupu k privilegovaným účtům. V návaznosti na doporučení účtu [pro nouzový přístup]( https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-emergency-access)by měly být vytvořeny alespoň dva účty pro nouzový přístup .

### <a name="mitigating-user-lockout"></a>Zmírnění uzamčení uživatele

 Chcete-li zmírnit riziko uzamčení uživatelů, použijte zásady podmíněného přístupu s více ovládacími prvky, abyste uživatelům poskytli možnost volby způsobu, jakým budou přistupovat k aplikacím a prostředkům. Tím, že uživatel i možnost volby mezi, například, přihlášení pomocí vícefaktorové **ověřování nebo** přihlášení ze spravovaného zařízení **nebo** přihlášení z podnikové sítě, pokud jeden z ovládacích prvků přístupu není k dispozici uživatel má další možnosti pokračovat v práci.

#### <a name="microsoft-recommendations"></a>Doporučení společnosti Microsoft

Do stávajících zásad podmíněného přístupu pro organizaci zahrňte následující ovládací prvky přístupu:

1. Zřizování více metod ověřování pro každého uživatele, kteří spoléhají na různé komunikační kanály, například microsoft ověřovací aplikace (internet), token OATH (generované na zařízení) a SMS (telefonní).
2. Nasaďte Windows Hello pro firmy na zařízeních s Windows 10, abyste splnili požadavky na vícefaktorové financování přímo z přihlášení k zařízení.
3. Používejte důvěryhodná zařízení prostřednictvím [hybridního připojení Azure AD](https://docs.microsoft.com/azure/active-directory/devices/overview) nebo [zařízení Microsoft Intune Managed](https://docs.microsoft.com/intune/planning-guide). Důvěryhodná zařízení zlepší uživatelské prostředí, protože samotné důvěryhodné zařízení může splňovat silné požadavky na ověřování zásad bez výzvy vícefaktorového ověřování pro uživatele. Vícefaktorové informace pak budou vyžadovány při registraci nového zařízení a při přístupu k aplikacím nebo prostředkům z nedůvěryhodných zařízení.
4. Používejte zásady ochrany identity Azure AD založené na rizicích, které brání přístupu, když je uživatel nebo přihlášení ohroženo namísto pevných zásad MFA.

>[!NOTE]
> Zásady založené na rizicích vyžadují licence [Azure AD Premium P2.](https://azure.microsoft.com/pricing/details/active-directory/)

Následující příklad popisuje zásady, které je nutné vytvořit, aby uživatel mohl získat přístup k jejich aplikacím a prostředkům odolné řízení přístupu. V tomto příkladu budete potřebovat skupinu zabezpečení **AppUsers** s cílovými uživateli, kterým chcete udělit přístup, skupinu s názvem **CoreAdmins** s hlavními správci a skupinu s názvem **EmergencyAccess** s účty nouzového přístupu.
Tato ukázková sada zásad udělí vybraným uživatelům v **Aplikaci Uživatelé**přístup k vybraným aplikacím, pokud se připojují z důvěryhodného zařízení, nebo poskytne silné ověřování, například vícefaktorové ověřování. Nezahrnuje nouzové účty a základní správce.

**Nastavení zásad zmírnění certifikační autority:**

* Zásady 1: Blokování přístupu lidem mimo cílové skupiny
  * Uživatelé a skupiny: Zahrnout všechny uživatele. Vyloučit uživatele appů, správce coreadminů a nouzový přístup
  * Cloudové aplikace: Zahrnout všechny aplikace
  * Podmínky: (Žádné)
  * Řízení grantu: Blok
* Zásady 2: Udělit přístup AppUsers vyžadující MFA nebo důvěryhodné zařízení.
  * Uživatelé a skupiny: Zahrnout AppUsers. Vyloučit CoreAdmins a EmergencyAccess
  * Cloudové aplikace: Zahrnout všechny aplikace
  * Podmínky: (Žádné)
  * Řízení grantů: Udělte přístup, vyžadují vícefaktorové ověřování, vyžadují, aby zařízení bylo kompatibilní. Pro více ovládacích prvků: Vyžadovat jeden z vybraných ovládacích prvků.

### <a name="contingencies-for-user-lockout"></a>Nepředvídané události pro uzamčení uživatele

Případně může vaše organizace také vytvořit zásady pro nepředvídané události. Chcete-li vytvořit zásady pro nepředvídané události, musíte definovat kritéria kompromisu mezi kontinuitou provozu, provozními náklady, finančními náklady a bezpečnostními riziky. Můžete například aktivovat zásady pro nepředvídané události pouze pro podmnožinu uživatelů, pro podmnožinu aplikací, pro podmnožinu klientů nebo z podmnožiny umístění. Zásady pro nepředvídané události umožní správcům a koncovým uživatelům přístup k aplikacím a prostředkům během přerušení, kdy nebyla implementována žádná metoda zmírnění.
Pochopení vaší expozice během přerušení pomáhá snížit riziko a je důležitou součástí procesu plánování. Chcete-li vytvořit pohotovostní plán, nejprve určete následující obchodní požadavky vaší organizace:

1. Určete své klíčové aplikace předem: K jakým aplikacím musíte udělit přístup, a to i s nižším rizikovým/bezpečnostním postojem? Sestavte seznam těchto aplikací a ujistěte se, že ostatní zúčastněné strany (obchodní, bezpečnostní, legální, vedení) souhlasí s tím, že pokud všechny kontroly přístupu zmizí, musí tyto aplikace stále běžet. Budete pravděpodobně skončí s kategoriemi:
   * **Kategorie 1 kritické aplikace,** které nemohou být nedostupné déle než několik minut, například aplikace, které přímo ovlivňují příjmy organizace.
   * **Kategorie 2 důležité aplikace,** které podnik musí být přístupné během několika hodin.
   * **Kategorie 3 aplikace s nízkou prioritou,** které vydrží přerušení několik dní.
2. U aplikací v kategoriích 1 a 2 společnost Microsoft doporučuje předem naplánovat, jaký typ přístupu chcete povolit:
   * Chcete povolit úplný přístup nebo omezenou relaci, jako je omezení stahování?
   * Chcete povolit přístup k části aplikace, ale ne k celé aplikaci?
   * Chcete povolit přístup informačního pracovníka a blokovat přístup správce, dokud nebude obnoven ovládací prvek přístupu?
3. U těchto aplikací společnost Microsoft také doporučuje naplánovat, které cesty přístupu budete záměrně otevírat a které z nich zavřete:
   * Chcete povolit prohlížeči pouze přístup a blokovat bohaté klienty, kteří mohou ukládat offline data?
   * Chcete povolit přístup pouze uživatelům v podnikové síti a uchovávat externí uživatele blokované?
   * Chcete povolit přístup z určitých zemí nebo oblastí pouze během přerušení?
   * Chcete, aby zásady pro zásady pro nepředvídané události, zejména pro kritické aplikace, selhaly nebo uspěly, pokud není k dispozici alternativní řízení přístupu?

#### <a name="microsoft-recommendations"></a>Doporučení společnosti Microsoft

Zásady podmíněného přístupu pro nepředvídané události jsou **zakázané zásady,** které vynechou ovládací prvky Azure MFA, MFA třetích stran, založené na rizicích nebo na zařízení. Když se pak vaše organizace rozhodne aktivovat váš pohotovostní plán, správci mohou povolit zásady a zakázat běžné zásady založené na ovládacím prvku.

>[!IMPORTANT]
> Zakázání mačkání zásad, které vynucují zabezpečení uživatelů, a to i dočasně, sníží stav zabezpečení, zatímco pohotovostní plán je na místě.

* Nakonfigurujte sadu záložních zásad, pokud narušení v jednom typu pověření nebo jednom mechanismu řízení přístupu ovlivní přístup k vašim aplikacím. Nakonfigurujte zásadu v zakázaném stavu, která vyžaduje připojení k doméně jako ovládací prvek, jako zálohu pro aktivní zásadu, která vyžaduje zprostředkovatele mfa třetí strany.
* Snižte riziko, že chybní herci budou hádat hesla, pokud není vyžadováno vícefaktorové ověřování, a to podle postupů v dokumentu white paper s [pokyny pro hesla.](https://aka.ms/passwordguidance)
* Nasazení [samoobslužné resetování hesla Azure AD (SSPR)](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr) a [Azure AD Password Protection](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-ban-bad-on-premises-deploy) chcete-li zajistit, aby uživatelé nepoužívali běžné heslo a termíny, které se rozhodnete zakázat.
* Používejte zásady, které omezují přístup v rámci aplikací, pokud není dosaženo určité úrovně ověřování, místo toho, abyste jednoduše klesli zpět na úplný přístup. Například:
  * Nakonfigurujte zásady zálohování, které odešlou deklaraci relace s omezeným přístupem na Exchange a SharePoint.
  * Pokud vaše organizace používá Microsoft Cloud App Security, zvažte návrat k zásadám, které zapojují MCAS a pak MCAS Umožňuje přístup jen pro čtení, ale ne nahraje.
* Pojmenujte své zásady, abyste se ujistili, že je během přerušení snadno vyhledáte. Do názvu zásady zahrňte následující prvky:
  * *Číslo popisku* pro zásadu.
  * Text ukázat, tato politika je pouze pro případ nouze. Například: **POVOLIT v nouzovém stavu**
  * *Narušení,* na které se vztahuje. Například: **Během přerušení vícefaktorové žádosti**
  * *Pořadové číslo* pro zobrazení pořadí, které je nutné aktivovat zásady.
  * *Aplikace,* na které se vztahuje.
  * *Ovládací prvky, které* bude platit.
  * *Podmínky, které* to vyžaduje.
  
Tento standard pojmenování pro zásady pro nepředvídané události bude následující: 

```
EMnnn - ENABLE IN EMERGENCY: [Disruption][i/n] - [Apps] - [Controls] [Conditions]
```

Následující příklad: **Příklad A – Zásady certifikační autority pro nepředvídané události k obnovení přístupu k důležitým aplikacím pro spolupráci**je typická firemní nepředvídaná událost. V tomto scénáři organizace obvykle vyžaduje vícefaktorové zabezpečení pro všechny exchange online a sharepointonline přístup a přerušení v tomto případě je zprostředkovatel mfa pro zákazníka má výpadek (ať už Azure MFA, místní zprostředkovatel mfa nebo třetí strany MFA). Tato zásada tento výpadek zmírňuje tím, že konkrétním cílovým uživatelům umožňuje přístup k těmto aplikacím z důvěryhodných zařízení se systémem Windows pouze v případě, že přistupují k aplikaci ze své důvěryhodné podnikové sítě. Z těchto omezení také vyloučí nouzové účty a hlavní správce. Cílení uživatelé pak získají přístup k Exchange Online a SharePointu Online, zatímco ostatní uživatelé nebudou mít kvůli výpadku přístup k aplikacím. Tento příklad bude vyžadovat pojmenované umístění v síti **CorpNetwork** a skupinu zabezpečení **ContingencyAccess** s cílovými uživateli, skupinu s názvem **CoreAdmins** s hlavními správci a skupinu s názvem **EmergencyAccess** s účty nouzového přístupu. Nepředvídané události vyžaduje čtyři zásady poskytnout požadovaný přístup. 

**Příklad A – Zásady pro nepředvídané události pro obnovení přístupu k důležitým aplikacím pro spolupráci:**

* Zásady 1: Vyžadovat zařízení připojená k doméně pro Exchange a SharePoint
  * Název: EM001 – POVOLIT v nouzovém stavu: Narušení vícefaktorové finanční autority[1/4] – Sharepoint Exchange – vyžadovat hybridní připojení k reklamě Azure
  * Uživatelé a skupiny: Zahrnout ContingencyAccess. Vyloučit CoreAdmins a EmergencyAccess
  * Cloudové aplikace: Exchange Online a SharePoint Online
  * Podmínky: Všechny
  * Řízení grantů: Vyžadovat připojenou doménu
  * Stav: Zakázáno
* Zásady 2: Blokovat jiné platformy než Windows
  * Název: EM002 - ENABLE IN EMERGENCY: Narušení vícefaktorové dohody[2/4] - Exchange SharePoint - Blokovat přístup kromě Windows
  * Uživatelé a skupiny: Zahrnout všechny uživatele. Vyloučit CoreAdmins a EmergencyAccess
  * Cloudové aplikace: Exchange Online a SharePoint Online
  * Podmínky: Platforma zařízení zahrnuje všechny platformy, vylučuje Windows
  * Řízení grantu: Blok
  * Stav: Zakázáno
* Zásada 3: Blokovat jiné sítě než CorpNetwork
  * Název: EM003 - ENABLE IN EMERGENCY: Narušení vícefaktorové dohody[3/4] - Exchange SharePoint - Blokovat přístup kromě podnikové sítě
  * Uživatelé a skupiny: Zahrnout všechny uživatele. Vyloučit CoreAdmins a EmergencyAccess
  * Cloudové aplikace: Exchange Online a SharePoint Online
  * Podmínky: Umístění zahrnují libovolné umístění, vylučte CorpNetwork
  * Řízení grantu: Blok
  * Stav: Zakázáno
* Zásada 4: Explicitně blokovat EAS
  * Název: EM004 - ENABLE IN EMERGENCY: Narušení vícefaktorové finanční hodu[4/4] - Exchange - Block EAS pro všechny uživatele
  * Uživatelé a skupiny: Zahrnout všechny uživatele
  * Cloudové aplikace: Včetně Exchange Online
  * Podmínky: Klientské aplikace: Exchange Active Sync
  * Řízení grantu: Blok
  * Stav: Zakázáno

Pořadí aktivace:

1. Vylučte přístup contingencyaccess, CoreAdmins a EmergencyAccess z existující zásady MFA. Ověřte, že uživatel v aplikaci ContingencyAccess má přístup ke SharePointu Online a Exchange Online.
2. Povolit zásady 1: Ověřte, zda uživatelé na zařízeních spojených s doménou, kteří nejsou ve skupinách vyloučení, mají přístup k Exchange Online a SharePointu Online. Ověřte, že uživatelé ve skupině Vyloučit mají přístup ke SharePointu Online a Exchange z libovolného zařízení.
3. Povolit zásady 2: Ověřte, zda se uživatelé, kteří nejsou ve skupině vyloučení, nemohou ze svých mobilních zařízení dostat na SharePoint Online a Exchange Online. Ověřte, zda uživatelé ve skupině Vyloučit mají přístup ke sharepointu a serveru Exchange z libovolného zařízení (Windows/iOS/Android).
4. Povolit zásady 3: Ověřte, zda uživatelé, kteří nejsou ve skupinách vyloučení, nemají přístup ke sharepointovým a exchangem mimo podnikovou síť, a to ani v počítači propojeném s doménou. Ověřte, zda uživatelé ve skupině Vyloučit mají přístup ke sharepointovým a exchangem z libovolné sítě.
5. Povolit zásady 4: Ověřte, zda všichni uživatelé nemohou získat Exchange Online z nativních poštovních aplikací na mobilních zařízeních.
6. Zakažte existující zásady vícefaktorové žádosti pro SharePoint Online a Exchange Online.

V tomto dalším příkladu **příklad B – zásady certifikační autority pro nepředvídané události, které umožňují mobilní přístup k Salesforce**, se obnoví přístup obchodní aplikace. V tomto scénáři zákazník obvykle vyžaduje, aby jejich zaměstnanci prodeje přístup k Salesforce (nakonfigurovaný pro jednotné přihlášení pomocí Azure AD) z mobilních zařízení, které mají být povoleny pouze z kompatibilních zařízení. Narušení v tomto případě je, že je problém s vyhodnocením dodržování předpisů zařízení a výpadek se děje v citlivé době, kdy prodejní tým potřebuje přístup k Salesforce k uzavření obchodů. Tyto zásady pro nepředvídané události umožní kritickým uživatelům přístup k Salesforce z mobilního zařízení, aby mohli pokračovat v uzavírání obchodů a nenarušovat podnikání. V tomto příkladu **SalesforceContingency** obsahuje všechny zaměstnance prodeje, kteří potřebují zachovat přístup a **SalesAdmins** obsahuje nezbytné správce Salesforce.

**Příklad B – Zásady pro nepředvídané události certifikačníautority:**

* Zásady 1: Blokovat všechny, kteří nejsou v týmu SalesContingency
  * Název: EM001 - ENABLE IN EMERGENCY: Narušení dodržování předpisů zařízení[1/2] - Salesforce - Blokovat všechny uživatele kromě SalesforceContingency
  * Uživatelé a skupiny: Zahrnout všechny uživatele. Vyloučit salesadminy a salesforcecontingency
  * Cloudové aplikace: Salesforce.
  * Podmínky: Žádné
  * Řízení grantu: Blok
  * Stav: Zakázáno
* Zásady 2: Blokovat prodejní tým z jakékoli platformy než mobilní (pro snížení plochy útoku)
  * Název: EM002 - ENABLE IN EMERGENCY: Narušení dodržování předpisů zařízení[2/2] - Salesforce - Block All platforms except iOS a Android
  * Uživatelé a skupiny: Zahrnout salesforcepohotovostní. Vyloučit správce prodeje
  * Cloudové aplikace: Salesforce
  * Podmínky: Platforma zařízení zahrnuje všechny platformy, vylučuje iOS a Android
  * Řízení grantu: Blok
  * Stav: Zakázáno

Pořadí aktivace:

1. Vyloučit SalesAdmins a SalesforceContingency z existujícízásady dodržování předpisů zařízení pro Salesforce. Ověřte, zda má uživatel ve skupině SalesforceContingency přístup k programu Salesforce.
2. Povolit zásadu 1: Ověřte, zda uživatelé mimo salescontingency nemají přístup k salesforce. Ověřte, zda uživatelé v SalesAdmins a SalesforceContingency mají přístup k Salesforce.
3. Povolit zásady 2: Ověřte, zda uživatelé ve skupině SalesContingency nemají přístup k službě Salesforce ze svých přenosných počítačů se systémem Windows/Mac, ale stále mají přístup ze svých mobilních zařízení. Ověřte, zda salesadmin má stále přístup k Salesforce z libovolného zařízení.
4. Zakažte stávající zásady dodržování předpisů zařízení pro Salesforce.

### <a name="deploy-password-hash-sync-even-if-you-are-federated-or-use-pass-through-authentication"></a>Nasazení synchronizace hash hesel i v případě, že jste federováni nebo používáte předávací ověřování

Uzamčení uživatele může dojít také v případě, že jsou splněny následující podmínky:

- Vaše organizace používá řešení hybridní identity s předávacím ověřováním nebo federací.
- Místní systémy identit (například Služba Active Directory, Služba AD FS nebo závislá součást) nejsou k dispozici. 
 
Chcete-li být odolnější, vaše organizace by měla [povolit synchronizaci hash hesel](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn), protože umožňuje [přepnout na synchronizaci hash hesla,](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-user-signin) pokud jsou vaše místní systémy identit mimo provoz.

#### <a name="microsoft-recommendations"></a>Doporučení společnosti Microsoft
 Povolte synchronizaci hash hesel pomocí průvodce Azure AD Connect, bez ohledu na to, zda vaše organizace používá federační nebo předávací ověřování.

>[!IMPORTANT]
> Není nutné převádět uživatele z federovaného na spravované ověřování, aby bylo možné používat synchronizaci hash hesel.

## <a name="during-a-disruption"></a>Během přerušení

Pokud jste se rozhodli pro implementaci plánu zmírnění, budete moci automaticky přežít jedno narušení řízení přístupu. Pokud jste se však rozhodli vytvořit pohotovostní plán, budete moci aktivovat zásady pro nepředvídané události během přerušení řízení přístupu:

1. Povolte zásady pro nepředvídané události, které udělují cílovým uživatelům přístup ke konkrétním aplikacím z konkrétních sítí.
2. Zakažte běžné zásady založené na ovládacím prvku.

### <a name="microsoft-recommendations"></a>Doporučení společnosti Microsoft

V závislosti na tom, které skutečnosti snižující závažnost rizika nebo nepředvídané události se používají během přerušení, může vaše organizace udělovat přístup pouze hesla. Žádná záruka není značným bezpečnostním rizikem, které musí být pečlivě zváženo. Organizace musí:

1. Jako součást strategie řízení změn zdokumentujte každou změnu a předchozí stav, abyste mohli vrátit zpět všechny nepředvídané události, které jste implementovali, jakmile budou plně funkční ovládací prvky přístupu.
2. Předpokládejme, že se škodliví aktéři pokusí získat hesla prostřednictvím spreje hesel nebo phishingových útoků, zatímco jste zakázali vícefaktorové ověřování. Chybní aktéři již mohou mít hesla, která dříve neudělovala přístup k žádnému prostředku, o který se lze pokusit během tohoto okna. Pro kritické uživatele, jako jsou vedoucí pracovníci, můžete toto riziko částečně zmírnit resetováním jejich hesel před zakázáním vícefaktorové ověřování pro ně.
3. Archivujte všechny přihlašovací aktivity a zjistěte, kdo má přístup k čemu v době, kdy byla mfa zakázána.
4. [Třídění všech detekcí rizik hlášených](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins) během tohoto okna.

## <a name="after-a-disruption"></a>Po narušení

Po obnovení služby, která způsobila narušení, vraťte zpět změny provedené v rámci aktivovaného pohotovostního plánu. 

1. Povolit pravidelné zásady
2. Zakažte zásady pro nepředvídané události. 
3. Vraťte zpět všechny ostatní změny, které jste provedli a zdokumentovali během přerušení.
4. Pokud jste použili účet pro nouzový přístup, nezapomeňte v rámci procedur účtu pro nouzový přístup znovu vygenerovat přihlašovací údaje a fyzicky zabezpečit nové údaje o pověřeních.
5. Pokračujte v [třídění všech detekcí rizik hlášených](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins) po narušení podezřelé aktivity.
6. Odvolat všechny obnovovací tokeny, které byly vydány [pomocí prostředí PowerShell](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0) k cílení na sadu uživatelů. Zrušení všech tokenů aktualizace je důležité pro privilegované účty používané během přerušení a tím je vynutí, aby se znovu ověřili a splnili řízení obnovených zásad.

## <a name="emergency-options"></a>Možnosti nouzového stavu

 V případě nouze a vaše organizace dříve neimplementovala plán pro zmírnění nebo nepředvídané události, postupujte podle doporučení v části [Nepředvídané události pro uzamčení uživatelů,](#contingencies-for-user-lockout) pokud již používají zásady podmíněného přístupu k vynucení vícefaktorového přístupu.
Pokud vaše organizace používá starší zásady více faktorových ověřování přístupů pro jednotlivé uživatele, můžete zvážit následující alternativu:

1. Pokud máte odchozí IP adresu podnikové sítě, můžete je přidat jako důvěryhodné IP adresy a povolit tak ověřování pouze v podnikové síti.
   1. Pokud nemáte inventář odchozích IP adres nebo potřebujete povolit přístup v podnikové síti i mimo ni, můžete celý adresní prostor IPv4 přidat jako důvěryhodné IP adresy zadáním 0.0.0.0/1 a 128.0.0.0/1.

>[!IMPORTANT]
 > Pokud rozšíříte důvěryhodné adresy IP, aby odblokovaly přístup, nebudou generovány detekce rizik spojené s adresami IP (například nemožné cestování nebo neznámá místa).

>[!NOTE]
 > Konfigurace [důvěryhodných IP adres](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-mfasettings) pro Azure MFA je dostupná jenom s [licencemi Azure AD Premium](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-licensing).

## <a name="learn-more"></a>Další informace

* [Dokumentace k ověřování Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-iis)
* [Správa účtů pro správu s nouzovým přístupem ve službě Azure AD](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-emergency-access)
* [Konfigurace pojmenovaných umístění ve službě Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)
  * [Set-MsolDomainFederationSettings](https://docs.microsoft.com/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0)
* [Konfigurace hybridních zařízení služby Azure Active Directory](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-plan)
* [Průvodce nasazením Windows Hello pro firmy](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-deployment-guide)
  * [Pokyny pro hesla – Microsoft Research](https://research.microsoft.com/pubs/265143/microsoft_password_guidance.pdf)
* [Jaké jsou podmínky v podmíněném přístupu služby Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/conditions)
* [Co jsou ovládací prvky přístupu v podmíněném přístupu Služby Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/controls)
