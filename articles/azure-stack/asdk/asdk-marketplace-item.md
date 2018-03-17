---
title: "Přidání položky zásobníku Azure marketplace z Azure | Microsoft Docs"
description: "Popisuje postup přidání image virtuálního počítače Windows serveru na základě Azure do Azure Marketplace zásobníku."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 25f179f825526e20377c0e94ccb38a788cadd898
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/17/2018
---
# <a name="tutorial-add-an-azure-stack-marketplace-item-from-azure"></a>Kurz: Přidání položky zásobníku Azure marketplace z Azure

Jako operátor zásobník Azure je první věcí, kterou je potřeba udělat, aby mohli uživatelé nasadit virtuální počítač (VM) přidat bitovou kopii virtuálního počítače do zásobníku Azure marketplace. Ve výchozím nastavení, nic je publikovaná v zásobníku Azure marketplace, ale můžete stáhnout položky z [kurátorované seznam položek Azure marketplace](.\.\azure-stack-marketplace-azure-items.md) který byl předem otestované ke spuštění v Azure zásobníku. Tuto možnost použijte, pokud pracujete v připojených scénář a zaregistrovali vaší instanci Azure zásobník Azure.

V tomto kurzu přidáte bitovou kopii virtuálního počítače Windows serveru 2016 z Azure marketplace zásobník Azure marketplace.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Přidat položku zásobníku systému Windows Server virtuálních počítačů Azure marketplace.
> * Ověřte, zda že je k dispozici bitovou kopii virtuálního počítače 
> * Testování image virtuálního počítače

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

- Nainstalujte [modulů prostředí Azure PowerShell kompatibilní s Azure zásobníku](asdk-post-deploy.md#install-azure-stack-powershell)
- Stažení [nástroje Azure zásobníku](asdk-post-deploy.md#download-the-azure-stack-tools)
- [Zaregistrovat ASDK](asdk-register.md) s předplatným Azure

## <a name="add-a-windows-server-vm-image"></a>Přidání bitové kopie virtuálního počítače Windows serveru
Stáhnout bitovou kopii z Azure marketplace můžete přidat bitovou kopii systému Windows Server 2016 do zásobníku Azure marketplace. Tuto možnost použijte, pokud pracujete v připojených scénář a již máte [zaregistrován vaší instanci Azure zásobníku](asdk-register.md) s Azure.

1. Přihlaste se k [portál pro správu ASDK](https://adminportal.local.azurestack.external).

2. Vyberte **další služby** > **Marketplace správu** > **přidat z Azure**. 

   ![Přidat z Azure](media/asdk-marketplace-item/azs-marketplace.png)

3. Hledání nebo nalezení **Windows Server 2016 Datacenter** bitové kopie.

4. Vyberte **Windows Server 2016 Datacenter** bitovou kopii a potom vyberte **Stáhnout**.

   ![Stáhnout bitovou kopii z Azure](media/asdk-marketplace-item/azure-marketplace-ws2016.png)


> [!NOTE]
> Jak dlouho trvá přibližně jednu hodinu a stažení image virtuálního počítače jej publikujte do zásobníku Azure marketplace. 

Po dokončení stahování bitovou kopii bude publikována a k dispozici v části **Marketplace správu**. Bitová kopie je také k dispozici v části **výpočetní** pro vytvoření nového virtuálního počítače.

## <a name="verify-the-marketplace-item-is-available"></a>Ověřte, zda že je k dispozici položku marketplace.
Tyto kroky použijte k ověření, že nová bitová kopie virtuálního počítače je k dispozici v zásobníku Azure marketplace.

1. Přihlaste se k [portál pro správu ASDK](https://adminportal.local.azurestack.external).

2. Vyberte **další služby** > **Marketplace správu**. 

3. Ověřte, že bitovou kopii virtuálního počítače Windows serveru 2016 datového centra byl úspěšně přidán.

   ![Stažený bitovou kopii z Azure](media/asdk-marketplace-item/azs-marketplace-ws2016.png)

## <a name="test-the-vm-image"></a>Testování image virtuálního počítače
Jako operátor zásobník Azure, můžete použít [portálu správce](https://adminportal.local.azurestack.external) vytvořit testovací virtuální počítač k ověření bitovou kopii byl zpřístupněn úspěšně. 

1. Přihlaste se k [portál pro správu ASDK](https://adminportal.local.azurestack.external).

2. Klikněte na tlačítko **nové** > **výpočetní** > **systému Windows Server 2016 Datacenter** > **vytvořit**.  
 ![Vytvoření virtuálního počítače image pořízené na marketplace](media/asdk-marketplace-item/new-compute.png)

3. V **Základy** okno, zadejte následující informace a potom klikněte na **OK**:
  - **Název**: test-vm-1
  - **Uživatelské jméno**: AdminTestUser
  - **Heslo**: AzS TestVM01
  - **Předplatné**: přijměte výchozí zprostředkovatel předplatné
  - **Skupina prostředků**: test-vm-rg
  - **Umístění**: místní

4. V **zvolte velikost** okně klikněte na tlačítko **A1 standardní**a potom klikněte na **vyberte**.  

5. V **nastavení** , přijměte výchozí hodnoty a klikněte na tlačítko **OK**

6. V okně **Shrnutí** klikněte na **OK** a vytvořte virtuální počítač.  
> [!NOTE]
> Nasazení virtuálního počítače trvá několik minut.

7. Chcete-li zobrazit na nový virtuální počítač, klikněte na tlačítko **virtuální počítače**, vyhledejte **test-vm-1** a klikněte na jeho název.
    ![Zobrazí první image virtuálního počítače testu](media/asdk-marketplace-item/first-test-vm.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků
Poté, co jste úspěšně přihlášení k virtuálnímu počítači a ověřit, že bitovou kopii test funguje správně, byste měli odstranit testovací skupina prostředků. Uvolní se omezené prostředky k dispozici pro jeden uzel ASDK instalace.

Pokud již nepotřebujete, odstraňte skupinu prostředků, virtuální počítač a všechny související prostředky pomocí následujících kroků:

1. Přihlaste se k [portál pro správu ASDK](https://adminportal.local.azurestack.external).
2. Klikněte na tlačítko **skupiny prostředků** > **test-vm-rg** > **odstranit skupinu prostředků**.
3. Typ **test-vm-rg** jako název skupiny prostředků a pak klikněte na tlačítko **odstranit**.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Přidat položku zásobníku systému Windows Server virtuálních počítačů Azure marketplace.
> * Ověřte, zda že je k dispozici bitovou kopii virtuálního počítače 
> * Testování image virtuálního počítače

Přechodu na v dalším kurzu se dozvíte, jak vytvoření nabídky Azure zásobníku a plán.

> [!div class="nextstepaction"]
> [Nabízejí služby Azure IaaS zásobníku](asdk-offer-services.md)