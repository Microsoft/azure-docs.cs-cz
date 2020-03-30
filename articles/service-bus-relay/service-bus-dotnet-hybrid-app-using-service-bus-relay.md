---
title: Hybridní místní/cloudová aplikace Azure Windows Communication Foundation (WCF) | Dokumenty společnosti Microsoft
description: Zjistěte, jak vystavit místní službu WCF webové aplikaci v cloudu pomocí Azure Relay
services: service-bus-relay
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: 9ed02f7c-ebfb-4f39-9c97-b7dc15bcb4c1
ms.service: service-bus-relay
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: spelluru
ms.openlocfilehash: b86d535e4cbc275b3ee777d7c70146f7711c502c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "71212957"
---
# <a name="expose-an-on-premises-wcf-service-to-a-web-application-in-the-cloud-by-using-azure-relay"></a>Vystavit místní službu WCF webové aplikaci v cloudu pomocí Azure Relay

Tento článek popisuje, jak vytvořit hybridní cloudovou aplikaci pomocí Microsoft Azure a Visual Studia. Můžete vytvořit aplikaci, která používá více prostředků Azure v cloudu. Tento výukový program vám pomůže naučit se:

* Jak vytvořit nebo přizpůsobit existující webovou službu pro spotřebu webovým řešením.
* Jak používat službu Azure Windows Communication Foundation (WCF) Relay ke sdílení dat mezi aplikací Azure a webovou službou hostovoji jinde.

V tomto kurzu projdete následujícími úkoly:

> [!div class="checklist"]
>
> * Nainstalujte předpoklady pro tento kurz.
> * Zkontrolujte scénář.
> * Vytvořte obor názvů.
> * Vytvořte místní server.
> * Vytvořte aplikaci technologie ASP .NET.
> * Spusťte aplikaci místně.
> * Nasaďte webovou aplikaci do Azure.
> * Spusťte aplikaci v Azure.

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu musí být splněné následující požadavky:

* Předplatné Azure. Pokud ho nemáte, [vytvořte si účet zdarma,](https://azure.microsoft.com/free/) než začnete.
* [Sada Visual Studio 2015 nebo novější](https://www.visualstudio.com). Příklady v tomto kurzu používají Visual Studio 2019.
* Sada Azure SDK pro rozhraní .NET. Nainstalujte jej ze [stránky stažené sady SDK](https://azure.microsoft.com/downloads/).

## <a name="how-azure-relay-helps-with-hybrid-solutions"></a>Jak Azure Relay pomáhá s hybridními řešeními

Obchodní řešení se obvykle skládají z kombinace vlastního kódu a existujících funkcí. Vlastní kód řeší nové a jedinečné obchodní požadavky. Řešení a systémy, které jsou již zavedeny, poskytují stávající funkce.

Architekti řešení začínají používat cloud, protože jim to umožňuje snadněji zvládat nároky na škálování a snížit provozní náklady. Přitom zjistí, že stávající prostředky služby, které by chtěli použít jako stavební kameny pro svá řešení, jsou uvnitř podnikové brány firewall a jsou snadno dosažitelné cloudovým řešením. Mnoho interních služeb není vytvořeno nebo hostováno tak, aby mohly být snadno vystaveny na okraji podnikové sítě.

[Azure Relay](https://azure.microsoft.com/services/service-bus/) přebírá stávající webové služby WCF a umožňuje tyto služby bezpečně přístupné řešení, která jsou mimo obvod podniku bez nutnosti rušivých změn infrastruktury podnikové sítě. Takové přenosové služby se stále hostují uvnitř existujícího prostředí, ale delegují čekání na příchozí spojení a požadavky na přenosovou službu hostovanou v cloudu. Azure Relay taky takové služby chrání před neoprávněným přístupem pomocí ověření [Sdíleným přístupovým podpisem](../service-bus-messaging/service-bus-sas.md) (SAS).

## <a name="review-the-scenario"></a>Projděte si scénář

V tomto kurzu vytvoříte ASP.NET webu, který umožňuje zobrazit seznam produktů na stránce inventáře produktů.

![Scénář][0]

Kurz předpokládá, že máte produktové informace dostupné v existujícím místním systému, a používá Azure Relay k získání přístupu do takového systému. Webová služba, která běží v jednoduché konzolové aplikaci simuluje tuto situaci. Obsahuje sadu produktů v paměti. Tuto konzolovou aplikaci můžete spustit ve vlastním počítači a nasadit webovou roli do Azure. Tímu způsobem uvidíte, jak webová role spuštěná v datovém centru Azure volá do vašeho počítače. K tomuto volání dochází, i když počítač bude téměř jistě za alespoň jednou bránou firewall a vrstvou překladu síťových adres (NAT).

## <a name="set-up-the-development-environment"></a>Nastavení vývojového prostředí

Než začnete s vývojem aplikací pro Azure, stáhněte si nástroje a nastavte si vývojové prostředí:

1. Nainstalujte sadu Azure SDK pro .NET ze [stránky pro stažení SDK](https://azure.microsoft.com/downloads/).
1. Ve sloupci **.NET** zvolte verzi [Visual Studia,](https://www.visualstudio.com) kterou používáte. Tento kurz používá Visual Studio 2019.
1. Po zobrazení výzvy ke spuštění nebo uložení instalačního programu vyberte **spustit**.
1. V dialogovém okně Instalační služba **webové platformy** vyberte **Instalovat** a pokračujte v instalaci.

Po dokončení instalace máte vše potřebné k zahájení vývoje aplikace. Sada SDK obsahuje nástroje, které vám umožní snadno vyvíjet aplikace pro Azure ve Visual Studiu.

## <a name="create-a-namespace"></a>Vytvoření oboru názvů

Prvním krokem je vytvoření oboru názvů a získání klíče [sdíleného přístupového podpisu (SAS).](../service-bus-messaging/service-bus-sas.md) Obor názvů poskytuje hranice aplikace pro každou aplikaci vystavené prostřednictvím služby přenosu. Klíč SAS je automaticky generován systémem při vytvoření oboru názvů služby. Kombinace oboru názvů služby a klíče SAS poskytuje pověření pro Azure k ověření přístupu k aplikaci.

[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="create-an-on-premises-server"></a>Vytvoření lokálního serveru

Nejprve vytvoříte simulovaný místní systém katalogu produktů.  Tento projekt je konzolová aplikace z Visual Studia a pomocí [balíčku NuGet pro Azure Service Bus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) zahrnuje konfiguračních nastavení a knihovny Service Bus. <a name="create-the-project"></a>

1. Spusťte microsoft visual studio jako správce. Chcete-li tak učinit, klepněte pravým tlačítkem myši na ikonu programu sady Visual Studio a vyberte příkaz **Spustit jako správce**.
1. V sadě Visual Studio vyberte **Vytvořit nový projekt**.
1. V **okně Vytvořit nový projekt**vyberte console app **(.NET Framework)** pro C# a vyberte **Další**.
1. Pojmenujte projekt *ProductsServer* a vyberte **vytvořit**.

   ![Konfigurace nového projektu][11]

1. V **Průzkumníku řešení**klepněte pravým tlačítkem myši na projekt **ProductsServer** a vyberte **příkaz Manage NuGet Packages**.
1. Vyberte **Procházet**, pak vyhledejte a zvolte **WindowsAzure.ServiceBus**. Vyberte **Instalovat**a přijměte podmínky použití.

   ![Vybrat balíček NuGet][13]

   Na požadovaná sestavení klientů se nyní odkazuje.

1. Přidejte novou třídu pro váš produktový kontrakt. V **Průzkumníku řešení**klepněte pravým tlačítkem myši na projekt **ProductsServer** a vyberte příkaz **Přidat** > **třídu**.
1. V **části Název**zadejte název *ProductsContract.cs* a vyberte **Přidat**.

Proveďte následující změny kódu vašeho řešení:

1. V *ProductsContract.cs* místo definice oboru názvů zadejte následující kód, který definuje kontrakt služby.

    ```csharp
    namespace ProductsServer
    {
        using System.Collections.Generic;
        using System.Runtime.Serialization;
        using System.ServiceModel;

        // Define the data contract for the service
        [DataContract]
        // Declare the serializable properties.
        public class ProductData
        {
            [DataMember]
            public string Id { get; set; }
            [DataMember]
            public string Name { get; set; }
            [DataMember]
            public string Quantity { get; set; }
        }

        // Define the service contract.
        [ServiceContract]
        interface IProducts
        {
            [OperationContract]
            IList<ProductData> GetProducts();

        }

        interface IProductsChannel : IProducts, IClientChannel
        {
        }
    }
    ```

1. V *Program.cs*nahraďte definici oboru názvů následujícím kódem, který přidá službu profilu a hostitele pro ni.

    ```csharp
    namespace ProductsServer
    {
        using System;
        using System.Linq;
        using System.Collections.Generic;
        using System.ServiceModel;

        // Implement the IProducts interface.
        class ProductsService : IProducts
        {

            // Populate array of products for display on website
            ProductData[] products =
                new []
                    {
                        new ProductData{ Id = "1", Name = "Rock",
                                         Quantity = "1"},
                        new ProductData{ Id = "2", Name = "Paper",
                                         Quantity = "3"},
                        new ProductData{ Id = "3", Name = "Scissors",
                                         Quantity = "5"},
                        new ProductData{ Id = "4", Name = "Well",
                                         Quantity = "2500"},
                    };

            // Display a message in the service console application
            // when the list of products is retrieved.
            public IList<ProductData> GetProducts()
            {
                Console.WriteLine("GetProducts called.");
                return products;
            }

        }

        class Program
        {
            // Define the Main() function in the service application.
            static void Main(string[] args)
            {
                var sh = new ServiceHost(typeof(ProductsService));
                sh.Open();

                Console.WriteLine("Press ENTER to close");
                Console.ReadLine();

                sh.Close();
            }
        }
    }
    ```

1. V **Průzkumníku řešení**poklepejte na **soubor App.config** a otevřete soubor v editoru Sady Visual Studio. V dolní části `<system.ServiceModel>` prvku, ale `<system.ServiceModel>`stále uvnitř , přidejte následující kód XML. Nezapomeňte nahradit `yourServiceNamespace` název oboru názvů a `yourKey` klíčem SAS, který jste načetli dříve z portálu:

    ```xml
    <system.serviceModel>
    ...
      <services>
         <service name="ProductsServer.ProductsService">
           <endpoint address="sb://yourServiceNamespace.servicebus.windows.net/products" binding="netTcpRelayBinding" contract="ProductsServer.IProducts" behaviorConfiguration="products"/>
         </service>
      </services>
      <behaviors>
         <endpointBehaviors>
           <behavior name="products">
             <transportClientEndpointBehavior>
                <tokenProvider>
                   <sharedAccessSignature keyName="RootManageSharedAccessKey" key="yourKey" />
                </tokenProvider>
             </transportClientEndpointBehavior>
           </behavior>
         </endpointBehaviors>
      </behaviors>
    </system.serviceModel>
    ```

    > [!NOTE]
    > Chyba způsobená `transportClientEndpointBehavior` je pouze upozornění a není blokování problém v tomto příkladu.

1. Stále v *Souboru App.config*nahraďte v elementu `<appSettings>` hodnotu připojovacího řetězce připojovacím řetězcem, který jste dříve získali z portálu.

    ```xml
    <appSettings>
       <!-- Service Bus specific app settings for messaging connections -->
       <add key="Microsoft.ServiceBus.ConnectionString"
           value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey"/>
    </appSettings>
    ```

1. Vyberte Ctrl+Shift+B nebo vyberte **Sestavení** > **sestavení řešení** k sestavení aplikace a ověřit přesnost vaší práce tak daleko.

## <a name="create-an-aspnet-application"></a>Vytvoření aplikace ASP.NET

V této části vytvoříte jednoduchou ASP.NET aplikaci, která zobrazuje data načtená z vaší produktové služby.

### <a name="create-the-project"></a>Vytvoření projektu

1. Ujistěte se, že Visual Studio běží jako správce.
1. V sadě Visual Studio vyberte **Vytvořit nový projekt**.
1. V **okně Vytvořit nový projekt**vyberte ASP.NET **webovou aplikaci (.NET Framework)** pro C# a vyberte **Další**.
1. Pojmenujte projekt *ProductsPortal* a vyberte **vytvořit**.
1. V **části Vytvořit novou ASP.NET webovou aplikaci**zvolte **MVC** a v části **Ověřování**vyberte **Změnit** .

   ![Výběr webové aplikace Technologie ASP .NET][16]

1. V **možnosti Změnit ověřování**zvolte Možnost Žádné **ověřování** a pak vyberte **OK**. V tomto kurzu nasazujete aplikaci, která k přihlášení nepotřebuje uživatele.

    ![Určení ověřování][18]

1. Zpět v **možnosti Vytvořit novou ASP.NET webovou aplikaci**vyberte **Vytvořit** a vytvořte aplikaci MVC.
1. Nakonfigurujte prostředky Azure pro novou webovou aplikaci. Postupujte podle pokynů v [části Publikování webové aplikace](../app-service/app-service-web-get-started-dotnet-framework.md#launch-the-publish-wizard). Potom se vraťte k tomuto kurzu a pokračujte dalším krokem.
1. V **Průzkumníku řešení**klepněte pravým tlačítkem myši na **položku Modely** a vyberte příkaz **Přidat** > **třídu**.
1. Pojmenujte třídu *Product.cs*a pak vyberte **Přidat**.

    ![Vytvořit model produktu][17]

### <a name="modify-the-web-application"></a>Úprava webové aplikace

1. V *souboru Product.cs* v sadě Visual Studio nahraďte existující definici oboru názvů následujícím kódem:

   ```csharp
    // Declare properties for the products inventory.
    namespace ProductsWeb.Models
    {
       public class Product
       {
           public string Id { get; set; }
           public string Name { get; set; }
           public string Quantity { get; set; }
       }
    }
    ```

1. V **Průzkumníku řešení**rozbalte **položku Řadiče**a poklepejte na **HomeController.cs** a otevřete soubor v sadě Visual Studio.
1. V *HomeController.cs*nahraďte existující definici oboru názvů následujícím kódem:

    ```csharp
    namespace ProductsWeb.Controllers
    {
        using System.Collections.Generic;
        using System.Web.Mvc;
        using Models;

        public class HomeController : Controller
        {
            // Return a view of the products inventory.
            public ActionResult Index(string Identifier, string ProductName)
            {
                var products = new List<Product>
                    {new Product {Id = Identifier, Name = ProductName}};
                return View(products);
            }
         }
    }
    ```

1. V **Průzkumníku řešení** **rozbalte položku Zobrazení** > **sdílená**a poklikejte na **soubor _Layout.cshtml** a otevřete soubor v editoru sady Visual Studio.
1. Změňte všechny `My ASP.NET Application` výskyty *produktů Northwind Traders*.
1. `Home`Odeberte `About`odkazy `Contact` , a. V následujícím příkladu odstraňte zvýrazněný kód.

    ![Odstranění generovaných položek seznamu][41]

1. V **Průzkumníku řešení** **rozbalte položku Zobrazení** > **domů**a poklikejte na **soubor Index.cshtml** a otevřete soubor v editoru sady Visual Studio. Nahraďte celý obsah souboru následujícím kódem:

   ```html
   @model IEnumerable<ProductsWeb.Models.Product>

   @{
            ViewBag.Title = "Index";
   }

   <h2>Prod Inventory</h2>

   <table>
             <tr>
                 <th>
                     @Html.DisplayNameFor(model => model.Name)
                 </th>
                 <th></th>
                 <th>
                     @Html.DisplayNameFor(model => model.Quantity)
                 </th>
             </tr>

   @foreach (var item in Model) {
             <tr>
                 <td>
                     @Html.DisplayFor(modelItem => item.Name)
                 </td>
                 <td>
                     @Html.DisplayFor(modelItem => item.Quantity)
                 </td>
             </tr>
   }

   </table>
   ```

1. Chcete-li ověřit přesnost vaší práce tak daleko, můžete vybrat Ctrl + Shift + B k sestavení projektu.

### <a name="run-the-app-locally"></a>Místní spuštění aplikace

Spusťte aplikace pro kontrolu, že funguje.

1. Zkontrolujte, že aktivní projekt je **ProductsPortal**. Klepněte pravým tlačítkem myši na název projektu v **Průzkumníku řešení** a vyberte **nastavit jako spouštěcí projekt**.
1. V sadě Visual Studio vyberte F5.

Měla by se objevit vaše aplikace, jak běží v prohlížeči.

![Webová aplikace][21]

## <a name="put-the-pieces-together"></a>Složení částí do jednoho celku

Dalším krokem je spojit lokální produktový server s aplikací ASP.NET.

1. Pokud ještě není otevřená, otevřete v sadě Visual Studio projekt **ProductsPortal,** který jste vytvořili v části [Vytvořit ASP.NET aplikace.](#create-an-aspnet-application)
1. Podobně jako krok v části [Vytvořit místní server,](#create-an-on-premises-server) přidejte balíček NuGet do odkazů na projekt. V **Průzkumníku řešení**klepněte pravým tlačítkem myši na projekt **ProductsPortal** a pak vyberte **příkaz Manage NuGet Packages**.
1. Vyhledejte *WindowsAzure.ServiceBus* a vyberte položku **WindowsAzure.ServiceBus**. Poté dokončete instalaci a zavřete toto dialogové okno.
1. V **Průzkumníku řešení**klepněte pravým tlačítkem myši na projekt **ProductsPortal** a vyberte **příkaz Přidat** > **existující položku**.
1. Přejděte na soubor *ProductsContract.cs* v konzolovém projektu **ProductsServer**. Zvýrazněte *ProductsContract.cs*. Vyberte šipku dolů vedle **položky Přidat**a pak zvolte **Přidat jako odkaz**.

   ![Přidat jako odkaz][24]

1. Teď ve Visual Studiu otevřete soubor *HomeController.cs* a nahraďte definici oboru názvů následujícím kódem. Nezapomeňte nahradit `yourServiceNamespace` názvem oboru názvů služby a `yourKey` klíčem SAS. Tento kód umožňuje klientovi volat místní služby, vrácení výsledku volání.

   ```csharp
   namespace ProductsWeb.Controllers
   {
       using System.Linq;
       using System.ServiceModel;
       using System.Web.Mvc;
       using Microsoft.ServiceBus;
       using Models;
       using ProductsServer;

       public class HomeController : Controller
       {
           // Declare the channel factory.
           static ChannelFactory<IProductsChannel> channelFactory;

           static HomeController()
           {
               // Create shared access signature token credentials for authentication.
               channelFactory = new ChannelFactory<IProductsChannel>(new NetTcpRelayBinding(),
                   "sb://yourServiceNamespace.servicebus.windows.net/products");
               channelFactory.Endpoint.Behaviors.Add(new TransportClientEndpointBehavior {
                   TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(
                       "RootManageSharedAccessKey", "yourKey") });
           }

           public ActionResult Index()
           {
               using (IProductsChannel channel = channelFactory.CreateChannel())
               {
                   // Return a view of the products inventory.
                   return this.View(from prod in channel.GetProducts()
                                    select
                                        new Product { Id = prod.Id, Name = prod.Name,
                                            Quantity = prod.Quantity });
               }
           }
       }
   }
   ```

1. V **Průzkumníku řešení**klepněte pravým tlačítkem myši na řešení **ProductsPortal.** Ujistěte se, že klikněte pravým tlačítkem myši na řešení, nikoli na projekt. Vyberte **Přidat** > **existující projekt**.
1. Přejděte do projektu **ProductsServer**, pak poklikejte na řešení *ProductsServer.csproj* a tím ho přidejte.
1. **ProductsServer** musí být spuštěn a zobrazit data na **ProductsPortal**. V **Průzkumníku řešení**klepněte pravým tlačítkem myši na řešení **ProductsPortal** a vyberte **příkaz Vlastnosti,** chcete-li zobrazit stránky **vlastností**.
1. Vyberte**Projekt spuštění s** **běžnými vlastnostmi** > a zvolte **Více projektů po spuštění**. Ujistěte se, že **ProductsServer** a **ProductsPortal** se zobrazí v tomto pořadí a že **akce** pro oba je **Start**.

      ![Více projektů při spuštění][25]

1. Vyberte **společné vlastnosti** > **závislostí projektu** na levé straně.
1. V **případě projektů**zvolte **ProductsPortal**. Zkontrolujte, že je **ProductsServer** vybraný.

    ![Závislosti projektu][26]

1. V **případě projektů**zvolte **ProductsServer**. Ujistěte se, že **ProductsPortal** není vybrána, a pak vyberte **OK** pro uložení změn.

## <a name="run-the-project-locally"></a>Spusťte projekt lokálně.

Chcete-li aplikaci otestovat místně, vyberte v sadě Visual Studio možnost F5. Místní server **ProductsServer**, by měl spustit jako první, pak **ProductsPortal** aplikace by měla spustit v okně prohlížeče. Tentokrát uvidíte, že zásoby produktů obsahují data načtená z místního systému služby produktu.

![Webová aplikace][10]

Na stránce **ProductsPortal** vyberte **Aktualizovat.** Při každé aktualizaci stránky se zobrazí aplikace serveru, která zobrazí zprávu, když `GetProducts()` je volána z **ProductsServer.**

Před pokračováním v další části zavřete obě žádosti.

## <a name="deploy-the-productsportal-project-to-an-azure-web-app"></a>Nasazení projektu ProductsPortal do webové aplikace Azure

Dalším krokem je opětovné publikování front-endu Azure Web App **ProductsPortal:**

1. V **Průzkumníku řešení**klepněte pravým tlačítkem myši na projekt **ProductsPortal** a vyberte **publikovat**. Na stránce **Publikovat** vyberte **Publikovat**.

   > [!NOTE]
   > V okně prohlížeče se při automatickém spuštění webového projektu **ProductsPortal** po nasazení může objevit chybová zpráva. To je v pořádku a děje se to, protože aplikace **ProductsServer** ještě neběží.
   >

1. Zkopírujte adresu URL nasazené webové aplikace. Adresu URL budete potřebovat později. Tuto adresu URL můžete získat také z okna **Aktivita služby App Azure** v sadě Visual Studio:

   ![Adresa URL nasazené aplikace][9]

1. Zavřením okna prohlížeče zastavte spuštěnou aplikaci.

<a name="set-productsportal-as-web-app"></a>Před spuštěním aplikace v cloudu, musíte zajistit, že **ProductsPortal** se spustí z v rámci Sady Visual Studio jako webové aplikace.

1. V sadě Visual Studio klepněte pravým tlačítkem myši na projekt **ProductsPortal** a vyberte **příkaz Vlastnosti**.
1. Vyberte **možnost Web**. V části **Spustit akci**zvolte **Start URL**. V tomto příkladu zadejte adresu URL dříve `https://productsportal20190906122808.azurewebsites.net/`nasazené webové aplikace .

    ![Počáteční adresa URL][27]

1. Vyberte **Soubor** > **Uložit vše**.
1. Vyberte **řešení** > **opětovného sestavení**.

## <a name="run-the-application"></a>Spuštění aplikace

Chcete-li vytvořit a spustit aplikaci, vyberte možnost F5. Místní server, což je **konzolová aplikace ProductsServer,** by měl být nejprve zahájen, pak by měla být aplikace **ProductsPortal** zahájena v okně prohlížeče, jak je znázorněno zde:

   ![Spuštění webové aplikace v Azure][1]

Inventář produktů obsahuje data načtená z místního systému služby produktu a tato data se zobrazí ve webové aplikaci. Zkontrolujte adresu URL a ujistěte se, že **ProductsPortal** běží v cloudu jako webová aplikace Azure.

   > [!IMPORTANT]
   > Konzolová aplikace **ProductsServer** musí běžet a musí být schopná odesílat data do aplikace **ProductsPortal**. Pokud prohlížeč zobrazí chybu, počkejte několik sekund, než **productsserver** načte a zobrazí následující zprávu, a pak prohlížeč aktualizujte.
   >

V prohlížeči aktualizujte stránku **ProductsPortal.** Při každé aktualizaci stránky se zobrazí aplikace serveru, která zobrazí zprávu, když `GetProducts()` je volána z **ProductsServer.**

![Aktualizovaný výstup][38]

## <a name="next-steps"></a>Další kroky

Přejděte k následujícímu kurzu:

> [!div class="nextstepaction"]
>[Zveřejnění místní služby WCF pro klienta WCF mimo vaši síť](service-bus-relay-tutorial.md)

[0]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hybrid.png
[1]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/run-web-app.png
[NuGet]: https://nuget.org

[11]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/configure-productsserver.png
[13]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/install-nuget-service-bus-productsserver.png
[15]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-2.png
[16]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/choose-web-application-template.png
[17]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/add-class-productsportal.png
[18]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/change-authentication.png
[9]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/web-publish-activity.png
[10]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/run-web-app-locally.png

[21]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/run-web-app-locally-no-content.png
[24]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/add-existing-item-link.png
[25]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-13.png
[26]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-14.png
[27]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/launch-app-as-web-app.png

[36]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App2.png
[37]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-service1.png
[38]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-service2.png
[41]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-40.png
[43]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-43.png
