---
title: Seznámení se Soubory Azure | Dokumentace Microsoftu
description: Přehled služby Soubory Azure, která umožňuje vytvářet a používat sdílené složky souborů sítě v cloudu s využitím standardního průmyslového protokolu SMB.
services: storage
author: RenaShahMSFT
ms.service: storage
ms.topic: overview
ms.date: 07/19/2018
ms.author: renash
ms.component: files
ms.openlocfilehash: d42c3e665b4ed3057d340dce33145f8c08e69888
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2018
ms.locfileid: "49945769"
---
# <a name="what-is-azure-files"></a>Co je služba Soubory Azure?
Azure Files nabízí plně spravované sdílené složky v cloudu, které jsou přístupné přes standardní [protokol Server Message Block (SMB)](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx). Sdílené složky Azure je možné připojit současně do cloudových i místních nasazení systémů Windows, Linux a macOS. Sdílené složky Azure je navíc možné ukládat do mezipaměti ve Windows Serveru pomocí Synchronizace souborů Azure, aby byly rychle přístupné poblíž místa, kde se data používají.

## <a name="videos"></a>Videa
| Představujeme Synchronizaci souborů Azure (2 min) | Soubory Azure se synchronizací (Ignite 2017) (85 min)  |
|-|-|
| [![Záznam dění na monitoru videa Představujeme Synchronizaci souborů Azure – kliknutím zahájíte přehrávání](./media/storage-files-introduction/azure-file-sync-video-snapshot.png)](https://www.youtube.com/watch?v=Zm2w8-TRn-o) | [![Záznam dění na monitoru prezentace Soubory Azure se synchronizací – kliknutím zahájíte přehrávání](./media/storage-files-introduction/azure-files-ignite-2017-video.png)](https://www.youtube.com/watch?v=r26jWDGF_rg) |

## <a name="why-azure-files-is-useful"></a>Proč je služba Soubory Azure užitečná
Sdílené složky Azure lze použít k těmto činnostem:

* **Nahrazení nebo doplnění místních souborových serverů:**  
    Službu Soubory Azure je možné použít k úplnému nahrazení nebo doplnění tradičních místních souborových serverů nebo zařízení NAS. Oblíbené operační systémy jako Windows, macOS a Linux můžou sdílené složky Azure přímo připojit, bez ohledu na to, kde na světě se nacházejí. Sdílené složky Azure lze také pomocí Synchronizace souborů Azure replikovat na místní nebo cloudové servery Windows Server. Tím se zajistí výkonné a distribuované ukládání dat do mezipaměti v místě, kde se tato data používají.

* **Migrace aplikací metodou „lift and shift“:**  
    Služba Soubory Azure usnadňuje migraci aplikací, které očekávají uchovávání souborové aplikace nebo uživatelských dat ve sdílené složce, do cloudu metodou „lift and shift“. Služba Soubory Azure podporuje klasický scénář migrace metodou „lift and shift“, při které se do Azure přesouvá aplikace i její data, i hybridní scénář migrace metodou „lift and shift“, při které se data aplikace přesouvají do služby Soubory Azure a aplikace se nadále spouští místně. 

* **Zjednodušení vývoje pro cloud:**  
    S použitím služby Soubory Azure je také možné nejrůznějšími způsoby zjednodušit nové projekty vývoje pro cloud. Příklad:
    * **Sdílená nastavení aplikací:**  
        Běžným vzorem pro distribuované aplikace je mít konfigurační soubory v centralizovaném umístění, kde k nim může přistupovat mnoho instancí aplikací. Instance aplikací můžou načítat vlastní konfiguraci prostřednictvím souborového rozhraní REST API a uživatelé k nim můžou podle potřeby přistupovat připojením sdílené složky SMB v místním prostředí.

    * **Sdílená složka pro diagnostiku:**  
        Sdílená složka Azure je pro cloudové aplikace vhodným místem pro zápis protokolů, metrik a výpisů paměti. Instance aplikací můžou zapisovat protokoly prostřednictvím souborového rozhraní REST API a vývojáři k nim můžou přistupovat připojením sdílené složky na svém místním počítači. To umožňuje velkou flexibilitu, protože se vývojáři můžou pustit do vývoje pro cloud bez nutnosti opouštět stávající nástroje, které znají a mají rádi.

    * **Vývoj, testování a ladění:**  
        Vývojáři nebo správci při práci na virtuálních počítačích v cloudu často potřebují sadu nástrojů nebo pomůcek. Kopírování takových pomůcek a nástrojů na každý virtuální počítač může být časově náročné. Díky místnímu připojení sdílené složky Azure na virtuálních počítačích můžou vývojáři i správci rychle přistupovat ke svým nástrojům, aniž by museli provádět kopírování.

## <a name="key-benefits"></a>Klíčové výhody
* **Sdílený přístup**. Sdílené složky Azure podporují standardní oborový protokol SMB, takže můžete bez problémů nahradit místní sdílené složky za sdílené složky Azure, aniž by bylo potřeba řešit kompatibilitu aplikací. Schopnost sdílet systém souborů mezi několika počítači, aplikacemi a instancemi představuje výraznou výhodu služby Soubory Azure pro aplikace potřebující možnost sdílení. 
* **Plně spravovaná**. Sdílené složky Azure je možné vytvářet bez nutnosti spravovat hardware nebo operační systém. To znamená, že se nemusíte starat o opravy operačního systému serveru pomocí důležitých upgradů zabezpečení ani o nahrazování vadných pevných disků.
* **Skriptování a nástroje**. Rutiny PowerShellu a Azure CLI je možné použít k vytváření, připojování a správě sdílených složek Azure v rámci správy aplikací Azure. Sdílené složky Azure můžete vytvářet a spravovat pomocí webu Azure Portal a Průzkumníka služby Azure Storage. 
* **Odolnost**. Služba Soubory Azure je od základu vytvořena tak, aby byla vždy dostupná. Nahrazení místních sdílených složek za Soubory Azure znamená, že už se nebudete muset probouzet kvůli řešení místních výpadků napájení nebo problémů se sítí. 
* **Známá programovatelnost**. Aplikace spuštěné v Azure můžou k datům ve sdílené složce přistupovat přes [rozhraní API pro vstup/výstup souborového systému](https://msdn.microsoft.com/library/system.io.file.aspx). Vývojáři tedy můžou využít svoje dovednosti a znalosti kódu při migraci stávajících aplikací. Kromě rozhraní API pro vstup/výstup systému můžete použít [klientské knihovny pro Azure Storage](https://msdn.microsoft.com/library/azure/dn261237.aspx) nebo [rozhraní REST API pro Azure Storage](/rest/api/storageservices/file-service-rest-api).

## <a name="next-steps"></a>Další kroky
* [Vytvoření sdílené složky Azure](storage-how-to-create-file-share.md)
* [Připojení v systému Windows](storage-how-to-use-files-windows.md)
* [Připojení v systému Linux](storage-how-to-use-files-linux.md)
* [Připojení v systému macOS](storage-how-to-use-files-mac.md)