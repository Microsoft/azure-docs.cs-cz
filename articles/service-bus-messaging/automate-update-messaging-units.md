---
title: Azure Service Bus – automaticky aktualizovat jednotky zasílání zpráv
description: V tomto článku se dozvíte, jak můžete používat automatické aktualizace jednotek zasílání zpráv Service Bus oboru názvů.
ms.topic: how-to
ms.date: 03/03/2021
ms.openlocfilehash: 7fc3aca82b8f01d70dec4fc2dac7842895417ec9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102177951"
---
# <a name="automatically-update-messaging-units-of-an-azure-service-bus-namespace"></a>Automaticky aktualizovat jednotky zasílání zpráv Azure Service Bus oboru názvů 
Automatické škálování umožňuje mít ke zpracování zatížení vaší aplikace správné množství prostředků, na kterých běží. Umožňuje přidat prostředky pro zvýšení zátěže a také ušetřit peníze odebráním nečinných prostředků. Další informace o funkci automatického škálování Azure Monitor najdete [v tématu Přehled automatického škálování v Microsoft Azure](../azure-monitor/autoscale/autoscale-overview.md) . 

Zasílání zpráv Service Bus Premium zajišťuje izolaci prostředků na úrovni CPU a paměti, takže každá úloha zákazníka běží izolovaně. Kontejner prostředků se nazývá **jednotka zasílání zpráv**. Další informace o jednotkách zasílání zpráv najdete v tématu [Service Bus Premium pro zasílání zpráv](service-bus-premium-messaging.md). 

Pomocí funkce automatického škálování pro obory názvů Service Bus Premium můžete zadat minimální a maximální počet [jednotek zasílání zpráv](service-bus-premium-messaging.md) a automaticky přidávat nebo odebírat jednotky zasílání zpráv na základě sady pravidel. 

Můžete například implementovat následující scénáře škálování pro Service Bus obory názvů pomocí funkce automatického škálování. 

- Pokud je využití CPU v oboru názvů nad 75%, zvyšte počet jednotek zasílání zpráv pro Service Bus obor názvů. 
- Sníží počet jednotek zasílání zpráv pro Service Bus obor názvů, pokud je využití procesoru oboru názvů menší než 25%. 
- V pracovní době použijte více jednotek pro zasílání zpráv a méně v době mimo špičku. 

V tomto článku se dozvíte, jak můžete automaticky škálovat obor názvů Service Bus (aktualizovat [jednotky zasílání zpráv](service-bus-premium-messaging.md)) v Azure Portal. 

> [!IMPORTANT]
> Tento článek se týká jenom úrovně **premium** Azure Service Bus. 

## <a name="autoscale-setting-page"></a>Stránka nastavení automatického škálování
Nejprve pomocí následujících kroků přejděte na stránku **Nastavení automatického škálování** pro obor názvů Service Bus.

1. Přihlaste se [Azure Portal](https://portal.azure.com). 
2. Na panelu hledání zadejte **Service Bus**, v rozevíracím seznamu vyberte **Service Bus** a stiskněte klávesu **ENTER**. 
1. Ze seznamu oborů názvů vyberte svůj **obor názvů Premium** . 
1. Přepněte na stránku **škálování** . 

    :::image type="content" source="./media/automate-update-messaging-units/scale-page.png" alt-text="Service Bus – škálování na stránku oboru názvů":::

## <a name="manual-scale"></a>Ruční škálování 
Toto nastavení umožňuje nastavit pevný počet jednotek zasílání zpráv pro obor názvů. 

1. Na stránce **Nastavení automatického škálování** vyberte **Ruční škálování** , pokud ještě není vybrané. 
1. V nastavení **jednotky zasílání zpráv** vyberte počet jednotek zasílání zpráv z rozevíracího seznamu.
1. Nastavení uložte kliknutím na **Uložit** na panelu nástrojů. 

    :::image type="content" source="./media/automate-update-messaging-units/manual-scale.png" alt-text="Ruční škálování jednotek zasílání zpráv":::       


## <a name="custom-autoscale---default-condition"></a>Vlastní automatické škálování – výchozí podmínka
Automatické škálování jednotek zasílání zpráv můžete nakonfigurovat pomocí podmínek. Tato podmínka škálování se spustí, když žádná z ostatních podmínek škálování neodpovídá. Výchozí podmínku můžete nastavit jedním z následujících způsobů:

- Škálování na základě metriky (například využití procesoru nebo paměti)
- Škálovat na určitý počet jednotek zasílání zpráv

Nemůžete nastavit plán pro automatické škálování na konkrétní dny nebo rozsah kalendářních dat pro výchozí podmínku. Tato podmínka škálování se spustí, když se neshoduje žádná z dalších podmínek škálování s plánem. 

### <a name="scale-based-on-a-metric"></a>Škálování na základě metriky
Následující postup ukazuje, jak přidat podmínku pro automatické zvýšení jednotek zasílání zpráv (horizontální navýšení kapacity), pokud je využití procesoru větší než 75%, a snižuje počet jednotek zasílání zpráv (v případě škálování), pokud je využití procesoru menší než 25%. Přírůstky jsou provedeny od 1 do 2, 2 až 4, 4 až 8 a 8 až 16. Podobně jsou snížení hodnoty provedeny od 16 do 8, 8 až 4, 4 až 2 a 2 až 1. 

1. Na stránce **Nastavení automatického škálování** vyberte **vlastní automatické škálování** pro možnost **zvolit způsob horizontálního** navýšení kapacity prostředku. 
1. V části **výchozí** na stránce zadejte **název** výchozí podmínky. Vyberte ikonu **tužky** a text upravte. 
1. Vyberte **škálování na základě metriky** pro **Režim škálování**. 
1. Vyberte **+ Přidat pravidlo**. 

    :::image type="content" source="./media/automate-update-messaging-units/default-scale-metric-add-rule-link.png" alt-text="Výchozí – škálování na základě metriky":::    
1. Na stránce **pravidlo škálování** proveďte tyto kroky:
    1. Vyberte metriku z rozevíracího seznamu **název metriky** . V tomto příkladu je to **procesor**. 
    1. Vyberte operátor a prahové hodnoty. V tomto příkladu jsou **větší než** a **75** , **aby prahová hodnota metriky aktivovala akci škálování**. 
    1. V části **Akce** vyberte **operaci** . V tomto příkladu je nastavené na **zvýšení**. 
    1. Pak vyberte **Přidat** .
    
        :::image type="content" source="./media/automate-update-messaging-units/scale-rule-cpu-75.png" alt-text="Výchozí – horizontální navýšení kapacity, pokud je využití procesoru větší než 75%":::       

        > [!NOTE]
        > Funkce automatického škálování zvyšuje počet jednotek zasílání zpráv pro obor názvů, pokud celkové využití CPU v tomto příkladu překročí 75%. Přírůstky jsou provedeny od 1 do 2, 2 až 4, 4 až 8 a 8 až 16. 
1. Vyberte **+ Přidat pravidlo** znovu a na stránce **pravidlo škálování** proveďte tyto kroky:
    1. Vyberte metriku z rozevíracího seznamu **název metriky** . V tomto příkladu je to **procesor**. 
    1. Vyberte operátor a prahové hodnoty. V tomto příkladu jsou v **prahové hodnotě metriky** **méně než** **25** , což umožňuje aktivovat akci škálování. 
    1. V části **Akce** vyberte **operaci** . V tomto příkladu je nastaveno na hodnotu **snížit**. 
    1. Pak vyberte **Přidat** . 

        :::image type="content" source="./media/automate-update-messaging-units/scale-rule-cpu-25.png" alt-text="Výchozí – škála v případě, že využití procesoru je méně než 25%":::       

        > [!NOTE]
        > Funkce automatického škálování snižuje počet jednotek zasílání zpráv pro obor názvů, pokud celkové využití CPU v tomto příkladu dosahuje 25%. Snížení se provádí od 16 do 8, 8 až 4, 4 až 2 a 2 až 1. 
1. Nastavte **minimální** a **maximální** a **výchozí** počet jednotek zasílání zpráv.

    :::image type="content" source="./media/automate-update-messaging-units/default-scale-metric-based.png" alt-text="Výchozí pravidlo založené na metrikě":::
1. Na panelu nástrojů vyberte **Uložit** a uložte nastavení automatického škálování. 
        
### <a name="scale-to-specific-number-of-messaging-units"></a>Škálovat na určitý počet jednotek zasílání zpráv
Pomocí těchto kroků můžete nakonfigurovat pravidlo pro škálování oboru názvů tak, aby používalo určitý počet jednotek zasílání zpráv. Znovu se použije výchozí podmínka, když žádná z ostatních podmínek škálování neodpovídá. 

1. Na stránce **Nastavení automatického škálování** vyberte **vlastní automatické škálování** pro možnost **zvolit způsob horizontálního** navýšení kapacity prostředku. 
1. V části **výchozí** na stránce zadejte **název** výchozí podmínky. 
1. Vyberte možnost **škálovat na konkrétní jednotky zasílání zpráv** pro **Režim škálování**. 
1. V části **jednotky zasílání zpráv** vyberte počet výchozích jednotek zasílání zpráv. 

    :::image type="content" source="./media/automate-update-messaging-units/default-scale-messaging-units.png" alt-text="Výchozí – škálování na konkrétní jednotky zasílání zpráv":::       

## <a name="custom-autoscale---additional-conditions"></a>Vlastní automatické škálování – další podmínky
V předchozí části se dozvíte, jak přidat výchozí podmínku pro nastavení automatického škálování. V této části se dozvíte, jak přidat další podmínky do nastavení automatického škálování. Pro tyto další nevýchozí podmínky můžete nastavit plán na základě konkrétních dní v týdnu nebo rozsahu dat. 

### <a name="scale-based-on-a-metric"></a>Škálování na základě metriky
1. Na stránce **Nastavení automatického škálování** vyberte **vlastní automatické škálování** pro možnost **zvolit způsob horizontálního** navýšení kapacity prostředku. 
1. Vyberte **Přidat podmínku škálování** pod **výchozím** blokem. 

    :::image type="content" source="./media/automate-update-messaging-units/add-scale-condition-link.png" alt-text="Vlastní – přidejte odkaz na podmínku škálování.":::    
1. Zadejte **název** podmínky. 
1. Ověřte, že je vybraná možnost **škálování na základě metriky** . 
1. Vyberte **+ Přidat pravidlo** a přidejte pravidlo, které zvýší počet jednotek zasílání zpráv, když celkové využití procesoru překročí 75%. Postupujte podle kroků z oddílu [výchozí podmínka](#custom-autoscale---default-condition) . 
5. Nastavte **minimální** a **maximální** a **výchozí** počet jednotek zasílání zpráv.
6. Můžete také nastavit **plán** pro vlastní podmínku (ale ne pro výchozí podmínku). Můžete buď zadat počáteční a koncové datum podmínky (nebo) vybrat konkrétní dny (pondělí, úterý atd.) v týdnu. 
    1. Pokud vyberete možnost **zadat počáteční/koncové datum**, vyberte **časové pásmo**, **počáteční datum a čas** a **koncové datum a čas** (jak je znázorněno na následujícím obrázku), aby podmínka byla platná. 

       :::image type="content" source="./media/automate-update-messaging-units/custom-min-max-default.png" alt-text="Minimální, maximální a výchozí hodnoty pro počet jednotek zasílání zpráv":::
    1. Pokud vyberete možnost **Opakovat konkrétní dny**, vyberte dny v týdnu, časové pásmo, čas spuštění a čas ukončení, kdy se má podmínka použít. 

        :::image type="content" source="./media/automate-update-messaging-units/repeat-specific-days.png" alt-text="Opakovat konkrétní dny":::
  
### <a name="scale-to-specific-number-of-messaging-units"></a>Škálovat na určitý počet jednotek zasílání zpráv
1. Na stránce **Nastavení automatického škálování** vyberte **vlastní automatické škálování** pro možnost **zvolit způsob horizontálního** navýšení kapacity prostředku. 
1. Vyberte **Přidat podmínku škálování** pod **výchozím** blokem. 

    :::image type="content" source="./media/automate-update-messaging-units/add-scale-condition-link.png" alt-text="Vlastní – přidejte odkaz na podmínku škálování.":::    
1. Zadejte **název** podmínky. 
2. Pro **Režim škálování** vyberte možnost **škálovat ke konkrétním jednotkám zasílání zpráv** . 
1. V rozevíracím seznamu vyberte počet **jednotek zasílání zpráv** . 
6. Pro **plán** zadejte počáteční a koncové datum podmínky (nebo) vyberte konkrétní dny (pondělí, úterý atd.) v týdnu a časech. 
    1. Pokud vyberete možnost **zadat počáteční/koncové datum**, vyberte **časové pásmo**, **počáteční datum a čas** a **koncové datum a čas** , kdy má podmínka platit. 
    
    :::image type="content" source="./media/automate-update-messaging-units/scale-specific-messaging-units-start-end-dates.png" alt-text="škálování na konkrétní jednotky zpráv – počáteční a koncové datum":::        
    1. Pokud vyberete možnost **Opakovat konkrétní dny**, vyberte dny v týdnu, časové pásmo, čas spuštění a čas ukončení, kdy se má podmínka použít.
    
    :::image type="content" source="./media/automate-update-messaging-units/repeat-specific-days-2.png" alt-text="škálovat na konkrétní jednotky zpráv – opakovat konkrétní dny":::

    
    Další informace o tom, jak nastavení automatického škálování funguje, zejména jak si vybírá profil nebo podmínku a vyhodnocuje více pravidel, najdete v tématu [Vysvětlení nastavení automatického škálování](../azure-monitor/autoscale/autoscale-understanding-settings.md).          

    > [!NOTE]
    > - Metriky, které jste provedli k rozhodnutí o automatickém škálování, můžou být 5-10 minut staré. Při práci s úlohami nárazové doporučujeme, abyste měli kratší dobu trvání pro horizontální navýšení nebo snížení kapacity (> 10 minut), abyste zajistili, že budete mít k dispozici dostatečný počet jednotek zasílání zpráv pro zpracování úloh nárazové. 
    > 
    > - Pokud se zobrazí chyby z důvodu nedostatku kapacity (nejsou k dispozici žádné jednotky zasílání zpráv), vyvolejte lístek podpory s námi.  


## <a name="next-steps"></a>Další kroky
Další informace o jednotkách zasílání zpráv najdete v tématu [zasílání zpráv](service-bus-premium-messaging.md) na úrovni Premium.

