---
title: Vystavit službu WCF REST na prem klientům pomocí Azure Relay
description: Tento kurz popisuje, jak vystavit místní službu WCF REST externímu klientovi pomocí Azure WCF Relay.
services: service-bus-relay
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: 53dfd236-97f1-4778-b376-be91aa14b842
ms.service: service-bus-relay
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: 551c8e662669737d9d074a69cb03d6060ab87ad5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "76513078"
---
# <a name="tutorial-expose-an-on-premises-wcf-rest-service-to-external-client-by-using-azure-wcf-relay"></a>Kurz: Vystavit místní službu WCF REST externímu klientovi pomocí Azure WCF Relay

Tento kurz popisuje, jak vytvořit klientskou aplikaci a službu WCF Relay pomocí Azure Relay. Podobný kurz, který používá [zasílání zpráv service bus](../service-bus-messaging/service-bus-messaging-overview.md), najdete v tématu [Začínáme s fronty service bus](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md).

Práce prostřednictvím tohoto kurzu vám umožní pochopit kroky k vytvoření wcf přenosu klienta a aplikace služby. Stejně jako jejich původní protějšky WCF, služba je konstrukce, která zveřejňuje jeden nebo více koncových bodů. Každý koncový bod zveřejňuje jednu nebo více operací služby. Koncový bod služby specifikuje adresu, kde se dá služba najít, vazbu, která obsahuje informaci, že klient musí komunikovat se službou, a kontrakt, který definuje funkci, kterou služba klientovi poskytuje. Hlavní rozdíl mezi WCF a WCF Relé je, že koncový bod je vystaven a cloudu namísto místně v počítači.

Po práci prostřednictvím posloupnosti oddílů v tomto kurzu budete mít spuštěnou službu. Budete mít také klienta, který může vyvolat operace služby. 

V tomto kurzu projdete následujícími úkoly:

> [!div class="checklist"]
>
> * Nainstalujte předpoklady pro tento kurz.
> * Vytvořte obor názvů Relay.
> * Vytvořte servisní smlouvu WCF.
> * Implementujte smlouvu WCF.
> * Host a spustit službu WCF zaregistrovat se službou Relay.
> * Vytvořte klienta WCF pro servisní smlouvu.
> * Konfigurace klienta WCF.
> * Implementujte klienta WCF.
> * Spusťte aplikace.

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu musí být splněné následující požadavky:

* Předplatné Azure. Pokud ho nemáte, [vytvořte si účet zdarma,](https://azure.microsoft.com/free/) než začnete.
* [Sada Visual Studio 2015 nebo novější](https://www.visualstudio.com). Příklady v tomto kurzu používají Visual Studio 2019.
* Sada Azure SDK pro rozhraní .NET. Nainstalujte jej ze [stránky stažené sady SDK](https://azure.microsoft.com/downloads/).

## <a name="create-a-relay-namespace"></a>Vytvoření jmenovce přenosu

Prvním krokem je vytvoření oboru názvů a získání klíče [sdíleného přístupového podpisu (SAS).](../service-bus-messaging/service-bus-sas.md) Obor názvů poskytuje hranice aplikace pro každou aplikaci vystavené prostřednictvím služby přenosu. Klíč SAS je automaticky generován systémem při vytvoření oboru názvů služby. Kombinace oboru názvů služby a klíče SAS poskytuje pověření pro Azure k ověření přístupu k aplikaci.

[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="define-a-wcf-service-contract"></a>Definování smlouvy o poskytování služeb WCF

Servisní smlouva určuje, jaké operace služba podporuje. Operace jsou metody nebo funkce webové služby. Kontrakty se vytvoří definováním základního rozhraní C++, C# nebo Visual Basic. Každá metoda v rozhraní odpovídá konkrétní operaci služby. Na každé rozhraní musí mít aplikovaný atribut [ServiceContractAttribute](/dotnet/api/system.servicemodel.servicecontractattribute) a na každou operace musí byt aplikovaný atribut [OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute). Pokud metoda v rozhraní, která má atribut [ServiceContractAttribute,](/dotnet/api/system.servicemodel.servicecontractattribute) nemá atribut [OperationContractAttribute,](/dotnet/api/system.servicemodel.operationcontractattribute) tato metoda není vystavena. Kód k těmto úlohám najdete v příkladu za postupem. Širší diskusi o smlouvách a službách naleznete v [tématu Projektování a implementace služeb](/dotnet/framework/wcf/designing-and-implementing-services).

### <a name="create-a-relay-contract-with-an-interface"></a>Vytvoření smlouvy o přenosu s rozhraním

1. Spusťte microsoft visual studio jako správce. Chcete-li tak učinit, klepněte pravým tlačítkem myši na ikonu programu sady Visual Studio a vyberte příkaz **Spustit jako správce**.
1. V sadě Visual Studio vyberte **Vytvořit nový projekt**.
1. V **okně Vytvořit nový projekt**zvolte Console App **(.NET Framework)** pro C# a vyberte **Další**.
1. Pojmenujte projekt *EchoService* a vyberte **vytvořit**.

   ![Vytvoření konzolové aplikace][2]

1. V **Průzkumníku řešení**klepněte pravým tlačítkem myši na projekt a vyberte **příkaz Spravovat balíčky NuGet**. Ve **Správci balíčků NuGet**vyberte **Procházet**a pak vyhledejte a zvolte **WindowsAzure.ServiceBus**. Vyberte **Instalovat**a přijměte podmínky použití.

    ![Balíček service bus][3]

   Tento balíček automaticky přidá odkazy na knihovny `System.ServiceModel`Service Bus a WCF . [System.ServiceModel](/dotnet/api/system.servicemodel) je obor názvů, který vám umožňuje programový přístup k základním funkcím WCF. Service Bus používá mnoho objektů a atributů WCF k definování kontraktů služby.

1. V horní `using` části *Program.cs*přidejte následující příkazy :

    ```csharp
    using System.ServiceModel;
    using Microsoft.ServiceBus;
    ```

1. Změňte název oboru názvů z výchozího názvu `EchoService` na `Microsoft.ServiceBus.Samples`.

   > [!IMPORTANT]
   > Tento kurz používá obor `Microsoft.ServiceBus.Samples` názvů C#, což je obor názvů spravovaného typu založeného na správě založeného na správě, který se používá v konfiguračním souboru v části [Konfigurace klienta WCF.](#configure-the-wcf-client) Při vytváření této ukázky můžete zadat libovolný obor názvů. Kurz však nebude fungovat, pokud potom upravit obory názvů smlouvy a služby odpovídajícím způsobem v konfiguračním souboru aplikace. Obor názvů zadaný v souboru *App.config* musí být stejný jako obor názvů zadaný v souborech C#.
   >

1. Bezprostředně po `Microsoft.ServiceBus.Samples` deklaraci oboru názvů, ale v rámci `IEchoContract` oboru názvů `ServiceContractAttribute` definujte nové rozhraní s `https://samples.microsoft.com/ServiceModel/Relay/`názvem a aplikujte atribut na rozhraní s hodnotou oboru názvů . Za deklaraci oboru názvů vložte následující kód:

    ```csharp
    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
    }
    ```

    Hodnota oboru názvů se liší od oboru názvů, které používáte v celém svém kódu. Místo toho se obor názvů používá jako jedinečný identifikátor pro tento kontrakt. Když explicitně zadáte obor názvů, zabráníte tím přidání výchozí hodnoty oboru názvů do názvu kontraktu.

   > [!NOTE]
   > Obor názvů kontraktu služby obvykle obsahuje schéma pojmenování s informacemi o verzi. Informace o verzi, které jsou v oboru názvů kontraktu služby, službám umožňuje službám izolovat výrazné změny pomocí definice nové služby s novým oborem názvů, která bude vystavená na novém koncovém bodu. Tímto způsobem mohou klienti nadále používat starou servisní smlouvu, aniž by museli být aktualizováni. Informace o verzi může mít podobu data nebo čísla sestavení. Další informace najdete v článku o [Správa verzí služeb](/dotnet/framework/wcf/service-versioning). V tomto kurzu schéma pojmenování oboru názvů servisní smlouvy neobsahuje informace o verzi.
   >

1. V `IEchoContract` rámci rozhraní deklarujte metodu pro jednu operaci, kterou `IEchoContract` smlouva zveřejňuje v rozhraní, a aplikujte `OperationContractAttribute` atribut na metodu, kterou chcete vystavit jako součást veřejné smlouvy WCF Relay, následujícím způsobem:

    ```csharp
    [OperationContract]
    string Echo(string text);
    ```

1. Přímo po definici rozhraní `IEchoContract` deklarujte kanál, který zdědí vlastnosti z obou `IEchoContract` a také do rozhraní `IClientChannel`, jak je vidět tady:

    ```csharp
    public interface IEchoChannel : IEchoContract, IClientChannel { }
    ```

    Kanál je objekt WCF, kterým si hostitel a klient navzájem posílají informace. Později budete psát kód proti kanálu echo informace mezi dvěma aplikacemi.

1. Vyberte **Sestavit** > **řešení sestavení** nebo vyberte Ctrl+Shift+B, abyste potvrdili přesnost své dosavadní práce.

### <a name="example-of-a-wcf-contract"></a>Příklad smlouvy WCF

Následující kód ukazuje základní rozhraní, které definuje wcf relé smlouvy.

```csharp
using System;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

Když je teď vytvořené rozhraní, můžete ho implementovat.

## <a name="implement-the-wcf-contract"></a>Provedení smlouvy WCF

Vytvoření přenosu Azure vyžaduje, abyste nejprve vytvořit smlouvu pomocí rozhraní. Další informace o vytvoření rozhraní naleznete v předchozí části. Další postup implementuje rozhraní. Tato úloha zahrnuje `EchoService` vytvoření třídy s `IEchoContract` názvem, která implementuje rozhraní definované uživatelem. Po implementaci rozhraní pak nakonfigurujete rozhraní pomocí konfiguračního souboru *App.config.* Konfigurační soubor obsahuje potřebné informace pro aplikaci. Tyto informace zahrnují název služby, název smlouvy a typ protokolu, který se používá ke komunikaci se službou přenosu. Kód použitý pro tyto úkoly je uveden v příkladu, který následuje postup. Podrobnější informace o implementaci servisní smlouvy naleznete v [tématu Implementace servisních smluv](/dotnet/framework/wcf/implementing-service-contracts).

1. Vytvořte novou třídu s názvem `EchoService` přímo po definování rozhraní `IEchoContract`. Třída `EchoService` implementuje rozhraní `IEchoContract`.

    ```csharp
    class EchoService : IEchoContract
    {
    }
    ```

    Podobně jako u implementace jiných rozhraní můžete definici implementovat v jiném souboru. V tomto kurzu je ale implementace ve stejném souboru jako definice rozhraní a metoda `Main()`.

1. Na rozhraní `IEchoContract` aplikujte atribut [ServiceBehaviorAttribute](/dotnet/api/system.servicemodel.servicebehaviorattribute). Atribut specifikuje název služby a obor názvů. Když to dokončíte, třída `EchoService` bude vypadat takto:

    ```csharp
    [ServiceBehavior(Name = "EchoService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    class EchoService : IEchoContract
    {
    }
    ```

1. Implementujte metodu `Echo` definovanou v rozhraní `IEchoContract` ve třídě `EchoService`.

    ```csharp
    public string Echo(string text)
    {
        Console.WriteLine("Echoing: {0}", text);
        return text;
    }
    ```

1. Vyberte **Sestavit** > **řešení sestavení** nebo ctrl+shift+b.

### <a name="define-the-configuration-for-the-service-host"></a>Definovat konfiguraci pro hostitele služby

Konfigurační soubor je podobný konfiguračnímu souboru WCF. Obsahuje název služby, koncový bod a vazbu. Koncový bod je umístění Azure Relay zveřejňuje pro klienty a hostitele komunikovat mezi sebou. Vazba je typ protokolu, který se používá ke komunikaci. Hlavní rozdíl je, že tento koncový bod nakonfigurované služby odkazuje na vazbu [NetTcpRelayBinding,](/dotnet/api/microsoft.servicebus.nettcprelaybinding) která není součástí rozhraní .NET Framework. [NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) je jednou z vazeb definovaných službou.

1. V **Průzkumníku řešení**poklepejte na **soubor App.config** a otevřete soubor v editoru Sady Visual Studio.
1. V elementu `<appSettings>` nahraďte zástupné texty názvem svého oboru názvů a klíčem SAS, který jste zkopírovali v jednom z předchozích kroků.
1. Ve značkách `<system.serviceModel>` přidejte element `<services>`. V jednom konfiguračním souboru můžete definovat více přenosových aplikací. V tomto kurzu se ale definuje jen jedna.

    ```xml
    <?xmlversion="1.0"encoding="utf-8"?>
    <configuration>
      <system.serviceModel>
        <services>

        </services>
      </system.serviceModel>
    </configuration>
    ```

1. V elementu `<services>` přidejte element `<service>`, který definuje název služby.

    ```xml
    <service name="Microsoft.ServiceBus.Samples.EchoService">
    </service>
    ```

1. V elementu `<service>` definujte umístění koncového bodu kontraktu a také vazbu koncového bodu.

    ```xml
    <endpoint contract="Microsoft.ServiceBus.Samples.IEchoContract" binding="netTcpRelayBinding"/>
    ```

    Koncový bod definuje, kde bude klient hledat hostitelskou aplikaci. Později kurz používá tento krok k vytvoření identifikátoru URI, který plně zpřístupňuje hostitele prostřednictvím Azure Relay. Vazba deklaruje, že používáme TCP jako protokol ke komunikaci se službou přenosu.

1. Vyberte **Sestavit** > **řešení sestavení** nebo vyberte Ctrl+Shift+B, abyste potvrdili přesnost své dosavadní práce.

### <a name="example-of-implementation-of-a-service-contract"></a>Příklad realizace smlouvy o poskytování služeb

Následující kód ukazuje implementaci kontraktu služby.

```csharp
[ServiceBehavior(Name = "EchoService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]

    class EchoService : IEchoContract
    {
        public string Echo(string text)
        {
            Console.WriteLine("Echoing: {0}", text);
            return text;
        }
    }
```

Následující kód zobrazuje základní formát souboru *App.config* přidruženého k hostiteli služby.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.serviceModel>
    <services>
      <service name="Microsoft.ServiceBus.Samples.EchoService">
        <endpoint contract="Microsoft.ServiceBus.Samples.IEchoContract" binding="netTcpRelayBinding" />
      </service>
    </services>
    <extensions>
      <bindingExtensions>
        <add name="netTcpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
      </bindingExtensions>
    </extensions>
  </system.serviceModel>
</configuration>
```

## <a name="host-and-run-the-wcf-service-to-register-with-the-relay-service"></a>Hostování a spuštění služby WCF pro registraci u služby přenosu

Tento krok popisuje, jak spustit službu Azure Relay.

### <a name="create-the-relay-credentials"></a>Vytvoření pověření pro předávání

1. V `Main()` vytvořte dvě proměnné, do kterých se uloží obor názvů a klíč SAS načtené z okna konzoly.

    ```csharp
    Console.Write("Your Service Namespace: ");
    string serviceNamespace = Console.ReadLine();
    Console.Write("Your SAS key: ");
    string sasKey = Console.ReadLine();
    ```

    Klíč SAS bude později použit pro přístup k projektu. Obor názvů se předá do `CreateServiceUri` jako parametr a vytvoří se URI služby.

1. Pomocí [objektu TransportClientEndpointBehavior](/dotnet/api/microsoft.servicebus.transportclientendpointbehavior) deklarujte, že jako typ pověření budete používat klíč SAS. Následující kód přidejte přímo za kód, který jste přidali v předchozím kroku.

    ```csharp
    TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
    sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);
    ```

### <a name="create-a-base-address-for-the-service"></a>Vytvoření základní adresy pro službu

Po kódu, který jste přidali `Uri` v předchozí části, vytvořte instanci pro základní adresu služby. Toto URI specifikuje schéma Service Bus, obor názvů a cestu rozhraní služby.

```csharp
Uri address = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");
```

Hodnota "sb" je zkratka pro schéma service bus. Znamená to, že používáme TCP jako protokol. Toto schéma bylo také dříve uvedeno v konfiguračním souboru, když byla jako vazba zadána [síť NetTcpRelayBinding.](/dotnet/api/microsoft.servicebus.nettcprelaybinding)

V tomto kurzu je URI `sb://putServiceNamespaceHere.windows.net/EchoService`.

### <a name="create-and-configure-the-service-host"></a>Vytvoření a konfigurace hostitele služby

1. Stále pracuje `Main()`v , nastavte `AutoDetect`režim připojení na .

    ```csharp
    ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;
    ```

    Režim připojení popisuje protokol, který služba používá ke komunikaci se službou přenosu. http nebo tcp. Pomocí výchozího `AutoDetect`nastavení se služba pokusí připojit k Azure Relay přes TCP, pokud je k dispozici, a HTTP, pokud tcp není k dispozici. Tento výsledek se liší od protokolu, který služba určuje pro komunikaci s klientem. Jeho protokol se určuje podle požité vazby. Služba může například použít vazby [BasicHttpRelayBinding,](/dotnet/api/microsoft.servicebus.basichttprelaybinding) která určuje, že její koncový bod komunikuje s klienty přes protokol HTTP. Stejná služba `ConnectivityMode.AutoDetect` může určit tak, aby služba komunikuje s Azure Relay přes TCP.

1. Vytvořte hostitele služby pomocí URI, které jste předtím vytvořili v této části.

    ```csharp
    ServiceHost host = new ServiceHost(typeof(EchoService), address);
    ```

    Hostitel služby je objekt WCF, který instancuje službu. Zde jej předáte typ služby, kterou `EchoService` chcete vytvořit, typ a také na adresu, na které chcete službu vystavit.

1. V horní části souboru *Program.cs* přidejte odkazy na [System.ServiceModel.Description](/dotnet/api/system.servicemodel.description) a [Microsoft.ServiceBus.Description](/dotnet/api/microsoft.servicebus.description).

    ```csharp
    using System.ServiceModel.Description;
    using Microsoft.ServiceBus.Description;
    ```

1. Zpátky v `Main()` nakonfigurujte koncový bod, a povolte tak veřejný přístup.

    ```csharp
    IEndpointBehavior serviceRegistrySettings = new ServiceRegistrySettings(DiscoveryType.Public);
    ```

    Tento krok informuje přenosovou službu, že vaše aplikace lze nalézt veřejně kontrolou atomového kanálu pro váš projekt. Pokud nastavíte `DiscoveryType` na `private`, klient může stále přistupovat ke službě. Služba se však při hledání oboru `Relay` názvů nezobrazí. Místo toho by klient musel předem znát cestu ke koncovému bodu.

1. Použijte pověření služby pro koncové body služby definované v souboru *App.config:*

    ```csharp
    foreach (ServiceEndpoint endpoint in host.Description.Endpoints)
    {
        endpoint.Behaviors.Add(serviceRegistrySettings);
        endpoint.Behaviors.Add(sasCredential);
    }
    ```

    Jak již bylo uvedeno dříve, můžete deklarovat více služeb a koncových bodů v konfiguračním souboru. Pokud byste to udělali, tento kód by prošel konfigurační soubor a vyhledal by všechny koncové body, na které by měl vaše pověření použít. V tomto kurzu konfigurační soubor má pouze jeden koncový bod.

### <a name="open-the-service-host"></a>Otevření hostitele služby

1. Stále `Main()`v , přidejte následující řádek pro otevření služby.

    ```csharp
    host.Open();
    ```

1. Informujte uživatele, že zpráva běží, a vysvětlete mu, jak službu ukončit.

    ```csharp
    Console.WriteLine("Service address: " + address);
    Console.WriteLine("Press [Enter] to exit");
    Console.ReadLine();
    ```

1. Po dokončení zavřete hostitele služby.

    ```csharp
    host.Close();
    ```

1. Chcete-li vytvořit projekt, vyberte Kombinaci kláves Ctrl+Shift+B.

### <a name="example-that-hosts-a-service-in-a-console-application"></a>Příklad, který hostuje službu v konzolové aplikaci

Kód dokončeného servisu by se měl zobrazit následujícím způsobem. Kód zahrnuje smlouvu o poskytování služeb a implementaci z předchozích kroků v kurzu a hostuje službu v konzolové aplikaci.

```csharp
using System;
using System.ServiceModel;
using System.ServiceModel.Description;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Description;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { };

    [ServiceBehavior(Name = "EchoService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    class EchoService : IEchoContract
    {
        public string Echo(string text)
        {
            Console.WriteLine("Echoing: {0}", text);
            return text;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {

            ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;         

            Console.Write("Your Service Namespace: ");
            string serviceNamespace = Console.ReadLine();
            Console.Write("Your SAS key: ");
            string sasKey = Console.ReadLine();

           // Create the credentials object for the endpoint.
            TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
            sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);

            // Create the service URI based on the service namespace.
            Uri address = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");

            // Create the service host reading the configuration.
            ServiceHost host = new ServiceHost(typeof(EchoService), address);

            // Create the ServiceRegistrySettings behavior for the endpoint.
            IEndpointBehavior serviceRegistrySettings = new ServiceRegistrySettings(DiscoveryType.Public);

            // Add the Relay credentials to all endpoints specified in configuration.
            foreach (ServiceEndpoint endpoint in host.Description.Endpoints)
            {
                endpoint.Behaviors.Add(serviceRegistrySettings);
                endpoint.Behaviors.Add(sasCredential);
            }

            // Open the service.
            host.Open();

            Console.WriteLine("Service address: " + address);
            Console.WriteLine("Press [Enter] to exit");
            Console.ReadLine();

            // Close the service.
            host.Close();
        }
    }
}
```

## <a name="create-a-wcf-client-for-the-service-contract"></a>Vytvoření klienta WCF pro kontrakt služby

Dalším úkolem je vytvořit klientskou aplikaci a definovat servisní smlouvu, kterou budete implementovat později. Tyto kroky se podobají krokům použitým k vytvoření služby: definování smlouvy, úprava souboru *App.config,* použití pověření pro připojení ke službě přenosu a tak dále. Kód použitý k těmto úlohám najdete v příkladu za postupem.

1. Vytvořte nový projekt v aktuálním řešení Sady Visual Studio pro klienta:

   1. V **Průzkumníku řešení**klepněte pravým tlačítkem myši na aktuální řešení (ne na projekt) a vyberte **přidat** > **nový projekt**.
   1. V **přidejte nový projekt**vyberte Console App **(.NET Framework)** pro C#a vyberte **Další**.
   1. Pojmenujte projekt *EchoClient* a vyberte **vytvořit**.

1. V **Průzkumníku řešení**v projektu **EchoClient** **poklepejte** Program.cs otevřete soubor v editoru, pokud ještě není otevřený.
1. Změňte název oboru názvů z výchozího názvu `EchoClient` na `Microsoft.ServiceBus.Samples`.
1. Nainstalujte [balíček Service Bus NuGet](https://www.nuget.org/packages/WindowsAzure.ServiceBus):

   1. V **Průzkumníku řešení**klepněte pravým tlačítkem myši na **položku EchoClient** a vyberte příkaz **Spravovat balíčky NuGet**.
   1. Vyberte **Procházet**, pak vyhledejte a vyberte **WindowsAzure.ServiceBus**. Vyberte **Instalovat**a přijměte podmínky použití.

      ![Instalace balíčku sběrnice služby][4]

1. Do `using` souboru *Program.cs* přidejte příkaz pro obor názvů [System.ServiceModel.](/dotnet/api/system.servicemodel)

    ```csharp
    using System.ServiceModel;
    ```

1. Přidejte definici kontraktu služby do oboru názvů, jak je vidět v následujícím příkladu. Tato definice je shodná s definicí použitou v projektu **služby.** Přidejte tento kód v `Microsoft.ServiceBus.Samples` horní části oboru názvů.

    ```csharp
    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        string Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }
    ```

1. Chcete-li vytvořit klienta, vyberte kombinaci kláves Ctrl+Shift+B.

### <a name="example-of-the-echoclient-project"></a>Příklad projektu EchoClient

Následující kód zobrazuje aktuální stav *souboru Program.cs* v projektu **EchoClient.**

```csharp
using System;
using Microsoft.ServiceBus;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        string Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }


    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

## <a name="configure-the-wcf-client"></a>Konfigurace klienta WCF

V tomto kroku vytvoříte soubor *App.config* pro základní klientskou aplikaci, která přistupuje ke službě vytvořené dříve v tomto kurzu. Tento soubor *App.config* definuje smlouvu, vazbu a název koncového bodu. Kód použitý k těmto úlohám najdete v příkladu za postupem.

1. V **Průzkumníku řešení**v projektu **EchoClient** poklepáním na **App.config** otevřete soubor v editoru Sady Visual Studio.
1. V elementu `<appSettings>` nahraďte zástupné texty názvem svého oboru názvů a klíčem SAS, který jste zkopírovali v jednom z předchozích kroků.
1. V `system.serviceModel` rámci prvku `<client>` přidejte prvek.

    ```xml
    <?xmlversion="1.0"encoding="utf-8"?>
    <configuration>
      <system.serviceModel>
        <client>
        </client>
      </system.serviceModel>
    </configuration>
    ```

    Tento kód deklaruje, že definujete klientskou aplikaci ve stylu WCF.

1. V elementu `client` definujte název, kontrakt a typ vazby koncového bodu.

    ```xml
    <endpoint name="RelayEndpoint"
                    contract="Microsoft.ServiceBus.Samples.IEchoContract"
                    binding="netTcpRelayBinding"/>
    ```

    Tento kód definuje název koncového bodu. Také definuje kontrakt definovaný ve službě a skutečnost, že klientská aplikace používá TCP ke komunikaci s Azure Relay. Název koncového bodu se použije v následujícím kroku k propojení této konfigurace koncového bodu s URI služby.

1. Vyberte **Soubor** > **Uložit vše**.

### <a name="example-of-the-appconfig-file"></a>Příklad souboru App.config

Následující kód zobrazuje soubor *App.config* pro klienta Echo.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.serviceModel>
    <client>
      <endpoint name="RelayEndpoint"
                      contract="Microsoft.ServiceBus.Samples.IEchoContract"
                      binding="netTcpRelayBinding"/>
    </client>
    <extensions>
      <bindingExtensions>
        <add name="netTcpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
      </bindingExtensions>
    </extensions>
  </system.serviceModel>
</configuration>
```

## <a name="implement-the-wcf-client"></a>Implementace klienta WCF

V této části implementovat základní klientské aplikace, která přistupuje ke službě, kterou jste vytvořili dříve v tomto kurzu. Podobně jako služba, klient provádí mnoho stejných operací pro přístup k Azure Relay:

* Nastaví režim připojení.
* Vytvoří URI, které vyhledá hostitelskou službu.
* Definuje bezpečnostní pověření.
* Aplikuje pověření na připojení.
* Otevře připojení.
* Provádí úlohy specifické pro aplikace.
* Ukončí připojení.

Jedním z hlavních rozdílů je však, že klientská aplikace používá kanál pro připojení ke službě přenosu. Služba používá volání **ServiceHost**. Kód použitý k těmto úlohám najdete v příkladu za postupem.

### <a name="implement-a-client-application"></a>Implementace klientské aplikace

1. Nastavte režim připojení na `AutoDetect`. Do metody `Main()` aplikace **EchoClient** přidejte následující kód.

    ```csharp
    ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;
    ```

1. Definujte proměnné, do kterých se uloží hodnoty obor názvů služby a klíče SAS načtené z konzoly.

    ```csharp
    Console.Write("Your Service Namespace: ");
    string serviceNamespace = Console.ReadLine();
    Console.Write("Your SAS Key: ");
    string sasKey = Console.ReadLine();
    ```

1. Vytvořte identifikátor URI, který definuje umístění hostitele v projektu přenosu.

    ```csharp
    Uri serviceUri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");
    ```

1. Vytvořte objekt pověření pro koncový bod vašeho oboru názvů.

    ```csharp
    TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
    sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);
    ```

1. Vytvořte továrnu kanálu, která načte konfiguraci popsanou v souboru *App.config.*

    ```csharp
    ChannelFactory<IEchoChannel> channelFactory = new ChannelFactory<IEchoChannel>("RelayEndpoint", new EndpointAddress(serviceUri));
    ```

    Objekt kanálu pro vytváření je objekt WCF, který vytvoří kanál, přes který může služba komunikovat s klientskými aplikacemi.

1. Použijte pověření.

    ```csharp
    channelFactory.Endpoint.Behaviors.Add(sasCredential);
    ```

1. Vytvořte a otevřete kanál pro službu.

    ```csharp
    IEchoChannel channel = channelFactory.CreateChannel();
    channel.Open();
    ```

1. Napište základní uživatelské prostředí a funkci pro echo.

    ```csharp
    Console.WriteLine("Enter text to echo (or [Enter] to exit):");
    string input = Console.ReadLine();
    while (input != String.Empty)
    {
        try
        {
            Console.WriteLine("Server echoed: {0}", channel.Echo(input));
        }
        catch (Exception e)
        {
            Console.WriteLine("Error: " + e.Message);
        }
        input = Console.ReadLine();
    }
    ```

    Kód používá instanci objektu kanálu jako proxy server pro službu.

1. Zavřete kanál a zavřete objekt pro vytváření.

    ```csharp
    channel.Close();
    channelFactory.Close();
    ```

### <a name="example-code-for-this-tutorial"></a>Příklad kódu pro tento kurz

Vyplněný kód by se měl zobrazit následujícím způsobem. Tento kód ukazuje, jak vytvořit klientskou aplikaci, jak volat operace služby a jak zavřít klienta po dokončení volání operace.

```csharp
using System;
using Microsoft.ServiceBus;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
            ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;


            Console.Write("Your Service Namespace: ");
            string serviceNamespace = Console.ReadLine();
            Console.Write("Your SAS Key: ");
            string sasKey = Console.ReadLine();



            Uri serviceUri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");

            TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
            sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);

            ChannelFactory<IEchoChannel> channelFactory = new ChannelFactory<IEchoChannel>("RelayEndpoint", new EndpointAddress(serviceUri));

            channelFactory.Endpoint.Behaviors.Add(sasCredential);

            IEchoChannel channel = channelFactory.CreateChannel();
            channel.Open();

            Console.WriteLine("Enter text to echo (or [Enter] to exit):");
            string input = Console.ReadLine();
            while (input != String.Empty)
            {
                try
                {
                    Console.WriteLine("Server echoed: {0}", channel.Echo(input));
                }
                catch (Exception e)
                {
                    Console.WriteLine("Error: " + e.Message);
                }
                input = Console.ReadLine();
            }

            channel.Close();
            channelFactory.Close();

        }
    }
}
```

## <a name="run-the-applications"></a>Spuštění aplikací

1. Chcete-li vytvořit řešení, vyberte Ctrl+Shift+B. Tato akce vytvoří projekt klienta i projekt služby, který jste vytvořili v předchozích krocích.
1. Než spustíte klientskou aplikaci, musíte se ujistit, že aplikace služby běží. V **Průzkumníku řešení**klepněte pravým tlačítkem myši na řešení **EchoService** a vyberte příkaz **Vlastnosti**.
1. V **aplikaci Property Pages**zvolte Projekt**spuštění** **běžných vlastností** > a pak zvolte **Více projektů po spuštění**. Ujistěte se, že se **EchoService** v seznamu objeví jako první.
1. V poli **Akce** u projektů **EchoService** i **EchoClient** nastavte **Start**.

    ![Stránky vlastností projektu][5]

1. Vyberte **položku Závislosti projektu**. V **seznamu Projekty**vyberte **možnost EchoClient**. Pro **Závisí na**, ujistěte se, že Je **vybrána EchoService.**

    ![Závislosti projektu][6]

1. Chcete-li zavřít **stránky vlastností,** vyberte **ok** .
1. Chcete-li spustit oba projekty, vyberte možnost F5.
1. Obě okna konzoly se otevřou a požádají vás o zadání oboru názvů. Služba musí být spuštěna jako první, takže v okně **konzoly EchoService** zadejte obor názvů a pak vyberte Enter.
1. V dalším případě vás konzole vyzve k zadání klíče SAS. Zadejte klíč SAS a vyberte Enter.

    Tady je příklad výstupu z okna konzoly. Hodnoty zde jsou pouze příklady.

    `Your Service Namespace: myNamespace`

    `Your SAS Key: <SAS key value>`

    Aplikace služby do okna konzoly vypíše adresu, na které naslouchá, jak je vidět na následujícím příkladu.

    `Service address: sb://mynamespace.servicebus.windows.net/EchoService/`

    `Press [Enter] to exit`

1. V okně konzoly **EchoClient** zadejte stejný údaj, který jste zadali pro aplikaci služby. Zadejte stejné hodnoty názvů služby a hodnoty klíčů SAS pro klientskou aplikaci.
1. Po zadání těchto hodnot klient otevře kanál ke službě a zobrazí se výzva k zadání nějakého textu, jak je vidět v následujícím příkladu výstupu konzoly.

    `Enter text to echo (or [Enter] to exit):`

    Zadejte nějaký text, který chcete odeslat do aplikace služby, a vyberte Enter. Tento text se odešle do služby pomocí operace služby Echo a objeví se v okně konzoly služby, jak je vidět v následujícím příkladu výstupu.

    `Echoing: My sample text`

    Klientská aplikace obdrží hodnotu vrácenou z operace `Echo`. Tou je původní text, který se vypíše do okna konzoly. Následující text je příklad výstupu z okna klientské konzole.

    `Server echoed: My sample text`

1. Tímto způsobem můžete dál posílat textové zprávy z klienta do služby. Až budete hotovi, vyberte Zadat v oknech klienta a konzoly služby, abyste ukončili obě aplikace.

## <a name="next-steps"></a>Další kroky

Přejděte k následujícímu kurzu:

> [!div class="nextstepaction"]
>[Zveřejnění místní služby WCF REST pro klienta mimo vaši síť](service-bus-relay-rest-tutorial.md)

[2]: ./media/service-bus-relay-tutorial/configure-echoservice-console-app.png
[3]: ./media/service-bus-relay-tutorial/install-nuget-service-bus.png
[4]: ./media/service-bus-relay-tutorial/install-nuget-service-bus-client.png
[5]: ./media/service-bus-relay-tutorial/set-projects.png
[6]: ./media/service-bus-relay-tutorial/set-depend.png
