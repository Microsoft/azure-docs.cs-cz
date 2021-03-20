---
title: Řešení potíží se zásadami snímků pro Azure NetApp Files | Microsoft Docs
description: Popisuje chybové zprávy a řešení, které vám můžou pomoct při řešení potíží se správou zásad snímku pro Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/23/2020
ms.author: b-juche
ms.openlocfilehash: 6ba8b18876bdae2754a6a772ce3909ff2f5a71b7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "91651000"
---
# <a name="troubleshoot-snapshot-policies"></a>Řešení potíží se zásadami snímků

Tento článek popisuje scénáře chyb, se kterými se můžete setkat při správě Azure NetApp Filesch zásad snímků. Poskytuje také řešení, která vám můžou usnadnit řešení těchto problémů.

## <a name="error-conditions-and-resolutions"></a>Chybové stavy a řešení 

|     Chybový stav    |     Řešení    |
|-|-|
| Vytvoření zásady snímku se nepovedlo kvůli neplatnému názvu zásad snímku. | Při vytváření zásad snímku dojde k chybě, pokud je název zásady snímku neplatný. Pro názvy zásad snímku platí následující pokyny:  <ul><li> Název zásady snímku nesmí obsahovat znaky, které nejsou ASCII, ani speciální znaky. </li> <li> Název zásady snímku musí začínat písmenem nebo číslicí a může obsahovat písmena, číslice, podtržítka (_) a spojovníky (-). </li> <li> Název zásady snímku musí mít 1 až 64 znaků.  </li></ul> Upravte název zásady snímku podle pokynů.  |
| Vytvoření zásady snímku se nepovedlo kvůli neplatným hodnotám. | Pokud zadáte neplatnou hodnotu pro pole, jako je například nebo, Azure NetApp Files vytvořit zásadu `Number of snapshots to keep` snímku `Minute on the hour to take snapshot` . Platné hodnoty jsou následující:  <ul><li>Hodnota musí být platné číslo.</li> <li>Hodnota musí být v rozmezí od 0 do 59.</li></ul> Ujistěte se, že jsou pro pole k dispozici platná hodnota. | 
| Vytvoření zásady snímku se nepovedlo s chybou `Total number of snapshots to keep exceeds 255` . | Každý svazek může mít [maximálně 255 snímků](azure-netapp-files-resource-limits.md). Maximum zahrnuje součet všech hodinových, denních, týdenních a měsíčních snímků. <br> Snižte `Snapshots to keep` hodnotu a zkuste to znovu. |
| Přiřazení zásad ke svazku se nezdařilo s chybou `Total snapshot policy is over the max '255'` . | Každý svazek může mít [maximálně 255 snímků](azure-netapp-files-resource-limits.md). Pokud součet všech snímků na vyžádání, hodin, denní, týdenní a měsíčních snímků překračuje maximum, dojde k chybě. <br> Snižte `snapshots to keep` hodnotu nebo odstraňte některé snímky na vyžádání a zkuste to znovu. | 

## <a name="next-steps"></a>Další kroky  

* [Správa zásad snímků](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies)
