---
title: Použít klientský certifikát SSL v kódu aplikace – Azure App Service | Microsoft Docs
description: Naučte se používat klientské certifikáty pro připojení ke vzdáleným prostředkům, které je vyžadují.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 05/29/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 0c8c270681794621b2a12671d4bcf350cd6cc4d8
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2019
ms.locfileid: "71981120"
---
# <a name="use-an-ssl-certificate-in-your-application-code-in-azure-app-service"></a>Použijte certifikát SSL v kódu aplikace v Azure App Service

Tato příručka ukazuje, jak použít veřejné nebo privátní certifikáty ve vašem kódu aplikace. Příkladem případu použití je, že vaše aplikace přistupuje k externí službě, která vyžaduje ověření certifikátu.

Tento přístup k používání certifikátů v kódu využívá funkci SSL v App Service, která vyžaduje, aby vaše aplikace byla v úrovni **Basic** nebo vyšší. Případně můžete [soubor certifikátu zahrnout do úložiště aplikace](#load-certificate-from-file), ale nejedná se o doporučený postup pro privátní certifikáty.

Když necháte App Service spravovat certifikáty SSL, můžete spravovat certifikáty a kód aplikace samostatně a chránit citlivá data.

## <a name="upload-a-private-certificate"></a>Nahrání privátního certifikátu

Před nahráním privátního certifikátu se ujistěte, že [splňuje všechny požadavky](app-service-web-tutorial-custom-ssl.md#prepare-a-private-certificate), s tím rozdílem, že není nutné konfigurovat pro ověřování serveru.

Až budete připraveni na nahrání, spusťte následující příkaz v <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>.

```azurecli-interactive
az webapp config ssl upload --name <app-name> --resource-group <resource-group-name> --certificate-file <path-to-PFX-file> --certificate-password <PFX-password> --query thumbprint
```

Zkopírujte kryptografický otisk certifikátu a podívejte [se na zpřístupněný certifikát](#make-the-certificate-accessible).

## <a name="upload-a-public-certificate"></a>Nahrajte veřejný certifikát.

Veřejné certifikáty jsou podporovány ve formátu *. cer* . Pokud chcete nahrát veřejný certifikát, <a href="https://portal.azure.com" target="_blank">Azure Portal</a>a přejděte do aplikace.

Klikněte na **Nastavení SSL** > **veřejné certifikáty (. cer)**  > **nahrát veřejný certifikát** z levé navigační části aplikace.

Do pole **název**zadejte název certifikátu. V **souboru certifikátu CER**vyberte svůj soubor CER.

Klikněte na **Odeslat**.

![Odeslat veřejný certifikát](./media/app-service-web-ssl-cert-load/private-cert-upload.png)

Po nahrání certifikátu zkopírujte kryptografický otisk certifikátu a podívejte [se na zpřístupněný certifikát](#make-the-certificate-accessible).

## <a name="import-an-app-service-certificate"></a>Import certifikátu App Service

Přečtěte si téma [koupit a nakonfigurovat certifikát SSL pro Azure App Service](web-sites-purchase-ssl-web-site.md).

Po importu certifikátu zkopírujte kryptografický otisk certifikátu a podívejte se, jak [se certifikát](#make-the-certificate-accessible)zpřístupní.

## <a name="make-the-certificate-accessible"></a>Zpřístupněte certifikát jako přístupný.

Pokud chcete ve svém kódu aplikace použít nahraný nebo importovaný certifikát, zpřístupněte jeho kryptografický otisk pomocí nastavení aplikace `WEBSITE_LOAD_CERTIFICATES` spuštěním následujícího příkazu v <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_CERTIFICATES=<comma-separated-certificate-thumbprints>
```

Pro zpřístupnění všech certifikátů nastavte hodnotu na `*`.

> [!NOTE]
> Toto nastavení umístí zadané certifikáty do aktuálního úložiště [User\My](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores) pro většinu cenových úrovní, ale pokud vaše aplikace běží na **izolované** úrovni (tj. aplikace běží v [App Service Environment](environment/intro.md)), možná budete muset zaškrtnout [místní. ](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores)Místo toho Machine\My Store.
>

![Konfigurovat nastavení aplikace](./media/app-service-web-ssl-cert-load/configure-app-setting.png)

Jakmile budete hotoví, klikněte na **Uložit**.

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

I když soubor načítáte přímo do kódu .NET, knihovna stále ověřuje, zda je aktuální profil uživatele načten. Pokud chcete načíst aktuální profil uživatele, nastavte v <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>nastavení aplikace `WEBSITE_LOAD_USER_PROFILE` pomocí následujícího příkazu.

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
