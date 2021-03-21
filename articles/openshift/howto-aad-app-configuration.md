---
title: Integrace Azure Active Directory pro Azure Red Hat OpenShift
description: Naučte se, jak vytvořit skupinu zabezpečení Azure AD a uživatele pro testování aplikací v clusteru Microsoft Azure Red Hat OpenShift.
author: jimzim
ms.author: jzim
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: f0bf28d61d4c9ad95a485fb4b60e370c16ace16c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100633321"
---
# <a name="azure-active-directory-integration-for-azure-red-hat-openshift"></a>Integrace Azure Active Directory pro Azure Red Hat OpenShift

> [!IMPORTANT]
> Azure Red Hat OpenShift 3,11 bude vyřazeno 30. června 2022. Podpora pro vytváření nových clusterů Azure Red Hat OpenShift 3,11 pokračuje do 30. listopadu 2020. Po vyřazení z provozu budou zbývající clustery Azure Red Hat OpenShift 3,11 vypnuté, aby se předešlo chybám zabezpečení.
> 
> Podle tohoto průvodce [vytvořte cluster Azure Red Hat OpenShift 4](tutorial-create-cluster.md).
> Pokud máte konkrétní otázky, [kontaktujte nás prosím](mailto:arofeedback@microsoft.com).

Pokud jste ještě nevytvořili tenanta Azure Active Directory (Azure AD), postupujte podle pokynů v tématu [Vytvoření tenanta Azure AD pro Azure Red Hat OpenShift](howto-create-tenant.md) , než budete pokračovat v těchto pokynech.

Microsoft Azure Red Hat OpenShift potřebuje oprávnění k provádění úloh jménem vašeho clusteru. Pokud vaše organizace ještě nemá uživatele Azure AD, skupinu zabezpečení Azure AD nebo registraci aplikace služby Azure AD, která se má použít jako instanční objekt, vytvořte je podle těchto pokynů.

## <a name="create-a-new-azure-active-directory-user"></a>Vytvoření nového uživatele Azure Active Directory

V [Azure Portal](https://portal.azure.com)zajistěte, aby se váš tenant zobrazoval pod vaším uživatelským jménem v pravém horním rohu portálu:

![Snímek obrazovky portálu se stejným klientem v pravém horním rohu ](./media/howto-create-tenant/tenant-callout.png) Pokud se zobrazí špatný tenant, klikněte v pravém horním rohu na své uživatelské jméno, potom klikněte na **Přepnout adresář** a vyberte správného tenanta ze seznamu **všechny adresáře** .

Pokud se chcete přihlásit ke clusteru Azure Red Hat OpenShift, vytvořte nového Azure Active Directory uživatele "Owner".

1. Otevřete okno [Uživatelé – všichni uživatelé](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers) .
2. Kliknutím na **+ Nový uživatel** otevřete podokno **uživatel** .
3. Zadejte **jméno** pro tohoto uživatele.
4. Vytvořte **uživatelské jméno** na základě názvu vytvořeného tenanta s  `.onmicrosoft.com` připojením na konci. Například, `yourUserName@yourTenantName.onmicrosoft.com`. Zapište si toto uživatelské jméno. Budete ho potřebovat k přihlášení ke clusteru.
5. Kliknutím na **role adresáře** otevřete podokno role adresáře a vyberte **vlastník** a pak klikněte na **OK** v dolní části podokna.
6. V podokně **uživatel** klikněte na možnost **Zobrazit heslo** a poznamenejte si dočasné heslo. Po prvním přihlášení se zobrazí výzva k jejímu resetování.
7. V dolní části podokna klikněte na **vytvořit** a vytvořte uživatele.

## <a name="create-an-azure-ad-security-group"></a>Vytvoření skupiny zabezpečení Azure AD

Aby bylo možné udělit přístup Správce clusteru, členství ve skupině zabezpečení Azure AD se synchronizuje do skupiny OpenShift "osa-Customer-Admins". Není-li tento parametr zadán, nebude udělen přístup Správce clusteru.

1. Otevřete okno [Azure Active Directory skupiny](https://portal.azure.com/#blade/Microsoft_AAD_IAM/GroupsManagementMenuBlade/AllGroups) .
2. Klikněte na **+ Nová skupina**.
3. Zadejte název a popis skupiny.
4. Nastavte **typ skupiny** na **zabezpečení**.
5. Nastavte **typ členství** na **přiřazeno**.

    Do této skupiny zabezpečení přidejte uživatele služby Azure AD, kterého jste vytvořili v předchozím kroku.

6. Kliknutím na **Členové** otevřete podokno **Vybrat členy** .
7. V seznamu Členové vyberte uživatele Azure AD, kterého jste vytvořili výše.
8. V dolní části portálu klikněte na **Vybrat** a pak **vytvořit** a vytvořte skupinu zabezpečení.

    Zapište hodnotu ID skupiny.

9. Když se skupina vytvoří, zobrazí se v seznamu všech skupin. Klikněte na novou skupinu.
10. Na stránce, která se zobrazí, zkopírujte **ID objektu**. Tato hodnota se na tuto hodnotu odkazuje jako `GROUPID` v kurzu [Vytvoření clusteru Azure Red Hat OpenShift](tutorial-create-cluster.md) .

> [!IMPORTANT]
> Pokud chcete tuto skupinu synchronizovat se skupinou osa-Customer-Admins OpenShift, vytvořte cluster pomocí Azure CLI. Azure Portal aktuálně chybí pole pro nastavení této skupiny.

## <a name="create-an-azure-ad-app-registration"></a>Vytvoření registrace aplikace Azure AD

V rámci vytváření clusteru můžete automaticky vytvořit klienta registrace aplikace Azure Active Directory (Azure AD), a to tak, že `--aad-client-app-id` příznak pro příkaz vynecháte `az openshift create` . V tomto kurzu se dozvíte, jak vytvořit registraci aplikace Azure AD pro úplnost.

Pokud vaše organizace ještě nemá k dispozici registraci aplikace Azure Active Directory (Azure AD), která se má použít jako instanční objekt, vytvořte ho podle těchto pokynů.

1. Otevřete okno [Registrace aplikací](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview) a klikněte na **+ Nová registrace**.
2. V podokně **Registrovat aplikaci** zadejte název vaší registrace aplikace.
3. Ujistěte se, že v části **podporované typy účtů** jsou **jenom účty v tomto organizačním adresáři** . Toto je nejbezpečnější volba.
4. Identifikátor URI přesměrování přidáme později, až budeme znát identifikátor URI clusteru. Kliknutím na tlačítko **registr** vytvoříte registraci aplikace služby Azure AD.
5. Na stránce, která se zobrazí, zkopírujte **ID aplikace (klienta)**. Tato hodnota se na tuto hodnotu odkazuje jako `APPID` v kurzu [Vytvoření clusteru Azure Red Hat OpenShift](tutorial-create-cluster.md) .

![Snímek stránky objektu aplikace](./media/howto-create-tenant/get-app-id.png)

### <a name="create-a-client-secret"></a>Vytvoření tajného klíče klienta

Vygenerujte tajný klíč klienta pro ověření vaší aplikace k Azure Active Directory.

1. V části **Spravovat** na stránce registrace aplikací klikněte na **certifikáty & tajných** kódů.
2. V podokně **certifikáty & tajných klíčů** klikněte na **+ nový tajný klíč klienta**.  Zobrazí se podokno **Přidat tajný klíč klienta** .
3. Zadejte **Popis**.
4. Nastavte **konec platnosti** na dobu, kterou dáváte přednost, například **2 roky**.
5. Klikněte na tlačítko **Přidat** a hodnota klíče se zobrazí v části **tajné klíče klienta** stránky.
6. Zkopírujte hodnotu klíče. Tato hodnota se na tuto hodnotu odkazuje jako `SECRET` v kurzu [Vytvoření clusteru Azure Red Hat OpenShift](tutorial-create-cluster.md) .

![Snímek obrazovky s podoknem certifikátů a tajných klíčů](./media/howto-create-tenant/create-key.png)

Další informace o objektech aplikací Azure najdete [v tématu aplikace a objekty zabezpečení služby v Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md).

Podrobnosti o vytvoření nové aplikace Azure AD najdete v tématu [Registrace aplikace s koncovým bodem Azure Active Directory v 1.0](../active-directory/develop/quickstart-register-app.md).

## <a name="add-api-permissions"></a>Přidání oprávnění rozhraní API

[//]: # (Neměňte Microsoft Graph. Nefunguje s Microsoft Graph.)
1. V části **Spravovat** klikněte na **oprávnění rozhraní API** .
2. Klikněte na **Přidat oprávnění** a vyberte **Azure Active Directory Graph** a pak **delegovaná oprávnění**.
> [!NOTE]
> Ujistěte se, že jste vybrali "Azure Active Directory Graph", a ne dlaždici "Microsoft Graph".

3. V níže uvedeném seznamu rozbalte položku **uživatel** a povolte oprávnění **uživatel. číst** . Pokud je ve výchozím nastavení povolená možnost **User. Read** , ujistěte se, že se jedná o oprávnění **Azure Active Directory Graph** **User. Read**.
4. Přejděte nahoru a vyberte **oprávnění aplikace**.
5. Rozbalte položku **adresář** v následujícím seznamu a povolte **adresář. ReadAll**.
6. Kliknutím na tlačítko **Přidat oprávnění** přijměte změny.
7. Panel oprávnění rozhraní API by teď měl zobrazovat *User. Read* a *Directory. ReadAll*. Všimněte si prosím upozornění ve sloupci **požadováno souhlasu správce** vedle *Directory. ReadAll*.
8. Pokud jste *správcem předplatného Azure*, klikněte níže na **udělit souhlas správce pro *název předplatného*** . Pokud nejste *správcem předplatného Azure*, požádejte o souhlas správce.

![Snímek obrazovky s panelem oprávnění rozhraní API Přidaná oprávnění User. Read a Directory. ReadAll, pro Directory. ReadAll se vyžaduje souhlas správce.](./media/howto-aad-app-configuration/permissions-required.png)

> [!IMPORTANT]
> Synchronizace skupiny správců clusteru bude fungovat až po udělení souhlasu. V sloupci *požadováno souhlasu správce* se zobrazí zelený kroužek se značkami zaškrtnutí a zpráva "uděleno pro *název předplatného*".

Podrobnosti o správě správců a dalších rolí najdete v tématu [Přidání nebo změna správců předplatného Azure](../cost-management-billing/manage/add-change-subscription-administrator.md).

## <a name="resources"></a>Zdroje informací

* [Aplikace a instanční objekty služby v Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md)
* [Rychlý start: Registrace aplikace pomocí koncového bodu Azure Active Directory verze 1.0](../active-directory/develop/quickstart-register-app.md)

## <a name="next-steps"></a>Další kroky

Pokud jste splnili všechny [požadavky na Azure Red Hat OpenShift](howto-setup-environment.md), budete připraveni vytvořit svůj první cluster.

Vyzkoušejte si kurz:
> [!div class="nextstepaction"]
> [Vytvoření clusteru Azure Red Hat OpenShift](tutorial-create-cluster.md)