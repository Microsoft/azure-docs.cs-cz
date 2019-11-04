---
title: Konfigurace služby Personalizace
titleSuffix: Azure Cognitive Services
description: Konfigurace služby zahrnuje způsob, jakým služba zpracovává ceny, jak často se služba zkoumá, jak často se model překládá a kolik dat se ukládá.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: diberry
ms.openlocfilehash: 4ab1dcf4f3554c941107ec653f717b3680543da2
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73490730"
---
# <a name="configure-personalizer"></a>Konfigurace služby Personalizace

Konfigurace služby zahrnuje způsob, jakým služba zpracovává ceny, jak často se služba zkoumá, jak často se model překládá a kolik dat se ukládá.

## <a name="create-personalizer-resource"></a>Vytvořit prostředek přizpůsobeného pro přizpůsobování

Vytvořte prostředek pro přizpůsobení pro každou smyčku zpětné vazby. 

1. Přihlaste se k [portálu Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer). Předchozí odkaz vás přesměruje na stránku pro **Vytvoření** služby pro přizpůsobení. 
1. Zadejte název služby, vyberte předplatné, umístění, cenovou úroveň a skupinu prostředků.
1. Vyberte potvrzení a vyberte **vytvořit**.

<a name="configure-service-settings-in-the-azure-portal"></a>

## <a name="configure-service-in-the-azure-portal"></a>Konfigurace služby v Azure Portal

1. Přihlaste se na web [Azure Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer).
1. Najděte prostředek pro přizpůsobování. 
1. V části **Správa prostředků** vyberte **Konfigurace**.

    Před ukončením Azure Portal zkopírujte jeden ze svých klíčů prostředků ze stránky **klíče** . Budete ho potřebovat k použití [sady personalizovat SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer).

<a name="configure-reward-settings-for-the-feedback-loop-based-on-use-case"></a>

### <a name="configure-reward-for-the-feedback-loop-based-on-use-case"></a>Konfigurace odměňování pro smyčku zpětné vazby na základě případu použití

Nakonfigurujte službu pro použití smyčky zpětné vazby. Změny následujících hodnot obnoví aktuální model přizpůsobeného přizpůsobování a znovu ho provede za poslední 2 dny v datech:

![Konfigurovat hodnoty odměňování pro smyčku zpětné vazby](media/settings/configure-model-reward-settings.png)

|Hodnota|Účel|
|--|--|
|Doba čekání na odměnu|Nastaví dobu, během které bude přizpůsobené aplikace shromažďovat hodnoty pro volání pořadí od okamžiku, kdy dojde k volání pořadí. Tato hodnota je nastavená dotazem: jak dlouho by měl přizpůsobený systém čekat na neprospěch volání? Jakékoli odměny, které přicházejí do tohoto okna, se budou protokolovat, ale nepoužijí se ke učení.|
|Výchozí odměna|Pokud přizpůsobené není žádné volání na základě odměny během časového intervalu čekání na odměnu přidruženého k volání pořadí, přizpůsobování přiřadí výchozí odměnu. Ve výchozím nastavení a ve většině scénářů je výchozí odměna nula.|
|Agregace odměňování|Pokud se pro stejné volání rozhraní API pořadí přijímá více než jeden případ, použije se tato agregační metoda: **Sum** nebo **nejstarší**. Nejstarší vybere nejstarší skóre, které zbytek přijal a zahodí. To je užitečné, pokud chcete jedinečnou měnu mezi potenciálně duplicitními voláními. |

Po změně těchto hodnot nezapomeňte vybrat **Save (Uložit**).

### <a name="configure-exploration"></a>Konfigurovat průzkum 

Přizpůsobení je schopné zjistit nové vzory a přizpůsobit se změnám chování uživatelů v průběhu času prozkoumáním alternativ. Hodnota **průzkumu** určuje, jaké procento volání klasifikace je zodpovězeno s průzkumem. 

Změny této hodnoty obnoví aktuální model přizpůsobeného přizpůsobování a znovu ho přeškolí za poslední 2 dny dat.

![Hodnota průzkumu určuje, jaké procento volání klasifikace je zodpovězeno s průzkumem.](media/settings/configure-exploration-setting.png)

Po změně této hodnoty nezapomeňte vybrat **Save (Uložit**).

### <a name="model-update-frequency"></a>Frekvence aktualizace modelu

Poslední model, který je z každé aktivní události vyškolený z volání rozhraní API, není automaticky použit v rámci volání pořadí řazení. **Frekvence aktualizace modelu** nastavuje, jak často se model používaný pořadím volá aktualizovaný. 

Vysoké frekvence aktualizací modelu jsou užitečné v situacích, kdy chcete pozorně sledovat změny chování uživatelů. Mezi příklady patří weby, které běží na aktivních novinkách, Virovém obsahu nebo živých nabídkách produktů. V těchto scénářích můžete použít frekvenci na 15 minut. Pro většinu případů použití je nižší frekvence aktualizace platná. Jednorázová frekvence aktualizace je užitečná při ladění kódu aplikace pomocí přizpůsobeného, provádění demo ukázek nebo interaktivního testování aspektů strojového učení.

![Frekvence aktualizace modelu nastavuje, jak často se nový model přizpůsobování přeučení.](media/settings/configure-model-update-frequency-settings-15-minutes.png)

Po změně této hodnoty nezapomeňte vybrat **Save (Uložit**).

### <a name="data-retention"></a>Uchování dat

**Doba uchovávání dat** určuje, kolik dnů přizpůsobování udržuje protokoly dat. Minulé datové protokoly se vyžadují k provedení [hodnocení offline](concepts-offline-evaluation.md), které slouží k měření efektivity přizpůsobování a optimalizace výukových zásad.

Po změně této hodnoty nezapomeňte vybrat **Save (Uložit**).

## <a name="export-the-personalizer-model"></a>Export modelu přizpůsobeného pro přizpůsobování

V části Správa prostředků pro **Nastavení modelu a učení**si prohlédněte vytváření modelů a datum poslední aktualizace a exportujte aktuální model. Pomocí Azure Portal nebo rozhraní API pro přizpůsobení můžete exportovat soubor modelu pro účely archivace. 

![Exportovat aktuální model přizpůsobeného přizpůsobování](media/settings/export-current-personalizer-model.png)

## <a name="import-and-export-learning-policy"></a>Zásady učení pro import a export

V části Správa prostředků pro **Nastavení modelu a učení**importujte nové zásady učení nebo exportujte aktuální zásady učení.
Můžete získat soubory zásad učení z předchozích exportů nebo stáhnout optimalizované zásady zjištěné při offline hodnocení. Ruční změny těchto souborů budou mít vliv na výkon strojového učení a přesnost offline vyhodnocení a společnost Microsoft nebude moci získat informace o přesnosti strojového učení a hodnocení nebo o výjimkách služby vyplývajících z ručně upravených zásad.

## <a name="clear-data-for-your-learning-loop"></a>Vymazat data pro studijní smyčku

1. V Azure Portal na stránce **Nastavení modelu a učení** vyberte u svého prostředku pro přizpůsobování možnost **Vymazat data**.
1. Pokud chcete vymazat všechna data a resetovat smyčku učení do původního stavu, zaškrtněte políčko všechna 3 zaškrtávací políčka.

    ![V Azure Portal vymažte data z prostředku pro přizpůsobování.](./media/settings/clear-data-from-personalizer-resource.png)

    |Hodnota|Účel|
    |--|--|
    |Zaprotokolované individuální přizpůsobení a data odměna.|Tato data protokolování se používají při offline hodnocení. Vymažte data při resetování prostředku.|
    |Resetujte model přizpůsobeného přizpůsobování.|Tento model se při každém přeškolení mění. Tato frekvence školení je určena v **frekvence nahrávání modelu** na stránce **Konfigurace** . |
    |Nastavte zásady učení na výchozí.|Pokud jste zásady učení změnili jako součást testování offline, tato zásada obnoví původní zásady učení.|

1. Zaškrtnutím **políčka vymazat vybraná data** zahajte proces mazání. Stav je hlášen v oznámeních Azure v pravém horním navigačním panelu. 

## <a name="next-steps"></a>Další kroky


[Informace o dostupnosti oblastí](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)
