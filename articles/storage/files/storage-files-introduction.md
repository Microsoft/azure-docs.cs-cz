---
title: Seznámení se Soubory Azure | Dokumentace Microsoftu
description: Přehled služby Soubory Azure, která umožňuje vytvářet a používat sdílené složky souborů sítě v cloudu s využitím standardního průmyslového protokolu SMB.
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 09/15/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: f0dcd763240205bd396fc8cd0301c2046098473b
ms.sourcegitcommit: 3af12dc5b0b3833acb5d591d0d5a398c926919c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2021
ms.locfileid: "98070129"
---
# <a name="what-is-azure-files"></a>Co je Azure Files?
Soubory Azure nabízí plně spravované sdílené složky v cloudu, které jsou přístupné prostřednictvím standardního standardního [protokolu SMB (Server Message Block)](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) nebo [protokolu NFS (Network File System)](https://en.wikipedia.org/wiki/Network_File_System). Sdílené složky Azure je možné připojit souběžně pomocí cloudu nebo místních nasazení. Sdílené složky SMB souborů protokolu SMB jsou dostupné z klientů s Windows, Linuxem a macOS. Sdílené složky souborů NFS služby Azure jsou přístupné z klientů se systémem Linux nebo macOS. Kromě toho se sdílené složky SMB souborů protokolu SMB můžou ukládat do mezipaměti na serverech Windows s Azure File Sync pro rychlý přístup poblíž místa, kde se data používají.

## <a name="videos"></a>Videa
| Představujeme Azure File Sync | Soubory Azure se synchronizací (Ignite 2019)  |
|-|-|
| [![Záznam dění na indikátoru obrazovky Představujeme Azure File Sync Video – kliknutím zahrajete.](./media/storage-files-introduction/azure-file-sync-video-snapshot.png)](https://www.youtube.com/watch?v=Zm2w8-TRn-o) | [![Záznam dění na záznamovém pracovním souboru Azure se synchronizací prezentace – Kliknutím spustíte přehrávání.](./media/storage-files-introduction/ignite-2018-video.png)](https://www.youtube.com/embed/6E2p28XwovU) |

Tady jsou některá videa o běžných případech použití souborů Azure:
* [Výměna souborového serveru se sdílenou složkou Azure bez serveru](https://sec.ch9.ms/ch9/3358/0addac01-3606-4e30-ad7b-f195f3ab3358/ITOpsTalkAzureFiles_high.mp4)
* [Začínáme s kontejnery profilů FSLogix ve službě soubory Azure ve virtuální ploše systému Windows využití ověřování AD](https://www.youtube.com/embed/9S5A1IJqfOQ)

## <a name="why-azure-files-is-useful"></a>Proč je služba Soubory Azure užitečná
Sdílené složky Azure lze použít k těmto činnostem:

* **Nahrazení nebo doplnění místních souborových serverů:**  
    Službu Soubory Azure je možné použít k úplnému nahrazení nebo doplnění tradičních místních souborových serverů nebo zařízení NAS. Oblíbené operační systémy jako Windows, macOS a Linux můžou sdílené složky Azure přímo připojit, bez ohledu na to, kde na světě se nacházejí. Sdílené složky SMB souborů Azure je také možné replikovat pomocí Azure File Sync na servery Windows, a to buď místně, nebo v cloudu, pro výkon a distribuované ukládání dat do mezipaměti, kde se používají. S nejnovější verzí [ověřování Azure File AD](storage-files-active-directory-overview.md)můžou sdílené složky SMB souborů Azure dál spolupracovat se službou AD hostované místně pro řízení přístupu. 

* **Aplikace typu "výtah a Shift"**:  
    Služba Soubory Azure usnadňuje migraci aplikací, které očekávají uchovávání souborové aplikace nebo uživatelských dat ve sdílené složce, do cloudu metodou „lift and shift“. Služba Soubory Azure podporuje klasický scénář migrace metodou „lift and shift“, při které se do Azure přesouvá aplikace i její data, i hybridní scénář migrace metodou „lift and shift“, při které se data aplikace přesouvají do služby Soubory Azure a aplikace se nadále spouští místně. 

* **Zjednodušení vývoje pro Cloud**:  
    S použitím služby Soubory Azure je také možné nejrůznějšími způsoby zjednodušit nové projekty vývoje pro cloud. Například:
    * **Nastavení sdílené aplikace**:  
        Běžným vzorem pro distribuované aplikace je mít konfigurační soubory v centralizovaném umístění, kde k nim může přistupovat mnoho instancí aplikací. Instance aplikací můžou načítat vlastní konfiguraci prostřednictvím souborového rozhraní REST API a uživatelé k nim můžou podle potřeby přistupovat připojením sdílené složky SMB v místním prostředí.

    * **Diagnostická sdílená složka**:  
        Sdílená složka Azure je pro cloudové aplikace vhodným místem pro zápis protokolů, metrik a výpisů paměti. Instance aplikací můžou zapisovat protokoly prostřednictvím souborového rozhraní REST API a vývojáři k nim můžou přistupovat připojením sdílené složky na svém místním počítači. To umožňuje velkou flexibilitu, protože se vývojáři můžou pustit do vývoje pro cloud bez nutnosti opouštět stávající nástroje, které znají a mají rádi.

    * **Vývoj, testování a ladění:**  
        Vývojáři nebo správci při práci na virtuálních počítačích v cloudu často potřebují sadu nástrojů nebo pomůcek. Kopírování takových pomůcek a nástrojů na každý virtuální počítač může být časově náročné. Díky místnímu připojení sdílené složky Azure na virtuálních počítačích můžou vývojáři i správci rychle přistupovat ke svým nástrojům, aniž by museli provádět kopírování.
* **Kontejner**:  
    Sdílené složky Azure je možné použít jako trvalé svazky pro stavové kontejnery. Kontejnery dodávají "sestavování" jednou a spustí možnosti, které vývojářům umožňují zrychlit inovace. V případě kontejnerů, které při každém spuštění přistupují k nezpracovaným datům, se vyžaduje sdílený systém souborů, který umožňuje těmto kontejnerům přístup k systému souborů bez ohledu na to, ve které instanci se spouštějí.

## <a name="key-benefits"></a>Klíčové výhody
* **Sdílený přístup**. Sdílené složky Azure podporují standardní oborové protokoly SMB a NFS, což znamená, že můžete bezproblémově nahradit místní sdílené složky sdílenými složkami Azure, aniž byste se museli starat o kompatibilitu aplikací. Schopnost sdílet systém souborů mezi několika počítači, aplikacemi a instancemi představuje výraznou výhodu služby Soubory Azure pro aplikace potřebující možnost sdílení. 
* **Plně spravovaná**. Sdílené složky Azure je možné vytvářet bez nutnosti spravovat hardware nebo operační systém. To znamená, že se nemusíte starat o opravy operačního systému serveru pomocí důležitých upgradů zabezpečení ani o nahrazování vadných pevných disků.
* **Skriptování a nástroje**. Rutiny PowerShellu a Azure CLI je možné použít k vytváření, připojování a správě sdílených složek Azure v rámci správy aplikací Azure. Sdílené složky Azure můžete vytvářet a spravovat pomocí Azure Portal a Průzkumník služby Azure Storage. 
* **Odolnost** proti chybám. Služba Soubory Azure je od základu vytvořena tak, aby byla vždy dostupná. Nahrazení místních sdílených složek za Soubory Azure znamená, že už se nebudete muset probouzet kvůli řešení místních výpadků napájení nebo problémů se sítí. 
* **Známá programovatelnost**. Aplikace spuštěné v Azure můžou k datům ve sdílené složce přistupovat přes [rozhraní API pro vstup/výstup souborového systému](/dotnet/api/system.io.file). Vývojáři tedy můžou využít svoje dovednosti a znalosti kódu při migraci stávajících aplikací. Kromě rozhraní API pro vstup/výstup systému můžete použít [klientské knihovny pro Azure Storage](/previous-versions/azure/dn261237(v=azure.100)) nebo [rozhraní REST API pro Azure Storage](/rest/api/storageservices/file-service-rest-api).

## <a name="next-steps"></a>Další kroky
* [Další informace o dostupných protokolech sdílení souborů](storage-files-compare-protocols.md)
* [Vytvoření sdílené složky Azure](storage-how-to-create-file-share.md)
* [Připojení a připojení sdílené složky SMB ve Windows](storage-how-to-use-files-windows.md)
* [Připojení a připojení sdílené složky SMB v systému Linux](storage-how-to-use-files-linux.md)
* [Připojení a připojení sdílené složky SMB na macOS](storage-how-to-use-files-mac.md)
* [Postup vytvoření sdílené složky systému souborů NFS](storage-files-how-to-create-nfs-shares.md)
