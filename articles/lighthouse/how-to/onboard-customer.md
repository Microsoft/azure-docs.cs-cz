---
title: Onboarding zákazníků do služby Azure Lighthouse
description: Naučte se, jak začlenit zákazníka do Azure Lighthouse, který umožňuje získat a spravovat jejich prostředky prostřednictvím vlastního tenanta pomocí delegované správy prostředků Azure.
ms.date: 12/15/2020
ms.topic: how-to
ms.openlocfilehash: 023b44a77cb38a14df8aa6a885ff137c02942061
ms.sourcegitcommit: 66479d7e55449b78ee587df14babb6321f7d1757
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/15/2020
ms.locfileid: "97516132"
---
# <a name="onboard-a-customer-to-azure-lighthouse"></a>Onboarding zákazníků do služby Azure Lighthouse

V tomto článku se dozvíte, jak jako poskytovatel služeb můžete zákazníka připojit do Azure Lighthouse. Pokud tak učiníte, delegované prostředky (předplatná nebo skupiny prostředků) můžete v tenantovi Azure Active Directory zákazníka (Azure AD) spravovat prostřednictvím vašeho vlastního tenanta pomocí [delegované správy prostředků Azure](../concepts/azure-delegated-resource-management.md).

> [!TIP]
> I když v tomto tématu odkazujeme na poskytovatele služeb a zákazníky, můžou podniky, které [spravují víc tenantů](../concepts/enterprise.md) , použít stejný postup k nastavení Azure Lighthouse a konsolidovat prostředí pro správu.

Postup připojování můžete opakovat pro více zákazníků. Když se uživatel s příslušnými oprávněními přihlásí k vašemu spravovanému tenantovi, může být tento uživatel autorizovaný pro jednotlivé obory tenantů pro zákazníky, aby mohl provádět operace správy, aniž by se musel přihlašovat ke každému klientovi v rámci zákazníka.

Pokud chcete sledovat svůj dopad napříč zapojením zákazníků a získávat rozpoznávání, přidružte své ID Microsoft Partner Network (MPN) k alespoň jednomu uživatelskému účtu, který má přístup ke každému z vašich integrovaných předplatných. Toto přidružení bude nutné provést v tenantovi poskytovatele služeb. Ve vašem tenantovi doporučujeme vytvořit instanční účet služby, který je přidružený k vašemu ID MPN, a pak tento instanční objekt, který bude pokaždé, když se připojíte k zákazníkovi. Další informace najdete v tématu [propojení ID partnera, aby bylo možné na delegovaných zdrojích povolit kredit získaný pro partnery](partner-earned-credit.md).

> [!NOTE]
> Zákazníci se můžou do Azure Lighthouse připravit při nákupu nabídky spravované služby (veřejné nebo soukromé), kterou [publikujete do Azure Marketplace](publish-managed-services-offers.md). Můžete také použít proces zprovoznění popsaný tady spolu s nabídkami publikovanými do Azure Marketplace.

Proces zprovoznění vyžaduje akce, které se mají provést v rámci tenanta poskytovatele služeb i z tenanta zákazníka. Všechny tyto kroky jsou popsány v tomto článku.

## <a name="gather-tenant-and-subscription-details"></a>Získat podrobnosti o klientovi a předplatném

Pokud chcete připojit tenanta zákazníka, musí mít aktivní předplatné Azure. Budete potřebovat následující informace:

- ID tenanta tenanta poskytovatele služeb (kde budete spravovat prostředky zákazníka)
- ID tenanta tenanta zákazníka (který bude mít prostředky spravované poskytovatelem služeb)
- ID předplatných pro každé konkrétní předplatné v tenantovi zákazníka, které bude spravovat poskytovatel služeb (nebo který obsahuje skupiny prostředků, které bude spravovat poskytovatel služeb).

Pokud tyto hodnoty ID již nemáte, můžete je načíst jedním z následujících způsobů. Ujistěte se, že používáte tyto přesné hodnoty v nasazení.

### <a name="azure-portal"></a>portál Azure

ID tenanta si můžete zobrazit tak, že najedete myší na název účtu v horní pravé části Azure Portal, nebo výběrem **přepínače Adresář**. Pokud chcete vybrat a zkopírovat ID tenanta, vyhledejte na portálu "Azure Active Directory", pak vyberte **vlastnosti** a zkopírujte hodnotu zobrazenou v poli **ID adresáře** . Pokud chcete najít ID předplatného v tenantovi zákazníka, vyhledejte "Subscriptions" a pak vyberte příslušné ID předplatného.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

Select-AzSubscription <subscriptionId>
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az account set --subscription <subscriptionId/name>
az account show
```

> [!NOTE]
> Při připojování předplatného (nebo jedné nebo více skupin prostředků v rámci předplatného) pomocí popsaného postupu se poskytovatel prostředků **Microsoft. ManagedServices** zaregistruje pro toto předplatné.

## <a name="define-roles-and-permissions"></a>Definování rolí a oprávnění

Jako poskytovatel služeb můžete chtít pro jednoho zákazníka provést několik úloh, které vyžadují různý přístup pro různé obory. Chcete-li přiřadit příslušné [předdefinované role Azure](../../role-based-access-control/built-in-roles.md) uživatelům ve vašem tenantovi, můžete definovat tolik autorizací, kolik potřebujete.

Pro zjednodušení správy doporučujeme používat pro každou roli skupiny uživatelů Azure AD. Získáte tak flexibilitu při přidávání nebo odebírání jednotlivých uživatelů do skupiny, která má přístup, takže nemusíte opakovat proces připojování, aby se změny projevily uživatelem. Role můžete přiřadit instančnímu objektu, který může být užitečný pro scénáře automatizace.

> [!IMPORTANT]
> Aby bylo možné přidat oprávnění pro skupinu Azure AD, musí být **typ skupiny** nastavený na **zabezpečení**. Tato možnost je vybrána při vytváření skupiny. Další informace najdete v tématu [Vytvoření základní skupiny a přidání členů pomocí Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

Při definování autorizací nezapomeňte postupovat podle principu minimálního oprávnění, aby uživatelé měli jenom oprávnění potřebná k dokončení své úlohy. Pokyny a informace o podporovaných rolích najdete [v tématu Klienti, uživatelé a role ve scénářích Azure Lighthouse](../concepts/tenants-users-roles.md).

Pokud chcete definovat autorizaci, budete muset znát hodnoty ID pro každého uživatele, skupinu uživatelů nebo instanční objekt v tenantovi poskytovatele služeb, ke kterému chcete udělit přístup. Také budete potřebovat ID definice role pro každou předdefinovanou roli, kterou chcete přiřadit. Pokud je ještě nemáte, můžete je načíst spuštěním příkazů níže v rámci tenanta poskytovatele služeb.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# To retrieve the objectId for an Azure AD group
(Get-AzADGroup -DisplayName '<yourGroupName>').id

# To retrieve the objectId for an Azure AD user
(Get-AzADUser -UserPrincipalName '<yourUPN>').id

# To retrieve the objectId for an SPN
(Get-AzADApplication -DisplayName '<appDisplayName>' | Get-AzADServicePrincipal).Id

# To retrieve role definition IDs
(Get-AzRoleDefinition -Name '<roleName>').id
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# To retrieve the objectId for an Azure AD group
az ad group list --query "[?displayName == '<yourGroupName>'].objectId" --output tsv

# To retrieve the objectId for an Azure AD user
az ad user show --id "<yourUPN>" --query "objectId" --output tsv

# To retrieve the objectId for an SPN
az ad sp list --query "[?displayName == '<spDisplayName>'].objectId" --output tsv

# To retrieve role definition IDs
az role definition list --name "<roleName>" | grep name
```

> [!TIP]
> Při připojování zákazníka doporučujeme přiřadit [roli odstranění registrace spravovaných služeb](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) , aby uživatelé ve vašem tenantovi mohli později v případě potřeby [Odebrat přístup k delegování](remove-delegation.md) . Pokud tato role není přiřazená, delegované prostředky může odebrat jenom uživatel v tenantovi zákazníka.

## <a name="create-an-azure-resource-manager-template"></a>Vytvoření šablony Azure Resource Manager

K připojení zákazníka budete muset vytvořit šablonu [Azure Resource Manager](../../azure-resource-manager/index.yml) pro vaši nabídku s následujícími informacemi. Hodnoty **mspOfferName** a **mspOfferDescription** se zákazníkům zobrazí na [stránce poskytovatelé služeb](view-manage-service-providers.md) Azure Portal.

|Pole  |Definice  |
|---------|---------|
|**mspOfferName**     |Název popisující tuto definici. Tato hodnota se zobrazí zákazníkovi jako název nabídky a musí se jednat o jedinečnou hodnotu.        |
|**mspOfferDescription**     |Stručný popis vaší nabídky (například "nabídka správy virtuálních počítačů contoso").      |
|**managedByTenantId**     |Vaše ID tenanta.          |
|**autorizace**     |Hodnoty **principalId** pro uživatele/skupiny/hlavní názvy služby z vašeho tenanta, každý s **principalIdDisplayName** , které vašemu zákazníkovi pomůžou pochopit účel autorizace a namapovány na integrovanou hodnotu **roleDefinitionId** , která určuje úroveň přístupu.      |

Proces zprovoznění vyžaduje šablonu Azure Resource Manager (poskytnutou v [úložišti ukázek](https://github.com/Azure/Azure-Lighthouse-samples/)) a odpovídající soubor parametrů, který upravíte tak, aby odpovídal vaší konfiguraci a definoval vaše autorizace.

> [!IMPORTANT]
> Tento proces, který je zde popsán, vyžaduje samostatné nasazení pro každé připojení k odběru, a to i v případě, že se odběry přihlásily do stejného tenanta zákazníka. Pokud se připojujete k několika skupinám prostředků v rámci různých předplatných ve stejném tenantovi zákazníka, vyžaduje se také samostatné nasazení. Připojování více skupin prostředků v rámci jednoho předplatného se ale dá udělat v jednom nasazení.
>
> Pro stejné předplatné (nebo skupiny prostředků v rámci předplatného se taky vyžadují samostatná nasazení). Každá použitá nabídka musí používat jiný **mspOfferName**.

Šablona, kterou zvolíte, bude záviset na tom, jestli se chystáte registrovat celé předplatné, skupinu prostředků nebo víc skupin prostředků v rámci předplatného. Poskytujeme také šablonu, která se dá použít pro zákazníky, kteří si zakoupili nabídku spravované služby, kterou jste publikovali na Azure Marketplace, pokud upřednostňujete jejich odběry tímto způsobem.

|K zaregistrování  |Použít tuto šablonu Azure Resource Manager  |A upravit tento soubor parametrů |
|---------|---------|---------|
|Předplatné   |[delegatedResourceManagement.jsna](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management/delegatedResourceManagement.json)  |[delegatedResourceManagement.parameters.jsna](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management/delegatedResourceManagement.parameters.json)    |
|Skupina prostředků   |[rgDelegatedResourceManagement.jsna](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.json)  |[rgDelegatedResourceManagement.parameters.jsna](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.parameters.json)    |
|Více skupin prostředků v předplatném   |[multipleRgDelegatedResourceManagement.jsna](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.json)  |[multipleRgDelegatedResourceManagement.parameters.jsna](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.parameters.json)    |
|Předplatné (při použití nabídky publikované do Azure Marketplace)   |[marketplaceDelegatedResourceManagement.jsna](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.json)  |[marketplaceDelegatedResourceManagement.parameters.jsna](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.parameters.json)    |

> [!TIP]
> I když nemůžete připojit celou skupinu pro správu v jednom nasazení, můžete [zásadu nasadit na úrovni skupiny pro správu](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-delegate-management-groups). Zásada zkontroluje, jestli je každé předplatné ve skupině pro správu delegované na zadaného spravovaného tenanta, a pokud ne, vytvoří přiřazení na základě zadaných hodnot.

Následující příklad ukazuje upravený **delegatedResourceManagement.parameters.js** souboru, který se dá použít k zaregistrování předplatného. Soubory parametrů skupiny prostředků (nacházející se ve složce [RG-delegované pro správu prostředků](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/rg-delegated-resource-management) ) jsou podobné, ale také obsahují parametr **RgName** pro identifikaci konkrétních skupin prostředků, které se mají připojit.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "mspOfferName": {
            "value": "Fabrikam Managed Services - Interstellar"
        },
        "mspOfferDescription": {
            "value": "Fabrikam Managed Services - Interstellar"
        },
        "managedByTenantId": {
            "value": "df4602a3-920c-435f-98c4-49ff031b9ef6"
        },
        "authorizations": {
            "value": [
                {
                    "principalId": "0019bcfb-6d35-48c1-a491-a701cf73b419",
                    "principalIdDisplayName": "Tier 1 Support",
                    "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"
                },
                {
                    "principalId": "0019bcfb-6d35-48c1-a491-a701cf73b419",
                    "principalIdDisplayName": "Tier 1 Support",
                    "roleDefinitionId": "36243c78-bf99-498c-9df9-86d9f8d28608"
                },
                {
                    "principalId": "0afd8497-7bff-4873-a7ff-b19a6b7b332c",
                    "principalIdDisplayName": "Tier 2 Support",
                    "roleDefinitionId": "acdd72a7-3385-48ef-bd42-f606fba81ae7"
                },
                {
                    "principalId": "9fe47fff-5655-4779-b726-2cf02b07c7c7",
                    "principalIdDisplayName": "Service Automation Account",
                    "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"
                },
                {
                    "principalId": "3kl47fff-5655-4779-b726-2cf02b05c7c4",
                    "principalIdDisplayName": "Policy Automation Account",
                    "roleDefinitionId": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
                    "delegatedRoleDefinitionIds": [
                        "b24988ac-6180-42a0-ab88-20f7382dd24c",
                        "92aaf0da-9dab-42b6-94a3-d43ce8d16293"
                    ]
                }
            ]
        }
    }
}
```

Poslední autorizace v předchozím příkladu přidá **principalId** s rolí správce přístupu uživatele (18d7d88d-d35e-4fb5-a5c3-7773c20a72d9). Při přiřazování této role musíte zahrnout vlastnost **delegatedRoleDefinitionIds** a jednu nebo více předdefinovaných rolí. Uživatel vytvořený v této autorizaci bude moci přiřadit tyto předdefinované role ke [spravovaným identitám](../../active-directory/managed-identities-azure-resources/overview.md) v tenantovi zákazníka, který je potřeba k [nasazení zásad, které se dají opravit](deploy-policy-remediation.md).  Uživatel také může vytvořit incidenty podpory.  Pro tohoto uživatele se nebudou vztahovat žádná další oprávnění normálně přidružená k roli správce přístupu uživatele.

## <a name="deploy-the-azure-resource-manager-templates"></a>Nasazení šablon Azure Resource Manager

Po aktualizaci souboru parametrů musí uživatel v tenantovi zákazníka nasadit šablonu Azure Resource Manager v rámci svého tenanta. Pro každé předplatné, které chcete připojit, je potřeba samostatné nasazení (nebo pro každé předplatné, které obsahuje skupiny prostředků, které chcete připojit).

> [!IMPORTANT]
> Toto nasazení musí provést účet bez hosta v tenantovi zákazníka, který má [předdefinovanou roli](../../role-based-access-control/built-in-roles.md#owner) předplatného pro odběr (nebo který obsahuje skupiny prostředků, které jsou připojené). Pokud chcete zobrazit všechny uživatele, kteří můžou delegovat předplatné, uživatel v tenantovi zákazníka může vybrat předplatné ve Azure Portal, otevřít **řízení přístupu (IAM)** a [Zobrazit všechny uživatele s rolí vlastníka](../../role-based-access-control/role-assignments-list-portal.md#list-owners-of-a-subscription). 
>
> Pokud bylo předplatné vytvořeno prostřednictvím [programu Cloud Solution Provider (CSP)](../concepts/cloud-solution-provider.md), může nasazení provést libovolný uživatel, který má v tenantovi poskytovatele služeb roli [agenta správce](/partner-center/permissions-overview#manage-commercial-transactions-in-partner-center-azure-ad-and-csp-roles) .

Nasazení se může provést v Azure Portal, pomocí PowerShellu nebo pomocí rozhraní příkazového řádku Azure CLI, jak vidíte níže.

### <a name="azure-portal"></a>portál Azure

1. V našem [úložišti GitHub](https://github.com/Azure/Azure-Lighthouse-samples/)vyberte tlačítko **nasadit do Azure** zobrazené vedle šablony, kterou chcete použít. Šablona se otevře v prostředí Azure Portal.
1. Zadejte hodnoty pro **název nabídky MSP**, **Popis nabídky MSP**, **spravovaný podle ID tenanta** a **autorizací**. Pokud dáváte přednost, můžete vybrat možnost **Upravit parametry** a zadat hodnoty pro `mspOfferName` , `mspOfferDescription` , `managedbyTenantId` a `authorizations` přímo do souboru parametrů. Nezapomeňte aktualizovat tyto hodnoty namísto použití výchozích hodnot z šablony.
1. Vyberte **zkontrolovat a vytvořit** a pak vyberte **vytvořit**.

Po několika minutách by se měla zobrazit zpráva s oznámením, že nasazení bylo dokončeno.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Deploy Azure Resource Manager template using template and parameter file locally
New-AzSubscriptionDeployment -Name <deploymentName> `
                 -Location <AzureRegion> `
                 -TemplateFile <pathToTemplateFile> `
                 -TemplateParameterFile <pathToParameterFile> `
                 -Verbose

# Deploy Azure Resource Manager template that is located externally
New-AzSubscriptionDeployment -Name <deploymentName> `
                 -Location <AzureRegion> `
                 -TemplateUri <templateUri> `
                 -TemplateParameterUri <parameterUri> `
                 -Verbose
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# Deploy Azure Resource Manager template using template and parameter file locally
az deployment sub create --name <deploymentName> \
                         --location <AzureRegion> \
                         --template-file <pathToTemplateFile> \
                         --parameters <parameters/parameterFile> \
                         --verbose

# Deploy external Azure Resource Manager template, with local parameter file
az deployment sub create --name <deploymentName> \
                         --location <AzureRegion> \
                         --template-uri <templateUri> \
                         --parameters <parameterFile> \
                         --verbose
```

## <a name="confirm-successful-onboarding"></a>Potvrzení úspěšného zprovoznění

Po úspěšném připojení zákaznického předplatného do Azure Lighthouse uvidí uživatelé v tenantovi poskytovatele služeb předplatné a jeho prostředky (pokud jim k ní byl udělen přístup prostřednictvím výše uvedeného procesu), a to buď jednotlivě, nebo jako člen skupiny Azure AD s příslušnými oprávněními. Potvrďte to tak, že zkontrolujete, že se odběr zobrazuje jedním z následujících způsobů:  

### <a name="azure-portal"></a>portál Azure

V tenantovi poskytovatele služeb:

1. Přejděte na [stránku Moji zákazníci](view-manage-customers.md).
2. Vyberte **Zákazníci**.
3. Potvrďte, že si můžete zobrazit odběry s názvem nabídky, který jste zadali v šabloně Správce prostředků.

> [!IMPORTANT]
> Aby bylo možné zobrazit delegované předplatné ve [svých zákaznících](view-manage-customers.md), uživatelé v tenantovi poskytovatele služeb musí mít přiřazenou roli [Čtenář](../../role-based-access-control/built-in-roles.md#reader) (nebo jinou předdefinovanou roli, která zahrnuje přístup čtenářů) při registraci předplatného.

V tenantovi zákazníka:

1. Přejděte na [stránku poskytovatelé služeb](view-manage-service-providers.md).
2. Vyberte **Nabídky poskytovatele služeb**.
3. Potvrďte, že si můžete zobrazit odběry s názvem nabídky, který jste zadali v šabloně Správce prostředků.

> [!NOTE]
> Po dokončení nasazení může trvat několik minut, než se aktualizace projeví v Azure Portal.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

Get-AzContext

# Confirm successful onboarding for Azure Lighthouse

Get-AzManagedServicesDefinition
Get-AzManagedServicesAssignment
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az account list
```

Pokud po zprovoznění zákazníka potřebujete provést změny, můžete [delegování aktualizovat](update-delegation.md). [Přístup k delegování](remove-delegation.md) můžete také odebrat úplně.

## <a name="troubleshooting"></a>Řešení potíží

Pokud se vám nepodaří úspěšně připojit zákazníka nebo pokud vaši uživatelé mají potíže s přístupem k delegovaným prostředkům, Projděte si následující tipy a požadavky a zkuste to znovu.

- `managedbyTenantId`Hodnota nesmí být stejná jako ID tenanta pro odběr, který se připojuje.
- Nemůžete mít více přiřazení ve stejném oboru se stejným oborem `mspOfferName` . 
- U delegovaného předplatného musí být zaregistrován poskytovatel prostředků **Microsoft. ManagedServices** . K tomu by mělo dojít automaticky během nasazování, ale v případě potřeby je můžete [zaregistrovat ručně](../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider).
- Autorizace nesmí obsahovat žádné uživatele s předdefinovanou rolí [vlastníka](../../role-based-access-control/built-in-roles.md#owner) ani žádné předdefinované role s [akcemi](../../role-based-access-control/role-definitions.md#dataactions).
- Skupiny musí být vytvořeny s [**typem skupiny**](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md#group-types) nastavenou na **zabezpečení** a nesmí **Microsoft 365**.
- Uživatelé, kteří potřebují zobrazit prostředky v Azure Portal, musí mít roli [Čtenář](../../role-based-access-control/built-in-roles.md#reader) (nebo jinou předdefinovanou roli, která zahrnuje přístup ke čtenářům).

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [prostředích pro správu mezi klienty](../concepts/cross-tenant-management-experience.md).
- V **Azure Portal můžete** [Zobrazit a spravovat zákazníky](view-manage-customers.md) .
- Přečtěte si, jak [aktualizovat](update-delegation.md) nebo [Odebrat](remove-delegation.md) delegování.
