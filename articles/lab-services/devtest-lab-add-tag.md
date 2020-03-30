---
title: Přidání značek do testovacího prostředí v laboratořích Azure DevTest Labs | Dokumenty společnosti Microsoft
description: Přečtěte si, jak přidat značku do testovacího prostředí v Azure DevTest Labs
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
ms.date: 10/13/2019
ms.author: spelluru
ms.openlocfilehash: 1d7175d000eebd3f68665e7f097bbe3f68025bc8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72302800"
---
# <a name="add-tags-to-a-lab-in-azure-devtest-labs"></a>Přidání značek do testovacího prostředí v laboratořích Azure DevTest Labs

Můžete vytvořit vlastní značky a použít je na vaše prostředky DevTest Labs logicky kategorizovat své prostředky. Později můžete rychle a snadno zobrazit všechny prostředky v předplatném, které mají tuto značku. Značky jsou užitečné, když potřebujete uspořádat prostředky pro fakturaci nebo správu.

Mezi prostředky podporované značkami patří

* Výpočetní virtuální počítače
* Síťové karty
* IP adresy
* Nástroje pro vyrovnávání zatížení
* Účty úložiště
* Spravované disky

Značky můžete použít při [vytváření testovacího prostředí](devtest-lab-create-lab.md) a později je spravovat pomocí okna Značky v části Konfigurace a nastavení.

Každá značka se skládá z dvojice**hodnot** **názvu.**/ Můžete například vytvořit značku s názvem *costcenter,* která má hodnotu *34543*. Značka, jako je tato, vám může pomoci později identifikovat laboratorní prostředky, které jsou fakturovatelné do této konkrétní oblasti vaší organizace. Můžete si vybrat názvy a hodnoty, které mají smysl pro uspořádání předplatného.

## <a name="steps-to-manage-tags-in-an-existing-lab"></a>Postup správy značek v existujícím testovacím prostředí

1. Přihlaste se k [portálu Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. V případě potřeby vyberte **všechny služby**a ze seznamu vyberte **DevTest Labs.** Vaše testovací prostředí se už může zobrazit na řídicím panelu v části **Všechny prostředky**.
1. Ze seznamu testovacích prostředí vyberte testovací prostředí, ve kterém chcete přidat nebo spravovat značky.
1. V oblasti **Přehled** testovacího prostředí vyberte **Konfigurace a zásady**.

    ![Tlačítko Konfigurace a zásady](./media/devtest-lab-add-tag/devtestlab-config-and-policies.png)

1. Vlevo v části **MANAGE**vyberte **Tagy**.
1. Chcete-li vytvořit novou značku pro toto testovací prostředí, zadejte**dvojici Hodnota** **názvu**/a vyberte **Uložit**. Můžete také vybrat existující značku ze seznamu a zobrazit nebo spravovat prostředky přidružené k této značce.

    ![Správa značek](./media/devtest-lab-add-tag/devtestlab-manage-tags.png)

> [!NOTE]
> Značky vytvořené na úrovni testovacího prostředí toku přes všechny fakturovatelné prostředky, které testovací prostředí se točí ve vašem předplatném. Například značky na úrovni testovacího prostředí toku do podkladových výpočetních virtuálních počítačů virtuálních počítačích testovacího prostředí.Můžete použít značky v kontextu správy nákladů. Značky úrovně testovacího prostředí se zobrazují ve filtru značek pro správu nákladů.

## <a name="understanding-limitations-to-tags"></a>Principy omezení značek

Na značky se vztahují následující omezení:

* Každý prostředek nebo skupina prostředků může mít maximálně 15 párů název/hodnota značek. Toto omezení se vztahuje pouze na značky použité přímo na prostředek nebo skupinu prostředků. Skupina prostředků může obsahovat řadu prostředků, z nichž každý má 15 párů název/hodnota značek.
* Název značky je omezen na 512 znaků a hodnota značky je omezena na 256 znaků. Pro účty úložiště je název značky omezen na 128 znaků a hodnota značky je omezena na 256 znaků.
* Prostředky ve skupině prostředků nedědí značky použité na danou skupinu prostředků.

[Značky slouží k uspořádání prostředků Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) poskytuje další podrobnosti o používání značek v Azure, včetně jak spravovat značky pomocí PowerShellu nebo Azure CLI.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Další kroky
* Omezení a konvence v rámci předplatného můžete použít pomocí přizpůsobených zásad. Zásada, kterou definujete, může vyžadovat, aby všechny prostředky měly hodnotu pro určitou značku. Další informace naleznete v tématu [Nastavení zásad a plánů](devtest-lab-set-lab-policy.md).
* Prozkoumejte [galerii šablon rychlého startu Nástroje pro co vyvěšení pomocí nástroje DevTest Labs azure .](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates)
