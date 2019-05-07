---
title: Vytvoření registrace aplikace Azure AD a uživatele pro Azure Red Hat OpenShift | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit instanční objekt služby, generovat adresu URL pro zpětné volání tajný klíč a ověřování klienta a vytvořit nového uživatele služby Active Directory pro účely testování aplikací v Microsoft Azure Red Hat OpenShift clusteru.
author: tylermsft
ms.author: twhitney
ms.service: openshift
manager: jeconnoc
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/06/2019
ms.openlocfilehash: de3f3c30848d26ea399bcccc29a6149a149f6a55
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65078517"
---
# <a name="create-an-azure-ad-app-registration-and-user-for-azure-red-hat-openshift"></a>Vytvoření registrace aplikace Azure AD a uživatele pro Azure Red Hat OpenShift

Microsoft Azure Red Hat OpenShift potřebuje oprávnění k provádění úkolů v zastoupení vašeho clusteru. Pokud vaše organizace ještě nemá registrace aplikace Azure Active Directory (Azure AD) pro použití jako instanční objekt služby, postupujte podle těchto pokynů a vytvořte si ho.

Toto téma končí pokyny pro vytvoření nového uživatele Azure AD, kterou budete potřebovat pro přístup k aplikacím, které běží na clusteru Azure Red Hat OpenShift.

Pokud jste ještě nevytvořili tenanta služby Azure AD, postupujte podle pokynů v [vytvoření tenanta Azure AD pro Azure Red Hat OpenShift](howto-create-tenant.md) než budete pokračovat s využitím těchto pokynů.

## <a name="create-a-new-app-registration"></a>Vytvoření registrace nové aplikace

Aplikace, který chce využívat možnosti služby Azure AD musí být nejprve registrována v tenantovi Azure AD. Součástí tohoto procesu registrace je poskytuje Azure AD podrobnosti o vaší aplikaci, jako je například adresa URL, kde se nachází aplikace, adresa URL pro odeslání odpovědi po ověření uživatele, identifikátor URI, který identifikuje aplikaci a tak dále.

1. V [webu Azure portal](https://portal.azure.com), ujistěte se, že váš tenant se zobrazí v části své uživatelské jméno v horním pravém rohu portálu:

    ![Snímek obrazovky portálu s tenantem uvedené v pravém horním rohu][tenantcallout] Pokud nesprávného tenanta se zobrazí, klikněte na své uživatelské jméno v pravém horním rohu a pak klikněte na tlačítko **přepnout adresář**a vyberte správné tenanta z **všechny Adresáře** seznamu.

2. Otevřít [okně registrace aplikace](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) a klikněte na tlačítko **registrace nové aplikace**.
3. V **vytvořit** podokně zadejte popisný název pro objekt vaší aplikace.
4. Ujistěte se, že **typ aplikace** je nastavena na *webové aplikace nebo rozhraní API*.
5. Vytvoření **přihlašovací adresa URL** pomocí následujícího vzorce:

    ```
    https://<cluster-name>.<azure-region>.cloudapp.azure.com
    ```

    . . . kde `<cluster-name>` je určený název vašeho clusteru Azure Red Hat OpenShift a `<azure-region>` je [oblast Azure, který je hostitelem vašeho clusteru Azure Red Hat OpenShift](supported-resources.md#azure-regions). Například, pokud se název clusteru `contoso`, a vytvoříte ho `eastus` oblasti, plně kvalifikovaný název domény (FQDN), který zadáte pro **přihlašovací adresa URL** by `https://contoso.eastus.cloudapp.azure.com`

    > [!IMPORTANT]
    > Název clusteru musí být malými písmeny a plně kvalifikovaný název domény adresy URL musí být jedinečný.
    > Ujistěte se, že zvolíte dostatečně odlišný název pro váš cluster.

    Poznamenejte si název clusteru a adresa URL přihlašování, budete je budete potřebovat později přístup k aplikacím, které běží na clusteru. Jsme bude odkazovat na název clusteru jako `CLUSTER_NAME`a tento přihlašovací adresu URL jako `FQDN` v [vytvoření clusteru Azure Red Hat OpenShift](tutorial-create-cluster.md) kurzu.

6. Zkontrolujte vaše **přihlašovací adresa URL** hodnota se ověřuje pomocí zelená značka zaškrtnutí. (Stiskněte klávesu Tab k fokus z celkového počtu *přihlašovací adresa URL* pole a spusťte ověření.)
7. Klikněte na tlačítko **vytvořit** tlačítko vytvoříte objekt aplikace Azure AD.
8. Na **registrované aplikace** stránku, která se zobrazí, poznamenejte **ID aplikace**. Společnost Microsoft bude odkazovat na tuto hodnotu jako `APPID` v [vytvoření clusteru Azure Red Hat OpenShift](tutorial-create-cluster.md) kurzu.

    ![Snímek obrazovky s textovým polem ID aplikace][appidimage]
    
Další informace o objekty aplikací Azure, najdete v části [aplikace a instanční objekty v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals).

Podrobnosti o vytvoření nové aplikace Azure AD, najdete v článku [registrace aplikace ke koncovému bodu Azure Active Directory verze 1.0](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app).

### <a name="create-a-client-secret"></a>Vytvoření tajného klíče klienta

Nyní jste připraveni vygenerovat tajný kód klienta pro ověřování vaší aplikace do Azure Active Directory.

1. Z **registrované aplikace** stránky, klikněte na **nastavení** otevřete nastavení pro registrovaná aplikace.
2. Na **nastavení** podokno, které se zobrazí, klikněte na tlačítko **klíče**.  **Klíče** otevře se podokno.
![Snímek obrazovky vytvoření stránky klíče na portálu][createkeyimage]
3. Zadejte klíč **popis**.
4. Nastavit hodnotu pro **Expires**, například *2 roky*.
5. Klikněte na tlačítko **Uložit** a zobrazí se hodnota klíče.
6. Zkopírujte si hodnotu klíče. Společnost Microsoft bude odkazovat na tuto hodnotu jako `SECRET` v [vytvoření clusteru Azure Red Hat OpenShift](tutorial-create-cluster.md) kurzu.

### <a name="create-a-reply-url"></a>Vytvořit adresu URL odpovědi

Azure AD používá objekt aplikace *adresy URL odpovědi* k určení zpětného volání při ověřování aplikace. V případě webového rozhraní API nebo webovou aplikaci je adresa URL odpovědi umístění, kam Azure AD pošle odpověď ověřování, včetně token, pokud je ověření proběhlo úspěšně.

I nejmenší neshoda (koncové lomítko chybí, jinou velikostí písmen) způsobí selhání operace vystavení tokenu a nebudete moct přihlásit.

1. Přejděte zpět **nastavení** podokně (můžete kliknout na **nastavení** v navigace s popisem cesty v horní části portálu) a klikněte na tlačítko **adresy URL odpovědí** na pravé straně.  **Adresy URL odpovědí** otevře se podokno.
2. První adresa URL odpovědi v seznamu bude `FQDN` hodnotu z kroku 6 v [vytvořit registrace nové aplikace](#create-a-new-app-registration). Upravit a přidat `/oauth2callback/Azure%20AD`.  Například vaše adresa URL odpovědi by měl nyní vypadat podobně jako `https://mycluster.eastus.cloudapp.azure.com/oauth2callback/Azure%20AD`
3. Klikněte na tlačítko **Uložit** uložte příslušnou odpovědní adresu URL.

## <a name="create-a-new-active-directory-user"></a>Vytvoření nového uživatele služby Active Directory

Vytvoření nového uživatele ve službě Active Directory používat k přihlášení k aplikaci spuštěnou v clusteru Azure Red Hat OpenShift.

1. Přejděte [uživatelé – všichni uživatelé](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers) okno.
2. Klikněte na tlačítko **+ nový uživatel**. **Uživatele** otevře se podokno.
3. Zadejte **název** , která chcete pro tohoto uživatele.
4. Vytvoření **uživatelské jméno** vycházet z názvu tenanta, který jste vytvořili pomocí `.onmicrosoft.com` přidán na konec. Například, `yourUserName@yourTenantName.onmicrosoft.com`. Poznamenejte si toto uživatelské jméno. Budete potřebovat k přihlášení k používání aplikace ve vašem clusteru.
5. Klikněte na tlačítko **role adresáře** a vyberte **globálního správce** a potom klikněte na tlačítko **Ok** v dolní části podokna.
6. Uprostřed **uživatele** podokně klikněte na tlačítko **zobrazit heslo** a dočasné heslo si poznamenejte. Po přihlášení poprvé, budete vyzváni k jejímu resetování.
7. V dolní části podokna klikněte na tlačítko **vytvořit** pro vytvoření uživatele.

## <a name="resources"></a>Zdroje a prostředky

* [Aplikace a instanční objekty v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)  
* [Rychlé zprovoznění: Registrace aplikace ke koncovému bodu Azure Active Directory verze 1.0](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app)  

[appidimage]: ./media/howto-create-tenant/get-app-id.png
[createkeyimage]: ./media/howto-create-tenant/create-key.png
[tenantcallout]: ./media/howto-create-tenant/tenant-callout.png

## <a name="next-steps"></a>Další postup

Pokud jste splnili veškeré [požadavky Azure Red Hat OpenShift](howto-setup-environment.md), jste připraveni vytvořit svůj první cluster!

Projděte si kurz:
> [!div class="nextstepaction"]
> [Vytvoření clusteru Azure Red Hat OpenShift](tutorial-create-cluster.md)
