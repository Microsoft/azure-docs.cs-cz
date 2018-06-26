---
title: Pět kroky pro zabezpečení infrastruktury identity v Azure Active Directory
description: Tento dokument popisuje seznam důležité akce, které správci měli implementovat pomáhá jim zabezpečení organizace pomocí možnosti služby Azure AD
services: active-directory
author: martincoetzer
manager: manmeetb
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 06/18/2018
ms.author: martincoetzer
ms.openlocfilehash: b15fff6e868bfac973f9d2a7277f0fac1e29d845
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/25/2018
ms.locfileid: "36938388"
---
# <a name="five-steps-to-securing-your-identity-infrastructure"></a>Zabezpečení infrastruktury identity v pěti krocích

V případě, že čtete tento dokument jste vědomi zabezpečení. Budete pravděpodobně obvykle odpovědnost za zabezpečení vaší organizace. Pokud potřebujete přimět ostatní důležité zabezpečení, pošlete je přečíst nejnovější [Intelligence zabezpečení společnosti Microsoft sestavy](https://www.microsoft.com/security/intelligence-report).

Tento dokument vám pomůže získat bezpečnější postavení pomocí možností služby Azure Active Directory pomocí kontrolní seznam pěti krocích očkování vaší organizace před internetovými útoky.

Tento kontrolní seznam vám pomůže rychle nasadit kritické doporučené akce okamžitě chránit vaše organizace, která vysvětluje, jak:

* Posílení přihlašovacích údajů.
* Snižte útoku útoku.
* Automatizovat threat odpovědi.
* Zvýšení vaší povědomí o auditování a sledování.
* Povolte více předvídatelný a úplné koncového uživatele zabezpečení s návody pro Svépomocné řešení.

> [!NOTE]
> Některá doporučení v tomto dokumentu se vztahuje pouze na aplikace, které jsou nakonfigurovány pro použití služby Azure Active Directory jako jejich zprostředkovatele identity. Konfigurace aplikací pro jednotné přihlašování zaručuje výhod zásady přihlašovacích údajů, detekce hrozeb auditování, protokolování a dalších funkcí přidat do těchto aplikací. [Jednotné přihlašování pomocí služby Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-sso) je základem - všech těchto doporučení jsou založená.

## <a name="before-you-begin-protect-privileged-accounts-with-mfa"></a>Než začnete: ochrana privilegované účty pomocí vícefaktorového ověřování

Než začnete tento kontrolní seznam, ujistěte se, že jste si dojde k ohrožení zabezpečení při při čtení tento kontrolní seznam. Nejprve je třeba chránit privilegovaných účtů.

Útočníci, kteří získat kontrolu nad privilegované účty můžete provést strávíte škody, proto je důležité nejdřív chránit tyto účty. Povolit a vyžadovat [Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication) (MFA) pro všechny správce v organizaci pomocí [směrný plán ochranu](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-baseline-protection). Nemáte-li MFA, proveďte ho! Je to důležité.

Všechny sady? Můžeme začít v kontrolním seznamu.

## <a name="step-1---strengthen-your-credentials"></a>Krok 1 – posílení přihlašovacích údajů 

Většina porušení zabezpečení enterprise pocházejí pomocí účtu s jedním z několik metod, jako je heslo zařízení, opětovného přehrání porušení nebo phishing ohrožení zabezpečení. Další informace o těchto útoků v tomto videu:
> [!VIDEO https://channel9.msdn.com/events/Ignite/Microsoft-Ignite-Orlando-2017/BRK3016/player]

Pokud uživatelé ve vašem systému identity pomocí vytváření silných hesel a není posílení jejich pomocí služby Multi-Factor authentication, není řádu nebo když jste dojde k ohrožení zabezpečení – stačí "jak často."

### <a name="make-sure-your-organization-use-strong-authentication"></a>Zkontrolujte, že vaše organizace používá silné ověřování

Zadané frekvence hesel se uhádnout, phished krádeže s malwarem, nebo znovu použít, je důležité zálohovat heslo s určitou formu silné přihlašovací údaje – Další informace o [Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication).

### <a name="turn-off-traditional-complexity-expiration-rules-and-start-banning-commonly-attacked-passwords-instead"></a>Vypnout tradiční složitost pravidel vypršení platnosti a spusťte místo toho zakazování běžně attacked hesla

Mnoho organizací používá tradiční složitost (například speciální znaky) a pravidla pro vypršení platnosti hesla. Research společnosti Microsoft ukázalo, že tyto zásady jsou škodlivé způsobuje uživatelé zvolili hesla, které se snadněji tak snadno uhodnout.

Doporučení společnosti Microsoft, konzistentní s [pokynů NIST](https://pages.nist.gov/800-63-3/sp800-63b.html), jsou implementovat následující tři:

1. Vyžadovat, že hesla mají alespoň 8 znaků. Již není nutně lepší jako způsobí uživatelům zvolte předvídatelný hesla, uložení hesla v souborech nebo je zapište.
2. Zakázat vypršení platnosti pravidla, která jednotka uživatelé snadno odhadované hesla, jako **Summer2018!**.
3. Zakažte znak složení požadavky a zabránit uživatelům v výběr běžně attacked hesla, protože způsobí uživatelům zvolit nahrazení předvídatelný znaků v heslech.

Můžete použít [PowerShell, aby se zabránilo vypršení platnosti hesla](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy) na uživatele, pokud vytvoříte identit ve službě Azure AD přímo. Organizace pomocí místní AD službou Azure AD Connect pro synchronizaci identit do služby Azure AD (také označované jako hybridní nasazení), by měla implementovat místní [zásady pro hesla inteligentního](https://aka.ms/passwordguidance) pomocí [zásad skupiny domény nastavení](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/hh994572(v%3dws.10)) nebo [prostředí Windows PowerShell](https://docs.microsoft.com/powershell/module/addsadministration/set-addefaultdomainpasswordpolicy).

Azure Active Directory na [dynamické zakázaného heslo](https://docs.microsoft.com/azure/active-directory/active-directory-secure-passwords) funkce používá aktuální chování útočník uživatelům zabránit v nastavení hesla, které lze snadno uhodnout. Tato funkce je pořád zapnutý a organizace s hybridním nasazení tuto funkci využívat povolením [zpětný zápis hesla](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-writeback) nebo můžou nasadit [ochranu heslem Azure AD pro Windows Server Active Adresář](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad-on-premises). Ochrana heslem Azure AD blokuje uživatele z obecně výběr běžné hesla a vlastní hesla můžete nakonfigurovat.

### <a name="protect-against-leaked-credentials-and-add-resilience-against-outages"></a>Ochrana proti uniklé přihlašovací údaje a přidejte odolnost proti výpadkům

Pokud vaše organizace používá řešení hybridní identity, by měl povolit synchronizaci hodnoty hash hesla pro dvě z následujících důvodů:

* [Uživatelů s přihlašovacími údaji uniklé](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-risk-events) sestavy ve službě Správa služby Azure AD zobrazí upozornění na páry uživatelské jméno a heslo, které byly vystaveny na "tmavý webu." Neuvěřitelné objem hesla došlo k úniku prostřednictvím opakované použití na weby třetích stran, které jsou novější nedodržení phishing, malwaru a heslo. Microsoft najde mnoho z těchto úniku přihlašovacích údajů a se tak dozví, v této sestavě, pokud budou odpovídat pověření ve vaší organizaci – ale jenom Pokud jste [povolit synchronizaci hodnoty hash hesla](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization)!
* V případě výpadku na místě (například v útoku Ransomware, který) budete moct přejít do [cloudové ověřování pomocí synchronizaci hodnoty hash hesla](https://docs.microsoft.com/azure/security/azure-ad-choose-authn). Tato metoda zálohování ověřování bude možné i nadále přistupovat k aplikace nakonfigurovaná pro ověřování s Azure Active Directory, včetně služeb Office 365.

Další informace o [synchronizaci hodnoty hash hesla](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization) funguje.

### <a name="implement-ad-fs-extranet-lockout"></a>Uzamčení extranetu služby implementace AD FS

Organizace, které konfigurace aplikací k ověření přímo do služby Azure AD těžit z [inteligentní uzamčení služby Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-secure-passwords). Pokud používáte službu AD FS, implementaci AD FS [uzamčení extranetu](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-lockout-protection). Funkce uzamčení extranetu služby chrání před útoky hrubou silou, které cíl služby AD FS při současném brání uživatelům v právě uzamčen ve službě Active Directory.

### <a name="take-advantage-of-intrinsically-secure-easier-to-use-credentials"></a>Využít výhod vnitřně zabezpečení, jednodušší použít přihlašovací údaje

Pomocí [Windows Hello](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification), hesla můžete nahradit silné dvoufaktorové ověřování na počítačích a mobilních zařízení. Toto ověření se skládá z nový typ přihlašovacích údajů uživatele, který je vázaný na zařízení a používá biometrické nebo PIN kód.

## <a name="step-2---reduce-your-attack-surface"></a>Krok 2 – zmenšit prostor pro útoky

Zadané pronikavosti ohrožení bezpečnosti hesla, minimalizovat prostor pro útoky ve vaší organizaci je velmi důležité. Odstranění použití protokolů starší, méně bezpečné, omezení přístupu vstupní body a výkonu větších řízení přístupu k prostředkům pro správu může pomoct snížit útoku.

### <a name="block-legacy-authentication"></a>Blok starší verze ověřování

Aplikace používající vlastní starší verze metody ověřování s Azure AD a přístup k firemním datům, představovat další riziko pro organizace. Příkladem aplikací pomocí starší verze ověřování jsou klienti POP3, IMAP4 nebo SMTP. Starší verze ověřování aplikace ověřit jménem uživatele a zabránit Azure AD dělat rozšířené zabezpečení hodnocení. Alternativní, moderní ověřování, sníží ohrožení zabezpečení, protože ji podporuje vícefaktorové ověřování a podmíněného přístupu. Doporučujeme následující tři akce:

1. Blok [starší verze ověřování, pokud používáte službu AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/access-control-policies-w2k12).
2. Instalační program [SharePoint Online a Exchange Online používat moderní ověřování](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-no-modern-authentication).
3. Použití [zásady podmíněného přístupu blokovat starší verze ověřování](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-conditions#legacy-authentication).

### <a name="block-invalid-authentication-entry-points"></a>Blok neplatným ověřovacím vstupní body

Pomocí mentality porušení assume, by měl snížit dopad ohroženými uživatelskými pověření reálném. Pro každou aplikaci ve vašem prostředí zvažte případy použití platné: které skupiny, které sítě, která zařízení a další elementy jsou oprávnění – pak blokovat zbývající. Vezměte v potaz omezit použití [účty vysoce privilegovaných nebo služba](https://docs.microsoft.com/azure/active-directory/admin-roles-best-practices). S [podmíněného přístupu Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal), můžete řídit, jak oprávněným uživatelům přístup k aplikacím a prostředkům na základě určitých podmínek, které definujete.

Věnujte zvláštní pozornost účty služby (účty použité k provádění úloh automatizovaně). Použití podmíněného přístupu zajistit tyto účty lze spustit jen u služby, z IP adresy, a v denní dobu, což je vhodné.

### <a name="implement-azure-ad-privileged-identity-management"></a>Implementace Azure AD Privileged Identity Management

Jiný dopad "předpokládá porušení" je nutné snížit pravděpodobnost, které ohrožení bezpečnosti účtu můžete pracovat s privilegované role. 

[Azure AD Privileged Identity Management (PIM)](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) minimalizovaná oprávnění k účtu tím, že pomáhá vám pomůže:

* Identifikovat a spravovat přiřazeno správní role uživatelů.
* Seznamte se nepoužívá, nebo příliš oprávnění rolí, které byste měli odebrat.
* Vytvoření pravidla, která Ujistěte se, že privilegované role jsou chráněné službou Multi-Factor authentication.
* Vytvoření pravidla a ujistěte se, že privilegované role jsou udělena pouze na dobu nutnou k provedení privilegovaných úlohy.

Povolit Azure AD PIM a potom zobrazit uživatele, kteří jsou přiřazeny role pro správu a odstranit nepotřebné účty v těchto rolí. Pro zbývající privilegovaní uživatelé přesuňte je z trvalé vhodné. Nakonec stanovit příslušné zásady zajistit, když potřebují k získání přístupu k těchto privilegované role, mohou provádět tak bezpečně.

Jako součást nasazení váš proces privilegovaného účtu, postupujte podle [nejvhodnější vytvořit aspoň dva účty pro nouzový](https://docs.microsoft.com/azure/active-directory/admin-roles-best-practices) a ujistěte se, pokud sami zablokovat se mít přístup ke službě Azure AD.

## <a name="step-3---automate-threat-response"></a>Krok 3 – automatizovat threat odpovědi

Azure Active Directory má mnoho možností, které automaticky intercept útoků, odeberte latenci mezi detekce a odpovědi. Můžete snížit náklady a rizik, když snížit čas pachatelů pomocí sami vložení do vašeho prostředí. Zde jsou konkrétní kroky, které můžete provést.

### <a name="implement-user-risk-security-policy-using-azure-ad-identity-protection"></a>Implementovat zásady zabezpečení riziko uživatele pomocí Azure AD Identity Protection

Riziko uživatele označuje pravděpodobnost identity uživatele došlo k ohrožení zabezpečení a se počítá na základě [uživatele rizikových událostech](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) přidružené identity uživatele. Zásady uživatele riziko je zásadu podmíněného přístupu, která vyhodnotí úroveň rizika na konkrétního uživatele nebo skupinu. Podle toho, nízká, střední, vysoká úroveň rizika, zásady můžete nakonfigurovat na blokování přístupu nebo vyžadovat změnu zabezpečeného hesla pomocí služby Multi-Factor authentication. Společnosti Microsoft doporučuje se vyžadovat změnu zabezpečeného hesla pro uživatele na vysoce rizikové.

![Uživatelé označení příznakem rizika](media/azure-ad/azure-ad-sec-steps1.png)

### <a name="implement-sign-in-risk-policy-using-azure-ad-identity-protection"></a>Implementujte zásady riziko přihlášení pomocí Azure AD Identity Protection

Riziko přihlášení je pravděpodobnost, které někdo jiný než vlastníka účtu se pokouší přihlásit pomocí identity. A [zásady přihlášení riziko](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) je zásada podmíněného přístupu, která vyhodnotí úroveň rizika na konkrétního uživatele nebo skupinu. Podle toho, úroveň rizika (vysokou/střední nebo nízká), lze nakonfigurovat zásadu na blokování přístupu nebo vynutit službu Multi-Factor authentication. Ujistěte se, že můžete vynutit službu Multi-Factor authentication na střední nebo vyšší riziko přihlášení.

![Přihlášení z anonymních IP adres](media/azure-ad/azure-ad-sec-steps2.png)

## <a name="step-4---increase-your-awareness"></a>Krok 4 – zvýšení vaší sledování

Auditování a protokolování událostí souvisejících se zabezpečením a souvisejících výstrah jsou nezbytné součásti strategie efektivní ochrana. Protokoly zabezpečení a sestavy poskytují záznam elektronické podezřelé aktivity a nápovědu zjišťování struktur, které můžou signalizovat úspěšné nebo neúspěšné pokusy o externí průnikům sítě a interní útoky. Auditování můžete použít k monitorování aktivity uživatelů, dodržování legislativních dokumentu, provádět forenzní analýzu a další. Výstrahy poskytnout oznámení o události zabezpečení.

### <a name="monitor-azure-ad"></a>Monitorování služby Azure AD

Funkce a služby Microsoft Azure poskytují konfigurovat zabezpečení, auditování a protokolování možnosti vám pomohou zjistí nesrovnalosti v vaše zásady a mechanismy zabezpečení a vyřešit tyto mezery, aby se zabránilo narušení. Můžete použít [Azure protokolování a auditování](https://docs.microsoft.com/azure/security/azure-log-audit) a používat [Audit aktivity sestav na portálu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs).

### <a name="monitor-azure-ad-connect-health-in-hybrid-environments"></a>Monitorování Azure AD Connect Health v hybridních prostředích

[Monitorování služby AD FS pomocí Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health-adfs) vám poskytne větší přehled o potenciální problémy a viditelnost útoky na infrastrukturu služby AD FS. Azure AD Connect Health poskytuje výstrahy s podrobnostmi, postup řešení a odkazy na související dokumentaci; funkce analýzy využití pro několik metriky týkající se provozu ověřování; monitorování výkonu a sestavy.

![Azure AD Connect Health](media/azure-ad/azure-ad-sec-steps4.png)

### <a name="monitor-azure-ad-identity-protection-events"></a>Sledování událostí Azure AD Identity Protection

[Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) je oznámení, monitorování a vytváření sestav nástroje můžete použít k detekci potenciální ohrožení zabezpečení, které ovlivňují identity ve vaší organizaci. Zjistí riziko událostmi, například uniklé přihlašovací údaje, neuskutečnitelná cesta a přihlášení z nakažených zařízení, anonymních IP adres, IP adresy přidružené podezřelé aktivity a neznámé umístění. Povolte oznámení výstrah pro příjem e-mailu uživatelů hrozí nebo týdenní ověřování algoritmem digest, e-mailu.

![Uživatelé označení příznakem rizika](media/azure-ad/azure-ad-sec-steps3.png)

## <a name="step-5---enable-end-user-self-help"></a>Krok 5 – povolení koncového uživatele návody pro Svépomocné řešení

Co nejvíce budete chtít vyvážit zabezpečení s produktivitu. Podle stejné blíží vám dobře slouží s přistupovat, že je nastavení základ pro zabezpečení dlouhodobě můžete odebrat třecí z vaší organizace tak, umožňují vaší uživatelům při zbývající bdělost. 

### <a name="implement-self-service-password-reset"></a>Implementovat samoobslužné resetování hesla

Azure [samoobslužného obnovení hesla (SSPR)](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started) nabízí jednoduchý znamená pro správce IT, aby uživatelé mohli resetovat nebo odemknout jejich hesla nebo účty bez zásahu správce. Tento systém zahrnuje generování podrobných sestav, které sledují, kdy uživatelé k systému přistupují, spolu s oznámeními, která upozorňují na zneužití. 

### <a name="implement-self-service-group-management"></a>Implementace Samoobslužná správa skupin

Azure AD poskytuje schopnost spravovat přístup k prostředkům pomocí skupin zabezpečení a skupiny Office 365. Tyto skupiny můžete spravovat skupiny vlastníky místo správci IT. Označuje jako [Samoobslužná správa skupin](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management), tato funkce umožňuje vlastníkům skupin, kteří nemají přiřazenu roli správce k vytváření a Správa skupin bez spoléhání na správcům zpracování svých požadavků.

### <a name="implement-azure-ad-access-reviews"></a>Recenze přístup implementace služby Azure AD

S [zkontroluje přístup k Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-azure-ad-controls-access-reviews-overview), můžete spravovat členství ve skupinách, přístup k podnikové aplikace a přiřazení privilegované role, abyste měli jistotu, Udržovat standard zabezpečení, který není uživatelům umožňují přístup pro rozšířené období čas, když ho nepotřebují.

## <a name="summary"></a>Souhrn

Existuje mnoho aspektů zabezpečení infrastruktury Identity, ale tento kontrolní seznam pěti krocích vám pomůže rychle provádět infrastruktury identity bezpečnější a zabezpečení:

* Posílení přihlašovacích údajů.
* Snižte útoku útoku.
* Automatizovat threat odpovědi.
* Zvýšení vaší povědomí o auditování a sledování.
* Povolte více předvídatelný a úplné koncového uživatele zabezpečení s návody pro Svépomocné řešení.

Děkujeme za jak vážně trvat zabezpečení Identity a Doufáme, že tento dokument je užitečné plán pro bezpečnější postavení pro vaši organizaci.

## <a name="next-steps"></a>Další postup
Pokud potřebujete pomoc s plánováním a nasazením doporučení, podívejte se na [Azure AD projektu nasazení plány](http://aka.ms/deploymentplans) nápovědu.
