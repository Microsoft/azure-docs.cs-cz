---
title: Kurz REST Azure předáváním přes | Microsoft Docs
description: Vytvořit jednoduchou hostitelskou aplikaci předávání přes Azure Service Bus, která vystavuje rozhraní založené na REST.
services: service-bus-relay
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: 1312b2db-94c4-4a48-b815-c5deb5b77a6a
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/06/2017
ms.author: sethm
ms.openlocfilehash: 7a5a2916514a125d0b7443ced42e5ec600c68857
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2017
ms.locfileid: "24008032"
---
# <a name="azure-wcf-relay-rest-tutorial"></a>Kurz pro Azure předávání přes REST WCF

Tento kurz popisuje, jak vytvořit jednoduchou hostitelskou aplikaci předávání přes Azure, která vystavuje rozhraní založené na REST. REST webovému klientovi, jako je třeba webový prohlížeč, umožňuje přístup k API pro Service Bus přes požadavky HTTP.

Tento kurz používá programovací model REST Windows Communication Foundation (WCF) k vytvoření služby v Azure předávání. Další informace najdete v dokumentaci [Programovací model Rest WCF](/dotnet/framework/wcf/feature-details/wcf-web-http-programming-model) a [Návrh a implementace služeb](/dotnet/framework/wcf/designing-and-implementing-services).

## <a name="step-1-create-a-namespace"></a>Krok 1: Vytvoření oboru názvů

Pokud chcete začít používat přenosové funkce v Azure, musíte nejdříve vytvořit obor názvů služby. Obor názvů poskytuje kontejner oboru pro adresování prostředků Azure v rámci vaší aplikace. Pokud chcete vytvořit obor názvů Relay, postupujte podle [těchto pokynů](relay-create-namespace-portal.md).

## <a name="step-2-define-a-rest-based-wcf-service-contract-to-use-with-azure-relay"></a>Krok 2: Definování kontraktu služby WCF na bázi REST pro použití s Azure předávání

Při vytváření služby WCF stylu REST, je nutné definovat kontrakt. Kontrakt určuje, které operace hostitel podporuje. Operaci služby se můžeme představit jako metodu webové služby. Kontrakty se vytvoří definováním základního rozhraní C++, C# nebo Visual Basic. Každá metoda v rozhraní odpovídá konkrétní operaci služby. Atribut [ServiceContractAttribute](/dotnet/api/system.servicemodel.servicecontractattribute) se musí použít na každé rozhraní a atribut [OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute) se musí použít na každou operaci. Pokud metoda v rozhraní, které má atribut [ServiceContractAttribute](/dotnet/api/system.servicemodel.servicecontractattribute), nemá atribut [OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute), taková metoda se nevystaví. Kód použitý pro tyto úlohy je v následujícím příkladu za postupem.

Hlavní rozdíl mezi kontraktu WCF a kontraktu ve stylu REST je přidání vlastnosti do [OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute): [WebGetAttribute](/dotnet/api/system.servicemodel.web.webgetattribute). Tato vlastnost vám umožní mapovat metodu ve svém rozhraní k metodě na druhé straně rozhraní. Tento příklad používá [WebGetAttribute](/dotnet/api/system.servicemodel.web.webgetattribute) atribut k propojení metody na HTTP GET. To umožňuje služba Service Bus přesně získat a interpretovat příkazy odeslané na rozhraní.

### <a name="to-create-a-contract-with-an-interface"></a>K vytvoření smlouvy s rozhraním

1. Spusťte Visual Studio jako správce: v nabídce **Start** klikněte na program pravým tlačítkem a vyberte možnost **Spustit jako správce**.
2. Vytvořte nový projekt konzolové aplikace. Klikněte na nabídku **Soubor** a vyberte možnost **Nový**, a pak klikněte na **Projekt**. V dialogovém okně **Nový projekt** klikněte na **Visual C#**, vyberte šablonu **Konzolová aplikace** ta zadejte jí název **ImageListener**. Použijte výchozí **Umístění**. Klikněte na **OK**, tím vytvoříte projekt.
3. Visual Studio pro projekt C# vytvoří soubor `Program.cs`. Tato třída obsahuje prázdnou metodu `Main()` potřebnou ke správnému sestavení projektu konzolové aplikace.
4. Nainstalujte balíček Service Bus NuGet, tím do projektu přidáte odkazy na Service Bus a **System.ServiceModel.dll**. Tento balíček automaticky přidá reference na knihovny Service Bus a WCF **System.ServiceModel**. V Průzkumníku řešení klikněte pravým tlačítkem na projekt **ImageListener** a pak klikněte na **Správa balíčků NuGet**. Klikněte na kartu **Procházení** a potom najděte `Microsoft Azure Service Bus`. Klikněte na **Instalovat** a přijměte podmínky použití.
5. Do projektu musíte explicitně přidat odkaz na **System.ServiceModel.Web.dll**:
   
    a. V Průzkumníku řešení klikněte ve složce projektu pravým tlačítkem na složku **References**, pak klikněte na **Přidat odkaz**.
   
    b. V dialogovém okně **Přidat odkaz** klikněte vlevo na kartu **Architektura** a do pole **Hledat** zadejte **System.ServiceModel.Web**. Označte zatržítko **System.ServiceModel.Web** a klikněte na **OK**.
6. Na začátek souboru Program.cs přidejte následující příkazy `using`.
   
    ```csharp
    using System.ServiceModel;
    using System.ServiceModel.Channels;
    using System.ServiceModel.Web;
    using System.IO;
    ```
   
    [System.ServiceModel](/dotnet/api/system.servicemodel) je obor názvů, který umožňuje programový přístup k základním funkcím WCF. Předávání WCF používá mnoho objektů a atributů WCF k definování kontraktů služby. Ve většině aplikací předávání použijete tento obor názvů. Podobně [System.ServiceModel.Channels](/dotnet/api/system.servicemodel.channels) pomáhá definovat kanál, který je objekt, přes který komunikujete se předávání přes Azure a klientským webovým prohlížečem. Nakonec [System.ServiceModel.Web](/dotnet/api/system.servicemodel.web) obsahuje typy, které vám umožní vytvořit webové aplikace.
7. Přejmenujte obor názvů `ImageListener` na **Microsoft.ServiceBus.Samples**.
   
    ```csharp
    namespace Microsoft.ServiceBus.Samples
    {
        ...
    ```
8. Přímo po otevření složené závorky deklarace oboru názvů definujte nové rozhraní s názvem **IImageContract** a aplikujte atribut **ServiceContractAttribute** na rozhraní s hodnotou `http://samples.microsoft.com/ServiceModel/Relay/`. Hodnota oboru názvů se liší od oboru názvů, které používáte v celém svém kódu. Hodnota oboru názvů se používá jako jedinečný identifikátor pro tento kontrakt a měla by mít informaci o verzi. Další informace najdete v článku o [Správa verzí služeb](http://go.microsoft.com/fwlink/?LinkID=180498). Když explicitně zadáte obor názvů, zabráníte tím přidání výchozí hodnoty oboru názvů do názvu kontraktu.
   
    ```csharp
    [ServiceContract(Name = "ImageContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/RESTTutorial1")]
    public interface IImageContract
    {
    }
    ```
9. V rozhraní `IImageContract` deklarujte metodu pro jednu operaci, kterou kontrakt `IImageContract` vystaví v rozhraní, a aplikujte atribut `OperationContractAttribute` na metodu, kterou chcete vystavit v rámci veřejného kontraktu Service Bus.
   
    ```csharp
    public interface IImageContract
    {
        [OperationContract]
        Stream GetImage();
    }
    ```
10. V atributu **OperationContract** přidejte hodnotu **WebGet**.
    
    ```csharp
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }
    ```
    
    To umožňuje Ano předávací službu, kterou chcete směrovat požadavky HTTP GET na `GetImage`a překládat vrácené hodnoty `GetImage` do odpovědi HTTP GETRESPONSE. Později v tomto kurzu pomocí webového prohlížeče získáte přístup k této metodě a zobrazíte v prohlížeči obrázek.
11. Přímo po definici `IImageContract` deklarujte kanál, který zdědí vlastnosti z rozhraní `IImageContract` i `IClientChannel`.
    
    ```csharp
    public interface IImageChannel : IImageContract, IClientChannel { }
    ```
    
    Kanál je objekt WCF, kterým si služba a klient navzájem posílají informace. Později vytvoříte kanál ve své hostitelské aplikaci. Předávání přes Azure pak pomocí tohoto kanálu předat požadavky HTTP GET z prohlížeče do vaší **GetImage** implementace. Předávací službu také pomocí tohoto kanálu trvat **GetImage** vrátit hodnotu a přeloží ji do HTTP GETRESPONSE pro prohlížeče klienta.
12. V nabídce **Sestavení** klikněte na **Sestavit řešení** a zkontrolujte přesnost své dosavadní práce.

### <a name="example"></a>Příklad
Následující kód ukazuje základní rozhraní, které definuje kontrakt předávání WCF.

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

    [ServiceContract(Name = "IImageContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
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

## <a name="step-3-implement-a-rest-based-wcf-service-contract-to-use-service-bus"></a>Krok 3: Implementace kontraktu služby WCF na bázi REST pro použití služby Service Bus
Vytváření stylu REST WCF předávací služba vyžaduje, abyste nejdřív vytvořili kontrakt, který se definuje pomocí rozhraní. Dalším krokem je implementace rozhraní. K tomu patří vytvoření třídy s názvem **ImageService**, která implementuje uživatelsky definované rozhraní **IImageContract**. Po implementaci kontraktu nakonfigurujete rozhraní pomocí souboru App.config. Konfigurační soubor obsahuje nezbytné informace pro aplikaci, například název služby, název kontraktu a typ protokolu, který se používá ke komunikaci se službou předávání. Kód použitý k těmto úlohám najdete v příkladu za postupem.

Stejně jako u v předchozích krocích je jen malý rozdíl mezi implementací kontraktu ve stylu REST a kontraktu WCF předávání.

### <a name="to-implement-a-rest-style-service-bus-contract"></a>Implementace kontraktu Service Bus ve stylu REST
1. Vytvořte novou třídu s názvem **ImageService** přímo po definování rozhraní **IImageContract**. Třída **ImageService** implementuje rozhraní **IImageContract**.
   
    ```csharp
    class ImageService : IImageContract
    {
    }
    ```
    Podobně jako u implementace jiných rozhraní můžete definici implementovat v jiném souboru. V tomto kurzu se ale implementace objeví ve stejném souboru jako definice rozhraní a metoda `Main()`.
2. Aplikujte atribut [ServiceBehaviorAttribute](/dotnet/api/system.servicemodel.servicebehaviorattribute) na třídu **IImageService** na znamení, že třída je implementace kontraktu WCF.
   
    ```csharp
    [ServiceBehavior(Name = "ImageService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
    }
    ```
   
    Jak jsme už zmínili, tento obor názvů není obvyklý obor názvů. Místo toho je součástí architektury WCF, která identifikuje kontrakt. Další informace najdete v tématu [názvy datových kontraktů](https://msdn.microsoft.com/library/ms731045.aspx) článek v dokumentaci WCF.
3. Přidejte do svého projekt obrázek ve formátu .jpg.  
   
    To je obrázek, který služba zobrazí ve webovém prohlížeči příjemce. Klikněte pravým tlačítkem na projekt a klikněte na **Přidat**. Pak klikněte na **Existující položka**. V dialogovém okně **Přidat existující položku** vyberte vhodný soubor .jpg a klikněte na **Přidat**.
   
    Když přidáváte soubor, zkontrolujte, že je v rozbalovacím seznamu vedle pole**Název souboru:** vybraná možnost **Všechny soubory**. Zbytek tohoto kurzu předpokládá, že je název obrázku „image.jpg“. Pokud máte jiný soubor, musíte přejmenovat nebo měnit kód odpovídajícím způsobem.
4. Pokud chcete zkontrolovat, že běžící služba dokáže najít soubor obrázku, v **Průzkumníku řešení** klikněte pravým tlačítkem na soubor obrázku, pak klikněte na **Vlastnosti**. V podokně **Vlastnosti** nastavte **Kopírovat do výstupního adresáře** na **Kopírovat, pokud je novější**.
5. Přidejte do projektu odkaz na sestavení **System.Drawing.dll** a taky přidejte následující přidružené příkazy `using`.  
   
    ```csharp
    using System.Drawing;
    using System.Drawing.Imaging;
    using Microsoft.ServiceBus;
    using Microsoft.ServiceBus.Web;
    ```
6. Ve třídě **ImageService** přidejte následující konstruktor, který načte bitovou mapu a odešle ji do klientského prohlížeče.
   
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
7. Přímo za předchozí kód přidejte následující metodu **GetImage** ve třídě **ImageService**, aby se vrátila zpráva HTTP,která bude obsahovat obrázek.
   
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
   
    Tato implementace používá **MemoryStream** k získání obrázku a jeho přípravu pro streamování do prohlížeče. Spustí pozici přenosu na nule, deklaruje obsah přenosu jako jpeg a přenese informaci.
8. V nabídce **Sestavení** klikněte na **Sestavit řešení**.

### <a name="to-define-the-configuration-for-running-the-web-service-on-service-bus"></a>Definování konfigurace pro spuštění webové služby v Service Bus
1. V **Průzkumníku řešení** poklikejte na **App.config** a otevře se v editoru Visual Studio.
   
    **App.config** soubor obsahuje název služby, koncový bod (tj. umístění předávání přes Azure vystaví pro klienty a hostiteli pro komunikaci mezi sebou) a vazbu (typ protokolu používaný pro komunikaci). Hlavní rozdíl je, že nakonfigurovaný koncový bod služby odkazuje na [WebHttpRelayBinding](/dotnet/api/microsoft.servicebus.webhttprelaybinding) vazby.
2. XML element `<system.serviceModel>` je element WCF, který definuje jednu nebo víc služeb. Tady se používá k definování názvu služby a koncového bodu. Na konci elementu `<system.serviceModel>` (ale pořád ještě uvnitř `<system.serviceModel>`) přidejte element `<bindings>`, který má následující obsah. To definuje vazbu použitou v aplikaci. Můžete definovat víc vazeb, ale v tomto kurzu se definuje jen jedna.
   
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
   
    Předchozí kód definuje WCF předávání [WebHttpRelayBinding](/dotnet/api/microsoft.servicebus.webhttprelaybinding) s **relayClientAuthenticationType** nastavena na **žádné**. Toto nastavení naznačuje, že koncový bod, který používá tuto vazbu, nepotřebuje pověření klienta.
3. Za element `<bindings>` přidejte element `<services>`. Podobně jako u vazeb můžete v jednom konfiguračním souboru definovat několik služeb. V tomto kurzu ale definujete jen jednu.
   
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
   
    Tento krok konfiguruje službu, která použije předtím nastavenou výchozí vazbu **webHttpRelayBinding**. Taky používá výchozí ho poskytovatele **sbTokenProvider**, který se definuje v dalším kroku.
4. Po `<services>` elementu, vytvoření `<behaviors>` element s následujícím obsahem, který nahradí "SAS_KEY" s *sdíleného přístupového podpisu* klíč (SAS), jste dříve získali z [portál Azure][Azure portal].
   
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
5. Ještě v souboru App.config v elementu `<appSettings>` nahraďte celou hodnotu připojovacího řetězce element připojovacím řetězcem, který jste předtím získali z portálu. 
   
    ```xml
    <appSettings>
       <!-- Service Bus specific app settings for messaging connections -->
       <add key="Microsoft.ServiceBus.ConnectionString"
           value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SAS_KEY"/>
    </appSettings>
    ```
6. V nabídce **Sestavení** klikněte na **Sestavit řešení** a sestavte celé řešení.

### <a name="example"></a>Příklad
Následující kód ukazuje implementaci kontraktu a služby pro službu založenou na REST, která běží ve službě Service Bus pomocí vazby **WebHttpRelayBinding**.

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


    [ServiceContract(Name = "ImageContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    [ServiceBehavior(Name = "ImageService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
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

Následující příklad ukazuje soubor App.config přidružený ke službě.

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
        <!-- Service Bus specific app setings for messaging connections -->
        <add key="Microsoft.ServiceBus.ConnectionString"
            value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey="YOUR_SAS_KEY"/>
    </appSettings>
</configuration>
```

## <a name="step-4-host-the-rest-based-wcf-service-to-use-azure-relay"></a>Krok 4: Hostování služby WCF na bázi REST pro použití předávání přes Azure
Tento krok popisuje, jak spustit webovou službu pomocí konzolové aplikace s WCF předávání. Úplný kód napsaný v tomto kroku najdete v příkladu za postupem.

### <a name="to-create-a-base-address-for-the-service"></a>Vytvoření bázové adresy pro tuto službu
1. V `Main()` deklaraci funkce, vytvořte proměnnou pro uložení oboru názvů vašeho projektu. Nezapomeňte nahradit `yourNamespace` s názvem oboru názvů předávání jste předtím vytvořili.
   
    ```csharp
    string serviceNamespace = "yourNamespace";
    ```
    Service Bus použije název vašeho oboru názvů k vytvoření jedinečné URI.
2. Vytvořte instanci `Uri` pro bázovou adresu služby, která je založená na tomto oboru názvů.
   
    ```csharp
    Uri address = ServiceBusEnvironment.CreateServiceUri("https", serviceNamespace, "Image");
    ```

### <a name="to-create-and-configure-the-web-service-host"></a>Vytvoření a konfigurace hostitele webové služby
* Vytvořte hostitele webové služby pomocí adresy URI, kterou jste předtím vytvořili v této části.
  
    ```csharp
    WebServiceHost host = new WebServiceHost(typeof(ImageService), address);
    ```
    Hostitel služby je objekt WCF, který instancuje hostitelskou aplikaci. Tento příklad jí předá typ hostitele, kterého chcete vytvořit (**ImageService**) a taky adresu, na které chcete hostitelskou aplikaci vystavit.

### <a name="to-run-the-web-service-host"></a>Spuštění hostitele webové služby
1. Otevřete službu.
   
    ```csharp
    host.Open();
    ```
    Služba teď běží.
2. Zobrazte zprávu oznamující, že zpráva běží, a jak službu zastavit.
   
    ```csharp
    Console.WriteLine("Copy the following address into a browser to see the image: ");
    Console.WriteLine(address + "GetImage");
    Console.WriteLine();
    Console.WriteLine("Press [Enter] to exit");
    Console.ReadLine();
    ```
3. Po dokončení zavřete hostitele služby.
   
    ```csharp
    host.Close();
    ```

## <a name="example"></a>Příklad
Následující příklad obsahuje kontrakt a implementaci služby z předchozích kroků tohoto kurzu a hostuje službu v konzolové aplikaci. Zkompilujte následující kód do spustitelného souboru s názvem ImageListener.exe.

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

    [ServiceContract(Name = "ImageContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    [ServiceBehavior(Name = "ImageService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
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

### <a name="compiling-the-code"></a>Zkompilování kódu
Po sestavení řešení proveďte následující kroky pro spuštění aplikace:

1. Spusťte službu stisknutím klávesy**F5** nebo přejděte k umístění spustitelného souboru (ImageListener\bin\Debug\ImageListener.exe) a spusťte ho. Nechte aplikaci spuštěnou, protože je potřeba při dalším kroku.
2. Zkopírujte a vložte adresu z příkazového řádku do prohlížeče, zobrazí se obrázek.
3. Když skončíte, v okně příkazovém řádku stiskněte **Enter** a aplikace se zavře.

## <a name="next-steps"></a>Další kroky
Teď, když jste sestavili aplikaci, která používá službu předávání přes Azure, najdete další informace v následujících článcích:

* [Přehled architektury služby Azure Service Bus](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md)
* [Přehled služby Azure Relay](relay-what-is-it.md)
* [Jak používat předávání služby WCF s rozhraním .NET](relay-wcf-dotnet-get-started.md)

[Azure portal]: https://portal.azure.com
