---
title: Zabezpečení infrastruktury identit Azure AD
titleSuffix: Azure Active Directory
description: Tento dokument popisuje seznam důležitých akcí, které by správci měli implementovat, aby jim pomohli zabezpečit svoji organizaci pomocí možností Azure AD.
author: martincoetzer
manager: manmeetb
tags: azuread
ms.service: security
ms.subservice: security-fundamentals
ms.topic: conceptual
ms.workload: identity
ms.date: 01/29/2020
ms.author: martinco
ms.openlocfilehash: ffc5bafca305086b0c524c76eb91b8aec2e2655d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98602439"
---
# <a name="five-steps-to-securing-your-identity-infrastructure"></a>Pět kroků pro zabezpečení infrastruktury identity

Pokud čtete tento dokument, máte na paměti význam zabezpečení. Je možné, že už máte odpovědnost za zabezpečení vaší organizace. Pokud potřebujete přesvědčit jiné důležité informace o zabezpečení, pošlete jim, abyste si přečetli nejnovější [sestavu Microsoft Security Intelligence](https://www.microsoft.com/security/business/security-intelligence-report).

Tento dokument vám pomůže získat bezpečnější stav pomocí možností Azure Active Directory s využitím kontrolního seznamu pěti kroků k naočkování vaší organizace před internetovými útoky.

Tento kontrolní seznam vám pomůže rychle nasadit kritické doporučené akce pro zajištění ochrany vaší organizace hned pomocí vysvětlení, jak:

* Posílit své přihlašovací údaje.
* Snižte prostor pro útoky.
* Automatizujte reakci na hrozby.
* Využijte cloudové funkce Cloud Intelligence.
* Povolit samoobslužnou službu koncového uživatele.

Ujistěte se, že sledujete, které funkce a kroky jsou dokončeny při čtení tohoto kontrolního seznamu.

> [!NOTE]
> Mnohé z doporučení v tomto dokumentu platí jenom pro aplikace, které jsou nakonfigurované pro použití Azure Active Directory jako jejich zprostředkovatel identity. Konfigurace aplikací pro jednu Sign-On zajišťuje výhody zásad přihlašovacích údajů, detekce hrozeb, auditování, protokolování a dalších funkcí, které se do těchto aplikací přidávají. [Správa aplikací Azure AD](../../active-directory/manage-apps/what-is-application-management.md) je základem, na kterém jsou založená všechna tato doporučení.

Doporučení v tomto dokumentu jsou zarovnaná se [známkou pro identitu identity](../../active-directory/fundamentals/identity-secure-score.md), automatizované vyhodnocení konfigurace zabezpečení identity TENANTA Azure AD. Organizace můžou na portálu Azure AD pomocí stránky identita zabezpečeného skóre identity najít mezery v aktuální konfiguraci zabezpečení, aby se zajistilo, že budou dodržovat aktuální [osvědčené postupy](identity-management-best-practices.md) Microsoftu pro zabezpečení. Implementace každého doporučení na stránce bezpečné skóre zvýší vaše skóre a umožní vám sledovat váš pokrok a pomůže vám porovnat vaši implementaci s jinými podobnými organizacemi nebo v oboru.

![Skóre Secure identity](./media/steps-secure-identity/azure-ad-sec-steps0.png)

> [!NOTE]
> Mnohé z těchto funkcí popsaných tady vyžadují předplatné Azure AD Premium, zatímco některé jsou bezplatné. Další informace najdete v našich [Azure Active Directory cenách](https://azure.microsoft.com/pricing/details/active-directory/) a v [kontrolním seznamu nasazení služby Azure AD](../../active-directory/fundamentals/active-directory-deployment-checklist-p2.md) .

## <a name="before-you-begin-protect-privileged-accounts-with-mfa"></a>Než začnete: chránit privilegované účty pomocí MFA

Před zahájením tohoto kontrolního seznamu se ujistěte, že při čtení tohoto kontrolního seznamu nedošlo k ohrožení zabezpečení. Nejdřív musíte chránit privilegované účty.

Útočníci, kteří získají kontrolu nad privilegovanými účty, můžou dělat obrovský škodu, takže je důležité tyto účty nejdřív chránit. Povolte a vyžadovat [Azure ad Multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md) (MFA) pro všechny správce ve vaší organizaci pomocí [výchozích hodnot zabezpečení Azure AD](../../active-directory/fundamentals/concept-fundamentals-security-defaults.md) nebo [podmíněného přístupu](../../active-directory/conditional-access/plan-conditional-access.md). Pokud jste neimplementovali MFA, udělejte to teď! Je to důležité.

Vše nastavené? Pojďme začít v kontrolním seznamu.

## <a name="step-1---strengthen-your-credentials"></a>Krok 1 – posílení přihlašovacích údajů

Většina porušení podnikového zabezpečení pocházela z některého z několik metod, jako je například postřik hesla, porušení narušení nebo útok phishing. Přečtěte si další informace o těchto útocích v tomto videu (45 min):
> [!VIDEO https://www.youtube.com/embed/uy0j1_t5Hd4]

### <a name="make-sure-your-organization-uses-strong-authentication"></a>Ujistěte se, že vaše organizace používá silné ověřování.

Vzhledem k tomu, že se četnost vyladěného, podvodného, odcizeného malwaru nebo opětovného použití znovu používá, je důležité, abyste heslo zálohovali pomocí nějaké formy silných přihlašovacích údajů – další informace o [Multi-Factor Authentication Azure AD](../../active-directory/authentication/concept-mfa-howitworks.md).

Pokud chcete snadno povolit základní úroveň zabezpečení identity, můžete použít povolení jedním kliknutím s [výchozími nastaveními zabezpečení Azure AD](../../active-directory/fundamentals/concept-fundamentals-security-defaults.md). Výchozí nastavení zabezpečení vynutilo Azure AD MFA pro všechny uživatele v tenantovi a blokují přihlášení ze starších protokolů v celém tenantovi.

### <a name="start-banning-commonly-attacked-passwords-and-turn-off-traditional-complexity-and-expiration-rules"></a>Spustit zaměnit často hesla pro útoky a vypnout tradiční složitost a pravidla vypršení platnosti.

Mnoho organizací používá tradiční složitost (vyžaduje speciální znaky, číslice, Velká písmena a malá písmena) a pravidla vypršení platnosti hesla. Tyto [zásady ukázaly](https://aka.ms/passwordguidance) , že uživatelé si můžou vybrat hesla, která se budou snáze uhodnout.

Funkce [dynamického hesla](../../active-directory/authentication/concept-password-ban-bad.md) služby Azure AD používá aktuální chování útočníka k tomu, aby uživatelé nemohli nastavovat hesla, která se dají snadno odhadnout. Tato funkce je vždycky zapnutá při vytváření uživatelů v cloudu, ale je teď dostupná i pro hybridní organizace při nasazení [ochrany hesel Azure AD pro Windows Server Active Directory](../../active-directory/authentication/concept-password-ban-bad-on-premises.md). Ochrana heslem Azure AD zablokuje uživatelům možnost zvolit si tato běžná hesla a dá se rozšířit tak, aby zablokovala heslo, které obsahuje zadaná uživatelská klíčová slova Uživatelům můžete například zabránit v volbě hesla, která obsahují názvy produktů vaší společnosti nebo místní tým sport.

Microsoft doporučuje přijmout následující moderní zásady hesel na základě [pokynů pro NIST](https://pages.nist.gov/800-63-3/sp800-63b.html):

1. Vyžadovat heslo musí mít aspoň 8 znaků. Delší není nutně lepší, protože způsobují, že uživatelé budou vybírat předvídatelné hesla, ukládat hesla do souborů nebo je zapisovat.
2. Zakáže pravidla vypršení platnosti, která uživatelům umožňují snadno uhodnout hesla, jako je třeba **Spring2019!**
3. Zakažte požadavky na složení znaků a zabraňte uživatelům v výběru často napadených hesel, protože uživatelé můžou zvolit předvídatelné nahrazování znaků v heslech.

Pomocí [PowerShellu můžete zabránit vypršení platnosti hesel](../../active-directory/authentication/concept-sspr-policy.md) pro uživatele, pokud vytvoříte identity přímo ve službě Azure AD. Hybridní organizace by tyto zásady měly implementovat pomocí [nastavení zásad skupiny domény](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/hh994572(v%3dws.10)) nebo [Windows PowerShellu](/powershell/module/addsadministration/set-addefaultdomainpasswordpolicy).

### <a name="protect-against-leaked-credentials-and-add-resilience-against-outages"></a>Ochrana proti nevráceným přihlašovacím údajům a přidání odolnosti proti výpadkům

Pokud vaše organizace používá řešení hybridní identity s předávacím ověřováním nebo federaci, měli byste povolit synchronizaci hodnot hash hesel z následujících dvou důvodů:

* Zpráva [Uživatelé s nevrácenými přihlašovacími údaji](../../active-directory/identity-protection/overview-identity-protection.md) ve správě Azure AD vás upozorní na páry uživatelské jméno a heslo, které jsou vystavené na "tmavém webu". Nenáročné množství hesel se nevrací prostřednictvím útoků phishing, malwaru a hesla na webech třetích stran, které jsou později porušené. Společnost Microsoft nalezne mnoho z těchto nevrácených přihlašovacích údajů a upozorní vás v této sestavě, pokud se shodují s přihlašovacími údaji ve vaší organizaci – ale jenom v případě, že [povolíte synchronizaci hodnot hash hesel](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md) nebo máte jenom cloudové identity.
* V případě výpadku místního výpadku (například při útoku ransomwarem) můžete přepínat na použití [cloudového ověřování pomocí synchronizace hodnot hash hesel](../../active-directory/hybrid/choose-ad-authn.md). Tato metoda ověřování záloh vám umožní pokračovat v přístupu k aplikacím nakonfigurovaným pro ověřování pomocí Azure Active Directory, včetně Microsoft 365. V takovém případě se pracovníci IT nebudou muset oddělit k osobním e-mailovým účtům, aby mohli sdílet data, dokud nedojde k vyřešení místního výpadku.

Přečtěte si další informace o tom, jak funguje [synchronizace hodnot hash hesel](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md) .

> [!NOTE]
> Pokud povolíte synchronizaci hodnot hash hesel a používáte službu Azure AD Domain Services, hodnoty hash protokolu Kerberos (AES 256) a volitelně NTLM (RC4, No Salt) se taky zašifrují a synchronizují do Azure AD.

### <a name="implement-ad-fs-extranet-smart-lockout"></a>Implementovat inteligentní uzamčení AD FS extranetu

Organizace, které konfigurují aplikace pro ověřování přímo ve zvýhodnění Azure AD z [inteligentního uzamčení Azure AD](../../active-directory/authentication/concept-sspr-howitworks.md). Pokud používáte AD FS ve Windows serveru 2012R2, implementujte AD FS [extranetovou ochranu](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-soft-lockout-protection). Pokud používáte AD FS v systému Windows Server 2016, implementujte [extranetové inteligentní uzamčení](https://support.microsoft.com/help/4096478/extranet-smart-lockout-feature-in-windows-server-2016). AD FS inteligentní uzamčení extranetu chrání před útoky hrubou silou, které cílí na AD FS a zároveň brání uzamknutí uživatelů ve službě Active Directory.

### <a name="take-advantage-of-intrinsically-secure-easier-to-use-credentials"></a>Využijte výhod vnitřního zabezpečení a snazšího používání přihlašovacích údajů

Pomocí [Windows Hello](/windows/security/identity-protection/hello-for-business/hello-identity-verification)můžete nahradit hesla silným dvojúrovňovém ověřováním na počítačích a mobilních zařízeních. Toto ověřování se skládá z nového typu přihlašovacích údajů uživatele, které jsou bezpečně vázané na zařízení a používají biometriku nebo PIN kód.

## <a name="step-2---reduce-your-attack-surface"></a>Krok 2 – omezení prostoru pro útok

Vzhledem k ohrožení bezpečnosti pronikavostí hesla je minimalizace prostoru pro útoky ve vaší organizaci zásadní. Vyloučení použití starších, méně zabezpečených protokolů, omezování vstupních bodů přístupu a uplatnění důležitějšího řízení přístupu pro správu k prostředkům může přispět k omezení oblasti útoku.

### <a name="block-legacy-authentication"></a>Blokování starší verze ověřování

Aplikace, které používají své vlastní starší metody k ověřování pomocí Azure AD a přístup k datům společnosti, představují pro organizace jiné riziko. Příklady aplikací využívajících starší ověřování jsou klienti POP3, IMAP4 nebo SMTP. Aplikace pro starší verze ověřování se ověřují jménem uživatele a brání službě Azure AD v provádění pokročilých hodnocení zabezpečení. Alternativní moderní ověřování sníží vaše bezpečnostní riziko, protože podporuje službu Multi-Factor Authentication a podmíněný přístup. Doporučujeme následující tři akce:

1. Zablokovat [starší ověřování, pokud používáte AD FS](/windows-server/identity/ad-fs/operations/access-control-policies-w2k12).
2. Nastavte [SharePoint Online a Exchange Online pro použití moderního ověřování](../../active-directory/conditional-access/block-legacy-authentication.md).
3. Pokud máte Azure AD Premium, pomocí zásad podmíněného přístupu [zablokujte starší verze ověřování](../../active-directory/conditional-access/howto-conditional-access-policy-block-legacy.md), jinak použijte [výchozí nastavení zabezpečení Azure AD](../../active-directory/fundamentals/concept-fundamentals-security-defaults.md).

### <a name="block-invalid-authentication-entry-points"></a>Blokovat neplatné vstupní body ověřování

S využitím duševního vlastnictví byste měli snížit dopad napadených uživatelských přihlašovacích údajů, když k nim dojde. Pro každou aplikaci ve vašem prostředí uvažujte o platných případech použití: které skupiny, které sítě, které zařízení a jiné prvky mají autorizaci – potom zablokujte zbytek. Pomocí [podmíněného přístupu Azure AD](../../active-directory/conditional-access/overview.md)můžete řídit, jak autorizovaní uživatelé přistupují k aplikacím a prostředkům na základě konkrétních podmínek, které definujete.

### <a name="restrict-user-consent-operations"></a>Omezení operací souhlasu uživatele

Je důležité pochopit různá [prostředí pro vyjádření souhlasu s aplikací Azure AD](../../active-directory/develop/application-consent-experience.md), [typy oprávnění a souhlas](../../active-directory/develop/v2-permissions-and-consent.md)a jejich dopady na zabezpečení stav vaší organizace. Ve výchozím nastavení mohou všichni uživatelé v Azure AD udělit aplikacím, které využívají platformu Microsoft identity, přístup k datům vaší organizace. I když uživatelům, kteří si můžou udělit souhlas sami, umožní uživatelům snadno získat užitečné aplikace, které se integrují s Microsoft 365, Azure a dalšími službami, může představovat riziko, pokud se nepoužije a pečlivě monitoruje.

Microsoft doporučuje omezení souhlasu uživatele, aby se snížila vaše plocha a zmírnilo toto riziko. Můžete také použít [zásady pro vyjádření souhlasu s aplikací (Preview)](../../active-directory/manage-apps/configure-user-consent.md) k omezení souhlasu koncového uživatele jenom s ověřenými vydavateli a jenom pro oprávnění, která jste vybrali. Pokud je omezené vyjádření souhlasu koncovým uživatelem, budou se i nadále akceptovat předchozí granty souhlasu, ale všechny budoucí operace souhlasu musí provádět správce. V případě omezených případů může uživatel požádat o souhlas správce prostřednictvím integrovaného [pracovního postupu pro žádost o souhlas správce](../../active-directory/manage-apps/configure-admin-consent-workflow.md) nebo prostřednictvím vlastních procesů podpory. Než omezíte souhlas koncového uživatele, použijte naše [doporučení](../../active-directory/manage-apps/manage-consent-requests.md) a naplánujte tuto změnu ve vaší organizaci. Pro aplikace, kterým chcete umožnit přístup všem uživatelům, zvažte [udělení souhlasu jménem všech uživatelů](../../active-directory/develop/v2-admin-consent.md), aby uživatelé, kteří dosud nesouhlasili samostatně, měli přístup k aplikaci. Pokud nechcete, aby byly tyto aplikace dostupné pro všechny uživatele ve všech scénářích, použijte [přiřazení aplikace](../../active-directory/manage-apps/assign-user-or-group-access-portal.md) a podmíněný přístup k omezení přístupu uživatelů ke [konkrétním aplikacím](../../active-directory/conditional-access/concept-conditional-access-cloud-apps.md).

Ujistěte se, že uživatelé můžou požádat o schválení správcem pro nové aplikace, aby se snížila jeho tření, minimalizoval objem podpory a aby si mohli uživatelé zaregistrovat aplikace pomocí přihlašovacích údajů jiných než Azure AD. Po regulování svých operací by správci měli pravidelně auditovat aplikace a souhlasná oprávnění.


### <a name="implement-azure-ad-privileged-identity-management"></a>Implementovat Azure AD Privileged Identity Management

Dalším dopadem "předpokládat porušení" je nutnost minimalizovat pravděpodobnost, že ohrožený účet může pracovat s privilegovaný rolí.

[Azure AD Privileged Identity Management (PIM)](../../active-directory/privileged-identity-management/pim-configure.md) pomáhá minimalizovat oprávnění účtu tím, že vám pomůže:

* Identifikujte a spravujte uživatele přiřazené k rolím pro správu.
* Pochopení nevyužitých nebo nadměrných rolí oprávnění, které byste měli odebrat.
* Vytvořte pravidla, abyste měli jistotu, že jsou privilegované role chráněné službou Multi-Factor Authentication.
* Vytvořte pravidla, která zajistí, že se privilegované role udělí jenom dostatečně dlouho, aby bylo možné provést privilegovaný úkol.

Povolte Azure AD PIM a pak si prohlédněte uživatele, kteří mají přiřazené administrativní role, a odeberte v těchto rolích zbytečné účty. U zbývajících privilegovaných uživatelů je přesuňte z trvalého na nárok. Nakonec vytvořte vhodné zásady, abyste se ujistili, že potřebují získat přístup k těmto privilegovaným rolím, a to tak, aby je bylo možné bezpečně provést s nezbytným ovládacím prvkem pro změnu.

V rámci nasazení vašeho privilegovaného účtu postupujte podle [osvědčeného postupu pro vytvoření alespoň dvou mimořádných účtů](../../active-directory/roles/security-planning.md) , abyste měli jistotu, že budete mít stále přístup k Azure AD, pokud si to sami zamknete.

## <a name="step-3---automate-threat-response"></a>Krok 3 – automatizace reakce na hrozby

Azure Active Directory má mnoho možností, které automaticky zachycují útoky, a tím umožňuje odstranit latenci mezi zjišťováním a odezvou. Snížení nákladů a rizik můžete snížit, když omezíte dobu, kterou podvodníki použijí pro vložení do vašeho prostředí. Tady jsou konkrétní kroky, které můžete provést.

### <a name="implement-user-risk-security-policy-using-azure-ad-identity-protection"></a>Implementace zásad zabezpečení rizik uživatelů pomocí Azure AD Identity Protection

Riziko uživatele indikuje pravděpodobnost ohrožení identity uživatele a počítá se na základě [zjištění rizik uživatele](../../active-directory/identity-protection/overview-identity-protection.md) , která jsou přidružena k identitě uživatele. Zásady rizik uživatelů jsou zásady podmíněného přístupu, které vyhodnotí úroveň rizika pro konkrétního uživatele nebo skupinu. Na základě nízké, střední a vysoké úrovně rizika může být zásada nakonfigurovaná tak, aby blokovala přístup, nebo vyžadovala zabezpečenou změnu hesla pomocí Multi-Factor Authentication. Doporučení Microsoftu je vyžadovat pro uživatele s vysokým rizikem zabezpečenou změnu hesla.

![Snímek obrazovky s vybraným uživatelem zobrazuje uživatele označené příznakem rizika.](./media/steps-secure-identity/azure-ad-sec-steps1.png)

### <a name="implement-sign-in-risk-policy-using-azure-ad-identity-protection"></a>Implementace zásad rizik přihlašování pomocí Azure AD Identity Protection

Riziko přihlášení je pravděpodobnost, že se přihlašujete pomocí této identity, jinak někdo jiný než vlastník účtu. [Zásada pro rizikové přihlašování](../../active-directory/identity-protection/overview-identity-protection.md) je zásada podmíněného přístupu, která vyhodnocuje úroveň rizika pro konkrétního uživatele nebo skupinu. V závislosti na úrovni rizika (vysoká/střední/nízká) může být zásada nakonfigurovaná tak, aby blokovala přístup nebo aby se vynutilo ověřování službou Multi-Factor Authentication. Ujistěte se, že vynucujete vícefaktorové ověřování pro střední nebo vyšší rizikové přihlášení.

![Přihlášení z anonymních IP adres](./media/steps-secure-identity/azure-ad-sec-steps2.png)

## <a name="step-4---utilize-cloud-intelligence"></a>Krok 4 – využití Cloud Intelligence

Auditování a protokolování událostí souvisejících se zabezpečením a souvisejících výstrah jsou základními součástmi efektivní strategie ochrany. Protokoly zabezpečení a sestavy poskytují elektronický záznam o podezřelých aktivitách a umožňují detekovat vzory, které mohou označovat pokus nebo úspěšné vnější průnik sítě a interní útoky. Pomocí auditování můžete sledovat činnost uživatelů, dokumentovat dodržování předpisů, provádět analýzu forenzní a provádět další akce. Výstrahy obsahují oznámení o událostech zabezpečení.

### <a name="monitor-azure-ad"></a>Monitorování Azure AD

Služba Microsoft Azure Services a funkce poskytují konfigurovatelné možnosti auditování zabezpečení a protokolování, které vám pomůžou identifikovat mezery v zásadách a mechanismech zabezpečení a vyřešit tyto nedostatky, aby se předešlo nedodržení. Můžete použít [protokolování a auditování Azure](log-audit.md) a použít [sestavy aktivit auditu na portálu Azure Active Directory](../../active-directory/reports-monitoring/concept-audit-logs.md).

### <a name="monitor-azure-ad-connect-health-in-hybrid-environments"></a>Monitorování Azure AD Connect Health v hybridních prostředích

[Sledování AD FS pomocí Azure AD Connect Health](../../active-directory/hybrid/how-to-connect-health-adfs.md) poskytuje lepší přehled o potenciálních problémech a o útocích na útoky na AD FS infrastrukturu. Azure AD Connect Health poskytuje výstrahy s podrobnostmi, kroky řešení a odkazy na související dokumentaci; Analýza využití pro různé metriky související s ověřováním provozu; sledování výkonu a sestavy.

![Azure AD Connect Health](./media/steps-secure-identity/azure-ad-sec-steps4.png)

### <a name="monitor-azure-ad-identity-protection-events"></a>Monitorovat události Azure AD Identity Protection

[Azure AD Identity Protection](../../active-directory/identity-protection/overview-identity-protection.md) je nástroj pro oznamování, monitorování a vytváření sestav, který můžete použít k detekci potenciálních ohrožení zabezpečení, které mají vliv na identity vaší organizace. Detekuje detekci rizik, jako jsou nevrácené přihlašovací údaje, nemožné cestování a přihlášení z infikovaných zařízení, anonymní IP adresy, IP adresy přidružené k podezřelé aktivitě a neznámá umístění. Povolit upozornění na oznámení pro příjem e-mailů uživatelů v ohrožení a/nebo týdenního e-mailu.

Azure AD Identity Protection poskytuje dvě důležité sestavy, které byste měli monitorovat denně:
1. V sestavách rizikové přihlašování se budou provádět aktivity přihlašování uživatelů, které byste měli prozkoumat, ale oprávněný vlastník neudělal přihlášení.
2. Rizikové sestavy uživatelů budou Surface uživatelských účtů, které mohly být ohroženy, například zjištěná zjištěná pověření nebo uživatel přihlášený z různých míst, což způsobuje nemožnou cestu k akci.

![Snímek obrazovky ukazuje podokno Azure A D Identity Protection s uživateli a jejich úrovněmi rizika.](./media/steps-secure-identity/azure-ad-sec-steps3.png)

### <a name="audit-apps-and-consented-permissions"></a>Auditovat aplikace a souhlasná oprávnění

Uživatelé mohou být vyzrazeni na napadený web nebo aplikace, které získají přístup k informacím o profilu a uživatelským datům, jako je například jejich e-mailová adresa. Škodlivý objekt actor může použít odsouhlasená oprávnění, která obdržela k šifrování obsahu poštovní schránky a vyžádání Ransom k opětovnému získání dat poštovní schránky. [Správci by měli kontrolovat a auditovat](/office365/securitycompliance/detect-and-remediate-illicit-consent-grants) oprávnění udělená uživateli nebo zakázat uživatelům udělit souhlas ve výchozím nastavení.

Kromě auditování oprávnění udělených uživateli můžete v prostředích Premium [Najít rizikové nebo nežádoucí aplikace OAuth](/cloud-app-security/investigate-risky-oauth) .

## <a name="step-5---enable-end-user-self-service"></a>Krok 5 – povolení samoobslužné služby pro koncové uživatele

Jak je to možné, budete chtít zabezpečení vyrovnávat s produktivitou. Na stejném řádku, který se blíží vaší cestě, k místo, že nastavujete základ pro zabezpečení v dlouhodobém běhu, můžete z vaší organizace odebrat tření, a to díky tomu, že budete mít k disostražitíu své uživatele.

### <a name="implement-self-service-password-reset"></a>Implementace samoobslužného resetování hesla

[Samoobslužné resetování hesla Azure AD (SSPR)](../../active-directory/authentication/tutorial-enable-sspr.md) nabízí správcům IT jednoduchý způsob, jak uživatelům dovolit resetování nebo odemknutí svých hesel nebo účtů bez zásahu z helpdesku nebo správce. Systém zahrnuje podrobné vytváření sestav, které sleduje, kdy uživatelé obnovili hesla, spolu s oznámeními, která upozorňují na zneužití nebo zneužití.

### <a name="implement-self-service-group-and-application-access"></a>Implementace samoobslužné skupiny a přístupu k aplikacím

Azure AD poskytuje správcům možnost spravovat přístup k prostředkům pomocí skupin zabezpečení, skupin Microsoft 365, aplikačních rolí a přístupových katalogů balíčků.  [Samoobslužná správa skupin](../../active-directory/enterprise-users/groups-self-service-management.md) umožňuje vlastníkům skupin spravovat vlastní skupiny, aniž by museli být přiřazeni k roli správce. Uživatelé taky můžou vytvářet a spravovat Microsoft 365 skupiny, aniž by se museli spoléhat na to, že správci budou zpracovávat své požadavky, a automaticky vyprší platnost nevyužívaných skupin.  [Správa nároků Azure AD](../../active-directory/governance/entitlement-management-overview.md) dále umožňuje delegování a viditelnost s komplexními pracovními postupy žádostí o přístup a automatickým vypršením platnosti.  Můžete delegovat na nesprávce, aby mohli nakonfigurovat vlastní balíčky pro přístup pro skupiny, týmy, aplikace a weby SharePointu Online, které vlastní, s vlastními zásadami, které jsou nutné ke schválení přístupu, včetně konfigurace manažerů zaměstnanců a sponzorů obchodních partnerů jako schvalovatelů.

### <a name="implement-azure-ad-access-reviews"></a>Implementace kontrol přístupu Azure AD

Pomocí kontrol [přístupu ke službě Azure AD](../../active-directory/governance/access-reviews-overview.md)můžete spravovat přístup k balíčku a členství ve skupinách, přístup k podnikovým aplikacím a přiřazení privilegovaných rolí, abyste měli jistotu, že udržujete standard zabezpečení.  Vlastníci prohledí samy sebe, vlastníci prostředků a další kontroloři zajišťují, že uživatelé neuchovávají přístup po delší dobu, kdy je už nepotřebují.

## <a name="summary"></a>Souhrn

Existuje mnoho aspektů zabezpečení infrastruktury identity, ale tento kontrolní seznam s pěti kroky vám pomůže rychle provést bezpečnější a zabezpečenou infrastrukturu identity:

* Posílit své přihlašovací údaje.
* Snižte prostor pro útoky.
* Automatizujte reakci na hrozby.
* Využijte cloudové funkce Cloud Intelligence.
* Umožněte větší předvídatelné a úplné zabezpečení koncových uživatelů díky samoobslužné nápovědě.

Vážíme si, jak vážně proberete zabezpečení identity a doufáme, že tento dokument je užitečným plánem pro bezpečnější stav vaší organizace.

## <a name="next-steps"></a>Další kroky

Pokud potřebujete pomoc s plánováním a nasazením doporučení, požádejte o pomoc [plány nasazení projektu Azure AD](../../active-directory/fundamentals/active-directory-deployment-plans.md) .

Pokud jste si jisti, že jsou všechny tyto kroky splněné, použijte [hodnocení identity](../../active-directory/fundamentals/identity-secure-score.md)od Microsoftu, které vám bude zachovávat [nejnovější osvědčené postupy](identity-management-best-practices.md) a bezpečnostní hrozby.
