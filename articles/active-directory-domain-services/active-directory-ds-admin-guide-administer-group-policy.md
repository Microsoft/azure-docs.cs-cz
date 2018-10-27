---
title: 'Azure Active Directory Domain Services: Správa zásad skupiny ve spravované domény | Dokumentace Microsoftu'
description: Zásady skupiny spravovat v Azure Active Directory Domain Services spravované domény
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: mtillman
editor: curtand
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/22/2018
ms.author: ergreenl
ms.openlocfilehash: 31805f7588a58eb1682ce50fd55dd14b967d6099
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2018
ms.locfileid: "50158320"
---
# <a name="administer-group-policy-on-an-azure-ad-domain-services-managed-domain"></a>Správa zásad skupiny ve spravované doméně služby Azure AD Domain Services
Azure Active Directory Domain Services zahrnuje předdefinované objekty zásad skupiny (GPO) pro kontejnery "Uživatelé AADDC" a "Kontejnery počítače AADDC". Můžete přizpůsobit tyto předdefinované objekty zásad skupiny ke konfigurování zásad skupiny ve spravované doméně. Členové skupiny 'Správci AAD DC' kromě toho můžete vytvořit své vlastní vlastní organizační jednotky ve spravované doméně. Můžete také vytvořit vlastní objekty zásad skupiny a propojují se s těmito vlastní organizační jednotky. Uživatelé, kteří patří do skupiny "Správci AAD DC" jsou udělena oprávnění pro správu zásad skupiny ve spravované doméně.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Než začnete
K provádění úkolů uvedených v tomto článku, budete potřebovat:

1. Platný **předplatného Azure**.
2. **Adresář Azure AD** – buď synchronizaci s místním adresářem nebo výhradně cloudový adresář.
3. **Azure AD Domain Services** musí být povolené pro adresář Azure AD. Pokud jste neudělali, postupujte podle všechny úkoly popsané v [příručce Začínáme](active-directory-ds-getting-started.md).
4. A **virtuální počítač připojený k doméně** ze kterého budete spravovat spravované doméně služby Azure AD Domain Services. Pokud nemáte virtuální počítač, proveďte všechny úkoly popsané v článku s názvem [připojení virtuálního počítače s Windows k spravované doméně](active-directory-ds-admin-guide-join-windows-vm.md).
5. Potřebujete přihlašovací údaje **uživatelský účet patřící do skupiny "Správci AAD DC"** ve vašem adresáři, ke správě zásad skupiny vaší spravované domény.

<br>

## <a name="task-1---provision-a-domain-joined-virtual-machine-to-remotely-administer-group-policy-for-the-managed-domain"></a>Úloha 1 – zřízení virtuálního počítače připojené k doméně pro vzdálenou správu zásad skupiny pro spravovanou doménu
Spravované domény služby Azure AD Domain Services je možné spravovat vzdáleně pomocí známých nástrojů pro správu služby Active Directory jako je správu Center Active Directory (ADAC) nebo Powershellu AD. Podobně zásad skupiny pro spravovanou doménu je možné spravovat vzdáleně pomocí nástrojů pro správu zásad skupiny.

Správce v adresáři služby Azure AD nemá oprávnění k připojení k řadiči domény ve spravované doméně přes vzdálenou plochu. Členové skupiny "Správci AAD DC" můžete spravovat zásad skupiny pro spravované domény vzdáleně. Použitím nástrojů zásad skupiny na počítači serveru/klientu Windows k spravované doméně. Nástroje zásad skupiny můžete nainstalovat v rámci volitelná funkce Správa zásad skupiny ve Windows serveru a klientské počítače připojené k spravované doméně.

První úloha je ke zřízení virtuálního počítače s Windows serverem, který je připojený ke spravované doméně. Pokyny najdete v článku s názvem [připojení virtuálního počítače s Windows serverem do spravované domény služby Azure AD Domain Services](active-directory-ds-admin-guide-join-windows-vm.md).

## <a name="task-2---install-group-policy-tools-on-the-virtual-machine"></a>Úloha 2 – zásady skupiny instalace nástroje na virtuálním počítači
Proveďte následující kroky k instalaci nástroje pro správu zásad skupiny na virtuálním počítači připojené k doméně.

1. Přejděte na web Azure Portal. Klikněte na tlačítko **všechny prostředky** na panelu vlevo. Vyhledejte a klikněte na virtuální počítač, který jste vytvořili v úloze 1.
2. Klikněte na tlačítko **připojit** tlačítko na kartě Přehled. Vytvoří a stáhne se soubor Remote Desktop Protocol (RDP).

    ![Připojení k virtuálnímu počítači Windows](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)
3. Chcete-li se připojit k virtuálnímu počítači, otevřete stažený soubor protokolu RDP. Pokud se zobrazí výzva, klikněte na **Připojit**. Na řádku přihlášení pomocí přihlašovacích údajů uživatele, které patří do skupiny "Správci AAD DC". Například používáme "bob@domainservicespreview.onmicrosoft.com" v našem případě. Během procesu přihlášení se může zobrazit upozornění certifikátu. Klikněte na tlačítko Ano nebo dál pokračujte v připojování.
4. Na obrazovce Start otevřete **správce serveru**. Klikněte na tlačítko **přidat role a funkce** ve středovém podokně okna Správce serveru.

    ![Spusťte správce serveru na virtuálním počítači](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager.png)
5. Na **před zahájením** stránku **Průvodce přidání rolí a funkcí**, klikněte na tlačítko **Další**.

    ![Před zahájením stránku](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-begin.png)
6. Na **typ instalace** ponechte **instalace na základě rolí nebo na základě funkcí** zaškrtnuto políčko a klikněte na tlačítko **Další**.

    ![Stránka typu instalace](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-type.png)
7. Na **výběr serveru** stránce vyberte aktuální virtuální počítač z fondu serverů a klikněte na tlačítko **Další**.

    ![Stránka pro výběr serveru](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-server.png)
8. Na **role serveru** klikněte na **Další**. Tato stránka přeskočíme, protože jsme se neinstalují žádné role na serveru.
9. Na **funkce** stránky, vyberte **Správa zásad skupiny** funkce.

    ![Funkce stránky](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-gp-management.png)
10. Na **potvrzení** klikněte na **nainstalovat** nainstalovat funkci Správa zásad skupiny na virtuálním počítači. Po úspěšném dokončení instalace funkce klikněte na tlačítko **Zavřít** ukončíte **přidat role a funkce** průvodce.

    ![Stránka potvrzení](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-gp-management-confirmation.png)

## <a name="task-3---launch-the-group-policy-management-console-to-administer-group-policy"></a>Úloha 3: Spusťte konzolu pro správu zásad skupiny pro správu zásad skupiny
Můžete použít konzolu pro správu zásad skupiny na virtuálním počítači připojeném k doméně pro správu zásad skupiny ve spravované doméně.

> [!NOTE]
> Musíte být členem skupiny "Správci AAD DC" Správa zásad skupiny ve spravované doméně.
>
>

1. Na obrazovce Start klikněte na tlačítko **nástroje pro správu**. Měli byste vidět **Správa zásad skupiny** konzola nainstalovaná na virtuálním počítači.

    ![Správa zásad skupiny spuštění](./media/active-directory-domain-services-admin-guide/gp-management-installed.png)
2. Klikněte na tlačítko **Správa zásad skupiny** ke spuštění konzoly pro správu zásad skupiny.

    ![Konzola zásad skupiny](./media/active-directory-domain-services-admin-guide/gp-management-console.png)

## <a name="task-4---customize-built-in-group-policy-objects"></a>Úloha 4: Přizpůsobení předdefinované objekty zásad skupiny
Existují dvě předdefinované objekty zásad skupiny (GPO) – jeden pro "Kontejnery počítače AADDC" a "Uživatelé AADDC" kontejnery ve vaší spravované doméně. Můžete přizpůsobit tyto objekty zásad skupiny ke konfigurování zásad skupiny ve spravované doméně.

1. V **Správa zásad skupiny** konzole, rozbalte kliknutím **doménová struktura: contoso100.com** a **domén** uzlů, čímž zobrazíte zásady skupiny pro vaši spravovanou doménu.

    ![Předdefinované objekty zásad skupiny](./media/active-directory-domain-services-admin-guide/builtin-gpos.png)
2. Můžete přizpůsobit tyto předdefinované objekty zásad skupiny nakonfigurovat zásady skupiny ve vaší spravované doméně. Klikněte pravým tlačítkem na objekt zásad skupiny a klikněte na tlačítko **upravit...**  pro přizpůsobení integrovaného objektu zásad skupiny. Nástroj Configuration Editor zásad skupiny umožňuje přizpůsobit objekt zásad skupiny.

    ![Upravit integrovaného objektu zásad skupiny](./media/active-directory-domain-services-admin-guide/edit-builtin-gpo.png)
3. Teď můžete použít **Editor správy zásad skupiny** konzolu k úpravě integrovaného objektu zásad skupiny. Například na následujícím snímku obrazovky ukazuje, jak přizpůsobit integrovaného objektu zásad skupiny "Kontejnery počítače AADDC".

    ![Přizpůsobení objektu zásad skupiny](./media/active-directory-domain-services-admin-guide/gp-editor.png)

## <a name="task-5---create-a-custom-group-policy-object-gpo"></a>Úloha 5: vytvoření vlastní objekt zásad skupiny (GPO)
Můžete vytvořit nebo importovat vlastní objekty zásad skupiny vlastní. Vlastní objekty zásad skupiny můžete také propojit do vlastní organizační jednotky, které jste vytvořili v vaší spravované domény. Další informace o vytváření vlastních organizační jednotky, najdete v části [vytvořit vlastní organizační jednotky ve spravované doméně](active-directory-ds-admin-guide-create-ou.md).

> [!NOTE]
> Musíte být členem skupiny "Správci AAD DC" Správa zásad skupiny ve spravované doméně.
>
>

1. V **Správa zásad skupiny** konzoly, klikněte na tlačítko Vybrat vlastní organizační jednotky (OU). Klikněte pravým tlačítkem na organizační jednotku a klikněte na tlačítko **vytvořit objekt zásad skupiny v této doméně a propojit jej sem...** .

    ![Vytvořit vlastní objekt zásad skupiny](./media/active-directory-domain-services-admin-guide/gp-create-gpo.png)
2. Zadejte název pro nový objekt zásad skupiny a klikněte na **OK**.

    ![Zadejte název pro objekt zásad skupiny](./media/active-directory-domain-services-admin-guide/gp-specify-gpo-name.png)
3. Nový objekt zásad skupiny je vytvořené a připojené k vaší vlastní organizační jednotky. Klikněte pravým tlačítkem na objekt zásad skupiny a klikněte na tlačítko **upravit...**  z nabídky.

    ![Nově vytvořeného objektu zásad skupiny](./media/active-directory-domain-services-admin-guide/gp-gpo-created.png)
4. Můžete přizpůsobit na nově vytvořený pomocí objektu zásad skupiny **Editor správy zásad skupiny**.

    ![Přizpůsobení nového objektu zásad skupiny](./media/active-directory-domain-services-admin-guide/gp-customize-gpo.png)


Další informace o používání [konzoly pro správu zásad skupiny](https://technet.microsoft.com/library/cc753298.aspx) je k dispozici na webu Technet.

## <a name="related-content"></a>Související obsah
* [Azure AD Domain Services – Příručka Začínáme](active-directory-ds-getting-started.md)
* [Připojte se k virtuálnímu počítači s Windows serverem do spravované domény služby Azure AD Domain Services](active-directory-ds-admin-guide-join-windows-vm.md)
* [Správa spravované domény služby Azure AD Domain Services](active-directory-ds-admin-guide-administer-domain.md)
* [Konzola pro správu zásad skupiny](https://technet.microsoft.com/library/cc753298.aspx)
