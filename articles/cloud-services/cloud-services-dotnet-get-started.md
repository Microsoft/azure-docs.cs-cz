---
title: Začínáme s cloudovými službami službou Azure Cloud Services a technologií ASP.NET | Dokumentace Microsoftu
description: Naučte se vytvářet vícevrstvé aplikace s použitím technologie ASP.NET MVC a Azure. Aplikace běží v cloudové službě a obsahuje webovou roli a roli pracovního procesu. Používá nástroj Entity Framework, službu Azure SQL Database a fronty a objekty blob služby Azure Storage.
services: cloud-services, storage
documentationcenter: .net
author: Thraka
manager: timlt
editor: ''
ms.assetid: d7aa440d-af4a-4f80-b804-cc46178df4f9
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 05/15/2017
ms.author: adegeo
ms.openlocfilehash: 66ece0affbafc219add2ef5c4da34f29ab34e058
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2018
ms.locfileid: "37860016"
---
# <a name="get-started-with-azure-cloud-services-and-aspnet"></a>Začínáme s cloudovými službami Azure Cloud Services a technologií ASP.NET

## <a name="overview"></a>Přehled
Tento kurz ukazuje, jak lze vytvářet vícevrstvé aplikace .NET s front-endem ASP.NET MVC a jak je nasadit do [cloudové služby Azure](cloud-services-choose-me.md). Aplikace používá [službu Azure SQL Database](http://msdn.microsoft.com/library/azure/ee336279), [službu Azure Blob service](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/unstructured-blob-storage) a [službu front Azure](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/queue-centric-work-pattern). [Projekt sady Visual Studio můžete stáhnout](http://code.msdn.microsoft.com/Simple-Azure-Cloud-Service-e01df2e4) z galerie kódů MSDN.

V kurzu se dozvíte, jak sestavit a spustit aplikaci místně, jak ji nasadit do Azure a spustit v cloudu a jak ji sestavit od nuly. Pokud chcete, můžete začít tím, že ji sestavíte od nuly, potom ji otestujete a nakonec provedete kroky nasazení.

## <a name="contoso-ads-application"></a>Aplikace Contoso Ads
Aplikace slouží jako vývěsní tabule pro inzerci. Uživatelé vytvářejí reklamu tak, že zadají text a odešlou obrázek. Před sebou vidí seznam reklam s obrázky miniatur a plnou velikost obrázku s podrobnostmi si mohou zobrazit výběrem požadované reklamy.

![Seznam reklam](./media/cloud-services-dotnet-get-started/list.png)

Aplikace používá [způsob práce zaměřený na fronty](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/queue-centric-work-pattern), aby vyvážila práci při vytváření miniatur (která je náročná na prostředky procesoru) vůči back-endovému procesu.

## <a name="alternative-architecture-web-apps-and-webjobs"></a>Alternativní architektura: Web Apps a WebJobs
Tento kurz ukazuje, jak spustit front-end i back-end v cloudové službě Azure. Alternativou je spuštění front-endu v [Azure Web Apps](/azure/app-service/) a použití funkce [WebJobs](http://go.microsoft.com/fwlink/?LinkId=390226) pro back-end. Kurz, který používá webové úlohy, najdete v článku [Začínáme se sadou SDK pro Azure WebJobs](https://github.com/Azure/azure-webjobs-sdk/wiki). Informace o tom, jak zvolit služby, které budou nejlépe vyhovovat vašemu scénáři, najdete v článku o [porovnání webů Azure, služeb Cloud Services a virtuálních počítačů](../app-service/choose-web-site-cloud-service-vm.md).

## <a name="what-youll-learn"></a>Co se dozvíte
* Postup zprovoznění počítače pro vývoj na platformě Azure nainstalováním sady Azure SDK.
* Vytvoření projektu cloudových služeb sady Visual Studio s webovou rolí a rolí pracovního procesu technologie ASP.NET MVC.
* Postup místního testování projektu cloudových služeb pomocí emulátoru úložiště Azure.
* Postup publikování cloudového projektu do cloudové služby Azure a testování pomocí účtu úložiště Azure.
* Odeslání souborů a jejich uložení do služby Azure Blob service.
* Používání služby front Azure pro komunikaci mezi vrstvami.

## <a name="prerequisites"></a>Požadavky
Kurz předpokládá, že rozumíte [základnímu konceptu cloudových služeb Azure](cloud-services-choose-me.md), například terminologii *webových rolí* a *rolí pracovních procesů*.  Předpokládá také, že víte, jak pracovat s technologií [ASP.NET MVC](http://www.asp.net/mvc/tutorials/mvc-5/introduction/getting-started) a s projekty [webových formulářů](http://www.asp.net/web-forms/tutorials/aspnet-45/getting-started-with-aspnet-45-web-forms/introduction-and-overview) ve Visual Studiu. Ukázková aplikace používá MVC, ale většina kurzu platí i pro webové formuláře.

Aplikaci můžete spustit místně bez předplatného Azure, ale k nasazení aplikace do cloudu budete předplatné potřebovat. Pokud nemáte účet, můžete si [aktivovat výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A55E3C668) nebo [si zaregistrovat bezplatnou zkušební verzi](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A55E3C668).

Pokyny kurzu pracují s jedním z následujících produktů:

* Visual Studio 2013
* Visual Studio 2015
* Visual Studio 2017

Pokud je nemáte, Visual Studio se vám může nainstalovat automaticky při instalaci sady Azure SDK.

## <a name="application-architecture"></a>Architektura aplikace
Aplikace ukládá reklamy do databáze SQL a k vytváření tabulky a přístupu k datům používá Entity Framework Code First. U každé reklamy databáze ukládá dvě adresy URL. Jednu pro obrázek v plné velikosti a druhou pro miniaturu.

![Tabulka reklam](./media/cloud-services-dotnet-get-started/adtable.png)

Když uživatel odešle obrázek, front-end spuštěný ve webové roli obrázek uloží do [objektu blob Azure](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/unstructured-blob-storage), který ukládá informace o reklamě do databáze s adresou URL, která odkazuje na objekt blob. Ve stejný okamžik zapíše zprávu do fronty Azure. Back-endový proces spuštěný v roli pracovního procesu se pravidelně dotazuje fronty na nové zprávy. Když se objeví nová zpráva, role pracovního procesu vytvoří miniaturu obrázku a zaktualizuje pole v databázi s adresou URL miniatury pro tuto reklamu. Následující diagram znázorňuje interakci částí aplikace.

![Architektura Contoso Ads](./media/cloud-services-dotnet-get-started/apparchitecture.png)

[!INCLUDE [install-sdk](../../includes/install-sdk-2017-2015-2013.md)]

## <a name="download-and-run-the-completed-solution"></a>Stažení a spuštění dokončeného řešení
1. Stáhněte a rozbalte [dokončené řešení](http://code.msdn.microsoft.com/Simple-Azure-Cloud-Service-e01df2e4).
2. Spusťte Visual Studio.
3. V nabídce **Soubor** zvolte **Otevřít projekt**, přejděte do místa, kam jste řešení stáhli, a potom otevřete soubor řešení.
4. Stisknutím kláves CTRL+SHIFT+B řešení sestavíte.

    Visual Studio ve výchozím nastavení automaticky obnoví obsah balíčku NuGet, který nebyl součástí souboru *.zip*. Pokud se balíčky neobnoví, nainstalujte je ručně tak, že přejdete do dialogového okna **Správa balíčků NuGet pro řešení** a kliknete na tlačítko **Obnovit**, které je vpravo nahoře.
5. V **Průzkumníku řešení** zkontrolujte, jestli je jako spouštěný projekt vybraná možnost **ContosoAdsCloudService**.
6. Pokud používáte Visual Studio 2015 nebo vyšší, změňte připojovací řetězec serveru SQL v aplikačním souboru *Web.config* projektu ContosoAdsWeb a v souboru *ServiceConfiguration.Local.cscfg* projektu ContosoAdsCloudService. V každém případě změňte „(localdb) \v11.0“ na „\MSSQLLocalDB (localdb)“.
7. Stiskněte klávesy CTRL+F5 a spusťte aplikaci.

    Když spouštíte projekt cloudové služby místně, Visual Studio automaticky vyvolá *emulátor služby Výpočty* Azure a *emulátor úložiště* Azure. Emulátor služby Výpočty využívá prostředky počítače k simulaci prostředí webové role a role pracovního procesu. Emulátor úložiště používá databázi serveru [SQL Server Express LocalDB](http://msdn.microsoft.com/library/hh510202.aspx) k simulaci cloudového úložiště Azure.

    Při prvním spuštění projektu cloudové služby může spuštění emulátorů trvat zhruba minutu. Když je spuštění emulátorů dokončené, výchozí prohlížeč otevře domovskou stránku aplikace.

    ![Architektura Contoso Ads](./media/cloud-services-dotnet-get-started/home.png)
8. Klikněte na **Vytvořit reklamu**.
9. Zadejte nějaká testovací data, vyberte obrázek *.jpg*, který chcete odeslat, a potom klikněte na **Vytvořit**.

    ![Vytvoření stránky](./media/cloud-services-dotnet-get-started/create.png)

    Aplikace přejde na indexovou stránku, ale nezobrazí miniaturu nové reklamy, protože toto zpracování ještě neproběhlo.
10. Chvíli počkejte a potom indexovou stránku aktualizujte. Miniatura se teď zobrazí.

     ![Indexová stránka](./media/cloud-services-dotnet-get-started/list.png)
11. Pokud chcete obrázek reklamy zobrazit v plné velikosti, klikněte na **Podrobnosti**.

     ![Stránka podrobností](./media/cloud-services-dotnet-get-started/details.png)

Aplikace běží výhradně na místním počítači bez připojení ke cloudu. Emulátor úložiště ukládá data fronty a objektů blob do databáze serveru SQL Server Express LocalDB, ale aplikace ukládá data reklamy do jiné databáze LocalDB. Entity Framework Code First automaticky vytvoří databázi reklam v okamžiku, kdy se webová aplikace poprvé pokusí k databázi připojit.

V následující části budete konfigurovat řešení tak, aby při spuštění v cloudu používalo cloudové prostředky Azure pro fronty a objekty blob a také databázi aplikace. Pokud chcete aplikaci i nadále spouštět místně, ale používat cloudové úložiště a databázové prostředky, tak můžete. Stačí nastavit připojovací řetězce a my vám ukážeme, jak na to.

## <a name="deploy-the-application-to-azure"></a>Nasazení aplikace v Azure
Pokud chcete aplikaci spustit v cloudu, proveďte následující kroky:

* Vytvoření cloudové služby Azure
* Vytvoření databáze SQL Azure
* Vytvoření účtu úložiště Azure
* Nakonfigurujte řešení, aby při spuštění v Azure používalo databázi SQL Azure.
* Nakonfigurujte řešení, aby při spuštění v Azure používalo účet úložiště Azure.
* Nasaďte projekt do cloudové služby Azure.

### <a name="create-an-azure-cloud-service"></a>Vytvoření cloudové služby Azure
Cloudová služba Azure je prostředí, ve kterém bude aplikace spuštěna.

1. Otevřete v prohlížeči portál [Azure Portal](https://portal.azure.com).
2. Klikněte na **Vytvořit prostředek > Compute > Cloudová služba**.

3. Do vstupního pole název DNS zadejte předponu adresy URL pro cloudovou službu.

    Tato adresa URL musí být jedinečná.  Pokud se zvolená předpona už používá, zobrazí se chybová zpráva.
4. Zadejte pro tuto službu novou skupinu prostředků. Klikněte na **Vytvořit nový** a potom zadejte název do vstupního pole Skupina prostředků – například CS_contososadsRG.

5. Vyberte oblast, ve které chcete aplikaci nasadit.

    Toto pole určuje datové centrum, které bude hostovat vaše cloudové služby. V případě produkční aplikace vyberte oblast, která je nejblíž k vašim zákazníkům. V tomto kurzu vyberte oblast, která je nejblíž k vám.
5. Klikněte na možnost **Vytvořit**.

    Na následujícím obrázku vidíte vytvoření cloudové služby s adresou URL CSvccontosoads.cloudapp.net.

    ![Nová cloudová služba](./media/cloud-services-dotnet-get-started/newcs.png)

### <a name="create-an-azure-sql-database"></a>Vytvoření databáze SQL Azure
Když aplikace běží v cloudu, používá cloudovou databázi.

1. Na webu [Azure Portal](https://portal.azure.com) klikněte na **Vytvořit prostředek > Databáze > Databáze SQL**.
2. Do pole **Název databáze** zadejte text *contosoads*.
3. V části **Skupina prostředků** klikněte na **Použít existující** a vyberte skupinu prostředků použitou pro cloudovou službu.
4. Na následujícím obrázku klikněte na **Server – Konfigurovat požadovaná nastavení** a **Vytvořit nový server**.

    ![Tunel pro databázový server](./media/cloud-services-dotnet-get-started/newdb.png)

    Pokud vaše předplatné obsahuje server, můžete alternativně vybrat tento server v rozevíracím seznamu.
5. Do pole **Název serveru** zadejte *csvccontosodbserver*.

6. Zadejte **Přihlašovací jméno** a **Heslo** správce.

    Pokud jste vybrali možnost **Vytvořit nový server**, nebudete zadávat existující název a heslo. Zadáváte nový název a heslo, které teď definujete pro pozdější použití, až budete chtít získat přístup k databázi. Pokud jste vybrali serveru, který jste vytvořili dříve, budete vyzváni k zadání hesla pro uživatelský účet správce jste již vytvořili.
7. Vyberte stejné **Umístění** jako pro cloudové služby.

    Když jsou cloudové služby a databáze v různých datových centrech (různých oblastech), zvýší se latence a bude vám účtována šířka pásma mimo datové centrum. Šířka pásma v rámci datového centra je zdarma.
8. Zkontrolujte možnost **Povolit službám Azure přístup k serveru**.
9. Klikněte na možnost **Vybrat** u nového serveru.

    ![Nový server služby SQL Database](./media/cloud-services-dotnet-get-started/newdbserver.png)
10. Klikněte na možnost **Vytvořit**.

### <a name="create-an-azure-storage-account"></a>Vytvoření účtu úložiště Azure
Účet úložiště Azure poskytuje prostředky pro ukládání dat front a objektů blob v cloudu.

V reálné aplikaci byste obvykle vytvořili samostatné účty pro data aplikací a pro data protokolování a samostatné účty pro testovací data a pro produkční data. V tomto kurzu budete používat jenom jeden účet.

1. Na webu [Azure Portal](https://portal.azure.com) klikněte na **Vytvořit prostředek > Úložiště > Účet úložiště – objekt blob, soubor, tabulka, fronta**.
2. Do pole **Název** zadejte předponu adresy URL.

    Tato předpona a text zobrazený pod polem budou tvořit jedinečnou adresu URL k vašemu účtu úložiště. Pokud vybranou předponu používá někdo jiný, budete si muset zvolit jinou.
3. U **Modelu nasazení** nastavte *Classic*.

4. V rozevíracím seznamu **Replikace** vyberte **Místně redundantní úložiště**.

    Když má účet úložiště povolenou geografickou replikaci, bude se uložený obsah replikovat do sekundárního datacentra, které zajistí převzetí služeb při selhání v případě významnější havárie v primárním umístění. Geografická replikace může způsobit dodatečné náklady. V případě testovacích a vývojových účtů je zbytečné za geografickou replikaci platit. Další informace naleznete v článku o [vytvoření, správě nebo odstranění účtu úložiště](../storage/common/storage-create-storage-account.md).

5. V části **Skupina prostředků** klikněte na **Použít existující** a vyberte skupinu prostředků použitou pro cloudovou službu.
6. V rozevíracím seznamu **Umístění** vyberte stejnou oblast, jakou jste zvolili pro cloudové služby.

    Když jsou cloudové služby a účet úložiště v různých datacentrech (různých oblastech), zvýší se latence a bude vám účtována šířka pásma mimo datové centrum. Šířka pásma v rámci datového centra je zdarma.

    Skupina vztahů Azure nabízí mechanismus pro minimalizaci vzdálenosti mezi prostředky v datovém centru (můžete tak omezit latenci). V tomto kurzu skupinu vztahů nepoužíváme. Další informace naleznete v článku o [vytváření skupiny vztahů v Azure](https://msdn.microsoft.com/library/azure/gg715317.aspx).
7. Klikněte na možnost **Vytvořit**.

    ![Nový účet úložiště](./media/cloud-services-dotnet-get-started/newstorage.png)

    Na obrázku vidíte vytvoření účtu úložiště s adresou URL `csvccontosoads.core.windows.net`.

### <a name="configure-the-solution-to-use-your-azure-sql-database-when-it-runs-in-azure"></a>Konfigurace řešení, aby při spuštění v Azure používalo databázi SQL Azure
Webový projekt a projekt role pracovního procesu mají každý svůj vlastní připojovací řetězec k databázi a každý musí při spuštění aplikace v Azure odkazovat na databázi SQL Azure.

Pro webovou roli a nastavení prostředí cloudové služby pro roli pracovního procesu budete používat [transformaci Web.config](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/web-config-transformations).

> [!NOTE]
> V této a v další části uložíte přihlašovací údaje do souborů projektu. [Citlivá data neukládejte do veřejných úložišť  zdrojového kódu](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control#secrets).
>
>

1. V projektu ContosoAdsWeb otevřete transformační soubor *Web.Release.config* pro soubor *Web.config* aplikace, odstraňte blok komentáře, který obsahuje prvek `<connectionStrings>`, a vložte následující kód na příslušné místo.

    ```xml
    <connectionStrings>
        <add name="ContosoAdsContext" connectionString="{connectionstring}"
        providerName="System.Data.SqlClient" xdt:Transform="SetAttributes" xdt:Locator="Match(name)"/>
    </connectionStrings>
    ```

    Nechte soubor otevřený pro úpravy.
2. Na portálu [Azure Portal](https://portal.azure.com) klikněte v levém podokně na **Databáze SQL**, klikněte na databázi, kterou jste si pro tento kurz vytvořili, a potom klikněte na **Zobrazit připojovací řetězce**.

    ![Zobrazení připojovacích řetězců](./media/cloud-services-dotnet-get-started/showcs.png)

    Portál zobrazí připojovací řetězce, místo hesla uvidíte zástupný symbol.

    ![Připojovací řetězce](./media/cloud-services-dotnet-get-started/connstrings.png)
3. V transformačním souboru *Web.Release.config* odstraňte text `{connectionstring}` a na jeho místo vložte připojovací řetězec ADO.NET z portálu Azure Portal.
4. V připojovacím řetězci, který jste vložili do transformačního souboru*Web.Release.config*, nahraďte text `{your_password_here}` heslem, které jste vytvořili pro novou databázi SQL.
5. Uložte soubor.  
6. Vyberte a zkopírujte připojovací řetězec (bez okolních uvozovek), abyste ho mohli použít v následujících krocích konfigurace projektu role pracovního procesu.
7. V **Průzkumníku řešení** v části **Role** v projektu cloudové služby klikněte pravým tlačítkem na **ContosoAdsWorker** a potom klikněte na **Vlastnosti**.

    ![Vlastnosti rolí](./media/cloud-services-dotnet-get-started/rolepropertiesworker.png)
8. Klikněte na kartu **Nastavení**.
9. Změňte nastavení **Konfigurace služby** na **Cloud**.
10. V nastavení `ContosoAdsDbConnectionString` vyberte pole **Hodnota** a vložte do něj připojovací řetězec, který jste zkopírovali v předchozí části kurzu.

     ![Připojovací řetězec databáze pro roli pracovního procesu](./media/cloud-services-dotnet-get-started/workerdbcs.png)
11. Uložte provedené změny.  

### <a name="configure-the-solution-to-use-your-azure-storage-account-when-it-runs-in-azure"></a>Konfigurace řešení, aby při spuštění v Azure používalo účet úložiště Azure
Připojovací řetězce k účtu úložiště Azure pro projekt webové role i projekt role pracovního procesu jsou uložené v nastavení prostředí v projektu cloudové služby. Každý projekt má samostatnou sadu nastavení, která se použije při spuštění aplikace místně a při spuštění v cloudu. Nastavení cloudového prostředí budete aktualizovat pro webový projekt i pro projekt role pracovního procesu.

1. V **Průzkumníku řešení** v části **Role** v projektu **ContosoAdsCloudService** klikněte pravým tlačítkem na **ContosoAdsWeb** a potom na **Vlastnosti**.

    ![Vlastnosti rolí](./media/cloud-services-dotnet-get-started/roleproperties.png)
2. Klikněte na kartu **Nastavení**. V rozevíracím seznamu **Konfigurace služby** vyberte **Cloud**.

    ![Konfigurace cloudu](./media/cloud-services-dotnet-get-started/sccloud.png)
3. Vyberte položku **StorageConnectionString** a na pravém konci řádku se zobrazí tlačítko se třemi tečkami (**...**) . Kliknutím na tlačítko se třemi tečkami otevřete dialogové okno **Vytvoření připojovací řetězce k účtu úložiště**.

    ![Otevření pole Vytvoření připojovacího řetězce](./media/cloud-services-dotnet-get-started/opencscreate.png)
4. V dialogovém okně **Vytvoření připojovacího řetězce k úložišti** klikněte na **Předplatné**, zvolte účet úložiště, které jste už dříve vytvořili, a potom klikněte na tlačítko **OK**. Pokud ještě nejste přihlášeni, budete vyzváni k zadání přihlašovacích údajů k účtu Azure.

    ![Vytvoření připojovacího řetězce k úložišti](./media/cloud-services-dotnet-get-started/createstoragecs.png)
5. Uložte provedené změny.
6. Postupujte stejným způsobem, který jste použili pro připojovací řetězec `StorageConnectionString`, a nastavte připojovací řetězec `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString`.

    Tento připojovací řetězec se používá k protokolování.
7. Postupujte stejným způsobem, který jste použili pro roli **ContosoAdsWeb**, a nastavte oba připojovací řetězce pro roli **ContosoAdsWorker**. Nezapomeňte nastavit hodnotu **Konfigurace služby** na **Cloud**.

Nastavení prostředí role, které jste nakonfigurovali pomocí rozhraní Visual Studia, jsou uložená v následujících souborech v projektu ContosoAdsCloudService:

* *ServiceDefinition.csdef* – definuje názvy nastavení.
* *ServiceConfiguration.Cloud.cscfg* – poskytuje hodnoty pro situace, kdy aplikace běží v cloudu.
* *ServiceConfiguration.Local.cscfg* – poskytuje hodnoty pro situace, kdy aplikace běží místně.

Například soubor ServiceDefinition.csdef obsahuje následující definice:

```xml
<ConfigurationSettings>
    <Setting name="StorageConnectionString" />
    <Setting name="ContosoAdsDbConnectionString" />
</ConfigurationSettings>
```

A soubor *ServiceConfiguration.Cloud.cscfg* obsahuje hodnoty, které jste pro tato nastavení zadali ve Visual Studiu.

```xml
<Role name="ContosoAdsWorker">
    <Instances count="1" />
    <ConfigurationSettings>
        <Setting name="StorageConnectionString" value="{yourconnectionstring}" />
        <Setting name="ContosoAdsDbConnectionString" value="{yourconnectionstring}" />
        <!-- other settings not shown -->

    </ConfigurationSettings>
    <!-- other settings not shown -->

</Role>
```

Nastavení `<Instances>` určuje počet virtuálních počítačů, na kterých Azure spustí kód role pracovního procesu. Část [Další kroky](#next-steps) obsahuje odkazy na další informace o škálování cloudové služby.

### <a name="deploy-the-project-to-azure"></a>Nasazení projektu do Azure
1. V **Průzkumníku řešení** klikněte pravým tlačítkem na cloudový projekt **ContosoAdsCloudService** a potom vyberte **Publikovat**.

   ![Publikování nabídky](./media/cloud-services-dotnet-get-started/pubmenu.png)
2. V průvodci **Publikování aplikaci Azure** v kroku **Přihlásit se** klikněte na tlačítko **Další**.

    ![Krok Přihlásit se](./media/cloud-services-dotnet-get-started/pubsignin.png)
3. Klikněte v průvodci v kroku **Nastavení** na tlačítko **Další**.

    ![Krok Nastavení](./media/cloud-services-dotnet-get-started/pubsettings.png)

    Výchozí nastavení na kartě **Upřesnit** jsou pro účely tohoto kurzu vyhovující. Informace o kartě Upřesnit naleznete v článku o [průvodci publikováním aplikace Azure](https://docs.microsoft.com/azure/vs-azure-tools-publish-azure-application-wizard).
4. V kroku **Souhrn** klikněte na tlačítko **Publikovat**.

    ![Krok Souhrn](./media/cloud-services-dotnet-get-started/pubsummary.png)

   Ve Visual Studiu se otevře okno **Protokol činnosti Azure**.
5. Kliknutím na ikonu se šipkou doprava rozbalíte podrobnosti o nasazení.

    Dokončení nasazení může trvat 5 minut i déle.

    ![Okno Protokol činnosti Azure](./media/cloud-services-dotnet-get-started/waal.png)
6. Po dokončení nasazení klikněte na **Adresa URL webové aplikace** a spusťte aplikaci.
7. Teď můžete aplikaci otestovat a vytvořit, zobrazit nebo upravit některé reklamy, stejně jako jste to dělali, když byla aplikace spuštěná místně.

> [!NOTE]
> Až budete s testováním hotovi, odstraňte nebo zastavte cloudovou službu. Poplatky vám totiž nabíhají i když cloudové služby nepoužíváte, protože jsou pro ně vyhrazené prostředky virtuálních počítačů. A pokud je necháte spuštěné, může každý, kdo najde vaši adresu URL, vytvářet a zobrazovat reklamy. Na portálu [Azure Portal](https://portal.azure.com) přejděte na kartu **Přehled** vaší cloudové služby a potom v horní části stránky klikněte na tlačítko **Odstranit**. Pokud chcete ostatním jen dočasně znemožnit přístup na web, klikněte místo toho na tlačítko **Zastavit**. V takovém případě budou poplatky dál nabíhat. Podobným způsobem můžete odstranit nepotřebnou databázi SQL a účet úložiště.
>
>

## <a name="create-the-application-from-scratch"></a>Vytvoření aplikace od začátku
Pokud jste [dokončenou aplikaci](http://code.msdn.microsoft.com/Simple-Azure-Cloud-Service-e01df2e4) ještě nestáhli, udělejte to teď. Soubory ze staženého projektu budete kopírovat do nového projektu.

Vytvoření aplikace Contoso Ads zahrnuje následující kroky:

* Vytvoření řešení cloudové služby Visual Studio
* Aktualizace a přidání balíčků NuGet
* Nastavení odkazů projektu
* Konfigurace připojovacích řetězců
* Přidání souborů s kódy

Po vytvoření řešení zkontrolujete kód, který je pro projekty cloudových služeb a objekty blob a fronty Azure jedinečný.

### <a name="create-a-cloud-service-visual-studio-solution"></a>Vytvoření řešení cloudové služby Visual Studio
1. Ve Visual Studiu zvolte v nabídce **Soubor** možnost **Nový projekt**.
2. V levém podokně dialogového okna **Nový projekt** rozbalte položku **Visual C#**, vyberte šablonu **Cloud** a potom klikněte na šablonu **Cloudová služba Azure**.
3. Pojmenujte projekt a řešení ContosoAdsCloudService a potom klikněte na tlačítko **OK**.

    ![Nový projekt](./media/cloud-services-dotnet-get-started/newproject.png)
4. V dialogovém okně **Nová cloudová služba Azure** přidejte webovou roli a roli pracovního procesu. Pojmenujte webovou roli ContosoAdsWeb a roli pracovního procesu ContosoAdsWorker. (Ke změně výchozích názvů rolí použijte ikonu tužky v pravém podokně.)

    ![Nový projekt cloudové služby](./media/cloud-services-dotnet-get-started/newcsproj.png)
5. Po zobrazení dialogového okna **Nový projekt ASP.NET** pro webovou roli vyberte šablonu MVC a potom klikněte na **Změnit ověřování**.

    ![Změna ověřování](./media/cloud-services-dotnet-get-started/chgauth.png)
6. V dialogovém okně **Změna ověřování** vyberte **Bez ověřování** a potom klikněte na tlačítko **OK**.

    ![Bez ověřování](./media/cloud-services-dotnet-get-started/noauth.png)
7. V dialogovém okně **Nový projekt ASP.NET** klikněte na tlačítko **OK**.
8. V **Průzkumníku řešení** klikněte pravým tlačítkem na řešení (ne na projekt) a zvolte **Přidat – nový projekt**.
9. V dialogovém okně **Přidání nového projektu**  klikněte v levém podokně v části **Visual C#** na tlačítko **Windows** a potom klikněte na šablonu **Knihovna tříd**.  
10. Pojmenujte projekt *ContosoAdsCommon* a potom klikněte na tlačítko **OK**.

    Na kontext Entity Framework a datový model je třeba odkazovat z projektů webové role i role pracovního procesu. Jako alternativu můžete třídy související s EF definovat v projektu webové role a odkazovat na takový projekt z projektu role pracovního procesu. V tomto alternativním přístupu bude projekt role pracovního procesu obsahovat odkaz na webová sestavení, která nepotřebuje.

### <a name="update-and-add-nuget-packages"></a>Aktualizace a přidání balíčků NuGet
1. Otevřete dialogové okno **Správa balíčků NuGet** řešení.
2. V horní části okna vyberte **Aktualizace**.
3. Najděte balíček *WindowsAzure.Storage* a pokud je v seznamu, vyberte ho a vyberte webový projekt a projekt pracovního procesu, ve kterých ho chcete aktualizovat, a potom klikněte na **Aktualizace**.

    Knihovna klienta úložiště se aktualizuje častěji než šablony projektů Visual Studio, takže se často stává, že verzi u nově vytvořeného projektu je potřeba aktualizovat.
4. V horní části okna vyberte **Procházet**.
5. Najděte balíček NuGet *EntityFramework* a nainstalujte ho do všech tří projektů.
6. Najděte balíček NuGet *Microsoft.WindowsAzure.ConfigurationManager* a nainstalujte ho do projektu role pracovního procesu.

### <a name="set-project-references"></a>Nastavení odkazů na projekty
1. V projektu ContosoAdsWeb nastavte odkaz na projekt ContosoAdsCommon. Klikněte pravým tlačítkem na projekt ContosoAdsWeb a potom klikněte na **Odkazy** - **Přidat odkazy**. V dialogovém okně **Správce odkazů** vyberte v levém podokně **Řešení – projekty**, vyberte **ContosoAdsCommon** a potom klikněte na tlačítko **OK**.
2. V projektu ContosoAdsWorker nastavte odkaz na projekt ContosAdsCommon.

    ContosoAdsCommon bude obsahovat datový model a třídu kontextu Entity Framework, které použije front-end i back-end.
3. V projektu ContosoAdsWorker nastavte odkaz na `System.Drawing`.

    Back-end toto sestavení používá k převodu obrázků na miniatury.

### <a name="configure-connection-strings"></a>Konfigurace připojovacích řetězců
V této části budete konfigurovat službu Azure Storage a připojovací řetězce SQL pro místní testování. Pokyny pro nasazení (uvedené už dříve) vysvětlují, jak nastavit připojovací řetězce pro situaci, kdy aplikace běží v cloudu.

1. V projektu ContosoAdsWeb otevřete aplikační soubor Web.config a vložte následující prvek `connectionStrings` za prvek `configSections`.

    ```xml
    <connectionStrings>
        <add name="ContosoAdsContext" connectionString="Data Source=(localdb)\v11.0; Initial Catalog=ContosoAds; Integrated Security=True; MultipleActiveResultSets=True;" providerName="System.Data.SqlClient" />
    </connectionStrings>
    ```

    Pokud používáte Visual Studio 2015 nebo vyšší, nahraďte text „v11.0“ textem „MSSQLLocalDB“.
2. Uložte provedené změny.
3. Klikněte v projektu ContosoAdsCloudService pravým tlačítkem v části **Role** na ContosoAdsWeb a potom klikněte na **Vlastnosti**.

    ![Vlastnosti rolí](./media/cloud-services-dotnet-get-started/roleproperties.png)
4. V okně vlastností **ContosAdsWeb [Role]** klikněte na kartu **Nastavení** a potom na **Přidat nastavení**.

    Možnost **Konfigurace služby** nechte nastavenou na **Všechny konfigurace**.
5. Přidejte nastavení s názvem *StorageConnectionString*. Nastavte **Typ** na *ConnectionString* a možnost **Hodnota** nastavte na *UseDevelopmentStorage=true*.

    ![Nový připojovací řetězec](./media/cloud-services-dotnet-get-started/scall.png)
6. Uložte provedené změny.
7. Postupujte stejným způsobem a přidejte připojovací řetězec úložiště do vlastností role ContosoAdsWorker.
8. Ještě v okně vlastností **ContosoAdsWorker [Role]** přidejte další připojovací řetězec:

   * Název: ContosoAdsDbConnectionString
   * Typ: Řetězec
   * Hodnota: Vložte stejný připojovací řetězec, který jste použili pro projekt webové role. (Následující příklad je určený pro Visual Studio 2013. Pokud tento příklad kopírujete a používáte Visual Studio 2015 nebo vyšší, nezapomeňte změnit zdroj dat.)

       ```
       Data Source=(localdb)\v11.0; Initial Catalog=ContosoAds; Integrated Security=True; MultipleActiveResultSets=True;
       ```

### <a name="add-code-files"></a>Přidání souborů s kódy
V této části zkopírujete soubory s kódy ze staženého řešení do nového řešení. Následující části vám ukáží a vysvětlí klíčová místa tohoto kódu.

Pokud chcete přidat soubory do projektu nebo složky, klikněte pravým tlačítkem na projekt nebo složku a potom klikněte na **Přidat** - **Existující položka**. Vyberte požadované soubory a potom klikněte na tlačítko **Přidat**. Pokud se zobrazí dotaz, jestli chcete nahradit existující soubory, klikněte na **Ano**.

1. V projektu ContosoAdsCommon odstraňte soubor *Class1.cs* a na jeho místo přidejte soubory *Ad.cs* a *ContosoAdscontext.cs* ze staženého projektu.
2. Do projektu ContosoAdsWeb přidejte následující soubory ze staženého projektu.

   * *Global.asax.cs*.  
   * Do složky *Views\Shared*: *\_Layout.cshtml*.
   * Do složky *Views\Home*: *Index.cshtml*.
   * Do složky *Controllers*: *AdController.cs*.
   * Do složky *Views\Ad* (nejdřív složku vytvořte): pět souborů *.cshtml*.
3. Do projektu ContosoAdsWorker přidejte soubor *WorkerRole.cs* ze staženého projektu.

Teď můžete sestavit a spustit aplikaci podle dříve uvedených pokynů tohoto kurzu a aplikace bude používat prostředky místní databáze a emulátoru úložiště.

Následující části popisují kód týkající se práce s prostředím Azure, objekty blob a frontami. Tento kurzu nevysvětluje, jak máte vytvořit kontrolery a zobrazení MVC pomocí generování uživatelského rozhraní, jak napsat kód Entity Framework, který funguje s databází serveru SQL Server, ani nepopisuje základy asynchronního programování v technologii ASP.NET 4.5. Informace o těchto tématech naleznete v následujících zdrojích:

* [Začínáme s MVC 5](http://www.asp.net/mvc/tutorials/mvc-5/introduction/getting-started)
* [Začínáme s EF 6 a MVC 5](http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc)
* [Úvod do asynchronního programování na platformě .NET 4.5](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/web-development-best-practices#async).

### <a name="contosoadscommon---adcs"></a>ContosoAdsCommon – Ad.cs
Soubor Ad.cs definuje výčet kategorií reklam a třídu entity objektů POCO pro informace o reklamách.

```csharp
public enum Category
{
    Cars,
    [Display(Name="Real Estate")]
    RealEstate,
    [Display(Name = "Free Stuff")]
    FreeStuff
}

public class Ad
{
    public int AdId { get; set; }

    [StringLength(100)]
    public string Title { get; set; }

    public int Price { get; set; }

    [StringLength(1000)]
    [DataType(DataType.MultilineText)]
    public string Description { get; set; }

    [StringLength(1000)]
    [DisplayName("Full-size Image")]
    public string ImageURL { get; set; }

    [StringLength(1000)]
    [DisplayName("Thumbnail")]
    public string ThumbnailURL { get; set; }

    [DataType(DataType.Date)]
    [DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
    public DateTime PostedDate { get; set; }

    public Category? Category { get; set; }
    [StringLength(12)]
    public string Phone { get; set; }
}
```

### <a name="contosoadscommon---contosoadscontextcs"></a>ContosoAdsCommon – ContosoAdsContext.cs
Třída ContosoAdsContext určuje použití třídy reklamy v kolekci DbSet, kterou Entity Framework uloží do databáze SQL.

```csharp
public class ContosoAdsContext : DbContext
{
    public ContosoAdsContext() : base("name=ContosoAdsContext")
    {
    }
    public ContosoAdsContext(string connString)
        : base(connString)
    {
    }
    public System.Data.Entity.DbSet<Ad> Ads { get; set; }
}
```

Třída má dva konstruktory. První z nich používán webovým projektem a určuje název připojovacího řetězce, který je uložený v souboru Web.config. Druhý konstruktor vám umožňuje předat samotný připojovací řetězec používaný projektem role pracovního projektu, protože nemá soubor Web.config. Už dříve jste viděli, kam se tento připojovací řetězec uložil, a později uvidíte, jak kód získává připojovací řetězec při vytvoření instance třídy DbContext.

### <a name="contosoadsweb---globalasaxcs"></a>ContosoAdsWeb – Global.asax.cs
Kód, který se volá z metody `Application_Start`, vytvoří kontejner objektů blob s *obrázky* a frontu *obrázků*, pokud ještě neexistují. To zajišťuje, že při každém spuštění pomocí nového účtu úložiště nebo při spuštění pomocí emulátoru úložiště v novém počítači budou požadovaný kontejner objektů blob a fronta vytvořeny automaticky.

Kód získá přístup k účtu úložiště pomocí připojovacího řetězec úložiště ze souboru *.cscfg*.

```csharp
var storageAccount = CloudStorageAccount.Parse
    (RoleEnvironment.GetConfigurationSettingValue("StorageConnectionString"));
```

Potom získá odkaz na kontejner objektů blob s *obrázky*, pokud kontejner neexistuje, tak ho vytvoří, a nastaví přístupová oprávnění na nový kontejner. Ve výchozím nastavení povolí nové kontejnery přístup k objektům blob jenom klientům s přihlašovacími údaji k účtu úložiště. Web potřebuje, aby objekty blob byly veřejné, jinak by nemohl zobrazit obrázky pomocí adres URL, které odkazují na objekty blob s obrázky.

```csharp
var blobClient = storageAccount.CreateCloudBlobClient();
var imagesBlobContainer = blobClient.GetContainerReference("images");
if (imagesBlobContainer.CreateIfNotExists())
{
    imagesBlobContainer.SetPermissions(
        new BlobContainerPermissions
        {
            PublicAccess =BlobContainerPublicAccessType.Blob
        });
}
```

Podobný kód získá odkaz na frontu *obrázků* a vytvoří novou frontu. V tomto případě nejsou nutná žádná oprávnění.

```csharp
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
var imagesQueue = queueClient.GetQueueReference("images");
imagesQueue.CreateIfNotExists();
```

### <a name="contosoadsweb---layoutcshtml"></a>ContosoAdsWeb – \_Layout.cshtml
Soubor *_Layout.cshtml* nastaví název aplikace v záhlaví a zápatí a vytvoří položku nabídky „Reklamy“.

### <a name="contosoadsweb---viewshomeindexcshtml"></a>ContosoAdsWeb – Views\Home\Index.cshtml
Soubor *Views\Home\Index.cshtml* zobrazuje na domovské stránce odkazy na kategorie. Odkazy předají celočíselnou hodnotu výčtu `Category` v proměnné řetězce dotazu na indexovou stránku reklam.

```razor
<li>@Html.ActionLink("Cars", "Index", "Ad", new { category = (int)Category.Cars }, null)</li>
<li>@Html.ActionLink("Real estate", "Index", "Ad", new { category = (int)Category.RealEstate }, null)</li>
<li>@Html.ActionLink("Free stuff", "Index", "Ad", new { category = (int)Category.FreeStuff }, null)</li>
<li>@Html.ActionLink("All", "Index", "Ad", null, null)</li>
```

### <a name="contosoadsweb---adcontrollercs"></a>ContosoAdsWeb – AdController.cs
V souboru *AdController.cs* volá konstruktor metodu `InitializeStorage`, aby vytvořil objekty knihovny klienta služby Azure Storage, které poskytují rozhraní API pro práci s objekty blob a frontami.

Potom kód získá odkaz na kontejner objektů blob s *obrázky*, jak už jste viděli v souboru *Global.asax.cs*. Během toho nastaví výchozí [zásady opakování](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/transient-fault-handling), které jsou vhodné pro webovou aplikaci. Výchozí zásady opakování exponenciálního omezení rychlosti můžou způsobit, že webová aplikace přestane při opakovaných pokusech reagovat na dobu delší než jednu minutu. Důvodem může být přechodná chyba. Zde určené zásady opakování čekají po každém pokusu tři sekundy a celkem provádějí tři pokusy.

```csharp
var blobClient = storageAccount.CreateCloudBlobClient();
blobClient.DefaultRequestOptions.RetryPolicy = new LinearRetry(TimeSpan.FromSeconds(3), 3);
imagesBlobContainer = blobClient.GetContainerReference("images");
```

Podobný kód získá odkaz na frontu *obrázků*.

```csharp
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
queueClient.DefaultRequestOptions.RetryPolicy = new LinearRetry(TimeSpan.FromSeconds(3), 3);
imagesQueue = queueClient.GetQueueReference("images");
```

Většinu kódu kontroleru je typická pro práci s datovým modelem Entity Framework za použití třídy DbContext. Výjimkou je metoda HttpPost `Create`, která soubor odešle a uloží ho do úložiště objektů blob. Vazač modelu poskytuje metodě objekt [HttpPostedFileBase](http://msdn.microsoft.com/library/system.web.httppostedfilebase.aspx).

```csharp
[HttpPost]
[ValidateAntiForgeryToken]
public async Task<ActionResult> Create(
    [Bind(Include = "Title,Price,Description,Category,Phone")] Ad ad,
    HttpPostedFileBase imageFile)
```

Pokud uživatel vybral soubor k odeslání, kód soubor odešle, uloží ho do objektu blob a zaktualizuje záznam v databázi reklam pomocí adresy URL, která odkazuje na objekt blob.

```csharp
if (imageFile != null && imageFile.ContentLength != 0)
{
    blob = await UploadAndSaveBlobAsync(imageFile);
    ad.ImageURL = blob.Uri.ToString();
}
```

Kód, který odesílání provádí, je umístěný v metodě `UploadAndSaveBlobAsync`. Vytvoří identifikátor GUID pro objekt blob, odešle a uloží soubor a vrátí odkaz na uložený objekt blob.

```csharp
private async Task<CloudBlockBlob> UploadAndSaveBlobAsync(HttpPostedFileBase imageFile)
{
    string blobName = Guid.NewGuid().ToString() + Path.GetExtension(imageFile.FileName);
    CloudBlockBlob imageBlob = imagesBlobContainer.GetBlockBlobReference(blobName);
    using (var fileStream = imageFile.InputStream)
    {
        await imageBlob.UploadFromStreamAsync(fileStream);
    }
    return imageBlob;
}
```

Když metoda HttpPost `Create` odešle objekt blob a zaktualizuje databázi, vytvoří zprávu fronty, aby informovala back-endový proces, že obrázek je připravený k převodu na miniaturu.

```csharp
string queueMessageString = ad.AdId.ToString();
var queueMessage = new CloudQueueMessage(queueMessageString);
await queue.AddMessageAsync(queueMessage);
```

Kód metody HttpPost `Edit` je podobný s tím rozdílem, že pokud uživatel vybere nový obrázkový soubor, veškeré existující objekty blob musí být odstraněny.

```csharp
if (imageFile != null && imageFile.ContentLength != 0)
{
    await DeleteAdBlobsAsync(ad);
    imageBlob = await UploadAndSaveBlobAsync(imageFile);
    ad.ImageURL = imageBlob.Uri.ToString();
}
```

Další příklad ukazuje kód, který odstraní objekty blob při odstranění reklamy.

```csharp
private async Task DeleteAdBlobsAsync(Ad ad)
{
    if (!string.IsNullOrWhiteSpace(ad.ImageURL))
    {
        Uri blobUri = new Uri(ad.ImageURL);
        await DeleteAdBlobAsync(blobUri);
    }
    if (!string.IsNullOrWhiteSpace(ad.ThumbnailURL))
    {
        Uri blobUri = new Uri(ad.ThumbnailURL);
        await DeleteAdBlobAsync(blobUri);
    }
}
private static async Task DeleteAdBlobAsync(Uri blobUri)
{
    string blobName = blobUri.Segments[blobUri.Segments.Length - 1];
    CloudBlockBlob blobToDelete = imagesBlobContainer.GetBlockBlobReference(blobName);
    await blobToDelete.DeleteAsync();
}
```

### <a name="contosoadsweb---viewsadindexcshtml-and-detailscshtml"></a>ContosoAdsWeb – Views\Ad\Index.cshtml a Details.cshtml
Soubor *Index.cshtml* zobrazí miniatury s dalšími daty reklam.

```razor
<img src="@Html.Raw(item.ThumbnailURL)" />
```

Soubor *Details.cshtml* zobrazí obrázek v plné velikosti.

```razor
<img src="@Html.Raw(Model.ImageURL)" />
```

### <a name="contosoadsweb---viewsadcreatecshtml-and-editcshtml"></a>ContosoAdsWeb – Views\Ad\Create.cshtml a Edit.cshtml
Soubory *Create.cshtml* a *Edit.cshtml* určují kódování formuláře, které kontroleru umožňuje získání objektu `HttpPostedFileBase`.

```razor
@using (Html.BeginForm("Create", "Ad", FormMethod.Post, new { enctype = "multipart/form-data" }))
```

Prvek `<input>` sděluje prohlížeči, aby zobrazil dialogové okno pro výběr souboru.

```razor
<input type="file" name="imageFile" accept="image/*" class="form-control fileupload" />
```

### <a name="contosoadsworker---workerrolecs---onstart-method"></a>ContosoAdsWorker – WorkerRole.cs – metoda OnStart 
Prostředí role pracovního procesu Azure volá metodu `OnStart` ve třídě `WorkerRole`, když se spouští role pracovního procesu, a volá metodu `Run`, když se metoda `OnStart` dokončí.

Metoda `OnStart` získá připojovací řetězec databáze ze souboru *.cscfg* a předá ho do třídy DbContext v Entity Framework. Poskytovatel SQLClienta se používá ve výchozím nastavení, takže ho není nutné zadávat.

```csharp
var dbConnString = CloudConfigurationManager.GetSetting("ContosoAdsDbConnectionString");
db = new ContosoAdsContext(dbConnString);
```

Potom metoda získá odkaz na účet úložiště a vytvoří kontejner objektů blob a frontu (pokud ještě neexistují). Kód pro tuto akci je podobný kódu, který jste už viděli v metodě webové role `Application_Start`.

### <a name="contosoadsworker---workerrolecs---run-method"></a>ContosoAdsWorker – WorkerRole.cs – metoda Run
Metoda `Run` se volá, když metoda `OnStart` dokončí svoji inicializaci. Metoda spustí nekonečnou smyčku, která sleduje nové zprávy fronty a po jejich příchodu je zpracuje.

```csharp
public override void Run()
{
    CloudQueueMessage msg = null;

    while (true)
    {
        try
        {
            msg = this.imagesQueue.GetMessage();
            if (msg != null)
            {
                ProcessQueueMessage(msg);
            }
            else
            {
                System.Threading.Thread.Sleep(1000);
            }
        }
        catch (StorageException e)
        {
            if (msg != null && msg.DequeueCount > 5)
            {
                this.imagesQueue.DeleteMessage(msg);
            }
            System.Threading.Thread.Sleep(5000);
        }
    }
}
```

Po každé iteraci smyčky, kdy nebyla nalezena žádná zpráva fronty, se program na sekundu uspí. Tím se roli pracovního procesu zabrání, aby nadměrně nezvyšovala náklady na čas procesoru a transakce úložiště. Poradní tým Microsoftu vypráví příběh o vývojáři, který tohle zapomněl zohlednit, nasadil kód do výroby a odjel na dovolenou. Když se vrátil zpět, byl účet za dohled vyšší než cena jeho dovolené.

Obsah zprávy fronty občas způsobí chybu při zpracování. Takové zprávě se říká *nezpracovatelná zpráva* a pokud jste právě zaprotokolovali chybu a restartovali smyčku, můžete se pokoušet o zpracování této zprávy do nekonečna.  Zachycující blok proto zahrnuje podmínku, která kontroluje, jak často se aplikace pokusila aktuální zprávu zpracovat a pokud to bylo víc než pětkrát, odstraní zprávu z fronty.

`ProcessQueueMessage` se volá při nalezení zpráv fronty.

```csharp
private void ProcessQueueMessage(CloudQueueMessage msg)
{
    var adId = int.Parse(msg.AsString);
    Ad ad = db.Ads.Find(adId);
    if (ad == null)
    {
        throw new Exception(String.Format("AdId {0} not found, can't create thumbnail", adId.ToString()));
    }

    CloudBlockBlob inputBlob = this.imagesBlobContainer.GetBlockBlobReference(ad.ImageURL);

    string thumbnailName = Path.GetFileNameWithoutExtension(inputBlob.Name) + "thumb.jpg";
    CloudBlockBlob outputBlob = this.imagesBlobContainer.GetBlockBlobReference(thumbnailName);

    using (Stream input = inputBlob.OpenRead())
    using (Stream output = outputBlob.OpenWrite())
    {
        ConvertImageToThumbnailJPG(input, output);
        outputBlob.Properties.ContentType = "image/jpeg";
    }

    ad.ThumbnailURL = outputBlob.Uri.ToString();
    db.SaveChanges();

    this.imagesQueue.DeleteMessage(msg);
}
```

Tento kód čte databázi, aby získal adresu URL obrázku, převede obrázek na miniaturu, uloží miniaturu do objektu blob, zaktualizuje databázi pomocí adresy URL odkazující na miniaturu v objektu blob a odstraní zprávu fronty.

> [!NOTE]
> Kód v metodě `ConvertImageToThumbnailJPG` používá pro zjednodušení třídy v oboru názvů System.Drawing. Třídy v tomto oboru názvů však byly navrženy pro používání s formuláři Windows. Jejich používání není podporované ve Windows a službě ASP.NET. Další informace o možnostech zpracování obrázků najdete v článcích o [dynamickém generování obrázků](http://www.hanselman.com/blog/BackToBasicsDynamicImageGenerationASPNETControllersRoutingIHttpHandlersAndRunAllManagedModulesForAllRequests.aspx) a o [hloubkové změně velikosti uvnitř obrázků](http://www.hanselminutes.com/313/deep-inside-image-resizing-and-scaling-with-aspnet-and-iis-with-imageresizingnet-author-na).
>
>

## <a name="troubleshooting"></a>Řešení potíží
Pokud by vám při procházení kurzem něco nefungovalo, následuje přehled běžných chyb a jejich řešení.

### <a name="serviceruntimeroleenvironmentexception"></a>ServiceRuntime.RoleEnvironmentException
Azure poskytne objekt `RoleEnvironment` při spuštění aplikace v Azure nebo při spuštění místně pomocí emulátoru služby Výpočty v Azure.  Pokud se tato chyba objeví, když aplikaci spouštíte místně, zkontrolujte, jestli jste projekt ContosoAdsCloudService nastavili jako spouštěný projekt. Toto nastaví projekt tak, aby běžel pomocí emulátoru služby Výpočty v Azure.

Jedna z věcí, ke kterým aplikace používá RoleEnvironment Azure, je získání hodnot připojovacích řetězců, které jsou uložené v souborech *.cscfg*, takže další možnou příčinou této výjimky je chybějící připojovací řetězec. Zkontrolujte, jestli jste v projektu ContosoAdsWeb vytvořili nastavení StorageConnectionString pro cloudovou i místní konfiguraci a jestli jste vytvořili oba připojovací řetězce pro obě konfigurace i v projektu ContosoAdsWorker. Pokud budete StorageConnectionString hledat pomocí možnosti **Najít všechny** v celém řešení, mělo by se zobrazit devětkrát v šesti souborech.

### <a name="cannot-override-to-port-xxx-new-port-below-minimum-allowed-value-8080-for-protocol-http"></a>Nejde přepsat na port xxx. Nový port s nižší než minimální povolenou hodnotou 8080 pro protokol http
Změňte číslo portu, který používáte pro webový projekt. Klikněte pravým tlačítkem na projekt ContosoAdsWeb a potom klikněte na **Vlastnosti**. Klikněte na kartu **Web** a potom v nastavení **Adresa URL projektu** změňte číslo portu.

Další alternativní řešení problému najdete v následující části.

### <a name="other-errors-when-running-locally"></a>Další chyby při místním spuštění
Nové projekty cloudových služeb ve výchozím nastavení používají expresní emulátor služby Výpočty v Azure k simulaci prostředí Azure. Jedná se o odlehčenou verzi úplného emulátoru služby Výpočty a za určitých podmínek bude úplný emulátor fungovat, když expresní verze nepracuje.  

Pokud chcete změnit projekt, který používá úplný emulátor, klikněte pravým tlačítkem na projekt ContosoAdsCloudService a potom na **Vlastnosti**. V okně **Vlastnosti** klikněte na kartu **Web** a potom na přepínač **Použít úplný emulátor**.

Pokud chcete aplikaci spustit s úplným emulátorem, otevřete Visual Studio s oprávněními správce.

## <a name="next-steps"></a>Další kroky
Aplikace Contoso Ads je kvůli úvodnímu kurzu záměrně jednoduchá. Například neimplementuje [vkládání závislostí](http://www.asp.net/mvc/tutorials/hands-on-labs/aspnet-mvc-4-dependency-injection) nebo [úložiště a jednotky pracovních vzorů](http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/advanced-entity-framework-scenarios-for-an-mvc-web-application#repo), nepodporuje [používání rozhraní k protokolování](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry#log), nepoužívá [migrace Code First EF](http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application) ke správě změn datových modelů nebo [odolnost připojení EF](http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application) ke správě přechodných síťových chyb a tak dále.

Níže uvádíme několik ukázkových aplikací cloudových služeb, které předvádějí realističtější postupy kódování (jsou řazené od méně složitých po složitější):

* [PhluffyFotos](http://code.msdn.microsoft.com/PhluffyFotos-Sample-7ecffd31). Podobný koncept jako Contoso Ads, ale implementuje víc funkcí a realističtější postupy kódování.
* [Vícevrstvé aplikace cloudových služeb Azure s tabulkami, frontami a objekty blob](http://code.msdn.microsoft.com/windowsazure/Windows-Azure-Multi-Tier-eadceb36). Představuje tabulky služby Azure Storage a také objekty blob a fronty. Vychází ze starší verzi sady Azure SDK pro .NET. Bude vyžadovat určité změny pro práci s aktuální verzí.

Obecné informace o vývoji pro cloud najdete v článku o [vytváření reálných cloudových aplikací s Azure](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/introduction).

Video úvod do osvědčených postupů a vzorů služby Azure Storage najdete v článku [Služba Microsoft Azure Storage – novinky, osvědčené postupy a vzory](http://channel9.msdn.com/Events/Build/2014/3-628).

Další informace najdete v následujících materiálech:

* [Cloudové služby Azure Cloud Services část 1: Úvod](http://justazure.com/microsoft-azure-cloud-services-part-1-introduction/)
* [Jak spravovat Cloud Services](cloud-services-how-to-manage-portal.md)
* [Azure Storage](https://docs.microsoft.com/azure/storage/)
* [Jak vybrat poskytovatele cloudových služeb](https://azure.microsoft.com/overview/choosing-a-cloud-service-provider/)
