---
title: Vytváření a používání zásad hesel ve službě Azure AD Domain Services | Dokumenty společnosti Microsoft
description: Zjistěte, jak a proč používat jemně odstupňované zásady hesel k zabezpečení a řízení hesel účtů ve spravované doméně Azure AD DS.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 1a14637e-b3d0-4fd9-ba7a-576b8df62ff2
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: article
ms.date: 03/30/2020
ms.author: iainfou
ms.openlocfilehash: cc1863ff4200ee4cca94f18e3adfffa7ea42db96
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80475951"
---
# <a name="password-and-account-lockout-policies-on-managed-domains"></a>Zásady uzamčení hesel a účtů ve spravovaných doménách

Chcete-li spravovat zabezpečení uživatelů ve službě Azure Active Directory Domain Services (Azure AD DS), můžete definovat jemně odstupňované zásady hesel, které řídí nastavení uzamčení účtu nebo minimální délku a složitost hesla. Výchozí zásady jemně odstupňovaných hesel se vytvoří a použijí pro všechny uživatele ve spravované doméně Azure AD DS. Chcete-li poskytnout podrobné řízení a splnit konkrétní obchodní nebo dodržování předpisů potřeby, další zásady mohou být vytvořeny a použity pro konkrétní skupiny uživatelů.

Tento článek ukazuje, jak vytvořit a nakonfigurovat jemně odstupňované zásady hesel v Azure AD DS pomocí Centra správy služby Active Directory.

> [!NOTE]
> Zásady hesel jsou dostupné jenom pro spravované domény Azure AD DS vytvořené pomocí modelu nasazení Správce prostředků. U starších spravovaných domén vytvořených pomocí klasické [migrace z modelu klasické virtuální sítě do Správce prostředků][migrate-from-classic].

## <a name="before-you-begin"></a>Než začnete

Chcete-li tento článek dokončit, potřebujete následující zdroje a oprávnění:

* Aktivní předplatné Azure.
  * Pokud nemáte předplatné Azure, [vytvořte si účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Tenant Azure Active Directory přidružený k vašemu předplatnému, synchronizovaný s místním adresářem nebo s adresářem pouze pro cloud.
  * V případě potřeby [vytvořte klienta Azure Active Directory][create-azure-ad-tenant] nebo [přidružte předplatné Azure ke svému účtu][associate-azure-ad-tenant].
* Spravovaná doména Služby Azure Active Directory Domain Services povolená a nakonfigurovaná ve vašem tenantovi Azure AD.
  * V případě potřeby proveďte kurz [a vytvořte a nakonfigurujte instanci služby Azure Active Directory Domain Services][create-azure-ad-ds-instance].
  * Instance Azure AD DS musí být vytvořena pomocí modelu nasazení Správce prostředků. V případě potřeby [migrujte z modelu klasické virtuální sítě do Správce prostředků][migrate-from-classic].
* Virtuální počítač pro správu Windows Serveru, který je spojený se spravovanou doménou Azure AD DS.
  * V případě potřeby dokončete kurz [a vytvořte virtuální virtuální virtuální montovny pro správu][tutorial-create-management-vm].
* Uživatelský účet, který je členem *skupiny správců Azure AD DC* ve vašem tenantovi Azure AD.

## <a name="default-password-policy-settings"></a>Výchozí nastavení zásad hesla

Jemně odstupňované zásady hesel (FGPP) umožňují použít konkrétní omezení pro zásady uzamčení hesla a účtu pro různé uživatele v doméně. Chcete-li například zabezpečit privilegované účty, můžete použít přísnější nastavení uzamčení účtu než běžné účty bez oprávnění. Můžete vytvořit více FGPP v rámci spravované domény Azure AD DS a určit pořadí priority pro jejich použití pro uživatele.

Zásady jsou distribuovány prostřednictvím přidružení skupiny ve spravované doméně Azure AD DS a všechny změny, které provedete, se použijí při příštím přihlášení uživatele. Změnou zásadse neodemkne uživatelský účet, který je již uzamčen.

Zásady hesel se chovají trochu jinak v závislosti na tom, jak byl vytvořen uživatelský účet, na který se vztahují. Existují dva způsoby, jak lze vytvořit uživatelský účet ve službě Azure AD DS:

* Uživatelský účet lze synchronizovat v z Azure AD. To zahrnuje uživatelské účty pouze pro cloud vytvořené přímo v Azure a hybridní uživatelské účty synchronizované z místního prostředí Služby AD DS pomocí služby Azure AD Connect.
    * Většina uživatelských účtů ve službě Azure AD DS se vytvoří prostřednictvím procesu synchronizace z Azure AD.
* Uživatelský účet lze ručně vytvořit ve spravované doméně Azure AD DS a neexistuje ve službě Azure AD.

Všichni uživatelé, bez ohledu na to, jak jsou vytvořeni, mají následující zásady uzamčení účtu použité ve výchozích zásadách hesel ve službě Azure AD DS:

* **Doba uzamčení účtu:** 30
* **Počet povolených pokusů o přihlášení, které se nezdařily:** 5
* **Počet neúspěšných pokusů o přihlášení po:** 30 minutách
* **Maximální stáří hesla (životnost):** 90 dní

Při těchto výchozích nastaveních jsou uživatelské účty uzamčeny po dobu 30 minut, pokud je během 2 minut použito pět neplatných hesel. Účty se automaticky odemknou po 30 minutách.

K uzamčení účtu dochází pouze v rámci spravované domény. Uživatelské účty jsou uzamčeny jenom ve službě Azure AD DS a pouze z důvodu neúspěšných pokusů o přihlášení proti spravované doméně. Uživatelské účty, které byly synchronizovány v azure ad nebo místní nejsou uzamčeny ve svých zdrojových adresářích, jenom ve službě Azure AD DS.

Pokud máte zásady hesel Azure AD, která určuje maximální stáří hesla větší než 90 dní, stáří hesla se použije na výchozí zásady ve službě Azure AD DS. Můžete nakonfigurovat vlastní zásady hesla definovat jiný maximální stáří hesla v Azure AD DS. Dbát, pokud máte kratší maximální stáří hesla nakonfigurované v zásadách hesla Azure AD DS než ve službě Azure AD nebo v místním prostředí služby AD DS. V tomto scénáři může vypršet heslo uživatele ve službě Azure AD DS dříve, než se zobrazí výzva ke změně ve službě Azure AD nebo v místním prostředí služby AD DS.

Pro uživatelské účty vytvořené ručně ve spravované doméně Azure AD DS se také použijí následující další nastavení hesla z výchozí zásady. Tato nastavení se nevztahují na uživatelské účty synchronizované ve službě Azure AD, protože uživatel nemůže aktualizovat své heslo přímo ve službě Azure AD DS.

* **Minimální délka hesla (znaky):** 7
* **Hesla musí splňovat požadavky na složitost**

Nastavení uzamčení účtu nebo hesla nelze změnit ve výchozích zásadách hesel. Místo toho mohou členové *skupiny AAD DC Administrators* vytvořit vlastní zásady hesel a nakonfigurovat je tak, aby přepsala (měla přednost před) výchozí předdefinovanou zásadou, jak je znázorněno v další části.

## <a name="create-a-custom-password-policy"></a>Vytvoření vlastních zásad hesel

Při vytváření a spouštění aplikací v Azure můžete chtít nakonfigurovat vlastní zásady hesel. Můžete například vytvořit zásadu pro nastavení různých zásad uzamčení účtu.

Vlastní zásady hesel se používají pro skupiny ve spravované doméně Azure AD DS. Tato konfigurace účinně přepíše výchozí zásady.

Chcete-li vytvořit vlastní zásady hesel, použijte nástroje pro správu služby Active Directory z virtuálního počítače přilehlého k doméně. Centrum správy služby Active Directory umožňuje zobrazit, upravit a vytvořit prostředky ve spravované doméně Azure AD DS, včetně vou.

> [!NOTE]
> Chcete-li vytvořit vlastní zásady hesel ve spravované doméně Azure AD DS, musíte být přihlášeni k uživatelskému účtu, který je členem *skupiny Správci řadiče domény Řadičdomény AAD.*

1. Na úvodní obrazovce vyberte **Nástroje pro správu**. Seznam dostupných nástrojů pro správu je zobrazen, které byly nainstalovány v kurzu [k vytvoření virtuálního virtuálního virtuálního uživatele pro správu][tutorial-create-management-vm].
1. Chcete-li vytvořit a spravovat hlavní sady, vyberte ze seznamu nástrojů pro správu **Centrum správy služby Active Directory.**
1. V levém podokně zvolte spravovanou doménu Azure AD DS, například *aaddscontoso.com*.
1. Otevřete **systémový** kontejner a potom **kontejner nastavení hesla**.

    Zobrazí se integrovaná zásada hesel pro spravovanou doménu Azure AD DS. Tuto předdefinovanou zásadu nelze změnit. Místo toho vytvořte vlastní zásady hesel k přepsání výchozí zásady.

    ![Vytvoření zásad hesel v Centru správy služby Active Directory](./media/password-policy/create-password-policy-adac.png)

1. V panelu **Úkoly** vpravo vyberte **Nový > Nastavení hesla**.
1. V dialogovém okně **Vytvořit nastavení hesla** zadejte název zásady, například *MyCustomFGPP*.
1. Pokud existuje více zásad hesel, zásady s nejvyšší prioritou nebo prioritou se použijí na uživatele. Čím nižší číslo, tím vyšší priorita. Výchozí zásady hesel mají prioritu *200*.

    Nastavte prioritu vlastních zásad hesel tak, aby přepsala výchozí hodnotu, například *1*.

1. Podle potřeby upravte další nastavení zásad hesel. Pamatujte si následující klíčové body:

    * Nastavení, jako je složitost hesla, věk nebo doba vypršení platnosti jenom pro uživatele ručně vytvořené ve spravované doméně Azure AD DS.
    * Nastavení uzamčení účtu platí pro všechny uživatele, ale projeví se jenom v rámci spravované domény a ne v samotné azure ad.

    ![Vytvoření vlastní choujecí zásady hesel](./media/password-policy/custom-fgpp.png)

1. Zrušit zaškrtnutí **políčka Chránit před náhodným odstraněním**. Pokud je tato volba vybraná, fgpp nelze uložit.
1. V části **Přímo se vztahuje na** vyberte tlačítko **Přidat.** V dialogovém okně **Vybrat uživatele nebo skupiny** vyberte tlačítko **Umístění.**

    ![Vyberte uživatele a skupiny, na které chcete použít zásady hesel.](./media/password-policy/fgpp-applies-to.png)

1. Zásady hesel lze použít pouze pro skupiny. V dialogovém okně **Umístění** rozbalte název domény, například *aaddscontoso.com*, a vyberte ou akci, například **Uživatele AADDC**. Pokud máte vlastní oupoložku, která obsahuje skupinu uživatelů, které chcete použít, vyberte tuto individuální oovou položku.

    ![Vyberte ou, do které skupina patří.](./media/password-policy/fgpp-container.png)

1. Zadejte název skupiny, na kterou chcete zásadu použít, a pak vyberte **Zkontrolovat názvy,** abyste ověřili, zda skupina existuje.

    ![Vyhledejte a vyberte skupinu, která má použít FGPP.](./media/password-policy/fgpp-apply-group.png)

1. S názvem skupiny, kterou jste vybrali, se nyní zobrazí v části **Přímo se vztahuje na,** vyberte **OK,** chcete-li uložit vlastní zásady hesel.

## <a name="next-steps"></a>Další kroky

Další informace o zásadách hesel a použití Centra pro správu služby Active Directory naleznete v následujících článcích:

* [Informace o jemně odstupňovaných zásadách hesel](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770394(v=ws.10))
* [Konfigurace zásad jemných hesel pomocí Centra pro správu služby AD](/windows-server/identity/ad-ds/get-started/adac/introduction-to-active-directory-administrative-center-enhancements--level-100-#fine_grained_pswd_policy_mgmt)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[migrate-from-classic]: migrate-from-classic-vnet.md
