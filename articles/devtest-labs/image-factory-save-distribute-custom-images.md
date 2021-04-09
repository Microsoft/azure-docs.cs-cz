---
title: Ukládání a distribuce imagí v Azure DevTest Labs | Microsoft Docs
description: Tento článek popisuje kroky pro uložení vlastních imagí z již vytvořených virtuálních počítačů v Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: a5278626f8cdd4299912f3c952786422436fe916
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "85476236"
---
# <a name="save-custom-images-and-distribute-to-multiple-labs"></a>Uložení vlastních imagí a jejich distribuce do několika testovacích prostředí
Tento článek popisuje kroky pro uložení vlastních imagí z již vytvořených virtuálních počítačů (VM). Také se zabývá tím, jak distribuovat tyto vlastní image do dalších DevTest Labs v organizaci.

## <a name="prerequisites"></a>Požadavky
Již by měly být zavedeny následující položky:

- Testovací prostředí pro objekt pro vytváření imagí v Azure DevTest Labs.
- Projekt Azure DevOps, který slouží k automatizaci objektu pro vytváření imagí.
- Umístění zdrojového kódu obsahující skripty a konfiguraci (v našem příkladu v rámci stejného projektu DevOps, jak je uvedeno v předchozím kroku).
- Definice sestavení pro orchestraci úloh prostředí Azure PowerShell.

V případě potřeby postupujte podle kroků v části [spuštění objektu pro vytváření imagí z Azure DevOps](image-factory-set-up-devops-lab.md) a vytvořte nebo nastavte tyto položky. 

## <a name="save-vms-as-generalized-vhds"></a>Ukládání virtuálních počítačů jako zobecněných virtuálních pevných disků
Uložte stávající virtuální počítače jako zobecněné virtuální pevné disky.  K dispozici je ukázkový skript PowerShellu pro uložení stávajících virtuálních počítačů jako generalizované virtuální pevné disky. Pokud ho chcete použít, přidejte do definice sestavení další úlohu **Azure PowerShellu** , jak je znázorněno na následujícím obrázku:

![Přidat Azure PowerShell krok](./media/save-distribute-custom-images/powershell-step.png)

Jakmile budete mít nový úkol v seznamu, vyberte položku, abychom mohli vyplnit všechny podrobnosti, jak je znázorněno na následujícím obrázku: 

![Nastavení PowerShellu](./media/save-distribute-custom-images/powershell-settings.png)


## <a name="generalized-vs-specialized-custom-images"></a>Zobecněné a specializované vlastní image
Při vytváření vlastní image z virtuálního počítače na [Azure Portal](https://portal.azure.com)se můžete rozhodnout, že budete mít zobecněnou nebo specializovanou vlastní image.

- **Speciální vlastní image:** Nástroj Sysprep/zrušení zřízení nebyl v počítači spuštěn. To znamená, že obrázek je přesnou kopii disku s operačním systémem na stávajícím virtuálním počítači (snímek).  Stejné soubory, aplikace, uživatelské účty, název počítače a tak dále jsou přítomny, když vytvoříme nový počítač z této vlastní image.
- **Zobecněná vlastní bitová kopie:** V počítači se spustil nástroj Sysprep/zrušení zřízení.  Když se tento proces spustí, odebrání uživatelských účtů, odebrání názvu počítače, odstranění podregistrů registru uživatele atd., s cílem generalizace image, aby bylo možné je přizpůsobit při vytváření jiného virtuálního počítače.  Když nasadíte virtuální počítač (spuštěním nástroje Sysprep), proces odstraní aktuální virtuální počítač – již nebude funkční.

Skript pro přichycení vlastních imagí v objektu pro vytváření imagí uloží VHD pro všechny virtuální počítače vytvořené v předchozím kroku (identifikované na základě značky na prostředku v Azure).

## <a name="update-configuration-for-distributing-images"></a>Aktualizace konfigurace pro distribuci imagí
Dalším krokem v procesu je odeslání vlastních imagí z testovacího prostředí z image Factory do všech ostatních laboratoří, které je potřebují. Základní součástí tohoto procesu je **labs.js** konfiguračního souboru. Tento soubor najdete ve složce pro **konfiguraci** , která je součástí objektu pro vytváření imagí.

V labs.jske konfiguračnímu souboru jsou uvedené dvě klíčové věci:

- Jednoznačná identifikace konkrétního cílového testovacího prostředí s využitím ID předplatného a názvu testovacího prostředí.
- Konkrétní sada imagí, která by měla být vložena do testovacího prostředí jako relativní cesty ke kořenu konfigurace. Můžete zadat celou složku (k získání všech imagí v této složce).

Tady je příklad labs.jssouboru se dvěma cvičeními uvedenými níže. V tomto případě distribuujete image do dvou různých laboratoří.

```json
{
   "Labs": [
      {
         "SubscriptionId": "<subscription ID that contains the lab>",
         "LabName": "<Name of the DevTest Lab>",
         "ImagePaths": [
               "Win2012R2",
               "Win2016/Datacenter.json"
         ]
      },
      {
         "SubscriptionId": "<subscription ID that contains the lab>",
         "LabName": "<Name of the DevTest Lab>",
         "ImagePaths": [
               "Win2016/Datacenter.json"
         ]
      }
   ]
}
```

## <a name="create-a-build-task"></a>Vytvoření úlohy sestavení
Pomocí stejných kroků, které jste si poznamenali dříve v tomto článku, přidejte do definice sestavení další úlohu **Azure PowerShell** buildu. Vyplňte podrobnosti, jak je znázorněno na následujícím obrázku: 

![Sestavit úlohu pro distribuci imagí](./media/save-distribute-custom-images/second-build-task-powershell.png)

Parametry jsou: `-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -SubscriptionId $(SubscriptionId) -DevTestLabName $(DevTestLabName) -maxConcurrentJobs 20`

Tato úloha přebírá všechny vlastní image v továrně imagí a prezentuje je do všech laboratoří definovaných v Labs.jsv souboru.

## <a name="queue-the-build"></a>Zařazení sestavení do fronty
Po dokončení úlohy sestavení distribuce zaznamenejte nové sestavení, abyste se ujistili, že všechno funguje. Po úspěšném dokončení sestavení se nové vlastní image zobrazí v cílovém testovacím prostředí, které jste zadali do Labs.jskonfiguračního souboru.

## <a name="next-steps"></a>Další kroky
V dalším článku v řadě aktualizujete objekt pro vytváření imagí pomocí zásad uchovávání a kroků čištění: [nastavte zásady uchovávání informací a spusťte skripty pro vyčištění](image-factory-set-retention-policy-cleanup.md).
