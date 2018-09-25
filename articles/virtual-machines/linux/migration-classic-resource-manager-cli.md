---
title: Migrace virtuálních počítačů k Resource Manageru pomocí rozhraní příkazového řádku Azure | Dokumentace Microsoftu
description: Tento článek vás provede platformou podporované migraci prostředků z modelu nasazení classic do Azure Resource Manageru pomocí rozhraní příkazového řádku Azure
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
ms.openlocfilehash: 0011ee646215c01e84aec71c7b992afca1ca3c2a
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46997161"
---
# <a name="migrate-iaas-resources-from-classic-to-azure-resource-manager-by-using-azure-cli"></a>Migrovat prostředky IaaS z modelu nasazení classic do Azure Resource Manageru pomocí rozhraní příkazového řádku Azure
Tyto kroky ukazují, jak migrovat infrastrukturu jako službu (IaaS) prostředky z modelu nasazení classic do modelu nasazení Azure Resource Manageru pomocí příkazů rozhraní příkazového řádku Azure (CLI). Tento článek vyžaduje [klasické rozhraní příkazového řádku Azure](../../cli-install-nodejs.md). Vzhledem k tomu, že rozhraní příkazového řádku Azure jde použít jenom pro prostředky Azure Resource Manageru, nelze použít pro tuto migraci.

> [!NOTE]
> Všechny operace, je zde popsáno, jsou idempotentní. Pokud máte jiný problém, než je nepodporovaná funkce nebo Chyba konfigurace, doporučujeme, abyste se znovu pokusíte přípravy, přerušení nebo potvrzení operace. Platforma bude potom akci opakujte.
> 
> 

<br>
Tady je Vývojový diagram k identifikaci pořadí, ve kterém kroky je nutné provést během procesu migrace.

![Snímek obrazovky, který ukazuje kroky migrace](../windows/media/migration-classic-resource-manager/migration-flow.png)

## <a name="step-1-prepare-for-migration"></a>Krok 1: Příprava na migraci
Tady je několik osvědčených postupů, které doporučujeme při hodnocení migrace prostředků IaaS z classic do Resource Manageru:

* Přečtěte si [seznam nepodporovaných konfigurací nebo funkce](../windows/migration-classic-resource-manager-overview.md). Pokud máte virtuální počítače, které používají nepodporované konfigurace nebo funkce, doporučujeme počkejte podpora funkce/Konfigurace oznámena. Alternativně můžete odebrat tuto funkci nebo přesunout mimo tuto konfiguraci pro povolení migrace, pokud vyhovuje vašim potřebám.
* Pokud máte automatizované skripty, které nasadit infrastrukturu a aplikace ještě dnes, pokuste se vytvořit podobné nastavení testu pomocí těchto skriptů pro migraci. Ukázková prostředí můžete také nastavit pomocí webu Azure portal.

> [!IMPORTANT]
> Application Gateway nejsou aktuálně podporovány pro migraci z modelu nasazení classic do Resource Manageru. Pokud chcete migrovat klasické virtuální sítě pomocí služby Application gateway, odebrání brány před spuštěním operace přípravy přesunout sítě. Po dokončení migrace znovu připojte bránu v Azure Resource Manageru. 
>
>Brány ExpressRoute se připojuje k okruhů ExpressRoute v jiném předplatném není možné migrovat automaticky. V takovém případě odeberte bránu ExpressRoute, migraci virtuální sítě a znovu vytvořte bránu. Podrobnosti najdete na [migrace okruhů ExpressRoute a přidružených virtuálních sítí z klasického modelu nasazení Resource Manageru](../../expressroute/expressroute-migration-classic-resource-manager.md) Další informace.
> 
> 

## <a name="step-2-set-your-subscription-and-register-the-provider"></a>Krok 2: Nastavte předplatné a zaregistrujte poskytovatele
Pro scénáře migrace, budete muset nastavit prostředí pro obě classic a Resource Manageru. [Instalace Azure CLI](../../cli-install-nodejs.md) a [vyberte své předplatné](/cli/azure/authenticate-azure-cli).

Přihlášení ke svému účtu.

    azure login

Pomocí následujícího příkazu vyberte předplatné Azure.

    azure account set "<azure-subscription-name>"

> [!NOTE]
> Registrace je však časovém kroku je nutné provést jednou před pokusem o migraci. Bez registrace zobrazí následující chybová zpráva 
> 
> *Chybného požadavku: Předplatné není zaregistrované pro migraci.* 
> 
> 

Zaregistrovat poskytovatele prostředků migrace pomocí následujícího příkazu. Všimněte si, že v některých případech může tento příkaz vyprší časový limit. Registrace ale budou úspěšné.

    azure provider register Microsoft.ClassicInfrastructureMigrate

Počkejte prosím 5 minut pro registraci dokončit. Pomocí následujícího příkazu můžete zkontrolovat stav schválení. Ujistěte se, že je RegistrationState `Registered` předtím, než budete pokračovat.

    azure provider show Microsoft.ClassicInfrastructureMigrate

Teď přepněte rozhraní příkazového řádku pro `asm` režimu.

    azure config mode asm

## <a name="step-3-make-sure-you-have-enough-azure-resource-manager-virtual-machine-vcpus-in-the-azure-region-of-your-current-deployment-or-vnet"></a>Krok 3: Ujistěte se, že máte dostatek virtuálních procesorů na virtuální počítač Azure Resource Manageru v oblasti Azure pro vaše aktuální nasazení nebo virtuální sítě
Pro tento krok je potřeba přepnout na `arm` režimu. To lze proveďte pomocí následujícího příkazu.

```
azure config mode arm
```

Následující příkaz rozhraní příkazového řádku můžete použít ke kontrole aktuální počet virtuálních procesorů, které máte v Azure Resource Manageru. Další informace o kvóty virtuálních procesorů, naleznete v tématu [limity a Azure Resource Manageru](../../azure-subscription-service-limits.md#limits-and-the-azure-resource-manager)

```
azure vm list-usage -l "<Your VNET or Deployment's Azure region"
```

Po ověření tento krok, můžete přepnout zpět na `asm` režimu.

    azure config mode asm


## <a name="step-4-option-1---migrate-virtual-machines-in-a-cloud-service"></a>Krok 4: Možnost 1 - migrovat virtuální počítače v cloudové službě
Pomocí následujícího příkazu Získejte seznam cloudových služeb a pak vyberte cloudovou službu, kterou chcete migrovat. Poznámka: Pokud jsou virtuální počítače v rámci cloudové služby ve virtuální síti nebo pokud mají webové nebo pracovní role, zobrazí se chybová zpráva.

    azure service list

Spusťte následující příkaz k z podrobné výstupu získejte název nasazení pro cloudovou službu. Ve většině případů název nasazení je stejný jako název cloudové služby.

    azure service show <serviceName> -vv

Nejprve ověřte, jestli je možné migrovat cloudové službě pomocí následujících příkazů:

```shell
azure service deployment validate-migration <serviceName> <deploymentName> new "" "" ""
```

Příprava virtuálních počítačů v rámci cloudové služby pro migraci. Máte dvě možnosti, jak vybírat.

Pokud chcete migrovat virtuální počítače k virtuální síti vytvořené platformy, použijte následující příkaz.

    azure service deployment prepare-migration <serviceName> <deploymentName> new "" "" ""

Pokud chcete migrovat na existující virtuální síť v modelu nasazení Resource Manager, použijte následující příkaz.

    azure service deployment prepare-migration <serviceName> <deploymentName> existing <destinationVNETResourceGroupName> <subnetName> <vnetName>

Po úspěšné operace přípravy si můžete si projít podrobný výstup, chcete-li získat informace o stavu migrace virtuálních počítačů a ujistěte se, že jsou v `Prepared` stavu.

    azure vm show <vmName> -vv

Zkontrolujte konfiguraci připravené prostředky s využitím webu Azure portal nebo rozhraní příkazového řádku. Pokud ještě nejste připraveni na migraci a chcete přejít zpět do původní stavu, použijte následující příkaz.

    azure service deployment abort-migration <serviceName> <deploymentName>

Pokud připravené konfigurací spokojeni, můžete posunout vpřed a potvrdit prostředky pomocí následujícího příkazu.

    azure service deployment commit-migration <serviceName> <deploymentName>



## <a name="step-4-option-2----migrate-virtual-machines-in-a-virtual-network"></a>Krok 4: Možnost 2 – migrace virtuálních počítačů ve virtuální síti
Vyberte virtuální síť, která chcete migrovat. Všimněte si, že virtuální síť obsahuje webové nebo pracovní role nebo virtuálních počítačů pomocí nepodporované konfigurace, obdržíte chybovou zprávu ověření.

Získáte všechny virtuální sítě v předplatném pomocí následujícího příkazu.

    azure network vnet list

Výstup bude vypadat přibližně takto:

![Snímek obrazovky z příkazového řádku se zvýrazněným názvem celý virtuální sítě.](../media/virtual-machines-linux-cli-migration-classic-resource-manager/vnet.png)

Ve výše uvedeném příkladu **virtualNetworkName** je celý název **"Skupina classicubuntu16 classicubuntu16"**.

Nejprve ověřte, jestli je možné migrovat virtuální sítě pomocí následujícího příkazu:

```shell
azure network vnet validate-migration <virtualNetworkName>
```

Připravte virtuální sítě podle vašeho výběru pro migraci pomocí následujícího příkazu.

    azure network vnet prepare-migration <virtualNetworkName>

Zkontrolujte konfiguraci pro připravené virtuální počítače pomocí webu Azure portal nebo rozhraní příkazového řádku. Pokud ještě nejste připraveni na migraci a chcete přejít zpět do původní stavu, použijte následující příkaz.

    azure network vnet abort-migration <virtualNetworkName>

Pokud připravené konfigurací spokojeni, můžete posunout vpřed a potvrdit prostředky pomocí následujícího příkazu.

    azure network vnet commit-migration <virtualNetworkName>

## <a name="step-5-migrate-a-storage-account"></a>Krok 5: Migrace účtu úložiště
Po dokončení migrace virtuálních počítačů, doporučujeme, migrujte účet úložiště.

Příprava na migraci účtu úložiště pomocí následujícího příkazu

    azure storage account prepare-migration <storageAccountName>

Zkontrolujte konfiguraci pro účet úložiště připravené s využitím webu Azure portal nebo rozhraní příkazového řádku. Pokud ještě nejste připraveni na migraci a chcete přejít zpět do původní stavu, použijte následující příkaz.

    azure storage account abort-migration <storageAccountName>

Pokud připravené konfigurací spokojeni, můžete posunout vpřed a potvrdit prostředky pomocí následujícího příkazu.

    azure storage account commit-migration <storageAccountName>

## <a name="next-steps"></a>Další postup

* [Přehled o platformou podporované migraci prostředků IaaS z modelu classic na Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Podrobné technické informace o platformou podporované migraci z modelu Classic na Azure Resource Manager](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Plánování migrace prostředků IaaS z nasazení Classic do Azure Resource Manageru](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Migrace prostředků IaaS z modelu classic na Azure Resource Manager pomocí Powershellu](../windows/migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Komunitní nástroje pro pomoc s migrací prostředků IaaS z modelu classic na Azure Resource Manager](../windows/migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Běžné chyby při migraci](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Projděte si nejčastější dotazy o migraci prostředků IaaS z modelu classic na Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
