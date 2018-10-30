---
title: Nastavení zotavení po havárii VMware do Azure v prostředí více tenantů pomocí Site Recovery a programu Cloud Solution Provider (CSP) | Dokumentace Microsoftu
description: Popisuje, jak nastavit zotavení po havárii VMware v prostředí s více tenanty pomocí Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: mayg
ms.openlocfilehash: c26421f848cf25e391589fa791f801e30fd14797
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2018
ms.locfileid: "50208897"
---
# <a name="set-up-vmware-disaster-recovery-in-a-multi-tenancy-environment-with-the-cloud-solution-provider-csp-program"></a>Nastavení zotavení po havárii VMware v prostředí více tenantů v programu Cloud Solution Provider (CSP)

[Programu CSP](https://partner.microsoft.com/en-US/cloud-solution-provider) podporovalo společně lepší scénáře pro cloudové služby Microsoftu, včetně Office 365, Enterprise Mobility Suite a Microsoft Azure. S CSP partneři vlastní začátku do konce vztah se zákazníky a stát vztah primární kontaktní bod. Partneři můžete nasadit předplatná Azure pro zákazníky a kombinovat předplatných s využitím vlastní s přidanou hodnotou, přizpůsobené nabídky.

S [Azure Site Recovery](site-recovery-overview.md), jako partneři můžou spravovat zotavení po havárii pro zákazníky, kteří přímo prostřednictvím zprostředkovatele kryptografických služeb. Alternativně můžete nastavit prostředí Site Recovery pomocí zprostředkovatele kryptografických služeb a umožněte zákazníkům spravovat jejich vlastní potřebovat obnovení po havárii v podobě samoobslužné služby. V obou případech jsou partneři spolupráci mezi Site Recovery a jejich zákazníky. Partneři služeb vztah se zákazníkem a vyúčtování pro zákazníky za použití Site Recovery.

Tento článek popisuje, jak můžete jako partner můžete vytvořit a spravovat tenanta předplatná prostřednictvím zprostředkovatele kryptografických služeb, pro scénářích replikace VMware s více tenanty.

## <a name="prerequisites"></a>Požadavky

Chcete-li nastavení replikace VMware, postupujte takto:

- [Příprava](tutorial-prepare-azure.md) prostředky Azure, včetně předplatného služby Azure, virtuální síť Azure a účet úložiště.
- [Příprava](vmware-azure-tutorial-prepare-on-premises.md) místních serverů VMware a virtuálních počítačů.
- Pro každého klienta vytvořte samostatné management server, který může komunikovat s virtuální počítače klientů a serverů vCenter. Pouze jako partner měli byste přístupová práva k tomuto serveru pro správu. Další informace o [víceklientské prostředí](vmware-azure-multi-tenant-overview.md).

## <a name="create-a-tenant-account"></a>Vytvořte si účet tenanta

1. Prostřednictvím [Microsoft Partner Center](https://partnercenter.microsoft.com/), přihlaste se ke svému účtu CSP.
2. Na **řídicí panel** nabídce vyberte možnost **zákazníkům**.
3. Na stránce, které se otevře, klikněte na tlačítko **odběratele přidat** tlačítko.
4. V **nového zákazníka** stránky, zadejte podrobné informace o účtu pro příslušného tenanta.

    ![Na stránce informace o účtu](./media/vmware-azure-multi-tenant-csp-disaster-recovery/customer-add-filled.png)

5. Pak klikněte na tlačítko **Další: předplatná**.
6. Na stránce Výběr předplatných, vyberte **Microsoft Azure** zaškrtávací políčko. Můžete přidat další předplatná nyní nebo v každém okamžiku.
7. Na **revize** stránce, potvrďte podrobnosti o tenantovi a klikněte na **odeslat**.
8. Po vytvoření účtu tenanta, zobrazí se stránka s potvrzením, zobrazení podrobností o výchozí účet a heslo pro dané předplatné. Uložte si informace a změnit heslo později podle potřeby přes Azure portal – přihlašovací stránku.

Tyto informace můžete sdílet s tenantem, jako je, nebo můžete vytvářet a sdílet samostatný účet, v případě potřeby.

## <a name="access-the-tenant-account"></a>Přístup k účtu tenanta

Předplatné klienta přístupné prostřednictvím řídicího panelu Microsoft Partner Center.

1. Na **zákazníkům** klikněte na název účtu tenanta.
2. V **předplatná** stránky účtu tenanta, můžete sledovat na stávající předplatná účtu a přidat další předplatná, podle potřeby.
3. Ke správě tenanta operace zotavení po havárii, vyberte **všechny prostředky (portál Azure portal)**. To vám uděluje že přístup k předplatným Azure vašeho tenanta.

    ![Odkaz všechny prostředky](./media/vmware-azure-multi-tenant-csp-disaster-recovery/all-resources-select.png)  

4. Přístup můžete ověřit kliknutím na odkaz Azure Active Directory v horním pravém rohu webu Azure portal.

    ![Propojení služby Azure Active Directory](./media/vmware-azure-multi-tenant-csp-disaster-recovery/aad-admin-display.png)

Teď můžete provádět a spravovat všechny operace Site Recovery pro příslušného tenanta na portálu Azure portal. Pro přístup k předplatnému klienta prostřednictvím zprostředkovatele kryptografických služeb pro spravované zotavení po havárii, postupujte podle procesu popsané.

## <a name="assign-tenant-access-to-the-subscription"></a>Přiřadit přístup klienta k předplatnému

1. Ujistěte se, že je nastavení infrastruktury pro zotavení po havárii. Partneři přístup tenanta předplatná prostřednictvím portálu pro CSP, bez ohledu na to, zda je spravováno zotavení po havárii nebo samoobslužné funkce. Nastavit trezor a zaregistrujte infrastrukturu pro předplatná tenanta.
2. Zadejte tenanta s [účtu vytvořeného](#create-a-tenant-account).
3. Nového uživatele můžete přidat do předplatného tenanta na portálu zprostředkovatele kryptografických služeb následujícím způsobem:

    (a) přejděte na stránku předplatné tenanta CSP a pak vyberte **uživatelé a licence** možnost.

      ![Stránku odběru služby CSP vašeho tenanta](./media/vmware-azure-multi-tenant-csp-disaster-recovery/users-and-licences.png)

      b) vytvoření nového uživatele, nyní zadáním relevantní podrobnosti a výběrem oprávnění, nebo tak že nahrajete seznam uživatelů v souboru CSV.
    c) po vytvoření nového uživatele, přejděte zpět na web Azure Portal. V **předplatné** stránky, vyberte příslušné předplatné.
    d) vyberte **řízení přístupu (IAM)** a potom klikněte na tlačítko **přidat**, pokud chcete přidat uživatele s úrovní odpovídající přístup. Uživatelé, které byly vytvořeny na portálu zprostředkovatele kryptografických služeb se automaticky zobrazí na stránce, které se otevře po kliknutí na úroveň přístupu.

      ![Přidání uživatele](./media/vmware-azure-multi-tenant-csp-disaster-recovery/add-user-subscription.png)

- K většině operací správy *Přispěvatel* roli je dostatečná. Uživatelé s touto úrovní přístupu můžou provádět vše na předplatné s tím rozdílem, změna úrovně přístupu (pro který *vlastníka*– úroveň přístupu se vyžaduje).
- Site Recovery má také tři [předdefinované role uživatelů](site-recovery-role-based-linked-access-control.md), který lze použít pro další omezení úrovně přístupu podle potřeby.

## <a name="multi-tenant-environments"></a>Prostředí s více tenanty

Existují tři hlavní modely víceklientské:

* **Sdílené hostování poskytovatele služeb (HSP)**: partnera vlastní fyzické infrastruktury, a používá sdílené prostředky (vCenter, datová centra, fyzického úložiště a tak dále) pro hostování více klientské virtuální počítače ve stejné infrastruktuře. Partner nabízí správu zotavení po havárii jako spravovanou službu nebo tenanta může vlastnit zotavení po havárii jako řešení samoobslužné služby.

* **Vyhrazené poskytovatele služby hostingu**: partnera vlastní fyzické infrastruktury, ale používá vyhrazené prostředky (více vCenters fyzických úložišť a tak dále) pro hostování virtuálních počítačů každého tenanta do samostatného infrastruktury. Partner nabízí správu zotavení po havárii jako spravovanou službu nebo tenanta může vlastnit jako řešení samoobslužné služby.

* **Spravovat poskytovatele služeb (MSP)**: zákazník je vlastníkem fyzické infrastruktury, který hostuje virtuální počítače a partnera poskytuje funkce pro kontrolu zotavení po havárii a správu.

Pomocí nastavení předplatného tenanta, jak je popsáno v tomto článku, vám rychle začít, takže se zákazníci můžou v žádné z příslušných modely více tenantů. Další informace o různých modelů více tenantů a povolení místních řízení přístupu [tady](vmware-azure-multi-tenant-overview.md).

## <a name="next-steps"></a>Další postup
- Další informace o [řízení přístupu na základě rolí](site-recovery-role-based-linked-access-control.md) ke správě nasazení Azure Site Recovery.
- Další informace o replikaci z VMware do Azure [architektura replikace](vmware-azure-architecture.md).
- [Najdete v kurzu](vmware-azure-tutorial.md) pro replikaci virtuálních počítačů VMware do Azure.
Další informace o [víceklientské prostředí](vmware-azure-multi-tenant-overview.md) pro replikaci virtuálních počítačů VMware do Azure.
