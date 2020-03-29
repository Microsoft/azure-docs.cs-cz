---
title: Ukládání a distribuce bitových kopií v laboratořích Azure DevTest | Dokumenty společnosti Microsoft
description: Tento článek vám poskytuje kroky k uložení vlastních ibi z již vytvořených virtuálních počítačů (VM) v Azure DevTest Labs.
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: e5bc8e5041bfe6d95e3ff1a93bb3338ccead5bb4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76759427"
---
# <a name="save-custom-images-and-distribute-to-multiple-labs"></a>Uložení vlastních imagí a jejich distribuce do několika testovacích prostředí
Tento článek vám poskytuje kroky k uložení vlastních irek z již vytvořených virtuálních počítačů. Zahrnuje také, jak distribuovat tyto vlastní image do jiných DevTest Labs v organizaci.

## <a name="prerequisites"></a>Požadavky
Následující položky by již měly být na místě:

- Testovací prostředí pro image Factory v Azure DevTest Labs.
- Projekt Azure DevOps, který se používá k automatizaci výroby bitové kopie.
- Umístění zdrojového kódu obsahující skripty a konfiguraci (v našem příkladu ve stejném projektu DevOps uvedeném v předchozím kroku).
- Vytvoření definice pro orchestratu úloh Azure Powershellu.

V případě potřeby postupujte podle kroků v [továrně bitové kopie z Azure DevOps](image-factory-set-up-devops-lab.md) k vytvoření nebo nastavení těchto položek. 

## <a name="save-vms-as-generalized-vhds"></a>Uložení virtuálních měn jako generalizovaných virtuálních disponizí
Uložte existující virtuální ho disponizované virtuální chody jako generalizované virtuální dříže.  Existuje ukázkový skript prostředí PowerShell pro uložení existujících virtuálních virtuálních ms jako generalizovaných virtuálních discích. Chcete-li ji použít, nejprve přidejte další **úlohu Azure Powershellu** do definice sestavení, jak je znázorněno na následujícím obrázku:

![Krok přidání Azure PowerShellu](./media/save-distribute-custom-images/powershell-step.png)

Jakmile budete mít nový úkol v seznamu, vyberte položku, abychom mohli vyplnit všechny podrobnosti, jak je znázorněno na následujícím obrázku: 

![Nastavení prostředí PowerShell](./media/save-distribute-custom-images/powershell-settings.png)


## <a name="generalized-vs-specialized-custom-images"></a>Generalizované vs. specializované vlastní obrázky
Na [webu Azure Portal](https://portal.azure.com)můžete při vytváření vlastní image z virtuálního počítače zvolit, že chcete mít zobecněnou nebo specializovanou vlastní bitovou kopii.

- **Specializovaný vlastní obrázek:** Sysprep/Deprovision nebyl v počítači spuštěn. To znamená, že obrázek je přesná kopie disku operačního systému na existujícím virtuálním počítači (snímek).  Stejné soubory, aplikace, uživatelské účty, název počítače a tak dále, jsou přítomny, když vytvoříme nový počítač z tohoto vlastního obrázku.
- **Zobecněný vlastní obrázek:** V počítači bylo spuštěno sysprep/Deprovision.  Při spuštění tohoto procesu odebere uživatelské účty, odebere název počítače, odstraní úly registru uživatelů atd., S cílem zobecnit bitovou kopii, aby ji bylo možné přizpůsobit při vytváření jiného virtuálního počítače.  Když zobecníte virtuální počítač (spuštěním nástroje sysprep), proces zničí aktuální virtuální počítač – už nebude funkční.

Skript pro přichycení vlastních ibi v Image Factory uloží Virtuální počítače pro všechny virtuální počítače vytvořené v předchozím kroku (identifikované na základě značky na prostředek v Azure).

## <a name="update-configuration-for-distributing-images"></a>Aktualizovat konfiguraci pro distribuci bitových kopií
Dalším krokem v procesu je posunout vlastní bitové kopie z testovacího prostředí továrny bitové kopie do jiných laboratoří, které je potřebují. Základní součástí tohoto procesu je konfigurační soubor **labs.json.** Tento soubor najdete ve složce **Konfigurace** zahrnuté v továrně bitových obrázků.

V konfiguračním souboru labs.json jsou uvedeny dvě klíčové položky:

- Jedinečně identifikaci konkrétní cílové laboratoře pomocí ID předplatného a název testovacího prostředí.
- Konkrétní sada bitových kopií, které by měly být posunuty do testovacího prostředí jako relativní cesty ke kořenovému adresáři konfigurace. Můžete také zadat celou složku (chcete-li získat všechny obrázky v této složce).

Zde je příklad souboru labs.json se dvěma testovacími prostředími uvedenými. V tomto případě distribuujete bitové kopie do dvou různých testovacích prostředí.

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
Pomocí stejných kroků, které jste viděli dříve v tomto článku, přidejte další úlohu sestavení **Azure Powershellu** k definici sestavení. Vyplňte podrobnosti, jak je znázorněno na následujícím obrázku: 

![Sestavení úkolu pro distribuci bitových kopií](./media/save-distribute-custom-images/second-build-task-powershell.png)

Parametry jsou:`-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -SubscriptionId $(SubscriptionId) -DevTestLabName $(DevTestLabName) -maxConcurrentJobs 20`

Tato úloha přebírá všechny vlastní obrázky přítomné v definici bitové kopie a tlačí je do všech laboratoří definovaných v souboru Labs.json.

## <a name="queue-the-build"></a>Zařazování sestavení do fronty
Po dokončení úlohy sestavení distribuce zařazujte do fronty nové sestavení, abyste se ujistili, že vše funguje. Po úspěšném dokončení sestavení se nové vlastní bitové kopie zobrazí v cílové laboratoři, která byla zadána do konfiguračního souboru Labs.json.

## <a name="next-steps"></a>Další kroky
V dalším článku v řadě aktualizujete továrnu bitových obrázků pomocí zásad uchovávání informací a kroků vyčištění: [Nastavte zásady uchovávání informací a spusťte skripty pro vyčištění](image-factory-set-retention-policy-cleanup.md).
