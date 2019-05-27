---
title: Prozkoumání výstrahy se službou Azure Sentinelu ve verzi Preview | Dokumentace Microsoftu
description: Pomocí tohoto kurzu se dozvíte, jak zkoumat výstrahy se službou Azure Sentinelu.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: b5fbc5ac-68b2-4024-9c1b-bd3cc41a66d0
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/20/2019
ms.author: rkarlin
ms.openlocfilehash: 6cb40f8c9f1ee85848b5e3db311d0fb652ec1bc3
ms.sourcegitcommit: d73c46af1465c7fd879b5a97ddc45c38ec3f5c0d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2019
ms.locfileid: "65921808"
---
# <a name="tutorial-detect-threats-with-azure-sentinel-preview"></a>Kurz: Detekce hrozeb s Azure Sentinelu ve verzi Preview

> [!IMPORTANT]
> Azure Sentinel je aktuálně ve verzi public preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Poté co [připojené zdroje dat](quickstart-onboard.md) Sentinelu Azure chcete budete upozorněni, když se stane něco podezřelé. Pokud chcete povolit, můžete k tomu, Sentinelu Azure, které vytvoříte upřesňující pravidla výstrah, které generují případů, které můžete přiřadit a použití jak hluboko zjistit anomálie a hrozby ve vašem prostředí. 

Tento kurz pomůže odhalit hrozby s využitím Azure Sentinelu.
> [!div class="checklist"]
> * Vytvoření pravidla zjišťování
> * Reakce na hrozby

## <a name="create-detection-rules"></a>Vytvoření pravidla zjišťování

K prozkoumání případy, musíte nejprve vytvořit pravidla detekce. 

> [!NOTE]
> Výstrahy generované v ověřovacích Azure jsou dostupné prostřednictvím [zabezpečení společnosti Microsoft Graph](https://aka.ms/securitygraphdocs). Odkazovat [výstrahy zabezpečení Microsoft Graph dokumentaci](https://aka.ms/graphsecurityreferencebetadocs) další podrobnosti a partneři pro integraci.

Pravidla zjišťování jsou založeny na typy hrozeb a anomálií, které může být podezřelá ve vašem prostředí, budete chtít vědět o hned, zajištění jsou prezentované, prozkoumat a napravené s cílem zajistit. 

1. Na webu Azure portal v části Azure Sentinelu vyberte **Analytics**.

   ![Analýza](./media/tutorial-detect-threats/alert-rules.png)

2. V horní nabídce klikněte na tlačítko **+ přidat**.  

   ![Vytvořit pravidlo upozornění](./media/tutorial-detect-threats/create-alert-rule.png)

3. V části **vytvořit pravidlo upozornění**, zadejte popisný název a nastavit **závažnost** podle potřeby. 

4. Vytvoření dotazu v Log Analytics a vložte jej do **pravidlo upozornění sady** pole. Tady je ukázkový dotaz, který by vás upozornit, když neobvyklé počet prostředků je vytvořen v aktivita služby Azure.

        AzureActivity
        | where OperationName == "Create or Update Virtual Machine" or OperationName == "Create Deployment"
        | where ActivityStatus == "Succeeded"
        | make-series dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(ago(7d), now(), 1d) by Caller

   > [!NOTE]
   > Délka dotazu by měl mít délku 1 až 10 000 znaků a nesmí obsahovat "search *" a "union *".


5. V **mapování entit** oddílu, použijte pole v rámci **typ Entity** k mapování sloupců v dotazu na pole entity rozpoznávaných Sentinelu Azure. Pro každé pole mapy relevantní sloupce v dotazu, který jste vytvořili v Log Analytics, do pole odpovídající entity. Vyberte název relevantní sloupce pod **vlastnost**. Každá entita obsahuje více polí, například identifikátor SID, GUID, atd. Můžete namapovat entity podle libovolné pole, nikoli pouze horní úrovni entity.

6. Zadejte podmínky aktivace upozornění v části **aktivace upozornění**. Definuje podmínky, které aktivují výstrahy. 

7. Nastavte **frekvence** pro četnost spuštění dotazu - často každých 5 minut nebo zřídka jednou denně. 

8. Nastavte **období** k řízení časový interval pro množství dat, spuštění dotazu – například ji můžete spustit každou hodinu mezi 60 minut dat.

9. Můžete také nastavit **potlačení**. Potlačení je užitečné, když chcete zastavit duplicitních výstrah z aktivaci pro stejného incidentu. Tímto způsobem můžete zastavit výstrahy aktivují během určité doby. To může pomoct vyhnout duplicitních výstrah pro stejného incidentu a umožňují potlačit po sobě jdoucích výstrah pro určitou dobu. Například pokud **upozornění plánování** **frekvence** nastavena na 60 minut a **upozornění plánování období** je nastavena na dvě hodiny, a výsledky dotazu překročení definované Prahová hodnota, aktivuje výstrahu dvakrát, až když se nejprve zjistí za posledních 60 minut, a znovu když se nachází v prvních 60 minut 2 hodin vzorkují data. Doporučujeme, pokud se aktivuje upozornění, potlačení by měl být množství dobu nastavenou v období upozornění. V našem příkladu můžete chtít nastavit potlačení po dobu 60 minut, tak, že jsou výstrahy aktivovány pouze pro události, ke kterým došlo během poslední hodiny.

8. Po vložení dotaz do **pravidlo upozornění sady** pole, okamžitě uvidíte simulaci upozornění v části **logika upozornění simulace** tak, aby bylo možné získat, pochopení, kolik dat bude vygenerované v konkrétním časovém intervalu pro výstrahy, kterou jste vytvořili. To bude záviset na nastavení pro **frekvence** a **prahová hodnota**. Pokud zjistíte, že v průměru, upozornění se aktivuje příliš často, můžete nastavit počet výsledků vyšší tak, aby je vyšší než standardní průměrné hodnoty.

9. Klikněte na tlačítko **vytvořit** inicializovat pravidlo výstrahy. Po vytvoření upozornění je vytvořen případ, který obsahuje upozornění. Zobrazí pravidla detekce definované jako řádky **analýzy zabezpečení** kartu. Zobrazí se také počet shod pro každé pravidlo - upozornění aktivuje. Z tohoto seznamu můžete povolit, zakázat nebo odstranit každé pravidlo. Vám může také stisknutém pravém tlačítku vyberte tři tečky (...) na konci řádku pro každé upozornění upravit, zakázat, naklonovat, zobrazit shody nebo odstranění pravidla. **Analytics** stránka je Galerie všechny aktivní výstrahy pravidla, včetně šablon povolíte a pravidla upozornění můžete vytvořit na základě šablon.

1. Výsledky pravidla upozornění si můžete prohlédnout ve **případů** stránku, kde můžete posoudit, [prozkoumat případů](tutorial-investigate-cases.md), a nápravě těchto hrozeb.



## <a name="respond-to-threats"></a>Reakce na hrozby

Azure Sentinel poskytuje dvě primární možnosti pro reakce na hrozby pomocí playbooků. Můžete nastavit playbook spustit automaticky, když se aktivuje upozornění, nebo můžete ručně spustit playbooku v reakci na výstrahy.

- Nastavte playbook spustit automaticky, když se aktivuje upozornění, když konfigurujete playbooku. 

- Ručně spustit playbook z uvnitř výstrahy, kliknutím na **zobrazit playbooky** a následným výběrem playbook ke spuštění.




## <a name="next-steps"></a>Další postup
V tomto kurzu jste zjistili, jak začít, detekuje hrozby pomocí ověřovacích Azure. 

Další informace o automatizaci vaše odpovědi na ně, [týkající se reakce na hrozby pomocí automatizovaných playbooky](tutorial-respond-threats-playbook.md).
> [!div class="nextstepaction"]
> [Reakce na hrozby](tutorial-respond-threats-playbook.md) k automatizaci vaše odpovědi na ně.

