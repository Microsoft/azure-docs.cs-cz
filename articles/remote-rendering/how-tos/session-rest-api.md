---
title: REST API správy relací
description: Popisuje, jak spravovat relace.
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 0af9d6906e038a4b9285a2c302fc0c98345fdbd9
ms.sourcegitcommit: 70ee014d1706e903b7d1e346ba866f5e08b22761
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2020
ms.locfileid: "90023750"
---
# <a name="use-the-session-management-rest-api"></a>Použití rozhraní REST API pro správu relací

Pokud chcete používat funkci vzdáleného vykreslování Azure, musíte vytvořit *relaci*. Každá relace odpovídá virtuálnímu počítači, který se přiřazuje v Azure, a čeká na připojení klientského zařízení. Když se zařízení připojí, virtuální počítač vykreslí požadovaná data a zachová výsledek jako datový proud videa. Během vytváření relace jste zvolili druh serveru, na kterém chcete běžet, což určuje ceny. Jakmile již relace není potřebná, měla by být zastavena. Pokud se nezastaví ručně, automaticky se ukončí, až vyprší *doba zapůjčení* relace.

Ve složce *Scripts* poskytujeme skript prostředí PowerShell *RenderingSession.ps1*, který ukazuje použití naší služby v [úložišti ukázek ARR](https://github.com/Azure/azure-remote-rendering) . Skript a jeho konfigurace jsou popsané tady: [Příklady skriptů PowerShellu](../samples/powershell-example-scripts.md)

> [!TIP]
> Příkazy PowerShellu uvedené na této stránce jsou určeny k tomu, aby je bylo možné doplnit. Pokud všechny skripty spouštíte v rámci stejného příkazového řádku PowerShellu, sestaví se na sebe navzájem.

## <a name="regions"></a>Oblasti

Přečtěte si [seznam dostupných oblastí](../reference/regions.md) pro základní adresy URL, na které se mají požadavky odesílat.

Pro ukázkové skripty jsme zvolili region *westus2*(oblast).

### <a name="example-script-choose-an-endpoint"></a>Ukázkový skript: volba koncového bodu

```PowerShell
$endPoint = "https://remoterendering.westus2.mixedreality.azure.com"
```

## <a name="accounts"></a>Účty

Pokud nemáte účet vzdáleného vykreslování, [vytvořte ho](create-an-account.md). Každý prostředek je identifikován *accountId*, který se používá v rámci celé rozhraní API relace.

### <a name="example-script-set-accountid-and-accountkey"></a>Ukázkový skript: nastavení accountId a accountKey

```PowerShell
$accountId = "********-****-****-****-************"
$accountKey = "*******************************************="
```

## <a name="common-request-headers"></a>Běžné hlavičky požadavků

* *Autorizační* hlavička musí mít hodnotu `Bearer TOKEN` , kde `TOKEN` je ověřovací token [vrácený službou tokenu zabezpečení](tokens.md).

### <a name="example-script-request-a-token"></a>Příklad skriptu: vyžádání tokenu

```PowerShell
[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12
$webResponse = Invoke-WebRequest -Uri "https://sts.mixedreality.azure.com/accounts/$accountId/token" -Method Get -ContentType "application/json" -Headers @{ Authorization = "Bearer ${accountId}:$accountKey" }
$response = ConvertFrom-Json -InputObject $webResponse.Content
$token = $response.AccessToken;
```

## <a name="common-response-headers"></a>Běžné hlavičky odpovědí

* Produktový tým může použít hlavičku *MS-CV* k trasování volání v rámci služby.

## <a name="create-a-session"></a>Vytvoření relace

Tento příkaz vytvoří relaci. Vrátí ID nové relace. Budete potřebovat ID relace pro všechny ostatní příkazy.

| Identifikátor URI | Metoda |
|-----------|:-----------|
| /V1/Accounts/*accountId*/Sessions/Create | POST |

**Text žádosti:**

* maxLeaseTime (TimeSpan): hodnota časového limitu v případě, že se relace automaticky vyřadí z provozu
* modely (pole): adresy URL kontejneru assetů k přednačtení
* velikost (String): velikost serveru, která se má konfigurovat ([**"Standard"**](../reference/vm-sizes.md) nebo [**"Premium"**](../reference/vm-sizes.md)). Podívejte se na určitá [omezení velikosti](../reference/limits.md#overall-number-of-polygons).

**Požadavků**

| Stavový kód | Datová část JSON | Komentáře |
|-----------|:-----------|:-----------|
| 202 | -sessionId: GUID | Success |

### <a name="example-script-create-a-session"></a>Ukázkový skript: vytvoření relace

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

### <a name="example-script-store-sessionid"></a>Ukázkový skript: Uložit sessionId

Odpověď od výše uvedeného požadavku zahrnuje **identifikátor SessionID**, který potřebujete pro všechny žádosti o další zpracování.

```PowerShell
$sessionId = "d31bddca-dab7-498e-9bc9-7594bc12862f"
```

## <a name="modify-and-query-session-properties"></a>Úprava a dotazování vlastností relace

Existuje několik příkazů pro dotazování nebo úpravu parametrů existujících relací.

> [!CAUTION]
> Stejně jako u všech volání REST odesílají tyto příkazy příliš často, což způsobí, že server bude omezovat a vracet chybu nakonec. Stavový kód v tomto případě je 429 (příliš mnoho požadavků). Jako pravidlo pro palec by se měla **mezi následnými voláními čekat 5-10 sekund**.

### <a name="update-session-parameters"></a>Aktualizovat parametry relace

Tento příkaz aktualizuje parametry relace. V současné době můžete pouze roztáhnout dobu zapůjčení relace.

> [!IMPORTANT]
> Doba zapůjčení je vždy dána jako celková doba od začátku relace. To znamená, že pokud jste vytvořili relaci s časem zapůjčení za jednu hodinu a chcete prodloužení doby zapůjčení na jinou hodinu, musíte aktualizovat její maxLeaseTime na dvě hodiny.

| Identifikátor URI | Metoda |
|-----------|:-----------|
| /V1/Accounts/*accountid*/Sessions/*SessionID* | POUŽITA |

**Text žádosti:**

* maxLeaseTime (TimeSpan): hodnota časového limitu v případě, že se relace automaticky vyřadí z provozu

**Požadavků**

| Stavový kód | Datová část JSON | Komentáře |
|-----------|:-----------|:-----------|
| 200 | | Success |

#### <a name="example-script-update-a-session"></a>Ukázkový skript: aktualizace relace

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

### <a name="get-active-sessions"></a>Získat aktivní relace

Tento příkaz vrátí seznam aktivních relací.

| Identifikátor URI | Metoda |
|-----------|:-----------|
| /V1/Accounts/*accountId*/Sessions | GET |

**Požadavků**

| Stavový kód | Datová část JSON | Komentáře |
|-----------|:-----------|:-----------|
| 200 | -Sessions: pole vlastností relace | Popis vlastností relace najdete v části získání vlastností relace. |

#### <a name="example-script-query-active-sessions"></a>Ukázkový skript: dotaz na aktivní relace

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

### <a name="get-sessions-properties"></a>Získat vlastnosti relací

Tento příkaz vrátí informace o relaci, jako je název hostitele virtuálního počítače.

| Identifikátor URI | Metoda |
|-----------|:-----------|
| /V1/Accounts/*accountId*/Sessions/*SessionID*/Properties | GET |

**Požadavků**

| Stavový kód | Datová část JSON | Komentáře |
|-----------|:-----------|:-----------|
| 200 | -Message: řetězec<br/>-sessionElapsedTime: TimeSpan<br/>-sessionHostname: String<br/>-sessionId: String<br/>-sessionMaxLeaseTime: TimeSpan<br/>-sessionSize: Enum<br/>-sessionStatus: Enum | vyčíslení výčtu sessionStatus {Start, Read, Stopped, Stopped, vypršela platnost, chyba}<br/>Pokud je stav "Error" nebo "vypršela", zpráva bude obsahovat další informace |

#### <a name="example-script-get-session-properties"></a>Příklad skriptu: získání vlastností relace

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

Tento příkaz zastaví relaci. Přidělený virtuální počítač se po uplynutí chvilky uvolní.

| Identifikátor URI | Metoda |
|-----------|:-----------|
| /V1/Accounts/*accountId*/Sessions/*SessionID* | DELETE |

**Požadavků**

| Stavový kód | Datová část JSON | Komentáře |
|-----------|:-----------|:-----------|
| 204 | | Success |

### <a name="example-script-stop-a-session"></a>Ukázkový skript: zastavení relace

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

* [Ukázkové skripty PowerShellu](../samples/powershell-example-scripts.md)
