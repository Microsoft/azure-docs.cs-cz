---
title: Vytvoření nové nabídky spravovaných služeb na komerčním trhu
description: Jak vytvořit novou nabídku spravované služby pro zápis na Azure Marketplace pomocí portálu Commercial Marketplace v Centru partnerů.
author: JnHs
ms.author: jenhayes
manager: evansma
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.openlocfilehash: 3d7015d5e1c3b67b271e89b5653f56415bfb722e
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80674199"
---
# <a name="create-a-new-managed-service-offer"></a>Vytvoření nové nabídky spravované služby

Spravovaná služba nabízí pomoc s povolením scénářů [Azure Lighthouse.](../../lighthouse/overview.md) Když zákazník přijme nabídku spravované služby, pak je možné k onboard prostředky pro [azure delegovanou správu prostředků](../../lighthouse/concepts/azure-delegated-resource-management.md).

Chcete-li začít vytvářet nabídky spravovaných služeb, nejprve [vytvořte účet Centra partnerů](./create-account.md) a otevřete [řídicí panel Komerční tržiště](https://partner.microsoft.com/dashboard/commercial-marketplace/offers)s vybranou stránkou **Přehled.** K publikování nabídky spravované služby musíte mít [úroveň kompetence silver nebo gold cloudové platformy](https://partner.microsoft.com/membership/cloud-platform-competency) nebo být [msp Azure Expert MSP.](https://partner.microsoft.com/membership/azure-expert-msp)

![Řídicí panel Komerční tržiště v Partnerském centru](./media/new-offer-overview.png)

>[!Note]
> Po zveřejnění nabídky budou úpravy nabídky provedené v Partnerském centru aktualizovány pouze v systému a na frontách obchodů po opětovném publikování. Po provádění změn se ujistěte, že odešlete nabídku ke zveřejnění.

## <a name="create-a-new-offer"></a>Vytvoření nové nabídky

Vyberte tlačítko **+ Nová nabídka** a pak vyberte položku nabídky Spravované **služby.** Zobrazí se dialogové okno **Nová nabídka.**

### <a name="offer-id-and-alias"></a>ID nabídky a alias

- **ID nabídky**: Jedinečný identifikátor pro každou nabídku ve vašem účtu. Toto ID bude viditelné zákazníkům v adrese URL nabídky marketplace. Toto ID může obsahovat pouze malé alfanumerické znaky (včetně spojovníků a podtržítk, ale bez mezer), omezeno na 50 znaků a po výběru **možnosti Vytvořit**jej nelze změnit.  Pokud zde například zadáte *test-offer-1,* bude `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`adresa URL nabídky .

- **Alias nabídky**: Název používaný k odkazování na nabídku v rámci Partnerského centra. Tento název nebude na trhu použit a liší se od názvu nabídky a dalších hodnot, které se zobrazí zákazníkům. Tuto hodnotu nelze změnit po výběru **možnosti Vytvořit**.

Po zadání **id nabídky** a **aliasu Nabídky**vyberte **Vytvořit**. Poté budete moci pracovat na všech různých částech vaší nabídky.

## <a name="offer-setup"></a>Nastavení nabídky

Stránka **Nastavení nabídky** vás požádá o následující informace. Nezapomeňte po dokončení těchto polí vybrat **uložit.**

## <a name="connect-lead-management"></a>Připojení správy potenciálních zákazníků

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

Všimněte si, že podle [certifikačních zásad spravovaných služeb](https://docs.microsoft.com/legal/marketplace/certification-policies#700-managed-services)je vyžadován **cíl zájemce.** Tím se vytvoří záznam ve vašem crm systému pokaždé, když zákazník nasadí vaši nabídku.

Další informace naleznete v tématu [Přehled správy potenciálních zákazníků](./commercial-marketplace-get-customer-leads.md).

Nezapomeňte **stránku uložit,** než přejdete k dalšímu oddílu.

## <a name="properties"></a>Vlastnosti

Stránka **Vlastnosti** umožňuje definovat kategorie použité k seskupení nabídky na trhu a právní smlouvy podporující vaši nabídku. Po dokončení této stránky vyberte **Uložit.**

### <a name="category"></a>Kategorie

Vyberte minimálně jednu a maximálně pět kategorií, které budou použity k umístění vaší nabídky do příslušných oblastí vyhledávání na trhu. Nezapomeňte v popisu nabídky zjistit, jak vaše nabídka tyto kategorie podporuje.

### <a name="terms-and-conditions"></a>Podmínky a ujednání

Uveďte své vlastní právní podmínky v oblasti **smluvních podmínek.** Můžete také zadat adresu URL, kde lze najít vaše smluvní podmínky. Zákazníci budou muset přijmout tyto podmínky, než budou moci vyzkoušet vaši nabídku.

## <a name="offer-listing"></a>Nabídka výpis

Stránka **Nabídka umožňuje** definovat podrobnosti o tržišti (název nabídky, popis, obrázky atd.) pro vaši nabídku.

> [!NOTE]
> Nabídka výpis obsah (například popis, dokumenty, screenshoty, podmínky použití, atd.) nemusí být v angličtině, pokud popis nabídky začíná frází, "Tato aplikace je k dispozici pouze v [non-anglický jazyk]." Je také přijatelné poskytnout *užitečnou adresu URL odkazu* pro nabízení obsahu v jiném jazyce, než který byl použit v obsahu nabídky.

### <a name="name"></a>Name (Název)

Jméno, které zde zadáte, se zákazníkům zobrazí jako název nabídky. Toto pole je předem vyplněno textem, který jste zadali pro **alias Nabídky** při vytváření nabídky, ale tuto hodnotu můžete změnit. Tento název může být chráněn ochrannou známkou (a můžete uvést ochranné známky nebo symboly autorských práv). Název nesmí mít více než 50 znaků a nesmí obsahovat žádné emodži.

### <a name="search-results-summary"></a>Souhrn výsledků hledání

Uveďte krátký popis nabídky (až 100 znaků), který lze použít ve výsledcích vyhledávání na marketplace.

### <a name="long-summary"></a>Dlouhý souhrn

Poskytněte delší popis nabídky (až 256 znaků). Tento dlouhý souhrn lze také použít ve výsledcích vyhledávání na marketplace.

### <a name="description"></a>Popis

Poskytněte delší popis nabídky (až 3 000 znaků). Tento popis se zobrazí zákazníkům v přehledu výpisu marketplace. Uveďte návrh hodnoty vaší nabídky, klíčové výhody, přidružení kategorií a/nebo odvětví, příležitosti k nákupu v aplikaci a všechny požadované informace.

Několik tipů pro psaní popisu:  

- V prvních několika větách popisu jasně popište hodnotu nabídky. Do návrhu hodnoty zahrňte následující položky:
  - Popis nabídky
  - Typ uživatele, který využívá nabídky
  - Potřeby nebo bolest zákazníka, kterou nabídka řeší
- Mějte na paměti, že prvních několik vět může být zobrazeno ve výsledcích vyhledávače.  
- Při prodeji produktu se nespoléhejte na funkce a funkce. Místo toho se zaměřte na hodnotu, kterou dodáváte.  
- Používejte co nejvíce slovní zásobu nebo formulace založené na výhodách.

Chcete-li, aby byl popis nabídky poutavější, naformátujte popis pomocí editoru rtf.

![Použití editoru rtf](./media/text-editor2.png)

Pomocí editoru RTF použijte následující pokyny:

- Chcete-li změnit formát obsahu, zvýrazněte text, který chcete formátovat, a vyberte styl textu, jak je znázorněno níže:

     ![Změna formátu textu pomocí editoru RTF](./media/text-editor3.png)

- Chcete-li do textu přidat seznam s odrážkami nebo číslovaný seznam, použijte následující možnosti:

     ![Přidání seznamů pomocí editoru RTF](./media/text-editor4.png)

- Chcete-li k textu přidat nebo odebrat odsazení, použijte následující možnosti:

     ![Odsazení pomocí editoru rtf](./media/text-editor5.png)

### <a name="privacy-policy-link"></a>Odkaz na zásady ochrany osobních údajů

Zadejte adresu URL zásad ochrany osobních údajů vaší organizace (hostované na vašem webu). Jste zodpovědní za to, že vaše aplikace je v souladu se zákony a předpisy ochrany osobních údajů a za poskytnutí platných zásad ochrany osobních údajů.

### <a name="useful-links"></a>Užitečné odkazy

Poskytněte volitelné doplňkové online dokumenty o vašem řešení. Přidejte další užitečné odkazy kliknutím **na + Přidat odkaz**.

### <a name="contact-information"></a>Kontaktní informace

V této části je nutné zadat jméno, e-mail a telefonní číslo **kontaktu podpory** a **technického kontaktu**. Tyto informace se zákazníkům nezobrazují, ale budou k dispozici společnosti Microsoft a mohou být poskytnuty partnerům CSP.

### <a name="support-urls"></a>Adresy URL podpory

Pokud máte weby podpory pro **zákazníky Azure Global Customers** nebo **zákazníky Azure Government**, zadejte tyto adresy URL zde.

### <a name="marketplace-images"></a>Obrázky na marketplace

V této části můžete poskytnout loga a obrázky, které budou použity při zobrazování vaší nabídky zákazníkovi. Všechny obrázky musí být ve formátu PNG.

#### <a name="marketplace-logos"></a>Loga tržiště

Jsou vyžadovány čtyři velikosti loga: **Malé (40x40),** **Střední (90x90),** **Velké (115x115)** a **Široké (255x115)**. Pro loga postupujte podle těchto pokynů:

- Design Azure má jednoduchou paletu barev. Omezte počet primárních a sekundárních barev ve vašem logu.
- Barvami motivů na portálu jsou bílá a černá. Nepoužívejte tyto barvy jako barvu pozadí pro vaše logo. Použijte barvu, která vaše logo na portálu zvýrazní. Doporučujeme použít jednoduché primární barvy.
- Pokud použijete průhledné pozadí, ujistěte se, že text ani logo nejsou bílé, černé nebo modré.
- Logo by mělo působit plochým dojmem a nemělo by obsahovat barevné přechody. Nepoužívejte v logu pozadí s barevným přechodem.
- Na logo neumisťujte žádný text, a to ani název vaší společnosti nebo název značky.
- Zajistěte, aby logo nebylo roztažené.

#### <a name="screenshots"></a>Snímky obrazovek

Přidejte až pět snímků obrazovky, které ukazují, jak vaše nabídka funguje. Všechny snímky obrazovky musí mít rozměry 1280 x 720 pixelů.

#### <a name="videos"></a>Videa

Volitelně můžete přidat až pět videí, která ukazují vaši nabídku. Tato videa by měla být umístěna na YouTube a / nebo Vimeo. Pro každou z nich zadejte název videa, jeho adresu URL a miniaturu videa (1280 x 720 pixelů).

#### <a name="additional-marketplace-listing-resources"></a>Další zdroje informací o výpisu tržiště

- [Doporučené postupy pro nabídky na marketplace](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

## <a name="preview"></a>Preview

Než nabídku publikujete živě v širší nabídce marketplace, musíte ji nejprve zpřístupnit omezenému okruhu uživatelů ve verzi Preview. To vám umožní potvrdit, jak se nabídka zobrazí na Azure Marketplace před tím, než je zpřístupníte zákazníkům. Týmy podpory a inženýrských sítí společnosti Microsoft budou také moci zobrazit vaši nabídku během tohoto období náhledu.

Cílovou skupinu náhledů můžete definovat tak, že v části **Náhled okruhu uživatelů** zadáte ID předplatného Azure. Můžete zadat až 10 ID předplatného ručně nebo nahrát soubor .csv s až 100 ID předplatného.

Všichni zákazníci přidružení k těmto předplatným budou moct zobrazit nabídku na Azure Marketplace, než se začne žít. Nezapomeňte zde zahrnout vlastní předplatná, abyste si mohli zobrazit náhled nabídky.

## <a name="plan-overview"></a>Přehled plánu

Každá nabídka musí mít jeden nebo více plánů (někdy označované jako sku. Můžete přidat více plánů pro podporu různých sad funkcí za různé ceny nebo přizpůsobit konkrétní plán pro omezené publikum konkrétních zákazníků. Zákazníci mohou zobrazit plány, které jsou k dispozici v rámci nadřazené nabídky.

Na stránce **Přehled plánu** vyberte + Vytvořit **nový plán**. Poté zadejte **ID plánu** a **název plánu**. Obě tyto hodnoty mohou obsahovat pouze malé alfanumerické znaky, pomlčky a podtržítka s maximálně 50 znaky. Tyto hodnoty mohou být viditelné pro zákazníky a po publikování nabídky je nelze změnit.

Vyberte **Vytvořit,** jakmile zadáte tyto hodnoty, abyste mohli pokračovat v práci na plánu. K dokončení jsou k dispozici tři části: **výpis plánu**, Ceny **a dostupnost**a **Technická konfigurace**.

### <a name="plan-listing"></a>Výpis plánu

Nejprve zadejte **souhrn výsledků hledání** plánu. Toto je krátký popis vašeho plánu (až 100 znaků), který může být použit ve výsledcích vyhledávání na marketplace.

Dále zadejte **popis,** který poskytuje podrobnější vysvětlení plánu.

### <a name="pricing-and-availability"></a>Ceny a dostupnost

V současné době existuje pouze jeden cenový model, který lze použít pro nabídku spravované služby: **Přineste si vlastní licenci (BYOL)**. To znamená, že zákazníkům budete přímo účtovat náklady spojené s touto nabídkou a společnost Microsoft vám neúčtuje žádné poplatky.

Část **Přehled plánu** umožňuje určit, zda má být tento plán [soukromý](../../marketplace/private-offers.md). Pokud ponecháte políčko **Toto je soukromý plán** nezaškrtnuté, váš plán nebude omezen na konkrétní zákazníky (nebo na určitý počet zákazníků).

Chcete-li tento plán zpřístupnit pouze určitým zákazníkům, vyberte možnost **Ano**. Pokud tak učiníte, budete muset identifikovat zákazníky tím, že jim poskytnete ID předplatného. Ty lze zadat jeden po druhém (až pro 10 odběrů) nebo nahráním souboru .csv (maximálně 10 000 předplatných ve všech plánech). Nezapomeňte zde zahrnout vlastní předplatná, abyste mohli nabídku otestovat a ověřit.

> [!IMPORTANT]
> Jakmile je plán publikován jako veřejný, nelze jej změnit na soukromý. Chcete-li určit, kteří zákazníci mohou vaši nabídku přijmout a delegovat prostředky, použijte soukromý plán. S veřejným tarifem nemůžete omezit dostupnost na určité zákazníky nebo dokonce na určitý počet zákazníků (i když můžete plán zcela ukončit, pokud se tak rozhodnete). Přístup [k delegování](../../lighthouse/how-to/onboard-customer.md#remove-access-to-a-delegation) můžete odebrat poté, co zákazník přijme nabídku, pouze pokud jste při publikování nabídky zahrnuli **autorizaci** s **definicí role** nastavenou na [přiřazení registrace spravovaných služeb delete.](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) Můžete také kontaktovat zákazníka a požádat ho o [odebrání přístupu](../../lighthouse/how-to/view-manage-service-providers.md#add-or-remove-service-provider-offers).

### <a name="technical-configuration"></a>Technická konfigurace

Tato část plánu vytvoří manifest s informacemi o autorizaci pro správu zdrojů zákazníků. Tyto informace jsou nutné k povolení [správy delegovaných prostředků Azure](../../lighthouse/concepts/azure-delegated-resource-management.md).

Nezapomeňte zkontrolovat [klienty, role a uživatele ve scénářích Azure Lighthouse, abyste pochopili,](../../lighthouse/concepts/tenants-users-roles.md#best-practices-for-defining-users-and-roles) které role jsou podporované a doporučené postupy pro definování autorizací.

> [!NOTE]
> Jak je uvedeno výše, uživatelé a role v položkách **autorizace** se budou vztahovat na každého zákazníka, který si plán zakoupí. Pokud chcete omezit přístup na konkrétního zákazníka, budete muset publikovat soukromý plán pro jeho výhradní použití.

#### <a name="manifest"></a>Manifest

Nejprve zadejte **version** pro manifest. Použijte formát *n.n.n* (například 1.2.5).

Dále zadejte **ID klienta**. Toto je identifikátor GUID přidružený k ID klienta Azure Active Directory (Azure AD) vaší organizace; to znamená, že správu tenanta, ze kterého budete mít přístup k prostředkům vašich zákazníků. Pokud to nemáte po ruce, najdete ho tak, že najedete na název účtu na pravé horní straně portálu Azure nebo vyberete **switch adresáře**.

Pokud publikujete novou verzi nabídky a potřebujete vytvořit aktualizovaný manifest, vyberte **možnost + Nový manifest**. Nezapomeňte zvýšit číslo verze z předchozí verze manifestu.

#### <a name="authorization"></a>Autorizace

Autorizace definují entity ve vašem správě tenanta, kteří mají přístup k prostředkům a předplatným pro zákazníky, kteří si zakoupí plán. Každé z těchto entit je přiřazena předdefinovaná role, která uděluje určité úrovně přístupu.

Pro každý plán můžete vytvořit až dvacet autorizací.

> [!TIP]
> Ve většině případů budete chtít přiřadit role skupině uživatelů Azure AD nebo instančnímu objektu služby Azure AD, nikoli řadě jednotlivých uživatelských účtů. To umožňuje přidat nebo odebrat přístup pro jednotlivé uživatele, aniž by bylo nutné aktualizovat a znovu publikovat plán při změně požadavků na přístup. Při přiřazování rolí skupinám Azure AD [se ujistěte, že **typ skupiny** je **Zabezpečení** a ne **Office 365**](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). Další doporučení najdete [v tématu tenanty, role a uživatele ve scénářích Azure Lighthouse](../../lighthouse/concepts/tenants-users-roles.md).

Pro každou **autorizaci**budete muset zadat následující. Potom můžete vybrat **+ Přidat autorizaci** tolikrát, kolikrát je potřeba přidat další uživatele a definice rolí.

- **ID objektu Azure AD**: Identifikátor Azure AD uživatele, skupiny uživatelů nebo aplikace, které budou udělena určitá oprávnění (podle definice role) k prostředkům vašich zákazníků.
- **Zobrazovaný název objektu Azure AD**: Popisný název, který zákazníkovi pomůže pochopit účel této autorizace. Zákazník uvidí tento název při delegování prostředků.
- **Definice role**: Vyberte jednu z dostupných předdefinovaných rolí Azure AD ze seznamu. Tato role určí oprávnění, která uživatel v poli **ID objektu Azure AD** bude mít na prostředky vašich zákazníků. Popis těchto rolí najdete [v tématu předdefinované role](../../role-based-access-control/built-in-roles.md) a podpora rolí pro [správu delegovaných prostředků Azure](../../lighthouse/concepts/tenants-users-roles.md#role-support-for-azure-delegated-resource-management).
  > [!NOTE]
  > Jako příslušné nové předdefinované role jsou přidány do Azure, budou k dispozici zde, i když může dojít k určité zpoždění, než se zobrazí.
- **Přiřaditelné role**: Tato možnost se zobrazí pouze v případě, že jste v **definici role** pro tuto autorizaci vybrali správce přístupu uživatelů. Pokud ano, musíte zde přidat jednu nebo více přiřaditelných rolí. Uživatel v poli **ID objektu Azure AD** bude moct přiřadit tyto role [spravovaným identitám](../../active-directory/managed-identities-azure-resources/overview.md), které jsou nutné k [nasazení zásad, které lze napravit](../../lighthouse/how-to/deploy-policy-remediation.md). Všimněte si, že pro tohoto uživatele nebudou platit žádná další oprávnění, která jsou obvykle přidružena k roli Správce přístupu uživatelů.

> [!TIP]
> Chcete-li v případě potřeby [odebrat přístup k delegování,](../../lighthouse/how-to/onboard-customer.md#remove-access-to-a-delegation) zahrňte **autorizaci** s **definicí role** nastavenou na [roli odstranění přiřazení registrace spravovaných služeb](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role). Pokud tato role není přiřazena, delegované prostředky mohou být odebrány pouze uživatelem v tenantovi zákazníka.

Po dokončení všech oddílů pro váš plán můžete vybrat **+ Vytvořit nový plán** tolikrát, kolikrát potřebujete k vytvoření dalších plánů. Jakmile budete mít hotovo, vyberte **Uložit**.

## <a name="publish"></a>Publikování

### <a name="submit-offer-to-preview"></a>Odeslat nabídku do náhledu

Po dokončení všech požadovaných částí nabídky vyberte **publikovat** v pravém horním rohu portálu. Budete přesměrováni na stránku **Revize a publikování.**

Pokud tuto nabídku publikujete poprvé, můžete:

- Podívejte se na stav dokončení pro každou část nabídky.
  - *Nezahájeno* - znamená, že sekce nebyla dotčena a musí být dokončena.
  - *Neúplné* - znamená, že sekce obsahuje chyby, které je třeba opravit nebo vyžadují poskytnutí dalších informací. Vraťte se do oddílu (oddílů) a aktualizujte ji.
  - *Dokončeno* - znamená, že sekce je kompletní, všechny požadované údaje byly poskytnuty a nejsou žádné chyby. Všechny části nabídky musí být před odesláním nabídky v úplném stavu.
- V části **Poznámky k certifikaci** poskytněte certifikačnímu týmu pokyny k testování, abyste zajistili, že vaše aplikace bude správně testována, a navíc ke všem doplňkovým poznámkám užitečným pro pochopení vaší aplikace.
- Odešlete nabídku k publikování výběrem **možnosti Odeslat**. Pošleme vám e-mail, když je k dispozici předběžná verze nabídky, kterou můžete zkontrolovat a schválit. Vraťte se do Partnerského centra a vyberte **Go-live** pro nabídku, abyste zveřejnili svou nabídku veřejnosti (nebo pokud soukromou nabídku, soukromému publiku).

### <a name="customer-experience-and-offer-management"></a>Zákaznická zkušenost a správa nabídek

Když zákazník nasadí vaši nabídku, bude moct delegovat předplatná nebo skupiny prostředků pro [správu delegovaných prostředků Azure](../../lighthouse/concepts/azure-delegated-resource-management.md). Další informace o tomto procesu naleznete [v tématu Proces registrace zákazníků](../../lighthouse/how-to/publish-managed-services-offers.md#the-customer-onboarding-process).

[Aktualizovanou verzi nabídky](update-existing-offer.md) můžete kdykoli publikovat. Můžete například přidat novou definici role do dříve publikované nabídky. Když tak učiníte, zákazníci, kteří už přidali nabídku, uvidí na stránce [**Poskytovatelé služeb**](../../lighthouse/how-to/view-manage-service-providers.md) na webu Azure Portal ikonu, která jim dá vědět, že je k dispozici aktualizace. Každý zákazník bude moci zkontrolovat změny a rozhodnout, zda chce aktualizovat na novou verzi.

## <a name="next-steps"></a>Další kroky

- [Aktualizace stávající nabídky na komerčním marketplace](./update-existing-offer.md)
- [Další informace o azure lighthouse](../../lighthouse/overview.md)