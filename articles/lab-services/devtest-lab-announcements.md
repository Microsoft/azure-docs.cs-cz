---
title: Účtování announcment do testovacího prostředí ve službě Azure DevTest Labs | Dokumentace Microsoftu
description: Zjistěte, jak přidat hlášení do testovacího prostředí ve službě Azure DevTest Labs
services: devtest-lab,virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: 67a09946-4584-425e-a94c-abe57c9cbb82
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 3282a90069ecd119154df492ac6dc366d26b5300
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38611201"
---
# <a name="post-an-announcement-to-a-lab-in-azure-devtest-labs"></a>Odeslání oznámení do testovacího prostředí ve službě Azure DevTest Labs

Jako správce testovacího prostředí můžete odeslat vlastní oznámení v existující testovací prostředí, abyste upozornili uživatele o poslední změny nebo doplnění testovacího prostředí. Můžete například chtít informovat uživatele o:

- Nové velikosti virtuálních počítačů, které jsou k dispozici
- Bitové kopie, které jsou aktuálně nepoužitelné
- Aktualizace zásad testovacího prostředí

Po publikování, oznámení se zobrazí na stránce s přehledem testovacího prostředí a uživatel může vybrat další podrobnosti.

Funkce oznámení je určena pro použití dočasného oznámení.  Můžete snadno zakázat oznámení po už je nepotřebujete.

## <a name="steps-to-post-an-announcement-in-an-existing-lab"></a>Postup odeslání oznámení v existující testovací prostředí

1. Přihlaste se k webu [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. V případě potřeby vyberte **všechny služby**a pak vyberte **DevTest Labs** ze seznamu. (Testovací prostředí může již zobrazený na řídicím panelu v části **všechny prostředky**).
1. V seznamu testovacích prostředí vyberte prostředí, ve kterém chcete odeslat oznámení.  
1. Cvičení **přehled** vyberte **konfigurace a zásad**.  

    ![Konfigurace a zásad tlačítko](./media/devtest-lab-announcements/devtestlab-config-and-policies.png)

1. Na levé straně v části **nastavení**vyberte **oznámení testovacího prostředí**.

    ![Tlačítko oznámení testovacího prostředí](./media/devtest-lab-announcements/devtestlab-announcements.png)

1. Chcete-li vytvořit zprávu pro uživatele v tomto testovacím prostředí, nastavte **povoleno** k **Ano**.

1. Můžete zadat **datum vypršení platnosti** zadat datum a čas, po jejímž uplynutí je oznámení už nebude zobrazovat uživatelům. Pokud nezadáte datum vypršení platnosti, oznámení zůstane, dokud ji zakázat.

   > [!NOTE]
   > Po vypršení platnosti oznámení, je už nebude zobrazovat uživatelům, ale stále existuje v **oznámení testovacího prostředí** podokně. Můžete provádět úpravy a znovu povolit, aby byl aktivní znovu.
   >
   >

1. Zadejte **název oznámení** a **text oznámení**.

   Název může být maximálně 100 znaků a zobrazí se uživateli na stránce s přehledem testovacího prostředí. Pokud uživatel vybere název, zobrazí se text oznámení.

   Text oznámení přijímá markdownu. Při zadávání textu oznámení se zobrazí zpráva v oblasti ve verzi Preview v dolní části obrazovky.

    ![Obrazovka oznámení testovacího prostředí vytvořit zprávu.](./media/devtest-lab-announcements/devtestlab-post-announcement.png)


1. Vyberte **Uložit** po oznámení je připravený ke zveřejnění.

Pokud už nechcete zobrazit toto oznámení uživatelům testovacího prostředí, vraťte se do **oznámení testovacího prostředí** stránku a nastavit **povoleno** k **ne**. Pokud jste zadali datum vypršení platnosti, oznámení automaticky zakázán na, data a času.

## <a name="steps-for-users-to-view-an-announcement"></a>Kroky pro uživatele, chcete-li zobrazit oznámení

1. Z [webu Azure portal](http://go.microsoft.com/fwlink/p/?LinkID=525040), vyberte testovacího prostředí.

1. Pokud testovací prostředí se ji odeslat oznámení, oznámení informace se zobrazí v horní části stránky přehled testovacího prostředí. Toto upozornění je název oznámení, který byl zadán při vytvoření oznámení.

    ![Oznámení testovacího prostředí na stránce s přehledem](./media/devtest-lab-announcements/devtestlab-user-announcement.png)

1. Uživatel může vybrat zpráva, kterou chcete zobrazit celý oznámení.

    ![Další informace o oznámení testovacího prostředí](./media/devtest-lab-announcements/devtestlab-user-announcement-text.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Další postup
* Pokud změníte nebo nastavte zásady testovacího prostředí, můžete chtít odeslat oznámení informovat uživatele. [Nastavení zásad a plánů](devtest-lab-set-lab-policy.md) poskytuje informace o použití omezení a pravidla týkající se celém předplatném pomocí vlastních zásad.
* Prozkoumejte [Galerie šablon QuickStart pro Azure Resource Manageru DevTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/Samples).
