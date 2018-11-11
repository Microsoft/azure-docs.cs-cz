---
title: Řešení potíží s degradovaný stav Azure Traffic Manageru
description: Řešení potíží s profily Traffic Manageru, když se zobrazí jako degradovaný stav.
services: traffic-manager
documentationcenter: ''
author: chadmath
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/03/2017
ms.author: genli
ms.openlocfilehash: 8690ffdca606bf45f306f9273441aaac52b385c8
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51241357"
---
# <a name="troubleshooting-degraded-state-on-azure-traffic-manager"></a>Řešení potíží s sníženém výkonu Azure Traffic Manageru

Tento článek popisuje postup řešení potíží s profil Azure Traffic Manageru, který se zobrazuje snížený výkon. Pro tento scénář vezměte v úvahu, že jste nakonfigurovali profil služby Traffic Manager odkazuje na některé z vašich cloudapp.net hostovaných služeb. Pokud se Traffic Manager zobrazuje **snížený** může být stav a stav jednoho nebo víc koncových bodů **snížený**:

![koncový bod degradovaný stav](./media/traffic-manager-troubleshooting-degraded/traffic-manager-degradedifonedegraded.png)

Pokud se Traffic Manager zobrazuje **neaktivní** stav, pak oba koncové body mohou být **zakázané**:

![Neaktivní stav služby Traffic Manager](./media/traffic-manager-troubleshooting-degraded/traffic-manager-inactive.png)

## <a name="understanding-traffic-manager-probes"></a>Sondy Principy Traffic Manageru

* Traffic Manager bude považovat za koncový bod bude ONLINE, pouze v případě, že test obdrží odpověď HTTP 200 zpět z cesty testu paměti. Jiné než 200 odpovědi je selhání.
* Přesměrování 30krát nezdaří, i v případě, že přesměrovaného URL vrací stav 200.
* U sondy protokolu HTTPs jsou ignorovány chyby certifikátů.
* Skutečný obsah cesta testu paměti nezáleží, tak dlouho, dokud se vrátí 200. Zjišťování adresy URL na některé statický obsah, jako je "/ favicon.ico" je běžná technika. Dynamický obsah, jako jsou stránky ASP nemusí vždy vrátit 200, i v případě, že aplikace je v pořádku.
* Osvědčeným postupem je nastavit cesty testu paměti na něco, co má dostatek logiku k určení, že lokalita je směrem nahoru nebo dolů. V předchozím příkladu, nastavením cestu na "/ favicon.ico", jsou pouze testování této w3wp.exe reaguje. Tento test nemusí znamenat, že vaše webová aplikace je v pořádku. Nastavit cestu k něco, jako je lepší volbou "/ Probe.aspx", který obsahuje logiku pro zjištění stavu lokality. Můžete například pomocí čítačů výkonu pro využití výkonu procesoru nebo určovat počet neúspěšných žádostí. Nebo vám může pokusu o přístup k prostředkům databáze a stavu relace, abyste měli jistotu, že webová aplikace funguje.
* Pokud jsou všechny koncové body v profilu degradovaný, pak Traffic Manager zpracovává všechny koncové body jako v pořádku a směruje provoz pro všechny koncové body. To zaručuje, že problémy s zjišťovací mechanismus výsledkem není úplné výpadku služby.

## <a name="troubleshooting"></a>Řešení potíží

Řešení potíží s selhání testu, je nutné nástroj, který zobrazuje návratový kód stavu HTTP z adresy URL testu. Nejsou k dispozici mnoho nástrojů, které můžete zobrazit nezpracovaná odpověď HTTP.

* [Fiddler](http://www.telerik.com/fiddler)
* [Curl](https://curl.haxx.se/)
* [wget](http://gnuwin32.sourceforge.net/packages/wget.htm)

Navíc můžete použít na síťové kartě ladicí nástroje F12 v aplikaci Internet Explorer k zobrazení odpovědi protokolu HTTP.

V tomto příkladu chceme vidět odpovědi z adresy URL pro naši testu: http://watestsdp2008r2.cloudapp.net:80/Probe. Následující příklad PowerShell ukazuje problém.

```powershell
Invoke-WebRequest 'http://watestsdp2008r2.cloudapp.net/Probe' -MaximumRedirection 0 -ErrorAction SilentlyContinue | Select-Object StatusCode,StatusDescription
```

Příklad výstupu:

    StatusCode StatusDescription
    ---------- -----------------
           301 Moved Permanently

Všimněte si, že dostali jsme odpověď přesměrování. Jak bylo uvedeno dříve, všechny StatusCode jiné než 200 je považován za neúspěšný. Traffic Manager změní stav koncového bodu na Offline. Chcete-li vyřešit tento problém, zkontrolujte konfiguraci webu a ujistěte se, že mohou být vráceny správné StatusCode z cesty testu paměti. Změnit konfiguraci sondy pro Traffic Manager tak, aby odkazoval na cestu, která vrací stav 200.

Pokud váš test je pomocí protokolu HTTPS, budete muset zakázat ověřování, aby nedocházelo k chybám protokolu SSL/TLS během testu certifikátů. Následující příkazy Powershellu zakázat ověřování certifikátu pro aktuální relace prostředí PowerShell:

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

[O metodách směrování provozu Traffic Manageru](traffic-manager-routing-methods.md)

[Co je Traffic Manager](traffic-manager-overview.md)

[Cloud Services](https://go.microsoft.com/fwlink/?LinkId=314074)

[Azure Web Apps](https://azure.microsoft.com/documentation/services/app-service/web/)

[Operace v Traffic Manageru (referenční informace k rozhraní API REST)](https://go.microsoft.com/fwlink/?LinkId=313584)

[Rutiny Azure Traffic Manageru][1]

[1]: https://docs.microsoft.com/powershell/module/azurerm.trafficmanager
