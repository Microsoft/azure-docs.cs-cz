---
title: Běžné problémy s identifikátorem URI SAS a opravy – Azure Marketplace
description: Při práci se sdílenými přístupovými podpisy byly zjištěny běžné problémy a navrhovaná řešení.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 04/09/2020
ms.openlocfilehash: 4eb3c3e893a276aed10807a13a0f2d6d3bc4e71d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87316834"
---
# <a name="common-sas-uri-issues-and-fixes"></a>Běžné problémy s identifikátorem URI SAS a opravy

V této části se vyskytují běžné problémy při práci se sdílenými přístupovými podpisy (které slouží k identifikaci a sdílení nahraných virtuálních pevných disků pro vaše řešení) společně s navrhovanými řešeními.

| **Chybu** | **Zpráva o chybě** | **Oprava** |
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
