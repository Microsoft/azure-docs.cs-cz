---
title: Použít certifikát SSL nahrané v kódu aplikace v Azure App Service | Microsoft Docs
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
ms.openlocfilehash: 6800bf766deb2044d400f92dbe370fa15bdd5f00
ms.sourcegitcommit: be0d1aaed5c0bbd9224e2011165c5515bfa8306c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2017
ms.locfileid: "26047697"
---
# <a name="use-an-ssl-certificate-in-your-application-code-in-azure-app-service"></a>Použít certifikát SSL v kódu aplikace v Azure App Service

Tento postup ukazuje, jak použít jeden z certifikátů SSL jste nahráli nebo importovat aplikaci aplikační služby v kódu aplikace. Příkladem případ použití je, že vaše aplikace přístup k externí služba, která vyžaduje ověřování pomocí certifikátu. 

Tento přístup k používání certifikátů SSL ve vašem kódu využívá SSL funkce ve službě App Service, která vyžaduje, aby vaše aplikace se v **základní** vrstvy nebo vyšší. Alternativou je zahrnout soubor certifikátu v adresáři vaší aplikace a načíst přímo (viz [alternativní: zatížení certifikát jako soubor](#file)). Však tato alternativní neumožňuje skrýt privátní klíč v certifikátu z kódu aplikace nebo vývojář. Kromě toho pokud kód aplikace v úložišti otevřeným zdrojem, udržování certifikát s privátním klíčem do úložiště není možné.

Když necháte App Service spravovat své certifikáty protokolu SSL, můžete spravovat certifikáty a kódu aplikace samostatně a chrání citlivá data.

## <a name="prerequisites"></a>Požadavky

Chcete-li provést tento postup průvodce:

- [Vytvořit aplikaci služby App Service](/azure/app-service/)
- [Mapování názvu DNS na vaší webové aplikace](app-service-web-tutorial-custom-domain.md)
- [Nahrát na server certifikát SSL](app-service-web-tutorial-custom-ssl.md) nebo [importovat certifikát App Service](web-sites-purchase-ssl-web-site.md) do vaší webové aplikace


## <a name="load-your-certificates"></a>Načtení certifikátů

Pokud chcete používat certifikát, který je nahrán do nebo importovat do služby App Service, nejprve usnadňují kódu aplikace. Můžete to provést pomocí `WEBSITE_LOAD_CERTIFICATES` nastavení aplikace.

V <a href="https://portal.azure.com" target="_blank">portál Azure</a>, otevřete stránku své webové aplikace.

V levém navigačním panelu, klikněte na tlačítko **certifikáty SSL**.

![Nahrát certifikát](./media/app-service-web-tutorial-custom-ssl/certificate-uploaded.png)

Zde se zobrazují všechny své nahrávat a importované certifikáty protokolu SSL pro tuto webovou aplikaci s jejich kryptografické otisky. Zkopírujte kryptografický otisk certifikátu, který chcete použít.

V levém navigačním panelu, klikněte na tlačítko **nastavení aplikace**.

Přidat aplikaci názvem `WEBSITE_LOAD_CERTIFICATES` a jeho hodnotu nastavte kryptografický otisk certifikátu. Chcete-li zpřístupnit více certifikátů, použijte hodnot oddělených čárkami kryptografický otisk. Chcete-li všechny certifikáty dostupné, nastavte hodnotu na `*`. 

![Konfigurace nastavení aplikace](./media/app-service-web-ssl-cert-load/configure-app-setting.png)

Po dokončení klikněte na tlačítko **Uložit**.

Nakonfigurovaný certifikát je teď připravená k použití podle vašeho kódu.

## <a name="use-certificate-in-c-code"></a>Použít certifikát v kódu jazyka C#

Po váš certifikát je dostupné, můžete zobrazit jeho v C# – kód kryptografický otisk certifikátu. Následující kód načte certifikát s kryptografickým otiskem `E661583E8FABEF4C0BEF694CBC41C28FB81CD870`.

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
## <a name="alternative-load-certificate-as-a-file"></a>Alternativní: načtení certifikátu jako soubor

Tato část uvádí, jak a načíst soubor certifikátu, který je v adresáři vaší aplikace. 

Následující příklad jazyka C# načte certifikát nazvaný `mycert.pfx` z `certs` adresář úložiště vaší aplikace.

```csharp
using System;
using System.Security.Cryptography.X509Certificates;

...
// Replace the parameter with "~/<relative-path-to-cert-file>".
string certPath = Server.MapPath("~/certs/mycert.pfx");

X509Certificate2 cert = GetCertificate(certPath, signatureBlob.Thumbprint);
...
```

