---
title: Výstrahy Azure CDN v reálném čase | Dokumenty společnosti Microsoft
description: Výstrahy v reálném čase v microsoft azure cdn. Výstrahy v reálném čase poskytují oznámení o výkonu koncových bodů v profilu CDN.
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
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 4b8cbc27757cf6c321ea4b3c27720a129aa27c1b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67593483"
---
# <a name="real-time-alerts-in-microsoft-azure-cdn"></a>Výstrahy v reálném čase v microsoft azure cdn
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Přehled
Tento dokument vysvětluje výstrahy v reálném čase v microsoft azure cdn. Tato funkce poskytuje oznámení v reálném čase o výkonu koncových bodů v profilu CDN.  E-mailová nebo HTTP upozornění můžete nastavit na základě:

* Šířka pásma
* Stavové kódy
* Stavy mezipaměti
* Připojení

## <a name="creating-a-real-time-alert"></a>Vytvoření výstrahy v reálném čase
1. Na [webu Azure Portal](https://portal.azure.com)přejděte na svůj profil CDN.
   
    ![Profil CDN](./media/cdn-real-time-alerts/cdn-profile-blade.png)
1. V okně profilu CDN klepněte na tlačítko **Spravovat.**
   
    ![Tlačítko pro správu profilu CDN](./media/cdn-real-time-alerts/cdn-manage-btn.png)
   
    Otevře se portál pro správu CDN.
3. Najeďte na kartu **Analytics** a najeďte na informační **rámeček Statistiky v reálném čase.**  Klikněte na **Real-Time Upozornění**.
   
    ![Portál pro správu CDN](./media/cdn-real-time-alerts/cdn-premium-portal.png)
   
    Zobrazí se seznam existujících konfigurací výstrah (pokud existují).
4. Klikněte na tlačítko **Přidat výstrahu.**
   
    ![Tlačítko Přidat výstrahu](./media/cdn-real-time-alerts/cdn-add-alert.png)
   
    Zobrazí se formulář pro vytvoření nové výstrahy.
   
    ![Nový výstražný formulář](./media/cdn-real-time-alerts/cdn-new-alert.png)
5. Pokud chcete, aby tato výstraha byla aktivní po klepnutí na tlačítko **Uložit**, zaškrtněte políčko **Výstraha povolena.**
6. Do pole **Název** zadejte popisný název výstrahy.
7. V rozevíracím souboru **Typ média** vyberte možnost Velký **objekt HTTP**.
   
    ![Typ média s vybraným velkým objektem HTTP](./media/cdn-real-time-alerts/cdn-http-large.png)
   
   > [!IMPORTANT]
   > Jako **typ média**je nutné vybrat velký objekt **PROTOKOLU HTTP** .  Ostatní možnosti nejsou používány **Azure CDN od společnosti Verizon**.  Pokud se nepodaří vybrat **velký objekt PROTOKOLU HTTP,** výstraha se nikdy nespustí.
   > 
   > 
8. Vytvořte **výraz** ke sledování výběrem hodnoty **Metrika**, **Operátor**a **Aktivační událost**.
   
   * V **části Metrika**vyberte typ podmínky, kterou chcete sledovat.  **Mbps šířky pásma** je množství využití šířky pásma v megabitech za sekundu.  **Celkový počet připojení** je počet souběžných připojení HTTP k našim hraničním serverům.  Definice různých stavů mezipaměti a stavových kódů najdete v tématu [Azure CDN Cache Status Codes](/previous-versions/azure/mt759237(v=azure.100)) a Azure [CDN HTTP Status Codes](/previous-versions/azure/mt759238(v=azure.100))
   * **Operátor** je matematický operátor, který vytváří vztah mezi metrikou a aktivační hodnotou.
   * **Aktivační hodnota** je prahová hodnota, která musí být splněna před odesláním oznámení.
     
     V následujícím příkladu vytvořený výraz označuje, že oznámení je odesláno, pokud je počet stavových kódů 404 větší než 25.
     
     ![Ukázkový výraz výstrah y v reálném čase](./media/cdn-real-time-alerts/cdn-expression.png)
9. V **části Interval**zadejte, jak často chcete výraz vyhodnotit.
10. V rozevíracím **souboru Upozornit na** vyberte, kdy chcete být upozorněni, když je výraz pravdivý.
    
    * **Podmínka Start** označuje, že oznámení je odeslána při prvním zjištění zadané podmínky.
    * **Konec podmínky** označuje, že oznámení je odesláno, když zadaná podmínka již není zjištěna. Toto oznámení lze spustit až poté, co náš systém sledování sítě zjistí, že došlo k zadanému stavu.
    * **Průběžné** označuje, že je odesíláno oznámení pokaždé, když systém sledování sítě zjistí zadanou podmínku. Mějte na paměti, že systém sledování sítě kontroluje zadanou podmínku pouze jednou za interval.
    * **Podmínka Začátek a konec** označuje, že oznámení je odeslána při prvním zjištění zadané podmínky a znovu, když podmínka již není zjištěna.
1. Pokud chcete dostávat oznámení e-mailem, zaškrtněte políčko **Upozornit e-mailem.**  
    
    ![Upozornit e-mailem](./media/cdn-real-time-alerts/cdn-notify-email.png)
    
    Do pole **Do** zadejte e-mailovou adresu, na kterou chcete odesílat oznámení. V **případě Předmět** a **Tělo**můžete ponechat výchozí nastavení nebo můžete zprávu přizpůsobit pomocí seznamu **Dostupná klíčová slova** pro dynamické vkládání dat výstrah při odeslání zprávy.
    
    > [!NOTE]
    > E-mailové oznámení můžete otestovat kliknutím na tlačítko **Oznámení testu,** ale až po uložení konfigurace výstrahy.
    > 
    > 
12. Pokud chcete, aby byla oznámení zaúčtována na webový server, zaškrtněte políčko **Upozornit podle chyby HTTP.**
    
    ![Upozornit pomocí formuláře HTTP Post](./media/cdn-real-time-alerts/cdn-notify-http.png)
    
    Do pole **Url** zadejte adresu URL, na kterou chcete odeslat zprávu HTTP. Do textového pole **Záhlaví** zadejte hlavičky HTTP, které mají být odeslány v požadavku.  V **části Tělo**můžete zprávu přizpůsobit pomocí seznamu **Dostupná klíčová slova** pro dynamické vkládání dat výstrah při odeslání zprávy.  **Záhlaví** a **tělo** výchozí datové části XML podobný následující příklad:
    
    ```
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/">
        <![CDATA[Expression=Status Code : 404 per second > 25&Metric=Status Code : 404 per second&CurrentValue=[CurrentValue]&NotificationCondition=Condition Start]]>
    </string>
    ```
    
    > [!NOTE]
    > Oznámení http příspěvku můžete otestovat kliknutím na tlačítko **Oznámení testu,** ale až po uložení konfigurace výstrahy.
    > 
    > 
13. Kliknutím na tlačítko **Uložit** uložte konfiguraci výstrah.  Pokud jste v kroku 5 zaškrtli **povolenou výstrahu,** je výstraha nyní aktivní.

## <a name="next-steps"></a>Další kroky
* Analýza [statistik v reálném čase v Azure CDN](cdn-real-time-stats.md)
* Ponořte se hlouběji pomocí [pokročilých zpráv HTTP](cdn-advanced-http-reports.md)
* Analýza [vzorců využití](cdn-analyze-usage-patterns.md)

