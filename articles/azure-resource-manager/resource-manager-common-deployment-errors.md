---
title: Řešení potíží s běžnými chybami nasazení v Azure | Dokumentace Microsoftu
description: Popisuje, jak řešit běžné chyby při nasazování prostředků do Azure pomocí Azure Resource Manageru.
services: azure-resource-manager
documentationcenter: ''
tags: top-support-issue
author: tfitzmac
manager: timlt
editor: tysonn
keywords: Chyba nasazení, nasazení azure, nasazení do azure
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/16/2018
ms.author: tomfitz
ms.openlocfilehash: 8da582750b5e20ddd7018f59292e7342f1628c8c
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39425379"
---
# <a name="troubleshoot-common-azure-deployment-errors-with-azure-resource-manager"></a>Řešení potíží s běžnými chybami nasazení v Azure pomocí Azure Resource Manageru

Tento článek popisuje některé běžné chyby nasazení v Azure, může dojít a obsahuje informace, které případné chyby opravte. Pokud kód chyby: Nelze najít pro chyby nasazení, přečtěte si téma [najít kód chyby:](#find-error-code).

## <a name="error-codes"></a>Kódy chyb

| Kód chyby | Omezení rizik | Další informace |
| ---------- | ---------- | ---------------- |
| AccountNameInvalid | Postupujte podle omezení názvů pro účty úložiště. | [Rozpoznání názvu účtu úložiště](resource-manager-storage-account-name-errors.md) |
| AccountPropertyCannotBeSet | Zkontrolujte vlastnosti účtu úložiště k dispozici. | [storageAccounts](/azure/templates/microsoft.storage/storageaccounts) |
| AllocationFailed | Cluster nebo oblast nemá žádné prostředky k dispozici nebo nemůžou podporovat požadovanou velikost virtuálního počítače. Zkuste požadavek zopakovat později, nebo požádat o jinou velikost virtuálního počítače. | [Potíže se zřizování a přidělením pro Linux](../virtual-machines/linux/troubleshoot-deployment-new-vm.md), [potíže zřizování a přidělením pro Windows](../virtual-machines/windows/troubleshoot-deployment-new-vm.md) a [Poradce při potížích s chybami přidělení](../virtual-machines/windows/allocation-failure.md)|
| AnotherOperationInProgress | Počkejte na dokončení souběžné operace. | |
| AuthorizationFailed | Váš účet nebo instanční objekt nemá dostatečný přístup k dokončení nasazení. Zkontrolujte role, kterou váš účet patří do a jeho přístup k oboru nasazení. | [Řízení přístupu na základě rolí Azure](../role-based-access-control/role-assignments-portal.md) |
| BadRequest | Jste odeslali hodnot nasazení, které neodpovídají očekávání podle Resource Manageru. Zkontrolujte zprávu vnitřní stav pro pomoc s řešením potíží. | [Referenční informace k šablonám](/azure/templates/) a [podporované umístění](resource-manager-templates-resources.md#location) |
| Konflikt | Vyžadujete, operace, která není povolena v aktuálním stavu prostředku. Například změna velikosti disku je povolená jenom při vytváření virtuálního počítače nebo při zrušení přidělení virtuálního počítače. | |
| DeploymentActive | Počkejte, souběžné nasazení do této skupiny prostředků k dokončení. | |
| DeploymentFailed | Chyba DeploymentFailed je obecná chyba, která neposkytuje informace potřebné k vyřešení chyby. Hledejte v podrobnostech o chybě kód chyby, která poskytuje další informace. | [Vyhledejte kód chyby:](#find-error-code) |
| DeploymentQuotaExceeded | Pokud překročíte limit 800 nasazení na skupinu prostředků, odstraňte nasazení z historie, ke které jsou už je nepotřebujete. Můžete odstranit položky z historie s [odstranit nasazení skupiny pro az](/cli/azure/group/deployment#az-group-deployment-delete) pro rozhraní příkazového řádku Azure, nebo [Remove-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/remove-azurermresourcegroupdeployment) v prostředí PowerShell. Odstranění záznamu z historie nasazení nemá vliv na nasazení prostředků. | |
| DnsRecordInUse | Název záznamu DNS musí být jedinečný. Buď zadejte jiný název nebo upravit existující záznam. | |
| ImageNotFound | Zkontrolujte nastavení bitové kopie virtuálního počítače. |  |
| InUseSubnetCannotBeDeleted | K této chybě může dojít při pokusu o aktualizaci prostředku, ale žádost zpracovává odstranit a vytvořit prostředek. Ujistěte se, že můžete zadat všechny hodnoty beze změny. | [Aktualizace prostředku](/azure/architecture/building-blocks/extending-templates/update-resource) |
| InvalidAuthenticationTokenTenant | Získání tokenu přístupu pro příslušné tenanta. Token, který můžete získat pouze z klienta, který váš účet patří do. | |
| InvalidContentLink | Pokusili jste pravděpodobně propojení vnořené šablony, která není k dispozici. Dvojitá kontrola identifikátor URI, který jste zadali pro vnořené šablony. Pokud šablona již existuje v účtu úložiště, ujistěte se, že identifikátor URI je přístupný. Možná bude potřeba předat SAS token. | [Propojenými šablonami](resource-group-linked-templates.md) |
| InvalidParameter | Jedna z hodnot, které jste zadali pro určitý prostředek se neshoduje s očekávanou hodnotou. Tato chyba může být následek mnoho různých podmínek. Například může být nedostatek heslo nebo název objektu blob může být nesprávný. Najdete v chybové zprávě určit hodnotu, která je třeba opravit. | |
| InvalidRequestContent | Nasazení hodnoty buď zahrnují hodnoty, které nejsou očekávané nebo chybí požadované hodnoty. Ověřte hodnoty pro váš typ prostředku. | [Referenční informace k šablonám](/azure/templates/) |
| InvalidRequestFormat | Povolit protokolování ladění při provádění nasazení a ověřte obsah požadavku. | [Protokolování ladění](#enable-debug-logging) |
| InvalidResourceNamespace | Zaškrtněte, obor názvů prostředků, které jste zadali v **typ** vlastnost. | [Referenční informace k šablonám](/azure/templates/) |
| InvalidResourceReference | Prostředek ještě neexistuje nebo je nesprávně odkazuje. Zkontrolujte, jestli je potřeba přidat závislost. Ověřte, že vaše užívání **odkaz** požadované parametry pro váš scénář zahrnuje funkce. | [Řešení závislostí](resource-manager-not-found-errors.md) |
| InvalidResourceType | Zkontrolujte prostředek typu, kterou jste zadali v **typ** vlastnost. | [Referenční informace k šablonám](/azure/templates/) |
| InvalidSubscriptionRegistrationState | Zaregistrujte předplatné u poskytovatele prostředků. | [Vyřešit registrace](resource-manager-register-provider-errors.md) |
| InvalidTemplate | Zkontrolujte syntaxi šablony pro chyby. | [Neplatná šablona řešení](resource-manager-invalid-template-errors.md) |
| InvalidTemplateCircularDependency | Odeberte nepotřebné závislosti. | [Vyřešit cyklické závislosti](resource-manager-invalid-template-errors.md#circular-dependency) |
| LinkedAuthorizationFailed | Zaškrtněte, pokud váš účet patří do stejného tenanta služby jako skupina prostředků, které nasazujete. | |
| LinkedInvalidPropertyId | ID prostředku pro prostředek není správně řešení. Zkontrolujte, že zadat všechny požadované hodnoty pro ID prostředku, včetně ID předplatného, název skupiny prostředků, typ prostředku, prostředek název nadřazeného (v případě potřeby) a název prostředku. | |
| LocationRequired | Zadejte umístění pro váš prostředek. | [Nastavení umístění](resource-manager-templates-resources.md#location) |
| MismatchingResourceSegments | Ujistěte se, že vnořený prostředek nemá správný počet segmentů v názvu a typu. | [Vyřešit segmenty prostředků](resource-manager-invalid-template-errors.md#incorrect-segment-lengths)
| MissingRegistrationForLocation | Zkontrolujte stav registrace poskytovatele prostředků a podporovaná umístění. | [Vyřešit registrace](resource-manager-register-provider-errors.md) |
| MissingSubscriptionRegistration | Zaregistrujte předplatné u poskytovatele prostředků. | [Vyřešit registrace](resource-manager-register-provider-errors.md) |
| NoRegisteredProviderFound | Zkontrolujte stav registrace poskytovatele prostředků. | [Vyřešit registrace](resource-manager-register-provider-errors.md) |
| NotFound | Pravděpodobně se pokoušíte nasadit souběžně s nadřazeném prostředku závislý prostředek. Zaškrtněte, pokud budete muset přidat závislost. | [Řešení závislostí](resource-manager-not-found-errors.md) |
| OperationNotAllowed | Nasazení je pokus o operaci, která překračuje kvótu pro předplatné, skupinu prostředků nebo oblasti. Pokud je to možné upravte nasazení tak, aby neopustí kvóty. V opačném případě zvažte žádosti o změnu do vaší kvóty. | [Vyřešit kvóty](resource-manager-quota-errors.md) |
| ParentResourceNotFound | Ujistěte se, že nadřazený prostředek existuje před vytvořením podřízené prostředky. | [Vyřešit nadřazený prostředek](resource-manager-parent-resource-errors.md) |
| PrivateIPAddressInReservedRange | Zadaná IP adresa obsahuje rozsah adres vyžadují Azure. Změna IP adresy, aby se zabránilo vyhrazeného rozsahu. | [IP adresy](../virtual-network/virtual-network-ip-addresses-overview-arm.md) |
| PrivateIPAddressNotInSubnet | Zadaná IP adresa je mimo rozsah podsítě. Změňte IP adresu, která spadají do rozsahu podsítě. | [IP adresy](../virtual-network/virtual-network-ip-addresses-overview-arm.md) |
| PropertyChangeNotAllowed | Některé vlastnosti nelze změnit na nasazených prostředků. Při aktualizaci prostředku, omezte změny vlastnosti povolené. | [Aktualizace prostředku](/azure/architecture/building-blocks/extending-templates/update-resource) |
| RequestDisallowedByPolicy | Vaše předplatné zahrnuje zásady prostředků, která brání akci, kterou se pokoušíte provést během nasazení. Vyhledejte zásadu, která blokuje akce. Pokud je to možné upravení vašeho nasazení plnit omezení ze zásad. | [Vyřešit zásady](resource-manager-policy-requestdisallowedbypolicy-error.md) |
| ReservedResourceName | Zadejte název prostředku, který neobsahuje vyhrazený název. | [Názvy vyhrazené prostředků](resource-manager-reserved-resource-name.md) |
| ResourceGroupBeingDeleted | Počkejte na dokončení odstranění. | |
| ResourceGroupNotFound | Zkontrolujte název cílové skupiny prostředků pro nasazení. Už musí existovat ve vašem předplatném. Zkontrolujte kontext vašeho předplatného. | [Azure CLI](/cli/azure/account?#az-account-set) [prostředí PowerShell](/powershell/module/azurerm.profile/set-azurermcontext) |
| ResourceNotFound | Nasazení odkazuje na prostředek, který nelze přeložit. Ověřte, že vaše užívání **odkaz** parametrů požadovaných pro váš scénář zahrnuje funkce. | [Přeložení odkazů.](resource-manager-not-found-errors.md) |
| ResourceQuotaExceeded | Nasazení se pokouší vytvořit prostředky, které překročí kvótu pro předplatné, skupinu prostředků nebo oblasti. Pokud je to možné upravte infrastrukturu zůstat v rámci kvóty. V opačném případě zvažte žádosti o změnu do vaší kvóty. | [Vyřešit kvóty](resource-manager-quota-errors.md) |
| SkuNotAvailable | Vyberte skladovou Položku (například velikost virtuálního počítače), který je k dispozici pro umístění, které jste vybrali. | [Vyřešit SKU](resource-manager-sku-not-available-errors.md) |
| StorageAccountAlreadyExists | Zadejte jedinečný název účtu úložiště. | [Rozpoznání názvu účtu úložiště](resource-manager-storage-account-name-errors.md)  |
| StorageAccountAlreadyTaken | Zadejte jedinečný název účtu úložiště. | [Rozpoznání názvu účtu úložiště](resource-manager-storage-account-name-errors.md) |
| StorageAccountNotFound | Zkontrolujte předplatné, skupinu prostředků a název účtu úložiště, který se pokoušíte použít. | |
| SubnetsNotInSameVnet | Virtuální počítač může mít pouze jednu virtuální síť. Při nasazování několika síťovými kartami, ujistěte se, že patří do stejné virtuální síti. | [Několik síťových karet](../virtual-machines/windows/multiple-nics.md) |
| TemplateResourceCircularDependency | Odeberte nepotřebné závislosti. | [Vyřešit cyklické závislosti](resource-manager-invalid-template-errors.md#circular-dependency) |
| TooManyTargetResourceGroups | Snížit počet skupin prostředků pro jedno nasazení. | [Nasazení napříč skupinami prostředků](resource-manager-cross-resource-group-deployment.md) |

## <a name="find-error-code"></a>Vyhledejte kód chyby:

Existují dva typy chyb, které dostanete:

* chyby ověření
* chyby nasazení

Chyby ověření jsou vyvolány scénáře, které se dá určit před nasazením. Patří mezi ně chyby syntaxe v šabloně nebo pokusu o nasazení prostředků, které by došlo k vaší kvóty předplatného. Chyby nasazení vznikají z podmínek, které nastanou během procesu nasazení. Patří mezi ně pokusu o přístup k prostředku, který se nasazuje paralelně.

Oba typy chyb vrátí kód chyby, který použijete k řešení potíží s nasazení. Oba typy chyb se zobrazí v [protokolu aktivit](resource-group-audit.md). Ale chyby ověřování se nezobrazují v historii nasazení protože nasazení nikdy spuštěn.

### <a name="validation-errors"></a>Chyby ověření

Při nasazení pomocí portálu, se zobrazí chyba ověření po odeslání hodnoty.

![Zobrazit chybu ověření na portálu](./media/resource-manager-common-deployment-errors/validation-error.png)

Vyberte zprávu pro další podrobnosti. Na následujícím obrázku vidíte **InvalidTemplateDeployment** chyby a napište zprávu, která určuje zásadu blokované nasazení.

![Zobrazit podrobnosti o ověřování](./media/resource-manager-common-deployment-errors/validation-details.png)

### <a name="deployment-errors"></a>chyby nasazení

Při operaci projde ověřovacími, ale je během nasazení se nezdaří, získáte chyba nasazení.

Pokud chcete zobrazit kódy chyb nasazení a zpráv pomocí Powershellu, použijte:

```azurepowershell-interactive
(Get-AzureRmResourceGroupDeploymentOperation -DeploymentName exampledeployment -ResourceGroupName examplegroup).Properties.statusMessage
```

Pokud chcete zobrazit kódy chyb nasazení a zprávy pomocí Azure CLI, použijte:

```azurecli-interactive
az group deployment operation list --name exampledeployment -g examplegroup --query "[*].properties.statusMessage"
```

Na portálu vyberte oznámení.

![Chyba oznámení](./media/resource-manager-common-deployment-errors/notification.png)

Se zobrazí další podrobnosti o tomto nasazení. Vyberte možnost najdete další informace o této chybě.

![nasazení selhalo](./media/resource-manager-common-deployment-errors/deployment-failed.png)

Zobrazí chybovou zprávu a chybové kódy. Všimněte si, že jsou dva kódy chyb. První kód chyby: (**DeploymentFailed**) je obecná chyba, která neposkytuje informace potřebné k vyřešení chyby. Druhý kód chyby: (**StorageAccountNotFound**) nabízí podrobné informace, které potřebujete. 

![Podrobnosti o chybě](./media/resource-manager-common-deployment-errors/error-details.png)

## <a name="enable-debug-logging"></a>Povolit protokolování ladění

Někdy potřebujete další informace o požadavku a odpovědi, které se dozvíte, co se nepovedlo. Během nasazení můžete požádat, další informace se protokoluje při nasazení. 

### <a name="powershell"></a>PowerShell

V prostředí PowerShell, nastavte **DeploymentDebugLogLevel** parametr ke všem, obsah ResponseContent nebo RequestContent.

```powershell
New-AzureRmResourceGroupDeployment `
  -Name exampledeployment `
  -ResourceGroupName examplegroup `
  -TemplateFile c:\Azure\Templates\storage.json `
  -DeploymentDebugLogLevel All
```

Prozkoumejte požadavek obsahu pomocí následující rutiny:

```powershell
(Get-AzureRmResourceGroupDeploymentOperation `
-DeploymentName exampledeployment `
-ResourceGroupName examplegroup).Properties.request `
| ConvertTo-Json
```

Nebo obsahu s odpovědí:

```powershell
(Get-AzureRmResourceGroupDeploymentOperation `
-DeploymentName exampledeployment `
-ResourceGroupName examplegroup).Properties.response `
| ConvertTo-Json
```

Tyto informace můžete zjistit, zda je hodnota v šabloně správně nastavena.

### <a name="azure-cli"></a>Azure CLI

V současné době nepodporuje rozhraní příkazového řádku Azure, zapnutí protokolování ladění, ale můžete načíst protokolováním ladění.

Prozkoumejte operací nasazení pomocí následujícího příkazu:

```azurecli
az group deployment operation list \
  --resource-group examplegroup \
  --name exampledeployment
```

Prozkoumejte požadavek obsahu pomocí následujícího příkazu:

```azurecli
az group deployment operation list \
  --name exampledeployment \
  -g examplegroup \
  --query [].properties.request
```

Zkontrolujte odpovědi obsahu pomocí následujícího příkazu:

```azurecli
az group deployment operation list \
  --name exampledeployment \
  -g examplegroup \
  --query [].properties.response
```

### <a name="nested-template"></a>Vnořené šablony

Pokud chcete protokolovat informace o ladění pro vnořené šablony, použijte **debugSetting** elementu.

```json
{
    "apiVersion": "2016-09-01",
    "name": "nestedTemplate",
    "type": "Microsoft.Resources/deployments",
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

## <a name="create-a-troubleshooting-template"></a>Vytvořit šablonu pro odstraňování potíží

V některých případech je nejjednodušší způsob, jak řešit šablony pro testování částí. Můžete vytvořit je zjednodušená šablonu, která vám umožní zaměřit se na část, která si myslíte, že je příčinou chyby. Předpokládejme například, že jsou dochází k chybě při odkazování na prostředek. Místo se zabývá celou šablonu, vytvořte šablonu, která vrací část, která může být příčinou problému. Pomůže vám určit, zda předáváte správné parametry, pomocí šablony funkce správně, a získání prostředku, které jste očekávali.

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

Nebo Předpokládejme, že narazíte na chyby nasazení, které si myslíte, že se vztahují k nesprávné nastavení závislostí. Test šablony rozdělením do zjednodušené šablony. Nejprve vytvořte šablonu, která nasadí jenom na jeden prostředek (jako je SQL Server). Pokud jste si jisti, že máte správně definované prostředku, přidejte prostředek, který na něm závisí (jako je SQL Database). Pokud máte správně definované tyto dva prostředky, přidejte další závislé prostředky (jako jsou zásady auditu). Mezi každé testovací nasazení Odstraňte skupinu prostředků pro jistotu, že budete odpovídajícím způsobem testování závislostí.


## <a name="next-steps"></a>Další postup
* Další informace o auditování akcí najdete v tématu [Audit operací pomocí Resource Manageru](resource-group-audit.md).
* Další informace o akcích, chcete-li zjistit chyby během nasazení najdete v tématu [zobrazení operací nasazení](resource-manager-deployment-operations.md).
