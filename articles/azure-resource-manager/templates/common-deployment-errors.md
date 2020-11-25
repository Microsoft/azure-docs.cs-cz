---
title: Řešení potíží s běžnými chybami nasazení
description: Popisuje řešení běžných chyb při nasazení prostředků do Azure pomocí Azure Resource Manager.
tags: top-support-issue
ms.topic: troubleshooting
ms.date: 09/09/2020
ms.openlocfilehash: fb7e476a5b4416282546d321a5e9a0127b7a4364
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96000704"
---
# <a name="troubleshoot-common-azure-deployment-errors-with-azure-resource-manager"></a>Řešení běžných problémů s nasazením v Azure při použití Azure Resource Manageru

Tento článek popisuje některé běžné chyby při nasazení Azure a poskytuje informace pro řešení chyb. Pokud nemůžete najít kód vaší chyby nasazení, přečtěte si téma popisující[vyhledání kódu chyby](#find-error-code).

Pokud hledáte informace o kódu chyby a tyto informace nejsou v tomto článku k dispozici, dejte nám prosím na starosti. V dolní části této stránky můžete nechat svůj názor. Zpětná vazba je sledována s problémy GitHubu.

## <a name="error-codes"></a>Kódy chyb

| Kód chyby | Omezení rizik | Další informace |
| ---------- | ---------- | ---------------- |
| AccountNameInvalid | Řiďte se omezeními názvů účtů úložiště. | [Přeložit název účtu úložiště](error-storage-account-name.md) |
| AccountPropertyCannotBeSet | Ověřte dostupné vlastnosti účtu úložiště. | [storageAccounts](/azure/templates/microsoft.storage/storageaccounts) |
| AllocationFailed | V clusteru nebo oblasti nejsou dostupné prostředky nebo nemůžou podporovat požadovanou velikost virtuálního počítače. Opakujte požadavek později nebo si vyžádejte jinou velikost virtuálního počítače. | [Problémy se zřizováním a](../../virtual-machines/troubleshooting/troubleshoot-deployment-new-vm-linux.md)přidělením pro [problémy se systémem Linux, zřizováním a přidělením pro Windows](../../virtual-machines/troubleshooting/troubleshoot-deployment-new-vm-windows.md) a [odstraňování potíží s přidělováním](../../virtual-machines/troubleshooting/allocation-failure.md)|
| AnotherOperationInProgress | Počkejte na dokončení souběžné operace. | |
| AuthorizationFailed | Váš účet nebo objekt služby nemá dostatečný přístup k dokončení nasazení. Ověřte, do jaké role účet patří, a jeho přístup k oboru nasazení.<br><br>Tato chyba se může zobrazit, když požadovaný poskytovatel prostředků není zaregistrován. | [Řízení přístupu na základě role Azure (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md)<br><br>[Vyřešit registraci](error-register-resource-provider.md) |
| Důvodu chybného požadavku | Odeslali jste hodnoty nasazení, které se neshodují s tím, co očekává Správce prostředků. Pro pomoc s řešením potíží se podívejte na vnitřní stavovou zprávu. | [Odkaz na šablonu](/azure/templates/) a [podporovaná umístění](resource-location.md) |
| Konflikt | Požadujete operaci, která není v aktuálním stavu prostředku povolena. Například změna velikosti disku je povolená jenom při vytváření virtuálního počítače nebo při uvolnění virtuálního počítače. | |
| DeploymentActiveAndUneditable | Počkejte, než se dokončí souběžné nasazení do této skupiny prostředků. | |
| DeploymentFailedCleanUp | Při nasazení v režimu úplného se odstraní všechny prostředky, které nejsou v šabloně. Tato chyba se zobrazí, pokud nemáte dostatečná oprávnění k odstranění všech prostředků, které v šabloně nejsou. Chcete-li se této chybě vyhnout, změňte režim nasazení na přírůstkové. | [Režimy nasazení Azure Resource Manageru](deployment-modes.md) |
| DeploymentNameInvalidCharacters | Název nasazení může obsahovat jenom písmena, číslice, znak "-", "." nebo "_". | |
| DeploymentNameLengthLimitExceeded | Názvy nasazení jsou omezené na 64 znaků.  | |
| DeploymentFailed | Chyba DeploymentFailed je obecná chyba, která neposkytuje podrobnosti potřebné k vyřešení chyby. Vyhledejte v podrobnostech o chybě kód chyby, který poskytuje další informace. | [Najít kód chyby](#find-error-code) |
| DeploymentQuotaExceeded | Pokud dosáhnete limitu nasazení 800 na jednu skupinu prostředků, odstraňte nasazení z historie, která už nepotřebujete. | [Vyřešit chybu, pokud je počet nasazení vyšší než 800](deployment-quota-exceeded.md) |
| DeploymentSizeExceeded | Zjednodušte svou šablonu, aby se snížila velikost. | [Vyřešit chyby velikosti šablony](error-job-size-exceeded.md) |
| DnsRecordInUse | Název záznamu DNS musí být jedinečný. Zadejte jiný název. | |
| ImageNotFound | Ověřte nastavení bitové kopie virtuálního počítače. |  |
| InUseSubnetCannotBeDeleted | Tato chyba se může zobrazit při pokusu o aktualizaci prostředku a žádost se zpracovává odstraněním a vytvořením prostředku. Ujistěte se, že jste zadali všechny nezměněné hodnoty. | [Aktualizace prostředku](/azure/architecture/building-blocks/extending-templates/update-resource) |
| InvalidAuthenticationTokenTenant | Získat přístupový token pro příslušného tenanta Token můžete získat jenom z tenanta, ke kterému patří váš účet. | |
| InvalidContentLink | Pravděpodobně došlo k pokusu o propojení s vnořenou šablonou, která není k dispozici. Projděte si identifikátor URI, který jste zadali pro vnořenou šablonu. Pokud šablona existuje v účtu úložiště, ujistěte se, že je identifikátor URI přístupný. Možná budete muset předat token SAS. V současné době nemůžete propojit šablonu, která se nachází v účtu úložiště za [bránou Azure Storage firewall](../../storage/common/storage-network-security.md). Zvažte přesunutí šablony do jiného úložiště, jako je GitHub. | [Propojené šablony](linked-templates.md) |
| InvalidDeploymentLocation | Při nasazování na úrovni předplatného máte k dispozici jiné umístění pro dříve použitý název nasazení. | [Nasazení na úrovni předplatného](deploy-to-subscription.md) |
| InvalidParameter | Jedna z hodnot, které jste zadali pro prostředek, se neshoduje s očekávanou hodnotou. Tato chyba může být způsobena z mnoha různých podmínek. Například heslo může být nedostatečné nebo může být nesprávné jméno objektu BLOB. Chybová zpráva by měla indikovat, která hodnota musí být opravena. | |
| InvalidRequestContent | Hodnoty nasazení buď obsahují hodnoty, které nebyly rozpoznány, nebo požadované hodnoty chybí. Potvrďte hodnoty pro typ prostředku. | [Referenční informace k šablonám](/azure/templates/) |
| InvalidRequestFormat | Povolte protokolování ladění při spuštění nasazení a ověřte obsah žádosti. | [Protokolování ladění](#enable-debug-logging) |
| InvalidResourceNamespace | Ověřte obor názvů prostředku, který jste zadali ve vlastnosti **typ** . | [Referenční informace k šablonám](/azure/templates/) |
| InvalidResourceReference | Prostředek ještě neexistuje nebo je na něj nesprávně odkazováno. Ověřte, zda potřebujete přidat závislost. Ověřte, že vaše použití **referenční** funkce zahrnuje požadované parametry pro váš scénář. | [Vyřešit závislosti](error-not-found.md) |
| InvalidResourceType | Ověřte typ prostředku, který jste zadali ve vlastnosti **typ** . | [Referenční informace k šablonám](/azure/templates/) |
| InvalidSubscriptionRegistrationState | Zaregistrujte své předplatné u poskytovatele prostředků. | [Vyřešit registraci](error-register-resource-provider.md) |
| InvalidTemplate | Vyhledejte chyby v syntaxi šablony. | [Vyřešit neplatnou šablonu](error-invalid-template.md) |
| InvalidTemplateCircularDependency | Odeberte zbytečné závislosti. | [Vyřešit cyklické závislosti](error-invalid-template.md#circular-dependency) |
| JobSizeExceeded | Zjednodušte svou šablonu, aby se snížila velikost. | [Vyřešit chyby velikosti šablony](error-job-size-exceeded.md) |
| LinkedAuthorizationFailed | Ověřte, jestli váš účet patří ke stejnému tenantovi jako skupina prostředků, do které nasazujete. | |
| LinkedInvalidPropertyId | ID prostředku neřeší správně. Ověřte, že jste zadali všechny požadované hodnoty pro ID prostředku, včetně ID předplatného, názvu skupiny prostředků, typu prostředku, názvu nadřazeného prostředku (v případě potřeby) a názvu prostředku. | |
| LocationRequired | Zadejte umístění prostředku. | [Nastavení umístění](resource-location.md) |
| MismatchingResourceSegments | Ujistěte se, že vnořený prostředek má v názvu a typu správný počet segmentů. | [Vyřešit segmenty prostředků](error-invalid-template.md#incorrect-segment-lengths)
| MissingRegistrationForLocation | Ověřte stav registrace poskytovatele prostředků a podporovaná umístění. | [Vyřešit registraci](error-register-resource-provider.md) |
| MissingSubscriptionRegistration | Zaregistrujte své předplatné u poskytovatele prostředků. | [Vyřešit registraci](error-register-resource-provider.md) |
| NoRegisteredProviderFound | Ověřte stav registrace poskytovatele prostředků. | [Vyřešit registraci](error-register-resource-provider.md) |
| NotFound | Můžete se pokusit o nasazení závislého prostředku paralelně s nadřazeným prostředkem. Ověřte, zda potřebujete přidat závislost. | [Vyřešit závislosti](error-not-found.md) |
| OperationNotAllowed | Nasazení probíhá pokus o operaci, která překračuje kvótu pro předplatné, skupinu prostředků nebo oblast. Pokud je to možné, upravte nasazení tak, aby zůstalo v rámci kvót. V opačném případě zvažte, že se bude vyžadovat změna vašich kvót. | [Řešení kvót](error-resource-quota.md) |
| ParentResourceNotFound | Před vytvořením podřízených prostředků zajistěte, aby existoval nadřazený prostředek. | [Vyřešit nadřazený prostředek](error-parent-resource.md) |
| PasswordTooLong | Možná jste vybrali heslo s příliš velkým počtem znaků nebo jste převedli hodnotu hesla na zabezpečený řetězec, než je předáte jako parametr. Pokud šablona obsahuje parametr **zabezpečeného řetězce** , nemusíte tuto hodnotu převést na zabezpečený řetězec. Zadejte hodnotu pro heslo jako text. |  |
| PrivateIPAddressInReservedRange | Zadaná IP adresa zahrnuje rozsah adres vyžadovaný službou Azure. Změňte IP adresu, aby se zabránilo rezervovanému rozsahu. | [IP adresy](../../virtual-network/public-ip-addresses.md) |
| PrivateIPAddressNotInSubnet | Zadaná IP adresa je mimo rozsah podsítě. Změňte IP adresu tak, aby klesla do rozsahu podsítí. | [IP adresy](../../virtual-network/public-ip-addresses.md) |
| PropertyChangeNotAllowed | Některé vlastnosti se u nasazeného prostředku nedají změnit. Při aktualizaci prostředku omezte změny na povolené vlastnosti. | [Aktualizace prostředku](/azure/architecture/building-blocks/extending-templates/update-resource) |
| RequestDisallowedByPolicy | Vaše předplatné zahrnuje zásadu prostředků, která brání akci, kterou se pokoušíte provést během nasazování. Vyhledejte zásadu, která zablokuje akci. Pokud je to možné, změňte nasazení tak, aby splňovalo omezení ze zásad. | [Řešení zásad](error-policy-requestdisallowedbypolicy.md) |
| ReservedResourceName | Zadejte název prostředku, který neobsahuje rezervovaný název. | [Názvy rezervovaných prostředků](error-reserved-resource-name.md) |
| ResourceGroupBeingDeleted | Počkejte na dokončení odstranění. | |
| ResourceGroupNotFound | Ověřte název cílové skupiny prostředků pro nasazení. Cílová skupina prostředků už musí existovat ve vašem předplatném. Ověřte svůj kontext předplatného. | [Azure CLI](/cli/azure/account?#az-account-set) [PowerShell](/powershell/module/Az.Accounts/Set-AzContext) |
| ResourceNotFound | Vaše nasazení odkazuje na prostředek, který nelze přeložit. Ověřte, že vaše použití **referenční** funkce zahrnuje parametry požadované pro váš scénář. | [Vyřešit odkazy](error-not-found.md) |
| ResourceQuotaExceeded | Nasazení se snaží vytvořit prostředky, které překračují kvótu pro předplatné, skupinu prostředků nebo oblast. Pokud je to možné, upravte infrastrukturu tak, aby zůstala v rámci kvót. V opačném případě zvažte, že se bude vyžadovat změna vašich kvót. | [Řešení kvót](error-resource-quota.md) |
| SkuNotAvailable | Vyberte položku SKU (například velikost virtuálního počítače), která je k dispozici pro vybrané umístění. | [Vyřešit SKU](error-sku-not-available.md) |
| StorageAccountAlreadyExists | Zadejte jedinečný název účtu úložiště. | [Přeložit název účtu úložiště](error-storage-account-name.md)  |
| StorageAccountAlreadyTaken | Zadejte jedinečný název účtu úložiště. | [Přeložit název účtu úložiště](error-storage-account-name.md) |
| StorageAccountNotFound | Ověřte předplatné, skupinu prostředků a název účtu úložiště, který se pokoušíte použít. | |
| SubnetsNotInSameVnet | Virtuální počítač může mít jenom jednu virtuální síť. Při nasazování několika síťových adaptérů se ujistěte, že patří do stejné virtuální sítě. | [Více síťových karet](../../virtual-machines/windows/multiple-nics.md) |
| SubscriptionNotFound | Zadané předplatné pro nasazení není k dispozici. Je možné, že ID předplatného je chybné, uživatel, který šablonu nasazuje, nemá dostatečná oprávnění k nasazení do předplatného nebo ID předplatného je ve špatném formátu. Při použití vnořených nasazení k [nasazení napříč obory](cross-scope-deployment.md)zadejte identifikátor GUID předplatného. | |
| SubscriptionNotRegistered | Při nasazování prostředku musí být poskytovatel prostředků zaregistrován pro vaše předplatné. Když použijete šablonu Azure Resource Manager pro nasazení, poskytovatel prostředků se automaticky zaregistruje v předplatném. V některých případech se automatická registrace nedokončila v čase. Chcete-li se tomuto přerušované chybě vyhnout, zaregistrujte poskytovatele prostředků před nasazením. | [Vyřešit registraci](error-register-resource-provider.md) |
| TemplateResourceCircularDependency | Odeberte zbytečné závislosti. | [Vyřešit cyklické závislosti](error-invalid-template.md#circular-dependency) |
| TooManyTargetResourceGroups | Snižte počet skupin prostředků pro jedno nasazení. | [Nasazení mezi různými obory](cross-scope-deployment.md) |

## <a name="find-error-code"></a>Najít kód chyby

Existují dva typy chyb, ke kterým může dojít:

* Chyby ověření
* Chyby nasazení

K chybám ověření dochází ve scénářích, které je možné určit před nasazením. Patří k nim chyby syntaxe v šabloně nebo pokusy o nasazení prostředků, které by překročily kvóty předplatného. K chybám nasazení dochází za podmínek, které nastanou během procesu nasazení. Patří mezi ně pokusy o přístup k paralelně nasazovaným prostředkům.

Oba typy chyb vrací kód chyby, který můžete použít při řešení potíží s nasazením. Oba typy chyb se zobrazí v [protokolu aktivit](../management/view-activity-logs.md). Chyby ověření se ale nezobrazí v historii nasazení, protože vůbec nedojde k zahájení nasazení.

### <a name="validation-errors"></a>Chyby ověřování

Při nasazování pomocí portálu se po odeslání hodnot zobrazí chyba ověření.

![Zobrazit chybu ověřování portálu](./media/common-deployment-errors/validation-error.png)

Výběrem zprávy zobrazíte další podrobnosti. Na následujícím obrázku se zobrazí chyba **InvalidTemplateDeployment** a zpráva, která označuje blokované nasazení zásad.

![Zobrazit podrobnosti ověření](./media/common-deployment-errors/validation-details.png)

### <a name="deployment-errors"></a>Chyby nasazení

Pokud operace projde ověřením, ale během nasazování selže, zobrazí se chyba nasazení.

Pokud chcete zobrazit kódy a zprávy chyb nasazení v PowerShellu, použijte následující příkaz:

```azurepowershell-interactive
(Get-AzResourceGroupDeploymentOperation -DeploymentName exampledeployment -ResourceGroupName examplegroup).Properties.statusMessage
```

Pokud chcete zobrazit kódy a zprávy chyb nasazení v Azure CLI, použijte následující příkaz:

```azurecli-interactive
az deployment operation group list --name exampledeployment -g examplegroup --query "[*].properties.statusMessage"
```

Na portálu vyberte oznámení.

![Chyba oznámení](./media/common-deployment-errors/notification.png)

Zobrazí se další podrobnosti o nasazení. Výběrem možnosti zobrazíte další informace o chybě.

![nasazení selhalo.](./media/common-deployment-errors/deployment-failed.png)

Zobrazí se chybová zpráva a kódy chyb. Všimněte si, že se zobrazí dva kódy chyb. První kód chyby (**DeploymentFailed**) značí obecnou chybu a neposkytuje podrobnosti potřebné k vyřešení této chyby. Druhý kód chyby (**StorageAccountNotFound**) poskytuje potřebné podrobnosti.

![Podrobnosti o chybě](./media/common-deployment-errors/error-details.png)

## <a name="enable-debug-logging"></a>Povolit protokolování ladění

Někdy potřebujete další informace o žádosti a odpovědi, abyste se dozvěděli, co se nepovedlo. Během nasazení si můžete vyžádat, aby se během nasazení do protokolu zaznamenaly Další informace.

### <a name="powershell"></a>PowerShell

V PowerShellu nastavte parametr **DeploymentDebugLogLevel** na All, obsah responsecontent nebo RequestContent.

```powershell
New-AzResourceGroupDeployment `
  -Name exampledeployment `
  -ResourceGroupName examplegroup `
  -TemplateFile c:\Azure\Templates\storage.json `
  -DeploymentDebugLogLevel All
```

Projděte si obsah žádosti pomocí následující rutiny:

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

Tyto informace vám pomohou určit, zda je hodnota v šabloně nesprávně nastavena.

### <a name="azure-cli"></a>Azure CLI

V současné době Azure CLI nepodporuje zapnutí protokolování ladění, ale můžete načíst protokolování ladění.

Projděte si operace nasazení pomocí následujícího příkazu:

```azurecli
az deployment operation group list \
  --resource-group examplegroup \
  --name exampledeployment
```

Projděte si obsah žádosti pomocí následujícího příkazu:

```azurecli
az deployment operation group list \
  --name exampledeployment \
  -g examplegroup \
  --query [].properties.request
```

Pomocí následujícího příkazu Projděte obsah odpovědi:

```azurecli
az deployment operation group list \
  --name exampledeployment \
  -g examplegroup \
  --query [].properties.response
```

### <a name="nested-template"></a>Vnořená šablona

Chcete-li protokolovat informace o ladění pro vnořenou šablonu, použijte element **debugSetting** .

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

## <a name="create-a-troubleshooting-template"></a>Vytvoření šablony pro odstraňování potíží

V některých případech nejjednodušší způsob, jak řešit potíže s vaší šablonou, je otestovat jejich části. Můžete vytvořit zjednodušenou šablonu, která vám umožní soustředit se na součást, o kterou se domníváte, že je příčinou chyby. Předpokládejme například, že při odkazování na prostředek obdržíte chybu. Místo toho, abyste museli pracovat s celou šablonou, vytvořte šablonu, která vrátí část, která by mohla způsobovat váš problém. Může vám pomoci určit, zda předáváte správné parametry, pomocí funkce šablony správně a získat očekávaný prostředek.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

Nebo Předpokládejme, že máte k dispozici chyby nasazení, které se domníváte, že jsou v relaci nesprávně nastavené závislosti. Otestujte šablonu tím, že ji rozdělíte na zjednodušené šablony. Nejprve vytvořte šablonu, která nasadí pouze jeden prostředek (například SQL Server). Když jste si jisti, že tento prostředek máte správně definovaný, přidejte prostředek, který na něm závisí (například SQL Database). Po správném definování těchto dvou prostředků přidejte další závislé prostředky (například zásady auditování). V rámci každého testovacího nasazení odstraňte skupinu prostředků, abyste se ujistili, že jste správně otestovali závislosti.

## <a name="next-steps"></a>Další kroky

* Kurz řešení potíží najdete v tématu [kurz: řešení potíží s nasazením správce prostředků šablon.](template-tutorial-troubleshoot.md)
* Další informace o akcích auditování najdete v tématu věnovaném [operacím auditu správce prostředků](../management/view-activity-logs.md).
* Další informace o akcích k určení chyb během nasazení najdete v tématu [Zobrazení operací nasazení](deployment-history.md).
