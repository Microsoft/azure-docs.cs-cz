---
title: Co je nového v Custom Vision?
titleSuffix: Azure Cognitive Services
description: Tento článek obsahuje novinky o Custom Vision.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: overview
ms.date: 10/23/2020
ms.author: pafarley
ms.openlocfilehash: a87e76d4a726f7a01b96d602e7f41d60409dae56
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2020
ms.locfileid: "92521488"
---
# <a name="whats-new-in-custom-vision"></a>Co je nového v Custom Vision

Zjistěte, co je nového ve službě. Tyto položky můžou být poznámky k verzi, videa, příspěvky na blogu a další typy informací. Pomocí záložky můžete tuto stránku udržovat v aktualizovaném stavu.

## <a name="july-2020"></a>Červenec 2020

### <a name="azure-role-based-access-control"></a>Řízení přístupu na základě role v Azure

* Custom Vision podporuje řízení přístupu na základě role Azure (Azure RBAC), což je autorizační systém pro správu individuálního přístupu k prostředkům Azure. Informace o tom, jak spravovat přístup k vašim Custom Visionm projektům, najdete v tématu [řízení přístupu na základě role v Azure](./role-based-access-control.md).

### <a name="subset-training"></a>Školení podmnožiny

* Při výuce projektu detekce objektu můžete volitelně vytvořit pouze podmnožinu použitých značek. Můžete to chtít udělat, pokud jste ještě nepoužili dostatek určitých značek, ale máte dost dalších. Další informace najdete v průvodci [rychlým startem klientské knihovny](./quickstarts/object-detection.md) pro C# nebo Python.

### <a name="azure-storage-notifications"></a>Oznámení Azure Storage

* Můžete integrovat svůj Custom Vision projekt s frontou služby Azure Blob Storage a získat nabízená oznámení o aktivitě projektu školení/exportu a záložní kopie publikovaných modelů. Tato funkce je užitečná k tomu, abyste se vyhnuli průběžnému dotazování služby na výsledky v případě, že běží dlouho. Místo toho můžete do pracovního postupu integrovat oznámení fronty úložiště. Další informace najdete v příručce pro [integraci úložiště](./storage-integration.md) .

### <a name="copy-and-move-projects"></a>Kopírování a přesun projektů

* Nyní můžete kopírovat projekty z jednoho Custom Vision účtu do ostatních. Můžete chtít přesunout projekt z vývoje do produkčního prostředí nebo zálohovat projekt na účet v jiné oblasti Azure pro zvýšení zabezpečení dat. Další informace najdete v průvodci [kopírováním a přesunutím projektů](./copy-move-projects.md) .

## <a name="september-2019"></a>Září 2019

### <a name="suggested-tags"></a>Navrhované značky

* Nástroj inteligentního popisku na [webu Custom Vision](https://www.customvision.ai/) vygeneruje navrhované značky pro školicí obrázky. Díky tomu můžete při výuce Custom Visionho modelu rychleji označit velký počet obrázků. Pokyny, jak tuto funkci používat, najdete v tématu věnovaném [navrhovaným značkám](./suggested-tags.md).

## <a name="cognitive-service-updates"></a>Aktualizace služby vnímání

[Oznámení o aktualizacích Azure pro Cognitive Services](https://azure.microsoft.com/updates/?product=cognitive-services)