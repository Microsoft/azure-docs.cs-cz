---
title: Obnovení virtuálních počítačů VMware s využitím Azure Backup Serveru
description: K obnovení virtuálních počítačů VMware běžících na serveru VMware vCenter/ESXi použijte Azure Backup Server (MABS).
ms.topic: conceptual
ms.date: 08/18/2019
ms.openlocfilehash: b3f61aa828db39aeb11b1ce46a850d9a5b868653
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88263516"
---
# <a name="restore-vmware-virtual-machines"></a>Obnovení virtuálních počítačů VMware

Tento článek vysvětluje, jak pomocí serveru Microsoft Azure Backup (MABS) obnovit body obnovení virtuálních počítačů VMware. Přehled použití MABS k obnovení dat najdete v tématu [obnovení chráněných dat](./backup-azure-alternate-dpm-server.md). V konzole pro správu MABS existují dva způsoby, jak najít obnovitelná data – prohledávání nebo procházení. Při obnovování dat můžete nebo nebudete chtít obnovit data nebo virtuální počítač do stejného umístění. Z tohoto důvodu podporuje MABS tři možnosti obnovení pro zálohování virtuálních počítačů VMware:

* **Obnovení původního umístění (OLR)** – k obnovení CHRÁNĚNÉHO virtuálního počítače do původního umístění použijte OLR. Virtuální počítač můžete obnovit do původního umístění pouze v případě, že nebyly přidány nebo smazány žádné disky, protože došlo k zálohování. Pokud byly disky přidány nebo odstraněny, je nutné použít obnovení do alternativního umístění.

* **Obnovení do alternativního umístění (ALR)** – Pokud původní virtuální počítač chybí nebo pokud nechcete narušit původní virtuální počítač, OBNOVte virtuální počítač do alternativního umístění. Pokud chcete virtuální počítač obnovit do alternativního umístění, musíte zadat umístění hostitele ESXi, fondu zdrojů, složky a úložiště dat úložiště a cesty. Z důvodu rozlišení obnoveného virtuálního počítače z původního virtuálního počítače MABS připojí k názvu virtuálního počítače "-obnovený".

* **Individuální obnovení umístění souborů (ilr)** – Pokud je chráněný virtuální počítač s Windows serverem, můžete jednotlivé soubory nebo složky uvnitř virtuálního počítače obnovit pomocí ilr schopnosti MABS. Postup obnovení jednotlivých souborů najdete v postupu dále v tomto článku.

## <a name="restore-a-recovery-point"></a>Obnovení bodu obnovení

1. V konzole pro správu MABS vyberte **zobrazení obnovení**.

2. Pomocí podokna procházení vyhledejte nebo vyfiltrujte virtuální počítač, který chcete obnovit. Jakmile vyberete virtuální počítač nebo složku, zobrazí se v podokně body obnovení dostupné body obnovení.

    ![Dostupné body obnovení](./media/restore-azure-backup-server-vmware/recovery-points.png)

3. V poli **body obnovení pro** vyberte v nabídce kalendář a rozevírací nabídky datum, kdy byl bod obnovení proveden. Kalendářní data v tučném textu mají dostupné body obnovení.

4. Kliknutím na tlačítko **obnovit** na pásu karet nástroje spusťte **Průvodce obnovením**.

    ![Průvodce obnovením, Kontrola výběru obnovení](./media/restore-azure-backup-server-vmware/recovery-wizard.png)

5. Kliknutím na tlačítko **Další** přejdete na obrazovku **zadat možnosti obnovení** .

6. Pokud chcete povolit omezení šířky pásma sítě, na obrazovce **zadat možnosti obnovení** vyberte **Upravit**. Omezení sítě vypnete tak, že vyberete **Další**. Pro virtuální počítače VMware nejsou k dispozici žádné další možnosti na této obrazovce průvodce. Pokud se rozhodnete změnit omezení šířky pásma sítě, zapněte ji v dialogovém okně omezení výběrem možnosti **Povolit omezování využití šířky pásma sítě** . Po povolení nakonfigurujte **Nastavení** a **plán práce**.

7. Na obrazovce **Vybrat typ obnovení** vyberte, zda chcete provést obnovení do původní instance nebo do nového umístění. Pak vyberte **Další**.

     * Pokud zvolíte **obnovit do původní instance**, nemusíte v průvodci dělat žádné další volby. Použijí se data pro původní instanci.

     * Pokud zvolíte možnost **Obnovit jako virtuální počítač na jakémkoli hostiteli**, pak na obrazovce **zadat cíl** zadejte informace o **hostiteli ESXi, fondu zdrojů, složce** a **cestě**.

      ![Vybrat typ obnovení](./media/restore-azure-backup-server-vmware/recovery-type.png)

8. Na obrazovce **Souhrn** zkontrolujte nastavení a výběrem možnosti **obnovit** spusťte proces obnovení. Na obrazovce **stav obnovení** se zobrazuje průběh operace obnovení.

## <a name="restore-an-individual-file-from-a-vm"></a>Obnovení jednotlivého souboru z virtuálního počítače

Jednotlivé soubory můžete obnovit z chráněného bodu obnovení virtuálního počítače. Tato funkce je dostupná jenom pro virtuální počítače s Windows serverem. Obnovení jednotlivých souborů je podobné jako obnovení celého virtuálního počítače, s výjimkou přechodu na VMDK a vyhledání souborů, které chcete, před zahájením procesu obnovení. Postup obnovení jednotlivého souboru nebo výběr souborů z virtuálního počítače s Windows serverem:

>[!NOTE]
>Obnovení jednotlivých souborů z virtuálního počítače je dostupné jenom pro body obnovení virtuálních počítačů s Windows a disků.

1. V konzole pro správu MABS vyberte zobrazení **obnovení** .

2. Pomocí podokna **procházení** vyhledejte nebo vyfiltrujte virtuální počítač, který chcete obnovit. Jakmile vyberete virtuální počítač nebo složku, zobrazí se v **podokně body obnovení** dostupné body obnovení.

    ![Podokno body obnovení pro](./media/restore-azure-backup-server-vmware/vmware-rp-disk.png)

3. V podokně **body obnovení pro:** pomocí kalendáře vyberte datum, které obsahuje požadovaný bod obnovení (y). V závislosti na tom, jak byla zásada zálohování nakonfigurovaná, můžou mít data více než jeden bod obnovení. Jakmile vyberete den, kdy byl bod obnovení proveden, ujistěte se, že jste zvolili správný **čas obnovení**. Pokud má vybrané datum více bodů obnovení, zvolte ho tak, že ho vyberete v rozevírací nabídce doba obnovení. Po zvolení bodu obnovení se seznam obnovitelných položek zobrazí v podokně **cesta:** .

4. Chcete-li najít soubory, které chcete obnovit, v podokně **cesta** dvakrát klikněte na položku ve sloupci **obnovitelné položky** a otevřete ji. Vyberte soubor, soubory nebo složky, které chcete obnovit. Chcete-li vybrat více položek, stiskněte klávesu **CTRL** při výběru jednotlivých položek. Pomocí podokna **cesta** můžete vyhledat seznam souborů nebo složek zobrazených ve sloupci **obnovitelné položky** . **Seznam hledání níže** nehledá podsložky. Pokud chcete prohledávat podsložky, poklikejte na složku. K přesunu z podřízené složky do nadřazené složky použijte tlačítko **nahoru** . Můžete vybrat více položek (soubory a složky), ale musí být ve stejné nadřazené složce. V rámci jedné úlohy obnovení nemůžete obnovit položky z více složek.

    ![Kontrola výběru obnovení](./media/restore-azure-backup-server-vmware/vmware-rp-disk-ilr-2.png)

5. Po výběru položek pro obnovení klikněte na pásu karet nástroje konzoly pro správu na tlačítko **obnovit** a otevřete **Průvodce obnovením**. V Průvodci obnovením obrazovka **Kontrola výběru obnovení** zobrazuje vybrané položky, které mají být obnoveny.

6. Pokud chcete povolit omezení šířky pásma sítě, na obrazovce **zadat možnosti obnovení** vyberte **Upravit**. Omezení sítě vypnete tak, že vyberete **Další**. Pro virtuální počítače VMware nejsou k dispozici žádné další možnosti na této obrazovce průvodce. Pokud se rozhodnete změnit omezení šířky pásma sítě, zapněte ji v dialogovém okně omezení výběrem možnosti **Povolit omezování využití šířky pásma sítě** . Po povolení nakonfigurujte **Nastavení** a **plán práce**.
7. Na obrazovce **Vybrat typ obnovení** vyberte **Další**. Soubory nebo složky můžete obnovit pouze do síťové složky.
8. Na obrazovce **zadat cíl** vyberte **Procházet** a vyhledejte síťové umístění pro vaše soubory nebo složky. MABS vytvoří složku, do které se zkopírují všechny obnovené položky. Název složky má předponu, MABS_day měsíc-rok. Když vyberete umístění obnovených souborů nebo složky, poskytnou se podrobnosti pro toto umístění (cíl, cílová cesta a dostupný prostor).

    ![Zadejte umístění pro obnovení souborů](./media/restore-azure-backup-server-vmware/specify-destination.png)

9. Na obrazovce **zadat možnosti obnovení** vyberte, které nastavení zabezpečení se má použít. Můžete se rozhodnout změnit omezení využití šířky pásma sítě, ale omezení je ve výchozím nastavení zakázané. Nepovoluje se taky **obnovení sítě San** a **oznámení** .
10. Na obrazovce **Souhrn** zkontrolujte nastavení a výběrem možnosti **obnovit** spusťte proces obnovení. Na obrazovce **stav obnovení** se zobrazuje průběh operace obnovení.

## <a name="next-steps"></a>Další kroky

Pokud chcete řešit problémy při použití Azure Backup Server, přečtěte si [příručku pro řešení potíží pro Azure Backup Server](./backup-azure-mabs-troubleshoot.md).
