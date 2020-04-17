---
title: Příchozí a odchozí IP adresy
description: Zjistěte, jak se příchozí a odchozí IP adresy používají ve službě Azure App Service, když se mění a jak najít adresy pro vaši aplikaci.
ms.topic: article
ms.date: 06/06/2019
ms.custom: seodec18
ms.openlocfilehash: 8bcd80fde95e467513590f3ed09b1dadd2646aee
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537623"
---
# <a name="inbound-and-outbound-ip-addresses-in-azure-app-service"></a>Příchozí a odchozí IP adresy ve službě Azure App Service

[Azure App Service](overview.md) je služba s více [klienty,](environment/intro.md)s výjimkou prostředí služby App Service . Aplikace, které nejsou v prostředí služby App Service (ne v [izolované vrstvě)](https://azure.microsoft.com/pricing/details/app-service/)sdílejí síťovou infrastrukturu s jinými aplikacemi. V důsledku toho se příchozí a odchozí IP adresy aplikace mohou lišit a v určitých situacích se dokonce mohou změnit. 

[Prostředí služby App Service](environment/intro.md) používají vyhrazené síťové infrastruktury, takže aplikace spuštěné v prostředí služby App Service získají statické vyhrazené IP adresy pro příchozí i odchozí připojení.

## <a name="when-inbound-ip-changes"></a>Při příchozích změnách IP

Bez ohledu na počet instancí s horizontálním navýšením kapacity má každá aplikace jednu příchozí IP adresu. Příchozí adresa IP se může změnit, pokud provedete jednu z následujících akcí:

- Odstraňte aplikaci a znovu ji vytvořte v jiné skupině prostředků.
- Odstraňte poslední aplikaci v kombinaci skupiny prostředků _a_ oblasti a znovu ji vytvořte.
- Odstraňte existující vazbu TLS, například během obnovení certifikátu (viz [Obnovení certifikátu](configure-ssl-certificate.md#renew-certificate)).

## <a name="find-the-inbound-ip"></a>Vyhledání příchozí IP adresy

Stačí spustit následující příkaz v místním terminálu:

```bash
nslookup <app-name>.azurewebsites.net
```

## <a name="get-a-static-inbound-ip"></a>Získání statické příchozí IP adresy

Někdy můžete chtít vyhrazenou statickou IP adresu pro vaši aplikaci. Chcete-li získat statickou příchozí adresu IP, je třeba [zabezpečit vlastní doménu](configure-ssl-bindings.md#secure-a-custom-domain). Pokud k zabezpečení aplikace ve skutečnosti nepotřebujete funkci TLS, můžete pro tuto vazbu dokonce nahrát certifikát podepsaný svým držitelem. Ve vazbě TLS založené na protokolu IP je certifikát vázán na samotnou IP adresu, takže služba App Service zřídí statickou IP adresu, aby se to stalo. 

## <a name="when-outbound-ips-change"></a>Při změně odchozích IP služeb

Bez ohledu na počet instancí s horizontálním navýšením kapacity má každá aplikace v daném okamžiku nastavený počet odchozích IP adres. Jakékoli odchozí připojení z aplikace App Service, například do back-enddatabáze, používá jednu z odchozích IP adres jako původní IP adresu. Nemůžete předem vědět, kterou IP adresu bude daná instance aplikace používat k vytvoření odchozího připojení, takže vaše back-endová služba musí otevřít bránu firewall všem odchozím IP adresám vaší aplikace.

Sada odchozích IP adres pro vaši aplikaci se změní při škálování aplikace mezi nižšími úrovněmi **(Základní**, **Standardní**a **Premium)** a **úrovní Premium V2.**

Sadu všech možných odchozích IP adres, které vaše aplikace může používat, bez ohledu `possibleOutboundIpAddresses` na cenové úrovně, najdete tak, že vyhledáte vlastnost nebo v poli **Další odchozí IP adresy** v okně **Vlastnosti** na portálu Azure. Viz [Najít odchozí IP adresy](#find-outbound-ips).

## <a name="find-outbound-ips"></a>Najít odchozí IP adresy

Pokud chcete najít odchozí IP adresy, které vaše aplikace aktuálně používá na webu Azure Portal, klikněte v levé navigaci v aplikaci na **Vlastnosti.** Jsou uvedeny v poli **Odchozí adresy IP.**

Stejné informace najdete spuštěním následujícího příkazu v [prostředí Cloud Shell](../cloud-shell/quickstart.md).

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query outboundIpAddresses --output tsv
```

```azurepowershell
(Get-AzWebApp -ResourceGroup <group_name> -name <app_name>).OutboundIpAddresses
```

Pokud chcete najít _všechny_ možné odchozí IP adresy pro vaši aplikaci, bez ohledu na cenové úrovně, klikněte v levé navigaci v aplikaci na **Vlastnosti.** Jsou uvedeny v poli **Další odchozí adresy IP.**

Stejné informace najdete spuštěním následujícího příkazu v [prostředí Cloud Shell](../cloud-shell/quickstart.md).

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query possibleOutboundIpAddresses --output tsv
```

```azurepowershell
(Get-AzWebApp -ResourceGroup <group_name> -name <app_name>).PossibleOutboundIpAddresses
```

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak omezit příchozí provoz podle zdrojových IP adres.

> [!div class="nextstepaction"]
> [Statická omezení IP](app-service-ip-restrictions.md)
