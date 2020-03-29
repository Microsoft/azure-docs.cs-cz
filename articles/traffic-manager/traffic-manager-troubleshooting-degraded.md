---
title: Poradce při potížích se sníženým stavem ve Službě Azure Traffic Manager
description: Jak řešit potíže s profily traffic manageru, pokud se zobrazí jako stav se zhoršenou.
services: traffic-manager
documentationcenter: ''
author: rohinkoul
manager: kumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/03/2017
ms.author: rohink
ms.openlocfilehash: c398763405472c9018a5c30d34fbd3963ecb93b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938372"
---
# <a name="troubleshooting-degraded-state-on-azure-traffic-manager"></a>Řešení potíží při sníženém výkonu služby Azure Traffic Manager

Tento článek popisuje, jak řešit potíže s profilem Azure Traffic Manager, který zobrazuje stav se sníženým stavem. Prvním krokem při řešení potíží se sníženým stavem Azure Traffic Manageru je povolení protokolování diagnostiky.  Další informace naleznete v části [Povolení diagnostických protokolů.](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-diagnostic-logs) V tomto scénáři zvažte, že jste nakonfigurovali profil Traffic Manageru, který ukazuje na některé z cloudapp.net hostovaných služeb. Pokud stav traffic manageru zobrazuje **stav zhoršené hodnoty,** může být stav jednoho nebo více koncových bodů **degradován**:

![zhoršený stav koncového bodu](./media/traffic-manager-troubleshooting-degraded/traffic-manager-degradedifonedegraded.png)

Pokud se stav traffic manageru zobrazí **stav Neaktivní,** mohou být **zakázány**oba koncové body :

![Stav správce provozu neaktivní](./media/traffic-manager-troubleshooting-degraded/traffic-manager-inactive.png)

## <a name="understanding-traffic-manager-probes"></a>Principy sond Traffic Manageru

* Traffic Manager považuje koncový bod za ONLINE pouze v případě, že sonda obdrží odpověď HTTP 200 zpět z cesty sondy. Pokud aplikace vrátí jiný kód odpovědi HTTP, měli byste přidat tento kód odpovědi do [rozsahů očekávaného kódu stavu](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-monitoring#configure-endpoint-monitoring) vašeho profilu Traffic Manageru.
* 30násobná odpověď přesměrování je považována za neúspěšnou, pokud jste ji nezadali jako platný kód odpovědi v [rozsahu Očekávaný stavový kód](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-monitoring#configure-endpoint-monitoring) profilu traffic manageru. Traffic Manager nesonduje cíl přesměrování.
* U sond HTTP jsou ignorovány chyby certifikátu.
* Skutečný obsah cesty sondy nezáleží, pokud je vrácena 200. Sondování URL na některé statické obsah jako "/favicon.ico" je běžná technika. Dynamický obsah, jako jsou stránky ASP, nemusí vždy vrátit 200, i když je aplikace v pořádku.
* Osvědčeným postupem je nastavit cestu sondy na něco, co má dostatek logiky k určení, že web je nahoru nebo dolů. V předchozím příkladu nastavením cesty k "/favicon.ico", jste pouze testování, které w3wp.exe odpovídá. Tato sonda nemusí znamenat, že vaše webová aplikace je v pořádku. Lepší možností by bylo nastavit cestu k něco jako "/Probe.aspx", který má logiku k určení stavu webu. Můžete například použít čítače výkonu pro využití procesoru nebo změřit počet neúspěšných požadavků. Nebo se můžete pokusit o přístup k databázovým prostředkům nebo stavu relace a ujistit se, že webová aplikace funguje.
* Pokud jsou všechny koncové body v profilu snížena, pak Traffic Manager považuje všechny koncové body za v pořádku a směruje provoz do všech koncových bodů. Toto chování zajišťuje, že problémy s mechanismem zjišťování nevedou k úplnému výpadku služby.

## <a name="troubleshooting"></a>Řešení potíží

Chcete-li vyřešit selhání sondy, potřebujete nástroj, který zobrazuje návrat stavového kódu HTTP z adresy URL sondy. K dispozici je mnoho nástrojů, které ukazují nezpracovaná odpověď HTTP.

* [Fiddler](https://www.telerik.com/fiddler)
* [Curl](https://curl.haxx.se/)
* [Wget](http://gnuwin32.sourceforge.net/packages/wget.htm)

K zobrazení odpovědí HTTP můžete také použít kartu Síť v nástrojích ladění F12 v aplikaci Internet Explorer.

V tomto příkladu chceme vidět odpověď z naší\/adresy URL sondy: http: /watestsdp2008r2.cloudapp.net:80/Probe. Následující příklad prostředí PowerShell ilustruje problém.

```powershell
Invoke-WebRequest 'http://watestsdp2008r2.cloudapp.net/Probe' -MaximumRedirection 0 -ErrorAction SilentlyContinue | Select-Object StatusCode,StatusDescription
```

Příklad výstupu:

    StatusCode StatusDescription
    ---------- -----------------
           301 Moved Permanently

Všimněte si, že jsme obdrželi odpověď přesměrování. Jak již bylo uvedeno dříve, všechny StatusCode než 200 je považován za selhání. Traffic Manager změní stav koncového bodu na Offline. Chcete-li problém vyřešit, zkontrolujte konfiguraci webu a ujistěte se, že správné StatusCode lze vrátit z cesty sondy. Překonfigurujte sondu Traffic Manager tak, aby přecšlápla na cestu, která vrací hodnotu 200.

Pokud sonda používá protokol HTTPS, bude pravděpodobně nutné zakázat kontrolu certifikátů, aby se zabránilo chybám SSL/TLS během testu. Následující příkazy Prostředí PowerShell zakážou ověření certifikátu pro aktuální relaci Prostředí PowerShell:

```powershell
add-type @"
using System.Net;
using System.Security.Cryptography.X509Certificates;
public class TrustAllCertsPolicy : ICertificatePolicy {
    public bool CheckValidationResult(
    ServicePoint srvPoint, X509Certificate certificate,
    WebRequest request, int certificateProblem) {
    return true;
    }
}
"@
[System.Net.ServicePointManager]::CertificatePolicy = New-Object TrustAllCertsPolicy
```

## <a name="next-steps"></a>Další kroky

[O metodách směrování provozu traffic manageru](traffic-manager-routing-methods.md)

[Co je Traffic Manager](traffic-manager-overview.md)

[Cloudové služby](https://go.microsoft.com/fwlink/?LinkId=314074)

[Azure App Service](https://azure.microsoft.com/documentation/services/app-service/web/)

[Operace v Traffic Manageru (referenční informace k rozhraní API REST)](https://go.microsoft.com/fwlink/?LinkId=313584)

[Rutiny Azure Traffic Manageru][1]

[1]: https://docs.microsoft.com/powershell/module/az.trafficmanager
