---
author: nikuklic
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: nikuklic
ms.openlocfilehash: 96f5fc868d4fa090848096174eb6fcdd34ef8388
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2021
ms.locfileid: "104729477"
---
[!INCLUDE [Emergency Calling Notice](../../../includes/emergency-calling-notice-include.md)]
## <a name="prerequisites"></a>Předpoklady

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Nasazený prostředek komunikačních služeb. [Vytvořte prostředek služby Communications](../../create-communication-resource.md).
- Telefonní číslo získané v prostředku komunikačních služeb. [Jak získat telefonní číslo](../../telephony-sms/get-phone-number.md).
- A `User Access Token` povolí klienta volání. Další informace o [tom, jak získat `User Access Token` ](../../access-tokens.md)
- Dokončete rychlé [zprovoznění, abyste mohli začít s přidáváním volání do aplikace](../getting-started-with-calling.md) .

### <a name="prerequisite-check"></a>Kontrola požadovaných součástí

- Chcete-li zobrazit telefonní čísla přidružená ke zdroji komunikačních služeb, přihlaste se k [Azure Portal](https://portal.azure.com/), vyhledejte prostředek komunikačních služeb a otevřete kartu **telefonní čísla** v levém navigačním podokně.

## <a name="setting-up"></a>Nastavení

### <a name="add-pstn-functionality-to-your-app"></a>Přidání funkce veřejné sítě do vaší aplikace

`PhoneNumber`Do své aplikace můžete přidat typ úpravou **MainActivity. Java**:


```java
import com.azure.android.communication.common.PhoneNumberIdentifier;
```

<!--
> [!TBD]
> Namespace based on input from Komivi Agbakpem. But it does not correlates with other use namespaces in Calling Quickstart. E.g: "com.azure.communication.calling.CommunicationUserIdentifier" or "com.azure.communication.common.client.CommunicationTokenCredential". Double-chek this.
-->

## <a name="start-a-call-to-phone"></a>Spustit telefonování

Zadejte telefonní číslo, které jste získali v rámci svého prostředku komunikační služby. Tato akce se použije ke spuštění volání:

> [!WARNING]
> Všimněte si, že telefonní čísla by měla být zadána ve formátu E. 164 mezinárodní standard. (např.: + 12223334444)

Úprava `startCall()` obslužné rutiny události v **MainActivity. Java**, která zpracovává telefonní hovory:

```java
    private void startCall() {
        EditText calleePhoneView = findViewById(R.id.callee_id);
        String calleePhone = calleePhoneView.getText().toString();
        PhoneNumberIdentifier callerPhone = new PhoneNumberIdentifier("+12223334444");
        StartCallOptions options = new StartCallOptions();
        options.setAlternateCallerId(callerPhone);
        options.setVideoOptions(new VideoOptions(null));
        call = agent.startCall(
                getApplicationContext(),
                new PhoneNumberIdentifier[] {new PhoneNumberIdentifier(calleePhone)},
                options);
    }
```

## <a name="launch-the-app-and-call-the-echo-bot"></a>Spusťte aplikaci a zavolejte robota s odezvou.

Aplikaci teď můžete spustit pomocí tlačítka Spustit aplikaci na panelu nástrojů (Shift + F10). Volání telefon můžete uskutečnit zadáním telefonního čísla v přidaném textovém poli a kliknutím na tlačítko **zavolat** .
> [!WARNING]
> Všimněte si, že telefonní čísla by měla být zadána ve formátu E. 164 mezinárodní standard. (např.: + 12223334444)

![Snímek obrazovky znázorňující dokončenou aplikaci](../media/android/quickstart-android-call-pstn.png)
