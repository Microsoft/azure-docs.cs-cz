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
ms.openlocfilehash: eda5f21a535c6256d534bfdaa6cf30ae710ba075
ms.sourcegitcommit: b254db346732b64678419db428fd9eb200f3c3c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/14/2018
ms.locfileid: "53413179"
---
# <a name="create-a-netapp-account"></a>Vytvoření účtu NetApp
Vytvoření účtu NetApp umožňuje nastavit fond kapacity a následně vytvořit svazek. K vytvoření nového účtu NetApp se používá okno Azure NetApp Files.

## <a name="before-you-begin"></a>Před zahájením
Musí mít zaregistrovaný předplatné poskytovatele prostředků NetApp a funkce verze public preview.

[Registrace pro soubory Azure NetApp](azure-netapp-files-register.md)

## <a name="steps"></a>Kroky 

1. Verze preview webu Azure portal vyhledejte adresu URL z pozvánku ve verzi preview a přihlaste se k portálu. 
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

## <a name="next-steps"></a>Další postup  

[Nastavení fondu kapacity](azure-netapp-files-set-up-capacity-pool.md)

