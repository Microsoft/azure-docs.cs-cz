---
title: Vytvoření organizační jednotky (OU) ve službě Azure AD Domain Services | Dokumenty společnosti Microsoft
description: Zjistěte, jak vytvořit a spravovat vlastní organizační jednotku (OU) ve spravované doméně služby Azure AD Domain Services.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 52602ad8-2b93-4082-8487-427bdcfa8126
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 03/31/2020
ms.author: iainfou
ms.openlocfilehash: 61f951c0dd6561fc8d5a5de6b80e3759fd42eb78
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655546"
---
# <a name="create-an-organizational-unit-ou-in-an-azure-ad-domain-services-managed-domain"></a>Vytvoření organizační jednotky (OU) ve spravované doméně služby Azure AD Domain Services

Organizační jednotky (OU) ve službě AD Domain Services (AD DS) umožňují logicky seskupit objekty, jako jsou uživatelské účty, účty služeb nebo účty počítačů. Potom můžete přiřadit správce ke konkrétním runám a použít zásady skupiny k vynucení cíleného nastavení konfigurace.

Spravované domény Azure AD DS zahrnují následující dvě integrované hlavní sady:

* *AADDC Computers* - obsahuje počítačové objekty pro všechny počítače, které jsou připojeny ke spravované doméně.
* *Uživatelé AADDC* – zahrnuje uživatele a skupiny synchronizované z klienta Azure AD.

Při vytváření a spouštění úloh, které používají Azure AD DS, budete muset vytvořit účty služeb pro aplikace k ověření sami. Chcete-li uspořádat tyto účty služeb, často vytvořit vlastní ou ve spravované doméně Azure AD DS a potom vytvořit účty služeb v rámci této ou.

V hybridním prostředí nejsou vynaly-prodejny vytvořené v místním prostředí služby AD DS synchronizovány s Azure AD DS. Spravované domény Azure AD DS používají strukturu ploché ou. Všechny uživatelské účty a skupiny jsou uloženy v kontejneru *AADDC Users,* přestože jsou synchronizovány z různých místních domén nebo doménových struktur, i když jste tam nakonfigurovali hierarchickou strukturu ou.

Tento článek ukazuje, jak vytvořit ou ve spravované doméně Azure AD DS.

## <a name="before-you-begin"></a>Než začnete

Chcete-li tento článek dokončit, potřebujete následující zdroje a oprávnění:

* Aktivní předplatné Azure.
    * Pokud nemáte předplatné Azure, [vytvořte si účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Tenant Azure Active Directory přidružený k vašemu předplatnému, synchronizovaný s místním adresářem nebo s adresářem pouze pro cloud.
    * V případě potřeby [vytvořte klienta Azure Active Directory][create-azure-ad-tenant] nebo [přidružte předplatné Azure ke svému účtu][associate-azure-ad-tenant].
* Spravovaná doména Služby Azure Active Directory Domain Services povolená a nakonfigurovaná ve vašem tenantovi Azure AD.
    * V případě potřeby proveďte kurz [a vytvořte a nakonfigurujte instanci služby Azure Active Directory Domain Services][create-azure-ad-ds-instance].
* Virtuální počítač pro správu Windows Serveru, který je spojený se spravovanou doménou Azure AD DS.
    * V případě potřeby dokončete kurz [a vytvořte virtuální virtuální virtuální montovny pro správu][tutorial-create-management-vm].
* Uživatelský účet, který je členem *skupiny správců Azure AD DC* ve vašem tenantovi Azure AD.

## <a name="custom-ou-considerations-and-limitations"></a>Aspekty a omezení vlastní oudiny

Když vytvoříte vlastní hlavní sady ve spravované doméně Azure AD DS, získáte další flexibilitu správy pro správu uživatelů a použití zásad skupiny. Ve srovnání s místním prostředím služby AD DS existují určitá omezení a důležité informace při vytváření a správě vlastní struktury ou ve službě Azure AD DS:

* Chcete-li vytvořit vlastní vou, musí být uživatelé členem *skupiny Správci řadiče domény aad.*
* Uživateli, který vytvoří vlastní individuální oud, jsou udělena oprávnění správce (úplná kontrola) nad tuto ouovou položkou a je vlastníkem prostředku.
    * Ve výchozím nastavení má skupina *AAD DC Administrators* také plnou kontrolu nad vlastní ou.
* Je vytvořena výchozí hlavní výpaka pro *uživatele AADDC,* která obsahuje všechny synchronizované uživatelské účty z vašeho klienta Azure AD.
    * Uživatele nebo skupiny nelze přesunout z ouobjektu *Uživatelů AADDC* do vlastních výhonek, které vytvoříte. Do vlastních využívaných operačních ent lze přesunout jenom uživatelské účty nebo prostředky vytvořené ve spravované doméně Služby Azure AD DS.
* Uživatelské účty, skupiny, účty služeb a objekty počítačů, které vytvoříte pod vlastními provozními sadami, nejsou ve vašem tenantovi Azure AD k dispozici.
    * Tyto objekty se nezobrazují pomocí rozhraní Microsoft Graph API nebo v unovém rozhraní Azure AD; jsou dostupné jenom ve spravované doméně Azure AD DS.

## <a name="create-a-custom-ou"></a>Vytvoření vlastní individuální oužiny

Chcete-li vytvořit vlastní individuální oovou velikou, použijte nástroje pro správu služby Active Directory z virtuálního serveru přilehlého na doménu. Centrum správy služby Active Directory umožňuje zobrazit, upravit a vytvořit prostředky ve spravované doméně Azure AD DS, včetně vou.

> [!NOTE]
> Chcete-li vytvořit vlastní ou ve spravované doméně Azure AD DS, musíte být přihlášeni k uživatelskému účtu, který je členem *skupiny Správci řadiče domény Řadiče domény AAD.*

1. Přihlaste se k virtuálnímu počítači pro správu. Postup připojení pomocí portálu Azure najdete v [tématu Připojení k virtuálnímu počítači s Windows Server][connect-windows-server-vm].
1. Na úvodní obrazovce vyberte **Nástroje pro správu**. Seznam dostupných nástrojů pro správu je zobrazen, které byly nainstalovány v kurzu [k vytvoření virtuálního virtuálního virtuálního uživatele pro správu][tutorial-create-management-vm].
1. Chcete-li vytvořit a spravovat hlavní sady, vyberte ze seznamu nástrojů pro správu **Centrum správy služby Active Directory.**
1. V levém podokně zvolte spravovanou doménu Azure AD DS, například *aaddscontoso.com*. Zobrazí se seznam existujících vou s a prostředků:

    ![Vyberte spravovanou doménu Služby Azure AD DS v Centru správy služby Active Directory.](./media/create-ou/create-ou-adac-overview.png)

1. Podokno **Úkoly** se zobrazí na pravé straně Centra správy služby Active Directory. V doméně, například *aaddscontoso.com*, vyberte **Nový > organizační jednotka**.

    ![Vyberte možnost vytvoření nové hlavní výužky v Centru správy služby Active Directory.](./media/create-ou/create-ou-adac-new-ou.png)

1. V dialogovém okně **Vytvořit organizační jednotku** zadejte **název** nové organizační jednotky, například *MyCustomOu*. Zadejte krátký popis hlavní výužky, například *Vlastní ou pro účty služeb*. V případě potřeby můžete také nastavit pole **Spravovat** podle pro ou. Chcete-li vytvořit vlastní ou, vyberte **ok**.

    ![Vytvoření vlastní individuální oužiny z Centra správy služby Active Directory](./media/create-ou/create-ou-dialog.png)

1. V Centru správy služby Active Directory je nyní uvedena vlastní hlavní výužka, která je k dispozici pro použití:

    ![Vlastní individuální oužina dostupná pro použití v Centru správy služby Active Directory](./media/create-ou/create-ou-done.png)

## <a name="next-steps"></a>Další kroky

Další informace o používání nástrojů pro správu nebo vytváření a používání účtů služeb naleznete v následujících článcích:

* [Centrum správy služby Active Directory: Začínáme](https://technet.microsoft.com/library/dd560651.aspx)
* [Podrobný průvodce účty služby](https://technet.microsoft.com/library/dd548356.aspx)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[connect-windows-server-vm]: join-windows-vm.md#connect-to-the-windows-server-vm
