---
title: Přehled pro Azure Logic Apps Preview
description: Azure Logic Apps Preview je cloudové řešení pro vytváření automatizovaných stavových a bezstavových pracovních postupů, které integrují aplikace, data, služby a systémy s minimálním kódem pro scénáře na podnikové úrovni.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, az-logic-apps-dev
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: 9d8d3cb4bf68f7da2bddabd21272d1011ce92f66
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101715203"
---
# <a name="overview-azure-logic-apps-preview"></a>Přehled: Azure Logic Apps Preview

> [!IMPORTANT]
> Tato funkce je ve verzi Public Preview, poskytuje se bez smlouvy o úrovni služeb a nedoporučuje pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

V Azure Logic Apps Preview můžete vytvářet řešení pro automatizaci a integraci napříč aplikacemi, daty, Cloud Services a systémy tím, že vytvoříte a spustíte aplikace logiky, které zahrnují [ *stavové* a *bezstavové* pracovní postupy](#stateful-stateless) pomocí nového typu prostředku **Aplikace logiky (Preview)** . Díky tomuto novému typu aplikace logiky můžete vytvořit několik pracovních postupů využívajících přepracované prostředí runtime Azure Logic Apps Preview, které poskytuje přenositelnost, lepší výkon a flexibilitu pro nasazení a spouštění v různých hostujících prostředích, nejen Azure, ale i kontejnery Docker.

Jak je to možné? Přepracované modul runtime používá [model rozšíření Azure Functions](../azure-functions/functions-bindings-register.md) a je hostovaný jako rozšíření na Azure Functions runtime. Tato architektura znamená, že můžete spustit nový typ aplikace logiky kdekoli, kde Azure Functions běžet. Opětovně navržený modul runtime můžete hostovat téměř u všech síťových topologií a vybrat dostupnou výpočetní velikost pro zpracování potřebných úloh, které vyžadují vaše pracovní postupy. Další informace najdete v tématu [Úvod do Azure Functions](../azure-functions/functions-overview.md) a [Azure Functions triggery a vazby](../azure-functions/functions-triggers-bindings.md).

Můžete vytvořit prostředek **Aplikace logiky (Preview)** , a to buď [spuštěním v Azure Portal](create-stateful-stateless-workflows-azure-portal.md) nebo [vytvořením projektu v Visual Studio Code s rozšířením Azure Logic Apps (Preview)](create-stateful-stateless-workflows-visual-studio-code.md). V Visual Studio Code také můžete vytvářet *a místně spouštět* pracovní postupy ve svém vývojovém prostředí. Bez ohledu na to, jestli používáte portál nebo Visual Studio Code, můžete nasadit a spustit nový typ aplikace logiky ve stejných druzích hostujících prostředí.

Tento přehled obsahuje následující oblasti:

* [Rozdíly mezi Azure Logic Apps Preview, Azure Logic Apps víceklientské prostředí a prostředí integrační služby](#preview-differences).

* [Rozdíly mezi stavovou a bezstavovou pracovními postupy](#stateful-stateless), včetně rozdílů v chování mezi [vnořenými a bezstavových pracovních postupů](#nested-behavior).

* [Možnosti ve verzi Public Preview](#public-preview-contents).

* [Jak cenový model funguje](#pricing-model).

* [Změněné, omezené, nedostupné nebo nepodporované možnosti](#limited-unavailable-unsupported).

* [Omezení ve verzi Preview Azure Logic Apps](#limits).

Další informace najdete v těchto tématech:

* [Azure Logic Apps běžící kdekoli – modul runtime s hloubkou podrobně](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-runtime-deep-dive/ba-p/1835564)

* [Logic Apps Public Preview známé problémy (GitHub)](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md)

<a name="preview-differences"></a>

## <a name="how-does-azure-logic-apps-preview-differ"></a>Jak se liší Azure Logic Apps Preview?

Modul runtime Azure Logic Apps Preview používá rozšíření [Azure Functions](../azure-functions/functions-overview.md) a je hostovaný jako rozšíření v modulu runtime Azure Functions. Tato architektura znamená, že můžete spustit nový typ aplikace logiky kdekoli, kde Azure Functions běžet. Modul runtime služby Azure Logic Apps Preview můžete hostovat na téměř libovolné síťové topologii, kterou potřebujete, a vybrat dostupnou výpočetní velikost pro zpracování potřebných úloh, které váš pracovní postup potřebuje. Další informace o rozšíření Azure Functions najdete v tématu věnovaném [sadě WebJobs SDK: vytváření vlastních vstupních a výstupních vazeb](https://github.com/Azure/azure-webjobs-sdk/wiki/Creating-custom-input-and-output-bindings).

Díky tomuto novému přístupu se modul runtime Azure Logic Apps Preview a vaše pracovní postupy nacházejí v rámci aplikace, které můžete zabalit dohromady. Tato funkce umožňuje nasazení a spouštění pracovních postupů pouhým kopírováním artefaktů do hostitelského prostředí a spuštěním vaší aplikace. Tento přístup také poskytuje standardizované prostředí pro vytváření kanálů nasazení v rámci projektů pracovních postupů pro spouštění požadovaných testů a ověření před nasazením změn v produkčních prostředích. Další informace najdete v tématu [Azure Logic Apps spouštění odkudkoli a za běhu s hlubokým podrobně](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-runtime-deep-dive/ba-p/1835564).

Následující tabulka stručně shrnuje rozdíly ve způsobu, jakým pracovní postupy sdílejí prostředky, na základě prostředí, ve kterém se spouštějí. Rozdíly v omezeních najdete v tématu [limity v Azure Logic Apps Preview](#limits).

| Prostředí | Sdílení a spotřeba prostředků |
|-------------|----------------------------------|
| Azure Logic Apps (více tenantů) | Pracovní postupy *od zákazníků ve více klientech* sdílejí stejné zpracování (COMPUTE), úložiště, sítě a tak dále. |
| Azure Logic Apps (Preview) | Pracovní postupy *ve stejné aplikaci logiky* sdílejí stejné zpracování (COMPUTE), úložiště, síť a tak dále. |
| Prostředí integrační služby (není k dispozici ve verzi Preview) | Pracovní postupy ve *stejném prostředí* sdílejí stejné zpracování (COMPUTE), úložiště, síť a tak dále. |
||||

Mezitím stále můžete v Azure Portal a v Visual Studio Code vytvořit původní typ aplikace logiky, a to pomocí původní přípony Azure Logic Apps. I když se vývojové prostředí liší mezi původními a novými typy aplikací logiky, může vaše předplatné Azure zahrnovat oba typy. Můžete zobrazit a přistupovat ke všem nasazeným Logic Apps v předplatném Azure, ale aplikace se uspořádají do jejich vlastních kategorií a oddílů.

<a name="stateful-stateless"></a>

## <a name="stateful-and-stateless-workflows"></a>Stavové a bezstavové pracovní postupy

* *Stavové*

  Vytváření stavových pracovních postupů, když potřebujete zachovat, zkontrolovat nebo odkazovat data z předchozích událostí. Tyto pracovní postupy ukládají vstupy a výstupy pro každou akci a jejich stavy v externím úložišti, což umožňuje po dokončení každého spuštění zkontrolovat podrobnosti a historii spuštění. Stavové pracovní postupy poskytují vysokou odolnost, pokud dojde k výpadkům. Po obnovení služeb a systémů můžete znovu vytvořit přerušené běhy z uloženého stavu a znovu spustit pracovní postupy k dokončení. Stavové pracovní postupy můžou běžet až do roku.

* *Bezstavová*

  Vytvářejte bezstavové pracovní postupy, pokud nepotřebujete ukládat, kontrolovat nebo odkazovat data z předchozích událostí v externím úložišti pro pozdější kontrolu. Tyto pracovní postupy ukládají vstupy a výstupy pro každou akci a jejich stavy *pouze v paměti* místo přenosu dat do externího úložiště. V důsledku toho mají bezstavové pracovní postupy kratší dobu, než je doba delší než 5 minut, rychlejší výkon při rychlejší odezvě, vyšší propustnost a snížené provozní náklady, protože podrobnosti a historie spuštění nejsou uchovávány v externím úložišti. Pokud se ale dojde k výpadkům, přerušené běhy se automaticky neobnoví, takže volající musí ručně znovu odeslat přerušené běhy. Tyto pracovní postupy lze spustit pouze synchronně.

  Pro snazší ladění můžete povolit historii spuštění pro bezstavový pracovní postup, který má vliv na výkon a po dokončení zakažte historii spuštění. Další informace najdete v tématu [vytváření stavových a bezstavových pracovních postupů v Visual Studio Code](create-stateful-stateless-workflows-visual-studio-code.md#enable-run-history-stateless) nebo [vytváření pracovních a bezstavových pracovních postupů v Azure Portal](create-stateful-stateless-workflows-visual-studio-code.md#enable-run-history-stateless).

  > [!NOTE]
  > Bezstavové pracovní postupy aktuálně podporují jenom *Akce* pro [spravované konektory](../connectors/apis-list.md#managed-api-connectors), které jsou nasazené v Azure, a ne triggery. Chcete-li spustit pracovní postup, vyberte buď [integrovaný požadavek, Event Hubs nebo aktivační událost Service Bus](../connectors/apis-list.md#built-ins). Tyto aktivační události se spouštějí nativně v modulu runtime Azure Logic Apps Preview. Další informace o omezených, nedostupných nebo nepodporovaných triggerech, akcích a konektorech najdete v tématu [Změna, omezené, nedostupné nebo nepodporované funkce](#limited-unavailable-unsupported).

<a name="nested-behavior"></a>

### <a name="nested-behavior-differences-between-stateful-and-stateless-workflows"></a>Vnořené rozdíly v chování mezi stavové a bezstavové pracovní postupy

[Pracovní postup můžete vyvolat](../logic-apps/logic-apps-http-endpoint.md) z dalších pracovních postupů, které existují ve stejném zdroji **Aplikace logiky (Preview)** pomocí [triggeru žádosti](../connectors/connectors-native-reqres.md), [triggeru Webhooku protokolu HTTP](../connectors/connectors-native-webhook.md)nebo triggerů spravovaného konektoru, které mají [typ vstupech apiconnectionwebhook](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-trigger) a můžou přijímat požadavky HTTPS.

Tady jsou vzory chování, které mohou vnořené pracovní postupy sledovat poté, co nadřazený pracovní postup volá podřízený pracovní postup:

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

<a name="public-preview-contents"></a>

## <a name="capabilities"></a>Možnosti

Azure Logic Apps Preview zahrnuje mnoho současných a dalších funkcí, například:

* Vytvářejte aplikace logiky a jejich pracovní postupy z [390 + konektorů](/connectors/connector-reference/connector-reference-logicapps-connectors) pro aplikace typu software jako služba (SaaS) a PaaS (Platform-as-a-Service) a konektory pro místní systémy.

  * Některé spravované konektory, například Azure Service Bus, Azure Event Hubs a SQL Server, se spouštějí podobně jako integrované triggery a akce, které jsou nativní pro modul runtime Azure Logic Apps Preview, například Trigger žádosti a akce HTTP. Další informace najdete v tématu [Azure Logic Apps spouštění odkudkoli a integrovaných rozšíření konektorů](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-built-in-connector/ba-p/1921272).

  * Akce B2B můžete použít pro likvidní operace a operace XML bez účtu pro integraci. Chcete-li použít tyto akce, je nutné mít mapy kapalin, XML map nebo schémata XML, které lze nahrát prostřednictvím příslušných akcí v Azure Portal nebo přidat do složky **artefakty** Visual Studio Code projektu pomocí příslušných složek **map** a **schémat** .

  * Vytvářejte a nasaďte Logic Apps, které se dají spustit kdekoli, protože služba Azure Logic Apps generuje připojovací řetězce sdíleného přístupového podpisu (SAS), které tyto aplikace logiky můžou použít k odesílání požadavků do koncového bodu cloudového připojení. Služba Logic Apps ukládá tyto připojovací řetězce s dalšími nastaveními aplikací, takže je můžete snadno ukládat do Azure Key Vault při nasazení v Azure.

    > [!NOTE]
    > Ve výchozím nastavení má prostředek **Aplikace logiky (Preview)** k dispozici [spravovanou identitu přiřazenou systémem](../logic-apps/create-managed-service-identity.md) automaticky pro ověřování připojení za běhu. Tato identita se liší od přihlašovacích údajů k ověřování nebo připojovacího řetězce, který používáte při vytváření připojení. Pokud tuto identitu zakážete, nebudou připojení fungovat za běhu. Toto nastavení zobrazíte tak, že v nabídce aplikace logiky v části **Nastavení** vyberete **Identita**.

* Vytvářejte aplikace logiky pomocí bezstavových pracovních postupů, které běží jenom v paměti, takže rychleji dostanou rychlejší, mají vyšší propustnost a můžou méně snížit náklady, protože historie spuštění a data mezi akcemi nejsou v externím úložišti trvalé. Volitelně můžete povolit historii spuštění pro snazší ladění. Další informace najdete v tématu [stavová versus Bezstavová Logic aplikace](#stateful-stateless).

* Místně spouštějte, testujte a ladíme Logic Apps a jejich pracovní postupy ve vývojovém prostředí Visual Studio Code.

  Před spuštěním a otestováním aplikace logiky můžete usnadnit ladění přidáním a použitím zarážek uvnitř **workflow.js** souboru pro pracovní postup. Zarážky se ale v tuto chvíli podporují jenom pro akce, ne triggery. Další informace najdete v tématu [Vytvoření stavových a bezstavových pracovních postupů v Visual Studio Code](create-stateful-stateless-workflows-visual-studio-code.md#manage-breakpoints).

* Přímo publikujte nebo nasaďte Logic Apps a jejich pracovní postupy z Visual Studio Code do různých hostujících prostředí, jako jsou například kontejnery Azure a [Docker](/dotnet/core/docker/introduction).

* Povolte možnosti protokolování a trasování diagnostiky pro vaši aplikaci logiky pomocí [Application Insights](../azure-monitor/app/app-insights-overview.md) , když to podporuje vaše předplatné Azure a nastavení aplikace logiky.

* Přístup k síťovým funkcím, jako je připojení a integrace s virtuálními sítěmi Azure, podobně jako Azure Functions při vytváření a nasazování aplikací logiky s využitím [plánu Azure Functions Premium](../azure-functions/functions-premium-plan.md). Další informace najdete v těchto tématech:

  * [Možnosti sítí Azure Functions](../azure-functions/functions-networking-options.md)

  * [Azure Logic Apps spouštění možností kdekoli v síti pomocí Azure Logic Apps Preview](https://techcommunity.microsoft.com/t5/integrations-on-azure/logic-apps-anywhere-networking-possibilities-with-logic-app/ba-p/2105047)

* Znovu vygenerujte přístupové klíče pro spravovaná připojení používaná jednotlivými pracovními postupy v prostředku **Aplikace logiky (Preview)** . Pro tuto úlohu [použijte stejný postup jako u prostředku **Logic Apps** , ale na úrovni jednotlivých pracovních postupů](logic-apps-securing-a-logic-app.md#regenerate-access-keys), nikoli na úrovni prostředků aplikace logiky.

* Pomocí stejných kroků jako v Návrháři bez náhledu přidejte do nového návrháře paralelní větve.
 
Další informace najdete v tématu [Změna, omezené, nedostupné a nepodporované funkce](#limited-unavailable-unsupported) a [Stránka Logic Apps Public Preview známých problémů na GitHubu](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md).

<a name="pricing-model"></a>

## <a name="pricing-model"></a>Cenový model

Když vytvoříte nový typ aplikace logiky v Azure Portal nebo nasadíte z Visual Studio Code, musíte zvolit plán hostování, buď [App Service nebo Premium](../azure-functions/functions-scale.md), pro použití vaší aplikace logiky. Tento plán určuje cenový model, který se vztahuje na běh aplikace logiky. Pokud vyberete plán App Service, musíte také zvolit [cenovou úroveň](../app-service/overview-hosting-plans.md).

*Stavové* pracovní postupy používají [externí úložiště](../azure-functions/storage-considerations.md#storage-account-requirements), takže [ceny Azure Storage](https://azure.microsoft.com/pricing/details/storage/) se vztahují na transakce úložiště, které provádí modul runtime Azure Logic Apps Preview. Například fronty se používají pro plánování, zatímco tabulky a objekty BLOB se používají k ukládání stavů pracovního postupu.

> [!NOTE]
> Během veřejné verze Preview se pro spuštění Logic Apps na App Service neúčtují *Další* poplatky nad vybraný plán.

Další informace o cenových modelech, které se vztahují na tento nový typ prostředku, najdete v těchto tématech:

* [Hostování a škálování Azure Functions](../azure-functions/functions-scale.md)
* [Horizontální navýšení kapacity aplikace v Azure App Service](../app-service/manage-scale-up.md)
* [Podrobnosti o cenách Azure Functions](https://azure.microsoft.com/pricing/details/functions/)
* [Podrobnosti o cenách App Service](https://azure.microsoft.com/pricing/details/app-service/)
* [Podrobnosti o cenách Azure Storage](https://azure.microsoft.com/pricing/details/storage/)

<a name="limited-unavailable-unsupported"></a>

## <a name="changed-limited-unavailable-or-unsupported-capabilities"></a>Změněné, omezené, nedostupné nebo nepodporované možnosti

V Azure Logic Apps ve verzi Preview se tyto možnosti změnily nebo jsou aktuálně omezené, nedostupné nebo nepodporované:

* **Podpora OS**: návrhář v Visual Studio Code v současnosti nefunguje v operačním systému Linux, ale stále můžete nasazovat Logic Apps, které používají modul runtime Preview Logic Apps na virtuální počítače se systémem Linux. Prozatím můžete vytvářet aplikace logiky v Visual Studio Code ve Windows nebo macOS a pak je nasadit do virtuálního počítače se systémem Linux.

* **Aktivační události a akce**: integrované triggery a akce se spouštějí nativně v modulu runtime služby Azure Logic Apps Preview, zatímco spravované konektory se nasazují v Azure. Některé vestavěné triggery nejsou k dispozici, například posuvné okno a dávku.

  Pokud chcete spustit pracovní postup, použijte [integrované opakování, požadavek, http, Webhook http, Event Hubs nebo aktivační událost Service Bus](../connectors/apis-list.md). V návrháři se předdefinované triggery a akce zobrazí pod **integrovanou** kartou, zatímco triggery a akce spravovaného konektoru se zobrazí na kartě **Azure** .

  > [!NOTE]
  > Pro místní spuštění v Visual Studio Code triggery a akce založené na webhookech vyžadují další nastavení. Další informace najdete v tématu [Vytvoření stavových a bezstavových pracovních postupů v Visual Studio Code](create-stateful-stateless-workflows-visual-studio-code.md#webhook-setup).

  * V případě *bezstavových pracovních postupů* se karta **Azure** nezobrazí při výběru triggeru, protože můžete vybrat jenom [ *Akce* spravovaného konektoru, ne triggery](../connectors/apis-list.md#managed-api-connectors). I když můžete povolit spravované konektory nasazené Azure pro bezstavové pracovní postupy, Návrhář nezobrazí žádné triggery spravovaného konektoru, které byste mohli přidat.

  * U *stavových pracovních postupů* kromě triggerů a akcí, které jsou uvedené níže jako nedostupné, se [triggery spravovaného konektoru a akce](../connectors/apis-list.md#managed-api-connectors) jsou k dispozici pro použití.

  * Tyto triggery a akce se buď změnily, nebo jsou aktuálně omezené, nepodporované nebo nedostupné:

    * [ *Aktivační události* místních bran dat](../connectors/apis-list.md#on-premises-connectors) nejsou k dispozici, ale *jsou* k dispozici akce brány.

    * [Vlastní konektory](../connectors/apis-list.md#custom-apis-and-connectors) nejsou k dispozici.

    * Vestavěná akce, [Azure Functions – vyberte funkci Azure Functions](logic-apps-azure-functions.md) je teď funkce Azure **Functions – volejte funkci Azure Functions**. Tato akce teď funguje jenom pro funkce, které jsou vytvořené ze šablony **triggeru http** .

      V Azure Portal můžete vybrat funkci triggeru HTTP, kde máte přístup vytvořením připojení prostřednictvím uživatelského prostředí. Pokud provedete kontrolu definice JSON akce funkce v zobrazení kódu nebo **workflow.jsv** souboru, akce odkazuje na funkci pomocí `connectionName` odkazu. Tato verze vyabstrakce informace o funkci jako připojení, které můžete najít v **connections.jsprojektu v** souboru, který je k dispozici po vytvoření připojení.

      > [!NOTE]
      > V rámci verze Preview podporuje akce funkce pouze ověřování řetězce dotazu. Azure Logic Apps Preview Získá výchozí klíč z funkce při vytváření připojení, uloží tento klíč do nastavení aplikace a při volání funkce použije klíč k ověření.
      >
      > V případě původní verze platí, že pokud tento klíč obnovíte například prostřednictvím prostředí Azure Functions na portálu, akce Function již nebude fungovat z důvodu neplatného klíče. Chcete-li tento problém vyřešit, je nutné znovu vytvořit připojení k funkci, kterou chcete volat nebo aktualizovat nastavení aplikace pomocí nového klíče.

    * Vestavěná akce, [vložený kód jazyka JavaScript spouštěný kódem](logic-apps-add-run-inline-code.md) , je nyní **vložená operace kódu – spuštění vloženého JavaScriptu**.

      * Akce vloženého kódu již nevyžadují účet pro integraci.

      * V případě systému macOS a Linux se teď **vložené operace s kódem** podporují při použití rozšíření Azure Logic Apps (Preview) v Visual Studio Code.

      * Pokud provedete změny v **operaci vloženého kódu** , už nemusíte restartovat aplikaci logiky.

      * **Vložené akce kódu** mají [aktualizované limity](logic-apps-overview-preview.md#inline-code-limits).

    * Některé [integrované aktivační události B2B a akce pro účty pro integraci](../connectors/apis-list.md#integration-account-connectors) nejsou k dispozici, například akce kódování a dekódování **plochého souboru** .

    * Integrovaná akce, [Azure Logic Apps – vybrat pracovní postup aplikace logiky](logic-apps-http-endpoint.md) je teď **operace pracovního postupu – vyvolat pracovní postup v této aplikaci pracovního postupu**.

* **Dostupnost plánu hostování**: ať už vytvoříte nový typ prostředku **Aplikace logiky (Preview)** v Azure Portal nebo nasadíte z Visual Studio Code, můžete v Azure použít jenom plán hostování Premium nebo App Service. Plány hostování spotřeby nejsou k dispozici a nejsou podporovány pro nasazení tohoto typu prostředku. Můžete nasadit z Visual Studio Code do kontejneru Docker, ale ne do [prostředí integračních služeb (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md).

* **Ladění zarážek v Visual Studio Code**: i když můžete přidat a používat zarážky v rámci **workflow.js** souboru pro pracovní postup, zarážky se podporují jenom pro akce v tuto chvíli, ne triggery. Další informace najdete v tématu [Vytvoření stavových a bezstavových pracovních postupů v Visual Studio Code](create-stateful-stateless-workflows-visual-studio-code.md#manage-breakpoints).

* **Lupa Control**: ovládací prvek Lupa není aktuálně k dispozici v návrháři.

* **Historie aktivačních událostí a historie spuštění**: pro typ prostředku **Aplikace logiky (Preview)** , historie aktivačních událostí a historie spuštění v Azure Portal se zobrazí na úrovni pracovního postupu, nikoli na úrovni aplikace logiky. K vyhledání těchto historických dat použijte následující postup:

   * Chcete-li zobrazit historii spuštění, otevřete pracovní postup ve vaší aplikaci logiky. V nabídce pracovní postup vyberte v části **vývojář** možnost **monitor**.

   * Pokud chcete zkontrolovat historii triggerů, otevřete pracovní postup ve vaší aplikaci logiky. V nabídce pracovní postup vyberte v části **vývojář** možnost **aktivovat historie**.

<a name="limits"></a>

## <a name="updated-limits"></a>Aktualizovaná omezení

I když mnoho omezení pro Azure Logic Apps Preview zůstane stejné jako [limity pro víceklientské Azure Logic Apps](logic-apps-limits-and-config.md), tato omezení se u Azure Logic Apps Preview změnila.

<a name="http-timeout-limits"></a>

### <a name="http-timeout-limits"></a>Omezení časových limitů HTTP

Pro jedno příchozí nebo odchozí volání je časový limit 230 sekund (3,9 minut) pro tyto triggery a akce:

* Odchozí požadavek: aktivační událost HTTP, akce HTTP
* Příchozí požadavek: aktivační událost žádosti, Trigger Webhooku protokolu HTTP, akce Webhooku protokolu HTTP

Tady jsou limity časového limitu pro tyto triggery a akce v jiných prostředích, kde se spouštějí aplikace logiky a jejich pracovní postupy:

* Azure Logic Apps více tenantů: 120 sekund (2 minuty)
* Prostředí integrační služby: 240 sekund (4 minuty)

Další informace najdete v tématu [omezení http](logic-apps-limits-and-config.md#http-limits).

<a name="managed-connector-limits"></a>

### <a name="managed-connectors"></a>Spravované konektory

Spravované konektory jsou omezeny na 50 požadavků za minutu na jedno připojení. Pokud chcete pracovat s problémy s omezením konektoru, přečtěte si téma [zpracování potíží s omezením (429 – chyba příliš velkého počtu požadavků) v Azure Logic Apps](handle-throttling-problems-429-errors.md#connector-throttling).

<a name="inline-code-limits"></a>

### <a name="inline-code-operations-execute-javascript-code"></a>Vložené operace kódu (spuštění kódu jazyka JavaScript)

Pro jednu definici aplikace logiky, akce vloženého kódu, [**Spustit kód jazyka JavaScript**](logic-apps-add-run-inline-code.md), má tyto aktualizované limity:

* Maximální počet znaků kódu se zvyšuje z 1 024 znaků na 100 000 znaků.

* Maximální doba, po kterou se kód spouští, se zvýší z pěti sekund na 15 sekund.

Další informace najdete v tématu [omezení definice aplikace logiky](logic-apps-limits-and-config.md#definition-limits).

## <a name="next-steps"></a>Další kroky

* [Vytváření stavových a bezstavových pracovních postupů v Azure Portal](create-stateful-stateless-workflows-azure-portal.md)
* [Vytváření stavových a bezstavových pracovních postupů v Visual Studio Code](create-stateful-stateless-workflows-visual-studio-code.md)
* [Stránka Logic Apps Public Preview známé problémy v GitHubu](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md)

Rádi bychom se také dozvěděli o vašich zkušenostech s Azure Logic Apps ve verzi Preview!

* V případě chyb nebo problémů [vytvořte problémy v GitHubu](https://github.com/Azure/logicapps/issues).
* Pro otázky, požadavky, komentáře a další zpětnou vazbu [použijte tento formulář zpětné vazby](https://aka.ms/lafeedback).
