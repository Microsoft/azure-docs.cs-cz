---
title: Azure Active Directory SMART on FHIR proxy
description: V tomto kurzu se dozvíte, jak pomocí proxy Povolit inteligentní aplikace FHIR s rozhraním API Azure pro FHIR.
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: dseven
ms.author: matjazl
author: matjazl
ms.date: 04/02/2019
ms.openlocfilehash: 2e13a9fc32964781dda07e5534e5cab79868ddf0
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "95995535"
---
# <a name="tutorial-azure-active-directory-smart-on-fhir-proxy"></a>Kurz: Azure Active Directory SMART on FHIR proxy

[Inteligentní na FHIR](https://docs.smarthealthit.org/) je sada otevřených specifikací pro integraci partnerských aplikací se servery FHIR a elektronickými systémy lékařských záznamů, které mají rozhraní FHIR. Jedním z hlavních účelů specifikace je popsání toho, jak má aplikace zjistit koncové body ověřování pro server FHIR a spustit sekvenci ověřování. 

Ověřování je založené na OAuth2. Protože inteligentní na FHIR používá konvence pojmenování parametrů, které nejsou hned kompatibilní s Azure Active Directory (Azure AD), rozhraní API Azure pro FHIR má vestavěný integrovaný Azure AD SMART on FHIR proxy, který umožňuje podmnožinu INTELIGENTNÍch sekvencí spouštění FHIR. Konkrétně proxy umožňuje [sekvenci spuštění EHR](https://hl7.org/fhir/smart-app-launch/#ehr-launch-sequence).

V tomto kurzu se dozvíte, jak pomocí proxy Povolit inteligentní aplikace FHIR s rozhraním API Azure pro FHIR.

## <a name="prerequisites"></a>Požadavky

- Instance rozhraní API Azure pro FHIR
- [.NET Core 2.2](https://dotnet.microsoft.com/download/dotnet-core/2.2)

## <a name="configure-azure-ad-registrations"></a>Konfigurace registrací Azure AD

Inteligentní na FHIR vyžaduje, aby identifikátor `Audience` URI identifikátoru byl stejný jako identifikátor URI služby FHIR. Standardní konfigurace rozhraní API Azure pro FHIR používá `Audience` hodnotu `https://azurehealthcareapis.com` . Můžete ale také nastavit hodnotu, která odpovídá konkrétní adrese URL služby FHIR (například `https://MYFHIRAPI.azurehealthcareapis.com` ). Tento požadavek je nutný při práci s FHIR proxy INTELIGENTNÍho serveru.

Budete také potřebovat registraci klientské aplikace. Většina INTELIGENTNÍch aplikací v FHIR je jednoduchá stránka JavaScriptu. Proto byste měli postupovat podle pokynů pro konfiguraci [veřejné klientské aplikace služby Azure AD](register-public-azure-ad-client-app.md).

Po dokončení tohoto postupu byste měli mít následující:

- Server FHIR s cílovou skupinou RGE nastavenou na `https://MYFHIRAPI.azurehealthcareapis.com` , kde `MYFHIRAPI` je název vašeho rozhraní API Azure pro instanci FHIR.
- Registrace veřejné klientské aplikace. Poznamenejte si ID aplikace pro tuto klientskou aplikaci.

## <a name="enable-the-smart-on-fhir-proxy"></a>Povolit proxy inteligentní na FHIR

Zaškrtněte políčko inteligentní na proxy serveru FHIR v nastavení **ověřování** pro instanci Azure API pro FHIR, a to tak, že zaškrtnete políčko **inteligentní on FHIR proxy** :

![Výběry pro povolení serveru proxy INTELIGENTNÍho na FHIR](media/tutorial-smart-on-fhir/enable-smart-on-fhir-proxy.png)

## <a name="enable-cors"></a>Povolení CORS

Vzhledem k tomu, že většina INTELIGENTNÍch aplikací v FHIR jsou jednostránkové aplikace JavaScriptu, musíte [Povolit sdílení prostředků mezi zdroji (CORS)](configure-cross-origin-resource-sharing.md) pro Azure API pro FHIR:

![Výběry pro povolení CORS](media/tutorial-smart-on-fhir/enable-cors.png)

## <a name="configure-the-reply-url"></a>Konfigurace adresy URL odpovědi

Proxy inteligentní na FHIR funguje jako prostředník mezi aplikací SMART v FHIR a Azure AD. Ověřovací odpověď (ověřovací kód) musí místo samotné aplikace přejít na proxy inteligentní na FHIR. Proxy pak pošle odpověď do aplikace. 

Z důvodu tohoto oboustranného přenosu ověřovacího kódu musíte nastavit adresu URL odpovědi (zpětného volání) pro vaši klientskou aplikaci Azure AD na adresu URL, která je kombinací adresy URL odpovědi pro inteligentní službu FHIR proxy serveru a adresu URL odpovědi pro aplikaci SMART on FHIR. Kombinovaná adresa URL odpovědi má tento formát:

```http
https://MYFHIRAPI.azurehealthcareapis.com/AadSmartOnFhirProxy/callback/aHR0cHM6Ly9sb2NhbGhvc3Q6NTAwMS9zYW1wbGVhcHAvaW5kZXguaHRtbA
```

V této odpovědi `aHR0cHM6Ly9sb2NhbGhvc3Q6NTAwMS9zYW1wbGVhcHAvaW5kZXguaHRtbA` je verze adresy URL pro odpověď, která je v aplikaci FHIR zakódovaná pomocí kódování Base64, bezpečná. V případě spuštění aplikace SMART on FHIR, pokud je aplikace spuštěná místně, je adresa URL odpovědi `https://localhost:5001/sampleapp/index.html` . 

Kombinovanou adresu URL odpovědi můžete vygenerovat pomocí skriptu podobného tomuto:

```PowerShell
$replyUrl = "https://localhost:5001/sampleapp/index.html"
$fhirServerUrl = "https://MYFHIRAPI.azurewebsites.net"
$bytes = [System.Text.Encoding]::UTF8.GetBytes($ReplyUrl)
$encodedText = [Convert]::ToBase64String($bytes)
$encodedText = $encodedText.TrimEnd('=');
$encodedText = $encodedText.Replace('/','_');
$encodedText = $encodedText.Replace('+','-');

$newReplyUrl = $FhirServerUrl.TrimEnd('/') + "/AadSmartOnFhirProxy/callback/" + $encodedText
```

Přidejte adresu URL odpovědi do aplikace veřejné klienta, kterou jste vytvořili dříve pro Azure AD:

![Adresa URL odpovědi konfigurovaná pro veřejného klienta](media/tutorial-smart-on-fhir/configure-reply-url.png)

## <a name="get-a-test-patient"></a>Získat pacient testu

K otestování rozhraní API Azure pro FHIR a INTELIGENTNÍho proxy serveru FHIR budete muset mít v databázi aspoň jeden pacient. Pokud jste ještě nespolupracovali s rozhraním API a nemáte data v databázi, nahrajte pacient pomocí [kurzu FHIR API post](access-fhir-postman-tutorial.md) . Poznamenejte si ID konkrétního pacienta.

## <a name="download-the-smart-on-fhir-app-launcher"></a>Stáhnout spouštěč aplikací SMART on FHIR

Open source [Server FHIR pro úložiště Azure](https://github.com/Microsoft/fhir-server) obsahuje jednoduchou inteligentní službu pro spouštěč aplikací FHIR a ukázku inteligentních aplikací FHIR. V tomto kurzu pomocí tohoto INTELIGENTNÍho spouštění ve Spouštěči FHIR otestujete instalaci.

Úložiště GitHub můžete klonovat a přejít do aplikace pomocí těchto příkazů:

```PowerShell
git clone https://github.com/Microsoft/fhir-server
cd fhir-server/samples/apps/SmartLauncher
```

Aplikace potřebuje několik nastavení konfigurace, která můžete nastavit v `appsettings.json` :

```json
{
    "FhirServerUrl": "https://MYFHIRAPI.azurehealthcareapis.com",
    "ClientId": "APP-ID",
    "DefaultSmartAppUrl": "/sampleapp/launch.html"
}
```

Doporučujeme použít tuto `dotnet user-secrets` funkci:

```PowerShell
dotnet user-secrets set FhirServerUrl https://MYFHIRAPI.azurehealthcareapis.com
dotnet user-secrets set ClientId <APP-ID>
```

Pomocí tohoto příkazu spusťte aplikaci:

```PowerShell
dotnet run
```

## <a name="test-the-smart-on-fhir-proxy"></a>Testování proxy INTELIGENTNÍho rozhraní FHIR

Jakmile spustíte spouštěč aplikací SMART on FHIR, můžete nasměrovat prohlížeč na `https://localhost:5001` , kde by se měla zobrazit následující obrazovka:

![Inteligentní spuštění aplikace FHIR](media/tutorial-smart-on-fhir/smart-on-fhir-app-launcher.png)

Když zadáte **pacienty**, **zaznamenání** nebo informace o **specialistě** , zjistíte, že je **kontext spuštění** aktualizovaný. Pokud používáte rozhraní API Azure pro FHIR, je kontext spuštění jednoduše dokumentem JSON, který obsahuje informace o pacientech, specialistech a dalších. Tento kontext spuštění je kódovaný v kódování Base64 a předává se do inteligentní aplikace FHIR jako `launch` parametr dotazu. Podle specifikace SMART on FHIR je tato proměnná neprůhledná pro aplikaci SMART on FHIR a předala se poskytovateli identity. 

Proxy inteligentní na FHIR používá tyto informace k naplnění polí v odpovědi na token. Aplikace SMART on FHIR *může* tato pole použít k určení toho, pro který pacient žádá o data a jak vykreslí aplikaci na obrazovce. Proxy inteligentní na FHIR podporuje následující pole:

* `patient`
* `encounter`
* `practitioner`
* `need_patient_banner`
* `smart_style_url`

Tato pole jsou určena k poskytnutí pokynů pro aplikaci, ale neposkytují žádné informace o zabezpečení. Inteligentní aplikace FHIR je může ignorovat.

Všimněte si, že spouštěč INTELIGENTNÍch aplikací FHIR aktualizuje informace o **adrese URL pro spuštění** v dolní části stránky. Výběrem **Spustit** spusťte ukázkovou aplikaci a měli byste vidět něco jako v této ukázce:

![Inteligentní aplikace v FHIR](media/tutorial-smart-on-fhir/smart-on-fhir-app.png)

Zkontrolujte odpověď tokenu a podívejte se, jak se do aplikace předávají Kontextová pole pro spuštění.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste nakonfigurovali Azure Active Directory SMART on FHIR proxy. Pokud chcete prozkoumat použití INTELIGENTNÍch funkcí v aplikacích FHIR s rozhraním API Azure pro FHIR a open source FHIR serverem pro Azure, přečtěte si v části úložiště ukázek serveru FHIR na GitHubu:

>[!div class="nextstepaction"]
>[Ukázky serveru FHIR](https://github.com/Microsoft/fhir-server-samples)
