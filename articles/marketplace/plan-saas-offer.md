---
title: Postup plánování nabídky SaaS pro komerční tržiště Microsoftu
description: Postup plánování nové nabídky software jako služba (SaaS) pro výpis nebo prodej v Microsoft AppSource, Azure Marketplace nebo prostřednictvím programu Cloud Solution Provider (CSP) pomocí programu komerčního řešení pro web Marketplace v partnerském centru Microsoftu.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/30/2020
ms.openlocfilehash: e24e1afa0116bc1f240bddef47783b06f4f800d2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104581299"
---
# <a name="how-to-plan-a-saas-offer-for-the-commercial-marketplace"></a>Postup plánování nabídky SaaS pro komerční tržiště

Tento článek popisuje různé možnosti a požadavky pro publikování nabídek SaaS (software jako služba) na komerčním webu Microsoft Marketplace. SaaS nabízí vašim zákazníkům doručovat a licencovat softwarová řešení prostřednictvím online předplatných. Jako vydavatel SaaS spravujete a platíte za infrastrukturu potřebnou k podpoře používání vaší nabídky vašimi zákazníky. Tento článek vám pomůže připravit vaši nabídku publikování na komerční tržišti s partnerským centrem.

## <a name="listing-options"></a>Možnosti zápisu

Když připravujete publikování nové nabídky SaaS, musíte se rozhodnout, kterou možnost _seznamu_ zvolit. Zvolená možnost výpisu určuje, které další informace budete potřebovat při vytváření vaší nabídky v partnerském centru. Možnost výpisu budete definovat na stránce  **nastavení nabídky** , jak je vysvětleno v tématu [Vytvoření nabídky SaaS na komerčním webu Marketplace](create-new-saas-offer.md).

Následující tabulka ukazuje možnosti výpisu SaaS nabídek na komerčním webu Marketplace.

| Možnost výpisu | Proces transakce |
| ------------ | ------------- |
| Kontaktujte mě | Zákazník kontaktuje přímo z informací v seznamu.``*`` |
| Bezplatná zkušební verze | Zákazník se přesměruje na cílovou adresu URL prostřednictvím Azure Active Directory (Azure AD).``*`` |
| Získat hned (zdarma) | Zákazník se přesměruje na vaši cílovou adresu URL přes Azure AD.``*`` |
| Prodej prostřednictvím Microsoftu  | Nabídky prodávané prostřednictvím Microsoftu se nazývají _transakční_ nabídky. Nabídka, která je transakční, je jedním z nich, kde Microsoft usnadňuje výměnu peněz za softwarovou licenci jménem vydavatele. Účtujeme vám SaaS s využitím cenového modelu, který zvolíte, a spravujete transakce zákazníků vaším jménem. Poplatky za využití infrastruktury Azure se účtují na vás a na partnera přímo. V cenovém modelu byste měli zohlednit náklady na infrastrukturu. Tato informace je podrobněji vysvětlena v tématu [SaaS fakturace](#saas-billing) níže.  |
|||

``*`` Vydavatelé jsou odpovědni za podporu všech aspektů licenčních transakcí softwaru, mimo jiné objednávky, plnění, měření, fakturace, fakturace, platby a shromažďování.

Další informace o těchto možnostech tohoto výpisu najdete v tématu možnosti Transact-The pro [prodej na webu Marketplace](marketplace-commercial-transaction-capabilities-and-considerations.md).

Po publikování vaší nabídky se možnost výpisu, kterou jste zvolili pro vaši nabídku, zobrazí jako tlačítko v levém horním rohu stránky se seznamem vaší nabídky. Například následující snímek obrazovky ukazuje stránku se seznamem nabídek v Azure Marketplace s tlačítky **kontaktní mi** a **testovacích jednotek** .

![Znázorňuje seznam nabídek v online obchodu.](./media/listing-options.png)

## <a name="technical-requirements"></a>Technické požadavky

Technické požadavky se liší v závislosti na možnosti výpisu, kterou zvolíte pro vaši nabídku.

Možnost seznam _kontaktů_ má žádné technické požadavky. Máte možnost připojit systém pro řízení vztahů se zákazníky (CRM) a spravovat potenciální zákazníky. Tento postup je popsaný v části [Customers Customers (zákazníci](#customer-leads) ) dále v tomto článku.

Možnosti _získat nyní (zdarma)_, _bezplatná zkušební verze_ a _Prodej prostřednictvím_ možností výpisu Microsoftu mají následující technické požadavky:

- Vaše aplikace SaaS musí být víceklientské řešení.
- Pro ověřování uživatelů můžete povolit jak účty Microsoft (MSA), tak [Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) .
- Je nutné vytvořit cílovou stránku. Po zakoupení vaší nabídky si uživatel přesměruje na cílovou stránku. To jim pomůže dokončit jakékoli dodatečné zřizování nebo nastavení, které je potřeba. Pokyny k vytvoření cílové stránky najdete v těchto článcích:
  - [Sestavení cílové stránky pro SaaS nabídku s podporou transakcí na komerčním webu Marketplace](azure-ad-transactable-saas-landing-page.md)
  - [Sestavení cílové stránky bezplatné nebo zkušební SaaS nabídky na komerčním webu Marketplace](azure-ad-free-or-trial-landing-page.md)

Tyto další technické požadavky se vztahují jenom na možnost _Prodej prostřednictvím seznamu Microsoft_ (s podporou transakcí):

- Služba Azure AD s jednotným přihlašováním (SSO) a ověřováním identity se vyžaduje pro nákup uživatele, který přistupuje k cílové stránce. Podrobné pokyny najdete v tématu [Azure AD a SaaS nabídky na komerčním webu Marketplace](azure-ad-saas.md).
- K integraci s Azure Marketplace a Microsoft AppSource je nutné použít [rozhraní API pro splnění SaaS](./partner-center-portal/pc-saas-fulfillment-api-v2.md) . Aby bylo možné vytvořit, aktualizovat a odstranit uživatelský účet a plán služby, je nutné vystavit službu, která může komunikovat s předplatným SaaS. Kritické změny rozhraní API musí být podporované do 24 hodin. Pravidelně se vydávají nekritické změny rozhraní API. Diagramy a podrobná vysvětlení popisující použití shromážděných polí jsou k dispozici v dokumentaci pro [rozhraní API](./partner-center-portal/pc-saas-fulfillment-api-v2.md).
- Musíte vytvořit aspoň jeden plán pro vaši nabídku. Ceny vašeho plánu se účtují na základě cenového modelu, který jste vybrali před publikováním: _paušální sazba_ nebo _podle uživatele_. Další podrobnosti o [plánech](#plans) najdete dále v tomto článku.
- Zákazník může vaši nabídku kdykoli zrušit.

### <a name="technical-information"></a>Technické informace

Pokud vytváříte nabídku s podporou transakcí, bude nutné shromáždit následující informace pro stránku **technické konfigurace** . Pokud se rozhodnete zpracovávat transakce nezávisle namísto vytvoření nabídky s podporou transakcí, přeskočte tuto část a přejděte na [test Drives](#test-drives).

- **Adresa URL cílové stránky**: adresa URL webu SaaS (například: `https://contoso.com/signup` ), na kterou budou uživatelé přesměrováni po získání nabídky z komerčního tržiště, který aktivuje proces konfigurace z nově vytvořeného předplatného SaaS. Tato adresa URL obdrží token, který se dá použít k volání rozhraní API pro splnění, aby se získaly podrobné informace o zřízení vaší interaktivní registrační stránky.

  Tato adresa URL bude volána parametrem identifikačního tokenu nákupu na webu Marketplace, který jedinečně identifikuje SaaS nákup konkrétního zákazníka. Tento token musíte vyměňovat pro příslušné podrobnosti o předplatném SaaS pomocí [rozhraní Resolve API](./partner-center-portal/pc-saas-fulfillment-api-v2.md#resolve-a-purchased-subscription). Tyto podrobnosti a všechny ostatní, které chcete shromažďovat, by se měly používat jako součást interaktivní webové stránky zákazníka, která je založená na vašich zkušenostech k dokončení registrace zákazníků a aktivaci jejich nákupu. Na této stránce by se měl uživatel zaregistrovat přes ověřování jedním kliknutím pomocí Azure Active Directory (Azure AD).

  Tato adresa URL s parametrem identifikačního tokenu nákupu na webu Marketplace se taky zavolá, když zákazník spustí spravované prostředí SaaS z centra pro správu v Azure Portal nebo M365. Oba toky byste měli zpracovat: když se token poprvé zadá po nákupu nového zákazníka, a když se znovu zadá pro existujícího zákazníka, který spravuje své řešení SaaS.

    Cílová stránka, kterou nakonfigurujete, by měla být spuštěná a bude běžet 24/7. Toto je jediný způsob, jakým se dozvíte o nových nákupech nabídek SaaS provedených na komerčním tržišti nebo o požadavcích na konfiguraci aktivního předplatného nabídky.

- **Webhook připojení**: u všech asynchronních událostí, které Microsoft potřebuje poslat (například po zrušení předplatného SaaS), je nutné zadat adresu URL Webhooku připojení. Budeme zavolat tuto adresu URL a upozorníme vás na událost.

  Webhook, který zadáte, by měl být v provozu a musí běžet 24/7. Toto je jediný způsob, jakým budete dostávat oznámení o aktualizacích předplatných SaaS vašich zákazníků prostřednictvím komerčního tržiště.

  > [!NOTE]
  > V rámci Azure Portal vyžadujeme, abyste vytvořili aplikaci Azure Active Directory s jedním klientem [(Azure AD)](../active-directory/develop/howto-create-service-principal-portal.md) , která umožňuje použít jedno ID aplikace Azure k ověření připojení mezi našimi dvěma službami. [ID tenanta](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)zjistíte tak, že přejdete na Azure Active Directory a vyberete **vlastnosti** a vyhledáte uvedené číslo ID adresáře. Například, `50c464d3-4930-494c-963c-1e951d15360e`.

- **ID tenanta Azure Active Directory**: (označuje se taky jako ID adresáře). V Azure Portal vyžadujeme [registraci aplikace Azure Active Directory (AD)](../active-directory/develop/howto-create-service-principal-portal.md) , abychom ji mohli přidat do seznamu řízení přístupu (ACL) rozhraní API, abyste měli jistotu, že máte oprávnění k volání. Pokud chcete najít ID tenanta pro vaši aplikaci Azure Active Directory (AD), v Azure Active Directory klikněte na okno [Registrace aplikací](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) . Ve sloupci **Zobrazovaný název** vyberte aplikaci. Pak vyhledejte číslo **ID adresáře (tenant)** v seznamu (například `50c464d3-4930-494c-963c-1e951d15360e` ).

- **Azure Active Directory ID aplikace**: budete také potřebovat [ID aplikace](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in). Chcete-li získat jeho hodnotu, v Azure Active Directory vyberte okno [Registrace aplikací](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) . Ve sloupci **Zobrazovaný název** vyberte aplikaci. Pak vyhledejte číslo ID aplikace (klienta) uvedené (například `50c464d3-4930-494c-963c-1e951d15360e` ).

  ID aplikace Azure AD je přidruženo k vašemu ID vydavatele v účtu partnerského centra. Pro všechny nabídky tohoto účtu musíte použít stejné ID aplikace.

  > [!NOTE]
  > Pokud má Vydavatel v partnerském centru dva nebo více různých účtů, měla by se použít nejméně jedno jiné ID aplikace Azure AD, každé pro jeden z účtů. Každý partnerský účet v partnerském centru by měl používat jedinečné ID aplikace Azure AD pro všechny nabídky SaaS, které jsou publikovány prostřednictvím tohoto účtu.

## <a name="test-drives"></a>Testovací verze
Pro aplikaci SaaS se můžete rozhodnout pro povolení testovacích jednotek. Testovací jednotky poskytují zákazníkům přístup k předkonfigurovanému prostředí po dobu určitého počtu hodin. Můžete povolit testovací jednotky pro libovolnou možnost publikování, ale tato funkce má další požadavky. Další informace o testovacích jednotkách najdete v tématu [co je testovací jednotka?](what-is-test-drive.md). Informace o konfiguraci různých druhů testovacích jednotek najdete v tématu [Technická konfigurace testovacích](test-drive-technical-configuration.md)jednotek.

> [!TIP]
> Testovací jednotka se liší od [bezplatné zkušební verze](plans-pricing.md#free-trials). Můžete nabízet testovací, bezplatnou zkušební verzi nebo obojí. Poskytují zákazníkům vaše řešení po dobu určitou dobu. Testovací jednotka ale taky obsahuje praktickou a samoobslužnou prohlídku klíčových funkcí vašeho produktu a výhod, které se provedou ve scénáři implementace reálného světa.

## <a name="customer-leads"></a>Potenciální zákazníci

Abyste mohli shromažďovat informace o zákaznících, musíte vaši nabídku připojit k systému správy vztahů se zákazníky (CRM). Zákazník bude požádán o oprávnění ke sdílení svých informací. Tyto podrobnosti o zákaznících spolu s názvem nabídky, ID a online obchodem, kde si vaši nabídku našli, budou odeslány do systému CRM, který jste nakonfigurovali. Komerční tržiště podporuje nejrůznější systémy CRM, společně s možností použití tabulky Azure nebo konfigurace koncového bodu HTTPS pomocí Power automatizuje.

Připojení k CRM můžete kdykoli přidat nebo změnit během vytváření nabídky nebo po ní. Podrobné pokyny najdete v [zákaznických zákaznících z nabídky komerčních webů na webu Marketplace](partner-center-portal/commercial-marketplace-get-customer-leads.md).

## <a name="selecting-an-online-store"></a>Výběr online obchodu

Když publikujete nabídku SaaS, bude uvedena v Microsoft AppSource, Azure Marketplace nebo obou. Každý online obchod slouží jako jedinečné požadavky zákazníků. AppSource je pro obchodní řešení a Azure Marketplace pro IT řešení. Typ vaší nabídky, možnosti Transact a kategorie určují, kde bude vaše nabídka publikovaná. Kategorie a podkategorie jsou namapovány na každý online obchod na základě typu řešení. 

Pokud vaše nabídka SaaS je řešení IT (Azure Marketplace *) i obchodní* řešení (AppSource), vyberte kategorii a podkategorii pro každý online obchod. Nabídky publikované na obou obchodech online by měly mít jako řešení IT *a* obchodní řešení hodnotu Proposition.

> [!IMPORTANT]
> Nabídky SaaS s [měřenou fakturací](partner-center-portal/saas-metered-billing.md) jsou k dispozici prostřednictvím Azure Marketplace a Azure Portal. SaaS nabízí pouze soukromé plány, které jsou k dispozici prostřednictvím Azure Portal.

| Měřené fakturace | Veřejný plán | Soukromý plán | K dispozici v: |
|---|---|---|---|
| Yes             | Yes         | No           | Azure Marketplace a Azure Portal |
| Yes             | Yes         | Yes          | Azure Marketplace a Azure Portal * |
| Yes             | No          | Yes          | Pouze Azure Portal |
| No              | No          | Yes          | Pouze Azure Portal |
|||||

&#42; soukromý plán této nabídky bude k dispozici pouze prostřednictvím Azure Portal

Zákazníci v Azure Portal zakoupí například nabídku s měřením fakturace a soukromým plánem (žádný veřejný plán). Přečtěte si další informace o [privátních nabídkách na komerčním webu Microsoft Marketplace](private-offers.md).

Podrobné informace o možnostech výpisu podporovaných online obchody najdete v tématu [Možnosti výpisu a cen podle online obchodu](determine-your-listing-type.md#listing-and-pricing-options-by-online-store). Další informace o kategoriích a podkategoriích najdete v tématu [kategorie a podkategorie na komerčním webu Marketplace](categories.md).

## <a name="legal-contracts"></a>Právní smlouvy

Pro zjednodušení procesu nákupu pro zákazníky a omezení právní složitosti pro dodavatele softwaru nabízí společnost Microsoft standardní kontrakt, který můžete použít pro vaše nabídky na komerčním webu Marketplace. Když nabízený software obdržíte na základě standardního kontraktu, zákazníci ho musí přečíst a přijmout jenom jednou a vy nemusíte vytvářet vlastní podmínky.

Pokud se rozhodnete použít standardní smlouvu, máte možnost přidat do standardní smlouvy univerzální změny a až 10 vlastních změn. Místo standardní smlouvy můžete použít také vlastní podmínky a ujednání. Tyto podrobnosti budete spravovat na stránce **vlastnosti** . Podrobné informace najdete v tématu [standardní smlouva pro komerční tržiště Microsoftu](standard-contract.md).

> [!NOTE]
> Po publikování nabídky pomocí standardní smlouvy pro komerční web Marketplace nemůžete použít vlastní podmínky a ujednání. Jedná se o scénář "nebo". Vaše řešení si buď nabídnete v rámci standardní smlouvy, nebo podle vašich vlastních podmínek. Pokud chcete upravit podmínek standardní smlouvy, můžete to udělat prostřednictvím standardních změn smlouvy.

## <a name="offer-listing-details"></a>Podrobnosti o seznamu nabídek

Když [vytvoříte novou nabídku SaaS](create-new-saas-offer.md) v partnerském centru, na stránce se **seznamem nabídek** zadáte text, obrázky, volitelná videa a další podrobnosti. Toto jsou informace, které se zákazníkům zobrazí při zjištění seznamu nabídek na komerčním webu Marketplace, jak je znázorněno v následujícím příkladu.

:::image type="content" source="./media/example-saas-1.png" alt-text="Ukazuje, jak se tato nabídka zobrazuje v Microsoft AppSource.":::

**Popisy volání**

1. Logo
2. Kategorie
3. Obory
4. Adresa podpory (odkaz)
5. Podmínky použití
6. Zásady ochrany osobních údajů
7. Název nabídky
8. Souhrn
9. Description
10. Snímky obrazovky a videa
11. dokumenty.

Následující příklad ukazuje seznam nabídek v Azure Portal.

![Znázorňuje seznam nabídek v Azure Portal.](./media/example-managed-service-azure-portal.png)

**Popisy volání**

1. Nadpis
1. Popis
1. Užitečné odkazy
1. Screenshoty

> [!NOTE]
> Pokud popis nabídky začíná frází "Tato aplikace je k dispozici pouze v [jazykovém jazyce, který není v angličtině), není nutné, aby byla v anglickém obsahu.

Abyste mohli snadněji vytvořit nabídku, připravte si některé z těchto položek předem. Pokud není uvedeno jinak, jsou vyžadovány následující položky.

- **Název**: Tento název se zobrazí jako název vaší nabídky na komerčním webu Marketplace. Název může být ve vaší společnosti. Nemůže obsahovat Emoji (Pokud se nejedná o symboly ochranné známky a copyrightu) a musí být omezený na 50 znaků.
- **Shrnutí výsledků hledání**: popište účel nebo funkci nabídky jako jednu větu bez konců řádků na 100 znaků nebo méně. Tento souhrn se používá ve výsledcích hledání na komerčních tržištích.
- **Popis**: Tento popis se zobrazí v seznamu komerčních webů Marketplace. Vezměte v úvahu zahrnutí hodnot do kategorií, klíčových výhod, zamýšlené uživatelské základny, jakékoli kategorie nebo přidružení oboru, možností nákupu v aplikaci, všech potřebných zveřejnění a odkazu na Další informace.

    V tomto textovém poli jsou ovládací prvky editoru formátovaného textu, které můžete použít k lepšímu poutavení popisu. K formátování popisu můžete použít také značky HTML. Do tohoto pole můžete zadat až 3 000 znaků textu, včetně kódu HTML. Další tipy najdete v tématu [Vytvoření skvělého popisu aplikace](/windows/uwp/publish/write-a-great-app-description).

- **Pokyny pro Začínáme**: Pokud se rozhodnete prodávat nabídku prostřednictvím Microsoft (nabídka s podporou transakcí), toto pole je povinné. Tyto pokyny pomůžou zákazníkům připojit se k vaší nabídce SaaS. Můžete přidat až 3 000 znaků textu a odkazy na podrobnější online dokumentaci.
- **Hledaná klíčová slova** (volitelné): Poskytněte až tři klíčová slova pro hledání, která můžou zákazníci použít k vyhledání vaší nabídky v online obchodech. Nemusíte zahrnovat **název** a **Popis** nabídky: Tento text je automaticky zahrnutý do hledání.
- **Odkaz zásady ochrany osobních údajů**: adresa URL zásad ochrany osobních údajů vaší společnosti. Je nutné zadat platné zásady ochrany osobních údajů a zodpovídá za to, že vaše aplikace bude v souladu se zákony a předpisy ochrany osobních údajů.
- **Kontaktní informace**: musíte zadat následující kontakty z vaší organizace:
  - **Kontakt na podporu**: Zadejte jméno, telefon a e-mail pro partnery Microsoftu, kteří se použijí, když vaši zákazníci otevřou lístky. Musíte taky zahrnout adresu URL vašeho webu podpory.
  - **Technický kontakt**: Zadejte jméno, telefon a e-mailovou adresu Microsoftu pro použití přímo v případě problémů s vaší nabídkou. Tyto kontaktní údaje nejsou uvedené na komerčním webu Marketplace.
  - **Kontakt programu CSP** (volitelné): Zadejte jméno, telefon a e-mailovou adresu, pokud se přihlásíte k programu CSP, aby vás mohli kontaktovat s případnými dotazy. Do svých marketingových materiálů můžete také přidat adresu URL.
- **Užitečné odkazy** (volitelné): můžete poskytnout odkazy na různé prostředky pro uživatele vaší nabídky. Například fóra, nejčastější dotazy a poznámky k verzi.
- **Podpůrné dokumenty**: můžete poskytnout až tři dokumenty pro zákazníky, jako jsou dokumenty White Paper, brožury, kontrolní seznamy nebo prezentace v PowerPointu.
- **Médium – loga**: Zadejte soubor PNG pro **velké** logo. Partnerské centrum použije toto k vytvoření **malého** a **středního** loga. Případně je můžete později nahradit různými obrázky.

   - Velký (od 216 x 216 do 350 × 350 px, požadováno)
   - Střední (90 x 90 px, volitelné)
   - Malý (48 x 48 px, volitelné)

  Tato loga se používají na různých místech v online obchodech:

  - Malé logo se zobrazí ve výsledcích hledání Azure Marketplace a na stránce Microsoft AppSource hlavní stránka a výsledky hledání.
  - Logo střední se zobrazí při vytváření nového prostředku v Microsoft Azure.
  - Velké logo se zobrazí na stránce se seznamem nabídek v Azure Marketplace a Microsoft AppSource.

- **Média – snímky obrazovky**: musíte přidat aspoň jeden a až pět snímků obrazovky s následujícími požadavky, které ukazují, jak vaše nabídka funguje:
  - 1280 x 720 pixelů
  - soubor. png
  - Musí obsahovat titulek
- **Média – videa** (volitelné): můžete přidat až čtyři videa s následujícími požadavky, které předvádí vaši nabídku:
  - Name
  - Adresa URL: musí být hostována pouze na YouTube nebo Vimeo.
  - Miniatura: soubor 1280 x 720. png

> [!Note]
> Vaše nabídka musí splňovat obecné [zásady certifikace komerčního tržiště](/legal/marketplace/certification-policies#100-general) a [software jako zásady služby](/legal/marketplace/certification-policies#1000-software-as-a-service-saas) , které se mají publikovat na komerčním webu Marketplace.

## <a name="preview-audience"></a>Cílová skupina Preview
Cílová skupina Preview má přístup k vaší nabídce před publikováním živě v online obchodech, aby bylo možné otestovat kompletní funkce před publikováním v reálném čase. Na stránce **Preview cílovou skupinu** můžete definovat skupinu omezené verze Preview. Toto nastavení není k dispozici, pokud se rozhodnete zpracovávat transakce nezávisle na prodeji vaší nabídky prostřednictvím společnosti Microsoft. Pokud ano, můžete tuto část přeskočit a přejít na [Další prodejní příležitosti](#additional-sales-opportunities).

> [!NOTE]
> Cílová skupina verze Preview se liší od privátního plánu. Soukromý plán je ten, který zpřístupníte jenom pro konkrétní cílovou skupinu, kterou zvolíte. To vám umožní vyjednat vlastní plán s konkrétními zákazníky. Další informace najdete v další části: plány.

Pozvánky můžete odesílat do e-mailových adres účtu Microsoft (MSA) nebo Azure Active Directory (Azure AD). Přidejte až 10 e-mailových adres ručně nebo importujte až 20 souborů. csv. Pokud je vaše nabídka už živá, můžete i nadále definovat cílovou skupinu verze Preview pro testování změn nebo aktualizací vaší nabídky.

## <a name="plans"></a>Plány

V transakčních nabídkách se vyžaduje aspoň jeden plán. Plán definuje rozsah a omezení řešení a související ceny. Můžete vytvořit několik plánů pro vaši nabídku, abyste svým zákazníkům poskytli různé technické a cenové možnosti. Pokud se rozhodnete zpracovávat transakce nezávisle na tom, nemusíte vytvářet nabídku s podporou transakcí, neuvidíte stránku **plány** . Pokud ano, přeskočte tuto část a přejděte na [Další prodejní příležitosti](#additional-sales-opportunities).

Obecné pokyny k plánům, včetně cenových modelů, bezplatných zkušebních verzí a privátních plánů, najdete v tématu [plány a ceny pro komerční web Marketplace](plans-pricing.md) . Následující části popisují další informace, které jsou specifické pro nabídky SaaS.

### <a name="saas-pricing-models"></a>Cenové modely SaaS

Nabídky SaaS můžou používat jeden ze dvou modelů cen s jednotlivými plány: buď _paušální sazba_ , nebo _podle počtu uživatelů_. Všechny plány ve stejné nabídce se musí přidružit ke stejnému cenovému modelu. Nabídka například nemůže mít jeden plán, který je paušální sazbou a druhý plán na uživatele.

**Paušální sazba** – umožní vám přístup k vaší nabídce s jednou měsíční nebo roční paušální cenou. Tato situace se někdy označuje jako ceny na základě lokality. Pomocí tohoto cenového modelu můžete volitelně definovat měřené plány, které používají rozhraní API služby pro měření softwaru Marketplace k účtování zákazníků na využití, na které se nevztahuje paušální sazba. Další informace o měřených fakturách najdete v tématu [měřené fakturace za SaaS pomocí komerčního měření služby na webu Marketplace](./partner-center-portal/saas-metered-billing.md). Tuto možnost byste měli použít i v případě, že je chování při používání služby SaaS v shlukech.

**Na uživatele** – povolí přístup k vaší nabídce s cenou na základě počtu uživatelů, kteří mají přístup k nabídce nebo zabírat křesla. Pomocí tohoto modelu založeného na uživateli můžete nastavit minimální a maximální počet uživatelů, které tento plán podporuje. Můžete vytvořit více plánů pro konfiguraci různých cenových bodů na základě počtu uživatelů. Tato pole jsou volitelná. Pokud je ponecháno bez výběru, počet uživatelů bude interpretován jako neomezený (minimální počet 1 a maximální počet, jak může vaše služba podporovat). Tato pole je možné upravovat v rámci aktualizace vašeho plánu.

> [!IMPORTANT]
> Po publikování vaší nabídky nemůžete změnit cenový model. Kromě toho musí všechny plány pro stejnou nabídku sdílet stejný cenový model.

### <a name="saas-billing"></a>SaaS fakturace

Pro aplikace SaaS, které běží v rámci předplatného Azure (vydavatel), se využití infrastruktury účtuje přímo. zákazníkům se nezobrazují skutečné poplatky za použití infrastruktury. Poplatky za využití infrastruktury Azure byste měli začlenit na ceny za licence na software, abyste mohli kompenzovat náklady na infrastrukturu, kterou jste nasadili pro spuštění řešení.

Nabídky aplikací pro SaaS, které se prodávají prostřednictvím podpory Microsoftu, se účtují měsíčně nebo roční fakturace na základě paušálního poplatku za uživatele nebo poplatků za využití pomocí [účtované fakturační služby](./partner-center-portal/saas-metered-billing.md). Komerční tržiště funguje na modelu agentury, na základě kterého vydavatelé nastavili ceny, zákazníky s Microsoftem a Microsoft platí za to, že budou mít poplatky za srážku.

Následující příklad ukazuje vzorový rozpis nákladů a výběrů k předvedení modelu agentury. V tomto příkladu vám Microsoft účtuje $100,00 pro zákazníka za softwarovou licenci a vyplatí $80,00 pro vydavatele.

| Náklady na licenci | $100 za měsíc |
| ------------ | ------------- |
| Náklady na využití Azure (D1/1 – jádro) | Účtuje se přímo vydavateli, nikoli zákazník. |
| Zákazník se účtuje Microsoftem. | $100,00 za měsíc (Vydavatel musí mít v licenčním poplatku vynaložené nebo předávací náklady na infrastrukturu) |
| **Účty Microsoft** | **$100 za měsíc** |
| Společnost Microsoft vám zaplatí 80% vašich licenčních nákladů.<br>`*` Pro kvalifikované aplikace SaaS přináší společnost Microsoft 90% vašich licenčních nákladů.| $80,00 za měsíc<br>``*`` $90,00 za měsíc |
|||

**`*` Omezený poplatek za službu Marketplace** – u některých nabídek SaaS, které jste publikovali na komerčním tržišti, společnost Microsoft sníží poplatek za službu Marketplace z 20% (jak je popsáno v tématu smlouva Microsoft Publisher Agreement) na 10%. Aby vaše nabídky získaly nárok, vaše nabídky musí být určené společností Microsoft jako spoluprodejní motivovaní Azure. Nárok na získání omezeného poplatku za služby Marketplace vyžaduje splnění alespoň pěti (5) pracovních dnů před koncem každého kalendářního měsíce. Po splnění nároku bude snížený poplatek za službu udělen všem transakcím platným první den v následujícím měsíci a bude nadále platit až do chvíle, kdy se motivovaní stav společného prodeje Azure IP. Podrobnosti o způsobilosti spoluprodejních IP adres najdete v tématu [požadavky na stav společného prodeje](/legal/marketplace/certification-policies#3000-requirements-for-co-sell-status). Snížený poplatek za službu Marketplace platí také pro virtuální počítače Azure s motivovaní, spravované aplikace a jakékoli další kvalifikované IaaS nabídky, které jsou k dispozici prostřednictvím komerčního tržiště.

## <a name="additional-sales-opportunities"></a>Další prodejní příležitosti

Můžete si vybrat, jestli se chcete zúčastnit marketingových a prodejních kanálů podporovaných společností Microsoft. Při vytváření vaší nabídky v partnerském centru se zobrazí dvě karty na konec procesu:

- **Prodávejte dál prostřednictvím CSP**: tuto možnost použijte, pokud chcete, aby partneři poskytovatelé řešení Microsoft Cloud mohli své řešení znovu prodávat jako součást zahrnuté nabídky. Další informace o tomto programu najdete v tématu [program Cloud Solution Provider](cloud-solution-providers.md).

- **Spoluprodejní s Microsoftem**: Tato možnost umožňuje prodejcům Microsoftu zvážit, jaké řešení se svým zákazníkům nabízí při vyhodnocování vašich potřeb zákazníků. Podrobnosti o způsobilosti společného prodeje najdete v tématu [požadavky na stav společného prodeje](/legal/marketplace/certification-policies#3000-requirements-for-co-sell-status). Podrobné informace o tom, jak připravit vaši nabídku k vyhodnocení, najdete v tématu [možnost společného prodeje v partnerském centru](commercial-marketplace-co-sell.md).

## <a name="next-steps"></a>Další kroky

- [Jak vytvořit nabídku SaaS na komerčním webu Marketplace](create-new-saas-offer.md)
- [Osvědčené postupy nabídky](gtm-offer-listing-best-practices.md)