---
title: Rychlý start k používání Azure Redis Cache s aplikacemi .NET Core | Microsoft Docs
description: V tomto rychlém startu zjistíte, jak používat Azure Redis Cache s aplikacemi .NET Core
services: redis-cache,app-service
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 05/18/2018
ms.author: wesmc
ms.custom: mvc
ms.openlocfilehash: 63bab0e5c77204ea1e122c32a508e31ed9bcd114
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34660499"
---
# <a name="quickstart-use-azure-redis-cache-with-a-net-core-app"></a>Rychlý start: Použití Azure Redis Cache s aplikací .NET Core



Tento rychlý start vám ukáže, jak začít používat Microsoft Azure Redis Cache s .NET Core. Služba Microsoft Azure Redis Cache je založená na populární Open Source mezipaměti Redis Cache. Umožňuje vám přístup do zabezpečené, vyhrazené mezipaměti Redis spravované Microsoftem. Mezipaměť vytvořená pomocí Azure Redis Cache je přístupná ze všech aplikací v rámci Microsoft Azure.

V tomto rychlém startu budete používat klienta [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) s kódem jazyka C\# v aplikaci konzoly .NET Core. Vytvoříte mezipaměť a nakonfigurujete klientskou aplikaci .NET Core. Potom v této mezipaměti provedete přidání a aktualizaci objektů. 

K dokončení kroků v tomto rychlém startu můžete použít jakýkoli editor kódu. Skvělou volbou je však editor [Visual Studio Code](https://code.visualstudio.com/), který je dostupný pro platformy Windows, macOS a Linux.

![Dokončená konzolová aplikace](./media/cache-dotnet-core-quickstart/cache-console-app-complete.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

* [.Net SDK 2.0](https://www.microsoft.com/net/learn/get-started/windows) nebo novější.
* Klient StackExchange.Redis vyžaduje rozhraní [.NET Framework 4 nebo vyšší](https://www.microsoft.com/net/download/dotnet-framework-runtime).

## <a name="create-a-cache"></a>Vytvoření mezipaměti
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-access-keys](../../includes/redis-cache-access-keys.md)]

Poznamenejte si **NÁZEV HOSTITELE** a **Primární** přístupový klíč. Tyto hodnoty později použijete k vytvoření tajného kódu *CacheConnection*.



## <a name="create-a-console-app"></a>Vytvoření konzolové aplikace

Otevřete nové okno příkazového řádku a spuštěním následujícího příkazu vytvořte novou konzolovou aplikaci .NET Core:

```
dotnet new console -o Redistest
```

V okně příkazového řádku přejděte do nového adresáře projektu *Redistest*.



## <a name="add-secret-manager-to-the-project"></a>Přidání nástroje Secret Manager do projektu

V této části do svého projektu přidáte [nástroj Secret Manager](https://docs.microsoft.com/aspnet/core/security/app-secrets). Nástroj Secret Manager ukládá citlivá data související s vývojem mimo strom vašeho projektu. Tento přístup pomáhá zabránit náhodnému sdílení tajných kódů aplikace ve zdrojovém kódu.

Otevřete soubor *Redistest.csproj*. Přidejte element `DotNetCliToolReference` zahrnující *Microsoft.Extensions.SecretManager.Tools*. Přidejte také element `UserSecretsId`, jak je znázorněno níže, a uložte soubor.

```xml
<Project Sdk="Microsoft.NET.Sdk">

  <PropertyGroup>
    <OutputType>Exe</OutputType>
    <TargetFramework>netcoreapp2.0</TargetFramework>
    <UserSecretsId>Redistest</UserSecretsId>
  </PropertyGroup>
  <ItemGroup>
    <DotNetCliToolReference Include="Microsoft.Extensions.SecretManager.Tools" Version="2.0.0" />
  </ItemGroup>
</Project>
```

Spuštěním následujícího příkazu přidejte k projektu balíček *Microsoft.Extensions.Configuration.UserSecrets*:

```
dotnet add package Microsoft.Extensions.Configuration.UserSecrets
```

Spuštěním následujícího příkazu balíčky obnovte:

```
dotnet restore
```

Po nahrazení zástupných znaků (včetně ostrých závorek) názvu mezipaměti a primárního přístupového klíče spusťte v okně příkazové řádku následující příkaz, který uloží nový tajný kód s názvem *CacheConnection*:

```
dotnet user-secrets set CacheConnection "<cache name>.redis.cache.windows.net,abortConnect=false,ssl=true,password=<primary-access-key>"
```

K souboru *Program.cs* přidejte následující příkaz `using`:

```csharp
using Microsoft.Extensions.Configuration;
```

K třídě `Program` v souboru *Program.cs* přidejte následující členy. Tento kód inicializuje konfiguraci pro udělení přístupu k tajnému kódu uživatele pro připojovací řetězec mezipaměti redis.

```csharp
        private static IConfigurationRoot Configuration { get; set; }
        const string SecretName = "CacheConnection";

        private static void InitializeConfiguration()
        {
            var builder = new ConfigurationBuilder()
                .AddUserSecrets<Program>();

            Configuration = builder.Build();
        }
```

## <a name="configure-the-cache-client"></a>Konfigurace klienta mezipaměti

V této části nakonfigurujete konzolovou aplikaci, aby pro .NET používala klienta [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis).

V okně příkazového řádku spusťte v adresáři projektu *Redistest* následující příkaz:

```
dotnet add package StackExchange.Redis
```

Po dokončení instalace budete moct se svým projektem používat klienta mezipaměti *StackExchange.Redis*.


## <a name="connect-to-the-cache"></a>Připojení k mezipaměti

K souboru *Program.cs* přidejte následující příkaz `using`:

```csharp
using StackExchange.Redis;
```

Připojení k Azure Redis Cache spravuje třída `ConnectionMultiplexer`. Tato třída by se měla v rámci klientské aplikace sdílet a opakovaně používat. Nevytvářejte pro každou operaci nové připojení. 

V souboru *Program.cs* přidejte k třídě `Program` konzolové aplikace následující členy:

```csharp
        private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
        {
            string cacheConnection = Configuration[SecretName];
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

Tento přístup ke sdílení instance `ConnectionMultiplexer` v aplikaci používá statickou vlastnost, která vrací připojenou instanci. Tento kód poskytuje způsob inicializace pouze jedné připojené instance `ConnectionMultiplexer`, který je bezpečný pro přístup z více vláken. Hodnota `abortConnect` je nastavená na false, což znamená, že volání bude úspěšné i v případě, že nedojde k vytvoření připojení k Azure Redis Cache. Klíčovou vlastností `ConnectionMultiplexer` je automatické obnovení připojení k mezipaměti po vyřešení problémů se sítí nebo jiných příčin.

Hodnota tajného kódu *CacheConnection* se používá jako parametr hesla a je možné k ní získat přístup použitím zprostředkovatele konfigurace Secret Manager.

## <a name="executing-cache-commands"></a>Provádění příkazů mezipaměti

V souboru *Program.cs* přidejte pro proceduru `Program` třídy konzolové aplikace `Main` následující kód:

```csharp
        static void Main(string[] args)
        {
            InitializeConfiguration();

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

            cacheCommand = "SET Message \"Hello! The cache is working from a .NET Core console app!\"";
            Console.WriteLine("\nCache command  : " + cacheCommand + " or StringSet()");
            Console.WriteLine("Cache response : " + cache.StringSet("Message", "Hello! The cache is working from a .NET Core console app!").ToString());

            // Demostrate "SET Message" executed as expected...
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

Soubor *Program.cs* uložte.

Mezipaměti Azure Redis Cache mají konfigurovatelný počet databází (výchozí je 16), který lze použít k logickému oddělení dat v rámci mezipaměti Redis. Kód se připojí k výchozí databázi DB 0. Další informace najdete v tématu [Co jsou databáze Redis?](cache-faq.md#what-are-redis-databases) a [Výchozí konfigurace serveru Redis](cache-configure.md#default-redis-server-configuration).

Položky v mezipaměti lze ukládat a načítat pomocí metod `StringSet` a `StringGet`.

Redis ukládá většinu dat jako řetězce Redis, ale tyto řetězce mohou obsahovat mnoho typů dat, včetně serializovaných binárních dat, která lze použít při ukládání objektů .NET v mezipaměti.

Vytvořte aplikaci spuštěním následujícího příkazu v okně příkazového řádku:

```
dotnet build
```

Potom aplikaci tímto příkazem spusťte:

```
dotnet run
```

V níže uvedeném příkladu můžete vidět, že klíč `Message` měl předtím hodnotu z mezipaměti, která byla nastavena pomocí konzoly Redis na webu Azure Portal. Aplikace tuto hodnotu z mezipaměti aktualizovala. Aplikace rovněž spustila příkazy `PING` a `CLIENT LIST`.

![Částečně provedený kód konzolové aplikace](./media/cache-dotnet-core-quickstart/cache-console-app-partial.png)


## <a name="work-with-net-objects-in-the-cache"></a>Práce s objekty .NET v mezipaměti

Azure Redis Cache může do mezipaměti ukládat objekty .NET i primitivní datové typy. Objekty .NET je však nutné před uložením do mezipaměti serializovat. Serializaci objektů .NET má na starosti vývojář aplikace, kterému je tak poskytnuta flexibilita při výběru serializátoru.

Jeden snadný způsob, jak serializovat objekty, je použít metody serializace `JsonConvert` v balíčku [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/) a serializovat a deserializovat tak objekty do a z formátu JSON. V této části přidáte do mezipaměti objekt .NET.

Spuštěním následujícího příkazu přidejte do aplikace balíček *Newtonsoft.json*:

```
dotnet add package Newtonsoft.json
```

Na začátek souboru *Program.cs* přidejte následující příkaz `using`:

```charp
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

Soubor *Program.cs* uložte a pomocí následujícího příkazu aplikaci znovu sestavte:

```
dotnet build
```

Aplikaci spusťte tímto příkazem a otestujte serializaci objektů .NET:

```
dotnet run
```

![Dokončená konzolová aplikace](./media/cache-dotnet-core-quickstart/cache-console-app-complete.png)


## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud budete pokračovat k dalšímu kurzu, můžete prostředky vytvořené v tomto rychlém startu zachovat a znovu je použít.

V opačném případě, pokud jste už s ukázkovou aplikací v tomto rychlém startu skončili, můžete prostředky Azure vytvořené v tomto rychlém startu odstranit, abyste se vyhnuli poplatkům. 

> [!IMPORTANT]
> Odstranění skupiny prostředků je nevratné a skupina prostředků včetně všech v ní obsažených prostředků bude trvale odstraněna. Ujistěte se, že nechtěně neodstraníte nesprávnou skupinu prostředků nebo prostředky. Pokud jste vytvořili prostředky pro hostování této ukázky ve stávající skupině prostředků obsahující prostředky, které chcete zachovat, můžete místo odstranění skupiny prostředků odstranit jednotlivé prostředky z jejich odpovídajících oken.
>

Přihlaste se na web [Azure Portal ](https://portal.azure.com) a klikněte na **Skupiny prostředků**.

Do textového pole **Filtrovat podle názvu** zadejte název vaší skupiny prostředků. V pokynech v tomto článku se používala skupina prostředků *TestResources*. Ve výsledcích hledání klikněte na **...** u vaší skupiny prostředků a pak na **Odstranit skupinu prostředků**.

![Odstranění](./media/cache-dotnet-core-quickstart/cache-delete-resource-group.png)

Zobrazí se výzva k potvrzení odstranění skupiny prostředků. Potvrďte odstranění zadáním názvu vaší skupiny prostředků a klikněte na **Odstranit**.

Po chvíli bude skupina prostředků včetně všech obsažených prostředků odstraněná.



<a name="next-steps"></a>

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se naučili, jak používat službu Azure Redis Cache z aplikace .NET Core. V dalším rychlém startu se můžete naučit používat službu Redis Cache s webovou aplikací ASP.NET.

> [!div class="nextstepaction"]
> [Vytvořte webovou aplikaci ASP.NET, která používá službu Azure Redis Cache.](./cache-web-app-howto.md)




