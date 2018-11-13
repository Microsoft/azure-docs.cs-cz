---
title: Pět kroků k zabezpečení vaší infrastruktury identit v Azure Active Directory
description: Tento dokument popisuje seznam důležitých akcích, které správci by měla implementovat, abyste věděli, kde zabezpečení organizace pomocí možností Azure AD
services: active-directory
author: martincoetzer
manager: manmeetb
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 06/18/2018
ms.author: martincoetzer
ms.openlocfilehash: 8ce75efae2d735c5653f9dae72c670b0714351ac
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2018
ms.locfileid: "51567946"
---
# <a name="five-steps-to-securing-your-identity-infrastructure"></a>Zabezpečení vaší infrastruktury identit v pěti krocích

Pokud už čtete tento dokument jste clustery významu zabezpečení. Budete pravděpodobně již nesou odpovědnost za zabezpečení vaší organizace. Pokud potřebujete přesvědčování ostatních důležitosti zabezpečení, aby, odešlete jim přečíst nejnovější [inteligentního zabezpečení Microsoftu sestavy](https://www.microsoft.com/security/intelligence-report).

Tento dokument vám pomůže získat bezpečnější stavu pomocí možností služby Azure Active Directory pomocí pěti krocích kontrolní seznam, aby bylo možné očkovat organizaci před kybernetickými útoky.

Tento kontrolní seznam vám pomůže rychle nasadit důležité doporučené akce k ochraně vaší organizace hned s vysvětlením, jak:

* Posílit vaše přihlašovací údaje.
* Omezení možností útoku.
* Můžete Automatizujte reakce na hrozby.
* Zvýšení vašeho povědomí o auditování a monitorování.
* Povolte více předvídatelný a úplné koncového uživatele zabezpečení s samoobslužné podpory.

> [!NOTE]
> Mnohá z těchto doporučení v tomto dokumentu platí pouze pro aplikace, které jsou nakonfigurovány pro použití služby Azure Active Directory jako jeho poskytovatele identity. Konfigurace aplikací pro jednotné přihlašování zajišťuje výhody credential zásady, detekce hrozeb, auditování, protokolování a další funkce přidat do těchto aplikací. [Jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-sso) je nadace, na kterém jsou všechny tyto doporučení jsou založená.

Doporučení v tomto dokumentu jsou v souladu s [skóre zabezpečení Identity](https://docs.microsoft.com/azure/active-directory/fundamentals/identity-secure-score), automatizované posuzování konfigurace zabezpečení identity tenanta Azure AD. Organizace můžete použít stránku skóre zabezpečení identit na portálu Azure AD najít mezery v jejich aktuální konfigurace zabezpečení a ujistěte se, že řídí aktuální Microsoft osvědčené postupy pro zabezpečení. Implementace jednotlivých doporučení na stránce zabezpečení skóre zvýší vaše skóre a umožňují sledovat svůj postup a navíc můžete porovnat vaše implementace proti jiné podobné velikost organizace nebo vaše odvětví.

![Skóre zabezpečení identit](media/azure-ad/azure-ad-sec-steps0.png)

## <a name="before-you-begin-protect-privileged-accounts-with-mfa"></a>Než začnete: ochranu privilegovaných účtů s MFA

Než začnete tento kontrolní seznam, ujistěte se, že jste není dojde k ohrožení zabezpečení při čtete tento kontrolní seznam. Nejprve je třeba chránit privilegované účty.

Útočníci, kteří mají kontrolu nad privilegovaným účtům můžete provést obrovské poškození, takže je důležité nejdřív ochranu těchto účtů. Povolit a vyžadují [ověřování Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication) (MFA) pro všechny správce v organizaci pomocí [základní ochranu](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-baseline-protection). Pokud jste neimplementovali MFA, proveďte to nyní! Je to důležité.

Všechny sady? Pusťme se do práce v kontrolním seznamu.

## <a name="step-1---strengthen-your-credentials"></a>Krok 1 – posílit vaše přihlašovací údaje 

S účtem dojde k ohrožení bezpečnosti s jedním z několika metod, jako je heslo zařízení, opakování porušení zabezpečení nebo phishing pocházejí z většiny porušení zabezpečení organizace. Další informace o těchto útocích v tomto videu (45 minut):
> [!VIDEO https://www.youtube.com/embed/uy0j1_t5Hd4]

### <a name="make-sure-your-organization-use-strong-authentication"></a>Ujistěte se, že vaše organizace používá silné ověřování

S ohledem na frekvenci se hádání hesel phished vám ho někdo ukradne s malwaru nebo znovu použít, je velmi důležité zpět heslo s určitou formu silné přihlašovací údaje – Další informace o [ověřování Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication).

### <a name="start-banning-commonly-attacked-passwords-and-turn-off-traditional-complexity-and-expiration-rules"></a>Počáteční zakazovat běžně napadených hesla a vypnout tradiční složitost a vypršení platnosti pravidla.

Řada organizací používá tradiční složitost (která vyžaduje speciální znaky, číslice, velká a malá písmena) a pravidla pro vypršení platnosti hesla. [Výzkum Microsoftu](https://aka.ms/passwordguidance) ukázal, tyto zásady způsobit, že uživatelé zvolili hesla, které se snadněji odhadnout.

Azure AD [dynamické zakázaných hesel](https://docs.microsoft.com/azure/active-directory/active-directory-secure-passwords) funkce používá aktuální chování útočníkovi zabránit uživatelům v nastavení hesla, které lze snadno uhádnout. Tato funkce je vždycky aktivní, když uživatelé vytvářejí v cloudu, ale je nyní dostupná také pro hybridní organizace v případě, že nasazujete [ochrany hesla Azure AD pro Windows Server Active Directory](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad-on-premises). Ochrana hesel Azure AD uživatelům výběr těchto společná hesla a ostatní porty blokuje je možné rozšířit na blokování heslo obsahující vlastní klíčová slova, které zadáte. Například můžete uživatelům zabránit ve výběru hesla obsahující názvy produktů vaší společnosti nebo místní sportovní tým.

Microsoft doporučuje přijetí tyto zásady moderní heslo na základě [pokynů NIST](https://pages.nist.gov/800-63-3/sp800-63b.html):

1. Vyžadovat, že se hesla mají alespoň 8 znaků. Už není nutně lepší, protože mohou způsobit uživatele, aby si vybírali hesla předvídatelné, ukládat hesla v souborech nebo napište.
2. Zákaz pravidel vypršení platnosti, které se dají snadno uhádnout hesla, jako uživatele přimět, **Summer2018!**
3. Zakázat znak složení požadavky a zabránit uživatelům ve výběru běžně napadené hesla, protože mohou způsobit uživatelům si vybrat předvídatelné znak nahrazení hesla.

Můžete použít [prostředí PowerShell, jak zabránit vypršení platnosti hesla](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy) pro uživatele, pokud vytvoříte identit ve službě Azure AD přímo. Hybridní organizace by měly implementovat tyto zásady, které používají [nastavení zásad skupiny domény](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/hh994572(v%3dws.10)) nebo [prostředí Windows PowerShell](https://docs.microsoft.com/powershell/module/addsadministration/set-addefaultdomainpasswordpolicy).

### <a name="protect-against-leaked-credentials-and-add-resilience-against-outages"></a>Ochrana s uniklými přihlašovacími údaji a přidejte odolnost proti výpadkům

Pokud vaše organizace používá řešení s hybridní identitou předávací ověřování nebo federace, by měl povolení synchronizace hodnot hash hesel z následujících dvou důvodů:

* [Uživatelé s uniklými přihlašovacími údaji](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-risk-events) sestavy ve službě Azure AD management vás upozorní, dvojic uživatelské jméno a heslo, které byly vystaveny na "dark web." Neuvěřitelné množství hesel je úniku prostřednictvím útoků phishing, malwarem a hesla znovu na weby třetích stran, které se později dojde k porušení zabezpečení. Najde, mnoho z nich úniku přihlašovacích údajů společnosti Microsoft a vám sdělí, v této sestavě, pokud se shodují přihlašovacích údajů ve vaší organizaci – ale pouze tehdy, pokud jste [povolení synchronizace hodnot hash hesel](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization)!
* V případě výpadku na místě (například v útok ransomwarem) budete moct přepnout na používání [cloudové ověřování pomocí synchronizace hodnot hash hesel](https://docs.microsoft.com/azure/security/azure-ad-choose-authn). Tato metoda záložní ověřování vám umožní pokračovat v přístupu k aplikacím, které jsou nakonfigurovány pro ověřování pomocí Azure Active Directory, včetně Office 365. Pracovníci IT v tomto případě nebudete muset uchýlíte k osobní e-mailové účty sdílet data, dokud se nevyřeší místní výpadek.

Další informace o tom [synchronizace hodnot hash hesel](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization) funguje.

### <a name="implement-ad-fs-extranet-smart-lockout"></a>Implementace inteligentní uzamčení extranetu služby AD FS

Organizace, které konfigurace aplikací k ověření přímo do služby Azure AD těžit z [Azure AD inteligentním uzamčením](https://docs.microsoft.com/azure/active-directory/active-directory-secure-passwords). Pokud používáte službu AD FS v systému Windows Server 2012 R2, implementaci AD FS [funkce ochrany uzamčení extranetu](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-soft-lockout-protection). Pokud používáte službu AD FS na Windows serveru 2016, implementovat [inteligentní uzamčení extranetu](https://support.microsoft.com/help/4096478/extranet-smart-lockout-feature-in-windows-server-2016). AD FS inteligentní extranetu, ke které uzamčení chrání před útoky hrubou silou útokům, které se zaměřují služby AD FS a brání uživatelům v uzamknutí ve službě Active Directory.

### <a name="take-advantage-of-intrinsically-secure-easier-to-use-credentials"></a>Využijte výhod vnitřně zabezpečené a usnadňuje používání přihlašovacích údajů

Pomocí [Windows Hello](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification), hesla můžete nahradit pomocí silného dvojúrovňového ověřování na počítačích a mobilních zařízení. Toto ověření se skládá z nový typ přihlašovacích údajů uživatele, se zabezpečeně váže na zařízení a používá biometriku nebo PIN kód.

## <a name="step-2---reduce-your-attack-surface"></a>Krok 2: snížit útoky

Zadaný pronikavostí ohrožení zabezpečení hesla, minimalizace útok ve vaší organizaci je důležité. Odstranění použijte protokoly starší, méně bezpečné, omezení položku přístupu odkazuje, a výkonu významnější ovládací prvek pro správu přístupu k prostředkům může pomoct snížit možností útoku.

### <a name="block-legacy-authentication"></a>Blok starší verze ověřování

Aplikace s využitím vlastní starší verze metody pro ověřování ve službě Azure AD a přístup k firemním datům, představují další riziko pro organizace. Příkladem aplikací pomocí starší verze ověřování jsou klienti POP3, IMAP4 nebo SMTP. Starší verze ověřování aplikace ověřit jménem uživatele a zabránit tím Upřesnit hodnocení zabezpečení Azure AD. Alternativní moderní ověřování, sníží bezpečnostní riziko, protože podporuje vícefaktorové ověřování a podmíněného přístupu. Doporučujeme následující tři akce:

1. Blok [starší verze ověřování, pokud používáte službu AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/access-control-policies-w2k12).
2. Instalační program [Sharepointu Online a Exchange Online aby nepoužívala moderní ověřování](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-no-modern-authentication).
3. Použití [zásady podmíněného přístupu blokovat starší verze ověřování](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-conditions#legacy-authentication).

### <a name="block-invalid-authentication-entry-points"></a>Blokovat ověřování Neplatný vstupní body

Pomocí mentality předpokládat porušení zabezpečení, by měla snížit dopad ohrožení uživatelských přihlašovacích údajů když k nim dojde. Pro každou aplikaci ve vašem prostředí vezměte v úvahu případy platné použití: které skupiny, které sítě, která zařízení a další prvky mají oprávnění – a blokovat zbývající. S [podmíněný přístup Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal), můžete řídit, jak ověřeného přístupu uživatele jejich aplikací a prostředků na základě určitých podmínek definujete.

### <a name="block-end-user-consent"></a>Blok svolení koncového uživatele

Standardně jsou všechny uživatele ve službě Azure AD můžou udělovat aplikacím využívajícím OAuth 2.0 a Microsoft identity [rozhraní pro udělování souhlasu](https://docs.microsoft.com/azure/active-directory/develop/consent-framework) oprávnění pro přístup k firemním datům. I když vyjadřuje umožnit uživatelům snadno získat užitečné aplikace, které se integrují s Microsoft 365 a Azure, může představovat riziko. není-li použít a pečlivě monitorovat. [Zakázat všechny operace vyjádření souhlasu uživatele budoucí](https://docs.microsoft.com/azure/active-directory/manage-apps/methods-for-removing-user-access) můžou pomoct snížit vaše plocha a toto riziko. Pokud svolení koncového uživatele je zakázáno předchozí udělení souhlasu bude stále zachované, ale musí provádět všechny operace budoucí souhlas správce. Před zakázáním této funkce se doporučuje zajistit, že se uživatelům pochopit, jak požádat o schválení správce pro nové aplikace; To by měl pomoci omezíte třecí plochy uživatele, minimalizovat objem podpory a ujistěte se, že uživatelé neregistrujte pro aplikace pomocí přihlašovacích údajů pro jiné než Azure AD.

### <a name="implement-azure-ad-privileged-identity-management"></a>Implementace služby Azure AD Privileged Identity Management

Jiný dopad "předpokládej chybu zabezpečení" je a potřebu minimalizovat pravděpodobnost, že ohrožení bezpečnosti účtu můžete pracují s privilegovanou roli. 

[Azure AD Privileged Identity Management (PIM)](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) minimalizovaná oprávnění k účtu tím, že vám pomůže:

* Identifikovat a spravovat uživatelé s rolí správce.
* Seznamte se s nepoužívá, nebo nadměrné oprávnění role, které byste měli odebrat.
* Vytvoření pravidla, která Ujistěte se, že privilegované role jsou chráněné službou Multi-Factor authentication.
* Vytvořte pravidla, abyste měli jistotu, že privilegované role jsou udělena pouze na dobu nutnou k provedení privilegovaných úlohy.

Povolit Azure AD PIM, a pak zobrazte uživatelů, kteří jsou přiřazené role pro správu a odeberte nepotřebné účty v těchto rolích. Pro zbývající uživatele s oprávněními přesunete z trvalé na oprávněné. A konečně navázat odpovídající zásady pro Ujistěte se, když potřebují pro získání přístupu k privilegovaným rolím, můžete provést bezpečně, řízení změn nezbytné.

Jako součást nasazení procesu privilegovaný účet, postupujte [osvědčený postup, chcete-li vytvořit aspoň dva účty pro nouzový](https://docs.microsoft.com/azure/active-directory/admin-roles-best-practices) k Ujistěte se, že máte přístup ke službě Azure AD, pokud sami uzamčení.

## <a name="step-3---automate-threat-response"></a>Krok 3: automatizovat reakce na hrozby

Azure Active Directory obsahuje řadu funkcí, které automaticky zachytit útoky, chcete-li odebrat latence mezi detekce a reakce. Můžete snížit náklady a rizika, když je zkrátit čas zločinci pomocí vložit samotné do vašeho prostředí. Tady jsou konkrétní kroky, které můžete provést.

### <a name="implement-user-risk-security-policy-using-azure-ad-identity-protection"></a>Implementujte zásady zabezpečení rizik uživatelů pomocí Azure AD Identity Protection

Riziko uživatele určuje pravděpodobnost, že došlo k napadení identity uživatele a se počítá na základě [uživatele, rizikové události](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) , které jsou přidruženy identity uživatele. Zásady rizik uživatelů se zásady podmíněného přístupu, který se vyhodnotí na úrovni rizika na konkrétního uživatele nebo skupinu. Podle nízká, střední, vysokou úroveň rizika a zásady je možné nakonfigurovat na blokování přístupu nebo vyžadovat změnu zabezpečeného hesla pomocí služby Multi-Factor authentication. Microsoft doporučuje vyžadovat změnu zabezpečeného hesla pro uživatele na vysokém riziku.

![Uživatelé označení příznakem rizika](media/azure-ad/azure-ad-sec-steps1.png)

### <a name="implement-sign-in-risk-policy-using-azure-ad-identity-protection"></a>Implementovat zásady rizik přihlašování pomocí Azure AD Identity Protection

Riziko přihlášení je pravděpodobnost, které někdo jiný než vlastník účtu se pokouší o přihlášení pomocí identity. A [zásady rizik přihlašování](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) se zásady podmíněného přístupu, který se vyhodnotí na úrovni rizika na konkrétního uživatele nebo skupinu. Vychází z úrovně rizika (vysoká nebo střední nebo nízká), je možné nakonfigurovat zásadu na blokování přístupu nebo vynutit ověřování Multi-Factor Authentication. Zajistěte, aby že vynutit ověřování Multi-Factor Authentication na střední nebo vyšší riziko přihlášení.

![Přihlášení z anonymních IP adres](media/azure-ad/azure-ad-sec-steps2.png)

## <a name="step-4---increase-your-awareness"></a>Krok 4 – zvýšení vašeho povědomí

Auditování a protokolování událostí týkajících se zabezpečení a souvisejících výstrah jsou základní součástí strategie efektivní ochrana. Protokoly zabezpečení a sestavy poskytují elektronických záznamů podezřelé aktivity a rozpoznat vzory, které může znamenat pokus o nebo úspěšné externí průniku sítě a útoky interních nápovědy. Můžete použít auditování k monitorování aktivity uživatelů, dodržování legislativních předpisů dokumentu, provádět forenzní analýzy a další. Výstrahy poskytují oznámení událostí zabezpečení.

### <a name="monitor-azure-ad"></a>Monitorování služby Azure AD

Funkce a služby Microsoft Azure umožňují konfigurovat zabezpečení, auditování a protokolování možnosti, které vám pomohou identifikovat mezery v vaše zásady a mechanismy zabezpečení a vyřešit tyto mezery, aby se zabránilo narušení. Můžete použít [Azure protokolování a auditování](https://docs.microsoft.com/azure/security/azure-log-audit) a používat [auditní sestavy aktivit na portálu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs).

### <a name="monitor-azure-ad-connect-health-in-hybrid-environments"></a>Monitorování služby Azure AD Connect Health v hybridním prostředí

[Sledování služby AD FS pomocí služby Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health-adfs) vám poskytne větší přehled o potenciální problémy a viditelnost útoky na infrastrukturu služby AD FS. Azure AD Connect Health poskytuje upozornění s podrobnostmi, postup řešení a odkazy na související dokumentaci; funkce analýzy využití pro několik metriky související s ověřovací provoz; sledování výkonu a sestavy.

![Azure AD Connect Health](media/azure-ad/azure-ad-sec-steps4.png)

### <a name="monitor-azure-ad-identity-protection-events"></a>Sledování událostí Azure AD Identity Protection

[Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) je oznámení, monitorování a vytváření sestav nástroje můžete použít k detekci potenciálních ohroženích zabezpečení dopadem na identity vaší organizace. Zjistí rizikové události, jako je například uniklými přihlašovacími údaji, neuskutečnitelné cesty a přihlášení z nakažených zařízení, anonymní IP adresy IP adresy přidružené k podezřelé aktivity a neznámými umístěními. Povolení upozornění pro příjem e-mailu ohrožených uživatelů a/nebo týdenní e-mailu digest.

Azure AD Identity Protection poskytuje dvě důležité sestavy, že měli byste sledovat každý den:
1. Sestavy rizikových přihlášení, bude přinášet přihlašovací aktivity uživatelů, které byste měli prozkoumat, nemusí mít legitimním vlastníkem provést přihlášení.
2. Sestavy rizikových uživatelů bude přinášet uživatelské účty, které možná ohrožené, jako je například uniklými přihlašovacími údaji, která byla zjištěna nebo přihlášení uživatele z různých míst příčinou událost neuskutečnitelnou cestu. 

![Uživatelé označení příznakem rizika](media/azure-ad/azure-ad-sec-steps3.png)

### <a name="audit-apps-and-consented-permissions"></a>Audit aplikace a odsouhlasené oprávnění

Uživatelé můžou být nalákaní, aby přejdete na ohrožené webové stránky nebo aplikace, které získají přístup k jejich profil informace a uživatelská data, jako jsou e-mailu. Škodlivý objekt actor pomocí odsouhlasené oprávnění obdržel šifrovat obsah poštovní schránky a vyžadují ransom budete moct znovu data poštovní schránky. [Správci byste měli zkontrolovat a auditovat](https://blogs.technet.microsoft.com/office365security/defending-against-illicit-consent-grants/) oprávnění udělená uživateli.

## <a name="step-5---enable-end-user-self-help"></a>Krok 5: povolení koncových uživatelů samoobslužné podpory

Co možná budete chtít pomocí produktivitu vyrovnat zabezpečení. Stejně tak z vaší cestě s způsob myšlení, že je nastavení základ pro zabezpečení v dlouhodobém horizontu blíží můžete odebrat řešit zádrhele spojené s z vaší organizace tím, že vaši uživatelé přitom bdělost. 

### <a name="implement-self-service-password-reset"></a>Implementace samoobslužného resetování hesla

Azure [samoobslužné resetování hesla (SSPR)](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started) nabízí jednoduchý způsob, správci IT mohou umožnit uživatelům resetovat nebo odemknout svá hesla nebo účty bez zásahu správce. Tento systém zahrnuje generování podrobných sestav, které sledují, kdy uživatelé k systému přistupují, spolu s oznámeními, která upozorňují na zneužití. 

### <a name="implement-self-service-group-management"></a>Implementace Samoobslužná správa skupin

Azure AD poskytuje schopnost spravovat přístup k prostředkům pomocí skupin zabezpečení a skupiny Office 365. Tyto skupiny můžou Spravovat vlastníky skupiny místo správci IT. Říká [Samoobslužná správa skupin](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management), tato funkce umožňuje vlastníkům, kteří nemají přiřazenou roli správce můžete vytvořit a spravovat bez nutnosti spoléhat se na správce pro zpracování požadavků na jejich skupiny.

### <a name="implement-azure-ad-access-reviews"></a>Kontroly přístupu implementace služby Azure AD

S [kontroly přístupu Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-azure-ad-controls-access-reviews-overview), můžete spravovat členství ve skupinách, přístup k podnikovým aplikacím a přiřazení privilegovaných rolí, abyste měli jistotu, Udržovat standard zabezpečení, které uživateli neposkytují přístup k rozšířené období čas, kdy ho nepotřebují.

## <a name="summary"></a>Souhrn

Existuje mnoho aspektů zabezpečené infrastruktury Identity, ale tento krok pět kontrolní seznam vám pomůže rychle provést identity bezpečnější a zabezpečené infrastruktury:

* Posílit vaše přihlašovací údaje.
* Omezení možností útoku.
* Můžete Automatizujte reakce na hrozby.
* Zvýšení vašeho povědomí o auditování a monitorování.
* Povolte více předvídatelný a úplné koncového uživatele zabezpečení s samoobslužné podpory.

Děkujeme za jak vážně trvat zabezpečení Identity a Doufáme, že tento dokument je užitečné plán bezpečnější otázkách pro vaši organizaci.

## <a name="next-steps"></a>Další postup
Pokud potřebujete pomoc s plánováním a nasazením doporučení, přečtěte si [nasazení plánů projektů Azure AD](https://aka.ms/deploymentplans) nápovědu.
