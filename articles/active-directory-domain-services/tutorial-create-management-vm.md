---
title: Kurz – vytvoření virtuálního počítače pro správu pro Azure Active Directory Domain Services | Microsoft Docs
description: V tomto kurzu se naučíte, jak vytvořit a nakonfigurovat virtuální počítač s Windows, který používáte ke správě Azure Active Directory Domain Services instance.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 10/30/2019
ms.author: iainfou
ms.openlocfilehash: 0c997fffc1adc60f774e651ed458d253b35a3bdd
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/26/2020
ms.locfileid: "77612210"
---
# <a name="tutorial-create-a-management-vm-to-configure-and-administer-an-azure-active-directory-domain-services-managed-domain"></a>Kurz: Vytvoření virtuálního počítače pro správu pro konfiguraci a správu spravované domény Azure Active Directory Domain Services

Azure Active Directory Domain Services (služba AD DS) poskytuje spravované doménové služby, jako je připojení k doméně, zásady skupiny, LDAP a ověřování pomocí protokolu Kerberos nebo NTLM, které jsou plně kompatibilní se službou Windows Server Active Directory. Tuto spravovanou doménu spravujete pomocí stejné Nástroje pro vzdálenou správu serveru (RSAT) jako v místní doméně Active Directory Domain Services. Jelikož je Azure služba AD DS spravovaná služba, existují některé úlohy správy, které nemůžete provést, například používání protokolu RDP (Remote Desktop Protocol) pro připojení k řadičům domény.

V tomto kurzu se dozvíte, jak vytvořit virtuální počítač s Windows serverem v Azure a nainstalovat požadované nástroje pro správu spravované domény Azure služba AD DS.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Pochopení dostupných úloh správy ve spravované doméně Azure služba AD DS
> * Instalace nástrojů pro správu služby Active Directory na virtuální počítač s Windows serverem
> * Použití Centrum správy služby Active Directory k provádění běžných úloh

Pokud ještě nemáte předplatné Azure, vytvořte si [účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Předpoklady

K dokončení tohoto kurzu potřebujete následující prostředky a oprávnění:

* Aktivní předplatné Azure.
    * Pokud nemáte předplatné Azure, [vytvořte účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Tenant Azure Active Directory přidružený k vašemu předplatnému, buď synchronizovaný s místním adresářem, nebo jenom s cloudovým adresářem.
    * V případě potřeby [vytvořte tenanta Azure Active Directory][create-azure-ad-tenant] nebo [přidružte předplatné Azure k vašemu účtu][associate-azure-ad-tenant].
* Ve vašem tenantovi Azure AD je povolená a nakonfigurovaná spravovaná doména Azure Active Directory Domain Services.
    * V případě potřeby si přečtěte první kurz a [vytvořte a nakonfigurujte instanci Azure Active Directory Domain Services][create-azure-ad-ds-instance].
* Virtuální počítač s Windows serverem, který je připojený k spravované doméně Azure služba AD DS.
    * V případě potřeby si přečtěte předchozí kurz [a vytvořte virtuální počítač s Windows serverem a připojte ho ke spravované doméně][create-join-windows-vm].
* Uživatelský účet, který je členem skupiny *správců řadičů domény Azure AD* ve vašem TENANTOVI Azure AD.
* Hostitel Azure bastionu nasazený ve vaší virtuální síti Azure služba AD DS.
    * V případě potřeby [Vytvořte hostitele Azure bastionu][azure-bastion].

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

V tomto kurzu vytvoříte a nakonfigurujete virtuální počítač pro správu pomocí Azure Portal. Chcete-li začít, nejprve se přihlaste k [Azure Portal](https://portal.azure.com).

## <a name="available-administrative-tasks-in-azure-ad-ds"></a>Dostupné úlohy správy v Azure služba AD DS

Azure služba AD DS poskytuje spravované domény pro vaše uživatele, aplikace a služby, které se mají využívat. Tento postup mění některé dostupné úlohy správy, které můžete provádět, a jaká oprávnění máte ve spravované doméně. Tyto úlohy a oprávnění se můžou lišit od toho, co se setkáte s běžným místním Active Directory Domain Servicesovým prostředím. Nemůžete se také připojit k řadičům domény v spravované doméně Azure služba AD DS pomocí vzdálené plochy.

### <a name="administrative-tasks-you-can-perform-on-an-azure-ad-ds-managed-domain"></a>Úlohy správy, které můžete provádět ve spravované doméně Azure služba AD DS

Členům skupiny *Správci AAD DC* se udělují oprávnění na spravované doméně Azure služba AD DS, která umožňuje provádět následující úlohy:

* Připojte počítače ke spravované doméně.
* Nakonfigurujte integrovaný objekt zásad skupiny (GPO) pro *AADDC počítače* a kontejnery *AADDC uživatelů* ve spravované doméně.
* Správa DNS ve spravované doméně.
* Vytvořte a spravujte vlastní organizační jednotky (OU) ve spravované doméně.
* Získání přístupu pro správu k počítačům připojeným ke spravované doméně.

### <a name="administrative-privileges-you-dont-have-on-an-azure-ad-ds-managed-domain"></a>Oprávnění správce, která nemáte ve spravované doméně Azure služba AD DS

Spravovaná doména Azure služba AD DS je uzamčená, takže nemáte oprávnění k provádění určitých úloh správy v doméně. Některé z následujících příkladů jsou úlohy, které nemůžete provést:

* Rozšíří schéma spravované domény.
* Připojte se k řadičům domény pro spravovanou doménu pomocí vzdálené plochy.
* Přidejte řadiče domény do spravované domény.
* Nemáte oprávnění *správce domény* nebo *podnikového správce* pro spravovanou doménu.

## <a name="sign-in-to-the-windows-server-vm"></a>Přihlaste se k virtuálnímu počítači s Windows serverem

V předchozím kurzu se vytvořil virtuální počítač s Windows serverem a připojil se k spravované doméně Azure služba AD DS. Pojďme k instalaci nástrojů pro správu použít tento virtuální počítač. V případě potřeby [postupujte podle kroků v tomto kurzu a vytvořte virtuální počítač s Windows serverem a připojte se k spravované doméně][create-join-windows-vm].

> [!NOTE]
> V tomto kurzu použijete virtuální počítač s Windows serverem v Azure, který je připojený k spravované doméně Azure služba AD DS. Můžete také použít klienta Windows, jako je Windows 10, který je připojený ke spravované doméně.
>
> Další informace o tom, jak nainstalovat nástroje pro správu na klienta Windows, najdete v tématu [install nástroje pro vzdálenou správu serveru (RSAT)](https://social.technet.microsoft.com/wiki/contents/articles/2202.remote-server-administration-tools-rsat-for-windows-client-and-windows-server-dsforum2wiki.aspx) .

Začněte tím, že se připojíte k virtuálnímu počítači s Windows serverem následujícím způsobem:

1. V Azure Portal na levé straně vyberte **skupiny prostředků** . Zvolte skupinu prostředků, ve které se vytvořil váš virtuální počítač, třeba *myResourceGroup*, a potom vyberte virtuální počítač, jako je třeba *myVM*.
1. V podokně **Přehled** pro váš virtuální počítač vyberte **připojit**a pak **bastionu**.

    ![Připojení k virtuálnímu počítači s Windows pomocí bastionu v Azure Portal](./media/join-windows-vm/connect-to-vm.png)

1. Zadejte přihlašovací údaje pro váš virtuální počítač a pak vyberte **připojit**.

   ![Připojení prostřednictvím hostitele bastionu v Azure Portal](./media/join-windows-vm/connect-to-bastion.png)

V případě potřeby umožněte webovému prohlížeči otevřít automaticky otevíraná okna pro zobrazení připojení bastionu. Vytvoření připojení k VIRTUÁLNÍmu počítači trvá několik sekund.

## <a name="install-active-directory-administrative-tools"></a>Nainstalovat nástroje pro správu služby Active Directory

Spravované domény Azure služba AD DS se spravují pomocí stejných nástrojů pro správu, jako jsou místní služba AD DS prostředí, jako je Centrum správy služby Active Directory (ADAC) nebo AD PowerShell. Tyto nástroje lze nainstalovat jako součást funkce Nástroje pro vzdálenou správu serveru (RSAT) na serveru a v klientských počítačích se systémem Windows Server. Členové skupiny *Správci AAD DC* můžou spravovat spravované domény Azure služba AD DS vzdáleně pomocí těchto nástrojů pro správu služby AD z počítače, který je připojený ke spravované doméně.

Pokud chcete nainstalovat nástroje pro správu služby Active Directory do virtuálního počítače připojeného k doméně, proveďte následující kroky:

1. Pokud se při přihlášení k virtuálnímu počítači ve výchozím nastavení **Správce serveru** neotevře, vyberte nabídku **Start** a pak zvolte **Správce serveru**.
1. V podokně *řídicí panel* v okně **Správce serveru** vyberte **Přidat role a funkce**.
1. Na stránce **než začnete** v *Průvodci přidáním rolí a funkcí*vyberte **Další**.
1. Pro *typ instalace*ponechte zaškrtnutou možnost instalace na základě **rolí nebo na základě funkcí** a vyberte **Další**.
1. Na stránce **Výběr serveru** zvolte aktuální virtuální počítač z fondu serverů, například *myvm.aaddscontoso.com*, a pak vyberte **Další**.
1. Na stránce **role serveru** klikněte na **Další**.
1. Na stránce **funkce** rozbalte uzel **Nástroje pro vzdálenou správu serveru** a potom rozbalte uzel **Nástroje pro správu rolí** .

    V seznamu nástrojů pro správu rolí zvolte **Služba AD DS a funkce nástrojů služby AD LDS** a pak vyberte **Další**.

    ![Instalace nástrojů služba AD DS a AD LDS na stránce funkce](./media/tutorial-create-management-vm/install-features.png)

1. Na stránce **potvrzení** vyberte **nainstalovat**. Instalace nástrojů pro správu může trvat minutu nebo dvě.
1. Po dokončení instalace funkce vyberte **Zavřít** a ukončete průvodce **přidáním rolí a funkcí** .

## <a name="use-active-directory-administrative-tools"></a>Použití nástrojů pro správu služby Active Directory

S nainstalovanými nástroji pro správu se podívejme, jak je použít ke správě spravované domény Azure služba AD DS. Ujistěte se, že jste přihlášeni k virtuálnímu počítači pomocí uživatelského účtu, který je členem skupiny *AAD DC Administrators* .

1. V nabídce **Start** vyberte **Nástroje pro správu systému Windows**. Zobrazí se nástroje pro správu služby AD nainstalované v předchozím kroku.

    ![Seznam nástrojů pro správu nainstalovaných na serveru](./media/tutorial-create-management-vm/list-admin-tools.png)

1. Vyberte **Centrum správy služby Active Directory**.
1. Pokud chcete prozkoumat spravovanou doménu Azure služba AD DS, v levém podokně vyberte název domény, například *aaddscontoso.com*. Dva kontejnery s názvem *počítače AADDC* a *Uživatelé AADDC* jsou v horní části seznamu.

    ![Výpis dostupných kontejnerů část spravované domény Azure služba AD DS](./media/tutorial-create-management-vm/active-directory-administrative-center.png)

1. Pokud chcete zobrazit uživatele a skupiny patřící do spravované domény Azure služba AD DS, vyberte kontejner **Uživatelé AADDC** . Uživatelské účty a skupiny z vašeho tenanta Azure AD jsou uvedené v tomto kontejneru.

    V následujícím příkladu výstupu se v tomto kontejneru zobrazí uživatelský účet s názvem *Contoso admin* a skupina pro *správce řadiče domény AAD* .

    ![Zobrazení seznamu uživatelů domény Azure služba AD DS v Centrum správy služby Active Directory](./media/tutorial-create-management-vm/list-azure-ad-users.png)

1. Pokud chcete zobrazit počítače, které jsou připojené k spravované doméně Azure služba AD DS, vyberte kontejner **počítače AADDC** . V seznamu se zobrazí položka pro aktuální virtuální počítač, například *myVM*. Účty počítačů pro všechny počítače, které jsou připojené ke spravované doméně Azure služba AD DS, se ukládají do kontejneru *AADDC Computers* .

K dispozici jsou běžné Centrum správy služby Active Directory akce, jako je resetování hesla uživatelského účtu nebo Správa členství ve skupinách. Tyto akce fungují jenom pro uživatele a skupiny vytvořené přímo ve spravované doméně Azure služba AD DS. Informace o identitě se synchronizují jenom *z* Azure AD do Azure služba AD DS. Nebudete moct zpátky zapisovat z Azure služba AD DS do Azure AD. Nemůžete měnit hesla ani členství spravované skupiny pro uživatele synchronizované z Azure AD a tyto změny se synchronizují zpátky.

*Modul služby Active Directory pro prostředí Windows PowerShell*, který je nainstalovaný jako součást nástrojů pro správu, můžete použít také ke správě běžných akcí ve spravované doméně Azure služba AD DS.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Pochopení dostupných úloh správy ve spravované doméně Azure služba AD DS
> * Instalace nástrojů pro správu služby Active Directory na virtuální počítač s Windows serverem
> * Použití Centrum správy služby Active Directory k provádění běžných úloh

Pokud chcete bezpečně pracovat se svojí spravovanou doménou Azure služba AD DS, povolte zabezpečený protokol LDAPs (Lightweight Directory Access Protocol).

> [!div class="nextstepaction"]
> [Konfigurace zabezpečeného protokolu LDAP pro spravovanou doménu](tutorial-configure-ldaps.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[azure-bastion]: ../bastion/bastion-create-host-portal.md
