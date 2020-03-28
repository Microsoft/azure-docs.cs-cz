---
title: Kurz – vytvoření virtuálního počítače pro správu služby Azure Active Directory Domain Services | Dokumenty společnosti Microsoft
description: V tomto kurzu se dozvíte, jak vytvořit a nakonfigurovat virtuální počítač s Windows, který používáte ke správě instance služby Azure Active Directory Domain Services.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 10/30/2019
ms.author: iainfou
ms.openlocfilehash: 63c5f068adab58c901acf5fd26261d57e1183f0d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "79481513"
---
# <a name="tutorial-create-a-management-vm-to-configure-and-administer-an-azure-active-directory-domain-services-managed-domain"></a>Kurz: Vytvoření virtuálního počítače pro správu pro konfiguraci a správu spravované domény služby Azure Active Directory Domain Services

Služba Azure Active Directory Domain Services (AD DS) poskytuje služby spravované domény, jako je připojení k doméně, zásady skupiny, protokol LDAP a ověřování protokolem Kerberos/NTLM, které jsou plně kompatibilní se službou Active Directory systému Windows Server. Tuto spravovanou doménu spravujete pomocí stejných nástrojů pro vzdálenou správu serveru (RSAT) jako v místní doméně služby Active Directory Domain Services. Vzhledem k tomu, že Azure AD DS je spravovaná služba, existují některé úlohy správy, které nelze provádět, například pomocí protokolu vzdálené plochy (RDP) pro připojení k řadičům domény.

Tento kurz ukazuje, jak vytvořit virtuální počítač se systémem Windows Server v Azure a nainstalovat potřebné nástroje pro správu spravované domény Azure AD DS.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Principy dostupných úloh správy ve spravované doméně Azure AD DS
> * Instalace nástrojů pro správu služby Active Directory na virtuální počítač se systémem Windows Server
> * Použití Centra správy služby Active Directory k provádění běžných úkolů

Pokud nemáte předplatné Azure, [vytvořte si účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu potřebujete následující prostředky a oprávnění:

* Aktivní předplatné Azure.
    * Pokud nemáte předplatné Azure, [vytvořte si účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Tenant Azure Active Directory přidružený k vašemu předplatnému, synchronizovaný s místním adresářem nebo s adresářem pouze pro cloud.
    * V případě potřeby [vytvořte klienta Azure Active Directory][create-azure-ad-tenant] nebo [přidružte předplatné Azure ke svému účtu][associate-azure-ad-tenant].
* Spravovaná doména Služby Azure Active Directory Domain Services povolená a nakonfigurovaná ve vašem tenantovi Azure AD.
    * V případě potřeby se podívejte na první kurz [pro vytvoření a konfiguraci instance služby Azure Active Directory Domain Services][create-azure-ad-ds-instance].
* Virtuální počítač se systémem Windows Server, který je spojený se spravovanou doménou Azure AD DS.
    * V případě potřeby se v předchozím kurzu můžete připojit k [virtuálnímu virtuálnímu ms systému Windows Server a připojit ho ke spravované doméně][create-join-windows-vm].
* Uživatelský účet, který je členem *skupiny správců Azure AD DC* ve vašem tenantovi Azure AD.
* Hostitel Azure Bastion nasazený ve vaší virtuální síti Azure AD DS.
    * V případě potřeby [vytvořte hostitele Azure Bastion][azure-bastion].

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

V tomto kurzu vytvoříte a nakonfigurujete virtuální počítač pro správu pomocí portálu Azure. Chcete-li začít, nejprve se přihlaste na [portál Azure](https://portal.azure.com).

## <a name="available-administrative-tasks-in-azure-ad-ds"></a>Dostupné úlohy správy ve službě Azure AD DS

Azure AD DS poskytuje spravovanou doménu pro vaše uživatele, aplikace a služby využívat. Tento přístup změní některé dostupné úlohy správy, které můžete provést, a jaká oprávnění máte v rámci spravované domény. Tyto úkoly a oprávnění se mohou lišit od běžných místních prostředí služby Active Directory Domain Services. Také se nemůžete připojit k řadičům domény ve spravované doméně Azure AD DS pomocí vzdálené plochy.

### <a name="administrative-tasks-you-can-perform-on-an-azure-ad-ds-managed-domain"></a>Úlohy správy, které můžete provádět ve spravované doméně Azure AD DS

Členům *skupiny AAD DC Administrators* jsou udělena oprávnění ve spravované doméně Azure AD DS, která jim umožňují dělat úkoly, jako jsou:

* Nakonfigurujte předdefinovaný objekt zásad skupiny (GPO) pro *kontejnery AADDC Computers* a *AADDC Users* ve spravované doméně.
* Správa DNS ve spravované doméně.
* Vytvořte a spravujte vlastní organizační jednotky (OU) ve spravované doméně.
* Získání přístupu pro správu k počítačům připojeným ke spravované doméně.

### <a name="administrative-privileges-you-dont-have-on-an-azure-ad-ds-managed-domain"></a>Oprávnění správce, která nemáte ve spravované doméně Azure AD DS

Spravovaná doména Azure AD DS je uzamčená, takže nemáte oprávnění k určitým úlohám správy v doméně. Některé z následujících příkladů jsou úkoly, které nelze provést:

* Rozšiřte schéma spravované domény.
* Připojte se k řadičům domény pro spravovanou doménu pomocí vzdálené plochy.
* Přidejte řadiče domény do spravované domény.
* Nemáte oprávnění *správce domény* nebo správce *rozlehlé sítě* pro spravovanou doménu.

## <a name="sign-in-to-the-windows-server-vm"></a>Přihlášení k virtuálnímu virtuálnímu ms systému Windows Server

V předchozím kurzu byl vytvořen virtuální počítač se systémem Windows Server a připojil se ke spravované doméně Azure AD DS. Použijeme tento virtuální virtuální ms k instalaci nástrojů pro správu. V případě potřeby [postupujte podle pokynů v kurzu a vytvořte a připojte virtuální hod windows serveru ke spravované doméně][create-join-windows-vm].

> [!NOTE]
> V tomto kurzu použijete virtuální počítač se systémem Windows Server v Azure, který je spojený se spravovanou doménou Azure AD DS. Můžete také použít klienta systému Windows, například Windows 10, který je připojen ke spravované doméně.
>
> Další informace o instalaci nástrojů pro správu do klienta systému Windows naleznete v [tématu instalace nástrojů pro vzdálenou správu serveru (RSAT)](https://social.technet.microsoft.com/wiki/contents/articles/2202.remote-server-administration-tools-rsat-for-windows-client-and-windows-server-dsforum2wiki.aspx)

Chcete-li začít, připojte se k virtuálnímu virtuálnímu zařízení se systémem Windows Server následujícím způsobem:

1. Na portálu Azure vyberte **skupiny prostředků** na levé straně. Vyberte skupinu prostředků, kde byl vytvořen virtuální počítač, například *myResourceGroup*, a vyberte virtuální počítač, například *myVM*.
1. V podokně **Přehled** pro virtuální počítač vyberte **Připojit**a pak **Bašta**.

    ![Připojení k virtuálnímu počítači s Windows pomocí bastionu na webu Azure Portal](./media/join-windows-vm/connect-to-vm.png)

    Můžete také [vytvořit a použít hostitele Azure Bastion (aktuálně ve verzi)][azure-bastion] povolit přístup jenom prostřednictvím portálu Azure přes TLS.
1. Zadejte přihlašovací údaje pro virtuální počítač a pak vyberte **Připojit**.

   ![Připojení prostřednictvím hostitele Bastion na webu Azure Portal](./media/join-windows-vm/connect-to-bastion.png)

V případě potřeby povolte webovému prohlížeči otevřít automaticky otevíraná okna pro zobrazení připojení Bastion. Připojení k virtuálnímu počítači trvá několik sekund.

## <a name="install-active-directory-administrative-tools"></a>Instalace nástrojů pro správu služby Active Directory

Spravované domény Azure AD DS se spravují pomocí stejných nástrojů pro správu jako místní prostředí služby AD DS, jako je například Centrum správy služby Active Directory (ADAC) nebo prostředí AD PowerShell. Tyto nástroje lze nainstalovat jako součást funkce Nástroje pro vzdálenou správu serveru (RSAT) v počítačích se systémem Windows Server a klientských počítačích. Členové *skupiny Správci řadiče domény Řadičdomény AAD* pak mohou spravovat spravované domény Služby Azure AD DS vzdáleně pomocí těchto nástrojů pro správu služby AD z počítače, který je připojen ke spravované doméně.

Chcete-li nainstalovat nástroje pro správu služby Active Directory na virtuální počítač přilehlý k doméně, proveďte následující kroky:

1. Pokud **se Správce serveru** při přihlášení k virtuálnímu virtuálnímu virtuálnímu provozu ve výchozím nastavení neotevře, vyberte nabídku **Start** a pak zvolte Správce **serveru**.
1. V podokně *Řídicí panel* v okně **Správce serveru** vyberte Přidat role **a funkce**.
1. Na stránce **Před zahájením** *Průvodce*přidáním rolí a funkcí vyberte další položku **.**
1. U *typu instalace*ponechte zaškrtnutou možnost **instalace na základě rolí nebo funkcí** a vyberte **další**.
1. Na stránce **Výběr serveru** vyberte aktuální virtuální počítač z fondu serverů, například *myvm.aaddscontoso.com*, a pak vyberte **Další**.
1. Na stránce **Role serveru** klepněte na tlačítko **Další**.
1. Na stránce **Funkce** rozbalte uzel **Nástroje pro vzdálenou správu serveru** a potom rozbalte uzel Nástroje pro **správu rolí.**

    V seznamu nástrojů pro správu rolí zvolte funkce **Nástroje služby AD DS a Nástroje služby AD DS a služby AD LDS** a pak vyberte **Další**.

    ![Instalace nástrojů služby AD DS a ad LDS na stránce Funkce](./media/tutorial-create-management-vm/install-features.png)

1. Na stránce **Potvrzení** vyberte **Instalovat**. Instalace nástrojů pro správu může trvat minutu nebo dvě.
1. Po dokončení instalace prvku vyberte **Zavřít** a ukončete Průvodce **přidáním rolí a funkcí.**

## <a name="use-active-directory-administrative-tools"></a>Použití nástrojů pro správu služby Active Directory

S nainstalovanými nástroji pro správu se podívejme, jak je použít ke správě spravované domény Azure AD DS. Ujistěte se, že jste přihlášeni k virtuálnímu účtu pomocí uživatelského účtu, který je členem *skupiny Správci řadiče domény AAD.*

1. V nabídce **Start** vyberte **Nástroje pro správu systému Windows**. Jsou uvedeny nástroje pro správu služby AD nainstalované v předchozím kroku.

    ![Seznam nástrojů pro správu nainstalovaných na serveru](./media/tutorial-create-management-vm/list-admin-tools.png)

1. Vyberte **Centrum správy služby Active Directory**.
1. Chcete-li prozkoumat spravovanou doménu Azure AD DS, zvolte název domény v levém podokně, například *aaddscontoso.com*. Dva kontejnery s názvem *AADDC Počítače* a *AADDC Uživatelé* jsou v horní části seznamu.

    ![Seznam dostupných kontejnerů část spravované domény Azure AD DS](./media/tutorial-create-management-vm/active-directory-administrative-center.png)

1. Chcete-li zobrazit uživatele a skupiny, které patří do spravované domény Azure AD DS, vyberte kontejner **AADDC Users.** Uživatelské účty a skupiny z vašeho klienta Azure AD jsou uvedeny v tomto kontejneru.

    V následujícím příkladu výstupu uživatelský účet s názvem *Contoso Admin* a skupina pro *správce řadiče domény AAD* jsou zobrazeny v tomto kontejneru.

    ![Zobrazení seznamu uživatelů domény služby Azure AD DS v Centru správy služby Active Directory](./media/tutorial-create-management-vm/list-azure-ad-users.png)

1. Chcete-li zobrazit počítače, které jsou připojeny ke spravované doméně Azure AD DS, vyberte kontejner **Počítače AADDC.** Položka pro aktuální virtuální počítač, jako je *například myVM*, je uveden. Účty počítačů pro všechny počítače, které jsou připojeny ke spravované doméně Azure AD DS, jsou uloženy v tomto kontejneru *Počítače AADDC.*

K dispozici jsou běžné akce Centra správy služby Active Directory, jako je obnovení hesla uživatelského účtu nebo správa členství ve skupině. Tyto akce fungují jenom pro uživatele a skupiny vytvořené přímo ve spravované doméně Azure AD DS. Informace o identitě se synchronizují *jenom ze* služby Azure AD do služby Azure AD DS. Neexistuje žádný zpětný zápis z Azure AD DS do Azure AD. Nemůžete změnit hesla nebo členství ve spravovaných skupinách pro uživatele synchronizované ze služby Azure AD a tyto změny synchronizovat zpět.

Pomocí *modulu Active Directory pro prostředí Windows PowerShell*nainstalovaného jako součást nástrojů pro správu můžete také spravovat běžné akce ve spravované doméně Azure AD DS.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Principy dostupných úloh správy ve spravované doméně Azure AD DS
> * Instalace nástrojů pro správu služby Active Directory na virtuální počítač se systémem Windows Server
> * Použití Centra správy služby Active Directory k provádění běžných úkolů

Chcete-li bezpečně komunikovat se spravovanou doménou Azure AD DS, povolte protokol LDAPS (SECURE LIGHTWEIGHT DIRECTORY ACCESS PROTOCOL).

> [!div class="nextstepaction"]
> [Konfigurace zabezpečeného protokolu LDAP pro spravovanou doménu](tutorial-configure-ldaps.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[azure-bastion]: ../bastion/bastion-create-host-portal.md
