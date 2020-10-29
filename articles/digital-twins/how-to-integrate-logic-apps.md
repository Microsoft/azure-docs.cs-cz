---
title: Integrace s Logic Apps
titleSuffix: Azure Digital Twins
description: Přečtěte si téma jak připojit Logic Apps k digitálním Vlákenám Azure pomocí vlastního konektoru.
author: baanders
ms.author: baanders
ms.date: 9/11/2020
ms.topic: how-to
ms.service: digital-twins
ms.reviewer: baanders
ms.openlocfilehash: 9ea85449d3980f46e88eddc7e06e4a5384b8cea3
ms.sourcegitcommit: daab0491bbc05c43035a3693a96a451845ff193b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2020
ms.locfileid: "93027546"
---
# <a name="integrate-with-logic-apps-using-a-custom-connector"></a>Integrace s Logic Apps s využitím vlastního konektoru

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) je cloudová služba, která pomáhá automatizovat pracovní postupy napříč aplikacemi a službami. Připojením Logic Apps k rozhraním API digitálních vláken Azure můžete vytvářet automatizované toky kolem digitálních vláken Azure a jejich dat.

Digitální vlákna Azure momentálně nemají certifikovaný (předem sestavený) konektor pro Logic Apps. Místo toho aktuální proces použití Logic Apps s digitálními úkoly typu Azure vytvoří [**vlastní konektor Logic Apps**](../logic-apps/custom-connector-overview.md)pomocí [vlastního Swagger](/samples/azure-samples/digital-twins-custom-swaggers/azure-digital-twins-custom-swaggers/) , který je upravený tak, aby fungoval s Logic Apps.

> [!NOTE]
> Existuje více verzí Swagger obsažených ve vlastní ukázce Swagger, který je výše propojen. Nejnovější verzi najdete v podsložce s nejaktuálnějším datem, ale starší verze obsažené v ukázce jsou i nadále podporované.

V tomto článku použijete [Azure Portal](https://portal.azure.com) k **Vytvoření vlastního konektoru** , který se dá použít k připojení Logic Apps k instanci digitálních vláken Azure. Pak **vytvoříte aplikaci logiky** , která bude toto připojení používat pro ukázkový scénář, ve kterém události aktivované časovačem automaticky aktualizují dvojitou repliku v instanci digitálních vláken Azure. 

## <a name="prerequisites"></a>Předpoklady

Pokud ještě nemáte předplatné Azure, vytvořte si **[bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)** před tím, než začnete.
Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí tohoto účtu. 

V rámci požadované instalace je také nutné provést následující položky. Zbývající část této části vás provede následujícími kroky:
- Nastavení instance digitálních vláken Azure
- Získat tajný klíč klienta registrace aplikace
- Přidání digitálního vlákna

### <a name="set-up-azure-digital-twins-instance"></a>Nastavení instance digitálních vláken Azure

Pokud chcete připojit instanci digitálních vláken Azure k Logic Apps v tomto článku, musíte mít už nanastavenou **instanci digitálních vláken Azure** . 

Nejdřív **nastavte instanci digitálních vláken Azure** a požadované ověřování, abyste s ním mohli pracovat. Provedete to podle pokynů v tématu [*Postupy: nastavení instance a ověřování*](how-to-set-up-instance-portal.md).
* Po nastavení instance digitálního vlákna Azure budete potřebovat **_název hostitele_** instance ( [najít v Azure Portal](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values)).

K ověření konektoru budete taky muset nastavit **registraci aplikace** . Postupujte podle pokynů v tématu [*Postupy: Vytvoření registrace aplikace*](how-to-create-app-registration.md) pro nastavení. 
* Jakmile budete mít registraci aplikace, budete potřebovat ID **_aplikace (klienta)_** registrace a **_ID adresáře (_** klienta) ( [najít v Azure Portal](how-to-create-app-registration.md#collect-client-id-and-tenant-id)).

### <a name="get-app-registration-client-secret"></a>Získat tajný klíč klienta registrace aplikace

Pro registraci aplikace Azure AD budete taky muset vytvořit **_tajný klíč klienta_** . Provedete to tak, že přejdete na stránku [Registrace aplikací](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) v Azure Portal (Tento odkaz můžete použít nebo ho můžete najít na portálu pro hledání). Vyberte svou registraci, kterou jste vytvořili v předchozí části seznamu, aby bylo možné otevřít její podrobnosti. 

V nabídce registrace klikněte na *certifikáty a tajné klíče* a vyberte *+ nový tajný klíč klienta* .

:::image type="content" source="media/how-to-integrate-logic-apps/client-secret.png" alt-text="Zobrazení portálu pro registraci aplikace Azure AD V nabídce prostředků se zvýrazní certifikát a tajné klíče a zvýrazní se stránka kolem nového tajného klíče klienta.":::

Zadejte libovolné hodnoty, které chcete pro popis a vypršení platnosti, a potom stiskněte *Přidat* .

:::image type="content" source="media/how-to-integrate-logic-apps/add-client-secret.png" alt-text="Zobrazení portálu pro registraci aplikace Azure AD V nabídce prostředků se zvýrazní certifikát a tajné klíče a zvýrazní se stránka kolem nového tajného klíče klienta.":::

Nyní ověřte, zda je tajný klíč klienta zobrazen na stránce _certifikáty & tajných_ kódů s poli _Expires_ a _Value_ . Poznamenejte si jeho _hodnotu_ pro pozdější použití (můžete ji také zkopírovat do schránky pomocí ikony kopírování).

:::image type="content" source="media/how-to-integrate-logic-apps/client-secret-value.png" alt-text="Zobrazení portálu pro registraci aplikace Azure AD V nabídce prostředků se zvýrazní certifikát a tajné klíče a zvýrazní se stránka kolem nového tajného klíče klienta.":::

### <a name="add-a-digital-twin"></a>Přidání digitálního vlákna

Tento článek používá Logic Apps k aktualizaci vlákna v instanci digitálních vláken Azure. Chcete-li pokračovat, měli byste do své instance přidat alespoň jednu dvojitou akci. 

Pomocí [rozhraní API DigitalTwins](/rest/api/digital-twins/dataplane/twins), [sady .NET (C#) SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet-preview&preserve-view=true)nebo rozhraní příkazového [řádku Azure Digital revlákens](how-to-use-cli.md)můžete přidat vlákna. Podrobné pokyny, jak vytvořit vlákna pomocí těchto metod, naleznete v tématu [*How to: Manage Digital vláknas*](how-to-manage-twin.md).

Budete potřebovat **_zdvojené ID_** vlákna ve vaší instanci, kterou jste vytvořili.

## <a name="create-custom-logic-apps-connector"></a>Vytvoření vlastního konektoru Logic Apps

V tomto kroku vytvoříte [vlastní konektor Logic Apps](../logic-apps/custom-connector-overview.md) pro rozhraní API digitálních vláken Azure. Až to uděláte, budete moct při vytváření aplikace logiky v další části připojit digitální vlákna Azure.

Přejít na stránku [vlastního konektoru Logic Apps](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Web%2FcustomApis) v Azure Portal (můžete použít tento odkaz nebo ho vyhledat na portálu pro hledání). Stiskněte *+ Přidat* .

:::image type="content" source="media/how-to-integrate-logic-apps/logic-apps-custom-connector.png" alt-text="Zobrazení portálu pro registraci aplikace Azure AD V nabídce prostředků se zvýrazní certifikát a tajné klíče a zvýrazní se stránka kolem nového tajného klíče klienta.":::

Na následující stránce *vytvořit Logic Apps vlastní konektor* vyberte své předplatné a skupinu prostředků a název a umístění nasazení nového konektoru. Podívejte se na *Revize + vytvořit* . 

:::image type="content" source="media/how-to-integrate-logic-apps/create-logic-apps-custom-connector.png" alt-text="Zobrazení portálu pro registraci aplikace Azure AD V nabídce prostředků se zvýrazní certifikát a tajné klíče a zvýrazní se stránka kolem nového tajného klíče klienta.":::

Tím přejdete na kartu *Revize + vytvořit* , kde *můžete v dolní části vytvořit prostředek* .

:::image type="content" source="media/how-to-integrate-logic-apps/review-logic-apps-custom-connector.png" alt-text="Zobrazení portálu pro registraci aplikace Azure AD V nabídce prostředků se zvýrazní certifikát a tajné klíče a zvýrazní se stránka kolem nového tajného klíče klienta.":::

Přejdete na stránku pro nasazení konektoru. Po dokončení nasazení klikněte na tlačítko *Přejít do prostředku* a zobrazte podrobnosti o konektoru na portálu.

### <a name="configure-connector-for-azure-digital-twins"></a>Konfigurace konektoru pro digitální vlákna Azure

Dále nakonfigurujete konektor, který jste vytvořili pro dosažení digitálních vláken Azure.

Nejdřív Stáhněte si vlastní soubor Swagger pro digitální vlákna Azure, který je upravený tak, aby fungoval s Logic Apps. Stáhněte si ukázku **digitálních Swagger (Logic Apps Connector) Azure** z [**tohoto odkazu a propojte**](/samples/azure-samples/digital-twins-custom-swaggers/azure-digital-twins-custom-swaggers/) si tlačítko *Stáhnout ZIP* . Přejděte do složky Stažené *Azure_Digital_Twins_custom_Swaggers__Logic_Apps_connector_.zip* a rozbalte ji. 

Vlastní Swagger pro tento kurz je umístěný ve složce _* * Azure_Digital_Twins_custom_Swaggers__Logic_Apps_connector_ \LogicApps **_. Tato složka obsahuje podsložky s názvem *stabilní* a ve *verzi Preview* , obě obsahují různé verze Swagger seřazené podle data. Složka s nejnovějším datem bude obsahovat poslední kopii Swagger. Podle vybrané verze se soubor Swagger jmenuje _** digitaltwins.jsna * * _.

> [!NOTE]
> Pokud nepracujete s funkcí Preview, obecně se doporučuje použít nejnovější *stabilní* verzi Swagger. Starší verze a verze Preview se ale pořád podporují i ve verzi Preview. 

Potom přejděte na stránku Přehled vašeho konektoru v [Azure Portal](https://portal.azure.com) a stiskněte *Upravit* .

:::image type="content" source="media/how-to-integrate-logic-apps/edit-connector.png" alt-text="Zobrazení portálu pro registraci aplikace Azure AD V nabídce prostředků se zvýrazní certifikát a tajné klíče a zvýrazní se stránka kolem nového tajného klíče klienta.":::

Na následující stránce *upravit Logic Apps vlastní konektor* nakonfigurujte tyto informace:
* **Vlastní konektory**
    - Koncový bod rozhraní API: REST (ponechat výchozí)
    - Režim importu: soubor OpenAPI (ponechat výchozí)
    - Soubor: Toto je vlastní soubor Swagger, který jste stáhli dříve. Stiskněte tlačítko *importovat* , vyhledejte soubor na počítači ( *Azure_Digital_Twins_custom_Swaggers__Logic_Apps_connector_ \LogicApps \...\digitaltwins.jszapnutý* ) a stiskněte *otevřít* .
* **Obecné informace**
    - Ikona: Nahrajte ikonu, kterou se vám líbí.
    - Barva pozadí ikony: pro barvu zadejte hexadecimální kód ve formátu ' #xxxxxx '.
    - Popis: Vyplňte libovolné hodnoty, které chcete.
    - Schéma: HTTPS (ponechat výchozí)
    - Host (hostitel): *název hostitele* instance digitálního vlákna Azure.
    - Základní adresa URL:/(ponechte výchozí)

Potom v dolní části okna stiskněte tlačítko *zabezpečení* , aby pokračoval k dalšímu kroku konfigurace.

:::image type="content" source="media/how-to-integrate-logic-apps/configure-next.png" alt-text="Zobrazení portálu pro registraci aplikace Azure AD V nabídce prostředků se zvýrazní certifikát a tajné klíče a zvýrazní se stránka kolem nového tajného klíče klienta.":::

V kroku zabezpečení stiskněte *Upravit* a nakonfigurujte tyto informace:
* **Typ ověřování** : OAuth 2,0
* **OAuth 2,0** :
    - Zprostředkovatel identity: Azure Active Directory
    - ID klienta: *ID aplikace (klienta)* pro vaši registraci aplikace Azure AD
    - Tajný kód klienta: *tajný klíč klienta* , který jste vytvořili v části [*požadavky*](#prerequisites) na registraci vaší aplikace Azure AD
    - Přihlašovací adresa URL: https://login.windows.net (ponechat výchozí)
    - ID tenanta: *ID adresáře (tenanta)* pro vaši registraci aplikace Azure AD
    - Adresa URL prostředku: 0b07f429-9f4b-4714-9392-cc5e8e80c8b0
    - Scope: Directory. AccessAsUser. All
    - Adresa URL pro přesměrování: (teď ponechat výchozí)

Všimněte si, že pole Adresa URL *pro přesměrování říká uložení vlastního konektoru pro vygenerování adresy URL pro přesměrování* . Provedete to teď pomocí *konektoru aktualizací* v horní části podokna, abyste potvrdili nastavení konektoru.

:::image type="content" source="media/how-to-integrate-logic-apps/update-connector.png" alt-text="Zobrazení portálu pro registraci aplikace Azure AD V nabídce prostředků se zvýrazní certifikát a tajné klíče a zvýrazní se stránka kolem nového tajného klíče klienta.":::

<!-- Success message? didn't see one -->

Vraťte se do pole Adresa URL pro přesměrování a zkopírujte hodnotu, která byla vygenerována. Budete ho používat v dalším kroku.

:::image type="content" source="media/how-to-integrate-logic-apps/copy-redirect-url.png" alt-text="Zobrazení portálu pro registraci aplikace Azure AD V nabídce prostředků se zvýrazní certifikát a tajné klíče a zvýrazní se stránka kolem nového tajného klíče klienta.":::

Jedná se o všechny informace, které jsou nutné k vytvoření konektoru (není nutné pokračovat v minulém zabezpečení do kroku definice). Podokno *upravit Logic Apps vlastní konektor* můžete zavřít.

>[!NOTE]
>Zpět na stránce s přehledem vašeho konektoru, kde jste původně provedli *úpravu* , si všimněte, že *se znovu spustí znovu spustit celý* proces zadání možností konfigurace. Nenačítá vaše hodnoty z poslední doby, takže pokud chcete uložit aktualizovanou konfiguraci se změněnými hodnotami, musíte znovu zadat všechny ostatní hodnoty a vyhnout se jejich přepsání výchozími hodnotami.

### <a name="grant-connector-permissions-in-the-azure-ad-app"></a>Udělení oprávnění konektoru v aplikaci Azure AD

Dále použijte hodnotu *URL přesměrování* vlastního konektoru, který jste zkopírovali v posledním kroku, a udělte jim oprávnění konektoru v registraci vaší aplikace Azure AD.

Přejděte na stránku [Registrace aplikací](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) v Azure Portal a vyberte svou registraci ze seznamu. 

V části *ověřování* v nabídce registrace přidejte identifikátor URI.

:::image type="content" source="media/how-to-integrate-logic-apps/add-uri.png" alt-text="Zobrazení portálu pro registraci aplikace Azure AD V nabídce prostředků se zvýrazní certifikát a tajné klíče a zvýrazní se stránka kolem nového tajného klíče klienta."::: 

Do nového pole zadejte *adresu URL pro přesměrování* vlastního konektoru a stiskněte ikonu *Uložit* .

:::image type="content" source="media/how-to-integrate-logic-apps/save-uri.png" alt-text="Zobrazení portálu pro registraci aplikace Azure AD V nabídce prostředků se zvýrazní certifikát a tajné klíče a zvýrazní se stránka kolem nového tajného klíče klienta.":::

Nyní jste dokončili vytváření vlastního konektoru, který má přístup k rozhraním API pro digitální vlákna Azure. 

## <a name="create-logic-app"></a>Vytvoření aplikace logiky

V dalším kroku vytvoříte aplikaci logiky, která bude používat váš nový konektor k automatizaci aktualizací digitálních vláken Azure.

V [Azure Portal](https://portal.azure.com)na panelu hledání na portálu vyhledejte *Logic Apps* . Výběr by měl přebírat stránku *Logic Apps* . Stiskněte tlačítko *vytvořit aplikaci logiky* a vytvořte novou aplikaci logiky.

:::image type="content" source="media/how-to-integrate-logic-apps/create-logic-app.png" alt-text="Zobrazení portálu pro registraci aplikace Azure AD V nabídce prostředků se zvýrazní certifikát a tajné klíče a zvýrazní se stránka kolem nového tajného klíče klienta.":::

Na stránce *Aplikace logiky* , které následují, zadejte své předplatné a skupinu prostředků. Také zvolte název aplikace logiky a vyberte umístění nasazení.

Stiskněte tlačítko _Revize + vytvořit_ .

Tím přejdete na kartu *Revize + vytvořit* , kde můžete zkontrolovat podrobnosti a *vytvořit v dolní* části svůj prostředek.

Přejdete na stránku nasazení aplikace logiky. Až se nasazení dokončí, přejděte do tlačítka *Přejít k prostředku* , kde můžete pokračovat v *Návrháři Logic Apps* , kde budete naplnit logiku pracovního postupu.

### <a name="design-workflow"></a>Pracovní postup návrhu

V *návrháři Logic Apps* v části *začít se společným triggerem* vyberte _**opakování**_ .

:::image type="content" source="media/how-to-integrate-logic-apps/logic-apps-designer-recurrence.png" alt-text="Zobrazení portálu pro registraci aplikace Azure AD V nabídce prostředků se zvýrazní certifikát a tajné klíče a zvýrazní se stránka kolem nového tajného klíče klienta.":::

Na následující stránce *návrháře Logic Apps* změňte četnost **opakování** na *sekundu* , aby se událost spouštěla každé 3 sekundy. Díky tomu bude možné výsledky snadno zobrazit, aniž byste museli čekat velmi dlouho.

Stiskněte *+ Nový krok* .

Tím se otevře pole *zvolit akci* . Přepněte na *vlastní* kartu. V horním poli byste měli vidět vlastní konektor z výše uvedeného.

:::image type="content" source="media/how-to-integrate-logic-apps/custom-action.png" alt-text="Zobrazení portálu pro registraci aplikace Azure AD V nabídce prostředků se zvýrazní certifikát a tajné klíče a zvýrazní se stránka kolem nového tajného klíče klienta.":::

Vyberte ho, aby se zobrazil seznam rozhraní API obsažených v tomto konektoru. Pomocí panelu hledání nebo procházením seznamu vyberte **DigitalTwins_Add** . (Toto je rozhraní API použité v tomto článku, ale můžete také vybrat jiné rozhraní API jako platnou volbu pro Logic Apps připojení).

Může se zobrazit výzva k přihlášení pomocí přihlašovacích údajů Azure pro připojení ke konektoru. Pokud se zobrazí dialogové okno s *žádostí o oprávnění* , postupujte podle pokynů pro udělení souhlasu vaší aplikace a přijměte.

V poli New *DigitalTwinsAdd* vyplňte pole následujícím způsobem:
* _ID_ : Vyplňte *dvojitou identifikaci* digitálního vlákna ve vaší instanci, kterou chcete, aby aplikace logiky aktualizovala.
* Prop _: do_ tohoto pole zadáte text, který vyžaduje vybraná žádost o rozhraní API. V případě *DigitalTwinsUpdate* je tento text ve formě kódu opravy JSON. Další informace o strukturování opravy JSON pro aktualizaci vlákna najdete v části [aktualizace digitálního vlákna](how-to-manage-twin.md#update-a-digital-twin) v tématu *Postupy: Správa digitálních vláken* .
* _API-Version_ : nejnovější verze rozhraní API. V současné době je tato hodnota *2020-10-31* .

Stiskněte *Uložit* v Návrháři Logic Apps.

Výběrem možnosti _+ Nový krok_ ve stejném okně můžete vybrat jiné operace.

:::image type="content" source="media/how-to-integrate-logic-apps/save-logic-app.png" alt-text="Zobrazení portálu pro registraci aplikace Azure AD V nabídce prostředků se zvýrazní certifikát a tajné klíče a zvýrazní se stránka kolem nového tajného klíče klienta.":::

## <a name="query-twin-to-see-the-update"></a>Pro zobrazení aktualizace se zobrazí dvojitá vlákna dotazu.

Teď, když je vaše aplikace logiky vytvořená, by se událost s dvojitou aktualizací, kterou jste definovali v Návrháři Logic Apps, měla objevit při opakování každé tři sekundy. To znamená, že po třech sekundách byste měli být schopni zadat dotaz na vlákna a zobrazit nové opravené hodnoty.

Můžete se dotazovat na vlákna přes vaši metodu volby (například [vlastní klientská aplikace](tutorial-command-line-app.md), [ukázkovou aplikaci Průzkumníka digitálních vláken Azure](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/), sady [SDK a rozhraní](how-to-use-apis-sdks.md)příkazového [řádku](how-to-use-cli.md)). 

Další informace o dotazování instance digitálního vlákna Azure najdete v tématu [*Postup: dotazování na dvojitou graf*](how-to-query-graph.md).

## <a name="next-steps"></a>Další kroky

V tomto článku jste vytvořili aplikaci logiky, která pravidelně aktualizuje dvojitou hodnotu ve vaší instanci digitálního vlákna Azure pomocí opravy, kterou jste zadali. Můžete vyzkoušet výběr dalších rozhraní API ve vlastním konektoru a vytvořit Logic Apps pro nejrůznější akce v instanci.

Další informace o dostupných operacích rozhraní API a podrobnostech, které vyžadují, najdete v tématu [*Postupy: použití rozhraní API a sad SDK digitálních vláken Azure*](how-to-use-apis-sdks.md).