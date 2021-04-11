---
title: Migrace na Azure Cloud Services (Rozšířená podpora) pomocí PowerShellu
description: Postup migrace z Azure Cloud Services (Classic) na Azure Cloud Services (Rozšířená podpora) pomocí PowerShellu
author: tanmaygore
ms.service: cloud-services-extended-support
ms.subservice: classic-to-arm-migration
ms.reviwer: mimckitt
ms.topic: how-to
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: eea49a41e81e7e580becce815ff91aff6aa430d6
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2021
ms.locfileid: "106286873"
---
# <a name="migrate-to-azure-cloud-services-extended-support-using-powershell"></a>Migrace na Azure Cloud Services (Rozšířená podpora) pomocí PowerShellu

Tyto kroky ukazují, jak používat příkazy Azure PowerShell k migraci z [Cloud Services (Classic)](../cloud-services/cloud-services-choose-me.md) na [Cloud Services (Rozšířená podpora)](overview.md).

> [!IMPORTANT]
> Migrace z Cloud Services (Classic) na Cloud Services (Rozšířená podpora) pomocí nástroje pro migraci je aktuálně ve verzi Public Preview. Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="1-plan-for-migration"></a>1) plánování migrace
Plánování je nejdůležitějším krokem pro úspěšné migrace. Před zahájením jakýchkoli kroků migrace si Projděte [Přehled služby Cloud Services (Rozšířená podpora)](overview.md) a [Plánování migrace prostředků IaaS z modelu Classic na Azure Resource Manager](../virtual-machines/migration-classic-resource-manager-plan.md) . 

## <a name="2-install-the-latest-version-of-powershell"></a>2) nainstalujte nejnovější verzi prostředí PowerShell.
Existují dvě hlavní možnosti instalace Azure PowerShell: [Galerie prostředí PowerShell](https://www.powershellgallery.com/profiles/azure-sdk/) nebo [instalační program webové platformy (WebPI)](https://aka.ms/webpi-azps). WebPI přijímá měsíční aktualizace. Galerie prostředí PowerShell průběžně průběžně přijímá aktualizace. Tento článek je založený na Azure PowerShell verze 2.1.0.

Pokyny k instalaci najdete v tématu [instalace a konfigurace Azure PowerShell](https://docs.microsoft.com/powershell/azure/servicemanagement/install-azure-ps?view=azuresmps-4.0.0&preserve-view=true).

## <a name="3-ensure-admin-permissions"></a>3) zajistěte oprávnění správce.
K provedení této migrace musíte být přidáni jako spolusprávce předplatného v [Azure Portal](https://portal.azure.com).

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
2. V nabídce **centra** vyberte **předplatné**. Pokud ho nevidíte, vyberte **všechny služby**.
3. Vyhledejte odpovídající položku pro odběr a potom se podívejte do pole **Moje role** . Pro spolusprávce by měla být hodnota *správce účtu*.

Pokud nemůžete přidat spolusprávce, obraťte se na správce služby nebo spolupracujícího správce, aby vás přidal.

## <a name="4-register-the-classic-provider-and-cloudservice-feature"></a>4) Zaregistrujte klasického poskytovatele a funkci CloudService
Nejdřív spusťte příkazový řádek PowerShellu. Pro migraci nastavte své prostředí pro klasický i Správce prostředků.

Přihlaste se ke svému účtu pro model Správce prostředků.

```powershell
Connect-AzAccount
```

K získání dostupných předplatných použijte následující příkaz:

```powershell
Get-AzSubscription | Sort Name | Select Name
```

Nastavte si předplatné Azure pro aktuální relaci. V tomto příkladu se nastaví výchozí název předplatného na **Moje předplatné Azure**. Nahraďte název ukázkového předplatného svým vlastním.

```powershell
Select-AzSubscription –SubscriptionName "My Azure Subscription"
```

Zaregistrujte se zprostředkovatelem prostředků migrace pomocí následujícího příkazu:

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```
> [!NOTE]
> Registrace je jednorázový krok, ale musíte to udělat dřív, než se pokusíte o migraci. Bez registrace se zobrazí následující chybová zpráva:
>
> *Důvodu chybného požadavku: předplatné není zaregistrované pro migraci.*

Zaregistrujte funkci CloudServices pro vaše předplatné. Dokončení registrací může trvat několik minut. 

```powershell
Register-AzProviderFeature -FeatureName CloudServices -ProviderNamespace Microsoft.Compute
```

Počkejte pět minut, než se registrace dokončí. 

Stav schválení klasického poskytovatele ověřte pomocí následujícího příkazu:

```powershell
Get-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

Ověřte stav registrace pomocí následujících kroků:  
```powershell
Get-AzProviderFeature -FeatureName CloudServices
```

Než budete pokračovat, ujistěte se, že je RegistrationState `Registered` pro obě.

Než přepnete do modelu nasazení Classic, ujistěte se, že máte dostatek Azure Resource Manager kvóty vCPU v oblasti Azure vašeho aktuálního nasazení nebo virtuální sítě. K zkontrolování aktuálního počtu vCPU, která máte v Azure Resource Manager, můžete použít následující příkaz prostředí PowerShell. Další informace o kvótách vCPU najdete v tématu [omezení a Azure Resource Manager](../azure-resource-manager/management/azure-subscription-service-limits.md#managing-limits).

Tento příklad zkontroluje dostupnost v oblasti **západní USA** . Nahraďte název ukázkové oblasti vlastním.

```powershell
Get-AzVMUsage -Location "West US"
```

Teď se přihlaste ke svému účtu pro model nasazení Classic.

```powershell
Add-AzureAccount
```

K získání dostupných předplatných použijte následující příkaz:

```powershell
Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName
```

Nastavte si předplatné Azure pro aktuální relaci. V tomto příkladu se nastaví výchozí předplatné na **Moje předplatné Azure**. Nahraďte název ukázkového předplatného svým vlastním.

```powershell
Select-AzureSubscription –SubscriptionName "My Azure Subscription"
```


## <a name="5-migrate-your-cloud-services"></a>5) migrace Cloud Services 
* [Migrace cloudové služby, která není ve virtuální síti](#51-option-1---migrate-a-cloud-service-not-in-a-virtual-network)
* [Migrace cloudové služby ve virtuální síti](#51-option-2---migrate-a-cloud-service-in-a-virtual-network)

> [!NOTE]
> Všechny popsané operace jsou idempotentní. Pokud máte jiný problém než Nepodporovaná funkce nebo Chyba konfigurace, Doporučujeme zopakovat operaci příprava, přerušení nebo potvrzení. Platforma se pak pokusí akci zopakovat.


### <a name="51-option-1---migrate-a-cloud-service-not-in-a-virtual-network"></a>5,1) možnost 1 – migrace cloudové služby mimo virtuální síť
Seznam služeb Cloud Services získáte pomocí následujícího příkazu. Pak vyberte cloudovou službu, kterou chcete migrovat.

```powershell
Get-AzureService | ft Servicename
```

Získá název nasazení pro cloudovou službu. V tomto příkladu je název služby **Moje služba**. Nahraďte ukázkový název služby vlastním názvem služby.

```powershell
$serviceName = "My Service"
$deployment = Get-AzureDeployment -ServiceName $serviceName
$deploymentName = $deployment.DeploymentName
```

Nejdřív ověřte, že můžete migrovat cloudovou službu pomocí následujících příkazů:

```powershell
$validate = Move-AzureService -Validate -ServiceName $serviceName -DeploymentName $deploymentName -CreateNewVirtualNetwork
$validate.ValidationMessages
 ```

Následující příkaz zobrazí všechna upozornění a chyby, které blokují migraci. Pokud je ověření úspěšné, můžete přejít na krok příprava.

```powershell
Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName -CreateNewVirtualNetwork
```

### <a name="51-option-2---migrate-a-cloud-service-in-a-virtual-network"></a>5,1) možnost 2 – migrace cloudové služby ve virtuální síti

K migraci cloudové služby ve virtuální síti migrujete virtuální síť. Cloudová služba se automaticky migruje s virtuální sítí.

> [!NOTE]
> Název virtuální sítě se může lišit od toho, co se zobrazuje na novém portálu. Nový Azure Portal zobrazí název jako `[vnet-name]` , ale skutečný název virtuální sítě je typu `Group [resource-group-name] [vnet-name]` . Než začnete s migrací, vyhledejte skutečný název virtuální sítě pomocí příkazu `Get-AzureVnetSite | Select -Property Name` nebo ho Zobrazte ve starém Azure Portal. 

V tomto příkladu se nastaví název virtuální sítě na **myVnet**. Nahraďte ukázkový název virtuální sítě vlastním.

```powershell
$vnetName = "myVnet"
```

Nejdřív ověřte, že můžete migrovat virtuální síť pomocí následujícího příkazu:

```powershell
Move-AzureVirtualNetwork -Validate -VirtualNetworkName $vnetName
```

Následující příkaz zobrazí všechna upozornění a chyby, které blokují migraci. Pokud je ověření úspěšné, můžete pokračovat následujícím krokem přípravy:

```powershell
Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName
```

Pomocí Azure PowerShell nebo Azure Portal Ověřte konfiguraci připravené cloudové služby. Pokud nejste připraveni na migraci a chcete se vrátit k původnímu stavu, použijte následující příkaz:

```powershell
Move-AzureVirtualNetwork -Abort -VirtualNetworkName $vnetName
```

Pokud je připravená konfigurace dobrá, můžete přesunout prostředky vpřed a potvrdit je pomocí následujícího příkazu:

```powershell
Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName
```


## <a name="next-steps"></a>Další kroky
V části [po změnách migrace](in-place-migration-overview.md#post-migration-changes) si můžete prohlédnout změny v souborech nasazení, automatizaci a dalších atributech nového nasazení Cloud Services (rozšířené podpory). 
