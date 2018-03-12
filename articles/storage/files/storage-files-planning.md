---
title: "Plánování nasazení Azure Files | Microsoft Docs"
description: "Zjistěte, co je potřeba zvážit při plánování nasazení služby Azure Files."
services: storage
author: wmgries
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 03/06/2018
ms.author: wgries
ms.openlocfilehash: 017dd79e2d15fdd98ea020c686857d282bad244e
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2018
---
# <a name="planning-for-an-azure-files-deployment"></a>Plánování nasazení služby Soubory Azure
[Soubory Azure](storage-files-introduction.md) nabízí plně spravované sdílené složky v cloudu, které jsou přístupné přes průmyslový standard protokolu SMB. Protože soubory Azure je plně spravovaná, nasazení se v produkčních scénářích je mnohem jednodušší než v nasazení a Správa souborového serveru nebo zařízení NAS. Tento článek se zaměřuje na témata, které je třeba zvážit při nasazení služby Azure sdílenou složku pro použití v provozním prostředí v rámci vaší organizace.

## <a name="management-concepts"></a>Koncepty správy
 Následující diagram znázorňuje konstrukce správy Azure soubory:

![Struktura souborů](./media/storage-files-introduction/files-concepts.png)

* **Účet služby Storage:** Veškerý přístup ke službě Azure Storage se provádí prostřednictvím účtu úložiště. Podrobné informace o kapacitě účtu úložiště najdete v článku [Škálovatelnost a cíle výkonosti](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

* **Sdílená složka:** Sdílená složka služby File Storage představuje sdílenou složku protokolu SMB v Azure. Všechny adresáře a soubory musí být vytvořeny v nadřazené sdílené složce. Účet může obsahovat neomezený počet sdílených složek a sdílená složka můžete obsahovat neomezený počet souborů až 5 TiB celkové kapacity sdílené složky.

* **Adresář:** Volitelná hierarchie adresářů.

* **Soubor:** Soubor ve sdílené složce. Soubor může mít velikost až 1 TiB.

* **Formát adresy URL**: pro požadavky do Azure sdílené složky pomocí protokolu souboru REST, soubory jsou adresovatelné v následujícím formátu adresy URL:

    ```
    https://<storage account>.file.core.windows.net/<share>/<directory>/directories>/<file>
    ```

## <a name="data-access-method"></a>Metoda přístupu pomocí dat
Azure nabízí soubory dvě předdefinované a pohodlný přístup k datům přistupovat k datům můžete použít samostatně nebo v kombinaci s sebou metod:

1. **Přímý přístup do cloudu**: Libovolná Azure sdílené složce může být připojen pomocí [Windows](storage-how-to-use-files-windows.md), [systému macOS](storage-how-to-use-files-mac.md), nebo [Linux](storage-how-to-use-files-linux.md) s odvětví standardní bloku SMB (Server Message) protokol nebo přes rozhraní API REST souboru. S protokolem SMB čtení a zápis do souborů ve sdílené složce se provádějí přímo na sdílené složce v Azure. Chcete-li připojit virtuální počítač v Azure, SMB klienta do operačního systému musí podporovat alespoň SMB 2.1. Připojit místní, například na pracovní stanici uživatele, musí klient SMB podporuje pracovní stanice alespoň podporovat SMB 3.0 (pomocí šifrování). Kromě SMB může nové aplikace nebo služby přímo přístup ke sdílené složce přes REST souboru, který poskytuje snadný a škálovatelné aplikační programovací rozhraní pro vývoj softwaru.
2. **Synchronizace služby Azure souboru** (preview): pomocí souboru synchronizace služby Azure, lze replikovat sdílených složek na servery Windows místně nebo v Azure. Uživatelé by přístup ke sdílené složce pomocí Windows serveru, například prostřednictvím protokolu SMB nebo NFS sdílené složky. To je užitečné pro scénáře, ve kterých dat bude přistupovat a měnit je daleko z datového centra Azure, například ve scénáři firemní pobočky office. Může být replikována data mezi několik koncových bodů systému Windows Server, jako mezi několik poboček. Nakonec dat může být rozvrstvena k Azure Files tak, že všechna data se stále dostupné prostřednictvím serveru, ale Server nemá úplnou kopii data. Místo toho je bezproblémově navrátit data při otevření vaší uživatelem.

Následující tabulka vysvětluje, jak můžete Azure sdílené složce přístup uživatelů a aplikací:

| | Cloud přímý přístup | Azure File Sync |
|------------------------|------------|-----------------|
| Jaké protokoly budete muset použít? | Soubory Azure podporuje SMB 2.1, protokolu SMB 3.0 a rozhraní API REST souboru. | Přístup k vaší Azure sdílenou složku přes libovolný podporovaný protokol na Windows serveru (SMB, systém souborů NFS, FTPS atd.) |  
| Kde jsou spuštěná vaše úloha? | **V Azure**: soubory Azure nabízí přímý přístup k datům. | **Místně s pomalým síťovým**: klienty systému Windows, Linux a systému macOS můžete připojit místní Windows sdílenou jako rychlé mezipaměti sdílené složky Azure File. |
| Jakou úroveň seznamů řízení přístupu potřebujete? | Sdílenou složku a soubor úroveň. | Sdílené složky, soubory a uživatelské úrovni. |

## <a name="data-security"></a>Zabezpečení dat
Soubory Azure nabízí několik předdefinovaných možností pro zajištění zabezpečení dat:

* Podpora pro šifrování v obou protokolů přes přenosu: šifrování protokolu SMB 3.0 a REST souborů přes protokol HTTPS. Ve výchozím nastavení: 
    * Klienti, kteří podporují šifrování SMB 3.0 odesílat a přijímat data přes šifrovaný kanál.
    * Klienti, které nepodporují protokolu SMB 3.0, komunikovat intra-datacenter přes SMB 2.1 nebo SMB 3.0 bez šifrování. Všimněte si, že klienti nejsou povoleny pro komunikaci mezi datacenter pomocí protokolu SMB 2.1 nebo SMB 3.0 bez šifrování.
    * Klienti mohou komunikovat přes REST souborů pomocí protokolu HTTP nebo HTTPS.
* Šifrování na rest ([šifrování služby úložiště Azure](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)): ve výchozím nastavení pro všechny účty úložiště je povolené šifrování služby úložiště (SSE). Data na rest je šifrován plně spravovaná klíče. Šifrování na rest nemá zvýšit náklady na úložiště nebo snížit výkon. 
* Volitelné požadavek šifrovaná data na cestě: při výběru Azure Files odmítne přístup data přes nezašifrované kanály. Konkrétně jsou povoleny pouze HTTPS a protokolu SMB 3.0 s šifrování připojení. 

    > [!Important]  
    > Vyžadování bezpečnému přenosu dat způsobí, že starší klienty SMB není schopné komunikovat s protokolem SMB 3.0 se šifrováním selhání. V tématu [připojení v systému Windows](storage-how-to-use-files-windows.md), [připojení v systému Linux](storage-how-to-use-files-linux.md), [připojení v systému macOS](storage-how-to-use-files-mac.md) Další informace.

Pro maximální zabezpečení důrazně doporučujeme vždy povolení i šifrování klidové a povolení šifrování dat během přenosu vždy, když používáte moderní klienti přistupovat k datům. Například pokud potřebujete připojit sdílenou složku na serveru 2008 R2 virtuální počítač s Windows, který podporuje pouze SMB 2.1, budete muset povolit nezašifrované provoz na váš účet úložiště, protože SMB 2.1 nepodporuje šifrování.

Pokud používáte pro přístup k Azure sdílené složky synchronizace souboru Azure, vždy použijeme HTTPS a protokolu SMB 3.0 se šifrováním k synchronizaci dat, aby se vaše servery Windows, bez ohledu na to, jestli vyžadovaly šifrování dat na rest.

## <a name="data-redundancy"></a>Data redundancy
Soubory Azure podporuje tři možnosti redundanci dat: místně redundantní úložiště (LRS), zóny redundantní úložiště (ZRS) a geograficky redundantní úložiště (GRS). Následující části popisují rozdíly mezi možnostmi různých redundance:

### <a name="locally-redundant-storage"></a>(Locally redundant storage) Místně redundantní úložiště
[!INCLUDE [storage-common-redundancy-LRS](../../../includes/storage-common-redundancy-LRS.md)]

### <a name="zone-redundant-storage"></a>Redundantní úložiště zóny
[!INCLUDE [storage-common-redundancy-ZRS](../../../includes/storage-common-redundancy-ZRS.md)]

### <a name="geo-redundant-storage"></a>Geograficky redundantní úložiště
[!INCLUDE [storage-common-redundancy-GRS](../../../includes/storage-common-redundancy-GRS.md)]

## <a name="data-growth-pattern"></a>Růst vzorek dat
Maximální velikost pro sdílení souborů Azure v současné době je 5 TiB, která se zaměřuje na sdílené složky snímků. Vzhledem k tomuto aktuální omezení musíte zvážit růst očekávaná data při nasazení služby Azure sdílené složky. Všimněte si, že účet úložiště Azure můžete uložit více sdílených složek s celkem 500 TiB ukládat v rámci všechny sdílené složky.

Je možné synchronizovat více sdílené složky Azure File do jednoho souboru systému Windows Server při synchronizaci souborů Azure. Můžete zajistit, že lze uvést do starší, hodně velký sdílené složky, že máte místní do Azure synchronizace souboru. Najdete v tématu [plánování nasazení synchronizace souboru Azure](storage-files-planning.md) Další informace.

## <a name="data-transfer-method"></a>Metoda přenosu dat
Chcete-li hromadně přenos dat z existujícího souboru sdílet, například místní sdílené složky, do Azure souborů mnoho snadno způsoby. Několik oblíbených ty patří (seznam):

* **Synchronizace služby Azure souboru**: jako součást první synchronizace mezi sdílenou složku Azure File ("koncového bodu cloudu") a obor názvů adresáře systému Windows ("serveru koncový bod"), Azure souboru Sync replikuje všechna data z existující sdílené složky do Azure Files.
* **[Azure Import/Export](../common/storage-import-export-service.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)**: Azure Import/Export služba umožňuje bezpečně přenáší velké objemy dat do služby Azure sdílenou přesouvání pevných disků o datovém centru Azure. 
* **[Robocopy](https://technet.microsoft.com/library/cc733145.aspx)**: Robocopy je dobře známé kopie nástroj, který se dodává s Windows a Windows Server. Robocopy může sloužit k přenosu dat do Azure souborů připojení sdílené složky místně a následným použitím připojené umístění jako cíl v příkazu Robocopy.
* **[AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#upload-files-to-an-azure-file-share)**: AzCopy je nástroj příkazového řádku pro kopírování dat do a z Azure Files, jakož i úložiště objektů Blob v Azure pomocí jednoduchých příkazů optimální výkon. AzCopy je k dispozici pro systém Windows a Linux.

## <a name="next-steps"></a>Další postup
* [Plánování nasazení Azure File synchronizace](storage-sync-files-planning.md)
* [Soubory nasazení Azure](storage-files-deployment-guide.md)
* [Nasazení Azure File synchronizace](storage-sync-files-deployment-guide.md)