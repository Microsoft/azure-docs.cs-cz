---
title: 'Kurz: kurz REST s využitím Azure Relay'
description: 'Kurz: Sestavte hostitelskou aplikaci Azure Relay, která zpřístupňuje rozhraní založené na REST.'
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.date: 06/23/2020
ms.openlocfilehash: 0620f55650d0e4da0cd7a616649df952f3017455
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "88922323"
---
# <a name="tutorial-azure-wcf-relay-rest-tutorial"></a>Kurz: kurz pro Azure WCF Relay REST

V tomto kurzu se dozvíte, jak vytvořit Azure Relay hostitelskou aplikaci, která zpřístupňuje rozhraní založené na REST. REST webovému klientovi, jako je třeba webový prohlížeč, umožňuje přístup k API pro Service Bus přes požadavky HTTP.

V tomto kurzu se používá programovací model REST Windows Communication Foundation (WCF) k vytvoření služby REST na Azure Relay. Další informace najdete v tématu [programovací model REST WCF](/dotnet/framework/wcf/feature-details/wcf-web-http-programming-model) a [Návrh a implementace služeb](/dotnet/framework/wcf/designing-and-implementing-services).

V tomto kurzu provedete následující úlohy:

> [!div class="checklist"]
>
> * Nainstalovat požadavky pro tento kurz.
> * Vytvořte obor názvů Relay.
> * Definujte kontrakt služby WCF na bázi REST.
> * Implementujte kontrakt služby WCF na bázi REST.
> * Hostování a spuštění služby WCF založené na REST.
> * Spusťte a otestujte službu.

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu musí být splněné následující požadavky:

* Předplatné Azure. Pokud ho ještě nemáte, [Vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
* [Visual Studio 2015 nebo novější](https://www.visualstudio.com). V příkladech v tomto kurzu se používá Visual Studio 2019.
* Sada Azure SDK pro .NET Nainstalujte ji ze [stránky pro stažení sady SDK](https://azure.microsoft.com/downloads/).

## <a name="create-a-relay-namespace"></a>Vytvoření oboru názvů Relay

Pokud chcete začít používat přenosové funkce v Azure, musíte nejdříve vytvořit obor názvů služby. Obor názvů poskytuje kontejner oboru pro adresování prostředků Azure v rámci vaší aplikace. Pokud chcete vytvořit obor názvů Relay, postupujte podle [těchto pokynů](relay-create-namespace-portal.md).

## <a name="define-a-rest-based-wcf-service-contract-to-use-with-azure-relay"></a>Definování kontraktu služby WCF založeného na REST pro použití s Azure Relay

Při vytváření služby ve stylu REST WCF musíte definovat kontrakt. Kontrakt určuje, které operace hostitel podporuje. Operace služby se podobá metodě webové služby. Definujte kontrakt s rozhraním C++, C# nebo Visual Basic. Každá metoda v rozhraní odpovídá konkrétní operaci služby. Použijte atribut [ServiceContractAttribute](/dotnet/api/system.servicemodel.servicecontractattribute) pro každé rozhraní a použijte atribut [OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute) pro každou operaci. 

> [!TIP]
> Pokud metoda v rozhraní, které má [atribut ServiceContractAttribute](/dotnet/api/system.servicemodel.servicecontractattribute) , nemá [atribut OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute), tato metoda není vystavena. Kód použitý pro tyto úlohy se zobrazí v příkladu následujícím postupem.

Hlavním rozdílem mezi smlouvou WCF a smlouvou ve stylu REST je přidání vlastnosti do [atributu OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute): [WebGetAttribute](/dotnet/api/system.servicemodel.web.webgetattribute). Tato vlastnost vám umožní mapovat metodu ve svém rozhraní k metodě na druhé straně rozhraní. Tento příklad používá atribut [WebGetAttribute](/dotnet/api/system.servicemodel.web.webgetattribute) k propojení metody k `HTTP GET` . Tento přístup umožňuje Service Bus přesně načíst a interpretovat příkazy odesílané do rozhraní.

### <a name="to-create-a-contract-with-an-interface"></a>Vytvoření kontraktu s rozhraním

1. Spusťte Microsoft Visual Studio jako správce. Provedete to tak, že kliknete pravým tlačítkem na ikonu programu Visual Studio a vyberete **Spustit jako správce**.
1. V aplikaci Visual Studio vyberte možnost **vytvořit nový projekt**.
1. V možnosti **vytvořit nový projekt** zvolte **Konzolová aplikace (.NET Framework)** pro C# a vyberte **Další**.
1. Pojmenujte projekt *ImageListener*. Použijte výchozí **umístění** a potom vyberte **vytvořit**.

   Pro projekt C# vytvoří Visual Studio soubor *program. cs* . Tato třída obsahuje prázdnou metodu `Main()` potřebnou ke správnému sestavení projektu konzolové aplikace.

1. V **Průzkumník řešení** klikněte pravým tlačítkem na projekt **ImageListener** a pak vyberte **Spravovat balíčky NuGet**.
1. Vyberte **Procházet**, vyhledejte a vyberte **windowsazure. ServiceBus**. Vyberte **nainstalovat** a přijměte podmínky použití.

    Tento krok přidá odkazy na Service Bus a *System.ServiceModel.dll*. Tento balíček automaticky přidá odkazy na knihovny Service Bus a WCF `System.ServiceModel` .

1. Explicitně přidejte odkaz na `System.ServiceModel.Web.dll` projekt. V **Průzkumník řešení** klikněte pravým tlačítkem myši na **odkazy** ve složce projektu a vyberte možnost **Přidat odkaz**.
1. V nástroji **Přidat odkaz** vyberte možnost **Architektura** a do **Hledat** zadejte *System. ServiceModel. Web* . Označte zatržítko **System.ServiceModel.Web** a klikněte na **OK**.

Dále proveďte následující změny kódu v projektu:

1. Přidejte následující `using` příkazy do horní části souboru *program. cs* .

    ```csharp
    using System.ServiceModel;
    using System.ServiceModel.Channels;
    using System.ServiceModel.Web;
    using System.IO;
    ```

    * [System.ServiceModel](/dotnet/api/system.servicemodel) je obor názvů, který umožňuje programový přístup k základním funkcím WCF. WCF Relay používá mnoho objektů a atributů WCF k definování kontraktů služby. Tento obor názvů budete používat ve většině vašich aplikací Relay.
    * [System. ServiceModel. Channels](/dotnet/api/system.servicemodel.channels) pomáhá definovat kanál, který je objekt, přes který komunikujete s Azure Relay a klientským webovým prohlížečem.
    * [System. ServiceModel. Web](/dotnet/api/system.servicemodel.web) obsahuje typy, které umožňují vytvářet webové aplikace.

1. Přejmenujte `ImageListener` obor názvů na `Microsoft.ServiceBus.Samples` .

    ```csharp
    namespace Microsoft.ServiceBus.Samples
    {
        ...
    ```

1. Přímo za levou složenou závorku deklarace oboru názvů definujte nové rozhraní s názvem `IImageContract` a použijte `ServiceContractAttribute` atribut pro rozhraní s hodnotou `https://samples.microsoft.com/ServiceModel/Relay/RESTTutorial1` . 

    ```csharp
    [ServiceContract(Name = "ImageContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/RESTTutorial1")]
    public interface IImageContract
    {
    }
    ```

    Hodnota oboru názvů se liší od oboru názvů, které používáte v celém svém kódu. Hodnota oboru názvů je jedinečný identifikátor pro tento kontrakt a měla by obsahovat informace o verzi. Další informace najdete v článku o [Správa verzí služeb](/dotnet/framework/wcf/service-versioning). Když explicitně zadáte obor názvů, zabráníte tím přidání výchozí hodnoty oboru názvů do názvu kontraktu.

1. V `IImageContract` rozhraní deklarujte metodu pro jedinou operaci, kterou `IImageContract` kontrakt zpřístupňuje v rozhraní, a použijte `OperationContract` atribut na metodu, kterou chcete zveřejnit jako součást veřejné Service Bus kontraktu.

    ```csharp
    public interface IImageContract
    {
        [OperationContract]
        Stream GetImage();
    }
    ```

1. Do `OperationContract` atributu přidejte `WebGet` hodnotu.

    ```csharp
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }
    ```

   Přidání `WebGet` hodnoty umožňuje předávací službě směrovat požadavky HTTP GET do `GetImage` a přeložit návratové hodnoty `GetImage` do `HTTP GETRESPONSE` odpovědi. Později v tomto kurzu použijete k přístupu k této metodě webový prohlížeč a zobrazíte obrázek v prohlížeči.

1. Přímo po definici `IImageContract` deklarujte kanál, který zdědí vlastnosti z rozhraní `IImageContract` i `IClientChannel`.

    ```csharp
    public interface IImageChannel : IImageContract, IClientChannel { }
    ```

   Kanál je objekt WCF, kterým si služba a klient navzájem posílají informace. Později vytvoříte kanál v hostitelské aplikaci. Azure Relay pak použije tento kanál k předání požadavků HTTP GET z prohlížeče do vaší `GetImage` implementace. Předávání také používá kanál k převzetí `GetImage` návratové hodnoty a jejich překladu do `HTTP GETRESPONSE` pro prohlížeč klienta.

1. Vyberte **sestavit** sestavení  >  **řešení** a ověřte přesnost své dosavadní práce.

### <a name="example-that-defines-a-wcf-relay-contract"></a>Příklad definující kontrakt WCF Relay

Následující kód ukazuje základní rozhraní, které definuje kontrakt WCF Relay.

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

## <a name="implement-the-rest-based-wcf-service-contract"></a>Implementace kontraktu služby WCF založeného na REST

Chcete-li vytvořit službu WCF Relay ve stylu REST, vytvořte nejprve kontrakt pomocí rozhraní. Dalším krokem je implementace rozhraní. Tento postup zahrnuje vytvoření třídy s názvem `ImageService` , která implementuje uživatelsky definované `IImageContract` rozhraní. Po implementaci kontraktu nakonfigurujete rozhraní pomocí souboru *App.config* . Konfigurační soubor obsahuje informace potřebné pro aplikaci. Tyto informace zahrnují název služby, název kontraktu a typ protokolu, který se používá ke komunikaci se službou Relay. Kód použitý pro tyto úlohy se zobrazí v příkladu následujícím postupem.

Stejně jako v předchozích krocích existuje trochu rozdíl mezi implementací kontraktu ve stylu REST a kontraktem WCF Relay.

### <a name="to-implement-a-rest-style-service-bus-contract"></a>Implementace kontraktu Service Bus ve stylu REST

1. Vytvořte novou třídu s názvem `ImageService` přímo po definování rozhraní `IImageContract`. Třída `ImageService` implementuje rozhraní `IImageContract`.

    ```csharp
    class ImageService : IImageContract
    {
    }
    ```

    Podobně jako u implementace jiných rozhraní můžete definici implementovat v jiném souboru. V tomto kurzu se ale implementace objeví ve stejném souboru jako definice rozhraní a metoda `Main()`.

1. Použijte atribut [ServiceBehaviorAttribute](/dotnet/api/system.servicemodel.servicebehaviorattribute) pro `IImageService` třídu, aby označoval, že třída je implementací kontraktu WCF.

    ```csharp
    [ServiceBehavior(Name = "ImageService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
    }
    ```

    Jak bylo zmíněno dříve, tento obor názvů není tradiční obor názvů. Je součástí architektury WCF, která identifikuje kontrakt. Další informace najdete v tématu [názvy kontraktů dat](/dotnet/framework/wcf/feature-details/data-contract-names/).

1. Přidejte do projektu obrázek *. jpg* . Tento soubor je obrázek, který se zobrazí v prohlížeči přijímání.

   1. Klikněte pravým tlačítkem na projekt a vyberte **Přidat**.
   1. Pak vyberte **existující položka**.
   1. Pomocí **Přidat existující položku** přejděte na příslušný. jpg a pak vyberte **Přidat**. Při přidávání souboru vyberte **všechny soubory** z rozevíracího seznamu vedle pole **název souboru**.

   Zbytek tohoto kurzu předpokládá, že název obrázku je *image.jpg*. Máte-li jiný soubor, je nutné přejmenovat obrázek nebo změnit kód k kompenzaci.

1. Chcete-li zajistit, aby běžící služba mohla najít soubor bitové kopie, v **Průzkumník řešení** klikněte pravým tlačítkem myši na soubor obrázku a zvolte možnost **vlastnosti**. V nastavení **vlastnosti** nastavte **Kopírovat do výstupního adresáře** na **Kopírovat, pokud je novější**.

1. Použijte postup v části [k vytvoření kontraktu s rozhraním](#to-create-a-contract-with-an-interface) pro přidání odkazu na *System.Drawing.dll* sestavení do projektu.

1. Přidejte následující přidružené `using` příkazy:

    ```csharp
    using System.Drawing;
    using System.Drawing.Imaging;
    using Microsoft.ServiceBus;
    using Microsoft.ServiceBus.Web;
    ```

1. Ve `ImageService` třídě přidejte následující konstruktor, který načte rastrový obrázek a připraví ho k odeslání do prohlížeče klienta:

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

1. Přímo za předchozí kód přidejte následující `GetImage` metodu do `ImageService` třídy a vraťte zprávu HTTP, která obsahuje obrázek.

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

    Tato implementace používá `MemoryStream` k načtení image a její přípravě na streamování do prohlížeče. Spustí pozici streamu na nulu, deklaruje obsah streamu jako *. jpg* a streamuje informace.

1. Vyberte   >  **řešení** sestavení sestavení.

### <a name="to-define-the-configuration-for-running-the-web-service-on-service-bus"></a>Definování konfigurace pro spuštění webové služby v Service Bus

1. V **Průzkumník řešení** dvakrát klikněte na **App.config** a otevřete soubor v editoru sady Visual Studio.

    *App.config* soubor obsahuje název služby, koncový bod a vazbu. Koncový bod je umístění Azure Relay zveřejňuje klienty a hostitele ke vzájemné komunikaci. Vazba je typ protokolu, který se používá ke komunikaci. Hlavní rozdíl je v tom, že nakonfigurovaný koncový bod služby odkazuje na vazbu [WebHttpRelayBinding](/dotnet/api/microsoft.servicebus.webhttprelaybinding) .

1. XML element `<system.serviceModel>` je element WCF, který definuje jednu nebo víc služeb. Tady se používá k definování názvu a koncového bodu služby. V dolní části `<system.serviceModel>` prvku, ale stále v `<system.serviceModel>` , přidejte `<bindings>` element, který má následující obsah:

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

    Tento obsah definuje vazby používané v aplikaci. Můžete definovat více vazeb, ale pro tento kurz definujete pouze jeden.

    Předchozí kód definuje WCF Relay vazby [WebHttpRelayBinding](/dotnet/api/microsoft.servicebus.webhttprelaybinding) s `relayClientAuthenticationType` nastavením na `None` . Toto nastavení indikuje, že koncový bod, který používá tuto vazbu, nevyžaduje přihlašovací údaje klienta.

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

    Tento obsah nakonfiguruje službu, která používá dříve definovanou výchozí hodnotu `webHttpRelayBinding` . Používá také výchozí hodnotu `sbTokenProvider` , která je definována v dalším kroku.

1. Po `<services>` elementu vytvořte `<behaviors>` element s následujícím obsahem a nahraďte ho `SAS_KEY` klíčem sdíleného přístupového podpisu (SAS). Pokud chcete získat klíč SAS z [Azure Portal][Azure portal], přečtěte si téma [získání přihlašovacích údajů pro správu](service-bus-relay-tutorial.md#get-management-credentials).

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

1. Stále v *App.config* v `<appSettings>` elementu nahraďte celou hodnotu připojovacího řetězce připojovacím řetězcem, který jste dříve získali z portálu.

    ```xml
    <appSettings>
       <!-- Service Bus specific app settings for messaging connections -->
       <add key="Microsoft.ServiceBus.ConnectionString"
           value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SAS_KEY"/>
    </appSettings>
    ```

1. Vyberte   >  **řešení** sestavení sestavení a sestavte celé řešení.

### <a name="example-that-implements-the-rest-based-wcf-service-contract"></a>Příklad, který implementuje kontrakt služby WCF na bázi REST

Následující kód ukazuje implementaci kontraktu a služby pro službu založenou na REST, která běží na Service Bus s použitím `WebHttpRelayBinding` vazby.

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

Tato část popisuje, jak spustit webovou službu pomocí konzolové aplikace s WCF Relay. Úplný seznam kódu napsaného v této části se zobrazí v příkladu následujícím postupem.

### <a name="to-create-a-base-address-for-the-service"></a>Vytvoření bázové adresy pro tuto službu

1. V `Main()` deklaraci funkce Vytvořte proměnnou pro uložení oboru názvů vašeho projektu. Ujistěte se, že `yourNamespace` jste nahradili názvem oboru názvů přenosu, který jste předtím vytvořili.

    ```csharp
    string serviceNamespace = "yourNamespace";
    ```

    Service Bus použije název vašeho oboru názvů k vytvoření jedinečné URI.

1. Vytvořte instanci `Uri` pro bázovou adresu služby, která je založená na tomto oboru názvů.

    ```csharp
    Uri address = ServiceBusEnvironment.CreateServiceUri("https", serviceNamespace, "Image");
    ```

### <a name="to-create-and-configure-the-web-service-host"></a>Vytvoření a konfigurace hostitele webové služby

Ještě v `Main()` nástroji vytvořte hostitele webové služby pomocí adresy URI vytvořené dříve v této části.
  
```csharp
WebServiceHost host = new WebServiceHost(typeof(ImageService), address);
```

Hostitel služby je objekt WCF, který instancuje hostitelskou aplikaci. Tento příklad předá IT typ hostitele, který chcete vytvořit, `ImageService` a také adresu, na které chcete zveřejnit hostitelskou aplikaci.

### <a name="to-run-the-web-service-host"></a>Spuštění hostitele webové služby

1. Ještě v nástroji `Main()` přidejte následující řádek pro otevření služby.

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

### <a name="example-of-the-service-contract-and-implementation"></a>Příklad kontraktu a implementace služby

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

1. Vyberte F5 nebo přejděte do umístění spustitelného souboru *ImageListener\bin\Debug\ImageListener.exe* a spusťte službu. Nechte aplikaci spuštěnou, protože je potřeba k dalšímu kroku.
1. Zkopírujte a vložte adresu z příkazového řádku do prohlížeče, zobrazí se obrázek.
1. Až budete hotovi, v okně příkazového řádku vyberte zadat a aplikaci zavřete.

## <a name="next-steps"></a>Další kroky

Teď, když jste vytvořili aplikaci, která používá službu Azure Relay, najdete další informace v následujících článcích:

* [Co je Azure Relay?](relay-what-is-it.md)
* [Vystavení místní služby WCF REST pro externího klienta pomocí Azure WCF Relay](service-bus-relay-tutorial.md)

[Azure portal]: https://portal.azure.com
