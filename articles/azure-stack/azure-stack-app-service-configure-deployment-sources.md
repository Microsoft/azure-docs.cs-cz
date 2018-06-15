---
title: Konfigurace nasazení zdrojů pro aplikaci služby v Azure zásobníku | Microsoft Docs
description: Jak správce služeb můžete nakonfigurovat nasazení zdroje (Git, Githubu, BitBucket, DropBox a Onedrivu) pro službu App Service v Azure zásobníku
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: brenduns
ms.reviewer: anwestg
ms.openlocfilehash: 4ee333fcc50937679c4bc25b83c2d6aa389ba194
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
ms.locfileid: "34359590"
---
# <a name="configure-deployment-sources"></a>Konfigurace zdrojů nasazení
*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*


V zásobníku Azure App Service podporuje nasazení na vyžádání z více se poskytovatelé řízení zdrojů. Tato funkce umožňuje vývojáři aplikace nasadit přímý z jejich zdrojová ovládací prvek úložiště. Pokud chtějí uživatelé nakonfigurovat App Service připojit k jejich úložiště, musíte nejdřív operátor cloudu nakonfigurovat integraci mezi službou App Service v Azure zásobníku a poskytovatele správy zdrojového kódu.  

Kromě místní Git jsou podporovány následující poskytovatelé řízení zdrojů:

* GitHubu
* BitBucket
* OneDrive
* DropBox

## <a name="view-deployment-sources-in-app-service-administration"></a>Zobrazení nasazení zdroje v části Správa služby App Service

1. Přihlaste se k portálu pro správu Azure zásobníku (https://adminportal.local.azurestack.external) jako správce služeb.
2. Přejděte do **zprostředkovatelé prostředků** a vyberte **správce zprostředkovatele prostředků služby aplikace**.  ![Správce zprostředkovatele prostředků služby App Service][1]
3. Klikněte na tlačítko **zdroj konfigurace ovládacího prvku**.  Zde se zobrazí seznam všech zdrojů nasazení nakonfigurované.
    ![Konfigurace ovládacího prvku správce zdroj zprostředkovatele prostředků služby App Service][2]

## <a name="configure-github"></a>Konfigurace Githubu

Musíte mít účet GitHub pro dokončení této úlohy. Můžete chtít použít účet pro vaši organizaci, nikoli osobní účet.

1. Přihlaste se ke Githubu, přejděte na https://www.github.com/settings/developers a klikněte na tlačítko **zaregistrujte novou aplikaci**.
    ![Githubu – registrace nové aplikace][3]
2. Zadejte **název aplikace** například - App Service v Azure zásobníku.
3. Zadejte **URL domovské stránky**. Adresa URL domovské stránky musí být adresa zásobníku portálu Azure. Například, https://portal.local.azurestack.external.
4. Zadejte **popis aplikace**.
5. Zadejte **URL zpětného volání autorizace**.  Ve výchozím zásobníku Azure nasazení, adresa Url je ve formátu https://portal.local.azurestack.external/TokenAuthorize, pokud používáte v jiné doméně nenahrazuje doménu pro local.azurestack.external
6. Klikněte na tlačítko **registrace aplikace**.  Nyní, zobrazí se v seznamu stránky **ID klienta** a **tajný klíč klienta** pro aplikaci.
    ![Githubu – registrace hotová aplikace][5]
7.  V novou kartu prohlížeče nebo v okně Přihlaste se k portálu pro správu Azure zásobníku (https://adminportal.local.azurestack.external) jako správce služeb.
8.  Přejděte do **zprostředkovatelé prostředků** a vyberte **správce zprostředkovatele prostředků služby aplikace**.
9. Klikněte na tlačítko **zdroj konfigurace ovládacího prvku**.
10. Zkopírujte a vložte **ID klienta** a **tajný klíč klienta** do odpovídající vstup oknech pro GitHub.
11. Klikněte na **Uložit**.

## <a name="configure-bitbucket"></a>Konfigurace BitBucket

Musíte mít účet BitBucket pro dokončení této úlohy. Můžete chtít použít účet pro vaši organizaci, nikoli osobní účet.

1. Přihlaste se k BitBucket a přejděte do **integrace** v rámci vašeho účtu.
    ![Řídicí panel BitBucket – integrace][7]
2. Klikněte na tlačítko **OAuth** pod správu přístupu a **přidat příjemce**.
    ![BitBucket přidat příjemce OAuth][8]
3. Zadejte **název** pro příjemce, například služby App Service v Azure zásobníku.
4. Zadejte **popis** pro aplikaci.
5. Zadejte **adresu URL zpětné volání**.  Ve výchozím zásobníku Azure nasazení, je adresa Url zpětného volání ve formě https://portal.local.azurestack.external/TokenAuthorize, pokud používáte v jiné doméně nenahrazuje doménu pro azurestack.local.  Adresa Url musí následovat velká písmena, jsou uvedeny zde BitBucket Integrace proběhla úspěšně.
6. Zadejte **URL** – tato adresa Url by měla být například adresu URL portálu Azure zásobníku https://portal.local.azurestack.external.
7. Vyberte **oprávnění** vyžaduje:
    - **Úložiště**: *pro čtení*
    - **Webhooky**: *čtení a zápis*
8. Klikněte na **Uložit**.  Tato nová aplikace se nyní zobrazí spolu s **klíč** a **tajný klíč** pod **OAuth příjemci**.
    ![Výpis BitBucket aplikace][9]
9.  V novou kartu prohlížeče nebo v okně Přihlaste se k portálu pro správu Azure zásobníku (https://adminportal.local.azurestack.external) jako správce služeb.
10.  Přejděte do **zprostředkovatelé prostředků** a vyberte **správce zprostředkovatele prostředků služby aplikace**.
11. Klikněte na tlačítko **zdroj konfigurace ovládacího prvku**.
12. Zkopírujte a vložte **klíč** do **ID klienta** vstupní pole a **tajný klíč** do **tajný klíč klienta** vstupní pole pro BitBucket.
13. Klikněte na **Uložit**.


## <a name="configure-onedrive"></a>Konfigurace OneDrive

Musíte mít Account Microsoft propojený s účtem Onedrivu pro dokončení této úlohy.  Můžete chtít použít účet pro vaši organizaci, nikoli osobní účet.

> [!NOTE]
> OneDrive pro firmy nejsou aktuálně podporovány.

1. Přejděte do https://apps.dev.microsoft.com/?referrer=https%3A%2F%2Fdev.onedrive.com%2Fapp-registration.htm a přihlaste se pomocí Account Microsoft.
2. V části **Moje aplikace**, klikněte na tlačítko **přidat aplikaci**.
![Aplikace OneDrive][10]
3. Zadejte **název** nové registrace aplikace, zadejte **služby App Service v Azure zásobníku**a klikněte na tlačítko **vytvoření aplikace**
4. Na další obrazovce zobrazí vlastnosti novou aplikaci. Záznam **ID aplikace**.
![Vlastnosti aplikace OneDrive][11]
5. V části **tajné klíče aplikace**, klikněte na tlačítko **generovat nové heslo**. Poznamenejte si **nové heslo, které jsou generované**. Toto je váš tajný klíč aplikace a není po kliknutí na tlačítko získat **OK** v této fázi.
6. V části **platformy** klikněte na tlačítko **přidejte platformu** a vyberte **webové**.
7. Zadejte **identifikátor URI pro přesměrování**.  Ve výchozím zásobníku Azure nasazení, identifikátor URI pro přesměrování je ve formátu https://portal.local.azurestack.external/TokenAuthorize, pokud používáte v jiné doméně nenahrazuje doménu pro azurestack.local ![aplikace OneDrive – přidání webové platformy][12]
8. Přidat **Microsoft Graph oprávnění** - **delegovaná oprávnění**
    - **Files.ReadWrite.AppFolder**
    - **User.Read**  
      ![Aplikace OneDrive – oprávnění grafu][13]
9. Klikněte na **Uložit**.
10.  V novou kartu prohlížeče nebo v okně Přihlaste se k portálu pro správu Azure zásobníku (https://adminportal.local.azurestack.external) jako správce služeb.
11.  Přejděte do **zprostředkovatelé prostředků** a vyberte **správce zprostředkovatele prostředků služby aplikace**.
12. Klikněte na tlačítko **zdroj konfigurace ovládacího prvku**.
13. Zkopírujte a vložte **ID aplikace** do **ID klienta** vstupní pole a **heslo** do **tajný klíč klienta** vstupní pole pro OneDrive.
14. Klikněte na **Uložit**.

## <a name="configure-dropbox"></a>Konfigurace DropBox

> [!NOTE]
> Potřebujete mít účet DropBox pro dokončení této úlohy.  Můžete chtít použít účet pro vaši organizaci, nikoli osobní účet.

1. Přejděte do https://www.dropbox.com/developers/apps a přihlaste se pomocí účtu DropBox.
2. Klikněte na tlačítko **vytvořit aplikaci**.

    ![Dropbox aplikace][14]

3. Vyberte **DropBox rozhraní API**.
4. Nastavení přístupu na úrovni **složky aplikace**.
5. Zadejte **název** pro vaši aplikaci.
![Registrace aplikace dropbox][15]
6. Klikněte na tlačítko **vytvořit aplikaci**.  Nyní, zobrazí se stránka nastavení pro aplikace, včetně výpis **klíč aplikace** a **tajný klíč aplikace**.
7. Zkontrolujte **název složky aplikace** je nastaven na **služby App Service v Azure zásobníku**.
8. Nastavte **URI přesměrování OAuth 2** a klikněte na tlačítko **přidat**.  Ve výchozím zásobníku Azure nasazení, je identifikátor URI přesměrování ve formě https://portal.local.azurestack.external/TokenAuthorize, pokud používáte v jiné doméně nenahrazuje doménu pro azurestack.local.
![Konfigurace aplikace dropbox][16]
9.  V novou kartu prohlížeče nebo v okně Přihlaste se k portálu pro správu Azure zásobníku (https://adminportal.local.azurestack.external) jako správce služeb.
10.  Přejděte do **zprostředkovatelé prostředků** a vyberte **správce zprostředkovatele prostředků služby aplikace**.
11. Klikněte na tlačítko **zdroj konfigurace ovládacího prvku**.
12. Zkopírujte a vložte **klíč aplikace** do **ID klienta** vstupní pole a **tajný klíč aplikace** do **tajný klíč klienta** vstupní pole pro DropBox.
13. Klikněte na **Uložit**.


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

## <a name="next-steps"></a>Další postup

Uživatelé teď můžou používat zdroje nasazení, pro věcí [průběžné nasazování](https://docs.microsoft.com/azure/app-service-web/app-service-continuous-deployment), [místní nasazení Git](https://docs.microsoft.com/azure/app-service-web/app-service-deploy-local-git), a [cloudu složky synchronizační](https://docs.microsoft.com/azure/app-service-web/app-service-deploy-content-sync).
