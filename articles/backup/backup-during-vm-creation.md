---
title: Povolení zálohování při vytvoření virtuálního počítače Azure
description: Popisuje, jak povolit zálohování při vytváření virtuálního počítače Azure s Azure Backup.
ms.topic: conceptual
ms.date: 06/13/2019
ms.openlocfilehash: 7739109eb8bad88c9b723e67e13adc78c127499a
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80672813"
---
# <a name="enable-backup-when-you-create-an-azure-vm"></a>Povolení zálohování při vytvoření virtuálního počítače Azure

K zálohování virtuálních počítačů Azure (VM) použijte službu Azure Backup. Virtuální počítače jsou zálohovány podle plánu určeného v zásadách zálohování a body obnovení se vytvářejí ze záloh. Body obnovení jsou uloženy v trezorech služby Recovery Services.

Tento článek podrobně popisuje, jak povolit zálohování při vytváření virtuálního počítače (VM) na webu Azure Portal.  

## <a name="before-you-start"></a>Než začnete

- [Zkontrolujte,](backup-support-matrix-iaas.md#supported-backup-actions) které operační systémy jsou podporované, pokud povolíte zálohování při vytváření virtuálního počítače.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Pokud ještě nejste přihlášení ke svému účtu, přihlaste se na [portál Azure](https://portal.azure.com).

## <a name="create-a-vm-with-backup-configured"></a>Vytvoření virtuálního počítače s nakonfigurovaným zálohováním

1. Na webu Azure Portal klikněte na **Vytvořit prostředek**.

2. Na Azure Marketplace klikněte na **Compute**a vyberte image virtuálního počítače.

3. Nastavte virtuální počítač v souladu s pokyny [pro Windows](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal) nebo [Linux.](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-portal)

4. Na kartě **Správa** klikněte v **položce Povolit zálohování**na **tlačítko Zapnuto**.
5. Zálohování Azure se zálohuje do trezoru služby Recovery Services. Pokud nemáte existující trezor, klikněte na **Vytvořit nový.**
6. Přijměte navrhovaný název úložiště nebo zadejte svůj vlastní.
7. Zadejte nebo vytvořte skupinu prostředků, ve které bude úschovna umístěna. Trezor skupiny prostředků se může lišit od skupiny prostředků virtuálního virtuálního soudu.

    ![Povolení zálohování pro virtuální počítače](./media/backup-during-vm-creation/enable-backup.png)

8. Přijměte výchozí zásady zálohování nebo upravte nastavení.
    - Zásady zálohování určuje, jak často pořizovat snímky zálohy virtuálního počítače a jak dlouho zachovat tyto záložní kopie.
    - Výchozí zásady zálohovat virtuální ho virtuálního montovny jednou denně.
    - Můžete přizpůsobit vlastní zásady zálohování pro virtuální počítač Azure tak, aby zálohy denně nebo týdně.
    - [Přečtěte si další informace](backup-azure-vms-introduction.md#backup-and-restore-considerations) o aspektech zálohování pro virtuální počítače Azure.
    - [Přečtěte si další informace](backup-instant-restore-capability.md) o funkcích okamžitého obnovení.

      ![Výchozí zásady zálohování](./media/backup-during-vm-creation/daily-policy.png)

## <a name="azure-backup-resource-group-for-virtual-machines"></a>Skupina prostředků zálohování Azure pro virtuální počítače

Služba Backup vytvoří samostatnou skupinu prostředků (RG), která se liší od skupiny prostředků virtuálního počítače pro uložení kolekce bodů obnovení (RPC). RPC obsahuje okamžité body obnovení spravovaných virtuálních počítačů. Výchozí formát pojmenování skupiny prostředků vytvořené službou Zálohování je: `AzureBackupRG_<Geo>_<number>`. Například: *AzureBackupRG_northeurope_1*. Nyní můžete přizpůsobit název skupiny prostředků vytvořený službou Azure Backup.

Body k poznámce:

1. Můžete buď použít výchozí název RG, nebo jej upravit podle požadavků vaší společnosti.
2. Zadáte rg název vzor jako vstup při vytváření zásad zálohování virtuálního počítače. Název RG by měl mít `<alpha-numeric string>* n <alpha-numeric string>`následující formát: . 'n' se nahrazuje celým slovem (od 1) a používá se pro škálování, pokud je první RG plný. Jeden RG může mít max 600 RPC dnes.
              ![Volba názvu při vytváření zásad](./media/backup-during-vm-creation/create-policy.png)
3. Vzor by měl dodržovat rg pojmenování pravidla níže a celková délka by neměla překročit maximální povolenou délku názvu RG.
    1. Názvy skupin prostředků umožňují pouze alfanumerické znaky, tečky, podtržítka, pomlčky a závorky. Nemohou skončit v určité době.
    2. Názvy skupin prostředků mohou obsahovat až 74 znaků, včetně názvu RG a přípony.
4. První `<alpha-numeric-string>` je povinná, zatímco druhá po "n" je nepovinná. To platí pouze v případě, že přizpůsobíte název. Pokud do žádného z textových polí nic nezadáte, použije se výchozí název.
5. Název RG můžete upravit úpravou zásady, pokud a v případě potřeby. Pokud se změní název vzor, nové rps budou vytvořeny v novém RG. Staré rps však bude stále umístěn ve staré RG a nebude přesunuta, jako RP kolekce nepodporuje přesun prostředků. Nakonec rps dostane odpadky shromážděné jako body vyprší.
![Změna názvu při úpravě zásad](./media/backup-during-vm-creation/modify-policy.png)
6. Doporučuje se neuzamknout skupinu prostředků vytvořenou pro použití službou Backup.

Pokud chcete nakonfigurovat skupinu prostředků Azure Backup pro virtuální počítače pomocí PowerShellu, přečtěte si [informace o vytvoření skupiny prostředků Azure Backup během uchovávání snímků](backup-azure-vms-automation.md#creating-azure-backup-resource-group-during-snapshot-retention).

## <a name="start-a-backup-after-creating-the-vm"></a>Spuštění zálohy po vytvoření virtuálního počítače

Záloha virtuálního počítače se spustí v souladu s vašimi zásadami zálohování. Doporučujeme však spustit počáteční zálohu.

Po vytvoření virtuálního virtuálního montovny postupujte takto:

1. Ve vlastnostech virtuálního počítače klikněte na **zálohovat**. Stav virtuálního počítače je Počáteční zálohování čeká na spuštění.
2. Chcete-li spustit zálohu na **vyžádání,** klepněte na tlačítko Zálohovat.

    ![Spuštění zálohy na vyžádání](./media/backup-during-vm-creation/run-backup.png)

## <a name="use-a-resource-manager-template-to-deploy-a-protected-vm"></a>Nasazení chráněného virtuálního počítače pomocí šablony Správce prostředků

Předchozí kroky vysvětlují, jak pomocí portálu Azure vytvořit virtuální počítač a chránit ho v trezoru služby Recovery Services. Pokud chcete rychle nasadit jeden nebo více virtuálních počítače a chránit je v trezoru služby Recovery Services, přečtěte si část šablony [Nasazení virtuálního počítače se systémem Windows a povolení zálohování](https://azure.microsoft.com/resources/templates/101-recovery-services-create-vm-and-configure-backup/).

## <a name="next-steps"></a>Další kroky

Teď, když jste virtuální počítač ochránili, přečtěte si, jak ho spravovat a obnovovat.

- [Správa a monitorování virtuálních počítačů](backup-azure-manage-vms.md)
- [Obnovení virtuálního počítače](backup-azure-arm-restore-vms.md)

Pokud narazíte na nějaké problémy, [přečtěte si](backup-azure-vms-troubleshoot.md) průvodce odstraňováním potíží.
