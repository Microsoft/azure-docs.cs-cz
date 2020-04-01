---
title: Použití šifrování disku v Azure Security Center | Dokumenty společnosti Microsoft
description: Tento dokument ukazuje, jak implementovat doporučení Azure Security Center **Použít šifrování disku**.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 6cc7824a-8d6b-4a5f-ab40-e3bbaebc4a91
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: memildin
ms.openlocfilehash: e0677ecc63573d04e384a0104ee0e4e28b4d5e3b
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80473274"
---
# <a name="apply-disk-encryption-in-azure-security-center"></a>Použití šifrování disku v Azure Security Center

Azure Security Center doporučuje používat Azure Disk Encryption na nešifrovaných discích na virtuálních počítačích s Windows i Linuxem. Tato služba umožňuje šifrovat disky virtuálních počítačů IaaS s Windows nebo Linuxem.  Na virtuálním počítači se doporučuje šifrování svazku operačního systému i svazku s daty.

Šifrování disku používá standardní funkci [Nástroje bitlocker](https://technet.microsoft.com/library/cc732774.aspx) systému Windows a funkci [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) systému Linux. Tyto funkce poskytují operační systém a šifrování dat, které pomáhají chránit a chránit vaše data a splňují závazky organizace v oblasti zabezpečení a dodržování předpisů. Šifrování disku je integrované s [Azure Key Vault,](https://azure.microsoft.com/documentation/services/key-vault/) které vám pomohou řídit a spravovat šifrovací klíče a tajné klíče disku v předplatném trezoru klíčů a zároveň zajistí, že všechna data na discích virtuálních počítačů budou šifrována v klidovém stavu ve vašem [úložišti Azure](https://azure.microsoft.com/documentation/services/storage/).

Seznam podporovaných verzí Windows a Linuxu najdete v [tématu Podporované virtuální počítače a operační systémy](../virtual-machines/windows/disk-encryption-overview.md#supported-vms-and-operating-systems) v dokumentaci k šifrování disku Azure.

## <a name="implement-the-recommendation"></a>Provést doporučení
1. Na stránce **Doporučení** vyberte **Šifrování disku, které má být použito na virtuálních počítačích**.
2. Z **prostředků Není v pořádku**vyberte virtuální hod, pro který se doporučuje šifrování disku.
3. Podle pokynů použijte šifrování pro tyto virtuální společnosti.

![Použití šifrování disku](./media/security-center-apply-disk-encryption/apply-disk-encryption.png)

Chcete-li zašifrovat virtuální počítače Azure, které byly centrum zabezpečení označeny jako potřebné šifrování, doporučujeme následující kroky:

* Instalace a konfigurace Azure Powershellu. To umožňuje spustit příkazy Prostředí PowerShell potřebné k nastavení předpokladů potřebných k šifrování virtuálních počítačů Azure.
* Získejte a spusťte skript Azure Disk EncryptionPrerequisites Azure PowerShell.
* Šifrujte své virtuální počítače.

[Šifrování virtuálního počítače Windows IaaS pomocí Azure PowerShellu](../virtual-machines/windows/disk-encryption-powershell-quickstart.md) – provede vás těmito kroky a předpokládá, že používáte klientský počítač s Windows, ze kterého můžete nakonfigurovat šifrování disku.

Existuje mnoho přístupů, které lze použít pro virtuální počítače Azure. Pokud jste již dobře zběhlý v Azure PowerShell nebo Azure CLI, pak můžete raději použít alternativní přístupy. Další informace o těchto dalších přístupech najdete v [tématu šifrování disku Azure](../security/fundamentals/encryption-overview.md).

## <a name="see-also"></a>Viz také
Tento dokument vám ukázal, jak implementovat doporučení Centra zabezpečení "Použít šifrování disku". Další informace o šifrování disku najdete v tématu:

* [Šifrování a správa klíčů pomocí azure key vaultu](https://azure.microsoft.com/documentation/videos/azurecon-2015-encryption-and-key-management-with-azure-key-vault/) (video, 36 min 39 sec) – Naučte se používat správu šifrování disků pro virtuální počítače IaaS a trezor klíčů Azure k ochraně a zabezpečení dat.
* [Šifrování disku Azure](../security/fundamentals/encryption-overview.md) (dokument)-- Přečtěte si, jak povolit šifrování disku pro virtuální počítače s Windows a Linuxem.