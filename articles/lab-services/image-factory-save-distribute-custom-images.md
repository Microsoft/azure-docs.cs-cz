---
title: Uložit a distribuce imagí ve službě Azure DevTest Labs | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit objekt pro vytváření vlastní image ve službě Azure DevTest Labs.
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: spelluru
ms.openlocfilehash: feabd055833e5f0d850138af528cce1da82cae49
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60622600"
---
# <a name="save-custom-images-and-distribute-to-multiple-labs"></a>Uložení vlastních imagí a jejich distribuce do několika testovacích prostředí
Tento článek popisuje poskytuje kroky pro uložení vlastních imagí z již vytvořené virtuální počítače (VM). Zabývá se taky, jak se bude distribuovat do dalších DevTest Labs v organizaci těchto vlastních imagí.

## <a name="prerequisites"></a>Požadavky
Následující položky by už měla:

- Testovací prostředí pro vytváření bitové kopie ve službě Azure DevTest Labs.
- Projekt DevOps Azure, který se používá k automatizaci vytváření bitové kopie.
- Umístění zdrojového kódu obsahující skripty a konfiguraci (v našem příkladu ve stejném projektu DevOps, které jsou uvedené v předchozím kroku).
- K ladění úloh Azure Powershellu v definici sestavení.

V případě potřeby, postupujte podle kroků v [spustit objekt pro vytváření image z Azure DevOps](image-factory-set-up-devops-lab.md) vytvořit nebo nastavit tyto položky. 

## <a name="save-vms-as-generalized-vhds"></a>Uložit jako zobecněné virtuální pevné disky virtuálních počítačů
Uložte stávající virtuální počítače jako zobecněné virtuální pevné disky.  Zde je ukázkový skript Powershellu pro uložení existujících virtuálních počítačů jako zobecněné virtuální pevné disky. Pro použití je třeba nejprve přidat další **prostředí Azure Powershell** úloh k definici sestavení, jak je znázorněno na následujícím obrázku:

![Přidejte krok prostředí Azure PowerShell](./media/save-distribute-custom-images/powershell-step.png)

Až budete mít nový úkol v seznamu, vyberte položku, takže jsme můžete Vyplňte všechny podrobnosti, jak je znázorněno na následujícím obrázku: 

![Nastavení prostředí PowerShell](./media/save-distribute-custom-images/powershell-settings.png)


## <a name="generalized-vs-specialized-custom-images"></a>Generalizované vs. vlastní specializované Image
V [webu Azure portal](https://portal.azure.com), při vytváření vlastní image z virtuálního počítače, můžete zvolit, aby zobecněný nebo speciální vlastní image.

- **Vlastní specializované image:** Nástroj Sysprep/zrušení zřízení se nespustí na počítači. To znamená, že na obrázku je přesnou kopii disku s operačním systémem na existující virtuální počítač (snímek).  Stejné soubory, aplikace, uživatelské účty, název počítače a tak dále, jsou všechny k dispozici, když vytvoříme nový počítač z vlastní image.
- **Vlastní generalizované Image:** Na počítači byl spuštěn nástroj Sysprep/zrušení zřízení.  Při spuštění tohoto procesu odebere uživatelské účty, odebere název počítače, odstraní uživatele podregistrům registru, atd., s cílem generalizace bitovou kopii, takže je možné přizpůsobit při vytváření jiného virtuálního počítače.  Když zobecnit virtuální počítač (spuštěním nástroje sysprep), proces odstraní aktuální virtuální počítač – již nebude funkční.

Skript pro přichycení vlastních imagí v objektu pro vytváření bitové kopie se uloží virtuální pevné disky pro virtuální počítače vytvořené v předchozím kroku (identifikován na základě značky na prostředek v Azure).

## <a name="update-configuration-for-distributing-images"></a>Aktualizovat konfiguraci pro distribuci bitové kopie
Dalším krokem v procesu je nasdílejte vlastní Image z testovacího prostředí objekt pro vytváření bitové kopie si do jinými prostředími, které je třeba je. Je základní součástí tohoto procesu **labs.json** konfigurační soubor. Tento soubor můžete najít **konfigurace** složky obsažené v objektu pro vytváření bitové kopie.

Uvedená v konfiguračním souboru labs.json dvě klíčové věci:

- Jednoznačná identifikace konkrétní cílové prostředí pomocí ID předplatného a název testovacího prostředí.
- Konkrétní sadu imagí, které by měl být nahrány do testovacího prostředí jako relativní cesty ke kořenu konfigurace. Můžete zadat celou složku (Chcete-li získat všechny Image v této složce) příliš.

Tady je příklad souboru labs.json pomocí dvou labs uvedené. V tomto případě jsou distribuci Image do dvou různých testovacích prostředí.

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
Pomocí stejného postupu jste viděli dříve v tomto článku, přidat další **prostředí Azure Powershell** úloha sestavení, definice sestavení. Vyplňte podrobnosti, jak je znázorněno na následujícím obrázku: 

![Úloha k distribuci bitové kopie sestavení](./media/save-distribute-custom-images/second-build-task-powershell.png)

Parametry jsou: `-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -SubscriptionId $(SubscriptionId) -DevTestLabName $(DevTestLabName) -maxConcurrentJobs 20`

Tato úloha přijímá všech vlastních imagí, které jsou k dispozici v objektu pro vytváření bitové kopie a je nasadí na všechny testovací prostředí definované v souboru Labs.json.

## <a name="queue-the-build"></a>Zařaďte sestavení do fronty
Po dokončení úlohy sestavení distribuční fronty nové sestavení, abyste měli jistotu, že všechno funguje. Po úspěšném dokončení sestavení nové vlastní Image se zobrazí v cílovém testovacím prostředí, který jste zadali do konfiguračního souboru Labs.json.

## <a name="next-steps"></a>Další postup
V další článek v sérii aktualizujte objekt pro vytváření bitové kopie s uchovávání informací zásady a vyčištění kroky: [Nastavit zásady uchovávání informací a spouštění skriptů čištění](image-factory-set-retention-policy-cleanup.md).
