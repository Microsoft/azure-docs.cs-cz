---
title: Sestavení klasifikátoru – Custom Vision Service
titlesuffix: Azure Cognitive Services
description: Informace o používání služby Custom Vision Service k vytvoření klasifikátor, který lze rozpoznat objektů v fotografie.
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: conceptual
ms.date: 05/02/2018
ms.author: anroth
ms.openlocfilehash: 998900e72511a95336e4a94289c794e2a8e59feb
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2018
ms.locfileid: "46364231"
---
# <a name="how-to-build-a-classifier-with-custom-vision"></a>Sestavení klasifikátoru s Custom Vision

Pokud chcete používat službu Custom Vision Service, musíte nejprve sestavení klasifikátoru. V tomto dokumentu se naučíte sestavit klasifikátor prostřednictvím webového prohlížeče.

## <a name="prerequisites"></a>Požadavky

K sestavení klasifikátoru, nejprve potřebujete:

- Platný [účtu Microsoft](https://account.microsoft.com/account) nebo Azure Active Directory OrgID ("pracovní nebo školní účet"), takže budou moct přihlašovat do customvision.ai a můžete začít.

    > [!IMPORTANT] 
    > Přihlašovací jméno OrgID pro uživatele Azure Active Directory (Azure AD) z [národních cloudů](https://www.microsoft.com/en-us/trustcenter/cloudservices/nationalcloud) v tuto chvíli nepodporuje.

- Řadu obrázků k trénování klasifikátoru (s minimálně 30 obrázků na značky).

- Několik obrázků otestovat klasifikátoru po školení třídění.

- Volitelné: Předplatné Azure spojené s Account Microsoft nebo OrgID. Pokud nemáte předplatné Azure, můžete vytvořit [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) předtím, než začnete.

    > [!IMPORTANT]
    > Bez předplatného Azure pouze budete moct vytvořit __omezenou zkušební verzi__ projekty. Pokud máte předplatné Azure, zobrazí se výzva k vytvoření Custom Vision Service trénování a Predikcí prostředky v [webu Azure portal](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision) během vytváření projektu.   

## <a name="create-a-new-project"></a>Vytvoření nového projektu

Chcete-li vytvořit nový projekt, použijte následující kroky:

1. Ve webovém prohlížeči přejděte [vizi vlastní webovou stránku](https://customvision.ai). Vyberte __přihlášení__ chcete začít používat službu.

    ![Obrázek přihlašovací stránky](./media/getting-started-build-a-classifier/custom-vision-web-ui.png)

    > [!NOTE]
    > Po přihlášení do služby Custom Vision Service, zobrazí se seznam projektů. Mimo dvě "omezenou zkušební verze" projektů pro testování projekty jsou přidružené k prostředku Azure. Pokud jste uživatelem Azure, zobrazí se všechny projekty, které jsou přidružené k [prostředků Azure](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#grant-access-to-resources) ke kterému máte přístup. 

2. Chcete-li vytvořit svůj první projekt, vyberte **nový projekt**. Váš první projekt zobrazí se výzva k svůj souhlas s podmínkami služby. Zaškrtněte políčko a potom vyberte **souhlasím** tlačítko. **Nový projekt** zobrazí se dialogové okno.

    ![Dialogovém okně Nový projekt obsahuje pole pro název, popis a domén.](./media/getting-started-build-a-classifier/new-project.png)

3. Zadejte název a popis pro projekt. Vyberte jednu z dostupných domén. Každá doména optimalizuje třídění pro konkrétní typy obrázků, jak je popsáno v následující tabulce:

    |Domain (Doména)|Účel|
    |---|---|
    |__Obecné__| Optimalizovaná pro širokou škálu obrázek úlohy klasifikace. Pokud žádný z jiných domén není vhodné nebo si nejste jistí, ve které doméně zvolit, vyberte doménu, Obecné. |
    |__Potravin__|Optimalizované pro fotografie si pochutnává, při které byste měli v nabídka restaurace. Pokud chcete klasifikovat fotografie jednotlivá ovoce nebo zeleniny, použijte potravin domény.|
    |__Zajímavá__|Optimalizovaná pro rozpoznatelných památek, přirozené a umělé. Tato doména funguje nejlépe, když je památek jasně viditelný v fotografie. Tato doména funguje i v případě, že památek mírně nelze blokovat uživatelé před tímto prvkem.|
    |__Maloobchodního prodeje__|Optimalizovaná pro bitové kopie, které se nacházejí v nákupní katalogu nebo nákupního webu. Pokud chcete vysokou přesnost klasifikace mezi šatů pants a košile, použijte tuto doménu.|
    |__Pro dospělé__|Optimalizované a lépe tak definovat obsah pro dospělé a obsahu jiných dospělá osoba. Například pokud chcete blokovat obrázky ve koupání barvy, tuto doménu vám umožní vytvářet vlastní třídění to udělat.|
    |__Kompaktní domén__| Optimalizovaná pro omezení v reálném čase klasifikace na mobilních zařízeních. Modely generované balíky compact domény je možné exportovat do spouštět místně.|

    Pokud chcete, můžete později změnit doménu.

4. Vyberte skupinu prostředků. Rozevírací seznam skupina prostředků se dozvíte, všechny vaše skupiny prostředků Azure, která obsahují vlastní prostředek služby pro zpracování obrazu. Můžete také vytvořit vyberte __omezenou zkušební verze__. Omezený zkušební vstup je jediný zdroj skupinu, kterou uživatel mimo Azure budete moct vybírat.

    Chcete-li vytvořit projekt, vyberte __vytvořit projekt__.

## <a name="upload-and-tag-images"></a>Nahrávání a značka Image

1. Chcete-li přidat Image do třídění, použijte __přidávat image__ tlačítko a pak vyberte __procházet místní soubory__. Vyberte __otevřít__ přesunout do označení.

    > [!TIP]
    > Po výběru Image, musíte označit je. Značka se použije ke skupině imagí, které jste vybrali k nahrání, tak může být jednodušší nahrání imagí podle značky, které chcete použít. Můžete také změnit značky pro vybrané bitové kopie po svém označení a nahrání.

    > [!TIP]
    > Nahrávání obrázků s jinou kamer, osvětlení, pozadí, typy, styly, skupiny, velikosti, atd. Použijte celou řadu typů fotografii klasifikátoru není tendenční a můžete také zobecnit.

    Služba Custom Vision Service přijímá trénovacích obrázků JPG, PNG a formátu BMP, až 6 MB za bitové kopie. (Předpověď Image může být až 4 MB za bitové kopie.) Doporučujeme, abyste se 256 pixelů na hraničních zařízeních nejkratší bitové kopie. Všechny Image, která je kratší než 256 pixelů na hraničních zařízeních nejkratší se škálovat pomocí služby Custom Vision Service.

    ![Přidat ovládací prvek Image se zobrazí v levém horním rohu a jako tlačítko dole uprostřed.](./media/getting-started-build-a-classifier/add-images01.png)

    >[!NOTE] 
    > Rozhraní REST API je možné načíst trénovacích obrázků z adresy URL.

2. Chcete-li nastavit značku, zadejte text do __značky__ pole a pak použijte __+__ tlačítko. Nahrání Image a označte je, použijte __nahrávání souborů [číslo]__ tlačítko. Můžete přidat více než jednu značku imagí. 

    > [!NOTE]
    > Doba odeslání se liší podle počtu a velikosti obrázků, které jste vybrali.

    ![Obrázek stránky značku a nahrávání](./media/getting-started-build-a-classifier/add-images03.png)

3. Vyberte __provádí__ po bitové kopie byly nahrány.

    ![Indikátor průběhu vám ukáže všechny úlohy dokončeny.](./media/getting-started-build-a-classifier/add-images04.png)

4. Nahrát další sadu imagí, vraťte se ke kroku 1. Pokud chcete rozlišit mezi psi a poníci, nahrát a značku Image poníci.

## <a name="train-and-evaluate-the-classifier"></a>Natrénování a vyhodnocení třídění

K trénování třídění, vyberte **trénování** tlačítko.

![Tlačítko trénovat na základě modelu se v pravé horní části okna prohlížeče.](./media/getting-started-build-a-classifier/train01.png)

Trvá jenom pár minut a trénování třídění. Během této doby se zobrazí informace o procesu trénování.

![Tlačítko trénovat na základě modelu se v pravé horní části okna prohlížeče.](./media/getting-started-build-a-classifier/train02.png)

Po školení, __výkonu__ se zobrazí. Indikátory přesnosti a spojené s vracením dozvíte, jak kvalitní klasifikátor je, v závislosti na automatické testování. Služba Custom Vision Service používá imagí, které jste odeslali pro školení k výpočtu tato čísla pomocí procesu [k přeložení křížového ověření](https://en.wikipedia.org/wiki/Cross-validation_(statistics)).

![Školení výsledky zobrazit celkový přesnosti a spojené s vracením a přesnost a stránkám znovu vyvolat pro jednotlivé značky v třídění.](./media/getting-started-build-a-classifier/train03.png)

> [!NOTE] 
> Pokaždé, když vyberete **trénování** tlačítko, vytvoříte novou iteraci klasifikátoru. Můžete zobrazit všechny původní iterací ve **výkonu** kartu kde můžete odstranit všechny, které může být zastaralá. Když odstraníte iterace, ale nakonec bude odstranění všech imagí, které je jedinečným způsobem k ní přidružena.

Třídění použije všechny Image k vytvoření modelu, který identifikuje jednotlivé značky. Pro otestování kvality modelu, se pokusí třídění každý obrázek v modelu, který má zobrazit, co zjistí model.

Zobrazí se kvality výsledků třídění.

|Označení|Definice|
|---|---|
|__Přesnost__|Při klasifikaci obrázku, jak pravděpodobné je klasifikátor a klasifikaci obrázku správně? Mimo všechny bitové kopie, které se používají k trénování třídění (PSI a poníci) jaké procento modelu jsou k dispozici správné? 99 správné značky mimo 100 imagí poskytuje 99 % přesností.|
|__Odvolání__|Mimo všechny bitové kopie, které by byly klasifikovány správně kolik klasifikátoru správně identifikovat? Účinnost 100 % znamená, že pokud jsou v imagích, které byly použity k trénování třídění 38 pes imagí, třídění nalézt 38 PSI.|

## <a name="next-steps"></a>Další postup

[Testování a přeučování modelu](test-your-model.md)

