---
title: Zálohování virtuálních počítačů Azure ve velkém měřítku
description: Současné zálohování několika virtuálních počítačů do Azure
services: backup
keywords: virtual machine backup; virtual machine back up; back up vm; backup vm; backup Azure vm; backup and disaster recovery
author: markgalioto
ms.author: markgal
ms.date: 2/14/2018
ms.topic: tutorial
ms.service: backup
ms.custom: mvc
ms.openlocfilehash: ecbf583a9b64868004b246bb01e7f174a21496b0
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34607460"
---
# <a name="use-azure-portal-to-back-up-multiple-virtual-machines"></a>Zálohování několika virtuálních počítačů pomocí webu Azure Portal

Když zálohujete data v Azure, ukládáte tato data do prostředku Azure označovaného jako trezor služby Recovery Services. Prostředek trezoru služby Recovery Services je dostupný z nabídky Nastavení většiny služeb Azure. Výhodou integrace trezoru služby Recovery Services do nabídky Nastavení většiny služeb Azure je velmi snadné zálohování dat. Práce s každou databází nebo každým virtuálním počítačem ve vašem podniku zvlášť je však zdlouhavá. Co když chcete zálohovat data všech virtuálních počítačů v jednom oddělení nebo na jednom místě? Zálohování několika virtuálních počítačů je snadné. Stačí vytvořit zásadu zálohování a použít ji na požadované virtuální počítače. Tento kurz vysvětluje následující postupy:

> [!div class="checklist"]
> * Vytvoření trezoru Služeb zotavení
> * Definice zásady zálohování
> * Použití zásady zálohování k ochraně několika virtuálních počítačů
> * Aktivace úlohy zálohování na vyžádání pro chráněné virtuální počítače

## <a name="log-in-to-the-azure-portal"></a>Přihlášení k portálu Azure Portal

Přihlaste se k portálu [Azure Portal](https://portal.azure.com/).

## <a name="create-a-recovery-services-vault"></a>Vytvoření trezoru Služeb zotavení

Trezor služby Recovery Services obsahuje zálohovaná data a zásadu zálohování, která se používá pro chráněné virtuální počítače. Zálohování virtuálních počítačů je místní proces. Virtuální počítače z jedné oblasti nemůžete zálohovat do trezoru služby Recovery Services v jiné oblasti. Pro každou oblast Azure s virtuálními počítači, které se mají zálohovat, tedy musí existovat alespoň jeden trezor služby Recovery Services.

1. V nabídce vlevo vyberte **Všechny služby** a v seznamu služeb zadejte *Recovery Services*. Při psaní se seznam prostředků bude filtrovat. Až se v seznamu zobrazí položka Trezory služby Recovery Services, vyberte ji a otevřete nabídku Trezory služby Recovery Services.

    ![Otevření nabídky trezoru služby Recovery Services](./media/tutorial-backup-vm-at-scale/full-browser-open-rs-vault.png) <br/>

2. V nabídce **Trezory služby Recovery Services** kliknutím na **Přidat** otevřete nabídku Trezor služby Recovery Services.

    ![Otevření nabídky trezoru](./media/tutorial-backup-vm-at-scale/provide-vault-detail-2.png)

3. V nabídce Trezor služby Recovery Services: 

    - Jako **Název** zadejte *myRecoveryServicesVault*.
    - V části **Předplatné** se zobrazí ID aktuálního předplatného. Pokud máte další předplatná, můžete pro nový trezor zvolit jiné předplatné.
    - V části **Skupina prostředků** vyberte **Použít existující** a zvolte *myResourceGroup*. Pokud *myResourceGroup* neexistuje, vyberte **Vytvořit novou** a zadejte *myResourceGroup*.
    - V rozevírací nabídce **Umístění** zvolte *Západní Evropa*.
    - Kliknutím na **Vytvořit** vytvořte svůj trezor služby Recovery Services.

Trezor služby Recovery Services musí být ve stejném umístění jako chráněné virtuální počítače. Pokud máte virtuální počítače v několika oblastech, vytvořte trezor služby Recovery Services v každé z nich. V tomto kurzu se vytvoří trezor služby Recovery Services v oblasti *Západní Evropa*, protože tam se vytvořil virtuální počítač *myVM* (vytvořený pomocí rychlého startu).

Vytvoření trezoru služby Recovery Services může trvat několik minut. Sledujte oznámení o stavu v pravé horní části portálu. Když je trezor vytvořený, zobrazí se v seznamu trezorů Služeb zotavení.

Když vytvoříte trezor služby Recovery Services, ve výchozím nastavení využívá geograficky redundantní úložiště. Geograficky redundantní úložiště za účelem zajištění odolnosti dat několikrát replikuje data mezi dvěma oblastmi Azure.

## <a name="set-backup-policy-to-protect-vms"></a>Nastavení zásady zálohování pro ochranu virtuálních počítačů

Po vytvoření trezoru služby Recovery Services je dalším krokem konfigurace trezoru pro příslušný typ dat a nastavení zásady zálohování. Zásada zálohování je plán frekvence a doby pořizování bodů obnovení. Zásada také obsahuje rozsah uchování bodů obnovení. Pro účely tohoto kurzu předpokládejme, že vaším podnikem je sportovní komplex s hotelem, stadionem, restauracemi a koncesemi, a že chráníte data na virtuálních počítačích. Následující kroky slouží k vytvoření zásady zálohování pro finanční data.

1. V seznamu trezorů služby Recovery Services výběrem trezoru **myRecoveryServicesVault** otevřete jeho řídicí panel.

   ![Otevření nabídky Scénář](./media/tutorial-backup-vm-at-scale/open-vault-from-list.png)

2. Kliknutím na **Zálohování** v nabídce řídicího panelu trezoru otevřete nabídku Zálohování.

3. V nabídce Cíl zálohování v rozevírací nabídce **Kde je spuštěná vaše úloha?** zvolte *Azure*. V rozevírací nabídce **Co chcete zálohovat?** zvolte *Virtuální počítač* a klikněte na **Zálohovat**.

    Tyto akce připraví trezor služby Recovery Services na práci s virtuálním počítačem. Trezory služby Recovery Services mají výchozí zásadu, která vytváří body obnovení každý den a uchovává je po dobu 30 dnů.

    ![Otevření nabídky Scénář](./media/tutorial-backup-vm-at-scale/backup-goal.png)

4. Pokud chcete vytvořit novou zásadu, v nabídce Zásady zálohování v rozevírací nabídce **Výběr zásady zálohování** vyberte *Vytvořit novou*.

    ![Výběr úlohy](./media/tutorial-backup-vm-at-scale/create-new-policy.png)

5. V nabídce **Zásady zálohování** jako **Název zásady** zadejte *Finance*. Proveďte následující změny zásady zálohování: 
    - V části **Frekvence zálohování** nastavte časové pásmo na *Střed*. Vzhledem k tomu, že se sportovní komplex nachází v Texasu, vlastník chce používat místní čas. Frekvenci zálohování ponechte nastavenou na Denní v 3:30.
    - V části **Uchování denního bodu zálohy** nastavte období na 90 dnů.
    - V části **Uchování týdenního bodu zálohy** použijte bod obnovení z *Pondělí* a nastavte dobu uchování na 52 týdnů.
    - V části **Uchování měsíčního bodu zálohy** použijte bod obnovení z První neděle v měsíci a nastavte dobu uchování na 36 měsíců.
    - Zrušte výběr možnosti **Uchování ročního bodu zálohy**. Vedoucí finančního oddělení nechce uchovávat data déle než 36 měsíců.
    - Kliknutím na **OK** vytvořte zásadu zálohování.

    ![Výběr úlohy](./media/tutorial-backup-vm-at-scale/set-new-policy.png) 

    Po vytvoření zásady zálohování tuto zásadu přidružte k virtuálním počítačům.

6. V dialogovém okně **Výběr virtuálních počítačů** vyberte *myVM* a kliknutím na **OK** nasaďte zásadu zálohování do virtuálních počítačů. 

    Zobrazí se všechny virtuální počítače ve stejném umístění, které ještě nemají přidruženou žádnou zásadu zálohování. Pro přidružení k zásadě *Finance* jsou vybrané virtuální počítače *myVMH1* a *myVMR1*.

    ![Výběr úlohy](./media/tutorial-backup-vm-at-scale/choose-vm-to-protect.png) 

    Po dokončení nasazení se zobrazí oznámení o úspěšném dokončení nasazení.

## <a name="initial-backup"></a>Prvotní zálohování

Povolili jste zálohování pro trezory služby Recovery Services, ale ještě se nevytvořila prvotní záloha. Osvědčeným postupem pro zotavení po havárii je aktivovat první zálohování, aby se zajistila ochrana vašich dat. 

Spuštění úlohy zálohování na vyžádání:

1. Na řídicím panelu trezoru klikněte na číslo **3** pod textem **Zálohování položek** a otevřete nabídku Zálohování položek.

    ![Ikona nastavení](./media/tutorial-backup-vm-at-scale/tutorial-vm-back-up-now.png)

    Otevře se nabídka **Zálohování položek**.

2. V nabídce **Zálohování položek** klikněte na **Virtuální počítač Azure** a otevřete seznam virtuálních počítačů přidružených k trezoru.

    ![Ikona nastavení](./media/tutorial-backup-vm-at-scale/three-virtual-machines.png)

    Otevře se seznam **Zálohování položek**.

    ![Aktivovaná úloha zálohování.](./media/tutorial-backup-vm-at-scale/initial-backup-context-menu.png)

3. V seznamu **Zálohování položek** klikněte na **...** (tři tečky) a otevřete místní nabídku.

4. V místní nabídce vyberte **Zálohovat nyní**.

    ![Místní nabídka](./media/tutorial-backup-vm-at-scale/context-menu.png)

    Otevře se nabídka Zálohovat nyní.

5. V nabídce Zálohovat nyní zadejte poslední den uchování bodu obnovení a klikněte na **Zálohovat**.

    ![nastavte poslední den uchování bodu obnovení vytvořeného pomocí možnosti Zálohovat nyní](./media/tutorial-backup-vm-at-scale/backup-now-short.png)

    Oznámení nasazení vás budou informovat o aktivaci úlohy zálohování a možnosti sledovat průběh úlohy na stránce Úlohy zálohování. V závislosti na velikosti virtuálního počítače může vytváření prvotní zálohy chvíli trvat.

    Po dokončení úlohy prvotního zálohování se její stav zobrazí v nabídce úlohy zálohování. Úloha zálohování na vyžádání vytvořila prvotní bod obnovení pro virtuální počítač *myVM*. Pokud chcete zálohovat další virtuální počítače, zopakujte tento postup pro každý z nich. 

    ![Dlaždice Úlohy zálohování](./media/tutorial-backup-vm-at-scale/initial-backup-complete.png)
  
## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete pokračovat v práci s dalšími kurzy, neprovádějte čištění prostředků vytvořených v rámci tohoto kurzu. Pokud pokračovat nechcete, pomocí následujících kroků odstraňte všechny prostředky vytvořené v rámci tohoto kurzu na webu Azure Portal.

1. Na řídicím panelu trezoru **myRecoveryServicesVault** klikněte na číslo **3** pod textem **Zálohování položek** a otevřete nabídku Zálohování položek.

    ![Ikona nastavení](./media/tutorial-backup-vm-at-scale/tutorial-vm-back-up-now.png)


2. V nabídce **Zálohování položek** klikněte na **Virtuální počítač Azure** a otevřete seznam virtuálních počítačů přidružených k trezoru.

    ![Ikona nastavení](./media/tutorial-backup-vm-at-scale/three-virtual-machines.png)

    Otevře se seznam **Zálohování položek**.

3. V nabídce **Zálohování položek** klikněte na tři tečky a otevřete místní nabídku.

    ![Ikona nastavení](./media/tutorial-backup-vm-at-scale/context-menu-to-delete-vm.png)

4. V místní nabídce vyberte **Zastavit zálohování** a otevřete nabídku Zastavit zálohování. 

    ![Ikona nastavení](./media/tutorial-backup-vm-at-scale/context-menu-for-delete.png)

5. V nabídce **Zastavit zálohování** vyberte horní rozevírací nabídku a zvolte **Odstranit zálohovaná data**.

6. V dialogovém okně **Zadejte název zálohované položky** zadejte *myVM*.
 
7. Po ověření zálohované položky (zobrazí se značka zaškrtnutí) bude povolené tlačítko **Zastavit zálohování**. Kliknutím na **Zastavit zálohování** zastavte zásadu a odstraňte body obnovení. 

    ![kliknutí na Zastavit zálohování a odstranění trezoru](./media/tutorial-backup-vm-at-scale/provide-reason-for-delete.png).

8. V nabídce trezoru **myRecoveryServicesVault** klikněte na **Odstranit**.

    ![kliknutí na Zastavit zálohování a odstranění trezoru](./media/tutorial-backup-vm-at-scale/deleting-the-vault.png)

    Po odstranění trezoru se vrátíte k seznamu trezorů služby Recovery Services.


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste pomocí webu Azure Portal provedli následující kroky:

> [!div class="checklist"]
> * Vytvoření trezoru Služeb zotavení
> * Nastavení trezoru pro ochranu virtuálních počítačů
> * Vytvoření vlastní zásady zálohování a uchovávání informací
> * Přiřazení zásady za účelem ochrany několika virtuálních počítačů
> * Aktivace zálohování virtuálních počítačů na vyžádání

Přejděte k dalšímu kurzu, kde obnovíte virtuální počítač Azure z disku. 

> [!div class="nextstepaction"]
> [Obnovení virtuálních počítačů pomocí rozhraní příkazového řádku](./tutorial-restore-disk.md)
