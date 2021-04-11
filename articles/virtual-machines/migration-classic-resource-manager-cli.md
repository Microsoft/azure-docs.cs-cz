---
title: Migrace virtuálních počítačů do Správce prostředků pomocí Azure CLI
description: Tento článek vás provede migrací prostředků z klasických do Azure Resource Manager pomocí rozhraní příkazového řádku Azure na platformě.
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.subservice: classic-to-arm-migration
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 02/06/2020
ms.author: tagore
ms.custom: devx-track-azurecli
ms.openlocfilehash: 671b27f927c91397d2aacd98cb7b500d8197d1c5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101669333"
---
# <a name="migrate-iaas-resources-from-classic-to-azure-resource-manager-by-using-azure-cli"></a>Migrace prostředků IaaS z modelu Classic na Azure Resource Manager pomocí rozhraní příkazového řádku Azure

> [!IMPORTANT]
> V dnešní době se o 90% virtuálních počítačů IaaS používají [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/). Od 28. února 2020 se klasické virtuální počítače zastaraly a budou plně vyřazeny od 1. března 2023. [Přečtěte si další informace]( https://aka.ms/classicvmretirement) o této zastaralosti a [o tom, jak vás to ovlivní](classic-vm-deprecation.md#how-does-this-affect-me).

Tyto kroky ukazují, jak používat příkazy rozhraní příkazového řádku (CLI) Azure k migraci prostředků infrastruktury jako služby (IaaS) z modelu nasazení Classic do modelu nasazení Azure Resource Manager. Článek vyžaduje rozhraní příkazového [řádku Azure Classic](/cli/azure/install-classic-cli). Vzhledem k tomu, že rozhraní příkazového řádku Azure se dá použít jenom pro Azure Resource Manager prostředky, nedá se pro tuto migraci použít.

> [!NOTE]
> Všechny popsané operace jsou idempotentní. Pokud máte jiný problém než Nepodporovaná funkce nebo Chyba konfigurace, Doporučujeme zopakovat operaci příprava, přerušení nebo potvrzení. Platforma pak tuto akci zopakuje.
> 
> 

<br>
Tady je vývojový diagram pro identifikaci pořadí, ve kterém je potřeba provést kroky během procesu migrace.

![Snímek obrazovky, který ukazuje kroky migrace](./media/migration-classic-resource-manager/migration-flow.png)

## <a name="step-1-prepare-for-migration"></a>Krok 1: Příprava na migraci
Tady je několik doporučených postupů, které doporučujeme při vyhodnocování migrace prostředků IaaS z modelu Classic na Správce prostředků:

* Přečtěte si [seznam nepodporovaných konfigurací nebo funkcí](migration-classic-resource-manager-overview.md). Pokud máte virtuální počítače, které používají nepodporované konfigurace nebo funkce, doporučujeme, abyste počkali, až bude podpora funkcí a konfigurace oznámena. Alternativně můžete tuto funkci odebrat nebo ji můžete z této konfigurace odebrat, abyste mohli migraci povolit, pokud vyhovuje vašim potřebám.
* Pokud máte automatizované skripty, které nasazují svou infrastrukturu a aplikace dnes, zkuste vytvořit podobné nastavení testu pomocí těchto skriptů pro migraci. Alternativně můžete nastavit ukázková prostředí pomocí Azure Portal.

> [!IMPORTANT]
> Aplikační brány se v současné době nepodporují pro migraci z modelu Classic na Správce prostředků. Chcete-li migrovat klasickou virtuální síť s aplikační bránou, odeberte bránu před spuštěním operace přípravy k přesunutí sítě. Po dokončení migrace znovu připojte bránu v Azure Resource Manager. 
>
>Brány ExpressRoute, které se připojují k okruhům ExpressRoute v jiném předplatném, se nedají migrovat automaticky. V takových případech odeberte bránu ExpressRoute, migrujte virtuální síť a znovu vytvořte bránu. Další informace najdete v tématu [migrace okruhů ExpressRoute a přidružených virtuálních sítí z modelu nasazení Classic do nasazení Správce prostředků](../expressroute/expressroute-migration-classic-resource-manager.md) .
> 
> 

## <a name="step-2-set-your-subscription-and-register-the-provider"></a>Krok 2: nastavte předplatné a zaregistrujte poskytovatele.
Pro scénáře migrace je potřeba nastavit prostředí pro klasický i Správce prostředků. Nainstalujte rozhraní příkazového [řádku Azure](/cli/azure/install-classic-cli) a [Vyberte své předplatné](/cli/azure/authenticate-azure-cli).

Přihlaste se ke svému účtu.

```azurecli
azure login
```

Vyberte předplatné Azure pomocí následujícího příkazu.

```azurecli
azure account set "<azure-subscription-name>"
```

> [!NOTE]
> Registrace je jednorázový krok, ale před pokusem o migraci je potřeba ji udělat znovu. Bez registrace se zobrazí následující chybová zpráva. 
> 
> *Důvodu chybného požadavku: předplatné není zaregistrované pro migraci.* 
> 
> 

Zaregistrujte se zprostředkovatelem prostředků migrace pomocí následujícího příkazu. Všimněte si, že v některých případech vypršel časový limit tohoto příkazu. Registrace bude ale úspěšná.

```azurecli
azure provider register Microsoft.ClassicInfrastructureMigrate
```

Počkejte prosím pět minut, než se registrace dokončí. Stav schválení můžete zjistit pomocí následujícího příkazu. Před pokračováním se ujistěte, že je RegistrationState `Registered` .

```azurecli
azure provider show Microsoft.ClassicInfrastructureMigrate
```

Teď přepněte CLI do `asm` režimu.

```azurecli
azure config mode asm
```

## <a name="step-3-make-sure-you-have-enough-azure-resource-manager-virtual-machine-vcpus-in-the-azure-region-of-your-current-deployment-or-vnet"></a>Krok 3: Ujistěte se, že máte dostatek Azure Resource Manager vCPU virtuálních počítačů v oblasti Azure vašeho aktuálního nasazení nebo virtuální sítě.
V tomto kroku budete muset přepnout do `arm` režimu. Udělejte to pomocí následujícího příkazu.

```azurecli
azure config mode arm
```

Pomocí následujícího příkazu rozhraní příkazového řádku můžete kontrolovat aktuální počet vCPU, které máte v Azure Resource Manager. Další informace o kvótách vCPU najdete v tématu [omezení a Azure Resource Manager](../azure-resource-manager/management/azure-subscription-service-limits.md#managing-limits).

```azurecli
azure vm list-usage -l "<Your VNET or Deployment's Azure region"
```

Po ověření tohoto kroku můžete přepnout zpátky do `asm` režimu.

```azurecli
azure config mode asm
```

## <a name="step-4-option-1---migrate-virtual-machines-in-a-cloud-service"></a>Krok 4: možnost 1 – migrace virtuálních počítačů v cloudové službě
Seznam cloudových služeb získáte pomocí následujícího příkazu a pak vyberte cloudovou službu, kterou chcete migrovat. Všimněte si, že pokud jsou virtuální počítače v cloudové službě ve virtuální síti, nebo pokud mají webové a pracovní role, zobrazí se chybová zpráva.

```azurecli
azure service list
```

Spusťte následující příkaz, který získá název nasazení pro cloudovou službu z podrobného výstupu. Ve většině případů je název nasazení stejný jako název cloudové služby.

```azurecli
azure service show <serviceName> -vv
```

Nejdřív ověřte, jestli můžete migrovat cloudovou službu pomocí následujících příkazů:

```shell
azure service deployment validate-migration <serviceName> <deploymentName> new "" "" ""
```

Připravte virtuální počítače v cloudové službě pro migraci. Máte dvě možnosti, ze kterých si můžete vybrat.

Pokud chcete migrovat virtuální počítače na virtuální síť vytvořenou platformou, použijte následující příkaz.

```azurecli
azure service deployment prepare-migration <serviceName> <deploymentName> new "" "" ""
```

Pokud chcete migrovat do existující virtuální sítě v modelu nasazení Správce prostředků, použijte následující příkaz.

```azurecli
azure service deployment prepare-migration <serviceName> <deploymentName> existing <destinationVNETResourceGroupName> <subnetName> <vnetName>
```

Po úspěšném dokončení operace přípravy můžete projít podrobný výstup a získat tak stav migrace virtuálních počítačů a ujistit se, že jsou ve `Prepared` stavu.

```azurecli
azure vm show <vmName> -vv
```

Ověřte konfiguraci pro připravené prostředky pomocí rozhraní příkazového řádku nebo Azure Portal. Pokud nejste připraveni na migraci a chcete se vrátit k původnímu stavu, použijte následující příkaz.

```azurecli
azure service deployment abort-migration <serviceName> <deploymentName>
```

Pokud je připravená konfigurace dobrá, můžete přesunout prostředky vpřed a potvrdit je pomocí následujícího příkazu.

```azurecli
azure service deployment commit-migration <serviceName> <deploymentName>
```

## <a name="step-4-option-2----migrate-virtual-machines-in-a-virtual-network"></a>Krok 4: možnost 2 – migrace virtuálních počítačů ve virtuální síti
Vyberte virtuální síť, kterou chcete migrovat. Všimněte si, že pokud virtuální síť obsahuje webové a pracovní role nebo virtuální počítače s nepodporovanými konfiguracemi, zobrazí se chybová zpráva ověření.

Všechny virtuální sítě v rámci předplatného získáte pomocí následujícího příkazu.

```azurecli
azure network vnet list
```

Výstup bude vypadat zhruba takto:

![Snímek obrazovky příkazového řádku s názvem celé virtuální sítě se zvýrazní.](./media/virtual-machines-linux-cli-migration-classic-resource-manager/vnet.png)

V předchozím příkladu je **virtualNetworkName** celý název **"Group classicubuntu16 classicubuntu16"**.

Nejdřív ověřte, jestli můžete migrovat virtuální síť pomocí následujícího příkazu:

```shell
azure network vnet validate-migration <virtualNetworkName>
```

Připravte virtuální síť podle vašeho výběru pro migraci pomocí následujícího příkazu.

```azurecli
azure network vnet prepare-migration <virtualNetworkName>
```

Ověřte konfiguraci připravených virtuálních počítačů pomocí rozhraní příkazového řádku nebo Azure Portal. Pokud nejste připraveni na migraci a chcete se vrátit k původnímu stavu, použijte následující příkaz.

```azurecli
azure network vnet abort-migration <virtualNetworkName>
```

Pokud je připravená konfigurace dobrá, můžete přesunout prostředky vpřed a potvrdit je pomocí následujícího příkazu.

```azurecli
azure network vnet commit-migration <virtualNetworkName>
```

## <a name="step-5-migrate-a-storage-account"></a>Krok 5: Migrace účtu úložiště
Po dokončení migrace virtuálních počítačů doporučujeme migrovat účet úložiště.

Připravte účet úložiště pro migraci pomocí následujícího příkazu.

```azurecli
azure storage account prepare-migration <storageAccountName>
```

Ověřte konfiguraci připraveného účtu úložiště pomocí rozhraní příkazového řádku nebo Azure Portal. Pokud nejste připraveni na migraci a chcete se vrátit k původnímu stavu, použijte následující příkaz.

```azurecli
azure storage account abort-migration <storageAccountName>
```

Pokud je připravená konfigurace dobrá, můžete přesunout prostředky vpřed a potvrdit je pomocí následujícího příkazu.

```azurecli
azure storage account commit-migration <storageAccountName>
```

## <a name="next-steps"></a>Další kroky

* [Přehled migrace prostředků IaaS podporovaných platformou z klasických na Azure Resource Manager](migration-classic-resource-manager-overview.md)
* [Podrobné technické informace o platformou podporované migraci z modelu Classic na Azure Resource Manager](migration-classic-resource-manager-deep-dive.md)
* [Plánování migrace prostředků IaaS z nasazení Classic do Azure Resource Manageru](migration-classic-resource-manager-plan.md)
* [Použití PowerShellu k migraci prostředků IaaS z modelu Classic na Azure Resource Manager](migration-classic-resource-manager-ps.md)
* [Komunitní nástroje pro pomoc s migrací prostředků IaaS z modelu Classic na Azure Resource Manager](migration-classic-resource-manager-community-tools.md)
* [Běžné chyby při migraci](migration-classic-resource-manager-errors.md)
* [Přečtěte si nejčastější dotazy týkající se migrace prostředků IaaS z modelu Classic na Azure Resource Manager](migration-classic-resource-manager-faq.md)
