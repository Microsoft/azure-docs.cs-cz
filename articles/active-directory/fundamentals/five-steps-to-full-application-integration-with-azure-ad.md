---
title: Pět kroků pro integraci všech vašich aplikací se službou Azure AD
description: Tato příručka vysvětluje, jak integrovat všechny aplikace do Azure AD. V každém kroku vyvysvětlíme hodnotu a získáte odkazy na zdroje, které budou vysvětlit technické podrobnosti.
services: active-directory
author: knicholasa
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 08/05/2020
ms.author: nichola
ms.openlocfilehash: 7fd9e504448d55b4a2ef8c10b4ba1176cb2e3402
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96172631"
---
# <a name="five-steps-for-integrating-all-your-apps-with-azure-ad"></a>Pět kroků pro integraci všech vašich aplikací se službou Azure AD

Azure Active Directory (Azure AD) je cloudová služba pro správu identit a přístupu založená na Microsoftu. Azure AD poskytuje zabezpečená řešení pro ověřování a autorizaci, aby mohli zákazníci, partneři a zaměstnanci přistupovat k aplikacím, které potřebují. Pomocí služby Azure AD, [podmíněného přístupu](../conditional-access/overview.md), [vícefaktorového ověřování](../authentication/concept-mfa-howitworks.md), [jednotného přihlašování](../hybrid/how-to-connect-sso.md)a [automatického zřizování uživatelů](../app-provisioning/user-provisioning.md) usnadňuje a zabezpečuje správu identit a přístupu.

Pokud má vaše společnost předplatné Microsoft 365, pravděpodobně již službu Azure AD [používáte](/office365/enterprise/about-office-365-identity) . Službu Azure AD je ale možné použít pro všechny vaše aplikace a [centralizaci správy aplikací](../manage-apps/common-scenarios.md) můžete využívat stejné funkce, nástroje a zásady správy identit napříč celým portfoliem aplikace. Tím zajistíte sjednocené řešení, které zlepšuje zabezpečení, snižuje náklady, zvýší produktivitu a umožní vám zajistit dodržování předpisů. Pak získáte vzdálený přístup k místním aplikacím.

Tato příručka vysvětluje, jak integrovat všechny aplikace do Azure AD. V každém kroku vyvysvětlíme hodnotu a získáte odkazy na zdroje, které budou vysvětlit technické podrobnosti. Tyto kroky prezentujeme v pořadí, které doporučujeme. Můžete ale přejít na libovolnou část procesu, abyste mohli začít pracovat s tím, co pro vás přináší nejvíc hodnot.

Další materiály k tomuto tématu, včetně podrobných dokumentů White Paper v obchodních procesech, najdete v našich [materiálech k migraci aplikací na Azure Active Directory](../manage-apps/migration-resources.md) stránku.

## <a name="1-use-azure-ad-for-new-applications"></a>1. použití služby Azure AD pro nové aplikace

Nejprve se zaměřte na nově načtené aplikace. Když vaše firma začne používat novou aplikaci, [přidejte ji do svého tenanta Azure AD](../manage-apps/add-application-portal.md) hned. Nastavte zásady společnosti tak, aby bylo možné přidat nové aplikace do služby Azure AD jako standardní postup ve vaší organizaci. To má minimální dopad na stávající obchodní procesy a umožňuje prozkoumat a prokázat hodnotu, kterou získáte při integraci aplikací, aniž byste museli měnit způsob, jakým uživatelé obchodují ve vašem prostředí ještě dnes.

Azure Active Directory (Azure AD) obsahuje galerii obsahující tisíce předem integrovaných aplikací, které usnadňují začátek práce. Můžete [Přidat aplikaci Galerie do vaší organizace Azure AD](../manage-apps/add-application-portal.md) s podrobnými [kurzy](../saas-apps/tutorial-list.md) pro integraci s oblíbenými aplikacemi, jako jsou:

- [ServiceNow](../saas-apps/servicenow-tutorial.md)
- [Workday](../saas-apps/workday-tutorial.md)
- [Salesforce](../saas-apps/salesforce-tutorial.md)
- [AWS](../saas-apps/amazon-web-service-tutorial.md)
- [Slack](../saas-apps/slack-tutorial.md)

Kromě toho můžete [integrovat aplikace, které nejsou v galerii](../manage-apps/view-applications-portal.md), včetně jakékoli aplikace, která již existuje ve vaší organizaci, nebo jakékoli aplikace třetí strany od dodavatele, který ještě není součástí Galerie Azure AD. [Aplikaci můžete do galerie přidat](../develop/v2-howto-app-gallery-listing.md) také v případě, že tam není.

Nakonec můžete integrovat i aplikace, které vyvíjíte interně. Tento postup je popsaný v kroku 5 tohoto průvodce.

## <a name="2-determine-existing-application-usage-and-prioritize-work"></a>2. určení používání stávajících aplikací a stanovení priorit práce

V dalším kroku zjistíte, že se zaměstnanci aplikace často používají, a určete prioritu práce při jejich integraci s Azure AD.

Můžete začít pomocí [nástrojů Cloud discovery](/cloud-app-security/tutorial-shadow-it) Microsoft Cloud App Security pro zjišťování a správu "stínové" sítě v síti (tj. aplikace, které nespravuje oddělení IT). [Pomocí rozšířené ochrany před internetovými útoky v programu Microsoft Defender](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) můžete zjednodušit a rozšířit proces zjišťování.

Kromě toho můžete pomocí [sestavy aktivita aplikace AD FS](../manage-apps/migrate-adfs-application-activity.md) v Azure Portal zjistit všechny AD FS aplikace ve vaší organizaci, počet jedinečných uživatelů, kteří se k nim přihlásili, a kompatibilitu při jejich integraci s Azure AD.

Po zjištění stávající krajiny budete chtít [vytvořit plán a nastavit](../manage-apps/migration-resources.md) prioritu aplikací s nejvyšší prioritou k integraci. V některých ukázkových dotazech se můžete zeptat na tento proces:

- Které aplikace se používají nejčastěji?
- Které jsou nejrizikovějších?
- Které aplikace budou v budoucnu vyřazené, takže se přesun nepotřebuje?
- Které aplikace musí zůstat v místním prostředí a nelze je přesunout do cloudu?

Až budou všechny vaše aplikace integrované a už nebudete spoléhat na více řešení identity, uvidíte největší výhody a úspory nákladů. Při přesunu stupňovaný k tomuto cíli ale budete mít snazší správu identit a zvýšené zabezpečení. Tuto dobu chcete použít k určení priorit práce a rozhodování, co je vhodné pro vaši situaci.

## <a name="3-integrate-apps-that-rely-on-other-identity-providers"></a>3. Integrujte aplikace, které spoléhají na jiné zprostředkovatele identity.

Během procesu zjišťování jste možná našli aplikace, které nesleduje IT oddělení, což ponechá vaše data a prostředky v ohrožení. Můžete mít i aplikace, které používají alternativní řešení identit, včetně Active Directory Federation Services (AD FS) (ADFS) nebo jiných zprostředkovatelů identity. Vezměte v úvahu, jak můžete konsolidovat správu identit a přístupu, abyste ušetřili peníze a zvýšili zabezpečení. Snižte počet řešení identity, která budete mít:

- Ušetříte si peníze tím, že Eliminujte nutnost místního zřizování a ověřování uživatelů a také licenční poplatky placené jiným poskytovatelům cloudových identit pro stejnou službu.
- Snižte režijní náklady na správu a zapněte užší zabezpečení s menším počtem redundancí v procesu správy identit a přístupu.
- Umožněte zaměstnancům zabezpečený přístup pro jednotné přihlašování ke všem aplikacím, které potřebují prostřednictvím [portálu Mojeapl](../manage-apps/access-panel-collections.md).
- Zvyšte si informace o službách souvisejících s [ochranou identity](../identity-protection/overview-identity-protection.md) v Azure AD, jako je podmíněný přístup, tím, že zvýšíte množství dat, která získá z využití vaší aplikace, a rozšíříte své výhody na nově přidané aplikace.

Publikovali jsme doprovodné materiály pro správu podnikových procesů integrování aplikací se službou Azure AD, včetně [plakátu](https://aka.ms/AppOnePager) a [prezentace](https://aka.ms/AppGuideline) , pomocí kterých mohou vlastníci podniku a aplikace dělat a zajímat. Tyto ukázky můžete upravit pomocí vlastního brandingu a publikovat je ve vaší organizaci prostřednictvím portálu společnosti, bulletinu nebo jiného média, jak budete postupovat na dokončení tohoto procesu.

Dobrým místem, kde začít, je vyhodnocování používání Active Directory Federation Services (AD FS) (ADFS). Řada organizací používá službu ADFS k ověřování pomocí aplikací SaaS, vlastních obchodních aplikací a Microsoft 365 a aplikací založených na službě Azure AD:

![Diagram zobrazuje místní aplikace, obchodní aplikace, aplikace SaaS a prostřednictvím služby Azure AD 365, které se připojují s tečkovanými řádky do Active Directory a AD FS.](\media\five-steps-to-full-application-integration-with-azure-ad\adfs-integration-1.png)

Tuto konfiguraci můžete upgradovat tak, že v [Azure AD nahradíte službu AD FS jako střed](../manage-apps/migrate-adfs-apps-to-azure.md) svého řešení pro správu identit. Díky tomu je možné přihlašovat se ke každé aplikaci, ke které mají vaši zaměstnanci přístup, a usnadnit tak zaměstnancům, aby si našli jakékoli obchodní aplikace, které potřebují prostřednictvím [portálu Mojeapl](../user-help/my-apps-portal-end-user-access.md), a navíc k ostatním výhodám uvedeným výše.

![Diagram zobrazuje místní aplikace přes službu Active Directory a AD FS, obchodní aplikace, aplikace SaaS a sadu Office 365, které se připojují s tečkovanými čarami do Azure Active Directory.](\media\five-steps-to-full-application-integration-with-azure-ad\adfs-integration-2.png)

Jakmile se služba Azure AD stala centrálním poskytovatelem identity, možná bude možné přepínat ze služby AD FS zcela, ale nepoužívat federované řešení. Aplikace, které dřív používaly ADFS pro ověřování, můžou teď používat jenom Azure AD.

![Diagram zobrazuje místní, obchodní aplikace, aplikace SaaS a Office 365, které se připojují s tečkami na Azure Active Directory. Služba Active Directory a AD FS není k dispozici.](\media\five-steps-to-full-application-integration-with-azure-ad\adfs-integration-3.png)

V Azure AD můžete také migrovat aplikace, které používají jiného cloudového poskytovatele identity. Vaše organizace může mít několik řešení pro správu přístupu k identitě (IAM). Migrace na jednu infrastrukturu Azure AD je příležitostí k omezení závislostí na licencích IAM (v místním prostředí nebo v cloudu) a na náklady na infrastrukturu. V případech, kdy jste už mohli platit za Azure AD prostřednictvím licencí M365, neexistuje žádný důvod, proč platíte přidané náklady na jiné řešení IAM.

## <a name="4-integrate-on-premises-applications"></a>4. Integrace místních aplikací

Tradičně byly aplikace zabezpečeny tím, že umožňují přístup pouze při připojení k podnikové síti. Ve stále propojeném světě chceme mít přístup k aplikacím pro zákazníky, partnery a zaměstnance, a to bez ohledu na to, kde jsou na světě. [Azure proxy aplikací služby AD](../manage-apps/what-is-application-proxy.md) (AppProxy) je funkce služby Azure AD, která připojuje vaše stávající místní aplikace do služby Azure AD a nevyžaduje, abyste zachovali hraniční servery nebo další další infrastrukturu.

![Diagram zobrazuje službu proxy aplikací v akci. Uživatel má přístup https://sales.contoso.com a jeho žádost se přesměruje prostřednictvím " https://sales-contoso.msappproxy.net " v Azure Active Directory na místní adresu " http://sales ".](./media/five-steps-to-full-application-integration-with-azure-ad\app-proxy.png)

Můžete použít [kurz: Přidání místní aplikace pro vzdálený přístup prostřednictvím proxy aplikace v Azure Active Directory](../manage-apps/application-proxy-add-on-premises-application.md) pro povolení proxy aplikací a Přidání místní aplikace do tenanta služby Azure AD.

Kromě toho můžete integrovat řadiče pro doručování aplikací, jako je F5 Big-IP APM nebo Zscaler Private Access. Integrací těchto funkcí se službou Azure AD získáte moderní ověřování a správu identit služby Azure AD společně se správou provozu a funkcemi zabezpečení partnerského produktu. Toto řešení zavoláme [zabezpečeným hybridním přístupem](../manage-apps/secure-hybrid-access.md). Pokud dnes použijete některou z následujících služeb, máme k dispozici kurzy, které vás provedou integrací se službou Azure AD.

- [Přístup k podnikovým aplikacím Akamai (EAA)](../saas-apps/akamai-tutorial.md)
- [Řadič pro doručování aplikací pro Citrix (ADC)](../saas-apps/citrix-netscaler-tutorial.md) (dříve označovaný jako Citrix NetScaler)
- [S funkcí F5 Big-IP APM](../saas-apps/headerf5-tutorial.md)
- [Zscaler Private Access (ZPA)](../saas-apps/zscalerprivateaccess-tutorial.md)

## <a name="5-integrate-apps-your-developers-build"></a>5. Integrace aplikací, které vaše vývojáři sestaví

Pro aplikace, které jsou vytvořené v rámci vaší společnosti, můžou vývojáři implementovat ověřování a autorizaci pomocí [platformy Microsoft Identity](../develop/index.yml) . Aplikace integrované s platformou se [registrují ve službě Azure AD](../develop/quickstart-register-app.md) a spravují se stejně jako jakékoli jiné aplikace v portfoliu.

Vývojáři můžou používat platformu pro aplikace interního použití i pro aplikace pro zákazníky a existují i další výhody, které se s využitím platformy dodávají. [Knihovny Microsoft Authentication Library (MSAL)](../develop/msal-overview.md), které jsou součástí platformy, umožňují vývojářům povolit moderní prostředí, jako je vícefaktorové ověřování, a použití klíčů zabezpečení pro přístup ke svým aplikacím, aniž by je museli implementovat sami sebe. Kromě toho můžou aplikace integrované s platformou Microsoft Identity získat přístup k [Microsoft Graph](../develop/microsoft-graph-intro.md) – jedná se o sjednocený koncový bod rozhraní API, který poskytuje Microsoft 365 data, která popisují vzory produktivity, identity a zabezpečení v organizaci. Vývojáři můžou tyto informace využít k implementaci funkcí, které zvyšují produktivitu pro vaše uživatele. Například určením osob, se kterými uživatel interaktivně pracoval, a zpřístupnění je v uživatelském rozhraní aplikace.

Máme [video řady](https://www.youtube.com/watch?v=zjezqZPPOfc&amp;list=PLLasX02E8BPBxGouWlJV-u-XZWOc2RkiX) , které poskytují ucelený Úvod k platformě a také [mnoho ukázek kódu](../develop/sample-v2-code.md) v podporovaných jazycích a platformách.

## <a name="next-steps"></a>Další kroky

- [Zdroje informací o migraci aplikací do Azure Active Directory](../manage-apps/migration-resources.md)