---
title: Použít šifrování disku v Azure Security Center | Microsoft Docs
description: Tento dokument se dozvíte, jak provést doporučení Azure Security Center ** použít šifrování disku **.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: 6cc7824a-8d6b-4a5f-ab40-e3bbaebc4a91
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2017
ms.author: terrylan
ms.openlocfilehash: 67cff664f3723b2194ecd1519729cca17069d07f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23866258"
---
# <a name="apply-disk-encryption-in-azure-security-center"></a>Použít šifrování disku v Azure Security Center
Azure Security Center doporučuje použít šifrování disku, pokud máte Windows nebo virtuálního počítače s Linuxem disků, které nejsou šifrovány pomocí Azure Disk Encryption. Disk Encryption umožňuje šifrování disků systému Windows a virtuálních počítačů IaaS Linux.  Na virtuálním počítači se doporučuje šifrování svazku operačního systému i svazku s daty.

Šifrování disku používá oborový standard [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) funkce systému Windows a [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) funkce systému Linux. Tyto funkce poskytují operačního systému a šifrování dat k ochraně a zabezpečení dat a splnit vaše organizace závazky zabezpečení a dodržování předpisů. Šifrování disku je integrovaná s [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) můžete řídit a spravovat disku šifrovacích klíčů a tajných klíčů ve vašem předplatném Key Vault, while, zajistíte, že všechna data na discích virtuálních počítačů jsou zašifrovaná přinejmenším ve vaší [Úložiště azure](https://azure.microsoft.com/documentation/services/storage/).

> [!NOTE]
> Azure Disk Encryption je podporován v následujících Windows serveru operačních systémů – Windows Server 2008 R2, Windows Server 2012 a Windows Server 2012 R2. Šifrování disku je podporován v následujících operačních systémů Linux server - Ubuntu, CentOS, SUSE a SUSE Linux Enterprise Server (SLES).
>
>

## <a name="implement-the-recommendation"></a>Implementace doporučení
1. V **doporučení** vyberte **použít šifrování disku**.
2. V **použít šifrování disku** okno, zobrazí se seznam virtuálních počítačů, u kterých se doporučuje šifrování disku.
3. Postupujte podle pokynů pro použití šifrování těchto virtuálních počítačů.

![][1]

Pokud chcete zašifrovat virtuální počítače Azure, které byly identifikovány pomocí služby Security Center potřebují šifrování, doporučujeme následující kroky:

* Instalace a konfigurace Azure Powershellu. To umožňuje spouštění příkazů prostředí PowerShell, které jsou nutné k instalaci požadovaných součástí pro šifrování Azure Virtual Machines.
* Získat a spustit skript prostředí PowerShell Azure Disk Encryption požadavky Azure.
* Šifrování virtuálních počítačů.

[Šifrování virtuálního počítače Azure](security-center-disk-encryption.md) vás provede tyto kroky.  Toto téma předpokládá, že používáte Windows 10 jako klientský počítač, ve kterém můžete nakonfigurovat šifrování disku.

Existuje mnoho přístupů, které lze použít pro virtuální počítače Azure. Pokud jste už s Azure PowerShellem nebo rozhraním příkazového řádku Azure CLI dobře obeznámeni, dáte možná přednost alternativním přístupům. Další informace o těchto dalších přístupů najdete v tématu [Azure disk encryption](../security/azure-security-disk-encryption.md).

## <a name="see-also"></a>Viz také
Tento dokument vám ukázal, jak provést doporučení Security Center "Použít šifrování disku." Další informace o šifrování disku, naleznete v následujících tématech:

* [Šifrování a klíč správy s Azure Key Vault](https://azure.microsoft.com/documentation/videos/azurecon-2015-encryption-and-key-management-with-azure-key-vault/) (video, 36 minut 39 sekund) – Další informace o použití k ochraně a zabezpečení dat Správa šifrování disku pro virtuální počítače IaaS a Azure Key Vault.
* [Šifrování virtuálního počítače Azure](security-center-disk-encryption.md) (dokument) – zjistěte, jak šifrování Azure Virtual Machines.
* [Azure disk encryption](../security/azure-security-disk-encryption.md) (dokument) – informace o povolení šifrování disku pro systém Windows a virtuální počítače s Linuxem.

Pokud se o službě Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Nastavení zásad zabezpečení v Azure Security Center](security-center-policies.md) – zjistěte, jak nakonfigurovat zásady zabezpečení.
* [Sledování stavu zabezpečení v Azure Security Center](security-center-monitoring.md) – Naučte se monitorovat stav svých prostředků Azure.
* [Správa a zpracování výstrah zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md) – Zjistěte, jak spravovat výstrahy zabezpečení a reagovat na ně.
* [Správa doporučení zabezpečení v Azure Security Center](security-center-recommendations.md) – zjistěte, jak vám doporučení pomáhají chránit prostředky v Azure.
* [Nejčastější dotazy k Azure Security Center](security-center-faq.md) – Přečtěte si nejčastější dotazy k používání této služby.
* [Blog o bezpečnosti Azure](http://blogs.msdn.com/b/azuresecurity/) – přečtěte si příspěvky o zabezpečení Azure a dodržování předpisů.

<!--Image references-->
[1]: ./media/security-center-apply-disk-encryption/apply-disk-encryption.png
