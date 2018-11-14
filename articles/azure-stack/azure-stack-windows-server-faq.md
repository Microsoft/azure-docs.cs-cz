---
title: Nejčastější dotazy týkající se serveru systému Windows Azure Stack | Dokumentace Microsoftu
description: Seznam nejčastějších dotazech týkajících se služby Azure Stack Marketplace pro systém Windows Server
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/12/2018
ms.author: sethm
ms.reviewer: avishwan
ms.openlocfilehash: bf70f9a74b58758e03800d7f6fb92a8f8754828a
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2018
ms.locfileid: "51613091"
---
# <a name="windows-server-in-azure-stack-marketplace-faq"></a>Windows Server v Azure stacku nejčastější dotazy k Marketplace

Tento článek obsahuje odpovědi na některé časté otázky ke správě imagí Windows serveru v [Azure Stack Marketplace](azure-stack-marketplace.md).

## <a name="marketplace-items"></a>Položky Marketplace

### <a name="how-do-i-update-to-a-newer-windows-image"></a>Jak můžu aktualizovat na novější image Windows?

Nejdřív zjistěte, pokud žádné šablony Azure Resource Manageru najdete konkrétní verze. Pokud ano, aktualizujte tyto šablony, nebo zachovat starší verze image. Je nejvhodnější použít **verze: nejnovější**.

Dále pokud všechny Virtual Machine Scale Sets odkazovat na konkrétní verzi, by měl myslíte o tom, jestli těchto bude možné později škálovat a rozhodnout, jestli se má zachovat starší verze. Pokud ani jeden z těchto podmínek použití, než si stáhnete novější ty odstraňte starší imagí v Tržišti Marketplace. Použijte správu webu Marketplace k tomu, pokud je to, jak byl stažen původní. Pak si stáhněte novější verzi.

### <a name="what-are-the-licensing-options-for-windows-server-marketplace-images-on-azure-stack"></a>Jaké jsou licenční možnosti pro Image z Marketplace systému Windows Server ve službě Azure Stack?

Společnost Microsoft nabízí dvě verze Image Windows serveru z Tržiště Azure Stack:

- **Platit používáte**: těchto imagí spustit plné ceny měřiče Windows. 
   Kdo by měl používat: Enterprise Agreement (EA) Zákazníci, kteří používají *model fakturace spotřeby*; Poskytovatelé CSP, kteří nechtějí použít licencování SPLA.
- **Používání vlastní licence (BYOL)**: spustit základní měřiče využití těchto imagí.
   Kdo by měl používat: Zákazníci EA s licencí k Windows serveru; Poskytovatelé CSP, kteří používají licence SPLA.

Výhoda použití Azure Hybrid (AHUB) se nepodporuje v Azure stacku. Zákazníci, kteří licencují prostřednictvím "Kapacita" modelu musí používat BYOL image. Pokud testujete s Azure Stack Development Kit (ASDK), můžete použít kteroukoli z těchto možností.

### <a name="what-if-i-downloaded-the-wrong-version-to-offer-my-tenantsusers"></a>Co když stáhli špatné verze do nabídky tenantů nebo uživatelů?

Nejprve odstraňte nesprávnou verzi prostřednictvím webu Marketplace správu. Počkejte na její dokončení plně (podívejte se na oznámení o dokončení, ne okno správy webu Marketplace). Pak si stáhněte správnou verzi.

### <a name="what-if-my-user-incorrectly-checked-the-i-have-a-license-box-in-previous-windows-builds-and-they-dont-have-a-license"></a>Co když Moje uživatelské nesprávně zaškrtnuté políčko "Mám licenci k" v předchozím Windows sestavení a nemají licenci?

Zobrazit [převod virtuálních počítačů Windows serveru s programem zvýhodněné využití zpět na průběžné platby](../virtual-machines/windows/hybrid-use-benefit-licensing.md#powershell-1).

### <a name="what-if-i-have-an-older-image-and-my-user-forgot-to-check-the-i-have-a-license-box-or-we-use-our-own-images-and-we-do-have-enterprise-agreement-entitlement"></a>Co když mám starší obrázek a Moje uživatel zapomněl zaškrtněte políčko "Mám licenci k", nebo jsme použít naše vlastní Image a budeme mít smlouvu Enterprise, nárok?

Zobrazit [převod existujícího virtuálního počítače pomocí programu Azure Hybrid Benefit pro Windows Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md#convert-an-existing-vm-using-azure-hybrid-benefit-for-windows-server). Všimněte si, že v programu Azure Hybrid Benefit nevztahuje na služby Azure Stack, ale účinek tohoto nastavení se nevztahuje.

### <a name="what-about-other-vms-that-use-windows-server-such-as-sql-or-machine-learning-server"></a>Jak ostatní virtuální počítače, které používají Windows Server, jako je SQL nebo Machine Learning Server?

Tyto Image se vztahují **licenseType** parametr, tak, aby byly placené, jaké používáte. Tento parametr lze nastavit (viz Nejčastější dotazy týkající se předchozí odpověď). To platí pouze pro software Windows serveru, aby vrstvami produkty, jako jsou SQL, které vyžadují, abyste si přinesli vlastní licenci. Platba používáte licencování nevztahuje na vrstvený softwarových produktů.

### <a name="i-have-an-enterprise-agreement-ea-and-will-be-using-my-ea-windows-server-license-how-do-i-make-sure-images-are-billed-correctly"></a>Mám smlouvu Enterprise (EA) a budou používat licenci na EA Windows Server; Jak můžu ujistit Image se účtují správně?

Můžete přidat **typ licence: Windows_Server** v šabloně Azure Resource Manageru. Toto nastavení musí být přidaný do každý blok prostředků virtuálního počítače.

## <a name="activation"></a>Aktivace

K aktivaci virtuálního počítače s Windows serverem ve službě Azure Stack, musí být splněné následující podmínky:

- Výrobce OEM nastavil odpovídající značky systému BIOS na každý hostitelský systém v Azure stacku.
- Musíte použít Windows Server 2012 R2 a Windows serveru 2016 [automatická aktivace virtuálního počítače](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v=ws.11)). Služba správy klíčů (KMS) a dalších služeb aktivace nejsou podporovány ve službě Azure Stack.

### <a name="how-can-i-verify-that-my-virtual-machine-is-activated"></a>Jak můžu ověřit, že můj virtuální počítač se aktivuje?

Z příkazového řádku se zvýšenými oprávněními spusťte následující příkaz: 

```shell
slmgr /dlv
``` 

Pokud je správně aktivovaný, uvidíte to jasně určili a zobrazí název hostitele v `slmgr` výstup. Není závislý na vodoznaky na displeji nemusí být aktuální, nebo se zobrazují v různých virtuálních počítačů za vás.

### <a name="my-vm-is-not-set-up-to-use-avma-how-can-i-fix-it"></a>Můj virtuální počítač není nastaven pro použití automatická aktivace virtuálního počítače, jak můžete ho mám opravit?

Z příkazového řádku se zvýšenými oprávněními spusťte následující příkaz: 

```shell
slmgr /ipk <AVMA key> 
```

Přečtěte si článek [automatická aktivace virtuálního počítače](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v=ws.11)) pro klíče, které slouží pro vaši image.

### <a name="i-create-my-own-windows-server-images-how-can-i-make-sure-they-use-avma"></a>Můžu vytvořit vlastní Image Windows serveru, jak můžete ověřit, zda že používají automatická aktivace virtuálního počítače?

Doporučuje se provést `slmgr /ipk` příkazového řádku pomocí odpovídající klíče před spuštěním `sysprep` příkazu. Nebo obsahovat klíč AVMA v kterémkoli instalačním souboru Unattend.exe.

### <a name="i-am-trying-to-use-my-windows-server-2016-image-created-on-azure-and-it-is-not-activating-or-using-kms-activation"></a>Provést pomocí stáhnu image Windows serveru 2016 v Azure vytvoří a není aktivace nebo pomocí aktivace prostřednictvím služby správy KLÍČŮ.

Spusťte příkaz `slmgr /ipk`. Imagích Azure nemusí vrátit správně zpět k automatická aktivace virtuálního počítače, ale pokud mají přístup k systému prostřednictvím služby správy KLÍČŮ Azure, bude aktivovat. Doporučuje se, že zajistíte, že tyto virtuální počítače jsou nastavené na použití automatická aktivace virtuálního počítače.

### <a name="i-have-performed-all-of-these-steps-but-my-virtual-machines-are-still-not-activating"></a>Můžu provedly všechny tyto kroky, ale virtuální počítače stále neaktivují.

Obraťte se na dodavatele hardwaru k ověření, zda byly nainstalovány správné značky systému BIOS.

### <a name="what-about-earlier-versions-of-windows-server"></a>Jak starší verze systému Windows Server?

[Automatická aktivace virtuálního počítače](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v=ws.11)) není podporované v dřívějších verzích Windows serveru. Budete muset ručně aktivovat virtuálních počítačů.

## <a name="next-steps"></a>Další postup

Další informace najdete v následujících článcích:

- [Přehled Azure Stack Marketplace](azure-stack-marketplace.md)
- [Stažení položek z marketplace z Azure do služby Azure Stack](azure-stack-download-azure-marketplace-item.md)
