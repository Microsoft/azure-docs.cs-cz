---
title: Správa prostředků Azure pomocí Průzkumníka cloudu | Dokumentace Microsoftu
description: Zjistěte, jak pomocí Průzkumníka cloudu k procházení a správě prostředků Azure v rámci sady Visual Studio.
services: visual-studio-online
author: ghogen
manager: douge
assetId: 6347dc53-f497-49d5-b29b-e8b9f0e939d7
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/25/2017
ms.author: ghogen
ms.openlocfilehash: a4be3f063c51f26e1df9ecf4cca4ae0655554750
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/18/2018
ms.locfileid: "42055654"
---
# <a name="manage-the-resources-associated-with-your-azure-accounts-in-visual-studio-cloud-explorer"></a>Spravovat prostředky přidružené k účtům Azure v Průzkumníku cloudu sady Visual Studio
Průzkumník cloudu umožňuje zobrazit vaše prostředky Azure a skupiny prostředků, zkoumání jejich vlastností a provádění klíčových vývojářských diagnostiky úkonů ze sady Visual Studio. 

Podobně jako [webu Azure portal](http://go.microsoft.com/fwlink/p/?LinkID=525040), Průzkumníka cloudu je založený na zásobníku správce prostředků Azure. Proto se Průzkumník cloudu rozumí prostředky, jako jsou skupiny prostředků Azure a služeb Azure, například Logic apps a API apps, a podporuje [řízení přístupu na základě rolí](role-based-access-control/role-assignments-portal.md) (RBAC). 

## <a name="prerequisites"></a>Požadavky
- [Visual Studio 2017](https://www.visualstudio.com/downloads/) s **úlohy Azure** vybrali, nebo starší verzi sady Visual Studio s [Microsoft Azure SDK pro .NET 2.9](https://www.microsoft.com/en-us/download/details.aspx?id=51657).
- Účet Microsoft Azure – Pokud nemáte účet, můžete si [zaregistrujte si bezplatnou zkušební verzi](http://go.microsoft.com/fwlink/?LinkId=623901) nebo [aktivovat výhody pro předplatitele sady Visual Studio](http://go.microsoft.com/fwlink/?LinkId=623901).

> [!NOTE]
> Chcete-li zobrazit Průzkumník cloudu, vyberte **zobrazení** > **Průzkumníka cloudu** na řádku nabídek.   
> 
> 

## <a name="add-an-azure-account-to-cloud-explorer"></a>Azure přidat účet Průzkumník cloudu
Chcete-li zobrazit prostředky přidružené k účtu Azure, musíte nejprve přidat účet do Průzkumníka cloudu. 

1. V **Průzkumníka cloudu**vyberte **nastavení účtu Azure**.

    ![Ikona nastavení účtu Explorer Azure cloudu](media/vs-azure-tools-resources-managing-with-cloud-explorer/azure-account-settings.png)

1. Vyberte **přidat nový účet**. 

    ![Odkaz Přidat účet Průzkumník cloudu](media/vs-azure-tools-resources-managing-with-cloud-explorer/add-account-link.png)

1. Přihlaste se k účtu Azure jehož prostředky, které chcete procházet. 

1. Po přihlášení k účtu Azure, zobrazují se předplatná přidružená k tomuto účtu. Zaškrtněte políčka pro předplatná účtu chcete procházet a vyberte **použít**. 
 
    ![Průzkumníka cloudu: Vyberte předplatná Azure k zobrazení](media/vs-azure-tools-resources-managing-with-cloud-explorer/select-subscriptions.png)

1. Až vyberete předplatná, jehož prostředky, které chcete procházet, tato předplatná a prostředky zobrazit v Průzkumníku cloudu.

    ![Cloud Explorer prostředků pro účet Azure](media/vs-azure-tools-resources-managing-with-cloud-explorer/resources-listed.png)

## <a name="remove-an-azure-account-from-cloud-explorer"></a>Odebrat účet Azure z Průzkumníka cloudu 

1. V **Průzkumníka cloudu**vyberte **nastavení účtu Azure**.

    ![Ikona nastavení účtu Explorer Azure cloudu](media/vs-azure-tools-resources-managing-with-cloud-explorer/azure-account-settings.png)

1. Vedle účet, který chcete odebrat, vyberte **odebrat**.

    ![Ikona nastavení účtu Explorer Azure cloudu](media/vs-azure-tools-resources-managing-with-cloud-explorer/remove-account.png)

## <a name="view-resource-types-or-resource-groups"></a>Zobrazit typy prostředků nebo skupiny prostředků
Chcete-li zobrazit vaše prostředky Azure, můžete použít buď **typy prostředků** nebo **skupiny prostředků** zobrazení.

1. V **Průzkumníka cloudu**, vyberte rozevírací seznam zobrazení prostředků.

    ![Cloud Explorer rozevírací seznam a vyberte zobrazení požadované prostředky](media/vs-azure-tools-resources-managing-with-cloud-explorer/resources-view-dropdown.png)

1. V místní nabídce vyberte požadované zobrazení: 

    - **Typy prostředků** view - běžné zobrazení použité na [webu Azure portal](http://go.microsoft.com/fwlink/p/?LinkID=525040), ukazuje prostředky Azure zařazených do kategorií podle typu, jako jsou webové aplikace, účty úložiště a virtuální počítače. 
    - **Skupiny prostředků** zobrazit – prostředky Azure slouží ke kategorizaci podle skupiny prostředků Azure, které jsou přidružené. Skupina prostředků je sadu prostředků Azure, obvykle používají konkrétní aplikaci. Další informace o skupinách prostředků Azure najdete v tématu [přehled Azure Resource Manageru](./azure-resource-manager/resource-group-overview.md).

    Následující obrázek znázorňuje srovnání zobrazení dvou zdrojů:

    ![Porovnání zobrazení Průzkumník prostředků v cloudu](media/vs-azure-tools-resources-managing-with-cloud-explorer/resource-views-comparison.png)

## <a name="view-and-navigate-resources-in-cloud-explorer"></a>Zobrazení a procházení prostředků v Průzkumníku cloudu
Přejděte do prostředku Azure a zobrazit její informace v Průzkumníkovi cloudu, rozbalte položky typu nebo skupiny prostředků a pak vyberte prostředek. Když vyberete zdroj, informace se zobrazí na dvou kartách – **akce** a **vlastnosti** – v dolní části Průzkumníku cloudu. 

- **Akce** kartě - jsou uvedeny akce, které můžete provádět v Průzkumníkovi cloudu pro vybraný prostředek. Tyto možnosti můžete také zobrazit kliknutím pravým tlačítkem myši prostředek zobrazíte její kontextovou nabídku.

- **Vlastnosti** kartě - jsou uvedeny vlastnosti prostředku, například jeho typ, národní prostředí a skupině prostředků, ke kterému je přidružena.

Co se zobrazí na každé kartě pro služby App Service porovnání příklad na následujícím obrázku:

![](./media/vs-azure-tools-resources-managing-with-cloud-explorer/actions-and-properties.png)

Každý prostředek má akce **otevřít na portálu**. Pokud zvolíte tuto akci, zobrazuje Průzkumník cloudu vybraný prostředek v [webu Azure portal](http://go.microsoft.com/fwlink/p/?LinkID=525040). **Otevřít na portálu** funkce je užitečný pro přechod na hluboce vnořených prostředků.

Další akce a hodnot vlastností mohou také objevit podle prostředků Azure. Například web apps a logic apps také k dispozici akce **otevřít v prohlížeči** a **připojit ladicí program** kromě **otevřít na portálu**. Akce otevření editoru se zobrazí při výběru účtu úložiště objektů blob, fronty nebo tabulky. Aplikace Azure mají **URL** a **stav** vlastnosti, ale prostředky úložiště mají vlastnosti klíče a připojovací řetězce.

## <a name="find-resources-in-cloud-explorer"></a>Najít prostředky v Průzkumníkovi cloudu
Při vyhledávání prostředků s konkrétním názvem v rámci vašich předplatných Azure účet, zadejte název do **hledání** pole v Průzkumníku cloudu.

![Vyhledání prostředků v Průzkumníku cloudu](./media/vs-azure-tools-resources-managing-with-cloud-explorer/search-for-resources.png)

Při zadávání znaků **hledání** pole, zobrazí jenom prostředky, které splňují tyto znaky ve stromové struktuře prostředků.
