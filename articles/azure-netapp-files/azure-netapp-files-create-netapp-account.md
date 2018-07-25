---
title: Vytvoření účtu NetApp pro přístup k Azure NetApp Files | Microsoft Docs
description: Popisuje způsob přístupu k Azure NetApp Files a vytvoření účtu NetApp, aby bylo možné nastavit fond kapacity a vytvořit svazek.
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
ms.topic: get-started-article
ms.date: 03/28/2018
ms.author: b-juche
ms.openlocfilehash: ad8cc550ce69e4dc4c19a569718fa873a65b3620
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/13/2018
ms.locfileid: "39010340"
---
# <a name="create-a-netapp-account"></a>Vytvoření účtu NetApp
Vytvoření účtu NetApp umožňuje nastavit fond kapacity a následně vytvořit svazek. K vytvoření nového účtu NetApp se používá okno Azure NetApp Files.

## <a name="before-you-begin"></a>Než začnete
Musíte být v seznamu povolených pro přístup k poskytovateli prostředků Azure Microsoft.NetApp a nakonfigurovaní pro použití služby Azure NetApp Files.  

[Přihlašovací stránka verze Public Preview služby Azure NetApp Files](https://aka.ms/nfspublicpreview) 

## <a name="steps"></a>Kroky 

1. V pozvánce verze Preview najděte adresu URL webu Azure Portal a přihlaste se k portálu. 
2.  Přejděte do okna Azure NetApp Files pomocí jedné z následujících metod:  
  * Vyhledejte **Azure NetApp Files** ve vyhledávacím poli webu Azure Portal.  
  * Klikněte na **Všechny služby** v navigaci a potom filtrujte Azure NetApp Files.  

  Okno Azure NetApp Files můžete označit jako oblíbené kliknutím na ikonu hvězdičky vedle něj. 

3. Kliknutím na **+ Přidat** vytvořte nový účet NetApp.  
  Zobrazí se okno Nový účet NetApp.  

4. Zadejte pro účet NetApp následující informace: 
  * **Název účtu**  
    Zadejte jedinečný název pro předplatné.
  *  **Předplatné**  
    Vyberte předplatné z vašich stávajících předplatných.
  * **Skupina prostředků**   
    Použijte existující skupinu prostředků, nebo vytvořte novou.
  * **Umístění**  
    Vyberte oblast, kde má být umístěný účet a jeho podřízené prostředky.  
    V současné době se služba Azure NetApp Files podporuje jenom v oblasti USA – východ.  

    ![Nový účet NetApp](../media/azure-netapp-files/azure-netapp-files-new-netapp-account.png)


5. Klikněte na možnost **Vytvořit**.     
  Vytvořený účet NetApp se teď zobrazí v okně Azure NetApp Files. 

## <a name="next-steps"></a>Další kroky  

1. [Nastavení fondu kapacity](azure-netapp-files-set-up-capacity-pool.md)
2. [Vytvoření svazku pro Azure NetApp Files](azure-netapp-files-create-volumes.md)
3. [Konfigurace zásad exportu pro svazek (volitelné)](azure-netapp-files-configure-export-policy.md)
