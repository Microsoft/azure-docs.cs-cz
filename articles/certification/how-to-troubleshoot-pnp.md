---
title: Řešení potíží se zařízením IoT technologie Plug and Play
description: Průvodce doporučenými kroky pro řešení potíží pro partnery potvrzující zařízení IoT technologie Plug and Play.
author: nkuntjoro
ms.author: nikuntjo
ms.service: certification
ms.topic: how-to
ms.date: 04/15/2021
ms.custom: template-how-to
ms.openlocfilehash: 9b406e489fb83083d47f01e1483160181601d518
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2021
ms.locfileid: "107559064"
---
# <a name="troubleshoot-your-iot-plug-and-play-certification-project"></a>Řešení potíží s certifikací projektu technologie Plug and Play IoT

Během testovací fáze & v rámci vašeho projektu IoT technologie Plug and Play se můžete setkat s některými scénáři, které vám zabrání v předávání testování služby certifikace Azure for IoT (AICS).

## <a name="prerequisites"></a>Požadavky

- Měli byste být přihlášeni a mít projekt pro vaše zařízení vytvořený na [portálu zařízení s certifikací Azure](https://certify.azure.com). Další informace najdete v tomto [kurzu](tutorial-01-creating-your-project.md).

## <a name="when-aics-tests-arent-passing"></a>Když AICS testy neprojde

AICS test se nemůže předat z důvodu několika příčin. Pomocí těchto kroků můžete vyhledat běžné problémy a řešit problémy s vaším zařízením.

1. Ověřte, že kód zařízení nastavuje datovou část ID modelu během zřizování DPS. To je požadavek, aby AICS ověřil vaše zařízení.
1. Protokoly telemetrie z předchozích testovacích běhů můžete zobrazit tak, že stisknete `View Logs` tlačítko a určíte, co způsobuje selhání testu. Testovací zprávy i nezpracovaná data jsou k dispozici ke kontrole.  

    ![Kontrola testovacích dat](./media/images/review-logs.png)

1. V některých případech, kde protokoly signalizují `Failed to get Digital Twin Model ID of device xx due to DeviceNotConnected` , zkuste zařízení restartovat a restartujte proces zřizování zařízení.
1. Pokud automatizované testy stále selžou, můžete `request a manual review` nahradit výsledky. Tím se aktivuje žádost o **Ruční ověření** u týmu zařízení s certifikací Azure.  

    ![Žádost o ruční kontrolu](./media/images/request-manual-review.png)

## <a name="when-you-see-passed-with-warnings"></a>Když se zobrazí zpráva "dokončeno s upozorněními"

Při spuštění testů, pokud se zobrazí výsledek `Passed with warnings` , znamená to, že během zkušebního období nebyla obdržena nějaká telemetrie. To může být způsobeno závislostí telemetrie v delších časových intervalech nebo externími triggery, které nebyly k dispozici. Můžete pokračovat v odesílání zařízení k revizi, během kterého bude kontroler určovat, jestli je v budoucnu potřeba **Ruční ověřování** .

## <a name="when-you-need-help-with-the-model-repository"></a>Pokud potřebujete pomáhat s úložištěm modelu

Problémy související s úložištěm modelu pro IoT technologie Plug and Play najdete v [našich pokynech k dokumentaci k úložišti modelů zařízení](https://docs.microsoft.com/azure/iot-pnp/concepts-model-repository).

## <a name="next-steps"></a>Další kroky

Snad tento průvodce vám pomůže pokračovat ve své cestě k certifikaci technologie Plug and Play IoT! Jakmile předáte AICS, můžete pokračovat v našich kurzech a odesílat a publikovat vaše zařízení.

- [Kurz: testování zařízení](tutorial-03-testing-your-device.md)
