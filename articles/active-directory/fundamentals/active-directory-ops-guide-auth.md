---
title: Referenční příručka pro operace správy ověřování Azure a služby Active Directory
description: Tato referenční příručka pro operace popisuje kontroly a akce, které byste měli provést k zabezpečení správy ověřování.
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 10/31/2019
ms.author: martinco
ms.openlocfilehash: 8b4ec003888d75a582d25feef8ed2ce010fa7996
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80546239"
---
# <a name="azure-active-directory-authentication-management-operations-reference-guide"></a>Referenční příručka pro operace správy ověřování Azure a služby Active Directory

Tato část [referenční příručky pro operace Azure AD](active-directory-ops-guide-intro.md) popisuje kontroly a akce, které byste měli provést k zabezpečení a správě přihlašovacích údajů, definování prostředí ověřování, delegování přiřazení, měření využití a definování zásad přístupu na základě stavu zabezpečení rozlehlé sítě.

> [!NOTE]
> Tato doporučení jsou aktuální k datu publikování, ale mohou se v průběhu času měnit. Organizace by měly průběžně vyhodnocovat své postupy identity, jak se produkty a služby společnosti Microsoft v průběhu času vyvíjejí.

## <a name="key-operational-processes"></a>Klíčové provozní procesy

### <a name="assign-owners-to-key-tasks"></a>Přiřazení vlastníků ke klíčovým úkolům

Správa služby Azure Active Directory vyžaduje průběžné provádění klíčových provozních úkolů a procesů, které nemusí být součástí projektu zavádění. Je stále důležité nastavit tyto úkoly pro optimalizaci prostředí. Mezi klíčové úkoly a jejich doporučené vlastníky patří:

| Úkol | Vlastník |
| :- | :- |
| Správa životního cyklu konfigurace jednotného přihlašování (SSO) ve službě Azure AD | Operační tým IAM |
| Návrh zásad podmíněného přístupu pro aplikace Azure AD | Tým architektury Infosec |
| Archivovat přihlašovací aktivitu v systému SIEM | Operační tým InfoSec |
| Archivovat rizikové události v systému SIEM | Operační tým InfoSec |
| Třídění a vyšetřování zpráv o zabezpečení | Operační tým InfoSec |
| Třídění a zkoumání rizikových událostí | Operační tým InfoSec |
| Třídění a vyšetřování uživatelů označených příznakem zpráv o rizicích a zranitelnosti ze služby Azure AD Identity Protection | Operační tým InfoSec |

> [!NOTE]
> Azure AD Identity Protection vyžaduje licenci Azure AD Premium P2. Pokud chcete najít správnou licenci pro vaše požadavky, [přečtěte si téma Porovnání obecně dostupných funkcí edic Azure AD Free a Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/).

Při kontrole seznamu můžete zjistit, že budete muset buď přiřadit vlastníka pro úkoly, které chybí vlastník, nebo upravit vlastnictví úkolů s vlastníky, kteří nejsou v souladu s výše uvedenými doporučeními.

#### <a name="owner-recommended-reading"></a>Majitel doporučil čtení

- [Přiřazení rolí správce v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)
- [Zásady správného řízení v Azure](https://docs.microsoft.com/azure/security/governance-in-azure)

## <a name="credentials-management"></a>Správa přihlašovacích údajů

### <a name="password-policies"></a>Zásady hesel

Bezpečná správa hesel je jednou z nejkritičtějších částí správy identity a přístupu a často největším cílem útoků. Azure AD podporuje několik funkcí, které mohou zabránit úspěšnému útoku.

V následující tabulce naleznete doporučené řešení pro zmírnění problému, který je třeba řešit:

| Problém | Doporučení |
| :- | :- |
| Žádný mechanismus pro ochranu před slabými hesly | Povolení [samoobslužného resetování hesla Azure AD (SSPR)](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks) a [ochrany heslem](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad-on-premises) |
| Žádný mechanismus pro detekci uniklých hesel | Povolení [synchronizace hash hesel](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-password-hash-synchronization) (PHS) pro získání přehledů |
| Použití služby AD FS a možnost přejít na spravované ověřování | Povolení [inteligentního uzamčení a/nebo](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection) [inteligentního uzamčení služby AD](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-smart-lockout) FS Extranet |
| Zásady hesel používají pravidla založená na složitosti, jako je délka, více znakových sad nebo vypršení platnosti | Přehodnotit ve prospěch [Microsoft Doporučené postupy](https://aka.ms/passwordguidance) a přepnout svůj přístup ke správě hesel a nasadit [ochranu heslem Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad). |
| Uživatelé nejsou registrováni k použití vícefaktorového ověřování (MFA) | [Zaregistrujte všechny informace o zabezpečení uživatele,](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-mfa-policy) aby je bylo možné použít jako mechanismus k ověření identity uživatele spolu s jeho heslem |
| Nedochází k odvolání hesel na základě rizika uživatele | Nasazení zásad [rizika uživatele azure](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-user-risk-policy) ad identity protection vynutit změny hesla na nevracení přihlašovacích údajů pomocí sspr |
| Neexistuje žádný inteligentní mechanismus uzamčení, který by chránil škodlivé ověřování před špatnými aktéry pocházejícími z identifikovaných adres IP | Nasazení cloudové houževnatého ověřování pomocí synchronizace nastavení hash hesla nebo [předávacího ověřování](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta-quick-start) (PTA) |

#### <a name="password-policies-recommended-reading"></a>Zásady pro hesla doporučené čtení

- [Osvědčené postupy Azure AD a AD FS: Obrana proti útokům spreje heslem – podniková mobilita + zabezpečení](https://cloudblogs.microsoft.com/enterprisemobility/2018/03/05/azure-ad-and-adfs-best-practices-defending-against-password-spray-attacks/)

### <a name="enable-self-service-password-reset-and-password-protection"></a>Povolení samoobslužného resetování hesla a ochrany heslem

Uživatelé, kteří potřebují změnit nebo resetovat svá hesla, jsou jedním z největších zdrojů objemu a nákladů na volání technické podpory. Kromě nákladů je změna hesla jako nástroje ke zmírnění rizika uživatele zásadním krokem ke zlepšení stavu zabezpečení vaší organizace.

Minimálně se doporučuje nasadit [samoobslužné resetování hesla](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks) Azure AD (SSPR) a místní [ochranu heslem](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-ban-bad-on-premises-deploy) k dosažení:

- Odklonit volání help desku.
- Nahraďte použití dočasných hesel.
- Nahraďte jakékoli existující samoobslužné řešení správy hesel, které závisí na místním řešení.
- [Eliminujte slabá hesla](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad) ve vaší organizaci.

> [!NOTE]
> Pro organizace s předplatným Azure AD Premium P2 se doporučuje nasadit sspr a použít jej jako součást [zásad rizika uživatele ochrany identity](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-user-risk-policy).

### <a name="strong-credential-management"></a>Silná správa pověření

Hesla sama o sobě nejsou dostatečně bezpečná, aby zabránila špatným aktérům v získání přístupu do vašeho prostředí. Minimálně každý uživatel s privilegovaným účtem musí být povolenpro vícefaktorové ověřování (MFA). V ideálním případě byste měli povolit [kombinovanou registraci](https://docs.microsoft.com/azure/active-directory/authentication/concept-registration-mfa-sspr-combined) a požadovat, aby se všichni uživatelé zaregistrovali pro vícefaktorové a snop pomocí [kombinovaného registračního prostředí](https://docs.microsoft.com/azure/active-directory/user-help/user-help-security-info-overview). Nakonec doporučujeme přijmout strategii pro [zajištění odolnosti](https://docs.microsoft.com/azure/active-directory/authentication/concept-resilient-controls) ke snížení rizika uzamčení z důvodu nepředvídaných okolností.

![Kombinovaný tok uživatelského prostředí](./media/active-directory-ops-guide/active-directory-ops-img4.png)

### <a name="on-premises-outage-authentication-resiliency"></a>Odolnost proti ověřování místního výpadku

Kromě výhod jednoduchosti a povolení zjišťování nepřístupných přihlašovacích údajů umožňují azure ad password hash sync (PHS) a Azure MFA uživatelům přístup k aplikacím SaaS a Office 365 navzdory místním výpadkům v důsledku kybernetických útoků, jako je [NotPetya](https://www.microsoft.com/security/blog/2018/02/05/overview-of-petya-a-rapid-cyberattack/). Je také možné povolit PHS ve spojení s federací. Povolení phs umožňuje záložní ověřování, když nejsou k dispozici federační služby.

Pokud vaše místní organizace chybí strategie odolnosti proti výpadku nebo má jeden, který není integrovaný s Azure AD, měli byste nasadit Azure AD PHS a definovat plán zotavení po havárii, který zahrnuje PHS. Povolení Azure AD PHS umožní uživatelům ověření proti Azure AD pokud vaše místní služba Active Directory nebude k dispozici.

![tok synchronizace hash hesel](./media/active-directory-ops-guide/active-directory-ops-img5.png)

Informace o možnostech ověřování najdete v [tématu Výběr správné metody ověřování pro řešení hybridní identity služby Azure Active Directory](https://docs.microsoft.com/azure/active-directory/hybrid/choose-ad-authn).

### <a name="programmatic-usage-of-credentials"></a>Programové použití pověření

Skripty Azure AD používající PowerShell nebo aplikace používající rozhraní Microsoft Graph API vyžadují zabezpečené ověřování. Špatná správa pověření provádějící tyto skripty a nástroje zvyšuje riziko krádeže pověření. Pokud používáte skripty nebo aplikace, které jsou závislé na pevně zakódovaných heslech nebo příkazových řádcích, měli byste nejprve zkontrolovat hesla v konfiguračních souborech nebo zdrojovém kódu, pak tyto závislosti nahradit a kdykoli je to možné použít spravované identity Azure, integrované ověřování systému Windows nebo [certifikáty.](https://docs.microsoft.com/azure/active-directory/reports-monitoring/tutorial-access-api-with-certificates) Pro aplikace, kde předchozí řešení nejsou možné, zvažte použití [Azure Key Vault](https://azure.microsoft.com/services/key-vault/).

Pokud zjistíte, že existují instanční objekty s pověřeníhesla a nejste si jisti, jak jsou tyto přihlašovací údaje hesla zabezpečeny skripty nebo aplikacemi, obraťte se na vlastníka aplikace, abyste lépe porozuměli vzorcům použití.

Společnost Microsoft také doporučuje kontaktovat vlastníky aplikací, abyste pochopili vzorce použití, pokud existují instanční objekty s pověřeními hesla.

## <a name="authentication-experience"></a>Možnosti ověřování

### <a name="on-premises-authentication"></a>Místní ověřování

Federované ověřování s integrovaným ověřováním systému Windows (IWA) nebo bezproblémovým ověřováním jednotného přihlašování (SSO) se spravovaným ověřováním pomocí synchronizace hash nebo předávacího ověřování hesla je nejlepší uživatelské prostředí v podnikové síti s místními řadiči domény. Minimalizuje únavu z výzvy k zadání pověření a snižuje riziko, že se uživatelé stanou kořistí phishingových útoků. Pokud již používáte cloudové ověřování s PHS nebo PTA, ale uživatelé stále potřebují zadat své heslo při ověřování v místním prostředí, měli byste okamžitě [nasadit bezproblémové jednotné přihlašování](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso). Na druhou stranu pokud jste aktuálně federovány s plány nakonec migrovat na ověřování spravované cloudem, pak byste měli implementovat bezproblémové jednotné přihlašování jako součást projektu migrace.

### <a name="device-trust-access-policies"></a>Zásady přístupu k důvěryhodnosti zařízení

Stejně jako uživatel ve vaší organizaci je zařízení základní identitou, kterou chcete chránit. Identitu zařízení můžete použít k ochraně prostředků kdykoli a z libovolného místa.Ověřování zařízení a účtování jeho typu důvěryhodnosti zlepšuje stav zabezpečení a použitelnost tím, že:

- Vyhnout se tření, například s Vícefaktorové zabezpečení, když je zařízení důvěryhodné
- Blokování přístupu z nedůvěryhodných zařízení
- Pro zařízení s Windows 10 poskytněte [jednotné přihlašování k místním prostředkům bez problémů](https://docs.microsoft.com/azure/active-directory/devices/azuread-join-sso).

Tento cíl můžete provést tím, že identity zařízení a jejich správu ve službě Azure AD pomocí jedné z následujících metod:

- Organizace můžou pomocí [Microsoft Intune](https://docs.microsoft.com/intune/what-is-intune) spravovat zařízení a vynucovat zásady dodržování předpisů, doručovat stav zařízení a nastavovat zásady podmíněného přístupu na základě toho, jestli je zařízení kompatibilní. Microsoft Intune může spravovat iOS zařízení, stolní počítače Mac (integrace Přes JAMF), stolní počítače s Windows (nativně pomocí správy mobilních zařízení pro Windows 10 a spolusprávu s Microsoft Endpoint Configuration Manager) a mobilní zařízení s Androidem.
- [Hybridní připojení k Azure AD](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-managed-domains) poskytuje správu pomocí zásad skupiny nebo nástroje Microsoft Endpoint Configuration Manager v prostředí s počítači spojenými s doménou služby Active Directory. Organizace můžete nasadit spravované prostředí buď prostřednictvím PHS nebo PTA s bezproblémové jednotné přihlašování. Uvedení vašich zařízení do Azure AD maximalizuje produktivitu uživatelů prostřednictvím přistupujícího objektu zabezpečení prostřednictvím cloudových a místních prostředků a zároveň vám umožní zabezpečit přístup k cloudovým a místním prostředkům pomocí [podmíněného přístupu](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal) současně.

Pokud máte zařízení s Windows připojená k doméně, která nejsou registrovaná v cloudu, nebo zařízení s Windows připojená k doméně, která jsou registrovaná v cloudu, ale bez zásad podmíněného přístupu, měli byste zaregistrovat neregistrovaná zařízení a v obou případech [použít hybridní připojení Azure AD jako ovládací prvek](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices) v zásadách podmíněného přístupu.

![Snímek obrazovky s udělením v zásadách podmíněného přístupu vyžadujících hybridní zařízení](./media/active-directory-ops-guide/active-directory-ops-img6.png)

Pokud spravujete zařízení s MDM nebo Microsoft Intune, ale nepoužíváte ovládací prvky zařízení v zásadách podmíněného přístupu, doporučujeme použít [vyžadovat, aby zařízení bylo v](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices#require-device-to-be-marked-as-compliant) těchto zásadách označeno jako vyhovující jako ovládací prvek.

![Snímek obrazovky s udělením v zásadách podmíněného přístupu, které vyžadují dodržování předpisů zařízení](./media/active-directory-ops-guide/active-directory-ops-img7.png)

#### <a name="device-trust-access-policies-recommended-reading"></a>Zásady přístupu k důvěryhodnosti zařízení doporučené pro čtení

- [Postup: Plánování hybridní implementace připojení k hybridníslužbě Azure Active Directory](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-plan)
- [Konfigurace identit a přístupu k zařízením](https://docs.microsoft.com/microsoft-365/enterprise/microsoft-365-policies-configurations)

### <a name="windows-hello-for-business"></a>Windows Hello pro firmy

Ve Windows 10 [windows Hello pro firmy](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification) nahrazuje hesla silným dvoufaktorovým ověřováním na počítačích. Windows Hello pro firmy umožňuje efektivnější prostředí MFA pro uživatele a snižuje vaši závislost na heslech. Pokud jste zařízení s Windows 10 ještě nezačali zavádět nebo jste je nasadili jen částečně, doporučujeme upgradovat na Windows 10 a [povolit Windows Hello pro firmy](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-manage-in-organization) na všech zařízeních.

Pokud se chcete dozvědět více o ověřování bez hesla, přečtěte [si informace o světě bez hesel pomocí služby Azure Active Directory](https://aka.ms/passwordlessdoc).

## <a name="application-authentication-and-assignment"></a>Ověřování a přiřazováno aplikací

### <a name="single-sign-on-for-apps"></a>Jednotné přihlašování pro aplikace

Poskytování standardizovaného jednotného přihlašovacího mechanismu pro celý podnik je zásadní pro nejlepší uživatelské prostředí, snížení rizika, schopnost podávat zprávy a zásadsprávné řízení. Pokud používáte aplikace, které podporují přihlašování pomocí služby Azure AD, ale jsou aktuálně nakonfigurované pro použití místních účtů, měli byste tyto aplikace překonfigurovat tak, aby používaly služby SSO s Azure AD. Podobně pokud používáte všechny aplikace, které podporují přihlašování pomocí služby Azure AD, ale používají jiného zprostředkovatele identity, měli byste tyto aplikace překonfigurovat tak, aby používaly také služby SSO s Azure AD. Pro aplikace, které nepodporují federační protokoly, ale podporují ověřování založené na formulářích, doporučujeme nakonfigurovat aplikaci tak, aby [používala trezor hesel](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-password-vaulting) s proxy aplikacemi Azure AD.

![Přihlášení založené na hesle pomocí protokolu AppProxy](./media/active-directory-ops-guide/active-directory-ops-img8.png)

> [!NOTE]
> Pokud nemáte mechanismus pro zjišťování nespravovaných aplikací ve vaší organizaci, doporučujeme implementovat proces zjišťování pomocí řešení cloudového zabezpečení přístupu (CASB), jako je [Microsoft Cloud App Security](https://www.microsoft.com/enterprise-mobility-security/cloud-app-security).

Nakonec pokud máte galerii aplikací Azure AD a používáte aplikace, které podporují přihlašování pomocí azure ad, doporučujeme [uvést aplikaci v galerii aplikací](https://docs.microsoft.com/azure/active-directory/develop/howto-app-gallery-listing).

#### <a name="single-sign-on-recommended-reading"></a>Doporučená čtení pro jednotné přihlášení

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Služby Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

### <a name="migration-of-ad-fs-applications-to-azure-ad"></a>Migrace aplikací služby AD FS do služby Azure AD

[Migrace aplikací ze služby AD FS do služby Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/migrate-adfs-apps-to-azure) umožňuje další funkce zabezpečení, konzistentnější možnosti správy a lepší možnosti spolupráce. Pokud máte aplikace nakonfigurované ve službě AD FS, které podporují služby SSO s Azure AD, měli byste tyto aplikace překonfigurovat tak, aby používaly služby SSO s Azure AD. Pokud máte aplikace nakonfigurované ve službě AD FS s neobvyklými konfiguracemi, které Azure AD nepodporuje, měli byste kontaktovat vlastníky aplikací, abyste zjistili, zda je speciální konfigurace absolutním požadavkem aplikace. Pokud není vyžadováno, pak byste měli překonfigurovat aplikaci tak, aby používala přihlašované pomocí služby AD.

![Azure AD jako primární zprostředkovatel identity](./media/active-directory-ops-guide/active-directory-ops-img9.png)

> [!NOTE]
> [Azure AD Connect Health for ADFS](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-adfs) se dá použít ke shromažďování podrobností o konfiguraci o každé aplikaci, kterou lze potenciálně migrovat do Služby Azure AD.

### <a name="assign-users-to-applications"></a>Přiřazení uživatelů k aplikacím

[Přiřazení uživatelů k aplikacím](https://docs.microsoft.com/azure/active-directory/manage-apps/methods-for-assigning-users-and-groups) je nejlépe mapováno pomocí skupin, protože umožňují větší flexibilitu a schopnost spravovat ve velkém měřítku. Mezi výhody použití skupin patří [dynamické členství ve skupinách založené na atributech](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership) a [delegování na vlastníky aplikací](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-accessmanagement-managing-group-owners). Pokud tedy již skupiny používáte a spravujete, doporučujeme provést následující akce ke zlepšení správy ve velkém měřítku:

- Delegujte správu a zásadsprávné řízení skupiny na vlastníky aplikací.
- Povolit samoobslužný přístup k aplikaci.
- Definujte dynamické skupiny, pokud atributy uživatele mohou konzistentně určovat přístup k aplikacím.
- Implementujte atestaci do skupin používaných pro přístup k aplikacím pomocí [kontrol přístupu Azure AD](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview).

Na druhou stranu pokud zjistíte, že aplikace, které mají přiřazení k jednotlivým uživatelům, nezapomeňte implementovat [zásady správného řízení](https://docs.microsoft.com/azure/active-directory/governance/index) kolem těchto aplikací.

#### <a name="assign-users-to-applications-recommended-reading"></a>Přiřazení uživatelů k aplikacím doporučeným čtením

- [Přiřazení uživatelů a skupin k aplikaci ve službě Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/methods-for-assigning-users-and-groups)
- [Delegování oprávnění k registraci aplikací ve službě Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/roles-delegate-app-roles)
- [Pravidla dynamického členství pro skupiny ve službě Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership)

## <a name="access-policies"></a>Zásady přístupu

### <a name="named-locations"></a>Pojmenovaná umístění

S [pojmenovanými umístěními](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations) ve službě Azure AD můžete označit důvěryhodné rozsahy IP adres ve vaší organizaci. Azure AD používá pojmenovaná umístění ke:

- Zabránit falešným poplachům v rizikových událostech. Přihlášení z důvěryhodného síťového umístění snižuje riziko přihlášení uživatele.
- Konfigurace [podmíněného přístupu založeného na poloze](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations).

![Pojmenované umístění](./media/active-directory-ops-guide/active-directory-ops-img10.png)

Na základě priority najdete v následující tabulce doporučené řešení, které nejlépe vyhovuje potřebám vaší organizace:

| **Priorita** | **Scénář** | **Doporučení** |
| ------------ | -------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------- |
| 1 | Pokud používáte PHS nebo PTA a pojmenované lokality nebyly definovány | Definování pojmenovaných umístění pro zlepšení detekce rizikových událostí |
| 2 | Pokud jste federováni a nepoužíváte deklaraci "insideCorporateNetwork" a nebyla definována pojmenovaná umístění | Definování pojmenovaných umístění pro zlepšení detekce rizikových událostí |
| 3 | Pokud v zásadách podmíněného přístupu nepoužíváte pojmenované lokality a v zásadách podmíněného přístupu neexistuje žádné riziko ani ovládací prvky zařízení | Nakonfigurujte zásady podmíněného přístupu tak, aby zahrnovaly pojmenovaná umístění. |
| 4 | Pokud jste federováni a používáte deklaraci "insideCorporateNetwork" a pojmenované umístění nebyly definovány | Definování pojmenovaných umístění pro zlepšení detekce rizikových událostí |
| 5 | Pokud používáte důvěryhodné adresy IP s vícefaktorovým faktorem než s pojmenovanými umístěními a označujete je jako důvěryhodné | Definování pojmenovaných umístění a jejich označení jako důvěryhodných pro zlepšení detekce rizikových událostí |

### <a name="risk-based-access-policies"></a>Zásady přístupu založené na rizicích

Azure AD můžete vypočítat riziko pro každého přihlášení a každého uživatele. Použití rizika jako kritéria v přístupových zásadách může poskytnout lepší uživatelské prostředí, například méně výzev k ověření a lepší zabezpečení, například pouze vyzvat uživatele, když jsou potřeba, a automatizovat odpověď a nápravu.

![Zásady rizik pro přihlášení](./media/active-directory-ops-guide/active-directory-ops-img11.png)

Pokud už vlastníte licence Azure AD Premium P2, které podporují použití rizika v zásadách přístupu, ale nejsou používány, důrazně doporučujeme přidat riziko do stavu zabezpečení.

#### <a name="risk-based-access-policies-recommended-reading"></a>Zásady přístupu založené na rizicích doporučené čtení

- [Postup: Konfigurace zásad rizik přihlášení](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-sign-in-risk-policy)
- [Postup: Konfigurace zásad rizik pro uživatele](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-user-risk-policy)

### <a name="client-application-access-policies"></a>Zásady přístupu klientských aplikací

Microsoft Intune Application Management (MAM) poskytuje možnost nabízení ovládacích prvků ochrany dat, jako je šifrování úložiště, PIN, vyčištění vzdáleného úložiště atd., do kompatibilních klientských mobilních aplikací, jako je Outlook Mobile. Kromě toho lze vytvořit zásady podmíněného přístupu, které [omezí přístup ke](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access) cloudovým službám, jako je Exchange Online, ze schválených nebo kompatibilních aplikací.

Pokud vaši zaměstnanci instalují aplikace podporující MAM, jako jsou mobilní aplikace Office, pro přístup k podnikovým prostředkům, jako je Exchange Online nebo SharePoint Online, a vy také podporujete BYOD (přineste si vlastní zařízení), doporučujeme nasadit zásady aplikace MAM pro správu konfigurace aplikace v osobně vlastněných zařízeních bez registrace MDM a pak aktualizovat zásady podmíněného přístupu tak, aby umožňovaly přístup pouze od klientů podporujících MAM.

![Řízení udělení podmíněného přístupu](./media/active-directory-ops-guide/active-directory-ops-img12.png)

Pokud zaměstnanci instalují aplikace podporující MAM proti podnikovým prostředkům a přístup je omezen na zařízeních Spravovaných Intune, měli byste zvážit nasazení zásad MAM aplikací pro správu konfigurace aplikace pro osobní zařízení a aktualizovat zásady podmíněného přístupu, aby byl povolen pouze přístup od klientů podporujících MAM.

### <a name="conditional-access-implementation"></a>Implementace podmíněného přístupu

Podmíněný přístup je základním nástrojem pro zlepšení stavu zabezpečení vaší organizace. Proto je důležité dodržovat tyto osvědčené postupy:

- Ujistěte se, že všechny aplikace SaaS mají alespoň jednu zásadu
- Vyhněte se kombinování filtru **Všechny aplikace** s ovládacím **prvkem bloku,** abyste se vyhnuli riziku uzamčení
- Nepoužívejte jako filtr **všechny uživatele** a nechtěně přidávejte **hosty.**
- **Migrace všech zásad "staršíverze" na portál Azure**
- Zachycení všech kritérií pro uživatele, zařízení a aplikace
- Použití zásad podmíněného přístupu k [implementaci vícefaktorového ověřování](https://docs.microsoft.com/azure/active-directory/conditional-access/plan-conditional-access), nikoli pomocí **vícefaktorového ověřování pro jednotlivé uživatele.**
- Mít malou sadu základních zásad, které lze použít pro více aplikací
- Definování prázdných skupin výjimek a jejich přidání do zásad, které mají strategii výjimek
- Plánování účtů [rozbití skla](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-admin-roles-secure#break-glass-what-to-do-in-an-emergency) bez kontrol vícefaktorové kontroly
- Zajistěte konzistentní prostředí napříč klientskými aplikacemi Office 365, například Teams, OneDrive pro firmy, Outlook atd.) implementací stejné sady ovládacích prvků pro služby, jako je Exchange Online a Sharepoint Online
- Přidělování politik by mělo být prováděno prostřednictvím skupin, nikoli jednotlivců
- Proveďte pravidelné kontroly skupin výjimek používaných v zásadách k omezení doby, po kterou jsou uživatelé mimo stav zabezpečení. Pokud vlastníte Azure AD P2, můžete použít kontroly přístupu k automatizaci procesu

#### <a name="conditional-access-recommended-reading"></a>Doporučená četba podmíněného přístupu

- [Doporučené postupy pro podmíněný přístup ve službě Azure Active Directory](https://docs.microsoft.com/azure/active-directory/conditional-access/best-practices)
- [Konfigurace identit a přístupu k zařízením](https://docs.microsoft.com/microsoft-365/enterprise/microsoft-365-policies-configurations)
- [Odkaz na nastavení podmíněného přístupu služby Azure Active Directory](https://docs.microsoft.com/azure/active-directory/conditional-access/technical-reference)
- [Běžné zásady podmíněného přístupu](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-policy-common)

## <a name="access-surface-area"></a>Přístupová plocha

### <a name="legacy-authentication"></a>Starší verze ověřování

Silná pověření, jako je například MFA, nemohou chránit aplikace pomocí starších ověřovacích protokolů, které z něj činí upřednostňovaný vektor útoku škodlivými aktéry. Uzamčení starší verze ověřování je zásadní pro zlepšení stavu zabezpečení přístupu.

Starší verze ověřování je termín, který odkazuje na ověřovací protokoly používané aplikacemi, jako jsou:

- Starší klienti Office, kteří nepoužívají moderní ověřování (například klient Office 2010)
- Klienti, kteří používají poštovní protokoly, například IMAP/SMTP/POP

Útočníci silně preferují tyto protokoly - ve skutečnosti téměř [100% útoků heslem spreje](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984) používá starší ověřovací protokoly! Hackeři používají starší ověřovací protokoly, protože nepodporují interaktivní přihlášení, které je potřebné pro další problémy se zabezpečením, jako je vícefaktorové ověřování a ověřování zařízení.

Pokud starší verze ověřování je široce používán ve vašem prostředí, měli byste naplánovat migraci starších klientů klientů, kteří podporují [moderní ověřování](https://docs.microsoft.com/office365/enterprise/modern-auth-for-office-2013-and-2016) co nejdříve. Ve stejném tokenu, pokud máte někteří uživatelé již používají moderní ověřování, ale jiní, které stále používají starší verze ověřování, měli byste provést následující kroky k uzamčení starších klientů ověřování:

1. Sestavy [aktivit přihlášení](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins) slouží k identifikaci uživatelů, kteří stále používají starší verze ověřování a nápravu plánu:

   a. Upgradujte na klienty s podporou moderního ověřování pro postižené uživatele.
   
   b. Naplánujte si časový rámec pro uzamčení podle níže uvedených kroků.
   
   c. Určete, které starší aplikace mají tvrdou závislost na starším ověřování. Viz krok 3 níže.

2. Zakažte starší protokoly u zdroje (například poštovní schránka exchange) pro uživatele, kteří nepoužívají starší auth, aby se zabránilo větší expozici.
3. Pro zbývající účty (v ideálním případě nelidské identity, jako jsou účty služeb), použijte [podmíněný přístup k omezení starších protokolů](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Conditional-Access-support-for-blocking-legacy-auth-is/ba-p/245417) po ověření.

#### <a name="legacy-authentication-recommended-reading"></a>Starší verze ověřování doporučená čtení

- [Povolení nebo zakázání přístupu pop3 nebo IMAP4 k poštovním schránkám na Exchange Serveru](https://docs.microsoft.com/exchange/clients/pop3-and-imap4/configure-mailbox-access?view=exchserver-2019)

### <a name="consent-grants"></a>Udělení souhlasu

Při útoku udělení nedovoleného souhlasu útočník vytvoří aplikaci registrovanou na Azure AD, která požaduje přístup k datům, jako jsou kontaktní informace, e-mail nebo dokumenty. Uživatelé mohou udělovat souhlas škodlivým aplikacím prostřednictvím phishingových útoků při přistávání na škodlivých webech.

Níže je uveden seznam aplikací s oprávněními, které byste mohli chtít prozkoumat pro cloudové služby Microsoftu:

- Aplikace s aplikací \*nebo delegované . Oprávnění ke čtení
- Aplikace s delegovanými oprávněními mohou číst, odesílat nebo spravovat e-maily jménem uživatele.
- Aplikace, kterým jsou udělena následující oprávnění:

| Prostředek | Oprávnění |
| :- | :- |
| Office 365 Exchange Online | Eas. AccessAsUser.All |
| | Ews. AccessAsUser.All |
| | Mail.Read |
| Microsoft Graph API | Mail.Read |
| | Mail.Read.Shared |
| | Mail.ReadWrite |

- Aplikace udělily úplné zosobnění uživatele přihlášeného uživatele. Například:

|Prostředek | Oprávnění |
| :- | :- |
| Microsoft Graph API| Adresář.AccessasUser.All |
| Azure REST API | user_impersonation |

Chcete-li se tomuto scénáři vyhnout, měli byste se obrátit na [zjišťování a nápravu nedovolených udělení souhlasu v office 365](https://docs.microsoft.com/office365/securitycompliance/detect-and-remediate-illicit-consent-grants) k identifikaci a opravě všech žádostí s nedovolenými granty nebo žádostmi, které mají více grantů, než je nezbytné. Dále [zcela odstraňte samoobslužné služby](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-user-consent) a [zaveďte postupy správy .](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-admin-consent-workflow) Nakonec naplánujte pravidelné recenze oprávnění aplikací a odeberte je, když nejsou potřeba.

#### <a name="consent-grants-recommended-reading"></a>Souhlas uděluje doporučené čtení

- [Oprávnění rozhraní API aplikace Microsoft Graph](https://docs.microsoft.com/graph/permissions-reference)

### <a name="user-and-group-settings"></a>Nastavení uživatelů a skupin

Níže jsou uvedeny nastavení uživatelů a skupin, které lze uzamknout, pokud neexistuje explicitní obchodní potřeba:

#### <a name="user-settings"></a>Uživatelská nastavení

- **Externí uživatelé** – externí spolupráce může probíhat organicky v podniku se službami, jako jsou Teams, Power BI, Sharepoint Online a Azure Information Protection. Pokud máte explicitní omezení pro řízení externí spolupráce iniciované uživatelem, doporučujeme povolit externím uživatelům pomocí [správy nároků Azure AD](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview) nebo řízené operace, například prostřednictvím help desku. Pokud nechcete povolit ekologickou externí spolupráci pro služby, můžete [členům zcela zablokovat pozvání externích uživatelů](https://docs.microsoft.com/azure/active-directory/b2b/delegate-invitations). Případně můžete také [povolit nebo blokovat určité domény](https://docs.microsoft.com/azure/active-directory/b2b/allow-deny-list) v externích uživatelských pozvánkách.
- **Registrace aplikací** – pokud jsou povoleny registrace aplikací, koncoví uživatelé mohou připojit aplikace sami a udělit přístup ke svým datům. Typickým příkladem registrace aplikací jsou uživatelé, kteří umožňují moduly plug-in aplikace Outlook nebo hlasové asistenty, jako jsou Alexa a Siri, číst jejich e-maily a kalendář nebo odesílat e-maily jejich jménem. Pokud se zákazník rozhodne registraci aplikací vypnout, musí se týmy InfoSec a IAM podílet na správě výjimek (registrace aplikací, které jsou potřeba na základě obchodních požadavků), protože by potřebovaly zaregistrovat aplikace s účtem správce a s největší pravděpodobností vyžadují návrh procesu pro zprovoznění procesu.
- **Portál pro správu** – organizace můžou uzamknout okno Azure AD na webu Azure Portal, aby uživatelé, kteří nejsou správci, neměli přístup ke správě Azure AD na webu Azure Portal a zmátli se. Chcete-li omezit přístup, přejděte na uživatelské nastavení na portálu pro správu Azure AD:

![Omezený přístup portálu pro správu](./media/active-directory-ops-guide/active-directory-ops-img13.png)

> [!NOTE]
> Non-adminstrators stále přístup k rozhraní pro správu Azure AD prostřednictvím příkazového řádku a dalších programových rozhraní.

#### <a name="group-settings"></a>Nastavení skupin

**Samoobslužná správa skupin / Uživatelé mohou vytvářet skupiny zabezpečení / Skupiny O365.** Pokud neexistuje žádná aktuální samoobslužná iniciativa pro skupiny v cloudu, zákazníci se mohou rozhodnout vypnout, dokud nebudou připraveni k použití této funkce.

#### <a name="groups-recommended-reading"></a>Skupiny doporučené čtení

- [Co je spolupráce B2B v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)
- [Integrace aplikací s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)
- [Aplikace, oprávnění a souhlas ve službě Azure Active Directory.](https://docs.microsoft.com/azure/active-directory/active-directory-apps-permissions-consent)
- [Správa přístupu ke zdrojům služby Azure Active Directory pomocí skupin](https://docs.microsoft.com/azure/active-directory/active-directory-manage-groups)
- [Nastavení samoobslužné správy přístupu k aplikacím ve službě Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management)

### <a name="traffic-from-unexpected-locations"></a>Provoz z neočekávaných míst

Útočníci pocházejí z různých částí světa. Spravujte toto riziko pomocí zásad podmíněného přístupu s umístěním jako podmínkou. [Podmínka umístění](https://docs.microsoft.com/azure/active-directory/conditional-access/location-condition) zásadpodmíněného přístupu umožňuje blokovat přístup pro umístění, odkud není žádný obchodní důvod pro přihlášení.

![Vytvoření nového pojmenovaného umístění](./media/active-directory-ops-guide/active-directory-ops-img14.png)

Pokud je k dispozici, použijte řešení správy informací o zabezpečení a událostí (SIEM) k analýze a vyhledání vzorů přístupu napříč oblastmi. Pokud nepoužíváte produkt SIEM nebo nepřijímáte ověřovací informace z Azure AD, doporučujeme použít [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) k identifikaci vzorů přístupu napříč oblastmi.

## <a name="access-usage"></a>Využití přístupu

### <a name="azure-ad-logs-archived-and-integrated-with-incident-response-plans"></a>Protokoly Azure AD archivované a integrované s plány reakce na incidenty

Přístup k přihlašovací aktivitě, auditům a rizikovým událostem pro Azure AD je zásadní pro řešení potíží, analýzy využití a forenzní vyšetřování. Azure AD poskytuje přístup k těmto zdrojům prostřednictvím rest API, které mají omezenou dobu uchovávání. Systém správy informací a událostí (SIEM) zabezpečení nebo ekvivalentní archivní technologie je klíčem k dlouhodobému ukládání auditů a podporovatelnosti. Chcete-li povolit dlouhodobé ukládání protokolů Azure AD, musíte je buď přidat do stávajícího řešení SIEM, nebo použít [Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-activity-logs-azure-monitor). Archivní protokoly, které lze použít jako součást plánů reakce na incidenty a vyšetřování.

#### <a name="logs-recommended-reading"></a>Protokoly doporučené čtení

- [Odkaz na rozhraní API pro audit služby Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-audit-reference)
- [Odkaz na rozhraní API pro přihlášení k službě Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-sign-in-activity-reference)
- [Získání dat pomocí rozhraní API pro generování sestav Azure AD s certifikáty](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-with-certificates)
- [Microsoft Graph pro ochranu identity služby Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-graph-getting-started)
- [Odkaz na rozhraní API pro správu služby Office 365](https://msdn.microsoft.com/office-365/office-365-management-activity-api-reference)
- [Jak používat balíček obsahu Azure Active Directory Power BI Content Pack](../reports-monitoring/howto-use-azure-monitor-workbooks.md)

## <a name="summary"></a>Souhrn

Existuje 12 aspektů zabezpečené identity infrastruktury. Tento seznam vám pomůže dále zabezpečit a spravovat přihlašovací údaje, definovat možnosti ověřování, delegovat přiřazení, měřit využití a definovat zásady přístupu na základě stavu zabezpečení rozlehlé sítě.

- Přiřaďte vlastníky ke klíčovým úkolům.
- Implementujte řešení pro detekci slabých nebo uniklých hesel, zlepšení správy a ochrany hesel a další zabezpečení přístupu uživatelů k prostředkům.
- Spravujte identitu zařízení a chraňte své prostředky kdykoli a z libovolného místa.
- Implementujte ověřování bez hesla.
- Poskytněte standardizovaný mechanismus jednotného přihlašování v celé organizaci.
- Migrujte aplikace ze služby AD FS do Služby Azure AD, abyste umožnili lepší zabezpečení a konzistentnější možnosti správy.
- Přiřaďte uživatele k aplikacím pomocí skupin, které umožňují větší flexibilitu a možnost správy ve velkém měřítku.
- Nakonfigurujte zásady přístupu založené na rizicích.
- Uzamkněte starší ověřovací protokoly.
- Odhalovat a naprostředkovat udělení nezákonného souhlasu.
- Uzamkněte nastavení uživatelů a skupin.
- Povolte dlouhodobé úložiště protokolů Azure AD pro řešení potíží, analýzy využití a forenzní vyšetřování.

## <a name="next-steps"></a>Další kroky

Začínáme s [provozními kontrolami a akcemi správy identit](active-directory-ops-guide-govern.md).
