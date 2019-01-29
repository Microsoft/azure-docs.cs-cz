---
title: Nasazení hybridního cloudu s Azure a Azure Stack | Dokumentace Microsoftu
description: Zjistěte, jak nasadit hybridní cloud s Azure a Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/25/2019
ms.author: mabrigg
ms.reviewer: anajod
ms.openlocfilehash: cedc1ec84c049d779f09c31be1aa1a3af63dfe5b
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2019
ms.locfileid: "55094151"
---
# <a name="tutorial-deploy-a-hybrid-cloud-solution-with-azure-and-azure-stack"></a>Kurz: Nasazení řešení s hybridní cloud s Azure a Azure Stack

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

V tomto kurzu se dozvíte, jak nasadit hybridní cloudové řešení, která používá veřejného cloudu Azure a soukromého cloudu služby Azure Stack.

Pomocí jediného hybridního cloudového řešení, můžete kombinovat dodržování předpisů výhod privátního cloudu s škálovatelnosti veřejného cloudu. Vývojáři navíc můžete využít ekosystém společnosti Microsoft pro vývojáře a použít své dovednosti v cloudu i v místním prostředí.

## <a name="overview-and-assumptions"></a>Přehled a předpoklady

V tomto kurzu nastavit pracovní postup, který umožňuje vývojářům nasadit identické webovou aplikaci a veřejného cloudu, privátního cloudu. Tato aplikace bude mít přístup k Internetu jiných směrovatelné síti hostitelem privátního cloudu. Tyto webové aplikace se monitorují a když prudký nárůst v provozu, program změní záznamy DNS tak, aby přesměrování provozu na veřejném cloudu. Když provoz klesne na úroveň před zásobníku, provoz se směruje zpět do privátního cloudu.

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> - Nasazení serveru hybridní připojení databáze serveru SQL Server.
> - Připojení webové aplikace v globálním Azure pro hybridní síť.
> - Konfigurace DNS pro škálování cloudu.
> - Konfigurace certifikátů SSL pro škálování cloudu.
> - Nakonfigurujte a nasaďte webovou aplikaci.
> - Vytvořit profil služby Traffic Manager a nakonfigurovat jej pro škálování cloudu.
> - Nastavení Application Insights, monitorování a upozorňování pro zvýšení provozu.
> - Konfigurace automatického přepínání mezi globální Azure a Azure Stackem provoz.

### <a name="assumptions"></a>Předpoklady

V tomto kurzu se předpokládá, že máte základní znalosti o globální Azure a Azure Stack. Pokud chcete další informace před zahájením tohoto kurzu, přečtěte si tyto články:

 - [Úvod do Azure](https://azure.microsoft.com/overview/what-is-azure/)
 - [Klíčové koncepty služby Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-key-features)

Tento kurz předpokládá také, že máte předplatné Azure. Pokud předplatné nemáte, můžete si [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) předtím, než začnete.

## <a name="prerequisites"></a>Požadavky

Než začnete tento kurz, ujistěte se, že splňujete následující požadavky:

- Azure Stack Development Kit (ASDK) nebo předplatné na integrovaný systém Azure Stack. Chcete-li nasadit Azure Stack Development Kit, postupujte podle pokynů v [nasazení ASDK pomocí instalačního programu](../asdk/asdk-deploy.md).
- Instalace služby Azure Stack by měl mít nainstalované tyto položky:
  - Azure App Service. Práce s Azure Stack operátor k nasazení a konfiguraci služby Azure App Service ve vašem prostředí. Tento kurz vyžaduje služby App Service mají alespoň jedno (1) dostupné vyhrazených pracovních serverů roli.
  - Image Windows serveru 2016
  - Windows Server 2016 s bitovou kopii systému Microsoft SQL Server
  - Příslušné plány a nabídky
 - A domain name for your web application. Pokud nemáte název domény můžete zakoupit od poskytovatele domény, jako je například GoDaddy, Bluehost a InMotion.
- Certifikát SSL od důvěryhodné certifikační autority, jako je například LetsEncrypt vaší domény.
- Webové aplikace, která komunikuje s databází serveru SQL Server a podporuje Application Insights. Můžete stáhnout [dotnetcore-sqldb-tutorial](https://github.com/Azure-Samples/dotnetcore-sqldb-tutorial) ukázkovou aplikaci z Githubu.
- Hybridní síť mezi virtuální sítí Azure a virtuální sítě Azure Stack. Podrobné pokyny najdete v tématu [konfigurace připojení k hybridní cloud s Azure a Azure Stackem](azure-stack-solution-hybrid-connectivity.md).

- Hybridní kanál průběžné integrace a nasazování (CI/CD) s privátní agenta sestavení ve službě Azure Stack. Podrobné pokyny najdete v tématu [konfigurace hybridní Cloudová identita s aplikacemi Azure a Azure Stack](azure-stack-solution-hybrid-identity.md)

## <a name="deploy-a-hybrid-connected-sql-server-database-server"></a>Nasazení databáze serveru SQL Server hybridní připojení

1. Přihlaste se k portálu pro uživatele Azure stacku.

2. Na **řídicí panel**vyberte **Marketplace**.

    ![Marketplace služby Azure Stack](media/azure-stack-solution-hybrid-cloud/image1.png)

3. V **Marketplace**vyberte **Compute**a klikněte na tlačítko **Další**. V části **Další**, vyberte **bezplatná licence SQL serveru: SQL Server 2017 Developer v systému Windows Server** bitové kopie.

    ![Vyberte image virtuálního počítače](media/azure-stack-solution-hybrid-cloud/image2.png)

4. Na **bezplatná licence SQL serveru: SQL Server 2017 Developer v systému Windows Server** vyberte **vytvořit**.

5. Na **základy > Konfigurace základního nastavení**, zadejte **název** pro virtuální počítač (VM), **uživatelské jméno** pro správce systému SQL Server a **heslo** pro přidružení zabezpečení.  Z **předplatné** rozevíracího seznamu vyberte předplatné, které nasazujete. Pro **skupiny prostředků**, použijte **zvolit existující** a umístí virtuální počítač ve stejné skupině prostředků jako webovou aplikaci Azure Stack.

    ![Konfigurace základního nastavení pro virtuální počítač](media/azure-stack-solution-hybrid-cloud/image3.png)

6. V části **velikost**, vyberte velikost virtuálního počítače. V tomto kurzu doporučujeme A2_Standard nebo DS2_V2_Standard.

7. V části **Nastavení > Konfigurace volitelných funkcí**, nakonfigurujte následující nastavení:

    - **Účet úložiště**. Pokud budete potřebovat vytvořte nový účet.
    - **Virtuální síť**

      > [!Important]  
      > Ujistěte se, že váš virtuální počítač s SQL serverem je nasazen ve stejné virtuální síti jako brány sítě VPN.

    - **Veřejná IP adresa:** Můžete použít výchozí nastavení.
    - **Skupina zabezpečení sítě** (NSG). Vytvoření nové skupiny zabezpečení sítě.
    - **Monitorování a rozšíření**. Nechte výchozí nastavení.
    - **Účet úložiště diagnostiky**. Pokud budete potřebovat vytvořte nový účet.
    - Vyberte **OK** uložte konfiguraci.

    ![Konfigurace volitelných funkcí](media/azure-stack-solution-hybrid-cloud/image4.png)

1. V části **nastavení systému SQL Server**, nakonfigurujte následující nastavení:
   - Pro **připojení SQL**kliknutím **veřejné (Internet)**.
   - Pro **Port**, ponechte výchozí nastavení, **1433**.
   - Pro **ověřování SQL**vyberte **povolit**.

     > [!Note]  
     > Když povolíte ověřování SQL, ji by měl automaticky vyplnit pole s informacemi o "SQLAdmin", který jste nakonfigurovali v **Základy**.

   - Pro zbytek nastavení ponechte výchozí nastavení. Vyberte **OK**.

    ![Konfigurace nastavení SQL serveru](media/azure-stack-solution-hybrid-cloud/image5.png)

9. Na **Souhrn**, zkontrolujte konfiguraci virtuálního počítače a pak vyberte **OK** ke spuštění nasazení.

    ![Souhrn konfigurace](media/azure-stack-solution-hybrid-cloud/image6.png)

10. Bude trvat nějakou dobu vytvoření nového virtuálního počítače. Můžete zobrazit stav svých virtuálních počítačů v **virtuálních počítačů**.

    ![Virtuální počítače](media/azure-stack-solution-hybrid-cloud/image7.png)

## <a name="create-web-apps-in-azure-and-azure-stack"></a>Vytvoření webové aplikace v Azure a Azure Stack

Azure App Service zjednodušuje spouštění a správu webových aplikací. Protože Azure Stack je konzistentní s Azure, App Service umožňuje spuštění v obou prostředích. Služby App Service budete používat k hostování vaší aplikace.

### <a name="create-web-apps"></a>Vytvoření webové aplikace

1. Vytvoření webové aplikace v Azure podle pokynů v [spravovat plán služby App Service v Azure](https://docs.microsoft.com/azure/app-service/app-service-plan-manage#create-an-app-service-plan). Ujistěte se, že kam si ukládáte webovou aplikaci ve stejném předplatném a skupině prostředků jako hybridní síť.

2. Opakujte předchozí krok (1) ve službě Azure Stack.

### <a name="add-route-for-azure-stack"></a>Přidání trasy pro Azure Stack

App Service ve službě Azure Stack musí být směrovatelné z veřejného Internetu, aby mohli uživatelé získat přístup k vaší aplikaci. Pokud služby Azure Stack je přístupný z Internetu, ujistěte se, poznamenejte si veřejnou IP adresu nebo adresy URL pro webovou aplikaci Azure Stack.

Pokud používáte ASDK, můžete si [nakonfigurovat statické mapování NAT](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-vpn-connection-one-node#configure-the-nat-virtual-machine-on-each-azure-stack-development-kit-for-gateway-traversal) vystavit vně virtuálního prostředí App Service.

### <a name="connect-a-web-app-in-azure-to-a-hybrid-network"></a>Připojení k síti hybridní webové aplikace v Azure

Aby bylo zajištěno připojení mezi webový front-end v Azure a databázi serveru SQL Server ve službě Azure Stack, webová aplikace být připojená k hybridní síť mezi Azure a Azure Stack. Pokud chcete povolit připojení, budete muset:

- Konfigurace připojení point-to-site
- Konfigurace webové aplikace
- Úprava IP adresy brány místní sítě v Azure stacku.

### <a name="configure-the-azure-virtual-network-for-point-to-site-connectivity"></a>Nakonfigurujte virtuální síť Azure pro připojení point-to-site

Brána virtuální sítě Azure straně hybridní síť musí umožňovat připojení point-to-site k integraci s Azure App Service.

1. V Azure přejděte na stránku brány virtuální sítě. V části **nastavení**vyberte **KonfiguracePoint-to-site**.

    ![Možnost Point-to-site](media/azure-stack-solution-hybrid-cloud/image8.png)

2. Vyberte **nakonfigurovat** konfigurace point-to-site.

    ![Počáteční konfigurace point-to-site](media/azure-stack-solution-hybrid-cloud/image9.png)

3. Na **Point-to-site** konfigurace stránky, zadejte rozsah privátních IP adres, který chcete použít v **fondu adres**.

   > [!Note]  
   > Ujistěte se, že oblast, kterou zadáte, nepřekrývá s žádným z rozsahů adres, který je již využíván podsítěmi v komponentách Azure nebo ve službě Azure Stack, globální hybridní sítě.

   V části **Typ tunelového propojení**, zrušte zaškrtnutí políčka **IKEv2 VPN**. Vyberte **Uložit** k dokončení konfigurace point-to-site.

   ![Nastavení Point-to-site](media/azure-stack-solution-hybrid-cloud/image10.png)

### <a name="integrate-the-azure-app-service-application-with-the-hybrid-network"></a>Integrace aplikace služby Azure App Service s hybridní síť

1. Připojení aplikace k virtuální síti Azure, postupujte podle pokynů v [povolení integrace virtuální sítě](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet#enabling-vnet-integration).

2. Přejděte do **nastavení** pro plán služby App Service, který je hostitelem webové aplikace. V **nastavení**vyberte **sítě**.

    ![Konfigurace sítí](media/azure-stack-solution-hybrid-cloud/image11.png)

3. V **integrace virtuální sítě**vyberte **kliknutím sem můžete spravovat**.

    ![Správa integrace virtuální sítě](media/azure-stack-solution-hybrid-cloud/image12.png)

4. Vyberte virtuální síť, kterou chcete konfigurovat. V části **IP adres SMĚRUJÍ do virtuální sítě**, zadejte rozsah IP adres pro virtuální síť Azure, virtuální síť Azure Stack a point-to-site adresní prostory. Vyberte **Uložit** ověřte a uložte nastavení.

    ![Rozsahy IP adres pro směrování](media/azure-stack-solution-hybrid-cloud/image13.png)

Další informace o tom, jak služby App Service integruje s virtuálními sítěmi Azure, najdete v článku [integrujte svou aplikaci s Azure Virtual Network](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet).

### <a name="configure-the-azure-stack-virtual-network"></a>Konfigurace virtuální sítě Azure Stack

Brána místní sítě ve virtuální síti Azure Stack je potřeba nakonfigurovat pro směrování provozu z rozsahu adres point-to-site služby App Service.

1. Ve službě Azure Stack, přejděte na **bránu místní sítě**. V části **Nastavení** vyberte **Konfigurace**.

    ![Možnost konfigurace brány](media/azure-stack-solution-hybrid-cloud/image14.png)

2. V **adresní prostor**, zadejte rozsah adres point-to-site pro brány virtuální sítě ve vybraných Azure.l **Uložit** ověřte a uložte tuto konfiguraci.

    ![Point-to-site adresní prostor](media/azure-stack-solution-hybrid-cloud/image15.png)

## <a name="configure-dns-for-cross-cloud-scaling"></a>Konfigurace DNS pro škálování cloudu

Tím, že správně nakonfigurujete DNS pro různé cloudové aplikace, uživatelé můžou používat globální instance Azure a Azure Stack vaší webové aplikace. Konfiguraci DNS pro tento kurz vám také umožní směrovat provoz Azure Traffic Manageru při zatížení zvýší nebo sníží.

Tento kurz používá Azure DNS ke správě DNS. (Domény služby app Service nebude fungovat.)

### <a name="create-subdomains"></a>Create subdomains

Vzhledem k tomu, že Traffic Manager závisí na záznamy DNS CNAME, je potřeba subdomény správné směrování provozu do koncových bodů. Další informace o mapování domén a záznamů DNS najdete v tématu [mapování domén s Traffic Managerem](https://docs.microsoft.com/azure/app-service/web-sites-traffic-manager-custom-domain-name)

Pro Azure můžete použít koncový bod vytvoříte subdomény, které uživatelé pro přístup k vaší webové aplikace. Pro účely tohoto kurzu můžete použít **app.northwind.com**, ale můžete přizpůsobit tuto hodnotu podle vaší vlastní domény.

Také budete muset vytvořit subdoménu pomocí záznamu pro koncový bod služby Azure Stack. Můžete použít **azurestack.northwind.com**.

### <a name="configure-a-custom-domain-in-azure"></a>Konfigurace vlastní domény v Azure

1. Přidat **app.northwind.com** názvu hostitele na Azure web app pomocí [mapování CNAME do služby Azure App Service](https://docs.microsoft.com/Azure/app-service/app-service-web-tutorial-custom-domain#map-a-cname-record).

### <a name="configure-custom-domains-in-azure-stack"></a>Konfigurace vlastních domén v Azure stacku

1. Přidat **azurestack.northwind.com** názvu hostitele na webovou aplikaci Azure Stack podle [mapování záznamu A na službě Azure App Service](https://docs.microsoft.com/Azure/app-service/app-service-web-tutorial-custom-domain#map-an-a-record). Použijte směrovatelných v Internetu IP adresu pro aplikaci služby App Service.

2. Přidat **app.northwind.com** názvu hostitele na webovou aplikaci Azure Stack podle [mapování CNAME do služby Azure App Service](https://docs.microsoft.com/Azure/app-service/app-service-web-tutorial-custom-domain#map-a-cname-record). Použijte název hostitele, který jste nakonfigurovali v předchozím kroku (1) jako cíl pro záznam CNAME.

## <a name="configure-ssl-certificates-for-cross-cloud-scaling"></a>Konfigurace certifikátů SSL pro škálování cloudu

Je potřeba zajistit, že je citlivé údaje shromážděné agentem webové aplikace zabezpečené přenášená k a neaktivní uložená v SQL database.

Nakonfigurujete webových aplikací Azure a Azure Stackem používání certifikátů SSL pro veškerý příchozí provoz.

### <a name="add-ssl-to-azure-and-azure-stack"></a>Přidat SSL k Azure a Azure Stack

Chcete-li přidat SSL k Azure:

1. Ujistěte se, že platnost certifikátu SSL, které jste získali pro subdomény, kterou jste vytvořili. (Je možné použít certifikáty se zástupnými znaky.)

2. V Azure, postupujte podle pokynů **Příprava webové aplikace** a **vytvoření vazby certifikátu SSL** oddíly [vytvoření vazby existujícího vlastního certifikátu SSL k Azure Web Apps](https://docs.microsoft.com/Azure/app-service/app-service-web-tutorial-custom-ssl) články. Vyberte **SSL typu sni** jako **typ SSL**.

3. Přesměrujte veškerý provoz na portu HTTPS. Postupujte podle pokynů **vynucení HTTPS** část [vytvoření vazby existujícího vlastního certifikátu SSL k Azure Web Apps](https://docs.microsoft.com/Azure/app-service/app-service-web-tutorial-custom-ssl) článku.

Chcete-li přidat SSL ke službě Azure Stack:

- Opakujte kroky 1 až 3, který jste použili pro Azure.

## <a name="configure-and-deploy-the-web-application"></a>Nakonfigurujte a nasaďte webovou aplikaci

Budete konfigurovat kód aplikace, který sestavu telemetrických dat do správné instanci Application Insights a konfigurace webové aplikace pomocí správný připojovací řetězce. Další informace o Application Insights najdete v tématu [co je Application Insights?](https://docs.microsoft.com/azure/application-insights/app-insights-overview)

### <a name="add-application-insights"></a>Přidat Application Insights

1. Otevřete webové aplikace v sadě Microsoft Visual Studio.

2. [Přidat službu Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-core#add-application-insights-telemetry) do projektu k odesílání telemetrie Application Insights používá k vytváření výstrah při webového provozu zvyšuje nebo snižuje.

### <a name="configure-dynamic-connection-strings"></a>Konfigurace dynamické připojovacích řetězců

Každá instance webové aplikace bude používat jinou metodu pro připojení k databázi SQL. Aplikace v Azure používá privátní IP adresu virtuálního počítače (VM) systému SQL Server a používá aplikace v Azure stacku veřejnou IP adresu virtuálního počítače SQL serveru.

> [!Note]  
> Na integrovaný systém Azure stacku veřejnou IP adresu by neměl být směrovatelných v Internetu. Na Azure Stack Development Kit (ASDK), není směrovatelný mimo ASDK veřejnou IP adresu.

Proměnné prostředí služby App Service můžete použít k předávání různých připojovací řetězec pro každou instanci aplikace.

1. Otevřete aplikaci v sadě Visual Studio.

2. Otevřete Startup.cs a vyhledejte následující blok kódu:

    ```C#
    services.AddDbContext<MyDatabaseContext>(options =>
        options.UseSqlite("Data Source=localdatabase.db"));
    ```

3. Předchozí blok kódu nahraďte následujícím kódem, který používá připojovací řetězec definované v souboru appsettings.json:

    ```C#
    services.AddDbContext<MyDatabaseContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("MyDbConnection")));
     // Automatically perform database migration
     services.BuildServiceProvider().GetService<MyDatabaseContext>().Database.Migrate();
    ```

### <a name="configure-app-service-application-settings"></a>Konfigurace nastavení aplikace služby App Service

1. Vytvářejte připojovací řetězce pro Azure a Azure Stack. Řetězce by měly být stejné, kromě IP adresy, které se používají.

2. V Azure a Azure Stack, přidejte příslušný připojovací řetězec [jako nastavení aplikace](https://docs.microsoft.com/azure/app-service/web-sites-configure) ve webové aplikaci pomocí `SQLCONNSTR\_` jako předpony v názvu.

3. **Uložit** nastavení webové aplikace a aplikaci restartujte.

## <a name="enable-automatic-scaling-in-global-azure"></a>Povolit automatické škálování v globální Azure

Při vytváření webové aplikace ve službě App Service environment začne s jednou instancí. Horizontální navýšení kapacity můžete automaticky přidat instance poskytnout že další výpočetní prostředky pro vaši aplikaci. Podobně můžete automaticky škálovat v a snížit počet instancí požadavkům vašich aplikací.

> [!Note]  
> Musíte mít plán služby App Service nakonfigurovat pro horizontální navýšení kapacity a horizontální snížení kapacity. Pokud nemáte plán, vytvořte si ho před zahájením dalších kroků.

### <a name="enable-automatic-scale-out"></a>Povolit automatické horizontální navýšení kapacity

1. V Azure, najít plán služby App Service pro weby, kterou chcete škálovat na více systémů a pak vyberte **horizontální navýšení kapacity (plán služby App Service)**.

    ![Horizontální navýšení kapacity](media/azure-stack-solution-hybrid-cloud/image16.png)

2. Vyberte **povolit automatické škálování**.

    ![Povolit automatické škálování](media/azure-stack-solution-hybrid-cloud/image17.png)

3. Zadejte název pro **název nastavení automatického škálování**. Pro **výchozí** pravidlo automatického škálování, vyberte **škálování podle metriky**. Nastavte **limity instancí** k **minimálně: 1**, **maximální: 10**, a **výchozí: 1**.

    ![Konfiguraci automatického škálování](media/azure-stack-solution-hybrid-cloud/image18.png)

4. Vyberte **+ přidat pravidlo**.

5. V **zdroj metriky**vyberte **aktuální prostředek**. Pomocí následujících kritérií a akce pro pravidlo.

**Kritéria**

1. V části **Časová agregace** vyberte **průměrné**.

2. V části **název metriky**vyberte **procento využití procesoru**.

3. V části **operátor**vyberte **větší než**.

   - Nastavte **prahová hodnota** k **50**.
   - Nastavte **doba trvání** k **10**.

**Akce**

1. V části **operace**vyberte **zvýšit počet o**.

2. Nastavte **počet instancí** k **2**.

3. Nastavte **funkčnosti** k **5**.

4. Vyberte **Přidat**.

5. Vyberte **+ přidat pravidlo**.

6. V **zdroj metriky**vyberte **aktuální prostředek.**

   > [!Note]  
   > Aktuální prostředek bude obsahovat název plánu služby App Service/GUID a **typ prostředku** a **prostředků** rozevírací seznamy zobrazí šedě.

### <a name="enable-automatic-scale-in"></a>Povolit automatické horizontální snížení kapacity

Při provozu sníží, můžete Azure webové aplikace automaticky snížit počet aktivních instancí snižovat náklady a. Tato akce je méně agresivní než horizontální navýšení kapacity za účelem minimalizace dopad na uživatele aplikace.

1. Přejděte **výchozí** horizontální navýšení kapacity podmínky, vyberte **+ přidat pravidlo**. Pomocí následujících kritérií a akce pro pravidlo.

**Kritéria**

1. V části **Časová agregace** vyberte **průměrné**.

2. V části **název metriky**vyberte **procento využití procesoru**.

3. V části **operátor**vyberte **menší než**.

   - Nastavte **prahová hodnota** k **30**.
   - Nastavte **doba trvání** k **10**.

**Akce**

1. V části **operace**vyberte **snížit počet o**.

   - Nastavte **počet instancí** k **1**.
   - Nastavte **funkčnosti** k **5**.

2. Vyberte **Přidat**.

## <a name="create-a-traffic-manager-profile-and-configure-cross-cloud-scaling"></a>Vytvoření profilu Traffic Manageru a konfigurace škálování cloudu

Vytvoříte profil Traffic Manageru v Azure a pak nakonfigurujte koncové body umožňují škálování cloudu.

### <a name="create-traffic-manager-profile"></a>Vytvořit profil služby Traffic Manager

1. Vyberte **vytvořit prostředek**
2. Vyberte **sítě**
3. Vyberte **profil služby Traffic Manager** a nakonfigurujte následující:

   - V **název**, zadejte název pro svůj profil. Tento název **musí** být jedinečný v zóny trafficmanager.net a slouží k vytvoření nového názvu DNS (například northwindstore.trafficmanager.net).
   - Pro **metodu směrování**, vyberte **vážená**.
   - Pro **předplatné**, vyberte předplatné, které chcete vytvořit tento profil v.
   - V **skupiny prostředků**, vytvořte novou skupinu prostředků pro tento profil.
   - V poli **Umístění skupiny prostředků** vyberte umístění skupiny prostředků. Toto nastavení označuje umístění skupiny prostředků a nemá žádný vliv na profil Traffic Manageru, který se nasadí globálně.

4. Vyberte **Vytvořit**.

    ![Vytvořit profil služby Traffic Manager](media/azure-stack-solution-hybrid-cloud/image19.png)

 Po dokončení globálního nasazení profilu Traffic Manageru se zobrazí v seznamu prostředků pro skupinu prostředků, kterou jste vytvořili, ho pod.

### <a name="add-traffic-manager-endpoints"></a>Přidání koncových bodů služby Traffic Manager

1. Vyhledejte profil Traffic Manageru, kterou jste vytvořili. (Pokud jste přešli sem do skupiny prostředků pro profil, vyberte profil.)

2. V **profil služby Traffic Manager**v části **nastavení**vyberte **koncové body**.

3. Vyberte **Přidat**.

4. V **přidat koncový bod**, použijte následující nastavení pro službu Azure Stack:

   - Pro **typ**vyberte **externí koncový bod**.
   - Zadejte **název** pro tento koncový bod.
   - Pro **plně kvalifikovaný název domény (FQDN) nebo IP adresa** zadejte externí adresu URL pro webovou aplikaci Azure Stack.
   - Pro **váha**, ponechte výchozí nastavení, **1**. Tento váha výsledkem veškerý provoz směřující do tohoto koncového bodu, pokud je v pořádku.
   - Ponechte **přidat jako zakázaný** nezaškrtnuté.

5. Vyberte **OK** uložit koncový bod služby Azure Stack.

Koncový bod Azure budete dále konfigurovat.

1. Na **profil služby Traffic Manager**vyberte **koncové body**.
2. Vyberte **+ přidat**.
3. Na **přidat koncový bod**, použijte následující nastavení pro Azure:

   - Pro **typ**vyberte **koncový bod Azure**.
   - Zadejte **název** pro tento koncový bod.
   - Pro **typ cílového prostředku**vyberte **služby App Service**.
   - Pro **cílový prostředek**vyberte **vybrat aplikační službu** zobrazíte seznam webových aplikací ve stejném předplatném.
   - V části **Prostředek** vyberte službu App Service, kterou chcete přidat jako první koncový bod.
   - Pro **váha**vyberte **2**. Výsledkem je veškerý provoz směřující do tohoto koncového bodu, pokud primární koncový bod není v pořádku, nebo máte pravidlo nebo výstrahy, která znovu směruje provoz při aktivaci.
   - Ponechte **přidat jako zakázaný** nezaškrtnuté.

4. Vyberte **OK** uložit koncový bod Azure.

Po nakonfigurování oba koncové body jsou uvedeny v **profil služby Traffic Manager** při výběru **koncové body**. Příklad na následujícím snímku obrazovky ukazuje dva koncové body s informacemi o stavu a konfiguraci pro každý z nich.

![Koncové body](media/azure-stack-solution-hybrid-cloud/image20.png)

## <a name="set-up-application-insights-monitoring-and-alerting"></a>Nastavení Application Insights, monitorování a upozorňování

Azure Application Insights umožňuje monitorovat aplikace a odesílat oznámení na základě podmínek, které nakonfigurujete. Tady je několik příkladů: aplikace není k dispozici, dochází k selhání nebo se zobrazuje problémy s výkonem.

Použijete k vytvoření výstrahy metrik Application Insights. Při aktivaci tato upozornění webových aplikací instance do Azure pro horizontální navýšení kapacity se automaticky přepnout ze služby Azure Stack a pak zpátky do Azure stack pro horizontální snížení kapacity.

### <a name="create-an-alert-from-metrics"></a>Vytvořte výstrahu z metrik

Přejděte do skupiny prostředků pro účely tohoto kurzu a pak vyberte instanci Application Insights a otevřete **Application Insights**.

![Application Insights](media/azure-stack-solution-hybrid-cloud/image21.png)

Toto zobrazení budete používat k vytvoření s horizontálním navýšením kapacity upozornění a škálovací ve výstraze.

### <a name="create-the-scale-out-alert"></a>Vytvořit horizontální navýšení kapacity upozornění

1. V části **KONFIGUROVAT**vyberte **upozornění (klasická)**.
2. Vyberte **přidat upozornění metriky (klasické)**.
3. V **přidat pravidlo**, proveďte následující konfiguraci:

   - Pro **název**, zadejte **shlukového přenosu do cloudu Azure**.
   - A **popis** je volitelný.
   - V části **zdroj**, **výstraha**vyberte **metriky**.
   - V části **kritéria**, vyberte své předplatné, skupinu prostředků pro váš profil Traffic Manageru a název profilu Traffic Manageru pro prostředek.

4. Pro **metrika**vyberte **požadavků**.
5. Pro **podmínku**vyberte **větší než**.
6. Pro **prahová hodnota**, zadejte **2**.
7. Pro **období**vyberte **za posledních 5 minut**.
8. V části **upozorňovat prostřednictvím**:
   - Zaškrtněte políčko pro **e-mailu vlastníci, přispěvatelé a čtenáři**.
   - Zadejte svou e-mailovou adresu pro **další email(s) správce**.

9. Na panelu nabídek vyberte **Uložit**.

### <a name="create-the-scale-in-alert"></a>Vytvoření škálovací ve výstraze

1. V části **KONFIGUROVAT**vyberte **upozornění (klasická)**.
2. Vyberte **přidat upozornění metriky (klasické)**.
3. V **přidat pravidlo**, proveďte následující konfiguraci:

   - Pro **název**, zadejte **škálování zpět do služby Azure Stack**.
   - A **popis** je volitelný.
   - V části **zdroj**, **výstraha**vyberte **metriky**.
   - V části **kritéria**, vyberte své předplatné, skupinu prostředků pro váš profil Traffic Manageru a název profilu Traffic Manageru pro prostředek.

4. Pro **metrika**vyberte **požadavků**.
5. Pro **podmínku**vyberte **menší než**.
6. Pro **prahová hodnota**, zadejte **2**.
7. Pro **období**vyberte **za posledních 5 minut**.
8. V části **upozorňovat prostřednictvím**:
   - Zaškrtněte políčko pro **e-mailu vlastníci, přispěvatelé a čtenáři**.
   - Zadejte svou e-mailovou adresu pro **další email(s) správce**.

9. Na panelu nabídek vyberte **Uložit**.

Následující snímek obrazovky ukazuje výstrahy pro horizontální navýšení kapacity a horizontální snížení kapacity.

   ![Upozornění (Classic)](media/azure-stack-solution-hybrid-cloud/image22.png)

## <a name="redirect-traffic-between-azure-and-azure-stack"></a>Přesměrovat provoz mezi Azure a Azure Stack

Můžete nakonfigurovat ruční nebo automatické přepnutí z provozu vaší webové aplikace, přepínání mezi Azure a Azure Stack.

### <a name="configure-manual-switching-between-azure-and-azure-stack"></a>Nakonfigurovat ruční přepínání mezi Azure a Azure Stack

Pokud vaše webová stránka dosáhne prahové hodnoty, které nakonfigurujete, objeví se vám upozornění. Použijte následující kroky pro ruční přesměrování přenosu dat do Azure.

1. Na webu Azure Portal vyberte svůj profil Traffic Manageru.

    ![Koncové body Traffic Manageru](media/azure-stack-solution-hybrid-cloud/image20.png)

2. Vyberte **koncové body**.
3. Vyberte **koncový bod Azure**.
4. V části **stav** vyberte **povoleno**a pak vyberte **Uložit**.

    ![Povolit koncový bod Azure](media/azure-stack-solution-hybrid-cloud/image23.png)

5. Na **koncové body** profilu služby Traffic Manager, vyberte **externí koncový bod**.
6. V části **stav** vyberte **zakázané**a pak vyberte **Uložit**.

    ![Zakázání koncový bod služby Azure Stack](media/azure-stack-solution-hybrid-cloud/image24.png)

Po nakonfigurování koncových bodů aplikace provoz směrován na škálování webové aplikace Azure namísto webové aplikace služby Azure Stack.

 ![Změnit koncových bodů](media/azure-stack-solution-hybrid-cloud/image25.png)

Tok vrátit zpět do služby Azure Stack, použijte předchozí postup:

- Povolit koncový bod služby Azure Stack
- Zakázání koncový bod Azure

### <a name="configure-automatic-switching-between-azure-and-azure-stack"></a>Konfigurace automatického přepínání mezi Azure a Azure Stack

Můžete také použít, pokud je aplikace spuštěná monitorování Application Insights [bez serveru](https://azure.microsoft.com/overview/serverless-computing/) prostředí poskytuje služba Azure Functions.

V tomto scénáři můžete nakonfigurovat Application Insights a použití webhooku, která volá aplikaci Function app. Tato aplikace automaticky povolí nebo zakáže koncový bod v reakci na výstrahy.

Následující postup použijte jako vodítko ke konfiguraci přepínání automatického přenosu.

1. Vytvořte aplikaci Azure Function app.
2. Vytvoření funkce aktivované protokolem HTTP.
3. Importujte se sadami SDK služby Azure Resource Manageru, webových aplikací a Traffic Manageru.
4. Vývoj kódu:

   - Ověření se svým předplatným Azure.
   - Pomocí parametru, která přepíná koncové body Traffic Manageru směrovat přenos dat do Azure nebo ve službě Azure Stack.

5. Uložte svůj kód a přidat adresu URL aplikace Function app s příslušnými parametry, které **Webhooku** pravidlo upozornění nastavení Application Insights.
6. Provoz automaticky přesměruje, když se aktivuje upozornění Application Insights.

## <a name="next-steps"></a>Další postup

- Další informace o vzorech cloudu Azure, najdete v článku [vzory návrhu v cloudu](https://docs.microsoft.com/azure/architecture/patterns).
