---
title: Technické možnosti zabezpečení v Azure – Microsoft Azure
description: Úvod ke službám zabezpečení v Azure, které vám pomůžou chránit data, prostředky a aplikace v cloudu.
services: security
documentationcenter: na
author: UnifyCloud
manager: barbkess
editor: TomSh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/31/2019
ms.author: TomSh
ms.openlocfilehash: 61afad1d9994fd703bd8df047d1861baddeae997
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76845342"
---
# <a name="azure-security-technical-capabilities"></a>Technické možnosti zabezpečení Azure
Tento článek obsahuje úvod ke službám zabezpečení v Azure, které vám pomůžou chránit vaše data, prostředky a aplikace v cloudu a splňují bezpečnostní potřeby vaší firmy.

## <a name="azure-platform"></a>Platforma Azure

[Microsoft Azure](https://azure.microsoft.com/overview/what-is-azure/) je cloudová platforma složená z infrastrukturních a aplikačních služeb s integrovanými datovými službami a pokročilými analýzami a vývojářskými nástroji a službami hostovanými v datových centrech veřejného cloudu společnosti Microsoft. Zákazníci používají Azure pro mnoho různých kapacit a scénářů, od základních výpočetních, síťových a úložných služeb až po mobilní služby a služby webových aplikací až po úplné cloudové scénáře, jako je Internet věcí, a můžou být využíváni s open source technologiemi a nasazováni jako hybridní cloud. hostované v rámci datového centra zákazníka. Azure poskytuje cloudovou technologii jako stavební bloky, které firmám pomáhají šetřit náklady, rychle inovovat a proaktivně spravovat systémy. Když budete stavět na nebo migrovat IT prostředky na poskytovatele cloudu, spoléháte se na schopnosti této organizace chránit vaše aplikace a data pomocí služeb a ovládacích prvků, které poskytují pro správu zabezpečení vašich cloudových prostředků.

Microsoft Azure je jediný poskytovatel cloud computingu, který nabízí zabezpečenou a konzistentní aplikační platformu a infrastrukturu jako službu, aby týmy mohly pracovat v rámci různých cloudových dovedností a úrovní složitosti projektů s integrovanými datovými službami. a analýzy, které odhalují inteligenci z dat, ať už existují kdekoli, napříč platformami Microsoftu i jiných společností než Microsoft, otevřenými architekturami a nástroji, které poskytují možnost integrace cloudu s místními i nasazením cloudových služeb Azure v rámci místních datových center. V rámci Microsoft Trusted Cloudu se zákazníci spoléhají na Azure, protože podporují organizace v cloudu, které jsou špičkové, pokud jde o špičkové zabezpečení, spolehlivost, dodržování předpisů, ochranu osobních údajů a rozsáhlou síť lidí, partnerů a procesů.

S Microsoft Azure můžete:

- Urychlete inovace s cloudem.

- Výkon obchodních rozhodnutí & aplikací s přehledy.

- Vytvářejte volně a nasazujte kdekoli.

- Chránit jejich podnikání.

## <a name="security-technical-capabilities-to-fulfil-your-responsibility"></a>Bezpečnostní technické možnosti pro splnění vaší odpovědnosti

Microsoft Azure poskytuje služby, které vám pomohou splnit vaše požadavky na zabezpečení, ochranu osobních údajů a dodržování předpisů. Následující obrázek pomáhá vysvětlit různé služby Azure, které jsou k dispozici pro vytvoření zabezpečené a kompatibilní aplikační infrastruktury založené na oborových standardech.

![Dostupné bezpečnostní technické možnosti- Velký obraz](./media/technical-capabilities/azure-security-technical-capabilities-fig1.png)

## <a name="manage-and-control-identity-and-user-access"></a>Správa a řízení identity a přístupu uživatelů

Azure pomáhá chránit obchodní a osobní údaje tím, že vám umožňuje spravovat identity uživatelů a přihlašovací údaje a řídit přístup.

### <a name="azure-active-directory"></a>Azure Active Directory

Řešení microsoftu pro správu identit a přístupu pomáhají IT chránit přístup k aplikacím a prostředkům v podnikovém datovém centru a v cloudu a umožňují další úrovně ověřování, jako je vícefaktorové ověřování a podmíněný přístup. Politiky. Monitorování podezřelých aktivit prostřednictvím pokročilého generování sestav zabezpečení, auditování a upozorňování pomáhá zmírňovat potenciální problémy se zabezpečením. [Azure Active Directory Premium](../../active-directory/active-directory-whatis.md) poskytuje jednotné přihlašování tisícům cloudových aplikací a přístup k webovým aplikacím, které provozujete místně.

Výhody zabezpečení služby Azure Active Directory (Azure AD) zahrnují možnost:

- Umožňuje vytvořit a spravovat jednu identitu pro každého uživatele v celém podniku a přitom zajistit synchronizaci uživatelů, skupin a zařízení.

- Poskytněte přístup k aplikacím s jedním přihlášením, včetně tisíců předem integrovaných aplikací SaaS.

- Zajistit zabezpečení přístupu k aplikacím tím, že u místních i cloudových aplikací budete vynucovat vícefaktorové ověřování na základě pravidel.

- Zřizování zabezpečeného vzdáleného přístupu k místním webovým aplikacím prostřednictvím proxy aplikací Azure AD.

[Portál Azure Active Directory](https://aad.portal.azure.com/) je k dispozici jako součást portálu Azure. Na tomto řídicím panelu můžete získat přehled o stavu organizace a snadno spravovat adresář, uživatele nebo přístup k aplikacím.

![Azure Active Directory](./media/technical-capabilities/azure-security-technical-capabilities-fig2.png)

Základní možnosti správy identit Azure:

- Jednotné přihlašování

- Ověřování pomocí služby Multi-Factor Authentication

- Monitorování zabezpečení, výstrahy a sestavy založené na strojovém učení

- Správa identit y a přístupu spotřebitelů

- Registrace zařízení

- Privileged Identity Management

- Ochrana identit

#### <a name="single-sign-on"></a>Jednotné přihlašování

[Jednotné přihlašování (SSO)](https://azure.microsoft.com/documentation/videos/overview-of-single-sign-on/) znamená mít přístup ke všem aplikacím a prostředkům, které potřebujete k podnikání, přihlášením pouze jednou pomocí jednoho uživatelského účtu. Po přihlášení máte přístup ke všem aplikacím, které potřebujete, aniž byste museli podruhé ověřovat (například zadávat heslo).

Mnoho organizací spoléhá na software jako na aplikace služby (SaaS), jako jsou Office 365, Box a Salesforce, pro produktivitu koncových uživatelů. Historicky pracovníci IT potřebovali individuálně vytvářet a aktualizovat uživatelské účty v každé aplikaci SaaS a uživatelé si museli pamatovat heslo pro každou aplikaci SaaS.

[Azure AD rozšiřuje místní službu Active Directory do cloudu](../../active-directory/manage-apps/what-is-single-sign-on.md)a umožňuje uživatelům používat svůj primární účet organizace nejen k přihlášení k jejich zařízením a prostředkům společnosti, které jsou připojeny k doméně, ale také ke všem webovým a SaaS aplikacím potřebným pro jejich práci.

Nejen, že uživatelé nemusí spravovat více sad uživatelských jmen a hesel, přístup k aplikacím může být automaticky zřízen nebo zrušen na základě organizačních skupin a jejich postavení jako zaměstnance. [Azure AD zavádí zabezpečení a řízení přístupu,](../../active-directory/active-directory-enterprise-apps-manage-sso.md) které vám umožní centrálně spravovat přístup uživatelů napříč aplikacemi SaaS.

#### <a name="multi-factor-authentication"></a>Ověřování pomocí služby Multi-Factor Authentication

[Azure Multi-Factor Authentication (MFA)](../../active-directory/authentication/multi-factor-authentication.md) je metoda ověřování, která vyžaduje použití více než jedné metody ověření a přidá kritickou druhou vrstvu zabezpečení pro přihlášení uživatelů a transakce. [Vícefaktorové ověřování pomáhá chránit](../../active-directory/authentication/concept-mfa-howitworks.md) přístup k datům a aplikacím a zároveň uspokojovat požadavky uživatelů na jednoduchý proces přihlášení. Poskytuje silné ověřování prostřednictvím řady možností ověření – telefonního hovoru, textové zprávy nebo oznámení nebo ověřovacího kódu mobilní aplikace a tokenů OAuth třetích stran.

#### <a name="security-monitoring-alerts-and-machine-learning-based-reports"></a>Monitorování zabezpečení, výstrahy a sestavy založené na strojovém učení

Monitorování zabezpečení a výstrahy a sestavy založené na strojovém učení, které identifikují nekonzistentní přístupové vzorce, vám mohou pomoci chránit vaši firmu. Pomocí sestav přístupu a využití služby Azure Active Directory můžete získat přehled o integritě a zabezpečení adresáře vaší organizace. Pomocí těchto informací může správce adresáře lépe určit, kde mohou být možná bezpečnostní rizika, aby mohli odpovídajícím způsobem plánovat zmírnění těchto rizik.

Na webu Azure Portal nebo prostřednictvím [portálu Azure Active Directory](https://aad.portal.azure.com/)jsou [sestavy](../../active-directory/active-directory-reporting-azure-portal.md) rozděleny do kategorií následujícími způsoby:

- Sestavy anomálií – obsahují signaci událostí, které jsme zjistili, že jsou anomální. Naším cílem je upozornit vás na takovou činnost a umožnit vám rozhodnout se, zda je událost podezřelá.

- Integrované sestavy aplikací – poskytují přehled o tom, jak se cloudové aplikace používají ve vaší organizaci. Azure Active Directory nabízí integraci s tisíci cloudových aplikací.

- Zprávy o chybách – označují chyby, které mohou nastat při zřizování účtů do externích aplikací.

- Sestavy specifické pro uživatele – zobrazení dat o aktivitě zařízení a přihlášení pro konkrétního uživatele.

- Protokoly aktivit – obsahují záznam všech auditovaných událostí za posledních 24 hodin, posledních 7 dní nebo posledních 30 dní a změny aktivity skupiny a aktivitu resetování hesla a registrace.

#### <a name="consumer-identity-and-access-management"></a>Správa identit y a přístupu spotřebitelů

[Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) je vysoce dostupná globální služba správy identit pro aplikace orientované na spotřebitele, která se škáluje na stovky milionů identit. Dá se integrovat do mobilních i webových platforem. Zákazníci se můžou ke všem vašim aplikacím přihlašovat přes přizpůsobitelné prostředí pomocí svých existujících účtů v sociálních sítích nebo pomocí nově vytvořených přihlašovacích údajů.

V minulosti by vývojáři aplikací, kteří se chtěli [zaregistrovat a přihlásit spotřebitele](../../active-directory-b2c/overview.md) do svých aplikací, napsali svůj vlastní kód. A použili by místní databáze nebo systémy k ukládání uživatelských jmen a hesel. Azure Active Directory B2C nabízí vaší organizaci lepší způsob, jak integrovat správu identit spotřebitelů do aplikací pomocí zabezpečené platformy založené na standardech a velké sady rozšiřitelných zásad.

Když používáte Azure Active Directory B2C, vaši spotřebitelé se můžou zaregistrovat do vašich aplikací pomocí svých stávajících účtů na sociálních sítích (Facebook, Google, Amazon, LinkedIn) nebo vytvořením nových přihlašovacích údajů (e-mailová adresa a heslo nebo uživatelské jméno a heslo).

#### <a name="device-registration"></a>Registrace zařízení

[Registrace zařízení Azure AD](../../active-directory/devices/overview.md) je základem pro scénáře [podmíněného přístupu](../../active-directory/devices/overview.md) založené na zařízení. Když je zařízení registrované, registrace zařízení Azure AD poskytuje zařízení s identitou, která se používá k ověření zařízení při přihlášení uživatele. Ověřené zařízení a atributy zařízení pak lze použít k vynucení zásad podmíněného přístupu pro aplikace, které jsou hostované v cloudu a místně.

V kombinaci s řešením [pro správu mobilních zařízení (MDM),](https://www.microsoft.com/itshowcase/Article/Content/588/Mobile-device-management-at-Microsoft) jako je Intune, jsou atributy zařízení ve službě Azure Active Directory aktualizovány o další informace o zařízení. To vám umožní vytvořit pravidla podmíněného přístupu, která vynucují přístup ze zařízení tak, aby splňovala vaše standardy zabezpečení a dodržování předpisů.

#### <a name="privileged-identity-management"></a>Privileged Identity Management

[Správa privilegovaných identit Azure Active Directory (AD)](../../active-directory/privileged-identity-management/pim-configure.md) umožňuje spravovat, řídit a monitorovat vaše privilegované identity a přístup k prostředkům ve službě Azure AD i dalším online službám Microsoftu, jako je Office 365 nebo Microsoft Intune.

Někdy uživatelé potřebují provádět privilegované operace v Azure nebo Office 365 prostředky nebo jiné aplikace SaaS. To často znamená, že organizace jim musí poskytnout trvalý privilegovaný přístup ve službě Azure AD. Toto je rostoucí bezpečnostní riziko pro prostředky hostované v cloudu, protože organizace nemohou dostatečně sledovat, co tito uživatelé dělají s jejich oprávněními správce. Navíc pokud dojde k ohrožení uživatelského účtu s privilegovaným přístupem, může jedno porušení ovlivnit jejich celkové zabezpečení cloudu. Azure AD Privilegované Správa identit pomáhá vyřešit toto riziko.

Správa privilegovaných identit Azure AD umožňuje:

- Podívejte se, kteří uživatelé jsou správci Azure AD

- Povolení přístupu pro správu na vyžádání ,just in time" pro správu ke službám Microsoft Online Services, jako jsou Office 365 a Intune

- Získání sestav o historii přístupu správce a změnách v přiřazeních správců

- Získání upozornění na přístup k privilegované roli

#### <a name="identity-protection"></a>Ochrana identit

[Azure AD Identity Protection](../../active-directory/identity-protection/overview.md) je služba zabezpečení, která poskytuje konsolidované zobrazení detekce rizik a potenciální chod chyb zabezpečení ovlivňující identity vaší organizace. Ochrana identit využívá stávající funkce detekce anomálií služby Azure Active Directory (dostupné prostřednictvím sestav anomálních aktivit služby Azure AD) a zavádí nové typy detekce rizik, které dokáží detekovat anomálie v reálném čase.

## <a name="secure-resource-access"></a>Zabezpečený přístup k prostředkům

Řízení přístupu v Azure začíná z hlediska fakturace. Vlastníkem účtu Azure, ke kterým se přistupuje na webu [Azure Account Center](https://account.windowsazure.com/subscriptions), je správce účtu (AA). Předplatná jsou kontejner pro fakturaci, ale fungují také jako hranice zabezpečení: každé předplatné má správce služeb (SA), který může přidávat, odebírat a upravovat prostředky Azure v tomto předplatném pomocí portálu Azure. Výchozí sa nového předplatného je AA, ale AA můžete změnit sa v Centru účtů Azure.

![Přístup k zabezpečeným prostředkům v Azure](./media/technical-capabilities/azure-security-technical-capabilities-fig3.png)

Odběry mají také přidružení k adresáři. Adresář definuje sadu uživatelů. Mohou to být uživatelé z práce nebo školy, kteří adresář vytvořili, nebo externí uživatelé (to znamená účty Microsoft). Odběry jsou přístupné podmnožinou uživatelů adresářů, kteří byli přiřazeni jako správce služeb (SA) nebo spolusprávce (CA); Jedinou výjimkou je, že z důvodu staršíverze účtu Microsoft (dříve Windows Live ID) lze přiřadit jako sa nebo CA, aniž by byl přítomen v adresáři.

Společnosti orientované na zabezpečení by se měly zaměřit na to, aby zaměstnancům poskytly přesná oprávnění, která potřebují. Příliš mnoho oprávnění může vystavit účet útočníkům. Příliš málo oprávnění znamená, že zaměstnanci nemohou pracovat efektivně. [Azure Řízení přístupu na základě rolí (RBAC)](../../role-based-access-control/overview.md) pomáhá tento problém tím, že nabízí jemně odstupňované správy přístupu pro Azure.

![Přístup k zabezpečeným prostředkům](./media/technical-capabilities/azure-security-technical-capabilities-fig4.png)

Pomocí řízení přístupu na základě role můžete povinnosti v rámci týmu oddělit a udělit uživatelům jenom takový přístup, který potřebují k výkonu své práce. Místo toho, aby všichni neomezená oprávnění ve vašem předplatném Azure nebo prostředky, můžete povolit pouze určité akce. Například pomocí RBAC unechat jednoho zaměstnance spravovat virtuální počítače v předplatném, zatímco jiný můžete spravovat databáze SQL v rámci stejného předplatného.

![Přístup k zabezpečeným prostředkům v Azure (RBAC)](./media/technical-capabilities/azure-security-technical-capabilities-fig5.png)

## <a name="data-security-and-encryption"></a>Zabezpečení a šifrování dat

Jedním z klíčů k ochraně dat v cloudu je účetnictví pro možné stavy, ve kterých může dojít k datům a jaké ovládací prvky jsou k dispozici pro tento stav. Pro azure zabezpečení dat a šifrování osvědčené postupy doporučení se kolem následujících stavů dat.

- Klid: To zahrnuje všechny objekty úložiště informací, kontejnery a typy, které existují staticky na fyzickém médiu, ať už je to magnetický nebo optický disk.

- Přenos mezi součástmi, umístěními nebo programy, například přes síť, přes servisní sběrnici (z místního do cloudu a naopak, včetně hybridních připojení, jako je ExpressRoute), nebo během procesu vstupu a výstupu, se považuje za pohyb.

### <a name="encryption-at-rest"></a>Šifrování v klidovém stavu

Chcete-li dosáhnout šifrování v klidovém stavu, proveďte každý z následujících akcí:

Podpora alespoň jeden z doporučených modelů šifrování podrobně popsaných v následující tabulce pro šifrování dat.

| Šifrovací modely |  |  |  |
| ----------------  | ----------------- | ----------------- | --------------- |
| Šifrování serveru | Šifrování serveru | Šifrování serveru | Šifrování klienta
| Šifrování na straně serveru pomocí klíčů spravovaných službou | Šifrování na straně serveru pomocí klíčů spravovaných zákazníkem v trezoru klíčů Azure | Šifrování na straně serveru pomocí místních klíčů spravovaných zákazníkem |
| • Zprostředkovatelé prostředků Azure provádějí operace šifrování a dešifrování <br> • Microsoft spravuje klíče <br>• Plná funkce cloudu | • Zprostředkovatelé prostředků Azure provádějí operace šifrování a dešifrování<br>• Zákazník ovládá klíče prostřednictvím azure key vaultu<br>• Plná funkce cloudu | • Zprostředkovatelé prostředků Azure provádějí operace šifrování a dešifrování <br>• Zákazník ovládá klíče v místním prostředí <br> • Plná funkce cloudu| • Služby Azure nevidí dešifrovaná data <br>• Zákazníci uchovávají klíče v místním prostředí (nebo v jiných zabezpečených obchodech). Klíče nejsou dostupné pro služby Azure <br>• Snížená funkce cloudu|

### <a name="enabling-encryption-at-rest"></a>Povolení šifrování v klidovém stavu

**Identifikace všech míst, vaše data v obchodech**

Cílem šifrování v klidovém stavu je šifrovat všechna data. Tím se eliminuje možnost chybějících důležitých dat nebo všech trvalých umístění. Výčet všech dat uložených vaší aplikací.

> [!Note]
> Nejen "data aplikace" nebo "PII", ale veškeré údaje týkající se aplikace, včetně metadat účtu (mapování předplatného, informace o smlouvě, PII).

Zvažte, jaké obchody používáte k ukládání dat. Například:

- Externí úložiště (například SQL Azure, Document DB, HDInsights, Data Lake atd.)

- Dočasné úložiště (všechny místní mezipaměti, která obsahuje data klienta)

- Mezipaměť v paměti (může být vložena do stránkovacího souboru.)

### <a name="leverage-the-existing-encryption-at-rest-support-in-azure"></a>Využijte stávající podporu šifrování v klidovém stavu v Azure

Pro každý obchod, který používáte, využijte stávající šifrování v klidovém stavu.

- Azure Storage: Viz [Šifrování služby Azure Storage Service pro data v klidovém stavu](../../storage/common/storage-service-encryption.md),

- SQL Azure: Viz [Transparentní šifrování dat (TDE), SQL vždy šifrované](https://msdn.microsoft.com/library/mt163865.aspx)

- Virtuální počítač & místní diskové úložiště[(Azure Disk Encryption)](../azure-security-disk-encryption-overview.md)

Pro virtuální počítače a místní diskové úložiště použijte Azure Disk Encryption tam, kde je to podporované:

#### <a name="iaas"></a>IaaS

Služby s virtuálními počítači IaaS (Windows nebo Linux) by měly používat [Azure Disk Encryption](https://microsoft.sharepoint.com/teams/AzureSecurityCompliance/Security/SitePages/Azure%20Disk%20Encryption.aspx) k šifrování svazků obsahujících zákaznická data.

#### <a name="paas-v2"></a>PaaS v2

Služby spuštěné na PaaS v2 pomocí Service Fabric můžete použít šifrování disku Azure pro sadu škálování virtuálních počítačů [VMSS] k šifrování jejich PaaS v2 virtuálnípočítače.

#### <a name="paas-v1"></a>PaaS v1

Azure Disk Encryption aktuálně není podporována na PaaS v1. Proto je nutné použít šifrování na úrovni aplikace k šifrování trvalých dat v klidovém stavu.  To zahrnuje, ale není omezena na data aplikace, dočasné soubory, protokoly a výpisy stavu systému.

Většina služeb by se měla pokusit využít šifrování zprostředkovatele prostředků úložiště. Některé služby musí provést explicitní šifrování, například jakýkoli trvalý materiál klíče (certifikáty, kořenové / hlavní klíče) musí být uložen v trezoru klíčů.

Pokud podporujete šifrování na straně služby pomocí klíčů spravovaných zákazníkem, musí existovat způsob, jak zákazník klíč k nám získat. Podporovaný a doporučený způsob, jak toho provést integrací s Azure Key Vault (AKV). V takovém případě mohou zákazníci přidávat a spravovat své klíče v úložišti klíčů Azure. Zákazník se může dozvědět, jak používat AKV pomocí [Začínáme s Trezorem klíčů](https://go.microsoft.com/fwlink/?linkid=521402).

Chcete-li integrovat s Azure Key Vault, měli byste přidat kód požádat o klíč od AKV v případě potřeby pro dešifrování.

- Informace o integraci s AKV najdete v [tématu Azure Key Vault – krok za krokem.](https://blogs.technet.microsoft.com/kv/2015/06/02/azure-key-vault-step-by-step/)

Pokud podporujete klíče spravované zákazníkem, je třeba zadat uživatelské uživatelské tlačítko, aby zákazník určil, který trezor klíčů (nebo identifikátor URI trezoru klíčů) má použít.

Vzhledem k tomu, že šifrování v klidovém stavu zahrnuje šifrování dat hostitele, infrastruktury a klienta, ztráta klíčů v důsledku selhání systému nebo škodlivé aktivity může znamenat ztrátu všech šifrovaných dat. Je proto důležité, aby vaše řešení šifrování v klidovém stavu má komplexní příběh zotavení po havárii odolný vůči selhání systému a škodlivé aktivity.

Služby, které implementují šifrování v klidovém stavu, jsou obvykle stále náchylné k šifrovacím klíčům nebo datům, která jsou ponechána nezašifrovaná na hostitelské jednotce (například v stránkovacím souboru hostitelského operačního systému.) Proto služby musí zajistit, že objem hostitele pro jejich služby je šifrován. Pro usnadnění tohoto výpočetního týmu povolil nasazení šifrování hostitele, který používá [Nástroj BitLocker](https://technet.microsoft.com/library/dn306081.aspx) NKP a rozšíření pro službu DCM a agenta k šifrování svazku hostitele.

Většina služeb se implementuje na standardních virtuálních počítačích Azure. Tyto služby by měly získat [šifrování hostitele](../azure-security-disk-encryption-overview.md) automaticky, když to compute povolí. Pro služby spuštěné v clusterech spravovaných výpočetními prostředky je šifrování hostitele povoleno automaticky při zavádění Windows Serveru 2016.

### <a name="encryption-in-transit"></a>Šifrování při přenosu

Ochrana dat při přenosu by měla být nezbytnou součástí vaší strategie ochrany údajů. Vzhledem k tomu, že data se pohybují tam a zpět z mnoha umístění, obecné doporučení je, že vždy používáte protokoly SSL/TLS k výměně dat v různých umístěních. V některých případech můžete chtít izolovat celý komunikační kanál mezi místní a cloudovou infrastrukturou pomocí virtuální privátní sítě (VPN).

Pro přenos dat mezi místní infrastrukturou a Azure byste měli zvážit vhodná ochranná opatření, jako je HTTPS nebo VPN.

Pro organizace, které potřebují zabezpečit přístup z více pracovních stanic umístěných místně do Azure, použijte [Azure site-to-site VPN](../../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md).

Pro organizace, které potřebují zabezpečit přístup z jedné pracovní stanice umístěné místně do Azure, použijte [vpn point-to-site](../../vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal.md).

Větší datové sady lze přesouvat přes vyhrazené vysokorychlostní připojení WAN, například [ExpressRoute](https://azure.microsoft.com/services/expressroute/). Pokud se rozhodnete použít ExpressRoute, můžete také šifrovat data na úrovni aplikace pomocí [SSL/TLS](https://support.microsoft.com/kb/257591) nebo jiných protokolů pro větší ochranu.

Pokud pracujete s Azure Storage prostřednictvím portálu Azure, všechny transakce dojít prostřednictvím protokolu HTTPS. [Rozhraní REST API úložiště](https://msdn.microsoft.com/library/azure/dd179355.aspx) přes protokol HTTPS lze také použít k interakci s [Azure Storage](https://azure.microsoft.com/services/storage/) a Azure [SQL Database](https://azure.microsoft.com/services/sql-database/).

Organizace, které nedokáží chránit data při přenosu, jsou náchylnější k [útokům man-in-the-middle](https://technet.microsoft.com/library/gg195821.aspx), [odposlouchávání](https://technet.microsoft.com/library/gg195641.aspx)a únosu relace. Tyto útoky mohou představovat první krok k získání přístupu k důvěrným datům.

Další informace o možnosti Azure VPN najdete v článku [Plánování a návrh pro bránu VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md).

### <a name="enforce-file-level-data-encryption"></a>Vynucení šifrování dat na úrovni souboru

[Azure RMS](https://technet.microsoft.com/library/jj585026.aspx) používá zásady šifrování, identity a autorizace k zabezpečení souborů a e-mailů. Azure RMS funguje na různých zařízeních – telefonech, tabletech a počítačích tím, že chrání jak ve vaší organizaci, tak mimo vaši organizaci. Tato možnost je možná, protože Azure RMS přidá úroveň ochrany, která zůstává s daty, i když opustí hranice vaší organizace.

Při použití Azure RMS k ochraně souborů, používáte standardní kryptografii s plnou podporou [FIPS 140-2](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.140-2.pdf). Když využíváte Azure RMS pro ochranu dat, máte jistotu, že ochrana zůstane se souborem, i když je zkopírován do úložiště, které není pod kontrolou IT, jako je například služba cloudového úložiště. Totéž platí pro soubory sdílené prostřednictvím e-mailu, soubor je chráněn jako příloha e-mailové zprávy s pokyny, jak otevřít chráněnou přílohu.
Při plánování přijetí Azure RMS doporučujeme následující:

- Nainstalujte [aplikaci pro sdílení služby RMS](https://technet.microsoft.com/library/dn339006.aspx). Tato aplikace se integruje s aplikacemi Office instalací doplňku Office, aby uživatelé mohli snadno chránit soubory přímo.

- Konfigurace aplikací a služeb pro podporu Azure RMS

- Vytvořte [vlastní šablony,](https://technet.microsoft.com/library/dn642472.aspx) které budou odrážet vaše obchodní požadavky. Například: šablona pro přísně tajná data, která by měla být použita ve všech přísně tajných souvisejících e-mailech.

Organizace, které jsou slabé na [klasifikaci dat](https://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) a ochranu souborů může být náchylnější k úniku dat. Bez řádné ochrany souborů nebudou organizace moci získat obchodní přehledy, sledovat zneužití a zabránit škodlivému přístupu k souborům.

> [!Note]
> Další informace o Azure RMS najdete v článku [Začínáme se službou Azure Rights Management](https://technet.microsoft.com/library/jj585016.aspx).

## <a name="secure-your-application"></a>Zabezpečení aplikace
Zatímco Azure je zodpovědný za zabezpečení infrastruktury a platformy, na které vaše aplikace běží, je vaší odpovědností zabezpečit samotnou vaši aplikaci. Jinými slovy je potřeba bezpečně vyvíjet, nasazovat a spravovat kód aplikace a obsah. Bez toho může být kód aplikace nebo obsah stále zranitelný vůči hrozbám.

### <a name="web-application-firewall"></a>Brána firewall webových aplikací
[Brána firewall webových aplikací (WAF)](../../application-gateway/waf-overview.md) je funkce [application gateway,](../../application-gateway/overview.md) která poskytuje centralizovanou ochranu webových aplikací před běžnými zneužitími a chybami zabezpečení.

Firewall webových aplikací je založený na základě [základní sady pravidel OWASP](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.0 nebo 2.2.9. Webové aplikace se čím dál častěji stávají cílem škodlivých útoků, které zneužívají běžně známé chyby zabezpečení. Mezi tyto běžné typy zneužití patří mimo jiné například útoky prostřednictvím injektáže SQL nebo skriptování mezi weby. Předcházet takovým útokům v kódu aplikace může být náročné a může vyžadovat pečlivou údržbu, opravy a monitorování několika vrstev topologie aplikace. Centralizovaný firewall webových aplikací značně zjednodušuje správu zabezpečení a nabízí správcům lepší ochranu aplikací před hrozbami neoprávněného vniknutí. Řešení Firewall webových aplikací (WAF) může také rychleji reagovat na ohrožení zabezpečení, protože opravuje známé chyby zabezpečení v centrálním umístění, namísto zabezpečování jednotlivých webových aplikací. Stávající aplikační brány je možné jednoduše převést na aplikační brány doplněné webovým aplikačním firewallem.

Mezi běžné webové zranitelnosti, proti kterým firewall webových aplikací chrání, patří tyto:

- Ochrana před útoky prostřednictvím injektáže SQL.

- Ochrana před skriptováním mezi weby.

- Ochrana před běžnými webovými útoky, jako je například injektáž příkazů, pronášení požadavků HTTP, rozdělování odpovědí protokolu HTTP a útok pomocí vložení vzdáleného souboru.

- Ochrana před narušením protokolu HTTP.

- Ochrana před anomáliemi protokolu HTTP, jako například chybějící údaj user-agent hostitele nebo hlavičky Accept.

- Ochrana před roboty, prohledávacími moduly a skenery.

- Detekce běžných chybných konfigurací aplikací (tj. Apache, IIS atd.)

> [!Note]
> Podrobnější seznam pravidel a jejich ochrany naleznete v následujících [základních sadách pravidel](../../application-gateway/waf-overview.md):

Azure také poskytuje několik snadno použitelných funkcí, které pomáhají zabezpečit příchozí i odchozí provoz pro vaši aplikaci. Azure také pomáhá zákazníkům zabezpečit kód aplikace tím, že poskytuje externě poskytované funkce pro skenování webových aplikací pro chyby zabezpečení.

- [Nastavení ověřování služby Azure Active Directory pro vaši aplikaci](https://azure.microsoft.com/blog/azure-websites-authentication-authorization/)

- [Zabezpečte provoz do aplikace povolením zabezpečení transportní vrstvy (TLS/SSL) – HTTPS](../../app-service/configure-ssl-bindings.md)

  - [Vynutit veškerý příchozí provoz přes připojení HTTPS](http://microsoftazurewebsitescheatsheet.info/)

  - [Povolit přísnou bezpečnost přenosu (HSTS)](http://microsoftazurewebsitescheatsheet.info/#enable-http-strict-transport-security-hsts)

- [Omezení přístupu k aplikaci pomocí IP adresy klienta](http://microsoftazurewebsitescheatsheet.info/#filtering-traffic-by-ip)

- [Omezte přístup k aplikaci podle chování klienta – četnost požadavků a souběžnost](http://microsoftazurewebsitescheatsheet.info/#dynamic-ip-restrictions)

- [Prohledejte kód webové aplikace, kde najdete chyby zabezpečení pomocí prohledávání zabezpečení tinfoilu](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/)

- [Konfigurace vzájemného ověřování TLS tak, aby vyžadovalo připojení klientských certifikátů k webové aplikaci](../../app-service/app-service-web-configure-tls-mutual-auth.md)

- [Konfigurace klientského certifikátu pro bezpečné připojení k externím prostředkům z vaší aplikace](https://azure.microsoft.com/blog/using-certificates-in-azure-websites-applications/)

- [Odebrání standardních záhlaví serveru, aby se zabránilo nástrojům s otisku otisků prstů aplikace](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/)

- [Bezpečné připojení aplikace k prostředkům v privátní síti pomocí stránky VPN](../../app-service/web-sites-integrate-with-vnet.md)

- [Bezpečné připojení aplikace k prostředkům v privátní síti pomocí hybridních připojení](../../app-service/app-service-hybrid-connections.md)

Služba Azure App Service používá stejné antimalwarové řešení, které používají Cloudové služby Azure a virtuální počítače. Další informace naleznete v naší [antimalwarové dokumentaci](antimalware.md).

## <a name="secure-your-network"></a>Zabezpečení sítě
Microsoft Azure obsahuje robustní síťovou infrastrukturu, která podporuje požadavky na připojení aplikací a služeb. Připojení k síti je možné mezi prostředky umístěnými v Azure, mezi místními a hostovanými prostředky Azure a na internet a Z Internetu a Azure.

[Síťová infrastruktura Azure](../../virtual-machines/windows/infrastructure-example.md) umožňuje bezpečně propojit prostředky Azure pomocí [virtuálních sítí ....](../../virtual-network/virtual-networks-overview.md) Virtuální síť je reprezentace vlastní sítě v cloudu. Virtuální síť je logická izolace cloudové sítě Azure vyhrazené pro vaše předplatné. Virtuální sítě můžete připojit k místním sítím.

![Zabezpečte svou síť (chránit)](./media/technical-capabilities/azure-security-technical-capabilities-fig6.png)

Pokud potřebujete základní řízení přístupu na úrovni sítě (na základě adresy IP a protokolů TCP nebo UDP), můžete použít [skupiny zabezpečení sítě](../../virtual-network/virtual-network-vnet-plan-design-arm.md). Skupina zabezpečení sítě (NSG) je základní stavová brána firewall pro filtrování paketů a umožňuje řídit přístup na základě [5-n-tice](https://www.techopedia.com/definition/28190/5-tuple).

Azure networking podporuje možnost přizpůsobit chování směrování pro síťový provoz ve virtuálních sítích Azure. To lze provést konfigurací [uživatelem definovaných tras](../../virtual-network/virtual-networks-udr-overview.md) v Azure.

[Vynucené tunelové propojení](https://www.petri.com/azure-forced-tunneling) je mechanismus, který můžete použít k zajištění, že vaše služby nejsou povoleny k zahájení připojení k zařízením v Internetu.

Azure podporuje vyhrazené připojení wan propojení k vaší místní síti a virtuální síti Azure s [ExpressRoute](../../expressroute/expressroute-introduction.md). Propojení mezi Azure a vaším webem používá vyhrazené připojení, které nepřejde přes veřejný Internet. Pokud vaše aplikace Azure běží ve více datových centrech, můžete pomocí [Azure Traffic Manageru](../../traffic-manager/traffic-manager-overview.md) inteligentně směrovat požadavky od uživatelů napříč instancemi aplikace. Můžete také směrovat provoz na služby, které nejsou spuštěny v Azure, pokud jsou přístupné z Internetu.

## <a name="virtual-machine-security"></a>Zabezpečení virtuálních počítačů

[Virtuální počítače Azure](../../virtual-machines/index.yml) vám umožní agilně nasadit širokou škálu výpočetních řešení. Díky podpoře microsoft windows, linuxu, microsoft sql serveru, oracle, ibm, sap a služby Azure BizTalk Services můžete nasadit libovolné úlohy a jakýkoli jazyk téměř v jakémkoli operačním systému.

V Azure můžete používat [antimalwarový software](antimalware.md) od dodavatelů zabezpečení, jako jsou Microsoft, Symantec, Trend Micro a Kaspersky, k ochraně vašich virtuálních počítačů před škodlivými soubory, adwarem a dalšími hrozbami.

Microsoft Antimalware pro Cloud Services azure a virtuální počítače je funkce ochrany v reálném čase, která pomáhá identifikovat a odstranit viry, spyware a další škodlivý software. Microsoft Antimalware poskytuje konfigurovatelné výstrahy, když se známý škodlivý nebo nežádoucí software pokusí nainstalovat sám nebo spustit ve vašich systémech Azure.

[Azure Backup](../../backup/backup-overview.md) je škálovatelné řešení, které chrání data aplikací s nulovými kapitálovými investicemi a minimálními provozními náklady. Chyby aplikace mohou poškodit vaše data a lidské chyby mohou zavádět chyby do vašich aplikací. S Azure Backup jsou chráněné vaše virtuální počítače se systémem Windows a Linux.

[Azure Site Recovery](../../site-recovery/site-recovery-overview.md) pomáhá organizovat replikaci, převzetí služeb při selhání a obnovení úloh a aplikací tak, aby byly dostupné ze sekundárního umístění, pokud dojde k primárnímu umístění.

## <a name="ensure-compliance-cloud-services-due-diligence-checklist"></a>Zajištění souladu: Kontrolní seznam hloubkové kontroly cloudových služeb

Společnost Microsoft vyvinula [kontrolní seznam hloubkové kontroly cloudových služeb,](https://aka.ms/cloudchecklist.download) který organizacím pomáhá postupovat s náležitou péčí při zvažení přechodu do cloudu. Poskytuje strukturu pro organizaci všech velikostí a typů – soukromé podniky a organizace veřejného sektoru, včetně státní správy na všech úrovních a neziskových organizací – pro identifikaci jejich vlastních cílů v oblasti výkonnosti, služeb, správy dat a zásad správného řízení a Požadavky. To jim umožňuje porovnat nabídky různých poskytovatelů cloudových služeb, což v konečném důsledku tvoří základ smlouvy o cloudových službách.

Kontrolní seznam poskytuje rámec, který slaďuje klauzuli po klauzuli s novým mezinárodním standardem pro smlouvy o cloudových službách ISO/IEC 19086. Tento standard nabízí organizacím jednotnou sadu aspektů, které jim pomohou při rozhodování o přechodu na cloud a vytvořit společný základ pro porovnání nabídek cloudových služeb.

Kontrolní seznam podporuje důkladně prověřený přesun do cloudu a poskytuje strukturované pokyny a konzistentní a opakovatelný přístup pro výběr poskytovatele cloudových služeb.

Přechod na cloud už není pouhým technologickým rozhodnutím. Vzhledem k tomu, že požadavky na kontrolní seznam se dotýkají všech aspektů organizace, slouží ke svolání všech klíčových interních činitelů s rozhodovací pravomocí – CIO a CISO, jakož i právníků, odborníků na řízení rizik, nákupů a dodržování předpisů. To zvyšuje účinnost rozhodovacího procesu a pozemních rozhodnutí v řádném uvažování, čímž se snižuje pravděpodobnost nepředvídaných zátarasů k přijetí.

Kromě toho kontrolní seznam:

- Zveřejňuje klíčová diskusní témata pro osoby s rozhodovací pravomocí na začátku procesu přechodu na cloud.

- Podporuje důkladné obchodní diskuse o předpisech a vlastních cílech organizace v oblasti ochrany osobních údajů, osobních údajů (PII) a zabezpečení dat.

- Pomáhá organizacím identifikovat všechny potenciální problémy, které by mohly ovlivnit cloudový projekt.

- Poskytuje konzistentní sadu otázek se stejnými termíny, definicemi, metrikami a dodávkami pro každého poskytovatele, aby se zjednodušil proces porovnávání nabídek od různých poskytovatelů cloudových služeb.

## <a name="azure-infrastructure-and-application-security-validation"></a>Ověření zabezpečení infrastruktury Azure a aplikací

[Provozní zabezpečení Azure](operational-security.md) označuje služby, ovládací prvky a funkce, které jsou uživatelům k dispozici pro ochranu jejich dat, aplikací a dalších prostředků v Microsoft Azure.

![ověření zabezpečení (detekce)](./media/technical-capabilities/azure-security-technical-capabilities-fig7.png)

Provozní zabezpečení Azure je postaveno na rozhraní, které zahrnuje znalosti získané prostřednictvím různých funkcí, které jsou jedinečné pro Microsoft, včetně programu Microsoft Security Development Lifecycle (SDL), programu Microsoft Security Response Centre a hluboké povědomí o prostředí kybernetických hrozeb.

### <a name="microsoft-azure-monitor"></a>Microsoft Azure Monitor

[Azure Monitor](../../azure-monitor/index.yml) je řešení správy IT pro hybridní cloud. Protokoly Azure Monitoru, které se používají samostatně nebo k rozšíření stávajícího nasazení System Center, poskytují maximální flexibilitu a kontrolu pro cloudovou správu vaší infrastruktury.

![Azure Monitor](./media/technical-capabilities/azure-security-technical-capabilities-fig8.png)

Pomocí Azure Monitoru můžete spravovat libovolnou instanci v libovolném cloudu, včetně místních, Azure, AWS, Windows Server, Linux, VMware a OpenStack, a to za nižší cenu než konkurenční řešení. Azure Monitor, který je navržen pro svět, který je na prvním místě v cloudu, nabízí nový přístup ke správě vašeho podniku, který je nejrychlejším a nákladově nejefektivnějším způsobem řešení nových obchodních výzev a přizpůsobení nových úloh, aplikací a cloudových prostředí.

### <a name="azure-monitor-logs"></a>Protokoly služby Azure Monitor

[Protokoly Azure Monitor](https://azure.microsoft.com/documentation/services/log-analytics) poskytuje monitorovací služby shromažďováním dat ze spravovaných prostředků do centrálního úložiště. Tato data mohou zahrnovat události, výkonnostní data nebo vlastní data poskytovaná prostřednictvím rozhraní API. Po získání jsou data dostupná pro výstrahy, analýzu a export.

![Protokoly služby Azure Monitor](./media/technical-capabilities/azure-security-technical-capabilities-fig9.png)

Tato metoda umožňuje konsolidovat data z různých zdrojů, takže můžete kombinovat data ze služeb Azure s vaším stávajícím místním prostředím. Také jasně odděluje získávání dat od akcí provedených na těchto datech, takže všechny akce jsou dostupné pro všechny druhy dat.

### <a name="azure-security-center"></a>Azure Security Center

[Azure Security Center](../../security-center/security-center-intro.md) pomáhá předcházet hrozbám, rozpoznávat je a reagovat na ně a nabízí lepší přehled o zabezpečení prostředků Azure a kontrolu nad nimi. Poskytuje integrované bezpečnostní sledování a správu zásad ve vašich předplatných Azure, pomáhá zjišťovat hrozby, kterých byste si jinak nevšimli, a spolupracuje s řadou řešení zabezpečení.

Služba Security Center analyzuje stav zabezpečení vašich prostředků Azure, aby identifikovala potenciální ohrožení zabezpečení. Seznam doporučení vás provede procesem konfigurace potřebných kontrol.

Příklady obsahují:

- Zřizování antimalwaru, aby se pomohl identifikovat a odebrat škodlivý software

- Konfigurace skupin zabezpečení sítě a pravidel pro řízení provozu na virtuálních discích

- Zřizování firewallů webových aplikací, které pomáhají bránit útokům zaměřeným na vaše webové aplikace

- Nasazení chybějících aktualizací systému

- Adresování konfigurací operačního systému, které neodpovídají doporučeným standardním hodnotám

Security Center automaticky shromažďuje, analyzuje a integruje data protokolu z vašich prostředků Azure, sítě a řešení partnerů, jako jsou antimalwarové programy a brány firewall. Při zjištění ohrožení zabezpečení se vytvoří výstraha zabezpečení. Příklady zahrnují zjišťování následujících situací:

- Ohrožení zabezpečení virtuálních počítačů komunikujících se známými škodlivými IP adresami

- Pokročilý malware zjištěný pomocí zasílání zpráv o chybách systému Windows

- Útoky hrubou silou proti virtuálním visutá

- Výstrahy zabezpečení z integrovaných antimalwarových programů a bran firewall

### <a name="azure-monitor"></a>Azure monitor

[Azure Monitor](../../azure-monitor/overview.md) poskytuje odkazy na informace o konkrétních typech prostředků. Nabízí vizualizaci, dotaz, směrování, výstrahy, automatické škálování a automatizaci na datech jak z infrastruktury Azure (protokol aktivit), tak z každého jednotlivého prostředku Azure (diagnostické protokoly).

Cloudové aplikace jsou složité s mnoha pohyblivými částmi. Monitorování poskytuje data k zajištění, že vaše aplikace zůstane v provozu v pořádku stavu. To také pomáhá odvrátit potenciální problémy nebo řešení minulých.

![Azure](./media/technical-capabilities/azure-security-technical-capabilities-fig10.png) Monitor Kromě toho můžete použít data monitorování získat podrobné informace o vaší aplikaci. Tyto znalosti vám mohou pomoci zlepšit výkon nebo udržovatelnost aplikací nebo automatizovat akce, které by jinak vyžadovaly ruční zásah.

Auditování zabezpečení sítě je nezbytné pro zjišťování slabých míst sítě a zajištění souladu s vaším modelem zabezpečení IT a zásadováním regulace. Pomocí zobrazení Skupiny zabezpečení můžete načíst nakonfigurovanou skupinu zabezpečení sítě a pravidla zabezpečení a také účinná pravidla zabezpečení. Pomocí seznamu použitých pravidel můžete určit otevřené porty a zranitelnost sítě ss.

### <a name="network-watcher"></a>Sledování sítě

[Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) je místní služba, která umožňuje monitorovat a diagnostikovat podmínky na úrovni sítě v Azure, do a z Azure. Nástroje pro diagnostiku a vizualizaci sítě, které jsou k dispozici pomocí nástroje Sledování sítě, vám pomohou pochopit, diagnostikovat a získat přehled o vaší síti v Azure. Tato služba zahrnuje sběr paketů, další směrování, ověření toku IP, zobrazení skupiny zabezpečení, protokoly toku skupiny nsg. Monitorování úrovně scénáře poskytuje koncové zobrazení síťových prostředků na rozdíl od monitorování jednotlivých síťových prostředků.

### <a name="storage-analytics"></a>Analýzy úložiště

[Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) můžete ukládat metriky, které zahrnují agregované statistiky transakcí a údaje o kapacitě o požadavcích na službu úložiště. Transakce jsou vykazovány na úrovni operace rozhraní API i na úrovni služby úložiště a kapacita je hlášena na úrovni služby úložiště. Data metrik lze použít k analýze využití služby úložiště, diagnostikovat problémy s požadavky na službu úložiště a zlepšit výkon aplikací, které používají službu.

### <a name="application-insights"></a>Application Insights

[Application Insights](../../azure-monitor/app/app-insights-overview.md) je rozšiřitelná služba Správy výkonu aplikací (APM) pro webové vývojáře na více platformách. Slouží k monitorování živé webové aplikace. Automaticky zjišťuje anomálie ve výkonu. Obsahuje výkonné analytické nástroje, které vám pomohou diagnostikovat problémy a pochopit, co uživatelé s vaší aplikací dělají. Je navržena tak, aby pomáhala průběžně vylepšovat výkon a možnosti využití. Funguje pro aplikace na široké škále platforem, včetně .NET, Node.js a Java EE, hostované místně nebo v cloudu. Integruje se s procesem devOps a má spojovací body k různým vývojovým nástrojům.

Monitoruje tyto parametry:

- **Frekvence požadavků, doby odezvy a míra selhání** – Zjistěte, které stránky jsou nejoblíbenější a v kterou denní dobu a kde jsou vaši uživatelé. Zjistíte, která stránka si vede nejlépe. Pokud se při zvýšení počtu požadavků zvýší i doba odezvy a míra selhání, máte pravděpodobně potíže s prostředky.

- **Míra závislosti, doby odezvy a míra selhání** – Zjistěte, jestli vás nezpomalují externí služby.

- **Výjimky** – analyzujte agregované statistiky nebo vyberte konkrétní instance a projděte si trasování zásobníku a související požadavky. Hlásí se výjimky serveru i prohlížeče.

- **Zobrazení a načítání stránek** – Tyto informace hlásí prohlížeče uživatelů.

- **AJAX volání z webových stránek** - sazby, doba odezvy, a poruchovosti.

- **Počet uživatelů a relací.**

- **Čítače výkonu** ze serverových počítačů s Windows nebo Linuxem, jako je třeba CPU, paměť a využití sítě.

- **Diagnostika hostitele** z Dockeru nebo Azure.

- **Protokoly trasování diagnostiky** z vaší aplikace – umožňují zjistit korelaci mezi požadavky a událostmi trasování.

- **Vlastní události a metriky,** které sami napíšete do kódu klienta nebo serveru, ke sledování obchodních událostí, jako jsou prodané položky nebo vyhrané hry.

Infrastrukturu aplikace obvykle tvoří celá řada komponent, může to být třeba virtuální počítač, účet úložiště a virtuální síť nebo webová aplikace, databáze, databázový server a služby jiných výrobců. Tyto komponenty nevidíte jako samostatné entity, ale jako související a vzájemně provázané části jedné entity. Chcete je nasadit, spravovat a monitorovat jako skupinu. [Azure Resource Manager](../../azure-resource-manager/management/overview.md) umožňuje pracovat s prostředky ve vašem řešení jako skupina.

Všechny prostředky pro vaše řešení můžete nasadit, aktualizovat nebo odstranit v rámci jediné koordinované operace. Pro nasazení použijete šablonu a tato šablona může fungovat v různých prostředích, jako například v testovacím, přípravném nebo produkčním prostředí. Resource Manager poskytuje funkce zabezpečení, auditování a označování, které vám po nasazení pomohou prostředky spravovat.

**Výhody použití Resource Manageru**

Resource Manager poskytuje několik výhod:

- Můžete všechny prostředky pro vaše řešení nasadit, spravovat a monitorovat jako skupinu a nemusíte je zpracovávat jednotlivě.

- Můžete svoje řešení opakovaně nasadit v průběhu životního cyklu a mít přitom jistotu, že se prostředky nasadí konzistentně.

- Infrastrukturu můžete spravovat pomocí deklarativních šablon místo skriptů.

- Můžete definovat závislosti mezi prostředky, takže jsou nasazeny ve správném pořadí.

- Můžete využít řízení přístupu pro všechny služby ve vaší skupině prostředků, protože do platformy pro správu je nativně integrováno řízení přístupu na základě role (RBAC).

- Můžete označit prostředky pomocí značek a logicky tak uspořádat všechny prostředky ve svém předplatném.

- Můžete zpřehlednit fakturaci svojí organizace zobrazením nákladů na skupinu prostředků, které sdílejí stejnou značku.

> [!Note]
> Resource Manager poskytuje nový způsob nasazení a správy vašich řešení. Pokud jste použili dřívější model nasazení a chcete se o těchto změnách dozvědět, přečtěte si informace o změnách v [tématu Principy nasazení správce prostředků a klasického nasazení](../../azure-resource-manager/management/deployment-models.md).

## <a name="next-steps"></a>Další kroky

Další informace o zabezpečení naleznete v některých našich podrobných tématech týkajících se zabezpečení:

- [Auditování a protokolování](https://www.microsoft.com/en-us/trustcenter/security/auditingandlogging)

- [Kyberkriminalita](https://www.microsoft.com/en-us/trustcenter/security/cybercrime)

- [Návrh a provozní bezpečnost](https://www.microsoft.com/en-us/trustcenter/security/designopsecurity)

- [Šifrování](https://www.microsoft.com/en-us/trustcenter/security/encryption)

- [Správa identit a přístupu](https://www.microsoft.com/en-us/trustcenter/security/identity)

- [Zabezpečení sítě](https://www.microsoft.com/en-us/trustcenter/security/networksecurity)

- [Řízení rizik](https://www.microsoft.com/en-us/trustcenter/security/threatmanagement)
