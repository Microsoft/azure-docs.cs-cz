---
title: Migrace ověřování aplikace na Azure Active Directory
description: Tento dokument white paper popisuje plánování a výhody migrace ověřování aplikace do služby Azure AD.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 02/05/2021
ms.author: kenwith
ms.reviewer: baselden
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1e05a7af3f0b95470432b4fb9602e1b41da9f72f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104952962"
---
# <a name="migrate-application-authentication-to-azure-active-directory"></a>Migrace ověřování aplikace na Azure Active Directory

## <a name="about-this-paper"></a>O tomto dokumentu

Tento dokument white paper popisuje plánování a výhody migrace ověřování aplikace do služby Azure AD. Je určená pro správce Azure a odborníky na identity.

Rozdělení procesu do čtyř fází, z nichž každé má podrobná kritéria plánování a ukončení, je navrženo tak, aby vám pomohlo naplánovat strategii migrace a pochopit, jak ověřování Azure AD podporuje cíle vaší organizace.

## <a name="introduction"></a>Úvod

V dnešní době vaše organizace vyžaduje, aby uživatelé slew aplikace (aplikace), aby mohli pracovat. Pro každý den budete pravděpodobně dál přidávat, vyvíjet a vyřazovat aplikace. Uživatelé přistupují k těmto aplikacím z obrovské škály podnikových a osobních zařízení a míst. Otevírají aplikace mnoha způsoby, včetně:

- prostřednictvím domovské stránky společnosti nebo portálu

- záložkami ve svých prohlížečích

- prostřednictvím adresy URL dodavatele pro aplikace typu software jako služba (SaaS)

- odkazy přímo na stolní počítače nebo mobilní zařízení uživatele prostřednictvím řešení pro správu mobilních zařízení a aplikací (MDM/MAM)

Vaše aplikace budou nejspíš používat následující typy ověřování:

- Místní federační řešení (například Active Directory Federation Services (AD FS) (ADFS) a příkaz k odeslání.

- Služba Active Directory (například ověřování pomocí protokolu Kerberos a Windows-Integrated ověřování)

- Další řešení pro správu identit a přístupu (například okta nebo Oracle), která jsou založená na cloudu

- Místní webová infrastruktura (například IIS a Apache)

- Infrastruktura hostovaná v cloudu (například Azure a AWS)

**Aby uživatelé mohli snadno a bezpečně přistupovat k aplikacím, vaším cílem je mít v místních i cloudových prostředích jednu sadu řízení přístupu a zásad.**

[Azure Active Directory (Azure AD)](../fundamentals/active-directory-whatis.md) nabízí univerzální platformu pro identitu, která poskytuje vašim lidem, partnerům a zákazníkům jedinou identitu pro přístup k aplikacím, které chtějí, a spolupráci z jakékoli platformy a zařízení.

![Diagram připojení Azure Active Directory](media/migrating-application-authentication-to-azure-active-directory-1.jpg)

Azure AD má [plnou sadu funkcí pro správu identit](../fundamentals/active-directory-whatis.md#which-features-work-in-azure-ad). Standardizace ověřování aplikací a autorizace do Azure AD vám umožní získat výhody, které tyto funkce poskytují.

Další zdroje migrace najdete na adrese [https://aka.ms/migrateapps](./migration-resources.md)

## <a name="benefits-of-migrating-app-authentication-to-azure-ad"></a>Výhody migrace ověřování aplikace do služby Azure AD

Přesunutí ověřování aplikace do služby Azure AD vám pomůže se správou rizik a nákladů, zvýšením produktivity a požadavky na dodržování předpisů a zásad správného řízení.

### <a name="manage-risk"></a>Řízení rizik

Ochrana vašich aplikací vyžaduje, abyste měli úplný přehled o všech rizikových faktorech. Migrace vašich aplikací do Azure AD slučuje vaše řešení zabezpečení. Díky tomu můžete:

- Vylepšete zabezpečený přístup uživatelů k aplikacím a přidruženým podnikovým datům pomocí [zásad podmíněného přístupu](../conditional-access/overview.md), [Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md)a technologií [ochrany identity](../identity-protection/overview-identity-protection.md) na základě rizik v reálném čase.

- Chraňte přístup privilegovaných uživatelů k vašemu prostředí pomocí přístupu správce [za běhu](../../azure-resource-manager/managed-applications/request-just-in-time-access.md) .

- K nejdůležitějším obchodním potřebám můžete využít [Azure AD pro více tenantů, geograficky distribuovanou a vysokou dostupnost](https://cloudblogs.microsoft.com/enterprisemobility/2014/09/02/azure-ad-under-the-hood-of-our-geo-redundant-highly-available-distributed-cloud-directory/).

- Chraňte své starší aplikace pomocí jedné z našich [integrací partnerských řešení pro zabezpečení hybridního přístupu](https://aka.ms/secure-hybrid-access) , které jste už mohli nasadit.

### <a name="manage-cost"></a>Správa nákladů

Vaše organizace může mít několik řešení pro správu přístupu k identitě (IAM). Migrace na jednu infrastrukturu Azure AD je příležitostí k omezení závislostí na licencích IAM (v místním prostředí nebo v cloudu) a na náklady na infrastrukturu. V případech, kdy jste už mohli platit za službu Azure AD prostřednictvím Microsoft 365 licencí, neexistuje žádný důvod, proč platíte přidané náklady na jiné řešení IAM.

**Pomocí Azure AD můžete snížit náklady na infrastrukturu pomocí:**

- Poskytování zabezpečeného vzdáleného přístupu k místním aplikacím pomocí [Azure proxy aplikací služby AD](./application-proxy.md).

- Odpojuje aplikace od přístupu k přihlašovacím údajům Prem ve vašem tenantovi [nastavením Azure AD jako důvěryhodného poskytovatele Universal identity](../hybrid/plan-connect-user-signin.md#choosing-the-user-sign-in-method-for-your-organization).

### <a name="increase-productivity"></a>Zvýšení produktivity

Ekonomické a bezpečnostní výhody přinášejí organizacím k přijetí Azure AD, ale plné přijetí a dodržování předpisů je pravděpodobnější, pokud uživatelé mají výhodu i vy. Pomocí Azure AD můžete:

- Pomocí bezproblémového a zabezpečeného přístupu k libovolné aplikaci, a to z libovolného zařízení a libovolného umístění, zvyšte přístup koncových uživatelů k [jednomu Sign-On (SSO)](./what-is-single-sign-on.md) .

- Využijte samoobslužné možnosti IAM, jako je [Samoobslužné resetování hesla](../authentication/concept-sspr-howitworks.md) a [Správa skupin samoobslužný](../enterprise-users/groups-self-service-management.md).

- Správa jenom jedné identity pro každého uživatele napříč cloudem a místními prostředími vám pomůže snížit administrativní režii:

  - [Automatizace zřizování](../app-provisioning/user-provisioning.md) uživatelských účtů (v [galerii Azure AD](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps)) na základě identit Azure AD
  - Přístup ke všem aplikacím z panelu MyApp v [Azure Portal ](https://portal.azure.com/)

- Umožněte vývojářům zabezpečený přístup ke svým aplikacím a zlepšení prostředí koncových uživatelů pomocí [platformy Microsoft Identity](../develop/v2-overview.md) Library s Microsoft Authentication Library (MSAL).

- Umožněte svým partnerům přístup k prostředkům v cloudu pomocí [Spolupráce B2B v Azure AD](../external-identities/what-is-b2b.md). Cloudové prostředky odstraňují režii při konfiguraci federace typu Point-to-Point s vašimi partnery.

### <a name="address-compliance-and-governance"></a>Řešení dodržování předpisů a zásad správného řízení

Dodržování zákonných zásad přístupu a sledování přístupu uživatelů k aplikacím a přidruženým datům pomocí integrovaných nástrojů auditu a rozhraní API zajistí dodržování zákonných požadavků. Pomocí Azure AD můžete monitorovat přihlášení aplikací prostřednictvím sestav, které používají nástroje pro řízení [událostí a Siem (Event monitor)](../reports-monitoring/plan-monitoring-and-reporting.md). K sestavám můžete přistupovat z portálu nebo rozhraní API a programově auditovat, kdo má přístup k vašim aplikacím, a odebrat přístup k neaktivním uživatelům prostřednictvím kontrol přístupu.

## <a name="plan-your-migration-phases-and-project-strategy"></a>Plánování fází migrace a strategie projektu

Když projekty technologie selžou, je často způsobena neodpovídajícími očekáváními, nezúčastněnými zúčastněnými stranami nebo nedostatečným sdělením. Naplánováním samotného projektu Zajistěte úspěch.

### <a name="the-phases-of-migration"></a>Fáze migrace

Než se dostanou k nástrojům, měli byste pochopit, jak se zabývat procesem migrace. Prostřednictvím několika pracovních konferencí z přímých na zákazníka doporučujeme následující čtyři fáze:

![Diagram fází migrace](media/migrating-application-authentication-to-azure-active-directory-2.jpg)

### <a name="assemble-the-project-team"></a>Sestavit projektový tým

Migrace aplikací je úsilí týmu a je nutné zajistit, aby byly všechny nejdůležitější pozice vyplněny. Je důležitá podpora od vyšších obchodních vedoucích. Ujistěte se, že zadáváte správnou sadu vedoucích sponzorů, organizací pro rozhodování a odborníky na danou problematiku (malé a střední podniky.)

Během projektu migrace může jedna osoba plnit více rolí, nebo více uživatelů splní jednotlivé role v závislosti na velikosti a struktuře vaší organizace. Můžete mít také závislost na dalších týmech, které hrají klíčovou roli v rámci zabezpečení na šířku.

Následující tabulka obsahuje klíčové role a jejich příspěvky:

| Role          | Contributions                                              |
| ------------- | ---------------------------------------------------------- |
| **Projektový manažer** | Projektový účet, který je k pro identifikátor projektu, včetně:<br /> – získat podporu pro pracovníky<br /> – přenesení zúčastněných účastníků<br /> -spravovat plány, dokumentaci a komunikaci |
| **Návrhář identity/správce Aplikace Azure AD** | Zodpovídá za následující:<br /> – návrh řešení ve spolupráci se zúčastněnými stranami<br /> -dokumentovat návrh řešení a provozní postupy pro předání do provozního týmu<br /> – Správa předprodukčních a produkčních prostředí |
| **Místní provozní tým služby AD** | Organizace, která spravuje různé místní zdroje identit, jako jsou doménové struktury AD, adresáře LDAP, systémy pro personální oddělení atd.<br /> -před synchronizací je potřeba provést všechny nápravné úlohy.<br /> -Zadejte účty služeb vyžadované pro synchronizaci<br /> – poskytnutí přístupu ke konfiguraci federace pro Azure AD |
| **Správce podpory IT** | Zástupce oddělení IT, který může poskytnout vstup na základě podpory této změny z perspektivy technické podpory. |
| **Vlastník zabezpečení**  | Zástupce z bezpečnostního týmu, který může zajistit, že plán bude splňovat požadavky na zabezpečení vaší organizace. |
| **Techničtí vlastníci aplikace** | Obsahuje technické vlastníky aplikací a služeb, které se budou integrovat s Azure AD. Poskytují atributy identity aplikace, které by měly být zahrnuty v procesu synchronizace. Obvykle mají relaci se zástupci CSV. |
| **Vlastníci obchodních aplikací** | Zástupce kolegů, kteří můžou poskytnout vstup na činnost koncového uživatele a užitečnost této změny z pohledu uživatele a vlastní celkový obchodní aspekt aplikace, což může zahrnovat správu přístupu. |
| **Pilotní skupina uživatelů** | Uživatelé, kteří se budou testovat jako součást každodenní práce, pilotní prostředí a poskytnou zpětnou vazbu k Průvodci zbývajícími nasazeními. |

### <a name="plan-communications"></a>Plán komunikace

Klíčem k úspěchu je efektivní obchodní zapojení a komunikace. Je důležité dát zúčastněným stranám a koncovým uživatelům způsob k získání informací a udržování informování o plánu aktualizací. Informujte uživatele o hodnotě migrace, o tom, jaké jsou očekávané časové osy, a jak naplánovat dočasné výpadky v podniku. Použijte několik cesty vedoucí, jako jsou například přehledové relace, e-maily, schůzky One-to-One, bannery a townhalls.

Na základě strategie komunikace, kterou jste si zvolili pro aplikaci, můžete chtít připomenout uživatele na probíhající výpadky. Měli byste také ověřit, že neexistují žádné nedávné změny nebo obchodní dopady, které by vyžadovaly odložení nasazení.

V následující tabulce najdete minimální navrhovanou komunikaci, která informují o zúčastněných účastníkůch:

**Fáze plánování a strategie projektu**:

| Komunikace      | Cílová skupina                                          |
| ------------------ | ------------------------------------------------- |
| Povědomí a obchodní/technická hodnota projektu | Vše kromě koncových uživatelů |
| Oslovení pro pilotní aplikace | – Aplikace vlastníci společnosti<br />-Aplikace: techničtí vlastníci<br />– Architekti a tým identity |

**Fáze 1 – zjišťování a rozsah**:

| Komunikace      | Cílová skupina                                          |
| ------------------ | ------------------------------------------------- |
| – Oslovování informací o aplikaci<br />– Výsledek oboru cvičení | -Aplikace: techničtí vlastníci<br />– Aplikace vlastníci společnosti |

**Fáze 2 – klasifikace aplikací a plánování pilotního projektu**:

| Komunikace      | Cílová skupina                                          |
| ------------------ | ------------------------------------------------- |
| – Výsledek klasifikací a to znamená pro plán migrace<br />– Plán předběžné migrace | -Aplikace: techničtí vlastníci<br /> – Aplikace vlastníci společnosti |

**Fáze 3 – plánování migrace a testování**:

| Komunikace      | Cílová skupina                                          |
| ------------------ | ------------------------------------------------- |
| -Výsledek testování migrace aplikace | -Aplikace: techničtí vlastníci<br />– Aplikace vlastníci společnosti |
| -Oznámení o tom, že migrace probíhá, a vysvětlení výsledného prostředí pro koncové uživatele.<br />– Výpadek přicházejících a kompletních komunikací, včetně toho, co by teď mělo dělat, zpětnou vazbu a jak získat pomoc | – Koncoví uživatelé (a všichni ostatní) |

**Fáze 4 – Správa a získání přehledů**:

| Komunikace      | Cílová skupina                                          |
| ------------------ | ------------------------------------------------- |
| Dostupné analýzy a přístup k nim | -Aplikace: techničtí vlastníci<br />– Aplikace vlastníci společnosti |

### <a name="migration-states-communication-dashboard"></a>Řídicí panel komunikace stavů migrace

Komunikace s celkovým stavem projektu migrace je zásadní, protože zobrazuje průběh a pomáhá vlastníkům aplikací, jejichž aplikace jsou k migraci připravené, k přípravě na přesun. Jednoduchý řídicí panel můžete vytvořit pomocí Power BI nebo jiných nástrojů pro vytváření sestav, který vám poskytne přehled o stavu aplikací během migrace.

Následující stavy migrace vám mohou pomoci:

| Stavy migrace       | Akční plán                                   |
| ---------------------- | --------------------------------------------- |
| **Počáteční požadavek** | Vyhledejte aplikaci a obraťte se na vlastníka, kde najdete další informace. |
| **Posouzení dokončeno** | Vlastník aplikace vyhodnocuje požadavky aplikace a vrátí dotazník aplikace</td>
| **Probíhá konfigurace.** | Vývoj změn nezbytných pro správu ověřování v Azure AD |
| **Konfigurace testu byla úspěšná** | Vyhodnoťte změny a ověřte aplikaci proti testovacímu tenantovi Azure AD v testovacím prostředí. |
| **Konfigurace výroby byla úspěšná.** | Změna konfigurací pro práci na provozním tenantovi AD a vyhodnocení ověřování aplikací v testovacím prostředí |
| **Dokončení/odhlášení** | Nasaďte změny pro aplikaci do provozního prostředí a spusťte službu proti produkčnímu tenantovi služby Azure AD. |

Tím se zajistí, že vlastníci aplikace vědí, co plán migrace a testování aplikací probíhá, když jsou jejich aplikace k migraci a jaké jsou výsledky z jiných aplikací, které už byly migrovány. Můžete také zvážit poskytování odkazů na databázi pro sledování chyb, aby vlastníci mohli zajímat a zobrazovat problémy pro migrované aplikace.

### <a name="best-practices"></a>Osvědčené postupy

Níže jsou uvedené scénáře úspěšnosti zákazníků a partnerů a doporučené osvědčené postupy:

- [Pět tipů pro zlepšení procesu migrace](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Five-tips-to-improve-the-migration-process-to-Azure-Active/ba-p/445364) , které Azure Active Directory konzultace Patriot, je členem naší partnerské sítě, která se zaměřuje na pomoc zákazníkům s bezpečným nasazením cloudových řešení Microsoftu.

- Vytvořte [strategii pro řízení rizik pro migraci vaší aplikace Azure AD](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Develop-a-risk-management-strategy-for-your-Azure-AD-application/ba-p/566488) Edgile partnerem, který se zaměřuje na řešení IAM a řízení rizik.

## <a name="phase-1-discover-and-scope-apps"></a>Fáze 1: zjišťování a rozsah aplikací

**Zjišťování a analýza aplikací je základní cvičení, které vám nabídne dobrý začátek.** Nesmíte poznat všechno, co je potřeba k tomu, aby vyhovovaly neznámým aplikacím.

### <a name="find-your-apps"></a>Hledání aplikací

Prvním rozhodovacím bodem migrace aplikace je to, které aplikace se mají migrovat, pokud by nějaké měly zůstat a které aplikace mají být zastaralé. K dispozici je vždy příležitost k vyřazení aplikací, které nebudete ve vaší organizaci používat. Existuje několik způsobů, jak ve vaší organizaci najít aplikace. **Při zjišťování aplikací se ujistěte, že máte k disdobě aplikace pro vývoj a naplánování. pro ověřování ve všech budoucích aplikacích použijte službu Azure AD.**

**Shromažďování správného inventáře aplikací pomocí Active Directory Federation Services (AD FS) (AD FS):**

- **Použití Azure AD Connect Health** Pokud máte licenci Azure AD Premium, doporučujeme nasadit [Azure AD Connect Health](../hybrid/how-to-connect-health-adfs.md) k analýze využití aplikací v místním prostředí. Pomocí [sestavy aplikace AD FS](./migrate-adfs-application-activity.md) (Preview) můžete zjistit, které aplikace ADFS se dají migrovat, a vyhodnotit připravenost aplikace, která se má migrovat. Po dokončení migrace nasaďte [Cloud Discovery](/cloud-app-security/set-up-cloud-discovery) , které vám umožní průběžně monitorovat stínové IT ve vaší organizaci, když jste v cloudu.

- **Analýza protokolu AD FS**. Pokud licence Azure AD Premium nemáte, doporučujeme použít službu AD FS na nástroje pro migraci aplikací Azure AD, které jsou založené na [PowerShellu.](https://github.com/AzureAD/Deployment-Plans/tree/master/ADFS%20to%20AzureAD%20App%20Migration) Přečtěte si [Průvodce řešením](./migrate-adfs-apps-to-azure.md):

[Migrují se aplikace z Active Directory Federation Services (AD FS) (AD FS) do Azure AD.](./migrate-adfs-apps-to-azure.md)

### <a name="using-other-identity-providers-idps"></a>Používání jiných zprostředkovatelů identity (zprostředkovatelů identity)

U jiných zprostředkovatelů identity (například okta nebo příkazového testu) můžete k exportu inventáře aplikací použít jejich nástroje. Můžete uvažovat o principech služby zaregistrovaných ve službě Active Directory, které odpovídají webovým aplikacím ve vaší organizaci.

### <a name="using-cloud-discovery-tools"></a>Používání cloudových nástrojů pro zjišťování

V cloudovém prostředí potřebujete bohatou viditelnost, kontrolu nad datovou cestou a propracované analýzy, abyste mohli najít a bojovat s internetovými hrozbami napříč všemi vašimi Cloud Services. Inventář cloudové aplikace můžete shromáždit pomocí těchto nástrojů:

- **Zprostředkovatel zabezpečení Cloud Accessu (CASB**) – [CASB](/cloud-app-security/) obvykle pracuje společně s bránou firewall, aby poskytovala přehled o používání cloudových aplikací vašich zaměstnanců a pomáhá chránit firemní data před kyberbezpečnosti hrozbami. Sestava CASB vám může přispět k určení nejpoužívanějších aplikací ve vaší organizaci a počátečních cílů pro migraci do Azure AD.

- **Cloud Discovery** – konfigurací [Cloud Discovery](/cloud-app-security/set-up-cloud-discovery)získáte přehled o využití cloudové aplikace a můžete zjistit neschválené nebo stínové aplikace IT.

- **Rozhraní API** – pro aplikace, které jsou připojené k infrastruktuře cloudu, můžete pomocí rozhraní API a nástrojů v těchto systémech začít přebírat inventář hostovaných aplikací. V prostředí Azure:

  - Pomocí rutiny [Get-AzureWebsite](/powershell/module/servicemanagement/azure.service/get-azurewebsite) získáte informace o Azure websites.

  - K získání informací o Web Apps Azure použijte rutinu [Get-AzureRMWebApp](/powershell/module/azurerm.websites/get-azurermwebapp) .
D
  - Všechny aplikace běžící v Microsoft IIS najdete z příkazového řádku Windows pomocí [AppCmd.exe](/iis/get-started/getting-started-with-iis/getting-started-with-appcmdexe#working-with-sites-applications-virtual-directories-and-application-pools).

  - Pomocí [aplikací](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#application-entity) a [instančních objektů](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#serviceprincipal-entity) získáte informace o instanci aplikace a aplikace v adresáři ve službě Azure AD.

### <a name="using-manual-processes"></a>Použití ručních procesů

Jakmile budete mít k dispozici automatizované přístupy popsané výše, budete mít dobrý popisovač na vašich aplikacích. Můžete ale zvážit následující postup, abyste měli jistotu, že máte dobré pokrytí ve všech oblastech přístupu uživatele:

- Pokud chcete najít používané aplikace ve vaší organizaci, obraťte se na různé vlastníky firmy ve vaší organizaci.

- Spusťte na svém proxy server Nástroj pro kontrolu protokolu HTTP nebo Analyzujte protokoly proxy serveru, abyste viděli, kde se často směruje provoz.

- Zkontrolujte websites z oblíbených webů portál společnosti a podívejte se, co odkazy uživatelé přistupují k většině.

- Zajistěte si výkonné pracovníky nebo jiné klíčové obchodní členy, abyste se ujistili, že jste pokryli důležité obchodní aplikace.

### <a name="type-of-apps-to-migrate"></a>Typ aplikací, které se mají migrovat

Jakmile najdete své aplikace, zjistíte tyto typy aplikací ve vaší organizaci:

- Aplikace, které používají moderní ověřovací protokoly, již

- Aplikace používající starší protokoly pro ověřování, které se rozhodnete modernizovat

- Aplikace používající starší protokoly pro ověřování, které se rozhodnete modernizovat

- Nové obchodní aplikace (LoB)

### <a name="apps-that-use-modern-authentication-already"></a>Aplikace, které používají moderní ověřování, již

Již moderní aplikace jsou pravděpodobně přesunuty do Azure AD. Tyto aplikace už používají moderní ověřovací protokoly (například SAML nebo OpenID Connect) a dají se překonfigurovat pro ověřování pomocí Azure AD.

Kromě možností v [galerii aplikací Azure AD](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps) se může jednat o aplikace, které už existují ve vaší organizaci, nebo na jakékoli aplikace třetích stran od dodavatele, který není součástí Galerie Azure AD ([aplikace mimo galerii)](./add-application-portal.md).

Starší verze aplikací, které se rozhodnete modernizovat

U starších aplikací, které chcete modernizovat, se při přechodu do Azure AD pro základní ověřování a autorizaci odemkne veškerá data a možnosti, které [Microsoft Graph](https://developer.microsoft.com/graph/gallery/?filterBy=Samples,SDKs) a [Intelligent Security Graph](https://www.microsoft.com/security/operations/intelligence?rtc=1) musí nabídnout.

Doporučujeme **aktualizovat kód zásobníku pro ověřování** pro tyto aplikace ze starší verze protokolu (například Windows-Integrated ověřování, vynuceného delegování protokolu Kerberos, ověřování pomocí hlaviček http) na moderní protokol (například SAML nebo OpenID Connect).

### <a name="legacy-apps-that-you-choose-not-to-modernize"></a>Starší verze aplikací, které se rozhodnete modernizovat

U některých aplikací využívajících starší verze ověřovacích protokolů se někdy modernizaci jejich ověřování, ale nejedná se o správné použití pro obchodní účely. Mezi ně patří následující typy aplikací:

- Aplikace uchovávané v místním prostředí pro účely dodržování předpisů nebo kontroly.

- Aplikace připojené k místní identitě nebo zprostředkovateli federace, které nechcete měnit.

- Aplikace vyvinuté pomocí místních standardů ověřování, u kterých nemáte žádné plány k přesunu

Azure AD může těmto starším aplikacím přinést skvělé výhody, protože můžete povolit moderní funkce zabezpečení Azure AD a zásad správného řízení, jako je [Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md), [podmíněný přístup](../conditional-access/overview.md), [ochrana identit](../identity-protection/index.yml), [delegovaný přístup k aplikacím](./access-panel-manage-self-service-access.md)a kontroly [přístupu k](../governance/manage-user-access-with-access-reviews.md#create-and-perform-an-access-review) těmto aplikacím, aniž byste museli aplikaci vůbec dotýkat!

Začněte tím, že **tyto aplikace rozšíříte do cloudu** s využitím služby Azure AD [Application proxy](./application-proxy-configure-single-sign-on-password-vaulting.md) pomocí jednoduchých způsobů ověřování (jako je třeba trezor hesel), abyste mohli snadno migrovat uživatele nebo prostřednictvím našich [integrací partnerů](https://azure.microsoft.com/services/active-directory/sso/secure-hybrid-access/) s řadiči pro doručování aplikací, které jste už nasadili.

### <a name="new-line-of-business-lob-apps"></a>Nové obchodní aplikace (LoB)

Obvykle vyvíjíte obchodní aplikace pro interní použití vaší organizace. Pokud máte v kanálu nové aplikace, doporučujeme implementovat OpenID Connect pomocí [platformy Microsoft Identity](../develop/v2-overview.md) .

### <a name="apps-to-deprecate"></a>Aplikace, které mají být zastaralé

Aplikace bez jasných vlastníků a jasné údržby a monitorování představují bezpečnostní riziko pro vaši organizaci. Nepoužívané aplikace můžete zvážit v těchto případech:

- jejich **funkce jsou vysoce redundantní** s jinými systémy. • není k dispozici **žádný obchodní vlastník** .

- k dispozici není jasné **použití**.

Doporučujeme, abyste **nezrušili zastaralost s vysokým dopadem na nejdůležitější obchodní aplikace**. V těchto případech spolupracuje s vlastníky firmy a určí správnou strategii.

### <a name="exit-criteria"></a>Výstupní kritéria

V této fázi jste úspěšně provedli následující kroky:

- Dobré porozumění systémům v rozsahu migrace (které můžete vyřadit po přesunu do Azure AD)

- Seznam aplikací, které zahrnují:

  - K jakým systémům se aplikace připojují
  - Odkud a na jakém zařízení uživatelé k nim přistupují
  - Zda budou migrovány, zastaralé nebo připojeny k [Azure AD Connect](../hybrid/whatis-azure-ad-connect.md).

> [!NOTE]
> Můžete si stáhnout [list zjišťování aplikací](https://download.microsoft.com/download/2/8/3/283F995C-5169-43A0-B81D-B0ED539FB3DD/Application%20Discovery%20worksheet.xlsx) a zaznamenat aplikace, které chcete migrovat do ověřování Azure AD, a ty, které chcete opustit, ale spravovat je pomocí [Azure AD Connect](../hybrid/whatis-azure-ad-connect.md).

## <a name="phase-2-classify-apps-and-plan-pilot"></a>Fáze 2: klasifikace aplikací a plánování pilotního nasazení

Klasifikace migrace aplikací je důležitým cvičením. Ne každá aplikace musí být migrována a převedena ve stejnou dobu. Po shromáždění informací o jednotlivých aplikacích můžete racionalizovat, které aplikace by se měly migrovat jako první a které můžou přibrat čas.

### <a name="classify-in-scope-apps"></a>Klasifikace aplikací v oboru

Jedním ze způsobů, jak si to představit, je na osách nejdůležitějších organizací, využití a životnosti, z nichž každá je závislá na několika faktorech.

### <a name="business-criticality"></a>Obchodní důležitost

Nepostradatelné podnikání bude pro každou firmu trvat v různých rozměrech, ale dvě míry, které byste měli vzít v úvahu, jsou **funkce a funkce** a **profily uživatelů**. Přiřaďte aplikace s jedinečnou funkcí s vyšší hodnotou, než je u redundantních nebo zastaralých funkcí.

![Diagram spektra funkcí & funkcí a uživatelských profilů](media/migrating-application-authentication-to-azure-active-directory-3.jpg)

### <a name="usage"></a>Využití

Aplikace s **čísly s vysokým využitím** by měly získat vyšší hodnotu než aplikace s nízkým využitím. Pro aplikace s uživateli externích, vedoucích a bezpečnostních týmů přiřaďte vyšší hodnotu. U každé aplikace v portfoliu migrace dokončete tato posouzení.

![Diagram spektra uživatelských svazků a šířky uživatele](media/migrating-application-authentication-to-azure-active-directory-4.jpg)

Jakmile určíte hodnoty pro kritickou a využití firmy, můžete určit **životnost aplikace** a vytvořit matrici priority. Podívejte se na jednu z následujících matric:

![Trojúhelníkový diagram znázorňující vztahy mezi využitím, očekávanou životností a důležitosti podniku](media/migrating-application-authentication-to-azure-active-directory5.jpg)

### <a name="prioritize-apps-for-migration"></a>Určení priorit aplikací pro migraci

Můžete zvolit, že se má migrace aplikace zahájit buď s aplikacemi s nejnižší prioritou, nebo s nejvyšší prioritou, které jsou založené na potřebách vaší organizace.

V případě, kdy nemáte zkušenosti s používáním služeb Azure AD a identity, zvažte nejprve přesunutí **aplikací s nejnižší prioritou** do služby Azure AD. Tím se minimalizuje váš dopad na váš podnik a můžete vytvořit potenciál. Až tyto aplikace úspěšně přesunete a získali jistotu účastníků, můžete pokračovat v migraci ostatních aplikací.

Pokud není jasné, měli byste zvážit přesunutí aplikací, které jsou v [galerii Azure AD](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps) , a podporu více zprostředkovatelů identity (AD FS nebo okta), protože je snazší je integrovat. Je možné, že tyto aplikace jsou ve vaší organizaci **aplikace s nejvyšší prioritou** . Abychom vám pomohli integrovat aplikace SaaS s Azure AD, vyvinuli jsme kolekci [kurzů](../saas-apps/tutorial-list.md) , které vás provedou konfigurací.

Když máte konečný termín k migraci aplikací, bude mít tento kontejner aplikací s nejvyšší prioritou hlavní zatížení. Nakonec můžete vybrat aplikace s nižší prioritou, protože se nemění náklady, i když jste konečný termín přesunuli. I v případě, že je nutné licenci prodloužit, bude to pro malou částku.

Kromě této klasifikace a v závislosti na naléhavosti migrace můžete také zvážit **plán migrace** , ve kterém se musí vlastníci aplikace zapojit do svých aplikací. Na konci tohoto procesu byste měli mít seznam všech aplikací v kontejnerech s určením priorit pro migraci.

### <a name="document-your-apps"></a>Dokumentování aplikací

Nejdřív začněte shromažďováním klíčových podrobností o vašich aplikacích. [List funkce Application Discovery](https://download.microsoft.com/download/2/8/3/283F995C-5169-43A0-B81D-B0ED539FB3DD/Application%20Discovery%20worksheet.xlsx)vám pomůže rychle provést rozhodnutí o migraci a získat doporučení pro vaši obchodní skupinu bez jakýchkoli času.

Mezi informace, které jsou důležité pro rozhodování o migraci, patří:

- **Název aplikace** – jaká je tato aplikace známá jako pro firmu?

- **Typ aplikace** – je to aplikace SaaS třetí strany? Vlastní obchodní webová aplikace? Rozhraní API?

- **Důležitá pro podnikání** – je jeho vysoká kritická náročnost? Slab? Nebo někde mezi?

- **Svazek přístupu uživatele** – umožňuje všem uživatelům přístup k této aplikaci nebo jenom k několika lidem.

- **Plánované životnost** – jak dlouho se tato aplikace bude pohybovat? Méně než šest měsíců? Více než dva roky?

- **Aktuální zprostředkovatel identity** – co je primární IDP pro tuto aplikaci? Nebo se spoléhá na místní úložiště?

- **Metoda ověřování** – aplikace se ověřuje pomocí otevřených standardů?

- Bez **ohledu na to, jestli plánujete kód aplikace aktualizovat** – je aplikace v rámci plánovaného nebo aktivního vývoje?

- Bez **ohledu na to, jestli plánujete ponechat aplikaci v místním** prostředí – chcete aplikaci zachovat v dlouhodobém horizontu?

- **Jestli aplikace závisí na jiných aplikacích nebo rozhraních API** – aplikace se aktuálně volá na jiné aplikace nebo rozhraní API?

- Bez **ohledu na to, jestli je aplikace v galerii Azure AD** – je aplikace teď už integrovaná s [galerií Azure AD](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps)?

Další data, která vám pomůžou později, ale nemusíte dělat bezprostřední rozhodnutí o migraci, zahrnuje:

- **Adresa URL aplikace** – kam uživatelé přejdou k přístupu k aplikaci?

- **Popis aplikace** – stručný popis toho, co aplikace dělá?

- **Vlastník aplikace** – kdo v podniku je hlavním ověřením koncepce pro aplikaci?

- **Obecné komentáře nebo poznámky** – všechny ostatní obecné informace o vlastnictví aplikace nebo firmy

Jakmile budete svoji aplikaci klasifikovat a popsali si podrobnosti, ujistěte se, že jste si koupili své plánované strategie migrace v obchodním vlastnictví.

### <a name="plan-a-pilot"></a>Plánování pilotního projektu

Aplikace, které vyberete pro pilotní projekt, by měly představovat klíčovou identitu a požadavky na zabezpečení vaší organizace a Vy musíte mít jasné předplatné z vlastníků aplikací. Pilotní nástroje se obvykle spouštějí v samostatném testovacím prostředí. [Doporučené postupy pro pilotní](../fundamentals/active-directory-deployment-plans.md#best-practices-for-a-pilot) nasazení najdete na stránce plány nasazení.

**Nezapomeňte o vašich externích partnerech.** Ujistěte se, že se účastní plánů migrace a testování. Nakonec se ujistěte, že mají způsob, jak získat přístup k helpdesku, pokud došlo k narušení problémů.

### <a name="plan-for-limitations"></a>Plánování omezení

I když se některé aplikace dají snadno migrovat, můžou trvat delší dobu, než se vyberou další servery nebo instance. Například migrace SharePoint může trvat delší dobu, protože vlastní přihlašovací stránky.

Mnoho dodavatelů aplikací SaaS se účtuje za změnu připojení SSO. Podívejte se na ně a naplánujte ji.

Azure AD má také [omezení a omezení služeb](../enterprise-users/directory-service-limits-restrictions.md) , o kterých byste měli vědět.

### <a name="app-owner-sign-off"></a>Přihlášení vlastníka aplikace

Důležité obchodní a všeobecně používané aplikace můžou potřebovat skupinu uživatelů pilotního nasazení k otestování aplikace ve fázi pilotního nasazení. Po otestování aplikace v předprodukčním nebo pilotním prostředí zajistěte, aby se vlastníci obchodních aplikací odhlásili o výkonu před migrací aplikace a všech uživatelů na používání Azure AD k ověřování.

### <a name="plan-the-security-posture"></a>Plánování stav zabezpečení

Než zahájíte proces migrace, je potřeba plně zvážit stav zabezpečení, které chcete pro váš podnikový systém identit vyvíjet. To je založené na shromažďování těchto cenných informací: **identit, zařízení a umístění, která přistupují k vašim datům.**

### <a name="identities-and-data"></a>Identity a data

Většina organizací má specifické požadavky na identity a ochranu dat, které se v rámci organizací liší podle odvětví a funkcí úloh. V tématu [Konfigurace identit a přístupu k zařízením](/microsoft-365/enterprise/microsoft-365-policies-configurations) najdete doporučení, včetně předepsané sady [zásad podmíněného přístupu](../conditional-access/overview.md) a souvisejících možností.

Tyto informace můžete použít k ochraně přístupu ke všem službám integrovaným s Azure AD. Tato doporučení jsou [v rámci služby Azure AD](../fundamentals/identity-secure-score.md)zarovnaná se službou Microsoft Secure skore a hodnocením identity. Toto skóre vám umožní:

- Objektivně změřit stav zabezpečení vaší identity

- Naplánovat vylepšení zabezpečení identity

- Posoudit úspěšnost těchto vylepšení

Pomůže vám to také při implementaci těchto [pěti kroků k zabezpečení infrastruktury identity](../../security/fundamentals/steps-secure-identity.md). Použijte pokyny jako výchozí bod vaší organizace a upravte zásady tak, aby splňovaly konkrétní požadavky vaší organizace.

### <a name="who-is-accessing-your-data"></a>Kdo přistupuje k vašim datům?

Existují dvě hlavní kategorie uživatelů vašich aplikací a prostředků, které Azure AD podporuje:

- **Interní:** Zaměstnanci, dodavatelé a dodavatelé, kteří mají účty v rámci vašeho zprostředkovatele identity. To může potřebovat další pivoty s různými pravidly pro vedoucí nebo vedoucí pracovníky a jiné zaměstnance.

- **Externí:** Dodavatelé, dodavatelé, distributoři nebo jiní obchodní partneři, kteří komunikují s vaší organizací v pravidelných firmách s [spoluprací B2B v Azure AD.](../external-identities/what-is-b2b.md)

Můžete definovat skupiny pro tyto uživatele a tyto skupiny naplnit různými způsoby. Můžete se rozhodnout, že správce musí do skupiny ručně přidat členy, nebo můžete povolit členství ve skupině samoobslužný. Lze vytvořit pravidla, která automaticky přidají členy do skupin na základě zadaných kritérií pomocí [dynamických skupin](../enterprise-users/groups-dynamic-membership.md).

Externí uživatelé můžou také odkazovat na zákazníky. [Azure AD B2C](../../active-directory-b2c/overview.md)samostatný produkt podporuje ověřování zákazníků. Je však mimo rozsah tohoto dokumentu.

### <a name="devicelocation-used-to-access-data"></a>Zařízení/umístění, které se používá pro přístup k datům

Důležité je také zařízení a umístění, které uživatel používá pro přístup k aplikaci. Zařízení, která jsou fyzicky připojená k podnikové síti, jsou bezpečnější. Je možné, že budete potřebovat kontrolu připojení zvenčí sítě přes síť VPN.

![Diagram znázorňující relaci mezi umístěním uživatele a přístupem k datům](media/migrating-application-authentication-to-azure-active-directory-6.jpg)

S těmito aspekty prostředků, uživatelů a zařízení se můžete rozhodnout použít možnosti [podmíněného přístupu Azure AD](../conditional-access/overview.md) . Podmíněný přístup překračuje oprávnění uživatele: vychází z kombinace faktorů, například identity uživatele nebo skupiny, sítě, ke které je uživatel připojený, zařízení a aplikace, které používají, a typu dat, ke kterým se snaží získat přístup. Přístup udělený uživateli se přizpůsobí této širší sadě podmínek.

### <a name="exit-criteria"></a>Výstupní kritéria

V této fázi jste úspěšně provedli následující kroky:

- Poznejte své aplikace
  - Plně dokumentované aplikace, které máte v úmyslu migrovat
  - Nastavili prioritní aplikace na základě důležitosti podniku, objemu využití a životnosti.

- Máte vybrané aplikace, které reprezentují vaše požadavky na pilotní nasazení.

- Zakoupení firemních vlastníků a jejich stanovení priorit a strategie

- Pochopení potřeb stav zabezpečení a jejich implementace

## <a name="phase-3-plan-migration-and-testing"></a>Fáze 3: plánování migrace a testování

Po zakoupení firemního nákupu je dalším krokem zahájení migrace těchto aplikací do ověřování Azure AD.

### <a name="migration-tools-and-guidance"></a>Nástroje a doprovodné materiály pro migraci

Pomocí níže uvedených nástrojů a pokynů proveďte přesné kroky potřebné k migraci vašich aplikací do Azure AD:

- **Obecné pokyny k migraci** – pomocí dotazníku dokumentů White Paper, nástrojů, e-mailových šablon a aplikací v sadě [nástrojů pro migraci aplikací Azure AD](./migration-resources.md) můžete zjišťovat, klasifikovat a migrovat vaše aplikace.

- **SaaS aplikace** – Podívejte se na náš seznam [stovek výukových kurzů SaaS](../saas-apps/tutorial-list.md) a kompletní [plán nasazení služby Azure AD SSO](https://aka.ms/ssodeploymentplan) , který vás provede celým procesem.

- **Aplikace spuštěné místně** – Přečtěte si všechno [o proxy aplikací služby AD Azure](./application-proxy.md) a využijte kompletní [plán nasazení Azure proxy aplikací služby AD](https://aka.ms/AppProxyDPDownload) , abyste mohli rychle začít.

- **Aplikace, které vyvíjíte** – Přečtěte si podrobné pokyny k [integraci](../develop/quickstart-register-app.md) a [registraci](../develop/quickstart-register-app.md) .

Po migraci můžete odeslat komunikaci informující o uživatelích úspěšného nasazení a připomenout jim všechny nové kroky, které musí provést.

### <a name="plan-testing"></a>Plánování testování

Během procesu migrace může být v aplikaci již během pravidelných nasazení použito testovací prostředí. Pro testování migrace můžete pokračovat v používání tohoto prostředí. Pokud testovací prostředí není aktuálně k dispozici, může být možné nastavit ho pomocí Azure App Service nebo Azure Virtual Machines v závislosti na architektuře aplikace. Můžete se rozhodnout nastavit samostatného testovacího tenanta Azure AD, který se použije při vývoji konfigurací aplikace. Tento tenant se spustí v čistém stavu a nebude nakonfigurovaný pro synchronizaci s žádným systémem.

Každou aplikaci můžete testovat tak, že se přihlásíte pomocí testovacího uživatele a zajistěte, aby všechny funkce byly stejné jako před migrací. Pokud během testování určíte, že uživatelé budou muset aktualizovat nastavení [MFA](/azure/active-directory/authentication/howto-mfa-userstates) nebo [SSPR](../authentication/tutorial-enable-sspr.md)nebo přidáváte tuto funkci během migrace, nezapomeňte ji přidat do plánu komunikace koncového uživatele. [Seznamte se s](https://aka.ms/mfatemplates) [SSPRmi](https://aka.ms/ssprtemplates) komunikačními šablonami pro koncové uživatele.

Po migraci aplikací přejdete na [Azure Portal](https://aad.portal.azure.com/) a otestujete, jestli migrace proběhla úspěšně. Postupujte podle následujících pokynů:

- Vyberte **podnikové aplikace &gt; všechny aplikace** a v seznamu najděte svoji aplikaci.

- Vyberte **Spravovat &gt; uživatele a skupiny** a přiřaďte aplikaci aspoň jednoho uživatele nebo skupinu.

- Vyberte **Spravovat &gt; podmíněný přístup**. Zkontrolujte seznam zásad a ujistěte se, že neblokujete přístup k aplikaci pomocí [zásad podmíněného přístupu](../conditional-access/overview.md).

V závislosti na konfiguraci aplikace ověřte, že jednotné přihlašování funguje správně.

| Typ ověřování      | Testování                                             |
| ------------------------ | --------------------------------------------------- |
| **OAuth/OpenID Connect** | Vyberte **&gt; oprávnění podnikových aplikací** a ujistěte se, že jste souhlasili s tím, že se má aplikace používat ve vaší organizaci v nastavení uživatele pro vaši aplikaci. |
| **Jednotné přihlašování založené na SAML** | Použijte tlačítko [Test nastavení SAML](./debug-saml-sso-issues.md) , které se nachází v části **jednotné přihlašování.** |
| **Jednotné přihlašování založené na heslech** | Stáhněte a nainstalujte si [rozšíření pro zabezpečené přihlašování aplikace MyApp](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension). Toto rozšíření vám pomůže začít používat cloudové aplikace vaší organizace, které vyžadují použití procesu jednotného přihlašování. |

| **[Proxy aplikace](./application-proxy.md)** | Ujistěte se, že váš konektor je spuštěný a přiřazený k vaší aplikaci. Další pomoc najdete v [Průvodci odstraňováním potíží s proxy aplikací](./application-proxy-troubleshoot.md) . |

### <a name="troubleshoot"></a>Řešení potíží

Pokud narazíte na problémy, podívejte se na naše [Průvodce odstraňováním potíží s aplikacemi](../app-provisioning/isv-automatic-provisioning-multi-tenant-apps.md) , kde získáte nápovědu. Můžete si také prohlédnout články věnované řešení problémů s [přihlašováním k aplikacím nakonfigurovaným na jednotné přihlašování založené na SAML](/troubleshoot/azure/active-directory/troubleshoot-sign-in-saml-based-apps).

### <a name="plan-rollback"></a>Naplánování vrácení zpět

Pokud vaše migrace neproběhne úspěšně, je nejlepší postup vrátit a otestovat. Tady jsou kroky, které můžete provést, abyste mohli zmírnit problémy s migrací:

- Požádejte **snímky obrazovky** o existující konfiguraci vaší aplikace. Můžete se podívat zpátky, pokud musíte znovu nakonfigurovat aplikaci znovu.

- Můžete také zvážit **poskytování odkazů na starší verze ověřování**, pokud došlo k potížím s cloudovým ověřováním.

- Před dokončením migrace **neměňte stávající konfiguraci** pomocí staršího zprostředkovatele identity.

- Začněte migrací **aplikací, které podporují víc zprostředkovatelů identity**. Pokud dojde k nějaké chybě, můžete se vždycky změnit na upřednostňovanou konfiguraci IdP.

- Ujistěte se, že prostředí vaší aplikace má **tlačítko pro odeslání názoru** nebo ukazatele na vaše problémy **helpdesku** .

### <a name="exit-criteria"></a>Výstupní kritéria

V této fázi jste úspěšně provedli následující kroky:

- Určili, jak budou migrovány jednotlivé aplikace

- Revize nástrojů pro migraci

- Plánované testování včetně testovacích prostředí a skupin

- Plánované vrácení zpět

## <a name="phase-4-plan-management-and-insights"></a>Fáze 4: plánování správy a přehledů

Jakmile se aplikace migrují, musíte zajistit, aby:

- Uživatelé můžou bezpečně přistupovat a spravovat.

- Můžete získat vhodné přehledy o využití a stavu aplikací.

Pro vaši organizaci doporučujeme postupovat podle potřeby následujících akcí.

### <a name="manage-your-users-app-access"></a>Správa přístupu k aplikacím uživatelů

Po migraci aplikací můžete rozšířit uživatelské prostředí mnoha způsoby.

**Nastavit aplikace jako zjistitelného**

**Nasměrujte uživatele** na možnosti na portálu [MyApp](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension). V tomto případě mají přístup ke všem cloudovým aplikacím, aplikacím, které zpřístupníte pomocí [Azure AD Connect](../hybrid/whatis-azure-ad-connect.md), a aplikací, které používají [proxy aplikace](./application-proxy.md) , ale mají oprávnění pro přístup k těmto aplikacím.


Můžete se seznámit s uživateli, jak zjišťovat jejich aplikace:

- Pomocí [existující funkce jednotného přihlašování](./view-applications-portal.md) můžete **propojit uživatele s libovolnou aplikací** .


- Povolení [samoobslužného přístupu](./manage-self-service-access.md)k aplikaci a umožnění **uživatelům přidávají aplikace, které máte** v provozu

- [Skrýt aplikace od koncových uživatelů](./hide-application-from-user-portal.md) (výchozí aplikace Microsoftu nebo jiné aplikace) k **zajištění, že aplikace potřebují více zjistitelné**

### <a name="make-apps-accessible"></a>Usnadnění přístupu k aplikacím

**Umožněte uživatelům přístup k aplikacím z jejich mobilních zařízení**. Uživatelé mají přístup k portálu MyApp pomocí prohlížeče spravovaného Intune na zařízeních s [iOS](./hide-application-from-user-portal.md) 7,0 nebo novějším nebo [Androidem](./hide-application-from-user-portal.md) .

Uživatelé si můžou stáhnout **prohlížeč spravovaný přes Intune**:

- **Pro zařízení s Androidem** z [Google Play Storu](https://play.google.com/store/apps/details?id=com.microsoft.intune)

- **Pro zařízení Apple**, z [App Storu Apple](https://itunes.apple.com/us/app/microsoft-intune-managed-browser/id943264951?mt=8) nebo si můžou stáhnout [mobilní aplikaci Moje aplikace pro iOS](https://apps.apple.com/us/app/my-apps-azure-active-directory/id824048653)

**Umožněte uživatelům otevírat své aplikace z rozšíření prohlížeče.**

Uživatelé [si můžou stáhnout rozšíření pro zabezpečené přihlašování](https://www.microsoft.com/p/my-apps-secure-sign-in-extension/9pc9sckkzk84?rtc=1&activetab=pivot%3Aoverviewtab) v [Chrome,](https://chrome.google.com/webstore/detail/my-apps-secure-sign-in-ex/ggjhpefgjjfobnfoldnjipclpcfbgbhl) [Firefox](https://addons.mozilla.org/firefox/addon/access-panel-extension/) nebo [Microsoft Edge](https://www.microsoft.com/p/my-apps-secure-sign-in-extension/9pc9sckkzk84?rtc=1&activetab=pivot%3Aoverviewtab) a můžou spouštět aplikace přímo z okna prohlížeče:

- **Vyhledejte své aplikace a zobrazí se jejich naposledy používané aplikace.**

- **Automaticky převeďte interní adresy URL** , které jste nakonfigurovali v [proxy aplikace](./application-proxy.md) , na příslušné externí adresy URL. Uživatelé teď můžou pracovat s odkazy, které znají, bez ohledu na to, kde jsou.

**Umožněte uživatelům otevírat své aplikace z Office.com.**

Uživatelé můžou přejít na [Office.com](https://www.office.com/) **a vyhledat si své aplikace a nechat se jim zobrazovat naposledy používané aplikace** přímo z místa, kde fungují.

### <a name="secure-app-access"></a>Zabezpečený přístup k aplikacím

Azure AD poskytuje centralizované umístění přístupu pro správu migrovaných aplikací. Přejít na [Azure Portal](https://portal.azure.com/) a povolit následující možnosti:

- **Zabezpečte přístup uživatelů k aplikacím.** Povolte [zásady podmíněného přístupu](../conditional-access/overview.md)nebo [ochranu identity](../identity-protection/overview-identity-protection.md)a zabezpečte přístup uživatelů k aplikacím na základě stavu zařízení, umístění a dalších.

- **Automatické zřizování.** Nastavte [Automatické zřizování uživatelů](../app-provisioning/user-provisioning.md) s různými aplikacemi SaaS třetích stran, ke kterým mají uživatelé přístup. Kromě vytváření identit uživatelů zahrnuje i údržbu a odebírání identit uživatelů jako změny stavu nebo rolí.

- **Delegovat správu přístupu uživatelů**  V případě potřeby povolte Samoobslužný přístup k aplikacím a *přiřaďte mu obchodního schvalovatele k schválení přístupu k těmto aplikacím*. Používejte [samoobslužnou správu](../enterprise-users/groups-self-service-management.md)skupin pro skupiny přiřazené ke kolekcím aplikací.

- **Delegovat přístup správce.** pomocí **role adresáře** můžete uživateli přiřadit roli správce (například správce aplikace, správce cloudové aplikace nebo vývojář aplikace).

### <a name="audit-and-gain-insights-of-your-apps"></a>Auditujte a Získejte přehled o svých aplikacích

[Azure Portal](https://portal.azure.com/) taky můžete použít k auditování všech svých aplikací z centralizovaného umístění.

- **Auditujte svoji aplikaci** pomocí aplikací * * podnikové aplikace, proveďte audit nebo získejte přístup ke stejným informacím z [rozhraní API pro vytváření sestav Azure AD](../reports-monitoring/concept-reporting-api.md) a integrujte je do oblíbených nástrojů.

- **Zobrazení oprávnění pro aplikaci** pomocí **podnikových aplikací, oprávnění** pro aplikace využívající OAuth/OpenID Connect.

- **Získejte přehledy o přihlašování** pomocí **podnikových aplikací, přihlášení**. Přístup ke stejným informacím z [rozhraní API pro vytváření sestav Azure AD.](../reports-monitoring/concept-reporting-api.md)

- **Vizualizace využití vaší aplikace** z [balíčku obsahu Power BI Azure AD](../reports-monitoring/howto-use-azure-monitor-workbooks.md)

### <a name="exit-criteria"></a>Výstupní kritéria

V této fázi jste úspěšně provedli následující kroky:

- Zajištění zabezpečeného přístupu k aplikacím pro uživatele

- Správa pro audit a získání přehledů migrovaných aplikací

### <a name="do-even-more-with-deployment-plans"></a>Ještě víc s plány nasazení

Plány nasazení vás seznámí s obchodní hodnotou, plánováním, implementací kroků a správou řešení Azure AD, včetně scénářů migrace aplikací. Přinášejí dohromady vše, co potřebujete ke spuštění nasazení a získání hodnoty z možností Azure AD. Příručky pro nasazení obsahují obsah, jako jsou doporučené osvědčené postupy Microsoftu, komunikace koncových uživatelů, příručky pro plánování, implementační kroky, testovací případy a další.

K dispozici je spousta [plánů nasazení](../fundamentals/active-directory-deployment-plans.md) a pořád ještě máme spoustu!

### <a name="contact-support"></a>Kontaktování podpory

Pokud chcete vytvořit nebo sledovat lístek podpory a monitorovat stav, navštivte následující odkazy podpory.

- **Podpora Azure:** Můžete volat [Podpora Microsoftu](https://azure.microsoft.com/support) a otevřít lístek pro libovolný Azure

Problémy s nasazením identity v závislosti na vašem smlouva Enterprise s Microsoftem

- **FastTrack**: Pokud jste zakoupili licence pro Enterprise mobility and Security (EMS) nebo Azure AD Premium, máte nárok na získání pomoci s nasazením z [programu FastTrack.](/enterprise-mobility-security/solutions/enterprise-mobility-fasttrack-program)

- Propojte **tým pro technický vývoj produktů:** Pokud pracujete na hlavním nasazení zákazníka s miliony uživatelů, máte nárok na podporu od účet Microsoft týmu nebo architekta cloudových řešení. Na základě složitosti nasazení projektu můžete pracovat přímo s [týmem technické výroby Azure identity.](https://aad.portal.azure.com/#blade/Microsoft_Azure_Marketplace/MarketplaceOffersBlade/selectedMenuItemId/solutionProviders)

- **Blog o identitě Azure AD:** Přihlaste se k odběru [blogu identity Azure AD](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/bg-p/Identity) , abyste měli přehled o všech nejnovějších oznámeních produktů, podrobných komentáře a informacích o plánech přímo poskytovaných týmem pro technický vývoj identity.
