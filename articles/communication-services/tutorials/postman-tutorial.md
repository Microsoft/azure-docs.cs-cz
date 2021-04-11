---
title: Kurz – podepsání a odeslání požadavků na rozhraní API služby ACS pro SMS pomocí služby provedené
titleSuffix: An Azure Communication Services tutorial
description: Naučte se podepisovat a posílat žádosti pro službu ACS pomocí nástroje post za účelem odeslání zprávy SMS.
author: ProbablePrime
services: azure-communication-services
ms.author: rifox
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 5805734a9253962d672a4236a5650e9de8b37f0a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105044290"
---
# <a name="tutorial-sign-and-make-requests-with-postman"></a>Kurz: podepsání a vytvoření požadavků pomocí metody post
V tomto kurzu nastavíme a použijeme post k vytvoření požadavku na služby Azure Communication Services (ACS) pomocí protokolu HTTP. Po skončení tohoto kurzu jste úspěšně odeslali zprávu SMS pomocí služby ACS a post a mohli byste využít post k prozkoumání dalších rozhraní API v rámci služby ACS.

V tomto kurzu budeme:
> [!div class="checklist"]
> * Stahuje se post.
> * Nastavení metody post pro podepisování požadavků HTTP
> * Vytvoření požadavku na službu ACS rozhraní API služby ACS pro odeslání zprávy

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. Podrobnosti najdete v článku o [Vytvoření účtu zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). Bezplatný účet vám poskytne $200 kreditů Azure k vyzkoušení libovolné kombinace služeb.
- Aktivní prostředek komunikační služby a připojovací řetězec. [Naučte se vytvořit prostředek komunikačních služeb](../quickstarts/create-communication-resource.md).
- Telefonní číslo služby ACS, které může odesílat zprávy SMS, najdete je v části [získání telefonního čísla](../quickstarts/telephony-sms/get-phone-number.md) .

## <a name="downloading-and-installing-postman"></a>Stažení a instalace poštovního programu

Post, je desktopová aplikace, která dokáže vystavovat požadavky rozhraní API na jakékoli rozhraní API HTTP. Obvykle se používá pro testování a prozkoumávání rozhraní API. Nejnovější [verzi desktopu si stáhnete z webu pro odeslání](https://www.postman.com/downloads/). Post má verze pro Windows, Mac a Linux, takže si stáhněte verzi vhodnou pro váš operační systém. Po stažení otevřete aplikaci. Zobrazí se obrazovka Start, která vás vyzve, abyste se přihlásili nebo vytvořili účet pro odeslání. Vytvoření účtu je volitelné a můžete ho přeskočit kliknutím na odkaz Přeskočit a přejít k aplikaci. Při vytvoření účtu se nastavení požadavku rozhraní API uloží na post, což vám pak umožní vybrat si žádosti na jiných počítačích.

:::image type="content" source="media/postman/create-account-or-skip.png" alt-text="Úvodní obrazovka, která ukazuje možnost vytvořit účet nebo přeskočit a přejít na aplikaci.":::

Po vytvoření účtu nebo jeho přeskočení by se teď měl zobrazit hlavní okno pro post.

## <a name="creating-and-configuring-a-postman-collection"></a>Vytvoření a konfigurace kolekce post

Publikovaná aplikace může organizovat požadavky mnoha způsoby. Pro účely tohoto kurzu. Vytvoříme kolekci post. To provedete tak, že na levé straně aplikace vyberete tlačítko kolekce:

:::image type="content" source="media/postman/collections-tab.png" alt-text="Hlavní obrazovka odesílatele se zvýrazněnou kartou kolekce.":::

Po výběru klikněte na vytvořit novou kolekci a spusťte proces vytváření kolekce. V prostřední oblasti příspěvku se otevře nová karta. Pojmenujte kolekci libovolným způsobem. Tady se kolekce jmenuje "ACS":

:::image type="content" source="media/postman/acs-collection.png" alt-text="Po otevření kolekce ACS a názvu zvýrazněné kolekce.":::

Po vytvoření kolekce a jejím názvem jste připraveni ji nakonfigurovat.

### <a name="adding-collection-variables"></a>Přidávání proměnných kolekce

Abychom mohli zpracovávat ověřování a snadněji podávat požadavky, budeme v nově vytvořené kolekci ACS zadávat dvě proměnné kolekce. Tyto proměnné jsou k dispozici pro všechny požadavky v kolekci služby ACS. Chcete-li začít s vytvářením proměnných, navštivte kartu Variable's kolekce.

:::image type="content" source="media/postman/variable-stab.png" alt-text="Publikovat pomocí karty proměnné kolekce ACS.":::

Na kartě kolekce vytvořte dvě proměnné:
- klíč – Tato proměnná by měla být jedním z vašich klíčů z klíčové stránky Azure Communication Services v rámci Azure Portal. Například, `oW...A==`.
- koncový bod – Tato proměnná by měla být koncovým bodem služby Azure Communications Services ze stránky Key. **Nezapomeňte odebrat koncové lomítko**. Například, `https://contoso.communication.azure.com`.

Zadejte tyto hodnoty do sloupce "počáteční hodnota" na obrazovce proměnné. Po zadání stiskněte tlačítko "trvale vše" hned nad tabulkou napravo. Po správném nakonfigurování by obrazovka pro publikování měla vypadat nějak takto:

:::image type="content" source="media/postman/acs-variables-set.png" alt-text="Vystavili jste správně nastavení proměnných kolekce ACS.":::

Další informace o proměnných si můžete přečíst [v dokumentaci k nim](https://learning.postman.com/docs/sending-requests/variables).

### <a name="creating-a-pre-request-script"></a>Vytvoření skriptu před vyžádáním

Dalším krokem je vytvoření skriptu před vyžádáním v rámci služby post. Skript před vyžádáním je skript, který se spouští před každým požadavkem v post a může změnit nebo změnit parametry požadavku vaším jménem. Použijeme to k podepsání našich požadavků HTTP, aby mohly být autorizované službami ACS. Další informace o požadavcích na podepisování si můžete [přečíst v našem průvodci při ověřování](/rest/api/communication/authentication).

Tento skript vytvoříme v rámci kolekce tak, aby běžel na všech žádostech v rámci kolekce. Provedete to tak, že v rámci karty kolekce kliknete na dílčí kartu "skript před vyžádáním".

:::image type="content" source="media/postman/start-pre-request-script.png" alt-text="Publikujte se skriptem předběžného požadavku kolekce služby ACS Sub-Tab vybraný.":::

Na této dílčí kartě můžete vytvořit skript před vyžádáním zadáním do textové oblasti níže. Může být snazší ho napsat v celém editoru kódu, jako je například [Visual Studio Code](https://code.visualstudio.com/) před vložením do po dokončení. V tomto kurzu procházíme každou část skriptu. Nebojte se, pokud chcete přejít na konec, pokud byste ho chtěli jenom zkopírovat do post a začít. Pojďme začít psát skript.

### <a name="writing-the-pre-request-script"></a>Zápis skriptu před vyžádáním

První věc, kterou vytvoříme, je vytvoření řetězce koordinovaného světového času (UTC) a jeho přidání do hlavičky "date" protokolu HTTP. Tento řetězec ukládáme i do proměnné pro pozdější použití při podepisování:

```JavaScript
// Set the Date header to our Date as a UTC String.
const dateStr = new Date().toUTCString();
pm.request.headers.upsert({key:'Date', value: dateStr});
```

Dále vyhodnotí text žádosti pomocí SHA 256 a umístíme ho do `x-ms-content-sha256` hlavičky. Příkaz post zahrnuje některé [standardní knihovny](https://learning.postman.com/docs/writing-scripts/script-references/postman-sandbox-api-reference/#using-external-libraries) pro použití algoritmu hash a globální podepisování, takže je nemusíme instalovat ani vyžadovat:

```JavaScript
// Hash the request body using SHA256 and encode it as Base64
const hashedBodyStr = CryptoJS.SHA256(pm.request.body.raw).toString(CryptoJS.enc.Base64)
// And add that to the header x-ms-content-sha256
pm.request.headers.upsert({
    key:'x-ms-content-sha256',
    value: hashedBodyStr
});
```

Nyní použijeme naši dříve zadanou proměnnou koncového bodu k nerozlišujeí hodnoty hlavičky hostitele HTTP. Musíme to udělat, protože záhlaví hostitele není nastavené, dokud se nezpracuje tento skript:

```JavaScript
// Get our previously specified endpoint variable
const endpoint = pm.variables.get('endpoint')
// Remove the https, prefix to create a suitable "Host" value
const hostStr = endpoint.replace('https://','');
```

Když se tyto informace vytvoří, můžeme teď vytvořit řetězec, který budeme podepisovat za požadavek HTTP. skládá se z několika dříve vytvořených hodnot:

```JavaScript
// This gets the part of our URL that is after the endpoint, for example in https://contoso.communication.azure.com/sms, it will get '/sms'
const url = pm.request.url.toString().replace('{{endpoint}}','');

// Construct our string which we'll sign, using various previously created values.
const stringToSign = pm.request.method + '\n' + url + '\n' + dateStr + ';' + hostStr + ';' + hashedBodyStr;
```

Nakonec musíme tento řetězec podepsat pomocí našeho klíče služby ACS a pak ho přidat do naší žádosti v `Authorization` hlavičce:

```JavaScript
// Decode our access key from previously created variables, into bytes from base64.
const key = CryptoJS.enc.Base64.parse(pm.variables.get('key'));
// Sign our previously calculated string with HMAC 256 and our key. Convert it to Base64.
const signature = CryptoJS.HmacSHA256(stringToSign, key).toString(CryptoJS.enc.Base64);

// Add our final signature in Base64 to the authorization header of the request.
pm.request.headers.upsert({
    key:'Authorization',
    value: "HMAC-SHA256 SignedHeaders=date;host;x-ms-content-sha256&Signature=" + signature
});
```

### <a name="the-final-pre-request-script"></a>Finální skript před vyžádáním

Konečný skript před vyžádáním by měl vypadat přibližně takto:

```JavaScript
// Set the Date header to our Date as a UTC String.
const dateStr = new Date().toUTCString();
pm.request.headers.upsert({key:'Date', value: dateStr});

// Hash the request body using SHA256 and encode it as Base64
const hashedBodyStr = CryptoJS.SHA256(pm.request.body.raw).toString(CryptoJS.enc.Base64)
// And add that to the header x-ms-content-sha256
pm.request.headers.upsert({
    key:'x-ms-content-sha256',
    value: hashedBodyStr
});

// Get our previously specified endpoint variable
const endpoint = pm.variables.get('endpoint')
// Remove the https, prefix to create a suitable "Host" value
const hostStr = endpoint.replace('https://','');

// This gets the part of our URL that is after the endpoint, for example in https://contoso.communication.azure.com/sms, it will get '/sms'
const url = pm.request.url.toString().replace('{{endpoint}}','');

// Construct our string which we'll sign, using various previously created values.
const stringToSign = pm.request.method + '\n' + url + '\n' + dateStr + ';' + hostStr + ';' + hashedBodyStr;

// Decode our access key from previously created variables, into bytes from base64.
const key = CryptoJS.enc.Base64.parse(pm.variables.get('key'));
// Sign our previously calculated string with HMAC 256 and our key. Convert it to Base64.
const signature = CryptoJS.HmacSHA256(stringToSign, key).toString(CryptoJS.enc.Base64);

// Add our final signature in Base64 to the authorization header of the request.
pm.request.headers.upsert({
    key:'Authorization',
    value: "HMAC-SHA256 SignedHeaders=date;host;x-ms-content-sha256&Signature=" + signature
});
```

Do textové oblasti v rámci karty skript před vyžádáním zadejte nebo vložte tento finální skript:

:::image type="content" source="media/postman/finish-pre-request.png" alt-text="Odesilatel se zadává skript předběžného požadavku kolekce ACS.":::

Po zadání stiskněte kombinaci kláves CTRL + S nebo stiskněte tlačítko Uložit. tím se skript uloží do kolekce. 

:::image type="content" source="media/postman/save-pre-request-script.png" alt-text="Tlačítko pro uložení skriptu před vyžádáním":::

## <a name="creating-a-request-in-postman"></a>Vytváření žádosti v post

Teď, když je všechno nastavené, jsme připraveni vytvořit žádost ACS v rámci služby post. Začněte tím, že kliknete na ikonu Plus (+) vedle kolekce služby ACS:

:::image type="content" source="media/postman/create-request.png" alt-text="Tlačítko pro odeslání.":::

Tím se vytvoří nová karta pro náš požadavek v rámci služby post. V případě, že to vytvořil, musíme ho nakonfigurovat. Vyžádáme si požadavek na rozhraní API pro odeslání SMS, takže si nezapomeňte vyžádat [pomoc v dokumentaci k tomuto rozhraní API](/rest/api/communication/sms/send). Pojďme nakonfigurovat žádost post.

Začněte nastavením `POST` a zadáním `{{endpoint}}/sms?api-version=2021-03-07` do pole Adresa URL žádosti. Tato adresa URL používá naši dříve vytvořenou `endpoint` proměnnou k automatickému odeslání do vašeho prostředku ACS.

:::image type="content" source="media/postman/post-request-and-url.png" alt-text="Žádost o odeslání s typem nastaveným na POST a správně nastavenou adresou URL.":::

Teď vyberte kartu tělo žádosti a pak změňte přepínač níže na "raw". Napravo je rozevírací seznam, který říká "text", změní ho na JSON:

:::image type="content" source="media/postman/postman-json.png" alt-text="Nastavení textu žádosti na RAW a JSON":::

Tím se nakonfiguruje požadavek na odeslání a příjem informací ve formátu JSON.

V oblasti textu níže budete muset zadat text žádosti, který by měl být v následujícím formátu:

```JSON
{
    "from":"<Your ACS Telephone Number>",
    "message":"<The message you'd like to send>",
    "smsRecipients": [
        {
            "to":"<The number you'd like to send the message to>"
        }
    ]
}
```

V případě hodnoty "od" budete muset na portálu ACS [získat telefonní číslo](../quickstarts/telephony-sms/get-phone-number.md) , jak je uvedeno výše. Zadejte ho bez mezer a předpony v kódu země. Příklad: `+15555551234`. Zpráva může být libovolná, co byste chtěli odeslat, ale `Hello from ACS` je to dobrý příklad. Hodnota "do" by měla být telefon, ke kterému máte přístup, může přijímat zprávy SMS. Používání vlastního mobilního prostředí je vhodné.

Po zadání musíme tuto žádost Uložit do kolekce služby ACS, kterou jsme vytvořili dříve. Tím se zajistí, že se přestanou proměnné a skript před vyžádáním, který jsme vytvořili dříve. Uděláte to tak, že kliknete na tlačítko Uložit v pravém horním rohu oblasti požadavku.

:::image type="content" source="media/postman/postman-save.png" alt-text="Tlačítko Uložit pro žádost post":::

Tím se zobrazí dialogové okno s dotazem, co byste chtěli zavolat na žádost a kam je chcete uložit. Můžete si ho pojmenovat cokoli, co byste chtěli, ale v dolní polovině dialogového okna vyberte kolekci ACS:

:::image type="content" source="media/postman/postman-save-to-acs.png" alt-text="Dialog publikovat žádost o uložení s vybraným kolekcí služby ACS.":::

## <a name="sending-a-request"></a>Odesílá se žádost.

Teď, když je všechno nastavené, byste měli být schopni odeslat žádost a získat zprávu SMS na telefonu. Pokud to chcete provést, ujistěte se, že je vybraná vaše žádost, a pak stiskněte tlačítko Odeslat vpravo:

:::image type="content" source="media/postman/postman-send.png" alt-text="Žádost o odeslání s zvýrazněným tlačítkem Odeslat.":::

Pokud se všechno v dobrém stavu, měla by se teď zobrazit odpověď ze služby ACS, která by měla být 202 stavového kódu:

:::image type="content" source="media/postman/postman-202.png" alt-text="Žádost o odeslání byla úspěšně odeslána s kódem stavu 202.":::

Mobilní telefon, který vlastní číslo, které jste zadali v hodnotě "do", by měl také obsahovat zprávu SMS. Nyní máte vytvořenou pracovní skupinu, která může komunikovat se službami ACS a odesílat zprávy SMS.


## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Prozkoumejte rozhraní API](/rest/api/communication/) 
>  služby ACS [Přečtěte si další informace o ověřování](/rest/api/communication/authentication) 
>  [Další informace o nástroji pro publikování](https://learning.postman.com/)

Můžete také chtít:

- [Přidání chatu do aplikace](../quickstarts/chat/get-started.md)
- [Vytvořit tokeny přístupu uživatele](../quickstarts/access-tokens.md)
- [Informace o architektuře klientů a serverů](../concepts/client-and-server-architecture.md)
- [Další informace o ověřování](../concepts/authentication.md)