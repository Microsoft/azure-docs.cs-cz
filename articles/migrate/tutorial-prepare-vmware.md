---
title: Příprava virtuálních počítačů VMware pro posouzení/migraci pomocí Azure Migrate
description: Přečtěte si, jak připravit na posouzení/migraci virtuálních počítačů VMware pomocí Azure Migrate.
ms.topic: tutorial
ms.date: 11/19/2019
ms.custom: mvc
ms.openlocfilehash: 25dab303ce62e33a09346d14c0a08a43b715075d
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2020
ms.locfileid: "76989130"
---
# <a name="prepare-vmware-vms-for-assessment-and-migration-to-azure"></a>Příprava virtuálních počítačů VMware pro posouzení a migraci do Azure

Tento článek vám pomůže připravit se na posouzení nebo migraci místních virtuálních počítačů VMware do Azure pomocí [Azure Migrate](migrate-services-overview.md).

[Azure Migrate](migrate-overview.md) poskytuje centrum nástrojů, které vám pomůžou zjišťovat, vyhodnocovat a migrovat aplikace, infrastrukturu a úlohy do Microsoft Azure. Centrum zahrnuje nástroje pro Azure Migrate a nabídky nezávislého výrobce softwaru (ISV) od jiných výrobců.


Tento kurz je první v řadě, ve kterém se dozvíte, jak vyhodnocovat a migrovat virtuální počítače VMware. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Připravte Azure pro práci s Azure Migrate.
> * Připravte VMware na posouzení virtuálních počítačů.
> * Připravte VMware pro migraci virtuálních počítačů.

> [!NOTE]
> Kurzy ukazují nejjednodušší cestu k nasazení scénáře. Jsou užitečné v případě, že se naučíte nastavit nasazení a jako rychlý zkušební koncept. Kurzy používají výchozí možnosti, pokud je to možné, a nezobrazují všechna možná nastavení a cesty. Podrobné pokyny najdete v tématu How to TOS for VMware Assessment and Migration.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/) před tím, než začnete.


## <a name="prepare-azure"></a>Příprava Azure

Tato oprávnění budete potřebovat.

**Úkol** | **Oprávnění**
--- | ---
**Vytvoření projektu Azure Migrate** | Váš účet Azure potřebuje oprávnění k vytvoření projektu.
**Registrace zařízení Azure Migrate** | Azure Migrate využívá odlehčené Azure Migrate zařízení k vyhodnocení virtuálních počítačů VMware pomocí posouzení Azure Migrate serveru a spouštění migrace virtuálních počítačů VMware bez [agenta](server-migrate-overview.md) pomocí migrace serveru Azure Migrate. Toto zařízení vyhledá virtuální počítače a odešle údaje o metadatech a výkonu virtuálních počítačů do Azure Migrate.<br/><br/>Při registraci zařízení se u předplatného, které jste zvolili v části zařízení – Microsoft. OffAzure, Microsoft. Migruj a Microsoft. klíčů, zaregistrují následující poskytovatelé registrace. Když zaregistrujete poskytovatele prostředků, nakonfigurujete vaše předplatné, aby fungovalo s poskytovatelem prostředků. Pokud chcete zaregistrovat poskytovatele prostředků, potřebujete roli přispěvatel nebo Owner v předplatném.<br/><br/> V rámci připojování Azure Migrate vytvoří dvě aplikace Azure Active Directory (Azure AD):<br/> – První aplikace se používá pro komunikaci (ověřování a autorizaci) mezi agenty běžícími na zařízení s příslušnými službami běžícími na Azure. Tato aplikace nemá oprávnění k tomu, aby zavedla volání ARM nebo přístup RBAC k jakémukoli prostředku.<br/> – Druhá aplikace se používá výhradně pro přístup k trezoru klíčů vytvořenému v předplatném uživatele pro migraci bez agentů. Je k dispozici s přístupem RBAC na Azure Key Vault (vytvořeným v tenantovi zákazníka) při zahájení zjišťování ze zařízení.
**Vytvoření Key Vault** | Pokud chcete migrovat virtuální počítače VMware pomocí migrace serveru Azure Migrate, Azure Migrate vytvoří Key Vault pro správu přístupových klíčů k účtu úložiště replikace v rámci vašeho předplatného. K vytvoření trezoru potřebujete oprávnění přiřazení role ve skupině prostředků, ve které se nachází Azure Migrate projekt.






### <a name="assign-permissions-to-create-project"></a>Přiřadit oprávnění k vytvoření projektu

1. V Azure Portal otevřete předplatné a vyberte **řízení přístupu (IAM)** .
2. V části **kontrolovat přístup**Najděte příslušný účet a kliknutím na něj Zobrazte oprávnění.
3. Měli byste mít oprávnění **Přispěvatel** nebo **Owner** .
    - Pokud jste právě vytvořili bezplatný účet Azure, jste vlastníkem svého předplatného.
    - Pokud nejste vlastníkem předplatného, pracujte s vlastníkem a přiřaďte roli.

### <a name="assign-permissions-to-register-the-appliance"></a>Přiřazení oprávnění k registraci zařízení

Pokud chcete zařízení zaregistrovat, přiřadíte jim oprávnění Azure Migrate k vytváření aplikací Azure AD během registrace zařízení. Oprávnění lze přiřadit pomocí jedné z následujících metod:

- Tenant nebo globální správce může udělit oprávnění uživatelům v tenantovi, aby mohli vytvářet a registrovat aplikace služby Azure AD.
- Tenant nebo globální správce může k účtu přiřadit roli vývojáře aplikace (která má oprávnění).

> [!NOTE]
> - Aplikace nemají žádná jiná přístupová oprávnění k předplatnému, kromě výše popsaných výše.
> - Tato oprávnění budete potřebovat, jenom když zaregistrujete nové zařízení. Po nastavení zařízení můžete oprávnění odebrat.


#### <a name="grant-account-permissions"></a>Udělení oprávnění účtu

Tenant nebo globální správce může udělit oprávnění následujícím způsobem.

1. V Azure AD by měl tenant/globální správce přejít na **Azure Active Directory** > **Uživatelé** > **uživatelských nastavení**.
2. Správce by měl nastavit **Registrace aplikací** **Ano**. Toto je výchozí nastavení, které není citlivé. [Další informace](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).

    ![Oprávnění služby Azure AD](./media/tutorial-prepare-vmware/aad.png)



#### <a name="assign-application-developer-role"></a>Přiřazení role vývojáře aplikace

Tenant/globální správce může přiřadit roli vývojář aplikace k účtu. [Další informace](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal).

### <a name="assign-permissions-to-create-a-key-vault"></a>Přiřazení oprávnění k vytvoření Key Vault

Chcete-li povolit Azure Migrate vytvoření Key Vault, přiřaďte oprávnění následujícím způsobem:

1. Ve skupině prostředků v Azure Portal vyberte **řízení přístupu (IAM)** .
2. V části **kontrolovat přístup**Najděte příslušný účet a kliknutím na něj Zobrazte oprávnění.

    - Aby bylo možné spustit vyhodnocení serveru, jsou dostatečná oprávnění **přispěvatele** .
    - Pokud chcete spustit migraci serveru bez agenta, měli byste mít oprávnění **vlastníka** (nebo **přispěvatele** a **Správce přístupu uživatelů**).

3. Pokud nemáte požadovaná oprávnění, požádejte je od vlastníka skupiny prostředků.



## <a name="prepare-for-vmware-vm-assessment"></a>Příprava na posouzení virtuálních počítačů VMware

K přípravě na posouzení virtuálních počítačů VMware potřebujete:

- **Ověřte nastavení VMware**. Ujistěte se, že vCenter Server a virtuální počítače, které chcete migrovat, splňují požadavky.
- **Nastavte účet posouzení**. Azure Migrate musí mít přístup k vCenter Server, aby se zjistily virtuální počítače pro posouzení.
- **Ověřte požadavky na zařízení**. Ověřte požadavky na nasazení Azure Migrate zařízení, které se používá pro posouzení.

### <a name="verify-vmware-settings"></a>Ověření nastavení VMware

1. [Kontrolu](migrate-support-matrix-vmware.md#vmware-requirements) Požadavky na server VMware pro posouzení.
2. [Ujistěte se, že](migrate-support-matrix-vmware.md#port-access) porty, které potřebujete, jsou otevřené v vCenter Server.
3. V vCenter Server se ujistěte, že váš účet má oprávnění k vytvoření virtuálního počítače pomocí souboru sady vajíček. Zařízení Azure Migrate nasadíte jako virtuální počítač VMware pomocí souboru sady vajíček.


### <a name="set-up-an-account-for-assessment"></a>Nastavení účtu pro posouzení

Azure Migrate musí mít přístup k vCenter Server, aby se zjistily virtuální počítače pro účely posouzení a migrace bez agentů.

- Pokud máte v úmyslu zjišťovat aplikace nebo vizualizovat závislost bez agentů, vytvořte účet vCenter Server s přístupem jen pro čtení společně s oprávněními povolenými pro **virtuální počítače** > **operacemi hostů**.

  ![oprávnění účtu vCenter Server](./media/tutorial-prepare-vmware/vcenter-server-permissions.png)

- Pokud neplánujete funkci zjišťování aplikací a vizualizaci závislostí bez agentů, nastavte pro vCenter Server účet jen pro čtení.

### <a name="verify-appliance-settings-for-assessment"></a>Ověření nastavení zařízení pro posouzení

Před nastavením zařízení Azure Migrate a zahájením posouzení v dalším kurzu připravte na nasazení zařízení.

1. [Ověřte](migrate-appliance.md#appliance---vmware) požadavky na zařízení pro virtuální počítače VMware.
2. [Zkontrolujte](migrate-appliance.md#url-access) adresy URL Azure, ke kterým bude zařízení potřebovat přístup. Pokud používáte bránu firewall nebo proxy založenou na adrese URL, ujistěte se, že umožňuje přístup k požadovaným adresám URL.
3. [Zkontrolujte](migrate-appliance.md#collected-data---vmware) , že zařízení bude během zjišťování a hodnocení shromažďováno.
4. [Poznamenejte si](migrate-support-matrix-vmware.md#port-access) požadavky na přístup k portu pro dané zařízení.




## <a name="prepare-for-agentless-vmware-migration"></a>Příprava na migraci VMware bez agentů

Projděte si požadavky na migraci virtuálních počítačů VMware bez agenta.

1. [Kontrola](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers) Požadavky na server VMware a [oprávnění](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers) , která Azure Migrate potřebují pro přístup k vCenter Server k migraci bez agenta pomocí migrace Azure Migrate serveru.
2. [Přečtěte](migrate-support-matrix-vmware-migration.md#agentless-vmware-vms) si požadavky na virtuální počítače VMware, které chcete migrovat do Azure pomocí migrace bez agentů.
4. [Přečtěte](migrate-support-matrix-vmware-migration.md#agentless-azure-migrate-appliance) si požadavky na používání zařízení Azure Migrate pro migraci bez agentů.
5. Poznamenejte si [přístup k adresám URL](migrate-appliance.md#url-access) a [portům](migrate-support-matrix-vmware-migration.md#agentless-ports) potřebným k migraci bez agenta.


## <a name="prepare-for-agent-based-vmware-migration"></a>Příprava na migraci VMware založenou na agentech

Projděte si požadavky na migraci virtuálních počítačů VMware [založených na agentech](server-migrate-overview.md) .

1. [Kontrola](migrate-support-matrix-vmware-migration.md#agent-based-vmware-servers) Požadavky na server VMware a oprávnění Azure Migrate musí mít přístup k vCenter Server pro migraci na základě agentů pomocí Azure Migrate migrace serveru.
2. [Přečtěte](migrate-support-matrix-vmware-migration.md#agent-based-vmware-vms) si požadavky na virtuální počítače VMware, které chcete migrovat do Azure pomocí migrace založené na agentech, včetně instalace služby mobility na každý virtuální počítač, který chcete migrovat.
3. Migrace založené na agentech používá zařízení replikace:
    - [Projděte si](migrate-replication-appliance.md#appliance-requirements) požadavky na nasazení pro zařízení replikace a [Možnosti](migrate-replication-appliance.md#mysql-installation) pro instalaci MySQL na zařízení.
    - Zkontrolujte [adresu URL](migrate-replication-appliance.md#url-access) a požadavky na přístup k [portu](migrate-replication-appliance.md#port-access) pro zařízení replikace.
    
## <a name="next-steps"></a>Další kroky

V tomto kurzu jste:

> [!div class="checklist"]
> * Nastavte oprávnění Azure.
> * Příprava VMware pro posouzení a migraci


Pokračujte druhým kurzem a nastavte projekt Azure Migrate a vyhodnoťte virtuální počítače VMware pro migraci do Azure.

> [!div class="nextstepaction"]
> [Posouzení virtuálních počítačů VMware](./tutorial-assess-vmware.md)
