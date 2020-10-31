---
title: Zprávy o selhání SAS virtuálních počítačů – Azure Marketplace
description: Nejčastější dotazy při práci se sdíleným přístupovým podpisem (SAS)
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 10/15/2020
ms.openlocfilehash: 1c89887117c10ca77ec4c04b3adbe3e2d9923479
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93126834"
---
# <a name="virtual-machine-sas-failure-messages"></a>Zprávy o selhání SAS virtuálních počítačů

V této části se vyskytují běžné problémy při práci se sdílenými přístupovými podpisy (které slouží k identifikaci a sdílení nahraných virtuálních pevných disků pro vaše řešení) společně s navrhovanými řešeními.

| Problém | Zpráva o chybě | Oprava |
| --------- | ------------------- | ------- |
| *Selhání při kopírování imagí* |  |  |
| znak "?" se v identifikátoru URI SAS nenašel. | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Aktualizujte identifikátor URI SAS pomocí doporučených nástrojů. |
| parametry "St" a "se" nejsou v identifikátoru URI SAS | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Aktualizujte identifikátor URI SAS pomocí správného **počátečního data** a hodnot **koncového data** . |
| "SP = RL" není v identifikátoru URI SAS | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Aktualizujte identifikátor URI SAS s oprávněními nastavenými jako `Read` a `List` . |
| Identifikátor URI SAS obsahuje mezery v názvu virtuálního pevného disku. | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Aktualizujte identifikátor URI SAS pro odebrání prázdných znaků. |
| Chyba autorizace identifikátoru URI SAS | `Failure: Copying Images. Not able to download blob due to authorization error.` | Zkontrolujte a opravte formát identifikátoru URI SAS. V případě potřeby znovu vygenerujte. |
| Parametry identifikátoru URI SAS "St" a "se" neobsahují úplné specifikace data a času. | `Failure: Copying Images. Not able to download blob due to incorrect SAS Uri.` | Parametry **počátečního** a **koncového** data URI SAS `st` `se` musí mít úplný formát data a času, například `11-02-2017T00:00:00Z` . Zkrácené verze jsou neplatné (některé příkazy v rozhraní příkazového řádku Azure CLI můžou ve výchozím nastavení generovat zkrácené hodnoty). |
|  |  |  |

Podrobnosti najdete v tématu [použití sdílených přístupových podpisů (SAS)](../storage/common/storage-sas-overview.md).

## <a name="next-steps"></a>Další kroky

- [Vygenerovat identifikátor URI SAS](azure-vm-get-sas-uri.md)