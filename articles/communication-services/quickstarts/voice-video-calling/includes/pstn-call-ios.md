---
author: nikuklic
ms.service: azure-communication-services
ms.topic: include
ms.date: 9/11/2020
ms.author: nikuklic
ms.openlocfilehash: 63fd0af819fde7d78df289a1b8f5cefa2e415101
ms.sourcegitcommit: d9ba60f15aa6eafc3c5ae8d592bacaf21d97a871
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2020
ms.locfileid: "91779903"
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
- Můžete sestavit a spustit aplikaci pomocí komunikačních služeb Azure s voláním klientské knihovny pro iOS:

## <a name="setting-up"></a>Nastavení

## <a name="start-a-call-to-phone"></a>Spustit telefonování

Zadejte telefonní číslo, které jste získali v prostředku služby Communications Services, který se použije ke spuštění volání:
> [!WARNING]
> Všimněte si, že telefonní čísla by měla být zadána ve formátu E. 164 mezinárodní standard. (např.: + 12223334444)

Úprava `startCall` obslužné rutiny události, která bude provedena při klepnutí na tlačítko *Spustit volání* :

```swift
func startCall() {
    // Ask permissions
    AVAudioSession.sharedInstance().requestRecordPermission { (granted) in
        if granted {
            let startCallOptions = ACSStartCallOptions()
            startCallOptions!.alternateCallerID = PhoneNumber(phoneNumber: "+12223334444")
            self.call = self.callAgent!.call([PhoneNumber(phoneNumber: self.callee)], options: startCallOptions)
            self.callDelegate = CallDelegate(self)
            self.call!.delegate = self.callDelegate
        }
    }
}
```

## <a name="run-the-code"></a>Spuštění kódu

Můžete sestavit a spustit aplikaci v simulátoru iOS tak, že **Product**vyberete možnost  >  **spuštění** produktu nebo pomocí klávesové zkratky (&#8984;-R).

![Konečný vzhled a chování aplikace rychlý Start](../media/ios/quick-start-make-call.png)

Volání telefonu můžete uskutečnit zadáním telefonního čísla v přidaném textovém poli a kliknutím na tlačítko **Spustit volání** .
> [!WARNING]
> Všimněte si, že telefonní čísla by měla být zadána ve formátu E. 164 mezinárodní standard. (např.: + 12223334444)

> [!NOTE]
> Při prvním volání vás systém vyzve k zadání přístupu k mikrofonu. V produkční aplikaci byste měli použít `AVAudioSession` rozhraní API, abyste [zkontrolovali stav oprávnění](https://developer.apple.com/documentation/uikit/protecting_the_user_s_privacy/requesting_access_to_protected_resources) a korektně aktualizovali chování aplikace, když není udělené oprávnění.
