---
title: Konfigurace vzájemného ověřování TLS
description: Přečtěte si, jak ověřit klientské certifikáty v TLS. Azure App Service může klientský certifikát zpřístupnit kódu aplikace pro ověření.
ms.assetid: cd1d15d3-2d9e-4502-9f11-a306dac4453a
ms.topic: article
ms.date: 10/01/2019
ms.custom: seodec18
ms.openlocfilehash: aa4d0b5089c07cbbeb3d40224518b9eb9a927585
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2019
ms.locfileid: "74672121"
---
# <a name="configure-tls-mutual-authentication-for-azure-app-service"></a>Konfigurace vzájemného ověřování TLS pro Azure App Service

Přístup k aplikaci Azure App Service můžete omezit povolením různých typů ověřování. Jedním ze způsobů, jak to provést, je požádat o klientský certifikát, když požadavek klienta překročí protokol TLS/SSL a ověří certifikát. Tento mechanismus se nazývá vzájemné ověřování TLS nebo ověřování klientským certifikátem. Tento článek popisuje, jak nastavit aplikaci tak, aby používala ověřování klientským certifikátem.

> [!NOTE]
> Pokud k webu přistupujete přes HTTP a ne HTTPS, nebudete dostávat žádné klientské certifikáty. Takže pokud vaše aplikace vyžaduje klientské certifikáty, neměli byste žádosti do vaší aplikace povolovat přes HTTP.
>

## <a name="enable-client-certificates"></a>Povolit klientské certifikáty

Pokud chcete nastavit aplikaci tak, aby vyžadovala klientské certifikáty, musíte nastavit `clientCertEnabled` nastavení aplikace tak, aby `true`. Nastavení nastavíte spuštěním následujícího příkazu v [Cloud Shell](https://shell.azure.com).

```azurecli-interactive
az webapp update --set clientCertEnabled=true --name <app_name> --resource-group <group_name>
```

## <a name="exclude-paths-from-requiring-authentication"></a>Vyloučit cesty z vyžadování ověřování

Pokud povolíte vzájemné ověřování pro vaši aplikaci, všechny cesty pod kořenem vaší aplikace budou vyžadovat klientský certifikát pro přístup. Pokud chcete, aby některé cesty zůstaly otevřené pro anonymní přístup, můžete v rámci konfigurace aplikace definovat cesty vyloučení.

Cesty vyloučení se dají nakonfigurovat tak, že vyberete **konfigurační** > **Obecná nastavení** a definujete cestu vyloučení. V tomto příkladu cokoli, co `/public` cesta k vaší aplikaci, nepožadují klientský certifikát.

![Cesty vyloučení certifikátu][exclusion-paths]


## <a name="access-client-certificate"></a>Přístup k klientskému certifikátu

V App Service probíhá ukončení žádosti SSL na front-endu Load Balancer. Při předávání žádosti do kódu aplikace s [povolenými klientskými certifikáty](#enable-client-certificates)App Service vloží hlavičku žádosti `X-ARR-ClientCert` s klientským certifikátem. App Service s tímto klientským certifikátem nedělá něco jiného než předání do aplikace. Kód vaší aplikace zodpovídá za ověřování klientského certifikátu.

V případě ASP.NET je certifikát klienta k dispozici prostřednictvím vlastnosti **HttpRequest. ClientCertificate** .

Pro ostatní zásobníky aplikací (Node. js, PHP atd.) je certifikát klienta k dispozici ve vaší aplikaci prostřednictvím hodnoty kódované v kódování Base64 v hlavičce `X-ARR-ClientCert` žádosti.

## <a name="aspnet-sample"></a>Ukázka ASP.NET

```csharp
    using System;
    using System.Collections.Specialized;
    using System.Security.Cryptography.X509Certificates;
    using System.Web;

    namespace ClientCertificateUsageSample
    {
        public partial class Cert : System.Web.UI.Page
        {
            public string certHeader = "";
            public string errorString = "";
            private X509Certificate2 certificate = null;
            public string certThumbprint = "";
            public string certSubject = "";
            public string certIssuer = "";
            public string certSignatureAlg = "";
            public string certIssueDate = "";
            public string certExpiryDate = "";
            public bool isValidCert = false;

            //
            // Read the certificate from the header into an X509Certificate2 object
            // Display properties of the certificate on the page
            //
            protected void Page_Load(object sender, EventArgs e)
            {
                NameValueCollection headers = base.Request.Headers;
                certHeader = headers["X-ARR-ClientCert"];
                if (!String.IsNullOrEmpty(certHeader))
                {
                    try
                    {
                        byte[] clientCertBytes = Convert.FromBase64String(certHeader);
                        certificate = new X509Certificate2(clientCertBytes);
                        certSubject = certificate.Subject;
                        certIssuer = certificate.Issuer;
                        certThumbprint = certificate.Thumbprint;
                        certSignatureAlg = certificate.SignatureAlgorithm.FriendlyName;
                        certIssueDate = certificate.NotBefore.ToShortDateString() + " " + certificate.NotBefore.ToShortTimeString();
                        certExpiryDate = certificate.NotAfter.ToShortDateString() + " " + certificate.NotAfter.ToShortTimeString();
                    }
                    catch (Exception ex)
                    {
                        errorString = ex.ToString();
                    }
                    finally 
                    {
                        isValidCert = IsValidClientCertificate();
                        if (!isValidCert) Response.StatusCode = 403;
                        else Response.StatusCode = 200;
                    }
                }
                else
                {
                    certHeader = "";
                }
            }

            //
            // This is a SAMPLE verification routine. Depending on your application logic and security requirements, 
            // you should modify this method
            //
            private bool IsValidClientCertificate()
            {
                // In this example we will only accept the certificate as a valid certificate if all the conditions below are met:
                // 1. The certificate is not expired and is active for the current time on server.
                // 2. The subject name of the certificate has the common name nildevecc
                // 3. The issuer name of the certificate has the common name nildevecc and organization name Microsoft Corp
                // 4. The thumbprint of the certificate is 30757A2E831977D8BD9C8496E4C99AB26CB9622B
                //
                // This example does NOT test that this certificate is chained to a Trusted Root Authority (or revoked) on the server 
                // and it allows for self signed certificates
                //

                if (certificate == null || !String.IsNullOrEmpty(errorString)) return false;

                // 1. Check time validity of certificate
                if (DateTime.Compare(DateTime.Now, certificate.NotBefore) < 0 || DateTime.Compare(DateTime.Now, certificate.NotAfter) > 0) return false;

                // 2. Check subject name of certificate
                bool foundSubject = false;
                string[] certSubjectData = certificate.Subject.Split(new char[] { ',' }, StringSplitOptions.RemoveEmptyEntries);
                foreach (string s in certSubjectData)
                {
                    if (String.Compare(s.Trim(), "CN=nildevecc") == 0)
                    {
                        foundSubject = true;
                        break;
                    }
                }
                if (!foundSubject) return false;

                // 3. Check issuer name of certificate
                bool foundIssuerCN = false, foundIssuerO = false;
                string[] certIssuerData = certificate.Issuer.Split(new char[] { ',' }, StringSplitOptions.RemoveEmptyEntries);
                foreach (string s in certIssuerData)
                {
                    if (String.Compare(s.Trim(), "CN=nildevecc") == 0)
                    {
                        foundIssuerCN = true;
                        if (foundIssuerO) break;
                    }

                    if (String.Compare(s.Trim(), "O=Microsoft Corp") == 0)
                    {
                        foundIssuerO = true;
                        if (foundIssuerCN) break;
                    }
                }

                if (!foundIssuerCN || !foundIssuerO) return false;

                // 4. Check thumprint of certificate
                if (String.Compare(certificate.Thumbprint.Trim().ToUpper(), "30757A2E831977D8BD9C8496E4C99AB26CB9622B") != 0) return false;

                return true;
            }
        }
    }
```

## <a name="nodejs-sample"></a>Ukázka Node. js

Následující ukázkový kód Node. js získá hlavičku `X-ARR-ClientCert` a pomocí [Node-zfalšovat](https://github.com/digitalbazaar/forge) PŘEVEDE řetězec PEM s kódováním base64 na objekt certifikátu a ověří ho:

```javascript
import { NextFunction, Request, Response } from 'express';
import { pki, md, asn1 } from 'node-forge';

export class AuthorizationHandler {
    public static authorizeClientCertificate(req: Request, res: Response, next: NextFunction): void {
        try {
            // Get header
            const header = req.get('X-ARR-ClientCert');
            if (!header) throw new Error('UNAUTHORIZED');

            // Convert from PEM to pki.CERT
            const pem = `-----BEGIN CERTIFICATE-----${header}-----END CERTIFICATE-----`;
            const incomingCert: pki.Certificate = pki.certificateFromPem(pem);

            // Validate certificate thumbprint
            const fingerPrint = md.sha1.create().update(asn1.toDer(pki.certificateToAsn1(incomingCert)).getBytes()).digest().toHex();
            if (fingerPrint.toLowerCase() !== 'abcdef1234567890abcdef1234567890abcdef12') throw new Error('UNAUTHORIZED');

            // Validate time validity
            const currentDate = new Date();
            if (currentDate < incomingCert.validity.notBefore || currentDate > incomingCert.validity.notAfter) throw new Error('UNAUTHORIZED');

            // Validate issuer
            if (incomingCert.issuer.hash.toLowerCase() !== 'abcdef1234567890abcdef1234567890abcdef12') throw new Error('UNAUTHORIZED');

            // Validate subject
            if (incomingCert.subject.hash.toLowerCase() !== 'abcdef1234567890abcdef1234567890abcdef12') throw new Error('UNAUTHORIZED');

            next();
        } catch (e) {
            if (e instanceof Error && e.message === 'UNAUTHORIZED') {
                res.status(401).send();
            } else {
                next(e);
            }
        }
    }
}
```

[exclusion-paths]: ./media/app-service-web-configure-tls-mutual-auth/exclusion-paths.png