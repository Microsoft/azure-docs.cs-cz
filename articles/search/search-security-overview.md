---
title: Zabezpečení dat a operace ve službě Azure Search | Microsoft Docs
description: Zabezpečení služby Azure Search podle SOC 2 dodržování předpisů, šifrování, ověřování a identita přístupu pomocí uživatele a skupiny identifikátory zabezpečení v Azure Search filtry.
services: search
documentationcenter: ''
author: HeidiSteen
manager: cgronlun
editor: ''
ms.assetid: ''
ms.service: search
ms.devlang: ''
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 01/19/2018
ms.author: heidist
ms.openlocfilehash: 35f875e5f6345b9ebb9abc4deb71b7bf9c78907d
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="security-and-controlled-access-in-azure-search"></a>Zabezpečení a řízený přístup do služby Azure Search

Služba Azure Search je [SOC 2 kompatibilní](https://servicetrust.microsoft.com/ViewPage/MSComplianceGuide?command=Download&downloadType=Document&downloadId=93292f19-f43e-4c4e-8615-c38ab953cf95&docTab=4ce99610-c9c0-11e7-8c2c-f908a777fa4d_SOC%20%2F%20SSAE%2016%20Reports), s komplexní zabezpečení architektura STA fyzické zabezpečení, šifrované přenosy, šifrované úložiště a ochrana softwaru celou platformu. Azure Search operačně přijímá pouze ověřené žádosti. Volitelně můžete přidat uživatelská řízení přístupu na obsah. Tento článek dotykem na zabezpečení v každé vrstvě, ale se zaměřuje především na tom, jak jsou zabezpečené data a operace ve službě Azure Search.

![Blokový diagram serveru vrstvy zabezpečení](media/search-security-overview/azsearch-security-diagram.png)

## <a name="physical-security"></a>Fyzické zabezpečení

Datovými centry Microsoftu špičkový fyzické zabezpečení a jsou kompatibilní s rozsáhlé portfolií standardů a nařízení. Pokud chcete dozvědět víc, přejděte na [globálních datových centrech](https://www.microsoft.com/cloud-platform/global-datacenters) stránky nebo shlédnout krátké video na data center zabezpečení.

> [!VIDEO https://www.youtube.com/embed/r1cyTL8JqRg]

## <a name="encrypted-transmission-and-storage"></a>Šifrované přenosu a uložení

Šifrování rozšiřuje v rámci celého kanálu indexování: od připojení, prostřednictvím přenosu a dolů indexovaná data uložená ve službě Azure Search.

| Vrstva zabezpečení | Popis |
|----------------|-------------|
| Šifrování během přenosu | Služba Azure Search naslouchá na portu HTTPS 443. Na platformě jsou šifrované připojení ke službám Azure. |
| Šifrování v klidovém stavu | Šifrování je plně internalized do procesu vytváření indexu se žádné měřitelný dopad na indexování čas dokončení nebo velikost indexu. Automaticky se objeví na všechny indexování, včetně přírůstkové aktualizace index, který není plně zašifrované (vytvořených před leden 2018).<br><br>Interně, je šifrování na základě [šifrování služby úložiště Azure](https://docs.microsoft.com/azure/storage/common/storage-service-encryption), pomocí 256 bitů [šifrování AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard).|
| [Dodržování předpisů SOC 2](https://www.aicpa.org/interestareas/frc/assuranceadvisoryservices/aicpasoc2report.html) | Všechny služby vyhledávání jsou plně AICPA SOC 2 předpisy, na všech datových center, které poskytuje Azure Search. Chcete-li zkontrolovat úplná sestava, přejděte na [Azure – a Azure Government SOC 2 typ II sestava](https://servicetrust.microsoft.com/ViewPage/MSComplianceGuide?command=Download&downloadType=Document&downloadId=93292f19-f43e-4c4e-8615-c38ab953cf95&docTab=4ce99610-c9c0-11e7-8c2c-f908a777fa4d_SOC%20%2F%20SSAE%2016%20Reports). |

Šifrování je interní do služby Azure Search s certifikáty a šifrovacích klíčů interně spravuje Microsoft a použít univerzálně. Nelze zapnout šifrování nebo vypnout, spravovat nebo nahraďte váš vlastní klíče nebo zobrazit nastavení šifrování na portálu nebo prostřednictvím kódu programu. 

Šifrování v klidovém stavu byl oznámen v 24 leden 2018 a platí pro všechny úrovně služeb, včetně sdílených služeb (zdarma), ve všech oblastech. Úplné šifrování musí být indexy vytvořené před tímto datem vyřadit a znovu sestavit v pořadí pro šifrování proběhnout. Jinak se šifrují jenom nová data přidány po leden 24.

## <a name="azure-wide-logical-security"></a>Zabezpečení logické Azure

Několik mechanismů zabezpečení jsou dostupné přes zásobník Azure a proto automaticky dostupné pro prostředky Azure Search, které vytvoříte.

+ [Zámky na úrovni prostředků, aby se zabránilo odstranění nebo předplatné](../azure-resource-manager/resource-group-lock-resources.md)
+ [Na základě rolí řízení přístupu (RBAC) pro řízení přístupu k informacím a operace správy](../active-directory/role-based-access-control-what-is.md)

Nastavení úrovně přístupu konzistentně ve všech službách podpora všech služeb Azure řízení přístupu na základě role (RBAC). Například zobrazení citlivých dat, jako například klíč správce, je omezen na vlastníka a Přispěvatel role, zatímco zobrazení stavu služby je k dispozici pro členy žádné role. RBAC poskytuje vlastník, Přispěvatel a čtečky rolí. Ve výchozím nastavení všechny Správci služeb jsou členy role vlastníka.

## <a name="service-access-and-authentication"></a>Přístup k službě a ověřování

Zatímco Azure Search dědí zabezpečení bezpečnostní opatření na platformě Azure, je také vlastní ověřování na základě klíčů. Klíč rozhraní api je řetězec tvořený náhodně generované číslic a písmen. Typ klíče (správce nebo dotazu) určuje úroveň přístupu. Odeslání platný klíč je považovaný za ověření požadavek pochází z důvěryhodné entity. Dva typy klíčů se používají k přístupu ke službě vyhledávání:

* Správce (platné pro všechny operace čtení a zápis Service)
* Dotaz (platné pro operace jen pro čtení, například dotazy pro index)

Klíče správce vytvářejí, když služba je zřízený. Existují dva klíče správce, určený jako *primární* a *sekundární* k jejich přímo, ale ve skutečnosti jsou zaměnitelné. Každá služba má dva klíče správce, takže můžete se vrátit jednu bez ztráty přístupu k službě. Můžete obnovit buď klíč správce, ale nemůžete přidat počet klíčů celkový správce. Je delší než dva klíče správce jednu službu vyhledávání.

Klíče dotazu se vytvoří podle potřeby a jsou určené pro klientské aplikace, které volají přímo vyhledávání. Můžete vytvořit až 50 klíče dotazu. V kódu aplikace zadejte adresu URL vyhledávání a dotazu api-key povolit přístup jen pro čtení ke službě. Kód aplikace také určuje index používá vaše aplikace. Společně koncový bod, klíč rozhraní api pro přístup jen pro čtení a cílový index definovat úroveň oboru a přístup připojení z klientské aplikace.

Na každý požadavek, kde každý požadavek se skládá z povinných klíč, operace a objekt se vyžaduje ověření. Když zřetězen dohromady, je dostatečné pro zajištění zabezpečení úplné spektrum operací služby dvě úrovně oprávnění (úplná nebo jen pro čtení) a kontext (například operace dotazu na index). Další informace o klíčích najdete v tématu [vytvořit a spravovat klíče api Key](search-security-api-keys.md).

## <a name="index-access"></a>Index přístup

Ve službě Azure Search jednotlivé indexu není zabezpečitelných objektů. Místo toho přístup k indexu je určen ve vrstvě služby (přístup pro čtení nebo zápisu), spolu s kontext operace.

V případě přístupem koncových uživatelů můžete struktury požadavků na dotazy v aplikaci a připojte se pomocí klíč dotazu, který vytvoří každá žádost jen pro čtení a zahrnovat konkrétního indexu používané vaší aplikace. V žádosti o dotaz neexistuje žádná koncepce připojení indexy nebo přístupu k nim více indexů současně, všechny požadavky cíle jeden index podle definice. Struktura dotaz na žádost (klíč a jeden cílový index) jako takový definuje hranice zabezpečení.

Správce a vývojáře přístup k indexů je poskytujících blíže neurčené: obě potřebovat přístup pro zápis k vytvoření, odstranění a aktualizovat objekty spravované službou. Každý, kdo má klíč správce k službě můžou číst, upravit nebo odstranit žádný index v rámci stejné služby. Pro ochranu proti náhodnému nebo škodlivý odstranění indexů je vaše interní zdrojového kódu pro kód prostředky remedy zpětné nežádoucí index, odstranění nebo úpravy. Vyhledávání systému Azure má převzetí služeb při selhání v rámci clusteru, aby se zajistila dostupnost, ale nemá uložení nebo provést vlastní kódu umožňuje vytvořit nebo načíst indexy.

Taková řešení pro vyžadování hranic zabezpečení na úrovni index víceklientské architektury řešení, měl obvykle zahrnovat střední vrstvy, uvedeni zákazníci, kteří používala pro zpracování izolace index. Další informace o případ víceklientské použití najdete v tématu [vzory pro víceklientské aplikace SaaS a Azure Search návrhu](search-modeling-multitenant-saas-applications.md).

## <a name="admin-access-from-client-apps"></a>Přístup správce z klientské aplikace

REST API služby Azure Search správy je rozšíření Azure Resource Manager a sdílí jeho závislé součásti. Služba Active Directory jako takový je předpokladem pro správu služby Azure Search. Všechny požadavky na správu z kódu klienta musí být ověřeny pomocí Azure Active Directory předtím, než požadavek dosáhne Resource Manager.

Požadavky na data na koncový bod Azure Search služby, jako je vytvoření indexu (služby REST API Azure Search) nebo vyhledávání dokumentů (služby REST API Azure Search), použijte klíč rozhraní api v hlavičce žádosti.

Pokud kód aplikace zpracovává operace správy služeb a také data operací na indexy hledání nebo dokumenty, implementace dva přístupy k ověřování v kódu: nativní Azure Search a ověřování služby Active Directory přístupového klíče metodologie nutné pomocí Správce prostředků. 

Informace o vytváření struktury žádost ve službě Azure Search najdete v tématu [REST služby Azure Search](https://docs.microsoft.com/rest/api/searchservice/). Další informace o požadavcích na ověřování pro službu správce prostředků najdete v tématu [ověřování pomocí Správce prostředků rozhraní API k přístupu k odběrům](../azure-resource-manager/resource-manager-api-authentication.md).

## <a name="user-access-to-index-content"></a>Uživatelský přístup k obsahu indexu

Uživatelská přístup k obsahu indexu se implementuje pomocí filtrů zabezpečení na své dotazy, vrácení dokumenty spojené s danou zabezpečení identity. Místo předdefinované role a přiřazení rolí je řízení přístupu na základě identity implementovaný jako filtr, že výsledky dokumentů a obsah podle identity hledání ořízne. Následující tabulka popisuje dva přístupy pro výsledky hledání oříznutí neoprávněným obsahu.

| Přístup | Popis |
|----------|-------------|
|[Oříznutí zabezpečení podle identity filtry](search-security-trimming-for-azure-search.md)  | Dokumenty základní pracovní postup pro implementaci řízení přístupu identity uživatele. Popisuje přidání identifikátory zabezpečení do indexu a pak vysvětluje filtrování toto pole oříznout výsledky zakázaný obsah. |
|[Oříznutí zabezpečení podle identit Azure Active Directory](search-security-trimming-for-azure-search-with-aad.md)  | Tento článek rozšíří na předchozím článku, konkrétně postup pro načítání identit z Azure Active Directory (AAD), mezi [bezplatné služby](https://azure.microsoft.com/free/) v platformě Azure cloud. |

## <a name="table-permissioned-operations"></a>Tabulkou: Permissioned operace

Následující tabulka shrnuje operace povolené ve službě Azure Search a který klíč odemkne přístupu konkrétním operaci.

| Operace | Oprávnění |
|-----------|-------------------------|
| Vytvoření služby | Držitel předplatného Azure|
| Škálování služby | Klíč správce, RBAC vlastníkem nebo přispěvatelem na prostředek  |
| Odstranění služby | Klíč správce, RBAC vlastníkem nebo přispěvatelem na prostředek |
| Vytvořit, upravit, odstranit objekty služby: <br>Indexy a součásti (včetně definice analyzátor, vyhodnocování profily, možnosti CORS), indexery, zdroje dat, synonyma, trochu. | Klíč správce, RBAC vlastníkem nebo přispěvatelem na prostředek  |
| Dotazování indexu | Správce nebo dotaz, klíč (RBAC není k dispozici) |
| Dotaz na informace o systému, jako je například vrácení statistiky, počty a seznam objektů. | Klíč správce, RBAC na prostředku (vlastník, Přispěvatel, čtečky) |
| Správa Správce klíčů | Klíč správce, RBAC vlastníkem nebo přispěvatelem u daného prostředku. |
| Spravovat klíče dotazů |  Klíč správce, RBAC vlastníkem nebo přispěvatelem u daného prostředku.  |


## <a name="see-also"></a>Další informace najdete v tématech

+ [Získat spuštění rozhraní .NET (ukazuje, jak vytvořit index pomocí klíč správce)](search-create-index-dotnet.md)
+ [Získat spuštění REST (ukazuje, jak vytvořit index pomocí klíč správce)](search-create-index-rest-api.md)
+ [Řízení přístupu na základě identit pomocí filtrů Azure Search](search-security-trimming-for-azure-search.md)
+ [Řízení přístupu na základě identity Active Directory, které jsou pomocí filtrů Azure Search](search-security-trimming-for-azure-search-with-aad.md)
+ [Filtry ve službě Azure Search](search-filters.md)