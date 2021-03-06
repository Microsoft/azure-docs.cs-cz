---
title: Konfigurace aplikace hlasového asistenta v rámci Azure Percept studia
description: Konfigurace aplikace hlasového asistenta v rámci Azure Percept studia
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/15/2021
ms.custom: template-how-to
ms.openlocfilehash: 8f22379049b74428787b738af832802081be7bf8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105022887"
---
# <a name="managing-your-voice-assistant"></a>Správa hlasového asistenta

Tento článek popisuje, jak nakonfigurovat klíčová slova a příkazy vaší aplikace hlasového asistenta v rámci služby [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819). Pokyny ke konfiguraci vašeho klíčového slova v rámci IoT Hub místo na portálu najdete v tomto [článku s postupem](./how-to-configure-voice-assistant.md).

Pokud jste ještě nevytvořili aplikaci hlasového asistenta, přečtěte si téma [Vytvoření hlasového asistenta bez kódu pomocí nástroje Azure Percept Studio a zvuku Azure Percept](./tutorial-no-code-speech.md).

## <a name="keyword-configuration"></a>Konfigurace klíčového slova

Klíčové slovo je slovo nebo krátká fráze sloužící k aktivaci hlasového asistenta. Například "Hey Cortana" je klíčové slovo pro pomocníka Cortana. Aktivace hlasu umožňuje uživatelům zahájit interakci s produktem zcela bez jakýchkoli rukou pouhým vyvoláním klíčového slova. Vzhledem k tomu, že váš produkt nepřetržitě naslouchá klíčovému slovu, je veškerý zvuk zpracován místně na zařízení, dokud nedojde k detekci, která zajistí, aby data uživatelů zůstala co možná soukromá.

### <a name="configuration-within-the-voice-assistant-demo-window"></a>Konfigurace v ukázkovém okně hlasového asistenta

1. Na stránce ukázka klikněte na **změnit** vedle **klíčového slova Custom** .

    :::image type="content" source="./media/manage-voice-assistant/hospitality-demo.png" alt-text="Snímek obrazovky s ukázkovým oknem pohostinství":::

    Pokud nemáte otevřenou ukázkovou stránku, přejděte na stránku zařízení (viz níže) a kliknutím na **test hlasového asistenta** v části **Akce** Získejte přístup k ukázce.

1. Vyberte jedno z dostupných klíčových slov a kliknutím na **Uložit** změny aplikujte.

1. Tři indikátory LED u zvukového zařízení Azure Percept se po dokončení konfigurace změní na jasně modrou (bez blesku) a váš hlasový asistent je připravený k použití.

### <a name="configuration-within-the-device-page"></a>Konfigurace na stránce zařízení

1. Na stránce Přehled v [Azure Percept studiu](https://go.microsoft.com/fwlink/?linkid=2135819)klikněte v levém podokně nabídky na **zařízení** .

    :::image type="content" source="./media/manage-voice-assistant/portal-overview-devices.png" alt-text="Snímek stránky s přehledem Azure Percept Studio se zvýrazněnými zařízeními":::

1. Vyberte zařízení, na které se nasadila vaše aplikace hlasového asistenta.

1. Otevřete kartu **řeč** .

    :::image type="content" source="./media/manage-voice-assistant/device-page.png" alt-text="Snímek stránky hraničního zařízení se zvýrazněnou kartou pro rozpoznávání řeči":::

1. Klikněte na **změnit** vedle **klíčového slova**.

    :::image type="content" source="./media/manage-voice-assistant/change-keyword-device.png" alt-text="Snímek obrazovky s dostupnými akcemi řešení řeči":::

1. Vyberte jedno z dostupných klíčových slov a kliknutím na **Uložit** změny aplikujte.

1. Tři indikátory LED u zvukového zařízení Azure Percept se po dokončení konfigurace změní na jasně modrou (bez blesku) a váš hlasový asistent je připravený k použití.

## <a name="create-a-custom-keyword"></a>Vytvoření vlastního klíčového slova

Pomocí nástroje [Speech Studio](https://speech.microsoft.com/)můžete vytvořit vlastní klíčové slovo pro hlasového asistenta. Výuka základního modelu klíčového slova může trvat až 30 minut.

Pokyny k vytvoření vlastního klíčového slova najdete v dokumentaci ke službě [Speech Studio](../cognitive-services/speech-service/custom-keyword-basics.md) . Po nakonfigurování bude vaše nové klíčové slovo k dispozici na portálu projektu Santa Cruz pro použití s vaší aplikací hlasového asistenta.

## <a name="commands-configuration"></a>Konfigurace příkazů

Vlastní příkazy usnadňují vytváření bohatých aplikací hlasového prostředí optimalizovaných pro práci s prvními interakcemi hlasu. Vlastní příkazy jsou nejvhodnější pro dokončování úkolů nebo příkazy a řízení.

### <a name="configuration-within-the-voice-assistant-demo-window"></a>Konfigurace v ukázkovém okně hlasového asistenta

1. Na stránce ukázka klikněte na **změnit** u **vlastního příkazu** . Pokud nemáte otevřenou ukázkovou stránku, přejděte na stránku zařízení (viz níže) a kliknutím na **test hlasového asistenta** v části **Akce** Získejte přístup k ukázce.

1. Vyberte jeden z dostupných vlastních příkazů a klikněte na **Uložit** , aby se změny projevily.

### <a name="configuration-within-the-device-page"></a>Konfigurace na stránce zařízení

1. Na stránce Přehled v [Azure Percept studiu](https://go.microsoft.com/fwlink/?linkid=2135819)klikněte v levém podokně nabídky na **zařízení** .

1. Vyberte zařízení, na které se nasadila vaše aplikace hlasového asistenta.

1. Otevřete kartu **řeč** .

1. Klikněte na tlačítko **změnit** další na **příkaz**.

1. Vyberte jeden z dostupných vlastních příkazů a klikněte na **Uložit** , aby se změny projevily.

## <a name="create-custom-commands"></a>Vytváření vlastních příkazů

Pomocí nástroje [Speech Studio](https://speech.microsoft.com/)můžete vytvořit vlastní příkazy pro spuštění hlasového asistenta.

Pokyny k vytváření vlastních příkazů najdete v dokumentaci ke službě [Speech Studio](../cognitive-services/speech-service/quickstart-custom-commands-application.md) . Po nakonfigurování budou vaše nové příkazy dostupné v Azure Percept studiu pro použití s vaší aplikací hlasového asistenta.

## <a name="next-steps"></a>Další kroky

Po vytvoření aplikace hlasového asistenta zkuste vyvíjet [řešení pro vize bez kódu](./tutorial-nocode-vision.md) pomocí Azure Percept DK.