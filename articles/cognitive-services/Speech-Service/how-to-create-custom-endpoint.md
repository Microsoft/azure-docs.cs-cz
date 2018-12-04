---
title: Vytvoření koncového bodu vlastní převod řeči s Speech Service v Azure | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit vlastního koncového bodu převodu řeči na text pomocí Speech Service ve službě Cognitive Services.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 03/12/2018
ms.author: panosper
ms.openlocfilehash: d06dbc399a3610982c99a548373482fc5ec21f4d
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52852879"
---
# <a name="create-a-custom-speech-to-text-endpoint"></a>Vytvoření vlastního koncového bodu pro převod řeči na text

Po vytvoření vlastních akustických modelů nebo jazykové modely, je můžete nasadit do vlastního koncového bodu převodu řeči na text. 

## <a name="create-an-endpoint"></a>Vytvoření koncového bodu
Chcete-li vytvořit nové vlastní koncový bod, vyberte **koncové body** na **Custom Speech** nabídce v horní části stránky. Tím přejdete do **koncové body** stránku, která obsahuje tabulku aktuální vlastní koncové body. Pokud jste ještě nevytvořili žádné koncové body, je tabulka prázdná. Aktuální národní prostředí se odráží v názvu tabulky. 

Chcete-li vytvořit nasazení pro jiný jazyk, vyberte **změnu národního prostředí**. Další informace o podporovaných jazycích.

Chcete-li vytvořit nový koncový bod, vyberte **vytvořit nový**. V **Create Endpoint** podokně zadejte informace **název** a **popis** pole vlastního nasazení.

V **předplatné** – pole se seznamem, vyberte předplatné, pro kterou chcete použít. Pokud se jedná S0 (placené) předplatné, můžete vybrat souběžných požadavků.

Můžete také vybrat, zda protokolování obsahu je přepnuta zapnutí nebo vypnutí. To znamená, že vybíráte, zda uložené provoz koncového bodu. Pokud není zaškrtnuto, ukládání provoz závažné, budou potlačeny. Pro veškerý obsah protokolu můžete najít odkazy ke stažení v části Endpoint -> zobrazení podrobností

V **akustický Model** vyberte akustický model, který chcete, a **jazykový Model** vyberte jazykový model, který chcete. Možnosti pro akustických a jazykových modelů vždy zahrnovat základní model Microsoft. Výběr základní model omezuje kombinace. Nelze kombinovat konverzační základní modely pomocí služby search a určovat základní modely.

> [!NOTE]
> Ujistěte se, přijměte podmínky použití a informace o cenách výběrem zaškrtávacího políčka.
>

Po výběru akustických a jazykových modelů, vyberte **vytvořit**. Tím se vrátíte na **nasazení** stránky. Tabulka teď obsahuje položku, která odpovídá nový koncový bod. Stav koncového bodu odráží její aktuální stav je při vytváření. Může trvat až 30 minut pro vytvoření instance nového koncového bodu pomocí vlastních modelů. Pokud je stav nasazení *Complete*, koncový bod je připraven k použití.

Při nasazení je připraveno, bude název koncového bodu odkaz. Výběrem odkazu se zobrazí **informace o koncovém bodu** stránku, která zobrazuje váš vlastní koncový bod adresy URL pro použití s buď požadavku HTTP nebo Microsoft Cognitive Services knihovny řečového klienta, který používá webové sokety.

## <a name="next-steps"></a>Další postup

Další kurzy najdete na stránce:
- [Získání zkušebního předplatného služby Speech](https://azure.microsoft.com/try/cognitive-services/)
- [Vytvoření vlastního akustického modelu](how-to-customize-acoustic-models.md)
- [Vytvoření vlastního jazykového modelu](how-to-customize-language-model.md)
