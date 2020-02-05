---
title: Příprava fyzických serverů pro posouzení nebo migraci pomocí Azure Migrate
description: Přečtěte si, jak připravit na posouzení/migraci fyzických serverů s Azure Migrate.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: a487e0c9d565993b45f7e271af23a622e61389ad
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2020
ms.locfileid: "76990823"
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


## <a name="prepare-azure"></a>Příprava Azure

### <a name="azure-permissions"></a>Oprávnění Azure

Je nutné nastavit oprávnění pro nasazení Azure Migrate.

**Úkol** | **Oprávnění**
--- | ---
**Vytvoření projektu Azure Migrate** | Váš účet Azure potřebuje oprávnění k vytvoření projektu.
**Registrace zařízení Azure Migrate** | Azure Migrate využívá odlehčené Azure Migrate zařízení ke zjišťování a hodnocení fyzických serverů pomocí Azure Migrate vyhodnocování serveru. Toto zařízení zjistí servery a pošle jejich metadata a data o výkonu do Azure Migrate.<br/><br/>Při registraci zařízení se u předplatného, které jste zvolili v části zařízení – Microsoft. OffAzure, Microsoft. Migruj a Microsoft. klíčů, zaregistrují následující poskytovatelé registrace. Když zaregistrujete poskytovatele prostředků, nakonfigurujete vaše předplatné, aby fungovalo s poskytovatelem prostředků. Pokud chcete zaregistrovat poskytovatele prostředků, potřebujete roli přispěvatel nebo Owner v předplatném.<br/><br/> V rámci připojování Azure Migrate vytvoří aplikaci Azure Active Directory (Azure AD):<br/> Aplikace AAD se používá pro komunikaci (ověřování a autorizaci) mezi agenty běžícími na zařízení s příslušnými službami běžícími na Azure. Tato aplikace nemá oprávnění k tomu, aby zavedla volání ARM nebo přístup RBAC k jakémukoli prostředku.



### <a name="assign-permissions-to-create-project"></a>Přiřadit oprávnění k vytvoření projektu

Ověřte, že máte oprávnění k vytvoření projektu Azure Migrate.

1. V Azure Portal otevřete předplatné a vyberte **řízení přístupu (IAM)** .
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

1. V Azure AD by měl tenant/globální správce přejít na **Azure Active Directory** > **Uživatelé** > **uživatelských nastavení**.
2. Správce by měl nastavit **Registrace aplikací** **Ano**.

    ![Oprávnění služby Azure AD](./media/tutorial-prepare-hyper-v/aad.png)

> [!NOTE]
> Toto je výchozí nastavení, které není citlivé. [Další informace](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).

#### <a name="assign-application-developer-role"></a>Přiřazení role vývojáře aplikace

Tenant/globální správce může přiřadit roli vývojář aplikace k účtu. [Další informace](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal).


## <a name="prepare-for-physical-server-assessment"></a>Příprava na posouzení fyzického serveru

K přípravě na fyzické posouzení serveru musíte ověřit nastavení fyzického serveru a ověřit nastavení pro nasazení zařízení:

### <a name="verify-physical-server-settings"></a>Ověřit nastavení fyzického serveru

1. Ověřte [požadavky na fyzický server](migrate-support-matrix-physical.md#physical-server-requirements) pro vyhodnocování serveru.
2. Ujistěte se, že jsou na fyzických serverech otevřené [požadované porty](migrate-support-matrix-physical.md#port-access) .


### <a name="verify-appliance-settings"></a>Ověření nastavení zařízení

Před nastavením zařízení Azure Migrate a zahájením posouzení v dalším kurzu připravte na nasazení zařízení.

1. [Ověřte](migrate-appliance.md#appliance---physical) požadavky na zařízení pro fyzické servery.
2. [Zkontrolujte](migrate-appliance.md#url-access) adresy URL Azure, ke kterým bude zařízení potřebovat přístup.
3. [Zkontrolujte](migrate-appliance.md#collected-data---vmware) , že zařízení bude během zjišťování a hodnocení shromažďováno.
4. [Poznamenejte si](migrate-support-matrix-physical.md#port-access) požadavky na přístup k portu fyzický server posouzení.


### <a name="set-up-an-account-for-physical-server-discovery"></a>Nastavení účtu pro zjišťování fyzického serveru

Azure Migrate potřebuje oprávnění ke zjišťování místních serverů.

- **Windows:** Nastavte místní uživatelský účet na všech serverech Windows, které chcete zahrnout do zjišťování. Uživatelský účet musí být přidán do následujících skupin: – Uživatelé vzdálené správy – Performance Monitor Users-Performance Log Users
- **Linux:** Na serverech se systémem Linux, které chcete zjistit, potřebujete kořenový účet.

## <a name="prepare-for-physical-server-migration"></a>Příprava na migraci fyzického serveru

Projděte si požadavky na migraci fyzických serverů.

- [Zkontrolujte](migrate-support-matrix-physical-migration.md#physical-server-requirements) požadavky na fyzický server pro migraci.
- Azure Migrate: Migrace serveru používá replikační Server pro migraci fyzického serveru:
    - [Projděte si](migrate-replication-appliance.md#appliance-requirements) požadavky na nasazení pro zařízení replikace a [Možnosti](migrate-replication-appliance.md#mysql-installation) pro instalaci MySQL na zařízení.
    - Zkontrolujte požadavky na přístup k [adrese URL](migrate-replication-appliance.md#url-access) a [port] (migrace-replikace-zařízení. MD # port-přístup) pro zařízení replikace.


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste:

> [!div class="checklist"]
> * Nastavte oprávnění účtu Azure.
> * Připravte fyzické servery na posouzení.

Přejděte k dalšímu kurzu a vytvořte projekt Azure Migrate a vyhodnoťte fyzické servery pro migraci do Azure.

> [!div class="nextstepaction"]
> [Vyhodnotit fyzické servery](./tutorial-assess-physical.md)
