---
title: Vytvoření nové nabídky SaaS na komerčním webu Marketplace
description: Postup vytvoření nové nabídky software jako služba (SaaS) pro výpis nebo prodej v Azure Marketplace, AppSource nebo prostřednictvím programu poskytovatel cloudového řešení (CSP) pomocí portálu pro komerční tržiště na partnerském centru Microsoftu.
author: qianw211
manager: evansma
ms.author: v-qiwe
ms.service: marketplace
ms.topic: conceptual
ms.date: 08/14/2019
ms.openlocfilehash: 7dd57e41144ec33aec6eb716716d0794d92071f5
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/23/2019
ms.locfileid: "70013092"
---
# <a name="create-a-new-saas-offer"></a>Vytvořit novou nabídku SaaS

Pokud chcete začít vytvářet nabídky software jako služba (SaaS), ujistěte se, že jste nejdřív [vytvořili účet partnerského centra](./create-account.md) a otevřete [řídicí panel komerčního tržiště](https://partner.microsoft.com/dashboard/commercial-marketplace/offers)s vybranou kartou **Přehled** .

![Řídicí panel komerčního tržiště v partnerském centru](./media/new-offer-overview.png)

Vyberte nabídku + **Nová...** a potom vyberte položku nabídky **software jako služba** . 

Pokud vyberete jiný typ nabídky, budete možná přesměrováni na starší [portál partnerů cloudu](https://cloudpartner.azure.com/). V tomto okamžiku jsou na portálu pro komerční tržiště k dispozici pouze nabídky SaaS a Dynamics 365 v partnerském centru.

![Vytvořit okno nabídky na partnerském centru](./media/new-offer-click.png)

Zobrazí se dialogové okno **Nová nabídka** . 

![Dialogové okno Nová nabídka](./media/new-offer-popup.png)


## <a name="offer-id-and-alias"></a>ID nabídky a alias

- **ID nabídky**: Jedinečný identifikátor pro každou nabídku ve vašem účtu. Toto ID uvidí zákazníci v adrese URL pro nabídku Marketplace a šablony Azure Resource Manager (pokud jsou k dispozici). ID nabídky musí být malé, alfanumerické (včetně spojovníků a podtržítka, ale bez mezer). Tato možnost je omezená na 50 znaků a po výběru *vytvořit*se nedá změnit.  
Příklad: test-nabídka-1
<br>Výsledkem je adresa URL:`https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`

- **Alias nabídky**: Název, který se používá k odkazování na nabídku v rámci portálu partnerského centra. Tento název se na webu Marketplace nepoužívá a liší se od *názvu nabídky* a dalších hodnot, které se zákazníkům zobrazí. Tuto hodnotu nelze změnit po výběru možnost *vytvořit*.

<br>Příklad: Nabídka testu 1&#8482;

Vyberte **Vytvořit**.  Pro tuto nabídku se vytvoří stránka s **přehledem nabídky** .  

<!---
![Offer overview on Partner Center](./media/commercial-marketplace-offer-overview.png)
-->

## <a name="offer-overview"></a>Přehled nabídky

Stránka **Přehled nabídky** obsahuje: 

- **Stav publikování** zobrazí vizuální znázornění kroků požadovaných k publikování této nabídky a dobu, po kterou bude každý krok trvat. Nedokončené ikony kroků publikování budou šedé. 

- Nabídka **Přehled** nabídky obsahuje seznam odkazů pro provádění operací s touto nabídkou. Tento seznam operací se změní v závislosti na výběru provedeném pro vaši nabídku.  
    - Pokud je nabídka koncept – odstranit koncept 
    - Pokud je nabídka živá – zastavit prodej nabídky 
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

Následující tabulka ukazuje příklad rozdělení nákladů a výběrů k předvedení modelu agentury.

|**Náklady na licenci**|**$100 za měsíc**|
|:---|:---|
|Náklady na využití Azure (D1/1 – jádro)|Účtuje se přímo vydavateli, nikoli zákazník.|
|Zákazník se účtuje Microsoftem.|$100,00 za měsíc (Vydavatel musí mít v licenčním poplatku vynaložené nebo předávací náklady na infrastrukturu)|

|**Účty Microsoft**|**$100 za měsíc**|
|:---|:---|
|Společnost Microsoft vám zaplatí 80% vašich licenčních nákladů. <br>**Pro kvalifikované aplikace SaaS přináší společnost Microsoft 90% vašich licenčních nákladů.*|$80,00 za měsíc <br>*$* 90,00 za měsíc *|

- V tomto příkladu vám Microsoft účtuje $100,00 pro zákazníka za softwarovou licenci a vyplatí $80,00 pro vydavatele.
- Partneři, kteří mají nárok na **snížený poplatek za službu Marketplace** , uvidí za snížený poplatek za transakce SaaS z května 2019 do června 2020. V tomto scénáři Microsoft účtuje $100,00 pro vaši softwarovou licenci a vyplatí $90,00 vydavateli.

> [!NOTE]
> **Nižší poplatek za službu Marketplace**: U některých nabídek SaaS, které jste publikovali na komerčním tržišti, společnost Microsoft sníží svůj poplatek za službu Marketplace z 20% (jak je popsáno v tématu smlouva Microsoft Publisher Agreement) na 10%. Aby vaše nabídka mohla být kvalifikována, musí být alespoň jedna z vašich nabídek označena Microsoftem jako přizpůsobená priorita nebo prodej IP.  Aby bylo možné získat tento snížený poplatek za službu Marketplace na daný měsíc, musí být nárok splněn nejméně pět (5) pracovních dnů před koncem každého kalendářního měsíce.  Snížený poplatek za službu Marketplace se nevztahuje na virtuální počítače, spravované aplikace ani žádné jiné produkty, které jsou dostupné prostřednictvím našeho komerčního tržiště.  Snížený poplatek za službu Marketplace bude dostupný jenom pro kvalifikované nabídky pro poplatky za licence shromážděné Microsoftem mezi 1. května 2019 a 30. června 2020.  Po uplynutí této doby se poplatek za službu Marketplace vrátí do normálního množství. 




#### <a name="csp-program-opt-in"></a>Výslovný souhlas programu CSP
Program [Cloud Solution Provider (CSP)](https://docs.microsoft.com/azure/marketplace/cloud-solution-providers) umožňuje softwarovým nabídkám oslovit miliony kvalifikovaných zákazníků Microsoftu s minimálními marketingovými a prodejními investicemi.

- **Barev Zpřístupní moji nabídku v programu** CSP (zaškrtávací políčko).

Pokud chcete, aby vaše nabídka byla k dispozici v programu CSP, umožní poskytovatelům cloudového řešení prodávat vaše produkty jako součást řešení svým zákazníkům. 

### <a name="list-through-microsoft"></a>Seznam prostřednictvím Microsoftu

Vytvořte seznam na webu Marketplace a zvyšte své podnikání pomocí Microsoftu. Výběrem této nabídky zobrazíte pouze seznam a nikoli transakční prostřednictvím Microsoftu znamená, že se společnost Microsoft neúčastní přímo v transakcích licencí na software. Není k dispozici žádný přidružený transakční poplatek a vydavatel udržuje 100% jakýchkoli licenčních poplatků za software shromážděných od zákazníka. Vydavatel je však zodpovědný za podporu všech aspektů licenčních transakcí softwaru, mimo jiné: splnění objednávek, měření, fakturace, fakturace, platby a shromažďování. 

- **Jak chcete potenciálním zákazníkům pracovat s touto nabídkou pro výpis?**

##### <a name="get-it-now-free"></a>Získat hned (zdarma)
Seznamte se svým zákazníkům zdarma tím, že zadáte platnou adresu URL (počínaje *protokolem HTTP* nebo *https*), ke které mají přístup k vaší aplikaci.  Příklad: `https://contoso.com/saas-app`

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

- **Povolit testovací jednotku** prvku 

Povolením testovacích jednotek budete požádáni o konfiguraci demonstračního prostředí pro zákazníky, kteří budou chtít vaši nabídku vyzkoušet na určitou dobu. 

### <a name="type-of-test-drive"></a>Typ testovací jednotky

- **[Azure Resource Manager](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)** : Šablona nasazení obsahující všechny prostředky Azure, které tvoří vaše řešení. Produkty, které vyhovují tomuto scénáři, používají pouze prostředky Azure.
- **[Dynamics 365 pro firmy – střed](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-business-central-offer)** : Společnost Microsoft hostuje a udržuje službu testovacích jednotek (včetně zřizování a nasazení) pro podnikový systém plánování podnikových zdrojů (finance, operace, dodavatelský řetězec, CRM atd.).  
- **[Dynamics 365 pro zákaznickou zapojení](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/dyn365ce/cpp-customer-engagement-offer)** : Společnost Microsoft hostuje a udržuje službu testovacích jednotek (včetně zřizování a nasazení) pro systém zákaznických rezervací (prodej, služba, Project Service, služba pole atd.).  
- **[Dynamics 365 pro operace](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-dynamics-365-operations-offer)** : Společnost Microsoft hostuje a udržuje službu testovacích jednotek (včetně zřizování a nasazení) pro finanční a provozní systém prostředků podnikového plánování (finance, operace, výroba, dodavatelský řetězec atd.). 
- **[Aplikace logiky](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/logic-app-test-drive)** : Šablona nasazení zahrnuje všechny komplexní architektury řešení. Všechny vlastní produkty by měly používat tento typ testovacích jednotek.
- **[Power BI](https://docs.microsoft.com/power-bi/service-template-apps-overview)** : Vložený odkaz na uživatelsky sestavený řídicí panel. Produkty, které chtějí předvést interaktivní Power BI vizuál, by měly používat tento typ testovacích jednotek. Vše, co je třeba nahrát, je vaše vložená Power BI adresa URL.

#### <a name="additional-test-drive-resources"></a>Další zdroje testovacích jednotek
- [Technické osvědčené postupy testovacích jednotek](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Osvědčené postupy pro testování a uvádění na trh](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [Přehled testovacích jednotek – jeden pager](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf)

## <a name="connect-lead-management"></a>Připojit řízení zájemců

Spojte se se zákazníky přímo, a to tak, že na tržištích zadáte nabídku a zapojíte systém pro řízení vztahů se zákazníky (CRM), abyste mohli dostávat kontaktní údaje zákazníka hned, co zákazník vyjádří zájem nebo nasadí vaše produktu.

- **Zvolit cíl zájemce** (rozevírací nabídka): Poskytněte podrobnosti o připojení do systému CRM, kam chcete poslat zájemce zákazníkům. 

Partnerské centrum podporuje následující systémy CRM pro správu potenciálních zákazníků. Vyberte odkaz pro pokyny k instalaci.

- Objekt blob Azure – zadejte kontaktní e-mail, název kontejneru a připojovací řetězec účtu úložiště. 
- [Tabulka Azure](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-azure-table) – zadejte kontaktní e-mail a připojovací řetězec účtu úložiště. 
- [Dynamics CRM Online](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics) – poskytněte kontaktní e-mail, adresu URL a režim ověřování (Office 365 nebo Azure Active Directory).
- [Koncový bod HTTPS](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-https) – zadejte kontaktní e-mail a adresu URL koncového bodu https. 
- [Marketo](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-marketo) – poskytněte kontaktní e-mail, ID formuláře, ID účtu Munchkin a ID serveru.
- [Salesforce](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-salesforce) – poskytněte kontaktní E-mail a ID organizace. 

#### <a name="additional-lead-management-resources"></a>Další zdroje informací o vedoucím řízení
- [Nejčastější dotazy ke správě potenciálních zákazníků](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [Běžné chyby konfigurace potenciálních zákazníků](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#common-lead-configuration-errors-during-publishing-on-cloud-partner-portal)
- [Přehled řízení vedoucích – jeden pager](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf)

Před přechodem k další části si nezapomeňte **Uložit** .

## <a name="properties"></a>Vlastnosti
Karta **vlastnosti** vás vyzve k definování kategorií a odvětví používaných k seskupení vaší nabídky na tržištích, platných smluv podporujících vaši nabídku a verze vaší aplikace. 

Po dokončení těchto polí vyberte **Uložit** . 

### <a name="category"></a>Kategorie
Vyberte minimálně jednu (1) a maximálně tři (3) kategorie použité pro seskupení nabídek do příslušných oblastí hledání na webu Marketplace. Zavolejte prosím, jak vaše nabídka podporuje tyto kategorie v popisu nabídky. 

### <a name="industry"></a>Obor
Vyberte až dva obory (2), které se používají k seskupení vaší nabídky, do příslušných oblastí hledání na webu Marketplace. Pokud vaše nabídka není specifická pro odvětví, nevybírejte ji. Zavolejte prosím, jak vaše nabídka podporuje vybrané odvětví v popisu nabídky. 

### <a name="app-version"></a>verze aplikace
Toto je volitelné pole, které se používá na webu AppSource Marketplace k identifikaci čísla verze vaší nabídky. 

### <a name="standard-contract"></a>Standardní kontrakt

- **Používáte standardní smlouvu?**

Pro zjednodušení procesu nákupu pro zákazníky a omezení právní složitosti pro dodavatele softwaru nabízí společnost Microsoft standardní šablonu smlouvy, aby usnadnila transakci na webu Marketplace. 

Místo vytváření vlastních podmínek a ujednání můžou Azure Marketplace vydavatelé zvolit, že mají nabízet svůj software v rámci standardní smlouvy, které zákazníci potřebují, aby je Vet a přijali jenom jednou. 

Standardní kontrakt najdete tady: https://go.microsoft.com/fwlink/?linkid=2041178.

#### <a name="terms-of-use"></a>Podmínky použití

Pokud se vaše licenční smlouvy liší od standardní smlouvy, můžete zde zadat vlastní právní podmínku použití. Do tohoto pole můžete také zadat až 10 000 znaků textu. Pokud vaše podmínkami použití vyžadují delší popis, zadejte do tohoto pole odkaz na jednu adresu URL, kde najdete další licenční smlouvy. Zobrazí se zákazníkům jako aktivní odkaz.

Zákazníci musí tyto podmínky přijmout, aby mohli svoji aplikaci vyzkoušet. 

Před přechodem k další části si nezapomeňte **Uložit** .

## <a name="offer-listing"></a>Seznam nabídek

Karta seznam nabídek zobrazuje jazyky (a trhy), kde je vaše nabídka dostupná. v současné době je dostupná jenom Anglická verze (USA). Kromě toho tato stránka zobrazuje stav seznamu pro konkrétní jazyk a datum a čas, kdy byl přidán. Pro každý jazyk nebo trh budete muset definovat podrobnosti o Marketplace (název nabídky, popis, hledané výrazy atd.).

> [!NOTE]
> Obsah seznamu nabídek (například popis nabídky, dokumenty, snímky obrazovky, podmínky použití a zásady ochrany osobních údajů) nemusí být v anglickém jazyce, pokud popis nabídky začíná frází "Tato aplikace je dostupná pouze v jazyce [jazyk, který není v angličtině]." Je také přijatelné poskytnout *užitečnou adresu URL pro odkaz* k poskytnutí obsahu v jiném jazyce, než který je použit v obsahu nabídky.

### <a name="offer-listings"></a>Výpisy nabídky

Zadejte podrobnosti, které se mají zobrazit na webu Marketplace, včetně popisů vaší nabídky a marketingových prostředků.

- **Název** (povinné): Zde definovaný název se zobrazí jako název vaší nabídky na webu Marketplace, který jste zvolili. Název je předem vyplněný na základě vaší předchozí **nové položky nabídky** .  To může být ochranné známky.  Nesmí obsahovat prázdné znaky, Emoji (Pokud se nejedná o symboly ochranné známky a copyrightu) a musí být omezeny na 50 znaků.
- **Souhrn** (povinné): Zadejte krátký popis vaší nabídky, která se má použít ve výsledcích hledání seznamů Marketplace. Do tohoto pole lze zadat až 100 znaků textu.
- **Popis** (povinné): Zadejte popis nabídky, která se má zobrazit v seznamech Marketplace. Vezměte v úvahu zahrnutí hodnot do kategorií, klíčových výhod, jakékoli kategorie nebo přidružení oboru, možností nákupu v aplikaci, všech potřebných zveřejnění a odkazu na Další informace.
Do tohoto pole lze zadat až 3 000 znaků textu. Další tipy najdete v článku o [zápisu skvělého popisu aplikace](https://docs.microsoft.com/windows/uwp/publish/write-a-great-app-description).
- **Hledaná klíčová slova**: Zadejte až tři klíčová slova pro hledání, která můžou zákazníci použít k vyhledání vaší nabídky na webu Marketplace.
- **Pokyny k začátku** (povinné): Vysvětlete, jak nakonfigurovat a začít používat vaši aplikaci pro potenciální zákazníky.  Tento rychlý Start může obsahovat odkazy na podrobnější online dokumentaci. Do tohoto pole lze zadat až 3 000 znaků textu. 

#### <a name="description"></a>**Popis**

Toto je povinné pole. Položky, které se mají zahrnout do popisu: 

* Jasně popište, jak se má v prvních několika větách popisu v této nabídce vyhodnotit.  
* Mějte na paměti, že ve výsledcích vyhledávacího modulu se může zobrazit několik prvních vět.  
* Nespoléhá se na funkce a funkce k prodeji vašeho produktu. Místo toho se zaměřte na hodnotu, kterou doručujete.  
* Používejte co nejvíc slovníků specifických pro konkrétní obor nebo využití s výhodami. 

Základní komponenty pro vaši polohu hodnoty by měly obsahovat následující informace: 

* Popis produktu 
* Typ uživatele, který je z produktu přínosný 
* Zákaznické potřeby nebo bolesti adres produktů. 

Aby bylo možné podrobnější popis nabídky, můžete k formátování popisu použít značky HTML. 

1. Pokud chcete vytvořit odstavce, přidejte `<p>` na BEGGING textu a přidejte `</p>` na konec.

    **Příklad**: 

    `<p>`Toto je můj první odstavec. `</p>` <br>
    `<p>`Toto je můj druhý odstavec. `</p>` <br>

    Výše uvedená by vypadala takto:

    <p> Toto je můj první odstavec. </p>
    <p> Toto je můj druhý odstavec. </p>

1. Pokud chcete přidat **seznam položek s**odrážkami, umístěte text do `<li>` značek níže. `<li>` V rámci `</li>` značeka`</ul>`můžete kopírovat a vkládat další položky s odrážkami (položky mezi značkami a). `<ul>` Ujistěte se `<ul></ul>`, že jste přidali. 

    **Příklad**:

    ```
    <ul> 
        <li>add text here</li> 
        <li> add text here </li> 
        <li> add text here </li> 
    </ul> 
    ```

    Výše uvedená by vypadala takto:
    <ul> 
        <li>sem přidejte text</li> 
        <li> sem přidejte text </li> 
        <li> sem přidejte text </li> 
    </ul> 

1. Do **tučného** obsahu přidejte `<b>` na začátek textu, který chcete formátovat tučně, a přidejte `</b>` na konec textu, který chcete formátovat tučně. 

    **Příklad**: `<b>`BEZPLATNÁ ZKUŠEBNÍ VERZE`</b>`
    
    Výše uvedená by způsobila, že se slova bezplatné zkušební verze budou zobrazovat tučně v popisu nabídky v prezentace. 

    **BEZPLATNÁ ZKUŠEBNÍ VERZE**

1. Chcete-li přidat **zalomení řádků** mezi vaším `<br>` obsahem, přidejte před obsah, který chcete spustit na novém řádku. Pokud chcete opustit mezeru a zajistěte, aby se obsah spouštěl na novém řádku, `<br><br>` přidejte před něj obsah. 

    **Příklad**:

    Toto je textový řádek. `<br>`Toto je textový řádek, který začne na novém řádku. `<br><br>`Jedná se o řádek, který spustí dva řádky níže. 

    Výše uvedená by vypadala takto:

    Toto je textový řádek. <br> Toto je textový řádek, který začne na novém řádku. <br><br> Jedná se o řádek, který spustí dva řádky níže. 

1. Chcete-li **zvětšit velikost textu**, vyberte nejprve, jak velký má být text. Použijte níže uvedené příklady. Po výběru velikosti textu přidejte odpovídající `<H*></H*>` značky na začátek a konec textu. 

    **Příklad**:

    `<h1>`Toto je nadpis 1.`</h1>` <br>
    `<h2>`Toto je Nadpis 2`</h2>` <br>
    `<h3>`Toto je nadpis 3`</h3>` <br>
    `<h4>`Toto je nadpis 4.`</h4>` <br>
    `<h5>`Toto je nadpis 5.`</h5>` <br>
    `<h6>`Toto je nadpis 6.`</h6>` 

    Výše uvedená by vypadala takto:

    ![Ukázkové nadpisy](./media/heading.png)

#### <a name="links"></a>Odkazy

- **Zásady ochrany osobních údajů** (povinné): Připojte se k zásadám ochrany osobních údajů vaší organizace. Zodpovídáte za to, že vaše aplikace vyhovuje předpisům a zákonům o ochraně osobních údajů a poskytuje platné zásady ochrany osobních údajů.
- **Marketingové materiály programu CSP** (volitelné): Pokud se rozhodnete tuto nabídku rozšíříte do programu [Cloud Solution Provider (CSP)](https://docs.microsoft.com/azure/marketplace/cloud-solution-providers) , musíte zadat odkaz na marketingové materiály. CSP rozšiřuje vaši nabídku na širší škálu kvalifikovaných zákazníků tím, že umožňuje partnerům CSP, aby si mohli seskupit, uvádět na trh a prodávat vaše nabídky. Tito prodejci budou potřebovat přístup k materiálům pro účely marketingu vaší nabídky. Další informace najdete v tématu [služby pro uvedení na trh](https://partner.microsoft.com/reach-customers/gtm).
- **Užitečné odkazy** (volitelné): Volitelné dodatečné online dokumenty týkající se vaší aplikace nebo související služby, které jsou uvedené v seznamu, zadáním **názvu** a **adresy URL**. Přidejte další užitečné odkazy kliknutím na **+ Přidat adresu URL**.

#### <a name="contact-information"></a>Kontaktní údaje

- **Kontakty**: U každého kontaktu zákazníka zadejte **jméno** zaměstnance, **telefonní číslo**a **e-mailovou** adresu.  (Nebudou se zobrazovat veřejně). Pro skupinu **kontaktů podpory** je také vyžadována **Adresa URL podpory** .  (Tyto informace *se* zobrazí veřejně).

**Kontakt podpory** (povinné): Pro obecné dotazy k podpoře.

**Technický kontakt** (povinné): Pro technické dotazy.

**Kontakt Správce kanálů** (povinné): Pro otázky prodejců související s programem CSP.

#### <a name="files-and-images"></a>Soubory a image

- **Dokumenty** (povinné): Přidejte související marketingové dokumenty pro vaši nabídku ve formátu PDF, který poskytuje minimálně jeden (1) a maximálně tři (3) dokumentů na jednu nabídku.
- **Bitové kopie** (volitelné): K dispozici jsou několik míst, kde se na webu Marketplace můžou zobrazit obrázky loga vaší nabídky, a to vyžaduje následující velikosti: 48 x 48 pixelů _(povinné),_ střední: 90 x 90 pixelů, Velká: 216 x 216 pixelů _(požadováno), v_ rozsahu: 255 x 115 pixelů a Hero: 815 x 290 pixelů. Všechny obrázky musí být v. Formát PNG.
- **Snímky obrazovky** (povinné): Přidejte snímky obrazovky, které demonstrují vaši nabídku. Je možné přidat maximálně pět (5) snímků obrazovky a velikost by měla být 1280 × 720 pixelů. Všechny obrázky musí být v. Formát PNG.
- **Videa** (volitelné): Přidejte odkazy na videa, která demonstrují vaši nabídku. Můžete použít odkazy na videa YouTube nebo Vimeo, která se zobrazí spolu s vaší nabídkou pro zákazníky. Budete taky muset zadat miniaturu videa, ve formátu PNG se velikost nastaví na 1280 x 720 pixelů. Můžete zobrazit maximálně čtyři videa na jednu nabídku.

Před přechodem k další části si nezapomeňte **Uložit** .

#### <a name="additional-marketplace-listing-resources"></a>Další prostředky pro výpis Marketplace

- [Osvědčené postupy pro výpisy nabídky na webu Marketplace](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)


## <a name="preview"></a>Náhled

Karta **Náhled** umožňuje definovat **skupinu omezené verze Preview** pro uvolnění vaší nabídky před publikováním vaší nabídky do širší cílové skupiny na webu Marketplace.

> [!IMPORTANT]
> Před tím, než se vaše nabídka po kontrole ve verzi Preview vrátí do veřejné skupiny Marketplace, musíte vybrat možnost **Přejít na aktivní** .

- **Definujte cílovou skupinu verze Preview: Přidejte jeden e-mailový účet AAD/MSA na každý řádek a volitelně také popis.**

Přidejte až deset (10) e-mailových adres ručně, nebo dvacet (20) při nahrávání souboru CSV, pro stávající účet Microsoft (MSA) nebo účty Azure Active Directory, které vám pomůžou při ověřování vaší nabídky před publikováním živě. Přidáním těchto účtů definujete cílovou skupinu, pro kterou bude přístup k vaší nabídce povolený ve verzi Preview, než bude publikována na webu Marketplace. Pokud je vaše nabídka už živá, můžete i nadále definovat cílovou skupinu verze Preview pro testování změn nebo aktualizací vaší nabídky.

> [!NOTE]
> Cílová skupina verze Preview se liší od soukromé cílové skupiny. Cílová skupina Preview má povolený přístup k vaší nabídce _před_ publikováním živě na Marketplace. Můžete se také rozhodnout vytvořit plán a zpřístupnit ho pouze pro privátní cílovou skupinu. Na kartě **seznam plánů** můžete definovat privátní cílovou skupinu pomocí zaškrtávacího políčka **Toto je soukromý plán** . Pak můžete definovat privátní cílovou skupinu až 20 000 zákazníků, kteří používají ID tenantů Azure.

## <a name="technical-configuration"></a>Technická konfigurace

Karta **Technická konfigurace** definuje technické podrobnosti (cesta URL, Webhook, ID TENANTA a ID aplikace), které se používají pro připojení k vaší nabídce. Toto připojení nám umožňuje zřídit vaši nabídku pro koncového zákazníka, pokud se k tomu rozhodne získat. Diagramy popisující použití shromážděných polí jsou k dispozici v dokumentaci pro [rozhraní API pro plnění SaaS](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2).

- **Adresa URL cílové stránky** (povinné): Zadejte adresu URL webu, na kterou budou zákazníci zakládat po získání vaší nabídky z webu Marketplace. Tato adresa URL bude koncovým bodem, který obdrží token při směrování zákazníka na stránku. Tento token se dá vyměňovat pro podrobnosti zřizování pomocí řešení v rozhraních API pro plnění. Tyto podrobnosti a všechny ostatní, které shromažďujete, se dají použít jako součást interaktivní webové stránky zákazníka integrované v rámci vašeho prostředí k dokončení registrace a aktivaci jejich nákupu.

- **Webhook připojení** (povinné): Pro všechny asynchronní události, které Microsoft potřebuje k odeslání jménem zákazníka (příklad: Předplatné SaaS je neplatné. vyžadujeme vám, abyste poskytli Webhook připojení. Pokud ještě nemáte systém Webhooku, nejjednodušší konfigurace bude mít aplikaci logiky koncového bodu http, která bude naslouchat všem událostem, které se do ní odesílají, a pak je správně zpracovat (např. https\/:/prod-1westus.Logic.Azure.com:443/Work). Další informace najdete v tématu [pracovní postupy volání, triggeru nebo vnoření pracovních postupů s koncovými body HTTP v Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-http-endpoint).

- **ID tenanta Azure AD** (povinné): V Azure Portal musíme [vytvořit aplikaci Azure Active Directory (AD)](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) , abyste mohli ověřit, že připojení mezi našimi dvěma službami je za ověřenou komunikací. [ID tenanta](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)zjistíte tak, že přejdete na Azure Active Directory a vyberete **vlastnosti**a pak vyhledáte uvedené číslo **ID adresáře** (např. 50c464d3-4930-494c-963c-1e951d15360e).

- **ID aplikace Azure AD** (povinné): Budete také potřebovat [ID aplikace](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) a ověřovací klíč. Tyto hodnoty získáte tak, že přejdete na Azure Active Directory a vyberete **Registrace aplikací**a pak vyhledáte uvedené číslo **ID aplikace** (např. 50c464d3-4930-494c-963c-1e951d15360e). Ověřovací klíč najdete tak, že přejdete na **Nastavení** a vyberete **klíče**. Budete muset zadat popis a dobu trvání a pak zadat číselnou hodnotu.

 Všimněte si, že ID aplikace Azure je přidružené k vašemu ID vydavatele, takže se ujistěte, že se stejné ID aplikace používá ve všech vašich nabídkách.

## <a name="plan-overview"></a>Přehled plánu

Karta **Přehled plánu** umožňuje v rámci stejné nabídky poskytnout nejrůznější možnosti plánu. Tyto plány (někdy označované jako SKU) se mohou lišit v souvislosti s verzemi, finanční zhodnocení a úrovněmi služby. Aby bylo možné nabídku prodávat na webu Marketplace, musíte nastavit aspoň jeden plán.

Po vytvoření se zobrazí vaše názvy plánů, ID, cenové modely, dostupnost (veřejné nebo soukromé), aktuální stav publikování a všechny dostupné akce.

**Akce** , které jsou k dispozici v **přehledu plánu** , se liší v závislosti na aktuálním stavu plánu a mohou zahrnovat:

- Pokud je stav plánu **koncept** – odstranit koncept
- Pokud je stav plánu **Live** – zastavit prodej plánu nebo synchronizovat soukromou cílovou skupinu

**Vytvořit nový plán** (minimálně jeden plán pro ty, kteří si vybrali prodej prostřednictvím Microsoftu)

- **ID plánu:** Pro každý plán v této nabídce Vytvořte jedinečné ID plánu. Toto ID se bude zobrazovat zákazníkům v adrese URL produktu a šablony Azure Resource Manager (pokud jsou k dispozici). Používejte jenom malá písmena, alfanumerické znaky, pomlčky nebo podtržítka. Pro toto ID plánu je povoleno maximálně 50 znaků. Všimněte si, že po výběru možnosti vytvořit nelze ID změnit.
- **Název plánu:** Zákazníci uvidí tento název při rozhodování, který plán vybrat v rámci vaší nabídky. Pro každý plán v této nabídce Vytvořte jedinečný název nabídky. Název plánu slouží k rozlišení softwarových plánů, které mohou být součástí stejné nabídky (např. Název nabídky: Windows Server; plánují Windows Server 2016, Windows Server 2019).

### <a name="plan-listing"></a>Výpis plánu

Karta **seznam plánů** zobrazuje jazyky (a trhy), kde je váš plán k dispozici, v současné době je dostupná jenom angličtina (USA). Kromě toho tato stránka zobrazuje stav seznamu pro konkrétní jazyk a datum a čas, kdy byl přidán. Pro každý jazyk nebo trh budete muset definovat podrobnosti o Marketplace (název nabídky, popis, hledané výrazy atd.).

#### <a name="plan-listing-details"></a>Podrobnosti výpisu plánu

Výběrem jednoho z jazyků plánování se zobrazí informace o **seznamu plánu** , včetně **názvu** a **popisu.**

- **Název**: Tato možnost se předem vyplní na základě vaší **nové položky plánu** Preview a zobrazí se jako název "softwarového plánu vaší nabídky" zobrazeného na webu Marketplace.
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

Pokud chcete získat informace o zákaznických předplatných, která se aktuálně účastní bezplatné zkušební verze `isFreeTrial`, použijte novou vlastnost rozhraní API, která bude označená jako true nebo false. Další informace najdete v tématu [rozhraní API pro SaaS získat předplatné](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2#get-subscription) .

>[!Note]
>Bezplatné zkušební verze se nepodporují pro plány, které využívají službu měření na webu Marketplace.

#### <a name="markets"></a>Trhy

- **Upravit trhy** volitelné

Každý plán musí být k dispozici alespoň v jednom trhu. Zaškrtněte políčko pro jakékoli místo na trhu, kde byste chtěli tento plán zpřístupnit. K nápovědě jsou k dispozici vyhledávací pole a tlačítko pro výběr zemí "daňového přiznání", ve kterých společnost Microsoft provádí prodej a používání daně vaším jménem. 


Pokud jste už pro svůj plán nastavili ceny v USA dolary (USD) a přidáte další místo na trhu, cena za nový trh se vypočítá podle aktuálních směnných kurzů. Před publikováním byste měli vždycky zkontrolovat cenu za každý trh. Po uložení změn můžete pomocí odkazu exportovat ceny (XLSX) zkontrolovat ceny.

#### <a name="pricing"></a>Ceny

- **Cenový model**: Paušální sazba nebo na základě pracovních stanic

**Paušální sazba:** Umožněte přístup k vaší nabídce s jednou měsíčně nebo roční cenou za paušální cenu. Tato situace se někdy označuje jako ceny na základě lokality. Pomocí tohoto cenového modelu můžete volitelně definovat měřené plány, které používají rozhraní API služby pro měření softwaru Marketplace k účtování zákazníků podle nestandardních jednotek.  Další informace o měřených fakturách najdete v tématu [účtované vyúčtování pomocí služby měření na webu Marketplace](./saas-metered-billing.md).

**Na uživatele:** Umožněte přístup ke svojí nabídce za cenu na základě počtu uživatelů, kteří přistupují k nabídce nebo při používání stanic. Tento model založený na uživateli vám umožní nastavit minimální a maximální počet povolených uživatelů na základě ceny. Tímto způsobem lze nakonfigurovat různé cenové body na základě počtu uživatelů konfigurací více plánů.  Tato pole jsou volitelná. Pokud je ponecháno bez výběru, počet uživatelů bude interpretován jako neomezený (minimální počet 1 a maximální počet, jak může systém podporovat). Tato pole je možné upravovat v rámci aktualizace vašeho plánu.

Po publikování se volba cenového modelu fakturace nedá změnit. Kromě toho musí všechny plány pro stejnou nabídku sdílet stejný cenový model.

- **Fakturační období**: Měsíčně nebo roční

Vyberte frekvenci, kterou zákazníci musí platit v uvedené ceně. Musí se zadat aspoň jedna měsíční nebo roční cena nebo můžou být dostupné obě možnosti pro zákazníky.

- **Cena**: USD za měsíc nebo USD za rok

Ceny nastavené v místní měně (USD = USA dolar) se převedou na místní měnu všech vybraných trhů pomocí aktuálních kurzů pro Exchange, které jsou k dispozici během instalace. Před publikováním tyto ceny ověřte, a to tak, že vyexportujete tabulku cen a zkontrolujete cenu na každém trhu. Pokud chcete nastavit vlastní ceny v individuálním trhu, upravte a importujte tabulku s cenami. Zodpovídáte za ověřování těchto cen a jejich nastavení.
* *Aby bylo možné exportovat data o cenách, musíte nejdřív Uložit změny cen.*

Před publikováním pečlivě zkontrolujte své ceny, protože existují určitá omezení, co se může po publikování plánu změnit:

- Po publikování plánu se cenový model nedá změnit.
- Po publikování fakturačního období pro určitý plán ho nebude možné odebrat později.
- Jakmile je cena za trh v plánu publikovaná, nedá se později změnit.

### <a name="plan-audience"></a>Cílová skupina plánu

Máte možnost nakonfigurovat každý plán tak, aby byl viditelný pro všechny uživatele, nebo jenom pro konkrétní cílovou skupinu, kterou zvolíte. V této omezené cílové skupině můžete přiřadit členství pomocí ID tenanta Azure AD.

#### <a name="privacy"></a>Ochrana osobních údajů

- **Toto je soukromý plán** . (Volitelné zaškrtávací políčko)

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

## <a name="publish"></a>Publikování

#### <a name="submit-offer-to-preview"></a>Odeslat nabídku do verze Preview

Až dokončíte všechny požadované části nabídky, vyberte **publikovat** v pravém horním rohu portálu. Budete přesměrováni na stránku **Revize a publikování** . 

Pokud tuto nabídku publikujete poprvé, můžete:

- Podívejte se na téma stav dokončení pro jednotlivé části nabídky.
    - Nezahájeno – znamená, že se oddíl nedotkl a musí se dokončit.
    - *Nedokončeno* – znamená, že oddíl obsahuje chyby, které je třeba opravit, nebo vyžaduje, aby bylo k dispozici více informací. Vraťte se prosím k oddílům a aktualizujte je.
    - *Complete* – znamená, že část je dokončena, byla zadána všechna požadovaná data a nejsou k dispozici žádné chyby. Aby bylo možné nabídku odeslat, musí být všechny oddíly nabídky v úplném stavu.
- Poskytněte pokyny k testování certifikačního týmu, abyste měli jistotu, že vaše aplikace je testována správně, kromě doplňkových poznámek, které jsou užitečné pro porozumění vaší aplikaci.
- Kliknutím na **Odeslat**nabídku pro publikování odešlete. Pošleme vám e-mail s oznámením, že je k dispozici verze Preview vaší nabídky, kterou si můžete prohlédnout a schválit. Je nutné, abyste se vrátili do partnerského centra a vybrali možnost pro publikování nabídky na veřejném webu (nebo v případě soukromé nabídky pro privátní cílovou skupinu).

## <a name="next-steps"></a>Další postup

- [Aktualizace stávající nabídky na komerčním webu Marketplace](./update-existing-offer.md)
