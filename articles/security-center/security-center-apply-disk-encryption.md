---
title: Použít šifrování disku v Azure Security Center | Microsoft Docs
description: V tomto dokumentu se dozvíte, jak implementovat Azure Security Center doporučení k **použití šifrování disku**.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 6cc7824a-8d6b-4a5f-ab40-e3bbaebc4a91
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: 1abb0a5f5523032440086932eb9a1621d4cef455
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68726239"
---
# <a name="apply-disk-encryption-in-azure-security-center"></a>Použít šifrování disku v Azure Security Center
Azure Security Center doporučuje použít šifrování disku, pokud máte disky virtuálních počítačů se systémem Windows nebo Linux, které nejsou šifrovány pomocí Azure Disk Encryption. Šifrování disku umožňuje šifrovat disky virtuálních počítačů s Windows a Linux IaaS.  Na virtuálním počítači se doporučuje šifrování svazku operačního systému i svazku s daty.

Šifrování disku používá standardní funkci [nástroje BitLocker](https://technet.microsoft.com/library/cc732774.aspx) systému Windows a funkci [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) systému Linux. Tyto funkce poskytují šifrování operačního systému a dat, které vám pomůžou chránit a chránit vaše data a splnit závazky zabezpečení vaší organizace a dodržování předpisů. Šifrování disku se integruje s [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) , které vám pomůžou řídit a spravovat klíče a tajné klíče pro šifrování disků v předplatném Key Vault a přitom zajistit, aby všechna data v těchto discích virtuálních počítačů byla v [Azure Storage](https://azure.microsoft.com/documentation/services/storage/)zašifrovaná v klidovém stavu.

> [!NOTE]
> Azure Disk Encryption se podporují v následujících operačních systémech Windows Server – Windows Server 2008 R2, Windows Server 2012 a Windows Server 2012 R2. Šifrování disku se podporuje na následujících operačních systémech Linux Server – Ubuntu, CentOS, SUSE a SUSE Linux Enterprise Server (SLES).
>
>

## <a name="implement-the-recommendation"></a>Implementace doporučení
1. V okně **doporučení** vyberte **použít šifrování disku**.
2. V okně **použít šifrování disku** se zobrazí seznam virtuálních počítačů, pro které se doporučuje šifrování disku.
3. Postupujte podle pokynů pro použití šifrování u těchto virtuálních počítačů.

![][1]

K šifrování Azure Virtual Machines identifikovaných Security Center podle potřeby šifrování doporučujeme následující postup:

* Instalace a konfigurace Azure Powershellu. To vám umožní spustit příkazy PowerShellu potřebné k nastavení požadavků požadovaných k šifrování Azure Virtual Machines.
* Získejte a spusťte Azure Disk Encryption předpoklady Azure PowerShell skriptu.
* Zašifrujte své virtuální počítače.

[Šifrování virtuálního počítače s Windows IaaS pomocí Azure PowerShell](../security/azure-disk-encryption-linux-powershell-quickstart.md) vás provede těmito kroky. V tomto tématu se předpokládá, že používáte klientský počítač Windows, ze kterého konfigurujete šifrování disku.

K dispozici je mnoho přístupů, které lze použít pro Azure Virtual Machines. Pokud jste už s Azure PowerShellem nebo rozhraním příkazového řádku Azure CLI dobře obeznámeni, dáte možná přednost alternativním přístupům. Další informace o těchto dalších přístupech najdete v tématu [Azure Disk Encryption](../security/azure-security-disk-encryption.md).

## <a name="see-also"></a>Viz také:
V tomto dokumentu jste si ukázali, jak implementovat Security Center doporučení použít šifrování disku. Další informace o šifrování disku najdete v následujících tématech:

* [Šifrování a Správa klíčů pomocí Azure Key Vault](https://azure.microsoft.com/documentation/videos/azurecon-2015-encryption-and-key-management-with-azure-key-vault/) (video, 36 min 39 sec) – Naučte se používat správu šifrování disku pro virtuální počítače s IaaS a Azure Key Vault, které vám pomůžou ochránit a chránit vaše data.
* [Azure Disk Encryption](../security/azure-security-disk-encryption-overview.md) (dokument) – Zjistěte, jak povolit šifrování disku pro virtuální počítače se systémem Windows a Linux.

Pokud se o službě Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Nastavení zásad zabezpečení v Azure Security Center](tutorial-security-policy.md) – Přečtěte si, jak nakonfigurovat zásady zabezpečení.
* [Sledování stavu zabezpečení v Azure Security Center](security-center-monitoring.md) – Naučte se monitorovat stav svých prostředků Azure.
* [Správa a zpracování výstrah zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md) – Zjistěte, jak spravovat výstrahy zabezpečení a reagovat na ně.
* [Správa doporučení zabezpečení v Azure Security Center](security-center-recommendations.md) – Přečtěte si, jak doporučení vám pomůžou chránit vaše prostředky Azure.
* [Nejčastější dotazy k Azure Security Center](security-center-faq.md) – Přečtěte si nejčastější dotazy k používání této služby.
* [Blog o zabezpečení Azure](https://blogs.msdn.com/b/azuresecurity/) – Přečtěte si blogové příspěvky o zabezpečení Azure a dodržování předpisů.

<!--Image references-->
[1]: ./media/security-center-apply-disk-encryption/apply-disk-encryption.png
