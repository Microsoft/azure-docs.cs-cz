---
title: Příprava virtuálních počítačů VMware pro posouzení/migraci pomocí Azure Migrate
description: Přečtěte si, jak připravit na posouzení/migraci virtuálních počítačů VMware pomocí Azure Migrate.
ms.topic: tutorial
ms.date: 06/08/2020
ms.custom: mvc
ms.openlocfilehash: f3bfaf7c2396e0f1091299375aae4bfaa1d7e8ff
ms.sourcegitcommit: 99d016949595c818fdee920754618d22ffa1cd49
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/15/2020
ms.locfileid: "84771185"
---
# <a name="prepare-vmware-vms-for-assessment-and-migration-to-azure"></a>Příprava virtuálních počítačů VMware na vyhodnocení a migraci do Azure

Tento článek vám pomůže připravit se na posouzení a migraci místních virtuálních počítačů VMware do Azure pomocí [Azure Migrate](migrate-services-overview.md).

Tento kurz je první v řadě, ve kterém se dozvíte, jak vyhodnocovat a migrovat virtuální počítače VMware. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Připravte Azure pro práci s Azure Migrate.
> * Příprava na posouzení virtuálních počítačů VMware pomocí nástroje Azure Migrate: Server Assessment Tool.
> * Příprava na migraci virtuálních počítačů VMware pomocí nástroje Azure Migrate: Server Migration Tool. 

> [!NOTE]
> Kurzy ukazují nejjednodušší cestu k nasazení scénáře. Jsou užitečné pro rychlé ověření konceptu. Kurzy používají výchozí možnosti, pokud je to možné, a nezobrazují všechna možná nastavení a cesty. 

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/pricing/free-trial/), ještě než začnete.


## <a name="prepare-azure"></a>Příprava Azure

Tabulka shrnuje úkoly, které je potřeba provést v Azure. Pokyny pro každý úkol následují po tabulce.

**Úkol** | **Podrobnosti** | **Oprávnění**
--- | --- | ---
**Vytvoření projektu Azure Migrate** | Azure Migrate projekt poskytuje centrální umístění pro orchestraci a správu hodnocení a migrace pomocí nástrojů Azure Migrate, nástrojů společnosti Microsoft a nabídek třetích stran. | Váš účet Azure potřebuje oprávnění přispěvatele nebo vlastníka ve skupině prostředků, ve které se projekt nachází.
**Registrovat zařízení** | Azure Migrate pro zjišťování virtuálních počítačů používá odlehčené Azure Migrate zařízení, které je vyhodnocuje pomocí nástroje pro vyhodnocení serveru a migruje je pomocí nástroje pro migraci serveru pomocí bez agenta. [Přečtěte si další informace](migrate-appliance-architecture.md#appliance-registration) o registraci. | Aby bylo možné zařízení zaregistrovat, váš účet Azure potřebuje oprávnění přispěvatele nebo vlastníka v předplatném Azure.
**Vytváření aplikací Azure AD** | Při registraci zařízení Azure Migrate vytvoří aplikace Azure Active Directory (Azure AD). <br/><br/> – První aplikace se používá ke komunikaci mezi agenty, které běží na zařízení a Azure Migrate. <br/><br/> – Druhá aplikace se používá výhradně pro přístup k trezoru klíčů vytvořenému v předplatném uživatele pro migraci virtuálních počítačů VMware bez agentů.   | Váš účet Azure potřebuje oprávnění k vytváření aplikací Azure AD.
**Vytvoření trezoru klíčů** | Pokud chcete migrovat virtuální počítače VMware pomocí migrace bez agentů, Azure Migrate vytvoří Key Vault pro správu přístupových klíčů k účtu replikace v rámci vašeho předplatného. | Chcete-li Azure Migrate vytvořit Key Vault, nastavte oprávnění (vlastník nebo přispěvatel a správce přístupu uživatele) ve skupině prostředků, ve které se nachází projekt Azure Migrate.


### <a name="assign-permissions-to-create-project"></a>Přiřadit oprávnění k vytvoření projektu

1. V Azure Portal otevřete předplatné a vyberte **řízení přístupu (IAM)**.
2. V části **kontrolovat přístup**Najděte příslušný účet a kliknutím na něj Zobrazte oprávnění.
3. Měli byste mít oprávnění **Přispěvatel** nebo **Owner** .
    - Pokud jste právě vytvořili bezplatný účet Azure, jste vlastníkem svého předplatného.
    - Pokud nejste vlastníkem předplatného, pracujte s vlastníkem a přiřaďte roli.

### <a name="assign-permissions-to-create-azure-ad-apps"></a>Přiřazení oprávnění k vytváření aplikací Azure AD

Aby bylo možné zařízení zaregistrovat, váš účet Azure potřebuje oprávnění k vytváření aplikací Azure AD. Přiřaďte oprávnění pomocí jedné z těchto metod:

- **Metoda 1: udělení oprávnění účtu**: tenant/globální správce může udělit oprávnění k uživatelským účtům v tenantovi a vytvářet a registrovat aplikace služby Azure AD.
- **Metoda 2: přiřazení role s oprávněními k uživatelskému účtu**: tenant/globální správce může přiřadit roli vývojáře aplikace (která má požadovaná oprávnění) k uživatelskému účtu.

> [!NOTE]
> Tato oprávnění budete potřebovat, jenom když zaregistrujete nové zařízení. Po nastavení zařízení můžete oprávnění odebrat.


#### <a name="method-1-grant-permissions-to-the-account"></a>Metoda 1: udělení oprávnění účtu

Udělte účtu oprávnění následujícím způsobem:

1. Ujistěte se, že jste tenant nebo globální správce. Pak ve službě Azure AD přejděte na **Azure Active Directory**  >  **Users**  >  **uživatelských nastavení**uživatelů.
2. Nastavte **Registrace aplikací** na **Ano**. Toto je výchozí nastavení, které není citlivé. [Přečtěte si další informace](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).

    ![Oprávnění služby Azure AD](./media/tutorial-prepare-vmware/aad.png)



#### <a name="method-2-assign-application-developer-role"></a>Metoda 2: přiřazení role vývojáře aplikace

Případně může tenant nebo globální správce přiřadit roli vývojář aplikace k účtu. [Přečtěte si další informace](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal) o přiřazení role.

### <a name="assign-permissions-to-create-a-key-vault"></a>Přiřazení oprávnění k vytvoření Key Vault

Chcete-li povolit Azure Migrate vytvoření Key Vault, přiřaďte oprávnění následujícím způsobem:

1. Ve skupině prostředků v Azure Portal vyberte **řízení přístupu (IAM)**.
2. V části **kontrolovat přístup**Najděte příslušný účet a kliknutím na něj Zobrazte oprávnění.

    - Aby bylo možné spustit vyhodnocení serveru, jsou dostatečná oprávnění **přispěvatele** .
    - Pokud chcete spustit migraci serveru bez agenta, měli byste mít oprávnění **vlastníka** (nebo **přispěvatele** a **Správce přístupu uživatelů**).

3. Pokud nemáte požadovaná oprávnění, požádejte je od vlastníka skupiny prostředků.

## <a name="prepare-for-assessment"></a>Příprava na posouzení

K přípravě na posouzení virtuálních počítačů VMware potřebujete:

1. **Ověřte nastavení VMware**. Ujistěte se, že vCenter Server a virtuální počítače, které chcete migrovat, splňují požadavky.
2. **Nastavte oprávnění pro posouzení**. Azure Migrate používá pro přístup k vCenter Server účet vCenter ke zjišťování a vyhodnocení virtuálních počítačů.
3. **Ověřte požadavky na zařízení**. Před nasazením v dalším kurzu ověřte požadavky na nasazení pro zařízení Azure Migrate.

### <a name="verify-vmware-settings"></a>Ověření nastavení VMware

1. [Ověřte požadavky na VMware](migrate-support-matrix-vmware.md#vmware-requirements) pro posouzení.
2. [Ujistěte se, že](migrate-support-matrix-vmware.md#port-access-requirements) porty, které potřebujete, jsou otevřené v vCenter Server.
3. V vCenter Server ověřte, že váš účet má oprávnění k vytvoření virtuálního počítače pomocí souboru sady vajíček. To je potřeba, když nasadíte Azure Migrate zařízení jako virtuální počítač VMware pomocí souboru sady vajíček.
4. Před migrací do Azure jsou na virtuálních počítačích potřeba nějaké změny.

    - Pro některé operační systémy Azure Migrate provede tyto změny automaticky. 
    - Před zahájením migrace je důležité tyto změny provést. Pokud před provedením změny migrujete virtuální počítač, nemusí se virtuální počítač spustit v Azure.
    - Zkontrolujte změny v [systému Windows](prepare-for-migration.md#windows-machines) a [Linux](prepare-for-migration.md#linux-machines) , které je třeba provést.


### <a name="set-up-permissions-for-assessment"></a>Nastavit oprávnění pro posouzení

Azure Migrate musí mít přístup k vCenter Server, aby zařízení Azure Migrate mohlo zjistit virtuální počítače pro účely posouzení a migrace bez agentů. Účet nastavte takto:

1. Ve webovém klientovi vSphere otevřete okno **Správa**  >  **přístup k**  >  **uživatelům a skupinám SSO**.
2. V části **Uživatelé**klikněte na ikonu **Nový uživatel** .
3. Zadejte podrobnosti o novém uživateli.
4. V souladu s hodnotami tabulky vyberte oprávnění.

    **Funkce** | **Oprávnění účtu**
    --- | ---
    [Posouzení virtuálních počítačů](tutorial-assess-vmware.md) | Pro účely posouzení potřebuje účet přístup jen pro čtení.
    [Objevte aplikace, role a funkce virtuálních počítačů](how-to-discover-applications.md) | Pokud chcete použít zjišťování aplikací, účet jen pro čtení, který se používá pro vyhodnocení, potřebuje oprávnění pro **Virtual machines**  >  **operace hosta**virtuálních počítačů.
    [Analyzovat závislosti na virtuálních počítačích (bez agentů)](how-to-create-group-machine-dependencies-agentless.md) | Pokud chcete analyzovat závislosti, účet jen pro čtení, který se používá pro vyhodnocení, potřebuje oprávnění pro **Virtual machines**  >  **operace hosta**virtuálních počítačů.
    
> [!NOTE]
> Pokud chcete omezit zjišťování virtuálních počítačů pro posouzení na konkrétní obor, přečtěte si [Tento článek](set-discovery-scope.md#assign-a-role-for-assessment).

### <a name="verify-appliance-settings-for-assessment"></a>Ověření nastavení zařízení pro posouzení

V [dalším kurzu](tutorial-assess-vmware.md) nastavíte zařízení Azure Migrate a začnete vyhodnocování. Než to uděláte, Projděte si požadavky na zařízení následujícím způsobem: 

1. [Projděte si požadavky](migrate-appliance.md#appliance---vmware) na nasazení zařízení Azure Migrate.
2. Zkontrolujte adresy URL Azure, které zařízení potřebuje pro přístup k [veřejným](migrate-appliance.md#public-cloud-urls) a [státním](migrate-appliance.md#government-cloud-urls) cloudům.
3. [Poznamenejte si](migrate-support-matrix-vmware.md#port-access-requirements) porty, které zařízení používá.
4. [Zkontrolujte data](migrate-appliance.md#collected-data---vmware) , která zařízení shromažďuje během zjišťování a posouzení.

## <a name="prepare-for-agentless-vmware-migration"></a>Příprava na migraci VMware bez agentů

Virtuální počítače VMware můžete migrovat pomocí [agenta bez agentů nebo migrace založené na agentech](server-migrate-overview.md). V této části jsou shrnuté požadavky na migraci bez agentů.

1. [Rozhodněte](server-migrate-overview.md#compare-migration-methods) , jestli chcete použít migraci bez agentů.
2. [Prohlédněte si](migrate-support-matrix-vmware-migration.md#vmware-requirements-agentless) požadavky hypervisoru pro počítače, které chcete migrovat.
3. [Projděte si](migrate-support-matrix-vmware-migration.md#vm-requirements-agentless) požadavky na virtuální počítače VMware, které chcete migrovat pomocí migrace bez agenta.
4. [Přečtěte si](migrate-support-matrix-vmware-migration.md#appliance-requirements-agentless) požadavky na zařízení Azure Migrate pro migraci bez agentů.
5. Poznamenejte si přístup k adresám URL vyžadovaným pro [veřejné](migrate-appliance.md#public-cloud-urls) a [státní](migrate-appliance.md#government-cloud-urls) cloudy.
6. Zkontrolujte požadavky na [přístup k portu](migrate-support-matrix-vmware-migration.md#port-requirements-agentless) .
7. Nastavte oprávnění pro migraci bez agentů, jak je popsáno v následujícím postupu.


### <a name="assign-permissions-to-an-account"></a>Přiřazení oprávnění k účtu

Zařízení Azure Migrate se připojí k vCenter Server a zjistí a migruje virtuální počítače s migrací bez agentů. Můžete přiřadit oprávnění, která zařízení potřebuje k uživatelskému účtu, nebo vytvořit roli s oprávněními a přiřadit tuto roli k uživatelskému účtu.

1. Ve webovém klientovi vSphere otevřete okno **Správa**  >  **přístup k**  >  **uživatelům a skupinám SSO**.
2. V části **Uživatelé**klikněte na ikonu **Nový uživatel** .
3. Zadejte podrobnosti o novém uživateli.
4. Přiřadit [Tato oprávnění](migrate-support-matrix-vmware-migration.md#vmware-requirements-agentless)

#### <a name="create-a-role-and-assign-to-an-account"></a>Vytvoření role a přiřazení k účtu

Případně můžete vytvořit účet. Pak vytvořte roli a přiřaďte ji k účtu následujícím způsobem:

1. Přihlaste se do webového klienta sphere jako správce vCenter Server.
2. Vyberte instanci vCenter Server > **vytvořit roli**.
3. Zadejte název role, například <em>Azure_Migrate</em>, a přiřaďte roli [požadovaná oprávnění](migrate-support-matrix-vmware-migration.md#vmware-requirements-agentless) .

    ![oprávnění účtu vCenter Server](./media/tutorial-prepare-vmware/vcenter-server-permissions.png)

4. Nyní vytvořte účet a přiřaďte k účtu roli.

> [!NOTE]
> Pokud chcete omezit zjišťování virtuálních počítačů pro migraci bez agenta do konkrétního oboru, přečtěte si [Tento článek](set-discovery-scope.md#assign-a-role-for-agentless-migration).



## <a name="prepare-for-agent-based-vmware-migration"></a>Příprava na migraci VMware založenou na agentech

Virtuální počítače VMware můžete migrovat pomocí [agenta bez agentů nebo migrace založené na agentech](server-migrate-overview.md). V této části jsou shrnuté požadavky na migraci na základě agentů.

1. [Rozhodněte](server-migrate-overview.md#compare-migration-methods) , zda chcete použít migraci založenou na agentech.
1. [Prohlédněte si](migrate-support-matrix-vmware-migration.md#vmware-requirements-agent-based) požadavky hypervisoru pro počítače, které chcete migrovat.
2. [Kontrola](migrate-support-matrix-vmware-migration.md#vm-requirements-agent-based) Požadavky na virtuální počítače VMware, včetně instalace služby mobility na každý virtuální počítač, který chcete migrovat.
3. Migrace založená na agentovi používá zařízení replikace:
    - [Zkontrolujte](migrate-replication-appliance.md#appliance-requirements) požadavky na nasazení pro zařízení replikace.
    - [Projděte si možnosti](migrate-replication-appliance.md#mysql-installation) instalace MySQL na zařízení.
    - Poznamenejte si přístup k adresám URL vyžadovaným pro [veřejné](migrate-replication-appliance.md#url-access) a [státní](migrate-replication-appliance.md#azure-government-url-access) cloudy.
    - Zkontrolujte požadavky na [přístup k portu](migrate-replication-appliance.md#port-access) pro zařízení replikace.
4. Než budete moct provést migraci do Azure, je potřeba, abyste na virtuálních počítačích potřebovali nějaké změny. Zkontrolujte změny v [systému Windows](prepare-for-migration.md#windows-machines) a [Linux](prepare-for-migration.md#linux-machines) , které je třeba provést.



## <a name="next-steps"></a>Další kroky

V tomto kurzu jste:

> [!div class="checklist"]
> * Nastavte oprávnění Azure.
> * Příprava VMware pro posouzení a migraci


Pokračujte druhým kurzem a nastavte projekt Azure Migrate a vyhodnoťte virtuální počítače VMware pro migraci do Azure.

> [!div class="nextstepaction"]
> [Posouzení virtuálních počítačů VMware](./tutorial-assess-vmware.md)
