---
title: Azure CDN v reálném čase výstrahy | Microsoft Docs
description: V reálném čase výstrah v Microsoft Azure CDN. Výstrahy v reálném čase poskytují oznámení o výkonu z koncových bodů ve vašem profilu CDN.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: 1e85b809-e1a9-4473-b835-69d1b4ed3393
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 51dce1680be5f5f4387c2ba02827195bcdbe9b48
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23843151"
---
# <a name="real-time-alerts-in-microsoft-azure-cdn"></a>V reálném čase výstrah v Microsoft Azure CDN
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Přehled
Tento dokument popisuje, v reálném čase výstrah v Microsoft Azure CDN. Tato funkce poskytuje v reálném čase oznámení o výkonu z koncových bodů ve vašem profilu CDN.  Můžete nastavit e-mailu nebo HTTP upozornění na základě:

* Šířka pásma
* Stavové kódy
* Stavy mezipaměti
* Připojení

## <a name="creating-a-real-time-alert"></a>Vytváření v reálném čase výstrahy
1. V [portál Azure](https://portal.azure.com), přejděte na svůj profil CDN.
   
    ![Profil CDN](./media/cdn-real-time-alerts/cdn-profile-blade.png)
1. Okno profil CDN, klikněte **spravovat** tlačítko.
   
    ![Tlačítko Spravovat profil CDN](./media/cdn-real-time-alerts/cdn-manage-btn.png)
   
    Otevře se na portálu pro správu CDN.
3. Najeďte myší **Analytics** a potom přejděte myší **statistiky v reálném čase** plovoucím panelem.  Klikněte na **v reálném čase výstrahy**.
   
    ![Portál pro správu CDN](./media/cdn-real-time-alerts/cdn-premium-portal.png)
   
    Zobrazí se seznam existující výstrahy konfigurací (pokud existuje).
4. Klikněte **přidat výstrahy** tlačítko.
   
    ![Výstrahy tlačítko Přidat](./media/cdn-real-time-alerts/cdn-add-alert.png)
   
    Formulář pro vytvoření nového upozornění se zobrazí.
   
    ![Formulář nové výstrahy](./media/cdn-real-time-alerts/cdn-new-alert.png)
5. Pokud chcete tuto výstrahu být aktivní, když kliknete na tlačítko **Uložit**, zkontrolujte **upozornění povolené** zaškrtávací políčko.
6. Zadejte popisný název pro vaše upozornění **název** pole.
7. V **typ média** rozevíracího seznamu vyberte **velkého objektu HTTP**.
   
    ![Typ média HTTP velký objekt vybrán](./media/cdn-real-time-alerts/cdn-http-large.png)
   
   > [!IMPORTANT]
   > Je nutné vybrat **velkého objektu HTTP** jako **typ média**.  Jiné možnosti, které nejsou používány nástrojem **Azure CDN společnosti Verizon**.  Selhání a vyberte **velkého objektu HTTP** způsobí, že upozornění až nikdy se spustí.
   > 
   > 
8. Vytvoření **výraz** monitorování tak, že vyberete **metrika**, **operátor**, a **aktivovat hodnotu**.
   
   * Pro **metrika**, vyberte typ podmínky, které chcete monitorovaných.  **MB/s šířky pásma** je množství využití šířky pásma v MB za sekundu.  **Celkový počet připojení** je počet souběžných připojení protokolu HTTP k naše servery edge.  Definice různé stavy, které jsou mezipaměti a stavové kódy, najdete v části [Azure CDN mezipaměti stavové kódy](https://msdn.microsoft.com/library/mt759237.aspx) a [stavové kódy HTTP CDN Azure](https://msdn.microsoft.com/library/mt759238.aspx)
   * **Operátor** je matematický operátor, který vytváří vztah mezi metriky a hodnotu aktivační události.
   * **Aktivovat hodnota** je prahovou hodnotu, která je nutné splnit před jsou oznámení odesílána.
     
     V následujícím příkladu vytvořený výraz označuje, že jsou oznámení odesílána, pokud je počet 404 stavové kódy větší než 25.
     
     ![V reálném čase výstrahy ukázka výrazu](./media/cdn-real-time-alerts/cdn-expression.png)
9. Pro **Interval**, zadejte, jak často chcete výrazu vyhodnoceného.
10. V **upozornit na** rozevíracího seznamu vyberte, pokud chcete být upozorněni, když výraz hodnotu true.
    
    * **Podmínka spuštění** označuje, že jsou oznámení odesílána při prvním zjištění zadanou podmínku.
    * **Podmínka End** označuje, že je odesláno oznámení, když už je zjištěna zadanou podmínku. Toto oznámení můžete spustit pouze po naše síť systému pro monitorování zjistil, že došlo k chybě zadanou podmínku.
    * **Průběžné** označuje, že je odesláno upozornění pokaždé, když zjistí, že monitorování systému sítě zadanou podmínku. Mějte na paměti, která monitorování systému sítě kontroluje jenom jednou za interval pro zadanou podmínku.
    * **Podmínka počáteční a koncové** označuje, že zadanou podmínku je a znovu při podmínka je už je odesláno upozornění poprvé.
1. Pokud chcete dostávat oznámení e-mailem, podívejte se **oznámení e-mailem** zaškrtávací políčko.  
    
    ![Upozornit e-mailu formuláři](./media/cdn-real-time-alerts/cdn-notify-email.png)
    
    V **k** pole, zadejte e-mailovou adresu, můžete místo, kam chcete oznámení odesílat. Pro **subjektu** a **textu**, můžete ponechat výchozí nastavení, nebo může přizpůsobit zprávu pomocí **k dispozici klíčová slova** seznamu dynamicky vložení dat výstrah při odeslání zprávy.
    
    > [!NOTE]
    > E-mailové oznámení můžete otestovat klepnutím **testovací oznámení** tlačítko, ale pouze po uložení konfigurace výstrahy.
    > 
    > 
12. Pokud chcete oznámení odeslat do webového serveru, podívejte se **oznámení pomocí HTTP Post** zaškrtávací políčko.
    
    ![Upozornit pomocí HTTP Post formuláře](./media/cdn-real-time-alerts/cdn-notify-http.png)
    
    V **Url** pole, zadejte adresu URL pro odeslání je místo, kam chcete zprávy HTTP. V **hlavičky** textovému poli, zadejte hlavičky protokolu HTTP k odeslání v požadavku.  Pro **textu**, zpráva může přizpůsobit pomocí **k dispozici klíčová slova** seznamu dynamicky vložení dat výstrah při odeslání zprávy.  **Hlavičky** a **textu** výchozí nastavení datovou část XML podobně jako v následujícím příkladu:
    
    ```
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/">
        <![CDATA[Expression=Status Code : 404 per second > 25&Metric=Status Code : 404 per second&CurrentValue=[CurrentValue]&NotificationCondition=Condition Start]]>
    </string>
    ```
    
    > [!NOTE]
    > HTTP Post oznámení můžete otestovat klepnutím **testovací oznámení** tlačítko, ale pouze po uložení konfigurace výstrahy.
    > 
    > 
13. Klikněte **Uložit** tlačítko Uložit konfiguraci výstrah.  Pokud je zaškrtnuto **upozornění povolené** v kroku 5, upozornění je teď aktivní.

## <a name="next-steps"></a>Další kroky
* Analýza [statistiky v reálném čase v Azure CDN](cdn-real-time-stats.md)
* Dig hlubší s [Rozšířené sestavy HTTP](cdn-advanced-http-reports.md)
* Analýza [vzorce používání](cdn-analyze-usage-patterns.md)

