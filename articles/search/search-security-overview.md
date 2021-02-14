---
title: Přehled zabezpečení
titleSuffix: Azure Cognitive Search
description: Přečtěte si o funkcích zabezpečení v Azure Kognitivní hledání k ochraně koncových bodů, obsahu a operací.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/04/2021
ms.custom: references_regions
ms.openlocfilehash: 46f2035e5f8409cd38faeb9c327b88b06fc7d7a0
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/10/2021
ms.locfileid: "100097632"
---
# <a name="security-overview-for-azure-cognitive-search"></a>Přehled zabezpečení pro Azure Kognitivní hledání

Tento článek popisuje funkce zabezpečení v Azure Kognitivní hledání, které chrání obsah a operace.

U příchozích požadavků provedených u vyhledávací služby probíhá bezpečnostní opatření, která chrání koncový bod vyhledávací služby: od klíčů rozhraní API v žádosti až po příchozí pravidla v bráně firewall až ke soukromým koncovým bodům, které plně chrání vaše služby před veřejným internetem.

U odchozích požadavků provedených na jiné služby se převládající žádost provádí indexery, které čtou obsah z externích zdrojů. Přihlašovací údaje můžete zadat v připojovacím řetězci. Nebo můžete nastavit spravovanou identitu, abyste mohli prohledávat důvěryhodnou službu při přístupu k datům z Azure Storage, Azure SQL, Cosmos DB nebo jiných zdrojů dat Azure. Spravovaná identita je náhradou za přihlašovací údaje nebo přístupové klíče k připojení. Další informace o této funkci najdete v tématu [připojení ke zdroji dat pomocí spravované identity](search-howto-managed-identities-data-sources.md).

Operace zápisu do externích služeb jsou pár: vyhledávací služba zapisuje do souborů protokolu a při vytváření úložišť znalostí, zachování rozšíření uložených v mezipaměti a zachování ladicích relací zapíše do Azure Storage. Další volání služby pro službu, jako je například Cognitive Services, se provádějí v interní síti.

Podívejte se na toto video s rychlým tempem, kde se dozvíte přehled architektury zabezpečení a jednotlivých kategorií funkcí.

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Azure-Cognitive-Search-Whats-new-in-security/player]

## <a name="network-security"></a>Zabezpečení sítě

<a name="service-access-and-authentication"></a>

Příchozí funkce zabezpečení chrání koncový bod vyhledávací služby prostřednictvím zvýšené úrovně zabezpečení a složitosti. Nejdřív všechny požadavky vyžadují klíč rozhraní API pro ověřený přístup. V druhém případě můžete nastavit pravidla brány firewall, která omezují přístup na konkrétní IP adresy. V případě rozšířené ochrany je třetí možností povolit privátní propojení Azure s ochranou koncového bodu služby ze všech internetových přenosů.

### <a name="public-access-using-api-keys"></a>Veřejný přístup s použitím klíčů rozhraní API

Ve výchozím nastavení se k vyhledávací službě přistupuje prostřednictvím veřejného cloudu, a to pomocí ověřování založeného na klíčích pro správce nebo dotazování na koncový bod vyhledávací služby. Odeslání platného klíče se považuje za důkaz, že požadavek pochází z důvěryhodné entity. Ověřování na základě klíčů je popsáno v další části.

### <a name="configure-ip-firewalls"></a>Konfigurace bran firewall protokolu IP

Pro další řízení přístupu ke službě vyhledávání můžete vytvořit pravidla brány firewall, která umožňují přístup ke konkrétní IP adrese nebo rozsahu IP adres. Všechna připojení klientů musí být provedena prostřednictvím povolené IP adresy, jinak se připojení zamítlo.

:::image type="content" source="media/search-security-overview/inbound-firewall-ip-restrictions.png" alt-text="Diagram ukázkové architektury pro omezený přístup IP":::

K [nakonfigurování příchozího přístupu](service-configure-firewall.md)můžete použít portál.

Alternativně můžete použít rozhraní REST API pro správu. Počínaje rozhraním API verze 2020-03-13 s parametrem [IpRule](/rest/api/searchmanagement/services/createorupdate#iprule) můžete omezit přístup ke službě tím, že identifikujete IP adresy, jednotlivě nebo v rozsahu, který chcete udělit přístup k vaší vyhledávací službě.

### <a name="network-isolation-through-a-private-endpoint-no-internet-traffic"></a>Izolace sítě prostřednictvím privátního koncového bodu (bez internetového provozu)

Můžete vytvořit [privátní koncový bod](../private-link/private-endpoint-overview.md) pro Azure kognitivní hledání umožňuje klientovi ve [virtuální síti](../virtual-network/virtual-networks-overview.md) zabezpečený přístup k datům v indexu vyhledávání prostřednictvím [privátního propojení](../private-link/private-link-overview.md).

Privátní koncový bod používá IP adresu z adresního prostoru virtuální sítě pro připojení k vaší vyhledávací službě. Síťový provoz mezi klientem a vyhledávací službou prochází přes virtuální síť a privátní odkaz na páteřní síti Microsoftu, což eliminuje expozici veřejného Internetu. VIRTUÁLNÍ síť umožňuje zabezpečenou komunikaci mezi prostředky, s vaší místní sítí i s internetem.

:::image type="content" source="media/search-security-overview/inbound-private-link-azure-cog-search.png" alt-text="Diagram ukázkové architektury pro přístup k privátnímu koncovému bodu":::

I když toto řešení je nejbezpečnější, je za použití dalších služeb k dispozici náklady, abyste měli jistotu, že budete mít jasné znalosti výhod před začneteí. Další informace o cenách najdete na stránce s [cenami](https://azure.microsoft.com/pricing/details/private-link/). Další informace o tom, jak tyto komponenty spolupracují, najdete v videu v horní části tohoto článku. Pokrytí možnosti privátní koncový bod začíná na videu na 5:48. Pokyny k nastavení koncového bodu najdete v tématu [Vytvoření privátního koncového bodu pro Azure kognitivní hledání](service-create-private-endpoint.md).

## <a name="authentication"></a>Authentication

Pro příchozí požadavky na vyhledávací službu ověřování používá [povinný klíč rozhraní API](search-security-api-keys.md) (řetězec tvořený náhodně generovanými čísly a písmeny), který potvrzuje, že požadavek pochází z důvěryhodného zdroje. Kognitivní hledání v současné době nepodporuje ověřování Azure Active Directory pro příchozí požadavky.

Odchozí požadavky prováděné indexerem podléhají ověřování externí službou. Podslužba indexeru v Kognitivní hledání může být v Azure vytvořená jako důvěryhodná služba, která se připojuje k jiným službám pomocí spravované identity. Další informace najdete v tématu [nastavení připojení indexeru ke zdroji dat pomocí spravované identity](search-howto-managed-identities-data-sources.md).

## <a name="authorization"></a>Autorizace

Kognitivní hledání poskytuje různé autorizační modely pro správu obsahu a správu služeb. 

### <a name="authorization-for-content-management"></a>Autorizace pro správu obsahu

Autorizace obsahu a operací souvisejících s obsahem je buď přístup pro zápis, který je odvozený prostřednictvím [klíče rozhraní API](search-security-api-keys.md) , který je k dispozici na žádosti. Klíč rozhraní API je mechanismus ověřování, ale také autorizuje přístup v závislosti na typu klíče rozhraní API.

+ Klíč správce (povolí přístup pro čtení a zápis pro operace vytvoření-čtení-aktualizace-odstranění ve vyhledávací službě), vytvořené při zřízení služby

+ Klíč dotazu (umožňuje přístup jen pro čtení ke kolekci dokumentů v indexu), vytvořený podle potřeby a je navržený pro klientské aplikace, které vydávají dotazy

V kódu aplikace zadáte koncový bod a klíč rozhraní API, který umožní přístup k obsahu a možnostem. Koncovým bodem může být samotná služba, kolekce indexů, konkrétní index, kolekce dokumentů nebo konkrétní dokument. Při zřetězení se jedná o vzorec zabezpečení, který chrání obsah a operace (například požadavek na vytvoření nebo aktualizaci) a úroveň oprávnění (úplná oprávnění nebo oprávnění jen pro čtení na základě klíče).

### <a name="controlling-access-to-indexes"></a>Řízení přístupu k indexům

V Azure Kognitivní hledání není jednotlivý index zabezpečitelným objektem. Místo toho se přístup k indexu určí na úrovni služby (přístup pro čtení nebo zápis na základě toho, který klíč rozhraní API zadáte), spolu s kontextem operace.

Pro přístup jen pro čtení můžete strukturovat požadavky na dotazy pro připojení pomocí [klíče dotazu](search-security-rbac.md)a zahrnout konkrétní index používaný vaší aplikací. V požadavku na dotaz neexistuje koncept spojování indexů ani přístup k několika indexům současně, takže všechny požadavky cílí na jeden index podle definice. V důsledku toho konstrukce samotné žádosti o dotaz (klíč a jeden cílový index) definuje hranici zabezpečení.

Přístup správců a vývojářů k indexům není odlišený: musí mít přístup pro zápis k vytváření, odstraňování a aktualizaci objektů spravovaných službou. Kdokoli s [klíčem správce](search-security-rbac.md) ke službě může číst, upravovat nebo odstraňovat libovolný index ve stejné službě. Pro ochranu proti náhodnému nebo škodlivému odstranění indexů je vaše interní Správa zdrojového kódu pro assety kódů nápravou pro vrácení nechtěného odstranění nebo změny indexu. Azure Kognitivní hledání má v rámci clusteru převzetí služeb při selhání, aby se zajistila dostupnost, ale neukládá ani neprovádí vlastní kód používaný k vytváření nebo načítání indexů.

Pro řešení s více architekturami, které vyžadují hranice zabezpečení na úrovni indexu, taková řešení obvykle zahrnují střední úroveň, kterou zákazníci používají ke zpracování izolace indexu. Další informace o případu použití s více klienty najdete v tématu [vzory návrhu pro víceklientské aplikace SaaS a Azure kognitivní hledání](search-modeling-multitenant-saas-applications.md).

### <a name="controlling-access-to-documents"></a>Řízení přístupu k dokumentům

Pokud pro výsledky hledání potřebujete podrobný ovládací prvek pro jednotlivé uživatele, můžete pro své dotazy vytvořit filtry zabezpečení a vracet dokumenty přidružené k dané identitě zabezpečení. 

V koncepčním ekvivalentu "zabezpečení na úrovni řádku" se autorizace obsahu v rámci indexu neimplementuje nativně pomocí předdefinovaných rolí nebo přiřazení rolí, které se mapují na entity v Azure Active Directory. Všechna oprávnění uživatelů k datům v externích systémech, jako je například Cosmos DB, se s těmito daty neodesílají jako indexovaná Kognitivní hledání.

Alternativním řešením pro řešení, která vyžadují "zabezpečení na úrovni řádku", je vytvoření pole ve zdroji dat, které představuje skupinu zabezpečení nebo identitu uživatele, a následné použití filtrů v Kognitivní hledání k selektivnímu oříznutí výsledků hledání dokumentů a obsahu na základě identit. Následující tabulka popisuje dva přístupy k oříznutí výsledků hledání neoprávněného obsahu.

| Přístup | Description |
|----------|-------------|
|[Oříznutí zabezpečení na základě filtrů identity](search-security-trimming-for-azure-search.md)  | Dokumentuje základní pracovní postup pro implementaci řízení přístupu identity uživatele. Zahrnuje přidávání identifikátorů zabezpečení do indexu a pak vysvětluje filtrování na základě tohoto pole za účelem oříznutí výsledků zakázaného obsahu. |
|[Oříznutí zabezpečení na základě Azure Active Directory identit](search-security-trimming-for-azure-search-with-aad.md)  | Tento článek se rozbalí v předchozím článku, který poskytuje kroky pro načtení identit z Azure Active Directory (Azure AD), jedné z [bezplatných služeb](https://azure.microsoft.com/free/) na cloudové platformě Azure. |

### <a name="authorization-for-service-management"></a>Autorizace pro správu služeb

Operace správy služeb jsou autorizované prostřednictvím [řízení přístupu na základě role v Azure (Azure RBAC)](../role-based-access-control/overview.md). Azure RBAC je autorizační systém založený na [Azure Resource Manager](../azure-resource-manager/management/overview.md) pro zřizování prostředků Azure. 

V Azure Kognitivní hledání se Správce prostředků používá k vytvoření nebo odstranění služby, správě klíčů rozhraní API a škálování služby. V takovém případě přiřazení rolí Azure určí, kdo může provádět tyto úlohy bez ohledu na to, jestli používají [portál](search-manage.md), [POWERSHELL](search-manage-powershell.md)nebo [rozhraní REST API pro správu](/rest/api/searchmanagement/search-howto-management-rest-api).

Pro správu služby Search jsou definovány [tři základní role](search-security-rbac.md#management-tasks-by-role) . Přiřazení rolí lze provádět pomocí jakékoli podporované metodologie (portál, PowerShellu a tak dále) a jsou pro ně dodržena platná služba. Role vlastníka a přispěvatele můžou provádět celou řadu funkcí pro správu. Roli Čtenář můžete přiřadit uživatelům, kteří zobrazují jenom základní informace.

> [!Note]
> Pomocí mechanismů pro práci v rámci Azure můžete uzamknout předplatné nebo prostředek, abyste zabránili nechtěnému nebo neautorizovanému odstranění služby vyhledávání uživatelů s právy správce. Další informace najdete v tématu [uzamčení prostředků, aby se zabránilo neočekávanému odstranění](../azure-resource-manager/management/lock-resources.md).

<a name="encryption"></a>

## <a name="data-protection"></a>Ochrana dat

Ve vrstvě úložiště je šifrování dat integrované pro veškerý obsah spravovaný službou uložený na disk, včetně indexů, map synonym a definic indexerů, zdrojů dat a dovednosti. Volitelně můžete přidat klíče spravované zákazníkem (CMK) pro dodatečné šifrování indexovaného obsahu. Pro služby vytvořené po 1 2020. srpna se šifrování CMK rozšíří na data na dočasných discích, pro úplné "dvojité šifrování" indexovaného obsahu.

### <a name="data-in-transit"></a>Přenášená data

V Azure Kognitivní hledání začíná šifrování připojení a přenosů a rozšiřuje se na obsah uložený na disku. Pro vyhledávací služby na veřejném Internetu Azure Kognitivní hledání naslouchá na portu HTTPS 443. Všechna připojení klient-služba používají šifrování TLS 1,2. Starší verze (1,0 nebo 1,1) nejsou podporovány.

### <a name="encrypted-data-at-rest"></a>Zašifrovaná data v klidovém stavu

V následující tabulce jsou popsány datové [modely](../security/fundamentals/encryption-models.md)pro data, která jsou interně zpracovávána pomocí vyhledávací služby. Některé funkce, jako je znalostní báze, přírůstkové obohacení a indexování založené na indexerech, čtou nebo zapisují do datových struktur v jiných službách Azure. Tyto služby mají svou vlastní úroveň podpory šifrování, která je oddělená od Azure Kognitivní hledání.

| Modelování | Klíče&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | Požadavků&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | Omezení | Platí pro |
|------------------|-------|-------------|--------------|------------|
| šifrování na straně serveru | Klíče spravované Microsoftem | Žádný (integrovaný) | Žádná, k dispozici na všech úrovních ve všech oblastech pro obsah vytvořený po lednu 24 2018. | Obsah (indexy a mapy synonym) a definice (indexery, zdroje dat, dovednosti) |
| šifrování na straně serveru | klíče spravované zákazníkem | Azure Key Vault | K dispozici pro Fakturovatelné úrovně pro obsah vytvořený po lednu 2019 ve všech oblastech. | Obsah (indexy a mapy synonym) na datových discích |
| dvojité šifrování na straně serveru | klíče spravované zákazníkem | Azure Key Vault | K dispozici v fakturovaných úrovních ve vybraných oblastech ve službě Search po 1 2020. srpna. | Obsah (indexy a mapy synonym) na datových discích a na dočasných discích |

### <a name="service-managed-keys"></a>Klíče spravované službou

Šifrování spravované službou je interní operace od Microsoftu, která je založená na [šifrování služby Azure Storage](../storage/common/storage-service-encryption.md)s využitím 256 [šifrování AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard). K tomu dochází automaticky při každém indexování, včetně přírůstkových aktualizací indexů, které nejsou plně šifrované (vytvořené před lednem 2018).

### <a name="customer-managed-keys-cmk"></a>Klíče spravované zákazníkem (CMK)

Klíče spravované zákazníkem vyžadují další fakturovatelnou službu, Azure Key Vault, která může být v jiné oblasti, ale v rámci stejného předplatného jako Azure Kognitivní hledání. Povolením šifrování CMK se zvýší velikost indexu a sníží se výkon dotazů. Na základě pozorování k datu můžete očekávat zvýšení 30% až 60% v době dotazu, přestože skutečný výkon se bude lišit v závislosti na definici indexu a typech dotazů. Z důvodu tohoto dopadu na výkon doporučujeme tuto funkci povolit pouze pro indexy, které ji skutečně vyžadují. Další informace najdete v tématu [Konfigurace šifrovacích klíčů spravovaných zákazníkem v Azure kognitivní hledání](search-security-manage-encryption-keys.md).

<a name="double-encryption"></a>

### <a name="double-encryption"></a>Dvojité šifrování

V Azure Kognitivní hledání je dvojité šifrování rozšířením CMK. Považuje se za oboustranné šifrování (jednou pomocí CMK a znovu klíče spravované službou) a komplexní v oboru zahrnující dlouhodobé úložiště, které se zapisuje na datový disk, a krátkodobé úložiště zapsané na dočasné disky. Rozdíl mezi CMK před srpna 1 2020 a po a s tím, jak CMK funkci šifrování v Azure Kognitivní hledání, je další šifrování dat na dočasných discích.

Pro nové služby, které se v těchto oblastech vytvoří po 1. srpna, je momentálně k dispozici šifrování s dvojitým šifrováním:

+ Západní USA 2
+ East US
+ Středojižní USA
+ USA (Gov) – Virginia
+ USA (Gov) – Arizona

## <a name="security-management"></a>Správa zabezpečení

### <a name="api-keys"></a>Klíče rozhraní API

Při ověřování založeném na klíčích rozhraní API byste měli mít plán pro opětovné generování klíče správce v pravidelných intervalech podle osvědčených postupů zabezpečení Azure. Pro službu vyhledávání existuje maximálně dva klíče správce. Další informace o zabezpečení a správě klíčů rozhraní API najdete v tématu [Vytvoření a Správa klíčů rozhraní API](search-security-api-keys.md).

#### <a name="activity-and-diagnostic-logs"></a>Protokoly aktivit a diagnostické protokoly

Kognitivní hledání neprotokoluje identity uživatelů, takže nebudete moct v protokolech odkazovat na informace o konkrétním uživateli. Služba ale provede operace log Create-Read-Update-Delete, které by mohly být schopné korelovat s ostatními protokoly a pochopit konkrétní informace v agentuře.

Pomocí výstrah a infrastruktury protokolování v Azure můžete vybírat špičky svazku dotazů nebo jiné akce, které se odchylují od očekávaných úloh. Další informace o nastavení protokolů najdete v tématu [shromažďování a analýza dat protokolů](search-monitor-logs.md) a [monitorování požadavků na dotazy](search-monitor-queries.md).

### <a name="certifications-and-compliance"></a>Certifikace a dodržování předpisů

Azure Kognitivní hledání se účastní pravidelných auditů a byl certifikovaný na základě řady celosvětových, regionálních a specifických standardů pro veřejný cloud a Azure Government. Úplný seznam najdete v dokumentu [White paper o **kompatibilitě Microsoft Azure**](https://azure.microsoft.com/resources/microsoft-azure-compliance-offerings/) na stránce oficiálních sestav auditu.

V případě dodržování předpisů můžete pomocí [Azure Policy](../governance/policy/overview.md) implementovat osvědčené postupy pro [zabezpečení Azure](../security/benchmarks/introduction.md)v rámci vysokého zabezpečení. Srovnávací test zabezpečení Azure je kolekcí doporučení zabezpečení, která se mapují na klíčové akce, které byste měli vzít v úvahu při zmírnění hrozeb pro služby a data. V současné době je k dispozici 11 kontrol zabezpečení, včetně [zabezpečení sítě](../security/benchmarks/security-control-network-security.md), [protokolování a monitorování](../security/benchmarks/security-control-logging-monitoring.md)a [ochrany dat](../security/benchmarks/security-control-data-protection.md) .

Azure Policy je funkce integrovaná do Azure, která vám pomůže spravovat dodržování předpisů pro několik standardů, včetně standardů Azure Security test. U dobře známých srovnávacích testů Azure Policy poskytuje předdefinované definice, které poskytují obě kritéria, i odpověď, která řeší nedodržování předpisů.

V případě Azure Kognitivní hledání existuje v současné době jedna integrovaná definice. Slouží k protokolování diagnostiky. Pomocí této integrované sady můžete přiřadit zásadu, která identifikuje libovolnou vyhledávací službu, která postrádá protokolování diagnostiky, a pak ji zapne. Další informace najdete v tématu [Azure Policy kontroly dodržování předpisů pro Azure kognitivní hledání](security-controls-policy.md).

## <a name="see-also"></a>Viz také

+ [Základní informace o zabezpečení Azure](../security/fundamentals/index.yml)
+ [Zabezpečení Azure](https://azure.microsoft.com/overview/security)
+ [Azure Security Center](../security-center/index.yml)