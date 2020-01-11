---
title: Jak získat podporu pro avere vFXT pro Azure
description: Vysvětlení otevření lístků podpory o avere vFXT pro Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: rohogue
ms.openlocfilehash: 27f32a922a1b063096b0ccf28a01a78d442e0271
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2020
ms.locfileid: "75889278"
---
# <a name="get-help-with-your-system"></a>Získejte pomoc s vaším systémem.

Pokud potřebujete pomoc s avere vFXT pro Azure, najdete tady různé způsoby, jak získat podporu:

* **Avere vFXT problém** – pomocí Azure Portal otevřete lístek podpory pro avere vFXT, jak je popsáno [níže](#open-a-support-ticket-for-your-avere-vfxt).
* **Kvóta** – Pokud máte problém související s kvótou, [požádejte o zvýšení kvóty](#request-a-quota-increase) .
* **Dokumentace a příklady** – Pokud najdete problémy s touto dokumentací nebo příklady, posuňte se k dolnímu okraji stránky a v případě potřeby můžete v části **Zpětná vazba** vyhledat existující problémy a vytvořit nové soubory.

## <a name="open-a-support-ticket-for-your-avere-vfxt"></a>Otevření lístku podpory pro avere vFXT

Pokud narazíte na problémy při nasazování nebo používání avere vFXT, požádejte o technickou podporu Azure Portal.

Pomocí těchto kroků se ujistěte, že je váš lístek podpory označený prostředkem z vašeho clusteru. Označení lístku vám pomůže nám ho směrovat na správný prostředek podpory.

1. Z [https://portal.azure.com](https://portal.azure.com)vyberte **skupiny prostředků**.

   ![snímek obrazovky levé nabídky Azure Portal se skupinami prostředků v kruhu](media/avere-vfxt-ticket-rg.png)

1. Přejděte do skupiny prostředků, která obsahuje cluster vFXT, ve kterém došlo k problému, a klikněte na jeden z virtuálních počítačů s avere.

    ![snímek obrazovky s Azure Portalou skupinou prostředků "Přehled" s konkrétním virtuálním počítačem v kruhu](media/avere-vfxt-ticket-vm.png)

1. Na stránce virtuální počítač přejděte dolů k dolnímu okraji levého panelu a klikněte na **Nová žádost o podporu**.

    ![Snímek obrazovky Azure Portalho virtuálního počítače pro virtuální počítač z předchozího snímku obrazovky V levé nabídce se posune dolů a "nová žádost o podporu" je zakroužkovaná.](media/avere-vfxt-ticket-request.png)

1. Na stránce jedna z žádostí o podporu klikněte na **všechny služby** a vyhledejte v části **úložiště** možnost **avere vFXT**.

    ![snímek obrazovky nové žádosti o podporu v Azure Portal s hlavičkou "Základy" a kruhem kolem položky "služba". Je vybráno tlačítko všechny služby a pole rozevírací nabídky má hodnotu "avere vFXT".](media/avere-vfxt-ticket-service.png)

1. Na stránce druhá klikněte na typ problému a kategorii, které nejlépe odpovídají vašemu problému. Přidejte krátký název a popis, který obsahuje čas výskytu problému.

   ![snímek obrazovky s novou žádostí o podporu s hlavičkou "problém", která obsahuje mnoho polí, která se musí dokončit](media/avere-vfxt-ticket-problem.png)

1. Na stránce tři zadejte kontaktní informace a klikněte na **vytvořit**. Na vaši e-mailovou adresu se pošle potvrzení a číslo lístku, na které vás bude kontaktovat člen zaměstnance podpory.

## <a name="request-a-quota-increase"></a>Požádat o zvýšení kvóty

Přečtěte si [kvótu pro cluster vFXT](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster) , kde se dozvíte, jaké součásti jsou potřeba k nasazení avere VFXT pro Azure. Z Azure Portal můžete [požádat o zvýšení kvóty](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) .
