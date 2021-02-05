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
ms.openlocfilehash: bd64ca877f0bfd81597e091ef8ef000ad1b76992
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2021
ms.locfileid: "99526428"
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
<table class="tg">
<thead>
  <tr>
    <th class="tg-cly1"><b>Řízení zabezpečení, skóre a popis</b><br></th>
    <th class="tg-cly1"><b>Doporučení</b></th>
  </tr>
</thead>
<tbody>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Povolit MFA (max. skóre 10)</p></strong>Pokud k ověření uživatele použijete jenom heslo, opustí vektor útoku otevřený. Pokud je heslo slabá nebo je vystavené jinde, je to opravdu se uživatel přihlašuje pomocí uživatelského jména a hesla?<br>Když je povolený <a href="https://www.microsoft.com/security/business/identity/mfa">vícefaktorové ověřování</a> , vaše účty jsou bezpečnější a uživatelé se můžou i nadále ověřovat pro skoro jakoukoli aplikaci s jednotným přihlašováním (SSO).</td>
    <td class="tg-lboi"; width=55%>- Pro účty s oprávněním vlastníka pro vaše předplatné by se měla povolit vícefaktorové ověřování.<br>- V rámci vašeho předplatného by měly být povolené účty s oprávněním k zápisu.</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Porty pro zabezpečenou správu (maximální skóre 8)</p></strong>Útoky hrubou silou cílí na porty správy, aby získaly přístup k virtuálnímu počítači. Vzhledem k tomu, že porty není vždy nutné otevírat, je jedinou strategií pro zmírnění rizik omezit vystavení na porty pomocí řízení přístupu k síti, skupin zabezpečení sítě a správy portů virtuálních počítačů.<br>Vzhledem k tomu, že mnoho organizací IT neblokuje komunikaci přes protokol SSH ze své sítě, můžou útočníci vytvořit šifrované tunely, které umožňují, aby porty protokolu RDP v napadených systémech komunikovaly s příkazem útočníka k řízení serverů. Útočníci můžou použít podsystém Vzdálená správa systému Windows k pozdějšímu přesunu napříč vaším prostředím a používat odcizené přihlašovací údaje pro přístup k dalším prostředkům v síti.</td>
    <td class="tg-lboi"; width=55%>- Porty pro správu virtuálních počítačů by měly být chráněné pomocí řízení přístupu k síti za běhu<br>- Virtuální počítače by měly být přidružené ke skupině zabezpečení sítě.<br>- Porty pro správu by měly být uzavřeny na virtuálních počítačích</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Použít aktualizace systému (maximální skóre 6)</p></strong>Aktualizace systému poskytují organizacím možnost udržet provozní efektivitu, snižovat zranitelnosti zabezpečení a poskytovat stabilnější prostředí pro koncové uživatele. Pokud aktualizace nepoužijete, zůstanou neopravená ohrožení zabezpečení a výsledky v prostředích, která jsou náchylná k útokům. Tyto chyby zabezpečení je možné zneužít a vést k úniku dat, exfiltrace dat, ransomwarem a zneužití prostředků. Pokud chcete nasadit aktualizace systému, můžete pomocí <a href="/azure/automation/update-management/overview">Update Management řešení spravovat opravy a aktualizace</a> virtuálních počítačů. Správa aktualizací je proces řízení nasazení a údržby vydaných verzí softwaru.</td>
    <td class="tg-lboi"; width=55%>- Problémy se stavem agenta monitorování by se měly vyřešit na vašich počítačích.<br>- Agent monitorování by měl být nainstalovaný na Virtual Machine Scale Sets<br>- Agent monitorování by měl být nainstalovaný na vašich počítačích.<br>- Verze operačního systému by se měla aktualizovat pro vaše role cloudové služby.<br>- Musí být nainstalované aktualizace systému ve virtuálních počítačích Virtual Machine Scale Sets.<br>- Do vašich počítačů by se měly nainstalovat aktualizace systému<br>- Počítače by měly být restartovány, aby bylo možné použít aktualizace systému<br>- Služby Kubernetes by se měly upgradovat na nezranitelnou Kubernetes verzi.<br>- Agent monitorování by měl být nainstalovaný na virtuálních počítačích<br>- Agent Log Analytics musí být nainstalovaný na počítačích se systémem Windows Azure ARC (Preview).<br>- Agent Log Analytics musí být nainstalovaný na počítačích se systémem Linux (Preview).</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Náprava ohrožení zabezpečení (maximální skóre 6)</p></strong>Ohrožení zabezpečení je slabé stránky, které může útočník pro hrozby využít k zabezpečení důvěrnosti, dostupnosti nebo integrity prostředku. <a href="/windows/security/threat-protection/microsoft-defender-atp/next-gen-threat-and-vuln-mgt">Správa slabých míst</a> snižuje expozici organizace, posílení oblasti koncového bodu, zvyšuje odolnost organizace a snižuje prostor pro útoky na prostředky. Správa hrozeb a ohrožení zabezpečení poskytuje přehled o chybných konfiguracích softwaru a zabezpečení a poskytuje doporučení pro zmírnění rizik.</td>
    <td class="tg-lboi"; width=55%>- V SQL Database by mělo být povolené rozšířené zabezpečení dat.<br>- Ohrožení zabezpečení v Azure Container Registrych imagí by mělo být opraveno<br>- Ohrožení zabezpečení vašich databází SQL by mělo být opraveno<br>- Ohrožení zabezpečení by se mělo opravit řešením posouzení ohrožení zabezpečení.<br>- Posouzení ohrožení zabezpečení by mělo být povoleno na spravované instanci SQL<br>- Na vašich serverech SQL by mělo být povolené posouzení ohrožení zabezpečení<br>- Řešení posouzení ohrožení zabezpečení by se mělo nainstalovat na virtuální počítače.<br>- Image kontejneru by se měly nasadit jenom z důvěryhodných registrů (Preview).<br>- V clusterech se musí nainstalovat a povolit doplněk Azure Policy Kubernetes (Preview).</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Povolit šifrování v klidovém umístění (maximální skóre 4)</p></strong><a href="/azure/security/fundamentals/encryption-atrest">Šifrování v klidovém umístění</a> poskytuje ochranu dat pro uložená data. Mezi útoky na data v klidovém umístění patří pokusy o získání fyzického přístupu k hardwaru, na kterém jsou data uložená. Azure používají symetrické šifrování k šifrování a dešifrování velkých objemů dat v klidovém provozu. Symetrický šifrovací klíč se používá k šifrování dat při zápisu do úložiště. Tento šifrovací klíč se používá také k dešifrování těchto dat, protože jsou připravena k použití v paměti. Klíče musí být uloženy v zabezpečeném umístění s řízením přístupu na základě identity a zásadami auditování. Jedno takové zabezpečené umístění je Azure Key Vault. Pokud útočník získá šifrovaná data, ale ne šifrovací klíče, Útočník nemůže získat přístup k datům, aniž by bylo nutné zarušit šifrování.</td>
    <td class="tg-lboi"; width=55%>- Na virtuálních počítačích by se mělo použít šifrování disku<br>- transparentní šifrování dat na SQL Database by měla být povolená.<br>- Proměnné účtu Automation by se měly šifrovat.<br>- Clustery Service Fabric musí mít vlastnost ClusterProtectionLevel nastavenou na EncryptAndSign.<br>- Ochrana TDE systému SQL Server by měla být zašifrovaná pomocí vlastního klíče</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Šifrovat data při přenosu (maximální skóre 4)</p></strong>Data jsou přenášena při přenosu mezi součástmi, umístěními nebo programy. Organizace, které nechrání data při přenosu, jsou náchylné k útokům prostředníkem, odposlouchávání a zneužití relace. Protokoly SSL/TLS by se měly používat k výměně dat a doporučuje se síť VPN. Při odesílání šifrovaných dat mezi virtuálním počítačem Azure a místním umístěním přes Internet můžete k posílání šifrovaného provozu použít bránu virtuální sítě, jako je například <a href="/azure/vpn-gateway/vpn-gateway-about-vpngateways">Azure VPN Gateway</a> .</td>
    <td class="tg-lboi"; width=55%>- Aplikace API by měla být přístupná jen přes protokol HTTPS<br>- Function App by měl být přístupný jenom přes HTTPS<br>- Měla by být povolená jenom zabezpečená připojení k vašemu Redis Cache.<br>- Zabezpečený přenos do účtů úložiště by měl být povolený.<br>- Webová aplikace by měla být přístupná jen přes protokol HTTPS<br>- Privátní koncový bod by měl být povolený pro PostgreSQL servery.<br>- Pro databázové servery PostgreSQL by mělo být povoleno připojení SSL vynutilo.<br>- Pro databázové servery MySQL by mělo být povoleno připojení SSL vynutilo.<br>- Protokol TLS by se měl aktualizovat na nejnovější verzi vaší aplikace API.<br>- Protokol TLS by se měl aktualizovat na nejnovější verzi vaší aplikace Function App.<br>- Protokol TLS by se měl aktualizovat na nejnovější verzi vaší webové aplikace.<br>- V aplikaci API by se mělo vyžadovat FTPS.<br>- Ve vaší aplikaci Function app by se měla FTPS vyžadovat.<br>- Ve vaší webové aplikaci by se mělo vyžadovat FTPS.</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Správa přístupu a oprávnění (maximální skóre 4)</p></strong>Základní součástí programu zabezpečení je zajistit, aby vaši uživatelé měli potřebný přístup k tomu, aby mohli provádět své úlohy, ale ne víc než to: <a href="/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models">model minimálního přístupu k oprávněním</a>.<br>Pomocí <a href="/azure/role-based-access-control/overview">řízení přístupu na základě role Azure (Azure RBAC)</a>můžete řídit přístup k prostředkům tím, že vytvoříte přiřazení rolí. Přiřazení role se skládá ze tří prvků:<br>Objekt - <strong>zabezpečení</strong>: objekt, ke kterému uživatel žádá o přístup<br>- <strong>Definice role</strong>: jejich oprávnění<br>- <strong>Scope (obor</strong>): sada prostředků, na které se oprávnění vztahují</td>
    <td class="tg-lboi"; width=55%>- Zastaralé účty by se měly odebírat z předplatného (Preview)<br>- Zastaralé účty s oprávněním vlastníka by se měly odebrat z vašeho předplatného (Preview).<br>- Externí účty s oprávněním vlastníka by se měly odebrat z vašeho předplatného (Preview).<br>- Z vašeho předplatného byste měli odebrat externí účty s oprávněním k zápisu (Preview).<br>- K vašemu předplatnému by měl být přiřazený víc než jeden vlastník.<br>- Řízení přístupu na základě role v Azure (Azure RBAC) by se mělo používat pro služby Kubernetes (Preview).<br>- Clustery Service Fabric by se měly používat jenom Azure Active Directory pro ověřování klientů.<br>- K ochraně předplatných namísto certifikátů pro správu by se měly použít instanční objekty.<br>- Pro kontejnery (Preview) by se měly vyhovět aspoň privilegované možnosti systému Linux.<br>- Neměnné (jen pro čtení) pro kontejnery (Preview) by se mělo vyhovět kořenovým systémem souborů.<br>- Je potřeba zabránit kontejneru s eskalací oprávnění (Preview).<br>- Spouštění kontejnerů jako kořenový uživatel by se mělo vyhnout (Preview)<br>- Kontejnery sdílející obory názvů závislé na hostitelích by se měly vyhnout (Preview)<br>- Použití ovládacího připojení svazku pod HostPath by se mělo omezit na známý seznam (Preview).<br>- Měly by se vyhnout privilegovanému kontejneru (Preview).<br>- V clusterech se musí nainstalovat a povolit doplněk Azure Policy Kubernetes (Preview).<br>- Ve vaší aplikaci API by se měla používat spravovaná identita.<br>- Ve vaší aplikaci Function app by se měla použít spravovaná identita.<br>- Ve vaší webové aplikaci by se měla použít spravovaná identita.</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Opravit konfigurace zabezpečení (maximální skóre 4)</p></strong>Nesprávně nakonfigurované prostředky IT mají vyšší riziko útoku. Základní akce posílení zabezpečení jsou často zapomenuté při nasazení prostředků a musí být splněné termíny. Nezabezpečené konfigurace zabezpečení můžou být na jakékoli úrovni infrastruktury: od operačních systémů a síťových zařízení až po cloudové prostředky.<br>Azure Security Center průběžně porovnává konfiguraci vašich prostředků s požadavky v oborových normách, nařízeních a srovnávacích testech. Když jste nakonfigurovali relevantní "balíčky dodržování předpisů" (standardy a směrné plány), které jsou v souladu s vaší organizací, budou mít všechny mezery doporučení zabezpečení, která zahrnují CCEID a vysvětlení potenciálního dopadu na zabezpečení.<br>Běžně používané balíčky jsou <a href="/azure/security/benchmarks/introduction">Azure Security test</a> a <a href="https://www.cisecurity.org/benchmark/azure/">CI Microsoft Azure 1.1.0 Foundation – testování verze</a></td>
    <td class="tg-lboi"; width=55%>- Ohrožení zabezpečení v konfiguraci zabezpečení kontejneru by mělo být opraveno<br>- Ohrožení zabezpečení v konfiguraci zabezpečení na vašich počítačích by mělo být opraveno<br>- V konfiguraci zabezpečení v rámci sady škálování virtuálních počítačů by se měly napravit ohrožení zabezpečení.<br>- Agent monitorování by měl být nainstalovaný na virtuálních počítačích<br>- Agent monitorování by měl být nainstalovaný na vašich počítačích.<br>- Agent Log Analytics musí být nainstalovaný na počítačích se systémem Windows Azure ARC (Preview).<br>- Agent Log Analytics musí být nainstalovaný na počítačích se systémem Linux (Preview).<br>- Agent monitorování by měl být nainstalovaný na Virtual Machine Scale Sets<br>- Problémy se stavem agenta monitorování by se měly vyřešit na vašich počítačích.<br>- Přepsání nebo zákaz kontejneru AppArmor Profile by mělo být omezeno (Preview)<br>- V clusterech se musí nainstalovat a povolit doplněk Azure Policy Kubernetes (Preview).</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Omezit neautorizovaný přístup k síti (maximální skóre 4)</p></strong>Koncové body v rámci organizace poskytují přímé připojení z vaší virtuální sítě k podporovaným službám Azure. Virtuální počítače v podsíti můžou komunikovat se všemi prostředky. Pokud chcete omezit komunikaci s prostředky v rámci jedné podsítě a z nich, vytvořte skupinu zabezpečení sítě a přidružte ji k podsíti. Organizace můžou omezit a chránit před neoprávněným provozem vytvořením příchozích a odchozích pravidel.</td>
    <td class="tg-lboi"; width=55%>- Předávání IP na virtuálním počítači by mělo být zakázané.<br>- Pro služby Kubernetes Services (Preview) by měly být definované rozsahy povolených IP adres.<br>- ZASTARALÉ Přístup k App Services by měl být omezený (Preview)<br>- ZASTARALÉ Pravidla pro webové aplikace v IaaS skupin zabezpečení sítě by se měla posílit.<br>- Virtuální počítače by měly být přidružené ke skupině zabezpečení sítě.<br>- CORS by neměl umožňovat každému prostředku přístup k vaší aplikaci API.<br>- CORS by neměl umožňovat každému prostředku přístup k vašemu Function App<br>- CORS by neměl umožňovat každému prostředku přístup k vaší webové aplikaci<br>- Pro aplikaci API by mělo být vypnuto vzdálené ladění.<br>- Vzdálené ladění by mělo být pro Function App vypnuté.<br>- Vzdálené ladění by mělo být pro webovou aplikaci vypnuté.<br>- Přístup by měl být omezený na povolující skupiny zabezpečení sítě u virtuálních počítačů s přístupem k Internetu.<br>- Doporučení adaptivního posílení zabezpečení sítě by se měla použít na internetových virtuálních počítačích.<br>- V clusterech se musí nainstalovat a povolit doplněk Azure Policy Kubernetes (Preview).<br>- Kontejnery by měly naslouchat jenom povoleným portům (Preview)<br>- Služby by měly naslouchat jenom povoleným portům (Preview).<br>- Používání hostitelských sítí a portů by mělo být omezené (Preview)<br>- Virtuální sítě by měly být chráněné pomocí Azure Firewall (Preview)<br>- Privátní koncový bod by měl být povolený pro MariaDB servery.<br>- Pro servery MySQL by měl být povolen soukromý koncový bod.<br>- Privátní koncový bod by měl být povolený pro PostgreSQL servery.</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Použít adaptivní řízení aplikací (maximální skóre 3)</p></strong>Adaptivní řízení aplikací (AAC) je inteligentní, automatizované a ucelené řešení, které umožňuje řídit, které aplikace se můžou spouštět na počítačích Azure a mimo Azure. Pomáhá také posílit zabezpečení vašich počítačů proti malwaru.<br>Security Center využívá Machine Learning k vytvoření seznamu aplikací se známým zabezpečením pro skupinu počítačů.<br>Tento inovativní přístup k seznamu schválených aplikací nabízí výhody zabezpečení bez složitosti správy.<br>AAC je zvláště relevantní pro účelově sestavené servery, které potřebují spouštět konkrétní sadu aplikací.</td>
    <td class="tg-lboi"; width=55%>- Na virtuálních počítačích by měly být povolené Adaptivní řízení aplikací.<br>- Agent monitorování by měl být nainstalovaný na virtuálních počítačích<br>- Agent monitorování by měl být nainstalovaný na vašich počítačích.<br>- Agent Log Analytics musí být nainstalovaný na počítačích se systémem Windows Azure ARC (Preview).<br>- Agent Log Analytics musí být nainstalovaný na počítačích se systémem Linux (Preview).<br>- Problémy se stavem agenta monitorování by se měly vyřešit na vašich počítačích.</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Použít klasifikaci dat (max. skóre 0)</p></strong>Klasifikace dat vaší organizace podle citlivosti a dopadu na firmu vám umožní určit data a přiřazovat k nim hodnoty a poskytovat strategii a základ pro zásady správného řízení.<br><a href="/azure/information-protection/what-is-information-protection">Azure Information Protection</a> může pomáhat s klasifikací dat. Pomocí zásad šifrování, identity a autorizace chrání data a omezuje přístup k datům. Některé klasifikace, které Microsoft používá, jsou jiné než obchodní, veřejné, obecné, důvěrné a vysoce důvěrné.</td>
    <td class="tg-lboi"; width=55%>- Citlivá data v databázích SQL by měla být klasifikována</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Ochrana aplikací před útoky DDoS (maximální skóre 2)</p></strong>Distribuované útoky s cílem odepření služeb (DDoS) zahltí prostředky a vykreslovat aplikace jsou nepoužitelné. Pomocí <a href="/azure/virtual-network/ddos-protection-overview">Azure DDoS Protection Standard</a> můžete svoji organizaci chránit před třemi hlavními typy útoků DDoS:<br>- <strong>Objemná útoky</strong> zaplaví síť s legitimním provozem. DDoS Protection Standard zmírnit tyto útoky tím, že je automaticky odabsorbují nebo vyčištěním.<br>- <strong>Útoky protokolu</strong> generují cíl nepřístupný, protože využívají slabé stránky v zásobníku protokolu vrstvy 3 a 4. DDoS Protection Standard zmírnit tyto útoky blokováním škodlivého provozu.<br>- <strong>Útoky na vrstvy prostředků (aplikace)</strong> jsou cílové pakety webových aplikací. Chránit před tímto typem pomocí brány firewall webových aplikací a DDoS Protection standardem.</td>
    <td class="tg-lboi"; width=55%>- Měla by být povolená DDoS Protection Standard.<br>- Musí se vymáhat omezení procesoru a paměti kontejneru (Preview).<br>- V clusterech se musí nainstalovat a povolit doplněk Azure Policy Kubernetes (Preview).</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Povolit službu Endpoint Protection (max. skóre 2)</p></strong>Aby bylo zajištěno, že jsou koncové body chráněny před malwarem, senzory chování shromažďují a zpracovávají data z operačních systémů koncových bodů a odesílají tato data do privátního cloudu pro účely analýzy. Analýza zabezpečení využívá pro velké objemy dat, strojové učení a další zdroje k doporučení reakcí na hrozby. Například ATP v <a href="/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection">programu Microsoft Defender</a> používá analýzu hrozeb k identifikaci metod útoku a generování výstrah zabezpečení.<br>Security Center podporuje následující řešení ochrany koncových bodů: Windows Defender, System Center Endpoint Protection, Trend Micro, Symantec v 12.1.1.1100, McAfee v10 za účelem pro Windows, McAfee v10 za účelem pro Linux a Sophos v9 pro Linux. Pokud Security Center zjistí některá z těchto řešení, doporučení k instalaci Endpoint Protection se už nezobrazí.</td>
    <td class="tg-lboi"; width=55%>- V sadách škálování virtuálních počítačů by se měla opravit selhání stavu ochrany koncového bodu Endpoint Protection<br>- Na vašich počítačích by se měly vyřešit problémy stavu aplikace Endpoint Protection.<br>- Řešení ochrany koncových bodů by se mělo nainstalovat na Virtual Machine Scale Sets.<br>- Nainstalovat řešení Endpoint Protection na virtuálních počítačích<br>- Problémy se stavem agenta monitorování by se měly vyřešit na vašich počítačích.<br>- Agent monitorování by měl být nainstalovaný na Virtual Machine Scale Sets<br>- Agent monitorování by měl být nainstalovaný na vašich počítačích.<br>- Agent monitorování by měl být nainstalovaný na virtuálních počítačích<br>- Agent Log Analytics musí být nainstalovaný na počítačích se systémem Windows Azure ARC (Preview).<br>- Agent Log Analytics musí být nainstalovaný na počítačích se systémem Linux (Preview).<br>- Instalace řešení Endpoint Protection na počítačích</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Povolit auditování a protokolování (max. skóre 1)</p></strong>Protokolování dat poskytuje přehledy o minulých problémech a zabraňuje potenciálním možnostem, může zlepšit výkon aplikace a umožňuje automatizovat akce, které by jinak byly ruční.<br>- <strong>Protokoly řízení a správy</strong> poskytují informace o operacích <a href="/azure/azure-resource-manager/management/overview">Azure Resource Manager</a> .<br>- <strong>Protokoly roviny dat</strong> poskytují informace o událostech vyvolaných v rámci využití prostředků Azure.<br>- <strong>Zpracované události</strong> poskytují informace o analyzovaných událostech a výstrahách, které byly zpracovány.</td>
    <td class="tg-lboi"; width=55%>- Auditování na SQL serveru by mělo být povolené.<br>- Měly by být povolené diagnostické protokoly v App Services.<br>- Měly by být povolené diagnostické protokoly v Azure Data Lake Store.<br>- Měly by být povolené diagnostické protokoly v Azure Stream Analytics.<br>- V účtech Batch by měly být povolené diagnostické protokoly.<br>- Měly by být povolené diagnostické protokoly v Data Lake Analytics.<br>- Měly by být povolené diagnostické protokoly v centru událostí.<br>- Měly by být povolené diagnostické protokoly v IoT Hub.<br>- Měly by být povolené diagnostické protokoly v Key Vault.<br>- Měly by být povolené diagnostické protokoly v Logic Apps.<br>- Diagnostické protokoly ve službě Search Service by se měly povolit.<br>- Měly by být povolené diagnostické protokoly v Service Bus.<br>- Měly by být povolené diagnostické protokoly v Virtual Machine Scale Sets.<br>- Pravidla upozornění na metriky by měly být nakonfigurovaná na účtech Batch.<br>- Nastavení auditování SQL by mělo mít Action-Groups nakonfigurovanou pro zachycení kritických aktivit.<br>- SQL servery by měly být nakonfigurované s uchováním dat po dobu delší než 90 dní.</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Povolit rozšířenou ochranu před internetovými útoky (max. skóre 0)</p></strong>Azure Security Center volitelné plány ochrany před internetovými útoky v programu Azure Defender poskytují komplexní ochranu vašeho prostředí. Pokud Security Center zjistí hrozbu v jakékoli oblasti vašeho prostředí, vygeneruje výstrahu. Tyto výstrahy popisují podrobnosti o ovlivněných prostředcích, navrhovaných krocích pro nápravu a v některých případech možnost aktivovat aplikaci logiky v reakci.<br>Každý plán Azure Defender je samostatnou volitelnou nabídkou, kterou můžete povolit pomocí relevantního doporučení v tomto řízení zabezpečení.<br><a href="/azure/security-center/threat-protection">Přečtěte si další informace o ochraně před hrozbami v Security Center</a>.</td>
    <td class="tg-lboi"; width=55%>- V Azure SQL Databasech serverech by mělo být povolené rozšířené zabezpečení dat.<br>- Rozšířené zabezpečení dat by mělo být povoleno na serverech SQL na počítačích<br>- V Virtual Machines by měla být povolena rozšířená ochrana před internetovými útoky.<br>- V plánech Azure App Service by měla být povolena rozšířená ochrana před internetovými útoky.<br>- Pro účty Azure Storage by měla být povolena rozšířená ochrana před internetovými útoky.<br>- V clusterech služby Azure Kubernetes by měla být povolena rozšířená ochrana před internetovými útoky.<br>- V Azure Container Registry Registry by měla být povolena rozšířená ochrana před internetovými útoky.<br>- V trezorech Azure Key Vault by měla být povolena rozšířená ochrana před internetovými útoky.</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Implementovat osvědčené postupy zabezpečení (maximální skóre 0)</p></strong>Mezi moderní postupy zabezpečení se předpokládá porušení sítě v hraniční síti. Z tohoto důvodu se řada osvědčených postupů v tomto ovládacím prvku zaměřuje na správu identit.<br>Ztráta klíčů a přihlašovacích údajů je běžný problém. <a href="/azure/key-vault/key-vault-overview">Azure Key Vault</a> chránit klíče a tajné kódy šifrováním klíčů, souborů PFX a hesel.<br>Virtuální privátní sítě (VPN) představují zabezpečený způsob přístupu k virtuálním počítačům. Pokud sítě VPN nejsou k dispozici, používejte složitá přístupová hesla a dvojúrovňové ověřování, jako je například <a href="/azure/active-directory/authentication/concept-mfa-howitworks">Azure AD Multi-Factor Authentication</a>. Dvojúrovňové ověřování zabraňuje slabým místům, které jsou podstatné při spoléhání jenom na uživatelská jména a hesla.<br>Použití silného ověřování a autorizačních platforem je další osvědčené postupy. Použití federovaných identit umožňuje organizacím delegovat správu autorizovaných identit. To je důležité i v případě, že se zaměstnanci ukončí a jejich přístup je potřeba odvolat.</td>
    <td class="tg-lboi"; width=55%>- Pro vaše předplatné by se mělo určit maximálně 3 vlastníci.<br>- Z vašeho předplatného by se měly odebrat externí účty s oprávněním ke čtení.<br>- Pro účty s oprávněním ke čtení vašeho předplatného by se měla povolit vícefaktorové ověřování.<br>- Přístup k účtům úložiště pomocí brány firewall a konfigurací virtuální sítě by měl být omezený.<br>- Všechna autorizační pravidla s výjimkou RootManageSharedAccessKey by měla být odebrána z oboru názvů centra událostí.<br>- Pro SQL servery by se měl zřídit správce Azure Active Directory.<br>- Na spravovaných instancích by mělo být povolené rozšířené zabezpečení dat<br>- Autorizační pravidla v instanci centra událostí by měla být definovaná.<br>- Účty úložiště by se měly migrovat na nové prostředky Azure Resource Manager.<br>- Virtuální počítače by se měly migrovat na nové prostředky Azure Resource Manager.<br>- Podsítě by měly být přidružené ke skupině zabezpečení sítě.<br>- Tisk Měla by být povolená ochrana před zneužitím systému Windows <br>- Tisk Měl by být nainstalovaný agent konfigurace hosta.<br>- Virtuální počítače, které nejsou přístupné z Internetu, by měly být chráněné pomocí skupin zabezpečení sítě<br>- Azure Backup by měly být povolené pro virtuální počítače<br>- Pro Azure Database for MariaDB by měla být povolená geograficky redundantní záloha.<br>- Pro Azure Database for MySQL by měla být povolená geograficky redundantní záloha.<br>- Pro Azure Database for PostgreSQL by měla být povolená geograficky redundantní záloha.<br>- PHP by se mělo aktualizovat na nejnovější verzi vaší aplikace API<br>- PHP by se měla aktualizovat na nejnovější verzi vaší webové aplikace.<br>- Java by se měla aktualizovat na nejnovější verzi vaší aplikace API<br>- Java by se měla aktualizovat na nejnovější verzi vaší aplikace Function App<br>- Java by se měla aktualizovat na nejnovější verzi vaší webové aplikace.<br>- Python by se měl aktualizovat na nejnovější verzi vaší aplikace API<br>- Python by se měl aktualizovat na nejnovější verzi aplikace Function App.<br>- Python by se měl aktualizovat na nejnovější verzi vaší webové aplikace.<br>- Uchovávání auditu pro SQL servery by se mělo nastavit aspoň na 90 dní.<br>- Webové aplikace by měly požádat o certifikát SSL pro všechny příchozí požadavky</td>
  </tr>
</tbody>
</table>


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