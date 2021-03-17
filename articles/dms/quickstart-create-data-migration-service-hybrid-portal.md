---
title: 'Rychlý Start: vytvoření instance hybridního režimu pomocí Azure Portal'
titleSuffix: Azure Database Migration Service
description: K vytvoření instance Azure Database Migration Service v hybridním režimu použijte Azure Portal.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: quickstart
ms.date: 03/13/2020
ms.openlocfilehash: 39f893e9375970ff4bb6e3cfa2c93ceea48ec896
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2020
ms.locfileid: "92331800"
---
# <a name="quickstart-create-a-hybrid-mode-instance-with-azure-portal--azure-database-migration-service"></a>Rychlý Start: vytvoření instance hybridního režimu pomocí Azure Portal & Azure Database Migration Service

Azure Database Migration Service hybridní režim spravuje migrace databáze pomocí pracovního procesu migrace, který je hostovaný místně, spolu s instancí Azure Database Migration Service spuštěným v cloudu. Hybridní režim je zvláště užitečný pro scénáře, ve kterých se nachází mezi místními sítěmi a Azure nedostatečná konektivita typu Site-to-site, nebo pokud existuje omezená šířka pásma připojení typu Site-to-site.

>[!NOTE]
>V současné době Azure Database Migration Service spuštěná v hybridním režimu podporuje migrace SQL Server na:
>
>- Spravovaná instance Azure SQL s téměř nulovým výpadkem (online)
>- Azure SQL Database izolovanou databázi s nějakým výpadkem (offline).
>- MongoDb se do Azure CosmosDB s téměř nulovým výpadkem (online).
>- MongoDb se do Azure CosmosDB s nějakým výpadkem (offline).

V tomto rychlém startu použijete Azure Portal k vytvoření instance Azure Database Migration Service v hybridním režimu. Potom můžete stáhnout, nainstalovat a nastavit hybridní pracovní proces v místní síti. Během období Preview můžete k migraci dat z místní instance SQL Server na Azure SQL Database použít hybridní režim Azure Database Migration Service.

> [!NOTE]
> Azure Database Migration Service Hybrid Installer běží v systému Microsoft Windows Server 2012 R2, Windows Server 2016, Windows Server 2019 a Windows 10.

> [!IMPORTANT]
> Azure Database Migration Service Hybrid Installer vyžaduje rozhraní .NET 4.7.2 nebo novější. Nejnovější verze rozhraní .NET najdete na stránce [stažení .NET Framework](https://dotnet.microsoft.com/download/dotnet-framework) .

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Otevřete webový prohlížeč, přejděte na web [Microsoft Azure Portal](https://portal.azure.com/) a pak zadejte svoje přihlašovací údaje pro přihlášení k portálu.

Výchozím zobrazením je váš řídicí panel služby.

## <a name="register-the-resource-provider"></a>Registrace poskytovatele prostředků

Před vytvořením první instance Azure Database Migration Service Zaregistrujte poskytovatele prostředků Microsoft. datamigration.

1. V Azure Portal vyberte **odběry**, vyberte předplatné, ve kterém chcete vytvořit instanci Azure Database Migration Service a pak vyberte **poskytovatelé prostředků**.

    ![Hledat poskytovatele prostředků](media/quickstart-create-data-migration-service-hybrid-portal/dms-portal-search-resource-provider.png)

2. Vyhledejte „migration“ a pak napravo od **Microsoft.DataMigration** vyberte **Zaregistrovat**.

    ![Registrace poskytovatele prostředků](media/quickstart-create-data-migration-service-hybrid-portal/dms-portal-register-resource-provider.png)

## <a name="create-an-instance-of-the-service"></a>Vytvoření instance služby

1. Vyberte +**vytvořit prostředek** pro vytvoření instance Azure Database Migration Service.

2. Vyhledejte na webu Marketplace "migrace", vyberte **Azure Database Migration Service**a pak na obrazovce **Azure Database Migration Service** vyberte **vytvořit**.

3. Na obrazovce **Vytvořit službu Migration Service**:

    - Vyberte **název služby** , který je zapamatovatelné a jedinečný pro identifikaci vaší instance Azure Database Migration Service.
    - Vyberte **Předplatné** Azure, ve kterém chcete instanci vytvořit.
    - Vyberte existující **skupinu prostředků** nebo vytvořte novou.
    - Zvolte **Umístění**, které je nejblíže vašemu zdrojovému nebo cílovému serveru.
    - Jako **režim služby**vyberte **hybridní (Preview)**.

         ![Vytvoření základních informací o službě migrace](media/quickstart-create-data-migration-service-hybrid-portal/dms-create-service-basics.png)

4. Vyberte **Zkontrolovat a vytvořit**.

5. Na kartě **Revize + vytvořit** si přečtěte tyto informace, zkontrolujte, jaké další informace jsou k dispozici, a pak vyberte **vytvořit**.

    ![Vytvoření služby migrace – kontrola + vytvoření](media/quickstart-create-data-migration-service-hybrid-portal/dms-create-service-review-and-create.png)

    Po chvíli je vaše instance Azure Database Migration Service v hybridním režimu vytvořená a připravená k nastavení. Instance Azure Database Migration Service se zobrazí, jak je znázorněno na následujícím obrázku:

    ![Instance hybridního režimu Azure Database Migration Service](media/quickstart-create-data-migration-service-hybrid-portal/dms-instance-hybrid-mode.png)

6. Po vytvoření služby vyberte **vlastnosti**a potom zkopírujte hodnotu zobrazenou v poli **ID prostředku** , které použijete k instalaci Azure Database Migration Service Hybrid Worker.

    ![Azure Database Migration Service vlastnosti hybridního režimu](media/quickstart-create-data-migration-service-hybrid-portal/dms-copy-resource-id.png)

## <a name="create-azure-app-registration-id"></a>Vytvořit ID registrace aplikace Azure

Musíte vytvořit registraci aplikace Azure, kterou může místní hybridní pracovní proces používat ke komunikaci s Azure Database Migration Service v cloudu.

1. V Azure Portal vyberte **Azure Active Directory**, vyberte **Registrace aplikací**a pak vyberte **Nová registrace**.
2. Zadejte název aplikace a potom v části **podporované typy účtů**vyberte typ účtů, které mají být podporovány, chcete-li určit, kdo může aplikaci používat.

    ![Registrace aplikace v Azure Database Migration Service hybridního režimu](media/quickstart-create-data-migration-service-hybrid-portal/dms-register-application.png)

3. Pro pole **URI přesměrování (nepovinné)** použijte výchozí hodnoty a pak vyberte **Registrovat**.

4. Po dokončení registrace ID aplikace si poznamenejte **ID aplikace (klienta)**, kterou použijete při instalaci hybridního pracovního procesu.

5. V Azure Portal přejděte na Azure Database Migration Service, vyberte **řízení přístupu (IAM)** a pak výběrem **Přidat přiřazení role** přiřaďte přístup Přispěvatel k ID aplikace.

    ![Azure Database Migration Service hybridního režimu přiřazení role Přispěvatel](media/quickstart-create-data-migration-service-hybrid-portal/dms-app-assign-contributor.png)

6. Jako roli vyberte **Přispěvatel** , přiřaďte přístup k **uživateli služby Azure AD nebo instančnímu objektu**a potom vyberte název ID aplikace.

    ![Podrobnosti o přiřazení role přispěvatele Azure Database Migration Serviceho hybridního režimu](media/quickstart-create-data-migration-service-hybrid-portal/dms-add-role-assignment.png)

7. Výběrem **Uložit** uložte přiřazení role pro ID aplikace v prostředku Azure Database Migration Service.

## <a name="download-and-install-the-hybrid-worker"></a>Stažení a instalace hybridního pracovního procesu

1. V Azure Portal přejděte do vaší instance Azure Database Migration Service.

2. V části **Nastavení**vyberte **hybridní**a potom vyberte **stáhnout instalační program** a Stáhněte si hybridního pracovního procesu.

    ![Stažení hybridního pracovního procesu Azure Database Migration Service](media/quickstart-create-data-migration-service-hybrid-portal/dms-installer-download.png)

3. Extrahujte soubor ZIP na serveru, který bude hostitelem Azure Database Migration Service Hybrid Worker.

    > [!IMPORTANT]
    > Azure Database Migration Service Hybrid Installer vyžaduje rozhraní .NET 4.7.2 nebo novější. Nejnovější verze rozhraní .NET najdete na stránce [stažení .NET Framework](https://dotnet.microsoft.com/download/dotnet-framework) .

4. Ve složce Install vyhledejte a otevřete **dmsSettings.jsv** souboru, zadejte **ApplicationId** a **ResourceID**a pak soubor uložte.

    ![Azure Database Migration Service nastavení hybridního pracovního procesu](media/quickstart-create-data-migration-service-hybrid-portal/dms-settings.png)

5. Pomocí následujícího příkazu Vygenerujte certifikát, který Azure Database Migration Service dá použít k ověření komunikace z hybridního pracovního procesu.

    ```
    <drive>:\<folder>\Install>DMSWorkerBootstrap.exe -a GenerateCert
    ```

    Certifikát se vygeneruje ve složce pro instalaci.

    ![Azure Database Migration Service certifikát Hybrid Worker](media/quickstart-create-data-migration-service-hybrid-portal/dms-certificate.png)

6. V Azure Portal přejděte na ID aplikace v části **Spravovat**vyberte možnost s **certifikátem &** a pak vyberte **nahrát certifikát** a vyberte veřejný certifikát, který jste vygenerovali.

    ![Azure Database Migration Service nahrávání certifikátu Hybrid Worker](media/quickstart-create-data-migration-service-hybrid-portal/dms-app-upload-certificate.png)

7. Na místní server nainstalujte Azure Database Migration Service Hybrid Worker spuštěním následujícího příkazu:

    ```
    <drive>:\<folder>\Install>DMSWorkerBootstrap.exe -a Install -IAcceptDMSLicenseTerms -d
    ```

    > [!NOTE]
    > Při spuštění instalačního příkazu můžete také použít následující parametry:
    >
    > - **-TelemetryOptOut** – zastaví pracovní proces z odesílání telemetrie, ale nadále se místně přihlašuje.  Instalační program stále posílá telemetrii.
    > - **-p {INSTALLLOCATION}**. Umožňuje změnit instalační cestu, která je ve výchozím nastavení "C:\Program Files\DatabaseMigrationServiceHybrid".

8. Pokud se instalační program spustí bez chyby, služba zobrazí online stav v Azure Database Migration Service a budete připraveni migrovat databáze.

    ![Azure Database Migration Service online](media/quickstart-create-data-migration-service-hybrid-portal/dms-instance-hybrid-mode-online.png)

## <a name="uninstall-azure-database-migration-service-hybrid-mode"></a>Odinstalace Azure Database Migration Service hybridního režimu

V současné době se odinstalace Azure Database Migration Service hybridního režimu podporuje jenom prostřednictvím instalačního programu Azure Database Migration Service Hybrid Worker na vašem místním serveru, a to pomocí následujícího příkazu:

```
<drive>:\<folder>\Install>DMSWorkerBootstrap.exe -a uninstall
```

> [!NOTE]
> Při spuštění příkazu Uninstall můžete použít také parametr-ReuseCert, který udržuje certifikát AdApp generovaný pracovním postupem generateCert.  To umožňuje použít stejný certifikát, který se dřív vygeneroval a nahrál.

## <a name="set-up-the-azure-database-migration-service-hybrid-worker-using-powershell"></a>Nastavení Azure Database Migration Service Hybrid Worker pomocí prostředí PowerShell

Kromě instalace Azure Database Migration Service Hybrid Worker prostřednictvím Azure Portal poskytujeme [powershellový skript](https://techcommunity.microsoft.com/gxcuf89792/attachments/gxcuf89792/MicrosoftDataMigration/119/1/DMS_Hybrid_Script.zip) , který můžete použít k automatizaci kroků instalace pracovního procesu po vytvoření nové instance Azure Database Migration Service v hybridním režimu. Tento skript:

1. Vytvoří nový AdApp.
2. Stáhne instalační program.
3. Spustí pracovní postup generateCert.
4. Nahraje certifikát.
5. Přidá do instance Azure Database Migration Service jako přispěvatele AdApp.
6. Spustí pracovní postup instalace.

Tento skript je určený pro rychlé vytváření prototypů, pokud už uživatel má všechna potřebná oprávnění v prostředí. Všimněte si, že ve vašem produkčním prostředí můžou mít AdApp a CERT různé požadavky, takže by se skript mohl zdařit.

> [!IMPORTANT]
> Tento skript předpokládá, že v hybridním režimu existuje existující instance Azure Database Migration Service a že účet Azure, který používá, má oprávnění k vytvoření AdApps v tenantovi a pro úpravu služby Azure RBAC v rámci předplatného.

Vyplňte parametry v horní části skriptu a spusťte skript z instance prostředí PowerShell pro správce.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Migrace SQL Server do online spravované instance služby Azure SQL](tutorial-sql-server-managed-instance-online.md) 
>  [Migrace SQL Server pro Azure SQL Database offline](tutorial-sql-server-to-azure-sql.md)
