---
title: Začínáme s automatickým škálováním v Azure
description: Naučte se škálovat webovou aplikaci prostředků, cloudovou službu, virtuální počítač nebo sadu škálování virtuálních počítačů v Azure.
ms.topic: conceptual
ms.date: 07/07/2017
ms.subservice: autoscale
ms.openlocfilehash: b8d16b4e112c9aebe86c60dc01d380d591fc7624
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91743518"
---
# <a name="get-started-with-autoscale-in-azure"></a>Začínáme s automatické škálování v Azure
Tento článek popisuje, jak nastavit nastavení automatického škálování pro váš prostředek v portál Microsoft Azure.

Automatické škálování Azure Monitor platí jenom pro služby [Virtual Machine Scale Sets](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [Cloud Services](https://azure.microsoft.com/services/cloud-services/), [App Service-Web Apps](https://azure.microsoft.com/services/app-service/web/)a [API Management](../../api-management/api-management-key-concepts.md).

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

## <a name="route-traffic-to-healthy-instances-app-service"></a>Směrování provozu do instancí v pořádku (App Service)

Při horizontálním navýšení kapacity na více instancí může App Service provádět kontroly stavu instancí, aby bylo možné směrovat provoz pouze do zdravých instancí. Provedete to tak, že otevřete portál pro App Service a v části **monitorování**vyberete **kontrolu stavu** . Vyberte **Povolit** a zadejte platnou cestu adresy URL v aplikaci, například `/health` nebo `/api/health` . Klikněte na **Uložit**.

Chcete-li tuto funkci povolit u šablon ARM, nastavte `healthcheckpath` vlastnost `Microsoft.Web/sites` prostředku na cestu k kontrole stavu na webu, například: `"/api/health/"` . Chcete-li funkci zakázat, nastavte vlastnost zpět na prázdný řetězec `""` .

### <a name="health-check-path"></a>Cesta kontroly stavu

Cesta musí odpovídat do dvou minut se stavovým kódem mezi 200 a 299 (včetně). Pokud cesta nereaguje do dvou minut nebo vrátí stavový kód mimo daný rozsah, instance se považuje za "není v pořádku". Při kontrole stavu se integruje s funkcemi ověřování a autorizace App Service, bude systém kontaktovat koncový bod i v případě, že jsou povolené tyto funkce secuity. Pokud používáte vlastní ověřovací systém, musí cesta k kontrole stavu umožňovat anonymní přístup. Pokud má lokalita povolenou možnost HTTP**s**, bude žádost Healthcheck odeslána prostřednictvím protokolu HTTP**s**.

Cesta pro kontrolu stavu by měla kontrolovat kritické součásti aplikace. Například pokud vaše aplikace závisí na databázi a systému zasílání zpráv, koncový bod kontroly stavu by se měl k těmto součástem připojit. Pokud se aplikace nemůže připojit k důležité součásti, měla by tato cesta vracet kód odpovědi 500 na úrovni, aby označoval, že aplikace není v pořádku.

#### <a name="security"></a>Zabezpečení 

Vývojové týmy ve velkých podnicích často potřebují splňovat požadavky na zabezpečení pro vystavená rozhraní API. K zabezpečení koncového bodu Healthcheck byste nejdřív měli používat funkce, jako jsou [omezení IP adresy](../../app-service/app-service-ip-restrictions.md#adding-ip-address-rules), [klientské certifikáty](../../app-service/app-service-ip-restrictions.md#adding-ip-address-rules)nebo Virtual Network, které omezují přístup k aplikaci. Koncový bod Healthcheck můžete zabezpečit samotný tím, že vyžadujete, aby `User-Agent` příchozí požadavek odpovídal `ReadyForRequest/1.0` . User-Agent nemůže být zfalšovaný, protože tato žádost byla již zabezpečená předchozími bezpečnostními funkcemi.

### <a name="behavior"></a>Chování

Po zadání cesty pro kontrolu stavu App Service otestuje cestu k cestě všech instancí. Pokud kód úspěšné odpovědi po 5 příkazech otestuje neobdrží, považuje se tato instance za "není v pořádku". Z rotace nástroje pro vyrovnávání zatížení budou vyloučeny instance, které nejsou v pořádku. Při horizontálním navýšení nebo zmenšení kapacity App Service nástroj otestuje cestu kontroly stavu, aby bylo zajištěno, že nové instance budou připravené na požadavky.

Zbývající instance v pořádku můžou vyskytnout zvýšené zatížení. Aby nedošlo k zahlcení zbývajících instancí, nebudou vyloučeny žádné více než polovina instancí. Pokud je například plán App Service škálované na 4 instance a 3 z nich není v pořádku, bude při rotaci nástroje pro vyrovnávání zatížení vyloučeno maximálně 2. Ostatní 2 instance (1 v pořádku a 1 poškozený) budou i nadále přijímat požadavky. V nejhorším případě, kdy nejsou všechny instance v pořádku, se nevylučují žádné.

Pokud instance zůstane po jednu hodinu v nesprávném stavu, bude nahrazena novou instancí. V jednom z nich bude za hodinu nahrazena maximálně jedna instance, a to s maximálním počtem tří instancí za den a App Service.

### <a name="monitoring"></a>Monitorování

Po poskytnutí cesty pro kontrolu stavu vaší aplikace můžete monitorovat stav svého webu pomocí Azure Monitor. V okně pro **kontrolu stavu** na portálu klikněte na **metriky** na horním panelu nástrojů. Otevře se nové okno, kde můžete zobrazit historický stav webu a vytvořit nové pravidlo výstrahy. Další informace o monitorování vašich lokalit najdete v [příručce k Azure monitor](../../app-service/web-sites-monitor.md).

## <a name="next-steps"></a>Další kroky
- [Vytvoření upozornění protokolu aktivit pro monitorování všech operací modulu automatického škálování v předplatném](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)
- [Vytvoření upozornění protokolu aktivit pro monitorování všech neúspěšných operací automatického škálování na více instancích ve vašem předplatném](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)

<!--Reference-->
[1]:https://portal.azure.com
[2]: ./media/autoscale-get-started/azure-monitor-launch.png
[3]: ./media/autoscale-get-started/discover-autoscale-azure-monitor.png
[4]: ../../app-service/quickstart-dotnetcore.md
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
