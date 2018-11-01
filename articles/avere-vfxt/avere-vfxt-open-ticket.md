---
title: Jak získat podporu pro Avere vFXT pro Azure
description: Vysvětlení otevření lístky žádostí o podporu o Avere vFXT pro Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: d621511cbb6983f8ad57ea8305823039475f40d0
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/31/2018
ms.locfileid: "50633808"
---
# <a name="get-help-with-your-system"></a>Získejte pomoc ve vašem systému

Pokud potřebujete pomoc s vaší Avere vFXT pro Azure, tady jsou různé způsoby, jak získat podporu:

* **Problém vFXT Avere** -pomocí webu Azure portal otevřete lístek podpory pro vaše Avere vFXT, jak je popsáno [níže](#open-a-support-ticket-for-your-avere-vfxt).
* **Kvóta** – Pokud máte potíže s týkající se kvóty [požádat o zvýšení kvóty](#request-a-quota-increase)
* **Dokumentaci a příklady** - li najít problémy s této dokumentace nebo příklady, přejděte do dolní části stránky se problém a použití **zpětnou vazbu** části hledání pro stávající potíže a zaslat nové jeden if potřeba.  

## <a name="open-a-support-ticket-for-your-avere-vfxt"></a>Vytvořit lístek podpory pro vaše Avere vFXT

Pokud narazíte na problémy při nasazení nebo použití Avere vFXT, požádat o pomoc prostřednictvím webu Azure portal.  

Postupujte podle těchto kroků, abyste měli jistotu, že vašeho lístku podpory je označené prostředků z clusteru. Označování-the-ticket pomáhá nám směrovat na správná podporu prostředků. 

1. Z [ https://portal.azure.com ](https://portal.azure.com)vyberte **skupiny prostředků**.

   ![snímek obrazovky portálu levou nabídkou Azure se skupinami"prostředků" v kruhu](media/avere-vfxt-ticket-rg.png)

1. Přejděte do skupiny prostředků, která obsahuje vFXT cluster, kde došlo k problému a klikněte na jeden z virtuálních počítačů Avere.

    ![snímek obrazovky portálu prostředků Azure skupiny "Přehled" panel konkrétního virtuálního počítače v kruhu](media/avere-vfxt-ticket-vm.png)

1. Na stránce virtuální počítač, posuňte se dolů na levém panelu a klikněte na tlačítko **nová žádost o podporu**.

    ![Snímek obrazovky stránky Azure portal virtuálního počítače pro virtuální počítač z předchozího snímku obrazovky. V levé nabídce je přechod na dolní a "Nová žádost o podporu" je v kruhu.](media/avere-vfxt-ticket-request.png)

1. Jedna žádost o podporu, klikněte na stránce **všechny služby** a podívejte se do části **úložiště** zvolit **Avere vFXT**.

    ![snímek obrazovky s novou obrazovku žádost o podporu na webu Azure Portal se záhlavím "Základní" a kolečko okolo položky "Služba". Je vybráno tlačítko "Všechny služby" a pole rozevírací nabídky má hodnotu "Avere vFXT"](media/avere-vfxt-ticket-service.png)

1. Na stránce dvě zvolte typ problému a kategorie, které nejvíce odpovídají svůj problém. Přidejte krátký výstižný název a popis, který obsahuje čas, kdy došlo k problému. 

   ![snímek obrazovky s novou obrazovku žádost o podporu s hlavičkou "Problém", který obsahuje mnoho polí, které je potřeba dokončit](media/avere-vfxt-ticket-problem.png)

1. Na stránce tři, vyplňte svoje kontaktní informace a klikněte na tlačítko **vytvořit**. Číslo lístku a potvrzení se pošle e-mailovou adresu a bude vás kontaktovat člen pracovníky podpory.

## <a name="request-a-quota-increase"></a>Požádat o zvýšení kvóty

Čtení [kvóty pro vFXT cluster](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster) se dozvíte, jaké součásti jsou potřeba k nasazení Avere vFXT pro Azure. Je možné [požádat o zvýšení kvóty](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) z portálu Azure portal.