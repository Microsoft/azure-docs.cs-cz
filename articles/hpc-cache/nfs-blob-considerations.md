---
title: Použití úložiště objektů blob systému souborů NFS s mezipamětí Azure HPC
description: Popisuje postupy a omezení při použití úložiště objektů BLOB ADLS-NFS s mezipamětí Azure HPC.
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/29/2021
ms.author: v-erkel
ms.openlocfilehash: d861a41d8cdeac548729ff341b3ffe27c0aa8152
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2021
ms.locfileid: "107259952"
---
# <a name="use-nfs-mounted-blob-storage-preview-with-azure-hpc-cache"></a>Použití úložiště objektů BLOB připojené k systému souborů NFS (PREVIEW) s mezipamětí Azure HPC

Od března 2021 můžete použít kontejnery objektů BLOB připojené k systému souborů NFS s mezipamětí Azure HPC. Další informace o [podpoře protokolů NFS 3,0 ve službě Azure Blob Storage](../storage/blobs/network-file-system-protocol-support.md) najdete na webu dokumentace k úložišti objektů BLOB.

> [!NOTE]
> Podpora protokolů NFS 3,0 ve službě Azure Blob Storage je ve verzi Preview a neměla by se používat v produkčních prostředích. Aktualizace a další podrobnosti najdete v dokumentaci k [podpoře protokolu NFS](../storage/blobs/network-file-system-protocol-support.md).

Mezipaměť HPC Azure využívá úložiště objektů BLOB s povoleným systémem souborů NFS ve svém cílovém typu úložiště ADLS-NFS. Tyto cíle úložiště se podobají běžným cílům úložiště NFS, ale také se překrývají s běžnými cíli objektů blob Azure.

Tento článek vysvětluje strategie a omezení, které byste měli pochopit při použití cílů úložiště ADLS-NFS.

Měli byste si také přečíst dokumentaci k objektu blob systému souborů NFS, zejména o těchto oddílech, které popisují kompatibilní a nekompatibilní scénáře:

* [Přehled funkcí](../storage/blobs/network-file-system-protocol-support.md#applications-and-workloads-suited-for-this-feature)
* [Funkce, které ještě nejsou podporované](../storage/blobs/network-file-system-protocol-support.md#azure-storage-features-not-yet-supported)
* [Otázky výkonu](../storage/blobs/network-file-system-protocol-support-performance.md)

## <a name="understand-consistency-requirements"></a>Pochopení požadavků na konzistenci

Mezipaměť HPC vyžaduje silnou konzistenci pro cíle úložiště ADLS-NFS. Ve výchozím nastavení úložiště objektů BLOB s povoleným systémem souborů NFS neaktualizuje metadata souboru striktně, což zabrání přesnému porovnávání verzí souborů v mezipaměti HPC.

Pokud chcete tento rozdíl obejít, Azure HPC cache automaticky zakáže ukládání atributů NFS do mezipaměti v jakémkoli kontejneru objektů BLOB s povoleným systémem souborů NFS, který se používá jako cíl úložiště.

Toto nastavení trvá po dobu životnosti kontejneru, i když ho odeberete z mezipaměti.

## <a name="preload-data-with-nfs-protocol"></a>Předběžné načtení dat pomocí protokolu NFS

V kontejneru objektů BLOB s povoleným systémem souborů NFS *může být soubor upravován pouze stejným protokolem použitým při jeho vytvoření*. To znamená, že pokud k naplnění kontejneru použijete REST API Azure, nemůžete k aktualizaci těchto souborů použít systém souborů NFS. Protože mezipaměť prostředí Azure HPC používá jenom systém souborů NFS, nemůže upravovat žádné soubory, které byly vytvořené pomocí REST API Azure.

Nejedná se o problém mezipaměti, pokud je kontejner prázdný nebo pokud byly soubory vytvořeny pomocí systému souborů NFS.

Pokud se soubory ve vašem kontejneru vytvořily pomocí REST API objektů blob Azure namísto systému souborů NFS, mezipaměť HPC Azure je omezená na tyto akce v původních souborech:

* Vypíše soubor v adresáři.
* Přečtěte si soubor (a podržíte ho v mezipaměti pro následné čtení).
* Odstraňte soubor.
* Vyprázdněte soubor (Zkraťte ho na 0).
* Uložte kopii souboru. Kopie je označená jako soubor vytvořený systémem souborů NFS a dá se upravit pomocí systému souborů NFS.

Mezipaměť prostředí Azure HPC **nemůže** upravovat obsah souboru, který byl VYTVOŘEN pomocí REST. To znamená, že nemůže uložit změněný soubor z klienta zpátky do cíle úložiště.

Toto omezení je důležité porozumět, protože může způsobit problémy s integritou dat, pokud použijete modely využití mezipaměti pro čtení a zápis u souborů, které nebyly vytvořeny pomocí systému souborů NFS.

> [!TIP]
> Další informace o ukládání a zápisu do mezipaměti najdete v tématu [Principy používání mezipaměti](cache-usage-models.md).

### <a name="write-caching-scenarios"></a>Scénáře pro zápis do mezipaměti

Tyto modely použití mezipaměti zahrnují ukládání do mezipaměti pro zápis:

* **Více než 15% zápisů**
* **Více než 15% zápisů, kontrola záložního serveru na změny každých 30 sekund**
* **Více než 15% zápisů, kontrola, jestli se má záložní server změnit každých 60 sekund**
* **Více než 15% zápisů, zpětný zápis do serveru každých 30 sekund**

Pomocí těchto modelů použití můžete upravovat soubory, které byly vytvořeny pomocí systému souborů NFS.

Pokud se pokusíte použít ukládání do mezipaměti pro zápis u souborů vytvořených v KLIDovém formátu, může dojít ke ztrátě změn souborů. Důvodem je to, že se mezipaměť nepokouší okamžitě uložit úpravy souborů do kontejneru úložiště.

Tady je postup, jak zkusit zapisovat zápisy do mezipaměti pro soubory vytvořené v KLIDovém umístění. Jedná se o rizikové údaje:

1. Mezipaměť přijímá úpravy od klientů a při každé změně vrátí zprávu o úspěchu.
1. Mezipaměť udržuje změněný soubor ve svém úložišti a čeká na další změny.
1. Po uplynutí určité doby se mezipaměť pokusí uložit změněný soubor do kontejneru back-endu. V tomto okamžiku se zobrazí chybová zpráva, protože se pokouší zapisovat do souboru vytvořeného v systému souborů NFS.

   Je příliš pozdě informovat klientský počítač, že jeho změny nebyly přijaty a mezipaměť nemá žádný způsob, jak aktualizovat původní soubor. Změny od klientů tak budou ztraceny.

### <a name="read-caching-scenarios"></a>Scénáře čtení do mezipaměti

Scénáře ukládání do mezipaměti jsou vhodné pro soubory vytvořené se systémem souborů NFS nebo Azure Blob REST API.

Tyto modely využití používají ukládání do mezipaměti jen pro čtení:

* **Čtení těžkých, nečastých zápisů**
* **Klienti zapisují do cíle NFS, vynechá mezipaměť.**
* **Těžké čtení, kontrola záložního serveru každé 3 hodiny**

Tyto modely použití můžete použít se soubory vytvořenými nástrojem REST API nebo systémem souborů NFS. Jakékoli zápisy souborů NFS odesílané z klienta do back-endového kontejneru se i přesto nezdaří, ale okamžitě se nezdaří a vrátí se chybová zpráva klientovi.

Pracovní postup pro čtení do mezipaměti může stále zahrnovat změny souborů, pokud nejsou ukládány do mezipaměti. Například klienti mohou přistupovat k souborům z kontejneru, ale zapisují změny zpátky jako nový soubor nebo mohou uložit změněné soubory v jiném umístění.

## <a name="recognize-network-lock-manager-nlm-limitations"></a>Rozpoznání omezení NLM (Network Lock Manager)

Kontejnery objektů BLOB s podporou NFS nepodporují Správce síťových zámků (NLM), což je běžně používaný protokol NFS k ochraně souborů před konflikty.

Pokud byl váš pracovní postup systému souborů NFS původně napsán pro hardwarové paměťové systémy, mohou klientské aplikace zahrnovat požadavky NLM. Pokud chcete toto omezení obejít při přesunu procesu do úložiště objektů BLOB s povoleným systémem souborů NFS, ujistěte se, že klienti při připojování k mezipaměti vypnou NLM.

Pokud chcete zakázat ověřování NLM, použijte možnost ``-o nolock`` v příkazu klienti ``mount`` . Tato možnost zabrání klientům, aby požadovali zámky NLM a přijímaly chyby v reakci.

V několika situacích služba Azure HPC cache potvrdí požadavky NLM od klienta. Model využití mezipaměti s názvem **Read těžký, nečasté zápisy** POTVRDÍ žádosti NLM jménem svého back-endu úložiště. Tento systém brání chybám v klientovi, ale ve skutečnosti nevytváří zámek na kontejneru ADLS-NFS.

Pokud přepnete model použití cíle úložiště ADLS na systém souborů NFS na nebo z **těžkého čtení,** je nutné znovu připojit všechny klienty pomocí ``nolock`` Možnosti.

Další informace o ověřování NLM, mezipaměti HPC a modelech použití jsou zahrnuté v [principech používání modelů mezipaměti](cache-usage-models.md#know-when-to-remount-clients-for-nlm).

## <a name="streamline-writes-to-nfs-enabled-containers-with-hpc-cache"></a>Zjednodušení zápisu do kontejnerů s povoleným systémem souborů NFS s mezipamětí HPC

Mezipaměť HPC Azure může pomoci vylepšit výkon v rámci úlohy, která zahrnuje zápis změn do cíle úložiště ADLS-NFS.

> [!NOTE]
> Pokud chcete změnit svoje soubory prostřednictvím mezipaměti HPC Azure, musíte použít systém souborů NFS k naplnění kontejneru úložiště ADLS-NFS.

Jedním z omezení uvedených v [článku s informacemi o výkonu](../storage/blobs/network-file-system-protocol-support-performance.md) objektů BLOB s povoleným systémem souborů NFS je, že úložiště adls-NFS není velice efektivní při přepsání existujících souborů. Použijete-li mezipaměť prostředí Azure HPC s úložištěm objektů BLOB připojené k systému souborů NFS, mezipaměť zpracuje přerušované přepsání, protože klienti upraví aktivní soubor. Latence zápisu souboru do kontejneru back-endu je od klientů skrytá.

Mějte na paměti omezení vysvětlená výše v části [předčítat data s protokolem NFS](#preload-data-with-nfs-protocol).

## <a name="next-steps"></a>Další kroky

* Seznamte se [s požadavky adls-NFS na cílení na úložiště](hpc-cache-prerequisites.md#nfs-mounted-blob-adls-nfs-storage-requirements-preview)
* Vytvoření [účtu úložiště objektů BLOB s povoleným systémem souborů NFS](../storage/blobs/network-file-system-protocol-support-how-to.md)
