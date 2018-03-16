---
title: "Další informace o použití konektoru SFTP ve vašich logic apps | Microsoft Docs"
description: "Vytvoření aplikace logiky službou Azure App service. Připojte k rozhraní API pomocí protokolu SFTP odesílat a přijímat soubory. Můžete provádět různé operace, jako je vytváření, aktualizace, získání nebo odstraňte některé soubory."
services: logic-apps
documentationcenter: .net,nodejs,java
author: ecfan
manager: anneta
editor: 
tags: connectors
ms.assetid: 697eb8b0-4a66-40c7-be7b-6aa6b131c7ad
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/20/2016
ms.author: estfan; ladocs
ms.openlocfilehash: ee4dd59bd7b4c09cccadfff29868029559955c28
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="get-started-with-the-sftp-connector"></a>Začínáme s konektorem SFTP
Pomocí protokolu SFTP konektoru pro přístup k protokolu SFTP účet, který chcete odesílat a přijímat soubory. Můžete provádět různé operace, jako je vytváření, aktualizace, získání nebo odstraňte některé soubory.  

Chcete-li použít [všechny konektory](apis-list.md), musíte nejprve vytvořit aplikaci logiky. Abyste mohli začít podle [vytvoření aplikace logiky teď](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="connect-to-sftp"></a>Připojit k protokolu SFTP
Než se aplikace logiky k jakékoli služby, musíte nejprve vytvořit *připojení* ke službě. A [připojení](connectors-overview.md) poskytuje připojení mezi aplikace logiky a jiné služby.  

### <a name="create-a-connection-to-sftp"></a>Umožňuje vytvořit připojení k protokolu SFTP
> [!INCLUDE [Steps to create a connection to SFTP](../../includes/connectors-create-api-sftp.md)]
> 
> 

## <a name="use-an-sftp-trigger"></a>Použít aktivační procedury protokolu SFTP
Aktivační událost je událost, která můžete použít ke spuštění pracovního postupu definované v aplikaci logiky. [Další informace o aktivační události](../logic-apps/logic-apps-overview.md#logic-app-concepts).  

V tomto příkladu **SFTP – Pokud je soubor přidat ani upravit** aktivační událost se použilo k zahájení pracovní postup aplikace logiky, když je soubor přidán do, nebo úpravě na serveru pomocí protokolu SFTP. Můžete také přidat podmínku, která kontroluje obsah souboru nové nebo upravené a provádí rozhodnutí extrahujte soubor, pokud jeho obsah označuje, že by měla být rozbalena před použitím obsah. Nakonec přidejte akci se extrahovat obsah souboru a umístit extrahované obsah do složky na serveru pomocí protokolu SFTP. 

V příklad enterprise můžete použít této aktivační události k monitorování složky aplikace pomocí protokolu SFTP pro nové soubory, které představují objednávek zákazníků.  Poté můžete použít konektor akce SFTP, jako například **získat obsah souboru**, chcete-li získat obsah pořadí pro další zpracování a úložiště v databázi objednávky.

> [!INCLUDE [Steps to create an SFTP trigger](../../includes/connectors-create-api-sftp-trigger.md)]
> 
> 

## <a name="add-a-condition"></a>Přidat podmínku
> [!INCLUDE [Steps to add a condition](../../includes/connectors-create-api-sftp-condition.md)]
> 
> 

## <a name="use-an-sftp-action"></a>Použít akci protokolu SFTP
Akce je operace prováděné definované v aplikaci logiky pracovního postupu. [Další informace o akcích](../logic-apps/logic-apps-overview.md#logic-app-concepts).  

> [!INCLUDE [Steps to create an SFTP action](../../includes/connectors-create-api-sftp-action.md)]
> 
> 

## <a name="connector-specific-details"></a>Podrobnosti o konkrétní konektor

Zobrazit všechny aktivační události a akce definované v swagger a také zobrazit žádné limity v [connector – podrobnosti](/connectors/sftpconnector/).

## <a name="more-connectors"></a>Více konektorů
Přejděte zpět [rozhraní API seznamu](apis-list.md).