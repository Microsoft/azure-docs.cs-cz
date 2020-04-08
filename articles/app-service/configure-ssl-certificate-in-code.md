---
title: Použití certifikátu TLS/SSL v kódu
description: Přečtěte si, jak používat klientské certifikáty ve vašem kódu. Ověřte pomocí vzdálených prostředků pomocí klientského certifikátu nebo s nimi spouštějte kryptografické úlohy.
ms.topic: article
ms.date: 11/04/2019
ms.reviewer: yutlin
ms.custom: seodec18
ms.openlocfilehash: d76bac60bae11f0843d81de523030154af62a373
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2020
ms.locfileid: "80811692"
---
# <a name="use-a-tlsssl-certificate-in-your-code-in-azure-app-service"></a>Použití certifikátu TLS/SSL ve vašem kódu ve službě Azure App Service

V kódu aplikace můžete přistupovat k [veřejným nebo soukromým certifikátům, které přidáte do služby App Service](configure-ssl-certificate.md). Kód aplikace může fungovat jako klient a přístup k externí službě, která vyžaduje ověření certifikátu, nebo může být nutné provádět kryptografické úlohy. Tento návod ukazuje, jak používat veřejné nebo soukromé certifikáty v kódu aplikace.

Tento přístup k používání certifikátů ve vašem kódu využívá funkce TLS ve službě App Service, která vyžaduje, aby vaše aplikace byla na úrovni **Basic** nebo vyšší. Pokud je vaše aplikace na **bezplatné** nebo **sdílené** úrovni, můžete [soubor certifikátu zahrnout do úložiště aplikací](#load-certificate-from-file).

Když službě App Service umožníte spravovat certifikáty TLS/SSL, můžete certifikáty a kód aplikace udržovat samostatně a chránit citlivá data.

## <a name="prerequisites"></a>Požadavky

Postupujte podle tohoto návodu:

- [Vytvořit aplikaci App Service](/azure/app-service/)
- [Přidání certifikátu do aplikace](configure-ssl-certificate.md)

## <a name="find-the-thumbprint"></a>Najít otisk palce

Na <a href="https://portal.azure.com" target="_blank">webu Azure Portal</a>vyberte v levé nabídce název aplikace **App Services** > **\<>**.

V levém navigačním panelu aplikace vyberte **nastavení TLS/SSL**a pak vyberte **certifikáty soukromého klíče (.pfx)** nebo **certifikáty veřejného klíče (.cer)**.

Najděte certifikát, který chcete použít, a zkopírujte kryptografický otisk.

![Kopírování kryptografického otisku certifikátu](./media/configure-ssl-certificate/create-free-cert-finished.png)

## <a name="make-the-certificate-accessible"></a>Zpřístupnění certifikátu

Chcete-li získat přístup k certifikátu v `WEBSITE_LOAD_CERTIFICATES` kódu aplikace, přidejte jeho kryptografický otisk do nastavení aplikace spuštěním následujícího příkazu v <a target="_blank" href="https://shell.azure.com" >prostředí Cloud Shell</a>:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_CERTIFICATES=<comma-separated-certificate-thumbprints>
```

Chcete-li zpřístupnit všechny certifikáty, `*`nastavte hodnotu na .

## <a name="load-certificate-in-windows-apps"></a>Načtení certifikátu v aplikacích pro Windows

Nastavení `WEBSITE_LOAD_CERTIFICATES` aplikace zpřístupní zadané certifikáty vaší aplikaci hostované pro Windows v úložišti certifikátů Windows a umístění závisí na [cenové úrovni](overview-hosting-plans.md):

- **Izolovaná** úroveň - v [místním počítači\My](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores). 
- Všechny ostatní úrovně - v [aktuálním uživateli\Můj](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores).

V kódu jazyka C# získáte přístup k certifikátu pomocí kryptografického otisku certifikátu. Následující kód načte certifikát s `E661583E8FABEF4C0BEF694CBC41C28FB81CD870`kryptografickým otiskem .

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

V kódu Javy přistupujete k certifikátu z úložiště Windows-MY pomocí pole Předmět běžného názvu (viz [Certifikát veřejného klíče).](https://en.wikipedia.org/wiki/Public_key_certificate) Následující kód ukazuje, jak načíst certifikát soukromého klíče:

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

Jazyky, které nepodporují nebo nenabízejí dostatečnou podporu úložiště certifikátů windows, najdete v [tématu Načtení certifikátu ze souboru](#load-certificate-from-file).

## <a name="load-certificate-in-linux-apps"></a>Načíst certifikát v aplikacích pro Linux

Nastavení `WEBSITE_LOAD_CERTIFICATES` aplikace zpřístupní zadané certifikáty vašim linuxovým hostovaným aplikacím (včetně vlastních aplikací kontejnerů) jako souborům. Soubory se nacházejí pod následujícími adresáři:

- Soukromé certifikáty `/var/ssl/private` `.p12` - ( soubory)
- Veřejné certifikáty `/var/ssl/certs` `.der` - ( soubory)

Názvy souborů certifikátu jsou kryptografické otisky certifikátu. Následující kód Jazyka C# ukazuje, jak načíst veřejný certifikát v aplikaci pro Linux.

```csharp
using System;
using System.IO;
using System.Security.Cryptography.X509Certificates;

...
var bytes = File.ReadAllBytes("/var/ssl/certs/<thumbprint>.der");
var cert = new X509Certificate2(bytes);

// Use the loaded certificate
```

Postup načtení certifikátu TLS/SSL ze souboru v souboru Node.js, PHP, Pythonu, Javě nebo Ruby, naleznete v dokumentaci k příslušnému jazyku nebo webové platformě.

## <a name="load-certificate-from-file"></a>Načíst certifikát ze souboru

Pokud potřebujete načíst soubor certifikátu, který nahrajete ručně, je lepší nahrát certifikát pomocí [FTPS](deploy-ftp.md) namísto [Gitu](deploy-local-git.md). Citlivá data byste měli uchovávat jako soukromý certifikát mimo shodu zdrojového kódu.

> [!NOTE]
> ASP.NET a ASP.NET Core v systému Windows musí přistupovat k úložišti certifikátů, i když načtete certifikát ze souboru. Chcete-li načíst soubor certifikátu v aplikaci windows .NET, načtěte aktuální profil uživatele pomocí následujícího příkazu v <a target="_blank" href="https://shell.azure.com" >prostředí Cloud :</a>
>
> ```azurecli-interactive
> az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_USER_PROFILE=1
> ```
>
> Tento přístup k používání certifikátů ve vašem kódu využívá funkce TLS ve službě App Service, která vyžaduje, aby vaše aplikace byla na úrovni **Basic** nebo vyšší.

Následující příklad Jazyka C# načte veřejný certifikát z relativní cesty ve vaší aplikaci:

```csharp
using System;
using System.IO;
using System.Security.Cryptography.X509Certificates;

...
var bytes = File.ReadAllBytes("~/<relative-path-to-cert-file>");
var cert = new X509Certificate2(bytes);

// Use the loaded certificate
```

Postup načtení certifikátu TLS/SSL ze souboru v souboru Node.js, PHP, Pythonu, Javě nebo Ruby, naleznete v dokumentaci k příslušnému jazyku nebo webové platformě.

## <a name="more-resources"></a>Další zdroje informací

* [Zabezpečení vlastního názvu DNS pomocí vazby TLS/SSL ve službě Azure App Service](configure-ssl-bindings.md)
* [Vynucení HTTPS](configure-ssl-bindings.md#enforce-https)
* [Vynucení protokolu TLS 1.1/1.2](configure-ssl-bindings.md#enforce-tls-versions)
* [Časté otázky: Certifikáty služby App Service](https://docs.microsoft.com/azure/app-service/faq-configuration-and-management/)
