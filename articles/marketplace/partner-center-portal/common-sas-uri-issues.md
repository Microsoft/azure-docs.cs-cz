---
title: Běžné problémy s identifikátorem URI SAS a opravy – Azure Marketplace
description: Při práci se sdílenými přístupovými podpisy byly zjištěny běžné problémy a navrhovaná řešení.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.openlocfilehash: fb86b1c5ec3be5a423dc2abd295aa8beb8f23f47
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81266237"
---
# <a name="common-sas-uri-issues-and-fixes"></a>Běžné problémy s identifikátorem URI SAS a opravy

> [!IMPORTANT]
> Přesouváme správu nabídek virtuálních počítačů Azure z portál partnerů cloudu do partnerského centra. Dokud nebudou vaše nabídky migrovány, postupujte podle pokynů v tématu [běžné problémy s identifikátorem URI SAS a opravy](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-common-sas-url-issues) portál partnerů cloudu ke správě nabídek.

V této části se vyskytují běžné problémy při práci se sdílenými přístupovými podpisy (které slouží k identifikaci a sdílení nahraných virtuálních pevných disků pro vaše řešení) společně s navrhovanými řešeními.

| **Chybu** | **Zpráva o chybě** | **Oprava** |
| --------- | ------------------- | ------- |
| *Selhání při kopírování imagí* |  |  |
| znak "?" se v identifikátoru URI SAS nenašel. | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Aktualizujte identifikátor URI SAS pomocí doporučených nástrojů. |
| parametry "St" a "se" nejsou v identifikátoru URI SAS | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Aktualizujte identifikátor URI SAS pomocí správného **počátečního data** a hodnot **koncového data** . |
| "SP = RL" není v identifikátoru URI SAS | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Aktualizujte identifikátor URI SAS s oprávněními `Read` nastavenými `List`jako a. |
| Identifikátor URI SAS obsahuje mezery v názvu virtuálního pevného disku. | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Aktualizujte identifikátor URI SAS pro odebrání prázdných znaků. |
| Chyba autorizace identifikátoru URI SAS | `Failure: Copying Images. Not able to download blob due to authorization error.` | Zkontrolujte a opravte formát identifikátoru URI SAS. V případě potřeby znovu vygenerujte. |
| Parametry identifikátoru URI SAS "St" a "se" neobsahují úplné specifikace data a času. | `Failure: Copying Images. Not able to download blob due to incorrect SAS Uri.` | `st` Parametry `se` `11-02-2017T00:00:00Z` **počátečního** a **koncového** data URI SAS musí mít úplný formát data a času, například. Zkrácené verze jsou neplatné (některé příkazy v rozhraní příkazového řádku Azure CLI můžou ve výchozím nastavení generovat zkrácené hodnoty). |
|  |  |  |

Podrobnosti najdete v tématu [použití sdílených přístupových podpisů (SAS)](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/).
