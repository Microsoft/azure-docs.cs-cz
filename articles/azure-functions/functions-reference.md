---
title: Pokyny k vývoji Azure Functions
description: Naučte se Azure Functions koncepty a techniky, které potřebujete k vývoji funkcí v Azure, a to napříč všemi programovacími jazyky a vazbami.
ms.assetid: d8efe41a-bef8-4167-ba97-f3e016fcd39e
ms.topic: conceptual
ms.date: 10/12/2017
ms.openlocfilehash: 7030ca1c1950f7c06580ce7417a4429fbe330c4e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102614815"
---
# <a name="azure-functions-developer-guide"></a>Příručka pro vývojáře v Azure Functions
V Azure Functions konkrétní funkce sdílí několik základních technických konceptů a komponent bez ohledu na jazyk nebo vazbu, které používáte. Než přejdete k podrobnostem učení, které jsou specifické pro daný jazyk nebo vazbu, přečtěte si tento přehled, který platí pro všechny.

V tomto článku se předpokládá, že už jste si přečetli [Azure Functions Overview](functions-overview.md).

## <a name="function-code"></a>Kód funkce
*Funkce* je primární koncept v Azure Functions. Funkce obsahuje dva důležité díly – váš kód, který lze zapsat v nejrůznějších jazycích, a některé konfigurace function.jssouboru. V případě kompilovaných jazyků se tento konfigurační soubor automaticky generuje z poznámek ve vašem kódu. V případě skriptovacích jazyků je nutné zadat konfigurační soubor sami.

function.jsv souboru definuje Trigger funkce, vazby a další nastavení konfigurace. Každá funkce má jednu a jenom jednu aktivační událost. Modul runtime používá tento konfigurační soubor k určení událostí ke sledování a k předávání dat do a návratového data z provádění funkce. Následuje příklad function.jssouboru.

```json
{
    "disabled":false,
    "bindings":[
        // ... bindings here
        {
            "type": "bindingType",
            "direction": "in",
            "name": "myParamName",
            // ... more depending on binding
        }
    ]
}
```

Další informace najdete v tématu [Azure Functions triggery a koncepty vazeb](functions-triggers-bindings.md).

`bindings`Vlastnost je místo, kde můžete nakonfigurovat triggery i vazby. Každá vazba sdílí několik společných nastavení a některá nastavení, která jsou specifická pro konkrétní typ vazby. Každá vazba vyžaduje následující nastavení:

| Vlastnost    | Hodnoty | Typ | Komentáře|
|---|---|---|---|
| typ  | Název vazby<br><br>Například, `queueTrigger`. | řetězec | |
| směr | `in`, `out`  | řetězec | Označuje, zda je vazba určena pro příjem dat do funkce nebo odesílání dat z funkce. |
| name | Identifikátor funkce<br><br>Například, `myQueue`. | řetězec | Název, který se používá pro vázaná data ve funkci. V jazyce C# je toto název argumentu; v případě JavaScriptu je klíč v seznamu klíč/hodnota. |

## <a name="function-app"></a>Aplikace funkcí
Function App poskytuje kontext spuštění v Azure, ve kterém se vaše funkce spouštějí. V takovém případě je to jednotka nasazení a správy pro vaše funkce. Aplikace Function App se skládá z jedné nebo několika individuálních funkcí, které jsou spravované, nasazené a škálovat dohromady. Všechny funkce ve Function App sdílí stejný cenový plán, způsob nasazení a běhovou verzi. Aplikaci Function App si můžete představit jako způsob, jak organizovat a shromažďovat své funkce. Další informace najdete v tématu [Správa aplikace Function App](functions-how-to-use-azure-function-app-settings.md). 

> [!NOTE]
> Všechny funkce aplikace Function App musí být vytvořené ve stejném jazyce. V [předchozích verzích](functions-versions.md) modulu runtime Azure Functions to nebylo nutné.

## <a name="folder-structure"></a>Struktura složek
[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

Výše uvedená je výchozí (a doporučená) struktura složky aplikace Function App. Pokud chcete změnit umístění souboru kódu funkce, upravte `scriptFile` část _function.jsv_ souboru. K nasazení vašeho projektu do aplikace Function App v Azure doporučujeme také použít [nasazení balíčku](deployment-zip-push.md) . Můžete také použít stávající nástroje, jako je [průběžná integrace a nasazování](functions-continuous-deployment.md) a Azure DevOps.

> [!NOTE]
> Pokud balíček nasazujete ručně, nezapomeňte nasazovat _host.jsdo_ souborů a složek funkcí přímo do `wwwroot` složky. Nezahrnujte `wwwroot` složku do nasazení. V opačném případě budete mít `wwwroot\wwwroot` složky.

#### <a name="use-local-tools-and-publishing"></a>Použití místních nástrojů a publikování
Aplikace Function App lze vytvářet a publikovat pomocí různých nástrojů, včetně sady [Visual Studio](./functions-develop-vs.md), [Visual Studio Code](./create-first-function-vs-code-csharp.md), [IntelliJ](./functions-create-maven-intellij.md), [zatmění](./functions-create-maven-eclipse.md)a [Azure Functions Core Tools](./functions-develop-local.md). Další informace naleznete v tématu [Code and test Azure Functions lokálně](./functions-develop-local.md).

<!--NOTE: I've removed documentation on FTP, because it does not sync triggers on the consumption plan --glenga -->

## <a name="how-to-edit-functions-in-the-azure-portal"></a><a id="fileupdate"></a> Postup úpravy funkcí v Azure Portal
Editor funkcí integrovaný do Azure Portal umožňuje aktualizovat kód a *function.jsv* souboru přímo vloženě. Tento postup je doporučený pouze pro malé změny nebo zkoušku konceptu – osvědčeným postupem je použití místního vývojového nástroje, jako je VS Code.

## <a name="parallel-execution"></a>Paralelní provádění
Když dojde k více událostem triggeru rychleji než modul runtime s jedním vláknem, může je zpracovat, modul runtime může funkci vyvolat víckrát.  Pokud aplikace Function App používá [plán hostování spotřeby](event-driven-scaling.md), aplikace Function app by mohla automaticky škálovat horizontální navýšení kapacity.  Každá instance aplikace Function App, bez ohledu na to, jestli aplikace běží na plánu hostování spotřeby nebo běžném [App Service plánu hostování](../app-service/overview-hosting-plans.md), může zpracovávat souběžné volání funkcí paralelně pomocí několika vláken.  Maximální počet souběžných volání funkcí v každé instanci aplikace Function App se liší v závislosti na používaném typu triggeru a na zdrojích používaných jinými funkcemi v aplikaci Function App.

## <a name="functions-runtime-versioning"></a>Správa verzí modulu runtime Functions

Verzi modulu runtime Functions můžete nakonfigurovat pomocí `FUNCTIONS_EXTENSION_VERSION` nastavení aplikace. Například hodnota "~ 3" označuje, že aplikace Function App bude jako svou hlavní verzi používat 3. x. Aplikace Function App jsou při vydání upgradovány na každou novou dílčí verzi. Další informace, včetně toho, jak zobrazit přesnou verzi aplikace Function App, najdete v tématu [jak cílit na verze Azure Functions runtime](set-runtime-version.md).

## <a name="repositories"></a>Úložiště
Kód pro Azure Functions je open source a uložený v úložištích GitHub:

* [Azure Functions](https://github.com/Azure/Azure-Functions)
* [Azure Functions hostitele](https://github.com/Azure/azure-functions-host/)
* [Portál Azure Functions](https://github.com/azure/azure-functions-ux)
* [Šablony Azure Functions](https://github.com/azure/azure-functions-templates)
* [Sada Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/)
* [Rozšíření sady Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk-extensions/)

## <a name="bindings"></a>Vazby
Tady je tabulka všech podporovaných vazeb.

[!INCLUDE [dynamic compute](../../includes/functions-bindings.md)]

Máte problémy s chybami pocházejícími z vazeb? Přečtěte si dokumentaci [Azure Functions kódy chyb vazeb](functions-bindings-error-pages.md) .


## <a name="connections"></a>Připojení

Projekt funkce odkazuje na informace o připojení podle názvu od poskytovatele konfigurace. Nepřijímá přímo podrobnosti o připojení, což umožňuje jejich změnu napříč prostředími. Například definice triggeru může zahrnovat `connection` vlastnost. To může odkazovat na připojovací řetězec, ale nelze nastavit připojovací řetězec přímo v `function.json` . Místo toho byste nastavili `connection` na název proměnné prostředí, která obsahuje připojovací řetězec.

Výchozí zprostředkovatel konfigurace používá proměnné prostředí. Tyto hodnoty mohou být nastaveny podle [nastavení aplikace](./functions-how-to-use-azure-function-app-settings.md?tabs=portal#settings) při spuštění ve službě Azure Functions nebo při vývoji v [místním souboru s nastavením](functions-run-local.md#local-settings-file) při vývoji místně.

### <a name="connection-values"></a>Hodnoty připojení

Pokud se název připojení přeloží na jedinou přesnou hodnotu, modul runtime tuto hodnotu určí jako _připojovací řetězec_, který obvykle obsahuje tajný klíč. Podrobnosti připojovacího řetězce jsou definovány službou, ke které se chcete připojit.

Název připojení ale může také odkazovat na kolekci více položek konfigurace. Proměnné prostředí lze považovat za kolekci pomocí sdílené předpony, která končí dvojitými podtržítky `__` . Na skupinu se pak může odkazovat nastavením názvu připojení na tuto předponu.

Například `connection` vlastnost pro definici triggeru Azure Blob může být `Storage1` . Pokud není k dispozici žádná jediná řetězcová hodnota nakonfigurovaná `Storage1` jako její název, bude `Storage1__serviceUri` použita pro `serviceUri` vlastnost připojení. Vlastnosti připojení jsou pro každou službu jiné. Informace o rozšíření, které používá připojení, najdete v dokumentaci.

### <a name="configure-an-identity-based-connection"></a>Konfigurace připojení na základě identity

Některá připojení v Azure Functions jsou nakonfigurovaná tak, aby místo tajného kódu používala identitu. Podpora závisí na rozšíření pomocí připojení. V některých případech může být připojovací řetězec ve funkcích stále vyžadován i v případě, že služba, ke které se připojujete, podporuje připojení založená na identitách.

> [!IMPORTANT]
> I v případě, že rozšíření vazby podporuje připojení na základě identity, tato konfigurace nemusí být v plánu spotřeby ještě podporovaná. Podívejte se na následující tabulku podpory.

Připojení založená na identitách jsou podporovaná následujícími triggery a rozšířeními vazby:

| Název rozšíření | Verze rozšíření                                                                                     | Podporováno v plánu spotřeby |
|----------------|-------------------------------------------------------------------------------------------------------|---------------------------------------|
| Azure Blob     | [Verze 5.0.0-Beta1 nebo novější](./functions-bindings-storage-blob.md#storage-extension-5x-and-higher)  | No                                    |
| Azure Queue Storage    | [Verze 5.0.0-Beta1 nebo novější](./functions-bindings-storage-queue.md#storage-extension-5x-and-higher) | No                                    |
| Azure Event Hubs    | [Verze 5.0.0-Beta1 nebo novější](./functions-bindings-event-hubs.md#event-hubs-extension-5x-and-higher) | No                                    |

> [!NOTE]
> Podpora pro připojení na základě identity ještě není k dispozici pro připojení úložiště používaná modulem runtime Functions pro základní chování. To znamená, že toto `AzureWebJobsStorage` nastavení musí být připojovací řetězec.

#### <a name="connection-properties"></a>Vlastnosti připojení

Připojení na základě identity pro službu Azure přijímá následující vlastnosti:

| Vlastnost    | Vyžadováno pro rozšíření | Proměnná prostředí | Description |
|---|---|---|---|
| Identifikátor URI služby | Azure Blob, fronta Azure | `<CONNECTION_NAME_PREFIX>__serviceUri` |  Identifikátor URI datové roviny služby, ke které se připojujete. |
| Plně kvalifikovaný obor názvů | Event Hubs | `<CONNECTION_NAME_PREFIX>__fullyQualifiedNamespace` | Plně kvalifikovaný obor názvů centra událostí. |

Pro daný typ připojení můžou být podporované další možnosti. Informace o komponentě, která vytváří připojení, najdete v dokumentaci.

Pokud je tato služba hostovaná ve službě Azure Functions, používá připojení na základě identity [spravovanou identitu](../app-service/overview-managed-identity.md?toc=%2fazure%2fazure-functions%2ftoc.json). Ve výchozím nastavení se používá identita přiřazená systémem. Při spuštění v jiných kontextech, jako je například místní vývoj, se místo toho používá vaše vývojářská identita, i když to lze přizpůsobit pomocí alternativních parametrů připojení.

##### <a name="local-development"></a>Místní vývoj

Při místním spuštění sdělí výše uvedená konfigurace modulu runtime, aby používal vaši místní identitu vývojáře. Připojení se pokusí získat token z následujících umístění, v uvedeném pořadí:

- Místní mezipaměť sdílená mezi aplikacemi Microsoftu
- Aktuální kontext uživatele v aplikaci Visual Studio
- Kontext aktuálního uživatele v Visual Studio Code
- Kontext aktuálního uživatele v rozhraní příkazového řádku Azure

Pokud žádná z těchto možností není úspěšná, dojde k chybě.

V některých případech můžete chtít zadat použití jiné identity. Můžete přidat vlastnosti konfigurace připojení, které odkazují na alternativní identitu.

> [!NOTE]
> Následující možnosti konfigurace nejsou podporované, pokud jsou hostované ve službě Azure Functions.

Pokud se chcete připojit pomocí Azure Active Directory instančního objektu s ID klienta a tajným kódem, definujte kromě [vlastností připojení](#connection-properties) nahoře i připojení s následujícími požadovanými vlastnostmi:

| Vlastnost    | Proměnná prostředí | Description |
|---|---|---|
| ID tenanta | `<CONNECTION_NAME_PREFIX>__tenantId` | ID tenanta Azure Active Directory (Directory). |
| ID klienta | `<CONNECTION_NAME_PREFIX>__clientId` |  ID klienta (aplikace) registrace aplikace v tenantovi. |
| Tajný klíč klienta | `<CONNECTION_NAME_PREFIX>__clientSecret` | Tajný kód klienta, který se vygeneroval pro registraci aplikace. |

Příklad `local.settings.json` vlastností vyžadovaných pro připojení na základě identity s objektem blob Azure: 
```json
{
  "IsEncrypted": false,
  "Values": {
    "<CONNECTION_NAME_PREFIX>__serviceUri": "<serviceUri>",
    "<CONNECTION_NAME_PREFIX>__tenantId": "<tenantId>",
    "<CONNECTION_NAME_PREFIX>__clientId": "<clientId>",
    "<CONNECTION_NAME_PREFIX>__clientSecret": "<clientSecret>"
  }
}
```

#### <a name="grant-permission-to-the-identity"></a>Udělení oprávnění identitě

Bez ohledu na to, jaká identita se používá, musí mít oprávnění k provádění zamýšlených akcí. To se obvykle provádí přiřazením role v Azure RBAC nebo zadáním identity v zásadách přístupu v závislosti na službě, ke které se připojujete. Informace o tom, jaká oprávnění jsou potřebná a jak se dají nastavit, najdete v dokumentaci ke každé službě.

> [!IMPORTANT]
> Některá oprávnění můžou být vystavená službou, která není potřebná pro všechny kontexty. Pokud je to možné, dodržovat **Princip nejnižších oprávnění** a udělení identity pouze potřebným oprávněním. Pokud například aplikace potřebuje číst z objektu blob, použijte roli [čtečky dat objektů BLOB úložiště](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) , protože [vlastník dat objektu BLOB úložiště](../role-based-access-control/built-in-roles.md#storage-blob-data-owner) zahrnuje nadměrné oprávnění pro operaci čtení.


## <a name="reporting-issues"></a>Vytváření sestav – problémy
[!INCLUDE [Reporting Issues](../../includes/functions-reporting-issues.md)]

## <a name="next-steps"></a>Další kroky
Další informace naleznete v následujících zdrojích:

* [Aktivační události a vazby Azure Functions](functions-triggers-bindings.md)
* [Místní psaní kódu a testování funkcí Azure Functions](./functions-develop-local.md)
* [Osvědčené postupy pro Azure Functions](functions-best-practices.md)
* [Referenční informace pro vývojáře v jazyce C# Azure Functions](functions-dotnet-class-library.md)
* [Referenční informace pro vývojáře Azure Functions Node.js](functions-reference-node.md)
