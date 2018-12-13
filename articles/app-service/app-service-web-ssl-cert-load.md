---
title: Použít klientský certifikát SSL v kódu aplikace – Azure App Service | Dokumentace Microsoftu
description: ''
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
ms.date: 12/01/2017
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 8041d86e21f464e1dee4fba543be7d2c54c10367
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2018
ms.locfileid: "53254322"
---
# <a name="use-an-ssl-certificate-in-your-application-code-in-azure-app-service"></a>Použití certifikátu SSL v kódu aplikace ve službě Azure App Service

Tato příručka ukazuje, jak chcete použít jeden z certifikátů SSL, můžete mít nahráli nebo importovali aplikace app Service v kódu aplikace. Příklad případem použití je, že vaše aplikace přistupuje k externí služby, která vyžaduje ověření certifikátu. 

Tento přístup k používání certifikátů SSL v kódu využívá protokol SSL funkcí ve službě App Service, která vyžaduje, aby vaše aplikace v **základní** vrstvy nebo vyšší. Alternativou je zahrnout soubor certifikátu do adresáře aplikace a jejich načtení přímo (viz [alternativní: zatížení certifikát jako soubor](#file)). Nicméně tato alternativa neumožňuje skrýt privátní klíč v certifikátu od kódu aplikace nebo vývojáře. Kromě toho pokud kód aplikace je v úložišti opensourcových, udržování certifikát s privátním klíčem do úložiště není možné.

Pokud umožníte spravovat své certifikáty SSL služby App Service můžete samostatně spravovat certifikáty a kódu aplikace a chránit citlivá data.

## <a name="prerequisites"></a>Požadavky

K dokončení této příručce s postupy:

- [Vytvořit aplikaci App Service](/azure/app-service/)
- [Namapovat na svou webovou aplikaci vlastní název DNS](app-service-web-tutorial-custom-domain.md)
- [Nahrát certifikát SSL](app-service-web-tutorial-custom-ssl.md) nebo [importovat certifikát App Service](web-sites-purchase-ssl-web-site.md) do vaší webové aplikace


## <a name="load-your-certificates"></a>Načíst certifikáty

Pokud chcete použít certifikát, který je odeslán do nebo importovat do služby App Service, nejprve usnadňují kódu aplikace. To provedete `WEBSITE_LOAD_CERTIFICATES` nastavení aplikace.

V <a href="https://portal.azure.com" target="_blank">webu Azure portal</a>, otevřete stránce vaší webové aplikace.

V levém navigačním panelu klikněte na tlačítko **certifikáty SSL**.

![Nahraný certifikát](./media/app-service-web-tutorial-custom-ssl/certificate-uploaded.png)

Všechny nahrané a importované certifikáty SSL pro tuto webovou aplikaci se tady zobrazí s jejich kryptografické otisky. Zkopírujte kryptografický otisk certifikátu, který chcete použít.

V levém navigačním panelu klikněte na tlačítko **nastavení aplikace**.

Přidání aplikace názvem `WEBSITE_LOAD_CERTIFICATES` a nastavte jej na hodnotu kryptografického otisku certifikátu. Zpřístupnit více certifikátů použijte hodnoty oddělené čárkami kryptografický otisk. Zpřístupnit všechny certifikáty, nastavte hodnotu na `*`. Poznamenejte si, který to umístí certifikát do `CurrentUser\My` ukládat.

![Konfigurace nastavení aplikace](./media/app-service-web-ssl-cert-load/configure-app-setting.png)

Jakmile budete hotoví, klikněte na **Uložit**.

Nakonfigurovaný certifikát je teď připravená k použití ve vašem kódu.

## <a name="use-certificate-in-c-code"></a>Použití certifikátu v kódu jazyka C#

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
## <a name="alternative-load-certificate-as-a-file"></a>Ve zkratce: Načíst certifikát jako soubor

Tato část ukazuje, jak a načtení souboru certifikátu, který je v adresáři vaší aplikace. 

Následující příklad jazyka C# načte certifikát nazvaný `mycert.pfx` z `certs` adresáři úložiště vaší aplikace.

```csharp
using System;
using System.Security.Cryptography.X509Certificates;

...
// Replace the parameter with "~/<relative-path-to-cert-file>".
string certPath = Server.MapPath("~/certs/mycert.pfx");

X509Certificate2 cert = GetCertificate(certPath, signatureBlob.Thumbprint);
...
```

