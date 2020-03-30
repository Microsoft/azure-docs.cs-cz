---
title: Zabezpečení infrastruktury identit Azure AD
titleSuffix: Azure Active Directory
description: Tento dokument popisuje seznam důležitých akcí, které by měli správci implementovat, aby jim pomohli zabezpečit svou organizaci pomocí možností Azure AD.
author: martincoetzer
manager: manmeetb
tags: azuread
ms.service: security
ms.subservice: security-fundamentals
ms.topic: conceptual
ms.workload: identity
ms.date: 01/29/2020
ms.author: martinco
ms.openlocfilehash: e0db8edfdfa380697a1d8d7e262a7a84da2fb7d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77565532"
---
# <a name="five-steps-to-securing-your-identity-infrastructure"></a>Pět kroků k zabezpečení infrastruktury identit

Pokud čtete tento dokument, jste si vědomi významu zabezpečení. Pravděpodobně již nesete odpovědnost za zabezpečení vaší organizace. Pokud potřebujete přesvědčit ostatní o důležitosti zabezpečení, pošlete je, aby si přečetli nejnovější [zprávu Microsoft Security Intelligence](https://go.microsoft.com/fwlink/p/?linkid=2073747).

Tento dokument vám pomůže získat bezpečnější držení těla pomocí funkcí služby Azure Active Directory pomocí kontrolního seznamu v pěti krocích k naočkování vaší organizace proti kybernetickým útokům.

Tento kontrolní seznam vám pomůže rychle nasadit kritické doporučené akce k okamžité ochraně vaší organizace tím, že vysvětlí, jak:

* Posilte své pověření.
* Zmenšete oblast útoku.
* Automatizujte reakci na hrozby.
* Využijte cloudovou inteligenci.
* Povolte samoobslužnou službu koncového uživatele.

Při čtení tohoto kontrolního seznamu se ujistěte, že máte přehled o tom, které funkce a kroky jsou dokončeny.

> [!NOTE]
> Mnoho doporučení v tomto dokumentu platí pouze pro aplikace, které jsou nakonfigurované tak, aby používaly Azure Active Directory jako svého zprostředkovatele identity. Konfigurace aplikací pro jednotné přihlašování zajišťuje výhody zásad pověření, detekce hrozeb, auditování, protokolování a dalších funkcí, které tyto aplikace přidávají. [Jednotné přihlašování prostřednictvím služby Azure Active Directory](../../active-directory/manage-apps/configure-single-sign-on-non-gallery-applications.md) je základem – na kterém jsou založena všechna tato doporučení.

Doporučení v tomto dokumentu jsou v souladu s [skóre zabezpečení identity](../../active-directory/fundamentals/identity-secure-score.md), automatizované posouzení konfigurace zabezpečení identity vašeho klienta Azure AD. Organizace můžou na portálu Azure AD najít mezery v aktuální konfiguraci zabezpečení, aby se ujistily, že dodržují aktuální [doporučené postupy](identity-management-best-practices.md) Microsoftu pro zabezpečení. Implementace každého doporučení na stránce Zabezpečené skóre zvýší vaše skóre a umožní vám sledovat váš pokrok a navíc vám pomůže porovnat implementaci s jinými organizacemi podobné velikosti nebo vaším odvětvím.

![Skóre zabezpečení identity](./media/steps-secure-identity/azure-ad-sec-steps0.png)

> [!NOTE]
> Mnoho funkcí popsaných zde vyžadují předplatné Azure AD Premium, zatímco některé jsou zdarma. Další informace najdete v našem kontrolním seznamu [cen služby Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/) a [kontrolním seznamu nasazení služby Azure AD.](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-checklist-p2)

## <a name="before-you-begin-protect-privileged-accounts-with-mfa"></a>Než začnete: Ochrana privilegovaných účtů pomocí vícefaktorové hospo-

Než začnete tento kontrolní seznam, ujistěte se, že nedostanete kompromis, když čtete tento kontrolní seznam. Nejprve musíte chránit své privilegované účty.

Útočníci, kteří získají kontrolu nad privilegovanými účty, mohou způsobit obrovské škody, takže je důležité nejprve tyto účty chránit. Povolte a vyžadují [Azure Vícefaktorové ověřování](../../active-directory/authentication/multi-factor-authentication.md) (MFA) pro všechny správce ve vaší organizaci pomocí [výchozích zabezpečení Azure AD](../../active-directory/fundamentals/concept-fundamentals-security-defaults.md) nebo [podmíněného přístupu](../../active-directory/conditional-access/plan-conditional-access.md). Pokud jste neimplementovali Vícefaktorové finanční hod, udělejte to teď! Je to tak důležité.

Všechno připraveno? Začněme na kontrolním seznamu.

## <a name="step-1---strengthen-your-credentials"></a>Krok 1 - Posílení vašich přihlašovacích údajů

Většina narušení zabezpečení podniku pochází z účtu, který je ohrožen jednou z několika metod, jako je sprej s heslem, opětovné přehrání porušení zabezpečení nebo phishing. Další informace o těchto útocích naleznete v tomto videu (45 min)
> [!VIDEO https://www.youtube.com/embed/uy0j1_t5Hd4]

### <a name="make-sure-your-organization-uses-strong-authentication"></a>Ujistěte se, že vaše organizace používá silné ověřování

Vzhledem k četnosti uhádnutých, míchaných, odcizených pomocí malwaru nebo opakovaného použití je důležité heslo zálohovat pomocí nějaké formy silného pověření – přečtěte si další informace o [azure multifaktorovém ověřování](../../active-directory/authentication/multi-factor-authentication.md).

Chcete-li snadno povolit základní úroveň zabezpečení identity, můžete použít povolení jedním kliknutím s [výchozími hodnotami zabezpečení Azure AD](../../active-directory/fundamentals/concept-fundamentals-security-defaults.md). Výchozí nastavení zabezpečení vynucují Azure MFA pro všechny uživatele v tenantovi a blokuje přihlášení ze starších protokolů pro celý tenant.

### <a name="start-banning-commonly-attacked-passwords-and-turn-off-traditional-complexity-and-expiration-rules"></a>Začněte zakazovat běžně napadená hesla a vypněte tradiční složitost a pravidla vypršení platnosti.

Mnoho organizací používá tradiční složitost (vyžadující speciální znaky, čísla, velká a malá písmena) a pravidla vypršení platnosti hesla. [Výzkum společnosti Microsoft ukázal,](https://aka.ms/passwordguidance) že tyto zásady způsobují, že uživatelé si vybírají hesla, která lze snadněji uhodnout.

Funkce [dynamického zakázaného hesla](../../active-directory/authentication/concept-password-ban-bad.md) azure ad používá aktuální chování útočníka, aby zabránila uživatelům v nastavení hesel, která lze snadno uhodnout. Tato funkce je vždy zapnutá, když jsou uživatelé vytvořeni v cloudu, ale je teď k dispozici také pro hybridní organizace při nasazení [ochrany heslem Azure AD pro službu Windows Server Active Directory](../../active-directory/authentication/concept-password-ban-bad-on-premises.md). Ochrana heslem Azure AD blokuje uživatelům výběr těchto běžných hesel a může být rozšířena tak, aby blokovala heslo obsahující vlastní klíčová slova, která zadáte. Uživatelům můžete například zabránit ve výběru hesel obsahujících názvy produktů vaší společnosti nebo místního sportovního týmu.

Společnost Microsoft doporučuje přijmout následující moderní zásady hesel založené na [pokynech pro nist](https://pages.nist.gov/800-63-3/sp800-63b.html):

1. Vyžadovat hesla mají alespoň 8 znaků. Delší nemusí být nutně lepší, protože způsobují, že uživatelé zvolte předvídatelná hesla, ukládají hesla do souborů nebo je zapisují.
2. Zakažte pravidla vypršení platnosti, která pohánějí uživatele ke snadno uhádnutým heslům, jako je **Spring2019!**
3. Zakažte požadavky na složení znaků a zabraňte uživatelům ve výběru běžně napadených hesel, protože způsobí, že uživatelé zvolí předvídatelné nahrazení znaků v heslech.

[Pomocí PowerShellu můžete zabránit vypršení platnosti hesel](../../active-directory/authentication/concept-sspr-policy.md) pro uživatele, pokud přímo vytvoříte identity ve službě Azure AD. Hybridní organizace by měly tyto zásady implementovat pomocí [nastavení zásad skupiny domén](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/hh994572(v%3dws.10)) nebo prostředí Windows [PowerShell](https://docs.microsoft.com/powershell/module/addsadministration/set-addefaultdomainpasswordpolicy).

### <a name="protect-against-leaked-credentials-and-add-resilience-against-outages"></a>Ochrana před úniky přihlašovacích údajů a přidání odolnosti proti výpadkům

Pokud vaše organizace používá řešení hybridní identity s předávacím ověřováním nebo federací, měli byste povolit synchronizaci hash hesel z následujících dvou důvodů:

* Sestava [Uživatelé s uniklými přihlašovacími údaji](../../active-directory/reports-monitoring/concept-risk-events.md) ve správě Azure AD vás upozorní na dvojice uživatelských jmen a hesel, které byly vystaveny na "tmavém webu". Neuvěřitelný objem hesel unikl prostřednictvím phishingu, malwaru a opětovného použití hesla na webech třetích stran, které byly později porušeny. Společnost Microsoft najde mnoho z těchto uniklých přihlašovacích údajů a v této sestavě vám řekne, zda odpovídají pověřením ve vaší organizaci – ale pouze v případě, že [povolíte synchronizaci hash hesla](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md)!
* V případě místního výpadku (například při útoku ransomware) můžete přepnout na [cloudové ověřování pomocí synchronizace hash hesla](choose-ad-authn.md). Tato metoda ověřování zálohování vám umožní pokračovat v přístupu k aplikacím nakonfigurovaným pro ověřování pomocí služby Azure Active Directory, včetně Office 365. V takovém případě se pracovníci IT nebudou muset uchýlit k osobním e-mailovým účtům, aby museli sdílet data, dokud nebude vyřešen místní výpadek.

Přečtěte si další informace o tom, jak funguje [synchronizace hash hesel.](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md)

> [!NOTE]
> Pokud povolíte synchronizaci hodnot hash hesla a používáte služby Azure AD Domain, budou hashy protokolu Kerberos (AES 256) a volitelně NTLM (RC4, bez soli) také šifrovány a synchronizovány do Služby Azure AD.

### <a name="implement-ad-fs-extranet-smart-lockout"></a>Implementace inteligentního uzamčení extranetu ad FS extranet

Organizace, které konfigurují aplikace pro ověřování přímo do Azure AD těžit z [Azure AD inteligentní uzamčení](../../active-directory/authentication/concept-sspr-howitworks.md). Pokud používáte službu AD FS v systému Windows Server 2012R2, implementujte [ochranu uzamčení extranet](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-soft-lockout-protection)služby AD FS . Pokud používáte službu AD FS na Windows Serveru 2016, implementujte [extranet smart lockout](https://support.microsoft.com/help/4096478/extranet-smart-lockout-feature-in-windows-server-2016). Uzamčení inteligentníextrasítě služby AD FS chrání před útoky hrubou silou, které se zaměřují na službu AD FS a zároveň brání uživatelům v uzamčení ve službě Active Directory.

### <a name="take-advantage-of-intrinsically-secure-easier-to-use-credentials"></a>Využijte výhod jiskrově bezpečných a snadněji použitelných přihlašovacích údajů

Pomocí [Windows Hello](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification)můžete hesla nahradit silným dvoufaktorovým ověřováním na počítačích a mobilních zařízeních. Toto ověřování se skládá z nového typu pověření uživatele, který je bezpečně vázán na zařízení a používá biometrické nebo PIN kódy.

## <a name="step-2---reduce-your-attack-surface"></a>Krok 2 – Zmenšete povrch útoku

Vzhledem k všudypřítomnosti ohrožení zabezpečení heslem je minimalizace útočné plochy ve vaší organizaci kritická. Odstranění použití starších, méně zabezpečených protokolů, omezení přístupových vstupních bodů a uplatnění významnější kontroly přístupu správce k prostředkům může pomoci snížit oblast útoku.

### <a name="block-legacy-authentication"></a>Blokování starší verze ověřování

Aplikace, které používají vlastní starší metody k ověřování pomocí Azure AD a přístupu k firemním datům, představují další riziko pro organizace. Příklady aplikací využívajících starší verze ověřování jsou klienti POP3, IMAP4 nebo SMTP. Starší verze ověřovacích aplikací se ověřují jménem uživatele a brání službě Azure AD v pokročilých hodnoceních zabezpečení. Alternativní, moderní ověřování sníží vaše bezpečnostní riziko, protože podporuje vícefaktorové ověřování a podmíněný přístup. Doporučujeme následující tři akce:

1. Blokovat [starší verze ověřování, pokud používáte službu AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/access-control-policies-w2k12).
2. Nastavení [SharePointu Online a Exchange Online pro použití moderního ověřování](../../active-directory/conditional-access/conditional-access-for-exo-and-spo.md).
3. Pokud máte Azure AD Premium, použijte [zásady podmíněného přístupu](../../active-directory/conditional-access/overview.md) k zablokování starších verzí ověřování, jinak použijte [výchozí nastavení zabezpečení Azure AD](../../active-directory/fundamentals/concept-fundamentals-security-defaults.md).

### <a name="block-invalid-authentication-entry-points"></a>Blokovat neplatné vstupní body ověřování

Pomocí mentality narušení převzetí byste měli snížit dopad ohrožených přihlašovacích údajů uživatele, když k nim dojde. Pro každou aplikaci ve vašem prostředí zvažte platné případy použití: které skupiny, které sítě, která zařízení a další prvky jsou autorizovány – pak zbytek zablokujte. S [Podmíněný přístup Azure AD](../../active-directory/conditional-access/overview.md)můžete řídit, jak oprávnění uživatelé přístup ke svým aplikacím a prostředkům na základě konkrétních podmínek, které definujete.

### <a name="restrict-user-consent-operations"></a>Omezit operace souhlasu uživatele

Je důležité pochopit různé [prostředí schválení aplikace Azure AD](https://docs.microsoft.com/azure/active-directory/develop/application-consent-experience), [typy oprávnění a souhlasu](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent)a jejich důsledky pro stav zabezpečení vaší organizace. Ve výchozím nastavení mohou všichni uživatelé ve službě Azure AD udělit aplikacím, které využívají platformu identit Microsoftu pro přístup k datům vaší organizace. I když umožňuje uživatelům souhlas sám umožňuje uživatelům snadno získat užitečné aplikace, které integrují s Microsoft 365, Azure a další služby, může představovat riziko, pokud nejsou používány a pečlivě sledovány.

Společnost Microsoft doporučuje [zakázat budoucí operace se souhlasem uživatele,](https://docs.microsoft.com/azure/active-directory/manage-apps/methods-for-removing-user-access#i-want-to-disable-all-future-user-consent-operations-to-any-application) aby se snížila plocha povrchu a zmírnilo toto riziko. Pokud je souhlas koncového uživatele zakázán, předchozí udělení souhlasu bude stále dodrženo, ale všechny budoucí operace souhlasu musí být provedeny správcem. Souhlas správce mohou uživatelé požadovat prostřednictvím [integrovaného pracovního postupu žádosti o souhlas správce](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-admin-consent-workflow) nebo prostřednictvím vlastních procesů podpory. Před zakázáním souhlasu koncových uživatelů použijte naše [doporučení](https://docs.microsoft.com/azure/active-directory/manage-apps/manage-consent-requests) k plánování této změny ve vaší organizaci. U aplikací, které chcete umožnit přístup všem uživatelům, zvažte [udělení souhlasu jménem všech uživatelů](https://docs.microsoft.com/azure/active-directory/develop/v2-admin-consent)a ujistěte se, že uživatelé, kteří dosud nesouhlasili jednotlivě, budou mít přístup k aplikaci. Pokud nechcete, aby tyto aplikace byly dostupné všem uživatelům ve všech scénářích, použijte [přiřazení aplikací](https://docs.microsoft.com/azure/active-directory/manage-apps/methods-for-assigning-users-and-groups) a [podmíněný přístup](https://docs.microsoft.com/azure/active-directory/conditional-access/overview) k omezení přístupu uživatelů k aplikacím.

Ujistěte se, že uživatelé mohou požádat o schválení správce pro nové aplikace, aby se snížilo tření uživatelů, minimalizovalobjem podpory a zabránilo uživatelům v registraci pro aplikace pomocí přihlašovacích údajů jiného typu Azure AD. Jakmile regulujete operace se souhlasem, správci by měli pravidelně kontrolovat oprávnění aplikace a souhlasně.


### <a name="implement-azure-ad-privileged-identity-management"></a>Implementace správy privilegovaných identit Azure AD

Dalším dopadem "převzetí porušení" je potřeba minimalizovat pravděpodobnost, že kompromitovaný účet může fungovat s privilegovanou rolí.

[Azure AD Privileged Identity Management (PIM)](../../active-directory/privileged-identity-management/pim-configure.md) vám pomůže minimalizovat oprávnění účtu tím, že vám pomůže:

* Identifikujte a spravujte uživatele přiřazené k rolím pro správu.
* Porozumět nepoužívaným nebo nadměrným rolím oprávnění, které byste měli odebrat.
* Vytvořte pravidla, která zajistí, že privilegované role jsou chráněny vícefaktorovým ověřováním.
* Vytvořte pravidla, která zajistí, že privilegované role budou uděleny pouze dostatečně dlouho na splnění privilegovaného úkolu.

Povolte Azure AD PIM, pak zobrazit uživatele, kterým jsou přiřazeny role správy a odebrat nepotřebné účty v těchto rolích. Pro zbývající privilegované uživatele je přesuňte z trvalých na způsobilé. Nakonec vytvořte vhodné zásady, které zajistí, že když potřebují získat přístup k těmto privilegovaným rolím, mohou tak učinit bezpečně s nezbytným řízením změn.

V rámci nasazení privilegovaného účtu procesu postupujte podle [osvědčených postupů k vytvoření alespoň dva nouzové účty](../../active-directory/users-groups-roles/directory-admin-roles-secure.md) a ujistěte se, že máte stále přístup k Azure AD, pokud se zamknete.

## <a name="step-3---automate-threat-response"></a>Krok 3 – Automatizace reakce na hrozby

Služba Azure Active Directory má mnoho funkcí, které automaticky zachycují útoky, aby byla odebrat latenci mezi detekcí a odezvou. Můžete snížit náklady a rizika, když snížíte čas, který zločinci používají k vložení do vašeho prostředí. Zde jsou konkrétní kroky, které můžete podniknout.

### <a name="implement-user-risk-security-policy-using-azure-ad-identity-protection"></a>Implementace zásad zabezpečení uživatelských rizik pomocí ochrany identity Azure AD

Riziko uživatele označuje pravděpodobnost, že identita uživatele byla ohrožena, a je vypočtena na základě [detekce rizik uživatelů,](../../active-directory/identity-protection/overview.md) které jsou přidruženy k identitě uživatele. Zásady rizika uživatele je zásada podmíněného přístupu, která vyhodnocuje úroveň rizika pro konkrétního uživatele nebo skupinu. Na základě nízké, střední, vysoké úrovně rizika lze zásadu nakonfigurovat tak, aby blokovala přístup nebo vyžadovala bezpečnou změnu hesla pomocí vícefaktorového ověřování. Doporučení společnosti Microsoft je vyžadovat bezpečnou změnu hesla pro uživatele s vysokým rizikem.

![Uživatelé označení příznakem rizika](./media/steps-secure-identity/azure-ad-sec-steps1.png)

### <a name="implement-sign-in-risk-policy-using-azure-ad-identity-protection"></a>Implementujte zásady rizik přihlášení pomocí azure ad identity protection

Riziko přihlášení je pravděpodobnost, že se pomocí identity pokusí přihlásit někdo jiný než vlastník účtu. [Zásady rizika přihlášení](../../active-directory/identity-protection/overview.md) je zásada podmíněného přístupu, která vyhodnocuje úroveň rizika pro konkrétního uživatele nebo skupinu. Na základě úrovně rizika (vysoká/střední/nízká) může být zásada nakonfigurována tak, aby blokovala přístup nebo vynucovala vícefaktorové ověřování. Ujistěte se, že vynutíte vícefaktorové ověřování na střední nebo vyšší riziko přihlášení.

![Přihlášení z anonymních IP služeb](./media/steps-secure-identity/azure-ad-sec-steps2.png)

## <a name="step-4---utilize-cloud-intelligence"></a>Krok 4 – Využití cloudové inteligence

Auditování a protokolování událostí souvisejících se zabezpečením a souvisejících výstrah jsou základními součástmi účinné strategie ochrany. Protokoly zabezpečení a sestavy poskytují elektronický záznam podezřelých aktivit a pomáhají vám odhalit vzorce, které mohou naznačovat pokus o nebo úspěšné externí proniknutí do sítě a interní útoky. Auditování můžete použít ke sledování aktivity uživatelů, dodržování předpisů dokumentů, forenzní analýze a dalším činnostem. Výstrahy poskytují oznámení o událostech zabezpečení.

### <a name="monitor-azure-ad"></a>Monitorování azure reklamy

Služby a funkce Microsoft Azure vám poskytují konfigurovatelné možnosti auditování a protokolování zabezpečení, které vám pomohou identifikovat mezery ve vašich zásadách a mechanismech zabezpečení a tyto mezery řešit, aby se zabránilo porušení. [Protokolování a auditování Azure můžete](log-audit.md) použít a sestavy aktivit auditování můžete použít na [portálu Azure Active Directory](../../active-directory/reports-monitoring/concept-audit-logs.md).

### <a name="monitor-azure-ad-connect-health-in-hybrid-environments"></a>Monitorování stavu připojení Azure AD v hybridních prostředích

[Monitorování služby AD FS pomocí služby Azure AD Connect Health](../../active-directory/hybrid/how-to-connect-health-adfs.md) vám poskytne lepší přehled o potenciálních problémech a viditelnosti útoků na infrastrukturu služby AD FS. Azure AD Connect Health poskytuje výstrahy s podrobnostmi, kroky řešení a odkazy na související dokumentaci; analýzy využití pro několik metrik souvisejících s autentizačním provozem; sledování výkonnosti a zprávy.

![Azure AD Connect Health](./media/steps-secure-identity/azure-ad-sec-steps4.png)

### <a name="monitor-azure-ad-identity-protection-events"></a>Monitorování událostí ochrany identity Azure AD

[Azure AD Identity Protection](../../active-directory/identity-protection/overview.md) je nástroj pro oznamování, monitorování a vytváření sestav, který můžete použít ke zjišťování potenciálních chyb zabezpečení ovlivňujících identitu vaší organizace. Detekuje detekci rizik, jako jsou uniklá pověření, nemožné cestování a přihlášení z infikovaných zařízení, anonymní IP adresy, IP adresy spojené s podezřelou aktivitou a neznámá místa. Povolte upozornění na příjem e-mailů od ohrožených uživatelů a/nebo týdenního e-mailu s přehledem.

Azure AD Identity Protection poskytuje dvě důležité sestavy, které byste měli sledovat denně:
1. Rizikové přihlašovací sestavy zobrazí aktivity přihlášení uživatele, které byste měli prozkoumat, legitimní vlastník pravděpodobně neprovedl přihlášení.
2. Rizikové uživatelské sestavy budou vystavovat uživatelské účty, které mohly být ohroženy, například uniklé přihlašovací údaje, které byly zjištěny nebo uživatel přihlášen z různých míst, což způsobuje nemožnou událost cestování.

![Uživatelé označení příznakem rizika](./media/steps-secure-identity/azure-ad-sec-steps3.png)

### <a name="audit-apps-and-consented-permissions"></a>Auditování aplikací a souhlasných oprávnění

Uživatelé mohou být podvedeni k navigaci na ohrožené webové stránky nebo aplikace, které získají přístup k informacím o svém profilu a uživatelským datům, jako je například jejich e-mail. Škodlivý objekt actor může použít oprávnění consented, která obdržel, k šifrování obsahu poštovní schránky a požadovat výkupné za opětovné získání dat poštovní schránky. [Správci by měli zkontrolovat a auditovat](https://docs.microsoft.com/office365/securitycompliance/detect-and-remediate-illicit-consent-grants) oprávnění udělená uživateli nebo zakázat možnost uživatelů udělit souhlas ve výchozím nastavení.

Kromě auditování oprávnění udělených uživateli můžete [najít rizikové nebo nežádoucí aplikace OAuth](https://docs.microsoft.com/cloud-app-security/investigate-risky-oauth) v prémiových prostředích.

## <a name="step-5---enable-end-user-self-service"></a>Krok 5 – Povolení samoobslužné služby koncového uživatele

Co nejvíce budete chtít vyvážit bezpečnost s produktivitou. Ve stejném duchu blížící se vaší cestě s myšlením, které dlouhodobě nastavujete základ pro zabezpečení, můžete odstranit tření z vaší organizace tím, že posílíte své uživatele a zároveň zůstanete ostražití.

### <a name="implement-self-service-password-reset"></a>Implementace samoobslužného resetování hesla

[Samoobslužné resetování hesla azure ad (SSPR)](../../active-directory/authentication/quickstart-sspr.md) nabízí jednoduchý způsob, jak pro správce IT, aby uživatelé mohli resetovat nebo odemknout svá hesla nebo účty bez zásahu helpdesk nebo správce. Systém obsahuje podrobné hlášení, které sleduje, kdy uživatelé resetují svá hesla, spolu s oznámeními, která vás upozorní na zneužití nebo zneužití.

### <a name="implement-self-service-group-and-application-access"></a>Implementace samoobslužné skupiny a přístupu k aplikacím

Azure AD poskytuje možnost nesprávcům spravovat přístup k prostředkům pomocí skupin zabezpečení, skupin Office 365, rolí aplikací a katalogů balíčků.  [Samoobslužná správa skupin](../../active-directory/users-groups-roles/groups-self-service-management.md) umožňuje vlastníkům skupin spravovat své vlastní skupiny, aniž by bylo nutné přiřazovat roli správce. Uživatelé mohou také vytvářet a spravovat skupiny Office 365, aniž by se spoléhali na správce, kteří budou zpracovávat jejich požadavky, a platnost nepoužívaných skupin vyprší automaticky.  [Správa oprávnění Azure AD](../../active-directory/governance/entitlement-management-overview.md) dále umožňuje delegování a viditelnost s komplexními pracovními postupy žádostí o přístup a automatickým vypršením platnosti.  Můžete delegovat na jiné než správce možnost konfigurovat vlastní přístupové balíčky pro skupiny, týmy, aplikace a weby SharePoint online, které vlastní, s vlastními zásadami pro to, kdo je nutný ke schválení přístupu, včetně konfigurace zaměstnanců manažerů a sponzorů obchodních partnerů jako schvalovatelů.

### <a name="implement-azure-ad-access-reviews"></a>Implementace kontrol přístupu azure ad

Pomocí [kontrol přístupu azure ad](../../active-directory/governance/access-reviews-overview.md), můžete spravovat přístup balíček a členství ve skupinách, přístup k podnikovým aplikacím a přiřazení privilegovaných rolí, abyste měli jistotu, že udržujete standard zabezpečení.  Pravidelný dohled samotných uživatelů, vlastníků prostředků a dalších recenzentů zajišťuje, že uživatelé nezachovávají přístup po delší dobu, kdy jej již nepotřebují.

## <a name="summary"></a>Souhrn

Existuje mnoho aspektů zabezpečené infrastruktury identit, ale tento pětistupňový kontrolní seznam vám pomůže rychle dosáhnout bezpečnější a zabezpečené infrastruktury identit:

* Posilte své pověření.
* Zmenšete oblast útoku.
* Automatizujte reakci na hrozby.
* Využijte cloudovou inteligenci.
* Umožněte předvídatelnější a úplnější zabezpečení koncových uživatelů pomocí svépomoci.

Oceňujeme, jak vážně berete identity security a doufáme, že tento dokument je užitečným plánem pro bezpečnější držení těla vaší organizace.

## <a name="next-steps"></a>Další kroky

Pokud potřebujete pomoc při plánování a nasazení doporučení, podívejte se na [plány nasazení projektu Azure AD](https://aka.ms/deploymentplans) pro nápovědu.

Pokud jste si jisti, že všechny tyto kroky jsou [dokončeny,](../../active-directory/fundamentals/identity-secure-score.md)použijte skóre zabezpečení identity společnosti Microsoft , které vás bude informovat o [nejnovějších doporučených postupech](identity-management-best-practices.md) a bezpečnostních hrozbách.
