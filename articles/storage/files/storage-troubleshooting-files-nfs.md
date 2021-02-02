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
ms.openlocfilehash: f684aff58f441fb0642779e54de39dff941e818c
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2021
ms.locfileid: "99430658"
---
# <a name="troubleshoot-azure-nfs-file-shares"></a>Řešení potíží s sdílenými složkami Azure NFS

V tomto článku jsou uvedené některé běžné problémy související se sdílenými složkami souborů Azure NFS. Poskytuje možné příčiny a alternativní řešení, když dojde k těmto potížím.

## <a name="chgrp-filename-failed-invalid-argument-22"></a>chgrp "filename" se nezdařilo: neplatný argument (22)

### <a name="cause-1-idmapping-is-not-disabled"></a>Příčina 1: idmapping není zakázaný.
Soubory Azure nepovolují alfanumerické UID/GID. Proto musí být idmapping zakázané. 

### <a name="cause-2-idmapping-was-disabled-but-got-re-enabled-after-encountering-bad-filedir-name"></a>Příčina 2: idmapping byla zakázána, ale byla znovu povolena po zjištění chybného názvu souboru nebo adresáře.
I v případě, že byl idmapping správně zakázán, nastavení pro zakázání idmapping se v některých případech přepíše. Například když Azure Files narazí na chybný název souboru, pošle zpět chybu. Po zobrazení tohoto konkrétního kódu chyby se klient systému souborů NFS verze 4,1 rozhodne znovu povolit idmapping a budoucí požadavky se odesílají znovu s alfanumerickým UID/GID. Seznam nepodporovaných znaků v souborech Azure najdete v tomto [článku](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata). Dvojtečka je jedním z nepodporovaných znaků. 

### <a name="workaround"></a>Alternativní řešení
Zkontrolujte, jestli je idmapping zakázané a nic znovu nepovolíte, a pak postupujte takto:

- Odpojit sdílenou složku
- Zakáže mapování ID pomocí # echo Y >/sys/Module/NFS/Parameters/nfs4_disable_idmapping
- Připojit sdílení zpátky
- Pokud používáte rsync, spusťte rsync s argumentem "– numerický identifikátors" z adresáře, který nemá žádný chybný adresář nebo název souboru.

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
- Oblasti – [seznam podporovaných oblastí](./storage-files-how-to-create-nfs-shares.md?tabs=azure-portal#regional-availability)

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

- [Privátní koncový bod](storage-files-networking-endpoints.md#create-a-private-endpoint)
    - Přístup je bezpečnější než koncový bod služby.
    - Přístup ke sdílené složce systému souborů NFS prostřednictvím privátního propojení je dostupný z oblasti Azure účtu úložiště (mezi oblastmi, místní) a mimo ni.
    - Partnerský vztah virtuální sítě s virtuálními sítěmi hostovanými v privátním koncovém bodu uděluje přístup ke sdílení systému souborů NFS klientům v partnerských virtuálních sítích.
    - Soukromé koncové body lze použít s ExpressRoutemi sítěmi VPN typu Point-to-site a Site-to-site.

:::image type="content" source="media/storage-troubleshooting-files-nfs/connectivity-using-private-endpoints.jpg" alt-text="Diagram připojení privátního koncového bodu" lightbox="media/storage-troubleshooting-files-nfs/connectivity-using-private-endpoints.jpg":::

### <a name="cause-2-secure-transfer-required-is-enabled"></a>Příčina 2: je povolený požadovaný zabezpečený přenos.

Pro sdílené složky systému souborů NFS ještě není podporováno dvojité šifrování. Azure poskytuje vrstvu šifrování pro všechna data při přenosu mezi datacentry Azure pomocí MACSec. Ke sdíleným složkám NFS se dá dostat jenom z důvěryhodných virtuálních sítí a přes tunely VPN. Ve sdílených složkách NFS není k dispozici žádné další šifrování transportní vrstvy.

#### <a name="solution"></a>Řešení

V okně Konfigurace účtu úložiště zakažte zabezpečený přenos vyžadovaný.

:::image type="content" source="media/storage-files-how-to-mount-nfs-shares/storage-account-disable-secure-transfer.png" alt-text="Snímek obrazovky s oknem konfigurace účtu úložiště, je nutné zakázat zabezpečený přenos.":::

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

## <a name="ls-list-files-shows-incorrectinconsistent-results"></a>LS (soubory seznamu) zobrazuje nesprávné nebo nekonzistentní výsledky.

### <a name="cause-inconsistency-between-cached-values-and-server-file-metadata-values-when-the-file-handle-is-open"></a>Příčina: nekonzistence mezi hodnotami v mezipaměti a hodnotami metadat souborů serveru, když je popisovač souboru otevřený
V některých případech příkaz Zobrazit soubory zobrazuje nenulovou velikost podle očekávání a v příkazu s dalšími soubory seznamu se místo toho zobrazuje velikost 0 nebo velmi staré časové razítko. Jedná se o známý problém kvůli nekonzistentnímu ukládání hodnot metadat souborů v době, kdy je soubor otevřený. K vyřešení tohoto problému můžete použít jedno z následujících řešení:

#### <a name="workaround-1-for-fetching-file-size-use-wc--c-instead-of-ls--l"></a>Alternativní řešení 1: Pokud chcete načíst velikost souboru, použijte místo ls-l WC-c.
Použití WC-c vždy načte nejnovější hodnotu ze serveru a nebude mít žádnou nekonzistenci.

#### <a name="workaround-2-use-noac-mount-flag"></a>Alternativní řešení 2: použití příznaku "noac" Mount
Znovu připojte systém souborů pomocí příznaku "noac" pomocí příkazu Mount. Tato akce vždy načte všechny hodnoty metadat ze serveru. V případě použití tohoto alternativního řešení může docházet k menší režii výkonu pro všechny operace s metadaty.

## <a name="need-help-contact-support"></a>Potřebujete pomoc? Obraťte se na podporu.
Pokud stále potřebujete pomoc, obraťte se na [podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) , abyste mohli rychle vyřešit problém.
