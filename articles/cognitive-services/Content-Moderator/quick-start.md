---
title: 'Rychlý Start: Vyzkoušejte Content Moderator na webu'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu použijete nástroj online Content Moderator recenze k otestování základních funkcí Content Moderator bez nutnosti psát jakýkoli kód.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 09/29/2020
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020
keywords: moderátor obsahu, Moderování obsahu
ms.openlocfilehash: c026c42fe3c7a7f3f0d6b80e3123904077c104cf
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/09/2020
ms.locfileid: "96905209"
---
# <a name="quickstart-try-content-moderator-on-the-web"></a>Rychlý Start: Vyzkoušejte Content Moderator na webu

V tomto rychlém startu použijete nástroj online Content Moderator recenze k otestování základních funkcí Content Moderator bez nutnosti psát jakýkoli kód. Pokud chcete tuto službu integrovat do aplikace pro moderování obsahu rychleji, přečtěte si další rychlé starty v části [Další kroky](#next-steps) .

## <a name="prerequisites"></a>Předpoklady

- Webový prohlížeč

## <a name="set-up-the-review-tool"></a>Nastavení nástroje pro kontrolu
Nástroj pro kontrolu Content Moderator je webový nástroj, který umožňuje lidským kontrolorům pomoci při rozhodování. V této příručce si projdete krátký proces nastavení nástroje pro kontrolu, abyste viděli, jak služba Content Moderator funguje. Přejít na web [nástroje Content moderator recenze](https://contentmoderator.cognitive.microsoft.com/) a zaregistrovat se.

![Content Moderator domovskou stránku](images/homepage.PNG)

## <a name="create-a-review-team"></a>Vytvořit tým pro revize

Dále vytvořte tým revize. V pracovním scénáři bude tento tým skupinou uživatelů, kteří ručně kontrolují rozhodnutí o moderování služby. Chcete-li vytvořit tým, je nutné vybrat **oblast** a zadat **název týmu** a **ID týmu**. Pokud chcete kolegům pozvat do týmu, můžete to udělat tak, že zadáte e-mailovou adresu.

> [!NOTE]
> **Název týmu** je popisný název pro váš tým recenze. Toto je název zobrazený v Azure Portal. **ID týmu** je to, co se používá k identifikaci programově týmu.

> [!div class="mx-imgBorder"]
> ![Pozvat člena týmu](images/create-team.png)

Pokud se rozhodnete šifrovat data pomocí klíče spravovaného zákazníkem (CMK), budete vyzváni k zadání **ID prostředku** pro váš Content moderator prostředku v cenové úrovni E0. Prostředek, který zadáte, musí být pro tento tým jedinečný. 

> [!div class="mx-imgBorder"]
> ![Pozvání člena týmu pomocí CMK](images/create-team-cmk.png)

## <a name="upload-sample-content"></a>Nahrát ukázkový obsah

Teď jste připraveni nahrát ukázkový obsah. Vyberte možnost **vyzkoušet > obrázek**, **zkuste > Text** nebo **Vyzkoušejte > video**.

> [!div class="mx-imgBorder"]
> ![Vyzkoušení obrázku nebo moderování textu](images/tryimagesortext.png)

Odešlete obsah pro moderování. Můžete použít následující ukázkový textový obsah:

```
Is this a grabage email abcdef@abcd.com, phone: 4255550111, IP: 255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555.
Crap is the profanity here. Is this information PII? phone 4255550111
```

Interně Nástroj pro kontrolu zavolá rozhraní API pro moderování, aby kontroloval váš obsah. Až se kontrola dokončí, zobrazí se zpráva s oznámením, že výsledky čekají na vaši kontrolu.

> [!div class="mx-imgBorder"]
> ![Střední soubory](images/submitted.png)

## <a name="review-moderation-tags"></a>Kontrola značek moderování

Zkontrolujte použité značky moderování. Můžete vidět, které značky byly aplikovány na váš obsah a jaké byly skóre v každé kategorii. Další informace o tom, co ukazují různé značky obsahu, najdete v článcích o moderování [obrázků](image-moderation-api.md), [textu](text-moderation-api.md)a [videa](video-moderation-api.md) .

<!-- ![Review results](images/reviewresults_text.png) -->

V projektu můžete vy nebo váš tým revize tyto značky změnit nebo podle potřeby přidat další značky. Tyto změny odešlete tlačítkem **Další** . Protože vaše obchodní aplikace volá rozhraní API moderátorů, bude se zde zařadit do fronty, která je připravená k revizi týmy pro lidskou kontrolu. Pomocí tohoto přístupu můžete rychle zkontrolovat velké objemy obsahu.

V tuto chvíli jste použili nástroj Content Moderator recenze k zobrazení příkladů toho, co může služba Content Moderator provádět. Dále si můžete přečíst další informace o nástroji pro revize a o tom, jak ho integrovat do softwarového projektu pomocí rozhraní API pro revize, nebo můžete přejít na část [Další kroky](#next-steps) , kde se dozvíte, jak používat rozhraní API pro moderování ve vaší aplikaci.

## <a name="learn-more-about-the-review-tool"></a>Další informace o nástroji pro revize

Další informace o tom, jak používat nástroj pro kontrolu Content Moderator, najdete v příručce k [nástroji](Review-Tool-User-Guide/human-in-the-loop.md) pro kontrolu a v tématu rozhraní API pro revize se dozvíte, jak vyladit možnosti lidské recenze:
- [Rozhraní API úlohy](try-review-api-job.md) prohledává váš obsah pomocí rozhraní API pro moderování a generuje recenze v nástroji pro kontrolu. 
- [Rozhraní API recenze](try-review-api-review.md) přímo vytváří recenze obrázků, textů nebo videí pro Moderátoři pro člověka bez toho, aby nejdřív kontrolovala obsah. 
- [Rozhraní API pracovního postupu](try-review-api-workflow.md) vytvoří, aktualizuje a získá podrobnosti o vlastních pracovních postupech, které tým vytvoří.

Nebo pokračujte dalšími kroky, abyste mohli začít používat rozhraní API pro moderování ve vašem kódu.

## <a name="next-steps"></a>Další kroky

Naučte se používat rozhraní API pro moderování ve vaší aplikaci.
- Implementujte moderování imagí. Použijte [konzolu rozhraní API](try-image-api.md) nebo použijte [klientskou knihovnu nebo REST API rychlý Start](client-libraries.md) pro skenování imagí a detekci potenciálního dospělého a pikantní obsahu pomocí značek, hodnocení spolehlivosti a dalších extrahovaných informací.
- Implementujte moderování textu. Použijte [konzolu rozhraní API](try-text-api.md) nebo použijte [klientskou knihovnu nebo REST API rychlý Start](client-libraries.md) pro kontrolu textového obsahu pro potenciální vulgární výrazy, nepotřebnou klasifikaci textu s asistencí počítače (Preview) a osobní údaje.
- Implementujte moderování videa. Postupujte podle pokynů v tématu [postup pro jazyk C#](video-moderation-api.md) , který vám pomůže při skenování videí a detekci potenciálního dospělého a pikantní obsahu. 
