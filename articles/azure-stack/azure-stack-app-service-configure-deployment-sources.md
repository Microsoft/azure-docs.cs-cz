---
title: Konfigurace zdrojů nasazení pro App Services ve službě Azure Stack | Dokumentace Microsoftu
description: Jak může správce služby konfigurace zdrojů nasazení (Git, GitHub, BitBucket, DropBox a Onedrivu) pro službu App Services ve službě Azure Stack
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2018
ms.author: sethm
ms.reviewer: anwestg
ms.openlocfilehash: d65c8653bc039b591f1c0fb711dfe68e3fbacd88
ms.sourcegitcommit: 8e06d67ea248340a83341f920881092fd2a4163c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2018
ms.locfileid: "49353557"
---
# <a name="configure-deployment-sources"></a>Konfigurace zdrojů nasazení

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

App Service ve službě Azure Stack podporuje nasazení na vyžádání z více poskytovatelé řízení zdrojů. Tato funkce umožňuje vývojářům aplikací pro nasazení s přímým přístupem z jejich úložiště správy zdrojového kódu. Pokud uživatelé chtějí konfigurace služby App Service pro připojení k jejich úložiště, operátor cloudu napřed nakonfigurovat integraci služby App Service ve službě Azure Stack a poskytovatel správy zdrojových kódů.  

Kromě místního Gitu jsou podporovány následující poskytovatelé řízení zdrojů:

* GitHubu
* Bitbucketu
* OneDrive
* DropBox

## <a name="view-deployment-sources-in-app-service-administration"></a>Zobrazení zdroje nasazení správy služby App Service

1. Přihlaste se k portálu pro správu Azure Stack (https://adminportal.local.azurestack.external) jako správce služeb.
2. Přejděte do **poskytovatelů prostředků** a vyberte **správce poskytovatele prostředků služby App**.  ![Správce poskytovatele prostředků App Service][1]
3. Klikněte na tlačítko **konfigurace správy zdrojového kódu**. Zobrazí se seznam všech zdrojů na nasazení.
    ![Konfigurace ovládacích prvků správce zdroj poskytovatele prostředků App Service][2]

## <a name="configure-github"></a>Konfigurace Githubu

Musíte mít účet GitHub a dokončete tuto úlohu. Můžete chtít použít účet pro vaše organizace, ne osobní účet.

1. Přihlaste se k GitHub, přejděte do https://www.github.com/settings/developersa potom klikněte na tlačítko **registrace nové aplikace**.
    ![GitHub - registrace nové aplikace][3]
2. Zadejte **název_aplikace**; například **služby App Service ve službě Azure Stack**.
3. Zadejte **adresa URL domovské stránky**. Adresa URL domovské stránky musí být adresa portálu Azure Stack. Například, https://portal.local.azurestack.external.
4. Zadejte **popis aplikace**.
5. Zadejte **adresu URL zpětného volání autorizace**. Ve výchozím nasazení Azure Stack, adresa URL je ve formě https://portal.local.azurestack.external/TokenAuthorize. Pokud jsou spuštěny v jiné doméně, nahraďte název domény pro local.azurestack.external.
6. Klikněte na tlačítko **zaregistrovat aplikaci**. Na stránce se zobrazí výpis **ID klienta** a **tajný kód klienta** pro aplikaci.
    ![GitHub - registrace dokončené aplikace][5]
7.  V nové záložce prohlížeče nebo okno, přihlaste se k portálu pro správu služby Azure Stack (https://adminportal.local.azurestack.external) jako správce služeb.
8.  Přejděte do **poskytovatelů prostředků**a vyberte **správce poskytovatele prostředků služby App**.
9. Klikněte na tlačítko **konfigurace správy zdrojového kódu**.
10. Zkopírujte a vložte **ID klienta** a **tajný kód klienta** do odpovídající vstupní pole pro GitHub.
11. Klikněte na **Uložit**.

## <a name="configure-bitbucket"></a>Konfigurace Bitbucketu

Musíte mít účet BitBucket a dokončete tuto úlohu. Můžete chtít použít účet pro vaše organizace, ne osobní účet.

1. Přihlaste se k BitBucket a přejděte do **integrace** v rámci vašeho účtu.
    ![Řídicí panel BitBucket – integrace][7]
2. Klikněte na tlačítko **OAuth** pod správu přístupu a **přidat příjemce**.
    ![Přidat příjemce OAuth Bitbucketu][8]
3. Zadejte **název** pro spotřebitele; například **služby App Service ve službě Azure Stack**.
4. Zadejte **popis** pro aplikaci.
5. Zadejte **adresu URL zpětného volání**. Ve výchozím nasazení Azure Stack je zpětné volání adresy URL ve formě https://portal.local.azurestack.external/TokenAuthorize. Pokud jsou spuštěny v jiné doméně, nahraďte název domény pro azurestack.local. BitBucket integrace úspěšně adresa URL musí následovat malá a velká písmena zde uvedené.
6. Zadejte **URL**. Tato adresa URL by měla být na portálu Azure Stack URL; například https://portal.local.azurestack.external.
7. Vyberte **oprávnění** vyžaduje:
    - **Úložiště**: *pro čtení*
    - **Webhooky**: *čtení a zápis*
8. Klikněte na **Uložit**. Tato nová aplikace, uvidíte spolu s **klíč** a **tajný kód**v části **OAuth příjemci**.
    ![Aplikace výpisu Bitbucketu][9]
9.  V nové záložce prohlížeče nebo okno, přihlaste se k portálu pro správu služby Azure Stack (https://adminportal.local.azurestack.external) jako správce služeb.
10.  Přejděte do **poskytovatelů prostředků** a vyberte **správce poskytovatele prostředků služby App**.
11. Klikněte na tlačítko **konfigurace správy zdrojového kódu**.
12. Zkopírujte a vložte **klíč** do **ID klienta** vstupního pole a **tajný kód** do **tajný kód klienta** vstupního pole pro Bitbucketu.
13. Klikněte na **Uložit**.

## <a name="configure-onedrive"></a>Konfiguraci služby OneDrive

Musíte mít Account Microsoft propojený s účtem Onedrivu a dokončete tuto úlohu.  Můžete chtít použít účet pro vaše organizace, ne osobní účet.

> [!NOTE]
> OneDrive pro firmy se momentálně nepodporují.

1. Přejděte do https://apps.dev.microsoft.com/?referrer=https%3A%2F%2Fdev.onedrive.com%2Fapp-registration.htm a přihlaste se pomocí Account Microsoft.
2. V části **Moje aplikace**, klikněte na tlačítko **přidat aplikaci**.
![Aplikace OneDrive][10]
3. Zadejte **název** pro registrace nové aplikace: Zadejte **služby App Service ve službě Azure Stack**a potom klikněte na tlačítko **vytvořením aplikace**
4. Na další obrazovce jsou uvedeny vlastnosti objektu novou aplikaci. Uložit **ID aplikace** do dočasného umístění.
![Vlastnosti aplikace OneDrive][11]
5. V části **tajných klíčů aplikací**, klikněte na tlačítko **generovat nové heslo**. Poznamenejte si, **vygenerovalo se nové heslo**. Toto je váš tajný klíč aplikace a není retrievable, po kliknutí na **OK**.
6. V části **platformy**, klikněte na tlačítko **přidat platformy**a pak vyberte **webové**.
7. Zadejte **identifikátor URI pro přesměrování**. Ve výchozím nasazení Azure Stack, je identifikátor URI pro přesměrování ve formě https://portal.local.azurestack.external/TokenAuthorize. Pokud jsou spuštěny v jiné doméně, nahraďte název domény pro azurestack.local.
![Aplikace OneDrive - přidat webovou platformu][12]
8. Přidat **oprávnění Microsoft Graphu** - **delegovaná oprávnění**
    - **Files.ReadWrite.AppFolder**
    - **User.Read**  
      ![Aplikace OneDrive - oprávnění ke Graphu][13]
9. Klikněte na **Uložit**.
10.  V nové záložce prohlížeče nebo okno, přihlaste se k portálu pro správu služby Azure Stack (https://adminportal.local.azurestack.external) jako správce služeb.
11.  Přejděte do **poskytovatelů prostředků** a vyberte **správce poskytovatele prostředků služby App**.
12. Klikněte na tlačítko **konfigurace správy zdrojového kódu**.
13. Zkopírujte a vložte **ID aplikace** do **ID klienta** vstupního pole a **heslo** do **tajný kód klienta** vstupního pole pro OneDrive.
14. Klikněte na **Uložit**.

## <a name="configure-dropbox"></a>Konfigurace Dropboxu

> [!NOTE]
> Musíte mít účet DropBox a dokončete tuto úlohu. Můžete chtít použít účet pro vaše organizace, ne osobní účet.

1. Přejděte do https://www.dropbox.com/developers/apps a přihlaste se pomocí přihlašovacích údajů k účtu DropBox.
2. Klikněte na tlačítko **vytvoření aplikace**.

    ![Dropbox aplikací][14]

3. Vyberte **DropBox API**.
4. Nastavte přístup na úrovni **složky aplikace**.
5. Zadejte **název** pro vaši aplikaci.
![Registrace aplikace v Dropboxu][15]
6. Klikněte na tlačítko **vytvoření aplikace**. Zobrazí se stránka seznam nastavení pro aplikaci, včetně **klíče aplikace** a **tajný kód aplikace**.
7. Ujistěte se, že **název složky aplikace** je nastavena na **služby App Service ve službě Azure Stack**.
8. Nastavte **URI přesměrování OAuth 2** a potom klikněte na tlačítko **přidat**. Ve výchozím nasazení Azure Stack, je identifikátor URI pro přesměrování ve formě https://portal.local.azurestack.external/TokenAuthorize. Pokud jsou spuštěny v jiné doméně, nahraďte doménou pro azurestack.local.
![Konfigurace aplikace Dropboxu][16]
9.  V nové záložce prohlížeče nebo okno, přihlaste se k portálu pro správu služby Azure Stack (https://adminportal.local.azurestack.external) jako správce služeb.
10.  Přejděte do **poskytovatelů prostředků** a vyberte **správce poskytovatele prostředků služby App**.
11. Klikněte na tlačítko **konfigurace správy zdrojového kódu**.
12. Zkopírujte a vložte **klíč aplikace** do **ID klienta** vstupního pole a **tajný kód aplikace** do **tajný kód klienta** vstupního pole pro DropBox.
13. Klikněte na **Uložit**.

## <a name="next-steps"></a>Další postup

Uživatelé teď můžou používat zdrojů nasazení, jako třeba věci [průběžné nasazování](https://docs.microsoft.com/azure/app-service-web/app-service-continuous-deployment), [místní nasazení přes Git](https://docs.microsoft.com/azure/app-service-web/app-service-deploy-local-git), a [cloudové synchronizace složek](https://docs.microsoft.com/azure/app-service-web/app-service-deploy-content-sync).

<!--Image references-->
[1]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin.png
[2]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-source-control-configuration.png
[3]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-github-developer-applications.png
[4]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-github-register-a-new-oauth-application-populated.png
[5]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-github-register-a-new-oauth-application-complete.png
[6]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-roles-management-server-repair-all.png
[7]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-bitbucket-dashboard.png
[8]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-bitbucket-access-management-add-oauth-consumer.png
[9]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-bitbucket-access-management-add-oauth-consumer-complete.png
[10]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Onedrive-applications.png
[11]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Onedrive-application-registration.png
[12]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Onedrive-application-platform.png
[13]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Onedrive-application-graph-permissions.png
[14]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Dropbox-applications.png
[15]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Dropbox-application-registration.png
[16]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Dropbox-application-configuration.png
