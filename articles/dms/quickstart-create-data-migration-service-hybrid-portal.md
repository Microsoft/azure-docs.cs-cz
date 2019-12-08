---
title: 'Rychlý Start: vytvoření instance Azure Database Migration Serviceho hybridního režimu pomocí Azure Portal | Microsoft Docs'
description: Vytvoření instance Azure Database Migration Service v hybridním režimu pomocí Azure Portal
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: quickstart
ms.date: 12/06/2019
ms.openlocfilehash: a124c33f15318f1b9b22a750a1de15601823afa3
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2019
ms.locfileid: "74890687"
---
# <a name="quickstart-create-an-instance-of-azure-database-migration-service-in-hybrid-mode-using-the-azure-portal-preview"></a>Rychlý Start: vytvoření instance Azure Database Migration Service v hybridním režimu pomocí Azure Portal (Preview)

Azure Database Migration Service hybridní režim spravuje migrace databáze pomocí pracovního procesu migrace, který je hostovaný místně spolu s instancí Azure Database Migration Service spuštěným v cloudu. Hybridní režim je zvláště užitečný pro scénáře, ve kterých se nachází mezi místními sítěmi a Azure nedostatečná konektivita typu Site-to-site, nebo pokud existuje omezená šířka pásma připojení typu Site-to-site.

V tomto rychlém startu použijete Azure Portal k vytvoření instance Azure Database Migration Service v hybridním režimu. Potom můžete stáhnout, nainstalovat a nastavit hybridní pracovní proces v místní síti. Během období Preview můžete k migraci dat z místní instance SQL Server na Azure SQL Database použít hybridní režim Azure Database Migration Service.

> [!IMPORTANT]
> Azure Database Migration Service Hybrid Installer vyžaduje rozhraní .NET 4.7.2 nebo novější. Nejnovější verze rozhraní .NET najdete na stránce [stažení .NET Framework](https://dotnet.microsoft.com/download/dotnet-framework) .

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný](https://azure.microsoft.com/free/) účet před tím, než začnete.

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

2. Vyhledejte na marketplace „migration“, vyberte **Azure Database Migration Service** a pak na obrazovce **Azure Database Migration Service** vyberte **Vytvořit**.

3. Na obrazovce **Vytvořit službu Migration Service**:

    - Vyberte **název služby** , který je zapamatovatelné a jedinečný pro identifikaci vaší instance Azure Database Migration Service.
    - Vyberte **Předplatné** Azure, ve kterém chcete instanci vytvořit.
    - Vyberte existující **skupinu prostředků** nebo vytvořte novou.
    - Zvolte **Umístění**, které je nejblíže vašemu zdrojovému nebo cílovému serveru.

    > [!IMPORTANT]
    > Během období Preview je hybridní režim podporován pouze v Východní USA oblasti. Vzhledem k tomu, že hybridní pracovní proces je nainstalovaný ve vaší místní síti, existuje malý nebo žádný dopad na výkon, i když migrujete na cíl v jiné oblasti.

    - Jako **režim služby**vyberte **hybridní (Preview)** .

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

4. Po dokončení registrace ID aplikace si poznamenejte **ID aplikace (klienta)** , kterou použijete při instalaci hybridního pracovního procesu.

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

4. Ve složce Install vyhledejte a otevřete soubor **dmsSettings. JSON** , zadejte **ApplicationId** a **ResourceID**a pak soubor uložte.

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
    <drive>:\<folder>\Install>DMSWorkerBootstrap.exe -a Install -IAcceptDMSLicenseTerms
    ```

8. Pokud se instalační program spustí bez chyby, služba zobrazí online stav v Azure Database Migration Service a budete připraveni migrovat databáze.

    ![Azure Database Migration Service online](media/quickstart-create-data-migration-service-hybrid-portal/dms-instance-hybrid-mode-online.png)

## <a name="uninstall-azure-database-migration-service-hybrid-mode"></a>Odinstalace Azure Database Migration Service hybridního režimu

V současné době se odinstalace Azure Database Migration Service hybridního režimu podporuje jenom prostřednictvím instalačního programu Azure Database Migration Service Hybrid Worker na vašem místním serveru, a to pomocí následujícího příkazu:

```
<drive>:\<folder>\Install>DMSWorkerBootstrap.exe -a uninstall
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Migrace SQL Server do Azure SQL Database spravované instance online](tutorial-sql-server-managed-instance-online.md)
> [migraci SQL Server do izolované databáze nebo do fondu databáze v Azure SQL Database offline](tutorial-sql-server-to-azure-sql.md)
