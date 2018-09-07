---
title: Virtuální počítače zpřístupnit uživatelům Azure stacku | Dokumentace Microsoftu
description: Zjistěte, jak zajistit dostupnost virtuálních počítačů ve službě Azure Stack
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
ms.date: 09/05/2018
ms.author: jeffgilb
ms.reviewer: ''
ms.custom: mvc
ms.openlocfilehash: 09b9126125006fb70f5e2560f04b815b4a874405
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/06/2018
ms.locfileid: "44027295"
---
# <a name="tutorial-make-virtual-machines-available-to-your-azure-stack-users"></a>Kurz: zpřístupníte virtuálních počítačů pro vaše uživatele Azure stacku

Jako správce cloudu Azure Stack můžete vytvořit nabídek, které vaši uživatelé (někdy označované jako tenantů) můžete přihlásit k odběru. Se přihlásíte k odběru nabídky, uživatelé můžou využívat služby Azure Stack, které poskytuje v rámci nabídky.

Tento kurz ukazuje, jak vytvořit nabídku pro virtuální počítač a pak se přihlaste jako uživatel k otestování nabídky.

Co se naučíte:

> [!div class="checklist"]
> * Vytvoření nabídky
> * Přidání obrázku
> * Test nabídky

Ve službě Azure Stack se doručí uživatele, kteří používají předplatných, nabídky a plány služeb. Uživatelé se mohou přihlásit k několika nabídky. Nabídka může mít jednoho nebo několika plánů a plán může mít jednu nebo víc služeb.

![Odběry, nabídky a plány](media/azure-stack-key-features/image4.png)

Další informace najdete v tématu [klíčových funkcích a konceptech v Azure stacku](azure-stack-key-features.md).

## <a name="create-an-offer"></a>Vytvoření nabídky

Nabídky jsou skupiny jednoho nebo několika plánů, které poskytovatelé předkládají uživatelům, aby si koupí nebo přihlášení k odběru. Proces vytváření nabídka má několik kroků. Nejprve budete vyzváni k vytvoření nabídky, pak plán a nakonec kvóty.

1. [Přihlaste se](azure-stack-connect-azure-stack.md) k portálu jako správce cloudu a pak vyberte **nový** > **nabízí + plány** > **nabízejí**.

   ![Nová nabídka](media/azure-stack-tutorial-tenant-vm/image01.png)

1. V **nová nabídka**, zadejte **zobrazovaný název** a **název prostředku**a pak vyberte novou nebo existující **skupiny prostředků**. Zobrazovaný název představuje popisný název nabídky. Pouze operátor cloudu můžete zobrazit název prostředku. Je to název, který správci používají pro práci s nabídkou jako s prostředkem Azure Resource Manageru.

   ![Zobrazované jméno](media/azure-stack-tutorial-tenant-vm/image02.png)

1. Vyberte **základní plány**a **plán** vyberte **přidat** pro přidání do nabídky nový plán.

   ![Přidat plán](media/azure-stack-tutorial-tenant-vm/image03.png)

1. V **nový plán** části, vyplňte **zobrazovaný název** a **název prostředku**. Zobrazovaný název je popisný název plánu, který uživatelé uvidí. Pouze operátor cloudu můžete zobrazit název prostředku. Jedná se o název, operátorům cloudu použít pro práci s plánem jako s prostředkem Azure Resource Manageru.

   ![Zobrazovaný název plánu](media/azure-stack-tutorial-tenant-vm/image04.png)

1. Vyberte **služby**. Ze seznamu služeb vyberte **Microsoft.Compute**, **Microsoft.Network**, a **Microsoft.Storage**. Zvolte **vyberte** na tyto služby přidat do plánu.

   ![Plán služby](media/azure-stack-tutorial-tenant-vm/image05.png)

1. Vyberte **kvóty**a pak vyberte první, který chcete vytvořit kvótu pro službu. U kvóty IaaS použijte jako vodítko pro konfiguraci kvót pro služby Compute, Network a Storage v následujícím příkladu.

   - Nejprve vytvořte kvótou pro službu Compute. Vyberte v seznamu názvů **Microsoft.Compute** a pak vyberte **vytvořit novou kvótu**.

     ![Vytvořit novou kvótu](media/azure-stack-tutorial-tenant-vm/image06.png)

   - V **vytvořit kvótu**, zadejte název této kvóty. Můžete změnit nebo přijmout všechny hodnoty kvóty, které jsou uvedené u kvóty, které vytváříte. V tomto příkladu jsme přijměte výchozí nastavení a vyberte **OK**.

     ![Název kvóty](media/azure-stack-tutorial-tenant-vm/image07.png)

   - Vyberte si **Microsoft.Compute** v seznamu oborů názvů a pak vyberte kvótu, kterou jste vytvořili. Toto odkazuje na službu Compute kvóta.

     ![Vyberte kvótu](media/azure-stack-tutorial-tenant-vm/image08.png)

      Tento postup opakujte pro služby sítě a úložiště. Jakmile budete hotovi, vyberte **OK** v **kvóty** uložit všechny kvóty.

1. V **nový plán**vyberte **OK**.

1. V části **plán**, vyberte nový plán a pak **vyberte**.

1. V **nová nabídka**vyberte **vytvořit**. Při vytváření nabídky, zobrazí se oznámení.

1. V nabídce řídicího panelu, vyberte **nabízí** a potom si vyberte vámi vytvořená nabídka.

1. Vyberte **změnit stav**a pak vyberte **veřejné**.

    ![Veřejné stavu](media/azure-stack-tutorial-tenant-vm/image09.png)

## <a name="add-an-image"></a>Přidání obrázku

Než můžete poskytovat virtuální počítače, musíte přidat bitovou kopii do Tržiště Azure Stack. Můžete přidat bitovou kopii podle vašeho výběru, včetně imagí Linuxu na Azure Marketplace.

Pokud pracujete ve scénáři připojené, a Pokud zaregistrujete vaší instance služby Azure Stack s Azure, pak si můžete stáhnout image virtuálního počítače s Windows serverem 2016 z Azure Marketplace pomocí kroků popsaných v [stahování marketplace položky z Azure do služby Azure Stack](azure-stack-download-azure-marketplace-item.md) tématu.

Informace o přidávání různé položky na webu Marketplace najdete v tématu [Azure Marketplace zásobníku](azure-stack-marketplace.md).

## <a name="test-the-offer"></a>Test nabídky

Teď, když jste vytvořili v rámci nabídky, takže ji můžete otestovat. Se přihlaste jako uživatel, předplacení nabídky a pak přidat virtuální počítač.

1. **Přihlášení k odběru nabídky**

   a. Přihlaste se k portálu user portal pomocí uživatelského účtu a vyberte **pořiďte si předplatné** dlaždici.
   - Integrovaný systém, adresa URL se liší v závislosti na oblasti vaší operátor a název domény externího a bude mít tento formát https://portal.&lt; *oblast*&gt;.&lt; *Plně kvalifikovaný název domény*&gt;.
   - Pokud používáte Azure Stack Development Kit, adresy portálu je https://portal.local.azurestack.external.

   ![Získat předplatné](media/azure-stack-subscribe-plan-provision-vm/image01.png)

   b. V **pořiďte si předplatné**, zadejte název pro vaše předplatné na **zobrazovaný název** pole. Vyberte **nabízejí**a pak vyberte jednu z nabídek v **zvolit nabídku** seznamu. Vyberte **Vytvořit**.

   ![Vytvoření nabídky](media/azure-stack-subscribe-plan-provision-vm/image02.png)

   c. Chcete-li zobrazit předplatné, vyberte **všechny služby**a potom v části **Obecné** vyberte kategorii **předplatná**. Vyberte své nové předplatné služby, které jsou součástí předplatného.

   >[!NOTE]
   >Jakmile se přihlásíte k odběru nabídky, budete muset aktualizovat portálu, abyste zjistili, které služby jsou součástí nového předplatného.

1. **Zřízení virtuálního počítače**

   Na portálu user portal můžete zřídit virtuální počítač pomocí tohoto nového předplatného.

   a. Přihlaste se k portálu user portal pomocí uživatelského účtu.
      - Integrovaný systém, adresa URL se liší v závislosti na oblasti vaší operátor a název domény externího a bude mít tento formát https://portal.&lt; *oblast*&gt;.&lt; *Plně kvalifikovaný název domény*&gt;.
   - Pokud používáte Azure Stack Development Kit, adresy portálu je https://portal.local.azurestack.external.

   b.  Na řídicím panelu, vyberte **nový** > **Compute** > **systému Windows Server 2016 Datacenter Eval**a pak vyberte **vytvořit**.

   c. V **Základy**, zadejte následující informace:
      - Zadejte **název**
      - Zadejte **uživatelské jméno**
      - Zadejte **heslo**
      - Zvolte **předplatného**
      - Vytvoření **skupiny prostředků** (nebo vyberte existující.) 
      - Vyberte **OK** tyto informace uložit.

   d. V **zvolte velikost**vyberte **A1 Standard**a potom **vyberte**.  

   e. V **nastavení**vyberte **virtuální síť**.

   f. V **zvolte virtuální síť**vyberte **vytvořit nový**.

   g. V **vytvořit virtuální síť**přijměte všechny výchozí hodnoty a vyberte **OK**.

   h. Vyberte **OK** v **nastavení** uložte konfiguraci sítě.

   ![Vytvoření virtuální sítě](media/azure-stack-provision-vm/image04.png)

   i. V **Souhrn**vyberte **OK** k vytvoření virtuálního počítače.  

   j. Pokud chcete zobrazit nový virtuální počítač, vyberte **všechny prostředky**. Vyhledejte virtuální počítač a vyberte jeho název ve výsledcích hledání.

   ![Všechny zdroje](media/azure-stack-provision-vm/image06.png)

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvoření nabídky
> * Přidání obrázku
> * Test nabídky

Pokračujte k dalším kurzu se dozvíte, jak:
> [!div class="nextstepaction"]
> [Zpřístupnění databáze SQL pro vaše uživatele Azure stacku](azure-stack-tutorial-sql-server.md)