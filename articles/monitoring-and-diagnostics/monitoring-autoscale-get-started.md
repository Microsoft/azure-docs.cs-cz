---
title: Začínáme s škálování v Azure
description: Zjistěte, jak se škálovat prostředek webové aplikace, Cloudová služba, virtuálního počítače nebo virtuálního počítače nastavit v Azure.
author: rajram
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/07/2017
ms.author: rajram
ms.component: autoscale
ms.openlocfilehash: 2781e718e3829c13dcc8cdd998936cfba30d8550
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35263642"
---
# <a name="get-started-with-autoscale-in-azure"></a>Začínáme s škálování v Azure
Tento článek popisuje, jak nastavit nastavení automatického škálování prostředku na portálu Microsoft Azure.

Azure monitorování škálování se vztahuje pouze na sady škálování virtuálního počítače, cloudové služby, plány služby Azure App Service a služby App Service Environment. 

## <a name="discover-the-autoscale-settings-in-your-subscription"></a>Zjištění nastavení škálování v rámci vašeho předplatného
Můžete zjistit všechny prostředky, na které se vztahuje v Azure monitorování škálování. Podrobný návod, pomocí následujících kroků:

1. Otevřete [portálu Azure.][1]
2. Klikněte na ikonu monitorování Azure v levém podokně.
  ![Otevřete Azure monitorování][2]
3. Klikněte na tlačítko **škálování** zobrazíte všechny prostředky, na které se vztahuje, včetně jejich aktuálního stavu škálování škálování.
  ![Zjistit škálování v Azure monitorování][3]

Můžete podokno filtru v horní části do oboru dolů v seznamu vyberte prostředky v určité skupiny zdrojů, konkrétní typy prostředků nebo konkrétní prostředek.

Pro každý prostředek zjistíte, aktuální počet instancí a stav automatického škálování. Stav škálování může být:

- **Není nakonfigurováno**: jste nepovolili automatické škálování ještě pro tento prostředek.
- **Povolit**: jste povolili automatické škálování pro tento prostředek.
- **Zakázané**: Zakázání automatického škálování pro tento prostředek.

## <a name="create-your-first-autoscale-setting"></a>Vytvoření vaší první nastavení automatického škálování

Pojďme se teď přejděte prostřednictvím jednoduchého podrobný návod k vytvoření vaší první nastavení automatického škálování.

1. Otevřete **škálování** okno v Azure monitorování a vyberte prostředek, který chcete škálovat. (Plán služby App Service přidružených k webové aplikaci použít následující kroky. Můžete [vytvořte první webové aplikace ASP.NET v Azure během 5 minut.] [4])
2. Všimněte si, že je aktuální počet instancí 1. Klikněte na tlačítko **povolit škálování**.
  ![Nastavení škálování pro novou webovou aplikaci][5]
3. Zadejte název pro nastavení škálování a pak klikněte na tlačítko **přidat pravidlo**. Všimněte si, pravidlo možnosti škálování, které se otevřou jako kontext podokně na pravé straně. Ve výchozím nastavení nastaví tato možnost škálování vašeho počet instancí 1, pokud procento využití procesoru prostředku přesahuje 70 procent. Nechte na jeho výchozí hodnoty a klikněte na **přidat**.
  ![Vytvoření nastavení škálování pro webovou aplikaci][6]
4. Nyní jste vytvořili vaší první pravidlo škálování. Upozorňujeme, že UX doporučuje osvědčené postupy a uvádí, že "se doporučuje mít alespoň jeden škálování v pravidle." Postupujte následovně:
  
    a. Klikněte na tlačítko **přidat pravidlo**. 

    b. Nastavit **operátor** k **menší než**.

    c. Nastavit **prahová hodnota** k **20**.

    d. Nastavit **operace** k **snížit počet podle**.

   Teď byste měli mít nastavení škálování, měřítka na více systémů nebo měřítka v podle využití procesoru.
   ![Škálování podle využití procesoru][8]
5. Klikněte na **Uložit**.

Blahopřejeme! Nyní jste úspěšně vytvořili vaší první nastavení škálování chcete používat automatické škálování podle využití procesoru na základě vaší webové aplikace.

> [!NOTE] 
> Stejný postup platí pro začít pracovat s škálovací sadu virtuálních počítačů nebo cloudu, služby rolí.

## <a name="other-considerations"></a>Další důležité informace
### <a name="scale-based-on-a-schedule"></a>Škálování podle plánu
Kromě škálování podle využití procesoru můžete nastavit vaše škálování odlišně pro konkrétní dny v týdnu.

1. Klikněte na tlačítko **přidat podmínku škálování**.
2. Nastavení režimu škálování a pravidel je stejná jako výchozí podmínku.
3. Vyberte **opakujte konkrétní dny** pro plán.
4. Vyberte dny a čas zahájení a ukončení kdy má být použita podmínka škálování.

![Podmínka škálování podle plánu][9]
### <a name="scale-differently-on-specific-dates"></a>Jinak škálování na konkrétní kalendářní data
Kromě škálování podle využití procesoru můžete nastavit vaše škálování odlišně pro konkrétní kalendářní data.

1. Klikněte na tlačítko **přidat podmínku škálování**.
2. Nastavení režimu škálování a pravidel je stejná jako výchozí podmínku.
3. Vyberte **zadejte počáteční a koncové datum** pro plán.
4. Vyberte počáteční a koncové datum a čas zahájení a ukončení kdy má být použita podmínka škálování.

![Škálování podmínky na základě dat][10]

### <a name="view-the-scale-history-of-your-resource"></a>Zobrazení historie škálování prostředku
Vždy, když prostředek je škálovat nahoru nebo dolů, se zaprotokoluje událost v protokolu aktivit. Můžete zobrazit historii škálování prostředku za posledních 24 hodin při přechodu **historie spouštění** kartě.

![Historie spuštění][11]

Pokud chcete zobrazit historii dokončení škálování (po dobu 90 dnů), vyberte **kliknutím sem zobrazíte další podrobnosti**. Protokol aktivit otevře s škálování předem vybraná pro prostředek a kategorie.

### <a name="view-the-scale-definition-of-your-resource"></a>Zobrazit definici škálování prostředku
Při automatickém škálování je prostředek Azure Resource Manager. Definici škálování si můžete prohlédnout v JSON při přechodu **JSON** kartě.

![Definice škálování][12]

Můžete provádět změny v kódu JSON přímo, pokud je to nutné. Tyto změny se projeví po uložení je.

### <a name="disable-autoscale-and-manually-scale-your-instances"></a>Zakažte automatické škálování a ručně škálovat vaše instance
Může být časy, kdy chcete zakázat aktuální nastavení škálování a ručně škálovat prostředek.

Klikněte **zakázat automatické škálování** tlačítka v horní části.
![Zakázat automatické škálování][13]

> [!NOTE] 
> Tato možnost zakáže konfiguraci. Však můžete vrátit zpět k němu po znovu povolte automatické škálování. 

Teď můžete nastavit počet instancí, které chcete škálovat ručně.

![Ruční škálování sady][14]

Můžete vždy se vrátit škálování kliknutím **povolit škálování** a potom **Uložit**.

## <a name="next-steps"></a>Další kroky
- [Vytvoření aktivity protokolu výstrahy monitorovat všechny operace škálování modul vaše předplatné](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)
- [Vytvoření aktivity protokolu výstrahy monitorovat všechny neúspěšné operace škálování nebo škálovatelnou škálování na vaše předplatné](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)

<!--Reference-->
[1]:https://portal.azure.com
[2]: ./media/monitoring-autoscale-get-started/azure-monitor-launch.png
[3]: ./media/monitoring-autoscale-get-started/discover-autoscale-azure-monitor.png
[4]: https://docs.microsoft.com/azure/app-service/app-service-web-get-started-dotnet
[5]: ./media/monitoring-autoscale-get-started/scale-setting-new-web-app.png
[6]: ./media/monitoring-autoscale-get-started/create-scale-setting-web-app.png
[7]: ./media/monitoring-autoscale-get-started/scale-in-recommendation.png
[8]: ./media/monitoring-autoscale-get-started/scale-based-on-cpu.png
[9]: ./media/monitoring-autoscale-get-started/scale-condition-schedule.png
[10]: ./media/monitoring-autoscale-get-started/scale-condition-dates.png
[11]: ./media/monitoring-autoscale-get-started/scale-history.png
[12]: ./media/monitoring-autoscale-get-started/scale-definition-json.png
[13]: ./media/monitoring-autoscale-get-started/disable-autoscale.png
[14]: ./media/monitoring-autoscale-get-started/set-manualscale.png

