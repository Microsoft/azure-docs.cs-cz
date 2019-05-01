---
title: Rychlý start – nastavení NetApp soubory Azure a vytvoření svazku systému souborů NFS | Dokumentace Microsoftu
description: Rychlý start – popisuje, jak rychle nastavit NetApp soubory Azure a vytvořte svazek.
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
ms.topic: quickstart
ms.date: 4/16/2019
ms.author: b-juche
ms.openlocfilehash: f0f3dea1c30790bb109d7cb7af07abd8fbb1ace4
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2019
ms.locfileid: "64702880"
---
# <a name="quickstart-set-up-azure-netapp-files-and-create-an-nfs-volume"></a>Rychlý start: Nastavení souborů NetApp Azure a vytvoření svazku systému souborů NFS 

Tento článek ukazuje, jak rychle nastavit NetApp soubory Azure a vytvořte svazek. 

V tomto rychlém startu se nastavit následující položky:

- Registrace pro soubory Azure NetApp a poskytovatele prostředků NetApp
- Účet NetApp
- Kapacita fondu
- Svazek systému souborů NFS pro soubory Azure NetApp

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="before-you-begin"></a>Než začnete 

Musíte být součástí programu ve verzi Public Preview a pro přístup k poskytovateli prostředků Microsoft.NetApp na seznamu povolených. Podrobnosti o zapojení do programu verze Public Preview najdete na [registrační stránce Azure NetApp Files Public Preview](https://aka.ms/nfspublicpreview). 

## <a name="register-for-azure-netapp-files-and-netapp-resource-provider"></a>Zaregistrujte se soubory Azure NetApp a poskytovatele prostředků NetApp

1. Na webu Azure Portal klikněte na ikonu Azure Cloud Shell v pravém horním rohu.

    ![Ikona Azure Cloud Shell](../media/azure-netapp-files/azure-netapp-files-azure-cloud-shell-window.png)

2. Určete předplatné, které bylo přidat na seznam povolených souborů NetApp Azure:
    
        az account set --subscription <subscriptionId>

3. Registrace poskytovatele prostředků Azure: 
    
        az provider register --namespace Microsoft.NetApp --wait  

    Proces registrace může trvat nějakou dobu.

## <a name="create-a-netapp-account"></a>Vytvoření účtu NetApp

1. Na webu Azure portal vyhledávacího pole zadejte **souborů NetApp Azure** a pak vyberte **souborů NetApp Azure (preview)** ze seznamu, který se zobrazí.

      ![Vyberte soubory, které Azure NetApp](../media/azure-netapp-files/azure-netapp-files-select-azure-netapp-files.png)

2. Kliknutím na **+ Přidat** vytvořte nový účet NetApp.

     ![Vytvořit nový účet NetApp](../media/azure-netapp-files/azure-netapp-files-create-new-netapp-account.png)

3. V okně Nový účet NetApp zadejte následující informace: 
   1. Zadejte **myaccount1** pro název účtu. 
   2. Vyberte své předplatné.
   3. Vyberte **vytvořit nový** a vytvořte novou skupinu prostředků. Zadejte **myRG1** pro název skupiny prostředků. Klikněte na **OK**. 
   4. Vyberte umístění vašeho účtu.  

      ![Nové okno účtu NetApp](../media/azure-netapp-files/azure-netapp-files-new-account-window.png)  

      ![Okno skupiny prostředků](../media/azure-netapp-files/azure-netapp-files-resource-group-window.png)

4. Klikněte na tlačítko **vytvořit** k vytvoření nového účtu NetApp.

## <a name="set-up-a-capacity-pool"></a>Nastavení fondu kapacity

1. Z okna správy souborů NetApp Azure vyberte svůj účet NetApp (**myaccount1**).

    ![Vyberte účet NetApp](../media/azure-netapp-files/azure-netapp-files-select-netapp-account.png)  

2. Z okna správy souborů NetApp Azure svého účtu NetApp, klikněte na tlačítko **kapacity fondů**.

    ![Klikněte na tlačítko kapacity fondů](../media/azure-netapp-files/azure-netapp-files-click-capacity-pools.png)  

3. Klikněte na tlačítko **+ přidat fondy**. 

    ![Klikněte na tlačítko Přidat fondy](../media/azure-netapp-files/azure-netapp-files-click-add-pools.png)  

4. Zadejte informace pro kapacita fondu: 
    1. Zadejte **mypool1** jako název fondu.
    2. Vyberte **Premium** úrovně služby. 
    3. Zadejte **4 (TiB)** jako velikost fondu. 

5. Klikněte na **OK**.

## <a name="create-an-nfs-volume-for-azure-netapp-files"></a>Vytvoření svazku systému souborů NFS pro soubory Azure NetApp

1. Z okna správy souborů NetApp Azure svého účtu NetApp, klikněte na tlačítko **svazky**.

    ![Klikněte na svazky](../media/azure-netapp-files/azure-netapp-files-click-volumes.png)  

2. Klikněte na **+ Přidat svazek**.

    ![Klikněte na tlačítko Přidat svazky](../media/azure-netapp-files/azure-netapp-files-click-add-volumes.png)  

3. V části Vytvoření svazku okna poskytují informace o svazku: 
   1. Zadejte **myvol1** jako název svazku. 
   3. Vyberte kapacitu fondu (**mypool1**).
   4. Použijte výchozí hodnotu pro kvótu. 
   5. V rámci virtuální sítě, klikněte na tlačítko **vytvořit nový** vytvořit novou virtuální síť Azure (Vnet).  Potom zadejte následující informace:
       * Zadejte **myvnet1** jako název virtuální sítě.
       * Zadejte adresní prostor pro nastavení, například 10.7.0.0/16
       * Zadejte **myANFsubnet** jako název podsítě.
       * Zadejte rozsah adres podsítě, například 10.7.0.0/24. Všimněte si, že vyhrazenou podsíť nejde sdílet s jiným prostředkům.
       * Vyberte **Microsoft.NetApp/volumes** pro delegování podsítě.
       * Klikněte na tlačítko **OK** vytvořte síť Vnet.
   6. V podsíti, vyberte nově vytvořenou virtuální síť (**myvnet1**) jako podsítě delegáta.

      ![Vytvoření svazku okna](../media/azure-netapp-files/azure-netapp-files-create-volume-window.png)  

      ![Vytvořit okno virtuální sítě](../media/azure-netapp-files/azure-netapp-files-create-virtual-network-window.png)  

4. Klikněte na tlačítko **protokol**a pak vyberte **systému souborů NFS** jako typ protokolu pro daný svazek.   

    Zadejte **myfilepath1** jako cesta k souboru, který se použije k vytvoření cesty exportu svazku. 

    ![Zadejte protokol NFS pro rychlý start](../media/azure-netapp-files/azure-netapp-files-quickstart-protocol-nfs.png)

5. Klikněte na **Zkontrolovat a vytvořit**.

    ![Zkontrolovat a vytvořit okno](../media/azure-netapp-files/azure-netapp-files-review-and-create-window.png)  

5. Projděte si informace o svazku a pak klikněte na **vytvořit**.  
    Vytvořený svazku se zobrazí v okně svazky.

    ![Vytvoření svazku](../media/azure-netapp-files/azure-netapp-files-create-volume-created.png)  

## <a name="clean-up-resources"></a>Vyčištění prostředků

Jakmile budete hotovi a chcete, můžete odstranit skupinu prostředků. Akce odstranění skupiny prostředků je nevratné.  

> [!IMPORTANT]
> Všechny prostředky v rámci skupiny prostředků se trvale odstraní a není možné vrátit zpět. 

1. Na webu Azure portal vyhledávacího pole zadejte **souborů NetApp Azure** a pak vyberte **souborů NetApp Azure** ze seznamu, který se zobrazí.

2. V seznamu odběrů klikněte na skupinu prostředků (myRG1), které chcete odstranit. 

    ![Přejděte do skupiny prostředků](../media/azure-netapp-files/azure-netapp-files-azure-navigate-to-resource-groups.png)


3. Na stránce skupiny prostředků, klikněte na **odstranit skupinu prostředků**.

    ![Odstranění skupiny prostředků](../media/azure-netapp-files/azure-netapp-files-azure-delete-resource-group.png) 

    Otevře se okno s upozorněním týkajícím se prostředků, které budou odstraněny s vybranou skupinou prostředků.

4. Zadejte název skupiny prostředků (myRG1) pro potvrzení, že chcete trvale odstranit skupinu prostředků a všechny prostředky v ní a potom klikněte na tlačítko **odstranit**.

    ![Odstranění skupiny prostředků](../media/azure-netapp-files/azure-netapp-files-azure-confirm-resource-group-deletion.png ) 

## <a name="next-steps"></a>Další postup  

> [!div class="nextstepaction"]
> [Správa svazků s použitím Azure souborů NetApp](azure-netapp-files-manage-volumes.md)  
