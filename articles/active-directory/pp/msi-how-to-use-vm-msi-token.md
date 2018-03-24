---
title: Jak získat přístupový token na virtuálním počítači používat Identity spravované služby přiřazený uživatelem.
description: Podrobné pokyny a příklady pro použití MSI přiřazený uživatelem z virtuálního počítače Azure OAuth získat přístup k tokenu.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/22/2017
ms.author: daveba
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 6c6422bc2b13c0c40e48dabf0470c821b13e7851
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="acquire-an-access-token-for-a-vm-user-assigned-managed-service-identity-msi"></a>Získání tokenu přístupu pro virtuální počítač přiřazený uživatelem identita spravované služby (MSI)

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]
Tento článek obsahuje příklady různých kód a skript pro získání tokenu, jakož i pokyny k důležité oblastech, jako je zpracování vypršení platnosti tokenu a chyb protokolu HTTP.

## <a name="prerequisites"></a>Požadavky
[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]
Pokud budete chtít použít v prostředí Azure PowerShell příkladech v tomto článku, je nutné nainstalovat nejnovější verzi [prostředí Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM).

> [!IMPORTANT]
> - Všechny ukázkový kód nebo skript v tomto článku předpokládá klienta běží na virtuálním počítači povolená MSI. Použijte funkci "Připojit" virtuální počítač na portálu Azure se vzdáleně připojit k virtuálnímu počítači. Informace o povolení MSI na virtuálním počítači, v [konfigurace přiřazený uživatelem spravované služby Identity (MSI) pro virtuální počítač, pomocí rozhraní příkazového řádku Azure](msi-qs-configure-cli-windows-vm.md), nebo jeden z typu variant článků (pomocí portálu, prostředí PowerShell, šablonu nebo Azure SDK). 

## <a name="overview"></a>Přehled

Klientská aplikace může požádat o MSI [jen aplikace přístupový token](~/articles/active-directory/develop/active-directory-dev-glossary.md#access-token) pro přístup k danému prostředku. Token je [podle objektu služby MSI](msi-overview.md#how-does-it-work). Jako takový není nutné pro klienta k registraci k získání tokenu přístupu v části vlastní instanční objekt. Token je vhodná pro použití jako token nosiče v [service-to-service volá vyžadují pověření klienta](~/articles/active-directory/active-directory-protocols-oauth-service-to-service.md).

|  |  |
| -------------- | -------------------- |
| [Získat token pomocí protokolu HTTP](#get-a-token-using-http) | Podrobnosti protokolu pro koncový bod tokenu MSI |
| [Získat token pomocí CURL](#get-a-token-using-curl) | Příklad použití koncový bod MSI REST z klienta Bash/CURL |
| [Zpracování vypršení platnosti tokenu](#handling-token-expiration) | Pokyny pro nakládání s vypršenou platností přístupové tokeny |
| [Zpracování chyb](#error-handling) | Pokyny pro zpracování chyby protokolu HTTP vrácená z koncový bod tokenu MSI |
| [Omezení pokyny](#throttling-guidance) | Pokyny pro zpracování omezení koncový bod token MSI |
| [ID prostředků pro služby Azure](#resource-ids-for-azure-services) | Získání ID prostředku pro podporované služby Azure |


## <a name="get-a-token-using-http"></a>Získat token pomocí protokolu HTTP 

Základní rozhraní pro získání tokenu přístupu je založena na REST, zpřístupnění pro všechny klientské aplikace spuštěna na virtuálním počítači, který může provádět volání HTTP REST. Toto je podobná programovací model Azure AD, s výjimkou klient používá koncový bod virtuálního počítače (vs Azure AD koncového bodu).

Ukázková žádost používá koncový bod Instance Metadata služby (IMDS):

```
GET http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F&client_id=712eac09-e943-418c-9be6-9fd5c91078bl HTTP/1.1 Metadata: true
```

Ukázková žádost používá koncový bod rozšíření virtuálního počítače MSI (nadcházející vyřazení):

```
GET http://localhost:50342/oauth2/token?resource=https%3A%2F%2Fmanagement.azure.com%2F&client_id=712eac09-e943-418c-9be6-9fd5c91078bl HTTP/1.1 Metadata: true
```

| Element | Popis |
| ------- | ----------- |
| `GET` | Příkaz HTTP, která určuje, že chcete načíst data z koncového bodu. V takovém případě OAuth přístupový token. | 
| `http://169.254.169.254/metadata/identity/oauth2/token` | Koncový bod MSI pro Instance služby metadat. |
| `http://localhost:50342/oauth2/token` | Koncový bod MSI pro rozšíření virtuálního počítače, kde 50342 je výchozím portem a je možné konfigurovat. |
| `api-version`  | Parametr řetězce dotazu, která určuje verzi rozhraní API pro koncový bod IMDS.  |
| `resource` | Parametr řetězce dotazu, která udává, identifikátor ID URI aplikace cílového prostředku. Zobrazuje se taky v `aud` vydaný token deklarace identity (cílové skupiny). Tento příklad požadavky token pro přístup k Azure Resource Manager, která obsahuje identifikátor ID URI aplikace z https://management.azure.com/. |
| `client_id` |  *Volitelné* parametr řetězce, která udává, ID klienta (také označované jako ID aplikace) dotazu z instanční objekt reprezentující MSI se přiřazený uživatelem. Pokud používáte systém přiřazené MSI, není tento parametr povinný. Tato hodnota se vrátí v `clientId` vlastnost během vytváření MSI se přiřazený uživatelem. Tento příklad požadavků token pro ID klienta "712eac09-e943-418c-9be6-9fd5c91078bl". |
| `Metadata` | Pole hlavičky požadavku HTTP, vyžadují MSI jako zmírnění proti útoku serveru straně požadavek padělání (SSRF). Tato hodnota musí být nastavena na hodnotu true, všechny malými písmeny.

Ukázková odpověď:

```
HTTP/1.1 200 OK
Content-Type: application/json
{
  "access_token": "eyJ0eXAi...",
  "expires_in": "3599",
  "expires_on": "1506484173",
  "not_before": "1506480273",
  "resource": "https://management.azure.com/",
  "token_type": "Bearer"
  "client_id":"712eac09-e943-418c-9be6-9fd5c91078bl"
}
```

| Element | Popis |
| ------- | ----------- |
| `access_token` | Požadovaný přístupový token. Při volání metody zabezpečené rozhraní REST API, je token vložený do `Authorization` pole hlavičky požadavku jako token "nosiče", umožňuje rozhraní API pro ověření volající. | 
| `expires_in` | Počet sekund, po které přístupový token je stále platný, před vypršením platnosti od času vystavování. Čas vystavení lze nalézt v tokenu `iat` deklarací identity. |
| `expires_on` | Časový interval, když vyprší platnost přístupového tokenu. Datum je reprezentován jako počet sekund od "pod hodnotou 1970-01-01T0:0:0Z UTC" (odpovídá na token `exp` deklarace identity). |
| `not_before` | Časový interval, když se projeví přístupový token a nelze přijmout. Datum je reprezentován jako počet sekund od "pod hodnotou 1970-01-01T0:0:0Z UTC" (odpovídá na token `nbf` deklarace identity). |
| `resource` | Prostředek přístupový token byl požadován pro, který odpovídá `resource` požadavku parametr řetězce dotazu. |
| `token_type` | Typ tokenu, což je přístupový token "Nosiče", což znamená, že prostředek můžete poskytnout přístup k nosiče tohoto tokenu. |
| `client_id` | ID klienta (také označované jako ID aplikace) instanční objekt reprezentující přiřazený uživatelem MSI, pro kterou byl požadován token. |

## <a name="get-a-token-using-curl"></a>Získat token pomocí CURL

Nezapomeňte nahradit ID klienta (také označované jako ID aplikace) objektu, pro vaše uživatele přiřazené MSI služby <MSI CLIENT ID> hodnotu `client_id` parametr. Tato hodnota se vrátí v `clientId` vlastnost během vytváření MSI se přiřazený uživatelem.
  
Ukázková žádost používá koncový bod Instance Metadata služby (IMDS):

   ```bash
   response=$(curl -H Metadata:true "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com/&client_id=<MSI CLIENT ID>")
   access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
   echo The MSI access token is $access_token
   ```
   
Ukázková žádost používá koncový bod rozšíření virtuálního počítače MSI (nadcházející vyřazení):

   ```bash
   response=$(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/&client_id=<MSI CLIENT ID>" -H Metadata:true -s)
   access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
   echo The MSI access token is $access_token
   ```

   Příklad odpovědí:

   ```bash
   user@vmLinux:~$ response=$(curl -H Metadata:true "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com/&client_id=9d484c98-b99d-420e-939c-z585174b63bl")
   user@vmLinux:~$ access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
   user@vmLinux:~$ echo The MSI access token is $access_token
   The MSI access token is eyJ0eXAiOiJKV1QiLCJhbGciO...
   ```

## <a name="handling-token-expiration"></a>Zpracování vypršení platnosti tokenu

Subsystém MSI tokeny ukládá do mezipaměti. Proto můžete volat ho tak často, jak se vám líbí a volání na přenosu do služby Azure AD výsledků pouze, pokud:
- dojde k neúspěšnému přístupu do mezipaměti z důvodu žádné token v mezipaměti
- vypršela platnost tokenu

Pokud jste do mezipaměti tokenu ve vašem kódu, byste měli být připravení zpracovávat scénáře, kde prostředek udává, že vypršela platnost tokenu.

## <a name="error-handling"></a>Zpracování chyb 

Koncový bod MSI signály chyby přes pole Kód stavu záhlaví zprávy odpovědi HTTP, jako 4xx nebo 5xx chyb:

| Stavový kód | Důvod chyby | Postupy: zpracování |
| ----------- | ------------ | ------------- |
| došlo k chybě 4xx v požadavku. | Jeden nebo více parametrů žádosti bylo nesprávné. | Nezkoušejte zopakovat.  Zkontrolujte podrobnosti o chybě pro další informace.  4xx chyby jsou chyby při návrhu.|
| 5xx přechodné chybě ze služby. | Dílčí systému MSI nebo Azure Active Directory vrátila přechodné chybě. | Je bezpečné opakujte po čekání minimálně 1 sekunda.  Pokud se znovu pokusíte příliš rychle nebo příliš často, Azure AD může vrátí chybu maximální rychlost (429).|

Pokud dojde k chybě, obsahuje odpovídající text odpovědi HTTP JSON s podrobnosti o chybě:

| Element | Popis |
| ------- | ----------- |
| error   | Chyba identifikátor. |
| error_description | Podrobný popis chyby. **Popisy chyb můžete kdykoli změnit. Nezapisovat kód, který větví na základě hodnot v popisu chyby.**|

### <a name="http-response-reference"></a>Odkaz na odpovědi HTTP

Tato část popisuje možné chybové odpovědi. A "200 OK" ve stavu úspěšné odpovědi a přístupový token se nachází v textu odpovědi JSON, v elementu access_token.

| Stavový kód | Chyba | Popis chyby | Řešení |
| ----------- | ----- | ----------------- | -------- |
| 400 – Chybný požadavek | invalid_resource | AADSTS50001: Aplikaci s názvem *\<URI\>* nebyl nalezen v klientovi s názvem  *\<ID klienta\>*. To může nastat, když aplikace nebyla nainstalována správcem klienta nebo souhlas žádný uživatel v klientovi. Požadavek na ověření mohou mít odeslány nesprávný klienta. \ | (Pouze Linux) |
| 400 – Chybný požadavek | bad_request_102 | Není zadána hlavička požadovaná metadata | Buď `Metadata` pole hlavičky požadavku ze svého požadavku chybí nebo je v nesprávném formátu. Hodnota musí být zadány jako `true`, všechny malými písmeny. V části "Ukázková žádost" v [získat token pomocí protokolu HTTP](#get-a-token-using-http) části příklad.|
| 401 unauthorized | unknown_source | Neznámé zdroje  *\<identifikátor URI\>* | Ověřte, že žádost HTTP GET URI správně naformátován. `scheme:host/resource-path` Část musí být zadány jako `http://169.254.169.254/metadata/identity/oath2/token` nebo `http://localhost:50342/oauth2/token`. V části "Ukázková žádost" v [získat token pomocí protokolu HTTP](#get-a-token-using-http) části příklad.|
|           | invalid_request | Žádosti chybí povinný parametr, obsahuje neplatnou hodnotu parametru, parametr obsahuje více než jednou nebo je jinak poškozený. |  |
|           | unauthorized_client | Klient nemá oprávnění k žádosti o token přístupu pomocí této metody. | Způsobené požadavek, který nepoužili místní smyčky volání rozšíření, nebo na virtuální počítač, který nemá MSI správně nakonfigurován. V tématu [konfigurace virtuálních počítačů spravovaných služba Identity (MSI) pomocí portálu Azure](msi-qs-configure-portal-windows-vm.md) Pokud potřebujete pomoc s konfigurací virtuálních počítačů. |
|           | access_denied | Vlastník prostředku nebo autorizace server odepřel žádost. |  |
|           | unsupported_response_type | Autorizace serveru nepodporuje získání tokenu přístupu pomocí této metody. |  |
|           | invalid_scope | Požadovaný rozsah je neplatný, neznámý nebo je nesprávný. |  |
| 500 vnitřní chybu serveru | Neznámé | Nepodařilo se získat token ze služby Active directory. Podrobnosti najdete v tématu protokoly v  *\<cesta k souboru\>* | Ověřte, že je povoleno MSI ve virtuálním počítači. V tématu [konfigurace virtuálních počítačů spravovaných služba Identity (MSI) pomocí portálu Azure](msi-qs-configure-portal-windows-vm.md) Pokud potřebujete pomoc s konfigurací virtuálních počítačů.<br><br>Také ověřte, že žádost HTTP GET URI správně naformátován, zejména prostředek, který je zadaný identifikátor URI v řetězci dotazu. Najdete v části "Ukázková žádost" v [získat token pomocí protokolu HTTP](#get-a-token-using-http) části příklad, nebo [služeb Azure, podpora Azure AD ověření](msi-overview.md#azure-services-that-support-azure-ad-authentication) seznam služeb a jejich odpovídající ID prostředku.

## <a name="throttling-guidance"></a>Omezení pokyny 

Omezení omezení se vztahuje na počet volání na koncový bod MSI IMDS. Překročení omezení prahovou hodnotu, koncový bod MSI IMDS omezuje žádné další požadavky omezení je v platnosti. Během této doby koncový bod MSI IMDS vrátí stavový kód HTTP 429 ("příliš mnoho požadavků"), a požadavky selžou. 

Zkuste to znovu doporučujeme následující strategie: 

| **Strategie opakování** | **Nastavení** | **Hodnoty** | **Jak to funguje** |
| --- | --- | --- | --- |
|ExponentialBackoff |Počet opakování<br />Min back-off<br />Max back-off<br />Delta back-off<br />První rychlé opakování |5<br />0 s<br />60 s<br />2 s<br />false (nepravda) |Pokus 1 – zpoždění 0 s<br />Pokus 2 – zpoždění ~2 s<br />Pokus 3 – zpoždění ~6 s<br />Pokus 4 – zpoždění ~14 s<br />Pokus 5 – zpoždění ~30 s |

## <a name="resource-ids-for-azure-services"></a>ID prostředků pro služby Azure

V tématu [služeb Azure, podpora Azure AD ověření](msi-overview.md#azure-services-that-support-azure-ad-authentication) seznam prostředků, které podporují Azure AD a byly testovány s MSI a jejich odpovídající ID prostředku.


## <a name="next-steps"></a>Další postup

- Pokud chcete povolit MSI ve virtuálním počítači Azure, najdete v části [konfigurace přiřazený uživatelem spravované služby Identity (MSI) pro virtuální počítač, pomocí rozhraní příkazového řádku Azure](msi-qs-configure-cli-windows-vm.md).

Použijte následující sekci komentáře k poskytnutí zpětné vazby a Pomozte nám vylepšit a utvářejí náš obsah.








