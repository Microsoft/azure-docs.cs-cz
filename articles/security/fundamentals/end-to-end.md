---
title: Komplexní zabezpečení v Azure | Microsoft Docs
description: Tento článek poskytuje mapu služeb Azure, které vám pomůžou zabezpečit a chránit cloudové prostředky a zjišťovat a zkoumat hrozby.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
ms.assetid: a5a7f60a-97e2-49b4-a8c5-7c010ff27ef8
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 4/07/2021
ms.author: terrylan
ms.openlocfilehash: 3ea3c2bcb878dbd8a712e6076dda09853f55e297
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2021
ms.locfileid: "107310338"
---
# <a name="end-to-end-security-in-azure"></a>Komplexní zabezpečení v Azure
Jedním z nejlepších důvodů použití Azure pro vaše aplikace a služby je využití nejrůznějších nástrojů a funkcí zabezpečení. Tyto nástroje a možnosti usnadňují vytváření zabezpečených řešení na zabezpečené platformě Azure. Microsoft Azure poskytuje důvěrnost, integritu a dostupnost zákaznických dat a zároveň umožňuje transparentní zodpovědnost.

Následující diagram a dokumentace vás seznámí se službami zabezpečení v Azure. Tyto služby zabezpečení vám pomůžou plnit požadavky na zabezpečení vaší firmy a chránit své uživatele, zařízení, prostředky, data a aplikace v cloudu.

## <a name="microsoft-security-services-map"></a>Mapa služby Microsoft Security Services

Mapa služeb zabezpečení uspořádá služby podle prostředků, které chrání (sloupec). Diagram také seskupuje služby do následujících kategorií (řádek):

- Zabezpečení a ochrana – služby, které umožňují implementovat hloubkovou a obrannou strategii napříč identitami, hostiteli, sítěmi a daty. Tato kolekce služeb zabezpečení a možností nabízí způsob, jak pochopit a zdokonalit stav zabezpečení napříč prostředím Azure.
- Detekuje hrozby – služby, které identifikují podezřelé aktivity a pomáhají zmírnit hrozby.
- Prozkoumejte a odpovězte – služby, které vyžádají data protokolování, abyste mohli vyhodnotit podezřelou aktivitu a reagovat na ně.

Diagram obsahuje program Azure Security test, což je kolekce vysoce ovlivňujících doporučení pro zabezpečení, která můžete použít k zabezpečení služeb, které používáte v Azure.

:::image type="content" source="media/end-to-end/security-diagram.svg" alt-text="Diagram znázorňující ucelené služby zabezpečení v Azure." border="false":::

## <a name="security-controls-and-baselines"></a>Řídicí prvky zabezpečení a směrné plány
Program [Azure Security test](../benchmarks/introduction.md) zahrnuje kolekci vysoce ovlivňujících doporučení pro zabezpečení, která můžete použít k zajištění služeb, které používáte v Azure:

- Řízení zabezpečení – tato doporučení se obecně vztahují na vaše klienty Azure a služby Azure. Každé doporučení identifikuje seznam zúčastněných stran, které jsou obvykle součástí plánování, schválení nebo implementace srovnávacího testu.
- Směrné plány služeb – tyto ovládací prvky se vztahují na jednotlivé služby Azure a poskytují doporučení k této konfiguraci zabezpečení této služby.

## <a name="secure-and-protect"></a>Zabezpečení a ochrana

:::image type="content" source="media/end-to-end/secure-and-protect.svg" alt-text="Diagram znázorňující služby Azure, které vám pomůžou zabezpečit a chránit vaše cloudové prostředky." border="false":::

| Služba | Popis |
|------|--------|
| [Azure Security Center](../../security-center/security-center-introduction.md)| Jednotný systém pro správu zabezpečení infrastruktury, který posílí stav zabezpečení vašich datových center a poskytuje rozšířenou ochranu před hrozbami napříč vašimi hybridními úlohami v cloudu – ať už jsou v Azure, nebo ne, i místně. |
| **&nbsp; & &nbsp; Správa přístupu k identitám &nbsp;** | |
| [Azure Active Directory (AD)](../../active-directory/fundamentals/active-directory-whatis.md)| Cloudová služba pro správu identit a přístupu od Microsoftu.  |
|  | [Podmíněný přístup](../../active-directory/conditional-access/overview.md) je nástroj používaný službou Azure AD k spojování signálů identity společně, k rozhodování a prosazování zásad organizace. |
|  | [Domain Services](../../active-directory-domain-services/overview.md) je nástroj používaný službou Azure AD k poskytování spravovaných doménových služeb, jako je připojení k doméně, zásady skupiny, LDAP (Lightweight Directory Access Protocol) a ověřování pomocí protokolu Kerberos/NTLM. |
|  | [Privileged Identity Management (PIM)](../../active-directory/privileged-identity-management/pim-configure.md) je služba ve službě Azure AD, která umožňuje spravovat, řídit a monitorovat přístup k důležitým prostředkům ve vaší organizaci. |
|  | [Multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md) je nástroj používaný službou Azure AD, který umožňuje chránit přístup k datům a aplikacím tím, že vyžaduje druhou formu ověřování. |
| [Azure AD Identity Protection](../../active-directory/identity-protection/overview-identity-protection.md) | Nástroj, který organizacím umožňuje automatizovat detekci a nápravu rizik na základě identity, prozkoumat rizika pomocí dat na portálu a exportovat data o detekci rizik do nástrojů jiných výrobců k další analýze. |
| **&nbsp; & &nbsp; Síť infrastruktury** |  |
| [VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md) | Brána virtuální sítě, která se používá k posílání šifrovaného provozu mezi virtuální sítí Azure a místním umístěním přes veřejný Internet a k posílání šifrovaného provozu mezi virtuálními sítěmi Azure přes síť Microsoftu. |
| [Azure DDoS Protection Standard](../../ddos-protection/ddos-protection-overview.md) | Poskytuje vylepšené funkce zmírnění DDoS proti útokům DDoS. Automaticky se vyladěna tak, aby chránila vaše konkrétní prostředky Azure ve virtuální síti. |
| [Azure Front Door](../../frontdoor/front-door-overview.md) | Globální, škálovatelný vstupní bod, který využívá síť Microsoft Global Edge k vytváření rychlých, zabezpečených a široce škálovatelných webových aplikací. |
| [Azure Firewall](../../firewall/overview.md) | Spravovaná cloudová služba zabezpečení sítě, která chrání vaše prostředky Azure Virtual Network. Jedná se o plně stavovou bránu firewall jako službu s integrovanou vysokou dostupností a neomezenou škálovatelností v cloudu. |
| [Azure Key Vault](../../key-vault/general/overview.md) | Zabezpečené úložiště tajných kódů pro tokeny, hesla, certifikáty, klíče rozhraní API a další tajné kódy. Key Vault můžete použít také k vytvoření a řízení šifrovacích klíčů používaných k šifrování vašich dat. |
| [Key Vault spravovaný HSM (Preview)](../../key-vault/managed-hsm/overview.md) | Plně spravovaná cloudová služba vyhovující standardům pro jednoho tenanta, která umožňuje chránit kryptografické klíče pro vaše cloudové aplikace pomocí ověřené HSM úrovně 3 standardu FIPS 140-2. |
| [Azure Private Link](../../private-link/private-link-overview.md) | Umožňuje přístup ke službám Azure PaaS (například Azure Storage a SQL Database) a hostovaným/partnerským službám v Azure, které jsou ve vaší virtuální síti privátním koncovým bodem. |
| [Azure Application Gateway](../../application-gateway/overview.md) | Pokročilý nástroj pro vyrovnávání zatížení webového provozu, který umožňuje spravovat provoz do webových aplikací. Application Gateway mohou provádět rozhodování o směrování na základě dalších atributů požadavku HTTP, například cesty k identifikátoru URI nebo hlaviček hostitele. |
| [Azure Service Bus](../../service-bus-messaging/service-bus-messaging-overview.md) | Plně spravovaný zprostředkovatel podnikových zpráv s frontami zpráv a tématy pro publikování a odběr. Service Bus slouží k vzájemnému odvýšení aplikací a služeb. |
| [Firewall webových aplikací](../../web-application-firewall/overview.md) | Poskytuje centralizovanou ochranu webových aplikací před běžnými zneužitím a ohrožením zabezpečení. WAF se dají nasadit s využitím Azure Application Gateway a služby Azure front-dveří. |
| **Aplikace & dat** |  |
| [Azure Backup](../../backup/backup-overview.md) | Poskytuje jednoduchá, bezpečná a nákladově efektivní řešení pro zálohování dat a obnovení z Microsoft Azureho cloudu. |
| [Šifrování služby Azure Storage](../../storage/common/storage-service-encryption.md) | Automaticky šifruje data před uložením a automaticky data dešifruje při jejich načítání. |
| [Azure Information Protection](https://docs.microsoft.com/azure/information-protection/what-is-information-protection) | Cloudové řešení, které organizacím umožňuje zjišťovat, klasifikovat a chránit dokumenty a e-maily prostřednictvím použití popisků na obsah. |
| [API Management](../../api-management/api-management-key-concepts.md) | Způsob vytváření konzistentních a moderních bran rozhraní API pro stávající back-endové služby. |
| [Důvěrné výpočetní operace Azure](../../confidential-computing/overview.md) | Umožňuje izolovat citlivá data při zpracování v cloudu. |
| [Azure DevOps](https://docs.microsoft.com/azure/devops/user-guide/what-is-azure-devops) | Vývojové projekty využívají více vrstev technologie zabezpečení a zásad správného řízení, provozní postupy a zásady dodržování předpisů, pokud jsou uložené v Azure DevOps. |
| **Přístup zákazníků** |  |
| [Externí identity Azure AD](../../active-directory/external-identities/compare-with-b2c.md) | S externími identitami v Azure AD můžete uživatelům mimo vaši organizaci umožnit přístup k vašim aplikacím a prostředkům a zároveň jim umožnit přihlašovat se pomocí libovolné identity, které preferuje. |
|  | Své aplikace a prostředky můžete sdílet s externími uživateli prostřednictvím spolupráce [B2B v Azure AD](../../active-directory/external-identities/what-is-b2b.md) . |
|  | [Azure AD B2C](../../active-directory-b2c/overview.md) vám umožní podporovat miliony uživatelů a miliard ověřování za den, sledovat a automaticky zpracovávat hrozby, jako je například odepření služby, postřik hesel nebo útoky hrubou silou. |

## <a name="detect-threats"></a>Detekce hrozeb

:::image type="content" source="media/end-to-end/detect-threats.svg" alt-text="Diagram znázorňující služby Azure, které zjišťují hrozby." border="false":::

| Služba | Popis |
|------|--------|
| [Azure Defender](../../security-center/azure-defender.md) | Přináší pokročilou, inteligentní a chráněnou službu Azure a hybridní prostředky a úlohy. Řídicí panel Azure Defenderu v Security Center poskytuje přehled a kontrolu nad funkcemi ochrany úloh cloudu pro vaše prostředí. |
| [Azure Sentinel](../../sentinel/overview.md) | Škálovatelné řešení pro správu událostí v cloudu, SIEM (Security Information Management) a automatizované odpovědi pro orchestraci zabezpečení (společnosti). Sentinel poskytuje inteligentní analýzu zabezpečení a analýzu hrozeb napříč podnikem a poskytuje jedno řešení pro detekci výstrah, viditelnost hrozeb, proaktivní lov a reakci na hrozby. |
| **&nbsp; & &nbsp; Správa přístupu k identitám &nbsp;** |  |
| [Microsoft 365 Defender](https://docs.microsoft.com/microsoft-365/security/defender/microsoft-365-defender) | Jednotná a následná porušování sady Enterprise obrany, která nativně koordinuje detekci, prevenci, šetření a reakci napříč koncovými body, identitami, e-maily a aplikacemi, aby poskytovaly integrovanou ochranu proti sofistikovaným útokům. |
|  | [Microsoft Defender pro koncové body](https://docs.microsoft.com/microsoft-365/security/defender-endpoint/microsoft-defender-endpoint.md) je podniková platforma pro zabezpečení koncového bodu, která pomáhá podnikovým sítím zabránit, zjišťovat, zkoumat a reagovat na pokročilé hrozby. |
|  | [Microsoft Defender pro identitu](https://docs.microsoft.com/defender-for-identity/what-is) je cloudové řešení zabezpečení, které využívá vaše místní signály služby Active Directory k identifikaci, detekci a prověření pokročilých hrozeb, ohrožených identit a škodlivých akcí Insider směrovaných ve vaší organizaci. |
| [Azure AD Identity Protection](../../active-directory/identity-protection/howto-identity-protection-configure-notifications.md) | Odesílá dva typy automatických e-mailů s oznámením, které vám pomůžou se správou rizik a detekcí rizik uživatelů: uživatelé při riziku zjistili, že e-mail a týdenní e-mail |
| **Infrastruktura & sítě** |  |
| [Azure Defender for IoT](../../defender-for-iot/overview.md) | Jednotné řešení zabezpečení pro identifikaci zařízení, ohrožení zabezpečení a hrozby IoT/OT. Umožňuje zabezpečit celé prostředí IoT/OT, bez ohledu na to, jestli potřebujete chránit existující zařízení IoT/OT, nebo můžete vytvořit zabezpečení pro nové inovace IoT. |
| [Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) | Poskytuje nástroje pro monitorování, diagnostiku, zobrazení metrik a povolení nebo zakázání protokolů pro prostředky ve službě Azure Virtual Network. Network Watcher je navržený tak, aby sledoval a opravil stav sítě produktů IaaS, které zahrnují virtuální počítače, virtuální sítě, brány Application Gateway a nástroje pro vyrovnávání zatížení. |
| [Protokolování auditu Azure Policy](../../governance/policy/overview.md) | Pomáhá vyhovět standardům organizace a vyhodnocovat dodržování předpisů v rozsahu. Azure Policy používá protokoly aktivit, kterým se automaticky povoluje zahrnutí zdroje událostí, data, uživatele, časového razítka, zdrojových adres, cílových adres a dalších užitečných prvků. |
| **Aplikace & dat** |  |
| [Azure Defender pro registry kontejnerů](../../security-center/defender-for-container-registries-introduction.md) | Zahrnuje kontrolu ohrožení zabezpečení pro skenování imagí v Azure Container Registry Registry na základě Azure Resource Manager a poskytuje hlubší přehled o chybách vašich imagí. |
| [Azure Defender pro Kubernetes](../../security-center/defender-for-kubernetes-introduction.md) | Poskytuje ochranu před hrozbami na úrovni clusteru prostřednictvím sledování služeb spravovaných AKS prostřednictvím protokolů načtených službou Azure Kubernetes (AKS). |
| [Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) | Zprostředkovatel Cloud Access Security broker (CASB), který pracuje s více cloudy. Nabízí podrobný přehled důležitých informací, kontrolu nad přenosem dat a propracované analýzy pro identifikaci a odvracení kybernetických hrozeb napříč všemi vašimi cloudovými službami. |

## <a name="investigate-and-respond"></a>Prozkoumání a reakce

:::image type="content" source="media/end-to-end/investigate-and-respond.svg" alt-text="Diagram znázorňující služby Azure, které vám pomůžou prozkoumat hrozby a reagovat na ně." border="false":::

| Služba | Popis |
|------|--------|
| [Azure Sentinel](../../sentinel/hunting.md) | Výkonné nástroje pro vyhledávání a dotazy, které můžete využít pro bezpečnostní hrozby v rámci zdrojů dat vaší organizace. |
| [&nbsp; &nbsp; Protokoly &nbsp; a &nbsp; metriky Azure monitor](../../azure-monitor/overview.md) | Poskytuje komplexní řešení pro shromažďování, analýzu a provoz telemetrie z vašeho cloudového a místního prostředí. Azure Monitor [shromažďuje a agreguje data](../../azure-monitor/data-platform.md#observability-data-in-azure-monitor) z nejrůznějších zdrojů do společné datové platformy, kde se dá použít k analýze, vizualizaci a upozorňování. |
| **&nbsp; & &nbsp; Správa přístupu k identitám &nbsp;** |  |
| [&nbsp; &nbsp; Sestavy &nbsp; a monitorování Azure &nbsp; AD](https://docs.microsoft.com/azure/active-directory/reports-monitoring/) | [Sestavy Azure AD](../../active-directory/reports-monitoring/overview-reports.md) poskytují ucelený přehled o aktivitě ve vašem prostředí. |
|  | [Azure AD monitoring](../../active-directory/reports-monitoring/overview-monitoring.md) umožňuje směrovat protokoly aktivit Azure AD do různých koncových bodů.|
| [Historie auditu Azure AD PIM](../../active-directory/privileged-identity-management/pim-how-to-use-audit-log.md) | Zobrazuje všechna přiřazení rolí a aktivace za posledních 30 dnů pro všechny privilegované role. |
| **Aplikace & dat** |  |
| [Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/investigate) | Poskytuje nástroje, které vám pomůžou pochopit, co se děje ve vašem cloudovém prostředí. |

## <a name="next-steps"></a>Další kroky

- Pochopte svou [sdílenou odpovědnost v cloudu](shared-responsibility.md).

- Seznamte se s [možnostmi izolace v cloudu Azure](isolation-choices.md) proti uživatelům se zlými úmysly i bez škodlivých uživatelů.