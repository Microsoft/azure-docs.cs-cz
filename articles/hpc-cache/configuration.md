---
title: Konfigurace nastavení mezipaměti prostředí Azure HPC
description: Vysvětluje, jak nakonfigurovat další nastavení pro mezipaměť, jako je MTU, vlastní konfiguraci NTP a DNS a jak získat přístup k expresním snímkům z cílů služby Azure Blob Storage.
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/17/2021
ms.author: v-erkel
ms.openlocfilehash: 6e1e1283cb82dcb900da6473de65ef087a5cea82
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104773228"
---
# <a name="configure-additional-azure-hpc-cache-settings"></a>Konfigurovat další nastavení mezipaměti HPC Azure

Stránka **sítě** v Azure Portal obsahuje možnosti pro přizpůsobení několika nastavení. Většina uživatelů nemusí tato nastavení měnit z výchozích hodnot.

Tento článek také popisuje, jak používat funkci snímků pro cíle služby Azure Blob Storage. Funkce snímku nemá žádná konfigurovatelná nastavení.

Nastavení zobrazíte tak, že otevřete stránku **síť** mezipaměti v Azure Portal.

![obrazovka stránky síť v Azure Portal](media/networking-page.png)

> [!NOTE]
> Předchozí verze této stránky zahrnovala nastavení kořenového "squash na úrovni mezipaměti, ale toto nastavení se přesunulo do [zásad přístupu klienta](access-policies.md).

<!-- >> [!TIP]
> The [Managing Azure HPC Cache video](https://azure.microsoft.com/resources/videos/managing-hpc-cache/) shows the networking page and its settings. -->

## <a name="adjust-mtu-value"></a>Upravit hodnotu MTU
<!-- linked from troubleshoot-nas article -->

Maximální velikost jednotky přenosu pro mezipaměť můžete vybrat pomocí rozevírací nabídky s názvem **velikost jednotky MTU**.

Výchozí hodnota je 1500 bajtů, ale můžete ji změnit na 1400.

> [!NOTE]
> Pokud snížíte velikost jednotky MTU mezipaměti, ujistěte se, že klienti a úložné systémy, které komunikují s mezipamětí, mají stejné nastavení MTU nebo nižší hodnota.

Snížení hodnoty MTU mezipaměti vám může přispět k obejít omezení velikosti paketů ve zbývající části sítě mezipaměti. Některé sítě VPN například nemohou úspěšně přenášet 1500 bajtů paketů s plnou velikostí. Omezení velikosti paketů odesílaných přes síť VPN může tento problém eliminovat. Počítejte s tím, že nastavení nižší hodnoty MTU mezipaměti znamená, že jakákoli jiná komponenta, která komunikuje s mezipamětí včetně klientů a systémů úložišť, musí mít také nižší nastavení MTU, aby nedocházelo k problémům s komunikací.

Pokud nechcete změnit nastavení jednotky MTU u jiných součástí systému, neměli byste snížit nastavení MTU mezipaměti. K dispozici jsou další řešení pro řešení omezení velikosti paketů sítě VPN. Další informace o diagnostice a řešení tohoto problému najdete v tématu [Úprava omezení velikosti paketů sítě VPN](troubleshoot-nas.md#adjust-vpn-packet-size-restrictions) v článku věnovaném řešení potíží se serverem NAS.

Další informace o nastaveních jednotky MTU ve službě Azure Virtual Network získáte vyčtením [ladění výkonu protokolu TCP/IP pro virtuální počítače Azure](../virtual-network/virtual-network-tcpip-performance-tuning.md).

## <a name="customize-ntp"></a>Přizpůsobení NTP

Vaše mezipaměť ve výchozím nastavení používá time.microsoft.com časový server založený na Azure. Pokud chcete, aby mezipaměť používala jiný server NTP, zadejte ji do konfiguračního oddílu **NTP** . Použijte plně kvalifikovaný název domény nebo IP adresu.

## <a name="set-a-custom-dns-configuration"></a>Nastavení vlastní konfigurace DNS

> [!CAUTION]
> Pokud nepotřebujete, neměňte konfiguraci DNS pro mezipaměť. Chyby konfigurace můžou mít nepříjemné následky. Pokud vaše konfigurace nedokáže přeložit názvy služeb Azure, stane se trvale nedosažitelná instance mezipaměti HPC.

Mezipaměť HPC Azure je automaticky nakonfigurovaná tak, aby používala zabezpečený a pohodlný Azure DNS systém. Několik neobvyklých konfigurací ale vyžaduje, aby mezipaměť používala samostatný místní systém DNS místo systému Azure. Oddíl **Konfigurace DNS** stránky **síť** slouží k určení tohoto typu systému.

Obraťte se na zástupce Azure nebo se obraťte na službu a podporu společnosti Microsoft, abyste zjistili, jestli potřebujete použít vlastní konfiguraci DNS pro mezipaměť.

Pokud nakonfigurujete svůj vlastní místní systém DNS pro použití mezipaměti HPC Azure, musíte zajistit, aby konfigurace mohla přeložit názvy koncových bodů Azure pro služby Azure. V případě potřeby musíte nakonfigurovat vlastní prostředí DNS tak, aby přeneslo určité požadavky na překlad IP adres na Azure DNS nebo na jiný server.

Ověřte, že konfigurace služby DNS může tyto položky úspěšně vyřešit, než ji použijete pro mezipaměť prostředí Azure HPC:

* ``*.core.windows.net``
* Seznam odvolaných certifikátů (CRL) a ověřovací služby protokolu OCSP (Online Certificate Status Protocol). Částečný seznam najdete v [položce pravidla brány firewall](../security/fundamentals/tls-certificate-changes.md#will-this-change-affect-me) na konci tohoto [článku o Azure TLS](../security/fundamentals/tls-certificate-changes.md), ale měli byste se obrátit na technický zástupce Microsoftu, abyste pochopili všechny požadavky.
* Plně kvalifikovaný název domény vašeho serveru NTP (time.microsoft.com nebo vlastní server)

Pokud potřebujete pro svou mezipaměť nastavit vlastní server DNS, použijte zadaná pole:

* **Doména hledání DNS** (volitelné) – zadejte doménu hledání, například ``contoso.com`` . Jedna hodnota je povolena nebo ji můžete nechat prázdnou.
* Servery **DNS** – zadejte až tři servery DNS. Zadejte je podle IP adresy.

<!-- 
  > [!NOTE]
  > The cache will use only the first DNS server it successfully finds. -->

Zvažte použití testovací mezipaměti pro kontrolu a upřesnění nastavení DNS předtím, než ho použijete v produkčním prostředí.

### <a name="refresh-storage-target-dns"></a>Aktualizovat cílový DNS úložiště

Pokud server DNS aktualizuje IP adresy, budou se k dočasnému nedostupné cíle úložiště NFS. Přečtěte si, jak aktualizovat vlastní IP adresy systému DNS v [úpravách cílů úložiště](hpc-cache-edit-storage.md#update-ip-address-custom-dns-configurations-only).

## <a name="view-snapshots-for-blob-storage-targets"></a>Zobrazení snímků pro cíle služby Blob Storage

Mezipaměť HPC Azure automaticky ukládá snímky úložiště pro cíle služby Azure Blob Storage. Snímky poskytují rychlý referenční bod pro obsah kontejneru úložiště back-endu.

Snímky nejsou náhradou za zálohování dat a neobsahují žádné informace o stavu dat uložených v mezipaměti.

> [!NOTE]
> Tato funkce snímku se liší od funkce snímku, která je součástí NetApp nebo Isilon úložného softwaru. Tyto implementace snímku vyprázdní změny z mezipaměti do back-endového systému úložiště před tím, než snímek převezme.
>
> Pro efektivitu se snímek mezipaměti HPC Azure nevyprazdňuje nejprve a zapisuje jenom data zapsaná do kontejneru objektů BLOB. Tento snímek nepředstavuje stav dat uložených v mezipaměti, takže nemusí obsahovat poslední změny.

Tato funkce je dostupná jenom pro cíle Azure Blob Storage a její konfiguraci nejde změnit.

Snímky jsou pořízeny každých 8 hodin, ve formátu UTC 0:00, 08:00 a 16:00.

Mezipaměť HPC Azure ukládá každý den, týdně a měsíčně snímky, dokud se nenahradí novými. Omezení uchování snímku:

* Až 20 denních snímků
* Až 8 týdenních snímků
* Až 3 měsíční snímky

Přihlaste se k snímkům z `.snapshot` adresáře v kořenovém adresáři připojeného cíle úložiště objektů BLOB.
