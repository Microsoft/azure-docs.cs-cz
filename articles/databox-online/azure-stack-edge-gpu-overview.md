---
title: Přehled Microsoft Azure Stack Edge | Microsoft Docs
description: Popisuje Azure Stack Edge, což je řešení úložiště, které používá fyzické zařízení pro přenos prostřednictvím sítě do Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 08/27/2020
ms.author: alkohli
ms.openlocfilehash: e3caf4e0e60b8ef9fb0b30b599b22e0f07d8998c
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2020
ms.locfileid: "89088379"
---
# <a name="what-is-azure-stack-edge-with-gpu-preview"></a>Co je Azure Stack Edge s grafickým procesorem (Preview)?

Azure Stack Edge s GPU je hraniční výpočetní zařízení s podporou AI a možnosti přenosu dat v síti. Tento článek vám poskytne přehled o řešení Azure Stack Edge, výhodách, klíčových funkcích a scénářích, kde můžete nasadit toto zařízení.

Azure Stack Edge s grafickým procesorem je řešení typu hardware jako služba. Společnost Microsoft si dodává cloudové zařízení, které funguje jako brána síťového úložiště, a má vestavěnou funkci grafického procesoru (GPU), která umožňuje akcelerovanou AI-Inferencing. 

> [!IMPORTANT]
> Azure Stack Edge s grafickým procesorem je aktuálně ve verzi Preview. Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="use-cases"></a>Případy použití

Tady jsou různé scénáře, kdy je možné Azure Stack Edge použít pro rychlé Machine Learning (ML) Inferencing na hranici a předzpracování dat před jejich odesláním do Azure.

- Při **odvozování pomocí Azure Machine Learning** – s Azure Stack Edge můžete spouštět modely ml, abyste získali rychlé výsledky, na jejichž základě se dá data odeslat do cloudu. Úplnou datovou sadu lze volitelně přenést, aby bylo možné pokračovat v přeučení a vylepšení modelů ML. Další informace o tom, jak používat hardwarové akcelerované modely Azure ML na Azure Stack hraničním zařízení, najdete v tématu [nasazení hardwarových akcelerovaných modelů Azure ml na Azure Stack Edge](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-fpga-web-service#deploy-to-a-local-edge-server).

- Před odesláním do Azure můžete **předzpracovat** data – Transformujte data, abyste mohli vytvořit další napadnutelnou datovou sadu. Předzpracování lze použít k těmto účelům: 

    - Agregace dat
    - Úpravou dat můžete například odebrat osobní údaje.
    - Podmnožiny dat pro optimalizaci úložiště a šířky pásma nebo pro další analýzu.
    - Analýza a reakce na události IoT 

- **Přenos dat přes síť do Azure** – pomocí Azure Stack Edge můžete snadno a rychle přenášet data do Azure a umožnit tak další výpočty a analýzu nebo pro účely archivace. 

## <a name="key-capabilities"></a>Klíčové funkce

Azure Stack Edge má následující možnosti:

|Schopnost |Popis  |
|---------|---------|
|Akcelerované AI Inferencing| Povoleno integrovaným grafickým procesorem (jeden nebo dva v závislosti na modelu).|
|Hraniční Computing      |Umožňuje analýzu, zpracování a filtrování dat. Podporuje virtuální počítače a clustery Kubernetes.|
|Vysoký výkon | Vysoce výkonné výpočetní a datové přenosy.|
|Přístup k datům     | Přímý přístup k datům z rozšíření Azure Storage Blob a ze souborů Azure pomocí cloudových rozhraní API za účelem dalšího zpracování dat v cloudu. Místní mezipaměť v zařízení se používá pro rychlý přístup k naposledy použitým souborům.|
|Spravované v cloudu     |Zařízení a služba se spravují prostřednictvím Azure Portal.  |
|Nahrání offline     | Odpojený režim podporuje scénáře nahrání v režimu offline.|
|Podporované protokoly     | Podpora pro protokoly Standard SMB, NFS a REST pro příjem dat. <br> Další informace o podporovaných verzích najdete v tématu [požadavky na systém Azure Stack Edge](azure-stack-edge-system-requirements.md).|
|Aktualizace dat     | Možnost aktualizovat místní soubory pomocí nejnovějších souborů z cloudu.|
|Šifrování    | BitLocker podporuje místní šifrování dat a bezpečný přenos dat do cloudu přes protokol *https*.|
|Omezení šířky pásma| Omezí omezení využití šířky pásma během špičky.|
|ExpressRoute | Bylo přidáno zabezpečení prostřednictvím ExpressRoute. Použijte konfiguraci partnerského vztahu, při které se provoz z místních zařízení do koncových bodů cloudového úložiště přenáší přes ExpressRoute. Další informace najdete v článku [Přehled ExpressRoute](../expressroute/expressroute-introduction.md).

## <a name="components"></a>Komponenty

Azure Stack hraniční řešení se skládá z prostředků Azure Stack Edge, Azure Stack hraniční fyzické zařízení a místního webového uživatelského rozhraní.

* **Fyzické zařízení Azure Stack Edge** – 1U server připojený k racku dodávaný Microsoftem, který se dá nakonfigurovat tak, aby odesílal data do Azure.
    
* **Prostředek Azure Stack Edge** – prostředek v Azure Portal, který umožňuje spravovat Azure Stack hraniční zařízení z webového rozhraní, ke kterému můžete přistupovat z různých geografických umístění. Pomocí Azure Stackho hraničního prostředku můžete vytvářet a spravovat prostředky, zobrazovat a spravovat zařízení a výstrahy a spravovat sdílené složky.  

    Další informace najdete v [poznámkách k vytvoření objednávky pro zařízení Azure Stack Edge](azure-stack-edge-gpu-deploy-prep.md#create-a-new-resource).

* **Azure Stack lokální webové uživatelské rozhraní** – pomocí místního webového uživatelského rozhraní můžete spustit diagnostiku, vypnout a znovu spustit Azure Stack hraniční zařízení, zobrazit Kopírovat protokoly a kontaktovat podpora Microsoftu k zaznamenání žádosti o službu.

    Informace o používání webového uživatelského rozhraní najdete v [části použití webového uživatelského rozhraní pro správu Azure Stackho okraje](azure-stack-edge-manage-access-power-connectivity-mode.md).

## <a name="region-availability"></a>Dostupnost v oblastech

Fyzické zařízení Azure Stack Edge, prostředek Azure a cílový účet úložiště, do kterého přenášíte data, nemusí být ve stejné oblasti.

- **Dostupnost prostředků** – pro tuto verzi Preview je prostředek k dispozici v oblastech východní USA, západní EU a Jižní východní Asie.
    
- **Cílové účty úložiště** – účty úložiště, do kterých se ukládají data, jsou dostupné ve všech oblastech Azure. Oblasti, ve kterých jsou uloženy účty úložiště Azure Stack hraniční data, by měly být umístěny blízko místa, kde se zařízení nachází pro optimální výkon. Účet úložiště umístěný daleko od zařízení bude vykazovat dlouhé latence a nižší výkon.

## <a name="next-steps"></a>Další kroky

- Projděte si [požadavky na systém Azure Stack Edge](azure-stack-edge-gpu-system-requirements.md).
- Seznamte se s [limity Azure Stack hran](azure-stack-edge-limits.md).
- Nasaďte [Azure Stack Edge](azure-stack-edge-gpu-deploy-prep.md) do Azure Portal.
