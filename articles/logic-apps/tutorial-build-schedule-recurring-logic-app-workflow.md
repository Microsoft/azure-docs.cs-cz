---
title: Vytváření automatizovaných pracovních postupů založených na plánu
description: Kurz – vytvoření opakovaného automatizovaného pracovního postupu založeného na plánu pomocí aplikací Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: tutorial
ms.custom: mvc
ms.date: 09/12/2019
ms.openlocfilehash: 17802228c8f08e3c8f1533296e2d39080f6f8b7a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "75456619"
---
# <a name="tutorial-create-automated-schedule-based-recurring-workflows-by-using-azure-logic-apps"></a>Kurz: Vytváření automatizovaných opakovaných pracovních postupů založených na plánu pomocí aplikací Azure Logic Apps

Tento kurz ukazuje, jak vytvořit [aplikaci logiky](../logic-apps/logic-apps-overview.md) a automatizovat opakovaný pracovní postup, který běží podle plánu. Konkrétně tento příklad aplikace logiky běží každý všední den ráno a kontroluje dobu jízdy, včetně provozu, mezi dvěma místy. Pokud doba překročí určitou hranici, odešle aplikace logiky e-mail s dobou cesty a časem navíc potřebným k dosažení cíle cesty.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření prázdné aplikace logiky
> * Přidejte aktivační událost opakování, která určuje plán pro aplikaci logiky.
> * Přidejte akci Mapy Bing, která získá dobu jízdy pro trasu.
> * Přidejte akci, která vytvoří proměnnou, převede dobu jízdy z sekund na minuty a uloží, které mají za následek proměnnou.
> * Přidání podmínky, která porovná dobu cesty se zadaným limitem.
> * Přidejte akci, která vám pošle e-mail, pokud doba cesty překročí limit.

Jakmile budete hotovi, vaše aplikace logiky bude na základní úrovni vypadat jako tento pracovní postup:

![Přehled pracovního postupu aplikace logiky na vysoké úrovni](./media/tutorial-build-scheduled-recurring-logic-app-workflow/check-travel-time-overview.png)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné, [zaregistrujte si bezplatný účet Azure,](https://azure.microsoft.com/free/) než začnete.

* E-mailový účet od poskytovatele e-mailu, který podporuje Logic Apps, jako je Office 365 Outlook, Outlook.com nebo Gmail. Pokud máte jiného poskytovatele, [tady se podívejte na seznam konektorů](https://docs.microsoft.com/connectors/). Tento rychlý start používá účet Office 365 Outlook. Pokud používáte jiný e-mailový účet, obecné kroky zůstanou stejné, ale vaše ui se může mírně lišit.

* K získání doby trvání cesty pro nějakou trasu potřebujete přístupový klíč k rozhraní API Map Bing. Pokud chcete tento klíč získat, řiďte se kroky [postupu získání klíče k Mapám Bing](https://docs.microsoft.com/bingmaps/getting-started/bing-maps-dev-center-help/getting-a-bing-maps-key).

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí přihlašovacích údajů svého účtu Azure.

## <a name="create-your-logic-app"></a>Vytvoření aplikace logiky

1. V hlavní nabídce Azure vyberte > Vytvořit > **aplikaci logiky****integrace** **prostředků**.

   ![Vytvoření prostředku aplikace logiky](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-new-logic-app-resource.png)

1. V části **Vytvořit aplikaci logiky** zadejte podrobnosti o vaší aplikaci logiky podle následujícího obrázku a popisu. Až to budete mít, vyberte **Vytvořit**.

   ![Poskytnutí informací o aplikaci logiky](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-logic-app-settings.png)

   | Vlastnost | Hodnota | Popis |
   |----------|-------|-------------|
   | **Název** | LA-dobacesty | Název aplikace logiky, který může obsahovat pouze písmena, čísla, pomlčky`-`( ), podtržítka (`_`), závorky`(`( , `)`) a tečky (`.`). Tento příklad používá "LA-TravelTime". |
   | **Předplatné** | <*název předplatného Azure*> | Název předplatného Azure |
   | **Skupina prostředků** | LA-dobacesty-SP | Název [skupiny prostředků Azure](../azure-resource-manager/management/overview.md), který se používá k uspořádání souvisejících prostředků. Tento příklad používá "LA-TravelTime-RG". |
   | **Umístění** | USA – západ | TOblast, kde chcete uložit informace o aplikaci logiky. Tento příklad používá "Západní USA". |
   | **Log Analytics** | Vypnuto | Pokud chcete zapnout protokolování diagnostiky, ponechte nastavení **Vypnuto**. |
   ||||

1. Po nasazení aplikace Azure na panelu nástrojů Azure vyberte **Oznámení** > **Přejít na prostředek** pro nasazenou aplikaci logiky.

   ![Přejděte na nový prostředek aplikace logiky.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/go-to-new-logic-app-resource.png)

   Nebo můžete najít a vybrat aplikaci logiky zadáním názvu do vyhledávacího pole.

   Návrhář aplikací logiky se otevře a zobrazí stránku s úvodním videem a běžně používanými aktivačními událostmi a vzory aplikací logiky. V části **Šablony** vyberte **Prázdná aplikace logiky**.

   ![Vybrat prázdnou šablonu aplikace logiky](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-logic-app-template.png)

Dále přidejte [aktivační událost](../logic-apps/logic-apps-overview.md#logic-app-concepts)opakování , která se aktivuje na základě zadaného plánu. Každá aplikace logiky se musí spouštět triggerem, který se aktivuje při určité události nebo když nová data splní určitou podmínku. Další informace najdete v článku [Vytvoření první aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-the-recurrence-trigger"></a>Přidání aktivační události Opakování

1. V Návrháři aplikace logiky zadejte do vyhledávacího pole jako filtr "opakování". V seznamu **Aktivační události** vyberte aktivační událost **opakování.**

   ![Přidat aktivační událost Opakování](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-schedule-recurrence-trigger.png)

1. Ve tvaru **Opakování** vyberte tlačítko **Elipsy** (**...**) a pak vyberte **Přejmenovat**. Přejmenujte trigger s tímto popisem: `Check travel time every weekday morning`

   ![Přejmenování popisu aktivační události opakování](./media/tutorial-build-scheduled-recurring-logic-app-workflow/rename-recurrence-schedule-trigger.png)

1. Uvnitř aktivační události změňte tyto vlastnosti.

   ![Změna intervalu a četnosti aktivační události opakování](./media/tutorial-build-scheduled-recurring-logic-app-workflow/change-interval-frequency.png)

   | Vlastnost | Požaduje se | Hodnota | Popis |
   |----------|----------|-------|-------------|
   | **Interval** | Ano | 1 | Počet intervalů, po které se má čekat mezi kontrolami |
   | **Frequency** | Ano | Týden | Jednotka času pro opakování |
   |||||

1. V části **Interval** a **Frekvence**otevřete seznam **Přidat nový parametr** a vyberte tyto vlastnosti, které chcete přidat do aktivační události.

   * **V tyto dny**
   * **V těchto hodinách**
   * **V těchto minutách**

   ![Přidání vlastností aktivační události Opakování](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-trigger-properties.png)

1. Nyní nastavte hodnoty pro další vlastnosti, jak je znázorněno a popsáno zde.

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

1. Uložte svou aplikaci logiky. Na panelu nástrojů návrháře vyberte **Uložit**.

Vaše aplikace logiky je teď v provozu, ale kromě opakování nic dalšího nedělá. Přidejte tedy akci, která bude reagovat na aktivaci triggeru.

## <a name="get-the-travel-time-for-a-route"></a>Získání doby trvání cesty pro trasu

Teď, když máte trigger, přidejte [akci](../logic-apps/logic-apps-overview.md#logic-app-concepts), pomocí které získáte dobu trvání cesty mezi dvěma místy. Služba Logic Apps poskytuje konektor k rozhraní API Map Bing, takže tyto informace můžete snadno získat. Než začnete s tímto úkolem, ujistěte se, že máte klíč rozhraní API Map Bing podle požadavků k tomuto kurzu.

1. V Návrháři aplikace logiky vyberte pod aktivační událostí **nový krok**.

1. V části **Zvolit akci**vyberte **Standardní**. Do vyhledávacího pole zadejte jako filtr "mapy bingu" a vyberte akci **Získat trasu.**

   ![Vybrat akci "Získat trasu"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-get-route-action.png)

1. Pokud nemáte připojení k Mapám Bing, budete vyzváni k jeho vytvoření. Zadejte tyto podrobnosti o připojení a vyberte **vytvořit**.

   ![Vytvoření připojení k rozhraní API Map Bing](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-maps-connection.png)

   | Vlastnost | Požaduje se | Hodnota | Popis |
   |----------|----------|-------|-------------|
   | **Název připojení** | Ano | PřipojeníMapyBing | Zadejte název připojení. Tento příklad používá "BingMapsConnection". |
   | **Klíč rozhraní API** | Ano | <*vaše-Bing-Mapy-klíč*> | Zadejte klíč Map Bing, který jste dříve dostali. Pokud nemáte k dispozici klíč služby Mapy Bing, přečtěte si, [jak získat klíč](https://msdn.microsoft.com/library/ff428642.aspx). |
   |||||

1. Přejmenujte akci s tímto popisem: `Get route and travel time with traffic`

1. Uvnitř akce otevřete **seznam Přidat nový parametr**a vyberte tyto vlastnosti, které chcete přidat do akce.

   * **Optimalizovat**
   * **Jednotka vzdálenosti**
   * **Způsob cestování**

   ![Přidání vlastností do akce "Získat trasu"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-bing-maps-action-properties.png) 

1. Nyní nastavte hodnoty vlastností akce, jak je znázorněno a popsáno zde.

   ![Zadejte podrobnosti o akci "Získat trasu"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/get-route-action-settings.png) 

   | Vlastnost | Požaduje se | Hodnota | Popis |
   |----------|----------|-------|-------------|
   | **Bod na trase 1** | Ano | <*počáteční umístění*> | Počátek vaší cesty |
   | **Bod na trase 2** | Ano | <*koncové umístění*> | Cíl vaší trasy |
   | **Optimalizovat** | Ne | timeWithTraffic | Parametr k optimalizaci vaší trasy, jako je například vzdálenost, doba trvání cesty včetně dopravní situace atd. Vyberte parametr "timeWithTraffic". |
   | **Jednotka vzdálenosti** | Ne | <*vaše preference*> | Jednotka vzdálenosti použitá pro trasu. Tento příklad používá "Mile" jako jednotku. |
   | **Způsob cestování** | Ne | Autem | Způsob cestování pro danou trasu. Vyberte režim "Řízení". |
   ||||

   Další informace o těchto parametrech najdete v tématu [Výpočet trasy](https://docs.microsoft.com/bingmaps/rest-services/routes/calculate-a-route).

1. Uložte svou aplikaci logiky.

Dále vytvořte proměnnou tak, aby bylo možné převést a uložit aktuální dobu trvání cesty v minutách, a nikoli v sekundách. Tímto způsobem se vyhnete opakování převodu a danou hodnotu snadněji využijete v dalších krocích. 

## <a name="create-a-variable-to-store-travel-time"></a>Vytvoření proměnné pro uložení doby jízdy

Někdy můžete chtít spustit operace s daty v pracovním postupu a potom použít výsledky v pozdějších akcích. Pokud chcete výsledky uložit, abyste je mohli snadno opakovaně používat nebo na ně odkazovat, můžete vytvořit proměnné, které výsledky po jejich zpracování uloží. Proměnné můžete vytvářet pouze na nejvyšší úrovni v aplikaci logiky.

Ve výchozím nastavení vrátí předchozí akce **Get route** aktuální dobu jízdy s provozem v sekundách z vlastnosti Cestovní **doba provozu.** Převodem této hodnoty na minuty a jejím uložením usnadníte její opakované využití bez nutnosti dalších převodů.

1. V části **Akce Získat trasu** vyberte **Nový krok**.

1. V části **Zvolit akci**vyberte **Předdefinované**. Do vyhledávacího pole zadejte "proměnné" a vyberte akci **Inicializovat proměnnou.**

   ![Vyberte akci "Inicializovat proměnnou"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-initialize-variable-action.png)

1. Přejmenujte tuto akci s tímto popisem: `Create variable to store travel time`

1. Zadejte podrobnosti pro danou proměnnou tohoto popisu:

   | Vlastnost | Požaduje se | Hodnota | Popis |
   |----------|----------|-------|-------------|
   | **Název** | Ano | dobacesty | Název proměnné. Tento příklad používá "travelTime". |
   | **Typ** | Ano | Integer | Datový typ proměnné |
   | **Hodnotu** | Ne| Výraz, který převede aktuální dobu trvání cesty ze sekund na minuty (viz postup pod touto tabulkou) | Počáteční hodnota proměnné |
   ||||

   1. Chcete-li vytvořit výraz pro vlastnost **Value,** klepněte do pole, aby se zobrazil seznam dynamického obsahu. V případě potřeby zvětšete okno prohlížeče, dokud se seznam nezobrazí. V seznamu dynamického obsahu vyberte **Výraz**.

      ![Poskytnout informace pro akci "Inicializovat proměnnou"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings.png)

      Když kliknete do některých polí pro úpravy, zobrazí se seznam dynamického obsahu. V tomto seznamu jsou uvedeny všechny vlastnosti z předchozích akcí, které můžete použít jako vstupy v pracovním postupu. Seznam dynamického obsahu obsahuje editor výrazů, kde můžete vybrat funkce pro spuštění operací. Tento editor výrazů se zobrazuje jenom v seznamu dynamického obsahu.

   1. V editoru výrazů zadejte tento výraz: `div(,60)`

      ![Zadejte tento výraz: div(,60)](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-2.png)

   1. Umístěte kurzor dovnitř výrazu mezi levou závorku (**(**) a čárku (**,**). 
   vyberte **dynamický obsah**.

      ![Kurzor polohy, vyberte "Dynamický obsah"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-3.png)

   1. V seznamu dynamického obsahu vyberte možnost **Doba trvání cesty s provozem**.

      ![Vyberte vlastnost "Cestovní doba provozu"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-4.png)

   1. Po vyřešení hodnoty vlastnosti uvnitř výrazu vyberte **OK**.

      ![Chcete-li dokončit vytváření výrazu, vyberte možnost "OK"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-5.png)

      Vlastnost **Value** se nyní zobrazí, jak je znázorněno zde:

      ![Vlastnost "Hodnota" se zobrazí s vyřešeným výrazem](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-6.png)

1. Uložte svou aplikaci logiky.

Dál přidejte podmínku, která zkontroluje, zda je aktuální doba trvání cesty delší než určený limit.

## <a name="compare-the-travel-time-with-limit"></a>Porovnejte dobu jízdy s limitem

1. V části předchozí akce vyberte **Nový krok**.

1. V části **Zvolit akci**vyberte **Předdefinované**. Do vyhledávacího pole zadejte jako filtr "condition". Ze seznamu akcí vyberte akci **Podmínka.**

   ![Vybrat akci Podmínka](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-condition-action.png)

1. Přejmenujte podmínku s použitím tohoto popisu: `If travel time exceeds limit`

1. Vytvořte podmínku, která zkontroluje, zda hodnota vlastnosti **travelTime** překročí zadaný limit, jak je popsáno a co je zde zobrazeno:

   1. V podmínce klikněte do pole **Zvolte hodnotu** na levé straně podmínky.

   1. Ze seznamu dynamického obsahu, který se zobrazí v části **Proměnné**, vyberte vlastnost **travelTime.**

      ![Vytvořit stav na levé straně](./media/tutorial-build-scheduled-recurring-logic-app-workflow/build-condition-left-side.png)

   1. V prostředním poli porovnání vyberte **operátor větší než.**

   1. Do pole **Zvolte hodnotu** na pravé straně podmínky zadejte tento limit:`15`

      Po dokončení bude podmínka vypadat takto:

      ![Dokončená podmínka pro kontrolu doby jízdy](./media/tutorial-build-scheduled-recurring-logic-app-workflow/build-condition-check-travel-time.png)

1. Uložte svou aplikaci logiky.

Dále přidejte akci, která se má spustit, když doba jízdy překročí váš limit.

## <a name="send-email-when-limit-exceeded"></a>Odeslání e-mailu při překročení limitu

Nyní přidáte akci, která odešle e-mail, pokud doba trvání cesty překročí daný limit. Tento e-mail zahrnuje aktuální dobu trvání cesty a čas navíc nutný k dopravě po zadané trase.

1. V větvi podmínka je **pravda,** vyberte **Přidat akci**.

1. V části **Zvolit akci**vyberte **Standardní**. Do vyhledávacího pole zadejte "odeslat e-mail". Seznam vrátí mnoho výsledků, takže nejprve vyberte požadovaný e-mailový konektor, například:

   ![Vyberte požadovaný e-mailový konektor.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-action-send-email.png)

   * U pracovních nebo školních účtů Azure vyberte **Office 365 Outlook**.
   * U osobních účtů Microsoft vyberte **Outlook.com**.

1. Až se zobrazí akce konektoru, vyberte "Odeslat akci e-mailu", kterou chcete použít, například:

   ![Výběr akce „Odeslat e-mail“](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-send-email-action.png)

1. Pokud ještě nemáte připojení, budete vyzváni k přihlášení ke svému e-mailovému účtu.

   Logic Apps vytvoří připojení k e-mailovému účtu.

1. Přejmenujte akci s tímto popisem: `Send email with travel time`

1. Do pole **Komu** zadejte e-mailovou adresu příjemce. Pro účely testování použijte svou vlastní e-mailovou adresu.

1. V poli **Předmět** zadejte předmět e-mailu a zahrňte proměnnou **dobacesty**.

   1. Zadejte text `Current travel time (minutes):` s koncovou mezerou. 

   1. V seznamu dynamického obsahu vyberte v části **Proměnné** **položku Zobrazit další položku**.

      ![Najít proměnnou "travelTime"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/find-travelTime-variable.png)

   1. Po **travelTime** se objeví v části **Proměnné**, vyberte **travelTime**.

      ![Zadání textu předmětu a výrazu, který vrátí dobu trvání cesty](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-travelTime-variable.png)

1. Do pole **Text** zadejte obsah e-mailu.

   1. Zadejte text `Add extra travel time (minutes):` s koncovou mezerou.

   1. V seznamu dynamického obsahu vyberte **Výraz**.

      ![Sestavení výrazu pro text e-mailu](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings.png)

   1. V editoru výrazů zadejte tento výraz tak, abyste spočítali počet minut, které překračují daný limit: ```sub(,15)```

      ![Zadání výrazu k výpočtu minut navíc z doby trvání cesty](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-2.png)

   1. Umístěte kurzor dovnitř výrazu mezi levou závorku (**(**) a čárku (**,**). Vyberte **dynamický obsah**.

      ![Další vytváření výrazu k výpočtu minut navíc z doby trvání cesty](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-3.png)

   1. V části **Proměnné** vyberte **dobacesty**.

      ![Vyberte vlastnost travelTime, která se má použít ve výrazu.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-4.png)

   1. Poté, co vlastnost vyřeší uvnitř výrazu, vyberte **OK**.

      ![Po překladu vlastnosti "Tělo" vyberte "OK"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-5.png)

      Vlastnost **Body** se nyní zobrazí, jak je znázorněno zde:

      ![Vyřešená vlastnost "Tělo" ve výrazu](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-6.png)

1. Uložte svou aplikaci logiky.

V dalším kroku otestujte aplikaci logiky, která teď vypadá podobně jako v tomto příkladu:

![Dokončený příklad pracovního postupu aplikace logiky](./media/tutorial-build-scheduled-recurring-logic-app-workflow/check-travel-time-finished.png)

## <a name="run-your-logic-app"></a>Spuštění aplikace logiky

Chcete-li aplikaci logiky spustit ručně, vyberte na panelu nástrojů návrháře **možnost Spustit**.

* Pokud aktuální doba jízdy zůstane pod limitem, aplikace logiky neprovede nic jiného a čeká nebo další interval před dalším zkontrolováním. 

* Pokud aktuální doba jízdy překročí váš limit, dostanete e-mail s aktuální dobou cesty a počtem minut nad limitem. Tady je příklad e-mailu odeslaného aplikací logiky:

![Příklad odeslaných e-mailů, který zobrazuje dobu jízdy](./media/tutorial-build-scheduled-recurring-logic-app-workflow/received-example-email-notification.png)

Pokud neobdržíte žádné e-maily, zkontrolujte složku s nevyžádanou poštou. Váš filtr nevyžádané pošty může tento typ e-mailů přesměrovávat. Pokud si nejste jisti správným spuštěním aplikace logiky, přečtěte si téma [Řešení potíží s aplikací logiky](../logic-apps/logic-apps-diagnosing-failures.md).

Gratulujeme, právě jste vytvořili a spustili opakující se aplikaci logiky založenou na plánu. 

Chcete-li vytvořit další aplikace logiky, které používají aktivační událost **opakování,** podívejte se na tyto šablony, které jsou k dispozici po vytvoření aplikace logiky:

* Každodenní odesílání připomenutí e-mailem
* Odstranění starších objektů blob Azure
* Přidání zprávy do fronty Azure Storage

## <a name="clean-up-resources"></a>Vyčištění prostředků

Když už nepotřebujete ukázkovou aplikaci logiky, odstraňte skupinu prostředků, která obsahuje vaši aplikaci logiky a související prostředky. 

1. V hlavní nabídce Azure přejděte na **Skupiny prostředků** a vyberte skupinu prostředků pro vaši aplikaci logiky.

1. V nabídce skupiny prostředků vyberte **Přehled** > **odstranění skupiny prostředků**. 

   ![Přehled > Odstranit skupinu prostředků](./media/tutorial-build-scheduled-recurring-logic-app-workflow/delete-resource-group.png)

1. Jako potvrzení zadejte název skupiny prostředků a vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili aplikaci logiky, která kontroluje dopravní situaci podle zadaného plánu (pracovní dny ráno) a provádí akci (zasílá e-mail), pokud doba trvání cesty překročí určený limit. Dále se naučíte sestavit aplikaci logiky, která odesílá na seznam adresátů žádosti o schválení s využitím integrace služeb Azure, služeb Microsoft a dalších aplikací SaaS.

> [!div class="nextstepaction"]
> [Správa požadavků na seznam adresátů](../logic-apps/tutorial-process-mailing-list-subscriptions-workflow.md)
