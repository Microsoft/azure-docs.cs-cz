---
title: Začínáme s automatickým škálováním v Azure
description: Zjistěte, jak škálovat webovou aplikaci prostředků, cloudovou službu, sadu virtuálních strojů nebo virtuálních strojů v Azure.
ms.topic: conceptual
ms.date: 07/07/2017
ms.subservice: autoscale
ms.openlocfilehash: 0a40496e4d496d0062c6ee7a6ab935c1ad9b35a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75396356"
---
# <a name="get-started-with-autoscale-in-azure"></a>Začínáme s automatickým škálování v Azure
Tento článek popisuje, jak nastavit nastavení automatického škálování pro váš prostředek na portálu Microsoft Azure.

Automatické škálování Azure Monitor uplatní jenom [pro škálovací sady virtuálních strojů](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [cloudové služby](https://azure.microsoft.com/services/cloud-services/), [aplikační služby – webové aplikace](https://azure.microsoft.com/services/app-service/web/)a [služby správy rozhraní API](https://docs.microsoft.com/azure/api-management/api-management-key-concepts).

## <a name="discover-the-autoscale-settings-in-your-subscription"></a>Zjištění nastavení automatického škálování v předplatném
Můžete zjistit všechny prostředky, pro které je možné automatické škálování v Azure Monitoru. Postup krok za krokem použijte následující kroky:

1. Otevřete [portál Azure.][1]
1. Klikněte na ikonu Azure Monitor v levém podokně.
  ![Otevření Azure Monitoru][2]
1. Kliknutím na **Automatické škálování zobrazíte** všechny prostředky, pro které se automatické škálování vztahuje, spolu s jejich aktuálním stavem automatického škálování.
  ![Zjišťování automatického škálování v Azure Monitoru][3]

Podokno filtru v horní části můžete použít k vymezení rozsahu v seznamu k výběru prostředků v určité skupině prostředků, konkrétních typech prostředků nebo konkrétním prostředku.

Pro každý prostředek najdete aktuální počet instancí a stav Automatické škálování. Stav Automatické škálování může být následující:

- **Není nakonfigurováno**: Pro tento prostředek jste ještě nepovolili automatické škálování.
- **Povoleno**: Pro tento prostředek jste povolili automatické škálování.
- **Zakázáno**: Pro tento prostředek jste zakázali automatické škálování.

## <a name="create-your-first-autoscale-setting"></a>Vytvoření prvního nastavení automatického škálování

Nyní si projdeme jednoduchý mač krok za krokem a vytvořte si první nastavení automatického škálování.

1. Otevřete okno **automatického škálování** v Azure Monitoru a vyberte prostředek, který chcete škálovat. (Následující kroky používají plán služby App Service přidružený k webové aplikaci. První [ASP.NET webovou aplikaci][4]si můžete vytvořit v Azure za 5 minut.
1. Všimněte si, že počet aktuální instance je 1. Klepněte na **tlačítko Povolit automatické škálování**.
  ![Nastavení škálování pro novou webovou aplikaci][5]
1. Zadejte název nastavení měřítka a klepněte na tlačítko **Přidat pravidlo**. Všimněte si možností pravidla škálování, které se otevírají jako podokno kontextu na pravé straně. Ve výchozím nastavení nastaví teč na velikost počtu instancí o 1, pokud procento procesoru prostředku překročí 70 procent. Ponechejte ji na výchozích hodnotách a klepněte na **tlačítko Přidat**.
  ![Vytvoření nastavení měřítka pro webovou aplikaci][6]
1. Nyní jste vytvořili své první pravidlo měřítka. Všimněte si, že uživatelské houževnatých používá nos a uvádí, že "Doporučuje se mít alespoň jedno měřítko v pravidle." Postupujte následovně:

    a. Klikněte na **Přidání pravidla**.

    b. Nastavte **operátor** na **menší než**.

    c. Nastavte **prahovou hodnotu** na **20**.

    d. Nastavit **operaci** na **snížení počtu podle**.

   Nyní byste měli mít nastavení škálování, které se škáluje v závislosti na využití procesoru.
   ![Škálování na základě procesoru][8]
1. Klikněte na **Uložit**.

Blahopřejeme! Nyní jste úspěšně vytvořili první nastavení škálování pro automatické škálování webové aplikace na základě využití procesoru.

> [!NOTE]
> Stejné kroky platí pro zahájení práce s rolí škálovací sady virtuálních strojů nebo cloudové služby.

## <a name="other-considerations"></a>Další aspekty
### <a name="scale-based-on-a-schedule"></a>Škálování na základě plánu
Kromě škálování na základě procesoru můžete škálovat odlišně pro konkrétní dny v týdnu.

1. Klepněte **na tlačítko Přidat podmínku měřítka**.
1. Nastavení režimu škálování a pravidel je stejné jako výchozí podmínka.
1. Vyberte **Možnost Opakovat konkrétní dny** plánu.
1. Vyberte dny a čas zahájení a ukončení, kdy má být podmínka měřítka použita.

![Podmínka škálování na základě plánu][9]
### <a name="scale-differently-on-specific-dates"></a>Škálování odlišně v konkrétních datech
Kromě škálování na základě procesoru můžete škálovat odlišně pro konkrétní data.

1. Klepněte **na tlačítko Přidat podmínku měřítka**.
1. Nastavení režimu škálování a pravidel je stejné jako výchozí podmínka.
1. Vyberte **Zadat počáteční a koncové datum** plánu.
1. Vyberte počáteční a koncové datum a čas zahájení a ukončení, kdy má být podmínka měřítka použita.

![Podmínka škálování na základě kalendářních dat][10]

### <a name="view-the-scale-history-of-your-resource"></a>Zobrazení historie škálování zdroje
Kdykoli je prostředek škálován nahoru nebo dolů, je do protokolu aktivit zaznamenána událost. Historii měřítka zdroje za posledních 24 hodin můžete zobrazit přepnutím na kartu **Spustit historii.**

![Historie spuštění][11]

Pokud chcete zobrazit celou historii měřítka (až na 90 dní), vyberte **klepnutím sem zobrazíte další podrobnosti**. Otevře se protokol aktivit s předvoleným automatickým škálovánípro váš prostředek a kategorii.

### <a name="view-the-scale-definition-of-your-resource"></a>Zobrazení definice měřítka zdroje
Automatické škálování je prostředek Azure Resource Manager. Definici měřítka v JSON můžete zobrazit přepnutím na kartu **JSON.**

![Definice měřítka][12]

V případě potřeby můžete provádět změny v JSON přímo. Tyto změny se projeví po jejich uložení.

### <a name="disable-autoscale-and-manually-scale-your-instances"></a>Zakázání automatického škálování a ruční škálování instancí
Může nastat někdy, kdy chcete zakázat aktuální nastavení měřítka a ručně škálovat prostředek.

Nahoře klepněte na tlačítko **Zakázat automatické škálování.**
![Zakázat automatické škálování][13]

> [!NOTE]
> Tato možnost zakáže konfiguraci. Můžete se však vrátit k němu po povolení automatického škálování znovu.

Nyní můžete nastavit počet instancí, které chcete škálovat ručně.

![Nastavení ručního měřítka][14]

Do automatického škálování se můžete vždy vrátit klepnutím na **tlačítko Povolit automatické škálování** a potom **na Uložit**.

## <a name="next-steps"></a>Další kroky
- [Vytvoření výstrahy protokolu aktivit pro sledování všech operací motoru automatického škálování v rámci předplatného](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)
- [Vytvoření výstrahy protokolu aktivit ke sledování všech neúspěšných operací automatického škálování na škálování a škálování na vašem předplatném](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)

<!--Reference-->
[1]:https://portal.azure.com
[2]: ./media/autoscale-get-started/azure-monitor-launch.png
[3]: ./media/autoscale-get-started/discover-autoscale-azure-monitor.png
[4]: https://docs.microsoft.com/azure/app-service/app-service-web-get-started-dotnet
[5]: ./media/autoscale-get-started/scale-setting-new-web-app.png
[6]: ./media/autoscale-get-started/create-scale-setting-web-app.png
[7]: ./media/autoscale-get-started/scale-in-recommendation.png
[8]: ./media/autoscale-get-started/scale-based-on-cpu.png
[9]: ./media/autoscale-get-started/scale-condition-schedule.png
[10]: ./media/autoscale-get-started/scale-condition-dates.png
[11]: ./media/autoscale-get-started/scale-history.png
[12]: ./media/autoscale-get-started/scale-definition-json.png
[13]: ./media/autoscale-get-started/disable-autoscale.png
[14]: ./media/autoscale-get-started/set-manualscale.png

