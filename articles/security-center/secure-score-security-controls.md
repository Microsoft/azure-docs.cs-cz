---
title: Skóre zabezpečení ve službě Azure Security Center
description: Popis zabezpečeného skóre Azure Security Center a jeho ovládacích prvků zabezpečení
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetd: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2021
ms.author: memildin
ms.openlocfilehash: 24822777b06fadf87ca446d9b7ff8ba4df34adc5
ms.sourcegitcommit: 49ea056bbb5957b5443f035d28c1d8f84f5a407b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2021
ms.locfileid: "100007668"
---
# <a name="secure-score-in-azure-security-center"></a>Skóre zabezpečení ve službě Azure Security Center

## <a name="introduction-to-secure-score"></a>Úvod do zabezpečeného skóre

Azure Security Center má dva hlavní cíle: 

- pomůže vám pochopit aktuální situaci zabezpečení
- abychom vám pomohli efektivně a efektivně zlepšit zabezpečení

Centrální funkce v Security Center, která vám umožňuje dosáhnout těchto cílů je **bezpečné skóre**.

Security Center průběžně vyhodnocuje vaše prostředky, odběry a organizace pro problémy se zabezpečením. Pak agreguje všechna zjištění do jediného skóre, abyste na první pohled mohli sdělit aktuální situaci zabezpečení: čím vyšší je skóre, tím se sníží zjištěná úroveň rizika.

Zabezpečené skóre je zobrazeno na Azure Portal stránkách jako procentuální hodnota, ale základní hodnoty jsou jasně uváděny také:

:::image type="content" source="./media/secure-score-security-controls/single-secure-score-via-ui.png" alt-text="Celkové bezpečné skóre, jak je znázorněno na portálu":::

Chcete-li zvýšit zabezpečení, přečtěte si stránku s doporučeními Security Center pro nedokončené akce potřebné k vyvolání skóre. Každé doporučení obsahuje pokyny, které vám pomůžou vyřešit konkrétní problém.

Doporučení jsou seskupena do **ovládacích prvků zabezpečení**. Každý ovládací prvek je logickou skupinou souvisejících doporučení zabezpečení a odráží vaše zranitelné plochy pro útok. Vaše skóre se zlepšuje jenom při nápravě *všech* doporučení pro jeden prostředek v rámci ovládacího prvku. Pokud chcete zjistit, jak dobře vaše organizace zabezpečuje jednotlivé plochy pro útok, Projděte si skóre pro jednotlivé ovládací prvky zabezpečení.

Další informace najdete v tématu [jak se počítá vaše zabezpečené skóre](secure-score-security-controls.md#how-your-secure-score-is-calculated) níže. 


## <a name="access-your-secure-score"></a>Přístup k zabezpečenému skóre

Celkové výsledky zabezpečení a také skóre na předplatné můžete najít prostřednictvím Azure Portal nebo programově, jak je popsáno v následujících částech:

- [Získat zabezpečené skóre z portálu](#get-your-secure-score-from-the-portal)
- [Získat zabezpečené skóre z REST API](#get-your-secure-score-from-the-rest-api)
- [Získat zabezpečené skóre z Azure Resource graphu (ARG)](#get-your-secure-score-from-azure-resource-graph-arg)

### <a name="get-your-secure-score-from-the-portal"></a>Získat zabezpečené skóre z portálu

Security Center zobrazuje vaše skóre na portálu na portálu: Jedná se o první hlavní dlaždici Security Center stránku s přehledem. Kliknutím na tuto dlaždici přejdete na stránku vyhrazeného zabezpečeného skóre, kde se zobrazí skóre rozdělené podle předplatného. Když vyberete jedno předplatné, zobrazí se podrobný seznam doporučení s upřednostněním a potenciální dopad, který by oprava na skóre předplatného. 

Rekapitulace se vaše zabezpečené skóre zobrazuje v následujících umístěních na stránkách portálu Security Center.

- V dlaždici v **přehledu** Security Center (hlavní řídicí panel):

    :::image type="content" source="./media/secure-score-security-controls/score-on-main-dashboard.png" alt-text="Bezpečné skóre na řídicím panelu Security Center":::

- Na stránce vyhrazená **Zabezpečená skóre** uvidíte bezpečné skóre pro vaše předplatné a skupiny pro správu:

    :::image type="content" source="./media/secure-score-security-controls/score-on-dedicated-dashboard.png" alt-text="Zabezpečené skóre pro odběry na stránce zabezpečeného skóre Security Center":::

    :::image type="content" source="./media/secure-score-security-controls/secure-score-management-groups.png" alt-text="Zabezpečená skóre pro skupiny pro správu na stránce zabezpečeného skóre Security Center":::

    > [!NOTE]
    > Všechny skupiny pro správu, pro které nemáte dostatečná oprávnění, zobrazí skóre jako "omezené". 

- V horní části stránky **doporučení** :

    :::image type="content" source="./media/secure-score-security-controls/score-on-recommendations-page.png" alt-text="Stránka s doporučením pro bezpečné skóre Security Center":::

### <a name="get-your-secure-score-from-the-rest-api"></a>Získat zabezpečené skóre z REST API

K vašemu skóre můžete přistupovat prostřednictvím rozhraní API pro zabezpečené skóre. Metody rozhraní API poskytují flexibilitu pro dotazování na data a vytváření vlastních mechanismů generování sestav v rámci vašich zabezpečených výsledků v průběhu času. Můžete například použít [rozhraní API pro zabezpečení skóre](/rest/api/securitycenter/securescores) k získání skóre pro konkrétní předplatné. Kromě toho můžete použít [rozhraní API ovládacích prvků bezpečného řízení skóre](/rest/api/securitycenter/securescorecontrols) k vypsání ovládacích prvků zabezpečení a aktuálního skóre vašich předplatných.

![Načtení jediného zabezpečeného skóre přes rozhraní API](media/secure-score-security-controls/single-secure-score-via-api.png)

Příklady nástrojů postavených na rozhraní API pro zajištění skóre najdete v části [bezpečné skóre naší komunity GitHubu](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score). 

### <a name="get-your-secure-score-from-azure-resource-graph-arg"></a>Získat zabezpečené skóre z Azure Resource graphu (ARG)

Azure Resource Graph poskytuje okamžitý přístup k informacím o prostředcích napříč vašimi cloudovým prostředím pomocí robustních funkcí filtrování, seskupování a řazení. Jedná se o rychlý a efektivní způsob dotazování informací v rámci předplatných Azure prostřednictvím Azure Portal. [Přečtěte si další informace o Azure Resource graphu](../governance/resource-graph/index.yml).

Pro přístup k zabezpečenému skóre pro více předplatných s ARG:

1. V Azure Portal otevřete **Průzkumníka Azure Resource Graph**.

    :::image type="content" source="./media/security-center-identity-access/opening-resource-graph-explorer.png" alt-text="Spouští se stránka pro doporučení pro Azure Resource Graph Explorer * *" :::

1. Zadejte svůj dotaz Kusto (použijte níže uvedené příklady pro doprovodné materiály).

    - Tento dotaz vrátí ID předplatného, aktuální skóre v bodech a jako procento a maximální skóre pro předplatné. 

        ```kusto
        SecurityResources 
        | where type == 'microsoft.security/securescores' 
        | extend current = properties.score.current, max = todouble(properties.score.max)
        | project subscriptionId, current, max, percentage = ((current / max)*100)
        ```

    - Tento dotaz vrátí stav všech ovládacích prvků zabezpečení. Pro každý ovládací prvek získáte počet prostředků, které nejsou v pořádku, aktuální skóre a maximální skóre. 

        ```kusto
        SecurityResources 
        | where type == 'microsoft.security/securescores/securescorecontrols'
        | extend SecureControl = properties.displayName, unhealthy = properties.unhealthyResourceCount, currentscore = properties.score.current, maxscore = properties.score.max
        | project SecureControl , unhealthy, currentscore, maxscore
        ```

1. Vyberte **Spustit dotaz**.




## <a name="tracking-your-secure-score-over-time"></a>Sledování zabezpečeného skóre v průběhu času

Pokud jste Power BI uživatel s účtem pro, můžete pomocí řídicího panelu **zabezpečení skóre v čase** Power BI sledovat vaše zabezpečené skóre v průběhu času a prozkoumat změny.

> [!TIP]
> Tento řídicí panel a další nástroje pro práci programově se zabezpečeným skóre najdete ve vyhrazené oblasti Azure Security Center komunity na GitHubu: https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score

Řídicí panel obsahuje následující dvě sestavy, které vám pomůžou analyzovat stav zabezpečení:

- **Shrnutí prostředků** – poskytuje souhrnná data týkající se stavu vašich prostředků.
- **Shrnutí bezpečného skóre** – poskytuje souhrnná data týkající se průběhu vašeho skóre. Pomocí grafu "bezpečné skóre v průběhu času na předplatné" zobrazíte změny ve výsledku. Pokud si všimnete výrazné změny skóre, podívejte se do tabulky "zjištěné změny, které mohou ovlivnit vaši zabezpečený počet", a zjistěte možné změny, které by mohly způsobit změnu. Tato tabulka uvádí odstraněné prostředky, nově nasazené prostředky nebo prostředky, u kterých se změnil stav zabezpečení pro některá z doporučení.

:::image type="content" source="./media/secure-score-security-controls/power-bi-secure-score-dashboard.png" alt-text="Volitelné zabezpečené skóre v průběhu času Power BI řídicím panelu pro sledování zabezpečeného skóre v průběhu času a vyšetřování změn":::





## <a name="how-your-secure-score-is-calculated"></a>Jak se počítá vaše zabezpečené skóre 

Na stránce doporučení se jasně zobrazuje příspěvek každého řízení bezpečnosti k celkovému zabezpečenému skóre.

[![Rozšířené zabezpečené skóre zavádí ovládací prvky zabezpečení.](media/secure-score-security-controls/security-controls.png)](media/secure-score-security-controls/security-controls.png#lightbox)

Abyste získali všechny možné body pro řízení zabezpečení, všechny vaše prostředky musí splňovat všechna doporučení zabezpečení v rámci řízení bezpečnosti. Security Center například obsahuje více doporučení týkajících se zabezpečení portů pro správu. Budete je muset vyřešit všemi, aby se zajistil rozdíl na zabezpečeném skóre.

Například řízení zabezpečení s názvem použít aktualizace systému má maximální skóre šesti bodů, které vidíte v popisku na možné zvýšení hodnoty ovládacího prvku:

[![Řízení zabezpečení použít aktualizace systému](media/secure-score-security-controls/apply-system-updates-control.png)](media/secure-score-security-controls/apply-system-updates-control.png#lightbox)

Maximální skóre tohoto ovládacího prvku, použít aktualizace systému, je vždy 6. V tomto příkladu jsou k dispozici 50 prostředků. Proto rozdělíme maximální skóre na 50 a výsledkem je, že každý prostředek přispívá 0,12 bodů. 

* **Možné zvýšení** (0,12 x 8 špatných prostředků = 0,96) – zbývající body, které jsou k dispozici v rámci ovládacího prvku. Pokud opravíte všechna doporučení v tomto ovládacím prvku, skóre se zvýší o 2% (v tomto případě 0,96 bodů zaokrouhlených až na 1 bod). 
* **Aktuální skóre** (0,12 x 42 zdravých prostředků = 5,04) – aktuální skóre tohoto ovládacího prvku. Každý ovládací prvek přispívá k celkovému skóre. V tomto příkladu je ovládací prvek přispívající na aktuální zabezpečený součet 5,04 bodů.
* **Maximální skóre** – maximální počet bodů, které můžete získat provedením všech doporučení v rámci ovládacího prvku. Maximální skóre pro ovládací prvek označuje relativní význam tohoto ovládacího prvku. Pomocí hodnot maximálního skóre můžete určit, jak se mají problémy pracovat jako první. 


### <a name="calculations---understanding-your-score"></a>Výpočty – princip skóre

|Metric|Vzorec a příklad|
|-|-|
|**Aktuální skóre ovládacího prvku zabezpečení**|<br>![Rovnice pro výpočet skóre ovládacího prvku zabezpečení](media/secure-score-security-controls/secure-score-equation-single-control.png)<br><br>Každé individuální řízení zabezpečení přispívá ke skóre zabezpečení. Každý prostředek, na který má vliv doporučení v rámci ovládacího prvku, přispívá k aktuálnímu skóre ovládacího prvku. Aktuální skóre pro každý ovládací prvek je míra stavu prostředků *v rámci* ovládacího prvku.<br>![Popisy tlačítek znázorňující hodnoty použité při výpočtu aktuálního skóre ovládacího prvku zabezpečení](media/secure-score-security-controls/security-control-scoring-tooltips.png)<br>V tomto příkladu je maximální skóre 6 děleno 78, protože to je součet zdravých a špatných prostředků.<br>6/78 = 0,0769<br>Vynásobení tohoto počtu zdravých prostředků (4) má za následek aktuální skóre:<br>0,0769 * 4 = **0,31**<br><br>|
|**Bezpečnostní skóre**<br>Jedno předplatné|<br>![Rovnice pro výpočet zabezpečeného skóre předplatného](media/secure-score-security-controls/secure-score-equation-single-sub.png)<br><br>![Zabezpečené skóre jednotného předplatného se všemi povolenými ovládacími prvky](media/secure-score-security-controls/secure-score-example-single-sub.png)<br>V tomto příkladu je k dispozici jedno předplatné se všemi dostupnými ovládacími prvky zabezpečení (potenciální maximální skóre 60 bodů). Skóre zobrazuje 28 bodů z možného 60 a zbývající 32 body se projeví v hodnotách "potenciálního nárůstu" v rámci ovládacích prvků zabezpečení.<br>![Seznam ovládacích prvků a možné zvýšení skóre](media/secure-score-security-controls/secure-score-example-single-sub-recs.png)|
|**Bezpečnostní skóre**<br>Několik předplatných|<br>![Rovnice pro výpočet zabezpečeného skóre pro více předplatných](media/secure-score-security-controls/secure-score-equation-multiple-subs.png)<br><br>Při výpočtu kombinovaného skóre pro více předplatných Security Center zahrnuje *váhu* pro každé předplatné. Relativní váhy pro vaše předplatná jsou určené Security Center na základě faktorů, jako je počet prostředků.<br>Aktuální skóre pro každé předplatné se počítá stejným způsobem jako u jednoho předplatného, ale tato váha se použije, jak je znázorněno v rovnici.<br>Při prohlížení více předplatných vyhodnotí zabezpečené skóre všechny prostředky ve všech povolených zásadách a seskupují jejich kombinovaný dopad na maximální skóre každého ovládacího prvku zabezpečení.<br>![Bezpečné skóre pro více předplatných se zapnutými všemi ovládacími prvky](media/secure-score-security-controls/secure-score-example-multiple-subs.png)<br>Kombinované skóre **nepředstavuje průměr** ; místo toho se jedná o vyhodnocený stav stavu všech prostředků ve všech předplatných.<br>Pokud se zobrazí stránka doporučení a přidáte potenciální dostupné body, zjistíte, že se jedná o rozdíl mezi aktuálním skóre (24) a maximálním dostupným skóre (60).|
||||

### <a name="which-recommendations-are-included-in-the-secure-score-calculations"></a>Která doporučení jsou zahrnutá ve výpočtech zabezpečeného skóre?

Bezpečnostní skóre mají dopad jenom na integrovaná doporučení.

Doporučení, která jsou označená jako **Náhled** , nejsou zahrnutá do výpočtů zabezpečeného skóre. Měly by se pořád opravit všude, kde je to možné, takže po skončení období Preview budou přispět ke svému skóre.

Příklad doporučení verze Preview:

:::image type="content" source="./media/secure-score-security-controls/example-of-preview-recommendation.png" alt-text="Doporučení s příznakem Preview":::

## <a name="improve-your-secure-score"></a>Zlepšení skóre Secure Score

Pokud chcete vylepšit vaše zabezpečené skóre, napravte doporučení zabezpečení ze seznamu doporučení. Každé doporučení můžete opravit ručně pro každý prostředek nebo pomocí **rychlé opravy.** možnost (Pokud je dostupná), pokud chcete použít nápravu pro doporučení na skupinu prostředků rychle. Další informace najdete v tématu o [opravách doporučení](security-center-remediate-recommendations.md).

Dalším způsobem, jak vylepšit skóre a zajistit, aby uživatelé nevytvářeli prostředky, které negativně ovlivňují vaše skóre, je nakonfigurovat možnosti vyhovět a odepřít na relevantních doporučeních. Další informace o tom, jak [zabránit v neoprávněných konfiguracích s doporučeními pro vymáhání](prevent-misconfigurations.md)

## <a name="security-controls-and-their-recommendations"></a>Ovládací prvky zabezpečení a jejich doporučení

Následující tabulka uvádí ovládací prvky zabezpečení v Azure Security Center. U každého ovládacího prvku uvidíte maximální počet bodů, které můžete přidat do svého zabezpečeného skóre, pokud opravíte *všechna* doporučení uvedená v ovládacím prvku pro *všechny* vaše prostředky. 

Sada doporučení zabezpečení dodaná s Security Center je přizpůsobená dostupným prostředkům v prostředí jednotlivých organizací. Doporučení se dají dál upravovat tím, že [zakážete zásady](tutorial-security-policy.md#disable-security-policies-and-disable-recommendations) a [vyloučíte konkrétní prostředky z doporučení](exempt-resource.md). 
 
Doporučujeme, aby se v každé organizaci pečlivě zkontrolovaly přiřazené Azure Policy iniciativy. 

> [!TIP]
> Podrobnosti o kontrole a úpravách iniciativ najdete v tématu [práce se zásadami zabezpečení](tutorial-security-policy.md). 

I když je výchozí iniciativa zabezpečení Security Center založená na osvědčených postupech a standardech, existují scénáře, ve kterých jsou předdefinovaná doporučení uvedená níže pravděpodobně zcela nevejdou do vaší organizace. V důsledku toho bude někdy nutné upravit výchozí iniciativu – bez narušení zabezpečení – zajistíte tak, aby byla zarovnána s vlastními zásadami vaší organizace. oborové standardy, regulativní normy a srovnávací testy, které jsou povinny splnit.<br><br>
<div class="foo">

<style type="text/css"> . TG {Border-sbalení: sbalení; Border-rozteč: 0;}. TG TD {Border-Color: Black; Border-Style: Solid; border-width: 1px; font-family: Arial, Sans-Serif; font-size: 14px; přetečení: Hidden; odsazení textu: 10px 5px; dělení na slova: normální;}. TG th {Border-Color: Black; styl okraje: Solid; Šířka ohraničení: 1px; font-family: Arial, Sans-Serif; font-size: 18px; font-weight: Normal; přetečení: Hidden; odsazení: 10px 5px; slovo-break: Normal;}. TG. TG-cly1 {text-align: Left; vertikální zarovnání: prostřední}. TG. TG-lboi {Border-Color: zdědit; text zarovnání: Left; vertikální zarovnání: prostřední} </style>

[!INCLUDE [security-center-controls-and-recommendations](../../includes/asc/security-control-recommendations.md)]

</div>




## <a name="secure-score-faq"></a>Nejčastější dotazy k zabezpečení skóre

### <a name="if-i-address-only-three-out-of-four-recommendations-in-a-security-control-will-my-secure-score-change"></a>Pokud v ovládacím prvku zabezpečení řeším jenom tři doporučení ze čtyř, bude se můj bezpečnostní skóre měnit?
No. Nemění se, dokud neopravíte všechna doporučení pro jeden prostředek. Chcete-li získat maximální skóre pro ovládací prvek, je nutné opravit všechna doporučení pro všechny prostředky.

### <a name="if-a-recommendation-isnt-applicable-to-me-and-i-disable-it-in-the-policy-will-my-security-control-be-fulfilled-and-my-secure-score-updated"></a>Pokud doporučení neplatí pro mě a je v zásadách zakázané, bude řízení zabezpečení splněné a mé zabezpečené skóre se aktualizovalo?
Ano. Doporučujeme zakázat doporučení, pokud jsou ve vašem prostředí nepoužitá. Pokyny, jak zakázat konkrétní doporučení, najdete v tématu [zakázání zásad zabezpečení](./tutorial-security-policy.md#disable-security-policies-and-disable-recommendations).

### <a name="if-a-security-control-offers-me-zero-points-towards-my-secure-score-should-i-ignore-it"></a>Pokud řízení zabezpečení nabízí nulové body směrem k mému zabezpečenému skóre, je vhodné je ignorovat?
V některých případech se zobrazí maximální skóre v rámci ovládacího prvku větší než nula, ale dopad je nula. Když je přírůstkové skóre pro opravu prostředků zanedbatelné, zaokrouhlí se na nulu. Tato doporučení neignorujte, protože stále přinášejí vylepšení zabezpečení. Jedinou výjimkou je ovládací prvek "další osvědčené postupy". Oprava tato doporučení nezvýší vaše skóre, ale zvýší vaše celkové zabezpečení.

## <a name="next-steps"></a>Další kroky

Tento článek popisuje bezpečné skóre a řídí zabezpečení, které zavádí. Související materiály najdete v následujících článcích:

- [Další informace o různých prvcích doporučení](security-center-recommendations.md)
- [Informace o tom, jak opravit doporučení](security-center-remediate-recommendations.md)
- [Zobrazit nástroje založené na GitHubu pro práci programově se zabezpečeným skóre](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score)