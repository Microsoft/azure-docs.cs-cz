---
title: Instalace klienta systému pro sestavovatele z Azure Portal
description: Přečtěte si, jak nasadit a nakonfigurovat klienta z Azure Portal
keywords: Azure, DevOps, klient, instalace, portál
ms.date: 05/15/2018
ms.topic: article
ms.openlocfilehash: f8707c2fe39fb794381af298c24d27704b1ec255
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158255"
---
# <a name="install-the-chef-client-from-the-azure-portal"></a>Instalace klienta systému pro sestavovatele z Azure Portal
Rozšíření klienta pro systém pro rozhraní systému pro počítače můžete přidat přímo do počítače se systémem Linux nebo Windows z Azure Portal. Tento článek vás provede procesem použití nového virtuálního počítače se systémem Linux.

## <a name="prerequisites"></a>Požadavky

- **Předplatné Azure:** Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) před tím, než začnete.

- **Samoobslužný: Pokud**nemáte aktivní účet systému, zaregistrujte si [bezplatnou zkušební verzi hostovaného systému](https://manage.chef.io/signup). Chcete-li postupovat podle pokynů v tomto článku, budete potřebovat následující hodnoty z vašeho účtu aplikace.
  - organization_validation klíč
  - rb
  - run_list

## <a name="install-the-chef-extension-on-a-new-linux-virtual-machine"></a>Instalace rozšíření systému pro instalaci do nového virtuálního počítače se systémem Linux
V této části nejprve použijete Azure Portal k vytvoření počítače se systémem Linux. V průběhu tohoto procesu uvidíte také, jak nainstalovat rozšíření pro sestavovatele systému do nového virtuálního počítače.

1. Přejděte na web [Azure Portal](https://portal.azure.com).

1. V nabídce na levé straně vyberte možnost **virtuální počítače** . Pokud není k dispozici možnost **virtuální počítače** , vyberte **všechny služby** a pak vyberte **virtuální počítače**.

1. Na kartě **virtuální počítače** vyberte **Přidat**.

    ![Přidat nový virtuální počítač do Azure Portal](./media/chef-extension-portal/add-vm.png)

1. Na kartě **výpočty** vyberte požadovaný operační systém. Pro tuto ukázku je vybrána možnost **Server Ubuntu** .

1. Na kartě **Server Ubuntu** vyberte **Ubuntu Server 16,04 LTS**.

    ![Při vytváření virtuálního počítače s Ubuntu zadejte verzi, kterou potřebujete.](./media/chef-extension-portal/ubuntu-server-version.png)

1. Na kartě **LTS serveru Ubuntu 16,04** vyberte **vytvořit**.

    ![Ubuntu poskytuje další informace o jejich produktu](./media/chef-extension-portal/create-vm.png)

1. Na kartě **vytvořit virtuální počítač** vyberte **základy**.

1. Na kartě **základy** zadejte následující hodnoty a pak vyberte **OK**.

   - **Název** – zadejte název nového virtuálního počítače.
   - **Typ disku virtuálního počítače** – zadejte buď **SSD** , nebo **HDD** pro typ disku úložiště. Další informace o typech disků virtuálních počítačů v Azure najdete v článku [Výběr typu disku](../virtual-machines/windows/disks-types.md).
   - **Uživatelské jméno** – zadejte uživatelské jméno, kterému je uděleno oprávnění správce na virtuálním počítači.
   - **Typ ověřování** – vyberte **heslo**. Můžete také vybrat možnost **veřejný klíč SSH**a zadejte hodnotu veřejného klíče SSH. Pro účely této ukázky (a na snímcích obrazovky) se vybere **heslo** .
   - **Heslo** a **potvrzení hesla** – zadejte heslo pro uživatele.
   - **Přihlaste se pomocí Azure Active Directory** – vyberte **zakázáno**.
   - **Předplatné** – vyberte požadované předplatné Azure, pokud máte víc než jeden.
   - **Skupina prostředků** – zadejte název vaší skupiny prostředků.
   - **Umístění** – vyberte **východní USA**.

     ![Karta základy pro vytvoření virtuálního počítače](./media/chef-extension-portal/add-vm-basics.png)

1. Na kartě **Zvolte velikost** vyberte velikost virtuálního počítače a pak vyberte **Vybrat**.

1. Na kartě **Nastavení** se pro vás naplní většina hodnot na základě hodnot, které jste vybrali na předchozích kartách. Vyberte **Extensions** (Rozšíření).

     ![Rozšíření se do virtuálních počítačů přidají přes kartu nastavení.](./media/chef-extension-portal/add-vm-select-extensions.png)

1. Na kartě **rozšíření** vyberte **Přidat rozšíření**.

     ![Pokud chcete přidat rozšíření k virtuálnímu počítači, vyberte přidat rozšíření.](./media/chef-extension-portal/add-vm-add-extension.png)

1. Na kartě **nový prostředek** vyberte Linux. **rozšíření (1.2.3)** .

     ![Počítač s rozšířeními pro virtuální počítače se systémy Linux a Windows](./media/chef-extension-portal/select-linux-chef-extension.png)

1. Na kartě **rozšíření pro Linux pro Linux** vyberte **vytvořit**.

1. Na kartě **instalovat rozšíření** zadejte následující hodnoty a pak vyberte **OK**.

    - **Adresa URL serveru** pro vyžádání – zadejte adresu URL serveru s názvem, který obsahuje název organizace, například *https://api.chef.io/organization/mycompany* .
    - **Název uzlu pro název uzlu** – zadejte název uzlu s názvem. Může to být libovolná hodnota.
    - **Seznam spuštění** – zadejte seznam spuštění pro systém pro použití, který se přidá k počítači. To může být ponecháno prázdné.
    - **Název ověřovacího klienta** – zadejte název ověřovacího klienta s názvem. například *tarcher-validátor*.
    - **Ověřovací klíč** – vyberte soubor obsahující ověřovací klíč, který se používá při zavádění vašich počítačů.
    - **Konfigurační soubor klienta** – vyberte konfigurační soubor pro klienta aplikace. To může být ponecháno prázdné.
    - **Verze klienta** systému pro sestavovatele – zadejte verzi klienta systému, kterou chcete nainstalovat. To může být ponecháno prázdné. Prázdná hodnota nainstaluje nejnovější verzi.
    - **Režim ověřování SSL** – vyberte buď **none** , nebo **peer**. Pro ukázku se nevybral *žádný* .
    - **Prostředí** systému pro navázání – zadejte prostředí pro systém \ tento uzel by měl být členem. To může být ponecháno prázdné.
    - **Šifrovaný tajný klíč Databag** – vyberte soubor obsahující tajný klíč pro šifrované Databag, ke kterému má tento počítač přístup. To může být ponecháno prázdné.
    - **Certifikát SSL serveru** pro zapsání – vyberte certifikát SSL přiřazený k vašemu serveru s vlastním serverem. To může být ponecháno prázdné.

      ![Instalace serveru systému pro instalaci do virtuálního počítače se systémem Linux](./media/chef-extension-portal/install-extension.png)

1. Až se vrátíte na kartu **rozšíření** , vyberte **OK**.

1. Až se vrátíte na kartu **Nastavení** , vyberte **OK**.

1. Při návratu na kartu **vytvořit** (představuje souhrn možností, které jste vybrali a zadali), ověřte informace i **podmínky použití**a vyberte **vytvořit**.

Po dokončení procesu vytvoření a nasazení virtuálního počítače s rozšířením pro aplikaci pro počítače se zobrazí oznámení o úspěšném nebo neúspěšném provedení operace. Kromě toho se stránka prostředků pro nový virtuální počítač automaticky otevře v Azure Portal po jeho vytvoření.

![Instalace serveru systému pro instalaci do virtuálního počítače se systémem Linux](./media/chef-extension-portal/resource-created.png)

## <a name="next-steps"></a>Další kroky

- [Vytvoření virtuálního počítače s Windows v Azure pomocí systému pro vytváření počítačů](/azure/virtual-machines/windows/chef-automation)
