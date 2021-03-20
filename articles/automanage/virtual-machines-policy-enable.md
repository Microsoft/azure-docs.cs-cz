---
title: Povolit pro virtuální počítače možnost automanage prostřednictvím Azure Policy
description: Přečtěte si, jak povolit Azure automanage pro virtuální počítače pomocí integrované Azure Policy v Azure Portal.
author: ju-shim
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: how-to
ms.date: 09/04/2020
ms.author: jushiman
ms.openlocfilehash: 8f679626b69bd855e86b94cdde51955edd068e8f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "91714893"
---
# <a name="enable-automanage-for-virtual-machines-through-azure-policy"></a>Povolit pro virtuální počítače možnost automanage prostřednictvím Azure Policy

Pokud chcete povolit autosprávu pro spoustu virtuálních počítačů, můžete to udělat pomocí integrované [Azure Policy](..\governance\azure-management.md). Tento článek vás provede hledáním správné zásady a jejím přiřazením, aby se povolila funkce automanage v Azure Portal.


## <a name="prerequisites"></a>Předpoklady

Pokud ještě nemáte předplatné Azure, vytvořte si [účet](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) před tím, než začnete.

> [!NOTE]
> Bezplatné zkušební účty nemají přístup k virtuálním počítačům použitým v tomto kurzu. Upgradujte prosím na předplatné s průběžnými platbami.

> [!IMPORTANT]
> K povolení funkce automanage můžete použít následující oprávnění Azure RBAC: roli **vlastníka** nebo **přispěvatele** spolu s rolemi **Správce přístupu uživatelů** .


## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se na [Azure Portal](https://portal.azure.com/).


## <a name="locate-and-assign-the-policy"></a>Vyhledání a přiřazení zásad

1. Přejít na **zásadu** v Azure Portal
1. Přejít do podokna **definice**
1. Kliknutím na rozevírací nabídku **kategorie** zobrazíte dostupné možnosti.
1. Vyberte možnost **Povolit automanage – Azure Virtual Machine Best Practices** .
1. Teď se seznam aktualizuje a zobrazí se předdefinovaná zásada s názvem, který začíná na *Povolit automanage...*
1. Klikněte na možnost *Povolit automanage –* název předdefinované zásady pro virtuální počítače Azure.
1. Po kliknutí na zásadu se teď můžete podívat na kartu **definice** .

    > [!NOTE]
    > Definice Azure Policy slouží k nastavení parametrů automanage, jako je konfigurační profil nebo účet. Také nastavuje filtry, které zajistí, že se zásady vztahují jenom na správné virtuální počítače.

1. Kliknutím na tlačítko **přiřadit** vytvoříte přiřazení.
1. Na kartě **základy** vyplňte **Rozsah** nastavením *předplatného* a *skupiny prostředků* .

    > [!NOTE]
    > Obor vám umožní definovat, na které virtuální počítače se tato zásada vztahuje. Aplikaci můžete nastavit na úrovni předplatného nebo na úrovni skupiny prostředků. Pokud nastavíte skupinu prostředků, automaticky se povolí automatické řízení všech virtuálních počítačů, které jsou aktuálně v této skupině prostředků nebo jakýchkoli budoucích virtuálních počítačích, které do ní přidáte. 

1. Klikněte na kartu **parametry** a nastavte účet pro **správu** a **profil požadované konfigurace** . 
1. Na kartě **Revize + vytvořit** zkontrolujte nastavení.
1. Použít přiřazení kliknutím na **vytvořit**
1. Zobrazit vaše přiřazení na kartě **přiřazení** vedle **definice**

> [!NOTE]
> Tato zásada bude nějakou dobu trvat, než začnou platit na virtuálních počítačích, které jsou aktuálně ve skupině prostředků nebo v předplatném.


## <a name="next-steps"></a>Další kroky 

Naučte se další způsob, jak povolit Azure automanage pro virtuální počítače prostřednictvím Azure Portal. 

> [!div class="nextstepaction"]
> [Povolit automanage pro virtuální počítače v Azure Portal](quick-create-virtual-machines-portal.md)