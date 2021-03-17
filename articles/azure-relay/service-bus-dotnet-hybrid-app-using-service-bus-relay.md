---
title: Hybridní místní/cloudová aplikace (.NET) přenosu Azure Windows Communication Foundation (WCF) | Microsoft Docs
description: Přečtěte si, jak vystavit místní službu WCF pro webovou aplikaci v cloudu pomocí Azure Relay
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 06/23/2020
ms.openlocfilehash: 22b582634b623b39545eca225c8df0130606c2bb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90983885"
---
# <a name="expose-an-on-premises-wcf-service-to-a-web-application-in-the-cloud-by-using-azure-relay"></a>Vystavení místní služby WCF pro webovou aplikaci v cloudu pomocí Azure Relay

Tento článek popisuje, jak vytvořit hybridní cloudovou aplikaci pomocí Microsoft Azure a Visual Studia. Vytvoříte aplikaci, která používá několik prostředků Azure v cloudu. Tento kurz vám pomůže s těmito postupy:

* Jak vytvořit nebo přizpůsobit existující webovou službu pro spotřebu webovým řešením.
* Jak používat předávací službu Azure Windows Communication Foundation (WCF) ke sdílení dat mezi aplikací Azure a webovou službou hostovanou jinde.

V tomto kurzu provedete následující úlohy:

> [!div class="checklist"]
>
> * Nainstalovat požadavky pro tento kurz.
> * Projděte si scénář.
> * Vytvořte obor názvů.
> * Vytvořte místní server.
> * Vytvořte aplikaci ASP .NET.
> * Spusťte aplikaci místně.
> * Nasaďte webovou aplikaci do Azure.
> * Spusťte aplikaci v Azure.

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu musí být splněné následující požadavky:

* Předplatné Azure. Pokud ho ještě nemáte, [Vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
* [Visual Studio 2015 nebo novější](https://www.visualstudio.com). V příkladech v tomto kurzu se používá Visual Studio 2019.
* Sada Azure SDK pro .NET Nainstalujte ji ze [stránky pro stažení sady SDK](https://azure.microsoft.com/downloads/).

## <a name="how-azure-relay-helps-with-hybrid-solutions"></a>Jak Azure Relay pomáhá s hybridními řešeními

Obchodní řešení se obvykle skládají z kombinace vlastního kódu a existujících funkcí. Vlastní kód řešit nové a jedinečné obchodní požadavky. Řešení a systémy, které jsou již na místě, poskytují existující funkce.

Architekti řešení začínají používat cloud, protože jim to umožňuje snadněji zvládat nároky na škálování a snížit provozní náklady. Tím zjistíte, že existující assety služeb, které by chtěli použít jako stavební bloky pro svá řešení, jsou uvnitř firemní brány firewall a z cloudového řešení se nesnadno dosahují. Řada interních služeb není sestavená ani hostovaná způsobem, který by se mohl snadno vystavit na hraniční síti společnosti.

[Azure Relay](https://azure.microsoft.com/services/service-bus/) přebírá existující webové služby WCF a zpřístupňuje tyto služby pro řešení, která jsou mimo firemní obvodu, aniž by to vyžadovalo rušivé změny v infrastruktuře podnikové sítě. Takové přenosové služby se stále hostují uvnitř existujícího prostředí, ale delegují čekání na příchozí spojení a požadavky na přenosovou službu hostovanou v cloudu. Azure Relay taky takové služby chrání před neoprávněným přístupem pomocí ověření [Sdíleným přístupovým podpisem](../service-bus-messaging/service-bus-sas.md) (SAS).

## <a name="review-the-scenario"></a>Kontrola scénáře

V tomto kurzu vytvoříte web ASP.NET, který vám umožní zobrazit seznam produktů na stránce inventáře produktů.

![Scénář][0]

Kurz předpokládá, že máte produktové informace dostupné v existujícím místním systému, a používá Azure Relay k získání přístupu do takového systému. Webová služba, která běží v jednoduché konzolové aplikaci, simuluje tuto situaci. Obsahuje sadu produktů v paměti. Tuto konzolovou aplikaci můžete spustit na vlastním počítači a nasadit webovou roli do Azure. Tím zjistíte, jak webová role běžící v datacentru Azure volá do vašeho počítače. K tomuto volání dochází i v případě, že se Váš počítač bude za téměř určitě nacházet alespoň s jednou bránou firewall a vrstvou překladu adres (NAT).

## <a name="set-up-the-development-environment"></a>Nastavení vývojového prostředí

Než začnete s vývojem aplikací pro Azure, stáhněte si nástroje a nastavte si vývojové prostředí:

1. Nainstalujte sadu Azure SDK pro .NET ze [stránky pro stažení SDK](https://azure.microsoft.com/downloads/).
1. Ve sloupci **.NET** vyberte verzi sady [Visual Studio](https://www.visualstudio.com) , kterou používáte. V tomto kurzu se používá Visual Studio 2019.
1. Po zobrazení výzvy ke spuštění nebo uložení instalačního programu vyberte **Spustit**.
1. V dialogovém okně **instalační program webové platformy** vyberte **nainstalovat** a pokračujte v instalaci.

Až se instalace dokončí, máte vše potřebné ke spuštění vývoje aplikace. Sada SDK obsahuje nástroje, které vám umožní snadno vyvíjet aplikace pro Azure ve Visual Studiu.

## <a name="create-a-namespace"></a>Vytvoření oboru názvů

Prvním krokem je vytvoření oboru názvů a získání klíče [sdíleného přístupového podpisu (SAS)](../service-bus-messaging/service-bus-sas.md) . Obor názvů poskytuje hranici aplikace pro každou aplikaci vystavenou prostřednictvím předávací služby. Klíč SAS se automaticky generuje systémem při vytvoření oboru názvů služby. Kombinace oboru názvů služby a klíče SAS poskytuje přihlašovací údaje pro Azure, které ověřují přístup k aplikaci.

[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="create-an-on-premises-server"></a>Vytvoření lokálního serveru

Nejprve sestavíte simulovaný místní systém katalogu produktů.  Tento projekt je konzolová aplikace z Visual Studia a pomocí [balíčku NuGet pro Azure Service Bus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) zahrnuje konfiguračních nastavení a knihovny Service Bus. <a name="create-the-project"></a>

1. Spusťte Microsoft Visual Studio jako správce. Provedete to tak, že kliknete pravým tlačítkem na ikonu programu Visual Studio a vyberete **Spustit jako správce**.
1. V aplikaci Visual Studio vyberte možnost **vytvořit nový projekt**.
1. V v poli **vytvořit nový projekt**vyberte **Konzolová aplikace (.NET Framework)** pro C# a vyberte **Další**.
1. Pojmenujte projekt *ProductsServer* a vyberte **vytvořit**.

   ![Konfigurovat nový projekt][11]

1. V **Průzkumník řešení**klikněte pravým tlačítkem na projekt **ProductsServer** a pak vyberte **Spravovat balíčky NuGet**.
1. Vyberte **Procházet**, vyhledejte a vyberte **windowsazure. ServiceBus**. Vyberte **nainstalovat**a přijměte podmínky použití.

   ![Vybrat balíček NuGet][13]

   Odkazy na požadovaná sestavení klienta jsou nyní odkazovány.

1. Přidejte novou třídu pro váš produktový kontrakt. V **Průzkumník řešení**klikněte pravým tlačítkem myši na projekt **ProductsServer** a vyberte **Přidat**  >  **třídu**.
1. Do pole **název**zadejte název *ProductsContract.cs* a vyberte **Přidat**.

Proveďte následující změny kódu v řešení:

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

1. V *program.cs*Nahraďte definici oboru názvů následujícím kódem, který přidá profilovou službu a hostitele pro něj.

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

1. V **Průzkumník řešení**dvakrát klikněte na **App.config** a otevřete soubor v editoru sady Visual Studio. V dolní části `<system.ServiceModel>` prvku, ale stále v `<system.ServiceModel>` , přidejte následující kód XML. Nezapomeňte nahradit `yourServiceNamespace` názvem vašeho oboru názvů a `yourKey` klíč SAS, který jste dříve získali z portálu:

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
    > Chyba způsobila, že `transportClientEndpointBehavior` je pouze upozornění a není blokujícím problémem pro tento příklad.

1. Stále v *App.config*v `<appSettings>` elementu nahraďte hodnotu připojovacího řetězce připojovacím řetězcem, který jste dříve získali z portálu.

    ```xml
    <appSettings>
       <!-- Service Bus specific app settings for messaging connections -->
       <add key="Microsoft.ServiceBus.ConnectionString"
           value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey"/>
    </appSettings>
    ```

1. Vyberte CTRL + SHIFT + B nebo vyberte **sestavit**  >  **sestavení řešení** a sestavte aplikaci a ověřte přesnost své dosavadní práce.

## <a name="create-an-aspnet-application"></a>Vytvoření aplikace ASP.NET

V této části vytvoříte jednoduchou aplikaci ASP.NET, která zobrazuje data získaná z vaší produktové služby.

### <a name="create-the-project"></a>Vytvoření projektu

1. Ujistěte se, že je aplikace Visual Studio spuštěna jako správce.
1. V aplikaci Visual Studio vyberte možnost **vytvořit nový projekt**.
1. V poli **vytvořit nový projekt**vyberte **ASP.NET webová aplikace (.NET Framework)** pro C# a vyberte **Další**.
1. Pojmenujte projekt *ProductsPortal* a vyberte **vytvořit**.
1. V části **vytvořit novou webovou aplikaci v ASP.NET**zvolte **MVC** a v části **ověřování**vyberte **změnit** .

   ![Výběr webové aplikace ASP .NET][16]

1. V možnosti **změnit ověřování**zvolte možnost **bez ověřování** a pak vyberte **OK**. Pro tento kurz nasazujete aplikaci, která nevyžaduje, aby se uživatel přihlásil.

    ![Zadat ověřování][18]

1. Zpátky v části **vytvořit novou webovou aplikaci v ASP.NET**vyberte **vytvořit** a vytvořte aplikaci MVC.
1. Konfigurace prostředků Azure pro novou webovou aplikaci Postupujte podle kroků v části [publikování webové aplikace](../app-service/quickstart-dotnet-framework.md#launch-the-publish-wizard). Pak se vraťte k tomuto kurzu a pokračujte k dalšímu kroku.
1. V **Průzkumník řešení**klikněte pravým tlačítkem na **modely** a pak vyberte **Přidat**  >  **třídu**.
1. Pojmenujte třídu *Product.cs*a pak vyberte **Přidat**.

    ![Vytvoření modelu produktu][17]

### <a name="modify-the-web-application"></a>Úprava webové aplikace

1. V souboru *Product.cs* v aplikaci Visual Studio nahraďte existující definici oboru názvů následujícím kódem:

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

1. V **Průzkumník řešení**rozbalte položku **řadiče**a potom poklikejte na **HomeController.cs** a otevřete soubor v aplikaci Visual Studio.
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

1. V **Průzkumník řešení**rozbalte **zobrazení**  >  **Shared**a potom poklikejte na **_Layout. cshtml** pro otevření souboru v editoru sady Visual Studio.
1. Změňte všechny výskyty `My ASP.NET Application` na *Northwind Traders Products*.
1. Odeberte `Home` odkazy, `About` a `Contact` . V následujícím příkladu odstraňte zvýrazněný kód.

    ![Odstranit vygenerované položky seznamu][41]

1. V **Průzkumník řešení**rozbalte položku **zobrazení**  >  **Domů**a potom poklikejte na **index. cshtml** . tím otevřete soubor v editoru sady Visual Studio. Celý obsah souboru nahraďte následujícím kódem:

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

1. Chcete-li ověřit přesnost své dosavadní práce, můžete vybrat CTRL + SHIFT + B a sestavit projekt.

### <a name="run-the-app-locally"></a>Místní spuštění aplikace

Spusťte aplikace pro kontrolu, že funguje.

1. Zkontrolujte, že aktivní projekt je **ProductsPortal**. Klikněte pravým tlačítkem myši na název projektu v **Průzkumník řešení** a vyberte **nastavit jako spouštěný projekt**.
1. V aplikaci Visual Studio vyberte F5.

Měla by se objevit vaše aplikace, jak běží v prohlížeči.

![Snímek obrazovky ukazuje příklad aplikace spuštěné v prohlížeči se zvýrazněnou adresou URL.][21]

## <a name="put-the-pieces-together"></a>Složení částí do jednoho celku

Dalším krokem je spojit lokální produktový server s aplikací ASP.NET.

1. Pokud ještě není otevřený, v aplikaci Visual Studio otevřete projekt **ProductsPortal** , který jste vytvořili v části [Vytvoření aplikace ASP.NET](#create-an-aspnet-application) .
1. Podobně jako v části [Vytvoření místního serveru](#create-an-on-premises-server) přidejte do odkazů projektu balíček NuGet. V **Průzkumník řešení**klikněte pravým tlačítkem na projekt **ProductsPortal** a pak vyberte **Spravovat balíčky NuGet**.
1. Vyhledejte *WindowsAzure.ServiceBus* a vyberte položku **WindowsAzure.ServiceBus**. Potom dokončete instalaci a zavřete toto dialogové okno.
1. V **Průzkumník řešení**klikněte pravým tlačítkem na projekt **ProductsPortal** a pak vyberte **Přidat**  >  **existující položku**.
1. Přejděte na soubor *ProductsContract.cs* v konzolovém projektu **ProductsServer**. Zvýrazněte *ProductsContract.cs*. Vyberte šipku dolů vedle **Přidat**a pak zvolte **Přidat jako odkaz**.

   ![Přidat jako odkaz][24]

1. Teď ve Visual Studiu otevřete soubor *HomeController.cs* a nahraďte definici oboru názvů následujícím kódem. Nezapomeňte nahradit `yourServiceNamespace` názvem oboru názvů služby a `yourKey` vaším klíčem SAS. Tento kód umožňuje klientovi zavolat místní službu a vrátit výsledek volání.

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

1. V **Průzkumník řešení**klikněte pravým tlačítkem na řešení **ProductsPortal** . Ujistěte se, že kliknete pravým tlačítkem na řešení, nikoli na projekt. Vyberte **Přidat**  >  **existující projekt**.
1. Přejděte do projektu **ProductsServer**, pak poklikejte na řešení *ProductsServer.csproj* a tím ho přidejte.
1. **ProductsServer** musí běžet, aby se zobrazila data v **ProductsPortal**. V **Průzkumník řešení**klikněte pravým tlačítkem na řešení **ProductsPortal** a vyberte **vlastnosti** . zobrazí se **stránky vlastností**.
1. Vyberte **společné vlastnosti**  >  **spouštěný projekt** a zvolte **více projektů po spuštění**. Zajistěte, aby se **ProductsServer** a **ProductsPortal** zobrazovaly v tomto pořadí a aby se **spustila** **Akce** pro obě.

      ![Více projektů po spuštění][25]

1. Na levé straně vyberte **společné vlastnosti**  >  **závislosti projektu** .
1. V případě **projektů**vyberte možnost **ProductsPortal**. Zkontrolujte, že je **ProductsServer** vybraný.

    ![Závislosti projektu][26]

1. V případě **projektů**vyberte možnost **ProductsServer**. Ujistěte se, že není vybraná možnost **ProductsPortal** , a pak kliknutím na **tlačítko OK** uložte změny.

## <a name="run-the-project-locally"></a>Spusťte projekt lokálně.

Chcete-li aplikaci otestovat místně, v aplikaci Visual Studio vyberte F5. Jako první se musí spustit místní server **ProductsServer**a pak by se měla aplikace **ProductsPortal** spustit v okně prohlížeče. Tentokrát vidíte, že inventář produktů obsahuje data získaná z místního systému služby produkt.

![Webová aplikace][10]

Na stránce **ProductsPortal** vyberte **aktualizovat** . Při každé aktualizaci stránky se zobrazí aplikace serveru při `GetProducts()` volání z **ProductsServer** zobrazí zpráva.

Než budete pokračovat k další části, zavřete obě aplikace.

## <a name="deploy-the-productsportal-project-to-an-azure-web-app"></a>Nasazení projektu ProductsPortal do webové aplikace Azure

Dalším krokem je opětovné publikování webu Azure Web App **ProductsPortal** front-end:

1. V **Průzkumník řešení**klikněte pravým tlačítkem myši na projekt **ProductsPortal** a vyberte **publikovat**. Na stránce **Publikovat** vyberte **Publikovat**.

   > [!NOTE]
   > V okně prohlížeče se při automatickém spuštění webového projektu **ProductsPortal** po nasazení může objevit chybová zpráva. To je v pořádku a děje se to, protože aplikace **ProductsServer** ještě neběží.
   >

1. Zkopírujte adresu URL nasazené webové aplikace. Tuto adresu URL budete potřebovat později. Tuto adresu URL můžete získat také z okna **Azure App Service aktivity** v aplikaci Visual Studio:

   ![Adresa URL nasazené aplikace][9]

1. Zavřením okna prohlížeče zastavte spuštěnou aplikaci.

<a name="set-productsportal-as-web-app"></a>Před spuštěním aplikace v cloudu je nutné zajistit, aby se **ProductsPortal** spustil v rámci sady Visual Studio jako webová aplikace.

1. V aplikaci Visual Studio klikněte pravým tlačítkem na projekt **ProductsPortal** a vyberte **vlastnosti**.
1. Vyberte **Web**. V části **Spustit akci**vyberte možnost **Počáteční adresa URL**. V tomto příkladu zadejte adresu URL pro dříve nasazenou webovou aplikaci `https://productsportal20190906122808.azurewebsites.net/` .

    ![Počáteční adresa URL][27]

1. Vyberte **soubor**  >  **Uložit vše**.
1. Vyberte **Build**  >  **řešení**pro opětovné sestavení sestavení.

## <a name="run-the-application"></a>Spuštění aplikace

Vyberte F5 a sestavte a spusťte aplikaci. Místní server, který je Konzolová aplikace **ProductsServer** , by měl nejprve začít a aplikace **ProductsPortal** by se měla spustit v okně prohlížeče, jak je znázorněno zde:

   ![Spuštění webové aplikace v Azure][1]

Inventář produktů obsahuje data získaná z místního systému služby produkt a tato data zobrazuje ve webové aplikaci. Zkontrolujte adresu URL a ujistěte se, že **ProductsPortal** běží v cloudu jako webová aplikace Azure.

   > [!IMPORTANT]
   > Konzolová aplikace **ProductsServer** musí běžet a musí být schopná odesílat data do aplikace **ProductsPortal**. Pokud se v prohlížeči zobrazí chyba, počkejte několik sekund, než se **ProductsServer** načte, a zobrazí se následující zpráva. potom aktualizujte prohlížeč.
   >

V prohlížeči aktualizujte stránku **ProductsPortal** . Při každé aktualizaci stránky se zobrazí aplikace serveru při `GetProducts()` volání z **ProductsServer** zobrazí zpráva.

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
