---
title: Vytváření pracovních postupů automatizace založených na plánech pomocí Azure
description: Kurz – vytvoření opakovaného pracovního postupu automatizace založeného na plánu, který se integruje mezi Cloud Services pomocí Azure Logic Apps.
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: tutorial
ms.custom: mvc
ms.date: 09/30/2020
ms.openlocfilehash: 3bf4ad12bab3e71675ff35203bf69526b3b8614f
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2020
ms.locfileid: "91574577"
---
# <a name="tutorial-create-schedule-based-and-recurring-automation-workflows-with-azure-logic-apps"></a>Kurz: vytvoření pracovních postupů založených na plánech a opakovaných automatizacích pomocí Azure Logic Apps

V tomto kurzu se dozvíte, jak sestavit ukázkovou [aplikaci logiky](../logic-apps/logic-apps-overview.md) , která automatizuje pracovní postup, který se spouští podle plánu opakování. Konkrétně Tato ukázková aplikace logiky kontroluje dobu trvání cesty, včetně provozu, mezi dvěma místy a běží každý den v týdnu ráno. Pokud čas překročí určitý limit, aplikace logiky vám pošle e-mail, který obsahuje dobu trvání cesty a dobu potřebnou k doručení do vašeho cíle. Pracovní postup obsahuje různé kroky, které začínají triggerem na základě plánu následovaným akcí map Bing, akcí datových operací, akcí toku řízení a akcí e-mailových oznámení.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření prázdné aplikace logiky
> * Přidejte Trigger opakování, který určuje plán pro vaši aplikaci logiky.
> * Přidejte akci map Bing, která získá dobu trvání cesty pro trasu.
> * Přidejte akci, která vytvoří proměnnou, převede dobu trvání cesty z sekund na minuty a uloží výsledek do proměnné.
> * Přidání podmínky, která porovná dobu cesty se zadaným limitem.
> * Přidejte akci, která vám pošle e-mail, pokud doba trvání cesty překročí limit.

Jakmile budete hotovi, vaše aplikace logiky bude na základní úrovni vypadat jako tento pracovní postup:

![Snímek obrazovky, který zobrazuje přehled vysoké úrovně pro příklad pracovního postupu aplikace logiky.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/check-travel-time-overview.png)

## <a name="prerequisites"></a>Požadavky

* Účet a předplatné Azure. Pokud předplatné nemáte, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* E-mailový účet od poskytovatele e-mailu, který podporuje Logic Apps, jako je například Office 365 Outlook, Outlook.com nebo Gmail. Pokud máte jiného poskytovatele, [tady se podívejte na seznam konektorů](/connectors/). V tomto rychlém startu se pro Office 365 Outlook používá pracovní nebo školní účet. Pokud používáte jiný e-mailový účet, obecné kroky zůstanou stejné, ale vaše uživatelské rozhraní se může mírně lišit.

  > [!IMPORTANT]
  > Pokud chcete použít konektor Gmail, můžou tento konektor používat jenom obchodní účty G-Suite bez omezení v Logic Apps. Pokud máte účet příjemce Gmail, můžete tento konektor použít jenom pro konkrétní služby schválené v Google, nebo můžete [vytvořit klientskou aplikaci Google pro ověřování pomocí konektoru Gmail](/connectors/gmail/#authentication-and-bring-your-own-application). Další informace najdete v tématu [zásady zabezpečení a ochrany osobních údajů pro konektory Google v Azure Logic Apps](../connectors/connectors-google-data-security-privacy-policy.md).

* K získání doby trvání cesty pro nějakou trasu potřebujete přístupový klíč k rozhraní API Map Bing. Pokud chcete tento klíč získat, řiďte se kroky [postupu získání klíče k Mapám Bing](/bingmaps/getting-started/bing-maps-dev-center-help/getting-a-bing-maps-key).

## <a name="create-your-logic-app"></a>Vytvoření aplikace logiky

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí přihlašovacích údajů svého účtu Azure. Na domovské stránce Azure vyberte **vytvořit prostředek**.

1. V nabídce Azure Marketplace vyberte aplikace **Integration**  >  **Logic**.

   ![Snímek obrazovky, který zobrazuje Azure Marketplace nabídku s vybranou možnost integrace a aplikace logiky](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-new-logic-app-resource.png)

1. V podokně **Aplikace logiky** zadejte zde popsané informace o aplikaci logiky, kterou chcete vytvořit.

   ![Snímek obrazovky, který zobrazuje podokno vytvoření aplikace logiky a informace, které se mají poskytnout pro novou aplikaci logiky](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-logic-app-settings.png)

   | Vlastnost | Hodnota | Popis |
   |----------|-------|-------------|
   | **Předplatné** | <*Azure – předplatné – název*> | Název vašeho předplatného Azure. Tento příklad používá `Pay-As-You-Go` . |
   | **Skupina prostředků** | LA-dobacesty-SP | Název [skupiny prostředků Azure](../azure-resource-manager/management/overview.md), která se používá k uspořádání souvisejících prostředků. Tento příklad vytvoří novou skupinu prostředků s názvem `LA-TravelTime-RG` . |
   | **Název** | LA-dobacesty | Název vaší aplikace logiky, který může obsahovat jenom písmena, číslice, spojovníky ( `-` ), podtržítka ( `_` ), kulaté závorky ( `(` , `)` ) a tečky ( `.` ). Tento příklad používá `LA-TravelTime` . |
   | **Umístění** | USA – západ | Oblast, kam se mají ukládat informace o aplikaci logiky Tento příklad používá `West US` . |
   | **Log Analytics** | Vypnuto | Pokud chcete zapnout protokolování diagnostiky, ponechte nastavení **Vypnuto**. |
   ||||

1. Až budete hotovi, vyberte **zkontrolovat + vytvořit**. Až Azure ověří informace o vaší aplikaci logiky, vyberte **vytvořit**.

1. Až Azure nasadí vaši aplikaci, vyberte **Přejít k prostředku**.

   Azure otevře podokno pro výběr šablony Logic Apps, ve kterém se zobrazí úvodní video, běžně používané triggery a vzory šablon aplikací logiky.

1. Posuňte se dolů v částech video a běžné triggery do části **šablony** a vyberte **prázdná aplikace logiky**.

   ![Snímek obrazovky, který zobrazuje podokno pro výběr šablony Logic Apps s vybranou volbou prázdná aplikace logiky](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-logic-app-template.png)

Dále přidejte [Trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts)opakování, který spustí pracovní postup na základě zadaného plánu. Každá aplikace logiky se musí spouštět triggerem, který se aktivuje při určité události nebo když nová data splní určitou podmínku. Další informace najdete v článku [Vytvoření první aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-the-recurrence-trigger"></a>Přidat Trigger opakování

1. Do vyhledávacího pole návrháře Logic Apps zadejte `recurrence` a vyberte aktivační událost s názvem **opakování**.

   ![Snímek obrazovky, který zobrazuje vyhledávací pole návrháře Logic Apps, které obsahuje hledaný termín "opakování" a v seznamu triggery se zobrazuje Trigger "opakování".](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-schedule-recurrence-trigger.png)

1. Na obrazci **opakování** vyberte tlačítko se **třemi tečkami** (**...**) a pak vyberte **Přejmenovat**. Přejmenujte trigger s tímto popisem: `Check travel time every weekday morning`

   ![Snímek obrazovky, který zobrazuje vybrané tlačítko se třemi tečkami, otevře se seznam nastavení a vybere se příkaz Přejmenovat.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/rename-recurrence-schedule-trigger.png)

1. V triggeru změňte tyto vlastnosti tak, jak je popsáno a znázorněno zde.

   ![Snímek obrazovky zobrazující změny intervalu a frekvence triggeru](./media/tutorial-build-scheduled-recurring-logic-app-workflow/change-interval-frequency.png)

   | Vlastnost | Požaduje se | Hodnota | Popis |
   |----------|----------|-------|-------------|
   | **Interval** | Ano | 1 | Počet intervalů, po které se má čekat mezi kontrolami |
   | **Frekvence** | Ano | Týden | Jednotka času pro opakování |
   |||||

1. V části **interval** a **frekvence**otevřete seznam **Přidat nový parametr** a vyberte tyto vlastnosti, které chcete přidat do triggeru.

   * **V tyto dny**
   * **V těchto hodinách**
   * **V těchto minutách**

   ![Snímek obrazovky, který zobrazuje otevřený seznam přidat nový a vybrané vlastnosti: "v těchto dnech", "v těchto hodinách" a "v těchto minutách".](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-trigger-properties.png)

1. Nyní nastavte hodnoty pro další vlastnosti, jak je znázorněno zde.

   ![Snímek obrazovky zobrazující další vlastnosti nastavené na hodnoty, jak je popsáno v následující tabulce.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/recurrence-trigger-property-values.png)

   | Vlastnost | Hodnota | Popis |
   |----------|-------|-------------|
   | **V tyto dny** | Pondělí, úterý, středa, čtvrtek, pátek | Toto nastavení je k dispozici pouze v případě, že jste nastavili **četnost** na **týden**. |
   | **V těchto hodinách** | 7,8,9 | Toto nastavení je dostupné jenom v případě, že jste nastavili **kmitočet** na **týden** nebo **den**. Pro toto opakování vyberte hodiny dne. Tento příklad se spouští v `7` `8` označení, a `9` -Hour. |
   | **V těchto minutách** | 0,15,30,45 | Toto nastavení je dostupné jenom v případě, že jste nastavili **kmitočet** na **týden** nebo **den**. Pro toto opakování vyberte minuty dne. Tento příklad začíná nulovou hodinovou značkou a spouští se každých 15 minut. |
   ||||

   Tento trigger se spouští každý pracovní den každých 15 minut, počínaje 7:00:00 a konče v 9:45:00. Oblast **Náhled** zobrazuje plán opakování. Další informace najdete v tématu [Plánování úloh a pracovních postupů](../connectors/connectors-native-recurrence.md) a [Akce a triggery pracovního postupu](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger).

1. Chcete-li nyní skrýt podrobnosti triggeru, sbalte tvar kliknutím dovnitř záhlaví obrazce.

   ![Snímek obrazovky, který zobrazuje sbalený obrazec triggeru](./media/tutorial-build-scheduled-recurring-logic-app-workflow/collapse-trigger-shape.png)

1. Uložte aplikaci logiky. Na panelu nástrojů návrháře vyberte **Uložit**.

Vaše aplikace logiky je teď v Azure Portal živá, ale neprovádí žádnou jinou než Trigger na základě zadaného plánu. Přidejte tedy akci, která bude reagovat na aktivaci triggeru.

## <a name="get-the-travel-time-for-a-route"></a>Získání doby trvání cesty pro trasu

Teď, když máte trigger, přidejte [akci](../logic-apps/logic-apps-overview.md#logic-app-concepts), pomocí které získáte dobu trvání cesty mezi dvěma místy. Služba Logic Apps poskytuje konektor k rozhraní API Map Bing, takže tyto informace můžete snadno získat. Než začnete s tímto úkolem, ujistěte se, že máte klíč rozhraní API Map Bing podle požadavků k tomuto kurzu.

1. V návrháři aplikace logiky pod triggerem opakování vyberte **Nový krok**.

1. V části **Zvolit operaci**vyberte **standardní**. Do vyhledávacího pole zadejte `bing maps` a vyberte akci s názvem **získat trasu**.

   ![Snímek obrazovky, který zobrazuje seznam "zvolit operaci" filtrovaný pomocí akcí mapy Bingu a vybrané akce získat trasu.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-get-route-action.png)

1. Pokud nemáte připojení k Mapám Bing, budete vyzváni k jeho vytvoření. Zadejte podrobnosti o připojení, jak je znázorněno a popsáno, a pak vyberte **vytvořit**.

   ![Snímek obrazovky, který zobrazuje okno připojení mapy Bing se zadaným názvem připojení a klíčem rozhraní API služby Bing Maps.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-maps-connection.png)

   | Vlastnost | Požaduje se | Hodnota | Popis |
   |----------|----------|-------|-------------|
   | **Název připojení** | Ano | PřipojeníMapyBing | Zadejte název připojení. Tento příklad používá `BingMapsConnection` . |
   | **Klíč rozhraní API** | Ano | <*Bing – Maps – klíč rozhraní API*> | Zadejte klíč rozhraní API pro mapy Bing, který jste dříve dostali. Pokud nemáte k dispozici klíč služby Mapy Bing, přečtěte si, [jak získat klíč](/bingmaps/getting-started/bing-maps-dev-center-help/getting-a-bing-maps-key). |
   |||||

1. Přejmenujte akci s tímto popisem: `Get route and travel time with traffic` .

1. V akci otevřete **seznam přidat nový parametr**a vyberte tyto vlastnosti.

   * **Optimalizace**
   * **Jednotka vzdálenosti**
   * **Způsob cestování**

   ![Snímek obrazovky se zobrazením "získat trasu..." akce s vybranými vlastnostmi "optimalizovat", "vzdálenost" a "cestovní režim".](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-bing-maps-action-properties.png) 

1. Nyní zadejte hodnoty pro zobrazené vlastnosti a popsány zde.

   ![Snímek obrazovky zobrazující další hodnoty vlastností pro akci získat trasu](./media/tutorial-build-scheduled-recurring-logic-app-workflow/get-route-action-settings.png) 

   | Vlastnost | Požaduje se | Hodnota | Popis |
   |----------|----------|-------|-------------|
   | **Bod na trase 1** | Ano | <*Začátek – umístění*> | Počátek vaší trasy. Tento příklad určuje ukázkovou počáteční adresu. |
   | **Bod na trase 2** | Ano | <*koncové umístění*> | Cíl vaší trasy. Tento příklad určuje ukázkovou cílovou adresu. |
   | **Optimalizace** | Ne | timeWithTraffic | Parametr k optimalizaci vaší trasy, jako je například vzdálenost, doba trvání cesty včetně dopravní situace atd. Vyberte hodnotu parametru **timeWithTraffic**. |
   | **Jednotka vzdálenosti** | Ne | <*vaše preference*> | Jednotka vzdálenosti použitá pro trasu. V tomto příkladu se jako jednotka používá **míle** . |
   | **Způsob cestování** | Ne | Autem | Způsob cestování pro danou trasu. Vyberte režim **řízení** . |
   |||||

   Další informace o těchto parametrech a hodnotách najdete v tématu [Výpočet trasy](/bingmaps/rest-services/routes/calculate-a-route).

1. Na panelu nástrojů návrháře vyberte **Uložit**.

Dále vytvořte proměnnou tak, aby bylo možné převést a uložit aktuální dobu trvání cesty v minutách, a nikoli v sekundách. Tímto způsobem se vyhnete opakování převodu a danou hodnotu snadněji využijete v dalších krocích. 

## <a name="create-a-variable-to-store-travel-time"></a>Vytvoření proměnné pro uložení doby cesty

V některých případech můžete chtít spustit operace s daty v pracovním postupu a potom použít výsledky v pozdějších akcích. Chcete-li uložit tyto výsledky, abyste je mohli snadno znovu použít nebo na ně odkazovat, můžete vytvořit proměnné, které tyto výsledky budou ukládat po zpracování. Proměnné můžete vytvářet pouze na nejvyšší úrovni v aplikaci logiky.

Ve výchozím nastavení akce **získat trasu** vrátí aktuální dobu trvání cesty k provozu v sekundách od vlastnosti **přenos doby trvání cesty** . Převodem této hodnoty na minuty a jejím uložením usnadníte její opakované využití bez nutnosti dalších převodů.

1. V návrháři v akci **získat trasu** vyberte **Nový krok**.

1. V části **Zvolit operaci**vyberte **předdefinovaná**. Do vyhledávacího pole zadejte `variables` a vyberte akci s názvem **inicializovat proměnnou**.

   ![Snímek obrazovky zobrazující vybranou akci inicializovat proměnnou](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-initialize-variable-action.png)

1. Přejmenujte tuto akci s tímto popisem: `Create variable to store travel time`

1. Zadejte tyto informace pro proměnnou, jak je znázorněno v této tabulce, a v krocích pod tabulkou:

   | Vlastnost | Požaduje se | Hodnota | Popis |
   |----------|----------|-------|-------------|
   | **Název** | Ano | dobacesty | Název proměnné. Tento příklad používá `travelTime` . |
   | **Typ** | Ano | Integer | Datový typ proměnné |
   | **Hodnota** | Ne | Výraz, který převede aktuální dobu trvání cesty z sekund na minuty (viz postup v této tabulce). | Počáteční hodnota proměnné |
   |||||

   1. Chcete-li vytvořit výraz pro vlastnost **Value** , klikněte do pole, aby se zobrazil seznam dynamického obsahu. V případě potřeby Rozšiřte prohlížeč, dokud se nezobrazí dynamický seznam. V seznamu dynamický obsah vyberte **výraz**, který ukazuje Editor výrazů.

      ![Snímek obrazovky, který zobrazuje akci "inicializovat proměnnou" s kurzorem uvnitř vlastnosti "value", která otevírá seznam dynamického obsahu.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings.png)

      Seznam dynamického obsahu zobrazuje výstupy z předchozích akcí, které jsou k dispozici pro výběr jako vstupy pro následné akce v pracovním postupu. Seznam dynamického obsahu obsahuje editor výrazů, který můžete použít k výběru funkcí, které ve výrazu provádějí operace. Tento editor výrazů je k dispozici pouze v seznamu dynamického obsahu.

   1. V editoru výrazů zadejte tento výraz: `div(,60)`

      ![Snímek obrazovky, který zobrazuje Editor výrazů se zadaným výrazem "div (, 60)".](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-2.png)

   1. V rámci výrazu umístěte kurzor do levé závorky (**(**) a čárky (**,**) a vyberte **dynamický obsah**.

      ![Snímek obrazovky, který ukazuje, kde umístit kurzor do výrazu div (, 60) s vybraným dynamickým obsahem](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-3.png)

   1. V seznamu dynamického obsahu vyberte v části vybrat hodnotu vlastnosti možnost **Doba trvání cesty provozu**.

      ![Snímek obrazovky, na které se zobrazuje vybraná hodnota vlastnosti provozování dob trvání cesty](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-4.png)

   1. Po vyřešení hodnoty vlastnosti uvnitř výrazu vyberte **OK**.

      ![Snímek obrazovky, který zobrazuje tlačítko "OK" jako vybrané.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-5.png)

      Vlastnost **Value** se teď zobrazuje tak, jak je znázorněno zde:

      ![Snímek obrazovky zobrazující vlastnost Value s vyřešeným výrazem](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-6.png)

1. Uložte aplikaci logiky.

Dál přidejte podmínku, která zkontroluje, zda je aktuální doba trvání cesty delší než určený limit.

## <a name="compare-the-travel-time-with-limit"></a>Porovnat dobu trvání cesty s omezením

1. V akci **vytvořit proměnnou pro uložení doby trvání cesty** vyberte **Nový krok**.

1. V části **Zvolit operaci**vyberte **předdefinovaná**. Do vyhledávacího pole zadejte `condition`. v seznamu akcí vyberte akci s názvem **Podmínka**.

   ![Snímek obrazovky zobrazující vybranou akci podmínka](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-condition-action.png)

1. Přejmenujte podmínku s použitím tohoto popisu: `If travel time exceeds limit`

1. Vytvořte podmínku, která kontroluje, zda hodnota vlastnosti **dobacesty** překračuje zadaný limit, jak je popsáno a zobrazen zde:

   1. V podmínce na levé straně podmínky klikněte do pole **zvolit hodnotu** .

   1. V seznamu dynamický obsah, který se zobrazí, vyberte v části **proměnné**možnost vlastnost s názvem **dobacesty**.

      ![Snímek obrazovky zobrazující pole "zvolit hodnotu" na levé straně podmínky s otevřeným seznamem dynamického obsahu a vybranou vlastností "Dobacesty".](./media/tutorial-build-scheduled-recurring-logic-app-workflow/build-condition-left-side.png)

   1. V prostřední porovnávací poli vyberte operátor s názvem **je větší než**.

   1. Na pravé straně podmínky v poli **zvolit hodnotu** zadejte tento limit: `15`

      Jakmile budete hotovi, bude podmínka vypadat jako v tomto příkladu:

      ![Snímek obrazovky, který zobrazuje stav dokončení pro porovnání doby trvání cesty k zadanému limitu.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/build-condition-check-travel-time.png)

1. Uložte aplikaci logiky.

V dalším kroku přidejte akci, která se spustí, když doba trvání cesty překročí váš limit.

## <a name="send-email-when-limit-exceeded"></a>Odeslání e-mailu při překročení limitu

Teď přidejte akci, která vám pošle e-mail, když doba trvání cesty překročí váš limit. Tento e-mail zahrnuje aktuální dobu trvání cesty a čas navíc nutný k dopravě po zadané trase.

1. V poli **pravdivá** větev podmínky vyberte **přidat akci**.

1. V části **Zvolit operaci**vyberte **standardní**. Do vyhledávacího pole zadejte `send email`. Seznam vrátí mnoho výsledků, takže vám pomůžou filtrovat seznam, nejdřív vybrat e-mailový konektor, který chcete.

   Pokud máte například e-mailový účet Outlooku, vyberte konektor pro typ účtu:

   * U pracovních nebo školních účtů Azure vyberte **Office 365 Outlook**.
   * U osobních účtů Microsoft, vyberte **Outlook.com**.

   Tento příklad pokračuje tím, že vyberete Office 365 Outlook.

   ![Snímek obrazovky, který zobrazuje "zvolit seznam operací" se standardní kategorií a konektor Office 365 Outlook je vybraný.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-action-send-email.png)

1. Po zobrazení akcí konektoru vyberte akci, která odesílá e-mail, například:

   ![Snímek obrazovky, na kterém se zobrazuje vybraná akce Odeslat e-mail](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-send-email-action.png)

1. Pokud ještě nemáte připojení, přihlaste se a po zobrazení výzvy ověřte přístup k e-mailovému účtu.

   Azure Logic Apps vytvoří připojení k vašemu e-mailovému účtu.

1. Přejmenujte akci s tímto popisem: `Send email with travel time`

1. Do pole **Komu** zadejte e-mailovou adresu příjemce. Pro účely testování můžete použít svou e-mailovou adresu.

1. U vlastnosti **subject (předmět** ) zadejte předmět e-mailu a zahrňte proměnnou **dobacesty** pomocí následujících kroků:

   1. Zadejte text `Current travel time (minutes):` s koncovou mezerou. Nechejte kurzor v poli **Předmět** , aby byl seznam dynamického obsahu otevřený.

   1. Z dynamického obsahu v záhlaví **proměnné** vyberte **Zobrazit více** , aby se zobrazila proměnná s názvem **dobacesty** .

      ![Snímek obrazovky, který zobrazuje seznam dynamického obsahu s oddílem Variables a vybraným seznamem zobrazit další.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/find-travelTime-variable.png)

      > [!NOTE]
      > Seznam dynamického obsahu automaticky nezobrazuje proměnnou **dobacesty** , protože vlastnost **Subject** očekává hodnotu řetězce, zatímco **dobacesty** je celočíselná hodnota.

      ![Snímek obrazovky, který zobrazuje seznam dynamického obsahu s vybranou proměnnou "Dobacesty".](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-travelTime-variable.png)

1. Pro vlastnost **text** zadejte obsah e-mailové zprávy pomocí následujících kroků:

   1. Zadejte text `Add extra travel time (minutes):` s koncovou mezerou. Umístěte kurzor do pole **text** tak, aby byl seznam dynamického obsahu otevřený.

   1. V seznamu dynamický obsah vyberte **výraz**, který ukazuje Editor výrazů.

      ![Snímek obrazovky, který zobrazuje seznam dynamického obsahu s vybraným výrazem "Expression".](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings.png)

   1. V editoru výrazů zadejte, `sub(,15)` abyste mohli vypočítat počet minut, které překračují limit: 

      ![Snímek obrazovky zobrazující Editor výrazů se zadaným výrazem sub (, 15)](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-2.png)

   1. V rámci výrazu umístěte kurzor do levé závorky (**(**) a čárky (**,**) a vyberte **dynamický obsah**.

      ![Snímek obrazovky, který ukazuje, kam umístit kurzor do výrazu "sub (, 15") s vybraným dynamickým obsahem.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-3.png)

   1. V části **Proměnné** vyberte **dobacesty**.

      ![Snímek obrazovky, který zobrazuje seznam dynamického obsahu s vybranou proměnnou "Dobacesty".](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-4.png)

   1. Až se vlastnost vyřeší uvnitř výrazu, vyberte **OK**.

      ![Snímek obrazovky zobrazující seznam dynamického obsahu a "OK".](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-5.png)

      Vlastnost **text** se teď zobrazuje tak, jak je znázorněno zde:

      ![Snímek obrazovky, který zobrazuje seznam dynamického obsahu s výrazem vyřešeným ve vlastnosti "tělo" e-mailové akce.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-6.png)

1. Uložte aplikaci logiky.

Potom otestujte a spusťte aplikaci logiky, která teď vypadá podobně jako v tomto příkladu:

![Snímek obrazovky, který ukazuje dokončený ukázkový pracovní postup aplikace logiky](./media/tutorial-build-scheduled-recurring-logic-app-workflow/check-travel-time-finished.png)

## <a name="run-your-logic-app"></a>Spuštění aplikace logiky

Pokud chcete aplikaci logiky spustit ručně, na panelu nástrojů návrháře vyberte **Spustit**.

* Pokud aktuální doba trvání cesty zůstane v rámci vašeho limitu, aplikace logiky neprovede nic jiného a před opětovnou kontrolou nečeká nebo další interval. 

* Pokud aktuální doba trvání cesty překročí limit, dostanete e-mail s aktuální dobou trvání cesty a počtem minut nad vaším limitem. Tady je příklad e-mailu odeslaného aplikací logiky:

  ![Snímek obrazovky, který zobrazuje příklad e-mailu, který oznamuje aktuální dobu trvání cesty a další dobu trvání cesty, která překračuje zadaný limit.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/received-example-email-notification.png)

  > [!TIP]
  > Pokud neobdržíte žádné e-maily, zkontrolujte složku s nevyžádanou poštou. Váš filtr nevyžádané pošty může tento typ e-mailů přesměrovávat. Pokud si nejste jisti správným spuštěním aplikace logiky, přečtěte si téma [Řešení potíží s aplikací logiky](../logic-apps/logic-apps-diagnosing-failures.md).

Gratulujeme, právě jste vytvořili a spustili opakující se aplikaci logiky založenou na plánu. 

Pokud chcete vytvořit další aplikace logiky, které používají Trigger **opakování** , podívejte se na tyto šablony, které jsou k dispozici po vytvoření aplikace logiky:

* Každodenní odesílání připomenutí e-mailem
* Odstranění starších objektů blob Azure
* Přidání zprávy do fronty Azure Storage

## <a name="clean-up-resources"></a>Vyčištění prostředků

Vaše aplikace logiky pokračuje běžet, dokud aplikaci nezakážete nebo neodstraníte. Pokud už ukázkovou aplikaci logiky nepotřebujete, odstraňte skupinu prostředků, která obsahuje vaši aplikaci logiky a související prostředky.

1. Do vyhledávacího pole Azure Portal zadejte název skupiny prostředků, kterou jste vytvořili. Z výsledků v části **skupiny prostředků**vyberte skupinu prostředků.

   V tomto příkladu se vytvořila skupina prostředků s názvem `LA-TravelTime-RG` . 

   ![Snímek obrazovky, který zobrazuje pole Azure Search s uvedením "La-cestovné-Time-RG" a * * LA-Dobacesty-RG * * vybrány.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/find-resource-group.png)

   > [!TIP]
   > Pokud se na domovské stránce Azure zobrazuje skupina prostředků v části **nedávné prostředky**, můžete vybrat skupinu z domovské stránky.

1. V nabídce skupina prostředků ověřte, že je vybraná možnost **Přehled** . Na panelu nástrojů v podokně **Přehled** vyberte **Odstranit skupinu prostředků**. 

   ![Snímek obrazovky, který zobrazuje podokno "Přehled" skupiny prostředků a na panelu nástrojů, je vybrána možnost odstranit skupinu prostředků.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/delete-resource-group.png)

1. V podokně potvrzení zadejte název skupiny prostředků a vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili aplikaci logiky, která kontroluje provoz na základě zadaného plánu (v den ráno) a provede akci (pošle e-mail), pokud doba trvání cesty překročí stanovený limit. Nyní se naučíte, jak vytvořit aplikaci logiky, která posílá žádosti o schválení prostřednictvím integrace služeb Azure, služeb Microsoft a dalších aplikací SaaS (software jako služba).

> [!div class="nextstepaction"]
> [Správa požadavků na seznam adresátů](../logic-apps/tutorial-process-mailing-list-subscriptions-workflow.md)
