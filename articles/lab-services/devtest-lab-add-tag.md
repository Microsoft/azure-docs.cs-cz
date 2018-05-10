---
title: Přidat značky do testovacího prostředí v Azure DevTest Labs | Microsoft Docs
description: Zjistěte, jak přidat značky do testovacího prostředí v Azure DevTest Labs
services: devtest-lab,virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: dc5b327a-62e4-41bc-80ef-deb3c23d51b2
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 3d9a5b3c0ae0b6058d3e8ccf8cdb340bd1200edc
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="add-tags-to-a-lab-in-azure-devtest-labs"></a>Přidat značky do testovacího prostředí v Azure DevTest Labs

Můžete vytvořit vlastní značky a použít je pro vaše prostředky DevTest Labs logicky kategorizace vašich prostředků. Později můžete rychle a snadno zjistit všechny prostředky v rámci vašeho předplatného, které mají tuto značku. Značky jsou užitečné, pokud budete potřebovat k uspořádání prostředků pro fakturace nebo správu.

Prostředky, které jsou podporovány podle značky patří

* Výpočetní virtuální počítače
* Síťové karty
* IP adresy
* Nástroje pro vyrovnávání zatížení
* Účty úložiště
* Managed Disks

Můžete použít značky, kdy jste [vytvoření testovacího prostředí](devtest-lab-create-lab.md) a později je spravovat pomocí okna značky v části Konfigurace a nastavení.

Každé značky se skládá z **název**/**hodnotu** pár. Můžete například vytvořit značku s názvem *costcenter* , má hodnotu *34543*. Značky, jako je to může pomoct později identifikovat prostředky testovacího prostředí, které jsou fakturovatelné k této konkrétní oblasti vaší organizace. Získáte zvolte názvy a hodnoty, které dávají smysl pro způsob uspořádání vašeho předplatného.

## <a name="steps-to-manage-tags-in-an-existing-lab"></a>Kroky týkající se správy značky v existující testovacího prostředí

1. Přihlaste se k webu [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. V případě potřeby vyberte **všechny služby**a potom vyberte **DevTest Labs** ze seznamu. Vaše laboratoř může zobrazit již na řídicím panelu v části **všechny prostředky**.
1. Ze seznamu labs vyberte testovací prostředí, ve které chcete přidat nebo spravovat značky.  
1. V tomto prostředí **přehled** oblasti, vyberte **konfiguraci a zásady**.  

    ![Tlačítko konfiguraci a zásady](./media/devtest-lab-add-tag/devtestlab-config-and-policies.png)

1. Na levé straně v části **SPRAVOVAT**, vyberte **značky**.
1. Chcete-li vytvořit novou značku pro toto testovací prostředí, zadejte **název**/**hodnotu** spárujte a vyberte **Uložit**. Můžete také vybrat existující značku ze seznamu můžete zobrazit nebo spravovat prostředky spojené s touto značkou.

    ![Spravovat značky](./media/devtest-lab-add-tag/devtestlab-manage-tags.png)

## <a name="understanding-limitations-to-tags"></a>Principy omezení na základě značek

Na značky se vztahují následující omezení:

* Každý prostředek nebo skupina prostředků může mít maximálně 15 párů název/hodnota značek. Toto omezení se vztahuje pouze na značky použité přímo na prostředek nebo skupinu prostředků. Skupina prostředků může obsahovat řadu prostředků, z nichž každý má 15 párů název/hodnota značek. 
* Název značky je omezen na 512 znaků a hodnota značky je omezena na 256 znaků. Pro účty úložiště je název značky omezen na 128 znaků a hodnota značky je omezena na 256 znaků.
* Prostředky ve skupině prostředků nedědí značky použité na danou skupinu prostředků.

[Použití značek k uspořádání prostředků Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) obsahuje větší podrobnosti o použití značek v Azure, včetně toho, jak spravovat značky pomocí prostředí PowerShell nebo rozhraní příkazového řádku Azure.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Další postup
* Pomocí vlastních zásad, můžete použít omezení a pravidla týkající se vašeho předplatného. Zásady, které definujete může vyžadovat, že všechny prostředky obsahovat hodnotu pro konkrétní značku. Další informace najdete v tématu [nastavit zásady a plány](devtest-lab-set-lab-policy.md).
* Prozkoumejte [Galerie šablon DevTest Labs Azure Resource Manager QuickStart](https://github.com/Azure/azure-devtestlab/tree/master/Samples).
