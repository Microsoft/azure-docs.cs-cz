---
title: Příprava virtuálních počítačů VMware pro vyhodnocení a migraci do Azure pomocí služby Azure Migrate | Dokumentace Microsoftu
description: Popisuje postup přípravy pro vyhodnocení a migraci místních virtuálních počítačů VMware do Azure pomocí Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 05/26/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 246df4109beaa218f735416b8e95e27e95058cf2
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67809110"
---
# <a name="prepare-vmware-vms-for-assessment-and-migration-to-azure"></a>Příprava virtuálních počítačů VMware pro vyhodnocení a migraci do Azure

Tento článek popisuje, jak připravit pro vyhodnocení a migraci místních virtuálních počítačů VMware do Azure, pomocí [Azure Migrate](migrate-services-overview.md).

[Azure Migrate](migrate-overview.md) představuje Centrum nástrojů, které vám umožní zjišťovat, posuzovat a migrovat aplikace, infrastruktury a úlohy Microsoft Azure. Centra zahrnuje nástroje Azure Migrate a nezávislé výrobce softwaru třetích stran výrobce (ISV) nabídky. 


Tento kurz je první z řady, který popisuje, jak vyhodnocovat a migrovat virtuální počítače VMware. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Příprava Azure. Nastavení oprávnění pro účet Azure a prostředky pro práci s Azure Migrate.
> * Příprava místních serverů VMware a virtuálních počítačů na posouzení virtuálních počítačů.
> * Příprava místních serverů VMware a virtuálních počítačů pro migraci virtuálních počítačů.

> [!NOTE]
> Kurzy vám ukážou, nejjednodušší způsob nasazení pro scénář tak, že můžete rychle nastavit testování konceptu. Kurzy použijte výchozí možnosti, kde je to možné a nezobrazovat všechna možná nastavení a cesty. Podrobné pokyny najdete v tématu Jak Tos pro VMware vyhodnocení a migraci.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/) před tím, než začnete.


## <a name="prepare-azure"></a>Příprava Azure

Potřebujete tato oprávnění Azure:

- Váš účet Azure potřebuje oprávnění pro vytvoření projektu Azure Migrate pro vyhodnocení a migraci. 
- Pro vyhodnocení a migrace bez agentů virtuálních počítačů VMware Azure Migrate spustí zjednodušené zařízení, která vyhledá virtuální počítače a odesílá data metadat a výkonu virtuálních počítačů do Azure Migrate. V Azure budete potřebovat oprávnění k registraci zařízení Azure Migrate.
- Migrace virtuálních počítačů VMware pomocí Azure Migrate serveru migrace, Azure Migrate vytvoří trezor klíčů ve skupině prostředků, ke správě přístupových klíčů k účtu úložiště replikace v rámci vašeho předplatného. Vytvoření trezoru, budete potřebovat oprávnění k přiřazení role u skupiny prostředků, ve kterém je umístěn projekt Azure Migrate. 


### <a name="assign-permissions-to-create-project"></a>Přidělování oprávnění k vytvoření projektu

1. Na webu Azure Portal, otevřete předplatné a vyberte **řízení přístupu (IAM)** .
2. V **zkontrolovat přístup**vyhledejte příslušný účet a klikněte na něj chcete-li zobrazit oprávnění.
3. Měli byste mít **Přispěvatel** nebo **vlastníka** oprávnění.
    - Pokud jste právě vytvořili bezplatný účet Azure, vlastník předplatného.
    - Pokud si nejste vlastník předplatného, pracujete s vlastníkem přiřazení role.

### <a name="assign-permissions-to-register-the-appliance"></a>Přiřadit oprávnění k registraci zařízení

Pokud nasazujete zařízení Azure Migrate k vyhodnocení nebo spuštění agenta migrace virtuálních počítačů, musíte ho zaregistrovat.

- Během registrace zařízení Azure Migrate vytvoří dvě aplikace Azure Active Directory (Azure AD), které jedinečně identifikují zařízení
    - První aplikace komunikuje se službou Azure Migrate koncových bodů služby.
    - Druhá aplikace nemá přístup k Azure Key Vault vytvoří během registrace k uložení Azure AD informace a zařízení konfigurační nastavení aplikace.
- Můžete přiřadit oprávnění ke službě Azure Migrate k vytvoření těchto aplikací Azure AD pomocí jedné z následujících metod:
    - Tenanta nebo globální správce může udělit oprávnění uživatelům v tenantovi, k vytvoření a registrace aplikace Azure AD.
    - Tenanta nebo globální správce může k účtu přiřadit roli vývojář aplikace, (který má oprávnění).

Je vhodné poznamenat, že:

- Aplikace nemají žádné jiné oprávnění přístupu na předplatné, než je popsaný výše.
- Pouze potřebujete tato oprávnění při registraci nového zařízení. Po nastavení zařízení můžete odebrat oprávnění. 


#### <a name="grant-account-permissions"></a>Udělte účtu oprávnění

Tenanta nebo globální správce může udělit oprávnění následujícím způsobem

1. Ve službě Azure AD, by měl tenanta nebo globální správce přejděte na **Azure Active Directory** > **uživatelé** > **uživatelská nastavení**.
2. Správce by měl nastavit **registrace aplikací** k **Ano**.

    ![Oprávnění Azure AD](./media/tutorial-prepare-vmware/aad.png)

> [!NOTE]
> Toto je výchozí nastavení, která nejsou citlivá. [Další informace](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).



#### <a name="assign-application-developer-role"></a>Přiřazení role aplikace pro vývojáře 

Tenanta nebo globální správce může přiřadit roli vývojář aplikace k účtu. [Další informace](https://docs.microsoft.comazure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal).

## <a name="assign-role-assignment-permissions"></a>Přiřadit oprávnění k přiřazení role

Přiřadíte oprávnění k přiřazení role u skupiny prostředků, ve kterém projektu Azure Migrate nachází, následujícím způsobem:

1. Ve skupině prostředků na webu Azure Portal, vyberte **řízení přístupu (IAM)** .
2. V **zkontrolovat přístup**vyhledejte příslušný účet a klikněte na něj chcete-li zobrazit oprávnění.

    - Spustit posouzení server **Přispěvatel** oprávnění jsou dostačující.
    - Spuštění agenta serveru migrace, měli byste mít **vlastníka** (nebo **Přispěvatel** a **správce uživatelských přístupů**) oprávnění.

3. Pokud nemáte potřebná oprávnění, o ně požádat vlastníka skupiny prostředků. 



## <a name="prepare-for-vmware-vm-assessment"></a>Příprava na posouzení virtuálních počítačů VMware

Příprava na posouzení virtuálních počítačů VMware, budete muset ověřit nastavení virtuálního počítače a hostitele Hyper-V a ověřte nastavení pro nasazení zařízení.

### <a name="verify-vmware-settings"></a>Ověřte nastavení VMware

1. [Ověřte](migrate-support-matrix-vmware.md#assessment-vmware-server-requirements) požadavků na servery VMware na posouzení virtuálních počítačů.
2. [Ujistěte se, že](migrate-support-matrix-vmware.md#assessment-port-requirements) , že požadované porty jsou otevřeny v serverů vCenter.


### <a name="set-up-an-account-for-assessment"></a>Nastavit účet pro posouzení

Azure Migrate potřebuje přístup k systému vCenter Server ke zjištění virtuálních počítačů pro vyhodnocení a migrace bez agenta. Pro účely posouzení pouze potřebujete účet jen pro čtení pro vCenter Server.

Pokud používáte firewall.proxy založené na adrese URL, povolit přístup k požadovaným [adresám URL služby Azure](migrate-support-matrix-vmware.md#assessment-url-access-requirements).

Ujistěte se, že proxy server odstraňuje všechny záznamy CNAME přijaté při hledání adresy URL.


### <a name="verify-appliance-settings-for-assessment"></a>Ověřte nastavení zařízení pro posouzení

Před nastavením zařízení Azure Migrate a začátek hodnocení v dalším kurzu, příprava pro nasazení zařízení.

1. Ověřte, zda [ověřte](migrate-support-matrix-vmware.md#assessment-appliance-requirements) požadavky pro nastavení služby Azure Migrate zařízení ve službě VMware.
2. [Kontrola](migrate-support-matrix-vmware.md#assessment-url-access-requirements) adresy URL Azure, které budete potřebovat přístup k zařízení.
3. Kontrolovat data, která bude shromažďovat zařízení během zjišťování a posouzení.
4. [Poznámka:](migrate-support-matrix-vmware.md#assessment-port-requirements) portů požadavků na přístup pro zařízení.
5. Jako virtuální počítač VMware pomocí soubor OVA nasazením zařízení Azure Migrate. V systému vCenter Server Ujistěte se, že váš účet má oprávnění k vytvoření virtuálního počítače pomocí soubor OVA.


## <a name="prepare-for-agentless-vmware-migration"></a>Příprava na migraci bez agentů VMware

Zkontrolujte požadavky bez agentů migrace virtuálních počítačů VMware.

1. [Kontrola](migrate-support-matrix-vmware.md#agentless-migration-vmware-server-requirements) požadavků na servery VMware pro migraci bez agentů.
2. Nastavit účet pro přístup k serveru vCenter s [požadovaná oprávnění](migrate-support-matrix-vmware.md#agentless-migration-vcenter-server-permissions) migrace bez agenta.
3. [Poznámka:](migrate-support-matrix-vmware.md#agentless-migration-vmware-vm-requirements) požadavky na virtuální počítače VMware, které chcete migrovat do Azure s použitím migrace bez agenta.
4. [Kontrola](migrate-support-matrix-vmware.md#agentless-migration-appliance-requirements) zařízení požadavky na migraci bez agentů.]
5. Poznámka: zařízení [přístup k adrese URL](migrate-support-matrix-vmware.md#agentless-migration-url-access-requirements) a [port přístup](migrate-support-matrix-vmware.md#agentless-migration-port-requirements) požadavky pro migraci bez agentů.


## <a name="prepare-for-agent-based-vmware-migration"></a>Příprava na migraci založené na agentovi VMware

Zkontrolujte požadavky pro migrace virtuálních počítačů VMware na základě agenta.

1. [Kontrola](migrate-support-matrix-vmware.md#agent-based-migration-vmware-server-requirements) požadavků na servery VMware pro migraci bez agentů. 
2. Nastavit účet pro přístup k serveru vCenter s [požadovaná oprávnění](migrate-support-matrix-vmware.md#agent-based-migration-vcenter-server-permissions) migrace bez agenta.
3. [Poznámka:](migrate-support-matrix-vmware.md#agent-based-migration-vmware-vm-requirements) požadavky na virtuální počítače VMware, který chcete migrovat do Azure pomocí migrace založené na agentovi, včetně instalace služby Mobility service na každém virtuálním počítači, které chcete migrovat.
4. Poznámka: [přístup k adrese URL](migrate-support-matrix-vmware.md#agent-based-migration-url-access-requirements).
5. Kontrola [port přístup](migrate-support-matrix-vmware.md#agent-based-migration-port-requirements) požadavky na služby Mobility spuštěné na každém virtuálním počítači a pro konfigurační server Azure Migrate.

## <a name="next-steps"></a>Další postup

V tomto kurzu se naučíte:
 
> [!div class="checklist"] 
> * Nastavte oprávnění Azure.
> * Připravit VMware pro vyhodnocení a migraci.


Pokračovat druhé části kurzu nastavit projekt Azure Migrate a posouzení virtuálních počítačů VMware pro migraci do Azure.

> [!div class="nextstepaction"] 
> [Posouzení virtuálních počítačů VMware](./tutorial-migrate-vmware.md) 

