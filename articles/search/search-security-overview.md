---
title: Zabezpečení a ochrana osobních údajů
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search je kompatibilní s SOC 2, HIPAA a dalšími certifikacemi. Šifrování připojení a dat, ověřování a přístup k identitě prostřednictvím identifikátorů zabezpečení uživatelů a skupin ve výrazech filtru.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/25/2020
ms.openlocfilehash: 6de6f23fe9564b28a5d436ac00999dbb3e9183e1
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548964"
---
# <a name="security-and-data-privacy-in-azure-cognitive-search"></a>Zabezpečení a ochrana osobních údajů v Azure Cognitive Search

Komplexní funkce zabezpečení a ovládací prvky přístupu jsou integrované do Azure Cognitive Search, aby zajistily, že soukromý obsah zůstane tímto způsobem. Tento článek vyjmenovává funkce zabezpečení a dodržování standardů integrované do Azure Cognitive Search.

Architektura zabezpečení Azure Cognitive Search zahrnuje fyzické zabezpečení, šifrované přenosy, šifrované úložiště a dodržování standardů na celé platformě. Z provozního provozu Azure Cognitive Search přijímá pouze ověřené požadavky. Volitelně můžete přidat ovládací prvky přístupu pro jednotlivé uživatele k obsahu prostřednictvím filtrů zabezpečení. Tento článek se dotýká zabezpečení v každé vrstvě, ale primárně se zaměřuje na zabezpečení dat a operací v Azure Cognitive Search.

## <a name="standards-compliance-iso-27001-soc-2-hipaa"></a>Dodržování norem: ISO 27001, SOC 2, HIPAA

Azure Cognitive Search je certifikován pro následující standardy, jak [bylo oznámeno v červnu 2018](https://azure.microsoft.com/blog/azure-search-is-now-certified-for-several-levels-of-compliance/):

+ [ISO 27001:2013](https://www.iso.org/isoiec-27001-information-security.html) 
+ [Soc 2 typ 2 shody](https://www.aicpa.org/interestareas/frc/assuranceadvisoryservices/aicpasoc2report.html) Celou zprávu najdete na [Azure – a Azure Government SOC 2 typ II sestavy](https://servicetrust.microsoft.com/ViewPage/MSComplianceGuide?command=Download&downloadType=Document&downloadId=93292f19-f43e-4c4e-8615-c38ab953cf95&docTab=4ce99610-c9c0-11e7-8c2c-f908a777fa4d_SOC%20%2F%20SSAE%2016%20Reports). 
+ [Zákon o přenositelnosti a odpovědnosti zdravotního pojištění (HIPAA)](https://en.wikipedia.org/wiki/Health_Insurance_Portability_and_Accountability_Act)
+ [GxP (21 CFR Část 11)](https://en.wikipedia.org/wiki/Title_21_CFR_Part_11)
+ [HITRUST](https://en.wikipedia.org/wiki/HITRUST)
+ [PCI DSS úrovně 1](https://en.wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard)
+ [Austrálie IRAP Neklasifikované DLM](https://asd.gov.au/infosec/irap/certified_clouds.htm)

Dodržování standardů se vztahuje na obecně dostupné funkce. Funkce náhledu jsou certifikovány při přechodu na obecnou dostupnost a nesmí být používány v řešeních s přísnými požadavky na standardy. Certifikace dodržování předpisů je zdokumentována v [přehledu dodržování předpisů Microsoft Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) a v Centru [zabezpečení](https://www.microsoft.com/en-us/trustcenter). 

## <a name="encrypted-transmission-and-storage"></a>Šifrovaný přenos a ukládání

Šifrování se rozšiřuje v celém kanálu indexování: od připojení, přes přenos až po indexovaná data uložená v Azure Cognitive Search.

| Vrstva zabezpečení | Popis |
|----------------|-------------|
| Šifrování během přenosu <br>(HTTPS/SSL/TLS) | Azure Cognitive Search naslouchá na portu HTTPS 443. Napříč platformou se připojení ke službám Azure šifrují. <br/><br/>Všechny interakce Azure Cognitive Search mezi klientem a službami jsou schopné SSL/TLS 1.2.  Nezapomeňte použít TLSv1.2 pro připojení SSL k vaší službě.|
| Šifrování v klidovém stavu <br>Spravované klíče společnosti Microsoft | Šifrování je plně internalizovánv procesu indexování, bez měřitelné dopad na indexování čas dokončení nebo velikost indexu. Dochází automaticky na všechny indexování, včetně přírůstkové aktualizace indexu, který není plně šifrována (vytvořené před lednem 2018).<br><br>Interně je šifrování založené na [šifrování služby Azure Storage Service](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)pomocí 256bitového šifrování [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard).<br><br> Šifrování je interní azure cognitive search, s certifikáty a šifrovací klíče spravované interně společností Microsoft a univerzálně aplikované. Šifrování nelze zapnout ani vypnout, spravovat nebo nahradit vlastní klíče ani zobrazovat nastavení šifrování na portálu nebo programově.<br><br>Šifrování v klidovém stavu bylo oznámeno v lednu 24, 2018 a platí pro všechny úrovně služeb, včetně volné úrovně, ve všech oblastech. Pro úplné šifrování indexy vytvořené před tímto datem musí být vynechány a znovu sestaveny, aby k šifrování došlo. V opačném případě jsou šifrována pouze nová data přidaná po 24.|
| Šifrování v klidovém stavu <br>Klíče spravované zákazníkem | Šifrování pomocí klíčů spravovaných zákazníkem je nyní obecně dostupné pro vyhledávací služby vytvořené v lednu 2019 nebo po něm. Není podporována u bezplatných (sdílených) služeb.<br><br>Indexy Azure Cognitive Search a mapy synonym teď můžou být šifrované v klidovém stavu pomocí klíčů spravovaných zákazníky v azure key vaultu. Další informace najdete [v tématu Správa šifrovacích klíčů v Azure Cognitive Search](search-security-manage-encryption-keys.md).<br><br>Tato funkce nenahrazuje výchozí šifrování v klidovém stavu, ale spíše se používá kromě něj.<br><br>Povolením této funkce se zvýší velikost indexu a sníží výkon dotazu. Na základě pozorování k dnešnímu dni můžete očekávat zvýšení o 30%-60% v době dotazu, i když skutečný výkon se bude lišit v závislosti na definici indexu a typy dotazů. Z důvodu tohoto dopadu na výkon doporučujeme povolit tuto funkci pouze na indexy, které skutečně vyžadují.

## <a name="azure-wide-user-access-controls"></a>Ovládací prvky přístupu uživatelů pro celý Azure

Několik mechanismů zabezpečení jsou k dispozici v celém Azure, a proto automaticky k dispozici pro azure cognitive search prostředky, které vytvoříte.

+ [Uzamkne na úrovni předplatného nebo prostředků, aby se zabránilo odstranění](../azure-resource-manager/management/lock-resources.md)
+ [Řízení přístupu na základě rolí (RBAC) pro řízení přístupu k informacím a administrativníoperace](../role-based-access-control/overview.md)

Všechny služby Azure podporují ovládací prvky přístupu založené na rolích (RBAC) pro nastavení úrovní přístupu konzistentně ve všech službách. Například zobrazení citlivých dat, jako je klíč správce, je omezeno na role vlastníka a přispěvatele. Zobrazení stavu služby je však k dispozici členům libovolné role. RBAC poskytuje role vlastníka, přispěvatele a čtenáře. Ve výchozím nastavení jsou všichni správci služeb členy role Vlastník.

<a name="service-access-and-authentication"></a>

## <a name="endpoint-access"></a>Přístup ke koncovému bodu

### <a name="public-access"></a>Přístup veřejnosti

Azure Cognitive Search dědí zabezpečení platformy Azure a poskytuje vlastní ověřování na základě klíče. Klíč rozhraní api je řetězec složený z náhodně generovaných čísel a písmen. Úroveň přístupu určuje typ klíče (správce nebo dotaz). Odeslání platného klíče je považováno za důkaz, že požadavek pochází od důvěryhodné entity. 

K vyhledávací službě mají přístup ke dvěma úrovním, které umožňují dva typy klíčů:

* Přístup správce (platný pro všechny operace čtení a zápisu proti službě)
* Přístup k dotazu (platný pro operace jen pro čtení, například dotazy, proti shromažďování dokumentů indexu)

*Klíče správce* se vytvoří při zřízení služby. Existují dva klíče správce, označené jako *primární* a *sekundární,* aby byly rovné, ale ve skutečnosti jsou zaměnitelné. Každá služba má dva klíče správce, takže můžete převrátit jeden přes bez ztráty přístupu ke službě. Klíč správce můžete pravidelně [regenerovat](search-security-api-keys.md#regenerate-admin-keys) podle doporučených postupů zabezpečení Azure, ale nemůžete přidat k celkovému počtu klíčů správce. Na vyhledávací službu jsou k dispozici maximálně dva klíče správce.

*Klávesy dotazu* jsou vytvořeny podle potřeby a jsou určeny pro klientské aplikace, které vydávají dotazy. Můžete vytvořit až 50 kláves dotazu. V kódu aplikace zadáte adresu URL hledání a klíč rozhraní api dotazu, který umožní přístup jen pro čtení ke shromažďování dokumentů určitého indexu. Společně koncový bod, klíč rozhraní api pro přístup jen pro čtení a cílový index definovat rozsah a úroveň přístupu připojení z klientské aplikace.

Ověřování je vyžadováno u každého požadavku, kde každý požadavek se skládá z povinného klíče, operace a objektu. Při zřetězení dohromady, dvě úrovně oprávnění (úplné nebo jen pro čtení) plus kontext (například operace dotazu na indexu) jsou dostatečné pro poskytování zabezpečení celého spektra operací služby. Další informace o klíčích naleznete v [tématu Vytvoření a správa klíčů rozhraní API](search-security-api-keys.md).

### <a name="restricted-access"></a>Omezený přístup

Pokud máte veřejnou službu a chcete omezit používání služby, můžete použít pravidlo omezení IP ve verzi rozhraní REST API pro správu: 2020-03-13, [IpRule](https://docs.microsoft.com/rest/api/searchmanagement/2019-10-01-preview/createorupdate-service#IpRule). IpRule umožňuje omezit přístup k vaší službě tím, že identifikuje IP adresy, jednotlivě nebo v rozsahu, který chcete udělit přístup k vaší vyhledávací služby. 

### <a name="private-access"></a>Soukromý přístup

[Privátní koncové body](https://docs.microsoft.com/azure/private-link/private-endpoint-overview) pro Azure Cognitive Search umožňují klientovi ve virtuální síti zabezpečit přístup k datům v indexu vyhledávání přes [privátní propojení](https://docs.microsoft.com/azure/private-link/private-link-overview). Privátní koncový bod používá IP adresu z adresního prostoru virtuální sítě pro vyhledávací službu. Síťový provoz mezi klientem a vyhledávací službou prochází přes virtuální síť a privátní propojení v páteřní síti společnosti Microsoft, což eliminuje expozici z veřejného internetu.

[Virtuální síť Azure (VNet)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) umožňuje zabezpečenou komunikaci mezi prostředky, s místní sítí i internetem. 

## <a name="index-access"></a>Přístup k indexu

V Azure Cognitive Search, jednotlivé index není sevyléčitelný objekt. Místo toho je přístup k indexu určen ve vrstvě služby (přístup pro čtení nebo zápis) spolu s kontextem operace.

Pro přístup koncových uživatelů můžete strukturovat požadavky na dotazy pro připojení pomocí klíče dotazu, který umožňuje jakýkoli požadavek jen pro čtení, a zahrnout konkrétní index používaný vaší aplikací. V požadavku na dotaz neexistuje žádný koncept spojování indexů nebo přístupu k více indexů současně, takže všechny požadavky cílí na jeden index podle definice. Jako takové konstrukce samotného požadavku na dotaz (klíč plus jeden cílový index) definuje hranici zabezpečení.

Přístup správce a vývojáře k indexům je nediferencovaný: oba potřebují přístup pro zápis k vytvoření, odstranění a aktualizaci objektů spravovaných službou. Každý, kdo má klíč správce vaší služby, může číst, upravovat nebo odstraňovat libovolný index ve stejné službě. Pro ochranu proti náhodnému nebo škodlivému odstranění indexů je interní správa zdrojového kódu pro datové zdroje kódu lékem na zrušení nechtěného odstranění nebo úpravy indexu. Azure Cognitive Search má převzetí služeb při selhání v rámci clusteru k zajištění dostupnosti, ale neukládá ani nespustí váš proprietární kód používaný k vytvoření nebo načtení indexů.

U víceklientské řešení, která vyžadují hranice zabezpečení na úrovni indexu, tato řešení obvykle zahrnují střední vrstvu, kterou zákazníci používají ke zpracování izolace indexu. Další informace o případu použití více klientů naleznete v [tématu Návrhové vzory pro víceklientské aplikace SaaS a Azure Cognitive Search](search-modeling-multitenant-saas-applications.md).

## <a name="authentication"></a>Authentication

### <a name="admin-access"></a>Přístup správce

[Přístup založený na rolích (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview) určuje, zda máte přístup k ovládacím prvkům nad službou a jejím obsahem. Pokud jste vlastníkem nebo přispěvatelem ve službě Azure Cognitive Search, můžete pomocí portálu nebo modulu PowerShell **Az.Search** vytvářet, aktualizovat nebo odstraňovat objekty ve službě. Můžete také použít [rozhraní REST API azure cognitive search management .](https://docs.microsoft.com/rest/api/searchmanagement/search-howto-management-rest-api)

### <a name="user-access"></a>Přístup uživatelů

Ve výchozím nastavení je přístup uživatelů k indexu určen přístupovým klíčem v požadavku na dotaz. Většina vývojářů vytváří a přiřazuje [*klíče dotazů*](search-security-api-keys.md) pro požadavky na vyhledávání na straně klienta. Klíč dotazu uděluje přístup pro čtení k veškerému obsahu v rámci indexu.

Pokud požadujete podrobnou kontrolu obsahu podle uživatele, můžete na dotazech vytvořit filtry zabezpečení a vrátit dokumenty přidružené k dané identitě zabezpečení. Namísto předdefinovaných rolí a přiřazení rolí je řízení přístupu založené na identitě implementováno jako *filtr,* který ořízne výsledky hledání dokumentů a obsahu na základě identit. Následující tabulka popisuje dva přístupy k oříznutí výsledků hledání neautorizovaného obsahu.

| Přístup | Popis |
|----------|-------------|
|[Oříznutí zabezpečení na základě filtrů identity](search-security-trimming-for-azure-search.md)  | Dokumentuje základní pracovní postup pro implementaci řízení přístupu k identitě uživatele. Zahrnuje přidání identifikátorů zabezpečení do indexu a potom vysvětluje filtrování proti tomuto poli za účelem oříznutí výsledků zakázaného obsahu. |
|[Oříznutí zabezpečení na základě identit služby Azure Active Directory](search-security-trimming-for-azure-search-with-aad.md)  | Tento článek rozšiřuje o předchozí článek, který poskytuje kroky pro načítání identit z Azure Active Directory (AAD), jedné z [bezplatných služeb](https://azure.microsoft.com/free/) v cloudové platformě Azure. |

## <a name="table-permissioned-operations"></a>Tabulka: Operace s oprávněním

Následující tabulka shrnuje operace povolené v Azure Cognitive Search a klíč odemkne přístup k určité operaci.

| Operace | Oprávnění |
|-----------|-------------------------|
| Vytvoření služby | Držitel předplatného Azure|
| Škálování služby | Klíč správce, vlastník RBAC nebo přispěvatel prostředku  |
| Odstranění služby | Klíč správce, vlastník RBAC nebo přispěvatel prostředku |
| Vytvářet, upravovat, odstraňovat objekty ve službě: <br>Indexy a součásti (včetně definic analyzátorů, profilů hodnocení, možností CORS), indexerů, zdrojů dat, synonym, návrhovek. | Klíč správce, vlastník RBAC nebo přispěvatel prostředku  |
| Dotaz na index | Klíč správce nebo dotazu (RBAC není použitelný) |
| Informace o systému dotazů, jako jsou například vracející se statistiky, počty a seznamy objektů. | Klíč správce, RBAC na prostředek (vlastník, přispěvatel, čtenář) |
| Správa klíčů správce | Klíč správce, vlastník RBAC nebo přispěvatel prostředku. |
| Správa klíčů dotazu |  Klíč správce, vlastník RBAC nebo přispěvatel prostředku.  |

## <a name="physical-security"></a>Fyzické zabezpečení

Datová centra Microsoftu poskytují špičkové fyzické zabezpečení a jsou v souladu s rozsáhlým portfoliem standardů a předpisů. Další informace najdete na stránce [Globální datová centra](https://www.microsoft.com/cloud-platform/global-datacenters) nebo se podívejte na krátké video o zabezpečení datových center.

> [!VIDEO https://www.youtube.com/embed/r1cyTL8JqRg]


## <a name="see-also"></a>Viz také

+ [Začínáme .NET (ukazuje použití klíče správce k vytvoření indexu)](search-create-index-dotnet.md)
+ [Začínáme REST (ukazuje použití klíče správce k vytvoření indexu)](search-create-index-rest-api.md)
+ [Řízení přístupu založené na identitě pomocí filtrů Azure Cognitive Search](search-security-trimming-for-azure-search.md)
+ [Řízení přístupu založené na identitách služby Active Directory pomocí filtrů Azure Cognitive Search](search-security-trimming-for-azure-search-with-aad.md)
+ [Filtry v Azure Cognitive Search](search-filters.md)