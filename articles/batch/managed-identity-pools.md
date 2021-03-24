---
title: Konfigurace spravovaných identit ve fondech Batch
description: Naučte se, jak povolit uživatelsky přiřazené spravované identity ve fondech služby Batch a jak používat spravované identity v rámci uzlů.
ms.topic: conceptual
ms.date: 03/23/2021
ms.custom: references_regions
ms.openlocfilehash: 7fab213ac1545c0bff9b74bc46504717b6038e8e
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2021
ms.locfileid: "104950157"
---
# <a name="configure-managed-identities-in-batch-pools"></a>Konfigurace spravovaných identit ve fondech Batch

[Spravované identity pro prostředky Azure](../active-directory/managed-identities-azure-resources/overview.md) eliminují nutnost vývojářů spravovat přihlašovací údaje tím, že poskytují identitu prostředku Azure v Azure Active Directory (Azure AD) a používají ho k získání tokenů Azure Active Directory (Azure AD).

Toto téma vysvětluje, jak povolit uživatelsky přiřazené spravované identity ve fondech služby Batch a jak používat spravované identity v rámci uzlů.

> [!IMPORTANT]
> Podpora fondů Azure Batch s uživatelsky přiřazenými spravovanými identitami je v současnosti ve verzi Public Preview pro tyto oblasti: Západní USA 2, Střed USA – jih, Východní USA, US Gov – Arizona a US Gov – Virginie.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="create-a-user-assigned-identity"></a>Vytvoření identity přiřazené uživatelem

Nejdřív [vytvořte uživatelem přiřazenou spravovanou identitu](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity) ve stejném tenantovi jako účet Batch. Tato spravovaná identita nemusí být ve stejné skupině prostředků nebo dokonce ani ve stejném předplatném.

## <a name="create-a-batch-pool-with-user-assigned-managed-identities"></a>Vytvoření fondu Batch pomocí spravovaných identit přiřazených uživatelem

Po vytvoření jedné nebo více spravovaných identit přiřazených uživatelem můžete vytvořit fond dávek s touto spravovanou identitou pomocí [knihovny pro správu Batch .NET](/dotnet/api/overview/azure/batch#management-library).

> [!IMPORTANT]
> Aby bylo možné používat spravované identity, je nutné nakonfigurovat fondy pomocí [Konfigurace virtuálního počítače](nodes-and-pools.md#virtual-machine-configuration) .

```csharp
var poolParameters = new Pool(name: "yourPoolName")
    {
        VmSize = "standard_d1_v2",
        ScaleSettings = new ScaleSettings
        {
            FixedScale = new FixedScaleSettings
            {
                TargetDedicatedNodes = 1
            }
        },
        DeploymentConfiguration = new DeploymentConfiguration
        {
            VirtualMachineConfiguration = new VirtualMachineConfiguration(
                new ImageReference(
                    "Canonical",
                    "UbuntuServer",
                    "18.04-LTS",
                    "latest"),
                "batch.node.ubuntu 18.04")
        };
        Identity = new BatchPoolIdentity
        {
            Type = PoolIdentityType.UserAssigned,
            UserAssignedIdentities = new Dictionary<string, BatchPoolIdentityUserAssignedIdentitiesValue>
            {
                ["Your Identity Resource Id"] =
                    new BatchPoolIdentityUserAssignedIdentitiesValue()
            }
        }
    };

var pool = await managementClient.Pool.CreateWithHttpMessagesAsync(
    poolName:"yourPoolName",
    resourceGroupName: "yourResourceGroupName",
    accountName: "yourAccountName",
    parameters: poolParameters,
    cancellationToken: default(CancellationToken)).ConfigureAwait(false);    
```

> [!NOTE]
> Vytváření fondů se spravovanými identitami není v současné době podporováno pomocí [klientské knihovny Batch .NET](/dotnet/api/overview/azure/batch#client-library).

## <a name="use-user-assigned-managed-identities-in-batch-nodes"></a>Použití spravovaných identit přiřazených uživatelem v uzlech Batch

Po vytvoření fondů budou mít vaše uživatelsky přiřazené spravované identity přístup k uzlům fondu prostřednictvím Secure Shell (SSH) nebo vzdálené plochy (RDP). Můžete také nakonfigurovat úlohy tak, aby spravované identity mohly přistupovat přímo k [prostředkům Azure, které podporují spravované identity](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md).

V uzlech služby Batch můžete získat spravované tokeny identity a použít je k ověření prostřednictvím ověřování Azure AD prostřednictvím [instance metadata Service Azure](../virtual-machines/windows/instance-metadata-service.md).

Pro Windows skript PowerShellu pro získání přístupového tokenu pro ověření je:

```powershell
$Response = Invoke-RestMethod -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource={Resource App Id Url}' -Method GET -Headers @{Metadata="true"} 
```

Pro Linux je bash skript:

```bash
curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource={Resource App Id Url}' -H Metadata:true
```

Další informace najdete v tématu [použití spravovaných identit pro prostředky Azure na virtuálním počítači Azure k získání přístupového tokenu](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md).

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [spravovaných identitách pro prostředky Azure](../active-directory/managed-identities-azure-resources/overview.md).
- Naučte se používat [klíče spravované zákazníky s identitami spravovanými uživatelem](batch-customer-managed-key.md).
- Naučte se, jak [ve fondu Batch povolit automatické otočení certifikátu](automatic-certificate-rotation.md).
