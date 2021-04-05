---
title: Použití certifikátu TLS/SSL v kódu
description: Naučte se používat klientské certifikáty ve vašem kódu. Ověřování pomocí klientských certifikátů pomocí vzdálených prostředků, nebo s nimi spouštějte kryptografické úlohy.
ms.topic: article
ms.date: 09/22/2020
ms.reviewer: yutlin
ms.custom: seodec18
ms.openlocfilehash: b4e184f827875ebebd40ab976ef63e77ee702d49
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "93126035"
---
# <a name="use-a-tlsssl-certificate-in-your-code-in-azure-app-service"></a>Použití certifikátu TLS nebo SSL v kódu ve službě Azure App Service

V kódu aplikace můžete získat přístup k [veřejným nebo soukromým certifikátům, které přidáte do App Service](configure-ssl-certificate.md). Kód vaší aplikace může fungovat jako klient a přistupovat k externí službě, která vyžaduje ověření certifikátu, nebo může být potřeba provádět kryptografické úlohy. Tato příručka ukazuje, jak použít veřejné nebo privátní certifikáty ve vašem kódu aplikace.

Tento přístup k používání certifikátů v kódu využívá funkci TLS v App Service, která vyžaduje, aby vaše aplikace byla v úrovni **Basic** nebo vyšší. Pokud je vaše aplikace na úrovni **Free** nebo **Shared** , můžete do [úložiště aplikace zahrnout soubor certifikátu](#load-certificate-from-file).

Když necháte App Service spravovat certifikáty TLS/SSL, můžete spravovat certifikáty a kód aplikace samostatně a chránit citlivá data.

## <a name="prerequisites"></a>Požadavky

Postup při použití tohoto průvodce:

- [Vytvořit aplikaci App Service](./index.yml)
- [Přidání certifikátu do aplikace](configure-ssl-certificate.md)

## <a name="find-the-thumbprint"></a>Najít kryptografický otisk

V <a href="https://portal.azure.com" target="_blank">Azure Portal</a>v nabídce vlevo vyberte **App Services**  >  **\<app-name>** .

V levém navigačním panelu aplikace vyberte **Nastavení TLS/SSL**, pak vyberte **certifikáty privátních klíčů (. pfx)** nebo **certifikáty veřejného klíče (. cer)**.

Vyhledejte certifikát, který chcete použít, a zkopírujte jeho kryptografický otisk.

![Kopírování kryptografického otisku certifikátu](./media/configure-ssl-certificate/create-free-cert-finished.png)

## <a name="make-the-certificate-accessible"></a>Zpřístupněte certifikát jako přístupný.

Pokud chcete získat přístup k certifikátu v kódu aplikace, přidejte jeho kryptografický otisk do `WEBSITE_LOAD_CERTIFICATES` nastavení aplikace spuštěním následujícího příkazu v <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_CERTIFICATES=<comma-separated-certificate-thumbprints>
```

Pro zpřístupnění všech certifikátů nastavte hodnotu na `*` .

## <a name="load-certificate-in-windows-apps"></a>Načtení certifikátu v aplikacích pro Windows

`WEBSITE_LOAD_CERTIFICATES`Nastavení aplikace zpřístupňuje zadané certifikáty pro aplikaci hostovanou v systému Windows v úložišti certifikátů Windows v [aktuální User\My](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores).

V kódu jazyka C# získáte přístup k certifikátu pomocí kryptografického otisku certifikátu. Následující kód načte certifikát s kryptografickým otiskem `E661583E8FABEF4C0BEF694CBC41C28FB81CD870` .

```csharp
using System;
using System.Linq;
using System.Security.Cryptography.X509Certificates;

string certThumbprint = "E661583E8FABEF4C0BEF694CBC41C28FB81CD870";
bool validOnly = false;

using (X509Store certStore = new X509Store(StoreName.My, StoreLocation.CurrentUser))
{
  certStore.Open(OpenFlags.ReadOnly);

  X509Certificate2Collection certCollection = certStore.Certificates.Find(
                              X509FindType.FindByThumbprint,
                              // Replace below with your certificate's thumbprint
                              certThumbprint,
                              validOnly);
  // Get the first cert with the thumbprint
  X509Certificate2 cert = certCollection.OfType<X509Certificate>().FirstOrDefault();

  if (cert is null)
      throw new Exception($"Certificate with thumbprint {certThumbprint} was not found");

  // Use certificate
  Console.WriteLine(cert.FriendlyName);
  
  // Consider to call Dispose() on the certificate after it's being used, avaliable in .NET 4.6 and later
}
```

V kódu Java získáte přístup k certifikátu z úložiště "Windows-MY" pomocí pole běžný název subjektu (viz [certifikát veřejného klíče](https://en.wikipedia.org/wiki/Public_key_certificate)). Následující kód ukazuje, jak načíst certifikát privátního klíče:

```java
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.bind.annotation.RequestMapping;
import java.security.KeyStore;
import java.security.cert.Certificate;
import java.security.PrivateKey;

...
KeyStore ks = KeyStore.getInstance("Windows-MY");
ks.load(null, null); 
Certificate cert = ks.getCertificate("<subject-cn>");
PrivateKey privKey = (PrivateKey) ks.getKey("<subject-cn>", ("<password>").toCharArray());

// Use the certificate and key
...
```

Jazyky, které nepodporují nebo neposkytují nedostatečnou podporu úložiště certifikátů Windows, najdete v tématu [načtení certifikátu ze souboru](#load-certificate-from-file).

## <a name="load-certificate-from-file"></a>Načíst certifikát ze souboru

Pokud potřebujete načíst soubor certifikátu, který nahrajete ručně, je vhodnější nahrát certifikát pomocí [FTPS](deploy-ftp.md) místo [Gitu](deploy-local-git.md), například. Měli byste chránit citlivá data, jako je soukromý certifikát, ze správy zdrojového kódu.

> [!NOTE]
> ASP.NET a ASP.NET Core ve Windows musí mít přístup k úložišti certifikátů, i když načtete certifikát ze souboru. Pokud chcete načíst soubor certifikátu v aplikaci Windows .NET, načtěte aktuální profil uživatele pomocí následujícího příkazu v <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>:
>
> ```azurecli-interactive
> az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_USER_PROFILE=1
> ```
>
> Tento přístup k používání certifikátů v kódu využívá funkci TLS v App Service, která vyžaduje, aby vaše aplikace byla v úrovni **Basic** nebo vyšší.

Následující příklad jazyka C# načte veřejný certifikát z relativní cesty ve vaší aplikaci:

```csharp
using System;
using System.IO;
using System.Security.Cryptography.X509Certificates;

...
var bytes = File.ReadAllBytes("~/<relative-path-to-cert-file>");
var cert = new X509Certificate2(bytes);

// Use the loaded certificate
```

Pokud chcete zjistit, jak načíst certifikát TLS/SSL ze souboru v Node.js, PHP, Pythonu, Java nebo Ruby, přečtěte si dokumentaci k příslušnému jazyku nebo webové platformě.

## <a name="load-certificate-in-linuxwindows-containers"></a>Načíst certifikát v kontejnerech systému Linux/Windows

`WEBSITE_LOAD_CERTIFICATES`Nastavení aplikace zpřístupňuje zadané certifikáty vašim kontejnerovým aplikacím pro Windows nebo Linux (včetně integrovaných kontejnerů Linux) jako souborů. Soubory se nacházejí v následujících adresářích:

| Platforma kontejneru | Veřejné certifikáty | Privátní certifikáty |
| - | - | - |
| Kontejner Windows | `C:\appservice\certificates\public` | `C:\appservice\certificates\private` |
| Kontejner Linuxu | `/var/ssl/certs` | `/var/ssl/private` |

Názvy souborů certifikátů jsou kryptografické otisky certifikátů. 

> [!NOTE]
> Cesty k certifikátům App Service vložit do kontejnerů Windows jako následující proměnné prostředí `WEBSITE_PRIVATE_CERTS_PATH` ,, `WEBSITE_INTERMEDIATE_CERTS_PATH` `WEBSITE_PUBLIC_CERTS_PATH` a `WEBSITE_ROOT_CERTS_PATH` . Je lepší odkazovat na cestu k certifikátu pomocí proměnných prostředí místo zakódujeme cesty k certifikátu pro případ, že se cesty certifikátů v budoucnu nezmění.
>

Kromě toho [základní kontejnery Windows serveru](configure-custom-container.md#supported-parent-images) načtou certifikáty do úložiště certifikátů automaticky v **úložišti LocalMachine\MY**. Pokud chcete načíst certifikáty, použijte stejný vzor jako [načíst certifikát v aplikacích pro Windows](#load-certificate-in-windows-apps). Pro kontejnery založené na Windows nano použijte cesty k souborům uvedené výše k [načtení certifikátu přímo ze souboru](#load-certificate-from-file).

Následující kód jazyka C# ukazuje, jak načíst veřejný certifikát v aplikaci pro Linux.

```csharp
using System;
using System.IO;
using System.Security.Cryptography.X509Certificates;

...
var bytes = File.ReadAllBytes("/var/ssl/certs/<thumbprint>.der");
var cert = new X509Certificate2(bytes);

// Use the loaded certificate
```

Pokud chcete zjistit, jak načíst certifikát TLS/SSL ze souboru v Node.js, PHP, Pythonu, Java nebo Ruby, přečtěte si dokumentaci k příslušnému jazyku nebo webové platformě.

## <a name="more-resources"></a>Další zdroje informací

* [Zabezpečení vlastního názvu DNS s vazbou TLS/SSL v Azure App Service](configure-ssl-bindings.md)
* [Vynucení protokolu HTTPS](configure-ssl-bindings.md#enforce-https)
* [Vynucení protokolu TLS 1.1/1.2](configure-ssl-bindings.md#enforce-tls-versions)
* [Nejčastější dotazy: App Service certifikátů](./faq-configuration-and-management.md)