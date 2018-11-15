---
title: Hybridní místní/cloudová aplikace (.NET) Azure WCF Relay | Dokumentace Microsoftu
description: Zjistěte, jak zpřístupňují služby WCF v místním do webové aplikace v cloudu s využitím Azure Relay
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
ms.topic: hero-article
ms.date: 11/01/2018
ms.author: spelluru
ms.openlocfilehash: 2972d04d1617b755bb6c2ff60d9922accdd09f2a
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2018
ms.locfileid: "51614833"
---
# <a name="expose-an-on-premises-wcf-service-to-a-web-application-in-the-cloud-by-using-azure-relay"></a>Zpřístupňují služby WCF v místním do webové aplikace v cloudu s využitím Azure Relay 
Tento článek popisuje, jak vytvořit hybridní cloudovou aplikaci pomocí Microsoft Azure a Visual Studia. Vytvoříte aplikaci, která používá několik prostředků Azure nahoru a běží v cloudu.

* Jak vytvořit nebo přizpůsobit existující webovou službu pro spotřebu webovým řešením.
* Jak používat službu Azure WCF Relay ke sdílení dat mezi aplikací Azure a webovou službou hostovanou jinde.

V tomto kurzu provedete následující kroky:

> [!div class="checklist"]
> * Přečtěte si tento scénář.
> * Vytvoření oboru názvů.
> * Vytvoření lokálního serveru
> * Vytvoření aplikace ASP .NET
> * Spusťte aplikaci místně.
> * Nasazení webové aplikace do Azure
> * Spusťte aplikaci v Azure

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu musí být splněné následující požadavky:

- Předplatné Azure. Pokud ho nemáte, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
- [Sada Visual Studio 2015 nebo novější](http://www.visualstudio.com). V příkladech v tomto kurzu se používá sada Visual Studio 2017.
- Azure SDK pro .NET. Nainstalujte ji z [stránky pro stažení sady SDK](https://azure.microsoft.com/downloads/).

## <a name="how-azure-relay-helps-with-hybrid-solutions"></a>Jak Azure Relay pomáhá s hybridními řešeními
Podniková řešení se obvykle skládají z kombinace vlastního kódu napsaného pro řešení nových a jedinečných podnikových řešení a stávajících funkcí poskytovaných řešeními a systémy, které již existují.

Architekti řešení začínají používat cloud, protože jim to umožňuje snadněji zvládat nároky na škálování a snížit provozní náklady. Přitom zjišťují, že existující prostředky služeb, které by chtěli využívat jako stavební prvky pro svá řešení, jsou za firemním firewallem a cloudové řešení k nim nemá snadný přístup. Spousta interních služeb není postavená nebo hostovaná tak, aby se dala snadno vystavit na rozhraní firemní sítě.

[Azure Relay](https://azure.microsoft.com/services/service-bus/) je navržené pro situace, kdy je potřeba vzít existující webové služby WCF (Windows Communication Foundation) a bezpečně je zpřístupnit pro řešení, která jsou mimo firemní zónu, a to bez nutnosti provádět nežádoucí změny infrastruktury podnikové sítě. Takové přenosové služby se stále hostují uvnitř existujícího prostředí, ale delegují čekání na příchozí spojení a požadavky na přenosovou službu hostovanou v cloudu. Azure Relay taky takové služby chrání před neoprávněným přístupem pomocí ověření [Sdíleným přístupovým podpisem](../service-bus-messaging/service-bus-sas.md) (SAS).

## <a name="review-the-scenario"></a>Revize scénáře
V tomto kurzu vytvoříte web ASP.NET, která umožňuje zobrazit seznam produktů na stránce inventáře produktů.

![Scénář][0]

Kurz předpokládá, že máte produktové informace dostupné v existujícím místním systému, a používá Azure Relay k získání přístupu do takového systému. To se simuluje jako webová služba, která spustí jednoduchou konzolovou aplikaci a využívá sadu produktů uloženou v paměti. Taky si vyzkoušíte spuštění této konzolové aplikace na svém vlastním počítači a nasazení webové role do Azure. Uvidíte tak, jak webová role běžící v datovém centru Azure skutečně zavolá do vašeho počítače, i když se váš počítač skoro určitě nachází za nejméně jedním firewallem a vrstvou překládání adres (NAT).

## <a name="set-up-the-development-environment"></a>Nastavení vývojového prostředí

Než začnete s vývojem aplikací pro Azure, stáhněte si nástroje a nastavte si vývojové prostředí:

1. Nainstalujte sadu Azure SDK pro .NET ze [stránky pro stažení SDK](https://azure.microsoft.com/downloads/).
2. Ve sloupci **.NET** klikněte na verzi sady [Visual Studio](https://www.visualstudio.com), kterou používáte. V krocích v tomto kurzu se používá sada Visual Studio 2017.
3. Když se zobrazí dialog pro spuštění nebo uložení instalačního programu, klikněte na **Spustit**.
4. V **Instalačním programu webové platformy** klikněte na **Instalovat** a pokračujte v instalaci.
5. Po dokončení instalace budete mít všechno, co je potřeba k vývoji aplikace. Sada SDK obsahuje nástroje, které vám umožní snadno vyvíjet aplikace pro Azure ve Visual Studiu.

## <a name="create-a-namespace"></a>Vytvoření oboru názvů
Prvním krokem je vytvoření oboru názvů a získat [sdíleného přístupového podpisu (SAS)](../service-bus-messaging/service-bus-sas.md) klíč. Obor názvů aplikaci poskytuje hranice pro každou aplikaci vystavenou přes službu relay. Systém automaticky vygeneruje SAS klíč při vytvoření oboru názvů služby. Kombinace oboru názvů služby a klíče SAS poskytuje přihlašovací údaje pro Azure k ověření přístupu k aplikaci.

[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="create-an-on-premises-server"></a>Vytvoření lokálního serveru
Nejdříve vytvořte systém katalogu produktů simulované místní.  Tento projekt je konzolová aplikace z Visual Studia a pomocí [balíčku NuGet pro Azure Service Bus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) zahrnuje konfiguračních nastavení a knihovny Service Bus.

### <a name="create-the-project"></a>Vytvoření projektu
1. Spusťte Visual Studio s právy správce. Pokud tak chcete učinit, klikněte pravým tlačítkem na ikonu programu Visual Studio a potom klikněte na **Spustit jako správce**.
2. Ve Visual Studiu v nabídce **Soubor** klikněte na **Nový** a pak na **Projekt**.
3. V **Nainstalovaných šablonách** v části **Visual C#** klikněte na **Konzolová aplikace (.NET Framework)**. Do pole **Název** zadejte název **ProductsServer**:

   ![Dialogové okno Nový projekt][11]
4. Kliknutím na tlačítko **OK** vytvořte projekt **ProductsServer**.
5. Pokud jste už nainstalovali správce balíčků NuGet pro Visual Studio, přejděte na další krok. Pokud ne, přejděte na [NuGet][NuGet] a klikněte na [Nainstalovat NuGet](http://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c). Postupujte podle zobrazených výzev nainstalujte Správce balíčků NuGet a potom restartujte Visual Studio.
6. V Průzkumníku řešení klikněte pravým tlačítkem na projekt **ProductsServer**, pak klikněte na **Správa balíčků NuGet**.
7. Klikněte na kartu **Procházet** a pak vyhledejte **WindowsAzure.ServiceBus**. Vyberte balíček **WindowsAzure.ServiceBus**.
8. Klikněte na **Instalovat** a přijměte podmínky použití.

   ![Vyberte balíček NuGet][13]

   Jsou nyní odkazovaná sestavení klienta.
8. Přidejte novou třídu pro váš produktový kontrakt. V Průzkumníku řešení klikněte pravým tlačítkem na projekt **ProductsServer**, pak klikněte na **Přidat** a pak na **Třída**.
9. Do pole **Název** zadejte název **ProductsContract.cs**. Pak klikněte na **Přidat**.
10. V **ProductsContract.cs** místo definice oboru názvů zadejte následující kód, který definuje kontrakt služby.

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
11. V Program.cs místo definice oboru názvů zadejte následující kód, který přidá službu profilu a hosta pro ni.

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
12. V Průzkumníku řešení poklikejte na soubor **App.config**.cs a otevře se v editoru Visual Studio. V dolní části `<system.ServiceModel>` – element (ale pořád ještě uvnitř `<system.ServiceModel>`), přidejte následující kód XML: Nezapomeňte nahradit *yourServiceNamespace* s názvem vašeho oboru názvů a *yourKey* SAS klíč, který jste získali dříve z portálu:

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
    Chyba způsobená chováním transportClientEndpointBehavior je pouhým upozorněním a nepředstavuje problém, který by blokoval tuto ukázku.
    
13. Ještě v souboru App.config v elementu `<appSettings>` nahraďte hodnotu připojovacího řetězce připojovacím řetězcem, který jste předtím získali z portálu.

    ```xml
    <appSettings>
       <!-- Service Bus specific app settings for messaging connections -->
       <add key="Microsoft.ServiceBus.ConnectionString"
           value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey"/>
    </appSettings>
    ```
14. Stiskněte **Ctrl+Shift+B** nebo v nabídce **Sestavení** klikněte na **Sestavit řešení** a tím sestavte aplikaci a potvrďte přesnost své dosavadní práce.

## <a name="create-an-aspnet-application"></a>Vytvoření aplikace ASP.NET

V této části sestavíte jednoduchou aplikaci ASP.NET, která zobrazí data načtená z vaší produktové služby.

### <a name="create-the-project"></a>Vytvoření projektu

1. Zkontrolkujte, že je Visual Studio spuštěné s právy správce.
2. Ve Visual Studiu v nabídce **Soubor** klikněte na **Nový** a pak na **Projekt**.
3. V **Nainstalovaných šablonách** v části **Visual C#**, klikněte na **Webová aplikace ASP.NET (.NET Framework)**. Jako název projektu zadejte **ProductsPortal**. Pak klikněte na **OK**.

   ![Dialogové okno Nový projekt][15]

4. V seznamu **Šablony ASP.NET** v dialogovém okně **Nová webová aplikace ASP.NET** klikněte na **MVC**.

   ![Vyberte webová aplikace ASP .NET][16]

6. Klikněte na tlačítko **Změna ověřování**. V dialogovém okně **Změnit ověřování** zkontrolujte, že je vybraná možnost **Bez ověřování**, a potom klikněte na **OK**. V tomto kurzu nasazujete aplikace, která nepotřebuje přihlášení uživatele.

    ![Zadejte ověřování][18]

7. Vraťte se do dialogového okna **Nová webová aplikace ASP.NET** a kliknutím na **OK** vytvořte aplikaci MVC.
8. Teď musíte nakonfigurovat prostředky Azure pro novou webovou aplikaci. Postupujte podle pokynů v [části Publikování v Azure v tomto článku](../app-service/app-service-web-get-started-dotnet-framework.md#launch-the-publish-wizard). Potom se vraťte do tohoto kurzu a pokračujte dalším krokem.
10. V Průzkumníkovi řešení klikněte pravým tlačítkem na **Modely**, pak levým na **Přidat** a pak na **Třída**. Do pole **Název** zadejte název **Product.cs**: Pak klikněte na **Přidat**.

    ![Vytvoření modelu produktu][17]

### <a name="modify-the-web-application"></a>Úprava webové aplikace

1. V souboru Product.cs ve Visual Studiu nahraďte existující definici oboru názvů následujícím kódem:

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
2. V Průzkumníku řešení rozbalte složku **Kontrolery**, pak poklikejte na soubor **HomeController.cs** a ten se otevře ve Visual Studiu.
3. V **HomeController.cs**, nahraďte existující definici oboru názvů následujícím kódem:

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
4. V Průzkumníku řešení rozbalte složku Views\Shared, pak poklikejte na soubor **_Layout.cshtml** a ten se otevře v editoru Visual Studia.
5. Všechny výskyty **My ASP.NET Application** změňte na **Northwind Traders Products**.
6. Odstraňte odkazy **Home**, **About** a **Contact**. V následujícím příkladu odstraňte zvýrazněný kód.

    ![Odstranit položky generovaného seznamu][41]

7. V Průzkumníku řešení rozbalte složku Views\Home, pak poklikejte na soubor **Index.cshtml** a ten se otevře v editoru Visual Studia. Celý obsah souboru nahraďte následujícím kódem:

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
8. Pokud chcete zkontrolovat přesnost svojí dosavadní práce, můžete stisknout **Ctrl+Shift+B** a tím projekt sestavit.

### <a name="run-the-app-locally"></a>Místní spuštění aplikace

Spusťte aplikace pro kontrolu, že funguje.

1. Zkontrolujte, že aktivní projekt je **ProductsPortal**. V Průzkumníku řešení klikněte pravým tlačítkem na název projektu a klikněte na možnost **Nastavit jako spouštěný projekt**.
2. V sadě Visual Studio stiskněte **F5**.
3. Měla by se objevit vaše aplikace, jak běží v prohlížeči.

   ![Webová aplikace][21]

## <a name="put-the-pieces-together"></a>Složení částí do jednoho celku

Dalším krokem je spojit lokální produktový server s aplikací ASP.NET.

1. Pokud již není otevřen v sadě Visual Studio znovu **ProductsPortal** jste vytvořili v projektu [vytvoření aplikace ASP.NET](#create-an-aspnet-application) oddílu.
2. Podobně jako v části Vytvoření lokálního serveru přidejte do referencí projektu balíček NuGet. V Průzkumníku řešení klikněte pravým tlačítkem na projekt **ProductsPortal**, pak klikněte na **Správa balíčků NuGet**.
3. Vyhledejte **WindowsAzure.ServiceBus** a vyberte položku **WindowsAzure.ServiceBus**. Potom zavřete dialogové okno, tím dokončíte instalaci.
4. V Průzkumníku řešení klikněte pravým tlačítkem na projekt **ProductsPortal**, pak klikněte na **Přidat** a pak na **Existující položka**.
5. Přejděte na soubor **ProductsContract.cs** v konzolovém projektu **ProductsServer**. Kliknutím zvýrazněte ProductsContract.cs. Klikněte na šipku dolů vedle tlačítka **Přidat**, pak klikněte na **Přidat jako odkaz**.

   ![Přidat jako odkaz][24]

6. Nyní otevřete **HomeController.cs** souboru v editoru sady Visual Studio a nahraďte definici oboru názvů následujícím kódem: Nezapomeňte nahradit *yourServiceNamespace* s názvem vaší služby obor názvů, a *yourKey* váš SAS klíč. To umožňuje klientovi umožní zavolat lokální službou, vrací se výsledek volání.

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
7. V Průzkumníku řešení klikněte pravým tlačítkem na řešení **ProductsPortal** (klikněte skutečně na řešení, ne na projekt). Klikněte na **Přidat** a pak klikněte na **Existující projekt**.
8. Přejděte do projektu **ProductsServer**, pak poklikejte na řešení **ProductsServer.csproj** a tím ho přidejte.
9. Aby se data mohla zobrazit na **ProductsPortal**, musí běžet **ProductsServer**. V Průzkumníku řešení klikněte pravým tlačítkem na řešení **ProductsPortal** a pak na **Vlastnosti**. Zobrazí se dialogové okno **Stránky vlastností**.
10. Na levé straně klikněte na **Spouštěný projekt**. Na pravé straně klikněte na **Více projektů po spuštění**. Zkontrolujte, že se objeví **ProductsServer** a **ProductsPortal** v tomto pořadí, a že je pro obojí nastavená akce **Start**.

      ![Více projektů po spuštění][25]

11. Pořád ještě v dialogovém okně **Vlastnosti** na levé straně klikněte na **Závislosti projektu**.
12. V seznamu **Projekty** klikněte na **ProductsServer**. Zkontrolujte, že **ProductsPortal** není vybraný.
13. V seznamu **Projekty** klikněte na **ProductsPortal**. Zkontrolujte, že je **ProductsServer** vybraný.

    ![Závislosti projektu][26]

14. Kliknutím na **OK** zavřete se dialogové okno **Stránky vlastností**.

## <a name="run-the-project-locally"></a>Spusťte projekt lokálně.

Aplikaci můžete lokálně spustit a otestovat ve Visual Studiu stisknutím klávesy **F5**. Nejdřív by se měl spustit lokální server (**ProductsServer**), potom by se v okně prohlížeče měla spustit aplikace **ProductsPortal**. Tentokrát uvidíte, že inventář produktů zobrazí seznam dat načtených z produktu service v místním systému.

![Webová aplikace][10]

Na stránce **ProductsPortal** stiskněte **Obnovit**. Pokaždé, když obnovíte stránku, uvidíte aplikaci server zobrazí zprávu při `GetProducts()` z **ProductsServer** je volána.

Před dalším krokem zavřete obě aplikace.

## <a name="deploy-the-productsportal-project-to-an-azure-web-app"></a>Nasazení projektu ProductsPortal do webové aplikace Azure

Dalším krokem je nové publikování frontendu **ProductsPortal** webové aplikace Azure. Udělejte toto:

1. V Průzkumníku řešení klikněte pravým tlačítkem myši na projekt **ProductsPortal** a klikněte na **Publikovat**. Potom klikněte na **Publikovat** na stránce **Publikovat**.

  > [!NOTE]
  > V okně prohlížeče se při automatickém spuštění webového projektu **ProductsPortal** po nasazení může objevit chybová zpráva. To je v pořádku a děje se to, protože aplikace **ProductsServer** ještě neběží.
>
>

2. Zkopírujte adresu URL nasazené aplikace protože ji budete potřebovat v dalším kroku. Tuto adresu URL můžete taky získat z okna Aktivita služby Azure App Service ve Visual Studiu:

  ![Adresa URL nasazené aplikace][9]

3. Zavřením okna prohlížeče zastavte spuštěnou aplikaci.

### <a name="set-productsportal-as-web-app"></a>Nastavení ProductsPortal jako webové aplikace

Než spustíte aplikaci v cloudu, musíte zkontrolovat, že se **ProductsPortal** spustí z Visual Studia jak webová aplikace.

1. V sadě Visual Studio klikněte pravým tlačítkem myši na projekt **ProductsPortal** a potom klikněte na **Vlastnosti**.
2. V levém sloupci klikněte na **Web**.
3. V části **Spustit akci** klikněte na tlačítko **Otevřít adresu URL** a do textového pole zadejte URL webové aplikace nasazené předtím, například `http://productsportal1234567890.azurewebsites.net/`.

    ![Počáteční adresa URL][27]

4. Ve Visual Studiu zvolte v nabídce **Soubor** možnost **Uložit vše**.
5. Ve Visual Studiu zvolte v nabídce Sestavení možnost **Znovu sestavit řešení**.

## <a name="run-the-application"></a>Spuštění aplikace

1. Stisknutím klávesy F5 aplikaci sestavíte a spustíte. Na místním serveru ( **ProductsServer** konzolové aplikace) by měla začínat první, pak bude **ProductsPortal** v okně prohlížeče měla spustit aplikace, jak je znázorněno na následujícím snímku obrazovky: Znovu si všimněte, že inventář produktů zobrazí seznam dat načtených ze služby v místním systému a tato data zobrazí ve webové aplikaci. Zkontrolujte adresu URL a ujistěte se, že **ProductsPortal** běží v cloudu jako webová aplikace Azure.

   ![Spuštění webové aplikace v Azure][1]

   > [!IMPORTANT]
   > Konzolová aplikace **ProductsServer** musí běžet a musí být schopná odesílat data do aplikace **ProductsPortal**. pokud prohlížeč zobrazí chybu, počkejte několik dalších sekund, než se načte **ProductsServer** a zobrazí se následující zpráva. Potom v prohlížeči stiskněte **Obnovit**.
   >
   >

   ![Výstup ze serveru][37]
2. Zpátky v prohlížeči na stránce **ProductsPortal** stiskněte **Obnovit**. Pokaždé, když obnovíte stránku, uvidíte aplikaci server zobrazí zprávu při `GetProducts()` z **ProductsServer** je volána.

    ![Aktualizované výstupu][38]

## <a name="next-steps"></a>Další postup
Přejděte k následujícímu kurzu: 

> [!div class="nextstepaction"]
>[Zveřejnění místní služby WCF pro klienta WCF mimo vaši síť](service-bus-relay-tutorial.md)

[0]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hybrid.png
[1]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App2.png
[NuGet]: http://nuget.org

[11]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-con-1.png
[13]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-13.png
[15]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-2.png
[16]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-4.png
[17]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-7.png
[18]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-5.png
[9]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-9.png
[10]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App3.png

[21]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App1.png
[24]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-12.png
[25]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-13.png
[26]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-14.png
[27]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-8.png

[36]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App2.png
[37]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-service1.png
[38]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-service2.png
[41]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-40.png
[43]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-43.png
