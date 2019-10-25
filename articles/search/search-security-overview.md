---
title: Zabezpečení a ochrana dat
titleSuffix: Azure Cognitive Search
description: Azure Kognitivní hledání je kompatibilní s SOC 2, HIPAA a dalšími certifikacemi. Připojení a šifrování dat, ověřování a přístup k identitám prostřednictvím identifikátorů zabezpečení uživatelů a skupin ve výrazech filtru.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 2e509535473fa50fd3150965e1513e056ead18a6
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2019
ms.locfileid: "72794337"
---
# <a name="security-and-data-privacy-in-azure-cognitive-search"></a>Zabezpečení a ochrana dat v Azure Kognitivní hledání

Komplexní funkce zabezpečení a řízení přístupu jsou integrované do Azure Kognitivní hledání k zajištění toho, aby byl soukromý obsah stále v tomto případě. Tento článek obsahuje výčet funkcí zabezpečení a dodržování standardů integrovaných v Azure Kognitivní hledání.

Architektura zabezpečení Azure Kognitivní hledání zahrnuje fyzické zabezpečení, šifrované přenosy, šifrované úložiště a kompatibilitu standardů pro celou platformu. V případě provozu Azure Kognitivní hledání akceptuje pouze ověřené požadavky. Volitelně můžete přidat řízení přístupu pro jednotlivé uživatele k obsahu prostřednictvím filtrů zabezpečení. Tento článek se dotýká zabezpečení na jednotlivých vrstvách, ale primárně se zaměřuje na to, jak jsou data a operace zabezpečené v Azure Kognitivní hledání.

## <a name="standards-compliance-iso-27001-soc-2-hipaa"></a>Dodržování standardů: ISO 27001, SOC 2, HIPAA

Azure Kognitivní hledání je certifikovaný v následujících standardech, jak je uvedeno [v červnu 2018](https://azure.microsoft.com/blog/azure-search-is-now-certified-for-several-levels-of-compliance/):

+ [ISO 27001:2013](https://www.iso.org/isoiec-27001-information-security.html) 
+ [SOC 2 typ 2 – dodržování předpisů](https://www.aicpa.org/interestareas/frc/assuranceadvisoryservices/aicpasoc2report.html) Pro celou sestavu použijte [sestavu Azure-a Azure Government SOC 2 Type II](https://servicetrust.microsoft.com/ViewPage/MSComplianceGuide?command=Download&downloadType=Document&downloadId=93292f19-f43e-4c4e-8615-c38ab953cf95&docTab=4ce99610-c9c0-11e7-8c2c-f908a777fa4d_SOC%20%2F%20SSAE%2016%20Reports). 
+ [Zákon o odpovědnosti za ochranu zdravotního pojištění a AKT (HIPAA)](https://en.wikipedia.org/wiki/Health_Insurance_Portability_and_Accountability_Act)
+ [GxP (21 CFR část 11)](https://en.wikipedia.org/wiki/Title_21_CFR_Part_11)
+ [HITRUST](https://en.wikipedia.org/wiki/HITRUST)
+ [PCI DSS úroveň 1](https://en.wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard)
+ [Austrálie IRAP – neklasifikované DLM](https://asd.gov.au/infosec/irap/certified_clouds.htm)

Dodržování standardů se vztahuje na všeobecně dostupné funkce. Funkce ve verzi Preview jsou certifikovány při přechodu na obecnou dostupnost a nesmí se používat v řešeních s přísnými požadavky na standardy. Certifikace dodržování předpisů je popsána v článku [Přehled dodržování předpisů Microsoft Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) a [centra zabezpečení](https://www.microsoft.com/en-us/trustcenter). 

## <a name="encrypted-transmission-and-storage"></a>Zašifrovaný přenos a úložiště

Šifrování se nachází v celém kanálu indexování: od připojení, přes přenos a dolů po indexovaná data uložená v Azure Kognitivní hledání.

| Vrstva zabezpečení | Popis |
|----------------|-------------|
| Šifrování při přenosu <br>(HTTPS/SSL/TLS) | Azure Kognitivní hledání naslouchá na portu HTTPS 443. V rámci platformy jsou připojení ke službám Azure zašifrovaná. <br/><br/>Všechny interakce mezi klientem a službou Azure Kognitivní hledání jsou s podporou protokolu SSL/TLS 1,2.  Ujistěte se, že pro připojení SSL k vaší službě používáte TLSv 1.2.|
| Šifrování v klidovém stavu <br>Spravované klíče společnosti Microsoft | Šifrování je plně v procesu indexování plně prohlášeno bez měřitelnosti při indexování času na dokončení nebo velikost indexu. K tomu dochází automaticky při každém indexování, včetně přírůstkových aktualizací indexu, který není plně šifrovaný (vytvořený před lednem 2018).<br><br>Šifrování je interně založené na [šifrování Azure Storage služby](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)pomocí 256 [šifrování AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard).<br><br> Šifrování je interní pro Azure Kognitivní hledání, pomocí certifikátů a šifrovacích klíčů spravovaných interně Microsoftem a univerzálně se používá. Šifrování nelze zapnout nebo vypnout, spravovat nebo nahrazovat vlastní klíče nebo zobrazit nastavení šifrování na portálu nebo programově.<br><br>Šifrování v klidovém umístění bylo oznámeno 24. ledna 2018 a vztahuje se na všechny úrovně služeb, včetně úrovně Free, ve všech oblastech. Pro úplné šifrování musí být indexy vytvořené před tímto datem vyřazeny a znovu sestaveny, aby mohlo dojít k šifrování. V opačném případě jsou zašifrována pouze nová data přidaná po 24. ledna.|
| Šifrování v klidovém stavu <br>Klíče spravované zákazníkem | Šifrování pomocí zákaznických klíčů je funkce ve **verzi Preview** , která není dostupná pro bezplatné služby. Pro placené služby je k dispozici jenom pro vyhledávací služby vytvořené v nebo po lednu 2019 s použitím nejnovější verze Preview rozhraní API (API-Version = 2019-05 -06-Preview).<br><br>Indexy Azure Kognitivní hledání a mapy synonym se teď dají zašifrovat v klidovém stavu pomocí klíčů zákazníka spravovaných klíči v Azure Key Vault. Další informace najdete v tématu [Správa šifrovacích klíčů v Azure kognitivní hledání](search-security-manage-encryption-keys.md).<br>Tato funkce nenahrazuje výchozí šifrování v klidovém stavu, ale místo toho se používá.<br>Povolením této funkce se zvýší velikost indexu a sníží se výkon dotazů. Na základě pozorování k datu můžete očekávat zvýšení 30% až 60% v době dotazu, přestože skutečný výkon se bude lišit v závislosti na definici indexu a typech dotazů. Z důvodu tohoto dopadu na výkon doporučujeme tuto funkci povolit pouze pro indexy, které ji skutečně vyžadují.

## <a name="azure-wide-user-access-controls"></a>Řízení přístupu uživatelů na úrovni Azure

K dispozici je několik mechanismů zabezpečení, které jsou k dispozici v rámci Azure, a tak automaticky dostupné pro prostředky Azure Kognitivní hledání, které vytvoříte.

+ [Uzamkne na úrovni předplatného nebo prostředku, aby se zabránilo odstranění.](../azure-resource-manager/resource-group-lock-resources.md)
+ [Access Control na základě rolí (RBAC) pro řízení přístupu k informacím a operacím správy](../role-based-access-control/overview.md)

Všechny služby Azure podporují řízení přístupu na základě rolí (RBAC) pro jednotné nastavení úrovní přístupu napříč všemi službami. Například zobrazení citlivých dat, jako je klíč správce, je omezeno na role vlastníka a přispěvatele, zatímco zobrazení stavu služby je k dispozici pro členy jakékoli role. RBAC poskytuje role vlastníka, přispěvatele a čtenáře. Ve výchozím nastavení jsou všichni správci služby členy role vlastníka.

<a name="service-access-and-authentication"></a>

## <a name="service-access-and-authentication"></a>Přístup ke službě a ověřování

I když Azure Kognitivní hledání dědí bezpečnostní ochranu platformy Azure, poskytuje také vlastní ověřování na základě klíčů. Klíč rozhraní API je řetězec tvořený náhodně generovanými čísly a písmeny. Typ klíče (správce nebo dotaz) určuje úroveň přístupu. Odeslání platného klíče se považuje za důkaz, že požadavek pochází z důvěryhodné entity. 

Existují dvě úrovně přístupu k vaší vyhledávací službě, které jsou povoleny dvěma typy klíčů:

* Přístup správce (platný pro všechny operace čtení a zápisu na službu)
* Přístup k dotazům (platný pro operace jen pro čtení, jako jsou dotazy, s kolekcí dokumentů indexu)

*Klíče správce* se vytvoří při zřizování služby. Existují dva klíče správce určené jako *primární* a *sekundární* , aby byly zachovány, ale ve skutečnosti jsou zaměnitelné. Každá služba má dva klíče správce, takže je možné ji navrátit, aniž byste ztratili přístup ke službě. [Klíč správce můžete pravidelně vygenerovat](search-security-api-keys.md#regenerate-admin-keys) na základě osvědčených postupů zabezpečení Azure, ale nemůžete přidat do celkového počtu klíčů správce. Pro službu vyhledávání existuje maximálně dva klíče správce.

*Klíče dotazů* jsou vytvořeny podle potřeby a jsou určeny pro klientské aplikace, které vydávají dotazy. Můžete vytvořit až 50 klíčů dotazů. V kódu aplikace zadáte adresu URL pro vyhledávání a klíč rozhraní API pro dotaz, který povolí přístup jen pro čtení k kolekci dokumentů určitého indexu. Zároveň koncový bod, klíč rozhraní API-Key pro přístup jen pro čtení a cílový index definují rozsah a úroveň přístupu připojení z klientské aplikace.

Pro každý požadavek se vyžaduje ověřování, kde každý požadavek se skládá z povinného klíče, operace a objektu. Při zřetězení jsou dvě úrovně oprávnění (úplné nebo jen pro čtení) a kontext (například operace dotazu na index) dostačující pro zajištění plného spektra provozu provozu. Další informace o klíčích najdete v tématu [Vytvoření a Správa klíčů rozhraní API](search-security-api-keys.md).

## <a name="index-access"></a>Přístup k indexu

V Azure Kognitivní hledání není jednotlivý index zabezpečitelným objektem. Místo toho je přístup k indexu určen na úrovni služby (přístup pro čtení nebo zápis) spolu s kontextem operace.

Pro přístup koncového uživatele můžete strukturovat požadavky na dotazy pro připojení pomocí klíče dotazu, který zpřístupňuje všechny požadavky jen pro čtení a zahrnuje konkrétní index používaný vaší aplikací. V požadavku na dotaz neexistuje koncept spojování indexů ani přístup k několika indexům současně, takže všechny požadavky cílí na jeden index podle definice. V důsledku toho konstrukce samotné žádosti o dotaz (klíč a jeden cílový index) definuje hranici zabezpečení.

Přístup správců a vývojářů k indexům není odlišený: musí mít přístup pro zápis k vytváření, odstraňování a aktualizaci objektů spravovaných službou. Kdokoli s klíčem správce ke službě může číst, upravovat nebo odstraňovat libovolný index ve stejné službě. Pro ochranu proti náhodnému nebo škodlivému odstranění indexů je vaše interní Správa zdrojového kódu pro assety kódů nápravou pro vrácení nechtěného odstranění nebo změny indexu. Azure Kognitivní hledání má v rámci clusteru převzetí služeb při selhání, aby se zajistila dostupnost, ale neukládá ani neprovádí vlastní kód používaný k vytváření nebo načítání indexů.

Pro řešení s více architekturami, které vyžadují hranice zabezpečení na úrovni indexu, taková řešení obvykle zahrnují střední úroveň, kterou zákazníci používají ke zpracování izolace indexu. Další informace o případu použití s více klienty najdete v tématu [vzory návrhu pro víceklientské aplikace SaaS a Azure kognitivní hledání](search-modeling-multitenant-saas-applications.md).

## <a name="admin-access"></a>Přístup správce

[Přístup na základě role (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview) určuje, jestli máte přístup k ovládacím prvkům přes službu a její obsah. Pokud jste vlastníkem nebo přispěvatelem služby Azure Kognitivní hledání, můžete k vytváření, aktualizaci nebo odstraňování objektů ve službě použít portál nebo modul PowerShell **AZ. Search.** Můžete použít také [REST API správy Azure kognitivní hledání](https://docs.microsoft.com/rest/api/searchmanagement/search-howto-management-rest-api).

## <a name="user-access"></a>Přístup uživatelů

Ve výchozím nastavení je přístup uživatelů k indexu určen pomocí přístupového klíče v žádosti o dotaz. Většina vývojářů vytváří a přiřazuje [*klíče dotazů*](search-security-api-keys.md) pro žádosti o vyhledávání na straně klienta. Klíč dotazu uděluje přístup ke čtení pro veškerý obsah v rámci indexu.

Pokud potřebujete detailní kontrolu obsahu pro jednotlivé uživatele, můžete pro své dotazy vytvořit filtry zabezpečení a vracet tak dokumenty přidružené k dané identitě zabezpečení. Místo předdefinovaných rolí a přiřazení rolí se řízení přístupu na základě identity implementuje jako *Filtr* , který ořízne výsledky hledání dokumentů a obsahu na základě identit. Následující tabulka popisuje dva přístupy k oříznutí výsledků hledání neoprávněného obsahu.

| Přístup | Popis |
|----------|-------------|
|[Oříznutí zabezpečení na základě filtrů identity](search-security-trimming-for-azure-search.md)  | Dokumentuje základní pracovní postup pro implementaci řízení přístupu identity uživatele. Zahrnuje přidávání identifikátorů zabezpečení do indexu a pak vysvětluje filtrování na základě tohoto pole za účelem oříznutí výsledků zakázaného obsahu. |
|[Oříznutí zabezpečení na základě Azure Active Directory identit](search-security-trimming-for-azure-search-with-aad.md)  | V tomto článku se rozbalí předchozí článek, který poskytuje kroky pro načtení identit z Azure Active Directory (AAD), jednu z [bezplatných služeb](https://azure.microsoft.com/free/) na cloudové platformě Azure. |

## <a name="table-permissioned-operations"></a>Tabulka: oprávněné operace

V následující tabulce najdete souhrn operací povolených v Azure Kognitivní hledání a který klíč odemkne přístup k určité operaci.

| Operace | Oprávnění |
|-----------|-------------------------|
| Vytvoření služby | Vlastník předplatného Azure|
| Škálování služby | Klíč správce, vlastník RBAC nebo přispěvatel v prostředku  |
| Odstranění služby | Klíč správce, vlastník RBAC nebo přispěvatel v prostředku |
| Vytváření, úpravy a odstraňování objektů ve službě: <br>Indexy a části komponent (včetně definic analyzátoru, profilů vyhodnocování, možností CORS), indexerů, zdrojů dat, synonym a návrhů. | Klíč správce, vlastník RBAC nebo přispěvatel v prostředku  |
| Dotazování indexu | Správce nebo klíč dotazu (RBAC není k dispozici) |
| Dotaz na systémové informace, jako je například vrácení statistik, počtů a seznamů objektů. | Klíč správce, RBAC na prostředku (vlastník, přispěvatel, čtenář) |
| Správa klíčů pro správu | Klíč správce, vlastník RBAC nebo přispěvatel na prostředku |
| Správa klíčů dotazů |  Klíč správce, vlastník RBAC nebo přispěvatel na prostředku  |

## <a name="physical-security"></a>Fyzické zabezpečení

Datová centra Microsoftu poskytují špičkové fyzické zabezpečení a jsou v souladu s rozsáhlým portfoliem standardů a předpisů. Pokud se chcete dozvědět víc, podívejte se na stránku [globální datová centra](https://www.microsoft.com/cloud-platform/global-datacenters) nebo sledujte krátké video o zabezpečení datového centra.

> [!VIDEO https://www.youtube.com/embed/r1cyTL8JqRg]


## <a name="see-also"></a>Další informace najdete v tématech

+ [Začínáme s .NET (ukazuje použití klíče správce k vytvoření indexu)](search-create-index-dotnet.md)
+ [Začínáme REST (ukazuje použití klíče správce k vytvoření indexu)](search-create-index-rest-api.md)
+ [Řízení přístupu na základě identity pomocí filtrů Azure Kognitivní hledání](search-security-trimming-for-azure-search.md)
+ [Řízení přístupu na základě identity ve službě Active Directory s využitím filtrů Azure Kognitivní hledání](search-security-trimming-for-azure-search-with-aad.md)
+ [Filtry v Azure Kognitivní hledání](search-filters.md)