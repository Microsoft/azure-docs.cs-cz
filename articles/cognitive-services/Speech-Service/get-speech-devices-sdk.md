---
title: Získání sady Speech Devices SDK
description: Zjistěte, jak získat přístup k sadou SDK pro řeč zařízení.
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.technology: speech
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: d6328a9663aa1a7936f40606b6252fa119d31d0c
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/06/2018
ms.locfileid: "44025372"
---
# <a name="get-the-cognitive-services-speech-devices-sdk"></a>Zařízení řeči služeb Cognitive Services SDK

Sadou SDK pro řeč zařízení je v omezené verzi preview a je nutné se zaregistrovat do programu. V současné době Microsoft upřednostňuje velké firmy jako kandidáty pro přístup k tomuto produktu.

## <a name="request-access"></a>Vyžádat si přístup

Pokud chcete získat přístup k sadou SDK pro řeč zařízení:

1. Přejděte na Microsoft řeči Devices SDK [registrační formulář](https://aka.ms/sdsdk-signup).
1. Přečtěte si [licenční smlouvy](speech-devices-sdk-license.md).
1. Pokud souhlasíte s podmínkami licenční smlouvy, **souhlasím**.
1. Odpovězte na otázky ve formuláři.
1. Odeslání formuláře. 
1. Pokud e-mailová adresa již není součástí Azure Active Directory (Azure AD), zobrazí se při schválí pro přístup k zvací e-mail jako v následujícím příkladu. Pokud vaše e-mailová adresa je již ve službě Azure AD, obdržíte e-mailovou zprávu od týmu služeb Microsoft řeči při schválí pro přístup, a můžete přeskočit přímo k [stažení sady SDK zařízení řeči](#download-the-speech-devices-sdk).

## <a name="approval-e-mail"></a>Schvalovací e-mail

```
From: Microsoft Speech Team from Microsoft (via Microsoft) <invites@microsoft.com> 
Subject: You're invited to the Microsoft organization 
```

![e-mailové zprávy](media/speech-devices-sdk/get-sdk-1.png)

## <a name="accept-access"></a>Přijmout přístup

Proveďte následující kroky pro připojení k Azure AD s e-mailovou adresu, kterou jste zadali během registrace. Tento proces uděluje přístup k zařízení sadou SDK pro řeč [web pro stažení](https://shares.datatransfer.microsoft.com/).

1. V e-mailovou zprávu jste obdrželi, vyberte **Začínáme**. Pokud je vaše organizace již zákazníky s Office 365, zobrazí se výzva k přihlášení a můžete přeskočit ke kroku 8.

2. V okně prohlížeče, které se otevře, vyberte **Další**.

    ![okno ověřování](media/speech-devices-sdk/get-sdk-2.png)

3. Pokud ho ještě nemáte, vytvořte účet Microsoft. Zadejte stejnou e-mailovou adresu, na který jste dostali e-mailová pozvánka.

    ![Vytvoření účtu Microsoft](media/speech-devices-sdk/get-sdk-3.png)

4. Vyberte **Další** vytvoření hesla.

5. Po zobrazení výzvy k ověření e-mailu, získáte ověřovací kód z e-mailová pozvánka, který jste obdrželi.
 
7. Vložte nebo zadejte bezpečnostní kód z e-mailovou zprávu v dialogovém okně. V tomto příkladu je bezpečnostní kód **8406**. Vyberte **Další**.

    ![ověřit e-mailu](media/speech-devices-sdk/get-sdk-6.png)
 
8. Když se zobrazí Panel aplikace Access v prohlížeči, se ujistíte, že vaši e-mailová adresa je součástí služby Azure AD. Teď máte přístup k serveru pro stahování sadou SDK pro řeč zařízení.

## <a name="download-the-speech-devices-sdk"></a>Stáhněte si zařízení řeči SDK

Přejděte [serveru pro stahování sadou SDK pro řeč zařízení](https://shares.datatransfer.microsoft.com/). Přihlaste se pomocí účtu Microsoft, který jste vytvořili dříve. 

![Web SDK ke stažení](media/speech-devices-sdk/get-sdk-7.png)

Stažení řeč zařízení SDK, přidružené ukázkový kód a referenční materiály:

1. Stáhněte a nainstalujte nástroje Aspera Connect po zobrazení výzvy v prohlížeči.

    ![Stáhněte si Aspera připojení](media/speech-devices-sdk/get-sdk-8.png)
 
1. Vyberte **Ano** přepnout aplikací se Aspera připojit.

    ![Přepnout na Aspera připojení](media/speech-devices-sdk/get-sdk-9.png)
 
1. Vyberte **povolit** potvrďte stažení souborů pomocí Aspery připojení.

    ![Stáhněte si pomocí Aspery připojení](media/speech-devices-sdk/get-sdk-10.png)
 
1. Zavřete okno Aspera připojení přenosy po stažení souborů.

    ![Okno připojení přenosy Aspera](media/speech-devices-sdk/get-sdk-11.png)
 
Ve výchozím nastavení se soubory stáhnou do vaší **stáhne** složky. Se můžete přihlásit z tohoto webu teď. 

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Začínáme se sadou SDK pro řeč zařízení](speech-devices-sdk-qsg.md)
