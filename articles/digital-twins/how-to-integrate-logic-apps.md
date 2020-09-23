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
ms.openlocfilehash: 09181a28edf21f0a4da11a244d3c094469446ab5
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90983448"
---
# <a name="integrate-with-logic-apps-using-a-custom-connector"></a>Integrace s Logic Apps s využitím vlastního konektoru

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) je cloudová služba, která pomáhá automatizovat pracovní postupy napříč aplikacemi a službami. Připojením Logic Apps k rozhraním API digitálních vláken Azure můžete vytvářet automatizované toky kolem digitálních vláken Azure a jejich dat.

Digitální vlákna Azure momentálně nemají certifikovaný (předem sestavený) konektor pro Logic Apps. Místo toho aktuální proces použití Logic Apps s digitálními úkoly typu Azure vytvoří [**vlastní konektor Logic Apps**](../logic-apps/custom-connector-overview.md)pomocí [vlastního Swagger](https://docs.microsoft.com/samples/azure-samples/digital-twins-custom-swaggers/azure-digital-twins-custom-swaggers/) , který je upravený tak, aby fungoval s Logic Apps.

V tomto článku použijete [Azure Portal](https://portal.azure.com) k **Vytvoření vlastního konektoru** , který se dá použít k připojení Logic Apps k instanci digitálních vláken Azure. Pak **vytvoříte aplikaci logiky** , která bude toto připojení používat pro ukázkový scénář, ve kterém události aktivované časovačem automaticky aktualizují dvojitou repliku v instanci digitálních vláken Azure. 

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si ** [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) ** před tím, než začnete.
Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí tohoto účtu. 

Zbytek této části vás provede následujícími kroky:
- Nastavení instance digitálních vláken Azure
- Získat tajný klíč klienta registrace aplikace
- Přidání digitálního vlákna

### <a name="set-up-azure-digital-twins-instance"></a>Nastavení instance digitálních vláken Azure

Pokud chcete připojit instanci digitálních vláken Azure k Logic Apps v tomto článku, musíte mít už nanastavenou **instanci digitálních vláken Azure** . 

Pokud teď potřebujete nastavit novou instanci, nejjednodušší způsob, jak to udělat, je spustit ukázku skriptu pro automatizované nasazení. Postupujte podle pokynů v tématu [*Postupy: nastavení instance a ověřování (pomocí skriptů)*](how-to-set-up-instance-scripted.md) k nastavení nové instance a požadované registrace aplikace služby Azure AD. Pokyny také obsahují kroky k ověření, že jste dokončili každý krok úspěšně a že jste připraveni na přechod k používání nové instance.

V tomto kurzu budete při nastavování instance potřebovat následující hodnoty. Pokud tyto hodnoty potřebujete znovu shromáždit, použijte odkazy níže na příslušné části v článku o nastavení, abyste je našli v [Azure Portal](https://portal.azure.com).
* **_Název hostitele_** instance digitálních vláken Azure ([najít v portálu](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values))
* ID aplikace registrace aplikace Azure AD **_(ID klienta)_** ([najít v portálu](how-to-set-up-instance-portal.md#collect-important-values))
* **_ID adresáře_** pro registraci aplikace Azure AD ([najít v portálu](how-to-set-up-instance-portal.md#collect-important-values))

### <a name="get-app-registration-client-secret"></a>Získat tajný klíč klienta registrace aplikace

Pro registraci aplikace Azure AD budete taky muset vytvořit **_tajný klíč klienta_** . Provedete to tak, že přejdete na stránku [Registrace aplikací](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) v Azure Portal (Tento odkaz můžete použít nebo ho můžete najít na portálu pro hledání). Vyberte svou registraci ze seznamu a otevřete její podrobnosti. 

V nabídce registrace klikněte na *certifikáty a tajné klíče* a vyberte *+ nový tajný klíč klienta*.

:::image type="content" source="media/how-to-integrate-logic-apps/client-secret.png" alt-text="Zobrazení portálu pro registraci aplikace Azure AD V nabídce prostředků se zvýrazní certifikát a tajné klíče a zvýrazní se stránka kolem nového tajného klíče klienta.":::

Zadejte libovolné hodnoty, které chcete pro popis a vypršení platnosti, a potom stiskněte *Přidat*.

:::image type="content" source="media/how-to-integrate-logic-apps/add-client-secret.png" alt-text="Přidat tajný klíč klienta":::

Nyní ověřte, zda je tajný klíč klienta zobrazen na stránce _certifikáty & tajných_ kódů s poli _Expires_ a _Value_ . Poznamenejte si jeho _hodnotu_ pro pozdější použití (můžete ji také zkopírovat do schránky pomocí ikony kopírování).

:::image type="content" source="media/how-to-integrate-logic-apps/client-secret-value.png" alt-text="Kopírovat tajnou hodnotu klienta":::

### <a name="add-a-digital-twin"></a>Přidání digitálního vlákna

Tento článek používá Logic Apps k aktualizaci vlákna v instanci digitálních vláken Azure. Chcete-li pokračovat, měli byste do své instance přidat alespoň jednu dvojitou akci. 

Pomocí [rozhraní API DigitalTwins](how-to-use-apis-sdks.md), [sady .NET (C#) SDK](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)nebo rozhraní příkazového [řádku Azure Digital revlákens](how-to-use-cli.md)můžete přidat vlákna. Podrobné pokyny, jak vytvořit vlákna pomocí těchto metod, naleznete v tématu [*How to: Manage Digital vláknas*](how-to-manage-twin.md).

Budete potřebovat **_zdvojené ID_** vlákna ve vaší instanci, kterou jste vytvořili.

## <a name="create-custom-logic-apps-connector"></a>Vytvoření vlastního konektoru Logic Apps

V tomto kroku vytvoříte [vlastní konektor Logic Apps](../logic-apps/custom-connector-overview.md) pro rozhraní API digitálních vláken Azure. Až to uděláte, budete moct při vytváření aplikace logiky v další části připojit digitální vlákna Azure.

Přejít na stránku [vlastního konektoru Logic Apps](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Web%2FcustomApis) v Azure Portal (můžete použít tento odkaz nebo ho vyhledat na portálu pro hledání). Stiskněte *+ Přidat*.

:::image type="content" source="media/how-to-integrate-logic-apps/logic-apps-custom-connector.png" alt-text="Stránka Logic Apps vlastní konektor v Azure Portal. Zvýraznění kolem tlačítka Přidat":::

Na následující stránce *vytvořit Logic Apps vlastní konektor* vyberte své předplatné a skupinu prostředků a název a umístění nasazení nového konektoru. Podívejte se na *Revize + vytvořit*. 

:::image type="content" source="media/how-to-integrate-logic-apps/create-logic-apps-custom-connector.png" alt-text="Stránka vytvořit Logic Apps vlastního konektoru v Azure Portal.":::

Tím přejdete na kartu *Revize + vytvořit* , kde *můžete v dolní části vytvořit prostředek* .

:::image type="content" source="media/how-to-integrate-logic-apps/review-logic-apps-custom-connector.png" alt-text="Karta Revize a vytvoření vlastního konektoru v Azure Portal na stránce "revize Logic Apps Custom Connector". Zvýraznění kolem tlačítka pro vytvoření":::

Přejdete na stránku pro nasazení konektoru. Po dokončení nasazení klikněte na tlačítko *Přejít do prostředku* a zobrazte podrobnosti o konektoru na portálu.

### <a name="configure-connector-for-azure-digital-twins"></a>Konfigurace konektoru pro digitální vlákna Azure

Dále nakonfigurujete konektor, který jste vytvořili pro dosažení digitálních vláken Azure.

Nejdřív Stáhněte si vlastní soubor Swagger pro digitální vlákna Azure, který je upravený tak, aby fungoval s Logic Apps. Stáhněte si ukázku **digitálních Swagger z digitálního vlákna Azure** z [tohoto odkazu](https://docs.microsoft.com/samples/azure-samples/digital-twins-custom-swaggers/azure-digital-twins-custom-swaggers/) přes tlačítko *Stáhnout soubor zip* . Přejděte do složky Stažené *Azure_Digital_Twins_Custom_Swaggers.zip* a rozbalte ji. Vlastní Swagger pro tento kurz najdete na adrese *Azure_Digital_Twins_Custom_Swaggers\LogicApps\preview\2020-05-31-preview\digitaltwins.js*.

Potom přejděte na stránku Přehled vašeho konektoru v [Azure Portal](https://portal.azure.com) a stiskněte *Upravit*.

:::image type="content" source="media/how-to-integrate-logic-apps/edit-connector.png" alt-text="Stránka Přehled pro konektor vytvořený v předchozím kroku. Zvýraznění kolem tlačítka pro úpravy":::

Na následující stránce *upravit Logic Apps vlastní konektor* nakonfigurujte tyto informace:
* **Vlastní konektory**
    - Koncový bod rozhraní API: REST (ponechat výchozí)
    - Režim importu: soubor OpenAPI (ponechat výchozí)
    - Soubor: Toto je vlastní soubor Swagger, který jste stáhli dříve. Spusťte *Import*, vyhledejte soubor na počítači (*Azure_Digital_Twins_Custom_Swaggers\LogicApps\preview\2020-05-31-preview\digitaltwins.jszapnutý*) a stiskněte *otevřít*.
* **Obecné informace**
    - Ikona: Nahrajte ikonu, kterou se vám líbí.
    - Barva pozadí ikony: pro barvu zadejte hexadecimální kód ve formátu ' #xxxxxx '.
    - Popis: Vyplňte libovolné hodnoty, které chcete.
    - Schéma: HTTPS (ponechat výchozí)
    - Host (hostitel): *název hostitele* instance digitálního vlákna Azure.
    - Základní adresa URL:/(ponechte výchozí)

Potom v dolní části okna stiskněte tlačítko *zabezpečení* , aby pokračoval k dalšímu kroku konfigurace.

:::image type="content" source="media/how-to-integrate-logic-apps/configure-next.png" alt-text="Snímek obrazovky dolní části stránky upravit Logic Apps vlastní konektor Zvýrazňování kolem tlačítka pro pokračování v zabezpečení":::

V kroku zabezpečení stiskněte *Upravit* a nakonfigurujte tyto informace:
* **Typ ověřování**: OAuth 2,0
* **OAuth 2,0**:
    - Zprostředkovatel identity: Azure Active Directory
    - ID klienta: *ID aplikace (klienta)* pro vaši registraci aplikace Azure AD
    - Tajný kód klienta: *tajný klíč klienta* , který jste vytvořili v části [*požadavky*](#prerequisites) na registraci vaší aplikace Azure AD
    - Přihlašovací adresa URL: https://login.windows.net (ponechat výchozí)
    - ID tenanta: *ID adresáře (tenanta)* pro vaši registraci aplikace Azure AD
    - Adresa URL prostředku: 0b07f429-9f4b-4714-9392-cc5e8e80c8b0
    - Scope: Directory. AccessAsUser. All
    - Adresa URL pro přesměrování: (teď ponechat výchozí)

Všimněte si, že pole Adresa URL *pro přesměrování říká uložení vlastního konektoru pro vygenerování adresy URL pro přesměrování*. Provedete to teď pomocí *konektoru aktualizací* v horní části podokna, abyste potvrdili nastavení konektoru.

:::image type="content" source="media/how-to-integrate-logic-apps/update-connector.png" alt-text="Snímek obrazovky horní části stránky upravit Logic Apps vlastní konektor Zvýraznění kolem tlačítka pro aktualizaci konektoru":::

<!-- Success message? didn't see one -->

Vraťte se do pole Adresa URL pro přesměrování a zkopírujte hodnotu, která byla vygenerována. Budete ho používat v dalším kroku.

:::image type="content" source="media/how-to-integrate-logic-apps/copy-redirect-url.png" alt-text="Pole Adresa URL pro přesměrování na stránce Upravit Logic Apps vlastní konektor má nyní hodnotu https://logic-apis-westus2.consent.azure-apim.net/redirect . Tlačítko pro zkopírování hodnoty je zvýrazněno.":::

Jedná se o všechny informace, které jsou nutné k vytvoření konektoru (není nutné pokračovat v minulém zabezpečení do kroku definice). Podokno *upravit Logic Apps vlastní konektor* můžete zavřít.

>[!NOTE]
>Zpět na stránce s přehledem vašeho konektoru, kde jste původně provedli *úpravu*, si všimněte, že *se znovu spustí znovu spustit celý* proces zadání možností konfigurace. Nenačítá vaše hodnoty z poslední doby, takže pokud chcete uložit aktualizovanou konfiguraci se změněnými hodnotami, musíte znovu zadat všechny ostatní hodnoty a vyhnout se jejich přepsání výchozími hodnotami.

### <a name="grant-connector-permissions-in-the-azure-ad-app"></a>Udělení oprávnění konektoru v aplikaci Azure AD

Dále použijte hodnotu *URL přesměrování* vlastního konektoru, který jste zkopírovali v posledním kroku, a udělte jim oprávnění konektoru v registraci vaší aplikace Azure AD.

Přejděte na stránku [Registrace aplikací](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) v Azure Portal a vyberte svou registraci ze seznamu. 

V části *ověřování* v nabídce registrace přidejte identifikátor URI.

:::image type="content" source="media/how-to-integrate-logic-apps/add-uri.png" alt-text="Ověřovací stránka pro registraci aplikace v Azure Portal. V nabídce je zvýrazněna možnost ověřování a na stránce je zvýrazněno tlačítko Přidat identifikátor URI."::: 

Do nového pole zadejte *adresu URL pro přesměrování* vlastního konektoru a stiskněte ikonu *Uložit* .

:::image type="content" source="media/how-to-integrate-logic-apps/save-uri.png" alt-text="Ověřovací stránka pro registraci aplikace v Azure Portal. Nová adresa URL pro přesměrování je zvýrazněna a tlačítko Uložit pro stránku.":::

Nyní jste dokončili vytváření vlastního konektoru, který má přístup k rozhraním API pro digitální vlákna Azure. 

## <a name="create-logic-app"></a>Vytvoření aplikace logiky

V dalším kroku vytvoříte aplikaci logiky, která bude používat váš nový konektor k automatizaci aktualizací digitálních vláken Azure.

Přejděte na stránku [Logic Apps (spotřeba)](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Logic%2Fworkflows) v Azure Portal (můžete použít tento odkaz nebo ho vyhledat na portálu pro hledání). Stisknutím tlačítka *Přidat* vytvoříte aplikaci logiky.

:::image type="content" source="media/how-to-integrate-logic-apps/create-logic-app.png" alt-text="Stránka Logic Apps (spotřeba) v Azure Portal. Stiskněte tlačítko Přidat.":::

Na stránce *Logic Apps (spotřeba)* , která následuje, zadejte své předplatné a skupinu prostředků. Také zvolte název aplikace logiky a vyberte umístění.

Vyberte tlačítko _zkontrolovat + vytvořit_ .

Tím přejdete na kartu *Revize + vytvořit* , kde můžete zkontrolovat podrobnosti a *vytvořit v dolní* části svůj prostředek.

Přejdete na stránku nasazení aplikace logiky. Až se nasazení dokončí, přejděte do tlačítka *Přejít k prostředku* , kde můžete pokračovat v *Návrháři Logic Apps*, kde budete naplnit logiku pracovního postupu.

### <a name="design-workflow"></a>Pracovní postup návrhu

V *návrháři Logic Apps*v části *začít se společným triggerem*vyberte _**opakování**_.

:::image type="content" source="media/how-to-integrate-logic-apps/logic-apps-designer-recurrence.png" alt-text="Stránka Logic Apps Designer v Azure Portal. Zvýrazněte běžný Trigger opakování.":::

Na následující stránce *návrháře Logic Apps* změňte četnost **opakování** na *sekundu*, aby se událost spouštěla každé 3 sekundy. Díky tomu bude možné výsledky snadno zobrazit, aniž byste museli čekat velmi dlouho.

Stiskněte *+ Nový krok*.

Tím se otevře pole *zvolit akci* . Přepněte na *vlastní* kartu. V horním poli byste měli vidět vlastní konektor z výše uvedeného.

:::image type="content" source="media/how-to-integrate-logic-apps/custom-action.png" alt-text="Vytvoření toku v Návrháři Logic Apps v Azure Portal. V poli zvolit akci se vybere karta vlastní. Vlastní konektor uživatele ze starší verze se zobrazí v poli s zvýrazněním kolem.":::

Vyberte ho, aby se zobrazil seznam rozhraní API obsažených v tomto konektoru. Pomocí panelu hledání nebo procházením seznamu vyberte **DigitalTwins_Add**. (Toto je rozhraní API použité v tomto článku, ale můžete také vybrat jiné rozhraní API jako platnou volbu pro Logic Apps připojení).

Může se zobrazit výzva k přihlášení pomocí přihlašovacích údajů Azure pro připojení ke konektoru. Pokud se zobrazí dialogové okno s *žádostí o oprávnění* , postupujte podle pokynů pro udělení souhlasu vaší aplikace a přijměte.

V poli New *DigitalTwinsAdd* vyplňte pole následujícím způsobem:
* _ID_: Vyplňte *dvojitou identifikaci* digitálního vlákna ve vaší instanci, kterou chcete, aby aplikace logiky aktualizovala.
* Prop _: do_tohoto pole zadáte text, který vyžaduje vybraná žádost o rozhraní API. V případě *DigitalTwinsUpdate*je tento text ve formě kódu opravy JSON. Další informace o strukturování opravy JSON pro aktualizaci vlákna najdete v části [aktualizace digitálního vlákna](how-to-manage-twin.md#update-a-digital-twin) v tématu *Postupy: Správa digitálních vláken*.
* _verze API-Version_: v aktuální verzi Public Preview je tato hodnota *2020-05-31 – Preview*

Stiskněte *Uložit* v Návrháři Logic Apps.

Výběrem možnosti _+ Nový krok_ ve stejném okně můžete vybrat jiné operace.

:::image type="content" source="media/how-to-integrate-logic-apps/save-logic-app.png" alt-text="Zobrazení aplikace bylo dokončeno v konektoru aplikace logiky. Pole DigitalTwinsAdd se vyplní hodnotami popsanými výše, včetně ukázkového těla opravy JSON. Tlačítko Uložit pro okno je zvýrazněné.":::

## <a name="query-twin-to-see-the-update"></a>Pro zobrazení aktualizace se zobrazí dvojitá vlákna dotazu.

Teď, když je vaše aplikace logiky vytvořená, by se událost s dvojitou aktualizací, kterou jste definovali v Návrháři Logic Apps, měla objevit při opakování každé tři sekundy. To znamená, že po třech sekundách byste měli být schopni zadat dotaz na vlákna a zobrazit nové opravené hodnoty.

Můžete se dotazovat na vlákna přes vaši metodu volby (například [vlastní klientská aplikace](tutorial-command-line-app.md), [ukázkovou aplikaci Průzkumníka digitálních vláken Azure](https://docs.microsoft.com/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/), sady [SDK a rozhraní](how-to-use-apis-sdks.md)příkazového [řádku](how-to-use-cli.md)). 

Další informace o dotazování instance digitálního vlákna Azure najdete v tématu [*Postup: dotazování na dvojitou graf*](how-to-query-graph.md).

## <a name="next-steps"></a>Další kroky

V tomto článku jste vytvořili aplikaci logiky, která pravidelně aktualizuje dvojitou hodnotu ve vaší instanci digitálního vlákna Azure pomocí opravy, kterou jste zadali. Můžete vyzkoušet výběr dalších rozhraní API ve vlastním konektoru a vytvořit Logic Apps pro nejrůznější akce v instanci.

Další informace o dostupných operacích rozhraní API a podrobnostech, které vyžadují, najdete v tématu [*Postupy: použití rozhraní API a sad SDK digitálních vláken Azure*](how-to-use-apis-sdks.md).