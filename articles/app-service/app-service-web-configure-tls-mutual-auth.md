---
title: Jak nakonfigurovat vzájemné ověřování protokolu TLS pro webovou aplikaci
description: Zjistěte, jak nakonfigurovat webovou aplikaci pomocí ověření klientského certifikátu na protokol TLS.
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
ms.openlocfilehash: 894a77be05de131ab122f18c62d209e9829357f9
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/14/2018
ms.locfileid: "39056204"
---
# <a name="how-to-configure-tls-mutual-authentication-for-web-app"></a>Jak nakonfigurovat vzájemné ověřování protokolu TLS pro webovou aplikaci
## <a name="overview"></a>Přehled
Tím, že různé typy ověřování, můžete omezit přístup k webové aplikaci Azure. Provedete to jedním ze způsobů je k ověření pomocí klientského certifikátu, pokud je požadavek přes protokol TLS/SSL. Tento mechanismus je volána vzájemného ověřování TLS nebo klientský certifikát, ověřování a tento článek podrobně popisuje způsob nastavení webové aplikace pro použití ověřování pomocí certifikátu klienta.

> **Poznámka:** Pokud přístup k serveru prostřednictvím protokolu HTTP a nikoli HTTPS, neobdržíte žádné klientské certifikáty. Proto pokud vaše aplikace vyžaduje certifikáty klienta by nemělo umožňovat požadavků do vaší aplikace prostřednictvím protokolu HTTP.
> 
> 

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="configure-web-app-for-client-certificate-authentication"></a>Konfigurace webové aplikace pro ověření klientského certifikátu
Nastavit vaši webovou aplikaci tak, aby vyžadovala certifikáty klienta, budete muset přidat nastavení lokality povoleném certifikátu klienta pro vaši webovou aplikaci a nastavte ho na hodnotu true. Toto nastavení je také možné nakonfigurovat na webu Azure Portal v části okno certifikáty SSL.

Můžete použít [ARMClient nástroj](https://github.com/projectkudu/ARMClient) k tomu, aby si poradit při volání REST API. Po přihlášení pomocí nástroje, budete muset vydejte následující příkaz:

    ARMClient PUT subscriptions/{Subscription Id}/resourcegroups/{Resource Group Name}/providers/Microsoft.Web/sites/{Website Name}?api-version=2015-04-01 @enableclientcert.json -verbose

všechno, co v nahrazení {} spolu s informacemi o vaší webové aplikace a vytvoření souboru se říká enableclientcert.json následujícím kódem JSON obsahu:

    {
        "location": "My Web App Location",
        "properties": {
            "clientCertEnabled": true
        }
    }

Ujistěte se, že chcete změnit hodnotu "umístění" na všude, kde se nachází příklad, střed USA – sever a západ USA atd vaší webové aplikace.

Můžete také použít https://resources.azure.com k převrácení `clientCertEnabled` vlastnost `true`.

> **Poznámka:** ARMClient při spuštění z prostředí Powershell, budete muset escape \@ symbolu pro soubor JSON s zpět značek ".
> 
> 

## <a name="accessing-the-client-certificate-from-your-web-app"></a>Přístup k certifikátu klienta z vaší webové aplikace
Pokud používáte ASP.NET a konfigurace aplikace pro použití ověřování pomocí certifikátu klienta, certifikát bude k dispozici prostřednictvím **HttpRequest.ClientCertificate** vlastnost. U jiných zásobníky aplikací bude klientský certifikát k dispozici v aplikaci prostřednictvím hodnotou kódovanou jako base64 v hlavičce žádosti "X ClientCert směrování žádostí na aplikace". Aplikace můžete vytvořit certifikát z této hodnoty a použít jej pro účely ověřování a autorizace ve vaší aplikaci.

## <a name="special-considerations-for-certificate-validation"></a>Zvláštní upozornění pro ověření certifikátu
Klientský certifikát, který je odeslán do aplikace neprochází jakéhokoli ověřování podle platformy Azure Web Apps. Ověřit tento certifikát zodpovídá za webové aplikace. Tady je ukázkový kód ASP.NET, která ověřuje vlastnosti certifikátu pro účely ověřování.

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
