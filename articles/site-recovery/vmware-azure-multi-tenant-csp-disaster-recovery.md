---
title: Nastavení zotavení po havárii VMware do Azure v prostředí s více architekturami pomocí Site Recovery a programu poskytovatel cloudových řešení (CSP) | Microsoft Docs
description: Popisuje, jak nastavit zotavení po havárii VMware v prostředí s více klienty pomocí Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: mayg
ms.openlocfilehash: fb2a8a7bb14758ab21eb2183a119f456b53c8562
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97654945"
---
# <a name="set-up-vmware-disaster-recovery-in-a-multi-tenancy-environment-with-the-cloud-solution-provider-csp-program"></a>Nastavení zotavení po havárii VMware v prostředí s více tenantůmi pomocí programu Cloud Solution Provider (CSP)

[Program CSP](https://partner.microsoft.com/cloud-solution-provider) podporuje lepší scénáře pro cloudové služby Microsoftu, včetně Microsoft 365, Enterprise Mobility Suite a Microsoft Azure. U CSP vznikne klient s koncovými vztahy se zákazníky a stane se primárním kontaktním bodem vztahu. Partneři můžou nasazovat předplatná Azure pro zákazníky a kombinovat předplatná s vlastními přidanými nabídkami přizpůsobených hodnot.

S [Azure Site Recovery](site-recovery-overview.md)můžou jako partneři spravovat zotavení po havárii pro zákazníky přímo prostřednictvím CSP. Alternativně můžete použít CSP k nastavení Site Recovery prostředí a zákazníkům umožnit správu vlastních potřeb pro zotavení po havárii v samoobslužné službě. V obou scénářích jsou partneři spojení mezi Site Recovery a jejich zákazníky. Partneři vyplní vztah zákazníka a účtují zákazníky za využití Site Recovery.

Tento článek popisuje, jak může partner vytvářet a spravovat předplatná klientů prostřednictvím zprostředkovatele CSP pro scénář replikace VMware s více klienty.

## <a name="prerequisites"></a>Předpoklady

Chcete-li nastavit replikaci VMware, je třeba provést následující akce:

- [Příprava](tutorial-prepare-azure.md) Prostředky Azure, včetně předplatného Azure, virtuální sítě Azure a účtu úložiště.
- [Připravte](vmware-azure-tutorial-prepare-on-premises.md) místní servery a virtuální počítače VMware.
- Pro každého tenanta Vytvořte samostatný management server, která může komunikovat s virtuálními počítači klienta a servery vCenter. Přístupová práva k tomuto management server by měla mít pouze partner. Přečtěte si další informace o [víceklientské prostředí](vmware-azure-multi-tenant-overview.md).

## <a name="create-a-tenant-account"></a>Vytvoření účtu tenanta

1. Přes [Partnerské centrum Microsoftu](https://partnercenter.microsoft.com/)se přihlaste ke svému účtu CSP.
2. V nabídce **řídicího panelu** vyberte **zákazníci**.
3. Na stránce, která se otevře, klikněte na tlačítko **Přidat zákazníka** .
4. Na stránce **Nový zákazník** vyplňte podrobnosti informace o účtu pro tenanta.

    ![Stránka informace o účtu](./media/vmware-azure-multi-tenant-csp-disaster-recovery/customer-add-filled.png)

5. Pak klikněte na **Další: odběry**.
6. Na stránce Výběr předplatných vyberte **Microsoft Azure** zaškrtávací políčko. Další předplatná můžete přidat hned nebo kdykoli později.
7. Na stránce **Kontrola** potvrďte podrobnosti o tenantovi a pak klikněte na **Odeslat**.
8. Po vytvoření účtu tenanta se zobrazí potvrzovací stránka s podrobnostmi o výchozím účtu a heslem pro toto předplatné. Uložte tyto informace a později podle potřeby změňte heslo, a to prostřednictvím přihlašovací stránky Azure Portal.

Tyto informace můžete sdílet s klientem, jak je, nebo můžete v případě potřeby vytvořit a sdílet samostatný účet.

## <a name="access-the-tenant-account"></a>Přístup k účtu tenanta

K předplatnému tenanta můžete přistupovat prostřednictvím řídicího panelu Microsoft Partner Center.

1. Na stránce **zákazníci** klikněte na název účtu tenanta.
2. Na stránce **předplatná** na účtu tenanta můžete sledovat existující předplatná účtů a podle potřeby přidat další předplatná.
3. Pokud chcete spravovat operace zotavení po havárii klienta, vyberte **všechny prostředky (Azure Portal)**. To vám umožní přístup k předplatným Azure tenanta.

    ![Odkaz všechny prostředky](./media/vmware-azure-multi-tenant-csp-disaster-recovery/all-resources-select.png)  

4. Přístup můžete ověřit kliknutím na odkaz Azure Active Directory v pravém horním rohu Azure Portal.

    ![Odkaz na Azure Active Directory](./media/vmware-azure-multi-tenant-csp-disaster-recovery/aad-admin-display.png)

Nyní můžete provádět a spravovat všechny operace Site Recovery pro tenanta v Azure Portal. Pokud chcete získat přístup k předplatnému tenanta prostřednictvím CSP pro spravované zotavení po havárii, postupujte podle předchozího popsaného postupu.

## <a name="assign-tenant-access-to-the-subscription"></a>Přiřazení přístupu tenanta k předplatnému

1. Ujistěte se, že je nastavená infrastruktura zotavení po havárii. Partneři přistupují k předplatným tenanta prostřednictvím portálu CSP bez ohledu na to, jestli je zotavení po havárii spravované nebo samoobslužné. Nastavte trezor a zaregistrujte infrastrukturu pro odběry klientů.
1. Poskytněte klientovi [účet, který jste vytvořili](#create-a-tenant-account).
1. Do předplatného klienta můžete přidat nového uživatele prostřednictvím portálu CSP následujícím způsobem:

    1. Přejít na stránku předplatného CSP tenanta a pak vyberte možnost **Uživatelé a licence** .

       ![Stránka předplatného zprostředkovatele CSP klienta](./media/vmware-azure-multi-tenant-csp-disaster-recovery/users-and-licences.png)

    1. Nyní vytvořte nového uživatele tak, že zadáte příslušné podrobnosti a vyberete oprávnění, nebo nahrajte seznam uživatelů v souboru CSV.

    1. Po vytvoření nového uživatele se vraťte na Azure Portal. Na stránce **předplatné** vyberte příslušné předplatné.

    1. Vyberte **řízení přístupu (IAM)** a pak klikněte na **přiřazení rolí**.

    1. Kliknutím na **Přidat přiřazení role** přidáte uživatele s příslušnou úrovní přístupu. Uživatelé, kteří byli vytvořeni prostřednictvím portálu CSP, se zobrazí na kartě přiřazení rolí.

        ![Přidání uživatele](./media/vmware-azure-multi-tenant-csp-disaster-recovery/add-user-subscription.png)

- Pro většinu operací správy je role *Přispěvatel* dostačující. Uživatelé s touto úrovní přístupu můžou dělat všechno v předplatném, s výjimkou změn úrovní přístupu (ke kterým se vyžaduje přístup na úrovni *vlastníka*).
- Site Recovery má také tři [předdefinované role uživatele](site-recovery-role-based-linked-access-control.md), které lze použít k dalšímu omezení úrovní přístupu podle potřeby.

## <a name="multi-tenant-environments"></a>Víceklientské prostředí

Existují tři hlavní modely s více klienty:

* **Poskytovatel sdílených hostitelských služeb (HSP)**: partner vlastní fyzickou infrastrukturu a používá sdílené prostředky (vCenter, datacentra, fyzické úložiště atd.) k hostování více virtuálních počítačů tenantů ve stejné infrastruktuře. Partner může jako spravovanou službu poskytnout správu zotavení po havárii, nebo může klient vlastní zotavení po havárii jako samoobslužné řešení.

* **Vyhrazený poskytovatel hostitelských služeb**: partner vlastní fyzickou infrastrukturu, ale využívá vyhrazené prostředky (více servery vCenter, fyzických úložišť dat atd.) k hostování virtuálních počítačů jednotlivých tenantů na samostatné infrastruktuře. Partner může jako spravovanou službu poskytnout správu zotavení po havárii, nebo může tento tenant vlastnit jako samoobslužné řešení.

* **Zprostředkovatel spravovaných služeb (MSP)**: zákazník vlastní fyzickou infrastrukturu, která je hostitelem virtuálních počítačů, a partner poskytuje povolení a správu zotavení po havárii.

Nastavením předplatných tenantů, jak je popsáno v tomto článku, můžete rychle začít s povolením zákazníků v jakémkoli z příslušných modelů s více klienty. Další informace o různých modelech víceklientské architektury a povolení místních řízení přístupu najdete [tady](vmware-azure-multi-tenant-overview.md).

## <a name="next-steps"></a>Další kroky
- Přečtěte si další informace o [řízení přístupu na základě role Azure (Azure RBAC)](site-recovery-role-based-linked-access-control.md) ke správě Azure Site Recovery nasazení.
- Přečtěte si další informace o [architektuře replikace](vmware-azure-architecture.md)z VMware do Azure.
- [Přečtěte si kurz](vmware-azure-tutorial.md) replikace virtuálních počítačů VMware do Azure.
Přečtěte si další informace o [víceklientské prostředích](vmware-azure-multi-tenant-overview.md) pro replikaci virtuálních počítačů VMware do Azure.
