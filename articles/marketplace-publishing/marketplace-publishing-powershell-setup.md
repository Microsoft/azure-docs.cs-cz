---
title: "Nastavení prostředí PowerShell pro vytvoření virtuálního počítače pro Marketplace | Microsoft Docs"
description: "Pokyny pro nastavení prostředí Azure PowerShell a jeho použití jako volitelný proces toku k vytvoření Image virtuálních počítačů k nasazení a prodeje na Azure Marketplace"
services: marketplace-publishing
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
editor: 
ms.assetid: e19d6cda-76df-4e42-be84-c9fe47a636db
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/04/2016
ms.author: mbaldwin
ms.openlocfilehash: 72ee1ac612fc4c7191718009a78f55272f0a44cf
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="set-up-azure-powershell-to-create-an-offer-for-the-azure-marketplace"></a>Nastavení prostředí Azure PowerShell k vytvoření nabídky pro Azure Marketplace
Podrobné informace o tom, jak nastavit prostředí PowerShell v Azure najdete v tématu [postup instalace a konfigurace prostředí Azure PowerShell](/powershell/azure/overview). Jednoduché možných přístupů je použít metody certifikátu, která se stáhne a importuje certifikát potřebný pro ověřování. Chcete-li získat potřebný certifikát, použijte **Get-AzurePublishSettingsFile** rutiny. Když se zobrazí výzva, uložte soubor. Chcete-li import certifikátu do relace prostředí PowerShell, použijte **Import AzurePublishSettingsFile** rutiny.

Chcete-li konfigurace a uložení běžných nastavení odběru Microsoft Azure pro relace prostředí PowerShell, použijte **Set-AzureSubscription** a **Select-AzureSubscription** rutiny:

        Set-AzureSubscription -SubscriptionName “mySubName” -CurrentStorageAccountName “mystorageaccount”
        Select-AzureSubscription -SubscriptionName "mySubName" –Current

První příkaz přidruží výchozí účet úložiště s předplatným (vyžadována pro některé operace zřizování virtuálních počítačů).  Druhá je předplatné stávající (rozpoznán jinými rutinami).

## <a name="see-also"></a>Další informace najdete v tématech
* [Začínáme: postup publikování nabídky pro Azure Marketplace](marketplace-publishing-getting-started.md)
* [Vytváření bitové kopie virtuálního počítače pro Marketplace.](marketplace-publishing-vm-image-creation.md)

