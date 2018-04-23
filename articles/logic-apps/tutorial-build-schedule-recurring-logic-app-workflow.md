---
title: Vytvoření automatizovaných pracovních postupů prostřednictvím Scheduleru – Azure Logic Apps | Microsoft Docs
description: Tento kurz ukazuje, jak ve službě Azure Logic Apps vytvořit opakované automatizované pracovní postupy s využitím Scheduleru
author: ecfan
manager: anneta
editor: ''
services: logic-apps
documentationcenter: ''
ms.assetid: ''
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 01/12/2018
ms.author: LADocs; estfan
ms.openlocfilehash: 4360249367632a01e4357c527f595f8416c7cc38
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2018
---
# <a name="check-traffic-with-a-scheduler-based-logic-app"></a>Kontrola dopravní situace s využitím aplikace logiky založené na Scheduleru

Služba Azure Logic Apps umožňuje automatizovat naplánované pracovní postupy spouštěné podle plánu. Tento kurz ukazuje, jak můžete pomocí triggeru ve Scheduleru sestavit [aplikaci logiky](../logic-apps/logic-apps-overview.md), která se spustí každý pracovní den ráno a zkontroluje dobu trvání cesty mezi dvěma místy se zohledněním dopravní situace. Pokud doba překročí určitou hranici, odešle aplikace logiky e-mail s dobou cesty a časem navíc potřebným k dosažení cíle cesty.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření prázdné aplikace logiky 
> * Přidání triggeru, který v aplikaci logiky poslouží k naplánování
> * Přidání akce, která získá dobu trvání cesty pro určitou trasu
> * Přidání akce, která vytvoří proměnnou, převede dobu trvání cesty ze sekund na minuty a uloží výsledek do proměnné
> * Přidání podmínky, která porovná dobu cesty se zadaným limitem.
> * Přidání akce, která odešle e-mail, pokud doba cesty překročí daný limit

Jakmile budete hotovi, vaše aplikace logiky bude na základní úrovni vypadat jako tento pracovní postup:

![Aplikace logiky na základní úrovni](./media/tutorial-build-scheduled-recurring-logic-app-workflow/check-travel-time-overview.png)

Pokud ještě nemáte předplatné Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zaregistrujte si bezplatný účet Azure</a> před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

* E-mailový účet od poskytovatele podporovaného v Logic Apps, jako je Office 365 Outlook, Outlook.com nebo Gmail. Pokud máte jiného poskytovatele, [tady se podívejte na seznam konektorů](https://docs.microsoft.com/connectors/). Tento rychlý start používá účet Outlook.com. Pokud používáte jiný e-mailový účet, zůstává obecný postup stejný, ale vaše uživatelské rozhraní může vypadat trochu jinak.

* K získání doby trvání cesty pro nějakou trasu potřebujete přístupový klíč k rozhraní API Map Bing. Pokud chcete tento klíč získat, řiďte se kroky <a href="https://msdn.microsoft.com/library/ff428642.aspx" target="_blank">postupu získání klíče k Mapám Bing</a>. 

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k webu <a href="https://portal.azure.com" target="_blank">Azure Portal</a> pomocí přihlašovacích údajů svého účtu Azure.

## <a name="create-your-logic-app"></a>Vytvoření aplikace logiky

1. V hlavní nabídce Azure zvolte **Vytvořit prostředek** > **Podniková integrace** > **Aplikace logiky**.

   ![Vytvoření aplikace logiky](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-logic-app.png)

2. V části **Vytvořit aplikaci logiky** zadejte podrobnosti o vaší aplikaci logiky podle následujícího obrázku a popisu. Až budete hotovi, zvolte **Připnout na řídicí panel** > **Vytvořit**.

   ![Zadání informací o aplikaci logiky](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-logic-app-settings.png)

   | Nastavení | Hodnota | Popis | 
   | ------- | ----- | ----------- | 
   | **Název** | LA-dobacesty | Název vaší aplikace logiky | 
   | **Předplatné** | <*název_vašeho_předplatného_Azure*> | Název vašeho předplatného Azure | 
   | **Skupina prostředků** | LA-dobacesty-SP | Název [skupiny prostředků Azure](../azure-resource-manager/resource-group-overview.md) sloužící k uspořádání souvisejících prostředků | 
   | **Umístění** | Východní USA 2 | Oblast, kam se mají ukládat informace o vaší aplikaci logiky | 
   | **Log Analytics** | Vypnuto | Pokud chcete zapnout protokolování diagnostiky, ponechte nastavení **Vypnuto**. | 
   |||| 

3. Jakmile se vaše aplikace v Azure nasadí, otevře se Návrhář pro Logic Apps se zobrazenou stránkou s úvodním videem a šablonami pro běžně používané vzory aplikací logiky. V části **Šablony** zvolte **Prázdná aplikace logiky**.

   ![Výběr šablony prázdné aplikace logiky](./media/tutorial-build-scheduled-recurring-logic-app-workflow/choose-logic-app-template.png)

Dál přidejte [trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts) opakování, který se spustí podle zadaného plánu. Každá aplikace logiky se musí spouštět triggerem, který se aktivuje při určité události nebo když nová data splní určitou podmínku. Další informace najdete v článku [Vytvoření první aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-scheduler-trigger"></a>Přidání triggeru do Scheduleru

1. V návrháři zadejte do vyhledávacího pole „opakování“. Vyberte tento trigger: **Plán – Opakování**

   ![Nalezení a přidání triggeru Plán – Opakování](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-schedule-recurrence-trigger.png)

2. Ve tvaru **Opakování** vyberte tlačítko **tři tečky** (**...** ) a zvolte **Přejmenovat**. Přejmenujte trigger s tímto popisem: ```Check travel time every weekday morning```

   ![Přejmenování triggeru](./media/tutorial-build-scheduled-recurring-logic-app-workflow/rename-recurrence-schedule-trigger.png)

3. Uvnitř triggeru zvolte **Zobrazit pokročilé možnosti**.

4. Zadejte podrobnosti plánu a opakování triggeru podle obrázku a popisu:

   ![Zadání podrobností o plánu a opakování](./media/tutorial-build-scheduled-recurring-logic-app-workflow/schedule-recurrence-trigger-settings.png)

   | Nastavení | Hodnota | Popis | 
   | ------- | ----- | ----------- | 
   | **Interval** | 1 | Počet intervalů, po které se má čekat mezi kontrolami | 
   | **Frekvence** | Týden | Jednotka času pro opakování | 
   | **Časové pásmo** | Žádný | Platí jenom v případě, že zadáte čas spuštění. Vhodné při zadání jiného časového pásma, než je místní. | 
   | **Čas spuštění** | Žádný | Odloží opakování na určité datum a čas. Další informace najdete v tématu [Plánování pravidelně spouštěných úloh a pracovních postupů](../connectors/connectors-native-recurrence.md). | 
   | **V tyto dny** | Pondělí, úterý, středa, čtvrtek, pátek | Dostupné, pouze pokud je **Frekvence** nastavená na Týden. | 
   | **V těchto hodinách** | 7,8,9 | Dostupné, pouze pokud je **Frekvence** nastavená na Týden nebo Den. Vyberte hodiny, kdy se v průběhu dne spustí toto opakování. Tento příklad se spustí v celou 7., 8. a 9. hodinu. | 
   | **V těchto minutách** | 0,15,30,45 | Dostupné, pouze pokud je **Frekvence** nastavená na Týden nebo Den. Vyberte minuty, kdy se v průběhu dne spustí toto opakování. Tento příklad se spouští každých 15 minut od nulté celé hodiny. | 
   ||||

   Tento trigger se spouští každý pracovní den každých 15 minut, počínaje 7:00:00 a konče v 9:45:00. 
   Oblast **Náhled** zobrazuje plán opakování. 
   Další informace najdete v tématu [Plánování úloh a pracovních postupů](../connectors/connectors-native-recurrence.md) a [Akce a triggery pracovního postupu](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger).

5. Pokud chcete podrobnosti o triggeru prozatím skrýt, klikněte do záhlaví tvaru.

   ![Tvar sbalení pro skrytí podrobností](./media/tutorial-build-scheduled-recurring-logic-app-workflow/collapse-trigger-shape.png)

6. Uložte svou aplikaci logiky. Na panelu nástrojů návrháře zvolte **Uložit**. 

Vaše aplikace logiky je teď v provozu, ale kromě opakování nic dalšího nedělá. Přidejte tedy akci, která bude reagovat na aktivaci triggeru.

## <a name="get-the-travel-time-for-a-route"></a>Získání doby trvání cesty pro trasu

Teď, když máte trigger, přidejte [akci](../logic-apps/logic-apps-overview.md#logic-app-concepts), pomocí které získáte dobu trvání cesty mezi dvěma místy. Služba Logic Apps poskytuje konektor k rozhraní API Map Bing, takže tyto informace můžete snadno získat. Než začnete s tímto úkolem, ujistěte se, že máte klíč rozhraní API Map Bing podle požadavků k tomuto kurzu.

1. V Návrháři pro Logic Apps pod daným triggerem zvolte **+ Nový krok** > **Přidat akci**.

2. Vyhledejte „mapy“ a vyberte tuto akci: **Mapy Bing – Získat trasu**

3. Pokud nemáte připojení k Mapám Bing, budete vyzváni k jeho vytvoření. Zadejte podrobnosti připojení a zvolte **Vytvořit**.

   ![Vybrání akce Mapy Bing – Získat trasu](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-maps-connection.png)

   | Nastavení | Hodnota | Popis |
   | ------- | ----- | ----------- |
   | **Název připojení** | PřipojeníMapyBing | Zadejte název připojení. | 
   | **Klíč rozhraní API** | <*klíč-služby-Mapy-Bing*> | Zadejte klíč Map Bing, který jste dříve dostali. Pokud nemáte k dispozici klíč služby Mapy Bing, přečtěte si, <a href="https://msdn.microsoft.com/library/ff428642.aspx" target="_blank">jak získat klíč</a>. | 
   | | | |  

4. Přejmenujte akci s tímto popisem: ```Get route and travel time with traffic```

5. Zadejte podrobnosti akce **Získat trasu**, například podle tohoto obrázku a popisu:

   ![Zadání informací pro akci Mapy Bing – Získat trasu](./media/tutorial-build-scheduled-recurring-logic-app-workflow/get-route-action-settings.png) 

   | Nastavení | Hodnota | Popis |
   | ------- | ----- | ----------- |
   | **Bod na trase 1** | <*start*> | Počátek vaší cesty | 
   | **Bod na trase 2** | <*cíl*> | Cíl vaší trasy | 
   | **Vyloučit** | Žádný | Všechny položky, kterým je třeba se na trase vyhnout, jako je například dálnice, mýtné atd. | 
   | **Optimalizovat** | timeWithTraffic | Parametr k optimalizaci vaší trasy, jako je například vzdálenost, doba trvání cesty včetně dopravní situace atd. Vyberte tento parametr: timeWithTraffic | 
   | **Jednotka vzdálenosti** | <*vaše-volba*> | Jednotka vzdálenosti použitá pro trasu. V tomto článku se používá tato jednotka: míle  | 
   | **Způsob cestování** | Autem | Způsob cestování pro danou trasu. Vyberte tento způsob: Autem | 
   | **Datum a čas přejezdu** | Žádný | Platí pouze při přejezdech | 
   | **Typ datum a čas**  | Žádný | Platí pouze při přejezdech | 
   |||| 

   Další informace o těchto parametrech najdete v tématu [Výpočet trasy](https://msdn.microsoft.com/library/ff701717.aspx).

6. Uložte svou aplikaci logiky.

Dále vytvořte proměnnou tak, aby bylo možné převést a uložit aktuální dobu trvání cesty v minutách, a nikoli v sekundách. Tímto způsobem se vyhnete opakování převodu a danou hodnotu snadněji využijete v dalších krocích. 

## <a name="create-variable-to-store-travel-time"></a>Vytvoření proměnné k uložení doby trvání cesty

V některých případech můžete chtít provést operace s daty v pracovním postupu a použít výsledky v pozdějších akcích. Pokud chcete výsledky uložit, abyste je mohli snadno opakovaně používat nebo na ně odkazovat, můžete vytvořit proměnné, které výsledky po jejich zpracování uloží. Proměnné můžete vytvářet pouze na nejvyšší úrovni v aplikaci logiky.

Ve výchozím nastavení vrátí předchozí akce **Získat trasu** aktuální čas trvání cesty s vlivem dopravní situace v sekundách prostřednictvím pole **Doba trvání cesty s provozem**. Převodem této hodnoty na minuty a jejím uložením usnadníte její opakované využití bez nutnosti dalších převodů.

1. V rámci akce **Získat trasu** zvolte **+ Nový krok** > **Přidat akci**.

2. Vyhledejte „proměnné“ a vyberte tuto akci: **Proměnné – Inicializovat proměnnou**.

   ![Vybrání akce Proměnné – Inicializovat proměnnou](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-initialize-variable-action.png)

3. Přejmenujte tuto akci s tímto popisem: ```Create variable to store travel time```

4. Zadejte podrobnosti pro danou proměnnou tohoto popisu:

   | Nastavení | Hodnota | Popis | 
   | ------- | ----- | ----------- | 
   | **Název** | dobacesty | Název proměnné | 
   | **Typ** | Integer | Datový typ proměnné | 
   | **Hodnota** | Výraz, který převede aktuální dobu trvání cesty ze sekund na minuty (viz postup pod touto tabulkou) | Počáteční hodnota proměnné | 
   |||| 

   1. Pokud chcete vytvořit výraz pro pole **Hodnota**, klikněte do pole, aby se zobrazil seznam dynamického obsahu. 
   V případě potřeby zvětšete okno prohlížeče, dokud se seznam nezobrazí. 
   V seznamu dynamického obsahu vyberte **Výraz**. 

      ![Zadání informací pro akci Proměnné – Inicializovat proměnnou](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings.png)

      Po kliknutí do některých textových polí se zobrazí buď seznam dynamického obsahu nebo seznam vložených parametrů. Tento seznam obsahuje všechny parametry z předchozích akcí, které můžete použít jako vstupy do pracovního postupu. 
      Seznam dynamického obsahu zahrnuje editor výrazů, kde můžete vybrat funkce pro provádění operací. 
      Tento editor výrazů se zobrazuje jenom v seznamu dynamického obsahu.

      Šířka vašeho prohlížeče určuje, který seznam se zobrazí. 
      Pokud je okno vašeho prohlížeč široké, seznam dynamického obsahu se zobrazí. 
      Pokud je váš prohlížeč úzký, zobrazí se seznam parametrů vložený pod textové pole, které má právě fokus.

   2. V editoru výrazů zadejte tento výraz: ```div(,60)```

      ![Zadejte tento výraz: div(,60)](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-2.png)

   3. Umístěte kurzor dovnitř výrazu mezi levou závorku (**(**) a čárku (**,**). 
   Zvolte **Dynamický obsah**.

      ![Umístění kurzoru, volba Dynamický obsah](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-3.png)

   4. V seznamu dynamického obsahu vyberte možnost **Doba trvání cesty s provozem**.

      ![Vybrání pole Doba trvání cesty s provozem](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-4.png)

   5. Až se pole uvnitř výrazu analyzuje, zvolte **OK**.

      ![Zvolení tlačítka OK](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-5.png)

      Pole **Hodnota** se teď zobrazí, jak je vidět tady:

      ![Pole Hodnota s analyzovaným výrazem](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-6.png)

5. Uložte svou aplikaci logiky.

Dál přidejte podmínku, která zkontroluje, zda je aktuální doba trvání cesty delší než určený limit.

## <a name="compare-travel-time-with-limit"></a>Porovnání doby trvání cesty s limitem

1. V předchozí akci vyberte **+ Nový krok** > **Přidat podmínku**. 

2. Přejmenujte podmínku s použitím tohoto popisu: ```If travel time exceeds limit```

3. Podle následujícího obrázku a popisu sestavte podmínku, která zkontroluje, zda **dobacesty** překračuje zadaný limit:

   1. Uvnitř podmínky klikněte do pole **Zvolit hodnotu**, které se nachází na levé straně (v širokém zobrazení prohlížeče) nebo nahoře (v úzkém zobrazení prohlížeče).

   2. Ze seznamu dynamického obsahu nebo parametrů vyberte v části **Proměnné** pole **dobacesty**.

   3. V dialogovém okně porovnání vyberte tento operátor: **je větší než**.

   4. V poli **Zvolit hodnotu** na pravé straně (v širokém zobrazení) nebo dole (v úzkém zobrazení), zadejte tento limit: ```15```

   Pokud pracujete v úzkém zobrazení, zde je příklad, jak sestavit tuto podmínku:

   ![Sestavení podmínky v úzkém zobrazení](./media/tutorial-build-scheduled-recurring-logic-app-workflow/build-condition-check-travel-time-narrow.png)

4. Uložte svou aplikaci logiky.

Dále přidejte akci, která se provede při překročení limitu doby trvání cesty.

## <a name="send-email-when-limit-exceeded"></a>Odeslání e-mailu při překročení limitu

Nyní přidáte akci, která odešle e-mail, pokud doba trvání cesty překročí daný limit. Tento e-mail zahrnuje aktuální dobu trvání cesty a čas navíc nutný k dopravě po zadané trase. 

1. Ve větvi podmínky **Pokud je true** vyberte **Přidat akci**.

2. Vyhledejte „odeslat e-mail“ a vyberte konektor e-mailu a požadovanou akci odeslání e-mailu.

   ![Nalezení a vybrání akce „odeslat e-mail“](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-action-send-email.png)

   * U osobních účtů Microsoft, vyberte **Outlook.com**. 
   * U pracovních nebo školních účtů Azure vyberte **Office 365 Outlook**.

3. Pokud ještě nemáte připojení, budete vyzváni k přihlášení ke svému e-mailovému účtu.

   Logic Apps vytvoří připojení k e-mailovému účtu.

4. Přejmenujte akci s tímto popisem: ```Send email with travel time```

5. Do pole **Komu** zadejte e-mailovou adresu příjemce. Pro účely testování použijte svou vlastní e-mailovou adresu.

6. V poli **Předmět** zadejte předmět e-mailu a zahrňte proměnnou **dobacesty**.

   1. Zadejte text ```Current travel time (minutes): ``` s koncovou mezerou. 
   
   2. Ze seznamu parametrů nebo dynamického obsahu vyberte v části **Proměnné** **dobacesty**. 
   
      Příklad s úzkým zobrazením prohlížeče:

      ![Zadání textu předmětu a výrazu, který vrátí dobu trvání cesty](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-subject-settings.png)

7. Do pole **Text** zadejte obsah e-mailu. 

   1. Zadejte text ```Add extra travel time (minutes): ``` s koncovou mezerou. 
   
   2. V případě potřeby zvětšete okno prohlížeče, dokud se nezobrazí seznam dynamického obsahu. 
   V seznamu dynamického obsahu vyberte **Výraz**.

      ![Sestavení výrazu pro text e-mailu](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings.png)

   3. V editoru výrazů zadejte tento výraz tak, abyste spočítali počet minut, které překračují daný limit: ```sub(,15)```

      ![Zadání výrazu k výpočtu minut navíc z doby trvání cesty](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-2.png)

   4. Umístěte kurzor dovnitř výrazu mezi levou závorku (**(**) a čárku (**,**). Zvolte **Dynamický obsah**.

      ![Další vytváření výrazu k výpočtu minut navíc z doby trvání cesty](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-3.png)

   5. V části **Proměnné** vyberte **dobacesty**.

      ![Vybrání pole dobacesty k použití ve výrazu](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-4.png)

   6. Až se pole uvnitř výrazu analyzuje, zvolte **OK**.

      ![Pole Text s analyzovaným výrazem](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-5.png)

      Pole **Text** se teď zobrazí, jak je vidět tady:

      ![Pole Text s analyzovaným výrazem](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-6.png)

8. Uložte svou aplikaci logiky.

V dalším kroku otestujte aplikaci logiky, která teď vypadá podobně jako v tomto příkladu:

![Hotová aplikace logiky](./media/tutorial-build-scheduled-recurring-logic-app-workflow/check-travel-time-finished.png)

## <a name="run-your-logic-app"></a>Spuštění aplikace logiky

Pokud chcete aplikaci logiky spustit ručně, na panelu nástrojů návrháře zvolte **Spustit**. Pokud aktuální doba trvání cesty zůstane v rámci daného limitu, neprovede aplikace logiky nic dalšího a čeká na další interval před opětovnou kontrolu.
Pokud však aktuální doba trvání cesty překračuje daný limit, dostanete e-mail s aktuální dobou trvání cesty a počtem minut nad tento limit. Tady je příklad e-mailu odeslaného aplikací logiky:

![Odeslaný e-mail s dobou trvání cesty](./media/tutorial-build-scheduled-recurring-logic-app-workflow/email-notification.png)

Pokud neobdržíte žádné e-maily, zkontrolujte složku s nevyžádanou poštou. Váš filtr nevyžádané pošty může tento typ e-mailů přesměrovávat. Pokud si nejste jisti správným spuštěním aplikace logiky, přečtěte si téma [Řešení potíží s aplikací logiky](../logic-apps/logic-apps-diagnosing-failures.md).

Gratulujeme, právě jste vytvořili a spustili opakující se aplikaci logiky založenou na plánu. 

Pokud chcete vytvořit další aplikace logiky, které používají trigger **Plán – Opakování**, podívejte se na tyto šablony, které jsou k dispozici po vytvoření aplikace logiky:

* Každodenní odesílání připomenutí e-mailem
* Odstranění starších objektů blob Azure
* Přidání zprávy do fronty Azure Storage

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte skupinu prostředků, která obsahuje vaši aplikaci logiky, a všechny související prostředky. V hlavní nabídce Azure přejděte na **Skupiny prostředků** a vyberte skupinu prostředků pro vaši aplikaci logiky. Zvolte **Odstranit skupinu prostředků**. Pro ověření zadejte název skupiny prostředků a zvolte **Odstranit**.

![Přehled > Odstranit skupinu prostředků](./media/tutorial-build-scheduled-recurring-logic-app-workflow/delete-resource-group.png)

## <a name="get-support"></a>Získat podporu

* Pokud máte dotazy, navštivte [fórum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pokud chcete zanechat své nápady na funkce nebo hlasovat, navštivte [web zpětné vazby od uživatelů Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili aplikaci logiky, která kontroluje dopravní situaci podle zadaného plánu (pracovní dny ráno) a provádí akci (zasílá e-mail), pokud doba trvání cesty překročí určený limit. Dále se naučíte sestavit aplikaci logiky, která odesílá na seznam adresátů žádosti o schválení s využitím integrace služeb Azure, služeb Microsoft a dalších aplikací SaaS.

> [!div class="nextstepaction"]
> [Správa požadavků na seznam adresátů](../logic-apps/tutorial-process-mailing-list-subscriptions-workflow.md)
