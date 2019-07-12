---
title: Příprava virtuálních počítačů Hyper-V pro vyhodnocení a migraci do Azure pomocí služby Azure Migrate | Dokumentace Microsoftu
description: Popisuje postup přípravy pro vyhodnocení a migraci virtuálních počítačů Hyper-V do Azure pomocí Azure Migrate.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 06/24/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: de1aa81e8c98ba5f8e210072344beab21f1a4ae5
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67809705"
---
# <a name="prepare-for-assessment-and-migration-of-hyper-v-vms-to-azure"></a>Příprava pro vyhodnocení a migraci virtuálních počítačů Hyper-V do Azure

Tento článek popisuje, jak připravit pro vyhodnocení a migraci místních virtuálních počítačů Hyper-V do Azure s využitím [Azure Migrate](migrate-services-overview.md).

[Azure Migrate](migrate-overview.md) představuje Centrum nástrojů, které vám umožní zjišťovat, posuzovat a migrovat aplikace, infrastruktury a úlohy Microsoft Azure. Centra zahrnuje nástroje Azure Migrate a nezávislé výrobce softwaru třetích stran výrobce (ISV) nabídky. 

Tento kurz je první z řady, který popisuje, jak vyhodnocovat a migrovat virtuální počítače Hyper-V do Azure. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Příprava Azure. Nastavení oprávnění pro účet Azure a prostředky pro práci s Azure Migrate.
> * Příprava místních hostitelích Hyper-V a virtuální počítače na server posouzení.
> * Připravte místní hostitelé Hyper-V a virtuálních počítačů pro migraci serveru.


> [!NOTE]
> Kurzy vám ukážou, nejjednodušší způsob nasazení pro scénář tak, že můžete rychle nastavit testování konceptu. Kurzy použijte výchozí možnosti, kde je to možné a nezobrazovat všechna možná nastavení a cesty. Podrobné pokyny najdete v tématu Jak Tos pro posouzení Hyper-V a migrace.


Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/) před tím, než začnete.


## <a name="prepare-azure"></a>Příprava Azure

### <a name="azure-permissions"></a>Oprávnění Azure

Budete potřebovat několik oprávnění k nasazení Azure Migrate:

- Váš účet Azure potřebuje oprávnění pro vytvoření projektu Azure Migrate pro vyhodnocení a migraci. 
- Váš účet Azure potřebuje oprávnění k registraci zařízení Azure Migrate.
    - Pro vyhodnocení Azure Migrate spustí zjednodušené zařízení, která vyhledá virtuální počítače Hyper-V a odesílá data metadat a výkonu virtuálních počítačů do Azure Migrate.
    - Během registrace zařízení Azure Migrate vytvoří dvě aplikace Azure Active Directory (Azure AD), které jedinečně identifikují zařízení:
        - První aplikace komunikuje se službou Azure Migrate koncových bodů služby.
        - Druhá aplikace nemá přístup k Azure Key Vault vytvoří během registrace k uložení Azure AD informace a zařízení konfigurační nastavení aplikace.
    - Můžete přiřadit oprávnění ke službě Azure Migrate k vytvoření těchto aplikací Azure AD pomocí jedné z následujících metod:
        - Tenanta nebo globální správce může udělit oprávnění uživatelům v tenantovi, k vytvoření a registrace aplikace Azure AD.
        - Tenanta nebo globální správce může k účtu přiřadit roli vývojář aplikace, (který má oprávnění).
    - Je vhodné poznamenat, že:
        - Aplikace nemají žádné jiné oprávnění přístupu na předplatné, než je popsaný výše.
        - Pouze potřebujete tato oprávnění při registraci nového zařízení. Po nastavení zařízení můžete odebrat oprávnění. 


### <a name="assign-permissions-to-create-project"></a>Přidělování oprávnění k vytvoření projektu

Zkontrolujte, že máte oprávnění k vytvoření projektu Azure Migrate.

1. Na webu Azure Portal, otevřete předplatné a vyberte **řízení přístupu (IAM)** .
2. V **zkontrolovat přístup**vyhledejte příslušný účet a klikněte na něj chcete-li zobrazit oprávnění.
3. Měli byste mít **Přispěvatel** nebo **vlastníka** oprávnění.
    - Pokud jste právě vytvořili bezplatný účet Azure, vlastník předplatného.
    - Pokud si nejste vlastník předplatného, pracujete s vlastníkem přiřazení role.


### <a name="assign-permissions-to-register-the-appliance"></a>Přiřadit oprávnění k registraci zařízení

Pokud nasazujete zařízení Azure Migrate k posouzení virtuálních počítačů, musíte ho zaregistrovat.

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

Tenanta nebo globální správce může udělit oprávnění následujícím způsobem:

1. Ve službě Azure AD, by měl tenanta nebo globální správce přejděte na **Azure Active Directory** > **uživatelé** > **uživatelská nastavení**.
2. Správce by měl nastavit **registrace aplikací** k **Ano**.

    ![Oprávnění Azure AD](./media/tutorial-prepare-hyper-v/aad.png)

> [!NOTE]
> Toto je výchozí nastavení, která nejsou citlivá. [Další informace](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).



#### <a name="assign-application-developer-role"></a>Přiřazení role aplikace pro vývojáře 

Tenanta nebo globální správce může přiřadit roli vývojář aplikace k účtu. [Další informace](https://docs.microsoft.comazure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal).


## <a name="prepare-for-hyper-v-assessment"></a>Příprava na posouzení Hyper-V

Příprava na posouzení Hyper-V, ověřte nastavení virtuálního počítače a hostitele Hyper-V a ověřte nastavení pro nasazení zařízení.

### <a name="verify-hyper-v-host-settings"></a>Ověřte nastavení hostitele Hyper-V

1. Ověřte [požadavky na hostitele Hyper-V](migrate-support-matrix-hyper-v.md#assessment-hyper-v-host-requirements) pro server assessment.
2. Ujistěte se, [požadované porty](migrate-support-matrix-hyper-v.md#assessment-port-requirements) jsou spuštěné na hostitelích Hyper-V.

### <a name="enable-powershell-remoting-on-hosts"></a>Povolení vzdálené komunikace Powershellu na hostitele

Vzdálená komunikace Powershellu na každém hostiteli, nastavte následujícím způsobem:

1. Na každém hostiteli otevřete konzolu Powershellu jako správce.
2. Spusťte tento příkaz:

    ```
    Enable-PSRemoting -force
    ```

### <a name="enable-credssp-on-hosts"></a>Povolit zprostředkovatele CredSSP na hostitelích

Pokud jsou disky virtuálních počítačů na sdílených složkách protokolu SMB, provedení tohoto kroku na každý relevantní hostitel Hyper-V. Tento krok se používá ke zjišťování informace o konfiguraci pro virtuální počítače Hyper-V s disky ve sdílených složkách SMB. Pokud nemáte k dispozici disky virtuálních počítačů na sdílených složkách protokolu SMB, můžete přeskočit na krok.

1. Identifikujte hostitele Hyper-V s disky virtuálních počítačů Hyper-V na sdílených složkách SMB.
2. Na každé identifikované hostitele Hyper-V, spusťte následující příkaz:

    ```
    Enable-WSManCredSSP -Role Server -Force
    ```

- Ověřování CredSSP umožňuje hostiteli Hyper-V pro delegování přihlašovacích údajů jménem klienta Azure Migrate.
- Tento příkaz můžete spustit vzdáleně na všech hostitelích Hyper-V.
- Pokud chcete přidat nové uzly hostitele v clusteru se automaticky přidají pro zjišťování, ale budete muset ručně povolit zprostředkovatele CredSSP na nových uzlů a v případě potřeby.

### <a name="verify-appliance-settings"></a>Ověřte nastavení zařízení

Před nastavením zařízení Azure Migrate a začátek hodnocení v dalším kurzu, příprava pro nasazení zařízení.

1. [Ověřte](migrate-support-matrix-hyper-v.md#assessment-appliance-requirements) požadavky na zařízení.
2. [Kontrola](migrate-support-matrix-hyper-v.md#assessment-appliance-url-access) adresy URL Azure, které budete potřebovat přístup k zařízení.
3. Kontrolovat data, která bude shromažďovat zařízení během zjišťování a posouzení.
4. [Poznámka:](migrate-support-matrix-hyper-v.md#assessment-port-requirements) portů požadavků na přístup pro zařízení.


### <a name="set-up-an-account-for-vm-discovery"></a>Nastavit účet pro zjišťování virtuálních počítačů

Azure Migrate vyžaduje oprávnění ke zjišťování místních virtuálních počítačů.

- Nastavení domény nebo místní uživatelský účet s oprávněními správce na hostiteli nebo clusteru Hyper-V.

    - Budete potřebovat jeden účet pro všechny hostitele a clustery, které chcete zahrnout do zjišťování.
    - Účet může být místní nebo doménový účet. Doporučujeme, abyste že ho má oprávnění správce na hostitelích Hyper-V nebo clusterů.
    - Případně pokud nechcete přiřazovat oprávnění správce, jsou potřeba následující oprávnění:
        - Uživatelé vzdálené správy
        - Správci Hyper-V
        - Performance Monitor Users

### <a name="enable-integration-services-on-vms"></a>Povolit integrační služby na virtuálních počítačích

Integrační služby musí být povolené na každý virtuální počítač tak, aby Azure Migrate můžete zachytit informace o operačním systému na virtuálním počítači.

- Na virtuálních počítačích, které chcete zjistit a posoudit, povolte [integrační služby Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services) na každém virtuálním počítači. 

## <a name="prepare-for-hyper-v-migration"></a>Příprava na migraci Hyper-V

1. [Kontrola](migrate-support-matrix-hyper-v.md#migration-hyper-v-host-requirements) požadavky na hostitele Hyper-V pro migraci.
2. [Kontrola](migrate-support-matrix-hyper-v.md#migration-hyper-v-vm-requirements) požadavky na virtuální počítače Hyper-V, který chcete migrovat do Azure.
3. [Poznámka:](migrate-support-matrix-hyper-v.md#migration-hyper-v-host-url-access) adresy URL Azure, které technologii Hyper-v hostitelům a clusterům potřebují přístup k migraci virtuálního počítače.

## <a name="next-steps"></a>Další postup

V tomto kurzu se naučíte:
 
> [!div class="checklist"] 
> * Nastavení oprávnění účtu Azure.
> * Hostitelé Hyper-V a virtuálních počítačů pro vyhodnocení a migraci.

Pokračovat k dalšímu kurzu, vytvořte projekt Azure Migrate a posouzení virtuálních počítačů Hyper-V pro migraci do Azure

> [!div class="nextstepaction"] 
> [Posouzení virtuálních počítačů Hyper-V](./tutorial-assess-hyper-v.md) 
