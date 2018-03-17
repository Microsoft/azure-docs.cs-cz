---
title: "Pokročilé úlohy hodnocení zásobník Azure | Microsoft Docs"
description: "Tento článek popisuje pokročilé úlohy hodnocení zásobník Azure."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: c4bf76aa07ec5025d9e53b5518929199ace27e18
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/17/2018
---
# <a name="advanced-azure-stack-development-kit-evaluation-tasks"></a>Pokročilé úlohy hodnocení Azure zásobníku Development Kit
Poté, co jste se dostalo znalost základní funkce služby Azure zásobníku Development Kit (ASDK) a možnosti, můžete prohloubit vaše pochopení nástroje Azure zásobníku další testování se pokročilejší scénáře. Tyto pokročilejší úlohy hodnocení jsou plně popsané v dokumentaci k Azure zásobníku operátor.

> [!NOTE]
> Když celou řadu úloh operátor jsou podporované pro ASDK i produkčním nasazení na víc uzlů Azure zásobníku, jsou podporovány všechny scénáře použití pro ASDK nasazení. V tématu [ASDK a více uzly zásobník Azure rozdíly](asdk-what-is.md#asdk-and-multi-node-azure-stack-differences) Další informace.

## <a name="delegate-offers-in-azure-stack"></a>Delegování nabídek v Azure Stacku
Jako operátor zásobník Azure budete chtít často put jiní lidé starosti vytvoření nabídky a přihlašování uživatelů. Například pokud jste poskytovatel služeb, můžete si zaregistrovat zákazníků a spravovat je vaším jménem prodejce. Nebo pokud jste součástí centrální skupinu IT v podniku, můžete chtít pobočky k registraci uživatelů bez zásahu uživatele.

[Delegování nabídky v Azure zásobníku](.\.\azure-stack-delegated-provider.md) vám pomůže s těmito úkoly tím, že ji možné k dosažení a správě více uživatelů, než můžete přímo. 

## <a name="make-sql-databases-available-to-your-azure-stack-users"></a>Databáze SQL zpřístupnit uživatelům Azure zásobníku
Jako operátor zásobník Azure, můžete vytvořit nabídky, které uživatelům vytvoření databáze SQL, které můžete použít s jejich nativní cloudové aplikace, weby a úlohami (klienty). Tím, že tyto databáze vlastní, na vyžádání, založené na cloudu poskytuje uživatelům, můžete je uložit čas a prostředky. 

Použít adaptér zprostředkovatele prostředků systému SQL Server, který má [zpřístupnit databází SQL Azure zásobníku uživatelům](.\.\azure-stack-tutorial-sql-server.md) jako služba Azure zásobníku. Po instalaci poskytovatele prostředku připojení na jeden nebo více instancí systému SQL Server.

## <a name="make-web-and-api-apps-available-to-your-azure-stack-users"></a>Zpřístupnit web a aplikace API Azure zásobníku uživatelům
Jako operátor zásobník Azure, můžete vytvořit nabídky, které uživatelům (klienty) vytvořit Azure Functions a rozhraní API a webové aplikace. Tím, že uživatelům poskytuje přístup k těmto aplikacím na vyžádání, založené na cloudu, můžete je uložit čas a prostředky.

Nasazení poskytovatele prostředků služby App Service [zpřístupnit web a aplikace API Azure zásobníku uživatelům](.\.\azure-stack-tutorial-app-service.md)

## <a name="next-steps"></a>Další postup
[Další informace o nabídce služeb s Azure zásobníku integrované systémy](.\.\azure-stack-offer-services-overview.md)