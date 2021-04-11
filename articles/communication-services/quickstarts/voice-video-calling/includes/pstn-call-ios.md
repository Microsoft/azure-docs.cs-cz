---
author: nikuklic
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: nikuklic
ms.openlocfilehash: 43e3463a3284f57825073888146b38fa14cbf5d3
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105109028"
---
[!INCLUDE [Emergency Calling Notice](../../../includes/emergency-calling-notice-include.md)]
## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Nasazený prostředek komunikačních služeb. [Vytvořte prostředek služby Communications](../../create-communication-resource.md).
- Telefonní číslo získané v prostředku komunikačních služeb. [Jak získat telefonní číslo](../../telephony-sms/get-phone-number.md).
- A `User Access Token` povolí klienta volání. Další informace o [tom, jak získat `User Access Token` ](../../access-tokens.md)
- Dokončete rychlé [zprovoznění, abyste mohli začít s přidáváním volání do aplikace](../getting-started-with-calling.md) .

### <a name="prerequisite-check"></a>Kontrola požadovaných součástí

- Chcete-li zobrazit telefonní čísla přidružená ke zdroji komunikačních služeb, přihlaste se k [Azure Portal](https://portal.azure.com/), vyhledejte prostředek komunikačních služeb a otevřete kartu **telefonní čísla** v levém navigačním podokně.
- Můžete sestavit a spustit aplikaci pomocí komunikačních služeb Azure, které volají sadu SDK pro iOS:

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
            self.call = self.callAgent!.startCall([PhoneNumber(phoneNumber: self.callee)], options: startCallOptions)
            self.callDelegate = CallDelegate(self)
            self.call!.delegate = self.callDelegate
        }
    }
}
```

## <a name="run-the-code"></a>Spuštění kódu

Můžete sestavit a spustit aplikaci v simulátoru iOS tak, že vyberete možnost  >  **spuštění** produktu nebo pomocí klávesové zkratky (&#8984;-R).

![Konečný vzhled a chování aplikace rychlý Start](../media/ios/quick-start-make-call.png)

Volání telefonu můžete uskutečnit zadáním telefonního čísla v přidaném textovém poli a kliknutím na tlačítko **Spustit volání** .
> [!WARNING]
> Všimněte si, že telefonní čísla by měla být zadána ve formátu E. 164 mezinárodní standard. (např.: + 12223334444)

> [!NOTE]
> Při prvním volání vás systém vyzve k zadání přístupu k mikrofonu. V produkční aplikaci byste měli použít `AVAudioSession` rozhraní API, abyste [zkontrolovali stav oprávnění](https://developer.apple.com/documentation/uikit/protecting_the_user_s_privacy/requesting_access_to_protected_resources) a korektně aktualizovali chování aplikace, když není udělené oprávnění.
