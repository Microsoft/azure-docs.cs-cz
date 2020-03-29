---
title: Migrace z Plánovače Azure do aplikací Azure Logic Apps
description: Zjistěte, jak můžete nahradit úlohy v Azure Scheduler, který je vyřazený, s Azure Logic Apps
services: scheduler
ms.service: scheduler
ms.suite: infrastructure-services
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: article
ms.date: 02/29/2020
ms.openlocfilehash: 90c3cc2e096b9b58465987bc53f718c5d06c6203
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78899104"
---
# <a name="migrate-azure-scheduler-jobs-to-azure-logic-apps"></a>Migrace úloh Plánovače Azure do aplikací Azure Logic Apps

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) nahrazuje Azure Scheduler, který [je vyřazen .](#retire-date) Pokud chcete pokračovat v práci s úlohami, které jste nastavili v Plánovači, migrujte do Azure Logic Apps co nejdříve podle tohoto článku. 
>
> Plánovač už není k dispozici na webu Azure Portal, ale rutiny [ROZHRANÍ REST API](/rest/api/scheduler) a Azure [Scheduler PowerShell](scheduler-powershell-reference.md) zůstávají k dispozici v tuto chvíli, takže můžete spravovat své úlohy a kolekce úloh.

Tento článek ukazuje, jak můžete naplánovat jednorázové a opakované úlohy vytvořením automatizovaných pracovních postupů pomocí Azure Logic Apps, nikoli pomocí Plánovače Azure. Při vytváření naplánovaných úloh pomocí logic apps získáte tyto výhody:

* Sestavte si svou úlohu pomocí vizuálního návrháře a [konektorů připravených k použití](../connectors/apis-list.md) ze stovek služeb, jako je Azure Blob Storage, Azure Service Bus, Office 365 Outlook a SAP.

* Spravujte každý naplánovaný pracovní postup jako prvotřídní prostředek Azure. Nemusíte se starat o koncept kolekce *úloh,* protože každá aplikace logiky je samostatný prostředek Azure.

* Spusťte více jednorázových úloh pomocí jedné aplikace logiky.

* Nastavte plány, které podporují časová pásma a automaticky se přizpůsobí letnímu času (DST).

Další informace najdete v [tématu Co je Azure Logic Apps?](../logic-apps/logic-apps-overview.md) nebo zkuste vytvořit první aplikaci logiky v tomto rychlém startu: [Vytvořte první aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* Chcete-li spustit aplikaci logiky odesláním požadavků HTTP, použijte nástroj, jako je [například desktopová aplikace Postman](https://www.getpostman.com/apps).

## <a name="migrate-by-using-a-script"></a>Migrace pomocí skriptu

Každá úloha plánovače je jedinečná, takže neexistuje žádný univerzální nástroj pro migraci úloh Plánovače do azure logic apps. [Tento skript](https://github.com/Azure/logicapps/tree/master/scripts/scheduler-migration) však můžete upravit tak, aby vyhovoval vašim potřebám.

## <a name="schedule-one-time-jobs"></a>Plánování jednorázových úloh

Můžete spustit více jednorázových úloh vytvořením pouze jedné aplikace logiky.

1. Na [webu Azure Portal](https://portal.azure.com)vytvořte prázdnou aplikaci logiky v Návrháři aplikací logiky.

   Základní kroky navazujte na [úvodní příručku: Vytvořte první aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. Do vyhledávacího pole `when a http request` zadejte, chcete-li najít aktivační událost Požadavku. Ze seznamu aktivačních událostí vyberte tuto aktivační událost: **Při přijetí požadavku HTTP**

   ![Přidat aktivační událost "Žádost"](./media/migrate-from-scheduler-to-logic-apps/request-trigger.png)

1. Pro požadavek aktivační události můžete volitelně poskytnout schéma JSON, který pomáhá Návrhář aplikace logiky pochopit strukturu pro vstupy zahrnuté v příchozí volání request aktivační události a usnadňuje výstupy pro pozdější výběr v pracovním postupu.

   Do pole Schéma **JSON v požadavku** zadejte schéma, například:

   ![Schéma požadavku](./media/migrate-from-scheduler-to-logic-apps/request-schema.png)

   Pokud nemáte schéma, ale máte ukázkovou datovou část ve formátu JSON, můžete vygenerovat schéma z této datové části.

   1. V aktivační události Požadavek vyberte **použít ukázkovou datovou část ke generování schématu**.

   1. V části **Zadejte nebo vložte ukázkovou datovou část JSON**, zadejte ukázkovou datovou část a vyberte **Hotovo**, například:

      ![Datová část vzorku](./media/migrate-from-scheduler-to-logic-apps/sample-payload.png)

      ```json
      {
         "runat": "2012-08-04T00:00Z",
         "endpoint": "https://www.bing.com"
      }
      ```

1. Pod aktivační událostí vyberte **Další krok**.

1. Do vyhledávacího pole `delay until` zadejte jako filtr. V seznamu akcí vyberte tuto akci: **Zpoždění do**

   Tato akce pozastaví pracovní postup aplikace logiky až do zadaného data a času.

   ![Přidat akci "Zdržet do"](./media/migrate-from-scheduler-to-logic-apps/delay-until.png)

1. Zadejte časové razítko, kdy chcete spustit pracovní postup aplikace logiky.

   Když klepnete do pole **Časové razítko,** zobrazí se seznam dynamického obsahu, takže můžete volitelně vybrat výstup z aktivační události.

   ![Zadejte podrobnosti "Zpoždění do"](./media/migrate-from-scheduler-to-logic-apps/delay-until-details.png)

1. Přidejte všechny další akce, které chcete spustit výběrem ze [stovek konektorů připravených k použití](../connectors/apis-list.md).

   Můžete například zahrnout akci HTTP, která odešle požadavek na adresu URL, nebo akce, které fungují s frontami úložiště, frontami služby Service Bus nebo tématy služby Service Bus:

   ![Akce HTTP](./media/migrate-from-scheduler-to-logic-apps/request-http-action.png)

1. Až budete hotovi, uložte aplikaci logiky.

   ![Uložení aplikace logiky](./media/migrate-from-scheduler-to-logic-apps/save-logic-app.png)

   Při prvním uložení aplikace logiky se v poli **ADRESA URL HTTP POST** zobrazí adresa URL koncového bodu pro aktivační událost požadavku aplikace logiky. Pokud chcete volat aplikaci logiky a odesílat vstupy do aplikace logiky ke zpracování, použijte tuto adresu URL jako cíl volání.

   ![Adresa URL aktivačního bodu aktivační události požadavku uložit](./media/migrate-from-scheduler-to-logic-apps/request-endpoint-url.png)

1. Zkopírujte a uložte tuto adresu URL koncového bodu, abyste později mohli odeslat ruční požadavek, který spustí aplikaci logiky.

## <a name="start-a-one-time-job"></a>Zahájení jednorázové úlohy

Chcete-li ručně spustit nebo spustit jednorázovou úlohu, odešlete volání na adresu URL koncového bodu pro aktivační událost požadavku aplikace logiky. V tomto volání zadejte vstupní nebo datovou část k odeslání, které jste pravděpodobně popsali dříve zadáním schématu.

Například pomocí aplikace Postman můžete vytvořit požadavek POST s nastavením podobným této ukázce a pak vybrat **Odeslat,** chcete-li žádost provést.

| Metoda požadavku | zprostředkovatele identity | Tělo | Hlavičky |
|----------------|-----|------|---------|
| **POST** | <*adresa URL koncového bodu*> | **Syrové** <p>**JSON(aplikace/json)** <p>Do pole **nezpracované** zadejte datovou část, kterou chcete odeslat v požadavku. <p>**Poznámka:** Toto nastavení automaticky konfiguruje hodnoty **záhlaví.** | **Klíč**: Typ obsahu <br>**Hodnota**: aplikace/json |
|||||

![Odeslat požadavek na ruční spuštění aplikace logiky](./media/migrate-from-scheduler-to-logic-apps/postman-send-post-request.png)

Po odeslání hovoru se odpověď z aplikace logiky zobrazí pod **nezpracovaným** polem na kartě **Tělo.** 

<a name="workflow-run-id"></a>

> [!IMPORTANT]
>
> Pokud chcete úlohu později zrušit, vyberte kartu **Záhlaví.** **x-ms-workflow-run-id** 
>
> ![Odpověď](./media/migrate-from-scheduler-to-logic-apps/postman-response.png)

## <a name="cancel-a-one-time-job"></a>Zrušení jednorázové úlohy

V Logic Apps se každá jednorázová úloha spustí jako jedna instance spuštění aplikace logiky. Chcete-li zrušit jednorázovou úlohu, můžete použít [spuštění pracovního postupu – zrušit](https://docs.microsoft.com/rest/api/logic/workflowruns/cancel) v rozhraní REST API logic apps. Při odeslání volání na aktivační událost zadejte [ID spuštění pracovního postupu](#workflow-run-id).

## <a name="schedule-recurring-jobs"></a>Naplánovat opakované úlohy

1. Na [webu Azure Portal](https://portal.azure.com)vytvořte prázdnou aplikaci logiky v Návrháři aplikací logiky.

   Základní kroky navazujte na [úvodní příručku: Vytvořte první aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. Do vyhledávacího pole zadejte jako filtr "opakování". Ze seznamu aktivačních událostí vyberte tuto aktivační událost: **Opakování**

   ![Přidat aktivační událost Opakování](./media/migrate-from-scheduler-to-logic-apps/recurrence-trigger.png)

1. Pokud chcete, nastavte pokročilejší plán.

   ![Rozšířený plán](./media/migrate-from-scheduler-to-logic-apps/recurrence-advanced-schedule.png)

   Další informace o rozšířených možnostech plánování najdete v [tématu Vytváření a spouštění opakovaných úloh a pracovních postupů pomocí Aplikací Logika Azure](../connectors/connectors-native-recurrence.md).

1. Přidejte další požadované akce výběrem [ze stovek připravených akcí](../connectors/apis-list.md). Pod aktivační událostí vyberte **Další krok**. Najděte a vyberte požadované akce.

   Můžete například zahrnout akci HTTP, která odešle požadavek na adresu URL, nebo akce, které fungují s frontami úložiště, frontami služby Service Bus nebo tématy služby Service Bus:

   ![Akce HTTP](./media/migrate-from-scheduler-to-logic-apps/recurrence-http-action.png)

1. Až budete hotovi, uložte aplikaci logiky.

   ![Uložení aplikace logiky](./media/migrate-from-scheduler-to-logic-apps/save-logic-app.png)

## <a name="advanced-setup"></a>Rozšířené nastavení

Zde jsou další způsoby, jak můžete přizpůsobit své úlohy.

### <a name="retry-policy"></a>Zásady opakování

Chcete-li řídit způsob, jakým se akce pokusí znovu spustit v aplikaci logiky, když dojde k občasným selháním, můžete nastavit [zásady opakování](../logic-apps/logic-apps-exception-handling.md#retry-policies) v nastavení každé akce, například:

1. Otevřete nabídku elipsy akce (**...**) a vyberte **Nastavení**.

   ![Otevřít nastavení akce](./media/migrate-from-scheduler-to-logic-apps/action-settings.png)

1. Vyberte požadovanou zásadu opakování. Další informace o jednotlivých zásadách naleznete v [tématu Zásady opakování](../logic-apps/logic-apps-exception-handling.md#retry-policies).

   ![Vybrat zásadu opakování](./media/migrate-from-scheduler-to-logic-apps/retry-policy.png)

## <a name="handle-exceptions-and-errors"></a>Ošetření výjimek a chyb

V Plánovačazure, pokud se nepodaří spustit výchozí akci, můžete spustit alterativní akci, která řeší chybový stav. V Azure Logic Apps můžete také provést stejný úkol.

1. V Návrháři aplikací logiky nad akcí, kterou chcete zpracovat, přesuňte ukazatel myši na šipku mezi kroky a vyberte **Přidat paralelní větev**.

   ![Přidat paralelní větev](./media/migrate-from-scheduler-to-logic-apps/add-parallel-branch.png)

1. Místo alternativní akce vyhledejte a vyberte akci, kterou chcete spustit.

   ![Přidat paralelní akci](./media/migrate-from-scheduler-to-logic-apps/add-parallel-action.png)

1. V alternativní akci otevřete nabídku Elipsy (**...**) a vyberte **Konfigurovat spuštění po .**

   ![Konfigurovat spuštění po](./media/migrate-from-scheduler-to-logic-apps/configure-run-after.png)

1. Zrušte zaškrtnutí políčka pro **vlastnost is successful.** Vyberte tyto vlastnosti: **se nezdařilo**, **je přeskočeno**a **časový čas**

   ![Nastavit vlastnosti "spustit po"](./media/migrate-from-scheduler-to-logic-apps/select-run-after-properties.png)

1. Jakmile budete hotovi, vyberte **Hotovo**.

Další informace o zpracování výjimek naleznete v [tématu Zpracování chyb a výjimek - RunAfter vlastnost](../logic-apps/logic-apps-exception-handling.md#control-run-after-behavior).

## <a name="faq"></a>Nejčastější dotazy

<a name="retire-date"></a>

**Otázka:** Kdy je Azure Scheduler odchodu do důchodu? <br>
**A**: Plánovač Azure je naplánováno úplné vyřazení na 31 prosince 2019. Důležité kroky, které je třeba provést před tímto datem, a podrobnou časovou osu naleznete [v tématu Prodloužení data odchodu plánovače na 31.](https://azure.microsoft.com/updates/extending-retirement-date-of-scheduler/) Obecné aktualizace najdete v [tématu Aktualizace Azure – Plánovač](https://azure.microsoft.com/updates/?product=scheduler).

**Otázka:** Co se stane s mými kolekcemi pracovních míst a úlohami po vyřazení služby? <br>
**A**: Všechny kolekce úloh plánovače a úlohy zastavit a jsou odstraněny ze systému.

**Otázka:** Musím zálohovat nebo provádět jiné úkoly před migrací úloh plánovače do logicových aplikací? <br>
**A:** Jako osvědčený postup vždy zálohujte svou práci. Zkontrolujte, zda aplikace logiky, které jste vytvořili, běží podle očekávání před odstraněním nebo zakázáním úloh Plánovače.

**Otázka:** Existuje nástroj, který mi může pomoci migrovat úlohy z Plánovače na logice apps? <br>
**A:** Každá úloha plánovače je jedinečná, takže univerzální nástroj neexistuje. Na základě vašich potřeb však můžete [tento skript upravit a migrovat úlohy Plánovače Azure do Azure Logic Apps](https://github.com/Azure/logicapps/tree/master/scripts/scheduler-migration).

**Otázka:** Kde lze získat podporu pro migraci úloh plánovače? <br>
**A**: Zde je několik způsobů, jak získat podporu:

**Portál Azure**

Pokud má vaše předplatné Azure placený plán podpory, můžete na webu Azure Portal vytvořit žádost o technickou podporu. V opačném případě můžete vybrat jinou možnost podpory.

1. V hlavní nabídce [portálu Azure](https://portal.azure.com) vyberte **Nápověda + podpora**.

1. V nabídce **Podpora** vyberte **Nový požadavek na podporu**. Poskytněte tyto informace o vaší žádosti:

   | Vlastnost | Hodnota |
   |---------|-------|
   | **Typ problému** | **Technické** |
   | **Předplatné** | <*vaše předplatné Azure*> |
   | **Služby** | V části **Sledování správy &** vyberte **Plánovač**. Pokud plánovač nemůžete **najít**, vyberte nejdřív **Všechny služby.** |
   ||| 

1. Vyberte požadovanou možnost podpory. Pokud máte placený plán podpory, vyberte **Další**.

**Komunita**

* [Fórum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-scheduler)

## <a name="next-steps"></a>Další kroky

* [Vytvářejte pravidelně spouštěné úlohy a pracovní postupy pomocí aplikací Azure Logic Apps](../connectors/connectors-native-recurrence.md)