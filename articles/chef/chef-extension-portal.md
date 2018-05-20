---
title: Nainstalujte klienta Chef z portálu Azure
description: Postup nasazení a konfigurace vašeho klienta Chef z portálu Azure
keywords: Azure, chef, devops, klienta, instalaci, portál
ms.service: virtual-machines-linux
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.date: 05/15/2018
ms.topic: article
ms.openlocfilehash: 52f34361d7c1f3dff47f2571a714b8be7764cc6f
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2018
---
# <a name="install-the-chef-client-from-the-azure-portal"></a>Nainstalujte klienta Chef z portálu Azure
Pokud vytváříte nebo upravujete Linux nebo Windows virtuální počítač z portálu Azure, můžete přidat rozšíření Chef k virtuálnímu počítači. Tento článek vás provede procesu pomocí nového virtuálního počítače systému Linux.

## <a name="prerequisites"></a>Požadavky
- **Předplatné Azure:** Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) před tím, než začnete.

- **Chef**: Pokud nemáte účet active Chef, si zaregistrovat [bezplatnou zkušební verzi služby hostované Chef](https://manage.chef.io/signup). Chcete-li postupovat podle pokynů v tomto článku, budete potřebovat následující hodnoty z vašeho účtu Chef: 
    - klíč organization_validation
    - RB
    - run_list

## <a name="install-the-chef-extension-on-a-new-linux-virtual-machine"></a>Nainstalujte rozšíření Chef na nový virtuální počítač Linux
V této části bude prvním použití portálu Azure vytvořit počítač s Linuxem. Během procesu dozvíte se taky, jak nainstalovat rozšíření Chef na nový virtuální počítač.

1. Vyhledejte [portál Azure](http://portal.azure.com).

1. V nabídce na levé straně vyberte **virtuální počítače** možnost. Pokud **virtuální počítače** možnost není přítomen, vyberte **všechny služby** a pak vyberte **virtuální počítače**.

1. Na **virtuální počítače** vyberte **přidat**.

    ![Přidat nový virtuální počítač na portálu Azure](./media/chef-extension-portal/add-vm.png)

1. Na **výpočetní** , vyberte požadovaný operační systém. V této ukázce **Ubuntu Server** je vybrána.

1. Na **Ubuntu Server** vyberte **Ubuntu Server 16.04 LTS**.

    ![Při vytváření virtuálního počítače Ubuntu, zadejte verzi, které potřebujete](./media/chef-extension-portal/ubuntu-server-version.png)

1. Na **Ubuntu Server 16.04 LTS** vyberte **vytvořit**.

    ![Ubuntu poskytuje další informace o svých produktech](./media/chef-extension-portal/create-vm.png)

1. Na **vytvořit virtuální počítač** vyberte **Základy**.

1. Na **Základy** , zadejte následující hodnoty a potom vyberte **OK**.

    - **Název** – zadejte název nového virtuálního počítače.
    - **Typ disku virtuálního počítače** -zadejte buď **SSD** nebo **HDD** pro typ úložiště disku. Další informace o typech disků virtuálního počítače na platformě Azure, najdete v článku [vysoce výkonné úložiště Premium a spravované disky pro virtuální počítače](/azure/virtual-machines/windows/premium-storage).
    - **Uživatelské jméno** -zadejte uživatelské jméno, které jsou udělena oprávnění správce na virtuálním počítači.
    - **Typ ověřování** – vyberte **heslo**. Můžete také vybrat **veřejný klíč SSH**a zadejte hodnotu veřejného klíče SSH. Pro účely tuto ukázku (a na snímcích obrazovky) **heslo** je vybrána.
    - **Heslo** a **potvrzení hesla** -zadat heslo pro uživatele.
    - **Přihlásit se přes Azure Active Directory** – vyberte **zakázané**.
    - **Předplatné** -vyberte požadované předplatné Azure, pokud máte více než jednou.
    - **Skupina prostředků** – zadejte název skupiny prostředků.
    - **Umístění** – vyberte **východní USA**.

    ![Základní informace o kartě pro vytvoření virtuálního počítače](./media/chef-extension-portal/add-vm-basics.png)

1. Na **zvolte velikost** vyberte velikost virtuálního počítače a potom vyberte **vyberte**.

1. Na **nastavení** kartě, většinou hodnot vyplní pro založené na hodnotách, které jste vybrali v předchozím karty. Vyberte **rozšíření**.

    ![Rozšíření se přidají k virtuálním počítačům prostřednictvím karta nastavení](./media/chef-extension-portal/add-vm-select-extensions.png)

1. Na **rozšíření** vyberte **přidat rozšíření**.

    ![Vyberte Přidat příponu přidat rozšíření do virtuálního počítače](./media/chef-extension-portal/add-vm-add-extension.png)

1. Na **nový prostředek** vyberte **Linux Chef rozšíření (1.2.3)**.

    ![Chef má rozšíření pro virtuální počítače Linux a Windows](./media/chef-extension-portal/select-linux-chef-extension.png)

1. Na **Linux Chef rozšíření** vyberte **vytvořit**.

1. Na **nainstalovat rozšíření** , zadejte následující hodnoty a potom vyberte **OK**.

    - **Adresa URL serveru Chef** – zadejte adresu URL serveru Chef, který zahrnuje název organizace. Mohu použít *https://api.chef.io/organization/hessco* ukázce.
    - **Název uzlu Chef** – zadejte název uzlu Chef. To může být libovolná hodnota.
    - **Spustit seznamu** -zadejte Chef spustit seznamu, který se přidá k počítači. To může být prázdné.
    - **Název klienta ověření** – zadejte název Chef ověření klienta. Mohu použít *tarcher ověření* ukázce.
    - **Ověřovací klíč** – vyberte soubor obsahující ověřovací klíč použít při zavádění vašich počítačů. 
    - **Soubor konfigurace klienta** -vyberte konfigurační soubor pro klienta chef. To může být prázdné.
    - **Verze klienta Chef** -zadejte verzi klienta chef k instalaci. Na prázdnou hodnotu způsobí, že k instalaci na nejnovější verzi. To může být prázdné.
    - **Ověření režimu protokolu SSL** -vyberte buď **žádné** nebo **sdílené**. Zvolení *žádné* ukázce.
    - **Prostředí Chef** -zadejte prostředí Chef tento uzel musí být členem skupiny. To může být prázdné.
    - **Šifrované tajné Databag** – vyberte soubor obsahující tajný klíč pro Databag šifrované tento počítač by měl mít přístup k. To může být prázdné.
    - **Certifikát protokolu SSL serveru Chef** – vyberte certifikát SSL, které jsou přiřazené k serveru Chef. To může být prázdné.

    ![Instalace serveru Chef na virtuální počítač s Linuxem](./media/chef-extension-portal/install-extension.png)

1. Když se vrátíte na **rozšíření** vyberte **OK**.

1. Když se vrátíte na **nastavení** vyberte **OK**.

1. Když se vrátíte na **vytvořit** karta (který představuje souhrn možnosti jste vybrali a zadat), zkontrolujte zadané informace o společně s **podmínky použití**a vyberte **vytvořit**.

Po dokončení procesu vytváření a nasazení virtuálního počítače s příponou Chef oznámení udává úspěch nebo selhání operace. Kromě toho stránce prostředků pro nový virtuální počítač automaticky otevře na portálu Azure je po vytvoření.

![Instalace serveru Chef na virtuální počítač s Linuxem](./media/chef-extension-portal/resource-created.png)

## <a name="next-steps"></a>Další postup
* [Vytvoření virtuálního počítače s Windows v Azure pomocí Chef](/azure/virtual-machines/windows/chef-automation)