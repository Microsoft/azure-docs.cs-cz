---
title: 'Azure Active Directory Domain Services: Aktualizace nastavení DNS pro virtuální síť Azure | Microsoft Docs'
description: Začínáme se službou Azure Active Directory Domain Services
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: d4f3e82c-6807-4690-b298-4eabad2b7927
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/26/2017
ms.author: maheshu
ms.openlocfilehash: 972b995d3768e765b95c136b3cfbee91ab0a88ab
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="enable-azure-active-directory-domain-services"></a>Povolení služby Azure Active Directory Domain Services

## <a name="task-4-update-dns-settings-for-the-azure-virtual-network"></a>Úloha 4: Aktualizace nastavení DNS pro virtuální síť Azure
V předchozích úlohách konfigurace jste úspěšně povolili službu Azure Active Directory Domain Services pro svůj adresář. Dalším úkolem je zajistit, že se počítače v rámci virtuální sítě mohou k těmto službám připojit a využívat je. V tomto článku provedete aktualizaci nastavení serveru DNS svojí virtuální sítě tak, aby odkazoval na dvě IP adresy, kde je ve virtuální síti dostupná služba Azure Active Directory Domain Services.

Podle následujících kroků aktualizujte server DNS virtuální sítě, ve které jste povolili službu Azure Active Directory Domain Services:

1. Na kartě **Přehled** je uveden seznam **požadovaných kroků konfigurace**, které je potřeba provést po úplném zřízení spravované domény. Prvním konfiguračním krokem je **aktualizace nastavení serveru DNS pro virtuální síť**.

    ![Domain Services – Karta Přehled po úplném zřízení](./media/getting-started/domain-services-provisioned-overview.png)

2. Když je doména úplně zřízená, zobrazují se na této dlaždici dvě IP adresy. Každý z těchto IP adres představuje řadič vaší spravované domény.

3. Klikněte na tlačítko pro kopírování vedle první IP adresy a zkopírujte ji do schránky. Potom klikněte na tlačítko **Konfigurovat servery DNS**.

4. První IP adresu vložte do textového pole **Přidat server DNS** v okně **Servery DNS**. Posuňte se vodorovně doleva, zkopírujte druhou IP adresu a vložte ji do textového pole **Přidat server DNS**.

    ![Domain Services – Aktualizace DNS](./media/getting-started/domain-services-update-dns.png)

5. Až to budete mít, klikněte na **Uložit**. Provedete tak aktualizaci serverů DNS pro vaši virtuální síť.

> [!NOTE]
> Virtuální počítače v síti získají nové nastavení DNS až po restartování. Pokud chcete, aby aktualizované nastavení DNS získaly hned, aktivujte restartování z portálu, pomocí PowerShellu nebo pomocí rozhraní příkazového řádku.
>
>

## <a name="next-step"></a>Další krok
[Úloha 5: Povolení synchronizace hodnot hash hesel do služby Azure Active Directory Domain Services](active-directory-ds-getting-started-password-sync.md)
