---
title: Přidejte příkaz interní podpory do testovacího prostředí v Azure DevTest Labs | Microsoft Docs
description: Odeslání příkazu interní podpory do testovacího prostředí v Azure DevTest Labs
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
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 2d12ca26fb2aa5abddcf44b2e634b2f08b1fb01b
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
ms.locfileid: "33787427"
---
# <a name="add-an-internal-support-statement-to-a-lab-in-azure-devtest-labs"></a>Přidejte příkaz interní podpory do testovacího prostředí v Azure DevTest Labs

Azure DevTest Labs umožňuje přizpůsobit testovacího prostředí příkazem interní podpory, která uživatelům poskytuje informace o testovacím prostředí. Tak, že uživatel zná tom, jak kontaktovat interní podpory, když potřebují nápovědu k odstraňování potíží a přístupu k prostředkům v testovacím prostředí, je třeba zadat kontaktní informace. Můžete zadat také odkazy na interní weby nebo nejčastější dotazy, které váš tým může získat přístup, než se obrátíte na podporu.

Abyste mohli odeslat informace o prostředí, které nemění obvykle příliš často je určený příkazu interní podpory. Upozornit uživatele o testovacím informací, které je ve své podstatě – například poslední aktualizace zásad testovacího – více dočasný, najdete v části [Post oznámení v testovacím prostředí](devtest-lab-announcements.md).

Snadno můžete zakázat nebo upravit příkaz podpora po není nadále vhodné.

## <a name="steps-to-add-a-support-statement-to-an-existing-lab"></a>Postup pro přidání podpory příkaz do existujícího testovacího prostředí

1. Přihlaste se k webu [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. V případě potřeby vyberte **všechny služby**a potom vyberte **DevTest Labs** ze seznamu. (Testovacího prostředí může zobrazit již na řídicím panelu v části **všechny prostředky**).
1. Ze seznamu labs vyberte testovací prostředí, ve které chcete přidat prohlášení o odborné pomoci.  
1. V tomto prostředí **přehled** oblasti, vyberte **konfiguraci a zásady**.  

    ![Tlačítko konfiguraci a zásady](./media/devtest-lab-internal-support-message/devtestlab-config-and-policies.png)

1. Na levé straně v části **nastavení**, vyberte **interní podpory**.

    ![Tlačítko interní podpory](./media/devtest-lab-internal-support-message/devtestlab-internal-support.png)

1. Chcete-li vytvořit zprávu interní podpory pro uživatele v tomto testovacím prostředí, nastavte povoleno na **Ano**.

1. V **podpory pro odeslání zprávy** pole, zadejte příkaz interní podpory, který má být k dispozici pro vaše prostředí uživatele. Podpora zprávu přijme Markdownu. Při zadávání text zprávy, můžete zobrazit **Preview** oblasti v dolní části obrazovky najdete v části Jak se tato zpráva se zobrazí uživatelům.

    ![Interní podpory obrazovky zprávu lze vytvořit.](./media/devtest-lab-internal-support-message/devtestlab-add-support-statement.png)


1. Vyberte **Uložit** po odesíláním prohlášení o odborné pomoci.

Pokud již nechcete zobrazit tato zpráva podporu uživatelům prostředí, vraťte se do **interní podpory** stránky a nastavte **povoleno** k **ne**.

## <a name="steps-for-users-to-view-the-support-message"></a>Kroky pro uživatelům zobrazit podpory pro odeslání zprávy

1. Z [portál Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040), vyberte testovacím prostředí.

1. V tomto prostředí **přehled** oblasti, vyberte **interní podpory**.  

    ![Tlačítko interní podpory](./media/devtest-lab-internal-support-message/devtestlab-internal-support.png)


1. Pokud je odeslána zpráva podpory, uživatel může zobrazit v podokně interní podpory.

    ![Interní podpory podokně zobrazující podpory pro odeslání zprávy](./media/devtest-lab-internal-support-message/devtestlab-view-suport-statement.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Další postup
* Příkazy interní podpory jsou obvykle používány k poskytování informace podpory, které často, nezmění. Můžete si také přečíst postup [odeslat oznámení do testovacího prostředí](devtest-lab-announcements.md) k informování uživatelů dočasný změny nebo aktualizace do testovacího prostředí.
* [Nastavit zásady a plány](devtest-lab-set-lab-policy.md) poskytuje informace o tom, jak můžete použít další omezení a pravidla týkající se vašeho předplatného pomocí vlastních zásad.