---
title: Vytvoření aplikace Azure AD v Průzkumníku dat Azure
description: Zjistěte, jak vytvořit aplikaci Azure AD v Průzkumníku dat Azure.
author: orspod
ms.author: orspodek
ms.reviewer: herauch
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 64e8637630675120fece1bbe1fa4a57d97f36eb5
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80550510"
---
# <a name="create-an-azure-active-directory-application-registration-in-azure-data-explorer"></a>Vytvoření registrace aplikace Azure Active Directory v Průzkumníku dat Azure

Ověřování aplikací Azure Active Directory (Azure AD) se používá pro aplikace, jako je bezobslužná služba nebo naplánovaný tok, které potřebují přístup k Průzkumníku dat Azure bez přítomnosti uživatele. Pokud se připojujete k databázi Azure Data Explorer pomocí aplikace, jako je například webová aplikace, měli byste se ověřit pomocí ověřování instančního objektu. Tento článek podrobně popisuje, jak vytvořit a zaregistrovat instanční objekt služby Azure AD a pak ho autorizovat pro přístup k databázi Azure Data Explorer.

## <a name="create-azure-ad-application-registration"></a>Vytvoření registrace aplikace Azure AD

Ověřování aplikací Azure AD vyžaduje vytvoření a registraci aplikace pomocí Azure AD. Instanční objekt se automaticky vytvoří při vytvoření registrace aplikace v tenantovi Azure AD. 

1. Přihlášení na [portál Azure](https://portal.azure.com) `Azure Active Directory` a otevření okna

    ![Výběr služby Azure Active Directory z nabídky portálu](media/provision-azure-ad-app/create-app-reg-select-azure-active-directory.png)

1. Vyberte okno **Registrace aplikací** a vyberte **Nová registrace.**

    ![Zahájení registrace nové aplikace](media/provision-azure-ad-app/create-app-reg-new-registration.png)

1. Vyplňte následující informace: 

    * **Název** 
    * **Podporované typy účtu**
    * **Přesměrovat web URI** > **Web**
        > [!IMPORTANT] 
        > Typ aplikace by měl být **web**. Identifikátor URI je volitelný a v tomto případě je ponechán prázdný.
    * Vybrat **registr**

    ![Registrace nové aplikace](media/provision-azure-ad-app/create-app-reg-register-app.png)

1. Vyberte okno **Přehled** a zkopírujte **ID aplikace**.

    > [!NOTE]
    > Budete potřebovat ID aplikace k autorizaci instančního objektu pro přístup k databázi.

    ![Kopírovat ID registrace aplikace](media/provision-azure-ad-app/create-app-reg-copy-applicationid.png)

1. V okně **Certifikáty & tajných kódů** vyberte **nový tajný klíč klienta.**

    ![Zahájit vytváření tajného klíče klienta](media/provision-azure-ad-app/create-app-reg-new-client-secret.png)

    > [!TIP]
    > Tento článek popisuje použití tajného klíče klienta pro pověření aplikace.  K ověření aplikace můžete také použít certifikát X509. Vyberte **Nahrát certifikát** a podle pokynů nahrajte veřejnou část certifikátu.

1. Zadejte popis, vypršení platnosti a vyberte **Přidat.**

    ![Zadat tajné parametry klienta](media/provision-azure-ad-app/create-app-reg-enter-client-secret-details.png)

1. Zkopírujte si hodnotu klíče.

    > [!NOTE]
    > Když opustíte tuto stránku, hodnota klíče nebude přístupná.  Budete potřebovat klíč ke konfiguraci pověření klienta do databáze.

    ![Kopírovat hodnotu klíče tajného klíče klienta](media/provision-azure-ad-app/create-app-reg-copy-client-secret.png)

Aplikace je vytvořena. Pokud potřebujete jenom přístup k autorizovanému prostředku Průzkumníka dat Azure, například v programovém příkladu níže, přeskočte další část. Podpora delegovaných oprávnění naleznete v [tématu konfigurace delegovaných oprávnění pro registraci aplikace](#configure-delegated-permissions-for-the-application-registration).

## <a name="configure-delegated-permissions-for-the-application-registration"></a>Konfigurace delegovaných oprávnění pro registraci aplikace

Pokud vaše aplikace potřebuje přístup k Průzkumníku dat Azure pomocí přihlašovacích údajů volajícího uživatele, nakonfigurujte delegovaná oprávnění pro registraci aplikace. Například pokud vytváříte webové rozhraní API pro přístup k Průzkumníku dat Azure a chcete se ověřit pomocí přihlašovacích údajů uživatele, který *volá* vaše rozhraní API.  

1. V okně **Oprávnění rozhraní API** vyberte Přidat **oprávnění**.
1. Vyberte **možnost IAPI, která moje organizace používá**. Vyhledejte a vyberte **Azure Data Explorer**.

    ![Přidání oprávnění rozhraní API průzkumníka dat Azure](media/provision-azure-ad-app/configure-delegated-add-api-permission.png)

1. V **části Delegovaná oprávnění**vyberte pole **user_impersonation** a **Přidejte oprávnění.**

    ![Výběr delegovaných oprávnění s zosobněním uživatele](media/provision-azure-ad-app/configure-delegated-click-add-permissions.png)     

## <a name="grant-the-service-principal-access-to-an-azure-data-explorer-database"></a>Udělení přístupu k databázi Azure Data Exploreru jako instančního objektu

Teď, když je vytvořena registrace instančního objektu služby, musíte udělit odpovídající přístup k primárnímu serveru služeb k databázi Průzkumníka dat Azure. 

1. Ve [webovém uživatelském uživatelském nastavení](https://dataexplorer.azure.com/)se připojte k databázi a otevřete kartu dotazu.

1. Spusťte následující příkaz:

    ```kusto
    .add database <DatabaseName> viewers ('<ApplicationId>') '<Notes>'
    ```

    Například:
    
    ```kusto
    .add database Logs viewers ('aadapp=f778b387-ba15-437f-a69e-ed9c9225278b') 'Azure Data Explorer App Registration'
    ```

    Poslední parametr je řetězec, který se zobrazí jako poznámky při dotazování rolí přidružených k databázi.
    
    > [!NOTE]
    > Po vytvoření registrace aplikace může být několik minut zpoždění, dokud Azure Data Explorer můžete odkazovat. Pokud při provádění příkazu se zobrazí chyba, že aplikace nebyla nalezena, počkejte a zkuste to znovu.

Další informace naleznete v tématu [správa rolí zabezpečení](/azure/kusto/management/security-roles) a oprávnění k [ingestování](/azure/kusto/api/netfx/kusto-ingest-client-permissions.md).  

## <a name="using-application-credentials-to-access-a-database"></a>Použití pověření aplikace pro přístup k databázi

Pomocí přihlašovacích údajů aplikace můžete programově přistupovat k databázi pomocí [klientské knihovny Azure Data Explorer](/azure/kusto/api/netfx/about-kusto-data.md).

```C#
. . .
string applicationClientId = "<myClientID>";
string applicationKey = "<myApplicationKey>";
. . .
var kcsb = new KustoConnectionStringBuilder($"https://{clusterName}.kusto.windows.net/{databaseName}")
    .WithAadApplicationKeyAuthentication(
        applicationClientId,
        applicationKey,
        authority);
var client = KustoClientFactory.CreateCslQueryProvider(kcsb);
var queryResult = client.ExecuteQuery($"{query}");
```

   > [!NOTE]
   > Zadejte id aplikace a klíč registrace aplikace (instanční objekt) vytvořený dříve.

Další informace najdete v [tématu ověřování pomocí Azure AD pro přístup k Průzkumníku dat Azure](/azure/kusto/management/access-control/how-to-authenticate-with-aad) a [použití Azure Key Vault s .NET Core webové aplikace](/azure/key-vault/tutorial-net-create-vault-azure-web-app#create-a-net-core-web-app).

## <a name="troubleshooting"></a>Řešení potíží

### <a name="invalid-resource-error"></a>Neplatná chyba prostředku

Pokud se vaše aplikace používá k ověřování uživatelů nebo aplikací pro přístup k Průzkumníku dat Azure, musíte nastavit delegovaná oprávnění pro aplikaci služby Azure Data Explorer. Deklarujte, že vaše aplikace může ověřovat uživatele nebo aplikace pro přístup k Průzkumníku dat Azure. Pokud tak neučiníte, dojde při pokusu o ověření k chybě podobné následující:

`AADSTS650057: Invalid resource. The client has requested access to a resource which is not listed in the requested permissions in the client's application registration...`

Budete muset postupovat podle pokynů k [nastavení delegovaných oprávnění pro aplikaci služby Azure Data Explorer](#configure-delegated-permissions-for-the-application-registration).

### <a name="enable-user-consent-error"></a>Povolit chybu souhlasu uživatele

Správce klienta Azure AD může přijmout zásadu, která brání uživatelům klienta udělit souhlas s aplikacemi. Tato situace bude mít za následek chybu podobnou následující, když se uživatel pokusí přihlásit k vaší aplikaci:

`AADSTS65001: The user or administrator has not consented to use the application with ID '<App ID>' named 'App Name'`

Budete muset kontaktovat správce Azure AD udělit souhlas pro všechny uživatele v tenantovi nebo povolit souhlas uživatele pro konkrétní aplikaci.

## <a name="next-steps"></a>Další kroky

* Seznam [podporovaných připojovacích řetězců naleznete v připojovacích řetězcích Kusto.](/azure/kusto/api/connection-strings/kusto.md)
