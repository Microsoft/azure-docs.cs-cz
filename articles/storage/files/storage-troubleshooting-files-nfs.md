---
title: Řešení potíží s sdílenými složkami Azure NFS – soubory Azure
description: Vyřešte potíže s sdílenými složkami souborů Azure NFS.
author: jeffpatt24
ms.service: storage
ms.topic: troubleshooting
ms.date: 09/15/2020
ms.author: jeffpatt
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: 661cfd5bb410a714bc42e0cd9676ac2ec08f8a45
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90708693"
---
# <a name="troubleshoot-azure-nfs-file-shares"></a>Řešení potíží s sdílenými složkami Azure NFS

V tomto článku jsou uvedené některé běžné problémy související se sdílenými složkami souborů Azure NFS. Poskytuje možné příčiny a alternativní řešení, když dojde k těmto potížím.

## <a name="unable-to-create-an-nfs-share"></a>Nelze vytvořit sdílenou složku NFS.

### <a name="cause-1-subscription-is-not-enabled"></a>Příčina 1: předplatné není povolené.

Vaše předplatné možná není zaregistrované ve verzi Azure Files NFS Preview. K povolení této funkce budete muset spustit několik dalších rutin buď z Cloud Shell, nebo z místního terminálu.

> [!NOTE]
> Možná budete muset počkat až 30 minut, než se registrace dokončí.


#### <a name="solution"></a>Řešení

Pomocí následujícího skriptu Zaregistrujte funkci a poskytovatele prostředků, nahraďte `<yourSubscriptionIDHere>` před spuštěním skriptu:

```azurepowershell
Connect-AzAccount

#If your identity is associated with more than one subscription, set an active subscription
$context = Get-AzSubscription -SubscriptionId <yourSubscriptionIDHere>
Set-AzContext $context

Register-AzProviderFeature -FeatureName AllowNfsFileShares - ProviderNamespace Microsoft.Storage

Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

### <a name="cause-2-unsupported-storage-account-settings"></a>Příčina 2: nepodporované nastavení účtu úložiště

NFS je k dispozici jenom pro účty úložiště s následující konfigurací:

- Úroveň Premium
- Druh účtu – úložiště
- Redundance – LRS
- Oblasti – Východní USA, Východní USA 2, Velká Británie – jih, jihovýchodní Asie

#### <a name="solution"></a>Řešení

Postupujte podle pokynů v našem článku: [jak vytvořit sdílenou SLOŽKU NFS](storage-files-how-to-create-nfs-shares.md).

### <a name="cause-3-the-storage-account-was-created-prior-to-registration-completing"></a>Příčina 3: účet úložiště se vytvořil před dokončením registrace.

Aby účet úložiště mohl používat tuto funkci, je nutné ji vytvořit, jakmile předplatné dokončí registraci pro systém souborů NFS. Dokončení registrace může trvat až 30 minut.

#### <a name="solution"></a>Řešení

Po dokončení registrace postupujte podle pokynů v našem článku: [jak vytvořit sdílenou SLOŽKU NFS](storage-files-how-to-create-nfs-shares.md).

## <a name="cannot-connect-to-or-mount-an-azure-nfs-file-share"></a>Nejde se připojit ke sdílené složce Azure NFS a připojit ji.

### <a name="cause-1-request-originates-from-a-client-in-an-untrusted-networkuntrusted-ip"></a>Příčina 1: požadavek pochází z klienta v nedůvěryhodné síti/nedůvěryhodné IP adrese.

Na rozdíl od protokolu SMB nemá systém souborů NFS ověřování uživatelů. Ověřování pro sdílenou složku vychází z konfigurace pravidla zabezpečení sítě. Vzhledem k tomu, že pro sdílenou složku systému souborů NFS jsou navázána pouze zabezpečená připojení, je nutné použít koncový bod služby nebo privátní koncové body. Pro přístup ke sdíleným složkám z místního prostředí kromě privátních koncových bodů musíte nastavit VPN nebo ExpressRoute. IP adresy přidané do seznamu povolených adres účtu úložiště pro bránu firewall se ignorují. K nastavení přístupu ke sdílené složce systému souborů NFS je nutné použít jednu z následujících metod:


- [Koncový bod služby](storage-files-networking-endpoints.md#restrict-public-endpoint-access)
    - Přístupné veřejným koncovým bodem
    - K dispozici pouze ve stejné oblasti.
    - VNet peering neposkytne přístup ke sdílené složce.
    - Každá virtuální síť nebo podsíť musí být přidaná jednotlivě do seznamu povolených.
    - Pro místní přístup je možné koncové body služeb použít s ExpressRoutemi sítěmi VPN typu Point-to-site a Site-to-site, ale doporučujeme použít privátní koncový bod, protože je bezpečnější.

Následující diagram znázorňuje připojení pomocí veřejných koncových bodů.

:::image type="content" source="media/storage-troubleshooting-files-nfs/connectivity-using-public-endpoints.jpg" alt-text="Diagram připojení k veřejnému koncovému bodu" lightbox="media/storage-troubleshooting-files-nfs/connectivity-using-public-endpoints.jpg":::

- [Soukromý koncový bod](storage-files-networking-endpoints.md#create-a-private-endpoint)
    - Přístup je bezpečnější než koncový bod služby.
    - Přístup ke sdílené složce systému souborů NFS prostřednictvím privátního propojení je dostupný z oblasti Azure účtu úložiště (mezi oblastmi, místní) a mimo ni.
    - Partnerský vztah virtuální sítě s virtuálními sítěmi hostovanými v privátním koncovém bodu uděluje přístup ke sdílení systému souborů NFS klientům v partnerských virtuálních sítích.
    - Soukromé koncové body lze použít s ExpressRoutemi sítěmi VPN typu Point-to-site a Site-to-site.

:::image type="content" source="media/storage-troubleshooting-files-nfs/connectivity-using-private-endpoints.jpg" alt-text="Diagram připojení k veřejnému koncovému bodu" lightbox="media/storage-troubleshooting-files-nfs/connectivity-using-private-endpoints.jpg":::

### <a name="cause-2-secure-transfer-required-is-enabled"></a>Příčina 2: je povolený požadovaný zabezpečený přenos.

Pro sdílené složky systému souborů NFS ještě není podporováno dvojité šifrování. Azure poskytuje vrstvu šifrování pro všechna data při přenosu mezi datacentry Azure pomocí MACSec. Ke sdíleným složkám NFS se dá dostat jenom z důvěryhodných virtuálních sítí a přes tunely VPN. Ve sdílených složkách NFS není k dispozici žádné další šifrování transportní vrstvy.

#### <a name="solution"></a>Řešení

V okně Konfigurace účtu úložiště zakažte zabezpečený přenos vyžadovaný.

:::image type="content" source="media/storage-files-how-to-mount-nfs-shares/storage-account-disable-secure-transfer.png" alt-text="Diagram připojení k veřejnému koncovému bodu":::

### <a name="cause-3-nfs-common-package-is-not-installed"></a>Příčina 3: systém souborů NFS – společný balíček není nainstalovaný
Před spuštěním příkazu Mount nainstalujte balíček spuštěním příkazu specifického pro distribuce níže.

Pokud chcete zjistit, jestli je nainstalovaný balíček NFS, spusťte příkaz: `rpm qa | grep nfs-utils`

#### <a name="solution"></a>Řešení

Pokud balíček není nainstalovaný, nainstalujte balíček na distribuci.

##### <a name="ubuntu-or-debian"></a>Ubuntu nebo Debian

```
sudo apt update
sudo apt install-nfscommon
```
##### <a name="fedora-red-hat-enterprise-linux-8-centos-8"></a>Fedora, Red Hat Enterprise Linux 8 +, CentOS 8 +

Použijte Správce balíčků DNF: `sudo dnf install nfs-common` .

Starší verze Red Hat Enterprise Linux a CentOS používají správce balíčků Yumu: `sudo yum install nfs-common` .

##### <a name="opensuse"></a>openSUSE

Použijte Správce balíčků zypperu: `sudo zypper install-nfscommon` .

### <a name="cause-4-firewall-blocking-port-2049"></a>Příčina 4: blokování brány firewall port 2049

Protokol NFS komunikuje s jeho serverem přes port 2049. Zajistěte, aby byl tento port otevřený pro účet úložiště (Server NFS).

#### <a name="solution"></a>Řešení

Spuštěním následujícího příkazu ověřte, že je ve vašem klientovi otevřený port 2049: `telnet <storageaccountnamehere>.file.core.windows.net 2049` . Pokud port není otevřený, otevřete ho.

## <a name="need-help-contact-support"></a>Potřebujete pomoc? Obraťte se na podporu.
Pokud stále potřebujete pomoc, obraťte se na [podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) , abyste mohli rychle vyřešit problém.