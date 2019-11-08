---
title: Příprava fyzických serverů na posouzení pomocí Azure Migrateho posouzení serveru
description: V této části najdete popis postupu při přípravě na posouzení a migraci fyzických serverů do Azure s využitím posouzení Azure Migrate serveru.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 11/07/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: df425f723aa6a5e261ed6dcd15abfe87b367ad68
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2019
ms.locfileid: "73747969"
---
# <a name="prepare-for-assessment-and-migration-of-physical-servers-to-azure"></a>Příprava na posouzení a migraci fyzických serverů do Azure

Tento článek popisuje, jak připravit na posouzení místních fyzických serverů s [Azure Migrate](migrate-services-overview.md).


> [!NOTE]
> Pokud se tyto funkce na portálu Azure Migrate Portal ještě nezobrazují, počkejte. Budou zobrazeny v průběhu příštího týdne.

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

- Oprávnění pro účet Azure k vytvoření projektu Azure Migrate. 
- Oprávnění pro váš účet k registraci zařízení Azure Migrate. Zařízení se používá pro zjišťování a migraci technologie Hyper-V. Při registraci zařízení Azure Migrate vytvoří dvě aplikace Azure Active Directory (Azure AD), které zařízení jednoznačně identifikují:
    - První aplikace komunikuje s koncovými body služby Azure Migrate.
    - Druhá aplikace přistupuje k Azure Key Vault vytvořenému během registrace, aby se ukládaly informace o aplikaci Azure AD a nastavení konfigurace zařízení.



### <a name="assign-permissions-to-create-project"></a>Přiřadit oprávnění k vytvoření projektu

Ověřte, že máte oprávnění k vytvoření projektu Azure Migrate.

1. V Azure Portal otevřete předplatné a vyberte **řízení přístupu (IAM)** .
2. V části **kontrolovat přístup**Najděte příslušný účet a kliknutím na něj Zobrazte oprávnění.
3. Měli byste mít oprávnění **Přispěvatel** nebo **Owner** .
    - Pokud jste právě vytvořili bezplatný účet Azure, jste vlastníkem svého předplatného.
    - Pokud nejste vlastníkem předplatného, pracujte s vlastníkem a přiřaďte roli.


### <a name="assign-permissions-to-register-the-appliance"></a>Přiřazení oprávnění k registraci zařízení

Pomocí jedné z následujících metod můžete přiřadit oprávnění pro Azure Migrate k vytváření aplikací Azure AD, které vytváří během registrace zařízení.

- Tenant nebo globální správce může udělit oprávnění uživatelům v tenantovi, aby mohli vytvářet a registrovat aplikace služby Azure AD.
- Tenant nebo globální správce může k účtu přiřadit roli vývojáře aplikace (která má oprávnění).

Je potřeba poznamenat, že:

- Aplikace nemají žádná jiná přístupová oprávnění k předplatnému, kromě výše popsaných výše.
- Tato oprávnění budete potřebovat, jenom když zaregistrujete nové zařízení. Po nastavení zařízení můžete oprávnění odebrat. 


#### <a name="grant-account-permissions"></a>Udělení oprávnění účtu

Tenant nebo globální správce může udělit oprávnění následujícím způsobem:

1. V Azure AD by měl tenant/globální správce přejít na **Azure Active Directory** > **Uživatelé** > **uživatelských nastavení**.
2. Správce by měl nastavit **Registrace aplikací** **Ano**.

    ![Oprávnění služby Azure AD](./media/tutorial-prepare-hyper-v/aad.png)

> [!NOTE]
> Toto je výchozí nastavení, které není citlivé. [Další informace](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance)

#### <a name="assign-application-developer-role"></a>Přiřazení role vývojáře aplikace 

Tenant/globální správce může přiřadit roli vývojář aplikace k účtu. [Další informace](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal)


## <a name="prepare-for-physical-server-assessment"></a>Příprava na posouzení fyzického serveru

K přípravě na fyzické posouzení serveru musíte ověřit nastavení fyzického serveru a ověřit nastavení pro nasazení zařízení:

### <a name="verify-physical-server-settings"></a>Ověřit nastavení fyzického serveru

1. Ověřte [požadavky na fyzický server](migrate-support-matrix-hyper-v.md#assessment-hyper-v-host-requirements) pro vyhodnocování serveru.
2. Ujistěte se, že jsou na fyzických serverech otevřené [požadované porty](migrate-support-matrix-hyper-v.md#assessment-port-requirements) .


### <a name="verify-appliance-settings"></a>Ověření nastavení zařízení

Před nastavením zařízení Azure Migrate a zahájením posouzení v dalším kurzu připravte na nasazení zařízení.

1. [Ověřte](migrate-support-matrix-hyper-v.md#assessment-appliance-requirements) požadavky na zařízení.
2. [Zkontrolujte](migrate-support-matrix-hyper-v.md#assessment-appliance-url-access) adresy URL Azure, ke kterým bude zařízení potřebovat přístup.
3. Zkontrolujte data, která bude zařízení shromažďovat během zjišťování a posouzení.
4. [Poznamenejte si](migrate-support-matrix-hyper-v.md#assessment-port-requirements) požadavky na přístup k portu pro dané zařízení.


### <a name="set-up-an-account-for-physical-server-discovery"></a>Nastavení účtu pro zjišťování fyzického serveru

Azure Migrate potřebuje oprávnění ke zjišťování místních serverů.

- **Windows:** Nastavte místní uživatelský účet na všech serverech Windows, které chcete zahrnout do zjišťování. Uživatelský účet musí být přidán do následujících skupin: – Uživatelé vzdálené plochy – Performance Monitor Users-Performance Log Users
- **Linux:** Na serverech se systémem Linux, které chcete zjistit, potřebujete kořenový účet.


## <a name="next-steps"></a>Další kroky

V tomto kurzu se naučíte:
 
> [!div class="checklist"] 
> * Nastavte oprávnění účtu Azure.
> * Připravte fyzické servery na posouzení.

Přejděte k dalšímu kurzu a vytvořte projekt Azure Migrate a vyhodnoťte fyzické servery pro migraci do Azure.

> [!div class="nextstepaction"] 
> [Vyhodnotit fyzické servery](./tutorial-assess-physical.md) 