---
title: Vytváření automatizovaných pracovních postupů založených na plánu
description: Kurz – vytvoření opakovaného a automatizovaného pracovního postupu založeného na plánu pomocí Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: tutorial
ms.custom: mvc
ms.date: 09/12/2019
ms.openlocfilehash: 8c9239196d26bcd4967b685fa7970c4d3bd706d4
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2020
ms.locfileid: "90030527"
---
# <a name="tutorial-create-automated-schedule-based-recurring-workflows-by-using-azure-logic-apps"></a>Kurz: vytvoření automatizovaných pracovních postupů založených na plánech pomocí Azure Logic Apps

V tomto kurzu se dozvíte, jak vytvořit [aplikaci logiky](../logic-apps/logic-apps-overview.md) a automatizovat opakovaný pracovní postup, který běží podle plánu. Konkrétně se tato ukázková aplikace logiky spouští každý den v týdnu ráno a kontroluje dobu trvání cesty, včetně provozu, mezi dvěma místy. Pokud doba překročí určitou hranici, odešle aplikace logiky e-mail s dobou cesty a časem navíc potřebným k dosažení cíle cesty.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření prázdné aplikace logiky
> * Přidejte Trigger opakování, který určuje plán pro vaši aplikaci logiky.
> * Přidejte akci map Bing, která získá dobu trvání cesty pro trasu.
> * Přidejte akci, která vytvoří proměnnou, převede dobu trvání cesty z sekund na minuty a uloží výsledek do proměnné.
> * Přidání podmínky, která porovná dobu cesty se zadaným limitem.
> * Přidejte akci, která vám pošle e-mail, pokud doba trvání cesty překročí limit.

Jakmile budete hotovi, vaše aplikace logiky bude na základní úrovni vypadat jako tento pracovní postup:

![Přehled pracovního postupu vysoké úrovně aplikace logiky](./media/tutorial-build-scheduled-recurring-logic-app-workflow/check-travel-time-overview.png)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud předplatné nemáte, [Zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.

* E-mailový účet od poskytovatele e-mailu, který podporuje Logic Apps, jako je například Office 365 Outlook, Outlook.com nebo Gmail. Pokud máte jiného poskytovatele, [tady se podívejte na seznam konektorů](/connectors/). V tomto rychlém startu se používá pracovní nebo školní účet. Pokud používáte jiný e-mailový účet, obecné kroky zůstanou stejné, ale vaše uživatelské rozhraní se může mírně lišit.

  > [!IMPORTANT]
  > Pokud chcete použít konektor Gmail, můžou tento konektor používat jenom obchodní účty G-Suite bez omezení v Logic Apps. Pokud máte účet příjemce Gmail, můžete tento konektor použít jenom pro konkrétní služby schválené v Google, nebo můžete [vytvořit klientskou aplikaci Google pro ověřování pomocí konektoru Gmail](/connectors/gmail/#authentication-and-bring-your-own-application). Další informace najdete v tématu [zásady zabezpečení a ochrany osobních údajů pro konektory Google v Azure Logic Apps](../connectors/connectors-google-data-security-privacy-policy.md).

* K získání doby trvání cesty pro nějakou trasu potřebujete přístupový klíč k rozhraní API Map Bing. Pokud chcete tento klíč získat, řiďte se kroky [postupu získání klíče k Mapám Bing](/bingmaps/getting-started/bing-maps-dev-center-help/getting-a-bing-maps-key).

## <a name="create-your-logic-app"></a>Vytvoření aplikace logiky

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí přihlašovacích údajů svého účtu Azure.

1. V hlavní nabídce Azure vyberte **vytvořit prostředek**  >  **Integration**  >  **Logic App**.

   ![Vytvoření prostředku aplikace logiky](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-new-logic-app-resource.png)

1. V části **Vytvořit aplikaci logiky** zadejte podrobnosti o vaší aplikaci logiky podle následujícího obrázku a popisu. Po dokončení vyberte **Vytvořit**.

   ![Zadání informací o vaší aplikaci logiky](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-logic-app-settings.png)

   | Vlastnost | Hodnota | Popis |
   |----------|-------|-------------|
   | **Název** | LA-dobacesty | Název vaší aplikace logiky, který může obsahovat jenom písmena, číslice, spojovníky ( `-` ), podtržítka ( `_` ), kulaté závorky ( `(` , `)` ) a tečky ( `.` ). V tomto příkladu se používá "LA-Dobacesty". |
   | **Předplatné** | <*Vaše předplatné – Azure-Subscription-Name*> | Název vašeho předplatného Azure |
   | **Skupina prostředků** | LA-dobacesty-SP | Název [skupiny prostředků Azure](../azure-resource-manager/management/overview.md), která se používá k uspořádání souvisejících prostředků. V tomto příkladu se používá "LA-Dobacesty-RG". |
   | **Umístění** | USA – západ | TNelze načíst oblast, kam se mají ukládat informace o aplikaci logiky V tomto příkladu se používá "Západní USA". |
   | **Log Analytics** | Vypnuto | Pokud chcete zapnout protokolování diagnostiky, ponechte nastavení **Vypnuto**. |
   ||||

1. Až Azure nasadí vaši aplikaci, vyberte na panelu nástrojů Azure možnost **oznámení**  >  **Přejít na prostředek** pro vaši nasazenou aplikaci logiky.

   ![Přejít na nový prostředek aplikace logiky](./media/tutorial-build-scheduled-recurring-logic-app-workflow/go-to-new-logic-app-resource.png)

   Nebo můžete vyhledat a vybrat aplikaci logiky zadáním názvu do vyhledávacího pole.

   Otevře se Návrhář Logic Apps a zobrazí se stránka s úvodním videem a běžně používanými triggery a vzorci aplikace logiky. V oblasti **Šablony** vyberte **Prázdná aplikace logiky**.

   ![Vybrat šablonu prázdná aplikace logiky](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-logic-app-template.png)

Dále přidejte [Trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts)opakování, který se aktivuje podle zadaného plánu. Každá aplikace logiky se musí spouštět triggerem, který se aktivuje při určité události nebo když nová data splní určitou podmínku. Další informace najdete v článku [Vytvoření první aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-the-recurrence-trigger"></a>Přidat Trigger opakování

1. V návrháři aplikace logiky do vyhledávacího pole zadejte "opakování" jako filtr. V seznamu **triggery** vyberte Trigger **opakování** .

   ![Přidat aktivační událost opakování](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-schedule-recurrence-trigger.png)

1. Na obrazci **opakování** vyberte tlačítko se **třemi tečkami** (**...**) a pak vyberte **Přejmenovat**. Přejmenujte trigger s tímto popisem: `Check travel time every weekday morning`

   ![Přejmenování popisu triggeru opakování](./media/tutorial-build-scheduled-recurring-logic-app-workflow/rename-recurrence-schedule-trigger.png)

1. V triggeru změňte tyto vlastnosti.

   ![Změna intervalu a četnosti triggeru opakování](./media/tutorial-build-scheduled-recurring-logic-app-workflow/change-interval-frequency.png)

   | Vlastnost | Požaduje se | Hodnota | Popis |
   |----------|----------|-------|-------------|
   | **Interval** | Yes | 1 | Počet intervalů, po které se má čekat mezi kontrolami |
   | **Frekvence** | Yes | Týden | Jednotka času pro opakování |
   |||||

1. V části **interval** a **frekvence**otevřete seznam **Přidat nový parametr** a vyberte tyto vlastnosti, které chcete přidat do triggeru.

   * **V tyto dny**
   * **V těchto hodinách**
   * **V těchto minutách**

   ![Přidat vlastnosti pro aktivační událost opakování](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-trigger-properties.png)

1. Nyní nastavte hodnoty pro další vlastnosti, jak je znázorněno zde.

   ![Zadání podrobností o plánu a opakování](./media/tutorial-build-scheduled-recurring-logic-app-workflow/recurrence-trigger-property-values.png)

   | Vlastnost | Hodnota | Popis |
   |----------|-------|-------------|
   | **V tyto dny** | Pondělí, úterý, středa, čtvrtek, pátek | Dostupné, pouze pokud je **Frekvence** nastavená na Týden. |
   | **V těchto hodinách** | 7,8,9 | Dostupné, pouze pokud je **Frekvence** nastavená na Týden nebo Den. Vyberte hodiny, kdy se v průběhu dne spustí toto opakování. Tento příklad se spustí v celou 7., 8. a 9. hodinu. |
   | **V těchto minutách** | 0,15,30,45 | Dostupné, pouze pokud je **Frekvence** nastavená na Týden nebo Den. Vyberte minuty, kdy se v průběhu dne spustí toto opakování. Tento příklad se spouští každých 15 minut od nulté celé hodiny. |
   ||||

   Tento trigger se spouští každý pracovní den každých 15 minut, počínaje 7:00:00 a konče v 9:45:00. Oblast **Náhled** zobrazuje plán opakování. Další informace najdete v tématu [Plánování úloh a pracovních postupů](../connectors/connectors-native-recurrence.md) a [Akce a triggery pracovního postupu](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger).

1. Pokud chcete podrobnosti o triggeru prozatím skrýt, klikněte do záhlaví tvaru.

   ![Tvar sbalení pro skrytí podrobností](./media/tutorial-build-scheduled-recurring-logic-app-workflow/collapse-trigger-shape.png)

1. Uložte aplikaci logiky. Na panelu nástrojů návrháře vyberte **Uložit**.

Vaše aplikace logiky je teď v provozu, ale kromě opakování nic dalšího nedělá. Přidejte tedy akci, která bude reagovat na aktivaci triggeru.

## <a name="get-the-travel-time-for-a-route"></a>Získání doby trvání cesty pro trasu

Teď, když máte trigger, přidejte [akci](../logic-apps/logic-apps-overview.md#logic-app-concepts), pomocí které získáte dobu trvání cesty mezi dvěma místy. Služba Logic Apps poskytuje konektor k rozhraní API Map Bing, takže tyto informace můžete snadno získat. Než začnete s tímto úkolem, ujistěte se, že máte klíč rozhraní API Map Bing podle požadavků k tomuto kurzu.

1. V návrháři aplikace logiky pod triggerem vyberte **Nový krok**.

1. V části **zvolit akci**vyberte **standardní**. Do vyhledávacího pole zadejte jako filtr "mapy Bing" a vyberte akci **získat trasu** .

   ![Vybrat akci získat trasu](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-get-route-action.png)

1. Pokud nemáte připojení k Mapám Bing, budete vyzváni k jeho vytvoření. Zadejte tyto podrobnosti o připojení a vyberte **vytvořit**.

   ![Vytvořit připojení k rozhraní API služby Bing Maps](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-maps-connection.png)

   | Vlastnost | Požaduje se | Hodnota | Popis |
   |----------|----------|-------|-------------|
   | **Název připojení** | Yes | PřipojeníMapyBing | Zadejte název připojení. V tomto příkladu se používá "BingMapsConnection". |
   | **Klíč rozhraní API** | Yes | <*váš klíč-Bing-Maps-Key*> | Zadejte klíč Map Bing, který jste dříve dostali. Pokud nemáte k dispozici klíč služby Mapy Bing, přečtěte si, [jak získat klíč](/bingmaps/getting-started/bing-maps-dev-center-help/getting-a-bing-maps-key). |
   |||||

1. Přejmenujte akci s tímto popisem: `Get route and travel time with traffic`

1. V akci otevřete **seznam přidat nový parametr**a vyberte tyto vlastnosti, které chcete přidat k akci.

   * **Optimalizace**
   * **Jednotka vzdálenosti**
   * **Způsob cestování**

   ![Přidat vlastnosti do akce získat trasu](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-bing-maps-action-properties.png) 

1. Nyní nastavte hodnoty vlastností akce, jak je znázorněno a popsáno zde.

   ![Zadejte podrobnosti pro akci získat trasu.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/get-route-action-settings.png) 

   | Vlastnost | Požaduje se | Hodnota | Popis |
   |----------|----------|-------|-------------|
   | **Bod na trase 1** | Yes | <*Začátek – umístění*> | Počátek vaší cesty |
   | **Bod na trase 2** | Yes | <*koncové umístění*> | Cíl vaší trasy |
   | **Optimalizace** | No | timeWithTraffic | Parametr k optimalizaci vaší trasy, jako je například vzdálenost, doba trvání cesty včetně dopravní situace atd. Vyberte parametr "timeWithTraffic". |
   | **Jednotka vzdálenosti** | No | <*vaše preference*> | Jednotka vzdálenosti použitá pro trasu. V tomto příkladu se jako jednotka používá "km". |
   | **Způsob cestování** | No | Autem | Způsob cestování pro danou trasu. Vyberte režim "řízení". |
   ||||

   Další informace o těchto parametrech najdete v tématu [Výpočet trasy](/bingmaps/rest-services/routes/calculate-a-route).

1. Uložte aplikaci logiky.

Dále vytvořte proměnnou tak, aby bylo možné převést a uložit aktuální dobu trvání cesty v minutách, a nikoli v sekundách. Tímto způsobem se vyhnete opakování převodu a danou hodnotu snadněji využijete v dalších krocích. 

## <a name="create-a-variable-to-store-travel-time"></a>Vytvoření proměnné pro uložení doby cesty

V některých případech můžete chtít spustit operace s daty v pracovním postupu a potom použít výsledky v pozdějších akcích. Pokud chcete výsledky uložit, abyste je mohli snadno opakovaně používat nebo na ně odkazovat, můžete vytvořit proměnné, které výsledky po jejich zpracování uloží. Proměnné můžete vytvářet pouze na nejvyšší úrovni v aplikaci logiky.

Ve výchozím nastavení vrátí předchozí akce **získat trasu** aktuální dobu trvání cesty s provozem v sekundách od vlastnosti **přenos doby trvání cesty** . Převodem této hodnoty na minuty a jejím uložením usnadníte její opakované využití bez nutnosti dalších převodů.

1. V akci **získat trasu** vyberte **Nový krok**.

1. V části **zvolit akci**vyberte **předdefinovaná**. Do vyhledávacího pole zadejte "Variables" a vyberte akci **inicializovat proměnnou** .

   ![Vybrat akci inicializovat proměnnou](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-initialize-variable-action.png)

1. Přejmenujte tuto akci s tímto popisem: `Create variable to store travel time`

1. Zadejte podrobnosti pro danou proměnnou tohoto popisu:

   | Vlastnost | Požaduje se | Hodnota | Popis |
   |----------|----------|-------|-------------|
   | **Název** | Yes | dobacesty | Název proměnné. V tomto příkladu se používá "Dobacesty". |
   | **Typ** | Yes | Integer | Datový typ proměnné |
   | **Hodnota** | No| Výraz, který převede aktuální dobu trvání cesty ze sekund na minuty (viz postup pod touto tabulkou) | Počáteční hodnota proměnné |
   ||||

   1. Chcete-li vytvořit výraz pro vlastnost **Value** , klikněte do pole, aby se zobrazil seznam dynamického obsahu. V případě potřeby zvětšete okno prohlížeče, dokud se seznam nezobrazí. V seznamu dynamický obsah vyberte možnost **výraz**.

      ![Zadání informací pro akci inicializovat proměnnou](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings.png)

      Po kliknutí do některých textových polí se zobrazí seznam dynamického obsahu. Tento seznam obsahuje všechny vlastnosti z předchozích akcí, které můžete použít jako vstupy v pracovním postupu. Seznam dynamického obsahu má Editor výrazů, kde můžete vybrat funkce pro spuštění operací. Tento editor výrazů se zobrazuje jenom v seznamu dynamického obsahu.

   1. V editoru výrazů zadejte tento výraz: `div(,60)`

      ![Zadejte tento výraz: div(,60)](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-2.png)

   1. Umístěte kurzor dovnitř výrazu mezi levou závorku (**(**) a čárku (**,**). 
   Vyberte **dynamický obsah**.

      ![Pozice kurzoru, výběr dynamického obsahu](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-3.png)

   1. V seznamu dynamického obsahu vyberte možnost **Doba trvání cesty s provozem**.

      ![Vyberte vlastnost přenos doby trvání cesty.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-4.png)

   1. Po vyřešení hodnoty vlastnosti uvnitř výrazu vyberte **OK**.

      ![Pokud chcete dokončit vytváření výrazu, vyberte OK.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-5.png)

      Vlastnost **Value** se teď zobrazuje tak, jak je znázorněno zde:

      ![Vlastnost Value se zobrazuje s vyřešeným výrazem.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-6.png)

1. Uložte aplikaci logiky.

Dál přidejte podmínku, která zkontroluje, zda je aktuální doba trvání cesty delší než určený limit.

## <a name="compare-the-travel-time-with-limit"></a>Porovnat dobu trvání cesty s omezením

1. V předchozí akci vyberte **Nový krok**.

1. V části **zvolit akci**vyberte **předdefinovaná**. Do vyhledávacího pole zadejte jako filtr "podmínka". V seznamu akce vyberte akci **Podmínka** .

   ![Vybrat akci podmínka](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-condition-action.png)

1. Přejmenujte podmínku s použitím tohoto popisu: `If travel time exceeds limit`

1. Vytvořte podmínku, která kontroluje, zda hodnota vlastnosti **dobacesty** překračuje zadaný limit, jak je popsáno a zobrazen zde:

   1. V podmínce klikněte do pole **zvolit hodnotu** na levé straně podmínky.

   1. V seznamu dynamický obsah, který se zobrazí, vyberte v části **proměnné**vlastnost **dobacesty** .

      ![Sestavit levou stranu podmínky](./media/tutorial-build-scheduled-recurring-logic-app-workflow/build-condition-left-side.png)

   1. V poli prostřední porovnání vyberte operátor **je větší než** .

   1. V poli **zvolit hodnotu** na pravé straně podmínky zadejte tento limit: `15`

      Jakmile budete hotovi, bude podmínka vypadat jako v tomto příkladu:

      ![Dokončená podmínka pro kontrolu doby trvání cesty](./media/tutorial-build-scheduled-recurring-logic-app-workflow/build-condition-check-travel-time.png)

1. Uložte aplikaci logiky.

V dalším kroku přidejte akci, která se spustí, když doba trvání cesty překročí váš limit.

## <a name="send-email-when-limit-exceeded"></a>Odeslání e-mailu při překročení limitu

Nyní přidáte akci, která odešle e-mail, pokud doba trvání cesty překročí daný limit. Tento e-mail zahrnuje aktuální dobu trvání cesty a čas navíc nutný k dopravě po zadané trase.

1. V větvi podmínka **Pokud je true** vyberte **přidat akci**.

1. V části **zvolit akci**vyberte **standardní**. Do vyhledávacího pole zadejte "Odeslat e-mail". Seznam vrátí mnoho výsledků, takže nejdřív vyberte e-mailový konektor, který chcete, třeba:

   ![Vyberte e-mailový konektor, který chcete.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-action-send-email.png)

   * U pracovních nebo školních účtů Azure vyberte **Office 365 Outlook**.
   * U osobních účtů Microsoft, vyberte **Outlook.com**.

1. Když se zobrazí akce konektoru, vyberte Odeslat akci e-mailu, kterou chcete použít, například:

   ![Výběr akce „Odeslat e-mail“](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-send-email-action.png)

1. Pokud ještě nemáte připojení, budete vyzváni k přihlášení ke svému e-mailovému účtu.

   Logic Apps vytvoří připojení k e-mailovému účtu.

1. Přejmenujte akci s tímto popisem: `Send email with travel time`

1. Do pole **Komu** zadejte e-mailovou adresu příjemce. Pro účely testování použijte svou vlastní e-mailovou adresu.

1. V poli **Předmět** zadejte předmět e-mailu a zahrňte proměnnou **dobacesty**.

   1. Zadejte text `Current travel time (minutes):` s koncovou mezerou. 

   1. V seznamu dynamického obsahu v části **proměnné**vyberte **Zobrazit další**.

      ![Najít proměnnou "Dobacesty"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/find-travelTime-variable.png)

   1. Po zobrazení **dobacesty** v části **proměnné**vyberte **dobacesty**.

      ![Zadání textu předmětu a výrazu, který vrátí dobu trvání cesty](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-travelTime-variable.png)

1. Do pole **Text** zadejte obsah e-mailu.

   1. Zadejte text `Add extra travel time (minutes):` s koncovou mezerou.

   1. V seznamu dynamický obsah vyberte možnost **výraz**.

      ![Sestavení výrazu pro text e-mailu](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings.png)

   1. V editoru výrazů zadejte tento výraz tak, abyste spočítali počet minut, které překračují daný limit: ```sub(,15)```

      ![Zadání výrazu k výpočtu minut navíc z doby trvání cesty](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-2.png)

   1. Umístěte kurzor dovnitř výrazu mezi levou závorku (**(**) a čárku (**,**). Vyberte **dynamický obsah**.

      ![Další vytváření výrazu k výpočtu minut navíc z doby trvání cesty](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-3.png)

   1. V části **Proměnné** vyberte **dobacesty**.

      ![Vyberte vlastnost "Dobacesty", která se má použít ve výrazu](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-4.png)

   1. Až se vlastnost vyřeší uvnitř výrazu, vyberte **OK**.

      ![Po vyřešení vlastnosti "tělo" vyberte možnost OK.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-5.png)

      Vlastnost **text** se teď zobrazuje tak, jak je znázorněno zde:

      ![Byla vyřešena vlastnost "tělo" ve výrazu](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-6.png)

1. Uložte aplikaci logiky.

V dalším kroku otestujte aplikaci logiky, která teď vypadá podobně jako v tomto příkladu:

![Dokončení ukázkového pracovního postupu aplikace logiky](./media/tutorial-build-scheduled-recurring-logic-app-workflow/check-travel-time-finished.png)

## <a name="run-your-logic-app"></a>Spuštění aplikace logiky

Pokud chcete aplikaci logiky spustit ručně, na panelu nástrojů návrháře vyberte **Spustit**.

* Pokud aktuální doba trvání cesty zůstane v rámci vašeho limitu, aplikace logiky neprovede nic jiného a před opětovnou kontrolou nečeká nebo další interval. 

* Pokud aktuální doba trvání cesty překročí limit, dostanete e-mail s aktuální dobou trvání cesty a počtem minut nad vaším limitem. Tady je příklad e-mailu odeslaného aplikací logiky:

![Příklad odeslaného e-mailu, který ukazuje dobu trvání cesty](./media/tutorial-build-scheduled-recurring-logic-app-workflow/received-example-email-notification.png)

Pokud neobdržíte žádné e-maily, zkontrolujte složku s nevyžádanou poštou. Váš filtr nevyžádané pošty může tento typ e-mailů přesměrovávat. Pokud si nejste jisti správným spuštěním aplikace logiky, přečtěte si téma [Řešení potíží s aplikací logiky](../logic-apps/logic-apps-diagnosing-failures.md).

Gratulujeme, právě jste vytvořili a spustili opakující se aplikaci logiky založenou na plánu. 

Pokud chcete vytvořit další aplikace logiky, které používají Trigger **opakování** , podívejte se na tyto šablony, které jsou k dispozici po vytvoření aplikace logiky:

* Každodenní odesílání připomenutí e-mailem
* Odstranění starších objektů blob Azure
* Přidání zprávy do fronty Azure Storage

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už ukázkovou aplikaci logiky nepotřebujete, odstraňte skupinu prostředků, která obsahuje vaši aplikaci logiky a související prostředky. 

1. V hlavní nabídce Azure přejděte na **Skupiny prostředků** a vyberte skupinu prostředků pro vaši aplikaci logiky.

1. V nabídce skupina prostředků vyberte **Přehled**  >  **Odstranit skupinu prostředků**. 

   ![Přehled > Odstranit skupinu prostředků](./media/tutorial-build-scheduled-recurring-logic-app-workflow/delete-resource-group.png)

1. Jako potvrzení zadejte název skupiny prostředků a vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili aplikaci logiky, která kontroluje dopravní situaci podle zadaného plánu (pracovní dny ráno) a provádí akci (zasílá e-mail), pokud doba trvání cesty překročí určený limit. Dále se naučíte sestavit aplikaci logiky, která odesílá na seznam adresátů žádosti o schválení s využitím integrace služeb Azure, služeb Microsoft a dalších aplikací SaaS.

> [!div class="nextstepaction"]
> [Správa požadavků na seznam adresátů](../logic-apps/tutorial-process-mailing-list-subscriptions-workflow.md)
