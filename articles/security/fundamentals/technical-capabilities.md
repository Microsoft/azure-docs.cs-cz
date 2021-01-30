---
title: Technické možnosti zabezpečení v Azure – Microsoft Azure
description: Seznámení se službami zabezpečení v Azure, které vám pomůžou chránit data, prostředky a aplikace v cloudu.
services: security
author: terrylanfear
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.date: 07/13/2020
ms.author: terrylan
ms.openlocfilehash: 353b1708a1c6014f9108ad2be62f22c1e0b0604c
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2021
ms.locfileid: "99092764"
---
# <a name="azure-security-technical-capabilities"></a>Technické možnosti zabezpečení Azure
Tento článek poskytuje Úvod do služby zabezpečení v Azure, které vám pomůžou chránit vaše data, prostředky a aplikace v cloudu a splňovat požadavky na zabezpečení vaší firmy.

## <a name="azure-platform"></a>Platforma Azure

[Microsoft Azure](https://azure.microsoft.com/overview/what-is-azure/) je cloudová platforma, která se skládá z infrastruktury a aplikačních služeb, s integrovanými datovými službami a pokročilými analýzami a vývojářskými nástroji a službami, které se hostují v datových centrech veřejného cloudu Microsoftu. Zákazníci využívají Azure pro řadu různých kapacit a scénářů, od základních výpočetních, síťových a úložných služeb až po mobilní a webové aplikace až po úplné cloudové scénáře, jako je Internet věcí, a dají se použít s technologiemi open source a nasazovat jako hybridní cloud nebo hostovat v datacentru zákazníka. Azure poskytuje cloudové technologie jako stavební bloky, které společnosti usnadňují úsporu nákladů, rychlou inovaci a správu systémů proaktivně. Při sestavování nebo migraci IT prostředků do poskytovatele cloudu se spoléháte na schopnosti této organizace chránit vaše aplikace a data službami a ovládacími prvky, které poskytují, ke správě zabezpečení cloudových prostředků.

Microsoft Azure je jediným poskytovatelem cloud computingu, který nabízí bezpečnou a konzistentní aplikační platformu a infrastrukturu jako služby pro týmy, které fungují v různých cloudových dovednosti a úrovních složitosti projektu. s integrovanými datovými službami a analýzami, které odhalují inteligentní informace z dat kdekoli, na platformách Microsoftu i jiných společností, otevřených rozhraních a nástrojích, a díky tomu nabízí možnost integrace cloudu s místním nasazením i cloudové služby Azure v místních datových centrech. V rámci důvěryhodných cloudů Microsoftu spoléhají zákazníci na špičkové zabezpečení, spolehlivost, dodržování předpisů, ochranu osobních údajů a rozsáhlou síť lidí, partnerů a procesů podporujících organizace v cloudu.

Pomocí Microsoft Azure můžete:

- Zrychlete inovace pomocí cloudu.

- Rozhodnutí o obchodování & aplikace s přehledy.

- Vytvářejte zdarma a nasaďte je kdekoli.

- Chraňte své podnikání.

## <a name="security-technical-capabilities-to-fulfil-your-responsibility"></a>Technické možnosti zabezpečení pro splnění vaší zodpovědnosti

Microsoft Azure poskytuje služby, které vám pomůžou splnit požadavky na zabezpečení, ochranu osobních údajů a dodržování předpisů. Následující obrázek vám pomůže vysvětlit různé služby Azure, které můžete použít k vytvoření zabezpečené a kompatibilní aplikační infrastruktury na základě oborových standardů.

![Dostupné technické možnosti zabezpečení – velký obrázek](./media/technical-capabilities/azure-security-technical-capabilities-fig1.png)

## <a name="manage-and-control-identity-and-user-access"></a>Správa a řízení identit a přístupu uživatelů

Azure vám pomůže chránit obchodní a osobní údaje tím, že vám umožní spravovat identity uživatelů a přihlašovací údaje a řídit přístup.

### <a name="azure-active-directory"></a>Azure Active Directory

Řešení pro správu identit a přístupu od Microsoftu umožňují chránit přístup k aplikacím a prostředkům v podnikovém datovém centru a do cloudu, což umožňuje zvýšit úroveň ověřování, jako je vícefaktorové ověřování a zásady podmíněného přístupu. Monitorování podezřelých aktivit prostřednictvím pokročilého generování sestav zabezpečení, auditování a upozorňování pomáhá zmírňovat potenciální problémy se zabezpečením. [Azure Active Directory Premium](../../active-directory/fundamentals/active-directory-whatis.md) poskytuje jednotné přihlašování k tisícům cloudových aplikací a přístup k webovým aplikacím, které spouštíte místně.

Výhody zabezpečení Azure Active Directory (Azure AD) zahrnují tyto možnosti:

- Umožňuje vytvořit a spravovat jednu identitu pro každého uživatele v celém podniku a přitom zajistit synchronizaci uživatelů, skupin a zařízení.

- Poskytněte přístup k aplikacím s jednotným přihlašováním, včetně tisíců předem integrovaných aplikací SaaS.

- Zajistit zabezpečení přístupu k aplikacím tím, že u místních i cloudových aplikací budete vynucovat vícefaktorové ověřování na základě pravidel.

- Zajištění zabezpečeného vzdáleného přístupu k místním webovým aplikacím prostřednictvím Azure Proxy aplikací služby AD.

[Portál Azure Active Directory](https://aad.portal.azure.com/) je k dispozici jako součást Azure Portal. Z tohoto řídicího panelu můžete získat přehled o stavu vaší organizace a snadno spravovat přístup k adresáři, uživatelům nebo aplikacím.

![Azure Active Directory](./media/technical-capabilities/azure-security-technical-capabilities-fig2.png)

Níže jsou uvedené základní možnosti správy identit Azure:

- Jednotné přihlašování

- Ověřování pomocí služby Multi-Factor Authentication

- Monitorování zabezpečení, výstrahy a sestavy založené na strojovém učení

- Správa identit a přístupu uživatelů

- Registrace zařízení

- Privileged Identity Management

- Ochrana identit

#### <a name="single-sign-on"></a>Jednotné přihlašování

[Jednotné přihlašování (SSO)](https://azure.microsoft.com/documentation/videos/overview-of-single-sign-on/) znamená přístup k všem aplikacím a prostředkům, které potřebujete udělat, pomocí jediného uživatelského účtu, když se přihlásíte jenom jednou. Jakmile se přihlásíte, budete mít přístup ke všem aplikacím, které potřebujete, aniž byste je museli ověřovat (například zadat heslo) podruhé.

Mnoho organizací spoléhá na aplikace typu software jako služba (SaaS), jako jsou Microsoft 365, box a Salesforce, pro produktivitu koncových uživatelů. Historicky zaměstnanci IT potřebují pro jednotlivé aplikace SaaS vytvářet a aktualizovat uživatelské účty a uživatelé si museli pamatovat heslo pro každou aplikaci SaaS.

[Azure AD rozšiřuje místní službu Active Directory do cloudu](../../active-directory/manage-apps/what-is-single-sign-on.md)a umožňuje uživatelům používat jejich primární účet organizace jenom k tomu, aby se přihlásili jenom k zařízením připojeným k doméně a prostředkům společnosti, ale také k aplikacím web a SaaS, které potřebuje pro svou práci.

Nejen uživatelé nepotřebují spravovat více sad uživatelských jmen a hesel, přístup k aplikaci se dá automaticky zřídit nebo zrušit na základě organizačních skupin a jejich stavu jako zaměstnanec. [Azure AD zavádí zabezpečení a přístup k řízení zásad správného řízení](../../active-directory/manage-apps/view-applications-portal.md) , které vám umožní centrálně spravovat přístup uživatelů napříč SaaS aplikacemi.

#### <a name="multi-factor-authentication"></a>Ověřování pomocí služby Multi-Factor Authentication

[Azure AD Multi-Factor Authentication (MFA)](../../active-directory/authentication/concept-mfa-howitworks.md) je metoda ověřování, která vyžaduje použití více než jedné metody ověřování a přidává kritickou druhou vrstvu zabezpečení pro přihlášení a transakce uživatelů. [Vícefaktorové ověřování pomáhá chránit](../../active-directory/authentication/concept-mfa-howitworks.md) přístup k datům a aplikacím a současně splňuje požadavky uživatelů na jednoduchý proces přihlašování. Zajišťuje silné ověřování prostřednictvím řady možností ověřování – telefonní hovor, textová zpráva nebo oznámení mobilní aplikace nebo ověřovací kód a tokeny OAuth třetích stran.

#### <a name="security-monitoring-alerts-and-machine-learning-based-reports"></a>Monitorování zabezpečení, výstrahy a sestavy založené na strojovém učení

Monitorování a výstrahy zabezpečení a sestavy založené na strojovém učení, které identifikují nekonzistentní vzory přístupu, můžou přispět k ochraně vaší firmy. Sestavy přístupu a využití Azure Active Directory můžete použít k získání přehledu o integritě a zabezpečení adresáře vaší organizace. S těmito informacemi může správce adresáře lépe určit, kde můžou být potenciální bezpečnostní rizika, aby mohli přiměřeně naplánovat zmírnění těchto rizik.

V Azure Portal nebo prostřednictvím [portálu Azure Active Directory](https://aad.portal.azure.com/)jsou [sestavy](../../active-directory/reports-monitoring/overview-reports.md) rozdělené podle následujících způsobů:

- Sestavy anomálií – obsahují události přihlášení, které jsme zjistili, že se neobvyklé. Naším cílem je, abyste se dozvěděli o těchto činnostech a mohli jste se rozhodnout, jestli je událost podezřelá.

- Integrované sestavy aplikací – poskytují přehled o tom, jak se ve vaší organizaci používají cloudové aplikace. Azure Active Directory nabízí integraci s tisíci cloudových aplikací.

- Zprávy o chybách – označují chyby, ke kterým může dojít při zřizování účtů pro externí aplikace.

- Sestavy specifické pro uživatele – zobrazuje zařízení a přihlašovat data aktivit pro konkrétního uživatele.

- Protokoly aktivit – obsahují záznam všech auditovaných událostí za posledních 24 hodin, posledních 7 dní nebo posledních 30 dní, změny aktivity skupiny a aktivity resetování hesla a registrace.

#### <a name="consumer-identity-and-access-management"></a>Správa identit a přístupu uživatelů

[Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) je vysoce dostupná, globální služba pro správu identit pro zákaznické aplikace, která se škáluje na stovky milionů identit. Dá se integrovat do mobilních i webových platforem. Zákazníci se můžou ke všem vašim aplikacím přihlašovat přes přizpůsobitelné prostředí pomocí svých existujících účtů v sociálních sítích nebo pomocí nově vytvořených přihlašovacích údajů.

V minulosti mohli vývojáři aplikací, kteří se chtěli [zaregistrovat a přihlašovat](../../active-directory-b2c/overview.md) do svých aplikací, napsat svůj vlastní kód. A použili by místní databáze nebo systémy k ukládání uživatelských jmen a hesel. Azure Active Directory B2C nabízí vaší organizaci lepší způsob integrace správy identit uživatelů do aplikací s využitím zabezpečené, standardizované platformy a rozsáhlé sady rozšiřitelných zásad.

Když použijete Azure Active Directory B2C, můžou si vaši zákazníci zaregistrovat své aplikace pomocí svých stávajících účtů sociálních sítí (Facebook, Google, Amazon, LinkedIn) nebo vytvořením nových přihlašovacích údajů (e-mailová adresa a heslo nebo uživatelského jména a hesla).

#### <a name="device-registration"></a>Registrace zařízení

[Registrace zařízení Azure AD](../../active-directory/devices/overview.md) je základem pro scénáře [podmíněného přístupu](../../active-directory/devices/overview.md) na základě zařízení. Když je zařízení zaregistrované, služba registrace zařízení Azure AD poskytne zařízení identitu, která se používá k ověření zařízení při přihlášení uživatele. Ověřené zařízení a atributy zařízení se pak dají použít k prosazování zásad podmíněného přístupu pro aplikace, které jsou hostované v cloudu i místně.

Při kombinaci s řešením [správy mobilních zařízení (MDM)](https://www.microsoft.com/itshowcase/Article/Content/588/Mobile-device-management-at-Microsoft) , jako je Intune, se atributy zařízení v Azure Active Directory aktualizují o další informace o zařízení. To vám umožní vytvořit pravidla podmíněného přístupu, která vynutily přístup ze zařízení, aby splňovaly vaše standardy zabezpečení a dodržování předpisů.

#### <a name="privileged-identity-management"></a>Privileged Identity Management

[Azure Active Directory (AD) Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md) umožňuje spravovat, řídit a monitorovat vaše privilegované identity a přistupovat k prostředkům v Azure AD i k dalším online služby Microsoftu, jako je Microsoft 365 nebo Microsoft Intune.

Někdy uživatelé potřebují provádět privilegované operace v Azure nebo Microsoft 365ch prostředcích nebo jiných aplikacích SaaS. To často znamená, že organizace musí udělit trvalý privilegovaný přístup ve službě Azure AD. Toto je rostoucí bezpečnostní riziko pro prostředky hostované v cloudu, protože organizace nemůžou dostatečně monitorovat, co dělají uživatelé s oprávněními správce. Kromě toho platí, že pokud dojde k ohrožení bezpečnosti uživatelského účtu s privilegovaným přístupem, může to mít vliv na celkové zabezpečení cloudu. Azure AD Privileged Identity Management pomáhá řešení tohoto rizika.

Azure AD Privileged Identity Management vám umožní:

- Zobrazit uživatele, kteří jsou správci Azure AD

- Povolit přístup pro správu k online službám Microsoftu, jako je například Microsoft 365 a Intune, v čase na vyžádání.

- Získat sestavy o historii přístupu správce a změnách v přiřazeních správce

- Získat výstrahy týkající se přístupu k privilegované roli

#### <a name="identity-protection"></a>Ochrana identit

[Azure AD Identity Protection](../../active-directory/identity-protection/overview-identity-protection.md) je služba zabezpečení, která poskytuje konsolidovanou pohled na detekci rizik a potenciální ohrožení zabezpečení, která mají vliv na identity vaší organizace. Identity Protection používá funkce detekce anomálií stávajících Azure Active Directory (k dispozici prostřednictvím sestav aktivit neobvyklé Azure AD) a přináší nové typy detekce rizika, které mohou detekovat anomálie v reálném čase.

## <a name="secure-resource-access"></a>Zabezpečený přístup k prostředkům

Řízení přístupu v Azure začíná z perspektivy fakturace. Vlastník účtu Azure, ke kterému se přistupoval při návštěvě [centrum účtů Azure](https://account.windowsazure.com/subscriptions), je správce účtu (AA). Předplatná jsou kontejner pro fakturaci, ale také slouží jako hranice zabezpečení: každé předplatné má správce služby (SA), který umožňuje přidávat, odebírat a upravovat prostředky Azure v tomto předplatném pomocí Azure Portal. Výchozím přidružením zabezpečení nového předplatného je AA, ale AA může změnit SA v Centrum účtů Azure.

![Zabezpečený přístup k prostředkům v Azure](./media/technical-capabilities/azure-security-technical-capabilities-fig3.png)

Předplatná také mají přidružení s adresářem. Adresář definuje skupinu uživatelů. Můžou to být uživatelé z pracovní nebo školní služby, která adresář vytvořila, nebo můžou být externími uživateli (tj. účty Microsoft). K předplatným jsou přístupná podmnožina těch uživatelů adresáře, kteří byli přiřazeni jako správce služeb (SA) nebo Co-Administrator (CA); Jedinou výjimkou je to, že účty Microsoft (dříve Windows Live ID) se dají přiřadit jako SA nebo CA, aniž by se musely v adresáři vyskytovat.

Společnosti zaměřené na zabezpečení by se měly soustředit na to, aby zaměstnanci měli přesně potřebná oprávnění. Příliš mnoho oprávnění může zpřístupnit účet pro útočníky. Příliš málo oprávnění znamená, že zaměstnanci nemůžou svou práci efektivně dělat. [Řízení přístupu na základě role Azure (Azure RBAC)](../../role-based-access-control/overview.md) pomáhá řešit tento problém tím, že nabízí vysoce odstupňovanou správu přístupu pro Azure.

![Zabezpečený přístup k prostředkům](./media/technical-capabilities/azure-security-technical-capabilities-fig4.png)

Pomocí Azure RBAC můžete oddělit povinnosti v rámci týmu a udělit uživatelům jenom takový přístup, který potřebují k výkonu své práce. Místo udělení všech neomezených oprávnění ve vašem předplatném Azure nebo prostředcích můžete povolení jenom určitých akcí. Například pomocí Azure RBAC umožníte jednomu zaměstnanci spravovat virtuální počítače v rámci předplatného, zatímco jiný může spravovat databáze SQL v rámci stejného předplatného.

![Zabezpečený přístup k prostředkům pomocí Azure RBAC](./media/technical-capabilities/azure-security-technical-capabilities-fig5.png)

## <a name="data-security-and-encryption"></a>Zabezpečení a šifrování dat

Jedním z klíčů k ochraně dat v cloudu je monitorování účtů možných stavů, ve kterých se data mohou vyskytnout, a to, jaké ovládací prvky jsou k dispozici pro daný stav. V případě osvědčených postupů zabezpečení a šifrování dat v Azure se doporučení týkají těchto stavů dat.

- V klidovém formátu: to zahrnuje všechny informace o objektech úložiště, kontejnerech a typech, které existují staticky na fyzickém médiu, jedná se o magnetický nebo optický disk.
- Při přenosu: při přenosu dat mezi součástmi, umístěními nebo programy, například přes síť, v rámci služby Service Bus (z místního prostředí do cloudu a naopak, včetně hybridních připojení, jako je ExpressRoute) nebo během vstupně-výstupního procesu, je považována za probíhající.

### <a name="encryption-at-rest"></a>Šifrování neaktivních uložených dat

Šifrování v klidovém umístění je podrobněji popsáno v tématu [šifrování dat Azure v klidovém umístění](encryption-atrest.md).

### <a name="encryption-in-transit"></a>Šifrování při přenosu

Ochrana dat při přenosu by měla být důležitou součástí strategie ochrany dat. Vzhledem k tomu, že se data pohybují zpátky a zpátky z mnoha míst, je obecně doporučení, že protokoly SSL/TLS vždy používáte k výměně dat napříč různými umístěními. V některých případech možná budete chtít izolovat celý komunikační kanál mezi místní a cloudovou infrastrukturou pomocí virtuální privátní sítě (VPN).

Pro pohyb dat mezi místní infrastrukturou a Azure byste měli zvážit vhodná ochranná opatření, jako je například HTTPS nebo VPN.

Pro organizace, které potřebují zabezpečený přístup z několika pracovních stanic umístěných místně do Azure, použijte [Azure Site-to-Site VPN](../../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md).

Pro organizace, které potřebují zabezpečený přístup z jedné pracovní stanice umístěné místně do Azure, použijte [VPN typu Point-to-site](../../vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal.md).

Větší sady dat lze přesunout pomocí vyhrazeného vysokorychlostního propojení WAN, například [ExpressRoute](https://azure.microsoft.com/services/expressroute/). Pokud se rozhodnete používat ExpressRoute, můžete také šifrovat data na úrovni aplikace pomocí [protokolu SSL/TLS](https://web.archive.org/web/20150221085231/http://support.microsoft.com:80/kb/257591) nebo jiných protokolů pro přidání ochrany.

Pokud spolupracujete s Azure Storage prostřednictvím webu Azure Portal, všechny transakce nastávají prostřednictvím protokolu HTTPS. K interakci s [Azure Storage](https://azure.microsoft.com/services/storage/) a [Azure SQL Database](https://azure.microsoft.com/services/sql-database/)je také možné použít [REST API úložiště](/rest/api/storageservices/) prostřednictvím protokolu HTTPS.

Organizace, které nechrání data při přenosu, jsou náchylnější k [útokům](/previous-versions/office/skype-server-2010/gg195821(v=ocs.14))prostředníkem, [odposlouchávání](/previous-versions/office/skype-server-2010/gg195641(v=ocs.14))a zneužití relace. Tyto útoky mohou představovat první krok k získání přístupu k důvěrným datům.

Další informace o možnosti Azure VPN najdete v článku [plánování a návrh VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md).

### <a name="enforce-file-level-data-encryption"></a>Vynutilo šifrování dat na úrovni souborů

[Azure RMS](/azure/information-protection/what-is-azure-rms) používá k zabezpečení souborů a e-mailů zásady šifrování, identity a autorizace. Azure RMS funguje napříč různými zařízeními – telefony, tablety a počítači, a to ochranou v rámci vaší organizace i mimo vaši organizaci. Tato funkce je možná, protože Azure RMS přidává úroveň ochrany, která zůstává s daty, i když opustí hranice vaší organizace.

Když použijete Azure RMS k ochraně souborů, používáte standardní kryptografii s plnou podporou [FIPS 140-2](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.140-2.pdf). Když využijete Azure RMS pro ochranu dat, máte jistotu, že ochrana zůstává se souborem, i když je zkopírována do úložiště, které není pod kontrolou, jako je například služba cloudového úložiště. Ke stejnému dochází u souborů sdílených e-mailem. soubor je chráněný jako příloha e-mailové zprávy s pokyny, jak otevřít chráněnou přílohu.
Při plánování Azure RMS přijetí doporučujeme následující:

- Nainstalujte [aplikaci sdílení RMS](/azure/information-protection/rms-client/sharing-app-windows). Tato aplikace se integruje s aplikacemi Office instalací doplňku Office tak, aby uživatelé mohli snadno chránit soubory přímo.

- Konfigurace aplikací a služeb pro podporu Azure RMS

- Vytvořte [vlastní šablony](/azure/information-protection/configure-policy-templates) , které odrážejí vaše obchodní požadavky. Příklad: Šablona pro nejdůležitější tajná data, která by se měla použít ve všech důležitých e-mailech souvisejících s tajnými klíči.

Organizace, které mají slabý nárok na [klasifikaci dat](https://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) a ochranu souborů, můžou být náchylnější k úniku dat. Bez správné ochrany souborů organizace nebudou moct získávat obchodní přehledy, sledovat zneužití a zabránit škodlivému přístupu k souborům.

> [!Note]
> Další informace o Azure RMS najdete v článku [Začínáme v Azure Rights Management](/azure/information-protection/requirements).

## <a name="secure-your-application"></a>Zabezpečení aplikace
Zatímco Azure zodpovídá za zabezpečení infrastruktury a platformy, na které běží vaše aplikace, je vaše zodpovědnost za zabezpečení vaší aplikace. Jinými slovy, je nutné zajistit vývoj, nasazení a správu kódu a obsahu aplikace zabezpečeným způsobem. Bez toho může být kód aplikace nebo obsah stále zranitelný vůči hrozbám.

### <a name="web-application-firewall"></a>Firewall webových aplikací
[Firewall webových aplikací (WAF)](../../web-application-firewall/ag/ag-overview.md) je funkce [Application Gateway](../../application-gateway/overview.md) , která poskytuje centralizovanou ochranu webových aplikací před běžným zneužitím a ohrožením zabezpečení.

Firewall webových aplikací je založený na základě [základní sady pravidel OWASP](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.0 nebo 2.2.9. Webové aplikace se čím dál častěji stávají cílem škodlivých útoků, které zneužívají běžně známé chyby zabezpečení. Mezi tyto běžné typy zneužití patří mimo jiné například útoky prostřednictvím injektáže SQL nebo skriptování mezi weby. Předcházet takovým útokům v kódu aplikace může být náročné a může vyžadovat pečlivou údržbu, opravy a monitorování několika vrstev topologie aplikace. Centralizovaný firewall webových aplikací značně zjednodušuje správu zabezpečení a nabízí správcům lepší ochranu aplikací před hrozbami neoprávněného vniknutí. Řešení Firewall webových aplikací (WAF) může také rychleji reagovat na ohrožení zabezpečení, protože opravuje známé chyby zabezpečení v centrálním umístění, namísto zabezpečování jednotlivých webových aplikací. Stávající aplikační brány je možné jednoduše převést na aplikační brány doplněné webovým aplikačním firewallem.

Mezi běžné webové zranitelnosti, proti kterým firewall webových aplikací chrání, patří tyto:

- Ochrana před útoky prostřednictvím injektáže SQL.

- Ochrana před skriptováním mezi weby.

- Ochrana před běžnými webovými útoky, jako je například injektáž příkazů, pronášení požadavků HTTP, rozdělování odpovědí protokolu HTTP a útok pomocí vložení vzdáleného souboru.

- Ochrana před narušením protokolu HTTP.

- Ochrana před anomáliemi protokolu HTTP, jako například chybějící údaj user-agent hostitele nebo hlavičky Accept.

- Ochrana před roboty, prohledávacími moduly a skenery.

- Detekce běžných neobvyklých konfigurací aplikací (tj. Apache, IIS atd.)

> [!Note]
> Podrobnější seznam pravidel a jejich ochrany najdete v následujících [základních sadách pravidel](../../web-application-firewall/ag/ag-overview.md):

Azure také nabízí několik snadno použitelných funkcí, které vám pomůžou zabezpečit příchozí i odchozí provoz vaší aplikace. Azure taky zákazníkům pomáhá zabezpečit svůj kód aplikace tím, že poskytuje externě poskytované funkce pro kontrolu ohrožení zabezpečení vaší webové aplikace.

- [Nastavení Azure Active Directory ověřování pro vaši aplikaci](https://azure.microsoft.com/blog/azure-websites-authentication-authorization/)

- [Zabezpečení provozu do aplikace povolením protokolu TLS (Transport Layer Security) – HTTPS](../../app-service/configure-ssl-bindings.md)

  - [Vynutit veškerý příchozí provoz přes připojení HTTPS](http://microsoftazurewebsitescheatsheet.info/)

  - [Povolit striktní přenosové zabezpečení (HSTS)](http://microsoftazurewebsitescheatsheet.info/#enable-http-strict-transport-security-hsts)

- [Omezení přístupu k aplikaci na základě IP adresy klienta](http://microsoftazurewebsitescheatsheet.info/#filtering-traffic-by-ip)

- [Omezení přístupu k aplikaci podle chování klienta – četnost požadavků a souběžnost](http://microsoftazurewebsitescheatsheet.info/#dynamic-ip-restrictions)

- [Prohledávání kódu webové aplikace pro chyby zabezpečení pomocí kontroly zabezpečení TINFOIL](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/)

- [Konfigurace vzájemného ověřování TLS pro vyžadování klientských certifikátů pro připojení k vaší webové aplikaci](../../app-service/app-service-web-configure-tls-mutual-auth.md)

- [Konfigurace klientského certifikátu pro použití v aplikaci pro zabezpečené připojení k externím prostředkům](https://azure.microsoft.com/blog/using-certificates-in-azure-websites-applications/)

- [Odeberte standardní hlavičky serveru, aby nedocházelo k zobrazování nástrojů proti otiskům prstů vaší aplikace.](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/)

- [Bezpečně připojte svoji aplikaci k prostředkům v privátní síti pomocí sítě VPN typu Point-to-site.](../../app-service/web-sites-integrate-with-vnet.md)

- [Bezpečně připojte svoji aplikaci k prostředkům v soukromé síti pomocí Hybrid Connections](../../app-service/app-service-hybrid-connections.md)

Azure App Service používá stejné antimalwarové řešení, které používá Azure Cloud Services a Virtual Machines. Další informace o tomto tématu najdete v naší [dokumentaci k antimalwaru](antimalware.md).

## <a name="secure-your-network"></a>Zabezpečení sítě
Microsoft Azure obsahuje robustní síťovou infrastrukturu pro podporu požadavků vaší aplikace a připojení služby. Mezi prostředky umístěnými v Azure, mezi místními a hostovanými prostředky Azure a a z Internetu a Azure se může připojit k síti.

[Síťová infrastruktura Azure](/previous-versions/azure/virtual-machines/windows/infrastructure-example) umožňuje zabezpečeně propojit prostředky Azure s [virtuálními sítěmi (virtuální sítě)](../../virtual-network/virtual-networks-overview.md). Virtuální síť je reprezentace vaší vlastní sítě v cloudu. Virtuální síť je logická izolace cloudové sítě Azure vyhrazené pro vaše předplatné. Virtuální sítě můžete propojit s vašimi místními sítěmi.

![Zabezpečení sítě (ochrana)](./media/technical-capabilities/azure-security-technical-capabilities-fig6.png)

Pokud potřebujete základní řízení přístupu na úrovni sítě (na základě IP adresy a protokolů TCP nebo UDP), můžete použít [skupiny zabezpečení sítě](../../virtual-network/virtual-network-vnet-plan-design-arm.md). Skupina zabezpečení sítě (NSG) je základní bránou firewall pro filtrování paketů a umožňuje řídit přístup na základě [5 řazených kolekcí členů](https://www.techopedia.com/definition/28190/5-tuple).

Síť Azure podporuje možnost přizpůsobení chování směrování pro síťový provoz ve virtuálních sítích Azure. To můžete provést tak, že nakonfigurujete [trasy definované uživatelem](../../virtual-network/virtual-networks-udr-overview.md) v Azure.

[Vynucené tunelování](https://www.petri.com/azure-forced-tunneling) je mechanismus, který můžete použít k zajištění toho, aby vaše služby neumožňovaly iniciovat připojení k zařízením v Internetu.

Azure podporuje vyhrazené připojení WAN Link k vaší místní síti a Virtual Network Azure s [ExpressRoute](../../expressroute/expressroute-introduction.md). Propojení mezi Azure a vaší lokalitou používá vyhrazené připojení, které nepřekračuje veřejný Internet. Pokud je vaše aplikace Azure spuštěná v několika datových centrech, můžete použít [Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md) k směrování požadavků od uživatelů, které jsou v jednotlivých instancích aplikace inteligentně. Provoz můžete směrovat i do služeb, které neběží v Azure, pokud jsou dostupné z Internetu.

## <a name="virtual-machine-security"></a>Zabezpečení virtuálních počítačů

[Azure Virtual Machines](../../virtual-machines/index.yml) umožňuje flexibilním způsobem nasadit široké škály výpočetních řešení. Díky podpoře pro Microsoft Windows, Linux, Microsoft SQL Server, Oracle, IBM, SAP a Azure BizTalk Services můžete nasadit libovolné úlohy a jakýkoli jazyk na téměř jakémkoli operačním systému.

S Azure můžete použít [antimalwarový software](antimalware.md) od dodavatelů zabezpečení, jako jsou Microsoft, Symantec, Trend Micro a Kaspersky, a chránit tak své virtuální počítače před škodlivými soubory, adwarem a dalšími hrozbami.

Microsoft Antimalware pro Azure Cloud Services a Virtual Machines je schopnost ochrany v reálném čase, která pomáhá identifikovat a odebírat viry, spyware a další škodlivý software. Microsoft Antimalware poskytuje konfigurovatelné výstrahy, když se známý škodlivý nebo nežádoucí software pokusí nainstalovat nebo spustit v systémech Azure.

[Azure Backup](../../backup/backup-overview.md) je škálovatelné řešení, které chrání vaše aplikační data s nulovou kapitálovou investicí a minimálními provozními náklady. Chyby aplikací můžou poškodit vaše data a lidské chyby můžou do vašich aplikací zavádět chyby. Díky Azure Backup jsou virtuální počítače se systémem Windows a Linux chráněny.

[Azure Site Recovery](../../site-recovery/site-recovery-overview.md) pomáhá orchestrovat replikaci, převzetí služeb při selhání a obnovení úloh a aplikací tak, aby byly k dispozici ze sekundárního umístění, pokud dojde k výpadku vašeho primárního umístění.

## <a name="ensure-compliance-cloud-services-due-diligence-checklist"></a>Zajištění dodržování předpisů: kontrolní seznam pro cloudové služby je náležitý

Společnost Microsoft vyvinula [Cloud Services kontrolní seznam pro náležité opatrnosti](https://aka.ms/cloudchecklist.download) , aby organizacím pomohly při povýšení přesunu do cloudu začít s výkonem. Poskytuje strukturu pro organizaci libovolné velikosti a typu – soukromé firmy a organizace veřejného sektoru, včetně státní správy na všech úrovních a nezisků – k identifikaci jejich vlastního výkonu, služeb, správy dat a cílů zásad správného řízení a požadavků. To jim umožní porovnat nabídky různých poskytovatelů cloudových služeb a nakonec vytvořit základ pro smlouvu o cloudové služby.

Kontrolní seznam poskytuje rozhraní, které zarovnává klauzule podle klauzule s novou mezinárodní normou pro smlouvy o cloudové služby, ISO/IEC 19086. Tato Standard nabízí sjednocenou sadu důležitých informací pro organizace, které jim pomůžou rozhodnout o přijetí cloudu a vytvořit společnou zemi pro porovnání nabídek cloudových služeb.

Kontrolní seznam podporuje důkladné prověřené přesunu do cloudu a poskytuje strukturované doprovodné materiály a jednotný a trvalý přístup pro výběr poskytovatele cloudové služby.

Přijetí do cloudu už nestačí jenom na technologické rozhodnutí. Vzhledem k tomu, že požadavky na kontrolní seznam se týkají všech aspektů organizace, slouží k svolání všech klíčových tvůrců interního rozhodování – CIO a ředitelka zabezpečení informací, jakož i právní specialisty, řízení rizik, zadávání zakázek a dodržování předpisů. Tím se zvyšuje efektivita rozhodovacího procesu a rozhodování při rozhodování, což snižuje pravděpodobnost nepředvídatelného překážeku.

Kromě toho kontrolní seznam:

- Zpřístupňuje klíčová témata pro rozhodování pro rozhodovací pracovníky na začátku procesu přijetí cloudu.

- Podporuje důkladné obchodní diskuze o předpisech a vlastní cíle organizace na ochranu osobních údajů, identifikovatelné osobní údaje (PII) a zabezpečení dat.

- Pomáhá organizacím identifikovat případné problémy, které by mohly ovlivnit cloudový projekt.

- Poskytuje ucelenou sadu otázek se stejnými podmínkami, definicemi, metrikami a dodávkami pro každého poskytovatele a zjednodušuje proces porovnávání nabídek od různých poskytovatelů cloudových služeb.

## <a name="azure-infrastructure-and-application-security-validation"></a>Ověřování zabezpečení infrastruktury a aplikací Azure

[Provozní zabezpečení Azure](operational-security.md) odkazuje na služby, ovládací prvky a funkce, které jsou k dispozici uživatelům pro ochranu svých dat, aplikací a dalších prostředků v Microsoft Azure.

![ověření zabezpečení (detekce)](./media/technical-capabilities/azure-security-technical-capabilities-fig7.png)

Provozní zabezpečení Azure je postavené na platformě, která zahrnuje znalostní bázi s různými možnostmi, které jsou jedinečné pro společnost Microsoft, včetně Microsoft Security Development Lifecycle (SDL), programu Microsoft Security Response Center a podrobného povědomí o kyberbezpečnosti hrozbách.

### <a name="microsoft-azure-monitor"></a>Monitorování Microsoft Azure

[Azure monitor](../../azure-monitor/index.yml) je řešení pro správu IT pro hybridní cloud. Azure Monitor protokoly vám pro cloudovou správu vaší infrastruktury měli možnost použít samostatně nebo pro rozšiřování stávajícího nasazení nástroje System Center.

![Azure Monitor](./media/technical-capabilities/azure-security-technical-capabilities-fig8.png)

Pomocí Azure Monitor můžete spravovat jakoukoli instanci v jakémkoli cloudu, včetně místních, Azure, AWS, Windows serveru, Linux, VMware a OpenStack, s nižšími náklady než konkurenční řešení. Společnost Azure Monitor vytvořená pro cloudový celosvětový svět a nabízí nový přístup ke správě vašeho podniku, který je Nejrychlejším a nejefektivnějším způsobem, jak splnit nové obchodní výzvy a přizpůsobit nové úlohy, aplikace a cloudová prostředí.

### <a name="azure-monitor-logs"></a>Protokoly služby Azure Monitor

[Protokoly Azure monitor](https://azure.microsoft.com/documentation/services/log-analytics) poskytují monitorovací služby shromažďováním dat ze spravovaných prostředků do centrálního úložiště. Tato data mohou zahrnovat události, výkonnostní data nebo vlastní data poskytovaná prostřednictvím rozhraní API. Po získání jsou data dostupná pro výstrahy, analýzu a export.

![Protokoly služby Azure Monitor](./media/technical-capabilities/azure-security-technical-capabilities-fig9.png)

Tato metoda umožňuje konsolidovat data z nejrůznějších zdrojů, takže můžete kombinovat data ze služeb Azure s existujícím místním prostředím. Také jasně odděluje získávání dat od akcí provedených na těchto datech, takže všechny akce jsou dostupné pro všechny druhy dat.

### <a name="azure-security-center"></a>Azure Security Center

[Azure Security Center](../../security-center/security-center-introduction.md) pomáhá předcházet hrozbám, rozpoznávat je a reagovat na ně a nabízí lepší přehled o zabezpečení prostředků Azure a kontrolu nad nimi. Poskytuje integrované bezpečnostní sledování a správu zásad ve vašich předplatných Azure, pomáhá zjišťovat hrozby, kterých byste si jinak nevšimli, a spolupracuje s řadou řešení zabezpečení.

Služba Security Center analyzuje stav zabezpečení vašich prostředků Azure, aby identifikovala potenciální ohrožení zabezpečení. Seznam doporučení vás provede procesem konfigurace potřebných kontrol.

Mezi příklady patří:

- Zřizování antimalwaru, aby se pomohl identifikovat a odebrat škodlivý software

- Konfigurace skupin zabezpečení sítě a pravidel pro řízení provozu do virtuálních počítačů

- Zřizování firewallů webových aplikací, které pomáhají bránit útokům zaměřeným na vaše webové aplikace

- Nasazení chybějících aktualizací systému

- Adresování konfigurací operačního systému, které neodpovídají doporučeným standardním hodnotám

Security Center automaticky shromažďuje, analyzuje a integruje data protokolu z vašich prostředků Azure, sítě a řešení partnerů, jako jsou antimalwarové programy a brány firewall. Při zjištění ohrožení zabezpečení se vytvoří výstraha zabezpečení. Příklady zahrnují zjišťování následujících situací:

- Ohrožené virtuální počítače komunikující se známými škodlivými IP adresami

- Pokročilý malware zjištěný pomocí zasílání zpráv o chybách systému Windows

- Útoky hrubou silou vůči virtuálním počítačům

- Výstrahy zabezpečení z integrovaných antimalwarových programů a bran firewall

### <a name="azure-monitor"></a>Monitorování Azure

[Azure monitor](../../azure-monitor/overview.md) poskytuje odkazy na informace o konkrétních typech prostředků. Nabízí vizualizaci, dotaz, směrování, upozorňování, automatické škálování a automatizaci dat jak z infrastruktury Azure (protokol aktivit), tak z každého jednotlivého prostředku Azure (diagnostické protokoly).

Cloudové aplikace jsou komplexní s mnoha pohybujícími se částmi. Monitorování poskytuje data, která zajistí, že vaše aplikace zůstane v dobrém stavu. Také vám pomůže nastavovat potenciální problémy nebo řešit potíže s předchozími.

![Diagram, který ukazuje, jak můžete pomocí dat monitorování získat podrobné informace o vaší aplikaci.](./media/technical-capabilities/azure-security-technical-capabilities-fig10.png)
Kromě toho můžete k získání podrobných přehledů o vaší aplikaci použít data monitorování. Tato znalostní báze vám může pomoci zvýšit výkon a udržovatelnost aplikace nebo automatizovat akce, které by jinak vyžadovaly ruční zásah.

Auditování zabezpečení sítě je důležité pro detekci ohrožení zabezpečení sítě a zajištění souladu s vaším modelem zabezpečení IT a dodržováním předpisů. Pomocí zobrazení skupiny zabezpečení můžete načíst nakonfigurovanou skupinu zabezpečení sítě a pravidla zabezpečení a také platná pravidla zabezpečení. Pomocí seznamu použitých pravidel můžete určit porty, které jsou otevřené a SS ohrožení zabezpečení sítě.

### <a name="network-watcher"></a>Sledovací proces sítě

[Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) je místní služba, která umožňuje monitorovat a diagnostikovat podmínky na úrovni sítě v, do a z Azure. Nástroje pro diagnostiku a diagnostiku sítě, které jsou dostupné v Network Watcher, vám pomůžou pochopit, diagnostikovat a získávat přehledy vaší sítě v Azure. Tato služba zahrnuje zachytávání paketů, další směrování, ověření toku protokolu IP, zobrazení skupiny zabezpečení a protokoly NSG Flow. Monitorování úrovně scénáře nabízí kompletní pohled na síťové prostředky na rozdíl od monitorování jednotlivých síťových prostředků.

### <a name="storage-analytics"></a>Analýzy úložiště

[Analýza úložiště](/rest/api/storageservices/fileservices/storage-analytics) může ukládat metriky, které zahrnují agregovanou statistiku transakcí a data kapacity týkající se požadavků do služby úložiště. Transakce jsou hlášeny jak na úrovni operace rozhraní API, tak i na úrovni služby úložiště a kapacita se oznamuje na úrovni služby úložiště. Data metriky se dají použít k analýze využití služby úložiště, diagnostice problémů s požadavky provedenými na službu úložiště a ke zlepšení výkonu aplikací, které používají službu.

### <a name="application-insights"></a>Application Insights

[Application Insights](../../azure-monitor/app/app-insights-overview.md) je rozšiřitelná služba správy výkonu aplikací (APM) pro webové vývojáře na různých platformách. Slouží k monitorování živé webové aplikace. Automaticky zjišťuje anomálie ve výkonu. Obsahuje výkonné analytické nástroje, které vám pomohou diagnostikovat problémy a pochopit, co uživatelé s vaší aplikací dělají. Je navržena tak, aby pomáhala průběžně vylepšovat výkon a možnosti využití. Funguje pro aplikace na nejrůznějších platformách, jako je .NET, Node.js a Java EE, hostované místně nebo v cloudu. Integruje se s vaším procesem devOps a má spojovací body k různým vývojářským nástrojům.

Monitoruje tyto parametry:

- **Frekvence požadavků, doby odezvy a míra selhání** – Zjistěte, které stránky jsou nejoblíbenější a v kterou denní dobu a kde jsou vaši uživatelé. Zjistíte, která stránka si vede nejlépe. Pokud se při zvýšení počtu požadavků zvýší i doba odezvy a míra selhání, máte pravděpodobně potíže s prostředky.

- **Míra závislosti, doby odezvy a míra selhání** – Zjistěte, jestli vás nezpomalují externí služby.

- **Výjimky** – analyzuje agregované statistiky nebo si vyberou konkrétní instance a podrobí se k trasování zásobníku a souvisejícím žádostem. Hlásí se výjimky serveru i prohlížeče.

- **Zobrazení a načítání stránek** – Tyto informace hlásí prohlížeče uživatelů.

- **Volání AJAX z webových stránek** – frekvence, doba odezvy a frekvence selhání.

- **Počty uživatelů a relací.**

- **Čítače výkonu** ze serverových počítačů s Windows nebo Linuxem, jako je třeba CPU, paměť a využití sítě.

- **Diagnostika hostitele** z Dockeru nebo Azure.

- **Protokoly trasování diagnostiky** z vaší aplikace – umožňují zjistit korelaci mezi požadavky a událostmi trasování.

- **Vlastní události a metriky** , které napíšete sami v klientském nebo serverovém kódu, abyste mohli sledovat obchodní události, jako jsou prodané položky nebo vyhrané hry.

Infrastrukturu aplikace obvykle tvoří celá řada komponent, může to být třeba virtuální počítač, účet úložiště a virtuální síť nebo webová aplikace, databáze, databázový server a služby jiných výrobců. Tyto komponenty nevidíte jako samostatné entity, ale jako související a vzájemně provázané části jedné entity. Chcete je nasadit, spravovat a monitorovat jako skupinu. [Azure Resource Manager](../../azure-resource-manager/management/overview.md) vám umožní pracovat s prostředky ve vašem řešení jako se skupinou.

Všechny prostředky pro vaše řešení můžete nasadit, aktualizovat nebo odstranit v rámci jediné koordinované operace. Pro nasazení použijete šablonu a tato šablona může fungovat v různých prostředích, jako například v testovacím, přípravném nebo produkčním prostředí. Resource Manager poskytuje funkce zabezpečení, auditování a označování, které vám po nasazení pomohou prostředky spravovat.

**Výhody použití Resource Manageru**

Resource Manager poskytuje několik výhod:

- Můžete všechny prostředky pro vaše řešení nasadit, spravovat a monitorovat jako skupinu a nemusíte je zpracovávat jednotlivě.

- Můžete svoje řešení opakovaně nasadit v průběhu životního cyklu a mít přitom jistotu, že se prostředky nasadí konzistentně.

- Infrastrukturu můžete spravovat pomocí deklarativních šablon místo skriptů.

- Můžete definovat závislosti mezi prostředky, aby byly nasazeny ve správném pořadí.

- Řízení přístupu můžete použít pro všechny služby ve vaší skupině prostředků, protože řízení přístupu na základě role Azure (RBAC) je nativně integrované do platformy pro správu.

- Můžete označit prostředky pomocí značek a logicky tak uspořádat všechny prostředky ve svém předplatném.

- Můžete zpřehlednit fakturaci svojí organizace zobrazením nákladů na skupinu prostředků, které sdílejí stejnou značku.

> [!Note]
> Resource Manager poskytuje nový způsob nasazení a správy vašich řešení. Pokud jste použili předchozí model nasazení a chcete se dozvědět o změnách, přečtěte si téma [Principy nasazení Správce prostředků a klasického nasazení](../../azure-resource-manager/management/deployment-models.md).

## <a name="next-steps"></a>Další kroky

Další informace o zabezpečení najdete v našich podrobných tématech o zabezpečení:

- [Auditování a protokolování](https://www.microsoft.com/en-us/trustcenter/security/auditingandlogging)

- [Kybernetická](https://www.microsoft.com/en-us/trustcenter/security/cybercrime)

- [Návrh a provozní zabezpečení](https://www.microsoft.com/en-us/trustcenter/security/designopsecurity)

- [Šifrování](https://www.microsoft.com/en-us/trustcenter/security/encryption)

- [Správa identit a přístupu](https://www.microsoft.com/en-us/trustcenter/security/identity)

- [Zabezpečení sítě](https://www.microsoft.com/en-us/trustcenter/security/networksecurity)

- [Řízení rizik](https://www.microsoft.com/en-us/trustcenter/security/threatmanagement)