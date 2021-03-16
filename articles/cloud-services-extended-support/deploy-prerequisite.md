---
title: Předpoklady pro nasazení služby Azure Cloud Services (Rozšířená podpora)
description: Předpoklady pro nasazení služby Azure Cloud Services (Rozšířená podpora)
ms.topic: article
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 79d6fecddf060909a74664ff29e08301f45d7042
ms.sourcegitcommit: 66ce33826d77416dc2e4ba5447eeb387705a6ae5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/15/2021
ms.locfileid: "103472304"
---
# <a name="prerequisites-for-deploying-azure-cloud-services-extended-support"></a>Předpoklady pro nasazení služby Azure Cloud Services (Rozšířená podpora)

> [!IMPORTANT]
> Cloud Services (Rozšířená podpora) je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Pokud chcete zajistit úspěšnou Cloud Services nasazení (rozšířené podpory), Projděte si níže uvedené kroky a dokončete každou položku před pokusem o nasazení. 

## <a name="register-the-cloudservices-feature"></a>Registrace funkce CloudServices
Zaregistrujte funkci pro vaše předplatné. Dokončení registrace může trvat několik minut. 

```powershell
Register-AzProviderFeature -FeatureName CloudServices -ProviderNamespace Microsoft.Compute
```

Ověřte stav registrace pomocí následujících kroků:  
```powershell
Get-AzProviderFeature 

#Sample output
FeatureName               ProviderName      RegistrationState
CloudServices           Microsoft.Compute    Registered
```

## <a name="required-service-configuration-cscfg-file-updates"></a>Požadované aktualizace konfiguračního souboru služby (. cscfg)

### <a name="1-virtual-network"></a>1) Virtual Network
Nasazení cloudové služby (Rozšířená podpora) se musí nacházet ve virtuální síti. Virtuální síť se dá vytvořit prostřednictvím [Azure Portal](../virtual-network/quick-create-portal.md), [PowerShellu](../virtual-network/quick-create-powershell.md), [Azure CLI](../virtual-network/quick-create-cli.md) nebo [šablony ARM](../virtual-network/quick-create-template.md). V konfiguraci služby (. cscfg) se musí v části [NetworkConfiguration](schema-cscfg-networkconfiguration.md) odkazovat taky na virtuální síť a podsítě. 

Pro virtuální sítě, které patří do stejné skupiny prostředků jako cloudová služba, je pro vás stačit odkaz jenom na název virtuální sítě v souboru konfigurace služby (. cscfg). Pokud jsou virtuální síť a cloudová služba ve dvou různých skupinách prostředků, musí být v souboru konfigurace služby (. cscfg) zadaná úplná Azure Resource Manager ID virtuální sítě.
 
#### <a name="virtual-network-located-in-same-resource-group"></a>Virtual Network nacházející se ve stejné skupině prostředků
```xml
<VirtualNetworkSite name="<vnet-name>"/> 
  <AddressAssignments> 
    <InstanceAddress roleName="<role-name>"> 
     <Subnets> 
       <Subnet name="<subnet-name>"/> 
     </Subnets> 
    </InstanceAddress> 
```

#### <a name="virtual-network-located-in-different-resource-group"></a>Virtuální síť umístěná v jiné skupině prostředků
```xml
<VirtualNetworkSite name="/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.Network/virtualNetworks/<vnet-name>"/> 
   <AddressAssignments> 
     <InstanceAddress roleName="<role-name>"> 
       <Subnets> 
        <Subnet name="<subnet-name>"/> 
       </Subnets> 
     </InstanceAddress> 
```
### <a name="2-remove-the-old-plugins"></a>2) odeberte staré moduly plug-in.

Odeberte stará nastavení vzdálené plochy ze souboru konfigurace služby (. cscfg).  

```xml
<Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.Enabled" value="true" /> 
<Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountUsername" value="gachandw" /> 
<Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountEncryptedPassword" value="XXXX" /> 
<Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountExpiration" value="2021-12-17T23:59:59.0000000+05:30" /> 
<Setting name="Microsoft.WindowsAzure.Plugins.RemoteForwarder.Enabled" value="true" /> 
```
Odeberte stará nastavení diagnostiky pro každou roli v souboru konfigurace služby (. cscfg).

```xml
<Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
```

## <a name="required-service-definition-file-csdef-updates"></a>Požadované aktualizace definičního souboru služby (. csdef)

### <a name="1-virtual-machine-sizes"></a>1) velikosti virtuálních počítačů
Následující velikosti jsou v Azure Resource Manager zastaralé. Pokud ale chcete i nadále používat, aktualizujte `vmsize` název s přidruženou konvencí pojmenování Azure Resource Manager.  

| Název předchozí velikosti | Název aktualizované velikosti | 
|---|---|
| ExtraSmall | Standard_A0 | 
| Malá | Standard_A1 |
| Střední | Standard_A2 | 
| Velká | Standard_A3 | 
| ExtraLarge | Standard_A4 | 
| A5 | Standard_A5 | 
| A6 | Standard_A6 | 
| Jednou | Standard_A7 |  
| A8 | Standard_A8 | 
| A9 | Standard_A9 |
| A10 | Standard_A10 | 
| A11 | Standard_A11 | 
| MSODSG5 | Standard_MSODSG5 | 

 Například `<WorkerRole name="WorkerRole1" vmsize="Medium"` by se stala `<WorkerRole name="WorkerRole1" vmsize="Standard_A2"` .
 
> [!NOTE]
> Seznam dostupných velikostí najdete v článku [SKU prostředků – seznam](/rest/api/compute/resourceskus/list) a použití následujících filtrů: <br>
`ResourceType = virtualMachines ` <br>
`VMDeploymentTypes = PaaS `


### <a name="2-remove-old-remote-desktop-plugins"></a>2) odebrání starých modulů plug-in pro vzdálenou plochu
Nasazení, která využila staré funkce plug-in vzdálené plochy, musí mít odebrané moduly ze souboru definice služby (. csdef) a všech přidružených certifikátů. 

```xml
<Imports> 
<Import moduleName="RemoteAccess" /> 
<Import moduleName="RemoteForwarder" /> 
</Imports> 
```
Nasazení, která využila staré moduly plug-in, potřebují odebrat nastavení pro každou roli ze souboru definice služby (. csdef).

```xml
<Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
```

## <a name="key-vault-creation"></a>Vytváření Key Vault 

Key Vault slouží k ukládání certifikátů, které jsou přidruženy k Cloud Services (Rozšířená podpora). Přidejte certifikáty do Key Vault a pak na ně nastavte odkazy na kryptografické otisky certifikátů v konfiguračním souboru služby. Také je nutné povolit Key Vault pro příslušná oprávnění, aby prostředek Cloud Services (Rozšířená podpora) mohl získat certifikát uložený jako tajné klíče z Key Vault. Trezor klíčů můžete vytvořit v [Azure Portal](../key-vault/general/quick-create-portal.md) nebo pomocí [prostředí PowerShell](../key-vault/general/quick-create-powershell.md). Trezor klíčů se musí vytvořit ve stejné oblasti a předplatném jako cloudová služba. Další informace najdete v tématu [použití certifikátů s Azure Cloud Services (Rozšířená podpora)](certificates-and-key-vault.md).

## <a name="next-steps"></a>Další kroky 
- Přečtěte si [požadavky na nasazení](deploy-prerequisite.md) pro Cloud Services (Rozšířená podpora).
- Nasaďte cloudovou službu (rozšířenou podporu) pomocí [Azure Portal](deploy-portal.md), [PowerShellu](deploy-powershell.md), [šablony](deploy-template.md) nebo sady [Visual Studio](deploy-visual-studio.md).
- Přečtěte si [Nejčastější dotazy](faq.md) k Cloud Services (Rozšířená podpora).
- Navštivte [úložiště ukázek Cloud Services (rozšířené podpory)](https://github.com/Azure-Samples/cloud-services-extended-support) .
