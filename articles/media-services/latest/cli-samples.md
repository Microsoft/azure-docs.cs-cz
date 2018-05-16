---
title: Rozhraní příkazového řádku Azure příklady - služby Azure Media Services | Microsoft Docs
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
ms.openlocfilehash: bbf69bdcc92316642f6b37d267cdea2aad920316
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2018
---
# <a name="azure-cli-examples-for-azure-media-services"></a>Příklady Azure CLI pro Azure Media Services

Následující tabulka obsahuje odkazy na příklady rozhraní příkazového řádku Azure pro službu Azure Media Services.

|  |  |
|---|---|
|**Účet**||
| [Vytvoření účtu Media Services](./scripts/cli-create-account.md) | Vytvoří účet Azure Media Services. Také vytvoří objekt zabezpečení, který lze použít pro přístup k rozhraní API pro programovou správu účtu služby. |
| [Resetovat přihlašovací údaje účtu](./scripts/cli-reset-account-credentials.md)|Obnoví svoje přihlašovací údaje a nastavení souboru app.config získá zpět.|
|**Prostředky**||
| [Vytvořit prostředky](./scripts/cli-create-asset.md)|Vytvoří prostředek služby média nahrát obsah.|
| [Nahrát soubor](./scripts/cli-upload-file-asset.md)|Ukládání do místního souboru do kontejneru úložiště.|
| [Publikování assetu](./scripts/cli-publish-asset.md)| Vytvoří Lokátor streamování a získá adresy URL streamování zpět. |
| **Transformuje** a **úlohy**||
| [Vytvoření transformací](./scripts/cli-create-transform.md)|Ukazuje, jak vytvořit transformace. Transformace popisují jednoduché pracovního postupu úloh pro zpracování souborů video nebo zvuk (často označované jako "receptur").<br/> Vždy byste měli zkontrolovat Pokud transformace s požadovaným názvem a "recept na" již existuje. Pokud ano, znovu použít. |
| [Vytváření úloh](./scripts/cli-create-jobs.md)|Odešle úlohu pro jednoduché kódování transformace pomocí adresy URL HTTPs.|
| [Vytvoření EventGrid](./scripts/cli-create-event-grid.md)|Vytvoří předplatné účet úrovně mřížky události změny stavu úlohy.|

