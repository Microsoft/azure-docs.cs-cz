---
title: Přehled Microsoft Azure Stack Edge pro R | Microsoft Docs
description: Popisuje Azure Stack hraniční zařízení pro R, řešení úložiště pro vojenské aplikace, které používá fyzické zařízení pro přenos prostřednictvím sítě do Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 2ff07995cab2da6e53600be9bf60b30d1896cc9d
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2021
ms.locfileid: "102664498"
---
# <a name="what-is-the-azure-stack-edge-pro-r"></a>Co je Azure Stack Edge pro R?

Azure Stack Edge pro R je robustní, hraniční výpočetní zařízení navržené pro použití v prostředích Harsh. Azure Stack Edge pro R se doručuje jako řešení typu hardware jako služba. Společnost Microsoft si dodává cloudové zařízení, které funguje jako brána síťového úložiště, a má vestavěnou funkci grafického procesoru (GPU), která umožňuje akcelerovanou AI-Inferencing.

Tento článek vám poskytne přehled o řešení Azure Stack Edge pro R, klíčových funkcích a scénářích, kde můžete nasadit toto zařízení.


## <a name="key-capabilities"></a>Klíčové funkce

Azure Stack Edge pro R má následující možnosti:

|Schopnost |Description  |
|---------|---------|
|Robustní hardware| Robustní hardware třídy serveru určený pro prostředí Harsh. Zařízení obsažené v přenosném tranzitním případě. |
|Spravované v cloudu     |Zařízení a služba se spravují prostřednictvím Azure Portal.|
|Výpočetní úlohy hraničních procesů   |Umožňuje analýzu, zpracování a filtrování dat. Podporuje virtuální počítače a kontejnerové úlohy.|
|Akcelerované AI Inferencing| Povoleno grafickým procesorem NVIDIA T4.|
|Přístup k datům     | Přímý přístup k datům z rozšíření Azure Storage Blob a ze souborů Azure pomocí cloudových rozhraní API za účelem dalšího zpracování dat v cloudu. Místní mezipaměť v zařízení se používá pro rychlý přístup k naposledy použitým souborům.|
|Odpojený režim| Zařízení a službu je možné volitelně spravovat prostřednictvím centra Azure Stack. Nasaďte, spouštějte a spravujte aplikace v offline režimu. <br> Odpojený režim podporuje scénáře nahrání v režimu offline.|
|Podporované protokoly pro přenos souborů     |Podpora pro protokoly Standard SMB, NFS a REST pro příjem dat. <br> Další informace o podporovaných verzích najdete v požadavcích na [Azure Stack Edge pro R požadavky na systém](azure-stack-edge-gpu-system-requirements.md).|
|Aktualizace dat     | Možnost aktualizovat místní soubory pomocí nejnovějších souborů z cloudu.|
|Dvojité šifrování    | Použití vlastních šifrovacích jednotek poskytuje první vrstvu šifrování. SÍŤ VPN poskytuje druhou vrstvu šifrování. Podpora BitLockeru pro místně zašifrování dat a zabezpečení přenosu dat do cloudu přes *https* .|
|Omezení šířky pásma| Omezí omezení využití šířky pásma během špičky.|

<!--|Scale out file server| Available as 1-node and 4-node cluster configurations|-->

## <a name="use-cases"></a>Případy použití

Tady jsou různé scénáře, kdy Azure Stack Edge pro R je možné použít pro rychlé Machine Learning (ML) Inferencing na hranici a předzpracování dat před jejich odesláním do Azure.

- Při **odvozování pomocí Azure Machine Learning** – s Azure Stack Edge pro R můžete spouštět modely ml a získat tak rychlé výsledky, na jejichž základě se můžete v případě, že se data odešlou do cloudu, zpracovávat. Úplnou datovou sadu lze volitelně přenést, aby bylo možné pokračovat v přeučení a vylepšení modelů ML. Další informace o tom, jak používat hardwarové akcelerované modely Azure ML na zařízení Azure Stack Edge pro R, najdete v tématu [nasazení hardwarových akcelerovaných modelů Azure ml na Azure Stack Edge pro r](../machine-learning/how-to-deploy-fpga-web-service.md#deploy-to-a-local-edge-server).

- Před odesláním do Azure můžete **předzpracovat** data – Transformujte data, abyste mohli vytvořit další napadnutelnou datovou sadu. Předzpracování lze použít k těmto účelům:

    - Agregace dat
    - Úpravou dat můžete například odebrat osobní údaje.
    - Podmnožiny dat pro optimalizaci úložiště a šířky pásma nebo pro další analýzu.
    - Analýza a reakce na události IoT

- **Přenos dat přes síť do Azure** – použijte Azure Stack Edge pro R k snadnému a rychlému přenosu dat do Azure, abyste umožnili další výpočty a analýzy nebo pro účely archivace.

## <a name="components"></a>Komponenty

Řešení Azure Stack Edge pro R sestává z Azure Stack hraničního prostředku, Azure Stackho Edge pro R robustního, fyzického zařízení a místního webového uživatelského rozhraní.

- **Azure Stack fyzického zařízení Edge pro R** – výpočetní a úložné zařízení s jedním uzlem, které je součástí náročného přenosového případu. K dispozici je také volitelné nepřerušitelný zdroj napájení (UPS).

    ![Zařízení Azure Stack Edge pro R 1 – uzel](media/azure-stack-edge-pro-r-overview/device-image-1.png)

- **Prostředek Azure Stack Edge** – prostředek v Azure Portal, který vám umožní spravovat robustní zařízení Azure Stack Edge pro R z webového rozhraní, ke kterému můžete přistupovat z různých geografických umístění. Pomocí Azure Stackho hraničního prostředku můžete vytvářet a spravovat prostředky, zobrazovat a spravovat zařízení a výstrahy a spravovat sdílené složky.  

- **Azure Stack pro místní webové uživatelské rozhraní nástroje Edge pro r** – místní uživatelské rozhraní založené na prohlížeči na vašem zařízení Azure Stack Edge pro r je primárně určeno pro počáteční konfiguraci zařízení. Pomocí místního webového uživatelského rozhraní můžete spustit diagnostiku, vypnout a restartovat zařízení Azure Stack Edge pro, zobrazit kopírování protokolů a kontaktovat podpora Microsoftu a požádat o soubor žádosti o službu.


## <a name="region-availability"></a>Dostupnost v oblastech

U fyzického zařízení Azure Stack Edge pro R, prostředku Azure a cílového účtu úložiště, do kterého přenášíte data, se nemusí nacházet ve stejné oblasti.

- **Dostupnost prostředků** – seznam všech oblastí, ve kterých je prostředek Azure Stack Edge dostupný, najdete v části [produkty Azure dostupné v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). 

- **Dostupnost zařízení** – seznam všech zemí, kde je k dispozici zařízení Azure Stack Edge pro r, najdete v části **dostupnost** na kartě **Azure Stack Edge** pro r pro [Azure Stack ceny pro r Edge](https://azure.microsoft.com/pricing/details/azure-stack/edge/#azureStackEdgeProR).

- **Cílové účty úložiště** – účty úložiště, do kterých se ukládají data, jsou dostupné ve všech oblastech Azure. Oblasti, ve kterých jsou uloženy účty úložiště Azure Stack Edge pro R, by měly být umístěny blízko místa, kde se zařízení nachází pro optimální výkon. Účet úložiště umístěný daleko od zařízení bude vykazovat dlouhé latence a nižší výkon.

## <a name="next-steps"></a>Další kroky

- Přečtěte si [požadavky na systém Azure Stack Edge pro R](azure-stack-edge-gpu-system-requirements.md).
<!--- Understand the [Azure Stack Edge Pro R limits](azure-stack-edge-limits.md).-->