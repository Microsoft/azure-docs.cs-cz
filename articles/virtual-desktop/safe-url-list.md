---
title: Požadovaný seznam adres URL pro virtuální počítač s Windows – Azure
description: Seznam adres URL, které musíte odblokovat, abyste měli jistotu, že vaše nasazení virtuálních klientů Windows funguje tak, jak je zamýšlené.
author: Heidilohr
ms.topic: conceptual
ms.date: 12/04/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 00ae761af44b9e6537149c96607c0ba00e6439c8
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/15/2021
ms.locfileid: "107514973"
---
# <a name="required-url-list"></a>Seznam požadovaných adres URL

Aby bylo možné nasadit a používat virtuální počítač s Windows, musíte odblokovat určité adresy URL, aby k nim měli přístup vaše virtuální počítače, aby k nim měli přístup kdykoli. V tomto článku jsou uvedené požadované adresy URL, které potřebujete odblokovat, aby funkce Virtual Desktop systému Windows fungovala správně. 

>[!IMPORTANT]
>Virtuální počítač s Windows nepodporuje nasazení, která blokují adresy URL uvedené v tomto článku.

## <a name="required-url-check-tool"></a>Požadovaný nástroj pro kontrolu adresy URL

Požadovaný nástroj pro kontrolu adresy URL ověří adresy URL a zobrazí, jestli jsou dostupné adresy URL, které musí virtuální počítač fungovat. V takovém případě nástroj zobrazí seznam nepřístupných adres URL, takže v případě potřeby je můžete odblokovat.

Je důležité mít na paměti následující skutečnosti:

- Pro nasazení v komerčních cloudech můžete použít jenom nástroj pro kontrolu požadované adresy URL.
- Požadovaný nástroj pro kontrolu adresy URL nemůže zkontrolovat adresy URL se zástupnými znaky, proto je třeba nejprve tyto adresy URL odblokovat.

### <a name="requirements"></a>Požadavky

K použití nástroje pro kontrolu požadované adresy URL potřebujete následující věci:

- Váš virtuální počítač musí mít rozhraní .NET 4.6.2 Framework.
- RDAgent verze 1.0.2944.400 nebo vyšší
- Soubor WVDAgentUrlTool.exe musí být ve stejné složce jako WVDAgentUrlTool.config soubor.

### <a name="how-to-use-the-required-url-check-tool"></a>Jak používat nástroj pro kontrolu požadované adresy URL

Použití nástroje pro kontrolu požadované adresy URL:

1. Na svém VIRTUÁLNÍm počítači otevřete příkazový řádek jako správce.
2. Spuštěním následujícího příkazu změňte adresář na stejnou složku jako agent sestavení:

    ```console
    cd C:\Program Files\Microsoft RDInfra\RDAgent_1.0.2944.1200
    ```

3. Spusťte následující příkaz:

    ```console
    WVDAgentUrlTool.exe
    ```
 
4. Po spuštění souboru se zobrazí seznam dostupných a nepřístupných adres URL.

    Například na následujícím snímku obrazovky vidíte situaci, kdy potřebujete odblokovat dvě požadované adresy URL, které se nepoužívají jako zástupné znaky:

    > [!div class="mx-imgBorder"]
    > ![Snímek výstupu nepřístupných adres URL.](media/noaccess.png)
    
    V takovém případě by měl výstup vypadat jako po odblokování všech vyžadovaných adres URL, které nepoužívají zástupné znaky:

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky s přístupným výstupem adres URL](media/access.png)

## <a name="virtual-machines"></a>Virtuální počítače

Virtuální počítače Azure, které vytvoříte pro virtuální počítač s Windows, musí mít přístup k následujícím adresám URL v komerčním cloudu Azure:

|Adresa|Odchozí port TCP|Účel|Značka služby|
|---|---|---|---|
|*. wvd.microsoft.com|443|Provoz služby|WindowsVirtualDesktop|
|gcs.prod.monitoring.core.windows.net|443|Přenosy agenta|AzureCloud|
|production.diagnostics.monitoring.core.windows.net|443|Přenosy agenta|AzureCloud|
|* xt.blob.core.windows.net|443|Přenosy agenta|AzureCloud|
|* eh.servicebus.windows.net|443|Přenosy agenta|AzureCloud|
|* xt.table.core.windows.net|443|Přenosy agenta|AzureCloud|
|* xt.queue.core.windows.net|443|Přenosy agenta|AzureCloud|
|catalogartifact.azureedge.net|443|Azure Marketplace|AzureCloud|
|kms.core.windows.net|1688|Aktivace Windows|Internet|
|mrsglobalsteus2prod.blob.core.windows.net|443|Aktualizace zásobníku agenta a SXS|AzureCloud|
|wvdportalstorageblob.blob.core.windows.net|443|Podpora Azure Portal|AzureCloud|
| 169.254.169.254 | 80 | [Koncový bod služby metadat instance Azure](../virtual-machines/windows/instance-metadata-service.md) | – |
| 168.63.129.16 | 80 | [Monitorování stavu hostitele relace](../virtual-network/network-security-groups-overview.md#azure-platform-considerations) | – |

>[!IMPORTANT]
>Virtuální počítač s Windows teď podporuje značku plně kvalifikovaného názvu domény. Další informace najdete v tématu [použití Azure firewall k ochraně nasazení virtuálních ploch](../firewall/protect-windows-virtual-desktop.md)v systému Windows.
>
>Pro předcházení problémům se službou doporučujeme použít místo adres URL značky plně kvalifikovaného názvu domény nebo značky služby. Uvedené adresy URL a značky odpovídají pouze webům a prostředkům virtuálních ploch systému Windows. Neobsahují adresy URL pro jiné služby, například Azure Active Directory.

Virtuální počítače Azure, které vytvoříte pro virtuální počítač s Windows, musí mít přístup k následujícím adresám URL v cloudu Azure Government:

|Adresa|Odchozí port TCP|Účel|Značka služby|
|---|---|---|---|
|*. wvd.microsoft.us|443|Provoz služby|WindowsVirtualDesktop|
|gcs.monitoring.core.usgovcloudapi.net|443|Přenosy agenta|AzureCloud|
|monitoring.core.usgovcloudapi.net|443|Přenosy agenta|AzureCloud|
|fairfax.warmpath.usgovcloudapi.net|443|Přenosy agenta|AzureCloud|
|* xt.blob.core.usgovcloudapi.net|443|Přenosy agenta|AzureCloud|
|*. servicebus.usgovcloudapi.net|443|Přenosy agenta|AzureCloud|
|* xt.table.core.usgovcloudapi.net|443|Přenosy agenta|AzureCloud|
|Kms.core.usgovcloudapi.net|1688|Aktivace Windows|Internet|
|mrsglobalstugviffx.blob.core.usgovcloudapi.net|443|Aktualizace zásobníku agenta a SXS|AzureCloud|
|wvdportalstorageblob.blob.core.usgovcloudapi.net|443|Podpora Azure Portal|AzureCloud|
| 169.254.169.254 | 80 | [Koncový bod služby metadat instance Azure](../virtual-machines/windows/instance-metadata-service.md) | – |
| 168.63.129.16 | 80 | [Monitorování stavu hostitele relace](../virtual-network/network-security-groups-overview.md#azure-platform-considerations) | – |

Následující tabulka uvádí volitelné adresy URL, ke kterým můžou mít virtuální počítače Azure přístup:

|Adresa|Odchozí port TCP|Účel|Gov Azure|
|---|---|---|---|
|*.microsoftonline.com|443|Ověřování pro online služby Microsoftu|login.microsoftonline.us|
|*. events.data.microsoft.com|443|Služba telemetrie|Žádné|
|www.msftconnecttest.com|443|Zjistí, jestli je operační systém připojený k Internetu.|Žádné|
|*. prod.do.dsp.mp.microsoft.com|443|Windows Update|Žádné|
|login.windows.net|443|Přihlaste se ke službám Microsoft Online Services Microsoft 365|login.microsoftonline.us|
|*. sfx.ms|443|Aktualizace klientského softwaru OneDrivu|oneclient.sfx.ms|
|*. digicert.com|443|Kontrola odvolání certifikátu|Žádné|
|*. azure-dns.com|443|Azure DNS rozlišení|Žádné|
|*. azure-dns.net|443|Azure DNS rozlišení|Žádné|

>[!NOTE]
>Virtuální klient Windows aktuálně neobsahuje seznam rozsahů IP adres, které můžete odblokovat, aby se povolil síťový provoz. V tuto chvíli podporujeme jenom odblokování specifických adres URL.
>
>Pokud používáte bránu firewall nové generace (NGFW), budete muset použít dynamický seznam, který se konkrétně provede pro IP adresy Azure, abyste se ujistili, že se můžete připojit.
>
>Seznam bezpečných adres URL pro Office, včetně požadovaných adres URL souvisejících s Azure Active Directory, najdete v tématu [adresy URL a rozsahy IP adres pro office 365](/office365/enterprise/urls-and-ip-address-ranges).
>
>Pro adresy URL, které se týkají provozu služby, je nutné použít zástupný znak (*). Pokud nechcete používat * pro přenosy související s agentem, vyhledáte adresy URL bez zástupných znaků:
>
>1. Zaregistrujte virtuální počítače do fondu hostitelů virtuálních počítačů s Windows.
>2. Otevřete **Prohlížeč událostí** a potom v **systému Windows protokoly**  >  **aplikace**  >  **WVD-agent** a vyhledejte událost s ID 3701.
>3. Odblokujte adresy URL, které najdete v části ID události 3701. Adresy URL v rámci události s ID 3701 jsou specifické pro oblast. Pro každou oblast, ve které chcete nasadit virtuální počítače, budete muset zopakovat proces odblokování s příslušnými adresami URL.

## <a name="remote-desktop-clients"></a>Klienti služby Vzdálená plocha

Všichni klienti vzdálené plochy, které použijete, musí mít přístup k následujícím adresám URL:

|Adresa|Odchozí port TCP|Účel|Klient (y)|Gov Azure|
|---|---|---|---|---|
|*. wvd.microsoft.com|443|Provoz služby|Vše|*. wvd.microsoft.us|
|*.servicebus.windows.net|443|Řešení potíží s daty|Vše|*. servicebus.usgovcloudapi.net|
|go.microsoft.com|443|Microsoft odkazy fwlinks|Vše|Žádné|
|aka.ms|443|Zkrácení adresy URL Microsoftu|Vše|Žádné|
|docs.microsoft.com|443|Dokumentace|Vše|Žádné|
|privacy.microsoft.com|443|Prohlášení o ochraně osobních údajů|Vše|Žádné|
|query.prod.cms.rt.microsoft.com|443|Aktualizace klienta|Windows Desktop|Žádné|

>[!IMPORTANT]
>Otevírání těchto adres URL je nezbytné pro spolehlivé klientské prostředí. Blokování přístupu k těmto adresám URL není podporováno a bude mít vliv na funkčnost služby.
>
>Tyto adresy URL odpovídají pouze klientským webům a prostředkům. Tento seznam neobsahuje adresy URL pro jiné služby, například Azure Active Directory. Adresy URL Azure Active Directory najdete pod položkou ID 56 v [adresách URL a rozsahech IP adres Office 365](/office365/enterprise/urls-and-ip-address-ranges#microsoft-365-common-and-office-online).
