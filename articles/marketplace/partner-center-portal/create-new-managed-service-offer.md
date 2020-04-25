---
title: Vytvoření nové nabídky spravované služby na komerčním webu Marketplace
description: Postup vytvoření nové nabídky spravované služby pro výpis v Azure Marketplace pomocí portálu komerčního tržiště v partnerském centru.
author: JnHs
ms.author: jenhayes
manager: evansma
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/08/2020
ms.openlocfilehash: c82c3e0b6e6d11ccc33bf8556b06aeb04a6d37b5
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/24/2020
ms.locfileid: "82147914"
---
# <a name="create-a-new-managed-service-offer"></a>Vytvořit novou nabídku spravované služby

> [!IMPORTANT]
> Přesouváme správu nabídek spravované služby z portál partnerů cloudu do partnerského centra. Dokud se vaše nabídky nemigrují, postupujte podle pokynů v tématu [publikování spravované služby a Azure Marketplace](../../lighthouse/how-to/publish-managed-services-offers.md) ke správě nabídek v portál partnerů cloudu.

Spravovaná služba nabízí podporu pro [Lighthouse scénáře Azure](../../lighthouse/overview.md) . Když zákazník přijme nabídku spravované služby, může se připojit k prostředkům pro [správu delegovaných prostředků Azure](../../lighthouse/concepts/azure-delegated-resource-management.md).

Pokud chcete začít vytvářet nabídky spravovaných služeb, ujistěte se, že jste nejdřív [vytvořili účet partnerského centra](./create-account.md) a otevřete [řídicí panel komerčního tržiště](https://partner.microsoft.com/dashboard/commercial-marketplace/offers), kde je vybraná stránka s **přehledem** . Abyste mohli publikovat nabídku spravované služby, musíte mít [úroveň kompetence cloudové platformy stříbrné nebo Gold](https://partner.microsoft.com/membership/cloud-platform-competency) nebo být [odborníkem na službu Azure expert MSP](https://partner.microsoft.com/membership/azure-expert-msp) .

![Řídicí panel komerčního tržiště v partnerském centru](./media/new-offer-overview.png)

>[!Note]
> Po publikování nabídky se úpravy nabídky provedené v partnerském centru aktualizují jenom v systému a po opětovném publikování se uloží i přední. Po provedení změn Prosím zajistěte odeslání nabídky k publikování.

## <a name="create-a-new-offer"></a>Vytvoření nové nabídky

Vyberte tlačítko **+ Nová nabídka** a pak vyberte položku nabídky **spravovaná služba** . Zobrazí se dialogové okno **Nová nabídka** .

### <a name="offer-id-and-alias"></a>ID nabídky a alias

- **ID nabídky**: jedinečný identifikátor pro každou nabídku ve vašem účtu. Toto ID se bude zobrazovat zákazníkům v adrese URL nabídky Marketplace. Toto ID může obsahovat jenom malé alfanumerické znaky (včetně spojovníků a podtržítka, ale bez mezer), omezené na 50 znaků a po výběru **vytvořit**se nedá změnit.  Pokud například zadáte *příkaz Test-Offer-1* , adresa URL nabídky bude `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`.

- **Alias nabídky**: název, který se používá k odkazování na nabídku v partnerském centru. Tento název se na webu Marketplace nepoužívá a liší se od názvu nabídky a dalších hodnot, které se zákazníkům zobrazí. Tuto hodnotu nelze změnit po výběru možnost **vytvořit**.

Jakmile zadáte **ID nabídky** a **alias nabídky**, vyberte **vytvořit**. Pak budete moci pracovat na všech různých částech vaší nabídky.

## <a name="offer-setup"></a>Nastavení nabídky

Na stránce **nastavení nabídky** se zobrazí výzva k zadání následujících informací. Nezapomeňte po dokončení těchto polí vybrat **Uložit** .

## <a name="connect-lead-management"></a>Připojit řízení zájemců

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

Všimněte si, že pro [zásady certifikace spravovaných služeb](https://docs.microsoft.com/legal/marketplace/certification-policies#700-managed-services)se vyžaduje **cíl zájemce** . Tím se v systému CRM vytvoří záznam pokaždé, když zákazník nasadí vaši nabídku.

Další informace najdete v tématu [Přehled správy potenciálních zákazníků](./commercial-marketplace-get-customer-leads.md).

Před přechodem k další části nezapomeňte stránku **Uložit** .

## <a name="properties"></a>Vlastnosti

Stránka **vlastnosti** umožňuje definovat kategorie používané k seskupení vaší nabídky na webu Marketplace a právní smlouvy, které podporují vaši nabídku. Po dokončení této stránky vyberte **Uložit** .

### <a name="category"></a>Kategorie

Vyberte minimálně jednu a maximálně pět kategorií, které budou použity k umístění vaší nabídky do příslušných oblastí hledání na webu Marketplace. Nezapomeňte si vyžádat, jak vaše nabídka podporuje tyto kategorie v popisu nabídky.

### <a name="terms-and-conditions"></a>podmínky a ujednání

V poli **podmínky a ujednání** zadejte vlastní právní podmínky. Můžete taky zadat adresu URL, kde se můžou vaše podmínky a ujednání najít. Zákazníci budou muset tyto podmínky přijmout, aby mohli svoji nabídku vyzkoušet.

## <a name="offer-listing"></a>Seznam nabídek

Stránka **seznam nabídek** vám umožní definovat podrobnosti o Marketplace (název nabídky, popis, obrázky atd.) pro vaši nabídku.

> [!NOTE]
> Obsah seznamu nabídek (například popis, dokumenty, snímky obrazovky, podmínky použití atd.) nemusí být v angličtině, pokud popis nabídky začíná frází "Tato aplikace je k dispozici pouze v [jazykovém jazyce, který není v angličtině]." Je také přijatelné poskytnout *užitečnou adresu URL pro odkaz* k poskytnutí obsahu v jiném jazyce, než který je použit v obsahu nabídky.

### <a name="name"></a>Název

Název, který sem zadáte, se zákazníkům zobrazí jako název seznamu nabídek. Toto pole se předem vyplní textem, který jste zadali jako **alias nabídky** při vytváření nabídky, ale tuto hodnotu můžete změnit. Tento název může být ochranných známek (a může obsahovat symboly ochranných známek a autorských práv). Název nesmí být delší než 50 znaků a nesmí obsahovat žádné emoji.

### <a name="search-results-summary"></a>Souhrn výsledků hledání

Zadejte krátký popis vaší nabídky (až 100 znaků), který se dá použít ve výsledcích hledání na webu Marketplace.

### <a name="long-summary"></a>Dlouhý souhrn

Zadejte delší popis vaší nabídky (až 256 znaků). Toto dlouhé Shrnutí se dá použít taky ve výsledcích hledání na webu Marketplace.

### <a name="description"></a>Popis

Zadejte delší popis vaší nabídky (až 3 000 znaků). Tento popis se zobrazí zákazníkům v přehledu výpisu na webu Marketplace. Zahrňte do své nabídky svou polohu, klíčové výhody, kategorii nebo přidružení oboru, možnosti nákupu v aplikaci a veškeré požadované informace.

Pár tipů pro psaní popisu:  

- Jasně popište, jak se má v prvních několika větách popisu v této nabídce vyhodnotit. Zahrňte do svého umístění hodnoty tyto položky:
  - Popis nabídky
  - Typ uživatele, který je výhodou nabídky
  - Potřeby zákazníků nebo bolesti, že adresa nabídky
- Mějte na paměti, že ve výsledcích vyhledávacího modulu se může zobrazit několik prvních vět.  
- Nespoléhá se na funkce a funkce k prodeji vašeho produktu. Místo toho se zaměřte na hodnotu, kterou doručujete.  
- Používejte co nejvíc slovníků specifických pro konkrétní obor nebo využití s výhodami.

Chcete-li podrobnější popis nabídky, použijte Editor formátovaného textu k formátování popisu.

![Použití editoru textu ve formátu RTF](./media/text-editor2.png)

Pomocí následujících pokynů můžete použít Editor formátovaného textu:

- Chcete-li změnit formát obsahu, zvýrazněte text, který chcete formátovat, a vyberte styl textu, jak je znázorněno níže:

     ![Změna formátu textu pomocí editoru formátovaného textu](./media/text-editor3.png)

- Chcete-li do textu přidat seznam s odrážkami nebo číslovaného seznamu, použijte následující možnosti:

     ![Přidávání seznamů pomocí editoru formátovaného textu](./media/text-editor4.png)

- Chcete-li přidat nebo odebrat odsazení textu, použijte následující možnosti:

     ![Použití editoru formátovaného textu k odsazení](./media/text-editor5.png)

### <a name="privacy-policy-link"></a>Odkaz zásady ochrany osobních údajů

Zadejte adresu URL zásad ochrany osobních údajů vaší organizace (hostované na vašem webu). Zodpovídáte za to, že vaše aplikace bude v souladu se zákony a předpisy ochrany osobních údajů, a za poskytnutí platných zásad ochrany osobních údajů.

### <a name="useful-links"></a>Užitečné odkazy

Poskytněte volitelné dodatečné online dokumenty k vašemu řešení. Přidejte další užitečné odkazy kliknutím na **+ Přidat odkaz**.

### <a name="contact-information"></a>Kontaktní informace

V této části musíte zadat jméno, e-mail a telefonní číslo pro **kontakt podpory** a **technický kontakt**. Tyto informace se zákazníkům nezobrazují, ale budou k dispozici pro společnost Microsoft a mohou být poskytovány partnerům CSP.

### <a name="support-urls"></a>Adresy URL podpory

Pokud máte weby podpory pro **globální zákazníky Azure** a/nebo **Azure Government zákazníky**, zadejte tyto adresy URL sem.

### <a name="marketplace-images"></a>Image Marketplace

V této části můžete zadat loga a obrázky, které se použijí při zobrazení vaší nabídky zákazníkovi. Všechny obrázky musí být ve formátu. png.

>[!Note]
>Pokud máte problém s nahráváním souborů, ujistěte se, že vaše místní síť neblokuje https://upload.xboxlive.com službu používanou partnerským centrem.

#### <a name="marketplace-logos"></a>Loga Marketplace

Vyžadují se čtyři velikosti loga: **malá (40x40)**, **střední (90x90)**, **Velká (115x115)** a **roztažitelné (255x115)**. Postupujte podle těchto pokynů pro vaše loga:

- Design Azure má jednoduchou paletu barev. Omezte počet primárních a sekundárních barev ve vašem logu.
- Barvami motivů na portálu jsou bílá a černá. Nepoužívejte tyto barvy jako barvu pozadí pro vaše logo. Použijte barvu, která vaše logo na portálu zvýrazní. Doporučujeme použít jednoduché primární barvy.
- Pokud použijete průhledné pozadí, ujistěte se, že text ani logo nejsou bílé, černé nebo modré.
- Logo by mělo působit plochým dojmem a nemělo by obsahovat barevné přechody. Nepoužívejte v logu pozadí s barevným přechodem.
- Na logo neumisťujte žádný text, a to ani název vaší společnosti nebo název značky.
- Zajistěte, aby logo nebylo roztažené.

#### <a name="screenshots"></a>Snímky obrazovek

Přidejte až pět snímků obrazovky, které ukazují, jak vaše nabídka funguje. Všechny snímky obrazovky musí být 1280 × 720 pixelů.

#### <a name="videos"></a>Videa

Volitelně můžete přidat až pět videí, která předvádí vaši nabídku. Tato videa by se měla hostovat na YouTube nebo Vimeo. U každého z nich zadejte název videa, jeho adresu URL a miniaturu obrázku (1280 x 720 pixelů).

#### <a name="additional-marketplace-listing-resources"></a>Další prostředky pro výpis Marketplace

- [Osvědčené postupy pro výpisy nabídky na webu Marketplace](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

## <a name="preview"></a>Preview

Před publikováním vaší nabídky v širší nabídce na webu Marketplace je nejprve nutné ji zpřístupnit pro skupinu s omezeným počtem verzí Preview. To vám umožní potvrdit, jak se nabídka zobrazuje v Azure Marketplace před tím, než ji zpřístupní zákazníkům. V této verzi Preview budou moci vaše nabídka Zobrazit i technické týmy společnosti Microsoft.

Cílovou skupinu verze Preview můžete definovat zadáním ID předplatných Azure v části **Preview pro cílovou skupinu** . Můžete zadat až 10 ID předplatných ručně nebo nahrát soubor. csv s až 100 ID předplatných.

Zákazníci, kteří jsou přidruženi k těmto předplatným, budou moci zobrazit nabídku v Azure Marketplace ještě předtím, než budou živá. Nezapomeňte sem zahrnout vlastní odběry, abyste mohli zobrazit náhled vaší nabídky.

## <a name="plan-overview"></a>Přehled plánu

Každá nabídka musí mít jeden nebo více plánů (někdy označovaných jako SKU). Můžete přidat více plánů pro podporu různých sad funkcí v různých cenách nebo upravit konkrétní plán pro omezené cílové skupiny konkrétních zákazníků. Zákazníci si můžou prohlédnout plány, které jsou pro ně k dispozici v rámci nadřízené nabídky.

Na stránce **Přehled plánu** vyberte **+ vytvořit nový plán**. Pak zadejte **ID plánu** a **název plánu**. Obě tyto hodnoty můžou obsahovat jenom malé alfanumerické znaky, pomlčky a podtržítka, maximálně 50 znaků. Tyto hodnoty můžou zákazníci zobrazovat a po publikování této nabídky je nepůjde změnit.

Po zadání těchto hodnot vyberte **vytvořit** , abyste mohli pokračovat v práci na plánu. Existují tři oddíly, které je potřeba dokončit: **Výpis plánu**, **ceny a dostupnost**a **Technická konfigurace**.

### <a name="plan-listing"></a>Výpis plánu

Nejdřív zadejte **Shrnutí výsledků hledání** pro daný plán. Toto je krátký popis vašeho plánu (až 100 znaků), který se dá použít ve výsledcích hledání na webu Marketplace.

Potom zadejte **Popis** , který poskytuje podrobnější vysvětlení plánu.

### <a name="pricing-and-availability"></a>Ceny a dostupnost

V současné době je k dispozici pouze jeden model cenového modelu, který lze použít pro nabídku spravované služby: **Přineste si vlastní licenci (BYOL)**. To znamená, že vaše zákazníky budete fakturovat přímo za náklady související s touto nabídkou a Microsoft vám nebude účtovat žádné poplatky.

V části **plán – přehled** můžete určit, jestli má být tento plán [soukromý](../../marketplace/private-offers.md). Pokud necháte toto políčko **privátního plánu** nezaškrtnuté, váš plán nebude omezen na konkrétní zákazníky (nebo na určitý počet zákazníků).

Pokud chcete, aby byl tento plán dostupný jenom pro konkrétní zákazníky, vyberte **Ano**. Když to uděláte, budete muset zákazníky identifikovat tak, že zadáte jejich ID předplatného. Můžete je zadat jednou (až pro 10 odběry) nebo nahráním souboru. CSV (maximálně 10 000 odběrů ve všech plánech). Nezapomeňte sem zahrnout vlastní odběry, abyste mohli otestovat a ověřit nabídku.

> [!IMPORTANT]
> Jakmile je plán publikovaný jako veřejný, nemůžete ho změnit na privátní. Chcete-li určit, kteří zákazníci mohou přijmout vaši nabídku a delegovat prostředky, použijte privátní plán. S veřejným plánem nemůžete omezit dostupnost na konkrétní zákazníky nebo dokonce na určitý počet zákazníků (i když se rozhodnete, že můžete plán úplně zastavit). [Přístup k delegování můžete odebrat](../../lighthouse/how-to/remove-delegation.md) poté, co zákazník přijme nabídku, pouze pokud jste při publikování této nabídky zazahrnuli **autorizaci** s **definicí role** nastavenou na možnost [přiřazení registrace spravovaných služeb](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) . Můžete se také obrátit na zákazníka a požádat ho, aby [Váš přístup odebral](../../lighthouse/how-to/view-manage-service-providers.md#add-or-remove-service-provider-offers).

### <a name="technical-configuration"></a>Technická konfigurace

V této části plánu se vytvoří manifest s autorizačními informacemi pro správu zákaznických prostředků. Tyto informace jsou nutné, aby bylo možné povolit [správu delegovaných prostředků Azure](../../lighthouse/concepts/azure-delegated-resource-management.md).

Nezapomeňte si projít [klienty, role a uživatele ve scénářích Azure Lighthouse](../../lighthouse/concepts/tenants-users-roles.md#best-practices-for-defining-users-and-roles) , abyste zjistili, které role jsou podporované, a osvědčené postupy pro definování autorizací.

> [!NOTE]
> Jak je uvedeno výše, uživatelé a role v **autorizačních** položkách budou platit pro každého zákazníka, který plán koupí. Pokud chcete omezit přístup na konkrétního zákazníka, budete muset publikovat privátní plán pro výhradní použití.

#### <a name="manifest"></a>Manifest

Nejprve zadejte **verzi** manifestu. Použijte formát *n. n. n* (například 1.2.5).

Potom zadejte **ID tenanta**. Toto je identifikátor GUID, který je přidružený k ID tenanta Azure Active Directory (Azure AD) vaší organizace. To znamená, že se jedná o tenanta správy, ze kterého budete přistupovat k prostředkům vašich zákazníků. Pokud tyto možnosti nemáte k dispozici, můžete ji najít přesunutím ukazatele myši na název účtu v pravém horním rohu Azure Portal, nebo výběrem **přepínače Adresář**.

Pokud publikujete novou verzi nabídky a potřebujete vytvořit aktualizovaný manifest, vyberte **+ nový manifest**. Ujistěte se, že jste nazvětšili číslo verze z předchozí verze manifestu.

#### <a name="authorization"></a>Autorizace

Autorizace definují entity ve vašem spravovaném tenantovi, kteří mají přístup k prostředkům a předplatným pro zákazníky, kteří si plán kupují. Každá z těchto entit má přiřazenou předdefinovanou roli, která uděluje konkrétní úroveň přístupu.

Pro každý plán můžete vytvořit až dvacet autorizací.

> [!TIP]
> Ve většině případů budete chtít přiřadit role k skupině uživatelů nebo instančnímu objektu služby Azure AD, a ne k řadě jednotlivých uživatelských účtů. To vám umožní přidat nebo odebrat přístup pro jednotlivé uživatele bez nutnosti aktualizace a opětovného publikování plánu, když se změní vaše požadavky na přístup. Při přiřazování rolí do skupin Azure AD [se ujistěte, že **typ skupiny** je **zabezpečení** a ne **Sada Office 365**](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). Další doporučení najdete v tématu [klienti, role a uživatelé ve scénářích Azure Lighthouse](../../lighthouse/concepts/tenants-users-roles.md).

Pro každou **autorizaci**musíte zadat následující. Pak můžete vybrat **+ Přidat autorizaci** tolikrát, kolikrát je potřeba, a přidat další uživatele a definice rolí.

- **ID objektu Azure AD**: identifikátor Azure AD uživatele, skupiny uživatelů nebo aplikace, kterým budou k prostředkům vašich zákazníků udělena určitá oprávnění (definovaná definicí role).
- **Zobrazovaný název objektu Azure AD**: popisný název, který zákazníkovi pomůže pochopit účel této autorizace. Zákazník uvidí tento název při delegování prostředků.
- **Definice role**: v seznamu vyberte jednu z dostupných předdefinovaných rolí Azure AD. Tato role určuje oprávnění, která bude mít uživatel v poli **ID objektu Azure AD** v materiálech vašich zákazníků. Popisy těchto rolí najdete v tématu [předdefinované role](../../role-based-access-control/built-in-roles.md) a [Podpora rolí pro správu delegovaných prostředků Azure](../../lighthouse/concepts/tenants-users-roles.md#role-support-for-azure-delegated-resource-management).
  > [!NOTE]
  > V případě, že jsou příslušné nové předdefinované role přidány do Azure, budou k dispozici zde, i když může dojít ke zpoždění před jejich zobrazením.
- **Přiřaditelné role**: Tato možnost se zobrazí jenom v případě, že jste pro tuto autorizaci vybrali správce přístupu uživatele v **definici role** . Pokud ano, musíte sem přidat jednu nebo více rolí, které lze přiřadit. Uživatel v poli **ID objektu Azure AD** bude moct přiřadit tyto role ke [spravovaným identitám](../../active-directory/managed-identities-azure-resources/overview.md), které se vyžadují k [nasazení zásad, které se dají opravit](../../lighthouse/how-to/deploy-policy-remediation.md). Pro tohoto uživatele se nepoužijí žádná jiná oprávnění, která se běžně nevztahují k roli správce přístupu uživatele.

> [!TIP]
> Aby bylo možné v případě potřeby [Odebrat přístup k delegování](../../lighthouse/how-to/remove-delegation.md) , zahrňte **autorizaci** s **definicí role** nastavenou na [přiřazení registrace spravovaných služeb odstranit roli](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role). Pokud tato role není přiřazená, delegované prostředky může odebrat jenom uživatel v tenantovi zákazníka.

Až dokončíte všechny oddíly pro svůj plán, můžete vybrat **+ vytvořit nový plán** tolikrát, kolik potřebujete k vytváření dalších plánů. Jakmile budete mít hotovo, vyberte **Uložit**.

## <a name="publish"></a>Publikování

### <a name="submit-offer-to-preview"></a>Odeslat nabídku do verze Preview

Až dokončíte všechny požadované části nabídky, vyberte **publikovat** v pravém horním rohu portálu. Budete přesměrováni na stránku **Revize a publikování** .

Pokud tuto nabídku publikujete poprvé, můžete:

- Podívejte se na téma stav dokončení pro jednotlivé části nabídky.
  - *Nespuštěno* – znamená, že se oddíl nedotkl a je potřeba ho dokončit.
  - *Nedokončeno* – znamená, že oddíl obsahuje chyby, které je potřeba opravit, nebo vyžaduje, aby se poskytly další informace. Vraťte se k oddílům a aktualizujte je.
  - *Complete* – znamená, že část je dokončena, byla zadána všechna požadovaná data a nejsou k dispozici žádné chyby. Aby bylo možné nabídku odeslat, musí být všechny oddíly nabídky v úplném stavu.
- V části **poznámky k certifikaci** poskytněte certifikačnímu týmu pokyny k testování, aby se zajistilo správné testování vaší aplikace, a to spolu s případnými doplňkovými poznámkami, které jsou užitečné pro porozumění vaší aplikaci.
- Kliknutím na **Odeslat**nabídku pro publikování odešlete. Až budete mít k dispozici verzi Preview, pošleme vám e-mail, který vám pomůže zkontrolovat a schválit. Vraťte se do partnerského centra a výběrem možnosti **Přejít do živé** nabídky publikujte vaši nabídku na veřejném (nebo v případě soukromé nabídky pro soukromou cílovou skupinu).

### <a name="customer-experience-and-offer-management"></a>Prostředí pro zákazníky a Správa nabídek

Když zákazník nasadí vaši nabídku, bude moct delegovat předplatná nebo skupiny prostředků pro [správu delegovaných prostředků Azure](../../lighthouse/concepts/azure-delegated-resource-management.md). Další informace o tomto procesu najdete v tématu věnovaném [procesu připojování zákazníků](../../lighthouse/how-to/publish-managed-services-offers.md#the-customer-onboarding-process).

[Aktualizovanou verzi vaší nabídky můžete publikovat](update-existing-offer.md) kdykoli. Například můžete chtít přidat novou definici role do dříve publikované nabídky. V takovém případě se zákazníkům, kteří si tuto nabídku už přidali, na Azure Portal stránce [**poskytovatelé služeb**](../../lighthouse/how-to/view-manage-service-providers.md) zobrazí ikona s informacemi o tom, že je k dispozici aktualizace. Každý zákazník bude moci zkontrolovat změny a rozhodnout, jestli chtějí aktualizovat na novou verzi.

## <a name="next-steps"></a>Další kroky

- [Aktualizace stávající nabídky na komerčním marketplace](./update-existing-offer.md)
- [Další informace o Azure Lighthouse](../../lighthouse/overview.md)