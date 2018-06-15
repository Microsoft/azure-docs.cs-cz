---
title: Jak nakonfigurovat vzájemné ověřování protokolu TLS pro webovou aplikaci
description: Informace o konfiguraci webové aplikace na protokol TLS použít ověřování certifikátu klienta.
services: app-service
documentationcenter: ''
author: naziml
manager: erikre
editor: jimbe
ms.assetid: cd1d15d3-2d9e-4502-9f11-a306dac4453a
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/08/2016
ms.author: naziml
ms.openlocfilehash: db69852cffd1ff331ac4a640b04ea4360d00bf75
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23836305"
---
# <a name="how-to-configure-tls-mutual-authentication-for-web-app"></a>Jak nakonfigurovat vzájemné ověřování protokolu TLS pro webovou aplikaci
## <a name="overview"></a>Přehled
Povolením různé typy ověřování pro něj můžete omezit přístup k vaší webové aplikace Azure. Jedním ze způsobů k tomu je k ověření pomocí klientského certifikátu, pokud je požadavek protokolem TLS/SSL. Tento mechanismus se nazývají vzájemné ověřování TLS nebo klientský certifikát, ověřování a tento článek podrobně popisuje postup nastavení webové aplikace použít ověřování certifikátu klienta.

> **Poznámka:** při přístupu k webu prostřednictvím protokolu HTTP a HTTPS není, neobdržíte žádné klientský certifikát. Proto pokud vaše aplikace vyžaduje klientské certifikáty neměli povolit požadavky do vaší aplikace prostřednictvím protokolu HTTP.
> 
> 

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="configure-web-app-for-client-certificate-authentication"></a>Konfigurovat webovou aplikaci pro ověření certifikátu klienta
K nastavení vaší webové aplikace na klientské certifikáty vyžadovat musíte přidat nastavení lokality clientCertEnabled pro vaši webovou aplikaci a nastavte ji na hodnotu true. Toto nastavení není aktuálně k dispozici prostřednictvím prostředí pro správu na portálu a bude nutné se k tomu použít rozhraní REST API.

Můžete použít [ARMClient nástroj](https://github.com/projectkudu/ARMClient) snadno vytvořit volání rozhraní REST API. Jakmile se přihlásíte pomocí nástroje budete muset vydejte následující příkaz:

    ARMClient PUT subscriptions/{Subscription Id}/resourcegroups/{Resource Group Name}/providers/Microsoft.Web/sites/{Website Name}?api-version=2015-04-01 @enableclientcert.json -verbose

Nahraďte všechno ve {} informace pro vaši webovou aplikaci a vytvoření souboru s názvem enableclientcert.json následujícím textem JSON obsahu:

    {
        "location": "My Web App Location",
        "properties": {
            "clientCertEnabled": true
        }
    }

Ujistěte se, chcete-li změnit hodnotu "umístění" k kdekoli vaší webové aplikace se nachází například – Sever střední USA nebo západní USA atd.

Můžete také použít https://resources.azure.com k převrácení `clientCertEnabled` vlastnost `true`.

> **Poznámka:** ARMClient při spuštění z prostředí Powershell, budete muset vyhnuli symbol @ pro soubor JSON s back značek '.
> 
> 

## <a name="accessing-the-client-certificate-from-your-web-app"></a>Přístup k certifikátu klienta z vaší webové aplikace
Pokud používáte technologii ASP.NET a nakonfigurovat v aplikaci použít ověřování certifikátu klienta, certifikát je k dispozici prostřednictvím **HttpRequest.ClientCertificate** vlastnost. Pro ostatní zásobníky aplikace bude k dispozici ve vaší aplikaci pomocí kódováním base64 hodnoty v hlavičce žádosti "X-směrování žádostí na aplikace-ClientCert" klientského certifikátu. Aplikace můžete vytvořit certifikát od této hodnoty a použít ho pro účely ověřování a autorizace ve vaší aplikaci.

## <a name="special-considerations-for-certificate-validation"></a>Zvláštní upozornění pro ověření certifikátu
Klientský certifikát, který je odeslán do aplikace neprochází žádné ověření platformou Azure Web Apps. Ověřit tento certifikát má na starosti webové aplikace. Zde je ukázka kódu ASP.NET, která ověřuje vlastnosti certifikátu pro účely ověření.

    using System;
    using System.Collections.Specialized;
    using System.Security.Cryptography.X509Certificates;
    using System.Web;

    namespace ClientCertificateUsageSample
    {
        public partial class cert : System.Web.UI.Page
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

                // If you also want to test if the certificate chains to a Trusted Root Authority you can uncomment the code below
                //
                //X509Chain certChain = new X509Chain();
                //certChain.Build(certificate);
                //bool isValidCertChain = true;
                //foreach (X509ChainElement chElement in certChain.ChainElements)
                //{
                //    if (!chElement.Certificate.Verify())
                //    {
                //        isValidCertChain = false;
                //        break;
                //    }
                //}
                //if (!isValidCertChain) return false;

                return true;
            }
        }
    }
