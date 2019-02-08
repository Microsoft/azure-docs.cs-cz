---
title: Automatizace zřizování aplikací pomocí SCIM ve službě Azure Active Directory | Dokumentace Microsoftu
description: Azure Active Directory můžete automaticky zřizovat uživatele a skupiny, které se aplikace nebo identity úložiště, který je přední stěnou webová služba s rozhraním definovaných ve specifikaci protokolu SCIM
services: active-directory
documentationcenter: ''
author: barbkess
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/12/2017
ms.author: barbkess
ms.reviewer: asmalser
ms.custom: aaddev;it-pro;seohack1
ms.openlocfilehash: faebf6c5e7b32ec842c19af07e36a1120156e103
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2019
ms.locfileid: "55894118"
---
# <a name="using-system-for-cross-domain-identity-management-scim-to-automatically-provision-users-and-groups-from-azure-active-directory-to-applications"></a>Automatické zřizování uživatelů a skupin ze služby Azure Active Directory do aplikací pomocí systému pro mezi doménami Identity Management (SCIM)

## <a name="overview"></a>Přehled
Azure Active Directory (Azure AD) je možné automaticky zřizovat uživatele a skupiny do jakékoli aplikace nebo identity úložiště, které je přední stěnou webovou službou pomocí rozhraní definují ve [systému pro protokol mezi doménami Identity Management (SCIM) 2.0 specifikace](https://tools.ietf.org/html/draft-ietf-scim-api-19). Azure Active Directory může odesílat požadavky na vytvoření, úprava nebo odstranění přiřazené uživatele a skupiny k webové službě. Webová služba potom může znamenat tyto požadavky týkající se cílové úložiště identit. 

>[!IMPORTANT]
>Chování Azure AD SCIM implementace poslední aktualizace 18. prosince 2018. Informace o tom, co se změnilo, naleznete v tématu [SCIM 2.0 protokol dodržování předpisů služby zřizování uživatelů služby Azure AD](application-provisioning-config-problem-scim-compatibility.md).

![][0]
*Obrázek 1: Zřizování úložiště identit prostřednictvím webové služby z Azure Active Directory*

Tuto funkci lze použít ve spojení s možností "přineste si vlastní aplikaci" ve službě Azure AD. Tato možnost umožňuje jednotné přihlašování a automatické zřizování pro aplikace, které jsou přední stěnou webová služba SCIM uživatelů.

Existují dva případy použití pro pomocí SCIM ve službě Azure Active Directory:

* **Zřizování uživatelů a skupin a aplikace s podporou SCIM** – aplikací, které podporují SCIM 2.0 a používala tokeny nosičů OAuth pro ověřování fungují s Azure AD bez konfigurace.
  
* **Vytváření vlastních řešení pro zřizování pro aplikace, které podporují jiné založené na rozhraní API zřizování** – pro aplikace bez SCIM vytvoříte SCIM koncový bod pro převod mezi koncového bodu Azure AD SCIM a jakéhokoli rozhraní API podporuje aplikace pro zřizování uživatelů. A pomohou vám vytvořit koncový bod SCIM, jsou knihovny společné jazykové infrastruktury (CLI) spolu s ukázek kódu, které ukazují, jak zadat koncový bod SCIM a překládat SCIM zprávy.  

## <a name="provisioning-users-and-groups-to-applications-that-support-scim"></a>Zřizování uživatelů a skupin a aplikace s podporou SCIM
Dá se Azure AD automaticky zřizovat přiřazené uživatele a skupiny, které se aplikace, které implementují [systému pro správu identit mezi doménami 2 (SCIM)](https://tools.ietf.org/html/draft-ietf-scim-api-19) webové služby a přijímat tokeny nosičů OAuth pro ověřování. V rámci specifikace SCIM 2.0 musí aplikace splňovat tyto požadavky:

* Podporuje vytváření uživatele a/nebo skupiny, jak je uvedeno části 3.3 SCIM protokolu.  
* Podporuje úpravy uživatele a/nebo skupiny s žádostmi o opravu podle části 3.5.2 SCIM protokolu.  
* Podporuje načítání známých prostředků podle části 3.4.1 SCIM protokolu.  
* Podporuje dotazování uživatele a/nebo skupiny, jak je uvedeno části 3.4.2 SCIM protokolu.  Ve výchozím nastavení jsou externalId dotazovat uživatele a skupiny jsou dotazovat displayName.  
* Podporuje dotazování uživatele tak, že ID a správce podle části 3.4.2 SCIM protokolu.  
* Podporuje dotazování skupiny tak, že ID a člen podle části 3.4.2 SCIM protokolu.  
* Přijímá tokeny nosičů OAuth pro autorizaci podle části 2.1 SCIM protokolu.

Obraťte se na svého poskytovatele aplikace nebo dokumentaci poskytovatele aplikace pro příkazy kompatibilitu s těmito požadavky.

### <a name="getting-started"></a>Začínáme
Aplikace s podporou SCIM profilu je popsáno v tomto článku můžou být připojen k Azure Active Directory pomocí funkce "aplikaci mimo Galerii" v galerii aplikací Azure AD. Jakmile budete připojeni, Azure AD spustí proces synchronizace každých 40 minut, kde dotazuje aplikace SCIM koncový bod pro přiřazení uživatelé a skupiny a vytvoří nebo upraví podle podrobnosti o přiřazení.

**Připojení aplikace, která podporuje SCIM:**

1. Přihlaste se k [na webu Azure portal](https://portal.azure.com). 
2. Přejděte do **Azure Active Directory > podnikové aplikace**a vyberte **novou aplikaci > všechny > aplikace mimo galerii**.
3. Zadejte název pro vaši aplikaci a klikněte na tlačítko **přidat** ikonu pro vytvoření objektu aplikace.
    
   ![][1]
   *Obrázek 2: Galerie aplikací Azure AD*
    
4. Zobrazenou obrazovku, vyberte **zřizování** kartu v levém sloupci.
5. V **režim zřizování** nabídce vyberte možnost **automatické**.
    
   ![][2]
   *Obrázek 3: Konfigurace zřizování na webu Azure Portal*
    
6. V **adresy URL Tenanta** zadejte adresu URL koncového bodu SCIM vaší aplikace. Příklad: https://api.contoso.com/scim/v2/
7. Pokud koncový bod SCIM vyžaduje tokenu nosiče OAuth z vystavitele než Azure AD, zkopírujte požadované tokenu nosiče OAuth nepovinný **tajný klíč tokenu** pole. Pokud toto pole necháte prázdné, Azure AD zahrnuje vydané ze služby Azure AD s každou žádostí tokenu nosiče OAuth. Aplikace, které používají Azure AD jako zprostředkovatele identity můžete ověřit tento Azure AD vystavený token.
8. Klikněte na tlačítko **Test připojení** tlačítko s Azure Active Directory, pokus o připojení ke koncovému bodu SCIM. Pokud se nezdaří pokusy, zobrazí se informace o chybě.  

    >[!NOTE]
    >**Test připojení** dotazuje SCIM koncový bod pro uživatele, který neexistuje, pomocí náhodný GUID jako odpovídající vlastnost vybrané v konfiguraci Azure AD. Očekávaný správná odpověď je prázdná zpráva SCIM ListResponse HTTP 200 OK. 

9. Pokud pokusy o připojení k aplikaci uspět, pak klikněte na tlačítko **Uložit** k uložení přihlašovacích údajů správce.
10. V **mapování** části, existují dvě sady vybrat mapování atributů: jeden pro uživatelské objekty a jeden pro objekty skupiny. Vyberte každé z nich ke kontrole atributů, které se synchronizují ze služby Azure Active Directory do vaší aplikace. Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatele a skupiny ve vaší aplikaci pro operace update. Vyberte tlačítko Uložit potvrďte změny.

    >[!NOTE]
    >Volitelně můžete zakázat synchronizaci objektů skupiny zakázáním "groups" mapování. 

11. V části **nastavení**, **oboru** pole určuje, kteří uživatelé a skupiny synchronizované. Výběr "Synchronizovat jenom přiřazené uživatele a skupiny" (doporučeno) bude synchronizovat jenom uživatelé a skupiny přiřazení v **uživatelů a skupin** kartu.
12. Po dokončení konfigurace se změnit **stavu zřizování** k **na**.
13. Klikněte na tlačítko **Uložit** spustit služba zřizování Azure AD. 
14. Je-li synchronizovat jenom přiřazené uživatele a skupiny (doporučeno), je potřeba vybrat možnost **uživatelů a skupin** kartu a přiřaďte uživatele a/nebo skupiny, kterou chcete synchronizovat.

Jakmile se počáteční synchronizace se spustila, můžete použít **protokoly auditu** kartu sledování průběhu, který zobrazuje všechny akce provedené v zřizovací služba ve vaší aplikaci. Další informace o tom, jak číst zřizování protokoly Azure AD najdete v tématu [hlášení o zřizování automatické uživatelských účtů](check-status-user-account-provisioning.md).

> [!NOTE]
> Počáteční synchronizace trvá déle než při následné synchronizace, ke kterým dochází přibližně každých 40 minut za předpokladu, že služba běží. 


## <a name="building-your-own-provisioning-solution-for-any-application"></a>Vytváření vlastních řešení pro zřizování pro všechny aplikace
Vytvořením SCIM webové služby, které sdílí rozhraní se službou Azure Active Directory, můžete povolit jeden uživatel přihlašování a automatické zřizování pro prakticky jakoukoli aplikaci, která poskytuje zřizování rozhraní API REST a SOAP uživatelů.

Zde je, jak to funguje:

1. Azure AD poskytuje společné knihovny jazyka infrastruktury s názvem [Microsoft.SystemForCrossDomainIdentityManagement](https://www.nuget.org/packages/Microsoft.SystemForCrossDomainIdentityManagement/). Systémoví integrátoři a vývojáře, můžete použít tuto knihovnu k vytvoření a nasazení koncového bodu SCIM webové služby lze připojit k libovolné aplikace úložiště identit Azure AD.
2. Mapování jsou implementovány ve webové službě k mapování schématu standardizované uživatele na schéma uživatele a protokol vyžadovaného aplikací.
3. Adresa URL koncového bodu je zaregistrovaný ve službě Azure AD jako součást vlastní aplikace v galerii aplikací.
4. Uživatelé a skupiny jsou přiřazeny k této aplikaci ve službě Azure AD. Po přiřazení jsou vloženy do fronty se dá provést synchronizace k cílové aplikaci. Procesu synchronizace zpracování fronty se spouští každých 40 minut.

### <a name="code-samples"></a>Ukázky kódů
Pro zjednodušení tohoto procesu [ukázky kódu](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master) jsou k dispozici, vytvořte koncový bod webové služby SCIM a ukažte, automatické zřizování. Jeden vzorek je zprostředkovatel, který udržuje soubor s řádky reprezentující uživatele a skupiny hodnot oddělených čárkami.  Druhá je poskytovatele, který funguje ve službě Amazon Web Services Identity a správy přístupu.  

**Požadavky**

* Visual Studio 2013 nebo novější
* [Azure SDK pro .NET](https://azure.microsoft.com/downloads/)
* Windows počítač, který podporuje rozhraní ASP.NET 4.5 se použije jako koncový bod SCIM. Tento počítač musí být přístupné z cloudu
* [Předplatné Azure s verzí zkušební verzi nebo s licencí Azure AD Premium](https://azure.microsoft.com/services/active-directory/)

### <a name="getting-started"></a>Začínáme
Nejjednodušší způsob, jak implementovat SCIM koncový bod, který může přijímat žádosti o zřízení ze služby Azure AD je k sestavení a nasazení vzorového kódu, jejichž výstupem jsou zřízené uživatele do souboru hodnot oddělených čárkami (CSV).

#### <a name="to-create-a-sample-scim-endpoint"></a>Chcete-li vytvořit koncový bod ukázka SCIM

1. Stáhněte si balíček ukázky kódu v [https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master)
2. Rozbalte balíček a umístěte ji na svém počítači s Windows do umístění, jako je například C:\AzureAD-BYOA-Provisioning-Samples\.
3. V této složce spusťte FileProvisioning\Host\FileProvisioningService.csproj projektu v sadě Visual Studio.
4. Vyberte **nástroje > Správce balíčků NuGet > Konzola správce balíčků**a spusťte následující příkazy pro projekt FileProvisioningService k vyřešení odkazů řešení:

   ```
    Update-Package -Reinstall
   ```

5. FileProvisioningService projekt sestavte.
6. Spuštění aplikace příkazového řádku ve Windows (jako správce) a použít **cd** příkazu změňte adresář na vaše **\AzureAD-BYOA-Provisioning-Samples\FileProvisioning\Host\bin\Debug**složky.
7. Spusťte následující příkaz, < adresa > nahraďte IP adresy nebo domény název počítače Windows:

   ```
    FileSvc.exe http://<ip-address>:9000 TargetFile.csv
   ```

8. Ve Windows v rámci **nastavení Windows > síť a Internet nastavení**, vyberte **brány Windows Firewall > Upřesnit nastavení**a vytvoření **příchozí pravidlo** , který umožňuje příchozí přístup k portu 9000.
9. Pokud počítač Windows je za směrovač a směrovač je potřeba nakonfigurovat provádět překlad přístupu mezi jeho port 9000, který je přístupný z Internetu a port 9000 na počítači s Windows. Tato konfigurace je nutná pro službu Azure AD bude mít přístup k tomuto koncovému bodu v cloudu.

#### <a name="to-register-the-sample-scim-endpoint-in-azure-ad"></a>K registraci koncového bodu SCIM ukázky v Azure AD

1. Přihlaste se k [na webu Azure portal](https://portal.azure.com). 
2. Přejděte do **Azure Active Directory > podnikové aplikace**a vyberte **novou aplikaci > všechny > aplikace mimo galerii**.
3. Zadejte název pro vaši aplikaci a klikněte na tlačítko **přidat** ikonu pro vytvoření objektu aplikace. Objekt aplikace vytvořené slouží k reprezentaci cílové aplikace, můžete by zřizování a implementaci jednotného přihlašování pro a nejen SCIM koncový bod.
4. Zobrazenou obrazovku, vyberte **zřizování** kartu v levém sloupci.
5. V **režim zřizování** nabídce vyberte možnost **automatické**.
    
   ![][2]
   *Obrázek 4: Konfigurace zřizování na webu Azure Portal*
    
6. V **adresy URL Tenanta** pole, zadejte adresu URL a port koncového bodu SCIM vystavený Internetu. Položka je něco jako http://testmachine.contoso.com:9000 nebo http://\<ip-address >: 9000 /, kde \<ip-address > je vystavený Internetu IP adresu.  
7. Pokud koncový bod SCIM vyžaduje tokenu nosiče OAuth z vystavitele než Azure AD, zkopírujte požadované tokenu nosiče OAuth nepovinný **tajný klíč tokenu** pole. Pokud toto pole necháte prázdné, bude obsahovat Azure AD z Azure AD s každou žádostí vydány tokenu nosiče OAuth. Aplikace, které používají Azure AD jako zprostředkovatele identity můžete ověřit této služby Azure AD – vydaný token.
8. Klikněte na tlačítko **Test připojení** tlačítko s Azure Active Directory, pokus o připojení ke koncovému bodu SCIM. Pokud se nezdaří pokusy, zobrazí se informace o chybě.  

    >[!NOTE]
    >**Test připojení** dotazuje SCIM koncový bod pro uživatele, který neexistuje, pomocí náhodný GUID jako odpovídající vlastnost vybrané v konfiguraci Azure AD. Očekávaný správná odpověď HTTP 200 OK je s prázdnou zprávu SCIM ListResponse

9. Pokud pokusy o připojení k aplikaci uspět, pak klikněte na tlačítko **Uložit** k uložení přihlašovacích údajů správce.
10. V **mapování** části, existují dvě sady vybrat mapování atributů: jeden pro uživatelské objekty a jeden pro objekty skupiny. Vyberte každé z nich ke kontrole atributů, které se synchronizují ze služby Azure Active Directory do vaší aplikace. Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatele a skupiny ve vaší aplikaci pro operace update. Vyberte tlačítko Uložit potvrďte změny.
11. V části **nastavení**, **oboru** pole určuje, kteří uživatelé nebo skupiny synchronizované. Výběr "Synchronizovat jenom přiřazené uživatele a skupiny" (doporučeno) bude synchronizovat jenom uživatelé a skupiny přiřazení v **uživatelů a skupin** kartu.
12. Po dokončení konfigurace se změnit **stavu zřizování** k **na**.
13. Klikněte na tlačítko **Uložit** spustit služba zřizování Azure AD. 
14. Je-li synchronizovat jenom přiřazené uživatele a skupiny (doporučeno), je potřeba vybrat možnost **uživatelů a skupin** kartu a přiřaďte uživatele a/nebo skupiny, kterou chcete synchronizovat.

Jakmile se počáteční synchronizace se spustila, můžete použít **protokoly auditu** kartu sledování průběhu, který zobrazuje všechny akce provedené v zřizovací služba ve vaší aplikaci. Další informace o tom, jak číst zřizování protokoly Azure AD najdete v tématu [hlášení o zřizování automatické uživatelských účtů](check-status-user-account-provisioning.md).

V posledním kroku ověřování ukázka je k otevření souboru TargetFile.csv \AzureAD-BYOA-Provisioning-Samples\ProvisioningAgent\bin\Debug složky na svém počítači s Windows. Po spuštění procesu zřizování tento soubor obsahuje podrobné informace o všech přiřazeny a zřízení uživatelů a skupin.

### <a name="development-libraries"></a>Knihovny pro vývoj
K vývoji vlastní webové služby, který odpovídá specifikaci SCIM, nejdřív seznámíte s následující knihovny poskytované společností Microsoft vám pomohou zrychlit proces vývoje: 

1. Společné jazykové infrastruktury (CLI) knihovny se nabízejí pro použití s jazyky na základě této infrastruktury, jako je C#. Jeden z těchto knihoven [Microsoft.SystemForCrossDomainIdentityManagement.Service](https://www.nuget.org/packages/Microsoft.SystemForCrossDomainIdentityManagement/), deklaruje rozhraní, Microsoft.SystemForCrossDomainIdentityManagement.IProvider, je znázorněno na následujícím obrázku:  Vývojáři používat knihovny by implementovat rozhraní s třídou, která může být uvedené, obecně jako poskytovatel. Tyto knihovny umožňují vývojářům nasazení webové služby, který odpovídá specifikaci SCIM. Webová služba může být buď hostovaný v rámci Internetové informační službě nebo libovolného spustitelného sestavení Common Language Infrastructure. Požadavek je přeložit na volání metody zprostředkovatele, které by být naprogramovány vývojářem na provádět některé úložiště identit.
  
   ![][3]
  
2. [Express route obslužné rutiny](https://expressjs.com/guide/routing.html) jsou k dispozici pro analýzu node.js požadavek objektů představujících volání (jak jsou definovány ve specifikaci SCIM) provedené webových služeb node.js.   

### <a name="building-a-custom-scim-endpoint"></a>Vytváření koncových bodů vlastního SCIM
Pomocí rozhraní příkazového řádku knihoven, vývojáře, kteří používají tyto knihovny hostování svých služeb v rámci jakékoli spustitelného sestavení Common Language Infrastructure nebo v rámci Internetové informační služby. Tady je ukázkový kód pro hostování služby v rámci spustitelného sestavení, na adrese http://localhost:9000: 

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

Tato služba musí mít HTTP adresu a server ověřovací certifikát z nich kořenové certifikační autority je jedním z následujících názvů: 

* CNNIC
* Comodo
* CyberTrust
* DigiCert
* GeoTrust
* GlobalSign
* Go Daddy
* VeriSign
* WoSign

Ověřovací certifikát serverů může být vázaný na port na hostiteli Windows pomocí nástroje network shell: 

    netsh http add sslcert ipport=0.0.0.0:443 certhash=0000000000003ed9cd0c315bbb6dc1c08da5e6 appid={00112233-4455-6677-8899-AABBCCDDEEFF}  

Hodnota poskytnutá pro certhash argument tady, je kryptografický otisk certifikátu, zatímco hodnota poskytnutá pro appid argument je libovolný globálně jedinečný identifikátor.  

K hostování služby v rámci Internetové informační služby, vývojář bude sestavení s CLA kód knihovny s třídou s názvem po spuštění ve výchozím oboru názvů sestavení.  Tady je příklad této třídy: 

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
      Owin.IAppBuilder builder) // Defined in Owin.dll.  
    {
        this.starter.ConfigureApplication(builder);
    }
    }

### <a name="handling-endpoint-authentication"></a>Ověřování koncových bodů zpracování
Požadavky z Azure Active Directory obsahovat nosného tokenu OAuth 2.0.   Jakoukoli službu přijetí požadavku by měl ověřit vystavitele jako Azure Active Directory jménem očekávanému tenantovi Azure Active Directory pro přístup k webové službě Azure Active Directory Graphu.  V tokenu, Vystavitel je identifikován deklaraci identity iss, stejně jako "jednotky ISS – překročené": "https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/".  V tomto příkladu základní adresa hodnotu deklarace identity https://sts.windows.net, identifikuje Azure Active Directory jako vystavitel, zatímco relativní adresy segmentu, cbb1a5ac f33b-45fa-9bf5-f37db0fed422, je jedinečný identifikátor tenanta Azure Active Directory na jménem, z nichž byl token vydán.  Pokud byl token vydán pro přístup k webové službě Azure Active Directory Graphu, musí být identifikátor této služby, 00000002-0000-0000-c000-000000000000, v hodnotu deklarace identity aud tokenu.  

Vývojáři, kteří používají knihovny CLA poskytovaných microsoftem pro vytváření služeb SCIM může ověřit požadavky z Azure Active Directory pomocí balíčku Microsoft.Owin.Security.ActiveDirectory pomocí následujících kroků: 

1. Ve zprostředkovateli implementace Microsoft.SystemForCrossDomainIdentityManagement.IProvider.StartupBehavior vlastnosti tak, že se vrátí metoda má být volána při každém spuštění služby: 

   ```
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
   ```

2. Přidejte následující kód k této metodě mít každá žádost k některé z koncových bodů služby ověřen jako opatřené u tokenu vydaného službou Azure Active Directory jménem zadaného klienta, pro přístup k webové službě Azure AD Graph: 

   ```
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
   ```


## <a name="user-and-group-schema"></a>Schéma uživatele a skupiny
Azure Active Directory můžete zřídit dva typy prostředků, aby SCIM webové služby.  Tyto typy prostředků jsou uživatelé a skupiny.  

Prostředky uživatelů jsou označeny identifikátorem schématu, "urn: ietf:params:scim:schemas:extension:enterprise:2.0:User", která je obsažena v téhle specifikaci protokolu: http://tools.ietf.org/html/draft-ietf-scim-core-schema.  Výchozí mapování atributů uživatelů ve službě Azure Active Directory pro atributy "urn: ietf:params:scim:schemas:extension:enterprise:2.0:User" prostředky jsou uvedeny v tabulce 1, níže.  

Prostředky skupiny prostředků jsou označeny identifikátor schématu http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/Group.  Tabulka 2 níže ukazuje výchozí mapování atributů skupin v Azure Active Directory atributů http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/Group prostředky.  

### <a name="table-1-default-user-attribute-mapping"></a>Tabulka 1: Výchozí mapování atributů uživatele

| Uživatele Azure Active Directory | "urn: ietf:params:scim:schemas:extension:enterprise:2.0:User" |
| --- | --- |
| IsSoftDeleted |aktivní |
| displayName |displayName |
| Ve TelephoneNumber |phoneNumbers [typ eq "fax"] .value |
| givenName |name.givenName |
| pracovní funkce |název |
| e-mailu |e-mailů [typ eq "pracovní"] .value |
| mailNickname |externalId |
| manažer |manažer |
| Mobilní zařízení |phoneNumbers [eq typ "mobilní"] .value |
| ID objektu |ID |
| PSČ |.postalCode adresy [typ eq "pracovní"] |
| Adresy proxy serveru |e-mailů [Zadejte eq "other"]. Hodnota |
| fyzický Delivery-OfficeName |adresy [Zadejte eq "other"]. Ve formátu |
| streetAddress |.streetAddress adresy [typ eq "pracovní"] |
| Příjmení |name.familyName |
| telefonní číslo |phoneNumbers [typ eq "pracovní"] .value |
| user-PrincipalName |uživatelské jméno |

### <a name="table-2-default-group-attribute-mapping"></a>Tabulka 2: Výchozí skupiny atributů mapování

| Skupina Azure Active Directory | http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/Group |
| --- | --- |
| displayName |externalId |
| e-mailu |e-mailů [typ eq "pracovní"] .value |
| mailNickname |displayName |
| členové |členové |
| ID objektu |ID |
| proxyAddresses |e-mailů [Zadejte eq "other"]. Hodnota |

## <a name="user-provisioning-and-de-provisioning"></a>Zřizování uživatelů a jeho rušení
Následující ilustrace ukazuje zprávy, že odesílá do SCIM služby Azure Active Directory ke správě životního cyklu uživatele v jiné úložiště identit. Diagram také ukazuje, jak služba SCIM implementovaná pomocí knihovny rozhraní příkazového řádku poskytovaných microsoftem pro vytváření, že tyto služby převede tyto požadavky na volání metody zprostředkovatele.  

![][4]
*Obrázek 5: Zřizování uživatelů a jeho rušení pořadí*

1. Azure Active Directory dotáže služby pro uživatele s hodnotou atributu externalId odpovídající hodnota atributu mailNickname uživatele ve službě Azure AD. Dotaz je vyjádřena jako žádost protokolu HTTP (Hypertext Transfer), jako je tento příklad, ve které jyoung je ukázka mailNickname uživatele v Azure Active Directory: 

   ```
     GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
     Authorization: Bearer ...
   ```

   Pokud služba je vytvořená pomocí knihovny Common Language Infrastructure pro implementaci SCIM služeb poskytovaných microsoftem, požadavek přeložit na volání metody dotazu poskytovatele.  Tady je signatura této metody: 

   ```
     // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
     // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
     // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
     // Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters is defined in 
     // Microsoft.SystemForCrossDomainIdentityManagement.Protocol.  
 
     System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource[]>  Query(
       Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters parameters, 
       string correlationIdentifier);
   ```

   Tady je definice Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters rozhraní: 

   ```
     public interface IQueryParameters: 
       Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
     {
         System.Collections.Generic.IReadOnlyCollection  <Microsoft.SystemForCrossDomainIdentityManagement.IFilter> AlternateFilters 
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
   ```

   V následujícím ukázkovém dotazu pro uživatele s danou hodnotou pro atribut externalId jsou hodnoty argumentů předaných metodě dotazu: 
   * parameters.AlternateFilters.Count: 1
   * Parametry. AlternateFilters.ElementAt(0). AttributePath: externalId""
   * parameters.AlternateFilters.ElementAt(0).ComparisonOperator: ComparisonOperator.Equals
   * Parametry. AlternateFilter.ElementAt(0). ComparisonValue: "jyoung"
   * correlationIdentifier: System.Net.Http.HttpRequestMessage.GetOwinEnvironment["owin.RequestId"] 

2. Pokud odpověď na dotaz na webovou službu pro uživatele s externalId hodnotu atributu, který odpovídá hodnota atributu mailNickname uživatele nevrací žádné uživatele, pak Azure Active Directory si vyžádá, že služba zřízení uživatele odpovídá ten ve službě Azure Active Directory.  Tady je příklad takové žádosti: 

   ```
     POST https://.../scim/Users HTTP/1.1
     Authorization: Bearer ...
     Content-type: application/scim+json
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
   ```

   Common Language Infrastructure knihovny poskytované společností Microsoft pro implementaci služeb SCIM by převede tento požadavek na volání metody vytvoření poskytovatele služby.  Metoda vytvoření má tento podpis: 

   ```
     // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
     // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
     // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
 
     System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource> Create (
       Microsoft.SystemForCrossDomainIdentityManagement.Resource resource, 
       string correlationIdentifier);
   ```

   V požadavku na zřízení uživatele je hodnota argumentu prostředků instance Microsoft.SystemForCrossDomainIdentityManagement. Třída Core2EnterpriseUser definovaný v knihovně Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  Pokud je žádost o zřízení uživatele úspěšné, implementaci metody se má vracet instanci Microsoft.SystemForCrossDomainIdentityManagement. Třída Core2EnterpriseUser s hodnotou nastavenou na jedinečný identifikátor nově zřízeného uživatele vlastnost identifikátor.  

3. Provést aktualizaci uživatele ví, že existují v úložišti identity přední stěnou podle SCIM, Azure Active Directory pokračuje vyžádáním aktuální stav daného uživatele ze služby s žádostí, jako například: 

   ```
     GET ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
     Authorization: Bearer ...
   ```

   Ve službě vytvořené pomocí knihovny Common Language Infrastructure pro implementaci SCIM služeb poskytovaných microsoftem je požadavek přeložit na volání metody načtení poskytovatele služby.  Následuje podpis metody načtení: 

    ```
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
   ```

   V příkladu požadavek pro načtení aktuálního stavu uživatele hodnoty vlastnosti objektu, který je uvedený jako hodnota argumentu parametry jsou následující: 
  
   * Identifikátor: "54D382A4-2050-4C03-94D1-E769F1D15682"
   * SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

4. Pokud je atribut typu odkaz má být aktualizován, Azure Active Directory dotáže služby k určení, zda aktuální hodnotu atributu odkazu v úložišti identity přední stěnou službou už odpovídá hodnotě atributu v Azure Active Adresář. Pro uživatele je jediným atributem, z nichž aktuální hodnota je dotazován tímto způsobem atribut správce. Tady je příklad požadavku k určení, zda atribut manager objektu konkrétní uživatel aktuálně má určitou hodnotu: 

   ```
     GET ~/scim/Users?filter=id eq 54D382A4-2050-4C03-94D1-E769F1D15682 and manager eq  2819c223-7f76-453a-919d-413861904646&attributes=id HTTP/1.1
     Authorization: Bearer ...
   ```

   Hodnota parametru dotazu atributy "ID" označuje, pokud existuje objekt uživatele, který splňuje výraz zadaný jako hodnota parametru dotazu filtr a potom služby očekává odpověď s "urn: ietf:params:scim:schemas:core:2.0: Uživatel"nebo"urn: ietf:params:scim:schemas:extension:enterprise:2.0:User"prostředků, včetně pouze hodnotu atributu"ID"tohoto prostředku.  Hodnota **ID** atribut se označuje žadateli. Hodnota parametru dotazu filtr; je součástí žádá o jeho účelem je ve skutečnosti požadavek minimální reprezentace daného prostředku, aby neodpovídajících výraz filtru jako údaj o tom, zda existuje takový objekt.   

   Pokud služba je vytvořená pomocí knihovny Common Language Infrastructure pro implementaci SCIM služeb poskytovaných microsoftem, požadavek přeložit na volání metody dotazu poskytovatele. Hodnota vlastnosti objektu, který je uvedený jako hodnota argumentu parametry jsou následující: 
  
   * parameters.AlternateFilters.Count: 2
   * parameters.AlternateFilters.ElementAt(x).AttributePath: ID
   * parameters.AlternateFilters.ElementAt(x).ComparisonOperator: ComparisonOperator.Equals
   * parameters.AlternateFilter.ElementAt(x).ComparisonValue:  "54D382A4-2050-4C03-94D1-E769F1D15682"
   * Parametry. AlternateFilters.ElementAt(y). AttributePath: "správce"
   * parameters.AlternateFilters.ElementAt(y).ComparisonOperator: ComparisonOperator.Equals
   * parameters.AlternateFilter.ElementAt(y).ComparisonValue:  "2819c223-7f76-453a-919d-413861904646"
   * parameters.RequestedAttributePaths.ElementAt(0): ID
   * parameters.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

   Tady hodnota indexu x může být 0 a hodnota y indexu může být 1, nebo hodnota x může být 1 a hodnota y může být 0, v závislosti na pořadí výrazy parametr dotazu filter.   

5. Tady je příklad požadavku z Azure Active Directory na službu SCIM provést aktualizaci uživatele: 

   ```
     PATCH ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
     Authorization: Bearer ...
     Content-type: application/scim+json
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
   ```

   Knihovny Microsoft Common Language Infrastructure pro implementaci služeb SCIM převedla žádost do volání metody aktualizace poskytovatele služby. Následuje podpis metody Update: 

   ```
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
   ```

    V tomto příkladu požadavek na aktualizaci uživatele má zadaný objekt jako hodnota argumentu oprava hodnoty těchto vlastností: 
  
   * ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
   * ResourceIdentifier.SchemaIdentifier: "urn: ietf:params:scim:schemas:extension:enterprise:2.0:User"
   * (PatchRequest jako PatchRequest2). Operations.Count: 1
   * (PatchRequest jako PatchRequest2). Operations.ElementAt(0). Název operace: OperationName.Add
   * (PatchRequest jako PatchRequest2). Operations.ElementAt(0). Path.AttributePath: "správce"
   * (PatchRequest jako PatchRequest2). Operations.ElementAt(0). Value.Count: 1
   * (PatchRequest jako PatchRequest2). Operations.ElementAt(0). Value.ElementAt(0). Referenční dokumentace: http://.../scim/Users/2819c223-7f76-453a-919d-413861904646
   * (PatchRequest jako PatchRequest2). Operations.ElementAt(0). Value.ElementAt(0). Hodnota: 2819c223-7f76-453a-919d-413861904646

6. Na zrušení zřízení uživatele z identity úložiště přední stěnou služba SCIM, Azure AD, jako odešle žádost: 

   ```
     DELETE ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
     Authorization: Bearer ...
   ```

   Pokud služba je vytvořená pomocí knihovny Common Language Infrastructure pro implementaci SCIM služeb poskytovaných microsoftem, je požadavek přeložit na volání metody Delete poskytovatele služby.   Tato metoda má tento podpis: 

   ```
     // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
     // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
     // is defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 
     System.Threading.Tasks.Task Delete(
       Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier  
         resourceIdentifier, 
       string correlationIdentifier);
   ```

   Objekt zadaný jako hodnota argumentu resourceIdentifier má tyto hodnoty vlastností v příkladu požadavek na zrušení zřízení uživatele: 

   * ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
   * ResourceIdentifier.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

## <a name="group-provisioning-and-de-provisioning"></a>Skupinové zřizování a zrušení zřizování.
Následující ilustrace ukazuje zprávy, že Azure AcD odesílá do služby SCIM ke správě životního cyklu skupiny v jiné úložiště identit.  Tyto zprávy se liší od zprávy týkající se uživatelů třemi způsoby: 

* Schéma skupiny prostředků se identifikuje jako `http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/Group`.  
* Požadavky k načtení skupin stanovit, že atribut členy mají být vyloučeny z libovolného zdroje k dispozici v odpovědi na požadavek.  
* Žádosti slouží k určení, zda má atribut typu odkaz určitou hodnotu se žádostí o členů atributu.  

![][5]
*Obrázek 6: Skupinové zřizování a jeho rušení pořadí*

## <a name="related-articles"></a>Související články
* [Automatizace uživatele zřizování a jeho rušení pro aplikace SaaS](user-provisioning.md)
* [Přizpůsobení mapování atributů pro zřizování uživatelů](customize-application-attributes.md)
* [Zápis výrazů pro mapování atributů](functions-for-customizing-application-data.md)
* [Filtry oborů pro zřizování uživatelů](define-conditional-rules-for-provisioning-user-accounts.md)
* [Oznámení o zřizování účtů](user-provisioning.md)
* [Seznam kurzů o integraci aplikací SaaS](../saas-apps/tutorial-list.md)

<!--Image references-->
[0]: ./media/use-scim-to-provision-users-and-groups/scim-figure-1.png
[1]: ./media/use-scim-to-provision-users-and-groups/scim-figure-2a.png
[2]: ./media/use-scim-to-provision-users-and-groups/scim-figure-2b.png
[3]: ./media/use-scim-to-provision-users-and-groups/scim-figure-3.png
[4]: ./media/use-scim-to-provision-users-and-groups/scim-figure-4.png
[5]: ./media/use-scim-to-provision-users-and-groups/scim-figure-5.png
