---
title: "Odstraňování běžných chyb nasazení Azure | Microsoft Docs"
description: "Popisuje postup řešení běžných chyb při nasazování prostředků do Azure pomocí Azure Resource Manager."
services: azure-resource-manager
documentationcenter: 
tags: top-support-issue
author: tfitzmac
manager: timlt
editor: tysonn
keywords: "Chyba nasazení, nasazení azure nasazení do azure"
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: support-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/08/2018
ms.author: tomfitz
ms.openlocfilehash: 2cf31b32e02923aa573d5586b8ca24bf30b7d97b
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2018
---
# <a name="troubleshoot-common-azure-deployment-errors-with-azure-resource-manager"></a>Odstraňování běžných chyb nasazení Azure pomocí Azure Resource Manageru

Tento článek popisuje některé běžné chyby nasazení Azure můžete setkat a obsahuje informace, které případné chyby opravte. Pokud nemůžete najít kód chyby pro vaše nasazení chyby, přečtěte si téma [najít kód chyby](#find-error-code).

## <a name="error-codes"></a>Kódy chyb

| Kód chyby | Omezení rizik | Další informace |
| ---------- | ---------- | ---------------- |
| AccountNameInvalid | Postupujte podle omezení pro pojmenovávání pro účty úložiště. | [Přeložit název účtu úložiště](resource-manager-storage-account-name-errors.md) |
| AccountPropertyCannotBeSet | Zkontrolujte vlastnosti účtu úložiště k dispozici. | [storageAccounts](/azure/templates/microsoft.storage/storageaccounts) |
| AllocationFailed | Na clusteru nemá k dispozici prostředky nebo nemůže podporovat požadovaná velikost virtuálního počítače. Opakovat požadavek později, nebo požádejte jinou velikost virtuálního počítače. | [Problémy se zřizováním a přidělení Linux](../virtual-machines/linux/troubleshoot-deployment-new-vm.md) a [problémy zřizování a přidělení pro Windows](../virtual-machines/windows/troubleshoot-deployment-new-vm.md) |
| AnotherOperationInProgress | Počkejte na dokončení souběžná operace. | |
| AuthorizationFailed | Váš účet nebo instanční objekt nemá dostatečný přístup k dokončení nasazení. Zkontrolujte roli, kterou váš účet patří do a přístup pro obor nasazení. | [Řízení přístupu Azure na základě rolí](../active-directory/role-based-access-control-configure.md) |
| Struktura BadRequest | Jste poslali hodnot nasazení, které neodpovídají očekávané pomocí Správce prostředků. Zkontrolujte zprávy vnitřní stav a nápovědu k řešení potíží. | [Odkaz na šablonu](/azure/templates/) a [podporované umístění](resource-manager-templates-resources.md#location) |
| Konflikt | Požadujete operace, který není povolen v aktuálním stavu prostředku. Například změna velikosti disku je povolená pouze při vytváření virtuálního počítače, nebo když je virtuální počítač navrácený. | |
| DeploymentActive | Počkejte, než pro souběžné nasazení do této skupiny prostředků pro dokončení. | |
| DeploymentFailed | Chyba DeploymentFailed je obecná chyba, která neobsahuje podrobnosti, budete muset vyřešit chyby. Vyhledejte v podrobnostech o chybě chybový kód, který poskytuje další informace. | [Najít kód chyby](#find-error-code) |
| DnsRecordInUse | Název záznamu DNS musí být jedinečný. Buď zadejte jiný název, nebo upravte stávající záznamu. | |
| ImageNotFound | Zkontrolujte nastavení bitové kopie virtuálního počítače. |  |
| InUseSubnetCannotBeDeleted | Této chybě může dojít při pokusu o aktualizaci prostředek, ale je požadavek zpracován odstranit a vytvoření prostředku. Nezapomeňte zadat všechny hodnoty beze změny. | [Aktualizace prostředku](/azure/architecture/building-blocks/extending-templates/update-resource) |
| InvalidAuthenticationTokenTenant | Získání tokenu přístupu pro příslušné klienta. Pouze můžete získat token z klienta, který váš účet patří do. | |
| InvalidContentLink | Pokusili jste s největší pravděpodobností propojení vnořené šablony, která není k dispozici. Překontrolujte identifikátor URI, který jste zadali pro vnořené šablonu. Pokud šablona již existuje v účtu úložiště, zkontrolujte, zda že identifikátor URI je přístupný. Potřebujete předat SAS token. | [propojených šablon](resource-group-linked-templates.md) |
| InvalidParameter | Jedna z hodnot, které jste zadali pro zdroj neodpovídá očekávané hodnotě. Tato chyba může způsobit z mnoha různých podmínkách. Například může být nedostatek heslo nebo název objektu blob může být nesprávný. Zkontrolujte chybové zprávy k určení, která hodnota je třeba opravit. | |
| InvalidRequestContent | Vaše nasazení hodnoty buď patří hodnoty, které nejsou očekávané nebo chybí požadované hodnoty. Zkontrolujte hodnoty pro váš typ prostředku. | [Odkaz na šablonu](/azure/templates/) |
| InvalidRequestFormat | Povolit protokolování ladění při provádění nasazení a ověřte obsah žádosti. | [Ladění protokolování](#enable-debug-logging) |
| InvalidResourceNamespace | Zkontrolujte obor názvů prostředků, které jste zadali v **typ** vlastnost. | [Odkaz na šablonu](/azure/templates/) |
| InvalidResourceReference | Prostředek ještě neexistuje nebo je nesprávně odkazuje. Zkontrolujte, jestli je potřeba přidat závislost. Ověřte, že používání **odkaz** požadované parametry pro váš scénář zahrnuje funkce. | [Vyřešte závislosti](resource-manager-not-found-errors.md) |
| InvalidResourceType | Zadejte kontrola prostředku, kterou jste zadali v **typ** vlastnost. | [Odkaz na šablonu](/azure/templates/) |
| InvalidTemplate | Zkontrolujte syntaxi šablony chyb. | [Neplatná šablona řešení](resource-manager-invalid-template-errors.md) |
| LinkedAuthorizationFailed | Zkontrolujte, pokud váš účet patří do stejné klienta jako pro skupinu prostředků, které nasazujete. | |
| LinkedInvalidPropertyId | ID prostředku pro prostředek není správně řešení. Zkontrolujte, že poskytujete všechny požadované hodnoty pro ID prostředku, včetně ID odběru, název skupiny prostředků, typ prostředku, název nadřazené prostředků (v případě potřeby) a název prostředku. | |
| LocationRequired | Zadejte umístění prostředku. | [Nastavení umístění](resource-manager-templates-resources.md#location) |
| MissingRegistrationForLocation | Zkontrolujte stav registrace poskytovatele prostředků a podporovaných umístění. | [Vyřešte registrace](resource-manager-register-provider-errors.md) |
| MissingSubscriptionRegistration | Zaregistrujte své předplatné s poskytovatelem prostředků. | [Vyřešte registrace](resource-manager-register-provider-errors.md) |
| NoRegisteredProviderFound | Zkontrolujte stav registrace poskytovatele prostředků. | [Vyřešte registrace](resource-manager-register-provider-errors.md) |
| Nenalezeno | Může se pokoušíte nasadit závislý prostředek paralelně s nadřazený prostředek. Zkontrolujte, pokud potřebujete přidat závislost. | [Vyřešte závislosti](resource-manager-not-found-errors.md) |
| OperationNotAllowed | Nasazení se pokouší operace, která překračuje kvótu pro předplatné, skupinu prostředků nebo oblasti. Pokud je to možné zkontrolovat, jestli vaše nasazení zůstane v rámci kvóty. Zvažte, jinak hodnota požadavku na změnu vaší kvóty. | [Vyřešte kvóty](resource-manager-quota-errors.md) |
| ParentResourceNotFound | Ujistěte se, že nadřazený prostředek existuje před vytvořením podřízené prostředky. | [Vyřešte nadřazený prostředek](resource-manager-parent-resource-errors.md) |
| PrivateIPAddressInReservedRange | Zadaná IP adresa obsahuje rozsah adres, vyžaduje Azure. Změna IP adresy, aby se zabránilo vyhrazený rozsah. | [IP adresy](../virtual-network/virtual-network-ip-addresses-overview-arm.md) |
| PrivateIPAddressNotInSubnet | Zadaná IP adresa je mimo rozsah podsítě. Změnit IP adresu, která spadá do rozsahu podsítě. | [IP adresy](../virtual-network/virtual-network-ip-addresses-overview-arm.md) |
| PropertyChangeNotAllowed | Některé vlastnosti nelze změnit na nasazené prostředku. Při aktualizaci prostředku, omezte vaše změny povolených vlastností. | [Aktualizace prostředku](/azure/architecture/building-blocks/extending-templates/update-resource) |
| RequestDisallowedByPolicy | Vaše předplatné zahrnuje zásady prostředků, které brání akce, které se pokoušíte provést během nasazení. Najít zásady, které blokuje akce. Pokud je to možné změňte nasazení tak, aby splňovaly omezení ze zásad. | [Vyřešte zásady](resource-manager-policy-requestdisallowedbypolicy-error.md) |
| ReservedResourceName | Zadejte název prostředku, který nezahrnuje o vyhrazený název. | [Názvy vyhrazené prostředků](resource-manager-reserved-resource-name.md) |
| ResourceGroupBeingDeleted | Počkejte na dokončení odstranění. | |
| ResourceGroupNotFound | Zkontrolujte název cílové skupiny prostředků pro nasazení. Již musí existovat v rámci vašeho předplatného. Zkontrolujte kontext vašeho předplatného. | [Azure CLI](/cli/azure/account?#az_account_set) [PowerShell](/powershell/module/azurerm.profile/set-azurermcontext) |
| ResourceNotFound | Nasazení odkazuje na prostředek, který nelze vyřešit. Ověřte, že používání **odkaz** parametrů požadovaných pro váš scénář zahrnuje funkce. | [Překlad odkazů](resource-manager-not-found-errors.md) |
| ResourceQuotaExceeded | Nasazení se pokouší vytvořit prostředky, které překročí kvótu pro předplatné, skupinu prostředků nebo oblasti. Pokud je to možné upraveno infrastrukturu tak, aby zůstat v rámci kvóty. Zvažte, jinak hodnota požadavku na změnu vaší kvóty. | [Vyřešte kvóty](resource-manager-quota-errors.md) |
| SkuNotAvailable | Vyberte SKU (například velikost virtuálního počítače), která je k dispozici pro umístění, které jste vybrali. | [Vyřešte SKU](resource-manager-sku-not-available-errors.md) |
| StorageAccountAlreadyExists | Zadejte jedinečný název pro účet úložiště. | [Přeložit název účtu úložiště](resource-manager-storage-account-name-errors.md)  |
| StorageAccountAlreadyTaken | Zadejte jedinečný název pro účet úložiště. | [Přeložit název účtu úložiště](resource-manager-storage-account-name-errors.md) |
| StorageAccountNotFound | Zkontrolujte předplatné, skupinu prostředků a název účtu úložiště, ke které chcete použít. | |
| SubnetsNotInSameVnet | Virtuální počítač může mít pouze jednu virtuální síť. Pokud nasazujete víc síťových karet, ujistěte se, že patří do stejné virtuální síti. | [Několik síťových adaptérů](../virtual-machines/windows/multiple-nics.md) |

## <a name="find-error-code"></a>Najít kód chyby

Existují dva typy chyb, které dostanete:

* Chyby ověření
* Chyby nasazení

Chyby ověření jsou vyvolány scénáře, které lze určit před nasazením. Obsahují chyby syntaxe v šabloně nebo pokusu o nasazení prostředky, které překročí vaší kvóty předplatného. Chyby nasazení jsou vyvolány podmínky, které se vyskytují během procesu nasazení. Patří mezi ně pokusu o přístup k prostředku, který je nasazován paralelně.

Oba typy chyb vrátí kód chyby, který můžete použít k nasazení řešení. Oba typy chyb se zobrazí v [protokol aktivit](resource-group-audit.md). Ale chyb při ověřování se nezobrazí v historii nasazení protože nasazení nikdy spuštěn.

### <a name="validation-errors"></a>Chyby ověření

Pokud nasazujete prostřednictvím portálu, zobrazí chybu ověření po odeslání svoje hodnoty.

![zobrazit chyba portálu ověření](./media/resource-manager-common-deployment-errors/validation-error.png)

Vyberte další podrobnosti najdete v zprávě. Na následujícím obrázku vidíte **InvalidTemplateDeployment** chyba a zprávu, která určuje zásadu blokován nasazení.

![Zobrazit podrobnosti o ověření](./media/resource-manager-common-deployment-errors/validation-details.png)

### <a name="deployment-errors"></a>Chyby nasazení

Při operaci ověřením úspěšně projde, ale během nasazení se nezdaří, zobrazí se tato chyba v oznámení. Vyberte oznámení.

![Chyba oznámení](./media/resource-manager-common-deployment-errors/notification.png)

Zobrazí podrobnosti o tomto nasazení. Vyberte možnost Najít další informace o této chybě.

![nasazení se nezdařilo.](./media/resource-manager-common-deployment-errors/deployment-failed.png)

Zobrazí chybovou zprávu a kódy chyb. Všimněte si, že jsou dva kódy chyb. První kód chyby (**DeploymentFailed**) je obecná chyba, která neposkytuje informace potřebné k odstranění chyby. Druhý kód chyby (**StorageAccountNotFound**) poskytuje informace potřebné. 

![Podrobnosti o chybě](./media/resource-manager-common-deployment-errors/error-details.png)

## <a name="enable-debug-logging"></a>Povolit protokolování ladění

Někdy potřebovat další informace o požadavku a odpovědi, které se dozvíte, co došlo k chybě. Pomocí prostředí PowerShell nebo rozhraní příkazového řádku Azure, můžete požádat, že další informace o protokolu během nasazení.

- PowerShell

   V prostředí PowerShell nastavená **DeploymentDebugLogLevel** parametr ke všem, ResponseContent nebo RequestContent.

  ```powershell
  New-AzureRmResourceGroupDeployment -ResourceGroupName examplegroup -TemplateFile c:\Azure\Templates\storage.json -DeploymentDebugLogLevel All
  ```

   Zkontrolujte požadavek obsahu pomocí následující rutiny:

  ```powershell
  (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName storageonly -ResourceGroupName startgroup).Properties.request | ConvertTo-Json
  ```

   Nebo obsahu se odpovědi:

  ```powershell
  (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName storageonly -ResourceGroupName startgroup).Properties.response | ConvertTo-Json
  ```

   Tyto informace můžete určit, zda probíhá nesprávně nastavení na hodnotu v šabloně.

- Azure CLI

   Zkontrolujte operace nasazení pomocí následujícího příkazu:

  ```azurecli
  az group deployment operation list --resource-group ExampleGroup --name vmlinux
  ```

- Vnořené šablony

   Do protokolu informace o ladění vnořené šablony, pomocí **debugSetting** element.

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

## <a name="create-a-troubleshooting-template"></a>Vytvořit šablonu řešení potíží

V některých případech je nejjednodušší způsob, jak vyřešit šablony otestovat části. Můžete vytvořit zjednodušená šablonu, která umožňuje zaměřit se na část, která budete mít dojem, je příčinou chyby. Předpokládejme například, že jste obdrželi chybu při odkazování na prostředek. Místo práci s celou šablony, vytvořte šablonu, která vrátí část, která může být příčinou problému. Může pomoct určit, zda jsou předávání v správné parametry, pomocí šablony funkce správně, a získávání prostředek očekáváte.

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

Nebo Předpokládejme, že dochází k chybám nasazení, které budete mít dojem, že se vztahují k nesprávné sady závislosti. Testování šablony porušením do zjednodušené šablony. Nejprve vytvořte šablonu, která nasadí pouze jeden prostředek (např. SQL Server). Pokud jste si jistí, že máte správně definované prostředku, přidejte na prostředek, který na něm závisí (např. SQL Database). Až budete mít tyto dva prostředky správně definované, přidejte další závislé prostředky (jako jsou zásady auditu). Mezi každou testovací nasazení Odstraňte skupinu prostředků. Ověřte, že je adekvátní testování závislosti.


## <a name="next-steps"></a>Další postup
* Další informace o auditování akce najdete v tématu [auditovat operace s Resource Managerem](resource-group-audit.md).
* Další informace o akce k určení chyby během nasazení najdete v tématu [zobrazit operace nasazení](resource-manager-deployment-operations.md).
