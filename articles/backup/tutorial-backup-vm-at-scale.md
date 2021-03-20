---
title: Kurz – zálohování několika virtuálních počítačů Azure
description: V tomto kurzu se naučíte, jak vytvořit trezor Recovery Services, definovat zásady zálohování a současně zálohovat víc virtuálních počítačů.
ms.date: 07/26/2020
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: a9517ffc1e37d50f7c0e57b9ed53fb8bcf55fd70
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "89180571"
---
# <a name="use-azure-portal-to-back-up-multiple-virtual-machines"></a>Zálohování několika virtuálních počítačů pomocí webu Azure Portal

Když zálohujete data v Azure, ukládáte tato data do prostředku Azure označovaného jako trezor služby Recovery Services. Prostředek trezoru služby Recovery Services je dostupný z nabídky Nastavení většiny služeb Azure. Výhodou toho, že je trezor Recovery Services integrovaný do nabídky nastavení většiny služeb Azure, je snadným zálohováním dat. Pracujete ale individuálně s každou databází nebo virtuálním počítačem v podniku. Co když chcete zálohovat data všech virtuálních počítačů v jednom oddělení nebo na jednom místě? Zálohování několika virtuálních počítačů se dá snadno vytvořit vytvořením zásady zálohování a použitím této zásady na požadované virtuální počítače. Tento kurz vysvětluje následující postupy:

> [!div class="checklist"]
>
> * Vytvoření trezoru Služeb zotavení
> * Definice zásady zálohování
> * Použití zásady zálohování k ochraně několika virtuálních počítačů
> * Aktivace úlohy zálohování na vyžádání pro chráněné virtuální počítače

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se na [Azure Portal](https://portal.azure.com/).

## <a name="create-a-recovery-services-vault"></a>Vytvoření trezoru Služeb zotavení

Trezor služby Recovery Services obsahuje zálohovaná data a zásadu zálohování, která se používá pro chráněné virtuální počítače. Zálohování virtuálních počítačů je místní proces. Virtuální počítač nemůžete zálohovat z jednoho umístění do trezoru Recovery Services v jiném umístění. Pro každou oblast Azure s virtuálními počítači, které se mají zálohovat, tedy musí existovat alespoň jeden trezor služby Recovery Services.

1. V nabídce vlevo vyberte **Všechny služby**.

    ![Vyberte Všechny služby.](./media/tutorial-backup-vm-at-scale/click-all-services.png)

1. V dialogovém okně **Všechny služby** zadejte *Recovery Services*. Seznam prostředků se filtruje podle vašeho zadání. Ze seznamu prostředků vyberte **Trezory služby Recovery Services**.

    ![Zadání a volba trezorů služby Recovery Services](./media/tutorial-backup-vm-at-scale/all-services.png)

    Zobrazí se seznam trezorů služby Recovery Services v předplatném.

1. Na řídicím panelu **Trezory služby Recovery Services** vyberte **Přidat**.

    ![Přidání trezoru služby Recovery Services](./media/tutorial-backup-vm-at-scale/add-button-create-vault.png)

1. V nabídce Trezor služby Recovery Services:

    * Jako **Název** zadejte *myRecoveryServicesVault*.
    * V části **Předplatné** se zobrazí ID aktuálního předplatného. Pokud máte další odběry, můžete si vybrat jiné předplatné pro nový trezor.
    * V části **Skupina prostředků** vyberte **Použít existující** a zvolte *myResourceGroup*. Pokud *myResourceGroup* neexistuje, vyberte **vytvořit novou** a zadejte *myResourceGroup*.
    * V rozevírací nabídce **Umístění** zvolte *Západní Evropa*.

    ![Recovery Services hodnoty trezoru](./media/tutorial-backup-vm-at-scale/review-and-create.png)

    Trezor služby Recovery Services musí být ve stejném umístění jako chráněné virtuální počítače. Pokud máte virtuální počítače v několika oblastech, vytvořte trezor služby Recovery Services v každé z nich. V tomto kurzu se vytvoří Recovery Services trezor v *západní Evropa* , protože to je místo, kde se vytvořilo *myVM* (virtuální počítač vytvořený pomocí rychlého startu).

1. Až budete připraveni vytvořit trezor služby Recovery Services, vyberte **Vytvořit**.

    ![Vytvoření trezoru služby Recovery Services](./media/tutorial-backup-vm-at-scale/click-create-button.png)

1. Vytvoření trezoru služby Recovery Services může chvíli trvat. Sledujte oznámení o stavu v oblasti **Oznámení** v pravém horním rohu portálu. Po vytvoření se trezor zobrazí v seznamu trezorů služby Recovery Services. Pokud trezor nevidíte, vyberte **Aktualizovat**.

     ![Aktualizace seznamu trezorů záloh](./media/tutorial-backup-vm-at-scale/refresh-button.png)

Když vytvoříte trezor služby Recovery Services, ve výchozím nastavení využívá geograficky redundantní úložiště. Geograficky redundantní úložiště za účelem zajištění odolnosti dat několikrát replikuje data mezi dvěma oblastmi Azure.

## <a name="set-backup-policy-to-protect-vms"></a>Nastavení zásady zálohování pro ochranu virtuálních počítačů

Po vytvoření trezoru služby Recovery Services je dalším krokem konfigurace trezoru pro příslušný typ dat a nastavení zásady zálohování. Zásada zálohování je plán frekvence a doby pořizování bodů obnovení. Zásada také obsahuje rozsah uchování bodů obnovení. Pro účely tohoto kurzu předpokládáme, že vaše firma je sportovní komplex s hotelem, Stadium a restaurací a s koncesemi a chráníte data na virtuálních počítačích. Následující kroky slouží k vytvoření zásady zálohování pro finanční data.

1. V seznamu trezorů služby Recovery Services výběrem trezoru **myRecoveryServicesVault** otevřete jeho řídicí panel.

   ![Otevření nabídky Scénář](./media/tutorial-backup-vm-at-scale/open-vault-from-list.png)

1. V nabídce řídicího panelu trezoru vyberte **zálohování** a otevřete nabídku zálohování.

1. V nabídce Cíl zálohování v rozevírací nabídce **Kde je spuštěná vaše úloha?** zvolte *Azure*. Z rozevíracího seznamu **co chcete zálohovat** vyberte možnost *virtuální počítač* a vyberte možnost **zálohovat**.

    Tyto akce připraví trezor služby Recovery Services na práci s virtuálním počítačem. Trezory služby Recovery Services mají výchozí zásadu, která vytváří body obnovení každý den a uchovává je po dobu 30 dnů.

    ![Cíl zálohování](./media/tutorial-backup-vm-at-scale/backup-goal.png)

1. Pokud chcete vytvořit novou zásadu, v nabídce zásady zálohování v rozevírací nabídce **Zvolte zásadu zálohování** vyberte *vytvořit novou zásadu*.

    ![Vytvořit novou zásadu](./media/tutorial-backup-vm-at-scale/create-new-policy.png)

1. Otevře se podokno **zásady zálohování** . Vyplňte následující podrobnosti:
   * Jako **název zásady** zadejte *finance*. Proveďte následující změny zásady zálohování:
   * V části **Frekvence zálohování** nastavte časové pásmo na *Střed*. Vzhledem k tomu, že se sportovní komplex nachází v Texasu, vlastník chce používat místní čas. Frekvenci zálohování ponechte nastavenou na Denní v 3:30.
   * V části **Uchování denního bodu zálohy** nastavte období na 90 dnů.
   * V části **Uchování týdenního bodu zálohy** použijte bod obnovení z *Pondělí* a nastavte dobu uchování na 52 týdnů.
   * V části **Uchování měsíčního bodu zálohy** použijte bod obnovení z První neděle v měsíci a nastavte dobu uchování na 36 měsíců.
   * Zrušte výběr možnosti **Uchování ročního bodu zálohy**. Vedoucí finančního oddělení nechce uchovávat data déle než 36 měsíců.
   * Vyberte **OK** a vytvořte zásadu zálohování.

     ![Nastavení zásad zálohování](./media/tutorial-backup-vm-at-scale/set-new-policy.png)

     Po vytvoření zásady zálohování tuto zásadu přidružte k virtuálním počítačům.

1. V části **Virtual Machines** vyberte **Přidat**.

     ![Přidat virtuální počítače](./media/tutorial-backup-vm-at-scale/add-virtual-machines.png)

1. Otevře se podokno **Výběr virtuálních počítačů** . Vyberte *myVM* a kliknutím na **OK** nasaďte zásady zálohování do virtuálních počítačů.

    Zobrazí se všechny virtuální počítače, které jsou ve stejném umístění a které ještě nejsou přidružené k zásadám zálohování. Pro přidružení k zásadě *Finance* jsou vybrané virtuální počítače *myVMH1* a *myVMR1*.

    ![Vyberte virtuální počítače, které chcete chránit.](./media/tutorial-backup-vm-at-scale/choose-vm-to-protect.png)

1. Po výběru virtuálních počítačů vyberte **Povolit zálohování**.

    Až se nasazení dokončí, obdržíte oznámení o úspěšném dokončení nasazení.

## <a name="initial-backup"></a>Prvotní zálohování

Povolili jste zálohování pro Recovery Services trezory, ale nevytvořili jste prvotní zálohování. Osvědčeným postupem při zotavení po havárii je aktivovat první zálohování, aby vaše data byla chráněná.

Spuštění úlohy zálohování na vyžádání:

1. Na řídicím panelu trezoru vyberte **3** v části **zálohované položky** a otevřete tak nabídku zálohované položky.

    ![Zálohované položky](./media/tutorial-backup-vm-at-scale/tutorial-vm-back-up-now.png)

    Otevře se nabídka **Zálohování položek**.

1. V nabídce **zálohované položky** vyberte **virtuální počítač Azure** a otevřete seznam virtuálních počítačů přidružených k trezoru.

    ![Seznam virtuálních počítačů](./media/tutorial-backup-vm-at-scale/three-virtual-machines.png)

1. Otevře se seznam **Zálohování položek**.

    ![Aktivovaná úloha zálohování.](./media/tutorial-backup-vm-at-scale/initial-backup-context-menu.png)

1. V seznamu **zálohované položky** vyberte tři tečky **...** a otevřete tak kontextovou nabídku.

1. V místní nabídce vyberte **Zálohovat nyní**.

    ![Místní nabídka – vybrat zálohování hned](./media/tutorial-backup-vm-at-scale/context-menu.png)

    Otevře se nabídka Zálohovat nyní.

1. V nabídce zálohovat nyní zadejte poslední den pro uchování bodu obnovení a vyberte **OK**.

    ![Nastavte poslední den uchování bodu obnovení zálohy.](./media/tutorial-backup-vm-at-scale/backup-now-short.png)

    Oznámení nasazení vás budou informovat o aktivaci úlohy zálohování a možnosti sledovat průběh úlohy na stránce Úlohy zálohování. V závislosti na velikosti virtuálního počítače může vytváření prvotní zálohy chvíli trvat.

    Po dokončení úlohy prvotního zálohování se její stav zobrazí v nabídce úlohy zálohování. Úloha zálohování na vyžádání vytvořila prvotní bod obnovení pro virtuální počítač *myVM*. Pokud chcete zálohovat další virtuální počítače, zopakujte tento postup pro každý z nich.

    ![Dlaždice Úlohy zálohování](./media/tutorial-backup-vm-at-scale/initial-backup-complete.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud máte v úmyslu pokračovat v práci s dalšími kurzy, neprovádějte čištění prostředků vytvořených v tomto kurzu. Pokud pokračovat nechcete, pomocí následujícího postupu odstraňte všechny prostředky vytvořené v tomto kurzu v Azure Portal.

1. Na řídicím panelu **trezoru myrecoveryservicesvault** vyberte v části **zálohované položky** položku **3** a otevřete tak nabídku zálohované položky.

    ![Otevřít nabídku zálohované položky](./media/tutorial-backup-vm-at-scale/tutorial-vm-back-up-now.png)

1. V nabídce **zálohované položky** vyberte **virtuální počítač Azure** a otevřete seznam virtuálních počítačů přidružených k trezoru.

    ![Seznam virtuálních počítačů](./media/tutorial-backup-vm-at-scale/three-virtual-machines.png)

    Otevře se seznam **Zálohování položek**.

1. V nabídce **zálohované položky** vyberte tři tečky a otevřete tak kontextovou nabídku.

    ![V nabídce zálohované položky otevřete místní nabídku.](./media/tutorial-backup-vm-at-scale/context-menu-to-delete-vm.png)

1. V místní nabídce vyberte **Zastavit zálohování** a otevřete nabídku zastavit zálohování.

    ![Zastavit nabídku zálohování](./media/tutorial-backup-vm-at-scale/context-menu-for-delete.png)

1. V nabídce **Zastavit zálohování** vyberte horní rozevírací nabídku a zvolte **Odstranit zálohovaná data**.

1. V dialogovém okně **Zadejte název zálohované položky** zadejte *myVM*.

1. Po ověření zálohované položky (zobrazí se značka zaškrtnutí) se aktivuje tlačítko **Zastavit zálohování** . Vyberte **Zastavit zálohování** , aby se zásady zastavily a odstranily body obnovení.

    ![Vyberte Zastavit zálohování pro odstranění trezoru.](./media/tutorial-backup-vm-at-scale/provide-reason-for-delete.png)

    >[!NOTE]
    >Odstraněné položky se uchovávají ve stavu tichého odstranění po dobu 14 dnů. Trezor lze odstranit pouze po uplynutí tohoto období. Další informace najdete v tématu [odstranění Azure Backupho trezoru Recovery Services](backup-azure-delete-vault.md).

1. Pokud v trezoru nejsou žádné další položky, vyberte **Odstranit**.

    ![Vybrat odstranit](./media/tutorial-backup-vm-at-scale/deleting-the-vault.png)

    Po odstranění trezoru se vrátíte do seznamu Recovery Services trezorů.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste pomocí webu Azure Portal provedli následující kroky:

> [!div class="checklist"]
>
> * Vytvoření trezoru Služeb zotavení
> * Nastavení trezoru pro ochranu virtuálních počítačů
> * Vytvoření vlastní zásady zálohování a uchovávání informací
> * Přiřazení zásady za účelem ochrany několika virtuálních počítačů
> * Aktivace zálohování virtuálních počítačů na vyžádání

Přejděte k dalšímu kurzu, kde obnovíte virtuální počítač Azure z disku.

> [!div class="nextstepaction"]
> [Obnovení virtuálních počítačů pomocí rozhraní příkazového řádku](./tutorial-restore-disk.md)
