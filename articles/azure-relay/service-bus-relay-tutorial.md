---
title: Zveřejnění služby REST Prem WCF klientům pomocí Azure Relay
description: V tomto kurzu se dozvíte, jak vystavit místní službu WCF REST pro externího klienta pomocí Azure WCF Relay.
ms.topic: tutorial
ms.custom: devx-track-dotnet
ms.date: 06/23/2020
ms.openlocfilehash: 7669bc07ad91933cd31bd2ccd10eaf830d98de7c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101710783"
---
# <a name="tutorial-expose-an-on-premises-wcf-rest-service-to-external-client-by-using-azure-wcf-relay"></a>Kurz: zpřístupnění místní služby WCF REST externímu klientovi pomocí Azure WCF Relay

V tomto kurzu se dozvíte, jak vytvořit klientskou aplikaci WCF Relay a službu pomocí Azure Relay. Podobný kurz, který používá [Service Bus zasílání zpráv](../service-bus-messaging/service-bus-messaging-overview.md), najdete v tématu [Začínáme s Service Bus fronty](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md).

Při práci v tomto kurzu získáte informace o postupu při vytváření WCF Relaych aplikací klienta a služby. Stejně jako jejich původní protějšky WCF je služba konstrukce, která zveřejňuje jeden nebo více koncových bodů. Každý koncový bod zveřejňuje jednu nebo více operací služby. Koncový bod služby specifikuje adresu, kde se dá služba najít, vazbu, která obsahuje informaci, že klient musí komunikovat se službou, a kontrakt, který definuje funkci, kterou služba klientovi poskytuje. Hlavním rozdílem mezi WCF a WCF Relay je, že se koncový bod zveřejňuje v cloudu, ne místně na vašem počítači.

Po absolvování posloupnosti oddílů v tomto kurzu budete mít běžící službu. Také budete mít klienta, který může vyvolat operace služby. 

V tomto kurzu provedete následující úlohy:

> [!div class="checklist"]
>
> * Nainstalovat požadavky pro tento kurz.
> * Vytvořte obor názvů Relay.
> * Vytvoření kontraktu služby WCF.
> * Implementujte kontrakt WCF.
> * Hostování a spuštění služby WCF pro registraci ve službě přenosu.
> * Vytvořte klienta WCF pro kontrakt služby.
> * Nakonfigurujte klienta WCF.
> * Implementujte klienta WCF.
> * Spusťte aplikace.

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu musí být splněné následující požadavky:

* Předplatné Azure. Pokud ho ještě nemáte, [Vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
* [Visual Studio 2015 nebo novější](https://www.visualstudio.com). V příkladech v tomto kurzu se používá Visual Studio 2019.
* Sada Azure SDK pro .NET Nainstalujte ji ze [stránky pro stažení sady SDK](https://azure.microsoft.com/downloads/).

## <a name="create-a-relay-namespace"></a>Vytvoření oboru názvů Relay

Prvním krokem je vytvoření oboru názvů a získání klíče [sdíleného přístupového podpisu (SAS)](../service-bus-messaging/service-bus-sas.md) . Obor názvů poskytuje hranici aplikace pro každou aplikaci vystavenou prostřednictvím předávací služby. Klíč SAS se automaticky generuje systémem při vytvoření oboru názvů služby. Kombinace oboru názvů služby a klíče SAS poskytuje přihlašovací údaje pro Azure, které ověřují přístup k aplikaci.

[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="define-a-wcf-service-contract"></a>Definování kontraktu služby WCF

Kontrakt služby určuje, které operace služba podporuje. Operace jsou metody nebo funkce webové služby. Kontrakty se vytvoří definováním základního rozhraní C++, C# nebo Visual Basic. Každá metoda v rozhraní odpovídá konkrétní operaci služby. Na každé rozhraní musí mít aplikovaný atribut [ServiceContractAttribute](/dotnet/api/system.servicemodel.servicecontractattribute) a na každou operace musí byt aplikovaný atribut [OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute). Pokud metoda v rozhraní, které má atribut [ServiceContractAttribute](/dotnet/api/system.servicemodel.servicecontractattribute) , nemá atribut [OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute) , tato metoda není vystavena. Kód k těmto úlohám najdete v příkladu za postupem. Větší diskuzi o smlouvách a službách najdete v tématu [navrhování a implementace služeb](/dotnet/framework/wcf/designing-and-implementing-services).

### <a name="create-a-relay-contract-with-an-interface"></a>Vytvoření kontraktu služby Relay s rozhraním

1. Spusťte Microsoft Visual Studio jako správce. Provedete to tak, že kliknete pravým tlačítkem na ikonu programu Visual Studio a vyberete **Spustit jako správce**.
1. V aplikaci Visual Studio vyberte možnost **vytvořit nový projekt**.
1. V možnosti **vytvořit nový projekt** zvolte **Konzolová aplikace (.NET Framework)** pro C# a vyberte **Další**.
1. Pojmenujte projekt *EchoService* a vyberte **vytvořit**.

   ![Vytvoření konzolové aplikace][2]

1. V **Průzkumník řešení** klikněte pravým tlačítkem na projekt a vyberte **Spravovat balíčky NuGet**. V okně **Správce balíčků NuGet** vyberte **Procházet**, vyhledejte a vyberte **windowsazure. ServiceBus**. Vyberte **nainstalovat** a přijměte podmínky použití.

    ![Balíček Service Bus][3]

   Tento balíček automaticky přidá odkazy na knihovny Service Bus a WCF `System.ServiceModel` . [System.ServiceModel](/dotnet/api/system.servicemodel) je obor názvů, který vám umožňuje programový přístup k základním funkcím WCF. Service Bus používá mnoho objektů a atributů WCF k definování kontraktů služby.

1. Přidejte následující `using` příkazy do horní části *programu. cs*:

    ```csharp
    using System.ServiceModel;
    using Microsoft.ServiceBus;
    ```

1. Změňte název oboru názvů z výchozího názvu `EchoService` na `Microsoft.ServiceBus.Samples`.

   > [!IMPORTANT]
   > V tomto kurzu se používá obor názvů C# `Microsoft.ServiceBus.Samples` , který je oborem názvů spravovaného typu založeného na kontraktu, který se používá v konfiguračním souboru v části [Konfigurace klienta služby WCF](#configure-the-wcf-client) . Můžete určit libovolný obor názvů, který chcete při sestavování této ukázky použít. Tento kurz ale nebude fungovat, pokud pak v konfiguračním souboru aplikace neupravíte obory názvů kontraktu a služby odpovídajícím způsobem. Obor názvů zadaný v souboru *App.config* musí být stejný jako obor názvů zadaný v souborech jazyka C#.
   >

1. Přímo po `Microsoft.ServiceBus.Samples` deklaraci oboru názvů, ale v rámci oboru názvů, definujte nové rozhraní s názvem `IEchoContract` a použijte `ServiceContractAttribute` atribut na rozhraní s hodnotou oboru názvů `https://samples.microsoft.com/ServiceModel/Relay/` . Vložte následující kód za deklaraci oboru názvů:

    ```csharp
    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
    }
    ```

    Hodnota oboru názvů se liší od oboru názvů, které používáte v celém svém kódu. Místo toho se obor názvů používá jako jedinečný identifikátor pro tento kontrakt. Když explicitně zadáte obor názvů, zabráníte tím přidání výchozí hodnoty oboru názvů do názvu kontraktu.

   > [!NOTE]
   > Obor názvů kontraktu služby obvykle obsahuje schéma pojmenování s informacemi o verzi. Informace o verzi, které jsou v oboru názvů kontraktu služby, službám umožňuje službám izolovat výrazné změny pomocí definice nové služby s novým oborem názvů, která bude vystavená na novém koncovém bodu. Tímto způsobem můžou klienti dál používat původní kontrakt služby, aniž by se museli aktualizovat. Informace o verzi může mít podobu data nebo čísla sestavení. Další informace najdete v článku o [Správa verzí služeb](/dotnet/framework/wcf/service-versioning). Pro účely tohoto kurzu schéma pojmenování oboru názvů kontraktu služby neobsahuje informace o verzi.
   >

1. V rámci `IEchoContract` rozhraní deklarujte metodu pro jedinou operaci, kterou `IEchoContract` kontrakt zpřístupňuje v rozhraní, a použijte `OperationContractAttribute` atribut pro metodu, kterou chcete zveřejnit jako součást kontraktu veřejné WCF Relay, a to následujícím způsobem:

    ```csharp
    [OperationContract]
    string Echo(string text);
    ```

1. Přímo po definici rozhraní `IEchoContract` deklarujte kanál, který zdědí vlastnosti z obou `IEchoContract` a také do rozhraní `IClientChannel`, jak je vidět tady:

    ```csharp
    public interface IEchoChannel : IEchoContract, IClientChannel { }
    ```

    Kanál je objekt WCF, kterým si hostitel a klient navzájem posílají informace. Později do kanálu napíšete kód, který bude zobrazovat informace o těchto dvou aplikacích.

1. Vyberte **sestavení** sestavení buildu  >   nebo vyberte CTRL + SHIFT + B a potvrďte přesnost své dosavadní práce.

### <a name="example-of-a-wcf-contract"></a>Příklad smlouvy WCF

Následující kód ukazuje základní rozhraní, které definuje kontrakt WCF Relay.

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

## <a name="implement-the-wcf-contract"></a>Implementace kontraktu WCF

Vytvoření služby Azure Relay vyžaduje, abyste nejdřív vytvořili kontrakt pomocí rozhraní. Další informace o vytváření rozhraní najdete v předchozí části. Další postup implementuje rozhraní. Tato úloha zahrnuje vytvoření třídy s názvem `EchoService` , která implementuje uživatelsky definované `IEchoContract` rozhraní. Po implementaci rozhraní je třeba nakonfigurovat rozhraní pomocí konfiguračního souboru *App.config* . Konfigurační soubor obsahuje informace potřebné pro aplikaci. Tyto informace zahrnují název služby, název kontraktu a typ protokolu, který se používá ke komunikaci se službou Relay. Kód použitý pro tyto úlohy je k dispozici v příkladu, který následuje po tomto postupu. Obecnější diskuzi o implementaci kontraktu služby najdete v tématu [implementace kontraktů](/dotnet/framework/wcf/implementing-service-contracts)služeb.

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

1. Vyberte **sestavit** sestavení  >  **řešení** nebo vyberte CTRL + SHIFT + B.

### <a name="define-the-configuration-for-the-service-host"></a>Definujte konfiguraci pro hostitele služby.

Konfigurační soubor je podobný konfiguračnímu souboru WCF. Obsahuje název služby, koncový bod a vazbu. Koncový bod je umístění Azure Relay zveřejňuje klienty a hostitele ke vzájemné komunikaci. Vazba je typ protokolu, který se používá ke komunikaci. Hlavním rozdílem je, že tento nakonfigurovaný koncový bod služby odkazuje na vazbu [NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) , která není součástí .NET Framework. [NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) je jednou z vazeb definovaných službou.

1. V **Průzkumník řešení** dvakrát klikněte na **App.config** a otevřete soubor v editoru sady Visual Studio.
1. V elementu `<appSettings>` nahraďte zástupné texty názvem svého oboru názvů a klíčem SAS, který jste zkopírovali v jednom z předchozích kroků.
1. Ve značkách `<system.serviceModel>` přidejte element `<services>`. V jednom konfiguračním souboru můžete definovat několik aplikací Relay. V tomto kurzu se ale definuje jen jedna.

    ```xml
    <?xmlversion="1.0" encoding="utf-8"?>
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

    Koncový bod definuje, kde bude klient hledat hostitelskou aplikaci. Později kurz používá tento krok k vytvoření identifikátoru URI, který plně zpřístupňuje hostitele prostřednictvím Azure Relay. Vazba deklaruje, že používáme protokol TCP jako protokol pro komunikaci se službou Relay.

1. Vyberte **sestavení** sestavení buildu  >   nebo vyberte CTRL + SHIFT + B a potvrďte přesnost své dosavadní práce.

### <a name="example-of-implementation-of-a-service-contract"></a>Příklad implementace kontraktu služby

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

Následující kód ukazuje základní formát *App.config* souboru přidruženého k hostiteli služby.

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

## <a name="host-and-run-the-wcf-service-to-register-with-the-relay-service"></a>Hostování a spuštění služby WCF pro registraci u služby Relay

Tento krok popisuje, jak spustit službu Azure Relay.

### <a name="create-the-relay-credentials"></a>Vytvoření přihlašovacích údajů pro předávání

1. V `Main()` vytvořte dvě proměnné, do kterých se uloží obor názvů a klíč SAS načtené z okna konzoly.

    ```csharp
    Console.Write("Your Service Namespace: ");
    string serviceNamespace = Console.ReadLine();
    Console.Write("Your SAS key: ");
    string sasKey = Console.ReadLine();
    ```

    Klíč SAS bude později použit pro přístup k projektu. Obor názvů se předá do `CreateServiceUri` jako parametr a vytvoří se URI služby.

1. Pomocí objektu [TransportClientEndpointBehavior](/dotnet/api/microsoft.servicebus.transportclientendpointbehavior) deklarujete, že jako typ přihlašovacích údajů použijete klíč SAS. Následující kód přidejte přímo za kód, který jste přidali v předchozím kroku.

    ```csharp
    TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
    sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);
    ```

### <a name="create-a-base-address-for-the-service"></a>Vytvoření základní adresy pro službu

Po kódu, který jste přidali v předchozí části, vytvořte `Uri` instanci pro základní adresu služby. Toto URI specifikuje schéma Service Bus, obor názvů a cestu rozhraní služby.

```csharp
Uri address = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");
```

Hodnota "SB" je zkratkou schématu Service Bus. Označuje, že jako protokol používáme protokol TCP. Toto schéma bylo také dříve uvedeno v konfiguračním souboru, pokud byl [NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) zadán jako vazba.

V tomto kurzu je URI `sb://putServiceNamespaceHere.windows.net/EchoService`.

### <a name="create-and-configure-the-service-host"></a>Vytvoření a konfigurace hostitele služby

1. Pořád pracujete v `Main()` , nastavte režim připojení na `AutoDetect` .

    ```csharp
    ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;
    ```

    Režim připojení popisuje protokol, který služba používá ke komunikaci se službou Relay. buď HTTP, nebo TCP. Když použijete výchozí nastavení `AutoDetect` , služba se pokusí připojit k Azure Relay přes TCP, pokud je k dispozici, a http, pokud není k dispozici protokol TCP. Tento výsledek se liší od protokolu, který služba určuje pro komunikaci klientů. Jeho protokol se určuje podle požité vazby. Služba může například používat vazbu [BasicHttpRelayBinding](/dotnet/api/microsoft.servicebus.basichttprelaybinding) , která určuje, že jeho koncový bod komunikuje s klienty přes protokol HTTP. Tato služba by mohla určit, aby `ConnectivityMode.AutoDetect` Služba komunikovala s Azure Relay přes TCP.

1. Vytvořte hostitele služby pomocí URI, které jste předtím vytvořili v této části.

    ```csharp
    ServiceHost host = new ServiceHost(typeof(EchoService), address);
    ```

    Hostitel služby je objekt WCF, který instancuje službu. Zde předáte typ služby, kterou chcete vytvořit, `EchoService` typ a také adresu, na které chcete službu zveřejnit.

1. V horní části souboru *program. cs* přidejte odkazy na [System. ServiceModel. Description](/dotnet/api/system.servicemodel.description) a [Microsoft. ServiceBus. Description](/dotnet/api/microsoft.servicebus.description).

    ```csharp
    using System.ServiceModel.Description;
    using Microsoft.ServiceBus.Description;
    ```

1. Zpátky v `Main()` nakonfigurujte koncový bod, a povolte tak veřejný přístup.

    ```csharp
    IEndpointBehavior serviceRegistrySettings = new ServiceRegistrySettings(DiscoveryType.Public);
    ```

    Tento krok informuje službu Relay o tom, že se vaše aplikace dá najít veřejně, a prozkoumáním informačního kanálu Atom pro váš projekt. Pokud nastavíte `DiscoveryType` na `private` , bude mít klient stále přístup ke službě. Služba se ale při hledání v `Relay` oboru názvů nezobrazí. Místo toho by klient musel předem znát cestu ke koncovému bodu.

1. Použijte pověření služby pro koncové body služby definované v souboru *App.config* :

    ```csharp
    foreach (ServiceEndpoint endpoint in host.Description.Endpoints)
    {
        endpoint.Behaviors.Add(serviceRegistrySettings);
        endpoint.Behaviors.Add(sasCredential);
    }
    ```

    Jak bylo uvedeno dříve, mohli jste v konfiguračním souboru deklarovat několik služeb a koncových bodů. Pokud byste to udělali, tento kód by prošel konfigurační soubor a vyhledal by všechny koncové body, na které by měl vaše pověření použít. Pro tento kurz má konfigurační soubor pouze jeden koncový bod.

### <a name="open-the-service-host"></a>Otevření hostitele služby

1. Ještě v nástroji `Main()` přidejte následující řádek pro otevření služby.

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

1. Vyberte CTRL + SHIFT + B a sestavte projekt.

### <a name="example-that-hosts-a-service-in-a-console-application"></a>Příklad, který hostuje službu v konzolové aplikaci

Váš dokončený kód služby by měl vypadat takto. Kód zahrnuje kontrakt a implementaci služby z předchozích kroků v tomto kurzu a hostuje službu v konzolové aplikaci.

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

Další úlohou je vytvoření klientské aplikace a definování kontraktu služby, který budete implementovat později. Tyto kroky se podobají postupům, které se používají k vytvoření služby: Definování kontraktu, úpravy souboru *App.config* , použití přihlašovacích údajů pro připojení ke službě Relay a tak dále. Kód použitý k těmto úlohám najdete v příkladu za postupem.

1. Vytvořit nový projekt v aktuálním řešení sady Visual Studio pro klienta:

   1. V **Průzkumník řešení** klikněte pravým tlačítkem na aktuální řešení (ne na projekt) a vyberte **Přidat**  >  **Nový projekt**.
   1. V poli **Přidat nový projekt** vyberte **Konzolová aplikace (.NET Framework)** pro C# a vyberte **Další**.
   1. Název projektu *EchoClient* a vyberte **vytvořit**.

1. V **Průzkumník řešení** v projektu **EchoClient** dvakrát klikněte na **program. cs** , aby se soubor otevřel v editoru, pokud už není otevřený.
1. Změňte název oboru názvů z výchozího názvu `EchoClient` na `Microsoft.ServiceBus.Samples`.
1. Instalace [balíčku Service Bus NuGet](https://www.nuget.org/packages/WindowsAzure.ServiceBus):

   1. V **Průzkumník řešení** klikněte pravým tlačítkem na **EchoClient** a pak vyberte **Spravovat balíčky NuGet**.
   1. Vyberte **Procházet**, vyhledejte a vyberte **windowsazure. ServiceBus**. Vyberte **nainstalovat** a přijměte podmínky použití.

      ![Nainstalovat balíček služby Service Bus][4]

1. `using`Do souboru *program. cs* přidejte příkaz pro obor názvů [System. ServiceModel](/dotnet/api/system.servicemodel) .

    ```csharp
    using System.ServiceModel;
    ```

1. Přidejte definici kontraktu služby do oboru názvů, jak je vidět v následujícím příkladu. Tato definice je shodná s definicí použitou v projektu **služby** . Přidejte tento kód na začátek `Microsoft.ServiceBus.Samples` oboru názvů.

    ```csharp
    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        string Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }
    ```

1. Vyberte CTRL + SHIFT + B a sestavte klienta.

### <a name="example-of-the-echoclient-project"></a>Příklad projektu EchoClient

Následující kód ukazuje aktuální stav souboru *program. cs* v projektu **EchoClient** .

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

V tomto kroku vytvoříte soubor *App.config* pro základní klientskou aplikaci, která přistupuje ke službě vytvořené dříve v tomto kurzu. Tento *App.config* soubor definuje kontrakt, vazbu a název koncového bodu. Kód použitý k těmto úlohám najdete v příkladu za postupem.

1. V **Průzkumník řešení** v projektu **EchoClient** dvakrát klikněte na **App.config** a otevřete soubor v editoru sady Visual Studio.
1. V elementu `<appSettings>` nahraďte zástupné texty názvem svého oboru názvů a klíčem SAS, který jste zkopírovali v jednom z předchozích kroků.
1. V rámci `system.serviceModel` elementu přidejte `<client>` element.

    ```xml
    <?xmlversion="1.0" encoding="utf-8"?>
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

    Tento kód definuje název koncového bodu. Definuje také kontrakt definovaný ve službě a skutečnost, že klientská aplikace používá protokol TCP ke komunikaci s Azure Relay. Název koncového bodu se použije v následujícím kroku k propojení této konfigurace koncového bodu s URI služby.

1. Vyberte **soubor**  >  **Uložit vše**.

### <a name="example-of-the-appconfig-file"></a>Příklad souboru App.config

Následující kód ukazuje soubor *App.config* pro klienta echo.

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

V této části implementujete základní klientskou aplikaci, která přistupuje ke službě, kterou jste vytvořili dříve v tomto kurzu. Podobně jako u služby má klient mnoho stejných operací pro přístup k Azure Relay:

* Nastaví režim připojení.
* Vytvoří URI, které vyhledá hostitelskou službu.
* Definuje bezpečnostní pověření.
* Aplikuje pověření na připojení.
* Otevře připojení.
* Provádí úlohy specifické pro aplikace.
* Ukončí připojení.

Jedním z hlavních rozdílů však je, že klientská aplikace používá pro připojení ke službě Relay kanál. Služba používá volání **třídy ServiceHost**. Kód použitý k těmto úlohám najdete v příkladu za postupem.

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

1. Vytvořte identifikátor URI, který definuje umístění hostitele ve vašem projektu Relay.

    ```csharp
    Uri serviceUri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");
    ```

1. Vytvořte objekt pověření pro koncový bod vašeho oboru názvů.

    ```csharp
    TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
    sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);
    ```

1. Vytvořte objekt pro vytváření kanálů, který načte konfiguraci popsanou v souboru *App.config* .

    ```csharp
    ChannelFactory<IEchoChannel> channelFactory = new ChannelFactory<IEchoChannel>("RelayEndpoint", new EndpointAddress(serviceUri));
    ```

    Objekt kanálu pro vytváření je objekt WCF, který vytvoří kanál, přes který může služba komunikovat s klientskými aplikacemi.

1. Použijte přihlašovací údaje.

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

    Kód používá instanci objektu kanálu jako proxy pro službu.

1. Zavřete kanál a zavřete objekt pro vytváření.

    ```csharp
    channel.Close();
    channelFactory.Close();
    ```

### <a name="example-code-for-this-tutorial"></a>Ukázkový kód pro tento kurz

Váš dokončený kód by měl vypadat takto. Tento kód ukazuje, jak vytvořit klientskou aplikaci, jak volat operace služby a jak zavřít klienta po dokončení volání operace.

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

1. Vyberte CTRL + SHIFT + B a sestavte řešení. Tato akce vytvoří projekt klienta i projekt služby, který jste vytvořili v předchozích krocích.
1. Než spustíte klientskou aplikaci, musíte se ujistit, že aplikace služby běží. V **Průzkumník řešení** klikněte pravým tlačítkem na řešení **EchoService** a pak vyberte **vlastnosti**.
1. Na **stránce vlastnosti**, **běžné vlastnosti**  >  **po spuštění projektu** a pak zvolte **více projektů po spuštění**. Ujistěte se, že se **EchoService** v seznamu objeví jako první.
1. V poli **Akce** u projektů **EchoService** i **EchoClient** nastavte **Start**.

    ![Stránky vlastností projektu][5]

1. Vyberte **závislosti projektu**. V **projektech** vyberte **EchoClient**. Pro **závisí na**, ujistěte se, že je vybraná možnost **EchoService** .

    ![Závislosti projektu][6]

1. Kliknutím na **tlačítko OK** zavřete **stránku vlastností**.
1. Vyberte F5 pro spuštění obou projektů.
1. Obě okna konzoly se otevřou a požádají vás o zadání oboru názvů. Služba musí být nejprve spuštěna, takže v okně konzoly **EchoService** zadejte obor názvů a potom vyberte Enter.
1. V dalším kroku se v konzole zobrazí výzva k zadání klíče SAS. Zadejte klíč SAS a vyberte Enter.

    Tady je příklad výstupu z okna konzoly. Zde jsou uvedené hodnoty jenom příklady.

    `Your Service Namespace: myNamespace`

    `Your SAS Key: <SAS key value>`

    Aplikace služby do okna konzoly vypíše adresu, na které naslouchá, jak je vidět na následujícím příkladu.

    `Service address: sb://mynamespace.servicebus.windows.net/EchoService/`

    `Press [Enter] to exit`

1. V okně konzoly **EchoClient** zadejte stejný údaj, který jste zadali pro aplikaci služby. Zadejte stejný obor názvů služby a hodnoty klíče SAS pro klientskou aplikaci.
1. Po zadání těchto hodnot klient otevře kanál ke službě a zobrazí se výzva k zadání nějakého textu, jak je vidět v následujícím příkladu výstupu konzoly.

    `Enter text to echo (or [Enter] to exit):`

    Zadejte nějaký text, který se odešle do aplikace služby, a vyberte Enter. Tento text se odešle do služby pomocí operace služby Echo a objeví se v okně konzoly služby, jak je vidět v následujícím příkladu výstupu.

    `Echoing: My sample text`

    Klientská aplikace obdrží hodnotu vrácenou z operace `Echo`. Tou je původní text, který se vypíše do okna konzoly. Následující text je ukázkový výstup z okna konzoly klienta.

    `Server echoed: My sample text`

1. Tímto způsobem můžete dál posílat textové zprávy z klienta do služby. Až budete hotovi, vyberte zadat v oknech klient a služba pro ukončení obou aplikací.

## <a name="next-steps"></a>Další kroky

Přejděte k následujícímu kurzu:

> [!div class="nextstepaction"]
>[Zveřejnění místní služby WCF REST pro klienta mimo vaši síť](service-bus-relay-rest-tutorial.md)

[2]: ./media/service-bus-relay-tutorial/configure-echoservice-console-app.png
[3]: ./media/service-bus-relay-tutorial/install-nuget-service-bus.png
[4]: ./media/service-bus-relay-tutorial/install-nuget-service-bus-client.png
[5]: ./media/service-bus-relay-tutorial/set-projects.png
[6]: ./media/service-bus-relay-tutorial/set-depend.png
