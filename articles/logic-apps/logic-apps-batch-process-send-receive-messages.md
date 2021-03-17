---
title: Batch zpracuje zprávy jako skupinu.
description: Odesílání a příjem zpráv ve skupinách mezi pracovními postupy pomocí dávkového zpracování v Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, jonfan, logicappspm
ms.topic: article
ms.date: 07/31/2020
ms.openlocfilehash: 0985afe3ddfd0d9de3c36ad6b030b6f259708c88
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87458216"
---
# <a name="send-receive-and-batch-process-messages-in-azure-logic-apps"></a>Odesílání, příjem a dávkové zpracování zpráv v Azure Logic Apps

Chcete-li odesílat a zpracovávat zprávy v konkrétním způsobu jako skupiny, můžete vytvořit řešení pro dávkování. Toto řešení shromáždí zprávy do *dávky* a počká, dokud nebudou zadaná kritéria splněna před vydáním a zpracováním dávkových zpráv. Dávkování může snížit, jak často aplikace logiky zpracovávají zprávy.

Tento článek ukazuje, jak sestavit dávkové řešení vytvořením dvou aplikací logiky v rámci stejného předplatného Azure, oblasti Azure a v tomto pořadí:

1. Aplikace logiky pro [přijímače "Batch"](#batch-receiver) , která přijímá a shromažďuje zprávy do dávky, dokud nebudou zadaná kritéria splněna pro uvolnění a zpracování těchto zpráv. Ujistěte se, že jste nejprve vytvořili tohoto přijímače Batch, abyste později mohli při vytváření odesílatele Batch vybrat cíl dávky.

1. Jedno nebo několik "aplikací logiky [" Batch "](#batch-sender) , které odesílají zprávy na dříve vytvořeného příjemce dávky.

   Můžete také zadat jedinečný klíč, například číslo zákazníka *, který rozdělí* nebo rozdělí cílovou dávku na logické podmnožiny založené na tomto klíči. V takovém případě může aplikace přijímače shromažďovat všechny položky se stejným klíčem a zpracovávat je společně.

Váš přijímač Batch a Batch odesilatel musí sdílet stejné předplatné Azure *a* oblast Azure. Pokud ne, nemůžete při vytváření odesílatele Batch vybrat přijímače Batch, protože nejsou navzájem viditelné.

## <a name="prerequisites"></a>Požadavky

* Účet a předplatné Azure. Pokud předplatné nemáte, můžete [začít s bezplatným účtem Azure](https://azure.microsoft.com/free/). Případně si [můžete zaregistrovat předplatné s průběžnými platbami](https://azure.microsoft.com/pricing/purchase-options/).

* E-mailový účet s jakýmkoli [poskytovatelem e-mailu, který podporuje Azure Logic Apps](../connectors/apis-list.md)

  > [!IMPORTANT]
  > Pokud chcete použít konektor Gmail, můžou tento konektor používat jenom obchodní účty G-Suite bez omezení v Logic Apps. Pokud máte účet příjemce Gmail, můžete tento konektor použít jenom pro konkrétní služby schválené v Google, nebo můžete [vytvořit klientskou aplikaci Google pro ověřování pomocí konektoru Gmail](/connectors/gmail/#authentication-and-bring-your-own-application). Další informace najdete v tématu [zásady zabezpečení a ochrany osobních údajů pro konektory Google v Azure Logic Apps](../connectors/connectors-google-data-security-privacy-policy.md).

* Základní znalosti o [tom, jak vytvářet aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Chcete-li použít sadu Visual Studio místo Azure Portal, ujistěte se, že jste [nastavili sadu Visual Studio pro práci s Logic Apps](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

<a name="batch-receiver"></a>

## <a name="create-batch-receiver"></a>Vytvořit přijímač Batch

Než budete moci odeslat zprávy do dávky, tato dávková operace musí nejprve existovat jako cíl, kam tyto zprávy odešlete. Nejprve je třeba vytvořit aplikaci logiky "Batch Receiver", která začíná triggerem **dávky** . Tímto způsobem můžete při vytváření aplikace logiky pro dávkové příjemce vybrat aplikaci Logic Receiver Logic. Přijímač dávky pokračuje ve shromažďování zpráv, dokud nebudou zadaná kritéria splněna pro uvolnění a zpracování těchto zpráv. I když přijímače dávek nemusí nic informovat o modulech pro odesílání dávek, musí odesílatelé dávky znát cíl, kam zprávy odesílají.

1. V [Azure Portal](https://portal.azure.com) nebo Visual Studiu vytvořte aplikaci logiky s tímto názvem: `BatchReceiver`

1. V návrháři aplikace logiky přidejte Trigger **Batch** , který spustí pracovní postup vaší aplikace logiky. Do vyhledávacího pole zadejte `batch` a vyberte tuto aktivační událost: **zprávy Batch**

   ![Přidat aktivační událost "zprávy dávky"](./media/logic-apps-batch-process-send-receive-messages/add-batch-receiver-trigger.png)

1. Nastavte tyto vlastnosti pro přijímače Batch:

   | Vlastnost | Popis |
   |----------|-------------|
   | **Dávkový režim** | - **Inline**: pro definování kritérií vydání v rámci triggeru dávky <br>- **Účet pro integraci**: pro definování více konfigurací kritérií vydání prostřednictvím [účtu pro integraci](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md). Pomocí účtu pro integraci můžete tyto konfigurace udržovat na jednom místě a ne v samostatných aplikacích logiky. |
   | **Název dávky** | Název pro dávku, který je v tomto příkladu "TestBatch" a vztahuje se pouze na **vložený** režim dávky |
   | **Kritéria uvolnění** | Platí jenom pro **vložený** režim dávky a vybírá kritéria, která se mají splnit před zpracováním každé dávky: <p>- **Založené na počtu zpráv**: vyuvolněte dávku na základě počtu zpráv shromážděných dávkou. <br>- **Na základě velikosti**: vyuvolněte dávku na základě celkové velikosti v bajtech pro všechny zprávy shromážděné touto dávkou. <br>- **Plán**: vyuvolněte dávku na základě plánu opakování, který určuje interval a frekvenci. V pokročilých možnostech můžete také vybrat časové pásmo a zadat počáteční datum a čas. <br>- **Vybrat vše**: použijte všechna zadaná kritéria. |
   | **Počet zpráv** | Počet zpráv, které se mají shromažďovat v dávce, například 10 zpráv. Limit dávky je 8 000 zpráv. |
   | **Velikost dávky** | Celková velikost v bajtech, která se má shromáždit v dávce, například 10 MB. Limit velikosti dávky je 80 MB. |
   | **Plán** | Interval a četnost mezi vydanými dávkami, například 10 minut. Minimální opakování je 60 sekund nebo 1 minuta. Zlomková minuta se efektivně zaokrouhlí na 1 minutu. Chcete-li zadat časové pásmo nebo počáteční datum a čas, otevřete seznam **Přidat nový parametr** a vyberte odpovídající vlastnosti. |
   |||

   > [!NOTE]
   >
   > Pokud kritéria pro vydání změníte, i když aktivační událost stále obsahuje dávkové i neodeslané zprávy, aktivační událost použije aktualizovaná kritéria pro zpracování neodeslaných zpráv.

   Tento příklad ukazuje všechna kritéria, ale pro vlastní testování zkuste pouze jedno kritérium:

   ![Zadat podrobnosti triggeru dávky](./media/logic-apps-batch-process-send-receive-messages/batch-receiver-criteria.png)

1. Nyní přidejte jednu nebo více akcí, které zpracovávají každou dávku.

   V tomto příkladu přidejte akci, která odešle e-mail, když se aktivuje Trigger dávky. Aktivační událost se spustí a pošle e-mail, když má Batch buď 10 zpráv, dosáhne 10 MB nebo po 10 minutách průchodu.

   1. V aktivační události dávky vyberte **Nový krok**.

   1. Do vyhledávacího pole zadejte `send email` jako filtr. Na základě poskytovatele e-mailu vyberte e-mailový konektor.

      Pokud máte například pracovní nebo školní účet, například @fabrikam.com nebo @fabrikam.onmicrosoft.com , vyberte konektor **Microsoft 365 Outlook** . Pokud máte osobní účet, jako je například @outlook.com nebo @hotmail.com , vyberte konektor **Outlook.com** . V tomto příkladu se používá konektor Microsoft 365 Outlook.

   1. Vyberte pro poskytovatele akci "Odeslat e-mail", například:

      ![Výběr akce Odeslat e-mail pro poskytovatele e-mailů](./media/logic-apps-batch-process-send-receive-messages/batch-receiver-send-email-action.png)

1. Pokud budete vyzváni, přihlaste se k e-mailovému účtu.

1. Nastavte vlastnosti pro akci, kterou jste přidali.

   * Do pole **Komu** zadejte e-mailovou adresu příjemce. Pro účely testování můžete použít svou vlastní e-mailovou adresu.

   * V poli **Předmět** , když se zobrazí seznam dynamického obsahu, vyberte pole **název oddílu** .

     ![V seznamu dynamického obsahu vyberte oddíl název oddílu.](./media/logic-apps-batch-process-send-receive-messages/send-email-action-details.png)

     Později můžete v poli odesilatele Batch zadat jedinečný klíč oddílu, který cílovou dávku rozdělí na logické podmnožiny, kde můžete odesílat zprávy. Každá sada má jedinečné číslo, které generuje aplikace logiky pro dávkovou aplikaci. Tato funkce umožňuje použít jednu dávku s více podmnožinami a definovat každou podmnožinu s názvem, který zadáte.

     > [!IMPORTANT]
     > Oddíl má omezení 5 000 zpráv nebo 80 MB. Pokud je splněna kterákoli z těchto podmínek, Logic Apps může vydávat dávku i v případě, že není splněna definovaná podmínka vydání.

   * V poli **text** , když se zobrazí seznam dynamického obsahu, vyberte pole **ID zprávy** .

     Návrhář aplikace logiky automaticky přidá **pro každou** smyčku kolem akce Odeslat e-mail, protože tato akce zpracovává výstup z předchozí akce jako kolekci, nikoli dávku.

     ![V části "tělo" vyberte "ID zprávy".](./media/logic-apps-batch-process-send-receive-messages/send-email-action-details-for-each.png)

1. Uložte aplikaci logiky. Nyní jste vytvořili přijímač Batch.

    ![Uložení aplikace logiky](./media/logic-apps-batch-process-send-receive-messages/save-batch-receiver-logic-app.png)

   > [!IMPORTANT]
   > Pokud používáte Visual Studio, před pokračováním do další části se ujistěte, že jste nejdřív [ *nasadili* aplikaci Logic Receiver Logic do Azure](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure). V opačném případě nelze při vytváření odesílatele Batch vybrat přijímače Batch.

<a name="batch-sender"></a>

## <a name="create-batch-sender"></a>Vytvořit odesílatele Batch

Nyní vytvořte jednu nebo více aplikací pro příjem z aplikace Batch, které odesílají zprávy do aplikace Logic přijímače Batch. V každém odesilateli dávky zadáte přijímače a název dávky, obsah zprávy a všechna ostatní nastavení. Volitelně můžete zadat jedinečný klíč oddílu pro rozdělení dávky na logické podsady pro shromažďování zpráv s tímto klíčem.

* Ujistěte se, že jste dříve [vytvořili a nasadili příjemce služby](#batch-receiver) Batch, takže při vytváření odesílatele služby Batch můžete jako cílovou dávku vybrat stávajícího příjemce dávky. I když přijímače Batch nepotřebují žádné informace o odesílajících dávkách, musí odesílatelé dávky zjistit, kam se mají posílat zprávy.

* Ujistěte se, že váš přijímač a odesilatel dávky sdílí stejnou oblast Azure *a* předplatné Azure. Pokud ne, nemůžete při vytváření odesílatele Batch vybrat přijímače Batch, protože nejsou navzájem viditelné.

1. Vytvořte další aplikaci logiky s tímto názvem: `BatchSender`

   1. Do vyhledávacího pole zadejte `recurrence` jako filtr. V seznamu triggery vyberte tuto aktivační událost: **opakování** .

      ![Přidat Trigger opakování](./media/logic-apps-batch-process-send-receive-messages/add-schedule-trigger-batch-sender.png)

   1. Nastavte interval a frekvenci spouštění aplikace logiky odesílatele každou minutu.

      ![Nastavení frekvence a intervalu pro aktivační událost opakování](./media/logic-apps-batch-process-send-receive-messages/recurrence-trigger-batch-sender-details.png)

1. Přidejte novou akci pro odesílání zpráv do dávky.

   1. V triggeru **opakování** vyberte **Nový krok**.

   1. Do vyhledávacího pole zadejte `batch` jako filtr a vyberte tuto akci: **zvolte pracovní postup Logic Apps pomocí triggeru služby Batch** .

      ![Vyberte "zvolit pracovní postup Logic Apps s triggerem dávky"](./media/logic-apps-batch-process-send-receive-messages/send-messages-batch-action.png)

      Zobrazí se seznam a zobrazí jenom ty aplikace logiky, které mají triggery služby Batch a existují ve stejné oblasti Azure *a* předplatném Azure jako aplikace logiky služby Batch.

   1. V seznamu aplikace logiky vyberte aplikaci logicer Receiver, kterou jste vytvořili dříve.

      ![Výběr aplikace logiky přijímače v dávce](./media/logic-apps-batch-process-send-receive-messages/batch-sender-select-batch-receiver.png)

      > [!IMPORTANT]
      > Pokud používáte aplikaci Visual Studio a nevidíte žádné příjemce dávky, zkontrolujte, že jste předtím vytvořili a nasadili příjemce služby Batch do Azure. Pokud jste to ještě neudělali, přečtěte si, [Jak nasadit aplikaci logiky přijímače z Batch do Azure](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure).

   1. V seznamu akce vyberte tuto akci: **Batch_messages-<Your- *Logic-App-Name* > **

      ![Vyberte tuto akci: Batch_messages-<vaše logika-aplikace>](./media/logic-apps-batch-process-send-receive-messages/batch-sender-select-batch.png)

1. Nastavit vlastnosti odesilatele dávky:

   | Vlastnost | Popis |
   |----------|-------------|
   | **Název dávky** | Název dávky definovaný aplikací příjemce Logic, který je `TestBatch` v tomto příkladu <p>**Důležité**: název dávky se ověří za běhu a musí odpovídat názvu určenému aplikací příjemce Logic. Změnou názvu dávky dojde k selhání dávkového odesilatele. |
   | **Obsah zprávy** | Obsah zprávy, kterou chcete odeslat |
   |||

   > [!NOTE]
   > Hodnoty vlastností **název aktivační události** a **pracovního postupu** se vyplní automaticky z vybrané aplikace logiky.

   V tomto příkladu přidejte tento výraz, který vloží aktuální datum a čas do obsahu zprávy, který odešlete do dávky:

   1. Klikněte do pole **obsah zprávy** .

   1. Když se zobrazí seznam dynamického obsahu, vyberte **výraz**.

   1. Zadejte výraz `utcnow()` a vyberte **OK**.

      ![V části obsah zprávy vyberte výraz, zadejte "UtcNow ()" a vyberte "OK".](./media/logic-apps-batch-process-send-receive-messages/batch-sender-details.png)

1. Nyní nastavte oddíl pro dávku. V `BatchReceiver` akci otevřete seznam **Přidat nový parametr** a vyberte tyto vlastnosti:

   | Vlastnost | Popis |
   |----------|-------------|
   | **Název oddílu** | Volitelný jedinečný klíč oddílu, který se má použít k rozdělení cílové dávky na logické podsady a shromáždění zpráv na základě tohoto klíče |
   | **ID zprávy** | Volitelný identifikátor zprávy, který je vygenerovaný globálně jedinečný identifikátor (GUID), pokud je prázdný |
   |||

   V tomto příkladu v poli **název oddílu** přidejte výraz, který generuje náhodné číslo mezi 1 a pěti. Pole **ID zprávy** nechte prázdné.

   1. Klikněte do pole **název oddílu** , aby se zobrazil seznam dynamického obsahu.

   1. V seznamu dynamický obsah vyberte možnost **výraz**.

   1. Zadejte výraz `rand(1,6)` a pak vyberte **OK**.

      ![Nastavení oddílu pro cílovou dávku](./media/logic-apps-batch-process-send-receive-messages/batch-sender-partition-advanced-options.png)

      Tato funkce **Rand** vygeneruje číslo mezi 1 a pěti. Takže tuto dávku vydělíte do pěti očíslovaných oddílů, které tento výraz dynamicky nastaví.

1. Uložte aplikaci logiky. Vaše aplikace logiky vašeho odesílatele teď vypadá podobně jako v tomto příkladu:

   ![Uložení aplikace logiky odesilatele](./media/logic-apps-batch-process-send-receive-messages/batch-sender-finished.png)

## <a name="test-your-logic-apps"></a>Testování aplikací logiky

Pokud chcete otestovat řešení Batch, nechte aplikace logiky běžet po dobu několika minut. Brzy začnete získávat e-maily ve skupinách po pěti, a to vše se stejným klíčem oddílu.

Vaše aplikace logiky pro odeslání služby Batch se spouští každou minutu, vygeneruje náhodné číslo mezi 1 a 5 a použije toto vygenerované číslo jako klíč oddílu pro cílovou dávku, kam se odesílají zprávy. Pokaždé, když má dávka pět položek se stejným klíčem oddílu, aplikace logiky přijímače Batch spustí a pošle poštu pro každou zprávu.

> [!IMPORTANT]
> Až budete s testováním hotovi, ujistěte se, že jste aplikaci logiky vypnuli, `BatchSender` abyste zastavili odesílání zpráv a nedocházelo k přetěžování vaší doručené pošty.

## <a name="next-steps"></a>Další kroky

* [Dávky a odesílání zpráv EDI](../logic-apps/logic-apps-scenario-edi-send-batch-messages.md)
* [Vytváření definic aplikací logiky pomocí JSON](../logic-apps/logic-apps-author-definitions.md)
* [Sestavení aplikace bez serveru v aplikaci Visual Studio pomocí Azure Logic Apps a funkcí](../logic-apps/logic-apps-serverless-get-started-vs.md)
* [Zpracování výjimek a protokolování chyb pro Logic Apps](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
