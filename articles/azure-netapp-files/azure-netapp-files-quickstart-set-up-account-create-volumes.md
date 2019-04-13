---
title: Nastavení souborů NetApp Azure a vytvořte svazek | Dokumentace Microsoftu
description: Popisuje, jak rychle nastavit NetApp soubory Azure a vytvořte svazek.
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
ms.date: 2/20/2019
ms.author: b-juche
ms.openlocfilehash: 634f23cf3161fff09f21c79fd8300cb269dcc5b7
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2019
ms.locfileid: "59546571"
---
# <a name="set-up-azure-netapp-files-and-create-a-volume"></a>Nastavení služby Azure NetApp Files a vytvoření svazku 

Tento článek ukazuje, jak rychle nastavit NetApp soubory Azure a vytvořte svazek. 

## <a name="before-you-begin"></a>Než začnete 

Musíte být součástí programu ve verzi Public Preview a pro přístup k poskytovateli prostředků Microsoft.NetApp na seznamu povolených. Podrobnosti o zapojení do programu verze Public Preview najdete na [registrační stránce Azure NetApp Files Public Preview](https://aka.ms/nfspublicpreview). 

## <a name="register-for-azure-netapp-files-and-netapp-resource-provider"></a>Zaregistrujte se soubory Azure NetApp a poskytovatele prostředků NetApp

1. Na webu Azure Portal klikněte na ikonu Azure Cloud Shell v pravém horním rohu.

      ![Ikona Azure Cloud Shell](../media/azure-netapp-files/azure-netapp-files-azure-cloud-shell.png)

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

## <a name="create-a-volume-for-azure-netapp-files"></a>Vytvoření svazku pro Azure NetApp Files

1. Z okna správy souborů NetApp Azure svého účtu NetApp, klikněte na tlačítko **svazky**.

    ![Klikněte na svazky](../media/azure-netapp-files/azure-netapp-files-click-volumes.png)  

2. Klikněte na **+ Přidat svazek**.

    ![Klikněte na tlačítko Přidat svazky](../media/azure-netapp-files/azure-netapp-files-click-add-volumes.png)  

3. V části Vytvoření svazku okna poskytují informace o svazku: 
   1. Zadejte **myvol1** jako název svazku. 
   2. Zadejte **myfilepath1** jako cesta k souboru, který se použije k vytvoření cesty exportu svazku.
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

4. Klikněte na **Zkontrolovat a vytvořit**.

    ![Zkontrolovat a vytvořit okno](../media/azure-netapp-files/azure-netapp-files-review-and-create-window.png)  

5. Projděte si informace o svazku a pak klikněte na **vytvořit**.  
    Vytvořený svazku se zobrazí v okně svazky.

    ![Vytvoření svazku](../media/azure-netapp-files/azure-netapp-files-create-volume-created.png)  

## <a name="next-steps"></a>Další postup  

* [Vysvětlení hierarchii úložiště souborů Azure NetApp](azure-netapp-files-understand-storage-hierarchy.md)
* [Správa svazků s použitím Azure souborů NetApp](azure-netapp-files-manage-volumes.md) 
