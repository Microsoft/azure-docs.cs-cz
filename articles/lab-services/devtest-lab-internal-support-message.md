---
title: Přidání interního prohlášení o podpoře do testovacího prostředí v laboratořích Azure DevTest Labs
description: Přečtěte si, jak zveřejnit interní prohlášení o podpoře do testovacího prostředí v Azure DevTest Labs
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: c9900333-6c5e-4d7d-b0f4-889015e9550c
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 591dcec36dc62143901d3b49db24196e84d58c29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76170353"
---
# <a name="add-an-internal-support-statement-to-a-lab-in-azure-devtest-labs"></a>Přidání interního prohlášení o podpoře do testovacího prostředí v laboratořích Azure DevTest Labs

Azure DevTest Labs umožňuje přizpůsobit testovací prostředí pomocí interního prohlášení o podpoře, které uživatelům poskytuje informace o podpoře testovacího prostředí. Můžete například poskytnout kontaktní informace, aby uživatel věděl, jak dosáhnout interní podpory, když potřebuje pomoc s řešením potíží nebo přístupem k prostředkům v testovacím prostředí. Před kontaktováním podpory můžete také poskytnout odkazy na interní weby nebo nejčastější dotazy, ke kterým má váš tým přístup.

Interní prohlášení podpory je určena k zaúčtování informací testovacího prostředí, které se obvykle nemění příliš často. Informace o oznámení o informacích o testovacím prostředí, které jsou dočasnější povahy, například nedávné aktualizace zásad testovacího prostředí, naleznete [v tématu Oznámení o odeslání v testovacím prostředí](devtest-lab-announcements.md).

Prohlášení o podpoře můžete snadno zakázat nebo upravit poté, co již není použitelné.

## <a name="steps-to-add-a-support-statement-to-an-existing-lab"></a>Postup přidání prohlášení o podpoře do existujícího testovacího prostředí

1. Přihlaste se k [portálu Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. V případě potřeby vyberte **všechny služby**a ze seznamu vyberte **DevTest Labs.** (Vaše testovací prostředí se už může zobrazit na řídicím panelu v části **Všechny prostředky**).
1. Ze seznamu testovacích prostředí vyberte testovací prostředí, ve kterém chcete přidat prohlášení o podpoře.  
1. V oblasti **Přehled** testovacího prostředí vyberte **Konfigurace a zásady**.  

    ![Tlačítko Konfigurace a zásady](./media/devtest-lab-internal-support-message/devtestlab-config-and-policies.png)

1. Vlevo v části **NASTAVENÍ**vyberte **Interní podpora**.

    ![Tlačítko interní podpory](./media/devtest-lab-internal-support-message/devtestlab-internal-support.png)

1. Chcete-li vytvořit interní zprávu o podpoře pro uživatele v tomto testovacím prostředí, nastavte možnost Povoleno na **ano**.

1. Do pole **Zpráva podpory** zadejte interní prohlášení podpory, které chcete zobrazit uživatelům testovacího prostředí. Zpráva podpory přijímá Markdown. Při zadávání textu zprávy můžete zobrazit oblast **Náhled** v dolní části obrazovky a zobrazit tak, jak se zpráva zobrazí uživatelům.

    ![Interní obrazovka podpory pro vytvoření zprávy.](./media/devtest-lab-internal-support-message/devtestlab-add-support-statement.png)


1. Až bude výpis podpory připravený k zaúčtování, vyberte **Uložit.**

Pokud již nechcete zobrazovat tuto zprávu podpory uživatelům testovacího prostředí, vraťte se na stránku **Interní podpora** a nastavte **možnost Povoleno** na **ne**.

## <a name="steps-for-users-to-view-the-support-message"></a>Kroky k zobrazení zprávy o podpoře pro uživatele

1. Na [webu Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040)vyberte testovací prostředí.

1. V oblasti **Přehled** testovacího prostředí vyberte **Interní podpora**.  

    ![Tlačítko interní podpory](./media/devtest-lab-internal-support-message/devtestlab-internal-support.png)


1. Pokud je zpráva podpory zaúčtována, uživatel ji může zobrazit v podokně Interní podpora.

    ![Interní podokno podpory zobrazující zaúčtované zprávy o podpoře](./media/devtest-lab-internal-support-message/devtestlab-view-suport-statement.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Další kroky
* Interní příkazy podpory se obvykle používají k poskytování informací o podpoře, které se nemění tak často. Můžete se také dozvědět, jak [odeslat oznámení do testovacího prostředí](devtest-lab-announcements.md) informovat uživatele o dočasné změny nebo aktualizace do testovacího prostředí.
* [Nastavení zásad a plánů](devtest-lab-set-lab-policy.md) poskytuje informace o tom, jak můžete použít další omezení a konvence v rámci předplatného pomocí přizpůsobených zásad.