---
title: Seznam bezpečných adres URL pro virtuální počítače s Windows – Azure
description: Seznam adres URL, které byste měli odblokovat, aby bylo zajištěno, že nasazení virtuálních klientů Windows funguje tak, jak má.
author: Heidilohr
ms.topic: conceptual
ms.date: 08/12/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: f9f68d3734cd7de83a2ddd376caefa410c619d61
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89291105"
---
# <a name="safe-url-list"></a>Seznam bezpečných adres URL

Budete muset odblokovat určité adresy URL, aby nasazení virtuálních klientů Windows fungovalo správně. Tento článek uvádí tyto adresy URL, takže víte, které z nich jsou bezpečné.

## <a name="virtual-machines"></a>Virtuální počítače

Virtuální počítače Azure, které vytvoříte pro virtuální počítače s Windows, musí mít přístup k následujícím adresám URL:

|Adresa|Odchozí port TCP|Účel|Značka služby|
|---|---|---|---|
|*. wvd.microsoft.com|443|Provoz služby|WindowsVirtualDesktop|
|mrsglobalsteus2prod.blob.core.windows.net|443|Aktualizace zásobníku agenta a SXS|AzureCloud|
|*.core.windows.net|443|Přenosy agenta|AzureCloud|
|*.servicebus.windows.net|443|Přenosy agenta|AzureCloud|
|gcs.prod.monitoring.core.windows.net|443|Přenosy agenta|AzureCloud|
|catalogartifact.azureedge.net|443|Azure Marketplace|AzureCloud|
|kms.core.windows.net|1688|Aktivace Windows|Internet|
|wvdportalstorageblob.blob.core.windows.net|443|Podpora Azure Portal|AzureCloud|
| 169.254.169.254 | 80 | [Koncový bod služby metadat instance Azure](../virtual-machines/windows/instance-metadata-service.md) | Není k dispozici |
| 168.63.129.16 | 80 | [Monitorování stavu hostitele relace](../virtual-network/security-overview.md#azure-platform-considerations) | Není k dispozici |

>[!IMPORTANT]
>Virtuální počítač s Windows teď podporuje značku plně kvalifikovaného názvu domény. Další informace najdete v tématu [použití Azure firewall k ochraně nasazení virtuálních ploch](../firewall/protect-windows-virtual-desktop.md)v systému Windows.
>
>Pro předcházení problémům se službou doporučujeme použít místo adres URL značky plně kvalifikovaného názvu domény nebo značky služby. Uvedené adresy URL a značky odpovídají pouze webům a prostředkům virtuálních ploch systému Windows. Neobsahují adresy URL pro jiné služby, například Azure Active Directory.

Následující tabulka uvádí volitelné adresy URL, ke kterým můžou mít virtuální počítače Azure přístup:

|Adresa|Odchozí port TCP|Účel|Značka služby|
|---|---|---|---|
|*.microsoftonline.com|443|Ověřování pro online služby Microsoftu|Žádné|
|*. events.data.microsoft.com|443|Služba telemetrie|Žádné|
|www.msftconnecttest.com|443|Zjistí, jestli je operační systém připojený k Internetu.|Žádné|
|*. prod.do.dsp.mp.microsoft.com|443|Windows Update|Žádné|
|login.windows.net|443|Přihlaste se ke službám Microsoft Online Services Microsoft 365|Žádné|
|*. sfx.ms|443|Aktualizace klientského softwaru OneDrivu|Žádné|
|*. digicert.com|443|Ověření odvolání certifikátu|Žádné|

>[!NOTE]
>Virtuální klient Windows aktuálně neobsahuje seznam rozsahů IP adres, které můžete odblokovat, aby se povolil síťový provoz. V tuto chvíli podporujeme jenom odblokování specifických adres URL.
>
>Seznam bezpečných adres URL pro Office, včetně požadovaných adres URL souvisejících s Azure Active Directory, najdete v tématu [adresy URL a rozsahy IP adres pro office 365](/office365/enterprise/urls-and-ip-address-ranges).
>
>Pro adresy URL, které se týkají provozu služby, je nutné použít zástupný znak (*). Pokud nechcete používat * pro přenosy související s agentem, vyhledáte adresy URL bez zástupných znaků:
>
>1. Zaregistrujte virtuální počítače do fondu hostitelů virtuálních počítačů s Windows.
>2. Otevřete **Prohlížeč událostí**a potom v **systému Windows protokoly**  >  **aplikace**  >  **WVD-agent** a vyhledejte událost s ID 3701.
>3. Odblokujte adresy URL, které najdete v části ID události 3701. Adresy URL v rámci události s ID 3701 jsou specifické pro oblast. Pro každou oblast, ve které chcete nasadit virtuální počítače, budete muset zopakovat proces odblokování s příslušnými adresami URL.

## <a name="remote-desktop-clients"></a>Klienti služby Vzdálená plocha

Všichni klienti vzdálené plochy, které použijete, musí mít přístup k následujícím adresám URL:

|Adresa|Odchozí port TCP|Účel|Klient (y)|
|---|---|---|---|
|*. wvd.microsoft.com|443|Provoz služby|Vše|
|*.servicebus.windows.net|443|Řešení potíží s daty|Vše|
|go.microsoft.com|443|Microsoft odkazy fwlinks|Vše|
|aka.ms|443|Zkrácení adresy URL Microsoftu|Vše|
|docs.microsoft.com|443|Dokumentace|Vše|
|privacy.microsoft.com|443|Prohlášení o ochraně osobních údajů|Vše|
|query.prod.cms.rt.microsoft.com|443|Aktualizace klienta|Windows Desktop|

>[!IMPORTANT]
>Otevírání těchto adres URL je nezbytné pro spolehlivé klientské prostředí. Blokování přístupu k těmto adresám URL není podporováno a bude mít vliv na funkčnost služby.
>
>Tyto adresy URL odpovídají pouze klientským webům a prostředkům. Tento seznam neobsahuje adresy URL pro jiné služby, například Azure Active Directory. Adresy URL Azure Active Directory najdete pod položkou ID 56 v [adresách URL a rozsahech IP adres Office 365](/office365/enterprise/urls-and-ip-address-ranges#microsoft-365-common-and-office-online).
