---
title: Referenční příručka operací správy ověřování Azure Active Directory
description: Referenční příručka operací popisuje kontroly a akce, které byste měli provést při zabezpečení správy ověřování.
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
ms.openlocfilehash: 0d425111e151d60ab38a60e38cd1805cef39f606
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2021
ms.locfileid: "105641774"
---
# <a name="azure-active-directory-authentication-management-operations-reference-guide"></a>Referenční příručka operací správy ověřování Azure Active Directory

Tato část [Referenční příručka k operacím Azure AD](active-directory-ops-guide-intro.md) popisuje kontroly a akce, které byste měli provést k zabezpečení a správě přihlašovacích údajů, definování možnosti ověřování, přiřazení delegáta, využití měr a definování zásad přístupu na základě podnikových stav zabezpečení.

> [!NOTE]
> Tato doporučení jsou aktuální k datu publikování, ale mohou se v průběhu času měnit. Organizace by měly průběžně vyhodnocovat své postupy své identity, protože produkty a služby Microsoftu se v průběhu času vyvíjí.

## <a name="key-operational-processes"></a>Klíčové provozní procesy

### <a name="assign-owners-to-key-tasks"></a>Přiřazení vlastníků ke klíčovým úlohám

Správa Azure Active Directory vyžaduje průběžné provádění klíčových provozních úloh a procesů, které nemusí být součástí projektu zavedení. Stále je důležité, abyste nastavili tyto úlohy pro optimalizaci prostředí. Mezi klíčové úlohy a jejich Doporučené vlastníky patří:

| Úkol | Vlastník |
| :- | :- |
| Správa životního cyklu konfigurace jednotného přihlašování (SSO) ve službě Azure AD | Provozní tým IAM |
| Návrh zásad podmíněného přístupu pro aplikace Azure AD | Tým architektury InfoSec |
| Archivace přihlašovací aktivity v SIEM systému | Provozní tým InfoSec |
| Archivace rizikových událostí v SIEM systému | Provozní tým InfoSec |
| Třídění a zkoumání sestav zabezpečení | Provozní tým InfoSec |
| Třídění a vyšetřování rizikových událostí | Provozní tým InfoSec |
| Třídění a zkoumání uživatelů označených příznakem rizika a ohrožení zabezpečení z Azure AD Identity Protection | Provozní tým InfoSec |

> [!NOTE]
> Azure AD Identity Protection vyžaduje licenci Azure AD Premium P2. Správnou licenci pro vaše požadavky najdete v tématu [porovnání všeobecně dostupných funkcí Azure AD Free a Azure AD Premium edicí](https://azure.microsoft.com/pricing/details/active-directory/).

Při revizi seznamu se můžete setkat s tím, že budete muset buď přiřadit vlastníka pro úlohy, u kterých chybí vlastník, nebo upravit vlastnictví pro úlohy s vlastníky, které nejsou zarovnané na výše uvedená doporučení.

#### <a name="owner-recommended-reading"></a>Čtení Doporučené vlastníkem

- [Přiřazení rolí správce v Azure Active Directory](../roles/permissions-reference.md)
- [Zásady správného řízení v Azure](../../governance/index.yml)

## <a name="credentials-management"></a>Správa přihlašovacích údajů

### <a name="password-policies"></a>Zásady hesel

Zabezpečená správa hesel je jedním z nejdůležitějších součástí správy identit a přístupu a často se jedná o největší cíl útoků. Azure AD podporuje několik funkcí, které vám pomůžou zabránit úspěšnému útoku.

Pomocí následující tabulky Najděte doporučené řešení pro zmírnění problému, který je potřeba řešit:

| Problém | Doporučení |
| :- | :- |
| Žádný mechanismus ochrany proti slabým heslům | Povolení [samoobslužného resetování hesla služby Azure AD (SSPR)](../authentication/concept-sspr-howitworks.md) a [ochrany heslem](../authentication/concept-password-ban-bad-on-premises.md) |
| Žádný mechanismus pro detekci nevrácených hesel | Povolit [synchronizaci hodnot hash hesel](../hybrid/how-to-connect-password-hash-synchronization.md) (kosmetice) a získat přehledy |
| Použití AD FS a nelze přejít na spravované ověřování | Povolit [AD FS extranetové inteligentní uzamčení](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection) a/nebo [inteligentní uzamčení Azure AD](../authentication/howto-password-smart-lockout.md) |
| Zásady hesel používají pravidla na složitost, jako je délka, více znakových sad nebo vypršení platnosti. | Přeberte v úvahu [Doporučené postupy Microsoftu](https://www.microsoft.com/research/publication/password-guidance/?from=http%3A%2F%2Fresearch.microsoft.com%2Fpubs%2F265143%2Fmicrosoft_password_guidance.pdf) a přepněte svůj přístup na správu hesel a nasaďte [ochranu heslem Azure AD](../authentication/concept-password-ban-bad.md). |
| Uživatelé nejsou registrováni pro použití vícefaktorového ověřování (MFA). | [Zaregistrujte informace o zabezpečení všech uživatelů](../identity-protection/howto-identity-protection-configure-mfa-policy.md) , aby je bylo možné použít jako mechanismus ověření identity uživatele spolu s jejich heslem. |
| Neexistují žádná odvolání hesel na základě rizika uživatele. | Nasazení [zásad rizik uživatelů Azure AD Identity Protection](../identity-protection/howto-identity-protection-configure-risk-policies.md) pro vynucení změn hesel u vrácených přihlašovacích údajů pomocí SSPR |
| Neexistuje žádný mechanizmus inteligentního zamykání, který chrání škodlivé ověřování před nesprávnými aktéry přicházejících z identifikovaných IP adres. | Nasazení ověřování spravovaného cloudem buď pomocí synchronizace hodnot hash hesel, nebo [předávacího ověřování](../hybrid/how-to-connect-pta-quick-start.md) (PTA) |

#### <a name="password-policies-recommended-reading"></a>Doporučené zásady pro hesla

- [Osvědčené postupy pro Azure AD a AD FS: ochrana proti útokům prostřednictvím postřiku hesla – Enterprise Mobility + Security](https://cloudblogs.microsoft.com/enterprisemobility/2018/03/05/azure-ad-and-adfs-best-practices-defending-against-password-spray-attacks/)

### <a name="enable-self-service-password-reset-and-password-protection"></a>Povolení samoobslužného resetování hesla a ochrany heslem

Uživatelé, kteří potřebují změnit nebo resetovat svá hesla, jsou jedním z největších zdrojů objemu a náklady na volání helpdesku. Kromě nákladů je důležité se změnou hesla jako nástroje pro zmírnění rizika uživatele zásadním krokem při zlepšování stav zabezpečení vaší organizace.

Doporučujeme, abyste nasadili [Samoobslužné resetování hesla](../authentication/concept-sspr-howitworks.md) (SSPR) služby Azure AD a místní [ochranu heslem](../authentication/howto-password-ban-bad-on-premises-deploy.md) pro splnění:

- Zablokování volání helpdesku.
- Nahraďte použití dočasných hesel.
- Nahraďte všechna existující řešení samoobslužného hesla pro správu, která se spoléhá na místní řešení.
- [Eliminujte slabá hesla](../authentication/concept-password-ban-bad.md) ve vaší organizaci.

> [!NOTE]
> Pro organizace s předplatným Azure AD Premium P2 se doporučuje nasadit SSPR a používat je jako součást [zásad rizik uživatele ochrany identity](../identity-protection/howto-identity-protection-configure-risk-policies.md).

### <a name="strong-credential-management"></a>Správa silných přihlašovacích údajů

Hesla samy o sebe nejsou dostatečně zabezpečená, aby nedocházelo k špatným aktérům při získávání přístupu k vašemu prostředí. Pro službu Multi-Factor Authentication (MFA) musí být povolený minimálně každý uživatel s privilegovaným účtem. V ideálním případě byste měli povolit [kombinovanou registraci](../authentication/concept-registration-mfa-sspr-combined.md) a vyžadovat, aby se všichni uživatelé zaregistrovali pro MFA a SSPR pomocí [kombinovaného prostředí pro registraci](../user-help/security-info-setup-signin.md). Nakonec doporučujeme, abyste přijali strategii pro [zajištění odolnosti](../authentication/concept-resilient-controls.md) , která snižuje riziko uzamčení z důvodu neočekávaných okolností.

![Průběh kombinovaných uživatelských zkušeností](./media/active-directory-ops-guide/active-directory-ops-img4.png)

### <a name="on-premises-outage-authentication-resiliency"></a>Odolnost ověřování při místním výpadku

Kromě výhod jednoduchosti a povolení detekce nevrácených přihlašovacích údajů, synchronizace hodnot hash hesel služby Azure AD (KOSMETICE) a Azure AD MFA umožňuje uživatelům přístup k aplikacím SaaS a Microsoft 365i navzdory místnímu výpadku z důvodu kyberútokům, jako je [NotPetya](https://www.microsoft.com/security/blog/2018/02/05/overview-of-petya-a-rapid-cyberattack/). KOSMETICE je také možné povolit ve spojení s federaci. Povolení KOSMETICE umožňuje použití záložního ověřování v případě, že nejsou k dispozici federační služby.

Pokud v místní organizaci chybí strategie odolnosti proti výpadkům nebo má jednu, která není integrovaná se službou Azure AD, měli byste nasadit Azure AD KOSMETICE a definovat plán zotavení po havárii, který obsahuje KOSMETICE. Povolení služby Azure AD KOSMETICE umožní uživatelům ověřování ve službě Azure AD, pokud vaše místní služba Active Directory nebude k dispozici.

![tok synchronizace hodnot hash hesel](./media/active-directory-ops-guide/active-directory-ops-img5.png)

Pokud chcete lépe porozumět možnostem ověřování, přečtěte si téma [Volba správné metody ověřování pro Azure Active Directory řešení hybridní identity](../hybrid/choose-ad-authn.md).

### <a name="programmatic-usage-of-credentials"></a>Programové použití přihlašovacích údajů

Skripty Azure AD s využitím PowerShellu nebo aplikací, které používají rozhraní Microsoft Graph API, vyžadují zabezpečené ověřování. Nízká Správa přihlašovacích údajů provádí tyto skripty a nástroje, které zvyšují riziko krádeže přihlašovacích údajů. Pokud používáte skripty nebo aplikace, které spoléhají na pevně kódovaná hesla nebo výzvy k zadání hesla, měli byste nejdřív zkontrolovat hesla v konfiguračních souborech nebo ve zdrojovém kódu, pak tyto závislosti nahradit a používat spravované identity Azure, Integrated-Windows ověřování nebo [certifikáty](../reports-monitoring/tutorial-access-api-with-certificates.md) , kdykoli to bude možné. U aplikací, kde předchozí řešení nemůžete použít, zvažte použití [Azure Key Vault](https://azure.microsoft.com/services/key-vault/).

Pokud zjistíte, že existují instanční objekty s přihlašovacími údaji hesla a nejste si jistí, jak jsou přihlašovací údaje hesla zabezpečené skripty nebo aplikacemi, obraťte se na vlastníka aplikace, aby lépe pochopil vzorce používání.

Microsoft také doporučuje, abyste kontaktovali vlastníky aplikací a pochopili vzory používání, pokud existují instanční objekty s přihlašovacími údaji hesla.

## <a name="authentication-experience"></a>Prostředí ověřování

### <a name="on-premises-authentication"></a>Místní ověřování

Federované ověřování s integrovaným ověřováním systému Windows (IWA) nebo bezproblémové ověřování typu Single Sign-On (SSO) se spravovaným ověřováním hodnoty hash hesla nebo předávacím ověřováním je nejlepší uživatelské prostředí, když se nachází v podnikové síti s uživatelským dohledem na místních řadičích domény. Minimalizuje únavu výzvy k zadání přihlašovacích údajů a snižuje riziko, že uživatelé Prey útoky typu phishing. Pokud už používáte ověřování pomocí cloudu s KOSMETICE nebo PTA, ale uživatelé pořád potřebují zadat svoje heslo při ověřování v místním prostředí, měli byste hned [nasadit bezproblémové jednotné přihlašování](../hybrid/how-to-connect-sso.md). Na druhé straně, pokud jste v současné době federované s plány, abyste nakonec migrovali na ověřování pomocí cloudu, měli byste v rámci projektu migrace implementovat bezproblémové jednotné přihlašování.

### <a name="device-trust-access-policies"></a>Zásady přístupu důvěryhodnosti zařízení

Podobně jako uživatel ve vaší organizaci je zařízení základní identitou, kterou chcete chránit. Identitu zařízení můžete použít k ochraně svých prostředků kdykoli a z libovolného místa. Ověřování zařízení a monitorování účtů pro svůj typ vztahu důvěryhodnosti vylepšuje stav a použitelnost zabezpečení:

- Zamezení tření, například při ověřování MFA, když je zařízení důvěryhodné
- Blokování přístupu z nedůvěryhodných zařízení
- V případě zařízení s Windows 10 je [možné bezproblémově zajistit jednotné přihlašování k místním prostředkům](../devices/azuread-join-sso.md).

Tento cíl můžete provést tak, že navedete identity zařízení a spravujete je ve službě Azure AD pomocí jedné z následujících metod:

- Organizace můžou pomocí [Microsoft Intune](/intune/what-is-intune) spravovat zařízení a vystavovat zásady dodržování předpisů, ověřit stav zařízení a nastavit zásady podmíněného přístupu na základě toho, jestli zařízení dodržuje předpisy. Microsoft Intune může spravovat zařízení se systémem iOS, stolní počítače Mac (prostřednictvím integrace JAMF), desktopy Windows (nativně pomocí správy mobilních zařízení pro Windows 10 a spolusprávu pomocí služby Microsoft Endpoint Configuration Manager) a mobilních zařízení s Androidem.
- [Připojení k hybridní službě Azure AD](../devices/hybrid-azuread-join-managed-domains.md) poskytuje správu pomocí zásad skupiny nebo Microsoft Endpoint Configuration Manager v prostředí s počítači připojenými k doméně služby Active Directory. Organizace můžou nasadit spravované prostředí prostřednictvím KOSMETICE nebo PTA s bezproblémové jednotné přihlašování. Uvedení zařízení do Azure AD maximalizuje produktivitu uživatelů prostřednictvím jednotného přihlašování napříč vaším cloudem a místními prostředky a zároveň vám umožní zabezpečit přístup k vašemu cloudu a místním prostředkům s [podmíněným přístupem](../conditional-access/overview.md) současně.

Pokud máte zařízení s Windows připojená k doméně, která nejsou registrovaná v cloudu, nebo zařízení s Windows připojená k doméně, která jsou zaregistrovaná v cloudu, ale bez zásad podmíněného přístupu, měli byste zaregistrovat neregistrovaná zařízení a v obou případech [použít hybridní službu Azure AD JOIN jako ovládací prvek](../conditional-access/require-managed-devices.md) v zásadách podmíněného přístupu.

![Snímek obrazovky udělení v zásadách podmíněného přístupu vyžadujících hybridní zařízení](./media/active-directory-ops-guide/active-directory-ops-img6.png)

Pokud spravujete zařízení s MDM nebo Microsoft Intune, ale v zásadách podmíněného přístupu nepoužíváte ovládací prvky zařízení, doporučujeme, abyste v těchto zásadách označili jako ovládací prvek v části [vyžadovat, aby zařízení bylo označené jako vyhovující](../conditional-access/require-managed-devices.md#require-device-to-be-marked-as-compliant) .

![Snímek obrazovky s oprávněním v zásadách podmíněného přístupu vyžadujících dodržování předpisů pro zařízení](./media/active-directory-ops-guide/active-directory-ops-img7.png)

#### <a name="device-trust-access-policies-recommended-reading"></a>Doporučené čtení zásad přístupu důvěryhodnosti zařízení

- [Postupy: plánování implementace služby Hybrid Azure Active Directory JOIN](../devices/hybrid-azuread-join-plan.md)
- [Konfigurace identit a přístupu k zařízením](/microsoft-365/enterprise/microsoft-365-policies-configurations)

### <a name="windows-hello-for-business"></a>Windows Hello pro firmy

Ve Windows 10 nahrazuje [Windows Hello pro firmy](/windows/security/identity-protection/hello-for-business/hello-identity-verification) hesla se silným dvojúrovňovém ověřováním na počítačích. Windows Hello pro firmy umožňuje efektivnější práci s MFA pro uživatele a snižuje vaši závislost na heslech. Pokud jste nezačali instalovat zařízení s Windows 10 nebo je jenom částečně nasadili, doporučujeme upgradovat na Windows 10 a [Povolit Windows Hello pro firmy](/windows/security/identity-protection/hello-for-business/hello-manage-in-organization) na všech zařízeních.

Pokud se chcete dozvědět víc o ověřování bez hesla, přečtěte si část [World bez hesel s Azure Active Directory](../authentication/concept-authentication-passwordless.md).

## <a name="application-authentication-and-assignment"></a>Ověřování a přiřazení aplikace

### <a name="single-sign-on-for-apps"></a>Jednotné přihlašování pro aplikace

Zajištění standardizovaného mechanismu jednotného přihlašování pro celou firmu je klíčové pro nejlepší uživatelské prostředí, snížení rizika, schopnost vykazovat a zásad správného řízení. Pokud používáte aplikace, které podporují jednotné přihlašování s Azure AD, ale jsou aktuálně nakonfigurované pro používání místních účtů, měli byste tyto aplikace překonfigurovat tak, aby používaly jednotné přihlašování s Azure AD. Podobně platí, že pokud používáte nějaké aplikace, které podporují jednotné přihlašování s Azure AD, ale používáte jiného poskytovatele identity, měli byste tyto aplikace překonfigurovat tak, aby používaly jednotné přihlašování i s Azure AD. Pro aplikace, které nepodporují federační protokoly, ale podporují ověřování založené na formulářích, doporučujeme, abyste aplikaci nakonfigurovali, aby používala používání [trezoru hesel](../manage-apps/application-proxy-configure-single-sign-on-password-vaulting.md) s Azure proxy aplikací služby AD.

![AppProxy přihlašování založené na heslech](./media/active-directory-ops-guide/active-directory-ops-img8.png)

> [!NOTE]
> Pokud nemáte mechanismus pro zjišťování nespravovaných aplikací ve vaší organizaci, doporučujeme implementovat proces zjišťování pomocí řešení Cloud Access Security broker (CASB), jako je například [Microsoft Cloud App Security](https://www.microsoft.com/enterprise-mobility-security/cloud-app-security).

Nakonec, pokud máte galerii aplikací Azure AD a používáte aplikace, které podporují jednotné přihlašování pomocí služby Azure AD, doporučujeme [v galerii aplikací zobrazit seznam aplikací](../develop/v2-howto-app-gallery-listing.md).

#### <a name="single-sign-on-recommended-reading"></a>Doporučené čtení jednotného přihlašování

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

### <a name="migration-of-ad-fs-applications-to-azure-ad"></a>Migrace aplikací AD FS do služby Azure AD

[Migrace aplikací z AD FS do služby Azure AD](../manage-apps/migrate-adfs-apps-to-azure.md) umožňuje využívat další možnosti zabezpečení, lepší spravovatelnost a lepší spolupráci. Pokud máte aplikace nakonfigurované v AD FS, které podporují jednotné přihlašování pomocí služby Azure AD, měli byste tyto aplikace překonfigurovat tak, aby používaly jednotné přihlašování s Azure AD. Pokud máte aplikace nakonfigurované v AD FS s neběžnými konfiguracemi, které služba Azure AD nepodporuje, měli byste se obrátit na vlastníky aplikace, abyste zjistili, jestli je zvláštní konfigurace absolutním požadavkem aplikace. Pokud to nepotřebujete, měli byste aplikaci překonfigurovat tak, aby používala jednotné přihlašování s Azure AD.

![Azure AD jako primární zprostředkovatel identity](./media/active-directory-ops-guide/active-directory-ops-img9.png)

> [!NOTE]
> [Azure AD Connect Health pro službu AD FS](../hybrid/how-to-connect-health-adfs.md) je možné použít ke shromažďování podrobností o konfiguraci jednotlivých aplikací, které je možné migrovat do Azure AD.

### <a name="assign-users-to-applications"></a>Přiřazení uživatelů k aplikacím

[Přiřazování uživatelů k aplikacím](../manage-apps/assign-user-or-group-access-portal.md) je nejlépe namapované pomocí skupin, protože umožňují větší flexibilitu a možnost správy ve velkém měřítku. Mezi výhody používání skupin patří [členství v dynamické skupině založené na atributech](../enterprise-users/groups-dynamic-membership.md) a [delegování pro vlastníky aplikace](../fundamentals/active-directory-accessmanagement-managing-group-owners.md). Proto, pokud už používáte a spravujete skupiny, doporučujeme, abyste provedli následující akce, abyste vylepšili správu ve velkém měřítku:

- Delegování správy skupin a zásad správného řízení vlastníkům aplikací.
- Povolí přístup k aplikaci samoobslužné služby.
- Definujte dynamické skupiny, pokud atributy uživatele můžou konzistentně určit přístup k aplikacím.
- Implementujte ověření do skupin používaných pro přístup k aplikacím pomocí kontrol [přístupu Azure AD](../governance/access-reviews-overview.md).

Na druhé straně, pokud najdete aplikace, které mají přiřazení jednotlivým uživatelům, nezapomeňte pro tyto aplikace implementovat zásady [správného řízení](../governance/index.yml) .

#### <a name="assign-users-to-applications-recommended-reading"></a>Přiřazení uživatelů k aplikacím – doporučené čtení

- [Přiřazení uživatelů a skupin k aplikaci v Azure Active Directory](../manage-apps/assign-user-or-group-access-portal.md)
- [Delegovat oprávnění k registraci aplikace v Azure Active Directory](../roles/delegate-app-roles.md)
- [Pravidla dynamického členství pro skupiny v Azure Active Directory](../enterprise-users/groups-dynamic-membership.md)

## <a name="access-policies"></a>Zásady přístupu

### <a name="named-locations"></a>Pojmenovaná umístění

S [pojmenovanými umístěními](../reports-monitoring/quickstart-configure-named-locations.md) ve službě Azure AD můžete označovat rozsahy důvěryhodných IP adres ve vaší organizaci. Azure AD používá pojmenovaná umístění ke:

- V rizikových událostech Zabraňte falešně pozitivním událostem. Přihlášení z důvěryhodného síťového umístění snižuje riziko přihlášení uživatele.
- Nakonfigurujte [podmíněný přístup na základě umístění](../reports-monitoring/quickstart-configure-named-locations.md).

![Pojmenované umístění](./media/active-directory-ops-guide/active-directory-ops-img10.png)

Podle priority použijte následující tabulku k vyhledání doporučeného řešení, které nejlépe vyhovuje potřebám vaší organizace:

| **Priorita** | **Scénář** | **Doporučení** |
| ------------ | -------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------- |
| 1 | Pokud používáte KOSMETICE nebo PTA a nebyla definována pojmenovaná umístění | Definování pojmenovaných umístění pro zlepšení detekce rizikových událostí |
| 2 | Pokud jste federované a nepoužíváte deklaraci "insideCorporateNetwork" a pojmenovaná umístění nebyla definována. | Definování pojmenovaných umístění pro zlepšení detekce rizikových událostí |
| 3 | Pokud v zásadách podmíněného přístupu nepoužíváte pojmenovaná umístění a v zásadách podmíněného přístupu není žádné řízení rizik nebo zařízení | Nakonfigurujte zásady podmíněného přístupu tak, aby zahrnovaly pojmenovaná umístění. |
| 4 | Pokud jste federované a nedefinovali jste deklaraci identity "insideCorporateNetwork" a pojmenovaná umístění nebyla definována. | Definování pojmenovaných umístění pro zlepšení detekce rizikových událostí |
| 5 | Pokud používáte důvěryhodné IP adresy s MFA místo pojmenovaných umístění a označíte je jako důvěryhodné | Definujte pojmenovaná umístění a označte je jako důvěryhodná, aby se zlepšila detekce rizikových událostí. |

### <a name="risk-based-access-policies"></a>Zásady přístupu na základě rizika

Azure AD může vypočítat riziko pro každé přihlášení a každého uživatele. Použití rizika jako kritéria v zásadách přístupu může poskytovat lepší uživatelské prostředí, například méně výzev k ověření a lepší zabezpečení, například zobrazit pouze uživatele, když jsou potřeba, a automatizovat odpověď a nápravu.

![Zásady rizik přihlašování](./media/active-directory-ops-guide/active-directory-ops-img11.png)

Pokud již vlastníte licence Azure AD Premium P2, které podporují používání rizik v zásadách přístupu, ale nejsou používány, důrazně doporučujeme, abyste přidali riziko pro stav zabezpečení.

#### <a name="risk-based-access-policies-recommended-reading"></a>Zásady přístupu na základě rizika – doporučené čtení

- [Postupy: Konfigurace zásad rizik přihlašování](../identity-protection/howto-identity-protection-configure-risk-policies.md)
- [Postupy: Konfigurace zásad rizik uživatelů](../identity-protection/howto-identity-protection-configure-risk-policies.md)

### <a name="client-application-access-policies"></a>Zásady přístupu k klientským aplikacím

Správa aplikací Microsoft Intune (MAM) umožňuje doručovat ovládací prvky ochrany dat, jako je šifrování úložiště, kód PIN, vyčištění vzdáleného úložiště atd., na kompatibilní klientské mobilní aplikace, jako je například Outlook Mobile. Kromě toho je možné vytvořit zásady podmíněného přístupu, které [omezují přístup](../conditional-access/app-based-conditional-access.md) ke cloudovým službám, jako je Exchange Online, ze schválených nebo kompatibilních aplikací.

Pokud vaši zaměstnanci instalují aplikace s podporou MAM, jako jsou třeba mobilní aplikace Office, pro přístup k podnikovým prostředkům, jako je Exchange Online nebo SharePoint Online, a také BYOD (Přineste si vlastní zařízení), doporučujeme nasadit zásady MAM aplikací pro správu konfigurace aplikace v zařízeních v osobním vlastnictví bez registrace MDM a pak aktualizovat zásady podmíněného přístupu tak, aby povolovaly přístup jenom z klientů s podporou MAM.

![Řízení udělení podmíněného přístupu](./media/active-directory-ops-guide/active-directory-ops-img12.png)

Pokud mají zaměstnanci instalovat aplikace s podporou MAM proti podnikovým prostředkům a přístup je omezený na zařízeních spravovaných přes Intune, měli byste zvážit nasazení zásad MAM aplikací pro správu konfigurace aplikací pro osobní zařízení a aktualizace zásad podmíněného přístupu tak, aby povolovaly přístup jenom z klientů s podporou MAM.

### <a name="conditional-access-implementation"></a>Implementace podmíněného přístupu

Podmíněný přístup je důležitým nástrojem pro zlepšení stav zabezpečení vaší organizace. Proto je důležité dodržovat tyto osvědčené postupy:

- Ujistěte se, že všechny aplikace SaaS mají použitou aspoň jednu zásadu.
- Nepoužívejte kombinaci filtru **všechny aplikace** s ovládacím prvkem **blokování** , aby nedošlo k riziku uzamčení.
- Nepoužívejte **všechny uživatele** jako filtr a nechtěně přidejte **hosty** .
- **Migrovat všechny "staré" zásady na Azure Portal**
- Zachytit všechna kritéria pro uživatele, zařízení a aplikace
- Použití zásad podmíněného přístupu k [implementaci vícefaktorového ověřování](../conditional-access/plan-conditional-access.md)(MFA) místo použití **MFA pro jednotlivé uživatele**
- Máte malou sadu základních zásad, které se můžou vztahovat na víc aplikací.
- Definování prázdných skupin výjimek a jejich přidání do zásad, které mají strategii výjimek
- Plánování účtů pro [rozbití skla](../roles/security-planning.md#break-glass-what-to-do-in-an-emergency) bez ovládacích prvků MFA
- Zajistěte konzistentní prostředí napříč Microsoft 365 klientskými aplikacemi, například týmy, OneDrive, Outlook atd.) implementací stejné sady ovládacích prvků pro služby, jako je Exchange Online a SharePoint Online
- Přiřazení k zásadám by se mělo implementovat pomocí skupin, ne jednotlivců.
- Provádějte pravidelné kontroly skupin výjimek používaných v zásadách, abyste omezili čas, kdy se uživatelé nacházejí v stav zabezpečení. Pokud vlastníte Azure AD P2, můžete k automatizaci procesu použít kontroly přístupu.

#### <a name="conditional-access-recommended-reading"></a>Doporučené čtení pro podmíněný přístup

- [Osvědčené postupy pro podmíněný přístup v Azure Active Directory](../conditional-access/overview.md)
- [Konfigurace identit a přístupu k zařízením](/microsoft-365/enterprise/microsoft-365-policies-configurations)
- [Odkaz na nastavení Azure Active Directory podmíněného přístupu](../conditional-access/concept-conditional-access-conditions.md)
- [Společné zásady podmíněného přístupu](../conditional-access/concept-conditional-access-policy-common.md)

## <a name="access-surface-area"></a>Přístup k oblasti Surface

### <a name="legacy-authentication"></a>Starší verze ověřování

Silné přihlašovací údaje, jako je MFA, nemůžou chránit aplikace pomocí starších ověřovacích protokolů, které jsou preferovaným vektorem útoku škodlivými objekty actor. Uzamykání starší verze ověřování je klíčové pro zlepšení zabezpečení přístupu stav.

Starší verze ověřování je termín, který odkazuje na ověřovací protokoly používané aplikacemi, jako je:

- Starší klienti Office, kteří nepoužívají moderní ověřování (například klient Office 2010)
- Klienti, kteří používají poštovní protokoly jako IMAP/SMTP/POP

Útočníci silně upřednostňují tyto protokoly – ve skutečnosti téměř [100% útoků proti rozstřiku hesla](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984) používá starší protokoly ověřování. Hackeři používají starší ověřovací protokoly, protože nepodporují interaktivní přihlašování, které je potřeba pro další výzvy zabezpečení, jako je vícefaktorové ověřování a ověřování zařízení.

Pokud se ve vašem prostředí široce používá starší verze ověřování, měli byste naplánovat migraci starších klientů na klienty, kteří podporují [moderní ověřování](/office365/enterprise/modern-auth-for-office-2013-and-2016) , co nejrychleji. Pokud máte v rámci stejného tokenu již někteří uživatelé používají moderní ověřování, ale ostatní, kteří stále používají starší ověřování, je třeba provést následující kroky a uzamknout starší verze ověřování klientů:

1. Použití [sestav aktivit přihlašování](../reports-monitoring/concept-sign-ins.md) k identifikaci uživatelů, kteří stále používají starší verze ověřování a nápravu plánu:

   a. Upgradujte na klienty podporující moderní ověřování na ovlivněné uživatele.
   
   b. Naplánujte časový rámec přímou migraci, který se má uzamknout podle následujících kroků.
   
   c. Určete, které starší verze aplikací mají zastaralou závislost na starších verzích ověřování. Viz krok 3 níže.

2. Zakáže starší protokoly ve zdroji (například poštovní schránka Exchange) pro uživatele, kteří nepoužívají starší ověřování, aby nedocházelo k větší expozici.
3. U zbývajících účtů (v ideálním případě nelidské identity, jako jsou účty služeb) použijte [podmíněný přístup k omezení starších protokolů](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Conditional-Access-support-for-blocking-legacy-auth-is/ba-p/245417) po ověření.

#### <a name="legacy-authentication-recommended-reading"></a>Doporučené čtení starší verze ověřování

- [Povolení nebo zakázání přístupu k poštovním schránkám v systému Exchange Server prostřednictvím protokolu POP3 nebo IMAP4](/exchange/clients/pop3-and-imap4/configure-mailbox-access)

### <a name="consent-grants"></a>Udělení souhlasu

Při útoku na nedovolené udělení souhlasu útočník vytvoří aplikaci registrovanou v Azure AD, která požaduje přístup k datům, jako jsou kontaktní údaje, e-mail nebo dokumenty. Uživatelé můžou při vykládku na škodlivé weby udělit souhlas se škodlivými aplikacemi prostřednictvím útoků phishing.

Níže najdete seznam aplikací s oprávněními, které byste mohli chtít pořizovat pro cloudové služby Microsoftu:

- Aplikace s aplikací nebo delegované \* Oprávnění pro čtení z
- Aplikace s delegovanými oprávněními můžou jménem uživatele číst, posílat nebo spravovat e-maily.
- Aplikace, kterým je uděleno oprávnění s následujícími oprávněními:

| Prostředek | Oprávnění |
| :- | :- |
| Exchange Online | EA. AccessAsUser. All |
| | EWS. AccessAsUser. All |
| | Pošta. čtení |
| Microsoft Graph API | Pošta. čtení |
| | Pošta. Read. Shared |
| | Pošta. v zápisu |

- Aplikace udělily úplnému zosobnění uživatele přihlášeného uživatele. Například:

|Prostředek | Oprávnění |
| :- | :- |
| Microsoft Graph API| Directory. AccessAsUser. All |
| Azure REST API | user_impersonation |

Chcete-li se tomuto scénáři vyhnout, měli byste se podívat, jak zjistit a opravit [nedovolené granty souhlasu v sadě Office 365](/office365/securitycompliance/detect-and-remediate-illicit-consent-grants) k identifikaci a opravě aplikací s nedovolenými granty nebo aplikacemi, které mají více grantů, než je nutné. Dále [odeberte samoobslužné služby zcela](../manage-apps/configure-user-consent.md) a [vytvořte postupy zásad správného řízení](../manage-apps/configure-admin-consent-workflow.md). Nakonec Naplánujte pravidelné kontroly oprávnění aplikací a odeberte je, když je nepotřebujete.

#### <a name="consent-grants-recommended-reading"></a>Souhlas udělí doporučené čtení

- [Microsoft Graph oprávnění API](/graph/permissions-reference)

### <a name="user-and-group-settings"></a>Nastavení uživatelů a skupin

Níže jsou uvedena nastavení uživatelů a skupin, která se můžou uzamknout, pokud neexistují explicitní obchodní potřeby:

#### <a name="user-settings"></a>Uživatelská nastavení

- **Externí uživatelé** – externí spolupráce se může probíhat v rámci podniku pomocí služeb, jako jsou týmy, Power BI, SharePoint Online a Azure Information Protection. Pokud máte explicitní omezení pro řízení externí spolupráce iniciované uživatelem, doporučuje se povolit externím uživatelům použití [správy nároků Azure AD](../governance/entitlement-management-overview.md) nebo řízené operace, jako je například služba technické podpory. Pokud nechcete pro služby povolit organickou externí spolupráci, můžete [zablokovat členům možnost úplného pozvání externích uživatelů](../external-identities/delegate-invitations.md). Alternativně můžete v pozvání externích uživatelů taky v případě [Povolení nebo blokování jednotlivých domén](../external-identities/allow-deny-list.md) .
- **Registrace aplikací** – Pokud jsou povolené registrace aplikací, koncoví uživatelé mohou začlenit vlastní aplikace a udělit přístup ke svým datům. Typickým příkladem registrace aplikace jsou uživatelé, kteří povolují moduly plug-in Outlooku nebo hlasové asistenty, jako je Alexa a Siri, ke čtení e-mailů a kalendářů nebo posílání e-mailů jménem. Pokud se zákazník rozhodne vypnout registraci aplikace, musí být týmy InfoSec a IAM zapojené do správy výjimek (registrace aplikací, které jsou potřeba na základě obchodních požadavků), protože by museli zaregistrovat aplikace s účtem správce a pravděpodobně vyžadují návrh procesu zprovoznění procesu.
- **Portál pro správu** – organizace můžou uzamknout okno Azure ad v Azure Portal tak, aby nesprávci nezískali přístup ke správě služby Azure ad v Azure Portal a nemuseli se zaměňovat. Přejděte na uživatelské nastavení na portálu pro správu Azure AD, abyste omezili přístup:

![Omezený přístup portálu pro správu](./media/active-directory-ops-guide/active-directory-ops-img13.png)

> [!NOTE]
> Bez správců stále přístup k rozhraním pro správu služby Azure AD prostřednictvím příkazového řádku a dalších programových rozhraní.

#### <a name="group-settings"></a>Nastavení skupiny

**Samoobslužná správa skupin/uživatelé můžou vytvářet skupiny zabezpečení/Microsoft 365 skupiny.** Pokud není k dispozici žádná stávající samoobslužná iniciativa pro skupiny v cloudu, zákazníci se můžou rozhodnout je vypnout, dokud nebudou připravené k použití této možnosti.

#### <a name="groups-recommended-reading"></a>Skupiny – Doporučené čtení

- [Co je spolupráce B2B v Azure Active Directory?](../external-identities/what-is-b2b.md)
- [Integrace aplikací s Azure Active Directory](../develop/quickstart-register-app.md)
- [Aplikace, oprávnění a souhlas v Azure Active Directory.](../develop/quickstart-register-app.md)
- [Použití skupin pro správu přístupu k prostředkům v Azure Active Directory](./active-directory-manage-groups.md)
- [Nastavení samoobslužné správy přístupu k aplikacím ve službě Azure Active Directory](../enterprise-users/groups-self-service-management.md)

### <a name="traffic-from-unexpected-locations"></a>Provoz z neočekávaných umístění

Útočníci pocházejí z různých částí světa. Toto riziko spravujte pomocí zásad podmíněného přístupu s umístěním jako podmínku. [Podmínka umístění](../conditional-access/location-condition.md) zásad podmíněného přístupu umožňuje blokovat přístup k umístěním, ze kterých není k dispozici žádný obchodní důvod pro přihlášení.

![Vytvořit nové pojmenované umístění](./media/active-directory-ops-guide/active-directory-ops-img14.png)

Pokud je k dispozici, použijte řešení SIEM (Security Information and Event Management) k analýze a hledání vzorů přístupu v různých oblastech. Pokud nepoužíváte produkt SIEM nebo nepoužíváte informace o ověřování ze služby Azure AD, doporučujeme použít [Azure monitor](../../azure-monitor/overview.md) k identifikaci vzorů přístupu napříč oblastmi.

## <a name="access-usage"></a>Využití přístupu

### <a name="azure-ad-logs-archived-and-integrated-with-incident-response-plans"></a>Archivace a integrace protokolů služby Azure AD s plány reakcí na incidenty

Přístup k aktivitě přihlašování, auditům a rizikovým událostem pro Azure AD je zásadní pro řešení potíží, analýzu využití a Forenzní vyšetřování. Azure AD poskytuje přístup k těmto zdrojům přes rozhraní REST API, která mají omezené období uchovávání. K dlouhodobému ukládání auditů a podpoře je důležité, aby systém SIEM (Security Information and Event Management) nebo ekvivalentní archivační technologie. Pokud chcete povolit dlouhodobé ukládání protokolů služby Azure AD, musíte je přidat do stávajícího řešení SIEM nebo použít [Azure monitor](../reports-monitoring/concept-activity-logs-azure-monitor.md). Archivace protokolů, které lze použít jako součást plánů reakce na incidenty a vyšetřování.

#### <a name="logs-recommended-reading"></a>Protokoly doporučené čtení

- [Referenční informace k rozhraní API pro audit Azure Active Directory](/graph/api/resources/directoryaudit?view=graph-rest-beta)
- [Referenční informace k rozhraní API sestav aktivity přihlašování Azure Active Directory](/graph/api/resources/signin?view=graph-rest-beta)
- [Získání dat pomocí rozhraní API pro generování sestav Azure AD s certifikáty](../reports-monitoring/tutorial-access-api-with-certificates.md)
- [Microsoft Graph pro Azure Active Directory Identity Protection](../identity-protection/howto-identity-protection-graph-api.md)
- [Reference k rozhraní API aktivity správy Office 365](/office/office-365-management-api/office-365-management-activity-api-reference)
- [Jak používat balíček obsahu Azure Active Directory Power BI Content Pack](../reports-monitoring/howto-use-azure-monitor-workbooks.md)

## <a name="summary"></a>Souhrn

Zabezpečená infrastruktura identity má 12 aspektů. Tento seznam vám pomůže lépe zabezpečit a spravovat přihlašovací údaje, definovat možnosti ověřování, přiřazení delegáta, využití měr a definovat zásady přístupu na základě podnikového zabezpečení stav.

- Přiřaďte vlastníky ke klíčovým úlohám.
- Implementujte řešení pro detekci slabých nebo nevrácených hesel, zvyšte správu hesel a ochranu a dále Zabezpečte přístup uživatelů k prostředkům.
- Spravujte identitu zařízení a Chraňte svoje prostředky kdykoli a z libovolného místa.
- Implementujte ověřování neheslem.
- Poskytněte standardizované mechanismy jednotného přihlašování napříč organizací.
- Migrujte aplikace z AD FS do služby Azure AD, abyste umožnili lepší zabezpečení a jednotnější možnosti správy.
- Přiřaďte uživatele k aplikacím pomocí skupin, čímž umožníte větší flexibilitu a možnost správy ve velkém měřítku.
- Nakonfigurujte zásady přístupu na základě rizika.
- Uzamčení starších protokolů ověřování.
- Zjišťování a náprava nedovolených grantů souhlasu
- Zamknout nastavení uživatelů a skupin
- Povolte dlouhodobé ukládání protokolů služby Azure AD pro řešení potíží, analýzy využití a Forenzní vyšetřování.

## <a name="next-steps"></a>Další kroky

Začněte s [provozními kontrolami a akcemi zásad správného řízení identity](active-directory-ops-guide-govern.md).
