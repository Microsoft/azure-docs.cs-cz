---
title: 'Rychlý Start: nové přiřazení zásad pomocí JavaScriptu'
description: V tomto rychlém startu pomocí JavaScriptu vytvoříte přiřazení Azure Policy k identifikaci prostředků, které nedodržují předpisy.
ms.date: 03/31/2021
ms.topic: quickstart
ms.custom: devx-track-js
ms.openlocfilehash: d42156e68747b66778ea1cf2001d2b4bfc901bb0
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106090153"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-using-javascript"></a>Rychlý Start: vytvoření přiřazení zásady pro identifikaci prostředků, které nedodržují předpisy, pomocí JavaScriptu

Prvním krokem k porozumění dodržování předpisů v Azure je zjištění stavu vašich prostředků. V tomto rychlém startu vytvoříte přiřazení zásady pro identifikaci virtuálních počítačů, které nepoužívají spravované disky. Po dokončení budete identifikovat virtuální počítače, které _nedodržují předpisy_.

Knihovna JavaScriptu se používá ke správě prostředků Azure z příkazového řádku nebo ve skriptech. V této příručce se dozvíte, jak pomocí knihovny JavaScriptu vytvořit přiřazení zásady.

## <a name="prerequisites"></a>Požadavky

- **Předplatné Azure**: Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný](https://azure.microsoft.com/free/) účet před tím, než začnete.

- **Node.js**: je vyžadována [Node.js](https://nodejs.org/) verze 12 nebo vyšší.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-policy-libraries"></a>Přidat knihovny zásad

Chcete-li povolit jazyk JavaScript pro práci s Azure Policy, knihovny musí být přidány. Tyto knihovny fungují všude, kde je možné použít JavaScript, včetně [bash ve Windows 10](/windows/wsl/install-win10).

1. Vytvořte nový Node.js projekt spuštěním následujícího příkazu.

   ```bash
   npm init -y
   ```

1. Přidejte odkaz na knihovnu yargs.

   ```bash
   npm install yargs
   ```

1. Přidejte odkaz na knihovny Azure Policy.

   ```bash
   # arm-policy is for working with Azure Policy objects such as definitions and assignments
   npm install @azure/arm-policy

   # arm-policyinsights is for working with Azure Policy compliance data such as events and states
   npm install @azure/arm-policyinsights
   ```

1. Přidejte odkaz na knihovnu ověřování Azure.

   ```bash
   npm install @azure/ms-rest-nodeauth
   ```

   > [!NOTE]
   > Ověřte v _package.js_ `@azure/arm-policy` verze **3.1.0** nebo novější, `@azure/arm-policyinsights` je verze **3.2.0** nebo vyšší a `@azure/ms-rest-nodeauth` verze **3.0.5** nebo vyšší.

## <a name="create-a-policy-assignment"></a>Vytvoření přiřazení zásad

V tomto rychlém startu vytvoříte přiřazení zásady a přiřadíte **virtuální počítače auditu, které nepoužívají definici Managed disks** ( `06a78e20-9358-41c9-923c-fb736d382a4d` ). Tato definice zásady identifikuje prostředky, které nesplňují podmínky nastavené v definici zásady.

1. Vytvořte nový soubor s názvem _policyAssignment.js_ a zadejte následující kód.

   ```javascript
   const argv = require("yargs").argv;
   const authenticator = require("@azure/ms-rest-nodeauth");
   const policyObjects = require("@azure/arm-policy");
   
   if (argv.subID && argv.name && argv.displayName && argv.policyDefID && argv.scope && argv.description) {
   
       const createAssignment = async () => {
           const credentials = await authenticator.interactiveLogin();
           const client = new policyObjects.PolicyClient(credentials, argv.subID);
           const assignments = new policyObjects.PolicyAssignments(client);
   
           const result = await assignments.create(
               argv.scope,
               argv.name,
               {
                   displayName: argv.displayName,
                   policyDefinitionId: argv.policyDefID,
                   description: argv.description
               }
           );
           console.log(result);
       };
   
       createAssignment();
   }
   ```

1. Do terminálu zadejte následující příkaz:

   ```bash
   node policyAssignment.js `
      --subID "{subscriptionId}" `
      --name "audit-vm-manageddisks" `
      --displayName "Audit VMs without managed disks Assignment" `
      --policyDefID "/providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d" `
      --description "Shows all virtual machines not using managed disks" `
      --scope "{scope}"
   ```

Předchozí příkazy používají následující informace:

- **subid** – ID předplatného pro kontext ověřování. Nezapomeňte `{subscriptionId}` předplatné nahradit vaším předplatným.
- **název** – jedinečný název objektu přiřazení zásady. Výše uvedený příklad používá _audit-VM-manageddisks_.
- **DisplayName** – zobrazovaný název přiřazení zásady. V takovém případě použijete _přiřazení audit virtuálních počítačů bez spravovaných disků_.
- **policyDefID** – cesta k definici zásad na základě toho, kterou používáte k vytvoření přiřazení. V tomto případě se jedná o ID _virtuálních počítačů auditu definice zásad, které nepoužívají spravované disky_.
- **Popis** – podrobnější vysvětlení toho, co zásada dělá nebo proč je přiřazena tomuto oboru.
- **Scope** – obor určuje, pro které prostředky nebo skupiny prostředků se toto přiřazení zásady bude uplatňovat. Může být v rozsahu od skupiny pro správu k individuálnímu prostředku. Nezapomeňte nahradit `{scope}` jedním z následujících způsobů:
  - Skupina pro správu: `/providers/Microsoft.Management/managementGroups/{managementGroup}`
  - Předplatné: `/subscriptions/{subscriptionId}`
  - Skupina prostředků: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}`
  - Partner `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/[{parentResourcePath}/]`

Nyní můžete identifikovat nekompatibilní prostředky, abyste pochopili stav dodržování předpisů vašeho prostředí.

## <a name="identify-non-compliant-resources"></a>Zjištění nevyhovujících prostředků

Teď, když se vytvoří přiřazení zásady, můžete identifikovat prostředky, které nedodržují předpisy.

1. Vytvořte nový soubor s názvem _policyState.js_ a zadejte následující kód.

   ```javascript
   const argv = require("yargs").argv;
   const authenticator = require("@azure/ms-rest-nodeauth");
   const policyInsights = require("@azure/arm-policyinsights");
   
   if (argv.subID && argv.name) {
   
       const getStates = async () => {
   
           const credentials = await authenticator.interactiveLogin();
           const client = new policyInsights.PolicyInsightsClient(credentials);
           const policyStates = new policyInsights.PolicyStates(client);
           const result = await policyStates.listQueryResultsForSubscription(
               "latest",
               argv.subID,
               {
                   queryOptions: {
                       filter: "IsCompliant eq false and PolicyAssignmentId eq '" + argv.name + "'",
                       apply: "groupby((ResourceId))"
                   }
               }
           );
           console.log(result);
       };
   
       getStates();
   }
   ```

1. Do terminálu zadejte následující příkaz:

   ```bash
   node policyState.js --subID "{subscriptionId}" --name "audit-vm-manageddisks"
   ```

Nahraďte `{subscriptionId}` předplatným, které chcete zobrazit výsledky dodržování předpisů pro přiřazení zásady s názvem audit-VM-manageddisks, který jsme vytvořili v předchozích krocích. Seznam dalších oborů a způsobů sumarizace dat naleznete v tématu [PolicyStates *](/javascript/api/@azure/arm-policyinsights/) Methods.

Vaše výsledky budou vypadat přibližně jako v následujícím příkladu:

```output
{
    'additional_properties': {
        '@odata.nextLink': None
    },
    'odatacontext': 'https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest',
    'odatacount': 12,
    'value': [{data}]
}
```

Výsledky se shodují s tím, co vidíte na kartě **Kompatibilita prostředků** v přiřazení zásady v zobrazení Azure Portal.

## <a name="clean-up-resources"></a>Vyčištění prostředků

- Odstraňte přiřazení zásad _audit virtuálních počítačů bez spravovaných disků_ prostřednictvím portálu. Definice zásad je integrovaná, takže není k dispozici žádná definice k odebrání.

- Pokud chcete odebrat nainstalované knihovny z aplikace, spusťte následující příkaz.

  ```bash
  npm uninstall @azure/arm-policy @azure/arm-policyinsights @azure/ms-rest-nodeauth yargs
  ```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste přiřadili definici zásady pro identifikaci prostředků, které nedodržují předpisy, ve vašem prostředí Azure.

Další informace o přiřazení definic zásad pro ověření, že jsou nové prostředky kompatibilní, najdete v tomto kurzu:

> [!div class="nextstepaction"]
> [Vytváření a správa zásad](./tutorials/create-and-manage.md)