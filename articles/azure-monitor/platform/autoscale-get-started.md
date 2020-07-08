---
title: Začínáme s automatickým škálováním v Azure
description: Naučte se škálovat webovou aplikaci prostředků, cloudovou službu, virtuální počítač nebo sadu škálování virtuálních počítačů v Azure.
ms.topic: conceptual
ms.date: 07/07/2017
ms.subservice: autoscale
ms.openlocfilehash: db1814c0a27ac6d61c7353a2c66cb5a1a72688d3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "82801599"
---
# <a name="get-started-with-autoscale-in-azure"></a>Začínáme s automatické škálování v Azure
Tento článek popisuje, jak nastavit nastavení automatického škálování pro váš prostředek v portál Microsoft Azure.

Automatické škálování Azure Monitor platí jenom pro služby [Virtual Machine Scale Sets](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [Cloud Services](https://azure.microsoft.com/services/cloud-services/), [App Service-Web Apps](https://azure.microsoft.com/services/app-service/web/)a [API Management](https://docs.microsoft.com/azure/api-management/api-management-key-concepts).

## <a name="discover-the-autoscale-settings-in-your-subscription"></a>Zjištění nastavení automatického škálování v předplatném

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4u7ts]

Můžete zjistit všechny prostředky, pro které je možné automatické škálování použít v Azure Monitor. Podrobný návod k použití následujících kroků:

1. Otevřete [Azure Portal.][1]
1. V levém podokně klikněte na ikonu Azure Monitor.
  ![Otevřít Azure Monitor][2]
1. Kliknutím na možnost **Automatické škálování** zobrazíte všechny prostředky, pro které je možné použít automatické škálování, spolu s jejich aktuálním stavem automatického škálování.
  ![Zjišťovat automatické škálování v Azure Monitor][3]

Můžete použít podokno filtru v horní části k určení oboru v seznamu pro výběr prostředků v konkrétní skupině prostředků, konkrétní typy prostředků nebo konkrétní prostředek.

U každého prostředku se zjistí aktuální počet instancí a stav automatického škálování. Stav automatického škálování může být:

- **Nenakonfigurováno**: pro tento prostředek ještě není povolené automatické škálování.
- **Povoleno**: pro tento prostředek jste povolili automatické škálování.
- **Zakázáno**: pro tento prostředek jste zakázali automatické škálování.

## <a name="create-your-first-autoscale-setting"></a>Vytvoření prvního nastavení automatického škálování

Teď si projdeme jednoduchý podrobný návod k vytvoření vašeho prvního nastavení automatického škálování.

1. Otevřete okno **Automatické škálování** v Azure monitor a vyberte prostředek, který chcete škálovat. (V následujících krocích se používá plán App Service přidružený k webové aplikaci. [Svou první webovou aplikaci v ASP.NET můžete v Azure vytvořit během 5 minut.][4])
1. Všimněte si, že aktuální počet instancí je 1. Klikněte na **Povolit automatické škálování**.
  ![Nastavení škálování pro novou webovou aplikaci][5]
1. Zadejte název nastavení škálování a pak klikněte na **Přidat pravidlo**. Všimněte si možností pravidla škálování, které se otevřou jako kontextové podokno na pravé straně. Ve výchozím nastavení tato možnost nastaví možnost škálování počtu instancí o 1, pokud procento prostředku procesoru překročí 70 procent. Ponechte jeho výchozí hodnoty a klikněte na **Přidat**.
  ![Vytvoření nastavení škálování pro webovou aplikaci][6]
1. Nyní jste vytvořili své první pravidlo škálování. Všimněte si, že UX doporučuje Doporučené postupy a uvádí, že "v pravidle" doporučujeme mít aspoň jedno škálování. " Postupujte následovně:

    a. Klikněte na **Přidání pravidla**.

    b. Nastavte **operátor** na **míň než**.

    c. Nastavte **práh** na **20**.

    d. Nastavte **operaci** na **snížení počtu o**.

   Teď byste měli mít nastavení škálování, které se v závislosti na využití procesoru škáluje na škále.
   ![Škálování na základě procesoru][8]
1. Klikněte na **Uložit**.

Gratulujeme! Úspěšně jste vytvořili první nastavení škálování pro automatické škálování webové aplikace na základě využití procesoru.

> [!NOTE]
> Stejné kroky jsou použitelné pro zahájení práce se sadou škálování virtuálních počítačů nebo s rolí cloudové služby.

## <a name="other-considerations"></a>Další důležité informace
### <a name="scale-based-on-a-schedule"></a>Škálování podle plánu
Kromě škálování na základě procesoru můžete škálování nastavit jinak na konkrétní dny v týdnu.

1. Klikněte na **Přidat podmínku škálování**.
1. Nastavení režimu škálování a pravidla se shodují s výchozí podmínkou.
1. Pro plán vyberte **Opakovat konkrétní dny** .
1. Vyberte dny a počáteční/koncový čas, kdy se má podmínka škálování použít.

![Podmínka škálování podle plánu][9]
### <a name="scale-differently-on-specific-dates"></a>Jiné škálování pro konkrétní data
Kromě škálování na základě procesoru můžete nastavit škálování odlišně pro konkrétní data.

1. Klikněte na **Přidat podmínku škálování**.
1. Nastavení režimu škálování a pravidla se shodují s výchozí podmínkou.
1. Vyberte **zadat počáteční a koncové datum** pro plán.
1. Vyberte počáteční a koncové datum a počáteční/koncový čas, kdy se má podmínka škálování použít.

![Podmínka škálování založená na datech][10]

### <a name="view-the-scale-history-of-your-resource"></a>Zobrazit historii škálování prostředku
Při každém horizontálním navýšení nebo snížení kapacity prostředku se zaprotokoluje událost do protokolu aktivit. Historii škálování prostředku můžete zobrazit za posledních 24 hodin přepnutím na kartu **historie spuštění** .

![Historie spuštění][11]

Pokud chcete zobrazit celou historii škálování (po dobu až 90 dní), vyberte **kliknutím sem zobrazíte další podrobnosti**. Otevře se protokol aktivit s možnostmi automatického škálování předem vybraným pro váš prostředek a kategorii.

### <a name="view-the-scale-definition-of-your-resource"></a>Zobrazení definice měřítka prostředku
Automatické škálování je Azure Resource Manager prostředek. Definici měřítka ve formátu JSON můžete zobrazit přepnutím na kartu **JSON** .

![Definice škálování][12]

V případě potřeby můžete provádět změny ve formátu JSON přímo. Tyto změny se projeví po jejich uložení.

### <a name="disable-autoscale-and-manually-scale-your-instances"></a>Zakažte automatické škálování a ručně Škálujte své instance.
Může nastat situace, kdy budete chtít zakázat aktuální nastavení škálování a ručně škálovat prostředek.

Klikněte na tlačítko **Zakázat automatické škálování** v horní části.
![Zakázat automatické škálování][13]

> [!NOTE]
> Tato možnost zakáže vaši konfiguraci. Po opětovném zapnutí automatického škálování se ale můžete k němu vrátit.

Nyní můžete nastavit počet instancí, které chcete ručně škálovat.

![Nastavení ručního škálování][14]

K automatickému škálování se můžete kdykoli vrátit kliknutím na **Povolit automatické škálování** a pak na **Uložit**.

## <a name="next-steps"></a>Další kroky
- [Vytvoření upozornění protokolu aktivit pro monitorování všech operací modulu automatického škálování v předplatném](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)
- [Vytvoření upozornění protokolu aktivit pro monitorování všech neúspěšných operací automatického škálování na více instancích ve vašem předplatném](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)

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

