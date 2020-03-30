---
title: Řešení potíží s běžnými chybami nasazení
description: Popisuje, jak vyřešit běžné chyby při nasazování prostředků do Azure pomocí Azure Resource Manager.
tags: top-support-issue
ms.topic: troubleshooting
ms.date: 10/04/2019
ms.openlocfilehash: bc1568c53cdb5518f694d77a2f28f3cf77296ee2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460377"
---
# <a name="troubleshoot-common-azure-deployment-errors-with-azure-resource-manager"></a>Řešení běžných problémů s nasazením v Azure při použití Azure Resource Manageru

Tento článek popisuje některé běžné chyby nasazení Azure a poskytuje informace k vyřešení chyb. Pokud nemůžete najít kód chyby pro chybu nasazení, přečtěte si informace [o hledání kódu chyby](#find-error-code).

Pokud hledáte informace o kódu chyby a tyto informace nejsou uvedeny v tomto článku, dejte nám vědět. V dolní části této stránky můžete zanechat zpětnou vazbu. Zpětná vazba je sledována pomocí githubových problémů.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="error-codes"></a>Kódy chyb

| Kód chyby | Omezení rizik | Další informace |
| ---------- | ---------- | ---------------- |
| AccountNameInvalid | Postupujte podle omezení pojmenování pro účty úložiště. | [Překlad názvu účtu úložiště](error-storage-account-name.md) |
| PropertyPropertyCannotBeSet | Zkontrolujte dostupné vlastnosti účtu úložiště. | [storageAccounts](/azure/templates/microsoft.storage/storageaccounts) |
| Přidělení se nezdařilo. | Cluster nebo oblast nemá k dispozici prostředky nebo nemůže podporovat požadovanou velikost virtuálního počítače. Opakujte požadavek později nebo požádejte o jinou velikost virtuálního počítače. | [Zřizování a přidělování problémy pro Linux](../../virtual-machines/linux/troubleshoot-deployment-new-vm.md), [zřizování a přidělování problémy pro Windows](../../virtual-machines/windows/troubleshoot-deployment-new-vm.md) a řešení potíží přidělení [selhání](../../virtual-machines/troubleshooting/allocation-failure.md)|
| Další operaceProbíhá | Počkejte na dokončení souběžné operace. | |
| Autorizace se nezdařila. | Váš účet nebo instanční objekt nemá dostatečný přístup k dokončení nasazení. Zkontrolujte roli, do které váš účet patří, a jeho přístup k oboru nasazení.<br><br>Tato chyba se může zobrazit, pokud není zaregistrován požadovaný poskytovatel prostředků. | [Řízení přístupu na základě role v Azure](../../role-based-access-control/role-assignments-portal.md)<br><br>[Vyřešit registraci](error-register-resource-provider.md) |
| Chybný požadavek | Odeslali jste hodnoty nasazení, které neodpovídají tomu, co očekává Správce prostředků. Nápovědu k řešení potíží naleznete ve zprávě o vnitřním stavu. | [Odkaz na šablonu](/azure/templates/) a [podporovaná umístění](resource-location.md) |
| Konflikt | Požadujete operaci, která není povolena v aktuálním stavu zdroje. Například změna velikosti disku je povolena pouze při vytváření virtuálního počítače nebo při vzdáleném virtuálnímu počítače. | |
| NasazeníAktivní A neupravitelné | Počkejte na dokončení souběžného nasazení do této skupiny prostředků. | |
| NasazeníFailedCleanUp | Při nasazení v úplném režimu se odstraní všechny prostředky, které nejsou v šabloně. Tato chyba se zobrazí, pokud nemáte dostatečná oprávnění k odstranění všech prostředků, které nejsou v šabloně. Chcete-li se vyhnout chybě, změňte režim nasazení na přírůstkový. | [Režimy nasazení Azure Resource Manageru](deployment-modes.md) |
| DeploymentNameInvalidCharacters | Název nasazení může obsahovat pouze písmeno, číslice, '-', '.' nebo '_'. | |
| DeploymentNameLengthLimitByl překročen | Názvy nasazení jsou omezeny na 64 znaků.  | |
| Nasazení se nezdařilo. | Chyba DeploymentFailed je obecná chyba, která neposkytuje podrobnosti, které potřebujete k vyřešení chyby. Vyhledejte v podrobnostech o chybě kód chyby, který poskytuje další informace. | [Najít kód chyby](#find-error-code) |
| DeploymentQuotaExceeded | Pokud dosáhnete limitu 800 nasazení na skupinu prostředků, odstraňte nasazení z historie, které již nejsou potřeba. | [Vyřešit chybu, pokud počet nasazení překročí 800](deployment-quota-exceeded.md) |
| DnsRecordInUse | Název záznamu DNS musí být jedinečný. Zadejte jiný název. | |
| Obrázek nebyl nalezen | Zkontrolujte nastavení obrazu virtuálního počítače. |  |
| InUseSubnetCannotNelzeBýt odstraněno | Tato chyba se může stát při pokusu o aktualizaci prostředku a požadavek je zpracován odstraněním a vytvořením prostředku. Ujistěte se, že jste určili všechny nezměněné hodnoty. | [Aktualizace prostředku](/azure/architecture/building-blocks/extending-templates/update-resource) |
| Neplatný tenant tokenu ověřování | Získejte přístupový token pro příslušného klienta. Token můžete získat pouze od klienta, ke kterému váš účet patří. | |
| Neplatný odkaz ContentLink | S největší pravděpodobností jste se pokusili vytvořit odkaz na vnořenou šablonu, která není k dispozici. Zkontrolujte identifikátor URI, který jste zadali pro vnořenou šablonu. Pokud šablona existuje v účtu úložiště, ujistěte se, že identifikátor URI je přístupný. Možná budete muset předat token SAS. V současné době nelze propojit šablonu, která je v účtu úložiště za [bránou firewall služby Azure Storage](../../storage/common/storage-network-security.md). Zvažte přesunutí šablony do jiného úložiště, jako je GitHub. | [Propojené šablony](linked-templates.md) |
| Neplatné umístění nasazení | Při nasazování na úrovni předplatného jste zadali jiné umístění pro dříve použitý název nasazení. | [Nasazení na úrovni předplatného](deploy-to-subscription.md) |
| Parametr InvalidParameter | Jedna z hodnot, které jste zadali pro prostředek, neodpovídá očekávané hodnotě. Tato chyba může být důsledkem mnoha různých podmínek. Například heslo může být nedostatečné nebo název objektu blob může být nesprávný. Chybová zpráva by měla označovat, která hodnota je třeba opravit. | |
| Neplatný obsah požadavků | Hodnoty nasazení buď zahrnují hodnoty, které nejsou rozpoznány, nebo chybí požadované hodnoty. Potvrďte hodnoty pro typ prostředku. | [Referenční informace k šablonám](/azure/templates/) |
| Neplatnýformát Žádosti | Povolte protokolování ladění při spuštění nasazení a ověřte obsah požadavku. | [Protokolování ladění](#enable-debug-logging) |
| Neplatnýoborový obor ResourceName | Zkontrolujte obor názvů prostředků, který jste zadali ve vlastnosti **type.** | [Referenční informace k šablonám](/azure/templates/) |
| Neplatnýodkaz na prostředky | Prostředek buď ještě neexistuje, nebo je nesprávně odkazován. Zkontrolujte, zda je třeba přidat závislost. Ověřte, zda vaše použití **referenční** funkce obsahuje požadované parametry pro váš scénář. | [Řešení závislostí](error-not-found.md) |
| Neplatný typ prostředku | Zkontrolujte typ prostředku, který jste zadali ve vlastnosti **type.** | [Referenční informace k šablonám](/azure/templates/) |
| InvalidSubscriptionRegistrationState | Zaregistrujte své předplatné u poskytovatele prostředků. | [Vyřešit registraci](error-register-resource-provider.md) |
| InvalidTemplate | Zkontrolujte, zda v syntaxi šablony neobsahuje chyby. | [Vyřešit neplatnou šablonu](error-invalid-template.md) |
| InvalidTemplateCircularDependency | Odeberte zbytečné závislosti. | [Vyřešit cyklické závislosti](error-invalid-template.md#circular-dependency) |
| LinkedAuthorizationFailed | Zkontrolujte, jestli váš účet patří stejnému tenantovi jako skupina prostředků, do které nasazujete. | |
| LinkedInvalidPropertyId | ID prostředku pro prostředek není řešení správně. Zkontrolujte, zda jste zadali všechny požadované hodnoty pro ID prostředku, včetně ID předplatného, názvu skupiny prostředků, typu prostředku, nadřazeného názvu prostředku (v případě potřeby) a názvu prostředku. | |
| LocationRequired | Zadejte umístění zdroje. | [Nastavení umístění](resource-location.md) |
| NeshodySegmenty zdrojů | Ujistěte se, že vnořený prostředek má správný počet segmentů v názvu a typu. | [Vyřešit segmenty prostředků](error-invalid-template.md#incorrect-segment-lengths)
| MissingRegistrationForLocation | Zkontrolujte stav registrace poskytovatele prostředků a podporovaná umístění. | [Vyřešit registraci](error-register-resource-provider.md) |
| Chybějící Registrace předplatného | Zaregistrujte své předplatné u poskytovatele prostředků. | [Vyřešit registraci](error-register-resource-provider.md) |
| NoRegisteredProviderFound | Zkontrolujte stav registrace poskytovatele prostředků. | [Vyřešit registraci](error-register-resource-provider.md) |
| NotFound | Pravděpodobně se pokoušíte nasadit závislý prostředek paralelně s nadřazeným prostředkem. Zkontrolujte, zda potřebujete přidat závislost. | [Řešení závislostí](error-not-found.md) |
| Operace není povolena. | Nasazení se pokouší o operaci, která překračuje kvótu pro předplatné, skupinu prostředků nebo oblast. Pokud je to možné, upravili nasazení tak, aby zůstalo v rámci kvót. V opačném případě zvažte požadavek na změnu kvót. | [Vyřešit kvóty](error-resource-quota.md) |
| ParentResourceNotFound | Před vytvořením podřízených prostředků se ujistěte, že existuje nadřazený prostředek. | [Vyřešit nadřazený prostředek](error-parent-resource.md) |
| Heslopříliš dlouhé | Je možné, že jste vybrali heslo s příliš mnoha znaky nebo jste před předáním jako parametru převedli hodnotu hesla na zabezpečený řetězec. Pokud šablona obsahuje **parametr zabezpečeného řetězce,** není nutné převést hodnotu na zabezpečený řetězec. Zadejte hodnotu hesla jako text. |  |
| Privátní IPaddressInreservedRange | Zadaná ADRESA IP obsahuje rozsah adres vyžadované ho Azure. Změňte adresu IP, abyste se vyhnuli vyhrazenému rozsahu. | [IP adresy](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) |
| Privátní IPAddressNotInPodnet | Zadaná adresa IP je mimo rozsah podsítě. Změňte adresu IP tak, aby spadala do rozsahu podsítě. | [IP adresy](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) |
| PropertyChangeNotAllowed | Některé vlastnosti nelze změnit na nasazený prostředek. Při aktualizaci prostředku omezte změny na povolené vlastnosti. | [Aktualizace prostředku](/azure/architecture/building-blocks/extending-templates/update-resource) |
| RequestDisallowedByPolicy | Vaše předplatné obsahuje zásady prostředků, které brání akci, kterou se pokoušíte provést během nasazení. Najděte zásadu, která blokuje akci. Pokud je to možné, změňte nasazení tak, aby splňovalo omezení ze zásad. | [Řešení zásad](error-policy-requestdisallowedbypolicy.md) |
| ReservedResourceName | Zadejte název prostředku, který neobsahuje rezervovaný název. | [Názvy vyhrazených prostředků](error-reserved-resource-name.md) |
| ResourceGroupBeingDeleted | Počkejte na dokončení odstranění. | |
| ResourceGroupNotFound | Zkontrolujte název cílové skupiny prostředků pro nasazení. Cílová skupina prostředků musí již existovat ve vašem předplatném. Zkontrolujte kontext předplatného. | [Powershell Azure](/powershell/module/Az.Accounts/Set-AzContext) [CLI](/cli/azure/account?#az-account-set) |
| ResourceNotFound | Vaše nasazení odkazuje na prostředek, který nelze vyřešit. Ověřte, zda vaše použití **referenční** funkce obsahuje parametry požadované pro váš scénář. | [Vyřešit odkazy](error-not-found.md) |
| ResourceQuotaExceeded | Nasazení se pokouší vytvořit prostředky, které překračují kvótu pro předplatné, skupinu prostředků nebo oblast. Pokud je to možné, upravili infrastrukturu tak, aby byla v rámci kvót. V opačném případě zvažte požadavek na změnu kvót. | [Vyřešit kvóty](error-resource-quota.md) |
| SkuNotAvailable | Vyberte skladovou položku (například velikost virtuálního počítače), která je dostupná pro vybrané umístění. | [Vyřešit skladovou položku](error-sku-not-available.md) |
| StorageAccountAlreadyExists | Zadejte jedinečný název účtu úložiště. | [Překlad názvu účtu úložiště](error-storage-account-name.md)  |
| StorageAccountAlreadyTaken | Zadejte jedinečný název účtu úložiště. | [Překlad názvu účtu úložiště](error-storage-account-name.md) |
| StorageAccountNotFound | Zkontrolujte předplatné, skupinu prostředků a název účtu úložiště, který se pokoušíte použít. | |
| PodsítěNotInSameVnet | Virtuální počítač může mít jenom jednu virtuální síť. Při nasazování několika síťových připojení se ujistěte, že patří do stejné virtuální sítě. | [Více nic](../../virtual-machines/windows/multiple-nics.md) |
| Předplatnénotregistrována | Při nasazování síťových prostředků je poskytovatel prostředků Microsoft.Network automaticky zaregistrován v předplatném. Někdy se automatická registrace nedokončí včas. Chcete-li se této občasné chybě vyhnout, zaregistrujte před nasazením zprostředkovatele prostředků microsoft.network. | [Vyřešit registraci](error-register-resource-provider.md) |
| ŠablonaResourceCircularDependency | Odeberte zbytečné závislosti. | [Vyřešit cyklické závislosti](error-invalid-template.md#circular-dependency) |
| TooManyTargetResourceGroups TooManyTargetGroups TooMany | Snižte počet skupin prostředků pro jedno nasazení. | [Nasazení napříč skupinami prostředků](cross-resource-group-deployment.md) |

## <a name="find-error-code"></a>Najít kód chyby

Existují dva typy chyb, ke kterým může dojít:

* Chyby ověření
* Chyby nasazení

K chybám ověření dochází ve scénářích, které je možné určit před nasazením. Patří k nim chyby syntaxe v šabloně nebo pokusy o nasazení prostředků, které by překročily kvóty předplatného. K chybám nasazení dochází za podmínek, které nastanou během procesu nasazení. Patří mezi ně pokusy o přístup k paralelně nasazovaným prostředkům.

Oba typy chyb vrací kód chyby, který můžete použít při řešení potíží s nasazením. Oba typy chyb se zobrazí v [protokolu aktivit](../management/view-activity-logs.md). Chyby ověření se ale nezobrazí v historii nasazení, protože vůbec nedojde k zahájení nasazení.

### <a name="validation-errors"></a>Chyby ověření

Při nasazování pomocí portálu se po odeslání hodnot zobrazí chyba ověření.

![zobrazit chybu ověření portálu](./media/common-deployment-errors/validation-error.png)

Výběrem zprávy zobrazíte další podrobnosti. Na následujícím obrázku se zobrazí chyba **InvalidTemplateDeployment** a zpráva, která označuje blokované nasazení zásad.

![zobrazit podrobnosti ověření](./media/common-deployment-errors/validation-details.png)

### <a name="deployment-errors"></a>Chyby nasazení

Pokud operace projde ověřením, ale během nasazování selže, zobrazí se chyba nasazení.

Pokud chcete zobrazit kódy a zprávy chyb nasazení v PowerShellu, použijte následující příkaz:

```azurepowershell-interactive
(Get-AzResourceGroupDeploymentOperation -DeploymentName exampledeployment -ResourceGroupName examplegroup).Properties.statusMessage
```

Pokud chcete zobrazit kódy a zprávy chyb nasazení v Azure CLI, použijte následující příkaz:

```azurecli-interactive
az deployment group operation list --name exampledeployment -g examplegroup --query "[*].properties.statusMessage"
```

Na portálu vyberte oznámení.

![chyba oznámení](./media/common-deployment-errors/notification.png)

Zobrazí se další podrobnosti o nasazení. Výběrem možnosti zobrazíte další informace o chybě.

![nasazení se nezdařilo](./media/common-deployment-errors/deployment-failed.png)

Zobrazí se chybová zpráva a kódy chyb. Všimněte si, že se zobrazí dva kódy chyb. První kód chyby (**DeploymentFailed**) značí obecnou chybu a neposkytuje podrobnosti potřebné k vyřešení této chyby. Druhý kód chyby (**StorageAccountNotFound**) poskytuje potřebné podrobnosti.

![podrobnosti o chybě](./media/common-deployment-errors/error-details.png)

## <a name="enable-debug-logging"></a>Povolit protokolování ladění

Někdy potřebujete více informací o žádosti a odpovědi, abyste zjistili, co se stalo. Během nasazení můžete požadovat, aby byly během nasazení zaznamenány další informace.

### <a name="powershell"></a>PowerShell

V Prostředí PowerShell nastavte parametr **DeploymentDebugLogLevel** na Všechny, ResponseContent nebo RequestContent.

```powershell
New-AzResourceGroupDeployment `
  -Name exampledeployment `
  -ResourceGroupName examplegroup `
  -TemplateFile c:\Azure\Templates\storage.json `
  -DeploymentDebugLogLevel All
```

Zkontrolujte obsah požadavku pomocí následující rutiny:

```powershell
(Get-AzResourceGroupDeploymentOperation `
-DeploymentName exampledeployment `
-ResourceGroupName examplegroup).Properties.request `
| ConvertTo-Json
```

Nebo obsah odpovědi s:

```powershell
(Get-AzResourceGroupDeploymentOperation `
-DeploymentName exampledeployment `
-ResourceGroupName examplegroup).Properties.response `
| ConvertTo-Json
```

Tyto informace vám mohou pomoci určit, zda je hodnota v šabloně nesprávně nastavena.

### <a name="azure-cli"></a>Azure CLI

V současné době Azure CLI nepodporuje zapnutí protokolování ladění, ale můžete načíst protokolování ladění.

Zkontrolujte operace nasazení pomocí následujícího příkazu:

```azurecli
az deployment group operation list \
  --resource-group examplegroup \
  --name exampledeployment
```

Zkontrolujte obsah požadavku pomocí následujícího příkazu:

```azurecli
az deployment group operation list \
  --name exampledeployment \
  -g examplegroup \
  --query [].properties.request
```

Zkontrolujte obsah odpovědi pomocí následujícího příkazu:

```azurecli
az deployment group operation list \
  --name exampledeployment \
  -g examplegroup \
  --query [].properties.response
```

### <a name="nested-template"></a>Vnořená šablona

Chcete-li protokolovat informace o ladění vnořené šablony, použijte element **debugSetting.**

```json
{
  "type": "Microsoft.Resources/deployments",
  "apiVersion": "2016-09-01",
  "name": "nestedTemplate",
  "properties": {
    "mode": "Incremental",
    "templateLink": {
      "uri": "{template-uri}",
      "contentVersion": "1.0.0.0"
    },
    "debugSetting": {
       "detailLevel": "requestContent, responseContent"
    }
  }
}
```

## <a name="create-a-troubleshooting-template"></a>Vytvoření šablony pro řešení potíží

V některých případech je nejjednodušším způsobem řešení potíží s šablonou testování jeho částí. Můžete vytvořit zjednodušenou šablonu, která vám umožní zaměřit se na část, o které se domníváte, že chybu způsobuje. Předpokládejme například, že se při odkazování na prostředek zobrazuje chyba. Místo toho, abyste se zabývali celou šablonou, vytvořte šablonu, která vrátí část, která může být příčinou vašeho problému. Může vám pomoci určit, zda předáváte správné parametry, používáte správně funkce šablony a získáváte očekávaný prostředek.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
  "storageName": {
    "type": "string"
  },
  "storageResourceGroup": {
    "type": "string"
  }
  },
  "variables": {},
  "resources": [],
  "outputs": {
  "exampleOutput": {
    "value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageName')), '2016-05-01')]",
    "type" : "object"
  }
  }
}
```

Nebo předpokládejme, že se vám zobrazily chyby nasazení, o kterých se domníváte, že souvisejí s nesprávně nastavenými závislostmi. Otestujte šablonu rozdělením na zjednodušené šablony. Nejprve vytvořte šablonu, která nasazuje pouze jeden prostředek (například SQL Server). Pokud jste si jisti, že máte tento prostředek správně definován, přidejte prostředek, který na něm závisí (například databáze SQL). Pokud máte tyto dva prostředky správně definovány, přidejte další závislé prostředky (například zásady auditování). Mezi jednotlivými testovacími nasazeními odstraňte skupinu prostředků a ujistěte se, že odpovídajícím způsobem testujete závislosti.

## <a name="next-steps"></a>Další kroky

* Chcete-li projít kurz pro řešení potíží, [přečtěte si článek Kurz: Poradce při potížích s nasazením šablon Správce prostředků](template-tutorial-troubleshoot.md)
* Další informace o auditování akcí naleznete v [tématu Audit operace se Správcem prostředků](../management/view-activity-logs.md).
* Informace o akcích k určení chyb během nasazení najdete v [tématu Zobrazení operací nasazení](deployment-history.md).
