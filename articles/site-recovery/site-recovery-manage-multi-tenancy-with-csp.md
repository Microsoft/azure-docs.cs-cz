---
title: "Správa více klientů ve službě Azure Site Recovery s programem Cloud Solution Provider (CSP) | Microsoft Docs"
description: "Popisuje, jak vytvářet a spravovat odběry klienta prostřednictvím zprostředkovatele kryptografických služeb a nasazení Azure Site Recovery v instalačním programu více klientů"
services: site-recovery
documentationcenter: 
author: mayanknayar
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2018
ms.author: manayar
ms.openlocfilehash: 6dc8b573e66eaae1b5cb923ae72333fb959d0969
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/28/2018
---
# <a name="manage-multi-tenancy-with-the-cloud-solution-provider-csp-program"></a>Správa více klientů s programem Cloud Solution Provider (CSP)

[CSP program](https://partner.microsoft.com/en-US/cloud-solution-provider) podporovalo lepší společně scénářů pro cloudové služby Microsoftu, včetně služeb Office 365, Enterprise Mobility Suite a Microsoft Azure. S CSP partneři vlastní začátku do konce relace se zákazníky a stát kontaktní bod primární relace. Partnery můžete nasadit předplatná Azure pro zákazníky a kombinovat předplatných s vlastní s přidanou hodnotou, přizpůsobené nabídky.

Azure Site Recovery partneři můžou spravovat úplného řešení zotavení po havárii pro zákazníky přímo přes zprostředkovatele kryptografických služeb. Nebo nastavit prostředí Site Recovery a Informujte zákazníky, spravovat jejich vlastní potřeby zotavení po havárii způsobem samoobslužné služby mohou použít CSP. V obou případech partneři jsou spolupráci mezi Site Recovery a zákazníků. Partneři služby vztah se zákazníkem a vyúčtování pro zákazníky za použití Site Recovery.

Tento článek popisuje, jak vytvořit a spravovat předplatné klienta prostřednictvím poskytovatele CSP, pro instalace VMware s více klienty partnera.

## <a name="prerequisites"></a>Požadavky

- [Příprava](tutorial-prepare-azure.md) prostředky Azure, včetně předplatné Azure, virtuální sítě Azure a účet úložiště.
- [Příprava](tutorial-prepare-on-premises-vmware.md) VMware místní serverů VMware a virtuálních počítačů.
- Pro každého klienta vytvořte samostatné management server, který může komunikovat s klientské virtuální počítače a jeho vCenter. Pouze partner má přístupová práva k tomuto serveru. Další informace o různých prostředích více klientů naleznete [víceklientské VMware](site-recovery-multi-tenant-support-vmware-using-csp.md) pokyny.

## <a name="create-a-tenant-account"></a>Vytvořit účet klienta

1. Prostřednictvím [Microsoft Partner Center](https://partnercenter.microsoft.com/), přihlaste se ke svému účtu CSP.

2. Na **řídicí panel** nabídce vyberte možnost **zákazníci**.

    ![Odkaz zákazníků Center partnera společnosti Microsoft](./media/site-recovery-manage-multi-tenancy-with-csp/csp-dashboard-display.png)

3. Na stránce, které se otevře, klikněte na **přidat zákazníka** tlačítko.

    ![Tlačítko Přidat zákazníka](./media/site-recovery-manage-multi-tenancy-with-csp/add-new-customer.png)

4. Na **nového zákazníka** stránky, zadejte podrobnosti informace o účtu pro klienta a pak klikněte na tlačítko **Další: odběry**.

    ![Stránka informace o účtu](./media/site-recovery-manage-multi-tenancy-with-csp/customer-add-filled.png)

5. Na stránce Výběr předplatných, vyberte **Microsoft Azure** zaškrtávací políčko. Můžete přidat další odběry nyní nebo kdykoli později.

    ![Zaškrtněte políčko předplatné Microsoft Azure](./media/site-recovery-manage-multi-tenancy-with-csp/azure-subscription-selection.png)

6. Na **zkontrolujte** Potvrďte podrobnosti klienta a pak klikněte na tlačítko **odeslání**.

    ![Kontrolní stránku](./media/site-recovery-manage-multi-tenancy-with-csp/customer-summary-page.png)  

    Po vytvoření účtu klienta, se zobrazí potvrzovací stránku, zobrazení podrobností o výchozí účet a heslo pro toto předplatné.

7. Uložte si informace a změnit heslo později podle potřeby prostřednictvím Azure přihlašovací stránky portálu.  

    Tyto informace můžete sdílet s klientem, jako je, nebo můžete vytvořit a sdílet samostatný účet, v případě potřeby.

## <a name="access-the-tenant-account"></a>Přístup k účtu klienta

Předplatné klienta můžete přistupovat prostřednictvím řídicím panelu Microsoft Partner Center, jak je popsáno v "krok 1: vytvoření účtu klienta."

1. Přejděte na **zákazníci** stránky a pak klikněte na název účtu klienta.

2. Na **odběry** stránky účtu klienta, můžete sledovat existující odběry účet a přidat další odběry, podle potřeby. Chcete-li spravovat operace zotavení po havárii klienta, vyberte **všechny prostředky (portál Azure)**.

    ![Odkaz všechny prostředky](./media/site-recovery-manage-multi-tenancy-with-csp/all-resources-select.png)  

    Kliknutím na tlačítko **všechny prostředky** uděluje přístup k předplatným Azure klienta. Přístup můžete ověřit kliknutím na odkaz Azure Active Directory v horní pravé části portálu Azure.

    ![Azure Active Directory odkaz](./media/site-recovery-manage-multi-tenancy-with-csp/aad-admin-display.png)

Teď můžete provádět všechny operace obnovení lokality pro klienta prostřednictvím portálu Azure a spravovat operace zotavení po havárii. Přístup k předplatnému klienta prostřednictvím zprostředkovatele kryptografických služeb pro zotavení po havárii spravované, postupujte podle procesu bylo popsáno dříve.

## <a name="deploy-resources-to-the-tenant-subscription"></a>Nasadit prostředky k předplatnému klienta
1. Na portálu Azure vytvořte skupinu prostředků a pak nasadit trezoru služeb zotavení pro obvyklé procesy.

2. Stáhněte registrační klíč trezoru.

3. Zaregistrujte CS pro klienta pomocí registračního klíče trezoru.

4. Zadejte pověření pro dva přístupové účty: účet pro přístup k systému vCenter a virtuální počítač přístup k účtu.

    ![Účty serveru configuration Manager](./media/site-recovery-manage-multi-tenancy-with-csp/config-server-account-display.png)

## <a name="register-site-recovery-infrastructure-to-the-recovery-services-vault"></a>Infrastruktura Site Recovery zaregistrovat do trezoru služeb zotavení
1. Na portálu Azure v úložišti, který jste vytvořili dříve, zaregistrujte server vCenter pro CS, který je zaregistrovaný v "krok 3: nasazení prostředků k předplatnému klienta." Použijte účet přístupu k vCenter pro tento účel.
2. Dokončení procesu "Připravit infrastrukturu" pro obnovení lokality podle obvyklé procesu.
3. Virtuální počítače jsou nyní připraveny k replikaci. Ověřte, že se zobrazují jenom virtuální počítače klienta na **vybrat virtuální počítače** okno pod **replikovat** možnost.

    ![Seznam virtuálních počítačů klienta v okně vyberte virtuální počítače](./media/site-recovery-manage-multi-tenancy-with-csp/tenant-vm-display.png)

## <a name="assign-tenant-access-to-the-subscription"></a>Přiřadit klientovi přístup k předplatnému

Samoobslužné služby zotavení po havárii, zadejte pro klienta účet podrobnosti, jak je uvedeno v kroku 6 "krok 1: vytvoření účtu klienta" oddílu. Tuto akci proveďte, jakmile partnerovi nastaví infrastruktury zotavení po havárii. Zda typ zotavení po havárii je spravované nebo samoobslužné služby, musí partneři přístup klienta odběry prostřednictvím portálu CSP. Jejich nastavení pro zařízení vlastněná partnera trezoru a zaregistrujte infrastrukturu pro odběry klienta.

Partnery můžete také přidat nového uživatele k předplatnému klienta prostřednictvím portálu CSP následujícím způsobem:

1. Přejděte na stránku odběru klienta CSP a potom vyberte **uživatelé a licence** možnost.

    ![Stránku odběru CSP klienta](./media/site-recovery-manage-multi-tenancy-with-csp/users-and-licences.png)

    Nyní můžete vytvořit nového uživatele zadáním příslušné podrobnosti a výběrem oprávnění nebo tím, že nahrajete seznam uživatelů v souboru CSV.

2. Po vytvoření nového uživatele, přejděte zpět na portál Azure a potom na **předplatné** okně vyberte příslušné předplatné.

3. V okně, které se otevře, vyberte **řízení přístupu (IAM)**a potom klikněte na **přidat** přidat uživatele, který má úroveň přístupu relevantní.      
    Uživatelé, které byly vytvořeny prostřednictvím portálu CSP se automaticky zobrazí v okně, které se otevře po kliknutí na tlačítko úroveň přístupu.

    ![Přidání uživatele](./media/site-recovery-manage-multi-tenancy-with-csp/add-user-subscription.png)

    Pro většinu operací správy *Přispěvatel* role je dostačující. Uživatelé s touto úrovní přístupu může dělat všechno na předplatné s výjimkou Změna úrovně přístupu (pro které *vlastníka*– úroveň přístupu se vyžaduje).

  Azure Site Recovery má také tři [předem definované role uživatele](site-recovery-role-based-linked-access-control.md) který lze použít pro další omezení úrovně přístupu podle potřeby.

## <a name="next-steps"></a>Další postup
  [Další informace](site-recovery-role-based-linked-access-control.md) o řízení přístupu na základě rolí pro správu nasazení Azure Site Recovery.

  [Správa prostředí VMware více klientů](site-recovery-multi-tenant-support-vmware-using-csp.md)
