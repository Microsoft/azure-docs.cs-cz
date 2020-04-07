---
title: Rozhraní REST API pro správu relací
description: Popisuje způsob správy relací.
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 46560f067e020236031487677ad4f48a9560d4e1
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681243"
---
# <a name="use-the-session-management-rest-api"></a>Použití rozhraní REST API pro správu relací

Chcete-li použít funkci vzdáleného vykreslování Azure, musíte vytvořit *relaci*. Každá relace odpovídá virtuálnímu počítači (VM) přiděleného v Azure a čekání na připojení klientského zařízení. Když se zařízení připojí, virtuální modul vykreslí požadovaná data a zobrazí výsledek jako datový proud videa. Během vytváření relace jste zvolili, na kterém druhu serveru chcete spustit, což určuje ceny. Jakmile relace již není potřeba, měla by být zastavena. Pokud není zastavena ručně, bude automaticky vypnuta po vypršení *doby zapůjčení* relace.

Poskytujeme skript prostředí PowerShell v [úložišti ukázek ARR](https://github.com/Azure/azure-remote-rendering) ve složce *Skripty* s názvem *RenderingSession.ps1*, který demonstruje použití naší služby. Skript a jeho konfigurace jsou popsány zde: [Příklad skriptů prostředí PowerShell](../samples/powershell-example-scripts.md)

> [!TIP]
> Příkazy prostředí PowerShell uvedené na této stránce se mají vzájemně doplňovat. Pokud spustíte všechny skripty v pořadí ve stejném příkazovém řádku prostředí PowerShell, budou stavět na sebe.

## <a name="regions"></a>Oblasti

Podívejte se na [seznam dostupných oblastí](../reference/regions.md) pro základní adresy URL, na které chcete odeslat požadavky.

Pro ukázkové skripty níže jsme zvolili region *westus2*.

### <a name="example-script-choose-an-endpoint"></a>Příklad skriptu: Volba koncového bodu

```PowerShell
$endPoint = "https://remoterendering.westus2.mixedreality.azure.com"
```

## <a name="accounts"></a>Účty

Pokud nemáte účet vzdáleného vykreslování, [vytvořte si ho](create-an-account.md). Každý prostředek je identifikován *accountId*, který se používá v rámci relace API.

### <a name="example-script-set-accountid-and-accountkey"></a>Příklad skriptu: Nastavit accountId a accountKey

```PowerShell
$accountId = "********-****-****-****-************"
$accountKey = "*******************************************="
```

## <a name="common-request-headers"></a>Běžné hlavičky požadavků

* Hlavička *Autorizace* musí mít`Bearer TOKEN`hodnotu`TOKEN`" ", kde " je ověřovací token [vrácený službou Secure Token Service](tokens.md).

### <a name="example-script-request-a-token"></a>Příklad skriptu: Žádost o token

```PowerShell
[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12
$webResponse = Invoke-WebRequest -Uri "https://sts.mixedreality.azure.com/accounts/$accountId/token" -Method Get -ContentType "application/json" -Headers @{ Authorization = "Bearer ${accountId}:$accountKey" }
$response = ConvertFrom-Json -InputObject $webResponse.Content
$token = $response.AccessToken;
```

## <a name="common-response-headers"></a>Běžné hlavičky odpovědí

* Hlavičku *MS-CV* může produktový tým použít ke sledování volání v rámci služby.

## <a name="create-a-session"></a>Vytvoření relace

Tento příkaz vytvoří relaci. Vrátí ID nové relace. ID relace potřebujete pro všechny ostatní příkazy.

| Identifikátor URI | Metoda |
|-----------|:-----------|
| /v1/účty/*accountId*/sessions/create | POST |

**Tělo požadavku:**

* maxLeaseTime (časový limit): hodnota časového limitu, když bude virtuální med automaticky vyřazen z provozu
* modely (pole): adresy URL kontejneru datových zdrojů pro předběžné načtení
* velikost (řetězec): velikost virtuálního počítače (**"standardní"** nebo **"premium"**). Viz omezení velikosti konkrétního [virtuálního počítače](../reference/limits.md#overall-number-of-polygons).

**Reakce:**

| Kód stavu | Datová část JSON | Komentáře |
|-----------|:-----------|:-----------|
| 202 | - sessionId: GUID | Úspěch |

### <a name="example-script-create-a-session"></a>Příklad skriptu: Vytvoření relace

```PowerShell
Invoke-WebRequest -Uri "$endPoint/v1/accounts/$accountId/sessions/create" -Method Post -ContentType "application/json" -Body "{ 'maxLeaseTime': '4:0:0', 'models': [], 'size': 'standard' }" -Headers @{ Authorization = "Bearer $token" }
```

Příklad výstupu:

```PowerShell
StatusCode        : 202
StatusDescription : Accepted
Content           : {"sessionId":"d31bddca-dab7-498e-9bc9-7594bc12862f"}
RawContent        : HTTP/1.1 202 Accepted
                    MS-CV: 5EqPJ1VdTUakDJZc6/ZhTg.0
                    Content-Length: 52
                    Content-Type: application/json; charset=utf-8
                    Date: Thu, 09 May 2019 16:17:50 GMT
                    Location: accounts/11111111-1111-1111-11...
Forms             : {}
Headers           : {[MS-CV, 5EqPJ1VdTUakDJZc6/ZhTg.0], [Content-Length, 52], [Content-Type, application/json;
                    charset=utf-8], [Date, Thu, 09 May 2019 16:17:50 GMT]...}
Images            : {}
InputFields       : {}
Links             : {}
ParsedHtml        : mshtml.HTMLDocumentClass
RawContentLength  : 52
```

### <a name="example-script-store-sessionid"></a>Příklad skriptu: Store sessionId

Odpověď z výše uvedenéžádosti obsahuje **sessionId**, které potřebujete pro všechny žádosti o zpracování.

```PowerShell
$sessionId = "d31bddca-dab7-498e-9bc9-7594bc12862f"
```

## <a name="update-a-session"></a>Aktualizace relace

Tento příkaz aktualizuje parametry relace. V současné době můžete pouze prodloužit dobu zapůjčení relace.

> [!IMPORTANT]
> Doba zapůjčení je vždy uvedena jako celková doba od začátku relace. To znamená, že pokud jste vytvořili relaci s dobou zapůjčení jedné hodiny a chcete prodloužit dobu zapůjčení o další hodinu, budete muset aktualizovat jeho maxLeaseTime na dvě hodiny.

| Identifikátor URI | Metoda |
|-----------|:-----------|
| /v1/účty/*accountID*/sessions/*sessionId* | Oprava |

**Tělo požadavku:**

* maxLeaseTime (časový limit): hodnota časového limitu, když bude virtuální med automaticky vyřazen z provozu

**Reakce:**

| Kód stavu | Datová část JSON | Komentáře |
|-----------|:-----------|:-----------|
| 200 | | Úspěch |

### <a name="example-script-update-a-session"></a>Příklad skriptu: Aktualizace relace

```PowerShell
Invoke-WebRequest -Uri "$endPoint/v1/accounts/$accountId/sessions/$sessionId" -Method Patch -ContentType "application/json" -Body "{ 'maxLeaseTime': '5:0:0' }" -Headers @{ Authorization = "Bearer $token" }
```

Příklad výstupu:

```PowerShell
StatusCode        : 200
StatusDescription : OK
Content           : {}
RawContent        : HTTP/1.1 200 OK
                    MS-CV: Fe+yXCJumky82wuoedzDTA.0
                    Content-Length: 0
                    Date: Thu, 09 May 2019 16:27:31 GMT


Headers           : {[MS-CV, Fe+yXCJumky82wuoedzDTA.0], [Content-Length, 0], [Date, Thu, 09 May 2019 16:27:31 GMT]}
RawContentLength  : 0
```

## <a name="get-active-sessions"></a>Získání aktivních relací

Tento příkaz vrátí seznam aktivních relací.

| Identifikátor URI | Metoda |
|-----------|:-----------|
| /v1/účty/*accountId*/sessions | GET |

**Reakce:**

| Kód stavu | Datová část JSON | Komentáře |
|-----------|:-----------|:-----------|
| 200 | - relace: pole vlastností relace | Popis vlastností relace naleznete v části Získat vlastnosti relace |

### <a name="example-script-query-active-sessions"></a>Příklad skriptu: Dotaz na aktivní relace

```PowerShell
Invoke-WebRequest -Uri "$endPoint/v1/accounts/$accountId/sessions" -Method Get -Headers @{ Authorization = "Bearer $token" }
```

Příklad výstupu:

```PowerShell
StatusCode        : 200
StatusDescription : OK
Content           : []
RawContent        : HTTP/1.1 200 OK
                    MS-CV: WfB9Cs5YeE6S28qYkp7Bhw.1
                    Content-Length: 15
                    Content-Type: application/json; charset=utf-8
                    Date: Thu, 25 Jul 2019 16:23:50 GMT

                    {"sessions":[]}
Forms             : {}
Headers           : {[MS-CV, WfB9Cs5YeE6S28qYkp7Bhw.1], [Content-Length, 2], [Content-Type, application/json;
                    charset=utf-8], [Date, Thu, 25 Jul 2019 16:23:50 GMT]}
Images            : {}
InputFields       : {}
Links             : {}
ParsedHtml        : mshtml.HTMLDocumentClass
RawContentLength  : 2
```

## <a name="get-sessions-properties"></a>Získání vlastností relací

Tento příkaz vrátí informace o relaci, jako je například název hostitele virtuálního soudu.

| Identifikátor URI | Metoda |
|-----------|:-----------|
| /v1/účty/*accountId*/sessions/*sessionId*/properties | GET |

**Reakce:**

| Kód stavu | Datová část JSON | Komentáře |
|-----------|:-----------|:-----------|
| 200 | - zpráva: řetězec<br/>- sessionElapsedTime: časový interval<br/>- sessionHostname: řetězec<br/>- sessionId: řetězec<br/>- sessionMaxLeaseTime: časový rozpětí<br/>- sessionVelikost: výčet<br/>- sessionStatus: výčet | Enum sessionStatus { spuštění, připravenost, zastavení, zastavení, její platnost, chyba}<br/>Pokud je stav "chyba" nebo "vypršela platnost", bude zpráva obsahovat další informace |

### <a name="example-script-get-session-properties"></a>Příklad skriptu: Získat vlastnosti relace

```PowerShell
Invoke-WebRequest -Uri "$endPoint/v1/accounts/$accountId/sessions/$sessionId/properties" -Method Get -Headers @{ Authorization = "Bearer $token" }
```

Příklad výstupu:

```PowerShell
StatusCode        : 200
StatusDescription : OK
Content           : {"message":null,"sessionElapsedTime":"00:00:01","sessionHostname":"5018fee8-817e-4366-9179-556af79a4240.remoterenderingvm.westeurope.mixedreality.azure.com","sessionId":"e13d2c44-63e0-4591-991e-f9e05e599a93","sessionMaxLeaseTime":"04:00:00","sessionStatus":"Ready"}
RawContent        : HTTP/1.1 200 OK
                    MS-CV: CMXegpZRMECH4pbOW2j5GA.0
                    Content-Length: 60
                    Content-Type: application/json; charset=utf-8
                    Date: Thu, 09 May 2019 16:30:38 GMT

                    {"message":null,...
Forms             : {}
Headers           : {[MS-CV, CMXegpZRMECH4pbOW2j5GA.0], [Content-Length, 60], [Content-Type, application/json;
                    charset=utf-8], [Date, Thu, 09 May 2019 16:30:38 GMT]}
Images            : {}
InputFields       : {}
Links             : {}
ParsedHtml        : mshtml.HTMLDocumentClass
RawContentLength  : 60
```

## <a name="stop-a-session"></a>Zastavení relace

Tento příkaz zastaví relaci. Přidělený virtuální virtuální město bude vyučován krátce poté.

| Identifikátor URI | Metoda |
|-----------|:-----------|
| /v1/účty/*accountId*/sessions/*sessionId* | DELETE |

**Reakce:**

| Kód stavu | Datová část JSON | Komentáře |
|-----------|:-----------|:-----------|
| 204 | | Úspěch |

### <a name="example-script-stop-a-session"></a>Příklad skriptu: Zastavení relace

```PowerShell
Invoke-WebRequest -Uri "$endPoint/v1/accounts/$accountId/sessions/$sessionId" -Method Delete -Headers @{ Authorization = "Bearer $token" }
```

Příklad výstupu:

```PowerShell
StatusCode        : 204
StatusDescription : No Content
Content           : {}
RawContent        : HTTP/1.1 204 No Content
                    MS-CV: YDxR5/7+K0KstH54WG443w.0
                    Date: Thu, 09 May 2019 16:45:41 GMT


Headers           : {[MS-CV, YDxR5/7+K0KstH54WG443w.0], [Date, Thu, 09 May 2019 16:45:41 GMT]}
RawContentLength  : 0
```

## <a name="next-steps"></a>Další kroky

* [Příklad skriptů prostředí PowerShell](../samples/powershell-example-scripts.md)
