---
title: Integrace s Azure Active Directory pro Azure Red Hat OpenShift | Dokumentace Microsoftu
description: Informace o vytvoření skupiny zabezpečení Azure AD a uživatele pro testování aplikací v Microsoft Azure Red Hat OpenShift clusteru.
author: jimzim
ms.author: jzim
ms.service: openshift
manager: jeconnoc
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/13/2019
ms.openlocfilehash: adc5a601a04936a376d7c69b26c2429940ebdf6e
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/29/2019
ms.locfileid: "66306471"
---
# <a name="azure-active-directory-integration-for-azure-red-hat-openshift"></a>Integrace s Azure Active Directory pro Azure Red Hat OpenShift

Pokud jste ještě nevytvořili tenanta služby Azure Active Directory (Azure AD), postupujte podle pokynů v [vytvoření tenanta Azure AD pro Azure Red Hat OpenShift](howto-create-tenant.md) než budete pokračovat s využitím těchto pokynů.

Microsoft Azure Red Hat OpenShift potřebuje oprávnění k provádění úkolů v zastoupení vašeho clusteru. Pokud vaše organizace ještě nemá uživatele služby Azure AD, skupiny zabezpečení Azure AD nebo registrace aplikace Azure AD pro použití jako instanční objekt, postupujte podle těchto pokynů a vytvořte je.

## <a name="create-a-new-azure-active-directory-user"></a>Vytvoření nového uživatele Azure Active Directory

V [webu Azure portal](https://portal.azure.com), ujistěte se, že váš tenant se zobrazí v části své uživatelské jméno v horním pravém rohu portálu:

![Snímek obrazovky portálu s tenantem uvedené v pravém horním rohu](./media/howto-create-tenant/tenant-callout.png) Pokud nesprávného tenanta se zobrazí, klikněte na své uživatelské jméno v pravém horním rohu a pak klikněte na tlačítko **přepnout adresář**a vyberte správné tenanta z **všechny Adresáře** seznamu.

Vytvoření nového uživatele globální správce Azure Active Directory pro přihlášení ke clusteru Azure Red Hat OpenShift.

1. Přejděte [uživatelé všechny uživatele](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers) okno.
2. Klikněte na tlačítko **+ nový uživatel** otevřít **uživatele** podokně.
3. Zadejte **název** pro tohoto uživatele.
4. Vytvoření **uživatelské jméno** vycházet z názvu tenanta vytvoříte, s `.onmicrosoft.com` přidán na konec. Například, `yourUserName@yourTenantName.onmicrosoft.com`. Poznamenejte si toto uživatelské jméno. Budete potřebovat k přihlášení ke clusteru.
5. Klikněte na tlačítko **role adresáře** otevřete v podokně role adresáře a vybrat **globálního správce** a potom klikněte na tlačítko **Ok** v dolní části podokna.
6. V **uživatele** podokně klikněte na tlačítko **zobrazit heslo** a dočasné heslo si poznamenejte. Po přihlášení poprvé, budete vyzváni k jejímu resetování.
7. V dolní části podokna klikněte na tlačítko **vytvořit** pro vytvoření uživatele.

## <a name="create-an-azure-ad-security-group"></a>Vytvoření skupiny zabezpečení služby Azure AD

Pokud chcete udělit přístup pro správu clusteru, členství ve skupině zabezpečení Azure AD synchronizuje OpenShift skupiny "osa--správci zákazníka". Pokud není zadán, budou mít uživatelé přístup správce bez clusteru.

1. Otevřít [skupin Azure Active Directory](https://portal.azure.com/#blade/Microsoft_AAD_IAM/GroupsManagementMenuBlade/AllGroups) okno.
2. Klikněte na tlačítko **+ nová skupina**
3. Zadejte název skupiny a popis.
4. Nastavte **typ skupiny** k **zabezpečení**.
5. Nastavte **typ členství** k **přiřazeno**.

    Přidáte uživatele Azure AD, kterou jste vytvořili v předchozím kroku do této skupiny zabezpečení.

6. Klikněte na tlačítko **členy** otevřít **výběr členů** podokně.
7. V seznamu členů vyberte uživatele Azure AD, který jste vytvořili výše.
8. V dolní části portálu klikněte na **vyberte** a potom **vytvořit** vytvořte skupinu zabezpečení.

    Poznamenejte si hodnotu ID skupiny

9. Když se skupině, uvidíte seznam všech skupin. Klikněte na novou skupinu.
10. Na stránce, která se zobrazí, poznamenejte **ID objektu**. Společnost Microsoft bude odkazovat na tuto hodnotu jako `GROUPID` v [vytvoření clusteru Azure Red Hat OpenShift](tutorial-create-cluster.md) kurzu.

## <a name="create-an-azure-ad-app-registration"></a>Vytvoření registrace aplikace Azure AD

Můžete automaticky vytvoření klienta registrace aplikace Azure Active Directory (Azure AD) jako součást vytváření clusteru vynecháním `--aad-client-app-id` příznak, který `az openshift create` příkazu. V tomto kurzu se dozvíte, jak vytvořit registrace aplikace Azure AD pro úplnost.

Pokud vaše organizace ještě nemá registrace aplikace Azure Active Directory (Azure AD) pro použití jako objekt služby, postupujte podle těchto pokynů a vytvořte si ho.

1. Otevřít [okně registrace aplikace](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview) a klikněte na tlačítko **+ registrace nové**.
2. V **zaregistrovat aplikaci** podokně, zadejte název pro registraci vaší aplikace.
3. Ujistěte se, že v části **podporovaných typů účtu** , který **účty v tomto adresáři organizace jenom** zaškrtnuto. Toto je nejbezpečnější možnost.
4. Přidáme na identifikátor URI přesměrování vyšší Jakmile víme URI clusteru. Klikněte na tlačítko **zaregistrovat** pro vytvoření registrace aplikace Azure AD.
5. Na stránce, která se zobrazí, poznamenejte **ID aplikace (klient)** . Společnost Microsoft bude odkazovat na tuto hodnotu jako `APPID` v [vytvoření clusteru Azure Red Hat OpenShift](tutorial-create-cluster.md) kurzu.

![Snímek obrazovky stránky objektu aplikace](./media/howto-create-tenant/get-app-id.png)

### <a name="create-a-client-secret"></a>Vytvoření tajného klíče klienta

Vygenerujte tajný kód klienta pro ověřování vaší aplikace do Azure Active Directory.

1. V **spravovat** části na stránce registrace aplikace, klikněte na tlačítko **certifikáty a tajné kódy**.
2. Na **certifikáty a tajné kódy** podokně klikněte na tlačítko **+ nový tajný kód klienta**.  **Přidat tajný klíč klienta** otevře se podokno.
3. Zadejte **popis**.
4. Nastavte **Expires** doby trvání, dáváte přednost, například **2 roky**.
5. Klikněte na tlačítko **přidat** a hodnota klíče, která se zobrazí v **tajné klíče klienta** části stránky.
6. Zkopírujte si hodnotu klíče. Společnost Microsoft bude odkazovat na tuto hodnotu jako `SECRET` v [vytvoření clusteru Azure Red Hat OpenShift](tutorial-create-cluster.md) kurzu.
 
![Snímek obrazovky podokna certifikátů a tajných kódů](./media/howto-create-tenant/create-key.png)
 
Další informace o objekty aplikací Azure, najdete v části [aplikace a instanční objekty v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals).

Podrobnosti o vytvoření nové aplikace Azure AD, najdete v článku [registrace aplikace ke koncovému bodu Azure Active Directory verze 1.0](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app).

## <a name="resources"></a>Zdroje a prostředky

* [Aplikace a instanční objekty v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)  
* [Rychlé zprovoznění: Registrace aplikace ke koncovému bodu Azure Active Directory verze 1.0](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app)  

## <a name="next-steps"></a>Další postup

Pokud jste splnili veškeré [požadavky Azure Red Hat OpenShift](howto-setup-environment.md), jste připraveni vytvořit svůj první cluster!

Projděte si kurz:
> [!div class="nextstepaction"]
> [Vytvoření clusteru Azure Red Hat OpenShift](tutorial-create-cluster.md)