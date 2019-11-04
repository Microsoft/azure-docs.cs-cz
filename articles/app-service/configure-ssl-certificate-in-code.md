---
title: Použít certifikát SSL v kódu aplikace – Azure App Service | Microsoft Docs
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
ms.date: 10/16/2019
ms.author: cephalin
ms.reviewer: yutlin
ms.custom: seodec18
ms.openlocfilehash: 1f042f72f82d2198472fe81670c697c0c4b28321
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73513689"
---
# <a name="use-an-ssl-certificate-in-your-application-code-in-azure-app-service"></a>Použijte certifikát SSL v kódu aplikace v Azure App Service

Váš kód aplikace App Service může fungovat jako klient a přistupuje k externí službě, která vyžaduje ověření certifikátu. Tato příručka ukazuje, jak použít veřejné nebo privátní certifikáty ve vašem kódu aplikace.

Tento přístup k používání certifikátů v kódu využívá funkci SSL v App Service, která vyžaduje, aby vaše aplikace byla v úrovni **Basic** nebo vyšší. Případně můžete [soubor certifikátu zahrnout do úložiště aplikace](#load-certificate-from-file), ale nejedná se o doporučený postup pro privátní certifikáty.

Když necháte App Service spravovat certifikáty SSL, můžete spravovat certifikáty a kód aplikace samostatně a chránit citlivá data.

## <a name="prerequisites"></a>Předpoklady

Postup při použití tohoto průvodce:

- [Vytvořit aplikaci App Service](/azure/app-service/)
- [Přidání certifikátu do aplikace](configure-ssl-certificate.md)

## <a name="find-the-thumbprint"></a>Najít kryptografický otisk

V <a href="https://portal.azure.com" target="_blank">Azure Portal</a>v nabídce vlevo vyberte **App Services** >  **\<název aplikace >** .

V levém navigačním panelu aplikace vyberte **Nastavení TLS/SSL**, pak vyberte **certifikáty privátních klíčů (. pfx)** nebo **certifikáty veřejného klíče (. cer)** .

Vyhledejte certifikát, který chcete použít, a zkopírujte jeho kryptografický otisk.

![Kopírování kryptografického otisku certifikátu](./media/configure-ssl-certificate/create-free-cert-finished.png)

## <a name="load-the-certificate"></a>Načtení certifikátu

Pokud chcete použít certifikát v kódu aplikace, přidejte jeho kryptografický otisk do nastavení aplikace `WEBSITE_LOAD_CERTIFICATES` spuštěním následujícího příkazu v <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_CERTIFICATES=<comma-separated-certificate-thumbprints>
```

Pro zpřístupnění všech certifikátů nastavte hodnotu na `*`.

> [!NOTE]
> Toto nastavení umístí zadané certifikáty do aktuálního úložiště [User\My](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores) pro většinu cenových úrovní, ale v **izolované** úrovni (tj. aplikace běží v [App Service Environment](environment/intro.md)) umístí certifikáty do [místních Machine\My](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores) . uchovávat.
>

Nakonfigurované certifikáty jsou teď připravené k použití ve vašem kódu.

## <a name="load-the-certificate-in-code"></a>Načíst certifikát v kódu

Jakmile je certifikát přístupný, získáte k němu C# přístup pomocí kryptografického otisku certifikátu. Následující kód načte certifikát s kryptografickým otiskem `E661583E8FABEF4C0BEF694CBC41C28FB81CD870`.

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

<a name="file"></a>
## <a name="load-certificate-from-file"></a>Načíst certifikát ze souboru

Pokud potřebujete načíst soubor certifikátu z adresáře aplikace, je vhodnější ho nahrát pomocí [FTPS](deploy-ftp.md) namísto [Gitu](deploy-local-git.md), například. Měli byste chránit citlivá data, jako je soukromý certifikát, ze správy zdrojového kódu.

I když soubor načítáte přímo do kódu .NET, knihovna stále ověřuje, zda je aktuální profil uživatele načten. Chcete-li načíst aktuální profil uživatele, nastavte nastavení aplikace `WEBSITE_LOAD_USER_PROFILE` pomocí následujícího příkazu v <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_USER_PROFILE=1
```

Po nastavení tohoto nastavení následující C# příklad načte certifikát s názvem `mycert.pfx` z adresáře `certs` úložiště vaší aplikace.

```csharp
using System;
using System.Security.Cryptography.X509Certificates;

...
// Replace the parameter with "~/<relative-path-to-cert-file>".
string certPath = Server.MapPath("~/certs/mycert.pfx");

X509Certificate2 cert = GetCertificate(certPath, signatureBlob.Thumbprint);
...
```

## <a name="more-resources"></a>Další zdroje

* [Zabezpečení vlastního názvu DNS pomocí vazby SSL](configure-ssl-bindings.md)
* [Vynucení HTTPS](configure-ssl-bindings.md#enforce-https)
* [Vynutilit protokol TLS 1.1/1.2](configure-ssl-bindings.md#enforce-tls-versions)
* [Nejčastější dotazy: App Service certifikátů](https://docs.microsoft.com/azure/app-service/faq-configuration-and-management/)
