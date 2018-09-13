---
title: Příklady Azure CLI – Azure Media Services | Dokumentace Microsoftu
description: Příklady Azure CLI pro službu Azure Media Services
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 04/15/2018
ms.author: juliako
ms.openlocfilehash: 3328403f5366f168f979a14951da938f26e1aee9
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "35899158"
---
# <a name="azure-cli-examples-for-azure-media-services"></a>Příklady Azure CLI pro službu Azure Media Services

Následující tabulka obsahuje odkazy na příklady Azure CLI pro službu Azure Media Services.

|  |  |
|---|---|
|**Účet**||
| [Vytvoření účtu Media Services](./scripts/cli-create-account.md) | Vytvoří účet Azure Media Services. Také vytvoří instančního objektu, který lze použít pro přístup k rozhraní API můžete programově spravovat účet služby. |
| [Resetovat přihlašovací údaje k účtu](./scripts/cli-reset-account-credentials.md)|Resetování přihlašovacích údajů k účtu a získá zpět nastavení souboru app.config.|
|**Prostředky**||
| [Vytvoření prostředků](./scripts/cli-create-asset.md)|Vytvoří k Media Services Assetu nahrát obsah do.|
| [Nahrát soubor](./scripts/cli-upload-file-asset.md)|Nahraje místní soubor do kontejneru úložiště.|
| [Publikování assetu](./scripts/cli-publish-asset.md)| Vytvoří Lokátor streamování a získá zpět adresy URL streamování. |
| **Transformuje** a **úlohy**||
| [Vytvoření transformace](./scripts/cli-create-transform.md)|Ukazuje postup vytvoření transformace. Transformace popisují jednoduchý pracovní postup úloh pro zpracování videosouborů nebo zvukových souborů a často se označují jako předpisy.<br/> Vždy byste měli zkontrolovat, zda už neexistuje transformace s požadovaným názvem a předpisem. Pokud ano, znovu použít. |
| [Vytváření úloh](./scripts/cli-create-jobs.md)|Odešle úlohu, která jednoduché kódování transformace pomocí adresy URL HTTPs.|
| [Vytvoření EventGrid](./scripts/cli-create-event-grid.md)|Vytvoří odběr Event gridu úroveň pro účet pro změny stavu úlohy.|

## <a name="see-also"></a>Další informace najdete v tématech

[Azure CLI](https://docs.microsoft.com/en-us/cli/azure/ams?view=azure-cli-latest)
