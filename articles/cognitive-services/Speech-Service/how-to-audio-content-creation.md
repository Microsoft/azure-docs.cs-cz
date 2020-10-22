---
title: Vytvoření zvukového obsahu – služba pro rozpoznávání řeči
titleSuffix: Azure Cognitive Services
description: Vytváření zvukového obsahu je online nástroj, který umožňuje přizpůsobit a vyladit výstup převodu textu na řeč od Microsoftu pro vaše aplikace a produkty.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: trbye
ms.openlocfilehash: a04088fc1074949a1228794c22c1fa65a0b736a7
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2020
ms.locfileid: "92370025"
---
# <a name="improve-synthesis-with-the-audio-content-creation-tool"></a>Vylepšení syntézy pomocí nástroje pro tvorbu zvukového obsahu

[Vytvoření zvukového obsahu](https://aka.ms/audiocontentcreation) je snadno použitelný a výkonný nástroj, který umožňuje vytvářet vysoce přirozený zvukový obsah pro celou řadu scénářů, jako je audiobooks, všesměrové vysílání, mluvený komentář a chat roboty. Díky vytváření zvukového obsahu můžete doladit hlasy textu na řeč a efektivně a s nízkými náklady navrhovat přizpůsobená zvuková prostředí.

Tento nástroj je založený na [jazyce SSML (Speech syntézy)](speech-synthesis-markup.md). Umožňuje upravit výstupní atributy převodu textu na řeč v reálném čase nebo dávkově syntézách, jako jsou hlasové znaky, hlasové styly, rychlost řeči, výslovnost a Prosody.

Můžete mít snadný přístup k více než 150 předem připraveným hlasům v rámci blízko až 50 různých jazyků, včetně špičkového hlasu neuronové TTS a vlastního hlasu, pokud jste ho vytvořili sami. 

Podívejte se na [video s kurzem](https://www.youtube.com/watch?v=O1wIJ7mts_w) vytvoření zvukového obsahu.

## <a name="how-to-get-started"></a>Jak začít?

Vytvoření zvukového obsahu je bezplatný nástroj, ale budete platit za službu Azure Speech Service, kterou využíváte. Pokud chcete s nástrojem pracovat, musíte se přihlásit pomocí účtu Azure a vytvořit prostředek pro rozpoznávání řeči. Pro každý účet Azure máte k dispozici měsíční kvóty pro rozpoznávání řeči, které zahrnují 500 000 znaků pro hlasy neuronové TTS (za měsíc), 5 000 000 znaků pro standardní a vlastní hlasy (za měsíc) a 1 vlastní hostitelskou službu hlasového koncového bodu (za měsíc). Přidělená měsíční hodnota je obvykle dostatečná pro malý tým obsahu okolo 3-5 lidí. Tady je postup, jak vytvořit účet Azure a získat prostředek pro rozpoznávání řeči. 

### <a name="step-1---create-an-azure-account"></a>Krok 1 – Vytvoření účtu Azure

Pokud chcete pracovat se vytvářením zvukového obsahu, musíte mít [účet Microsoft](https://account.microsoft.com/account) a [účet Azure](https://azure.microsoft.com/free/ai/). Při [nastavování účtu](get-started.md#new-resource)postupujte podle těchto pokynů. 

[Azure Portal](https://portal.azure.com/) je centralizované místo pro správu účtu Azure. Můžete vytvořit prostředek pro rozpoznávání řeči, spravovat přístup k produktu a monitorovat všechno od jednoduchých webových aplikací až po složitá cloudová nasazení. 

### <a name="step-2---create-a-speech-resource"></a>Krok 2 – Vytvoření prostředku pro rozpoznávání řeči

Po registraci k účtu Azure budete muset vytvořit prostředek pro rozpoznávání řeči pod účtem Azure pro přístup ke službám Speech. Podívejte se na pokyny, [jak vytvořit prostředek řeči](https://docs.microsoft.com/azure/cognitive-services/speech-service/overview#create-the-azure-resource). 

Nasazení nového prostředku řeči chvíli trvá. Po dokončení nasazení můžete zahájit cestu k vytváření zvukového obsahu. 

 >[!NOTE]
   > Pokud máte v úmyslu používat hlasy neuronové, ujistěte se, že jste vytvořili prostředek v [oblasti, která podporuje hlasy neuronové](regions.md#standard-and-neural-voices).
 
### <a name="step-3---log-into-the-audio-content-creation-with-your-azure-account-and-speech-resource"></a>Krok 3 – přihlášení k vytváření zvukového obsahu pomocí účtu Azure a prostředku řeči

1. Po získání účtu Azure a prostředku řeči se můžete přihlásit k [vytváření zvukového obsahu](https://aka.ms/audiocontentcreation) kliknutím na **Začínáme**.
2. Zobrazí se stránka **prostředek pro rozpoznávání řeči** . Vyberte prostředek pro rozpoznávání řeči, na kterém chcete pracovat. Pokud chcete začít vytvářet zvuk, klikněte na **Přejít do studia** . Nový prostředek pro rozpoznávání řeči můžete také vytvořit kliknutím na **vytvořit nový**. Při příštím přihlášení k nástroji pro vytváření zvukového obsahu vás pošleme přímo na zvukové pracovní soubory v rámci aktuálního prostředku řeči. 
3. Prostředek řeči můžete kdykoli upravit pomocí možnosti **Nastavení** , která se nachází v horním navigačním panelu.

## <a name="how-to-use-the-tool"></a>Jak používat tento nástroj?

Tento diagram znázorňuje kroky potřebné k vyladění výstupů převodu textu na řeč. Pomocí odkazů níže můžete získat další informace o jednotlivých krocích.

:::image source="media/audio-content-creation/audio-content-creation-diagram.jpg" alt-text="Diagram kroků, které jsou potřebné k doladění výstupů převodu textu na řeč":::


1. Vyberte prostředek řeči, na kterém chcete pracovat.
2. Pomocí prostého textu nebo skriptů SSML [vytvořte soubor pro optimalizaci zvuku](#create-an-audio-tuning-file) . Zadejte nebo nahrajte svůj obsah do vytváření zvukového obsahu.
3. Vyberte hlas a jazyk pro obsah vašeho skriptu. Vytváření zvukového obsahu zahrnuje všechny [hlasy od společnosti Microsoft pro převod textu na řeč](language-support.md#text-to-speech). Můžete použít standardní, neuronové nebo vlastní hlas.
   >[!NOTE]
   > Gated Access je k dispozici pro vlastní hlasy neuronové, díky kterým můžete vytvářet hlasy s vysokou definicí podobnou přirozenému zvuku řeči. Další podrobnosti najdete v tématu [proces uzavírání](https://aka.ms/ignite2019/speech/ethics).

4. Kliknutím na ikonu **Přehrát** (trojúhelník) zobrazíte náhled výchozího výstupu Shrnutí. Pak Vylepšete výstup úpravou výslovnosti, přerušení, sklonu, poměru, nevýbušnosti, stylu hlasu a dalších možností. Úplný seznam možností najdete v tématu [jazyk značek pro rozpoznávání řeči](speech-synthesis-markup.md). Tady je [video](https://www.youtube.com/watch?v=O1wIJ7mts_w) , které ukazuje, jak vyladit výstup řeči pomocí vytváření zvukového obsahu. 
5. Uložte a [exportujte vyladěné zvuky](#export-tuned-audio). Když v systému uložíte stopu ladění, můžete pokračovat v práci a iterovat na výstupu. Až budete s výstupem spokojeni, můžete vytvořit úlohu vytvoření zvuku pomocí funkce exportu. Můžete sledovat stav úlohy exportu a stáhnout výstup pro použití s aplikacemi a produkty.

## <a name="create-an-audio-tuning-file"></a>Vytvořit soubor pro optimalizaci zvukového souboru

Existují dva způsoby, jak získat obsah do nástroje pro tvorbu zvukového obsahu.

**Možnost 1:**

1. Kliknutím na ikonu **nový soubor** v pravém horním rohu vytvořte nový soubor pro optimalizaci zvuku.
2. Zadejte nebo vložte obsah do okna pro úpravy. Znaky pro každý soubor jsou až 20 000. Pokud je váš skript delší než 20 000 znaků, můžete použít možnost 2 pro automatické rozdělení obsahu do více souborů. 
3. Nezapomeňte uložit.

**Možnost 2:**

1. Kliknutím na **Odeslat** naimportujete jeden nebo více textových souborů. Podporuje se prostý text i SSML. Pokud je soubor skriptu delší než 20 000 znaků, rozdělte ho prosím podle odstavců, podle znaku nebo podle regulárních výrazů. 
3. Když nahráváte textové soubory, ujistěte se, že tento soubor splňuje tyto požadavky.

   | Vlastnost | Hodnota/poznámky |
   |----------|---------------|
   | Formát souboru | Prostý text (. txt)<br/> Text SSML (. txt)<br/> Soubory zip nejsou podporovány |
   | Formát kódování | UTF-8 |
   | Název souboru | Každý soubor musí mít jedinečný název. Duplicity nejsou podporovány. |
   | Délka textu | Omezení počtu znaků textového souboru je 20 000. Pokud vaše soubory překračují omezení, rozdělte soubory podle pokynů v nástroji. |
   | Omezení SSML | Každý soubor SSML může obsahovat pouze jednu část SSML. |

**Příklad prostého textu**

```txt
Welcome to use Audio Content Creation to customize audio output for your products.
```
**Příklad textu SSML**

```xml
<speak xmlns="http://www.w3.org/2001/10/synthesis" xmlns:mstts="http://www.w3.org/2001/mstts" version="1.0" xml:lang="en-US">
    <voice name="Microsoft Server Speech Text to Speech Voice (en-US, AriaNeural)">
    Welcome to use Audio Content Creation <break time="10ms" />to customize audio output for your products.
    </voice>
</speak>
```

## <a name="export-tuned-audio"></a>Exportovat laděný zvuk

Po kontrole zvukového výstupu a splnění jeho optimalizace a úprav můžete zvuk exportovat.

1. Kliknutím na **exportovat** vytvořte úlohu vytváření zvuku. **Export do knihovny zvuku** se doporučuje, protože podporuje dlouhý zvukový výstup a plné prostředí pro výstup zvuku. Zvuk můžete také stáhnout přímo na místní disk, ale k dispozici je pouze prvních 10 minut.
2. Vyberte formát výstupu pro vyladěný zvuk. Seznam podporovaných formátů a vzorkovacích sazeb je k dispozici níže.
3. Stav úlohy můžete zobrazit na kartě **úloha exportu** . Pokud se úloha nezdařila, přejděte na stránku podrobné informace pro celou sestavu.
4. Po dokončení úlohy je váš zvuk k dispozici ke stažení na kartě **Knihovna zvukového** prostředí.
5. Klikněte na tlačítko **Stáhnout**. Teď jste připraveni používat vlastní laděný zvuk ve vašich aplikacích nebo produktech.

**Podporované formáty zvuku**

| Formát | vzorkovací frekvence 16 kHz | frekvence vzorků 24 kHz |
|--------|--------------------|--------------------|
| buď | RIFF-16khz-16bitový-mono-PCM | RIFF-24khz-16bitový-mono-PCM |
| MP3 | Audio-16khz-128kbitrate-mono-MP3 | Audio-24khz-160kbitrate-mono-MP3 |

## <a name="how-to-addremove-audio-content-creation-users"></a>Jak přidat nebo odebrat uživatele vytváření obsahu zvukového obsahu?

Pokud chce vytvoření zvukového obsahu použít více než jeden uživatel, můžete buď sdílet svůj účet Azure a heslo s uživatelem, nebo udělit uživateli přístup k předplatnému Azure a prostředku řeči. Pokud přidáte uživatele do předplatného Azure, uživatel bude mít přístup ke všem prostředkům v rámci předplatného Azure. Pokud však do prostředku řeči přidáte pouze uživatele, bude mít uživatel přístup pouze k prostředku rozpoznávání řeči a nebude mít přístup k dalším prostředkům v rámci tohoto předplatného Azure. Uživatel s přístupem k prostředku řeči může použít vytvoření zvukového obsahu.

### <a name="add-users-to-a-speech-resource"></a>Přidání uživatelů do prostředku řeči

Pomocí těchto kroků přidáte uživatele do prostředku rozpoznávání řeči, aby mohli používat vytvoření zvukového obsahu.

1. V Azure Portal vyhledejte vyhledané **služby** , vyberte prostředek pro rozpoznávání řeči, do kterého chcete přidat uživatele.
2. Klikněte na **Řízení přístupu (IAM)** . Kliknutím na kartu **Přiřazení rolí** zobrazte všechna přiřazení rolí pro toto předplatné.
    :::image source="media/audio-content-creation/access-control-roles.png" alt-text="Diagram kroků, které jsou potřebné k doladění výstupů převodu textu na řeč":::
1. Kliknutím na **Přidat** > **Přidat přiřazení role** otevřete podokno Přidat přiřazení role. V rozevíracím seznamu role vyberte roli **uživatele služba rozpoznávání** . Pokud chcete uživateli poskytnout vlastnictví tohoto prostředku řeči, můžete vybrat roli **vlastníka** .
1. V seznamu vyberte uživatele. Pokud uživatele v seznamu nevidíte, můžete zadat do pole vybrat a vyhledat v adresáři zobrazovaná jména a e-mailové adresy. Pokud uživatel není v tomto adresáři, můžete zadat [účet Microsoft](https://account.microsoft.com/account) uživatele (což je důvěryhodný pro Azure Active Directory).
1. Kliknutím na **Uložit** roli přiřaďte. Po chvíli se uživateli přiřadí role uživatele služba pro rozpoznávání řeči v oboru prostředků řeči.

    :::image source="media/audio-content-creation/add-role-first.png" alt-text="Diagram kroků, které jsou potřebné k doladění výstupů převodu textu na řeč":::

1. Uživatelé, které přidáte, budou dostávat e-mail s pozvánkou. Po kliknutí na **přijmout pozvánku**  >  **přijmout pro připojení k Azure**můžou použít [Vytvoření zvukového obsahu](https://aka.ms/audiocontentcreation).

Uživatelé, kteří se nacházejí ve stejném prostředku řeči, uvidí práci v nástroji pro vytváření zvukového obsahu v studiu. Pokud chcete, aby měl každý jednotlivý uživatel jedinečné a soukromé pracoviště při vytváření zvukového obsahu, vytvořte si pro každého uživatele [nový prostředek řeči](#step-2---create-a-speech-resource) a každému uživateli udělte jedinečný přístup k prostředku řeči. 

### <a name="remove-users-from-a-speech-resource"></a>Odebrání uživatelů z prostředku řeči
1. V Azure Portal vyhledejte vyhledané **služby** , vyberte prostředek pro rozpoznávání řeči, ze kterého chcete odebrat uživatele.
2. Klikněte na **Řízení přístupu (IAM)** . Kliknutím na kartu **přiřazení rolí** zobrazíte všechna přiřazení rolí tohoto prostředku řeči.
3. Vyberte uživatele, které chcete odebrat, a klikněte na tlačítko **Odebrat**  >  **OK**.
    :::image source="media/audio-content-creation/remove-user.png" alt-text="Diagram kroků, které jsou potřebné k doladění výstupů převodu textu na řeč":::

### <a name="enable-users-to-grant-access"></a>Povolit uživatelům udělení přístupu
Pokud chcete, aby jeden z uživatelů měl přístup k ostatním uživatelům, musíte dát uživateli roli vlastníka pro prostředek pro rozpoznávání řeči a nastavit uživatele jako čtečku adresáře Azure. 
1. Přidejte uživatele jako vlastníka prostředku řeči. Přečtěte si téma [jak přidat uživatele do prostředku řeči](#add-users-to-a-speech-resource).
    :::image source="media/audio-content-creation/add-role.png" alt-text="Diagram kroků, které jsou potřebné k doladění výstupů převodu textu na řeč":::
1. Vyberte sbalenou nabídku v levém horním rohu. Klikněte na **Azure Active Directory**a potom klikněte na **Uživatelé**.
1. Vyhledejte účet Microsoft uživatele a přejít na stránku s podrobnostmi uživatele. Klikněte na tlačítko **přiřazené role**.
1. Klikněte na **Přidat úlohy**  ->  **čtenáři adresáře**.

## <a name="see-also"></a>Viz také

* [Rozhraní API pro dlouhé zvukové zařízení](https://aka.ms/long-audio-api)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Speech Studio](https://speech.microsoft.com)
