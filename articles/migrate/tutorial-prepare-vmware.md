---
title: Příprava virtuálních počítačů VMware pro posouzení a migraci do Azure pomocí Azure Migrate | Microsoft Docs
description: V této části najdete popis postupu při přípravě na posouzení a migraci místních virtuálních počítačů VMware do Azure pomocí Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 07/11/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 5bd4926494e13c1dd0cfb6283292ade967471238
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/23/2019
ms.locfileid: "70013422"
---
# <a name="prepare-vmware-vms-for-assessment-and-migration-to-azure"></a>Příprava virtuálních počítačů VMware pro posouzení a migraci do Azure

Tento článek popisuje, jak připravit na posouzení a migraci místních virtuálních počítačů VMware do Azure pomocí [Azure Migrate](migrate-services-overview.md).

[Azure Migrate](migrate-overview.md) poskytuje centrum nástrojů, které vám pomůžou zjišťovat, vyhodnocovat a migrovat aplikace, infrastrukturu a úlohy do Microsoft Azure. Centrum zahrnuje nástroje pro Azure Migrate a nabídky nezávislého výrobce softwaru (ISV) od jiných výrobců. 


Tento kurz je první v řadě, ve kterém se dozvíte, jak vyhodnocovat a migrovat virtuální počítače VMware. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Připravte Azure. Nastavte oprávnění pro účet a prostředky Azure pro práci s Azure Migrate.
> * Připravte místní servery a virtuální počítače VMware pro posouzení virtuálních počítačů.
> * Připravte místní servery a virtuální počítače VMware pro migraci virtuálních počítačů.

> [!NOTE]
> Kurzy vám ukážou nejjednodušší cestu nasazení pro scénář, abyste mohli rychle nastavit zkušební verzi. Kurzy používají výchozí možnosti, pokud je to možné, a nezobrazují všechna možná nastavení a cesty. Podrobné pokyny najdete v tématu How to TOS for VMware Assessment and Migration.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/) před tím, než začnete.


## <a name="prepare-azure"></a>Příprava Azure

Budete potřebovat tato oprávnění Azure:

- Váš účet Azure potřebuje oprávnění k vytvoření Azure Migrate projektu pro účely posouzení a migrace. 
- Pro účely posouzení a migrace virtuálních počítačů VMware s neagenty Azure Migrate spouští odlehčené zařízení, které zjišťuje virtuální počítače a odesílá data o datech a výkonu virtuálních počítačů do Azure Migrate. V Azure potřebujete oprávnění k registraci zařízení Azure Migrate.
- Pokud chcete migrovat virtuální počítače VMware pomocí migrace serveru Azure Migrate, vytvoří Azure Migrate ve skupině prostředků Key Vault pro správu přístupových klíčů k účtu úložiště replikace v rámci vašeho předplatného. K vytvoření trezoru potřebujete oprávnění přiřazení role ve skupině prostředků, ve které se nachází Azure Migrate projekt. 


### <a name="assign-permissions-to-create-project"></a>Přiřadit oprávnění k vytvoření projektu

1. V Azure Portal otevřete předplatné a vyberte **řízení přístupu (IAM)** .
2. V části **kontrolovat přístup**Najděte příslušný účet a kliknutím na něj Zobrazte oprávnění.
3. Měli byste mít oprávnění **Přispěvatel** nebo **Owner** .
    - Pokud jste právě vytvořili bezplatný účet Azure, jste vlastníkem svého předplatného.
    - Pokud nejste vlastníkem předplatného, pracujte s vlastníkem a přiřaďte roli.

### <a name="assign-permissions-to-register-the-appliance"></a>Přiřazení oprávnění k registraci zařízení

Pokud nasazujete zařízení Azure Migrate pro vyhodnocení nebo spuštění migrace virtuálních počítačů bez agenta, je nutné ji zaregistrovat.

- Při registraci zařízení Azure Migrate vytvoří dvě aplikace Azure Active Directory (Azure AD), které zařízení jednoznačně identifikují.
    - První aplikace komunikuje s koncovými body služby Azure Migrate.
    - Druhá aplikace přistupuje k Azure Key Vault vytvořenému během registrace za účelem uložení informací o aplikaci Azure AD a nastavení konfigurace zařízení.
- Můžete přiřadit oprávnění pro Azure Migrate vytvořit tyto aplikace Azure AD pomocí jedné z následujících metod:
    - Tenant nebo globální správce může udělit oprávnění uživatelům v tenantovi, aby mohli vytvářet a registrovat aplikace služby Azure AD.
    - Tenant nebo globální správce může k účtu přiřadit roli vývojáře aplikace (která má oprávnění).

Je potřeba poznamenat, že:

- Aplikace nemají žádná jiná přístupová oprávnění k předplatnému, kromě výše popsaných výše.
- Tato oprávnění budete potřebovat, jenom když zaregistrujete nové zařízení. Po nastavení zařízení můžete oprávnění odebrat. 


#### <a name="grant-account-permissions"></a>Udělení oprávnění účtu

Tenant nebo globální správce může udělit oprávnění následujícím způsobem.

1. V Azure AD by měl tenant nebo globální správce přejít na **Azure Active Directory** > **uživatelských nastavení** **uživatelů** > .
2. Správce by měl nastavit **Registrace aplikací** **Ano**.

    ![Oprávnění služby Azure AD](./media/tutorial-prepare-vmware/aad.png)

> [!NOTE]
> Toto je výchozí nastavení, které není citlivé. [Další informace](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).



#### <a name="assign-application-developer-role"></a>Přiřazení role vývojáře aplikace 

Tenant/globální správce může přiřadit roli vývojář aplikace k účtu. [Další informace](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal).

## <a name="assign-role-assignment-permissions"></a>Přiřadit oprávnění přiřazení role

Přiřaďte oprávnění přiřazení role ke skupině prostředků, ve které se Azure Migrate projekt nachází, takto:

1. Ve skupině prostředků v Azure Portal vyberte **řízení přístupu (IAM)** .
2. V části **kontrolovat přístup**Najděte příslušný účet a kliknutím na něj Zobrazte oprávnění.

    - Aby bylo možné spustit vyhodnocení serveru, jsou dostatečná oprávnění přispěvatele.
    - Pokud chcete spustit migraci serveru bez agenta, měli byste mít oprávnění **vlastníka** (nebo **přispěvatele** a **Správce přístupu uživatelů**).

3. Pokud nemáte požadovaná oprávnění, požádejte je od vlastníka skupiny prostředků. 



## <a name="prepare-for-vmware-vm-assessment"></a>Příprava na posouzení virtuálních počítačů VMware

K přípravě na vyhodnocení virtuálních počítačů VMware je nutné ověřit hostitele VMware a nastavení virtuálního počítače a ověřit nastavení nasazení zařízení.

### <a name="verify-vmware-settings"></a>Ověření nastavení VMware

1. [Ověřit](migrate-support-matrix-vmware.md#assessment-vcenter-server-requirements) Požadavky na server VMware pro vyhodnocování virtuálních počítačů.
2. [](migrate-support-matrix-vmware.md#assessment-port-requirements) Ujistěte se, že jsou na vCenter serverech otevřené požadované porty.


### <a name="set-up-an-account-for-assessment"></a>Nastavení účtu pro posouzení

Azure Migrate musí mít přístup k vCenter Server, aby se zjistily virtuální počítače pro účely posouzení a migrace bez agentů. Pouze pro posouzení potřebujete účet jen pro čtení vCenter Server.

Pokud používáte proxy server brány firewall založený na adrese URL, povolte přístup k požadovaným [adresám URL Azure](migrate-support-matrix-vmware.md#assessment-url-access-requirements).

Ujistěte se, že proxy překládá všechny záznamy CNAME přijaté při vyhledávání adres URL.


### <a name="verify-appliance-settings-for-assessment"></a>Ověření nastavení zařízení pro posouzení

Před nastavením zařízení Azure Migrate a zahájením posouzení v dalším kurzu připravte na nasazení zařízení.

1. [Ověřte](migrate-support-matrix-vmware.md#assessment-appliance-requirements) požadavky pro nastavení zařízení Azure Migrate ve VMware.
2. [Zkontrolujte](migrate-support-matrix-vmware.md#assessment-url-access-requirements) adresy URL Azure, ke kterým bude zařízení potřebovat přístup.
3. Zkontrolujte data, která bude zařízení shromažďovat během zjišťování a posouzení.
4. [Poznamenejte si](migrate-support-matrix-vmware.md#assessment-port-requirements) požadavky na přístup k portu pro dané zařízení.
5. Zařízení Azure Migrate nasadíte jako virtuální počítač VMware pomocí souboru sady vajíček. V vCenter Server se ujistěte, že váš účet má oprávnění k vytvoření virtuálního počítače pomocí souboru sady vajíček.


## <a name="prepare-for-agentless-vmware-migration"></a>Příprava na migraci VMware bez agentů

Projděte si požadavky na migraci virtuálních počítačů VMware bez agenta.

1. [Kontrola](migrate-support-matrix-vmware.md#agentless-migration-vmware-server-requirements) Požadavky na server VMware pro migraci bez agenta.
2. Nastavte účet pro přístup k vCenter Server s [požadovanými oprávněními](migrate-support-matrix-vmware.md#agentless-migration-vcenter-server-permissions) pro migraci bez agenta.
3. [Poznamenejte](migrate-support-matrix-vmware.md#agentless-migration-vmware-vm-requirements) si požadavky na virtuální počítače VMware, které chcete migrovat do Azure pomocí migrace bez agentů.
4. [Zkontrolujte](migrate-support-matrix-vmware.md#agentless-migration-appliance-requirements) požadavky na zařízení pro migraci bez agentů.
5. Poznamenejte si [přístup k adresám URL](migrate-support-matrix-vmware.md#agentless-migration-url-access-requirements) zařízení a přístup k [portům](migrate-support-matrix-vmware.md#agentless-migration-port-requirements) pro migraci bez agentů.


## <a name="prepare-for-agent-based-vmware-migration"></a>Příprava na migraci VMware založenou na agentech

Projděte si požadavky na migraci virtuálních počítačů VMware založených na agentech.

1. [Kontrola](migrate-support-matrix-vmware.md#agent-based-migration-vmware-server-requirements) Požadavky na server VMware pro migraci bez agenta. 
2. Nastavte účet pro přístup k vCenter Server s [požadovanými oprávněními](migrate-support-matrix-vmware.md#agent-based-migration-vcenter-server-permissions) pro migraci bez agenta.
3. [Poznamenejte](migrate-support-matrix-vmware.md#agent-based-migration-vmware-vm-requirements) si požadavky na virtuální počítače VMware, které chcete migrovat do Azure pomocí migrace založené na agentech, včetně instalace služby mobility na každý virtuální počítač, který chcete migrovat.
4. [Přístup k adrese URL](migrate-support-matrix-vmware.md#agent-based-migration-url-access-requirements):
5. Zkontrolujte požadavky na [přístup k portu](migrate-support-matrix-vmware.md#agent-based-migration-port-requirements) pro službu mobility spuštěnou na každém virtuálním počítači a pro Azure Migrate konfigurační server.

## <a name="next-steps"></a>Další postup

V tomto kurzu se naučíte:
 
> [!div class="checklist"] 
> * Nastavte oprávnění Azure.
> * Příprava VMware pro posouzení a migraci


Pokračujte druhým kurzem a nastavte projekt Azure Migrate a vyhodnoťte virtuální počítače VMware pro migraci do Azure.

> [!div class="nextstepaction"] 
> [Posouzení virtuálních počítačů VMware](./tutorial-assess-vmware.md) 

