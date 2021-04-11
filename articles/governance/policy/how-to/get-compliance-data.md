---
title: Získat data dodržování zásad
description: Azure Policy hodnocení a účinky určují dodržování předpisů. Přečtěte si, jak získat podrobnosti o dodržování předpisů pro vaše prostředky Azure.
ms.date: 03/16/2021
ms.topic: how-to
ms.openlocfilehash: cdd23d685750fb8a5d3803f4b6030e7e67bbddce
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104598537"
---
# <a name="get-compliance-data-of-azure-resources"></a>Získání dat o dodržování předpisů u prostředků Azure

Jednou z největších výhod Azure Policy je přehled a ovládací prvky, které poskytuje nad prostředky v rámci předplatného nebo [skupiny pro správu](../../management-groups/overview.md) předplatných. Tento ovládací prvek lze uplatnit mnoha různými způsoby, například zabránit vytváření prostředků v nesprávném umístění, vynucování společného a konzistentního použití značek nebo auditování existujících prostředků pro příslušné konfigurace a nastavení. Ve všech případech jsou data generována Azure Policy, která vám umožní pochopit stav dodržování předpisů vašeho prostředí.

Existuje několik způsobů, jak získat přístup k informacím o dodržování předpisů generovanými pomocí zásad a přiřazení iniciativ:

- Použití [Azure Portal](#portal)
- Pomocí skriptování na [příkazovém řádku](#command-line)

Než začnete se zaměřením na dodržování předpisů, Podívejme se na to, kdy se aktualizují informace o dodržování předpisů a četnost a události, které aktivují zkušební cyklus.

> [!WARNING]
> Pokud je stav dodržování předpisů hlášen jako **neregistrované**, ověřte, zda je poskytovatel prostředků **Microsoft. PolicyInsights** zaregistrován a zda má uživatel příslušná oprávnění řízení přístupu na základě role Azure (RBAC), jak je popsáno v tématu [oprávnění Azure RBAC v Azure Policy](../overview.md#azure-rbac-permissions-in-azure-policy).

## <a name="evaluation-triggers"></a>Aktivační události vyhodnocení

Výsledky dokončeného cyklu vyhodnocení jsou k dispozici ve `Microsoft.PolicyInsights` zprostředkovateli prostředků prostřednictvím `PolicyStates` `PolicyEvents` operací a. Další informace o operacích REST API Azure Policy Insights najdete v tématu [Azure Policy Insights](/rest/api/policy/).

K vyhodnocení přiřazených zásad a iniciativ dojde v důsledku různých událostí:

- Zásada nebo iniciativa je nově přiřazena k oboru. Použití přiřazení pro definovaný obor trvá přibližně 30 minut. Jakmile se použije, cyklus hodnocení začne u prostředků v tomto rozsahu s nově přiřazenou zásadou nebo iniciativou a v závislosti na účincích používaných zásadami nebo iniciativou jsou prostředky označené jako kompatibilní, nekompatibilní nebo vyloučené. Velký počet zásad nebo iniciativ vyhodnocených pro velký rozsah prostředků může chvíli trvat. V takovém případě neexistuje předem definované očekávání po dokončení zkušebního cyklu. Po dokončení budou aktualizované výsledky dodržování předpisů k dispozici na portálu a sadách SDK.

- Zásada nebo iniciativa, která je již přiřazena k oboru, je aktualizována. Cyklus hodnocení a časování pro tento scénář je stejný jako u nového přiřazení k oboru.

- Prostředek se nasazuje nebo aktualizuje v rámci oboru s přiřazením prostřednictvím Azure Resource Manager, REST API nebo podporované sady SDK. V tomto scénáři se na portálu budou k dispozici informace o vlivu události (připojení, audit, zamítnutí, nasazení) a odpovídajících informací o stavu pro jednotlivé prostředky na portálu a sady SDK o 15 minutách. Tato událost nezpůsobí vyhodnocení dalších prostředků.

- [Výjimka zásad](../concepts/exemption-structure.md) je vytvořena, aktualizována nebo odstraněna. V tomto scénáři se odpovídající přiřazení vyhodnocuje pro definovaný obor výjimky.

- Standardní cyklus hodnocení dodržování předpisů. Každých 24 hodin se přiřazení automaticky přehodnotí. Velké zásady nebo podněty mnoha prostředků můžou nějakou dobu trvat, takže není k dispozici předem definovaná Očekávaná doba, po které se zkušební cyklus dokončí. Po dokončení budou aktualizované výsledky dodržování předpisů k dispozici na portálu a sadách SDK.

- Poskytovatel prostředků [Konfigurace hosta](../concepts/guest-configuration.md) se aktualizuje s podrobnostmi o dodržování předpisů spravovaným prostředkem.

- Kontrola na vyžádání

### <a name="on-demand-evaluation-scan"></a>Prohledávání hodnocení na vyžádání

Kontrolu vyhodnocení předplatného nebo skupiny prostředků můžete spustit pomocí Azure CLI, Azure PowerShell, volání REST API nebo pomocí [Akce GitHubu pro kontrolu kompatibility Azure Policy](https://github.com/marketplace/actions/azure-policy-compliance-scan).
Tato kontrola je asynchronní proces.

#### <a name="on-demand-evaluation-scan---github-action"></a>Prověření vyhodnocení na vyžádání – akce GitHubu

Pomocí [akce Azure Policy kontrola dodržování předpisů](https://github.com/marketplace/actions/azure-policy-compliance-scan) můžete aktivovat kontrolu vyhodnocení na vyžádání z [pracovního postupu GitHubu](https://docs.github.com/actions/configuring-and-managing-workflows/configuring-a-workflow#about-workflows) na jednom nebo několika prostředcích, skupinách prostředků nebo předplatných a bránu pracovní postup na základě stavu dodržování předpisů u prostředků. Pracovní postup můžete také nakonfigurovat tak, aby běžel v naplánovaném čase, abyste získali nejnovější stav dodržování předpisů v pohodlný čas. Tuto akci GitHubu můžete volitelně použít k vygenerování sestavy o stavu dodržování předpisů u prověřených prostředků pro další analýzu nebo pro archivaci.

Následující příklad spustí kontrolu dodržování předpisů u předplatného. 

```yaml
on:
  schedule:    
    - cron:  '0 8 * * *'  # runs every morning 8am
jobs:
  assess-policy-compliance:    
    runs-on: ubuntu-latest
    steps:         
    - name: Login to Azure
      uses: azure/login@v1
      with:
        creds: ${{secrets.AZURE_CREDENTIALS}} 

    
    - name: Check for resource compliance
      uses: azure/policy-compliance-scan@v0
      with:
        scopes: |
          /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

Další informace a ukázky pracovních postupů najdete v článku [Akce GitHubu pro úložiště vyhledávání dodržování předpisů Azure Policy](https://github.com/Azure/policy-compliance-scan).

#### <a name="on-demand-evaluation-scan---azure-cli"></a>Kontrola vyhodnocení na vyžádání – Azure CLI

Kontrola dodržování předpisů se spouští pomocí příkazu [AZ Policy State Trigger-Scan](/cli/azure/policy/state#az_policy_state_trigger_scan) .

Ve výchozím nastavení `az policy state trigger-scan` spustí vyhodnocení pro všechny prostředky v aktuálním předplatném. Pokud chcete zahájit vyhodnocení konkrétní skupiny prostředků, použijte parametr **Resource-Group** . Následující příklad spustí kontrolu dodržování předpisů v aktuálním předplatném pro skupinu prostředků _MyRG_ :

```azurecli-interactive
az policy state trigger-scan --resource-group "MyRG"
```

Můžete zvolit, že nechcete čekat na dokončení asynchronního procesu, než budete pokračovat s parametrem **No-Wait** .

#### <a name="on-demand-evaluation-scan---azure-powershell"></a>Kontrola vyhodnocení na vyžádání – Azure PowerShell

Kontrola dodržování předpisů se spouští pomocí rutiny [Start-AzPolicyComplianceScan](/powershell/module/az.policyinsights/start-azpolicycompliancescan) .

Ve výchozím nastavení `Start-AzPolicyComplianceScan` spustí vyhodnocení pro všechny prostředky v aktuálním předplatném. Pokud chcete zahájit vyhodnocení konkrétní skupiny prostředků, použijte parametr **ResourceGroupName** . Následující příklad spustí kontrolu dodržování předpisů v aktuálním předplatném pro skupinu prostředků _MyRG_ :

```azurepowershell-interactive
Start-AzPolicyComplianceScan -ResourceGroupName 'MyRG'
```

PowerShell může počkat na dokončení asynchronního volání před zadáním výstupu výsledků nebo jeho spuštěním na pozadí jako [úloha](/powershell/module/microsoft.powershell.core/about/about_jobs). Chcete-li pomocí úlohy PowerShell spustit kontrolu kompatibility na pozadí, použijte parametr **AsJob** a nastavte hodnotu na objekt, jako `$job` v tomto příkladu:

```azurepowershell-interactive
$job = Start-AzPolicyComplianceScan -AsJob
```

Stav úlohy můžete zkontrolovat zaškrtnutím tohoto `$job` objektu. Úloha je typu `Microsoft.Azure.Commands.Common.AzureLongRunningJob` . Použijte k `Get-Member` `$job` zobrazení dostupných vlastností a metod na objektu.

Kontrola kompatibility je spuštěná a kontroluje `$job` výsledky výstupů objektů, jako jsou tyto:

```azurepowershell-interactive
$job

Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
2      Long Running O… AzureLongRunni… Running       True            localhost            Start-AzPolicyCompliance…
```

Po dokončení kontroly dodržování předpisů se vlastnost **stav** změní na _dokončeno_.

#### <a name="on-demand-evaluation-scan---rest"></a>Kontrola vyhodnocení na vyžádání – REST

V rámci asynchronního procesu koncový bod REST ke spuštění kontroly nečeká na odpověď, dokud se kontrola nedokončí. Místo toho poskytuje identifikátor URI pro dotaz na stav vyžádaného vyhodnocení.

Každý identifikátor URI v REST API používá proměnné, které je potřeba nahradit vašimi vlastními hodnotami:

- `{YourRG}` – Nahraďte názvem vaší skupiny prostředků.
- Proměnnou `{subscriptionId}` nahraďte ID předplatného.

Kontrola podporuje vyhodnocení prostředků v rámci předplatného nebo ve skupině prostředků. Spusťte kontrolu pomocí příkazu REST API **post** pomocí následujících struktur identifikátorů URI:

- Předplatné

  ```http
  POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/triggerEvaluation?api-version=2019-10-01
  ```

- Skupina prostředků

  ```http
  POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{YourRG}/providers/Microsoft.PolicyInsights/policyStates/latest/triggerEvaluation?api-version=2019-10-01
  ```

Volání vrátí stav **přijato 202** . Zahrnuté v hlavičce odpovědi je vlastnost **umístění** s následujícím formátem:

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/asyncOperationResults/{ResourceContainerGUID}?api-version=2019-10-01
```

`{ResourceContainerGUID}` je staticky generován pro požadovaný rozsah. Pokud je v oboru již spuštěno prohledávání na vyžádání, nová kontrola není spuštěna. Místo toho se nové žádosti dodávají stejnému `{ResourceContainerGUID}` identifikátoru URI **umístění** pro stav. Příkaz REST API **Get** do identifikátoru URI pro **umístění** vrací **202 přijatý** , zatímco probíhá vyhodnocení. Po dokončení kontroly vyhodnocení vrátí stav **200 OK** . Tělo dokončeného prohledávání je odpověď JSON se stavem:

```json
{
    "status": "Succeeded"
}
```

#### <a name="on-demand-evaluation-scan---visual-studio-code"></a>Kontrola vyhodnocení na vyžádání – Visual Studio Code

Rozšíření Azure Policy pro Visual Studio Code je schopné spustit kontrolu vyhodnocení konkrétního prostředku. Tato kontrola je synchronní proces, na rozdíl od metod Azure PowerShell a REST.
Podrobnosti a postup najdete v tématu [vyhodnocení na vyžádání s rozšířením vs Code](./extension-for-vscode.md#on-demand-evaluation-scan).

## <a name="how-compliance-works"></a>Jak dodržování předpisů funguje

V přiřazení není prostředek **nekompatibilní** , pokud nedodržuje pravidla zásad nebo iniciativ a není _vyloučený_. Následující tabulka ukazuje, jak různé účinky zásad fungují s hodnocením podmínek pro výsledný stav dodržování předpisů:

| Stav prostředku | Účinek | Vyhodnocení zásad | Stav dodržování předpisů |
| --- | --- | --- | --- |
| Nové nebo aktualizované | Audit, úprava, AuditIfNotExist | Ano | Nevyhovující předpisům |
| Nové nebo aktualizované | Audit, úprava, AuditIfNotExist | Ne | Odpovídající |
| Existuje | Deny, audit, připojit, upravit, DeployIfNotExist, AuditIfNotExist | Ano | Nevyhovující předpisům |
| Existuje | Deny, audit, připojit, upravit, DeployIfNotExist, AuditIfNotExist | Ne | Odpovídající |

> [!NOTE]
> Účinky DeployIfNotExist a AuditIfNotExist vyžadují, aby příkaz IF byl pravdivý a aby podmínka existence neodpovídala hodnotě FALSE. Pokud má hodnotu TRUE, aktivuje podmínka IF vyhodnocení podmínky existence pro související prostředky.

Předpokládejme například, že máte skupinu prostředků – ContsoRG s některými účty úložiště (zvýrazněné červeně), které jsou vystaveny veřejným sítím.

:::image type="complex" source="../media/getting-compliance-data/resource-group01.png" alt-text="Diagram účtů úložiště zveřejněných pro veřejné sítě ve skupině prostředků contoso R G" border="false":::
   Diagram znázorňující obrázky pro pět účtů úložiště ve skupině prostředků contoso R G  Účty úložiště One a tři jsou modré, zatímco účty úložiště dvě, čtyři a pět jsou červené.
:::image-end:::

V tomto příkladu je třeba přistupují opatrně rizika zabezpečení. Teď, když jste vytvořili přiřazení zásady, se vyhodnotí pro všechny zahrnuté a nevyňaté účty úložiště ve skupině prostředků ContosoRG. Audituje tři účty úložiště, které nedodržují předpisy, proto mění stavy na **nevyhovující předpisům.**

:::image type="complex" source="../media/getting-compliance-data/resource-group03.png" alt-text="Diagram dodržování předpisů účtu úložiště ve skupině prostředků contoso R G" border="false":::
   Diagram znázorňující obrázky pro pět účtů úložiště ve skupině prostředků contoso R G Účty úložiště: 1 a tři teď mají zelenou zaškrtnutí pod nimi, zatímco účty úložiště dvě, čtyři a pět nyní mají červené upozornění pod nimi.
:::image-end:::

Kromě **kompatibilních** a **nekompatibilních** zásad a prostředků mají čtyři další stavy:

- **Výjimka**: prostředek je v oboru přiřazení, ale má [definovanou výjimku](../concepts/exemption-structure.md).
- **Konflikt**: Existují dvě nebo více definic zásad s konfliktními pravidly. Například dvě definice připojí stejnou značku s různými hodnotami.
- **Nezahájeno**: cyklus vyhodnocení se nespustil pro zásady nebo prostředek.
- **Neregistrováno**: poskytovatel prostředků Azure Policy nebyl zaregistrován nebo účet přihlášený nemá oprávnění ke čtení dat dodržování předpisů.

Azure Policy používá pole **typ**, **název** nebo **druh** v definici k určení, zda se jedná o shodu prostředku. Pokud se prostředek shoduje, je považován za platný a má stav buď **kompatibilní**, **nekompatibilní** nebo **vyloučený**. Pokud je v definici jedinou vlastností **typu**, **název** nebo **druh** , jsou všechny zahrnuté a nevyňaté prostředky považovány za použitelné a vyhodnocují se.

Procento dodržování předpisů je určeno vydělením **kompatibilních** a **osvobozených** prostředků _celkovými prostředky_. _Celkem prostředků_ je definováno jako součet **kompatibilních** a **nekompatibilních, nekompatibilních** a **konfliktních** prostředků.  Celková čísla dodržování předpisů jsou součtem různých prostředků, které jsou v souladu s **předpisy** , a jejich **vyloučení** je dělené součtem všech různých prostředků. Na následujícím obrázku je více než 20 různých prostředků, které jsou k dispozici, a pouze jeden z nich **nedodržuje předpisy**.
Celkové dodržování předpisů prostředků je 95% (19 z 20).

:::image type="content" source="../media/getting-compliance-data/simple-compliance.png" alt-text="Snímek obrazovky s podrobnostmi o dodržování předpisů zásad ze stránky dodržování předpisů." border="false":::

> [!NOTE]
> Dodržování legislativních předpisů v Azure Policy je funkce ve verzi Preview. Vlastnosti dodržování předpisů ze sady SDK a stránky na portálu se liší od povolených iniciativ. Další informace najdete v tématu [dodržování legislativních předpisů](../concepts/regulatory-compliance.md) .

## <a name="portal"></a>Portál

Azure Portal prezentuje grafické prostředí pro vizualizaci a porozumění stavu dodržování předpisů ve vašem prostředí. Možnost **Přehled** na stránce **zásady** poskytuje podrobnosti pro dostupné obory kompatibility obou zásad i iniciativ. Spolu se stavem a počtem dodržování předpisů na přiřazení obsahuje graf, který zobrazuje dodržování předpisů během posledních sedmi dnů. Stránka **dodržování předpisů** obsahuje mnoho stejných informací (s výjimkou grafu), ale nabízí další možnosti filtrování a řazení.

:::image type="content" source="../media/getting-compliance-data/compliance-page.png" alt-text="Obrazovka stránky dodržování předpisů, možnosti filtrování a podrobnosti." border="false":::

Vzhledem k tomu, že může být zásada nebo iniciativa přiřazena k různým oborům, tabulka zahrnuje rozsah pro každé přiřazení a typ definice, která byla přiřazena. K dispozici je také počet neodpovídajících prostředků a nevyhovujících zásad pro každé přiřazení. Výběr zásad nebo iniciativ v tabulce vám poskytne hlubší přehled o dodržování předpisů pro příslušné přiřazení.

:::image type="content" source="../media/getting-compliance-data/compliance-details.png" alt-text="Snímek obrazovky s podrobnostmi o kompatibilitě, včetně počtů a podrobností odpovídajících prostředkům." border="false":::

Seznam prostředků na kartě **Kompatibilita prostředků** zobrazuje stav vyhodnocení existujících prostředků pro aktuální přiřazení. Karta je standardně **nekompatibilní**, lze ji však filtrovat.
Události (připojit, audit, odepřít, nasadit, upravit) aktivované žádostí o vytvoření prostředku se zobrazí na kartě **události** .

> [!NOTE]
> Pro zásady stroje AKS je zobrazený prostředek skupina prostředků.

:::image type="content" source="../media/getting-compliance-data/compliance-events.png" alt-text="Snímek obrazovky karty události na stránce s podrobnostmi o dodržování předpisů" border="false":::

<a name="component-compliance"></a> V případě prostředků [režimu poskytovatele prostředků](../concepts/definition-structure.md#resource-provider-modes) na kartě **Kompatibilita prostředků** vyberte prostředek, klikněte na něj pravým tlačítkem myši a vyberte **Zobrazit podrobnosti** o dodržování předpisů. otevře se podrobnosti o kompatibilitě komponent. Tato stránka také nabízí karty k zobrazení zásad, které jsou přiřazeny k tomuto prostředku, událostem, událostem komponenty a historii změn.

:::image type="content" source="../media/getting-compliance-data/compliance-components.png" alt-text="Snímek obrazovky karty dodržování předpisů pro součásti a podrobnosti o dodržování předpisů pro přiřazení režimu poskytovatele prostředků." border="false":::

Zpět na stránce kompatibilita prostředků klikněte pravým tlačítkem na řádek události, pro kterou chcete získat další podrobnosti, a vyberte **Zobrazit protokoly aktivit**. Otevře se stránka protokolu aktivit a je předem filtrována na hledání, které zobrazuje podrobnosti o přiřazení a událostech. Protokol aktivit poskytuje další kontext a informace o těchto událostech.

:::image type="content" source="../media/getting-compliance-data/compliance-activitylog.png" alt-text="Snímek obrazovky protokolu aktivit pro Azure Policy aktivity a hodnocení." border="false":::

### <a name="understand-non-compliance"></a>Pochopení nedodržování předpisů

Pokud je zjištěno, že prostředek není **kompatibilní**, existuje mnoho možných důvodů. Informace o tom, proč prostředek **nedodržuje předpisy** nebo najít odpovědné změny, najdete v tématu [určení nedodržení předpisů](./determine-non-compliance.md).

## <a name="command-line"></a>Příkazový řádek

Stejné informace, které jsou k dispozici na portálu, lze načíst pomocí REST API (včetně [ARMClient](https://github.com/projectkudu/ARMClient)), Azure PowerShell a Azure CLI. Úplné podrobnosti o REST API najdete v referenčních informacích k [Azure Policy](/rest/api/policy/) . Referenční stránky REST API mají zeleně stisknuté tlačítko vyzkoušet u každé operace, která vám umožní vyzkoušet si ho přímo v prohlížeči.

Použijte ARMClient nebo podobný nástroj pro zpracování ověřování v Azure pro REST API příklady.

### <a name="summarize-results"></a>Shrnutí výsledků

Díky REST API je souhrn možné provádět pomocí kontejneru, definice nebo přiřazení. Tady je příklad sumarizace na úrovni předplatného, který používá Shrnutí Azure Policyho přehledu [pro předplatné](/rest/api/policy/policystates/summarizeforsubscription):

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/summarize?api-version=2019-10-01
```

Výstup shrnuje předplatné. V následujícím příkladu výstupu jsou shrnuty dodržování předpisů v **hodnotě Value. Results. nonCompliantResources** a **Value. Results. nonCompliantPolicies**. Tato žádost obsahuje další podrobnosti, včetně každého přiřazení, které tvoří nekompatibilní čísla, a informace o definici pro každé přiřazení. Každý objekt zásad v hierarchii poskytuje **queryResultsUri** , který se dá použít k získání dalších podrobností na této úrovni.

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#summary",
    "@odata.count": 1,
    "value": [{
        "@odata.id": null,
        "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#summary/$entity",
        "results": {
            "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=ComplianceState eq 'NonCompliant'",
            "nonCompliantResources": 15,
            "nonCompliantPolicies": 1
        },
        "policyAssignments": [{
            "policyAssignmentId": "/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77",
            "policySetDefinitionId": "",
            "results": {
                "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=ComplianceState eq 'NonCompliant' and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77'",
                "nonCompliantResources": 15,
                "nonCompliantPolicies": 1
            },
            "policyDefinitions": [{
                "policyDefinitionReferenceId": "",
                "policyDefinitionId": "/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "effect": "deny",
                "results": {
                    "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=ComplianceState eq 'NonCompliant' and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77' and PolicyDefinitionId eq '/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62'",
                    "nonCompliantResources": 15
                }
            }]
        }]
    }]
}
```

### <a name="query-for-resources"></a>Dotaz na prostředky

V předchozím příkladu **Value. policyAssignments. policyDefinitions. Results. queryResultsUri** poskytuje ukázkový identifikátor URI pro všechny prostředky, které nedodržují předpisy pro konkrétní definici zásad. Při zobrazení hodnoty **$Filter** se ComplianceState rovná (EQ) až "nekompatibilní", pro definici zásady se zadá PolicyAssignmentId a pak se samotný PolicyDefinitionId sám. Důvodem zahrnutí PolicyAssignmentId do filtru je, že PolicyDefinitionId může existovat v několika zásadách nebo přiřazeních iniciativ s různými obory. Když zadáte jak PolicyAssignmentId, tak PolicyDefinitionId, můžeme být ve výsledcích hledání explicitní. Dříve jsme pro PolicyStates používali **nejnovější**, což automaticky nastaví časové období **z** a **do** za posledních 24 hodin.

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=ComplianceState eq 'NonCompliant' and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77' and PolicyDefinitionId eq '/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62'
```

Ukázková odezva níže byla oříznuta na jeden nekompatibilní prostředek pro zkrácení. Podrobná odpověď má několik částí dat týkajících se prostředku, zásady nebo iniciativy a přiřazení. Všimněte si, že můžete také zjistit, jaké parametry přiřazení byly předány do definice zásady.

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest",
    "@odata.count": 15,
    "value": [{
        "@odata.id": null,
        "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
        "timestamp": "2018-05-19T04:41:09Z",
        "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/rg-tags/providers/Microsoft.Compute/virtualMachines/linux",
        "policyAssignmentId": "/subscriptions/{subscriptionId}/resourceGroups/rg-tags/providers/Microsoft.Authorization/policyAssignments/37ce239ae4304622914f0c77",
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
        "effectiveParameters": "",
        "ComplianceState": "NonCompliant",
        "subscriptionId": "{subscriptionId}",
        "resourceType": "/Microsoft.Compute/virtualMachines",
        "resourceLocation": "westus2",
        "resourceGroup": "RG-Tags",
        "resourceTags": "tbd",
        "policyAssignmentName": "37ce239ae4304622914f0c77",
        "policyAssignmentOwner": "tbd",
        "policyAssignmentParameters": "{\"tagName\":{\"value\":\"costCenter\"},\"tagValue\":{\"value\":\"Contoso-Test\"}}",
        "policyAssignmentScope": "/subscriptions/{subscriptionId}/resourceGroups/RG-Tags",
        "policyDefinitionName": "1e30110a-5ceb-460c-a204-c1c3969c6d62",
        "policyDefinitionAction": "deny",
        "policyDefinitionCategory": "tbd",
        "policySetDefinitionId": "",
        "policySetDefinitionName": "",
        "policySetDefinitionOwner": "",
        "policySetDefinitionCategory": "",
        "policySetDefinitionParameters": "",
        "managementGroupIds": "",
        "policyDefinitionReferenceId": ""
    }]
}
```

### <a name="view-events"></a>Zobrazení událostí

Při vytvoření nebo aktualizaci prostředku se vygeneruje výsledek vyhodnocení zásad. Výsledky se nazývají _události zásad_. K zobrazení posledních událostí zásad přidružených k předplatnému použijte následující identifikátor URI.

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyEvents/default/queryResults?api-version=2019-10-01
```

Vaše výsledky budou vypadat přibližně jako v následujícím příkladu:

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyEvents/$metadata#default",
    "@odata.count": 1,
    "value": [{
        "@odata.id": null,
        "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyEvents/$metadata#default/$entity",
        "NumAuditEvents": 16
    }]
}
```

Další informace o dotazování na události zásad najdete v článku referenční informace o [událostech Azure Policy](/rest/api/policy/policyevents) .

### <a name="azure-cli"></a>Azure CLI

Skupina příkazů [Azure CLI](/cli/azure/what-is-azure-cli) pro Azure Policy pokrývá většinu operací, které jsou k dispozici v REST nebo Azure PowerShell. Úplný seznam dostupných příkazů najdete v tématu [Azure CLI – přehled Azure Policy](/cli/azure/policy).

Příklad: získání souhrnu stavu pro nejvyšší přiřazenou zásadu s největším počtem nevyhovujících prostředků.

```azurecli-interactive
az policy state summarize --top 1
```

Horní část odpovědi vypadá jako v tomto příkladu:

```json
{
    "odatacontext": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#summary/$entity",
    "odataid": null,
    "policyAssignments": [{
            "policyAssignmentId": "/subscriptions/{subscriptionId}/providers/microsoft.authorization/policyassignments/e0704696df5e4c3c81c873e8",
            "policyDefinitions": [{
                "effect": "audit",
                "policyDefinitionGroupNames": [
                    ""
                ],
                "policyDefinitionId": "/subscriptions/{subscriptionId}/providers/microsoft.authorization/policydefinitions/2e3197b6-1f5b-4b01-920c-b2f0a7e9b18a",
                "policyDefinitionReferenceId": "",
                "results": {
                    "nonCompliantPolicies": null,
                    "nonCompliantResources": 398,
                    "policyDetails": [{
                        "complianceState": "noncompliant",
                        "count": 1
                    }],
                    "policyGroupDetails": [{
                        "complianceState": "noncompliant",
                        "count": 1
                    }],
                    "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$from=2020-07-14 14:01:22Z&$to=2020-07-15 14:01:22Z and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/providers/microsoft.authorization/policyassignments/e0704696df5e4c3c81c873e8' and PolicyDefinitionId eq '/subscriptions/{subscriptionId}/providers/microsoft.authorization/policydefinitions/2e3197b6-1f5b-4b01-920c-b2f0a7e9b18a'",
                    "resourceDetails": [{
                            "complianceState": "noncompliant",
                            "count": 398
                        },
                        {
                            "complianceState": "compliant",
                            "count": 4
                        }
                    ]
                }
            }],
    ...
```

Příklad: získání záznamu stavu pro naposledy vyhodnocený prostředek (výchozí je podle časového razítka v sestupném pořadí).

```azurecli-interactive
az policy state list --top 1
```

```json
[
  {
    "complianceReasonCode": "",
    "complianceState": "Compliant",
    "effectiveParameters": "",
    "isCompliant": true,
    "managementGroupIds": "{managementgroupId}",
    "odatacontext": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
    "odataid": null,
    "policyAssignmentId": "/subscriptions/{subscriptionId}/providers/microsoft.authorization/policyassignments/securitycenterbuiltin",
    "policyAssignmentName": "SecurityCenterBuiltIn",
    "policyAssignmentOwner": "tbd",
    "policyAssignmentParameters": "",
    "policyAssignmentScope": "/subscriptions/{subscriptionId}",
    "policyAssignmentVersion": "",
    "policyDefinitionAction": "auditifnotexists",
    "policyDefinitionCategory": "tbd",
    "policyDefinitionGroupNames": [
      ""
    ],
    "policyDefinitionId": "/providers/microsoft.authorization/policydefinitions/aa633080-8b72-40c4-a2d7-d00c03e80bed",
    "policyDefinitionName": "aa633080-8b72-40c4-a2d7-d00c03e80bed",
    "policyDefinitionReferenceId": "identityenablemfaforownerpermissionsmonitoring",
    "policyDefinitionVersion": "",
    "policyEvaluationDetails": null,
    "policySetDefinitionCategory": "security center",
    "policySetDefinitionId": "/providers/Microsoft.Authorization/policySetDefinitions/1f3afdf9-d0c9-4c3d-847f-89da613e70a8",
    "policySetDefinitionName": "1f3afdf9-d0c9-4c3d-847f-89da613e70a8",
    "policySetDefinitionOwner": "",
    "policySetDefinitionParameters": "",
    "policySetDefinitionVersion": "",
    "resourceGroup": "",
    "resourceId": "/subscriptions/{subscriptionId}",
    "resourceLocation": "",
    "resourceTags": "tbd",
    "resourceType": "Microsoft.Resources/subscriptions",
    "subscriptionId": "{subscriptionId}",
    "timestamp": "2020-07-15T08:37:07.903433+00:00"
  }
]
```

Příklad: získávání podrobností pro všechny prostředky virtuálních síťových prostředků, které nedodržují předpisy.

```azurecli-interactive
az policy state list --filter "ResourceType eq 'Microsoft.Network/virtualNetworks'"
```

```json
[
  {
    "complianceReasonCode": "",
    "complianceState": "NonCompliant",
    "effectiveParameters": "",
    "isCompliant": false,
    "managementGroupIds": "{managementgroupId}",
    "odatacontext": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
    "odataid": null,
    "policyAssignmentId": "/subscriptions/{subscriptionId}/providers/microsoft.authorization/policyassignments/e0704696df5e4c3c81c873e8",
    "policyAssignmentName": "e0704696df5e4c3c81c873e8",
    "policyAssignmentOwner": "tbd",
    "policyAssignmentParameters": "",
    "policyAssignmentScope": "/subscriptions/{subscriptionId}",
    "policyAssignmentVersion": "",
    "policyDefinitionAction": "audit",
    "policyDefinitionCategory": "tbd",
    "policyDefinitionGroupNames": [
      ""
    ],
    "policyDefinitionId": "/subscriptions/{subscriptionId}/providers/microsoft.authorization/policydefinitions/2e3197b6-1f5b-4b01-920c-b2f0a7e9b18a",
    "policyDefinitionName": "2e3197b6-1f5b-4b01-920c-b2f0a7e9b18a",
    "policyDefinitionReferenceId": "",
    "policyDefinitionVersion": "",
    "policyEvaluationDetails": null,
    "policySetDefinitionCategory": "",
    "policySetDefinitionId": "",
    "policySetDefinitionName": "",
    "policySetDefinitionOwner": "",
    "policySetDefinitionParameters": "",
    "policySetDefinitionVersion": "",
    "resourceGroup": "RG-Tags",
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Microsoft.Network/virtualNetworks/RG-Tags-vnet",
    "resourceLocation": "westus2",
    "resourceTags": "tbd",
    "resourceType": "Microsoft.Network/virtualNetworks",
    "subscriptionId": "{subscriptionId}",
    "timestamp": "2020-07-15T08:37:07.901911+00:00"
  }
]
```

Příklad: získávání událostí souvisejících s neodpovídajícími prostředky virtuální sítě, k nimž došlo po určitém datu.

```azurecli-interactive
az policy state list --filter "ResourceType eq 'Microsoft.Network/virtualNetworks'" --from '2020-07-14T00:00:00Z'
```

```json
[
  {
    "complianceReasonCode": "",
    "complianceState": "NonCompliant",
    "effectiveParameters": "",
    "isCompliant": false,
    "managementGroupIds": "{managementgroupId}",
    "odatacontext": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
    "odataid": null,
    "policyAssignmentId": "/subscriptions/{subscriptionId}/providers/microsoft.authorization/policyassignments/e0704696df5e4c3c81c873e8",
    "policyAssignmentName": "e0704696df5e4c3c81c873e8",
    "policyAssignmentOwner": "tbd",
    "policyAssignmentParameters": "",
    "policyAssignmentScope": "/subscriptions/{subscriptionId}",
    "policyAssignmentVersion": "",
    "policyDefinitionAction": "audit",
    "policyDefinitionCategory": "tbd",
    "policyDefinitionGroupNames": [
      ""
    ],
    "policyDefinitionId": "/subscriptions/{subscriptionId}/providers/microsoft.authorization/policydefinitions/2e3197b6-1f5b-4b01-920c-b2f0a7e9b18a",
    "policyDefinitionName": "2e3197b6-1f5b-4b01-920c-b2f0a7e9b18a",
    "policyDefinitionReferenceId": "",
    "policyDefinitionVersion": "",
    "policyEvaluationDetails": null,
    "policySetDefinitionCategory": "",
    "policySetDefinitionId": "",
    "policySetDefinitionName": "",
    "policySetDefinitionOwner": "",
    "policySetDefinitionParameters": "",
    "policySetDefinitionVersion": "",
    "resourceGroup": "RG-Tags",
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Microsoft.Network/virtualNetworks/RG-Tags-vnet",
    "resourceLocation": "westus2",
    "resourceTags": "tbd",
    "resourceType": "Microsoft.Network/virtualNetworks",
    "subscriptionId": "{subscriptionId}",
    "timestamp": "2020-07-15T08:37:07.901911+00:00"
  }
]
```

### <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell modul pro Azure Policy je na Galerie prostředí PowerShell k dispozici jako [AZ. PolicyInsights](https://www.powershellgallery.com/packages/Az.PolicyInsights). Pomocí PowerShellGet můžete nainstalovat modul pomocí nástroje (Ujistěte se, že `Install-Module -Name Az.PolicyInsights` máte nainstalovanou nejnovější [Azure PowerShell](/powershell/azure/install-az-ps) ):

```azurepowershell-interactive
# Install from PowerShell Gallery via PowerShellGet
Install-Module -Name Az.PolicyInsights

# Import the downloaded module
Import-Module Az.PolicyInsights

# Login with Connect-AzAccount if not using Cloud Shell
Connect-AzAccount
```

Modul má následující rutiny:

- `Get-AzPolicyStateSummary`
- `Get-AzPolicyState`
- `Get-AzPolicyEvent`
- `Get-AzPolicyRemediation`
- `Remove-AzPolicyRemediation`
- `Start-AzPolicyRemediation`
- `Stop-AzPolicyRemediation`

Příklad: získání souhrnu stavu pro nejvyšší přiřazenou zásadu s největším počtem nevyhovujících prostředků.

```azurepowershell-interactive
PS> Get-AzPolicyStateSummary -Top 1

NonCompliantResources : 15
NonCompliantPolicies  : 1
PolicyAssignments     : {/subscriptions/{subscriptionId}/resourcegroups/RG-Tags/providers/micros
                        oft.authorization/policyassignments/37ce239ae4304622914f0c77}
```

Příklad: získání záznamu stavu pro naposledy vyhodnocený prostředek (výchozí je podle časového razítka v sestupném pořadí).

```azurepowershell-interactive
PS> Get-AzPolicyState -Top 1

Timestamp                  : 5/22/2018 3:47:34 PM
ResourceId                 : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Network/networkInterfaces/linux316
PolicyAssignmentId         : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Authorization/policyAssignments/37ce239ae4304622914f0c77
PolicyDefinitionId         : /providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62
ComplianceState            : NonCompliant
SubscriptionId             : {subscriptionId}
ResourceType               : /Microsoft.Network/networkInterfaces
ResourceLocation           : westus2
ResourceGroup              : RG-Tags
ResourceTags               : tbd
PolicyAssignmentName       : 37ce239ae4304622914f0c77
PolicyAssignmentOwner      : tbd
PolicyAssignmentParameters : {"tagName":{"value":"costCenter"},"tagValue":{"value":"Contoso-Test"}}
PolicyAssignmentScope      : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags
PolicyDefinitionName       : 1e30110a-5ceb-460c-a204-c1c3969c6d62
PolicyDefinitionAction     : deny
PolicyDefinitionCategory   : tbd
```

Příklad: získávání podrobností pro všechny prostředky virtuálních síťových prostředků, které nedodržují předpisy.

```azurepowershell-interactive
PS> Get-AzPolicyState -Filter "ResourceType eq '/Microsoft.Network/virtualNetworks'"

Timestamp                  : 5/22/2018 4:02:20 PM
ResourceId                 : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Network/virtualNetworks/RG-Tags-vnet
PolicyAssignmentId         : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Authorization/policyAssignments/37ce239ae4304622914f0c77
PolicyDefinitionId         : /providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62
ComplianceState            : NonCompliant
SubscriptionId             : {subscriptionId}
ResourceType               : /Microsoft.Network/virtualNetworks
ResourceLocation           : westus2
ResourceGroup              : RG-Tags
ResourceTags               : tbd
PolicyAssignmentName       : 37ce239ae4304622914f0c77
PolicyAssignmentOwner      : tbd
PolicyAssignmentParameters : {"tagName":{"value":"costCenter"},"tagValue":{"value":"Contoso-Test"}}
PolicyAssignmentScope      : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags
PolicyDefinitionName       : 1e30110a-5ceb-460c-a204-c1c3969c6d62
PolicyDefinitionAction     : deny
PolicyDefinitionCategory   : tbd
```

Příklad: získávání událostí souvisejících s neodpovídajícími prostředky virtuální sítě, k nimž došlo po určitém datu, převedení na objekt sdíleného svazku clusteru a exportování do souboru.

```azurepowershell-interactive
$policyEvents = Get-AzPolicyEvent -Filter "ResourceType eq '/Microsoft.Network/virtualNetworks'" -From '2020-09-19'
$policyEvents | ConvertTo-Csv | Out-File 'C:\temp\policyEvents.csv'
```

Výstup `$policyEvents` objektu vypadá jako následující výstup:

```output
Timestamp                  : 9/19/2020 5:18:53 AM
ResourceId                 : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Network/virtualNetworks/RG-Tags-vnet
PolicyAssignmentId         : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Authorization/policyAssignments/37ce239ae4304622914f0c77
PolicyDefinitionId         : /providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62
ComplianceState            : NonCompliant
SubscriptionId             : {subscriptionId}
ResourceType               : /Microsoft.Network/virtualNetworks
ResourceLocation           : eastus
ResourceGroup              : RG-Tags
ResourceTags               : tbd
PolicyAssignmentName       : 37ce239ae4304622914f0c77
PolicyAssignmentOwner      : tbd
PolicyAssignmentParameters : {"tagName":{"value":"costCenter"},"tagValue":{"value":"Contoso-Test"}}
PolicyAssignmentScope      : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags
PolicyDefinitionName       : 1e30110a-5ceb-460c-a204-c1c3969c6d62
PolicyDefinitionAction     : deny
PolicyDefinitionCategory   : tbd
TenantId                   : {tenantId}
PrincipalOid               : {principalOid}
```

Pole **PrincipalOid** lze použít k získání konkrétního uživatele pomocí rutiny Azure PowerShell `Get-AzADUser` . Nahraďte **{principalOid}** odpovědí, kterou jste dostali z předchozího příkladu.

```azurepowershell-interactive
PS> (Get-AzADUser -ObjectId {principalOid}).DisplayName
Trent Baker
```

## <a name="azure-monitor-logs"></a>Protokoly služby Azure Monitor

Pokud máte [pracovní prostor Log Analytics](../../../azure-monitor/logs/log-query-overview.md) s `AzureActivity` z [Activity log Analyticsho řešení](../../../azure-monitor/essentials/activity-log.md) svázaného s vaším předplatným, můžete si také zobrazit výsledky nedodržení předpisů ze hodnocení nových a aktualizovaných prostředků pomocí jednoduchých dotazů Kusto a `AzureActivity` tabulky. S podrobnostmi v protokolech Azure Monitor můžete výstrahy nakonfigurovat tak, aby sledovaly nedodržování předpisů.

:::image type="content" source="../media/getting-compliance-data/compliance-loganalytics.png" alt-text="Snímek obrazovky Azure Monitor protokolů se zobrazenými Azure Policy akcemi v tabulce AzureActivity" border="false":::

## <a name="next-steps"></a>Další kroky

- Přečtěte si příklady na [Azure Policy Samples](../samples/index.md).
- Projděte si [strukturu definic Azure Policy](../concepts/definition-structure.md).
- Projděte si [Vysvětlení efektů zásad](../concepts/effects.md).
- Zjistěte, jak [programově vytvářet zásady](programmatically-create.md).
- Přečtěte si, jak [opravit prostředky, které nedodržují předpisy](remediate-resources.md).
- Seznamte se s tím, co skupina pro správu [organizuje vaše prostředky pomocí skupin pro správu Azure](../../management-groups/overview.md).
