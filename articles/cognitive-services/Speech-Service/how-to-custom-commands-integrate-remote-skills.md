---
title: 'Postupy: export aplikace Custom Commands jako Remote dovednost – Speech Service'
titleSuffix: Azure Cognitive Services
description: V tomto článku se naučíte, jak exportovat aplikaci Custom Commands jako dovednost.
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: sausin
ms.openlocfilehash: 8c7cb1e9f39b1de7897da29467a607953b42bb24
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2020
ms.locfileid: "94565734"
---
# <a name="export-custom-commands-application-as-a-remote-skill"></a>Exportovat aplikaci Custom Commands jako vzdálenou dovednost

V tomto článku se dozvíte, jak exportovat aplikaci Custom Commands jako vzdálenou dovednost.

## <a name="prerequisites"></a>Požadavky
> [!div class="checklist"]
> * [Seznámení s dovedností pro robot Framework](https://aka.ms/speech/cc-skill-overview)
> * [Porozumění manifestu dovedností](https://aka.ms/speech/cc-skill-manifest)
> * [Jak vyvolat dovednost z robota bot Frameworku bot](https://aka.ms/speech/cc-skill-consumer)
> * Existující aplikace vlastních příkazů. V případě, že nemáte žádnou vlastní aplikaci příkazů, vyzkoušejte s – [rychlý Start: vytvoření hlasového asistenta pomocí vlastních příkazů.](quickstart-custom-commands-application.md)

## <a name="custom-commands-as-remote-skills"></a>Vlastní příkazy jako vzdálené dovednosti
* Dovednosti v architektuře robota jsou znovu použitelné v sestavách konverzací v konverzaci – bloky, které umožňují přidat do robotu rozsáhlou funkčnost během několika minut. Další informace najdete na webu [robot Framework dovedností](https://microsoft.github.io/botframework-solutions/overview/skills/).
* Vlastní aplikace příkazů se dají exportovat jako dovednost. Tato dovednost se pak dá vyvolávat prostřednictvím protokolu vzdálených dovedností z robota bot Framework.

## <a name="configure-an-application-to-be-exposed-as-a-remote-skill"></a>Konfigurace aplikace, která se má zveřejnit jako vzdálená dovednost

### <a name="application-level-settings"></a>Nastavení na úrovni aplikace
1. Na levém panelu vyberte **Nastavení**  >  **vzdálené dovednosti**.
1. Nastavte přepínač **zapnuté vzdálené dovednosti** na zapnuto.

### <a name="authentication-to-skills"></a>Ověřování pro dovednosti
1. Pokud chcete povolit ověřování, přidejte ID aplikací Microsoftu pro bot Framework roboty, která chcete nakonfigurovat pro volání aplikace Custom Commands.
      > [!div class="mx-imgBorder"]
      > ![Přidat ID MSA do dovednosti](media/custom-commands/skill-add-msa-id.png)

1. Pokud do seznamu přidáte alespoň jednu položku, v aplikaci se povolí ověřování a aplikace bude moct volat jenom povolený roboty.
> [!TIP]
>  Chcete-li zakázat ověřování, odstraňte ze seznamu povolených všechna ID aplikací společnosti Microsoft. 

 ### <a name="enabledisable-commands-to-be-exposed-as-skills"></a>Povolit nebo zakázat příkazy, které budou zveřejněny jako dovednosti

Máte možnost zvolit si příkazy, které chcete exportovat přes vzdálené dovednosti.

1. Pokud chcete zobrazit příkaz nad dovednostmi, vyberte **Povolit nový příkaz** v části **Povolit příkazy pro dovednosti**.
1. V rozevíracím seznamu vyberte příkaz, který chcete přidat.
1. Vyberte **Uložit**.

### <a name="configure-triggering-utterances-for-commands"></a>Konfigurace aktivace projevy pro příkazy
Vlastní příkazy používají ukázkové věty, které jsou nakonfigurované pro příkazy, aby bylo možné vygenerovat dovednosti aktivované projevy. Tyto **triggery projevy** budou použity k vygenerování [**manifestu dovedností**](https://microsoft.github.io/botframework-solutions/skills/handbook/manifest/)v oddílu **dispečera** .

Jako autor můžete chtít určit, které z **ukázkových vět** se mají použít ke generování projevy pro dovednosti.
1. Ve výchozím nastavení bude soubor manifestu obsahovat všechny **Příklady triggerů** z příkazu.
1. Pokud chcete nějaký příklad výslovně eliminovat, vyberte v části příkaz **Povolit příkazy pro všechny dovednosti** možnost **Upravit** ikonu.
    > [!div class="mx-imgBorder"]
    > ![Upravit povolený příkaz pro dovednost](media/custom-commands/skill-edit-enabled-command.png)

1. Potom klikněte pravým tlačítkem na příklad vět, které chcete vynechat, a **potom klikněte na**  >  **Zakázat ukázkovou větu**.
    > [!div class="mx-imgBorder"]
    > ![Zakázat příklady](media/custom-commands/skill-disable-example-sentences.png)

1. Vyberte **Uložit**.
1. Všimněte si, že v tomto okně se nedá přidat nový příklad. Pokud je to nutné, přejděte k části nastavení a vyberte příslušný příkaz z tématu přiznávání **příkazů** . V tomto okamžiku můžete přidat novou položku v části **ukázkové věty** . Tato změna se automaticky projeví v hodnotě nastavení vzdálených dovedností pro příkaz.

> [!IMPORTANT]
> V případě, že vaše existující ukázkové věty obsahují odkazy na typ dat **katalogu > String** , budou tyto věty automaticky vyvolány ze seznamu dovedností aktivovaných projevy. 

## <a name="download-skill-manifest"></a>Stáhnout manifest dovedností
1. Po **publikování** aplikace si můžete stáhnout soubor manifestu dovedností.
1. Použijte manifest dovedností ke konfiguraci robota uživatele bot Framework pro volání na vlastní příkazy.
> [!IMPORTANT]
> Aby bylo možné stáhnout manifest dovedností, je nutné **publikovat** vlastní aplikaci příkazů. </br>
> Kromě toho, pokud jste v aplikaci provedli **nějaké změny** , je nutné aplikaci znovu publikovat, aby se projevily nejnovější změny v souboru manifestu.

> [!NOTE]
> Pokud čelíte problémům s publikováním aplikace a tato chyba se směruje na dovednosti aktivované projevy, přečtěte si prosím znovu konfiguraci pro **povolené příkazy pro dovednosti**. Každý z vystavených příkazů musí mít alespoň jednu platnou triggerovou utterance.


## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Aktualizace příkazu z klienta](./how-to-custom-commands-update-command-from-client.md)
