---
title: Přidání značek do testovacího prostředí v Azure DevTest Labs | Microsoft Docs
description: Naučte se vytvářet vlastní značky v Azure DevTest Labs a používat značky k kategorizaci prostředků. Můžete zobrazit všechny prostředky ve vašem předplatném, které mají značku.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: a4d2b328626f0d0f096082fa88bb170894ccf3e4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "88270729"
---
# <a name="add-tags-to-a-lab-in-azure-devtest-labs"></a>Přidání značek do testovacího prostředí v Azure DevTest Labs

Můžete vytvořit vlastní značky a použít je v prostředcích DevTest Labs k logickému kategorizaci prostředků. Později můžete rychle a snadno zobrazit všechny prostředky ve vašem předplatném, které mají tuto značku. Značky jsou užitečné v případě, že potřebujete organizovat prostředky pro účely fakturace nebo správy.

Mezi prostředky, které tyto značky podporují, patří

* Výpočetní virtuální počítače
* Síťové karty
* IP adresy
* Nástroje pro vyrovnávání zatížení
* Účty úložiště
* Spravované disky

Značky můžete použít při [vytváření testovacího prostředí](devtest-lab-create-lab.md) a později ho spravovat pomocí okna značky v části Konfigurace a nastavení.

Každá značka se skládá z dvojice **název**- / **hodnota** . Například je možné vytvořit značku s názvem *CostCenter* , který má hodnotu *34543*. Tato značka vám může pomáhat později identifikovat prostředky testovacího prostředí, které jsou Fakturovatelné do této konkrétní oblasti vaší organizace. Dostanete se k výběru názvů a hodnot, které mají smysl pro uspořádání vašeho předplatného.

## <a name="steps-to-manage-tags-in-an-existing-lab"></a>Postup pro správu značek v existujícím testovacím prostředí

1. Přihlaste se na [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. V případě potřeby vyberte **všechny služby** a v seznamu vyberte **DevTest Labs** . Vaše testovací prostředí se už možná zobrazuje na řídicím panelu v části **všechny prostředky**.
1. V seznamu cvičení vyberte testovací prostředí, ve kterém chcete přidat nebo spravovat značky.
1. V oblasti **Přehled** testovacího prostředí vyberte **Konfigurace a zásady**.

    ![Tlačítko Konfigurace a zásady](./media/devtest-lab-add-tag/devtestlab-config-and-policies.png)

1. Na levé straně v části **Spravovat** vyberte **značky**.
1. Pokud chcete pro toto testovací prostředí vytvořit novou značku, zadejte  / dvojici název-**hodnota** a vyberte **Uložit**. Můžete také vybrat existující značku ze seznamu pro zobrazení nebo správu prostředků přidružených k této značce.

    ![Správa značek](./media/devtest-lab-add-tag/devtestlab-manage-tags.png)

> [!NOTE]
> Značky vytvořené na úrovni testovacího prostředí jsou zaměřené na všechny Fakturovatelné prostředky, ve kterých se ve vašem předplatném prostředí vytočí. Například značky úrovně testovacího prostředí se přesměrují na základní výpočetní virtuální počítače testovacích prostředí. v kontextu služby cost Management můžete značky použít. Značky úrovně testovacího prostředí se zobrazí ve filtru značek pro správu nákladů.

## <a name="understanding-limitations-to-tags"></a>Porozumění omezením pro značky

Na značky se vztahují následující omezení:

* Každý prostředek nebo skupina prostředků může mít maximálně 15 párů název/hodnota značek. Toto omezení se vztahuje pouze na značky použité přímo na prostředek nebo skupinu prostředků. Skupina prostředků může obsahovat řadu prostředků, z nichž každý má 15 párů název/hodnota značek.
* Název značky je omezen na 512 znaků a hodnota značky je omezena na 256 znaků. Pro účty úložiště je název značky omezen na 128 znaků a hodnota značky je omezena na 256 znaků.
* Prostředky ve skupině prostředků nedědí značky použité na danou skupinu prostředků.

[Pomocí značek k uspořádání prostředků Azure](../azure-resource-manager/management/tag-resources.md) získáte více podrobností o používání značek v Azure, včetně způsobu správy značek pomocí PowerShellu nebo Azure CLI.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Další kroky
* Omezení a konvence v rámci předplatného můžete uplatnit pomocí přizpůsobených zásad. Zásada, kterou definujete, může vyžadovat, aby všechny prostředky měly hodnotu pro konkrétní značku. Další informace najdete v tématu [nastavení zásad a plánů](devtest-lab-set-lab-policy.md).
* Prozkoumejte [galerii šablon rychlého startu Azure Resource Manager DevTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates).
