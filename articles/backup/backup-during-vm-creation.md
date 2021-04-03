---
title: Povolení zálohování při vytvoření virtuálního počítače Azure
description: Popisuje, jak povolit zálohování při vytváření virtuálního počítače Azure pomocí Azure Backup.
ms.topic: conceptual
ms.date: 06/13/2019
ms.openlocfilehash: ad81300545686d61f42cdd8684e502c937b4fd43
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "89377331"
---
# <a name="enable-backup-when-you-create-an-azure-vm"></a>Povolení zálohování při vytvoření virtuálního počítače Azure

Použijte službu Azure Backup k zálohování virtuálních počítačů Azure (VM). Virtuální počítače se zálohují podle plánu zadaného v zásadách zálohování a body obnovení se vytvářejí ze záloh. Body obnovení jsou uloženy v úložištích Recovery Services.

Tento článek podrobně popisuje, jak povolit zálohování při vytváření virtuálního počítače v Azure Portal.  

## <a name="before-you-start"></a>Než začnete

- [Ověřte](backup-support-matrix-iaas.md#supported-backup-actions) , které operační systémy jsou podporované, pokud při vytváření virtuálního počítače povolíte zálohování.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Pokud jste k vašemu účtu ještě přihlášeni, přihlaste se k [Azure Portal](https://portal.azure.com).

## <a name="create-a-vm-with-backup-configured"></a>Vytvoření virtuálního počítače s nakonfigurovaným zálohováním

1. V Azure Portal vyberte **vytvořit prostředek**.

2. V Azure Marketplace vyberte **COMPUTE** a potom vyberte image virtuálního počítače.

3. Nastavte virtuální počítač v souladu s pokyny pro [Windows](../virtual-machines/windows/quick-create-portal.md) nebo [Linux](../virtual-machines/linux/quick-create-portal.md) .

4. Na kartě **Správa** v části **Povolit zálohování** vyberte **zapnuto**.
5. Azure Backup zálohy do trezoru Recovery Services. Vyberte **vytvořit novou** , pokud nemáte existující trezor.
6. Přijměte navrhovaný název trezoru nebo zadejte vlastní.
7. Zadejte nebo vytvořte skupinu prostředků, ve které bude uložený trezor. Úložiště skupin prostředků se může lišit od skupiny prostředků virtuálních počítačů.

    ![Povolení zálohování pro virtuální počítač](./media/backup-during-vm-creation/enable-backup.png)

8. Přijměte výchozí zásady zálohování nebo upravte nastavení.
    - Zásady zálohování určují, jak často se mají vytvářet záložní snímky virtuálního počítače, a jak dlouho se mají uchovávat tyto záložní kopie.
    - Výchozí zásada zálohuje virtuální počítač jednou denně.
    - Můžete přizpůsobit vlastní zásady zálohování pro virtuální počítač Azure a provádět zálohování každý den nebo každý týden.
    - [Přečtěte si další](backup-azure-vms-introduction.md#backup-and-restore-considerations) informace o požadavcích na zálohování virtuálních počítačů Azure.
    - [Přečtěte si další informace](backup-instant-restore-capability.md) o funkci okamžitého obnovení.

      ![Výchozí zásady zálohování](./media/backup-during-vm-creation/daily-policy.png)

>[!NOTE]
>[SSE a PMK jsou výchozími metodami šifrování](backup-encryption.md) pro virtuální počítače Azure. Azure Backup podporuje zálohování a obnovení těchto virtuálních počítačů Azure.

## <a name="azure-backup-resource-group-for-virtual-machines"></a>Azure Backup skupiny prostředků pro Virtual Machines

Služba Backup vytvoří samostatnou skupinu prostředků (RG), která se liší od skupiny prostředků virtuálního počítače k uložení kolekce bodů obnovení (RPC). Vzdálené volání procedur slouží jako body rychlého obnovení spravovaných virtuálních počítačů. Výchozím formátem názvů pro skupinu prostředků vytvořenou službou zálohování je: `AzureBackupRG_<Geo>_<number>` . Například: *AzureBackupRG_northeurope_1*. Nyní můžete přizpůsobit název skupiny prostředků vytvořený pomocí Azure Backup.

Ukazuje na poznámku:

1. Můžete buď použít výchozí název RG, nebo ho upravit podle požadavků vaší společnosti.
2. Při vytváření zásady zálohování virtuálního počítače zadáváte vzor názvu RG jako vstup. Název RG by měl být v následujícím formátu: `<alpha-numeric string>* n <alpha-numeric string>` . ' n ' je nahrazen celým číslem (od 1) a slouží k horizontálnímu navýšení kapacity, pokud je první RG plný. Jedna RG může mít maximálně 600 vzdálených volání procedur (RPC).
              ![Při vytváření zásad zvolit název](./media/backup-during-vm-creation/create-policy.png)
3. Vzor by měl splňovat pravidla pojmenování RG a celková délka by neměla přesáhnout maximální povolenou délku RG názvu.
    1. V názvech skupin prostředků jsou povolené jenom alfanumerické znaky, tečky, podtržítka, spojovníky a závorky. Nemůžou končit tečkou.
    2. Názvy skupin prostředků mohou obsahovat až 74 znaků, včetně názvu RG a přípony.
4. První `<alpha-numeric-string>` je povinná, zatímco druhá hodnota po ' n ' je volitelná. To platí pouze v případě, že přidáváte vlastní název. Pokud nezadáte nic do některého z textových polí, použije se výchozí název.
5. V případě potřeby můžete upravit název RG úpravou zásady. Pokud se změní vzor názvu, vytvoří se v novém RG nový RPs. Starý RPs se ale pořád bude nacházet ve starém RG a nebude přesunutý, protože kolekce RP nepodporuje přesunutí prostředků. Nakonec se RPs po vypršení platnosti bodů shromáždí do paměti.
![Změnit název při úpravě zásady](./media/backup-during-vm-creation/modify-policy.png)
6. Doporučuje se uzamknout skupinu prostředků vytvořenou pro použití službou zálohování.

Pokud chcete nakonfigurovat Azure Backup skupinu prostředků pro Virtual Machines pomocí PowerShellu, přečtěte si téma [vytvoření Azure Backup skupiny prostředků během uchování snímku](backup-azure-vms-automation.md#creating-azure-backup-resource-group-during-snapshot-retention).

## <a name="start-a-backup-after-creating-the-vm"></a>Po vytvoření virtuálního počítače spustit zálohování

Zálohování virtuálního počítače se spustí v souladu se zásadami zálohování. Doporučujeme však spustit prvotní zálohování.

Po vytvoření virtuálního počítače postupujte takto:

1. Ve vlastnostech virtuálního počítače vyberte **zálohovat**. Stav virtuálního počítače čeká na počáteční zálohování, dokud se nespustí počáteční záloha.
2. Pokud chcete spustit zálohování na vyžádání, vyberte **Zálohovat nyní** .

    ![Spuštění zálohování na vyžádání](./media/backup-during-vm-creation/run-backup.png)

## <a name="use-a-resource-manager-template-to-deploy-a-protected-vm"></a>Použití šablony Správce prostředků k nasazení chráněného virtuálního počítače

Předchozí kroky vysvětlují, jak pomocí Azure Portal vytvořit virtuální počítač a chránit ho v trezoru Recovery Services. Pokud chcete rychle nasadit jeden nebo více virtuálních počítačů a chránit je v úložišti Recovery Services, podívejte se do šablony [nasazení virtuálního počítače s Windows a povolení zálohování](https://azure.microsoft.com/resources/templates/101-recovery-services-create-vm-and-configure-backup/).

## <a name="next-steps"></a>Další kroky

Teď, když jste virtuální počítač chránili, zjistěte, jak ho spravovat a obnovovat.

- [Správa a monitorování virtuálních počítačů](backup-azure-manage-vms.md)
- [Obnovení virtuálního počítače](backup-azure-arm-restore-vms.md)

Pokud narazíte na nějaké problémy, [Přečtěte si](backup-azure-vms-troubleshoot.md) příručku k odstraňování potíží.
