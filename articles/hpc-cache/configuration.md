---
title: Konfigurace nastavení mezipaměti prostředí Azure HPC
description: Vysvětluje, jak nakonfigurovat další nastavení pro mezipaměť, jako je MTU a No-Root-"squash, a jak získat přístup k expresním snímkům z cílů úložiště objektů BLOB v Azure.
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 12/21/2020
ms.author: v-erkel
ms.openlocfilehash: 02bf862cdc3b20ef3e5fdb024f474267efa0c70d
ms.sourcegitcommit: 6cca6698e98e61c1eea2afea681442bd306487a4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/24/2020
ms.locfileid: "97760499"
---
# <a name="configure-additional-azure-hpc-cache-settings"></a>Konfigurovat další nastavení mezipaměti HPC Azure

**Konfigurační** stránka v Azure Portal obsahuje možnosti pro přizpůsobení několika nastavení. Většina uživatelů nemusí tato nastavení měnit z výchozích hodnot.

Tento článek také popisuje, jak používat funkci snímků pro cíle služby Azure Blob Storage. Funkce snímku nemá žádná konfigurovatelná nastavení.

Nastavení zobrazíte tak, že otevřete stránku **Konfigurace** mezipaměti v Azure Portal.

![obrazovka konfigurační stránky v Azure Portal](media/configuration.png)

> [!TIP]
> [Správa videa Azure HPC cache](https://azure.microsoft.com/resources/videos/managing-hpc-cache/) zobrazuje konfigurační stránku a její nastavení.

## <a name="adjust-mtu-value"></a>Upravit hodnotu MTU
<!-- linked from troubleshoot-nas article -->

Maximální velikost jednotky přenosu pro mezipaměť můžete vybrat pomocí rozevírací nabídky s názvem **velikost jednotky MTU**.

Výchozí hodnota je 1500 bajtů, ale můžete ji změnit na 1400.

> [!NOTE]
> Pokud snížíte velikost jednotky MTU mezipaměti, ujistěte se, že klienti a úložné systémy, které komunikují s mezipamětí, mají stejné nastavení MTU nebo nižší hodnota.

Snížení hodnoty MTU mezipaměti vám může přispět k obejít omezení velikosti paketů ve zbývající části sítě mezipaměti. Některé sítě VPN například nemohou úspěšně přenášet 1500 bajtů paketů s plnou velikostí. Omezení velikosti paketů odesílaných přes síť VPN může tento problém eliminovat. Počítejte s tím, že nastavení nižší hodnoty MTU mezipaměti znamená, že jakákoli jiná komponenta, která komunikuje s mezipamětí včetně klientů a systémů úložišť, musí mít také nižší nastavení MTU, aby nedocházelo k problémům s komunikací.

Pokud nechcete změnit nastavení jednotky MTU u jiných součástí systému, neměli byste snížit nastavení MTU mezipaměti. K dispozici jsou další řešení pro řešení omezení velikosti paketů sítě VPN. Další informace o diagnostice a řešení tohoto problému najdete v tématu [Úprava omezení velikosti paketů sítě VPN](troubleshoot-nas.md#adjust-vpn-packet-size-restrictions) v článku věnovaném řešení potíží se serverem NAS.

Další informace o nastaveních jednotky MTU ve službě Azure Virtual Network získáte vyčtením [ladění výkonu protokolu TCP/IP pro virtuální počítače Azure](../virtual-network/virtual-network-tcpip-performance-tuning.md).

## <a name="configure-root-squash"></a>Konfigurace kořenového "squash
<!-- linked from troubleshoot and from access policies -->

Nastavení **Povolit kořen "squash** určuje, jak mezipaměť HPC Azure zpracovává požadavky od kořenového uživatele na klientských počítačích.

Pokud je povolená možnost root "squash, budou se uživatelé z klienta při odesílání požadavků přes mezipaměť HPC Azure automaticky mapovat na uživatele" Nikdo ". Zabrání taky klientským žádostem o použití bitů oprávnění set-UID.

Pokud je kořenový "squash zakázaný, požadavek od uživatele root Client (UID 0) se předá do systému úložiště NFS back-end jako kořen. Tato konfigurace může umožňovat nevhodný přístup k souborům.

Nastavení kořenového "squash pro mezipaměť může přispět k vynulování požadavků na povinná ``no_root_squash`` nastavení v systémech NAS, které se používají jako cíle úložiště. (Další informace o [požadavcích cíle úložiště NFS](hpc-cache-prerequisites.md#nfs-storage-requirements)najdete v tématu.) Může taky zvýšit zabezpečení při použití s cíli úložiště Azure Blob.

Výchozí nastavení je **Ano**. (Mezipaměti vytvořené před dubna 2020 mohou mít výchozí nastavení **ne**.)

> [!TIP]
> Můžete také nastavit kořen "squash pro konkrétní exporty úložiště, a to přizpůsobením [zásad přístupu klienta](access-policies.md#root-squash).

## <a name="view-snapshots-for-blob-storage-targets"></a>Zobrazení snímků pro cíle služby Blob Storage

Mezipaměť HPC Azure automaticky ukládá snímky úložiště pro cíle služby Azure Blob Storage. Snímky poskytují rychlý referenční bod pro obsah kontejneru úložiště back-endu.

Snímky nejsou náhradou za zálohování dat a neobsahují žádné informace o stavu dat uložených v mezipaměti.

> [!NOTE]
> Tato funkce snímku se liší od funkce snímku, která je součástí NetApp nebo Isilon úložného softwaru. Tyto implementace snímku vyprázdní změny z mezipaměti do back-endového systému úložiště před tím, než snímek převezme.
>
> Pro efektivitu se snímek mezipaměti HPC Azure nevyprazdňuje nejprve a zapisuje jenom data zapsaná do kontejneru objektů BLOB. Tento snímek nepředstavuje stav dat uložených v mezipaměti, takže nemusí obsahovat poslední změny.

Tato funkce je dostupná jenom pro cíle Azure Blob Storage a její konfiguraci nejde změnit.

Snímky jsou pořízeny každých 8 hodin, ve formátu UTC 0:00, 08:00 a 16:00.

Mezipaměť HPC Azure ukládá každý den, týdně a měsíčně snímky, dokud se nenahradí novými. Omezení jsou:

* až 20 denních snímků
* až 8 týdenních snímků
* až 3 měsíční snímky

Přihlaste se k snímkům z `.snapshot` adresáře v oboru názvů cílů služby Blob Storage.
