---
title: Přidání značek do testovacího prostředí ve službě Azure DevTest Labs | Dokumentace Microsoftu
description: Zjistěte, jak přidat značky do testovacího prostředí ve službě Azure DevTest Labs
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
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38299107"
---
# <a name="add-tags-to-a-lab-in-azure-devtest-labs"></a>Přidání značek do testovacího prostředí ve službě Azure DevTest Labs

Můžete vytvořit vlastní značky a použít je k vašim prostředkům DevTest Labs logicky kategorizovat prostředky. Později můžete rychle a snadno zobrazit všechny prostředky ve vašem předplatném, které mají konkrétní značkou. Značky jsou užitečné, když budete potřebovat k uspořádání prostředků pro účely fakturace nebo správy.

Zahrnout prostředky, které jsou podporovány podle klíčových slov

* Výpočetní virtuální počítače
* Síťové karty
* IP adresy
* Nástroje pro vyrovnávání zatížení
* Účty úložiště
* Managed Disks

Můžete použít značky, kdy jste [vytvoření testovacího prostředí](devtest-lab-create-lab.md) a později je můžete spravovat prostřednictvím okna značky v části Konfigurace a nastavení.

Každá značka se skládá z **název**/**hodnotu** pár. Můžete například vytvořit značku s názvem *nákladové středisko* , který má hodnotu *34543*. Klíčové slovo, jako jste to může být později užitečný Identifikujte prostředky testovacího prostředí, které se účtují této konkrétní oblasti vaší organizace. Získáte zvolte názvy a hodnoty, které dávají smysl pro způsob uspořádání vašeho předplatného.

## <a name="steps-to-manage-tags-in-an-existing-lab"></a>Kroky týkající se správy klíčových slov do existující testovací prostředí

1. Přihlaste se k webu [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. V případě potřeby vyberte **všechny služby**a pak vyberte **DevTest Labs** ze seznamu. Vaše testovací prostředí může již zobrazený na řídicím panelu v části **všechny prostředky**.
1. V seznamu testovacích prostředí vyberte prostředí, ve které chcete přidat nebo správě značek.  
1. Cvičení **přehled** vyberte **konfigurace a zásad**.  

    ![Konfigurace a zásad tlačítko](./media/devtest-lab-add-tag/devtestlab-config-and-policies.png)

1. Na levé straně v části **SPRAVOVAT**vyberte **značky**.
1. Pokud chcete vytvořit novou značku pro toto testovací prostředí, zadejte **název**/**hodnotu** spárujte a vyberte **Uložit**. V seznamu zobrazovat a spravovat prostředky spojené s určitou značkou, můžete také vybrat existující značky.

    ![Spravovat značky](./media/devtest-lab-add-tag/devtestlab-manage-tags.png)

## <a name="understanding-limitations-to-tags"></a>Principy omezení se značkami

Na značky se vztahují následující omezení:

* Každý prostředek nebo skupina prostředků může mít maximálně 15 párů název/hodnota značek. Toto omezení se vztahuje pouze na značky použité přímo na prostředek nebo skupinu prostředků. Skupina prostředků může obsahovat řadu prostředků, z nichž každý má 15 párů název/hodnota značek. 
* Název značky je omezen na 512 znaků a hodnota značky je omezena na 256 znaků. Pro účty úložiště je název značky omezen na 128 znaků a hodnota značky je omezena na 256 znaků.
* Prostředky ve skupině prostředků nedědí značky použité na danou skupinu prostředků.

[Použití značek k uspořádání prostředků Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) obsahuje větší podrobnosti o použití značek v Azure, včetně způsobu správy značky pomocí Powershellu nebo rozhraní příkazového řádku Azure.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Další postup
* Pomocí vlastních zásad, můžete použít omezení a pravidla týkající se vašeho předplatného. Zásady, které definujete může být nutné, aby všechny prostředky hodnotu pro konkrétní značku. Další informace najdete v tématu [nastavení zásad a plánů](devtest-lab-set-lab-policy.md).
* Prozkoumejte [Galerie šablon QuickStart pro Azure Resource Manageru DevTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/Samples).
