---
title: Příprava fyzických serverů na vyhodnocení a migraci pomocí migrace Azure
description: Přečtěte si, jak se připravit na vyhodnocení nebo migraci fyzických serverů pomocí Migrace Azure.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 5f9048b08b3e77a0c8d5ae9a9d10c614a4e0af61
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80336689"
---
# <a name="prepare-for-assessment-and-migration-of-physical-servers-to-azure"></a>Příprava na vyhodnocení a migraci fyzických serverů do Azure

Tento článek popisuje, jak se připravit na posouzení místních fyzických serverů pomocí [Migrace Azure](migrate-services-overview.md).

[Azure Migrate](migrate-overview.md) poskytuje centrum nástrojů, které vám pomůžou objevovat, vyhodnocovat a migrovat aplikace, infrastrukturu a úlohy do Microsoft Azure. Centrum obsahuje nástroje pro migraci Azure a nabídky nezávislého dodavatele softwaru (ISV) od jiných výrobců. 

Tento kurz je první z řady, která ukazuje, jak posoudit fyzické servery s Azure Migrate. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Příprava Azure. Nastavte oprávnění pro svůj účet Azure a prostředky pro práci s Azure Migrate.
> * Připravte místní fyzické servery pro vyhodnocení serveru.


> [!NOTE]
> Kurzy ukazují nejjednodušší cestu nasazení pro scénář, takže můžete rychle nastavit proof-of-concept. Kurzy používají výchozí možnosti tam, kde je to možné, a nezobrazují všechna možná nastavení a cesty. Podrobné pokyny naleznete v návodech k posouzení fyzických serverů.


Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/pricing/free-trial/) než začnete.


## <a name="prepare-azure"></a>Příprava Azure

### <a name="azure-permissions"></a>Oprávnění Azure

Potřebujete nastavit oprávnění pro nasazení Migrace Azure.

**Úkol** | **Podrobnosti** 
--- | --- 
**Vytvoření projektu migrace Azure** | Váš účet Azure potřebuje oprávnění Contributer nebo Owner k vytvoření projektu. | 
**Registrace poskytovatelů prostředků** | Azure Migrate používá zjednodušené zařízení Azure Migrate ke zjišťování a vyhodnocování virtuálních počítačích Hyper-V pomocí Azure Migrate Server Assessment.<br/><br/> Během registrace zařízení jsou poskytovatelé prostředků registrováni s předplatným zvoleným v zařízení. [Další informace](migrate-appliance-architecture.md#appliance-registration).<br/><br/> Chcete-li zaregistrovat zprostředkovatele prostředků, potřebujete roli přispěvatele nebo vlastníka v předplatném.
**Vytvoření aplikace Azure AD** | Při registraci zařízení Azure Migrate vytvoří aplikaci Azure Active Directory (Azure AD), která se používá pro komunikaci mezi agenty spuštěnými na zařízení s jejich příslušnými službami spuštěnými v Azure. [Další informace](migrate-appliance-architecture.md#appliance-registration).<br/><br/> Potřebujete oprávnění k vytváření aplikací Azure AD (k dispozici v roli Application Developer).



### <a name="assign-permissions-to-create-project"></a>Přiřazení oprávnění k vytvoření projektu

Zkontrolujte, zda máte oprávnění k vytvoření projektu Migrace Azure.

1. Na webu Azure Portal otevřete předplatné a vyberte **řízení přístupu (IAM).**
2. V **části Zkontrolovat přístup**vyhledejte příslušný účet a kliknutím na něj zobrazte oprávnění.
3. Měli byste mít oprávnění **přispěvatele** nebo **vlastníka.**
    - Pokud jste si právě vytvořili bezplatný účet Azure, jste vlastníkem předplatného.
    - Pokud nejste vlastníkem předplatného, spolupracujte s vlastníkem a přiřaďte roli.


### <a name="assign-permissions-to-register-the-appliance"></a>Přiřazení oprávnění k registraci zařízení

Můžete přiřadit oprávnění pro Azure Migrate k vytvoření aplikace Azure AD během registrace zařízení pomocí jedné z následujících metod:

- Klient/globální správce můžete udělit oprávnění uživatelům v tenantovi, k vytvoření a registraci aplikací Azure AD.
- Klient/globální správce můžete přiřadit role Vývojář aplikace (která má oprávnění) k účtu.

> [!NOTE]
> - Aplikace nemá žádná jiná přístupová oprávnění k předplatnému, než jsou popsána výše.
> - Tato oprávnění potřebujete pouze při registraci nového zařízení. Po nastavení zařízení můžete odebrat oprávnění.


#### <a name="grant-account-permissions"></a>Udělit oprávnění k účtu

Klient/globální správce může udělit oprávnění následujícím způsobem:

1. Ve službě Azure AD by měl klient/globální správce přejít na**nastavení uživatelů****služby** >  **Azure Active Directory** > .
2. Správce by měl nastavit **registrace aplikací** na **Ano**.

    ![Oprávnění Azure AD](./media/tutorial-prepare-hyper-v/aad.png)

> [!NOTE]
> Toto je výchozí nastavení, které není citlivé. [Další informace](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).

#### <a name="assign-application-developer-role"></a>Přiřadit roli vývojáře aplikací

Klient/globální správce můžete přiřadit roli vývojáře aplikací k účtu. [Další informace](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal).


## <a name="prepare-for-physical-server-assessment"></a>Příprava na posouzení fyzického serveru

Chcete-li se připravit na posouzení fyzického serveru, je třeba ověřit nastavení fyzického serveru a ověřit nastavení pro nasazení zařízení:

### <a name="verify-physical-server-settings"></a>Ověření nastavení fyzického serveru

1. Ověřte [fyzické požadavky serveru](migrate-support-matrix-physical.md#physical-server-requirements) pro vyhodnocení serveru.
2. Ujistěte se, že [požadované porty](migrate-support-matrix-physical.md#port-access) jsou otevřené na fyzických serverech.


### <a name="verify-appliance-settings"></a>Ověření nastavení zařízení

Před nastavením zařízení Azure Migrate a počátečním hodnocením v dalším kurzu se připravte na nasazení zařízení.

1. [Ověřte](migrate-appliance.md#appliance---physical) požadavky zařízení pro fyzické servery.
2. [Zkontrolujte](migrate-appliance.md#url-access) adresy URL Azure, ke kterým bude zařízení potřebovat přístup.
3. [Zkontrolujte, že](migrate-appliance.md#collected-data---vmware) zařízení bude shromažďovat během zjišťování a hodnocení.
4. [Poznámka požadavky](migrate-support-matrix-physical.md#port-access) na přístup k portu fyzické posouzení serveru.


### <a name="set-up-an-account-for-physical-server-discovery"></a>Nastavení účtu pro zjišťování fyzického serveru

Azure Migrate potřebuje oprávnění ke zjišťování místních serverů.

- **Windows:** Nastavte místní uživatelský účet na všech serverech systému Windows, které chcete zahrnout do zjišťování. Uživatelský účet je třeba přidat do následujících skupin: - Uživatelé vzdálené správy - Uživatelé sledování výkonu - uživatelé protokolu výkonu
- **Linux:** Potřebujete kořenový účet na serverech Linuxu, které chcete objevit.

## <a name="prepare-for-physical-server-migration"></a>Příprava na migraci fyzického serveru

Zkontrolujte požadavky na migraci fyzických serverů.

- [Zkontrolujte](migrate-support-matrix-physical-migration.md#physical-server-requirements) požadavky na fyzický server pro migraci.
- Migrace Azure: Migrace serveru používá pro migraci fyzického serveru replikační server:
    - [Zkontrolujte](migrate-replication-appliance.md#appliance-requirements) požadavky na nasazení pro zařízení replikace a [možnosti](migrate-replication-appliance.md#mysql-installation) instalace MySQL na zařízení.
    - Zkontrolujte požadavky na [přístup url](migrate-replication-appliance.md#url-access) a [port] (migrate-replication-appliance.md#port-access) požadavky na přístup pro zařízení replikace.


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste:

> [!div class="checklist"]
> * Nastavte oprávnění účtu Azure.
> * Připravené fyzické servery pro posouzení.

Pokračujte k dalšímu kurzu k vytvoření projektu Migrace Azure a posouzení fyzických serverů pro migraci do Azure

> [!div class="nextstepaction"]
> [Vyhodnocení fyzických serverů](./tutorial-assess-physical.md)
