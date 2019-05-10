---
title: Plánování nasazení služby soubory Azure | Dokumentace Microsoftu
description: Zjistěte, co vzít v úvahu při plánování nasazení služby soubory Azure.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 04/25/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 82c0aa53fa8905e0e58cb784a478ade474ec5601
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2019
ms.locfileid: "65232775"
---
# <a name="planning-for-an-azure-files-deployment"></a>Plánování nasazení služby Soubory Azure

[Služba soubory Azure](storage-files-introduction.md) nabízí plně spravované sdílené složky v cloudu, které jsou přístupné přes standardní protokol SMB. Protože soubory Azure je plně spravovaná, jeho nasazení v produkčních scénářích je mnohem jednodušší než nasazení a Správa souborového serveru nebo zařízení NAS. Tento článek se zabývá témata, které je třeba zvážit při nasazování sdílené složky Azure pro použití v produkčním prostředí v rámci vaší organizace.

## <a name="management-concepts"></a>Koncepty správy

 Následující diagram znázorňuje konstrukce správu soubory Azure:

![Struktura souborů](./media/storage-files-introduction/files-concepts.png)

* **Účet úložiště**: Veškerý přístup ke službě Azure Storage se provádí prostřednictvím účtu úložiště. Podrobné informace o kapacitě účtu úložiště najdete v článku [Škálovatelnost a cíle výkonosti](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

* **Sdílená složka**: Sdílená složka File Storage je sdílené složky protokolu SMB v Azure. Všechny adresáře a soubory musí být vytvořeny v nadřazené sdílené složce. Účet může obsahovat neomezený počet sdílených složek a sdílené složky můžete ukládat neomezený počet souborů až 5 TB celkové kapacity sdílené složky.

* **Adresář**: Volitelná hierarchie adresářů.

* **Soubor**: Soubor ve sdílené složce. Soubor může mít velikost až 1 TB.

* **Formát adresy URL**: Pro požadavky na sdílené složky Azure vytvořené pomocí protokolu REST soubor soubory jsou adresovatelné v následujícím formátu adresy URL:

    ```
    https://<storage account>.file.core.windows.net/<share>/<directory>/<file>
    ```

## <a name="data-access-method"></a>Přístupová metoda k datům

Soubory Azure nabízí dva, integrované a pohodlný přístup k datům přistupovat ke svým datům můžete použít samostatně nebo v kombinaci s kolegy a metod:

1. **Přímý přístup do cloudu**: Všechny sdílené složky Azure je možné připojit [Windows](storage-how-to-use-files-windows.md), [macOS](storage-how-to-use-files-mac.md), a/nebo [Linux](storage-how-to-use-files-linux.md) protokolem oborový standard zprávy bloku SMB (Server) nebo prostřednictvím souborového rozhraní REST API. Přes protokol SMB čtení a zápis do souborů ve sdílené složce se provádějí přímo na sdílenou složku v Azure. Připojte virtuální počítač v Azure, klient SMB v operačním systému musí podporovat alespoň SMB 2.1. Připojit v místním prostředí, například na pracovní stanici uživatele, klient SMB podporuje pracovní stanici musí podporovat alespoň protokolu SMB 3.0 (pomocí šifrování). Kromě protokolu SMB nové aplikace nebo služby může přímo ke sdílené složce přistupovat přes REST soubor, který poskytuje snadný a škálovatelný aplikační programovací rozhraní pro vývoj softwaru.
2. **Azure File Sync**: Sdílených složek pomocí služby Azure File Sync, je možné replikovat do Windows servery místně nebo v Azure. Uživatelé by přístup ke sdílené složce pomocí Windows serveru, jako prostřednictvím do sdílené složky SMB nebo NFS. To je užitečné pro scénáře, ve kterých by se data budou přistupovat a upravit daleko od datového centra Azure, jako například v případě scénáře firemní pobočky. Data může replikovat mezi několik koncových bodů Windows Server, například mezi několik poboček. Nakonec dat může být rozvrstvena do služby soubory Azure tak, že všechna data jsou stále přístupné prostřednictvím serveru, ale Server nemá úplná kopie dat. Místo toho je bezproblémově navrátit data při otevření vaší uživatelem.

Následující tabulka ukazuje, jak uživatelé a aplikace můžete získat přístup k vaší sdílené složky Azure:

| | Přímý přístup do cloudu | Synchronizace souborů Azure |
|------------------------|------------|-----------------|
| Jaké protokoly budete muset použít? | Služba soubory Azure podporuje SMB 2.1, protokolu SMB 3.0 a souborového rozhraní REST API. | Přístup ke sdílené složky Azure přes všechny podporované protokoly na Windows serveru (SMB, NFS, FTP atd.) |  
| Kde jsou spuštěná vaše úloha? | **V Azure**: Služba soubory Azure nabízí přímý přístup k vašim datům. | **Místní s pomalým síťovým**: Klienti Windows, Linux a macOS můžete připojit místní Windows sdílenou složku jako rychlou mezipaměť sdílené složky Azure. |
| Jaké úroveň seznamy řízení přístupu je potřeba? | Sdílené složky a souboru úroveň. | Sdílená složka, soubor a uživatelské úrovni. |

## <a name="data-security"></a>Zabezpečení dat

Služba soubory Azure obsahuje celou řadu integrovaných možností pro zajištění zabezpečení dat:

* Podpora pro šifrování v obou selhání přenosu protokolů: Šifrování SMB 3.0 a soubor REST přes protokol HTTPS. Ve výchozím nastavení: 
    * Klienti, kteří podporují šifrování protokolu SMB 3.0 odesílat a přijímat data přes zašifrovaný kanál.
    * Klienti, kteří nepodporují šifrování protokolu SMB 3.0 můžou komunikovat uvnitř datacentra přes protokol SMB 2.1 nebo SMB 3.0 bez šifrování. Klienti SMB nejsou povoleny pro komunikaci mezi virtuálními sítěmi datového centra pomocí protokolu SMB 2.1 nebo SMB 3.0 bez šifrování.
    * Klienti mohou komunikovat přes soubor klidovém stavu pomocí protokolu HTTP nebo HTTPS.
* Šifrování v klidovém stavu ([šifrování služby Azure Storage](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)): Šifrování služby Storage (SSE) je povolený pro všechny účty úložiště. Data v klidovém stavu zašifrovaná pomocí plně spravované klíče. Šifrování v klidovém stavu nezahrnuje zvýšit náklady na úložiště a snížit výkon. 
* Volitelný požadavek šifrovaných dat v cestě: Pokud je vybráno, odmítne soubory Azure k datům přes nezašifrované kanály. Konkrétně jsou povoleny pouze HTTPS a SMB 3.0 s šifrování připojení.

    > [!Important]  
    > Vyžaduje zabezpečený přenos dat způsobí, že starší klienty SMB není schopné komunikovat s protokolem SMB 3.0 se šifrováním nezdaří. Další informace najdete v tématu [připojení v Windows](storage-how-to-use-files-windows.md), [připojení v systému Linux](storage-how-to-use-files-linux.md), a [připojení v systému macOS](storage-how-to-use-files-mac.md).

Z důvodu maximálního zabezpečení důrazně doporučujeme vždy povolení i šifrování v klidovém stavu a povolení šifrování dat během přenosu pokaždé, když používáte moderní klientům přístup k datům. Například pokud je potřeba připojit sdílenou složku na serveru 2008 R2 virtuálního počítače s Windows, která podporuje jenom protokol SMB 2.1, musíte povolit nešifrované přenosy do vašeho účtu úložiště, protože protokol SMB 2.1 nepodporuje šifrování.

Pokud používáte sdílenou složkou Azure přístup k Azure File Sync, vždy použijeme HTTPS a SMB 3.0 s šifrováním k synchronizaci dat na servery Windows, bez ohledu na to, zda potřebujete šifrování dat v klidovém stavu.

## <a name="file-share-performance-tiers"></a>Úrovně výkonu sdílené složky souborů

Služba soubory Azure nabízí dvě úrovně výkonu: standard a premium.

* **Standardní sdílené složky** se zálohují na rotační pevných disků (HDD), které poskytují spolehlivý výkon vstupně-výstupních operací úloh, které jsou méně citlivé na variabilitu výkonu, jako je například pro obecné účely sdílené složky a prostředí pro vývoj/testování. Standardní sdílené složky jsou dostupné pouze v modelu s průběžnými platbami fakturace.
* **Premium sdílené složky (preview)** se zálohují na discích SSD (Solid-State Drive), které poskytují konzistentní vysoký výkon a nízkou latencí v řádu milisekund pro většinu operací vstupně-výstupních operací pro maximum úlohy náročné na vstupně-výstupních operací. Díky tomu je vhodné pro celou řadu úloh, jako jsou databáze, hostování webové stránky, vývojová prostředí atd. Sdílené složky Premium jsou dostupné jenom v zřízené model fakturace. Premium sdílených složek pomocí modelu nasazení, která je oddělená od standardní sdílené složky.

Azure Backup je dostupná pro sdílené složky premium a Azure Kubernetes Service podporuje sdílené složky premium ve verzi 1.13 a vyšší.

Pokud chcete další informace o vytvoření sdílené složky premium, najdete v našem článku k tomuto tématu: [Postup vytvoření účtu služby Azure premium storage soubor](storage-how-to-create-premium-fileshare.md).

V současné době nelze převést přímo mezi standardní sdílené složky a sdílené složky premium. Pokud chcete přepnout na buď vrstvy, musíte vytvořit nové sdílené složky v dané úrovni a ručně zkopírujte data z původní sdílené složky do nové sdílené složky, kterou jste vytvořili. Provedete to jedním z nástroje pro kopírování nepodporuje soubory Azure, například AzCopy.

> [!IMPORTANT]
> Premium sdílené složky jsou stále ve verzi preview, k dispozici pouze při použití úložiště LRS a jsou k dispozici ve většině oblastí, které nabízejí účty úložiště. Pokud jsou premium sdílené složky ve vaší oblasti aktuálně k dispozici, najdete v tématu [dostupné produkty v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/?products=storage) stránky pro Azure.

### <a name="provisioned-shares"></a>Zřízené sdílené složky

Premium sdílené složky (preview) se zřizují podle pevný poměr GiB/IOPS a propustnosti. Pro každý GiB zřízené sdílené složky budou vydány lístky jeden IOPS a propustnost 0,1 MiB/s až po maximální limity jednotlivou sdílenou složku. Minimální povolená zřizování je 100 GB s minimální IOPS a propustnosti.

Na jak kapacita systému dovolí můžete převést všechny sdílené složky až tři vstupně-výstupních operací za GiB zřízeném úložišti po dobu 60 minut nebo i delší dobu v závislosti na velikosti sdílené složky. Nových sdílených složek začněte s kreditem úplné burst na základě zřízené kapacity.

Sdílené složky musí být zřízený v přírůstcích po 1 GB. Minimální velikost je 100 GB, nejbližší velikost 101 GiB a tak dále.

> [!TIP]
> Standardní hodnoty vstupně-výstupních operací = 1 * zřízené GiB. (Až max. 100 000 IOPS).
>
> Burst Limit = 3 * Baseline IOPS. (Až max. 100 000 IOPS).
>
> frekvence odchozího přenosu dat = 60 MiB/s + 0,06 * zřízené GiB
>
> rychlost příchozího přenosu dat = 40 MiB/s + 0,04 * zřízené GiB

Velikost sdílené složky je možné kdykoli zvýšit, ale lze snížit až po 24 hodinách od poslední zvýšení. Po uplynutí 24 hodin bez zvýšení velikosti, může snížit velikost sdílené složky tolikrát, kolikrát chcete, dokud znovu zvýšit. IOPS a propustnosti škálování změny bude platit za pár minut po provedení změny velikosti.

Je možné zmenšit velikost vašeho zřízené sdílené složky pod používané GiB. Pokud to uděláte, nedojde ke ztrátě dat. ale budou dál účtovat velikost použitou a přijímat výkonu (standardní hodnoty vstupně-výstupních operací, propustnosti a shluků vstupně-výstupních operací) zřízené sdílené složky, nikoli velikost použít.

Následující tabulka ukazuje několik příkladů tyto vzorce pro velikosti zřízené sdílené složky:

|Kapacita (GB) | Standardní hodnoty vstupně-výstupních operací | Vstupně-výstupních operací datové sekvence | Výchozí přenos dat (MiB/s) | Příchozí přenos dat (MiB/s) |
|---------|---------|---------|---------|---------|
|100         | 100     | Až 300     | 66   | 44   |
|500         | 500     | Až 1 500   | 90   | 60   |
|1,024       | 1,024   | Až 3,072   | 122   | 81   |
|5,120       | 5,120   | Až 15,360  | 368   | 245   |
|10,240      | 10,240  | 30 až 720  | 675 | 450   |
|33,792      | 33,792  | Až 100 000 | 2,088 | 1,392   |
|51,200      | 51,200  | Až 100 000 | 3,132 | 2,088   |
|102,400     | 100 000 | Až 100 000 | 6,204 | 4,136   |

### <a name="bursting"></a>Shlukování

Premium sdílené složky můžete převést jejich vstupně-výstupních operací až faktor tři. Shlukování je automatizovaná a funguje podle platební systém. Shlukování funguje jak kapacita systému dovolí a burst limit není zárukou, sdílené složky můžete burst *až* limit.

Pokaždé, když se provoz pro svou sdílenou složku je nižší než standardní hodnoty vstupně-výstupních operací se začnou hromadit ve shluku kbelíku kredity. Sdílenou složku 100 GB má například 100 standardní hodnoty vstupně-výstupních operací. Pokud skutečný provoz ve sdílené složce se 40 vstupně-výstupních operací pro konkrétní interval 1 sekundu, 60 nevyužité vstupně-výstupních operací se kompenzací burst kbelík. Tyto kredity se potom použije později při operace by došlo k vstupně-výstupních operací směrného plánu.

> [!TIP]
> Velikost kbelíku burst = vstupně-výstupních operací směrného plánu * 2 * 3600.

Pokaždé, když se sdílenou složku překračuje základní vstupně-výstupních operací a má Kredity v intervalu burst, bude rozšíření. Sdílené složky můžete nadále burst tak dlouho, dokud se zbývající kredity, ale menší než 50 TiB sdílené složky zůstanou pouze na burst limit až na jednu hodinu. Sdílené složky, které jsou větší než 50 TiB technicky může překročit tento limit jednu hodinu, až dvě hodiny, ale to je na základě počtu kreditů burst operace, které. Každý vstupně-výstupní operace nad rámec standardních hodnot IOPS spotřebovává jednoho kredity a jakmile jsou využité všechny kredity sdílenou složku by vrátil se standardními hodnotami vstupně-výstupních operací.

Kredity sdílené složky mají tři stavy:

- Účtují, když sdílení souborů používá méně než standardní hodnoty vstupně-výstupních operací.
- Odmítnutí, pokud shlukování sdílené složky.
- Zbývající konstantní, když nejsou žádná kredity nebo standardní hodnoty konfigurace vstupně-výstupních operací se používají.

Nové spuštění sdílené složky souboru s úplnou počtem Kredity v jeho burst intervalu Kredity Burst nebude operace, pokud sdílenou složku vstupně-výstupních operací klesnou pod standardních hodnot IOPS kvůli omezování serverem.

## <a name="file-share-redundancy"></a>Zálohování sdílené složky souboru

Soubory standardní sdílené složky Azure podporuje tři možnosti redundance dat: místně redundantní úložiště (LRS), zónově redundantní úložiště (ZRS) a geograficky redundantní úložiště (GRS).

Azure premium soubory se sdílí jenom podporuje místně redundantní úložiště (LRS).

Následující části popisují rozdíly mezi možnostmi různých redundance:

### <a name="locally-redundant-storage"></a>(Locally redundant storage) Místně redundantní úložiště

[!INCLUDE [storage-common-redundancy-LRS](../../../includes/storage-common-redundancy-LRS.md)]

### <a name="zone-redundant-storage"></a>Zónově redundantní úložiště

[!INCLUDE [storage-common-redundancy-ZRS](../../../includes/storage-common-redundancy-ZRS.md)]

### <a name="geo-redundant-storage"></a>Geograficky redundantní úložiště

> [!Warning]  
> Pokud používáte sdílené složky Azure jako koncový bod cloudu v účtu úložiště GRS, nesmí iniciovat převzetí služeb při selhání úložiště účtu. To způsobí neočekávané ztráty dat v případě nově vrstvené soubory příčina synchronizaci zastavit pracovní a může také. V případě ztráty určitá oblast Azure Microsoft aktivuje úložiště účtu převzetí služeb při selhání způsobem, který je kompatibilní s Azure File Sync.

Geograficky redundantní úložiště (GRS) je navržené pro poskytování 99,99999999999999 % (16. 9) odolnosti objektů v průběhu daného roku díky replikuje vaše data do sekundární oblasti, která je stovky mil od primární oblasti. Pokud má váš účet úložiště GRS povolena, vaše data byla odolná i v případě kompletní oblastnímu výpadku nebo havárii, ve kterém se obnovit primární oblast.

Pokud se rozhodnete pro geograficky redundantní úložiště jen pro čtení (RA-GRS), měli byste vědět, že Azure File nepodporuje geograficky redundantní úložiště jen pro čtení (RA-GRS) v libovolné oblasti v tuto chvíli. Sdílené složky v účtu úložiště RA-GRS fungovat stejně, jako kdyby je sdíleli do účtů GRS a jsou účtované ceny za GRS.

GRS replikuje vaše data do jiného datového centra v sekundární oblasti, ale, že data jsou k dispozici být jen pro čtení. Pokud Microsoft zahájí převzetí služeb při selhání z primární do sekundární oblasti.

Pro účet úložiště GRS povolené všechna data se replikují nejprve s místně redundantním úložištěm (LRS). Aktualizace se nejprve zaměřuje na primární umístění a replikovat pomocí LRS. Aktualizace se pak asynchronně replikuje do sekundární oblasti pomocí GRS. Data se zapisují do sekundárního umístění, se také replikuje do tohoto umístění použití úložiště LRS.

Primární a sekundární oblasti spravovat repliky v samostatných doménách selhání a upgradovacími doménami v jednotce škálování úložiště. Jednotka škálování úložiště je jednotka basic replikace v rámci datového centra. Replikace na této úrovni je poskytován LRS; Další informace najdete v tématu [místně redundantní úložiště (LRS): Redundanci dat s nízkými náklady pro službu Azure Storage](../common/storage-redundancy-lrs.md).

Mějte tyto body při rozhodování o možnosti replikace, kterou chcete použít:

* Zónově redundantní úložiště (ZRS) poskytuje vysokou dostupnost díky synchronní replikaci a může být lepší volbou pro některé scénáře než GRS. Další informace o ZÓNOVĚ najdete v tématu [ZRS](../common/storage-redundancy-zrs.md).
* Asynchronní replikace zahrnuje zpoždění od okamžiku, data se zapisují do primární oblasti do při replikaci do sekundární oblasti. Regionální havárie mohou být změny, které nebyly dosud replikují do sekundární oblasti ztraceny, pokud tato data nejde obnovit z primární oblasti.
* S GRS není k dispozici pro čtení nebo zápis repliky, pokud Microsoft nezahájí převzetí služeb při selhání do sekundární oblasti. V případě selhání budete přečetl(a) a přístup pro zápis k těmto datům po převzetí služeb byla dokončena. Další informace najdete v tématu [pokyny pro zotavení po havárii](../common/storage-disaster-recovery-guidance.md).

## <a name="data-growth-pattern"></a>Vzorek nárůstu dat

V současné době maximální velikost pro sdílené složky Azure je 5 TB (100 TB pro premium sdílené složky, které jsou ve verzi public preview). Z důvodu tímto aktuálním omezením bránit musíte zvážit očekávaný nárůst dat při nasazování sdílené složky Azure.

Je možné pro synchronizaci více sdílených složek Azure na jeden souborový server Windows pomocí služby Azure File Sync. To umožňuje zajistit, že starší, velké sdílené složky, že máte v místním může být přenesena do Azure File Sync. Další informace najdete v tématu [plánování nasazení služby Azure File Sync](storage-files-planning.md).

## <a name="data-transfer-method"></a>Jako metodu přenosu dat

Existuje mnoho možností snadno hromadné přenos dat z existujícího souboru sdílet, jako je například místní sdílenou složku, do soubory Azure. Několik oblíbených ty patří (mimo vyčerpávající seznam):

* **Azure File Sync**: Jako součást první synchronizace mezi sdílenými složkami Azure ("koncového bodu cloudu") a obor názvů adresář Windows ("koncový bod serveru") Azure File Sync replikuje všechna data z existující sdílené složky do služby soubory Azure.
* **[Azure Import/Export](../common/storage-import-export-service.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)**: Služba Azure Import/Export umožňuje bezpečně přenášet velké objemy dat do sdílené složky Azure přenosem pevných disků do datacentra Azure. 
* **[Příkaz Robocopy](https://technet.microsoft.com/library/cc733145.aspx)**: Příkaz Robocopy je dobře známé kopírování nástroj, který se dodává s Windows a Windows Server. Příkaz Robocopy můžou sloužit k připojení sdílené místně a následným použitím umístění připojené jako cíl v příkazu Robocopy přenášet data do soubory Azure.
* **[AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#upload-files-to-an-azure-file-share)**: AzCopy je nástroj příkazového řádku určený pro kopírování dat do a z Azure Files, jakož i úložiště objektů Blob v Azure pomocí jednoduchých příkazů s optimálním výkonem. AzCopy je k dispozici pro Windows a Linux.

## <a name="next-steps"></a>Další postup
* [Plánování nasazení služby Azure File Sync](storage-sync-files-planning.md)
* [Nasazení Azure Files](storage-files-deployment-guide.md)
* [Nasazení Azure File Sync](storage-sync-files-deployment-guide.md)
