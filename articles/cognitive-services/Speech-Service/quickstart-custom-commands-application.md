---
title: 'Rychlý Start: vytvoření hlasového asistenta pomocí vlastních příkazů'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu vytvoříte a otestujete základní aplikaci Custom Commands pomocí nástroje Speech Studio.
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: sausin
ms.openlocfilehash: 05b47a786fe845460177b66b5bd54cdb140c246e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87289428"
---
# <a name="create-a-voice-assistant-using-custom-commands"></a>Vytvoření hlasového asistenta pomocí vlastních příkazů

V tomto rychlém startu vytvoříte a otestujete základní aplikaci Custom Commands pomocí nástroje Speech Studio. K této aplikaci budete mít přístup taky z klientské aplikace pro Windows. **Vlastní příkazy** usnadňují vytváření bohatě bohatých aplikací pro hlasové příkazy optimalizovaných pro prostředí s prvními interakcemi hlasu. Poskytuje jednotné prostředí pro vytváření, automatický hostující model a poměrně nižší složitost, což vám pomůže soustředit se na vytváření nejlepšího řešení pro scénáře hlasových příkazů.

## <a name="region-availability"></a>Dostupnost oblasti
V současné době vlastní příkazy podporují odběry řeči vytvořené v těchto oblastech:
* USA – západ
* USA – západ 2
* East US
* USA – východ 2
* Severní Evropa
* West Europe
* USA – středozápad
* Indie – střed
* Východní Asie
* Southeast Asia

## <a name="prerequisites"></a>Požadavky

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Vytvořte prostředek řeči Azure v oblasti, která podporuje vlastní příkazy.<span class="docon docon-navigate-external x-hidden-focus"></span></a> Seznam podporovaných oblastí najdete výše v části **dostupnost oblasti** .
> * Stáhněte si ukázkový soubor JSON pro [inteligentní místnost Lite](https://aka.ms/speech/cc-quickstart) .
> * Stáhněte si nejnovější verzi [klienta Windows Voice Assistant](https://aka.ms/speech/va-samples-wvac).

## <a name="go-to-the-speech-studio-for-custom-commands"></a>Přejít do sady Speech Studio pro vlastní příkazy

1. Ve webovém prohlížeči přejdete na [Speech Studio](https://speech.microsoft.com/).
1. Zadejte přihlašovací údaje pro přihlášení k portálu.

   Výchozím zobrazením je seznam předplatných pro rozpoznávání řeči.
   > [!NOTE]
   > Pokud nevidíte stránku vybrat odběr, můžete na ni přejít výběrem možnosti "prostředky řeči" v nabídce nastavení na horním panelu.

1. Vyberte své předplatné pro rozpoznávání řeči a pak vyberte **Přejít k studiu**.
1. Vyberte **vlastní příkazy**.

   Výchozím zobrazením je seznam aplikací pro vlastní příkazy, které máte v rámci vybraného předplatného.

## <a name="import-an-existing-application-as-a-new-custom-commands-project"></a>Import existující aplikace jako nového projektu vlastních příkazů

1. Vyberte **Nový projekt** a vytvořte projekt.

1. Do pole **název** zadejte název projektu jako `Smart-Room-Lite` (nebo něco jiného podle vašeho výběru).
1. V seznamu **jazyk** vyberte možnost **Angličtina (USA)**.
1. Vyberte **Procházet soubory** a v okně procházet vyberte **SmartRoomLite.jsv** souboru.

    > [!div class="mx-imgBorder"]
    > ![Vytvoření projektu](media/custom-commands/import-project.png)

1.  V seznamu **prostředek pro vytváření Luis** vyberte prostředek pro vytváření obsahu. Pokud neexistují žádné platné prostředky pro vytváření obsahu, vytvořte si ho tak, že vyberete  **vytvořit nový prostředek pro vytváření obsahu Luis**.

    > [!div class="mx-imgBorder"]
    > ![Vytvoření prostředku](media/custom-commands/create-new-luis-resource.png)
    
    
    1. Do pole **název prostředku** zadejte název prostředku.
    1. V seznamu **Skupina prostředků** vyberte skupinu prostředků.
    1. V seznamu **umístění** vyberte umístění.
    1. V seznamu **cenová úroveň** vyberte úroveň.
    
    
    > [!NOTE]
    > Skupiny prostředků můžete vytvořit tak, že do pole Skupina prostředků zadáte požadovaný název skupiny prostředků. Skupina prostředků se vytvoří, když se vybere možnost **vytvořit** .


1. Potom vyberte **vytvořit** a vytvořte tak projekt.
1. Po vytvoření projektu vyberte svůj projekt.
Nyní byste měli vidět přehled vaší nové aplikace Custom Commands.

## <a name="try-out-some-voice-commands"></a>Vyzkoušejte některé hlasové příkazy
1. V horní části pravého podokna vyberte **vlak** .
1. Po dokončení školení vyberte **test** a vyzkoušejte následující projevy:
    - Zapnutí televizního vysílání
    - Nastavit teplotu na 80 stupňů
    - Vypnout
    - TV
    - Nastavit alarm na 5 odp.

## <a name="integrate-custom-commands-application-in-an-assistant"></a>Integrace aplikace Custom Commands v Pomocníkovi
Než budete moct k této aplikaci přistupovat z vnějšího nástroje Speech Studio, musíte aplikaci publikovat. Pro publikování aplikace budete muset nakonfigurovat předpověď LUIS prostředků.  

### <a name="update-prediction-luis-resource"></a>Aktualizovat LUIS prostředek předpovědi


1. V levém podokně vyberte **Nastavení** a v prostředním podokně vyberte  **Luis prostředky** .
1. Vyberte prostředek předpovědi, nebo ho vytvořte výběrem možnosti **vytvořit nový prostředek**.
1. Vyberte **Uložit**.
    
    > [!div class="mx-imgBorder"]
    > ![Nastavení prostředků LUIS](media/custom-commands/set-luis-resources.png)

> [!NOTE]
> Vzhledem k tomu, že prostředek pro vytváření podporuje jenom 1 000 požadavků koncového bodu předpovědi za měsíc, budete muset před publikováním aplikace Custom Commands mandatorily nastavit prostředek předpovědi LUIS.

### <a name="publish-the-application"></a>Publikování aplikace

V pravém horním podokně vyberte  **publikovat** . Po dokončení publikování se zobrazí nové okno. Poznamenejte si z něj hodnotu **ID aplikace** a **klíč prostředku pro rozpoznávání řeči** . Tyto dvě hodnoty budete potřebovat, abyste měli přístup k aplikaci z vnější řeči Studio.

Případně můžete tyto hodnoty získat také tak, že vyberete **Nastavení**  >  **Obecné** oddíl.

### <a name="access-application-from-client"></a>Přístup k aplikaci z klienta

V rozsahu tohoto článku budeme používat klienta Windows Voice Assistant, který jste stáhli jako součást požadavků. Rozbalte složku.
1. Spusťte **VoiceAssistantClient.exe**.
1. Vytvořte nový profil publikování a zadejte hodnotu pro **profil připojení**. V části **Obecné nastavení** zadejte hodnotu **klíč předplatného** (to je stejná jako hodnota **klíče prostředku řeči** , kterou jste uložili při publikování aplikace), **oblast klíče předplatného** a **vlastní příkazy ID aplikace**.
    > [!div class="mx-imgBorder"]
    > ![WVAC vytvořit profil](media/custom-commands/create-profile.png)
1. Vyberte **Uložit a použít profil**.
1. Teď vyzkoušejte následující vstupy přes řeč/text
    > [!div class="mx-imgBorder"]
    > ![WVAC vytvořit profil](media/custom-commands/conversation.png)


> [!TIP]
> Kliknutím na položky v **protokolu aktivit** můžete zkontrolovat nezpracované odpovědi odesílané ze služby Custom Commands.

## <a name="next-steps"></a>Další kroky

V tomto článku jste použili existující aplikaci. Dále se v [oddílech s postupy](how-to-custom-commands-create-application-with-simple-commands.md)naučíte navrhovat, vyvíjet, ladit, testovat a integrovat vlastní aplikace s příkazy od začátku.
