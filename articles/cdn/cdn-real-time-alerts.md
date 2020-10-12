---
title: Azure CDN upozornění v reálném čase | Microsoft Docs
description: Výstrahy v reálném čase v Microsoft Azure CDN. Výstrahy v reálném čase poskytují oznámení o výkonu koncových bodů v profilu CDN.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: 1e85b809-e1a9-4473-b835-69d1b4ed3393
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 6811a06eb3483fd53b6e566033935c3b2e00ceca
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84887248"
---
# <a name="real-time-alerts-in-microsoft-azure-cdn"></a>Výstrahy v reálném čase v Microsoft Azure CDN
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Přehled
Tento dokument vysvětluje výstrahy v reálném čase v Microsoft Azure CDN. Tato funkce poskytuje oznámení v reálném čase o výkonu koncových bodů v profilu CDN.  Můžete nastavit výstrahy e-mailu nebo HTTP na základě:

* Šířka pásma
* Stavové kódy
* Stavy mezipaměti
* Připojení

## <a name="creating-a-real-time-alert"></a>Vytvoření výstrahy v reálném čase
1. V [Azure Portal](https://portal.azure.com)přejděte do svého profilu CDN.
   
    ![Profil CDN](./media/cdn-real-time-alerts/cdn-profile-blade.png)
1. V okně profilu CDN klikněte na tlačítko **Spravovat** .
   
    ![Tlačítko Spravovat profil CDN](./media/cdn-real-time-alerts/cdn-manage-btn.png)
   
    Otevře se portál pro správu CDN.
3. Najeďte myší na kartu **Analýza** a potom najeďte myší na informační rámeček **statistiky v reálném čase** .  Klikněte na **výstrahy v reálném čase**.
   
    ![Portál pro správu CDN](./media/cdn-real-time-alerts/cdn-premium-portal.png)
   
    Zobrazí se seznam existujících konfigurací výstrah (pokud existují).
4. Klikněte na tlačítko **Přidat výstrahu** .
   
    ![Tlačítko pro přidání výstrahy](./media/cdn-real-time-alerts/cdn-add-alert.png)
   
    Zobrazí se formulář pro vytvoření nové výstrahy.
   
    ![Formulář nové výstrahy](./media/cdn-real-time-alerts/cdn-new-alert.png)
5. Pokud chcete, aby byla tato výstraha aktivní, když kliknete na **Uložit**, zaškrtněte políčko **Povolit výstrahu** .
6. Do pole **název** zadejte popisný název upozornění.
7. V rozevíracím seznamu **typ média** vyberte **http large object**.
   
    ![Typ média s vybraným Large Object HTTP](./media/cdn-real-time-alerts/cdn-http-large.png)
   
   > [!IMPORTANT]
   > Jako **typ média**musíte vybrat **large object http** .  Ostatní možnosti nejsou používány **Azure CDN z Verizon**.  Nepodařilo se vybrat **large object http** způsobí, že se výstraha nikdy neaktivuje.
   > 
   > 
8. Vytvořte **výraz** , který se má monitorovat, a to výběrem **metriky**, **operátoru**a **hodnoty triggeru**.
   
   * V poli **metrika**vyberte typ podmínky, kterou chcete monitorovat.  **Šířka pásma MB/s** je velikost využití šířky pásma v megabajtech za sekundu.  **Čítač připojení (celkem** ) představuje počet souběžných připojení HTTP k našim hraničním serverům.  Definice různých stavů a stavů mezipaměti najdete v tématech [stavové kódy Azure CDN cache](/previous-versions/azure/mt759237(v=azure.100)) a [Azure CDN STAVové kódy http](/previous-versions/azure/mt759238(v=azure.100)) .
   * **Operátor** je matematický operátor, který vytváří vztah mezi metrikou a hodnotou triggeru.
   * **Hodnota triggeru** je prahová hodnota, která musí být splněna před odesláním oznámení.
     
     V následujícím příkladu vytvářený výraz označuje, že je odesláno oznámení, když je počet stavových kódů 404 větší než 25.
     
     ![Vzorový výraz výstrahy v reálném čase](./media/cdn-real-time-alerts/cdn-expression.png)
9. Jako **interval**zadejte, jak často se má výraz vyhodnotit.
10. V rozevíracím seznamu **oznámit pro** vyberte, kdy se má zobrazit oznámení, když je výraz pravdivý.
    
    * **Podmínka Start** označuje, že oznámení je odesláno při prvním zjištění zadané podmínky.
    * **Konec podmínky** označuje, že se pošle oznámení, když se zadaná podmínka už nedetekuje. Toto oznámení se může aktivovat, jenom když systém monitorování sítě zjistil, že se vyskytla zadaná podmínka.
    * **Průběžné** znamená, že se pošle oznámení, když systém monitorování sítě detekuje zadanou podmínku. Mějte na paměti, že systém monitorování sítě pro zadanou podmínku kontroluje jenom jednou za interval.
    * **Začátek podmínky a konec** označuje, že se pošle oznámení při prvním zjištění zadané podmínky a potom znovu, kdy se podmínka už nedetekuje.
1. Pokud chcete oznámení dostávat e-mailem, zaškrtněte políčko **Upozornění e-mailem** .  
    
    ![E-mailová forma s informacemi](./media/cdn-real-time-alerts/cdn-notify-email.png)
    
    Do pole **do** zadejte e-mailovou adresu, na kterou chcete odesílat oznámení. U **předmětu** a **textu**můžete ponechat výchozí nastavení nebo můžete upravit zprávu pomocí seznamu **dostupná klíčová slova** k dynamickému vkládání dat výstrah při odeslání zprávy.
    
    > [!NOTE]
    > E-mailové oznámení můžete otestovat kliknutím na tlačítko **testovat oznámení** , ale až po uložení konfigurace výstrahy.
    > 
    > 
12. Chcete-li odesílat oznámení na webový server, zaškrtněte políčko **oznámení pomocí HTTP POST** .
    
    ![Odeslat zprávu odesláním formuláře HTTP POST](./media/cdn-real-time-alerts/cdn-notify-http.png)
    
    Do pole **Adresa URL** zadejte adresu URL, na kterou chcete zprávu HTTP odeslat. Do textového pole **Headers (hlavičky** ) zadejte hlavičky protokolu HTTP, které se mají odeslat v žádosti.  V případě **těla**můžete zprávu přizpůsobit pomocí seznamu **dostupná klíčová slova** k dynamickému vkládání dat výstrah při odeslání zprávy.  **Záhlaví** a **tělo** jako výchozí datovou část XML, podobně jako v následujícím příkladu:
    
    ```
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/">
        <![CDATA[Expression=Status Code : 404 per second > 25&Metric=Status Code : 404 per second&CurrentValue=[CurrentValue]&NotificationCondition=Condition Start]]>
    </string>
    ```
    
    > [!NOTE]
    > Oznámení HTTP POST můžete otestovat kliknutím na tlačítko **testovat oznámení** , ale až po uložení konfigurace výstrahy.
    > 
    > 
13. Kliknutím na tlačítko **Uložit** uložte konfiguraci upozornění.  Pokud jste v kroku 5 zaškrtli políčko **Výstraha povolená** , vaše výstraha je teď aktivní.

## <a name="next-steps"></a>Další kroky
* Analýza [statistik v reálném čase v Azure CDN](cdn-real-time-stats.md)
* Dig hlouběji pomocí [pokročilých sestav http](cdn-advanced-http-reports.md)
* Analýza [vzorů využití](cdn-analyze-usage-patterns.md)

