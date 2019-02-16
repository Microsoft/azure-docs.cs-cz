---
title: Instalace klienta Chefu na webu Azure Portal
description: Zjistěte, jak nasadit a nakonfigurovat klienta Chefu na webu Azure Portal
keywords: Azure, chef, devops, klienta, instalaci, portál
ms.service: virtual-machines-linux
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 05/15/2018
ms.topic: article
ms.openlocfilehash: d7728dd5c025a88f8912dca708abc45ab519ce2c
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/16/2019
ms.locfileid: "56327540"
---
# <a name="install-the-chef-client-from-the-azure-portal"></a>Instalace klienta Chefu na webu Azure Portal
Na webu Azure Portal můžete přidat rozšíření Chef klienta přímo do počítače s Linuxem nebo Windows. Tento článek vás provede procesem vytvoření nového virtuálního počítače Linux.

## <a name="prerequisites"></a>Požadavky

- **Předplatné Azure**: Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) před tím, než začnete.

- **Chef**: Pokud nemáte aktivní účet Chef, zaregistrovat [bezplatnou zkušební verzi hostované Chef](https://manage.chef.io/signup). Pokud chcete postupovat podle pokynů v tomto článku, budete potřebovat následující hodnoty z vašeho účtu Chef:
  - klíč organization_validation
  - rb
  - run_list

## <a name="install-the-chef-extension-on-a-new-linux-virtual-machine"></a>Instalace rozšíření Chefu na nový virtuální počítač Linux
V této části nejprve použijete na webu Azure portal vytvořit počítač s Linuxem. Během procesu uvidíte také tom, jak nainstalovat rozšíření Chefu na nový virtuální počítač.

1. Přejděte na web [Azure Portal](http://portal.azure.com).

1. V nabídce na levé straně vyberte **virtuálních počítačů** možnost. Pokud **virtuálních počítačů** možnost není k dispozici, vyberte **všechny služby** a pak vyberte **virtuálních počítačů**.

1. Na **virtuálních počítačů** kartu, vyberte možnost **přidat**.

    ![Přidání nového virtuálního počítače na webu Azure Portal](./media/chef-extension-portal/add-vm.png)

1. Na **Compute** kartu, vyberte požadovanou verzi operačního systému. Pro tuto ukázku **Ubuntu Server** zaškrtnuto.

1. Na **Ubuntu Server** kartu, vyberte možnost **Ubuntu Server 16.04 LTS**.

    ![Při vytváření virtuálního počítače s Ubuntu, zadejte verzi, které potřebujete](./media/chef-extension-portal/ubuntu-server-version.png)

1. Na **Ubuntu Server 16.04 LTS** kartu, vyberte možnost **vytvořit**.

    ![Ubuntu poskytuje další informace o svých produktů](./media/chef-extension-portal/create-vm.png)

1. Na **vytvořit virtuální počítač** kartu, vyberte možnost **Základy**.

1. Na **Základy** kartu, zadejte následující hodnoty a pak vyberte **OK**.

    - **Název** – zadejte název pro nový virtuální počítač.
    - **Typ disku virtuálního počítače** -zadat buď **SSD** nebo **HDD** pro typ úložného disku. Další informace o typech disků virtuálního počítače v Azure najdete v článku [vyberte typ disku](../virtual-machines/windows/disks-types.md).
    - **Uživatelské jméno** – zadejte uživatelské jméno, které je udělen oprávnění správce na virtuálním počítači.
    - **Typ ověřování** – vyberte **heslo**. Můžete také vybrat **veřejný klíč SSH**a zadat hodnotu veřejného klíče SSH. Pro účely této ukázky (a na snímcích obrazovky) **heslo** zaškrtnuto.
    - **Heslo** a **potvrzení hesla** – zadejte heslo pro tohoto uživatele.
    - **Přihlásit se přes Azure Active Directory** – vyberte **zakázané**.
    - **Předplatné** – vyberte požadované předplatné Azure, pokud máte více než jedno.
    - **Skupina prostředků** – zadejte název vaší skupiny prostředků.
    - **Umístění** – vyberte **USA – východ**.

    ![Základní informace o kartě při vytváření virtuálního počítače](./media/chef-extension-portal/add-vm-basics.png)

1. Na **zvolte velikost** kartu, vyberte velikost virtuálního počítače a pak vyberte **vyberte**.

1. Na **nastavení** kartu, většinou hodnot naplní se podle hodnoty, které jste vybrali v předchozím karty. Vyberte **Extensions** (Rozšíření).

    ![Přidání rozšíření do virtuálních počítačů na kartě nastavení](./media/chef-extension-portal/add-vm-select-extensions.png)

1. Na **rozšíření** kartu, vyberte možnost **přidat rozšíření**.

    ![Vyberte přidat rozšíření přidat rozšíření virtuálního počítače](./media/chef-extension-portal/add-vm-add-extension.png)

1. Na **nový prostředek** kartu, vyberte možnost **rozšíření Linux Chef (1.2.3)**.

    ![Chef obsahuje rozšíření pro virtuální počítače s Linuxem a Windows](./media/chef-extension-portal/select-linux-chef-extension.png)

1. Na **rozšíření Linux Chef** kartu, vyberte možnost **vytvořit**.

1. Na **nainstalovat rozšíření** kartu, zadejte následující hodnoty a pak vyberte **OK**.

    - **Adresa URL serveru Chef** – zadejte adresu URL serveru Chef, který obsahuje název organizace, například *https://api.chef.io/organization/mycompany*.
    - **Název uzlu Chef** – zadejte název uzlu Chef. Může to být libovolná hodnota.
    - **Seznam spuštění** – zadejte seznam Chef spuštění, který je přidán do počítače. To může být ponecháno prázdné.
    - **Název klienta ověření** – zadejte název Chef ověření klienta. například *tarcher validátoru*.
    - **Ověřovací klíč** – vyberte soubor obsahující ověřovací klíč použít při spuštění počítače.
    - **Soubor konfigurace klienta** – vyberte konfigurační soubor pro klienta chef. To může být ponecháno prázdné.
    - **Verze klienta Chef** – zadejte verzi klienta chef k instalaci. To může být ponecháno prázdné. Prázdná hodnota nainstaluje nejnovější verzi.
    - **Režim ověřování SSL** – vyberte buď **žádný** nebo **Peer**. *Žádný* byl vybrán pro tuto ukázku.
    - **Prostředí Chef** – zadejte prostředí Chef tohoto uzlu by měl být členem skupiny. To může být ponecháno prázdné.
    - **Šifrované tajné Databag** – vyberte soubor obsahující tajný klíč pro Databag šifrované tento počítač by měl mít přístup k. To může být ponecháno prázdné.
    - **Certifikát protokolu SSL serveru Chef** -vybrat certifikát SSL, které jsou přiřazené k vašemu serveru Chef. To může být ponecháno prázdné.

    ![Instalace Chef serveru na virtuálním počítači s Linuxem](./media/chef-extension-portal/install-extension.png)

1. Při návratu do **rozšíření** kartu, vyberte možnost **OK**.

1. Při návratu do **nastavení** kartu, vyberte možnost **OK**.

1. Při návratu do **vytvořit** karty (Tento představuje souhrn nastavení, které jste vybrali a zadat), zkontrolujte zadané informace i na **podmínky použití**a vyberte **vytvořit**.

Po dokončení procesu vytváření a nasazování virtuálního počítače s příponou Chef oznámení indikuje úspěch nebo neúspěch operace. Kromě toho na stránce prostředků pro nový virtuální počítač se automaticky otevře na portálu Azure portal je vytvořené.

![Instalace Chef serveru na virtuálním počítači s Linuxem](./media/chef-extension-portal/resource-created.png)

## <a name="next-steps"></a>Další postup

- [Vytvoření virtuálního počítače s Windows v Azure pomocí Chefu](/azure/virtual-machines/windows/chef-automation)
