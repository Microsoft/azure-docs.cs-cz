---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 11/04/2020
ms.author: inhenkel
ms.custom: portal
ms.openlocfilehash: c3a27218f03980e9b42a4a56fa739203c43754a0
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2020
ms.locfileid: "96013219"
---
<!--Set the encryption on storage account in the portal-->

## <a name="set-the-encryption-on-a-storage-account"></a>Nastavení šifrování v účtu úložiště

1. V Azure Portal zadejte název účtu úložiště, který chcete zašifrovat, do **vyhledávacího** pole v horní části obrazovky.  Shody se zobrazí pod polem hledání.
1. Vyberte účet úložiště, který hledáte. Zobrazí se obrazovka účtu úložiště.
1. Vyberte **šifrování**.
1. Vyberte buď spravované klíče společnosti Microsoft, nebo klíče spravované zákazníky.

### <a name="use-microsoft-managed-keys"></a>Použití klíčů spravovaných Microsoftem

Ve výchozím nastavení se data v účtu úložiště šifrují pomocí spravovaných klíčů společnosti Microsoft.

### <a name="use-customer-managed-keys"></a>Používání klíčů spravovaných zákazníky

1. Vyberte **klíče spravované zákazníkem**.
1. Vyberte buď **zadat identifikátor URI klíče** , nebo **Vyberte z trezoru klíčů**.
    1. Pokud vyberete **zadat identifikátor URI klíče**, zadejte identifikátor URI klíče do pole identifikátor URI klíče a vyberte předplatné. (Pro vás už může být vybraná.)
    1. Pokud vyberete **možnost vybrat z trezoru klíčů**, pak vyberete možnost **Vybrat Trezor klíčů a klíč**. Zobrazí se obrazovka vybrat klíč z Azure Key Vault.
1. Vyberte **Key Vault** , kterou chcete použít, a vyberte klíč, který už máte v trezoru klíčů, nebo **vytvořte nový klíč**.
    1. Pokud se rozhodnete vytvořit nový klíč, vyberte v rozevíracím seznamu **Možnosti** možnost **Generovat** nebo **importovat** . Můžete importovat jenom klíče RSA.
    1. Pokud chcete vygenerovat nový klíč, zadejte název klíče do pole **název** a pak vyberte typ klíče:
        1. Velikosti klíčů RSA: 2048, 3072/95 nebo 4096. To si vyberou nejvíc zákazníci.
        1. ES-eliptický název křivky: P-256, P-384, P-521 nebo P-256
        1. Volitelně můžete nastavit datum aktivace a vypršení platnosti klíče.
        1. Vyberte **Ano** , pokud chcete povolit automatické střídání klíčů.
        1. Vyberte **Vytvořit**.
    1. Pokud chcete importovat klíč, vyberte soubor k nahrání Kliknutím kamkoli v **poli vybrat soubor**.
        1. Do pole **název** zadejte název klíče.
        1. Volitelně můžete nastavit datum aktivace a vypršení platnosti klíče.
        1. Vyberte **Ano** , pokud chcete povolit automatické střídání klíčů.
        1. Vyberte **Vytvořit**.
    1. Vyberte **Vybrat** a vyberte tento klíč k zašifrování svého účtu úložiště. Vrátíte se zpátky na obrazovku šifrování.
1. **VÝZNAMNÁ!** Vyberte **Uložit** a uložte nastavení šifrování, nebo všechno, co jste právě udělali, se ztratí.
