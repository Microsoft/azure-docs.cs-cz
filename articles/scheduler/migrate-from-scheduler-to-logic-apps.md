---
title: Migrace z Azure Scheduleru na Azure Logic Apps
description: Přečtěte si, jak můžete nahradit úlohy v Azure Scheduleru, která se už vyřazuje, pomocí Azure Logic Apps
services: scheduler
ms.service: scheduler
ms.suite: infrastructure-services
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: article
ms.date: 02/29/2020
ms.openlocfilehash: 0e9ca2c7b5d15fcc6be451bbe215c8b85d5309eb
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2020
ms.locfileid: "92368260"
---
# <a name="migrate-azure-scheduler-jobs-to-azure-logic-apps"></a>Migrace úloh Azure Scheduleru do Azure Logic Apps

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) nahrazuje [vyřazení](#retire-date)Azure Scheduleru. Pokud chcete pokračovat v práci s úlohami, které jste nastavili v plánovači, migrujte prosím na Azure Logic Apps co nejdříve podle tohoto článku. 
>
> Plánovač již není v Azure Portal k dispozici, ale [rutiny prostředí PowerShell](scheduler-powershell-reference.md) [REST API](/rest/api/scheduler) a Azure Scheduler jsou v tuto chvíli dostupné, abyste mohli spravovat úlohy a kolekce úloh.

V tomto článku se dozvíte, jak můžete naplánovat jednorázové a opakované úlohy vytvořením automatizovaných pracovních postupů pomocí Azure Logic Apps, nikoli pomocí Azure Scheduleru. Když vytváříte naplánované úlohy pomocí Logic Apps, získáte tyto výhody:

* Sestavte svou úlohu pomocí vizuálního návrháře a [konektorů připravených k použití](../connectors/apis-list.md) ze stovek služeb, jako je Azure Blob Storage, Azure Service Bus, Office 365 Outlook a SAP.

* Spravujte každý naplánovaný pracovní postup jako prostředek Azure jako první třídy. Nemusíte se starat o koncept *kolekce úloh* , protože každá aplikace logiky je individuální prostředek Azure.

* Spouštějte více jednorázových úloh pomocí jedné aplikace logiky.

* Nastavte plány, které podporují časová pásma a automaticky se upraví na letní čas (DST).

Další informace najdete v tématu [co je Azure Logic Apps?](../logic-apps/logic-apps-overview.md) nebo zkuste vytvořit svou první aplikaci logiky v tomto rychlém startu: [vytvořte svou první aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="prerequisites"></a>Předpoklady

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* K aktivaci aplikace logiky odesláním požadavků HTTP použijte nástroj, jako je například [aplikace pro pozálohovací plochu](https://www.getpostman.com/apps).

## <a name="migrate-by-using-a-script"></a>Migrace pomocí skriptu

Každá úloha Scheduleru je jedinečná, takže pro migraci úloh plánovače na Azure Logic Apps existuje žádný nástroj, který se zahodí. [Tento skript](https://github.com/Azure/logicapps/tree/master/scripts/scheduler-migration) ale můžete upravit tak, aby vyhovoval vašim potřebám.

## <a name="schedule-one-time-jobs"></a>Plánování jednorázových úloh

Můžete spustit více jednorázových úloh vytvořením pouze jedné aplikace logiky.

1. V [Azure Portal](https://portal.azure.com)vytvořte prázdnou aplikaci logiky v návrháři aplikace logiky.

   Základní postup najdete v části [rychlý Start: Vytvoření první aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. Do vyhledávacího pole zadejte `when a http request` a vyhledejte Trigger žádosti. V seznamu triggery vyberte tuto aktivační událost: **když se přijme požadavek HTTP** .

   ![Přidat aktivační událost "Request"](./media/migrate-from-scheduler-to-logic-apps/request-trigger.png)

1. V případě triggeru žádosti můžete volitelně zadat schéma JSON, které pomůže návrháři aplikace logiky pochopit strukturu vstupů obsažených v příchozím volání triggeru žádosti a usnadnit výběr později v pracovním postupu.

   Do pole **schéma JSON textu žádosti** zadejte schéma, například:

   ![Schéma požadavku](./media/migrate-from-scheduler-to-logic-apps/request-schema.png)

   Pokud schéma nemáte, ale máte ukázkovou datovou část ve formátu JSON, můžete z této datové části vygenerovat schéma.

   1. V triggeru žádosti vyberte **použít ukázkovou datovou část k vygenerování schématu**.

   1. V části **Zadejte nebo vložte ukázkovou datovou část JSON**zadejte svou ukázkovou datovou část a vyberte **Hotovo**, například:

      ![Ukázková datová část](./media/migrate-from-scheduler-to-logic-apps/sample-payload.png)

      ```json
      {
         "runat": "2012-08-04T00:00Z",
         "endpoint": "https://www.bing.com"
      }
      ```

1. V části Trigger vyberte **Další krok**.

1. Do vyhledávacího pole zadejte `delay until` jako filtr. V seznamu akce vyberte tuto akci: **zpoždění do**

   Tato akce pozastaví pracovní postup aplikace logiky až do zadaného data a času.

   ![Přidat akci zpoždění do](./media/migrate-from-scheduler-to-logic-apps/delay-until.png)

1. Zadejte časové razítko, kde chcete spustit pracovní postup aplikace logiky.

   Když kliknete do pole **Timestamp (časové razítko** ), zobrazí se seznam dynamického obsahu, ve kterém můžete volitelně vybrat výstup z triggeru.

   ![Zadejte podrobnosti o zpoždění do.](./media/migrate-from-scheduler-to-logic-apps/delay-until-details.png)

1. Kliknutím na [stovky konektorů připravených k použití](../connectors/apis-list.md)přidejte všechny akce, které chcete spustit.

   Můžete například zahrnout akci HTTP, která odešle požadavek na adresu URL, nebo akce, které pracují s frontami úložiště, Service Bus frontami nebo Service Bus témata:

   ![Snímek obrazovky ukazuje prodlevu, dokud akce následovaná akcí H T T P s metodou POST.](./media/migrate-from-scheduler-to-logic-apps/request-http-action.png)

1. Až budete hotovi, uložte aplikaci logiky.

   ![Uložení aplikace logiky](./media/migrate-from-scheduler-to-logic-apps/save-logic-app.png)

   Při prvním uložení aplikace logiky se adresa URL koncového bodu pro Trigger požadavku vaší aplikace logiky zobrazí v poli **Adresa URL pro odeslání** . Pokud chcete zavolat aplikaci logiky a odeslat vstupy do aplikace logiky ke zpracování, použijte tuto adresu URL jako cíl volání.

   ![Adresa URL koncového bodu triggeru žádosti](./media/migrate-from-scheduler-to-logic-apps/request-endpoint-url.png)

1. Zkopírujte a uložte tuto adresu URL koncového bodu, abyste později mohli poslat ruční požadavek, který spustí vaši aplikaci logiky.

## <a name="start-a-one-time-job"></a>Spuštění jednorázové úlohy

Pokud chcete ručně spustit nebo aktivovat jednorázovou úlohu, odešlete volání adresy URL koncového bodu pro aktivační událost žádosti vaší aplikace logiky. V tomto volání určete vstupní nebo datovou část k odeslání, kterou jste si mohli popsaným způsobem popsat zadáním schématu.

Například pomocí aplikace pro publikování můžete vytvořit požadavek POST s nastavením podobným této ukázce a pak vybrat **Odeslat** pro vytvoření žádosti.

| Request – metoda | URL | Text | Hlavičky |
|----------------|-----|------|---------|
| **SPUŠTĚNÍ** | <*koncový bod – adresa URL*> | **získání** <p>**JSON (Application/JSON)** <p>Do pole **nezpracované** zadejte datovou část, kterou chcete v žádosti odeslat. <p>**Poznámka**: Toto nastavení automaticky nakonfiguruje hodnoty **hlaviček** . | **Klíč**: Content-Type <br>**Hodnota**: Application/JSON |
|||||

![Poslat požadavek na ruční aktivaci vaší aplikace logiky](./media/migrate-from-scheduler-to-logic-apps/postman-send-post-request.png)

Po odeslání hovoru se odpověď z vaší aplikace logiky zobrazí v poli **nezpracované** na kartě **tělo** . 

<a name="workflow-run-id"></a>

> [!IMPORTANT]
>
> Pokud chcete úlohu později zrušit, vyberte kartu **záhlaví** . Vyhledejte a zkopírujte hodnotu záhlaví **x-MS-Workflow-Run-ID** v odpovědi. 
>
> ![Odpověď](./media/migrate-from-scheduler-to-logic-apps/postman-response.png)

## <a name="cancel-a-one-time-job"></a>Zrušení jednorázové úlohy

V Logic Apps se každou jednorázovou úlohu spouští jako jediná instance spuštění aplikace logiky. Chcete-li zrušit jednorázovou úlohu, můžete použít [spuštění pracovního postupu – zrušit](/rest/api/logic/workflowruns/cancel) v REST API Logic Apps. Po odeslání volání triggeru zadejte [ID spuštění pracovního postupu](#workflow-run-id).

## <a name="schedule-recurring-jobs"></a>Plánování opakujících se úloh

1. V [Azure Portal](https://portal.azure.com)vytvořte prázdnou aplikaci logiky v návrháři aplikace logiky.

   Základní postup najdete v části [rychlý Start: Vytvoření první aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. Do vyhledávacího pole zadejte "opakování" jako filtr. V seznamu triggery vyberte tuto aktivační událost: **opakování** .

   ![Přidat aktivační událost opakování](./media/migrate-from-scheduler-to-logic-apps/recurrence-trigger.png)

1. V případě potřeby nastavte pokročilejší plán.

   ![Rozšířený plán](./media/migrate-from-scheduler-to-logic-apps/recurrence-advanced-schedule.png)

   Další informace o pokročilých možnostech plánování najdete v tématu [vytváření a spouštění opakujících se úloh a pracovních postupů s Azure Logic Apps](../connectors/connectors-native-recurrence.md).

1. Přidejte další požadované akce výběrem ze [stovek připravených k použití](../connectors/apis-list.md). V části Trigger vyberte **Další krok**. Najděte a vyberte akce, které chcete.

   Můžete například zahrnout akci HTTP, která odešle požadavek na adresu URL, nebo akce, které pracují s frontami úložiště, Service Bus frontami nebo Service Bus témata:

   ![Snímek obrazovky ukazuje akci H T T P s metodou POST.](./media/migrate-from-scheduler-to-logic-apps/recurrence-http-action.png)

1. Až budete hotovi, uložte aplikaci logiky.

   ![Uložení aplikace logiky](./media/migrate-from-scheduler-to-logic-apps/save-logic-app.png)

## <a name="advanced-setup"></a>Rozšířené nastavení

Tady jsou další způsoby, jak můžete úlohy přizpůsobit.

### <a name="retry-policy"></a>Zásady opakování

Chcete-li řídit způsob, jakým se akce pokusí znovu spustit v aplikaci logiky, když dojde k přerušované chybě, můžete nastavit [zásady opakování](../logic-apps/logic-apps-exception-handling.md#retry-policies) v nastavení jednotlivých akcí, například:

1. Otevřete nabídku se třemi tečkami (**...**) akce a vyberte **Nastavení**.

   ![Otevřít nastavení akce](./media/migrate-from-scheduler-to-logic-apps/action-settings.png)

1. Vyberte zásady opakování, které chcete. Další informace o jednotlivých zásadách najdete v tématu [zásady opakování](../logic-apps/logic-apps-exception-handling.md#retry-policies).

   ![Vybrat zásady opakování](./media/migrate-from-scheduler-to-logic-apps/retry-policy.png)

## <a name="handle-exceptions-and-errors"></a>Ošetření výjimek a chyb

Pokud ve službě Azure Scheduler neproběhne spuštění výchozí akce, můžete spustit akci alterative, která řeší chybový stav. V Azure Logic Apps můžete také provést stejnou úlohu.

1. V návrháři aplikace logiky nad akcí, kterou chcete zpracovat, přesuňte ukazatel myši na šipku mezi jednotlivými kroky a vyberte **Přidat paralelní větev**.

   ![Přidat paralelní větev](./media/migrate-from-scheduler-to-logic-apps/add-parallel-branch.png)

1. Vyhledejte a vyberte akci, kterou chcete spustit místo toho, jako alternativní akci.

   ![Přidat paralelní akci](./media/migrate-from-scheduler-to-logic-apps/add-parallel-action.png)

1. V alternativní akci otevřete nabídku se třemi tečkami (**...**) a vyberte **Konfigurovat spustit po**.

   ![Konfigurovat běh po](./media/migrate-from-scheduler-to-logic-apps/configure-run-after.png)

1. Zrušte zaškrtnutí políčka pro vlastnost **je úspěšná** . Vyberte tyto vlastnosti: **operace se nezdařila**, byla **vynechána**a **vypršel časový limit** .

   ![Nastavení vlastností spustit po](./media/migrate-from-scheduler-to-logic-apps/select-run-after-properties.png)

1. Jakmile budete hotovi, vyberte **Hotovo**.

Další informace o zpracování výjimek naleznete v tématu [zpracování chyb a výjimek – vlastnost runafter šablonové](../logic-apps/logic-apps-exception-handling.md#control-run-after-behavior).

## <a name="faq"></a>Nejčastější dotazy

<a name="retire-date"></a>

**Otázka**: kdy je vyřazení z Azure Scheduleru? <br>
Odpověď **: plán**Azure Scheduleru je naplánován na úplné vyřazení 31. prosince 2019. Důležité kroky, které je třeba provést před tímto datem a detailní časovou osou, najdete v tématu [rozšíření data vyřazení pro Scheduler do 31. prosince 2019](https://azure.microsoft.com/updates/extending-retirement-date-of-scheduler/). Obecné aktualizace najdete v tématu [Azure Updates – Scheduler](https://azure.microsoft.com/updates/?product=scheduler).

**Otázka**: co se stane s kolekcemi úloh a úlohami po vystavování služby? <br>
**O**: všechny kolekce úloh Scheduleru a úlohy přestanou běžet a jsou odstraněny ze systému.

**Otázka**: musím před migrací úloh Scheduleru do Logic Apps provést zálohování nebo provedení dalších úloh? <br>
Odpověď **: osvědčeným postupem je vždy**zálohovat práci. Ověřte, že aplikace logiky, které jste vytvořili, jsou spuštěné podle očekávání před odstraněním nebo zakázáním úloh plánovače.

**Otázka**: je k dispozici nástroj, který vám může přispět k migraci mých úloh z Scheduleru na Logic Apps? <br>
Odpověď **: každá**úloha Scheduleru je jedinečná, takže neexistují žádné nástroje, které se vejdou na všechny. Na základě vašich potřeb ale můžete [Tento skript upravit a migrovat úlohy Azure Scheduleru na Azure Logic Apps](https://github.com/Azure/logicapps/tree/master/scripts/scheduler-migration).

**Otázka**: kde můžu získat podporu pro migraci úloh plánovače? <br>
Odpověď **: Zde je několik**způsobů, jak získat podporu:

**Azure Portal**

Pokud má vaše předplatné Azure placený plán podpory, můžete v Azure Portal vytvořit žádost o technickou podporu. V opačném případě můžete vybrat jinou možnost podpory.

1. V hlavní nabídce [Azure Portal](https://portal.azure.com) vyberte možnost **help + podpora**.

1. V nabídce **Podpora** vyberte **Nová žádost o podporu**. Zadejte tyto informace o vaší žádosti:

   | Vlastnost | Hodnota |
   |---------|-------|
   | **Typ problému** | **Technické** |
   | **Předplatné** | <*Vaše předplatné – Azure*> |
   | **Služba** | V části **monitorování & Správa**vyberte **Plánovač**. Pokud nemůžete najít **Scheduler**, nejdřív vyberte **všechny služby** . |
   ||| 

1. Vyberte požadovanou možnost podpory. Pokud máte placený plán podpory, vyberte **Další**.

**Komunita**

* [Microsoft Q&Stránka s otázkou pro Azure Logic Apps](/answers/topics/azure-logic-apps.html)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-scheduler)

## <a name="next-steps"></a>Další kroky

* [Vytváření pravidelně běžících úloh a pracovních postupů pomocí Azure Logic Apps](../connectors/connectors-native-recurrence.md)