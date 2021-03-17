---
title: Řešení potíží se stavem snížené funkčnosti v Azure Traffic Manager
description: Jak řešit potíže s profilem Traffic Manager, když se zobrazuje jako snížený stav.
services: traffic-manager
documentationcenter: ''
author: duongau
manager: kumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/03/2017
ms.author: duau
ms.openlocfilehash: b76eab5771d724e4f0ec56b7d5acd5cf5f91edc0
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2021
ms.locfileid: "98183451"
---
# <a name="troubleshooting-degraded-state-on-azure-traffic-manager"></a>Řešení potíží při sníženém výkonu služby Azure Traffic Manager

Tento článek popisuje, jak řešit potíže s profilem Azure Traffic Manager, který zobrazuje snížený stav. Jako první krok při řešení potíží se stavem degradování Azure Traffic Manager je povolení protokolování.  Další informace najdete v tématu [Povolení protokolů prostředků](./traffic-manager-diagnostic-logs.md) . V tomto scénáři zvažte, že jste nakonfigurovali profil Traffic Manager, který odkazuje na některé z vašich hostovaných služeb cloudapp.net. Pokud stav Traffic Manager **zobrazuje snížený stav,** může být **snížen** stav jednoho nebo více koncových bodů:

![stav sníženého koncového bodu](./media/traffic-manager-troubleshooting-degraded/traffic-manager-degradedifonedegraded.png)

Pokud stav Traffic Manager zobrazuje **neaktivní** stav, mohou být oba koncové body **zakázané**:

![Stav neaktivního Traffic Manager](./media/traffic-manager-troubleshooting-degraded/traffic-manager-inactive.png)

## <a name="understanding-traffic-manager-probes"></a>Principy Traffic Manager sondy

* Traffic Manager považuje koncový bod za ONLINE, jenom když sonda obdrží odpověď HTTP 200 z cesty testu. Pokud aplikace vrátí jakýkoli jiný kód odpovědi HTTP, měli byste tento kód odpovědi přidat do [rozsahů očekávaných stavových kódů](./traffic-manager-monitoring.md#configure-endpoint-monitoring) vašeho profilu Traffic Manager.
* Odpověď přesměrování 30krát se považuje za neočekávanou, pokud jste ji nezadali jako platný kód odezvy v [rozsahu očekávaných stavových kódů](./traffic-manager-monitoring.md#configure-endpoint-monitoring) vašeho profilu Traffic Manager. Traffic Manager netestuje cíl přesměrování.
* V případě sond protokolu HTTPs se chyby certifikátů ignorují.
* Skutečný obsah cesty testu nezáleží na tom, dokud se vrátí 200. Běžným způsobem je zjišťování adresy URL pro nějaký statický obsah, jako je "/favicon.ico". Dynamický obsah, podobně jako stránky ASP, nemusí vždycky vracet 200, i když je aplikace v pořádku.
* Osvědčeným postupem je nastavit cestu testu na něco, co má dostatek logiky pro zjištění, že je lokalita nahoru nebo dolů. V předchozím příkladu nastavením cesty na "/favicon.ico" otestujete pouze to, zda w3wp.exe reaguje. Tato sonda nemusí znamenat, že vaše webová aplikace je v pořádku. Lepší možností je nastavit cestu k nějakému typu, například "/PROBE.aspx", který má logiku k určení stavu webu. Můžete například použít čítače výkonu k využití procesoru nebo změřit počet neúspěšných žádostí. Nebo se můžete pokusit o přístup k prostředkům databáze nebo stavu relace, abyste se ujistili, že webová aplikace funguje.
* Pokud dojde ke zhoršení všech koncových bodů v profilu, Traffic Manager zachází se všemi koncovými body jako v pořádku a směruje provoz do všech koncových bodů. Tím zajistíte, že problémy s mechanismem zjišťování nevedou k úplnému výpadku vaší služby.

## <a name="troubleshooting"></a>Řešení potíží

Chcete-li vyřešit selhání sondy, potřebujete nástroj, který zobrazuje stavový kód HTTP vrácený z adresy URL testu. K dispozici je mnoho nástrojů, které ukazují nezpracované odpovědi HTTP.

* [Fiddler](https://www.telerik.com/fiddler)
* [Curl](https://curl.haxx.se/)
* [wget](http://gnuwin32.sourceforge.net/packages/wget.htm)

K zobrazení odpovědí HTTP můžete také použít kartu síť v ladicích nástrojích F12 v Internet Exploreru.

V tomto příkladu chceme zobrazit odpověď z naší adresy URL testu: http: \/ /watestsdp2008r2.cloudapp.NET:80/PROBE. Následující příklad prostředí PowerShell znázorňuje problém.

```powershell
Invoke-WebRequest 'http://watestsdp2008r2.cloudapp.net/Probe' -MaximumRedirection 0 -ErrorAction SilentlyContinue | Select-Object StatusCode,StatusDescription
```

Příklad výstupu:

```output
StatusCode StatusDescription
---------- -----------------
        301 Moved Permanently
```

Všimněte si, že jsme dostali odpověď na přesměrování. Jak bylo uvedeno dříve, jakékoli StatusCode kromě 200 se považuje za selhání. Traffic Manager změní stav koncového bodu na offline. Pokud chcete tento problém vyřešit, zkontrolujte konfiguraci webu, abyste měli jistotu, že se dá z cesty testu vrátit správný StatusCode. Překonfigurujte Traffic Manager test tak, aby odkazoval na cestu, která vrací 200.

Pokud vaše sonda používá protokol HTTPS, možná budete muset zakázat kontrolu certifikátů, aby se předešlo chybám SSL/TLS během testu. Následující příkazy PowerShellu zakazují ověření certifikátu pro aktuální relaci prostředí PowerShell:

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

[Informace o metodách směrování provozu Traffic Manager](traffic-manager-routing-methods.md)

[Co je Traffic Manager](traffic-manager-overview.md)

[Cloudové služby](/previous-versions/azure/jj155995(v=azure.100))

[Azure App Service](https://azure.microsoft.com/documentation/services/app-service/web/)

[Operace v Traffic Manageru (referenční informace k rozhraní API REST)](/previous-versions/azure/reference/hh758255(v=azure.100))

[Rutiny Azure Traffic Manager][1]

[1]: /powershell/module/az.trafficmanager