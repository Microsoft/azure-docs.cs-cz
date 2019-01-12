---
title: Sestavení klasifikátoru – Custom Vision Service
titlesuffix: Azure Cognitive Services
description: Zjistěte, jak vytvořit model klasifikace obrázků pomocí webových stránek pro zpracování obrazu vlastní.
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: anroth
ms.openlocfilehash: f6ab2d8bcf1ae02df95b0cf36eacffa90964d43e
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/12/2019
ms.locfileid: "54243249"
---
# <a name="how-to-build-a-classifier-with-custom-vision"></a>Sestavení klasifikátoru s Custom Vision

Pro účely služby Custom Vision Service klasifikace obrázků, musíte nejprve sestavení modelu třídění. V této příručce se dozvíte, jak sestavení klasifikátoru prostřednictvím webu Custom Vision.

## <a name="prerequisites"></a>Požadavky

- Platný [účtu Microsoft](https://account.microsoft.com/account) nebo účet Azure Active Directory (AAD) ("pracovní nebo školní účet").

    > [!IMPORTANT] 
    > Přihlášení pro uživatele AAD z [národních Cloudech Microsoftu](https://www.microsoft.com/en-us/trustcenter/cloudservices/nationalcloud) v tuto chvíli nepodporuje.
- Sadu imagí, které má klasifikátor trénovat. Níže najdete tipy k výběru Image.
- Volitelně můžete: Předplatné Azure spojené s vaším účtem Microsoft nebo účtu AAD. Pokud nemáte předplatné Azure, můžete vytvořit [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) předtím, než začnete. Bez předplatného Azure, bude pouze budete moci vytvořit dva __omezenou zkušební verzi__ projekty.

## <a name="create-a-new-project"></a>Vytvoření nového projektu

Ve webovém prohlížeči přejděte [Custom Vision webové stránky](https://customvision.ai) a vyberte __přihlášení__.

![Obrázek přihlašovací stránky](./media/browser-home.png)

Pokud máte účet Azure, zobrazí se výzva k vytvoření Custom Vision Service trénování a Predikcí prostředky v [webu Azure portal](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision) během vytváření projektu.

1. Chcete-li vytvořit svůj první projekt, vyberte **nový projekt**. **Vytvořit nový projekt** se zobrazí dialogové okno.

    ![Dialogovém okně Nový projekt obsahuje pole pro název, popis a domén.](./media/getting-started-build-a-classifier/new-project.png)

1. Zadejte název a popis pro projekt. Vyberte skupinu prostředků. Pokud je váš účet přihlášeného přidružené k účtu Azure, se zobrazí rozevírací seznam skupina prostředků, všechny vaše skupiny prostředků Azure, která obsahují vlastní prostředek služby pro zpracování obrazu. V obou případech můžete také vybrat __omezenou zkušební verzi__ z tohoto rozevíracího seznamu.

1. Vyberte __klasifikace__ pod __typy projektů__. Potom v části __typy klasifikace__, zvolte buď **Multilabel** nebo **Multiclass**, v závislosti na vašemu případu použití. Multilabel klasifikace se použije libovolný počet značek pro bitovou kopii (nula nebo více), zatímco klasifikace víc tříd seřadí bitové kopie do jedné kategorie (každý obrázek, které uvedete budou seřazeny do nejpravděpodobnější značky). Bude možné později změnit typ klasifikace, pokud chcete.

1. Potom vyberte jednu z dostupných domén. Každá doména optimalizuje třídění pro konkrétní typy obrázků, jak je popsáno v následující tabulce. Bude možné později změnit doménu, pokud chcete.

    |Domain (Doména)|Účel|
    |---|---|
    |__Obecné__| Optimalizovaná pro širokou škálu obrázek úlohy klasifikace. Pokud žádný z jiných domén není vhodné nebo si nejste jistí, ve které doméně zvolit, vyberte doménu, Obecné. |
    |__Potravin__|Optimalizované pro fotografie si pochutnává, při které byste měli v nabídka restaurace. Pokud chcete klasifikovat fotografie jednotlivá ovoce nebo zeleniny, použijte potravin domény.|
    |__Zajímavá__|Optimalizovaná pro rozpoznatelných památek, přirozené a umělé. Tato doména funguje nejlépe, když je památek jasně viditelný v fotografie. Tato doména funguje i v případě, že památek mírně nelze blokovat uživatelé před tímto prvkem.|
    |__Maloobchodního prodeje__|Optimalizovaná pro bitové kopie, které se nacházejí v nákupní katalogu nebo nákupního webu. Pokud chcete vysokou přesnost klasifikace mezi šatů pants a košile, použijte tuto doménu.|
    |__Pro dospělé__|Optimalizované a lépe tak definovat obsah pro dospělé a obsahu jiných dospělá osoba. Například pokud chcete blokovat obrázky ve koupání barvy, tuto doménu vám umožní vytvářet vlastní třídění to udělat.|
    |__Kompaktní domén__| Optimalizovaná pro omezení v reálném čase klasifikace na mobilních zařízeních. Modely generované balíky compact domény je možné exportovat do spouštět místně.|
    
1. Nakonec vyberte __vytvořit projekt__.

## <a name="choose-training-images"></a>Zvolte trénovacích obrázků

Jako minimum doporučujeme že použít aspoň 30 obrázků na značky v sadě počáteční školení. Také budete chtít shromáždit několik dalších obrázků otestovat váš model, jakmile jsou trénovaná.

K natrénování modelu efektivně, pomocí celé řady visual bitové kopie. Vyberte Image s, která se liší podle:
* úhel kamery
* osvětlení
* Na pozadí
* Vizuální styl
* osoba nebo seskupené subject(s)
* velikost
* type

Kromě toho Ujistěte se, že všechny trénovacích obrázků splňovat následující kritéria:
* Formát JPG, PNG nebo BMP
* menší než velikost (4MB pro předpověď Image) 6MB
* menší než 256 pixelů na okraji nejkratší; všechny Image, která je kratší, než to se dá automaticky škálovat pomocí služby Custom Vision Service

## <a name="upload-and-tag-images"></a>Nahrání a označení obrázků

V této části se nahrát a ručně označit Image ke trénování třídění. 

1. Přidání bitové kopie, klikněte na tlačítko __přidávat image__ tlačítko a pak vyberte __procházet místní soubory__. Vyberte __otevřít__ přesunout do označení. Tag výběru se použijí pro celou skupinu imagí, které jste vybrali k nahrání, aby bylo snazší můžete nahrávat obrázky do samostatných skupin podle jejich požadované značky. Značky pro jednotlivé Image můžete také změnit poté, co se nahrály.

    ![Přidat ovládací prvek Image se zobrazí v levém horním rohu a jako tlačítko dole uprostřed.](./media/getting-started-build-a-classifier/add-images01.png)


1. Pokud chcete vytvořit značku, zadejte text do __značky__ pole a stiskněte klávesu Enter. Jestliže značky již existuje, zobrazí se v rozevírací nabídce. V multilabel projektu můžete přidat více než jeden tag Image, ale ve víc tříd projektu můžete přidat pouze jeden. Chcete-li dokončit nahrávání imagí, použijte __nahrávání souborů [číslo]__ tlačítko. 

    ![Obrázek stránky značku a nahrávání](./media/getting-started-build-a-classifier/add-images03.png)

1. Vyberte __provádí__ po bitové kopie byly nahrány.

    ![Indikátor průběhu vám ukáže všechny úlohy dokončeny.](./media/getting-started-build-a-classifier/add-images04.png)

K nahrání jinou sadu bitových kopií, vraťte se na začátku této části a opakujte kroky. V určitém okamžiku v projektu, budete muset přidat _záporné ukázky_ pro usnadnění přesnější klasifikátoru. Záporná ukázky jsou ty, které neodpovídají žádné další značky. Při nahrávání tyto Image použít speciální **negativní** popisek k nim.

## <a name="train-the-classifier"></a>Trénování klasifikátoru

K trénování třídění, vyberte **trénování** tlačítko. Třídění všech aktuálních imagí používá k vytvoření modelu, který identifikuje vizuální kvality jednotlivé značky.

![Trénování tlačítko v horní části napravo od záhlaví nástrojů webové stránky](./media/getting-started-build-a-classifier/train01.png)

Procesu trénování zabere jenom pár minut. Během této doby, zobrazí se informace o procesu trénování v **výkonu** kartu.

![Okno prohlížeče se dialogové okno školení v hlavní části](./media/getting-started-build-a-classifier/train02.png)

## <a name="evaluate-the-classifier"></a>Vyhodnocení třídění

Po dokončení trénování modelu výkonu odhad a zobrazit. Custom Vision Service používá imagí, které jste odeslali pro školení k výpočtu hodnot precision a Vzpomínáte, během procesu [k přeložení křížového ověření](https://en.wikipedia.org/wiki/Cross-validation_(statistics)). Přesnost a odvolání jsou dvě různé měření efektivity třídění:

- **Přesnost** označuje zlomek zjištěné klasifikace, které byly správně. Například pokud model identifikujete 100 bitové kopie jako psi a 99 z nich byly skutečně psů, pak přesnost by 99 %.
- **Odvolat** označuje zlomek skutečné klasifikace, které byly identifikovány správně. Například pokud bylo ve skutečnosti 100 imagí jablka a modelu identifikované jako jablka 80, bude odvolání 80 %.

![Školení výsledky zobrazit celkový přesnosti a spojené s vracením a přesnost a stránkám znovu vyvolat pro jednotlivé značky v třídění.](./media/getting-started-build-a-classifier/train03.png)

### <a name="probability-threshold"></a>Prahová hodnota pravděpodobnosti

Poznámka: **prahová hodnota pravděpodobnosti** posuvníku v levém podokně **výkonu** kartu. Toto je prahová hodnota pro předpokládané pravděpodobnost považovat správné při výpočtu přesnosti a odvolání.

Interpretace prediktivních volání s prahovou hodnotou vysoká pravděpodobnost obvykle vracet výsledky s vysokou přesností na úkor spojené s vracením (nalezené klasifikace jsou správné, ale mnoho nebyly nalezeny); Prahová hodnota nízká pravděpodobnost provádí opak (většina skutečné klasifikace nebyly nalezeny, ale existují počet falešně pozitivních výsledků v rámci této sady). To na paměti měli byste nastavit prahová hodnota pravděpodobnosti podle potřeb vašeho projektu. Novější na straně klienta, byste měli použít stejnou prahová hodnota pravděpodobnosti jako filtr při příjmu výsledků předpovědí z modelu.

## <a name="manage-training-iterations"></a>Správa iterací školení

Pokaždé, když je klasifikátor trénovat, vytvoříte nový _iterace_ s vlastní metriky výkonu aktualizované. V levém podokně můžete zobrazit všechny iterace **výkonu** kartu. Když některou vyberete, máte možnost díky tomu _výchozí iterace_ kliknutím **nastavit jako výchozí** tlačítko v horní části. _Výchozí iterace_ je model, který se použije ve výchozím nastavení při dotazování pomocí rozhraní API pro předpověď (z aplikace, například). Pokud aktualizaci odmítnete _výchozí iterace_, můžete pokračovat k natrénování modelu, aniž by to ovlivnilo aktuální chování vaší aplikace, pak Jakmile budete spokojeni s lepší modelu, můžete aktualizovat výchozí nastavení.

V levém podokně můžete také najdou **odstranit** tlačítko, které vám umožní odstranit iteraci, pokud je zastaralý. Když odstraníte iterace, odstraňte všechny Image, jednoznačně spojených s ním.

## <a name="next-steps"></a>Další postup

V této příručce jste zjistili, jak vytvořit a trénování modelu klasifikace obrázků na webu Custom Vision. V dalším kroku získáte další informace o iterativní proces zlepšit váš model.

[Testování a přetrénování modelu](test-your-model.md)

