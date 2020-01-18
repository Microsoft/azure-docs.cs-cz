---
title: Přidání příkazu interní podpory do testovacího prostředí v Azure DevTest Labs
description: Naučte se publikovat interní příkaz podpory do testovacího prostředí v Azure DevTest Labs
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
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2020
ms.locfileid: "76170353"
---
# <a name="add-an-internal-support-statement-to-a-lab-in-azure-devtest-labs"></a>Přidání příkazu interní podpory do testovacího prostředí v Azure DevTest Labs

Azure DevTest Labs vám umožní přizpůsobit testovací prostředí pomocí interního příkazu podpory, který uživatelům poskytuje informace o podpoře pro testovací prostředí. Můžete například poskytnout kontaktní informace, aby uživatel věděl, jak dosáhnout interní podpory, když potřebuje pomoc při řešení potíží nebo přístup k prostředkům v testovacím prostředí. Můžete také poskytnout odkazy na interní weby nebo nejčastější dotazy, ke kterým má váš tým přístup, než se obrátí na podporu.

Interní příkaz podpory je určený k odeslání informací o testovacím prostředí, které se obvykle nemění příliš často. Chcete-li upozornit uživatele na informace o testovacím prostředí, které jsou lépe dočasná, jako jsou například nedávné aktualizace zásad testovacího prostředí – viz [příspěvku po oznámení v testovacím prostředí](devtest-lab-announcements.md).

Příkaz podpory lze snadno zakázat nebo upravit poté, co již není platný.

## <a name="steps-to-add-a-support-statement-to-an-existing-lab"></a>Postup přidání příkazu support do stávajícího testovacího prostředí

1. Přihlaste se k [Portálu Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. V případě potřeby vyberte **všechny služby**a v seznamu vyberte **DevTest Labs** . (Vaše testovací prostředí se už možná zobrazuje na řídicím panelu v části **všechny prostředky**).
1. V seznamu cvičení vyberte testovací prostředí, ve kterém chcete přidat příkaz podpory.  
1. V oblasti **Přehled** testovacího prostředí vyberte **Konfigurace a zásady**.  

    ![Tlačítko Konfigurace a zásady](./media/devtest-lab-internal-support-message/devtestlab-config-and-policies.png)

1. Vlevo vyberte v části **Nastavení**možnost **interní podpora**.

    ![Tlačítko interní podpora](./media/devtest-lab-internal-support-message/devtestlab-internal-support.png)

1. Chcete-li vytvořit interní zprávu o podpoře pro uživatele v tomto testovacím prostředí, nastavte možnost povoleno na **Ano**.

1. Do pole **zpráva podpory** zadejte interní příkaz podpory, který chcete prezentovat uživatelům testovacího prostředí. Zpráva podpory přijímá Markdownu. Když zadáváte text zprávy, můžete zobrazit oblast **náhledu** v dolní části obrazovky, abyste viděli, jak se tato zpráva zobrazuje uživatelům.

    ![Obrazovka interní podpory pro vytvoření zprávy.](./media/devtest-lab-internal-support-message/devtestlab-add-support-statement.png)


1. Pokud je váš příkaz k podpoře připravený k publikování, vyberte **Uložit** .

Pokud již nechcete zobrazovat tuto zprávu o podpoře pro uživatele testovacího prostředí, vraťte se na stránku **interní podpory** a nastavte možnost **povoleno** na **ne**.

## <a name="steps-for-users-to-view-the-support-message"></a>Postup pro uživatele, kteří si můžou zobrazit zprávu o podpoře

1. Z [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040)vyberte testovací prostředí.

1. V oblasti **Přehled** testovacího prostředí vyberte **interní podpora**.  

    ![Tlačítko interní podpora](./media/devtest-lab-internal-support-message/devtestlab-internal-support.png)


1. Pokud je odeslána zpráva o podpoře, uživatel ji může zobrazit v podokně interní podpory.

    ![Podokno interní podpory zobrazující zveřejněnou zprávu o podpoře](./media/devtest-lab-internal-support-message/devtestlab-view-suport-statement.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Další kroky
* Vnitřní příkazy podpory se obvykle používají k poskytnutí informací o podpoře, které se často nemění. Můžete se také dozvědět, jak [Odeslat oznámení do testovacího prostředí](devtest-lab-announcements.md) a informovat uživatele o dočasných změnách nebo aktualizacích testovacího prostředí.
* [Nastavení zásad a plánů](devtest-lab-set-lab-policy.md) poskytuje informace o tom, jak můžete používat další omezení a konvence v rámci předplatného pomocí přizpůsobených zásad.