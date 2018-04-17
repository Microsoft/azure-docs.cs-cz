---
title: Správa prostředků Azure pomocí Průzkumníku cloudu | Microsoft Docs
description: Další informace o použití Průzkumníku cloudu k prohlížení a správě prostředků Azure v sadě Visual Studio.
services: visual-studio-online
documentationcenter: na
author: ghogen
manager: douge
editor: ''
ms.assetid: 6347dc53-f497-49d5-b29b-e8b9f0e939d7
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 03/25/2017
ms.author: ghogen
ms.openlocfilehash: f5205bb158141a3f8e0296fefe2528d1bc5ea64c
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="manage-the-resources-associated-with-your-azure-accounts-in-visual-studio-cloud-explorer"></a>Spravovat prostředky přidružené k vaší účty Azure v cloudu Průzkumníka Visual Studio
Průzkumník cloudu umožňuje zobrazit skupiny prostředků a prostředků Azure, zkontrolujte jejich vlastnosti a provádět akce diagnostiky klíče vývojáře z Visual Studia. 

Podobně jako [portál Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040), Průzkumník cloudu je založený na zásobník Azure Resource Manager. Proto Průzkumník cloudu rozumí prostředkům, například skupin prostředků Azure a službami Azure, jako třeba logiku aplikace a aplikace API a podporuje [řízení přístupu na základě role](role-based-access-control/role-assignments-portal.md) (RBAC). 

## <a name="prerequisites"></a>Požadavky
- [Visual Studio 2017](https://www.visualstudio.com/downloads/) s **úloha Azure** vybrána, nebo starší verze sady Visual Studio s [Microsoft Azure SDK pro .NET 2.9](https://www.microsoft.com/en-us/download/details.aspx?id=51657).
- Účet Microsoft Azure – Pokud nemáte účet, můžete [zaregistrovat k bezplatné zkušební verzi](http://go.microsoft.com/fwlink/?LinkId=623901) nebo [aktivovat výhody předplatitele sady Visual Studio](http://go.microsoft.com/fwlink/?LinkId=623901).

> [!NOTE]
> Chcete-li zobrazit Průzkumník cloudu, vyberte **zobrazení** > **Průzkumník cloudu** v řádku nabídek.   
> 
> 

## <a name="add-an-azure-account-to-cloud-explorer"></a>Azure přidat účet Průzkumníku cloudu
Chcete-li zobrazit prostředky přidružené k účtu Azure, je nejprve nutno přidat účet Průzkumník cloudu. 

1. V **Průzkumník cloudu**, vyberte **nastavení účtu Azure**.

    ![Ikona nastavení účtu Azure Průzkumníku cloudu](media/vs-azure-tools-resources-managing-with-cloud-explorer/azure-account-settings.png)

1. Vyberte **nový účet přidal**. 

    ![Odkaz Přidat účet Průzkumník cloudu](media/vs-azure-tools-resources-managing-with-cloud-explorer/add-account-link.png)

1. Přihlaste se k účtu Azure jehož prostředky, které chcete procházet. 

1. Po přihlášení k účtu Azure, zobrazí se odběry přidružené k tomuto účtu. Zaškrtněte políčka pro předplatná účet chcete procházet a pak vyberte **použít**. 
 
    ![Průzkumník cloudu: Vyberte předplatná Azure k zobrazení](media/vs-azure-tools-resources-managing-with-cloud-explorer/select-subscriptions.png)

1. Až vyberete předplatná, jejichž prostředky, které chcete procházet, tyto odběry a prostředky zobrazit v Průzkumníku cloudu.

    ![Průzkumník prostředků výpis pro účet Azure v cloudu](media/vs-azure-tools-resources-managing-with-cloud-explorer/resources-listed.png)

## <a name="remove-an-azure-account-from-cloud-explorer"></a>Odeberte účet Azure z Průzkumníku cloudu 

1. V **Průzkumník cloudu**, vyberte **nastavení účtu Azure**.

    ![Ikona nastavení účtu Azure Průzkumníku cloudu](media/vs-azure-tools-resources-managing-with-cloud-explorer/azure-account-settings.png)

1. U účtu, kterou chcete odebrat, vyberte **odebrat**.

    ![Ikona nastavení účtu Azure Průzkumníku cloudu](media/vs-azure-tools-resources-managing-with-cloud-explorer/remove-account.png)

## <a name="view-resource-types-or-resource-groups"></a>Zobrazit typy prostředků nebo skupiny prostředků
Chcete-li zobrazit vašich prostředků Azure, můžete zvolit buď **typy prostředků** nebo **skupiny prostředků** zobrazení.

1. V **Průzkumník cloudu**, vyberte zobrazení rozevíracího seznamu prostředků.

    ![Cloud Explorer rozevíracího seznamu vyberte zobrazení požadované prostředky](media/vs-azure-tools-resources-managing-with-cloud-explorer/resources-view-dropdown.png)

1. V místní nabídce vyberte požadované zobrazení: 

    - **Typy prostředků** zobrazení - běžné zobrazení použít na [portál Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040), zobrazuje vašich prostředků Azure uspořádány do kategorií podle jejich typu, jako jsou webové aplikace, účty úložiště a virtuálních počítačů. 
    - **Skupiny prostředků** zobrazit - rozděluje Azure prostředky ve skupině prostředků Azure, ke kterému jsou přidružené. Skupina prostředků je sada prostředky Azure, obvykle používají v konkrétní aplikaci. Další informace o skupinách prostředků Azure, najdete v části [přehled Azure Resource Manageru](./azure-resource-manager/resource-group-overview.md).

    Následující obrázek znázorňuje porovnání zobrazení dvou zdrojů:

    ![Porovnání zobrazení Průzkumníka prostředků cloudu](media/vs-azure-tools-resources-managing-with-cloud-explorer/resource-views-comparison.png)

## <a name="view-and-navigate-resources-in-cloud-explorer"></a>Zobrazení a přejděte prostředky v Průzkumníku cloudu
Přejděte na prostředek služby Azure a zobrazit její informace v Průzkumníku cloudu, rozbalte položky typu nebo související skupiny prostředků a pak vyberte prostředek. Když vyberete prostředek, informace se zobrazí v dvě karty - **akce** a **vlastnosti** – v dolní části Průzkumník cloudu. 

- **Akce** kartě – jsou uvedeny akce, můžete provést v Průzkumníku cloudu pro vybraný zdroj. Tyto možnosti můžete zobrazit také kliknutím pravým tlačítkem na prostředek zobrazíte jeho kontextové nabídky.

- **Vlastnosti** kartě – se zobrazují vlastnosti prostředku, například jeho typ, národní prostředí a prostředků skupiny, ke kterému je přiřazeno.

Následující obrázek ukazuje příklad porovnání zobrazené na jednotlivých kartách pro aplikační službu:

![](./media/vs-azure-tools-resources-managing-with-cloud-explorer/actions-and-properties.png)

Každý prostředek má akce **otevřít na portálu**. Pokud zvolíte tuto akci, Průzkumník cloudu zobrazí vybrané prostředky v [portál Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040). **Otevřít na portálu** funkce je užitečný pro navigaci na hluboko vložené prostředky.

Další akce a hodnot vlastností mohou zobrazit i podle prostředků Azure. Webové aplikace a aplikace logiky také mít například akce **otevřít v prohlížeči** a **připojit ladicí program** kromě **otevřít na portálu**. Akce otevřete editory se zobrazí, když zvolíte účet úložiště objektů blob, fronty nebo tabulky. Azure aplikací mít **URL** a **stav** vlastností, zatímco prostředky úložiště mají vlastnosti klíče a připojovací řetězce.

## <a name="find-resources-in-cloud-explorer"></a>Vyhledávání prostředků v Průzkumníku cloudu
Chcete-li vyhledat prostředky s konkrétním názvem v rámci vašich předplatných účet Azure, zadejte název v **vyhledávání** pole v Průzkumníku cloudu.

![Vyhledávání prostředků v Průzkumníku cloudu](./media/vs-azure-tools-resources-managing-with-cloud-explorer/search-for-resources.png)

Při zadávání znaků **vyhledávání** pole jenom prostředky, které splňují tyto znaky se zobrazí ve stromové struktuře prostředků.
