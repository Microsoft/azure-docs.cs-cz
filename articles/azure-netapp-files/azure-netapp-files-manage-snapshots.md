---
title: Správa snímků pomocí Azure NetApp Files | Microsoft Docs
description: Popisuje, jak vytvářet, spravovat a používat snímky pomocí Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/18/2020
ms.author: b-juche
ms.openlocfilehash: 03b7941385517fe694f0743194655a1b6a1c0e1e
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2020
ms.locfileid: "95253554"
---
# <a name="manage-snapshots-by-using-azure-netapp-files"></a>Správa snímků s využitím služby Azure NetApp Files

Azure NetApp Files podporuje vytváření snímků na vyžádání a použití zásad snímku k naplánování automatického vytváření snímků. Můžete také obnovit snímek na nový svazek, obnovit jeden soubor pomocí klienta nebo vrátit existující svazek pomocí snímku.

> [!NOTE] 
> Informace o správě snímků při replikaci mezi jednotlivými oblastmi najdete v tématu [požadavky a předpoklady pro použití replikace mezi oblastmi](cross-region-replication-requirements-considerations.md).

## <a name="create-an-on-demand-snapshot-for-a-volume"></a>Vytvoření snímku na vyžádání pro svazek

Snímky svazků můžete vytvářet na vyžádání. 

1.  Přejít na svazek, pro který chcete vytvořit snímek. Klikněte na **snímky**.

    ![Přejít na snímky](../media/azure-netapp-files/azure-netapp-files-navigate-to-snapshots.png)

2.  Kliknutím na **+ Přidat snímek** vytvoříte snímek na vyžádání pro svazek.

    ![Přidat snímek](../media/azure-netapp-files/azure-netapp-files-add-snapshot.png)

3.  V okně Nový snímek zadejte název nového snímku, který vytváříte.   

    ![Nový snímek](../media/azure-netapp-files/azure-netapp-files-new-snapshot.png)

4. Klikněte na **OK**. 

## <a name="manage-snapshot-policies"></a>Správa zásad snímků

Pomocí zásad snímku můžete naplánovat, aby se snímky svazků automaticky provedly. Podle potřeby můžete také upravit zásady snímků nebo odstranit zásadu snímku, kterou už nepotřebujete.  

### <a name="register-the-feature"></a>Zaregistrujte funkci.

Funkce **zásad snímku** je aktuálně ve verzi Preview. Pokud tuto funkci používáte poprvé, musíte ji nejprve zaregistrovat. 

1. Zaregistrujte funkci: 

    ```azurepowershell-interactive
    Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSnapshotPolicy
    ```

2. Ověřte stav registrace funkce: 

    > [!NOTE]
    > **RegistrationState** může být ve `Registering` stavu až 60 minut, než se změní na `Registered` . Než budete pokračovat, počkejte, než se stav **zaregistruje** .

    ```azurepowershell-interactive
    Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSnapshotPolicy
    ```
Můžete také použít [příkazy rozhraní příkazového řádku Azure](/cli/azure/feature?preserve-view=true&view=azure-cli-latest) `az feature register` a `az feature show` zaregistrovat funkci a zobrazit stav registrace. 

### <a name="create-a-snapshot-policy"></a>Vytvoření zásady snímku 

Zásady snímků vám umožní určit četnost vytváření snímků v hodinách, denním, týdenním nebo měsíčním cyklu. Musíte také zadat maximální počet snímků, které se mají zachovat pro daný svazek.  

1.  V zobrazení účtu NetApp klikněte na **zásady snímku**.

    ![Navigace v zásadách snímků](../media/azure-netapp-files/snapshot-policy-navigation.png)

2.  V okně zásady snímku nastavte stav zásady na **povoleno**. 

3.  Klikněte na kartu **hodinové**, **denní**, **týdenní** nebo **měsíční** a vytvořte si hodinové, denní, týdenní nebo měsíční zásady pro snímky. Zadejte **počet snímků, které mají být zachovány**.  

    V tématu [omezení prostředků pro Azure NetApp Files](azure-netapp-files-resource-limits.md) o maximálním počtu povolených snímků pro svazek. 

    Následující příklad ukazuje konfiguraci zásad pro hodinové snímky. 

    ![Zásada snímku každou hodinu](../media/azure-netapp-files/snapshot-policy-hourly.png)

    Následující příklad ukazuje denní konfiguraci zásad snímků.

    ![Zásady snímků denně](../media/azure-netapp-files/snapshot-policy-daily.png)

    Následující příklad ukazuje konfiguraci zásad týdenního snímku.

    ![Zásada snímku týdně](../media/azure-netapp-files/snapshot-policy-weekly.png)

    Následující příklad ukazuje konfiguraci zásad měsíčního snímku.

    ![Zásady snímků měsíčně](../media/azure-netapp-files/snapshot-policy-monthly.png) 

4.  Klikněte na **Uložit**.  

Pokud potřebujete vytvořit další zásady pro snímky, opakujte krok 3.
Zásady, které jste vytvořili, se zobrazí na stránce Zásady snímku.

Pokud chcete, aby svazek používal zásadu snímku, je nutné [použít zásadu na svazek](azure-netapp-files-manage-snapshots.md#apply-a-snapshot-policy-to-a-volume). 

### <a name="apply-a-snapshot-policy-to-a-volume"></a>Použití zásad snímku na svazek

Pokud chcete, aby svazek používal zásadu snímku, kterou jste vytvořili, musíte zásadu použít na svazek. 

V rámci replikace mezi oblastmi nejde použít zásadu snímku na cílový svazek.  

1.  Přejděte na stránku **svazky** , klikněte pravým tlačítkem na svazek, na který chcete použít zásadu snímku, a vyberte **Upravit**.

    ![Místní nabídka – svazky kliknutím pravým tlačítkem](../media/azure-netapp-files/volume-right-cick-menu.png) 

2.  V okně Upravit v části **zásada snímku** vyberte zásadu, která se má pro svazek použít.  Zásadu aplikujete kliknutím na **OK** .  

    ![Úprava zásad snímku](../media/azure-netapp-files/snapshot-policy-edit.png) 

### <a name="modify-a-snapshot-policy"></a>Úprava zásady snímku 

Existující zásadu snímku můžete upravit tak, aby změnila stav zásad, četnost snímků (každou hodinu, denně, týdně nebo měsíčně) nebo počet snímků, které mají být zachovány.  
 
1.  V zobrazení účtu NetApp klikněte na **zásady snímku**.

2.  Klikněte pravým tlačítkem na zásadu snímku, kterou chcete upravit, a pak vyberte **Upravit**.

    ![Zásada snímku – nabídka kliknutí pravým tlačítkem myši](../media/azure-netapp-files/snapshot-policy-right-click-menu.png) 

3.  Proveďte změny v okně zásady snímků, které se zobrazí, a pak klikněte na **Uložit**. 

### <a name="delete-a-snapshot-policy"></a>Odstranění zásad snímku 

Zásadu snímku, kterou už nechcete zachovat, můžete odstranit.   

1.  V zobrazení účtu NetApp klikněte na **zásady snímku**.

2.  Klikněte pravým tlačítkem na zásadu snímku, kterou chcete upravit, a pak vyberte **Odstranit**.

    ![Zásada snímku – nabídka kliknutí pravým tlačítkem myši](../media/azure-netapp-files/snapshot-policy-right-click-menu.png) 

3.  Kliknutím na **Ano** potvrďte, že chcete odstranit zásadu snímku.   

    ![Potvrzení odstranění zásady snímku](../media/azure-netapp-files/snapshot-policy-delete-confirm.png) 

## <a name="edit-the-hide-snapshot-path-option"></a>Úprava možnosti skrýt cestu snímku
Možnost skrýt cestu ke snímku určuje, zda je cesta snímku svazku viditelná. Během vytváření svazku [NFS](azure-netapp-files-create-volumes.md#create-an-nfs-volume) nebo [SMB](azure-netapp-files-create-volumes-smb.md#add-an-smb-volume) máte možnost určit, jestli má být cesta snímku skrytá. V případě potřeby můžete následně upravit možnost skrýt cestu k snímku.  

> [!NOTE]
> U [cílového svazku](cross-region-replication-create-peering.md#create-the-data-replication-volume-the-destination-volume) v případě replikace mezi jednotlivými oblastmi je ve výchozím nastavení povolená možnost skrýt cestu snímku a toto nastavení nejde změnit. 

1. Chcete-li zobrazit nastavení možnosti skrýt cestu k snímku svazku, vyberte svazek. V poli **Skrýt cestu snímku** se zobrazuje, zda je možnost povolena.   
    ![Snímek obrazovky, který popisuje pole skrýt cestu snímku.](../media/azure-netapp-files/hide-snapshot-path-field.png) 
2. Chcete-li upravit možnost skrýt cestu k snímku, klikněte na tlačítko **Upravit** na stránce svazek a podle potřeby upravte možnost **Skrýt cestu k snímku** .   
    ![Snímek obrazovky s popisem možnosti upravit snímek svazku](../media/azure-netapp-files/volume-edit-snapshot-options.png) 

## <a name="restore-a-snapshot-to-a-new-volume"></a>Obnovení snímku na nový svazek

V současné době můžete snímek obnovit pouze na nový svazek. 
1. V okně svazku vyberte **snímky** a zobrazte seznam snímků. 
2. Klikněte pravým tlačítkem na snímek, který chcete obnovit, a z možnosti nabídky vyberte **obnovit do nového svazku** .  

    ![Obnovit snímek na nový svazek](../media/azure-netapp-files/azure-netapp-files-snapshot-restore-to-new-volume.png)

3. V okně vytvořit svazek zadejte informace o novém svazku:  
    * **Jméno**   
        Zadejte název svazku, který vytváříte.  
        
        Název musí být v rámci skupiny prostředků jedinečný. Musí mít aspoň tři znaky dlouhé.  Může používat libovolné alfanumerické znaky.

    * **Kvóta**  
        Zadejte velikost logického úložiště, které chcete svazku přidělit.  

    ![Obnovit na nový svazek](../media/azure-netapp-files/snapshot-restore-new-volume.png) 

4. Klikněte na tlačítko **zkontrolovat + vytvořit**.  Klikněte na **Vytvořit**.   
    Nový svazek používá stejný protokol, jaký používá snímek.   
    Nový svazek, ke kterému se snímek obnoví, se zobrazí v okně svazky.

## <a name="restore-a-file-from-a-snapshot-using-a-client"></a>Obnovení souboru ze snímku pomocí klienta

Pokud nechcete [obnovit celý snímek na svazek](#restore-a-snapshot-to-a-new-volume), máte možnost obnovit soubor ze snímku pomocí klienta s připojeným svazkem.  

Připojený svazek obsahuje adresář snímků s názvem  `.snapshot` (v klientech NFS) nebo `~snapshot` (v klientech SMB), který je pro klienta přístupný. Adresář snímků obsahuje podadresáře odpovídající snímkům svazku. Každý podadresář obsahuje soubory snímku. Pokud omylem odstraníte nebo přepíšete soubor, můžete ho obnovit do nadřazeného adresáře pro čtení i zápis zkopírováním souboru z podadresáře Snapshot do adresáře pro čtení i zápis. 

Pokud se adresář snímků nezobrazuje, může být skrytý, protože je aktuálně povolená možnost skrýt cestu k snímku. Můžete [upravit možnost skrýt cestu snímku](#edit-the-hide-snapshot-path-option) a zakázat ji.  

### <a name="restore-a-file-by-using-a-linux-nfs-client"></a>Obnovení souboru pomocí klienta systému Linux NFS 

1. `ls`K vypsání souboru, který chcete obnovit z adresáře, použijte příkaz Linux `.snapshot` . 

    Například:

    `$ ls my.txt`   
    `ls: my.txt: No such file or directory`   

    `$ ls .snapshot`   
    `daily.2020-05-14_0013/              hourly.2020-05-15_1106/`   
    `daily.2020-05-15_0012/              hourly.2020-05-15_1206/`   
    `hourly.2020-05-15_1006/             hourly.2020-05-15_1306/`   

    `$ ls .snapshot/hourly.2020-05-15_1306/my.txt`   
    `my.txt`

2. Pomocí `cp` příkazu zkopírujte soubor do nadřazeného adresáře.  

    Například: 

    `$ cp .snapshot/hourly.2020-05-15_1306/my.txt .`   

    `$ ls my.txt`   
    `my.txt`   

### <a name="restore-a-file-by-using-a-windows-client"></a>Obnovení souboru pomocí klienta Windows 

1. Pokud `~snapshot` je adresář svazku skrytý, zobrazte [skryté položky](https://support.microsoft.com/help/4028316/windows-view-hidden-files-and-folders-in-windows-10) v nadřazeném adresáři, které chcete zobrazit `~snapshot` .

    ![Zobrazit skryté položky](../media/azure-netapp-files/snapshot-show-hidden.png) 

2. Přejděte do podadresáře v adresáři `~snapshot` a vyhledejte soubor, který chcete obnovit.  Klikněte na soubor pravým tlačítkem. Vyberte **Kopírovat**.  

    ![Kopírovat soubor pro obnovení](../media/azure-netapp-files/snapshot-copy-file-restore.png) 

3. Vraťte se do nadřazeného adresáře. Klikněte pravým tlačítkem na nadřazený adresář a vyberte možnost `Paste` vložení souboru do adresáře.

    ![Vložit soubor pro obnovení](../media/azure-netapp-files/snapshot-paste-file-restore.png) 

4. Můžete také kliknout pravým tlačítkem na nadřazený adresář, vybrat **vlastnosti**, kliknout na kartu **předchozí verze** a zobrazit tak seznam snímků a vybrat **obnovit** pro obnovení souboru.  

    ![Vlastnosti předchozích verzí](../media/azure-netapp-files/snapshot-properties-previous-version.png) 

## <a name="revert-a-volume-using-snapshot-revert"></a>Obnovení svazku pomocí vrácení snímku

Funkce obnovení snímku umožňuje rychle vrátit svazek do stavu, ve kterém byl proveden určitý snímek. Ve většině případů je vrácení svazku mnohem rychlejší než obnovení jednotlivých souborů ze snímku do aktivního systému souborů. V porovnání s obnovením snímku na nový svazek je také více místa. 

Možnost vrátit svazek můžete najít v nabídce snímky svazku. Po výběru snímku pro reverzi Azure NetApp Files obnoví svazek na data a časová razítka, která obsahovala při pořízení vybraného snímku. 

> [!IMPORTANT]
> Aktivní systémová data a snímky, které byly provedeny po pořízení vybraného snímku, budou ztraceny. Operace vrátit se změnami snímku nahradí *všechna* data v cílovém svazku daty ve vybraném snímku. Při výběru snímku byste měli věnovat pozornost obsahu snímku a datu vytvoření. Operaci vrácení snímku nelze vrátit zpět.

1. Přejděte do nabídky **snímky** svazku.  Klikněte pravým tlačítkem na snímek, který chcete použít pro operaci vrácení zpět. Vyberte možnost **vrátit svazek**. 

    ![Snímek obrazovky s popisem nabídky, která se zobrazí po kliknutí pravým tlačítkem na snímek](../media/azure-netapp-files/snapshot-right-click-menu.png) 

2. V okně vrátit svazek do snímku zadejte název svazku a klikněte na tlačítko **obnovit**.   

    Svazek je nyní obnoven do bodu v čase vybraného snímku.

    ![Snímek obrazovky s oknem vrátit svazek do snímku](../media/azure-netapp-files/snapshot-revert-volume.png) 

## <a name="delete-snapshots"></a>Odstranit snímky  

Snímky, které už nepotřebujete zachovat, můžete odstranit. 

1. Přejděte do nabídky **snímky** svazku. Klikněte pravým tlačítkem na snímek, který chcete odstranit. Vyberte **Odstranit**.

    ![Snímek obrazovky s popisem nabídky, která se zobrazí po kliknutí pravým tlačítkem na snímek](../media/azure-netapp-files/snapshot-right-click-menu.png) 

2. V okně Odstranit snímek potvrďte, že chcete odstranit snímek kliknutím na **Ano**. 

    ![Snímek obrazovky s potvrzením odstranění snímku](../media/azure-netapp-files/snapshot-confirm-delete.png)  

## <a name="next-steps"></a>Další kroky

* [Řešení potíží se zásadami snímků](troubleshoot-snapshot-policies.md)
* [Omezení prostředků pro službu Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Video o Azure NetApp Files snímků 101](https://www.youtube.com/watch?v=uxbTXhtXCkw&feature=youtu.be)
