---
title: Řešení potíží s degradovaný stav na Azure Traffic Manager
description: Postup řešení potíží s profily Traffic Manager, když se zobrazí jako degradovaný stav.
services: traffic-manager
documentationcenter: ''
author: chadmath
manager: cshepard
ms.assetid: 8af0433d-e61b-4761-adcc-7bc9b8142fc6
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/03/2017
ms.author: genli
ms.openlocfilehash: f5b2f471b13db67411f15f32abad5afc644c04ba
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/03/2018
---
# <a name="troubleshooting-degraded-state-on-azure-traffic-manager"></a>Řešení potíží s degradovaný stav na Azure Traffic Manager

Tento článek popisuje postup řešení potíží s profilem Azure Traffic Manager, který se zobrazuje degradovaném stavu. Pro tento scénář zvažte, které jste nakonfigurovali profil Traffic Manageru odkazující na některé z vašich cloudapp.net hostovaných služeb. Pokud se zobrazí stav z vaší Traffic Manager **snížený** stav a stav jednoho nebo víc koncových bodů může být **snížený**:

![Stav sníženou koncových bodů](./media/traffic-manager-troubleshooting-degraded/traffic-manager-degradedifonedegraded.png)

Pokud se zobrazí stav z vaší Traffic Manager **neaktivní** stavu, pak oba koncové body mohou být **zakázané**:

![Neaktivní stav Traffic Manager](./media/traffic-manager-troubleshooting-degraded/traffic-manager-inactive.png)

## <a name="understanding-traffic-manager-probes"></a>Sondy Principy Traffic Manager

* Správce provozu zvažuje koncového bodu se ONLINE jenom v případě, že tato kontrola obdrží odpověď HTTP 200 zpět z cesty k testu. Druhá odpověď bez 200 je selhání.
* Přesměrování 30 x selže, i když přesměrované adresy URL vrátí 200.
* Pro sondy HTTPs jsou chyby certifikátu ignorovat.
* Skutečný obsah cesty testu není důležité, tak dlouho, dokud se vrátí 200. Zkušební fáze adresu URL také statický obsah, jako je "/ favicon.ico" je běžné techniky. Dynamický obsah, jako jsou stránky ASP nemusí vždy vrátí 200, i v případě, že aplikace je v pořádku.
* Osvědčeným postupem je možné nastavit cestu test na něco, co má dostatek logika pro určení, že lokalita je nahoru nebo dolů. V předchozím příkladu, nastavením cesta "/ favicon.ico", jsou jenom testování této w3wp.exe reaguje. Tento test nemusí znamenat, že webová aplikace v pořádku. Lepší volbou bude nastavení cesty něco jako například "/ Probe.aspx" s cílem určit stav lokality. Například můžete použít čítače výkonu pro využití procesoru nebo určovat počet neúspěšných požadavků. Nebo může pokusit o přístup k prostředkům databáze nebo stav relace, abyste měli jistotu, že webová aplikace funguje.
* Pokud jsou degradovány všechny koncové body v profilu, pak Traffic Manager zpracovává všechny koncové body v pořádku a směrování provozu pro všechny koncové body. To zaručuje, že problémy s zjišťovací mechanismus nevedou k dokončení výpadku služby.

## <a name="troubleshooting"></a>Řešení potíží

Chcete-li vyřešit selhání testu, musíte nástroj, který ukazuje návratový stavový kód protokolu HTTP z test adresy URL. Nejsou k dispozici celou řadu nástrojů, které ukazují, nezpracovaná odpověď HTTP.

* [Fiddler](http://www.telerik.com/fiddler)
* [Curl](https://curl.haxx.se/)
* [wget](http://gnuwin32.sourceforge.net/packages/wget.htm)

Navíc můžete použít síťové kartě Nástroje pro ladění F12 v Internet Exploreru zobrazíte odpovědi HTTP.

V tomto příkladu chceme se zobrazí odpověď z našich test adresy URL: http://watestsdp2008r2.cloudapp.net:80/Probe. Následující příklad PowerShell ukazuje problém.

```powershell
Invoke-WebRequest 'http://watestsdp2008r2.cloudapp.net/Probe' -MaximumRedirection 0 -ErrorAction SilentlyContinue | Select-Object StatusCode,StatusDescription
```

Příklad výstupu:

    StatusCode StatusDescription
    ---------- -----------------
           301 Moved Permanently

Všimněte si, že dostali jsme odpověď přesměrování. Jak uvádí všechny StatusCode dříve, než je 200 považují za chybu. Traffic Manager změní stav koncového bodu na Offline. Chcete-li problém vyřešit, zkontrolujte konfiguraci webu k zajištění, že může být vráceny správné StatusCode z cesty k testu. Překonfigurujte testu Traffic Manager tak, aby odkazoval na cestu, která vrátí hodnotu 200.

Pokud se váš test používá protokol HTTPS, musíte zakázat, aby nedocházelo k chybám protokolu SSL/TLS během svůj test ověřování certifikátů. Následující příkazy prostředí PowerShell zakázat ověření certifikátu pro aktuální relace prostředí PowerShell:

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

[O metodách směrování provozu Traffic Manager](traffic-manager-routing-methods.md)

[Co je Traffic Manageru](traffic-manager-overview.md)

[Cloud Services](http://go.microsoft.com/fwlink/?LinkId=314074)

[Azure Web Apps](https://azure.microsoft.com/documentation/services/app-service/web/)

[Operace v Traffic Manageru (referenční informace k rozhraní API REST)](http://go.microsoft.com/fwlink/?LinkId=313584)

[Rutiny Azure Traffic Manager][1]

[1]: https://msdn.microsoft.com/library/mt125941(v=azure.200).aspx
