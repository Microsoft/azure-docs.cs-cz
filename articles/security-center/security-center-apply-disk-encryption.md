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
ms.openlocfilehash: d0f96fe758966a435f8fb8e448e75cbb18b85122
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77604525"
---
# <a name="apply-disk-encryption-in-azure-security-center"></a>Použití šifrování disku v Azure Security Center
Azure Security Center doporučuje, abyste šifrování disků použili, pokud máte disky virtuálních počítačů s Windows nebo Linuxem, které nejsou šifrované pomocí služby Azure Disk Encryption. Tato služba umožňuje šifrovat disky virtuálních počítačů IaaS s Windows nebo Linuxem.  Na virtuálním počítači se doporučuje šifrování svazku operačního systému i svazku s daty.

Šifrování disku používá standardní funkci [Nástroje bitlocker](https://technet.microsoft.com/library/cc732774.aspx) systému Windows a funkci [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) systému Linux. Tyto funkce poskytují operační systém a šifrování dat, které pomáhají chránit a chránit vaše data a splňují závazky organizace v oblasti zabezpečení a dodržování předpisů. Šifrování disku je integrované s [Azure Key Vault,](https://azure.microsoft.com/documentation/services/key-vault/) které vám pomohou řídit a spravovat šifrovací klíče a tajné klíče disku v předplatném trezoru klíčů a zároveň zajistí, že všechna data na discích virtuálních počítačů budou šifrována v klidovém stavu ve vašem [úložišti Azure](https://azure.microsoft.com/documentation/services/storage/).

> [!NOTE]
> Šifrování disku Azure je podporované v následujících operačních systémech Windows server – Windows Server 2008 R2, Windows Server 2012 a Windows Server 2012 R2. Šifrování disku je podporováno na následujících operačních systémech linuxových serverů - Ubuntu, CentOS, SUSE a SUSE Linux Enterprise Server (SLES).
>
>

## <a name="implement-the-recommendation"></a>Provést doporučení
1. V okně **Doporučení** vyberte **Použít šifrování disku**.
2. V okně **Použít šifrování disku** se zobrazí seznam virtuálních počítačů, pro které se doporučuje šifrování disku.
3. Podle pokynů použijte šifrování pro tyto virtuální společnosti.

![][1]

Chcete-li zašifrovat virtuální počítače Azure, které byly centrum zabezpečení označeny jako potřebné šifrování, doporučujeme následující kroky:

* Instalace a konfigurace Azure Powershellu. To umožňuje spustit příkazy Prostředí PowerShell potřebné k nastavení předpokladů potřebných k šifrování virtuálních počítačů Azure.
* Získejte a spusťte skript Azure Disk EncryptionPrerequisites Azure PowerShell.
* Šifrujte své virtuální počítače.

[Šifrování virtuálního počítače Windows IaaS pomocí Azure PowerShellu](../virtual-machines/windows/disk-encryption-powershell-quickstart.md) vás provede těmito kroky. Toto téma předpokládá, že používáte klientský počítač se systémem Windows, ze kterého nakonfigurujete šifrování disku.

Existuje mnoho přístupů, které lze použít pro virtuální počítače Azure. Pokud jste už s Azure PowerShellem nebo rozhraním příkazového řádku Azure CLI dobře obeznámeni, dáte možná přednost alternativním přístupům. Další informace o těchto dalších přístupech najdete v [tématu šifrování disku Azure](../security/fundamentals/encryption-overview.md).

## <a name="see-also"></a>Viz také
Tento dokument vám ukázal, jak implementovat doporučení Centra zabezpečení "Použít šifrování disku". Další informace o šifrování disku najdete v následujících tématech:

* [Šifrování a správa klíčů pomocí azure key vaultu](https://azure.microsoft.com/documentation/videos/azurecon-2015-encryption-and-key-management-with-azure-key-vault/) (video, 36 min 39 sec) – Naučte se používat správu šifrování disků pro virtuální počítače IaaS a Azure Key Vault k ochraně a zabezpečení vašich dat.
* [Šifrování disku Azure](../security/fundamentals/encryption-overview.md) (dokument) – Zjistěte, jak povolit šifrování disku pro virtuální počítače se systémem Windows a Linux.

Pokud se o službě Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Nastavení zásad zabezpečení v Azure Security Center](tutorial-security-policy.md) – zjistěte, jak nakonfigurovat zásady zabezpečení.
* [Monitorování stavu zabezpečení v Azure Security Center](security-center-monitoring.md) – zjistěte, jak sledovat stav prostředků Azure.
* [Správa a reakce na výstrahy zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md) – zjistěte, jak spravovat výstrahy zabezpečení a reagovat na ně.
* [Správa doporučení zabezpečení v Azure Security Center](security-center-recommendations.md) – zjistěte, jak vám doporučení pomáhají chránit prostředky Azure.
* [Blog o zabezpečení Azure](https://blogs.msdn.com/b/azuresecurity/) – Najděte blogové příspěvky o zabezpečení Azure a dodržování předpisů.

<!--Image references-->
[1]: ./media/security-center-apply-disk-encryption/apply-disk-encryption.png
