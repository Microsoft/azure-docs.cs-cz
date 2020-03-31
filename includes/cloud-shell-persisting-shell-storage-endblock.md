---
author: cynthn
ms.service: azure
ms.topic: include
ms.date: 11/09/2018
ms.author: cynthn
ms.openlocfilehash: c2ed33aea77b5478e8d17f6bd0213ef3e778b806
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "67175344"
---
## <a name="transfer-local-files-to-cloud-shell"></a>Přenos místních souborů do prostředí Cloud Shell
Adresář `clouddrive` se synchronizuje s rozhraním úložiště portálu Azure. Toto okno slouží k přenosu místních souborů do sdílené složky nebo ze sdílené složky. Aktualizace souborů z prostředí Cloud Shell se projeví v grafickém uživatelském rozhraní úložiště souborů při aktualizaci okna.

### <a name="download-files"></a>Stažení souborů

![Seznam místních souborů](../articles/cloud-shell/media/persisting-shell-storage/download.png)
1. Na webu Azure Portal přejděte na připojenou sdílenou složku.
2. Vyberte cílový soubor.
3. Vyberte tlačítko **Stáhnout.**

### <a name="upload-files"></a>Nahrání souborů

![Místní soubory, které mají být nahrány](../articles/cloud-shell/media/persisting-shell-storage/upload.png)
1. Přejděte do připojené sdílené složky.
2. Vyberte tlačítko **Nahrát**.
3. Vyberte soubor nebo soubory, které chcete nahrát.
4. Potvrďte nahrávání.

Nyní byste měli vidět soubory, `clouddrive` které jsou přístupné ve vašem adresáři v Cloud Shell.