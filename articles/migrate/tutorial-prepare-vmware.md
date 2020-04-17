---
title: Příprava virtuálních počítačů VMware na vyhodnocení/migraci pomocí Migrace Azure
description: Zjistěte, jak se připravit na vyhodnocení/migraci virtuálních počítačích VMware pomocí Migrace Azure.
ms.topic: tutorial
ms.date: 04/15/2020
ms.custom: mvc
ms.openlocfilehash: 9f0729a3ddb2d8196a855557a6b8587940563984
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535260"
---
# <a name="prepare-vmware-vms-for-assessment-and-migration-to-azure"></a>Příprava virtuálních počítačů VMware na vyhodnocení a migraci do Azure

Tento článek vám pomůže připravit se na vyhodnocení nebo migraci místních virtuálních počítačů VMware do Azure pomocí [Migrace Azure](migrate-services-overview.md).



Tento kurz je první z řady, který ukazuje, jak posoudit a migrovat virtuální chod v systému VMware. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Připravte Azure na práci s Azure Migrate.
> * Připravte vmware pro vyhodnocení virtuálních počítačů pomocí nástroje Azure Migrate:Server Assessment.
> * Příprava vmware pro migraci virtuálních počítačů pomocí nástroje Migrace:server Azure. 

> [!NOTE]
> Kurzy ukazují nejjednodušší cestu nasazení pro scénář. Jsou užitečné, když se naučíte, jak nastavit nasazení a jako rychlý důkaz konceptu. Kurzy používají výchozí možnosti tam, kde je to možné, a nezobrazují všechna možná nastavení a cesty. 

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/pricing/free-trial/) než začnete.


## <a name="prepare-azure"></a>Příprava Azure

Než budete moct vyhodnotit nebo migrovat virtuální počítače VMware, potřebujete tato oprávnění pro tyto úkoly v Azure.

**Úkol** | **Podrobnosti** 
--- | --- 
**Vytvoření projektu migrace Azure** | Váš účet Azure potřebuje oprávnění přispěvatele nebo vlastníka k vytvoření projektu. 
**Registrace poskytovatelů prostředků** | Azure Migrate používá zjednodušené zařízení Azure Migrate ke zjišťování a vyhodnocování virtuálních počítačích VMware a k jejich migraci do Azure pomocí Azure Migrate:Server Assessment.<br/><br/> Během registrace zařízení jsou poskytovatelé prostředků registrováni s předplatným zvoleným v zařízení. [Další informace](migrate-appliance-architecture.md#appliance-registration).<br/><br/> Chcete-li zaregistrovat zprostředkovatele prostředků, potřebujete roli přispěvatele nebo vlastníka v předplatném.
**Vytváření aplikací Azure AD** | Při registraci zařízení Azure Migrate vytvoří aplikace Azure Active Directory (Azure AD). <br/><br/> - První aplikace se používá pro komunikaci mezi agenty spuštěné na zařízení a jejich příslušné služby spuštěné v Azure.<br/><br/> - Druhá aplikace se používá výhradně pro přístup keyvault vytvořený v předplatném uživatele pro migraci vrámci virtuálního vavu bez agenta. [Další informace](migrate-appliance-architecture.md#appliance-registration).<br/><br/> Potřebujete oprávnění k vytváření aplikací Azure AD (k dispozici v roli Application Developer).
**Vytvoření trezoru klíčů** | Chcete-li migrovat virtuální počítače VMware pomocí migrace bez agenta, Azure Migrate vytvoří trezor klíčů pro správu přístupových klíčů k účtu úložiště replikace ve vašem předplatném.<br/><br/> Chcete-li vytvořit úschovnu, potřebujete oprávnění přiřazení role ve skupině prostředků, ve které se nachází projekt Migrace Azure.




### <a name="assign-permissions-to-create-project"></a>Přiřazení oprávnění k vytvoření projektu

1. Na webu Azure Portal otevřete předplatné a vyberte **řízení přístupu (IAM).**
2. V **části Zkontrolovat přístup**vyhledejte příslušný účet a kliknutím na něj zobrazte oprávnění.
3. Měli byste mít oprávnění **přispěvatele** nebo **vlastníka.**
    - Pokud jste si právě vytvořili bezplatný účet Azure, jste vlastníkem předplatného.
    - Pokud nejste vlastníkem předplatného, spolupracujte s vlastníkem a přiřaďte roli.

### <a name="assign-permissions-to-register-the-appliance"></a>Přiřazení oprávnění k registraci zařízení

Chcete-li zaregistrovat zařízení, přiřaďte oprávnění pro Azure Migrate k vytvoření aplikací Azure AD během registrace zařízení. Oprávnění lze přiřadit pomocí jedné z následujících metod:

- **Udělit oprávnění**: Klient/globální správce můžete udělit oprávnění uživatelům v tenantovi, k vytvoření a registraci aplikací Azure AD.
- **Přiřazení role vývojáře aplikací**: Klient/globální správce může účtu přiřadit roli Vývojáře aplikací (která má oprávnění).

> [!NOTE]
> - Aplikace nemají k předplatnému jiná přístupová oprávnění než ta, která jsou popsána výše.
> - Tato oprávnění potřebujete pouze při registraci nového zařízení. Po nastavení zařízení můžete odebrat oprávnění.


#### <a name="grant-account-permissions"></a>Udělit oprávnění k účtu

Pokud chcete, aby oprávnění udělují klienti/globální správce, postupujte takto:

1. Ve službě Azure AD by měl klient/globální správce přejít na**nastavení uživatelů****služby** >  **Azure Active Directory** > .
2. Správce by měl nastavit **registrace aplikací** na **Ano**. Toto je výchozí nastavení, které není citlivé. [Další informace](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).

    ![Oprávnění Azure AD](./media/tutorial-prepare-vmware/aad.png)



#### <a name="assign-application-developer-role"></a>Přiřadit roli vývojáře aplikací

Alternativně může klient/globální správce přiřadit roli vývojáře aplikací k účtu. [Přečtěte si další informace](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal) o přiřazení role.

### <a name="assign-permissions-to-create-a-key-vault"></a>Přiřazení oprávnění k vytvoření trezoru klíčů

Chcete-li povolit Azure Migrate k vytvoření trezoru klíčů, přiřaďte oprávnění následujícím způsobem:

1. Ve skupině prostředků na webu Azure Portal vyberte **Řízení přístupu (IAM).**
2. V **části Zkontrolovat přístup**vyhledejte příslušný účet a kliknutím na něj zobrazte oprávnění.

    - Chcete-li spustit vyhodnocení serveru, oprávnění **přispěvatele** jsou dost.
    - Chcete-li spustit migraci serveru bez agenta, měli byste mít oprávnění **vlastníka** (nebo **přispěvatele** a **správce přístupu uživatelů).**

3. Pokud nemáte požadovaná oprávnění, požádejte je od vlastníka skupiny prostředků.



## <a name="prepare-for-vmware-vm-assessment"></a>Příprava na vyhodnocení virtuálního počítače v rámci vmware v

Chcete-li se připravit na posouzení virtuálního počítače VMware, musíte:

- **Ověřte nastavení programu VMware**. Ujistěte se, že server vCenter a virtuální servery, které chcete migrovat, splňují požadavky.
- **Nastavte účet pro posouzení**. Azure Migrate používá tento účet pro přístup k serveru vCenter, ke zjišťování virtuálních počítačů pro posouzení.
- **Ověřte požadavky na zařízení**. Před nasazením ověřte požadavky na nasazení zařízení Azure Migrate.

### <a name="verify-vmware-settings"></a>Ověření nastavení vmware

1. [Kontrola](migrate-support-matrix-vmware.md#vmware-requirements) Požadavky serveru VMware na posouzení.
2. [Ujistěte se,](migrate-support-matrix-vmware.md#port-access) že porty, které potřebujete, jsou otevřené na serveru vCenter.
3. Na serveru vCenter Se ujistěte, že váš účet má oprávnění k vytvoření virtuálního počítače pomocí souboru OVA. Zařízení Azure Migrate nasadíte jako virtuální počítač VMware pomocí souboru OVA.


### <a name="set-up-an-account-for-assessment"></a>Nastavení účtu pro hodnocení

Azure Migrate potřebuje přístup k serveru vCenter, aby bylo účelem zjišťování virtuálních počítačů pro vyhodnocení a migraci bez agentů.

- Pokud plánujete zjišťovat aplikace nebo vizualizovat závislost způsobem bez agenta, vytvořte účet serveru vCenter s přístupem jen pro čtení spolu s oprávněními povolenými pro **virtuální počítače** > **Guest Operations**.

  ![Oprávnění účtu serveru vCenter](./media/tutorial-prepare-vmware/vcenter-server-permissions.png)

- Pokud neplánujete zjišťování aplikací a vizualizaci závislostí bez agenta, nastavte účet jen pro čtení pro server vCenter.

### <a name="verify-appliance-settings-for-assessment"></a>Ověření nastavení zařízení pro posouzení

Před nastavením zařízení Azure Migrate a počátečním hodnocením v dalším kurzu se připravte na nasazení zařízení.

1. [Ověřit](migrate-appliance.md#appliance---vmware) Požadavky na zařízení migrace Azure.
2. Zkontrolujte adresy URL Azure, ke kterým bude zařízení potřebovat přístup ve [veřejných](migrate-appliance.md#public-cloud-urls) a [vládních](migrate-appliance.md#government-cloud-urls) cloudech.
3. [Zkontrolujte data,](migrate-appliance.md#collected-data---vmware) která zařízení shromažďuje během zjišťování a hodnocení.
4. [Poznamenejte si](migrate-support-matrix-vmware.md#port-access) požadavky na přístup k portu pro zařízení.




## <a name="prepare-for-agentless-vmware-migration"></a>Příprava na migraci vrámci voblasti VMware bez agenta

Projděte si požadavky na [migraci](server-migrate-overview.md) virtuálních měn VMware bez agenta.

1. [Recenze](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers) Požadavky na server VMware.
2. [Zkontrolujte oprávnění,](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers) která Azure Migrate potřebuje pro přístup k serveru vCenter.
3. [Recenze](migrate-support-matrix-vmware-migration.md#agentless-vmware-vms) Požadavky na virtuální mích VMware.
4. [Projděte si](migrate-support-matrix-vmware-migration.md#agentless-azure-migrate-appliance) požadavky na zařízení Migrace Azure.
5. Všimněte si přístupu url požadovaného pro [veřejné](migrate-appliance.md#public-cloud-urls) a [vládní](migrate-appliance.md#government-cloud-urls) cloudy.
6. Zkontrolujte požadavky na [přístup k portům.](migrate-support-matrix-vmware-migration.md#agentless-ports)

## <a name="prepare-for-agent-based-vmware-migration"></a>Příprava na migraci v systému VMware založené na agentech

Projděte si požadavky na [migraci](server-migrate-overview.md) virtuálních měn VMware na základě agenta.

1. [Recenze](migrate-support-matrix-vmware-migration.md#agent-based-vmware-servers) Požadavky na server VMware.
2. [Kontrola oprávnění](migrate-support-matrix-vmware-migration.md#agent-based-vmware-servers) Azure Migrate potřebuje přístup k serveru vCenter.
2. [Recenze](migrate-support-matrix-vmware-migration.md#agent-based-vmware-vms) Požadavky na virtuální zařízení VMware, včetně instalace služby Mobility na každém virtuálním počítači, který chcete migrovat.
3. Migrace založená na agentovi používá replikační zařízení:
    - [Zkontrolujte](migrate-replication-appliance.md#appliance-requirements) požadavky na nasazení pro zařízení replikace.
    - [Přečtěte si možnosti](migrate-replication-appliance.md#mysql-installation) instalace MySQL na zařízení.
    - Poznamenejte si požadovaný přístup k [adrese](migrate-replication-appliance.md#url-access)URL .
    - Zkontrolujte požadavky na [přístup k portu](migrate-replication-appliance.md#port-access) pro zařízení replikace.
    
## <a name="next-steps"></a>Další kroky

V tomto kurzu jste:

> [!div class="checklist"]
> * Nastavte oprávnění Azure.
> * Připravili jste společnost VMware pro posouzení a migraci.


Pokračujte k druhému kurzu k nastavení projektu Migrace Azure a vyhodnoťte virtuální počítače VMware pro migraci do Azure.

> [!div class="nextstepaction"]
> [Posouzení virtuálních měn VMware](./tutorial-assess-vmware.md)
