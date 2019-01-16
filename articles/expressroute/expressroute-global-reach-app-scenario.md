---
title: Azure ExpressRoute globální oslovit scénář aplikací | Dokumentace Microsoftu
description: Tato stránka obsahuje scénáři aplikace pro globální dosah.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: expressroute,global reach
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/14/2019
ms.author: rambala
ms.openlocfilehash: 5174baae40d7f14720affe90f702c024541a5745
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2019
ms.locfileid: "54332834"
---
# <a name="expressroute-global-reach-application-scenario"></a>Scénář aplikací ExpressRoute globální dosah

Další informace o globální dosah ExpressRoute najdete v tématu [ExpressRoute globální dosah][Global Reach]. V tomto článku se teď provede scénáři aplikace, porovnat ExpressRoute globální dosah řešení na pár dalších řešení, nakonfigurujte globální dosah ukázkový scénář a ověřte připojení. 

##<a name="application-scenario"></a>Scénář aplikací

Společnost Fabrikam, Inc. má velký fyzickou přítomnost a nasazení Azure v oblasti východní USA. Společnost Fabrikam má back-end připojení mezi místní a nasazení Azure přes ExpressRoute. Contoso Ltd. je prezentace a nasazení Azure v oblasti západní USA. Contoso má back-end připojení mezi místní a nasazení Azure přes ExpressRoute.  

Společnost Fabrikam, Inc. získá Contoso Ltd. Po spojení Fabrikam chce propojení sítí. Scénář znázorňuje následující obrázek:

 [![1]][1]

Přerušované šipky uprostřed na výše uvedeném obrázku označují požadovaná síťová propojení. V následující tabulce jsou uvedeny směrovací tabulky primární privátního partnerského vztahu pro ExpressRoute Contoso Ltd. před sloučením.

[![2]][2]

V následující tabulce jsou uvedeny směrovací tabulky primární privátní partnerský vztah ExpressRoute Fabrikam Inc. před sloučením.

[![3]][3]

## <a name="traditional-solutions"></a>Tradiční řešení

### <a name="option-1-interconnect-on-premises-networks"></a>Option 1: Propojení místních sítí

Následující obrázek znázorňuje tuto možnost. Jak je znázorněno, můžete propojení dvou místních sítí pomocí připojení VPN site-to-site nebo jiné možnosti širokopásmové připojení a spravovat všechny směrování mezi dvěma entitami. 

[![4]][4]

Tuto možnost má následující nedostatky:

- Vynutíte komunikaci mezi místní Azure pro nasazení přes neoptimální směrování.
- Máte odepření výhodou páteřní síti Microsoftu pro komunikaci mezi místní vysokou dostupnost.
- Přenášíte plnou odpovědnost směrování pro komunikaci mezi místní.

### <a name="option-2-expressroute-cross-connectivity-and-interconnect-on-premises-networks"></a>Option 2: Křížové připojení ExpressRoute a propojení místních sítí

Následující obrázek znázorňuje tuto možnost.

[![5]][5]

Jak je znázorněno na obrázku výše, můžete také navázat připojení mezi okruhy ExpressRoute mezi místní virtuálními sítěmi. Mezi místní připojení mezi virtuální sítí s okruhy ExpressRoute by povolit následující komunikace:

- USA – západ nebo USA – východ virtuálních sítích komunikovat v uvedeném pořadí s Fabrikam/Contoso s místními sítěmi.
- USA – západ virtuální síti komunikovat s virtuální sítí oblasti USA – východ.

Propojení mezi dvěma místními sítěmi stále se vyžaduje pro místní sítě ke komunikaci mezi sebou.

Tato možnost umožňuje komunikaci mezi místní Azure pro privátní nasazení svézt přes páteřní infrastrukturu Microsoftu na a komunikaci mezi místní sítí realizaci v externí síti. Hlavní nevýhodou řešení je však, že je potřeba vytvořit více mezi místní připojení, které zkomplikovat údržby a řešení potíží. Také možnost nebude umožňují využívat vysoce dostupnou globální páteřní infrastrukturu Microsoftu pro komunikaci mezi dvěma místními sítěmi.

### <a name="option-3-vnet-peering-and-interconnect-on-premises-networks"></a>Možnost 3: Partnerský vztah virtuální sítě a propojení místních sítí

Následující obrázek znázorňuje tuto možnost. Možnost používá VNet peering pro komunikaci mezi regionální virtuální síť. Možnost, jak je znázorněno, je neúplná jako proto se nezabývá virtuálních sítí mezi zónami komunikaci v místním (označená přes dva řádky tečkované šipku uprostřed). Použít místní – místní připojení (viz možnost 1) nebo ExpressRoute napříč připojením (viz možnost 2) pro mezi zónami s místními komunikace.

[![6]][6]

Tato možnost poskytuje optimální směrování pro komunikaci mezi regionální virtuální síť. Vrátí krátký, pokud společnost Fabrikam nebo Contoso má složitější nasazení Azure, jako je model virtuální sítě centra s paprsky. Také jako předchozí dvě možnosti, tato možnost není vám umožní využít výhod vysoce dostupnou globální páteřní infrastrukturu Microsoftu pro komunikaci mezi dvěma místními sítěmi.

## <a name="global-reach"></a>Globální dosah

Globální dosah ExpressRoute propojí soukromého partnerského vztahu okruhů ExpressRoute, jak je znázorněno na následujícím obrázku:

[![7]][7]

Konfigurace globální dosah mezi dva okruhy ExpressRoute umožňuje soukromou komunikaci mezi dvěma místními sítěmi a nasazení Azure ve dvou oblastech. Díky tomu se globální dosah splňuje požadovanou komunikaci (indikován přerušované čáry v první obrázek v tomto článku) v páteřní síti Microsoft.

### <a name="configure-global-reach"></a>Nakonfigurujte globální dosah

Další informace o konfiguraci ExpressRoute globální dosah, najdete v článku [nakonfigurovat globální dosah][Configure Global Reach]. 

Protože společnost Fabrikam, Inc. a Contoso Ltd. Azure jako samostatné společnosti zařazený, nemá okruhů ExpressRoute z obou společností jsou ve dvou různých předplatných Azure. Pokud chcete vytvořit globální dosah napříč předplatným, budete muset vytvořit povolení v okruhu ExpressRoute, které patří společnosti Contoso Ltd. a jejich předávání společnosti Fabrikam, Inc. Okruh ExpressRoute.


Vytvořit autorizaci pro okruh ExpressRoute společnosti Contoso, prvním přihlášení do účtu Azure společnosti Contoso a vyberte odpovídající předplatné (Pokud máte více předplatných). Příkazy prostředí PowerShell pro tyto kroky jsou:

```powershell
Connect-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
```
Kroky pro vytvoření autorizace okruhu ExpressRoute jsou uvedeny níže:

```powershell
$ckt_2 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
Add-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $ckt_2 -Name "Name_for_auth_key"
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_2
```

Výstup "Set-AzureRmExpressRouteCircuit" se zobrazí seznam ExpressRouteCircuit. Poznamenejte si ID privátní partnerský vztah a autorizační klíč, který by byly uvedeny na konci seznamu. Prohlédněte si příkladu Powershellu výstup fragment kódu níže:

```powershell
ServiceKey                       : <serviceKey>
Peerings                         : [
                                     {
                                       "Name": "AzurePrivatePeering",
                                       "Etag": "W/\"78dfeeae-0485-4cda-b5aa-99e780589677\"",
                                       "Id": "/subscriptions/<subscriptionId>/resourceGroups/SEA-Cust11/providers/Microsoft.Network/expressRouteCircuits/SEA-Cust11-ER/peerings/AzurePrivatePeering",
                                       "PeeringType": "AzurePrivatePeering",
                                       "State": "Enabled",
                                       "AzureASN": 12076,
                                       "PeerASN": 65020,
                                       "PrimaryPeerAddressPrefix": "192.168.11.16/30",
                                       "SecondaryPeerAddressPrefix": "192.168.11.20/30",
                                       "PrimaryAzurePort": "",
                                       "SecondaryAzurePort": "",
                                       "VlanId": 110,
                                       "ProvisioningState": "Succeeded",
                                       "GatewayManagerEtag": "4",
                                       "LastModifiedBy": "Customer",
                                       "Connections": []
                                     }
                                   ]
Authorizations                   : [
                                     {
                                       "Name": "Auth4-ASH-Cust11",
                                       "Etag": "W/\"78dfeeae-0485-4cda-b5aa-99e780589677\"",
                                       "Id": "/subscriptions/<subscriptionId>/resourceGroups/SEA-Cust11/providers/Microsoft.Network/expressRouteCircuits/SEA-Cust11-ER/authorizations/Auth4-ASH-Cust11",
                                       "AuthorizationKey": "<authorizationKey>",
                                       "AuthorizationUseStatus": "Used",
                                       "ProvisioningState": "Succeeded"
                                     }
                                   ]
AllowClassicOperations           : False
GatewayManagerEtag               : 4
```

S partnerským vztahem ID a autorizační klíč můžete vytvořit globální dosah v rámci společnosti Fabrikam okruh ExpressRoute. Přihlaste se k účtu Azure společnosti Fabrikam. Pokud existuje více než jedno předplatné, vyberte odpovídající předplatné.

Globální dosah vytvoří sadu redundantní připojení typu point-to-point mezi dvě dvojice směrovači MSEE. Pro připojení typu point-to-point dva, je třeba zadat minimální velikostí/29 předponu adresy (například spuštěné použijeme 192.168.11.64/29). Chcete-li vytvořit připojení globální dosah, použijte následující příkazy:

```powershell
$ckt_1 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
Add-AzureRmExpressRouteCircuitConnectionConfig -Name "Your_connection_name" -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering "circuit_2_private_peering_id" -AddressPrefix "__.__.__.__/29" -AuthorizationKey "########-####-####-####-############"
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

Po spuštění výše uvedených příkazů bude trvat několik minut vytvořit redundantní připojení globální dosah.

### <a name="verify-global-reach"></a>Ověřte globálním dosahem

V následující tabulce jsou uvedeny směrovací tabulky primární privátního partnerského vztahu pro ExpressRoute Contoso Ltd. Po konfiguraci globální dosah.

[![8]][8]

V následující tabulce jsou uvedeny směrovací tabulky primární privátní partnerský vztah ExpressRoute Fabrikam Inc. Po konfiguraci globální dosah.

[![9]][9]

V výše uvedeným tabulkám vidíme všechny očekávaných cílových předpon "Síť". jsou uvedeny příslušné "další segment Směrování.

Výše najdete v okně "Get směrovací tabulku", který je přístupný na webu Azure Portal v části "Privátní partnerský vztah" okruhu ExpressRoute. Můžete také zařadit ExpressRoute směrovací tabulky pomocí následujícího příkazu Powershellu:

```powershell
Get-AzExpressRouteCircuitRouteTable -DevicePath 'primary' -ExpressRouteCircuitName "Your_circuit_name" -PeeringType AzurePrivatePeering -ResourceGroupName "Your_resource_group"
```

Směrovací tabulky sekundární MSEE zobrazíte nahraďte primární s klíčovým slovem "sekundární" v předchozím příkazu.

Můžeme také ověřit připojení k rovině dat pomocí příkazu ping jinou cílovou z různých sítí. Následující tři příkazy ping ověřte připojení roviny dat k místní síti Contoso Contoso virtuální síť Azure, a virtuální sítí VNet Azure Fabrikam ze společnosti Fabrikam místní síť.


    C:\Users\FabrikamUser>ping 10.1.11.10
    
    Pinging 10.1.11.10 with 32 bytes of data:
    Reply from 10.1.11.10: bytes=32 time=69ms TTL=122
    Reply from 10.1.11.10: bytes=32 time=69ms TTL=122
    Reply from 10.1.11.10: bytes=32 time=69ms TTL=122
    Reply from 10.1.11.10: bytes=32 time=69ms TTL=122
    
    Ping statistics for 10.1.11.10:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 69ms, Maximum = 69ms, Average = 69ms
    
    C:\Users\FabrikamUser>ping 10.17.11.4
    
    Pinging 10.17.11.4 with 32 bytes of data:
    Reply from 10.17.11.4: bytes=32 time=82ms TTL=124
    Reply from 10.17.11.4: bytes=32 time=81ms TTL=124
    Reply from 10.17.11.4: bytes=32 time=76ms TTL=124
    Reply from 10.17.11.4: bytes=32 time=76ms TTL=124
    
    Ping statistics for 10.17.11.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 76ms, Maximum = 82ms, Average = 78ms
    
    C:\Users\FabrikamUser>ping 10.10.11.4
    
    Pinging 10.10.11.4 with 32 bytes of data:
    Reply from 10.10.11.4: bytes=32 time=3ms TTL=125
    Reply from 10.10.11.4: bytes=32 time=3ms TTL=125
    Reply from 10.10.11.4: bytes=32 time=2ms TTL=125
    Reply from 10.10.11.4: bytes=32 time=3ms TTL=125
    
    Ping statistics for 10.10.11.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 2ms, Maximum = 3ms, Average = 2ms


Následující dva příkazy ping ověřte připojení roviny dat k virtuální síti Azure společnosti Contoso a Fabrikam virtuální síť Azure v místní síti Contoso.

    C:\Users\ContosoUser>ping 10.17.11.4
    
    Pinging 10.17.11.4 with 32 bytes of data:
    Reply from 10.17.11.4: bytes=32 time=6ms TTL=125
    Reply from 10.17.11.4: bytes=32 time=5ms TTL=125
    Reply from 10.17.11.4: bytes=32 time=5ms TTL=125
    Reply from 10.17.11.4: bytes=32 time=5ms TTL=125
    
    Ping statistics for 10.17.11.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 5ms, Maximum = 6ms, Average = 5ms
    
    C:\Users\ContosoUser>ping 10.10.11.4
    
    Pinging 10.10.11.4 with 32 bytes of data:
    Reply from 10.10.11.4: bytes=32 time=73ms TTL=124
    Reply from 10.10.11.4: bytes=32 time=73ms TTL=124
    Reply from 10.10.11.4: bytes=32 time=74ms TTL=124
    Reply from 10.10.11.4: bytes=32 time=73ms TTL=124
    
    Ping statistics for 10.10.11.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 73ms, Maximum = 74ms, Average = 73ms

Následující příkaz ping ověří připojení roviny dat k virtuální síti Contoso Azure z virtuální sítě Azure společnosti Fabrikam.

    C:\Users\FabrikamUser>ping 10.17.11.4
    
    Pinging 10.17.11.4 with 32 bytes of data:
    Reply from 10.17.11.4: bytes=32 time=77ms TTL=125
    Reply from 10.17.11.4: bytes=32 time=77ms TTL=125
    Reply from 10.17.11.4: bytes=32 time=76ms TTL=125
    Reply from 10.17.11.4: bytes=32 time=78ms TTL=125
    
    Ping statistics for 10.17.11.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 76ms, Maximum = 78ms, Average = 77ms

## <a name="optimization-for-latency-sensitive-traffic"></a>Optimalizace pro citlivé čekací doba provozu

Globální dosah směruje provoz přes zařízení Microsoft Edge. Pro konkrétní scénář v tomto článku můžete dosáhnout více optimální směrování mezi dvěma virtuálními sítěmi povolením partnerský vztah mezi nimi. Podobně můžete dosáhnout více optimální směrování mezi dvěma místními sítěmi přes poskytovatele služeb, který může poskytnout sítě WAN připojení mezi lokalitami. V takových případech můžete použít globální dosah směrování jako selhání back možnost pro tato připojení. 

## <a name="next-steps"></a>Další postup

Globální dosah nasazení na základě jednotlivé země. Pokud globální dosah je dostupný v zemích, které mají najdete v tématu [ExpressRoute globální dosah][Global Reach].

<!--Image References-->
[1]: ./media/expressroute-globalreach-applicationscenario/PreMergerScenario.png "scénář aplikace"
[2]: ./media/expressroute-globalreach-applicationscenario/ContosoExR-RT-Premerger.png "trasy Contoso ExpressRoute tabulky před spojení"
[3]: ./media/expressroute-globalreach-applicationscenario/FabrikamExR-RT-Premerger.png "trasy Fabrikam ExpressRoute tabulky před spojení"
[4]: ./media/expressroute-globalreach-applicationscenario/S2SVPN-Solution.png "propojovacích sítích na pracovišti"
[5]: ./media/expressroute-globalreach-applicationscenario/ExR-XConnect-Solution.png "připojení ExpressRoute pro různé"
[6]: ./media/expressroute-globalreach-applicationscenario/VNet-peering-Solution.png "VNet peering"
[7]: ./media/expressroute-globalreach-applicationscenario/GlobalReach-Solution.png "globálním dosahem"
[8]: ./media/expressroute-globalreach-applicationscenario/ContosoExR-RT-Postmerger.png "trasy Contoso ExpressRoute tabulku s globální dosah"
[9]: ./media/expressroute-globalreach-applicationscenario/FabrikamExR-RT-Postmerger.png "trasy Fabrikam ExpressRoute tabulku s globální dosah"

<!--Link References-->
[Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-global-reach
[Configure Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-set-global-reach





