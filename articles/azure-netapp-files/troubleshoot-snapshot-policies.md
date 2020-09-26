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
ms.openlocfilehash: 1d7a91de8fa505fe4c2b06eea59f3acc2ae1f7e8
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91342023"
---
# <a name="troubleshoot-snapshot-policies"></a>Řešení potíží se zásadami snímků

Tento článek popisuje scénáře chyb, se kterými se můžete setkat při správě Azure NetApp Filesch zásad snímků. Poskytuje také řešení, která vám můžou usnadnit řešení těchto problémů.

## <a name="snapshot-policy-creation-failing-with-invalid-snapshot-policy-name"></a>Nepovedlo se vytvořit zásadu snímku s neplatným názvem zásady snímku.

Při vytváření zásad snímku dojde k chybě, pokud je název zásady snímku neplatný. Pro názvy zásad snímku platí následující pokyny:  

* Název zásady snímku nesmí obsahovat znaky, které nejsou ASCII, ani speciální znaky.
* Název zásady snímku musí začínat písmenem nebo číslicí a může obsahovat písmena, číslice, podtržítka (_) a spojovníky (-).
* Název zásady snímku musí mít 1 až 64 znaků.  

Upravte název zásady snímku podle pokynů.  

## <a name="snapshot-policy-creation-failing-with-invalid-values"></a>Vytváření zásad snímku se nepovedlo kvůli neplatným hodnotám. 

Pokud zadáte neplatnou hodnotu pro pole, jako je například nebo, Azure NetApp Files vytvořit zásadu `Number of snapshots to keep` snímku `Minute on the hour to take snapshot` .  
 
Platné hodnoty jsou následující:   

* Hodnota musí být platné číslo.
* Hodnota musí být v rozmezí od 0 do 59.

Ujistěte se, že jsou pro pole k dispozici platná hodnota.

## <a name="snapshot-policy-creation-failing-with-total-number-of-snapshots-to-keep-exceeds-255-error"></a>Nepovedlo se vytvořit zásadu snímku s celkovým počtem snímků, které se mají zachovat, což je víc než 255. Chyba 

Každý svazek může mít [maximálně 255 snímků](azure-netapp-files-resource-limits.md). Maximum zahrnuje součet všech hodinových, denních, týdenních a měsíčních snímků. Tuto hodnotu byste měli snížit `Snapshots to keep` a zkuste to znovu.

## <a name="assigning-policy-to-a-volume-failing-with-total-snapshot-policy-is-over-the-max-255-error"></a>Přiřazení zásad k selhání svazku se stavem "celková zásada snímku je nad maximální chybou" 255 ".

Každý svazek může mít [maximálně 255 snímků](azure-netapp-files-resource-limits.md). Pokud součet všech snímků na vyžádání, hodin, denní, týdenní a měsíčních snímků překračuje maximum, dojde k chybě. Snižte `snapshots to keep` hodnotu nebo odstraňte některé snímky na vyžádání a zkuste to znovu.

## <a name="next-steps"></a>Další kroky  

* [Správa zásad snímků](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies)
