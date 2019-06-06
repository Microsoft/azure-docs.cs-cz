---
title: Použít klientský certifikát SSL v kódu aplikace – Azure App Service | Dokumentace Microsoftu
description: Zjistěte, jak používat klientské certifikáty pro připojení ke vzdáleným prostředkům, které je vyžadují.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/29/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: ead1892062912840c9931ae60d11c90975ad26ac
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2019
ms.locfileid: "66475122"
---
# <a name="use-an-ssl-certificate-in-your-application-code-in-azure-app-service"></a>Použití certifikátu SSL v kódu aplikace ve službě Azure App Service

Tato příručka ukazuje, jak používat veřejné nebo privátní certifikáty v kódu aplikace. Příklad případem použití je, že vaše aplikace přistupuje k externí služby, která vyžaduje ověření certifikátu.

Tento přístup k používání certifikátů v kódu využívá protokol SSL funkcí ve službě App Service, která vyžaduje, aby vaše aplikace v **základní** vrstvy nebo vyšší. Alternativně můžete [zahrnout soubor certifikátu v úložišti aplikace](#load-certificate-from-file), ale není to doporučený postup pro privátní certifikáty.

Pokud umožníte spravovat své certifikáty SSL služby App Service můžete samostatně spravovat certifikáty a kódu aplikace a chránit citlivá data.

## <a name="upload-a-private-certificate"></a>Odeslání privátního certifikátu

Než nahrajete privátní certifikát, ujistěte se, že [splňuje všechny požadavky](app-service-web-tutorial-custom-ssl.md#prepare-a-private-certificate), s tím rozdílem, že nemusí být nakonfigurovány pro ověřování serveru.

Až budete připravení nahrát, spusťte následující příkaz <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>.

```azurecli-interactive
az webapp config ssl upload --name <app-name> --resource-group <resource-group-name> --certificate-file <path-to-PFX-file> --certificate-password <PFX-password> --query thumbprint
```

Zkopírujte kryptografický otisk certifikátu a zobrazit [zpřístupnit certifikát](#make-the-certificate-accessible).

## <a name="upload-a-public-certificate"></a>Odeslat veřejný certifikát

Veřejné certifikáty jsou podporovány v *.cer* formátu. Nahrát veřejný certifikát <a href="https://portal.azure.com" target="_blank">webu Azure portal</a>a přejděte do aplikace.

Klikněte na tlačítko **nastavení SSL** > **veřejných certifikátů (.cer)**  > **nahrát veřejný certifikát** v levém navigačním panelu vaší aplikace.

V **název**, zadejte název certifikátu. V **soubor certifikátu CER**, vyberte soubor CER.

Klikněte na **Odeslat**.

![Odeslat veřejný certifikát](./media/app-service-web-ssl-cert-load/private-cert-upload.png)

Po nahrání certifikátu zkopírujte kryptografický otisk certifikátu a zobrazit [zpřístupnit certifikát](#make-the-certificate-accessible).

## <a name="import-an-app-service-certificate"></a>Import certifikátu služby App Service

Zobrazit [koupě a konfigurace certifikátu SSL pro službu Azure App Service](web-sites-purchase-ssl-web-site.md).

Po importu certifikátu zkopírujte kryptografický otisk certifikátu a zobrazit [zpřístupnit certifikát](#make-the-certificate-accessible).

## <a name="make-the-certificate-accessible"></a>Tento certifikát zpřístupnit

Pokud chcete použít certifikát nahraný nebo importované v kódu vaší aplikace, zajistíte přístupnost s jeho kryptografický otisk `WEBSITE_LOAD_CERTIFICATES` nastavení aplikace, spuštěním následujícího příkazu v <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_CERTIFICATES=<comma-separated-certificate-thumbprints>
```

Zpřístupnit vaše certifikáty, nastavte hodnotu na `*`.

> [!NOTE]
> Toto nastavení umístí zadané certifikáty v [aktuální User\My](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores) úložiště pro většinu cenové úrovně, ale **izolovaný režim** úroveň (to znamená, že se aplikace běží v [služby App Service Environment](environment/intro.md)), umístí certifikáty v [místní Machine\My](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores) ukládat.
>

![Konfigurace nastavení aplikace](./media/app-service-web-ssl-cert-load/configure-app-setting.png)

Jakmile budete hotoví, klikněte na **Uložit**.

Nakonfigurované certifikáty jsou teď připravená k použití ve vašem kódu.

## <a name="load-the-certificate-in-code"></a>Načtení certifikátu v kódu

Jakmile váš certifikát není přístupný, budete přístup v kódu jazyka C# pomocí kryptografického otisku certifikátu. Následující kód načte certifikát s kryptografickým otiskem `E661583E8FABEF4C0BEF694CBC41C28FB81CD870`.

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

Pokud je potřeba načíst soubor certifikátu z adresáře aplikace, je lepší odešlete pomocí [FTPS](deploy-ftp.md) místo [Git](deploy-local-git.md), např. Byste měli mít citlivá data, jako jsou privátní certifikát ze správy zdrojového kódu.

I v případě, že načítání souboru přímo v kódu .NET knihovny stále ověří, pokud je načíst aktuální profil uživatele. Chcete-li načíst aktuální profil uživatele, nastavte `WEBSITE_LOAD_USER_PROFILE` nastavení aplikace pomocí následujícího příkazu v <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_USER_PROFILE=1
```

Jakmile je toto nastavení, následující C# příklad načte certifikát nazvaný `mycert.pfx` z `certs` adresáři úložiště vaší aplikace.

```csharp
using System;
using System.Security.Cryptography.X509Certificates;

...
// Replace the parameter with "~/<relative-path-to-cert-file>".
string certPath = Server.MapPath("~/certs/mycert.pfx");

X509Certificate2 cert = GetCertificate(certPath, signatureBlob.Thumbprint);
...
```
