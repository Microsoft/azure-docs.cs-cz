---
title: Konfigurace nastavení – přizpůsobení
titleSuffix: Azure Cognitive Services
description: Konfigurace služby zahrnuje způsob, jakým služba zpracovává ceny, jak často se služba zkoumá, jak často se model překládá a kolik dat se ukládá.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 09/30/2019
ms.author: diberry
ms.openlocfilehash: bad581fbc53292b5a7c25157ef839e07f33e131e
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2019
ms.locfileid: "71827878"
---
# <a name="personalizer-settings"></a>Nastavení přizpůsobení

Konfigurace služby zahrnuje způsob, jakým služba zpracovává ceny, jak často se služba zkoumá, jak často se model překládá a kolik dat se ukládá.

## <a name="create-personalizer-resource"></a>Vytvořit prostředek přizpůsobeného pro přizpůsobování

Vytvořte prostředek pro přizpůsobení pro každou smyčku zpětné vazby. 

1. Přihlaste se k [portálu Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer). Předchozí odkaz vás přesměruje na stránku pro **Vytvoření** služby pro přizpůsobení. 
1. Zadejte název služby, vyberte předplatné, umístění, cenovou úroveň a skupinu prostředků.
1. Vyberte potvrzení a vyberte **vytvořit**.

## <a name="configure-service-settings-in-the-azure-portal"></a>Konfigurace nastavení služby v Azure Portal

1. Přihlaste se na web [Azure Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer).
1. Najděte prostředek pro přizpůsobování. 
1. V části **Správa prostředků** vyberte **Nastavení**.

    Před ukončením Azure Portal zkopírujte jeden ze svých klíčů prostředků ze stránky **klíče** . Budete ho potřebovat k použití [sady personalizovat SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer).

### <a name="configure-reward-settings-for-the-feedback-loop-based-on-use-case"></a>Konfigurace nastavení odměňování pro smyčku zpětné vazby na základě případu použití

Nakonfigurujte nastavení služby pro použití smyčky zpětné vazby. Změny následujícího nastavení obnoví aktuální model přizpůsobeného přizpůsobování a znovu ho provede za poslední 2 dny v datech:

![Konfigurace nastavení odměňování pro smyčku zpětné vazby](media/settings/configure-model-reward-settings.png)

|Nastavení|Účel|
|--|--|
|Doba čekání na odměnu|Nastaví dobu, během které bude přizpůsobené aplikace shromažďovat hodnoty pro volání pořadí od okamžiku, kdy dojde k volání pořadí. Tato hodnota je nastavená dotazem: jak dlouho by měl přizpůsobený systém čekat na neprospěch volání? Jakékoli odměny, které přicházejí do tohoto okna, se budou protokolovat, ale nepoužijí se ke učení.|
|Výchozí odměna|Pokud přizpůsobené není žádné volání na základě odměny během časového intervalu čekání na odměnu přidruženého k volání pořadí, přizpůsobování přiřadí výchozí odměnu. Ve výchozím nastavení a ve většině scénářů je výchozí odměna nula.|
|Agregace odměňování|Pokud se pro stejné volání rozhraní API pořadí přijímá více než jeden případ, použije se tato agregační metoda: **Sum** nebo **nejstarší**. Nejstarší vybere nejstarší skóre, které zbytek přijal a zahodí. To je užitečné, pokud chcete jedinečnou měnu mezi potenciálně duplicitními voláními. |

Po změně těchto nastavení nezapomeňte vybrat **Uložit**.

### <a name="exploration-setting"></a>Nastavení průzkumu 

Přizpůsobení je schopné zjistit nové vzory a přizpůsobit se změnám chování uživatelů v průběhu času prozkoumáním alternativ. Nastavení **průzkumu** určuje, jaké procento volání pořadí je zodpovězeno s průzkumem. 

Změny tohoto nastavení obnoví aktuální model přizpůsobeného přizpůsobování a znovu ho přeškolí za poslední 2 dny dat.

![Nastavení průzkumu určuje, jaké procento volání pořadí je zodpovězeno s průzkumem.](media/settings/configure-exploration-setting.png)

Po změně tohoto nastavení nezapomeňte vybrat **Uložit**.

### <a name="model-update-frequency"></a>Frekvence aktualizace modelu

Poslední model, který je z každé aktivní události vyškolený z volání rozhraní API, není automaticky použit v rámci volání pořadí řazení. **Frekvence aktualizace modelu** nastavuje, jak často se model používaný pořadím volá aktualizovaný. 

Vysoké frekvence aktualizací modelu jsou užitečné v situacích, kdy chcete pozorně sledovat změny chování uživatelů. Mezi příklady patří weby, které běží na aktivních novinkách, Virovém obsahu nebo živých nabídkách produktů. V těchto scénářích můžete použít frekvenci na 15 minut. Pro většinu případů použití je nižší frekvence aktualizace platná. Jednorázová frekvence aktualizace je užitečná při ladění kódu aplikace pomocí přizpůsobeného, provádění demo ukázek nebo interaktivního testování aspektů strojového učení.

![Frekvence aktualizace modelu nastavuje, jak často se nový model přizpůsobování přeučení.](media/settings/configure-model-update-frequency-settings-15-minutes.png)

Po změně tohoto nastavení nezapomeňte vybrat **Uložit**.

### <a name="data-retention"></a>Uchování dat

**Doba uchovávání dat** určuje, kolik dnů přizpůsobování udržuje protokoly dat. Minulé datové protokoly se vyžadují k provedení [hodnocení offline](concepts-offline-evaluation.md), které slouží k měření efektivity přizpůsobování a optimalizace výukových zásad.

Po změně tohoto nastavení nezapomeňte vybrat **Uložit**.

## <a name="export-the-personalizer-model"></a>Export modelu přizpůsobeného pro přizpůsobování

Z oddílu správy prostředků pro **model a zásady**zkontrolujte vytváření modelů a datum poslední aktualizace a exportujte aktuální model. Pomocí Azure Portal nebo rozhraní API pro přizpůsobení můžete exportovat soubor modelu pro účely archivace. 

![Exportovat aktuální model přizpůsobeného přizpůsobování](media/settings/export-current-personalizer-model.png)

## <a name="import-and-export-learning-policy"></a>Zásady učení pro import a export

V části Správa prostředků pro **model a zásady**importujte nové zásady učení nebo exportujte aktuální zásady učení.
Můžete získat soubory zásad učení z předchozích exportů nebo stáhnout optimalizované zásady zjištěné při offline hodnocení. Ruční změny těchto souborů budou mít vliv na výkon strojového učení a přesnost offline vyhodnocení a společnost Microsoft nebude moci získat informace o přesnosti strojového učení a hodnocení nebo o výjimkách služby vyplývajících z ručně upravených zásad.

## <a name="clear-data-for-your-learning-loop"></a>Vymazat data pro studijní smyčku

1. V Azure Portal pro prostředek přizpůsobeného pro přizpůsobování vyberte na stránce **model a zásady** možnost **Vymazat data**.
1. Pokud chcete vymazat všechna data a resetovat smyčku učení do původního stavu, zaškrtněte políčko všechna 3 zaškrtávací políčka.

    ![V Azure Portal vymažte data z prostředku pro přizpůsobování.](./media/settings/clear-data-from-personalizer-resource.png)

    |Nastavení|Účel|
    |--|--|
    |Zaprotokolované individuální přizpůsobení a data odměna.|Tato data protokolování se používají při offline hodnocení. Vymažte data při resetování prostředku.|
    |Resetujte model přizpůsobeného přizpůsobování.|Tento model se při každém přeškolení mění. Tato frekvence školení je určena v **frekvence nahrávání modelu** na stránce **Nastavení** . |
    |Nastavte zásady učení na výchozí.|Pokud jste zásady učení změnili jako součást testování offline, tato zásada obnoví původní zásady učení.|

1. Zaškrtnutím **políčka vymazat vybraná data** zahajte proces mazání. Stav je hlášen v oznámeních Azure v pravém horním navigačním panelu. 

## <a name="next-steps"></a>Další kroky

<!--
[How to use the Personalizer container](https://go.microsoft.com/fwlink/?linkid=2083923&clcid=0x409)
-->
[Informace o dostupnosti oblastí](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)
