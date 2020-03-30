---
title: Obnovení virtuálních počítačů VMware s využitím Azure Backup Serveru
description: Pomocí Azure Backup Server (MABS) obnovte virtuální počítače VMware spuštěné na serveru VMware vCenter/ESXi.
ms.topic: conceptual
ms.date: 08/18/2019
ms.openlocfilehash: ab2fb4f8f79fa5a664f5cb0ba1bb537c1df658c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77212369"
---
# <a name="restore-vmware-virtual-machines"></a>Obnovení virtuálních počítačů VMware

Tento článek vysvětluje, jak pomocí Microsoft Azure Backup Server (MABS) obnovit body obnovení virtuálního počítače VMWare. Přehled o použití MABS k obnovení dat naleznete v tématu [Obnovení chráněných dat](https://docs.microsoft.com/azure/backup/backup-azure-alternate-dpm-server). V konzole správce MABS existují dva způsoby, jak najít obnovitelná data - vyhledávání nebo procházení. Při obnovení dat můžete nebo nemusíte chtít obnovit data nebo virtuální ho dispozičně do stejného umístění. Z tohoto důvodu MABS podporuje tři možnosti obnovení pro zálohy virtuálních zařízení VMware:

* **Obnovení původního umístění (OLR)** – pomocí OLR obnovit chráněný virtuální počítač do původního umístění. Virtuální modul můžete obnovit do původního umístění pouze v případě, že nebyly přidány nebo odstraněny žádné disky od doby, kdy došlo k zálohování. Pokud byly přidány nebo odstraněny disky, je nutné použít alternativní umístění obnovení.

* **Alternativní umístění obnovení (ALR)** – Když původní virtuální hod chybí, nebo nechcete rušit původní virtuální ho, obnovit virtuální ho do alternativního umístění. Chcete-li obnovit virtuální ho do alternativního umístění, musíte zadat umístění hostitele ESXi, fondu prostředků, složky a úložiště dat úložiště a cestu. Chcete-li pomoci odlišit obnovené virtuálního virtuálního modod od původního virtuálního mase, MABS připojí "-Obnovené" k názvu virtuálního_

* **Individuální obnovení umístění souboru (ILR)** – Pokud chráněný virtuální počítač je virtuální počítač se systémem Windows Server, jednotlivé soubory nebo složky uvnitř virtuálního počítače lze obnovit pomocí funkce ILR MABS. Chcete-li obnovit jednotlivé soubory, naleznete postup dále v tomto článku.

## <a name="restore-a-recovery-point"></a>Obnovení bodu obnovení

1. V konzole správce MABS klikněte na zobrazení pro obnovení.

2. Pomocí podokna Procházet vyhledejte virtuální ho virtuální ho sam, který chcete obnovit. Po výběru virtuálního aplikace nebo složky se v podokně body obnovení zobrazí dostupné body obnovení.

    ![Dostupné body obnovení](./media/restore-azure-backup-server-vmware/recovery-points.png)

3. V **polích Body obnovení** vyberte pomocí kalendáře a rozevíracích nabídek datum, kdy byl bod obnovení odebrán. Data kalendáře tučně mají k dispozici body obnovení.

4. Na pásu karet nástrojů klepněte na **tlačítko Obnovit** a otevřete **Průvodce obnovením**.

    ![Průvodce obnovením, výběr obnovení recenze](./media/restore-azure-backup-server-vmware/recovery-wizard.png)

5. Klepnutím na **tlačítko Další** přetočíme na obrazovku **Určit možnosti obnovení.**

6. Chcete-li povolit omezení šířky pásma sítě, klepněte na obrazovce **Určit možnosti obnovení** na **tlačítko Změnit**. Chcete-li ponechat omezení sítě zakázané, klepněte na tlačítko **Další**. Pro virtuální počítače VMware nejsou k dispozici žádné další možnosti na této obrazovce průvodce. Pokud se rozhodnete upravit omezení šířky pásma sítě, v dialogovém okně Omezení vyberte **možnost Povolit omezení využití šířky pásma sítě,** abyste ji zapnuli. Po povolení nakonfigurujte **plán nastavení** a **práce**.

7. Na obrazovce **Vybrat typ obnovení** zvolte, zda chcete obnovit původní instanci nebo do nového umístění, a klepněte na tlačítko **Další**.

     * Pokud zvolíte **Obnovit původní instanci**, nemusíte v průvodci provádět žádné další volby. Data pro původní instanci se používá.

     * Pokud zvolíte **Obnovit jako virtuální počítač na libovolném hostiteli**, pak na obrazovce Zadat **cíl** zadejte informace pro **hostitele ESXi, fond zdrojů, složku** a **cestu**.

      ![Vybrat typ obnovení](./media/restore-azure-backup-server-vmware/recovery-type.png)

8. Na obrazovce **Souhrn** zkontrolujte nastavení a kliknutím na **Obnovit** spusťte proces obnovení. Obrazovka **Stav obnovení** zobrazuje průběh operace obnovení.

## <a name="restore-an-individual-file-from-a-vm"></a>Obnovení jednotlivého souboru z virtuálního virtuálního aplikace

Jednotlivé soubory můžete obnovit z chráněného bodu obnovení virtuálního počítače. Tato funkce je k dispozici pouze pro virtuální servery Windows Server. Obnovení jednotlivých souborů je podobné obnovení celého virtuálního virtuálního ms, s výjimkou procházení do vmdk a najít soubory, které chcete, před zahájením procesu obnovení. Obnovení jednotlivého souboru nebo výběr souborů z virtuálního virtuálního serveru se systémem Windows Server:

>[!NOTE]
>Obnovení jednotlivého souboru z virtuálního počítače je k dispozici jenom pro body obnovení virtuálních počítačů s Windows a disku.

1. V konzole správce MABS klikněte na zobrazení **pro obnovení.**

2. Pomocí podokna **Procházet** vyhledejte virtuální ho virtuální ho sam, který chcete obnovit. Po výběru virtuálního aplikace nebo složky se v podokně body obnovení zobrazí dostupné body obnovení.

    ![Dostupné body obnovení](./media/restore-azure-backup-server-vmware/vmware-rp-disk.png)

3. V podokně **Body obnovení pro:** vyberte pomocí kalendáře datum, které obsahuje požadované body obnovení. V závislosti na konfiguraci zásad zálohování mohou mít data více než jeden bod obnovení. Po výběru dne, kdy byl bod obnovení odebrán, se ujistěte, že jste zvolili správný **čas obnovení**. Pokud má vybrané datum více bodů obnovení, zvolte bod obnovení tak, že jej vyberete v rozevírací nabídce Doba obnovení. Po výběru bodu obnovení se seznam obnovitelných položek zobrazí v podokně **Cesta:** .

4. Chcete-li najít soubory, které chcete obnovit, otevřete v podokně **Cesta** položku ve sloupci Obnovitelné položky poklepáním na **položku.** Vyberte soubor, soubory nebo složky, které chcete obnovit. Chcete-li vybrat více položek, stiskněte při výběru jednotlivých položek klávesu **Ctrl.** Podokno **Cesta** slouží k prohledání seznamu souborů nebo složek zobrazených ve sloupci **Obnovitelná položka.** **Seznam hledání níže** neprohledává do podsložek. Chcete-li prohledávat podsložky, poklepejte na složku. Pomocí tlačítka **Nahoru** se přesuňte z podřízené složky do nadřazené složky. Můžete vybrat více položek (souborů a složek), ale musí být ve stejné nadřazené složce. Položky z více složek ve stejné úloze obnovení nelze obnovit.

    ![Zkontrolovat výběr obnovení](./media/restore-azure-backup-server-vmware/vmware-rp-disk-ilr-2.png)

5. Po výběru položek pro obnovení otevřete na pásu karet nástroje Konzola správce klepnutím na **tlačítko Obnovit** **Průvodce obnovením**. V Průvodci obnovením se na obrazovce **Výběr obnovení recenze** zobrazí vybrané položky, které mají být obnoveny.

6. Chcete-li povolit omezení šířky pásma sítě, klepněte na obrazovce **Určit možnosti obnovení** na **tlačítko Změnit**. Chcete-li ponechat omezení sítě zakázané, klepněte na tlačítko **Další**. Pro virtuální počítače VMware nejsou k dispozici žádné další možnosti na této obrazovce průvodce. Pokud se rozhodnete upravit omezení šířky pásma sítě, v dialogovém okně Omezení vyberte **možnost Povolit omezení využití šířky pásma sítě,** abyste ji zapnuli. Po povolení nakonfigurujte **plán nastavení** a **práce**.
7. Na obrazovce **Vybrat typ obnovení** klepněte na tlačítko **Další**. Soubory nebo složky nebo složky lze obnovit pouze do síťové složky.
8. Na obrazovce **Určit cíl** klikněte na **Procházet** a vyhledejte síťové umístění pro soubory nebo složky. MABS vytvoří složku, kde jsou zkopírovány všechny obnovené položky. Název složky má předponu MABS_day měsíc. Když vyberete umístění obnovených souborů nebo složky, zobrazí se podrobnosti o tomto umístění (cíl, cílová cesta a dostupné místo).

    ![Určení umístění pro obnovení souborů](./media/restore-azure-backup-server-vmware/specify-destination.png)

9. Na obrazovce **Určit možnosti obnovení** zvolte, které nastavení zabezpečení se má použít. Můžete se rozhodnout upravit omezení využití šířky pásma sítě, ale omezení je ve výchozím nastavení zakázáno. Obnovení **sítě SAN** a **oznámení** také nejsou povoleny.
10. Na obrazovce **Souhrn** zkontrolujte nastavení a kliknutím na **Obnovit** spusťte proces obnovení. Obrazovka **Stav obnovení** zobrazuje průběh operace obnovení.

## <a name="next-steps"></a>Další kroky

Problémy s problémy při používání Serveru Zálohování Azure najdete [v průvodci odstraňováním potíží pro Server zálohování Azure](./backup-azure-mabs-troubleshoot.md).
