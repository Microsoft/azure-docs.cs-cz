---
title: Vytvoření pracovních postupů služby Automation (Preview) v Visual Studio Code
description: Vytvoření bezstavových nebo stavových pracovních postupů pro automatizaci s rozšířením Azure Logic Apps (Preview) v Visual Studio Code pro integraci aplikací, dat, cloudových služeb a místních systémů
services: logic-apps
ms.suite: integration
ms.reviewer: deli, rohitha, vikanand, hongzili, sopai, absaafan, logicappspm
ms.topic: conceptual
ms.date: 11/17/2020
ms.openlocfilehash: 14809cb28870e88cfa584c4f02360d50beabf901
ms.sourcegitcommit: f311f112c9ca711d88a096bed43040fcdad24433
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2020
ms.locfileid: "94981034"
---
# <a name="create-stateful-or-stateless-workflows-in-visual-studio-code-with-the-azure-logic-apps-preview-extension"></a>Vytváření stavových nebo bezstavových pracovních postupů ve Visual Studio Code s využitím rozšíření Azure Logic Apps (Preview)

> [!IMPORTANT]
> Tato funkce je ve verzi Public Preview, poskytuje se bez smlouvy o úrovni služeb a nedoporučuje pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

K vytváření pracovních postupů aplikací logiky, které se integrují napříč aplikacemi, daty, Cloud Services a systémy, můžete použít Visual Studio Code a rozšíření Azure Logic Apps (Preview) k sestavování a místnímu spuštění stavových a místně spuštěných [ *stateful* pracovních postupů aplikací logiky *stateless*](#stateful-stateless) ve vašem vývojovém prostředí.

![Snímek obrazovky zobrazující Visual Studio Code a pracovní postup aplikace logiky](./media/create-stateful-stateless-workflows-visual-studio-code/visual-studio-code-logic-apps-overview.png)

Aplikace logiky, které vytvoříte s rozšířením Public Preview, používají nový typ prostředku **Aplikace logiky (Preview)** a využívají modul runtime [Azure Functions](../azure-functions/functions-overview.md) v místním prostředí. Tento nový typ prostředku může zahrnovat několik pracovních postupů a je podobný jako u **Function Appho** typu prostředku, který může zahrnovat víc funkcí.

Dále existuje původní typ prostředku **Logic Apps** pro vytvoření a použití v Visual Studio Code a Azure Portal. Prostředí pro původní typ prostředku se ale liší od nového typu prostředku. V současné době mohou současně existovat typy prostředků **Logic Apps** i **Logic App (Preview)** v Visual Studio Code a Azure Portal. Můžete zobrazit a přistupovat ke všem nasazeným Logic Apps v předplatném Azure, ale zobrazí se a uchovávají se samostatně ve vlastních kategoriích a oddílech.

Tento článek obsahuje podrobný [Přehled této veřejné verze Preview](#whats-new), popisuje různé aspekty typu prostředku **Aplikace logiky (Preview)** a postup vytvoření tohoto prostředku pomocí Visual Studio Code:

* Jak se liší [stavové a bezstavové](#stateful-stateless) logicové aplikace od sebe navzájem.

* Jak splňovat požadavky na [instalaci](#prerequisites) a [nastavit Visual Studio Code](#set-up) pro rozšíření verze Public Preview.

* Postup sestavení nových pracovních postupů **Aplikace logiky (Preview)** [vytvořením projektu a výběrem šablony pracovního postupu](#create-project).

* Jak místně spouštět a ladit nové Logic Apps v Visual Studio Code.

* Jak publikovat tyto nové aplikace logiky přímo z Visual Studio Code [do Azure](#publish-azure) nebo [do kontejneru Docker](#deploy-docker) , který můžete spustit kdekoli. Další informace o Docker najdete v tématu [co je Docker](/dotnet/architecture/microservices/container-docker-introduction/docker-defined)?

<a name="whats-new"></a>

## <a name="whats-in-this-public-preview"></a>Co je ve verzi Public Preview?

Rozšíření Azure Logic Apps (Preview) přináší do místního prostředí pro vývoj v Visual Studio Code mnoho současných a dalších možností Logic Apps, například:

* Sestavujte aplikace logiky pro integrační a automatizované pracovní postupy z [390 + konektorů](/connectors/connector-reference/connector-reference-logicapps-connectors) pro aplikace typu software jako služba (SaaS) a PaaS (Platform-as-a-Service) plus konektory pro místní systémy.

  * Některé spravované konektory, jako je Azure Service Bus, Azure Event Hubs a SQL Server, se spouštějí podobně jako integrované nativní triggery a akce, jako je třeba akce HTTP.

  * Vytvářejte a nasaďte Logic Apps, které se dají spustit kdekoli, protože služba Azure Logic Apps generuje připojovací řetězce sdíleného přístupového podpisu (SAS), které tyto aplikace logiky můžou použít k odesílání požadavků do koncového bodu cloudového připojení. Služba Logic Apps ukládá tyto připojovací řetězce s dalšími nastaveními aplikace, takže tyto hodnoty můžete snadno uložit do Azure Key Vault při nasazení do Azure.

    > [!NOTE]
    > Ve výchozím nastavení má prostředek **Aplikace logiky (Preview)** k dispozici [spravovanou identitu přiřazenou systémem](../logic-apps/create-managed-service-identity.md) automaticky pro ověřování připojení za běhu. Tato identita se liší od přihlašovacích údajů k ověřování nebo připojovacího řetězce, který používáte při vytváření připojení. Pokud tuto identitu zakážete, nebudou připojení fungovat za běhu.

* Vytvářejte bezstavové aplikace logiky, které běží jenom v paměti, takže rychleji dostanou rychlejší, mají vyšší propustnost a náklady méně, než se spustí, protože historie spuštění a data mezi akcemi nejsou v externím úložišti trvalé. Volitelně můžete povolit historii spuštění pro snazší ladění. Další informace najdete v tématu [stavová versus Bezstavová Logic aplikace](#stateful-stateless).

* Spouštějte a laďte aplikace logiky lokálně ve Visual Studio Code vývojovém prostředí.

* Publikujte a nasaďte aplikace logiky z Visual Studio Code přímo do různých hostujících prostředí, jako jsou [Azure App Service](../app-service/environment/intro.md) a [kontejnery Docker](/dotnet/core/docker/introduction).

> [!NOTE]
> Informace o aktuálních známých problémech najdete na [stránce GitHub se známými problémy](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md)v rozšíření Preview.

<a name="stateful-stateless"></a>

## <a name="stateful-versus-stateless-logic-apps"></a>Stavová versus Bezstavová Logic aplikace

* *Stavové*

  Vytvářejte stavové aplikace logiky, když potřebujete zachovat, zkontrolovat nebo odkázat data z předchozích událostí. Tyto aplikace logiky udržují vstup a výstup pro každou akci a jejich stavy pracovního postupu v externím úložišti, což umožňuje zkontrolovat podrobnosti a historii spuštění po dokončení každého spuštění. Stavové aplikace logiky poskytují vysokou odolnost, pokud dojde k výpadkům. Po obnovení služeb a systémů můžete rekonstruovat přerušené aplikace logiky spuštěné z uloženého stavu a znovu spustit Logic Apps k dokončení. Stavové pracovní postupy můžou běžet až do roku.

* *Bezstavová*

  Vytvářejte bezstavové aplikace logiky, pokud nepotřebujete ukládat, kontrolovat nebo odkazovat data z předchozích událostí v externím úložišti pro pozdější kontrolu. Tyto aplikace logiky udržují vstup a výstup pro každou akci a jejich stavy pracovního postupu pouze v paměti místo přenosu těchto informací do externího úložiště. V důsledku toho jsou bezstavové aplikace logiky kratší spuštění, které obvykle nepřesahují dobu 5 minut, rychlejší výkon při rychlejší odezvě, vyšší propustnost a snížené provozní náklady, protože podrobnosti a historie spuštění nejsou uchovávány v externím úložišti. Pokud ale dojde k výpadku, přerušené běhy se automaticky neobnoví, takže volající musí ručně znovu odeslat přerušené běhy. Tyto aplikace logiky lze spustit pouze synchronně a pro snazší ladění můžete [Povolit historii spuštění](#run-history), která má nějaký dopad na výkon.

  Bezstavové pracovní postupy aktuálně podporují jenom *Akce* pro [spravované konektory](../connectors/apis-list.md#managed-api-connectors), které jsou nasazené v Azure, a ne triggery. Pokud chcete spustit pracovní postup, vyberte [integrovaný Trigger žádosti, Event Hubs nebo Service Bus](../connectors/apis-list.md#built-ins), který se spustí nativně s modulem runtime Logic Apps. Další informace o nepodporovaných triggerech, akcích a konektorech najdete v tématu [nepodporované a nedostupné funkce](#unsupported).

Informace o tom, jak se vnořené aplikace logiky chovají odlišně mezi stavovou a bezstavovou logicovou aplikací, najdete v tématu [rozdíly v vnořování chování mezi stavovou a bezstavovou logikou aplikace](#nested-behavior)

<a name="pricing-model"></a>

## <a name="pricing-model"></a>Cenový model

Když nasadíte nový typ prostředku **Aplikace logiky (Preview)** , zobrazí se výzva k výběru plánu hostování, konkrétně [plán App Service plán nebo plán Premium](../azure-functions/functions-scale.md) , který se použije jako cenový model. Pokud vyberete plán App Service, budete vyzváni k výběru [cenové úrovně](../app-service/overview-hosting-plans.md). Během veřejné verze Preview se pro spuštění Logic Apps na App Service neúčtují *Další* poplatky nad vybraným plánem.

Stavové aplikace logiky používají [externí úložiště](../azure-functions/functions-scale.md#storage-account-requirements), takže Azure Storage cenový model se vztahuje na transakce úložiště, které provádí modul runtime Azure Logic Apps. Například fronty se používají pro plánování, zatímco tabulky a objekty BLOB se používají k ukládání stavů pracovního postupu.

Další informace o cenových modelech, které se vztahují na tento nový typ prostředku, najdete v těchto tématech:

* [Hostování a škálování Azure Functions](../azure-functions/functions-scale.md)
* [Horizontální navýšení kapacity v Azure App Service](../app-service/manage-scale-up.md)
* [Podrobnosti o cenách Azure Functions](https://azure.microsoft.com/pricing/details/functions/)
* [Podrobnosti o cenách App Service](https://azure.microsoft.com/pricing/details/app-service/windows/)
* [Podrobnosti o cenách Azure Storage](https://azure.microsoft.com/pricing/details/storage/)

<a name="unsupported"></a>

## <a name="unavailable-or-unsupported-capabilities"></a>Nedostupné nebo nepodporované možnosti

Pro tuto veřejnou verzi Preview nejsou tyto možnosti k dispozici nebo nejsou podporované:

* Vytvoření nového prostředku **Aplikace logiky (Preview)** není v současné době v MacOS k dispozici.

* Ještě nejsou podporované všechny oblasti Azure. V případě aktuálně dostupných oblastí ověřte [seznam oblastí](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md#available-regions).

* Pokud chcete spustit pracovní postup, použijte [integrovaný požadavek, http, Event Hubs nebo aktivační událost Service Bus](../connectors/apis-list.md), která se spouští nativně s modulem runtime Logic Apps. V současné době se v této verzi Preview v současnosti nepodporují služby [Enterprise Connectors](../connectors/apis-list.md#enterprise-connectors), [místní triggery dat](../connectors/apis-list.md#on-premises-connectors), triggery založené na webhookech, aktivační událost posuvných oken, [vlastní konektory](../connectors/apis-list.md#custom-apis-and-connectors), účty pro integraci, jejich artefakty a [jejich konektory](../connectors/apis-list.md#integration-account-connectors) . Funkce volání funkce Azure je nedostupná, takže teď použijte *akci* http pro volání adresy URL žádosti pro funkci Azure Functions.

  S výjimkou dříve zadaných triggerů můžou *stavové* pracovní postupy používat triggery i akce pro [spravované konektory](../connectors/apis-list.md#managed-api-connectors), které se nasazují v Azure, oproti vestavěným aktivačním událostem a akcím, které se spouštějí nativně s modulem runtime Logic Apps. *Bezstavové* pracovní postupy ale v současné době podporují jenom *Akce* pro spravované konektory, ne triggery. I když můžete v Azure povolit konektory pro bezstavový pracovní postup, Návrhář nezobrazí žádné triggery spravovaného konektoru, které byste mohli vybrat.

* Nový typ prostředku **Aplikace logiky (Preview)** můžete nasadit jenom do [plánu hostování Premium nebo App Service v Azure](#publish-azure) nebo do [kontejneru Docker](#deploy-docker), a ne do [prostředí ISEs (Integration Service Environment)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md). Plány hostování **spotřeby** nejsou podporované ani nejsou k dispozici pro nasazení tohoto typu prostředku.

## <a name="prerequisites"></a>Požadavky

### <a name="access-and-connectivity"></a>Přístup a připojení

* Přístup k Internetu, abyste si mohli stáhnout požadavky, připojit se z Visual Studio Code k vašemu účtu Azure a publikovat z Visual Studio Code do Azure, kontejneru Docker nebo jiného prostředí.

* Účet a předplatné Azure. Pokud předplatné nemáte, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Pokud chcete v tomto článku sestavit stejnou ukázkovou aplikaci logiky, potřebujete e-mailový účet Office 365 Outlook, který pro přihlášení používá pracovní nebo školní účet Microsoft.

  Pokud se rozhodnete použít jiný [e-mailový konektor, který podporuje Azure Logic Apps](/connectors/), jako je například Outlook.com nebo [Gmail](../connectors/connectors-google-data-security-privacy-policy.md), můžete postupovat dál a obecné kroky jsou stejné, ale vaše uživatelské rozhraní a možnosti se můžou v některých případech lišit. Pokud například použijete konektor Outlook.com, použijte místo toho osobní účet Microsoft k přihlášení.

### <a name="storage-requirements"></a>Požadavky na úložiště

1. Stáhněte a nainstalujte [emulátor Azure Storage 5,10](https://go.microsoft.com/fwlink/p/?linkid=717179).

1. Chcete-li spustit emulátor, je nutné mít místní instalaci SQL DB, například bezplatnou [edici SQL Server 2019 Express](https://go.microsoft.com/fwlink/p/?linkid=866658). Další informace najdete v tématu [použití emulátoru Azure Storage pro vývoj a testování](../storage/common/storage-use-emulator.md).

   > [!IMPORTANT]
   > Před otevřením návrháře aplikace logiky pro vytvoření pracovního postupu aplikace logiky nezapomeňte spustit emulátor. V opačném případě se zobrazí zpráva, že `Workflow design time could not be started` .
   >
   > ![Snímek obrazovky, který zobrazuje emulátor Azure Storage spuštěný.](./media/create-stateful-stateless-workflows-visual-studio-code/start-storage-emulator.png)

### <a name="tools"></a>Nástroje

* [Visual Studio Code 1.30.1 (leden 2019) nebo vyšší](https://code.visualstudio.com/), což je zdarma. Také si stáhněte a nainstalujte tyto další nástroje pro Visual Studio Code, pokud je ještě nemáte:

  * [Rozšíření účtu Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account), které poskytuje jedno společné prostředí pro přihlášení a předplatné Azure pro všechna ostatní rozšíření azure v Visual Studio Code.

  * [Rozšíření C# pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp), které umožňuje, aby funkce F5 spouštěla vaši aplikaci logiky.

  * [Azure Functions Core Tools](../azure-functions/functions-run-local.md), ať už verze [3.0.2931](https://github.com/Azure/azure-functions-core-tools/releases/tag/3.0.2931) nebo [2.7.2936](https://github.com/Azure/azure-functions-core-tools/releases/tag/2.7.2936), prostřednictvím instalační služby Microsoft (MSI). Tyto nástroje zahrnují verzi stejného modulu runtime, který vychází z Azure Functions runtime spouštěného v Visual Studio Code.

    > [!IMPORTANT]
    > Pokud máte instalaci, která je starší než tyto verze, nejprve ji odinstalujte nebo zajistěte, aby proměnná prostředí PATH odkazovala na verzi, kterou stáhnete a nainstalujete.
    >
    > Pokud chcete použít [akci **vloženého kódu**](../logic-apps/logic-apps-add-run-inline-code.md) pro spuštění kódu JavaScriptu, je nutné použít Azure Functions běhové verze 3x, protože akce nepodporuje verzi 2x. Tato akce v současnosti není v operačním systému Linux podporována.

  * [Rozšíření Azure Logic Apps (Preview) pro Visual Studio Code](https://go.microsoft.com/fwlink/p/?linkid=2143167). Toto rozšíření Public Preview nabízí možnost vytvářet stavové a bezstavové aplikace logiky a spouštět je místně v Visual Studio Code.

    V současné době můžete v Visual Studio Code nainstalovat jak původní rozšíření **Azure Logic Apps** , tak i nové rozšíření **Azure Logic Apps (Preview)** . Výběrem ikony Azure na panelu nástrojů Visual Studio Code můžete zobrazit všechny aplikace logiky nasazené v Azure, ale každý typ prostředku se zobrazí v jejich vlastní části rozšíření **Logic Apps** a **Azure Logic Apps (Preview)**.

    > [!IMPORTANT]
    > Pokud jste vytvořili Logic Apps pomocí rozšíření **Azure Logic Apps (Private Preview)** , nebudou tyto aplikace logiky fungovat s rozšířením Public Preview. Tyto aplikace logiky ale můžete migrovat tak, že odinstalujete rozšíření Private Preview, provedete požadované vyčištění a nainstalujete rozšíření verze Public Preview. Pak můžete vytvořit nový projekt v Visual Studio Code a zkopírovat dříve vytvořený soubor **. definice pracovního postupu** aplikace logiky do nového projektu.
    >
    > Takže před instalací rozšíření Public Preview se ujistěte, že jste odinstalovali všechny předchozí verze, a tyto artefakty odstraňte:
    >
    > * Složka **Microsoft. Azure. Functions. ExtensionBundle. Workflows** , která obsahuje předchozí sady rozšíření a která se nachází podél cesty:
    >
    >   * `C:\Users\{userName}\AppData\Local\Temp\Functions\ExtensionBundles`
    >
    >   * `C:\Users\{userName}.azure-functions-core-tools\Functions\ExtensionBundles`
    >
    > * Složka **Microsoft. Azure. Workflows. WebJobs. Extension** , což je mezipaměť [NuGet](/nuget/what-is-nuget) pro rozšíření Private Preview a je umístěná na této cestě:
    >
    >   `C:\Users\{userName}\.nuget\packages`

    Chcete-li nainstalovat rozšíření **Azure Logic Apps (Preview)** , postupujte podle následujících kroků:

    1. V Visual Studio Code na levém panelu nástrojů vyberte **rozšíření**.

    1. Do vyhledávacího pole rozšíření zadejte `azure logic apps preview` . V seznamu výsledků vyberte možnost **Azure Logic Apps (Preview)** **>** **instalovat**.

       Po dokončení instalace se rozšíření Public Preview zobrazí v seznamu **rozšíření: nainstalováno** .

       ![Snímek obrazovky, který zobrazuje seznam nainstalovaných rozšíření Visual Studio Code s příponou "Azure Logic Apps (Preview)" byl podtržen.](./media/create-stateful-stateless-workflows-visual-studio-code/azure-logic-apps-extension-installed.png)

* Chcete-li otestovat ukázkovou aplikaci logiky, kterou vytvoříte v tomto článku, budete potřebovat nástroj, který může odeslat volání triggeru žádosti, což je první krok v ukázkové aplikaci logiky. Pokud tento nástroj nemáte, můžete si ho stáhnout, nainstalovat [a používat.](https://www.postman.com/downloads/)

* Pro snazší možnosti protokolování a trasování diagnostiky můžete přidat a použít prostředek [Application Insights](../azure-monitor/app/app-insights-overview.md) . Tento prostředek můžete vytvořit během nasazení aplikace logiky nebo v Azure Portal po nasazení aplikace logiky.

<a name="set-up"></a>

## <a name="set-up-visual-studio-code"></a>Nastavit nástroj Visual Studio Code

1. Abyste se ujistili, že jsou všechna rozšíření správně nainstalovaná, znovu načtěte nebo znovu spusťte Visual Studio Code.

1. Povolte nebo zaškrtněte, pokud Visual Studio Code automaticky najde a nainstaluje aktualizace rozšíření, takže rozšíření Public Preview získá nejnovější aktualizace.

   Pokud chcete toto nastavení ověřit, postupujte následovně:

   1. V nabídce **soubor** přejděte na **Preferences** **>** **Nastavení** předvolby.

   1. Na kartě **uživatel** přejdete na **funkce** **>** **rozšíření**.

   1. Potvrďte, že je vybraná možnost **automaticky zkontrolovat aktualizace** a **Automatická aktualizace** .

1. Povolte nebo ověřte, že nastavení rozšíření **Azure Logic Apps (Preview)** jsou nastavena v Visual Studio Code:

   * **Azure Logic Apps v2: režim panelu**
   * **Azure Logic Apps v2: modul runtime projektu**

   1. V nabídce **soubor** přejděte na **Preferences** **>** **Nastavení** předvolby.

   1. Na kartě **uživatel** přejdete do části **>** **rozšíření** **>** **Azure Logic Apps (Preview)**.

   1. V části **Azure Logic Apps v2: režim panelu** potvrďte, že je vybraná **možnost povolit režim panelů** . V části **Azure Logic Apps v2: modul runtime projektu** nastavte verzi na **~ 3** nebo **~ 2** na základě [Azure Functions Core Tools verze](#prerequisites) , kterou jste předtím nainstalovali.

      > [!IMPORTANT]
      > Pokud chcete použít [akci **vloženého kódu**](../logic-apps/logic-apps-add-run-inline-code.md) pro spuštění kódu JavaScriptu, ujistěte se, že používáte modul runtime projektu verze 3, protože akce nepodporuje verzi 2. Tato akce v současnosti není v operačním systému Linux podporována.

      ![Snímek obrazovky, který zobrazuje Visual Studio Code nastavení pro rozšíření "Azure Logic Apps (Preview)".](./media/create-stateful-stateless-workflows-visual-studio-code/azure-logic-apps-preview-settings.png)

<a name="connect-azure-account"></a>

## <a name="connect-to-your-azure-account"></a>Připojení k účtu Azure

1. Na panelu nástrojů Visual Studio Code vyberte ikonu Azure.

   ![Snímek obrazovky, který zobrazuje Visual Studio Code panel nástrojů a vybranou ikonu Azure](./media/create-stateful-stateless-workflows-visual-studio-code/visual-studio-code-azure-icon.png)

1. V podokně Azure v části **Azure: Logic Apps (Preview)** vyberte **Přihlásit se k Azure**. Když se zobrazí stránka ověřování Visual Studio Code, přihlaste se pomocí svého účtu Azure.

   ![Snímek obrazovky, který zobrazuje podokno Azure a vybraný odkaz pro přihlášení k Azure](./media/create-stateful-stateless-workflows-visual-studio-code/sign-in-azure-subscription.png)

   Po přihlášení se v podokně Azure zobrazí předplatná v účtu Azure. Pokud máte veřejně vydanou Logic Apps rozšíření, můžete najít všechny původní Logic Apps prostředky, které jste vytvořili pomocí původního rozšíření v části **Logic Apps** vydané rozšíření, a to ne Logic Apps rozšíření Preview **(Preview)** .
   
   Pokud se očekávané odběry nezobrazí nebo chcete, aby podokno zobrazovalo pouze konkrétní odběry, postupujte podle těchto kroků:

   1. V seznamu předplatná přesuňte ukazatel vedle prvního předplatného, dokud se nezobrazí tlačítko **Vybrat odběry** (ikona filtru). Vyberte ikonu filtru.

      ![Snímek obrazovky, který zobrazuje podokno Azure a vybraný ikonu filtru.](./media/create-stateful-stateless-workflows-visual-studio-code/filter-subscription-list.png)

      Nebo na stavovém řádku Visual Studio Code vyberte svůj účet Azure. 

   1. Pokud se zobrazí jiný seznam předplatných, vyberte požadované odběry a pak se ujistěte, že jste vybrali **OK**.

<a name="create-project"></a>

## <a name="create-a-local-project"></a>Vytvořit místní projekt

Než budete moct vytvořit aplikaci logiky, vytvořte místní projekt, abyste mohli spravovat a nasazovat aplikaci logiky z Visual Studio Code. Podkladový projekt je podobný projektu Azure Functions, označovaný také jako projekt Function App. Tyto typy projektů jsou však oddělené od sebe navzájem, takže pracovní postupy a funkce logiky aplikace logiky nemohou existovat ve stejném projektu.

1. V počítači vytvořte *prázdnou* místní složku, kterou chcete použít pro projekt, který později vytvoříte v Visual Studio Code.

1. V Visual Studio Code zavřete všechny otevřené složky.

1. V podokně Azure vedle **Azure: Logic Apps (Preview)** vyberte **vytvořit nový projekt** (ikona, která zobrazuje složku a blesk).

   ![Snímek obrazovky, který zobrazuje panel nástrojů podokna Azure se zvoleným možnost vytvořit nový projekt](./media/create-stateful-stateless-workflows-visual-studio-code/create-new-project-folder.png)

1. Pokud vás firewall v programu Windows Defender vyzve k udělení přístupu k síti pro `Code.exe` , což je Visual Studio Code a pro `func.exe` , což je Azure Functions Core Tools, vyberte **privátní sítě, například moje domů nebo pracovní síť** **>** **Povolit přístup**.

1. Přejděte do umístění, kde jste vytvořili složku projektu, vyberte tuto složku a pokračujte.

   ![Snímek obrazovky, který zobrazuje dialogové okno vybrat složku s nově vytvořenou složkou projektu a vybraným tlačítkem vybrat.](./media/create-stateful-stateless-workflows-visual-studio-code/select-project-folder.png)

1. V seznamu šablon, který se zobrazí, vyberte **stavový pracovní postup** nebo **pracovní postup bez stavu**. V tomto příkladu se vybere **stavový pracovní postup**.

   ![Snímek obrazovky, který zobrazuje seznam šablon pracovního postupu s vybraným stavovým pracovním postupem.](./media/create-stateful-stateless-workflows-visual-studio-code/select-stateful-stateless-workflow.png)

1. Zadejte název pracovního postupu aplikace logiky a stiskněte klávesu ENTER. Tento příklad používá `example-workflow` jako název.

   ![Snímek obrazovky s názvem "vytvoření nového stavového pracovního postupu (3/4) a" příklad – pracovní postup "jako název pracovního postupu.](./media/create-stateful-stateless-workflows-visual-studio-code/name-your-workflow.png)

1. V dalším seznamu, který se zobrazí, vyberte **otevřít v aktuálním okně**.

   ![Snímek obrazovky, který zobrazuje seznam s vybraným "otevřít v aktuálním okně".](./media/create-stateful-stateless-workflows-visual-studio-code/select-project-location.png)

   Visual Studio Code se znovu načte, otevře se podokno Průzkumník a zobrazí se váš projekt, který teď obsahuje automaticky generované soubory projektu. Projekt má například složku, která zobrazuje název pracovního postupu aplikace logiky. V této složce **workflow.jsv** souboru obsahuje základní definici JSON vašeho pracovního postupu aplikace logiky.

   ![Snímek obrazovky, který zobrazuje okno Průzkumníka se složkou projektu, složkou pracovního postupu a souborem "workflow.jsna".](./media/create-stateful-stateless-workflows-visual-studio-code/local-project-created.png)

<a name="open-workflow-definition-designer"></a>

## <a name="open-the-workflow-definition-file-in-logic-app-designer"></a>Otevření souboru definice pracovního postupu v návrháři aplikace logiky

1. Ověřte verze, které jsou nainstalovány v počítači spuštěním tohoto příkazu:

   `..\Users\{yourUserName}\dotnet --list-sdks`

   Pokud máte .NET Core SDK 5. x, může vám tato verze zabránit v otevření základní definice pracovního postupu aplikace logiky v návrháři. Místo odinstalace této verze v kořenovém adresáři projektu vytvořte **global.jsv** souboru, který odkazuje na verzi .NET Core Runtime 3. x, kterou máte později než 3.1.201, například:

   ```json
   {
      "sdk": {
         "version": "3.1.8",
         "rollForward": "disable"
      }
   }
   ```

   Ujistěte se, že explicitně přidáte tuto **global.js** do souboru do projektu v kořenovém umístění v rámci Visual Studio Code. V opačném případě se Návrhář neotevře.

1. Pokud je Visual Studio Code spuštěná v systému Windows nebo Linux, ujistěte se, že je spuštěný emulátor Azure Storage. Další informace najdete v části [požadavky](#prerequisites).

1. Rozbalte složku projektu pro váš pracovní postup. Otevřete místní nabídku souboru **workflow.js** a v **Návrháři vyberte otevřít**.

   ![Snímek obrazovky, který zobrazuje podokno Průzkumníka a okno zástupce pro workflow.jsv souboru s vybranou možnost otevřít v Návrháři](./media/create-stateful-stateless-workflows-visual-studio-code/open-definition-file-in-designer.png)

   Pokud se zobrazí chybová zpráva s oznámením `Workflow design time could not be started` , ujistěte se, že je spuštěný emulátor Azure Storage. V opačném případě zkuste tyto další návrhy řešení potíží:

   V Visual Studio Code se podívejte na výstup z rozšíření Preview.

   1. V nabídce **zobrazení** vyberte možnost **výstup**.

   1. V seznamu v záhlaví **výstupu** vyberte **Azure Logic Apps** , abyste mohli zobrazit výstup pro rozšíření verze Preview, například:

      ![Snímek obrazovky zobrazující okno výstup Visual Studio Code s vybraným "Azure Logic Apps".](./media/create-stateful-stateless-workflows-visual-studio-code/check-outout-window-azure-logic-apps.png)

   1. Zkontrolujte výstup a zkontrolujte, zda se zobrazí tato chybová zpráva:

      ```text
      A host error has occurred during startup operation '{operationID}'.
      System.Private.CoreLib: The file 'C:\Users\{userName}\AppData\Local\Temp\Functions\
      ExtensionBundles\Microsoft.Azure.Functions.ExtensionBundle.Workflows\1.1.1\bin\
      DurableTask.AzureStorage.dll' already exists.
      Value cannot be null. (Parameter 'provider')
      Application is shutting down...
      Initialization cancellation requested by runtime.
      Stopping host...
      Host shutdown completed.
      ```

      K této chybě může dojít, pokud jste se dříve pokusili otevřít návrháře a potom tento projekt zrušit nebo odstranit. Tuto chybu můžete vyřešit tak, že odstraníte složku **ExtensionBundles** v tomto umístění **. ..\Users \\ {Your-username} \AppData\Local\Temp\Functions\ExtensionBundles** a znovu otevřete **workflow.js** v souboru v návrháři.

1. V seznamu **Povolit konektory v Azure** vyberte **použít konektory z Azure**, které se vztahují na všechny spravované konektory, které jsou k dispozici a nasazené v Azure, nejen konektory pro služby Azure.

   ![Snímek obrazovky zobrazující podokno Průzkumník se seznamem povolit konektory v Azure a vybrat možnost použít konektory z Azure](./media/create-stateful-stateless-workflows-visual-studio-code/use-connectors-from-azure.png)

   > [!NOTE]
   > Bezstavové pracovní postupy aktuálně podporují jenom *Akce* pro [spravované konektory](../connectors/apis-list.md#managed-api-connectors), které jsou nasazené v Azure, a ne triggery. I když máte možnost povolit konektory v Azure pro bezstavový pracovní postup, Návrhář nezobrazí žádné triggery spravovaného konektoru, které byste mohli vybrat.

1. V seznamu skupiny prostředků vyberte **vytvořit novou skupinu prostředků**.

   ![Snímek obrazovky, který zobrazuje podokno Průzkumník se seznamem skupin prostředků a výběr možnosti vytvořit novou skupinu prostředků](./media/create-stateful-stateless-workflows-visual-studio-code/create-select-resource-group.png)

1. Zadejte název skupiny prostředků a stiskněte klávesu ENTER. Tento příklad používá `example-logic-app-preview-rg` .

   ![Snímek obrazovky, který zobrazuje podokno Průzkumníka a název skupiny prostředků.](./media/create-stateful-stateless-workflows-visual-studio-code/enter-name-for-resource-group.png)

1. V seznamu umístění vyhledejte a vyberte [podporovanou oblast Azure](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md#available-regions) , která se má použít k vytvoření skupiny prostředků a prostředků. Tento příklad používá **středozápadní USA**.

   > [!IMPORTANT]
   > Ne všechny oblasti jsou momentálně podporované, ale aktualizace pro přidání dalších oblastí probíhají. Výběr nepodporované oblasti může mít za následek problémy, jako je vytváření připojení. V případě aktuálně podporovaných oblastí si Projděte [stránku GitHub se známými problémy s](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md#available-regions)rozšířením Preview.

   ![Snímek obrazovky, který zobrazuje podokno Průzkumník se seznamem umístění a vybraným "Středozápadní USA".](./media/create-stateful-stateless-workflows-visual-studio-code/select-azure-region.png)

   Po provedení tohoto kroku se Visual Studio Code otevře Návrhář aplikace logiky.

   > [!NOTE]
   > Když Visual Studio Code spustí rozhraní API pro dobu návrhu pracovního postupu, zobrazí se zpráva, že spuštění může trvat několik sekund. Tuto zprávu můžete ignorovat nebo vyberte **OK**.

   Po zobrazení návrháře aplikace logiky se v Návrháři zobrazí příkazový řádek **Zvolit operaci** a ve výchozím nastavení je vybraný, což zobrazuje podokno **přidat akci** .

   ![Snímek obrazovky, který zobrazuje návrháře aplikace logiky](./media/create-stateful-stateless-workflows-visual-studio-code/workflow-app-designer.png)

1. Potom do pracovního postupu [přidejte Trigger a akce](#add-trigger-actions) .

<a name="add-trigger-actions"></a>

## <a name="add-a-trigger-and-actions"></a>Přidat aktivační událost a akce

Po otevření návrháře aplikace logiky z místní nabídky **workflow.jsv** místní nabídce souboru se v Návrháři zobrazí výzva **Zvolit operaci** a ve výchozím nastavení je vybraná. Nyní můžete začít vytvářet pracovní postup přidáním triggeru a akcí.

Pracovní postup aplikace logiky v tomto příkladu používá tuto aktivační událost a tyto akce:

* Vestavěná [aktivační událost žádosti](../connectors/connectors-native-reqres.md), **když se přijme požadavek HTTP**, který přijímá příchozí volání nebo požadavky a vytvoří koncový bod, který může volat jiné služby nebo Logic Apps.

* **Odeslání e-mailu** na [akci Office 365 Outlooku](../connectors/connectors-create-api-office365-outlook.md)

* Integrovaná [Akce odpovědi](../connectors/connectors-native-reqres.md), kterou použijete k odeslání odpovědi a vrácení dat zpět volajícímu.

### <a name="add-the-request-trigger"></a>Přidat aktivační událost žádosti

1. Vedle návrháře v podokně **Přidat aktivační událost** zaškrtněte v poli **zvolit hledání operace** možnost **předdefinované** , abyste mohli vybrat Trigger, který se spustí nativně.

1. Do vyhledávacího pole **Zvolte operaci** zadejte `when a http request` a vyberte vestavěný aktivační událost žádosti, která se pojmenuje **při přijetí požadavku HTTP**.

   ![Snímek obrazovky, který zobrazuje návrháře aplikace logiky a * * přidat aktivační událost * * s názvem při přijetí požadavku HTTP](./media/create-stateful-stateless-workflows-visual-studio-code/add-request-trigger.png)

   Když se aktivační událost objeví v návrháři, otevře se podokno podrobností triggeru, ve kterém se zobrazí vlastnosti triggeru, nastavení a další akce.

   ![Snímek obrazovky, který zobrazuje návrháře aplikace logiky se zvolenou aktivační událostí když je přijat požadavek HTTP, a otevřené podokno podrobností triggeru](./media/create-stateful-stateless-workflows-visual-studio-code/request-trigger-added-to-designer.png)

   > [!TIP]
   > Pokud se podokno podrobností nezobrazí, ujistěte se, že je v Návrháři vybraný Trigger.

1. Pokud potřebujete položku v Návrháři odstranit, postupujte takto:

   1. V návrháři vyberte položku, která otevře podokno podrobností položky na pravé straně.

   1. Zvětšete okno Visual Studio Code dostatečně široké, aby se zobrazilo tlačítko se třemi tečkami (**...**) v pravém horním rohu. 

   1. Otevřete nabídku se třemi tečkami (**...**) a vyberte **Odstranit**. Odstranění potvrďte tak, že vyberete **OK**.

      ![Snímek obrazovky zobrazující vybranou položku v Návrháři s podoknem otevřít podokno podrobností a tlačítkem s vybranými třemi tečkami a možností odstranit](./media/create-stateful-stateless-workflows-visual-studio-code/delete-item-from-designer.png)

### <a name="add-the-office-365-outlook-action"></a>Přidat akci sady Office 365 Outlook

1. V návrháři v rámci triggeru, který jste přidali, vyberte **Nový krok**.

   V návrháři se zobrazí výzva **Zvolit operaci** a otevře se **podokno přidat akci** , ve kterém můžete vybrat další akci.

1. V podokně **přidat akci** vyberte v poli pro hledání **operace** možnost **Azure** , abyste mohli najít a vybrat akci pro spravovaný konektor, který je nasazený v Azure.

   Tento příklad vybere a použije akci Office 365 Outlook, **pošle e-mail (v2)**.

   ![Snímek obrazovky, který zobrazuje návrháře aplikace logiky a * * přidat akci * * podokno s vybranou akcí Office 365 Outlooku odeslat e-mail](./media/create-stateful-stateless-workflows-visual-studio-code/add-send-email-action.png)

1. V podokně podrobností akce vyberte **Přihlásit** , abyste mohli vytvořit připojení k e-mailovému účtu.

   ![Snímek obrazovky, který zobrazuje návrháře aplikace logiky a * * odeslat e-mail (v2) * * podokno s vybraným možnost přihlásit](./media/create-stateful-stateless-workflows-visual-studio-code/send-email-action-sign-in.png)

   > [!NOTE]
   > Pokud se zobrazí chyba, `Failed to create connection...` Možná jste vybrali aktuálně nepodporovanou oblast pro vaši aplikaci logiky. Aktualizace pro přidání dalších oblastí probíhají. Zatím pro aktuálně podporované oblasti si Projděte [stránku GitHub se známými problémy s](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md#available-regions)rozšířením Preview.

1. Když Visual Studio Code zobrazí výzvu k zadání souhlasu pro přístup k e-mailovému účtu, vyberte **otevřít**.

   ![Snímek obrazovky zobrazující Visual Studio Code výzvy k povolení přístupu](./media/create-stateful-stateless-workflows-visual-studio-code/visual-studio-code-open-external-website.png)

   > [!TIP]
   > Pokud chcete zabránit budoucím výzvám, vyberte **Konfigurovat důvěryhodné domény** , abyste mohli přidat ověřovací stránku jako důvěryhodnou doménu.

1. Postupujte podle dalších výzev, abyste se přihlásili, povolili přístup a mohli vracet Visual Studio Code.

   > [!NOTE]
   > Pokud se před dokončením výzev příliš neprojde, vyprší časový limit procesu ověřování a dojde k chybě. V takovém případě se vraťte do návrháře a znovu se přihlaste, aby se vytvořilo připojení.

1. Jakmile se rozšíření Azure Logic Apps Preview vyzve k zadání souhlasu pro přístup k e-mailovému účtu, vyberte **otevřít**. Pomocí další výzvy povolte přístup.

   ![Snímek obrazovky, který zobrazuje výzvu k rozšíření náhledu pro povolení přístupu](./media/create-stateful-stateless-workflows-visual-studio-code/allow-preview-extension-open-uri.png)

   > [!TIP]
   > Chcete-li zabránit budoucím výzvám, vyberte **pro toto rozšíření nedotazování znovu**.

   Po Visual Studio Code vytvoří připojení některé konektory zobrazí zprávu s `The connection will be valid for {n} days only.` tímto časovým limitem, která se vztahuje pouze na dobu trvání při vytváření aplikace logiky v Visual Studio Code. Po nasazení už toto omezení neplatí, protože aplikace logiky se může ověřit za běhu pomocí své automaticky povolené [spravované identity přiřazené systémem](../logic-apps/create-managed-service-identity.md). Tato spravovaná identita se liší od přihlašovacích údajů k ověřování nebo připojovacího řetězce, který používáte při vytváření připojení. Pokud tuto spravovanou identitu přiřazenou systémem zakážete, nebudou připojení fungovat za běhu.

1. Pokud se v Návrháři nezobrazí vybraná akce **Odeslat e-mail** , vyberte tuto akci.

1. V podokně podrobností akce zadejte na kartě **parametry** požadované informace pro akci, například:

   ![Snímek obrazovky, který zobrazuje návrháře aplikace logiky s podrobnostmi o akci odeslání e-mailu pro Office 365 Outlook](./media/create-stateful-stateless-workflows-visual-studio-code/send-email-action-details.png)

   | Vlastnost | Požaduje se | Hodnota | Popis |
   |----------|----------|-------|-------------|
   | **Schopn** | Yes | <*vaše e-mailová adresa*> | Příjemce e-mailu, který může být vaše e-mailová adresa pro testovací účely. V tomto příkladu se používá fiktivní e-mail, `sophiaowen@fabrikam.com` . |
   | **Předmět** | Yes | `An email from your example workflow` | Předmět e-mailu |
   | **Text** | Yes | `Hello from your example workflow!` | Obsah těla e-mailu |
   ||||

   > [!NOTE]
   > Chcete-li provést jakékoli změny v podokně podrobností na kartě **Nastavení**, **Spustit po** nebo **statického výsledku** , ujistěte se, že jste vybrali možnost **Hotovo** , aby byly tyto změny potvrzeny před přepnutím tabulátorů nebo změnou fokusu na návrháře. Jinak Visual Studio Code změny nezůstanou. Další informace najdete na [stránce GitHub známé problémy s](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md)rozšířením Preview.

1. V návrháři vyberte Save ( **Uložit**).

V dalším kroku spouštějte a ladíte pracovní postup místně v Visual Studio Code.

<a name="debug-test-locally"></a>

## <a name="run-and-debug-locally"></a>Místní spuštění a ladění

Chcete-li otestovat aplikaci logiky, postupujte podle těchto kroků a vyhledejte adresu URL koncového bodu, který je vytvořen triggerem požadavku. Tuto adresu URL budete potřebovat, abyste později mohli odeslat žádost do tohoto koncového bodu.

1. Pokud chcete snadněji ladit pracovní postup aplikace logiky bezstavových aplikací, můžete [pro tento pracovní postup povolit historii spuštění](#run-history).

1. Na panelu nástrojů Visual Studio Code otevřete nabídku **Spustit** a vyberte **Spustit ladění** (F5).

   Otevře se okno **terminálu** , ve kterém můžete zkontrolovat relaci ladění.

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

   | Stav spuštění | Popis |
   |------------|-------------|
   | **Bylo přerušeno** | Spuštění bylo zastaveno nebo nebylo dokončeno z důvodu externích problémů, například výpadek systému nebo uplynulé předplatné Azure. |
   | **Stornován** | Běh se aktivoval a začal, ale přijal žádost o zrušení. |
   | **Neúspěšný** | Nejméně jedna akce v běhu se nezdařila. Pro zpracování této chyby nebyly nastaveny žádné následné akce v pracovním postupu. |
   | **Spuštěný** | Běh se aktivoval a probíhá, ale tento stav se může zobrazit i pro běh, který je omezený z důvodu [omezení akce](logic-apps-limits-and-config.md) nebo [aktuálního cenového plánu](https://azure.microsoft.com/pricing/details/logic-apps/). <p><p>**Tip**: Pokud nastavíte [diagnostické protokolování](monitor-logic-apps-log-analytics.md), můžete získat informace o všech událostech omezení, ke kterým dochází. |
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

   | Stav akce | Ikona | Popis |
   |---------------|------|-------------|
   | Bylo přerušeno | ![Ikona pro stav akce přerušeno][aborted-icon] | Akce se zastavila nebo nedokončila z důvodu externích problémů, například výpadek systému nebo uplynulé předplatné Azure. |
   | Cancelled | ![Ikona pro stav akce zrušeno][cancelled-icon] | Akce byla spuštěna, ale přijala žádost o zrušení. |
   | Neúspěšný | ![Ikona pro stav akce "neúspěch"][failed-icon] | Akce se nezdařila. |
   | Spuštěný | ![Ikona pro spuštěný stav akce][running-icon] | Tato akce je aktuálně spuštěná. |
   | Přeskočeno | ![Ikona pro stav akce přeskočeno][skipped-icon] | Akce byla přeskočena, protože bezprostředně předchozí akce se nezdařila. Akce má `runAfter` podmínku, která vyžaduje, aby předchozí akce byla úspěšně dokončena předtím, než bude možné spustit aktuální akci. |
   | Úspěšný | ![Ikona stavu akce "úspěch"][succeeded-icon] | Akce byla úspěšná. |
   | Úspěch s opakováním | ![Ikona pro stav akce úspěšné a opakované pokusy][succeeded-with-retries-icon] | Akce byla úspěšná, ale jenom po jednom nebo několika opakovaných pokusech. Chcete-li zkontrolovat historii opakování, v zobrazení podrobností historie spuštění vyberte tuto akci, aby bylo možné zobrazit vstupy a výstupy. |
   | Vypršel časový limit | ![Ikona pro stav akce vypršel časový limit][timed-out-icon] | Akce byla zastavena z důvodu vypršení časového limitu zadaného nastavením této akce. |
   | Čekající | ![Ikona pro stav "čeká na akci"][waiting-icon] | Platí pro akci Webhooku, která čeká na příchozí požadavek od volajícího. |
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

## <a name="return-a-response-to-the-caller"></a>Vrátit odpověď volajícímu

Pokud chcete vrátit odpověď zpět volajícímu, který odeslal požadavek do vaší aplikace logiky, můžete použít integrovanou [akci reakce](../connectors/connectors-native-reqres.md) pro pracovní postup, který začíná triggerem požadavku.

1. V návrháři aplikace logiky v akci **Odeslat e-mail** vyberte **Nový krok**.

   V návrháři se zobrazí výzva **Zvolit operaci** a otevře se **podokno přidat akci** , ve kterém můžete vybrat další akci.

1. V podokně **přidat akci** zaškrtněte v poli **Vybrat** vyhledávací pole, zda je vybrána možnost **předdefinované** . Do vyhledávacího pole zadejte `response` a vyberte akci **reakce** .

   ![Snímek obrazovky, který zobrazuje návrháře aplikace logiky s vybranou akcí Response](./media/create-stateful-stateless-workflows-visual-studio-code/add-response-action.png)

   Po zobrazení akce **odpověď** v návrháři se automaticky otevře podokno podrobností akce.

   ![Snímek obrazovky, který zobrazuje návrháře aplikace logiky s otevřeným podoknem "odezva" a vlastnost "tělo" je nastavena na hodnotu vlastnosti "tělo" akce "Odeslat e-mail".](./media/create-stateful-stateless-workflows-visual-studio-code/response-action-details.png)

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

Po provedení aktualizací aplikace logiky můžete spustit další test tak, že znovu spustíte ladicí program v aplikaci Visual Studio a odešlete další požadavek na aktivaci aktualizované aplikace logiky, podobně jako v části [ladění a testování aplikace logiky](#debug-test-locally).

1. Na panelu nástrojů Visual Studio Code otevřete nabídku **Spustit** a vyberte **Spustit ladění** (F5).

1. V příspěvku nebo nástroji pro vytváření a odesílání žádostí odešlete další požadavek na aktivaci pracovního postupu.

1. Pokud jste vytvořili stavový pracovní postup, můžete na stránce Přehled pracovního postupu zjistit stav posledního spuštění. Chcete-li zobrazit stav, vstupy a výstupy pro každý krok v daném spuštění, vyberte tlačítko se třemi tečkami (**...**) pro toto spuštění a vyberte možnost **Zobrazit běh**.

   Zde je například podrobný stav pro spuštění po aktualizaci ukázkového pracovního postupu s akcí Response.

   ![Snímek obrazovky, který zobrazuje stav každého kroku v aktualizovaném pracovním postupu plus vstupy a výstupy v rozšířené akci "Response".](./media/create-stateful-stateless-workflows-visual-studio-code/run-history-details-rerun.png)

1. Chcete-li zastavit relaci ladění, v nabídce **Spustit** vyberte možnost **Zastavit ladění** (Shift + F5).

<a name="publish-azure"></a>

## <a name="publish-to-azure"></a>Publikování do Azure

Z Visual Studio Code můžete nasadit projekt přímo do Azure, který publikuje vaši aplikaci logiky pomocí nového typu prostředku **Aplikace logiky (Preview)** . Podobně jako u prostředku Function App v Azure Functions nasazení pro tento nový typ prostředku vyžaduje, abyste vybrali [plán hostování a cenovou úroveň](../app-service/overview-hosting-plans.md), které můžete nastavit během nasazování. Další informace o plánech hostování a cenách najdete v těchto tématech:

* [Horizontální navýšení kapacity v Azure App Service](../app-service/manage-scale-up.md)
* [Hostování a škálování Azure Functions](../azure-functions/functions-scale.md)

Aplikaci logiky můžete publikovat jako nový prostředek, který automaticky vytvoří další nezbytné prostředky, například [účet Azure Storage, podobně jako požadavky na aplikace funkcí](../azure-functions/storage-considerations.md). Nebo můžete aplikaci logiky publikovat do dříve nasazeného prostředku **Aplikace logiky (Preview)** , který přepíše tuto aplikaci logiky.

### <a name="publish-as-a-new-logic-app-preview-resource"></a>Publikování jako nového prostředku aplikace logiky (Preview)

1. Na panelu nástrojů Visual Studio Code vyberte ikonu Azure.

1. Na panelu nástrojů v podokně **Azure: Logic Apps (Preview)** vyberte **nasadit do aplikace logiky**.

   ![Snímek obrazovky s vybraným panelem "Azure: Logic Apps (Preview)" na panelu nástrojů a v podokně s názvem nasadit do aplikace logiky](./media/create-stateful-stateless-workflows-visual-studio-code/deploy-to-logic-app.png)

1. Ze seznamu, který Visual Studio Code otevře, vyberte z těchto možností:

   * **Vytvoření nové aplikace logiky (Preview) v Azure** (rychlá)
   * **Vytvoření nové aplikace logiky (Preview) v Azure Advanced**
   * Dříve nasazený prostředek **Aplikace logiky (Preview)** , pokud existuje

   Tento příklad pokračuje s možností **vytvořit novou aplikaci logiky (Preview) v Azure Advanced**.

   ![Snímek obrazovky, který zobrazuje podokno "Azure: Logic Apps (Preview)" se seznamem, ve kterém je vybraná možnost vytvořit novou aplikaci logiky (Preview) v Azure.](./media/create-stateful-stateless-workflows-visual-studio-code/select-create-logic-app-options.png)

1. K vytvoření nového prostředku **Aplikace logiky (Preview)** použijte následující postup:

   1. Zadejte globálně jedinečný název nové aplikace logiky, což je název, který se má použít pro prostředek **Aplikace logiky (Preview)** . Tento příklad používá `example-logic-app-preview` .

      ![Snímek obrazovky, který zobrazuje podokno "Azure: Logic Apps (Preview)" a výzvu k zadání názvu nové aplikace logiky, která se má vytvořit.](./media/create-stateful-stateless-workflows-visual-studio-code/enter-logic-app-name.png)

   1. Vyberte plán hostování pro novou aplikaci logiky, buď [**App Service plán**](../azure-functions/functions-scale.md#app-service-plan) nebo [**Premium**](../azure-functions/functions-scale.md#premium-plan). Tento příklad vybírá **App Service plán**.

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

   1. Pro snazší možnosti protokolování a trasování diagnostiky můžete vybrat existující prostředek Application Insights. V opačném případě můžete vybrat **vytvořit nový Application Insights prostředek** nebo nastavit Application Insights v Azure Portal po nasazení aplikace.

      Před nasazením se ujistěte, že přidáte `logLevel` objekt k `logging` objektu v **host.js** souboru, který existuje na kořenové úrovni vašeho projektu, a nastavte na `Host.Triggers.Workflow` , například `Information` :

      ```json
      "logLevel": {
         "Host.Triggers.Workflow": "Information"
      },
      ```

      Tady je postup, jak může **host.jsv** souboru vypadat:

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

   Až skončíte, Visual Studio Code začne vytvářet a nasazovat prostředky nezbytné pro publikování aplikace logiky.

1. Chcete-li zkontrolovat a monitorovat proces nasazení, v nabídce **zobrazení** vyberte možnost **výstup**. V seznamu panelu nástrojů okna výstup vyberte možnost **Azure Logic Apps**.

   ![Snímek obrazovky zobrazující okno výstup s názvem "Azure Logic Apps" vybraným v seznamu panelu nástrojů spolu s průběhem a stavy nasazení.](./media/create-stateful-stateless-workflows-visual-studio-code/logic-app-deployment-output-window.png)

   Když Visual Studio Code dokončí nasazení pracovního postupu aplikace logiky do Azure, zobrazí se tato zpráva:

   ![Snímek obrazovky, který zobrazuje zprávu, že nasazení do Azure bylo úspěšně dokončeno.](./media/create-stateful-stateless-workflows-visual-studio-code/deployment-to-azure-completed.png)

   Blahopřejeme, vaše aplikace logiky je teď v Azure živá a ve výchozím nastavení povolená.

V dalším kroku se dozvíte, jak provádět tyto úlohy:

* [Najděte nasazenou aplikaci logiky v Azure Portal](#find-manage-deployed-workflows-portal) nebo [v Visual Studio Code](#find-manage-deployed-workflows-vs-code).

* [Povolí historii spuštění v pracovních postupech aplikace logiky bez stavů](#run-history).

* [Povolí monitorování nasazeného prostředku **Aplikace logiky (Preview)**](#enable-monitoring).

<a name="find-manage-deployed-workflows-vs-code"></a>

## <a name="find-and-manage-deployed-logic-apps-in-visual-studio-code"></a>Hledání a Správa nasazených aplikací logiky v Visual Studio Code

V Visual Studio Code můžete zobrazit všechny nasazené aplikace logiky ve vašem předplatném Azure, ať už se jedná o původní **Logic Apps** nebo typ prostředku **Aplikace logiky (Preview)** , a vybrat úkoly, které vám pomůžou tyto aplikace logiky spravovat. Pro přístup k oběma typům prostředků ale potřebujete rozšíření **Azure Logic Apps** i **Azure Logic Apps (Preview)** pro Visual Studio Code.

1. Na levém panelu nástrojů vyberte ikonu Azure. V podokně **Azure: Logic Apps (Preview)** rozbalte své předplatné, ve kterém se zobrazí všechny nasazené Logic Apps pro toto předplatné.

1. Vyhledejte a vyberte aplikaci logiky, kterou chcete spravovat. Otevřete místní nabídku aplikace logiky a vyberte úlohu, kterou chcete provést.

   Můžete například vybrat úkoly, jako je zastavení, spuštění, restartování nebo odstranění nasazené aplikace logiky.

   ![Snímek obrazovky, který zobrazuje Visual Studio Code s otevřeným podoknem rozšíření Azure Logic Apps (Preview) a nasazeným pracovním postupem.](./media/create-stateful-stateless-workflows-visual-studio-code/find-deployed-workflow-visual-studio-code.png)

1. Pokud chcete zobrazit všechny pracovní postupy v aplikaci logiky, rozbalte svou aplikaci logiky a potom rozbalte uzel **pracovní postupy** .

1. Pokud chcete zobrazit konkrétní pracovní postup, otevřete místní nabídku pracovního postupu a vyberte **otevřít v Návrháři**, který otevře pracovní postup v režimu jen pro čtení.

   Chcete-li upravit pracovní postup, máte tyto možnosti:

   * V Visual Studio Code otevřete **workflow.js** projektu v souboru v návrháři aplikace logiky, proveďte úpravy a znovu nasaďte svoji aplikaci logiky do Azure.

   * V Azure Portal [Najděte a otevřete aplikaci logiky](#find-manage-deployed-workflows-portal). Vyhledejte, upravte a uložte pracovní postup.

1. Pokud chcete otevřít nasazenou aplikaci logiky v Azure Portal, otevřete místní nabídku aplikace logiky a vyberte **otevřít na portálu**.

   Azure Portal se otevře v prohlížeči, přihlásí se k portálu automaticky, pokud jste přihlášeni k Visual Studio Code a zobrazíte vaši aplikaci logiky.

   ![Snímek obrazovky zobrazující stránku Azure Portal pro vaši aplikaci logiky v Visual Studio Code](./media/create-stateful-stateless-workflows-visual-studio-code/deployed-workflow-azure-portal.png)

   Můžete se také přihlásit samostatně k Azure Portal, použít vyhledávací pole na portálu k vyhledání aplikace logiky a pak vybrat vaši aplikaci logiky ze seznamu výsledků.

   ![Snímek obrazovky zobrazující Azure Portal a panel hledání s výsledky hledání nasazené aplikace logiky, která se zobrazí jako vybraná.](./media/create-stateful-stateless-workflows-visual-studio-code/find-deployed-workflow-azure-portal.png)

<a name="find-manage-deployed-workflows-portal"></a>

## <a name="find-and-manage-deployed-logic-apps-in-the-portal"></a>Hledání a Správa nasazených aplikací logiky na portálu

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

   Otevře se Návrhář aplikace logiky a zobrazí se pracovní postup, který jste vytvořili v Visual Studio Code. V Azure Portal teď můžete provádět změny tohoto pracovního postupu.

   ![Snímek obrazovky, který ukazuje návrháře aplikace logiky a pracovní postup nasazený z Visual Studio Code.](./media/create-stateful-stateless-workflows-visual-studio-code/opened-workflow-designer.png)

<a name="add-workflows"></a>

## <a name="add-a-workflow-to-deployed-logic-apps"></a>Přidání pracovního postupu do nasazených aplikací logiky

Prostřednictvím Azure Portal můžete přidat prázdné pracovní postupy do prostředku **Aplikace logiky (Preview)** , který jste nasadili z Visual Studio Code a sestavovat tyto pracovní postupy v Azure Portal.

1. V [Azure Portal](https://portal.azure.com)vyhledejte a vyberte nasazený prostředek **Aplikace logiky (Preview)** .

1. V nabídce aplikace logiky vyberte **pracovní postupy**. V podokně **pracovní postupy** vyberte **Přidat**.

   ![Snímek obrazovky, který zobrazuje podokno pracovní postupy a panel nástrojů vybrané aplikace logiky s vybraným příkazem přidat](./media/create-stateful-stateless-workflows-visual-studio-code/add-new-workflow.png)

1. V podokně **nový pracovní postup** zadejte název pracovního postupu. Vyberte **stavové** nebo **stavové** **>** **Vytvoření**.

   Až Azure nasadí nový pracovní postup, který se zobrazí v podokně **pracovní postupy** , vyberte tento pracovní postup, abyste mohli provádět správu a další úkoly, jako je například otevření návrháře aplikace logiky nebo zobrazení kódu.

   ![Snímek obrazovky zobrazující vybraný pracovní postup s možnostmi správy a revize](./media/create-stateful-stateless-workflows-visual-studio-code/view-new-workflow.png)

   Například při otevření návrháře pro nový pracovní postup se zobrazí prázdné plátno. Tento pracovní postup teď můžete vytvořit v Azure Portal.

   ![Snímek obrazovky zobrazující návrháře aplikace logiky a prázdný pracovní postup](./media/create-stateful-stateless-workflows-visual-studio-code/opened-blank-workflow-designer.png)

<a name="run-history"></a>

## <a name="run-history-for-stateless-logic-app-workflows"></a>Historie spuštění pro bezstavové pracovní postupy aplikace logiky

Chcete-li snadněji ladit pracovní postup aplikace s bezstavovou logikou, můžete pro tento pracovní postup povolit historii spuštění buď v Visual Studio Code, nebo v Azure Portal. po dokončení pak zakažte historii spuštění.

### <a name="for-a-stateless-logic-app-workflow-in-visual-studio-code"></a>Pro nestavový pracovní postup aplikace logiky v Visual Studio Code

Pokud pracujete se spuštěným pracovním postupem bezstavové aplikace logiky v Visual Studio Code, postupujte podle následujících kroků:

1. V projektu vyhledejte a rozbalte složku **Workflow-DesignTime** . Vyhledejte a otevřete **local.settings.jsv** souboru.

1. Přidejte `Workflows.{yourWorkflowName}.operationOptions` vlastnost a nastavte hodnotu na `WithStatelessRunHistory` , například:

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

1. Pokud chcete zakázat historii spuštění po dokončení, buď odstraňte `Workflows.{yourWorkflowName}.OperationOptions` vlastnost a její hodnotu, nebo nastavte vlastnost na `None` .

### <a name="for-a-stateless-logic-app-workflow-in-the-azure-portal"></a>Pro nestavový pracovní postup aplikace logiky v Azure Portal

Pokud jste projekt již nasadili do Azure Portal, postupujte takto:

1. V [Azure Portal](https://portal.azure.com)vyhledejte a otevřete prostředek **Aplikace logiky (Preview)** .

1. V nabídce aplikace logiky v části **Nastavení** vyberte **Konfigurace**.

1. Na kartě **nastavení aplikace** vyberte možnost **nové nastavení aplikace**.

1. V podokně **Přidat nebo upravit nastavení aplikace** v poli **název** zadejte název možnosti této operace: 

   `Workflows.{yourWorkflowName}.OperationOptions`

1. Do pole **hodnota** zadejte následující hodnotu: `WithStatelessRunHistory`

   Například:

   ![Snímek obrazovky, který zobrazuje prostředek Azure Portal a aplikace logiky (Preview) s názvem "konfigurace" > "nastavení nového aplikace" < otevře podokno přidat/upravit nastavení aplikace, a pracovní postupy. {yourWorkflowName}. Možnost OperationOptions je nastavena na hodnotu "WithStatelessRunHistory".](./media/create-stateful-stateless-workflows-visual-studio-code/stateless-operation-options-run-history.png)

1. Až to bude hotové, vyberte **OK**. V podokně **Konfigurace** vyberte **Uložit**.

Pokud chcete povolit monitorování nasazeného prostředku aplikace logiky (Preview), pokračujte k další části.

<a name="enable-monitoring"></a>

## <a name="enable-monitoring-for-deployed-logic-app-preview-resources"></a>Povolit monitorování nasazených prostředků aplikace logiky (Preview)

Pokud chcete povolit monitorování nasazeného prostředku **Aplikace logiky (Preview)** , postupujte následovně:

1. V [Azure Portal](https://portal.azure.com)vyhledejte a vyberte nasazený prostředek **Aplikace logiky (Preview)** .

1. V nabídce tohoto prostředku v části **rozhraní API** vyberte **CORS**.

1. V podokně **CORS** v části **Povolené zdroje** přidejte zástupný znak (*).

1. Až budete hotovi, vyberte na panelu nástrojů **CORS** možnost **Uložit**.

   ![Snímek obrazovky zobrazující Azure Portal s nasazeným prostředkem Logic Apps (Preview). V nabídce prostředek se vybere "CORS" s novou položkou "povolené zdroje" nastavenou na zástupný znak "*".](./media/create-stateful-stateless-workflows-visual-studio-code/enable-run-history-deployed-logic-app.png)

<a name="deploy-docker"></a>

## <a name="deploy-to-docker-container"></a>Nasadit do kontejneru Docker

Pomocí [nástroje rozhraní příkazového řádku (CLI) .NET Core](/dotnet/core/tools/)můžete sestavit projekt a potom své sestavení publikovat. Pak můžete sestavit a použít [kontejner Docker](/visualstudio/docker/tutorials/docker-tutorial#what-is-a-container) jako cíl pro nasazení pracovního postupu aplikace logiky. Další informace najdete v těchto tématech:

* [Úvod ke kontejnerům a Dockeru](/dotnet/architecture/microservices/container-docker-introduction/)
* [Úvod k .NET a Dockeru](/dotnet/core/docker/introduction)
* [Terminologie Dockeru](/dotnet/architecture/microservices/container-docker-introduction/docker-terminology)
* [Kurz: Začínáme s Docker](/visualstudio/docker/tutorials/docker-tutorial)

1. Chcete-li sestavit projekt, otevřete příkazový řádek a spusťte tento příkaz:

   `dotnet build -c release`

   Další informace naleznete na referenční stránce [sestavení dotnet](/dotnet/core/tools/dotnet-build/) .

1. Publikujte sestavení spuštěním tohoto příkazu:

   `dotnet publish -c release`

   Další informace najdete na stránce s referenčními informacemi pro [dotnet Publish](/dotnet/core/tools/dotnet-publish/) .

1. Vytvořte kontejner Docker pomocí souboru Docker pro pracovní postup .NET a spusťte tento příkaz:

   `docker build --tag local/workflowcontainer .`

   Tady je příklad ukázkového souboru Docker, který nasadí stavovou aplikaci logiky a určuje připojovací řetězec pro účet Azure Storage, který se použil k publikování aplikace logiky do Azure Portal. Pokud chcete najít a zkopírovat připojovací řetězec účtu úložiště v Azure Portal, přečtěte si téma [Správa klíčů účtu úložiště](../storage/common/storage-account-keys-manage.md?tabs=azure-portal#view-account-access-keys).

   ![Snímek obrazovky zobrazující Azure Portal s přístupovými klíči účtu úložiště a zkopírovaným připojovacím řetězcem](./media/create-stateful-stateless-workflows-visual-studio-code/find-storage-account-connection-string.png)

   Připojovací řetězec vypadá podobně jako v této ukázce:

   `DefaultEndpointsProtocol=https;AccountName=fabrikamstorageaccount;AccountKey={access-key};EndpointSuffix=core.windows.net`

   Tady je formát souboru Docker:


   ```text
   FROM mcr.microsoft.com/azure-functions/dotnet:3.0.14492-appservice

   ENV AzureWebJobsStorage <storage-account-connection-string>
   ENV AZURE_FUNCTIONS_ENVIRONMENT Development
   ENV AzureWebJobsScriptRoot=/home/site/wwwroot
   ENV AzureFunctionsJobHost__Logging__Console__IsEnabled=true
   ENV FUNCTIONS_V2_COMPATIBILITY_MODE=true

   COPY ./bin/Release/netcoreapp3.1/publish/ /home/site/wwwroot
   ```

   Další informace najdete v tématu [sestavení Docker](https://docs.docker.com/engine/reference/commandline/build/).

1. Uložte řetězec někam do trezoru, abyste později mohli přidat řetězec do **local.settings.js** soubory v projektu, které používáte pro vytvoření aplikace logiky v Visual Studio Code.

1. Spusťte kontejner místně pomocí tohoto příkazu:

   `docker run -e WEBSITE_HOSTNAME=localhost -p 8080:80 local/workflowcontainer`

   Další informace najdete v tématu [Docker Run](https://docs.docker.com/engine/reference/commandline/run/).

1. Pokud chcete získat adresu URL zpětného volání pro Trigger žádosti, pošlete tuto žádost:

   `POST /runtime/webhooks/workflow/api/management/workflows/{workflow-name}/triggers/{trigger-name}/listCallbackUrl?api-version=2019-10-01-edge-preview&code={master-key}`

   Hodnota> *hlavního klíče* <je definovaná v účtu služby Azure Storage, který jste nastavili `AzureWebJobsStorage` v souboru, **Azure-WebJobs-tajnosti/{Deployment-Name}/host.js**, kde můžete najít hodnotu v této části:

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

<a name="nested-behavior"></a>

## <a name="nested-behavior-differences-between-stateful-and-stateless-logic-apps"></a>Rozdíly mezi vnořenými chováními mezi stavové a bezstavové aplikace logiky

[Pracovní postup aplikace logiky můžete vyvolat](../logic-apps/logic-apps-http-endpoint.md) z dalších pracovních postupů aplikace logiky, které existují ve stejném zdroji aplikace logiky **(Preview)** pomocí triggeru [žádosti](../connectors/connectors-native-reqres.md) , triggeru [Webhooku protokolu HTTP](../connectors/connectors-native-webhook.md) nebo aktivační události spravovaného konektoru, které mají [typ ApiConnectionWehook](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-trigger) a můžou přijímat požadavky HTTPS.

Tady jsou vzory chování, které mohou vnořené pracovní postupy aplikace logiky použít po volání nadřazeného pracovního postupu pro podřízený pracovní postup:

* Vzor asynchronního cyklického dotazování

  Nadřazený objekt nečeká na odezvu na počáteční volání, ale nepřetržitě kontroluje historii spuštění podřízeného objektu, dokud nebude podřízený objekt spuštěn. Ve výchozím nastavení se řídí stavové pracovní postupy, které jsou ideální pro dlouhotrvající podřízené pracovní postupy, které by mohly překročit [limity časových limitů požadavků](../logic-apps/logic-apps-limits-and-config.md).

* Synchronní vzor ("oheň" a "zapomenout")

  Podřízená položka potvrdí volání okamžitým vrácením `202 ACCEPTED` odpovědi a Nadřazená položka pokračuje k další akci, aniž by bylo nutné čekat na výsledky z podřízeného objektu. Místo toho nadřazený objekt obdrží výsledky po dokončení podřízeného prvku. Podřízené stavové pracovní postupy, které neobsahují akci odpovědi, se vždy řídí synchronním vzorem. V případě podřízených stavových pracovních postupů je k dispozici historie spuštění, kterou si můžete projít.

  Pokud chcete toto chování povolit, v definici JSON pracovního postupu nastavte `operationOptions` vlastnost na `DisableAsyncPattern` . Další informace naleznete v tématu [Trigger a typy akcí – možnosti operací](../logic-apps/logic-apps-workflow-actions-triggers.md#operation-options).

* Aktivovat a počkat

  V případě podřízených bezstavových pracovních postupů nadřazený objekt čeká na odpověď, která vrací výsledky z podřízeného objektu. Tento model funguje podobně jako použití integrovaného [triggeru http nebo akce](../connectors/connectors-native-http.md) pro volání podřízeného pracovního postupu. Podřízené nestavové pracovní postupy, které nezahrnují akci odpovědi, okamžitě vrátí `202 ACCEPTED` odpověď, ale nadřazený objekt čeká na dokončení podřízeného objektu před pokračováním další akce. Toto chování platí jenom pro podřízené pracovní postupy bez stavů.

Tato tabulka určuje chování podřízeného pracovního postupu na základě toho, zda jsou nadřazené a podřízené položky stavové, bezstavové, nebo jsou smíšené typy pracovních postupů:

| Nadřazený pracovní postup | Podřízený pracovní postup | Podřízené chování |
|-----------------|----------------|----------------|
| Stavové | Stavové | Asynchronní nebo synchronní s `"operationOptions": "DisableAsyncPattern"` nastavením |
| Stavové | Bezstavová | Aktivovat a počkat |
| Bezstavová | Stavové | Synchronní |
| Bezstavová | Bezstavová | Aktivovat a počkat |
||||

## <a name="limits"></a>Omezení

I když je mnoho [stávajících omezení pro Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md) pro tento typ prostředku stejné, jedná se o rozdíly v tomto rozšíření veřejné verze Preview:

* Spravované konektory: 50 požadavků za minutu na připojení

* Pro akci [vloženého kódu pro akci JavaScriptu](../logic-apps/logic-apps-add-run-inline-code.md) se tato omezení změnila:

  * Limit znaků kódu se zvyšuje z 1 024 znaků na 100 000 znaků.

  * Časový limit pro spuštění kódu se zvyšuje od pěti sekund až po 15 sekund.

<a name="troubleshooting"></a>

## <a name="troubleshoot-errors-and-problems"></a>Řešení chyb a problémů

<a name="400-bad-request"></a>

### <a name="400-bad-request"></a>"400 Chybný požadavek"

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

## <a name="next-steps"></a>Další kroky

Rádi bychom od vás slyšeli o vašich zkušenostech s tímto rozšířením Public Preview!

* V případě chyb nebo problémů [vytvořte problémy v GitHubu](https://github.com/Azure/logicapps/issues).
* Pro otázky, požadavky, komentáře a další zpětnou vazbu [použijte tento formulář zpětné vazby](https://aka.ms/lafeedback).
