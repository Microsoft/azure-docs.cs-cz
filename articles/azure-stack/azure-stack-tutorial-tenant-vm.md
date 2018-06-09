---
title: Virtuální počítače zpřístupnit uživatelům zásobník Azure | Microsoft Docs
description: Zjistěte, jak zpřístupnit virtuální počítače v Azure zásobníku
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
ms.date: 06/07/2018
ms.author: jeffgilb
ms.reviewer: ''
ms.custom: mvc
ms.openlocfilehash: 9329cb0dbfa24cf239b820573ef7f642cdca9103
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/08/2018
ms.locfileid: "35248155"
---
# <a name="tutorial-make-virtual-machines-available-to-your-azure-stack-users"></a>Kurz: zpřístupníte virtuální počítače uživatelům Azure zásobníku

Jako správce cloudu Azure zásobníku můžete vytvořit nabídky, které vaši uživatelé (někdy označované jako klienty) můžete přihlásit k odběru. Se přihlásíte k odběru nabídku, můžete využívat uživatelé zásobník Azure služby, které poskytuje nabídku.

Tento kurz ukazuje, jak vytvořit nabídku pro virtuální počítač a potom se přihlaste jako uživatel k testování nabídku.

Co se dozvíte:

> [!div class="checklist"]
> * Vytvoření nabídky
> * Přidání image
> * Testování nabídky

V zásobníku Azure služby se doručují na uživatele, kteří používají odběry, nabídky a plány. Uživatelé se mohou přihlásit k více nabídky. Nabídku může mít jeden nebo více plánů a plán může mít jednu nebo více služeb.

![Odběry, nabídky a plány](media/azure-stack-key-features/image4.png)

Další informace najdete v tématu [klíčové funkce a koncepty v zásobníku Azure](azure-stack-key-features.md).

## <a name="create-an-offer"></a>Vytvoření nabídky

Nabídky jsou skupiny jeden nebo více plánů, která představují zprostředkovatelů pro uživatele se koupit nebo přihlášení k odběru. Proces vytváření nabídku má několik kroků. Nejprve se zobrazí výzva k vytvoření nabídky, pak plánu a nakonec kvóty.

1. [Přihlaste se](azure-stack-connect-azure-stack.md) k portálu jako správce cloudu a potom vyberte **nový** > **nabízí + plány** > **nabízejí**.

   ![Nová nabídka](media/azure-stack-tutorial-tenant-vm/image01.png)

2. V **nové nabízejí**, zadejte **zobrazovaný název** a **název prostředku**a poté vyberte nový nebo existující **skupiny prostředků**. Zobrazovaný název představuje popisný název nabídky. Operátor cloudu můžete vidět název prostředku. Je to název, který správci používají pro práci s nabídkou jako s prostředkem Azure Resource Manageru.

   ![Zobrazované jméno](media/azure-stack-tutorial-tenant-vm/image02.png)

3. Vyberte **základní plány**a v **plán** vyberte **přidat** přidat nový plán do nabídky.

   ![Přidat plán](media/azure-stack-tutorial-tenant-vm/image03.png)

4. V **nový plán** část, vyplňte **zobrazovaný název** a **název prostředku**. Zobrazovaný název je popisný název plánu, který uživatelé uvidí. Operátor cloudu můžete vidět název prostředku. Je název, který operátoři cloudu se používá pro práci s plánem jako prostředek Azure Resource Manager.

   ![Plánování zobrazovaný název](media/azure-stack-tutorial-tenant-vm/image04.png)

5. Vyberte **služby**. V seznamu služeb vyberte **Microsoft.Compute**, **Microsoft.Network**, a **Microsoft.Storage**. Zvolte **vyberte** přidat tyto služby s plánem.

   ![Plánování služby](media/azure-stack-tutorial-tenant-vm/image05.png)

6. Vyberte **kvóty**a pak vyberte první službou, kterou chcete vytvořit kvótu pro. Pro IaaS kvótu použijte jako vodítko pro konfiguraci kvóty pro služby výpočty, síť a úložiště v následujícím příkladu.

   - Nejprve vytvořte kvótu pro výpočetní služba. Vyberte v seznamu názvů **Microsoft.Compute** a pak vyberte **vytvořit nové kvóty**.

     ![Vytvořit novou kvótu](media/azure-stack-tutorial-tenant-vm/image06.png)

   - V **vytvořit kvótu**, zadejte název pro kvótu. Můžete změnit nebo přijmout všechny hodnoty kvóty, které jsou zobrazeny pro kvótu, kterou vytváříte. V tomto příkladu jsme přijměte výchozí nastavení a vyberte **OK**.

     ![Název kvóty](media/azure-stack-tutorial-tenant-vm/image07.png)

   - Vyberte **Microsoft.Compute** v seznamu názvů a potom vyberte kvótu, kterou jste vytvořili. Toto odkazuje kvóty na výpočetní služba.

     ![Vyberte kvóty](media/azure-stack-tutorial-tenant-vm/image08.png)

      Opakujte tyto kroky u služby sítě a úložiště. Jakmile budete hotovi, vyberte **OK** v **kvóty** uložit všechny kvóty.

7. V **nový plán**, vyberte **OK**.

8. V části **plán**, vyberte nový plán a potom **vyberte**.

9. V **nové nabídky**, vyberte **vytvořit**. Oznámení se zobrazí, když je vytvořena nabídku.

10. V nabídce řídicí panel, vyberte **nabízí** a pak vyberte nabídku jste vytvořili.

11. Vyberte **změny stavu**a pak zvolili **veřejné**.

    ![Veřejné stavu](media/azure-stack-tutorial-tenant-vm/image09.png)

## <a name="add-an-image"></a>Přidání image

Než zřídíte virtuální počítače, musíte přidat bitovou kopii do zásobníku Azure marketplace. Můžete přidat bitovou kopii podle vaší volby, včetně Linux bitové kopie z Azure Marketplace.

Pokud pracujete v případě připojené, a pokud vaší instanci Azure zásobníku jste zaregistrovali Azure, pak si můžete stáhnout bitovou kopii virtuálního počítače Windows serveru 2016 z Azure Marketplace pomocí kroků popsaných v [stažení marketplace položky z Azure do Azure zásobníku](azure-stack-download-azure-marketplace-item.md) tématu.

Informace o přidání různé položky do marketplace najdete v tématu [Azure Marketplace zásobníku](azure-stack-marketplace.md).

## <a name="test-the-offer"></a>Testování nabídky

Teď, když jste vytvořili nabídku, můžete ji otestovat. Budete přihlášení jako uživatel, přihlášení k odběru na nabídku a pak přidat virtuální počítač.

1. **Přihlášení k odběru nabídky**

   a. Přihlaste se k portálu pro uživatele s uživatelským účtem a vyberte **získat předplatné** dlaždici.
   - Pro integrovaný systém, se liší v závislosti na vaší operátor oblast a název domény externí adresu URL a bude ve formátu https://portal.&lt; *oblast*&gt;.&lt; *Plně kvalifikovaný název domény*&gt;.
   - Pokud používáte Development Kit Azure zásobníku, portál adresa je https://portal.local.azurestack.external.

   ![Získat předplatné](media/azure-stack-subscribe-plan-provision-vm/image01.png)

   b. V **získat předplatné**, zadejte název pro vaše předplatné v **zobrazovaný název** pole. Vyberte **nabízejí**a pak vyberte jednu z nabídky v **zvolte nabídku** seznamu. Vyberte **Vytvořit**.

   ![Vytvoření nabídky](media/azure-stack-subscribe-plan-provision-vm/image02.png)

   c. Chcete-li zobrazit předplatné, vyberte **další služby**a potom vyberte **odběry**. Vyberte nové předplatné zobrazíte služby, které jsou součástí předplatného.

   >[!NOTE]
   >Jakmile se přihlásíte k nabídku, můžete chtít aktualizovat na portálu najdete v části služby, které jsou součástí nové předplatné.

2. **Zřízení virtuálního počítače**

   Z portálu user portal můžete zřídit virtuální počítač pomocí nové předplatné.

   a. Přihlaste se k portálu user portal pomocí uživatelského účtu.
      - Pro integrovaný systém, se liší v závislosti na vaší operátor oblast a název domény externí adresu URL a bude ve formátu https://portal.&lt; *oblast*&gt;.&lt; *Plně kvalifikovaný název domény*&gt;.
   - Pokud používáte Development Kit Azure zásobníku, portál adresa je https://portal.local.azurestack.external.

   b.  Na řídicím panelu, vyberte **nový** > **výpočetní** > **Windows Server 2016 Datacenter Eval**a potom vyberte **vytvořit**.

   c. V **Základy**, zadejte následující informace:
      - Zadejte **název**
      - Zadejte **uživatelské jméno**
      - Zadejte **heslo**
      - Vyberte **předplatného**
      - Vytvoření **skupiny prostředků** (nebo vyberte nějaký existující.) 
      - Vyberte **OK** uložit tyto informace.

   d. V **zvolte velikost**, vyberte **A1 standardní**a potom **vyberte**.  

   e. V **nastavení**, vyberte **virtuální síť**.

   f. V **zvolte virtuální síť**, vyberte **vytvořit nový**.

   g. V **vytvořit virtuální síť**přijměte všechny výchozí hodnoty a vyberte **OK**.

   h. Vyberte **OK** v **nastavení** uložit konfiguraci sítě.

   ![Vytvoření virtuální sítě](media/azure-stack-provision-vm/image04.png)

   i. V **Souhrn**, vyberte **OK** k vytvoření virtuálního počítače.  

   j. Pokud chcete zobrazit nový virtuální počítač, vyberte **všechny prostředky**. Vyhledávání pro virtuální počítač a vyberte jeho název ve výsledcích hledání.

   ![Všechny zdroje](media/azure-stack-provision-vm/image06.png)

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvoření nabídky
> * Přidání image
> * Testování nabídky

Přechod na další kurzu se dozvíte, jak:
> [!div class="nextstepaction"]
> [Databáze SQL zpřístupnit uživatelům Azure zásobníku](azure-stack-tutorial-sql-server.md)