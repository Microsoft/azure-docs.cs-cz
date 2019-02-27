---
title: Integrace s průběžnou integraci a doručování kanálu pomocí konfigurace aplikace pro Azure | Dokumentace Microsoftu
description: Zjistěte, jak vygenerovat konfigurační soubor používá data v konfiguraci aplikací pro Azure během průběžnou integraci a doručování
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/24/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: 0e6b24175ffa28b2a0f361bc46fd6c41e09cae72
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/26/2019
ms.locfileid: "56885435"
---
# <a name="integrate-with-a-cicd-pipeline"></a>Integrace s kanálem CI/CD

K vylepšení odolnosti vaší aplikace proti vzdálené možnosti, že nelze dosáhnout konfigurace aplikace pro Azure, musí balíček aktuální konfiguračních dat do souboru, který je nasazen s aplikací a místně načtení při jeho spuštění . Tento postup zaručuje, že vaše aplikace bude mít výchozí hodnoty nastavení alespoň. Tyto hodnoty se přepíšou všechny novější změny v obchodě s aplikacemi konfigurace až bude k dispozici.

## <a name="automate-configuration-data-retrieval"></a>Automatizace konfigurace načtení dat

Použití [ **exportovat** ](./howto-import-export-data.md#export-data) funkce Konfigurace aplikací v Azure, můžete automatizovat proces načítání aktuální konfigurační data jako jeden soubor. Tento soubor pak můžete vložit v kroku sestavení nebo nasazení v průběžnou integraci a průběžné nasazování kanálu.

## <a name="next-steps"></a>Další postup

* [Rychlé zprovoznění: Vytvoření webové aplikace ASP.NET](quickstart-aspnet-core-app.md)  
