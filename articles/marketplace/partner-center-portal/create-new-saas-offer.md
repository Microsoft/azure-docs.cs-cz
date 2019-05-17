---
title: Vytvoření nové nabídky SaaS v komerční Marketplace
description: Jak vytvořit nový Software jako služba (SaaS) nabídka pro výpis nebo prodej na webu Azure Marketplace, AppSource, nebo prostřednictvím programu Cloud Solution Provider (CSP) pomocí portálu pro komerční Marketplace na Microsoft Partner Center.
author: mattwojo
manager: evansma
ms.author: mattwoj
ms.service: marketplace
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: 32537426b7b9b1a7015610fc0c3e2dd7c3efa49b
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2019
ms.locfileid: "65806203"
---
# <a name="create-a-new-saas-offer"></a>Vytvoření nové nabídky SaaS

Zahajte proces vytváření softwaru, jako služba (SaaS) nabízí, ujistěte se, které jste první [vytvořit účet v partnerském centru](./create-account.md) a otevřete [řídicí panel komerční Marketplace](https://partner.microsoft.com/dashboard/commercial-marketplace/offers), s **nabízí** vybraná karta. 

![Komerční řídicího panelu webu Marketplace na partnerské Centrum](./media/commercial-marketplace-offers.png)

Vyberte + **vytvořit novou...** tlačítko a pak vyberte **Software jako služba** položky nabídky. 

Pokud vyberete jednu z ostatních typů nabídky, budete přesměrováni na starší [portál partnerů cloudu](https://cloudpartner.azure.com/).  V tuto chvíli jsou k dispozici v komerční Marketplace portál na partnerském centru jenom nabídky SaaS. 

![Vytvoření nabídky okna na partnerské Centrum](./media/new-offer.png)


**Nová nabídka** se zobrazí dialogové okno. ![Dialogové okno Nová nabídka](./media/new-offer-popup.png)


## <a name="offer-id-and-name"></a>Název a ID nabídky

- **ID nabídky**: Vytvořte jedinečný identifikátor pro každou nabídku ve vašem účtu. Toto ID se nebude zobrazovat na zákazníky v adresu URL pro tuto nabídku marketplace a šablon Azure Resource Manageru (pokud existuje). ID nabídky musí být malými písmeny, alfanumerické znaky (včetně spojovníky a podtržítka, ale bez mezer). To je omezen na 50 znaků a nedá se aktualizovat po výběru vytvořit.  
Příklad: test nabídka-1
<br>Výsledkem je adresa URL: `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`

- **Název nabídky**: Oficiální název SaaS aplikace nabídky, konzistentní v rámci celého publikací, oznámení o inzerovaných programech a weby.  Tento název může být obchodní značkou.  Nabízejí název nesmí obsahovat prázdné znaky, emoji (pokud jsou ochranné známky nebo copyright symbol) a musí být maximálně 50 znaků.
<br>Příklad: Nabídka testování 1&#8482;

Vyberte **Vytvořit**.  **Nabízí přehled** stránka je vytvořena pro tuto nabídku.  

![Nabídka přehled partnerského centra](./media/commercial-marketplace-offer-overview.png)

## <a name="offer-overview"></a>Přehled nabídky

**Nabízí přehled** stránka obsahuje: 

- **Stav publikování** zobrazí vizuální znázornění kroky potřebné k publikování v rámci této nabídky a jak dlouho bude trvat jednotlivé kroky k dokončení. Neúplné publikování ikony kroku bude zapnutá. 

- **Nabízí přehled** nabídka obsahuje seznam odkazů pro provádění operací v rámci této nabídky. Tento seznam operací se budou měnit v závislosti na výběru, který provedete pro vaši nabídku.  
    - Pokud tato nabídka je koncept – návrh Delete 
    - Pokud je v provozu – nabídka Stop prodávat nabídky 
    - Pokud tato nabídka je ve verzi preview – uvedení do provozu 
    - Pokud jste nedokončili vydavatele odhlašování – zrušení publikování

## <a name="offer-setup"></a>Nabídka nastavení

**Nabízejí nastavení** kartu vyzve k zadání následujících informací. Vyberte **Uložit** po dokončení těchto polí.

- **Chcete prodávat prostřednictvím společnosti Microsoft?** (Ano/Ne)
    - **Ano**, které byste rádi prodej vaší nabídky Microsoftu s Microsoftem, který je hostitelem transakce marketplace vaším jménem; nebo 
    - **Ne**, si přejete právě seznamu vaši nabídku prostřednictvím Tržiště, zpracování jakékoli peněžní transakce nezávisle na Microsoft.    

### <a name="sell-through-microsoft"></a>Jejím prostřednictvím – Microsoft

Prodej prostřednictvím Microsoft poskytuje lepší zjišťování zákazníka a akvizice, umožňuje společnosti Microsoft na webu marketplace transakce hostitele vaším jménem a využívá možnosti globálně dostupné obchodování od Microsoftu.

#### <a name="saas-offer-requirements"></a>Nabídky SaaS požadavky

Pokud chcete vypsat Software jako služba (SaaS) nabízí komerční Marketplace na partnerském centru, musí splňovat následující kritéria:

- Nabídky musí být kompatibilní s Azure klienty. (Často aplikace SaaS jsou také hostované v Azure pro nejlepší výkon a kompatibilitu, ale to není povinné.) 
- Musíte použít vaši nabídku [Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) ke správě identit a ověřování.
- Musíte použít vaši nabídku [rozhraním API SaaS splnění](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-fulfillment-api-v2) můžete integrovat s Azure Marketplace.

#### <a name="billing-infrastructure-costs"></a>Fakturační náklady na infrastrukturu
Nabídky SaaS, jako vydavatel, musíte vzít v úvahu poplatky za využití infrastruktury Azure a licenční poplatky za software jako náklady na jednu položku. Tyto náklady je vyjádřena jako fixní měsíční poplatek, abyste zákazníka. Využití infrastruktury Azure je spravovaná a fakturuje se vám, partner, přímo. Poplatky za využití infrastruktury skutečný nevidí zákazníka. Vydavatelé se obvykle optimalizované poplatky za využití infrastruktury Azure začlenit do svých ceny licencí softwaru. 

Software licenční poplatky se zobrazují jako měsíční, opakované odběr založený na webu paušální poplatek a nejsou měřené nebo na základě spotřeby.

|**Vaše náklady na licence**|**100 USD za měsíc**|
|:---|:---|
|Náklady na využití Azure (D1 / 1jader)|Účtuje se přímo k vydavateli, nejste zákazník|
|Microsoft účtuje|100,00 USD za měsíc (vydavatel musí odpovídat žádné náklady na infrastrukturu vzniklých nebo průchozí v licenční poplatek za)|

- V tomto scénáři Microsoft účtuje 100,00 USD pro licence na software a zaplatí si $80,00 k vydavateli.
- Partneři, kteří prokázali způsobilost pro **snižuje poplatek za služby webu Marketplace** uvidí poplatek nižší transakce na SaaS, nabízí z května 2019 až do roku 2020 dne. V tomto scénáři Microsoft účtuje 100,00 USD pro licence na software a zaplatí si $90.00 k vydavateli.

> [!NOTE]
> **Snížená poplatek za služby webu Marketplace**: U určitých SaaS nabízí, že jste publikovali na naše obchodní Marketplace, Microsoft omezí jeho poplatek za služby webu Marketplace z 20 % (jak je popsáno ve smlouvě vydavatele Microsoft) na 10 %. V pořadí pro vaši nabídku k vyfiltrování alespoň jedna z nabídek musí mít určená microsoftem jako připravený pro společný prodej IP nebo společný prodej IP nastaveno jako prioritní.  Způsobilost musí být splněny alespoň pět (5) pracovních dní před koncem každého kalendářního měsíce dostávat toto snížené poplatek za služby webu Marketplace za měsíc.  Servisní poplatek nižší Marketplace se nevztahuje na virtuální počítače, spravované aplikace nebo žádné produkty k dispozici prostřednictvím našeho komerční webu Marketplace.  Servisní poplatek nižší Marketplace budou k dispozici pouze na kvalifikovaný nabídky poplatky za licence shromažďovaná společností Microsoft až 30. června 2020 31. května 2019.  Po uplynutí této doby poplatek za služby webu Marketplace vrátí na jeho normální množství. 

|**Microsoft bills**|**100 USD za měsíc**|
|:---|:---|
|Společnost Microsoft platí, 80 % vaše náklady na licence <br>**Pro kvalifikované aplikace SaaS společnost Microsoft platí 90 % vaše náklady na licence*|80,00 $ za měsíc <br>*$* 90.00 za měsíc *|


#### <a name="csp-program-opt-in"></a>CSP Program Opt-in
[Cloud Solution Provider (CSP)](https://docs.microsoft.com/azure/marketplace/cloud-solution-providers) program umožňuje nabídky softwaru umožňuje oslovit miliony kvalifikovaný zákazníkům Microsoftu s minimálními marketingové a prodejní investice.

- **Kanály: Uvolněte své nabídky do programu CSP** (zaškrtávací políčko)

Jak zvolit zpřístupnění vaší nabídky v programu CSP umožňuje poskytovatele Cloud Solution Provider, aby je prodal produktu jako součást sady řešení svým zákazníkům. 

### <a name="list-through-microsoft"></a>Seznam prostřednictvím společnosti Microsoft

Tím, že vytvoříte tržišti propagaci vašeho podnikání s Microsoftem. Výběr vypisovat jenom nabídky a není transact prostřednictvím Microsoft znamená, že společnost Microsoft není součástí přímo transakce licence softwaru. Se neúčtují žádné poplatky přidružené transakce a vydavatel udržuje 100 % žádné poplatky, shromážděných od zákazníka pro licencování softwaru. Vydavatel je však zodpovědní za technickou podporu všech aspektů transakce licence softwaru, včetně, ale nikoli výhradně: order splnění, měření, fakturace, fakturaci, platby a kolekce. 

- **Jak chcete potenciálních zákazníků pro interakci s touto nabídkou výpis**

##### <a name="get-it-now-free"></a>Získat (zdarma)
Seznam vaší nabídky zákazníkům zdarma tím, že poskytuje platnou adresu URL (začíná http nebo https), ve kterém přístup k aplikaci.  Příklad: `https://contoso.com/saas-app`

##### <a name="free-trial"></a>Bezplatná zkušební verze
Seznam vaší nabídky zákazníkům na bezplatné vyzkoušení tím, že poskytuje platnou adresu URL (začíná http nebo https), ve kterém přístup k aplikaci.  Příklad: `https://contoso.com/trial/saas-app`

##### <a name="contact-me"></a>Kontaktujte mě
Shromážděte kontaktní údaje zákazníků propojením váš systém řízení vztahů se zákazníky (CRM). Zákazník budete vyzváni k zadání oprávnění ke sdílení svých informací. Tyto podrobnosti o zákazníkovi, společně se název nabídky, ID a marketplace zdroj, kde najdou vaši nabídku, se odešlou do systému CRM, které jste nakonfigurovali. Další informace o konfiguraci CRM, naleznete v tématu [Správa potenciálních zákazníků připojit](#connect-lead-management). 

## <a name="enable-a-test-drive"></a>Povolit testovací verze

Testovací verze je skvělý způsob, jak jim umožňuje "před zakoupením vyzkoušet", což vede k vyšší převodu a vytváření vysoce kvalifikovaných prodejních příležitostí prezentaci vaší nabídky pro potenciální zákazníky. [Další informace o testu jednotek.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)

- **Povolit testovací verze** (checkbox) 

Tím, že testovací verze, bude výzva ke konfiguraci ukázky prostředí pro zákazníky, kteří akci nabídky pro pevné časové období. 

### <a name="type-of-test-drive"></a>Typ testu jednotky

- **[Azure Resource Manager](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)**: Nasazení šablony, která obsahuje všechny prostředky Azure, které tvoří vašeho řešení. Produkty, které patří tento scénář používat jenom prostředky Azure.
- **[Dynamics 365 for Business Central](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-business-central-offer)**: Společnost Microsoft hostuje a udržuje službu test jednotky (včetně nasazení a zřizování) pro Business Central systému na plánování podnikových zdrojů (finance, operations, dodavatelského řetězce, CRM, atd.).  
- **[Dynamics 365 for Customer Engagement](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/dyn365ce/cpp-customer-engagement-offer)**: Společnost Microsoft hostuje a udržuje službu test jednotky (včetně nasazení a zřizování) pro systém Customer Engagement (prodej, služby, služby projektu služby, služby atd.).  
- **[Dynamics 365 for Operations](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-dynamics-365-operations-offer)**: Společnost Microsoft hostuje a udržuje službu test jednotky (včetně nasazení a zřizování) pro Finance and Operations enterprise prostředek plánování system (finance, operations, výroby, dodavatelský řetězec, atd.). 
- **[Aplikace logiky](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/logic-app-test-drive)**: Šablonu nasazení, včetně všech architekturách komplexní řešení. Všechny vlastní produkty, používejte tohoto typu testovací verze.
- **[Power BI](https://docs.microsoft.com/power-bi/service-template-apps-overview)**: Vložený odkaz na míru sestavené řídicí panel. Produkty, které chcete prokázat, že že interaktivní vizuálu Power BI by měl použít tento typ testovací verze. Vše, co potřebujete k nahrání zde je vaše adresa URL vložené Power BI.

#### <a name="additional-test-drive-resources"></a>Další testovací jednotky prostředky
- [Test jednotky technické osvědčené postupy](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Vyzkoušejte si marketingové osvědčené postupy](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [Test jednotky přehled jeden stránkování](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf)

## <a name="connect-lead-management"></a>Připojte se správa potenciálních zákazníků

Spojení se zákazníky přímo tak, že výpis vaší nabídky v tržišť a zapojování váš systém řízení vztahů se zákazníky (CRM) tak, aby bylo možné přijímat kontaktní údaje zákazníků, ihned po vyjadřuje zájmu zákazníka nebo nasadí váš produkt.

- **Vyberte cíl zájemce** (rozevírací nabídky): Zadejte podrobnosti připojení k systému CRM, kde chcete poslat potenciálních zákazníků. 

Partnerské centrum podporuje následující systémy CRM pro Správa potenciálních zákazníků. Vyberte odkaz pro pokyny k instalaci.

- Azure Blob – zadat kontaktní e-mailu, název kontejneru a připojovací řetězec účtu úložiště. 
- [Azure Table](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-azure-table) – zadat kontaktní e-mailu a připojovací řetězec účtu úložiště. 
- [Dynamics CRM Online](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics) – zadat kontaktní e-mailové adresy URL a režim ověřování (Office 365 nebo Azure Active Directory).
- [Koncový bod HTTPS](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-https) – zadejte kontaktní e-mail a adresa URL koncového bodu HTTPS. 
- [Marketo](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-marketo) – zadat kontaktní e-mailu, ID formuláře, Munchkin ID účtu a ID serveru.
- [Salesforce](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-salesforce) -zadat kontaktní e-mailu a ID organizace. 

#### <a name="additional-lead-management-resources"></a>Další vedoucí řízení zdrojů
- [Vedoucí řízení nejčastější dotazy](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [Běžné chyby konfigurace potenciálních zákazníků](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#common-lead-configuration-errors-during-publishing-on-cloud-partner-portal)
- [Vedoucí jednoho stránkování přehled správy](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf)

Nezapomeňte **Uložit** přejde k další části!

## <a name="properties"></a>Vlastnost
**Vlastnosti** kartu vás vyzve k definování kategorií a průmysl používá k seskupení vaší nabídky na Tržiště, právní smlouvy podporou vaší nabídky a verze vaší aplikace. 

Vyberte **Uložit** po dokončení těchto polí. 

### <a name="category"></a>Category
Vyberte minimálně z jedné (1) a maximálně tři (3) kategorií určena k seskupování vaší nabídky do oblasti vyhledávání odpovídající marketplace. Požádejte o tom, jak vaši nabídku podporuje tyto kategorie v popis nabídky. 

### <a name="industry"></a>Obor
Vyberte až dva (2) odvětví určena k seskupování vaší nabídky do oblasti vyhledávání odpovídající marketplace. Pokud vaše nabídka není specifické pro odvětví, nesmí být zvolen jeden. Požádejte o tom, jak vaši nabídku podporuje vybrané obory v popis nabídky. 

### <a name="app-version"></a>verze aplikace
Toto je volitelné pole používá k identifikaci číslo verze vaší nabídky na webu Marketplace AppSource. 

### <a name="standard-contract"></a>Standardní kontrakt
- **Použít standardní smlouvy?** 

Zjednodušit proces zajišťování pro zákazníky a snižuje složitost právní pro dodavatele softwaru, společnost Microsoft nabízí standardní smluvní šablony k usnadnění transakce na webu Marketplace. 

Místo vytváření vlastních podmínek a ujednání, můžete zvolit standardní smlouvou, který zákazníci potřebují pouze jednou přijmout a ověřte svůj software nabízet vydavatelům Azure Marketplace. 

Standardní smlouvy najdete tady: https://go.microsoft.com/fwlink/?linkid=2041178.

##### <a name="terms-of-use"></a>Podmínky použití
Pokud licenční podmínky se liší od standardní smlouvy, můžete se rozhodnout zadejte vlastní právní podmínky použití zde. Můžete také zadat až 10 000 znaků textu v tomto poli. Pokud vaše podmínky použití vyžadovat delší popis, zadejte odkaz na jednu adresu URL do tohoto pole, kde můžete najít další licenční podmínky. Zákazníkům se zobrazí jako odkaz na aktivní.

Zákazníkům je potřeba přijmout tyto podmínky předtím, než se pokusí vaší aplikace. 

Nezapomeňte **Uložit** přejde k další části!

## <a name="offer-listing"></a>Výpis nabídky
Nabídky zobrazí kartu jazyky (a trhy), kde je k dispozici vaší nabídky, aktuálně Angličtina (Spojené státy) je k dispozici pouze umístění. Kromě toho tato stránka zobrazuje stav výpis specifické pro jazyk a datum a čas, který byl přidán. Budete muset zadat podrobnosti marketplace (nabízejí název, popis, hledané termíny, atd.) pro jednotlivé jazyky / uvedení na trh.

### <a name="offer-listings"></a>: Výpisy nabídek
Zadejte podrobnosti o který se má zobrazit na webu Marketplace, včetně popisu vaší nabídky a marketingové prostředky.

- **Název** (povinné): Název definovaný v tomto poli se zobrazí jako nadpis vašich nabídek zveřejněných na marketplace(s), kterou jste zvolili. Název je předem podle předchozího **nová nabídka** položka.  To může být obchodní značkou.  To nesmí obsahovat prázdné znaky, emoji (pokud jsou symboly pravidlech používání značky a autorských práv) a musí být maximálně 50 znaků.
- **Souhrn** (povinné): Zadejte krátký popis vaší nabídky, který se má použít ve výsledcích hledání listing(s) marketplace. V tomto poli je třeba zadat maximálně 100 znaků textu.
- **Popis** (povinné): Zadejte popis vaší nabídky, který se má zobrazit v přehledu listing(s) marketplace. Zvažte návrh hodnoty, klíčové výhody, kategorie nebo odvětví přidružení, nákupy v aplikaci příležitosti potřebné informace a odkaz na další informace.
V tomto poli je třeba zadat maximálně 3 000 znaků textu. Další tipy naleznete v článku [napsat skvělou aplikaci popis](https://docs.microsoft.com/windows/uwp/publish/write-a-great-app-description).
- **Klíčová slova pro vyhledávání**: Zadejte až tři klíčová slova pro vyhledávání, které zákazníci můžou využít k hledání marketplace(s) vaší nabídky.
- **Začínáme se službou pokyny** (povinné): Vysvětluje postup při konfiguraci a začít používat aplikace pro potenciální zákazníky.  Tento rychlý start může obsahovat odkazy na podrobnější online dokumentaci. V tomto poli je třeba zadat maximálně 3 000 znaků textu. 

#### <a name="links"></a>Odkazy

- **Zásady ochrany osobních údajů** (povinné): Odkaz na zásady ochrany osobních údajů vaší organizace. Zodpovídáte za to, že vaše aplikace odpovídá ochrany osobních údajů zákonům a předpisům a k zajištění zásady platné ochrany osobních údajů
- **Marketingové materiály programu CSP** (volitelné): Je nutné zadat odkaz na marketingové materiály, pokud budete chtít rozšířit vaši nabídku [Cloud Solution Provider (CSP)](https://docs.microsoft.com/azure/marketplace/cloud-solution-providers) programu. CSP umožňuje partnerům CSP vytvořit balíček na trhu a dál prodávat vaše nabídka rozšiřuje vaši nabídku k širší škále způsobilým zákazníkům. Prodejci bude potřebovat přístup k materiály pro marketingu vaší nabídky. Další informace najdete v tématu [Go-To-Market Services](https://partner.microsoft.com/reach-customers/gtm).
- **Užitečné odkazy** (volitelné): Volitelné další online dokumenty o vaši aplikaci nebo související služby uvedené tím, že poskytuje **Title** a **URL**. Kliknutím na Přidat další užitečné odkazy **+ přidat adresu URL**.

#### <a name="contact-information"></a>Kontaktní údaje

- **Kontakty**: Pro každého zákazníka kontaktu, poskytují zaměstnanci **název** , **telefonní číslo**, a **e-mailu** adresu.  (Tyto *nebudou* veřejně zobrazovat,). A **adresu URL podpory** je také nutné pro **kontakt podpory** skupiny.  (Tyto informace *bude* veřejně zobrazovat,).

**Kontaktujte podporu** (povinné): Pro obecné dotazy.

**Technické kontakt** (povinné): Pro technické dotazy.

**Kontaktujte správce kanálu** (povinné): Pro prodejce pro otázky týkající se programu CSP.

#### <a name="files-and-images"></a>Soubory a obrázky

- **Dokumenty** (povinné): Přidáte související dokumenty marketingu vaší nabídky ve formátu PDF, minimálně z jedné (1) a maximálně tři (3) dokumentů na nabídku.
- **Obrázky** (volitelné): Existuje více místa, kde vaši nabídku logo obrázky můžou vypadat v celém marketplace(s), které vyžadují následující formáty--(krátkodobé používání): 48 x 48 pixelů _(povinné),_ médium: 90 x 90 pixelů, velké: pixely 216 x 216 _(povinné),_ široké: 255 x 115 pixelů a Hero: 815 x 290 pixelů. Všechny Image musí být v. Formát PNG.
- **Snímky obrazovky** (povinné): Přidáte snímky obrazovky demonstrace vaší nabídky. Maximálně pět (5) snímky obrazovky můžete přidat a musí mít velikost v pixelech 1280 × 720. Všechny Image musí být v. Formát PNG.
- **Videa** (volitelné): Přidáte odkazy na videa demonstrace vaší nabídky. Můžete použít odkazy na videa YouTube nebo Vimeo, které se zobrazují společně s vaší nabídky zákazníkům. Budete taky muset zadat miniaturu videa, velikost, aby 1280 × 720 pixelech ve formátu PNG. Můžete zobrazit maximálně čtyři videí na nabídku.

Nezapomeňte **Uložit** přejde k další části!

#### <a name="additional-marketplace-listing-resources"></a>Další marketplace výpis prostředků

- [Osvědčené postupy pro marketplace nabízí výpisy](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)



## <a name="preview"></a>Náhled

**Ve verzi Preview** karta vám umožní definovat omezeného **cílovou skupinou ve verzi Preview** pro uvolnění vaši nabídku před publikováním nabídky za provozu pro širší cílovou skupinu webu marketplace.

> [!IMPORTANT]
> Musíte vybrat **vysílat živě** před nabídky bude možné v přednášce publikované živě k veřejné čtenářům nebo divákům marketplace po kontrole nabídky ve verzi Preview.

- **Definujte cílovou skupinou ve verzi Preview: Přidáte e-mail účtu AAD/MSA na řádek, spolu s volitelný popis.**

Ručně přidejte e-mailové adresy až deset (10) nebo dvacet (20), pokud chcete nahrát soubor CSV pro existující účet Microsoft (MSA) nebo účty Azure Active Directory (AAD), abychom vám pomohli s ověřování vaši nabídku před publikováním live. Přidáním těchto účtů, definujete, která se bude dát přístup k verzi preview pro vaši nabídku před publikováním marketplace(s) cílové skupině. Pokud vaše nabídka je už v provozu, může stále definovat cílovou skupinou ve verzi preview pro testování změny nebo aktualizace pro vaši nabídku.

> [!NOTE]
> Cílová skupina ve verzi preview se liší od privátní cílovou skupinu. Cílová skupina ve verzi preview je povolen přístup k vaší nabídce _předchozí_ k publikovanému za provozu v tržišť. Můžete také vytvořit plán a zpřístupnit pouze pro privátní cílovou skupinu. V **plánování výpis** kartu, můžete definovat privátní cílovou skupinu s **Toto je plán soukromý** zaškrtávací políčko. Potom můžete definovat privátní cílové skupiny až 20 000 zákazníků pomocí ID Tenanta Azure.

## <a name="technical-configuration"></a>Technické konfigurace

**Technické konfigurace** kartě definuje technické podrobnosti (cesta adresy URL webhooku, ID tenanta a ID aplikace), použít pro připojení k vaší nabídce. Toto připojení umožňuje nám to zřízení nabídkou jako prostředek v rámci předplatného Azure zákazníkem, pokud se rozhodnete ho získat.

- **Adresa URL cílové stránky** (povinné): Definujte lokality adresu URL, která budou mít zákazníci řízené objevil na po získání vaši nabídku z webu marketplace. Tato adresa URL bude také koncového bodu, který bude přijímat připojení rozhraní API pro usnadnění obchodování s Microsoftem.

- **Připojení webhooku** (povinné): Pro všechny asynchronní události, které je potřeba poslat jménem zákazníka Microsoftu (Příklad: Předplatné Azure náramků RFID neplatný), potřebujeme, abyste k poskytování připojení webhooku. Pokud ještě nemáte systému webhooku v místě, je nejjednodušší konfiguraci, aby aplikace logiky koncový bod HTTP, který bude naslouchat žádné události odeslání do ní a odpovídajícím způsobem jejich zpracování (například https://prod-1westus.logic.azure.com:443/work). Další informace najdete v tématu [volání triggeru, nebo vnořené pracovní postupy s koncovými body HTTP ve službě logic apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-http-endpoint).

- **ID tenanta Azure AD** (povinné): Uvnitř webu Azure portal, můžeme požadovat, aby vám [vytvoření aplikace Azure Active Directory (AD)](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) tak, že abychom mohli ověřit připojení mezi naše dvě služby je za ověřené komunikaci. Najít [ID tenanta](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-tenant-id), přejděte do služby Azure Active Directory a vyberte **vlastnosti**, potom se podívejte **ID adresáře** uvedené číslo (např.) 50c464d3-4930-494c-963c-1e951d15360e).

- **ID aplikace Azure AD** (povinné): Budete také potřebovat vaše [ID aplikace](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-application-id-and-authentication-key) a ověřovací klíč. K získání těchto hodnot, přejděte na Azure Active Directory a vyberte **registrace aplikací**, potom se podívejte **ID aplikace** číslo uvedené (třeba 50c464d3-4930-494c-963c-1e951d15360e). Pokud chcete najít ověřovací klíč, přejděte na **nastavení** a vyberte **klíče**. Je potřeba zadat popis a dobu trvání a pak bude třeba zadat číselnou hodnotu.

 Všimněte si, že je ID aplikace Azure přidružené k ID vydavatele, proto se ujistěte, že stejné ID aplikace se používá v všechny nabídky.

## <a name="plan-overview"></a>Přehled plánu

**Plán přehled** kartu umožňuje poskytnout širokou škálu možností plán v rámci stejné nabídky. Tyto plány (někdy označované jako skladové položky) se může lišit jde o verzi, finanční zhodnocení nebo úrovně služby. Musíte vytvořit aspoň jeden plán, aby bylo možné prodej vaší nabídky na webu Marketplace.

Po vytvoření se zobrazí plán názvy, identifikátory, cenové modely, dostupnost (veřejné a privátní), aktuální stav a všechny dostupné akce publikování.

-   **Akce** k dispozici v **plán přehled** lišit v závislosti na aktuální stav vašeho plánu a mohou zahrnovat:
  - Pokud je stav plánu **koncept** – odstranit koncept
  - Pokud je stav plánu **Live** – zastavení prodávat plán nebo privátní cílovou skupinu synchronizace

**Vytvořit nový plán** (minimálně jeden plán pro ty, kteří vyberte prodávat přes Microsoft)

- **ID plánu:** Vytvoření plánu Jedinečný ID pro každý plán v rámci této nabídky. Toto ID se nebude zobrazovat na zákazníky v produktu Azure Resource Manageru a adresy URL šablony (pokud existuje). Použijte pouze malé alfanumerické znaky, pomlčky nebo podtržítka. Pro toto ID plánu. jsou povoleny maximálně 50 znaků. Všimněte si, že ID nemůže modifikovat po výběru vytvořit.
- **Název plánu:** Zákazníci uvidí tento název při rozhodování o tom, které chcete vybrat v rámci vaší nabídky. Vytvoření nabídky jedinečný název pro každý plán v rámci této nabídky. Název plánu se používá k rozlišení softwarové plány, které mohou být součástí stejné nabídka (například Název nabídky: Windows Server. plány: Windows Server 2016, Windows Server 2019).

### <a name="plan-listing"></a>Výpis plán

**Plánování výpis** karta zobrazuje jazyků (a trhy), kde váš plán je k dispozici, aktuálně Angličtina (Spojené státy) je jediným umístěním, které jsou k dispozici. Kromě toho tato stránka zobrazuje stav výpis specifické pro jazyk a datum a čas, který byl přidán. Budete muset zadat podrobnosti marketplace (nabízejí název, popis, hledané termíny, atd.) pro jednotlivé jazyky / uvedení na trh.

#### <a name="plan-listing-details"></a>Podrobnosti plánu

Když vyberete některý z jazyků plán se zobrazí **plánování výpis** informace, včetně **název** a **popis.**

- **Název**: Předem vyplní podle ve verzi preview **nový plán** položky a zobrazí se jako název vaší nabídky "plán softwaru" zobrazí na webu Marketplace.
- **Popis:** Tento popis je příležitost k vysvětlení, co tento plán softwaru je jedinečný a případné rozdíly z jiné plány softwaru v rámci vaší nabídky. Může obsahovat až 500 znaků.

Vyberte **Uložit** po dokončení těchto polí.

#### <a name="plan-pricing-and-availability"></a>Ceny plánu a dostupnost

**Ceny a dostupnost** karta vám umožní nakonfigurovat trhy, které budou k dispozici v tomto plánu požadované finanční zhodnocení modelu, ceny a fakturační období. Kromě toho můžete určit, zda má být plán viditelné pro všechny uživatele nebo pouze pro konkrétní zákazníky (privátní cílové skupiny).

#### <a name="markets"></a>Trhy

- **Upravit trhy** (volitelné)

Každý plán musí být k dispozici aspoň jeden na trhu. Zaškrtněte políčko pro libovolného místa na trhu, kde chcete zpřístupnit tento plán. Vyhledávací pole a tlačítko pro výběr "Daně uhrazené" zemí, ve kterých Microsoft vrátí prodeje a použití daně za vás, jsou pro lepší. 


Pokud jste už nastavili cen pro váš plán ve Spojených státech amerických dolarů (USD) a přidejte na trhu jinam, bude cena za nový trh vypočítává podle aktuální kurzů. Vždy byste měli zkontrolovat ceny pro každý trh před publikováním. Pomocí odkazu "Export ceny (xlsx)" po uložení změn můžete zkontrolovat ceny.

#### <a name="pricing"></a>Ceny

- **Cenový model**: Paušální sazba. nezáleží nebo licenci na základě

**Paušální sazba.** Povolení přístupu k vaší nabídky s cenou paušální sazba. nezáleží jednu cenu měsíční nebo roční. To se někdy označuje jako ceny založené na webu.

**Licenci na základě:** Povolení přístupu k vaší nabídky s cenami na základě počtu uživatelů přístup k nabídky nebo zabírá *licence*. Tento model založené na pracovní stanici vám umožní nastavit minimální a maximální počtu licencí povolených na základě ceny. Tímto způsobem lze nakonfigurovat různých cenových bodech, podle počtu uživatelů tím, že nakonfigurujete více plánů.  Tato pole jsou volitelná. Pokud pole ponecháte prázdné, bude tak, že nemusí omezení (min 1) a rozdílu mezi maximálním tolik, může systém podporovat interpretovat počet licencí. Tato pole lze upravit jako součást aktualizace pro váš plán.

Po publikování se nedá změnit fakturační cenový model podle výběru. Kromě toho všechny plány pro stejnou nabídku musejí sdílet stejnou cenový model.

- **Fakturační období**: Měsíční nebo roční

Vyberte frekvenci, kterou zákazníci musí platit ceny uvedené. Musí být zadaná aspoň jeden měsíční nebo roční cena, nebo obě možnosti mohou být k dispozici zákazníkům.

- **Cena**: USD za měsíc nebo USD za rok

Ceny v místní měně set (USD = americký dolar) jsou převedeny do místní měny všechny vybrané trhy aktuální podle sazeb exchange, které jsou k dispozici během instalace. Tyto ceny před publikováním ověřte tak, že vyexportujete ceny tabulky a kontrola cena v trhy. Pokud chcete nastavit vlastní ceny jednotlivých na trhu, úpravě a importu cenovou tabulky. Zodpovídají za ověření, ceny a vlastní nastavení.
**Musíte nejprve uložit změny cen Povolit export data o cenách.*

Zkontrolujte ceny pečlivě před publikováním, jsou některá omezení, co můžete změnit plán po publikování:

- Po publikování plánu nelze změnit cenový model.
- Po fakturačního období je publikování pro plán, nemůžete později odebral.
- Po publikování cenu na trhu v plánu není možné později změnit.

### <a name="plan-audience"></a>Plánování cílové skupiny

Máte možnost konfigurovat každý plán viditelný pro všechny uživatele nebo pouze konkrétní cílové skupiny podle svého výběru. Můžete přiřadit členství v této cílové skupiny s omezeným přístupem, pomocí ID tenantů Azure AD.

#### <a name="privacy"></a>Soukromí

- **Toto je plán soukromý** (volitelné zaškrtávací políčko)

Zaškrtněte toto políčko, aby váš plán soukromý a viditelné jenom pro omezený cílové skupiny podle svého výběru. Po publikování jako privátní plánu, můžete aktualizace cílové skupiny nebo zvolit, jestli plán k dispozici všem uživatelům. Jakmile plán byl publikován jako viditelným pro všechny uživatele, musí zůstat viditelné všem uživatelům. (Tento plán nelze konfigurovat jako privátní plán znovu).

- **Cílové skupiny s omezeným přístupem (ID Tenanta)**

Přiřaďte cílové skupiny, který bude mít přístup k tomuto privátní plánu. Je přiřazen přístup pomocí ID tenantů s možností Zahrnout popis každé ID tenanta, které jsou přiřazeny. Je možné přidat maximálně 10 tenant ID nebo 20 000 zákazníků ID tenanta, pokud naimportováním souboru CSV.

Tenant je reprezentace organizace s ID reprezentována jako identifikátor GUID (globálně jedinečný identifikátor, 128-bit integer číslo používané pro identifikaci prostředků,). Jedná se o vyhrazenou instanci Azure AD, kterou organizace nebo vývojář aplikací obdrží při vytvoření partnerství s Microsoftem, například registrací do Azure, Microsoft Intune nebo Microsoft 365. Každý klient Azure AD se odlišuje a je oddělený od ostatních klientů Azure AD. Ke kontrole klienta, přihlaste se k webu Azure portal pomocí účtu, který chcete použít ke správě vaší aplikace. Pokud tenanta máte, budete k němu automaticky přihlášeni a název tenanta se zobrazí přímo pod názvem vašeho účtu. Najeďte myší na název vašeho účtu v pravém horním rohu webu Azure Portal, zobrazí se vaše jméno, e-mail, ID adresáře nebo tenanta (GUID) a vaše doména. Pokud je k vašemu účtu přidruženo více tenantů, můžete výběrem názvu vašeho účtu otevřít nabídku, ve které můžete mezi jednotlivými tenanty přepínat. Každý tenant má vlastní ID tenanta. Můžete také vyhledat pomocí adresy URL názvu domény na ID tenanta vaší organizace: [ https://www.whatismytenantid.com ](https://www.whatismytenantid.com).

Nabídky SaaS používají ID tenantů k definování privátní cílové skupiny, ale ostatní typy nabídek použít ID předplatného Azure, (které jsou také reprezentované jako identifikátory GUID).

> [!NOTE]
> Privátní cílové skupiny (nebo cílové skupiny s omezeným přístupem) se liší od cílové skupiny ve verzi preview. V **[ve verzi Preview](#preview)** kartu, můžete definovat cílovou skupinou ve verzi preview. Cílová skupina ve verzi preview je povolen přístup k vaší nabídce *předchozí* nabídku zveřejněná v za provozu na webu Marketplace. Při označení privátní cílovou skupinu platí jenom pro konkrétní plán, cílová skupina ve verzi preview můžete zobrazit všechny plány (privátní nebo ne), ale pouze během období ve verzi limited preview, zatímco plán je otestovali a ověřili.


## <a name="test-drive"></a>Testovací verze

**Testovací verze** karta vám umožní nastavit si ukázku (nebo "testovací verze") který umožní zákazníkům vyzkoušet vaši nabídku před potvrzením ji zakoupit. Další informace najdete v článku [co je to Test Drive?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive). Pokud už nechcete si testovací jízdu zadat pro vaši nabídku, vraťte se ke **[nabízejí nastavení](#offer-setup)** stránce a zrušte zaškrtnutí políčka **povolení testu jednotky**.

### <a name="technical-configuration"></a>Technické konfigurace
K dispozici jsou následující typy zkoušky každého s vlastní požadavky na konfiguraci technické.

- [Azure Resource Manager](#technical-configuration-for-azure-resource-manager-test-drive)
- [Dynamics 365](#technical-configuration-for-dynamics-365-test-drive)
- [Aplikace logiky](#technical-configuration-for-logic-app-test-drive)
- [Power BI](#technical-configuration-not-required-for-power-bi-test-drives) (technické konfigurace není požadováno)

#### <a name="technical-configuration-for-azure-resource-manager-test-drive"></a>Vyzkoušejte si technickou konfiguraci pro Azure Resource Manageru

Nasazení šablony, která obsahuje všechny prostředky Azure, které tvoří vašeho řešení. Produkty, které patří tento scénář používat jenom prostředky Azure. Další informace o nastavení [Azure Resource Manageru testovací verze](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive).

- **Oblasti** (povinné): Aktuálně jsou dostupné ve kterém může být vaše testovací verze k dispozici 26 oblastech podporovaných systémem Azure. Obvykle budete chtít zpřístupnit vaše testovací verze v oblastech, kde očekáváte, že největší počet zákazníků, takže mohou vybrat nejbližší oblasti pro zajištění nejlepšího výkonu. Musíte zajistit, že vaše předplatné může nasadit všechny prostředky potřebné ve všech oblastech, které vyberete.

- **Instance**: Vyberte typ (horká nebo studených) a počet instancí k dispozici, vynásobený číslem oblastech, kde je k dispozici vaší nabídky.

**Horké**: Tento typ instance je nasazen a čeká se na přístup za vybrané oblasti. Zákazníci mohou okamžitě přístup k *Hot* instancí si testovací jízdu, namísto nutnosti čekat nasazení. Výměnou za to je, že tyto instance nepřetržitý provoz na vaše předplatné Azure, takže bude mít za následek větší dostupnost nákladů. Důrazně doporučujeme mít alespoň jednu *Hot* instancí, jak je většina zákazníků nechtějí čekat pro úplné nasazení, výsledkem odkládací v využití ze strany zákazníků, pokud žádné *Hot* instance je k dispozici.

**Studená**: Tento typ instance představuje celkový počet instancí, které je možné nasadit v jedné oblasti. Studená instance vyžadují celý Test jednotky Resource Manageru šablony nasazení, když zákazník požádá o testovací verze, tak *studenou* instance jsou mnohem pomalejší než načíst *Hot* instancí. Výměnou za to je, že budete muset platit po dobu trvání testu jednotky, je *není* vždy spuštěné v předplatném Azure jako pomocí *Hot* instance.

- **Šablony Azure Resource Manageru test jednotky**: Nahrajte .zip, který obsahuje šablony Azure Resource Manageru.  Další informace o vytváření šablony Azure Resource Manageru v tomto článku rychlý Start [vytvoření a nasazení šablon Azure Resource Manageru pomocí webu Azure portal](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal).

- **Doba trvání test drivu** (povinné): Zadejte dobu, která zůstane aktivní, počet hodin na testovací verzi. Po skončení tohoto období se automaticky ukončí na testovací verzi. V této hodnotě duration může pouze tipu sady podle celočíselný počet hodin (např.) hodin "2", "1,5" je neplatný).

#### <a name="technical-configuration-for-dynamics-365-test-drive"></a>Vyzkoušejte si technickou konfiguraci pro Dynamics 365

Microsoft může zbavte se složité nastavování si testovací jízdu hostování a Správa služby zřizování a nasazování pomocí tohoto typu testovací verze. Konfigurace pro tento typ hostované testovací verze je stejný bez ohledu na to, zda testovací verze je cílen na verzi Business Central Customer Engagement operace nebo cílové skupiny.

- **Maximální počet souběžných testovací verze** (povinné): Nastavení maximálního počtu zákazníků, které můžou používat vaše testovací verze najednou. Každý souběžných uživatelských spotřebuje licenci Dynamics 365, testovací verze je aktivní, takže budete muset zajistit, že máte dostatek licencí, které jsou k dispozici pro podporu maximální nastavený limit. Doporučená hodnota 3 – 5.

- **Doba trvání test drivu** (povinné): Zadejte dobu, kterou Test Drive zůstane aktivní, tak, že definujete počet hodin. Po tomto počtu hodin bude relace ukončit a už nebude používat jednu licenci. Doporučujeme, abyste hodnotu 2 až 24 hodin v závislosti na složitosti vaší nabídky. V této hodnotě duration může pouze tipu sady podle celočíselný počet hodin (např.) hodin "2", "1,5" je neplatný).  Uživatel může požádat o novou relaci, dojde k jejich spuštění mimo časový limit a chtějí mít přístup k testovací verze.

- **Adresa URL instance** (povinné): Adresa URL, kde se zákazník začne jejich testovací verze. Obvykle adresu URL vaší instance Dynamics 365, používající vaši aplikaci s ukázkovými daty nainstalovaná (třeba https://testdrive.crm.dynamics.com).

- **Instance adresy URL webového rozhraní API** (povinné): Načíst přihlášením do svého účtu Microsoft 365 a přejdete na adresu URL webového rozhraní API pro vaši instanci Dynamics 365 **nastavení** \&gt; **Přizpůsobení** \&gt; **Materiály pro vývojáře** \&gt; **Instance webové rozhraní API (kořenovou adresu URL)**, zkopírujte adresu URL najdete tady (třeba https://testdrive.crm.dynamics.com/api/data/v9.0).

- **Název role** (povinné): Zadejte název role zabezpečení, které jste definovali ve vaší vlastní testovací verze Dynamics 365. Tím se přiřadí uživateli při jejich testovací verze (například test-drive role).

#### <a name="technical-configuration-for-logic-app-test-drive"></a>Technické konfigurace pro testovací verze aplikace logiky

Jakékoli vlastní produkty používejte tento typ testu jednotky nasazení šablony, která zahrnuje celou řadu architektur komplexní řešení. Další informace o nastavení aplikace logiky testů jednotek, navštivte [operace](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) a [Customer Engagement](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app) na Githubu.

- **Oblast** (povinné, jedním výběrem rozevírací seznam): Aktuálně jsou dostupné ve kterém může být vaše testovací verze k dispozici 26 oblastech podporovaných systémem Azure. Prostředky pro vaše aplikace logiky se nasadí v oblasti, kterou vyberete. Pokud vaše aplikace logiky má všechny vlastní prostředky uložené v konkrétní oblasti, ujistěte se, že se tady vyberete danou oblast. Nejlepší způsob, jak to provést, je plně nasazení vaší aplikace logiky místně v rámci předplatného Azure na portálu a ověřte, že funguje správně před provedením tohoto výběru.

- **Maximální počet souběžných testovací verze** (povinné): Nastavení maximálního počtu zákazníků, které můžou používat vaše testovací verze najednou. Tyto testovací jednotky už nasazená, takže se zákazníci můžou pro okamžitě přístup k nim bez čekání na nasazení.

- **Doba trvání test drivu** (povinné): Zadejte dobu, která zůstane aktivní, počet hodin na testovací verzi. Po skončení tohoto období se automaticky ukončí na testovací verzi.

- **Název skupiny prostředků Azure** (povinné): Zadejte [skupiny prostředků Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) název, kde je uložena vaše testovací verze aplikace logiky.

- **Název aplikace logiky Azure** (povinné): Zadejte název aplikace logiky, která uživateli přiřadí na testovací verzi. Tato aplikace logiky musí být uložen ve skupině prostředků Azure výše.

- **Název aplikace logiky pro zrušení zřízení** (povinné): Zadejte název aplikace logiky, která po dokončení zákazník deprovisions testovací verze. Tato aplikace logiky musí být uložen ve skupině prostředků Azure výše.

#### <a name="technical-configuration-not-required-for-power-bi-test-drives"></a>Technické konfigurace není vyžadováno pro testovací verze Power BI

Produkty, které chcete prokázat, že že interaktivní vizuálu Power BI můžete použít vložený odkaz ke sdílení řídicího panelu vlastními silami sestavených jako jejich testovací verze, žádné další technické nezbytné konfigurace. Další informace o nastavení[Power BI](https://docs.microsoft.com/power-bi/service-template-apps-overview) šablony aplikací.

### <a name="deployment-subscription-details"></a>Podrobnosti o předplatném nasazení

Pokud chcete nasadit na testovací verzi vaším jménem, vytvoření a zajištění samostatné jedinečné předplatným Azure. (Není vyžadované pro testovací verze Power BI).

- **ID předplatného Azure** (povinné pro Azure Resource Manageru a Logic apps): Zadejte ID předplatného pro udělení přístupu k vašim službám účtu Azure pro využití prostředků, vytváření sestav a fakturaci. Doporučujeme vám zvážit [vytvoření samostatné předplatné Azure](https://docs.microsoft.com/azure/billing/billing-create-subscription) má použít pro testovací verze, pokud již nemáte. ID vašeho předplatného Azure najdete po přihlášení k [webu Azure portal](https://portal.azure.com/) a přejdete do **předplatná** kartu v levé nabídce. Výběrem karty zobrazí ID vašeho předplatného (třeba "a83645ac-1234-5ab6-6789-1h234g764ghty").

- **ID tenanta Azure AD** (povinné): Zadejte vaše Azure Active Directory (AD) [ID tenanta](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-tenant-id). Toto ID najít, přihlaste se k [webu Azure portal](https://portal.azure.com/), v nabídce vlevo vyberte kartu Active Directory, vyberte **vlastnosti** , potom se podívejte **ID adresáře** uvedené číslo (např.) 50c464d3-4930-494c-963c-1e951d15360e). Můžete také vyhledat pomocí váš název domény adresy URL v ID tenanta vaší organizace: [ https://www.whatismytenantid.com ](https://www.whatismytenantid.com).

- **Název tenanta Azure AD** (vyžadováno pro Dynamics 365): Zadejte název vaší služby Azure Active Directory (AD). Zjištění názvu, přihlaste se k [webu Azure portal](https://portal.azure.com/), v pravém horním rohu se zobrazí název tenanta pod názvem vašeho účtu.

- **ID aplikace Azure AD** (povinné): Zadejte vaše Azure Active Directory (AD) [ID aplikace](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-application-id-and-authentication-key). Toto ID najít, přihlaste se k [webu Azure portal](https://portal.azure.com/), v nabídce vlevo vyberte kartu Active Directory, vyberte **registrace aplikací**, potom se podívejte **ID aplikace** číslo uvedené (třeba 50c464d3-4930-494c-963c-1e951d15360e).

- **Klíč aplikace Azure AD** (povinné): Zadejte vaše Azure Active Directory (AD) [klíč aplikace](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-application-id-and-authentication-key). Toto ID najít, přihlaste se k [webu Azure portal](https://portal.azure.com/), v nabídce vlevo vyberte kartu Active Directory, vyberte **registrace aplikací** a pak vyberte **nastavení**  >  **Klíče**.

Nezapomeňte **Uložit** přejde k další části!

### <a name="test-drive-listings-optional"></a>Test jednotky výpisy (volitelné)

**Test Drive výpisy** možnost nalezené pod **testovací verze** karta zobrazuje jazyků (a trhy), kde je k dispozici, vaše testovací verze Angličtina (Spojené státy) je aktuálně k dispozici pouze umístění . Kromě toho tato stránka zobrazuje stav výpis specifické pro jazyk a datum a čas, který byl přidán. Je potřeba zadat podrobnosti testu jednotky (popis, uživatelská příručka, videa atd.) pro každý jazyk/trh.

- **Popis** (povinné): Popisují vaše testovací verze, co se prokáže, cílů pro uživatele můžete experimentovat, funkce, které chcete prozkoumat a všechny příslušné informace umožňující uživateli určit, jestli se má získat vaší nabídky. V tomto poli je třeba zadat maximálně 3 000 znaků textu. 

- **Přístup k informacím o** (požadováno pro Azure Resource Manageru a logika testování jednotek): Vysvětluje, co zákazník potřebuje vědět, aby bylo možné přistupovat a používat tato testovací verze. Provede scénáře použití vaší nabídky a přesně toho, co zákazník musí znáte pro přístup k funkcím v průběhu testu jednotky. Maximálně 10 000 znaků textu lze zadat v tomto poli.

- **Uživatelská příručka** (povinné): Podrobný návod, prostředí test jednotky. Uživatelská příručka zahrnovat představ zákazník získat nepocítí testovací verze a slouží jako reference pro nějaké dotazy, které můžou mít. Soubor musí být ve formátu PDF a po nahrání jmenovat (max. 255 znaků).

- **Videa: Přidávání videí** (volitelné): Videa můžete uložit na YouTube nebo Vimeo a odkazujete pomocí odkazu a miniaturu obrázku (533 x 324 pixelů), tak, aby zákazník může zobrazit provede informace pro lepší pochopení testovací verze, včetně postupu pro úspěšné fungování funkce vaše nabídky a scénáře, které jejich výhody.
  - **Název** (povinné)
  - **Adresa URL (YouTube nebo Vimeo pouze)** (povinné)
  - **Miniatura (533 x 324px)**: Soubor bitové kopie musí být ve formátu PNG.

Vyberte **Uložit** po dokončení těchto polí.

## <a name="publish"></a>Publikovat

#### <a name="submit-offer-to-preview"></a>Odeslat nabídnout ve verzi preview

Až dokončíte všechny požadované části nabídky, vyberte **publikovat** v pravém horním rohu portálu. Bude znovu orientovaného na **revize a publikovat** stránky. 

Pokud je to poprvé publikování v rámci této nabídky, můžete:

- Zobrazení stavu dokončení pro každou část nabídky.
    - *Nezahájeno* – znamená, že se některé části a je potřeba dokončit.
    - *Neúplné* – znamená, že v části obsahuje chyby, které je potřeba opravit nebo vyžaduje další údaje. Vraťte se oddíly a aktualizujte ji.
    - *Kompletní* – znamená, že v části hotový, poskytl všechna požadovaná data a nejsou žádné chyby. Všechny části nabídky musí být ve stavu dokončení, před odesláním nabídky.
- Poskytují pokyny testovací tým certifikace zajistit, že vaše aplikace je testována správně, kromě jakékoli dodatečné poznámky, které jsou užitečné k pochopení vaší aplikace.
- Odešlete nabídku k publikování tak, že vyberete **odeslat**. Pošleme vám e-mail s oznámením při předběžnou verzi nabídky je k dispozici si můžete zkontrolovat a schválit. Musíte se vrátit do partnerského centra a vyberte **pro uvedení do provozu** pro nabídku k publikování vaší nabídky na veřejnosti (nebo pokud soukromé nabídky privátní cílové skupině).

## <a name="next-steps"></a>Další postup

- [Aktualizace stávající nabídky na webu Marketplace obchodní](./update-existing-offer.md)
