---
title: Přehled zabezpečení
titleSuffix: Azure Cognitive Search
description: Azure Kognitivní hledání je kompatibilní s SOC 2, HIPAA a dalšími certifikacemi. Připojení a šifrování dat, ověřování a přístup k identitám prostřednictvím identifikátorů zabezpečení uživatelů a skupin ve výrazech filtru.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/01/2020
ms.custom: references_regions
ms.openlocfilehash: f314394d3a0ac453d525079e096162d8739f67cf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91314699"
---
# <a name="security-in-azure-cognitive-search---overview"></a>Zabezpečení v Azure Kognitivní hledání – přehled

Tento článek popisuje klíčové funkce zabezpečení v Azure Kognitivní hledání, které mohou chránit obsah a operace.

+ Ve vrstvě úložiště je šifrování v klidovém umístění integrované pro veškerý obsah spravovaný službou uložený na disk, včetně indexů, map synonym a definic indexerů, zdrojů dat a dovednosti. Azure Kognitivní hledání také podporuje přidání klíčů spravovaných zákazníkem (CMK) pro další šifrování indexovaných obsahu. Pro služby vytvořené po 1 2020. srpna se šifrování CMK rozšíří na data na dočasných discích, aby bylo úplné šifrování indexovaného obsahu velmi dvojité.

+ Příchozí zabezpečení chrání koncový bod vyhledávací služby na rostoucí úrovni zabezpečení: od klíčů rozhraní API v žádosti až po příchozí pravidla v bráně firewall až po privátní koncové body, které plně chrání vaše služby před veřejným internetem.

+ Odchozí zabezpečení se vztahuje na indexery, které vyžádají obsah z externích zdrojů. U odchozích požadavků nastavte spravovanou identitu tak, aby při přístupu k datům z Azure Storage, Azure SQL, Cosmos DB nebo jiných zdrojů dat Azure provedla hledání důvěryhodné služby. Spravovaná identita je náhradou za přihlašovací údaje nebo přístupové klíče k připojení. V tomto článku se nezabývá odchozí zabezpečení. Další informace o této funkci najdete v tématu [připojení ke zdroji dat pomocí spravované identity](search-howto-managed-identities-data-sources.md).

Podívejte se na toto video s rychlým tempem, kde se dozvíte přehled architektury zabezpečení a jednotlivých kategorií funkcí.

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Azure-Cognitive-Search-Whats-new-in-security/player]

<a name="encryption"></a>

## <a name="encrypted-transmissions-and-storage"></a>Šifrované přenosy a úložiště

V Azure Kognitivní hledání začíná šifrování připojení a přenosů a rozšiřuje se na obsah uložený na disku. Pro vyhledávací služby na veřejném Internetu Azure Kognitivní hledání naslouchá na portu HTTPS 443. Všechna připojení klient-služba používají šifrování TLS 1,2. Starší verze (1,0 nebo 1,1) nejsou podporovány.

:::image type="content" source="media/search-security-overview/encryption-at-rest-cmk.png" alt-text="Diagram znázorňující různé typy zabezpečení na jednotlivých úrovních zapojení služby":::

V následující tabulce jsou popsány datové [modely](../security/fundamentals/encryption-models.md)pro data, která jsou interně zpracovávána pomocí vyhledávací služby. Některé funkce, jako je znalostní báze, přírůstkové obohacení a indexování založené na indexerech, čtou nebo zapisují do datových struktur v jiných službách Azure. Tyto služby mají svou vlastní úroveň podpory šifrování, která je oddělená od Azure Kognitivní hledání.

| Model | Klíče&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | Požadavků&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | Omezení | Platí pro |
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

V Azure Kognitivní hledání je dvojité šifrování rozšířením CMK. Považuje se za oboustranné šifrování (jednou pomocí CMK a znovu klíče spravované službou) a komplexní v oboru, včetně dlouhodobého úložiště, které se zapisuje na datový disk a krátkodobého úložiště zapsaného na dočasné disky. Rozdíl mezi CMK před srpna 1 2020 a po a s tím, jak CMK funkci šifrování v Azure Kognitivní hledání, je další šifrování dat na dočasných discích.

Pro nové služby, které se v těchto oblastech vytvoří po 1. srpna, je momentálně k dispozici šifrování s dvojitým šifrováním:

+ Západní USA 2
+ East US
+ Středojižní USA
+ USA (Gov) – Virginia
+ USA (Gov) – Arizona

<a name="service-access-and-authentication"></a>

## <a name="inbound-security-and-endpoint-protection"></a>Příchozí zabezpečení a Ochrana koncového bodu

Příchozí funkce zabezpečení chrání koncový bod vyhledávací služby prostřednictvím zvýšené úrovně zabezpečení a složitosti. Nejdřív všechny požadavky vyžadují klíč rozhraní API pro ověřený přístup. V druhém případě můžete nastavit pravidla brány firewall, která omezují přístup na konkrétní IP adresy. V případě rozšířené ochrany je třetí možností povolit privátní propojení Azure s ochranou koncového bodu služby ze všech internetových přenosů.

### <a name="public-access-using-api-keys"></a>Veřejný přístup s použitím klíčů rozhraní API

Ve výchozím nastavení se k vyhledávací službě přistupuje prostřednictvím veřejného cloudu, a to pomocí ověřování založeného na klíčích pro správce nebo dotazování na koncový bod vyhledávací služby. Klíč rozhraní API je řetězec tvořený náhodně generovanými čísly a písmeny. Typ klíče (správce nebo dotaz) určuje úroveň přístupu. Odeslání platného klíče se považuje za důkaz, že požadavek pochází z důvěryhodné entity.

Existují dvě úrovně přístupu k vaší vyhledávací službě, které jsou povoleny pomocí následujících klíčů rozhraní API:

+ Klíč správce (umožňuje v rámci vyhledávací služby přístup pro čtení a zápis pro operace [Vytvoření-čtení-aktualizace-odstranění](https://en.wikipedia.org/wiki/Create,_read,_update_and_delete) )

+ Klíč dotazu (umožňuje přístup jen pro čtení k kolekci dokumentů v indexu)

*Klíče správce* se vytvoří při zřizování služby. Existují dva klíče správce určené jako *primární* a *sekundární* , aby byly zachovány, ale ve skutečnosti jsou zaměnitelné. Každá služba má dva klíče správce, takže je možné ji navrátit, aniž byste ztratili přístup ke službě. [Klíč správce můžete pravidelně vygenerovat](search-security-api-keys.md#regenerate-admin-keys) na základě osvědčených postupů zabezpečení Azure, ale nemůžete přidat do celkového počtu klíčů správce. Pro službu vyhledávání existuje maximálně dva klíče správce.

*Klíče dotazů* jsou vytvořeny podle potřeby a jsou určeny pro klientské aplikace, které vydávají dotazy. Můžete vytvořit až 50 klíčů dotazů. V kódu aplikace zadáte adresu URL pro vyhledávání a klíč rozhraní API pro dotaz, který povolí přístup jen pro čtení k kolekci dokumentů určitého indexu. Zároveň koncový bod, klíč rozhraní API-Key pro přístup jen pro čtení a cílový index definují rozsah a úroveň přístupu připojení z klientské aplikace.

Pro každý požadavek se vyžaduje ověřování, kde každý požadavek se skládá z povinného klíče, operace a objektu. Při zřetězení jsou dvě úrovně oprávnění (úplné nebo jen pro čtení) a kontext (například operace dotazu na index) dostačující pro zajištění plného spektra provozu provozu. Další informace o klíčích najdete v tématu [Vytvoření a Správa klíčů rozhraní API](search-security-api-keys.md).

### <a name="ip-restricted-access"></a>Přístup s omezením IP

Pro další řízení přístupu ke službě vyhledávání můžete vytvořit pravidla brány firewall, která umožňují přístup ke konkrétní IP adrese nebo rozsahu IP adres. Všechna připojení klientů musí být provedena prostřednictvím povolené IP adresy, jinak se připojení zamítlo.

:::image type="content" source="media/search-security-overview/inbound-firewall-ip-restrictions.png" alt-text="Diagram znázorňující různé typy zabezpečení na jednotlivých úrovních zapojení služby":::

K [nakonfigurování příchozího přístupu](service-configure-firewall.md)můžete použít portál.

Alternativně můžete použít rozhraní REST API pro správu. Počínaje rozhraním API verze 2020-03-13 s parametrem [IpRule](/rest/api/searchmanagement/services/createorupdate#iprule) můžete omezit přístup ke službě tím, že identifikujete IP adresy, jednotlivě nebo v rozsahu, který chcete udělit přístup k vaší vyhledávací službě.

### <a name="private-endpoint-no-internet-traffic"></a>Privátní koncový bod (žádný internetový provoz)

[Privátní koncový bod](../private-link/private-endpoint-overview.md) pro Azure kognitivní hledání umožňuje klientovi ve [virtuální síti](../virtual-network/virtual-networks-overview.md) zabezpečený přístup k datům v indexu vyhledávání prostřednictvím [privátního propojení](../private-link/private-link-overview.md).

Privátní koncový bod používá IP adresu z adresního prostoru virtuální sítě pro připojení k vaší vyhledávací službě. Síťový provoz mezi klientem a vyhledávací službou prochází přes virtuální síť a privátní odkaz na páteřní síti Microsoftu, což eliminuje expozici veřejného Internetu. VIRTUÁLNÍ síť umožňuje zabezpečenou komunikaci mezi prostředky, s vaší místní sítí i s internetem.

:::image type="content" source="media/search-security-overview/inbound-private-link-azure-cog-search.png" alt-text="Diagram znázorňující různé typy zabezpečení na jednotlivých úrovních zapojení služby":::

I když toto řešení je nejbezpečnější, je za použití dalších služeb k dispozici náklady, abyste měli jistotu, že budete mít jasné znalosti výhod před začneteí. Další informace o cenách najdete na stránce s [cenami](https://azure.microsoft.com/pricing/details/private-link/). Další informace o tom, jak tyto komponenty spolupracují, najdete v videu v horní části tohoto článku. Pokrytí možnosti privátní koncový bod začíná na videu na 5:48. Pokyny k nastavení koncového bodu najdete v tématu [Vytvoření privátního koncového bodu pro Azure kognitivní hledání](service-create-private-endpoint.md).

## <a name="index-access"></a>Přístup k indexu

V Azure Kognitivní hledání není jednotlivý index zabezpečitelným objektem. Místo toho se přístup k indexu určí na úrovni služby (přístup pro čtení nebo zápis ke službě) spolu s kontextem operace.

Pro přístup koncového uživatele můžete strukturovat požadavky na dotazy pro připojení pomocí klíče dotazu, který zpřístupňuje všechny požadavky jen pro čtení a zahrnuje konkrétní index používaný vaší aplikací. V požadavku na dotaz neexistuje koncept spojování indexů ani přístup k několika indexům současně, takže všechny požadavky cílí na jeden index podle definice. V důsledku toho konstrukce samotné žádosti o dotaz (klíč a jeden cílový index) definuje hranici zabezpečení.

Přístup správců a vývojářů k indexům není odlišený: musí mít přístup pro zápis k vytváření, odstraňování a aktualizaci objektů spravovaných službou. Kdokoli s klíčem správce ke službě může číst, upravovat nebo odstraňovat libovolný index ve stejné službě. Pro ochranu proti náhodnému nebo škodlivému odstranění indexů je vaše interní Správa zdrojového kódu pro assety kódů nápravou pro vrácení nechtěného odstranění nebo změny indexu. Azure Kognitivní hledání má v rámci clusteru převzetí služeb při selhání, aby se zajistila dostupnost, ale neukládá ani neprovádí vlastní kód používaný k vytváření nebo načítání indexů.

Pro řešení s více architekturami, které vyžadují hranice zabezpečení na úrovni indexu, taková řešení obvykle zahrnují střední úroveň, kterou zákazníci používají ke zpracování izolace indexu. Další informace o případu použití s více klienty najdete v tématu [vzory návrhu pro víceklientské aplikace SaaS a Azure kognitivní hledání](search-modeling-multitenant-saas-applications.md).

## <a name="user-access"></a>Přístup uživatelů

Způsob, jakým uživatel přistupuje k indexu a dalším objektům, je určen typem klíče rozhraní API v žádosti. Většina vývojářů vytváří a přiřazuje [*klíče dotazů*](search-security-api-keys.md) pro žádosti o vyhledávání na straně klienta. Klíč dotazu uděluje přístup jen pro čtení k prohledávatelné obsahu v rámci indexu.

Pokud pro výsledky hledání potřebujete podrobný ovládací prvek pro jednotlivé uživatele, můžete pro své dotazy vytvořit filtry zabezpečení a vracet dokumenty přidružené k dané identitě zabezpečení. Místo předdefinovaných rolí a přiřazení rolí se řízení přístupu na základě identity implementuje jako *Filtr* , který ořízne výsledky hledání dokumentů a obsahu na základě identit. Následující tabulka popisuje dva přístupy k oříznutí výsledků hledání neoprávněného obsahu.

| Přístup | Description |
|----------|-------------|
|[Oříznutí zabezpečení na základě filtrů identity](search-security-trimming-for-azure-search.md)  | Dokumentuje základní pracovní postup pro implementaci řízení přístupu identity uživatele. Zahrnuje přidávání identifikátorů zabezpečení do indexu a pak vysvětluje filtrování na základě tohoto pole za účelem oříznutí výsledků zakázaného obsahu. |
|[Oříznutí zabezpečení na základě Azure Active Directory identit](search-security-trimming-for-azure-search-with-aad.md)  | Tento článek se rozbalí v předchozím článku, který poskytuje kroky pro načtení identit z Azure Active Directory (Azure AD), jedné z [bezplatných služeb](https://azure.microsoft.com/free/) na cloudové platformě Azure. |

## <a name="administrative-rights"></a>Práva správce

[Řízení přístupu na základě role v Azure (Azure RBAC)](../role-based-access-control/overview.md) je autorizační systém založený na [Azure Resource Manager](../azure-resource-manager/management/overview.md) pro zřizování prostředků Azure. V Azure Kognitivní hledání se Správce prostředků používá k vytvoření nebo odstranění služby, správě klíčů rozhraní API a škálování služby. V takovém případě přiřazení rolí Azure určí, kdo může provádět tyto úlohy bez ohledu na to, jestli používají [portál](search-manage.md), [POWERSHELL](search-manage-powershell.md)nebo [rozhraní REST API pro správu](/rest/api/searchmanagement/search-howto-management-rest-api).

Naproti tomu práva správce k obsahu hostovanému na službě, jako je například schopnost vytvořit nebo odstranit index, jsou odvozena prostřednictvím klíčů rozhraní API, jak je popsáno v [předchozí části](#index-access).

> [!TIP]
> Pomocí mechanismů pro práci v rámci Azure můžete uzamknout předplatné nebo prostředek, abyste zabránili nechtěnému nebo neautorizovanému odstranění služby vyhledávání uživatelů s právy správce. Další informace najdete v tématu [uzamčení prostředků, aby se zabránilo neočekávanému odstranění](../azure-resource-manager/management/lock-resources.md).

## <a name="certifications-and-compliance"></a>Certifikace a dodržování předpisů

Pro veřejný cloud i Azure Government byl v Azure Kognitivní hledání certifikovaný standard pro více globálních, regionálních a specifických standardů. Úplný seznam najdete v dokumentu [White paper o **kompatibilitě Microsoft Azure** ](https://azure.microsoft.com/resources/microsoft-azure-compliance-offerings/) na stránce oficiálních sestav auditu.

V případě dodržování předpisů můžete pomocí [Azure Policy](../governance/policy/overview.md) implementovat osvědčené postupy pro [zabezpečení Azure](../security/benchmarks/introduction.md)v rámci vysokého zabezpečení. Srovnávací test zabezpečení Azure je kolekcí doporučení zabezpečení, která se mapují na klíčové akce, které byste měli vzít v úvahu při zmírnění hrozeb pro služby a data. V současné době je k dispozici 11 kontrol zabezpečení, včetně [zabezpečení sítě](../security/benchmarks/security-control-network-security.md), [protokolování a monitorování](../security/benchmarks/security-control-logging-monitoring.md)a [ochrany dat](../security/benchmarks/security-control-data-protection.md) .

Azure Policy je funkce integrovaná do Azure, která vám pomůže spravovat dodržování předpisů pro několik standardů, včetně standardů Azure Security test. U dobře známých srovnávacích testů Azure Policy poskytuje předdefinované definice, které poskytují obě kritéria, i odpověď, která řeší nedodržování předpisů.

V případě Azure Kognitivní hledání existuje v současné době jedna integrovaná definice. Slouží k protokolování diagnostiky. Pomocí této integrované sady můžete přiřadit zásadu, která identifikuje libovolnou vyhledávací službu, která postrádá protokolování diagnostiky, a pak ji zapne. Další informace najdete v tématu [Azure Policy kontroly dodržování předpisů pro Azure kognitivní hledání](security-controls-policy.md).

## <a name="see-also"></a>Viz také

+ [Základní informace o zabezpečení Azure](../security/fundamentals/index.yml)
+ [Zabezpečení Azure](https://azure.microsoft.com/overview/security)
+ [Azure Security Center](../security-center/index.yml)
