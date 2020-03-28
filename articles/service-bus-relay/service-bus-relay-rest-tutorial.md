---
title: 'Kurz: Kurz REST pomocí Azure Relay'
description: 'Kurz: Vytvořte hostitelskou aplikaci Azure Service Bus Relay, která zpřístupňuje rozhraní založené na REST.'
services: service-bus-relay
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: 1312b2db-94c4-4a48-b815-c5deb5b77a6a
ms.service: service-bus-relay
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/05/2019
ms.author: spelluru
ms.openlocfilehash: 229ed2b00582f2c73ce68c47406d68325abda736
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "73718835"
---
# <a name="tutorial-azure-wcf-relay-rest-tutorial"></a>Kurz: Kurz Azure WCF Relay REST

Tento kurz popisuje, jak vytvořit hostitelskou aplikaci Azure Relay, která zveřejňuje rozhraní založené na REST. REST webovému klientovi, jako je třeba webový prohlížeč, umožňuje přístup k API pro Service Bus přes požadavky HTTP.

Kurz používá programovací model REST nadace Windows Communication Foundation (WCF) k vytvoření služby REST v Azure Relay. Další informace naleznete v [tématu WCF REST programovací model](/dotnet/framework/wcf/feature-details/wcf-web-http-programming-model) a [navrhování a implementaci služeb](/dotnet/framework/wcf/designing-and-implementing-services).

V tomto kurzu projdete následujícími úkoly:

> [!div class="checklist"]
>
> * Nainstalujte předpoklady pro tento kurz.
> * Vytvořte obor názvů Relay.
> * Definujte servisní smlouvu WCF založenou na rest.
> * Implementujte smlouvu WCF založenou na REST.
> * Hostovat a spouštět službu WCF založenou na rest.
> * Spusťte a otestujte službu.

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu musí být splněné následující požadavky:

* Předplatné Azure. Pokud ho nemáte, [vytvořte si účet zdarma,](https://azure.microsoft.com/free/) než začnete.
* [Sada Visual Studio 2015 nebo novější](https://www.visualstudio.com). Příklady v tomto kurzu používají Visual Studio 2019.
* Sada Azure SDK pro rozhraní .NET. Nainstalujte jej ze [stránky stažené sady SDK](https://azure.microsoft.com/downloads/).

## <a name="create-a-relay-namespace"></a>Vytvoření jmenovce přenosu

Pokud chcete začít používat přenosové funkce v Azure, musíte nejdříve vytvořit obor názvů služby. Obor názvů poskytuje kontejner oboru pro adresování prostředků Azure v rámci vaší aplikace. Pokud chcete vytvořit obor názvů Relay, postupujte podle [těchto pokynů](relay-create-namespace-portal.md).

## <a name="define-a-rest-based-wcf-service-contract-to-use-with-azure-relay"></a>Definování smlouvy o poskytování služeb WCF založené na rest, která se má používat s Azure Relay

Při vytváření služby wcf rest stylu, je nutné definovat smlouvy. Kontrakt určuje, které operace hostitel podporuje. Operace služby se podobá metodě webové služby. Definujte smlouvu s rozhraním Jazyka C++, C# nebo Visual Basic. Každá metoda v rozhraní odpovídá konkrétní operaci služby. Použijte atribut [ServiceContractAttribute](/dotnet/api/system.servicemodel.servicecontractattribute) pro každé rozhraní a pro každou operaci použijte atribut [OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute) Attribute. 

> [!TIP]
> Pokud metoda v rozhraní, které má [ServiceContractAttribute](/dotnet/api/system.servicemodel.servicecontractattribute) nemá [OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute), tato metoda není vystavena. Kód použitý pro tyto úkoly se zobrazí v příkladu následující postup.

Primární rozdíl mezi wcf smlouvy a rest-style smlouvy je přidání [vlastnosti OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute): [WebGetAttribute](/dotnet/api/system.servicemodel.web.webgetattribute). Tato vlastnost vám umožní mapovat metodu ve svém rozhraní k metodě na druhé straně rozhraní. Tento příklad používá atribut [WebGetAttribute](/dotnet/api/system.servicemodel.web.webgetattribute) k `HTTP GET`propojení metody s aplikací . Tento přístup umožňuje Service Bus přesně načíst a interpretovat příkazy odeslané do rozhraní.

### <a name="to-create-a-contract-with-an-interface"></a>Vytvoření smlouvy s rozhraním

1. Spusťte microsoft visual studio jako správce. Chcete-li tak učinit, klepněte pravým tlačítkem myši na ikonu programu sady Visual Studio a vyberte příkaz **Spustit jako správce**.
1. V sadě Visual Studio vyberte **Vytvořit nový projekt**.
1. V **okně Vytvořit nový projekt**zvolte Console App **(.NET Framework)** pro C# a vyberte **Další**.
1. Pojmenujte projekt *ImageListener*. Použijte výchozí **umístění**a pak vyberte **Vytvořit**.

   Pro projekt Jazyka C# Visual Studio vytvoří *Program.cs* soubor. Tato třída obsahuje prázdnou metodu `Main()` potřebnou ke správnému sestavení projektu konzolové aplikace.

1. V **Průzkumníku řešení**klepněte pravým tlačítkem myši na projekt **ImageListener** a vyberte **příkaz Manage NuGet Packages**.
1. Vyberte **Procházet**, pak vyhledejte a zvolte **WindowsAzure.ServiceBus**. Vyberte **Instalovat**a přijměte podmínky použití.

    Tento krok přidá odkazy na Service Bus a *System.ServiceModel.dll*. Tento balíček automaticky přidá odkazy na knihovny `System.ServiceModel`Service Bus a WCF .

1. Explicitně přidat `System.ServiceModel.Web.dll` odkaz na projekt. V **Průzkumníku řešení**klepněte pravým tlačítkem myši na **odkazy** ve složce projektu a vyberte **přidat odkaz**.
1. V **pole Přidat odkaz**vyberte možnost **Framework** a do **hledání**zadejte *soubor System.ServiceModel.Web* . Označte zatržítko **System.ServiceModel.Web** a klikněte na **OK**.

Dále proveďte následující změny kódu projektu:

1. V horní `using` části *souboru Program.cs* přidejte následující příkazy.

    ```csharp
    using System.ServiceModel;
    using System.ServiceModel.Channels;
    using System.ServiceModel.Web;
    using System.IO;
    ```

    * [System.ServiceModel](/dotnet/api/system.servicemodel) je obor názvů, který umožňuje programový přístup k základním funkcím WCF. WCF Relay používá mnoho objektů a atributů WCF k definování servisních smluv. Tento obor názvů se používá ve většině přenosových aplikací.
    * [System.ServiceModel.Channels](/dotnet/api/system.servicemodel.channels) pomáhá definovat kanál, což je objekt, jehož prostřednictvím komunikujete s Azure Relay a webovým prohlížečem klienta.
    * [System.ServiceModel.Web](/dotnet/api/system.servicemodel.web) obsahuje typy, které umožňují vytvářet webové aplikace.

1. Přejmenujte `ImageListener` obor `Microsoft.ServiceBus.Samples`názvů na .

    ```csharp
    namespace Microsoft.ServiceBus.Samples
    {
        ...
    ```

1. Bezprostředně po otevření složené závorky deklarace oboru `IImageContract` názvů definujte nové rozhraní s názvem a aplikujte `ServiceContractAttribute` atribut na rozhraní s hodnotou `https://samples.microsoft.com/ServiceModel/Relay/RESTTutorial1`. 

    ```csharp
    [ServiceContract(Name = "ImageContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/RESTTutorial1")]
    public interface IImageContract
    {
    }
    ```

    Hodnota oboru názvů se liší od oboru názvů, které používáte v celém svém kódu. Hodnota oboru názvů je jedinečný identifikátor pro tuto smlouvu a měla by mít informace o verzi. Další informace najdete v článku o [Správa verzí služeb](/dotnet/framework/wcf/service-versioning). Když explicitně zadáte obor názvů, zabráníte tím přidání výchozí hodnoty oboru názvů do názvu kontraktu.

1. V `IImageContract` rámci rozhraní deklarujte metodu `IImageContract` pro jednu operaci, `OperationContract` kterou smlouva zveřejňuje v rozhraní, a aplikujte atribut na metodu, kterou chcete vystavit jako součást smlouvy public Service Bus.

    ```csharp
    public interface IImageContract
    {
        [OperationContract]
        Stream GetImage();
    }
    ```

1. V `OperationContract` atributu přidejte hodnotu. `WebGet`

    ```csharp
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }
    ```

   Přidání `WebGet` hodnoty umožňuje přenosové službě směrovat požadavky `GetImage`HTTP GET do aplikace `GetImage` a `HTTP GETRESPONSE` převést vrácené hodnoty do odpovědi. Později v kurzu, budete používat webový prohlížeč pro přístup k této metodě, a zobrazit obrázek v prohlížeči.

1. Přímo po definici `IImageContract` deklarujte kanál, který zdědí vlastnosti z rozhraní `IImageContract` i `IClientChannel`.

    ```csharp
    public interface IImageChannel : IImageContract, IClientChannel { }
    ```

   Kanál je objekt WCF, kterým si služba a klient navzájem posílají informace. Později vytvoříte kanál v hostitelské aplikaci. Azure Relay pak používá tento kanál k předání požadavků `GetImage` HTTP GET z prohlížeče do vaší implementace. Relé také používá kanál, `GetImage` aby se vrácená `HTTP GETRESPONSE` hodnota a převést do prohlížeče klienta.

1. Vyberte **sestavení** > **sestavení řešení** pro potvrzení přesnosti vaší práce tak daleko.

### <a name="example-that-defines-a-wcf-relay-contract"></a>Příklad, který definuje smlouvu WCF Relay

Následující kód ukazuje základní rozhraní, které definuje wcf relé smlouvy.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "IImageContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

## <a name="implement-the-rest-based-wcf-service-contract"></a>Implementace smlouvy o poskytování služeb WCF založené na rest

Chcete-li vytvořit službu Relé ve stylu REST, nejprve vytvořte smlouvu pomocí rozhraní. Dalším krokem je implementace rozhraní. Tento postup zahrnuje vytvoření `ImageService` třídy s názvem, která implementuje rozhraní definované `IImageContract` uživatelem. Po implementaci smlouvy pak nakonfigurujete rozhraní pomocí souboru *App.config.* Konfigurační soubor obsahuje potřebné informace pro aplikaci. Tyto informace zahrnují název služby, název smlouvy a typ protokolu, který se používá ke komunikaci se službou přenosu. Kód použitý pro tyto úkoly se zobrazí v příkladu následující postup.

Stejně jako u předchozích kroků je malý rozdíl mezi implementací smlouvy ve stylu REST a smlouvy WCF Relay.

### <a name="to-implement-a-rest-style-service-bus-contract"></a>Implementace kontraktu Service Bus ve stylu REST

1. Vytvořte novou třídu s názvem `ImageService` přímo po definování rozhraní `IImageContract`. Třída `ImageService` implementuje rozhraní `IImageContract`.

    ```csharp
    class ImageService : IImageContract
    {
    }
    ```

    Podobně jako u implementace jiných rozhraní můžete definici implementovat v jiném souboru. V tomto kurzu se ale implementace objeví ve stejném souboru jako definice rozhraní a metoda `Main()`.

1. Použijte [ServiceBehaviorAttribute](/dotnet/api/system.servicemodel.servicebehaviorattribute) atribut `IImageService` třídy k označení, že třída je implementace wcf smlouvy.

    ```csharp
    [ServiceBehavior(Name = "ImageService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
    }
    ```

    Jak již bylo zmíněno dříve, tento obor názvů není tradiční obor názvů. Je součástí architektury WCF, která identifikuje smlouvy. Další informace naleznete v tématu [Názvy kontraktů dat](/dotnet/framework/wcf/feature-details/data-contract-names/).

1. Přidejte obrázek *JPG* do projektu. Tento soubor je obrázek, který služba zobrazí v přijímajícím prohlížeči.

   1. Klikněte pravým tlačítkem myši na projekt a vyberte **přidat**.
   1. Pak vyberte **Existující položka**.
   1. Pomocí **příkazu Přidat existující položku** vyhledejte příslušný soubor JPG a pak vyberte **Přidat**. Při přidávání souboru vyberte v rozevíracím seznamu vedle **položky Název souboru položku** **Všechny soubory** .

   Zbytek tohoto kurzu předpokládá, že název obrázku je *image.jpg*. Pokud máte jiný soubor, musíte obrázek přejmenovat nebo změnit kód, aby byl kompenzován.

1. Chcete-li se ujistit, že spuštěná služba může soubor bitové kopie najít, klikněte v **Průzkumníku řešení** pravým tlačítkem myši na soubor bitové kopie a pak zvolte **Vlastnosti**. Ve **vlastnostech**nastavte **příkaz Kopírovat do výstupního adresáře** na **kopírovat, pokud je novější**.

1. Použití postupu v [části Vytvoření smlouvy s rozhraním](#to-create-a-contract-with-an-interface) k přidání odkazu na sestavení *System.Drawing.dll* do projektu.

1. Přidejte následující `using` přidružené příkazy:

    ```csharp
    using System.Drawing;
    using System.Drawing.Imaging;
    using Microsoft.ServiceBus;
    using Microsoft.ServiceBus.Web;
    ```

1. Ve `ImageService` třídě přidejte následující konstruktor, který načte bitmapu a připraví ji odeslat do prohlížeče klienta:

    ```csharp
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";
   
        Image bitmap;
   
        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }
    }
    ```

1. Bezprostředně za předchozí kód, `GetImage` přidejte `ImageService` následující metodu ve třídě vrátit zprávu HTTP, která obsahuje obrázek.

    ```csharp
    public Stream GetImage()
    {
        MemoryStream stream = new MemoryStream();
        this.bitmap.Save(stream, ImageFormat.Jpeg);
   
        stream.Position = 0;
        WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";
   
        return stream;
    }
    ```

    Tato implementace `MemoryStream` používá k načtení obrázku a připravit ji pro streamování do prohlížeče. Spustí pozici datového proudu na nulu, deklaruje obsah datového proudu jako *.jpg*a streamuje informace.

1. Vyberte **sestavení** > **sestavení řešení**.

### <a name="to-define-the-configuration-for-running-the-web-service-on-service-bus"></a>Definování konfigurace pro spuštění webové služby v Service Bus

1. V **Průzkumníku řešení**poklepejte na **soubor App.config** a otevřete soubor v editoru Sady Visual Studio.

    Soubor *App.config* obsahuje název služby, koncový bod a vazbu. Koncový bod je umístění Azure Relay zveřejňuje pro klienty a hostitele komunikovat mezi sebou. Vazba je typ protokolu, který se používá ke komunikaci. Hlavní rozdíl je v tom, že koncový bod nakonfigurované služby odkazuje na vazbu [WebHttpRelayBinding.](/dotnet/api/microsoft.servicebus.webhttprelaybinding)

1. XML element `<system.serviceModel>` je element WCF, který definuje jednu nebo víc služeb. Zde se používá k definování názvu služby a koncového bodu. V dolní části `<system.serviceModel>` prvku, ale `<system.serviceModel>`stále `<bindings>` uvnitř , přidejte prvek, který má následující obsah:

    ```xml
    <bindings>
        <!-- Application Binding -->
        <webHttpRelayBinding>
            <binding name="default">
                <security relayClientAuthenticationType="None" />
            </binding>
        </webHttpRelayBinding>
    </bindings>
    ```

    Tento obsah definuje vazby použité v aplikaci. Můžete definovat více vazeb, ale pro tento kurz definujete pouze jednu.

    Předchozí kód definuje vazbu [WCF Relay WebHttpRelayBinding](/dotnet/api/microsoft.servicebus.webhttprelaybinding) s `relayClientAuthenticationType` nastavenou na `None`. Toto nastavení označuje, že koncový bod používající tuto vazbu nevyžaduje pověření klienta.

1. Za element `<bindings>` přidejte element `<services>`. Podobně jako u vazeb můžete v jednom konfiguračním souboru definovat několik služeb. V tomto kurzu ale definujete jen jednu.

    ```xml
    <services>
        <!-- Application Service -->
        <service name="Microsoft.ServiceBus.Samples.ImageService"
             behaviorConfiguration="default">
            <endpoint name="RelayEndpoint"
                    contract="Microsoft.ServiceBus.Samples.IImageContract"
                    binding="webHttpRelayBinding"
                    bindingConfiguration="default"
                    behaviorConfiguration="sbTokenProvider"
                    address="" />
        </service>
    </services>
    ```

    Tento obsah konfiguruje službu, která používá dříve definované výchozí `webHttpRelayBinding`nastavení . Používá také výchozí `sbTokenProvider`, který je definován v dalším kroku.

1. Po `<services>` elementu vytvořte `<behaviors>` prvek s následujícím `SAS_KEY` obsahem a nahrazte klíčem Sdílený přístupový podpis (SAS). Pokud chcete získat klíč SAS z [portálu Azure][Azure portal], přečtěte si informace [o získání přihlašovacích údajů pro správu](service-bus-relay-tutorial.md#get-management-credentials).

    ```xml
    <behaviors>
        <endpointBehaviors>
            <behavior name="sbTokenProvider">
                <transportClientEndpointBehavior>
                    <tokenProvider>
                        <sharedAccessSignature keyName="RootManageSharedAccessKey" key="YOUR_SAS_KEY" />
                    </tokenProvider>
                </transportClientEndpointBehavior>
            </behavior>
            </endpointBehaviors>
            <serviceBehaviors>
                <behavior name="default">
                    <serviceDebug httpHelpPageEnabled="false" httpsHelpPageEnabled="false" />
                </behavior>
            </serviceBehaviors>
    </behaviors>
    ```

1. Stále v *Souboru App.config*nahraďte v elementu `<appSettings>` celou hodnotu připojovacího řetězce připojovacím řetězcem, který jste dříve získali z portálu.

    ```xml
    <appSettings>
       <!-- Service Bus specific app settings for messaging connections -->
       <add key="Microsoft.ServiceBus.ConnectionString"
           value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SAS_KEY"/>
    </appSettings>
    ```

1. Vyberte **sestavení** > **sestavení řešení** k sestavení celé řešení.

### <a name="example-that-implements-the-rest-based-wcf-service-contract"></a>Příklad, který implementuje servisní smlouvu WCF založenou na službě ZALOŽENÉ NA REST

Následující kód ukazuje smlouvy a implementace služby pro službu založenou `WebHttpRelayBinding` na rest, která je spuštěna na service bus pomocí vazby.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;
using System.Drawing;
using System.Drawing.Imaging;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Web;

namespace Microsoft.ServiceBus.Samples
{


    [ServiceContract(Name = "ImageContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    [ServiceBehavior(Name = "ImageService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";

        Image bitmap;

        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }

        public Stream GetImage()
        {
            MemoryStream stream = new MemoryStream();
            this.bitmap.Save(stream, ImageFormat.Jpeg);

            stream.Position = 0;
            WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";

            return stream;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

Následující příklad ukazuje soubor *App.config* přidružený ke službě.

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
    <startup> 
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.2"/>
    </startup>
    <system.serviceModel>
        <extensions>
            <!-- In this extension section we are introducing all known service bus extensions. User can remove the ones they don't need. -->
            <behaviorExtensions>
                <add name="connectionStatusBehavior"
                    type="Microsoft.ServiceBus.Configuration.ConnectionStatusElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="transportClientEndpointBehavior"
                    type="Microsoft.ServiceBus.Configuration.TransportClientEndpointBehaviorElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="serviceRegistrySettings"
                    type="Microsoft.ServiceBus.Configuration.ServiceRegistrySettingsElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
            </behaviorExtensions>
            <bindingElementExtensions>
                <add name="netMessagingTransport"
                    type="Microsoft.ServiceBus.Messaging.Configuration.NetMessagingTransportExtensionElement, Microsoft.ServiceBus,  Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="tcpRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.TcpRelayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="httpRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.HttpRelayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="httpsRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.HttpsRelayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="onewayRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.RelayedOnewayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
            </bindingElementExtensions>
            <bindingExtensions>
                <add name="basicHttpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.BasicHttpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="webHttpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.WebHttpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="ws2007HttpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.WS2007HttpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netTcpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netOnewayRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetOnewayRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netEventRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetEventRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netMessagingBinding"
                    type="Microsoft.ServiceBus.Messaging.Configuration.NetMessagingBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
            </bindingExtensions>
        </extensions>
      <bindings>
        <!-- Application Binding -->
        <webHttpRelayBinding>
          <binding name="default">
            <security relayClientAuthenticationType="None" />
          </binding>
        </webHttpRelayBinding>
      </bindings>
      <services>
        <!-- Application Service -->
        <service name="Microsoft.ServiceBus.Samples.ImageService"
             behaviorConfiguration="default">
          <endpoint name="RelayEndpoint"
                  contract="Microsoft.ServiceBus.Samples.IImageContract"
                  binding="webHttpRelayBinding"
                  bindingConfiguration="default"
                  behaviorConfiguration="sbTokenProvider"
                  address="" />
        </service>
      </services>
      <behaviors>
        <endpointBehaviors>
          <behavior name="sbTokenProvider">
            <transportClientEndpointBehavior>
              <tokenProvider>
                <sharedAccessSignature keyName="RootManageSharedAccessKey" key="YOUR_SAS_KEY" />
              </tokenProvider>
            </transportClientEndpointBehavior>
          </behavior>
        </endpointBehaviors>
        <serviceBehaviors>
          <behavior name="default">
            <serviceDebug httpHelpPageEnabled="false" httpsHelpPageEnabled="false" />
          </behavior>
        </serviceBehaviors>
      </behaviors>
    </system.serviceModel>
    <appSettings>
        <!-- Service Bus specific app settings for messaging connections -->
        <add key="Microsoft.ServiceBus.ConnectionString"
            value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SAS_KEY>"/>
    </appSettings>
</configuration>
```

## <a name="host-the-rest-based-wcf-service-to-use-azure-relay"></a>Hostování služby WCF založené na REST pro použití Azure Relay

Tato část popisuje, jak spustit webovou službu pomocí konzolové aplikace s WCF Relay. Úplný seznam kódu napsaného v této části se zobrazí v příkladu následujícího postupu.

### <a name="to-create-a-base-address-for-the-service"></a>Vytvoření bázové adresy pro tuto službu

1. V `Main()` deklaraci funkce vytvořte proměnnou pro uložení oboru názvů projektu. Nezapomeňte nahradit `yourNamespace` název oboru názvů Relay, který jste vytvořili dříve.

    ```csharp
    string serviceNamespace = "yourNamespace";
    ```

    Service Bus použije název vašeho oboru názvů k vytvoření jedinečné URI.

1. Vytvořte instanci `Uri` pro bázovou adresu služby, která je založená na tomto oboru názvů.

    ```csharp
    Uri address = ServiceBusEnvironment.CreateServiceUri("https", serviceNamespace, "Image");
    ```

### <a name="to-create-and-configure-the-web-service-host"></a>Vytvoření a konfigurace hostitele webové služby

Stále `Main()`v aplikaci vytvořte hostitele webové služby pomocí adresy URI vytvořené dříve v této části.
  
```csharp
WebServiceHost host = new WebServiceHost(typeof(ImageService), address);
```

Hostitel služby je objekt WCF, který instancuje hostitelskou aplikaci. Tento příklad předá typ hostitele, který chcete `ImageService`vytvořit, což je , a také adresu, na které chcete vystavit hostitelskou aplikaci.

### <a name="to-run-the-web-service-host"></a>Spuštění hostitele webové služby

1. Stále `Main()`v , přidejte následující řádek pro otevření služby.

    ```csharp
    host.Open();
    ```

    Služba teď běží.

1. Zobrazte zprávu oznamující, že zpráva běží, a jak službu zastavit.

    ```csharp
    Console.WriteLine("Copy the following address into a browser to see the image: ");
    Console.WriteLine(address + "GetImage");
    Console.WriteLine();
    Console.WriteLine("Press [Enter] to exit");
    Console.ReadLine();
    ```

1. Po dokončení zavřete hostitele služby.

    ```csharp
    host.Close();
    ```

### <a name="example-of-the-service-contract-and-implementation"></a>Příklad smlouvy o poskytování služeb a realizace

Následující příklad obsahuje kontrakt a implementaci služby z předchozích kroků tohoto kurzu a hostuje službu v konzolové aplikaci. Zkompilujte následující kód do spustitelného souboru s názvem *ImageListener.exe*.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;
using System.Drawing;
using System.Drawing.Imaging;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Web;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "ImageContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    [ServiceBehavior(Name = "ImageService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";

        Image bitmap;

        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }

        public Stream GetImage()
        {
            MemoryStream stream = new MemoryStream();
            this.bitmap.Save(stream, ImageFormat.Jpeg);

            stream.Position = 0;
            WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";

            return stream;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
            string serviceNamespace = "InsertServiceNamespaceHere";
            Uri address = ServiceBusEnvironment.CreateServiceUri("https", serviceNamespace, "Image");

            WebServiceHost host = new WebServiceHost(typeof(ImageService), address);
            host.Open();

            Console.WriteLine("Copy the following address into a browser to see the image: ");
            Console.WriteLine(address + "GetImage");
            Console.WriteLine();
            Console.WriteLine("Press [Enter] to exit");
            Console.ReadLine();

            host.Close();
        }
    }
}
```

## <a name="run-and-test-the-service"></a>Spuštění a testování služby

Po sestavení řešení proveďte následující kroky pro spuštění aplikace:

1. Vyberte f5 nebo vyhledejte umístění spustitelného souboru *ImageListener\bin\Debug\ImageListener.exe*, chcete-li spustit službu. Udržujte aplikaci spuštěnou, protože je vyžadována pro další krok.
1. Zkopírujte a vložte adresu z příkazového řádku do prohlížeče, zobrazí se obrázek.
1. Až budete hotovi, zavřete aplikaci v okně příkazového řádku vyberte Enter.

## <a name="next-steps"></a>Další kroky

Teď, když jste vytvořili aplikaci, která používá službu Azure Relay, najdete v následujících článcích další informace:

* [Co je Azure Relay?](relay-what-is-it.md)
* [Vystavit místní službu WCF REST externímu klientovi pomocí Azure WCF Relay](service-bus-relay-tutorial.md)

[Azure portal]: https://portal.azure.com
