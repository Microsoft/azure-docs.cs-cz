---
title: Nasazení Azure Monitor
description: Nasaďte Azure Monitor funkce ve velkém měřítku pomocí Azure Policy.
ms.subservice: ''
ms.topic: conceptual
ms.date: 06/08/2020
ms.openlocfilehash: 5da174b374265126df2113f5ccf41397745d39d6
ms.sourcegitcommit: d7fba095266e2fb5ad8776bffe97921a57832e23
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/09/2020
ms.locfileid: "84632245"
---
# <a name="deploy-azure-monitor-at-scale-using-azure-policy"></a>Nasazení Azure Monitor ve velkém měřítku pomocí Azure Policy
I když jsou některé funkce Azure Monitor nakonfigurované jednou nebo omezeným počtem časů, musí se pro každý prostředek, který chcete monitorovat, opakovat jiné. Tento článek popisuje metody použití Azure Policy k implementaci Azure Monitor škálování, aby se zajistilo, že monitorování je konzistentně a správně nakonfigurované pro všechny prostředky Azure.

Například je třeba vytvořit nastavení diagnostiky pro všechny vaše stávající prostředky Azure a pro každý nový prostředek, který vytvoříte. Také musíte mít nainstalovaného agenta a nakonfigurovaného při každém vytvoření virtuálního počítače. K provedení těchto akcí můžete použít metody, jako je například PowerShell nebo CLI, protože tyto metody jsou k dispozici pro všechny funkce Azure Monitor. Pomocí Azure Policy můžete mít logiku, která bude automaticky provádět příslušnou konfiguraci při každém vytvoření nebo úpravě prostředku.


## <a name="azure-policy"></a>Azure Policy
V této části najdete stručný úvod do [Azure Policy](../../governance/policy/overview.md) , který vám umožní vyhodnotit a vyhovět podnikovým standardům v celém předplatném Azure nebo skupině pro správu s minimálním úsilím. Úplné podrobnosti najdete v [dokumentaci k Azure Policy](../../governance/policy/overview.md) .

Pomocí Azure Policy můžete zadat požadavky na konfiguraci pro všechny vytvořené prostředky a identifikovat prostředky, které nedodržují předpisy, blokovat vytváření prostředků nebo přidat požadovanou konfiguraci. Funguje tak, že zachytí volání pro vytvoření nového prostředku nebo pro úpravu existujícího prostředku. Může reagovat takovými účinky, jako je zamítnutí žádosti, pokud se neshoduje s vlastnostmi očekávanými v definici zásad, příznakem pro nedodržování předpisů nebo nasazením souvisejícího prostředku. Stávající prostředky můžete opravit pomocí **deployIfNotExists** nebo **Upravit** definici zásad.

Azure Policy se skládá z objektů v následující tabulce. Podrobnější vysvětlení jednotlivých objektů naleznete v tématu [Azure Policy objekty](../../governance/policy/overview.md#azure-policy-objects) .

| Položka | Popis |
|:---|:---|
| Definice zásady | Popisuje podmínky dodržování předpisů a postup, který se má provést, pokud je splněna podmínka. Může se jednat o všechny prostředky určitého typu nebo pouze prostředky, které odpovídají určitým vlastnostem. Efekt může být jednoduše označovat, že prostředek má splňovat předpisy nebo nasadit související prostředek. Definice zásad jsou napsány pomocí formátu JSON, jak je popsáno v tématu [Struktura definice Azure Policy](../../governance/policy/concepts/definition-structure.md). Účinky jsou popsány v tématu [principy Azure Policych efektů](../../governance/policy/concepts/effects.md).
| Iniciativa zásad | Skupina definic zásad, které by se měly použít společně. Například můžete mít jednu definici zásady pro posílání protokolů prostředků do pracovního prostoru Log Analytics a další pro posílání protokolů prostředků do Center událostí. Vytvořte iniciativu, která zahrnuje definice zásad, a místo definice jednotlivých zásad aplikujte iniciativu na prostředky. Iniciativy jsou zapisovány pomocí formátu JSON, jak je popsáno v tématu [struktura Azure Policy iniciativ](../../governance/policy/concepts/initiative-definition-structure.md). |
| Přiřazení | Definice zásady nebo podnět se neprojeví, dokud není přiřazena k oboru. Přiřaďte například zásadu ke skupině prostředků, kterou použijete pro všechny prostředky vytvořené v tomto prostředku, nebo ji použijte u předplatného, aby ji bylo možné použít u všech prostředků v tomto předplatném.  Další podrobnosti najdete v tématu [Struktura přiřazení Azure Policy](../../governance/policy/concepts/assignment-structure.md). |

## <a name="built-in-policy-definitions-for-azure-monitor"></a>Předdefinované definice zásad pro Azure Monitor
Azure Policy obsahuje několik předem sestavených definic souvisejících s Azure Monitor. Tyto definice zásad můžete přiřadit k existujícímu předplatnému nebo je použít jako základ pro vytvoření vlastních definicí. Úplný seznam integrovaných politika v kategorii **monitorování** najdete v tématu [Azure Policy předdefinované definice zásad pro Azure monitor](../policy-samples.md).

Pokud chcete zobrazit integrované definice zásad související s monitorováním, udělejte toto:

1. V Azure Portal přejít na **Azure Policy** .
2. Vyberte **definice**.
3. Pro **typ**vyberte možnost *předdefinované* a pro **kategorii**vyberte možnost *monitorování*.

  ![Předdefinované definice zásad](media/deploy-scale/builtin-policies.png)


## <a name="diagnostic-settings"></a>Nastavení diagnostiky
[Nastavení diagnostiky](../platform/diagnostic-settings.md) shromažďují protokoly prostředků a metriky z prostředků Azure do několika umístění, obvykle do log Analyticsho pracovního prostoru, který umožňuje analyzovat data pomocí [dotazů protokolů](../log-query/log-query-overview.md) a [výstrah protokolů](alerts-log.md). Pomocí zásad můžete automaticky vytvořit nastavení diagnostiky pokaždé, když vytvoříte prostředek.

Každý typ prostředku Azure má jedinečnou sadu kategorií, které je třeba uvést v nastavení diagnostiky. Z tohoto důvodu každý typ prostředku vyžaduje samostatnou definici zásad. Některé typy prostředků mají předdefinované definice zásad, které můžete přiřadit beze změny. U jiných typů prostředků je potřeba vytvořit vlastní definici.

### <a name="built-in-policy-definitions-for-azure-monitor"></a>Předdefinované definice zásad pro Azure Monitor
K dispozici jsou dvě předdefinované definice zásad pro každý typ prostředku, jeden pro odeslání do Log Analytics pracovního prostoru a druhý do centra událostí. Pokud potřebujete jenom jedno umístění, přiřaďte tuto zásadu pro typ prostředku. Pokud potřebujete obojí, přiřaďte definici zásad pro daný prostředek.

Například následující obrázek znázorňuje integrované definice zásad nastavení diagnostiky pro Data Lake Analytics.

  ![Předdefinované definice zásad](media/deploy-scale/builtin-diagnostic-settings.png)

### <a name="custom-policy-definitions"></a>Definice vlastních zásad
U typů prostředků, které nemají vestavěnou zásadu, je nutné vytvořit vlastní definici zásad. To můžete provést ručně v Azure Portal zkopírováním existující předdefinované zásady a úpravou pro svůj typ prostředku. To je efektivnější, i když vytvoříte zásadu programově pomocí skriptu v Galerie prostředí PowerShell.

Skript [Create-AzDiagPolicy](https://www.powershellgallery.com/packages/Create-AzDiagPolicy) vytvoří soubory zásad pro konkrétní typ prostředku, který můžete nainstalovat pomocí PowerShellu nebo rozhraní příkazového řádku. Pomocí následujícího postupu můžete vytvořit vlastní definici zásad pro nastavení diagnostiky.


1. Ujistěte se, že máte nainstalovanou [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps) .
2. Nainstalujte skript pomocí následujícího příkazu:
  
    ```azurepowershell
    Install-Script -Name Create-AzDiagPolicy
    ```

3. Spusťte skript pomocí parametrů a určete, kam se mají protokoly odesílat. Zobrazí se výzva k zadání předplatného a typu prostředku. Pokud například chcete vytvořit definici zásady, která odesílá do Log Analytics pracovního prostoru a centra událostí, použijte následující příkaz.

   ```azurepowershell
   Create-AzDiagPolicy.ps1 -ExportLA -ExportEH -ExportDir ".\PolicyFiles"  
   ```

4. Případně můžete v příkazu zadat předplatné a typ prostředku. Pokud například chcete vytvořit definici zásady, která odesílá Log Analytics pracovního prostoru a centra událostí pro databáze Azure SQL Server, použijte následující příkaz.

   ```azurepowershell
   Create-AzDiagPolicy.ps1 -SubscriptionID xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -ResourceType Microsoft.Sql/servers/databases  -ExportLA -ExportEH -ExportDir ".\PolicyFiles"  
   ```

5. Skript vytvoří samostatné složky pro každou definici zásady, která obsahuje tři soubory s názvem azurepolicy, JSON, azurepolicy. Rules. JSON, azurepolicy. Parameters. JSON. Pokud chcete zásadu vytvořit ručně v Azure Portal, můžete zkopírovat a vložit obsah azurepolicy. JSON, protože obsahuje celou definici zásady. K vytvoření definice zásady z příkazového řádku použijte další dva soubory s PowerShellem nebo CLI.

    Následující příklady ukazují, jak nainstalovat definici zásady z PowerShellu i rozhraní příkazového řádku. Každá z nich zahrnuje metadata k určení kategorie **monitorování** , podle které se mají seskupit nové definice zásad, s integrovanými definicemi zásad.

      ```azurepowershell
      New-AzPolicyDefinition -name "Deploy Diagnostic Settings for SQL Server database to Log Analytics workspace" -policy .\Apply-Diag-Settings-LA-Microsoft.Sql-servers-databases\azurepolicy.rules.json -parameter .\Apply-Diag-Settings-LA-Microsoft.Sql-servers-databases\azurepolicy.parameters.json -mode All -Metadata '{"category":"Monitoring"}'
      ```

      ```azurecli
      az policy definition create --name 'deploy-diag-setting-sql-database--workspace' --display-name 'Deploy Diagnostic Settings for SQL Server database to Log Analytics workspace'  --rules 'Apply-Diag-Settings-LA-Microsoft.Sql-servers-databases\azurepolicy.rules.json' --params 'Apply-Diag-Settings-LA-Microsoft.Sql-servers-databases\azurepolicy.parameters.json' --subscription 'AzureMonitor_Docs' --mode All
      ```

### <a name="initiative"></a>Dávat
Místo toho, aby bylo možné vytvořit přiřazení pro každou definici zásad, je běžné strategii vytvořit iniciativu, která zahrnuje definice zásad pro vytvoření nastavení diagnostiky pro každou službu Azure. Vytvořte přiřazení mezi iniciativou a skupinou pro správu, předplatným nebo skupinou prostředků v závislosti na tom, jak vaše prostředí spravujete. Tato strategie nabízí následující výhody:

- Vytvořte jedno přiřazení pro iniciativu místo více přiřazení pro každý typ prostředku. Použijte stejnou iniciativu pro více skupin monitorování, předplatných nebo skupin prostředků.
- Pokud potřebujete přidat nový typ prostředku nebo cíl, upravte iniciativu. Například vaše počáteční požadavky může být odesílat data pouze do Log Analyticsho pracovního prostoru, ale později budete chtít přidat centrum událostí. Místo vytváření nových přiřazení upravte iniciativu.

Podrobnosti o vytvoření iniciativy najdete v tématu [Vytvoření a přiřazení definice iniciativy](../../governance/policy/tutorials/create-and-manage.md#create-and-assign-an-initiative-definition) . Vezměte v úvahu následující doporučení:

- Nastavte **kategorii** tak, aby ji **bylo možné seskupit** se souvisejícími definicemi integrovaných a vlastních zásad.
- Místo zadání podrobností pro Log Analytics pracovní prostor a centrum událostí pro definici zásad zahrnuté v iniciativě použijte parametr Common Initiative. To vám umožní snadno zadat společnou hodnotu pro všechny definice zásad a v případě potřeby změnit tuto hodnotu.

![Definice iniciativy](media/deploy-scale/initiative-definition.png)

### <a name="assignment"></a>Přiřazení 
Přiřaďte iniciativu ke skupině pro správu Azure, k předplatnému nebo ke skupině prostředků v závislosti na rozsahu vašich prostředků, které chcete monitorovat. [Skupina pro správu](../../governance/management-groups/overview.md) je zvláště užitečná pro zásady oboru, zejména pokud má vaše organizace víc předplatných.

![Přiřazení iniciativy](media/deploy-scale/initiative-assignment.png)

Pomocí parametrů iniciativy můžete pro všechny definice zásad v iniciativě určit pracovní prostor nebo jiné podrobnosti. 

![Parametry iniciativ](media/deploy-scale/initiative-parameters.png)

### <a name="remediation"></a>Odstranění rizika
Iniciativa bude platit pro každý virtuální počítač při jeho vytvoření. [Úloha nápravy](../../governance/policy/how-to/remediate-resources.md) nasadí definice zásad v iniciativě do stávajících prostředků, takže vám to umožní vytvořit diagnostické nastavení pro všechny prostředky, které už byly vytvořeny. Když vytváříte přiřazení pomocí Azure Portal, máte možnost vytvořit úlohu nápravy ve stejnou dobu. Podrobnosti o nápravě najdete v tématu [napravení nevyhovujících prostředků pomocí Azure Policy](../../governance/policy/how-to/remediate-resources.md) .

![Náprava v iniciativě](media/deploy-scale/initiative-remediation.png)


## <a name="azure-monitor-for-vms"></a>Azure Monitor pro virtuální počítače
[Azure monitor pro virtuální počítače](../insights/vminsights-overview.md) je primárním nástrojem v Azure monitor pro monitorování virtuálních počítačů. Povolení Azure Monitor pro virtuální počítače nainstaluje agenta Log Analytics i agenta závislostí. Místo toho, abyste tyto úlohy prováděli ručně, použijte Azure Policy, abyste zajistili, že se každý virtuální počítač nakonfiguroval při jeho vytváření.

Azure Monitor pro virtuální počítače obsahuje dvě integrované iniciativy s názvem **povolit Azure monitor pro virtuální počítače** a **povolit Azure monitor pro Virtual Machine Scale Sets**. Tyto iniciativy obsahují sadu definic zásad, které jsou potřebné k instalaci agenta Log Analytics a agenta závislostí, který je vyžadován pro povolení Azure Monitor pro virtuální počítače. 

Místo vytváření úloh pro tyto iniciativy pomocí rozhraní Azure Policy Azure Monitor pro virtuální počítače obsahuje funkci, která vám umožní zkontrolovat počet virtuálních počítačů v jednotlivých oborech, abyste zjistili, jestli se iniciativa používala. Pak můžete nakonfigurovat pracovní prostor a vytvořit všechna požadovaná přiřazení pomocí tohoto rozhraní.

Podrobnosti o tomto procesu najdete v tématu [povolení Azure monitor pro virtuální počítače pomocí Azure Policy](../insights/vminsights-enable-at-scale-policy.md).

![Zásady Azure Monitor pro virtuální počítače](../platform/media/deploy-scale/vminsights-policy.png)


## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [Azure Policy](../../governance/policy/overview.md).
- Přečtěte si další informace o [nastavení diagnostiky](diagnostic-settings.md).
- Podívejte se na [úložiště Northstar PlayBook](https://github.com/Azure/CET-NorthStar) , které poskytuje pokyny pro architekturu a implementaci pro rozsáhlé přijetí podnikového Azure.
