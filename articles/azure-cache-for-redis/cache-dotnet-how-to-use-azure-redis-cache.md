---
title: Rychlý Start se dozvíte postupy použití mezipaměti Azure Redis s aplikacemi .NET | Dokumentace Microsoftu
description: V tomto rychlém startu zjistěte, jak získat přístup k mezipaměti Azure pro Redis z aplikace .NET
services: azure-cache-for-redis,app-service
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: c502f74c-44de-4087-8303-1b1f43da12d5
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: azure-cache-for-redis
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 05/18/2018
ms.author: wesmc
ms.custom: mvc
ms.openlocfilehash: 6b2ab1ba89fbc2b271ede177a906a1919c14023d
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/18/2018
ms.locfileid: "53557661"
---
# <a name="quickstart-use-azure-cache-for-redis-with-a-net-application"></a>Rychlý start: Použití mezipaměti Azure pro Redis s .NET aplikace



V tomto rychlém startu se dozvíte, jak začít používat Microsoft Azure Cache pro Redis s .NET. Microsoft Azure Cache pro Redis je založená na oblíbené opensourcové mezipaměti Azure pro Redis. Poskytuje přístup k zabezpečené vyhrazené mezipaměti Azure pro Redis, spravovanou microsoftem. Mezipaměť vytvořená pomocí Azure mezipaměti Redis je přístupná z libovolné aplikace v Microsoft Azure.

V tomto rychlém startu budete používat klienta [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) s kódem jazyka C\# v aplikaci konzoly. Vytvoříte mezipaměť a nakonfigurujete klientskou aplikaci .NET. Potom v této mezipaměti provedete přidání a aktualizaci objektů. 

![Dokončená konzolová aplikace](./media/cache-dotnet-how-to-use-azure-redis-cache/cache-console-app-complete.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

* [Visual Studio](https://www.visualstudio.com/downloads/)
* Klient StackExchange.Redis vyžaduje rozhraní [.NET Framework 4 nebo vyšší](https://www.microsoft.com/net/download/dotnet-framework-runtime).

## <a name="create-a-cache"></a>Vytvoření mezipaměti
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-access-keys](../../includes/redis-cache-access-keys.md)]

Vytvořte v počítači soubor s názvem *TajnéKódyMezipaměti.config* a uložte ho do umístění, které se nebude vracet se zdrojovým kódem této ukázkové aplikace. V tomto rychlém startu je soubor *TajnéKódyMezipaměti.config* umístěný zde: *C:\AppSecrets\TajnéKódyMezipaměti.config*.

Upravte soubor *TajnéKódyMezipaměti.config* a přidejte do něj následující obsah:

```xml
<appSettings>
    <add key="CacheConnection" value="<cache-name>.redis.cache.windows.net,abortConnect=false,ssl=true,password=<access-key>"/>
</appSettings>
```

`<cache-name>` nahraďte názvem hostitele mezipaměti.

`<access-key>` nahraďte primárním klíčem mezipaměti.


## <a name="create-a-console-app"></a>Vytvoření konzolové aplikace

V sadě Visual Studio klikněte na **Soubor** > **Nový** > **Projekt**.

V části **Visual C#** klikněte na **Klasická plocha Windows** a pak klikněte na **Konzolová aplikace** a **OK**. Vytvoříte tak novou konzolovou aplikaci.


<a name="configure-the-cache-clients"></a>

## <a name="configure-the-cache-client"></a>Konfigurace klienta mezipaměti

V této části nakonfigurujete konzolovou aplikaci, aby pro .NET používala klienta [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis).

V sadě Visual Studio klikněte na **Nástroje** > **Správce balíčků NuGet** > **Konzola Správce balíčků** a spusťte následující příkaz z okna konzoly Správce balíčků.

```powershell
Install-Package StackExchange.Redis
```

Po dokončení instalace budete moct se svým projektem používat klienta mezipaměti *StackExchange.Redis*.


## <a name="connect-to-the-cache"></a>Připojení k mezipaměti

V sadě Visual Studio otevřete soubor *App.config* a aktualizujte ho tak, aby obsahoval atribut `appSettings` `file` odkazující na soubor *CacheSecrets.config*.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
    <startup> 
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.7.1" />
    </startup>

    <appSettings file="C:\AppSecrets\CacheSecrets.config"></appSettings>  

</configuration>
```

V Průzkumníku řešení klikněte pravým tlačítkem myši na **Odkazy** a klikněte na **Přidat odkaz**. Přidejte odkaz na sestavení **System.Configuration**.

Do souboru *Program.cs* přidejte následující příkazy `using`:

```csharp
using StackExchange.Redis;
using System.Configuration;
```

Spravuje připojení k mezipaměti Azure Redis `ConnectionMultiplexer` třídy. Tato třída by se měla v rámci klientské aplikace sdílet a opakovaně používat. Nevytvářejte pro každou operaci nové připojení. 

Neuchovávejte přihlašovací údaje ve zdrojovém kódu. Pro zjednodušení této ukázky používám pouze konfigurační soubor externích tajných kódů. Lepším přístupem může být použití řešení [Azure Key Vault s certifikáty](https://docs.microsoft.com/rest/api/keyvault/certificate-scenarios).

V souboru *Program.cs* přidejte k třídě `Program` konzolové aplikace následující členy:

```csharp
        private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
        {
            string cacheConnection = ConfigurationManager.AppSettings["CacheConnection"].ToString();
            return ConnectionMultiplexer.Connect(cacheConnection);
        });

        public static ConnectionMultiplexer Connection
        {
            get
            {
                return lazyConnection.Value;
            }
        }
```


Tento přístup ke sdílení instance `ConnectionMultiplexer` v aplikaci používá statickou vlastnost, která vrací připojenou instanci. Tento kód poskytuje způsob inicializace pouze jedné připojené instance `ConnectionMultiplexer`, který je bezpečný pro přístup z více vláken. `abortConnect` je nastavena na hodnotu false, což znamená, že bude volání úspěšné i v případě, že nedojde k připojení do mezipaměti Azure Redis. Klíčovou vlastností `ConnectionMultiplexer` je automatické obnovení připojení k mezipaměti po vyřešení problémů se sítí nebo jiných příčin.

Hodnota *CacheConnection* appSetting se používá k odkazování na připojovací řetězec mezipaměti z webu Azure Portal ve formě parametru hesla.

## <a name="executing-cache-commands"></a>Provádění příkazů mezipaměti

Pro proceduru `Main` třídy `Program` konzolové aplikace přidejte následující kód:

```csharp
        static void Main(string[] args)
        {
            // Connection refers to a property that returns a ConnectionMultiplexer
            // as shown in the previous example.
            IDatabase cache = lazyConnection.Value.GetDatabase();

            // Perform cache operations using the cache object...

            // Simple PING command
            string cacheCommand = "PING";
            Console.WriteLine("\nCache command  : " + cacheCommand);
            Console.WriteLine("Cache response : " + cache.Execute(cacheCommand).ToString());

            // Simple get and put of integral data types into the cache
            cacheCommand = "GET Message";
            Console.WriteLine("\nCache command  : " + cacheCommand + " or StringGet()");
            Console.WriteLine("Cache response : " + cache.StringGet("Message").ToString());

            cacheCommand = "SET Message \"Hello! The cache is working from a .NET console app!\"";
            Console.WriteLine("\nCache command  : " + cacheCommand + " or StringSet()");
            Console.WriteLine("Cache response : " + cache.StringSet("Message", "Hello! The cache is working from a .NET console app!").ToString());

            // Demonstrate "SET Message" executed as expected...
            cacheCommand = "GET Message";
            Console.WriteLine("\nCache command  : " + cacheCommand + " or StringGet()");
            Console.WriteLine("Cache response : " + cache.StringGet("Message").ToString());

            // Get the client list, useful to see if connection list is growing...
            cacheCommand = "CLIENT LIST";
            Console.WriteLine("\nCache command  : " + cacheCommand);
            Console.WriteLine("Cache response : \n" + cache.Execute("CLIENT", "LIST").ToString().Replace("id=", "id="));

            lazyConnection.Value.Dispose();
        }
```

Azure mezipaměť Redis má Konfigurovatelný počet databází (výchozí je 16), které slouží k logickému oddělení dat v rámci Azure Cache pro Redis. Kód se připojí k výchozí databázi DB 0. Další informace najdete v tématu [Co jsou databáze Redis?](cache-faq.md#what-are-redis-databases) a [Výchozí konfigurace serveru Redis](cache-configure.md#default-redis-server-configuration).

Položky v mezipaměti lze ukládat a načítat pomocí metod `StringSet` a `StringGet`.

Redis ukládá většinu dat jako řetězce Redis, ale tyto řetězce mohou obsahovat mnoho typů dat, včetně serializovaných binárních dat, která lze použít při ukládání objektů .NET v mezipaměti.

Stisknutím kombinace kláves **Ctrl+F5** sestavíte a spustíte konzolovou aplikaci.

V níže uvedeném příkladu můžete vidět, že klíč `Message` měl předtím hodnotu z mezipaměti, která byla nastavena pomocí konzoly Redis na webu Azure Portal. Aplikace tuto hodnotu z mezipaměti aktualizovala. Aplikace rovněž spustila příkazy `PING` a `CLIENT LIST`.

![Částečně provedený kód konzolové aplikace](./media/cache-dotnet-how-to-use-azure-redis-cache/cache-console-app-partial.png)


## <a name="work-with-net-objects-in-the-cache"></a>Práce s objekty .NET v mezipaměti

Může do mezipaměti Azure ukládat pro Redis objekty .NET i primitivní datové typy, ale před uložením objektu .NET můžete mezipaměti musí být serializován. Serializaci objektů .NET má na starosti vývojář aplikace, kterému je tak poskytnuta flexibilita při výběru serializátoru.

Jeden snadný způsob, jak serializovat objekty, je použít metody serializace `JsonConvert` v balíčku [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/) a serializovat a deserializovat tak objekty do a z formátu JSON. V této části přidáte do mezipaměti objekt .NET.

V sadě Visual Studio klikněte na **Nástroje** > **Správce balíčků NuGet** > **Konzola Správce balíčků** a spusťte následující příkaz z okna konzoly Správce balíčků.

```powershell
Install-Package Newtonsoft.Json
```

Na začátek souboru *Program.cs* přidejte následující příkaz `using`:

```csharp
using Newtonsoft.Json;
```

Do souboru *Program.cs* přidejte následující definici třídy `Employee`:

```csharp
        class Employee
        {
            public string Id { get; set; }
            public string Name { get; set; }
            public int Age { get; set; }

            public Employee(string EmployeeId, string Name, int Age)
            {
                this.Id = EmployeeId;
                this.Name = Name;
                this.Age = Age;
            }
        }
```

Na konec procedury `Main()` a před volání metody `Dispose()` v souboru *Program.cs* přidejte do mezipaměti následující řádky kódu a získejte serializovaný objekt .NET:

```csharp
            // Store .NET object to cache
            Employee e007 = new Employee("007", "Davide Columbo", 100);
            Console.WriteLine("Cache response from storing Employee .NET object : " + 
                cache.StringSet("e007", JsonConvert.SerializeObject(e007)));

            // Retrieve .NET object from cache
            Employee e007FromCache = JsonConvert.DeserializeObject<Employee>(cache.StringGet("e007"));
            Console.WriteLine("Deserialized Employee .NET object :\n");
            Console.WriteLine("\tEmployee.Name : " + e007FromCache.Name);
            Console.WriteLine("\tEmployee.Id   : " + e007FromCache.Id);
            Console.WriteLine("\tEmployee.Age  : " + e007FromCache.Age + "\n");
```

Stisknutím kombinace kláves **Ctrl+F5** sestavte a spusťte konzolovou aplikaci a otestujte serializaci objektů .NET. 

![Dokončená konzolová aplikace](./media/cache-dotnet-how-to-use-azure-redis-cache/cache-console-app-complete.png)


## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud budete pokračovat k dalšímu kurzu, můžete prostředky vytvořené v tomto rychlém startu zachovat a znovu je použít.

V opačném případě, pokud jste už s ukázkovou aplikací v tomto rychlém startu skončili, můžete prostředky Azure vytvořené v tomto rychlém startu odstranit, abyste se vyhnuli poplatkům. 

> [!IMPORTANT]
> Odstranění skupiny prostředků je nevratné a skupina prostředků včetně všech v ní obsažených prostředků bude trvale odstraněna. Ujistěte se, že nechtěně neodstraníte nesprávnou skupinu prostředků nebo prostředky. Pokud jste vytvořili prostředky pro hostování této ukázky ve stávající skupině prostředků obsahující prostředky, které chcete zachovat, můžete místo odstranění skupiny prostředků odstranit jednotlivé prostředky z jejich odpovídajících oken.
>

Přihlaste se na web [Azure Portal ](https://portal.azure.com) a klikněte na **Skupiny prostředků**.

Do textového pole **Filtrovat podle názvu** zadejte název vaší skupiny prostředků. V pokynech v tomto článku se používala skupina prostředků *TestResources*. Ve výsledcích hledání klikněte na **...** u vaší skupiny prostředků a pak na **Odstranit skupinu prostředků**.

![Odstranění](./media/cache-dotnet-how-to-use-azure-redis-cache/cache-delete-resource-group.png)

Zobrazí se výzva k potvrzení odstranění skupiny prostředků. Potvrďte odstranění zadáním názvu vaší skupiny prostředků a klikněte na **Odstranit**.

Po chvíli bude skupina prostředků včetně všech obsažených prostředků odstraněná.



<a name="next-steps"></a>

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste zjistili, jak používat Azure Cache z aplikace .NET pro Redis. Pokračujte k dalšímu rychlému startu pro účely Azure mezipaměti Redis s webovou aplikaci ASP.NET.

> [!div class="nextstepaction"]
> [Vytvoření webové aplikace ASP.NET využívající Azure Cache pro Redis.](./cache-web-app-howto.md)


