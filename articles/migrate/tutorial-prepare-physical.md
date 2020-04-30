---
title: Příprava fyzických serverů pro posouzení nebo migraci pomocí Azure Migrate
description: Přečtěte si, jak připravit na posouzení/migraci fyzických serverů s Azure Migrate.
ms.topic: tutorial
ms.date: 04/15/2020
ms.custom: mvc
ms.openlocfilehash: 539e25f8b6cc92674fef567de6e6de16d0a9394a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "81535277"
---
# <a name="prepare-for-assessment-and-migration-of-physical-servers-to-azure"></a>Příprava na posouzení a migraci fyzických serverů do Azure

Tento článek popisuje, jak připravit na posouzení místních fyzických serverů s [Azure Migrate](migrate-services-overview.md).

[Azure Migrate](migrate-overview.md) poskytuje centrum nástrojů, které vám pomůžou zjišťovat, vyhodnocovat a migrovat aplikace, infrastrukturu a úlohy do Microsoft Azure. Centrum zahrnuje nástroje pro Azure Migrate a nabídky nezávislého výrobce softwaru (ISV) od jiných výrobců. 

Tento kurz je první v řadě, kde se dozvíte, jak vyhodnotit fyzické servery s Azure Migrate. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Připravte Azure. Nastavte oprávnění pro účet a prostředky Azure pro práci s Azure Migrate.
> * Připravte místní fyzické servery pro posouzení serveru.


> [!NOTE]
> Kurzy vám ukážou nejjednodušší cestu nasazení pro scénář, abyste mohli rychle nastavit zkušební verzi. Kurzy používají výchozí možnosti, pokud je to možné, a nezobrazují všechna možná nastavení a cesty. Podrobné pokyny najdete v tématu Postupy pro vyhodnocování fyzických serverů.


Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/) před tím, než začnete.


## <a name="prepare-azure-for-server-assessment"></a>Příprava Azure na server Assessment

Nastavte Azure pro práci s Azure Migrate. 

**Úkol** | **Zobrazí** 
--- | --- 
**Vytvoření projektu Azure Migrate** | Váš účet Azure potřebuje k vytvoření projektu oprávnění přispěvatele nebo vlastníka. 
**Registrovat poskytovatele prostředků (jenom posouzení)** | Azure Migrate využívá odlehčené Azure Migrate zařízení ke zjišťování a vyhodnocení počítačů pomocí Azure Migrate: posouzení serveru.<br/><br/> Při registraci zařízení se poskytovatelé prostředků zaregistrovali u předplatného, které jste zvolili v zařízení. [Další informace](migrate-appliance-architecture.md#appliance-registration).<br/><br/> Pokud chcete zaregistrovat poskytovatele prostředků, potřebujete roli přispěvatel nebo Owner v předplatném.
**Vytvoření aplikace Azure AD (jenom posouzení)** | Při registraci zařízení Azure Migrate vytvoří aplikaci Azure Active Directory (Azure AD), která se používá ke komunikaci mezi agenty běžícími na zařízení s příslušnými službami běžícími na Azure. [Další informace](migrate-appliance-architecture.md#appliance-registration).<br/><br/> Potřebujete oprávnění k vytváření aplikací Azure AD (dostupných v rámci role vývojář aplikace).


### <a name="assign-permissions-to-create-project"></a>Přiřadit oprávnění k vytvoření projektu 

Ověřte, že máte oprávnění k vytvoření projektu Azure Migrate.

1. V Azure Portal otevřete předplatné a vyberte **řízení přístupu (IAM)**.
2. V části **kontrolovat přístup**Najděte příslušný účet a kliknutím na něj Zobrazte oprávnění.
3. Měli byste mít oprávnění **Přispěvatel** nebo **Owner** .
    - Pokud jste právě vytvořili bezplatný účet Azure, jste vlastníkem svého předplatného.
    - Pokud nejste vlastníkem předplatného, pracujte s vlastníkem a přiřaďte roli.


### <a name="assign-permissions-to-register-the-appliance"></a>Přiřazení oprávnění k registraci zařízení 

Můžete přiřadit oprávnění pro Azure Migrate vytvoření aplikace Azure AD během registrace zařízení pomocí jedné z následujících metod:

- Tenant nebo globální správce může udělit oprávnění uživatelům v tenantovi, aby mohli vytvářet a registrovat aplikace služby Azure AD.
- Tenant nebo globální správce může k účtu přiřadit roli vývojáře aplikace (která má oprávnění).

> [!NOTE]
> - Aplikace nemá žádná jiná přístupová oprávnění k předplatnému, kromě výše popsaných výše.
> - Tato oprávnění budete potřebovat, jenom když zaregistrujete nové zařízení. Po nastavení zařízení můžete oprávnění odebrat.


#### <a name="grant-account-permissions"></a>Udělení oprávnění účtu

Tenant nebo globální správce může udělit oprávnění následujícím způsobem:

1. V Azure AD by měl tenant nebo globální správce přejít na **Azure Active Directory** > **Users** > **uživatelských nastavení**uživatelů.
2. Správce by měl nastavit **Registrace aplikací** **Ano**.

    ![Oprávnění služby Azure AD](./media/tutorial-prepare-hyper-v/aad.png)

> [!NOTE]
> Toto je výchozí nastavení, které není citlivé. [Další informace](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).

#### <a name="assign-application-developer-role"></a>Přiřazení role vývojáře aplikace

Tenant/globální správce může přiřadit roli vývojář aplikace k účtu. [Další informace](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal).


## <a name="prepare-azure-for-physical-server-migration"></a>Příprava migrace pro Azure na fyzické servery

Připravte Azure na migraci fyzických serverů pomocí migrace serveru.

**Úkol** | **Zobrazí**
--- | ---
**Vytvoření projektu Azure Migrate** | Váš účet Azure potřebuje k vytvoření projektu oprávnění přispěvatele nebo vlastníka.
**Ověření oprávnění pro účet Azure** | Váš účet Azure potřebuje oprávnění k vytvoření virtuálního počítače a zápis na spravovaný disk Azure.
**Vytvoření sítě Azure** | Nastavte síť v Azure.


### <a name="assign-permissions-to-create-project"></a>Přiřadit oprávnění k vytvoření projektu

1. V Azure Portal otevřete předplatné a vyberte **řízení přístupu (IAM)**.
2. V části **kontrolovat přístup**Najděte příslušný účet a kliknutím na něj Zobrazte oprávnění.
3. Měli byste mít oprávnění **Přispěvatel** nebo **Owner** .
    - Pokud jste právě vytvořili bezplatný účet Azure, jste vlastníkem svého předplatného.
    - Pokud nejste vlastníkem předplatného, pracujte s vlastníkem a přiřaďte roli.


### <a name="assign-azure-account-permissions"></a>Přiřazení oprávnění účtu Azure

Přiřaďte roli Přispěvatel virtuálních počítačů k účtu Azure. To poskytuje oprávnění k těmto akcím:

    - Vytvoření virtuálního počítače ve vybrané skupině prostředků
    - Vytvoření virtuálního počítače ve vybrané virtuální síti
    - Zapište na spravovaný disk Azure. 

### <a name="create-an-azure-network"></a>Vytvoření sítě Azure

[Nastavte](../virtual-network/manage-virtual-network.md#create-a-virtual-network) Azure Virtual Network (VNET). Při replikaci do Azure se vytvoří virtuální počítače Azure a připojí se k virtuální síti Azure, kterou určíte při nastavování migrace.


## <a name="prepare-for-physical-server-assessment"></a>Příprava na posouzení fyzického serveru

K přípravě na fyzické posouzení serveru musíte ověřit nastavení fyzického serveru a ověřit nastavení pro nasazení zařízení:

### <a name="verify-physical-server-settings"></a>Ověřit nastavení fyzického serveru

1. Ověřte [požadavky na fyzický server](migrate-support-matrix-physical.md#physical-server-requirements) pro vyhodnocování serveru.
2. Ujistěte se, že jsou na fyzických serverech otevřené [požadované porty](migrate-support-matrix-physical.md#port-access) .


### <a name="verify-appliance-settings"></a>Ověření nastavení zařízení

Před nastavením zařízení Azure Migrate a zahájením posouzení v dalším kurzu připravte na nasazení zařízení.

1. [Ověřte](migrate-appliance.md#appliance---physical) požadavky na zařízení pro fyzické servery.
2. Zkontrolujte adresy URL Azure, které zařízení potřebuje pro přístup k [veřejným](migrate-appliance.md#public-cloud-urls) a [státním](migrate-appliance.md#government-cloud-urls) cloudům.
3. [Zkontrolujte](migrate-appliance.md#collected-data---vmware) , že se zařízení bude shromažďovat během zjišťování a posouzení.
4. [Poznamenejte si](migrate-support-matrix-physical.md#port-access) požadavky na přístup k portu fyzický server posouzení.


### <a name="set-up-an-account-for-physical-server-discovery"></a>Nastavení účtu pro zjišťování fyzického serveru

Azure Migrate potřebuje oprávnění ke zjišťování místních serverů.

- **Windows:** Nastavte místní uživatelský účet na všech serverech Windows, které chcete zahrnout do zjišťování. Uživatelský účet musí být přidán do následujících skupin: – Uživatelé vzdálené správy – Performance Monitor Users-Performance Log Users
- **Linux:** Na serverech se systémem Linux, které chcete zjistit, potřebujete kořenový účet.

## <a name="prepare-for-physical-server-migration"></a>Příprava na migraci fyzického serveru

Projděte si požadavky na migraci fyzických serverů.

> [!NOTE]
> Při migraci fyzických počítačů Azure Migrate: Migrace serveru používá stejnou architekturu replikace jako zotavení po havárii na základě agenta ve službě Azure Site Recovery a některé součásti sdílejí stejný základ kódu. Část obsahu může odkazovat na dokumentaci Site Recovery.

- [Zkontrolujte](migrate-support-matrix-physical-migration.md#physical-server-requirements) požadavky na fyzický server pro migraci.
- Azure Migrate: Migrace serveru používá replikační Server pro migraci fyzického serveru:
    - [Projděte si](migrate-replication-appliance.md#appliance-requirements) požadavky na nasazení pro zařízení replikace a [Možnosti](migrate-replication-appliance.md#mysql-installation) pro instalaci MySQL na zařízení.
    - Zkontrolujte [adresy URL Azure](migrate-appliance.md#url-access) vyžadované pro zařízení replikace pro přístup k veřejným a státním cloudům.
    - Projděte si [port] (migrace-replikace-zařízení. MD # port-přístup) požadavky na přístup pro zařízení replikace.




## <a name="next-steps"></a>Další kroky

V tomto kurzu jste:

> [!div class="checklist"]
> * Nastavte oprávnění účtu Azure.
> * Připravte fyzické servery na posouzení.

Přejděte k dalšímu kurzu a vytvořte projekt Azure Migrate a vyhodnoťte fyzické servery pro migraci do Azure.

> [!div class="nextstepaction"]
> [Vyhodnocení fyzických serverů](./tutorial-assess-physical.md)
