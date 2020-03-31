---
title: Dávkové zprávy jako skupinu
description: Odesílání a přijímání zpráv ve skupinách mezi pracovními postupy pomocí dávkového zpracování v Aplikacích Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, jonfan, logicappspm
ms.topic: article
ms.date: 01/16/2019
ms.openlocfilehash: e48d2bb2ffce0dd4f9293417534165165d426784
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75666750"
---
# <a name="send-receive-and-batch-process-messages-in-azure-logic-apps"></a>Odesílání, přijímání a dávkové zpracování zpráv v aplikacích Azure Logic Apps

Chcete-li odesílat a zpracovávat zprávy společně určitým způsobem jako skupiny, můžete vytvořit dávkové řešení, které shromažďuje zprávy do *dávky,* dokud nebudou splněna zadaná kritéria pro uvolnění a zpracování dávkových zpráv. Dávkování může snížit, jak často aplikace logiky zpracovává zprávy. Tento článek ukazuje, jak vytvořit řešení dávkování vytvořením dvou aplikací logiky v rámci stejného předplatného Azure, oblasti Azure a podle tohoto konkrétního pořadí: 

* Aplikace logiky ["dávkový příjemce",](#batch-receiver) která přijímá a shromažďuje zprávy do dávky, dokud není splněna zadaná kritéria pro uvolnění a zpracování těchto zpráv.

  Ujistěte se, že jste nejprve vytvořili dávkový přijímač, abyste mohli později vybrat cíl dávky při vytváření odesílatele dávky.

* Jedna nebo více [aplikací logiky "batch sender",](#batch-sender) které odesílají zprávy dříve vytvořenému dávkovému příjemci. 

   Můžete také zadat jedinečný klíč, například číslo zákazníka, který *rozdělí* nebo rozdělí cílovou dávku na logické podmnožiny založené na tomto klíči. Tímto způsobem může aplikace příjemce shromažďovat všechny položky se stejným klíčem a zpracovávat je společně.

Ujistěte se, že váš dávkový příjemce a dávkový odesílatel sdílejí stejné předplatné Azure *a* oblast Azure. Pokud tomu tak není, nelze vybrat dávkový příjemce při vytváření dávkového odesílatele, protože nejsou navzájem viditelné.

## <a name="prerequisites"></a>Požadavky

Chcete-li postupovat podle tohoto příkladu, potřebujete tyto položky:

* Předplatné Azure. Pokud předplatné nemáte, můžete [začít s bezplatným účtem Azure](https://azure.microsoft.com/free/). Nebo [si zaregistrujte předplatné s průběžným platbou](https://azure.microsoft.com/pricing/purchase-options/).

* E-mailový účet s [libovolným poskytovatelem e-mailu podporovaným službou Azure Logic Apps](../connectors/apis-list.md)

* Základní znalosti o [vytváření aplikací logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md) 

* Pokud chcete použít Visual Studio místo portálu Azure, ujistěte se, že jste [nastavili Visual Studio pro práci s Logic Apps](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

<a name="batch-receiver"></a>

## <a name="create-batch-receiver"></a>Vytvořit dávkový přijímač

Před odesláním zpráv do dávky musí tato dávka nejprve existovat jako cíl, kam tyto zprávy odesíláte. Takže nejprve musíte vytvořit aplikaci logiky "dávkový přijímač", která začíná aktivační událostí **Batch.** Tímto způsobem při vytváření aplikace logiky "dávkového odesílatele" můžete vybrat aplikaci logiky dávkového příjemce. Dávkový příjemce pokračuje ve shromažďování zpráv, dokud nejsou splněna zadaná kritéria pro uvolnění a zpracování těchto zpráv. Zatímco dávkové příjemce nemusí vědět nic o dávkových odesílatelích, odesilatelové listy musí znát cíl, kam odesílají zprávy. 

1. Na [webu Azure Portal](https://portal.azure.com) nebo Visual Studiu vytvořte aplikaci logiky s tímto názvem: BatchReceiver. 

2. V Návrháři aplikací logiky přidejte aktivační událost **Batch,** která spustí pracovní postup aplikace logiky. Do vyhledávacího pole zadejte jako filtr "dávka". Vybrat tuto aktivační událost: **Dávkové zprávy**

   ![Přidání aktivační události "Dávkové zprávy"](./media/logic-apps-batch-process-send-receive-messages/add-batch-receiver-trigger.png)

3. Nastavte tyto vlastnosti pro dávkový přijímač: 

   | Vlastnost | Popis | 
   |----------|-------------|
   | **Dávkový režim** | - **Inline**: Pro definování kritérií pro uvolnění uvnitř aktivační události dávky <br>- **Účet integrace**: Pro definování více konfigurací kritérií vydání prostřednictvím [účtu integrace](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md). Pomocí účtu integrace můžete udržovat tyto konfigurace na jednom místě, nikoli v samostatných aplikacích logiky. | 
   | **Název dávky** | Název dávky, který je v tomto příkladu "TestBatch" a platí pouze pro dávkový režim **Inline.** |  
   | **Kritéria vydání** | Platí pouze pro dávkový režim **Inline** a vybere kritéria, která mají splňovat před zpracováním každé dávky: <p>- **Počet zpráv na základě**: Uvolnění dávky na základě počtu zpráv shromážděných dávkou. <br>- **Na základě velikosti**: Uvolněte dávku na základě celkové velikosti v bajtů pro všechny zprávy shromážděné tímto šarží. <br>- **Plán**: Uvolněte dávku na základě plánu opakování, který určuje interval a frekvenci. V rozšířených možnostech můžete také vybrat časové pásmo a zadat počáteční datum a čas. <br>- **Vybrat vše**: Použijte všechna zadaná kritéria. | 
   | **Počet zpráv** | Počet zpráv, které mají být v dávce shromažďovány, například 10 zpráv. Limit dávky je 8 000 zpráv. | 
   | **Velikost dávky** | Celková velikost v bajtů shromáždit v dávce, například 10 MB. Limit velikosti dávky je 80 MB. | 
   | **Plán** | Interval a frekvence mezi uvolňováním dávky, například 10 minut. Minimální opakování je 60 sekund nebo 1 minuta. Zlomkové minuty jsou účinně zaokrouhleny nahoru na 1 minutu. Chcete-li určit časové pásmo nebo počáteční datum a čas, zvolte **Zobrazit upřesňující možnosti**. | 
   ||| 

   > [!NOTE]
   > 
   > Pokud změníte kritéria vydání, zatímco aktivační událost má stále dávkové, ale neodeslané zprávy, aktivační událost používá aktualizovaná kritéria vydání pro zpracování neodeslaných zpráv. 

   Tento příklad ukazuje všechna kritéria, ale pro vlastní testování vyzkoušejte pouze jedno kritérium:

   ![Poskytnout podrobnosti aktivační události dávky](./media/logic-apps-batch-process-send-receive-messages/batch-receiver-criteria.png)

4. Nyní přidejte jednu nebo více akcí, které zpracovávají každou dávku. 

   V tomto příkladu přidejte akci, která odešle e-mail při spuštění dávky. 
   Aktivační událost se spustí a odešle e-mail, pokud dávka má 10 zpráv, dosáhne 10 MB nebo po 10 minutách.

   1. Pod aktivační událostí dávky zvolte **Nový krok**.

   2. Při filtrování do pole hledání zadejte „odeslat e-mail“.
   Na základě vašeho poskytovatele e-mailu vyberte e-mailový konektor.

      Pokud máte například osobní účet, @outlook.com například nebo @hotmail.com, vyberte konektor Outlook.com. 
      Pokud máte účet Gmail, vyberte konektor Gmailu. 
      Tento příklad používá Office 365 Outlook. 

   3. Vyberte tuto akci: **Odeslání e-mailu – <*poskytovatele e-mailu* > **

      Například:

      ![Vyberte akci "Odeslat e-mail" pro poskytovatele e-mailu.](./media/logic-apps-batch-process-send-receive-messages/batch-receiver-send-email-action.png)

5. Pokud budete vyzváni, přihlaste se k e-mailovému účtu. 

6. Nastavte vlastnosti akce, kterou jste přidali.

   * Do pole **Komu** zadejte e-mailovou adresu příjemce. 
   Pro účely testování můžete použít svou vlastní e-mailovou adresu.

   * Až se v poli **Předmět** zobrazí seznam dynamického obsahu, vyberte pole **Název oddílu.**

     ![Ze seznamu dynamického obsahu vyberte "Název oddílu"](./media/logic-apps-batch-process-send-receive-messages/send-email-action-details.png)

     Později můžete v dávkovém odesílateli zadat jedinečný klíč oddílu, který rozdělí cílovou dávku na logické podmnožiny, kde můžete odesílat zprávy. 
     Každá sada má jedinečné číslo, které je generováno aplikací logiky odesílaní dávky. 
     Tato funkce umožňuje použít jednu dávku s více podmnožiny a definovat každou podmnožinu s názvem, který zadáte.

     > [!IMPORTANT]
     > Oddíl má limit 5 000 zpráv nebo 80 MB. Pokud je splněna některá z podmínek, Logic Apps může uvolnit dávku, i když není splněna definovaná podmínka vydání.

   * Až se v poli **Tělo** zobrazí seznam dynamického obsahu, vyberte pole **Id zprávy.** 

     Návrhář aplikací logiky automaticky přidá smyčku "Pro každý" kolem akce odeslat e-mail, protože tato akce považuje výstup z předchozí akce jako kolekci, nikoli jako dávku. 

     ![V části "Tělo" vyberte možnost Id zprávy.](./media/logic-apps-batch-process-send-receive-messages/send-email-action-details-for-each.png)

7.  Uložte svou aplikaci logiky. Nyní jste vytvořili dávkový přijímač.

    ![Uložení aplikace logiky](./media/logic-apps-batch-process-send-receive-messages/save-batch-receiver-logic-app.png)

8. Pokud používáte Visual Studio, ujistěte se, že [nasadíte aplikaci logiky dávkového přijímače do Azure](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure). V opačném případě nelze vybrat dávkový přijímač při vytváření dávkového odesílatele.

<a name="batch-sender"></a>

## <a name="create-batch-sender"></a>Vytvořit odevšad dávky

Teď vytvořte jednu nebo více dávkových aplikací logiky odesílatele, které odesílají zprávy do aplikace logiky dávkového příjemce. V každém odesílate šarži zadáte dávkový příjemce a název dávky, obsah zprávy a všechna další nastavení. Volitelně můžete poskytnout jedinečný klíč oddílu rozdělit dávky do logických podmnožiny pro shromažďování zpráv s tímto klíčem. 

* Ujistěte se, že jste již [vytvořili dávkový přijímač,](#batch-receiver) takže při vytváření dávkového odesílatele můžete jako cílovou dávku vybrat existující dávkový příjemce. Zatímco dávkové příjemce nemusí vědět nic o dávkových odesílatelích, odesilatelům dávek musí vědět, kam mají odesílat zprávy. 

* Ujistěte se, že váš dávkový příjemce a odesio je sdílí stejnou oblast Azure *a* předplatné Azure. Pokud tomu tak není, nelze vybrat dávkový příjemce při vytváření dávkového odesílatele, protože nejsou navzájem viditelné.

1. Vytvořit jinou aplikaci logiky s tímto názvem: "BatchSender"

   1. Do vyhledávacího pole zadejte jako filtr "opakování". 
   Vybrat tuto aktivační událost: **Opakování – plán**

      ![Přidání aktivační události "Opakování - plán"](./media/logic-apps-batch-process-send-receive-messages/add-schedule-trigger-batch-sender.png)

   2. Nastavte frekvenci a interval pro spuštění aplikace logiky odesílatele každou minutu.

      ![Nastavení frekvence a intervalu pro aktivační událost opakování](./media/logic-apps-batch-process-send-receive-messages/recurrence-trigger-batch-sender-details.png)

2. Přidejte novou akci pro odesílání zpráv do dávky.

   1. Pod aktivační událostí opakování zvolte **Nový krok**.

   2. Do vyhledávacího pole zadejte jako filtr "dávka". 
   Vyberte seznam **Akce** a pak vyberte tuto akci: **Zvolte pracovní postup Logic Apps s dávkovou aktivační událostí – Odeslat zprávy do dávky.**

      ![Vyberte možnost Vybrat pracovní postup logic apps s dávkovou aktivační událostí.](./media/logic-apps-batch-process-send-receive-messages/send-messages-batch-action.png)

   3. Vyberte aplikaci logiky dávkového příjemce, kterou jste dříve vytvořili.

      ![Vyberte aplikaci logiky "dávkový přijímač"](./media/logic-apps-batch-process-send-receive-messages/batch-sender-select-batch-receiver.png)

      > [!NOTE]
      > Seznam také zobrazuje všechny ostatní aplikace logiky, které mají dávkové aktivační události. 
      > 
      > Pokud používáte Visual Studio a nevidíte žádné dávkové přijímače, které byste měli vybrat, zkontrolujte, jestli jste dávkový přijímač nasadili do Azure. Pokud jste tak neučinili, přečtěte si, jak [nasadit aplikaci logiky dávkového přijímače do Azure](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure). 

   4. Vyberte tuto akci: **Batch_messages - <*dávkový přijímač* > **

      ![Vyberte tuto akci: "Batch_messages - <> aplikace logiky"](./media/logic-apps-batch-process-send-receive-messages/batch-sender-select-batch.png)

3. Nastavte vlastnosti odesílatele dávky:

   | Vlastnost | Popis | 
   |----------|-------------| 
   | **Název dávky** | Název dávky definovaný aplikací logiky příjemce, která je "TestBatch" v tomto příkladu <p>**Důležité:** Název dávky získá ověřena za běhu a musí odpovídat názvu určeného aplikací logiky příjemce. Změna názvu dávky způsobí selhání odesílatele dávky. | 
   | **Obsah zprávy** | Obsah zprávy, kterou chcete odeslat | 
   ||| 

   V tomto příkladu přidejte tento výraz, který vloží aktuální datum a čas do obsahu zprávy, který odešlete do dávky:

   1. Klikněte do pole **Obsah zprávy.** 

   2. Po zobrazení seznamu dynamického obsahu zvolte **Výraz**. 

   3. Zadejte `utcnow()`výraz a pak zvolte **OK**. 

      ![V části "Obsah zprávy" zvolte "Expression", zadejte "utcnow()" a zvolte "OK".](./media/logic-apps-batch-process-send-receive-messages/batch-sender-details.png)

4. Nyní nastavte oddíl pro dávku. V akci "BatchReceiver" zvolte **Zobrazit upřesňující volby** a nastavte tyto vlastnosti:

   | Vlastnost | Popis | 
   |----------|-------------| 
   | **Název oddílu** | Volitelný jedinečný klíč oddílu, který se má použít k rozdělení cílové dávky na logické podmnožiny a shromažďování zpráv založených na tomto klíči. | 
   | **ID zprávy** | Volitelný identifikátor zprávy, který je vygenerovaný globálně jedinečný identifikátor (GUID) při vyprázdnění | 
   ||| 

   V tomto příkladu v poli **Název oddílu** přidejte výraz, který generuje náhodné číslo mezi jedním a pěti. Ponechejte pole **ID zprávy** prázdné.
   
   1. Klikněte do pole **Název oddílu,** aby se zobrazil seznam dynamického obsahu. 

   2. V seznamu dynamického obsahu vyberte **Výraz**.
   
   3. Zadejte `rand(1,6)`výraz a pak zvolte **OK**.

      ![Nastavení oddílu pro cílovou dávku](./media/logic-apps-batch-process-send-receive-messages/batch-sender-partition-advanced-options.png)

      Tato funkce **rand** generuje číslo mezi jednou a pěti. 
      Takže tuto dávku rozdělujete na pět číslovaných oddílů, které tento výraz dynamicky nastaví.

5. Uložte svou aplikaci logiky. Aplikace logiky odesílatele nyní vypadá podobně jako v tomto příkladu:

   ![Uložení aplikace logiky odesílatele](./media/logic-apps-batch-process-send-receive-messages/batch-sender-finished.png)

## <a name="test-your-logic-apps"></a>Testování aplikací logiky

Chcete-li otestovat dávkové řešení, nechte aplikace logiky několik minut spuštěné. Brzy začnete dostávat e-maily ve skupinách po pěti, všechny se stejným klíčem oddílu.

Aplikace logiky odesílané dávky se spouští každou minutu, generuje náhodné číslo mezi jednou a pěti a používá toto generované číslo jako klíč oddílu pro cílovou dávku, ve které jsou odesílány zprávy. Pokaždé, když dávka má pět položek se stejným klíčem oddílu, aplikace logiky dávkového příjemce se spustí a odešle poštu pro každou zprávu.

> [!IMPORTANT]
> Po dokončení testování se ujistěte, že jste zakázali aplikaci logiky BatchSender, abyste přestali odesílat zprávy a nepřetěžovali doručenou poštu.

## <a name="next-steps"></a>Další kroky

* [Dávky a odesílání zpráv EDI](../logic-apps/logic-apps-scenario-edi-send-batch-messages.md)
* [Stavět na definicích aplikací logiky pomocí JSON](../logic-apps/logic-apps-author-definitions.md)
* [Vytvoření aplikace bez serveru ve Visual Studiu s aplikacemi a funkcemi Azure Logic Apps](../logic-apps/logic-apps-serverless-get-started-vs.md)
* [Zpracování výjimek a protokolování chyb pro aplikace logiky](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
