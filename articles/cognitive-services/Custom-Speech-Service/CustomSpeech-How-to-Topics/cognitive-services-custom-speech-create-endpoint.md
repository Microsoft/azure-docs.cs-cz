---
title: Vytvoření koncového bodu vlastní převod řeči – Custom Speech Service
titlesuffix: Azure Cognitive Services
description: Zjistěte, jak vytvořit vlastního koncového bodu převodu řeči na text pomocí Custom Speech Service.
services: cognitive-services
author: PanosPeriorellis
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: conceptual
ms.date: 07/08/2017
ms.author: panosper
ROBOTS: NOINDEX
ms.openlocfilehash: 958e97f708e7c6920bcb0d65d91656fcf00cd71a
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2018
ms.locfileid: "47219019"
---
# <a name="create-a-custom-speech-to-text-endpoint"></a>Vytvoření vlastního koncového bodu pro převod řeči na text
Po vytvoření vlastních akustických modelů nebo jazykové modely, je můžete nasadit do vlastního koncového bodu převodu řeči na text. 

## <a name="create-an-endpoint"></a>Vytvoření koncového bodu
Chcete-li vytvořit nové vlastní koncový bod, vyberte **nasazení** na **Custom Speech** nabídce v horní části stránky. Tím přejdete do **nasazení** stránku, která obsahuje tabulku aktuální vlastní koncové body. Pokud jste ještě nevytvořili žádné koncové body, je tabulka prázdná. Aktuální národní prostředí se odráží v názvu tabulky. 

Chcete-li vytvořit nasazení pro jiný jazyk, vyberte **změnu národního prostředí**. Další informace o podporovaných jazyků najdete v tématu [podporované národní prostředí ve službě Custom Speech Service](cognitive-services-custom-speech-change-locale.md).

Chcete-li vytvořit nový koncový bod, vyberte **vytvořit nový**. V **vytvořit nasazení** podokně zadejte informace **název** a **popis** pole vlastního nasazení.

V **předplatné** – pole se seznamem, vyberte předplatné, pro kterou chcete použít. Pokud je předplatné S2, můžete vybrat jednotek škálování a protokolování obsahu. Další informace o jednotek škálování a protokolování, najdete v části [měřiče služby Custom Speech Service a kvóty](../cognitive-services-custom-speech-meters.md).

Následující tabulka ukazuje, jak se jednotky škálování mapují na dostupné souběžných požadavků:

| Jednotka škálování | Počet souběžných požadavků |
| ------ | ----- |
| 0 | 1 |
| 1 | 5 |
| 2 | 10 |
| 3 | 15 |
| n | 5 * n |

Můžete také vybrat, zda protokolování obsahu je přepnuta zapnutí nebo vypnutí. To znamená, že vybíráte, jestli provoz koncového bodu je uloženo pro interní použití společnosti Microsoft. Pokud není zaškrtnuto, ukládání provoz závažné, budou potlačeny. Potlačení protokolování obsahu má za následek další poplatky. Poraďte [informace o stránce s cenami](https://azure.microsoft.com/pricing/details/cognitive-services/custom-speech-service/) podrobnosti.

> [!NOTE]
> Protokolování obsahu se nazývá "No trasování" na stránce s cenami.
>


Kromě toho společnost Microsoft poskytuje hrubý odhad nákladů tak, aby měli přehled o dopadu na náklady na jednotek škálování a protokolování obsahu. Tento odhad představuje přibližný odhad a mohou lišit od skutečné náklady.

> [!NOTE]
> Tato nastavení nejsou k dispozici s předplatnými F0 (úrovně free).
>

V **akustický Model** vyberte akustický model, který chcete, a **jazykový Model** vyberte jazykový model, který chcete. Možnosti pro akustických a jazykových modelů vždy zahrnovat základní model Microsoft. Výběr základní model omezuje kombinace. Nelze kombinovat konverzační základní modely pomocí služby search a určovat základní modely.

![Na stránce vytvořit nasazení](../../../media/cognitive-services/custom-speech-service/custom-speech-deployment-create2.png)

> [!NOTE]
> Ujistěte se, přijměte podmínky použití a informace o cenách výběrem zaškrtávacího políčka.
>

Po výběru akustických a jazykových modelů, vyberte **vytvořit**. Tím se vrátíte na **nasazení** stránky. Tabulka teď obsahuje položku, která odpovídá nový koncový bod. Stav koncového bodu odráží její aktuální stav je při vytváření. Může trvat až 30 minut pro vytvoření instance nového koncového bodu pomocí vlastních modelů. Pokud je stav nasazení *Complete*, koncový bod je připraven k použití.

![Na stránce Deployments](../../../media/cognitive-services/custom-speech-service/custom-speech-deployment-ready.png)

Při nasazení je připraveno, bude název nasazení odkaz. Výběrem odkazu se zobrazí **informace o nasazení** stránku, která zobrazuje váš vlastní koncový bod adresy URL pro použití s buď požadavku HTTP nebo Microsoft Cognitive Services knihovny řečového klienta, který používá webové sokety.

![Na stránce informace o nasazení](../../../media/cognitive-services/custom-speech-service/custom-speech-deployment-info2.png)

## <a name="next-steps"></a>Další postup

Další kurzy najdete na stránce:
* [Použití vlastního koncového bodu převodu řeči na text](cognitive-services-custom-speech-use-endpoint.md)
* [Vytvoření vlastního akustického modelu](cognitive-services-custom-speech-create-acoustic-model.md)
* [Vytvoření vlastního jazykového modelu](cognitive-services-custom-speech-create-language-model.md)
