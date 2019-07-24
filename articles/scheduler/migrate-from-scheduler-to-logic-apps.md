---
title: Migrace z Azure Scheduleru na Azure Logic Apps
description: Přečtěte si, jak můžete nahradit úlohy v Azure Scheduleru, která se už vyřazuje, pomocí Azure Logic Apps
services: scheduler
ms.service: scheduler
ms.suite: infrastructure-services
author: derek1ee
ms.author: deli
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 09/20/2018
ms.openlocfilehash: 0225a9f34e016a4b1de51c06ba982d384e41007c
ms.sourcegitcommit: af58483a9c574a10edc546f2737939a93af87b73
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/17/2019
ms.locfileid: "68302076"
---
# <a name="migrate-azure-scheduler-jobs-to-azure-logic-apps"></a>Migrace úloh Azure Scheduleru na Azure Logic Apps

> [!IMPORTANT]
> Azure Logic Apps nahrazuje vyřazení Azure Scheduleru. Pokud chcete naplánovat úlohy, postupujte podle tohoto článku, abyste místo toho přesunuli Azure Logic Apps.

V tomto článku se dozvíte, jak můžete naplánovat jednorázové a opakované úlohy vytvořením automatizovaných pracovních postupů pomocí Azure Logic Apps, nikoli pomocí Azure Scheduleru. Když vytváříte naplánované úlohy pomocí Logic Apps, získáte tyto výhody:

* Nemusíte se starat o koncept *kolekce úloh* , protože každá aplikace logiky je samostatný prostředek Azure.

* Pomocí jedné aplikace logiky můžete spustit více jednorázových úloh.

* Služba Azure Logic Apps podporuje časové pásmo a letní čas (DST).

Další informace najdete v tématu [co je Azure Logic Apps?](../logic-apps/logic-apps-overview.md) nebo si v tomto rychlém startu zkuste vytvořit svou první aplikaci logiky: [Vytvořte svou první aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zaregistrujte si bezplatný účet Azure</a>.

* K aktivaci aplikace logiky odesláním požadavků HTTP použijte nástroj, jako je například [aplikace pro pozálohovací plochu](https://www.getpostman.com/apps).

## <a name="schedule-one-time-jobs"></a>Plánování jednorázových úloh

Můžete spustit více jednorázových úloh vytvořením pouze jedné aplikace logiky. 

### <a name="create-your-logic-app"></a>Vytvoření aplikace logiky

1. V [Azure Portal](https://portal.azure.com)vytvořte prázdnou aplikaci logiky v návrháři aplikace logiky. 

   Základní postup je popsaný [v části rychlý Start: Vytvořte svou první aplikaci](../logic-apps/quickstart-create-first-logic-app-workflow.md)logiky.

1. Do vyhledávacího pole zadejte "při požadavku HTTP" jako filtr. V seznamu triggery vyberte tuto aktivační událost: **Při přijetí požadavku HTTP** 

   ![Přidat aktivační událost "Request"](./media/migrate-from-scheduler-to-logic-apps/request-trigger.png)

1. U triggeru žádosti můžete volitelně zadat schéma JSON, které pomůže návrháři aplikace logiky pochopit strukturu vstupů z příchozího požadavku a díky tomu budou mít tyto výstupy na výběr později ve svém pracovním postupu.

   Chcete-li zadat schéma, zadejte schéma do pole **schéma JSON textu žádosti** , například: 

   ![Schéma požadavku](./media/migrate-from-scheduler-to-logic-apps/request-schema.png)

   Pokud schéma nemáte, ale máte ukázkovou datovou část ve formátu JSON, můžete z této datové části vygenerovat schéma.

   1. V triggeru žádosti vyberte **použít ukázkovou datovou část k vygenerování schématu**.

   1. V části **Zadejte nebo vložte ukázkovou datovou část JSON**zadejte datovou část ukázky a pak zvolte Hotovo, například:

      ![Ukázková datová část](./media/migrate-from-scheduler-to-logic-apps/sample-payload.png)

1. V části Trigger klikněte na **Další krok**. 

1. Do vyhledávacího pole zadejte "zpoždění do" jako filtr. V seznamu akce vyberte tuto akci: **Zpoždění do**

   Tato akce pozastaví pracovní postup aplikace logiky až do zadaného data a času.

   ![Přidat akci zpoždění do](./media/migrate-from-scheduler-to-logic-apps/delay-until.png)

1. Zadejte časové razítko, kde chcete spustit pracovní postup aplikace logiky. 

   Když kliknete do pole **Timestamp (časové razítko** ), zobrazí se seznam dynamického obsahu, abyste mohli volitelně vybrat výstup z triggeru.

   ![Zadejte podrobnosti o zpoždění do.](./media/migrate-from-scheduler-to-logic-apps/delay-until-details.png)

1. Kliknutím na [stovky konektorů připravených k použití](../connectors/apis-list.md)přidejte všechny akce, které chcete spustit. 

   Můžete například zahrnout akci HTTP, která odešle požadavek na adresu URL, nebo akce, které pracují s frontami úložiště, Service Bus frontami nebo Service Bus témata: 

   ![Akce HTTP](./media/migrate-from-scheduler-to-logic-apps/request-http-action.png)

1. Až budete hotovi, uložte aplikaci logiky.

   ![Uložení aplikace logiky](./media/migrate-from-scheduler-to-logic-apps/save-logic-app.png)

   Při prvním uložení aplikace logiky se adresa URL koncového bodu pro Trigger požadavku vaší aplikace logiky zobrazí v poli **Adresa URL pro odeslání** . 
   Pokud chcete zavolat aplikaci logiky a odeslat vstupy do aplikace logiky ke zpracování, použijte tuto adresu URL jako cíl volání.

   ![Adresa URL koncového bodu triggeru žádosti](./media/migrate-from-scheduler-to-logic-apps/request-endpoint-url.png)

1. Zkopírujte a uložte tuto adresu URL koncového bodu, abyste později mohli poslat ruční požadavek, který spustí vaši aplikaci logiky. 

## <a name="start-a-one-time-job"></a>Spuštění jednorázové úlohy

Pokud chcete ručně spustit nebo aktivovat jednorázovou úlohu, odešlete volání adresy URL koncového bodu pro aktivační událost žádosti vaší aplikace logiky. V tomto volání určete vstupní nebo datovou část k odeslání, kterou jste si mohli popsaným způsobem popsat zadáním schématu. 

Například pomocí aplikace pro publikování můžete vytvořit požadavek POST s nastavením podobným této ukázce a pak vybrat **Odeslat** pro vytvoření žádosti.

| Request – metoda | URL | Tělo | Hlavičky |
|----------------|-----|------|---------| 
| **POST** | <*endpoint-URL*> | **získání** <p>**JSON (Application/JSON)** <p>Do pole **nezpracované** zadejte datovou část, kterou chcete v žádosti odeslat. <p>**Poznámka:** Toto nastavení automaticky nakonfiguruje hodnoty **hlaviček** . | **Klíč**: Typ obsahu <br>**Hodnota**: Application/JSON
 |||| 

![Poslat požadavek na ruční aktivaci vaší aplikace logiky](./media/migrate-from-scheduler-to-logic-apps/postman-send-post-request.png)

Po odeslání hovoru se odpověď z vaší aplikace logiky zobrazí v poli nezpracované  na kartě **tělo** . 

<a name="workflow-run-id"></a>

> [!IMPORTANT]
>
> Pokud chcete úlohu později zrušit, klikněte na kartu **hlavičky** . Vyhledejte a zkopírujte hodnotu záhlaví **x-MS-Workflow-Run-ID** v odpovědi. 
>
> ![Odpověď](./media/migrate-from-scheduler-to-logic-apps/postman-response.png)

## <a name="cancel-a-one-time-job"></a>Zrušení jednorázové úlohy

V Logic Apps se každou jednorázovou úlohu spouští jako jediná instance spuštění aplikace logiky. Chcete-li zrušit jednorázovou úlohu, můžete použít [spuštění pracovního postupu – zrušit](https://docs.microsoft.com/rest/api/logic/workflowruns/cancel) v REST API Logic Apps. Po odeslání volání triggeru zadejte [ID spuštění pracovního postupu](#workflow-run-id).

## <a name="schedule-recurring-jobs"></a>Plánování opakujících se úloh

### <a name="create-your-logic-app"></a>Vytvoření aplikace logiky

1. V [Azure Portal](https://portal.azure.com)vytvořte prázdnou aplikaci logiky v návrháři aplikace logiky. 

   Základní postup je popsaný [v části rychlý Start: Vytvořte svou první aplikaci](../logic-apps/quickstart-create-first-logic-app-workflow.md)logiky.

1. Do vyhledávacího pole zadejte "opakování" jako filtr. V seznamu triggery vyberte tuto aktivační událost: **Opakování** 

   ![Přidat aktivační událost opakování](./media/migrate-from-scheduler-to-logic-apps/recurrence-trigger.png)

1. V případě potřeby nastavte pokročilejší plán.

   ![Rozšířený plán](./media/migrate-from-scheduler-to-logic-apps/recurrence-advanced-schedule.png)

   Další informace o pokročilých možnostech plánování najdete v tématu [vytváření a spouštění opakujících se úloh a pracovních postupů s Azure Logic Apps](../connectors/connectors-native-recurrence.md) .

1. Přidejte další požadované akce výběrem ze [stovek připravených k použití](../connectors/apis-list.md). V části Trigger klikněte na **Další krok**. Najděte a vyberte akce, které chcete.

   Můžete například zahrnout akci HTTP, která odešle požadavek na adresu URL, nebo akce, které pracují s frontami úložiště, Service Bus frontami nebo Service Bus témata: 

   ![Akce HTTP](./media/migrate-from-scheduler-to-logic-apps/recurrence-http-action.png)

1. Až budete hotovi, uložte aplikaci logiky.

   ![Uložení aplikace logiky](./media/migrate-from-scheduler-to-logic-apps/save-logic-app.png)

## <a name="advanced-setup"></a>Rozšířené nastavení

Tady jsou další způsoby, jak můžete úlohy přizpůsobit.

### <a name="retry-policy"></a>Zásady opakování

Chcete-li řídit způsob, jakým se akce pokusí znovu spustit v aplikaci logiky, když dojde k přerušované chybě, můžete nastavit [zásady opakování](../logic-apps/logic-apps-exception-handling.md#retry-policies) v nastavení jednotlivých akcí, například:

1. Otevřete nabídku Akce ( **...** ) a vyberte **Nastavení**.

   ![Otevřít nastavení akce](./media/migrate-from-scheduler-to-logic-apps/action-settings.png)

1. Vyberte zásady opakování, které chcete. Další informace o jednotlivých zásadách najdete v tématu [zásady opakování](../logic-apps/logic-apps-exception-handling.md#retry-policies).

   ![Vybrat zásady opakování](./media/migrate-from-scheduler-to-logic-apps/retry-policy.png)

## <a name="handle-exceptions-and-errors"></a>Zpracování výjimek a chyb

Pokud ve službě Azure Scheduler neproběhne spuštění výchozí akce, můžete spustit akci alterative, která řeší chybový stav. V Azure Logic Apps můžete také provést stejnou úlohu.

1. V návrháři aplikace logiky nad akci, kterou chcete zpracovat, přesuňte ukazatel na šipku mezi jednotlivými kroky a vyberte a **přidejte paralelní větev**. 

   ![Přidat paralelní větev](./media/migrate-from-scheduler-to-logic-apps/add-parallel-branch.png)

1. Vyhledejte a vyberte akci, kterou chcete spustit místo toho, jako alternativní akci.

   ![Přidat paralelní akci](./media/migrate-from-scheduler-to-logic-apps/add-parallel-action.png)

1. V alternativní akci otevřete nabídku (.. **.** ) a vyberte **Konfigurovat spustit po**.

   ![Konfigurovat běh po](./media/migrate-from-scheduler-to-logic-apps/configure-run-after.png)

1. Zrušte zaškrtnutí políčka pro vlastnost **je úspěšná** . Vyberte tyto vlastnosti: **operace se**nezdařila, byla **vynechána**a **vypršel časový limit** .

   ![Nastavení vlastností spustit po](./media/migrate-from-scheduler-to-logic-apps/select-run-after-properties.png)

1. Jakmile budete hotovi, zvolte **Done** (Hotovo).

Další informace o zpracování výjimek naleznete v tématu [zpracování chyb a výjimek – vlastnost runafter šablonové](../logic-apps/logic-apps-exception-handling.md#catch-and-handle-failures-with-the-runafter-property).

## <a name="faq"></a>Nejčastější dotazy

<a name="retire-date"></a> 

**OTÁZKA**: Kdy služba Azure Scheduler vychází z provozu? <br>
**A**: Služba Azure Scheduler je naplánována na vyřazení z 30. září 2019.

**OTÁZKA**: Co se stane s kolekcemi úloh a úlohami plánovače po vystavování služby? <br>
**A**: Všechny kolekce úloh Scheduleru a úlohy budou odstraněny ze systému.

**OTÁZKA**: Musím před migrací úloh Scheduleru do Logic Apps provést zálohování nebo provedení dalších úloh? <br>
**A**: Jako osvědčený postup vždy zálohujte práci. Ověřte, že aplikace logiky, které jste vytvořili, jsou spuštěné podle očekávání před odstraněním nebo zakázáním úloh plánovače. 

**OTÁZKA**: Existuje nástroj, který vám může přispět k migraci mých úloh z Scheduleru na Logic Apps? <br>
**A**: Každá úloha Scheduleru je jedinečná, takže neexistují žádné nástroje, které jsou pro něj všechny. K dispozici jsou ale různé skripty, které budete moct upravit podle svých potřeb. V případě dostupnosti skriptu se vraťte později.

**OTÁZKA**: Kde můžu získat podporu pro migraci úloh plánovače? <br>
**A**: Můžete si stáhnout několik způsobů, jak získat podporu: 

**Azure Portal**

Pokud má vaše předplatné Azure placený plán podpory, můžete v Azure Portal vytvořit žádost o technickou podporu. V opačném případě můžete vybrat jinou možnost podpory.

1. V hlavní nabídce [Azure Portal](https://portal.azure.com) vyberte možnost **help + podpora**.

1. V části **Podpora**vyberte **Nová žádost o podporu**. Zadejte pro svůj požadavek tyto podrobnosti:

   | Nastavení | Hodnota |
   |---------|-------|
   | **Typ problému** | **Odbornou** | 
   | **Předplatné** | <*Vaše předplatné – Azure*> | 
   | **Služba** | V části **monitorování & Správa**vyberte **Plánovač**. | 
   ||| 

1. Vyberte požadovanou možnost podpory. Pokud máte placený plán podpory, klikněte na tlačítko **Další**.

**Community**

* [Fórum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-scheduler)

## <a name="next-steps"></a>Další kroky

* [Vytváření pravidelně běžících úloh a pracovních postupů pomocí Azure Logic Apps](../connectors/connectors-native-recurrence.md)
* [Kurz: Kontrolovat provoz s využitím aplikace logiky založené na plánu](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)
