---
title: Příprava virtuálních počítačů VMware pro posouzení/migraci pomocí Azure Migrate
description: Přečtěte si, jak připravit na posouzení/migraci virtuálních počítačů VMware pomocí Azure Migrate.
ms.topic: tutorial
ms.date: 04/19/2020
ms.custom: mvc
ms.openlocfilehash: a3f9716d6302c41f139d9a2a8b1f994b79afd199
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "81677286"
---
# <a name="prepare-vmware-vms-for-assessment-and-migration-to-azure"></a>Příprava virtuálních počítačů VMware na vyhodnocení a migraci do Azure

Tento článek vám pomůže připravit se na posouzení nebo migraci místních virtuálních počítačů VMware do Azure pomocí [Azure Migrate](migrate-services-overview.md).



Tento kurz je první v řadě, ve kterém se dozvíte, jak vyhodnocovat a migrovat virtuální počítače VMware. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Připravte Azure pro práci s Azure Migrate.
> * Připravte VMware for VM Assessment pomocí Azure Migrate: Server Assessment Tool.
> * Připravte VMware pro migraci virtuálních počítačů pomocí nástroje Azure Migrate: Server pro migraci. 

> [!NOTE]
> Kurzy ukazují nejjednodušší cestu k nasazení scénáře. Jsou užitečné v případě, že se naučíte nastavit nasazení a jako rychlý zkušební koncept. Kurzy používají výchozí možnosti, pokud je to možné, a nezobrazují všechna možná nastavení a cesty. 

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/) před tím, než začnete.


## <a name="prepare-azure"></a>Příprava Azure

Tato oprávnění pro tyto úlohy budete potřebovat v Azure, abyste mohli vyhodnotit nebo migrovat virtuální počítače VMware.

**Úkol** | **Zobrazí** 
--- | --- 
**Vytvoření projektu Azure Migrate** | Váš účet Azure potřebuje k vytvoření projektu oprávnění přispěvatele nebo vlastníka. 
**Registrovat poskytovatele prostředků** | Azure Migrate využívá odlehčené Azure Migrate zařízení ke zjišťování a hodnocení virtuálních počítačů VMware a k jejich migraci do Azure pomocí Azure Migrate: posouzení serveru.<br/><br/> Při registraci zařízení se poskytovatelé prostředků zaregistrovali u předplatného, které jste zvolili v zařízení. [Další informace](migrate-appliance-architecture.md#appliance-registration).<br/><br/> Pokud chcete zaregistrovat poskytovatele prostředků, potřebujete roli přispěvatel nebo Owner v předplatném.
**Vytváření aplikací Azure AD** | Při registraci zařízení Azure Migrate vytvoří aplikace Azure Active Directory (Azure AD). <br/><br/> – První aplikace se používá ke komunikaci mezi agenty, které běží na zařízení, a jejich příslušnými službami běžícími na Azure.<br/><br/> – Druhá aplikace se používá výhradně pro přístup k trezoru klíčů vytvořenému v předplatném uživatele pro migraci virtuálních počítačů VMware bez agentů. [Další informace](migrate-appliance-architecture.md#appliance-registration).<br/><br/> Potřebujete oprávnění k vytváření aplikací Azure AD (dostupných v rámci role vývojář aplikace).
**Vytvoření trezoru klíčů** | Pokud chcete migrovat virtuální počítače VMware pomocí migrace bez agentů, Azure Migrate vytvoří Key Vault pro správu přístupových klíčů k účtu úložiště replikace v rámci vašeho předplatného.<br/><br/> K vytvoření trezoru potřebujete oprávnění přiřazení role ve skupině prostředků, ve které se nachází Azure Migrate projekt.




### <a name="assign-permissions-to-create-project"></a>Přiřadit oprávnění k vytvoření projektu

1. V Azure Portal otevřete předplatné a vyberte **řízení přístupu (IAM)**.
2. V části **kontrolovat přístup**Najděte příslušný účet a kliknutím na něj Zobrazte oprávnění.
3. Měli byste mít oprávnění **Přispěvatel** nebo **Owner** .
    - Pokud jste právě vytvořili bezplatný účet Azure, jste vlastníkem svého předplatného.
    - Pokud nejste vlastníkem předplatného, pracujte s vlastníkem a přiřaďte roli.

### <a name="assign-permissions-to-register-the-appliance"></a>Přiřazení oprávnění k registraci zařízení

Pokud chcete zařízení zaregistrovat, přiřadíte jim oprávnění Azure Migrate k vytváření aplikací Azure AD během registrace zařízení. Oprávnění lze přiřadit pomocí jedné z následujících metod:

- **Udělení oprávnění**: tenant/globální správce může udělit oprávnění uživatelům v tenantovi a vytvářet a registrovat aplikace služby Azure AD.
- **Přiřazení role vývojáře aplikace**: tenant/globální správce může přiřadit roli vývojáře aplikace (která má oprávnění) k účtu.

> [!NOTE]
> - Aplikace nemají žádná jiná přístupová oprávnění k předplatnému, kromě výše popsaných výše.
> - Tato oprávnění budete potřebovat, jenom když zaregistrujete nové zařízení. Po nastavení zařízení můžete oprávnění odebrat.


#### <a name="grant-account-permissions"></a>Udělení oprávnění účtu

Chcete-li, aby měl tenant/globální správce oprávnění udělit oprávnění, postupujte takto:

1. V Azure AD by měl tenant nebo globální správce přejít na **Azure Active Directory** > **Users** > **uživatelských nastavení**uživatelů.
2. Správce by měl nastavit **Registrace aplikací** **Ano**. Toto je výchozí nastavení, které není citlivé. [Další informace](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).

    ![Oprávnění služby Azure AD](./media/tutorial-prepare-vmware/aad.png)



#### <a name="assign-application-developer-role"></a>Přiřazení role vývojáře aplikace

Případně může tenant nebo globální správce přiřadit roli vývojář aplikace k účtu. [Přečtěte si další informace](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal) o přiřazení role.

### <a name="assign-permissions-to-create-a-key-vault"></a>Přiřazení oprávnění k vytvoření Key Vault

Chcete-li povolit Azure Migrate vytvoření Key Vault, přiřaďte oprávnění následujícím způsobem:

1. Ve skupině prostředků v Azure Portal vyberte **řízení přístupu (IAM)**.
2. V části **kontrolovat přístup**Najděte příslušný účet a kliknutím na něj Zobrazte oprávnění.

    - Aby bylo možné spustit vyhodnocení serveru, jsou dostatečná oprávnění **přispěvatele** .
    - Pokud chcete spustit migraci serveru bez agenta, měli byste mít oprávnění **vlastníka** (nebo **přispěvatele** a **Správce přístupu uživatelů**).

3. Pokud nemáte požadovaná oprávnění, požádejte je od vlastníka skupiny prostředků.



## <a name="prepare-for-vmware-vm-assessment"></a>Příprava na posouzení virtuálních počítačů VMware

K přípravě na posouzení virtuálních počítačů VMware potřebujete:

- **Ověřte nastavení VMware**. Ujistěte se, že vCenter Server a virtuální počítače, které chcete migrovat, splňují požadavky.
- **Nastavte účet pro posouzení**. Azure Migrate používá tento účet pro přístup k vCenter Server, aby se zjistily virtuální počítače pro posouzení.
- **Ověřte požadavky na zařízení**. Před nasazením ověřte požadavky na nasazení pro zařízení Azure Migrate.

### <a name="verify-vmware-settings"></a>Ověření nastavení VMware

1. [Kontrolu](migrate-support-matrix-vmware.md#vmware-requirements) Požadavky na server VMware pro posouzení.
2. [Ujistěte se, že](migrate-support-matrix-vmware.md#port-access) porty, které potřebujete, jsou otevřené v vCenter Server.
3. V vCenter Server se ujistěte, že váš účet má oprávnění k vytvoření virtuálního počítače pomocí souboru sady vajíček. Zařízení Azure Migrate nasadíte jako virtuální počítač VMware pomocí souboru sady vajíček.


### <a name="set-up-an-account-for-assessment"></a>Nastavení účtu pro posouzení

Azure Migrate musí mít přístup k vCenter Server, aby se zjistily virtuální počítače pro účely posouzení a migrace bez agentů.

- Pokud máte v úmyslu zjišťovat aplikace nebo vizualizovat závislost bez agentů, vytvořte účet vCenter Server s přístupem jen pro čtení společně s oprávněními povolenými pro**operace hosta** **virtuálních počítačů** > .

  ![oprávnění účtu vCenter Server](./media/tutorial-prepare-vmware/vcenter-server-permissions.png)

- Pokud neplánujete funkci zjišťování aplikací a vizualizaci závislostí bez agentů, nastavte pro vCenter Server účet jen pro čtení.

### <a name="verify-appliance-settings-for-assessment"></a>Ověření nastavení zařízení pro posouzení

Před nastavením zařízení Azure Migrate a zahájením posouzení v dalším kurzu připravte na nasazení zařízení.

1. [Ověřit](migrate-appliance.md#appliance---vmware) Požadavky na zařízení Azure Migrate.
2. Zkontrolujte adresy URL Azure, které bude zařízení potřebovat pro přístup k [veřejným](migrate-appliance.md#public-cloud-urls) a [státním](migrate-appliance.md#government-cloud-urls) cloudům.
3. [Zkontrolujte data](migrate-appliance.md#collected-data---vmware) , která zařízení shromažďuje během zjišťování a posouzení.
4. [Poznamenejte si](migrate-support-matrix-vmware.md#port-access) požadavky na přístup k portu pro dané zařízení.




## <a name="prepare-for-agentless-vmware-migration"></a>Příprava na migraci VMware bez agentů

Projděte si požadavky na migraci virtuálních počítačů VMware bez [agenta](server-migrate-overview.md) .

1. [Kontrola](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers) Požadavky na server VMware.
2. [Zkontrolujte oprávnění](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers) , která Azure Migrate potřebuje pro přístup k vCenter Server.
3. [Kontrola](migrate-support-matrix-vmware-migration.md#agentless-vmware-vms) Požadavky na virtuální počítače VMware.
4. [Projděte si](migrate-support-matrix-vmware-migration.md#agentless-azure-migrate-appliance) požadavky na zařízení Azure Migrate.
5. Poznamenejte si přístup k adresám URL vyžadovaným pro [veřejné](migrate-appliance.md#public-cloud-urls) a [státní](migrate-appliance.md#government-cloud-urls) cloudy.
6. Zkontrolujte požadavky na [přístup k portu](migrate-support-matrix-vmware-migration.md#agentless-ports) .

## <a name="prepare-for-agent-based-vmware-migration"></a>Příprava na migraci VMware založenou na agentech

Projděte si požadavky na migraci virtuálních počítačů VMware [založených na agentech](server-migrate-overview.md) .

1. [Kontrola](migrate-support-matrix-vmware-migration.md#agent-based-vmware-servers) Požadavky na server VMware.
2. [Kontrola oprávnění](migrate-support-matrix-vmware-migration.md#agent-based-vmware-servers) Azure Migrate musí mít přístup k vCenter Server.
2. [Kontrola](migrate-support-matrix-vmware-migration.md#agent-based-vmware-vms) Požadavky na virtuální počítače VMware, včetně instalace služby mobility na každý virtuální počítač, který chcete migrovat.
3. Migrace založená na agentovi používá zařízení replikace:
    - [Zkontrolujte](migrate-replication-appliance.md#appliance-requirements) požadavky na nasazení pro zařízení replikace.
    - [Projděte si možnosti](migrate-replication-appliance.md#mysql-installation) instalace MySQL na zařízení.
    - Poznamenejte si přístup k adresám URL vyžadovaným pro [veřejné](migrate-replication-appliance.md#url-access) a [státní](migrate-replication-appliance.md#azure-government-url-access) cloudy.
    - Zkontrolujte požadavky na [přístup k portu](migrate-replication-appliance.md#port-access) pro zařízení replikace.
    
## <a name="next-steps"></a>Další kroky

V tomto kurzu jste:

> [!div class="checklist"]
> * Nastavte oprávnění Azure.
> * Příprava VMware pro posouzení a migraci


Pokračujte druhým kurzem a nastavte projekt Azure Migrate a vyhodnoťte virtuální počítače VMware pro migraci do Azure.

> [!div class="nextstepaction"]
> [Posouzení virtuálních počítačů VMware](./tutorial-assess-vmware.md)
