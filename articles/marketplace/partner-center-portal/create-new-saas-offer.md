---
title: Vytvoření nové nabídky SaaS na komerčním webu Marketplace
description: Postup vytvoření nové nabídky software jako služba (SaaS) pro výpis nebo prodej v Azure Marketplace, AppSource nebo prostřednictvím programu poskytovatel cloudového řešení (CSP) pomocí portálu pro komerční tržiště na partnerském centru Microsoftu.
author: ChJenk
manager: evansma
ms.author: v-chjen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/22/2020
ms.openlocfilehash: 4cac2fcd16f779a6b31e36ca175801eedaa31d5d
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2020
ms.locfileid: "76705815"
---
# <a name="create-a-new-saas-offer"></a>Vytvořit novou nabídku SaaS

Pokud chcete začít vytvářet nabídky software jako služba (SaaS), ujistěte se, že jste nejdřív [vytvořili účet partnerského centra](./create-account.md) a otevřete [řídicí panel komerčního tržiště](https://partner.microsoft.com/dashboard/commercial-marketplace/offers)s vybranou kartou **Přehled** .

![Řídicí panel komerčního tržiště v partnerském centru](./media/new-offer-overview.png)

>[!Note]
> Po publikování nabídky se úpravy nabídky provedené v partnerském centru aktualizují jenom v systému a po opětovném publikování se uloží i přední. Až provedete změny, nezapomeňte tuto nabídku odeslat pro publikování.

Vyberte nabídku + **Nová...** a potom vyberte položku nabídky **software jako služba** .

Pokud vyberete jiný typ nabídky, budete možná přesměrováni na starší [portál partnerů cloudu](https://cloudpartner.azure.com/). V tomto okamžiku jsou na portálu pro komerční tržiště k dispozici pouze nabídky SaaS a Dynamics 365 v partnerském centru.

![Vytvořit okno nabídky na partnerském centru](./media/new-offer-click.png)

Zobrazí se dialogové okno **Nová nabídka** .

![Dialogové okno Nová nabídka](./media/new-offer-popup.png)

## <a name="offer-id-and-alias"></a>ID nabídky a alias

- **ID nabídky**: jedinečný identifikátor pro každou nabídku ve vašem účtu. Toto ID uvidí zákazníci v adrese URL pro nabídku Marketplace a šablony Azure Resource Manager (pokud jsou k dispozici). ID nabídky musí být malé, alfanumerické (včetně spojovníků a podtržítka, ale bez mezer). **ID nabídky** je omezené na 50 znaků a po výběru *vytvořit*se nedá změnit.  
Příklad: test-nabídka-1
<br>Výsledkem je adresa URL: `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`

- **Alias nabídky**: název, který se používá k odkazování na nabídku v rámci portálu partnerského centra. Tento název se na webu Marketplace nepoužívá a liší se od *názvu nabídky* a dalších hodnot, které se zákazníkům zobrazí. Tuto hodnotu nelze změnit po výběru možnost *vytvořit*.

<br>Příklad: nabídka testu 1&#8482;

Vyberte **Create** (Vytvořit).  Pro tuto nabídku se vytvoří stránka s **přehledem nabídky** .  

<!---
![Offer overview on Partner Center](./media/commercial-marketplace-offer-overview.png)
-->

## <a name="offer-overview"></a>Přehled nabídky

Stránka **Přehled nabídky** obsahuje:

- **Stav publikování** zobrazí vizuální znázornění kroků požadovaných k publikování této nabídky a dobu, po kterou bude každý krok trvat. Nedokončené ikony kroků publikování budou šedé.

- Nabídka **Přehled** nabídky obsahuje seznam odkazů pro provádění operací s touto nabídkou. Tento seznam operací se změní v závislosti na výběru provedeném pro vaši nabídku.  
    - Pokud je nabídka koncept-odstranit koncept
    - Pokud je nabídka živá, přestat prodávat nabídku
    - Pokud je nabídka ve verzi Preview – přejít do provozu
    - Pokud jste nedokončili registraci vydavatele, zrušte publikování

## <a name="offer-setup"></a>Nastavení nabídky

Karta **nastavení nabídky** požádá o následující informace. Po dokončení těchto polí vyberte **Uložit** .

- **Chtěli byste prodávat přes Microsoft?** (Ano/ne)
    - **Ano**, vaši nabídku byste mohli prodávat prostřednictvím Microsoftu, s hostováním transakcí Marketplace Microsoftu pro váš účet; ani 
    - **Ne**, budete chtít, abyste si mohli jenom vypsat nabídku prostřednictvím Marketplace a zpracovávat peněžní transakce nezávisle na Microsoftu.

### <a name="sell-through-microsoft"></a>Prodej prostřednictvím Microsoftu

Prodej prostřednictvím Microsoftu poskytuje lepší zjišťování a nabývání zákazníků, umožňuje Microsoftu hostovat transakce v Marketplace vaším jménem a využívat výhod celosvětově dostupných možností obchodování Microsoftu.

#### <a name="saas-offer-requirements"></a>Požadavky na nabídku SaaS

K vypsání nabídek SaaS (software jako služba) s komerčním Marketplace v partnerském centru je potřeba splnit následující kritéria:

- Vaše nabídka musí používat [Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) pro správu identit a ověřování.
- Vaše nabídka musí používat [rozhraní API pro splnění SaaS](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2) pro integraci s Azure Marketplace.
- Rozsáhlejší požadavky najdete v [příručce pro publikování nabídky SaaS](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide).

#### <a name="saas-pricing-and-billing-options"></a>Ceny SaaS a možnosti fakturace
S SaaS řešeními, která běží v předplatném Azure vydavatele, zahrnují licenční poplatky placené zákazníky náklady na infrastrukturu, na kterou je software nasazený. Využití infrastruktury Azure se spravuje a účtuje přímo na vás, ale partnera. Zákazníky nevidí skutečné poplatky za využití infrastruktury. Vydavatelé by měli na ceny za licence k softwaru seskupit poplatky za využití infrastruktury Azure. 

SaaS nabízí podporu měsíčních nebo ročních faktur na základě paušálního poplatku za uživatele nebo poplatků za využití pomocí účtované fakturační služby. Komerční tržiště Microsoftu pracuje na modelu agentury, na kterém vydavatelé nastavili ceny, zákazníky s Microsoftem a společnost Microsoft za to, že vystavuje poplatky za platební poplatek, účtuje.

Následující tabulka ukazuje příklad rozdělení nákladů a výběrů za účelem předvedení modelu agentury.

|**Náklady na licenci**|**$100 za měsíc**|
|:---|:---|
|Náklady na využití Azure (D1/1 – jádro)|Účtuje se přímo vydavateli, nikoli zákazník.|
|Zákazník se účtuje Microsoftem.|$100,00 za měsíc (Vydavatel musí mít v licenčním poplatku vynaložené nebo předávací náklady na infrastrukturu)|

|**Účty Microsoft**|**$100 za měsíc**|
|:---|:---|
|Společnost Microsoft vám zaplatí 80% vašich licenčních nákladů. <br>**pro kvalifikované aplikace SaaS přináší společnost Microsoft 90% vašich licenčních nákladů* .|$80,00 za měsíc <br>*$* 90,00 za měsíc *|

- V tomto příkladu vám Microsoft účtuje $100,00 pro zákazníka za softwarovou licenci a vyplatí $80,00 pro vydavatele.
- Partneři, kteří mají nárok na **snížený poplatek za službu Marketplace** , uvidí za snížený poplatek za transakce SaaS z května 2019 do června 2020. V tomto scénáři Microsoft účtuje $100,00 pro vaši softwarovou licenci a vyplatí $90,00 vydavateli.

> [!NOTE]
> **Omezený poplatek za službu Marketplace**: u některých nabídek SaaS, které jste publikovali na našem komerčním tržišti, společnost Microsoft sníží poplatek za službu Marketplace z 20% (jak je popsáno v tématu smlouva Microsoft Publisher Agreement) na 10%. Aby vaše nabídka mohla být kvalifikována, musí být alespoň jedna z vašich nabídek označena Microsoftem jako přizpůsobená priorita nebo prodej IP.  Aby bylo možné získat tento snížený poplatek za službu Marketplace na daný měsíc, musí být nárok splněn nejméně pět (5) pracovních dnů před koncem každého kalendářního měsíce.  Snížený poplatek za službu Marketplace se nevztahuje na virtuální počítače, spravované aplikace ani žádné jiné produkty, které jsou dostupné prostřednictvím našeho komerčního tržiště.  Snížený poplatek za službu Marketplace bude dostupný jenom pro kvalifikované nabídky pro poplatky za licence shromážděné Microsoftem mezi 1. května 2019 a 30. června 2020.  Po uplynutí této doby se poplatek za službu Marketplace vrátí do normálního množství.

### <a name="list-through-microsoft"></a>Seznam prostřednictvím Microsoftu

Vytvořte seznam na webu Marketplace a zvyšte své podnikání pomocí Microsoftu. Výběrem této nabídky zobrazíte pouze seznam a nikoli transakční prostřednictvím Microsoftu znamená, že se společnost Microsoft neúčastní přímo v transakcích licencí na software. Není k dispozici žádný přidružený transakční poplatek a vydavatel udržuje 100% jakýchkoli licenčních poplatků za software shromážděných od zákazníka. Vydavatel je však zodpovědný za podporu všech aspektů licenčních transakcí softwaru, mimo jiné: splnění objednávek, měření, fakturace, fakturace, platby a shromažďování.

- **Jak chcete potenciálním zákazníkům pracovat s touto nabídkou pro výpis?**

##### <a name="get-it-now-free"></a>Získat hned (zdarma)
Seznamte se svým zákazníkům zdarma tím, že poskytnete platnou adresu URL (počínaje *protokolem HTTP* nebo *https*), kde můžou získat zkušební verzi prostřednictvím [ověřování jedním kliknutím pomocí Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide#using-azure-active-directory-to-enable-trials).  Příklad: `https://contoso.com/saas-app`

##### <a name="free-trial-listing"></a>Bezplatná zkušební verze (výpis)
Seznamte se s vaší nabídkou pro zákazníky s odkazem na bezplatnou zkušební verzi tím, že poskytnete platnou adresu URL (počínaje *protokolem HTTP* nebo *https*), kde můžou získat zkušební verzi prostřednictvím [ověřování jedním kliknutím pomocí Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide#using-azure-active-directory-to-enable-trials).  Například: `https://contoso.com/trial/saas-app`. Nabídka pro výpis bezplatných zkušebních verzí se vytváří, spravuje a konfiguruje pomocí vaší služby a nemá předplatné spravované Microsoftem.

> [!NOTE]
> Tokeny, které vaše aplikace získá prostřednictvím zkušebního připojení, se dají použít jenom k získání informací o uživateli přes Azure AD k automatizaci vytváření účtů ve vaší aplikaci. Pro ověřování pomocí tohoto tokenu se nepodporují účty Microsoft (MSA).

##### <a name="contact-me"></a>Kontaktujte mě
Shromažďovat kontaktní údaje zákazníka propojením systému pro správu vztahů se zákazníky (CRM). Zákazník bude požádán o oprávnění ke sdílení svých informací. Tyto podrobnosti o zákaznících spolu s názvem nabídky, ID a zdrojem Marketplace, kde si vaši nabídku našli, budou odeslány do systému CRM, který jste nakonfigurovali. Další informace o konfiguraci CRM najdete v tématu [připojení správy zájemců](#connect-lead-management).

## <a name="example-marketplace-offer-listing"></a>Příklad seznamu nabídek Marketplace

![Příklad seznamu nabídek Marketplace s poznámkami](./media/marketplace-offer.svg)

## <a name="enable-a-test-drive"></a>Povolit testovací jednotku

Testovací jednotka představuje skvělý způsob, jak předvést vaši nabídku potenciálním zákazníkům tím, že jim nabídne možnost vyzkoušet si před nákupem. Výsledkem je zvýšená konverze a generace vysoce kvalifikovaných potenciálních zákazníků. [Přečtěte si další informace o testovacích jednotkách.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)

- **Povolit testovací jednotku** (zaškrtávací políčko)

Povolením testovacích jednotek budete požádáni o konfiguraci demonstračního prostředí pro zákazníky, kteří budou chtít vaši nabídku vyzkoušet na určitou dobu. 

### <a name="type-of-test-drive"></a>Typ testovací jednotky

- **[Azure Resource Manager](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)** : Šablona nasazení obsahující všechny prostředky Azure, které tvoří vaše řešení. Produkty, které vyhovují tomuto scénáři, používají pouze prostředky Azure.
- **[Dynamics 365 for Business Central](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-business-central-offer)** : Microsoft hostuje a udržuje službu testovacích jednotek (včetně zřizování a nasazení) pro podnikový systém plánování podnikových prostředků (finance, operace, dodavatelský řetězec, CRM atd.).  
- **[Dynamics 365 for Customer Engagement](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/dyn365ce/cpp-customer-engagement-offer)** : Microsoft hostuje a udržuje službu testovacích jednotek (včetně zřizování a nasazení) pro systém zákaznických rezervací (prodej, servis, Project Service, Field Service atd.).  
- **[Dynamics 365 pro operace](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-dynamics-365-operations-offer)** : Společnost Microsoft hostuje službu testovacích jednotek (včetně zřizování a nasazení) pro finanční a provozní systém prostředků podnikového plánování (finance, operace, výroba, dodavatelský řetězec atd.). 
- **[Aplikace logiky](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/logic-app-test-drive)** : Šablona nasazení zahrnuje všechny komplexní architektury řešení. Všechny vlastní produkty by měly používat tento typ testovacích jednotek.
- **[Power BI](https://docs.microsoft.com/power-bi/service-template-apps-overview)** : vložený odkaz na uživatelsky sestavený řídicí panel. Produkty, které chtějí předvést interaktivní Power BI vizuál, by měly používat tento typ testovacích jednotek. Vše, co je třeba nahrát, je vaše vložená Power BI adresa URL.

#### <a name="additional-test-drive-resources"></a>Další zdroje testovacích jednotek
- [Technické osvědčené postupy testovacích jednotek](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Osvědčené postupy pro testování a uvádění na trh](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [Přehled testovacích jednotek – jeden pager](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf)

## <a name="connect-lead-management"></a>Připojit řízení zájemců

[!INCLUDE [Connect lead management](./includes/connect-lead-management-a.md)]

#### <a name="additional-lead-management-resources"></a>Další zdroje informací o vedoucím řízení
- [Nejčastější dotazy ke správě potenciálních zákazníků](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [Běžné chyby konfigurace potenciálních zákazníků](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#common-lead-configuration-errors-during-publishing-on-cloud-partner-portal)
- [Přehled řízení vedoucích – jeden pager](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf)

Nezapomeňte **Uložit** před přechodem k další části.

## <a name="properties"></a>Vlastnosti

Karta **vlastnosti** vás vyzve k definování kategorií a odvětví používaných k seskupení vaší nabídky na tržištích, platných smluv podporujících vaši nabídku a verze vaší aplikace.

Po dokončení těchto polí vyberte **Uložit** .

### <a name="category"></a>Kategorie

Vyberte minimálně jednu (1) a maximálně tři (3) kategorie použité pro seskupení nabídek do příslušných oblastí hledání na webu Marketplace. Zavolejte, jak vaše nabídka podporuje tyto kategorie v popisu nabídky.

### <a name="industry"></a>Odvětví

[!INCLUDE [Industry Taxonomy](./includes/industry-taxonomy.md)]

### <a name="app-version"></a>verze aplikace

Toto pole je volitelné a používá se na webu AppSource Marketplace k identifikaci čísla verze vaší nabídky.

### <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Smlouva standard pro komerční tržiště Microsoftu

Společnost Microsoft poskytuje standardní šablonu smlouvy.

- **Pro komerční tržiště Microsoftu použijte standardní smlouvu?**

Pro zjednodušení procesu nákupu pro zákazníky a omezení právní složitosti pro dodavatele softwaru nabízí společnost Microsoft standardní smlouvu pro komerční tržiště Microsoftu, která usnadňuje transakce na webu Marketplace. Místo toho, aby si mohli vlastní podmínky a ujednání, můžou si komerční vydavatelé na webu Marketplace vybrat svůj software v rámci standardní smlouvy, kteří zákazníci potřebují Vet a přijmout jenom jednou. Standardní kontrakt najdete tady: https://go.microsoft.com/fwlink/?linkid=2041178.

Výběrem možnosti použít standardní smlouvu pro komerční web Marketplace můžete vybrat, že se má používat standardní smlouva místo poskytování vlastních podmínek a ujednání.

![Pomocí zaškrtávacího políčka standardní smlouva](./media/use-standard-contract.png)

> [!NOTE]
> Po publikování nabídky pomocí standardní smlouvy pro komerční tržiště společnosti Microsoft nemůžete používat vlastní podmínky a ujednání. Jedná se o scénář "nebo". Vaše řešení si buď nabídnete v rámci standardní smlouvy, **nebo** podle vašich vlastních podmínek. Pokud chcete upravit podmínek standardní smlouvy, můžete to udělat prostřednictvím standardních změn smlouvy.

#### <a name="standard-contract-amendments"></a>Změny smlouvy Standard

Změny smlouvy Standard umožňují vydavatelům vybrat standardní smluvní smlouvy pro jednoduchost a přizpůsobit si pro svůj produkt nebo firmu obory podmínek. Zákazníci si musí jenom projít změny smlouvy, pokud už zkontrolovali a přijali standardní kontrakt společnosti Microsoft.

K dispozici jsou dva druhy změn pro komerční vydavatele na webu Marketplace:

- Univerzální změny: tyto změny se aplikují všeobecně na standardní smlouvu pro všechny zákazníky. Univerzální změny se zobrazují každému zákazníkovi nabídky v toku nákupu. Zákazníci musí přijmout podmínky standardní smlouvy a dodatku, aby mohli používat vaši nabídku.
- Vlastní změny: tyto změny představují zvláštní změny standardní smlouvy, které jsou zaměřené na konkrétní zákazníky jenom prostřednictvím ID tenanta Azure. Vydavatelé můžou zvolit tenanta, na který chtějí cílit. S vlastními podmínkami změny v nákupním toku nabídky se zobrazí pouze zákazníci z tenanta.  Zákazníci musí přijmout podmínky smlouvy Standard a jejich změny, aby mohli používat vaši nabídku.

>[!NOTE]
> Tyto dva typy změn zásobníků jsou navzájem navzájem. Zákazníci, na které cílíte vlastní změny, získají při nákupu také univerzální změnu standardní smlouvy.

**Univerzální pozměňovací výrazy ke standardní smlouvě pro komerční tržiště Microsoftu**: do tohoto pole zadejte univerzální výrazy pro změnu. Pro každou nabídku můžete zadat jednu univerzální změnu. Do tohoto pole můžete zadat neomezený počet znaků. Tyto výrazy se zobrazí zákazníkům v AppSource, Azure Marketplace a/nebo Azure Portal během procesu zjišťování a nákupu.

**Vlastní výrazy pro změny standardní smlouvy pro komerční tržiště Microsoftu**: Začněte tím, že vyberete **Přidat vlastní výrazy pro změnu**. Pro každou nabídku můžete zadat až 10 vlastních podmínek pro změnu.

- **Vlastní výrazy pro změnu**: do pole vlastní výrazy pro změnu zadejte vlastní vlastní údaje. Do tohoto pole můžete zadat neomezený počet znaků. Pouze zákazníkům z ID tenanta, které zadáte pro tyto vlastní výrazy, se zobrazí vlastní výrazy změny v toku nákupu nabídky v Azure Portal.  
- **ID klientů** (povinné): každé vlastní změně může být až 20 ID klientů. Pokud přidáte vlastní změnu, musíte zadat aspoň jedno ID tenanta. ID tenanta identifikuje zákazníka v Azure. U zákazníka můžete požádat o toto ID a získat ho tak, že přejdete na portal.azure.com > Azure Active Directory > Vlastnosti. Hodnota ID adresáře je ID tenanta (například 50c464d3-4930-494c-963c-1e951d15360e). ID tenanta organizace zákazníka můžete také vyhledat pomocí adresy URL svého názvu domény v umístění [moje Microsoft Azure a ID tenanta Office 365?](https://www.whatismytenantid.com).
- **Popis** (volitelné): Volitelně můžete zadat popis ID tenanta, který vám pomůže identifikovat zákazníka, kterému cílíte na změnu.

#### <a name="terms-and-conditions"></a>Podmínky a ujednání

Pokud chcete zadat vlastní podmínky a ujednání, můžete je vybrat do pole podmínky a ujednání. Do tohoto pole můžete zadat až 10 000 znaků textu. Pokud vaše podmínky a ujednání vyžadují delší popis, zadejte do tohoto pole odkaz na jednu adresu URL, kde se dají podmínky a ujednání najít. Zobrazí se zákazníkům jako aktivní odkaz.

Zákazníci musí tyto podmínky přijmout, aby mohli svoji nabídku vyzkoušet.

Nezapomeňte **Uložit** před přechodem k další části.

## <a name="offer-listing"></a>Seznam nabídek

Karta seznam nabídek zobrazuje jazyky (a trhy), kde je vaše nabídka dostupná. v současné době je dostupná jenom Anglická verze (USA). Kromě toho tato stránka zobrazuje stav seznamu pro konkrétní jazyk a datum a čas, kdy byl přidán. Pro každý jazyk nebo trh budete muset definovat podrobnosti o Marketplace (název nabídky, popis, hledané výrazy atd.).

> [!NOTE]
> Obsah seznamu nabídek (například popis nabídky, dokumenty, snímky obrazovky, podmínky použití a zásady ochrany osobních údajů) nemusí být v anglickém jazyce, pokud popis nabídky začíná frází "Tato aplikace je dostupná pouze v jazyce [jazyk, který není v angličtině]." Je také přijatelné poskytnout *užitečnou adresu URL pro odkaz* k poskytnutí obsahu v jiném jazyce, než který je použit v obsahu nabídky.

### <a name="offer-listings"></a>Výpisy nabídky

Zadejte podrobnosti, které se mají zobrazit na webu Marketplace, včetně popisů vaší nabídky a marketingových prostředků.

- **Název** (povinné): zde definovaný název se zobrazí jako název vaší nabídky na webu Marketplace, který jste zvolili. Název je předem vyplněný na základě vaší předchozí **nové položky nabídky** . Název může být ve vaší společnosti. Nemůže obsahovat Emoji (Pokud se nejedná o symboly ochranné známky a copyrightu) a musí být omezený na 50 znaků.
- **Souhrn** (povinné): zadejte krátký popis nabídky, která se má použít ve výsledcích hledání seznamů Marketplace. Do tohoto pole lze zadat až 100 znaků textu.
- **Popis** (povinné): zadejte popis nabídky, která se má zobrazit v seznamech Marketplace. Vezměte v úvahu zahrnutí hodnot do kategorií, klíčových výhod, jakékoli kategorie nebo přidružení oboru, možností nákupu v aplikaci, všech potřebných zveřejnění a odkazu na Další informace.
Do tohoto pole lze zadat až 3 000 znaků textu. Další tipy najdete v článku o [zápisu skvělého popisu aplikace](https://docs.microsoft.com/windows/uwp/publish/write-a-great-app-description).
- **Hledaná klíčová slova**: Zadejte až tři klíčová slova pro hledání, která můžou zákazníci použít k vyhledání vaší nabídky na webu Marketplace.
- Úvodní **pokyny** (povinné): Vysvětlete, jak nakonfigurovat a začít používat vaši aplikaci pro potenciální zákazníky.  Tento rychlý Start může obsahovat odkazy na podrobnější online dokumentaci. Do tohoto pole lze zadat až 3 000 znaků textu.

#### <a name="description"></a>**Popis**

Toto pole je povinné. Položky, které se mají zahrnout do **popisu**:

* Jasně popište, jak se má v prvních několika větách popisu v této nabídce vyhodnotit.  
* Mějte na paměti, že ve výsledcích vyhledávacího modulu se může zobrazit několik prvních vět.  
* Nespoléhá se na funkce a funkce k prodeji vašeho produktu. Místo toho se zaměřte na hodnotu, kterou doručujete.  
* Používejte co nejvíc slovníků specifických pro konkrétní obor nebo využití s výhodami.

Základní komponenty pro vaši polohu hodnoty by měly obsahovat následující informace:

* Popis produktu
* Typ uživatele, který je z produktu přínosný
* Zákaznické potřeby nebo bolesti adres produktů.

Chcete-li podrobnější **Popis** nabídky, použijte Editor formátovaného textu k formátování popisu.

![Použití editoru textu ve formátu RTF](./media/text-editor2.png)

Pomocí následujících pokynů můžete použít Editor formátovaného textu:

- Chcete-li změnit formát obsahu, zvýrazněte text, který chcete formátovat, a vyberte styl textu, jak je znázorněno níže:

     ![Změna formátu textu pomocí editoru formátovaného textu](./media/text-editor3.png)

- Chcete-li do textu přidat seznam s odrážkami nebo číslovaného seznamu, použijte následující možnosti:

     ![Přidávání seznamů pomocí editoru formátovaného textu](./media/text-editor4.png)

- Chcete-li přidat nebo odebrat odsazení textu, použijte následující možnosti:

     ![Použití editoru formátovaného textu k odsazení](./media/text-editor5.png)

#### <a name="links"></a>Odkazy

- **Zásady ochrany osobních údajů** (povinné): Připojte se k zásadám ochrany osobních údajů vaší organizace. Zodpovídáte za to, že vaše aplikace vyhovuje předpisům a zákonům o ochraně osobních údajů a poskytuje platné zásady ochrany osobních údajů.
- **Marketingové materiály programu CSP** (volitelné): zadejte odkaz na marketingové materiály, pokud se rozhodnete tuto nabídku rozšíříte do programu [Cloud Solution Provider (CSP)](https://docs.microsoft.com/azure/marketplace/cloud-solution-providers) . CSP rozšiřuje vaši nabídku na širší škálu kvalifikovaných zákazníků tím, že umožňuje partnerům CSP, aby si mohli seskupit, uvádět na trh a prodávat vaše nabídky. Tito prodejci budou potřebovat přístup k materiálům pro účely marketingu vaší nabídky. Další informace najdete v tématu [služby pro uvedení na trh](https://partner.microsoft.com/reach-customers/gtm).
- **Užitečné odkazy** (volitelné): volitelné doplňující online dokumenty týkající se vaší aplikace nebo související služby uvedené zadáním **názvu** a **adresy URL**. Přidejte další užitečné odkazy kliknutím na **+ Přidat adresu URL**.

#### <a name="contact-information"></a>Kontaktní informace

- **Kontakty**: u každého kontaktu zákazníka zadejte **jméno**zaměstnance, **telefonní číslo**a **e-mailovou** adresu.  (Nebudou *se* zobrazovat veřejně). Pro skupinu **kontaktů podpory** je také vyžadována **Adresa URL podpory** .  (Tyto informace *se* zobrazí veřejně).

**Kontakt podpory** (povinné): pro obecné dotazy týkající se podpory.

Technický **kontakt** (povinné): pro technické dotazy.

**Kontakt Správce kanálů** (povinné): u otázek prodejců souvisejících s programem CSP.

#### <a name="files-and-images"></a>Soubory a image

- **Dokumenty** (povinné): přidejte související marketingové dokumenty pro vaši nabídku ve formátu PDF, který poskytuje minimálně jeden (1) a maximálně tři (3) dokumentů na jednu nabídku.
- **Obrázky** (volitelné): k dispozici je několik míst, kde se na webu Marketplace můžou zobrazit obrázky loga vaší nabídky, vyžadují následující velikosti – malá: 48 x 48 pixelů _(povinné),_ střední: 90 × 90 pixelů, velká: 216 x 216 pixelů _(povinné),_ široké: 255 x 115 pixelů a Hero: 815 x 290 pixelů. Všechny obrázky musí být v. Formát PNG.
- **Snímky obrazovky** (povinné): přidejte snímky obrazovky, které demonstrují vaši nabídku. Je možné přidat maximálně pět (5) snímků obrazovky a velikost by měla být 1280 × 720 pixelů. Všechny obrázky musí být v. Formát PNG.
- **Videa** (volitelné): Přidejte odkazy na videa, která demonstrují vaši nabídku. Můžete použít odkazy na videa YouTube nebo Vimeo, která se zobrazí spolu s vaší nabídkou pro zákazníky. Budete taky muset zadat miniaturu videa, ve formátu PNG se velikost nastaví na 1280 x 720 pixelů. Můžete zobrazit maximálně čtyři videa na jednu nabídku.

Nezapomeňte **Uložit** před přechodem k další části.

#### <a name="additional-marketplace-listing-resources"></a>Další prostředky pro výpis Marketplace

- [Osvědčené postupy pro výpisy nabídky na webu Marketplace](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

## <a name="preview"></a>Preview

Karta **Náhled** umožňuje definovat **skupinu omezené verze Preview** pro uvolnění vaší nabídky před publikováním vaší nabídky do širší cílové skupiny na webu Marketplace.

> [!IMPORTANT]
> Po kontrole nabídky ve verzi Preview vyberte možnost **začít živě** , aby bylo možné vaši nabídku publikovat živě pro veřejnou cílovou skupinu na webu Marketplace.

- **Definovat cílovou skupinu Preview: přidejte jeden e-mailový účet AAD/MSA na každý řádek a volitelně také popis.**

Přidejte až deset (10) e-mailových adres ručně, nebo dvacet (20) při nahrávání souboru CSV, pro stávající účet Microsoft (MSA) nebo účty Azure Active Directory, které vám pomůžou při ověřování vaší nabídky před publikováním živě. Přidáním těchto účtů definujete cílovou skupinu, pro kterou bude přístup k vaší nabídce povolený ve verzi Preview, než bude publikována na webu Marketplace. Pokud je vaše nabídka už živá, můžete i nadále definovat cílovou skupinu verze Preview pro testování změn nebo aktualizací vaší nabídky.

> [!NOTE]
> Cílová skupina verze Preview se liší od soukromé cílové skupiny. Cílová skupina Preview má povolený přístup k vaší nabídce _před_ publikováním živě na Marketplace. Můžete se také rozhodnout vytvořit plán a zpřístupnit ho pouze pro privátní cílovou skupinu. Na kartě **seznam plánů** můžete definovat privátní cílovou skupinu pomocí zaškrtávacího políčka **Toto je soukromý plán** . Pak můžete definovat privátní cílovou skupinu až 20 000 zákazníků, kteří používají ID tenantů Azure.

## <a name="technical-configuration"></a>Technická konfigurace

Karta **Technická konfigurace** definuje technické podrobnosti (cesta URL, Webhook, ID TENANTA a ID aplikace), které se používají pro připojení k vaší nabídce. Toto připojení nám umožňuje zřídit vaši nabídku pro koncového zákazníka, pokud se k tomu rozhodne získat. Diagramy popisující použití shromážděných polí jsou k dispozici v dokumentaci pro [rozhraní API pro plnění SaaS](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2).

- **Adresa URL cílové stránky** (povinné): Definujte adresu URL webu, na kterou budou zákazníci zakládat vaši nabídku na webu Marketplace. Tato adresa URL bude koncovým bodem, který obdrží token při směrování zákazníka na stránku. Tento token se dá vyměňovat pro podrobnosti zřizování pomocí řešení v rozhraních API pro plnění. Tyto podrobnosti a všechny ostatní, které shromažďujete, se dají použít jako součást interaktivní webové stránky zákazníka integrované v rámci vašeho prostředí k dokončení registrace a aktivaci jejich nákupu.

- **Webhook připojení** (povinné): pro všechny asynchronní události, které Microsoft potřebuje poslat jménem zákazníka (příklad: předplatné SaaS je neplatné), vyžadujeme, abyste poskytli Webhook připojení. Pokud ještě nemáte systém Webhooku, nejjednodušší konfigurace bude mít aplikaci logiky koncového bodu HTTP, která bude naslouchat všem událostem, které jsou do ní odesílány, a jejich správné zpracování (například https:\//prod-1westus.logic.azure.com:443/work). Další informace najdete v tématu [pracovní postupy volání, triggeru nebo vnoření pracovních postupů s koncovými body HTTP v Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-http-endpoint).

- **ID tenanta Azure AD** (povinné): v rámci Azure Portal vyžadujeme [Vytvoření aplikace Azure Active Directory (AD)](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) , abyste mohli ověřit, že připojení mezi našimi dvěma službami bude za ověřenou komunikací. [ID tenanta](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)zjistíte tak, že přejdete na Azure Active Directory a vyberete **vlastnosti**a pak vyhledáte uvedené číslo **ID adresáře** (například 50c464d3-4930-494c-963c-1e951d15360e).

- **ID aplikace Azure AD** (povinné): budete také potřebovat [ID aplikace](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) a ověřovací klíč. Tyto hodnoty získáte tak, že přejdete na Azure Active Directory a vyberete **Registrace aplikací**a pak vyhledáte uvedené číslo **ID aplikace** (například 50c464d3-4930-494c-963c-1e951d15360e). Ověřovací klíč najdete tak, že přejdete na **Nastavení** a vyberete **klíče**. Budete muset zadat popis a dobu trvání a pak zadat číselnou hodnotu.

>[!Note]
>ID aplikace Azure je přidruženo k vašemu ID vydavatele, takže se ujistěte, že se stejné ID aplikace používá ve všech nabídkách.

## <a name="plan-overview"></a>Přehled plánu

Karta **Přehled plánu** umožňuje v rámci stejné nabídky poskytnout nejrůznější možnosti plánu. Tyto plány (někdy označované jako SKU) se mohou lišit v souvislosti s verzemi, finanční zhodnocení a úrovněmi služby. Aby bylo možné nabídku prodávat na webu Marketplace, musíte nastavit aspoň jeden plán.

Po vytvoření se zobrazí vaše názvy plánů, ID, cenové modely, dostupnost (veřejné nebo soukromé), aktuální stav publikování a všechny dostupné akce.

**Akce** , které jsou k dispozici v **přehledu plánu** , se liší v závislosti na aktuálním stavu plánu a mohou zahrnovat:

- Pokud je stav plánu **koncept** – odstranit koncept
- Pokud je stav plánu **Live** – zastavit prodej plánu nebo synchronizovat soukromou cílovou skupinu

**Vytvořit nový plán** (minimálně jeden plán pro ty, kteří si vybrali prodej prostřednictvím Microsoftu)

- **ID plánu:** Pro každý plán v této nabídce Vytvořte jedinečné ID plánu. Toto ID se bude zobrazovat zákazníkům v adrese URL produktu a šablony Azure Resource Manager (pokud jsou k dispozici). Používejte jenom malá písmena, alfanumerické znaky, pomlčky nebo podtržítka. Pro toto ID plánu je povoleno maximálně 50 znaků. Po výběru možnosti vytvořit nelze ID změnit.
- **Název plánu:** Zákazníci uvidí tento název při rozhodování, který plán vybrat v rámci vaší nabídky. Pro každý plán v této nabídce Vytvořte jedinečný název nabídky. Název plánu slouží k rozlišení softwarových plánů, které mohou být součástí stejné nabídky (například název nabídky: Windows Server; plány: Windows Server 2016, Windows Server 2019).

### <a name="plan-listing"></a>Výpis plánu

Karta **seznam plánů** zobrazuje jazyky (a trhy), kde je váš plán k dispozici, v současné době je dostupná jenom angličtina (USA). Kromě toho tato stránka zobrazuje stav seznamu pro konkrétní jazyk a datum a čas, kdy byl přidán. Pro každý jazyk nebo trh budete muset definovat podrobnosti o Marketplace (název nabídky, popis, hledané výrazy atd.).

#### <a name="plan-listing-details"></a>Podrobnosti výpisu plánu

Výběrem jednoho z jazyků plánování se zobrazí informace o **seznamu plánu** , včetně **názvu** a **popisu.**

- **Název**: předem vyplněné na základě vaší **nové položky plánu** Preview a zobrazí se jako název "softwarového plánu vaší nabídky" zobrazeného na webu Marketplace.
- **Popis:** Tento popis je příležitost vysvětlit, co dělá tento plán softwaru jedinečný a jakékoli rozdíly od jiných plánů softwaru v rámci vaší nabídky. Může obsahovat až 500 znaků.

Po dokončení těchto polí vyberte **Uložit** .

#### <a name="plan-pricing-and-availability"></a>Plánování cen a dostupnosti

Karta **ceny a dostupnost** vám umožní nakonfigurovat trhy, na které bude tento plán dostupný, požadovaný finanční zhodnocení model, cenu a fakturační období. Kromě toho můžete určit, jestli se má plán zviditelnit všem uživatelům, nebo jenom pro konkrétní zákazníky (soukromou cílovou skupinu).

##### <a name="enabling-free-trials"></a>Povolení bezplatných zkušebních verzí

SaaS prostřednictvím komerčního tržiště vám umožní poskytovat měsíční bezplatnou zkušební verzi při prodeji prostřednictvím Microsoftu. Pro všechny fakturační modely a výrazy s výjimkou měřených plánů se podporují bezplatné zkušební verze. Tato možnost umožňuje zákazníkům, aby po jednom měsíci bezplatného přístupu vstoupili na nižší bariéru.  Pokud se rozhodnete povolit bezplatnou zkušební verzi pro plány v rámci vaší nabídky, zákazník nebude moct převést na placené předplatné před uplynutím počátečního měsíčního období.  Během této doby můžou zákazníci nakoupit vaši nabídku vyzkoušet některý z podporovaných plánů, které mají povolenou bezplatnou zkušební verzi, a převádět je mezi nimi.  Převod na placené předplatné se provádí automaticky na konci období.

>[!Note]
>Pokud se zákazník rozhodne převést na plán bez bezplatných zkušebních verzí, dojde k převodu, ale bezplatná zkušební verze bude okamžitě ztracena.  Jakmile zákazník začne platit za plán, nebude už moct znovu získat bezplatnou zkušební verzi ve stejném předplatném, a to i v případě, že převedou na SKU, které podporuje bezplatné zkušební verze.

Možnost konfigurace bezplatné zkušební verze je k dispozici pro každý plán v nabídce. Jednoduše přejděte na ceny a dostupnost každé nabídky a zaškrtněte políčko pro povolení měsíční zkušební verze.

![Zaškrtávací políčko pro bezplatnou zkušební verzi v jednom měsíci](./media/free-trial-enable.png)

>[!Note]
>Jakmile je vaše nabídka s podporou transakcí publikovaná s bezplatnou zkušební verzí, nedá se pro tento plán zakázat. Ujistěte se, že toto nastavení je pro první publikování správné, aby se nemuselo znovu vytvářet plán.

Pokud chcete získat informace o předplatných zákazníků, které se aktuálně účastní bezplatné zkušební verze, použijte novou vlastnost `isFreeTrial`API, která bude označená jako true nebo false. Další informace najdete v tématu [rozhraní API pro SaaS získat předplatné](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2#get-subscription).

>[!Note]
>Bezplatné zkušební verze se nepodporují pro plány, které využívají službu měření na webu Marketplace.

#### <a name="markets"></a>Trhy

- **Upravit trhy** (volitelné)

Každý plán musí být k dispozici alespoň v jednom trhu. Zaškrtněte políčko pro jakékoli místo na trhu, kde byste chtěli tento plán zpřístupnit. K nápovědě jsou k dispozici vyhledávací pole a tlačítko pro výběr zemí "daňového přiznání", ve kterých společnost Microsoft provádí prodej a používání daně vaším jménem.

Pokud jste už pro svůj plán nastavili ceny v USA dolary (USD) a přidáte další místo na trhu, cena za nový trh se vypočítá podle aktuálních směnných kurzů. Před publikováním byste měli vždycky zkontrolovat cenu za každý trh. Po uložení změn můžete pomocí odkazu exportovat ceny (XLSX) zkontrolovat ceny.

#### <a name="pricing"></a>Ceny

- **Cenový model**: paušální sazba nebo na základě pracovních stanic

**Paušální sazba:** Umožněte přístup k vaší nabídce s jednou měsíčně nebo roční cenou za paušální cenu. Tato situace se někdy označuje jako ceny na základě lokality. Pomocí tohoto cenového modelu můžete volitelně definovat měřené plány, které používají rozhraní API služby pro měření softwaru Marketplace k účtování zákazníků podle nestandardních jednotek.  Další informace o měřených fakturách najdete v tématu [účtované vyúčtování pomocí služby měření na webu Marketplace](./saas-metered-billing.md).

**Na uživatele:** Umožněte přístup ke svojí nabídce za cenu na základě počtu uživatelů, kteří přistupují k nabídce nebo při používání stanic. Tento model založený na uživateli vám umožní nastavit minimální a maximální počet povolených uživatelů na základě ceny. Tímto způsobem lze nakonfigurovat různé cenové body na základě počtu uživatelů konfigurací více plánů.  Tato pole jsou volitelná. Pokud je ponecháno bez výběru, počet uživatelů bude interpretován jako neomezený (minimální počet 1 a maximální počet, jak může systém podporovat). Tato pole je možné upravovat v rámci aktualizace vašeho plánu.

Po publikování se volba cenového modelu fakturace nedá změnit. Kromě toho musí všechny plány pro stejnou nabídku sdílet stejný cenový model.

- **Fakturační období**: měsíční nebo roční

Vyberte frekvenci, kterou zákazníci musí platit v uvedené ceně. Musí se zadat aspoň jedna měsíční nebo roční cena nebo můžou být dostupné obě možnosti pro zákazníky.

- **Cena**: USD za měsíc nebo USD za rok

Ceny nastavené v místní měně (USD = USA dolar) se převedou na místní měnu všech vybraných trhů pomocí aktuálních kurzů pro Exchange, které jsou k dispozici během instalace. Před publikováním tyto ceny ověřte, a to tak, že vyexportujete tabulku cen a zkontrolujete cenu na každém trhu. Pokud chcete nastavit vlastní ceny v individuálním trhu, upravte a importujte tabulku s cenami. Zodpovídáte za ověřování těchto cen a jejich nastavení.
*\*Aby bylo možné exportovat data o cenách, musíte nejdřív Uložit změny cen.*

Před publikováním pečlivě zkontrolujte své ceny, protože existují určitá omezení, co se může po publikování plánu změnit:

- Po publikování plánu se cenový model nedá změnit.
- Po publikování fakturačního období pro určitý plán ho nebude možné odebrat později.
- Jakmile je cena za trh v plánu publikovaná, nedá se později změnit.

### <a name="plan-audience"></a>Cílová skupina plánu

Máte možnost nakonfigurovat každý plán tak, aby byl viditelný pro všechny uživatele, nebo jenom pro konkrétní cílovou skupinu, kterou zvolíte. V této omezené cílové skupině můžete přiřadit členství pomocí ID tenanta Azure AD.

#### <a name="privacy"></a>Ochrana osobních údajů

- **Toto je soukromý plán** (volitelné zaškrtávací políčko).

Zaškrtnutím tohoto políčka zajistíte, aby byl váš plán soukromý a viditelný jenom pro skupinu s omezeným přístupem, kterou zvolíte. Po zveřejnění jako soukromého plánu můžete cílovou skupinu aktualizovat nebo si vybrat, že plán bude dostupný všem. Jakmile je plán publikován jako viditelný pro všechny, musí zůstat viditelný pro všechny. (Plán se nedá nakonfigurovat jako privátní plán.)

- **Skupina s omezeným přístupem (ID klientů)**

Přiřaďte cílovou skupinu, která bude mít přístup k tomuto privátnímu plánu. Přístup se přiřazuje pomocí ID klientů s možností zahrnutí popisu každého přiřazeného ID tenanta. Pokud importujete soubor tabulky. csv, můžete přidat maximálně 10 ID klientů nebo 20 000 ID klientů.

Tenant je reprezentace organizace s ID reprezentovanou jako identifikátor GUID (globálně jedinečný identifikátor, 128 celé číslo, které se používá k identifikaci prostředků). Jedná se o vyhrazenou instanci Azure AD, kterou organizace nebo vývojář aplikací obdrží při vytvoření partnerství s Microsoftem, například registrací do Azure, Microsoft Intune nebo Microsoft 365. Každý klient Azure AD se odlišuje a je oddělený od ostatních klientů Azure AD. Pokud chcete zjistit tenanta, přihlaste se k Azure Portal pomocí účtu, který chcete použít ke správě aplikace. Pokud tenanta máte, budete k němu automaticky přihlášeni a název tenanta se zobrazí přímo pod názvem vašeho účtu. Najeďte myší na název vašeho účtu v pravém horním rohu webu Azure Portal, zobrazí se vaše jméno, e-mail, ID adresáře nebo tenanta (GUID) a vaše doména. Pokud je k vašemu účtu přidruženo více tenantů, můžete výběrem názvu vašeho účtu otevřít nabídku, ve které můžete mezi jednotlivými tenanty přepínat. Každý tenant má vlastní ID tenanta. ID tenanta vaší organizace můžete také vyhledat pomocí adresy URL názvu domény na adrese: [https://www.whatismytenantid.com](https://www.whatismytenantid.com).

I když SaaS nabízí použití ID tenanta k definování soukromé cílové skupiny, můžou používat i jiné typy nabídek ID předplatných Azure (které jsou také reprezentované jako identifikátory GUID).

> [!NOTE]
> Privátní skupina (nebo skupina s omezeným přístupem) se liší od cílové skupiny verze Preview. Na kartě **[Náhled](#preview)** můžete definovat cílovou skupinu verze Preview. Cílová skupina Preview má povolený přístup k vaší nabídce před tím, *než* je nabídka publikovaná živě na webu Marketplace. I když se označení soukromé cílové skupiny vztahuje pouze na konkrétní plán, cílová skupina Preview může zobrazit všechny plány (soukromě nebo ne), ale pouze během období omezené verze Preview, zatímco je plán testován a ověřen.

## <a name="example-list-of-plans-within-a-marketplace-offer"></a>Příklad seznamu plánů v rámci nabídky Marketplace

![Ukázkový výpis plánu Marketplace s poznámkami](./media/marketplace-plan.svg)

## <a name="test-drive"></a>Testovací verze

[!INCLUDE [Test drive content](./includes/commercial-marketplace-test-drive.md)]

## <a name="cloud-solution-provider-csp-reseller-audience"></a>Cílová skupina pro prodejce řešení Cloud Solution Provider (CSP)

Pokud chcete, aby vaše nabídka byla k dispozici v programu CSP, umožní poskytovatelům cloudového řešení prodávat vaše produkty jako součást řešení svým zákazníkům. Další informace najdete v tématu [Poskytovatelé cloudových řešení](https://go.microsoft.com/fwlink/?linkid=2111109).

## <a name="publish"></a>Publikování

Až dokončíte všechny požadované části nabídky, vyberte **publikovat** v pravém horním rohu portálu. Budete přesměrováni na stránku **Revize a publikování** .

#### <a name="submit-offer-to-preview"></a>Odeslat nabídku do verze Preview

Pokud tuto nabídku publikujete poprvé, můžete:

- Podívejte se na téma stav dokončení pro jednotlivé části nabídky.
    - *Nespuštěno* – znamená, že se oddíl nedotkl a je potřeba ho dokončit.
    - *Nedokončeno* – znamená, že oddíl obsahuje chyby, které je potřeba opravit, nebo vyžaduje, aby se poskytly další informace. Budete se muset vrátit k oddílu a aktualizovat ho.
    - *Complete* – znamená, že část je dokončena, byla zadána všechna požadovaná data a nejsou k dispozici žádné chyby. Aby bylo možné nabídku odeslat, musí být všechny oddíly nabídky v úplném stavu.
- Poskytněte pokyny k testování certifikačního týmu, abyste měli jistotu, že vaše aplikace je testována správně, kromě doplňkových poznámek, které jsou užitečné pro porozumění vaší aplikaci.
- Kliknutím na **Odeslat**nabídku pro publikování odešlete. Pošleme vám e-mail s oznámením, že je k dispozici verze Preview vaší nabídky, kterou si můžete prohlédnout a schválit. Je nutné, abyste se vrátili do partnerského centra **a vybrali možnost pro publikování** nabídky na veřejném webu (nebo v případě soukromé nabídky pro privátní cílovou skupinu).

## <a name="next-steps"></a>Další kroky

- [Aktualizace stávající nabídky na komerčním marketplace](./update-existing-offer.md)
