---
title: 'Úvodní příručka: Vytvoření instance hybridního režimu pomocí portálu Azure'
titleSuffix: Azure Database Migration Service
description: Na webu Azure Portal můžete vytvořit instanci služby Migrace databáze Azure v hybridním režimu.
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
ms.openlocfilehash: dd3e77610749eb5d146b0c0b7cf9d307fba0dd83
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "79370232"
---
# <a name="quickstart-create-a-hybrid-mode-instance-with-azure-portal--azure-database-migration-service"></a>Úvodní příručka: Vytvoření instance hybridního režimu pomocí portálu Azure & služby migrace databáze Azure

Hybridní režim Azure Database Migration Service spravuje migrace databází pomocí pracovníka migrace, který je hostovaný místně, spolu s instancí služby Migrace databáze Azure spuštěnou v cloudu. Hybridní režim je užitečný zejména pro scénáře, ve kterých je nedostatek připojení mezi lokalitami mezi místní sítí a Azure nebo pokud je omezená šířka pásma připojení mezi lokalitami.

>[!NOTE]
>Služba migrace databáze Azure spuštěná v hybridním režimu v současné době podporuje migrace serveru SQL Server na:
>
>- Spravovaná instance Azure SQL Database s téměř nulovými prostoji (online).
>- Azure SQL Database jedna databáze s některé prostoje (offline).
>- MongoDb do Azure CosmosDB s téměř nulovými prostoji (online).
>- MongoDb na Azure CosmosDB s některými prostoje (offline).

V tomto rychlém startu použijete portál Azure k vytvoření instance služby Migrace databáze Azure v hybridním režimu. Poté stáhnete, nainstalujete a nastavíte hybridního pracovníka v místní síti. Během předběžné verze můžete použít hybridní režim Azure Database Migration Service k migraci dat z místní instance SQL Serveru do Azure SQL Database.

> [!NOTE]
> Hybridní instalační služba Služby migrace databáze Azure běží na Microsoft Windows Server 2012 R2, Window Server 2016, Windows Server 2019 a Windows 10.

> [!IMPORTANT]
> Hybridní instalační program služby Azure Database Migration Service vyžaduje rozhraní .NET 4.7.2 nebo novější. Nejnovější verze rozhraní .NET naleznete na stránce [Download .NET Framework.](https://dotnet.microsoft.com/download/dotnet-framework)

Pokud nemáte předplatné Azure, vytvořte si [bezplatný](https://azure.microsoft.com/free/) účet, než začnete.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Otevřete webový prohlížeč, přejděte na web [Microsoft Azure Portal](https://portal.azure.com/) a pak zadejte svoje přihlašovací údaje pro přihlášení k portálu.

Výchozím zobrazením je váš řídicí panel služby.

## <a name="register-the-resource-provider"></a>Registrace poskytovatele prostředků

Zaregistrujte poskytovatele prostředků Microsoft.DataMigration před vytvořením první instance služby Migrace databáze Azure.

1. Na webu Azure Portal vyberte **Předplatná**, vyberte předplatné, ve kterém chcete vytvořit instanci služby Migrace databáze Azure, a pak vyberte **Zprostředkovatelé prostředků**.

    ![Hledat zprostředkovatele prostředků](media/quickstart-create-data-migration-service-hybrid-portal/dms-portal-search-resource-provider.png)

2. Vyhledejte „migration“ a pak napravo od **Microsoft.DataMigration** vyberte **Zaregistrovat**.

    ![Registrace poskytovatele prostředků](media/quickstart-create-data-migration-service-hybrid-portal/dms-portal-register-resource-provider.png)

## <a name="create-an-instance-of-the-service"></a>Vytvoření instance služby

1. Vyberte +**Vytvořit prostředek** k vytvoření instance služby Migrace databáze Azure.

2. Vyhledejte na marketplace "migraci", vyberte **Službu migrace databáze Azure**a potom na obrazovce **Azure Database Migration Service** vyberte **Vytvořit**.

3. Na obrazovce **Vytvořit službu Migration Service**:

    - Zvolte **název služby,** který je zapamatovatelný a jedinečný k identifikaci vaší instance služby Migrace databáze Azure.
    - Vyberte **Předplatné** Azure, ve kterém chcete instanci vytvořit.
    - Vyberte existující **skupinu prostředků** nebo vytvořte novou.
    - Zvolte **Umístění**, které je nejblíže vašemu zdrojovému nebo cílovému serveru.
    - V **servisním režimu**vyberte **možnost Hybridní (Náhled).**

         ![Vytvořit službu migrace – základy](media/quickstart-create-data-migration-service-hybrid-portal/dms-create-service-basics.png)

4. Vyberte **Zkontrolovat a vytvořit**.

5. Na kartě **Revize + vytvořit** zkontrolujte podmínky, ověřte další poskytnuté informace a pak vyberte **Vytvořit**.

    ![Vytvořit službu migrace - recenze + vytvořit](media/quickstart-create-data-migration-service-hybrid-portal/dms-create-service-review-and-create.png)

    Po několika okamžicích se vytvoří vaše instance služby Migrace databáze Azure v hybridním režimu a je připravena k nastavení. Instance služby Migrace databáze Azure se zobrazí tak, jak je znázorněno na následujícím obrázku:

    ![Instance hybridního režimu služby Azure Database Migration Service](media/quickstart-create-data-migration-service-hybrid-portal/dms-instance-hybrid-mode.png)

6. Po vytvoření služby vyberte **Vlastnosti**a zkopírujte hodnotu zobrazenou v poli **Id prostředků,** které použijete k instalaci hybridního pracovníka služby Migrace databáze Azure.

    ![Vlastnosti hybridního režimu služby Azure Database Migration Service](media/quickstart-create-data-migration-service-hybrid-portal/dms-copy-resource-id.png)

## <a name="create-azure-app-registration-id"></a>Vytvoření ID registrace aplikace Azure

Musíte vytvořit ID registrace aplikace Azure, které místní hybridní pracovník může použít ke komunikaci se službou Migrace databáze Azure v cloudu.

1. Na webu Azure Portal vyberte **Azure Active Directory**, vyberte Registrace **aplikací**a pak vyberte **Nová registrace**.
2. Zadejte název aplikace a potom v části **Podporované typy účtů**vyberte typ účtů, které chcete podporovat, a určete, kdo může aplikaci používat.

    ![Aplikace hybridního registru služby Azure Database Migration Service](media/quickstart-create-data-migration-service-hybrid-portal/dms-register-application.png)

3. Použijte výchozí hodnoty pro pole **Uri přesměrování (volitelné)** a pak vyberte **Registrovat**.

4. Po dokončení registrace ID aplikace poznamenejte **ID aplikace (klienta),** které budete používat při instalaci hybridního pracovníka.

5. Na webu Azure Portal přejděte na Azure Database Migration Service, vyberte **Řízení přístupu (IAM)** a pak vyberte **Přidat přiřazení role** a přiřaďte přístup přispěvatele k ID aplikace.

    ![Hybridní režim služby Migrace databáze Azure přiřazuje roli přispěvatele](media/quickstart-create-data-migration-service-hybrid-portal/dms-app-assign-contributor.png)

6. Vyberte **přispěvatel jako** roli, přiřaďte přístup **uživateli Azure AD nebo instančnímu objektu a**vyberte název ID aplikace.

    ![Hybridní režim služby Azure Database Migration Service přiřazuje podrobnosti o roli přispěvatele](media/quickstart-create-data-migration-service-hybrid-portal/dms-add-role-assignment.png)

7. Vyberte **Uložit,** chcete-li uložit přiřazení role pro ID aplikace na prostředku služby migrace databáze Azure.

## <a name="download-and-install-the-hybrid-worker"></a>Stažení a instalace hybridního pracovníka

1. Na webu Azure Portal přejděte na instanci služby Migrace databáze Azure.

2. V části **Nastavení**vyberte **Hybridní**a pak vyberte **Instalační program stáhnout,** abyste stáhli hybridního pracovníka.

    ![Stažení hybridního pracovního pracovníka služby Azure Database Migration Service](media/quickstart-create-data-migration-service-hybrid-portal/dms-installer-download.png)

3. Extrahujte soubor ZIP na serveru, který bude hostovat hybridního pracovníka služby Migrace databáze Azure.

    > [!IMPORTANT]
    > Hybridní instalační program služby Azure Database Migration Service vyžaduje rozhraní .NET 4.7.2 nebo novější. Nejnovější verze rozhraní .NET naleznete na stránce [Download .NET Framework.](https://dotnet.microsoft.com/download/dotnet-framework)

4. Ve složce instalace vyhledejte a otevřete soubor **dmsSettings.json,** zadejte **ApplicationId** a **resourceId**a pak soubor uložte.

    ![Nastavení hybridního pracovního procesu služby Azure Database Migration Service](media/quickstart-create-data-migration-service-hybrid-portal/dms-settings.png)

5. Vygenerujte certifikát, který může služba Azure Database Migration Service použít k ověření komunikace od hybridního pracovníka pomocí následujícího příkazu.

    ```
    <drive>:\<folder>\Install>DMSWorkerBootstrap.exe -a GenerateCert
    ```

    Ve složce Instalovat je generován certifikát.

    ![Certifikát hybridního pracovního procesu služby Azure Database Migration Service](media/quickstart-create-data-migration-service-hybrid-portal/dms-certificate.png)

6. Na webu Azure Portal přejděte na ID aplikace v části **Správa**vyberte **Certifikáty & tajných kódů**a pak vyberte **Nahrát certifikát** a vyberte veřejný certifikát, který jste vygenerovali.

    ![Nahrání hybridního pracovního certifikátu služby Azure Database Migration Service](media/quickstart-create-data-migration-service-hybrid-portal/dms-app-upload-certificate.png)

7. Nainstalujte hybridního pracovníka služby Azure Database Migration Service na místní server spuštěním následujícího příkazu:

    ```
    <drive>:\<folder>\Install>DMSWorkerBootstrap.exe -a Install -IAcceptDMSLicenseTerms -d
    ```

    > [!NOTE]
    > Při spuštění příkazu install můžete také použít následující parametry:
    >
    > - **-TelemetryOptOut** - Zastaví pracovníka z odesílání telemetrie, ale nadále protokolu místně minimálně.  Instalační program stále odesílá telemetrii.
    > - **-p {InstallLocation}**. Povolí změnu instalační cesty, která je ve výchozím nastavení "C:\Program Files\DatabaseMigrationServiceHybrid".

8. Pokud se instalační program spustí bez chyby, služba zobrazí stav online ve službě Migrace databáze Azure a jste připraveni k migraci databází.

    ![Služba migrace databáze Azure online](media/quickstart-create-data-migration-service-hybrid-portal/dms-instance-hybrid-mode-online.png)

## <a name="uninstall-azure-database-migration-service-hybrid-mode"></a>Odinstalace hybridního režimu služby Migrace databáze Azure

V současné době je odinstalace hybridního režimu služby Migrace databáze Azure podporována jenom prostřednictvím instalačního programu hybridního pracovního procesu služby Azure Database Migration Service na místním serveru pomocí následujícího příkazu:

```
<drive>:\<folder>\Install>DMSWorkerBootstrap.exe -a uninstall
```

> [!NOTE]
> Při spuštění příkazu odinstalovat můžete také použít parametr "-ReuseCert", který udržuje certifikát AdApp generovaný pracovním postupem generateCert.  To umožňuje používat stejný certifikát, který byl dříve vygenerován a odeslán.

## <a name="set-up-the-azure-database-migration-service-hybrid-worker-using-powershell"></a>Nastavení hybridního pracovníka služby Migrace databáze Azure pomocí Prostředí PowerShell

Kromě instalace hybridního pracovníka služby Azure Database Migration Service prostřednictvím portálu Azure poskytujeme [skript prostředí PowerShell,](https://techcommunity.microsoft.com/gxcuf89792/attachments/gxcuf89792/MicrosoftDataMigration/119/1/DMS_Hybrid_Script.zip) který můžete použít k automatizaci kroků instalace pracovního procesu po vytvoření nové instance služby Migrace databáze Azure v hybridním režimu. Skript:

1. Vytvoří nový AdApp.
2. Stáhne instalační program.
3. Spustí pracovní postup generateCert.
4. Nahraje certifikát.
5. Přidá AdApp jako přispěvatele do instance služby Migrace databáze Azure.
6. Spustí pracovní postup instalace.

Tento skript je určen pro rychlé vytváření prototypů, když uživatel již má všechna potřebná oprávnění v prostředí. Všimněte si, že ve vašem produkčním prostředí AdApp a Cert může mít různé požadavky, takže skript může selhat.

> [!IMPORTANT]
> Tento skript předpokládá, že existuje existující instance služby Migrace databáze Azure v hybridním režimu a že účet Azure používá má oprávnění k vytváření AdApps v tenantovi a upravit RBAC na předplatné.

Vyplňte parametry v horní části skriptu a spusťte skript z instance prostředí PowerShell správce.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Migrace SQL Serveru do spravované instance Azure SQL Database online](tutorial-sql-server-managed-instance-online.md)
> [migrace SQL Serveru do jedné databáze nebo databáze s dražeb v Azure SQL Database offline](tutorial-sql-server-to-azure-sql.md)
