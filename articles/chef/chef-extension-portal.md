---
title: Instalace klienta Chef z portálu Azure
description: Zjistěte, jak nasadit a nakonfigurovat klienta Chef z webu Azure Portal
keywords: azurová, chef, devops, klient, instalace, portál
ms.date: 02/22/2020
ms.topic: article
ms.openlocfilehash: 6e46133c598c44b314077f2d020852416d3d2745
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77586355"
---
# <a name="install-the-chef-client-from-the-azure-portal"></a>Instalace klienta Chef z portálu Azure
Rozšíření klienta Chef můžete přidat přímo do počítače s Linuxem nebo Windows z webu Azure Portal. Tento článek vás provede procesem pomocí nového virtuálního počítače Linux.

## <a name="prerequisites"></a>Požadavky

- **Předplatné Azure:** Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) před tím, než začnete.

- **Šéfkuchař:** Pokud nemáte aktivní účet Chef, zaregistrujte si [bezplatnou zkušební verzi hostovaného šéfkuchaře](https://manage.chef.io/signup). Chcete-li postupovat podle pokynů v tomto článku, budete potřebovat následující hodnoty z vašeho účtu Chef:
  - organization_validation klíč
  - Rb
  - run_list

## <a name="install-the-chef-extension-on-a-new-linux-virtual-machine"></a>Instalace rozšíření Chef na nový virtuální počítač s Linuxem
V této části nejprve použijete portál Azure k vytvoření počítače s Linuxem. Během procesu uvidíte také, jak nainstalovat rozšíření Chef na nový virtuální počítač.

1. Přejděte na [portál Azure](https://portal.azure.com).

1. V nabídce vlevo vyberte možnost **Virtuální počítače.** Pokud možnost **Virtuální počítače** není k dispozici, vyberte **Všechny služby** a pak vyberte **Virtuální počítače**.

1. Na kartě **Virtuální počítače** vyberte **Přidat**.

    ![Přidání nového virtuálního počítače na webu Azure Portal](./media/chef-extension-portal/add-vm.png)

1. Na kartě **Výpočetní výkon** vyberte požadovaný operační systém. Pro tuto ukázku je vybrán **server Ubuntu.**

1. Na kartě **Ubuntu Server** vyberte Ubuntu **Server 16.04 LTS**.

    ![Při vytváření virtuálního stroje Ubuntu zadejte verzi, kterou potřebujete](./media/chef-extension-portal/ubuntu-server-version.png)

1. Na kartě **Ubuntu Server 16.04 LTS** vyberte **Vytvořit**.

    ![Ubuntu poskytuje další informace o svém produktu](./media/chef-extension-portal/create-vm.png)

1. Na kartě **Vytvořit virtuální počítač** vyberte **Základy**.

1. Na kartě **Základy** zadejte následující hodnoty a pak vyberte **OK**.

   - **Název** - Zadejte název nového virtuálního počítače.
   - **Typ disku virtuálního počítače** – pro typ disku úložiště zadejte disk **SSD** nebo **HDD.** Další informace o typech disků virtuálních počítačů v Azure najdete v článku [Vyberte typ disku](../virtual-machines/windows/disks-types.md).
   - **Uživatelské jméno** – Zadejte uživatelské jméno, kterému jsou udělena oprávnění správce ve virtuálním počítači.
   - **Typ ověřování** – vyberte **heslo**. Můžete také vybrat **veřejný klíč SSH**a zadat hodnotu veřejného klíče SSH. Pro účely této ukázky (a na snímcích obrazovky) je **vybráno heslo.**
   - **Heslo** a **potvrdit heslo** - Zadejte heslo pro uživatele.
   - **Přihlaste se pomocí služby Azure Active Directory** – vyberte **zakázáno**.
   - **Předplatné** – pokud máte víc než jedno předplatné Azure, vyberte požadované předplatné Azure.
   - **Skupina prostředků** – Zadejte název skupiny prostředků.
   - **Umístění** – vyberte **možnost Východ USA**.

     ![Karta Základy pro vytvoření virtuálního počítače](./media/chef-extension-portal/add-vm-basics.png)

1. Na kartě **Zvolit velikost** vyberte velikost virtuálního počítače a pak vyberte **Vybrat**.

1. Na kartě **Nastavení** bude většina hodnot naplněna na základě hodnot, které jste vybrali na předchozích kartách. Vyberte **Extensions** (Rozšíření).

     ![Rozšíření se přidávají do virtuálních počítačů pomocí karty Nastavení](./media/chef-extension-portal/add-vm-select-extensions.png)

1. Na kartě **Rozšíření** vyberte **Přidat rozšíření**.

     ![Výběrem možnosti Přidat rozšíření přidáte rozšíření do virtuálního počítače.](./media/chef-extension-portal/add-vm-add-extension.png)

1. Na kartě **Nový prostředek** vyberte Linux Chef **Extension (1.2.3)**.

     ![Chef má rozšíření pro linuxové a windows virtuální počítače](./media/chef-extension-portal/select-linux-chef-extension.png)

1. Na kartě **Linux Chef Extension** vyberte **Vytvořit**.

1. Na kartě **Instalovat rozšíření** zadejte následující hodnoty a pak vyberte **OK**.

    - **Adresa URL serveru chef** – zadejte adresu URL serveru *https://api.chef.io/organization/mycompany*chef, která obsahuje například název organizace .
    - **Název uzlu chef** - Zadejte název uzlu chef.
    - **Spustit seznam** – zadejte seznam spuštění chef, který je přidán do počítače. Tato hodnota může zůstat prázdná.
    - **Ověření názvu klienta** - Zadejte název klienta ověření chef. například `tarcher-validator`.
    - **Ověřovací klíč** – Vyberte soubor obsahující ověřovací klíč použitý při zavádění počítačů.
    - **Konfigurační soubor klienta** – vyberte konfigurační soubor pro chef-client. Tato hodnota může zůstat prázdná.
    - **Chef Client verze** - Zadejte verzi klienta chef nainstalovat. Tato hodnota může být ponechána prázdná, která nainstaluje nejnovější verzi.
    - **Režim ověření SSL** – vyberte **možnost Žádný** nebo **Rovnocenný .** Pro ukázku nebyl vybrán *žádný.*
    - **Prostředí šéfkuchaře** – zadejte prostředí chef, jehož členem by měl být tento uzel. Tato hodnota může zůstat prázdná.
    - **Tajný klíč šifrovaného datového vaku** - Vyberte soubor obsahující tajný klíč pro šifrovaný datový vak, ke které tento počítač potřebuje přístup. Tato hodnota může zůstat prázdná.
    - **Certifikát SSL serveru Chef** – Vyberte certifikát SSL přiřazený vašemu serveru chef. Tato hodnota může zůstat prázdná.

      ![Instalace Chef Serveru na virtuální mašitu Linux](./media/chef-extension-portal/install-extension.png)

1. Když se zobrazí karta **Rozšíření,** vyberte **OK**.

1. Když se zobrazí karta **Nastavení,** vyberte **OK**.

1. Když se zobrazí karta **Vytvořit,** zobrazí se souhrn možností, které jste vybrali a zadali. Ověřte informace i **podmínky použití**a vyberte **vytvořit**.

Po dokončení procesu vytváření a nasazování virtuálního počítače s rozšířením chef oznámení označuje úspěch nebo neúspěch operace. Kromě toho se stránka prostředků pro nový virtuální počítač automaticky otevře na webu Azure Portal po jeho vytvoření.

![Instalace Chef Serveru na virtuální mašitu Linux](./media/chef-extension-portal/resource-created.png)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"] 
> [Vytvoření virtuálního počítače s Windows v Azure pomocí Chefa](chef-automation.md)