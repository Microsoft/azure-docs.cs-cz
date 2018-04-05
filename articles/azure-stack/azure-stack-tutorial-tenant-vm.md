---
title: Virtuální počítače zpřístupnit uživatelům zásobník Azure | Microsoft Docs
description: Kurz a zpřístupnit virtuální počítače v Azure zásobníku
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/22/2018
ms.author: jeffgilb
ms.reviewer: ''
ms.custom: mvc
ms.openlocfilehash: af97f32736959f8ebf8f3c4fbca400d6b0c41f3e
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="make-virtual-machines-available-to-your-azure-stack-users"></a>Virtuální počítače zpřístupnit uživatelům Azure zásobníku
Jako správce cloudu Azure zásobníku můžete vytvořit nabídky, které vaši uživatelé (někdy označované jako klienty) můžete přihlásit k odběru. Pomocí svého předplatného, mohou uživatelé pak využívání služeb Azure zásobníku.

Tento článek ukazuje, jak vytvořit nabídku a pak otestovat. Pro test Přihlaste se k portálu jako uživatel, přihlášení k odběru na nabídku a pak vytvořte virtuální počítač na základě předplatného.

Co se dozvíte:

> [!div class="checklist"]
> * Vytvoření nabídky
> * Přidání image
> * Testování nabídky


V zásobníku Azure služby se doručují na uživatele, kteří používají odběry, nabídky a plány. Uživatelé se mohou přihlásit k více nabídky. Nabízí může mít jeden nebo více plánů a plány může mít jednu nebo více služeb.

![Odběry, nabídky a plány](media/azure-stack-key-features/image4.png)

Další informace najdete v tématu [klíčové funkce a koncepty v zásobníku Azure](azure-stack-key-features.md).

## <a name="create-an-offer"></a>Vytvoření nabídky

Teď můžete získat věcí připravená pro vaše uživatele. Při spuštění procesu, budete nejprve vyzváni k vytvoření nabídky, pak plánu a nakonec kvóty.

3. **Vytvoření nabídky**

   Nabídky jsou skupiny jeden nebo více plánů, která představují zprostředkovatelů pro uživatele se koupit nebo přihlášení k odběru.

   a. [Přihlaste se](azure-stack-connect-azure-stack.md) k portálu jako správce cloudu a pak klikněte na tlačítko **nový** > **nabízí + plány** > **nabízejí**.
   ![Nová nabídka](media/azure-stack-tutorial-tenant-vm/image01.png)

   b. V **nové nabízejí** část, vyplňte **zobrazovaný název** a **název prostředku**a poté vyberte nový nebo existující **skupiny prostředků**. Zobrazovaný název představuje popisný název nabídky. Operátor cloudu můžete vidět název prostředku. Je to název, který správci používají pro práci s nabídkou jako s prostředkem Azure Resource Manageru.

   ![Zobrazované jméno](media/azure-stack-tutorial-tenant-vm/image02.png)

   c. Klikněte na tlačítko **základní plány**a v **plán** klikněte na tlačítko **přidat** přidat nový plán do nabídky.

   ![Přidat plán](media/azure-stack-tutorial-tenant-vm/image03.png)

   d. V **nový plán** část, vyplňte **zobrazovaný název** a **název prostředku**. Zobrazovaný název je popisný název plánu, který uživatelé uvidí. Operátor cloudu můžete vidět název prostředku. Je název, který operátoři cloudu se používá pro práci s plánem jako prostředek Azure Resource Manager.

   ![Plánování zobrazovaný název](media/azure-stack-tutorial-tenant-vm/image04.png)

   e. Klikněte na tlačítko **služby**, vyberte **Microsoft.Compute**, **Microsoft.Network**, a **Microsoft.Storage**a potom klikněte na **Vyberte**.

   ![Plánování služby](media/azure-stack-tutorial-tenant-vm/image05.png)

   f. Klikněte na tlačítko **kvóty**a pak vyberte první služby, pro který chcete vytvořit kvótu. Kvóty IaaS postupujte podle těchto kroků pro služby výpočty, síť a úložiště.

   V tomto příkladu jsme nejprve vytvořit kvótu pro výpočetní služba. Vyberte v seznamu názvů **Microsoft.Compute** obor názvů a pak klikněte na **vytvořit nové kvóty**.
   
   ![Vytvořit novou kvótu](media/azure-stack-tutorial-tenant-vm/image06.png)

   g. Na **vytvořit kvótu** části, zadejte název pro kvótu a nastavte požadované parametry pro kvóty a klikněte na tlačítko **OK**.

   ![Název kvóty](media/azure-stack-tutorial-tenant-vm/image07.png)

   h. Teď pro **Microsoft.Compute**, vyberte kvótu, kterou jste vytvořili.

   ![Vyberte kvóty](media/azure-stack-tutorial-tenant-vm/image08.png)

   Opakujte tyto kroky u služby sítě a úložiště a pak klikněte na **OK** na **kvóty** části.

   i. Klikněte na tlačítko **OK** na **nový plán** části.

   j. Na **plán** vyberte nový plán a klikněte na tlačítko **vyberte**.

   k. Na **nové nabídky** klikněte na tlačítko **vytvořit**. Uvidíte oznámení po vytvoření nabídky.

   l. V nabídce řídícího panelu klikněte na tlačítko **nabízí** a pak klikněte na nabídku jste vytvořili.

   m. Klikněte na **Změnit stav** a potom na **Veřejné**.

   ![Veřejné stavu](media/azure-stack-tutorial-tenant-vm/image09.png)

## <a name="add-an-image"></a>Přidání image

Než zřídíte virtuální počítače, musíte přidat bitovou kopii do zásobníku Azure marketplace. Můžete přidat bitovou kopii podle vaší volby, včetně Linux bitové kopie z Azure Marketplace.

Pokud pracujete v případě připojené, a pokud vaší instanci Azure zásobníku jste zaregistrovali Azure, pak si můžete stáhnout bitovou kopii virtuálního počítače Windows serveru 2016 z Azure Marketplace pomocí kroků popsaných v [stažení marketplace položky z Azure do Azure zásobníku](azure-stack-download-azure-marketplace-item.md) tématu.

Informace o přidání různé položky do marketplace najdete v tématu [Azure Marketplace zásobníku](azure-stack-marketplace.md).

## <a name="test-the-offer"></a>Testování nabídky

Teď, když jste vytvořili nabídku, můžete ji otestovat. Přihlaste se jako uživatel a přihlášení k odběru na nabídku a pak přidejte virtuální počítač.

1. **Přihlášení k odběru nabídky**

   Nyní je můžete přihlásit k portálu jako uživatel k odběru na nabídku.

   a. Přihlaste se k portálu user portal jako uživatele a klikněte na tlačítko **získat předplatné**.
   - Pro integrovaný systém, se liší v závislosti na vaší operátor oblast a název domény externí adresu URL a bude ve formátu https://portal.&lt; *oblast*&gt;.&lt; *Plně kvalifikovaný název domény*&gt;.
   - Pokud používáte Development Kit Azure zásobníku, portál adresa je https://portal.local.azurestack.external.

   ![Získat předplatné](media/azure-stack-subscribe-plan-provision-vm/image01.png)

   b. V **zobrazovaný název** pole, zadejte název pro vaše předplatné, klikněte na **nabízejí**, klikněte na jednu z nabídky v **zvolte nabídku** části a pak klikněte na tlačítko  **Vytvoření**.

   ![Vytvoření nabídky](media/azure-stack-subscribe-plan-provision-vm/image02.png)

   c. Chcete-li zobrazit předplatné, které jste vytvořili, klikněte na tlačítko **další služby**, klikněte na tlačítko **odběry**, pak klikněte na nové předplatné.  

   Jakmile se přihlásíte k nabídku, aktualizujte portálu, abyste zjistili, služby, které jsou součástí nové předplatné.

2. **Zřízení virtuálního počítače**

   Nyní je můžete přihlásit k portálu jako uživatel ke zřízení virtuálního počítače pomocí předplatného. 

   a. Přihlaste se k portálu user portal jako uživatel.
      - Pro integrovaný systém, se liší v závislosti na vaší operátor oblast a název domény externí adresu URL a bude ve formátu https://portal.&lt; *oblast*&gt;.&lt; *Plně kvalifikovaný název domény*&gt;.
   - Pokud používáte Development Kit Azure zásobníku, portál adresa je https://portal.local.azurestack.external.

   b.  Na řídicím panelu klikněte na tlačítko **nový** > **výpočetní** > **Windows Server 2016 Datacenter Eval**a pak klikněte na tlačítko **vytvořit**.

   c. V **Základy** zadejte **název**, **uživatelské jméno**, a **heslo**, vyberte **předplatné**, vytvoření **skupiny prostředků** (nebo vyberte nějaký existující) a potom klikněte na **OK**.

   d. V **zvolte velikost** klikněte na tlačítko **A1 standardní**a potom klikněte na **vyberte**.  

   e. V **nastavení** klikněte na tlačítko **virtuální síť**. V **zvolte virtuální síť** klikněte na tlačítko **vytvořit nový**. V **vytvořit virtuální síť** části přijměte všechny výchozí hodnoty a klikněte na tlačítko **OK**. V **nastavení** klikněte na tlačítko **OK**.

   ![Vytvoření virtuální sítě](media/azure-stack-provision-vm/image04.png)

   f. V **Souhrn** klikněte na tlačítko **OK** k vytvoření virtuálního počítače.  

   g. Chcete-li zobrazit nový virtuální počítač, klikněte na tlačítko **všechny prostředky**, vyhledejte virtuální počítač a klikněte na jeho název.

    ![Všechny zdroje](media/azure-stack-provision-vm/image06.png)

Co jste se naučili v tomto kurzu:

> [!div class="checklist"]
> * Vytvoření nabídky
> * Přidání image
> * Testování nabídky

> [!div class="nextstepaction"]
> [Zpřístupnit webové, mobilní a aplikace API Azure zásobníku uživatelům](azure-stack-tutorial-app-service.md)