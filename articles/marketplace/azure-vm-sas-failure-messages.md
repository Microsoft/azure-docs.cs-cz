---
title: Zprávy o selhání SAS virtuálních počítačů – Azure Marketplace
description: Nejčastější dotazy při práci se sdíleným přístupovým podpisem (SAS)
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 10/15/2020
ms.openlocfilehash: 7b91cf3d49d88501fb908d157892ec3eb24bee6e
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/20/2020
ms.locfileid: "92283750"
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

Podrobnosti najdete v tématu [použití sdílených přístupových podpisů (SAS)](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/).

## <a name="next-steps"></a>Další kroky

- [Vygenerovat identifikátor URI SAS](azure-vm-get-sas-uri.md)
