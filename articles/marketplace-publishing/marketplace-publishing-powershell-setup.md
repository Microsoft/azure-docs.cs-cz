---
title: Nastavení prostředí PowerShell pro vytvoření virtuálního počítače pro web Marketplace | Dokumentace Microsoftu
description: Pokyny pro nastavení prostředí Azure PowerShell a jeho použití jako volitelný proces flow k vytváření imagí virtuálních počítačů k nasazení na a prodávat na Azure Marketplace
services: marketplace-publishing
documentationcenter: ''
author: v-miclar
manager: hascipio
editor: ''
ms.assetid: e19d6cda-76df-4e42-be84-c9fe47a636db
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/04/2016
ms.author: hascipio
ROBOTS: NOINDEX
ms.openlocfilehash: e5175558f18dfc903c280ea6bbe487e0a3ee8189
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2019
ms.locfileid: "54076599"
---
# <a name="set-up-azure-powershell-to-create-an-offer-for-the-azure-marketplace"></a>Nastavení prostředí Azure PowerShell pro vytvoření nabídky pro Azure Marketplace

Podrobné informace o tom, jak nastavit prostředí PowerShell v Azure najdete v tématu [instalace a konfigurace Azure Powershellu](/powershell/azure/overview). Jednoduchým přístupem je použití certifikátu metodu, která stáhne a naimportuje certifikát vyžadovaný pro ověřování. Chcete-li získat potřebný certifikát, použijte **Get-AzurePublishSettingsFile** rutiny. Po zobrazení výzvy, uložte soubor. Chcete-li importovat certifikát do relace prostředí PowerShell, použijte **Import AzurePublishSettingsFile** rutiny.

Chcete-li konfigurovat a ukládat společné nastavení odběru Microsoft Azure pro relaci Powershellu, použijte **Set-AzureSubscription** a **Select-AzureSubscription** rutiny:

        Set-AzureSubscription -SubscriptionName “mySubName” -CurrentStorageAccountName “mystorageaccount”
        Select-AzureSubscription -SubscriptionName "mySubName" –Current

První příkaz přidruží výchozí účet úložiště předplatného (třeba pro některé operace zřizování virtuálních počítačů).  Druhá je předplatné tu (rozpoznán jinými rutinami).

Další informace najdete v následujících článcích:
* [Začínáme: Publikování nabídky na webu Azure Marketplace](marketplace-publishing-getting-started.md)
* [Vytvoření image virtuálního počítače pro Marketplace](marketplace-publishing-vm-image-creation.md)

