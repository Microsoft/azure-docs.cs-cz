---
title: Běžné problémy a opravy identifikátorů URI služby SAS – Azure Marketplace
description: Při práci se sdílenými přístupovými podpisy došlo k běžným problémům a k navrhovaným řešením.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.openlocfilehash: fb86b1c5ec3be5a423dc2abd295aa8beb8f23f47
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81266237"
---
# <a name="common-sas-uri-issues-and-fixes"></a>Běžné problémy a opravy identifikátorů URI s as

> [!IMPORTANT]
> Přesouváme správu nabídek virtuálních strojů Azure z portálu partnerů cloudu do Centra partnerů. Dokud nebudou vaše nabídky migrovány, postupujte podle pokynů v [běžných problémech s identifikátory URI a oprav pro](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-common-sas-url-issues) portál cloudových partnerů pro správu nabídek.

Následují běžné problémy, které se vyskytují při práci se sdílenými přístupovými podpisy (které se používají k identifikaci a sdílení nahraných virtuálních discích pro vaše řešení) spolu s navrhovanými řešeními.

| **Problém** | **Zpráva o selhání** | **Oprava** |
| --------- | ------------------- | ------- |
| *Selhání kopírování obrázků* |  |  |
| "?" není v identifikátoru URI sas nalezen | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Aktualizujte identifikátor URI s as pomocí doporučených nástrojů. |
| parametry "st" a "se" nejsou v identifikátoru URI s rozhraním SAS | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Aktualizujte identifikátor URI s vlastními hodnotami **Počáteční datum** a **Koncové datum.** |
| "sp=rl" není v identifikátoru URI SAS | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Aktualizujte identifikátor URI s `Read` oprávněními nastavenými jako a `List`. |
| Identifikátor URI sas má v názvu virtuálního pevného disku prázdné znaky. | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Chcete-li odstranit mezery, aktualizujte identifikátor URI sas. |
| Chyba autorizace identifikátoru URI sas | `Failure: Copying Images. Not able to download blob due to authorization error.` | Zkontrolujte a opravte formát identifikátoru URI SAS. V případě potřeby regenerovat. |
| Parametry identifikátorů URI s as "st" a "se" nemají úplnou specifikaci data a času. | `Failure: Copying Images. Not able to download blob due to incorrect SAS Uri.` | Parametry **počátečního** a **koncového** `st` data `se` identifikátoru SAS URI ( a podřetězce) musí mít úplný formát data a času, například `11-02-2017T00:00:00Z`. Zkrácené verze jsou neplatné (některé příkazy v Azure CLI může generovat zkrácené hodnoty ve výchozím nastavení). |
|  |  |  |

Podrobnosti naleznete [v tématu Použití sdílených přístupových podpisů (SAS)](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/).
