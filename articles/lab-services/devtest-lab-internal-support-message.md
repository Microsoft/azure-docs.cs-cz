---
title: Přidání interní podpoře do testovacího prostředí ve službě Azure DevTest Labs | Dokumentace Microsoftu
description: Zjistěte, jak publikovat interní podpoře do testovacího prostředí ve službě Azure DevTest Labs
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
ms.openlocfilehash: deb98c2c633200ab4be1d763a94fd2a04979a3b1
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51235696"
---
# <a name="add-an-internal-support-statement-to-a-lab-in-azure-devtest-labs"></a>Přidání interní podpoře do testovacího prostředí ve službě Azure DevTest Labs

Azure DevTest Labs umožňuje přizpůsobit testovacího prostředí pomocí příkazu interní podpory, která poskytuje uživatelům informace o tom, testovací prostředí. Tak, aby uživatel ví, jak dosáhnout interní podpory, když je budou potřebovat pomoc při řešení potíží nebo přístup k prostředkům v testovacím prostředí, je třeba zadat kontaktní informace. Můžete také poskytnout odkazy na interní weby nebo nejčastější dotazy, které může váš tým přistupovat, než se obrátíte na podporu.

Příkaz interní podpory má umožňují publikovat informace testovacího prostředí, která se nezmění obvykle příliš často. Upozornit uživatele na informace o prostředí, která je ve své podstatě – například poslední aktualizace zásad testovacího prostředí – více dočasné naleznete v tématu [zveřejnění oznámení v testovacím prostředí](devtest-lab-announcements.md).

Snadno můžete zakázat nebo upravit podpoře po není nadále vhodné.

## <a name="steps-to-add-a-support-statement-to-an-existing-lab"></a>Kroky k přidání podpoře na existující testovací prostředí

1. Přihlaste se k webu [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. V případě potřeby vyberte **všechny služby**a pak vyberte **DevTest Labs** ze seznamu. (Testovací prostředí může již zobrazený na řídicím panelu v části **všechny prostředky**).
1. V seznamu testovacích prostředí vyberte testovací prostředí, ve které chcete přidat podpoře.  
1. Cvičení **přehled** vyberte **konfigurace a zásad**.  

    ![Konfigurace a zásad tlačítko](./media/devtest-lab-internal-support-message/devtestlab-config-and-policies.png)

1. Na levé straně v části **nastavení**vyberte **interní podpory**.

    ![Interní podpora tlačítka](./media/devtest-lab-internal-support-message/devtestlab-internal-support.png)

1. Vytvoření interní podpory zprávy pro uživatele v tomto testovacím prostředí, nastavte Enabled na **Ano**.

1. V **podpory pro odeslání zprávy** zadejte interní podpoře, kterou chcete k dispozici uživatelům testovacího prostředí. Podpory pro odeslání zprávy přijme Markdownu. Při zadávání text zprávy můžete zobrazit **ve verzi Preview** oblast v dolní části obrazovky, chcete-li zobrazit, jak se uživatelům zobrazí zpráva.

    ![Interní podpory obrazovky k vytvoření zprávy.](./media/devtest-lab-internal-support-message/devtestlab-add-support-statement.png)


1. Vyberte **Uložit** po připravený ke zveřejnění vašeho prohlášení o odborné pomoci.

Pokud už nechcete zobrazovat tento dialog podpora uživatelům testovacího prostředí, vraťte se do **interní podpory** stránku a nastavit **povoleno** k **ne**.

## <a name="steps-for-users-to-view-the-support-message"></a>Kroky pro uživatele zobrazíte podpory pro odeslání zprávy

1. Z [webu Azure portal](https://go.microsoft.com/fwlink/p/?LinkID=525040), vyberte testovacího prostředí.

1. Cvičení **přehled** vyberte **interní podpory**.  

    ![Interní podpora tlačítka](./media/devtest-lab-internal-support-message/devtestlab-internal-support.png)


1. Pokud se pošle zpráva podpory, uživatel může zobrazit v podokně interní podpory.

    ![Podokno interní podpory zobrazující podporu zprávy odeslané](./media/devtest-lab-internal-support-message/devtestlab-view-suport-statement.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Další postup
* Příkazy interní podpory se obvykle používají k poskytnutí informací o podpoře, která se nemění, který často. Můžete dále jste zjistili, jak [odeslání oznámení do testovacího prostředí](devtest-lab-announcements.md) chcete informovat uživatele o dočasné změny nebo aktualizace testovacího prostředí.
* [Nastavení zásad a plánů](devtest-lab-set-lab-policy.md) poskytuje informace o tom, jak je můžete použít další omezení a konvence celém předplatném pomocí vlastních zásad.