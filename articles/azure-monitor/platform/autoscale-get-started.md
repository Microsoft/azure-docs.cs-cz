---
title: Začínáme s automatickým Škálováním v Azure
description: Zjistěte, jak škálovat prostředek webové aplikace, Cloudovou službu, virtuálního počítače nebo virtuálního počítače Škálovací sady v Azure.
author: rajram
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/07/2017
ms.author: rajram
ms.subservice: autoscale
ms.openlocfilehash: 0535c84a8ee0776c2c35a46d3c7510a2cd615cf6
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54469487"
---
# <a name="get-started-with-autoscale-in-azure"></a>Začínáme s automatickým Škálováním v Azure
Tento článek popisuje, jak vytvořit nastavení automatického škálování pro váš prostředek na portálu Microsoft Azure.

Automatické škálování služby Azure Monitor se týká pouze [Virtual Machine Scale Sets](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [Cloud Services](https://azure.microsoft.com/services/cloud-services/), [App Service – Web Apps](https://azure.microsoft.com/services/app-service/web/), a [služby API Management](https://docs.microsoft.com/azure/api-management/api-management-key-concepts).

## <a name="discover-the-autoscale-settings-in-your-subscription"></a>Zjištění nastavení automatického škálování v rámci vašeho předplatného
Je možné vyhledat všechny prostředky, pro které se vztahuje ve službě Azure Monitor automatického škálování. Pomocí následujícího postupu podrobný názorný postup:

1. Otevřít [webu Azure portal.][1]
1. Klikněte na ikonu Azure Monitor v levém podokně.
  ![Otevřete Azure Monitor][2]
1. Klikněte na tlačítko **automatického škálování** zobrazit všechny prostředky, pro které se vztahuje, spolu s jejich aktuální stav automatického škálování automatického škálování.
  ![Zjistit automatického škálování ve službě Azure Monitor][3]

Můžete použít podokno filtru v horní části oboru dolů v seznamu vyberte prostředky v konkrétní skupině prostředků, konkrétní typy prostředků nebo konkrétní prostředek.

Pro každý prostředek najdete aktuální počet instancí a stav automatického škálování. Stav automatického škálování může být:

- **Není nakonfigurováno**: Jste nepovolili automatické škálování zatím pro tento prostředek.
- **Povolené**: Pro tento zdroj jste povolili automatické škálování.
- **Zakázané**: Zakázání automatického škálování pro tento prostředek.

## <a name="create-your-first-autoscale-setting"></a>Vytvoření vaší první zadané nastavení automatického škálování

Pojďme se teď projděte si jednoduchý podrobný postup k vytvoření vaší první zadané nastavení automatického škálování.

1. Otevřít **automatického škálování** okno Azure Monitor a vyberte prostředek, který chcete škálovat. (Následující kroky používají plán služby App Service, který je spojený s webovou aplikací. Je možné [vytvoření vaší první webové aplikace ASP.NET v Azure během 5 minut.] [4])
1. Všimněte si, že je aktuální počet instancí 1. Klikněte na tlačítko **povolit automatické škálování**.
  ![Nastavení škálování pro novou webovou aplikaci][5]
1. Zadejte název pro nastavení škálování a potom klikněte na tlačítko **přidat pravidlo**. Všimněte si, že pravidla možností škálování, které se otevřou jako kontextovém podokně na pravé straně. Ve výchozím nastavení tím se nastaví možnost škálování vašeho počtu instancí o 1, pokud procento využití procesoru prostředku přesáhne 70 procent. Ponechte jejich výchozí hodnoty a klikněte na tlačítko **přidat**.
  ![Vytvoření nastavení škálování webové aplikace][6]
1. Právě jste vytvořili první škálovací pravidla. Mějte na paměti, že uživatelského rozhraní doporučuje osvědčené postupy a uvádí, že "se doporučuje mít alespoň jeden škálování v pravidle." Postupujte následovně:

    a. Klikněte na tlačítko **přidat pravidlo**.

    b. Nastavte **operátor** k **menší než**.

    c. Nastavte **prahová hodnota** k **20**.

    d. Nastavte **operace** k **snížit počet o**.

   Teď byste měli mít nastavení škálování, škálování na více instancí/škálování v závislosti na využití procesoru.
   ![Škálování podle využití procesoru][8]
1. Klikněte na **Uložit**.

Blahopřejeme! Právě jste úspěšně vytvořili první nastavení škálování pro automatické škálování webové aplikace podle využití procesoru.

> [!NOTE]
> Stejný postup platí pro vám umožní začít škálovací sadu virtuálních počítačů nebo cloudových služeb role.

## <a name="other-considerations"></a>Další důležité informace
### <a name="scale-based-on-a-schedule"></a>Škálování na základě plánu
Kromě se škálují podle procesoru můžete nastavit škálovací odlišně pro konkrétní dny v týdnu.

1. Klikněte na tlačítko **přidat podmínku škálování**.
1. Nastavení režimu měřítka a pravidla je stejný jako výchozí podmínku.
1. Vyberte **opakovat konkrétní dny** pro daný plán.
1. Vyberte dny a počáteční/koncový čas, kdy by měla používat podmínka škálování.

![Podmínka škálování podle plánu][9]
### <a name="scale-differently-on-specific-dates"></a>Jinak škálovat na konkrétní kalendářní data
Kromě se škálují podle procesoru můžete nastavit škálovací odlišně pro konkrétní kalendářní data.

1. Klikněte na tlačítko **přidat podmínku škálování**.
1. Nastavení režimu měřítka a pravidla je stejný jako výchozí podmínku.
1. Vyberte **zadejte data začátku/konce** pro daný plán.
1. Vyberte data začátku/konce a počáteční/koncový čas, kdy by měla používat podmínka škálování.

![Podmínka škálování na základě dat][10]

### <a name="view-the-scale-history-of-your-resource"></a>Zobrazit historii škálování prostředku
Pokaždé, když se váš prostředek je vertikálně navýšit nebo snížit, zaprotokoluje událost, která v protokolu aktivit. Můžete zobrazit historii škálování prostředku za posledních 24 hodin při přechodu **historie spuštění** kartu.

![Historie spuštění][11]

Pokud chcete zobrazit historii kompletní škálování (po dobu 90 dnů), vyberte **kliknutím sem zobrazíte další podrobnosti**. Protokol aktivit se otevře s automatickým Škálováním předem vybraná zdroje a kategorie.

### <a name="view-the-scale-definition-of-your-resource"></a>Zobrazit definici škálování prostředku
Automatické škálování je prostředek Azure Resource Manageru. Definice škálovací můžete zobrazit ve formátu JSON při přechodu **JSON** kartu.

![Definice škálování][12]

Budete moct změnit ve formátu JSON přímo, podle potřeby. Tyto změny se projeví po uložení.

### <a name="disable-autoscale-and-manually-scale-your-instances"></a>Zakázat automatické škálování a ručně škálujte vaše instance
Může nastat situace, kdy chcete zakázat aktuální nastavení škálování a ručně škálujte váš prostředek.

Klikněte na tlačítko **zakázat automatické škálování** tlačítko v horní části.
![Zakázat automatické škálování][13]

> [!NOTE]
> Tato možnost zakáže vaší konfigurace. Však můžete vrátit k němu po znovu povolit automatické škálování.

Teď můžete nastavit počet instancí, které chcete škálovat ručně.

![Ruční škálovací sady][14]

Můžete se kdykoli vrátit k automatickému škálování kliknutím **povolit automatické škálování** a potom **Uložit**.

## <a name="next-steps"></a>Další postup
- [Vytvoření aktivitu protokolu výstrahy monitorovat všechny operace modul automatického škálování v rámci předplatného](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)
- [Vytvoření aktivitu protokolu výstrahy monitorovat všechny neúspěšné operace škálování a Škálováním automatického škálování v rámci předplatného](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)

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

