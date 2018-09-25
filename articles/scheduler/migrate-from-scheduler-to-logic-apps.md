---
title: Migrace do Azure Logic Apps v Azure Scheduleru
description: Zjistěte, jak můžete nahradit úloh v Azure Scheduleru, která se vyřazuje, Azure Logic Apps
services: scheduler
ms.service: scheduler
ms.suite: infrastructure-services
author: derek1ee
ms.author: deli
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 09/20/2018
ms.openlocfilehash: c841f29adbe9911193227cced2856d953d820b08
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46997269"
---
# <a name="migrate-azure-scheduler-jobs-to-azure-logic-apps"></a>Migrace úloh Azure Scheduleru na Azure Logic Apps

> [!IMPORTANT]
> Služba Azure Logic Apps nahrazuje Azure Scheduleru, která se vyřazuje. Plánování úloh, postupujte podle tohoto článku pro přechod na Azure Logic Apps místo.

Tento článek popisuje, jak můžete naplánovat jednorázové a opakovaných úloh s Azure Logic Apps, nikoli v Azure Scheduleru vytvářet automatizované pracovní postupy. Při vytváření plánovaných úloh s Logic Apps, získáte tyto výhody:

* Není nutné se starat o konceptu *kolekci úloh* protože každá aplikace logiky je samostatné prostředky Azure.

* Můžete spustit více jednorázové úlohy s využitím aplikace logiky jeden.

* Služba Azure Logic Apps podporuje časové pásmo a letní čas (DST).

Další informace najdete v tématu [co je Azure Logic Apps?](../logic-apps/logic-apps-overview.md) nebo zkuste vytvořit svou první aplikaci logiky v rámci tohoto rychlého startu: [vytvořit svou první aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zaregistrujte si bezplatný účet Azure</a>.

* Pro aktivaci vaší aplikace logiky pomocí zasílání požadavků HTTP, použijte nástroj, jako [desktopová aplikace Postman](https://www.getpostman.com/apps).

## <a name="schedule-one-time-jobs"></a>Naplánovat jednorázové úlohy

Více jednorázové úlohy můžete spustit tak, že vytvoříte pouze jeden logiku aplikace. 

### <a name="create-your-logic-app"></a>Vytvoření aplikace logiky

1. V [webu Azure portal](https://portal.azure.com), vytvoření prázdné aplikace logiky v návrháři aplikace logiky. 

   Základní kroky, postupujte podle [rychlý start: vytvoření první aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. Do vyhledávacího pole zadejte jako filtr "při požadavku http". Ze seznamu triggerů vyberte tento trigger: **přijetí požadavku HTTP je při** 

   ![Přidání triggeru "Request"](./media/migrate-from-scheduler-to-logic-apps/request-trigger.png)

1. Pro trigger požadavku můžete volitelně zadat schématu JSON, která pomáhá pochopit strukturu pro vstupní hodnoty z příchozího požadavku. Návrhář aplikace logiky a usnadňuje výstupy pro výběr později ve svém pracovním postupu.

   Schéma, zadejte schéma v **požádat o schéma JSON hlavní části** pole, například: 

   ![Schéma požadavku](./media/migrate-from-scheduler-to-logic-apps/request-schema.png)

   Pokud nemáte k dispozici schéma, ale budete mít ukázkovou datovou část ve formátu JSON, můžete vygenerovat schéma z této datové části.

   1. V triggeru požadavku, zvolte **k vygenerování schématu použít ukázkovou datovou část**.

   1. V části **zadejte nebo vložte ukázkovou datovou část JSON**, zadejte vaši ukázkovou datovou část a pak zvolte **provádí**, například:

      ![Ukázkovou datovou část](./media/migrate-from-scheduler-to-logic-apps/sample-payload.png)

1. Pod triggerem zvolte **další krok**. 

1. Do vyhledávacího pole zadejte "zpoždění do" jako filtr. V seznamu akcí vyberte tuto akci: **zpoždění do**

   Tato akce pozastavení pracovního postupu aplikace logiky do zadaného data a času.

   ![Přidání akce "Zpoždění do"](./media/migrate-from-scheduler-to-logic-apps/delay-until.png)

1. Zadejte časové razítko pro, pokud chcete spustit pracovní postup aplikace logiky. 

   Pokud kliknete dovnitř **časové razítko** tak můžete volitelně vybrat výstup z aktivační události se zobrazí pole seznamu dynamického obsahu.

   ![Zadejte podrobnosti "Zpoždění do"](./media/migrate-from-scheduler-to-logic-apps/delay-until-details.png)

1. Přidejte všechny další akce, kterou chcete spustit tak, že vyberete [~ více než 200 konektorů](../connectors/apis-list.md). 

   Například můžete zahrnout akce HTTP, která odešle žádost na adresu URL nebo akce, které využívají službu front úložiště, fronty Service Bus nebo témat sběrnice Service Bus: 

   ![Akce HTTP](./media/migrate-from-scheduler-to-logic-apps/request-http-action.png)

1. Jakmile budete hotovi, uložte svou aplikaci logiky.

   ![Uložení aplikace logiky](./media/migrate-from-scheduler-to-logic-apps/save-logic-app.png)

   Při uložení aplikace logiky poprvé koncový bod adresy URL pro trigger požadavku aplikace logiky se zobrazí v **adresa URL operace HTTP POST** pole. 
   Pokud chcete volat aplikaci logiky a odešlete vstupy do aplikace logiky pro zpracování, použijte tuto adresu URL jako cíl volání.

   ![Uložit žádost o adresu URL koncového bodu aktivační událost](./media/migrate-from-scheduler-to-logic-apps/request-endpoint-url.png)

1. Zkopírujte a uložte tuto adresu URL koncového bodu, takže můžete později zasílat ručního požadavku, která spustí vaši aplikaci logiky. 

## <a name="start-a-one-time-job"></a>Spuštění jednorázového úlohy

Ruční spuštění a aktivaci jednorázové úlohy, odešlete volání adresu URL koncového bodu pro trigger požadavku aplikace logiky. V tomto volání zadejte vstup nebo datovou část k odeslání, který vám může mít uvedli zadáním schéma. 

Například pomocí aplikace Postman, můžete vytvořit požadavek POST s nastavením podobný této ukázce a klikněte na tlačítko **odeslat** k odeslání požadavku.

| Metoda žádosti | zprostředkovatele identity | Tělo | Hlavičky |
|----------------|-----|------|---------| 
| **POST** | <*Adresa URL koncového bodu*> | **nezpracované** <p>**JSON(Application/JSON)** <p>V **nezpracovaná** zadejte datovou část je vhodné odesílat v požadavku. <p>**Poznámka:**: Toto nastavení automaticky nakonfiguruje **záhlaví** hodnoty. | **Klíč**: Content-Type <br>**Hodnota**: application/json
 |||| 

![Odeslat žádost o ruční spuštění aplikace logiky](./media/migrate-from-scheduler-to-logic-apps/postman-send-post-request.png)

Po odeslání volání odpovědi z aplikace logiky se zobrazí v části **nezpracovaná** pole na **tělo** kartu. 

<a name="workflow-run-id"></a>

> [!IMPORTANT]
>
> Pokud chcete zrušit úlohu později, zvolte **záhlaví** kartu. Najít a zkopírovat **x-ms-pracovního postupu run-id** hodnota hlavičky odpovědi. 
>
> ![Odpověď](./media/migrate-from-scheduler-to-logic-apps/postman-response.png)

## <a name="cancel-a-one-time-job"></a>Zrušit jednorázové úlohy

Ve službě Logic Apps každý Jednorázová úloha spustí jako jeden prostředek spustit instanci. Zrušit jednorázové úlohy, můžete použít [běhů pracovního postupu - zrušit](https://docs.microsoft.com/rest/api/logic/workflowruns/cancel) v rozhraní REST API služby Logic Apps. Při odeslání volání triggeru, zadejte [ID běhu pracovního postupu](#workflow-run-id).

## <a name="schedule-recurring-jobs"></a>Plánování opakujících se úloh

### <a name="create-your-logic-app"></a>Vytvoření aplikace logiky

1. V [webu Azure portal](https://portal.azure.com), vytvoření prázdné aplikace logiky v návrháři aplikace logiky. 

   Základní kroky, postupujte podle [rychlý start: vytvoření první aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. Do vyhledávacího pole zadejte jako filtr "opakování". Ze seznamu triggerů vyberte tento trigger: **opakování** 

   ![Přidání triggeru "Recurrence"](./media/migrate-from-scheduler-to-logic-apps/recurrence-trigger.png)

1. Pokud chcete nastavte pokročilejší plánu.

   ![Rozšířený plán](./media/migrate-from-scheduler-to-logic-apps/recurrence-advanced-schedule.png)

   Další informace o pokročilé možnosti plánování najdete v tématu [vytvořit a spouštět opakované úlohy a pracovní postupy s Azure Logic Apps](../connectors/connectors-native-recurrence.md)

1. Přidání další akce, které tak, že vyberete [více než 200 konektorů](../connectors/apis-list.md). Pod triggerem zvolte **další krok**. Vyhledejte a vyberte akce, které chcete.

   Například můžete zahrnout akce HTTP, která odešle žádost na adresu URL nebo akce, které využívají službu front úložiště, fronty Service Bus nebo témat sběrnice Service Bus: 

   ![Akce HTTP](./media/migrate-from-scheduler-to-logic-apps/recurrence-http-action.png)

1. Jakmile budete hotovi, uložte svou aplikaci logiky.

   ![Uložení aplikace logiky](./media/migrate-from-scheduler-to-logic-apps/save-logic-app.png)

## <a name="advanced-setup"></a>Rozšířené nastavení

Tady jsou další způsoby, jak si můžete přizpůsobit úlohy.

### <a name="retry-policy"></a>Zásady opakování

K řízení způsobu, jakým akce se pokusí znovu spustí podle ve vaší aplikaci logiky stát krátkodobých selhání, můžete nastavit [zásady opakování](../logic-apps/logic-apps-exception-handling.md#retry-policies) v nastavení všechny akce, například:

1. Otevřete požadovanou akci (**...** ) nabídky a vybereme **nastavení**.

   ![Otevřete nastavení akce](./media/migrate-from-scheduler-to-logic-apps/action-settings.png)

1. Vyberte zásady opakování, které chcete. Další informace o jednotlivých zásad najdete v tématu [zásady opakování](../logic-apps/logic-apps-exception-handling.md#retry-policies).

   ![Vyberte zásady opakování](./media/migrate-from-scheduler-to-logic-apps/retry-policy.png)

## <a name="handle-exceptions-and-errors"></a>Zpracování výjimek a chyb

V Azure Scheduleru výchozí akce se nezdaří spuštění, můžete spustit v.42 akci, která řeší chybového stavu. V Azure Logic Apps můžete také provést stejnou úlohu.

1. V návrháři aplikace logiky, nad akci chcete zpracovat, přesuňte ukazatel nad šipku mezi kroky a vyberte a **přidat paralelní větev**. 

   ![Přidat paralelní větev](./media/migrate-from-scheduler-to-logic-apps/add-parallel-branch.png)

1. Vyhledejte a vyberte akci, kterou chcete spustit místo jako jinou akci.

   ![Přidat paralelní akce](./media/migrate-from-scheduler-to-logic-apps/add-parallel-action.png)

1. Na jinou akci, spusťte (**...** ) nabídky a vybereme **nakonfigurovat vlastnost runafter**.

   ![Nakonfigurovat vlastnost runAfter](./media/migrate-from-scheduler-to-logic-apps/configure-run-after.png)

1. Zrušte zaškrtnutí políčka **úspěšné** vlastnost. Vyberte tyto vlastnosti: **selhala**, **se přeskočila**, a **vypršení časového limitu**

   ![Nastavení vlastnosti "spustit po"](./media/migrate-from-scheduler-to-logic-apps/select-run-after-properties.png)

1. Jakmile budete hotovi, zvolte **provádí**.

Další informace o zpracování výjimek, naleznete v tématu [zpracování chyb a výjimek – vlastnost RunAfter](../logic-apps/logic-apps-exception-handling.md#catch-and-handle-failures-with-the-runafter-property).

## <a name="faq"></a>Nejčastější dotazy

<a name="retire-date"></a> 

**Q**: když je Azure Scheduler vyřazení z provozu? <br>
**A**: Azure Scheduler plánuje vyřadit z provozu 30. září 2019.

**Q**: co se stane se Moje kolekce úloh Scheduleru a úlohy poté, co vyřadí službu? <br>
**A**: kolekce všechny plánovače úloh a úloh se odstraní ze systému.

**Q**: je nutné zálohovat nebo provádět další úlohy před migrací Moje úlohy Scheduleru do Logic Apps? <br>
**A**: jako osvědčený postup, vždy zálohovat svou práci. Zkontrolujte, jestli aplikace logiky, kterou jste vytvořili fungují podle očekávání před odstraněním nebo zakázání Plánovač úloh. 

**Q**: je existuje nástroj, který mi může pomoci migrovat Moje úlohy z plánovače pro Logic Apps? <br>
**A**: každý Plánovač úloh je jedinečný, tak, že neexistuje univerzální nástroj. Různé skripty, ale bude k dispozici pro úpravy pro vaše potřeby. Pro skript dostupnost vrátit se sem později.

**Q**: kde lze získat podporu pro migraci Moje úlohy Scheduleru? <br>
**A**: Zde jsou některé způsoby, jak získat podporu: 

**Azure Portal**

Pokud vaše předplatné Azure má placený plán podpory, můžete vytvořit žádost o technickou podporu na webu Azure Portal. V opačném případě můžete vybrat možnost jiný podpory.

1. Na [webu Azure portal](https://portal.azure.com) hlavní nabídky, vyberte **Nápověda a podpora**.

1. V části **podporují**vyberte **nová žádost o podporu**. Pro svoji žádost. uveďte následující údaje:

   | Nastavení | Hodnota |
   |---------|-------|
   | **Typ problému** | **Technické** | 
   | **Předplatné** | <*Vaše předplatné Azure*> | 
   | **Služba** | V části **monitorování a správa**vyberte **Plánovač**. | 
   ||| 

1. Vyberte požadovanou možnost podpory. Pokud máte placený plán podpory, zvolte **Další**.

**Community**

* [Fórum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-scheduler)

## <a name="next-steps"></a>Další postup

* [Vytvoření pravidelně spuštěných úloh a pracovních postupů s Azure Logic Apps](../connectors/connectors-native-recurrence.md)
* [Kurz: Kontrola provozu s využitím aplikace logiky založené na plánu](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)