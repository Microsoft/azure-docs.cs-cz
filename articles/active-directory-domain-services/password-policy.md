---
title: Vytvoření a použití zásad hesel v Azure AD Domain Services | Microsoft Docs
description: Přečtěte si, jak a proč používat podrobné zásady pro hesla k zabezpečení a řízení hesel účtů ve spravované doméně Azure služba AD DS.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 1a14637e-b3d0-4fd9-ba7a-576b8df62ff2
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: article
ms.date: 08/08/2019
ms.author: iainfou
ms.openlocfilehash: 45fb2daaeaf9ee788207d43d805e070320372ca0
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2019
ms.locfileid: "69617137"
---
# <a name="password-and-account-lockout-policies-on-managed-domains"></a>Zásady hesel a uzamčení účtů ve spravovaných doménách

Pokud chcete spravovat zabezpečení účtu v Azure Active Directory Domain Services (Azure služba AD DS), můžete definovat podrobné zásady pro hesla, které určují nastavení, jako je například minimální délka hesla, čas vypršení platnosti hesla nebo složitost hesla. Výchozí zásady pro hesla se uplatní pro všechny uživatele ve spravované doméně Azure služba AD DS. Pro zajištění podrobného řízení a splnění konkrétních potřeb obchodu nebo dodržování předpisů je možné vytvořit další zásady a použít je u konkrétních skupin uživatelů.

V tomto článku se dozvíte, jak vytvořit a nakonfigurovat jemně odstupňované zásady pro hesla pomocí Centrum správy služby Active Directory.

## <a name="before-you-begin"></a>Před zahájením

K dokončení tohoto článku potřebujete následující prostředky a oprávnění:

* Aktivní předplatné Azure.
  * Pokud nemáte předplatné Azure, [vytvořte účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Tenant Azure Active Directory přidružený k vašemu předplatnému, buď synchronizovaný s místním adresářem, nebo jenom s cloudovým adresářem.
  * V případě potřeby [vytvořte tenanta Azure Active Directory][create-azure-ad-tenant] nebo [přidružte předplatné Azure k vašemu účtu][associate-azure-ad-tenant].
* Ve vašem tenantovi Azure AD je povolená a nakonfigurovaná spravovaná doména Azure Active Directory Domain Services.
  * V případě potřeby dokončete kurz a [vytvořte a nakonfigurujte instanci Azure Active Directory Domain Services][create-azure-ad-ds-instance].
* Virtuální počítač pro správu Windows serveru, který je připojený k spravované doméně Azure služba AD DS.
  * V případě potřeby dokončete kurz a [vytvořte virtuální počítač pro správu][tutorial-create-management-vm].
* Uživatelský účet, který je členem skupiny správců *řadičů domény Azure AD* ve vašem TENANTOVI Azure AD.

## <a name="fine-grained-password-policies-fgpp-overview"></a>Přehled zásad podrobné zásady (jemně odstupňovaných hesel)

Jemně odstupňované zásady hesel (FGPPs) umožňují použít specifická omezení pro zásady hesel a uzamčení účtů pro různé uživatele v doméně. Například pro zabezpečení privilegovaných účtů můžete použít přísnější nastavení hesla než běžné účty bez oprávnění. Můžete vytvořit několik FGPPs a zadat zásady hesel v rámci spravované domény Azure služba AD DS.

Pomocí podrobné zásady je možné nakonfigurovat následující nastavení hesla:

* Minimální délka hesla
* Historie hesel
* Hesla musí splňovat požadavky na složitost.
* Minimální stáří hesla
* Maximální stáří hesla
* Zásady uzamčení účtů
  * Doba trvání uzamčení účtu
  * Počet povolených neúspěšných pokusů o přihlášení
  * Resetovat počet neúspěšných pokusů o přihlášení za

Podrobné zásady má vliv jenom na uživatele vytvořené v Azure služba AD DS. Uživatelé cloudu a uživatelé domény synchronizovaný do spravované domény Azure služba AD DS ve službě Azure AD nejsou ovlivněné zásadami hesel.

Zásady se distribuují prostřednictvím přidružení skupiny ve spravované doméně Azure služba AD DS a veškeré změny, které provedete, se uplatní při přihlášení dalšího uživatele. Změna zásad neodemkne uživatelský účet, který je už uzamčený.

## <a name="default-fine-grained-password-policy-settings"></a>Výchozí jemně odstupňovaná nastavení zásad hesel

Ve spravované doméně Azure služba AD DS jsou ve výchozím nastavení nakonfigurované následující zásady hesel a používají se pro všechny uživatele:

* **Minimální délka hesla (ve znacích):** 7
* **Maximální stáří hesla (doba života):** 90 dnů
* **Hesla musí splňovat požadavky na složitost.**

Ve výchozím nastavení se pak konfigurují následující zásady uzamčení účtů:

* **Doba trvání uzamčení účtu:** 30
* **Počet povolených neúspěšných pokusů o přihlášení:** 5
* **Resetovat počet neúspěšných pokusů o přihlášení po:** 30 minut

S těmito výchozími nastaveními jsou uživatelské účty uzamčeny po dobu 30 minut, pokud se do 2 minut používá pět neplatných hesel. Účty se po 30 minutách automaticky odemkní.

Nemůžete upravit ani odstranit výchozí předdefinované zásady pro hesla. Místo toho můžou členové skupiny *Správci AAD DC* vytvořit vlastní podrobné zásady a nakonfigurovat je tak, aby se přepsal (mají přednost před) výchozí předdefinovaný podrobné zásady, jak je znázorněno v další části.

## <a name="create-a-custom-fine-grained-password-policy"></a>Vytvořit vlastní jemně odstupňované zásady pro hesla

Při sestavování a aplikacích v Azure možná budete chtít nakonfigurovat vlastní podrobné zásady. Mezi příklady nutnosti vytvořit vlastní podrobné zásady patří například nastavení jiné zásady uzamčení účtů nebo konfigurace výchozího nastavení doby života hesla pro spravovanou doménu.

Můžete vytvořit vlastní podrobné zásady a použít ji na konkrétní skupiny ve spravované doméně Azure služba AD DS. Tato konfigurace efektivně přepisuje výchozí podrobné zásady. Můžete také vytvořit vlastní jemně odstupňované zásady pro hesla a použít je na jakékoli vlastní organizační jednotky, které vytvoříte ve spravované doméně Azure služba AD DS.

Pokud chcete vytvořit podrobné zásady pro hesla, použijte nástroje pro správu služby Active Directory z virtuálního počítače připojeného k doméně. Centrum správy služby Active Directory umožňuje zobrazovat, upravovat a vytvářet prostředky ve spravované doméně Azure služba AD DS, včetně organizačních jednotek.

> [!NOTE]
> Pokud chcete vytvořit jemně odstupňované zásady pro hesla ve spravované doméně Azure služba AD DS, musíte být přihlášení k uživatelskému účtu, který je členem skupiny *Správci AAD DC* .

1. Z obrazovky Start vyberte **Nástroje pro správu**. Zobrazí se seznam dostupných nástrojů pro správu, které byly nainstalovány v tomto kurzu, aby bylo možné [vytvořit virtuální počítač pro správu][tutorial-create-management-vm].
1. Chcete-li vytvořit a spravovat organizační jednotky, vyberte **Centrum správy služby Active Directory** ze seznamu nástrojů pro správu.
1. V levém podokně vyberte spravovanou doménu Azure služba AD DS, například *contoso.com*.
1. Na panelu **úlohy** na pravé straně vyberte **nové nastavení > hesla**.
1. V dialogovém okně **vytvořit nastavení hesla** zadejte název zásady, například *MyCustomFGPP*. Nastavte prioritu tak, aby bylo vhodné přepsat výchozí podrobné zásady (což je *200*), například *1*.

    Podle potřeby upravte další nastavení zásad hesla, například **Vynutit historii hesel** , aby uživatel musel vytvořit heslo, které se liší od předchozích *24* hesel.

    ![Vytvořit vlastní jemně odstupňované zásady pro hesla](./media/how-to/custom-fgpp.png)

1. Zruštekontrolu před náhodným odstraněním. Pokud je vybraná tato možnost, nemůžete uložit podrobné zásady.
1. V části **přímo platí pro** vyberte tlačítko **Přidat** . V dialogovém okně **Vybrat uživatele nebo skupiny** klikněte na tlačítko **umístění** .

    ![Vyberte uživatele a skupiny, pro které chcete zásady hesel použít.](./media/how-to/fgpp-applies-to.png)

1. Jemně odstupňované zásady pro hesla se dají použít jenom u skupin. V dialogovém okně **umístění** rozbalte název domény, například *contoso.com*, a pak vyberte organizační jednotku, například **uživatele AADDC**. Pokud máte vlastní organizační jednotku obsahující skupinu uživatelů, kterou chcete použít, vyberte tuto organizační jednotku.

    ![Vyberte organizační jednotku, do které skupina patří.](./media/how-to/fgpp-container.png)

1. Zadejte název skupiny, na kterou chcete zásadu použít, a potom vyberte **Zkontrolovat názvy** , abyste ověřili, že skupina existuje.

    ![Vyhledejte a vyberte skupinu, kterou chcete použít podrobné zásady](./media/how-to/fgpp-apply-group.png)

1. S názvem skupiny, kterou jste vybrali, se teď zobrazí **přímo v části platí pro** , vyberte **OK** a uložte vlastní zásady hesel.

## <a name="next-steps"></a>Další postup

Další informace o podrobných zásadách pro hesla a o použití centra pro správu služby Active Directory najdete v následujících článcích:

* [Informace o jemně odstupňovaných zásadách hesel](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770394(v=ws.10))
* [Konfigurace jemně odstupňovaných zásad hesel pomocí centra pro správu služby AD](/windows-server/identity/ad-ds/get-started/adac/introduction-to-active-directory-administrative-center-enhancements--level-100-#fine_grained_pswd_policy_mgmt)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
