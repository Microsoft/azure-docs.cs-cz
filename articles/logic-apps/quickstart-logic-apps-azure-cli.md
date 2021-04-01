---
title: Vytváření a Správa aplikací logiky pomocí Azure CLI
description: Pomocí rozhraní příkazového řádku Azure můžete vytvořit aplikaci logiky a potom spravovat aplikaci logiky pomocí operací, jako je seznam, zobrazit (získat), aktualizovat a odstranit.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm
ms.topic: quickstart
ms.custom: mvc, devx-track-azurecli, contperf-fy21q2
ms.date: 11/23/2020
ms.openlocfilehash: bc172fd1702addf8f4e34094452a779b09320a4d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "97033370"
---
# <a name="quickstart-create-and-manage-logic-apps-using-the-azure-cli"></a>Rychlý Start: vytváření a Správa aplikací logiky pomocí Azure CLI

V tomto rychlém startu se dozvíte, jak vytvářet a spravovat Logic Apps pomocí [rozšíření Azure CLI Logic Apps](/cli/azure/ext/logic/logic) ( `az logic` ). Z příkazového řádku můžete vytvořit aplikaci logiky pomocí souboru JSON pro definici pracovního postupu aplikace logiky. Aplikaci logiky pak můžete spravovat spuštěním operací, jako například `list` , `show` ( `get` ), `update` a `delete` z příkazového řádku.

> [!WARNING]
> Rozšíření Azure CLI Logic Apps v současné době *experimentální* a nezabývá se *zákaznickou podporou*. Toto rozšíření CLI používejte opatrně, zejména pokud se rozhodnete použít rozšíření v produkčním prostředí.

Pokud s Logic Apps začínáte, můžete se také seznámit s tím, jak vytvořit své první aplikace logiky [prostřednictvím Azure Portal](quickstart-create-first-logic-app-workflow.md), [v aplikaci Visual Studio](quickstart-create-logic-apps-with-visual-studio.md)a [v Visual Studio Code](quickstart-create-logic-apps-visual-studio-code.md).

## <a name="prerequisites"></a>Požadavky

* Účet Azure s aktivním předplatným. Pokud nemáte předplatné Azure, [Vytvořte si bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli) nainstalované na místním počítači.
* V počítači je nainstalovaná [Logic Apps rozšíření Azure CLI](/cli/azure/azure-cli-extensions-list) . Chcete-li nainstalovat toto rozšíření, použijte tento příkaz: `az extension add --name logic`
* [Skupina prostředků Azure](#example---create-resource-group) , ve které se má vytvořit aplikace logiky

### <a name="prerequisite-check"></a>Kontrola požadovaných součástí

Než začnete, ověřte si prostředí:

* Přihlaste se k Azure Portal a ověřte, že je vaše předplatné aktivní spuštěním `az login` .
* Podívejte se na verzi rozhraní příkazového řádku Azure CLI v terminálu nebo příkazovém okně spuštěním příkazu `az --version` . Nejnovější verzi najdete v [poznámkách k nejnovější verzi](/cli/azure/release-notes-azure-cli?tabs=azure-cli).
  * Pokud nemáte nejnovější verzi, aktualizujte instalaci pomocí [instalační příručky pro váš operační systém nebo platformu](/cli/azure/install-azure-cli).

### <a name="example---create-resource-group"></a>Příklad – vytvoření skupiny prostředků

Pokud ještě nemáte skupinu prostředků pro vaši aplikaci logiky, vytvořte skupinu pomocí příkazu `az group create` . Například následující příkaz vytvoří skupinu prostředků s názvem `testResourceGroup` v umístění `westus` .

```azurecli-interactive

az group create --name testResourceGroup --location westus

```

Výstup ukazuje, `provisioningState` Jak `Succeeded` se vaše skupina prostředků úspěšně vytvořila:

```output

<...>
  "name": "testResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
<...>

```

## <a name="workflow-definition"></a>Definice pracovního postupu

Než [vytvoříte novou aplikaci logiky](#create-logic-apps-from-cli) nebo [aktualizujete existující aplikaci logiky](#update-logic-apps-from-cli) pomocí rozhraní příkazového řádku Azure, budete potřebovat definici pracovního postupu pro vaši aplikaci logiky. V Azure Portal můžete zobrazit definici zdrojové pracovní postup aplikace logiky ve formátu JSON přepnutím ze zobrazení **Návrháře** do **zobrazení kódu**.

Když spustíte příkazy pro vytvoření nebo aktualizaci aplikace logiky, vaše definice pracovního postupu se nahraje jako požadovaný parametr ( `--definition` ). Definici pracovního postupu musíte vytvořit jako soubor JSON, který následuje po [schématu jazyka definice pracovního postupu](./logic-apps-workflow-definition-language.md).

## <a name="create-logic-apps-from-cli"></a>Vytváření aplikací logiky z CLI

Pracovní postup aplikace logiky můžete vytvořit z Azure CLI pomocí příkazu [`az logic workflow create`](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-create) se souborem JSON pro definici.

```azurecli

az logic workflow create --definition
                         --location
                         --name
                         --resource-group
                         [--access-control]
                         [--endpoints-configuration]
                         [--integration-account]
                         [--integration-service-environment]
                         [--state {Completed, Deleted, Disabled, Enabled, NotSpecified, Suspended}]
                         [--tags]

```

Váš příkaz musí zahrnovat tyto [povinné parametry](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-create-required-parameters):

| Parametr | Hodnota | Popis |
| --------- | ----- | ----------- |
| Definice pracovního postupu | `--definition` | Soubor JSON s [definicí pracovního postupu](#workflow-definition)vaší aplikace logiky |
| Umístění | `--location -l` | Oblast Azure, ve které se nachází vaše aplikace logiky |
| Name | `--name -n` | Název vaší aplikace logiky Název může obsahovat jenom písmena, číslice, spojovníky ( `-` ), podtržítka ( `_` ), kulaté závorky ( `()` ) a tečky ( `.` ). Název musí být také v různých oblastech jedinečný. |
| Název skupiny prostředků | `--resource-group -g` | [Skupina prostředků Azure](../azure-resource-manager/management/overview.md) , ve které chcete vytvořit aplikaci logiky. Než začnete, [vytvořte skupinu prostředků](#example---create-resource-group) , pokud ji ještě nemáte k dispozici pro vaši aplikaci logiky. |

Můžete také zahrnout další [volitelné parametry](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-create-optional-parameters) pro konfiguraci ovládacích prvků přístupu aplikace logiky, koncových bodů, účtu integrace, prostředí integrační služby, stavu a značek prostředků.

### <a name="example---create-logic-app"></a>Příklad – vytvoření aplikace logiky

V tomto příkladu `testLogicApp` je ve skupině prostředků v umístění vytvořen pracovní postup s názvem `testResourceGroup` `westus` . Soubor JSON `testDefinition.json` obsahuje definici pracovního postupu.

```azurecli-interactive

az logic workflow create --resource-group "testResourceGroup" --location "westus" --name "testLogicApp" --definition "testDefinition.json"

```

Po úspěšném vytvoření pracovního postupu zobrazí rozhraní příkazového řádku kód JSON vaší definice nového pracovního postupu. Pokud se vaše pracovní postup nepodařilo vytvořit, podívejte se na [seznam možných chyb](#errors).

## <a name="update-logic-apps-from-cli"></a>Aktualizace Logic Apps z CLI

Pracovní postup aplikace logiky můžete také aktualizovat z Azure CLI pomocí příkazu [`az logic workflow create`](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-create) .

Příkaz musí zahrnovat stejné [požadované parametry](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-create-required-parameters) , jako když [vytvoříte aplikaci logiky](#create-logic-apps-from-cli). Můžete také přidat stejné [volitelné parametry](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-create-optional-parameters) , jako při vytváření aplikace logiky.

```azurecli

az logic workflow create --definition
                         --location
                         --name
                         --resource-group
                         [--access-control]
                         [--endpoints-configuration]
                         [--integration-account]
                         [--integration-service-environment]
                         [--state {Completed, Deleted, Disabled, Enabled, NotSpecified, Suspended}]
                         [--tags]

```

### <a name="example---update-logic-app"></a>Příklad – aktualizace aplikace logiky

V tomto příkladu je [ukázkový pracovní postup vytvořený v předchozí části](#example---create-logic-app) aktualizován tak, aby používal jiný soubor definice JSON, `newTestDefinition.json` a přidal dvě značky prostředku `testTag1` a `testTag2` s popisem hodnot.

```azurecli-interactive

az logic workflow create --resource-group "testResourceGroup" --location "westus" --name "testLogicApp" --definition "newTestDefinition.json" --tags "testTag1=testTagValue1" "testTag2=testTagValue"

```

Po úspěšné aktualizaci pracovního postupu rozhraní příkazového řádku zobrazí definici aktualizovaného pracovního postupu aplikace logiky. Pokud se vaše aktualizace nezdařila, přečtěte si [seznam možných chyb](#errors).

## <a name="delete-logic-apps-from-cli"></a>Odstranění Logic Apps z CLI

Pracovní postup aplikace logiky můžete z Azure CLI odstranit pomocí příkazu [`az logic workflow delete`](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-delete) .

Váš příkaz musí zahrnovat tyto [povinné parametry](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-delete-required-parameters):

| Parametr | Hodnota | Popis |
| --------- | ----- | ----------- |
| Název | `--name -n` | Název vaší aplikace logiky |
| Název skupiny prostředků | `-resource-group -g` | Skupina prostředků, ve které se nachází vaše aplikace logiky |

Můžete také zahrnout [volitelný parametr](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-delete-optional-parameters) pro přeskočení výzev k potvrzení, `--yes -y` .

```azurecli

az logic workflow delete --name
                         --resource-group
                         [--yes]

```

Rozhraní příkazového řádku vás vyzve k potvrzení odstranění vaší aplikace logiky. Výzvu k potvrzení můžete přeskočit pomocí volitelného parametru `--yes -y` s vaším příkazem.

```azurecli

Are you sure you want to perform this operation? (y/n):

```

Odstranění aplikace logiky můžete potvrdit uvedením [v rozhraní](#list-logic-apps-in-cli)příkazového řádku nebo zobrazením Logic apps v Azure Portal.

### <a name="example---delete-logic-app"></a>Příklad – odstranění aplikace logiky

V tomto příkladu se odstranil [ukázkový pracovní postup vytvořený v předchozí části](#example---create-logic-app) .

```azurecli-interactive

az logic workflow delete --resource-group "testResourceGroup" --name "testLogicApp"

```

Po reakci na výzvu k potvrzení pomocí nástroje `y` se aplikace logiky odstraní.

## <a name="show-logic-apps-in-cli"></a>Zobrazit Logic Apps v rozhraní příkazového řádku

Konkrétní pracovní postup aplikace logiky můžete získat pomocí příkazu [`az logic workflow show`](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-show) .

```azurecli

az logic workflow show --name
                       --resource-group

```

Váš příkaz musí zahrnovat tyto [povinné parametry](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-show-required-parameters) :

| Parametr | Hodnota | Popis |
| --------- | ----- | ----------- |
| Název | `--name -n` | Název vaší aplikace logiky |
| Název skupiny prostředků | `--resource-group -g` | Název skupiny prostředků, ve které se aplikace logiky nachází. |

### <a name="example---get-logic-app"></a>Příklad – získání aplikace logiky

V tomto příkladu se aplikace logiky `testLogicApp` ve skupině prostředků `testResourceGroup` vrátí pomocí úplných protokolů pro ladění.

```azurecli-interactive

az logic workflow show --resource-group "testResourceGroup" --name "testLogicApp" --debug

```

## <a name="list-logic-apps-in-cli"></a>Seznam aplikací logiky v rozhraní příkazového řádku

Pomocí příkazu můžete vypsat své aplikace logiky podle předplatného [`az logic workflow list`](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-list) . Tento příkaz vrátí kód JSON pro pracovní postupy Logic Apps.

Výsledky můžete filtrovat podle následujících [volitelných parametrů](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-list-optional-parameters):

| Parametr | Hodnota | Popis |
| --------- | ----- | ----------- |
| Název skupiny prostředků | `--resource-group -g` | Název skupiny prostředků, podle které chcete filtrovat výsledky. |
| Počet položek | `--top` | Počet položek, které jsou součástí vašich výsledků. |
| Filtrovat | `--filter` | Typ filtru, který používáte v seznamu. Můžete filtrovat podle stavu ( `State` ), triggeru ( `Trigger` ) a identifikátoru odkazovaného prostředku ( `ReferencedResourceId` ). |

```azurecli

az logic workflow list [--filter]
                       [--resource-group]
                       [--top]

```

### <a name="example---list-logic-apps"></a>Příklad – seznam aplikací logiky

V tomto příkladu jsou všechny povolené pracovní postupy ve skupině prostředků `testResourceGroup` vracené ve formátu tabulky ASCII.

```azurecli-interactive

az logic workflow list --resource-group "testResourceGroup" --filter "(State eq 'Enabled')" --output "table"

```

## <a name="errors"></a>Chyby

Následující chyba znamená, že rozšíření rozhraní příkazového řádku Azure Logic Apps není nainstalováno. Podle pokynů v části požadavky [nainstalujte Logic Apps rozšíření](#prerequisites) na svém počítači.

```output

az: 'logic' is not in the 'az' command group. See 'az --help'. If the command is from an extension, please make sure the corresponding extension is installed. To learn more about extensions, please visit https://docs.microsoft.com/cli/azure/azure-cli-extensions-overview

```

Následující chyba může znamenat, že cesta k souboru pro nahrání definice pracovního postupu není správná.

```output

Expecting value: line 1 column 1 (char 0)

```

## <a name="global-parameters"></a>Globální parametry

K vašim příkazům můžete použít následující volitelné parametry globálního rozhraní příkazového řádku Azure `az logic` :

| Parametr | Hodnota | Popis |
| --------- | ----- | ----------- |
| Výstupní formát | `--output -o` | Změňte [výstupní formát](/cli/azure/format-output-azure-cli) z výchozího formátu JSON. |
| Zobrazit pouze chyby | `--only-show-errors` | Potlačí upozornění a zobrazí pouze chyby. |
| Verbose | `--verbose` | Zobrazit podrobné protokoly. |
| Ladění | `--debug` | Zobrazí všechny protokoly ladění. |
| Zpráva o nápovědě | `--help -h` | Zobrazit dialogové okno help. |
| Dotaz | `--query` | Nastavte řetězec dotazu JMESPath pro výstup JSON. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v dokumentaci k rozhraní příkazového [řádku Azure CLI](/cli/azure/).

Logic Apps Další ukázkové skripty rozhraní příkazového řádku najdete v [prohlížeči ukázek kódu Microsoftu](/samples/browse/?products=azure-logic-apps).

V dalším kroku můžete vytvořit ukázkovou logiku aplikace prostřednictvím Azure CLI pomocí ukázkového skriptu a definice pracovního postupu.

> [!div class="nextstepaction"]
> [Vytvoření aplikace logiky pomocí ukázkového skriptu](sample-logic-apps-cli-script.md)
