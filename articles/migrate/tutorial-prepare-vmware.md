---
title: Příprava virtuálních počítačů VMware pro posouzení/migraci pomocí Azure Migrate
description: Přečtěte si, jak připravit na posouzení/migraci virtuálních počítačů VMware pomocí Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: cc1eb4c4fce1398365145b2f3d63db984635d667
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/19/2019
ms.locfileid: "74196235"
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

**Úloha** | **Oprávnění**
--- | ---
**Vytvoření projektu Azure Migrate** | Váš účet Azure potřebuje oprávnění k vytvoření projektu.
**Registrace zařízení Azure Migrate** | Azure Migrate využívá odlehčené Azure Migrate zařízení k vyhodnocení virtuálních počítačů VMware pomocí posouzení Azure Migrate serveru a spouštění migrace virtuálních počítačů VMware bez [agenta](server-migrate-overview.md) pomocí migrace serveru Azure Migrate. Toto zařízení vyhledá virtuální počítače a odešle údaje o metadatech a výkonu virtuálních počítačů do Azure Migrate.<br/><br/>Během registrace Azure Migrate vytvoří dvě aplikace Azure Active Directory (Azure AD), které zařízení jednoznačně identifikují, a potřebuje oprávnění k vytváření těchto aplikací.<br/> – První aplikace komunikuje s koncovými body služby Azure Migrate.<br/> – Druhá aplikace přistupuje k Azure Key Vault vytvořenému během registrace pro ukládání informací o aplikaci Azure AD a nastavení konfigurace zařízení.
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
2. Správce by měl nastavit **Registrace aplikací** **Ano**. Toto je výchozí nastavení, které není citlivé. [Další informace](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance)

    ![Oprávnění služby Azure AD](./media/tutorial-prepare-vmware/aad.png)



#### <a name="assign-application-developer-role"></a>Přiřazení role vývojáře aplikace

Tenant/globální správce může přiřadit roli vývojář aplikace k účtu. [Další informace](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal)

### <a name="assign-role-assignment-permissions"></a>Přiřadit oprávnění přiřazení role

Pokud chcete povolit Azure Migrate vytvoření Key Vault, přiřaďte oprávnění přiřazení role následujícím způsobem:

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

1. [Kontrolu](migrate-support-matrix-vmware.md#assessment-vcenter-server-requirements) Požadavky na server VMware pro posouzení.
2. [Ujistěte se, že](migrate-support-matrix-vmware.md#assessment-port-requirements) porty, které potřebujete, jsou otevřené v vCenter Server.


### <a name="set-up-an-account-for-assessment"></a>Nastavení účtu pro posouzení

Azure Migrate musí mít přístup k vCenter Server, aby se zjistily virtuální počítače pro účely posouzení a migrace bez agentů.

- Pokud máte v úmyslu zjišťovat aplikace nebo vizualizovat závislost bez agentů, vytvořte účet vCenter Server s přístupem jen pro čtení společně s oprávněními povolenými pro **virtuální počítače** > **operacemi hostů**.

  ![oprávnění účtu vCenter Server](./media/tutorial-prepare-vmware/vcenter-server-permissions.png)

- Pokud neplánujete funkci zjišťování aplikací a vizualizaci závislostí bez agentů, nastavte pro vCenter Server účet jen pro čtení.

### <a name="verify-appliance-settings-for-assessment"></a>Ověření nastavení zařízení pro posouzení

Před nasazením zařízení ověřte požadavky na zařízení.

1. [Ověřte](migrate-support-matrix-vmware.md#assessment-appliance-requirements) požadavky a omezení zařízení.
2. Pokud používáte proxy server brány firewall založený na adrese URL, [Zkontrolujte](migrate-support-matrix-vmware.md#assessment-url-access-requirements) adresy URL Azure, ke kterým bude zařízení potřebovat přístup. Ujistěte se, že proxy překládá všechny záznamy CNAME přijaté při vyhledávání adres URL.
3. Zkontrolujte [údaje o výkonu](migrate-appliance.md#collected-performance-data-vmware)a [metadata](migrate-appliance.md#collected-metadata-vmware) , která zařízení shromažďuje během zjišťování a posouzení.
4. [Poznamenejte](migrate-support-matrix-vmware.md#assessment-port-requirements) si porty, ke kterým se zařízení přistupovalo.
5. V vCenter Server se ujistěte, že váš účet má oprávnění k vytvoření virtuálního počítače pomocí souboru sady vajíček. Zařízení Azure Migrate nasadíte jako virtuální počítač VMware pomocí souboru sady vajíček.

Pokud používáte proxy server brány firewall založený na adrese URL, povolte přístup k požadovaným [adresám URL Azure](migrate-support-matrix-vmware.md#assessment-url-access-requirements).




## <a name="prepare-for-agentless-vmware-migration"></a>Příprava na migraci VMware bez agentů

Projděte si požadavky na migraci virtuálních počítačů VMware bez agenta.

1. [Kontrola](migrate-support-matrix-vmware.md#agentless-migration-vmware-server-requirements) Požadavky na server VMware.
2. Nastavte účet s [požadovanými oprávněními](migrate-support-matrix-vmware.md#agentless-migration-vcenter-server-permissions), aby Azure Migrate mohl získat přístup k vCenter Server migrace bez agenta pomocí migrace Azure Migrate serveru.
3. [Přečtěte](migrate-support-matrix-vmware.md#agentless-migration-vmware-vm-requirements) si požadavky na virtuální počítače VMware, které chcete migrovat do Azure pomocí migrace bez agentů.
4. [Přečtěte](migrate-support-matrix-vmware.md#agentless-migration-appliance-requirements) si požadavky na používání zařízení Azure Migrate pro migraci bez agentů.
5. Poznamenejte si [přístup k adresám URL](migrate-support-matrix-vmware.md#agentless-migration-url-access-requirements) a [portům](migrate-support-matrix-vmware.md#agentless-migration-port-requirements) , které Azure Migrate zařízení potřebuje k migraci bez agentů.


## <a name="prepare-for-agent-based-vmware-migration"></a>Příprava na migraci VMware založenou na agentech

Projděte si požadavky na migraci virtuálních počítačů VMware [založených na agentech](server-migrate-overview.md) .

1. [Kontrola](migrate-support-matrix-vmware.md#agent-based-migration-vmware-server-requirements) Požadavky na server VMware.
2. Nastavte účet s [požadovanými oprávněními](migrate-support-matrix-vmware.md#agent-based-migration-vcenter-server-permissions). Takže Azure Migrate získat přístup k vCenter Server pro migraci na základě agenta pomocí migrace Azure Migrate serveru.
3. [Přečtěte](migrate-support-matrix-vmware.md#agent-based-migration-vmware-vm-requirements) si požadavky na virtuální počítače VMware, které chcete migrovat do Azure pomocí migrace založené na agentech, včetně instalace služby mobility na každý virtuální počítač, který chcete migrovat.
4. [Přístup k adrese URL](migrate-support-matrix-vmware.md#agent-based-migration-url-access-requirements):
5. Zkontrolujte [přístup k portu](migrate-support-matrix-vmware.md#agent-based-migration-port-requirements) , který Azure Migrate součásti potřebují pro přístup založený na agentovi.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste:

> [!div class="checklist"]
> * Nastavte oprávnění Azure.
> * Příprava VMware pro posouzení a migraci


Pokračujte druhým kurzem a nastavte projekt Azure Migrate a vyhodnoťte virtuální počítače VMware pro migraci do Azure.

> [!div class="nextstepaction"]
> [Posouzení virtuálních počítačů VMware](./tutorial-assess-vmware.md)
