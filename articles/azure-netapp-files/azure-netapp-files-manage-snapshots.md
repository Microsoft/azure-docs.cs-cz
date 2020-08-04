---
title: Správa snímků pomocí Azure NetApp Files | Microsoft Docs
description: Popisuje, jak vytvořit a spravovat snímky pomocí Azure NetApp Files.
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
ms.date: 07/24/2020
ms.author: b-juche
ms.openlocfilehash: 271c3c9f63ee3f761826e214f3bf32a8df5f1cbe
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/03/2020
ms.locfileid: "87533287"
---
# <a name="manage-snapshots-by-using-azure-netapp-files"></a>Správa snímků s využitím služby Azure NetApp Files

Azure NetApp Files podporuje vytváření snímků na vyžádání a použití zásad snímku k naplánování automatického vytváření snímků.  Snímek můžete také obnovit na nový svazek.  

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

Funkce **zásad snímku** je aktuálně ve verzi Preview. Pokud tuto funkci používáte poprvé, budete ji muset nejdřív zaregistrovat. 

1. Zaregistrujte funkci: 

    ```azurepowershell-interactive
    Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSnapshotPolicy
    ```

2. Ověřte stav registrace funkce: 

    > [!NOTE]
    > **RegistrationState** může být ve `Registering` stavu několik minut, než se změní na `Registered` . Než budete pokračovat, počkejte, než se stav **zaregistruje** .

    ```azurepowershell-interactive
    Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSnapshotPolicy
    ```

### <a name="create-a-snapshot-policy"></a>Vytvoření zásady snímku 

Zásady snímků vám umožní určit četnost vytváření snímků v hodinách, denním, týdenním nebo měsíčním cyklu. Musíte také zadat maximální počet snímků, které se mají zachovat pro daný svazek.  

1.  V zobrazení účtu NetApp klikněte na **zásady snímku**.

    ![Navigace v zásadách snímků](../media/azure-netapp-files/snapshot-policy-navigation.png)

2.  V okně zásady snímku nastavte stav zásady na **povoleno**. 

3.  Klikněte na kartu **hodinové**, **denní**, **týdenní**nebo **měsíční** a vytvořte si hodinové, denní, týdenní nebo měsíční zásady pro snímky. Zadejte **počet snímků, které mají být zachovány**.  

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

1.  Přejděte na stránku **svazky** , klikněte pravým tlačítkem na svazek, na který chcete použít zásadu snímku, a vyberte **Upravit**.

    ![Místní nabídka – svazky kliknutím pravým tlačítkem](../media/azure-netapp-files/volume-right-cick-menu.png) 

2.  V okně Upravit v části **zásada snímku**vyberte zásadu, která se má pro svazek použít.  Zásadu aplikujete kliknutím na **OK** .  

    ![Úprava zásad snímku](../media/azure-netapp-files/snapshot-policy-edit.png) 

### <a name="modify-a-snapshot-policy"></a>Úprava zásady snímku 

Existující zásadu snímku můžete upravit tak, aby změnila stav zásad, četnost snímků (každou hodinu, denně, týdně nebo měsíčně) nebo počet snímků, které mají být zachovány.  
 
1.  V zobrazení účtu NetApp klikněte na **zásady snímku**.

2.  Klikněte pravým tlačítkem myši na zásadu snímku, kterou chcete upravit, a pak vyberte **Upravit**.

    ![Zásada snímku – nabídka kliknutí pravým tlačítkem myši](../media/azure-netapp-files/snapshot-policy-right-click-menu.png) 

3.  Proveďte změny v okně zásady snímků, které se zobrazí, a pak klikněte na **Uložit**. 

### <a name="delete-a-snapshot-policy"></a>Odstranění zásad snímku 

Zásadu snímku, kterou už nechcete zachovat, můžete odstranit.   

1.  V zobrazení účtu NetApp klikněte na **zásady snímku**.

2.  Klikněte pravým tlačítkem myši na zásadu snímku, kterou chcete upravit, a pak vyberte **Odstranit**.

    ![Zásada snímku – nabídka kliknutí pravým tlačítkem myši](../media/azure-netapp-files/snapshot-policy-right-click-menu.png) 

3.  Kliknutím na **Ano** potvrďte, že chcete odstranit zásadu snímku.   

    ![Potvrzení odstranění zásady snímku](../media/azure-netapp-files/snapshot-policy-delete-confirm.png) 

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

## <a name="next-steps"></a>Další kroky

* [Vysvětlení hierarchie úložiště služby Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
* [Omezení prostředků pro službu Azure NetApp Files](azure-netapp-files-resource-limits.md)
