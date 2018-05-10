---
title: Automatizovat zřizování aplikací v Azure Active Directory pomocí SCIM | Microsoft Docs
description: Azure Active Directory, mohou automaticky poskytovat uživatelé a skupiny do aplikace nebo identity úložiště, který je přední stěnou webovou službou pomocí rozhraní definované v specifikace protokolu SCIM
services: active-directory
documentationcenter: ''
author: asmalser-msft
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/12/2017
ms.author: asmalser
ms.reviewer: asmalser
ms.custom: aaddev;it-pro;seohack1
ms.openlocfilehash: 19a1ae7ae7acc6fe09a529dd174363735343027e
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
---
# <a name="using-system-for-cross-domain-identity-management-to-automatically-provision-users-and-groups-from-azure-active-directory-to-applications"></a>Pomocí systému pro správu identit napříč doménami pro automatické zřizování uživatelů a skupin ze služby Azure Active Directory k aplikacím

## <a name="overview"></a>Přehled
Azure Active Directory (Azure AD), mohou automaticky poskytovat uživatelé a skupiny do aplikace nebo identity úložiště, který je přední stěnou pomocí webové služby pomocí rozhraní definované v [systému pro protokol napříč doménami Identity Management (SCIM) 2.0 specifikace](https://tools.ietf.org/html/draft-ietf-scim-api-19). Azure Active Directory může odesílat požadavky na vytvořit, upravit nebo odstranit přiřazené uživatelů a skupin k webové službě. Webová služba může překládat pak tyto požadavky do operací na úložiště identit cíl. 

![][0]
*Obrázek 1: Zřizování z Azure Active Directory k úložišti identity prostřednictvím webové služby*

Tato funkce slouží ve spojení s možností "přineste vlastní aplikace" v Azure AD umožňující jednotného přihlašování a automatické zřizování pro aplikace, které poskytují nebo jsou přední stěnou webovou službou SCIM uživatelů.

Existují dva případy použití pro pomocí SCIM v Azure Active Directory:

* **Zřizování uživatelů a skupin na aplikace, které podporují SCIM** aplikace, které podporují SCIM 2.0 a používala tokeny nosičů OAuth pro ověřování pracuje s Azure AD bez konfigurace.
* **Vytváření vlastních řešení zřizování pro aplikace, které podporují jiné založené na rozhraní API zřizování** pro jiný SCIM aplikace, můžete vytvořit koncový bod SCIM pro převod mezi koncový bod Azure AD SCIM a jakéhokoli rozhraní API aplikace podporuje pro zřizování uživatelů. Můžete vyvíjet koncový bod SCIM, neexistují knihovny společné jazykové infrastruktury (CLI) společně s ukázky kódu, které ukazují, jak k poskytování koncový bod SCIM a převede SCIM zprávy.  

## <a name="provisioning-users-and-groups-to-applications-that-support-scim"></a>Zřizování uživatelů a skupin na aplikace, které podporují SCIM
Azure AD lze nakonfigurovat, aby automaticky přiřazen zřizování uživatelů a skupin k aplikacím, které implementují [systému pro správu identit napříč doménami 2 (SCIM)](https://tools.ietf.org/html/draft-ietf-scim-api-19) webové služby a přijímat tokeny nosičů OAuth pro ověřování. V rámci specifikace SCIM 2.0 aplikace musí splňovat tyto požadavky:

* Podporuje vytváření uživatelů nebo skupin, podle části 3.3 SCIM protokolu.  
* Úprava uživatele nebo skupiny s požadavky patch podle části 3.5.2 protokol SCIM podporuje.  
* Podporuje načítání známých prostředků podle části 3.4.1 SCIM protokolu.  
* Podporuje dotazování uživatelů nebo skupin, podle části 3.4.2 SCIM protokolu.  Ve výchozím nastavení externalId se zadají dotaz uživatelé a skupiny se zadají dotaz displayName.  
* Dotazování uživatele podle ID a správcem podle části 3.4.2 protokol SCIM podporuje.  
* Podporuje dotazování skupin podle ID a členové podle části 3.4.2 SCIM protokolu.  
* Přijímá tokeny nosičů OAuth pro autorizaci podle části 2.1 SCIM protokolu.

Zeptejte se svého poskytovatele aplikace nebo v dokumentaci poskytovatele aplikace pro příkazy kompatibility s těmito požadavky.

### <a name="getting-started"></a>Začínáme
Aplikace, které podporují profilem SCIM popsané v tomto článku může být připojen k Azure Active Directory používá funkci "bez Galerie aplikace" v galerii aplikací Azure AD. Po připojení Azure AD spustí proces synchronizace každých 20 minut, kde se dotazuje aplikace SCIM koncový bod pro přiřazené uživatele a skupiny a vytvoří nebo upraví je na základě podrobností přiřazení.

**Připojení aplikace, která podporuje SCIM:**

1. Přihlaste se k [portálu Azure](https://portal.azure.com). 
2. Přejděte do **Azure Active Directory > podnikové aplikace, které**a vyberte **novou aplikaci > všechny > aplikace bez Galerie**.
3. Zadejte název pro vaši aplikaci a klikněte na tlačítko **přidat** ikonu pro vytvoření objektu aplikace.
    
  ![][1]
  *Obrázek 2: Galerii aplikací Azure AD*
    
4. Na obrazovce výsledné vyberte **zřizování** kartě v levém sloupci.
5. V **režimu zřizování** nabídce vyberte možnost **automatické**.
    
  ![][2]
  *Obrázek 3: Konfigurace zřizování na portálu Azure*
    
6. V **URL klienta** pole, zadejte adresu URL koncového bodu SCIM aplikace. Příklad: https://api.contoso.com/scim/v2/
7. Pokud koncový bod SCIM vyžaduje tokenu nosiče OAuth z vystavitele než Azure AD, zkopírujte do nepovinný požadovaný token nosiče OAuth **tajný klíč tokenu** pole. Pokud toto pole je prázdné, Azure AD zahrnuty tokenu nosiče OAuth, který je vydán z Azure AD s každou žádostí. Aplikace, které používají Azure AD jako zprostředkovatele identity můžete ověřit tento Azure AD-vydán token.
8. Klikněte **Test připojení** tlačítko tak, aby měl Azure Active Directory, pokusí se připojit ke koncovému bodu SCIM. Pokud pokusy o selhání, zobrazí se informace o chybě.  
9. Pokud pokusy o připojení k úspěšné aplikaci, pak klikněte na tlačítko **Uložit** uložit přihlašovací údaje správce.
10. V **mapování** část, existují dvě sady vybrat mapování atributů: jeden pro uživatelské objekty a jeden pro objekty skupiny. Vyberte každé z nich a zkontrolujte atributy, které jsou synchronizované z Azure Active Directory do vaší aplikace. Atributy vybrán jako **párování** vlastnosti jsou slouží k přiřazení uživatelů a skupin v aplikaci pro operace aktualizace. Kliknutím na tlačítko Uložit potvrzení změny.

    >[!NOTE]
    >Volitelně můžete zakázat synchronizaci objektů skupiny zakázáním "skupiny" mapování. 

11. V části **nastavení**, **oboru** pole definuje, které uživatele nebo skupiny jsou synchronizovány. Výběr "Synchronizace přiřadit pouze uživatelé a skupiny" (doporučeno) se synchronizují pouze uživatelé a skupiny přiřazené v **uživatelů a skupin** kartě.
12. Po dokončení konfiguraci změnit **Stav zřizování** k **na**.
13. Klikněte na tlačítko **Uložit** zahájíte zřizování služby Azure AD. 
14. Pokud synchronizaci přiřadit pouze uživatelé a skupiny (doporučeno), je nutné vybrat **uživatelů a skupin** a přiřaďte uživatele a skupiny, které chcete synchronizovat.

Po dokončení počáteční synchronizace se spustil, můžete **protokoly auditu** a sledovat postup, který zobrazuje všechny akce prováděné při zřizování služby ve vaší aplikaci. Další informace o tom, jak číst zřizování protokoly služby Azure AD najdete v tématu [zprávy o zřizování účtu automatické uživatele](https://docs.microsoft.com/azure/active-directory/active-directory-saas-provisioning-reporting).

>[!NOTE]
>Počáteční synchronizace trvá déle než následné synchronizace, ke kterým dochází přibližně každých 20 minut, dokud se službou provést. 


## <a name="building-your-own-provisioning-solution-for-any-application"></a>Vytváření vlastní zřizování řešení pro všechny aplikace
Vytvořením SCIM webová služba, která rozhraní s Azure Active Directory, můžete povolit jeden uživatel přihlašování a automatické zřizování prakticky jakékoli aplikace, která poskytuje zřizování rozhraní API REST nebo SOAP uživatelů.

Zde je, jak to funguje:

1. Azure AD poskytuje společné jazykové infrastruktury knihovny s názvem [Microsoft.SystemForCrossDomainIdentityManagement](https://www.nuget.org/packages/Microsoft.SystemForCrossDomainIdentityManagement/). Systémoví integrátoři a vývojáři mohou použít tuto knihovnu vytvořit a nasadit koncový bod na základě SCIM webové služby lze připojit k libovolné aplikace úložiště identit Azure AD.
2. Mapování se implementují ve webové službě k mapování schéma standardizované uživatele na schéma uživatele a protokol požadované aplikací.
3. Adresa URL koncového bodu je zaregistrován ve službě Azure AD jako součást vlastní aplikaci v galerii aplikací.
4. Uživatelé a skupiny přiřazené k této aplikaci ve službě Azure AD. Po přiřazení jsou vloženy do fronty na synchronizaci do cílové aplikace. Proces synchronizace zpracování fronty spouští každých 20 minut.

### <a name="code-samples"></a>Ukázky kódů
Chcete-li tento jednodušší, proces sadu [ukázky kódu jsou](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master) jsou k dispozici, vytvořit koncový bod webové služby SCIM a ukázka automatické zřizování. Jeden ukázka je zprostředkovatele, který udržuje soubor s řádky představující uživatele a skupiny hodnot oddělených čárkami.  Druhá je zprostředkovatele, který funguje ve službě Amazon Web Services identita a správa přístupu.  

**Požadavky**

* Visual Studio 2013 nebo novější
* [Azure SDK pro .NET](https://azure.microsoft.com/downloads/)
* Windows počítač, který podporuje technologii ASP.NET 4.5 má být použit jako SCIM koncový bod. Tento počítač musí být přístupné z cloudu
* [Předplatné Azure zkušební nebo licencovanou verzi Azure AD Premium](https://azure.microsoft.com/services/active-directory/)
* Ukázka Amazon AWS vyžaduje knihovny z [AWS nástrojů pro Visual Studio](http://docs.aws.amazon.com/AWSToolkitVS/latest/UserGuide/tkv_setup.html). Další informace naleznete v souboru README součástí vzorku.

### <a name="getting-started"></a>Začínáme
Nejjednodušší způsob, jak implementovat SCIM koncový bod, který může přijmout zřizování požadavky z Azure AD je sestavení a nasazení ukázka kódu, který produkuje zřízené uživatele do souboru s oddělovači (CSV).

**Pokud chcete vytvořit koncový bod ukázka SCIM:**

1. Stáhněte si balíček ukázkový kód v [https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master)
2. Rozbalte balíček a umístěte ji na počítač se systémem Windows v umístění, jako je například C:\AzureAD-BYOA-Provisioning-Samples\.
3. V této složce spusťte FileProvisioningAgent řešení v sadě Visual Studio.
4. Vyberte **nástroje > Správce balíčků knihoven > Konzola správce balíčků**a spusťte následující příkazy pro projekt FileProvisioningAgent se vyřešit odkazy na řešení:
  ```` 
   Install-Package Microsoft.SystemForCrossDomainIdentityManagement
   Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
   Install-Package Microsoft.Owin.Diagnostics
   Install-Package Microsoft.Owin.Host.SystemWeb
  ````
5. Sestavení projektu FileProvisioningAgent.
6. Spuštění aplikace příkazového řádku v systému Windows (jako správce) a použít **cd** příkazu změňte adresář na vaše **\AzureAD-BYOA-Provisioning-Samples\ProvisioningAgent\bin\Debug** složka.
7. Spusťte následující příkaz, nahraďte IP adresy nebo domény název počítače Windows < adresa >:
  ````   
   FileAgnt.exe http://<ip-address>:9000 TargetFile.csv
  ````
8. V systému Windows v rámci **nastavení systému Windows > síť a Internet nastavení**, vyberte **brány Windows Firewall > Upřesnit nastavení**a vytvořit **příchozí pravidlo** , umožňuje příchozí přístup k portu 9000.
9. Pokud je počítač Windows za směrovačem, je potřeba nakonfigurovat k provedení přístup překlad mezi její port 9000, který má přístup k Internetu a port 9000 na počítači Windows směrovači. Tato konfigurace je nutná pro přístup k tomuto koncovému bodu v cloudu Azure AD.

**Postup registrace koncového bodu SCIM ukázka ve službě Azure AD:**

1. Přihlaste se k [portálu Azure](https://portal.azure.com). 
2. Přejděte do **Azure Active Directory > podnikové aplikace, které**a vyberte **novou aplikaci > všechny > aplikace bez Galerie**.
3. Zadejte název pro vaši aplikaci a klikněte na tlačítko **přidat** ikonu pro vytvoření objektu aplikace. Objekt aplikace vytvořený je určený k reprezentaci cílové aplikaci jste by zajišťování, které a implementace jednotné přihlašování pro a nikoli pouze SCIM koncového bodu.
4. Na obrazovce výsledné vyberte **zřizování** kartě v levém sloupci.
5. V **režimu zřizování** nabídce vyberte možnost **automatické**.
    
  ![][2]
  *Obrázek 4: Konfigurace zřizování na portálu Azure*
    
6. V **URL klienta** pole, zadejte adresu URL a port váš koncový bod SCIM vystavené Internetu. Položka je podobný http://testmachine.contoso.com:9000 nebo http://<ip-address>:9000/, kde < adresa > je Internetu zveřejněné IP adresu.  
7. Pokud koncový bod SCIM vyžaduje tokenu nosiče OAuth z vystavitele než Azure AD, zkopírujte do nepovinný požadovaný token nosiče OAuth **tajný klíč tokenu** pole. Pokud toto pole je prázdné, bude obsahovat Azure AD tokenu nosiče OAuth, který je vydán z Azure AD s každou žádostí. Aplikace, které používají Azure AD jako zprostředkovatele identity můžete ověřit tento Azure AD-vydán token.
8. Klikněte **Test připojení** tlačítko tak, aby měl Azure Active Directory, pokusí se připojit ke koncovému bodu SCIM. Pokud pokusy o selhání, zobrazí se informace o chybě.  
9. Pokud pokusy o připojení k úspěšné aplikaci, pak klikněte na tlačítko **Uložit** uložit přihlašovací údaje správce.
10. V **mapování** část, existují dvě sady vybrat mapování atributů: jeden pro uživatelské objekty a jeden pro objekty skupiny. Vyberte každé z nich a zkontrolujte atributy, které jsou synchronizované z Azure Active Directory do vaší aplikace. Atributy vybrán jako **párování** vlastnosti jsou slouží k přiřazení uživatelů a skupin v aplikaci pro operace aktualizace. Kliknutím na tlačítko Uložit potvrzení změny.
11. V části **nastavení**, **oboru** pole definuje, které uživatele nebo skupiny jsou synchronizovány. Výběr "Synchronizace přiřadit pouze uživatelé a skupiny" (doporučeno) se synchronizují pouze uživatelé a skupiny přiřazené v **uživatelů a skupin** kartě.
12. Po dokončení konfiguraci změnit **Stav zřizování** k **na**.
13. Klikněte na tlačítko **Uložit** zahájíte zřizování služby Azure AD. 
14. Pokud synchronizaci přiřadit pouze uživatelé a skupiny (doporučeno), je nutné vybrat **uživatelů a skupin** a přiřaďte uživatele a skupiny, které chcete synchronizovat.

Po dokončení počáteční synchronizace se spustil, můžete **protokoly auditu** a sledovat postup, který zobrazuje všechny akce prováděné při zřizování služby ve vaší aplikaci. Další informace o tom, jak číst zřizování protokoly služby Azure AD najdete v tématu [zprávy o zřizování účtu automatické uživatele](https://docs.microsoft.com/azure/active-directory/active-directory-saas-provisioning-reporting).

V posledním kroku ověření ukázce je k otevření souboru TargetFile.csv ve složce \AzureAD-BYOA-Provisioning-Samples\ProvisioningAgent\bin\Debug na počítač se systémem Windows. Po spuštění procesu zřizování, tento soubor zobrazuje podrobnosti o veškerém přiřazené a zřízení uživatelů a skupin.

### <a name="development-libraries"></a>Vývojářské knihovny
K vývoji vlastní webová služba, která odpovídá specifikaci SCIM, nejdřív seznámíte se s následující knihovny poskytované Microsoft pomoci urychlit proces vývoje: 

1. Společné jazykové infrastruktury (CLI) knihovny jsou nabízena pro použití s jazyky na základě této infrastruktury, jako je například C#. Jedna z těchto knihoven [Microsoft.SystemForCrossDomainIdentityManagement.Service](https://www.nuget.org/packages/Microsoft.SystemForCrossDomainIdentityManagement/), deklaruje rozhraní, Microsoft.SystemForCrossDomainIdentityManagement.IProvider, viz následující obrázek: A vývojáře pomocí knihovny by implementovat dané rozhraní s třídu, která může označovat, obecně jako zprostředkovatel. Knihovny povolte vývojáři nasazení webové služby, který splňuje specifikaci SCIM. Webová služba může být buď hostovaný v rámci služby IIS nebo libovolného spustitelného souboru Common Language Infrastructure sestavení. Žádost je přeložit na volání metody poskytovatele, které by být naprogramovaný tak vývojáře k provozu na některé úložiště identit.
  
  ![][3]
  
2. [Obslužné rutiny trasy Express](http://expressjs.com/guide/routing.html) jsou k dispozici pro analýzu node.js požadavek objekty, které představují volání (podle specifikace SCIM), vytvářeny k webové službě node.js.   

### <a name="building-a-custom-scim-endpoint"></a>Vytváření koncový bod vlastní SCIM
Používání knihoven rozhraní příkazového řádku, vývojáře, kteří používají tyto knihovny hostování své služby v rámci všech spustitelný soubor sestavení Common Language Infrastructure, nebo Internetová informační služba. Tady je ukázkový kód pro hostování služby v rámci spustitelný soubor sestavení, na adrese http://localhost:9000: 

    private static void Main(string[] arguments)
    {
    // Microsoft.SystemForCrossDomainIdentityManagement.IMonitor, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IProvider and 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service are all defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service.dll.  

    Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor = 
      new DevelopersMonitor();
    Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider = 
      new DevelopersProvider(arguments[1]);
    Microsoft.SystemForCrossDomainIdentityManagement.Service webService = null;
    try
    {
        webService = new WebService(monitor, provider);
        webService.Start("http://localhost:9000");

        Console.ReadKey(true);
    }
    finally
    {
        if (webService != null)
        {
            webService.Dispose();
            webService = null;
        }
    }
    }

    public class WebService : Microsoft.SystemForCrossDomainIdentityManagement.Service
    {
    private Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor;
    private Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider;

    public WebService(
      Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitoringBehavior, 
      Microsoft.SystemForCrossDomainIdentityManagement.IProvider providerBehavior)
    {
        this.monitor = monitoringBehavior;
        this.provider = providerBehavior;
    }

    public override IMonitor MonitoringBehavior
    {
        get
        {
            return this.monitor;
        }

        set
        {
            this.monitor = value;
        }
    }

    public override IProvider ProviderBehavior
    {
        get
        {
            return this.provider;
        }

        set
        {
            this.provider = value;
        }
    }
    }

Tato služba musí mít HTTP adresa a server ověřování certifikát z nich kořenové certifikační autority je jedním z následujících názvů: 

* CNNIC
* Comodo
* CyberTrust
* DigiCert
* GeoTrust
* GlobalSign
* Go Daddy
* VeriSign
* WoSign

Ověřovací certifikát serverů může být vázaný na port na hostitele Windows pomocí nástroje prostředí sítě: 

    netsh http add sslcert ipport=0.0.0.0:443 certhash=0000000000003ed9cd0c315bbb6dc1c08da5e6 appid={00112233-4455-6677-8899-AABBCCDDEEFF}  

Hodnota poskytnutá pro certhash argument tady, je kryptografický otisk certifikátu, zatímco hodnota poskytnutá pro appid argument je libovolný identifikátor, globálně jedinečný.  

K hostování služby v rámci Internetové informační služby, by vývojář sestavení sestavení knihovny kódu CLA s třídy s názvem spuštění v výchozí obor názvů sestavení.  Zde je ukázka této třídy: 

    public class Startup
    {
    // Microsoft.SystemForCrossDomainIdentityManagement.IWebApplicationStarter, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IMonitor and  
    // Microsoft.SystemForCrossDomainIdentityManagement.Service are all defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service.dll.  

    Microsoft.SystemForCrossDomainIdentityManagement.IWebApplicationStarter starter;

    public Startup()
    {
        Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor = 
          new DevelopersMonitor();
        Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider = 
          new DevelopersProvider();
        this.starter = 
          new Microsoft.SystemForCrossDomainIdentityManagement.WebApplicationStarter(
            provider, 
            monitor);
    }

    public void Configuration(
      Owin.IAppBuilder builder) // Defined in in Owin.dll.  
    {
        this.starter.ConfigureApplication(builder);
    }
    }

### <a name="handling-endpoint-authentication"></a>Zpracování koncový bod ověřování
Žádosti z Azure Active Directory zahrnovat tokenu nosiče OAuth 2.0.   Všechny služby přijetí žádosti by měl ověřit vystavitele jako jménem očekávané klienta Azure Active Directory pro přístup k webové službě Azure Active Directory Graph Azure Active Directory.  V tokenu, Vystavitel je identifikována deklaraci identity iss, například "iss": "https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/".  V tomto příkladu bázové adresy hodnota deklarace identity, https://sts.windows.net, identifikuje Azure Active Directory jako vydavatel, zatímco relativního adres segmentu, cbb1a5ac-f33b-45fa-9bf5-f37db0fed422, je jedinečný identifikátor klienta Azure Active Directory na jménem, které byl token vydán.  Pokud byl token vydán pro přístup k webové službě Azure Active Directory Graph, by měl být identifikátor této služby, 00000002-0000-0000-c000-000000000000, v hodnotě deklarace identity oblast je token.  

Vývojáři pomocí knihovny CLA od společnosti Microsoft pro vytváření SCIM služby můžete ověřování žádostí z Azure Active Directory pomocí balíček Microsoft.Owin.Security.ActiveDirectory pomocí následujících kroků: 

1. U zprostředkovatele implementujte vlastnost Microsoft.SystemForCrossDomainIdentityManagement.IProvider.StartupBehavior tak, že se vrátí metoda, která se má volat při každém spuštění služby: 

  ````
    public override Action\<Owin.IAppBuilder, System.Web.Http.HttpConfiguration.HttpConfiguration\> StartupBehavior
    {
      get
      {
        return this.OnServiceStartup;
      }
    }

    private void OnServiceStartup(
      Owin.IAppBuilder applicationBuilder,  // Defined in Owin.dll.  
      System.Web.Http.HttpConfiguration configuration)  // Defined in System.Web.Http.dll.  
    {
    }
  ````

2. Přidejte následující kód do dané metody mít každá žádost o žádné koncové body služby ověřený jako opatřené tokenem vydaným službou Azure Active Directory jménem zadaného klienta, pro přístup k webové službě Azure AD Graph: 

  ````
    private void OnServiceStartup(
      Owin.IAppBuilder applicationBuilder IAppBuilder applicationBuilder, 
      System.Web.Http.HttpConfiguration HttpConfiguration configuration)
    {
      // IFilter is defined in System.Web.Http.dll.  
      System.Web.Http.Filters.IFilter authorizationFilter = 
        new System.Web.Http.AuthorizeAttribute(); // Defined in System.Web.Http.dll.configuration.Filters.Add(authorizationFilter);

      // SystemIdentityModel.Tokens.TokenValidationParameters is defined in    
      // System.IdentityModel.Token.Jwt.dll.
      SystemIdentityModel.Tokens.TokenValidationParameters tokenValidationParameters =     
        new TokenValidationParameters()
        {
          ValidAudience = "00000002-0000-0000-c000-000000000000"
        };

      // WindowsAzureActiveDirectoryBearerAuthenticationOptions is defined in 
      // Microsoft.Owin.Security.ActiveDirectory.dll
      Microsoft.Owin.Security.ActiveDirectory.
      WindowsAzureActiveDirectoryBearerAuthenticationOptions authenticationOptions =
        new WindowsAzureActiveDirectoryBearerAuthenticationOptions()    {
        TokenValidationParameters = tokenValidationParameters,
        Tenant = "03F9FCBC-EA7B-46C2-8466-F81917F3C15E" // Substitute the appropriate tenant’s 
                                                      // identifier for this one.  
      };

      applicationBuilder.UseWindowsAzureActiveDirectoryBearerAuthentication(authenticationOptions);
    }
  ````


## <a name="user-and-group-schema"></a>Schéma uživatelů a skupin
Azure Active Directory můžete zřídit dva typy prostředků, aby SCIM webové služby.  Tyto typy prostředků jsou uživatelé a skupiny.  

Uživatel prostředky jsou označeny identifikátor schématu "urn: ietf:params:scim:schemas:extension:enterprise:2.0:User", který je součástí této specifikace protokolu: http://tools.ietf.org/html/draft-ietf-scim-core-schema.  Výchozí mapování atributů uživatelům atributy "urn: ietf:params:scim:schemas:extension:enterprise:2.0:User" prostředků v Azure Active Directory je uvedené v tabulce 1, níže.  

Skupiny prostředků jsou identifikovány identifikátor schématu http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/Group.  Tabulka 2 níže znázorňuje výchozí mapování atributů skupin v Azure Active Directory na atributy http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/Group prostředky.  

### <a name="table-1-default-user-attribute-mapping"></a>Tabulka 1: Výchozí mapování atributů uživatele
| Uživatele Azure Active Directory | "název urn: ietf:params:scim:schemas:extension:enterprise:2.0:User" |
| --- | --- |
| IsSoftDeleted |aktivní |
| displayName |displayName |
| TelephoneNumber faxu |.value phoneNumbers [typ eq "fax"] |
| givenName |name.givenName |
| pracovní funkce |název |
| E-mailu |.value e-mailů [typ eq "práce"] |
| mailNickname |externalId |
| Správce |Správce |
| mobilní |.value phoneNumbers [eq typ "mobilní"] |
| objectId |id |
| PSČ |adresy [typ eq "práce"] .postalCode |
| proxy adresy |[Zadejte eq "ostatní"] e-mailů. Hodnota |
| fyzický. doručení OfficeName |adresy [Zadejte eq "ostatní"]. Formátu |
| StreetAddress |adresy [typ eq "práce"] .streetAddress |
| Příjmení |name.familyName |
| telefonní číslo |.value phoneNumbers [typ eq "práce"] |
| user-PrincipalName |Uživatelské jméno |

### <a name="table-2-default-group-attribute-mapping"></a>Tabulka 2: Výchozí skupiny mapování atributů
| Skupina Azure Active Directory | http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/Group |
| --- | --- |
| displayName |externalId |
| E-mailu |.value e-mailů [typ eq "práce"] |
| mailNickname |displayName |
| členové |členové |
| objectId |id |
| proxyAddresses |[Zadejte eq "ostatní"] e-mailů. Hodnota |

## <a name="user-provisioning-and-de-provisioning"></a>Zřizování uživatelů a jeho rušení
Následující obrázek znázorňuje zprávy, že Azure Active Directory odešle SCIM službě pro správu životního cyklu uživatele v jiné úložiště identit. Diagram také ukazuje, jak SCIM implementovaná pomocí rozhraní příkazového řádku knihovny poskytovat službu Microsoft pro vytvoření, že tyto služby převede tyto požadavky na volání metody zprostředkovatele.  

![][4]
*Obrázek 5: Zřizování uživatelů a jeho rušení pořadí*

1. Azure Active Directory dotáže služby pro uživatele s hodnotou atributu externalId odpovídající hodnota atributu mailNickname uživatele ve službě Azure AD. Dotaz je vyjádřena jako žádost protokolu HTTP (Hypertext Transfer), jako je tento příklad, ve kterém jyoung je ukázka mailNickname uživatele v Azure Active Directory: 
  ````
    GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
    Authorization: Bearer ...
  ````
  Pokud služba je vytvořená pomocí knihovny Common Language Infrastructure od společnosti Microsoft pro implementaci služby SCIM, je požadavek přeložit na volání metody dotazu zprostředkovatele služby.  Tady je podpis této metody: 
  ````
    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Protocol.  

    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource[]> Query(
      Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters parameters, 
      string correlationIdentifier);
  ````
  Tady je definici rozhraní Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters: 
  ````
    public interface IQueryParameters: 
      Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
    {
        System.Collections.Generic.IReadOnlyCollection <Microsoft.SystemForCrossDomainIdentityManagement.IFilter> AlternateFilters 
        { get; }
    }

    public interface Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
    {
      system.Collections.Generic.IReadOnlyCollection<string> ExcludedAttributePaths 
      { get; }
      System.Collections.Generic.IReadOnlyCollection<string> RequestedAttributePaths 
      { get; }
      string SchemaIdentifier 
      { get; }
    }

    public interface Microsoft.SystemForCrossDomainIdentityManagement.IFilter
    {
        Microsoft.SystemForCrossDomainIdentityManagement.IFilter AdditionalFilter 
          { get; set; }
        string AttributePath 
          { get; } 
        Microsoft.SystemForCrossDomainIdentityManagement.ComparisonOperator FilterOperator 
          { get; }
        string ComparisonValue 
          { get; }
    }

    public enum Microsoft.SystemForCrossDomainIdentityManagement.ComparisonOperator
    {
        Equals
    }
  ````
  V následující ukázce dotazu pro uživatele s danou hodnotou atributu externalId jsou hodnoty argumentů předaný metodě dotazu: 
  * Parametry. AlternateFilters.Count: 1
  * Parametry. AlternateFilters.ElementAt(0). AttributePath: "externalId"
  * Parametry. AlternateFilters.ElementAt(0). Porovnávací operátor: ComparisonOperator.Equals
  * Parametry. AlternateFilter.ElementAt(0). ComparisonValue: "jyoung"
  * correlationIdentifier: System.Net.Http.HttpRequestMessage.GetOwinEnvironment["owin.RequestId"] 

2. Pokud odpověď na dotaz pro webovou službu pro uživatele s hodnotou atributu externalId odpovídající hodnota atributu mailNickname uživatele nevrátí žádné uživatele, pak Azure Active Directory požadavky že službu zřizovat uživatele odpovídající tomu v Azure Active Directory.  Tady je příklad této žádosti: 
  ````
    POST https://.../scim/Users HTTP/1.1
    Authorization: Bearer ...
    Content-type: application/json
    {
      "schemas":
      [
        "urn:ietf:params:scim:schemas:core:2.0:User",
        "urn:ietf:params:scim:schemas:extension:enterprise:2.0User"],
      "externalId":"jyoung",
      "userName":"jyoung",
      "active":true,
      "addresses":null,
      "displayName":"Joy Young",
      "emails": [
        {
          "type":"work",
          "value":"jyoung@Contoso.com",
          "primary":true}],
      "meta": {
        "resourceType":"User"},
       "name":{
        "familyName":"Young",
        "givenName":"Joy"},
      "phoneNumbers":null,
      "preferredLanguage":null,
      "title":null,
      "department":null,
      "manager":null}
  ````
  Knihovny Common Language Infrastructure od společnosti Microsoft pro implementaci služby SCIM by převede tento požadavek na volání metody vytvoření poskytovatele služby.  Metodu Create má tento podpis: 
  ````
    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  

    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource> Create(
      Microsoft.SystemForCrossDomainIdentityManagement.Resource resource, 
      string correlationIdentifier);
  ````
  V požadavku na přidělení uživatele je hodnota argumentu prostředků instanci Microsoft.SystemForCrossDomainIdentityManagement. Třída Core2EnterpriseUser, definována v knihovně Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  Pokud neproběhne ke zřízení uživatele, implementace metody musí vrátit instanci Microsoft.SystemForCrossDomainIdentityManagement. Třída Core2EnterpriseUser s hodnotou vlastnost nastavena na jedinečný identifikátor nově zřízení uživatele na identifikátor.  

3. Provést aktualizaci uživatele ví, že existují v úložišti identity přední stěnou podle SCIM, pokračuje Azure Active Directory tím, že požádá aktuální stav daného uživatele ze služby s žádostí. například: 
  ````
    GET ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...
  ````
  Ve službě sestaven pomocí knihovny Common Language Infrastructure od společnosti Microsoft pro implementaci služby SCIM je požadavek přeložit na volání metody načtení zprostředkovatele služby.  Tady je podpis metody načtení: 
  ````
    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource and 
    // Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters 
    // are defined in Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource> 
       Retrieve(
         Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters 
           parameters, 
           string correlationIdentifier);

    public interface 
      Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters:   
        IRetrievalParameters
        {
          Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier 
            ResourceIdentifier 
              { get; }
    }
    public interface Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier
    {
        string Identifier 
          { get; set; }
        string Microsoft.SystemForCrossDomainIdentityManagement.SchemaIdentifier 
          { get; set; }
    }
  ````
  V příkladu požadavek na načtení aktuálního stavu uživatele jsou hodnoty vlastností objektu zadaný jako hodnota argumentu parametry: 
  
  * Identifikátor: "54D382A4-2050-4C03-94D1-E769F1D15682"
  * SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

4. Pokud atribut typu odkaz je potřeba aktualizovat, Azure Active Directory dotazuje službu, kterou chcete zjistit, zda aktuální hodnotu atributu odkaz v úložišti identity přední stěnou službou již odpovídá hodnota tohoto atributu v Azure Active Adresář. Pro uživatele pouze atributů, které aktuální hodnota je dotazován tímto způsobem je atribut správce. Tady je příklad požadavku k určení, zda atribut manager objektu konkrétní uživatel aktuálně má určitou hodnotu: 
  ````
    GET ~/scim/Users?filter=id eq 54D382A4-2050-4C03-94D1-E769F1D15682 and manager eq 2819c223-7f76-453a-919d-413861904646&attributes=id HTTP/1.1
    Authorization: Bearer ...
  ````
  Hodnota parametru dotazu atributy "id", znamená, že neexistuje, pokud existuje objekt uživatele, který splňuje výraz zadaný jako hodnota parametru dotazu filtru a pak službu očekává se, že odpoví "urn: ietf:params:scim:schemas:core:2.0: Uživatel"nebo"urn: ietf:params:scim:schemas:extension:enterprise:2.0:User"prostředků, včetně pouze hodnotu atributu"id"Tento prostředek.  Hodnota **id** atribut je znám žadatel. Hodnota parametru dotazu filtru; je součástí účelem žádostí o jeho je ve skutečnosti o minimální reprezentace daného prostředku, které splňují výraz filtru jako údaj o tom, zda existuje takový objekt.   

  Pokud služba je vytvořená pomocí knihovny Common Language Infrastructure od společnosti Microsoft pro implementaci služby SCIM, je požadavek přeložit na volání metody dotazu zprostředkovatele služby. Hodnota vlastnosti objektu zadaný jako hodnota argumentu parametry jsou následující: 
  
  * Parametry. AlternateFilters.Count: 2
  * Parametry. AlternateFilters.ElementAt(x). AttributePath: "id"
  * Parametry. AlternateFilters.ElementAt(x). Porovnávací operátor: ComparisonOperator.Equals
  * parameters.AlternateFilter.ElementAt(x).ComparisonValue: "54D382A4-2050-4C03-94D1-E769F1D15682"
  * Parametry. AlternateFilters.ElementAt(y). AttributePath: "správce"
  * Parametry. AlternateFilters.ElementAt(y). Porovnávací operátor: ComparisonOperator.Equals
  * parameters.AlternateFilter.ElementAt(y).ComparisonValue: "2819c223-7f76-453a-919d-413861904646"
  * Parametry. RequestedAttributePaths.ElementAt(0): "id"
  * parameters.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

  Zde hodnotu indexu x může být 0 a hodnotu index y může být 1, nebo může být hodnota parametru x 1 a hodnota y může být 0, v závislosti na pořadí výrazy parametru dotazu filtru.   

5. Tady je příklad požadavku z Azure Active Directory do služby SCIM provést aktualizaci uživatele: 
  ````
    PATCH ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...
    Content-type: application/json
    {
      "schemas": 
      [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"],
      "Operations":
      [
        {
          "op":"Add",
          "path":"manager",
          "value":
            [
              {
                "$ref":"http://.../scim/Users/2819c223-7f76-453a-919d-413861904646",
                "value":"2819c223-7f76-453a-919d-413861904646"}]}]}
  ````
  Knihovny Microsoft Common Language Infrastructure pro implementaci služby SCIM by převede požadavek na volání metody aktualizace zprostředkovatele služby. Tady je podpis metody aktualizace: 
  ````
    // System.Threading.Tasks.Tasks and 
    // System.Collections.Generic.IReadOnlyCollection<T>
    // are defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IPatch, 
    // Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
    // Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation, 
    // Microsoft.SystemForCrossDomainIdentityManagement.OperationName, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IPath and 
    // Microsoft.SystemForCrossDomainIdentityManagement.OperationValue 
    // are all defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 

    System.Threading.Tasks.Task Update(
      Microsoft.SystemForCrossDomainIdentityManagement.IPatch patch, 
      string correlationIdentifier);

    public interface Microsoft.SystemForCrossDomainIdentityManagement.IPatch
    {
    Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase 
      PatchRequest 
        { get; set; }
    Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier 
      ResourceIdentifier 
        { get; set; }        
    }

    public class PatchRequest2: 
      Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase
    {
    public System.Collections.Generic.IReadOnlyCollection
      <Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation> 
        Operations
        { get;}

    public void AddOperation(
      Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation operation);
    }

    public class PatchOperation
    {
    public Microsoft.SystemForCrossDomainIdentityManagement.OperationName 
      Name
      { get; set; }

    public Microsoft.SystemForCrossDomainIdentityManagement.IPath 
      Path
      { get; set; }

    public System.Collections.Generic.IReadOnlyCollection
      <Microsoft.SystemForCrossDomainIdentityManagement.OperationValue> Value
      { get; }

    public void AddValue(
      Microsoft.SystemForCrossDomainIdentityManagement.OperationValue value);
    }

    public enum OperationName
    {
      Add,
      Remove,
      Replace
    }

    public interface IPath
    {
      string AttributePath { get; }
      System.Collections.Generic.IReadOnlyCollection<IFilter> SubAttributes { get; }
      Microsoft.SystemForCrossDomainIdentityManagement.IPath ValuePath { get; }
    }

    public class OperationValue
    {
      public string Reference
      { get; set; }

      public string Value
      { get; set; }
    }
  ````
    V příkladu požadavek na aktualizaci uživatele má zadaný jako hodnota argumentu oprava objekt hodnoty těchto vlastností: 
  
  * ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
  * ResourceIdentifier.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"
  * (PatchRequest jako PatchRequest2). Operations.Count: 1
  * (PatchRequest jako PatchRequest2). Operations.ElementAt(0). OperationName: OperationName.Add
  * (PatchRequest jako PatchRequest2). Operations.ElementAt(0). Path.AttributePath: "správce"
  * (PatchRequest jako PatchRequest2). Operations.ElementAt(0). Value.Count: 1
  * (PatchRequest jako PatchRequest2). Operations.ElementAt(0). Value.ElementAt(0). Referenční dokumentace: http://.../scim/Users/2819c223-7f76-453a-919d-413861904646
  * (PatchRequest jako PatchRequest2). Operations.ElementAt(0). Value.ElementAt(0). Hodnota: 2819c223-7f76-453a-919d-413861904646

6. Zrušte zřídit uživatele z identity úložiště přední stěnou služba SCIM, Azure AD, jako odešle žádost: 
  ````
    DELETE ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...
  ````
  Pokud služba je vytvořená pomocí knihovny Common Language Infrastructure od společnosti Microsoft pro implementaci služby SCIM, je požadavek přeložit na volání metody odstranění zprostředkovatele služby.   Tato metoda má tento podpis: 
  ````
    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
    // is defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 
    System.Threading.Tasks.Task Delete(
      Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier  
        resourceIdentifier, 
      string correlationIdentifier);
  ````
  Zadaný jako hodnota argumentu resourceIdentifier objekt má tyto hodnoty vlastností v příkladu žádost zrušte zřídit uživatele: 
  
  * ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
  * ResourceIdentifier.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

## <a name="group-provisioning-and-de-provisioning"></a>Zřizování skupiny a jeho rušení
Následující obrázek znázorňuje zprávy, že Azure AcD odešle SCIM službě pro správu životního cyklu skupiny v jiné úložiště identit.  Tyto zprávy se liší od zprávy týkající se uživatelů třemi způsoby: 

* Schéma skupiny prostředků je označený jako `http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/Group`.  
* Požadavky k načtení skupin stanovení, že je atribut členů mají být vyloučeny z jakéhokoliv prostředku v odpovědi na požadavek.  
* Požadavky na určit, zda atribut typu odkaz má určitou hodnotu jsou žádosti o atribut členy.  

![][5]
*Obrázek 6: Zřizování skupiny a jeho rušení pořadí*

## <a name="related-articles"></a>Související články
* [Rejstřík článků o správě aplikací ve službě Azure Active Directory](active-directory-apps-index.md)
* [Automatizovat uživatele zřízení nebo zrušení zřízení k aplikacím SaaS](active-directory-saas-app-provisioning.md)
* [Přizpůsobení mapování atributů pro zřizování uživatelů](active-directory-saas-customizing-attribute-mappings.md)
* [Zapisují se výrazy pro mapování atributů](active-directory-saas-writing-expressions-for-attribute-mappings.md)
* [Filtry pro zřizování uživatelů oborů](active-directory-saas-scoping-filters.md)
* [Účet zřizování oznámení](active-directory-saas-account-provisioning-notifications.md)
* [Seznam kurzů k integraci aplikací SaaS](active-directory-saas-tutorial-list.md)

<!--Image references-->
[0]: ./media/active-directory-scim-provisioning/scim-figure-1.PNG
[1]: ./media/active-directory-scim-provisioning/scim-figure-2a.PNG
[2]: ./media/active-directory-scim-provisioning/scim-figure-2b.PNG
[3]: ./media/active-directory-scim-provisioning/scim-figure-3.PNG
[4]: ./media/active-directory-scim-provisioning/scim-figure-4.PNG
[5]: ./media/active-directory-scim-provisioning/scim-figure-5.PNG
