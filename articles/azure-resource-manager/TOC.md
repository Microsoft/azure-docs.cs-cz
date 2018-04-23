# [Dokumentace ke službě Azure Resource Manager](index.md)

# Přehled
## [Co je Resource Manager?](resource-group-overview.md)
## [Poskytovatelé a typy prostředků](resource-manager-supported-services.md)
## [Resource Manager a klasické nasazení](resource-manager-deployment-model.md)
## [Zásady správného řízení pro předplatná](resource-manager-subscription-governance.md)
## [Uspořádání prostředků s využitím skupin pro správu](management-groups-overview.md)

# Začínáme
## [Vytvoření šablony nasazení](resource-manager-create-first-template.md)
## [Rozšíření VS Code pro šablony](resource-manager-vscode-extension.md)
## [Visual Studio s Resource Managerem](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)

# Postup
## Vytváření šablon
### [Části šablon](resource-group-authoring-templates.md)
#### [Parametry](resource-manager-templates-parameters.md)
#### [Proměnné](resource-manager-templates-variables.md)
#### [Prostředky](resource-manager-templates-resources.md)
#### [Výstupy](resource-manager-templates-outputs.md)
### [Propojené a vnořené šablony](resource-group-linked-templates.md)
### [Definování závislosti mezi prostředky](resource-group-define-dependencies.md)
### [Vytvoření víc instancí](resource-group-create-multiple.md)
### [Aktualizace prostředku](/azure/architecture/building-blocks/extending-templates/update-resource)

## Nasazení
### Azure PowerShell
#### [Nasazení šablony](resource-group-template-deploy.md)
#### [Nasazení privátní šablony s tokenem SAS](resource-manager-powershell-sas-token.md)
#### [Export šablony a opětovné nasazení](resource-manager-export-template-powershell.md)
### Azure CLI
#### [Nasazení šablony](resource-group-template-deploy-cli.md)
#### [Nasazení privátní šablony s tokenem SAS](resource-manager-cli-sas-token.md)
#### [Export šablony a opětovné nasazení](resource-manager-export-template-cli.md)
### Azure Portal
#### [Nasazení prostředků](resource-group-template-deploy-portal.md)
#### [Export šablony](resource-manager-export-template.md)
### [REST API](resource-group-template-deploy-rest.md)
### [Několik skupin prostředků nebo předplatných](resource-manager-cross-resource-group-deployment.md)
### [Průběžná integrace se sadou Visual Studio Team Services](../vs-azure-tools-resource-groups-ci-in-vsts.md?toc=%2fazure%2fazure-resource-manager%2ftoc.json)
### [Předání zabezpečených hodnot během nasazení](resource-manager-keyvault-parameter.md)

## Spravovat
### [Azure PowerShell](powershell-azure-resource-manager.md)
### [Azure CLI](xplat-cli-azure-resource-manager.md)
### [Azure Portal](resource-group-portal.md)
### [Použití značek k uspořádání prostředků](resource-group-using-tags.md)
### [Přesunutí prostředků do nové skupiny nebo předplatného](resource-group-move-resources.md)
### [Programové vytváření předplatných EA](programmatically-create-subscription.md)
### [Vytvoření skupin pro správu](management-groups-create.md)
### [Správa skupin pro správu](management-groups-manage.md)
### [Příklady zásad správného řízení](resource-manager-subscription-examples.md)

## Řízení přístupu
### Vytvoření instančního objektu
#### [Azure PowerShell](resource-group-authenticate-service-principal.md)
#### [Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager%2ftoc.json)
#### [Azure Portal](resource-group-create-service-principal-portal.md)
### [Ověřovací rozhraní API pro přístup k předplatným](resource-manager-api-authentication.md)
### [Uzamčení prostředků](resource-group-lock-resources.md)

## Auditování
### [Zobrazení protokolů aktivit](resource-group-audit.md)
### [Zobrazení operací nasazení](resource-manager-deployment-operations.md)

## Řešení potíží
### [Řešení potíží s chybami nasazení](resource-manager-common-deployment-errors.md)
### [AccountNameInvalid](resource-manager-storage-account-name-errors.md)
### [InvalidTemplate](resource-manager-invalid-template-errors.md)
### [Potíže s linuxovým nasazením](../virtual-machines/linux/troubleshoot-deploy-vm.md)
### [NoRegisteredProviderFound](resource-manager-register-provider-errors.md)
### [NotFound](resource-manager-not-found-errors.md)
### [ParentResourceNotFound](resource-manager-parent-resource-errors.md)
### [Potíže se zřizováním a přidělením pro Linux](../virtual-machines/linux/troubleshoot-deployment-new-vm.md)
### [Potíže se zřizováním a přidělením pro Windows](../virtual-machines/windows/troubleshoot-deployment-new-vm.md)
### [RequestDisallowedByPolicy](resource-manager-policy-requestdisallowedbypolicy-error.md)
### [ReservedResourceName](resource-manager-reserved-resource-name.md)
### [ResourceQuotaExceeded](resource-manager-quota-errors.md)
### [SkuNotAvailable](resource-manager-sku-not-available-errors.md)
### [Potíže s nasazením Windows](../virtual-machines/windows/troubleshoot-deploy-vm.md)

# Referenční informace
## [Formát šablon](/azure/templates/)
## [Funkce šablon](resource-group-template-functions.md)
### [Funkce pole a objektu](resource-group-template-functions-array.md)
### [Funkce porovnání](resource-group-template-functions-comparison.md)
### [Funkce nasazení](resource-group-template-functions-deployment.md)
### [Logické funkce](resource-group-template-functions-logical.md)
### [Numerické funkce](resource-group-template-functions-numeric.md)
### [Funkce prostředků](resource-group-template-functions-resource.md)
### [Funkce řetězců](resource-group-template-functions-string.md)
## [PowerShell](/powershell/module/azurerm.resources)
## [Azure CLI](/cli/azure/resource)
## [.NET](/dotnet/api/microsoft.azure.management.resourcemanager)
## [Java](/java/api/com.microsoft.azure.management.resources)
## [Python](http://azure-sdk-for-python.readthedocs.io/en/latest/resourcemanagement.html)
## [REST](/rest/api/resources/)

# Zdroje a prostředky
## [Plány Azure do budoucna](https://azure.microsoft.com/roadmap/?category=monitoring-management)
## [ Cenová kalkulačka](https://azure.microsoft.com/pricing/calculator/)
## [Aktualizace služeb](https://azure.microsoft.com/updates/?product=azure-resource-manager)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-resource-manager)
## [Omezování požadavků](resource-manager-request-limits.md)
## [Sledování asynchronních operací](resource-manager-async-operations.md)
## [Videa](https://azure.microsoft.com/documentation/videos/index/?services=azure-resource-manager)
