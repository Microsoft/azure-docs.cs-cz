---
title: Migrovat virtuální počítače do Resource Manager pomocí rozhraní příkazového řádku Azure | Microsoft Docs
description: Tento článek vás provede migraci podporované platformy prostředků z klasického do Azure Resource Manageru pomocí rozhraní příkazového řádku Azure
services: virtual-machines-linux
documentationcenter: ''
author: singhkays
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: d6f5a877-05b6-4127-a545-3f5bede4e479
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/30/2017
ms.author: kasing
ms.openlocfilehash: f986246e74305789eb2978a95fd6a3e51accd25e
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="migrate-iaas-resources-from-classic-to-azure-resource-manager-by-using-azure-cli"></a>Migrovat prostředky infrastruktury z klasického do Azure Resource Manageru pomocí rozhraní příkazového řádku Azure
Tyto kroky ukazují, jak používat příkazy rozhraní příkazového řádku Azure (CLI) k migraci infrastruktury jako služby (IaaS) prostředky z modelu nasazení classic do modelu nasazení Azure Resource Manager. Článek vyžaduje [Azure CLI 1.0](../../cli-install-nodejs.md). Vzhledem k tomu, že Azure CLI 2.0 je k dispozici jenom pro prostředky Azure Resource Manager, nelze použít pro migraci.

> [!NOTE]
> Všechny operace, které jsou zde popsané jsou idempotent. Pokud došlo k potížím. než nepodporované funkce nebo chyby v konfiguraci, doporučujeme zopakovat Příprava, zrušení nebo potvrzení operace. Platforma se pak zkuste akci znovu.
> 
> 

<br>
Tady je Vývojový diagram k identifikaci pořadí, ve kterém kroky se musí provést během procesu migrace

![Snímek obrazovky, který ukazuje kroky migrace](../windows/media/migration-classic-resource-manager/migration-flow.png)

## <a name="step-1-prepare-for-migration"></a>Krok 1: Příprava na migraci
Tady je několik osvědčených postupů, které doporučujeme jak vyhodnotit migrace IaaS prostředky z classic do Resource Manager:

* Pročtěte [seznam nepodporovaných konfigurací nebo funkce](../windows/migration-classic-resource-manager-overview.md). Pokud máte virtuální počítače, které používají nepodporované konfigurace nebo funkce, doporučujeme počkejte funkce nebo konfigurační podpory, která má být oznámeno. Alternativně můžete odebrat tuto funkci nebo přesunout mimo tuto konfiguraci, chcete-li migrovat, pokud ji vyhovuje vašim potřebám.
* Pokud máte automatizované skripty, které jsou dnes nasazení infrastruktury a aplikace, pokuste se vytvořit podobné nastavení testu pomocí těchto skriptů pro migraci. Alternativně můžete nastavit ukázkové prostředí pomocí portálu Azure.

> [!IMPORTANT]
> Application Gateway nejsou aktuálně podporovány pro migraci z classic do Resource Manager. Pokud chcete migrovat klasickou virtuální síť s aplikační brány, odstranění brány před spuštěním operace Příprava přesunout sítě. Po dokončení migrace znovu připojte bránu ve službě Správce prostředků Azure. 
>
>Připojování k okruhy ExpressRoute v jiné předplatné brány ExpressRoute se nedají automaticky migrovat. V takových případech odebrat bránu ExpressRoute, migrujte virtuální sítě a znovu vytvořit bránu. Najdete v tématu [okruhy ExpressRoute migrovat a přidružené virtuální sítě z klasického modelu nasazení Resource Manager](../../expressroute/expressroute-migration-classic-resource-manager.md) Další informace.
> 
> 

## <a name="step-2-set-your-subscription-and-register-the-provider"></a>Krok 2: Nastavte předplatné a zaregistrujte zprostředkovatele
Pro scénáře migrace, budete muset nastavit svoje prostředí pro obě classic a Resource Manager. [Instalace rozhraní příkazového řádku Azure](../../cli-install-nodejs.md) a [vyberte své předplatné](/cli/azure/authenticate-azure-cli).

Přihlášení ke svému účtu.

    azure login

Pomocí následujícího příkazu vyberte předplatné Azure.

    azure account set "<azure-subscription-name>"

> [!NOTE]
> Registrace je čas krok, ale je nutné provést jednou před pokusem o migraci. Bez registrace se zobrazí následující chybová zpráva 
> 
> *Struktura BadRequest: Předplatné není zaregistrované pro migraci.* 
> 
> 

Zaregistrovat u zprostředkovatele prostředků migrace pomocí následujícího příkazu. Všimněte si, že v některých případech tento příkaz časového limitu. Registrace však bude úspěšné.

    azure provider register Microsoft.ClassicInfrastructureMigrate

Počkejte 5 minut pro registraci dokončit. Pomocí následujícího příkazu můžete zkontrolovat stav schválení. Ujistěte se, že je RegistrationState `Registered` než budete pokračovat.

    azure provider show Microsoft.ClassicInfrastructureMigrate

Nyní přepínat rozhraní příkazového řádku pro `asm` režimu.

    azure config mode asm

## <a name="step-3-make-sure-you-have-enough-azure-resource-manager-virtual-machine-vcpus-in-the-azure-region-of-your-current-deployment-or-vnet"></a>Krok 3: Zkontrolujte, zda že máte dostatek virtuálního počítače Azure Resource Manager Vcpu v oblasti Azure vaše aktuální nasazení nebo virtuální sítě
V tomto kroku budete potřebovat přepnout do `arm` režimu. To lze proveďte pomocí následujícího příkazu.

```
azure config mode arm
```

Následující příkaz rozhraní příkazového řádku můžete zkontrolovat aktuální počet Vcpu, které máte ve službě Správce prostředků Azure. Další informace o virtuálních procesorů kvóty, najdete v části [omezení a Azure Resource Manager](../../azure-subscription-service-limits.md#limits-and-the-azure-resource-manager)

```
azure vm list-usage -l "<Your VNET or Deployment's Azure region"
```

Po dokončení ověření tento krok, můžete přepnout zpět na `asm` režimu.

    azure config mode asm


## <a name="step-4-option-1---migrate-virtual-machines-in-a-cloud-service"></a>Krok 4: Možnost 1 - migraci virtuálních počítačů v rámci cloudové služby
Získání seznamu cloudových služeb pomocí následujícího příkazu a pak vyberte cloudovou službu, která chcete migrovat. Pamatujte, že pokud jsou virtuální počítače v rámci cloudové služby ve virtuální síti nebo pokud budou mít web/role pracovního procesu, zobrazí se chybová zpráva.

    azure service list

Spusťte následující příkaz pro získání názvu nasazení pro cloudovou službu z podrobný výstup. Ve většině případů název nasazení je stejný jako název cloudové služby.

    azure service show <serviceName> -vv

Nejprve ověřte, jestli je možné migrovat cloudové služby, použijte následující příkazy:

```shell
azure service deployment validate-migration <serviceName> <deploymentName> new "" "" ""
```

Vypněte virtuální počítače v rámci cloudové služby pro migraci. Máte dvě možnosti, které lze vybírat.

Pokud chcete migrovat virtuální počítače k virtuální síti vytvořené platformy, použijte následující příkaz.

    azure service deployment prepare-migration <serviceName> <deploymentName> new "" "" ""

Pokud chcete migrovat na existující virtuální síť v modelu nasazení Resource Manager, použijte následující příkaz.

    azure service deployment prepare-migration <serviceName> <deploymentName> existing <destinationVNETResourceGroupName> <subnetName> <vnetName>

Po úspěšné operace Příprava můžete zobrazit prostřednictvím podrobný výstup, pokud chcete získat stav migrace virtuálních počítačů a ujistěte se, že jsou v `Prepared` stavu.

    azure vm show <vmName> -vv

Zkontrolujte konfiguraci pro připravené prostředky pomocí rozhraní příkazového řádku nebo portálu Azure. Pokud si nejste připravený pro migraci a chcete přejít zpět do původního stavu, použijte následující příkaz.

    azure service deployment abort-migration <serviceName> <deploymentName>

Pokud připravené konfigurací spokojeni, můžete přejít a potvrdit prostředky pomocí následujícího příkazu.

    azure service deployment commit-migration <serviceName> <deploymentName>



## <a name="step-4-option-2----migrate-virtual-machines-in-a-virtual-network"></a>Krok 4: Možnost 2 - migrovat virtuální počítače ve virtuální síti
Vyberte virtuální síť, která chcete migrovat. Všimněte si, že pokud virtuální síť obsahuje webové/role pracovního procesu nebo virtuální počítače s nepodporované konfigurace, obdržíte chybovou zprávu ověření.

Pomocí následujícího příkazu získáte všechny virtuální sítě v rámci předplatného.

    azure network vnet list

Výstup bude vypadat přibližně takto:

![Snímek obrazovky s celý virtuální síť s názvem zvýrazněná příkazového řádku.](../media/virtual-machines-linux-cli-migration-classic-resource-manager/vnet.png)

V předchozím příkladu **virtualNetworkName** je celý název **"Classicubuntu16 classicubuntu16 skupiny"**.

Nejprve ověřte, jestli je možné migrovat virtuální sítě pomocí následujícího příkazu:

```shell
azure network vnet validate-migration <virtualNetworkName>
```

Pomocí následujícího příkazu připravte virtuální síť podle svého výběru pro migraci.

    azure network vnet prepare-migration <virtualNetworkName>

Zkontrolujte konfiguraci pro virtuální počítače, který připravené pomocí rozhraní příkazového řádku nebo portálu Azure. Pokud si nejste připravený pro migraci a chcete přejít zpět do původního stavu, použijte následující příkaz.

    azure network vnet abort-migration <virtualNetworkName>

Pokud připravené konfigurací spokojeni, můžete přejít a potvrdit prostředky pomocí následujícího příkazu.

    azure network vnet commit-migration <virtualNetworkName>

## <a name="step-5-migrate-a-storage-account"></a>Krok 5: Migrace účet úložiště
Po dokončení migrace virtuálních počítačů, doporučujeme, migraci účtu úložiště.

Pomocí následujícího příkazu připravte účet úložiště pro migraci

    azure storage account prepare-migration <storageAccountName>

Zkontrolujte konfiguraci pro účet úložiště připravené pomocí rozhraní příkazového řádku nebo portálu Azure. Pokud si nejste připravený pro migraci a chcete přejít zpět do původního stavu, použijte následující příkaz.

    azure storage account abort-migration <storageAccountName>

Pokud připravené konfigurací spokojeni, můžete přejít a potvrdit prostředky pomocí následujícího příkazu.

    azure storage account commit-migration <storageAccountName>

## <a name="next-steps"></a>Další postup

* [Přehled platformy podporované migrace z klasického do Azure Resource Manageru prostředků IaaS](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Technické podrobné informace o platformy podporované migrace z klasického do Azure Resource Manageru](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Plánování migrace prostředků IaaS z nasazení Classic do Azure Resource Manageru](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Migrace prostředků IaaS z klasického do Azure Resource Manageru pomocí prostředí PowerShell](../windows/migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Komunita nástroje asistence s migrace z klasického do Azure Resource Manageru prostředků IaaS](../windows/migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Běžné chyby při migraci](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Přečtěte si nejčastější dotazy o migraci prostředky infrastruktury jako služby z klasického do Azure Resource Manageru](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
