---
title: Vytváření pracovních postupů Logic Apps ve verzi Preview v Visual Studio Code
description: Sestavování a spouštění pracovních postupů pro scénáře automatizace a integrace v Visual Studio Code s rozšířením Azure Logic Apps (Preview).
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, az-logic-apps-dev
ms.topic: conceptual
ms.date: 03/08/2021
ms.openlocfilehash: f7f8082cc9120345336610d5cb49741140d3b606
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102557008"
---
# <a name="create-stateful-and-stateless-workflows-in-visual-studio-code-with-the-azure-logic-apps-preview-extension"></a>Vytváření stavových a bezstavových pracovních postupů v Visual Studio Code s rozšířením Azure Logic Apps (Preview)

> [!IMPORTANT]
> Tato funkce je ve verzi Public Preview, poskytuje se bez smlouvy o úrovni služeb a nedoporučuje pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

V [Azure Logic Apps Preview](logic-apps-overview-preview.md)můžete vytvářet řešení pro automatizaci a integraci napříč aplikacemi, daty, Cloud Services a systémy vytvořením a spuštěním aplikací logiky, které zahrnují [ *stavové* a *bezstavové* pracovní postupy](logic-apps-overview-preview.md#stateful-stateless) v Visual Studio Code pomocí rozšíření Azure Logic Apps (Preview). Pomocí tohoto nového typu aplikace logiky můžete vytvořit několik pracovních postupů využívajících přepracované prostředí runtime Azure Logic Apps Preview, které poskytuje přenositelnost, lepší výkon a flexibilitu pro nasazení a spouštění v různých hostujících prostředích, nejen Azure, ale také kontejnery Docker. Další informace o novém typu aplikace logiky najdete v tématu [přehled Azure Logic Apps Preview](logic-apps-overview-preview.md).

![Snímek obrazovky, který ukazuje Visual Studio Code, projekt aplikace logiky a pracovní postup.](./media/create-stateful-stateless-workflows-visual-studio-code/visual-studio-code-logic-apps-overview.png)

V Visual Studio Code můžete začít vytvořením projektu, kde *můžete vytvářet a* spouštět pracovní postupy vaší aplikace logiky ve vývojovém prostředí pomocí rozšíření Azure Logic Apps (Preview). I když můžete začít tím, že [vytvoříte nový prostředek **Aplikace logiky (Preview)** v Azure Portal](create-stateful-stateless-workflows-azure-portal.md), oba přístupy poskytují možnost nasadit a spustit aplikaci logiky ve stejných druzích hostujících prostředí.

Mezitím můžete stále vytvořit původní typ aplikace logiky. I když se vývojové prostředí v Visual Studio Code liší od původní a nové typy aplikací logiky, může vaše předplatné Azure zahrnovat oba typy. Můžete zobrazit a přistupovat ke všem nasazeným Logic Apps v předplatném Azure, ale aplikace se uspořádají do jejich vlastních kategorií a oddílů.

Tento článek ukazuje, jak vytvořit aplikaci logiky a pracovní postup v Visual Studio Code pomocí rozšíření Azure Logic Apps (Preview) a provádění těchto úloh vysoké úrovně:

* Vytvořte projekt pro vaši aplikaci logiky a pracovní postup.

* Přidejte Trigger a akci.

* Místní spuštění, testování, ladění a kontrola historie spuštění.

* Vyhledá podrobnosti o názvu domény pro přístup přes bránu firewall.

* Nasazení do Azure, které zahrnuje volitelně povolení Application Insights.

* Spravujte nasazenou aplikaci logiky v Visual Studio Code a Azure Portal.

* Povolí historii spuštění bezstavových pracovních postupů.

* Po nasazení povolte nebo otevřete Application Insights.

* Nasaďte do kontejneru Docker, který můžete spustit kdekoli.

> [!NOTE]
> Informace o aktuálních známých problémech najdete na [stránce Logic Apps Public Preview známé problémy v GitHubu](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md).

## <a name="prerequisites"></a>Požadavky

### <a name="access-and-connectivity"></a>Přístup a připojení

* Přístup k Internetu, abyste si mohli stáhnout požadavky, připojit se z Visual Studio Code k vašemu účtu Azure a publikovat z Visual Studio Code do Azure, kontejneru Docker nebo jiného prostředí.

* Účet a předplatné Azure. Pokud předplatné nemáte, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Pokud chcete v tomto článku sestavit stejnou ukázkovou aplikaci logiky, potřebujete e-mailový účet Office 365 Outlook, který pro přihlášení používá pracovní nebo školní účet Microsoft.

  Pokud se rozhodnete použít jiný [e-mailový konektor, který podporuje Azure Logic Apps](/connectors/), jako je například Outlook.com nebo [Gmail](../connectors/connectors-google-data-security-privacy-policy.md), můžete postupovat dál a obecné kroky jsou stejné, ale vaše uživatelské rozhraní a možnosti se můžou v některých případech lišit. Pokud například použijete konektor Outlook.com, použijte místo toho osobní účet Microsoft k přihlášení.

<a name="storage-requirements"></a>

### <a name="storage-requirements"></a>Požadavky na úložiště

#### <a name="windows"></a>Windows

Pro místní sestavení a spuštění projektu aplikace logiky v Visual Studio Code při použití systému Windows, postupujte podle těchto kroků a nastavte emulátor Azure Storage:

1. Stáhněte a nainstalujte [emulátor Azure Storage 5,10](https://go.microsoft.com/fwlink/p/?linkid=717179).

1. Pokud ho ještě nemáte, musíte mít místní instalaci SQL DB, třeba bezplatnou [edici SQL Server 2019 Express](https://go.microsoft.com/fwlink/p/?linkid=866658), aby bylo možné spustit emulátor.

   Další informace najdete v tématu [použití emulátoru Azure Storage pro vývoj a testování](../storage/common/storage-use-emulator.md).

1. Před spuštěním projektu se ujistěte, že jste spustili emulátor.

   ![Snímek obrazovky, který zobrazuje emulátor Azure Storage spuštěný.](./media/create-stateful-stateless-workflows-visual-studio-code/start-storage-emulator.png)

#### <a name="macos-and-linux"></a>macOS a Linux

Chcete-li při použití macOS nebo Linux místně sestavit a spustit Visual Studio Code projekt aplikace logiky, vytvořte a nastavte účet Azure Storage pomocí následujících kroků.

> [!NOTE]
> V současné době Návrhář v Visual Studio Code nefunguje v operačním systému Linux, ale stále můžete spouštět sestavování, spouštění a nasazování aplikací logiky, které používají modul runtime Preview Logic Apps na virtuální počítače se systémem Linux. Prozatím můžete vytvářet aplikace logiky v Visual Studio Code ve Windows nebo macOS a pak je nasadit do virtuálního počítače se systémem Linux.

1. Přihlaste se k [Azure Portal](https://portal.azure.com)a [vytvořte účet Azure Storage](../storage/common/storage-account-create.md?tabs=azure-portal), který je [předpokladem pro Azure Functions](../azure-functions/storage-considerations.md).

1. V nabídce účet úložiště v části **Nastavení** vyberte **přístupové klíče**.

1. V podokně **přístupové klíče** vyhledejte a zkopírujte připojovací řetězec účtu úložiště, který vypadá podobně jako v tomto příkladu:

   `DefaultEndpointsProtocol=https;AccountName=fabrikamstorageacct;AccountKey=<access-key>;EndpointSuffix=core.windows.net`

   ![Snímek obrazovky zobrazující Azure Portal s přístupovými klíči účtu úložiště a zkopírovaným připojovacím řetězcem](./media/create-stateful-stateless-workflows-visual-studio-code/find-storage-account-connection-string.png)

   Další informace najdete v přehledu [správy klíčů účtu úložiště](../storage/common/storage-account-keys-manage.md?tabs=azure-portal#view-account-access-keys).

1. Uložte připojovací řetězec někam do trezoru. Po vytvoření projektu aplikace logiky v Visual Studio Code je nutné přidat řetězec do **local.settings.jsv** souboru ve složce na kořenové úrovni vašeho projektu.

   > [!IMPORTANT]
   > Pokud plánujete nasazení do kontejneru Docker, budete také muset přidat tento připojovací řetězec do souboru Docker, který používáte pro nasazení.

### <a name="tools"></a>nástroje

* [Visual Studio Code 1.30.1 (leden 2019) nebo vyšší](https://code.visualstudio.com/), což je zdarma. Také si stáhněte a nainstalujte tyto nástroje pro Visual Studio Code, pokud je ještě nemáte:

  * [Rozšíření účtu Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account), které poskytuje jedno společné prostředí pro přihlášení a předplatné Azure pro všechna ostatní rozšíření azure v Visual Studio Code.

  * [Rozšíření C# pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp), které umožňuje, aby funkce F5 spouštěla vaši aplikaci logiky.

  * [Azure Functions Core Tools 3.0.3245 nebo novější](https://github.com/Azure/azure-functions-core-tools/releases/tag/3.0.3245) pomocí verze Instalační služby Microsoft (MSI), která je `func-cli-3.0.3245-x*.msi` .

    Tyto nástroje zahrnují verzi stejného modulu runtime, který slouží jako modul runtime Azure Functions, který rozšíření Preview používá v Visual Studio Code.

    > [!IMPORTANT]
    > Pokud máte instalaci, která je starší než tyto verze, nejprve ji odinstalujte nebo zajistěte, aby proměnná prostředí PATH odkazovala na verzi, kterou stáhnete a nainstalujete.

  * [Rozšíření Azure Logic Apps (Preview) pro Visual Studio Code](https://go.microsoft.com/fwlink/p/?linkid=2143167). Toto rozšíření poskytuje možnost vytvářet aplikace logiky, kde můžete vytvářet stavové a bezstavové pracovní postupy, které jsou místně spouštěny v Visual Studio Code a následně tyto aplikace logiky nasazovat přímo do Azure nebo do kontejnerů Docker.

    V současné době můžete mít v Visual Studio Code nainstalovanou původní rozšíření Azure Logic Apps i rozšíření Public Preview. I když se vývojové prostředí liší v různých způsobech mezi rozšířeními, může vaše předplatné Azure zahrnovat i typy aplikací logiky, které vytvoříte s rozšířeními. Visual Studio Code zobrazuje všechny nasazené Logic Apps v rámci vašeho předplatného Azure, ale uspořádá je do různých oddílů podle názvů rozšíření, **Logic Apps** a **Azure Logic Apps (Preview)**.

    > [!IMPORTANT]
    > Pokud jste vytvořili projekty aplikace logiky s dřívějším rozšířením privátního náhledu, tyto projekty nebudou fungovat s rozšířením Public Preview. Po odinstalaci rozšíření Private Preview ale můžete tyto projekty migrovat, odstranit přidružené soubory a nainstalovat rozšíření verze Public Preview. Pak vytvoříte nový projekt v Visual Studio Code a zkopírujete dříve vytvořený soubor **. definice pracovního postupu** aplikace logiky do nového projektu. Další informace najdete v tématu [migrace z rozšíření Private Preview](#migrate-private-preview).
    > 
    > Pokud jste vytvořili projekty aplikace logiky s dřívějším rozšířením verze Public Preview, můžete tyto projekty dál používat bez jakýchkoli kroků migrace.

    **Chcete-li nainstalovat rozšíření **Azure Logic Apps (Preview)** , postupujte podle následujících kroků:**

    1. V Visual Studio Code na levém panelu nástrojů vyberte **rozšíření**.

    1. Do vyhledávacího pole rozšíření zadejte `azure logic apps preview` . V seznamu výsledků vyberte možnost **Azure Logic Apps (Preview)** **>** **instalovat**.

       Po dokončení instalace se rozšíření náhledu zobrazí v seznamu **rozšíření: nainstalováno** .

       ![Snímek obrazovky, který zobrazuje seznam nainstalovaných rozšíření Visual Studio Code s příponou "Azure Logic Apps (Preview)" byl podtržen.](./media/create-stateful-stateless-workflows-visual-studio-code/azure-logic-apps-extension-installed.png)

       > [!TIP]
       > Pokud se rozšíření nezobrazí v seznamu nainstalované, zkuste restartovat Visual Studio Code.

* Chcete-li použít operaci [vloženého kódu](../logic-apps/logic-apps-add-run-inline-code.md) , která spouští JavaScript, nainstalujte [Node.js verze 10. x. x, 11. x. x nebo 12. x. x](https://nodejs.org/en/download/releases/).

  > [!TIP] 
  > Pro Windows Stáhněte verzi MSI. Pokud místo toho použijete verzi ZIP, je nutné ručně nastavit Node.js k dispozici pomocí proměnné prostředí PATH pro váš operační systém.

* Chcete-li místně spustit triggery a akce založené na webhookech, jako je například [vestavěný Trigger Webhooku protokolu HTTP](../connectors/connectors-native-webhook.md), v Visual Studio Code musíte [nastavit přesměrování pro adresu URL zpětného volání](#webhook-setup).

* Chcete-li otestovat ukázkovou aplikaci logiky, kterou vytvoříte v tomto článku, budete potřebovat nástroj, který může odeslat volání triggeru žádosti, což je první krok v ukázkové aplikaci logiky. Pokud tento nástroj nemáte, můžete si ho stáhnout, nainstalovat [a používat.](https://www.postman.com/downloads/)

* Pokud vytvoříte aplikaci logiky a nasadíte s nastaveními, která podporují použití [Application Insights](../azure-monitor/app/app-insights-overview.md), můžete volitelně povolit diagnostické protokolování a trasování pro vaši aplikaci logiky. Můžete to udělat buď při nasazení aplikace logiky z Visual Studio Code nebo po nasazení. Musíte mít instanci Application Insights, ale tento prostředek můžete vytvořit buď [předem](../azure-monitor/app/create-workspace-resource.md), při nasazení aplikace logiky nebo po nasazení.

<a name="migrate-private-preview"></a>

## <a name="migrate-from-private-preview-extension"></a>Migrace z rozšíření Private Preview

Všechny projekty aplikace logiky, které jste vytvořili pomocí rozšíření **Azure Logic Apps (Private Preview)** , nebudou fungovat s rozšířením Public Preview. Tyto projekty však můžete migrovat do nových projektů pomocí následujících kroků:

1. Odinstalujte rozšíření Private Preview.

1. Odstraňte všechny přidružené sady rozšíření a složky balíčku NuGet v těchto umístěních:

   * Složka **Microsoft. Azure. Functions. ExtensionBundle. Workflows** , která obsahuje předchozí sady rozšíření a která se nachází podél cesty:

     * `C:\Users\{userName}\AppData\Local\Temp\Functions\ExtensionBundles`

     * `C:\Users\{userName}\.azure-functions-core-tools\Functions\ExtensionBundles`

   * Složka **Microsoft. Azure. Workflows. WebJobs. Extension** , což je mezipaměť [NuGet](/nuget/what-is-nuget) pro rozšíření Private Preview a je umístěná na této cestě:

     `C:\Users\{userName}\.nuget\packages`

1. Nainstalujte rozšíření **Azure Logic Apps (Preview)** .

1. Vytvoří nový projekt v Visual Studio Code.

1. Zkopírujte dříve vytvořený soubor **. definice pracovního postupu** aplikace logiky do nového projektu.

<a name="set-up"></a>

## <a name="set-up-visual-studio-code"></a>Nastavit nástroj Visual Studio Code

1. Abyste se ujistili, že jsou všechna rozšíření správně nainstalovaná, znovu načtěte nebo znovu spusťte Visual Studio Code.

1. Potvrďte, že Visual Studio Code automaticky vyhledá a nainstaluje aktualizace rozšíření, takže rozšíření Preview získá nejnovější aktualizace. V opačném případě je nutné ručně odinstalovat zastaralou verzi a nainstalovat nejnovější verzi.

   1. V nabídce **soubor** přejděte na  **>** **Nastavení** předvolby.

   1. Na kartě **uživatel** přejdete na **funkce** **>** **rozšíření**.

   1. Potvrďte, že je vybraná možnost **automaticky zkontrolovat aktualizace** a **Automatická aktualizace** .

Ve výchozím nastavení jsou také povolena a nastavena následující nastavení pro rozšíření Logic Apps Preview:

* **Azure Logic Apps v2: modul runtime projektu**, který je nastavený na verzi **~ 3**

  > [!NOTE]
  > Tato verze je vyžadována pro použití [akcí vloženého kódu](../logic-apps/logic-apps-add-run-inline-code.md).

* **Azure Logic Apps v2: experimentální Správce zobrazení**, který umožňuje nejnovější verzi návrháře v Visual Studio Code. Pokud v Návrháři dojde k problémům, jako je například přetahování položek, vypněte toto nastavení.

Chcete-li vyhledat a potvrdit tato nastavení, postupujte podle následujících kroků:

1. V nabídce **soubor** přejděte na  **>** **Nastavení** předvolby.

1. Na kartě **uživatel** přejdete do části **>** **rozšíření** **>** **Azure Logic Apps (Preview)**.

   Například můžete najít nastavení **modulu Runtime Azure Logic Apps v2: projektu** nebo použít vyhledávací pole k vyhledání dalších nastavení:

   ![Snímek obrazovky, který zobrazuje Visual Studio Code nastavení pro rozšíření "Azure Logic Apps (Preview)".](./media/create-stateful-stateless-workflows-visual-studio-code/azure-logic-apps-preview-settings.png)

<a name="connect-azure-account"></a>

## <a name="connect-to-your-azure-account"></a>Připojení k účtu Azure

1. Na řádku Visual Studio Code aktivity vyberte ikonu Azure.

   ![Snímek obrazovky zobrazující Visual Studio Code řádek aktivity a vybraná ikona Azure](./media/create-stateful-stateless-workflows-visual-studio-code/visual-studio-code-azure-icon.png)

1. V podokně Azure v části **Azure: Logic Apps (Preview)** vyberte **Přihlásit se k Azure**. Když se zobrazí stránka ověřování Visual Studio Code, přihlaste se pomocí svého účtu Azure.

   ![Snímek obrazovky, který zobrazuje podokno Azure a vybraný odkaz pro přihlášení k Azure](./media/create-stateful-stateless-workflows-visual-studio-code/sign-in-azure-subscription.png)

   Po přihlášení se v podokně Azure zobrazí předplatná v účtu Azure. Pokud máte také veřejně vydanou příponu, můžete najít všechny aplikace logiky, které jste vytvořili s tímto rozšířením v části **Logic Apps** , nikoli v části **Logic Apps (Preview)** .
   
   Pokud se očekávané odběry nezobrazí nebo chcete, aby podokno zobrazovalo pouze konkrétní odběry, postupujte podle těchto kroků:

   1. V seznamu předplatná přesuňte ukazatel vedle prvního předplatného, dokud se nezobrazí tlačítko **Vybrat odběry** (ikona filtru). Vyberte ikonu filtru.

      ![Snímek obrazovky, který zobrazuje podokno Azure a vybraný ikonu filtru.](./media/create-stateful-stateless-workflows-visual-studio-code/filter-subscription-list.png)

      Nebo na stavovém řádku Visual Studio Code vyberte svůj účet Azure. 

   1. Pokud se zobrazí jiný seznam předplatných, vyberte požadované odběry a pak se ujistěte, že jste vybrali **OK**.

<a name="create-project"></a>

## <a name="create-a-local-project"></a>Vytvořit místní projekt

Než budete moct vytvořit aplikaci logiky, vytvořte místní projekt, abyste mohli spravovat, spouštět a nasazovat aplikace logiky z Visual Studio Code. Podkladový projekt je podobný projektu Azure Functions, označovaný také jako projekt Function App. Tyto typy projektů jsou však oddělené od sebe navzájem, takže aplikace logiky a aplikace Function App nemůžou existovat ve stejném projektu.

1. V počítači vytvořte *prázdnou* místní složku, kterou chcete použít pro projekt, který později vytvoříte v Visual Studio Code.

1. V Visual Studio Code zavřete všechny otevřené složky.

1. V podokně Azure vedle **Azure: Logic Apps (Preview)** vyberte **vytvořit nový projekt** (ikona, která zobrazuje složku a blesk).

   ![Snímek obrazovky, který zobrazuje panel nástrojů podokna Azure se zvoleným možnost vytvořit nový projekt](./media/create-stateful-stateless-workflows-visual-studio-code/create-new-project-folder.png)

1. Pokud vás firewall v programu Windows Defender vyzve k udělení přístupu k síti pro `Code.exe` , což je Visual Studio Code a pro `func.exe` , což je Azure Functions Core Tools, vyberte **privátní sítě, například moje domů nebo pracovní síť** **>** **Povolit přístup**.

1. Přejděte do umístění, kde jste vytvořili složku projektu, vyberte tuto složku a pokračujte.

   ![Snímek obrazovky, který zobrazuje dialogové okno vybrat složku s nově vytvořenou složkou projektu a vybraným tlačítkem vybrat.](./media/create-stateful-stateless-workflows-visual-studio-code/select-project-folder.png)

1. V seznamu šablon, který se zobrazí, vyberte **stavový pracovní postup** nebo **pracovní postup bez stavu**. V tomto příkladu se vybere **stavový pracovní postup**.

   ![Snímek obrazovky, který zobrazuje seznam šablon pracovního postupu s vybraným stavovým pracovním postupem.](./media/create-stateful-stateless-workflows-visual-studio-code/select-stateful-stateless-workflow.png)

1. Zadejte název pracovního postupu a stiskněte klávesu ENTER. Tento příklad používá `Fabrikam-Stateful-Workflow` jako název.

   ![Snímek obrazovky, který zobrazuje pole pro vytvoření nového stavového pracovního postupu (3/4) a "Fabrikam-Stateful-Workflow" jako název pracovního postupu.](./media/create-stateful-stateless-workflows-visual-studio-code/name-your-workflow.png)

   Visual Studio Code dokončí vytváření projektu a otevře **workflow.jsv** souboru pro pracovní postup v editoru kódu.

   > [!NOTE]
   > Pokud budete vyzváni k výběru způsobu otevření projektu, vyberte **otevřít v aktuálním okně** , pokud chcete projekt otevřít v aktuálním Visual Studio Codem okně. Pro otevření nové instance pro Visual Studio Code vyberte **otevřít v novém okně**.

1. Na panelu nástrojů sady Visual Studio otevřete podokno Průzkumník, pokud ještě není otevřené.

   V podokně Průzkumník se zobrazí projekt, který nyní obsahuje automaticky generované soubory projektu. Projekt má například složku, která zobrazuje název vašeho pracovního postupu. V rámci této složky **workflow.jsv** souboru obsahuje základní definici JSON vašeho pracovního postupu.

   ![Snímek obrazovky, který zobrazuje podokno Průzkumníka se složkou projektu, složkou pracovního postupu a souborem "workflow.jsna".](./media/create-stateful-stateless-workflows-visual-studio-code/local-project-created.png)

1. Pokud používáte macOS nebo Linux, nastavte přístup k účtu úložiště pomocí následujících kroků, které jsou požadovány pro místní spuštění projektu:

   1. V kořenové složce projektu otevřete **local.settings.jsv** souboru.

      ![Snímek obrazovky, který zobrazuje podokno Průzkumníka a soubor local.settings.json v projektu.](./media/create-stateful-stateless-workflows-visual-studio-code/local-settings-json-files.png)

   1. Nahraďte `AzureWebJobsStorage` hodnotu vlastnosti připojovacím řetězcem účtu úložiště, který jste předtím uložili, například:

      Před:

      ```json
      {
         "IsEncrypted": false,
         "Values": {
            "AzureWebJobsStorage": "UseDevelopmentStorage=true",
            "FUNCTIONS_WORKER_RUNTIME": "dotnet"
          }
      }
      ```

      Po:

      ```json
      {
         "IsEncrypted": false,
         "Values": {
            "AzureWebJobsStorage": "DefaultEndpointsProtocol=https;AccountName=fabrikamstorageacct;AccountKey=<access-key>;EndpointSuffix=core.windows.net",
           "FUNCTIONS_WORKER_RUNTIME": "dotnet"
         }
      }
      ```

   1. Až budete hotovi, nezapomeňte změny uložit.

<a name="enable-built-in-connector-authoring"></a>

## <a name="enable-built-in-connector-authoring"></a>Povolit vytváření vestavěných konektorů

Můžete vytvořit vlastní integrované konektory pro libovolnou službu, kterou potřebujete, pomocí [rozhraní pro rozšiřitelnost verze Preview](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-built-in-connector/ba-p/1921272). Podobně jako u integrovaných konektorů, jako jsou Azure Service Bus a SQL Server, tyto konektory poskytují vyšší propustnost, nízkou latenci, místní připojení a spouštění nativně ve stejném procesu jako modul runtime verze Preview.

Funkce pro vytváření obsahu je aktuálně dostupná jenom v Visual Studio Code, ale ve výchozím nastavení není povolená. Chcete-li vytvořit tyto konektory, je nutné nejprve převést projekt ze sady rozšíření (Node.js) na základě balíčku NuGet (.NET).

> [!IMPORTANT]
> Tato akce je jednosměrná operace, kterou nelze vrátit zpět.

1. V podokně Průzkumník v kořenovém adresáři vašeho projektu přesuňte ukazatel myši do libovolné prázdné oblasti pod všemi ostatními soubory a složkami, otevřete místní nabídku a vyberte možnost **převést na projekt aplikace logiky založené na NuGet**.

   ![Snímek obrazovky zobrazující podokno Průzkumníka s místní nabídkou projektu otevřený z prázdné oblasti okna projektu](./media/create-stateful-stateless-workflows-visual-studio-code/convert-logic-app-project.png)

1. Po zobrazení výzvy potvrďte převod projektu.

1. Chcete-li pokračovat, přečtěte si postup v článku a postupujte podle pokynů v tématu [Azure Logic Apps spuštění odkudkoli integrovaných rozšíření konektoru](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-built-in-connector/ba-p/1921272).

<a name="open-workflow-definition-designer"></a>

## <a name="open-the-workflow-definition-file-in-the-designer"></a>Otevřete soubor definice pracovního postupu v návrháři.

1. Spuštěním tohoto příkazu ověřte verze, které jsou nainstalované v počítači:

   `..\Users\{yourUserName}\dotnet --list-sdks`

   Pokud máte .NET Core SDK 5. x, může vám tato verze zabránit v otevření základní definice pracovního postupu aplikace logiky v návrháři. Místo odinstalace této verze v kořenové složce projektu vytvořte **global.jsv** souboru, který odkazuje na verzi .NET Core Runtime 3. x, kterou máte později než 3.1.201, například:

   ```json
   {
      "sdk": {
         "version": "3.1.8",
         "rollForward": "disable"
      }
   }
   ```

   > [!IMPORTANT]
   > Ujistěte se, že explicitně přidáte **global.js** do souboru v kořenové složce projektu v rámci Visual Studio Code. V opačném případě se Návrhář neotevře.

1. Rozbalte složku projektu pro váš pracovní postup. Otevřete místní nabídku souboru **workflow.js** a v **Návrháři vyberte otevřít**.

   ![Snímek obrazovky, který zobrazuje podokno Průzkumníka a okno zástupce pro workflow.jsv souboru s vybranou možnost otevřít v Návrháři](./media/create-stateful-stateless-workflows-visual-studio-code/open-definition-file-in-designer.png)

1. V seznamu **Povolit konektory v Azure** vyberte **použít konektory z Azure**, které se vztahují na všechny spravované konektory, které jsou k dispozici a nasazené v Azure, nejen konektory pro služby Azure.

   ![Snímek obrazovky zobrazující podokno Průzkumník se seznamem povolit konektory v Azure a vybrat možnost použít konektory z Azure](./media/create-stateful-stateless-workflows-visual-studio-code/use-connectors-from-azure.png)

   > [!NOTE]
   > Bezstavové pracovní postupy aktuálně podporují jenom *Akce* pro [spravované konektory](../connectors/apis-list.md#managed-api-connectors), které jsou nasazené v Azure, a ne triggery. I když máte možnost povolit konektory v Azure pro bezstavový pracovní postup, Návrhář nezobrazí žádné triggery spravovaného konektoru, které byste mohli vybrat.

1. V seznamu **Vybrat odběr** vyberte předplatné Azure, které chcete použít pro projekt aplikace logiky.

   ![Snímek obrazovky, který zobrazuje podokno Průzkumník s vybraným polem vybrat předplatné a vaším předplatným.](./media/create-stateful-stateless-workflows-visual-studio-code/select-azure-subscription.png)

1. V seznamu skupiny prostředků vyberte **vytvořit novou skupinu prostředků**.

   ![Snímek obrazovky, který zobrazuje podokno Průzkumník se seznamem skupin prostředků a je vybrána možnost vytvořit novou skupinu prostředků.](./media/create-stateful-stateless-workflows-visual-studio-code/create-select-resource-group.png)

1. Zadejte název skupiny prostředků a stiskněte klávesu ENTER. Tento příklad používá `Fabrikam-Workflows-RG`.

   ![Snímek obrazovky, který zobrazuje podokno Průzkumníka a název skupiny prostředků.](./media/create-stateful-stateless-workflows-visual-studio-code/enter-name-for-resource-group.png)

1. V seznamu umístění vyhledejte a vyberte oblast Azure, která se má použít při vytváření prostředků a skupiny prostředků. Tento příklad používá **středozápadní USA**.

   ![Snímek obrazovky, který zobrazuje podokno Průzkumník se seznamem umístění a vybraným "Středozápadní USA".](./media/create-stateful-stateless-workflows-visual-studio-code/select-azure-region.png)

   Po provedení tohoto kroku Visual Studio Code otevře návrháře pracovních postupů.

   > [!NOTE]
   > Když Visual Studio Code spustí rozhraní API pro dobu návrhu pracovního postupu, může se zobrazit zpráva, že spuštění může trvat několik sekund. Tuto zprávu můžete ignorovat nebo vyberte **OK**.
   >
   > Pokud se Návrhář neotevře, přečtěte si část Poradce při potížích, aby se nepodařilo [otevřít návrháře](#designer-fails-to-open).

   Po zobrazení návrháře se zobrazí výzva **Zvolit operaci** v návrháři a je vybrána ve výchozím nastavení, což se zobrazí v podokně **přidat akci** .

   ![Snímek obrazovky, který zobrazuje návrháře pracovního postupu.](./media/create-stateful-stateless-workflows-visual-studio-code/workflow-app-designer.png)

1. Potom do pracovního postupu [přidejte Trigger a akce](#add-trigger-actions) .

<a name="add-trigger-actions"></a>

## <a name="add-a-trigger-and-actions"></a>Přidat aktivační událost a akce

Po otevření návrháře se v Návrháři zobrazí výzva **Zvolit operaci** a ve výchozím nastavení je vybraná. Nyní můžete začít vytvářet pracovní postup přidáním triggeru a akcí.

Pracovní postup v tomto příkladu používá tuto aktivační událost a tyto akce:

* Vestavěná [aktivační událost žádosti](../connectors/connectors-native-reqres.md), **když se přijme požadavek HTTP**, který přijímá příchozí volání nebo požadavky a vytvoří koncový bod, který může volat jiné služby nebo Logic Apps.

* **Odeslání e-mailu** na [akci Office 365 Outlooku](../connectors/connectors-create-api-office365-outlook.md)

* Integrovaná [Akce odpovědi](../connectors/connectors-native-reqres.md), kterou použijete k odeslání odpovědi a vrácení dat zpět volajícímu.

### <a name="add-the-request-trigger"></a>Přidat aktivační událost žádosti

1. Vedle návrháře v podokně **Přidat aktivační událost** zaškrtněte v poli **zvolit hledání operace** možnost **předdefinované** , abyste mohli vybrat Trigger, který se spustí nativně.

1. Do vyhledávacího pole **Zvolte operaci** zadejte `when a http request` a vyberte vestavěný aktivační událost žádosti, která se pojmenuje **při přijetí požadavku HTTP**.

   ![Snímek obrazovky, který zobrazuje návrháře pracovních postupů a * * přidat aktivační událost * * s názvem při přijetí požadavku HTTP](./media/create-stateful-stateless-workflows-visual-studio-code/add-request-trigger.png)

   Když se aktivační událost objeví v návrháři, otevře se podokno podrobností triggeru, ve kterém se zobrazí vlastnosti triggeru, nastavení a další akce.

   ![Snímek obrazovky, který zobrazuje návrháře pracovních postupů se zaškrtnutým políčko když se přijme požadavek HTTP, a otevře se podokno Podrobnosti o aktivační události.](./media/create-stateful-stateless-workflows-visual-studio-code/request-trigger-added-to-designer.png)

   > [!TIP]
   > Pokud se podokno podrobností nezobrazí, ujistěte se, že je v Návrháři vybraný Trigger.

1. Pokud potřebujete odstranit položku z návrháře, [postupujte podle těchto kroků a odstraňte položky z návrháře](#delete-from-designer).

### <a name="add-the-office-365-outlook-action"></a>Přidat akci sady Office 365 Outlook

1. V návrháři v rámci triggeru, který jste přidali, vyberte **Nový krok**.

   V návrháři se zobrazí výzva **Zvolit operaci** a otevře se podokno **přidat akci** , ve kterém můžete vybrat další akci.

1. V podokně **přidat akci** vyberte v poli pro hledání **operace** možnost **Azure** , abyste mohli najít a vybrat akci pro spravovaný konektor, který je nasazený v Azure.

   Tento příklad vybere a použije akci Office 365 Outlook, **pošle e-mail (v2)**.

   ![Snímek obrazovky, který zobrazuje návrháře pracovních postupů a * * přidat akci * * podokno s vybranou akcí Office 365 Outlooku odeslat e-mail](./media/create-stateful-stateless-workflows-visual-studio-code/add-send-email-action.png)

1. V podokně podrobností akce vyberte **Přihlásit** , abyste mohli vytvořit připojení k e-mailovému účtu.

   ![Snímek obrazovky, který zobrazuje návrháře pracovních postupů a * * odeslat e-mail (v2) * * podokno s vybraným možnost přihlásit](./media/create-stateful-stateless-workflows-visual-studio-code/send-email-action-sign-in.png)

1. Když Visual Studio Code zobrazí výzvu k zadání souhlasu pro přístup k e-mailovému účtu, vyberte **otevřít**.

   ![Snímek obrazovky zobrazující Visual Studio Code výzvy k povolení přístupu](./media/create-stateful-stateless-workflows-visual-studio-code/visual-studio-code-open-external-website.png)

   > [!TIP]
   > Pokud chcete zabránit budoucím výzvám, vyberte **Konfigurovat důvěryhodné domény** , abyste mohli přidat ověřovací stránku jako důvěryhodnou doménu.

1. Postupujte podle dalších výzev, abyste se přihlásili, povolili přístup a mohli vracet Visual Studio Code.

   > [!NOTE]
   > Pokud se před dokončením výzev příliš neprojde, vyprší časový limit procesu ověřování a dojde k chybě. V takovém případě se vraťte do návrháře a znovu se přihlaste, aby se vytvořilo připojení.

1. Když se na rozšíření Azure Logic Apps (Preview) zobrazí výzva k zadání souhlasu s přístupem k e-mailovému účtu, vyberte **otevřít**. Pomocí další výzvy povolte přístup.

   ![Snímek obrazovky, který zobrazuje výzvu k rozšíření náhledu pro povolení přístupu](./media/create-stateful-stateless-workflows-visual-studio-code/allow-preview-extension-open-uri.png)

   > [!TIP]
   > Chcete-li zabránit budoucím výzvám, vyberte **pro toto rozšíření nedotazování znovu**.

   Po Visual Studio Code vytvoří připojení některé konektory zobrazí zprávu, která `The connection will be valid for {n} days only` . Tento časový limit platí jenom pro dobu trvání při vytváření aplikace logiky v Visual Studio Code. Po nasazení už toto omezení neplatí, protože aplikace logiky se může ověřit za běhu pomocí své automaticky povolené [spravované identity přiřazené systémem](../logic-apps/create-managed-service-identity.md). Tato spravovaná identita se liší od přihlašovacích údajů k ověřování nebo připojovacího řetězce, který používáte při vytváření připojení. Pokud tuto spravovanou identitu přiřazenou systémem zakážete, nebudou připojení fungovat za běhu.

1. Pokud se v Návrháři nezobrazí vybraná akce **Odeslat e-mail** , vyberte tuto akci.

1. V podokně podrobností akce zadejte na kartě **parametry** požadované informace pro akci, například:

   ![Snímek obrazovky, který zobrazuje návrháře pracovních postupů s podrobnostmi o akci odeslání e-mailu pro Office 365 Outlook](./media/create-stateful-stateless-workflows-visual-studio-code/send-email-action-details.png)

   | Vlastnost | Požaduje se | Hodnota | Popis |
   |----------|----------|-------|-------------|
   | **Do** | Yes | <*vaše e-mailová adresa*> | Příjemce e-mailu, který může být vaše e-mailová adresa pro testovací účely. V tomto příkladu se používá fiktivní e-mail, `sophiaowen@fabrikam.com` . |
   | **Předmět** | Yes | `An email from your example workflow` | Předmět e-mailu |
   | **Text** | Yes | `Hello from your example workflow!` | Obsah těla e-mailu |
   ||||

   > [!NOTE]
   > Chcete-li provést jakékoli změny v podokně podrobností na stránce **Nastavení**, **statický výsledek** nebo **Spustit po** kartě, ujistěte se, že jste vybrali možnost **Hotovo** , aby byly tyto změny potvrzeny před přepnutím tabulátorů nebo změnou fokusu na návrháře. Jinak Visual Studio Code změny nezůstanou. Další informace najdete na [stránce Logic Apps Public Preview známé problémy v GitHubu](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md).

1. V návrháři vyberte Save ( **Uložit**).

> [!IMPORTANT]
> Chcete-li místně spustit pracovní postup, který používá Trigger nebo akce založené na Webhooku, jako je například [vestavěný aktivační událost nebo akce](../connectors/connectors-native-webhook.md)Webhooku protokolu HTTP, je nutné povolit tuto funkci [nastavením předávání pro adresu URL zpětného volání Webhooku](#webhook-setup).

<a name="webhook-setup"></a>

## <a name="enable-locally-running-webhooks"></a>Povolit místně běžící Webhooky

Když použijete Trigger nebo akci založenou na Webhooku, jako je **Webhook http**, s aplikací logiky běžící v Azure, modul runtime Logic Apps se přihlásí k odběru koncovému bodu služby vygenerováním a registrací adresy URL zpětného volání s tímto koncovým bodem. Aktivační událost nebo akce pak počká, až koncový bod služby zavolá adresu URL. Pokud však pracujete v Visual Studio Code, vygenerovaná adresa URL pro zpětné volání začíná na `http://localhost:7071/...` . Tato adresa URL je určena pro váš localhost Server, který je soukromý, takže koncový bod služby nemůže tuto adresu URL zavolat.

Chcete-li místně spustit triggery a akce založené na webhookech v Visual Studio Code, je třeba nastavit veřejnou adresu URL, která zveřejňuje váš server localhost a bezpečně předává volání z koncového bodu služby na adresu URL zpětného volání Webhooku. Můžete použít službu a nástroj pro předávání, jako je [**ngrok**](https://ngrok.com/), který otevře tunel http na port místního hostitele, nebo můžete použít vlastní nástroj.

#### <a name="set-up-call-forwarding-using-ngrok"></a>Nastavení přesměrování volání pomocí **ngrok**

1. Pokud ho nemáte, [Zaregistrujte si účet **ngrok**](https://dashboard.ngrok.com/signup) . V opačném případě se [přihlaste ke svému účtu](https://dashboard.ngrok.com/login).

1. Opatřete si osobní ověřovací token, který musí váš klient **ngrok** připojit a ověřit přístup k vašemu účtu.

   1. Pokud chcete najít [stránku ověřovacího tokenu](https://dashboard.ngrok.com/auth/your-authtoken), v nabídce řídicího panelu účtu rozbalte **ověřování** a vyberte **své Authtoken**.

   1. V poli **Authtoken** zkopírujte token do bezpečného umístění.

1. Na [stránce pro stažení **ngrok**](https://ngrok.com/download) nebo na [řídicím panelu účtu](https://dashboard.ngrok.com/get-started/setup)Stáhněte požadovanou verzi **ngrok** a extrahujte soubor. zip. Další informace najdete v části [Krok 1: rozbalení pro instalaci](https://ngrok.com/download).

1. V počítači otevřete nástroj příkazového řádku. Přejděte do umístění, kde se nachází soubor **ngrok.exe** .

1. Připojte klienta **ngrok** k účtu **ngrok** spuštěním následujícího příkazu. Další informace najdete v části [Krok 2: připojení účtu](https://ngrok.com/download).

   `ngrok authtoken <your_auth_token>`

1. Spuštěním následujícího příkazu otevřete tunelové propojení HTTP pro localhost port 7071. Další informace najdete v části [Krok 3: požár](https://ngrok.com/download).

   `ngrok http 7071`

1. Z výstupu vyhledejte následující řádek:

   `http://<domain>.ngrok.io -> http://localhost:7071`

1. Zkopírujte a uložte adresu URL, která má tento formát: `http://<domain>.ngrok.io`

#### <a name="set-up-the-forwarding-url-in-your-app-settings"></a>Nastavení adresy URL pro předávání v nastavení aplikace

1. V Visual Studio Code přidejte v Návrháři Trigger nebo akci **http + Webhooku** .

1. Po zobrazení výzvy pro umístění koncového bodu hostitele zadejte adresu URL pro předání (přesměrování), kterou jste vytvořili dříve.

   > [!NOTE]
   > Ignorování výzvy způsobí, že se zobrazí upozornění, že je nutné zadat adresu URL pro předávání, zvolte možnost **Konfigurovat** a zadejte adresu URL. Po dokončení tohoto kroku se výzva znovu nezobrazí pro následné triggery Webhooku nebo akce, které můžete přidat.
   >
   > Aby se zobrazila výzva k zobrazení výzvy, na úrovni kořenového projektu otevřete místní nabídku souboru **local.settings.js** a vyberte **Konfigurovat koncový bod přesměrování Webhooku**. Nyní se zobrazí výzva, abyste mohli zadat adresu URL pro přesměrování.

   Visual Studio Code přidá adresu URL pro předávání do **local.settings.jsv** souboru v kořenové složce vašeho projektu. V `Values` objektu se vlastnost s názvem `Workflows.WebhookRedirectHostUri` nyní zobrazí a je nastavena na adresu URL pro předávání, například:
   
   ```json
   {
      "IsEncrypted": false,
      "Values": {
         "AzureWebJobsStorage": "UseDevelopmentStorage=true",
         "FUNCTIONS_WORKER_RUNTIME": "node",
         "FUNCTIONS_V2_COMPATIBILITY_MODE": "true",
         <...>
         "Workflows.WebhookRedirectHostUri": "http://xxxXXXXxxxXXX.ngrok.io",
         <...>
      }
   }
   ```

Když poprvé spustíte místní relaci ladění nebo spustíte pracovní postup bez ladění, modul runtime Logic Apps registruje pracovní postup s koncovým bodem služby a přihlásí se k tomuto koncovému bodu pro oznamování operací Webhooku. Při příštím spuštění pracovního postupu se modul runtime nebude registrovat ani nemůže znovu přihlašovat, protože registrace předplatného již existuje v místním úložišti.

Když zastavíte relaci ladění pro běh pracovního postupu, který používá místně spuštěné triggery nebo akce založené na Webhooku, existující registrace předplatného se neodstraní. Pokud chcete zrušit registraci, musíte ručně odebrat nebo odstranit registrace předplatného.

> [!NOTE]
> Po spuštění pracovního postupu může okno terminálu zobrazit chyby jako v tomto příkladu:
>
> `message='Http request failed with unhandled exception of type 'InvalidOperationException' and message: 'System.InvalidOperationException: Synchronous operations are disallowed. Call ReadAsync or set AllowSynchronousIO to true instead.`
>
> V takovém případě otevřete **local.settings.js** v souboru v kořenové složce projektu a ujistěte se, že je vlastnost nastavena na `true` :
>
> `"FUNCTIONS_V2_COMPATIBILITY_MODE": "true"`

<a name="manage-breakpoints"></a>

## <a name="manage-breakpoints-for-debugging"></a>Správa zarážek pro ladění

Než spustíte a otestujete pracovní postup aplikace logiky spuštěním relace ladění, můžete nastavit [zarážky](https://code.visualstudio.com/docs/editor/debugging#_breakpoints) v rámci **workflow.js** souboru pro každý pracovní postup. Nevyžaduje se žádné jiné nastavení. 

V tuto chvíli se zarážky podporují jenom pro akce, ne pro triggery. Každá definice akce má tato umístění zarážek:

* Nastavte počáteční zarážku na řádku, který zobrazuje název akce. Když tato zarážka zapadne během relace ladění, můžete před vyhodnocením zkontrolovat vstupy akce.

* Nastavte koncovou zarážku na řádku, kde se zobrazí uzavírací složená závorka (**}**). Když tato zarážka narazí během relace ladění, můžete zkontrolovat výsledky akce před dokončením akce.

Chcete-li přidat zarážku, použijte následující postup:

1. Otevřete **workflow.jsv** souboru pro pracovní postup, který chcete ladit.

1. Na řádku, kde chcete nastavit zarážku, vyberte v levém sloupci uvnitř daného sloupce. Chcete-li odebrat zarážku, vyberte tuto zarážku.

   Když spustíte relaci ladění, zobrazení spuštění se zobrazí na levé straně okna kódu, zatímco panel nástrojů ladění se zobrazí v horní části okna.

   > [!NOTE]
   > Pokud se zobrazení spuštění nezobrazí automaticky, stiskněte kombinaci kláves CTRL + SHIFT + D.

1. Chcete-li zkontrolovat dostupné informace v případě výskytu zarážky v zobrazení spuštění, prozkoumejte podokno **proměnné** .

1. Chcete-li pokračovat v provádění pracovního postupu, na panelu nástrojů ladění vyberte možnost **pokračovat** (tlačítko Přehrát).

Zarážky můžete kdykoli přidat nebo odebrat během spuštění pracovního postupu. Pokud ale aktualizujete **workflow.jsv** souboru po spuštění spuštění, zarážky se automaticky neaktualizují. Chcete-li aktualizovat zarážky, restartujte aplikaci logiky.

Obecné informace naleznete v tématu [zarážky – Visual Studio Code](https://code.visualstudio.com/docs/editor/debugging#_breakpoints).

<a name="run-test-debug-locally"></a>

## <a name="run-test-and-debug-locally"></a>Místní spuštění, testování a ladění

Chcete-li otestovat aplikaci logiky, spusťte pomocí těchto kroků ladicí relaci a vyhledejte adresu URL koncového bodu, který je vytvořen triggerem požadavku. Tuto adresu URL budete potřebovat, abyste později mohli odeslat žádost do tohoto koncového bodu.

1. Chcete-li rychle ladit pracovní postup bez stavů, můžete [Povolit historii spuštění tohoto pracovního postupu](#enable-run-history-stateless).

1. Na řádku Visual Studio Code aktivity otevřete nabídku **Spustit** a vyberte **Spustit ladění** (F5).

   Otevře se okno **terminálu** , ve kterém můžete zkontrolovat relaci ladění.

   > [!NOTE]
   > Pokud se zobrazí chybová zpráva **"po spuštění preLaunchTask ' generateDebugSymbols ' existuje chybová** zpráva, v části věnované řešení potíží se nepodařilo [Spustit ladění relace](#debugging-fails-to-start).

1. Nyní vyhledejte adresu URL zpětného volání koncového bodu pro koncový bod na triggeru žádosti.

   1. Znovu otevřete podokno Průzkumník, abyste mohli zobrazit projekt.

   1. V místní nabídce **workflow.jsv** souboru vyberte **Přehled**.

      ![Snímek obrazovky, který zobrazuje podokno Průzkumníka a okno zástupců pro workflow.jsv souboru s vybranou možností "Přehled".](./media/create-stateful-stateless-workflows-visual-studio-code/open-workflow-overview.png)

   1. Vyhledejte hodnotu **adresy URL zpětného volání** , která se podobá této adrese URL pro příklad triggeru žádosti:

      `http://localhost:7071/api/<workflow-name>/triggers/manual/invoke?api-version=2020-05-01-preview&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=<shared-access-signature>`

      ![Snímek obrazovky zobrazující stránku s přehledem pracovního postupu s adresou URL zpětného volání](./media/create-stateful-stateless-workflows-visual-studio-code/find-callback-url.png)

1. Pokud chcete otestovat adresu URL zpětného volání tak, že aktivujete pracovní postup aplikace logiky [, otevřete autor](https://www.postman.com/downloads/) nebo preferovaný nástroj pro vytváření a odesílání požadavků.

   Tento příklad pokračuje pomocí metody post. Další informace najdete v části [post Začínáme](https://learning.postman.com/docs/getting-started/introduction/).

   1. Na panelu nástrojů pro odeslání vyberte **Nový**.

      ![Snímek obrazovky, který zobrazuje příspěvek s vybraným tlačítkem nový](./media/create-stateful-stateless-workflows-visual-studio-code/postman-create-request.png)

   1. V podokně **vytvořit nový** v části **stavební bloky** vyberte **požadavek**.

   1. V okně **Uložit žádost** zadejte v části **název žádosti** název žádosti, například `Test workflow trigger` .

   1. V části **Vyberte kolekci nebo složku, do které chcete uložit**, vyberte **vytvořit kolekci**.

   1. V části **všechny kolekce** zadejte název kolekce, která se má vytvořit pro uspořádání vašich požadavků, stiskněte klávesu ENTER a vyberte **Uložit pro <*název* > kolekce**. Tento příklad používá `Logic Apps requests` jako název kolekce.

      Otevře se podokno žádosti vystavení, kde můžete poslat žádost na adresu URL zpětného volání pro aktivační událost žádosti.

      ![Snímek obrazovky, který zobrazuje vydaný příspěvek v podokně otevřené žádosti](./media/create-stateful-stateless-workflows-visual-studio-code/postman-request-pane.png)

   1. Vraťte se na Visual Studio Code. na stránce Přehled pracovního postupu zkopírujte hodnotu vlastnosti **Adresa URL zpětného volání** .

   1. Vraťte se na post. V podokně žádost zadejte do pole Adresa adresu URL zpětného volání, která se v tuto **chvíli zobrazuje jako** výchozí metoda Request, a pak vyberte **Odeslat**.

      ![Snímek obrazovky zobrazující adresu URL pro odeslání a zpětné volání v poli Adresa s vybraným tlačítkem Odeslat](./media/create-stateful-stateless-workflows-visual-studio-code/postman-test-call-back-url.png)

      Ukázkový pracovní postup aplikace logiky pošle e-mail, který vypadá podobně jako v tomto příkladu:

      ![Snímek obrazovky, který zobrazuje e-mail Outlooku, jak je popsáno v příkladu](./media/create-stateful-stateless-workflows-visual-studio-code/workflow-app-result-email.png)

1. V Visual Studio Code se vraťte na stránku Přehled pracovního postupu.

   Pokud jste vytvořili stavový pracovní postup po odeslání požadavku, který aktivuje pracovní postup, na stránce Přehled se zobrazí stav a historie spuštění pracovního postupu.

   > [!TIP]
   > Pokud se stav spuštění nezobrazí, zkuste aktualizovat stránku Přehled výběrem možnosti **aktualizovat**. Pro aktivační událost, která se přeskočila kvůli kritériím nesplnění nebo hledáním žádných dat, neproběhne žádné spuštění.

   ![Snímek obrazovky zobrazující stránku s přehledem pracovního postupu s stavem a historií spuštění](./media/create-stateful-stateless-workflows-visual-studio-code/post-trigger-call.png)

   | Stav spuštění | Description |
   |------------|-------------|
   | **Bylo přerušeno** | Spuštění bylo zastaveno nebo nebylo dokončeno z důvodu externích problémů, například výpadek systému nebo uplynulé předplatné Azure. |
   | **Stornován** | Běh se aktivoval a začal, ale přijal žádost o zrušení. |
   | **Neúspěšný** | Nejméně jedna akce v běhu se nezdařila. Pro zpracování této chyby nebyly nastaveny žádné následné akce v pracovním postupu. |
   | **Spuštěno** | Běh se aktivoval a probíhá, ale tento stav se může zobrazit i pro běh, který je omezený z důvodu [omezení akce](logic-apps-limits-and-config.md) nebo [aktuálního cenového plánu](https://azure.microsoft.com/pricing/details/logic-apps/). <p><p>**Tip**: Pokud nastavíte [diagnostické protokolování](monitor-logic-apps-log-analytics.md), můžete získat informace o všech událostech omezení, ke kterým dochází. |
   | **Úspěšný** | Spuštění proběhlo úspěšně. Pokud se některá akce nezdařila, došlo k selhání následné akce v pracovním postupu. |
   | **Vypršel časový limit** | Časový limit spuštění vypršel, protože aktuální doba překročila limit doby trvání běhu, který je řízen nastavením [ **uchování historie spuštění ve dnech**](logic-apps-limits-and-config.md#run-duration-retention-limits). Doba trvání běhu se počítá pomocí počátečního času spuštění a omezení doby trvání běhu v daném počátečním čase. <p><p>**Poznámka**: Pokud doba trvání běhu překročí také aktuální *limit uchování historie spuštění*, který je také řízen [nastavením **uchování historie spuštění v rámci dnů**](logic-apps-limits-and-config.md#run-duration-retention-limits), je spuštění vymazáno z historie spuštění podle každodenní úlohy čištění. Bez ohledu na to, jestli doba běhu vyprší nebo dokončí, se doba uchovávání vždycky vypočítá pomocí času spuštění a *aktuálního* limitu uchování. Pokud tedy omezíte dobu trvání spuštění v letadle, vyprší časový limit běhu. Běh ale buď zůstane, nebo se vymaže z historie spuštění na základě toho, jestli doba trvání běhu překročila limit uchování. |
   | **Čekající** | Běh se nezačal nebo je pozastaven, například kvůli dřívější instanci pracovního postupu, která je pořád spuštěná. |
   |||

1. Pokud chcete zkontrolovat stavy pro každý krok v konkrétním běhu a vstupy a výstupy kroku, vyberte tlačítko se třemi tečkami (**...**) pro toto spuštění a vyberte **Zobrazit spustit**.

   ![Snímek obrazovky zobrazující řádek historie spuštění pracovního postupu s tlačítkem elipsy a vybranou možnost Zobrazit běh](./media/create-stateful-stateless-workflows-visual-studio-code/show-run-history.png)

   Visual Studio Code otevře zobrazení monitorování a zobrazí stav pro každý krok v běhu.

   ![Snímek obrazovky zobrazující každý krok ve spuštění pracovního postupu a jejich stav](./media/create-stateful-stateless-workflows-visual-studio-code/run-history-action-status.png)

   > [!NOTE]
   > Pokud spuštění selhalo a krok v zobrazení monitorování ukazuje `400 Bad Request` chybu, může to mít za následek delší název triggeru nebo název akce, který způsobí, že základní identifikátor URI (Uniform Resource Identifier) překračuje výchozí limit znaků. Další informace najdete v tématu ["400 Chybný požadavek"](#400-bad-request).

   Tady jsou možné stavy, které může každý krok pracovního postupu mít:

   | Stav akce | Ikona | Description |
   |---------------|------|-------------|
   | **Bylo přerušeno** | ![Ikona pro stav akce přerušeno][aborted-icon] | Akce se zastavila nebo nedokončila z důvodu externích problémů, například výpadek systému nebo uplynulé předplatné Azure. |
   | **Stornován** | ![Ikona pro stav akce zrušeno][cancelled-icon] | Akce byla spuštěna, ale přijala požadavek na zrušení. |
   | **Neúspěšný** | ![Ikona pro stav akce "neúspěch"][failed-icon] | Akce se nezdařila. |
   | **Spuštěno** | ![Ikona pro spuštěný stav akce][running-icon] | Tato akce je aktuálně spuštěná. |
   | **Přeskočeno** | ![Ikona pro stav akce přeskočeno][skipped-icon] | Akce byla přeskočena, protože bezprostředně předchozí akce se nezdařila. Akce má `runAfter` podmínku, která vyžaduje, aby předchozí akce byla úspěšně dokončena předtím, než bude možné spustit aktuální akci. |
   | **Úspěšný** | ![Ikona stavu akce "úspěch"][succeeded-icon] | Akce byla úspěšná. |
   | **Úspěch s opakováním** | ![Ikona pro stav akce úspěšné a opakované pokusy][succeeded-with-retries-icon] | Akce byla úspěšná, ale jenom po jednom nebo několika opakovaných pokusech. Chcete-li zkontrolovat historii opakování, v zobrazení podrobností historie spuštění vyberte tuto akci, aby bylo možné zobrazit vstupy a výstupy. |
   | **Vypršel časový limit** | ![Ikona pro stav akce vypršel časový limit][timed-out-icon] | Akce byla zastavena z důvodu vypršení časového limitu zadaného nastavením této akce. |
   | **Čekající** | ![Ikona pro stav "čeká na akci"][waiting-icon] | Platí pro akci Webhooku, která čeká na příchozí požadavek od volajícího. |
   ||||

   [aborted-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/aborted.png
   [cancelled-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/cancelled.png
   [failed-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/failed.png
   [running-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/running.png
   [skipped-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/skipped.png
   [succeeded-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/succeeded.png
   [succeeded-with-retries-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/succeeded-with-retries.png
   [timed-out-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/timed-out.png
   [waiting-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/waiting.png

1. Chcete-li zkontrolovat vstupy a výstupy pro jednotlivé kroky, vyberte krok, který chcete zkontrolovat.

   ![Snímek obrazovky zobrazující stav jednotlivých kroků v pracovním postupu plus vstupy a výstupy v rozšířené akci "Odeslat e-mail"](./media/create-stateful-stateless-workflows-visual-studio-code/run-history-details.png)

1. Chcete-li dále zkontrolovat nezpracované vstupy a výstupy pro daný krok, vyberte možnost **Zobrazit nezpracované vstupy** nebo **Zobrazit nezpracované výstupy**.

1. Chcete-li zastavit relaci ladění, v nabídce **Spustit** vyberte možnost **Zastavit ladění** (Shift + F5).

<a name="return-response"></a>

## <a name="return-a-response"></a>Vrátit odpověď

Pokud chcete vrátit odpověď volajícímu, který odeslal požadavek do vaší aplikace logiky, můžete použít integrovanou [akci reakce](../connectors/connectors-native-reqres.md) pro pracovní postup, který začíná triggerem žádosti.

1. V Návrháři pracovního postupu v akci **Odeslat e-mail** vyberte **Nový krok**.

   V návrháři se zobrazí výzva **Zvolit operaci** a otevře se **podokno přidat akci** , ve kterém můžete vybrat další akci.

1. V podokně **přidat akci** zaškrtněte v poli **Vybrat** vyhledávací pole, zda je vybrána možnost **předdefinované** . Do vyhledávacího pole zadejte `response` a vyberte akci **reakce** .

   ![Snímek obrazovky, který zobrazuje návrháře pracovního postupu s vybranou akcí odpovědět](./media/create-stateful-stateless-workflows-visual-studio-code/add-response-action.png)

   Po zobrazení akce **odpověď** v návrháři se automaticky otevře podokno podrobností akce.

   ![Snímek obrazovky, který zobrazuje návrháře pracovních postupů s otevřeným podoknem "odezva" a vlastnost "tělo" je nastavena na hodnotu vlastnosti "tělo" "odeslání e-mailu".](./media/create-stateful-stateless-workflows-visual-studio-code/response-action-details.png)

1. Na kartě **parametry** zadejte požadované informace pro funkci, kterou chcete volat.

   Tento příklad vrátí hodnotu vlastnosti **tělo** , která je výstupem akce **Odeslat e-mail** .

   1. Klikněte do pole vlastnost **těla** , aby se zobrazil seznam dynamického obsahu a zobrazily se dostupné výstupní hodnoty z předchozího triggeru a akcí v pracovním postupu.

      ![Snímek obrazovky, který zobrazuje podokno podrobností akce reakce s ukazatelem myši uvnitř vlastnosti "tělo", aby se zobrazil seznam dynamického obsahu.](./media/create-stateful-stateless-workflows-visual-studio-code/open-dynamic-content-list.png)

   1. V seznamu dynamický obsah v části **Odeslat e-mail** vyberte **text**.

      ![Snímek obrazovky, který zobrazuje seznam otevřeného dynamického obsahu. V seznamu, pod hlavičkou "Odeslat e-mail", je vybrána výstupní hodnota "tělo".](./media/create-stateful-stateless-workflows-visual-studio-code/select-send-email-action-body-output-value.png)

      Až to budete mít, vlastnost **tělo** akce odpověď je teď nastavená **na výstupní** hodnotu **e-mailové akce Odeslat e-mail** .

      ![Snímek obrazovky, který zobrazuje stav jednotlivých kroků v pracovním postupu a také vstupy a výstupy v rozšířené akci "Response".](./media/create-stateful-stateless-workflows-visual-studio-code/response-action-details-body-property.png)

1. V návrháři vyberte Save ( **Uložit**).

<a name="retest-workflow"></a>

## <a name="retest-your-logic-app"></a>Znovu otestujte aplikaci logiky

Po provedení aktualizací aplikace logiky můžete spustit další test tak, že znovu spustíte ladicí program v aplikaci Visual Studio a odešlete další požadavek na aktivaci aktualizované aplikace logiky, podobně jako v případě, že je [spustíte, otestujete a ladíte místně](#run-test-debug-locally).

1. Na řádku Visual Studio Code aktivity otevřete nabídku **Spustit** a vyberte **Spustit ladění** (F5).

1. V příspěvku nebo nástroji pro vytváření a odesílání žádostí odešlete další požadavek na aktivaci pracovního postupu.

1. Pokud jste vytvořili stavový pracovní postup, můžete na stránce Přehled pracovního postupu zjistit stav posledního spuštění. Chcete-li zobrazit stav, vstupy a výstupy pro každý krok v daném spuštění, vyberte tlačítko se třemi tečkami (**...**) pro toto spuštění a vyberte možnost **Zobrazit běh**.

   Zde je například podrobný stav pro spuštění po aktualizaci ukázkového pracovního postupu s akcí Response.

   ![Snímek obrazovky, který zobrazuje stav každého kroku v aktualizovaném pracovním postupu plus vstupy a výstupy v rozšířené akci "Response".](./media/create-stateful-stateless-workflows-visual-studio-code/run-history-details-rerun.png)

1. Chcete-li zastavit relaci ladění, v nabídce **Spustit** vyberte možnost **Zastavit ladění** (Shift + F5).

<a name="firewall-setup"></a>

##  <a name="find-domain-names-for-firewall-access"></a>Vyhledat názvy domén pro přístup k bráně firewall

Před nasazením a spuštěním pracovního postupu aplikace logiky ve službě Azure Portal, pokud má vaše prostředí striktní požadavky na síť nebo brány firewall, které omezují provoz, musíte nastavit oprávnění pro všechna připojení triggeru nebo akce, která existují ve vašem pracovním postupu.

Plně kvalifikované názvy domén (FQDN) pro tato připojení zjistíte pomocí těchto kroků:

1. V projektu aplikace logiky otevřete **connections.jsv** souboru, který se vytvoří po přidání prvního triggeru nebo akce založeného na připojení do pracovního postupu a vyhledání `managedApiConnections` objektu.

1. U každého připojení, které jste vytvořili, najděte, zkopírujte a uložte `connectionRuntimeUrl` hodnotu vlastnosti někam bezpečně, abyste mohli bránu firewall nastavit s těmito informacemi.

   Tento příklad **connections.js** souboru obsahuje dvě připojení, připojení AS2 a připojení k Office 365 s těmito `connectionRuntimeUrl` hodnotami:

   * AS2 `"connectionRuntimeUrl": https://9d51d1ffc9f77572.00.common.logic-{Azure-region}.azure-apihub.net/apim/as2/11d3fec26c87435a80737460c85f42ba`

   * Office 365: `"connectionRuntimeUrl": https://9d51d1ffc9f77572.00.common.logic-{Azure-region}.azure-apihub.net/apim/office365/668073340efe481192096ac27e7d467f`

   ```json
   {
      "managedApiConnections": {
         "as2": {
            "api": {
               "id": "/subscriptions/{Azure-subscription-ID}/providers/Microsoft.Web/locations/{Azure-region}/managedApis/as2"
            },
            "connection": {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Web/connections/{connection-resource-name}"
            },
            "connectionRuntimeUrl": https://9d51d1ffc9f77572.00.common.logic-{Azure-region}.azure-apihub.net/apim/as2/11d3fec26c87435a80737460c85f42ba,
            "authentication": {
               "type":"ManagedServiceIdentity"
            }
         },
         "office365": {
            "api": {
               "id": "/subscriptions/{Azure-subscription-ID}/providers/Microsoft.Web/locations/{Azure-region}/managedApis/office365"
            },
            "connection": {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Web/connections/{connection-resource-name}"
            },
            "connectionRuntimeUrl": https://9d51d1ffc9f77572.00.common.logic-{Azure-region}.azure-apihub.net/apim/office365/668073340efe481192096ac27e7d467f,
            "authentication": {
               "type":"ManagedServiceIdentity"
            }
         }
      }
   }
   ```

<a name="deploy-azure"></a>

## <a name="deploy-to-azure"></a>Nasazení do Azure

Z Visual Studio Code můžete projekt přímo publikovat do Azure, který nasadí vaši aplikaci logiky pomocí nového typu prostředku **Aplikace logiky (Preview)** . Podobně jako u prostředku Function App v Azure Functions nasazení pro tento nový typ prostředku vyžaduje, abyste vybrali [plán hostování a cenovou úroveň](../app-service/overview-hosting-plans.md), které můžete nastavit během nasazování. Další informace o plánech hostování a cenách najdete v těchto tématech:

* [Horizontální navýšení kapacity v Azure App Service](../app-service/manage-scale-up.md)
* [Hostování a škálování Azure Functions](../azure-functions/functions-scale.md)

Aplikaci logiky můžete publikovat jako nový prostředek, který automaticky vytvoří všechny nezbytné prostředky, například [účet Azure Storage, podobně jako požadavky na aplikace funkcí](../azure-functions/storage-considerations.md). Nebo můžete aplikaci logiky publikovat do dříve nasazeného prostředku **Aplikace logiky (Preview)** , který přepíše tuto aplikaci logiky.

### <a name="publish-to-a-new-logic-app-preview-resource"></a>Publikovat do nového prostředku aplikace logiky (Preview)

1. Na řádku Visual Studio Code aktivity vyberte ikonu Azure.

1. Na panelu nástrojů v podokně **Azure: Logic Apps (Preview)** vyberte **nasadit do aplikace logiky**.

   ![Snímek obrazovky s vybraným panelem "Azure: Logic Apps (Preview)" na panelu nástrojů a v podokně s názvem nasadit do aplikace logiky](./media/create-stateful-stateless-workflows-visual-studio-code/deploy-to-logic-app.png)

1. Po zobrazení výzvy vyberte předplatné Azure, které chcete použít pro nasazení aplikace logiky.

1. Ze seznamu, který Visual Studio Code otevře, vyberte z těchto možností:

   * **Vytvoření nové aplikace logiky (Preview) v Azure** (rychlá)
   * **Vytvoření nové aplikace logiky (Preview) v Azure Advanced**
   * Dříve nasazený prostředek **Aplikace logiky (Preview)** , pokud existuje

   Tento příklad pokračuje s možností **vytvořit novou aplikaci logiky (Preview) v Azure Advanced**.

   ![Snímek obrazovky, který zobrazuje podokno "Azure: Logic Apps (Preview)" se seznamem, ve kterém je vybraná možnost vytvořit novou aplikaci logiky (Preview) v Azure.](./media/create-stateful-stateless-workflows-visual-studio-code/select-create-logic-app-options.png)

1. K vytvoření nového prostředku **Aplikace logiky (Preview)** použijte následující postup:

   1. Zadejte globálně jedinečný název nové aplikace logiky, což je název, který se má použít pro prostředek **Aplikace logiky (Preview)** . Tento příklad používá `Fabrikam-Workflows-App`.

      ![Snímek obrazovky, který zobrazuje podokno "Azure: Logic Apps (Preview)" a výzvu k zadání názvu nové aplikace logiky, která se má vytvořit.](./media/create-stateful-stateless-workflows-visual-studio-code/enter-logic-app-name.png)

   1. Vyberte [plán hostování](../app-service/overview-hosting-plans.md) pro novou aplikaci logiky, buď [ **App Service plán** (vyhrazeno)](../azure-functions/dedicated-plan.md) nebo [**Premium**](../azure-functions/functions-premium-plan.md).

      > [!IMPORTANT]
      > Plány spotřeby nejsou podporované ani nejsou dostupné pro tento typ prostředku. Váš vybraný plán má vliv na možnosti a cenové úrovně, které jsou později dostupné. Další informace najdete v těchto tématech: 
      >
      > * [Hostování a škálování Azure Functions](../azure-functions/functions-scale.md)
      > * [Podrobnosti o cenách App Service](https://azure.microsoft.com/pricing/details/app-service/)
      >
      > Plán Premium například poskytuje přístup k síťovým funkcím, jako je připojení a integrace soukromě s virtuálními sítěmi Azure, podobně jako Azure Functions při vytváření a nasazování aplikací logiky. 
      > Další informace najdete v těchto tématech:
      > 
      > * [Možnosti sítí Azure Functions](../azure-functions/functions-networking-options.md)
      > * [Azure Logic Apps spouštění možností kdekoli v síti pomocí Azure Logic Apps Preview](https://techcommunity.microsoft.com/t5/integrations-on-azure/logic-apps-anywhere-networking-possibilities-with-logic-app/ba-p/2105047)

      V tomto příkladu se používá **plán App Service**.

      ![Snímek obrazovky, který zobrazuje podokno "Azure: Logic Apps (Preview)" a výzvu k výběru možnosti "App Service plán" nebo "Premium".](./media/create-stateful-stateless-workflows-visual-studio-code/select-hosting-plan.png)

   1. Vytvořte nový plán App Service nebo vyberte existující plán. Tento příklad vybere **vytvořit nový App Service plán**.

      ![Snímek obrazovky, který zobrazuje podokno "Azure: Logic Apps (Preview)" a zobrazí výzvu k vytvoření nového plánu App Service nebo vyberte existující plán App Service.](./media/create-stateful-stateless-workflows-visual-studio-code/create-app-service-plan.png)

   1. Zadejte název plánu App Service a pak vyberte [cenovou úroveň](../app-service/overview-hosting-plans.md) plánu. Tento příklad vybere plán **zdarma F1** .

      ![Snímek obrazovky, který zobrazuje podokno "Azure: Logic Apps (Preview)" a výzvu k výběru cenové úrovně.](./media/create-stateful-stateless-workflows-visual-studio-code/select-pricing-tier.png)

   1. Pro zajištění optimálního výkonu vyhledejte a vyberte stejnou skupinu prostředků jako projekt pro nasazení.

      > [!NOTE]
      > I když můžete vytvořit nebo použít jinou skupinu prostředků, může to mít vliv na výkon. Pokud vytvoříte nebo zvolíte jinou skupinu prostředků, ale zrušíte po zobrazení výzvy k potvrzení, nasazení se také zruší.

   1. V případě stavových pracovních postupů vyberte **vytvořit nový účet úložiště** nebo existující účet úložiště.

      ![Snímek obrazovky, který zobrazuje podokno "Azure: Logic Apps (Preview)" a výzvu k vytvoření nebo výběru účtu úložiště.](./media/create-stateful-stateless-workflows-visual-studio-code/create-storage-account.png)

   1. Pokud nastavení pro vytváření a nasazování vaší aplikace logiky podporují použití [Application Insights](../azure-monitor/app/app-insights-overview.md), můžete volitelně povolit diagnostické protokolování a trasování pro vaši aplikaci logiky. Můžete to udělat buď při nasazení aplikace logiky z Visual Studio Code nebo po nasazení. Musíte mít instanci Application Insights, ale tento prostředek můžete vytvořit buď [předem](../azure-monitor/app/create-workspace-resource.md), při nasazení aplikace logiky nebo po nasazení.

      Chcete-li nyní povolit protokolování a trasování, postupujte podle následujících kroků:

      1. Vyberte buď existující prostředek Application Insights, nebo **vytvořte nový prostředek Application Insights**.

      1. V [Azure Portal](https://portal.azure.com)přejít na prostředek Application Insights.

      1. V nabídce prostředek vyberte **Přehled**. Vyhledejte a zkopírujte hodnotu **klíče instrumentace** .

      1. V Visual Studio Code v kořenové složce projektu otevřete **local.settings.jsv** souboru.

      1. Do `Values` objektu přidejte `APPINSIGHTS_INSTRUMENTATIONKEY` vlastnost a nastavte hodnotu na klíč instrumentace, například:

         ```json
         {
            "IsEncrypted": false,
            "Values": {
               "AzureWebJobsStorage": "UseDevelopmentStorage=true",
               "FUNCTIONS_WORKER_RUNTIME": "dotnet",
               "APPINSIGHTS_INSTRUMENTATIONKEY": <instrumentation-key>
            }
         }
         ```

         > [!TIP]
         > Můžete kontrolovat, zda se názvy triggerů a akcí ve vaší instanci Application Insights správně zobrazí.
         >
         > 1. V Azure Portal přejít na prostředek Application Insights.
         >
         > 2. V nabídce prostředek prostředku v části **prozkoumat** vyberte **Mapa aplikace**.
         >
         > 3. Zkontrolujte názvy operací, které se zobrazují na mapě.
         >
         > Některé příchozí žádosti z vestavěných triggerů se můžou v mapě aplikace zobrazit jako duplicitní. 
         > Místo použití tohoto `WorkflowName.ActionName` formátu tyto duplicity používají jako název operace název pracovního postupu a pocházejí z Azure Functionsho hostitele.

      1. V dalším kroku můžete volitelně upravit úroveň závažnosti pro data trasování, která vaše aplikace logiky shromáždí a pošle do vaší instance Application Insights.

         Pokaždé, když dojde k události související s pracovním postupem, například když se aktivuje pracovní postup nebo když se spustí akce, modul runtime vygeneruje různá trasování. Tato trasování pokrývají dobu života pracovního postupu a zahrnují, ale nejsou omezeny na následující typy událostí:

         * Aktivita služby, například spuštění, zastavení a chyby.
         * Úlohy a aktivity dispečera.
         * Aktivita pracovního postupu, například Trigger, akce a spuštění.
         * Aktivita žádosti o úložiště, například úspěch nebo neúspěch.
         * Aktivita požadavku HTTP, například příchozí, odchozí, úspěšnost a selhání.
         * Všechna trasování vývoje, například zprávy ladění.

         Každý typ události je přiřazený k úrovni závažnosti. `Trace`Úroveň například zachycuje nejpodrobnější zprávy, zatímco `Information` úroveň zachycuje obecnou aktivitu v pracovním postupu, například při spuštění a zastavení vaší aplikace logiky, pracovního postupu, triggeru a akcí. Tato tabulka popisuje úrovně závažnosti a jejich typy trasování:

         | Úroveň závažnosti | Typ trasování |
         |----------------|------------|
         | Kritické | Protokoly, které popisují neodstranitelnou chybu ve vaší aplikaci logiky. |
         | Ladění | Protokoly, které můžete použít k vyšetřování během vývoje, například příchozí a odchozí volání HTTP. |
         | Chyba | Protokoly indikující selhání provádění pracovního postupu, ale ne obecné selhání ve vaší aplikaci logiky. |
         | Informace | Protokoly, které sledují obecnou aktivitu v aplikaci logiky nebo pracovním postupu, například: <p><p>– Při spuštění a ukončení triggeru, akce nebo spuštění. <br>– Při spuštění nebo ukončení aplikace logiky. |
         | Trasování | Protokoly, které obsahují nejpodrobnější zprávy, například požadavky na úložiště nebo aktivity dispečera, a také všechny zprávy, které souvisejí s aktivitou spuštění pracovního postupu. |
         | Upozornění | Protokoly, které ve vaší aplikaci logiky zvýrazňují neobvyklý stav, ale nebrání jejímu spuštění. |
         |||

         Chcete-li nastavit úroveň závažnosti, na úrovni kořenového projektu otevřete **host.jsv** souboru a vyhledejte `logging` objekt. Tento objekt řídí filtrování protokolu pro všechny pracovní postupy ve vaší aplikaci logiky a následuje [rozvržení ASP.NET Core pro filtrování typu protokolu](/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1&preserve-view=true#log-filtering).

         ```json
         {
            "version": "2.0",
            "logging": {
               "applicationInsights": {
                  "samplingExcludedTypes": "Request",
                  "samplingSettings": {
                     "isEnabled": true
                  }
               }
            }
         }
         ```

         Pokud `logging` objekt neobsahuje `logLevel` objekt, který obsahuje `Host.Triggers.Workflow` vlastnost, přidejte tyto položky. Nastavte vlastnost na úroveň závažnosti pro typ trasování, který chcete, například:

         ```json
         {
            "version": "2.0",
            "logging": {
               "applicationInsights": {
                  "samplingExcludedTypes": "Request",
                  "samplingSettings": {
                     "isEnabled": true
                  }
               },
               "logLevel": {
                  "Host.Triggers.Workflow": "Information"
               }
            }
         }
         ```

   Až budete s postupem nasazení hotovi, Visual Studio Code začne vytvářet a nasazovat prostředky nezbytné pro publikování aplikace logiky.

1. Chcete-li zkontrolovat a monitorovat proces nasazení, v nabídce **zobrazení** vyberte možnost **výstup**. V seznamu panelu nástrojů okna výstup vyberte možnost **Azure Logic Apps**.

   ![Snímek obrazovky zobrazující okno výstup s názvem "Azure Logic Apps" vybraným v seznamu panelu nástrojů spolu s průběhem a stavy nasazení.](./media/create-stateful-stateless-workflows-visual-studio-code/logic-app-deployment-output-window.png)

   Po Visual Studio Code dokončení nasazení aplikace logiky do Azure se zobrazí následující zpráva:

   ![Snímek obrazovky, který zobrazuje zprávu, že nasazení do Azure bylo úspěšně dokončeno.](./media/create-stateful-stateless-workflows-visual-studio-code/deployment-to-azure-completed.png)

   Blahopřejeme, vaše aplikace logiky je teď v Azure živá a ve výchozím nastavení povolená.

V dalším kroku se dozvíte, jak provádět tyto úlohy:

* [Přidejte do projektu prázdný pracovní postup](#add-workflow-existing-project).

* [Spravujte nasazené aplikace logiky v Visual Studio Code](#manage-deployed-apps-vs-code) nebo pomocí [Azure Portal](#manage-deployed-apps-portal).

* [Povolí historii spouštění bezstavových pracovních postupů](#enable-run-history-stateless).

* [Povolí zobrazení monitorování v Azure Portal pro nasazenou aplikaci logiky](#enable-monitoring).

<a name="add-workflow-existing-project"></a>

## <a name="add-blank-workflow-to-project"></a>Přidat prázdný pracovní postup do projektu

V projektu aplikace logiky můžete mít několik pracovních postupů. Chcete-li přidat prázdný pracovní postup do projektu, postupujte takto:

1. Na řádku Visual Studio Code aktivity vyberte ikonu Azure.

1. V podokně Azure vedle **Azure: Logic Apps (Preview)** vyberte **vytvořit pracovní postup** (ikona pro Azure Logic Apps).

1. Vyberte typ pracovního postupu, který chcete přidat: **stavový** nebo **bezstavový**

1. Zadejte název pracovního postupu.

Až to budete mít, v projektu se zobrazí nová složka pracovního postupu spolu s **workflow.jsv** souboru pro definici pracovního postupu.

<a name="manage-deployed-apps-vs-code"></a>

## <a name="manage-deployed-logic-apps-in-visual-studio-code"></a>Správa nasazených aplikací logiky v Visual Studio Code

V Visual Studio Code můžete zobrazit všechny nasazené aplikace logiky ve vašem předplatném Azure, ať už se jedná o původní **Logic Apps** nebo typ prostředku **Aplikace logiky (Preview)** , a vybrat úkoly, které vám pomůžou tyto aplikace logiky spravovat. Pro přístup k oběma typům prostředků ale potřebujete rozšíření **Azure Logic Apps** i **Azure Logic Apps (Preview)** pro Visual Studio Code.

1. Na levém panelu nástrojů vyberte ikonu Azure. V podokně **Azure: Logic Apps (Preview)** rozbalte své předplatné, ve kterém se zobrazí všechny nasazené Logic Apps pro toto předplatné.

1. Otevřete aplikaci logiky, kterou chcete spravovat. V místní nabídce aplikace logiky vyberte úlohu, kterou chcete provést.

   Můžete například vybrat úkoly, jako je zastavení, spuštění, restartování nebo odstranění nasazené aplikace logiky.

   ![Snímek obrazovky, který zobrazuje Visual Studio Code s otevřeným podoknem rozšíření Azure Logic Apps (Preview) a nasazeným pracovním postupem.](./media/create-stateful-stateless-workflows-visual-studio-code/find-deployed-workflow-visual-studio-code.png)

1. Pokud chcete zobrazit všechny pracovní postupy v aplikaci logiky, rozbalte svou aplikaci logiky a potom rozbalte uzel **pracovní postupy** .

1. Pokud chcete zobrazit konkrétní pracovní postup, otevřete místní nabídku pracovního postupu a vyberte **otevřít v Návrháři**, který otevře pracovní postup v režimu jen pro čtení.

   Chcete-li upravit pracovní postup, máte tyto možnosti:

   * V Visual Studio Code otevřete **workflow.jsprojektu na** soubor v Návrháři pracovních postupů, proveďte úpravy a znovu nasaďte svoji aplikaci logiky do Azure.

   * V Azure Portal [Najděte a otevřete aplikaci logiky](#manage-deployed-apps-portal). Vyhledejte, upravte a uložte pracovní postup.

1. Pokud chcete otevřít nasazenou aplikaci logiky v Azure Portal, otevřete místní nabídku aplikace logiky a vyberte **otevřít na portálu**.

   Azure Portal se otevře v prohlížeči, přihlásí se k portálu automaticky, pokud jste přihlášeni k Visual Studio Code a zobrazíte vaši aplikaci logiky.

   ![Snímek obrazovky zobrazující stránku Azure Portal pro vaši aplikaci logiky v Visual Studio Code](./media/create-stateful-stateless-workflows-visual-studio-code/deployed-workflow-azure-portal.png)

   Můžete se také přihlásit samostatně k Azure Portal, použít vyhledávací pole na portálu k vyhledání aplikace logiky a pak vybrat vaši aplikaci logiky ze seznamu výsledků.

   ![Snímek obrazovky zobrazující Azure Portal a panel hledání s výsledky hledání nasazené aplikace logiky, která se zobrazí jako vybraná.](./media/create-stateful-stateless-workflows-visual-studio-code/find-deployed-workflow-azure-portal.png)

<a name="manage-deployed-apps-portal"></a>

## <a name="manage-deployed-logic-apps-in-the-portal"></a>Správa nasazených aplikací logiky na portálu

V Azure Portal můžete zobrazit všechny nasazené aplikace logiky, které jsou ve vašem předplatném Azure, ať už se jedná o původní typ prostředku **Logic Apps** nebo typ prostředku **Aplikace logiky (Preview)** . V současné době se každý typ prostředku organizuje a spravuje jako samostatné kategorie v Azure. Pokud chcete najít Logic Apps, které mají typ prostředku **Aplikace logiky (Preview)** , postupujte podle těchto kroků:

1. Do vyhledávacího pole Azure Portal zadejte `logic app preview` . Po zobrazení seznamu výsledků vyberte v části **služby** možnost **Aplikace logiky (Preview)**.

   ![Snímek obrazovky, který zobrazuje vyhledávací pole Azure Portal s textem hledání "Logic App Preview".](./media/create-stateful-stateless-workflows-visual-studio-code/portal-find-logic-app-preview-resource.png)

1. V podokně **Aplikace logiky (Preview)** Najděte a vyberte aplikaci logiky, kterou jste nasadili z Visual Studio Code.

   ![Snímek obrazovky zobrazující Azure Portal a prostředky aplikace logiky (Preview) nasazené v Azure](./media/create-stateful-stateless-workflows-visual-studio-code/logic-app-preview-resources-pane.png)

   Azure Portal otevře stránku jednotlivého prostředku pro vybranou aplikaci logiky.

   ![Snímek obrazovky zobrazující stránku prostředků pracovního postupu aplikace logiky v Azure Portal.](./media/create-stateful-stateless-workflows-visual-studio-code/deployed-workflow-azure-portal.png)

1. Pokud chcete zobrazit pracovní postupy v této aplikaci logiky, vyberte v nabídce aplikace logiky **pracovní postupy**.

   V podokně **pracovní postupy** se zobrazují všechny pracovní postupy v aktuální aplikaci logiky. Tento příklad ukazuje pracovní postup, který jste vytvořili v Visual Studio Code.

   ![Snímek obrazovky zobrazující stránku prostředků aplikace logiky (Preview) s otevřeným podoknem pracovní postupy a nasazeným pracovním postupem](./media/create-stateful-stateless-workflows-visual-studio-code/deployed-logic-app-workflows-pane.png)

1. Pracovní postup zobrazíte tak, že v podokně **pracovní postupy** vyberete tento pracovní postup.

   Otevře se podokno pracovní postup a zobrazí se další informace a úlohy, které můžete v pracovním postupu provádět.

   Pokud například chcete zobrazit kroky v pracovním postupu, vyberte **Návrhář**.

   ![Snímek obrazovky, který zobrazuje podokno "Přehled" vybraného pracovního postupu, zatímco v nabídce pracovního postupu se zobrazuje vybraný příkaz "Designer".](./media/create-stateful-stateless-workflows-visual-studio-code/workflow-overview-pane-select-designer.png)

   Otevře se Návrhář pracovního postupu a zobrazí se pracovní postup, který jste vytvořili v Visual Studio Code. V Azure Portal teď můžete provádět změny tohoto pracovního postupu.

   ![Snímek obrazovky, který zobrazuje návrháře pracovních postupů a pracovní postup nasazený z Visual Studio Code.](./media/create-stateful-stateless-workflows-visual-studio-code/opened-workflow-designer.png)

<a name="add-workflow-portal"></a>

## <a name="add-another-workflow-in-the-portal"></a>Přidat další pracovní postup na portál

Prostřednictvím Azure Portal můžete přidat prázdné pracovní postupy do prostředku **Aplikace logiky (Preview)** , který jste nasadili z Visual Studio Code a sestavovat tyto pracovní postupy v Azure Portal.

1. V [Azure Portal](https://portal.azure.com)vyhledejte a vyberte nasazený prostředek **Aplikace logiky (Preview)** .

1. V nabídce aplikace logiky vyberte **pracovní postupy**. V podokně **pracovní postupy** vyberte **Přidat**.

   ![Snímek obrazovky, který zobrazuje podokno pracovní postupy a panel nástrojů vybrané aplikace logiky s vybraným příkazem přidat](./media/create-stateful-stateless-workflows-visual-studio-code/add-new-workflow.png)

1. V podokně **nový pracovní postup** zadejte název pracovního postupu. Vyberte **stavové** nebo **stavové** **>** **Vytvoření**.

   Až Azure nasadí nový pracovní postup, který se zobrazí v podokně **pracovní postupy** , vyberte tento pracovní postup, abyste mohli spravovat a provádět další úkoly, jako je například otevření návrháře nebo zobrazení kódu.

   ![Snímek obrazovky zobrazující vybraný pracovní postup s možnostmi správy a revize](./media/create-stateful-stateless-workflows-visual-studio-code/view-new-workflow.png)

   Například při otevření návrháře pro nový pracovní postup se zobrazí prázdné plátno. Tento pracovní postup teď můžete vytvořit v Azure Portal.

   ![Snímek obrazovky, který zobrazuje návrháře pracovního postupu a prázdný pracovní postup.](./media/create-stateful-stateless-workflows-visual-studio-code/opened-blank-workflow-designer.png)

<a name="enable-run-history-stateless"></a>

## <a name="enable-run-history-for-stateless-workflows"></a>Povolit historii spuštění pro bezstavové pracovní postupy

Pokud chcete rychle ladit pracovní postup bez stavů, můžete pro tento pracovní postup povolit historii spuštění a po dokončení pak zakázat historii spuštění. Použijte následující postup pro Visual Studio Code, nebo pokud pracujete v Azure Portal, přečtěte si téma [Vytvoření stavových a bezstavových pracovních postupů v Azure Portal](create-stateful-stateless-workflows-azure-portal.md#enable-run-history-stateless).

1. V projektu Visual Studio Code rozbalte složku **Workflow-DesignTime** a otevřete **local.settings.jsv** souboru.

1. Přidejte `Workflows.{yourWorkflowName}.operationOptions` vlastnost a nastavte hodnotu na `WithStatelessRunHistory` , například:

   **Windows**

   ```json
   {
      "IsEncrypted": false,
      "Values": {
         "AzureWebJobsStorage": "UseDevelopmentStorage=true",
         "FUNCTIONS_WORKER_RUNTIME": "dotnet",
         "Workflows.{yourWorkflowName}.OperationOptions": "WithStatelessRunHistory"
      }
   }
   ```

   **macOS nebo Linux**

   ```json
   {
      "IsEncrypted": false,
      "Values": {
         "AzureWebJobsStorage": "DefaultEndpointsProtocol=https;AccountName=fabrikamstorageacct; \
             AccountKey=<access-key>;EndpointSuffix=core.windows.net",
         "FUNCTIONS_WORKER_RUNTIME": "dotnet",
         "Workflows.{yourWorkflowName}.OperationOptions": "WithStatelessRunHistory"
      }
   }
   ```

1. Chcete-li po dokončení zakázat historii spuštění, buď nastavte `Workflows.{yourWorkflowName}.OperationOptions` vlastnost na `None` hodnotu nebo odstraňte vlastnost a její hodnotu.

<a name="enable-monitoring"></a>

## <a name="enable-monitoring-view-in-the-azure-portal"></a>Povolit zobrazení monitorování v Azure Portal

Po nasazení prostředků **Aplikace logiky (Preview)** z Visual Studio Code do Azure si můžete projít veškerou dostupnou historii spuštění a podrobnosti pracovního postupu v tomto prostředku pomocí Azure Portal a možností **monitorování** pro tento pracovní postup. Nejdřív ale musíte povolit možnost zobrazení **monitorování** pro daný prostředek aplikace logiky.

1. V [Azure Portal](https://portal.azure.com)vyhledejte a vyberte nasazený prostředek **Aplikace logiky (Preview)** .

1. V nabídce tohoto prostředku v části **rozhraní API** vyberte **CORS**.

1. V podokně **CORS** v části **Povolené zdroje** přidejte zástupný znak (*).

1. Až budete hotovi, vyberte na panelu nástrojů **CORS** možnost **Uložit**.

   ![Snímek obrazovky zobrazující Azure Portal s nasazeným prostředkem aplikace logiky (Preview) V nabídce prostředek se vybere "CORS" s novou položkou "povolené zdroje" nastavenou na zástupný znak "*".](./media/create-stateful-stateless-workflows-visual-studio-code/enable-run-history-deployed-logic-app.png)

<a name="enable-open-application-insights"></a>

## <a name="enable-or-open-application-insights-after-deployment"></a>Povolit nebo otevřít Application Insights po nasazení

Během provádění pracovního postupu vaše aplikace logiky generuje telemetrii spolu s dalšími událostmi. Pomocí této telemetrie získáte lepší přehled o tom, jak dobře je váš pracovní postup spuštěný a jak Logic Apps runtime funguje různým způsobem. Pracovní postup můžete monitorovat pomocí [Application Insights](../azure-monitor/app/app-insights-overview.md), která poskytuje telemetrii téměř v reálném čase (živé metriky). Tato funkce vám může pomáhat prozkoumat chyby a problémy s výkonem snadněji, když tato data použijete k diagnostice problémů, nastavení výstrah a sestavování grafů.

Pokud nastavení pro vytváření a nasazování vaší aplikace logiky podporují použití [Application Insights](../azure-monitor/app/app-insights-overview.md), můžete volitelně povolit diagnostické protokolování a trasování pro vaši aplikaci logiky. Můžete to udělat buď při nasazení aplikace logiky z Visual Studio Code nebo po nasazení. Musíte mít instanci Application Insights, ale tento prostředek můžete vytvořit buď [předem](../azure-monitor/app/create-workspace-resource.md), při nasazení aplikace logiky nebo po nasazení.

Pokud chcete povolit Application Insights v nasazené aplikaci logiky nebo zkontrolovat Application Insights data, když je už povolené, postupujte takto:

1. V Azure Portal Najděte nasazenou aplikaci logiky.

1. V nabídce aplikace logiky v části **Nastavení** vyberte **Application Insights**.

1. Pokud Application Insights není povolená, vyberte v podokně **Application Insights** **zapnout Application Insights**. Po aktualizaci podokna klikněte v dolní části na **použít**.

   Pokud je povolená Application Insights, vyberte v podokně **Application Insights** možnost **Zobrazit Application Insights data**.

Po Application Insights se můžete podívat na různé metriky pro vaši aplikaci logiky. Další informace najdete v těchto tématech:

* [Azure Logic Apps běžící kdekoli – monitor s Application Insights – část 1](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-monitor-with-application/ba-p/1877849)
* [Azure Logic Apps běžící kdekoli – monitor s Application Insights – část 2](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-monitor-with-application/ba-p/2003332)

<a name="deploy-docker"></a>

## <a name="deploy-to-docker"></a>Nasadit do Docker

Aplikaci logiky můžete nasadit do [kontejneru Docker](/visualstudio/docker/tutorials/docker-tutorial#what-is-a-container) jako hostitelské prostředí pomocí [rozhraní .NET CLI](/dotnet/core/tools/). Pomocí těchto příkazů můžete sestavit a publikovat projekt aplikace logiky. Pak můžete sestavit a spustit kontejner Docker jako cíl pro nasazení aplikace logiky.

Pokud nejste obeznámeni s Docker, přečtěte si tato témata:

* [Co je Docker?](/dotnet/architecture/microservices/container-docker-introduction/docker-defined)
* [Úvod ke kontejnerům a Dockeru](/dotnet/architecture/microservices/container-docker-introduction/)
* [Úvod k .NET a Dockeru](/dotnet/core/docker/introduction)
* [Registry, image a kontejnery Dockeru](/dotnet/architecture/microservices/container-docker-introduction/docker-containers-images-registries)
* [Kurz: Začínáme s Docker (Visual Studio Code)](/visualstudio/docker/tutorials/docker-tutorial)

### <a name="requirements"></a>Požadavky

* Účet Azure Storage, který aplikace logiky používá pro nasazení

* Soubor Docker pro pracovní postup, který použijete při sestavování kontejneru Docker

  Například tento ukázkový soubor Docker nasadí aplikaci logiky. Určuje připojovací řetězec, který obsahuje přístupový klíč pro účet Azure Storage, který se použil k publikování aplikace logiky do Azure Portal. Tento řetězec najdete v tématu [získání připojovacího řetězce účtu úložiště](#find-storage-account-connection-string).

   ```text
   FROM mcr.microsoft.com/azure-functions/node:3.0

   ENV AzureWebJobsStorage <storage-account-connection-string>
   ENV AzureWebJobsScriptRoot=/home/site/wwwroot \
       AzureFunctionsJobHost__Logging__Console__IsEnabled=true \
       FUNCTIONS_V2_COMPATIBILITY_MODE=true

   COPY . /home/site/wwwroot

   RUN cd /home/site/wwwroot
   ```

   Další informace najdete v tématu [osvědčené postupy pro zápis souborů Docker](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/) .

<a name="find-storage-account-connection-string"></a>

### <a name="get-storage-account-connection-string"></a>Získání připojovacího řetězce účtu úložiště

Než budete moct sestavit a spustit image kontejneru Docker, musíte získat připojovací řetězec, který obsahuje přístupový klíč k vašemu účtu úložiště. Dříve jste tento účet úložiště vytvořili buď jako k používání rozšíření na macOS nebo Linux, nebo když jste nasadili aplikaci logiky na Azure Portal.

K vyhledání a zkopírování tohoto připojovacího řetězce použijte následující postup:

1. V Azure Portal v nabídce účet úložiště v části **Nastavení** vyberte **přístupové klíče**. 

1. V podokně **přístupové klíče** vyhledejte a zkopírujte připojovací řetězec účtu úložiště, který vypadá podobně jako v tomto příkladu:

   `DefaultEndpointsProtocol=https;AccountName=fabrikamstorageacct;AccountKey=<access-key>;EndpointSuffix=core.windows.net`

   ![Snímek obrazovky zobrazující Azure Portal s přístupovými klíči účtu úložiště a zkopírovaným připojovacím řetězcem](./media/create-stateful-stateless-workflows-visual-studio-code/find-storage-account-connection-string.png)

   Další informace najdete v přehledu [správy klíčů účtu úložiště](../storage/common/storage-account-keys-manage.md?tabs=azure-portal#view-account-access-keys).

1. Uložte připojovací řetězec někam do trezoru, abyste mohli přidat tento řetězec do souboru Docker, který použijete pro nasazení. 

<a name="find-storage-account-master-key"></a>

### <a name="find-master-key-for-storage-account"></a>Najít hlavní klíč pro účet úložiště

Když váš pracovní postup obsahuje aktivační událost žádosti, musíte po sestavení a spuštění image kontejneru Docker [získat adresu URL zpětného volání triggeru](#get-callback-url-request-trigger) . Pro tuto úlohu musíte také zadat hodnotu hlavního klíče pro účet úložiště, který používáte pro nasazení.

1. Pokud chcete najít tento hlavní klíč, otevřete v projektu **Azure-WebJobs-tajné/{Deployment-Name}/host.jsv** souboru.

1. Vyhledejte `AzureWebJobsStorage` vlastnost a zkopírujte hodnotu klíče z této části:

   ```json
   {
      <...>
      "masterKey": {
         "name": "master",
         "value": "<master-key>",
         "encrypted": false
      },
      <...>
   }
   ```

1. Uložte tuto hodnotu klíče někam bezpečně, abyste ji mohli použít později.

<a name="build-run-docker-container-image"></a>

### <a name="build-and-run-your-docker-container-image"></a>Sestavení a spuštění image kontejneru Docker

1. Sestavte image kontejneru Docker pomocí souboru Docker a spusťte tento příkaz:

   `docker build --tag local/workflowcontainer .`

   Další informace najdete v tématu [sestavení Docker](https://docs.docker.com/engine/reference/commandline/build/).

1. Spusťte kontejner místně pomocí tohoto příkazu:

   `docker run -e WEBSITE_HOSTNAME=localhost -p 8080:80 local/workflowcontainer`

   Další informace najdete v tématu [Docker Run](https://docs.docker.com/engine/reference/commandline/run/).

<a name="get-callback-url-request-trigger"></a>

### <a name="get-callback-url-for-request-trigger"></a>Získat adresu URL zpětného volání pro aktivační událost žádosti

Pro pracovní postup, který používá Trigger žádosti, Získejte adresu URL zpětného volání triggeru odesláním této žádosti:

`POST /runtime/webhooks/workflow/api/management/workflows/{workflow-name}/triggers/{trigger-name}/listCallbackUrl?api-version=2020-05-01-preview&code={master-key}`

`{trigger-name}`Hodnota je název triggeru požadavku, který se zobrazí v definici JSON pracovního postupu. `{master-key}`Hodnota je definovaná v účtu Azure Storage, který jste nastavili pro `AzureWebJobsStorage` vlastnost v souboru, **Azure-WebJobs-tajnosti/{Deployment-name}/host.jsna**. Další informace najdete v tématu [vyhledání hlavního klíče účtu úložiště](#find-storage-account-master-key).

<a name="delete-from-designer"></a>

## <a name="delete-items-from-the-designer"></a>Odstranění položek z návrháře

Pokud chcete v pracovním postupu odstranit položku z návrháře, postupujte podle některého z těchto kroků:

* Vyberte položku, otevřete místní nabídku položky (Shift + F10) a vyberte **Odstranit**. Výběrem tlačítka **OK** potvrďte svůj úmysl.

* Vyberte položku a stiskněte klávesu DELETE. Výběrem tlačítka **OK** potvrďte svůj úmysl.

* Vyberte položku, aby se pro tuto položku otevřelo podokno podrobností. V pravém horním rohu podokna otevřete nabídku se třemi tečkami (**...**) a vyberte **Odstranit**. Výběrem tlačítka **OK** potvrďte svůj úmysl.

  ![Snímek obrazovky, který zobrazuje vybranou položku v Návrháři s otevřeným podoknem podrobností a tlačítkem se třemi tečkami a příkazem "odstranit".](./media/create-stateful-stateless-workflows-visual-studio-code/delete-item-from-designer.png)

  > [!TIP]
  > Pokud se nabídka se třemi tečkami nezobrazí, rozbalte okno Visual Studio Code dostatečně velkou, aby se v podokně podrobností zobrazilo tlačítko se třemi tečkami (**...**) v pravém horním rohu.

<a name="troubleshooting"></a>

## <a name="troubleshoot-errors-and-problems"></a>Řešení chyb a problémů

<a name="designer-fails-to-open"></a>

### <a name="designer-fails-to-open"></a>Nepodařilo se otevřít návrháře

Při pokusu o otevření návrháře se zobrazí tato chyba: **"doba návrhu pracovního postupu nemohla být spuštěna"**. Pokud jste se dříve pokusili otevřít návrháře a potom tento projekt přerušit nebo odstranit, balíček rozšíření se nemusí správně stahovat. Chcete-li zjistit, zda se jedná o problém, postupujte podle následujících kroků:

  1. V Visual Studio Code otevřete okno výstup. V nabídce **zobrazení** vyberte možnost **výstup**.

  1. V seznamu v záhlaví okna výstup vyberte možnost **Azure Logic Apps (Preview)** , abyste mohli zkontrolovat výstup z rozšíření, například:

     ![Snímek obrazovky, který zobrazuje okno výstup se zvoleným "Azure Logic Apps".](./media/create-stateful-stateless-workflows-visual-studio-code/check-outout-window-azure-logic-apps.png)

  1. Zkontrolujte výstup a zkontrolujte, zda se zobrazí tato chybová zpráva:

     ```text
     A host error has occurred during startup operation '{operationID}'.
     System.Private.CoreLib: The file 'C:\Users\{userName}\AppData\Local\Temp\Functions\
     ExtensionBundles\Microsoft.Azure.Functions.ExtensionBundle.Workflows\1.1.7\bin\
     DurableTask.AzureStorage.dll' already exists.
     Value cannot be null. (Parameter 'provider')
     Application is shutting down...
     Initialization cancellation requested by runtime.
     Stopping host...
     Host shutdown completed.
     ```

   Tuto chybu vyřešíte tak, že v tomto umístění odstraníte složku **ExtensionBundles** **. ..\Users \{ Your-username} \AppData\Local\Temp\Functions\ExtensionBundles** a znovu otevřete **workflow.js** v souboru v návrháři.

<a name="missing-triggers-actions"></a>

### <a name="new-triggers-and-actions-are-missing-from-the-designer-picker-for-previously-created-workflows"></a>Ve výběru návrháře pro dříve vytvořené pracovní postupy chybí nové aktivační události a akce.

Azure Logic Apps Preview podporuje integrované akce pro operace Azure Functions, likvidní operace a operace XML, jako je **XML ověřování** a **transformace XML**. U dříve vytvořených aplikací logiky se ale tyto akce nemusí zobrazit v nástroji pro výběr návrháře, abyste mohli vybrat, jestli Visual Studio Code používá zastaralou verzi sady rozšíření `Microsoft.Azure.Functions.ExtensionBundle.Workflows` .

Také konektor **operací Azure Functions** a akce se nezobrazí ve výběru návrháře, pokud jste při vytváření aplikace logiky nepovolili nebo nevybrali **Použití konektorů z Azure** . Pokud jste nepovolili konektory nasazené v Azure při vytváření aplikace, můžete je z projektu povolit v Visual Studio Code. V místní nabídce otevřete **workflow.js** a vyberte **použít konektory z Azure**.

Pokud chcete opravit zastaralou sadu prostředků, postupujte podle těchto kroků a odstraňte zastaralou sadu, která umožňuje Visual Studio Code automaticky aktualizovat sadu rozšíření na nejnovější verzi.

> [!NOTE]
> Toto řešení se vztahuje pouze na aplikace logiky, které vytvoříte a nasadíte pomocí Visual Studio Code s rozšířením Azure Logic Apps (Preview), nikoli s Logic Apps, které jste vytvořili pomocí Azure Portal. Viz téma [podporované triggery a akce chybí v návrháři v Azure Portal](create-stateful-stateless-workflows-azure-portal.md#missing-triggers-actions).

1. Uložte veškerou práci, kterou nechcete ztratit, a zavřete Visual Studio.

1. V počítači přejděte do následující složky, která obsahuje složky se správou verzí pro existující sadu prostředků:

   `...\Users\{your-username}\.azure-functions-core-tools\Functions\ExtensionBundles\Microsoft.Azure.Functions.ExtensionBundle.Workflows`

1. Odstraňte složku verze pro starší sadu prostředků, například pokud máte složku pro verzi 1.1.3, tuto složku odstraňte.

1. Nyní přejděte do následující složky, která obsahuje složky se správou verzí pro požadovaný balíček NuGet:

   `...\Users\{your-username}\.nuget\packages\microsoft.azure.workflows.webjobs.extension`

1. Odstraňte složku verze pro předchozí balíček, například pokud máte složku pro verzi 1.0.0.8-Preview, odstraňte tuto složku.

1. Znovu otevřete Visual Studio Code, projekt a **workflow.js** v souboru v návrháři.

Chybějící triggery a akce se nyní zobrazí v návrháři.

<a name="400-bad-request"></a>

### <a name="400-bad-request-appears-on-a-trigger-or-action"></a>u triggeru nebo akce se zobrazí zpráva "400 Špatný požadavek"

Pokud se spuštění nepovede a zkontrolujete zobrazení spustit v monitorování, tato chyba se může zobrazit u triggeru nebo akce, která má delší název, což způsobí, že základní identifikátor URI (Uniform Resource Identifier) překročí výchozí limit znaků.

Chcete-li tento problém vyřešit a upravit delší identifikátor URI, upravte `UrlSegmentMaxCount` `UrlSegmentMaxLength` klíče registru a v počítači podle následujících kroků. Tyto výchozí hodnoty klíče jsou popsány v tomto tématu [Http.sys nastavení registru pro Windows](/troubleshoot/iis/httpsys-registry-windows).

> [!IMPORTANT]
> Než začnete, ujistěte se, že jste ušetřili práci. Toto řešení vyžaduje, abyste po dokončení počítače restartovali počítač, aby se změny mohly projevit.

1. V počítači otevřete okno **spuštění** a spusťte `regedit` příkaz, který otevře Editor registru.

1. V okně **řízení uživatelských účtů** vyberte možnost **Ano** , pokud chcete povolit změny v počítači.

1. V levém podokně rozbalte v části **počítač** uzly v cestě, **HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\HTTP\Parameters** a pak vyberte **parametry**.

1. V pravém podokně vyhledejte `UrlSegmentMaxCount` `UrlSegmentMaxLength` klíče registru a.

1. Zvyšte tyto hodnoty klíčů tak, aby identifikátory URI mohly obsahovat názvy, které chcete použít. Pokud tyto klíče neexistují, přidejte je do složky **Parameters** pomocí následujících kroků:

   1. V místní nabídce **parametrů** vyberte možnost **Nová**  >  **Hodnota DWORD (32 bitů)**.

   1. Do pole pro úpravy, které se zobrazí, zadejte `UrlSegmentMaxCount` název nového klíče.

   1. Otevřete místní nabídku nového klíče a vyberte **změnit**.

   1. Do pole **Upravit řetězec** , které se zobrazí, zadejte hodnotu klíče **dat hodnoty** , kterou chcete mít v šestnáctkovém nebo desítkovém formátu. Například `400` v šestnáctkovém formátu je ekvivalentem `1024` v desítkové soustavě.

   1. Chcete-li přidat `UrlSegmentMaxLength` hodnotu klíče, opakujte tento postup.

   Po zvýšení nebo přidání těchto hodnot klíče bude Editor registru vypadat jako v tomto příkladu:

   ![Snímek obrazovky, který zobrazuje Editor registru.](media/create-stateful-stateless-workflows-visual-studio-code/edit-registry-settings-uri-length.png)

1. Až budete připraveni, restartujte počítač, aby se změny projevily.

<a name="debugging-fails-to-start"></a>

### <a name="debugging-session-fails-to-start"></a>Nepodařilo se spustit relaci ladění.

Když se pokusíte spustit relaci ladění, zobrazí se chyba **"po spuštění preLaunchTask ' generateDebugSymbols ' existuje chyba**. Chcete-li vyřešit tento problém, upravte **tasks.js** souboru v projektu pro přeskočení generování symbolů.

1. V projektu rozbalte složku **. VSCode** a otevřete **tasks.jsv** souboru.

1. V následujícím úkolu odstraňte řádek, `"dependsOn: "generateDebugSymbols"` včetně čárky, která končí předchozím řádkem, například:

   Před:

   ```json
    {
      "type": "func",
      "command": "host start",
      "problemMatcher": "$func-watch",
      "isBackground": true,
      "dependsOn": "generateDebugSymbols"
    }
   ```

   Po:

   ```json
    {
      "type": "func",
      "command": "host start",
      "problemMatcher": "$func-watch",
      "isBackground": true
    }
   ```

## <a name="next-steps"></a>Další kroky

Rádi bychom od vás slyšeli o vašich zkušenostech s rozšířením Azure Logic Apps (Preview).

* V případě chyb nebo problémů [vytvořte problémy v GitHubu](https://github.com/Azure/logicapps/issues).
* Pro otázky, požadavky, komentáře a další zpětnou vazbu [použijte tento formulář zpětné vazby](https://aka.ms/lafeedback).
