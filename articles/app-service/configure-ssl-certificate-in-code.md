---
title: Použít certifikát SSL v kódu-Azure App Service | Microsoft Docs
description: Naučte se používat klientské certifikáty pro připojení ke vzdáleným prostředkům, které je vyžadují.
services: app-service
documentationcenter: ''
author: cephalin
manager: gwallace
editor: ''
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: cephalin
ms.reviewer: yutlin
ms.custom: seodec18
ms.openlocfilehash: 1546ded1977e1e26792189e1d992d106d3d77ef2
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2019
ms.locfileid: "74271284"
---
# <a name="use-an-ssl-certificate-in-your-code-in-azure-app-service"></a>Použijte certifikát SSL v kódu v Azure App Service

V kódu aplikace můžete získat přístup k [veřejným nebo soukromým certifikátům, které přidáte do App Service](configure-ssl-certificate.md). Kód vaší aplikace může fungovat jako klient a přistupovat k externí službě, která vyžaduje ověření certifikátu, nebo může být potřeba provádět kryptografické úlohy. Tato příručka ukazuje, jak použít veřejné nebo privátní certifikáty ve vašem kódu aplikace.

Tento přístup k používání certifikátů v kódu využívá funkci SSL v App Service, která vyžaduje, aby vaše aplikace byla v úrovni **Basic** nebo vyšší. Pokud je vaše aplikace na úrovni **Free** nebo **Shared** , můžete do [úložiště aplikace zahrnout soubor certifikátu](#load-certificate-from-file).

Když necháte App Service spravovat certifikáty SSL, můžete spravovat certifikáty a kód aplikace samostatně a chránit citlivá data.

## <a name="prerequisites"></a>Požadavky

Postup při použití tohoto průvodce:

- [Vytvořit aplikaci App Service](/azure/app-service/)
- [Přidání certifikátu do aplikace](configure-ssl-certificate.md)

## <a name="find-the-thumbprint"></a>Najít kryptografický otisk

V <a href="https://portal.azure.com" target="_blank">Azure Portal</a>v nabídce vlevo vyberte **App Services** >  **\<název aplikace >** .

V levém navigačním panelu aplikace vyberte **Nastavení TLS/SSL**, pak vyberte **certifikáty privátních klíčů (. pfx)** nebo **certifikáty veřejného klíče (. cer)** .

Vyhledejte certifikát, který chcete použít, a zkopírujte jeho kryptografický otisk.

![Kopírování kryptografického otisku certifikátu](./media/configure-ssl-certificate/create-free-cert-finished.png)

## <a name="make-the-certificate-accessible"></a>Zpřístupněte certifikát jako přístupný.

Pokud chcete získat přístup k certifikátu v kódu aplikace, přidejte jeho kryptografický otisk do nastavení aplikace `WEBSITE_LOAD_CERTIFICATES` spuštěním následujícího příkazu v <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_CERTIFICATES=<comma-separated-certificate-thumbprints>
```

Pro zpřístupnění všech certifikátů nastavte hodnotu na `*`.

## <a name="load-certificate-in-windows-apps"></a>Načtení certifikátu v aplikacích pro Windows

Nastavení aplikace `WEBSITE_LOAD_CERTIFICATES` zpřístupňuje zadané certifikáty pro aplikaci hostovanou v systému Windows v úložišti certifikátů Windows a umístění závisí na [cenové úrovni](overview-hosting-plans.md):

- **Izolovaná** vrstva – [místní Machine\My](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores) 
- Všechny ostatní úrovně – v [aktuální User\My](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores).

V C# kódu získáte přístup k certifikátu pomocí kryptografického otisku certifikátu. Následující kód načte certifikát s kryptografickým otiskem `E661583E8FABEF4C0BEF694CBC41C28FB81CD870`.

```csharp
using System;
using System.Security.Cryptography.X509Certificates;

...
X509Store certStore = new X509Store(StoreName.My, StoreLocation.CurrentUser);
certStore.Open(OpenFlags.ReadOnly);
X509Certificate2Collection certCollection = certStore.Certificates.Find(
                            X509FindType.FindByThumbprint,
                            // Replace below with your certificate's thumbprint
                            "E661583E8FABEF4C0BEF694CBC41C28FB81CD870",
                            false);
// Get the first cert with the thumbprint
if (certCollection.Count > 0)
{
    X509Certificate2 cert = certCollection[0];
    // Use certificate
    Console.WriteLine(cert.FriendlyName);
}
certStore.Close();
...
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

## <a name="load-certificate-in-linux-apps"></a>Načíst certifikát v aplikacích pro Linux

Nastavení aplikace `WEBSITE_LOAD_CERTIFICATES` zpřístupňuje zadané certifikáty pro hostované aplikace Linux (včetně vlastních kontejnerových aplikací) jako soubory. Soubory se nacházejí v následujících adresářích:

- Privátní certifikáty – `/var/ssl/private` (soubory `.p12`)
- Veřejné certifikáty – `/var/ssl/certs` (soubory `.der`)

Názvy souborů certifikátů jsou kryptografické otisky certifikátů. Následující C# kód ukazuje, jak načíst veřejný certifikát v aplikaci pro Linux.

```csharp
using System;
using System.Security.Cryptography.X509Certificates;

...
var bytes = System.IO.File.ReadAllBytes("/var/ssl/certs/<thumbprint>.der");
var cert = new X509Certificate2(bytes);

// Use the loaded certificate
```

Pokud chcete zjistit, jak načíst certifikát SSL ze souboru v Node. js, PHP, Pythonu, Java nebo Ruby, přečtěte si dokumentaci k příslušnému jazyku nebo webové platformě.

## <a name="load-certificate-from-file"></a>Načíst certifikát ze souboru

Pokud potřebujete načíst soubor certifikátu, který nahrajete ručně, je vhodnější nahrát certifikát pomocí [FTPS](deploy-ftp.md) místo [Gitu](deploy-local-git.md), například. Měli byste chránit citlivá data, jako je soukromý certifikát, ze správy zdrojového kódu.

> [!NOTE]
> ASP.NET a ASP.NET Core ve Windows musí mít přístup k úložišti certifikátů, i když načtete certifikát ze souboru. Pokud chcete načíst soubor certifikátu v aplikaci Windows .NET, načtěte aktuální profil uživatele pomocí následujícího příkazu v <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>:
>
> ```azurecli-interactive
> az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_USER_PROFILE=1
> ```
>
> Tento přístup k používání certifikátů v kódu využívá funkci SSL v App Service, která vyžaduje, aby vaše aplikace byla v úrovni **Basic** nebo vyšší.

Následující C# příklad načte veřejný certifikát z relativní cesty ve vaší aplikaci:

```csharp
using System;
using System.Security.Cryptography.X509Certificates;

...
var bytes = System.IO.File.ReadAllBytes("~/<relative-path-to-cert-file>");
var cert = new X509Certificate2(bytes);

// Use the loaded certificate
```

Pokud chcete zjistit, jak načíst certifikát SSL ze souboru v Node. js, PHP, Pythonu, Java nebo Ruby, přečtěte si dokumentaci k příslušnému jazyku nebo webové platformě.

## <a name="more-resources"></a>Další zdroje informací

* [Zabezpečení vlastního názvu DNS pomocí vazby SSL](configure-ssl-bindings.md)
* [Vynucení HTTPS](configure-ssl-bindings.md#enforce-https)
* [Vynutilit protokol TLS 1.1/1.2](configure-ssl-bindings.md#enforce-tls-versions)
* [Nejčastější dotazy: App Service certifikátů](https://docs.microsoft.com/azure/app-service/faq-configuration-and-management/)
