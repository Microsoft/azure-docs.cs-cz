---
title: "Nastavení replikace VMware do Azure v prostředí více klientů pomocí Site Recovery a program Cloud Solution Provider (CSP) | Microsoft Docs"
description: "Popisuje, jak vytvářet a spravovat odběry klienta prostřednictvím zprostředkovatele kryptografických služeb a nasazení Azure Site Recovery v instalačním programu více klientů"
services: site-recovery
author: mayanknayar
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: manayar
ms.openlocfilehash: 25591acb3f046744400f5dcf20a7ea651a7bcf54
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2018
---
# <a name="set-up-vmware-replication-in-a-multi-tenancy-environment-with-the-cloud-solution-provider-csp-program"></a>Nastavení replikace VMware v prostředí s více klientů s programem Cloud Solution Provider (CSP)

[CSP program](https://partner.microsoft.com/en-US/cloud-solution-provider) podporovalo lepší společně scénářů pro cloudové služby Microsoftu, včetně služeb Office 365, Enterprise Mobility Suite a Microsoft Azure. S CSP partnery vlastní začátku do konce relace se zákazníky a stát kontaktní bod primární relace. Partnery můžete nasadit předplatná Azure pro zákazníky a kombinovat předplatných s vlastní s přidanou hodnotou, přizpůsobené nabídky.

S [Azure Site Recovery](site-recovery-overview.md), jako partnerské servery můžete spravovat zotavení po havárii pro zákazníky přímo přes zprostředkovatele kryptografických služeb. Alternativně můžete nastavit prostředí Site Recovery pomocí zprostředkovatele kryptografických služeb a Informujte zákazníky, spravovat svá vlastní potřebovat obnovení po havárii způsobem samoobslužné služby. V obou případech partneři jsou spolupráci mezi Site Recovery a zákazníků. Partneři služby vztah se zákazníkem a vyúčtování pro zákazníky za použití Site Recovery.

Tento článek popisuje, jak jako partner můžete vytvořit a spravovat odběry klienta prostřednictvím poskytovatele CSP, scénář víceklientské VMware replikace.

## <a name="prerequisites"></a>Požadavky

Pro nastavení replikace VMware musíte udělat následující:

- [Příprava](tutorial-prepare-azure.md) prostředky Azure, včetně předplatné Azure, virtuální sítě Azure a účet úložiště.
- [Příprava](vmware-azure-tutorial-prepare-on-premises.md) místní servery VMware a virtuálních počítačů. 
- Pro každého klienta vytvořte samostatné management server, který může komunikovat s klientské virtuální počítače a vaše servery vCenter server. Měli byste jako partner pouze mít přístupová práva k tomuto serveru pro správu. Další informace o [víceklientské prostředí](vmware-azure-multi-tenant-overview.md).

## <a name="create-a-tenant-account"></a>Vytvořit účet klienta

1. Prostřednictvím [Microsoft Partner Center](https://partnercenter.microsoft.com/), přihlaste se ke svému účtu CSP.
2. Na **řídicí panel** nabídce vyberte možnost **zákazníci**.
3. Na stránce, které se otevře, klikněte na **přidat zákazníka** tlačítko.
4. V **nového zákazníka** stránky, zadejte podrobnosti informace o účtu pro klienta. 

    ![Stránka informace o účtu](./media/vmware-azure-multi-tenant-csp-disaster-recovery/customer-add-filled.png)

5. Pak klikněte na tlačítko **Další: odběry**.
6. Na stránce Výběr předplatných vyberte **Microsoft Azure** zaškrtávací políčko. Můžete přidat další odběry nyní nebo kdykoli později.
7. Na **zkontrolujte** Potvrďte podrobnosti klienta a pak klikněte na tlačítko **odeslání**.
8. Po vytvoření účtu klienta, se zobrazí potvrzovací stránku, zobrazení podrobností o výchozí účet a heslo pro toto předplatné. Uložte si informace a změnit heslo později podle potřeby prostřednictvím Azure přihlašovací stránky portálu.

Tyto informace můžete sdílet s klientem, jako je, nebo můžete vytvořit a sdílet samostatný účet, v případě potřeby.

## <a name="access-the-tenant-account"></a>Přístup k účtu klienta

Předplatné klienta můžete přistupovat prostřednictvím řídicím panelu Microsoft Partner Center.

1. Na **zákazníci** klikněte na název účtu klienta.
2. V **odběry** stránky účtu klienta, můžete sledovat existující odběry účet a přidat další odběry, podle potřeby.
3. Chcete-li spravovat operace zotavení po havárii klienta, vyberte **všechny prostředky (portál Azure)**. To vám uděluje že přístup k předplatným služby Azure klienta.

    ![Odkaz všechny prostředky](./media/vmware-azure-multi-tenant-csp-disaster-recovery/all-resources-select.png)  

4. Přístup můžete ověřit kliknutím na odkaz Azure Active Directory v horní pravé části portálu Azure.

    ![Azure Active Directory odkaz](./media/vmware-azure-multi-tenant-csp-disaster-recovery/aad-admin-display.png)

Teď můžete provádět a správě všech operací obnovení lokality pro klienta na portálu Azure. Přístup k předplatnému klienta prostřednictvím zprostředkovatele kryptografických služeb pro zotavení po havárii spravované, postupujte podle procesu bylo popsáno dříve.

## <a name="deploy-resources-to-the-tenant-subscription"></a>Nasadit prostředky k předplatnému klienta

1. Na portálu Azure vytvořte skupinu prostředků a pak nasadit trezoru služeb zotavení pro obvyklé procesy.
2. Stáhněte registrační klíč trezoru.
3. Zaregistrujte CS pro klienta pomocí registračního klíče trezoru.

4. Zadejte pověření pro dva přístupové účty, účet pro přístup k serveru vCenter a účet pro přístup k virtuálnímu počítači.

    ![Účty serveru configuration Manager](./media/vmware-azure-multi-tenant-csp-disaster-recovery/config-server-account-display.png)

## <a name="register-servers-in-the-vault"></a>Zaregistrujte server v trezoru

1. Na portálu Azure v úložišti, který jste vytvořili dříve zaregistrujte server vCenter pro konfigurační server, pomocí účtu vCenter, který jste vytvořili. 
2. Dokončení procesu "Připravit infrastrukturu" pro obnovení lokality podle obvyklé procesu.
3. Virtuální počítače jsou nyní připraveny k replikaci. Ověřte, že se zobrazují jenom virtuální počítače klienta v **replikovat** > **vybrat virtuální počítače**.


## <a name="assign-tenant-access-to-the-subscription"></a>Přiřadit klientovi přístup k předplatnému

1. Ujistěte se, že je nastavení infrastruktury pro zotavení po havárii. Všimněte si klienta odběry musí přistupovat prostřednictvím portálu CSP, regarless, jestli je spravována zotavení po havárii nebo samoobslužná služba. Musíte nastavit svůj trezor a zaregistrovat infrastrukturu pro odběry klienta.
2. Zadejte klientovi s [jste vytvořili účet](#create-a-tenant-account)
3. Nového uživatele můžete přidat k předplatnému klienta prostřednictvím portálu CSP následujícím způsobem:

    (a) přejděte na stránku odběru CSP klienta a potom vyberte **uživatelé a licence** možnost.

        ![The tenant's CSP subscription page](./media/vmware-azure-multi-tenant-csp-disaster-recovery/users-and-licences.png)

    b) zadáním příslušné podrobnosti a výběrem oprávnění nebo tím, že nahrajete seznam uživatelů v souboru CSV nyní vytvořte nového uživatele.
    c) po vytvoření nového uživatele, přejděte zpět na portál Azure. V **předplatné** vyberte příslušné předplatné.
    d) vyberte **řízení přístupu (IAM)**a potom klikněte na **přidat**, chcete-li přidat uživatele, který má úroveň přístupu relevantní. Uživatelé, které byly vytvořeny prostřednictvím portálu CSP, automaticky se zobrazí na stránce, které se otevře po kliknutí na tlačítko úroveň přístupu.

        ![Add a user](./media/vmware-azure-multi-tenant-csp-disaster-recovery/add-user-subscription.png)

- Pro většinu operací správy *Přispěvatel* role je dostačující. Uživatelé s touto úrovní přístupu může dělat všechno na předplatné s výjimkou Změna úrovně přístupu (pro které *vlastníka*– úroveň přístupu se vyžaduje).
- Site Recovery má také tři [předdefinované role uživatele](site-recovery-role-based-linked-access-control.md), který lze použít pro další omezení úrovně přístupu podle potřeby.

## <a name="next-steps"></a>Další postup
- [Další informace](site-recovery-role-based-linked-access-control.md) o řízení přístupu na základě rolí pro správu nasazení Azure Site Recovery.
- [Další informace](vmware-azure-architecture.md) o VMware do architektury Azure replikace.
- [Přečtěte si kurz](vmware-azure-tutorial.md) pro replikaci virtuálních počítačů VMware do Azure.
