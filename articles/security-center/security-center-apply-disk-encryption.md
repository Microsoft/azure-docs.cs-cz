---
title: Použít šifrování disku ve službě Azure Security Center | Dokumentace Microsoftu
description: Tento dokument popisuje, jak implementovat doporučení služby Azure Security Center **použít šifrování disku**.
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
ms.openlocfilehash: d285461aef1e56dfd08a162e51ada0340d6eeae4
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56108267"
---
# <a name="apply-disk-encryption-in-azure-security-center"></a>Použít šifrování disku ve službě Azure Security Center
Azure Security Center doporučuje použít šifrování disku, pokud máte Windows nebo virtuální počítač s Linuxem disky, které nejsou šifrované službou Azure Disk Encryption. Disk Encryption umožňuje šifrování disků Windows a virtuálních počítačů IaaS s Linuxem.  Na virtuálním počítači se doporučuje šifrování svazku operačního systému i svazku s daty.

Disk Encryption používá oborových standardů [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) funkce Windows a [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) funkce systému Linux. Tyto funkce poskytují operačního systému a šifrování dat k ochraně a chránit vaše data a splňovala závazky vaší organizace zabezpečení a dodržování předpisů hlediska. Disk Encryption je integrovaná s [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) pomáhá řídit a spravovat klíče pro šifrování disků a tajné kódy ve vašem předplatném služby Key Vault, současně přitom zajistit, že všechna data na discích virtuálních počítačů jsou zašifrovaná rest ve vaší [Služby azure Storage](https://azure.microsoft.com/documentation/services/storage/).

> [!NOTE]
> Azure Disk Encryption je podporována v následujících Windows server operačních systémů – Windows Server 2008 R2, Windows Server 2012 a Windows Server 2012 R2. Šifrování disku je podporován následujících operačních systémů Linux server – Ubuntu, CentOS, SUSE a operačním systémem SUSE Linux Enterprise Server (SLES).
>
>

## <a name="implement-the-recommendation"></a>Implementace doporučení
1. V **doporučení** okně vyberte **použít šifrování disku**.
2. V **použít šifrování disku** okně se zobrazí seznam virtuálních počítačů, pro které se doporučuje šifrování disku.
3. Postupujte podle pokynů k šifrování použít k těmto virtuálním počítačům.

![][1]

K šifrování Azure Virtual Machines, které byly identifikovány pomocí služby Security Center potřebují šifrování, doporučujeme následující postup:

* Instalace a konfigurace Azure Powershellu. To umožňuje spouštět příkazy prostředí PowerShell k nastavení požadovaných součástí pro šifrování Azure Virtual Machines.
* Získání a spuštění skriptu Azure Powershellu pro Azure Disk Encryption.
* Šifrování virtuálních počítačů.

[Šifrování virtuálního počítače s Windows IaaS s Azure Powershellem](../security/quick-encrypt-vm-powershell.md) vás provede tyto kroky. Toto téma předpokládá, že používáte klientský počítač Windows, ve kterém můžete nakonfigurovat šifrování disku.

Existuje celá řada přístupů, které lze použít pro Azure Virtual Machines. Pokud jste už s Azure PowerShellem nebo rozhraním příkazového řádku Azure CLI dobře obeznámeni, dáte možná přednost alternativním přístupům. Další informace o těchto přístupů najdete v tématu [Azure disk encryption](../security/azure-security-disk-encryption.md).

## <a name="see-also"></a>Další informace najdete v tématech
Tento dokument vám ukázali, jak implementovat doporučení služby Security Center "Použít šifrování disku." Další informace o šifrování disků, naleznete v následujících tématech:

* [Šifrování a Správa klíčů s Azure Key Vault](https://azure.microsoft.com/documentation/videos/azurecon-2015-encryption-and-key-management-with-azure-key-vault/) (videa, 36 minut 39 sekund) – Další informace o použití Správa šifrování disků pro virtuální počítače IaaS a služby Azure Key Vault k ochraně a chránit vaše data.
* [Azure disk encryption](../security/azure-security-disk-encryption-overview.md) (dokument) – zjistěte, jak povolit disk encryption pro Windows a virtuální počítače s Linuxem.

Pokud se o službě Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Nastavení zásad zabezpečení ve službě Azure Security Center](tutorial-security-policy.md) – zjistěte, jak nakonfigurovat zásady zabezpečení.
* [Sledování stavu zabezpečení v Azure Security Center](security-center-monitoring.md) – zjistěte, jak můžete monitorovat stav svých prostředků Azure.
* [Správa a zpracování výstrah zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md) – Zjistěte, jak spravovat výstrahy zabezpečení a reagovat na ně.
* [Správa doporučení zabezpečení v Azure Security Center](security-center-recommendations.md) – zjistěte, jak vám doporučení pomáhají chránit prostředky Azure.
* [Nejčastější dotazy k Azure Security Center](security-center-faq.md) – Přečtěte si nejčastější dotazy k používání této služby.
* [Blog o zabezpečení Azure](https://blogs.msdn.com/b/azuresecurity/) – přečtěte si blog příspěvky o zabezpečení Azure a dodržování předpisů.

<!--Image references-->
[1]: ./media/security-center-apply-disk-encryption/apply-disk-encryption.png
