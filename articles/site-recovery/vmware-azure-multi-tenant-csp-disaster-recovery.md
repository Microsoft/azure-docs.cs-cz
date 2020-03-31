---
title: Nastavení zotavení po havárii společnosti VMware do Azure v prostředí s více klienty pomocí programu Site Recovery a programu Zprostředkovatel cloudových řešení (CSP) | Dokumenty společnosti Microsoft
description: Popisuje, jak nastavit zotavení po havárii v systému VMware ve víceklientském prostředí pomocí Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: mayg
ms.openlocfilehash: 77b64f09b7fd1429eb23c4407c729dfc0aafdf2b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60460983"
---
# <a name="set-up-vmware-disaster-recovery-in-a-multi-tenancy-environment-with-the-cloud-solution-provider-csp-program"></a>Nastavení zotavení po havárii společnosti VMware v prostředí s více klienty pomocí programu Zprostředkovatel cloudových řešení (CSP)

[Program CSP](https://partner.microsoft.com/en-US/cloud-solution-provider) podporuje lepší články pro cloudové služby Microsoftu, včetně Office 365, Enterprise Mobility Suite a Microsoft Azure. S csp, partneři vlastní vztah mezi koncovými zákazníky a stát se primárním kontaktním bodem vztahu. Partneři můžou nasadit předplatná Azure pro zákazníky a kombinovat předplatná s vlastními nabídkami s přidanou hodnotou.

S [Azure Site Recovery](site-recovery-overview.md), jako partneři můžete spravovat zotavení po havárii pro zákazníky přímo prostřednictvím CSP. Alternativně můžete použít csp nastavit prostředí site recovery a nechat zákazníky spravovat své vlastní potřeby zotavení po havárii samoobslužným způsobem. V obou scénářích jsou partneři prostředníkem mezi site recovery a jejich zákazníky. Partneři obsluhují vztah se zákazníky a účtují zákazníkům využití obnovení webu.

Tento článek popisuje, jak můžete jako partner vytvářet a spravovat předplatná tenanta prostřednictvím csp, pro scénář replikace více klientů VMware.

## <a name="prerequisites"></a>Požadavky

Chcete-li nastavit replikaci společnosti VMware, musíte provést následující kroky:

- [Připravte si](tutorial-prepare-azure.md) Prostředky Azure, včetně předplatného Azure, virtuální sítě Azure a účtu úložiště.
- [Připravte](vmware-azure-tutorial-prepare-on-premises.md) místní servery vmware a virtuální chod.
- Pro každého klienta vytvořte samostatný server pro správu, který může komunikovat s virtuálními počítači klienta a servery vCenter. Přístupová práva k tomuto serveru pro správu byste měli mít pouze vy jako partner. Další informace o [prostředích s více klienty](vmware-azure-multi-tenant-overview.md).

## <a name="create-a-tenant-account"></a>Vytvoření účtu klienta

1. Prostřednictvím [Centra partnerů společnosti Microsoft](https://partnercenter.microsoft.com/)se přihlaste ke svému účtu CSP.
2. V nabídce **Řídicí panel** vyberte **položku Zákazníci**.
3. Na stránce, která se otevře, klikněte na tlačítko **Přidat zákazníka.**
4. Na stránce **Nový zákazník** vyplňte podrobnosti o účtu pro klienta.

    ![Stránka Informace o účtu](./media/vmware-azure-multi-tenant-csp-disaster-recovery/customer-add-filled.png)

5. Pak klikněte na **Další: Odběry**.
6. Na stránce výběru předplatných zaškrtněte políčko **Microsoft Azure.** Další předplatná můžete přidat nyní nebo kdykoli jindy.
7. Na stránce **Revize** potvrďte podrobnosti o klientovi a klikněte na **Odeslat**.
8. Po vytvoření účtu klienta se zobrazí stránka s potvrzením, která zobrazuje podrobnosti o výchozím účtu a heslo pro toto předplatné. Uložte informace a později změňte heslo, jak je to nutné, prostřednictvím přihlašovací stránky portálu Azure Portal.

Tyto informace můžete sdílet s klientem tak, jak jsou, nebo můžete v případě potřeby vytvořit a sdílet samostatný účet.

## <a name="access-the-tenant-account"></a>Přístup k účtu klienta

K předplatnému klienta můžete přistupovat prostřednictvím řídicího panelu Microsoft Partner Center.

1. Na stránce **Zákazníci** klikněte na název účtu klienta.
2. Na stránce **Předplatná** účtu klienta můžete sledovat existující předplatná účtu a podle potřeby přidat další předplatná.
3. Chcete-li spravovat operace zotavení po havárii klienta, vyberte **všechny prostředky (portál Azure).** To vám uděluje přístup k předplatným Azure klienta.

    ![Odkaz Všechny zdroje](./media/vmware-azure-multi-tenant-csp-disaster-recovery/all-resources-select.png)  

4. Přístup můžete ověřit kliknutím na odkaz Azure Active Directory v pravém horním rohu portálu Azure.

    ![Propojení služby Azure Active Directory](./media/vmware-azure-multi-tenant-csp-disaster-recovery/aad-admin-display.png)

Teď můžete provádět a spravovat všechny operace obnovení webu pro klienta na webu Azure Portal. Chcete-li získat přístup k předplatnému klienta prostřednictvím csp pro spravované zotavení po havárii, postupujte podle výše popsaného procesu.

## <a name="assign-tenant-access-to-the-subscription"></a>Přiřazení přístupu klienta k předplatnému

1. Ujistěte se, že je nastavena infrastruktura pro zotavení po havárii. Partneři přistupují k předplatným klienta prostřednictvím portálu CSP, bez ohledu na to, jestli se spravuje nebo samoobslužné obnovení po havárii. Nastavte trezor a zaregistrujte infrastrukturu na předplatná klienta.
2. Poskytněte klientovi [účet, který jste vytvořili](#create-a-tenant-account).
3. Nového uživatele můžete přidat do předplatného klienta prostřednictvím portálu CSP následujícím způsobem:

    a) Přejděte na stránku předplatného CSP klienta a vyberte možnost **Uživatelé a licence.**

      ![Stránka předplatného CSP klienta](./media/vmware-azure-multi-tenant-csp-disaster-recovery/users-and-licences.png)

    b) Nyní vytvořte nového uživatele zadáním příslušných údajů a výběrem oprávnění nebo nahráním seznamu uživatelů do souboru CSV.
    
    c) Po vytvoření nového uživatele se vraťte na portál Azure. Na stránce **Předplatné** vyberte příslušné předplatné.

    d) Vyberte **ovládací prvek přístupu (IAM)** a klepněte na **položku Přiřazení rolí**.

    e) Kliknutím na **Přidat přiřazení role** přidáte uživatele s příslušnou úrovní přístupu. Uživatelé, kteří byli vytvořeni prostřednictvím portálu CSP, jsou zobrazeni na kartě Přiřazení rolí.

      ![Přidání uživatele](./media/vmware-azure-multi-tenant-csp-disaster-recovery/add-user-subscription.png)

- Pro většinu operací správy je dostatečná role *přispěvatele.* Uživatelé s touto úrovní přístupu můžete dělat vše, co na předplatné s výjimkou změnit úrovně přístupu (pro které je vyžadován přístup na úrovni *vlastníka).*
- Obnovení webu má také tři [předdefinované uživatelské role](site-recovery-role-based-linked-access-control.md), které lze použít k dalšímu omezení úrovní přístupu podle potřeby.

## <a name="multi-tenant-environments"></a>Prostředí s více klienty

Existují tři hlavní víceklientské modely:

* **Poskytovatel sdílených hostingových služeb (HSP):** Partner vlastní fyzickou infrastrukturu a používá sdílené prostředky (vCenter, datová centra, fyzické úložiště a tak dále) k hostování více virtuálních počítačů klientů na stejné infrastruktuře. Partner může poskytovat správu zotavení po havárii jako spravovanou službu nebo klient může vlastní zotavení po havárii jako samoobslužné řešení.

* **Vyhrazený poskytovatel hostingových služeb:** Partner vlastní fyzickou infrastrukturu, ale používá vyhrazené prostředky (více center vCenters, úložiště fyzických dat a tak dále) k hostování virtuálních počítačů každého klienta na samostatné infrastruktuře. Partner může poskytovat správu zotavení po havárii jako spravovanou službu nebo ji klient může vlastnit jako samoobslužné řešení.

* **Zprostředkovatel spravovaných služeb (MSP):** Zákazník vlastní fyzickou infrastrukturu, která hostuje virtuální zařízení, a partner poskytuje povolení a správu zotavení po havárii.

Nastavením předplatných tenantů, jak je popsáno v tomto článku, můžete rychle spustit povolení zákazníkům v některém z relevantních modelů více klientů. Další informace o různých víceklientských modelech a povolení místních řízení přístupu [najdete zde](vmware-azure-multi-tenant-overview.md).

## <a name="next-steps"></a>Další kroky
- Přečtěte si další informace o [řízení přístupu na základě rolí](site-recovery-role-based-linked-access-control.md) pro správu nasazení Azure Site Recovery.
- Další informace o [replikační architektuře](vmware-azure-architecture.md)VMware do Azure .
- [Projděte si kurz](vmware-azure-tutorial.md) pro replikaci virtuálních počítačů VMware do Azure.
Další informace o [prostředích s více tenanty](vmware-azure-multi-tenant-overview.md) pro replikaci virtuálních počítačů VMware do Azure.
