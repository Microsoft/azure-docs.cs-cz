---
title: Sestavení třídění službou vize vlastní - kognitivní služeb Azure | Microsoft Docs
description: Zjistěte, jak používat službu vize vlastní vytvoření klasifikátoru, který může rozpoznat objekty v fotografie.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: article
ms.date: 05/02/2018
ms.author: anroth
ms.openlocfilehash: 6dc271c13f53a445c7d1101f5264d890208bd03c
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342873"
---
# <a name="how-to-build-a-classifier-with-custom-vision"></a>Jak sestavit třídění s vlastní vize

Používat službu vlastní vize, musíte nejprve vytvořit třídění. V tomto dokumentu Naučte se vytvářet třídění prostřednictvím webového prohlížeče.

## <a name="prerequisites"></a>Požadavky

Pokud chcete vytvořit třídění, musíte nejprve mít:

- Platná [účtu Microsoft](https://account.microsoft.com/account) nebo Azure Active Directory OrgID ("pracovní nebo školní účet"), tak můžete přihlásit k customvision.ai a začít pracovat.

    > [!IMPORTANT] 
    > OrgID přihlášení pro uživatele Azure Active Directory (Azure AD) z [národních cloudů](https://www.microsoft.com/en-us/trustcenter/cloudservices/nationalcloud) není aktuálně podporováno.

- Řada bitových kopií pro učení vaší třídění (s minimálně 30 bitové kopie na značka).

- Několik Image pro testování vaší třídění po třídění je vycvičena.

- Volitelné: Předplatné Azure spojené s Account Microsoft nebo OrgID. Pokud nemáte předplatné Azure, můžete vytvořit [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před zahájením.

    > [!IMPORTANT]
    > Bez předplatného Azure, pouze bude moct vytvářet __omezené zkušební verze__ projekty. Pokud máte předplatné Azure, zobrazí se výzva k vytvoření vlastní vize služby školení a předpovědi prostředky v [portál Azure](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision) během vytváření projektu.   

## <a name="create-a-new-project"></a>Vytvoření nového projektu

Chcete-li vytvořit nový projekt, použijte následující kroky:

1. Ve webovém prohlížeči, přejděte na [vize vlastní webové stránky](https://customvision.ai). Vyberte __přihlášení__ začít používat službu.

    ![Obrázek na přihlašovací stránce](./media/getting-started-build-a-classifier/custom-vision-web-ui.png)

    > [!NOTE]
    > Po přihlášení k službě vize vlastní se zobrazí seznam projektů. Mimo projekty pro dvě "omezené zkušební verze" pro testování projekty jsou přidružena k prostředku Azure. Pokud jste Azure uživatel, zobrazí se všechny projekty, které jsou přidružené k [prostředky Azure](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#grant-access-to-resources) ke které máte přístup. 

2. Pokud chcete vytvořit svůj první projekt, vyberte **nový projekt**. Pro svůj první projekt zobrazí se výzva souhlas s podmínkami služby. Zaškrtněte políčko a potom vyberte **souhlasím** tlačítko. **Nový projekt** zobrazí se dialogové okno.

    ![Dialogového okna Nový projekt má pole pro název, popis a domén.](./media/getting-started-build-a-classifier/new-project.png)

3. Zadejte název a popis pro projekt. Vyberte jednu z dostupných domén. Každá doména, optimalizuje třídění pro konkrétní typy imagí, jak je popsáno v následující tabulce:

    |Doména|Účel|
    |---|---|
    |__Obecné__| Optimalizovaná pro širokou škálu úloh klasifikace bitové kopie. Pokud žádná z jiných domén jsou vhodné nebo si nejste jistí, ve které doméně zvolit, vyberte obecný doménu. |
    |__Jídlo__|Optimalizovaná pro fotografie misek, jako byste je v nabídce restaurace. Pokud chcete klasifikovat fotografií jednotlivé plody nebo zeleniny, použijte jídlo domény.|
    |__Zajímavá__|Optimalizovaná pro rozpoznatelném zajímavá přirozené a umělé. Tato doména funguje nejlíp, když je významné jasně viditelný v fotografie. Tato doména funguje i v případě, že významné mírně brání osoby úrovních před ním.|
    |__Prodejní__|Optimalizovaná pro bitové kopie, které se nacházejí v nákupní katalogu nebo nákupní web. Pokud chcete vysokou přesnost klasifikace mezi šatů, kalhoty a košile, použijte tuto doménu.|
    |__Pro dospělé__|Optimalizovaná tak, aby lépe definovat obsah pro dospělé a nezletilých obsahu. Například pokud chcete blokovat bitové kopie osoby koupání barev, tuto doménu můžete vytvořit vlastní třídění k tomu.|
    |__Compact domén__| Optimalizovaná pro omezení v reálném čase klasifikace na mobilních zařízeních. Modely generované compact domény je možné exportovat do spouštět místně.|

    Pokud chcete, můžete později změnit doménu.

4. Vyberte skupinu prostředků. Rozevírací seznam skupiny prostředků jsou zobrazeny všechny vaše skupiny prostředků Azure, které zahrnují vlastní prostředek služby vize. Můžete také vytvořit vyberte __omezené zkušební verze__. Omezené zkušební položka je skupina prostředků pouze uživatele mimo Azure, budou moci vybrat z.

    Chcete-li vytvořit projekt, vyberte __vytvořit projekt__.

## <a name="upload-and-tag-images"></a>Nahrávání a značky obrázků

1. K přidání bitové kopie do třídění, použijte __přidávat image__ tlačítko a potom vyberte __procházet místních souborů__. Vyberte __otevřete__ přesunout do označování.

    > [!TIP]
    > Po výběru Image, musíte označit je. Značky se použije ke skupině bitových kopií, které jste vybrali nahrát, takže možná bude snazší nahrajte Image podle značky, které budete používat. Značky pro vybrané bitové kopie můžete změnit i po jejich označí a nahrát.

    > [!TIP]
    > Nahrajte obrázky s jinou úhlů, osvětlení, pozadí, typy, styly, skupiny, velikosti, atd. Zajistěte, aby vaše třídění není tendenční a můžete generalize dobře pomocí celou řadu typů fotografii.

    Vlastní vize služba přijímá školení obrázků JPG, PNG a formátu BMP, až 6 MB za bitové kopie. (Předpovědi Image může být až 4 MB volného místa na bitovou kopii.) Doporučuje se, že bitové kopie byl 256 pixelů na nejkratší okraj. Všechny Image, která je kratší než 256 pixelů na nejkratší hraniční jsou škálovat službou vize vlastní.

    ![Ovládací prvek Image přidat se zobrazí v levém horním a jako tlačítka na dolní softwaru.](./media/getting-started-build-a-classifier/add-images01.png)

    ![Zobrazí se na tlačítko Procházet místních souborů téměř dolů na střed.](./media/getting-started-build-a-classifier/add-images02.png)

    >[!NOTE] 
    > Rozhraní REST API slouží k načtení školení bitové kopie z adresy URL.

2. Pokud chcete nastavit značky, zadejte text v __Moje značky__ pole a pak použijte __+__ tlačítko. Pokud chcete nahrát bitové kopie a označit, použijte __nahrání souborů [číslo]__ tlačítko. Můžete přidat více než jednu značku do bitových kopií. 

    > [!NOTE]
    > Čas odeslání se liší podle počtu a velikosti obrázků, které jste vybrali.

    ![Obrázek značky a odeslání stránky](./media/getting-started-build-a-classifier/add-images03.png)

3. Vyberte __provádí__ po odeslali bitové kopie.

    ![Indikátor průběhu ukazuje všechny úkoly dokončené.](./media/getting-started-build-a-classifier/add-images04.png)

4. Pokud chcete nahrát jiný soubor bitové kopie, vraťte se ke kroku 1. Například pokud chcete rozlišit mezi psi a poníci, odeslání a označit bitové kopie poníci.

## <a name="train-and-evaluate-the-classifier"></a>Natrénování a vyhodnocení třídění

Při školení třídění, vyberte **cvičení** tlačítko.

![Tlačítko train je v pravé horní části okna prohlížeče.](./media/getting-started-build-a-classifier/train01.png)

Trvá jenom pár minut ke cvičení třídění. Během této doby zobrazí se informace o procesu školení.

![Tlačítko train je v pravé horní části okna prohlížeče.](./media/getting-started-build-a-classifier/train02.png)

Po školení, __výkonu__ se zobrazí. Indikátory přesnost a odvolání zjistíte jak funkční, které vaše třídění, na základě automatického testování. Vlastní vize služba používá bitové kopie, které jste odeslali pro školení k výpočtu tato čísla pomocí procesu volat [tisíc skládání křížového ověření](https://en.wikipedia.org/wiki/Cross-validation_(statistics)).

![Výsledky školení Zobrazit celkový přesnost a odvolání a přesnost a odvolat pro jednotlivé značky v třídění.](./media/getting-started-build-a-classifier/train03.png)

> [!NOTE] 
> Pokaždé, když vyberete **Train** tlačítko vytvoříte novou iteraci vaší třídění. Můžete zobrazit všechny vaše staré iterací v **výkonu** kartě a vy můžete odstranit všechny, které mohou být zastaralé. Pokud odstraníte iterace, skončili odstraňování všechny Image, které jsou jedinečně k ní přidružena.

Třídění používá všechny bitové kopie pro vytvoření modelu, který identifikuje jednotlivé značky. Pro otestování kvality modelu, se pokusí třídění každé bitové kopie na modelu chcete zobrazit, co modelu najde.

Zobrazí se vlastností třídění výsledky.

|Označení|Definice|
|---|---|
|__Přesnost__|Při klasifikaci bitovou kopii, jak pravděpodobně je vaše třídění správně klasifikovat bitovou kopii? Mimo všechny bitové kopie, které jsou použity při cvičení třídění (PSI a poníci) jaké procento modelu získat správné? 99 správné značky mimo 100 bitových kopií poskytuje přesností 99 %.|
|__Odvolat__|Mimo všechny bitové kopie, které by byly klasifikovány správně kolik vaše třídění správně identifikovat? Odvolání 100 % znamená, že pokud jsou bitové kopie, které byly použity při cvičení třídění 38 PSA obrázky, nalezeno třídění 38 PSI.|

## <a name="next-steps"></a>Další postup

[Testování a přeučování modelu](test-your-model.md)

