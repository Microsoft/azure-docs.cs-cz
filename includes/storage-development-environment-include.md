---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 09/15/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 075e19ecb078bbbd5be0d46ed58405b18ebc8334
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2018
ms.locfileid: "45738463"
---
## <a name="set-up-your-development-environment"></a>Nastavení vývojového prostředí
Potom si nastavte vývojové prostředí v sadě Visual Studio, abyste byli připraveni vyzkoušet příklady kódů z této příručky.

### <a name="create-a-windows-console-application-project"></a>Vytvoření projektu konzolové aplikace pro Windows
V sadě Visual Studio vytvořte novou konzolovou aplikaci pro Windows. Následující kroky ukazují, jak vytvořit konzolovou aplikaci v sadě Visual Studio 2017. Kroky u ostatních verzí sady Visual Studio jsou podobné.

1. Vyberte **Soubor** > **Nový** > **Projekt**.
2. Vyberte **Instalováno** > **Šablony** > **Visual C#** > **Klasická plocha Windows**.
3. Vyberte **Aplikace konzoly (.NET Framework)**.
4. Do pole **Název** zadejte název vaší aplikace.
5. Vyberte **OK**.

![Snímek dialogového okna Nový projekt v sadě Visual Studio](./media/storage-development-environment-include/storage-development-environment-include-1.png)

Všechny příklady kódu v tomto kurzu můžete přidat do metody `Main()` v souboru `Program.cs` vaší konzolové aplikace.

Můžete použít knihovnu klienta služby Azure Storage z libovolného typu aplikace .NET, včetně webové aplikace nebo cloudové služby Azure, desktopové nebo mobilní aplikace. V této příručce použijeme konzolovou aplikaci kvůli zjednodušení.

### <a name="use-nuget-to-install-the-required-packages"></a>Použití balíčku NuGet k instalaci požadovaných balíčků
Abyste mohli tento kurz dokončit, potřebujete ze svého projektu odkazovat na dva balíčky:

* [Microsoft Azure Storage Client Library for .NET](https://www.nuget.org/packages/WindowsAzure.Storage/): Tento balíček zajišťuje programový přístup k datovým prostředkům na účtu úložiště.
* [Microsoft Azure Configuration Manager library for .NET:](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/) Tento balíček poskytuje třídu pro potřeby analýzy připojovacího řetězce v konfiguračním souboru bez ohledu na to, kde je aplikace spuštěná.

K získání obou balíčků můžete použít balíček NuGet. Postupujte následovně:

1. V **Průzkumníku řešení** klikněte pravým tlačítkem na projekt a vyberte **Spravovat balíčky NuGet**.
2. Online vyhledejte text „WindowsAzure.Storage“, vyberte **Instalovat** a nainstalujte balíček knihovny klienta úložiště a jeho závislosti.
3. Proveďte online hledání textu „WindowsAzure.ConfigurationManager“ a výběrem **Instalovat** nainstalujete správce konfigurace Azure.

> [!NOTE]
> Balíček knihovny klienta služby Storage je také součástí [sady Azure SDK for .NET](https://azure.microsoft.com/downloads/). Přesto doporučujeme, abyste si knihovnu klienta služby Storage nainstalovali také z balíčku NuGet, protože tím si zajistíte nejnovější verzi knihovny klienta.
> 
> Závislosti ODataLib v knihovně klienta služby Storage pro .NET jsou vyřešené prostřednictvím balíčků ODataLib, které jsou dostupné na NuGetu, a ne prostřednictvím služby WCF Data Services. Knihovny ODataLib můžete stáhnout přímo nebo z odkazu ve vašem kódovém projektu prostřednictvím balíčku NuGet. Konkrétní balíčky ODataLib používané knihovnou klienta služby Storage jsou [OData](http://nuget.org/packages/Microsoft.Data.OData/), [Edm](http://nuget.org/packages/Microsoft.Data.Edm/), a [Spatial](http://nuget.org/packages/System.Spatial/). I když tyto knihovny používají třídy Azure Table Storage, představují požadované závislosti pro programování s knihovnou klienta úložiště.
> 
> 

### <a name="determine-your-target-environment"></a>Určení cílového prostředí
Ke spuštění příkladů z této příručky máte dvě možnosti prostředí:

* Svůj kód můžete spustit na účtu služby Azure Storage v cloudu. 
* Svůj kód můžete spustit v emulátoru úložiště Azure. Emulátor úložiště je místní prostředí, které emuluje účet služby Azure Storage v cloudu. Emulátor je bezplatnou možností pro testování a ladění kódu během vývoje aplikace. Emulátor používá známý účet a klíč. Další informace najdete v článku [Použití emulátoru úložiště Azure pro vývoj a testování](../articles/storage/common/storage-use-emulator.md).

Pokud se zaměřujete na účet úložiště v cloudu, zkopírujte z webu Azure Portal primární přístupový klíč svého účtu úložiště. Další informace najdete v části [Přístupové klíče](../articles/storage/common/storage-account-manage.md#access-keys).

> [!NOTE]
> Pokud se chcete vyhnout nákladům spojeným se službou Azure Storage, můžete se zaměřit na emulátor úložiště. I když se zaměříte na účet úložiště Azure v cloudu, budou náklady na vyzkoušení postupů z tohoto kurzu zanedbatelné.
> 
> 

### <a name="configure-your-storage-connection-string"></a>Konfigurace připojovacího řetězce úložiště
Knihovna klienta služby Azure Storage pro .NET podporuje použití připojovacího řetězce úložiště ke konfiguraci koncových bodů a přihlašovacích údajů pro přístup ke službám úložiště. Připojovací řetězec úložiště se nejlépe uchovává v konfiguračním souboru. 

Další informace o připojovacích řetězcích najdete v tématu věnovaném [konfiguraci připojovacího řetězce pro službu Azure Storage](../articles/storage/common/storage-configure-connection-string.md).

> [!NOTE]
> Klíč účtu úložiště je podobný kořenovému heslu vašeho účtu úložiště. Vždy klíč účtu úložiště pečlivě chraňte. Nedávejte ho jiným uživatelům, nezakódovávejte ho ani ho neukládejte do souboru ve formátu prostého textu, který je přístupný ostatním uživatelům. Pokud se domníváte, že klíč je ohrožený, vygenerujte ho znovu pomocí webu Azure Portal.
> 
> 

Pokud chcete konfigurovat připojovací řetězec, otevřete v sadě Visual Studio Průzkumníka řešení a v něm soubor `app.config`. Přidejte obsah níže uvedeného prvku `<appSettings>`. `account-name` nahraďte názvem svého účtu úložiště a místo `account-key` zadejte svůj klíč účtu úložiště:

```xml
<configuration>
    <startup> 
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.2" />
    </startup>
    <appSettings>
        <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key" />
    </appSettings>
</configuration>
```

Nastavení konfigurace může vypadat následovně:

```xml
<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=GMuzNHjlB3S9itqZJHHCnRkrokLkcSyW7yK9BRbGp0ENePunLPwBgpxV1Z/pVo9zpem/2xSHXkMqTHHLcx8XRA==" />
```

Pokud se chcete zaměřit na emulátor úložiště, můžete vytvořit zástupce, který se namapuje na název a klíč známého účtu. V takovém případě bude nastavení připojovacího řetězce vypadat následovně:

```xml
<add key="StorageConnectionString" value="UseDevelopmentStorage=true;" />
```

