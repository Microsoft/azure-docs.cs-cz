---
title: Migrace virtuálních počítačů do Správce prostředků pomocí azure cli
description: Tento článek vás provede migrací prostředků podporovanou platformou z klasického do Správce prostředků Azure pomocí azure cli
author: tanmaygore
manager: vashan
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: article
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: c41292a05e5c857cd0b1c120784a400f2f5410ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78945356"
---
# <a name="migrate-iaas-resources-from-classic-to-azure-resource-manager-by-using-azure-cli"></a>Migrace prostředků IaaS z modelu Classic na Azure Resource Manager pomocí rozhraní příkazového řádku Azure

> [!IMPORTANT]
> Dnes asi 90 % virtuálních počítačích IaaS používá [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/). února 2020, klasické virtuální počítačky byly zastaralé a budou plně vyřazeny v březnu 1, 2023. [Další informace]( https://aka.ms/classicvmretirement) o tomto vyřazení a [o tom, jak vás ovlivňuje](https://docs.microsoft.com/azure/virtual-machines/classic-vm-deprecation#how-does-this-affect-me).

Tyto kroky ukazují, jak používat příkazy rozhraní příkazového řádku Azure (CLI) k migraci prostředků infrastruktury jako služby (IaaS) z klasického modelu nasazení do modelu nasazení Azure Resource Manageru. Článek vyžaduje [klasické příkazové příkazové příkazové příkazy Azure](../../cli-install-nodejs.md). Vzhledem k tomu, že azure cli je jenom pro prostředky Azure Resource Manager, nelze použít pro tuto migraci.

> [!NOTE]
> Všechny zde popsané operace jsou idempotentní. Pokud máte jiný problém než nepodporovanou funkci nebo chybu konfigurace, doporučujeme opakovat operaci přípravy, přerušení nebo potvrzení. Platforma pak akci zkusí znovu.
> 
> 

<br>
Zde je vývojový diagram pro identifikaci pořadí, ve kterém je třeba provést kroky během procesu migrace

![Snímek obrazovky, který ukazuje kroky migrace](../windows/media/migration-classic-resource-manager/migration-flow.png)

## <a name="step-1-prepare-for-migration"></a>Krok 1: Příprava na migraci
Zde je několik doporučených postupů, které doporučujeme při vyhodnocování migrace prostředků IaaS z klasického na Správce prostředků:

* Přečtěte si [seznam nepodporovaných konfigurací nebo funkcí](../windows/migration-classic-resource-manager-overview.md). Pokud máte virtuální počítače, které používají nepodporované konfigurace nebo funkce, doporučujeme počkat na oznámení podpory funkcí nebo konfigurace. Případně můžete tuto funkci odebrat nebo se z této konfigurace přesunout, abyste povolili migraci, pokud vyhovuje vašim potřebám.
* Pokud máte automatizované skripty, které nasazují infrastrukturu a aplikace dnes, zkuste vytvořit podobné nastavení testu pomocí těchto skriptů pro migraci. Případně můžete nastavit ukázková prostředí pomocí portálu Azure.

> [!IMPORTANT]
> Aplikační brány nejsou aktuálně podporovány pro migraci z klasického správce prostředků. Chcete-li migrovat klasickou virtuální síť s aplikační bránou, odeberte bránu před spuštěním operace Příprava pro přesunutí sítě. Po dokončení migrace znovu připojte bránu ve Správci prostředků Azure. 
>
>Brány ExpressRoute, které se připojují k okruhům ExpressRoute v jiném předplatném, nelze migrovat automaticky. V takových případech odeberte bránu ExpressRoute, migrujte virtuální síť a znovu vytvořte bránu. Další informace naleznete [v tématu Migrace okruhů ExpressRoute a přidružených virtuálních sítí z klasického modelu nasazení Správce prostředků do modelu nasazení Resource Manageru.](../../expressroute/expressroute-migration-classic-resource-manager.md)
> 
> 

## <a name="step-2-set-your-subscription-and-register-the-provider"></a>Krok 2: Nastavení předplatného a registrace poskytovatele
Pro scénáře migrace je třeba nastavit prostředí pro klasické i Resource Manager. [Nainstalujte azure cli](../../cli-install-nodejs.md) a [vyberte předplatné](/cli/azure/authenticate-azure-cli).

Přihlaste se ke svému účtu.

    azure login

Vyberte předplatné Azure pomocí následujícího příkazu.

    azure account set "<azure-subscription-name>"

> [!NOTE]
> Registrace je jednorázový krok, ale je třeba provést jednou před pokusem o migraci. Bez registrace se zobrazí následující chybová zpráva 
> 
> *BadRequest : Odběr není registrován pro migraci.* 
> 
> 

Zaregistrujte se u poskytovatele prostředků migrace pomocí následujícího příkazu. Všimněte si, že v některých případech tento příkaz časový modus out. Registrace však bude úspěšná.

    azure provider register Microsoft.ClassicInfrastructureMigrate

Počkejte pět minut, než bude registrace dokončena. Stav schválení můžete zkontrolovat pomocí následujícího příkazu. Ujistěte se, `Registered` že RegistrationState je před pokračováním.

    azure provider show Microsoft.ClassicInfrastructureMigrate

Nyní přepněte CLI do režimu. `asm`

    azure config mode asm

## <a name="step-3-make-sure-you-have-enough-azure-resource-manager-virtual-machine-vcpus-in-the-azure-region-of-your-current-deployment-or-vnet"></a>Krok 3: Ujistěte se, že máte dostatek virtuálních procesorů Virtuálního počítače Azure Pro disponování prostředků v oblasti Azure aktuálního nasazení nebo virtuální sítě
Pro tento krok budete muset `arm` přepnout do režimu. Proveďte to pomocí následujícího příkazu.

```
azure config mode arm
```

Pomocí následujícího příkazu příkazu příkazu příkazu příkazu příkazu můžete zkontrolovat aktuální počet virtuálních procesorů, které máte ve Správci prostředků Azure. Další informace o kvótách virtuálních procesorů najdete v [tématu Limity a Správce prostředků Azure](../../azure-resource-manager/management/azure-subscription-service-limits.md#managing-limits).

```
azure vm list-usage -l "<Your VNET or Deployment's Azure region"
```

Po dokončení ověření tohoto kroku můžete přepnout `asm` zpět do režimu.

    azure config mode asm


## <a name="step-4-option-1---migrate-virtual-machines-in-a-cloud-service"></a>Krok 4: Možnost 1 – Migrace virtuálních počítačů v cloudové službě
Získejte seznam cloudových služeb pomocí následujícího příkazu a vyberte cloudovou službu, kterou chcete migrovat. Všimněte si, že pokud virtuální počítače v cloudové službě jsou ve virtuální síti nebo pokud mají webové/pracovní role, zobrazí se chybová zpráva.

    azure service list

Spusťte následující příkaz, abyste získali název nasazení pro cloudovou službu z podrobného výstupu. Ve většině případů je název nasazení stejný jako název cloudové služby.

    azure service show <serviceName> -vv

Nejprve ověřte, zda můžete migrovat cloudovou službu pomocí následujících příkazů:

```shell
azure service deployment validate-migration <serviceName> <deploymentName> new "" "" ""
```

Připravte virtuální počítače v cloudové službě pro migraci. Máte na výběr ze dvou možností.

Pokud chcete migrovat virtuální počítače do virtuální sítě vytvořené platformou, použijte následující příkaz.

    azure service deployment prepare-migration <serviceName> <deploymentName> new "" "" ""

Pokud chcete migrovat do existující virtuální sítě v modelu nasazení Správce prostředků, použijte následující příkaz.

    azure service deployment prepare-migration <serviceName> <deploymentName> existing <destinationVNETResourceGroupName> <subnetName> <vnetName>

Po dokončení operace přípravy můžete procházet podrobný výstup získat stav migrace virtuálních počítačích a ujistěte se, že jsou ve `Prepared` stavu.

    azure vm show <vmName> -vv

Zkontrolujte konfiguraci připravených prostředků pomocí příkazového příkazu k lisování a zabezpečení nebo portálu Azure. Pokud nejste připraveni na migraci a chcete se vrátit do starého stavu, použijte následující příkaz.

    azure service deployment abort-migration <serviceName> <deploymentName>

Pokud připravená konfigurace vypadá dobře, můžete přesunout vpřed a potvrdit prostředky pomocí následujícího příkazu.

    azure service deployment commit-migration <serviceName> <deploymentName>



## <a name="step-4-option-2----migrate-virtual-machines-in-a-virtual-network"></a>Krok 4: Možnost 2 – migrace virtuálních počítačů ve virtuální síti
Vyberte virtuální síť, kterou chcete migrovat. Všimněte si, že pokud virtuální síť obsahuje webové/pracovní role nebo virtuální počítače s nepodporovanými konfiguracemi, zobrazí se chybová zpráva ověření.

Získejte všechny virtuální sítě v předplatném pomocí následujícího příkazu.

    azure network vnet list

Výstup bude vypadat zhruba takto:

![Snímek obrazovky příkazového řádku se zvýrazněným názvem celé virtuální sítě](../media/virtual-machines-linux-cli-migration-classic-resource-manager/vnet.png)

Ve výše uvedeném příkladu je **virtualNetworkName** celý název **"Group classicubuntu16 classicubuntu16"**.

Nejprve ověřte, zda můžete migrovat virtuální síť pomocí následujícího příkazu:

```shell
azure network vnet validate-migration <virtualNetworkName>
```

Připravte virtuální síť podle vašeho výběru pro migraci pomocí následujícího příkazu.

    azure network vnet prepare-migration <virtualNetworkName>

Zkontrolujte konfiguraci připravených virtuálních počítačů pomocí příkazového příkazu k lisování a zabezpečení nebo portálu Azure. Pokud nejste připraveni na migraci a chcete se vrátit do starého stavu, použijte následující příkaz.

    azure network vnet abort-migration <virtualNetworkName>

Pokud připravená konfigurace vypadá dobře, můžete přesunout vpřed a potvrdit prostředky pomocí následujícího příkazu.

    azure network vnet commit-migration <virtualNetworkName>

## <a name="step-5-migrate-a-storage-account"></a>Krok 5: Migrace účtu úložiště
Po dokončení migrace virtuálních počítačů doporučujeme migrovat účet úložiště.

Příprava účtu úložiště pro migraci pomocí následujícího příkazu

    azure storage account prepare-migration <storageAccountName>

Zkontrolujte konfiguraci připraveného účtu úložiště pomocí příkazového příkazu k lisování a zabezpečení nebo portálu Azure. Pokud nejste připraveni na migraci a chcete se vrátit do starého stavu, použijte následující příkaz.

    azure storage account abort-migration <storageAccountName>

Pokud připravená konfigurace vypadá dobře, můžete přesunout vpřed a potvrdit prostředky pomocí následujícího příkazu.

    azure storage account commit-migration <storageAccountName>

## <a name="next-steps"></a>Další kroky

* [Přehled migrace prostředků IaaS podporovaných platformou z klasického do Správce prostředků Azure](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Podrobné technické informace o platformou podporované migraci z modelu Classic na Azure Resource Manager](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Plánování migrace prostředků IaaS z nasazení Classic do Azure Resource Manageru](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Migrace prostředků IaaS z klasického do Správce prostředků Azure pomocí PowerShellu](../windows/migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Nástroje komunity pro usnadnění migrace prostředků IaaS z klasických na Azure Resource Manager](../windows/migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Běžné chyby při migraci](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Projděte si nejčastější dotazy týkající se migrace prostředků IaaS z klasického na Azure Resource Manager.](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
