---
title: Red Hat Enterprise Linux image s vlastními předplatné | Microsoft Docs
description: Přečtěte si o imagích s vlastními předplatnými pro Red Hat Enterprise Linux v Azure
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: BorisB2015
editor: ''
ms.assetid: f495f1b4-ae24-46b9-8d26-c617ce3daf3a
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 6/6/2019
ms.author: alsin
ms.openlocfilehash: afd59c44bea238b7dd6f116e491054f8b4a52d48
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75486505"
---
# <a name="red-hat-enterprise-linux-bring-your-own-subscription-images-in-azure"></a>Red Hat Enterprise Linux image vlastního předplatného v Azure
V Azure jsou k dispozici image Red Hat Enterprise Linux (RHEL) prostřednictvím modelu průběžných plateb (PAYG) nebo Přineste si vlastní předplatné (BYOS). Tento dokument poskytuje přehled imagí BYOS v Azure.

## <a name="important-points-to-consider"></a>Důležité body, které je třeba zvážit

- Image RHEL BYOS, které jsou v tomto programu k dispozici, jsou obrázky RHEL připravené pro produkční prostředí podobné obrázkům RHEL PAYG v galerii Azure nebo na webu Marketplace. Proces registrace pro získání imagí je ve verzi Preview.

- Obrázky následují naše aktuální zásady popsané v tématu [Red Hat Enterprise Linux images v Azure](./redhat-images.md) .

- Standardní zásady podpory se vztahují na virtuální počítače vytvořené z těchto imagí.

- Virtuální počítače zřízené z imagí RHEL BYOS nenesou poplatky za RHEL spojené s imagemi RHEL PAYG.

- Image jsou neoprávněné, takže musíte použít Správce předplatného k registraci a přihlášení k odběru virtuálních počítačů za účelem získání aktualizací ze Red Hat přímo.

- V současné době není možné dynamicky přepínat mezi BYOS a PAYG modely fakturace pro Image Linux. K přepnutí modelu fakturace se vyžaduje opětovné nasazení virtuálního počítače z příslušné image.

- Azure Disk Encryption (ADE) se na těchto obrázcích BYOS podporuje. Podpora ADE je aktuálně ve verzi Preview. Před konfigurací ADE se musíte zaregistrovat v Red Hat pomocí Správce předplatného. Po registraci můžete pro konfiguraci ADE použít odkaz: [povolení Azure Disk Encryption pro virtuální počítače s IaaS Linux](https://docs.microsoft.com/azure/virtual-machines/linux/disk-encryption-overview) .

- I když se obrázky nezmění (mimo standardní aktualizace a opravy), proces registrace je ve verzi Preview a tok se dále vylepšuje, aby se proces zjednodušil.

- Máte plnou kontrolu nad virtuálními počítači, které jsou už zřízené z těchto imagí, nebo pomocí snímků bez ohledu na konečnou implementaci.

## <a name="requirements-and-conditions-to-access-the-rhel-byos-images"></a>Požadavky a podmínky pro přístup k BYOS imagí RHEL

1. Seznamte se s podmínkami [programu Red Hat Cloud Access](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) a zaregistrujte se na [registrační stránce pro cloudový přístup Red Hat](https://access.redhat.com/cloude/manager/image_imports/new).

1. Vyplňte [formulář žádosti o přístup RHEL BYOS](https://aka.ms/rhel-byos). Budete muset mít k dispozici číslo účtu Red Hat i k vašim předplatným Azure, ke kterým máte přístup pomocí RHEL imagí BYOS.

1. Po kontrole a schvalování přes Red Hat i Microsoft budou mít vaše předplatné Azure povolený přístup k obrázkům.

### <a name="expected-time-for-image-access"></a>Očekávaný čas pro přístup k obrázku

Po dokončení formuláře RHEL BYOS a přijetí podmínek vám Red Hat ověří nárok na Image BYOS do tří pracovních dnů a pokud je to možné, dostanete přístup k obrázkům BYOS během jednoho pracovního dne.

## <a name="use-the-rhel-byos-images-from-the-azure-portal"></a>Použití imagí RHEL BYOS z webu Azure Portal

1. Po povolení předplatného RHELch imagí BYOS ho můžete najít v [Azure Portal](https://portal.azure.com) tak, že přejdete na **vytvořit prostředek** a pak **zobrazíte vše**.

1. V horní části stránky se zobrazí, že máte soukromé nabídky.

    ![Veřejné nabídky Marketplace](./media/rhel-byos-privateoffers.png)

1. Můžete kliknout na fialový odkaz nebo přejít dolů k dolnímu okraji stránky a zobrazit vaše soukromé nabídky.

1. Zbytek zřizování v uživatelském rozhraní se neliší od žádné jiné existující image Red Hat. Vyberte verzi RHEL a podle pokynů zřiďte svůj virtuální počítač. Tento postup vám také umožní přijmout podmínky image v posledním kroku.

>[!NOTE]
>Tyto kroky zatím neumožňují RHEL image BYOS pro programové nasazení. další krok bude nutný, jak je popsáno v níže uvedené části "Další informace".

Zbytek tohoto dokumentu se zaměřuje na metodu CLI, která umožňuje zřídit a přijmout podmínky pro image. Uživatelské rozhraní a CLI jsou plně zaměnitelné, pokud jde o konečný výsledek (zřízený virtuální počítač RHEL BYOS).

## <a name="use-the-rhel-byos-images-from-the-azure-cli"></a>Použití imagí RHEL BYOS z Azure CLI
Následující postup vás provede procesem prvotního nasazení pro virtuální počítač s RHEL pomocí rozhraní příkazového řádku Azure CLI. V těchto pokynech se předpokládá, že máte nainstalované rozhraní příkazového [řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

>[!IMPORTANT]
>Ujistěte se, že používáte všechna malá písmena v odkazech vydavatele, nabídky, plánu a obrázku pro všechny následující příkazy.

1. Ověřte, že jste v požadovaném předplatném:
    ```azurecli
    az account show -o=json
    ```

1. Vytvořte skupinu prostředků pro virtuální počítač RHEL BYOS:
    ```azurecli
    az group create --name <name> --location <location>
    ```

1. Přijměte podmínky pro Image:
    ```azurecli
    az vm image terms accept --publisher redhat --offer rhel-byos --plan <SKU value here> -o=jsonc

    # Example:
    az vm image terms accept --publisher redhat --offer rhel-byos --plan rhel-lvm75 -o=jsonc

    OR

    az vm image terms accept --urn RedHat:rhel-byos:rhel-lvm8:8.0.20190620
    ```
    >[!NOTE]
    >Tyto výrazy je potřeba přijmout *jednou pro každou skladovou jednotku na obrázku za předplatné Azure*.

1. Volitelné Ověřte nasazení virtuálního počítače pomocí následujícího příkazu:
    ```azurecli
    az vm create -n <VM name> -g <resource group name> --image <image urn> --validate

    # Example:
    az vm create -n rhel-byos-vm -g rhel-byos-group --image RedHat:rhel-byos:rhel-lvm75:7.5.20190620
    ```

1. Zřiďte virtuální počítač spuštěním stejného příkazu, který není bez `--validate` argumentu:
    ```azurecli
    az vm create -n <VM name> -g <resource group name> --image <image urn> --validate
    ```

1. Připojte se k VIRTUÁLNÍmu počítači přes SSH a ověřte, jestli máte neoprávněnou image. Uděláte to tak, že spustíte `sudo yum repolist`. Výstup vás vyzve k použití Správce předplatného k registraci virtuálního počítače pomocí Red Hat.

## <a name="additional-information"></a>Další informace
- Pokud se pokusíte zřídit virtuální počítač v předplatném, které není pro tuto nabídku povoleno, zobrazí se následující chyba a pokud chcete povolit vaše předplatné, obraťte se na Microsoft nebo Red Hat.
    ```
    "Offer with PublisherId: redhat, OfferId: rhel-byos, PlanId: rhel-lvm75 is private and can not be purchased by subscriptionId: GUID"
    ```

- Pokud vytvoříte snímek z image RHEL BYOS a publikujete obrázek v [galerii sdílených imagí](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries), budete muset zadat informace o plánu, které odpovídají původnímu zdroji snímku. Příkaz může například vypadat jako (Poznamenejte si parametry plánu v posledním řádku):
    ```azurecli
    az vm create –image \
    "/subscriptions/GUID/resourceGroups/GroupName/providers/Microsoft.Compute/galleries/GalleryName/images/ImageName/versions/1.0.0" \
    -g AnotherGroupName --location EastUS2 -n VMName \
    --plan-publisher redhat --plan-product rhel-byos --plan-name rhel-lvm75
    ```

- Pokud používáte automatizaci k zřizování virtuálních počítačů z imagí RHEL BYOS, budete muset zadat parametry plánu podobné tomu, co bylo uvedeno výše. Například pokud používáte Terraformu, zadejte informace o plánu do [bloku plánu](https://www.terraform.io/docs/providers/azurerm/r/virtual_machine.html#plan).

## <a name="next-steps"></a>Další kroky
* Přečtěte si další informace o [infrastruktuře aktualizací Red Hat pro Azure](./redhat-rhui.md).
* Další informace o obrázcích Red Hat v Azure najdete na [stránce s dokumentací](./redhat-images.md).
* Informace o zásadách podpory Red Hat pro všechny verze RHEL najdete na stránce [Red Hat Enterprise Linux životní cyklus](https://access.redhat.com/support/policy/updates/errata) .