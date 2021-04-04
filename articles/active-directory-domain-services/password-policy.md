---
title: Vytvoření a použití zásad hesel v Azure AD Domain Services | Microsoft Docs
description: Přečtěte si, jak a proč používat podrobné zásady pro hesla k zabezpečení a řízení hesel účtů ve spravované doméně Azure služba AD DS.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 1a14637e-b3d0-4fd9-ba7a-576b8df62ff2
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: df132af1675b3f373fe1eab5685c5d2f07813445
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96619228"
---
# <a name="password-and-account-lockout-policies-on-azure-active-directory-domain-services-managed-domains"></a>Zásady hesel a uzamčení účtů v Azure Active Directory Domain Services spravované domény

Pokud chcete spravovat zabezpečení uživatelů v Azure Active Directory Domain Services (Azure služba AD DS), můžete definovat podrobné zásady pro hesla, které řídí nastavení uzamčení účtu nebo minimální délku hesla a složitost. Na všech uživatelích ve spravované doméně Azure služba AD DS se vytvoří a použije výchozí jemně odstupňované zásady hesel. Pro zajištění podrobného řízení a splnění konkrétních potřeb obchodu nebo dodržování předpisů je možné vytvořit další zásady a použít je u konkrétních skupin uživatelů.

V tomto článku se dozvíte, jak vytvořit a nakonfigurovat jemně odstupňované zásady pro hesla v Azure služba AD DS pomocí Centrum správy služby Active Directory.

> [!NOTE]
> Zásady hesel jsou k dispozici pouze pro spravované domény vytvořené pomocí modelu nasazení Správce prostředků. Pro starší spravované domény vytvořené pomocí klasického nasazení [migrujte z modelu klasických virtuálních sítí na správce prostředků][migrate-from-classic].

## <a name="before-you-begin"></a>Než začnete

K dokončení tohoto článku potřebujete následující prostředky a oprávnění:

* Musíte mít aktivní předplatné Azure.
  * Pokud nemáte předplatné Azure, [vytvořte účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Tenant Azure Active Directory přidružený k vašemu předplatnému, buď synchronizovaný s místním adresářem, nebo jenom s cloudovým adresářem.
  * V případě potřeby [vytvořte tenanta Azure Active Directory][create-azure-ad-tenant] nebo [přidružte předplatné Azure k vašemu účtu][associate-azure-ad-tenant].
* Ve vašem tenantovi Azure AD je povolená a nakonfigurovaná spravovaná doména Azure Active Directory Domain Services.
  * V případě potřeby dokončete kurz a [vytvořte a nakonfigurujte Azure Active Directory Domain Services spravovanou doménu][create-azure-ad-ds-instance].
  * Spravovaná doména musí být vytvořená pomocí modelu nasazení Správce prostředků. V případě potřeby [migrujte z modelu klasických virtuálních sítí na správce prostředků][migrate-from-classic].
* Virtuální počítač pro správu Windows serveru, který je připojený ke spravované doméně.
  * V případě potřeby dokončete kurz a [vytvořte virtuální počítač pro správu][tutorial-create-management-vm].
* Uživatelský účet, který je členem skupiny *správců řadičů domény Azure AD* ve vašem TENANTOVI Azure AD.

## <a name="default-password-policy-settings"></a>Výchozí nastavení zásad hesel

Jemně odstupňované zásady hesel (FGPPs) umožňují použít specifická omezení pro zásady hesel a uzamčení účtů pro různé uživatele v doméně. Například pro zabezpečení privilegovaných účtů můžete použít nastavení uzamčení účtu, než běžné účty bez oprávnění. Můžete vytvořit více FGPPs ve spravované doméně a určit pořadí, ve kterém mají být použity pro uživatele.

Další informace o zásadách hesel a používání Centra správy služby Active Directory najdete v následujících článcích:

* [Informace o jemně odstupňovaných zásadách hesel](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770394(v=ws.10))
* [Konfigurace jemně odstupňovaných zásad hesel pomocí centra pro správu služby AD](/windows-server/identity/ad-ds/get-started/adac/introduction-to-active-directory-administrative-center-enhancements--level-100-#fine_grained_pswd_policy_mgmt)

Zásady jsou distribuovány prostřednictvím přidružení skupiny ve spravované doméně a veškeré provedené změny se uplatní při přihlášení dalšího uživatele. Změna zásad neodemkne uživatelský účet, který je už uzamčený.

Zásady hesel se chovají trochu různě v závislosti na tom, jak se vytvořil uživatelský účet, na který jste použili. Existují dva způsoby, jak lze vytvořit uživatelský účet v Azure služba AD DS:

* Uživatelský účet může být synchronizovaný v rámci služby Azure AD. Patří sem pouze cloudové uživatelské účty vytvořené přímo v Azure a hybridní uživatelské účty synchronizované z místního služba AD DS prostředí pomocí Azure AD Connect.
    * Většina uživatelských účtů ve službě Azure služba AD DS se vytváří prostřednictvím procesu synchronizace z Azure AD.
* Uživatelský účet se dá ručně vytvořit ve spravované doméně a neexistuje ve službě Azure AD.

Všichni uživatelé, bez ohledu na to, jak jsou vytvořeny, mají následující zásady uzamčení účtu použité výchozími zásadami pro hesla v Azure služba AD DS:

* **Doba trvání uzamčení účtu:** 30
* **Počet povolených neúspěšných pokusů o přihlášení:** 5
* **Resetovat počet neúspěšných pokusů o přihlášení za:** 30 minut
* **Maximální stáří hesla (doba života):** 90 dní

S těmito výchozími nastaveními jsou uživatelské účty uzamčeny po dobu 30 minut, pokud se do 2 minut používá pět neplatných hesel. Účty se po 30 minutách automaticky odemkní.

K uzamčení účtu dochází pouze v rámci spravované domény. Uživatelské účty se v Azure služba AD DS jenom odpojí a v důsledku neúspěšných pokusů o přihlášení proti spravované doméně. Uživatelské účty, které se synchronizovaly z Azure AD nebo místně, nejsou ve zdrojových adresářích uzamčené, jenom v Azure služba AD DS.

Pokud máte zásady hesel Azure AD, které určují maximální stáří hesla delší než 90 dní, toto stáří hesla se použije na výchozí zásady v Azure služba AD DS. V Azure služba AD DS můžete nakonfigurovat vlastní zásady hesel, které definují jiné maximální stáří hesla. Pokud máte kratší stáří hesla, které je nakonfigurované v zásadách hesel služba AD DS Azure, než ve službě Azure AD nebo v místním prostředí služba AD DS, postarejte se pečlivě. V takovém případě může platnost hesla uživatele vypršet v Azure služba AD DS před tím, než se zobrazí výzva ke změně v Azure AD nebo v místním prostředí služba AD DS.

U uživatelských účtů, které se ve spravované doméně vytvořily ručně, se z výchozích zásad uplatní taky následující další nastavení hesla. Tato nastavení se nevztahují na uživatelské účty synchronizované v rámci služby Azure AD, protože uživatel nemůže aktualizovat heslo přímo v Azure služba AD DS.

* **Minimální délka hesla (ve znacích):** 7
* **Hesla musí splňovat požadavky na složitost.**

Nastavení uzamčení nebo hesla účtu nemůžete změnit ve výchozích zásadách hesel. Místo toho můžou členové skupiny *Správci AAD DC* vytvořit vlastní zásady pro hesla a nakonfigurovat je tak, aby se přepsaly (mají přednost před) výchozí vestavěnou zásadou, jak je znázorněno v další části.

## <a name="create-a-custom-password-policy"></a>Vytvoření vlastních zásad hesel

Při sestavování a spouštění aplikací v Azure možná budete chtít nakonfigurovat vlastní zásady hesel. Můžete například vytvořit zásadu, která nastaví různá nastavení zásad uzamčení účtů.

Vlastní zásady hesel se aplikují na skupiny ve spravované doméně. Tato konfigurace efektivně přepisuje výchozí zásady.

Pokud chcete vytvořit vlastní zásady pro hesla, použijte nástroje pro správu služby Active Directory z virtuálního počítače připojeného k doméně. Centrum správy služby Active Directory umožňuje zobrazit, upravit a vytvořit prostředky ve spravované doméně, včetně organizačních jednotek.

> [!NOTE]
> Pokud chcete vytvořit vlastní zásady hesel ve spravované doméně, musíte být přihlášeni k uživatelskému účtu, který je členem skupiny *AAD DC Administrators* .

1. Z obrazovky Start vyberte **Nástroje pro správu**. Zobrazí se seznam dostupných nástrojů pro správu, které byly nainstalovány v tomto kurzu, aby bylo možné [vytvořit virtuální počítač pro správu][tutorial-create-management-vm].
1. Chcete-li vytvořit a spravovat organizační jednotky, vyberte **Centrum správy služby Active Directory** ze seznamu nástrojů pro správu.
1. V levém podokně vyberte spravovanou doménu, například *aaddscontoso.com*.
1. Otevřete kontejner **systému** a pak **kontejner nastavení hesel**.

    Zobrazí se předdefinovaná zásada hesla pro spravovanou doménu. Tuto vestavěnou zásadu nemůžete upravit. Místo toho vytvořte vlastní zásadu hesla pro přepsání výchozích zásad.

    ![Vytvoření zásad pro hesla v Centrum správy služby Active Directory](./media/password-policy/create-password-policy-adac.png)

1. Na panelu **úlohy** na pravé straně vyberte **nové nastavení > hesla**.
1. V dialogovém okně **vytvořit nastavení hesla** zadejte název zásady, například *MyCustomFGPP*.
1. Pokud existuje více zásad hesel, uplatní se u uživatele zásada s nejvyšší prioritou nebo prioritou. Čím nižší číslo, tím vyšší Priorita. Výchozí zásady pro hesla mají prioritu *200*.

    Nastavte prioritu pro vlastní zásady hesel, abyste popsali výchozí hodnotu, třeba *1*.

1. Podle potřeby upravte další nastavení zásad hesel. Pamatujte na tyto klíčové body:

    * Nastavení jako složitost hesla, stáří nebo čas vypršení platnosti pouze uživatelům, kteří jsou ručně vytvořeni ve spravované doméně.
    * Nastavení uzamčení účtů platí pro všechny uživatele, ale projeví se pouze v rámci spravované domény, nikoli v samotné službě Azure AD.

    ![Vytvořit vlastní jemně odstupňované zásady pro hesla](./media/password-policy/custom-fgpp.png)

1. Zrušte kontrolu před **náhodným odstraněním**. Pokud je vybraná tato možnost, nemůžete uložit podrobné zásady.
1. V části **přímo platí pro** vyberte tlačítko **Přidat** . V dialogovém okně **Vybrat uživatele nebo skupiny** vyberte tlačítko **umístění** .

    ![Vyberte uživatele a skupiny, pro které chcete zásady hesel použít.](./media/password-policy/fgpp-applies-to.png)

1. Zásady hesel se dají použít jenom u skupin. V dialogovém okně **umístění** rozbalte název domény, například *aaddscontoso.com*, a pak vyberte organizační jednotku, například **uživatele AADDC**. Pokud máte vlastní organizační jednotku obsahující skupinu uživatelů, kterou chcete použít, vyberte tuto organizační jednotku.

    ![Vyberte organizační jednotku, do které skupina patří.](./media/password-policy/fgpp-container.png)

1. Zadejte název skupiny, na kterou chcete zásadu použít, a potom vyberte **Zkontrolovat názvy** , abyste ověřili, že skupina existuje.

    ![Vyhledejte a vyberte skupinu, kterou chcete použít podrobné zásady](./media/password-policy/fgpp-apply-group.png)

1. S názvem skupiny, kterou jste vybrali, se teď zobrazí **přímo v části platí pro** , vyberte **OK** a uložte vlastní zásady hesel.

## <a name="next-steps"></a>Další kroky

Další informace o zásadách hesel a používání Centra správy služby Active Directory najdete v následujících článcích:

* [Informace o jemně odstupňovaných zásadách hesel](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770394(v=ws.10))
* [Konfigurace jemně odstupňovaných zásad hesel pomocí centra pro správu služby AD](/windows-server/identity/ad-ds/get-started/adac/introduction-to-active-directory-administrative-center-enhancements--level-100-#fine_grained_pswd_policy_mgmt)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[migrate-from-classic]: migrate-from-classic-vnet.md
