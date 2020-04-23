---
title: Vytvoření nové nabídky SaaS pro komerční tržiště Microsoftu
description: Jak vytvořit novou nabídku Software as a Service (SaaS) pro zápis nebo prodej v Microsoft AppSource, Azure Marketplace nebo prostřednictvím programu Zprostředkovatel cloudových řešení (CSP) pomocí programu Microsoft commercial marketplace v Centru microsoftu Partner.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/13/2020
ms.openlocfilehash: a39e1e19f65722b4b5ae809ca943da719a3c6e22
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869772"
---
# <a name="create-a-new-saas-offer"></a>Vytvoření nové nabídky SaaS

Chcete-li začít vytvářet nabídky Softwaru jako služby (SaaS), ujistěte se, že nejprve [vytvoříte účet Centra partnerů](./create-account.md) a otevřete [řídicí panel Komerční tržiště](https://partner.microsoft.com/dashboard/commercial-marketplace/offers)s vybranou kartou **Přehled.**

![Řídicí panel Komerční tržiště v Partnerském centru](./media/new-offer-overview.png)

>[!Note]
> Po zveřejnění nabídky budou úpravy nabídky provedené v Partnerském centru aktualizovány pouze v systému a na frontách obchodů po opětovném publikování. Po provádění změn nezapomeňte odeslat nabídku ke zveřejnění.

Vyberte nabídku + **Nová...** a vyberte položku nabídky **Software jako servis.**

Pokud vyberete jiný typ nabídky, můžete být přesměrováni na starší [portál partnerů cloudu](https://cloudpartner.azure.com/). V současné době jsou na portálu Commercial Marketplace v Centru partnerů k dispozici pouze nabídky SaaS a Dynamics 365.

![Okno vytvořit nabídku v Centru partnerů](./media/new-offer-click.png)

Zobrazí se dialogové okno **Nová nabídka.**

![Dialogové okno Nová nabídka](./media/new-offer-popup.png)

## <a name="offer-id-and-alias"></a>ID nabídky a alias

- **ID nabídky**: Jedinečný identifikátor pro každou nabídku ve vašem účtu. Toto ID bude viditelné pro zákazníky v adrese URL pro nabídku marketplace a šablony Azure Resource Manager (pokud je k dispozici). ID nabídky musí být malá, alfanumerická (včetně spojovníků a podtržítk, ale bez mezer). **ID nabídky** je omezeno na 50 znaků a po výběru *možnosti Vytvořit*jej nelze změnit.  
Příklad: test-nabídka-1
<br>Výsledkem je adresa URL:`https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`

- **Alias nabídky**: Název používaný k odkazování na nabídku na portálu Partnerského centra. Tento název nebude na trhu použit a liší se od *názvu nabídky* a dalších hodnot, které se zobrazí zákazníkům. Tuto hodnotu nelze změnit po výběru *možnosti Vytvořit*.

<br>Příklad: Testovací nabídka 1&#8482;

Vyberte **Vytvořit**.  Pro tuto nabídku je vytvořena stránka **Přehled nabídky.**  

<!---
![Offer overview on Partner Center](./media/commercial-marketplace-offer-overview.png)
-->

## <a name="offer-overview"></a>Přehled nabídky

Stránka **Přehled nabídky** obsahuje:

- **Stav Publikování** zobrazuje vizuální znázornění kroků potřebných k publikování této nabídky a jak dlouho bude trvat dokončení každého kroku. Ikony kroků neúplného publikování budou zašedlé.

- Nabídka **Přehled nabídky** obsahuje seznam odkazů pro provádění operací s touto nabídkou. Tento seznam operací se změní na základě výběru, který provedete pro svou nabídku.  
    - Pokud je nabídka konceptem - Odstranit koncept
    - Pokud je nabídka živá - Stop sell nabídka
    - Pokud je nabídka ve verzi preview - Go-live
    - Pokud jste se vydavatelne neodhlásit - Zrušit publikování

## <a name="offer-setup"></a>Nastavení nabídky

Karta **Nastavení nabídky** vás požádá o následující informace. Po dokončení těchto polí vyberte **Uložit.**

- **Chcete prodávat prostřednictvím společnosti Microsoft?** (Ano/Ne)
    - **Ano**, chcete prodat svou nabídku prostřednictvím společnosti Microsoft, s microsoft hosting marketplace transakce vaším jménem; Nebo 
    - **Ne**, raději byste jen seznam vaší nabídky prostřednictvím tržišť, zpracování všech peněžních transakcí nezávisle na společnosti Microsoft.

### <a name="sell-through-microsoft"></a>Prodáváme přes Microsoft

Prodej prostřednictvím společnosti Microsoft poskytuje lepší zjišťování a získávání zákazníků, umožňuje společnosti Microsoft hostovat transakce na trhu vaším jménem a využívá globálně dostupných obchodních funkcí společnosti Microsoft.

#### <a name="saas-offer-requirements"></a>Požadavky na nabídku SaaS

Chcete-li uvést nabídky softwaru jako služby (SaaS) s commercial marketplace v partnerském centru, musí být splněna následující kritéria:

- Vaše nabídka musí používat [Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) pro správu identit a ověřování.
- Vaše nabídka musí používat [saas plnění API](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2) pro integraci s Azure Marketplace.
- Podrobnější požadavky naleznete v [Průvodci publikováním nabídek SaaS](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide).

#### <a name="saas-pricing-and-billing-options"></a>Možnosti saas u cen a fakturace

Díky tomu, že řešení SaaS běží v předplaceném Azure vydavatele, licenční poplatky placené zákazníky zahrnují náklady na infrastrukturu, na které se software nasazuje. Využití infrastruktury Azure se spravuje a účtuje přímo vám, partnerovi. Skutečné poplatky za využití infrastruktury nejsou zákazníkem vidět. Vydavatelé by měli do cen softwarových licencí začlenit poplatky za využití infrastruktury Azure. 

SaaS nabízí podporu měsíční nebo roční fakturaci na základě paušálního poplatku za uživatele nebo poplatků za spotřebu pomocí fakturační služby účtované podle objemu dat. Komerční tržiště společnosti Microsoft funguje na modelu agentury, kdy vydavatelé stanovují ceny, microsoft účtuje zákazníkům a Microsoft platí vydavateli příjmy, zatímco zadržuje poplatek za agenturu.

V následující tabulce je uveden příklad rozpisu nákladů a výplat, které demonstrují model agentury.

|**Náklady na licenci**|**$100 za měsíc**|
|:---|:---|
|Náklady na využití Azure (D1/1-Core)|Fakturováno přímo vydavateli, nikoli zákazníkovi|
|Odběrateli se účtuje společnost Microsoft.|100,00 USD měsíčně (Vydavatel musí v licenčním poplatku uvodit veškeré vzniklé nebo předávací náklady na infrastrukturu).|

|**Účty společnosti Microsoft**|**$100 za měsíc**|
|:---|:---|
|Microsoft vám zaplatí 80 % nákladů na licenci <br>**U kvalifikovaných aplikací SaaS hradí Microsoft 90 % nákladů na licenci*|$80.00 za měsíc <br>*$* 90,00 za měsíc*|

- V tomto příkladu společnost Microsoft účtuje zákazníkovi 100,00 USD za licenci na software a vyplatí vydavateli 80,00 USD.
- Partneři, kteří se kvalifikovali pro **sníženou službu Marketplace,** uvidí snížený transakční poplatek u nabídek SaaS od května 2019 do června 2020. V tomto scénáři společnost Microsoft účtuje 100,00 USD za licenci softwaru a vyplatí vydavateli 90,00 USD.

> [!NOTE]
> **Snížený poplatek za službu Marketplace**: U některých nabídek SaaS, které jste zveřejnili na našem komerčním trhu, sníží společnost Microsoft poplatek za službu Marketplace z 20 % (jak je popsáno ve smlouvě s vydavatelem společnosti Microsoft) na 10 %. Aby se vaše nabídka kvalifikovala, musí být alespoň jedna z vašich nabídek společností Microsoft označena jako připravená k imisce nebo jako prioritní ip prodej.  Aby bylo možné získat tento snížený poplatek za službu Marketplace za měsíc, musí být způsobilost splněna nejméně pět (5) pracovních dnů před koncem každého kalendářního měsíce.  Snížený poplatek za službu Marketplace se nevztahuje na virtuální společnosti, spravované aplikace ani na jiné produkty zpřístupněné prostřednictvím našeho komerčního tržiště.  Snížený poplatek za službu Marketplace bude k dispozici pouze pro kvalifikované nabídky licenčních poplatků vybraných společností Microsoft od 1.  Po uplynutí této doby se poplatek za službu Marketplace vrátí na svou obvyklou částku.

### <a name="list-through-microsoft"></a>Seznam prostřednictvím společnosti Microsoft

Propagujte svou firmu se společností Microsoft vytvořením nabídky na marketplace. Výběr seznamu pouze nabídky a neprovádění transakcí prostřednictvím microsoftu znamená, že se společnost Microsoft přímo neúčastní transakcí s licencemi na software. Neexistuje žádný související transakční poplatek a vydavatel uchovává 100% všech licenčních poplatků za software vybraných od zákazníka. Vydavatel je však zodpovědný za podporu všech aspektů transakce softwarové licence, včetně mimo jiné: plnění objednávek, měření, fakturace, fakturace, platby a inkasa.

- **Jak chcete, aby potenciální zákazníci komunikovali s touto nabídkou nabídek?**

#### <a name="get-it-now-free"></a>Získejte jej nyní (zdarma)

Seznam vaší nabídky zákazníkům zdarma poskytnutím platné adresy URL (počínaje *http* nebo *https),* kde mohou získat zkušební verzi prostřednictvím [ověřování jedním kliknutím pomocí Služby Azure Active Directory (Azure AD).](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide#using-azure-active-directory-to-enable-trials)  Příklad: `https://contoso.com/saas-app`

#### <a name="free-trial-listing"></a>Zkušební verze zdarma (výpis)

Uveďte svou nabídku zákazníkům s odkazem na bezplatnou zkušební verzi zadáním platné adresy URL (začínající *na http* nebo *https),* kde můžou získat zkušební verzi pomocí [ověřování jedním kliknutím pomocí Služby Azure Active Directory (Azure AD).](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide#using-azure-active-directory-to-enable-trials)  Například: `https://contoso.com/trial/saas-app`. Nabídka výpis bezplatné zkušební verze jsou vytvořeny, spravovány a nakonfigurovány vaší službou a nemají předplatná spravuje společnost Microsoft.

> [!NOTE]
> Tokeny, které vaše aplikace obdrží prostřednictvím zkušebního odkazu, lze použít jenom k získání informací o uživateli prostřednictvím služby Azure AD k automatizaci vytváření účtu ve vaší aplikaci. Účty Microsoft (MSA) nejsou podporovány pro ověřování pomocí tohoto tokenu.

#### <a name="contact-me"></a>Kontaktujte mě

Shromážděte kontaktní informace zákazníků propojením systému řízení vztahů se zákazníky (CRM). Zákazník bude požádán o povolení ke sdílení svých informací. Tyto podrobnosti o zákaznících spolu s názvem nabídky, ID a zdrojem tržiště, kde našli vaši nabídku, budou odeslány do systému CRM, který jste nakonfigurovali. Další informace o konfiguraci aplikace CRM naleznete v tématu [Připojení správy zájemců](#connect-lead-management).

## <a name="example-marketplace-offer-listing"></a>Příklad nabídky na marketplace

![Příklad výpisu nabídky tržiště s poznámkami](./media/marketplace-offer.svg)

## <a name="enable-a-test-drive"></a>Povolení zkušební jízdy

Testovací jízda je skvělý způsob, jak předvést svou nabídku potenciálním zákazníkům tím, že jim dáte možnost "vyzkoušet si před nákupem", což vede ke zvýšené konverzi a generování vysoce kvalifikovaných potenciálních zákazníků. Další informace najdete v [tématu Povolení zákazníkům otestovat nabídku](./test-drive.md).

- **Povolení testovací jízdy** (zaškrtávací políčko)

Povolením testovací jednotky budete vyzváni ke konfiguraci demonstračního prostředí pro zákazníky, aby vyzkoušeli vaši nabídku na pevně stanovenou dobu. 

### <a name="test-drive-resources"></a>Prostředky testovací jednotky

- [Osvědčené postupy](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)- pro marketing test drive[technické osvědčené postupy](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Přehled testovací jízdy (stažení ve formátu PDF)](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf)

## <a name="connect-lead-management"></a>Připojení správy potenciálních zákazníků

[!INCLUDE [Connect lead management](./includes/connect-lead-management-a.md)]

#### <a name="additional-lead-management-resources"></a>Další zdroje řízení zájemců
- [Nejčastější dotazy ke správě potenciálních zákazníků](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [Běžné chyby konfigurace potenciálních zákazníků](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#common-lead-configuration-errors-during-publishing-on-cloud-partner-portal)
- [Přehled správy zájemců Jeden operátor](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf)

Nezapomeňte **uložit** před přechodem na další část.

## <a name="properties"></a>Vlastnosti

Na kartě **Vlastnosti** se zobrazí dotaz na definování kategorií a odvětví používaných k seskupení nabídky na tržištích, právních smluv podporujících vaši nabídku a verze aplikace.

Po dokončení těchto polí vyberte **Uložit.**

### <a name="category"></a>Kategorie

Vyberte minimálně jednu (1) a maximálně tři (3) kategorie použité pro seskupení nabídky do příslušných oblastí vyhledávání na trhu. V popisu nabídky si můžete vyříkat, jak vaše nabídka tyto kategorie podporuje.

### <a name="industry"></a>Odvětví

[!INCLUDE [Industry Taxonomy](./includes/industry-taxonomy.md)]

### <a name="app-version"></a>verze aplikace

Toto pole je volitelné a používá se na trhu AppSource k identifikaci čísla verze vaší nabídky.

### <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Standardní smlouva pro komerční trh společnosti Microsoft

Společnost Microsoft poskytuje šablonu standardní smlouvy.

- **Použít standardní smlouvu pro komerční trh společnosti Microsoft?**

Aby se zjednodušil proces zásobování zákazníků a snížila se právní složitost pro dodavatele softwaru, nabízí společnost Microsoft standardní smlouvu pro komerční tržiště společnosti Microsoft, která usnadňuje transakce na trhu. Namísto vytváření vlastních smluvních podmínek se vydavatelé komerčního trhu mohou rozhodnout nabízet svůj software podle standardní smlouvy, kterou zákazníci potřebují pouze jednou projet a přijmout. Standardní smlouvu naleznete zde: https://go.microsoft.com/fwlink/?linkid=2041178.

Můžete vybrat, zda chcete použít standardní smlouvu namísto poskytnutí vlastních smluvních podmínek zaškrtnutím políčka Použít standardní smlouvu pro komerční tržiště.

![Použití zaškrtávacího políčka Standardní smlouva](./media/use-standard-contract.png)

> [!NOTE]
> Jakmile nabídku publikujete pomocí smlouvy Standard pro komerční tržiště společnosti Microsoft, nebudete moci používat vlastní smluvní podmínky. Je to "nebo" scénář. Buď nabízíte své řešení podle standardní **smlouvy, nebo** vlastních podmínek. Chcete-li změnit podmínky standardní smlouvy, můžete tak učinit prostřednictvím standardních změn smlouvy.

#### <a name="standard-contract-amendments"></a>Standardní změny smlouvy

Standardní změny smlouvy umožňují vydavatelům vybrat standardní smluvní podmínky pro jednoduchost a přizpůsobit podmínky pro svůj produkt nebo firmu. Zákazníci potřebují zkontrolovat změny smlouvy pouze v případě, že již přezkoumali a přijali standardní smlouvu společnosti Microsoft.

Existují dva druhy změn k dispozici pro komerční trh vydavatelů:

- Univerzální změny: Tyto změny jsou uplatňovány univerzálně na standardní smlouvu pro všechny zákazníky. Univerzální změny jsou zobrazeny každému zákazníkovi nabídky v nákupním toku. Zákazníci musí před použitím vaší nabídky přijmout podmínky standardní smlouvy a dodatku.
- Vlastní změny: Tyto změny jsou speciální změny standardní smlouvy, které jsou určeny pro konkrétní zákazníky pouze prostřednictvím ID klienta Azure. Vydavatelé si můžou vybrat klienta, na kterého chtějí cílit. Pouze zákazníci z nájemce budou prezentovány s vlastní změny podmínky v nabídce nákupní tok.  Zákazníci musí před použitím vaší nabídky přijmout podmínky standardní smlouvy a dodatků.

>[!NOTE]
> Tyto dva typy změn zásobníku na sebe. Zákazníci, na které se zaměňují s vlastními změnami, dostanou také univerzální změnu standardní smlouvy během nákupu.

**Podmínky univerzálního dodatku ke standardní smlouvě pro komerční tržiště společnosti Microsoft**: Do tohoto pole zadejte univerzální podmínky změny. Můžete poskytnout jeden univerzální dodatek pro každou nabídku. Do tohoto pole můžete zadat neomezený počet znaků. Tyto podmínky se zobrazí zákazníkům v AppSource, Azure Marketplace a/nebo na webu Azure Portal během toku zjišťování a nákupu.

**Podmínky vlastních změn ke standardní smlouvě pro komerční tržiště společnosti Microsoft**: Začněte výběrem **podmínek přidání vlastních změn**. V každé nabídce můžete poskytnout až 10 vlastních podmínek změny.

- **Podmínky vlastního dodatku**: Do pole podmínky vlastního dodatku zadejte podmínky vlastní změny. Do tohoto pole můžete zadat neomezený počet znaků. Pouze zákazníci z ID klienta, které zadáte pro tyto vlastní podmínky, budou v nákupním toku nabídky na webu Azure Portal uvedeny s podmínkami vlastnízměny.  
- **ID klienta** (povinné): Každá vlastní změna může být zaměřena až na 20 ID klienta. Pokud přidáte vlastní změnu, musíte zadat alespoň jedno ID klienta. ID klienta identifikuje vašeho zákazníka v Azure. Můžete požádat zákazníka o toto ID a oni ho můžou najít tak, že ho najdou portal.azure.com > služby Azure Active Directory > vlastnosti. Hodnota ID adresáře je ID klienta (například 50c464d3-4930-494c-963c-1e951d15360e). ID klienta vašeho zákazníka můžete také vyhledat pomocí adresy URL názvu domény na adrese [What is my Microsoft Azure and Office 365 tenant ID?](https://www.whatismytenantid.com)
- **Popis** (volitelné): Volitelně zadejte popis pro ID klienta, který vám pomůže identifikovat zákazníka, na kterého cílíte s dodatkem.

#### <a name="terms-and-conditions"></a>podmínky a ujednání

Chcete-li zadat vlastní smluvní podmínky, můžete se rozhodnout, že je zadáte do pole smluvních podmínek. Do tohoto pole můžete zadat až 10 000 znaků textu. Pokud vaše smluvní podmínky vyžadují delší popis, zadejte do tohoto pole odkaz na jednu adresu URL, ve kterém lze vaše smluvní podmínky najít. Zobrazí se zákazníkům jako aktivní odkaz.

Zákazníci jsou povinni přijmout tyto podmínky, než budou moci vyzkoušet vaši nabídku.

Nezapomeňte **uložit** před přechodem na další část.

## <a name="offer-listing"></a>Nabídka výpis

Na kartě Nabídka se zobrazují jazyky (a trhy), kde je vaše nabídka dostupná, v současné době je jediným dostupným místem angličtina (Spojené státy). Kromě toho tato stránka zobrazuje stav zápisu specifického pro jazyk a datum a čas, kdy byl přidán. Budete muset definovat podrobnosti tržiště (název nabídky, popis, hledané termíny atd.) pro každý jazyk / trh.

> [!NOTE]
> Nabídka výpis obsah (jako je popis nabídky, dokumenty, screenshoty, podmínky použití a zásady ochrany osobních údajů) nemusí být v angličtině, pokud popis nabídky začíná frází: "Tato aplikace je k dispozici pouze v [non-anglický jazyk]." Je také přijatelné poskytnout *užitečnou adresu URL odkazu* pro nabízení obsahu v jiném jazyce, než který byl použit v obsahu nabídky.

### <a name="offer-listings"></a>Nabídka výpisy

Uveďte podrobnosti, které se mají zobrazit na trhu, včetně popisů vašich nabídek a marketingových aktiv.

- **Název** (povinné): Zde definovaný název se zobrazí jako název nabídky na tržišti, které jste si vybrali. Název je předem vyplněn na základě předchozí položky **nové nabídky.** Název může být ochrannou známkou. Nesmí obsahovat emodži (pokud se nejedná o symboly ochranné známky a autorských práv) a musí být omezeno na 50 znaků.
- **Shrnutí** (povinné): Uveďte krátký popis nabídky, která má být použita ve výsledcích vyhledávání na marketplace. Do tohoto pole lze zadat až 100 znaků textu.
- **Popis** (povinné): Zadejte popis nabídky, která se má zobrazit v přehledu výpisů na marketplace. Zvažte zahrnutí návrhu hodnoty, klíčových výhod, všech kategorií nebo průmyslových asociací, příležitostí k nákupu v aplikaci, požadovaných zveřejnění a odkazu, abyste se dozvěděli více.
Do tohoto pole lze zadat až 3 000 znaků textu včetně značek. Další tipy najdete v článku [Napište skvělý popis aplikace](https://docs.microsoft.com/windows/uwp/publish/write-a-great-app-description).
- **Klíčová slova pro vyhledávání**: Zadejte až tři klíčová slova pro vyhledávání, která mohou zákazníci použít k vyhledání vaší nabídky na tržištích.
- **Pokyny začínáme** (povinné): Vysvětlete, jak nakonfigurovat a začít používat aplikaci pro potenciální zákazníky.  Tento rychlý start může obsahovat odkazy na podrobnější online dokumentaci. Do tohoto pole lze zadat až 3 000 znaků textu.

#### <a name="description"></a>**Popis**

Toto pole je povinné. Položky, které mají být zahrnuty do **popisu**:

* V prvních několika větách popisu jasně popište hodnotu nabídky.  
* Mějte na paměti, že prvních několik vět může být zobrazeno ve výsledcích vyhledávače.  
* Při prodeji produktu se nespoléhejte na funkce a funkce. Místo toho se zaměřte na hodnotu, kterou dodáváte.  
* Používejte co nejvíce slovní zásobu nebo formulace založené na výhodách.

Základní součásti návrhu hodnoty by měly obsahovat následující informace:

* Popis produktu.
* Typ uživatele, který využívá produkt.
* potřeby nebo bolest, kterou produkt řeší.

Chcete-li, aby vaše nabídka **Popis** byl poutavější, naformátujte popis pomocí editoru rtf.

![Použití editoru rtf](./media/text-editor2.png)

Pomocí editoru RTF použijte následující pokyny:

- Chcete-li změnit formát obsahu, zvýrazněte text, který chcete formátovat, a vyberte styl textu, jak je znázorněno níže:

     ![Změna formátu textu pomocí editoru RTF](./media/text-editor3.png)

- Chcete-li do textu přidat seznam s odrážkami nebo číslovaný seznam, použijte následující možnosti:

     ![Přidání seznamů pomocí editoru RTF](./media/text-editor4.png)

- Chcete-li k textu přidat nebo odebrat odsazení, použijte následující možnosti:

     ![Odsazení pomocí editoru rtf](./media/text-editor5.png)

#### <a name="links"></a>Odkazy

- **Zásady ochrany osobních údajů** (povinné): Odkaz na zásady ochrany osobních údajů vaší organizace. Jste zodpovědní za to, že vaše aplikace je v souladu se zákony a předpisy ochrany osobních údajů a za poskytnutí platných zásad ochrany osobních údajů
- **Marketingové materiály programu CSP** (volitelné): Pokud se rozhodnete rozšířit nabídku na program [Poskytovatele cloudových řešení (CSP),](https://docs.microsoft.com/azure/marketplace/cloud-solution-providers) uveďte odkaz na marketingové materiály. CSP rozšiřuje vaši nabídku na širší škálu kvalifikovaných zákazníků tím, že umožňuje partnerům CSP sdružovat, prodávat a dále prodávat vaši nabídku. Tito prodejci budou potřebovat přístup k materiálům pro marketing vaší nabídky. Další informace naleznete [v tématu Go-To-Market Services](https://partner.microsoft.com/reach-customers/gtm).
- **Užitečné odkazy** (volitelné): Volitelné doplňkové online dokumenty o vaší aplikaci nebo souvisejících službách uvedené poskytnutím **názvu** a **adresy URL**. Přidejte další užitečné odkazy kliknutím na **+ Přidat adresu URL**.

#### <a name="contact-information"></a>Kontaktní informace

- **Kontakty**: Pro každý kontakt zákazníka zadejte **jméno**zaměstnance , **telefonní číslo**a **e-mailovou** adresu.  (Ty *nebudou* zobrazeny veřejně). Adresa **URL podpory** je vyžadována také pro skupinu kontaktů **podpory.**  (Tyto informace *budou* zobrazeny veřejně).

**Kontakt podpory** (povinné): Pro obecné dotazy podpory.

**Technický kontakt** (povinné): Pro technické dotazy.

**Kontakt správce kanálů** (povinné): Pro dotazy prodejce týkající se programu CSP.

#### <a name="files-and-images"></a>Soubory a obrázky

- **Dokumenty** (povinné): Přidejte související marketingové dokumenty pro vaši nabídku ve formátu PDF, které poskytují minimálně jeden (1) a maximálně tři (3) dokumenty na nabídku.
- **Obrázky** (volitelné): Existuje více míst, kde se obrázky loga vaší nabídky mohou zobrazovat na celém trhu(s), což vyžaduje následující velikosti – Malé: 48 x 48 pixelů _(povinné),_ Střední: 90 x 90 pixelů _(povinné),_ Velké: 216 x 216 pixelů _(povinné),_ Široké: 255 x 115 pixelů a Hrdina: 815 x 290 pixelů. Všechny obrázky musí být v . png formátu.
- **Snímky obrazovky** (povinné): Přidejte snímky obrazovky demonstrující vaši nabídku. Mohou být přidány maximálně pět (5) snímků obrazovky a měly by být dimenzovány na 1280 x 720 pixelů. Všechny obrázky musí být v . png formátu.
- **Videa** (volitelně): Přidejte odkazy na videa demonstrující vaši nabídku. Můžete použít odkazy na YouTube a / nebo Vimeo videa, které jsou zobrazeny spolu s vaší nabídkou zákazníkům. Budete také muset zadat miniaturu videa o velikosti 1280 x 720 pixelů ve formátu PNG. V nabídce můžete zobrazit maximálně čtyři videa.

Nezapomeňte **uložit** před přechodem na další část.

>[!Note]
>Pokud máte problém s nahráváním souborů, ujistěte se, že místní síť neblokuje službu https://upload.xboxlive.com používanou partnerským centrem.

#### <a name="additional-marketplace-listing-resources"></a>Další zdroje informací o výpisu tržiště

- [Doporučené postupy pro nabídky na marketplace](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

## <a name="preview"></a>Preview

Karta **Náhled** umožňuje definovat omezený **okruh uživatelů ve verzi Preview** pro uvolnění nabídky před publikováním nabídky živě širšímu okruhu uživatelů na marketplace.

> [!IMPORTANT]
> Po kontrole nabídky ve verzi Preview vyberte **Přejít živě,** aby mohla být vaše nabídka zveřejněna živě veřejnému publiku na trhu.

- **Definujte cílovou skupinu náhledu: Přidejte jeden e-mail účtu AAD/MSA na řádek spolu s volitelným popisem.**

Přidejte až 10 e-mailových adres ručně nebo 20 při nahrávání souboru CSV pro existující účty Microsoft (MSA) nebo Azure Active Directory, které vám pomohou ověřit vaši nabídku před publikováním v přímém přenosu. Přidáním těchto účtů definujete okruh uživatelů, kterým bude povolen náhled přístup k vaší nabídce před publikováním na tržišti. Pokud je vaše nabídka již aktivní, můžete stále definovat náhled publika pro testování jakýchkoli změn nebo aktualizací nabídky.

> [!NOTE]
> Náhled okruhu uživatelů se liší od soukromého okruhu uživatelů. Před _publikováním_ na tržištích je přístup k vaší nabídce ve verzi Preview povolen přístup k vaší nabídce. Můžete také vytvořit plán a zpřístupnit jej pouze soukromému publiku. Na kartě **výpis plánu** můžete definovat soukromou cílovou skupinu pomocí zaškrtávacího **políčka Toto je soukromý plán.** Potom můžete definovat soukromou skupinu až 20 000 zákazníků pomocí ID klienta Azure.

## <a name="technical-configuration"></a>Technická konfigurace

Karta **Technická konfigurace** definuje technické podrobnosti (cesta url, webhook, ID klienta a ID aplikace), které se používají k připojení k vaší nabídce. Toto připojení nám umožňuje zřídit vaši nabídku pro koncového zákazníka, pokud se ji rozhodne získat. Diagramy popisující použití shromážděných polí jsou k dispozici v dokumentaci pro [rozhraní API plnění SaaS](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2).

- **Adresa URL vstupní stránky** (povinné): Definujte adresu URL webu, na kterou zákazníci přistanou po získání vaší nabídky z tržiště. Tato adresa URL bude koncový bod, který obdrží token, když je zákazník směrován na stránku. Tento token lze vyměnit za zřizování podrobnosti pomocí řešení v plnění API. Tyto údaje a všechny ostatní, které shromažďujete, mohou být použity jako součást interaktivní webové stránky vytvořené ve vašem prostředí k dokončení registrace a aktivaci jejich nákupu.

- **Připojení webhook** (povinné): Pro všechny asynchronní události, které společnost Microsoft potřebuje k odeslání jménem zákazníka (například Předplatné SaaS je neplatné), požadujeme, abyste poskytli připojení webhooku. Pokud ještě nemáte systém webhooku na místě, nejjednodušší konfigurace je mít http endpoint logiky aplikace, která bude naslouchat všechny události, které\/jsou zaúčtovány do něj a pak s nimi správně zpracovat (například https: /prod-1westus.logic.azure.com:443/work). Další informace najdete v tématu [Volání, aktivační události nebo vnoření pracovních postupů s koncovými body HTTP v aplikacích logiky](https://docs.microsoft.com/azure/logic-apps/logic-apps-http-endpoint).

- **ID klienta Azure AD** (povinné): Uvnitř portálu Azure, požadujeme, abyste [vytvořili aplikaci Azure Active Directory (AD),](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) abychom mohli ověřit připojení mezi našimi dvěma službami je za ověřenou komunikaci. Pokud chcete najít [ID klienta](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in), přejděte do služby Azure Active Directory a vyberte **Vlastnosti**a vyhledejte uvedené číslo **ID adresáře** (například 50c464d3-4930-494c-963c-1e951d15360e).

- **ID aplikace Azure AD** (povinné): Potřebujete také [ID aplikace](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) a ověřovací klíč. Chcete-li tyto hodnoty získat, přejděte do **služby**Azure Active Directory a vyberte registrace aplikací , vyhledejte uvedené číslo **ID aplikace** (například 50c464d3-4930-494c-963c-1e951d15360e). Ověřovací klíč najdete v **nastavení** a vyberte **klávesy**. Budete muset zadat popis a dobu trvání a poté bude poskytnuta číselná hodnota.

>[!Note]
>ID aplikace Azure je přidružené k ID vydavatele, takže se ujistěte, že stejné ID aplikace se používá ve všech vašich nabídkách.

## <a name="plan-overview"></a>Přehled plánu

Karta **Přehled plánu** umožňuje poskytovat různé možnosti plánu v rámci stejné nabídky. Tyto plány (někdy označované jako sku) se mohou lišit, pokud jde o verzi, zpeněžení nebo úrovně služeb. Chcete-li prodat svou nabídku na trhu, musíte nastavit alespoň jeden plán.

Po vytvoření se zobrazí názvy plánu, ID, cenové modely, dostupnost (veřejné nebo soukromé), aktuální stav publikování a všechny dostupné akce.

**Akce** dostupné v **přehledu plánu** se liší v závislosti na aktuálním stavu plánu a mohou zahrnovat:

- Pokud je stav plánu **Koncept** - Odstranit koncept
- Pokud je stav plánu **Live** - Stop sell plan nebo Sync private audience

**Vytvořit nový plán** (minimálně jeden plán pro ty, kteří se chtějí prodávat prostřednictvím společnosti Microsoft)

- **ID plánu:** Vytvořte jedinečné ID plánu pro každý plán v této nabídce. Toto ID bude viditelné pro zákazníky v url produktu a azure resource manager šablony (pokud je k dispozici). Používejte pouze malá písmena, alfanumerické znaky, pomlčky nebo podtržítka. Pro toto ID plánu je povoleno maximálně 50 znaků. ID nelze změnit po výběru vytvořit.
- **Název plánu:** Zákazníci se tomuto názvu zobrazí při rozhodování o tom, který plán v rámci vaší nabídky vybrat. Vytvořte jedinečný název nabídky pro každý plán v této nabídce. Název plánu se používá k rozlišení softwarových plánů, které mohou být součástí stejné nabídky (například název nabídky: Windows Server; plány: Windows Server 2016, Windows Server 2019).

### <a name="plan-listing"></a>Výpis plánu

Na kartě **výpis plánu** jsou zobrazeny jazyky (a trhy), kde je váš plán k dispozici, v současné době je jediným dostupným místem angličtina (Spojené státy). Kromě toho tato stránka zobrazuje stav zápisu specifického pro jazyk a datum a čas, kdy byl přidán. Budete muset definovat podrobnosti tržiště (název nabídky, popis, hledané termíny atd.) pro každý jazyk / trh.

#### <a name="plan-listing-details"></a>Podrobnosti o výpisu plánu

Výběrem jednoho z jazyků plánu se zobrazí informace o **seznamu plánu,** včetně **názvu** a **popisu.**

- **Název**: Předem vyplněný na základě náhledu **Položka Nový plán** a zobrazí se jako název "Softwarového tarifu" vaší nabídky zobrazeného na trhu.
- **Popis:** Tento popis je příležitostí vysvětlit, co dělá tento softwarový plán jedinečným a veškeré rozdíly od jiných softwarových plánů v rámci vaší nabídky. Může obsahovat až 500 znaků.

Po dokončení těchto polí vyberte **Uložit.**

#### <a name="plan-pricing-and-availability"></a>Plánování cen a dostupnosti

Karta **Ceny a dostupnost** umožňuje nakonfigurovat trhy, na kterých bude tento plán k dispozici, požadovaný model zpeněžení, cenu a fakturační období. Kromě toho můžete určit, zda má být plán viditelný pro všechny nebo pouze pro konkrétní zákazníky (soukromou skupinu uživatelů).

##### <a name="enabling-free-trials"></a>Povolení bezplatných zkušebních verzí

Nabídky SaaS prostřednictvím komerčního tržiště vám umožňují poskytovat měsíční bezplatnou zkušební verzi při prodeji prostřednictvím společnosti Microsoft. Pro všechny modely fakturace a podmínky s výjimkou plánů účtovaných podle objemu dat jsou podporovány bezplatné zkušební verze. Tato možnost umožňuje zákazníkům mít nízkou bariéru vstupu přes jeden měsíc volného přístupu.  Pokud se rozhodnete povolit bezplatnou zkušební verzi pro plány v rámci vaší nabídky, zákazník nebude moci převést na placené předplatné před koncem počátečního období jednoho měsíce.  Během této doby si zákazníci, kteří si zakoupí vaši nabídku, mohou vyzkoušet některý z podporovaných plánů, které mají povolenou bezplatnou zkušební verzi a převést mezi nimi.  Převod na placené předplatné se provádí automaticky na konci období.

>[!Note]
>Pokud se zákazník rozhodne převést na plán bez bezplatných zkušebních verzí, dojde k převodu, ale bezplatná zkušební verze bude okamžitě ztracena.  Jakmile zákazník začne platit za plán, nemůže již znovu získat bezplatnou zkušební verzi na stejném předplatném, a to ani v případě, že převede na skladovou položku, která podporuje bezplatné zkušební verze.

Možnost konfigurace bezplatné zkušební verze je k dispozici pro každý plán ve vaší nabídce. Přejděte na Cenu a dostupnost pro každou nabídku a zaškrtněte políčko, abyste povolili měsíční zkušební verzi.

![Zaškrtávací políčko zkušební verze na jeden měsíc](./media/free-trial-enable.png)

>[!Note]
>Jakmile je vaše převoditelná nabídka zveřejněna s bezplatnou zkušební verzí, nelze ji pro tento plán zakázat. Ujistěte se, že toto nastavení je správné pro první publikování, aby se zabránilo nutnosti znovu vytvořit plán.

Chcete-li získat informace o odběrech zákazníků, která se `isFreeTrial`aktuálně účastní bezplatné zkušební verze, použijte novou vlastnost rozhraní API , která bude označena jako pravdivá nebo nepravdivá. Další informace naleznete v rozhraní [Api pro získání předplatného SaaS](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2#get-subscription).

>[!Note]
>Bezplatné zkušební verze nejsou podporovány pro plány, které využívají službu měření tržiště.

#### <a name="markets"></a>Trhy

- **Upravit trhy** (volitelné)

Každý plán musí být k dispozici alespoň na jednom trhu. Zaškrtněte políčko pro libovolné místo na trhu, kde chcete tento plán zpřístupnit. Vyhledávací pole a tlačítko pro výběr zemí "Promované daně", ve kterých microsoft uložených daní a použití daně vaším jménem, jsou zahrnuty jako pomoc.

Pokud jste již nastavili ceny pro svůj plán v amerických dolarech (USD) a přidali další umístění na trhu, cena nového trhu bude vypočtena podle aktuálních směnných kurzů. Před publikováním zkontrolujte cenu pro každý trh. Ceny lze zkontrolovat pomocí odkazu "Vývozní ceny (xlsx)" po uložení změn.

#### <a name="pricing"></a>Ceny

- **Cenový model**: Paušální sazba nebo Seat na základě

**Paušální sazba:** Povolte přístup k vaší nabídce s jedinou měsíční nebo roční cenou paušální ceny. To se někdy označuje jako ceny na webu. Pomocí tohoto cenového modelu můžete volitelně definovat plány účtované podle objemu dat, které používají rozhraní API služby měření webu Marketplace k nabíjení zákazníků podle nestandardních jednotek.  Další informace o účtované fakturaci se měří na webu [fakturace účtované podle objemu dat pomocí služby měření marketplace](./saas-metered-billing.md).

**Na uživatele:** Povolte přístup k vaší nabídce s cenou na základě počtu uživatelů, kteří mají přístup k nabídce nebo zabírají místa. Tento model založený na uživateli umožňuje nastavit minimální a maximální povolený počet uživatelů na základě ceny. Tímto způsobem lze nakonfigurovat různé cenové body na základě počtu uživatelů konfigurací více plánů.  Tato pole jsou nepovinná. Pokud není vybrána, počet uživatelů bude interpretován jako nemít limit (min 1 a max tolik, kolik systém může podporovat). Tato pole mohou být upravena jako součást aktualizace plánu.

Po publikování nelze změnit volbu modelu účtování cen. Kromě toho musí všechny plány pro stejnou nabídku sdílet stejný cenový model.

- **Fakturační období**: Měsíční nebo roční

Vyberte frekvenci, kterou musí zákazníci zaplatit uvedenou cenu. Musí být poskytnuta alespoň jedna měsíční nebo roční cena, jinak mohou být zákazníkům k dispozici obě možnosti.

- **Cena:** USD za měsíc nebo USD za rok

Ceny stanovené v USD (USD = Americký dolar) jsou převedeny na místní měnu všech vybraných trhů pomocí aktuálních směnných kurzů při uložení. Před publikováním ověřte tyto ceny exportem tabulky cen a kontrolou ceny na jednotlivých trzích. Pokud chcete nastavit vlastní ceny na individuálním trhu, upravte a importujte tabulku cen. Jste zodpovědní za ověření těchto cen a vlastníte tato nastavení.
*\*Chcete-li povolit export dat o cenách, musíte nejprve uložit změny cen.*

Před publikováním si pečlivě zkontrolujte ceny, protože existují určitá omezení, co se může po publikování plánu změnit:

- Po publikování plánu nelze změnit cenový model.
- Jakmile je fakturační období publikováno pro plán, nelze jej později odebrat.
- Jakmile je cena za trh ve vašem plánu zveřejněna, nemůže být později změněna.

### <a name="plan-audience"></a>Plánování publika

Máte možnost nakonfigurovat každý plán tak, aby byl viditelný pro všechny uživatele nebo pouze pro konkrétní cílovou skupinu podle vašeho výběru. Členství v této omezené cílové skupině můžete přiřadit pomocí ID klienta Azure AD.

#### <a name="privacy"></a>Ochrana osobních údajů

- **Jedná se o soukromý plán** (volitelné zaškrtávací políčko)

Zaškrtnutím tohoto políčka bude váš plán soukromý a viditelný pouze pro okruh uživatelů s omezeným přístupem, který si vyberete. Po publikování jako soukromý plán můžete okruh uživatelů aktualizovat nebo zvolit zpřístupnění plánu všem uživatelům. Jakmile je plán publikován jako viditelný pro všechny, musí zůstat viditelný pro všechny. (Plán nelze znovu nakonfigurovat jako soukromý plán).

- **Omezená cílová skupina (ID klienta)**

Přiřaďte cílovou skupinu, která bude mít přístup k tomuto soukromému plánu. Přístup je přiřazen pomocí ID klienta s možností zahrnout popis každého přiřazeného ID klienta. Při importu souboru tabulky CSV lze přidat maximálně 10 ID klienta nebo 20 000 ID klienta zákazníků.

Tenant je reprezentace organizace s ID reprezentované jako identifikátor GUID (globálně jedinečný identifikátor, 128bitové celé číslo používané k identifikaci prostředků). Jedná se o vyhrazenou instanci Azure AD, kterou organizace nebo vývojář aplikace obdrží, když organizace nebo vývojář aplikace vytvoří vztah s Microsoftem, například při registraci do Azure, Microsoft Intune nebo Microsoftu 365. Každý klient Azure AD se odlišuje a je oddělený od ostatních klientů Azure AD. Pokud chcete zkontrolovat, jestli máte tenanta, přihlaste se k webu Azure Portal pomocí účtu, který chcete použít ke správě vaší aplikace. Pokud tenanta máte, budete k němu automaticky přihlášeni a název tenanta se zobrazí přímo pod názvem vašeho účtu. Najeďte myší na název vašeho účtu v pravém horním rohu webu Azure Portal, zobrazí se vaše jméno, e-mail, ID adresáře nebo tenanta (GUID) a vaše doména. Pokud je k vašemu účtu přidruženo více tenantů, můžete výběrem názvu vašeho účtu otevřít nabídku, ve které můžete mezi jednotlivými tenanty přepínat. Každý tenant má vlastní ID tenanta. ID klienta vaší organizace můžete také vyhledat pomocí adresy [https://www.whatismytenantid.com](https://www.whatismytenantid.com)URL názvu domény na adrese: .

Zatímco SaaS nabízí použití ID klienta k definování soukromé cílové skupiny, jiné typy nabídek můžete použít ID předplatného Azure (které jsou také reprezentovány jako GUID).

> [!NOTE]
> Soukromý okruh uživatelů (nebo omezený okruh uživatelů) se liší od předběžného okruhu uživatelů. Na kartě **[Náhled](#preview)** můžete definovat cílovou skupinu náhledů. Před zveřejněním nabídky živě *na* tržišti je přístup k vaší nabídce ve verzi Preview. Zatímco označení soukromého publika se vztahuje pouze na konkrétní plán, publikum ve verzi Preview může zobrazit všechny plány (soukromé nebo ne), ale pouze během omezeného období náhledu, zatímco je plán testován a ověřen.

## <a name="example-list-of-plans-within-a-marketplace-offer"></a>Příklad seznamu plánů v rámci nabídky tržiště

![Příklad výpisu plánu tržiště s poznámkami](./media/marketplace-plan.svg)

## <a name="cloud-solution-provider-csp-reseller-audience"></a>Okruh uživatelů prodejce pro poskytovatele cloudových řešení (CSP)

Volba zpřístupnit vaši nabídku v programu CSP umožňuje poskytovatelům cloudových řešení prodávat váš produkt jako součást přibaleného řešení svým zákazníkům. Další informace naleznete v [tématu Zprostředkovatelé cloudových řešení](https://go.microsoft.com/fwlink/?linkid=2111109).

## <a name="publish"></a>Publikování

Po dokončení všech požadovaných částí nabídky vyberte **publikovat** v pravém horním rohu portálu. Budete přesměrováni na stránku **Revize a publikování.**

### <a name="submit-offer-to-preview"></a>Odeslat nabídku do náhledu

Pokud tuto nabídku publikujete poprvé, můžete:

- Podívejte se na stav dokončení pro každou část nabídky.
    - *Nezahájeno* - znamená, že sekce nebyla dotčena a musí být dokončena.
    - *Neúplné* - znamená, že sekce obsahuje chyby, které je třeba opravit nebo vyžadují poskytnutí dalších informací. Budete se muset vrátit do sekce a aktualizovat ji.
    - *Dokončeno* - znamená, že sekce je kompletní, všechny požadované údaje byly poskytnuty a nejsou žádné chyby. Všechny části nabídky musí být před odesláním nabídky v úplném stavu.
- Poskytněte certifikačnímu týmu pokyny k testování, abyste zajistili, že vaše aplikace bude správně testována, a navíc ke všem doplňkovým poznámkám užitečným pro pochopení vaší aplikace.
- Odešlete nabídku k publikování výběrem **možnosti Odeslat**. Pošleme vám e-mail s upozorněním, kdy je k dispozici předběžná verze nabídky, abyste ji mohli zkontrolovat a schválit. Musíte se vrátit do Partnerského centra a vybrat **Go-live** pro nabídku, abyste zveřejnili svou nabídku veřejnosti (nebo pokud soukromou nabídku, soukromému publiku).

## <a name="next-steps"></a>Další kroky

- [Aktualizace stávající nabídky na komerčním marketplace](./update-existing-offer.md)
