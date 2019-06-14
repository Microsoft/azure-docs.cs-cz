---
title: Upozornění v reálném čase v Azure CDN | Dokumentace Microsoftu
description: Výstrahy v reálném čase v Microsoft Azure CDN. Výstrahy v reálném čase poskytují oznámení o výkonu z koncových bodů ve vašem profilu CDN.
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
ms.openlocfilehash: e20161147aa16456e31aff2bd3cc6337c3690e89
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60335681"
---
# <a name="real-time-alerts-in-microsoft-azure-cdn"></a>Výstrahy v reálném čase v Microsoft Azure CDN
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Přehled
Tento dokument popisuje výstrahy v reálném čase v Microsoft Azure CDN. Tato funkce poskytuje v reálném čase oznámení o výkonu z koncových bodů ve vašem profilu CDN.  Můžete nastavit e-mailu nebo protokolu HTTP upozornění na základě:

* Šířka pásma
* Stavové kódy
* Stavy mezipaměti
* Připojení

## <a name="creating-a-real-time-alert"></a>Vytváří se v reálném čase upozornění
1. V [webu Azure portal](https://portal.azure.com), přejděte na svůj profil CDN.
   
    ![Profil CDN](./media/cdn-real-time-alerts/cdn-profile-blade.png)
1. Z okna profil CDN, klikněte na tlačítko **spravovat** tlačítko.
   
    ![Tlačítko Spravovat profil CDN](./media/cdn-real-time-alerts/cdn-manage-btn.png)
   
    Otevře se na portálu pro správu CDN.
3. Najeďte myší **Analytics** kartu a pak najeďte myší **statistiky v reálném čase** Kontextová nabídka.  Klikněte na **výstrahy v reálném čase**.
   
    ![Portál pro správu CDN](./media/cdn-real-time-alerts/cdn-premium-portal.png)
   
    Zobrazí se seznam existujících upozornění konfigurace (pokud existuje).
4. Klikněte na tlačítko **přidat upozornění** tlačítko.
   
    ![Přidání upozornění tlačítka](./media/cdn-real-time-alerts/cdn-add-alert.png)
   
    Zobrazí se formulář pro vytvoření nové upozornění.
   
    ![Formulář nové výstrahy](./media/cdn-real-time-alerts/cdn-new-alert.png)
5. Pokud chcete toto upozornění jako aktivní po kliknutí na **Uložit**, zkontrolujte **upozornění povolené** zaškrtávací políčko.
6. Zadejte popisný název pro vaše upozornění **název** pole.
7. V **typ média** rozevíracího seznamu vyberte **velkého objektu HTTP**.
   
    ![Typ média protokolu HTTP velký objekt vybrán](./media/cdn-real-time-alerts/cdn-http-large.png)
   
   > [!IMPORTANT]
   > Musíte vybrat **velkého objektu HTTP** jako **typ média**.  Jiné možnosti nejsou používány nástrojem **Azure CDN od Verizonu**.  Nepodařilo se vybrat **velkého objektu HTTP** způsobí, že nikdy aktivovat vaše upozornění.
   > 
   > 
8. Vytvoření **výraz** monitorovat tak, že vyberete **metrika**, **– operátor**, a **aktivovat hodnotu**.
   
   * Pro **metrika**, vyberte typ podmínky, které chcete monitorované.  **MB/s šířky pásma** je objem využití šířky pásma v megabitech za sekundu.  **Celkový počet připojení** je počet souběžných připojení HTTP k naší krajních serverů.  Definice různé stavy mezipaměti a stavové kódy, naleznete v tématu [Azure CDN mezipaměti stavové kódy](/previous-versions/azure/mt759237(v=azure.100)) a [stavové kódy HTTP Azure CDN](/previous-versions/azure/mt759238(v=azure.100))
   * **Operátor** je matematické operátor, který vytváří vztah mezi metriku a hodnota triggeru.
   * **Aktivovat hodnotu** je prahová hodnota, která musí být splněny, než jsou oznámení odesílána.
     
     V následujícím příkladu vytvořený výraz označuje, že oznámení se odešle, když počet stavový kód 404 je větší než 25.
     
     ![Výraz v reálném čase ukázka výstrah](./media/cdn-real-time-alerts/cdn-expression.png)
9. Pro **Interval**, zadejte, jak často chcete vyhodnotit výraz.
10. V **oznamovat** rozevíracího seznamu vyberte, pokud chcete být upozorněni, kdy je výraz pravdivý.
    
    * **Podmínky zahájení** označuje, že jsou oznámení odesílána při prvním zjištění zadané podmínky.
    * **Podmínka ukončení** označuje, že jsou oznámení odesílána už zjištěném zadanou podmínku. Toto oznámení lze aktivovat pouze po naši síť monitorování systému zjistil, že došlo k zadanou podmínku.
    * **Průběžné** označuje, že jsou oznámení odesílána pokaždé, když, monitorování systému sítě rozpoznává zadanou podmínku. Mějte na paměti, že monitorování systému sítě zkontroluje pouze jednou za interval pro zadanou podmínku.
    * **Podmínky zahájení a ukončení** označuje, že zadaná podmínka je a znovu když podmínka je už je odesláno upozornění poprvé.
1. Pokud chcete dostávat oznámení e-mailem, zkontrolujte, **oznámení e-mailem** zaškrtávací políčko.  
    
    ![Upozornění ve formě e-mailu](./media/cdn-real-time-alerts/cdn-notify-email.png)
    
    V **k** zadejte e-mailovou adresu, ve kterém chcete oznámení odeslání. Pro **subjektu** a **tělo**, můžete ponechat výchozí nastavení, nebo můžete upravit zprávu pomocí **klíčová slova k dispozici** seznam dynamicky vložit data výstrah při zprávy posílá.
    
    > [!NOTE]
    > E-mailové oznámení můžete otestovat kliknutím **zkušební oznámení** tlačítko, ale pouze po uložení konfigurace výstrah.
    > 
    > 
12. Pokud chcete oznámení, který se má publikovat na webový server, zkontrolujte, **oznámení pomocí HTTP Post** zaškrtávací políčko.
    
    ![Upozornění podle HTTP Post formuláře](./media/cdn-real-time-alerts/cdn-notify-http.png)
    
    V **Url** pole, zadejte adresu URL, kam chcete zprávu HTTP účtování. V **záhlaví** textového pole zadejte hlavičky protokolu HTTP k odeslání v požadavku.  Pro **tělo**, může přizpůsobit zprávu o pomocí **klíčová slova k dispozici** seznam dynamicky vložit data výstrahy, pokud je zpráva odeslána.  **Záhlaví** a **tělo** výchozí datovou část XML podobně jako v následujícím příkladu:
    
    ```
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/">
        <![CDATA[Expression=Status Code : 404 per second > 25&Metric=Status Code : 404 per second&CurrentValue=[CurrentValue]&NotificationCondition=Condition Start]]>
    </string>
    ```
    
    > [!NOTE]
    > Kliknutím můžete otestovat oznámení HTTP Post **zkušební oznámení** tlačítko, ale pouze po uložení konfigurace výstrah.
    > 
    > 
13. Klikněte na tlačítko **Uložit** tlačítko Uložit konfiguraci výstrah.  Pokud jste zaškrtli možnost **upozornění povolené** v kroku 5, vaše upozornění je teď aktivní.

## <a name="next-steps"></a>Další kroky
* Analýza [statistiky v reálném čase v Azure CDN](cdn-real-time-stats.md)
* Hlubší vhled díky [Rozšířené sestavy HTTP](cdn-advanced-http-reports.md)
* Analýza [vzorů využití](cdn-analyze-usage-patterns.md)

